Te guío paso a paso para crear el proyecto de Entrenamiento Canino:

## 1. Creación de la estructura del proyecto

```bash
# Crear carpeta del proyecto
mkdir UIII_Entrenamiento_Canino_0602
cd UIII_Entrenamiento_Canino_0602

# Abrir VS Code en esta carpeta
code .
```

## 2. Configuración del entorno virtual

En la terminal de VS Code:

```bash
# Crear entorno virtual
python -m venv .venv

# Activar entorno virtual
# Windows:
.venv\Scripts\activate
# Linux/Mac:
source .venv/bin/activate

# Instalar Django
pip install django
```

## 3. Creación del proyecto Django

```bash
# Crear proyecto
django-admin startproject backend_Entrenamiento_Canino .

# Crear aplicación
python manage.py startapp app_Entrenamiento_Canino
```

## 4. Configuración inicial

**backend_Entrenamiento_Canino/settings.py**:
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app_Entrenamiento_Canino',  # Agregar esta línea
]

# Agregar al final del archivo
STATICFILES_DIRS = [
    BASE_DIR / "static",
]
```

## 5. Modelo Cliente

**app_Entrenamiento_Canino/models.py**:
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

## 6. Vistas

**app_Entrenamiento_Canino/views.py**:
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

## 7. URLs de la aplicación

**app_Entrenamiento_Canino/urls.py**:
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

## 8. URLs principales

**backend_Entrenamiento_Canino/urls.py**:
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_Entrenamiento_Canino.urls')),
]
```

## 9. Templates

**app_Entrenamiento_Canino/templates/base.html**:
```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema de Entrenamiento Canino</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        body {
            background-color: #f8f9fa;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        .navbar {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        }
        .sidebar {
            background-color: #2c3e50;
            min-height: calc(100vh - 56px);
        }
        .sidebar .nav-link {
            color: #ecf0f1;
        }
        .sidebar .nav-link:hover {
            background-color: #34495e;
        }
        .main-content {
            background-color: #ffffff;
            border-radius: 10px;
            box-shadow: 0 0 20px rgba(0,0,0,0.1);
            margin: 20px 0;
            padding: 30px;
        }
        .footer {
            background-color: #2c3e50;
            color: white;
            position: fixed;
            bottom: 0;
            width: 100%;
        }
    </style>
</head>
<body>
    {% include 'header.html' %}
    {% include 'navbar.html' %}
    
    <div class="container-fluid">
        <div class="row">
            <div class="col-md-2 sidebar p-0">
                {% include 'sidebar.html' %}
            </div>
            <div class="col-md-10">
                <div class="main-content">
                    {% block content %}
                    {% endblock %}
                </div>
            </div>
        </div>
    </div>
    
    {% include 'footer.html' %}
    
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**app_Entrenamiento_Canino/templates/header.html**:
```html
<nav class="navbar navbar-expand-lg navbar-dark">
    <div class="container-fluid">
        <span class="navbar-brand">
            <i class="fas fa-paw me-2"></i>
            Sistema de Administración Entrenamiento Canino
        </span>
    </div>
</nav>
```

**app_Entrenamiento_Canino/templates/navbar.html**:
```html
<nav class="navbar navbar-expand-lg navbar-light bg-light">
    <div class="container-fluid">
        <div class="collapse navbar-collapse">
            <ul class="navbar-nav me-auto mb-2 mb-lg-0">
                <li class="nav-item">
                    <a class="nav-link" href="{% url 'inicio' %}">
                        <i class="fas fa-home me-1"></i>Inicio
                    </a>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="categoriaDropdown" role="button" data-bs-toggle="dropdown">
                        <i class="fas fa-users me-1"></i>Clientes
                    </a>
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

**app_Entrenamiento_Canino/templates/sidebar.html**:
```html
<nav class="nav flex-column p-3">
    <h6 class="text-white-50 small text-uppercase mb-3">Menú Principal</h6>
    
    <a class="nav-link mb-2" href="{% url 'inicio' %}">
        <i class="fas fa-home me-2"></i>Inicio
    </a>
    
    <div class="dropdown">
        <a class="nav-link dropdown-toggle mb-2" href="#" role="button" data-bs-toggle="dropdown">
            <i class="fas fa-users me-2"></i>Clientes
        </a>
        <ul class="dropdown-menu bg-dark">
            <li><a class="dropdown-item text-white" href="{% url 'agregar_cliente' %}">Agregar Cliente</a></li>
            <li><a class="dropdown-item text-white" href="{% url 'ver_cliente' %}">Ver Clientes</a></li>
        </ul>
    </div>
</nav>
```

**app_Entrenamiento_Canino/templates/footer.html**:
```html
<footer class="footer mt-auto py-3">
    <div class="container-fluid">
        <div class="row">
            <div class="col-md-6">
                <span>&copy; {% now "Y" %} Sistema de Entrenamiento Canino. Todos los derechos reservados.</span>
            </div>
            <div class="col-md-6 text-end">
                <span>Creado por Técnico Hernández Sánchez María Rene, CBTIS 128</span>
            </div>
        </div>
    </div>
</footer>
```

**app_Entrenamiento_Canino/templates/inicio.html**:
```html
{% extends 'base.html' %}

{% block content %}
<div class="row">
    <div class="col-md-6">
        <h1 class="display-4 text-primary">Bienvenido al Sistema de Entrenamiento Canino</h1>
        <p class="lead">Sistema especializado para la administración de clientes, perros y entrenamientos caninos.</p>
        
        <div class="mt-5">
            <h3>Funcionalidades principales:</h3>
            <ul class="list-group">
                <li class="list-group-item">Gestión de Clientes</li>
                <li class="list-group-item">Registro de Perros</li>
                <li class="list-group-item">Control de Entrenamientos</li>
                <li class="list-group-item">Seguimiento de Progreso</li>
            </ul>
        </div>
    </div>
    <div class="col-md-6">
        <img src="https://images.unsplash.com/photo-1554456854-55a089fd4cb2?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1170&q=80" 
             alt="Entrenamiento Canino" class="img-fluid rounded shadow">
    </div>
</div>
{% endblock %}
```

