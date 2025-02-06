In **Express Gateway**, you can configure multiple domains and proxy them to specific IPs by modifying the `gateway.config.yml` file. Below is a step-by-step guide to set it up.

---

### **1. Update `gateway.config.yml` to Support Multiple Domains**
You need to configure the **http** section to listen on multiple domains and set up proxy rules.

#### **Example Configuration:**
```yaml
http:
  port: 8080
  host: 0.0.0.0

admin:
  port: 9876
  host: 127.0.0.1

apiEndpoints:
  siteA:
    host: sitea.127-1-1-1.nip.io
    paths: ["/*"]
    # paths: ["/api/*"]

  siteB:
    host: siteb.127-1-1-1.nip.io
    paths: ["/api/*"]

  siteC:
    host: sitec.127-1-1-1.nip.io
    paths: ["/api/*"]

serviceEndpoints:
  backendA:
    url: "https://jsonplaceholder.typicode.com/todos"

  backendB:
    url: "http://192.168.1.110:8080"

  backendC:
    url: "http://192.168.1.110"

policies:
  - proxy
  - cors
  - log
  # - basic-auth
  # - expression
  # - key-auth
  # - oauth2
  - rate-limit

pipelines:
  siteA_pipeline:
    apiEndpoints:
      - siteA
    policies:
      - rate-limit:
          - action:
              # Limit to 1 per 10 seconds based on the hostname
              max: 4
              windowMs: 1000
              rateLimitBy: "${req.hostname}"
          # - action:
          #     max: 8  # Allow only 8 requests
          #     window: 1000  # Time window in milliseconds (1 second)
          #     key: ip  # Rate limit based on IP address
          #     remainingAttempts: true
          #     rejectWithHTTPCode: 429  # Return 429 Too Many Requests when limit is exceeded

      - proxy:
          - action:
              serviceEndpoint: backendA
              changeOrigin: true

  siteB_pipeline:
    apiEndpoints:
      - siteB
    policies:
      - proxy:
          - action:
              serviceEndpoint: backendB
              changeOrigin: true

  siteC_pipeline:
    apiEndpoints:
      - siteC
    policies:
      - proxy:
          - action:
              serviceEndpoint: backendC
              changeOrigin: true

```

---

### **2. Explanation of Configuration**
- **apiEndpoints**:
  - Defines multiple domain endpoints (`siteA.com`, `siteB.com`, `siteC.com`).
  - Each domain is mapped to `"/api/*"` (can be changed as needed).
- **serviceEndpoints**:
  - Maps domains to their backend IPs (`backendA`, `backendB`, `backendC`).
- **pipelines**:
  - Defines the rules for each domain.
  - Uses the `proxy` policy to forward requests to the corresponding backend IP.

---

### **3. Running the Gateway**
Restart Express Gateway to apply the configuration:

```bash
eg gateway stop
eg gateway start
```

---

### **4. Configure Your DNS or Hosts File**
Ensure that `siteA.com`, `siteB.com`, and `siteC.com` resolve to your Express Gateway server.

If testing locally, edit your `/etc/hosts` file (Linux/Mac) or `C:\Windows\System32\drivers\etc\hosts` (Windows):

```
127.0.0.1 siteA.com
127.0.0.1 siteB.com
127.0.0.1 siteC.com
```

---

### **5. Testing**
Run:

```bash
curl -H "Host: siteA.com" http://localhost:8080/api/test
```

It should forward the request to `http://192.168.1.100:3000/api/test`.

---

### **Conclusion**
- You have successfully set up **Express Gateway** to proxy multiple domains to different backend IPs.
- You can modify this setup for SSL, rate limiting, authentication, etc.

Would you like help setting up HTTPS or more advanced configurations? 🚀
