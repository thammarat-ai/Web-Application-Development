# Project 3

ระดับความยาก: ปานกลาง

ภาพรวม ในโปรเจกต์นี้ คุณจะสร้างแอปพลิเคชันแชทที่ซับซ้อนมากขึ้น

### คำแนะนำ

1. คุณได้รับโฟลเดอร์ชื่อ project3 ซึ่งมีข้อมูลและไฟล์อื่นๆ ที่จำเป็นสำหรับโปรเจกต์นี้ ใช้ npm init เพื่อตั้งค่าโฟลเดอร์ และ npm install เพื่อเพิ่ม express
2. ตรวจสอบไฟล์ chat-adv-client-markup-only.html ในเบราว์เซอร์ มันแสดงมาร์กอัพของเวอร์ชันสมบูรณ์ คุณจะทำงานกับ chat-adv-client.html ที่ไม่มีมาร์กอัพเพิ่มเติม คุณจะเขียนโค้ดใน chat-adv-client.js เพื่อสร้างมาร์กอัพโดยอัตโนมัติตามการรับข้อความจากเซิร์ฟเวอร์
3. โค้ดเซิร์ฟเวอร์ของคุณจะต้องเก็บรักษารายการของออบเจ็กต์ผู้ใช้ สำหรับผู้ใช้ใหม่แต่ละคน คุณจะต้องบันทึกชื่อและหมายเลข ID ซึ่งควรเป็นตัวเลขสุ่มระหว่าง 1 ถึง 70 หมายเลขนี้จะถูกใช้โดยไคลเอนต์แชทเพื่อแสดงรูปโปรไฟล์จาก https://randomuser.me โค้ดเซิร์ฟเวอร์ของคุณจะต้องส่งรายชื่อผู้ใช้ที่อัปเดตแล้วไปยังไคลเอนต์ทั้งหมดเมื่อมีการเพิ่มผู้ใช้ใหม่ เนื่องจากเป็นตัวเลขสุ่ม จึงเป็นไปได้ที่ผู้ใช้สองคนอาจมีรูปโปรไฟล์เดียวกัน เพื่อความง่าย สมมติว่าชื่อผู้ใช้แต่ละคนไม่ซ้ำกัน ในฝั่งไคลเอนต์ เมื่อได้รับข้อความจากเซิร์ฟเวอร์ว่ามีผู้ใช้ใหม่ ควรแสดงข้อความและสร้างรายชื่อผู้ใช้ใหม่ในด้านซ้ายของแชทโดยใช้ข้อมูลรายชื่อผู้ใช้ที่ส่งมา
4. ไคลเอนต์แชทของคุณมีปุ่ม Leave เมื่อผู้ใช้คลิกปุ่มนี้ ควรส่งข้อความไปยังเซิร์ฟเวอร์ว่าผู้ใช้นี้ได้ออกไปแล้ว จากนั้นซ่อนหน้าต่างแชท เซิร์ฟเวอร์ควรลบผู้ใช้ออกจากรายการ แล้วส่งข้อความไปยังไคลเอนต์ทั้งหมดเกี่ยวกับการกระทำนี้และให้รายชื่อผู้ใช้ที่อัปเดตแล้ว ไคลเอนต์ที่เหลือควรแสดงข้อความและสร้างรายชื่อผู้ใช้ใหม่ในด้านซ้ายของแชทโดยใช้ข้อมูลรายชื่อผู้ใช้ที่ส่งมา
5. ไคลเอนต์แชทมีกล่องข้อความและปุ่ม Send เมื่อคลิกปุ่ม Send ควรแสดงข้อความโดยตรงในหน้าต่างแชทแล้วส่งข้อความไปยังเซิร์ฟเวอร์ เมื่อเซิร์ฟเวอร์ได้รับข้อความใหม่ ควรกระจายข้อความไปยังไคลเอนต์อื่นทั้งหมด (แต่ไม่ใช่ไคลเอนต์ที่สร้างข้อความ) ไคลเอนต์อื่นๆ ควรแสดงเนื้อหาข้อความ ผู้ใช้ที่สร้างข้อความ และเวลาปัจจุบัน
6. ดังนั้น ไคลเอนต์แชทสามารถแสดงข้อความสี่ประเภทในหน้าต่างแชท: ข้อความผู้ใช้เข้าร่วม ข้อความผู้ใช้ออก ข้อความแชทของผู้ใช้อื่น และข้อความแชทของผู้ใช้ปัจจุบัน มีคลาส CSS ที่เกี่ยวข้องสามคลาสที่ให้มา: .message-received, .message-sent และ .message-user โค้ดไคลเอนต์ของคุณควรกำหนดคลาสที่เหมาะสมขึ้นอยู่กับข้อความที่ได้รับ

คำแนะนำและการทดสอบ&#x20;

7\. ทดสอบโดยเปิดหน้าต่างหลายหน้าต่างด้วยชื่อผู้ใช้ที่แตกต่างกัน การส่งข้อความและการออกควรทำงานได้อย่างเหมาะสมและมีลักษณะดังที่แสดงในรูปที่ 15

<figure><img src="../../../.gitbook/assets/Screenshot 2568-01-17 at 21.36.45.png" alt=""><figcaption><p>รูปที่ 15</p></figcaption></figure>







