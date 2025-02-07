In **Express Gateway**, the **admin port** is used for managing and controlling the gateway using the **Admin API**. This API allows you to dynamically manage configurations, policies, and users without modifying configuration files manually.

---

### **1. Where is the Admin Port Configured?**
The **admin port** is specified in the `system` section of `gateway.config.yml`:

```yaml
system:
  admin:
    port: 9876
```
By default, the admin API runs on **port 9876**, but you can change it as needed.

---

### **2. What is the Admin API Used For?**
You can use the **Admin API** for:
- **Managing users** (create, update, delete, list)
- **Managing API keys, OAuth tokens, and credentials**
- **Modifying pipeline policies dynamically**
- **Viewing gateway health and stats**

---

### **3. How to Use the Admin API**
Once Express Gateway is running, you can interact with the Admin API using tools like **cURL, Postman, or any HTTP client**.

#### **Example: Check Gateway Status**
```sh
curl http://localhost:9876
```

#### **Example: List Registered Users**
```sh
curl http://localhost:9876/users
```

#### **Example: Add a New User**
```sh
curl -X POST http://localhost:9876/users -H "Content-Type: application/json" -d '{
  "username": "admin",
  "email": "admin@example.com",
  "firstname": "Admin",
  "lastname": "User"
}'
```

---

### **4. Disabling the Admin API**
If you don’t need the admin API, you can **disable it** by removing or commenting out the `admin` section in `gateway.config.yml`:

```yaml
system:
  admin:
    # port: 9876  # Comment or remove this line to disable Admin API
```

After making changes, restart the gateway:
```sh
express-gateway restart
```

---

### **5. Securing the Admin API**
Since the Admin API can modify the gateway dynamically, it's important to secure it:
- **Use authentication**: Require an API key or OAuth token for access.
- **Restrict access**: Bind the admin API to a local interface or use firewall rules.
- **Disable it in production** if it's not needed.

---

### **Summary**
- The **admin port** (default: `9876`) allows you to interact with the Express Gateway using the Admin API.
- It helps manage users, API keys, OAuth credentials, and gateway configurations.
- If unused, you should **disable or secure** it to prevent unauthorized access.

Need help with anything specific? 🚀
