# การแยกฟังก์ชันออกเป็นโมดูล

แม้ว่าโค้ดใน Listing 4 จะค่อนข้างตรงไปตรงมา แต่ถ้าเรามีเส้นทางห้าหรือหกเส้นทางหรือมากกว่านั้น? ในกรณีเช่นนี้ ไฟล์ Node เดียวของเราจะเริ่มซับซ้อนเกินไป วิธีที่ดีกว่าคือการแยกฟังก์ชันการกำหนดเส้นทางออกเป็นโมดูลแยกต่างหาก

โมดูลในแนวทาง CommonJS แบบดั้งเดิมใน Node แทนที่จะใช้คีย์เวิร์ด export ของ JavaScript คุณจะตั้งค่าคุณสมบัติ export ของออบเจ็กต์โมดูลแทน Listing 5 แสดงให้เห็นว่าคุณสามารถใส่ฟังก์ชันการอ่านข้อมูล JSON สำหรับ API ของเราไว้ในโมดูลแยกต่างหากได้อย่างไร สังเกตบรรทัดสุดท้ายใน Listing นี้ มันระบุว่าวัตถุใดจะสามารถเข้าถึงได้จากภายนอกโมดูลนี้; เนื่องจากฟังก์ชัน getCompanyData() ไม่รวมอยู่ในรายการวัตถุที่ส่งออก มันจึงเป็นส่วนตัวต่อโมดูล

คุณจะใช้งานโมดูลนี้ได้อย่างไร?&#x20;

เช่นเดียวกับโมดูล Node อื่นๆ คุณต้องใช้ฟังก์ชัน require() ตัวอย่างเช่น หากโค้ดใน Listing 5 ถูกบันทึกในไฟล์ชื่อ company-provider.js ในโฟลเดอร์ scripts คุณสามารถใช้งานมันได้ผ่านบรรทัดโค้ดต่อไปนี้:

```javascript
const companyProvider = require('./scripts/company-provider.js');
...
const data = companyProvider.getData();
```

คุณยังสามารถวางตรรกะตัวจัดการเส้นทางของคุณลงในโมดูลแยกต่างหากได้ Listing 6 แสดงให้เห็นว่าตัวจัดการเส้นทางใน Listing 4 จะมีลักษณะอย่างไรในโมดูล (เราจะบันทึกไว้ในโฟลเดอร์ scripts เป็น company-router.js)

```javascript
const fs = require('fs').promises;
const path = require('path');

// สำหรับตอนนี้ เราจะรับข้อมูลโดยการอ่านไฟล์ json ที่ให้มา
const jsonPath = path.join(__dirname, '../public', 'companies.json');
// รับข้อมูลแบบอะซิงโครนัส
let companies;
getCompanyData(jsonPath);
async function getCompanyData(jsonPath) {
    try {
        const data = await fs.readFile(jsonPath, "utf-8");
        companies = JSON.parse(data);
    } catch (err) { 
        console.log('เกิดข้อผิดพลาดในการอ่าน ' + jsonPath); 
    }
}
function getData() {
    return companies;
}
// ระบุว่าวัตถุใดจะสามารถเข้าถึงได้จากภายนอกโมดูล
module.exports = { getData };
```

LISTING 5 การกำหนดโมดูล



```javascript
// ส่งคืนบริษัททั้งหมด
const handleAll = (companyProvider, app) => {
    app.get('/companies/', (req, resp) => {
        // รับข้อมูลจาก provider บริษัท
        const companies = companyProvider.getData();
        resp.json(companies);
    });
}

// ส่งคืนบริษัทที่ร้องขอเพียงบริษัทเดียว
const handleSingleSymbol = (companyProvider, app) => {
    app.get('/companies/:symbol', (req, resp) => {
        const companies = companyProvider.getData();
        const symbolToFind = req.params.symbol.toUpperCase();
        const stock = companies.filter(obj => symbolToFind === obj.symbol);
        if (stock.length > 0) {
            resp.json(stock);
        } else {
            resp.json(jsonMessage(`Symbol ${symbolToFind} not found`));
        }
    });
};

// ส่งคืนบริษัททั้งหมดที่มีชื่อประกอบด้วยข้อความที่ระบุ
const handleNameSearch = (companyProvider, app) => {
    app.get('/companies/name/:substring', (req, resp) => {
        const companies = companyProvider.getData();
        const substring = req.params.substring.toLowerCase();
        const matches = companies.filter((obj) =>
            obj.name.toLowerCase().includes(substring)
        );
        if (matches.length > 0) {
            resp.json(matches);
        } else {
            resp.json(jsonMessage(`No company matches found for ${substring}`));
        }
    });
};

const jsonMessage = (msg) => {
    return { message: msg };
};

module.exports = {
    handleAll,
    handleSingleSymbol,
    handleNameSearch
};
```

