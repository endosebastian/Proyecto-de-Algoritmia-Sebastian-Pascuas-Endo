# Proyecto-de-Algoritmia-Sebastian-Pascuas-Endo
from datetime import datetime, date

# ================= UTILIDADES =================

DURACION_RESERVA = 30

HORA_APERTURA = 9
HORA_CIERRE = 17

ADMIN_USER = "Admn1234"
ADMIN_PASS = "Admn1234"


# ================= VISUAL =================

def limpiar():
    """
    Simula limpiar pantalla.
    """
    print("\n" * 30)


def linea():
    print("=" * 50)


def titulo(texto):
    """
    Muestra títulos decorados.
    """
    linea()
    print(f"✂️  {texto.upper()}".center(50))
    linea()


def mensaje_error(texto):
    print(f"❌ {texto}")


def mensaje_exito(texto):
    print(f"✅ {texto}")


def mensaje_info(texto):
    print(f"ℹ️ {texto}")


def pausa():
    input("\nPresione ENTER para continuar...")


# ================= ARCHIVOS =================

def asegurar_archivo(nombre):
    """
    Crea el archivo si no existe.
    """

    try:
        open(nombre, "r")

    except FileNotFoundError:
        open(nombre, "w")


# ================= VALIDACIONES =================

def nombre_valido(texto):
    """
    Verifica que el texto no esté vacío.
    """

    return texto.strip() != ""


def validar_fecha(fecha):
    """
    Valida:
    - Formato YYYY-MM-DD
    - Que no sea una fecha pasada
    """

    try:

        fecha_dt = datetime.strptime(
            fecha,
            "%Y-%m-%d"
        ).date()

        hoy = date.today()

        if fecha_dt < hoy:
            return False

        return True

    except ValueError:
        return False


def validar_hora(hora):
    """
    Valida formato HH:MM.
    """

    try:

        datetime.strptime(hora, "%H:%M")
        return True

    except ValueError:
        return False


def convertir_hora_a_minutos(hora):
    """
    Convierte hora HH:MM a minutos.
    """

    hora_dt = datetime.strptime(
        hora,
        "%H:%M"
    )

    return (
        hora_dt.hour * 60 +
        hora_dt.minute
    )


def convertir_minutos_a_hora(minutos):
    """
    Convierte minutos a formato HH:MM.
    """

    hora = minutos // 60
    minuto = minutos % 60

    return f"{hora:02d}:{minuto:02d}"


# ================= HORARIOS =================

def obtener_horarios_disponibles(fecha):
    """
    Genera horarios disponibles.
    - Desde 9:00 AM
    - Hasta 5:00 PM
    - Intervalos de 30 minutos
    """

    horarios = []

    inicio_jornada = HORA_APERTURA * 60
    fin_jornada = HORA_CIERRE * 60

    hoy = date.today()
    ahora = datetime.now()

    # ================= GENERAR HORARIOS =================

    for minutos in range(
        inicio_jornada,
        fin_jornada,
        DURACION_RESERVA
    ):

        ocupado = False

        nuevo_inicio = minutos
        nuevo_fin = minutos + DURACION_RESERVA

        # ================= VALIDAR SI YA PASÓ =================

        fecha_dt = datetime.strptime(
            fecha,
            "%Y-%m-%d"
        ).date()

        if fecha_dt == hoy:

            minutos_actuales = (
                ahora.hour * 60 +
                ahora.minute
            )

            if nuevo_inicio <= minutos_actuales:
                continue

        # ================= VALIDAR CONFLICTOS =================

        try:

            with open("reservas.txt", "r") as archivo:

                for linea in archivo:

                    datos = linea.strip().split(",")

                    if len(datos) < 6:
                        continue

                    r_fecha = datos[0]
                    r_hora = datos[1]
                    r_duracion = int(datos[5])

                    if r_fecha != fecha:
                        continue

                    r_inicio = convertir_hora_a_minutos(
                        r_hora
                    )

                    r_fin = r_inicio + r_duracion

                    conflicto = not (
                        nuevo_inicio >= r_fin or
                        nuevo_fin <= r_inicio
                    )

                    if conflicto:
                        ocupado = True
                        break

        except FileNotFoundError:
            pass

        if not ocupado:

            horarios.append(
                convertir_minutos_a_hora(
                    minutos
                )
            )

    return horarios


