import re
import os
import json

# Clase Contacto
class Contacto:
    def __init__(self, nombre, telefono, correo):
        self.nombre = nombre
        self.telefono = telefono
        self.correo = correo

    def __str__(self):
        return f"{self.nombre} | Tel: {self.telefono} | Email: {self.correo}"

    def to_dict(self):
        return {
            "nombre": self.nombre,
            "telefono": self.telefono,
            "correo": self.correo
        }

    @staticmethod
    def validar_correo(correo):
        patron = r'^[\w\.-]+@[\w\.-]+\.\w+$'
        return re.match(patron, correo)


# Clase Gestor de Contactos
class GestionContactos:
    def __init__(self, archivo="contactos.txt"):
        self.archivo = archivo
        self.contactos = []
        self.cargar_contactos()

    def agregar_contacto(self, nombre, telefono, correo):
        if not Contacto.validar_correo(correo):
            raise ValueError("❌ El correo electrónico no es válido.")
        nuevo = Contacto(nombre, telefono, correo)
        self.contactos.append(nuevo)
        self.guardar_contactos()
        print(f"✅ Contacto '{nombre}' agregado correctamente.")

    def mostrar_contactos(self):
        if not self.contactos:
            print("📭 No hay contactos disponibles.")
        else:
            print("\n📋 Lista de contactos:")
            for i, c in enumerate(self.contactos, 1):
                print(f"{i}. {c}")

    def buscar_contacto(self, nombre):
        encontrados = [c for c in self.contactos if c.nombre.lower() == nombre.lower()]
        if encontrados:
            print("\n🔍 Contactos encontrados:")
            for c in encontrados:
                print(c)
        else:
            print("❌ No se encontró ningún contacto con ese nombre.")

    def eliminar_contacto(self, nombre):
        for c in self.contactos:
            if c.nombre.lower() == nombre.lower():
                self.contactos.remove(c)
                self.guardar_contactos()
                print(f"🗑️ Contacto '{nombre}' eliminado.")
                return
        print("❌ Contacto no encontrado.")

    def guardar_contactos(self):
        try:
            with open(self.archivo, 'w', encoding='utf-8') as f:
                json.dump([c.to_dict() for c in self.contactos], f, indent=4)
        except Exception as e:
            print(f"⚠️ Error al guardar contactos: {e}")

    def cargar_contactos(self):
        if os.path.exists(self.archivo):
            try:
                with open(self.archivo, 'r', encoding='utf-8') as f:
                    datos = json.load(f)
                    self.contactos = [Contacto(**d) for d in datos]
            except Exception as e:
                print(f"⚠️ Error al cargar contactos: {e}")


# Menú de interacción
def menu():
    gestor = GestionContactos()

    while True:
        print("\n===== MENÚ DE CONTACTOS =====")
        print("1. Agregar contacto")
        print("2. Mostrar contactos")
        print("3. Buscar contacto")
        print("4. Eliminar contacto")
        print("5. Salir")

        opcion = input("Selecciona una opción: ")

        if opcion == "1":
            nombre = input("Nombre: ").strip()
            telefono = input("Teléfono: ").strip()
            correo = input("Correo: ").strip()
            if not nombre or not telefono or not correo:
                print("⚠️ Todos los campos son obligatorios.")
                continue
            try:
                gestor.agregar_contacto(nombre, telefono, correo)
            except ValueError as e:
                print(e)

        elif opcion == "2":
            gestor.mostrar_contactos()

        elif opcion == "3":
            nombre = input("Nombre a buscar: ").strip()
            gestor.buscar_contacto(nombre)

        elif opcion == "4":
            nombre = input("Nombre a eliminar: ").strip()
            gestor.eliminar_contacto(nombre)

        elif opcion == "5":
            print("👋 Saliendo del programa. ¡Hasta pronto!")
            break

        else:
            print("❌ Opción no válida. Intenta de nuevo.")

# Ejecutar el programa
if __name__ == "__main__":
    menu()

