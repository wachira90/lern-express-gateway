In an **Express Gateway** (EG) project, you can load environment variables from a `.env` file or your shell environment using **dotenv** or native Node.js features.

### 1. **Using `dotenv` package**
You can use the `dotenv` package to load environment variables from a `.env` file into your Express Gateway configuration.

#### **Steps:**
1. **Install `dotenv` package**:
   ```sh
   npm install dotenv
   ```

2. **Create a `.env` file** in the root of your Express Gateway project:
   ```
   PORT=8080
   NODE_ENV=development
   API_KEY=your_api_key_here
   ```

3. **Modify your `server.js` (or main entry file like `index.js`)**:
   ```js
   require('dotenv').config();

   console.log("PORT:", process.env.PORT);
   console.log("NODE_ENV:", process.env.NODE_ENV);
   ```

### 2. **Using Native Shell Environment Variables**
If you're running your **Express Gateway** without `dotenv`, you can directly set environment variables before running the service:

#### **For Linux/macOS (Bash/Zsh)**
```sh
export PORT=8080
export NODE_ENV=production
express-gateway start
```

#### **For Windows (Command Prompt)**
```cmd
set PORT=8080
set NODE_ENV=production
express-gateway start
```

#### **For Windows (PowerShell)**
```powershell
$env:PORT="8080"
$env:NODE_ENV="production"
express-gateway start
```

### 3. **Accessing Environment Variables in `gateway.config.yml`**
Express Gateway allows environment variables inside the configuration file:

Modify `gateway.config.yml`:
```yaml
http:
  port: ${PORT}
```

Make sure you run your application with environment variables set.

---

### **Which method should you use?**
- If you want flexibility, **use `dotenv`** so you can manage environment variables through a `.env` file.
- If you're using **Docker or a cloud platform (Heroku, AWS, etc.)**, environment variables should be set in the runtime environment.
- If you're working in a local development environment, exporting variables in the shell is fine.

Let me know if you need help with a specific setup! 🚀
