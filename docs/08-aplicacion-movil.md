# 08 - Aplicacion Movil

## SACP: Documentacion del APK (Expo/React Native)

---

## 1. Arquitectura General

### 1.1 Clean Architecture (4 Capas)

La aplicacion movil sigue Clean Architecture con 4 capas separadas:

```
┌─────────────────────────────────────────────────┐
│                 PRESENTACION                     │
│  Pantallas (Expo Router) + Hooks + Contexts     │
│  app/*.tsx, src/presentation/*                   │
├─────────────────────────────────────────────────┤
│                    DOMINIO                       │
│  Entidades + Casos de Uso + Interfaces Repo     │
│  src/domain/*                                    │
├─────────────────────────────────────────────────┤
│                     DATOS                        │
│  DTOs + Mappers + DataSources + Repo Impl        │
│  src/data/*                                      │
├─────────────────────────────────────────────────┤
│               INFRAESTRUCTURA                    │
│  SQLite + Network + DI Container                 │
│  src/infrastructure/*                            │
└─────────────────────────────────────────────────┘
```

📸 **Captura pendiente**: Diagrama de capas Clean Architecture de la app movil

### 1.2 Principios

1. **Regla de dependencia**: Las dependencias apuntan hacia adentro (Presentacion → Dominio ← Datos ← Infraestructura)
2. **Dominio independiente**: Las entidades y casos de uso no dependen de ningun framework
3. **Datos como implementacion**: Los repositorios concretos implementan interfaces del dominio
4. **Offline-first**: Toda operacion se realiza primero en la base de datos local

---

## 2. Capa de Presentacion

### 2.1 Pantallas (Expo Router)

| Pantalla | Archivo | Descripcion |
|---|---|---|
| Login | `app/login.tsx` | Autenticacion de usuario |
| Dashboard | `app/(main)/index.tsx` | Pantalla principal con resumen del parqueo |
| Scanner | `app/(main)/scanner.tsx` | Escaneo de codigo QR |
| Seleccion Parqueo | `app/select-parqueo.tsx` | Seleccion de parqueo de trabajo |
| Configuracion | `app/settings.tsx` | Configuracion de la app |
| Layout | `app/_layout.tsx` | Layout raiz con providers |
| Main Layout | `app/(main)/_layout.tsx` | Layout de pantallas autenticadas |

### 2.2 Flujo de Pantallas

```
┌─────────┐    Login OK    ┌────────────────┐
│  Login   │──────────────>│ Select Parqueo │
└─────────┘               └───────┬────────┘
                                  │ Selecciona parqueo
                                  ▼
                           ┌────────────────┐
                  ┌───────│   Dashboard    │───────┐
                  │       └────────────────┘       │
                  │ Scanner QR       │ Configuracion│
                  ▼                  ▼
           ┌────────────┐    ┌──────────────┐
           │  Scanner   │    │  Settings    │
           └────────────┘    └──────────────┘
```

📸 **Captura pendiente**: Diagrama de flujo de pantallas de la app movil

### 2.3 Hooks de Presentacion

| Hook | Archivo | Descripcion |
|---|---|---|
| `useNetwork` | `src/presentation/hooks/useNetwork.ts` | Detecta estado de conexion a internet |
| `useParkingOperation` | `src/presentation/hooks/useParkingOperation.ts` | Orquesta operaciones de entrada/salida |
| `usePrecios` | `src/presentation/hooks/usePrecios.ts` | Obtiene precios del parqueo seleccionado |
| `useResumenParqueo` | `src/presentation/hooks/useResumenParqueo.ts` | Obtiene resumen del parqueo |
| `useSync` | `src/presentation/hooks/useSync.ts` | Gestiona sincronizacion de datos pendientes |

### 2.4 Contextos

| Context | Archivo | Descripcion |
|---|---|---|
| `AuthContext` | `src/presentation/contexts/AuthContext.tsx` | Estado de autenticacion, usuario actual, tokens |

---

## 3. Capa de Dominio