def mostrar_horarios_disponibles():
    """
    Muestra horarios disponibles de una fecha.
    """

    limpiar()
    titulo("HORARIOS DISPONIBLES")

    fecha = input(
        "📅 Fecha (YYYY-MM-DD): "
    ).strip()

    if not validar_fecha(fecha):

        mensaje_error(
            "Fecha inválida o pasada"
        )

        return

    horarios = obtener_horarios_disponibles(
        fecha
    )

    if not horarios:

        mensaje_error(
            "No hay horarios disponibles"
        )

        return

    print("\n🕒 HORARIOS DISPONIBLES:\n")

    for i, hora in enumerate(
        horarios,
        start=1
    ):

        print(f"{i}. {hora}")

        # ================= USUARIOS =================

def validar_usuario():
    """
    Valida nombre de usuario.
    - Entre 8 y 12 caracteres
    - Letras y números
    """

    while True:

        user = input(
            "👤 Usuario: "
        ).strip()

        if len(user) < 8 or len(user) > 12:

            mensaje_error(
                "Debe tener entre 8 y 12 caracteres"
            )

            continue

        if not user.isalnum():

            mensaje_error(
                "Solo letras y números"
            )

            continue

        if not any(c.isalpha() for c in user):

            mensaje_error(
                "Debe contener letras"
            )

            continue

        if not any(c.isdigit() for c in user):

            mensaje_error(
                "Debe contener números"
            )

            continue

        return user


def validar_contraseña():
    """
    Valida contraseña.
    """

    while True:

        password = input(
            "🔒 Contraseña: "
        ).strip()

        if len(password) < 8 or len(password) > 12:

            mensaje_error(
                "Debe tener entre 8 y 12 caracteres"
            )

            continue

        if not any(c.isalpha() for c in password):

            mensaje_error(
                "Debe contener letras"
            )

            continue

        if not any(c.isdigit() for c in password):

            mensaje_error(
                "Debe contener números"
            )

            continue

        return password


def usuario_existe(user):
    """
    Verifica si el usuario ya existe.
    """

    try:

        with open("usuarios.txt", "r") as archivo:

            for linea in archivo:

                datos = linea.strip().split(",")

                if len(datos) < 2:
                    continue

                usuario_guardado = datos[0]

                if (
                    usuario_guardado.lower() ==
                    user.lower()
                ):

                    return True

    except FileNotFoundError:
        return False

    return False


def registrar_usuario():
    """
    Registra nuevo usuario.
    """

    limpiar()
    titulo("REGISTRO DE USUARIO")

    while True:

        user = validar_usuario()

        if usuario_existe(user):

            mensaje_error(
                "Ese usuario ya existe"
            )

        else:
            break

    password = validar_contraseña()

    try:

        with open(
            "usuarios.txt",
            "a"
        ) as archivo:

            archivo.write(
                f"{user},{password}\n"
            )

        mensaje_exito(
            "Usuario registrado correctamente"
        )

    except:

        mensaje_error(
            "Error guardando usuario"
        )


def iniciar_sesion():
    """
    Inicia sesión.
    """

    limpiar()
    titulo("INICIAR SESIÓN")

    intentos = 3

    while intentos > 0:

        user = input(
            "👤 Usuario: "
        ).strip()

        password = input(
            "🔒 Contraseña: "
        ).strip()

        # ================= ADMIN =================

        if (
            user == ADMIN_USER and
            password == ADMIN_PASS
        ):

            mensaje_exito(
                "Bienvenido administrador"
            )

            return "admin", user

        # ================= CLIENTES =================

        try:

            with open(
                "usuarios.txt",
                "r"
            ) as archivo:

                for linea in archivo:

                    datos = linea.strip().split(",")

                    if len(datos) < 2:
                        continue

                    usuario_guardado = datos[0]
                    password_guardada = datos[1]

                    if (
                        user == usuario_guardado and
                        password == password_guardada
                    ):

                        mensaje_exito(
                            "Inicio de sesión exitoso"
                        )

                        return "cliente", user

        except FileNotFoundError:

            mensaje_error(
                "No existen usuarios registrados"
            )

            return None, None

        intentos -= 1

        mensaje_error(
            f"Datos incorrectos | "
            f"Intentos restantes: {intentos}"
        )

    mensaje_error(
        "Acceso bloqueado"
    )

    return None, None

    # ================= PRODUCTOS =================

def producto_existe(nombre):
    """
    Verifica si un servicio ya existe.
    """

    try:

        with open(
            "productos.txt",
            "r"
        ) as archivo:

            for linea in archivo:

                datos = linea.strip().split(",")

                if len(datos) < 2:
                    continue

                servicio = datos[0]

                if (
                    servicio.lower() ==
                    nombre.lower()
                ):

                    return True

    except FileNotFoundError:
        return False

    return False


