## การใช้งาน **Docker Express-Gateway 1.16.11**

`Express-Gateway` เป็น API Gateway แบบโอเพนซอร์ซที่ใช้ Node.js และ Express ซึ่งช่วยให้เราสามารถสร้าง, จัดการ และควบคุม API ต่างๆ ได้อย่างง่ายดาย

ในคู่มือนี้ เราจะครอบคลุมถึง:
1. ติดตั้ง Docker และ Express-Gateway
2. สร้างและกำหนดค่า Express-Gateway
3. ทดสอบ API Gateway
4. ใช้งาน Docker Compose สำหรับ Express-Gateway

---

## **1. ติดตั้ง Docker และ Express-Gateway**

### **1.1 ติดตั้ง Docker**
หากยังไม่ได้ติดตั้ง Docker สามารถติดตั้งได้ตาม OS ของคุณ:

- **Windows / macOS:** ดาวน์โหลดจาก [Docker Official Site](https://www.docker.com/products/docker-desktop/)
- **Linux (Ubuntu/Debian):** ติดตั้งด้วยคำสั่ง:
  ```sh
  sudo apt update
  sudo apt install docker.io -y
  sudo systemctl enable --now docker
  ```

ตรวจสอบว่า Docker ทำงานอยู่:
```sh
docker --version
```
ควรแสดงเวอร์ชันของ Docker ที่ติดตั้ง

---

### **1.2 ดึง Express-Gateway 1.16.11 จาก Docker Hub**
ใช้คำสั่งนี้เพื่อติดตั้ง `express-gateway:1.16.11`
```sh
docker pull express-gateway:1.16.11
```
หรือใช้ `run` เพื่อเรียกใช้งานทันที:
```sh
docker run -d --name express-gateway -p 8080:8080 express-gateway:1.16.11
```

---

## **2. สร้างและกำหนดค่า Express-Gateway**
Express-Gateway ต้องการไฟล์ config และ project structure ดังนี้:

### **2.1 สร้างโปรเจกต์ Express-Gateway**
ใช้คำสั่งนี้เพื่อสร้างโปรเจกต์ Gateway:
```sh
npx express-gateway create my-gateway
```
จากนั้นเข้าไปในโฟลเดอร์:
```sh
cd my-gateway
```

โครงสร้างของโปรเจกต์ที่ได้:
```
my-gateway
│── config
│   ├── gateway.config.yml
│   ├── models
│   │   ├── applications.json
│   │   ├── credentials.json
│   │   ├── tokens.json
│   │   ├── users.json
│── package.json
│── server.js
```

---

### **2.2 แก้ไขไฟล์ `gateway.config.yml`**
เปิดไฟล์ `config/gateway.config.yml` และเพิ่ม policy หรือ route ที่ต้องการ เช่น:

```yaml
http:
  port: 8080

admin:
  port: 9876
  hostname: localhost

apiEndpoints:
  api:
    host: '*'

serviceEndpoints:
  example_service:
    url: 'http://example.com'

policies:
  - cors
  - proxy
  - log

pipelines:
  default:
    apiEndpoints:
      - api
    policies:
      - proxy:
          - action:
              serviceEndpoint: example_service
```
ค่า `proxy` จะกำหนดให้ API Gateway ส่ง request ไปยัง `http://example.com`

---

## **3. ทดสอบ API Gateway**
### **3.1 รัน Express-Gateway**
เมื่อแก้ไข config เสร็จแล้ว ให้รัน Gateway ด้วย:
```sh
npx express-gateway start
```
หรือถ้าใช้ Docker:
```sh
docker run -d --name my-gateway -p 8080:8080 -v $(pwd)/config:/var/lib/express-gateway/config express-gateway:1.16.11
```

---

### **3.2 ทดสอบ API Gateway**
ลองใช้ `curl` หรือ Postman เพื่อทดสอบ API:
```sh
curl http://localhost:8080
```

ถ้าการตั้งค่าถูกต้อง API Gateway จะทำงานและตอบกลับข้อมูลจาก service ที่ตั้งค่าไว้

---

## **4. ใช้งาน Docker Compose สำหรับ Express-Gateway**
แทนที่จะรัน Express-Gateway ด้วยคำสั่ง `docker run` เราสามารถใช้ `docker-compose` ได้

### **4.1 สร้างไฟล์ `docker-compose.yml`**
สร้างไฟล์ `docker-compose.yml` และเพิ่มการตั้งค่าดังนี้:

```yaml
version: '3'
services:
  gateway:
    image: express-gateway:1.16.11
    container_name: express-gateway
    ports:
      - "8080:8080"
    volumes:
      - ./config:/var/lib/express-gateway/config
    restart: always
```

### **4.2 รัน Express-Gateway ด้วย Docker Compose**
```sh
docker-compose up -d
```

ตรวจสอบว่า container ทำงาน:
```sh
docker ps
```

ถ้าต้องการดู log:
```sh
docker logs -f express-gateway
```

---

## **สรุป**
1. ดึง Docker image `express-gateway:1.16.11`
2. สร้างและตั้งค่า Express-Gateway
3. กำหนด API Gateway ให้รับ request และส่งต่อไปยัง backend services
4. ทดสอบ API Gateway โดยใช้ `curl` หรือ Postman
5. ใช้ `docker-compose` เพื่อจัดการ Express-Gateway ได้ง่ายขึ้น

หากมีคำถามเพิ่มเติม แจ้งมาได้เลย! 🚀
