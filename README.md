from datetime import datetime

# UTILIDADES

ARCHIVO_USUARIOS = "usuarios.txt"
ARCHIVO_PRODUCTOS = "productos.txt"
ARCHIVO_RESERVAS = "reservas.txt"

ADMIN_USER = "Admn1234"
ADMIN_PASS = "Admn1234"

HORA_APERTURA = 9
HORA_CIERRE = 17
DURACION_RESERVA = 30


#  VISUAL

def linea():
    print("=" * 50)


def titulo(texto):

    linea()
    print(f"✂️  {texto.upper()}")
    linea()


def mensaje_error(texto):
    print(f"❌ {texto}")


def mensaje_exito(texto):
    print(f"✅ {texto}")


def mensaje_info(texto):
    print(f"ℹ️ {texto}")


def pausa():
    input("\nPresione ENTER para continuar...")


def limpiar():
    print("\n" * 25)


# UTIL

def asegurar_archivo(nombre):

    try:
        open(nombre, "r", encoding="utf-8")

    except FileNotFoundError:
        open(nombre, "w", encoding="utf-8")


def nombre_valido(texto):

    return texto.strip() != ""


def validar_fecha(fecha):

    try:

        fecha_obj = datetime.strptime(
            fecha,
            "%Y-%m-%d"
        ).date()

        hoy = datetime.now().date()

        if fecha_obj < hoy:
            return False

        return True

    except ValueError:
        return False


def validar_hora(hora):

    try:

        datetime.strptime(hora, "%H:%M")
        return True

    except ValueError:
        return False


def convertir_hora_a_minutos(hora):

    hora_dt = datetime.strptime(
        hora,
        "%H:%M"
    )

    return hora_dt.hour * 60 + hora_dt.minute


def convertir_minutos_a_hora(minutos):

    hora = minutos // 60
    minuto = minutos % 60

    return f"{hora:02d}:{minuto:02d}"


# USUARIOS

def validar_usuario():

    while True:

        user = input("👤 Usuario: ").strip()

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

    try:

        with open(
            ARCHIVO_USUARIOS,
            "r",
            encoding="utf-8"
        ) as archivo:

            for linea in archivo:

                datos = linea.strip().split(",")

                if len(datos) < 2:
                    continue

                if datos[0] == user:
                    return True

    except FileNotFoundError:
        return False

    return False


def registrar_usuario():

    titulo("REGISTRO")

    while True:

        user = validar_usuario()

        if usuario_existe(user):
            mensaje_error(
                "El usuario ya existe"
            )
        else:
            break

    password = validar_contraseña()

    with open(
        ARCHIVO_USUARIOS,
        "a",
        encoding="utf-8"
    ) as archivo:

        archivo.write(
            f"{user},{password}\n"
        )

    mensaje_exito(
        "Usuario registrado correctamente"
    )


def iniciar_sesion():

    titulo("INICIAR SESIÓN")

    intentos = 3

    while intentos > 0:

        user = input(
            "👤 Usuario: "
        ).strip()

        password = input(
            "🔒 Contraseña: "
        ).strip()

        # ADMIN
        if (
            user == ADMIN_USER and
            password == ADMIN_PASS
        ):

            mensaje_exito(
                "Bienvenido administrador"
            )

            return "admin", user

        # CLIENTE
        try:

            with open(
                ARCHIVO_USUARIOS,
                "r",
                encoding="utf-8"
            ) as archivo:

                for linea in archivo:

                    datos = linea.strip().split(",")

                    if len(datos) < 2:
                        continue

                    if (
                        datos[0] == user and
                        datos[1] == password
                    ):

                        mensaje_exito(
                            "Inicio de sesión exitoso"
                        )

                        return "cliente", user

        except FileNotFoundError:

            mensaje_error(
                "No existen usuarios registrados"
            )

        intentos -= 1

        mensaje_error(
            f"Datos incorrectos | Intentos restantes: {intentos}"
        )

    mensaje_error("Acceso bloqueado")

    return None, None


