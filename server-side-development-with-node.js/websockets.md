---
icon: '5'
---

# การทำงานกับ WebSockets

หนึ่งในข้อดีหลักของสภาพแวดล้อม Node คือความสามารถในการสร้างแอปพลิเคชันแบบ push-based ซึ่งความสามารถนี้พึ่งพา **WebSockets** ซึ่งเป็นฟีเจอร์ของเบราว์เซอร์ที่รองรับโดยเบราว์เซอร์ปัจจุบันทั้งหมดในขณะนี้&#x20;

**WebSockets** เป็น API ที่ช่วยเปิดช่องทางการสื่อสารแบบโต้ตอบ (สองทาง) ระหว่างเบราว์เซอร์และเซิร์ฟเวอร์ โดยไม่ต้องใช้ HTTP ยกเว้นในขั้นตอนเริ่มต้นการเชื่อมต่อ

มีโมดูล WebSocket หลายตัวที่สามารถใช้ได้ผ่าน npm ในตัวอย่างต่อไปนี้ เราจะใช้ **Socket.io** (http://socket.io/) เนื่องจาก Socket.io ไม่ใช่ส่วนหนึ่งของระบบ Node.js โดยค่าเริ่มต้น คุณจึงต้องเพิ่มลงในโปรเจ็กต์ของคุณโดยใช้คำสั่ง:

```bash
npm install socket.io
```

คำสั่งนี้จะติดตั้งโค้ด JavaScript ที่จำเป็นสำหรับทั้งฝั่งไคลเอนต์และเซิร์ฟเวอร์ สิ่งสำคัญที่ควรเน้นย้ำคือ Socket.io มี API JavaScript สองตัว: หนึ่งตัวทำงานบนเบราว์เซอร์ และอีกตัวทำงานบนเซิร์ฟเวอร์ เพื่ออธิบาย เรามาดูตัวอย่างกัน ซึ่งประกอบด้วยสองไฟล์:

* **แอปพลิเคชันเซิร์ฟเวอร์ Node (chat-server.js)**: รับข้อความจากไคลเอนต์และส่งต่อข้อความไปยังทุกไคลเอนต์ที่เชื่อมต่อ.
* **ไฟล์ไคลเอนต์เบราว์เซอร์ (chat-client.html)**: แสดงอินเทอร์เฟซสำหรับส่งและรับข้อความแชท ซึ่งเซิร์ฟเวอร์จะส่งออกเมื่อเบราว์เซอร์ทำคำขอไปยังแอปพลิเคชันของเซิร์ฟเวอร์.

รูปที่ 10 แสดงถึงกระแสข้อมูลโดยรวมระหว่างเซิร์ฟเวอร์แชทและไคลเอนต์แชทต่างๆ&#x20;

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption><p>รูปที่ 10 กระแสข้อมูลใช้ Socket.io</p></figcaption></figure>



ตัวอย่างใน Listing 9 แสดงเซิร์ฟเวอร์แชท Node.js ที่เรียบง่าย โมดูล Socket.io จัดการงาน WebSocket ที่แท้จริงให้กับเรา

{% tabs %}
{% tab title="commonjs" %}
```javascript
const path = require("path");
const express = require("express");
const app = express();
const http = require("http").createServer(app);
const io = require("socket.io")(http);
// handle requests for static resources
app.use("/static", express.static(path.join(__dirname, "public")));
// every time we receive a root get request, send the chat client
app.get("/", (req, res) => {
  res.sendFile(__dirname + "/public/chat-client.html");
});
// handles all WebSocket events, each client will be given a
// unique socket
io.on("connection", (socket) => {
  // client has sent a username message (message names can be
  // any valid string)
  socket.on("username", (msg) => {
    // save username for this socket
    socket.username = msg;
    // broadcast message to all connected clients
    const obj = { user: socket.username, message: msg };
    io.emit("user joined", obj);
  });
  // client has sent a chat message . . . broadcast it
  socket.on("chat from client", (msg) => {
    const obj = { user: socket.username, message: msg };
    io.emit("chat from server", obj);
  });
});

http.listen(7000, () => {
  console.log("listening on *:7000");
});
```
{% endtab %}

{% tab title="module" %}
```javascript
import express from "express";
import { join } from "path";
import path from "path";
import { fileURLToPath } from "url";
import { createServer } from "http";
import { Server } from "socket.io";


const app = express();

// Define __dirname for ES module
const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

// จัดการคำขอสำหรับทรัพยากรแบบ static
app.use("/static", express.static(join(__dirname, "public")));
const httpServer = createServer(app);
const io = new Server(httpServer);

// ทุกครั้งที่ได้รับคำขอ GET ที่ root ให้ส่งไฟล์ไคลเอนต์แชท
app.get("/", (req, res) => {
  res.sendFile(__dirname + "/public/chat-client.html");
});

// จัดการเหตุการณ์ทั้งหมดของ WebSocket โดยแต่ละไคลเอนต์จะได้รับ socket ที่ไม่ซ้ำกัน
io.on("connection", (socket) => {
  // ไคลเอนต์ส่งข้อความชื่อผู้ใช้ (ชื่อข้อความสามารถเป็นข้อความที่ถูกต้องใดๆ ก็ได้)
  socket.on("username", (msg) => {
    // บันทึกชื่อผู้ใช้สำหรับ socket นี้
    socket.username = msg;
    // ส่งข้อความไปยังไคลเอนต์ที่เชื่อมต่อทั้งหมด
    const obj = { user: socket.username, message: msg };
    io.emit("user joined", obj);
  });

  // ไคลเอนต์ส่งข้อความแชท... ส่งต่อข้อความนั้น
  socket.on("chat from client", (msg) => {
    const obj = { user: socket.username, message: msg };
    io.emit("chat from server", obj);
  });
});

httpServer.listen(7000, () => {
  console.log("listening on *:7000");
});
```
{% endtab %}
{% endtabs %}

**LISTING 9 เซิร์ฟเวอร์แชท (chat-server.js)**

#### คำอธิบายโค้ด:

1. **การกำหนดเส้นทางสำหรับทรัพยากร static**: ใช้ `express.static()` เพื่อเสิร์ฟไฟล์จากโฟลเดอร์ `public`.
2. **การเสิร์ฟไฟล์ไคลเอนต์แชท**: เมื่อมีคำขอ GET ที่ root (`"/"`) ให้ส่งไฟล์ `chat-client.html`.
3. **การจัดการเหตุการณ์ WebSocket**: เมื่อไคลเอนต์เชื่อมต่อ จะได้รับ socket ที่ไม่ซ้ำกัน.
   * **รับข้อความชื่อผู้ใช้**: เมื่อไคลเอนต์ส่งข้อความชื่อผู้ใช้ จะบันทึกชื่อผู้ใช้และแจ้งเตือนไคลเอนต์ที่เชื่อมต่อทั้งหมดว่าผู้ใช้ใหม่เข้าร่วม.
   * **รับข้อความแชท**: เมื่อไคลเอนต์ส่งข้อความแชท จะส่งต่อข้อความนั้นไปยังไคลเอนต์ที่เชื่อมต่อทั้งหมด.
4. **การรันเซิร์ฟเวอร์**: เซิร์ฟเวอร์จะรับฟังที่พอร์ต `7000`.

ฟังก์ชัน `io.on()` จัดการเหตุการณ์ที่เกี่ยวข้องกับ WebSocket ฟังก์ชัน `socket.io()` จัดการการรับข้อความจากไคลเอนต์ คุณสามารถระบุประเภทข้อความที่แตกต่างกันผ่านพารามิเตอร์แรกได้ ใน Listing 9 แอปพลิเคชันเซิร์ฟเวอร์จัดการข้อความสองประเภทจากไคลเอนต์: ข้อความชื่อผู้ใช้ (ซึ่งให้ชื่อผู้ใช้ที่รวบรวมจากไคลเอนต์) และข้อความแชทจากไคลเอนต์ ชื่อข้อความจริงๆ สามารถเป็นอะไรก็ได้ตามที่คุณต้องการ

ดังที่เห็นใน Listing 9 ข้อความจะถูกส่งไปยังไคลเอนต์ที่เชื่อมต่อทั้งหมดผ่านฟังก์ชัน `io.emit()` เราสามารถส่งวัตถุใดๆ ผ่านวิธีนี้ได้ วัตถุในรายการประกอบด้วยชื่อผู้ใช้ที่สร้างข้อความและข้อความที่แท้จริง แต่เราสามารถปรับเปลี่ยนโค้ดเพื่อส่งวัตถุที่มีข้อมูลเพิ่มเติมได้

ไคลเอนต์ (แสดงใน Listing 10) มีความซับซ้อนเพียงเล็กน้อย HTML ค่อนข้างเรียบง่าย มันรวมไลบรารี JavaScript ของ Socket.io และมีพื้นที่ที่จะแสดงข้อความที่ได้รับพร้อมกับ `<form>` สำหรับการส่งข้อความแชท.

```html
<head>
    
    <script src="/socket.io/socket.io.js"></script>
  </head>
  <body>
    <div class="panel">
      <div class="panel-header"><h3>แชท</h3></div>
      <div class="panel-body"><ul id="messages"></ul></div>
      <div class="panel-footer">
        <form id="chatForm">
          <input type="text" id="entry" autocomplete="off" />
          <button>ส่ง</button>
        </form>
      </div>
    </div>
    <script>
      // เริ่มต้นการเชื่อมต่อ WebSocket
      const socket = io();
      
      // รับชื่อผู้ใช้แล้วแจ้งให้เซิร์ฟเวอร์
      let username = prompt("ชื่อผู้ใช้ของคุณคืออะไร?");
      document.querySelector(".panel-header h3").textContent = "แชท [" + username + "]";
      socket.emit("username", username);
  
      // ได้รับข้อความแจ้งเตือนการเชื่อมต่อผู้ใช้ใหม่
      socket.on("user joined", msg => {
        const li = document.createElement("li");
        li.innerHTML = `${msg.user} - ${msg.message}`;
        document.querySelector("#messages").appendChild(li);
      });
  
      // ผู้ใช้ป้อนข้อความใหม่
      document.querySelector("#chatForm").addEventListener('submit', e => {
        e.preventDefault();
        const entry = document.querySelector("#entry");
        socket.emit("chat from client", entry.value);
        entry.value = "";
      });
  
      // ได้รับข้อความแชทใหม่
      socket.on("chat from server", msg => {
        const li = document.createElement("li");
        li.textContent = msg.user + ": " + msg.message;
        document.querySelector("#messages").appendChild(li);
      });
    </script>
  </body>
```

**LISTING 10 ไคลเอนต์แชท**

#### คำอธิบายโค้ด:

1. **การเชื่อมต่อ WebSocket**: ใช้ `io()` เพื่อเริ่มต้นการเชื่อมต่อ WebSocket.
2. **รับชื่อผู้ใช้และแจ้งเซิร์ฟเวอร์**: รับชื่อผู้ใช้จากผู้ใช้และแจ้งให้เซิร์ฟเวอร์ทราบ.
3. **รับข้อความแจ้งเตือนการเชื่อมต่อผู้ใช้ใหม่**: เมื่อได้รับข้อความแจ้งเตือนการเชื่อมต่อผู้ใช้ใหม่ จะแสดงข้อความนั้นในรายการ.
4. **ส่งข้อความแชท**: เมื่อผู้ใช้ส่งข้อความแชท จะส่งต่อข้อความนั้นไปยังเซิร์ฟเวอร์.
5. **รับข้อความแชทใหม่**: เมื่อได้รับข้อความแชทใหม่ จะแสดงข้อความนั้นในรายการ.



งานของ WebSocket จัดการโดยไลบรารีไคลเอนต์ Socket.io โดยใช้ฟังก์ชัน `emit()` เพื่อส่งข้อความไปยังเซิร์ฟเวอร์ เช่นเดียวกับฟังก์ชัน `emit()` ฝั่งเซิร์ฟเวอร์ คุณสามารถแยกแยะประเภทข้อความที่แตกต่างกันโดยการให้ชื่อข้อความที่แตกต่างกัน ฟังก์ชัน `on()` ใช้เพื่อจัดการข้อความที่ได้รับจากเซิร์ฟเวอร์ (หรือที่ถูกผลักดันไปยังไคลเอนต์) รูปที่ 11 แสดงถึงแอปพลิเคชันในเบราว์เซอร์

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption><p>รูปที่ 11 Chat ในเบราเซอร์</p></figcaption></figure>





<details>

<summary>ทดสอบความรู้ของคุณ #2</summary>

ขยายเซิร์ฟเวอร์แชทและไคลเอนต์ใน Listings 9 และ 10 ดังนี้:

1. **เพิ่มปุ่ม Leave ในไคลเอนต์แชท**: เพิ่มปุ่ม "Leave" ในไคลเอนต์แชท และเพิ่มแฮนด์เลอร์สำหรับปุ่มนี้เพื่อส่งข้อความ 'client left' ไปยังเซิร์ฟเวอร์ โดยข้อความนี้ควรรวมชื่อผู้ใช้ด้วย.
2. **เพิ่มแฮนด์เลอร์ในเซิร์ฟเวอร์แชทสำหรับข้อความ 'client left'**: เพิ่มแฮนด์เลอร์ในเซิร์ฟเวอร์แชทเพื่อรับข้อความ 'client left' และส่งข้อความ 'user has left' ไปยังไคลเอนต์ทั้งหมด ข้อความนี้ควรรวมชื่อผู้ใช้ด้วย.
3. **เพิ่มแฮนด์เลอร์ในไคลเอนต์แชทสำหรับข้อความ 'user has left'**: เพิ่มแฮนด์เลอร์ในไคลเอนต์แชทเพื่อรับข้อความ 'user has left' และแสดงข้อความที่เหมาะสมในรายการ `<ul>` ของข้อความ.

</details>



<details>

<summary>Solution</summary>

ทดสอบความรู้ของคุณ #2

ขยายเซิร์ฟเวอร์แชทและไคลเอนต์ใน Listings 9 และ 10 ดังนี้:

1. **เพิ่มปุ่ม Leave ในไคลเอนต์แชท**: เพิ่มปุ่ม "Leave" ในไคลเอนต์แชท และเพิ่มแฮนด์เลอร์สำหรับปุ่มนี้เพื่อส่งข้อความ 'client left' ไปยังเซิร์ฟเวอร์ โดยข้อความนี้ควรรวมชื่อผู้ใช้ด้วย.
2. **เพิ่มแฮนด์เลอร์ในเซิร์ฟเวอร์แชทสำหรับข้อความ 'client left'**: เพิ่มแฮนด์เลอร์ในเซิร์ฟเวอร์แชทเพื่อรับข้อความ 'client left' และส่งข้อความ 'user has left' ไปยังไคลเอนต์ทั้งหมด ข้อความนี้ควรรวมชื่อผู้ใช้ด้วย.
3. **เพิ่มแฮนด์เลอร์ในไคลเอนต์แชทสำหรับข้อความ 'user has left'**: เพิ่มแฮนด์เลอร์ในไคลเอนต์แชทเพื่อรับข้อความ 'user has left' และแสดงข้อความที่เหมาะสมในรายการ `<ul>` ของข้อความ.

#### ตัวอย่างโค้ดที่แก้ไข:

**เซิร์ฟเวอร์แชท (chat-server.js):**

```javascript
// ...

io.on("connection", (socket) => {
  // ...

  // รับข้อความ 'client left'
  socket.on("client left", (username) => {
    // ส่งข้อความ 'user has left' ไปยังไคลเอนต์ทั้งหมด
    const obj = { user: username, message: "has left the chat" };
    io.emit("user has left", obj);
  });

  // ...
});
```

**ไคลเอนต์แชท (chat-client.html):**

```html
<!-- เพิ่มปุ่ม Leave -->
<div class="panel-footer">
  <form id="chatForm">
    <input type="text" id="entry" autocomplete="off" />
    <button>ส่ง</button>
  </form>
  <button id="leaveButton">ออกจากแชท</button>
</div>

<script>
  // ...

  // แฮนด์เลอร์สำหรับปุ่ม Leave
  document.getElementById("leaveButton").addEventListener('click', () => {
    socket.emit("client left", username);
    // อาจปิดการเชื่อมต่อหรือทำการอื่นๆ ตามต้องการ
  });

  // รับข้อความ 'user has left'
  socket.on("user has left", msg => {
    const li = document.createElement("li");
    li.textContent = msg.user + " " + msg.message;
    document.querySelector("#messages").appendChild(li);
  });

  // ...
</script>
```

#### คำอธิบายโค้ด:

1. **การเพิ่มปุ่ม Leave และแฮนด์เลอร์**: เพิ่มปุ่ม "ออกจากแชท" และตั้งค่าแฮนด์เลอร์สำหรับปุ่มนี้เพื่อส่งข้อความ 'client left' ไปยังเซิร์ฟเวอร์พร้อมกับชื่อผู้ใช้.
2. **การเพิ่มแฮนด์เลอร์สำหรับ 'client left' ในเซิร์ฟเวอร์**: เพิ่มแฮนด์เลอร์ในเซิร์ฟเวอร์เพื่อรับข้อความ 'client left' และส่งข้อความ 'user has left' ไปยังไคลเอนต์ทั้งหมดพร้อมกับชื่อผู้ใช้.
3. **การเพิ่มแฮนด์เลอร์สำหรับ 'user has left' ในไคลเอนต์**: เพิ่มแฮนด์เลอร์ในไคลเอนต์เพื่อรับข้อความ 'user has left' และแสดงข้อความที่เหมาะสมในรายการข้อความ.

</details>
