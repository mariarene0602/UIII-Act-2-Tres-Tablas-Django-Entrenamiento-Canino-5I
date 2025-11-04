Perfecto 🌟 Aquí tienes **TODO el proyecto completo y funcional**:
estructura, procedimientos, `models.py`, `views.py`, `urls.py`, y todos los **archivos HTML, CSS y JS** (con Bootstrap y un diseño limpio).
Solo trabajarás con el modelo **CLIENTE** (como indicas).

---

## 🐾 **PROYECTO: Entrenamiento Canino**

### 🔹 Lenguaje:

Python

### 🔹 Framework:

Django

### 🔹 Editor:

Visual Studio Code

---

## ⚙️ **PASOS PARA CREAR EL PROYECTO**

### 1️⃣ Crear carpeta del proyecto

Crea una carpeta llamada:

```
UIII_Entrenamiento_Canino_0602
```

---

### 2️⃣ Abrir la carpeta en VS Code

En VS Code → `Archivo → Abrir carpeta` → selecciona la carpeta creada.

---

### 3️⃣ Abrir la terminal

`Terminal → Nueva terminal`

---

### 4️⃣ Crear entorno virtual

```bash
python -m venv .venv
```

---

### 5️⃣ Activar entorno virtual

```bash
.venv\Scripts\activate
```

---

### 6️⃣ Instalar Django

```bash
pip install django
```

---

### 7️⃣ Crear proyecto

```bash
django-admin startproject backend_Entrenamiento_Canino .
```

---

### 8️⃣ Crear aplicación

```bash
python manage.py startapp app_Entrenamiento_Canino
```

---

## 🧩 **MODELOS – `models.py`**

📄 `app_Entrenamiento_Canino/models.py`

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

### 9️⃣ Registrar modelo en admin

📄 `app_Entrenamiento_Canino/admin.py`

```python
from django.contrib import admin
from .models import Cliente

admin.site.register(Cliente)
```

---

### 🔟 Migraciones

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## 🌐 **RUTAS**

📄 `backend_Entrenamiento_Canino/urls.py`

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Entrenamiento_Canino.urls')),
]
```

📄 `app_Entrenamiento_Canino/urls.py`

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_entrenamiento_canino, name='inicio'),
    path('agregar/', views.agregar_cliente, name='agregar_cliente'),
    path('ver/', views.ver_cliente, name='ver_cliente'),
    path('actualizar/<int:id>/', views.actualizar_cliente, name='actualizar_cliente'),
    path('realizar_actualizacion/<int:id>/', views.realizar_actualizacion_cliente, name='realizar_actualizacion_cliente'),
    path('borrar/<int:id>/', views.borrar_cliente, name='borrar_cliente'),
]
```

---

## ⚙️ **VISTAS (views.py)**

📄 `app_Entrenamiento_Canino/views.py`

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Cliente
from datetime import date

# Página de inicio
def inicio_entrenamiento_canino(request):
    return render(request, 'inicio.html')

# Agregar cliente
def agregar_cliente(request):
    if request.method == 'POST':
        nombre = request.POST['nombre']
        direccion = request.POST['direccion']
        telefono = request.POST['telefono']
        email = request.POST['email']
        fecha_registro = request.POST['fecha_registro']
        fecha_nacimiento = request.POST['fecha_nacimiento']
        comentarios = request.POST['comentarios']

        Cliente.objects.create(
            nombre=nombre,
            direccion=direccion,
            telefono=telefono,
            email=email,
            fecha_registro=fecha_registro,
            fecha_nacimiento=fecha_nacimiento,
            comentarios=comentarios
        )
        return redirect('ver_cliente')
    return render(request, 'clientes/agregar_cliente.html')

# Ver clientes
def ver_cliente(request):
    clientes = Cliente.objects.all()
    return render(request, 'clientes/ver_cliente.html', {'clientes': clientes})

# Actualizar cliente
def actualizar_cliente(request, id):
    cliente = get_object_or_404(Cliente, id_cliente=id)
    return render(request, 'clientes/actualizar_cliente.html', {'cliente': cliente})

def realizar_actualizacion_cliente(request, id):
    cliente = get_object_or_404(Cliente, id_cliente=id)
    if request.method == 'POST':
        cliente.nombre = request.POST['nombre']
        cliente.direccion = request.POST['direccion']
        cliente.telefono = request.POST['telefono']
        cliente.email = request.POST['email']
        cliente.fecha_registro = request.POST['fecha_registro']
        cliente.fecha_nacimiento = request.POST['fecha_nacimiento']
        cliente.comentarios = request.POST['comentarios']
        cliente.save()
        return redirect('ver_cliente')
    return redirect('ver_cliente')

# Borrar cliente
def borrar_cliente(request, id):
    cliente = get_object_or_404(Cliente, id_cliente=id)
    cliente.delete()
    return redirect('ver_cliente')
