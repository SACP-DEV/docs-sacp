# 07 - Modelo de Datos

## SACP: Colecciones MongoDB, Relaciones e Indices

---

## 1. Vision General

### 1.1 Base de Datos

| Aspecto | Valor |
|---|---|
| **Motor** | MongoDB 6+ |
| **ORM** | TypeORM con driver MongoDB |
| **Patron de IDs** | ObjectId nativo de MongoDB |
| **Patron de relaciones** | Referencias manuales (almacenamiento de ObjectId como string) |
| **Soft Delete** | Campo `activo` booleano en todas las entidades |

### 1.2 Diagrama de Entidades

```
┌──────────────┐       ┌──────────────┐       ┌──────────────┐
│    User      │──m:n──│     Rol      │──m:n──│   Funcion    │
│   (user)     │       │    (rol)     │       │  (funcion)   │
└──────┬───────┘       └──────────────┘       └──────┬───────┘
       │                                              │
       │ m:n                                    m:n   │
       ▼                                              ▼
┌──────────────┐                              ┌──────────────┐
│   Parqueo    │                              │  EndPoint    │
│(nom_parqueo) │                              │ (end_point)  │
└──────┬───────┘                              └──────────────┘
       │
       │ 1:n
       ├──────────────────────┐
       ▼                      ▼
┌──────────────┐       ┌──────────────┐
│  Movimiento  │       │   Precio     │
│(movimiento)  │       │  (precio)    │
└──────┬───────┘       └──────┬───────┘
       │                      │
       │ n:1                  │ n:1
       ▼                      ▼
┌──────────────┐       ┌──────────────┐
│ TipoMedio    │       │ TipoMedio    │
│(nom_tipo_    │       │(nom_tipo_    │
│   medio)     │       │   medio)     │
└──────────────┘       └──────────────┘

┌──────────────┐       ┌──────────────┐       ┌──────────────┐
│     QR       │       │  LogHistory  │       │   Menu       │
│    (qr)      │       │(log_history) │       │   (menu)     │
└──────────────┘       └──────────────┘       └──────────────┘

┌──────────────┐
│ RegistroDiario│
│(registro_    │
│   diario)    │
└──────────────┘
```

📸 **Captura pendiente**: Diagrama ER del modelo de datos

---

## 2. Entidad Base: GenericEntity

Todas las entidades heredan de `GenericEntity` que proporciona:

| Campo | Tipo | Descripcion |
|---|---|---|
| `_id` | ObjectId | ID unico de MongoDB |
| `activo` | Boolean | Indicador de registro activo (default: true) |
| `createdAt` | Date | Fecha de creacion (auto) |
| `updatedAt` | Date | Fecha de ultima modificacion (auto) |

**Indices heredados:** `_id` (PK), `activo` (index), `createdAt` (index)

---

## 3. Entidad Base: GenericNomencladorEntity

Los nomencladores heredan de `GenericNomencladorEntity` que extiende `GenericEntity`:

| Campo | Tipo | Restricciones | Descripcion |
|---|---|---|---|
| `nombre` | String | unique, min 2, max 100 | Nombre unico del nomenclador |
| `descripcion` | String | min 1, max 500 | Descripcion detallada |

**Indices adicionales:** `nombre` (unique index)

---

## 4. Colecciones Detalladas

### 4.1 `user` — Usuarios del Sistema

| Campo | Tipo | Restricciones | Indice | Descripcion |
|---|---|---|---|---|
| `_id` | ObjectId | PK | PK | ID unico |
| `activo` | Boolean | default: true | Index | Registro activo |
| `userName` | String | unique, min 4, max 20 | **Unique** | Nombre de usuario |
| `email` | String | email, max 255 | Index | Correo electronico |
| `password` | String | min 8, max 255 | | Contrasena hasheada (bcrypt) |
| `refreshToken` | String | opcional | | Token de refresco |
| `refreshTokenExp` | Date | opcional | | Expiracion del refresh token |
| `salt` | String | opcional | | Salt para hash de contrasena |
| `roleIds` | String[] | opcional | Index | IDs de roles asignados |
| `funcionIds` | String[] | opcional | Index | IDs de funciones asignadas |
| `resetPasswordCode` | Number | opcional | | Codigo de recuperacion |
| `parqueoIds` | String[] | opcional | Index | IDs de parqueos asignados |
| `createdAt` | Date | auto | Index | Fecha de creacion |
| `updatedAt` | Date | auto | | Fecha de modificacion |

