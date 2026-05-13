# 06 - Referencia de la API

## SACP: Documentacion de Endpoints REST

---

## Informacion General

| Aspecto | Valor |
|---|---|
| **Base URL** | `/api` |
| **Autenticacion** | JWT Bearer Token |
| **Formato** | JSON |
| **Prefijo global** | `/api` |
| **Swagger** | `/api/docs` |
| **Version** | 1.0 |

### Roles del Sistema

| Rol | Descripcion |
|---|---|
| `ADMINISTRADOR` | Acceso total, ve todos los parqueos |
| `USUARIO` | Operador de parqueo, ve solo sus parqueos asignados |

### Headers Comunes

```
Authorization: Bearer <access_token>
Content-Type: application/json
```

### Respuestas Estandar

**Exito:**
```json
{ "statusCode": 200, "message": "Operacion exitosa" }
```

**Error:**
```json
{ "statusCode": 400, "message": "Datos invalidos", "error": "Bad Request" }
```

**Listado paginado (ListadoDto):**
```json
{
  "header": ["id", "Nombre", "Email"],
  "key": ["id", "userName", "email"],
  "data": {
    "items": [...],
    "meta": { "totalItems": 100, "itemCount": 10, "itemsPerPage": 10, "totalPages": 10, "currentPage": 1 },
    "links": { "first": "...", "last": "...", "next": "...", "previous": "..." }
  }
}
```

---

## 1. Auth (`/api/auth`)

### POST /api/auth/signin
Iniciar sesion y obtener tokens JWT.

| Atributo | Valor |
|---|---|
| **Auth** | Publico |
| **Body** | `AuthCredentialsDto` |

**Request:**
```json
{
  "userName": "admin",
  "password": "Admin1234"
}
```

**Response (200):**
```json
{
  "accessToken": "eyJhbGciOiJIUzI1NiIs...",
  "refreshToken": "eyJhbGciOiJIUzI1NiIs...",
  "expiresIn": 900,
  "user": {
    "id": "507f1f77bcf86cd799439011",
    "userName": "admin",
    "email": "admin@sacp.cu",
    "roles": [{"id": "...", "nombre": "ADMINISTRADOR"}],
    "funciones": [...],
    "parqueos": [...]
  }
}
```

---

### POST /api/auth/signup
Registrar un nuevo usuario.

| Atributo | Valor |
|---|---|
| **Auth** | Publico |
| **Body** | `UserDto` |

**Request:**
```json
{
  "userName": "operador1",
  "password": "Operador123",
  "email": "operador@sacp.cu",
  "roleIds": ["rol-id-2"],
  "funcionIds": ["func-id-1"],
  "parqueoIds": ["parqueo-id-1"]
}
```

**Response (201):**
```json
{ "statusCode": 201, "message": "Usuario creado exitosamente" }
```

---

### POST /api/auth/refresh-tokens
Obtener nuevos tokens usando refresh token.

| Atributo | Valor |
|---|---|
| **Auth** | Bearer (refresh strategy) |
| **Body** | `RefreshTokenDto` |

**Request:**
```json
{ "refreshToken": "eyJhbGciOiJIUzI1NiIs..." }
```

**Response (200):** Mismo formato que signin.

---

### POST /api/auth/logout
Cerrar sesion e invalidar refresh token.

| Atributo | Valor |
|---|---|
| **Auth** | JWT |
| **Body** | Ninguno |

**Response (200):**
```json
{ "statusCode": 200, "message": "Sesion cerrada exitosamente" }
```

---

### PATCH /api/auth/request/reset/password
Solicitar codigo de recuperacion de contrasena por email.

| Atributo | Valor |
|---|---|
| **Auth** | Publico |
| **Body** | `RequestResetPasswordDto` |

**Request:**
```json
{ "email": "usuario@sacp.cu" }
```

---

### PATCH /api/auth/reset/password
Restablecer contrasena con codigo de verificacion.

| Atributo | Valor |
|---|---|
| **Auth** | Publico |
| **Body** | `ResetPasswordDto` |