### 3.1 Entidades

| Entidad | Archivo | Descripcion |
|---|---|---|
| `Movimiento` | `src/domain/entities/Movimiento.ts` | Registro de entrada/salida de vehiculo |
| `QR` | `src/domain/entities/QR.ts` | Codigo QR del sistema |
| `Usuario` | `src/domain/entities/Usuario.ts` | Usuario del sistema |
| `Parqueo` | `src/domain/entities/Parqueo.ts` | Parqueo vehicular |
| `Precio` | `src/domain/entities/Precio.ts` | Tarifa de parqueo |

**Entidad Movimiento:**

```typescript
interface Movimiento {
  id: string;
  qrId: string;
  qrCodigo: string;
  parqueoId: string;
  parqueoNombre: string;
  tipoMedioId: string;
  tipoMedioNombre: string;
  usuarioEntradaId: string;
  usuarioEntradaNombre: string;
  usuarioSalidaId: string | null;
  usuarioSalidaNombre: string | null;
  precioId: string;
  precioMonto: number;
  fechaEntrada: Date;
  fechaSalida: Date | null;
  tiempoEstadia: number | null;
  montoCobrado: number | null;
  observaciones: string | null;
  sincronizado: boolean;
  createdAt: Date;
  updatedAt: Date;
}
```

**Entidad QR y ResultadoEscaneoQR:**

```typescript
interface QR {
  id: string;
  codigo: string;
  loteId: string;
  loteNombre: string;
  activo: boolean;
  tipoMedioId: string | null;
  tipoMedioNombre: string | null;
  createdAt: Date;
}

interface ResultadoEscaneoQR {
  valido: boolean;
  qr: QR | null;
  mensaje: string;
  puedeEntrar: boolean;
  puedeSalir: boolean;
  movimientoActivo: MovimientoActivo | null;
}
```

### 3.2 Casos de Uso

| Caso de Uso | Archivo | Descripcion |
|---|---|---|
| `LoginUseCase` | `src/domain/usecases/LoginUseCase.ts` | Autenticar usuario y almacenar credenciales |
| `LogoutUseCase` | `src/domain/usecases/LogoutUseCase.ts` | Cerrar sesion y limpiar datos |
| `RegistrarEntradaUseCase` | `src/domain/usecases/RegistrarEntradaUseCase.ts` | Registrar entrada de vehiculo con QR |
| `RegistrarSalidaUseCase` | `src/domain/usecases/RegistrarSalidaUseCase.ts` | Registrar salida y calcular precio |
| `ObtenerPreciosUseCase` | `src/domain/usecases/ObtenerPreciosUseCase.ts` | Obtener precios vigentes para un parqueo |
| `ObtenerResumenParqueoUseCase` | `src/domain/usecases/ObtenerResumenParqueoUseCase.ts` | Obtener resumen del parqueo |
| `SincronizarUseCase` | `src/domain/usecases/SincronizarUseCase.ts` | Sincronizar movimientos pendientes |

### 3.3 Interfaces de Repositorios

| Interface | Archivo | Descripcion |
|---|---|---|
| `AuthRepository` | `src/domain/repositories/AuthRepository.ts` | Operaciones de autenticacion |
| `MovimientoRepository` | `src/domain/repositories/MovimientoRepository.ts` | CRUD de movimientos |
| `QRRepository` | `src/domain/repositories/QRRepository.ts` | Busqueda de QRs |
| `PrecioRepository` | `src/domain/repositories/PrecioRepository.ts` | Consulta de precios |
| `ParqueoRepository` | `src/domain/repositories/ParqueoRepository.ts` | Consulta de parqueos |
| `TipoMedioRepository` | `src/domain/repositories/TipoMedioRepository.ts` | Consulta de tipos de medio |
| `SyncRepository` | `src/domain/repositories/SyncRepository.ts` | Sincronizacion con servidor |

---

## 4. Capa de Datos

### 4.1 DataSources Remotos

