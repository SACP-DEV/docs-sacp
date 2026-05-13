# 02 - Arquitectura del Sistema

## SACP: Arquitectura de 3 Capas

---

## 1. Arquitectura General

### 1.1 Vision General

El sistema SACP sigue una arquitectura de **3 capas** con un componente movil adicional que opera de forma offline-first:

```
┌──────────────────────────────────────────────────────────────┐
│                     CAPA DE PRESENTACION                      │
│  ┌─────────────────────┐    ┌──────────────────────────────┐ │
│  │    web-sacp          │    │       apk-sacp               │ │
│  │  (Next.js 16 +      │    │   (Expo/React Native +       │ │
│  │   shadcn/ui)         │    │    SQLite offline)            │ │
│  │  Dashboard Admin     │    │   App Operador Parqueo       │ │
│  └──────────┬───────────┘    └──────────────┬───────────────┘ │
└─────────────┼───────────────────────────────┼─────────────────┘
              │ HTTP/REST (JSON)              │ HTTP/REST (JSON)
              │ JWT Bearer Token              │ JWT Bearer Token
┌─────────────┼───────────────────────────────┼─────────────────┐
│             ▼         CAPA DE API            ▼                │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │                    api-sacp                              │  │
│  │              (NestJS + TypeORM)                          │  │
│  │                                                          │  │
│  │  ┌─────────┐ ┌──────────┐ ┌────────┐ ┌───────────────┐ │  │
│  │  │AuthModule│ │QRModule  │ │Movimien│ │PrecioModule   │ │  │
│  │  └─────────┘ └──────────┘ │toModule│ └───────────────┘ │  │
│  │  ┌─────────┐ ┌──────────┐ └────────┘ ┌───────────────┐ │  │
│  │  │UserModule│ │RolModule │ │LogModule│ │BIModule       │ │  │
│  │  └─────────┘ └──────────┘ └────────┘ └───────────────┘ │  │
│  │  ┌─────────┐ ┌──────────┐ ┌────────────────────────┐   │  │
│  │  │MenuModule│ │FuncModule│ │SyncModule + SocketIO   │   │  │
│  │  └─────────┘ └──────────┘ └────────────────────────┘   │  │
│  └────────────────────────┬────────────────────────────────┘  │
└───────────────────────────┼───────────────────────────────────┘
                            │ MongoDB Driver
┌───────────────────────────┼───────────────────────────────────┐
│                           ▼   CAPA DE DATOS                   │
│  ┌──────────────────────────────────────────────────────────┐ │
│  │                     MongoDB                               │ │
│  │  Colecciones: user, rol, funcion, menu, qr, movimiento,  │ │
│  │  precio, log_history, end_point, nom_parqueo,             │ │
│  │  nom_tipo_medio, registro_diario                          │ │
│  └──────────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────────┘
```

📸 **Captura pendiente**: Diagrama de arquitectura general del sistema

---

## 2. Arquitectura por Componente

### 2.1 api-sacp (Backend REST API)

| Aspecto | Detalle |
|---|---|
| **Framework** | NestJS (TypeScript) |
| **ORM** | TypeORM con driver MongoDB |
| **Autenticacion** | JWT (access token + refresh token) con Passport |
| **Validacion** | class-validator + ValidationPipe global |
| **Documentacion** | Swagger/OpenAPI en `/api/docs` |
| **Puerto** | 3000 (configurable via .env) |
| **Prefijo** | `/api` en todos los endpoints |

**Estructura interna:**

