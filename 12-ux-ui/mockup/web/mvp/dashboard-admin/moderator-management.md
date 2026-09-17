# Wireframe: Gestión de Moderadores

**Ruta:** `/admin/moderator-management`  
**Componente:** `ModeratorManagementComponent`  
**Layout:** `AdminLayout` (Sidebar admin + Topbar admin)  
**Rol requerido:** ADMIN  
**Guards:** `authGuard` + `adminGuard`  
**Lazy loading:** sí  

> ⚠️ **Nota de implementación:** La ruta `/admin/moderator-management` y el componente `ModeratorManagementComponent` existen en `navigation-map.md`. Según la tabla de servicios, se usa `UsersService` con filtro por rol `'moderador'`. El nivel de implementación real es **parcial**: la ruta está registrada y el componente existe, pero comparte el mismo `UsersService` que `UserComponent` — es probable que sea una vista filtrada de usuarios con rol moderador. El wireframe documenta la pantalla como implementada sobre esa base, con CRUD específico de moderadores.

---

## Layout ASCII — Vista Principal

```
┌─────────────────────────────────────────────────────────────────┐
│  ☰  AlertaMujer Admin        [🔔]  [☀️/🌙]  [ Admin ▼ ]        │
├──────────┬──────────────────────────────────────────────────────┤
│          │                                                        │
│  📊 Dash │   🛡️ Gestión de Moderadores                           │
│  👥 Usuar│                                                        │
│  🔔 Alert│   ┌──────────────┐  ┌──────────────┐  ┌────────────┐  │
│  🗺️ Zonas│   │  TOTAL MOD.  │  │   ACTIVOS    │  │ INACTIVOS  │  │
│  🚨 Emerg│   │              │  │              │  │            │  │
│  📋 Moder│   │      8       │  │      6       │  │     2      │  │
│  📱 Disp.│   └──────────────┘  └──────────────┘  └────────────┘  │
│          │                                                        │
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │  FILTROS Y BÚSQUEDA                             │ │
│          │   │  [ 🔍 Buscar por nombre o correo... ]           │ │
│          │   │  [app-chip: Todos●] [app-chip: Activos]         │ │
│          │   │  [app-chip: Inactivos]                          │ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │  MODERADORES (8 registros)  [➕ NUEVO MODERADOR]│ │
│          │   │                                                  │ │
│          │   │  ┌───┬──────────────┬───────────┬──────┬──────┐ │ │
│          │   │  │ # │ Nombre       │ Correo    │ Est. │ Acc. │ │ │
│          │   │  ├───┼──────────────┼───────────┼──────┼──────┤ │ │
│          │   │  │ 1 │ Carlos Vélez │ cv@g.com  │  ✅  │ ✏️ 🚫 │ │ │
│          │   │  │ 2 │ Diana Ríos   │ dr@g.com  │  ✅  │ ✏️ 🚫 │ │ │
│          │   │  │ 3 │ Fernando L.  │ fl@g.com  │  ✅  │ ✏️ 🚫 │ │ │
│          │   │  │ 4 │ Gloria Peña  │ gp@g.com  │  🔴  │ ✏️ ✅ │ │ │
│          │   │  │ 5 │ Hernán Mora  │ hm@g.com  │  ✅  │ ✏️ 🚫 │ │ │
│          │   │  └───┴──────────────┴───────────┴──────┴──────┘ │ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
└──────────┴──────────────────────────────────────────────────────┘
```

---

## Layout ASCII — Modal Crear / Editar Moderador

```
                  ┌──────────────────────────────────────┐
                  │  ➕ Nuevo moderador                   │  [✕]
                  │                                       │
                  │  ┌─────────────────┬───────────────┐  │
                  │  │ [👤] Nombre *   │ [👤] Apellido*│  │
                  │  └─────────────────┴───────────────┘  │
                  │                                       │
                  │  [✉️] Correo electrónico *             │
                  │  ──────────────────────────────────   │
                  │                                       │
                  │  [📱] Teléfono *                      │
                  │  ──────────────────────────────────   │
                  │                                       │
                  │  [🔒] Contraseña *  (solo en crear)  │
                  │  ──────────────────────────────────   │
                  │                                       │
                  │  Estado:   ● Activo   ○ Inactivo      │
                  │                                       │
                  │  Rol asignado: 🛡️ moderador (fijo)    │
                  │                                       │
                  │  [ CANCELAR ]   [ 💾 GUARDAR ]        │
                  └──────────────────────────────────────┘
```

---

## Campos del Formulario

### Crear / Editar moderador

| Campo | Tipo | Requerido | Validación |
|-------|------|-----------|------------|
| Nombre | `text` | ✅ | Mín. 2 caracteres |
| Apellido | `text` | ✅ | Mín. 2 caracteres |
| Correo electrónico | `email` | ✅ | Formato email; único en sistema |
| Teléfono | `tel` | ✅ | Formato internacional |
| Contraseña | `password` | ✅ solo crear | Mín. 8 chars, 1 mayúscula, 1 número |
| Estado | `radio` | ✅ | `Activo` / `Inactivo` |
| Rol | — | — | Siempre `'moderador'` — no editable |

### Filtros

| Filtro | Tipo | Valores |
|--------|------|---------|
| Búsqueda | `text` | Nombre o correo (debounce 300ms) |
| Estado | `app-chip` | Todos · Activos · Inactivos |

---

## Validaciones

