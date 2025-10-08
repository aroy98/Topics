# Node.js Mastery Series

## **Part IV – Databases**

### **1. Overview**

Node.js supports multiple databases — both **SQL** and **NoSQL**. The most common include:

* **MongoDB** – Document-oriented (NoSQL)
* **PostgreSQL** – Relational (SQL)
* **SQLite** – Lightweight, file-based database

---

### **2. Connecting to MongoDB**

Use **Mongoose**, a popular ODM (Object Data Modeling) library.

```js
import mongoose from 'mongoose';

mongoose.connect('mongodb://localhost:27017/mydb')
  .then(() => console.log('MongoDB connected'))
  .catch(err => console.error(err));

const userSchema = new mongoose.Schema({
  name: String,
  email: String,
  age: Number
});

const User = mongoose.model('User', userSchema);

// Insert a document
await User.create({ name: 'Alice', email: 'alice@example.com', age: 25 });
```

**Key Concepts:**

* Schema defines structure and validation.
* Models provide CRUD operations.
* Middleware hooks for pre/post actions.

---

### **3. Connecting to PostgreSQL**

Use the **pg** package or an ORM like **Sequelize** or **Prisma**.

#### **Using pg (Native Driver):**

```js
import pkg from 'pg';
const { Pool } = pkg;

const pool = new Pool({
  user: 'postgres',
  host: 'localhost',
  database: 'testdb',
  password: 'password',
  port: 5432,
});

const res = await pool.query('SELECT NOW()');
console.log(res.rows[0]);
```

#### **Using Sequelize ORM:**

```js
import { Sequelize, DataTypes } from 'sequelize';
const sequelize = new Sequelize('testdb', 'postgres', 'password', {
  host: 'localhost',
  dialect: 'postgres',
});

const User = sequelize.define('User', {
  name: DataTypes.STRING,
  email: DataTypes.STRING,
});

await sequelize.sync();
await User.create({ name: 'John', email: 'john@example.com' });
```

---

### **4. Connecting to SQLite**

SQLite is perfect for lightweight or embedded applications.

#### **Using better-sqlite3:**

```js
import Database from 'better-sqlite3';
const db = new Database('app.db');

db.prepare('CREATE TABLE IF NOT EXISTS users (id INTEGER PRIMARY KEY, name TEXT)').run();
db.prepare('INSERT INTO users (name) VALUES (?)').run('Alice');
const rows = db.prepare('SELECT * FROM users').all();
console.log(rows);
```

#### **Using Prisma (with SQLite):**

```bash
npx prisma init --datasource-provider sqlite
```

**prisma/schema.prisma**

```prisma
datasource db {
  provider = "sqlite"
  url      = "file:./dev.db"
}

model User {
  id    Int     @id @default(autoincrement())
  name  String
  email String
}
```

**Generate and use:**

```bash
npx prisma migrate dev --name init
```

```js
import { PrismaClient } from '@prisma/client';
const prisma = new PrismaClient();

const users = await prisma.user.findMany();
console.log(users);
```

---

### **5. ORM/ODM Comparison**

| Tool      | Type | Supported DB                  | Key Feature          |
| --------- | ---- | ----------------------------- | -------------------- |
| Prisma    | ORM  | SQL (Postgres, MySQL, SQLite) | Type-safe queries    |
| Sequelize | ORM  | SQL                           | Model-based querying |
| Mongoose  | ODM  | MongoDB                       | Schema enforcement   |

---

### **6. Query Optimization Techniques**

* Use **indexes** on frequently queried fields.
* Apply **projection** (return only necessary fields).
* Use **pagination** (limit + offset).
* Optimize queries with **explain plans**.

#### **Example (MongoDB Index):**

```js
User.createIndexes({ email: 1 });
```

#### **Example (PostgreSQL Pagination):**

```sql
SELECT * FROM users ORDER BY id LIMIT 10 OFFSET 20;
```

---

### **7. Transactions**

Ensure data integrity in multi-step operations.

#### **PostgreSQL Transaction:**

```js
const client = await pool.connect();
try {
  await client.query('BEGIN');
  await client.query('INSERT INTO users(name) VALUES($1)', ['Alice']);
  await client.query('UPDATE accounts SET balance = balance - 100 WHERE id = 1');
  await client.query('COMMIT');
} catch (err) {
  await client.query('ROLLBACK');
  throw err;
} finally {
  client.release();
}
```

#### **MongoDB Transaction:**

```js
const session = await mongoose.startSession();
session.startTransaction();
try {
  await User.create([{ name: 'Bob' }], { session });
  await session.commitTransaction();
} catch (err) {
  await session.abortTransaction();
} finally {
  session.endSession();
}
```

---

### **8. Indexing Strategies**

* Use **compound indexes** for multi-field queries.
* Avoid over-indexing (affects write performance).
* Periodically monitor slow queries.

Example:

```js
User.createIndexes({ name: 1, age: -1 });
```

---

### **9. Database Configuration Best Practices**

* Keep credentials in **.env** file.
* Use **connection pooling**.
* Handle disconnections gracefully.
* Enable **SSL/TLS** in production.

---

### **10. Real-World Example: Unified DB Service Layer**

```js
import { PrismaClient } from '@prisma/client';
const prisma = new PrismaClient();

export const UserService = {
  async createUser(data) {
    return await prisma.user.create({ data });
  },
  async getUsers() {
    return await prisma.user.findMany();
  },
  async deleteUser(id) {
    return await prisma.user.delete({ where: { id } });
  }
};
```

---

## **Next Up: Part V – Authentication & Authorization**
