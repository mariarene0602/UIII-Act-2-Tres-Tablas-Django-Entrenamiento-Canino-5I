# PROYECTO ENTRENAMIENTO CANINO - CONFIGURACIÓN COMPLETA

## 1. ESTRUCTURA DE CARPETAS Y ARCHIVOS

```bash
UIII_Entrenamiento_Canino_0602/
├── .venv/
├── backend_Entrenamiento_Canino/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── app_Entrenamiento_Canino/
│   ├── migrations/
│   ├── templates/
│   │   ├── clientes/
│   │   │   ├── agregar_cliente.html
│   │   │   ├── ver_cliente.html
│   │   │   ├── actualizar_cliente.html
│   │   │   └── borrar_cliente.html
│   │   ├── base.html
│   │   ├── header.html
│   │   ├── navbar.html
│   │   ├── footer.html
│   │   └── inicio.html
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── tests.py
│   ├── urls.py
│   └── views.py
├── manage.py
└── db.sqlite3
```

## 2. PROCEDIMIENTOS INICIALES

```bash
# Crear carpeta y entrar
mkdir UIII_Entrenamiento_Canino_0602
cd UIII_Entrenamiento_Canino_0602

# Crear y activar entorno virtual
python -m venv .venv
.venv\Scripts\activate  # Windows
# source .venv/bin/activate  # Mac/Linux

# Instalar Django
pip install django

# Crear proyecto
django-admin startproject backend_Entrenamiento_Canino .

# Crear aplicación
python manage.py startapp app_Entrenamiento_Canino
```

## 3. ARCHIVOS DE CONFIGURACIÓN

**backend_Entrenamiento_Canino/settings.py**
```python
"""
Django settings for backend_Entrenamiento_Canino project.
"""

from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent.parent

SECRET_KEY = 'django-insecure-your-secret-key-here'

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
        'DIRS': [],
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
DEFAULT_AUTO_FIELD = 'django.db.models.BigAutoField'
```

**backend_Entrenamiento_Canino/urls.py**
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Entrenamiento_Canino.urls')),
]
```

## 4. MODELO

**app_Entrenamiento_Canino/models.py**
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

## 5. VISTAS

**app_Entrenamiento_Canino/views.py**
```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Cliente

def inicio_entrenamiento_canino(request):
    return render(request, 'inicio.html')

def agregar_cliente(request):
    if request.method == 'POST':
        nombre = request.POST['nombre']
        direccion = request.POST['direccion']
        telefono = request.POST['telefono']
        email = request.POST['email']
        fecha_registro = request.POST['fecha_registro']
        fecha_nacimiento = request.POST['fecha_nacimiento']
        comentarios = request.POST.get('comentarios', '')
        
        cliente = Cliente(
            nombre=nombre,
            direccion=direccion,
            telefono=telefono,
            email=email,
            fecha_registro=fecha_registro,
            fecha_nacimiento=fecha_nacimiento,
            comentarios=comentarios
        )
        cliente.save()
        return redirect('ver_cliente')
    
    return render(request, 'clientes/agregar_cliente.html')

def ver_cliente(request):
    clientes = Cliente.objects.all()
    return render(request, 'clientes/ver_cliente.html', {'clientes': clientes})

def actualizar_cliente(request, id_cliente):
    cliente = get_object_or_404(Cliente, id_cliente=id_cliente)
    return render(request, 'clientes/actualizar_cliente.html', {'cliente': cliente})

def realizar_actualizacion_cliente(request, id_cliente):
    if request.method == 'POST':
        cliente = get_object_or_404(Cliente, id_cliente=id_cliente)
        cliente.nombre = request.POST['nombre']
        cliente.direccion = request.POST['direccion']
        cliente.telefono = request.POST['telefono']
        cliente.email = request.POST['email']
        cliente.fecha_registro = request.POST['fecha_registro']
        cliente.fecha_nacimiento = request.POST['fecha_nacimiento']
        cliente.comentarios = request.POST.get('comentarios', '')
        cliente.save()
        return redirect('ver_cliente')
    
    return redirect('ver_cliente')

def borrar_cliente(request, id_cliente):
    cliente = get_object_or_404(Cliente, id_cliente=id_cliente)
    if request.method == 'POST':
        cliente.delete()
        return redirect('ver_cliente')
    
    return render(request, 'clientes/borrar_cliente.html', {'cliente': cliente})
