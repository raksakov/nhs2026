# 🎧 หนูได้ยิน — สรุปโปรแกรมและฟีเจอร์
### ระบบบันทึกการคัดกรองการได้ยินทารกแรกเกิด (NHS Hearing Screening)
> สำหรับจัดทำคู่มือและ Slide อบรมผู้ใช้งาน

---

## 1. ภาพรวมระบบ

| รายการ | รายละเอียด |
|---|---|
| Platform | Google Apps Script (GAS) — ทำงานบน Browser / Mobile |
| ฐานข้อมูล | Google Sheets |
| การเข้าถึง | URL คงที่ (Managed Deployment) |
| รองรับ | Mobile Phone · Desktop · Tablet |
| ภาษา | ไทย (Thai UI) |

---

## 2. โครงสร้างหน้าจอ (Pages)

```
หน้าเข้าสู่ระบบ (Login / Register)
    └── Dashboard (หน้าหลัก)
            ├── ค้นหาผู้ป่วย → โปรไฟล์ผู้ป่วย
            ├── เพิ่มผู้ป่วยใหม่
            ├── Popup: จำนวนผู้ป่วยทั้งหมด (Chart)
            ├── Popup: จำนวน Refer (Chart)
            └── Popup: สัดส่วนกิจกรรม (Chart)
```

---

## 3. ระบบผู้ใช้งาน (User Management)

### 3.1 สมัครสมาชิก (Register)
กรอกข้อมูล: ชื่อผู้ใช้ · รหัสผ่าน · อีเมล · ชื่อ-นามสกุล · CID · โรงพยาบาล · หน่วยบริการ

### 3.2 เข้าสู่ระบบ (Login)
- ยืนยัน username / password (SHA-256 hash)
- ระบบจำ session อัตโนมัติ

### 3.3 โปรไฟล์ผู้ใช้
แก้ไขได้: ชื่อ · CID · โรงพยาบาล · หน่วยบริการ · ออกจากระบบ

### 3.4 ความปลอดภัย
- รหัสผ่านเข้ารหัส SHA-256
- Session token UUID
- ตรวจสอบ session ทุก 5 นาที

---

## 4. Dashboard — หน้าหลัก

### 4.1 Stat Cards (3 ปุ่มคลิกได้)

| ปุ่ม | แสดงผล | เมื่อคลิก |
|---|---|---|
| 🔵 จำนวนผู้ป่วยทั้งหมด | Distinct CID ทั้งหมด | Popup: Bar Chart by โรงพยาบาลที่คลอด |
| 🔴 จำนวน Refer (Distinct) | CID ที่มีผล Refer | Popup: Chart หน่วยบริการ + สถานะ + ตาราง |
| 🟢 กิจกรรมทั้งหมด | จำนวน record ทั้งหมด | Popup: Pie/Bar Chart สัดส่วนกิจกรรม |

### 4.2 Quick Actions
- 🔍 ค้นหาผู้ป่วย
- ➕ เพิ่มผู้ป่วยใหม่

### 4.3 รายการล่าสุด
แสดง record ย้อนหลัง 1 เดือน พร้อมกรองตาม Filter โรงพยาบาล

---

## 5. Popup Charts (Drill-down Analytics)

### 5.1 จำนวนผู้ป่วยทั้งหมด
- **Chart**: Bar — นับ Distinct CID แยกตามโรงพยาบาลที่คลอด
- **คลิก Bar** → กรองตาราง: ชื่อ · CID · ประเภทตรวจ · ผล Rt · ผล Lt · วันที่
- คลิกแถวในตาราง → เปิดโปรไฟล์ผู้ป่วย

### 5.2 จำนวน Refer
- **Chart 1**: Bar — แยกตามหน่วยบริการ
- **Chart 2**: Bar — แยกตามสถานะ
- **คลิก Bar** → กรองตาราง Refer: หน่วยบริการ · CID · ชื่อ · โรงพยาบาล · วันที่ Refer · ระยะเวลา · สถานะ · เบอร์โทร
- Badge สีแสดงความเร่งด่วน (🟢 ≤3 วัน / 🟡 3+ วัน / 🔴 เกินกำหนด)

