---

## 🐶 **Estructura del Proyecto**

📁 **UIII_Entrenamiento_Canino_0602/**
│
├── 📁 **.venv/**
│   (Carpeta del entorno virtual)
│
├── 📁 **backend_Entrenamiento_Canino/**
│   ├── 📁 **backend_Entrenamiento_Canino/**
│   │   ├── **init**.py
│   │   ├── **settings.py**
│   │   ├── **urls.py**
│   │   ├── asgi.py
│   │   └── wsgi.py
│   │
│   ├── 📁 **app_Entrenamiento_Canino/**
│   │   ├── **init**.py
│   │   ├── **admin.py**
│   │   ├── **apps.py**
│   │   ├── **models.py**
│   │   ├── **views.py**
│   │   ├── **urls.py**
│   │   ├── **migrations/**
│   │   │   └── **init**.py
│   │   │
│   │   └── 📁 **templates/**
│   │       ├── **base.html**
│   │       ├── **header.html**
│   │       ├── **navbar.html**
│   │       ├── **footer.html**
│   │       ├── **inicio.html**
│   │       └── 📁 **clientes/**
│   │           ├── agregar_cliente.html
│   │           ├── ver_cliente.html
│   │           ├── actualizar_cliente.html
│   │           └── borrar_cliente.html
│   │
│   ├── **manage.py**
│
└── 📁 **static/**
  └── 📁 **css/**
    └── estilos.css

---

## ⚙️ **Explicación Paso a Paso (con comandos)**

### 1️⃣ Crear carpeta principal y abrir en VS Code

```bash
mkdir UIII_Entrenamiento_Canino_0602
cd UIII_Entrenamiento_Canino_0602
code .
```

---

### 2️⃣ Crear entorno virtual

```bash
python -m venv .venv
```

Activar entorno:

* En Windows:

```bash
.venv\Scripts\activate
```

---

### 3️⃣ Activar intérprete en VS Code

* Presiona `Ctrl + Shift + P`
* Escribe: **Python: Select Interpreter**
* Selecciona el entorno **.venv**

---

### 4️⃣ Instalar Django

```bash
pip install django
```

---

### 5️⃣ Crear proyecto

```bash
django-admin startproject backend_Entrenamiento_Canino .
```

---

### 6️⃣ Crear aplicación

```bash
python manage.py startapp app_Entrenamiento_Canino
```

Agregar la app al `settings.py`:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app_Entrenamiento_Canino',
]
```

---

### 7️⃣ Crear el modelo (models.py)

```python
from django.db import models

# ==========================================
# MODELO: CLIENTE
# ==========================================
class Cliente(models.Model):
    id_cliente = models.AutoField(primary_key=True)
    nombre = models.CharField(max_length=100)
    direccion = models.CharField(max_length=100)
    telefono = models.CharField(max_length=20)
    email = models.EmailField(max_length=100, unique=True)
    fecha_registro = models.DateField()
    fecha_nacimiento = models.DateField()
    comentarios = models.CharField(max_length=100, blank=True, null=True)

    def __str__(self):
        return self.nombre
```

---

### 8️⃣ Migraciones

```bash
python manage.py makemigrations
python manage.py migrate
```

---

### 9️⃣ Registrar el modelo en `admin.py`

```python
from django.contrib import admin
from .models import Cliente

admin.site.register(Cliente)
```

---

### 🔟 Crear vistas en `views.py`

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Cliente

def inicio_entrenamiento_canino(request):
    return render(request, 'inicio.html')

def agregar_cliente(request):
    if request.method == 'POST':
        Cliente.objects.create(
            nombre=request.POST['nombre'],
            direccion=request.POST['direccion'],
            telefono=request.POST['telefono'],
            email=request.POST['email'],
            fecha_registro=request.POST['fecha_registro'],
            fecha_nacimiento=request.POST['fecha_nacimiento'],
            comentarios=request.POST['comentarios']
        )
        return redirect('ver_cliente')
    return render(request, 'clientes/agregar_cliente.html')

def ver_cliente(request):
    clientes = Cliente.objects.all()
    return render(request, 'clientes/ver_cliente.html', {'clientes': clientes})

def actualizar_cliente(request, id):
    cliente = get_object_or_404(Cliente, id_cliente=id)
    if request.method == 'POST':
        cliente.nombre = request.POST['nombre']
        cliente.direccion = request.POST['direccion']
        cliente.telefono = request.POST['telefono']
        cliente.email = request.POST['email']
        cliente.comentarios = request.POST['comentarios']
        cliente.save()
        return redirect('ver_cliente')
    return render(request, 'clientes/actualizar_cliente.html', {'cliente': cliente})

def borrar_cliente(request, id):
    cliente = get_object_or_404(Cliente, id_cliente=id)
    if request.method == 'POST':
        cliente.delete()
        return redirect('ver_cliente')
    return render(request, 'clientes/borrar_cliente.html', {'cliente': cliente})
```

---

### 1️⃣1️⃣ URLs de la app (`app_Entrenamiento_Canino/urls.py`)

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_entrenamiento_canino, name='inicio'),
    path('agregar/', views.agregar_cliente, name='agregar_cliente'),
    path('ver/', views.ver_cliente, name='ver_cliente'),
    path('actualizar/<int:id>/', views.actualizar_cliente, name='actualizar_cliente'),
    path('borrar/<int:id>/', views.borrar_cliente, name='borrar_cliente'),
]
```

---

### 1️⃣2️⃣ Enlazar URLs con el proyecto principal (`backend_Entrenamiento_Canino/urls.py`)

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Entrenamiento_Canino.urls')),
]
```

---

### 1️⃣3️⃣ Archivos HTML principales

#### 📄 base.html

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Entrenamiento Canino</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
    <link rel="stylesheet" href="{% static 'css/estilos.css' %}">
</head>
<body>
    {% include 'navbar.html' %}
    <div class="container mt-4">
        {% block content %}{% endblock %}
    </div>
    {% include 'footer.html' %}
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

#### 📄 navbar.html

```html
<nav class="navbar navbar-expand-lg navbar-dark bg-primary">
  <div class="container-fluid">
    <a class="navbar-brand" href="#">🐾 Sistema de Administración Entrenamiento Canino</a>
    <div class="collapse navbar-collapse">
      <ul class="navbar-nav ms-auto">
        <li class="nav-item"><a class="nav-link" href="{% url 'inicio' %}">Inicio</a></li>
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown">Clientes</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="{% url 'agregar_cliente' %}">Agregar Cliente</a></li>
            <li><a class="dropdown-item" href="{% url 'ver_cliente' %}">Ver Clientes</a></li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</nav>
```

#### 📄 footer.html

```html
<footer class="bg-light text-center text-muted py-3 fixed-bottom">
  © <script>document.write(new Date().getFullYear())</script> — Creado por Técnico Hernández Sánchez María Rene, CBTIS 128
</footer>
```

#### 📄 inicio.html

```html
{% extends 'base.html' %}
{% block content %}
<div class="text-center">
    <h1>Bienvenido al Sistema de Entrenamiento Canino</h1>
    <p>Administra clientes, perros y entrenamientos fácilmente.</p>
    <img src="https://cdn.pixabay.com/photo/2016/02/19/11/19/dog-1209621_1280.jpg" class="img-fluid rounded shadow" alt="Entrenamiento Canino">
</div>
{% endblock %}
```

#### 📁 clientes/agregar_cliente.html

```html
{% extends 'base.html' %}
{% block content %}
<h2>Agregar Cliente</h2>
<form method="POST">
    {% csrf_token %}
    <input type="text" name="nombre" placeholder="Nombre" class="form-control mb-2">
    <input type="text" name="direccion" placeholder="Dirección" class="form-control mb-2">
    <input type="text" name="telefono" placeholder="Teléfono" class="form-control mb-2">
    <input type="email" name="email" placeholder="Email" class="form-control mb-2">
    <input type="date" name="fecha_registro" class="form-control mb-2">
    <input type="date" name="fecha_nacimiento" class="form-control mb-2">
    <textarea name="comentarios" placeholder="Comentarios" class="form-control mb-2"></textarea>
    <button class="btn btn-success">Guardar</button>
</form>
{% endblock %}
```

#### 📁 clientes/ver_cliente.html

```html
{% extends 'base.html' %}
{% block content %}
<h2>Lista de Clientes</h2>
<table class="table table-striped">
  <thead>
    <tr>
      <th>Nombre</th><th>Dirección</th><th>Email</th><th>Acciones</th>
    </tr>
  </thead>
  <tbody>
  {% for cliente in clientes %}
  <tr>
    <td>{{ cliente.nombre }}</td>
    <td>{{ cliente.direccion }}</td>
    <td>{{ cliente.email }}</td>
    <td>
      <a href="{% url 'actualizar_cliente' cliente.id_cliente %}" class="btn btn-warning btn-sm">Editar</a>
      <a href="{% url 'borrar_cliente' cliente.id_cliente %}" class="btn btn-danger btn-sm">Borrar</a>
    </td>
  </tr>
  {% endfor %}
  </tbody>
</table>
{% endblock %}
```

---

### 🧩 Estilos (static/css/estilos.css)

```css
body {
  background-color: #f8fafc;
}

h1, h2 {
  color: #2b3d52;
}

footer {
  background-color: #e8f0fe;
  color: #555;
}
```

---

### 🚀 Ejecutar servidor

```bash
python manage.py runserver 8019
```

Luego abre:
👉 [http://127.0.0.1:8019/](http://127.0.0.1:8019/)

---

¿Quieres que te genere también los HTML del CRUD completo (actualizar y borrar) para clientes con sus botones y formularios?
