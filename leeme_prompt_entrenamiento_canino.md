# Estructura Completa de Carpetas y Archivos

```
UIII_Entrenamiento_Canino_0602/
│
├── .venv/                          # Entorno virtual (se crea automáticamente)
│
├── backend_Entrenamiento_Canino/   # Proyecto Django
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   ├── asgi.py
│   └── wsgi.py
│
├── app_Entrenamiento_Canino/       # Aplicación principal
│   ├── migrations/
│   │   └── __init__.py
│   ├── templates/
│   │   ├── app_Entrenamiento_Canino/
│   │   │   └── (vacío inicialmente)
│   │   │
│   │   ├── clientes/
│   │   │   ├── agregar_cliente.html
│   │   │   ├── ver_cliente.html
│   │   │   ├── actualizar_cliente.html
│   │   │   └── borrar_cliente.html
│   │   │
│   │   ├── base.html
│   │   ├── header.html
│   │   ├── navbar.html
│   │   ├── footer.html
│   │   └── inicio.html
│   │
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── tests.py
│   ├── views.py
│   └── urls.py                     # Archivo a crear
│
├── static/                         # Carpeta para archivos estáticos
│   ├── css/
│   ├── js/
│   └── images/
│
├── manage.py
├── requirements.txt
└── db.sqlite3                      # Base de datos (se crea automáticamente)
```

## Contenido de cada archivo:

### 1. backend_Entrenamiento_Canino/settings.py
```python
"""
Django settings for backend_Entrenamiento_Canino project.
"""

import os
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent.parent

SECRET_KEY = 'django-insecure-clave-secreta-temporal'

DEBUG = True

ALLOWED_HOSTS = []

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app_Entrenamiento_Canino',
]

MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

ROOT_URLCONF = 'backend_Entrenamiento_Canino.urls'

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'app_Entrenamiento_Canino', 'templates')],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]

WSGI_APPLICATION = 'backend_Entrenamiento_Canino.wsgi.application'

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}

AUTH_PASSWORD_VALIDATORS = [
    {
        'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator',
    },
]

LANGUAGE_CODE = 'es-mx'
TIME_ZONE = 'America/Mexico_City'
USE_I18N = True
USE_TZ = True

STATIC_URL = 'static/'
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static'),
]

DEFAULT_AUTO_FIELD = 'django.db.models.BigAutoField'
```

### 2. backend_Entrenamiento_Canino/urls.py
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Entrenamiento_Canino.urls')),
]
```

### 3. app_Entrenamiento_Canino/models.py
```python
from django.db import models

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

### 4. app_Entrenamiento_Canino/views.py
```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Cliente

def inicio_entrenamiento_canino(request):
    return render(request, 'inicio.html')

def agregar_cliente(request):
    if request.method == 'POST':
        cliente = Cliente(
            nombre=request.POST['nombre'],
            direccion=request.POST['direccion'],
            telefono=request.POST['telefono'],
            email=request.POST['email'],
            fecha_registro=request.POST['fecha_registro'],
            fecha_nacimiento=request.POST['fecha_nacimiento'],
            comentarios=request.POST.get('comentarios', '')
        )
        cliente.save()
        return redirect('ver_clientes')
    return render(request, 'clientes/agregar_cliente.html')

def ver_clientes(request):
    clientes = Cliente.objects.all()
    return render(request, 'clientes/ver_cliente.html', {'clientes': clientes})

def actualizar_cliente(request, id):
    cliente = get_object_or_404(Cliente, id_cliente=id)
    return render(request, 'clientes/actualizar_cliente.html', {'cliente': cliente})

def realizar_actualizacion_cliente(request, id):
    if request.method == 'POST':
        cliente = get_object_or_404(Cliente, id_cliente=id)
        cliente.nombre = request.POST['nombre']
        cliente.direccion = request.POST['direccion']
        cliente.telefono = request.POST['telefono']
        cliente.email = request.POST['email']
        cliente.fecha_registro = request.POST['fecha_registro']
        cliente.fecha_nacimiento = request.POST['fecha_nacimiento']
        cliente.comentarios = request.POST.get('comentarios', '')
        cliente.save()
        return redirect('ver_clientes')
    return redirect('ver_clientes')

def borrar_cliente(request, id):
    cliente = get_object_or_404(Cliente, id_cliente=id)
    if request.method == 'POST':
        cliente.delete()
        return redirect('ver_clientes')
    return render(request, 'clientes/borrar_cliente.html', {'cliente': cliente})
```

