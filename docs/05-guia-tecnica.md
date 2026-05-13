# 05 - Guia de Practicas Tecnicas

## SACP: Coding Standards, TDD, CI/CD, Pair Programming y Git Workflow

---

## 1. Coding Standards

### 1.1 TypeScript - Normas Generales

```typescript
// ✅ BIEN: Tipado explicito, nombres descriptivos
interface CreateMovimientoDto {
  tipoMedioId: string;
  parqueoId: string;
  qrEscaneado: string;
  precioId: string;
}

// ❌ MAL: Tipo any, nombres cripticos
interface Data {
  tm: any;
  p: any;
  qr: any;
}
```

| Regla | Convencion |
|---|---|
| Archivos | kebab-case: `movimiento.service.ts`, `qr.controller.ts` |
| Clases | PascalCase: `MovimientoService`, `QrEntity` |
| Interfaces | PascalCase con prefijo I (opcional): `IRepository`, `IJwtPayload` |
| Variables/funciones | camelCase: `findById`, `precioUnitarioCobrado` |
| Constantes | UPPER_SNAKE_CASE: `DATABASE_NAME`, `CONFIG_KEYS` |
| Enums | PascalCase para nombre y valores: `RolType.ADMINISTRADOR` |
| DTOs | Sufijo Dto: `CreateMovimientoDto`, `ReadQrDto` |
| Entidades | Sufijo Entity: `MovimientoEntity`, `QrEntity` |
| Repositorios | Sufijo Repository: `MovimientoRepository` |
| Controladores | Sufijo Controller: `MovimientoController` |
| Servicios | Sufijo Service: `MovimientoService` |

### 1.2 NestJS - Convenciones

```
Estructura de un modulo:
├── controller/     → Recibe HTTP, delega al service, retorna respuesta
├── service/        → Logica de negocio, orquesta repositorios
├── repository/     → Acceso a datos (TypeORM)
├── mapper/         → Conversion Entity ↔ DTO
├── entity/         → Definicion de esquema MongoDB
└── dto/            → Validacion de entrada/salida
```

**Reglas del Controlador:**
- Solo maneja HTTP (request/response)
- Delega toda logica al servicio
- Usa decoradores Swagger para documentacion
- Nunca accede directamente al repositorio

**Reglas del Servicio:**
- Contiene la logica de negocio
- Usa repositorios para acceso a datos
- Registra trazas de auditoria
- Lanza excepciones HTTP apropiadas

**Reglas del Repositorio:**
- Solo operaciones de base de datos
- Usa TypeORM con MongoDB
- No contiene logica de negocio

### 1.3 React/Next.js - Convenciones

```typescript
// ✅ Componentes con tipado explicito
interface QrCardProps {
  qr: ReadQrDto;
  onDelete: (id: string) => void;
}

export function QrCard({ qr, onDelete }: QrCardProps) {
  // ...
}
```

| Regla | Convencion |
|---|---|
| Componentes | PascalCase: `QrCard`, `LoginForm` |
| Hooks | Prefijo use: `useParkingOperation`, `useSync` |
| Paginas | page.tsx en App Router |
| Adapters | Sufijo adapter: `movimiento.adapter.ts` |
| Endpoints | Sufijo endpoint: `movimiento.endpoint.ts` |
| Services | Sufijo service: `movimiento.service.ts` |
| Models | Sufijo model: `movimiento.model.ts` |

### 1.4 React Native/Expo - Convenciones

| Regla | Convencion |
|---|---|
| Entidades de dominio | Interfaces en `src/domain/entities/` |
| Casos de uso | Sufijo UseCase: `RegistrarEntradaUseCase` |
| Repositorios | Interfaces en `src/domain/repositories/` |
| DataSources | Sufijo DataSource: `MovimientoRemoteDataSource` |
| Hooks de presentacion | Prefijo use: `useNetwork`, `useSync` |

---

## 2. TDD (Test-Driven Development)

### 2.1 Ciclo Red-Green-Refactor

```
1. RED:    Escribir un test que falle (define el comportamiento deseado)
2. GREEN:  Escribir el codigo minimo para que el test pase
3. REFACTOR: Mejorar el codigo manteniendo los tests pasando
4. Repetir
```

### 2.2 Ejemplo Practico - MovimientoService

