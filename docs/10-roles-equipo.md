# 10 - Roles y Equipo

## SACP: Roles Scrum, Roles XP y Acuerdos de Equipo

---

## 1. Roles Scrum

### 1.1 Product Owner (PO)

| Aspecto | Detalle |
|---|---|
| **Responsabilidad** | Maximizar el valor del producto y gestionar el Product Backlog |
| **Mapeo XP** | On-Site Customer (cliente en el sitio) |
| **Asignado** | [Nombre del PO] |

**Funciones especificas en SACP:**

- Definir y priorizar las historias de usuario del Product Backlog
- Decidir que funcionalidades se incluyen en cada sprint
- Aceptar o rechazar historias al final del sprint (Sprint Review)
- Clarificar requisitos al equipo durante el sprint
- Validar que el software cumple las necesidades del negocio
- Tomar decisiones sobre el alcance (que entra, que queda fuera)
- Representar los intereses de administradores y operadores de parqueo

**Criterios de exito:**
- El Product Backlog esta ordenado por prioridad de valor
- Las historias tienen criterios de aceptacion claros
- El PO esta disponible para consultas durante el sprint
- Las decisiones de alcance se documentan

---

### 1.2 Scrum Master (SM)

| Aspecto | Detalle |
|---|---|
| **Responsabilidad** | Facilitar el proceso Scrum y eliminar impedimentos |
| **Mapeo XP** | XP Coach (entrenador de practicas XP) |
| **Asignado** | [Nombre del SM] |

**Funciones especificas en SACP:**

- Facilitar las ceremonias Scrum (Sprint Planning, Daily, Review, Retrospective)
- Eliminar impedimentos que bloquean al equipo
- Asegurar que se siguen las practicas Scrum y XP
- Coaching en TDD, Pair Programming y Refactoring
- Proteger al equipo de interrupciones externas durante el sprint
- Promover la mejora continua a traves de las retrospectivas
- Monitorear la salud del equipo (sustainable pace)
- Asegurar que la DoD se cumple en cada historia

**Criterios de exito:**
- Las ceremonias se realizan en tiempo y forma
- Los impedimentos se resuelven en menos de 24 horas
- El equipo mejora su velocidad sprint a sprint
- Las practicas XP se adoptan gradualmente

---

### 1.3 Equipo de Desarrollo

| Aspecto | Detalle |
|---|---|
| **Responsabilidad** | Construir el producto (diseno, codigo, tests, documentacion) |
| **Mapeo XP** | Pair Programming Partners |
| **Tamano** | 4-6 personas |

**Principios del equipo:**

- **Cross-functional**: Cada miembro puede trabajar en API, Web o Movil
- **Self-organizing**: El equipo decide como implementar las historias
- **Collective Code Ownership**: Todos son responsables de todo el codigo
- **Sustainable Pace**: No se trabaja horas extra de forma sostenida

**Composicion sugerida:**

| Rol Tecnico | Cantidad | Responsabilidad principal |
|---|---|---|
| Backend (NestJS) | 2 | API, base de datos, servicios |
| Frontend (Next.js) | 1-2 | Dashboard web, componentes UI |
| Movil (Expo) | 1-2 | App movil, offline-first, sync |
| Fullstack | 1 | Flexibilidad entre capas |

---

## 2. Roles XP (Fusion con Scrum)

### 2.1 Mapeo de Roles Scrum ↔ XP

| Rol Scrum | Rol XP | Fusion en SXP |
|---|---|---|
| Product Owner | On-Site Customer | El PO es el "cliente en el sitio" que define requisitos y prioriza |
| Scrum Master | XP Coach | El SM tambien coaching en practicas XP (TDD, Pair, Refactoring) |
| Development Team | Pair Programming Partners | Los desarrolladores trabajan en pares rotativos |
| - | Tracker | El SM o un miembro del equipo mide metricas (velocidad, coverage) |

### 2.2 Practicas XP en el Equipo

#### TDD (Test-Driven Development)

