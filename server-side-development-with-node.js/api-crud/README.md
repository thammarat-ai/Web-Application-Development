---
icon: '4'
---

# การสร้าง API CRUD

สำหรับแอปพลิเคชันที่ใช้ JavaScript อย่างเข้มข้น มันเป็นเรื่องปกติที่เว็บ API จะให้ความสามารถในการดึงข้อมูล นอกเหนือจากการสร้าง แก้ไข และลบข้อมูลด้วย เนื่องจากบริการเว็บ REST มีข้อจำกัดเฉพาะ HTTP จึงเป็นเรื่องปกติที่จะใช้ HTTP verbs ที่แตกต่างกันเพื่อแสดงว่าเราต้องการสร้าง ดึงข้อมูล แก้ไข หรือลบ (CRUD) ข้อมูล แม้ว่าเราจะเชื่อมโยง HTTP verb กับการกระทำ CRUD ได้ แต่เป็นแบบแผนในการใช้&#x20;

**GET** สำหรับการร้องขอดึงข้อมูล,&#x20;

**POST** สำหรับการสร้างข้อมูล,&#x20;

**PUT** สำหรับการแก้ไขข้อมูล, และ&#x20;

**DELETE** สำหรับการลบข้อมูล

สำหรับ API เว็บจริงที่มีพฤติกรรม CRUD API จะแก้ไขฐานข้อมูลที่อยู่ด้านล่างสำหรับการร้องขอ **POST**, **PUT**, และ **DELETE** ในบทถัดไป คุณจะทำงานกับฐานข้อมูล ดังนั้นในตัวอย่างนี้ เราจะแก้ไขข้อมูลในหน่วยความจำแทน

ตัวอย่างเช่น โดยใช้ตัวอย่าง API บริษัทจากส่วนก่อนหน้า เพื่อใช้ฟังก์ชันการแก้ไข คุณอาจเพิ่มแฮนด์เลอร์ที่แยกออกมาสำหรับ HTTP verbs อื่นๆ ภายในแฮนด์เลอร์เส้นทางบริษัทเดียว เช่นแสดงในโค้ดต่อไปนี้:

```javascript
const handleSingleSymbol = (companyProvider, app) => {
  app.get('/companies/:symbol', (req, resp) => {
    // ...
  });
  app.put('/companies/:symbol', (req, resp) => {
    // ...
  });
  app.post('/companies/:symbol', (req, resp) => {
    // ...
  });
  app.delete('/companies/:symbol', (req, resp) => {
    // ...
  });
};
```

โค้ดตัวอย่างสำหรับแฮนด์เลอร์ **PUT** จะใช้ข้อมูลที่ส่งมาเป็นส่วนหนึ่งของการร้องขอเพื่อแก้ไขข้อมูลในอาร์เรย์ JSON ในหน่วยความจำ:

```javascript
app.put('/companies/:symbol', (req, resp) => {

const symbol = req.params.symbol.toUpperCase();
        const updatedCompany = req.body;
        let companies = getData();
        const index = companies.findIndex(c => c.symbol.toUpperCase() === symbol);
        if (index !== -1) {
            companies[index] = updatedCompany;
            try {
                await fs.writeFile(jsonPath, JSON.stringify(companies, null, 2));
                res.status(200).json(updatedCompany);
            } catch (err) {
                res.status(500).send('เกิดข้อผิดพลาดในการบันทึกข้อมูล');
            }
        } else {
            res.status(404).send('ไม่พบบริษัทที่มีสัญลักษณ์นี้');
        }

});

```

Listing 8 Sample PUT handler
