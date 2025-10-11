# FastAPI Mastery Series — Questions, Explanations, and Examples

This comprehensive series covers **everything you need to master FastAPI**, from fundamentals to production deployment. Each section includes **questions**, **explanations**, and **hands-on code examples**.

---

## 🧩 Part I — Fundamentals

### **Question 1:** What is FastAPI?

**Explanation:** FastAPI is a modern, async-first web framework built on **Starlette (ASGI)** and **Pydantic**. It’s designed for performance, developer productivity, and data validation.

**Key Features:**

* Native async support (ASGI)
* Automatic OpenAPI documentation (Swagger, Redoc)
* Pydantic models for type validation
* Dependency injection system

**Example — Minimal App**

```python
from fastapi import FastAPI
app = FastAPI()

@app.get('/')
def home():
    return {"message": "Hello, FastAPI!"}
```

Run:

```bash
uvicorn main:app --reload
```

---

### **Question 2:** What is ASGI, and how is it different from WSGI?

**Explanation:**

* **WSGI** supports synchronous requests (used by Flask, Django Classic).
* **ASGI** supports both synchronous and asynchronous requests, enabling WebSockets, long polling, and background tasks.

**Example — Async Endpoint**

```python
import asyncio
from fastapi import FastAPI
app = FastAPI()

@app.get('/async')
async def async_endpoint():
    await asyncio.sleep(1)
    return {"status": "Completed"}
```

---

## ⚙️ Part II — Request Handling and Validation

### **Question 3:** How does FastAPI handle request validation?

**Explanation:** FastAPI uses **Pydantic** models to validate request bodies automatically.

**Example — Input Validation**

```python
from fastapi import FastAPI
from pydantic import BaseModel, EmailStr

app = FastAPI()

class User(BaseModel):
    name: str
    email: EmailStr
    age: int

@app.post('/users')
def create_user(user: User):
    return {"user": user}
```

If invalid data is sent, FastAPI automatically returns a 422 response with error details.

---

### **Question 4:** How are query and path parameters validated?

**Explanation:** Use FastAPI's type hints with built-in validation.

**Example — Query Parameters**

```python
from fastapi import Query

@app.get('/search')
def search(q: str = Query(..., min_length=3)):
    return {"query": q}
```

**Example — Path Parameters**

```python
@app.get('/users/{user_id}')
def get_user(user_id: int):
    return {"user_id": user_id}
```

---

## 🌐 Part III — Building APIs

### **Question 5:** How to create RESTful APIs in FastAPI?

**Explanation:** Define CRUD operations with `@app.get`, `@app.post`, etc. Use `ResponseModel` for structured outputs.

**Example — CRUD API**

```python
from typing import List
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

class Item(BaseModel):
    id: int
    name: str
    price: float

items: List[Item] = []

@app.get('/items', response_model=List[Item])
def get_items():
    return items

@app.post('/items', response_model=Item)
def create_item(item: Item):
    items.append(item)
    return item
```

### **Question 6:** How does FastAPI handle errors?

**Explanation:** Use `HTTPException` or custom exception handlers.

**Example — Error Handling**

```python
from fastapi import HTTPException

@app.get('/items/{id}')
def get_item(id: int):
    item = next((i for i in items if i.id == id), None)
    if not item:
        raise HTTPException(status_code=404, detail="Item not found")
    return item
```

---

## 💾 Part IV — Databases and ORM

### **Question 7:** How do you integrate databases in FastAPI?

**Explanation:** Use **SQLAlchemy**, **Tortoise ORM**, or **Gino** for async DB operations.

**Example — SQLAlchemy Integration**

```python
from sqlalchemy import Column, Integer, String, create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

DATABASE_URL = 'sqlite:///./test.db'
engine = create_engine(DATABASE_URL, connect_args={"check_same_thread": False})
SessionLocal = sessionmaker(bind=engine)
Base = declarative_base()

class User(Base):
    __tablename__ = 'users'
    id = Column(Integer, primary_key=True, index=True)
    name = Column(String, index=True)

Base.metadata.create_all(bind=engine)
```

### **Question 8:** How to use async ORM (Tortoise ORM)?