| Aspecto | Regla |
|---|---|
| **Cuando** | Siempre para nueva funcionalidad |
| **Quien** | Todos los desarrolladores |
| **Como** | Red → Green → Refactor (ciclo de 2-5 minutos) |
| **Cobertura minima** | 80% en servicios core |
| **Verificacion** | CI bloquea merge si tests fallan |

#### Pair Programming

| Aspecto | Regla |
|---|---|
| **Cuando** | Historias >= 8 story points, refactoring complejo, onboarding |
| **Rotacion** | Cada 25 minutos (Pomodoro) |
| **Modalidad** | Presencial o remoto (screen sharing) |
| **Verificacion** | Historias con pair programming se marcan en el sprint backlog |

#### Continuous Integration

| Aspecto | Regla |
|---|---|
| **Frecuencia** | Al menos 1 commit por dia por desarrollador |
| **Pipeline** | Lint → Unit Tests → E2E Tests → Build |
| **Merge** | Requiere 1 aprobacion + CI verde |
| **Verificacion** | No hay commits en main sin CI verde |

#### Refactoring

| Aspecto | Regla |
|---|---|
| **Cuando** | Continuo (regla del Boy Scout) |
| **Como** | Un refactoring por commit, tests pasando antes y despues |
| **Deuda tecnica** | Se registra como historias en el backlog |
| **Verificacion** | No hay TODO/FIXME sin ticket |

#### Simple Design

| Aspecto | Regla |
|---|---|
| **Principio** | El diseno mas simple que funciona (YAGNI) |
| **Patrones** | Usar patrones existentes (GenericController, GenericEntity) |
| **Verificacion** | Code review verifica simplicidad |

#### Coding Standards

| Aspecto | Regla |
|---|---|
| **Lenguaje** | TypeScript estricto (no any sin justificacion) |
| **Estilo** | ESLint + Prettier configurados |
| **Convenciones** | Ver Guia Tecnica (doc 05) |
| **Verificacion** | CI ejecuta lint antes de tests |

#### Collective Code Ownership

| Aspecto | Regla |
|---|---|
| **Principio** | Cualquiera puede modificar cualquier archivo |
| **Revision** | Todo cambio se revisa (code review o pair) |
| **Verificacion** | No hay "duenos" de modulos |

#### Sustainable Pace

| Aspecto | Regla |
|---|---|
| **Jornada** | 8 horas maximas por dia |
| **Horas extra** | Solo en emergencias, nunca mas de 2 dias seguidos |
| **Verificacion** | El SM monitorea la carga de trabajo |

---

## 3. Acuerdos de Equipo

### 3.1 Acuerdos de Trabajo

| # | Acuerdo | Detalle |
|---|---|---|
| 1 | **Horario** | Lunes a Viernes, 9:00 - 17:00 (o acuerdo del equipo) |
| 2 | **Daily Scrum** | 9:15 AM, maximo 15 minutos, formato: que hice, que hare, impedimentos |
| 3 | **Disponibilidad** | Responder mensajes en menos de 2 horas en horario laboral |
| 4 | **Code Review** | Revision en menos de 24 horas |
| 5 | **Commits** | Al menos 1 commit por dia por persona (integracion continua) |
| 6 | **Branches** | No se trabaja en main o develop directamente |
| 7 | **DoD** | No se mueve una historia a Done sin cumplir TODOS los criterios |
| 8 | **Pair Programming** | Obligatorio para historias >= 8 puntos |
| 9 | **TDD** | Obligatorio para nueva funcionalidad |
| 10 | **Retrospectiva** | Acciones concretas con responsable y fecha limite |

### 3.2 Acuerdos de Comunicacion

| Canal | Uso | Tiempo de respuesta |
|---|---|---|
| **Daily Scrum** | Sincronizacion diaria del equipo | Presencial/sincrono |
| **Chat (Slack/Discord)** | Preguntas rapidas, links, notas | < 2 horas |
| **Pull Requests** | Revision de codigo, discusion tecnica | < 24 horas |
| **Sprint Review** | Demo al PO y stakeholders | Cada 2 semanas |
| **Retrospectiva** | Mejora continua del proceso | Cada 2 semanas |