### 5. app_Entrenamiento_Canino/urls.py
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_entrenamiento_canino, name='inicio'),
    path('agregar_cliente/', views.agregar_cliente, name='agregar_cliente'),
    path('ver_clientes/', views.ver_clientes, name='ver_clientes'),
    path('actualizar_cliente/<int:id>/', views.actualizar_cliente, name='actualizar_cliente'),
    path('realizar_actualizacion_cliente/<int:id>/', views.realizar_actualizacion_cliente, name='realizar_actualizacion_cliente'),
    path('borrar_cliente/<int:id>/', views.borrar_cliente, name='borrar_cliente'),
]
```

### 6. app_Entrenamiento_Canino/admin.py
```python
from django.contrib import admin
from .models import Cliente

@admin.register(Cliente)
class ClienteAdmin(admin.ModelAdmin):
    list_display = ('id_cliente', 'nombre', 'email', 'telefono', 'fecha_registro')
    search_fields = ('nombre', 'email')
    list_filter = ('fecha_registro',)
```

### 7. app_Entrenamiento_Canino/templates/base.html
```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema de Entrenamiento Canino</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body { background-color: #f8f9fa; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        .navbar-brand { font-weight: bold; color: #2c3e50 !important; }
        .main-content { padding: 20px; min-height: 80vh; }
        .card { border: none; border-radius: 10px; box-shadow: 0 4px 6px rgba(0,0,0,0.1); }
        .btn-primary { background-color: #3498db; border: none; }
        .btn-primary:hover { background-color: #2980b9; }
    </style>
</head>
<body>
    {% include 'navbar.html' %}
    <div class="container-fluid main-content">
        {% block content %}
        {% endblock %}
    </div>
    {% include 'footer.html' %}
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

### 8. app_Entrenamiento_Canino/templates/navbar.html
```html
<nav class="navbar navbar-expand-lg navbar-dark" style="background-color: #2c3e50;">
    <div class="container-fluid">
        <a class="navbar-brand" href="{% url 'inicio' %}">
            🐕 Sistema de Administración Entrenamiento Canino
        </a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNav">
            <ul class="navbar-nav me-auto">
                <li class="nav-item">
                    <a class="nav-link" href="{% url 'inicio' %}">🏠 Inicio</a>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="categoriaDropdown" role="button" data-bs-toggle="dropdown">
                        📁 Clientes
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="{% url 'agregar_cliente' %}">Agregar Cliente</a></li>
                        <li><a class="dropdown-item" href="{% url 'ver_clientes' %}">Ver Clientes</a></li>
                    </ul>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="perrosDropdown" role="button" data-bs-toggle="dropdown">
                        🐶 Perros
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="#">Agregar Perros</a></li>
                        <li><a class="dropdown-item" href="#">Ver Perros</a></li>
                    </ul>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="entrenamientosDropdown" role="button" data-bs-toggle="dropdown">
                        🎾 Entrenamientos
                    </a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="#">Agregar Entrenamientos</a></li>
                        <li><a class="dropdown-item" href="#">Ver Entrenamientos</a></li>
                    </ul>
                </li>
            </ul>
        </div>
    </div>
</nav>
```

### 9. app_Entrenamiento_Canino/templates/footer.html
```html
<footer class="bg-dark text-white text-center py-3 mt-5">
    <div class="container">
        <p class="mb-0">
            &copy; {% now "Y" %} Derechos reservados | 
            Creado por Técnico Hernández Sánchez María Rene, Cbtis 128 |
            Fecha: {% now "d/m/Y" %}
        </p>
    </div>
</footer>
```

### 10. app_Entrenamiento_Canino/templates/inicio.html
```html
{% extends 'base.html' %}

{% block content %}
<div class="row">
    <div class="col-12">
        <div class="card">
            <div class="card-body">
                <h1 class="card-title text-center mb-4">Bienvenido al Sistema de Entrenamiento Canino</h1>
                <div class="row">
                    <div class="col-md-6">
                        <p class="lead">Sistema especializado para la gestión de clientes, perros y entrenamientos.</p>
                        <ul>
                            <li>Gestión completa de clientes</li>
                            <li>Registro de perros</li>
                            <li>Seguimiento de entrenamientos</li>
                            <li>Reportes y estadísticas</li>
                        </ul>
                    </div>
                    <div class="col-md-6">
                        <img src="https://images.unsplash.com/photo-1554456854-55a089fd4cb2?ixlib=rb-4.0.3&auto=format&fit=crop&w=1170&q=80" 
                             alt="Entrenamiento Canino" class="img-fluid rounded">
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

### 11. app_Entrenamiento_Canino/templates/clientes/agregar_cliente.html
```html
{% extends 'base.html' %}

{% block content %}
<div class="row justify-content-center">
    <div class="col-md-8">
        <div class="card">
            <div class="card-header bg-primary text-white">
                <h4 class="mb-0">Agregar Nuevo Cliente</h4>
            </div>
            <div class="card-body">
                <form method="POST">
                    {% csrf_token %}
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Nombre:</label>
                            <input type="text" class="form-control" name="nombre" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Teléfono:</label>
                            <input type="text" class="form-control" name="telefono" required>
                        </div>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Dirección:</label>
                        <input type="text" class="form-control" name="direccion" required>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Email:</label>
                        <input type="email" class="form-control" name="email" required>
                    </div>
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Fecha de Registro:</label>
                            <input type="date" class="form-control" name="fecha_registro" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Fecha de Nacimiento:</label>
                            <input type="date" class="form-control" name="fecha_nacimiento" required>
                        </div>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Comentarios:</label>
                        <textarea class="form-control" name="comentarios" rows="3"></textarea>
                    </div>
                    <div class="d-grid gap-2">
                        <button type="submit" class="btn btn-primary">Guardar Cliente</button>
                        <a href="{% url 'ver_clientes' %}" class="btn btn-secondary">Cancelar</a>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

### 12. app_Entrenamiento_Canino/templates/clientes/ver_cliente.html
```html
{% extends 'base.html' %}

{% block content %}
<div class="row">
    <div class="col-12">
        <div class="card">
            <div class="card-header bg-success text-white d-flex justify-content-between align-items-center">
                <h4 class="mb-0">Lista de Clientes</h4>
                <a href="{% url 'agregar_cliente' %}" class="btn btn-light">➕ Agregar Cliente</a>
            </div>
            <div class="card-body">
                <div class="table-responsive">
                    <table class="table table-striped table-hover">
                        <thead class="table-dark">
                            <tr>
                                <th>ID</th>
                                <th>Nombre</th>
                                <th>Teléfono</th>
                                <th>Email</th>
                                <th>Dirección</th>
                                <th>Fecha Registro</th>
                                <th>Acciones</th>
                            </tr>
                        </thead>
                        <tbody>
                            {% for cliente in clientes %}
                            <tr>
                                <td>{{ cliente.id_cliente }}</td>
                                <td>{{ cliente.nombre }}</td>
                                <td>{{ cliente.telefono }}</td>
                                <td>{{ cliente.email }}</td>
                                <td>{{ cliente.direccion }}</td>
                                <td>{{ cliente.fecha_registro }}</td>
                                <td>
                                    <a href="{% url 'actualizar_cliente' cliente.id_cliente %}" class="btn btn-warning btn-sm">✏️ Editar</a>
                                    <a href="{% url 'borrar_cliente' cliente.id_cliente %}" class="btn btn-danger btn-sm">🗑️ Borrar</a>
                                </td>
                            </tr>
                            {% empty %}
                            <tr>
                                <td colspan="7" class="text-center">No hay clientes registrados</td>
                            </tr>
                            {% endfor %}
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

### 13. app_Entrenamiento_Canino/templates/clientes/actualizar_cliente.html
```html
{% extends 'base.html' %}

{% block content %}
<div class="row justify-content-center">
    <div class="col-md-8">
        <div class="card">
            <div class="card-header bg-warning text-dark">
                <h4 class="mb-0">Actualizar Cliente: {{ cliente.nombre }}</h4>
            </div>
            <div class="card-body">
                <form method="POST" action="{% url 'realizar_actualizacion_cliente' cliente.id_cliente %}">
                    {% csrf_token %}
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Nombre:</label>
                            <input type="text" class="form-control" name="nombre" value="{{ cliente.nombre }}" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Teléfono:</label>
                            <input type="text" class="form-control" name="telefono" value="{{ cliente.telefono }}" required>
                        </div>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Dirección:</label>
                        <input type="text" class="form-control" name="direccion" value="{{ cliente.direccion }}" required>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Email:</label>
                            <input type="email" class="form-control" name="email" value="{{ cliente.email }}" required>
                    </div>
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Fecha de Registro:</label>
                            <input type="date" class="form-control" name="fecha_registro" value="{{ cliente.fecha_registro|date:'Y-m-d' }}" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Fecha de Nacimiento:</label>
                            <input type="date" class="form-control" name="fecha_nacimiento" value="{{ cliente.fecha_nacimiento|date:'Y-m-d' }}" required>
                        </div>
                    </div>
                    <div class="mb-3">
                        <label class="form-label">Comentarios:</label>
                        <textarea class="form-control" name="comentarios" rows="3">{{ cliente.comentarios }}</textarea>
                    </div>
                    <div class="d-grid gap-2">
                        <button type="submit" class="btn btn-warning">Actualizar Cliente</button>
                        <a href="{% url 'ver_clientes' %}" class="btn btn-secondary">Cancelar</a>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

### 14. app_Entrenamiento_Canino/templates/clientes/borrar_cliente.html
```html
{% extends 'base.html' %}

{% block content %}
<div class="row justify-content-center">
    <div class="col-md-6">
        <div class="card">
            <div class="card-header bg-danger text-white">
                <h4 class="mb-0">Confirmar Eliminación</h4>
            </div>
            <div class="card-body text-center">
                <h5>¿Estás seguro de que deseas eliminar al cliente?</h5>
                <p class="lead">{{ cliente.nombre }}</p>
                <p><strong>Email:</strong> {{ cliente.email }}</p>
                <p><strong>Teléfono:</strong> {{ cliente.telefono }}</p>
                
                <form method="POST">
                    {% csrf_token %}
                    <div class="d-grid gap-2 d-md-block">
                        <button type="submit" class="btn btn-danger">✅ Sí, Eliminar</button>
                        <a href="{% url 'ver_clientes' %}" class="btn btn-secondary">❌ Cancelar</a>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

### 15. manage.py
```python
#!/usr/bin/env python
"""Django's command-line utility for administrative tasks."""
import os
import sys

def main():
    """Run administrative tasks."""
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'backend_Entrenamiento_Canino.settings')
    try:
        from django.core.management import execute_from_command_line
    except ImportError as exc:
        raise ImportError(
            "Couldn't import Django. Are you sure it's installed and "
            "available on your PYTHONPATH environment variable? Did you "
            "forget to activate a virtual environment?"
        ) from exc
    execute_from_command_line(sys.argv)

if __name__ == '__main__':
    main()
```

## Comandos para ejecutar:

```bash
# Crear migraciones
python manage.py makemigrations

# Aplicar migraciones
python manage.py migrate

# Ejecutar servidor en puerto 8019
python manage.py runserver 8019
```
