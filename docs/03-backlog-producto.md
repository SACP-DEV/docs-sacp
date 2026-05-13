# 03 - Backlog del Producto

## SACP: Historias de Usuario por Epic

---

## Formato de Historia de Usuario

```
Como [rol], quiero [funcionalidad], para [beneficio]
```

**Atributos:**
- **Prioridad**: Alta (MVP), Media (Post-MVP), Baja (Mejora)
- **Story Points**: Estimacion en puntos Fibonacci (1, 2, 3, 5, 8, 13)
- **Criterios de Aceptacion**: Condiciones verificables

---

## Epic 1: Gestion de QR

### QR-001: Generar lote de codigos QR

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Generar un lote de codigos QR para un tipo de medio y parqueo especifico |
| **Para** | Disponer de credenciales de acceso para distribuir a los usuarios del parqueo |
| **Prioridad** | Alta |
| **Story Points** | 8 |

**Criterios de Aceptacion:**
- [ ] El administrador puede seleccionar tipo de medio y parqueo
- [ ] El administrador puede indicar la cantidad de QRs a generar
- [ ] Se genera un ID de lote unico para agrupar los QRs
- [ ] Cada QR tiene un codigo unico consecutivo (formato QR-XXXXXX)
- [ ] Se registra traza de auditoria con IP del solicitante
- [ ] El operador (USUARIO) genera QRs automaticamente para su parqueo asignado
- [ ] Se retorna informacion del lote generado (cantidad, loteId, tipoMedio, parqueo)

---

### QR-002: Visualizar listado de QRs generados

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Ver el listado de todos los QRs generados con paginacion |
| **Para** | Tener visibilidad del inventario de codigos QR disponibles |
| **Prioridad** | Alta |
| **Story Points** | 3 |

**Criterios de Aceptacion:**
- [ ] El listado muestra QRs con paginacion (page, limit)
- [ ] ADMINISTRADOR ve QRs de todos los parqueos
- [ ] USUARIO ve solo QRs de sus parqueos asignados
- [ ] Se muestra: codigo, tipo de medio, parqueo, estado, fecha de generacion

---

### QR-003: Descargar PDF de QRs por lote

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Descargar un PDF con las tarjetas QR de un lote para impresion |
| **Para** | Imprimir y distribuir fisicamente los codigos QR a los usuarios |
| **Prioridad** | Alta |
| **Story Points** | 5 |

**Criterios de Aceptacion:**
- [ ] Se genera un PDF con formato de tarjetas para cada QR del lote
- [ ] Cada tarjeta incluye la imagen QR y el codigo legible
- [ ] El PDF se descarga con nombre `qrs-{loteId}.pdf`
- [ ] ADMINISTRADOR puede descargar cualquier lote
- [ ] USUARIO solo puede descargar lotes de sus parqueos

---

### QR-004: Ver informacion de lotes generados

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Ver la informacion consolidada de todos los lotes generados |
| **Para** | Gestionar el inventario de QRs y saber cuantos hay por lote |
| **Prioridad** | Media |
| **Story Points** | 3 |

**Criterios de Aceptacion:**
- [ ] Se lista cada lote con: ID, tipo de medio, parqueo, cantidad de QRs, fecha
- [ ] Se puede navegar a los QRs de un lote especifico
- [ ] Filtrado por parqueo segun rol del usuario

---

### QR-005: Eliminar QR (borrado virtual)

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Eliminar uno o varios QRs sin borrarlos fisicamente |
| **Para** | Desactivar QRs que ya no se necesitan manteniendo la trazabilidad |
| **Prioridad** | Alta |
| **Story Points** | 5 |

**Criterios de Aceptacion:**
- [ ] La eliminacion establece `activo = false` (soft delete)
- [ ] Se puede eliminar un QR individual por ID
- [ ] Se pueden eliminar multiples QRs por IDs
- [ ] Se pueden eliminar todos los QRs de un lote
- [ ] Se registra traza de auditoria por cada QR eliminado
- [ ] USUARIO solo puede eliminar QRs de sus parqueos asignados

---

### QR-006: Anular QR

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Anular un QR cambiando su estado a "anulado" |
| **Para** | Invalidar un QR sin eliminarlo del sistema (para control) |
| **Prioridad** | Media |
| **Story Points** | 3 |

**Criterios de Aceptacion:**
- [ ] El estado del QR cambia a "anulado"
- [ ] Un QR anulado no puede ser usado para registro de entrada/salida
- [ ] Se puede anular un QR individual o multiples QRs
- [ ] Se registra traza de auditoria
- [ ] No se puede anular un QR ya anulado (error 400)

