# Node.js Mastery Series

## **Part III – Building APIs**

### **1. Setting Up an Express Server**

* Express is the most widely used Node.js web framework.

```js
import express from 'express';
const app = express();
const PORT = process.env.PORT || 3000;

app.get('/', (req, res) => {
  res.send('Hello, Node.js API World!');
});

app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

---

### **2. Express Middleware System**

* Middleware functions execute **before the route handler**.
* They can modify `req` and `res`, or terminate the request.

```js
app.use(express.json()); // Parses JSON request bodies

app.use((req, res, next) => {
  console.log(`${req.method} ${req.url}`);
  next(); // Continue to next middleware
});
```

**Types of Middleware:**

* Application-level
* Router-level
* Built-in (`express.json`, `express.static`)
* Third-party (e.g. `cors`, `helmet`)

---

### **3. Routing System**

* Define multiple endpoints for CRUD operations.

```js
const users = [
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' }
];

app.get('/users', (req, res) => res.json(users));

app.get('/users/:id', (req, res) => {
  const user = users.find(u => u.id === parseInt(req.params.id));
  user ? res.json(user) : res.status(404).send('User not found');
});

app.post('/users', (req, res) => {
  const newUser = { id: users.length + 1, ...req.body };
  users.push(newUser);
  res.status(201).json(newUser);
});
```

---

### **4. RESTful API Design Principles**

| Method | Purpose                 | Example Endpoint |
| ------ | ----------------------- | ---------------- |
| GET    | Retrieve resource       | `/api/users`     |
| POST   | Create new resource     | `/api/users`     |
| PUT    | Update entire resource  | `/api/users/:id` |
| PATCH  | Update partial resource | `/api/users/:id` |
| DELETE | Delete resource         | `/api/users/:id` |

**Guidelines:**

* Use **nouns** for resources.
* Implement **status codes** properly (200, 201, 400, 404, 500).
* Return **JSON** responses.
* Maintain versioning (e.g., `/api/v1/users`).

---

### **5. Request Validation**

Use `express-validator` to validate and sanitize input.

```js
import { body, validationResult } from 'express-validator';

app.post(
  '/register',
  [
    body('email').isEmail(),
    body('password').isLength({ min: 6 })
  ],
  (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }
    res.send('User registered successfully!');
  }
);
```

---

### **6. Error Handling Middleware**

Centralized error handling improves consistency.

```js
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({ message: 'Internal Server Error' });
});
```

---

### **7. Organizing Routes and Controllers**

* Use **Router()** for modular route structure.

**routes/userRoutes.js:**

```js
import express from 'express';
import { getUsers, createUser } from '../controllers/userController.js';

const router = express.Router();
router.get('/', getUsers);
router.post('/', createUser);
export default router;
```

**controllers/userController.js:**

```js
export const getUsers = (req, res) => res.json(['Alice', 'Bob']);
export const createUser = (req, res) => res.status(201).send('User Created');
```

**server.js:**

```js
import userRoutes from './routes/userRoutes.js';
app.use('/api/users', userRoutes);
```

---

### **8. Handling File Uploads (Multer)**

Use **Multer** for file uploads in APIs.

```js
import multer from 'multer';
const upload = multer({ dest: 'uploads/' });

app.post('/upload', upload.single('file'), (req, res) => {
  res.send(`File uploaded: ${req.file.filename}`);
});
```

---

### **9. Response Formatting & Pagination**

```js
app.get('/posts', (req, res) => {
  const page = parseInt(req.query.page) || 1;
  const limit = 10;
  const offset = (page - 1) * limit;
  const paginated = posts.slice(offset, offset + limit);
  res.json({ page, results: paginated });
});
```

---

### **10. Implementing CORS and Security Headers**

```js
import cors from 'cors';
import helmet from 'helmet';

app.use(cors()); // Allow cross-origin requests
app.use(helmet()); // Secure HTTP headers
```

---

### **11. Versioning and API Documentation**

* Maintain clear versioning: `/api/v1/`, `/api/v2/`
* Use **Swagger** for documentation.

```bash
npm install swagger-ui-express swagger-jsdoc
```

**swagger.js:**

```js
import swaggerUi from 'swagger-ui-express';
import swaggerJsdoc from 'swagger-jsdoc';

const specs = swaggerJsdoc({
  definition: {
    openapi: '3.0.0',
    info: { title: 'Node.js API', version: '1.0.0' },
  },
  apis: ['./routes/*.js'],
});

app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(specs));
```

---

### **12. Example Project Structure**

```
node-api/
│
├── controllers/
│   └── userController.js
├── routes/
│   └── userRoutes.js
├── middlewares/
│   └── errorHandler.js
├── models/
│   └── userModel.js
├── server.js
└── package.json
```

---

## **Next Up: Part IV – Databases**
