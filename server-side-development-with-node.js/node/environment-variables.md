# ตัวแปรสภาพแวดล้อม (Environment Variables)

ตัวแปรสภาพแวดล้อมเป็นกลไกสำหรับการกำหนดค่าแอปพลิเคชัน Node ของคุณ คุณสามารถดูตัวแปรสภาพแวดล้อมที่มีอยู่ในระบบของคุณได้ผ่าน:

```javascript
console.log(process.env);
```

สภาพแวดล้อมการโฮสต์บางแห่งจะตั้งค่าตัวแปรสภาพแวดล้อมบางตัวโดยอัตโนมัติ เช่น ค่า PORT อย่างไรก็ตาม คุณสามารถตั้งค่าตัวแปรสภาพแวดล้อมของคุณเองโดยใช้แพ็คเกจ dotenv ที่ได้รับความนิยม ด้วยคำสั่ง:

```
npm install dotenv
```

เมื่อติดตั้งแล้ว คุณสามารถสร้างตัวแปรสภาพแวดล้อมของคุณเองภายในไฟล์ชื่อ .env (ไม่มีตัวอักษรก่อนจุด) ไฟล์สภาพแวดล้อมนี้สามารถมีคู่ชื่อ=ค่า ได้หลายคู่ เช่น:

```
PORT=8080
BUILD=development
```

ภายในแอปพลิเคชัน Node ของคุณ คุณสามารถอ้างอิงค่าในไฟล์นี้โดยใช้แพ็คเกจ dotenv ดังที่แสดงในตัวอย่างต่อไปนี้:



{% tabs %}
{% tab title="commonjs" %}
```javascript
// ใช้งานแพ็คเกจ dotenv
require('dotenv').config();

// อ้างอิงค่าจากไฟล์ .env
console.log("build type=" + process.env.BUILD);
server.listen(process.env.PORT);
```
{% endtab %}

{% tab title="es module" %}
```javascript
// ใช้งานแพ็คเกจ dotenv
import dotenv from 'dotenv'

// อ้างอิงค่าจากไฟล์ .env
console.log("build type=" + process.env.BUILD);
server.listen(process.env.PORT);
```
{% endtab %}
{% endtabs %}

เป็นเรื่องปกติที่จะเก็บข้อมูลที่ละเอียดอ่อน เช่น API keys และค่าลับในไฟล์ .env นี้ ดังนั้นคุณมักจะต้องเพิ่ม .env ลงในไฟล์ .gitignore ของคุณเพื่อไม่ให้ข้อมูลนี้ถูกผลักไปยังที่เก็บสาธารณะโดยไม่ตั้งใจ