---

### QR-007: Buscar QR por codigo

| Atributo | Valor |
|---|---|
| **Como** | Operador |
| **Quiero** | Buscar un QR especifico por su codigo |
| **Para** | Verificar rapidamente la informacion de un QR escaneado |
| **Prioridad** | Alta |
| **Story Points** | 2 |

**Criterios de Aceptacion:**
- [ ] La busqueda retorna el QR con toda su informacion
- [ ] Se muestra: codigo, tipo de medio, parqueo, estado, lote
- [ ] USUARIO solo puede buscar QRs de sus parqueos asignados
- [ ] Si no existe, retorna 404

---

### QR-008: Contar QRs disponibles por tipo de medio

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Ver cuantos QRs disponibles hay por tipo de medio |
| **Para** | Saber cuando generar mas QRs para un tipo de vehiculo |
| **Prioridad** | Baja |
| **Story Points** | 2 |

**Criterios de Aceptacion:**
- [ ] Retorna la cantidad de QRs con estado "disponible"
- [ ] Filtrado por tipo de medio y parqueo segun rol

---

## Epic 2: Control de Acceso

### AUTH-001: Iniciar sesion

| Atributo | Valor |
|---|---|
| **Como** | Usuario del sistema |
| **Quiero** | Iniciar sesion con mis credenciales (usuario y contrasena) |
| **Para** | Acceder a las funcionalidades del sistema segun mi rol |
| **Prioridad** | Alta |
| **Story Points** | 5 |

**Criterios de Aceptacion:**
- [ ] Se valida usuario y contrasena
- [ ] Se genera access token JWT (expiracion corta)
- [ ] Se genera refresh token (expiracion larga)
- [ ] Se retorna informacion del usuario con roles y funciones
- [ ] Credenciales incorrectas retornan 401 con mensaje claro
- [ ] El refresh token se almacena en el documento del usuario

---

### AUTH-002: Refrescar token

| Atributo | Valor |
|---|---|
| **Como** | Usuario autenticado |
| **Quiero** | Obtener un nuevo access token usando mi refresh token |
| **Para** | Continuar usando el sistema sin tener que iniciar sesion nuevamente |
| **Prioridad** | Alta |
| **Story Points** | 3 |

**Criterios de Aceptacion:**
- [ ] Se valida el refresh token contra la estrategia Passport
- [ ] Se genera un nuevo par de access + refresh tokens
- [ ] Se actualiza el refresh token en el documento del usuario
- [ ] Token invalido retorna 401

---

### AUTH-003: Cerrar sesion

| Atributo | Valor |
|---|---|
| **Como** | Usuario autenticado |
| **Quiero** | Cerrar sesion de forma segura |
| **Para** | Evitar que terceros usen mi cuenta |
| **Prioridad** | Alta |
| **Story Points** | 2 |

**Criterios de Aceptacion:**
- [ ] Se invalida el refresh token del usuario
- [ ] Se requiere JWT valido para cerrar sesion
- [ ] Se retorna confirmacion de cierre

---

### AUTH-004: Registrar nuevo usuario

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Crear cuentas de usuario con roles y funciones asignadas |
| **Para** | Controlar quien accede al sistema y con que permisos |
| **Prioridad** | Alta |
| **Story Points** | 5 |

**Criterios de Aceptacion:**
- [ ] Se valida que el userName sea unico
- [ ] La contrasena se hashea con bcrypt antes de almacenarse
- [ ] Se pueden asignar roles y funciones al crear el usuario
- [ ] Se pueden asignar parqueos al usuario
- [ ] Se registra traza de auditoria

---

### AUTH-005: Recuperar contrasena

| Atributo | Valor |
|---|---|
| **Como** | Usuario |
| **Quiero** | Recuperar mi contrasena mediante correo electronico |
| **Para** | Poder acceder al sistema si olvide mi contrasena |
| **Prioridad** | Media |
| **Story Points** | 5 |

**Criterios de Aceptacion:**
- [ ] Se envia un correo con codigo de verificacion
- [ ] El usuario puede restablecer la contrasena con el codigo
- [ ] El codigo tiene expiracion
- [ ] Se usa template Handlebars para el correo

---

### AUTH-006: Cambiar contrasena

