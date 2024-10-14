
# Rust CRUD API

Este proyecto es un servidor web simple escrito en **Rust** que implementa un CRUD (Crear, Leer, Actualizar, Eliminar) de usuarios en una base de datos PostgreSQL. El servidor escucha en el puerto `8080` y responde a solicitudes HTTP para interactuar con los datos almacenados en la base de datos.

## Funcionalidades Principales

1. **Crear usuario**: Recibe solicitudes HTTP POST para agregar nuevos usuarios a la base de datos.
2. **Obtener usuario por ID**: Recibe solicitudes HTTP GET para obtener un usuario específico según su ID.
3. **Obtener todos los usuarios**: Recibe solicitudes HTTP GET para obtener todos los usuarios almacenados en la base de datos.
4. **Actualizar usuario**: Recibe solicitudes HTTP PUT para modificar los datos de un usuario existente.
5. **Eliminar usuario**: Recibe solicitudes HTTP DELETE para eliminar un usuario específico de la base de datos.

## Estructura del Proyecto

### 1. Modelo de Usuario

El modelo `User` es una estructura que define los campos de un usuario, que incluyen:

- `id`: Es opcional y puede ser `None` al momento de la creación.
- `name`: El nombre del usuario.
- `email`: El correo electrónico del usuario.

```rust
#[derive(Serialize, Deserialize)]
struct User {
    id: Option<i32>,
    name: String,
    email: String,
}
```

### 2. Conexión a la Base de Datos

Se utiliza el crate `postgres` para conectarse a una base de datos PostgreSQL. La URL de la base de datos se obtiene de una variable de entorno llamada `DATABASE_URL`.

```rust
const DB_URL: &str = env!("DATABASE_URL");
```

### 3. Servidor TCP

Se levanta un servidor que escucha en el puerto `8080` para recibir y manejar conexiones de clientes. Cada conexión es gestionada por la función `handle_client`.

```rust
let listener = TcpListener::bind(format!("0.0.0.0:8080")).unwrap();
```

### 4. Controladores para Manejo de Solicitudes

El servidor tiene varios controladores que responden a diferentes tipos de solicitudes HTTP:

- **POST /users**: Crea un nuevo usuario.
- **GET /users/:id**: Obtiene un usuario por su ID.
- **GET /users**: Obtiene todos los usuarios.
- **PUT /users/:id**: Actualiza un usuario existente.
- **DELETE /users/:id**: Elimina un usuario.

Cada solicitud es procesada por una función dedicada que se conecta a la base de datos y ejecuta la operación correspondiente.

### 5. Estructura del Código

- **`main`**: Inicia el servidor y maneja las conexiones.
- **`handle_client`**: Procesa las solicitudes del cliente y delega las operaciones a las funciones correspondientes según el tipo de solicitud HTTP.
- **`set_database`**: Inicializa la base de datos creando una tabla `users` si no existe.
- **Controladores de solicitud**: Funciones para manejar las operaciones CRUD en la base de datos (crear, obtener, actualizar y eliminar usuarios).

### 6. Respuesta del Servidor

Se definieron constantes para las respuestas HTTP comunes:

- `OK_RESPONSE`: Respuesta 200 OK.
- `NOT_FOUND`: Respuesta 404 Not Found.
- `INTERNAL_SERVER_ERROR`: Respuesta 500 Internal Server Error.

```rust
const OK_RESPONSE: &str = "HTTP/1.1 200 OK
Content-Type: application/json

";
const NOT_FOUND: &str = "HTTP/1.1 404 NOT FOUND

";
const INTERNAL_SERVER_ERROR: &str = "HTTP/1.1 500 INTERNAL SERVER ERROR

";
```

## Dependencias

Este proyecto utiliza las siguientes dependencias de Rust:

- **postgres**: Para la conexión y manipulación de la base de datos PostgreSQL.
- **serde**: Para la serialización y deserialización de datos JSON.
- **std**: Para las funcionalidades básicas de I/O, manejo de sockets y variables de entorno.

## Instalación y Configuración

1. Clona el repositorio.
2. Asegúrate de tener PostgreSQL instalado y ejecutándose.
3. Establece la variable de entorno `DATABASE_URL` con la cadena de conexión a tu base de datos.
4. Ejecuta el proyecto:

```bash
cargo run
```

El servidor estará escuchando en el puerto `8080`.

## Estructura de la Base de Datos

La tabla `users` tiene la siguiente estructura:

```sql
CREATE TABLE IF NOT EXISTS users (
    id SERIAL PRIMARY KEY,
    name VARCHAR NOT NULL,
    email VARCHAR NOT NULL
);
```