```

---

## 🎨 **TEMPLATES Y DISEÑO**

📂 Estructura:

```
app_Entrenamiento_Canino/
├── templates/
│   ├── base.html
│   ├── navbar.html
│   ├── footer.html
│   ├── inicio.html
│   └── clientes/
│       ├── agregar_cliente.html
│       ├── ver_cliente.html
│       ├── actualizar_cliente.html
│       └── borrar_cliente.html
```

---

### 🧱 base.html

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Entrenamiento Canino</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">

    {% include 'navbar.html' %}

    <div class="container my-4">
        {% block content %}{% endblock %}
    </div>

    {% include 'footer.html' %}

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

---

### 🧭 navbar.html

```html
<nav class="navbar navbar-expand-lg navbar-dark bg-primary">
  <div class="container-fluid">
    <a class="navbar-brand" href="{% url 'inicio' %}">🐶 Sistema de Entrenamiento Canino</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
      <span class="navbar-toggler-icon"></span>
    </button>

    <div class="collapse navbar-collapse" id="navbarNav">
      <ul class="navbar-nav ms-auto">
        <li class="nav-item"><a class="nav-link" href="{% url 'inicio' %}">🏠 Inicio</a></li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown">👤 Clientes</a>
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

---

### 🦴 footer.html

```html
<footer class="bg-dark text-white text-center py-3 fixed-bottom">
  <p class="mb-0">
    © <script>document.write(new Date().getFullYear())</script> | 
    Creado por Técnico Hernández Sánchez María Rene, CBTIS 128
  </p>
</footer>
```

---

### 🏠 inicio.html

```html
{% extends 'base.html' %}

{% block content %}
<div class="text-center">
    <h1 class="mb-4 text-primary">Bienvenido al Sistema de Entrenamiento Canino</h1>
    <p class="lead">Administra fácilmente tus clientes, perros y entrenamientos.</p>
    <img src="https://cdn.pixabay.com/photo/2016/02/19/10/00/dog-1209621_1280.jpg" class="img-fluid rounded shadow" width="60%">
</div>
{% endblock %}
```

---

### 📋 clientes/agregar_cliente.html

```html
{% extends 'base.html' %}
{% block content %}
<h2 class="text-center text-success">Agregar Cliente</h2>
<form method="POST" class="card p-4 shadow">
    {% csrf_token %}
    <div class="row">
        <div class="col-md-6 mb-3">
            <label>Nombre:</label>
            <input type="text" name="nombre" class="form-control" required>
        </div>
        <div class="col-md-6 mb-3">
            <label>Dirección:</label>
            <input type="text" name="direccion" class="form-control">
        </div>
        <div class="col-md-6 mb-3">
            <label>Teléfono:</label>
            <input type="text" name="telefono" class="form-control">
        </div>
        <div class="col-md-6 mb-3">
            <label>Email:</label>
            <input type="email" name="email" class="form-control">
        </div>
        <div class="col-md-6 mb-3">
            <label>Fecha Registro:</label>
            <input type="date" name="fecha_registro" class="form-control">
        </div>
        <div class="col-md-6 mb-3">
            <label>Fecha Nacimiento:</label>
            <input type="date" name="fecha_nacimiento" class="form-control">
        </div>
        <div class="mb-3">
            <label>Comentarios:</label>
            <input type="text" name="comentarios" class="form-control">
        </div>
    </div>
    <button type="submit" class="btn btn-success w-100">Guardar</button>
</form>
{% endblock %}
```

---

### 📄 clientes/ver_cliente.html

```html
{% extends 'base.html' %}
{% block content %}
<h2 class="text-center text-primary mb-4">Lista de Clientes</h2>
<table class="table table-striped table-bordered shadow">
    <thead class="table-primary">
        <tr>
            <th>Nombre</th>
            <th>Teléfono</th>
            <th>Email</th>
            <th>Acciones</th>
        </tr>
    </thead>
    <tbody>
        {% for c in clientes %}
        <tr>
            <td>{{ c.nombre }}</td>
            <td>{{ c.telefono }}</td>
            <td>{{ c.email }}</td>
            <td>
                <a href="{% url 'actualizar_cliente' c.id_cliente %}" class="btn btn-warning btn-sm">Editar</a>
                <a href="{% url 'borrar_cliente' c.id_cliente %}" class="btn btn-danger btn-sm">Borrar</a>
            </td>
        </tr>
        {% endfor %}
    </tbody>
</table>
{% endblock %}
```

---

### ✏️ clientes/actualizar_cliente.html

```html
{% extends 'base.html' %}
{% block content %}
<h2 class="text-center text-warning">Actualizar Cliente</h2>
<form method="POST" action="{% url 'realizar_actualizacion_cliente' cliente.id_cliente %}" class="card p-4 shadow">
    {% csrf_token %}
    <input type="text" name="nombre" value="{{ cliente.nombre }}" class="form-control mb-2">
    <input type="text" name="direccion" value="{{ cliente.direccion }}" class="form-control mb-2">
    <input type="text" name="telefono" value="{{ cliente.telefono }}" class="form-control mb-2">
    <input type="email" name="email" value="{{ cliente.email }}" class="form-control mb-2">
    <input type="date" name="fecha_registro" value="{{ cliente.fecha_registro|date:'Y-m-d' }}" class="form-control mb-2">
    <input type="date" name="fecha_nacimiento" value="{{ cliente.fecha_nacimiento|date:'Y-m-d' }}" class="form-control mb-2">
    <input type="text" name="comentarios" value="{{ cliente.comentarios }}" class="form-control mb-3">
    <button type="submit" class="btn btn-warning w-100">Actualizar</button>
</form>
{% endblock %}
```

---

## 🚀 Ejecutar el servidor

```bash
python manage.py runserver 8019
```

Abrir en el navegador:

```
http://127.0.0.1:8019/
```

---