```
api-sacp/
├── src/
│   ├── api/                    # Capa de presentacion (Controllers, Guards, Decorators)
│   │   ├── controller/         # Controladores REST
│   │   ├── guard/              # Guards de autenticacion y autorizacion
│   │   └── decorator/          # Decoradores personalizados
│   ├── core/                   # Capa de logica de negocio (Services, Mappers, Strategies)
│   │   ├── service/            # Servicios de negocio
│   │   ├── mapper/             # Mappers Entity ↔ DTO
│   │   ├── strategy/           # Estrategias JWT (jwt, refresh)
│   │   └── logger/             # Provider de logging
│   ├── persistence/            # Capa de acceso a datos (Entities, Repositories)
│   │   ├── entity/             # Entidades TypeORM/MongoDB
│   │   └── repository/         # Repositorios personalizados
│   ├── shared/                 # Compartido (DTOs, Enums, Interfaces, Pagination)
│   │   ├── dto/                # Data Transfer Objects
│   │   ├── enum/               # Enumeraciones
│   │   ├── interface/          # Interfaces compartidas
│   │   └── pagination/         # Helpers de paginacion
│   ├── mail/                   # Servicio de envio de correos
│   ├── database/               # Configuracion de base de datos
│   └── config/                 # Configuracion de la aplicacion
```

**Patron arquitectonico:** Controlador → Servicio → Repositorio, con Mappers para conversion Entity ↔ DTO.

### 2.2 web-sacp (Dashboard Web)

| Aspecto | Detalle |
|---|---|
| **Framework** | Next.js 16 (App Router) |
| **UI** | shadcn/ui + Tailwind CSS |
| **State** | React Context (StoreContext) |
| **Autenticacion** | JWT con cookies + refresh token |
| **Tablas** | DataTable component con paginacion |
| **Despliegue** | Docker + Vercel |

**Estructura interna:**

```
web-sacp/
├── app/
│   ├── auth/                   # Paginas de autenticacion (login)
│   │   ├── components/         # Formulario de login
│   │   ├── adapters/           # Adaptadores de datos auth
│   │   ├── endpoints/          # Llamadas a API auth
│   │   └── services/           # Servicios de auth
│   ├── admin/                  # Paginas de administracion (protegidas)
│   │   ├── qr/                 # Gestion de QRs
│   │   ├── users/              # Gestion de usuarios
│   │   ├── roles/              # Gestion de roles
│   │   ├── functions/          # Gestion de funciones
│   │   ├── menus/              # Gestion de menus
│   │   ├── nomenclators/       # Gestion de nomencladores
│   │   ├── logs-history/       # Trazas de auditoria
│   │   └── bi/                 # Business Intelligence
│   └── layout.tsx              # Layout principal
├── components/                 # Componentes compartidos
│   ├── ui/                     # Componentes shadcn/ui
│   ├── Menu/                   # Sistema de menus
│   └── DataTable/              # Tabla de datos generica
├── contexts/                   # React Contexts
├── utilities/                  # Funciones utilitarias
├── services/                   # Servicios globales
├── models/                     # Modelos de datos
└── localdb/                    # Base de datos local (IndexedDB/Dexie)
```

**Patron por modulo:** Cada modulo de admin sigue la estructura: `page.tsx → adapters → endpoints → services → models`.

### 2.3 apk-sacp (Aplicacion Movil)

| Aspecto | Detalle |
|---|---|
| **Framework** | Expo SDK + React Native |
| **Arquitectura** | Clean Architecture (4 capas) |
| **Base de datos local** | SQLite (expo-sqlite) |
| **Offline** | Offline-first con cola de sincronizacion |
| **QR Scanner** | Componente nativo de camara |

**Estructura interna:**

```
apk-sacp/
├── app/                        # Pantallas (Expo Router)
│   ├── login.tsx               # Pantalla de login
│   ├── (main)/                 # Pantallas principales
│   │   ├── index.tsx           # Dashboard principal
│   │   └── scanner.tsx         # Escaner QR
│   ├── settings.tsx            # Configuracion
│   ├── select-parqueo.tsx      # Seleccion de parqueo
│   └── _layout.tsx             # Layout principal
├── src/
│   ├── domain/                 # CAPA DE DOMINIO
│   │   ├── entities/           # Entidades del negocio
│   │   ├── usecases/           # Casos de uso
│   │   └── repositories/       # Interfaces de repositorios
│   ├── data/                   # CAPA DE DATOS
│   │   ├── datasources/        # Fuentes de datos (remote + local)
│   │   ├── repositories/       # Implementacion de repositorios
│   │   ├── dtos/               # Data Transfer Objects
│   │   └── mappers/            # Mapeo DTO ↔ Entity
│   ├── infrastructure/         # CAPA DE INFRAESTRUCTURA
│   │   ├── database/           # SQLite schema y acceso
│   │   ├── network/            # Servicio de conectividad
│   │   └── di/                 # Inyeccion de dependencias
│   └── presentation/           # CAPA DE PRESENTACION
│       ├── contexts/           # React Context (Auth)
│       └── hooks/              # Custom hooks
└── components/                 # Componentes UI compartidos
    ├── QRScanner.tsx
    ├── OperationButton.tsx
    └── ResultModal.tsx
```

