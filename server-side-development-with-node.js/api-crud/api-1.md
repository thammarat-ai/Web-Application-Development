# เครื่องมือสำหรับการทดสอบ API

เมื่อคุณสร้าง API ที่ใช้ HTTP verbs เช่น **POST**, **PUT**, และ **DELETE** คุณอาจใช้ JavaScript `fetch()` เพื่อส่งคำขอเหล่านี้ อย่างไรก็ตาม ในหลายกรณี API ที่พัฒนาด้วย Node.js อาจถูกสร้างแยกจากส่วนของ front-end โดยทีมพัฒนาที่แตกต่างกัน&#x20;

ในกรณีเช่นนี้ ทีมพัฒนา API จะทดสอบ API ของพวกเขาอย่างไร?&#x20;

เนื่องจากเบราว์เซอร์ไม่สามารถส่งคำขอ **PUT** หรือ **DELETE** ได้โดยตรงหากไม่มีการเขียนโค้ด JavaScript และแม้ว่า HTML ฟอร์มธรรมดาจะสามารถส่งคำขอ **POST** ได้โดยไม่ต้องใช้ JavaScript แต่ฟอร์มจะใช้ `application/x-www-form-urlencoded` เสมอ ซึ่งไม่สามารถใช้ทดสอบการส่งข้อมูล JSON ได้

ด้วยเหตุนี้ นักพัฒนา API มักใช้เครื่องมือทดสอบ API ของบุคคลที่สาม เช่น **Postman** หรือ **Insomnia** ซึ่งเป็นเครื่องมือยอดนิยม ตัวอย่างในรูปที่ 9 แสดงให้เห็นว่าคุณสามารถระบุ URL endpoint ที่ต้องการทดสอบ ควบคุม HTTP verbs ที่จะใช้ กำหนดค่า `Content-Type` header และป้อนข้อมูลที่ต้องการส่งได้อย่างง่ายดาย

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption><p>รูปที่ 9 เครื่องมือทดสอบ API</p></figcaption></figure>

#### เปรียบเทียบ Postman และ Insomnia

* **Postman**: มีอินเทอร์เฟซที่ใช้งานง่าย รองรับการสร้างและจัดการคำขอ API การเขียนและรันการทดสอบอัตโนมัติ รวมถึงการทำงานร่วมกับทีม นอกจากนี้ยังมีการผสานรวมกับเครื่องมืออื่นๆ เช่น Jira, GitHub, และ Slack
* **Insomnia**: โดดเด่นด้วยอินเทอร์เฟซที่เรียบง่าย รองรับหลายโปรโตคอล และมีความสามารถในการจัดการตัวแปรและสภาพแวดล้อมที่ยืดหยุ่น เหมาะสำหรับนักพัฒนาที่ต้องการความคล่องตัวและความเรียบง่าย

ทั้งสองเครื่องมือนี้มีข้อดีเฉพาะตัว การเลือกใช้งานขึ้นอยู่กับความต้องการของโครงการและความถนัดของผู้ใช้งาน
