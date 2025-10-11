# Flask Mastery Series — Questions, Explanations, and Examples

This expanded version includes **questions**, **detailed explanations**, and **hands-on examples** for each Flask topic — ideal for interviews, self-assessment, or structured learning.

---

## 🧩 Part I — Fundamentals

### **Question 1:** What is Flask, and how does it differ from Django?

**Explanation:** Flask is a lightweight microframework built on WSGI, emphasizing flexibility and minimalism. Django is a full-stack framework with ORM, admin panel, and conventions. Flask gives you full control over structure and dependencies.

### **Example — Minimal App**

```python
from flask import Flask
app = Flask(__name__)

@app.get('/')
def home():
    return 'Hello, Flask!'

if __name__ == '__main__':
    app.run(debug=True)
```

**Explanation:** This starts a development server on port 5000. The `@app.get('/')` decorator registers a route for HTTP GET requests.

### **Question 2:** What is the difference between WSGI and ASGI?

**Explanation:**

- **WSGI** handles synchronous requests (used by Flask, Django classic).
- **ASGI** supports async I/O, enabling websockets and background tasks.

---

## ⚙️ Part II — Asynchronous Programming (Deep Dive)

### **Question 3:** How does Flask handle async routes?

**Explanation:** Flask ≥2.0 allows `async def` routes, but true concurrency requires an ASGI server or frameworks like Quart.

### **Example — Async Route**

```python
import asyncio
from flask import Flask, jsonify
app = Flask(__name__)

@app.get('/slow')
async def slow():
    await asyncio.sleep(2)
    return jsonify(done=True)
```

### **Question 4:** When should you use Celery with Flask?

**Explanation:** Celery is used for running time-consuming or background jobs (emails, reports, etc.) outside the main request thread.

**Example — Celery Integration**

```python
from celery import Celery
celery = Celery(__name__, broker='redis://localhost:6379/0')

@celery.task
def send_email(to):
    print(f'Sending email to {to}')
```

---

## 🌐 Part III — Building APIs

### **Question 5:** How can you create REST APIs using Flask?

**Explanation:** Flask allows defining routes that respond to HTTP verbs. You can use extensions like Flask-RESTX or Marshmallow for schema validation.

**Example — CRUD API**

```python
from flask import Flask, request, jsonify
app = Flask(__name__)
users = [{'id': 1, 'name': 'Alice'}]

@app.get('/api/users')
def get_users():
    return jsonify(users)

@app.post('/api/users')
def create_user():
    data = request.get_json()
    new = {'id': len(users)+1, **data}
    users.append(new)
    return jsonify(new), 201
```

### **Question 6:** How to handle validation errors?

**Explanation:** Use `marshmallow` to validate request data and return descriptive errors.

---

## 💾 Part IV — Databases

### **Question 7:** What is Flask-SQLAlchemy?

**Explanation:** Flask-SQLAlchemy integrates SQLAlchemy ORM into Flask. It abstracts database operations and manages sessions.

**Example — SQLAlchemy Model**

```python
from flask_sqlalchemy import SQLAlchemy
from flask import Flask
app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///app.db'
db = SQLAlchemy(app)

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100))
```

### **Question 8:** How do you perform migrations?

**Explanation:** Migrations are managed using **Alembic**, which tracks schema versions.

---

## 🔐 Part V — Authentication & Authorization

### **Question 9:** How do JWTs work in Flask?

**Explanation:** JWT (JSON Web Token) provides stateless authentication. The token encodes user identity and claims.

**Example — JWT Authentication**

```python
from flask_jwt_extended import JWTManager, create_access_token, jwt_required, get_jwt_identity

app.config['JWT_SECRET_KEY'] = 'super-secret'
jwt = JWTManager(app)

@app.post('/login')
def login():
    token = create_access_token(identity='user123')
    return {'access_token': token}

@app.get('/me')
@jwt_required()
def me():
    return {'user': get_jwt_identity()}
```

### **Question 10:** How does OAuth2 integration work?

**Explanation:** Flask integrates OAuth2 via **Authlib** for federated login using providers like Google or GitHub.

---

## 🧪 Part VI — Testing & CI/CD

### **Question 11:** How do you test Flask endpoints?

**Explanation:** Flask includes a test client that simulates requests without running the server.

**Example — pytest Test**

```python
from app import app

def test_ping():
    with app.test_client() as c:
        res = c.get('/ping')
        assert res.status_code == 200
```

### **Question 12:** How can you automate Flask tests in CI/CD?

**Explanation:** Use GitHub Actions to run automated test workflows.

---

## ⚡ Part VII — Advanced Flask Concepts

### **Question 13:** How do you cache expensive API calls?

**Explanation:** Use Flask-Caching or Redis to reduce computation time.

**Example — Cached Route**

```python
from flask_caching import Cache
cache = Cache(app, config={'CACHE_TYPE': 'SimpleCache'})

@cache.cached(timeout=60)
@app.get('/expensive')
def expensive():
    return {'value': 42}
```

### **Question 14:** How do you limit request rates?

**Explanation:** Flask-Limiter controls request frequency based on IP or user.

---

## 🚀 Part VIII — Production & Deployment

### **Question 15:** What are best practices for Flask in production?

**Explanation:**

- Use Gunicorn + Nginx for serving.
- Enable HTTPS and security headers.
- Use environment variables for secrets.

**Example — Dockerfile**

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["gunicorn", "app:app", "-w", "4", "-b", "0.0.0.0:8000"]
```

### **Question 16:** How do you deploy Flask on Kubernetes?

**Explanation:** Use a Deployment for scaling and a Service for load balancing.

**Example — Kubernetes Manifest**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata: { name: flask-api }
spec:
  replicas: 3
  selector: { matchLabels: { app: flask-api } }
  template:
    metadata: { labels: { app: flask-api } }
    spec:
      containers:
        - name: app
          image: flask-api:latest
          ports: [{ containerPort: 8000 }]
```

---

## ✅ Summary Checklist

| Area            | Key Focus                           |
| --------------- | ----------------------------------- |
| **Security**    | HTTPS, JWT, OAuth2, CSRF Protection |
| **Performance** | Caching, Pagination, Indexes        |
| **Scalability** | Gunicorn Workers, Docker, K8s       |
| **Testing**     | pytest, CI/CD with GitHub Actions   |
| **Monitoring**  | Logging, Sentry, OpenTelemetry      |

---

**End of Document — Flask Mastery Series (with Questions & Explanations)**
