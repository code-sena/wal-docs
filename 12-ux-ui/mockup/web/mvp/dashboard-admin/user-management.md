# Gestión de Usuarios

**Ruta:** `/admin/usuarios`
**Acceso:** ADMIN
**Estado de implementación:** Implementada — `UserComponent` con lazy loading; consume `UsersService` (getAll, getById, create, update, updateEstado, delete); tabla paginada con búsqueda y filtros.

## Estilo base — design tokens
```css
/* Fondo de página */
background: var(--bg-base); /* #f5f4ff */

/* Cards */
background: var(--bg-card);
border: 1.5px solid var(--border);   /* #c4b5fd */
border-radius: var(--dash-radius-card); /* 12px */
box-shadow: var(--dash-shadow-card);

/* Badge estado Activo */
background: var(--green-pale); color: var(--green); /* #dcfce7 / #16a34a */

/* Badge estado Inactivo */
background: #f3f4f6; color: var(--text-dim); /* #9ca3af */

/* Badge estado Bloqueado */
background: var(--red-pale); color: var(--red); /* #fee2e2 / #dc2626 */

/* Badge estado Warning */
background: var(--amber-pale); color: var(--amber);

/* Chip filtro activo */
background: var(--purple-pale); border: 1.5px solid var(--purple-deep);
color: var(--purple-deep);

/* Fila hover */
background: var(--bg-card-hover); /* #f0eeff */ cursor: pointer;

/* Nombre en tabla */
font-size: 0.9rem; font-weight: 600; color: var(--text-primary);

/* Correo / teléfono */
font-size: 0.875rem; color: var(--text-muted);

/* Título página */
font-size: 2rem; font-weight: 700; color: var(--text-primary);

/* Contador registros */
font-size: 0.875rem; color: var(--text-muted);

/* Página padding */
padding: 24px; gap: 16px;

/* Filas tabla */
padding: 12px 16px;
```

## Objetivo
Permitir al administrador visualizar, crear, editar, activar/desactivar y eliminar cuentas de usuarias (rol operador) con búsqueda en tiempo real y filtrado por estado.

## Estructura (de arriba a abajo)
1. AdminLayout: Sidebar + Topbar
2. Título: "👥 Gestión de Usuarios"
3. Card "Filtros y búsqueda":
   - Input 🔍 "Buscar por nombre, correo, teléfono..." (debounce 300ms)
   - Chips: Todos● / Activos / Inactivos / Bloqueados
   - `app-period-badge` — período de registro
4. Card "Usuarios (N registros)" con botón "➕ NUEVO USUARIO":
   - Tabla paginada: # | Nombre | Correo | Teléfono | Estado | Acciones
   - Acciones por fila: [✏️ Editar] [🗑️ Eliminar]
   - Click en nombre → modal de detalle con métricas
   - Paginación con "Página X / Y"
5. **Modal Crear/Editar Usuario:**
   - Grid 2 cols: Nombre + Apellido | Edad + Teléfono
   - Correo | Contraseña (solo en crear) | Estado (radio: Activo/Inactivo)
6. **Modal Ver Detalle:**
   - Datos personales + Rol + Fecha registro + Alertas totales/pendientes + Dispositivos
   - Botones: ✏️ Editar / 🚫 Desactivar / 🔴 Bloquear
7. **Modal Confirmar Eliminar (danger)**

## Componentes usados
- `app-card` — filtros y tabla
- `app-chip` con `active: boolean` — filtros de estado
- `app-period-badge` con `conDropdown: true` — período de registro
- `app-button` variant `primary`, size `sm` — "➕ Nuevo usuario"
- `app-modal` size `md`, default — crear/editar/detalle
- `app-modal` size `sm`, danger — confirmar eliminación
- `app-spinner` size `sm`, inline — guardando

## Estados
- **cargando:** Skeleton de tabla (5 filas)
- **listo:** Tabla con paginación
- **buscando:** Tabla actualiza con debounce 300ms
- **filtrado:** Chips activos + contador actualizado
- **modal-crear:** Formulario vacío
- **modal-editar:** Formulario pre-cargado
- **modal-detalle:** Vista solo lectura con métricas
- **guardando:** Botón con spinner
- **eliminando:** Modal danger activo
- **error:** Toast rojo

## Restricciones
- **Mobile < 760px:** Cards por usuario en lugar de tabla; sidebar colapsado
- **Tablet 760–1024px:** Tabla sin columna teléfono; buscador compacto
- **Desktop > 1024px:** Tabla completa con todas las columnas
- **WCAG AA:** Tabla con `<caption>`; buscador con `aria-label` y `role="searchbox"`; botones con `aria-label` descriptivo por usuario; modales con `role="dialog"`, `aria-modal="true"`, foco atrapado; badges con texto visible; paginación con `aria-label="Página X de Y"`
- **Modo oscuro:** Tokens dark activos

## Prompt listo para Google Stitch
> Diseña la pantalla Gestión de Usuarios de AlertaMujer Web en Angular para el rol Admin. Plataforma web, layout AdminLayout con sidebar y topbar. Fondo `#f5f4ff`. La pantalla tiene título "👥 Gestión de Usuarios" en `2rem 700 #1e1a3c` y dos cards blancas con `border: 1.5px solid #c4b5fd` y `border-radius 12px`: (1) Card Filtros con input 🔍 "Buscar por nombre, correo, teléfono..." con debounce, chips multi-select (Todos, Activos, Inactivos, Bloqueados) con estado activo en `#ede9fe`/`#7c3aed`, y `app-period-badge`; (2) Card tabla paginada con header "Usuarios (1.234 registros)" y botón "➕ NUEVO USUARIO", tabla con columnas # | Nombre (0.9rem 600) | Correo (0.875rem #6b7280) | Teléfono | Estado | Acciones, badges de estado (Activo fondo `#dcfce7` texto `#16a34a`, Inactivo fondo `#f3f4f6` texto `#9ca3af`, Bloqueado fondo `#fee2e2` texto `#dc2626`), botones ✏️ y 🗑️ por fila, fila con hover `#f0eeff` y cursor pointer, paginación "Página X / Y". Modal de crear/editar con grid 2 columnas para Nombre+Apellido y Edad+Teléfono, correo, contraseña (solo crear) y estado radio. Modal de detalle con datos + métricas (alertas, dispositivos) + acciones Editar/Desactivar/Bloquear. Modal danger para eliminar. Cumple WCAG AA.