## 10. Templates de Clientes

**app_Entrenamiento_Canino/templates/clientes/agregar_cliente.html**:
```html
{% extends 'base.html' %}

{% block content %}
<h2 class="mb-4">Agregar Nuevo Cliente</h2>
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
    <div class="row">
        <div class="col-md-6 mb-3">
            <label class="form-label">Email:</label>
            <input type="email" class="form-control" name="email" required>
        </div>
        <div class="col-md-6 mb-3">
            <label class="form-label">Dirección:</label>
            <input type="text" class="form-control" name="direccion" required>
        </div>
    </div>
    <div class="row">
        <div class="col-md-6 mb-3">
            <label class="form-label">Fecha de Nacimiento:</label>
            <input type="date" class="form-control" name="fecha_nacimiento" required>
        </div>
        <div class="col-md-6 mb-3">
            <label class="form-label">Fecha de Registro:</label>
            <input type="date" class="form-control" name="fecha_registro" required>
        </div>
    </div>
    <div class="mb-3">
        <label class="form-label">Comentarios:</label>
        <textarea class="form-control" name="comentarios" rows="3"></textarea>
    </div>
    <button type="submit" class="btn btn-success">Guardar Cliente</button>
    <a href="{% url 'ver_cliente' %}" class="btn btn-secondary">Cancelar</a>
</form>
{% endblock %}
```

**app_Entrenamiento_Canino/templates/clientes/ver_cliente.html**:
```html
{% extends 'base.html' %}

{% block content %}
<h2 class="mb-4">Lista de Clientes</h2>
<a href="{% url 'agregar_cliente' %}" class="btn btn-primary mb-3">
    <i class="fas fa-plus me-1"></i>Agregar Nuevo Cliente
</a>

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
                    <a href="{% url 'actualizar_cliente' cliente.id_cliente %}" class="btn btn-warning btn-sm">
                        <i class="fas fa-edit"></i>
                    </a>
                    <a href="{% url 'borrar_cliente' cliente.id_cliente %}" class="btn btn-danger btn-sm">
                        <i class="fas fa-trash"></i>
                    </a>
                </td>
            </tr>
            {% endfor %}
        </tbody>
    </table>
</div>
{% endblock %}
```

**app_Entrenamiento_Canino/templates/clientes/actualizar_cliente.html**:
```html
{% extends 'base.html' %}

{% block content %}
<h2 class="mb-4">Actualizar Cliente</h2>
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
    <div class="row">
        <div class="col-md-6 mb-3">
            <label class="form-label">Email:</label>
            <input type="email" class="form-control" name="email" value="{{ cliente.email }}" required>
        </div>
        <div class="col-md-6 mb-3">
            <label class="form-label">Dirección:</label>
            <input type="text" class="form-control" name="direccion" value="{{ cliente.direccion }}" required>
        </div>
    </div>
    <div class="row">
        <div class="col-md-6 mb-3">
            <label class="form-label">Fecha de Nacimiento:</label>
            <input type="date" class="form-control" name="fecha_nacimiento" value="{{ cliente.fecha_nacimiento|date:'Y-m-d' }}" required>
        </div>
        <div class="col-md-6 mb-3">
            <label class="form-label">Fecha de Registro:</label>
            <input type="date" class="form-control" name="fecha_registro" value="{{ cliente.fecha_registro|date:'Y-m-d' }}" required>
        </div>
    </div>
    <div class="mb-3">
        <label class="form-label">Comentarios:</label>
        <textarea class="form-control" name="comentarios" rows="3">{{ cliente.comentarios }}</textarea>
    </div>
    <button type="submit" class="btn btn-success">Actualizar Cliente</button>
    <a href="{% url 'ver_cliente' %}" class="btn btn-secondary">Cancelar</a>
</form>
{% endblock %}
```

**app_Entrenamiento_Canino/templates/clientes/borrar_cliente.html**:
```html
{% extends 'base.html' %}

{% block content %}
<h2 class="mb-4">Eliminar Cliente</h2>
<div class="alert alert-danger">
    <h4>¿Está seguro que desea eliminar al cliente?</h4>
    <p><strong>Nombre:</strong> {{ cliente.nombre }}</p>
    <p><strong>Email:</strong> {{ cliente.email }}</p>
    <p><strong>Teléfono:</strong> {{ cliente.telefono }}</p>
</div>
<form method="POST">
    {% csrf_token %}
    <button type="submit" class="btn btn-danger">Eliminar</button>
    <a href="{% url 'ver_cliente' %}" class="btn btn-secondary">Cancelar</a>
</form>
{% endblock %}
```

## 11. Admin

**app_Entrenamiento_Canino/admin.py**:
```python
from django.contrib import admin
from .models import Cliente

@admin.register(Cliente)
class ClienteAdmin(admin.ModelAdmin):
    list_display = ('id_cliente', 'nombre', 'email', 'telefono', 'fecha_registro')
    search_fields = ('nombre', 'email')
    list_filter = ('fecha_registro',)
```

## 12. Migraciones y ejecución

```bash
# Realizar migraciones
python manage.py makemigrations
python manage.py migrate

# Crear superusuario (opcional)
python manage.py createsuperuser

# Ejecutar servidor en puerto 8019
python manage.py runserver 8019
```

## 13. Estructura final de carpetas

```
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
│   │   ├── sidebar.html
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

El proyecto estará disponible en: `http://localhost:8019`
