# HS8AC ARDF ePunch

> ระบบบันทึกจุดอิเล็กทรอนิกส์ราคาประหยัดสำหรับการแข่งขัน Amateur Radio Direction Finding (ARDF) ออกแบบให้ทำงานแบบ Offline-first และรายงานผลแบบเรียลไทม์ โดยริเริ่มจาก HS8AC / E25XLD ประเทศไทย

## วิสัยทัศน์

HS8AC ARDF ePunch เป็นโครงการพัฒนาระบบอิเล็กทรอนิกส์ราคาประหยัดเพื่อยกระดับการแข่งขัน ARDF ในประเทศไทย เป้าหมายคือเปลี่ยนจากการเจาะบัตรหรือประทับตราบนกระดาษแบบเดิม ไปสู่ระบบอิเล็กทรอนิกส์ที่สมาคมวิทยุสมัครเล่นสามารถประกอบ ดูแล ซ่อม และต่อยอดได้เอง

หลักการสำคัญที่สุดของระบบคือ:

**สถานีภาคสนามต้องทำงานต่อได้ แม้อินเทอร์เน็ตจะใช้งานไม่ได้**

การรายงานผลขึ้น Cloud แบบเรียลไทม์เป็นความสามารถเพิ่มเติม ไม่ใช่เงื่อนไขที่ทำให้ Punch ถูกบันทึกหรือไม่

## Prototype V1

Prototype รุ่นแรกจะใช้เพียง:

- 1 × สถานี START
- 1 × สถานี FOX 1
- แท็ก NFC/RFID สำหรับนักกีฬาประมาณ 10 ชิ้น
- HS8AC Cloud Backend
- ระบบติดตามผลแบบสดที่ `ardf.hs8ac.com`

เมื่อผ่านการทดสอบภาคสนามแล้ว ระบบสามารถขยายเป็น:

- START
- FOX 1
- FOX 2
- FOX 3
- FOX 4
- FOX 5
- FINISH

รวมทั้งหมด **7 สถานีอิสระ**

## แท็กของนักกีฬา

นักกีฬาแต่ละคนจะได้รับ Passive NFC/RFID Tag ความถี่ 13.56 MHz

คุณสมบัติของแท็ก:

- ไม่มีแบตเตอรี่
- ไม่ต้องชาร์จ
- นำกลับมาใช้ซ้ำได้
- ทำเป็นพวงกุญแจ สายรัดข้อมือ หรือป้ายประจำตัวนักกีฬาได้
- UID ของแท็กจะถูกผูกกับข้อมูลนักกีฬาในฐานข้อมูลการแข่งขัน

ตัวอย่าง:

```text
Athlete: A025
Callsign: HS8XYZ
Tag UID: 04:A2:9C:72:D3:41:80
```

## สถาปัตยกรรมของเครื่องอ่านภาคสนาม

ทุกสถานี START / FOX / FINISH เป็น Reader ที่ทำงานเป็นอิสระจากกัน

ฮาร์ดแวร์เป้าหมายของ Prototype:

- Controller ที่ใช้ ESP32
- โมเด็ม 4G LTE
- PN532-class NFC Reader สำหรับ Prototype V1
- RTC ระดับ DS3231
- Local Storage (microSD / flash)
- LED แสดงสถานะ
- Buzzer
- แบตเตอรี่

แพลตฟอร์ม Controller ที่เลือกใช้เบื้องต้น:

**LILYGO T-A7670G R2**

บอร์ดนี้รวม ESP32 + LTE + SIM + microSD ไว้ด้วยกัน ช่วยลดจำนวนสาย จุดเชื่อมต่อ และจุดเสีย เมื่อเทียบกับการใช้ Arduino พื้นฐานร่วมกับโมดูลหลายตัวแยกกัน

## ลำดับการ Punch

Reader ต้องบันทึกเหตุการณ์ลง Local Storage ให้สำเร็จก่อน แล้วจึงค่อยพยายามส่งขึ้น Cloud

```text
แท็กนักกีฬา
    |
    v
NFC Reader ตรวจพบ UID
    |
    v
อ่านเวลาจาก RTC ภายในเครื่อง
    |
    v
บันทึก Punch ลง Local Storage ก่อน
    |
    +--> Beep + ไฟเขียว = รับ Punch สำเร็จ
    |
    v
พยายามส่งข้อมูลผ่าน 4G
    |
    +--> Server ACK -> ทำเครื่องหมายว่า Sync แล้ว
    |
    +--> ไม่มี Network -> เก็บไว้ใน Retry Queue
```

การที่ 4G หลุดจะต้อง **ไม่ทำให้ Punch หายเด็ดขาด**

## กติกาเรื่องเวลา

เวลาที่ถือเป็นเวลาจริงของ Punch คือเวลาที่ Reader ภาคสนามตรวจพบแท็กของนักกีฬา

ห้ามใช้เวลาที่ Server ได้รับ HTTP/MQTT เป็นเวลาหลัก เพราะ Cellular Network อาจมี Delay แตกต่างกันได้

ตัวอย่าง Event:

```json
{
  "event": "CHUMPHON_ARDF_2026",
  "station": "FOX01",
  "athlete": "A025",
  "tag_uid": "04A29C72D34180",
  "punch_time": "2026-12-05T10:42:16.384+07:00",
  "sequence": 1482,
  "signal": -83,
  "battery": 78
}
```

