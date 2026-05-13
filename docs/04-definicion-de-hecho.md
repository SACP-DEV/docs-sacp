# 04 - Definicion de Hecho (Definition of Done)

## SACP: Criterios Extendidos con Practicas XP

---

## Principio

La Definicion de Hecho (DoD) es un acuerdo del equipo sobre lo que significa que una Historia de Usuario este realmente "terminada". En SXP, la DoD incorpora tanto los criterios de calidad de Scrum como las practicas de ingenieria de XP.

> **Una historia no esta Done hasta que cumple TODOS los criterios siguientes.**  
> No existe "parcialmente Done". Si un criterio no se cumple, la historia queda en el sprint backlog.

---

## 1. Criterios de Codigo

### 1.1 Tests Unitarios (TDD)

- [ ] **El codigo tiene tests unitarios escritos siguiendo TDD** (Red → Green → Refactor)
- [ ] Los tests cubren los caminos felices y los casos de error
- [ ] Los tests son independientes entre si (no dependen del orden de ejecucion)
- [ ] Se usan mocks/stubs para dependencias externas (base de datos, APIs)
- [ ] Cobertura minima: **80%** en servicios core (auth, movimiento, qr, precio)
- [ ] Los tests se ejecutan en menos de 30 segundos para no bloquear el flujo de trabajo

### 1.2 Tests de Integracion

- [ ] Los endpoints nuevos tienen tests e2e (ver `test/` en api-sacp)
- [ ] Se verifican los codigos de estado HTTP correctos
- [ ] Se valida el esquema de respuesta
- [ ] Se prueban los guards de autenticacion y autorizacion

### 1.3 Calidad de Codigo

- [ ] **El codigo fue revisado (Code Review) o programado en Pair Programming**
- [ ] No hay comentarios `TODO`, `FIXME` o `HACK` sin un ticket asociado
- [ ] No hay codigo muerto o comentado
- [ ] Se respetan las convenciones de nombres (ver Guia Tecnica)
- [ ] No hay duplicacion de codigo (DRY)
- [ ] Las funciones/metodos no exceden 30 lineas
- [ ] No hay `any` en TypeScript sin justificacion explicita

---

## 2. Criterios de Integracion Continua

### 2.1 Pipeline CI

- [ ] **Todos los tests pasan en el pipeline de CI** (unitarios + e2e)
- [ ] El build se completa sin warnings ni errores
- [ ] El linting pasa sin errores (ESLint)
- [ ] La compilacion de TypeScript no genera errores

### 2.2 Merge

- [ ] La rama esta actualizada con `main` (no hay conflictos)
- [ ] Al menos 1 aprobacion de code review antes de merge
- [ ] Se usa squash merge para mantener historial limpio

---

## 3. Criterios de Refactoring

### 3.1 Sin Deuda Tecnica Conocida

- [ ] **El codigo esta refactorizado y no contiene deuda tecnica conocida**
- [ ] Se aplico el principio de Simple Design (el diseno mas simple que funciona)
- [ ] Las entidades y DTOs estan alineados (no hay campos sin uso)
- [ ] Los mappers estan actualizados si se modificaron entidades
- [ ] Se eliminaron imports no utilizados

### 3.2 Principios de Diseno

- [ ] Se respeta el patron Controller → Service → Repository
- [ ] No hay logica de negocio en controladores
- [ ] No hay acceso directo a la base de datos desde controladores
- [ ] Las dependencias se inyectan, no se instancian directamente

---

## 4. Criterios de Funcionalidad

### 4.1 Criterios de Aceptacion

- [ ] **Todos los criterios de aceptacion de la historia de usuario se cumplen**
- [ ] Se probaron los flujos principales manualmente
- [ ] Se probaron los casos de error (datos invalidos, sin permisos, no encontrado)
- [ ] Se probo con los roles ADMINISTRADOR y USUARIO segun corresponda

### 4.2 Compatibilidad

- [ ] Los endpoints nuevos son compatibles con la app movil (si aplica)
- [ ] Los cambios no rompen funcionalidades existentes (tests de regresion)
- [ ] La API mantiene retrocompatibilidad (no se eliminan campos de respuesta sin versionar)

---

## 5. Criterios de Trazabilidad

### 5.1 Auditoria

- [ ] **Las operaciones CRUD registran traza de auditoria en log_history**
- [ ] Se registra: userId, action (Adicionar/Modificar/Eliminar), tabla, valorAnterior, valorNuevo, direccionIp
- [ ] La traza se genera automaticamente (no requiere codigo manual por cada endpoint)

### 5.2 Logging

- [ ] Se agregan logs apropiados en operaciones criticas (errores, advertencias)
- [ ] No se logean datos sensibles (contrasenas, tokens completos)

---

## 6. Criterios de Documentacion

### 6.1 API

- [ ] **Los endpoints estan documentados en Swagger/OpenAPI**
- [ ] Se definen: @ApiTags, @ApiOperation, @ApiResponse, @ApiBody
- [ ] Se documentan los roles requeridos con @Roles
- [ ] Los DTOs tienen @ApiProperty con ejemplos

### 6.2 Tecnica

- [ ] Si se agrego una nueva coleccion, se actualiza el Modelo de Datos (doc 07)
- [ ] Si se agrego un nuevo endpoint, se actualiza la Referencia API (doc 06)
- [ ] Si se cambio la arquitectura, se actualiza el documento de Arquitectura (doc 02)

---

## 7. Criterios de Seguridad

### 7.1 Autenticacion y Autorizacion

- [ ] Los endpoints protegidos tienen los guards apropiados (AuthGuard, RolGuard, PermissionGuard)
- [ ] Se definen los roles requeridos con @Roles
- [ ] Los endpoints publicos usan @Public() decorator explicitamente

### 7.2 Validacion

- [ ] Todos los inputs se validan con class-validator (DTOs)
- [ ] Se usa ValidationPipe global
- [ ] Se sanitizan los inputs (no hay SQL/NoSQL injection)

---

## 8. Checklist Rapido de DoD

```
DoD Quick Check - Antes de mover una historia a "Done":

[ ] Tests unitarios escritos y pasando (TDD)
[ ] Tests e2e pasando en CI
[ ] Code review aprobado o pair programmed
[ ] Codigo refactorizado, sin deuda tecnica conocida
[ ] Criterios de aceptacion todos cumplidos
[ ] Traza de auditoria implementada (para CRUD)
[ ] Endpoints documentados en Swagger
[ ] Guards de autenticacion/autorizacion correctos
[ ] DTOs con validacion class-validator
[ ] Documentacion tecnica actualizada
```

---

## Niveles de DoD

### Historia de Usuario (Item-level DoD)
Los criterios anteriores aplican a cada historia individual.

### Sprint (Sprint-level DoD)
- [ ] Todas las historias del sprint cumplen el DoD
- [ ] El sprint backlog esta completo
- [ ] No hay historias "a medio hacer"
- [ ] La demo esta preparada para el Sprint Review

### Release (Release-level DoD)
- [ ] Todos los sprints de la release cumplen DoD
- [ ] Tests de regresion completos pasan
- [ ] Migration scripts preparados (si aplica)
- [ ] Documentacion de release preparada
- [ ] Rollback plan definido

---

*Documento generado como parte de la metodologia SXP (Scrum + XP Fusion)*  
*Ultima actualizacion: Marzo 2025*
