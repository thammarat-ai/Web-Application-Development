# การเพิ่มเส้นทาง

เพื่อทำให้บริการเว็บที่สร้างขึ้นในส่วนก่อนหน้านี้มีประโยชน์มากขึ้น เรามาเพิ่มเส้นทางเพิ่มเติมกัน สังเกตว่าใน Express การกำหนดเส้นทางหมายถึงกระบวนการในการกำหนดว่าระบบจะตอบสนองต่อคำขออย่างไร ตัวอย่างเช่น แทนที่จะแสดงบริษัททั้งหมด เราอาจต้องการแสดงบริษัทเดียวที่ระบุโดยสัญลักษณ์ของมัน หรือบริษัทบางส่วนตามเกณฑ์ที่กำหนด คำขอที่แตกต่างกันเหล่านี้มักจะถูกแยกออกโดยใช้เส้นทาง URL ที่แตกต่างกัน (แทนที่จะใช้พารามิเตอร์ใน query string)

เราจะเพิ่มเส้นทางใหม่สองเส้นทาง: `/companies/:symbol` (ซึ่งจะส่งคืน JSON สำหรับวัตถุบริษัทเดียวที่ตรงกับสัญลักษณ์หุ้นที่ให้มา) และ `/companies/name/:substring` ซึ่งจะส่งคืนบริษัททั้งหมดที่มีชื่อประกอบด้วย substring ที่ให้มา

การเพิ่มเส้นทางใหม่เป็นเพียงการเพิ่มการเรียกใช้ `app.get()` สำหรับแต่ละเส้นทาง Listing 4 แสดงการดำเนินการของทั้งสามเส้นทางใน API



```javascript
// ส่งคืนบริษัททั้งหมดเมื่อมีการร้องขอรูท
app.get('/', (req, resp) => { resp.json(companies) } );

app.get('/company/:symbol', (req, res) => 
    {
        const symbol = req.params.symbol.toLowerCase();
        const companies = getData(); // getData มาจาก company-provider.js
        const company = companies.find(c => c.symbol.toLowerCase() === symbol);
      
        if (company) {
            res.json(company);
        } else {
           res.status(404).send('ไม่พบบริษัทที่มีสัญลักษณ์นี้');
    }
 });
 
// ส่งคืนบริษัทที่มีชื่อประกอบด้วยข้อความที่ระบุ เช่น /companies/name/dat
app.get('/companies/name/:substring', (req, res) => {
    const substring = req.params.substring.toLowerCase();
    const companies = getData();
    const matchingCompanies = companies.filter(company => 
        company.name.toLowerCase().includes(substring)
    );
    if (matchingCompanies.length > 0) {
        res.json(matchingCompanies);
    } else {
        res.status(404).send('ไม่พบบริษัทที่มีชื่อประกอบด้วย substring นี้');
    }
});
```

LISTING 4 การเพิ่มเส้นทางไปยัง API

**หมายเหตุ** คุณอาจสงสัยว่าทำไมฟังก์ชันการกำหนดเส้นทางใน Express ใน Listing 4 ถึงชื่อว่า `get()`คำอธิบายค่อนข้างตรงไปตรงมา คุณใช้ `app.get()` สำหรับคำขอ HTTP GET, `app.post()` สำหรับคำขอ POST, `app.put()` สำหรับคำขอ PUT, และ `app.delete()` สำหรับคำขอ DELETE