```python
from tortoise import Tortoise, fields
from tortoise.models import Model

class User(Model):
    id = fields.IntField(pk=True)
    name = fields.CharField(max_length=50)

async def init():
    await Tortoise.init(db_url='sqlite://db.sqlite3', modules={'models': ['__main__']})
    await Tortoise.generate_schemas()
```

---

## 🔐 Part V — Authentication & Authorization

### **Question 9:** How to secure APIs with JWT in FastAPI?

**Explanation:** Use `fastapi-jwt-auth` or `python-jose`.

**Example — JWT Authentication**

```python
from fastapi import Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
from jose import JWTError, jwt

SECRET_KEY = "secret"
ALGORITHM = "HS256"
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

@app.post('/token')
def login(form_data: OAuth2PasswordRequestForm = Depends()):
    if form_data.username != "admin" or form_data.password != "1234":
        raise HTTPException(status_code=401, detail="Invalid credentials")
    token = jwt.encode({"sub": form_data.username}, SECRET_KEY, algorithm=ALGORITHM)
    return {"access_token": token}

@app.get('/me')
def me(token: str = Depends(oauth2_scheme)):
    data = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
    return {"user": data['sub']}
```

### **Question 10:** How to use OAuth2 Social Login?

**Explanation:** Integrate with **Authlib** or **fastapi-users** for Google/GitHub logins.

---

## 🧪 Part VI — Testing & CI/CD

### **Question 11:** How to test FastAPI endpoints?

**Explanation:** Use FastAPI’s built-in `TestClient` (powered by Starlette).

**Example — pytest Test**

```python
from fastapi.testclient import TestClient
from main import app

client = TestClient(app)

def test_home():
    res = client.get('/')
    assert res.status_code == 200
```

### **Question 12:** How to integrate with GitHub Actions?

```yaml
name: FastAPI CI
on: [push]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.11'
      - run: pip install -r requirements.txt
      - run: pytest -q
```

---

## ⚡ Part VII — Advanced Concepts

### **Question 13:** How does dependency injection work in FastAPI?

**Explanation:** Dependencies are functions that can be shared across routes.

**Example — Dependency Injection**

```python
from fastapi import Depends

def common_params(q: str = None, skip: int = 0, limit: int = 10):
    return {"q": q, "skip": skip, "limit": limit}

@app.get('/items')
def read_items(commons: dict = Depends(common_params)):
    return commons
```

### **Question 14:** How to handle background tasks?

**Explanation:** Use `BackgroundTasks` for non-blocking operations.

**Example:**

```python
from fastapi import BackgroundTasks

def log_email(email: str):
    with open('emails.log', 'a') as f:
        f.write(f"Sent email to {email}\n")

@app.post('/send-email')
def send_email(email: str, bg: BackgroundTasks):
    bg.add_task(log_email, email)
    return {"status": "queued"}
```

---

## 🚀 Part VIII — Production & Deployment

### **Question 15:** How to deploy FastAPI in production?

**Explanation:** Use **Uvicorn**, **Gunicorn**, or **Hypercorn** with async workers.

**Example — Gunicorn Command**

```bash
gunicorn -k uvicorn.workers.UvicornWorker main:app -w 4 -b 0.0.0.0:8000
```

### **Question 16:** How to Dockerize FastAPI?

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### **Question 17:** How to scale FastAPI with Kubernetes?

**Explanation:** Create a Deployment and Service for horizontal scaling.

**Example — Kubernetes Manifest**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata: { name: fastapi-app }
spec:
  replicas: 3
  selector: { matchLabels: { app: fastapi-app } }
  template:
    metadata: { labels: { app: fastapi-app } }
    spec:
      containers:
        - name: fastapi
          image: fastapi:latest
          ports: [{ containerPort: 8000 }]
```

---

## ✅ Summary Checklist

| Area            | Focus                                       |
| --------------- | ------------------------------------------- |
| **Framework**   | FastAPI (async-native, Pydantic validation) |
| **Database**    | SQLAlchemy / Tortoise ORM                   |
| **Auth**        | OAuth2, JWT, FastAPI Users                  |
| **Testing**     | pytest, TestClient, GitHub Actions          |
| **Performance** | Uvicorn, Caching, Background Tasks          |
| **Production**  | Docker, Kubernetes, HTTPS, Monitoring       |

---

**End of Document — FastAPI Mastery Series (with Questions, Explanations, and Examples)**
