# Fullstack Webapp (Docker)

## ภาพรวม

โปรเจกต์นี้ประกอบด้วย:
- Frontend: Next.js
- Backend: NestJS
- Database: PostgreSQL
- Orchestration: Docker Compose

## 1) Requirements

ติดตั้งเครื่องมือเหล่านี้ก่อน:
- Docker Desktop (แนะนำเวอร์ชันล่าสุด)
- Git

หมายเหตุสำหรับ Windows:
- เปิด File Sharing ให้ไดรฟ์ที่เก็บโปรเจกต์ (เช่น `C:`) ใน Docker Desktop

## 2) เตรียม Environment Variables

สร้างไฟล์ `.env` ที่ root ของโปรเจกต์ แล้วใส่ค่า:

```env
# PostgreSQL
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_DB=postgres_db

# Backend
POSTGRES_HOST=database
POSTGRES_PORT=5432

# App Ports
BACKEND_PORT=3003
FRONTEND_PORT=3004
```

หมายเหตุ:
- `BACKEND_PORT` คือพอร์ตที่เปิดบนเครื่อง host
- ใน container backend ใช้พอร์ต 3000
- `FRONTEND_PORT` คือพอร์ตที่เปิดบนเครื่อง host
- ใน container frontend ใช้พอร์ต 4000

## 3) วิธีรันโปรเจกต์

ที่ root ของโปรเจกต์ รัน:

```bash
docker compose up -d --build
```

ตรวจสอบสถานะ:

```bash
docker compose ps
```

ดู log:

```bash
docker compose logs -f backend
docker compose logs -f frontend
docker compose logs -f database
```

## 4) URL ที่ใช้งาน

- Frontend: `http://localhost:${FRONTEND_PORT}`
  - จากค่า default จะเป็น `http://localhost:3004`
- Backend: `http://localhost:${BACKEND_PORT}`
  - จากค่า default จะเป็น `http://localhost:3003`
- Database: `localhost:5432`

ตัวอย่างทดสอบ backend:

```bash
curl http://localhost:3003/
```

## 5) การพัฒนา (Hot Reload)

โปรเจกต์นี้ถูกตั้งค่าให้ hot reload ใน Docker แล้ว:
- Backend ใช้ `nest start --watch`
- Frontend ใช้ `next dev --webpack`
- เปิด polling watcher สำหรับ environment บน Docker/Windows แล้ว

หากแก้โค้ดแล้วไม่รีโหลด ให้ดูหัวข้อ Troubleshooting ด้านล่าง

## 6) คำสั่งที่ใช้บ่อย

หยุด service:

```bash
docker compose stop
```

หยุดและลบ container/network:

```bash
docker compose down
```

ลบรวม volume (ข้อมูล database จะหาย):

```bash
docker compose down -v
```

build ใหม่เฉพาะ frontend/backend:

```bash
docker compose up -d --build frontend backend
```

เข้า shell ใน container:

```bash
docker compose exec backend sh
docker compose exec frontend sh
```

## 7) Troubleshooting

### 7.1 แก้โค้ดแล้ว hot reload ไม่ทำงาน

ลองตามลำดับนี้:
1. เช็กว่า container ยังรันอยู่

```bash
docker compose ps
```

2. ดู log ว่ามีการจับ file change หรือไม่

```bash
docker compose logs -f backend
docker compose logs -f frontend
```

3. Recreate service พร้อม build ใหม่

```bash
docker compose up -d --build backend frontend
```

4. บน Windows ให้ยืนยันว่า Docker Desktop เปิด File Sharing ให้ไดรฟ์ที่เก็บโปรเจกต์

### 7.2 เข้า frontend/backend ไม่ได้

ตรวจค่า `.env` โดยเฉพาะ:
- `BACKEND_PORT`
- `FRONTEND_PORT`

แล้วเช็กว่าไม่มีโปรแกรมอื่นใช้พอร์ตซ้ำ

### 7.3 Database เริ่มไม่ขึ้น

ดู log database:

```bash
docker compose logs -f database
```

ถ้าต้องการ reset ข้อมูลทั้งหมด:

```bash
docker compose down -v
docker compose up -d --build
```

## 8) โครงสร้างโดยย่อ

```text
backend/     NestJS API
frontend/    Next.js app
docker-compose.yml
README.md
README.en.md
README.th.md
```