#  PRODUCTOS

def producto_existe(nombre):

    try:

        with open(
            ARCHIVO_PRODUCTOS,
            "r",
            encoding="utf-8"
        ) as archivo:

            for linea in archivo:

                datos = linea.strip().split(",")

                if len(datos) < 2:
                    continue

                if datos[0].lower() == nombre.lower():
                    return True

    except FileNotFoundError:
        return False

    return False


def obtener_productos():

    productos = []

    try:

        with open(
            ARCHIVO_PRODUCTOS,
            "r",
            encoding="utf-8"
        ) as archivo:

            for linea in archivo:

                datos = linea.strip().split(",")

                if len(datos) < 2:
                    continue

                nombre = datos[0]
                precio = float(datos[1])

                productos.append(
                    (nombre, precio)
                )

    except FileNotFoundError:
        return []

    return productos


def añadir_producto():

    titulo("AÑADIR SERVICIO")

    nombre = input(
        "✂️ Nombre del servicio: "
    ).strip()

    if not nombre_valido(nombre):
        mensaje_error("Nombre inválido")
        return

    if producto_existe(nombre):
        mensaje_error(
            "Ese servicio ya existe"
        )
        return

    while True:

        try:

            precio = float(
                input("💰 Precio: $")
            )

            if precio <= 0:
                mensaje_error(
                    "Precio inválido"
                )
                continue

            break

        except ValueError:

            mensaje_error(
                "Ingrese un número válido"
            )

    with open(
        ARCHIVO_PRODUCTOS,
        "a",
        encoding="utf-8"
    ) as archivo:

        archivo.write(
            f"{nombre},{precio}\n"
        )

    mensaje_exito(
        "Servicio añadido correctamente"
    )


def editar_precio():

    titulo("EDITAR PRECIO")

    nombre = input(
        "✂️ Servicio a editar: "
    ).strip()

    if not producto_existe(nombre):
        mensaje_error(
            "El servicio no existe"
        )
        return

    while True:

        try:

            nuevo_precio = float(
                input("💰 Nuevo precio: $")
            )

            if nuevo_precio <= 0:
                mensaje_error(
                    "Precio inválido"
                )
                continue

            break

        except ValueError:

            mensaje_error(
                "Ingrese un número válido"
            )

    nuevas_lineas = []

    with open(
        ARCHIVO_PRODUCTOS,
        "r",
        encoding="utf-8"
    ) as archivo:

        for linea in archivo:

            datos = linea.strip().split(",")

            if len(datos) < 2:
                continue

            if datos[0].lower() == nombre.lower():

                nuevas_lineas.append(
                    f"{datos[0]},{nuevo_precio}\n"
                )

            else:
                nuevas_lineas.append(linea)

    with open(
        ARCHIVO_PRODUCTOS,
        "w",
        encoding="utf-8"
    ) as archivo:

        archivo.writelines(nuevas_lineas)

    mensaje_exito(
        "Precio actualizado"
    )


def eliminar_producto():

    titulo("ELIMINAR SERVICIO")

    nombre = input(
        "🗑️ Servicio a eliminar: "
    ).strip()

    if not producto_existe(nombre):
        mensaje_error(
            "El servicio no existe"
        )
        return

    nuevas_lineas = []

    with open(
        ARCHIVO_PRODUCTOS,
        "r",
        encoding="utf-8"
    ) as archivo:

        for linea in archivo:

            datos = linea.strip().split(",")

            if len(datos) < 2:
                continue

            if datos[0].lower() != nombre.lower():
                nuevas_lineas.append(linea)

    with open(
        ARCHIVO_PRODUCTOS,
        "w",
        encoding="utf-8"
    ) as archivo:

        archivo.writelines(nuevas_lineas)

    mensaje_exito(
        "Servicio eliminado"
    )


