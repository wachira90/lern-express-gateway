### การเพิ่มความปลอดภัยให้กับ Express-Gateway ในไฟล์ `gateway.config.yml`

ในการเพิ่มความปลอดภัยให้กับ **Express-Gateway** คุณสามารถใช้ **การยืนยันตัวตน (Authentication)** และ **การกำหนดสิทธิ์ (Authorization)** เช่น **API Key**, **JWT (JSON Web Token)** และ **OAuth2** เพื่อป้องกันการเข้าถึงที่ไม่พึงประสงค์

---

## 🔹 วิธีเพิ่มความปลอดภัยใน `gateway.config.yml`

1. **เปิดใช้งานนโยบายการรักษาความปลอดภัย** (API Key, JWT ฯลฯ)
2. **กำหนด Pipeline สำหรับการควบคุมการเข้าถึง**
3. **เพิ่มนโยบายความปลอดภัยใน Pipeline**
4. **กำหนดให้เฉพาะผู้ที่ได้รับอนุญาตเท่านั้นที่สามารถเข้าถึงบาง Endpoint ได้**

---

### 🔹 ตัวอย่างไฟล์ `gateway.config.yml`

```yaml
http:
  port: 8080

admin:
  port: 9876
  hostname: localhost

apiEndpoints:
  secure-api:
    host: "*"
    paths:
      - "/secure/*"

serviceEndpoints:
  backend-service:
    url: "http://backend-service:3000"

policies:
  - jwt
  - key-auth
  - proxy

pipelines:
  secure-pipeline:
    apiEndpoints:
      - secure-api
    policies:
      - jwt:
          - action:
              secretOrPublicKey: "your-secret-key"
              checkCredentialExistence: true
              credentialsKey: "jwt"
      - key-auth:
          - action:
              apiKeyHeader: "x-api-key"
              apiKeyQueryParam: "api_key"
              allowMultiple: false
      - proxy:
          - action:
              serviceEndpoint: backend-service
```

---

## 🔹 คำอธิบายนโยบายความปลอดภัย

1. **JWT Authentication (`jwt`)**
   - กำหนดให้ต้องมี **JWT Token** ในคำขอ (Request)
   - ใช้ `secretOrPublicKey` ในการตรวจสอบความถูกต้องของ Token
   - ตรวจสอบว่ามี Credential ในคำขอหรือไม่

2. **API Key Authentication (`key-auth`)**
   - กำหนดให้ผู้ใช้ต้องส่ง **API Key** ใน Header (`x-api-key`) หรือ Query Parameter (`api_key`)
   - เฉพาะผู้ที่มี API Key ที่ถูกต้องเท่านั้นที่สามารถเข้าถึง Endpoint ได้

3. **Proxy Policy (`proxy`)**
   - ใช้สำหรับส่งคำขอไปยัง **Backend Service** หลังจากผ่านการตรวจสอบสิทธิ์แล้ว

---

## 🔹 วิธีเพิ่มการป้องกันเพิ่มเติม

### ✅ **ป้องกันการโจมตีด้วย Rate Limiting (จำกัดจำนวนคำขอ)**
```yaml
- rate-limit:
    - action:
        max: 100
        window: 60
        key: "{{req.ip}}"
```
📌 **อธิบาย:** จำกัดจำนวนคำขอที่ **100 ครั้งต่อนาที** ต่อ IP เพื่อป้องกันการใช้งานเกินควร (เช่น DDoS)

---

### ✅ **ป้องกัน CORS (Cross-Origin Resource Sharing)**
```yaml
- cors:
    - action:
        origin: "*"
        methods: "GET,POST,PUT,DELETE"
```
📌 **อธิบาย:** กำหนดให้รองรับการเข้าถึงจากทุกโดเมน (หรือสามารถจำกัดเฉพาะโดเมนที่ต้องการได้)

---

## 🔹 วิธีทดสอบการรักษาความปลอดภัย

1️⃣ **ทดสอบ API Key Authentication**
   ```sh
   curl -H "x-api-key: YOUR_API_KEY" http://localhost:8080/secure/data
   ```

2️⃣ **ทดสอบ JWT Authentication**
   ```sh
   curl -H "Authorization: Bearer YOUR_JWT_TOKEN" http://localhost:8080/secure/data
   ```

---

## 🔹 สรุป
✅ ใช้ **JWT และ API Key Authentication** เพื่อควบคุมการเข้าถึง  
✅ ใช้ **Rate Limiting** ป้องกันการโจมตีแบบ DDoS  
✅ ใช้ **CORS Protection** เพื่อควบคุมการเข้าถึงจากแหล่งที่มาอื่น ๆ  

---

🔹 **ต้องการให้ช่วยสร้าง API Key หรือ JWT Token สำหรับทดสอบไหม?** 🚀
