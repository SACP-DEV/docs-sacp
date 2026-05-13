# 09 - Registro de Sprints

## SACP: Plantilla para Seguimiento de Sprints

---

## Formato de Registro

Cada sprint debe completar las siguientes secciones al finalizar el sprint.

---

## Sprint X: [Nombre del Sprint]

### Informacion General

| Campo | Valor |
|---|---|
| **Sprint #** | X |
| **Nombre** | [Nombre descriptivo] |
| **Fecha inicio** | YYYY-MM-DD |
| **Fecha fin** | YYYY-MM-DD |
| **Duracion** | 2 semanas |
| **Scrum Master** | [Nombre] |
| **Product Owner** | [Nombre] |
| **Equipo** | [Lista de miembros] |

---

### Sprint Goal

> [Objetivo claro y conciso del sprint. Debe ser negociable pero medible.]

**Ejemplo:** "Implementar el flujo completo de generacion y visualizacion de codigos QR, permitiendo al administrador generar lotes y descargar PDFs para impresion."

---

### Sprint Backlog

| ID | Historia | Story Points | Asignado a | Estado |
|---|---|---|---|---|
| QR-001 | Generar lote de QRs | 8 | [Nombre] | Done / In Progress / Pending |
| QR-002 | Visualizar listado de QRs | 3 | [Nombre] | Done / In Progress / Pending |
| QR-007 | Buscar QR por codigo | 2 | [Nombre] | Done / In Progress / Pending |
| AUTH-001 | Iniciar sesion | 5 | [Nombre] | Done / In Progress / Pending |

**Total Story Points comprometidos:** X  
**Story Points completados:** X  
**Velocidad del sprint:** X puntos

---

### Burndown Chart

📸 **Captura pendiente**: Burndown chart del Sprint X

```
Story Points
  18 │ *
     │   *
  15 │     *
     │       *
  12 │         *
     │           *
   9 │             *
     │               *
   6 │                 *
     │                   *
   3 │                     *
     │                       *
   0 └─────────────────────────*─── Dia
     1  2  3  4  5  6  7  8  9  10

     --- Linea ideal
     *** Progreso real
```

| Dia | Puntos Restantes | Trabajo Realizado |
|---|---|---|
| 1 | 18 | Setup, planning |
| 2 | 16 | QR-001 inicio |
| 3 | 14 | QR-001 progreso |
| 4 | 12 | QR-001 completado |
| 5 | 10 | QR-002 inicio |
| 6 | 8 | QR-002 completado, QR-007 inicio |
| 7 | 6 | QR-007 completado, AUTH-001 inicio |
| 8 | 4 | AUTH-001 progreso |
| 9 | 2 | AUTH-001 completado |
| 10 | 0 | Testing, deploy |

---

### Daily Scrum (Resumen)

| Dia | Impedimentos | Acuerdos |
|---|---|---|
| Dia 1 | Ninguno | Iniciar QR-001 |
| Dia 2 | Ninguno | Continuar QR-001, pair programming |
| Dia 3 | Problema con generacion de PDF | Pedir ayuda a [Nombre] |
| Dia 4 | Resuelto problema PDF | Completar QR-001, iniciar QR-002 |
| ... | ... | ... |

---

### Sprint Review

**Asistentes:** [Lista]

**Demostracion realizada:**
- [ ] Funcionalidad 1 demostrada
- [ ] Funcionalidad 2 demostrada
- [ ] ...

**Feedback del Product Owner:**
- [Nota feedback]

**Feedback de stakeholders:**
- [Nota feedback]

**Historias aceptadas:**
| ID | Historia | Aceptada? | Notas |
|---|---|---|---|
| QR-001 | Generar lote de QRs | Si/No | [Notas] |
| QR-002 | Visualizar listado de QRs | Si/No | [Notas] |
| ... | ... | ... | ... |

**Historias no completadas (pasan al siguiente sprint):**
| ID | Historia | Razon |
|---|---|---|
| ... | ... | [Razon de no completar] |

---

### Sprint Retrospective

#### Que fue bien (Keep)

1. [Ejemplo: El pair programming en QR-001 mejoro la calidad del codigo]
2. [Ejemplo: Los tests unitarios escritos primero (TDD) redujeron bugs]
3. [...]

#### Que puede mejorarse (Improve)

1. [Ejemplo: El estimado de AUTH-001 fue bajo, deberia haber sido 8 en vez de 5]
2. [Ejemplo: La comunicacion sobre el formato de PDF no fue clara desde el inicio]
3. [...]

#### Acciones concretas (Action Items)

