# 01 - Vision del Producto

## SACP: Sistema Automatizado de Control de Parqueos

---

## 1. Declaracion del Problema

### Problema Principal

La gestion de parqueos vehiculares en entornos institucionales y empresariales enfrenta desafios criticos:

- **Control manual ineficiente**: El registro de entrada y salida de vehiculos se realiza de forma manual, generando errores humanos, perdida de informacion y tiempos de espera prolongados.
- **Ausencia de trazabilidad**: No existe un registro confiable de quien estuvo en el parqueo, cuando ingreso, cuando salio ni cuanto pago, lo que imposibilita la auditoria.
- **Calculo de precios impreciso**: La determinacion del monto a cobrar depende de factores como tipo de vehiculo, tiempo de estadía y tarifa vigente, que al calcularse manualmente genera inconsistencias.
- **Falta de visibilidad operativa**: Los administradores no tienen informacion en tiempo real sobre la ocupacion del parqueo, ingresos generados ni tendencias de uso.
- **Dependencia de conectividad**: En instalaciones donde la conexion a internet es intermitente, los sistemas que dependen exclusivamente de la nube quedan inoperativos.
- **Gestion de credenciales insegura**: El uso de tarjetas fisicas o sistemas de acceso sin validacion automatica permite fraudes y duplicados.

### Impacto del Problema

| Impacto | Descripcion |
|---|---|
| Perdida de ingresos | Errores en el cobro generan perdidas estimadas del 10-15% de ingresos potenciales |
| Insatisfaccion del usuario | Tiempos de espera largos y errores en cobros afectan la experiencia |
| Riesgo de seguridad | Sin trazabilidad, es imposible investigar incidentes |
| Ineficiencia operativa | Personal dedicado a tareas manuales que podrian automatizarse |
| Decisiones sin datos | Ausencia de informacion para planificar capacidad y recursos |

---

## 2. Vision del Producto

### Statement de Vision

> **Para** los administradores y operadores de parqueos institucionales  
> **que** necesitan controlar el acceso vehicular de manera eficiente y trazable,  
> **el SACP** es un sistema automatizado de control de parqueos  
> **que** permite gestionar el ciclo completo de entrada/salida de vehiculos mediante codigos QR, calcula precios automaticamente, funciona offline y proporciona analisis de negocio.  
> **A diferencia de** los sistemas manuales o semi-automatizados existentes,  
> **nuestro producto** ofrece trazabilidad completa, operacion sin conexion a internet y business intelligence en tiempo real.

### Atributos Clave del Producto

1. **Automatizacion QR**: Generacion masiva de codigos QR unicos por tipo de vehiculo y parqueo
2. **Registro digital**: Entrada y salida registrada con escaneo QR desde dispositivo movil
3. **Calculo automatico**: Precios calculados automaticamente segun tipo de medio, tiempo de estadía y tarifa vigente
4. **Offline-first**: La aplicacion movil funciona sin conexion y sincroniza cuando hay conectividad
5. **Trazabilidad**: Toda operacion CRUD queda registrada con usuario, fecha, IP y datos anteriores/nuevos
6. **Business Intelligence**: Dashboard con KPIs, tendencias, comparativas y reportes de ingresos
7. **Control de acceso**: Autenticacion JWT con roles (ADMINISTRADOR, USUARIO) y permisos por funcion

---

## 3. Usuarios Objetivo

### 3.1 Administrador (Rol: ADMINISTRADOR)

| Aspecto | Detalle |
|---|---|
| **Perfil** | Gerente o administrador general del sistema de parqueos |
| **Necesidades** | Vision global de todos los parqueos, gestion de usuarios/roles, reportes financieros, auditoria |
| **Plataforma principal** | Web dashboard (web-sacp) |
| **Acciones clave** | Generar QRs para cualquier parqueo, administrar usuarios y roles, ver trazas de auditoria, acceder a BI completo, configurar precios y nomencladores |

### 3.2 Operador de Parqueo (Rol: USUARIO)

| Aspecto | Detalle |
|---|---|
| **Perfil** | Operador que se encuentra fisicamente en el parqueo |
| **Necesidades** | Escanear QRs rapidamente, registrar entradas/salidas, ver vehiculos dentro, funcionar sin internet |
| **Plataforma principal** | Aplicacion movil (apk-sacp) |
| **Acciones clave** | Escanear QR para entrada/salida, ver vehiculos dentro del parqueo, registrar movimientos offline, sincronizar al recuperar conexion |

### 3.3 Sistema (Interno)

| Aspecto | Detalle |
|---|---|
| **Perfil** | Procesos automaticos del sistema |
| **Necesidades** | Generacion de consecutivos, calculo de precios, registro de trazas |
| **Acciones clave** | Auto-registro de trazas de auditoria, calculo automatico de precios vigentes, gestion de consecutivos de QR |

---

## 4. Resumen de Funcionalidades Clave

