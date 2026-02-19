# API REST - Sistema de Gestión de Usuarios

## Información General

- **Base URL:** `https://api.ejemplo.com/v1`
- **Formato de Respuesta:** JSON
- **Autenticación:** JWT (Bearer Token)

## Autenticación

Todas las peticiones (excepto login/registro) requieren un token JWT en el header:

```
Authorization: Bearer <tu_token_jwt>
```

## Estructura de Recursos

### Usuario
```json
{
  "id": "string",
  "email": "string",
  "nombre": "string",
  "apellido": "string",
  "rolId": "string",
  "perfilId": "string",
  "activo": "boolean",
  "fechaCreacion": "datetime",
  "fechaActualizacion": "datetime"
}
```

### Rol
```json
{
  "id": "string",
  "nombre": "string",
  "descripcion": "string",
  "permisos": ["string"]
}
```

### Perfil
```json
{
  "id": "string",
  "nombre": "string",
  "avatar": "string",
  "telefono": "string",
  "direccion": "string"
}
```

## Endpoints

### Tabla de Endpoints

| Método | Ruta | Descripción | Códigos de Respuesta |
|--------|------|-------------|----------------------|
| POST | `/auth/registro` | Registrar nuevo usuario | 201, 400, 409, 500 |
| POST | `/auth/login` | Iniciar sesión | 200, 400, 401, 500 |
| GET | `/usuarios` | Listar todos los usuarios | 200, 401, 403, 500 |
| GET | `/usuarios/{id}` | Obtener usuario por ID | 200, 401, 404, 500 |
| POST | `/usuarios` | Crear nuevo usuario | 201, 400, 401, 409, 500 |
| PUT | `/usuarios/{id}` | Actualizar usuario existente | 200, 400, 401, 404, 500 |
| PATCH | `/usuarios/{id}` | Actualizar parcialmente usuario | 200, 400, 401, 404, 500 |
| DELETE | `/usuarios/{id}` | Eliminar usuario | 204, 401, 404, 500 |
| GET | `/roles` | Listar roles | 200, 401, 500 |
| GET | `/perfiles` | Listar perfiles | 200, 401, 500 |

## Ejemplos de Requests y Responses

### Registro de Usuario

**Request:**
```http
POST /auth/registro
Content-Type: application/json

{
  "email": "usuario@ejemplo.com",
  "password": "Password123!",
  "nombre": "Juan",
  "apellido": "Pérez",
  "rolId": "rol_123",
  "perfilId": "perfil_456"
}
```

**Response Exitosa (201 Created):**
```json
{
  "id": "usr_789",
  "email": "usuario@ejemplo.com",
  "nombre": "Juan",
  "apellido": "Pérez",
  "rolId": "rol_123",
  "perfilId": "perfil_456",
  "activo": true,
  "fechaCreacion": "2024-01-15T10:30:00Z",
  "fechaActualizacion": "2024-01-15T10:30:00Z"
}
```

**Response Error (400 Bad Request):**
```json
{
  "error": "VALIDATION_ERROR",
  "message": "Datos de entrada inválidos",
  "details": [
    {
      "campo": "email",
      "mensaje": "El email no tiene un formato válido"
    },
    {
      "campo": "password",
      "mensaje": "La contraseña debe tener al menos 8 caracteres"
    }
  ]
}
```

### Login

**Request:**
```http
POST /auth/login
Content-Type: application/json

{
  "email": "usuario@ejemplo.com",
  "password": "Password123!"
}
```

**Response Exitosa (200 OK):**
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJ1c3JfNzg5IiwibmFtZSI6Ikp1YW4gUMOpcmV6Iiwicm9sIjoiYWRtaW4iLCJpYXQiOjE1MTYyMzkwMjJ9",
  "tokenType": "Bearer",
  "expiresIn": 3600,
  "usuario": {
    "id": "usr_789",
    "email": "usuario@ejemplo.com",
    "nombre": "Juan",
    "apellido": "Pérez"
  }
}
```

**Response Error (401 Unauthorized):**
```json
{
  "error": "INVALID_CREDENTIALS",
  "message": "Email o contraseña incorrectos"
}
```

### Listar Usuarios

**Request:**
```http
GET /usuarios?page=1&limit=10&rol=admin
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Response Exitosa (200 OK):**
```json
{
  "data": [
    {
      "id": "usr_789",
      "email": "usuario@ejemplo.com",
      "nombre": "Juan",
      "apellido": "Pérez",
      "rolId": "rol_123",
      "perfilId": "perfil_456",
      "activo": true,
      "fechaCreacion": "2024-01-15T10:30:00Z"
    },
    {
      "id": "usr_790",
      "email": "maria@ejemplo.com",
      "nombre": "María",
      "apellido": "García",
      "rolId": "rol_124",
      "perfilId": "perfil_457",
      "activo": true,
      "fechaCreacion": "2024-01-16T09:15:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 45,
    "pages": 5
  }
}
```

### Obtener Usuario por ID