LISTING 6 ตัวจัดการเส้นทางภายในโมดูล

ตัวจัดการเส้นทางเหล่านี้ในโมดูลจะถูกใช้งานอย่างไร?&#x20;

Listing 7 แสดงให้เห็นถึงสิ่งนี้ และยังแสดงวิธีการใช้งานตัวจัดการเส้นทางเหล่านี้ นอกจากนี้ยังแสดงวิธีการรวมการจัดการไฟล์แบบสแตติกและการจัดการ 404 ที่กำหนดเองสำหรับเส้นทางที่ไม่รู้จัก

```javascript
// moudule separate
const path = require('path');
const express = require('express');
const app = express();

// อ้างอิงโมดูลของเราเอง
const companyProvider = require('./scripts/company-provider.js');
const companyHandler = require('./scripts/company-router.js');

// จัดการคำขอสำหรับทรัพยากรแบบสแตติก
app.use('/static', express.static(path.join(__dirname, 'public')));

// กำหนดเส้นทางสำหรับการจัดการบริษัท
companyHandler.handleAll(companyProvider, app);
companyHandler.handleSingleSymbol(companyProvider, app);
companyHandler.handleNameSearch(companyProvider, app);

// สำหรับคำขออื่นๆ ให้แสดงข้อผิดพลาด 404
app.use((req, resp) => {
    resp.status(404).send('ไม่สามารถค้นหาทรัพยากรที่ร้องขอได้!');
});

// เริ่มเซิร์ฟเวอร์
const port = 8080;
app.listen(port, () => {
    console.log(`เซิร์ฟเวอร์กำลังทำงานที่ http://localhost:${port}`);
});
```

LISTING 7 เซิร์ฟเวอร์ API สำหรับข้อมูลบริษัท



<details>

<summary><strong>ทดสอบความรู้ของคุณ # 1</strong></summary>

สร้าง API Node ใหม่โดยใช้ Express สำหรับไฟล์ [photos.json](https://github.com/thammarat-ai/web-dev-code/blob/main/exampleFiles/photos.json) ที่ให้มา&#x20;

1. สร้างไฟล์ใหม่ชื่อ `test-know1.js` คุณจะต้องสร้างเส้นทางสามเส้นทาง
2. เส้นทางแรกจะเป็น "/": มันควรส่งคืนวัตถุภาพทั้งหมดในไฟล์ JSON
3. เส้นทางที่สองจะเป็น "/:id" (เช่น /30): มันควรส่งคืนเพียงภาพเดียวตามคุณสมบัติ id ในไฟล์ หากค่า id ที่ให้มาไม่มีในไฟล์ ให้ส่งคืน JSON ที่มีข้อความข้อผิดพลาดที่เหมาะสม
4. เส้นทางที่สามจะเป็น "/iso/:iso" (เช่น /iso/ca): มันควรส่งคืนภาพทั้งหมดที่มีคุณสมบัติ iso ตรงกับค่าที่ให้มา ควรทำงานได้เหมือนกันสำหรับค่า iso ตัวพิมพ์เล็กและตัวพิมพ์ใหญ่ หากค่า iso ที่ให้มาไม่มีในไฟล์ ให้ส่งคืน JSON ที่มีข้อความข้อผิดพลาดที่เหมาะสม
5. เพิ่มการจัดการไฟล์แบบสแตติกไปยัง `test-know1.js` เพื่อให้ง่ายต่อการทดสอบเส้นทางของคุณ สร้างไฟล์ HTML ง่ายๆ ชื่อ `test-know1.html` ในโฟลเดอร์ public ไฟล์ HTML นี้ควรมีลิงก์ไปยังเส้นทางเหล่านี้ทั้งหมด

</details>

