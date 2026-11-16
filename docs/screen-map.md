# Screen Map

Mapa de rutas y pantallas del front-end. Documento de **referencia y guía** del proyecto:
define qué pantallas existen y dónde viven. Los estados internos de cada pantalla
(loading, vacío, error, sellado, etc.) se definen módulo por módulo, aparte.

Vocabulario de dominio: ver `GLOSSARY.md` del backend.

---

## Layouts

```
src/layouts/
├── AuthLayout.tsx        → Login / First-access (sin autenticación)
├── AdminLayout.tsx       → Dashboard del administrador
├── DashboardLayout.tsx   → Dashboard del juez general + perfil
└── RefereeLayout.tsx     → Dashboard del árbitro
```

---

## Rutas por perfil

### Auth (AuthLayout)

| Ruta | Pantalla | Descripción |
|------|----------|-------------|
| `/login` | Login | Acceso para todos los roles (usuario + contraseña) |
| `/first-access` | First Access | Cambio obligatorio de contraseña (cuenta semilla) |

### Administrador (AdminLayout)

| Ruta | Pantalla | Descripción |
|------|----------|-------------|
| `/admin/accounts` | Cuentas a mi cargo | CRUD de cuentas de jueces generales |

### Juez general (DashboardLayout)

| Ruta | Pantalla | Descripción |
|------|----------|-------------|
| `/judge/tournaments` | Lista de torneos | Todos los torneos del juez |
| `/judge/tournaments/:id/overview` | Overview | Mesas + árbitros → asignación directa |
| `/judge/tournaments/:id/participants` | Participantes | Alta individual + drawer de importación de Excel |
| `/judge/tournaments/:id/group-phase` | Fase de grupos | Configuración → sorteo → publicación → posiciones y cruces |
| `/judge/tournaments/:id/scheduling` | Programación | Colas por mesa + tablero didáctico SVG (vistas conmutables) |
| `/judge/tournaments/:id/referees` | Árbitros | Gestión de cuentas de árbitros del torneo |
| `/judge/tournaments/:id/knockout` | Llave final | Modal de configuración + bracket visual |
| `/judge/tournaments/:id/results` | Resultados | Podios + botón "Cerrar torneo" |
| `/judge/tournaments/:id/audit-log` | Auditación | Correcciones y reprogramaciones del torneo |

> `/judge/tournaments/:id` hace redirect a `overview`.

### Árbitro (RefereeLayout)

| Ruta | Pantalla | Descripción |
|------|----------|-------------|
| `/referee/matches` | Mis partidos | Partidos asignados + registro de resultados (modal) |

### Transversal (DashboardLayout)

| Ruta | Pantalla | Descripción |
|------|----------|-------------|
| `/profile` | Perfil propio | Datos personales + cambio de contraseña (secciones colapsables) |

---

## Módulos de `src/modules/`

```
src/modules/
├── auth/              → Login, first-access, sesión
├── account/           → Gestión de cuentas (admin ↔ jueces, juez ↔ árbitros)
├── tournament/        → Lista y contexto de torneo seleccionado
├── participants/      → CRUD + importación de Excel (drawer)
├── group-phase/       → Configuración, sorteo, publicación, posiciones, cruces
├── scheduling/        → Colas de mesa + tablero didáctico SVG
├── referees/          → Gestión de cuentas de árbitros del torneo
├── knockout/          → Configuración de llave + bracket visual
├── results/           → Podios + cierre del torneo
├── audit/             → Auditación del torneo
└── profile/           → Perfil propio + cambio de contraseña
```

---

## Decisiones de diseño

- **Listados en pantallas, acciones rápidas en modales.** Cada módulo tiene su ruta
  de listado; crear/editar/confirmar va en modal o drawer.
- **Dashboard con menú lateral.** El juez navega por secciones independientes bajo
  `/judge/tournaments/:id/*`; cada sección carga solo sus datos.
- **Cuentas separadas por rol.** El administrador gestiona jueces generales
  (`/admin/accounts`); el juez general, los árbitros de cada torneo
  (`/judge/tournaments/:id/referees`). No hay pantalla compartida.
- **Importación de Excel en drawer.** La vista previa de importación
  (válidas / inválidas / duplicadas / advertencias) es un drawer dentro de
  `participants`, no una ruta propia.
- **Fase de grupos en pantalla única.** Flujo secuencial (configurar → sortear →
  publicar → consultar) con selector de grupo por categoría × género × modalidad.
- **Scheduling con vistas conmutables.** Lista de colas por defecto, toggle al
  tablero didáctico SVG; mismo contexto, dos representaciones.
- **Llave final en pantalla única.** La configuración inicial (clasificados por
  grupo, siembra cruzada) va en modal; el bracket domina la vista.
- **Resultados y cierre en una sola pantalla.** "Cerrar el torneo" es una acción,
  no un estado: botón al final de `/results`; tras el cierre queda en solo lectura.
- **Vista pública fuera de este proyecto.** La consulta pública en vivo se
  construye en un proyecto separado.

### Regla de sellado (aplica a group-phase y otras secciones)

Antes de **publicar la fase de grupos**: edición libre.
Después: **corrección auditada** — las correcciones pasan por un flujo que registra
quién, cuándo y cuál era el valor anterior (visible en `/audit-log`). Lo mismo
aplica a reprogramaciones y a correcciones de resultados con dependencias ya jugadas.

---

## Resumen

| Concepto | Cantidad |
|----------|----------|
| Layouts | 4 |
| Pantallas (rutas) | 16 |
| Módulos funcionales | 11 |