```

## 6. URLs DE LA APLICACIÓN

**app_Entrenamiento_Canino/urls.py**
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_entrenamiento_canino, name='inicio'),
    path('agregar-cliente/', views.agregar_cliente, name='agregar_cliente'),
    path('ver-cliente/', views.ver_cliente, name='ver_cliente'),
    path('actualizar-cliente/<int:id_cliente>/', views.actualizar_cliente, name='actualizar_cliente'),
    path('realizar-actualizacion-cliente/<int:id_cliente>/', views.realizar_actualizacion_cliente, name='realizar_actualizacion_cliente'),
    path('borrar-cliente/<int:id_cliente>/', views.borrar_cliente, name='borrar_cliente'),
]
```

## 7. ADMIN

**app_Entrenamiento_Canino/admin.py**
```python
from django.contrib import admin
from .models import Cliente

@admin.register(Cliente)
class ClienteAdmin(admin.ModelAdmin):
    list_display = ('id_cliente', 'nombre', 'email', 'telefono', 'fecha_registro')
    list_filter = ('fecha_registro',)
    search_fields = ('nombre', 'email', 'telefono')
```

## 8. TEMPLATES BASE

**app_Entrenamiento_Canino/templates/base.html**
```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema de Administración Entrenamiento Canino</title>
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body {
            background-color: #f8f9fa;
            padding-top: 56px;
        }
        .sidebar {
            background-color: #2c3e50;
            min-height: calc(100vh - 56px);
        }
        .main-content {
            background-color: #ecf0f1;
            min-height: calc(100vh - 56px);
            padding: 20px;
        }
        .footer {
            background-color: #34495e;
            color: white;
            padding: 10px 0;
        }
    </style>
</head>
<body>
    {% include 'header.html' %}
    
    <div class="container-fluid">
        <div class="row">
            {% include 'navbar.html' %}
            
            <main class="col-md-9 ms-sm-auto col-lg-10 px-md-4 main-content">
                {% block content %}
                {% endblock %}
            </main>
        </div>
    </div>
    
    {% include 'footer.html' %}

    <!-- Bootstrap JS -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**app_Entrenamiento_Canino/templates/header.html**
```html
<header>
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark fixed-top">
        <div class="container-fluid">
            <a class="navbar-brand" href="{% url 'inicio' %}">
                🐕 Sistema de Administración Entrenamiento Canino
            </a>
        </div>
    </nav>
</header>
```

**app_Entrenamiento_Canino/templates/navbar.html**
```html
<nav class="col-md-3 col-lg-2 d-md-block sidebar collapse">
    <div class="position-sticky pt-3">
        <ul class="nav flex-column">
            <li class="nav-item">
                <a class="nav-link text-white" href="{% url 'inicio' %}">
                    🏠 Inicio
                </a>
            </li>
            
            <li class="nav-item dropdown">
                <a class="nav-link dropdown-toggle text-white" href="#" data-bs-toggle="dropdown">
                    📁 Categoria
                </a>
                <ul class="dropdown-menu">
                    <li><a class="dropdown-item" href="{% url 'agregar_cliente' %}">Agregar Cliente</a></li>
                    <li><a class="dropdown-item" href="{% url 'ver_cliente' %}">Ver Cliente</a></li>
                </ul>
            </li>
            
            <li class="nav-item dropdown">
                <a class="nav-link dropdown-toggle text-white" href="#" data-bs-toggle="dropdown">
                    🐶 Perros
                </a>
                <ul class="dropdown-menu">
                    <li><a class="dropdown-item" href="#">Agregar perros</a></li>
                    <li><a class="dropdown-item" href="#">Ver perros</a></li>
                    <li><a class="dropdown-item" href="#">Actualizar perros</a></li>
                    <li><a class="dropdown-item" href="#">Borrar perros</a></li>
                </ul>
            </li>
            
            <li class="nav-item dropdown">
                <a class="nav-link dropdown-toggle text-white" href="#" data-bs-toggle="dropdown">
                    🎾 Entrenamientos
                </a>
                <ul class="dropdown-menu">
                    <li><a class="dropdown-item" href="#">Agregar entrenamientos</a></li>
                    <li><a class="dropdown-item" href="#">Ver entrenamientos</a></li>
                    <li><a class="dropdown-item" href="#">Actualizar entrenamientos</a></li>
                    <li><a class="dropdown-item" href="#">Borrar entrenamientos</a></li>
                </ul>
            </li>
        </ul>
    </div>
