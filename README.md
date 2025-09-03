# sistema-de-gestion-de-biblioteca-dijital
todo de python
# Clase que representa un libro en la biblioteca
class Libro:
    def __init__(self, titulo, autor, categoria, isbn):
        # Autor y título son inmutables, se almacenan en una tupla
        self.info = (titulo, autor)
        self.categoria = categoria
        self.isbn = isbn

    def __str__(self):
        return f"'{self.info[0]}' por {self.info[1]} | Categoría: {self.categoria} | ISBN: {self.isbn}"

# Clase que representa un usuario de la biblioteca
class Usuario:
    def __init__(self, nombre, id_usuario):
        self.nombre = nombre
        self.id_usuario = id_usuario
        # Lista para almacenar libros actualmente prestados
        self.libros_prestados = []

    def listar_libros_prestados(self):
        if self.libros_prestados:
            print(f"Libros prestados a {self.nombre}:")
            for libro in self.libros_prestados:
                print(f" - {libro}")
        else:
            print(f"{self.nombre} no tiene libros prestados.")

# Clase que gestiona la biblioteca
class Biblioteca:
    def __init__(self):
        # Diccionario para almacenar libros, clave = ISBN
        self.libros = {}
        # Diccionario para almacenar usuarios, clave = ID
        self.usuarios = {}
        # Conjunto para asegurar IDs únicos
        self.ids_usuarios = set()

    # Función para añadir un libro
    def agregar_libro(self, libro):
        if libro.isbn not in self.libros:
            self.libros[libro.isbn] = libro
            print(f"Libro '{libro.info[0]}' agregado a la biblioteca.")
        else:
            print(f"El libro con ISBN {libro.isbn} ya existe.")

    # Función para quitar un libro
    def quitar_libro(self, isbn):
        if isbn in self.libros:
            del self.libros[isbn]
            print(f"Libro con ISBN {isbn} eliminado de la biblioteca.")
        else:
            print(f"No se encontró un libro con ISBN {isbn}.")

    # Función para registrar un usuario
    def registrar_usuario(self, usuario):
        if usuario.id_usuario not in self.ids_usuarios:
            self.usuarios[usuario.id_usuario] = usuario
            self.ids_usuarios.add(usuario.id_usuario)
            print(f"Usuario '{usuario.nombre}' registrado exitosamente.")
        else:
            print(f"ID de usuario {usuario.id_usuario} ya existe.")

    # Función para dar de baja a un usuario
    def dar_baja_usuario(self, id_usuario):
        if id_usuario in self.usuarios:
            del self.usuarios[id_usuario]
            self.ids_usuarios.remove(id_usuario)
            print(f"Usuario con ID {id_usuario} dado de baja.")
        else:
            print(f"No se encontró un usuario con ID {id_usuario}.")

    # Función para prestar un libro a un usuario
    def prestar_libro(self, isbn, id_usuario):
        if isbn not in self.libros:
            print(f"No existe un libro con ISBN {isbn}.")
            return
        if id_usuario not in self.usuarios:
            print(f"No existe un usuario con ID {id_usuario}.")
            return
        libro = self.libros[isbn]
        usuario = self.usuarios[id_usuario]
        usuario.libros_prestados.append(libro)
        # Se elimina del catálogo disponible
        del self.libros[isbn]
        print(f"Libro '{libro.info[0]}' prestado a {usuario.nombre}.")

    # Función para devolver un libro
    def devolver_libro(self, isbn, id_usuario):
        if id_usuario not in self.usuarios:
            print(f"No existe un usuario con ID {id_usuario}.")
            return
        usuario = self.usuarios[id_usuario]
        libro_devuelto = None
        for libro in usuario.libros_prestados:
            if libro.isbn == isbn:
                libro_devuelto = libro
                break
        if libro_devuelto:
            usuario.libros_prestados.remove(libro_devuelto)
            self.libros[isbn] = libro_devuelto
            print(f"Libro '{libro_devuelto.info[0]}' devuelto por {usuario.nombre}.")
        else:
            print(f"{usuario.nombre} no tiene el libro con ISBN {isbn}.")

    # Función para buscar libros por título, autor o categoría
    def buscar_libros(self, criterio, valor):
        resultados = []
        for libro in self.libros.values():
            if criterio == "titulo" and valor.lower() in libro.info[0].lower():
                resultados.append(libro)
            elif criterio == "autor" and valor.lower() in libro.info[1].lower():
                resultados.append(libro)
            elif criterio == "categoria" and valor.lower() in libro.categoria.lower():
                resultados.append(libro)
        if resultados:
            print(f"Resultados de búsqueda por {criterio} '{valor}':")
            for libro in resultados:
                print(f" - {libro}")
        else:
            print(f"No se encontraron libros por {criterio} '{valor}'.")

# -------------------------
# Ejemplo de uso del sistema
# -------------------------

# Crear la biblioteca
biblioteca = Biblioteca()

# Crear libros
libro1 = Libro("Cien Años de Soledad", "Gabriel García Márquez", "Novela", "12345")
libro2 = Libro("El Principito", "Antoine de Saint-Exupéry", "Infantil", "67890")

# Agregar libros a la biblioteca
biblioteca.agregar_libro(libro1)
biblioteca.agregar_libro(libro2)

# Registrar usuarios
usuario1 = Usuario("Ana", "U001")
usuario2 = Usuario("Luis", "U002")
biblioteca.registrar_usuario(usuario1)
biblioteca.registrar_usuario(usuario2)

# Prestar y devolver libros
biblioteca.prestar_libro("12345", "U001")
usuario1.listar_libros_prestados()
biblioteca.devolver_libro("12345", "U001")
usuario1.listar_libros_prestados()

# Buscar libros
biblioteca.buscar_libros("autor", "Saint-Exupéry")
biblioteca.buscar_libros("categoria", "Novela")
