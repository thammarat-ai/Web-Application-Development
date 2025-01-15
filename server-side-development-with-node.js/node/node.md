# แอปพลิเคชัน Node อย่างง่าย

สมมติว่าคุณได้ติดตั้ง Node หรือมีสิทธิ์เข้าถึงแล้ว มาสร้างแอปพลิเคชัน Node อย่างง่ายกัน เราจะเริ่มต้นด้วยตัวอย่าง Hello World ตามปกติ สร้างไฟล์ใหม่ ป้อนโค้ดที่แสดงในโค้ด 1 ลงในไฟล์นั้น และบันทึกเป็น hello.js



{% tabs %}
{% tab title="แบบ commonjs" %}
```javascript
// แบบ commonjs
// ใช้งานโมดูล http 
const http = require('http');

// กำหนดค่าเซิร์ฟเวอร์ HTTP ให้ตอบกลับด้วยข้อความง่ายๆ สำหรับทุกคำขอ
const server = http.createServer(function (request, response) {
    response.writeHead(200, {"Content-Type": "text/plain"});
    response.write("Hello this is our first node.js application");
    response.end();
});

// รับฟังที่พอร์ต 8080 บน localhost
const port = 8080;
server.listen(port);

// แสดงข้อความบนเทอร์มินัล
console.log("Server running at port=" + port);
```
{% endtab %}

{% tab title="แบบ es module" %}
```javascript
// แบบ es module 
// เพิ่ม "type":"module" ใน package.json
// ใช้งานโมดูล http
import { createServer } from 'http';

// กำหนดค่าเซิร์ฟเวอร์ HTTP ให้ตอบกลับด้วยข้อความง่ายๆ สำหรับทุกคำขอ
const server = createServer(function (request, response) {
    response.writeHead(200, {"Content-Type": "text/plain"});
    response.write("Hello this is our first node.js application");
    response.end();
});

// รับฟังที่พอร์ต 8080 บน localhost
const port = 8080;
server.listen(port);

// แสดงข้อความบนเทอร์มินัล
console.log("Server running at port=" + port);
```
{% endtab %}
{% endtabs %}

Listing 1 Hello World ใน Node

คุณคิดว่าจะรันแอปพลิเคชันนี้อย่างไร? หากคุณพยายามเปิดไฟล์นี้โดยตรงในเบราว์เซอร์ มันจะไม่ทำงานด้วยเหตุผลเดียวกับที่การเปิดไฟล์ PHP โดยตรงในเบราว์เซอร์ไม่ทำงาน เราต้องบอก Node ให้เรียกใช้ไฟล์นี้

คุณทำอย่างไร? หากคุณได้ติดตั้ง Node ไว้ในเครื่องแล้ว คุณจะต้องเปิดหน้าต่างคำสั่ง/เทอร์มินัล นำทางไปยังโฟลเดอร์ที่คุณบันทึกไฟล์นี้ไว้ และป้อนคำสั่งต่อไปนี้:

```
node hello.js
```

นี่ควรแสดงข้อความว่าเซิร์ฟเวอร์กำลังทำงานที่พอร์ตเฉพาะ ตอนนี้คุณต้องสลับไปที่เบราว์เซอร์ของคุณ และส่งคำขอไปยัง localhost ที่พอร์ต 8080 หากทำงานได้ คุณควรเห็นบางอย่างคล้ายกับที่แสดงในรูปที่ 5

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption><p>รูปที่ 5 ตัวอย่างการรัน Hello World</p></figcaption></figure>

โค้ดนี้ทำอะไร? สังเกตบรรทัดแรกที่มีการเรียก require มันบอกให้รันไทม์ของ Node ใช้โมดูลชื่อ http คุณอาจจำ เพื่อให้การห่อหุ้มโค้ดที่ดีขึ้นและลดความขัดแย้งของเนมสเปซ Node ต้องพัฒนาระบบโมดูลของตัวเอง (มักเรียกว่า CommonJS) เนื่องจาก JavaScript (จนถึงเมื่อไม่นานมานี้) ไม่มีระบบโมดูล ณ เวลาที่เขียน เวอร์ชันล่าสุดของ Node รองรับไวยากรณ์โมดูล ES6 แบบใหม่ (เช่น การใช้คีย์เวิร์ด import) อย่างไรก็ตาม โค้ด Node ที่มีอยู่แล้วส่วนใหญ่ใช้ฟังก์ชัน require() และไม่ใช้คำสั่ง import