| # | Accion | Responsable | Fecha limite | Estado |
|---|---|---|---|---|
| 1 | [Ejemplo: Refinar DoD para incluir verificacion de Swagger] | [Nombre] | YYYY-MM-DD | Pending/Done |
| 2 | [Ejemplo: Crear template de PDF antes de codificar] | [Nombre] | YYYY-MM-DD | Pending/Done |
| 3 | [Ejemplo: Aumentar estimacion de tareas de auth a 8 pts] | Equipo | Proximo sprint | Pending/Done |

---

### Metricas del Sprint

| Metrica | Valor |
|---|---|
| **Story Points comprometidos** | X |
| **Story Points completados** | X |
| **Velocidad (puntos/sprint)** | X |
| **Tasa de completitud** | X% |
| **Bugs encontrados** | X |
| **Bugs resueltos** | X |
| **Deuda tecnica agregada** | [Descripcion] |
| **Deuda tecnica resuelta** | [Descripcion] |
| **Pair programming sessions** | X horas |
| **Cobertura de tests** | X% |

---

## Historico de Velocidad

| Sprint | Puntos Comprometidos | Puntos Completados | Velocidad |
|---|---|---|---|
| Sprint 1 | - | - | - |
| Sprint 2 | - | - | - |
| Sprint 3 | - | - | - |
| ... | ... | ... | ... |
| **Promedio** | **-** | **-** | **-** |

📸 **Captura pendiente**: Grafico de velocidad historical

---

## Planificacion de Sprints (Sugerencia)

### Sprint 1 (Semanas 1-2): Autenticacion y Core
**Goal:** "Implementar autenticacion JWT completa y generacion de QRs"

| Historia | Story Points |
|---|---|
| AUTH-001: Iniciar sesion | 5 |
| AUTH-002: Refrescar token | 3 |
| AUTH-003: Cerrar sesion | 2 |
| AUTH-004: Registrar usuario | 5 |
| QR-001: Generar lote de QRs | 8 |
| QR-002: Visualizar listado de QRs | 3 |
| QR-007: Buscar QR por codigo | 2 |
| **Total** | **28** |

### Sprint 2 (Semanas 3-4): Movimientos y Operaciones
**Goal:** "Completar el flujo de registro de entrada y salida de vehiculos"

| Historia | Story Points |
|---|---|
| MOV-001: Registrar entrada | 8 |
| MOV-002: Registrar salida | 8 |
| MOV-003: Ver vehiculos dentro | 3 |
| MOV-004: Verificar estado QR | 3 |
| QR-003: Descargar PDF de QRs | 5 |
| QR-005: Eliminar QR | 5 |
| **Total** | **32** |

### Sprint 3 (Semanas 5-6): Auditoria y Administracion
**Goal:** "Implementar trazas de auditoria y gestion de nomencladores"

| Historia | Story Points |
|---|---|
| LOG-001: Ver trazas de auditoria | 3 |
| LOG-002: Ver detalle de traza | 2 |
| LOG-003: Filtrar trazas | 5 |
| ADM-005: Gestionar nomencladores | 8 |
| ADM-006: Gestionar precios | 5 |
| MOV-005: Resumen del parqueo | 5 |
| **Total** | **28** |

### Sprint 4 (Semanas 7-8): Administracion Avanzada
**Goal:** "Completar la administracion de usuarios, roles y funciones"

| Historia | Story Points |
|---|---|
| ADM-001: Gestionar usuarios | 8 |
| ADM-002: Gestionar roles | 5 |
| ADM-003: Gestionar funciones | 5 |
| ADM-004: Gestionar menus | 5 |
| AUTH-005: Recuperar contrasena | 5 |
| AUTH-006: Cambiar contrasena | 3 |
| **Total** | **31** |

### Sprint 5 (Semanas 9-10): Aplicacion Movil
**Goal:** "Desplegar la aplicacion movil con operacion offline-first"

| Historia | Story Points |
|---|---|
| MOB-001: Login en app movil | 5 |
| MOB-002: Escanear QR | 8 |
| MOB-003: Operar sin conexion | 13 |
| MOB-004: Sincronizar pendientes | 8 |
| MOB-005: Dashboard del parqueo | 5 |
| **Total** | **39** |

### Sprint 6 (Semanas 11-12): Business Intelligence
**Goal:** "Implementar dashboard de BI y reportes para administradores"

| Historia | Story Points |
|---|---|
| BI-001: Dashboard consolidado | 8 |
| BI-002: Comparar periodos | 5 |
| BI-003: Analisis tipo de medio | 5 |
| BI-004: Tendencias de ingresos | 5 |
| BI-005: Comparativa parqueos | 5 |
| BI-006: Reporte de ingresos | 3 |
| **Total** | **31** |

---

*Documento generado como parte de la metodologia SXP (Scrum + XP Fusion)*  
*Ultima actualizacion: Marzo 2025*
