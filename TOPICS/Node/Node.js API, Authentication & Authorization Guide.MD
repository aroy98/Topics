# Node.js API, Authentication & Authorization – Explained with Examples

## **1. HTTP Methods Overview**

HTTP methods define what action should be performed on a given resource in your API.

### **GET – Retrieve Data**

Used to **fetch** data without modifying anything.

```js
app.get('/api/users', (req, res) => {
  res.json([{ id: 1, name: 'Alice' }, { id: 2, name: 'Bob' }]);
});
```

### **POST – Create Data**

Used to **create** new resources.

```js
app.post('/api/users', (req, res) => {
  const user = req.body;
  users.push(user);
  res.status(201).json(user);
});
```

### **PUT – Update Entire Resource**

Used to **replace** existing data.

```js
app.put('/api/users/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const updatedUser = req.body;
  users[id - 1] = updatedUser;
  res.json(updatedUser);
});
```

### **PATCH – Partial Update**

Used to **modify** part of a resource.

```js
app.patch('/api/users/:id', (req, res) => {
  const user = users.find(u => u.id === parseInt(req.params.id));
  Object.assign(user, req.body);
  res.json(user);
});
```

### **DELETE – Remove Resource**

Removes a record.

```js
app.delete('/api/users/:id', (req, res) => {
  const index = users.findIndex(u => u.id === parseInt(req.params.id));
  users.splice(index, 1);
  res.status(204).send();
});
```

---

## **2. HTTP Status Codes Explained**

Each HTTP response includes a status code that indicates the result of the request.

| Code                          | Meaning               | Example Scenario                          |
| ----------------------------- | --------------------- | ----------------------------------------- |
| **200 OK**                    | Request succeeded     | Data retrieved successfully               |
| **201 Created**               | New record created    | A user was added                          |
| **400 Bad Request**           | Invalid input         | Missing required field                    |
| **401 Unauthorized**          | Authentication failed | Missing JWT token                         |
| **403 Forbidden**             | Access denied         | Regular user trying to access admin route |
| **404 Not Found**             | Resource missing      | User ID doesn’t exist                     |
| **500 Internal Server Error** | Server crashed        | Exception not handled                     |

Example:

```js
app.get('/api/users/:id', (req, res) => {
  const user = users.find(u => u.id === parseInt(req.params.id));
  if (!user) return res.status(404).json({ message: 'User not found' });
  res.status(200).json(user);
});
```

---

## **3. Authentication vs Authorization**

| Concept            | Purpose                           | Example                      |
| ------------------ | --------------------------------- | ---------------------------- |
| **Authentication** | Verifies *who* the user is        | Login with username/password |
| **Authorization**  | Determines *what* the user can do | Only admins can delete users |

Example:

```js
app.get('/dashboard', authenticate, (req, res) => {
  res.send(`Welcome ${req.user.name}`);
});
```

Here, authentication ensures the user is logged in before accessing the dashboard.

---

## **4. JSON Web Token (JWT) Authentication**

JWT tokens are used to verify users **without maintaining sessions**.

### **Token Creation**

```js
import jwt from 'jsonwebtoken';
const token = jwt.sign({ id: user.id }, process.env.JWT_SECRET, { expiresIn: '1h' });
res.json({ token });
```

### **Middleware to Verify Token**

```js
export const authenticate = (req, res, next) => {
  const token = req.header('Authorization')?.replace('Bearer ', '');
  if (!token) return res.status(401).send('Access Denied');

  try {
    const verified = jwt.verify(token, process.env.JWT_SECRET);
    req.user = verified;
    next();
  } catch (err) {
    res.status(400).send('Invalid Token');
  }
};
```

### **Complete Example**

```js
app.post('/login', async (req, res) => {
  const user = users.find(u => u.username === req.body.username);
  if (!user) return res.status(400).send('User not found');

  const valid = await bcrypt.compare(req.body.password, user.password);
  if (!valid) return res.status(401).send('Invalid password');

  const token = jwt.sign({ username: user.username }, process.env.JWT_SECRET);
  res.json({ token });
});
```

