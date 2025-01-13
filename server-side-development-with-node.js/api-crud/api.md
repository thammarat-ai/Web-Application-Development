# การส่งข้อมูลไปยัง API

คุณได้เรียนรู้ว่าการจัดการข้อมูลฟอร์มขึ้นอยู่กับว่าแอตทริบิวต์ `method` ของฟอร์มถูกตั้งค่าเป็น **GET** หรือ **POST**&#x20;

ข้อมูลฟอร์มที่ส่งผ่าน **GET** จะถูกเพิ่มในรูปแบบพารามิเตอร์ของ query string ที่ URL&#x20;

ในขณะที่ข้อมูลฟอร์มที่ส่งผ่าน **POST** จะเพิ่ม query string หลังจากส่วน HTTP heade rสิ่งที่ไม่ได้กล่าวถึงในตอนนั้น คือเบราว์เซอร์จะตั้งค่า HTTP header `Content-Type` เป็น `application/x-www-form-urlencoded` โดยอัตโนมัติ อย่างไรก็ตาม ยังมีวิธีอื่นในการส่งข้อมูลจากเบราว์เซอร์ไปยังเซิร์ฟเวอร์นอกเหนือจากการใช้ query string parameters หากไม่ได้ใช้ HTML forms (เช่น การใช้ JavaScript fetch) โดยสามารถส่งข้อมูลในรูปแบบ plain text, JSON, XML หรือเนื้อหาไฟล์ก็ได้ ซึ่งต้องตั้งค่า `Content-Type` ให้เหมาะสม เช่นในตัวอย่างในรูปที่ 8

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption><p>รูปที่ 8 การส่งข้อมูลให้ API</p></figcaption></figure>

สิ่งสำคัญที่ควรรู้คือ Express เริ่มต้นจะไม่จัดการข้อมูล **POST** หรือ **PUT** ที่ส่งมาจากเบราว์เซอร์โดยอัตโนมัติ แต่เราสามารถตั้งค่าให้มันจัดการได้ง่ายๆ โดยเพิ่ม middleware ต่อไปนี้ในเซิร์ฟเวอร์ก่อนแฮนด์เลอร์ใดๆ (เช่น ใน Listing 7 จะปรากฏก่อน `app.use()` สำหรับจัดการคำขอ static):

```javascript
// สำหรับการ parse ข้อมูล application/json 
app.use(express.json());
// สำหรับการ parse ข้อมูล application/x-www-form-urlencoded 
app.use(express.urlencoded({ extended: true }));
```
