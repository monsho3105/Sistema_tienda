"""
CRUD simple de productos - Sistema Tienda
Permite Crear, Leer, Actualizar y Eliminar productos.
Los datos se guardan en un archivo de texto (inventario.txt) para persistencia.
"""

import os

ARCHIVO = "INVENTARIO_PY.txt"


def cargar_productos():
    """Lee el archivo y devuelve una lista de productos como diccionarios."""
    productos = []
    if not os.path.exists(ARCHIVO):
        return productos

    with open(ARCHIVO, "r", encoding="utf-8") as f:
        for linea in f:
            linea = linea.strip()
            if not linea:
                continue
            partes = linea.split(";")
            if len(partes) == 3:
                id_p, nombre, precio = partes
                productos.append({
                    "id": int(id_p),
                    "nombre": nombre,
                    "precio": float(precio)
                })
    return productos


def guardar_productos(productos):
    """Sobrescribe el archivo con la lista actual de productos."""
    with open(ARCHIVO, "w", encoding="utf-8") as f:
        for p in productos:
            f.write(f"{p['id']};{p['nombre']};{p['precio']}\n")


def siguiente_id(productos):
    if not productos:
        return 1
    return max(p["id"] for p in productos) + 1


def crear_producto(productos):
    nombre = input("Nombre del producto: ").strip()
    try:
        precio = float(input("Precio: $").strip())
    except ValueError:
        print("Precio inválido. Debe ser un número.")
        return

    nuevo = {
        "id": siguiente_id(productos),
        "nombre": nombre,
        "precio": precio
    }
    productos.append(nuevo)
    guardar_productos(productos)
    print(f"Producto creado con id {nuevo['id']}.")


def listar_productos(productos):
    if not productos:
        print("No hay productos registrados.")
        return

    print("\nID   Producto                Precio")
    print("---  ----------------------  ----------")
    for p in productos:
        print(f"{p['id']:<4} {p['nombre']:<23} ${p['precio']:.2f}")
    print()


def buscar_producto(productos, id_buscado):
    for p in productos:
        if p["id"] == id_buscado:
            return p
    return None


def actualizar_producto(productos):
    listar_productos(productos)
    if not productos:
        return

    try:
        id_p = int(input("ID del producto a actualizar: ").strip())
    except ValueError:
        print("ID inválido.")
        return

    producto = buscar_producto(productos, id_p)
    if not producto:
        print("Producto no encontrado.")
        return

    nuevo_nombre = input(f"Nuevo nombre (Enter para dejar '{producto['nombre']}'): ").strip()
    nuevo_precio = input(f"Nuevo precio (Enter para dejar ${producto['precio']:.2f}): ").strip()

    if nuevo_nombre:
        producto["nombre"] = nuevo_nombre
    if nuevo_precio:
        try:
            producto["precio"] = float(nuevo_precio)
        except ValueError:
            print("Precio inválido, se mantiene el anterior.")

    guardar_productos(productos)
    print("Producto actualizado.")


def eliminar_producto(productos):
    listar_productos(productos)
    if not productos:
        return

    try:
        id_p = int(input("ID del producto a eliminar: ").strip())
    except ValueError:
        print("ID inválido.")
        return

    producto = buscar_producto(productos, id_p)
    if not producto:
        print("Producto no encontrado.")
        return

    confirmar = input(f"¿Eliminar '{producto['nombre']}'? (s/n): ").strip().lower()
    if confirmar == "s":
        productos.remove(producto)
        guardar_productos(productos)
        print("Producto eliminado.")
    else:
        print("Operación cancelada.")


def menu():
    productos = cargar_productos()

    opciones = {
        "1": ("Crear producto", lambda: crear_producto(productos)),
        "2": ("Listar productos", lambda: listar_productos(productos)),
        "3": ("Actualizar producto", lambda: actualizar_producto(productos)),
        "4": ("Eliminar producto", lambda: eliminar_producto(productos)),
        "5": ("Salir", None),
    }

    while True:
        print("\n--- CRUD PRODUCTOS - SISTEMA TIENDA ---")
        for clave, (texto, _) in opciones.items():
            print(f"{clave}. {texto}")

        eleccion = input("Elige una opción: ").strip()

        if eleccion == "5":
            print("Saliendo...")
            break
        elif eleccion in opciones:
            opciones[eleccion][1]()
        else:
            print("Opción no válida, intenta de nuevo.")


if __name__ == "__main__":
    menu()