### 5.3 สัดส่วนกิจกรรม
- **Chart**: แสดงสัดส่วนประเภทกิจกรรมทั้งหมด

---

## 6. ค้นหาผู้ป่วย (Search)

- ค้นหาด้วย **เลขบัตรประชาชน** หรือ **ชื่อผู้ป่วย**
- กด Enter หรือปุ่มค้นหา
- แสดงผลเป็น Card list → คลิกเพื่อดู Profile

---

## 7. โปรไฟล์ผู้ป่วย (Patient Profile)

### ข้อมูลพื้นฐาน
| Field | รายละเอียด |
|---|---|
| เลขบัตรประชาชน | 13 หลัก (แสดงในรูปแบบ x-xxxx-xxxxx-xx-x) |
| ชื่อ-นามสกุล | — |
| วันเกิด | (แสดงเป็น วัน/เดือน/ปี พ.ศ.) |
| ชื่อมารดา | — |
| โรงพยาบาลที่คลอด | — |
| เบอร์โทร | ปุ่มโทรออกได้โดยตรง 📞 |

### Timeline ประวัติการตรวจ
แสดงทุก record เรียงตามวันที่ พร้อม:
- ประเภทกิจกรรม · ผลตรวจ Rt/Lt (icon ✓/✕) · ผู้ตรวจ · สถานะ · การจัดการเพิ่มเติม

### Actions
- ➕ เพิ่มกิจกรรม (Drawer)
- ✏️ แก้ไขข้อมูลผู้ป่วย

---

## 8. เพิ่มผู้ป่วยใหม่ (New Patient)

### ส่วนที่ 1: ข้อมูลผู้ป่วย
| Field | หมายเหตุ |
|---|---|
| เลขบัตรประชาชน | ตรวจ 13 หลัก + validate realtime |
| ชื่อ-นามสกุล | — |
| วันเกิด | Date picker + แสดงวันไทย |
| ชื่อมารดา | — |
| โรงพยาบาลที่คลอด | Dropdown (จาก Sheet) + ระบุเองได้ |
| เบอร์โทร | — |

### ส่วนที่ 2: ข้อมูลกิจกรรม
*(เหมือนกับฟอร์ม "เพิ่มกิจกรรม" ด้านล่าง)*

---

## 9. เพิ่มกิจกรรม (Activity Form — Drawer)

> ใช้ได้ทั้งในหน้า **New Patient** และ **Profile** (Slide-in Drawer)

### Fields
| Field | ตัวเลือก / หมายเหตุ |
|---|---|
| หน่วยบริการ | Dropdown (จาก Sheet) + ระบุเองได้ |
| รายละเอียดกิจกรรมวันนี้ | Free text |
| **ประเภทกิจกรรม** | OAE / AABR / Diag ABR/ASSR / Follow-up |
| วันที่ตรวจ | Date picker + แสดงวันไทย |
| ผลหูขวา (Rt) | *ดูตาราง options ด้านล่าง* |
| ผลหูซ้าย (Lt) | *ดูตาราง options ด้านล่าง* |
| ผู้ตรวจ | auto-fill จาก profile ผู้ใช้ |
| สถานะ | ปกติ / ติดตาม / ส่งต่อ |
| การจัดการเพิ่มเติม | Free text |

### Options ผลหู — แยกตามประเภทกิจกรรม

| ประเภทตรวจ | Options ผลหู Rt / Lt |
|---|---|
| **OAE** | Pass · Refer · Not done |
| **AABR** | Pass · Refer · Not done |
| **Diag ABR/ASSR** | Pass (≤35 dB HL) · Refer (>35 dB HL) · 40/50/60/70/80/90 dB HL · No response · Not done |
| **Follow-up** | *(ไม่แสดง ear section)* |

> ⚡ Options เปลี่ยนอัตโนมัติเมื่อเลือกประเภทกิจกรรม

### Conditional Fields
- เลือก **ติดตาม** → ขยายส่วน: ช่องทางติดตาม · วันนัดติดตาม · รายละเอียด
- เลือก **ส่งต่อ** → ขยายส่วน: ส่งต่อไปที่ · เหตุผลส่งต่อ

---

## 10. ข้อมูลที่บันทึกลง Google Sheets

