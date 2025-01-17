# การเพิ่ม Express

นักพัฒนา Node จำนวนมากพยายามทำให้ง่ายขึ้นและลดปริมาณโค้ดที่ต้องเขียนโดยใช้โมดูลที่มีอยู่แล้ว หนึ่งในโมดูลที่ได้รับความนิยมมากที่สุดคือ Express ซึ่งเป็นเฟรมเวิร์ก JavaScript ที่ค่อนข้างเล็กและเบาเพื่อทำให้การสร้างเว็บแอปพลิเคชันและเว็บเซอร์วิสใน Node ง่ายขึ้น

ในการใช้งาน Express ในแอปพลิเคชัน Node ใดๆ คุณต้องใช้ npm เพื่อติดตั้งไฟล์ JavaScript ของ Express ลงในโฟลเดอร์แอปพลิเคชันของคุณก่อน โดยใช้คำสั่งต่อไปนี้:

```
npm install express
```

เมื่อคุณทำเช่นนั้นแล้ว คุณเพียงแค่ต้องเพิ่มการเรียกใช้ require() ที่เหมาะสม แล้วคุณก็สามารถเริ่มใช้งาน Express ได้ เวอร์ชัน Express ที่เทียบเท่า (เกือบ) กับเซิร์ฟเวอร์ไฟล์ในรูปที่ 6 สามารถดูได้ใน Listing 2



{% tabs %}
{% tab title="แบบ commonjs" %}
```javascript
const path = require("path");
const express = require("express");
const app = express();
const options = {
  // แมปคำขอรูท (เช่น "/") ไปยังโฟลเดอร์ย่อยชื่อ "public"
  root: path.join(__dirname, "public")
};

// ด้วย express คุณกำหนดตัวจัดการสำหรับเส้นทาง
app.get("/:filename", (req, resp) => {
  resp.sendFile(req.params.filename, options, (err) => {
    if (err) {
      console.log(err);
      resp.status(404).send("ไม่พบไฟล์");
    }
    else {
      console.log("ส่งแล้ว:", req.params.filename);
    }
  });
});

app.listen(8080, () => {
  console.log("ตัวอย่างเซิร์ฟเวอร์ไฟล์ express กำลังรับฟังที่พอร์ต 8080");
});
```
{% endtab %}

{% tab title="แบบ es module" %}
```javascript
import express from 'express';
import path from 'path';
import { fileURLToPath } from 'url';

const app = express();

// Define __dirname for ES module
const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

// Route for the root URL
app.get('/', (req, resp) => {
    resp.send('Welcome to the Express File Server!');
  });

app.get('/file/:filename', (req, resp) => {
  const options = {
    root: path.join(__dirname, 'public'),
    dotfiles: 'deny',
    headers: {
      'x-timestamp': Date.now(),
      'x-sent': true
    }
  };

  resp.sendFile(req.params.filename, options, (err) => {
    if (err) {
      console.log(err);
      resp.status(404).send("ไม่พบไฟล์");
    } else {
      console.log("ส่งแล้ว:", req.params.filename);
    }
  });
});

app.listen(8080, () => {
  console.log("ตัวอย่างเซิร์ฟเวอร์ไฟล์ express กำลังรับฟังที่พอร์ต 8080");
});
```
{% endtab %}
{% endtabs %}

LISTING 2 เวอร์ชัน Express ของเซิร์ฟเวอร์ไฟล์

ด้วย Express คุณมักจะเขียนตัวจัดการสำหรับเส้นทางต่างๆ ในแอปพลิเคชันของคุณ&#x20;

เส้นทางคืออะไร?&#x20;

เส้นทางใน Express คือ URL ซึ่งเป็นชุดของโฟลเดอร์หรือไฟล์หรือข้อมูลพารามิเตอร์ภายใน URL ตัวอย่างนี้มีเพียงเส้นทางเดียว: '/' ทุกอย่างหลังเครื่องหมายทับจะถูกจัดการเป็นข้อมูลพารามิเตอร์ที่บรรจุไว้ให้คุณในตัวแปร filename ไม่มีอะไรพิเศษเกี่ยวกับชื่อ filename เราสามารถตั้งชื่อตัวแปรนี้เป็นอะไรก็ได้ตามที่เราต้องการ

แต่ละตัวจัดการใน Express จะได้รับตัวแปรพารามิเตอร์อย่างน้อยสองตัว: ออบเจ็กต์คำขอ (request object) และออบเจ็กต์การตอบสนอง (response object) ออบเจ็กต์คำขอประกอบด้วยออบเจ็กต์ params ที่เก็บข้อมูลพารามิเตอร์ใดๆ ที่รวมมากับคำขอ/เส้นทาง ออบเจ็กต์การตอบสนองให้เมธอดสำหรับการตั้งค่าส่วนหัว HTTP และคุกกี้ การส่งไฟล์หรือข้อมูล JSON และอื่นๆ พารามิเตอร์ที่สามที่มักเรียกว่า next ก็สามารถระบุได้เช่นกัน ซึ่งจะอธิบายด้านล่าง

คุณสามารถสร้างเซิร์ฟเวอร์ไฟล์อย่างง่ายด้วยโค้ดที่น้อยลงใน Express โดยใช้ฟังก์ชัน static():

```javascript
var express = require("express");
var app = express();
app.use("/static", express.static(path.join(__dirname, "public")));
app.listen(8080, () => { ... });
```

ฟังก์ชัน app.use() จะเรียกใช้ฟังก์ชันมิดเดิลแวร์ที่ระบุ ใน Express&#x20;

ฟังก์ชันมิดเดิลแวร์คือฟังก์ชันที่โดยปกติจะได้รับออบเจ็กต์คำขอ ออบเจ็กต์การตอบสนอง และฟังก์ชัน next ในห่วงโซ่ของฟังก์ชันที่จัดการคำขอ ฟังก์ชัน next ไม่จำเป็นเสมอไป แต่การประมวลผลคำขอจะสิ้นสุดลงหากฟังก์ชันมิดเดิลแวร์ไม่เรียกใช้มันหลังจากที่ประมวลผลเสร็จสิ้น&#x20;

เหตุผลเบื้องหลังฟังก์ชันมิดเดิลแวร์ คือชุดของฟังก์ชันสามารถผลัดกันประมวลผลคำขอก่อนได้ ทำไมคุณถึงต้องการทำเช่นนี้?&#x20;

รูปที่ 7 แสดงสถานการณ์ที่ฟังก์ชันมิดเดิลแวร์สามฟังก์ชันประมวลผลแต่ละคำขอก่อน

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption><p>รูปที่ 7 ฟังก์ชัน Middleware ใน Express</p></figcaption></figure>

ในโค้ดก่อนหน้าที่ใช้ฟังก์ชัน static() พารามิเตอร์แรกของฟังก์ชัน app.use() คือเส้นทางที่ร้องขอ ในขณะที่ฟังก์ชัน express.static() เป็นฟังก์ชันมิดเดิลแวร์สำหรับการให้บริการทรัพยากรแบบสแตติก บรรทัดนั้นจะรับคำขอทั้งหมดสำหรับไฟล์ที่ขึ้นต้นด้วยเส้นทาง /static (เช่น http://www.examplesite.com/static/index.html) และพยายามให้บริการจากโฟลเดอร์ public ของแอปพลิเคชัน