def añadir_producto():
    """
    Añade un nuevo servicio.
    """

    limpiar()
    titulo("AÑADIR SERVICIO")

    nombre = input(
        "✂️ Nombre del servicio: "
    ).strip()

    if not nombre_valido(nombre):

        mensaje_error(
            "Nombre inválido"
        )

        return

    if producto_existe(nombre):

        mensaje_error(
            "Ese servicio ya existe"
        )

        return

    # ================= PRECIO =================

    try:

        precio = float(
            input("💰 Precio: $")
        )

        if precio <= 0:

            mensaje_error(
                "El precio debe ser mayor a 0"
            )

            return

    except ValueError:

        mensaje_error(
            "Debe ingresar un número válido"
        )

        return

    # ================= GUARDAR =================

    try:

        with open(
            "productos.txt",
            "a"
        ) as archivo:

            archivo.write(
                f"{nombre},{precio}\n"
            )

        mensaje_exito(
            "Servicio añadido correctamente"
        )

    except:

        mensaje_error(
            "Error guardando servicio"
        )


def editar_precio():
    """
    Edita el precio de un servicio.
    """

    limpiar()
    titulo("EDITAR PRECIO")

    nombre = input(
        "✂️ Servicio a editar: "
    ).strip()

    if not producto_existe(nombre):

        mensaje_error(
            "Ese servicio no existe"
        )

        return

    # ================= NUEVO PRECIO =================

    try:

        nuevo_precio = float(
            input("💰 Nuevo precio: $")
        )

        if nuevo_precio <= 0:

            mensaje_error(
                "El precio debe ser mayor a 0"
            )

            return

    except ValueError:

        mensaje_error(
            "Debe ingresar un número válido"
        )

        return

    nuevas_lineas = []

    # ================= ACTUALIZAR =================

    try:

        with open(
            "productos.txt",
            "r"
        ) as archivo:

            for linea in archivo:

                datos = linea.strip().split(",")

                if len(datos) < 2:
                    continue

                servicio = datos[0]

                if (
                    servicio.lower() ==
                    nombre.lower()
                ):

                    nuevas_lineas.append(
                        f"{servicio},{nuevo_precio}\n"
                    )

                else:

                    nuevas_lineas.append(
                        linea
                    )

        with open(
            "productos.txt",
            "w"
        ) as archivo:

            archivo.writelines(
                nuevas_lineas
            )

        mensaje_exito(
            "Precio actualizado correctamente"
        )

    except:

        mensaje_error(
            "Error actualizando precio"
        )


def eliminar_producto():
    """
    Elimina un servicio.
    """

    limpiar()
    titulo("ELIMINAR SERVICIO")

    nombre = input(
        "🗑️ Servicio a eliminar: "
    ).strip()

    if not producto_existe(nombre):

        mensaje_error(
            "Ese servicio no existe"
        )

        return

    nuevas_lineas = []

    # ================= ELIMINAR =================

    try:

        with open(
            "productos.txt",
            "r"
        ) as archivo:

            for linea in archivo:

                datos = linea.strip().split(",")

                if len(datos) < 2:
                    continue

                servicio = datos[0]

                if (
                    servicio.lower() !=
                    nombre.lower()
                ):

                    nuevas_lineas.append(
                        linea
                    )

        with open(
            "productos.txt",
            "w"
        ) as archivo:

            archivo.writelines(
                nuevas_lineas
            )

        mensaje_exito(
            "Servicio eliminado correctamente"
        )

    except:

        mensaje_error(
            "Error eliminando servicio"
        )


def ver_productos():
    """
    Muestra todos los servicios.
    """

    limpiar()
    titulo("CATÁLOGO DE SERVICIOS")

    try:

        with open(
            "productos.txt",
            "r"
        ) as archivo:

            servicios = archivo.readlines()

            if not servicios:

                mensaje_info(
                    "No hay servicios registrados"
                )

                return

            print()

            for i, linea in enumerate(
                servicios,
                start=1
            ):

                datos = linea.strip().split(",")

                if len(datos) < 2:
                    continue

                nombre = datos[0]
                precio = datos[1]

                print(
                    f"{i}. "
                    f"✂️ {nombre:<25} "
                    f"💰 ${precio}"
                )

    except FileNotFoundError:

        mensaje_error(
            "No existe catálogo"
        )