📸 **Captura pendiente**: Diagrama de arquitectura Clean Architecture de la app movil

---

## 3. Stack Tecnologico

### 3.1 Backend (api-sacp)

| Tecnologia | Version | Uso |
|---|---|---|
| Node.js | 18+ | Runtime |
| NestJS | 10+ | Framework web |
| TypeORM | 0.3.x | ORM para MongoDB |
| MongoDB | 6+ | Base de datos principal |
| Passport | 0.6+ | Estrategias de autenticacion |
| bcryptjs | 2.4+ | Hash de contrasenas |
| class-validator | 0.14+ | Validacion de DTOs |
| Swagger | 7+ | Documentacion API |
| Nodemailer | 6+ | Envio de correos |
| Handlebars | 4+ | Templates de correo |

### 3.2 Web (web-sacp)

| Tecnologia | Version | Uso |
|---|---|---|
| Next.js | 16 | Framework React SSR/SSG |
| React | 19 | Libreria UI |
| TypeScript | 5+ | Lenguaje |
| Tailwind CSS | 4 | Framework CSS |
| shadcn/ui | latest | Componentes UI |
| React Hook Form | 7+ | Manejo de formularios |
| Zod | 3+ | Validacion de esquemas |

### 3.3 Movil (apk-sacp)

| Tecnologia | Version | Uso |
|---|---|---|
| Expo SDK | 52+ | Framework React Native |
| React Native | 0.76+ | Framework movil |
| expo-sqlite | 14+ | Base de datos local |
| expo-camera | 15+ | Escaneo QR |
| expo-netinfo | 11+ | Deteccion de conectividad |

---

## 4. Flujos de Datos

### 4.1 Flujo de Registro de Entrada

```
Operador            APK (offline)         API (online)          MongoDB
   │                     │                      │                    │
   │  Escanea QR        │                      │                    │
   │──────────────────>│                      │                    │
   │                     │  Busca QR en cache   │                    │
   │                     │  (SQLite local)      │                    │
   │                     │                      │                    │
   │                     │  [Sin conexion]       │                    │
   │                     │  Guarda movimiento   │                    │
   │                     │  en movimientos_     │                    │
   │                     │  pendientes (SQLite)  │                    │
   │                     │                      │                    │
   │                     │  [Con conexion]       │                    │
   │                     │  POST /movimiento    │                    │
   │                     │─────────────────────>│                    │
   │                     │                      │  Insert movimiento │
   │                     │                      │──────────────────>│
   │                     │                      │  Update QR estado │
   │                     │                      │──────────────────>│
   │                     │                      │  Insert log_history│
   │                     │                      │──────────────────>│
   │                     │  Response 201        │                    │
   │                     │<─────────────────────│                    │
   │  Resultado OK      │                      │                    │
   │<──────────────────│                      │                    │
```

📸 **Captura pendiente**: Diagrama de flujo de registro de entrada con secuencia

### 4.2 Flujo de Sincronizacion Offline