| Atributo | Valor |
|---|---|
| **Como** | Usuario autenticado |
| **Quiero** | Cambiar mi contrasena |
| **Para** | Mantener la seguridad de mi cuenta |
| **Prioridad** | Media |
| **Story Points** | 3 |

**Criterios de Aceptacion:**
- [ ] Se valida la contrasena actual
- [ ] Se valida que la nueva contrasena cumpla requisitos de seguridad
- [ ] Se hashea la nueva contrasena
- [ ] El administrador puede cambiar la contrasena de cualquier usuario

---

## Epic 3: Gestion de Movimientos

### MOV-001: Registrar entrada de vehiculo

| Atributo | Valor |
|---|---|
| **Como** | Operador de parqueo |
| **Quiero** | Registrar la entrada de un vehiculo escaneando un codigo QR |
| **Para** | Controlar el acceso y tener registro de quien esta en el parqueo |
| **Prioridad** | Alta |
| **Story Points** | 8 |

**Criterios de Aceptacion:**
- [ ] Se escanea un QR y se verifica que este disponible (no usado/anulado)
- [ ] Se registra fecha/hora de entrada automaticamente
- [ ] Se asocia el tipo de medio, parqueo y precio vigente
- [ ] Se calcula el precio unitario segun la tarifa vigente
- [ ] El estado del QR cambia a "usado"
- [ ] Se registra traza de auditoria
- [ ] Si no hay conexion, se guarda en la cola de pendientes (SQLite)

---

### MOV-002: Registrar salida de vehiculo

| Atributo | Valor |
|---|---|
| **Como** | Operador de parqueo |
| **Quiero** | Registrar la salida de un vehiculo escaneando su QR |
| **Para** | Liberar el espacio y calcular el monto a cobrar |
| **Prioridad** | Alta |
| **Story Points** | 8 |

**Criterios de Aceptacion:**
- [ ] Se verifica que el vehiculo esta dentro del parqueo (sin fechaSalida)
- [ ] Se registra fecha/hora de salida automaticamente
- [ ] Se calcula el tiempo de estadía
- [ ] Se calcula el monto cobrado segun precio y tiempo
- [ ] Se registra traza de auditoria
- [ ] Si no hay conexion, se guarda en la cola de pendientes

---

### MOV-003: Ver vehiculos dentro del parqueo

| Atributo | Valor |
|---|---|
| **Como** | Operador de parqueo |
| **Quiero** | Ver la lista de vehiculos que estan actualmente dentro del parqueo |
| **Para** | Tener visibilidad de la ocupacion actual |
| **Prioridad** | Alta |
| **Story Points** | 3 |

**Criterios de Aceptacion:**
- [ ] Se listan movimientos sin fechaSalida (vehiculos dentro)
- [ ] Se puede filtrar por parqueoId
- [ ] Se muestra: tipo de medio, hora de entrada, QR, tiempo de estadía

---

### MOV-004: Verificar estado de QR

| Atributo | Valor |
|---|---|
| **Como** | Operador de parqueo |
| **Quiero** | Verificar si un QR esta dentro o fuera del parqueo |
| **Para** | Determinar si se debe registrar entrada o salida |
| **Prioridad** | Alta |
| **Story Points** | 3 |

**Criterios de Aceptacion:**
- [ ] Se busca el movimiento activo para el QR
- [ ] Si hay movimiento sin fechaSalida, el QR esta "dentro"
- [ ] Si no hay movimiento activo, el QR esta "fuera"
- [ ] Se retorna informacion del movimiento si existe

---

### MOV-005: Obtener resumen del parqueo

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Ver un resumen del estado del parqueo |
| **Para** | Tomar decisiones operativas basadas en datos actuales |
| **Prioridad** | Media |
| **Story Points** | 5 |

**Criterios de Aceptacion:**
- [ ] Se muestra: vehiculos dentro, ingresos del dia, movimientos del dia
- [ ] Se calcula el porcentaje de ocupacion
- [ ] Filtrado por parqueoId

---

### MOV-006: Filtrar y buscar movimientos

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Filtrar y buscar movimientos por diferentes criterios |
| **Para** | Encontrar registros especificos para auditoria o consulta |
| **Prioridad** | Media |
| **Story Points** | 5 |

**Criterios de Aceptacion:**
- [ ] Filtrado generico por campos y valores
- [ ] Busqueda de texto libre
- [ ] Paginacion en resultados
- [ ] Se muestra: tipo de medio, fechas, parqueo, precio, QR

---

## Epic 4: Auditoria y Trazas