**Request:**
```json
{
  "email": "usuario@sacp.cu",
  "code": 123456,
  "newPassword": "NuevaClave123"
}
```

---

### PATCH /api/auth/change/password
Cambiar contrasena del usuario autenticado.

| Atributo | Valor |
|---|---|
| **Auth** | JWT + USUARIO |
| **Body** | `ChangePasswordDto` |

**Request:**
```json
{
  "currentPassword": "ViejaClave123",
  "newPassword": "NuevaClave456"
}
```

---

## 2. QR (`/api/qr`)

**Auth requerida en todos los endpoints:** JWT + RolGuard + PermissionGuard

### POST /api/qr/generar
Generar un lote de codigos QR.

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |
| **Body** | `GenerateQrDto` |

**Request:**
```json
{
  "tipoMedioId": "507f1f77bcf86cd799439011",
  "parqueoId": "507f1f77bcf86cd799439012",
  "cantidad": 50
}
```
> Nota: USUARIO no necesita enviar parqueoId, se usa el suyo asignado.

**Response (201):**
```json
{
  "loteId": "lote-2025-03-04-001",
  "cantidad": 50,
  "primerQR": "QR-000101",
  "ultimoQR": "QR-000150",
  "tipoMedio": "Bicicleta",
  "parqueo": "Parqueo # 1",
  "fechaGeneracion": "2025-03-04T08:00:00Z"
}
```

---

### GET /api/qr
Listar QRs generados con paginacion.

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |
| **Query** | `page` (default: 1), `limit` (default: 10) |

**Response (200):** ListadoDto con QRs.

---

### GET /api/qr/lotes
Obtener informacion de todos los lotes generados.

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |

**Response (200):**
```json
[
  {
    "loteId": "lote-2025-03-04-001",
    "tipoMedio": "Bicicleta",
    "parqueo": "Parqueo # 1",
    "cantidad": 50,
    "disponibles": 30,
    "fechaGeneracion": "2025-03-04T08:00:00Z"
  }
]
```

---

### GET /api/qr/lote/:loteId
Obtener QRs de un lote especifico.

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |

---

### GET /api/qr/pdf/:loteId
Descargar PDF con tarjetas QR de un lote.

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |
| **Response** | `application/pdf` |

---

### GET /api/qr/:id
Obtener un QR por ID.

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |

---

### GET /api/qr/codigo/:codigo
Obtener un QR por su codigo.

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |

---

### GET /api/qr/tipo-medio/:tipoMedioId
Obtener QRs por tipo de medio.

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |

---

### GET /api/qr/disponibles/:tipoMedioId
Contar QRs disponibles por tipo de medio.

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |

**Response (200):**
```json
{ "cantidad": 30 }
```

---

### DELETE /api/qr/:id
Eliminar un QR (borrado virtual).

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |

---

### DELETE /api/qr/elementos/multiples
Eliminar multiples QRs (borrado virtual).

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |
| **Body** | `string[]` (array de IDs) |

---

### DELETE /api/qr/lote/:loteId
Eliminar todos los QRs de un lote (borrado virtual).

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |

---

### PATCH /api/qr/:id/anular
Anular un QR (cambiar estado a "anulado").

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |

---

### PATCH /api/qr/anular/elementos/multiples
Anular multiples QRs.

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |
| **Body** | `string[]` (array de IDs) |

---

## 3. Movimientos (`/api/movimiento`)

**Auth requerida:** JWT + RolGuard + PermissionGuard

### GET /api/movimiento/activos
Obtener vehiculos actualmente dentro del parqueo.

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |
| **Query** | `parqueoId` (opcional) |

**Response (200):**
```json
[
  {
    "id": "...",
    "tipoMedio": "Bicicleta",
    "fechaEntrada": "2025-03-04T08:30:00Z",
    "parqueo": "Parqueo # 1",
    "qrEscaneado": "QR-000101"
  }
]
```

---

### GET /api/movimiento/resumen/:parqueoId
Obtener resumen del parqueo.

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |

**Response (200):** `ResumenParqueoDto`

---

### GET /api/movimiento/verificar/:qrEscaneado
Verificar estado de un QR.

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |

**Response (200):**
```json
{
  "dentro": true,
  "movimiento": {
    "id": "...",
    "tipoMedio": "Bicicleta",
    "fechaEntrada": "2025-03-04T08:30:00Z",
    "parqueo": "Parqueo # 1",
    "precioUnitarioCobrado": 10.50,
    "qrEscaneado": "QR-000101"
  }
}
```

---

### GET /api/movimiento
Listar movimientos con paginacion.

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR |
| **Query** | `page`, `limit`, `sinPaginacion` |

---

### GET /api/movimiento/:id
Obtener un movimiento por ID.

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |

---

### POST /api/movimiento
Crear un movimiento.

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |
| **Body** | `CreateMovimientoDto` |

**Request:**
```json
{
  "tipoMedioId": "...",
  "parqueoId": "...",
  "precioId": "...",
  "qrEscaneado": "QR-000101",
  "fechaEntrada": "2025-03-04T08:30:00Z"
}
```

---

### POST /api/movimiento/multiple
Crear multiples movimientos.

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |
| **Body** | `CreateMovimientoDto[]` |

---

### PATCH /api/movimiento/:id
Actualizar un movimiento.

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |
| **Body** | `UpdateMovimientoDto` |

---

### DELETE /api/movimiento/:id
Eliminar un movimiento (borrado virtual).

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |

---

### DELETE /api/movimiento/elementos/multiples
Eliminar multiples movimientos (borrado virtual).

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |
| **Body** | `string[]` |

---

### POST /api/movimiento/filtrar
Filtrar movimientos por parametros.

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |
| **Body** | `FiltroGenericoDto` |
| **Query** | `page`, `limit` |

---

### POST /api/movimiento/buscar
Buscar movimientos por texto.

| Atributo | Valor |
|---|---|
| **Roles** | ADMINISTRADOR, USUARIO |
| **Body** | `BuscarDto` |
| **Query** | `page`, `limit` |

---

## 4. Precios (`/api/precio`)

**Auth requerida:** JWT + RolGuard + PermissionGuard  
**Todos los endpoints requieren rol ADMINISTRADOR**

| Metodo | Path | Descripcion |
|---|---|---|
| GET | `/` | Listar precios con paginacion |
| GET | `/:id` | Obtener precio por ID |
| POST | `/elementos/multiples` | Obtener multiples precios por IDs |
| GET | `/crear/select` | Obtener precios para select |
| POST | `/` | Crear precio |
| POST | `/multiple` | Crear multiples precios |
| POST | `/importar/elementos` | Importar precios masivamente |
| PATCH | `/:id` | Actualizar precio |
| PATCH | `/elementos/multiples` | Actualizar multiples precios |
| DELETE | `/:id` | Eliminar precio (borrado virtual) |
| DELETE | `/elementos/multiples` | Eliminar multiples precios (borrado virtual) |
| POST | `/filtrar` | Filtrar precios |
| POST | `/buscar` | Buscar precios |

**CreatePrecioDto:**
```json
{
  "tipoMedioId": "...",
  "parqueoId": "...",
  "valor": 10.50,
  "fechaInicioVigencia": "2025-01-01T00:00:00Z",
  "fechaFinVigencia": "2025-12-31T23:59:59Z"
}
```

---

## 5. Users (`/api/user`)

| Metodo | Path | Descripcion | Roles |
|---|---|---|---|
| GET | `/` | Listar usuarios | ADMINISTRADOR |
| GET | `/:id` | Obtener usuario por ID | ADMINISTRADOR |
| POST | `/` | Crear usuario | ADMINISTRADOR |
| PATCH | `/:id` | Actualizar usuario | ADMINISTRADOR |
| DELETE | `/:id` | Eliminar usuario | ADMINISTRADOR |
| DELETE | `/elementos/multiples` | Eliminar multiples usuarios | ADMINISTRADOR |
| POST | `/filtrar` | Filtrar usuarios | ADMINISTRADOR |
| POST | `/buscar` | Buscar usuarios | ADMINISTRADOR |
| PATCH | `/:id/change/password` | Cambiar contrasena de usuario | ADMINISTRADOR |
| GET | `/crear/select` | Obtener usuarios para select | Publico |