</nav>
```

**app_Entrenamiento_Canino/templates/footer.html**
```html
<footer class="footer mt-auto py-3 bg-dark text-white">
    <div class="container text-center">
        <span>
            &copy; {% now "Y" %} - Derechos reservados | 
            Fecha del sistema: {% now "d/m/Y" %} | 
            Creado por Técnico Hernández Sánchez María Rene, Cbtis 128
        </span>
    </div>
</footer>
```

**app_Entrenamiento_Canino/templates/inicio.html**
```html
{% extends 'base.html' %}

{% block content %}
<div class="row">
    <div class="col-12">
        <h1 class="display-4 text-center mb-4">Bienvenido al Sistema de Entrenamiento Canino</h1>
        
        <div class="card shadow-sm">
            <div class="card-body">
                <h5 class="card-title">Sistema de Gestión Integral</h5>
                <p class="card-text">
                    Este sistema permite administrar clientes, perros y entrenamientos de manera eficiente 
                    y organizada. Utilice el menú lateral para navegar entre las diferentes secciones.
                </p>
                
                <div class="text-center mt-4">
                    <img src="https://images.unsplash.com/photo-1554456854-55a089fd4cb2?ixlib=rb-4.0.3&auto=format&fit=crop&w=1170&q=80" 
                         alt="Entrenamiento Canino" 
                         class="img-fluid rounded shadow" 
                         style="max-height: 400px;">
                </div>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

## 9. TEMPLATES DE CLIENTES

**app_Entrenamiento_Canino/templates/clientes/agregar_cliente.html**
```html
{% extends 'base.html' %}

{% block content %}
<div class="row">
    <div class="col-12">
        <h2>Agregar Nuevo Cliente</h2>
        
        <div class="card shadow-sm">
            <div class="card-body">
                <form method="POST">
                    {% csrf_token %}
                    <div class="row mb-3">
                        <div class="col-md-6">
                            <label class="form-label">Nombre:</label>
                            <input type="text" class="form-control" name="nombre" required>
                        </div>
                        <div class="col-md-6">
                            <label class="form-label">Teléfono:</label>
                            <input type="text" class="form-control" name="telefono" required>
                        </div>
                    </div>
                    
                    <div class="row mb-3">
                        <div class="col-md-6">
                            <label class="form-label">Email:</label>
                            <input type="email" class="form-control" name="email" required>
                        </div>
                        <div class="col-md-6">
                            <label class="form-label">Dirección:</label>
                            <input type="text" class="form-control" name="direccion" required>
                        </div>
                    </div>
                    
                    <div class="row mb-3">
                        <div class="col-md-6">
                            <label class="form-label">Fecha de Registro:</label>
                            <input type="date" class="form-control" name="fecha_registro" required>
                        </div>
                        <div class="col-md-6">
                            <label class="form-label">Fecha de Nacimiento:</label>
                            <input type="date" class="form-control" name="fecha_nacimiento" required>
                        </div>
                    </div>
                    
                    <div class="mb-3">
                        <label class="form-label">Comentarios:</label>
                        <textarea class="form-control" name="comentarios" rows="3"></textarea>
                    </div>
                    
                    <button type="submit" class="btn btn-success">Guardar Cliente</button>
                    <a href="{% url 'ver_cliente' %}" class="btn btn-secondary">Cancelar</a>
                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

**app_Entrenamiento_Canino/templates/clientes/ver_cliente.html**
```html
{% extends 'base.html' %}

{% block content %}
<div class="row">
    <div class="col-12">
        <h2>Lista de Clientes</h2>
        
        <div class="card shadow-sm">
            <div class="card-body">
                <div class="table-responsive">
                    <table class="table table-striped table-hover">
                        <thead class="table-dark">
                            <tr>
                                <th>ID</th>
                                <th>Nombre</th>
                                <th>Email</th>
                                <th>Teléfono</th>
                                <th>Fecha Registro</th>
                                <th>Acciones</th>
                            </tr>
                        </thead>
                        <tbody>
                            {% for cliente in clientes %}
                            <tr>
                                <td>{{ cliente.id_cliente }}</td>
                                <td>{{ cliente.nombre }}</td>
                                <td>{{ cliente.email }}</td>
                                <td>{{ cliente.telefono }}</td>
                                <td>{{ cliente.fecha_registro }}</td>
                                <td>
                                    <a href="{% url 'actualizar_cliente' cliente.id_cliente %}" class="btn btn-warning btn-sm">Editar</a>
                                    <a href="{% url 'borrar_cliente' cliente.id_cliente %}" class="btn btn-danger btn-sm">Borrar</a>
                                </td>
                            </tr>
                            {% empty %}
                            <tr>
                                <td colspan="6" class="text-center">No hay clientes registrados</td>
                            </tr>
                            {% endfor %}
                        </tbody>
                    </table>
                </div>
                
                <a href="{% url 'agregar_cliente' %}" class="btn btn-primary">Agregar Nuevo Cliente</a>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