**Documento de ejemplo:**
```json
{
  "_id": ObjectId("65e4a1b2c3d4e5f6a7b8c9d0"),
  "activo": true,
  "userName": "admin",
  "email": "admin@sacp.cu",
  "password": "$2a$10$X5t3kH9vM2rQ7wL1nY4bZ...",
  "salt": "$2a$10$X5t3kH9vM2rQ7wL1nY4bZ",
  "roleIds": ["65e4a1b2c3d4e5f6a7b8c9d1"],
  "funcionIds": ["65e4a1b2c3d4e5f6a7b8c9d2"],
  "parqueoIds": ["65e4a1b2c3d4e5f6a7b8c9d3"],
  "createdAt": ISODate("2025-01-15T08:00:00Z"),
  "updatedAt": ISODate("2025-03-01T10:30:00Z")
}
```

---

### 4.2 `rol` — Roles del Sistema

| Campo | Tipo | Restricciones | Indice | Descripcion |
|---|---|---|---|---|
| `_id` | ObjectId | PK | PK | ID unico |
| `activo` | Boolean | default: true | Index | Registro activo |
| `nombre` | String | unique, min 3, max 50 | **Unique** | Nombre del rol |
| `descripcion` | String | max 255 | | Descripcion del rol |
| `userIds` | String[] | opcional | | IDs de usuarios con este rol |
| `funcionIds` | String[] | opcional | | IDs de funciones del rol |
| `createdAt` | Date | auto | Index | Fecha de creacion |
| `updatedAt` | Date | auto | | Fecha de modificacion |

**Valores por defecto del sistema:**
- `ADMINISTRADOR` — Acceso total al sistema
- `USUARIO` — Operador de parqueo

---

### 4.3 `funcion` — Funciones/Permisos

| Campo | Tipo | Restricciones | Indice | Descripcion |
|---|---|---|---|---|
| `_id` | ObjectId | PK | PK | ID unico |
| `activo` | Boolean | default: true | Index | Registro activo |
| `nombre` | String | max 100 | Index | Nombre de la funcion |
| `descripcion` | String | max 500 | | Descripcion |
| `menuId` | String | opcional | | ID del menu asociado |
| `endPointIds` | String[] | opcional | Index | IDs de endpoints de la funcion |
| `userIds` | String[] | opcional | Index | IDs de usuarios con esta funcion |
| `roleIds` | String[] | opcional | Index | IDs de roles con esta funcion |
| `createdAt` | Date | auto | Index | Fecha de creacion |
| `updatedAt` | Date | auto | | Fecha de modificacion |

---

### 4.4 `end_point` — Endpoints del Sistema

| Campo | Tipo | Restricciones | Indice | Descripcion |
|---|---|---|---|---|
| `_id` | ObjectId | PK | PK | ID unico |
| `controller` | String | max 255 | | Nombre del controlador |
| `servicio` | String | max 255 | | Nombre del servicio |
| `ruta` | String | max 255 | | Ruta del endpoint |
| `nombre` | String | max 255 | | Nombre descriptivo |
| `metodo` | String | max 20 | Index | Metodo HTTP (GET/POST/PATCH/DELETE) |
| `funcionIds` | String[] | opcional | | IDs de funciones que usan este endpoint |

**Nota:** Los endpoints se generan automaticamente en bootstrap analizando los controladores con `parseController`.

---

### 4.5 `menu` — Menus del Sistema

| Campo | Tipo | Restricciones | Indice | Descripcion |
|---|---|---|---|---|
| `_id` | ObjectId | PK | PK | ID unico |
| `activo` | Boolean | default: true | Index | Registro activo |
| `label` | String | max 100 | | Etiqueta visible |
| `icon` | String | max 50 | | Clase de icono |
| `to` | String | max 255 | | Ruta/URL |
| `menuId` | String | opcional | | ID del menu padre (jerarquia) |
| `tipo` | String | enum: interno/externo | Index | Tipo de menu (default: "interno") |
| `nomenclador` | String | max 255, opcional | | Nomenclador asociado |
| `createdAt` | Date | auto | Index | Fecha de creacion |
| `updatedAt` | Date | auto | | Fecha de modificacion |

---

### 4.6 `qr` — Codigos QR