| DataSource | Archivo | Descripcion |
|---|---|---|
| `AuthRemoteDataSource` | `src/data/datasources/remote/AuthRemoteDataSource.ts` | Llamadas a /api/auth |
| `MovimientoRemoteDataSource` | `src/data/datasources/remote/MovimientoRemoteDataSource.ts` | Llamadas a /api/movimiento |
| `PrecioRemoteDataSource` | `src/data/datasources/remote/PrecioRemoteDataSource.ts` | Llamadas a /api/precio |
| `QRRemoteDataSource` | `src/data/datasources/remote/QRRemoteDataSource.ts` | Llamadas a /api/qr |
| `SyncRemoteDataSource` | `src/data/datasources/remote/SyncRemoteDataSource.ts` | Llamadas a /api/sync |

### 4.2 DataSources Locales

| DataSource | Archivo | Descripcion |
|---|---|---|
| `AuthLocalDataSource` | `src/data/datasources/local/AuthLocalDataSource.ts` | Sesiones y credenciales en SQLite |
| `MovimientoLocalDataSource` | `src/data/datasources/local/MovimientoLocalDataSource.ts` | Movimientos cache en SQLite |
| `QRLocalDataSource` | `src/data/datasources/local/QRLocalDataSource.ts` | QRs cache en SQLite |
| `PrecioLocalDataSource` | `src/data/datasources/local/PrecioLocalDataSource.ts` | Precios cache en SQLite |
| `ConfigLocalDataSource` | `src/data/datasources/local/ConfigLocalDataSource.ts` | Configuracion en SQLite |

### 4.3 Implementaciones de Repositorios

| Implementacion | Interface | Descripcion |
|---|---|---|
| `AuthRepositoryImpl` | `AuthRepository` | Orquesta local + remote auth |
| `MovimientoRepositoryImpl` | `MovimientoRepository` | Local-first con cola de sync |
| `QRRepositoryImpl` | `QRRepository` | Cache local + remote fallback |
| `PrecioRepositoryImpl` | `PrecioRepository` | Cache local + remote fallback |
| `SyncRepositoryImpl` | `SyncRepository` | Envio de pendientes al servidor |

---

## 5. Capa de Infraestructura

### 5.1 Base de Datos Local (SQLite)

**Esquema completo:**

| Tabla | Proposito |
|---|---|
| `usuarios_offline` | Credenciales para login offline |
| `sesion` | Sesion actual del usuario |
| `movimientos_pendientes` | Cola de operaciones pendientes de sync |
| `movimientos_cache` | Cache de movimientos sincronizados |
| `qrs_cache` | Cache de codigos QR |
| `precios_cache` | Cache de tarifas vigentes |
| `tipos_medio_cache` | Cache de tipos de vehiculo |
| `parqueos_cache` | Cache de parqueos |
| `configuracion` | Configuracion general de la app |

### 5.2 Tabla: movimientos_pendientes

```sql
CREATE TABLE IF NOT EXISTS movimientos_pendientes (
    id_local TEXT PRIMARY KEY,
    operacion TEXT NOT NULL CHECK(operacion IN ('entrada', 'salida')),
    data_json TEXT NOT NULL,
    created_at TEXT NOT NULL,
    reintentos INTEGER DEFAULT 0,
    sincronizado INTEGER DEFAULT 0,
    movimiento_id TEXT
);
```

| Campo | Tipo | Descripcion |
|---|---|---|
| `id_local` | TEXT PK | UUID generado localmente |
| `operacion` | TEXT | "entrada" o "salida" |
| `data_json` | TEXT | JSON con los datos de la operacion |
| `created_at` | TEXT | Fecha de creacion local |
| `reintentos` | INTEGER | Contador de reintentos fallidos |
| `sincronizado` | INTEGER | 0 = pendiente, 1 = sincronizado |
| `movimiento_id` | TEXT | ID del movimiento en el servidor (tras sync) |

### 5.3 Tabla: movimientos_cache