## ความทนทานแบบ Offline-first

Reader ทุกเครื่องต้องรองรับ:

- การเก็บ Event ภายในเครื่อง
- Sequence Number แบบถาวร
- การกู้สถานะหลัง Restart
- Retry Queue
- Duplicate Detection
- อัปโหลดย้อนหลังเมื่อ LTE กลับมา
- Server Acknowledgement

Reader ต้องยังสามารถใช้งานได้แม้ Offline โดยสมบูรณ์

## Device Identity และ Security

Reader แต่ละเครื่องจะมี Station Identity และ Secret สำหรับ Authentication ของตัวเอง

ตัวอย่าง Station ID:

```text
CP-START
CP-F01
CP-F02
CP-F03
CP-F04
CP-F05
CP-FINISH
```

ข้อมูลที่อัปโหลดต้องมี Authentication/Signature เพื่อป้องกัน Client ที่ไม่ได้รับอนุญาตส่ง Punch ปลอมเข้าสู่ระบบ

Backend ต้องตรวจอย่างน้อย:

- Competition/Event ID
- Device/Station ID
- Event Sequence Number
- Timestamp
- Tag UID
- Message Authentication/Signature
- Duplicate Event ID

## Live Competition Dashboard

เป้าหมาย: `ardf.hs8ac.com`

กรรมการควรสามารถดูข้อมูลต่อไปนี้ได้:

- นักกีฬาที่กำลังอยู่ในสนาม
- Punch ล่าสุด
- ความคืบหน้าผ่าน FOX แต่ละจุด
- เวลา Start
- เวลา Finish
- เวลารวมเบื้องต้น
- FOX ที่ยังขาด
- Punch ซ้ำ
- Punch ที่ Sync ย้อนหลังหลังจาก Offline

ตัวอย่าง:

```text
ATHLETE   START   F1   F2   F3   F4   F5   FINISH
HS8AAA      OK    OK   OK   OK   --   --     --
HS8BBB      OK    OK   --   OK   OK   --     --
```

## Device Health Dashboard

ทุกสถานีควรส่ง Heartbeat เป็นระยะ โดยมีข้อมูลเช่น:

- Online/Offline
- ความแรงสัญญาณ LTE
- แรงดัน/เปอร์เซ็นต์แบตเตอรี่
- สถานะ Local Storage
- สถานะ RTC
- Firmware Version
- จำนวน Punch ที่ยังไม่ได้ Sync
- เวลา Heartbeat ล่าสุด

ตัวอย่าง:

```text
FOX 1   ONLINE    LTE -79 dBm   Battery 87%   Queue 0
FOX 2   ONLINE    LTE -91 dBm   Battery 76%   Queue 0
FOX 3   OFFLINE   Last seen 3m  Battery 63%   Queue unknown
```

## ลำดับการพัฒนา Prototype

1. ทดสอบไฟเลี้ยงและการสื่อสารผ่าน USB
2. อัปโหลด Firmware เข้า ESP32
3. อ่าน NFC Tag
4. อ่าน RTC
5. บันทึก Punch ลง Local Storage
6. ทดสอบ LED/Buzzer
7. เชื่อมต่อ 4G
8. สร้าง Authenticated Punch API
9. ทำ Offline Queue + Retry
10. ทำ Live Web Dashboard
11. ทดสอบภาคสนามจริง
12. ออกแบบ PCB และกล่องใช้งานจริง

## หลักการออกแบบ

โครงการนี้ควรมีคุณสมบัติ:

- ราคาประหยัด
- ทำซ้ำได้
- เป็น Modular
- ซ่อมได้ในท้องถิ่น
- มีเอกสารสำหรับผู้เริ่มต้น
- ไม่ต้องพึ่งอินเทอร์เน็ตตลอดเวลา
- ขยายจากระดับชมรมไปสู่การแข่งขันขนาดใหญ่ได้

เป้าหมายไม่ใช่เพียงทำ IoT Demo แต่ต้องพัฒนาให้สามารถนำไปใช้ในการแข่งขันจริงได้ หลังผ่านการตรวจสอบทางวิศวกรรมและการทดสอบภาคสนามอย่างเพียงพอ

## สถานะปัจจุบัน

**สถานะ: วางแผน Prototype / เตรียมจัดหา Hardware**

Prototype V1 จะเริ่มประกอบบน Breadboard แบบไม่บัดกรีก่อน การบัดกรีถาวร การออกแบบกล่อง และการออกแบบ PCB จะเริ่มหลังวงจรและ Firmware ผ่านการทดสอบแล้วเท่านั้น

## ที่มาและเครดิต

โครงการนี้ริเริ่มในปี 2026 โดย **E25XLD / HS8AC ประเทศไทย** เพื่อพัฒนาแพลตฟอร์ม Electronic Punching สำหรับการแข่งขัน ARDF ที่เข้าถึงได้ ราคาประหยัด และสามารถต่อยอดในประเทศไทยได้

สถาปัตยกรรม การออกแบบ และพัฒนาการของระบบจะถูกบันทึกตั้งแต่ Prototype รุ่นแรก เพื่อให้ที่มาและลำดับวิวัฒนาการของโครงการสามารถตรวจสอบย้อนหลังได้

---

**HS8AC — สมาคมวิทยุสมัครเล่นจังหวัดชุมพร**