def ver_productos():

    titulo("CATÁLOGO DE SERVICIOS")

    productos = obtener_productos()

    if not productos:
        mensaje_info(
            "No hay servicios registrados"
        )
        return

    for i, (nombre, precio) in enumerate(
        productos,
        start=1
    ):

        print(
            f"{i}. ✂️ {nombre:<25} 💰 ${precio:.2f}"
        )



# RESERVAS

def obtener_reservas():

    reservas = []

    try:

        with open(
            ARCHIVO_RESERVAS,
            "r",
            encoding="utf-8"
        ) as archivo:

            for linea in archivo:

                datos = linea.strip().split(",")

                if len(datos) != 6:
                    continue

                reservas.append(datos)

    except FileNotFoundError:
        return []

    return reservas


def horario_ocupado(fecha, hora):

    reservas = obtener_reservas()

    nuevo_inicio = convertir_hora_a_minutos(hora)
    nuevo_fin = nuevo_inicio + DURACION_RESERVA

    for reserva in reservas:

        r_fecha = reserva[0]
        r_hora = reserva[1]

        if r_fecha != fecha:
            continue

        r_inicio = convertir_hora_a_minutos(r_hora)
        r_fin = r_inicio + DURACION_RESERVA

        conflicto = not (
            nuevo_inicio >= r_fin or
            nuevo_fin <= r_inicio
        )

        if conflicto:
            return True

    return False


def obtener_horarios_disponibles(fecha):

    horarios = []

    try:

        fecha_obj = datetime.strptime(
            fecha,
            "%Y-%m-%d"
        ).date()

    except ValueError:
        return []

    hoy = datetime.now().date()

    if fecha_obj < hoy:
        return []

    inicio = HORA_APERTURA * 60
    fin = HORA_CIERRE * 60

    for minutos in range(
        inicio,
        fin,
        DURACION_RESERVA
    ):

        hora = convertir_minutos_a_hora(minutos)

        # evitar horas pasadas hoy
        if fecha_obj == hoy:

            hora_actual = datetime.now().time()

            hora_reserva = datetime.strptime(
                hora,
                "%H:%M"
            ).time()

            if hora_reserva <= hora_actual:
                continue

        if horario_ocupado(fecha, hora):
            continue

        horarios.append(hora)

    return horarios


def mostrar_horarios_disponibles():

    titulo("HORARIOS DISPONIBLES")

    fecha = input(
        "📅 Fecha (YYYY-MM-DD): "
    ).strip()

    if not validar_fecha(fecha):
        mensaje_error("Fecha inválida")
        return

    horarios = obtener_horarios_disponibles(fecha)

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


def reservar(usuario):

    titulo("RESERVAR CITA")

    productos = obtener_productos()

    if not productos:
        mensaje_error(
            "No hay servicios disponibles"
        )
        return

    print("\n💈 SERVICIOS:\n")

    for i, (nombre, precio) in enumerate(
        productos,
        start=1
    ):

        print(
            f"{i}. ✂️ {nombre:<25} 💰 ${precio:.2f}"
        )

    try:

        opcion = int(
            input("\nSeleccione servicio: ")
        )

        nombre, precio = productos[opcion - 1]

    except:
        mensaje_error(
            "Selección inválida"
        )
        return

    fecha = input(
        "\n📅 Fecha (YYYY-MM-DD): "
    ).strip()

    if not validar_fecha(fecha):
        mensaje_error("Fecha inválida")
        return

    horarios = obtener_horarios_disponibles(fecha)

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

    try:

        opcion_hora = int(
            input("\nSeleccione horario: ")
        )

        hora = horarios[opcion_hora - 1]

    except:
        mensaje_error(
            "Horario inválido"
        )
        return

    with open(
        ARCHIVO_RESERVAS,
        "a",
        encoding="utf-8"
    ) as archivo:

        archivo.write(
            f"{fecha},{hora},{usuario},{nombre},{precio},{DURACION_RESERVA}\n"
        )

    titulo("RESERVA CONFIRMADA")

    print(f"👤 Cliente: {usuario}")
    print(f"💈 Servicio: {nombre}")
    print(f"📅 Fecha: {fecha}")
    print(f"🕒 Hora: {hora}")
    print(f"💰 Precio: ${precio:.2f}")
    print(f"⏳ Duración: {DURACION_RESERVA} min")