---

## **5. OAuth2 Authentication (Google Example)**

OAuth2 allows users to sign in with external providers.

```js
import passport from 'passport';
import { Strategy as GoogleStrategy } from 'passport-google-oauth20';

passport.use(new GoogleStrategy({
  clientID: process.env.GOOGLE_CLIENT_ID,
  clientSecret: process.env.GOOGLE_CLIENT_SECRET,
  callbackURL: '/auth/google/callback'
}, (accessToken, refreshToken, profile, done) => {
  done(null, profile);
}));

app.get('/auth/google', passport.authenticate('google', { scope: ['profile', 'email'] }));
app.get('/auth/google/callback', passport.authenticate('google', { failureRedirect: '/' }), (req, res) => {
  res.redirect('/dashboard');
});
```

This automatically handles Google sign-in and returns user information securely.

---

## **6. Password Hashing with bcrypt**

Always hash passwords before storing them.

```js
import bcrypt from 'bcryptjs';

const salt = await bcrypt.genSalt(10);
const hashed = await bcrypt.hash(password, salt);
console.log('Hashed password:', hashed);

// Verification
authenticated = await bcrypt.compare(password, hashed);
```

---

## **7. Role-Based Access Control (RBAC)**

Control access based on user roles like `admin`, `editor`, or `user`.

```js
export const authorizeRoles = (...roles) => (req, res, next) => {
  if (!roles.includes(req.user.role)) return res.status(403).send('Forbidden');
  next();
};

app.get('/admin', authenticate, authorizeRoles('admin'), (req, res) => {
  res.send('Admin Access Granted');
});
```

---

## **8. Secure API Best Practices**

Implement the following to keep APIs secure:

1. **Use HTTPS** → Prevent data snooping.
2. **Add rate limiting:**

   ```js
   import rateLimit from 'express-rate-limit';
   app.use(rateLimit({ windowMs: 15 * 60 * 1000, max: 100 }));
   ```
3. **Enable CORS and helmet:**

   ```js
   import cors from 'cors';
   import helmet from 'helmet';
   app.use(cors());
   app.use(helmet());
   ```
4. **Validate all inputs** → Prevent SQL/NoSQL injections.
5. **Hide tech stack:** `app.disable('x-powered-by');`
6. **Store credentials in .env** files.

---

## **9. End-to-End Example (JWT Auth)**

```js
import express from 'express';
import jwt from 'jsonwebtoken';
import bcrypt from 'bcryptjs';
import dotenv from 'dotenv';
import { authenticate } from './middleware/auth.js';

dotenv.config();
const app = express();
app.use(express.json());
const users = [];

// Register
app.post('/register', async (req, res) => {
  const { username, password } = req.body;
  const hashedPassword = await bcrypt.hash(password, 10);
  users.push({ username, password: hashedPassword, role: 'user' });
  res.status(201).send('User Registered');
});

// Login
app.post('/login', async (req, res) => {
  const user = users.find(u => u.username === req.body.username);
  if (!user) return res.status(404).send('User not found');

  const valid = await bcrypt.compare(req.body.password, user.password);
  if (!valid) return res.status(401).send('Invalid credentials');

  const token = jwt.sign({ username: user.username, role: user.role }, process.env.JWT_SECRET, { expiresIn: '1h' });
  res.json({ token });
});

// Protected Route
app.get('/profile', authenticate, (req, res) => {
  res.json({ message: `Welcome ${req.user.username}` });
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

---

## **10. Summary**

✅ Learn CRUD operations using **HTTP methods**.
✅ Handle errors properly using **status codes**.
✅ Implement **JWT and OAuth2 authentication**.
✅ Secure routes with **RBAC authorization**.
✅ Follow **security best practices** for production.

---

### 🚀 Next Steps

* Integrate **Auth0 or Firebase** for managed authentication.
* Add **refresh tokens** and **logout endpoint**.
* Implement **2FA (Two-Factor Authentication)** for advanced security.

---

**Author:** Node.js Mastery Series — Extended Edition with Code Examples