```sql
CREATE TABLE IF NOT EXISTS movimientos_cache (
    id TEXT PRIMARY KEY,
    qr_id TEXT,
    qr_codigo TEXT NOT NULL,
    parqueo_id TEXT NOT NULL,
    parqueo_nombre TEXT,
    tipo_medio_id TEXT,
    tipo_medio_nombre TEXT,
    usuario_entrada_id TEXT,
    usuario_entrada_nombre TEXT,
    usuario_salida_id TEXT,
    usuario_salida_nombre TEXT,
    precio_id TEXT,
    precio_monto REAL,
    fecha_entrada TEXT NOT NULL,
    fecha_salida TEXT,
    tiempo_estadia INTEGER,
    monto_cobrado REAL,
    observaciones TEXT,
    sincronizado INTEGER DEFAULT 1,
    created_at TEXT NOT NULL,
    updated_at TEXT NOT NULL
);
```

### 5.4 Tabla: qrs_cache

```sql
CREATE TABLE IF NOT EXISTS qrs_cache (
    id TEXT PRIMARY KEY,
    codigo TEXT UNIQUE NOT NULL,
    lote_id TEXT,
    lote_nombre TEXT,
    activo INTEGER DEFAULT 1,
    tipo_medio_id TEXT,
    tipo_medio_nombre TEXT,
    created_at TEXT NOT NULL
);
```

### 5.5 Tabla: precios_cache

```sql
CREATE TABLE IF NOT EXISTS precios_cache (
    id TEXT PRIMARY KEY,
    monto REAL NOT NULL,
    tipo_medio_id TEXT NOT NULL,
    tipo_medio_nombre TEXT,
    parqueo_id TEXT,
    activo INTEGER DEFAULT 1,
    fecha_vigencia_inicio TEXT NOT NULL,
    fecha_vigencia_fin TEXT,
    created_at TEXT NOT NULL,
    updated_at TEXT NOT NULL
);
```

### 5.6 Indices SQLite

```sql
CREATE INDEX IF NOT EXISTS idx_movimientos_parqueo ON movimientos_cache(parqueo_id);
CREATE INDEX IF NOT EXISTS idx_movimientos_fecha ON movimientos_cache(fecha_entrada);
CREATE INDEX IF NOT EXISTS idx_movimientos_sincronizado ON movimientos_cache(sincronizado);
CREATE INDEX IF NOT EXISTS idx_qrs_codigo ON qrs_cache(codigo);
CREATE INDEX IF NOT EXISTS idx_precios_tipo_medio ON precios_cache(tipo_medio_id);
CREATE INDEX IF NOT EXISTS idx_precios_parqueo ON precios_cache(parqueo_id);
CREATE INDEX IF NOT EXISTS idx_pendientes_sincronizado ON movimientos_pendientes(sincronizado);
```

---

## 6. Diseno Offline-First

### 6.1 Flujo de Operacion Offline

```
Usuario escanea QR
       │
       ▼
┌──────────────┐
│ useNetwork   │──── Esta online?
│ detecta red  │     │
└──────────────┘     │
       │         No  │ Si
       │      ┌──────┴──────┐
       │      ▼             ▼
       │  ┌────────┐  ┌────────────┐
       │  │ Guardar │  │ Enviar al │
       │  │ en cola │  │ API directo│
       │  │ SQLite │  │            │
       │  └────────┘  └────────────┘
       │      │             │
       │      ▼             ▼
       │  Confirmar      Confirmar
       │  localmente     con servidor
       │      │             │
       └──────┴─────────────┘
              │
              ▼
        Mostrar resultado
```

### 6.2 Estrategia de Sincronizacion

**Cuando se recupera la conexion:**

1. `useSync` hook detecta cambio de estado (offline → online)
2. Se ejecuta `SincronizarUseCase`
3. Se leen movimientos pendientes de `movimientos_pendientes` donde `sincronizado = 0`
4. Se envian secuencialmente al API via `POST /api/sync`
5. Por cada operacion exitosa:
   - Se marca `sincronizado = 1` en `movimientos_pendientes`
   - Se actualiza/crea registro en `movimientos_cache`