**app_Entrenamiento_Canino/templates/clientes/actualizar_cliente.html**
```html
{% extends 'base.html' %}

{% block content %}
<div class="row">
    <div class="col-12">
        <h2>Actualizar Cliente</h2>
        
        <div class="card shadow-sm">
            <div class="card-body">
                <form method="POST" action="{% url 'realizar_actualizacion_cliente' cliente.id_cliente %}">
                    {% csrf_token %}
                    <div class="row mb-3">
                        <div class="col-md-6">
                            <label class="form-label">Nombre:</label>
                            <input type="text" class="form-control" name="nombre" value="{{ cliente.nombre }}" required>
                        </div>
                        <div class="col-md-6">
                            <label class="form-label">Teléfono:</label>
                            <input type="text" class="form-control" name="telefono" value="{{ cliente.telefono }}" required>
                        </div>
                    </div>
                    
                    <div class="row mb-3">
                        <div class="col-md-6">
                            <label class="form-label">Email:</label>
                            <input type="email" class="form-control" name="email" value="{{ cliente.email }}" required>
                        </div>
                        <div class="col-md-6">
                            <label class="form-label">Dirección:</label>
                            <input type="text" class="form-control" name="direccion" value="{{ cliente.direccion }}" required>
                        </div>
                    </div>
                    
                    <div class="row mb-3">
                        <div class="col-md-6">
                            <label class="form-label">Fecha de Registro:</label>
                            <input type="date" class="form-control" name="fecha_registro" value="{{ cliente.fecha_registro|date:'Y-m-d' }}" required>
                        </div>
                        <div class="col-md-6">
                            <label class="form-label">Fecha de Nacimiento:</label>
                            <input type="date" class="form-control" name="fecha_nacimiento" value="{{ cliente.fecha_nacimiento|date:'Y-m-d' }}" required>
                        </div>
                    </div>
                    
                    <div class="mb-3">
                        <label class="form-label">Comentarios:</label>
                        <textarea class="form-control" name="comentarios" rows="3">{{ cliente.comentarios }}</textarea>
                    </div>
                    
                    <button type="submit" class="btn btn-warning">Actualizar Cliente</button>
                    <a href="{% url 'ver_cliente' %}" class="btn btn-secondary">Cancelar</a>
                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

**app_Entrenamiento_Canino/templates/clientes/borrar_cliente.html**
```html
{% extends 'base.html' %}

{% block content %}
<div class="row">
    <div class="col-12">
        <h2>Borrar Cliente</h2>
        
        <div class="card shadow-sm">
            <div class="card-body">
                <div class="alert alert-danger">
                    <h4>¿Está seguro que desea borrar al cliente?</h4>
                    <p><strong>Nombre:</strong> {{ cliente.nombre }}</p>
                    <p><strong>Email:</strong> {{ cliente.email }}</p>
                    <p><strong>Teléfono:</strong> {{ cliente.telefono }}</p>
                </div>
                
                <form method="POST">
                    {% csrf_token %}
                    <button type="submit" class="btn btn-danger">Sí, Borrar Cliente</button>
                    <a href="{% url 'ver_cliente' %}" class="btn btn-secondary">Cancelar</a>
                </form>
            </div>
        </div>
    </div>
</div>
{% endblock %}
```

## 10. EJECUCIÓN FINAL

```bash
# Realizar migraciones
python manage.py makemigrations
python manage.py migrate

# Crear superusuario (opcional)
python manage.py createsuperuser

# Ejecutar servidor en puerto 8019
python manage.py runserver 8019
```

**URLs disponibles:**
- http://localhost:8019/ (Inicio)
- http://localhost:8019/agregar-cliente/ (Agregar cliente)
- http://localhost:8019/ver-cliente/ (Ver clientes)
- http://localhost:8019/admin/ (Admin Django)

El proyecto está completamente funcional con colores suaves y diseño moderno, siguiendo exactamente las especificaciones solicitadas.