**CreateUserDto:**
```json
{
  "userName": "operador1",
  "password": "Operador123",
  "email": "operador@sacp.cu",
  "roleIds": ["..."],
  "funcionIds": ["..."],
  "parqueoIds": ["..."]
}
```

---

## 6. Roles (`/api/rol`)

| Metodo | Path | Descripcion | Roles |
|---|---|---|---|
| GET | `/` | Listar roles | ADMINISTRADOR |
| GET | `/:id` | Obtener rol por ID | ADMINISTRADOR |
| POST | `/elementos/multiples` | Obtener multiples roles | ADMINISTRADOR |
| GET | `/crear/select` | Obtener roles para select | ADMINISTRADOR |
| POST | `/crear/select/dependiente` | Select dependiente | ADMINISTRADOR |
| POST | `/` | Crear rol | ADMINISTRADOR |
| POST | `/multiple` | Crear multiples roles | ADMINISTRADOR |
| PATCH | `/:id` | Actualizar rol | ADMINISTRADOR |
| PATCH | `/elementos/multiples` | Actualizar multiples roles | ADMINISTRADOR |
| DELETE | `/:id` | Eliminar rol (borrado virtual) | ADMINISTRADOR |
| DELETE | `/elementos/multiples` | Eliminar multiples roles | ADMINISTRADOR |
| POST | `/filtrar` | Filtrar roles | ADMINISTRADOR |
| POST | `/buscar` | Buscar roles | ADMINISTRADOR |

---

## 7. Funciones (`/api/funcion`)

| Metodo | Path | Descripcion | Roles |
|---|---|---|---|
| GET | `/` | Listar funciones | ADMINISTRADOR |
| GET | `/:id` | Obtener funcion por ID | ADMINISTRADOR |
| POST | `/elementos/multiples` | Obtener multiples funciones | ADMINISTRADOR |
| GET | `/crear/select` | Obtener funciones para select | ADMINISTRADOR |
| POST | `/` | Crear funcion | ADMINISTRADOR |
| POST | `/multiple` | Crear multiples funciones | ADMINISTRADOR |
| PATCH | `/:id` | Actualizar funcion | ADMINISTRADOR |
| PATCH | `/elementos/multiples` | Actualizar multiples funciones | ADMINISTRADOR |
| DELETE | `/:id` | Eliminar funcion (borrado virtual) | ADMINISTRADOR |
| DELETE | `/elementos/multiples` | Eliminar multiples funciones | ADMINISTRADOR |
| POST | `/filtrar` | Filtrar funciones | ADMINISTRADOR |
| POST | `/buscar` | Buscar funciones | ADMINISTRADOR |

---

## 8. Menus (`/api/menu`)

| Metodo | Path | Descripcion | Roles |
|---|---|---|---|
| GET | `/` | Listar menus | ADMINISTRADOR |
| GET | `/:id` | Obtener menu por ID | ADMINISTRADOR |
| GET | `/tipo/:tipo` | Obtener menus por tipo | Publico |
| POST | `/elementos/multiples` | Obtener multiples menus | ADMINISTRADOR |
| POST | `/` | Crear menu | ADMINISTRADOR |
| POST | `/multiple` | Crear multiples menus | ADMINISTRADOR |
| PATCH | `/:id` | Actualizar menu | ADMINISTRADOR |
| PATCH | `/elementos/multiples` | Actualizar multiples menus | ADMINISTRADOR |
| DELETE | `/:id` | Eliminar menu (borrado virtual) | ADMINISTRADOR |
| DELETE | `/elementos/multiples` | Eliminar multiples menus | ADMINISTRADOR |
| POST | `/filtrar` | Filtrar menus | ADMINISTRADOR |
| POST | `/buscar` | Buscar menus | ADMINISTRADOR |

---

## 9. Nomencladores (`/api/nomenclador`)

Controlador generico que maneja multiples nomencladores. Parametro `name` define el tipo: `tipoMedio`, `parqueo`.