6. Por cada operacion fallida:
   - Se incrementa `reintentos`
   - Si `reintentos > 3`, se marca como error permanente
7. Se actualiza el cache con `datosActualizados` del servidor

### 6.3 Cache de Datos Maestros

Los siguientes datos se cachean localmente al iniciar sesion o sincronizar:

| Dato | Tabla SQLite | Fuente API | Frecuencia de actualizacion |
|---|---|---|---|
| QRs | `qrs_cache` | `GET /api/qr` | Al sincronizar |
| Precios | `precios_cache` | `GET /api/precio` | Al sincronizar |
| Tipos de medio | `tipos_medio_cache` | `GET /api/nomenclador/tipoMedio` | Al sincronizar |
| Parqueos | `parqueos_cache` | `GET /api/nomenclador/parqueo` | Al sincronizar |

---

## 7. Integracion con la API

### 7.1 Endpoints Utilizados por la App

| Endpoint | Metodo | Uso en la App |
|---|---|---|
| `/api/auth/signin` | POST | Login |
| `/api/auth/refresh-tokens` | POST | Refrescar token |
| `/api/auth/logout` | POST | Cerrar sesion |
| `/api/movimiento` | POST | Registrar entrada |
| `/api/movimiento/:id` | PATCH | Registrar salida |
| `/api/movimiento/activos` | GET | Vehiculos dentro |
| `/api/movimiento/verificar/:qr` | GET | Verificar QR |
| `/api/movimiento/resumen/:parqueoId` | GET | Resumen del parqueo |
| `/api/qr/codigo/:codigo` | GET | Buscar QR |
| `/api/precio` | GET | Obtener precios |
| `/api/sync` | POST | Sincronizar pendientes |
| `/api/sync/status` | GET | Estado de sincronizacion |
| `/api/nomenclador/tipoMedio/listado/elementos` | GET | Tipos de medio |
| `/api/nomenclador/parqueo/listado/elementos` | GET | Parqueos |

### 7.2 Manejo de Tokens

- Access token se almacena en `sesion` (SQLite)
- Refresh token se almacena en `sesion` (SQLite)
- Antes de cada peticion, se verifica si el token esta proximo a expirar
- Si esta por expirar, se ejecuta automaticamente `refresh-tokens`
- Si el refresh falla, se redirige al login

---

## 8. Componentes UI

### 8.1 QRScanner

Componente que utiliza la camara del dispositivo para escanear codigos QR.

```typescript
// Uso:
<QRScanner onScan={(codigo) => handleQrScanned(codigo)} />
```

### 8.2 OperationButton

Boton de operacion que muestra entrada o salida segun el estado del QR.

```typescript
<OperationButton
  tipo="entrada"  // o "salida"
  onPress={handleOperation}
  loading={isLoading}
/>
```

### 8.3 ResultModal

Modal que muestra el resultado de una operacion (exito o error).

```typescript
<ResultModal
  visible={showModal}
  tipo="exito"  // o "error"
  mensaje="Entrada registrada exitosamente"
  onClose={() => setShowModal(false)}
/>
```

---

## 9. Configuracion y Build

### 9.1 Configuracion (app.json)

```json
{
  "expo": {
    "name": "SACP",
    "slug": "apk-sacp",
    "version": "1.0.0",
    "orientation": "portrait",
    "icon": "./assets/icon.svg",
    "splash": { "image": "./assets/splash.png" },
    "ios": { "bundleIdentifier": "com.sacp.apk" },
    "android": {
      "package": "com.sacp.apk",
      "versionCode": 1,
      "adaptiveIcon": { "foregroundImage": "./assets/icon.svg" }
    }
  }
}
```

### 9.2 Build con EAS

```bash
# Build de preview
eas build --profile preview --platform android

# Build de produccion
eas build --profile production --platform android
```

---

*Documento generado como parte de la metodologia SXP (Scrum + XP Fusion)*  
*Ultima actualizacion: Marzo 2025*
