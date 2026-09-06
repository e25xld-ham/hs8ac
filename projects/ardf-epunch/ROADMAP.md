# แผนพัฒนา HS8AC ARDF ePunch

## Phase 0 — กำหนดแนวคิดและขอบเขต

สถานะ: **กำลังดำเนินการ**

- กำหนดเป้าหมายของระบบ
- กำหนดหลักการ Offline-first
- กำหนดบทบาทของสถานี START / FOX / FINISH
- กำหนดแนวคิดแท็กประจำตัวนักกีฬา
- เลือกตระกูล Hardware สำหรับ Prototype V1
- เผยแพร่สถาปัตยกรรมและที่มาของโครงการ

## Phase 1 — Prototype บนโต๊ะ

Hardware เป้าหมาย:

- LILYGO T-A7670G R2
- PN532 NFC Reader
- DS3231 RTC
- microSD
- Passive NFC/RFID Tag 10 ชิ้น
- Buzzer + LEDs

Milestone:

1. ESP32 เปิดทำงานได้เสถียร
2. อัปโหลด Firmware จากคอมพิวเตอร์ได้
3. Reader ตรวจพบ Tag ทดสอบครบ 10/10 ซ้ำ ๆ ได้
4. อ่านและตั้งค่า RTC ได้
5. บันทึก Punch ลง Local Storage แบบถาวรได้
6. LED/Buzzer ยืนยัน Punch ได้
7. Restart แล้ว Punch ที่ยังไม่ Sync ต้องไม่หาย
8. LTE ใช้งานกับ SIM ของเครือข่ายมือถือในประเทศไทยได้
9. Test Event ที่ผ่าน Authentication ส่งถึง HS8AC Backend ได้

## Phase 2 — ทดสอบ Offline Queue

การทดสอบ:

- ถอด SIM แล้ว Punch หลาย Tag
- ปิด LTE ระหว่างการ Punch
- Restart เครื่องขณะที่ยังมี Event ไม่ได้ Sync
- เปิด LTE กลับมาและตรวจว่าข้อมูลถูกส่งย้อนหลังครบ
- จงใจส่ง Network Event ซ้ำ
- ตรวจ Server Idempotency
- ตรวจว่า Punch ที่เครื่องยืนยันแล้วไม่มีรายการใดหาย

เงื่อนไขผ่าน:

**Punch ทดสอบทุกครั้งที่เครื่องยืนยันสำเร็จ ต้องสามารถกู้คืนได้หลัง Network หลุดและ Restart**

## Phase 3 — Prototype ภาคสนาม START + FOX 1

นำ Reader อิสระ 2 เครื่องไปใช้งาน:

- CP-START
- CP-F01

ใช้ Tag นักกีฬาประมาณ 10 ชิ้น

วัดและทดสอบ:

- ความเร็วในการอ่าน Tag
- ความสะดวกขณะนักกีฬากำลังวิ่ง
- พฤติกรรมจากการแตะซ้ำ/แตะโดยไม่ตั้งใจ
- ประสิทธิภาพ LTE
- อายุการใช้งานแบตเตอรี่
- Clock Drift
- การทำงานกลางแจ้งในสภาพร้อน/ชื้น
- การกู้การเชื่อมต่อเมื่อสัญญาณมือถือไม่ดี

## Phase 4 — HS8AC Cloud + Live Dashboard

เชื่อมเข้ากับ `ardf.hs8ac.com`

กรรมการควรเห็น:

- การลงทะเบียนนักกีฬาและการจับคู่ Tag
- Start Event แบบสด
- FOX 1 Event แบบสด
- Feed ของ Punch ล่าสุด
- เวลารวมเบื้องต้น
- สถานะ Online/Offline ของ Device
- สัญญาณ LTE/สถานะแบตเตอรี่
- เครื่องหมาย Punch ที่ Sync ย้อนหลัง
- Audit/Event Logs

## Phase 5 — ปรับระบบให้พร้อมใช้งานจริง

หลัง Prototype ภาคสนามผ่าน:

- ทบทวน PN532 เทียบกับ NFC Reader รุ่นใหม่สำหรับ Production
- สรุป Electrical Design
- กำหนด Connector สำหรับ Production
- เพิ่ม Surge/ESD/Power Protection
- ปรับปรุงระบบแบตเตอรี่
- ออกแบบกล่องกันน้ำ
- ออกแบบขั้นตอนบำรุงรักษาและซ่อม
- กำหนดกระบวนการ Update Firmware
- กำหนด Device Provisioning และ Key Rotation

## Phase 6 — Custom PCB

เริ่มขั้นนี้เมื่อ Pin Assignment, Power Design และ Firmware Interface คงที่แล้วเท่านั้น

เป้าหมาย:

- ลดสายต่อหลวม
- ลดความผิดพลาดในการประกอบ
- ทำให้ทุกสถานีเป็นมาตรฐานเดียวกัน
- ซ่อมง่ายขึ้น
- ระบุ Test Point ชัดเจน
- มีหมายเลข Hardware Revision ของ HS8AC ที่ตรวจสอบย้อนหลังได้

ตัวอย่างชื่อ Revision:

```text
HS8AC-EPUNCH-READER Rev.A
HS8AC-EPUNCH-READER Rev.B
```

## Phase 7 — ชุดการแข่งขันเต็มระบบ

สร้างสถานีอิสระทั้งหมด 7 เครื่อง:

```text
START
FOX 1
FOX 2
FOX 3
FOX 4
FOX 5
FINISH
```

ต้องผ่านทั้งการจำลองการแข่งขันและการทดสอบบนสนามจริง ก่อนนำไปเป็นระบบหลักในการตัดสินผลอย่างเป็นทางการ

## Phase 8 — เอกสารและการทำซ้ำระบบ

เผยแพร่เอกสารให้ทีมวิทยุสมัครเล่นอื่นสามารถสร้างระบบตามได้อย่างปลอดภัย ได้แก่:

- BOM
- Wiring Diagram
- Pinout Table
- คู่มือ Build Firmware
- Server/API Specification
- Deployment Guide
- Checklist ก่อนติดตั้งภาคสนาม
- Checklist วันแข่งขัน
- Troubleshooting Guide
- Test Procedure

ข้อมูล Credential และ Secret ของ Production ต้องไม่ถูกเผยแพร่

## ทิศทางระยะยาว

เป้าหมายระยะยาวคือสร้างแพลตฟอร์ม Electronic Punching สำหรับ ARDF ที่ใช้งานจริงได้ ราคาประหยัด มีต้นกำเนิดจาก HS8AC ประเทศไทย และสามารถนำไปปรับใช้โดยสมาคมอื่นได้ โดยไม่ต้องพึ่งระบบจับเวลาแบบ Proprietary ที่มีราคาสูง
