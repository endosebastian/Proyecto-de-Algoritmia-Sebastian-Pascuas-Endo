# 💈 WEB-PELUQUERÍA6

Sistema de reservas para peluquería desarrollado en Python.
Permite registrar usuarios, iniciar sesión, administrar servicios y reservar citas con validación de horarios.

---

# 📌 Características

## 👤 Usuarios

* Registro de usuarios
* Inicio de sesión
* Validación de usuario y contraseña
* Control de intentos fallidos

## 🔑 Administrador

Usuario administrador integrado:

```python
Usuario: Admn1234
Contraseña: Admn1234
```

Funciones del administrador:

* Ver reservas
* Añadir servicios
* Editar precios
* Eliminar servicios
* Ver catálogo de servicios

## 💈 Servicios

* Registro de servicios
* Gestión de precios
* Catálogo dinámico

## 📅 Reservas

* Reservar citas
* Validación de fecha y hora
* Control de conflictos de horario
* Restricción de horario laboral
* Duración automática de reservas

---

# 🛠 Tecnologías utilizadas

* Python 3
* Archivos TXT como almacenamiento
* Programación modular
* Manejo de archivos
* Validaciones con `datetime`

---

# 📂 Estructura de archivos

```plaintext
WEB-PELUQUERÍA6/
│
├── main.py
├── usuarios.txt
├── productos.txt
├── reservas.txt
└── README.md
```

---

# ▶️ Cómo ejecutar el proyecto

## 1. Clonar repositorio

```bash
git clone TU_LINK_DEL_REPOSITORIO
```

---

## 2. Entrar a la carpeta

```bash
cd WEB-PELUQUERÍA6
```

---

## 3. Ejecutar programa

```bash
python main.py
```

---

# 📋 Reglas del sistema

## Usuarios

* Entre 8 y 12 caracteres
* Deben contener letras y números
* No se permiten caracteres especiales

## Horarios

* Horario laboral:

  * 08:00 AM → 09:00 PM
* Duración de reserva:

  * 30 minutos
* No se permiten reservas cruzadas

---

# 💡 Funciones principales

## Usuarios

* `registrar_usuario()`
* `iniciar_sesion()`
* `usuario_existe()`

## Servicios

* `añadir_producto()`
* `editar_precio()`
* `eliminar_producto()`
* `ver_productos()`

## Reservas

* `reservar()`
* `ver_reservas()`

---

# 🚀 Futuras mejoras

* Interfaz gráfica
* Página web con Flask
* Base de datos MySQL o SQLite
* Recuperación de contraseña
* Panel web para administrador
* Calendario visual
* Reservas online

---

# 👨‍💻 Autor

Desarrollado por SEBASTIAN PASCUAS ENDO.

---

# 📄 Licencia

Proyecto de uso educativo.