โมดูลของ Node คืออะไร?&#x20;

โมดูลก็คือไลบรารีฟังก์ชัน JavaScript ที่มีโค้ดเพิ่มเติมเพื่อห่อหุ้มฟังก์ชันไว้ภายในออบเจ็กต์ Node core มีโมดูลสำคัญหลายตัว (เช่น http ใน Listing 1) ที่ต้องการเพียงการเรียกฟังก์ชัน require() ที่เหมาะสม อย่างไรก็ตาม แอปพลิเคชัน Node ส่วนใหญ่มักต้องการการติดตั้งโมดูลเพิ่มเติม ซึ่งต้องใช้ npm หรือ Node Package Manager&#x20;

ส่วนที่เหลือของโค้ดใน Listing 1 ประกอบด้วยการเรียกใช้ createServer() ซึ่งเป็นฟังก์ชัน JavaScript ที่กำหนดไว้ในโมดูล http เช่นเดียวกับฟังก์ชัน Node อื่นๆ หลายตัว มันรับฟังก์ชันคอลแบ็คที่คุณกำหนดเข้าไป ในตัวอย่างนี้ มันส่งรหัสการตอบสนอง HTTP กลับไปพร้อมกับส่วนหัว HTTP Content-Type รวมถึงเนื้อหาข้อความบางส่วน เบราว์เซอร์จะแสดงเพียงเนื้อหาข้อความนั้น

รูปที่ 6 แสดงตัวอย่างที่ซับซ้อนขึ้นเล็กน้อย: เซิร์ฟเวอร์ไฟล์แบบสแตติก มันตอบสนองต่อคำขอ HTTP สำหรับไฟล์โดยตรวจสอบว่าไฟล์นั้นมีอยู่หรือไม่ หากไม่มี มันจะส่งเนื้อหา 404 ที่เหมาะสมกลับไปยังผู้ร้องขอ หากมีอยู่ มันจะส่งเนื้อหาของไฟล์ที่ร้องขอกลับไป นี่คือเวอร์ชันอย่างง่ายของ Apache หรือ IIS ของคุณเอง!



{% tabs %}
{% tab title="commonjs" %}
<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption><p>รูปที่ 6 Static file server</p></figcaption></figure>


{% endtab %}

{% tab title="es module" %}
```javascript
// แบบ es module
import { createServer } from 'http';
import { stat, readFile } from 'fs';
import { join, dirname } from 'path';
import { fileURLToPath } from 'url';

// Define __dirname for ES module
const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);

const server = createServer((request, response) => {
    let filename = join(__dirname, request.url);

    // ตรวจสอบว่าเป็นไดเรกทอรีหรือไม่
    stat(filename, (err, stats) => {
        if (err) {
            response.writeHead(500, {"Content-Type": "text/html"});
            response.write("<h1>500 Error</h1>\n");
            response.write(err + "\n");
            response.end();
            return;
        }

        if (stats.isDirectory()) {
            // Serve index.html if the directory is requested
            filename = join(filename, 'index.html');
            stat(filename, (err, stats) => {
                if (err || !stats.isFile()) {
                    response.writeHead(403, {"Content-Type": "text/html"});
                    response.write("<h1>403 Forbidden</h1>\n");
                    response.write("Access to directories is not allowed.\n");
                    response.end();
                    return;
                }

                // อ่านไฟล์และส่งกลับไปหาผู้ใช้
                readFile(filename, "binary", (err, file) => {
                    if (err) {
                        response.writeHead(500, {"Content-Type": "text/html"});
                        response.write("<h1>500 Error</h1>\n");
                        response.write(err + "\n");
                        response.end();
                        return;
                    }

                    response.writeHead(200);
                    response.write(file, "binary");
                    response.end();
                });
            });
            return;
        }

        // อ่านไฟล์และส่งกลับไปหาผู้ใช้
        readFile(filename, "binary", (err, file) => {
            if (err) {
                response.writeHead(500, {"Content-Type": "text/html"});
                response.write("<h1>500 Error</h1>\n");
                response.write(err + "\n");
                response.end();
                return;
            }

            response.writeHead(200);
            response.write(file, "binary");
            response.end();
        });
    });
});

const port = 8080;
server.listen(port, () => {
    console.log(`Server running at http://localhost:${port}/`);
});

```
{% endtab %}
{% endtabs %}
