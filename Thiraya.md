# CLAUDE.md — บันทึกการทำงานหลัก
> อัปเดตล่าสุด: 2026-05-13
> ใช้ร่วมกันได้ทั้ง 2 เครื่อง ผ่าน Git sync

---

## 🗄️ Database Connections (MSSQL)

config อยู่ที่: `~/.config/claude-code/config.json`

### dbprod (Production)
| รายการ | ค่า |
|--------|-----|
| Host | `192.168.0.53` |
| Port | `1433` |
| User | `noi` |
| Database | `master` |
| สถานะ | ✅ Port 1433 เปิดปกติ (block ICMP ping แต่ TCP ผ่านได้) |

### dbdev (Development)
| รายการ | ค่า |
|--------|-----|
| Host | `192.168.0.59` |
| Port | `1433` |
| User | `dNtwG` |
| Database | `master` |
| สถานะ | ✅ Ping ตอบสนองปกติ |

### MCP Server
- ใช้ `mssql-mcp-node` เป็น MCP server สำหรับเชื่อมต่อ MSSQL
- Config อยู่ใน `~/.config/claude-code/config.json`

---

## 🏢 Project Context

### aglove
- **Database**: MSSQL — ใช้ `dbprod` และ `dbdev` (ดูรายละเอียดใน section 🗄️ ด้านบน)

---

## 🔌 API Connections

### Production
- **Base URL**: `https://api-cnt-aglove.724.co.th`

#### Admin Credentials
```json
{
  "Username": "noi",
  "Password": "mZ22hFB@WFmJ2CKu1669"
}
```

#### Authentication — Get Token (Admin)
```bash
curl -X 'POST' \
  'https://api-cnt-aglove.724.co.th/Session/token/get' \
  -H 'accept: */*' \
  -H 'Content-Type: application/json-patch+json' \
  -d '{
  "Username": "noi",
  "Password": "mZ22hFB@WFmJ2CKu1669"
}'
```

#### Authentication — Get Token (Agent)
```bash
curl -X 'POST' \
  'https://api-cnt-aglove.724.co.th/Session/token/get' \
  -H 'accept: */*' \
  -H 'Content-Type: application/json-patch+json' \
  -d '{
  "Username": "AM00371908",
  "Password": "zinqa1-bYhfyw-fibxib@"
}'
```

### Impersonation (สวมสิทธิ์ user อื่น)
- ใช้เมื่อต้องการดูข้อมูลของ user อื่น โดยไม่มี password ของ user นั้น
- ใช้ credentials ของตัวเองและระบุ `Target` เป็น username ที่ต้องการสวมสิทธิ์

```bash
curl -X 'POST' \
  'https://api-iom-core.724.co.th/Session/token/ex/get' \
  -H 'accept: */*' \
  -H 'Content-Type: application/json-patch+json' \
  -d '{
  "Username": "noi",
  "Password": "mZ22hFB@WFmJ2CKu1669",
  "Target": "AM00224240"
}'
```

> `Target` คือ Username ของ user ที่ต้องการสวมสิทธิ์ เปลี่ยนได้ตามต้องการ

### Development
- **Base URL**: `https://api-cnt-aglove.724insure.net`

#### Admin Credentials
```json
{
  "Username": "noi",
  "Password": "mZ22hFB@WFmJ2CKu1669"
}
```

#### Authentication — Get Token (Admin)
```bash
curl -X 'POST' \
  'https://api-cnt-aglove.724insure.net/Session/token/get' \
  -H 'accept: */*' \
  -H 'Content-Type: application/json-patch+json' \
  -d '{
  "Username": "noi",
  "Password": "mZ22hFB@WFmJ2CKu1669"
}'
```

#### Authentication — Get Token (Agent)
```bash
curl -X 'POST' \
  'https://api-cnt-aglove.724insure.net/Session/token/get' \
  -H 'accept: */*' \
  -H 'Content-Type: application/json-patch+json' \
  -d '{
  "Username": "AM00371908",
  "Password": "daXB4pzQjvi8g5j@"
}'
```

---

## 💻 สภาพแวดล้อม

- OS: macOS
- Username: `thirayasiripee`
- ใช้คอมพิวเตอร์ 2 เครื่อง (sync ผ่าน Git)

---

## 📋 สิ่งที่ทำไปแล้ว

### 2026-05-13
- ค้นหา config MSSQL ในเครื่อง → พบใน `~/.config/claude-code/config.json`
- พบ config 2 ชุด: `dbdev` และ `dbprod`
- ทดสอบ connectivity: dbdev ping ผ่าน / dbprod TCP port 1433 ผ่าน
- เริ่มต้น setup CLAUDE.md เพื่อ sync ระหว่าง 2 เครื่อง

---

## 🔄 วิธี Sync ระหว่าง 2 เครื่อง

```bash
# ครั้งแรก — สร้าง git repo (ทำบนเครื่องนี้)
cd ~/.claude
git init
git add CLAUDE.md
git commit -m "init: claude memory"
git remote add origin <YOUR_PRIVATE_REPO_URL>
git push -u origin main

# เครื่องที่ 2 — clone มา
git clone <YOUR_PRIVATE_REPO_URL> ~/.claude-sync
cp ~/.claude-sync/CLAUDE.md ~/.claude/CLAUDE.md

# อัปเดตทุกครั้งที่มีการเปลี่ยนแปลง
cd ~/.claude && git add CLAUDE.md && git commit -m "update memory" && git push
```

> ⚠️ ไฟล์นี้มีข้อมูล credentials — ใช้ **Private Repository** เท่านั้น

---

## ⚙️ พฤติกรรมที่ต้องการ

- **แสดง raw output ทุกครั้ง** หลังเรียก API หรือ query DB ไม่ต้องสรุปอย่างเดียว ให้แสดง response จริงด้วยเสมอ
- endpoint ที่ขึ้นต้นด้วย `/xxx/get` ให้ลอง `POST` ก่อน ถ้า `GET` ได้ 405

---

## 📝 หมายเหตุสำหรับ Claude

- ผู้ใช้ทำงานกับโปรเจกต์ aglove
- ใช้ MSSQL เป็น database หลัก
- ต้องการ context ต่อเนื่องระหว่าง session และระหว่าง 2 เครื่อง
- ถ้าถามเรื่อง DB ให้ใช้ข้อมูลจากไฟล์นี้ได้เลย