### LOG-001: Ver listado de trazas de auditoria

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Ver el listado de todas las trazas de auditoria del sistema |
| **Para** | Supervisar las operaciones realizadas en el sistema |
| **Prioridad** | Alta |
| **Story Points** | 3 |

**Criterios de Aceptacion:**
- [ ] Solo accesible por ADMINISTRADOR
- [ ] Se listan: usuario, fecha, modelo, accion, registro afectado
- [ ] Paginacion con page y limit (max 100)
- [ ] Ordenado por fecha descendente

---

### LOG-002: Ver detalle de una traza

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Ver el detalle completo de una traza de auditoria |
| **Para** | Conocer exactamente que datos se modificaron |
| **Prioridad** | Alta |
| **Story Points** | 2 |

**Criterios de Aceptacion:**
- [ ] Se muestran los datos anteriores (valorAnterior) y nuevos (valorNuevo)
- [ ] Se muestra la direccion IP del operador
- [ ] Se muestra la fecha exacta de la operacion

---

### LOG-003: Filtrar trazas por usuario y parametros

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Filtrar las trazas por usuario y otros parametros |
| **Para** | Encontrar rapidamente las operaciones de un usuario especifico |
| **Prioridad** | Media |
| **Story Points** | 5 |

**Criterios de Aceptacion:**
- [ ] Filtrado por usuario
- [ ] Filtrado por modelo/tabla
- [ ] Filtrado por accion (Adicionar, Modificar, Eliminar)
- [ ] Filtrado por rango de fechas

---

## Epic 5: Administracion

### ADM-001: Gestionar usuarios (CRUD)

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Crear, leer, actualizar y eliminar usuarios del sistema |
| **Para** | Administrar el acceso y permisos de cada persona |
| **Prioridad** | Alta |
| **Story Points** | 8 |

**Criterios de Aceptacion:**
- [ ] CRUD completo: crear, listar, ver detalle, actualizar, eliminar (soft delete)
- [ ] Eliminacion multiple por IDs
- [ ] Asignacion de roles y funciones al usuario
- [ ] Asignacion de parqueos al usuario
- [ ] Cambio de contrasena por administrador
- [ ] Filtrado y busqueda avanzada
- [ ] Solo accesible por ADMINISTRADOR

---

### ADM-002: Gestionar roles (CRUD)

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Crear y gestionar roles con sus funciones asignadas |
| **Para** | Definir perfiles de acceso al sistema |
| **Prioridad** | Alta |
| **Story Points** | 5 |

**Criterios de Aceptacion:**
- [ ] CRUD completo con nombre y descripcion
- [ ] Asignacion de funciones a roles
- [ ] Select de roles para formularios
- [ ] Trazas de auditoria en cada operacion

---

### ADM-003: Gestionar funciones (CRUD)

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Crear y gestionar funciones del sistema asociadas a endpoints |
| **Para** | Definir permisos granulares de acceso |
| **Prioridad** | Alta |
| **Story Points** | 5 |

**Criterios de Aceptacion:**
- [ ] CRUD completo con nombre y descripcion
- [ ] Asignacion de endpoints a funciones
- [ ] Asociacion con menu
- [ ] Select de funciones para formularios

---

### ADM-004: Gestionar menus (CRUD)

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Crear y gestionar el arbol de menus del sistema |
| **Para** | Organizar la navegacion segun la estructura del sistema |
| **Prioridad** | Media |
| **Story Points** | 5 |

**Criterios de Aceptacion:**
- [ ] CRUD completo con label, icon, ruta y tipo
- [ ] Jerarquia de menus (menu padre → menus hijos)
- [ ] Asociacion con nomenclador para menus dinamicos
- [ ] Filtrado por tipo de menu

---

### ADM-005: Gestionar nomencladores (CRUD generico)

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Gestionar los nomencladores del sistema (tipoMedio, parqueo) |
| **Para** | Mantener actualizados los catalogos de valores del sistema |
| **Prioridad** | Alta |
| **Story Points** | 8 |

**Criterios de Aceptacion:**
- [ ] Controlador generico que maneja multiples nomencladores por nombre
- [ ] CRUD completo: crear, listar, ver, actualizar, eliminar (virtual y real)
- [ ] Nomencladores disponibles: `tipoMedio`, `parqueo`
- [ ] Select de nomencladores para formularios
- [ ] Select dependiente (filtrado)
- [ ] Conteo de elementos por nomenclador

---