| Metodo | Path | Descripcion | Auth |
|---|---|---|---|
| GET | `/` | Listar nombres de nomencladores | Publico |
| GET | `/:name/:id` | Obtener nomenclador por ID | JWT |
| GET | `/:name/listado/elementos` | Listar elementos del nomenclador | JWT |
| GET | `/:name/create/select` | Obtener para select | Publico |
| GET | `/:name/cantidad/elementos` | Contar elementos | Publico |
| POST | `/:name` | Crear elemento | JWT |
| POST | `/:name/multiple` | Crear multiples elementos | JWT |
| POST | `/:name/importar/elementos` | Importar masivamente | JWT |
| PATCH | `/:name/:id` | Actualizar elemento | JWT |
| PATCH | `/:name/elementos/multiples` | Actualizar multiples | JWT |
| DELETE | `/:name/:id` | Eliminar (borrado virtual) | JWT |
| DELETE | `/:name/elementos/multiples` | Eliminar multiples (virtual) | JWT |
| DELETE | `/:name/:id/delete/real` | Eliminar (borrado real) | JWT |
| DELETE | `/:name/delete/real/elementos/multiples` | Eliminar multiples (real) | JWT |
| POST | `/:name/filtrar/por` | Filtrar elementos | JWT |
| POST | `/:name/buscar` | Buscar elementos | JWT |
| POST | `/:name/create/select/dependiente` | Select dependiente | Publico |

---

## 10. Log History (`/api/log-history`)

**Todos los endpoints requieren rol ADMINISTRADOR**

| Metodo | Path | Descripcion |
|---|---|---|
| GET | `/` | Listar trazas con paginacion |
| GET | `/:id` | Obtener traza por ID |
| DELETE | `/:id` | Eliminar traza |
| POST | `/filtro/por` | Filtrar trazas por usuario y parametros |

---

## 11. Sincronizacion (`/api/sync`)

| Metodo | Path | Descripcion | Roles |
|---|---|---|---|
| POST | `/` | Sincronizar movimientos pendientes | ADMINISTRADOR, USUARIO |
| GET | `/status` | Obtener estado de sincronizacion | ADMINISTRADOR, USUARIO |

**SyncRequestDto:**
```json
{
  "movimientos": [
    {
      "operacion": "entrada",
      "data": { "qrCodigo": "QR-000101", "parqueoId": "...", "tipoMedioId": "...", "precioId": "...", "usuarioId": "..." }
    }
  ]
}
```

**SyncResponseDto:**
```json
{
  "exitosos": 5,
  "errores": 0,
  "detalles": [...],
  "datosActualizados": {
    "precios": [...],
    "tiposMedio": [...],
    "movimientos": [...]
  }
}
```

---

## 12. Business Intelligence (`/api/bi`)

| Metodo | Path | Descripcion | Roles |
|---|---|---|---|
| GET | `/dashboard` | Dashboard consolidado | ADMINISTRADOR, USUARIO |
| POST | `/dashboard/filtrado` | Dashboard con filtros avanzados | ADMINISTRADOR, USUARIO |
| GET | `/kpis` | KPIs del negocio | ADMINISTRADOR, USUARIO |
| GET | `/comparacion-periodos/:parqueoId` | Comparacion entre periodos | ADMINISTRADOR, USUARIO |
| GET | `/ingresos/:parqueoId` | Reporte de ingresos por parqueo | ADMINISTRADOR, USUARIO |
| GET | `/comparativa-parqueos` | Comparativa entre parqueos | ADMINISTRADOR, USUARIO |
| GET | `/analisis-tipo-medio` | Analisis por tipo de medio | ADMINISTRADOR, USUARIO |
| GET | `/tendencias` | Tendencia de ingresos | ADMINISTRADOR, USUARIO |

**Query params comunes:** `fechaInicio`, `fechaFin`, `tipoPeriodo` (dia, semana, mes, trimestre, anio), `parqueoId`

---

*Documento generado como parte de la metodologia SXP (Scrum + XP Fusion)*  
*Ultima actualizacion: Marzo 2025*