| Campo | Tipo | Restricciones | Indice | Descripcion |
|---|---|---|---|---|
| `_id` | ObjectId | PK | PK | ID unico |
| `activo` | Boolean | default: true | Index | Registro activo |
| `codigo` | String | unique, max 50 | **Unique** | Codigo unico (QR-XXXXXX) |
| `numeroConsecutivo` | Number | | | Numero consecutivo global |
| `tipoMedioId` | String | | Index | ID del tipo de medio |
| `tipoMedioNombre` | String | max 100 | | Nombre del tipo de medio (denormalizado) |
| `tipoMedioCodigo` | String | max 20 | | Codigo del tipo de medio (denormalizado) |
| `parqueoId` | String | | Index | ID del parqueo |
| `parqueoNombre` | String | max 100 | | Nombre del parqueo (denormalizado) |
| `contenido` | String (text) | | | JSON stringificado del contenido QR |
| `fechaGeneracion` | Date | default: now | Index | Fecha de generacion del lote |
| `loteId` | String | max 100 | Index | ID del lote de generacion |
| `estado` | String | max 20, default: "disponible" | Index | Estado: disponible, usado, anulado |
| `fechaEstado` | Date | opcional | | Fecha del ultimo cambio de estado |
| `createdAt` | Date | auto | Index | Fecha de creacion |
| `updatedAt` | Date | auto | | Fecha de modificacion |

**Documento de ejemplo:**
```json
{
  "_id": ObjectId("65e4a1b2c3d4e5f6a7b8c9d4"),
  "activo": true,
  "codigo": "QR-000101",
  "numeroConsecutivo": 101,
  "tipoMedioId": "65e4a1b2c3d4e5f6a7b8c9d5",
  "tipoMedioNombre": "Bicicleta",
  "tipoMedioCodigo": "BIC001",
  "parqueoId": "65e4a1b2c3d4e5f6a7b8c9d3",
  "parqueoNombre": "Parqueo # 1",
  "contenido": "{\"tipoMedioCodigo\":\"BIC001\",\"tipoMedioNombre\":\"Bicicleta\",\"numeroQr\":101}",
  "fechaGeneracion": ISODate("2025-03-04T08:00:00Z"),
  "loteId": "lote-2025-03-04-001",
  "estado": "disponible",
  "createdAt": ISODate("2025-03-04T08:00:00Z"),
  "updatedAt": ISODate("2025-03-04T08:00:00Z")
}
```

---

### 4.7 `movimiento` — Movimientos de Parqueo

| Campo | Tipo | Restricciones | Indice | Descripcion |
|---|---|---|---|---|
| `_id` | ObjectId | PK | PK | ID unico |
| `activo` | Boolean | default: true | Index | Registro activo |
| `tipoMedioId` | String | | Index | ID del tipo de medio |
| `fechaEntrada` | Date | | Index | Fecha/hora de entrada |
| `fechaSalida` | Date | opcional | | Fecha/hora de salida |
| `parqueoId` | String | | Index | ID del parqueo |
| `precioUnitarioCobrado` | Number (float) | max 99999.99 | | Precio cobrado |
| `precioId` | String | | | ID del precio aplicado |
| `qrEscaneado` | String | max 100 | | Codigo QR escaneado |
| `createdAt` | Date | auto | Index | Fecha de creacion |
| `updatedAt` | Date | auto | | Fecha de modificacion |

**Documento de ejemplo:**
```json
{
  "_id": ObjectId("65e4a1b2c3d4e5f6a7b8c9d6"),
  "activo": true,
  "tipoMedioId": "65e4a1b2c3d4e5f6a7b8c9d5",
  "fechaEntrada": ISODate("2025-03-04T08:30:00Z"),
  "fechaSalida": ISODate("2025-03-04T10:15:00Z"),
  "parqueoId": "65e4a1b2c3d4e5f6a7b8c9d3",
  "precioUnitarioCobrado": 10.50,
  "precioId": "65e4a1b2c3d4e5f6a7b8c9d7",
  "qrEscaneado": "QR-000101",
  "createdAt": ISODate("2025-03-04T08:30:00Z"),
  "updatedAt": ISODate("2025-03-04T10:15:00Z")
}
```

---

### 4.8 `precio` — Precios/Tarifas

| Campo | Tipo | Restricciones | Indice | Descripcion |
|---|---|---|---|---|
| `_id` | ObjectId | PK | PK | ID unico |
| `activo` | Boolean | default: true | Index | Registro activo |
| `tipoMedioId` | String | | Index | ID del tipo de medio |
| `parqueoId` | String | | Index | ID del parqueo |
| `valor` | Number (float) | max 999999.99 | | Monto del precio |
| `fechaInicioVigencia` | Date | default: now | | Inicio de vigencia |
| `fechaFinVigencia` | Date | opcional | | Fin de vigencia |
| `createdAt` | Date | auto | Index | Fecha de creacion |
| `updatedAt` | Date | auto | | Fecha de modificacion |