```typescript
// 1. RED: Test primero
describe('MovimientoService - registrarEntrada', () => {
  it('debe registrar entrada cuando QR esta disponible', async () => {
    // Arrange
    const qr = { codigo: 'QR-000001', estado: 'disponible', tipoMedioId: 'tm-1' };
    const precio = { valor: 10.50, tipoMedioId: 'tm-1', parqueoId: 'p-1' };
    qrRepository.findByCodigo.mockResolvedValue(qr);
    precioRepository.findVigente.mockResolvedValue(precio);
    
    // Act
    const result = await service.registrarEntrada(user, dto, ip);
    
    // Assert
    expect(result.exitoso).toBe(true);
    expect(movimientoRepository.create).toHaveBeenCalled();
    expect(qr.estado).toBe('usado');
  });

  it('debe rechazar entrada cuando QR ya esta usado', async () => {
    const qr = { codigo: 'QR-000001', estado: 'usado' };
    qrRepository.findByCodigo.mockResolvedValue(qr);
    
    await expect(service.registrarEntrada(user, dto, ip))
      .rejects.toThrow(BadRequestException);
  });
});

// 2. GREEN: Implementacion minima
// 3. REFACTOR: Mejorar sin romper tests
```

### 2.3 Piramide de Tests

```
        /\
       /  \        E2E Tests (pocos, lentos, caros)
      /    \       - Flujo completo de API
     /______\      - Test/coverage > 50%
    /        \     
   /  Integration  \  Tests de integracion (moderados)
  /    Tests       \  - Service + Repository
 /__________________\  - Test/coverage > 70%
/                    \
/   Unit Tests        \  Tests unitarios (muchos, rapidos, baratos)
/   (TDD obligatorio)  \  - Logica de negocio pura
/________________________\ - Test/coverage > 80%
```

### 2.4 Reglas de TDD para el Equipo

1. **Siempre escribir el test primero** para nueva funcionalidad
2. **No escribir codigo de produccion** sin un test que falle
3. **Tests unitarios para servicios**: Mockear repositorios
4. **Tests e2e para controladores**: Verificar HTTP + guards + validaciones
5. **Nombre del test**: Debe describir el comportamiento esperado en espanol
6. **Arrange-Act-Assert**: Estructura clara del test

---

## 3. CI/CD Pipeline

### 3.1 Pipeline de Integracion Continua

```yaml
# .github/workflows/ci.yml (esquema)
name: CI Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  test-api:
    runs-on: ubuntu-latest
    steps:
      - checkout
      - setup Node.js 18
      - npm ci
      - npm run lint
      - npm run test:unit
      - npm run test:e2e
      - coverage report

  test-web:
    runs-on: ubuntu-latest
    steps:
      - checkout
      - setup Node.js 18
      - npm ci
      - npm run lint
      - npm run build
      - npm run test

  test-apk:
    runs-on: ubuntu-latest
    steps:
      - checkout
      - setup Node.js 18
      - npm ci
      - npm run lint
      - npm run test
```

### 3.2 Pipeline de Deployment

```
main branch → CI pasa → Build Docker → Deploy a produccion
develop    → CI pasa → Build Docker → Deploy a staging
feature/*  → CI pasa → Sin deploy automatico
```

### 3.3 Reglas de CI

1. **No se hace merge sin que CI pase**
2. **No se hace force push a main o develop**
3. **Se requiere al menos 1 aprobacion de code review**
4. **Los tests e2e usan base de datos MongoDB de test** (no produccion)
5. **El coverage se reporta pero no bloquea el merge** (goal: > 80%)

---

## 4. Pair Programming

### 4.1 Protocolo

**Driver (Conductor):**
- Escribe el codigo
- Se enfoca en la implementacion tactica
- Sigue las indicaciones del Navigator

**Navigator (Navegante):**
- Revisa el codigo mientras se escribe
- Piensa estrategicamente (diseno, tests, edge cases)
- Sugiere mejoras y detecta errores

**Rotacion:**
- Cambiar roles cada 25 minutos (tecnica Pomodoro)
- Rotar parejas al menos una vez por sprint

### 4.2 Cuando Hacer Pair Programming

| Situacion | Obligatorio | Recomendado |
|---|---|---|
| Historias con story points >= 8 | ✅ | |
| Tareas de refactoring complejo | ✅ | |
| Nuevos miembros del equipo | ✅ | |
| Bugs criticos de produccion | ✅ | |
| Historias con story points 3-5 | | ✅ |
| Tareas simples (story points 1-2) | | Opcional |

### 4.3 Modalidades

1. **Presencial**: Ambos en la misma estacion de trabajo
2. **Remoto**: Screen sharing con comunicación por voz (Discord/Meet)
3. **Async**: Code review detallado como alternativa cuando no es posible sincrono

---

## 5. Refactoring Guidelines

### 5.1 Regla del Boy Scout

> "Deja el codigo mejor de como lo encontraste"

Cada vez que tocas un archivo, mejora algo pequeno:
- Renombra una variable poco clara
- Extrae un metodo repetido
- Agrega un tipo mas especifico
- Elimina codigo muerto

### 5.2 Tecnicas de Refactoring Comunes

