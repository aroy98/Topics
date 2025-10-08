# Node.js Mastery Series

## **Part VIII – Production & Deployment**

### **1. Environment Variables Management**

Use environment variables to configure your app securely.

#### **Setup:**

```bash
npm install dotenv
```

#### **.env File:**

```
PORT=3000
DB_URI=mongodb://localhost:27017/appdb
JWT_SECRET=supersecretkey
```

#### **Usage in Node.js:**

```js
import dotenv from 'dotenv';
dotenv.config();

console.log(process.env.PORT);
```

**Best Practices:**

* Never commit `.env` to version control.
* Use `.env.example` for documentation.
* In production, use environment managers (AWS SSM, Docker Secrets, Kubernetes Secrets).

---

### **2. Logging & Monitoring**

Logging and monitoring are critical for maintaining production systems.

#### **Popular Libraries:**

* **Winston** – Versatile logging with transports.
* **Morgan** – HTTP request logger.
* **Pino** – Extremely fast JSON logger.

#### **Example (Winston):**

```js
import winston from 'winston';

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.json()
  ),
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: 'app.log' })
  ]
});

logger.info('Server started successfully');
logger.error('Something went wrong');
```

#### **Monitoring Tools:**

* **PM2 Monitoring Dashboard**
* **Datadog**, **New Relic**, **Grafana**, **Prometheus**

---

### **3. Process Management with PM2**

**PM2** is a process manager for Node.js to ensure apps run continuously.

#### **Installation:**

```bash
npm install pm2 -g
```

#### **Basic Usage:**

```bash
pm2 start app.js --name "my-app"
pm2 list
pm2 logs my-app
pm2 restart my-app
```

#### **Auto-Restart on Server Reboot:**

```bash
pm2 startup
pm2 save
```

#### **Clustering Mode:**

```bash
pm2 start app.js -i max
```

---

### **4. Dockerization of Node.js Apps**

Docker provides isolated environments for consistent deployments.

#### **Dockerfile Example:**

```Dockerfile
FROM node:20-alpine
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install --production
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

#### **Build and Run Container:**

```bash
docker build -t nodejs-app .
docker run -d -p 3000:3000 nodejs-app
```

#### **Docker Compose Example:**

```yaml
version: '3'
services:
  app:
    build: .
    ports:
      - '3000:3000'
    environment:
      - NODE_ENV=production
      - DB_URI=mongodb://mongo:27017/appdb
    depends_on:
      - mongo
  mongo:
    image: mongo:latest
    ports:
      - '27017:27017'
```

---

### **5. Scaling with Kubernetes**

Kubernetes provides orchestration for deploying and scaling containerized apps.

#### **Basic Deployment YAML:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-api-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: node-api
  template:
    metadata:
      labels:
        app: node-api
    spec:
      containers:
        - name: node-api
          image: nodejs-app:latest
          ports:
            - containerPort: 3000
---
apiVersion: v1
kind: Service
metadata:
  name: node-api-service
spec:
  selector:
    app: node-api
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
  type: LoadBalancer
```

#### **Deploy to Cluster:**

```bash
kubectl apply -f deployment.yaml
kubectl get pods
kubectl get services
```

**Scaling Commands:**

```bash
kubectl scale deployment node-api-deployment --replicas=5
```

---

### **6. Security & Best Practices**

#### **1. Security Headers:**

```js
import helmet from 'helmet';
app.use(helmet());
```

#### **2. Input Validation:**

Always validate user input using libraries like `express-validator` or `joi`.

#### **3. Rate Limiting:**

```js
import rateLimit from 'express-rate-limit';
const limiter = rateLimit({ windowMs: 15 * 60 * 1000, max: 100 });
app.use(limiter);
```

#### **4. Prevent NoSQL/SQL Injection:**

* Use **parameterized queries**.
* Sanitize MongoDB queries.

#### **5. Hide Tech Stack:**

```js
app.disable('x-powered-by');
```

#### **6. Dependency Management:**

```bash
npm audit fix
npm outdated
```

#### **7. Run Linter and Static Analysis:**

```bash
npm install eslint --save-dev
npx eslint .
```

---

### **7. CI/CD Deployment Flow Example**

1. Developer pushes code → GitHub Action triggers.
2. Run tests, lint checks, and build.
3. Build Docker image → Push to container registry.
4. Deploy image to Kubernetes or AWS ECS.
5. PM2 keeps Node.js process alive.
6. Monitoring & alerts via Datadog/New Relic.

---

### **8. Backup & Disaster Recovery**

* Automate database backups.
* Use **AWS S3**, **Google Cloud Storage**, or **Azure Blob Storage**.
* Schedule cron jobs for backups.

Example cron job using `node-cron`:

```js
import cron from 'node-cron';
import { exec } from 'child_process';

cron.schedule('0 2 * * *', () => {
  exec('mongodump --db appdb --out /backups/$(date +%F)');
});
```

---

### **9. Observability Stack**

* **Logging** → Winston, Pino
* **Metrics** → Prometheus, Grafana
* **Tracing** → OpenTelemetry

#### **Example: OpenTelemetry Setup**

```js
import opentelemetry from '@opentelemetry/api';
const tracer = opentelemetry.trace.getTracer('node-api');

const span = tracer.startSpan('database_query');
// Perform operation
span.end();
```

---

### **10. Production Checklist**

✅ Use environment variables securely
✅ Log all errors and access requests
✅ Apply rate limiting & security headers
✅ Use HTTPS and valid SSL certificates
✅ Automate deployments (CI/CD)
✅ Monitor performance metrics
✅ Enable clustering and PM2 restarts
✅ Test and version backups

---

## **🎯 Node.js Mastery Series Completed!**

You’ve reached the end of the Node.js Mastery Series covering everything from **fundamentals** to **production deployment**.
Next steps:

* Move to the **TypeScript Mastery Series** or **Python Mastery Series** for backend integration.
* Explore **Advanced Microservices with Node.js & Docker**.