---

### 4.9 `log_history` — Trazas de Auditoria

| Campo | Tipo | Restricciones | Indice | Descripcion |
|---|---|---|---|---|
| `_id` | ObjectId | PK | PK | ID unico |
| `activo` | Boolean | default: true | Index | Registro activo |
| `userId` | String | opcional | Index | ID del usuario que realizo la accion |
| `date` | Date | auto | | Fecha de la accion |
| `tabla` | String | max 100 | | Nombre de la coleccion afectada |
| `action` | Enum | HISTORY_ACTION | Index | Accion realizada |
| `valorNuevo` | Object (JSON) | opcional | | Datos nuevos |
| `valorAnterior` | Object (JSON) | opcional | | Datos anteriores |
| `registroId` | String | max 100, opcional | | ID del registro afectado |
| `direccionIp` | String | max 100 | | Direccion IP del cliente |
| `createdAt` | Date | auto | Index | Fecha de creacion |
| `updatedAt` | Date | auto | | Fecha de modificacion |

**Enum HISTORY_ACTION:**
- `Adicionar` — Se creo un nuevo registro
- `Modificar` — Se modifico un registro existente
- `Eliminar` — Se elimino logicamente (activo = false)
- `Eliminar_completamente` — Se elimino fisicamente

**Documento de ejemplo:**
```json
{
  "_id": ObjectId("65e4a1b2c3d4e5f6a7b8c9d8"),
  "activo": true,
  "userId": "65e4a1b2c3d4e5f6a7b8c9d0",
  "date": ISODate("2025-03-04T08:30:15Z"),
  "tabla": "movimiento",
  "action": "Adicionar",
  "valorNuevo": {
    "tipoMedioId": "...",
    "fechaEntrada": "2025-03-04T08:30:00Z",
    "parqueoId": "...",
    "qrEscaneado": "QR-000101"
  },
  "valorAnterior": null,
  "registroId": "65e4a1b2c3d4e5f6a7b8c9d6",
  "direccionIp": "192.168.1.100",
  "createdAt": ISODate("2025-03-04T08:30:15Z")
}
```

---

### 4.10 `nom_tipo_medio` — Tipos de Medio (Nomenclador)

Hereda de GenericNomencladorEntity:

| Campo | Tipo | Restricciones | Descripcion |
|---|---|---|---|
| `_id` | ObjectId | PK | ID unico |
| `activo` | Boolean | default: true | Registro activo |
| `nombre` | String | unique, min 2, max 100 | Nombre (ej: "Bicicleta") |
| `descripcion` | String | max 500 | Descripcion |
| `movimientoIds` | String[] | opcional | IDs de movimientos asociados |
| `precioIds` | String[] | opcional | IDs de precios asociados |
| `createdAt` | Date | auto | Fecha de creacion |
| `updatedAt` | Date | auto | Fecha de modificacion |

---

### 4.11 `nom_parqueo` — Parqueos (Nomenclador)

Hereda de GenericNomencladorEntity:

| Campo | Tipo | Restricciones | Descripcion |
|---|---|---|---|
| `_id` | ObjectId | PK | ID unico |
| `activo` | Boolean | default: true | Registro activo |
| `nombre` | String | unique | Nombre (ej: "Parqueo # 1") |
| `descripcion` | String | | Descripcion |
| `registroDiarioIds` | String[] | opcional | IDs de registros diarios |
| `movimientoIds` | String[] | opcional | IDs de movimientos |
| `createdAt` | Date | auto | Fecha de creacion |
| `updatedAt` | Date | auto | Fecha de modificacion |

---

### 4.12 `registro_diario` — Registro Diario

| Campo | Tipo | Restricciones | Indice | Descripcion |
|---|---|---|---|---|
| `_id` | ObjectId | PK | PK | ID unico |
| `activo` | Boolean | default: true | Index | Registro activo |
| `fecha` | Date | | Index | Fecha del registro |
| `parqueoId` | String | | Index | ID del parqueo |
| `estado` | String | max 50 | | Estado (ej: "cerrado") |
| `totalMedios` | Integer | default: 0, max 999999 | | Total de medios del dia |
| `totalIngresos` | Float | max 999999999.99 | | Total de ingresos del dia |
| `detalleTipos` | Object[] (JSON) | | | Detalle por tipo de medio |
| `createdAt` | Date | auto | Index | Fecha de creacion |
| `updatedAt` | Date | auto | | Fecha de modificacion |

---

## 5. Relaciones entre Entidades

### 5.1 Mapa de Referencias

