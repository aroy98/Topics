# Django Mastery Series — Questions, Explanations, and Examples

This comprehensive guide covers **everything from Django fundamentals to production deployment**, including **Django REST Framework (DRF)** and **Celery**. Each section contains **questions**, **explanations**, and **code examples**.

---

## 🧩 Part I — Fundamentals

### **Question 1:** What is Django, and what architecture does it use?

**Explanation:** Django is a high-level Python web framework following the **MTV pattern** (Model–Template–View). It emphasizes DRY (Don’t Repeat Yourself), rapid development, and clean design.

**MTV vs MVC:**

* **Model:** Data and database representation.
* **Template:** HTML layer (similar to View in MVC).
* **View:** Business logic (similar to Controller in MVC).

**Example — Minimal Project Setup**

```bash
pip install django

django-admin startproject mysite
cd mysite
python manage.py startapp core
python manage.py runserver
```

**Example — View & URL**

```python
# core/views.py
from django.http import HttpResponse

def home(request):
    return HttpResponse("Hello, Django!")
```

```python
# mysite/urls.py
from django.urls import path
from core.views import home
urlpatterns = [path('', home)]
```

---

### **Question 2:** How does Django handle requests internally?

**Explanation:** Django uses the **WSGI interface**. Each request passes through:

1. Middleware
2. URL Resolver
3. View
4. Template Rendering
5. Response

---

## 🧱 Part II — Models and ORM

### **Question 3:** How does Django ORM work?

**Explanation:** Django ORM maps database tables to Python classes. It supports multiple databases (PostgreSQL, MySQL, SQLite, etc.) and migrations.

**Example — Model Definition**

```python
# core/models.py
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
```

**Example — Database Migration**

```bash
python manage.py makemigrations
python manage.py migrate
```

**Example — ORM Querying**

```python
from core.models import Post

Post.objects.create(title="Hello", content="First Post")
Post.objects.all()
Post.objects.filter(title__icontains="Hello")
```

---

### **Question 4:** How do you use model relationships?

**Explanation:** Django supports **One-to-One**, **Many-to-One**, and **Many-to-Many** relationships.

**Example — Relationships**

```python
class Author(models.Model):
    name = models.CharField(max_length=100)

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
```

---

## 🧭 Part III — Views and Templates

### **Question 5:** What are function-based and class-based views?

**Explanation:**

* **Function-Based Views (FBV):** Simple functions that handle requests.
* **Class-Based Views (CBV):** Classes offering reusable patterns for CRUD.

**Example — Function-Based View**

```python
def about(request):
    return render(request, 'about.html', {'title': 'About Us'})
```

**Example — Class-Based View**

```python
from django.views import View

class AboutView(View):
    def get(self, request):
        return render(request, 'about.html')
```

### **Question 6:** How does Django’s template system work?

**Explanation:** Django templates support logic via tags, filters, and inheritance.

**Example — Template**

```html
<!-- templates/home.html -->
{% extends 'base.html' %}
{% block content %}
  <h1>{{ title }}</h1>
  {% for post in posts %}
    <p>{{ post.title }} - {{ post.created_at }}</p>
  {% endfor %}
{% endblock %}
```

---

## 🌐 Part IV — Building REST APIs (Django REST Framework)

### **Question 7:** What is Django REST Framework (DRF)?

**Explanation:** DRF simplifies RESTful API development using serializers, viewsets, and routers.

**Example — Install & Setup**

```bash
pip install djangorestframework
```

```python
# mysite/settings.py
INSTALLED_APPS = ['rest_framework', 'core']
```

**Example — Serializer**

```python
# core/serializers.py
from rest_framework import serializers
from .models import Post

class PostSerializer(serializers.ModelSerializer):
    class Meta:
        model = Post
        fields = '__all__'
```

**Example — API ViewSet**

```python
# core/views.py
from rest_framework import viewsets
from .models import Post
from .serializers import PostSerializer

class PostViewSet(viewsets.ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostSerializer
```

**Example — Router**

```python
# core/urls.py
from rest_framework.routers import DefaultRouter
from .views import PostViewSet

router = DefaultRouter()
router.register('posts', PostViewSet)
urlpatterns = router.urls
```

### **Question 8:** How do you handle authentication in DRF?

**Explanation:** DRF supports session, token, and JWT-based authentication.