```
APK (recupera conexion)              API                      MongoDB
        │                              │                         │
        │  GET /sync/status           │                         │
        │────────────────────────────>│                         │
        │                              │                         │
        │  POST /sync                  │                         │
        │  { movimientos: [...] }      │                         │
        │────────────────────────────>│                         │
        │                              │  Procesa cada movimiento│
        │                              │  Verifica duplicados    │
        │                              │  Insert/Update en MongoDB│
        │                              │──────────────────────>│
        │                              │                         │
        │  Response:                   │                         │
        │  { exitosos: 5,              │                         │
        │    errores: 0,               │                         │
        │    datosActualizados: {...} } │                         │
        │<────────────────────────────│                         │
        │                              │                         │
        │  Actualiza cache local       │                         │
        │  (SQLite) con datos nuevos   │                         │
        │                              │                         │
```

📸 **Captura pendiente**: Diagrama de flujo de sincronizacion offline

---

## 5. Arquitectura de Despliegue

### 5.1 Ambiente de Produccion

```
┌─────────────────────────────────────────────────────────────┐
│                       Internet / Intranet                     │
└──────────┬──────────────────────────────────┬───────────────┘
           │                                  │
           ▼                                  ▼
┌─────────────────────┐              ┌────────────────────────┐
│   Vercel / CDN       │              │   Servidor On-Premise  │
│   (web-sacp)         │              │                        │
│   Next.js SSR        │              │   ┌──────────────────┐ │
│   Static Assets      │              │   │  Docker           │ │
└──────────┬───────────┘              │   │  ┌──────────────┐ │ │
           │                          │   │  │ api-sacp     │ │ │
           │ HTTPS                    │   │  │ NestJS:3000  │ │ │
           │                          │   │  └──────────────┘ │ │
           └──────────────────────────>│   │  ┌──────────────┐ │ │
                                      │   │  │ MongoDB      │ │ │
                                      │   │  │ :27017       │ │ │
                                      │   │  └──────────────┘ │ │
                                      │   └──────────────────┘ │
                                      └────────────────────────┘
           ┌──────────────────────────────────────────┐
           │        Dispositivos Moviles              │
           │   apk-sacp (Expo)                        │
           │   Con SQLite local para offline          │
           └──────────────────────────────────────────┘
```

📸 **Captura pendiente**: Diagrama de arquitectura de despliegue

### 5.2 Ambiente de Desarrollo

```
Developer Machine
├── api-sacp    → localhost:3000 (NestJS con hot-reload)
├── web-sacp    → localhost:3001 (Next.js dev server)
├── apk-sacp    → Expo Go en emulador o dispositivo fisico
└── MongoDB     → localhost:27017 (Docker o local)
```

---

## 6. Arquitectura de Seguridad

### 6.1 Flujo de Autenticacion JWT

```
Cliente                          API
  │                               │
  │  POST /api/auth/signin        │
  │  { userName, password }       │
  │──────────────────────────────>│
  │                               │  Valida credenciales
  │                               │  Genera accessToken (15min)
  │                               │  Genera refreshToken (7d)
  │                               │  Almacena refreshToken en user
  │  { accessToken, refreshToken, │
  │    expiresIn, user }          │
  │<──────────────────────────────│
  │                               │
  │  GET /api/qr                  │
  │  Authorization: Bearer <JWT>  │
  │──────────────────────────────>│
  │                               │  JwtStrategy valida token
  │                               │  RolGuard verifica rol
  │                               │  PermissionGuard verifica funcion
  │  Response 200                 │
  │<──────────────────────────────│
  │                               │
  │  [Token expirado]             │
  │                               │
  │  POST /api/auth/refresh-tokens│
  │  { refreshToken }             │
  │──────────────────────────────>│
  │                               │  RefreshStrategy valida
  │                               │  Genera nuevo accessToken
  │  { accessToken, refreshToken, │
  │    expiresIn }                │
  │<──────────────────────────────│
```

📸 **Captura pendiente**: Diagrama de flujo de autenticacion JWT

### 6.2 Modelo de Autorizacion

```
Usuario ──m:n──> Rol ──m:n──> Funcion ──m:n──> EndPoint
  │                                         │
  │                                         ├── controller
  │                                         ├── servicio
  │                                         ├── ruta
  │                                         ├── nombre
  │                                         └── metodo (GET/POST/PATCH/DELETE)
  │
  └──m:n──> Parqueo (asignacion de parqueos al operador)
```

