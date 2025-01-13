# API ง่ายๆ

Listing 3 ให้โค้ดสำหรับ API ที่ง่ายมาก มันอ่านไฟล์ข้อมูล JSON และส่งคืนข้อมูล JSON เมื่อมีการร้องขอ URL

สังเกตโค้ดที่เน้นใน Listing 3 มันใช้วิธีการคอลแบ็คแบบดั้งเดิมของ Node&#x20;

Node มีมาตั้งแต่ก่อนที่ Promises และ async...await จะเกิดขึ้นเกือบหนึ่งทศวรรษ ดังนั้นแพ็คเกจส่วนใหญ่ใน Node จึงใช้ฟังก์ชันคอลแบ็ค โดยทั่วไปแล้ว ฟังก์ชันคอลแบ็คใน Node จะรับพารามิเตอร์สองตัว: ออบเจ็กต์ข้อผิดพลาด (error object) และออบเจ็กต์ข้อมูล (data object) ในกรณีนี้ ออบเจ็กต์ข้อมูลในคอลแบ็ค readFile() จะมีเนื้อหาของไฟล์

```javascript
// เรียกใช้งานโมดูลที่จำเป็น
const fs = require('fs');
const path = require('path');
const express = require('express');
const app = express();
// สำหรับตอนนี้ เราจะอ่านไฟล์ json จากโฟลเดอร์ public
const jsonPath = path.join(__dirname, 'public', 'companies.json');
// รับข้อมูลโดยใช้วิธีการคอลแบ็คแบบดั้งเดิมของ Node
let companies;
fs.readFile(jsonPath, (err, data) => {
    if (err)
        console.log('ไม่สามารถอ่านไฟล์ข้อมูล json ได้');
    else
        companies = JSON.parse(data);
});
// ส่งคืนบริษัททั้งหมดเมื่อมีการร้องขอรูท
app.get('/', (req, resp) => { resp.json(companies) });
// ใช้ express เพื่อฟังที่พอร์ต
let port = 8080;
app.listen(port, () => {
    console.log("เซิร์ฟเวอร์กำลังทำงานที่พอร์ต= " + port);
});
```

LISTING 3 API ง่ายๆ โดยใช้วิธีการคอลแบ็ค



ตั้งแต่ Node v11 (ปลายปี 2018) Node ได้มีการสนับสนุน async...await รวมถึงเวอร์ชัน promisified ของแพ็คเกจในตัวหลายตัวด้วย คุณสามารถกำจัดคอลแบ็คใน Listing 3 และใช้ async...await ตามที่แสดงในด้านล่างนี้:

```javascript
// ใช้เวอร์ชัน promisified ของแพ็คเกจ fs
const fs = require('fs').promises;
...
let companies;
getCompanyData(jsonPath);
...
async function getCompanyData(jsonPath) {
    try {
        const data = await fs.readFile(jsonPath, "utf-8");
        companies = JSON.parse(data);
    } catch (err) {
        console.log('เกิดข้อผิดพลาดในการอ่าน ' + jsonPath);
    }
}
```