def ver_reservas():

    titulo("LISTA DE RESERVAS")

    reservas = obtener_reservas()

    if not reservas:
        mensaje_info(
            "No hay reservas registradas"
        )
        return

    for reserva in reservas:

        fecha = reserva[0]
        hora = reserva[1]
        usuario = reserva[2]
        servicio = reserva[3]
        precio = reserva[4]

        print(f"""
📅 Fecha:    {fecha}
🕒 Hora:     {hora}
👤 Cliente:  {usuario}
💈 Servicio: {servicio}
💰 Precio:   ${precio}
----------------------------------------
""")


#MENÚS

def menu_admin():

    while True:

        titulo("PANEL ADMINISTRADOR")

        print("""
1️⃣  Ver reservas
2️⃣  Ver horarios disponibles
3️⃣  Añadir servicio
4️⃣  Ver servicios
5️⃣  Editar precio
6️⃣  Eliminar servicio
7️⃣  Cerrar sesión
""")

        opcion = input(
            "👉 Seleccione una opción: "
        ).strip()

        if opcion == "1":

            ver_reservas()
            pausa()

        elif opcion == "2":

            mostrar_horarios_disponibles()
            pausa()

        elif opcion == "3":

            añadir_producto()
            pausa()

        elif opcion == "4":

            ver_productos()
            pausa()

        elif opcion == "5":

            editar_precio()
            pausa()

        elif opcion == "6":

            eliminar_producto()
            pausa()

        elif opcion == "7":

            mensaje_info(
                "Sesión cerrada"
            )
            break

        else:

            mensaje_error(
                "Opción inválida"
            )
            pausa()


def menu_cliente(usuario):

    while True:

        titulo(f"BIENVENIDO {usuario}")

        print("""
1️⃣  Ver servicios
2️⃣  Ver horarios disponibles
3️⃣  Reservar cita
4️⃣  Cerrar sesión
""")

        opcion = input(
            "👉 Seleccione una opción: "
        ).strip()

        if opcion == "1":

            ver_productos()
            pausa()

        elif opcion == "2":

            mostrar_horarios_disponibles()
            pausa()

        elif opcion == "3":

            reservar(usuario)
            pausa()

        elif opcion == "4":

            mensaje_info(
                "Sesión cerrada"
            )
            break

        else:

            mensaje_error(
                "Opción inválida"
            )
            pausa()


# MAIN

asegurar_archivo(ARCHIVO_USUARIOS)
asegurar_archivo(ARCHIVO_PRODUCTOS)
asegurar_archivo(ARCHIVO_RESERVAS)

limpiar()

titulo("WEB-PELUQUERÍA")

print("""
💈 SISTEMA DE RESERVAS

1️⃣  Iniciar sesión
2️⃣  Registrarse
3️⃣  Salir
""")

opcion = input(
    "👉 Seleccione una opción: "
).strip()

tipo = None
usuario = None

if opcion == "1":

    tipo, usuario = iniciar_sesion()

elif opcion == "2":

    registrar_usuario()

    print("\n🔐 Ahora inicie sesión\n")

    tipo, usuario = iniciar_sesion()

elif opcion == "3":

    mensaje_info("Programa finalizado")

else:

    mensaje_error("Opción inválida")


if tipo == "admin":

    menu_admin()

elif tipo == "cliente":

    menu_cliente(usuario)

elif tipo is not None:

    mensaje_error(
        "No se pudo acceder al sistema"
    )