ทุก record บันทึก 21 columns:

| # | Column | คำอธิบาย |
|---|---|---|
| 1 | Timestamp | วันเวลาบันทึก (auto) |
| 2 | Date of Birth | วันเกิดทารก |
| 3 | Mother Name | ชื่อมารดา |
| 4 | Birth Hospital | โรงพยาบาลที่คลอด |
| 5 | Child CID | เลขบัตรประชาชน 13 หลัก |
| 6 | Phone | เบอร์โทรติดต่อ |
| 7 | Patient Name | ชื่อทารก |
| 8 | Service Unit | หน่วยบริการที่ตรวจ |
| 9 | Today Activity | รายละเอียดกิจกรรม |
| 10 | Activity Type | OAE / AABR / Diag ABR/ASSR / Follow-up |
| 11 | Activity Date | วันที่ตรวจ |
| 12 | Rt Ear | ผลหูขวา |
| 13 | Lt Ear | ผลหูซ้าย |
| 14 | Examiner | ผู้ตรวจ |
| 15 | Additional Management | การจัดการเพิ่มเติม |
| 16 | Followup Channel | ช่องทางติดตาม |
| 17 | Followup Date | วันนัดติดตาม |
| 18 | Status | ปกติ / ติดตาม / ส่งต่อ |
| 19 | Reason Details | รายละเอียดเหตุผล |
| 20 | Referral To | ส่งต่อไปที่ |
| 21 | Referral Reason | เหตุผลส่งต่อ |

---

## 11. UX / UI Features

| Feature | รายละเอียด |
|---|---|
| Dark Theme | พื้นหลังสีเข้ม เหมาะสำหรับใช้ในห้องมืด/ห้องตรวจ |
| Responsive | ปรับขนาดอัตโนมัติทั้ง Mobile และ Desktop |
| Toast Notification | แจ้งเตือน success/error/warning มุมขวาบน |
| Spinner Overlay | แสดงสถานะ loading ทุกการ request |
| Date แสดงแบบไทย | วัน/เดือน/ปี พ.ศ. อ่านง่าย |
| CID Format | แสดง x-xxxx-xxxxx-xx-x |
| Validate CID | ตรวจ 13 หลัก real-time ขณะพิมพ์ |
| Auto-fill | ผู้ตรวจ + หน่วยบริการ จาก profile ผู้ใช้ |
| Badge สี | 🟢 Pass / 🔴 Refer / 🟡 dB HL |
| โทรออกได้ทันที | ปุ่ม 📞 โทรหาผู้ปกครองจากโปรไฟล์ |
| Logo Vector | SVG คมชัดทุกขนาด ไม่แตกบน Retina |
| Session Check | ตรวจสอบ login อัตโนมัติทุก 5 นาที |

---

## 12. Backend (Google Apps Script)

| Function | หน้าที่ |
|---|---|
| `getCurrentUser()` | ตรวจสอบ session ปัจจุบัน |
| `registerUser()` | สมัครสมาชิก + hash password |
| `getHospitals()` | ดึงรายชื่อโรงพยาบาล |
| `getDashboardData()` | สถิติ dashboard ทั้งหมด |
| `getPatientsByHospital()` | รายชื่อผู้ป่วยตามโรงพยาบาล + ear results |
| `searchByCID()` | ค้นหาผู้ป่วยด้วย CID หรือชื่อ |
| `updatePatientInfo()` | แก้ไขข้อมูลพื้นฐานผู้ป่วย |
| `saveRecord()` | บันทึก record ใหม่ลง Sheet |

---

## 13. Flow การใช้งานหลัก

```
[Login] → [Dashboard]
    │
    ├── ผู้ป่วยใหม่ → กรอก NP Form → บันทึก → กลับ Dashboard
    │
    ├── ค้นหาผู้ป่วยเก่า → Profile → เพิ่มกิจกรรม (Drawer) → บันทึก
    │
    └── ดูสถิติ → คลิก Stat Card → Popup Chart
                    └── คลิก Bar → ตารางผู้ป่วย → คลิกแถว → Profile
```

---

*เวอร์ชัน: เมษายน 2568 | พัฒนาบน Google Apps Script + Google Sheets*
