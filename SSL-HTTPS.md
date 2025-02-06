## 🔒 **การใส่ SSL (HTTPS) ใน Express-Gateway Config**
การเปิดใช้งาน **SSL/TLS (HTTPS)** ใน Express-Gateway สามารถทำได้โดยการกำหนดค่าใน `gateway.config.yml` และเพิ่มไฟล์ใบรับรอง SSL (`certificate` และ `private key`)

---

## **1. เตรียมไฟล์ SSL Certificate และ Private Key**
หากมีไฟล์ SSL Certificate (`.crt` หรือ `.pem`) และ Private Key (`.key`) อยู่แล้ว สามารถใช้ได้ทันที  
แต่ถ้ายังไม่มี สามารถสร้าง **Self-Signed Certificate** ได้โดยใช้ OpenSSL:

```sh
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes
```

ไฟล์ที่ได้:
- `key.pem` → **Private Key**
- `cert.pem` → **SSL Certificate**

ให้นำไฟล์นี้ไปวางไว้ในโฟลเดอร์ `config/ssl/` ภายในโปรเจกต์ Express-Gateway

---

## **2. แก้ไข `gateway.config.yml` เพื่อเปิดใช้งาน HTTPS**
เปิดไฟล์ `config/gateway.config.yml` และเพิ่มส่วนของ **HTTPS Configuration**:

```yaml
http:
  port: 8080   # เปิด HTTP ปกติ

https:
  port: 8443   # เปิด HTTPS บนพอร์ต 8443
  tls:
    cert: "./config/ssl/cert.pem"   # ใส่พาธไปยังไฟล์ SSL Certificate
    key: "./config/ssl/key.pem"     # ใส่พาธไปยังไฟล์ Private Key

apiEndpoints:
  api:
    host: '*'
    protocols:
      - https  # เปิดให้รองรับเฉพาะ HTTPS
```

### 🔹 **อธิบาย**
- `https.port: 8443` → ระบุพอร์ตสำหรับ HTTPS (สามารถเปลี่ยนเป็น `443` ได้)
- `tls.cert` → ระบุที่อยู่ของไฟล์ **SSL Certificate**
- `tls.key` → ระบุที่อยู่ของไฟล์ **Private Key**
- `protocols: [https]` → บังคับให้ API ใช้ HTTPS เท่านั้น

---

## **3. รัน Express-Gateway และตรวจสอบ**
### **3.1 รันด้วย Node.js**
```sh
npx express-gateway start
```
ลองทดสอบว่า HTTPS ทำงานหรือไม่:
```sh
curl -k https://localhost:8443
```
> **หมายเหตุ:** ใช้ `-k` เพื่อข้ามการตรวจสอบ SSL ในกรณีที่เป็น Self-Signed Certificate

---

### **3.2 รันด้วย Docker**
หากใช้ Docker ให้ mount ไฟล์ SSL และเปิดพอร์ต HTTPS:

```sh
docker run -d --name express-gateway \
  -p 8080:8080 -p 8443:8443 \
  -v $(pwd)/config:/var/lib/express-gateway/config \
  -v $(pwd)/config/ssl:/etc/ssl \
  express-gateway:1.16.11
```

> ตรวจสอบว่า Docker เปิดพอร์ต **8443** และสามารถเข้าถึงได้

---

## **4. ใช้งาน SSL กับ Docker Compose**
### **4.1 แก้ไข `docker-compose.yml`**
```yaml
version: '3'
services:
  gateway:
    image: express-gateway:1.16.11
    container_name: express-gateway
    ports:
      - "8080:8080"
      - "8443:8443"
    volumes:
      - ./config:/var/lib/express-gateway/config
      - ./config/ssl:/etc/ssl
    restart: always
```

### **4.2 รัน Express-Gateway**
```sh
docker-compose up -d
```

---

## **5. ตรวจสอบ HTTPS**
เปิดเบราว์เซอร์แล้วลองเข้า:
```
https://localhost:8443
```
ถ้าใช้ **Self-Signed Certificate** เบราว์เซอร์อาจแจ้งเตือน **"Not Secure"**  
→ สามารถกด **"Proceed Anyway"** เพื่อเข้าใช้งาน

---

## 🎯 **สรุป**
- ✅ เพิ่ม **SSL Certificate และ Private Key** ใน `config/ssl/`
- ✅ ปรับค่า `gateway.config.yml` ให้รองรับ HTTPS
- ✅ รัน Express-Gateway ด้วย Node.js หรือ Docker
- ✅ ตรวจสอบ HTTPS ผ่าน `curl` หรือเบราว์เซอร์

หากต้องการใช้งานกับ **Let's Encrypt** หรือ CA จริง สามารถใช้ Reverse Proxy อย่าง **NGINX** หรือ **Traefik** ร่วมกับ Docker ได้ 🚀
