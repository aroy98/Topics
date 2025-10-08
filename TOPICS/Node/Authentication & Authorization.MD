# Node.js Mastery Series

## **Part V – Authentication & Authorization**

### **1. Overview**

Authentication verifies **who** a user is, while authorization determines **what** they can access. Node.js supports multiple auth mechanisms like **JWT**, **OAuth2**, and **Session-based Auth**.

---

### **2. JWT (JSON Web Token) Authentication**

JWT is a compact, stateless way to authenticate users via tokens.

#### **Setup:**

```bash
npm install jsonwebtoken bcryptjs
```

#### **Generate Token:**

```js
import jwt from 'jsonwebtoken';
const token = jwt.sign({ id: user.id }, process.env.JWT_SECRET, { expiresIn: '1h' });
res.json({ token });
```

#### **Verify Token (Middleware):**

```js
import jwt from 'jsonwebtoken';

export const authenticate = (req, res, next) => {
  const token = req.header('Authorization')?.replace('Bearer ', '');
  if (!token) return res.status(401).send('Access Denied');
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (err) {
    res.status(400).send('Invalid Token');
  }
};
```

#### **Usage in Routes:**

```js
app.get('/dashboard', authenticate, (req, res) => {
  res.send(`Welcome ${req.user.id}`);
});
```

---

### **3. Password Hashing with bcrypt**

Always hash passwords before saving them.

```js
import bcrypt from 'bcryptjs';

const salt = await bcrypt.genSalt(10);
const hashedPassword = await bcrypt.hash(password, salt);

// Compare during login
const validPassword = await bcrypt.compare(req.body.password, user.password);
```

---

### **4. Session-Based Authentication (express-session)**

Used when you need **stateful** sessions (e.g., dashboards, admin panels).

```bash
npm install express-session connect-mongo
```

```js
import session from 'express-session';
import MongoStore from 'connect-mongo';

app.use(session({
  secret: process.env.SESSION_SECRET,
  resave: false,
  saveUninitialized: false,
  store: MongoStore.create({ mongoUrl: process.env.MONGO_URI }),
  cookie: { maxAge: 1000 * 60 * 60 }, // 1 hour
}));
```

---

### **5. OAuth2 Authentication**

OAuth2 enables third-party login (e.g., Google, GitHub, Facebook).

#### **Using Passport.js:**

```bash
npm install passport passport-google-oauth20
```

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

---

### **6. Role-Based Access Control (RBAC)**

Assign users roles (Admin, Editor, User) and restrict access accordingly.

```js
export const authorizeRoles = (...roles) => {
  return (req, res, next) => {
    if (!roles.includes(req.user.role)) {
      return res.status(403).send('Access Denied');
    }
    next();
  };
};

// Usage:
app.get('/admin', authenticate, authorizeRoles('admin'), (req, res) => {
  res.send('Admin Access Granted');
});
```

---

### **7. Integration with Auth0**

Auth0 provides a full-featured authentication and authorization platform.

#### **Setup:**

```bash
npm install express-openid-connect
```

```js
import { auth } from 'express-openid-connect';

app.use(auth({
  authRequired: false,
  auth0Logout: true,
  secret: process.env.AUTH0_SECRET,
  baseURL: 'http://localhost:3000',
  clientID: process.env.AUTH0_CLIENT_ID,
  issuerBaseURL: process.env.AUTH0_ISSUER
}));

app.get('/profile', (req, res) => {
  res.send(JSON.stringify(req.oidc.user));
});
```

---

### **8. Firebase Authentication**

Firebase simplifies JWT-based user management.

```js
import admin from 'firebase-admin';
admin.initializeApp({
  credential: admin.credential.cert('./serviceAccount.json')
});

// Verify user token
app.use(async (req, res, next) => {
  const token = req.header('Authorization')?.split(' ')[1];
  try {
    const decoded = await admin.auth().verifyIdToken(token);
    req.user = decoded;
    next();
  } catch {
    res.status(401).send('Unauthorized');
  }
});
```

---

### **9. API Security Best Practices**

* Use **HTTPS**.
* Sanitize input to prevent **SQL Injection / XSS**.
* Set **HTTP security headers** with `helmet`.
* Rate-limit requests using `express-rate-limit`.
* Store secrets securely in `.env` or secret managers.
* Invalidate tokens on logout or rotation.

---

### **10. Real-World Example – JWT Auth API**

```js
import express from 'express';
import jwt from 'jsonwebtoken';
import bcrypt from 'bcryptjs';
import dotenv from 'dotenv';

dotenv.config();
const app = express();
app.use(express.json());

const users = [];

app.post('/register', async (req, res) => {
  const { username, password } = req.body;
  const hashedPassword = await bcrypt.hash(password, 10);
  users.push({ username, password: hashedPassword });
  res.send('User registered successfully');
});

app.post('/login', async (req, res) => {
  const user = users.find(u => u.username === req.body.username);
  if (!user) return res.status(400).send('User not found');
  const valid = await bcrypt.compare(req.body.password, user.password);
  if (!valid) return res.status(401).send('Invalid credentials');

  const token = jwt.sign({ username: user.username }, process.env.JWT_SECRET, { expiresIn: '1h' });
  res.json({ token });
});

app.get('/protected', authenticate, (req, res) => {
  res.send('Access granted to protected route');
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

---

## **Next Up: Part VI – Testing & CI/CD**