### 3.3 Acuerdos sobre Definicion de Hecho

El equipo acuerda cumplir la DoD completa (ver documento 04) para cada historia. Si un criterio no se puede cumplir, se eleva al SM para decidir si:

1. Se trabaja en el criterio faltante antes de cerrar la historia, o
2. Se devuelve la historia al sprint backlog con una justificacion documentada.

**No se acepta "parcialmente Done".**

### 3.4 Acuerdos sobre Estimacion

| Aspecto | Regla |
|---|---|
| **Unidad** | Story Points (Fibonacci: 1, 2, 3, 5, 8, 13) |
| **Metodo** | Planning Poker (consenso del equipo) |
| **Referencia** | 1 SP = 1 dia ideal de trabajo para un desarrollador |
| **Refactoring** | Se estima como parte de la historia, no por separado |
| **Tests** | Se incluyen en la estimacion (TDD: escribir test cuenta) |

---

## 4. Ceremonias Scrum

### 4.1 Sprint Planning

| Aspecto | Detalle |
|---|---|
| **Cuando** | Primer dia del sprint |
| **Duracion** | Maximo 4 horas (para sprint de 2 semanas) |
| **Participantes** | Todo el equipo |
| **Producto** | Sprint Backlog con historias comprometidas + Sprint Goal |

**Agenda:**
1. PO presenta las historias candidatas (10 min)
2. Equipo pregunta y clarifica (30 min)
3. Equipo estima y selecciona historias (60 min)
4. Equipo descompone historias en tareas (60 min)
5. Se define el Sprint Goal (10 min)

### 4.2 Daily Scrum

| Aspecto | Detalle |
|---|---|
| **Cuando** | Todos los dias, mismo horario |
| **Duracion** | Maximo 15 minutos |
| **Participantes** | Todo el equipo (SM facilita) |
| **Formato** | Que hice ayer, que hare hoy, tengo impedimentos |

### 4.3 Sprint Review

| Aspecto | Detalle |
|---|---|
| **Cuando** | Ultimo dia del sprint |
| **Duracion** | Maximo 2 horas |
| **Participantes** | Equipo + PO + Stakeholders |
| **Producto** | Historias aceptadas/rechazadas, feedback documentado |

### 4.4 Sprint Retrospective

| Aspecto | Detalle |
|---|---|
| **Cuando** | Ultimo dia del sprint (despues del Review) |
| **Duracion** | Maximo 1.5 horas |
| **Participantes** | Solo el equipo + SM |
| **Producto** | Acciones de mejora con responsable y fecha |
| **Formato** | Keep / Improve / Action Items |

---

## 5. Herramientas del Equipo

| Herramienta | Uso |
|---|---|
| **GitHub** | Repositorio de codigo, Pull Requests, CI/CD |
| **GitHub Projects** | Tablero Kanban para Sprint Backlog |
| **Slack/Discord** | Comunicacion diaria del equipo |
| **Figma** | Diseno de UI (si aplica) |
| **Swagger** | Documentacion de API (en `/api/docs`) |
| **Expo** | Desarrollo y build de la app movil |
| **Docker** | Ambiente de desarrollo y despliegue |

---

## 6. Onboarding de Nuevos Miembros

Cuando un nuevo miembro se une al equipo:

1. **Dia 1**: Lectura de documentacion SXP (docs 01-10)
2. **Dia 2**: Setup del ambiente de desarrollo (api + web + apk)
3. **Dia 3**: Pair programming con un miembro experimentado en una tarea simple
4. **Semana 1**: Tareas de story points 1-2 con pair programming obligatorio
5. **Semana 2**: Tareas de story points 3-5 con pair programming recomendado
6. **Semana 3+**: Integracion completa al equipo

**Buddy asignado:** Cada nuevo miembro tiene un "buddy" experimentado durante las primeras 2 semanas.

---

*Documento generado como parte de la metodologia SXP (Scrum + XP Fusion)*  
*Ultima actualizacion: Marzo 2025*