**Example — JWT Auth**

```bash
pip install djangorestframework-simplejwt
```

```python
# settings.py
REST_FRAMEWORK = {
  'DEFAULT_AUTHENTICATION_CLASSES': (
      'rest_framework_simplejwt.authentication.JWTAuthentication',
  )
}
```

```python
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

urlpatterns = [
  path('api/token/', TokenObtainPairView.as_view()),
  path('api/token/refresh/', TokenRefreshView.as_view()),
]
```

---

## 🔐 Part V — Authentication & Authorization

### **Question 9:** How does Django handle user authentication?

**Explanation:** Django provides a built-in `User` model, forms, and authentication views.

**Example — Login View**

```python
from django.contrib.auth import authenticate, login
from django.shortcuts import render, redirect

def login_view(request):
    if request.method == 'POST':
        user = authenticate(request, username=request.POST['username'], password=request.POST['password'])
        if user:
            login(request, user)
            return redirect('home')
    return render(request, 'login.html')
```

### **Question 10:** How do permissions work in Django?

**Explanation:** Django provides object-level and group-based permissions.

**Example — Custom Permission**

```python
from rest_framework.permissions import BasePermission

class IsAuthor(BasePermission):
    def has_object_permission(self, request, view, obj):
        return obj.author == request.user
```

---

## 🧪 Part VI — Testing & CI/CD

### **Question 11:** How to test Django apps?

**Explanation:** Django integrates with `unittest` and supports fixtures and test databases.

**Example — Basic Test**

```python
from django.test import TestCase
from .models import Post

class PostTestCase(TestCase):
    def test_create_post(self):
        post = Post.objects.create(title="Test", content="Post content")
        self.assertEqual(Post.objects.count(), 1)
```

### **Question 12:** How to automate CI/CD?

**Example — GitHub Actions**

```yaml
name: Django CI
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
      - run: python manage.py test
```

---

## ⚙️ Part VII — Advanced Django Concepts

### **Question 13:** What are Django signals?

**Explanation:** Signals allow decoupled applications to get notified when actions occur.

**Example — Signals**

```python
from django.db.models.signals import post_save
from django.dispatch import receiver
from .models import Post

@receiver(post_save, sender=Post)
def post_created(sender, instance, created, **kwargs):
    if created:
        print(f'New post created: {instance.title}')
```

### **Question 14:** How to schedule background tasks in Django?

**Explanation:** Use **Celery** for async background jobs.

**Example — Celery Setup**

```bash
pip install celery redis
```

```python
# mysite/celery.py
from celery import Celery
app = Celery('mysite', broker='redis://localhost:6379/0')
app.autodiscover_tasks()
```

```python
# core/tasks.py
from mysite.celery import app
@app.task
def send_email():
    print('Email sent')
```

---

## 🚀 Part VIII — Production & Deployment

### **Question 15:** How to deploy Django in production?

**Explanation:**

* Use **Gunicorn** + **Nginx**
* Use environment variables for secrets
* Serve static files via **WhiteNoise** or S3

**Example — Gunicorn Command**

```bash
gunicorn mysite.wsgi:application --bind 0.0.0.0:8000 --workers 4
```

**Example — Dockerfile**

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["gunicorn", "mysite.wsgi:application", "--bind", "0.0.0.0:8000"]
```

### **Question 16:** How to scale with Kubernetes?

**Explanation:** Use Deployment + Service for autoscaling.

**Example — K8s Manifest**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata: { name: django-app }
spec:
  replicas: 3
  selector: { matchLabels: { app: django-app } }
  template:
    metadata: { labels: { app: django-app } }
    spec:
      containers:
        - name: django
          image: django:latest
          ports: [{ containerPort: 8000 }]
```

---

## ✅ Summary Checklist

| Area                | Focus                                 |
| ------------------- | ------------------------------------- |
| **Framework**       | Django (MTV Pattern, ORM, Templating) |
| **API**             | Django REST Framework, JWT Auth       |
| **Background Jobs** | Celery, Redis                         |
| **Testing**         | Django TestCase, pytest, CI/CD        |
| **Security**        | CSRF, XSS, HTTPS, Permissions         |
| **Deployment**      | Gunicorn, Docker, Kubernetes          |

---

**End of Document — Django Mastery Series (with DRF & Celery, Questions, Explanations, and Examples)**