**Request:**
```http
GET /usuarios/usr_789
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Response Exitosa (200 OK):**
```json
{
  "id": "usr_789",
  "email": "usuario@ejemplo.com",
  "nombre": "Juan",
  "apellido": "Pérez",
  "rolId": "rol_123",
  "perfilId": "perfil_456",
  "activo": true,
  "fechaCreacion": "2024-01-15T10:30:00Z",
  "fechaActualizacion": "2024-01-15T10:30:00Z"
}
```

**Response Error (404 Not Found):**
```json
{
  "error": "USER_NOT_FOUND",
  "message": "No se encontró un usuario con el ID proporcionado"
}
```

### Crear Usuario (requiere autenticación)

**Request:**
```http
POST /usuarios
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json

{
  "email": "nuevo@ejemplo.com",
  "password": "Password123!",
  "nombre": "Carlos",
  "apellido": "Rodríguez",
  "rolId": "rol_125",
  "perfilId": "perfil_458"
}
```

**Response Exitosa (201 Created):**
```json
{
  "id": "usr_791",
  "email": "nuevo@ejemplo.com",
  "nombre": "Carlos",
  "apellido": "Rodríguez",
  "rolId": "rol_125",
  "perfilId": "perfil_458",
  "activo": true,
  "fechaCreacion": "2024-01-17T14:20:00Z",
  "fechaActualizacion": "2024-01-17T14:20:00Z"
}
```

**Response Error (409 Conflict):**
```json
{
  "error": "EMAIL_EXISTS",
  "message": "El email proporcionado ya está registrado"
}
```

### Actualizar Usuario

**Request:**
```http
PUT /usuarios/usr_789
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json

{
  "email": "juan.actualizado@ejemplo.com",
  "nombre": "Juan Carlos",
  "apellido": "Pérez Gómez",
  "rolId": "rol_126"
}
```

**Response Exitosa (200 OK):**
```json
{
  "id": "usr_789",
  "email": "juan.actualizado@ejemplo.com",
  "nombre": "Juan Carlos",
  "apellido": "Pérez Gómez",
  "rolId": "rol_126",
  "perfilId": "perfil_456",
  "activo": true,
  "fechaCreacion": "2024-01-15T10:30:00Z",
  "fechaActualizacion": "2024-01-17T15:45:00Z"
}
```

### Actualización Parcial (PATCH)

**Request:**
```http
PATCH /usuarios/usr_789
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json

{
  "nombre": "Juan Carlos"
}
```

**Response Exitosa (200 OK):**
```json
{
  "id": "usr_789",
  "email": "juan.actualizado@ejemplo.com",
  "nombre": "Juan Carlos",
  "apellido": "Pérez Gómez",
  "rolId": "rol_126",
  "perfilId": "perfil_456",
  "activo": true,
  "fechaCreacion": "2024-01-15T10:30:00Z",
  "fechaActualizacion": "2024-01-17T16:00:00Z"
}
```

### Eliminar Usuario

**Request:**
```http
DELETE /usuarios/usr_789
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Response Exitosa (204 No Content)**

*No hay contenido en el cuerpo de la respuesta*

### Listar Roles

**Request:**
```http
GET /roles
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Response Exitosa (200 OK):**
```json
{
  "data": [
    {
      "id": "rol_123",
      "nombre": "Administrador",
      "descripcion": "Acceso total al sistema",
      "permisos": ["crear_usuario", "editar_usuario", "eliminar_usuario", "ver_reportes"]
    },
    {
      "id": "rol_124",
      "nombre": "Editor",
      "descripcion": "Puede editar contenido",
      "permisos": ["editar_contenido", "ver_contenido"]
    },
    {
      "id": "rol_125",
      "nombre": "Usuario",
      "descripcion": "Acceso básico",
      "permisos": ["ver_contenido"]
    }
  ]
}
```

### Listar Perfiles

**Request:**
```http
GET /perfiles
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Response Exitosa (200 OK):**
```json
{
  "data": [
    {
      "id": "perfil_456",
      "nombre": "Perfil Ejecutivo",
      "avatar": "https://ejemplo.com/avatars/ejecutivo.png",
      "telefono": "+123456789",
      "direccion": "Av. Principal 123"
    },
    {
      "id": "perfil_457",
      "nombre": "Perfil Estándar",
      "avatar": "https://ejemplo.com/avatars/estandar.png",
      "telefono": null,
      "direccion": null
    }
  ]
}
```

## Códigos de Respuesta HTTP

| Código | Descripción |
|--------|-------------|
| 200 | OK - La petición fue exitosa |
| 201 | Created - Recurso creado exitosamente |
| 204 | No Content - Petición exitosa sin contenido en la respuesta |
| 400 | Bad Request - La petición tiene errores de validación |
| 401 | Unauthorized - No autenticado o token inválido |
| 403 | Forbidden - No tiene permisos para acceder al recurso |
| 404 | Not Found - Recurso no encontrado |
| 409 | Conflict - Conflicto con el estado actual del recurso |
| 500 | Internal Server Error - Error interno del servidor |