### ADM-006: Gestionar precios (CRUD)

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Configurar los precios por tipo de medio y parqueo con vigencia |
| **Para** | Definir las tarifas que se aplican automaticamente al registrar movimientos |
| **Prioridad** | Alta |
| **Story Points** | 5 |

**Criterios de Aceptacion:**
- [ ] CRUD completo con tipo de medio, parqueo, valor y fechas de vigencia
- [ ] Se valida que haya un precio vigente al registrar entrada
- [ ] Solo accesible por ADMINISTRADOR
- [ ] Importacion masiva de precios

---

## Epic 6: Aplicacion Movil

### MOB-001: Iniciar sesion en la app movil

| Atributo | Valor |
|---|---|
| **Como** | Operador de parqueo |
| **Quiero** | Iniciar sesion en la aplicacion movil con mis credenciales |
| **Para** | Acceder a las funciones de registro de entrada/salida |
| **Prioridad** | Alta |
| **Story Points** | 5 |

**Criterios de Aceptacion:**
- [ ] Login con usuario y contrasena
- [ ] Credenciales almacenadas para login offline
- [ ] Seleccion de parqueo de trabajo
- [ ] Redireccion al dashboard principal

---

### MOB-002: Escanear codigo QR

| Atributo | Valor |
|---|---|
| **Como** | Operador de parqueo |
| **Quiero** | Escanear un codigo QR con la camara del dispositivo |
| **Para** | Registrar rapidamente la entrada o salida de un vehiculo |
| **Prioridad** | Alta |
| **Story Points** | 8 |

**Criterios de Aceptacion:**
- [ ] La camara se abre al tocar el boton de escaneo
- [ ] El QR se detecta y decodifica automaticamente
- [ ] Se verifica el estado del QR (disponible, usado, anulado)
- [ ] Se determina automaticamente si es entrada o salida
- [ ] Se muestra resultado de la operacion (exito/error)

---

### MOB-003: Operar sin conexion a internet

| Atributo | Valor |
|---|---|
| **Como** | Operador de parqueo |
| **Quiero** | Registrar entradas y salidas aunque no tenga internet |
| **Para** | No interrumpir la operacion del parqueo por fallos de red |
| **Prioridad** | Alta |
| **Story Points** | 13 |

**Criterios de Aceptacion:**
- [ ] La app detecta automaticamente el estado de la red
- [ ] Las operaciones se guardan en SQLite local
- [ ] Se muestran indicadores visuales del estado offline
- [ ] Las operaciones pendientes se almacenan en cola de sincronizacion
- [ ] Se puede consultar cache de QRs, precios y tipos de medio offline

---

### MOB-004: Sincronizar datos pendientes

| Atributo | Valor |
|---|---|
| **Como** | Operador de parqueo |
| **Quiero** | Que mis registros offline se sincronicen automaticamente al recuperar conexion |
| **Para** | Asegurar que la informacion este completa en el servidor central |
| **Prioridad** | Alta |
| **Story Points** | 8 |

**Criterios de Aceptacion:**
- [ ] Deteccion automatica de recuperacion de conexion
- [ ] Envio secuencial de movimientos pendientes al API
- [ ] Manejo de reintentos ante errores
- [ ] Actualizacion del cache local con datos del servidor
- [ ] Indicadores visuales del progreso de sincronizacion

---

### MOB-005: Ver dashboard del parqueo

| Atributo | Valor |
|---|---|
| **Como** | Operador de parqueo |
| **Quiero** | Ver un resumen del estado del parqueo en mi pantalla principal |
| **Para** | Conocer la ocupacion actual y los ingresos del dia |
| **Prioridad** | Media |
| **Story Points** | 5 |

**Criterios de Aceptacion:**
- [ ] Se muestra cantidad de vehiculos dentro
- [ ] Se muestran ingresos del dia
- [ ] Se muestra listado de vehiculos dentro
- [ ] Acceso rapido al escaneo QR

---

## Epic 7: Business Intelligence

### BI-001: Ver dashboard consolidado

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Ver un dashboard con KPIs consolidados de todos los parqueos |
| **Para** | Tomar decisiones de negocio basadas en datos |
| **Prioridad** | Alta |
| **Story Points** | 8 |

**Criterios de Aceptacion:**
- [ ] ADMINISTRADOR ve datos de todos los parqueos
- [ ] USUARIO ve datos solo de sus parqueos asignados
- [ ] Filtros por rango de fechas y tipo de periodo
- [ ] KPIs: ingresos totales, vehiculos atendidos, ticket promedio, ocupacion promedio