**Guards en orden de ejecucion:**

1. **AuthGuard('jwt')**: Verifica que el token JWT sea valido
2. **RolGuard**: Verifica que el usuario tenga el rol requerido (@Roles)
3. **PermissionGuard**: Verifica que el usuario tenga la funcion que permite acceder al endpoint

### 6.3 Registro de Auditoria

Toda operacion CRUD genera automaticamente un registro en `log_history`:

| Campo | Contenido |
|---|---|
| `userId` | ID del usuario que realizo la operacion |
| `date` | Fecha y hora de la operacion |
| `tabla` | Nombre de la coleccion afectada |
| `action` | Tipo: Adicionar, Modificar, Eliminar, Eliminar_completamente |
| `valorAnterior` | JSON con los datos anteriores (en modificaciones/eliminaciones) |
| `valorNuevo` | JSON con los datos nuevos (en adiciones/modificaciones) |
| `registroId` | ID del registro afectado |
| `direccionIp` | IP del cliente que realizo la operacion |

---

## 7. Arquitectura Offline-First (App Movil)

### 7.1 Principios de Diseno

1. **Local-first**: Toda operacion se realiza primero en SQLite local
2. **Cola de sincronizacion**: Operaciones pendientes se almacenan en `movimientos_pendientes`
3. **Cache de datos maestros**: QRs, precios, tipos de medio y parqueos se cachean localmente
4. **Deteccion de red**: `useNetwork` hook detecta cambios de conectividad
5. **Sincronizacion automatica**: Cuando se recupera la conexion, se envian pendientes al API

### 7.2 Capas de Datos en el Movil

```
┌──────────────────────────────────┐
│     Presentacion (UI)            │
│  Hooks: useParkingOperation,     │
│  useSync, usePrecios, etc.       │
└──────────────┬───────────────────┘
               │
┌──────────────▼───────────────────┐
│     Dominio (Use Cases)          │
│  RegistrarEntradaUseCase         │
│  RegistrarSalidaUseCase          │
│  SincronizarUseCase              │
│  LoginUseCase, etc.              │
└──────────────┬───────────────────┘
               │
┌──────────────▼───────────────────┐
│     Datos (Repositories)         │
│  ┌────────────┐ ┌─────────────┐  │
│  │ Local DS   │ │ Remote DS   │  │
│  │ (SQLite)   │ │ (API REST)  │  │
│  └────────────┘ └─────────────┘  │
└──────────────────────────────────┘
```

📸 **Captura pendiente**: Diagrama de arquitectura offline-first del movil

---

## 8. Decisiones Arquitectonicas

| Decision | Rationale | Alternativa descartada |
|---|---|---|
| MongoDB como BD | Esquema flexible para nomencladores, buen rendimiento con TypeORM | PostgreSQL (requeria migraciones rigidas) |
| TypeORM con MongoDB | ORM conocido por el equipo, soporta MongoDB | Mongoose (menos integracion con NestJS) |
| JWT con refresh token | Seguridad + experiencia de usuario (sin re-login frecuente) | Solo access token (UX mala por expiracion) |
| Offline-first con SQLite | Operatividad sin conexion, critical para parqueos | Solo online (inaceptable para operadores) |
| Clean Architecture en APK | Separacion de concerns, testeabilidad | Arquitectura monolitica (dificil de mantener) |
| Borrado virtual (soft delete) | Trazabilidad, posibilidad de recuperar datos | Borrado fisico (perdida de informacion para auditoria) |
| Referencias por ID (no embebido) | Escalabilidad, consistencia en MongoDB | Documentos embebidos (duplicacion, inconsistencia) |
| Prefijo `/api` global | Separacion clara de versiones, compatibilidad con proxies | Sin prefijo (riesgo de colision de rutas) |

---

*Documento generado como parte de la metodologia SXP (Scrum + XP Fusion)*  
*Ultima actualizacion: Marzo 2025*