| Tecnica | Cuando usarla |
|---|---|
| Extract Method | Metodo largo (>30 lineas) o logica repetida |
| Rename Variable/Method | Nombre no descriptivo |
| Replace Magic Number | Numeros sin contexto en el codigo |
| Introduce Parameter Object | Mas de 3 parametros en un metodo |
| Move Method | Metodo en la clase incorrecta |
| Remove Dead Code | Codigo no utilizado |

### 5.3 Reglas de Refactoring

1. **Los tests deben pasar antes y despues del refactoring**
2. **Un refactoring a la vez** (no mezclar refactoring con nueva funcionalidad)
3. **Commits pequenos** (cada refactoring es un commit)
4. **No cambiar comportamiento**, solo estructura

---

## 6. Git Workflow

### 6.1 Estrategia de Branching (GitHub Flow simplificado)

```
main (produccion)
  │
  ├── develop (integracion)
  │     │
  │     ├── feature/SXP-001-generacion-qr
  │     ├── feature/SXP-002-registro-entrada
  │     ├── bugfix/SXP-003-fix-sync-error
  │     └── refactor/SXP-004-clean-mapper
  │
  └── hotfix/SXP-005-fix-auth-crash
```

### 6.2 Convencion de Nombres de Ramas

| Tipo | Formato | Ejemplo |
|---|---|---|
| Feature | `feature/SXP-{numero}-{descripcion}` | `feature/SXP-012-generacion-qr` |
| Bugfix | `bugfix/SXP-{numero}-{descripcion}` | `bugfix/SXP-015-fix-sync-error` |
| Refactor | `refactor/SXP-{numero}-{descripcion}` | `refactor/SXP-018-clean-mapper` |
| Hotfix | `hotfix/SXP-{numero}-{descripcion}` | `hotfix/SXP-020-fix-auth-crash` |

### 6.3 Convencion de Commits (Conventional Commits)

```
<tipo>(<ambito>): <descripcion corta>

[cuerpo opcional con mas detalle]

[footer opcional con referencia a issue]
```

**Tipos:**

| Tipo | Uso |
|---|---|
| `feat` | Nueva funcionalidad |
| `fix` | Correccion de bug |
| `refactor` | Refactoring sin cambio de comportamiento |
| `test` | Agregar o modificar tests |
| `docs` | Cambios en documentacion |
| `chore` | Tareas de mantenimiento (deps, config) |
| `style` | Formato (no cambia logica) |
| `perf` | Mejora de rendimiento |

**Ejemplos:**

```bash
feat(qr): agregar endpoint de generacion masiva de QRs
fix(sync): corregir error de duplicados al sincronizar movimientos
refactor(mapper): simplificar conversion de Entity a DTO en MovimientoMapper
test(auth): agregar tests e2e para flujo de refresh token
docs(api): actualizar referencia de endpoints de BI
chore(deps): actualizar NestJS a v10.3
```

### 6.4 Flujo de Trabajo

```
1. Crear rama desde develop:
   git checkout develop
   git pull origin develop
   git checkout -b feature/SXP-012-generacion-qr

2. Desarrollar con commits atomicos:
   git add .
   git commit -m "feat(qr): agregar DTO para generacion de lote"

3. Mantener rama actualizada:
   git fetch origin
   git rebase origin/develop

4. Push y crear Pull Request:
   git push origin feature/SXP-012-generacion-qr
   # Crear PR en GitHub: feature/SXP-012 → develop

5. Code Review + CI pasa → Squash Merge a develop

6. Release: develop → main (con tag de version)
```

### 6.5 Reglas de Proteccion

- **main**: No push directo, requiere PR + 1 aprobacion + CI verde
- **develop**: No push directo, requiere PR + CI verde
- **feature/**: Push directo permitido al autor, CI recomendado

---

## 7. Simple Design

### 7.1 Principios

1. **Ejecuta todos los tests**: El diseno debe hacer pasar todos los tests
2. **No tiene duplicacion**: Cada concepto se expresa una sola vez (DRY)
3. **Expresa la intencion**: El codigo debe comunicar su proposito claramente
4. **Minimo numero de elementos**: Menos clases, metodos y variables posibles (YAGNI)

### 7.2 Aplicado a SACP

- **Patron GenericController**: Un controlador generico maneja CRUD para todas las entidades que comparten la misma estructura
- **Patron GenericNomencladorController**: Un controlador maneja multiples nomencladores dinamicamente
- **GenericEntity**: Campos comunes (id, activo, createdAt, updatedAt) en una sola clase base
- **GenericNomencladorEntity**: Hereda de GenericEntity y agrega nombre + descripcion

---

*Documento generado como parte de la metodologia SXP (Scrum + XP Fusion)*  
*Ultima actualizacion: Marzo 2025*