---

### BI-002: Comparar periodos

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Comparar los ingresos entre dos periodos consecutivos |
| **Para** | Analizar tendencias de crecimiento o decrecimiento |
| **Prioridad** | Media |
| **Story Points** | 5 |

**Criterios de Aceptacion:**
- [ ] Seleccion de parqueo especifico
- [ ] Comparacion automatica con periodo anterior
- [ ] Variacion porcentual entre periodos

---

### BI-003: Analisis por tipo de medio

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Ver el analisis de uso por tipo de vehiculo |
| **Para** | Entender que tipos de vehiculos generan mas ingresos |
| **Prioridad** | Media |
| **Story Points** | 5 |

**Criterios de Aceptacion:**
- [ ] Desglose por tipo de medio
- [ ] Cantidad de movimientos por tipo
- [ ] Ingresos por tipo de medio
- [ ] Filtros por parqueo y fechas

---

### BI-004: Ver tendencias de ingresos

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Ver la evolucion de ingresos en el tiempo |
| **Para** | Identificar patrones y planificar recursos |
| **Prioridad** | Media |
| **Story Points** | 5 |

**Criterios de Aceptacion:**
- [ ] Grafico de tendencia temporal
- [ ] Granularidad: dia, semana, mes, trimestre, anio
- [ ] Filtros por parqueo
- [ ] Datos globales o por parqueo especifico

---

### BI-005: Comparativa entre parqueos

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Comparar el rendimiento entre diferentes parqueos |
| **Para** | Identificar parqueos con mejor/peor rendimiento |
| **Prioridad** | Media |
| **Story Points** | 5 |

**Criterios de Aceptacion:**
- [ ] Comparativa lado a lado de todos los parqueos
- [ ] Metricas: ingresos, vehiculos, ticket promedio, ocupacion
- [ ] Filtros por fechas

---

### BI-006: Reporte de ingresos por parqueo

| Atributo | Valor |
|---|---|
| **Como** | Administrador |
| **Quiero** | Ver el detalle diario de ingresos de un parqueo |
| **Para** | Conciliar informacion financiera |
| **Prioridad** | Media |
| **Story Points** | 3 |

**Criterios de Aceptacion:**
- [ ] Detalle diario con ingresos y cantidad de movimientos
- [ ] Filtros por rango de fechas
- [ ] USUARIO solo ve sus parqueos asignados

---

## Resumen de Story Points por Epic

| Epic | Historias | Story Points Total |
|---|---|---|
| Epic 1: Gestion de QR | 8 | 31 |
| Epic 2: Control de Acceso | 6 | 23 |
| Epic 3: Gestion de Movimientos | 6 | 32 |
| Epic 4: Auditoria y Trazas | 3 | 10 |
| Epic 5: Administracion | 6 | 36 |
| Epic 6: Aplicacion Movil | 5 | 39 |
| Epic 7: Business Intelligence | 6 | 31 |
| **TOTAL** | **40** | **202** |

---

## Priorizacion del Backlog

### Sprint 1-2 (MVP Core) — ~40 puntos
- AUTH-001 (5), AUTH-002 (3), AUTH-003 (2), AUTH-004 (5)
- QR-001 (8), QR-002 (3), QR-007 (2)
- MOV-001 (8), MOV-002 (8)

### Sprint 3-4 (MVP Operativo) — ~40 puntos
- QR-003 (5), QR-005 (5), QR-006 (3)
- MOV-003 (3), MOV-004 (3), MOV-005 (5)
- LOG-001 (3), LOG-002 (2), LOG-003 (5)
- ADM-005 (8)

### Sprint 5-6 (Administracion) — ~40 puntos
- ADM-001 (8), ADM-002 (5), ADM-003 (5)
- ADM-004 (5), ADM-006 (5)
- QR-004 (3), QR-008 (2)
- AUTH-005 (5), AUTH-006 (3)

### Sprint 7-8 (Movil) — ~40 puntos
- MOB-001 (5), MOB-002 (8), MOB-003 (13), MOB-004 (8), MOB-005 (5)

### Sprint 9-10 (BI y Mejoras) — ~40 puntos
- BI-001 (8), BI-002 (5), BI-003 (5), BI-004 (5), BI-005 (5), BI-006 (3)

---

*Documento generado como parte de la metodologia SXP (Scrum + XP Fusion)*  
*Ultima actualizacion: Marzo 2025*