### Epic 1: Gestion de QR
- Generacion masiva de codigos QR por lote, tipo de medio y parqueo
- Visualizacion de QRs con filtros por lote, tipo de medio, estado
- Descarga de PDF con tarjetas QR para impresion
- Eliminacion (borrado virtual) individual, multiple y por lote
- Anulacion de QRs (cambio de estado sin eliminacion)

### Epic 2: Control de Acceso
- Autenticacion con JWT (access token + refresh token)
- Registro de usuarios con validacion
- Recuperacion de contrasena por email
- Roles: ADMINISTRADOR y USUARIO
- Permisos granulares por funciones y endpoints
- Guards: RolGuard y PermissionGuard

### Epic 3: Gestion de Movimientos
- Registro de entrada de vehiculo con escaneo QR
- Registro de salida con calculo automatico de precio
- Verificacion de estado de QR (dentro/fuera del parqueo)
- Listado de vehiculos actualmente dentro del parqueo
- Resumen del parqueo (vehiculos dentro, ingresos, etc.)
- Filtro y busqueda avanzada de movimientos

### Epic 4: Auditoria y Trazas
- Registro automatico de toda operacion CRUD (Adicionar, Modificar, Eliminar)
- Datos anteriores y nuevos almacenados en la traza
- Direccion IP del operador registrada
- Filtrado por usuario, modelo y accion
- Visualizacion detallada de cada traza

### Epic 5: Administracion
- CRUD completo de usuarios, roles, funciones, menus y nomencladores
- Asignacion de roles y funciones a usuarios
- Asignacion de parqueos a usuarios
- Nomencladores genericos (tipoMedio, parqueo) con patron reusable
- Gestion de endpoints y permisos

### Epic 6: Aplicacion Movil
- Escaneo de QR con camara del dispositivo
- Registro rapido de entrada/salida
- Funcionamiento offline-first con SQLite local
- Sincronizacion automatica de movimientos pendientes
- Cache de QRs, precios y tipos de medio

### Epic 7: Business Intelligence
- Dashboard consolidado con KPIs
- Comparacion de periodos
- Analisis por tipo de medio
- Tendencias de ingresos
- Comparativa entre parqueos
- Reporte de ingresos por parqueo
- Filtros por fecha y tipo de periodo

---

## 5. Metricas de Exito

### 5.1 Metricas de Negocio

| Metrica | Objetivo | Medicion |
|---|---|---|
| Reduccion de errores en cobro | > 95% de precision en calculo automatico | Comparacion cobro manual vs automatico |
| Tiempo de registro entrada/salida | < 10 segundos por operacion | Medicion en app movil |
| Trazabilidad completa | 100% de operaciones registradas | Verificacion de trazas vs operaciones |
| Disponibilidad offline | 100% de operaciones basicas sin conexion | Testing de escenarios offline |
| Adopcion de usuarios | > 90% de operadores usando el sistema | Usuarios activos vs registrados |

### 5.2 Metricas Tecnicas

| Metrica | Objetivo | Medicion |
|---|---|---|
| Cobertura de tests | > 80% en servicios core | Jest coverage |
| Tiempo de respuesta API | < 500ms en P95 | Monitoreo de endpoints |
| Tiempo de build CI | < 10 minutos | Pipeline metrics |
| Tasa de fallos en sincronizacion | < 1% de movimientos pendientes | Monitoreo de sync |
| Uptime de API | > 99.5% | Monitoreo de disponibilidad |

---

## 6. Restricciones y Supuestos

### Restricciones

- **Base de datos**: MongoDB como unico motor de persistencia (sin SQL)
- **Movil**: Expo/React Native limita acceso a APIs nativas especificas
- **Offline**: La sincronizacion es unidireccional (APK → API), no bidireccional
- **Autenticacion**: Solo JWT, sin integracion con proveedores externos (OAuth, SAML)
- **Despliegue**: Ambiente on-premise con recursos limitados

### Supuestos

- Los parqueos tienen al menos un punto de acceso con dispositivo movil disponible
- La conectividad a internet es intermitente pero existe al menos una vez al dia
- Los tipos de medio y precios son configurados previamente por el administrador
- Los operadores tienen dispositivos compatibles con Expo/React Native

---

## 7. Riesgos

| Riesgo | Probabilidad | Impacto | Mitigacion |
|---|---|---|---|
| Perdida de datos offline | Media | Alto | Retry automatico + almacenamiento local persistente |
| Conflictos de sincronizacion | Media | Medio | Estrategia last-write-wins con trazas |
| Cambios en requisitos de precios | Baja | Alto | Diseno flexible del modelo de precios con vigencia |
| Performance con grandes volumenes | Media | Medio | Indices MongoDB + paginacion en todos los endpoints |
| Adopcion resistida por operadores | Alta | Alto | Training + UI intuitiva + feedback continuo |

---

*Documento generado como parte de la metodologia SXP (Scrum + XP Fusion)*  
*Ultima actualizacion: Marzo 2025*
