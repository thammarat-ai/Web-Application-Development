# สถาปัตยกรรมแบบ Push

Node โดดเด่นอย่างมากในเว็บแอปพลิเคชันแบบ push นี่หมายความว่าอย่างไร?&#x20;

เว็บแอปพลิเคชันที่เราได้ลองทำมาจนถึงตอนนี้ล้วนเป็นแบบ pull ทั้งสิ้น เว็บเซิร์ฟเวอร์จะอยู่ในสถานะว่างจนกว่าคุณจะส่งคำขอ: เราจะเรียกว่าผู้ใช้ดึง (pull) ข้อมูล/บริการจากเซิร์ฟเวอร์ นั่นคือ ผู้ใช้เป็นผู้ควบคุมการส่งคำขอ และหน้าที่ของเซิร์ฟเวอร์คือการตอบสนองต่อคำขอนั้น ในขณะที่ลักษณะการดึงข้อมูล (pull-based) ของเว็บทำงานได้ดี&#x20;

แต่ก็มีแอปพลิเคชันบางประเภทที่จำเป็นต้องใช้การผลักข้อมูล (push-based) นั่นคือ บางแอปพลิเคชันต้องผลักข้อมูลจากเซิร์ฟเวอร์ไปยังไคลเอนต์&#x20;

การโทรศัพท์เป็นแบบ push-based: ระบบโทรศัพท์หลักจะส่งข้อความ (สายเรียกเข้า) ไปยังโทรศัพท์ และโทรศัพท์ก็ตอบสนอง (ด้วยการดังขึ้น)

ตัวอย่างคลาสสิกของเว็บแอปพลิเคชันแบบ push คือ ฟีเจอร์แชทที่อยู่ภายในหน้าเว็บ ดังแสดงในรูปที่ 1

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption><p>รูปที่ 1 ตัวอย่างของเว็บ push แอพพลิเคชั้น</p></figcaption></figure>

เซิร์ฟเวอร์ต้องตอบสนองต่อข้อความแชทที่เข้ามาโดยส่งต่อไปยังทุกฝ่ายที่กำลังรับฟังในห้องแชท แม้ว่าจะสามารถสร้างแอปพลิเคชันประเภทนี้โดยใช้สภาพแวดล้อมเช่น PHP ได้ แต่โดยทั่วไปแล้วจะต้องใช้การ polling ที่ไม่มีประสิทธิภาพ (นั่นคือ การให้เซิร์ฟเวอร์ "ถาม" ไคลเอนต์ซ้ำๆ ว่ามีอะไรใหม่หรือไม่)&#x20;

ในทางตรงกันข้าม สภาพแวดล้อม Node เหมาะสมอย่างยิ่งสำหรับการสร้างแอปพลิเคชันประเภทนี้ จริงๆ แล้ว บทช่วยสอนออนไลน์เกี่ยวกับ Node หลายแห่งมักจะสร้างเซิร์ฟเวอร์แชทเป็นตัวอย่างแอปพลิเคชันแรกหลังจากตัวอย่าง "Hello World" ที่เป็นธรรมเนียมปฏิบัติ