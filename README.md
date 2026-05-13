# SACP - Documentacion Tecnica y Metodologica

> **Sistema Automatizado de Control de Parqueos (SACP)**
> Documentacion del proyecto bajo la metodologia **SXP (Scrum + XP Fusion)**

---

## Descripcion General

El SACP es un sistema integral para la gestion y control de parqueos vehiculares que permite la administracion de codigos QR, registro de entradas/salidas de vehiculos, calculo automatico de precios, trazabilidad de operaciones y analisis de negocio mediante Business Intelligence.

### Componentes del Sistema

| Componente | Tecnologia | Descripcion |
|---|---|---|
| **api-sacp** | NestJS + MongoDB | API REST con autenticacion JWT, gestion de QRs, movimientos, precios y trazas de auditoria |
| **web-sacp** | Next.js 16 + shadcn/ui | Dashboard web de administracion para gestionar QRs, usuarios, roles y trazas |
| **apk-sacp** | Expo/React Native | Aplicacion movil para operadores de parqueo con escaneo QR y modo offline-first |

### Metodologia: SXP (Scrum + XP Fusion)

SXP combina las practicas de gestion de proyecto de **Scrum** (Sprints, Product Owner, Scrum Master, Daily Scrum, Sprint Review, Retrospective, Backlog) con las practicas de ingenieria de **Extreme Programming** (TDD, Pair Programming, CI, Refactoring, Simple Design, Coding Standards, Collective Code Ownership, Sustainable Pace).

> **Referencia**: Henrik Kniberg, "Scrum and XP from the Trenches" (2007)

---

## Indice de Documentacion

| # | Documento | Descripcion |
|---|---|---|
| 01 | [Vision del Producto](docs/01-vision-producto.md) | Declaracion de vision, problema, usuarios objetivo y metricas de exito |
| 02 | [Arquitectura del Sistema](docs/02-arquitectura-sistema.md) | Arquitectura de 3 capas, componentes, flujo de datos, seguridad |
| 03 | [Backlog del Producto](docs/03-backlog-producto.md) | Historias de usuario organizadas por epics con prioridad y story points |
| 04 | [Definicion de Hecho](docs/04-definicion-de-hecho.md) | Criterios DoD extendidos con practicas XP |
| 05 | [Guia Tecnica](docs/05-guia-tecnica.md) | Coding standards, TDD, CI/CD, Pair Programming, Git workflow |
| 06 | [Referencia API](docs/06-api-referencia.md) | Documentacion completa de endpoints REST |
| 07 | [Modelo de Datos](docs/07-modelo-datos.md) | Colecciones MongoDB, relaciones, indices, documentos de ejemplo |
| 08 | [Aplicacion Movil](docs/08-aplicacion-movil.md) | Clean Architecture, offline-first, sync, flujo de pantallas |
| 09 | [Registro de Sprints](docs/09-sprints-registro.md) | Plantilla para seguimiento de sprints |
| 10 | [Roles y Equipo](docs/10-roles-equipo.md) | Roles Scrum/XP, acuerdos de equipo |

---

## Estructura del Repositorio

```
docs-sacp/
├── README.md                          # Este archivo - Indice principal
├── docs/
│   ├── 01-vision-producto.md          # Vision y charter del producto
│   ├── 02-arquitectura-sistema.md     # Arquitectura del sistema
│   ├── 03-backlog-producto.md         # Backlog del producto
│   ├── 04-definicion-de-hecho.md      # Definicion de Hecho (DoD)
│   ├── 05-guia-tecnica.md             # Guia de practicas tecnicas
│   ├── 06-api-referencia.md           # Referencia de la API REST
│   ├── 07-modelo-datos.md             # Modelo de datos MongoDB
│   ├── 08-aplicacion-movil.md         # Documentacion de la app movil
│   ├── 09-sprints-registro.md         # Plantilla de registro de sprints
│   └── 10-roles-equipo.md             # Roles y equipo
└── SACP-Documentacion-Tecnica.docx    # Documentacion tecnica previa
```

---

## Convenciones

- **Idioma**: Toda la documentacion esta en espanol
- **Formato**: Markdown (.md)
- **Capturas**: Donde se necesiten diagramas o capturas de pantalla, se indica con `📸 **Captura pendiente**: [descripcion]`
- **Versionamiento**: La documentacion se versiona junto con el codigo en el repositorio `docs-sacp`

---

## Licencia

Documentacion interna del proyecto SACP. Uso restringido al equipo de desarrollo.

---

*Ultima actualizacion: Marzo 2025*