```typescript
// Formulario crear/editar moderador
nombre   : required, minLength(2)
apellido : required, minLength(2)
correo   : required, email, unique
telefono : required, pattern internacional
password : required (solo crear), minLength(8), pattern(/[A-Z]/, /[0-9]/)
estado   : required, enum['Activo','Inactivo']
// rol: siempre 'moderador' — se fija en backend al crear
```

---

## Estados

| Estado | Descripción | UI |
|--------|-------------|-----|
| `cargando` | Obteniendo moderadores | Skeleton stat cards + tabla |
| `listo` | Datos cargados | Stat cards + tabla |
| `buscando` | Input activo | Tabla filtra con debounce |
| `modal-crear` | Modal nuevo | Formulario vacío |
| `modal-editar` | Modal editar | Formulario pre-cargado |
| `guardando` | POST/PUT | Botón con spinner |
| `activando` | PATCH updateEstado | Badge actualiza en tabla |
| `error` | Fallo API | Toast rojo |

---

## Flujo

```
/admin/moderator-management
  ├── onInit → UsersService.getAll() filtrado por rol === 'moderador'
  │             → Stat cards + tabla
  │
  ├── Búsqueda / Chips
  │     → Filtra lista localmente
  │
  ├── [➕ Nuevo moderador]
  │     → Modal formulario vacío
  │     → [Guardar] → UsersService.create({ ...datos, rol: 'moderador' })
  │     → Toast éxito → tabla refresca
  │
  ├── [✏️ Editar]
  │     → UsersService.getById(id) → pre-carga modal
  │     → [Guardar] → UsersService.update(id, datos)
  │
  ├── [🚫 Desactivar] (moderador activo)
  │     → UsersService.updateEstado(id, 'Inactivo')
  │     → Badge cambia a rojo; acción cambia a [✅ Activar]
  │
  └── [✅ Activar] (moderador inactivo)
        → UsersService.updateEstado(id, 'Activo')
        → Badge cambia a verde
```

---

## Estilos Aplicados

```css
background: var(--bg-base);

/* Stat cards */
background: var(--bg-card);
border: 1.5px solid var(--border);
border-radius: var(--dash-radius-card);
box-shadow: var(--dash-shadow-card);

/* Badges estado */
--estado-activo   : background var(--green-pale) / color var(--green);
--estado-inactivo : background var(--red-pale)   / color var(--red);

/* Fila hover */
background: var(--bg-card-hover); cursor: pointer;

/* Ícono rol moderador */
color: var(--purple-deep); /* #7c3aed */
```

---

## Componentes Utilizados

| Componente | Variante/Config | Uso |
|------------|----------------|-----|
| `app-card` | — | Stat cards, filtros, tabla |
| `app-chip` | `active: boolean` | Filtros de estado |
| `app-button` | `primary`, `sm` | "➕ Nuevo moderador" |
| `app-button` | `danger`, `sm` | Botón desactivar en fila |
| `app-modal` | `md`, `default` | Modal crear/editar |
| `app-spinner` | `sm`, `inline` | Estados loading |

---

## Responsive

| Breakpoint | Comportamiento |
|------------|---------------|
| Desktop `>1024px` | Stat cards 3 columnas; tabla completa |
| Tablet `760–1024px` | Stat cards 2 columnas; tabla sin teléfono |
| Mobile `<760px` | Stat cards apiladas; tabla con scroll horizontal |

---

## Accesibilidad

- Tabla con `<caption>` "Lista de moderadores del sistema"
- Botones de acción con `aria-label` por moderador
- Rol fijo en modal marcado como `readonly` con `aria-readonly="true"`
- Modal con `role="dialog"`, `aria-modal="true"`, foco atrapado
- Contador "8 registros" con `aria-live="polite"`

---

## Integración con Servicios

```typescript
// UsersService — métodos utilizados (filtrados por rol 'moderador')

UsersService.getAll(): Observable<Usuario[]>
// GET /usuarios  → filtra client-side por rol === 'moderador'
// o GET /usuarios?rol=moderador si el backend lo soporta

UsersService.create(datos: Omit<Usuario, 'id'>): Observable<Usuario>
// POST /usuarios  con { ...datos, rol: 'moderador' }

UsersService.update(id: number, datos: Partial<Usuario>): Observable<Usuario>
// PUT /usuarios/:id

UsersService.updateEstado(id: number, estado: string): Observable<Usuario>
// PATCH /usuarios/:id/estado

// Modelo reutilizado
interface Usuario {
  id      : number;
  nombre  : string;
  apellido: string;
  correo  : string;
  telefono: string;
  rol     : 'moderador';   // siempre en esta pantalla
  estado  : 'Activo' | 'Inactivo';
}
```

---

## Casos de Uso

| # | Actor | Acción | Resultado |
|---|-------|--------|-----------|
| 1 | Admin | Navega a `/admin/moderator-management` | Ve stat cards + lista de moderadores |
| 2 | Admin | Click "➕ Nuevo moderador" | Modal de formulario vacío con rol fijo |
| 3 | Admin | Guarda nuevo moderador | Aparece en tabla con badge verde "Activo" |
| 4 | Admin | Click [✏️] en moderador | Modal pre-cargado con sus datos |
| 5 | Admin | Click [🚫] en moderador activo | Estado cambia a Inactivo; pierde acceso al sistema |
| 6 | Admin | Click [✅] en moderador inactivo | Estado vuelve a Activo |
| 7 | Admin | Busca por nombre | Tabla filtra en tiempo real |