| Origen | Campo | Destino | Tipo |
|---|---|---|---|
| `user` | `roleIds[]` | `rol._id` | Muchos a Muchos |
| `user` | `funcionIds[]` | `funcion._id` | Muchos a Muchos |
| `user` | `parqueoIds[]` | `nom_parqueo._id` | Muchos a Muchos |
| `rol` | `userIds[]` | `user._id` | Muchos a Muchos (inversa) |
| `rol` | `funcionIds[]` | `funcion._id` | Muchos a Muchos |
| `funcion` | `endPointIds[]` | `end_point._id` | Muchos a Muchos |
| `funcion` | `menuId` | `menu._id` | Muchos a Uno |
| `menu` | `menuId` | `menu._id` | Auto-referencia (padre) |
| `movimiento` | `tipoMedioId` | `nom_tipo_medio._id` | Muchos a Uno |
| `movimiento` | `parqueoId` | `nom_parqueo._id` | Muchos a Uno |
| `movimiento` | `precioId` | `precio._id` | Muchos a Uno |
| `precio` | `tipoMedioId` | `nom_tipo_medio._id` | Muchos a Uno |
| `precio` | `parqueoId` | `nom_parqueo._id` | Muchos a Uno |
| `qr` | `tipoMedioId` | `nom_tipo_medio._id` | Muchos a Uno |
| `qr` | `parqueoId` | `nom_parqueo._id` | Muchos a Uno |
| `log_history` | `userId` | `user._id` | Muchos a Uno |
| `registro_diario` | `parqueoId` | `nom_parqueo._id` | Muchos a Uno |

### 5.2 Denormalizacion Intencional

Para optimizar la lectura en MongoDB, algunas entidades incluyen campos denormalizados:

| Entidad | Campo denormalizado | Fuente | Rationale |
|---|---|---|---|
| `qr` | `tipoMedioNombre` | `nom_tipo_medio.nombre` | Evitar lookup al mostrar QR |
| `qr` | `tipoMedioCodigo` | `nom_tipo_medio.codigo` | Escaneo rapido |
| `qr` | `parqueoNombre` | `nom_parqueo.nombre` | Evitar lookup al mostrar QR |

---

## 6. Indices Recomendados

### 6.1 Indices existentes (definidos en Entity)

| Coleccion | Campo(s) | Tipo | Uso |
|---|---|---|---|
| `user` | `userName` | Unique | Login rapido |
| `user` | `email` | Index | Busqueda por email |
| `user` | `roleIds` | Index | Filtrar usuarios por rol |
| `user` | `parqueoIds` | Index | Filtrar usuarios por parqueo |
| `rol` | `nombre` | Unique | Busqueda por nombre de rol |
| `funcion` | `nombre` | Index | Busqueda por nombre |
| `end_point` | `metodo` | Index | Filtrar por metodo HTTP |
| `qr` | `codigo` | Unique | Busqueda rapida por codigo |
| `qr` | `loteId` | Index | Busqueda por lote |
| `qr` | `estado` | Index | Filtrar por estado |
| `qr` | `tipoMedioId` | Index | Filtrar por tipo de medio |
| `qr` | `parqueoId` | Index | Filtrar por parqueo |
| `movimiento` | `fechaEntrada` | Index | Busqueda por fecha |
| `movimiento` | `parqueoId` | Index | Filtrar por parqueo |
| `movimiento` | `tipoMedioId` | Index | Filtrar por tipo de medio |
| `precio` | `tipoMedioId` | Index | Busqueda de precio por tipo |
| `precio` | `parqueoId` | Index | Busqueda de precio por parqueo |
| `log_history` | `action` | Index | Filtrar por tipo de accion |
| `log_history` | `userId` | Index | Filtrar por usuario |
| `registro_diario` | `fecha` | Index | Busqueda por fecha |
| `registro_diario` | `parqueoId` | Index | Filtrar por parqueo |

### 6.2 Indices Compuestos Sugeridos

```javascript
// Movimientos por parqueo y fecha (para BI)
db.movimiento.createIndex({ parqueoId: 1, fechaEntrada: -1 });

// QRs por lote y estado
db.qr.createIndex({ loteId: 1, estado: 1 });

// Log history por usuario y fecha
db.log_history.createIndex({ userId: 1, date: -1 });

// Precio vigente por tipo medio y parqueo
db.precio.createIndex({ tipoMedioId: 1, parqueoId: 1, fechaInicioVigencia: -1 });
```

---

*Documento generado como parte de la metodologia SXP (Scrum + XP Fusion)*  
*Ultima actualizacion: Marzo 2025*
