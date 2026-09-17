# Wireframe: Gestión de Dispositivos (Admin)

**Ruta:** `/admin/devices-management`  
**Componente:** `DeviceManagementComponent`  
**Layout:** `AdminLayout` (Sidebar admin + Topbar admin)  
**Rol requerido:** ADMIN  
**Guards:** `authGuard` + `adminGuard`  
**Lazy loading:** sí  
**Estado:** ✅ Implementada  

---

## Layout ASCII — Vista Principal

```
┌─────────────────────────────────────────────────────────────────┐
│  ☰  AlertaMujer Admin        [🔔]  [☀️/🌙]  [ Admin ▼ ]        │
├──────────┬──────────────────────────────────────────────────────┤
│          │                                                        │
│  📊 Dash │   📱 Gestión de Dispositivos                          │
│  👥 Usuar│                                                        │
│  🔔 Alert│   ┌──────────────┐ ┌──────────────┐ ┌─────────────┐  │
│  🗺️ Zonas│   │  TOTAL       │ │  CONECTADOS  │ │ DESCONECTAD.│  │
│  🚨 Emerg│   │              │ │              │ │             │  │
│  📋 Moder│   │     342      │ │     298      │ │     44      │  │
│  📱 Disp.│   │ dispositivos │ │  en línea    │ │  sin señal  │  │
│          │   └──────────────┘ └──────────────┘ └─────────────┘  │
│          │                                                        │
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │  FILTROS                                        │ │
│          │   │  [ 🔍 Buscar por UUID, modelo, usuaria... ]     │ │
│          │   │                                                  │ │
│          │   │  [app-chip: Todos●] [app-chip: Conectados]      │ │
│          │   │  [app-chip: Desconectados] [app-chip: Inactivos]│ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │  DISPOSITIVOS  (342 registros) [➕ REGISTRAR]   │ │
│          │   │                                                  │ │
│          │   │  ┌───┬──────────────┬──────────┬───────┬──────┐ │ │
│          │   │  │ # │ UUID / Modelo│ Usuaria  │ 🔋/GPS│ Est. │ │ │
│          │   │  ├───┼──────────────┼──────────┼───────┼──────┤ │ │
│          │   │  │ 1 │ a1b2… S21    │ Ana G.   │ 85%💪 │ ✅Co.│ │ │
│          │   │  │ 2 │ c3d4… A52    │ María L. │ 72%💪 │ ✅Co.│ │ │
│          │   │  │ 3 │ e5f6… S20    │ Laura T. │ 15%⚠️ │ ✅Co.│ │ │
│          │   │  │ 4 │ 7890… P30    │ Paula M. │ —     │ 🔴De.│ │ │
│          │   │  │ 5 │ abcd… A12    │ Sofía R. │ —     │ ⚫In.│ │ │
│          │   │  └───┴──────────────┴──────────┴───────┴──────┘ │ │
│          │   │                                                  │ │
│          │   │  [ < Anterior ]   Pág. 1 / 69  [ Siguiente > ]  │ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
└──────────┴──────────────────────────────────────────────────────┘
```

---

## Layout ASCII — Modal Detalle / Editar Dispositivo

```
                  ┌──────────────────────────────────────┐
                  │  📱 Dispositivo a1b2c3d4              │  [✕]
                  │                                       │
                  │  UUID:        a1b2c3d4-e5f6-7890      │
                  │  Modelo:      Samsung Galaxy S21      │
                  │  OS:          Android 13              │
                  │  Versión App: 2.1.0                   │
                  │                                       │
                  │  Usuaria:     Ana García              │
                  │  Correo:      ana@gmail.com           │
                  │                                       │
                  │  Estado:   ● Conectado                │
                  │  Batería:  85%  ████████████░░        │
                  │  GPS:      Fuerte  ◉◉◉◉○              │
                  │  Última sync: Hoy, 10:32 am           │
                  │                                       │
                  │  ─── Actualizar estado ───────────    │
                  │  ● Conectado  ○ Desconectado          │
                  │  ○ Inactivo                           │
                  │                                       │
                  │  [ CANCELAR ]  [ 💾 ACTUALIZAR ]      │
                  └──────────────────────────────────────┘
```

---

## Layout ASCII — Modal Registrar Dispositivo

```
                  ┌──────────────────────────────────────┐
                  │  ➕ Registrar dispositivo             │  [✕]
                  │                                       │
                  │  UUID del dispositivo *               │
                  │  [ a1b2c3d4-e5f6-7890              ] │
                  │  Formato: xxxxxxxx-xxxx-xxxx          │
                  │                                       │
                  │  Usuaria asignada *                   │
                  │  [ Buscar usuaria...        ▼       ] │
                  │                                       │
                  │  Modelo (opcional)                    │
                  │  [ Samsung Galaxy S21               ] │
                  │                                       │
                  │  Estado inicial:                      │
                  │  ● Activo   ○ Inactivo                │
                  │                                       │
                  │  [ CANCELAR ]   [ 💾 REGISTRAR ]      │
                  └──────────────────────────────────────┘
```

---

## Campos del Formulario

### Registrar dispositivo

| Campo | Tipo | Requerido | Validación |
|-------|------|-----------|------------|
| UUID | `text` | ✅ | Formato UUID v4 (`/^[0-9a-f]{8}-[0-9a-f]{4}-/i`) |
| Usuaria asignada | `select` con búsqueda | ✅ | Usuario existente en sistema |
| Modelo | `text` | ❌ | Máx. 100 caracteres |
| Estado inicial | `radio` | ✅ | `Activo` / `Inactivo` |

### Editar dispositivo (modal detalle)

| Campo | Tipo | Requerido |
|-------|------|-----------|
| Estado | `radio` | ✅ | `Conectado` / `Desconectado` / `Inactivo` |

### Filtros

| Filtro | Tipo | Valores |
|--------|------|---------|
| Búsqueda | `text` | UUID, modelo, nombre de usuaria |
| Estado | `app-chip` | Todos · Conectados · Desconectados · Inactivos |

---

## Validaciones

```typescript
// Formulario registrar dispositivo
uuid     : required, pattern UUID v4
usuaria  : required, debe existir en UsersService
modelo   : optional, maxLength(100)
estado   : required, enum['Activo','Inactivo']

// Editar estado
estado   : required, enum['Conectado','Desconectado','Inactivo']
```

---

## Estados

| Estado | Descripción | UI |
|--------|-------------|-----|
| `cargando` | Obteniendo dispositivos | Skeleton stat cards + tabla |
| `listo` | Datos cargados | Stat cards + tabla paginada |
| `filtrado` | Chips / búsqueda | Tabla actualizada |
| `modal-registrar` | Modal nuevo | Formulario vacío |
| `modal-detalle` | Click en fila | Modal con detalles + edición de estado |
| `guardando` | POST/PUT en progreso | Botón con spinner |
| `bateria-critica` | Batería < 20% | Icono y texto en rojo en tabla |
| `error` | Fallo API | Toast rojo |

---

## Flujo

```
/admin/devices-management
  ├── onInit → DevicesService.getAll()
  │             → Stat cards (total / conectados / desconectados)
  │             → Tabla paginada
  │
  ├── Búsqueda / Chips
  │     → DevicesService.getAll(filtros)
  │     → Actualiza tabla
  │
  ├── [➕ Registrar]
  │     → Modal formulario vacío
  │     → [Registrar] → DevicesService.create(datos)
  │     → Toast éxito → tabla refresca
  │
  ├── Click en fila
  │     → DevicesService.getByUsuario(usuarioId) → pre-carga modal
  │     → Modal con detalle completo (lectura + editar estado)
  │
  ├── [💾 Actualizar] estado en modal
  │     → DevicesService.updateEstado(id, nuevoEstado)
  │     → Badge actualizado en tabla; toast éxito
  │
  └── [🗑️ Eliminar] (dentro del modal detalle)
        → Modal danger: "¿Eliminar dispositivo UUID?"
        → DevicesService.delete(id)
        → Fila eliminada de tabla
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

/* Badges estado dispositivo */
--estado-conectado    : background var(--green-pale)  / color var(--green);
--estado-desconectado : background var(--red-pale)    / color var(--red);
--estado-inactivo     : background #f3f4f6 / color var(--text-dim);

/* Batería crítica (< 20%) */
color: var(--red); font-weight: 600;

/* GPS */
--gps-fuerte   : color var(--green);
--gps-moderada : color var(--amber);
--gps-debil    : color var(--red);

/* UUID en tabla */
font-family: monospace; font-size: 0.8rem; color: var(--text-dim);

/* Fila hover */
background: var(--bg-card-hover); cursor: pointer;
```

---

## Componentes Utilizados

| Componente | Variante/Config | Uso |
|------------|----------------|-----|
| `app-card` | — | Stat cards, filtros, tabla |
| `app-chip` | `active: boolean` | Filtros de estado |
| `app-button` | `primary`, `sm` | "➕ Registrar" |
| `app-modal` | `md`, `default` | Modal registrar + detalle/editar |
| `app-modal` | `sm`, `danger` | Confirmar eliminar dispositivo |
| `app-spinner` | `sm`, `inline` | Estados loading |

---

## Responsive

| Breakpoint | Comportamiento |
|------------|---------------|
| Desktop `>1024px` | Stat cards 3 columnas; tabla completa con UUID |
| Tablet `760–1024px` | Stat cards 2 columnas; UUID truncado |
| Mobile `<760px` | Stat cards apiladas; tabla scroll horizontal; UUID oculto |

---

## Accesibilidad

- Tabla con `<caption>` "Lista de dispositivos registrados"
- UUID con `aria-label="Identificador único del dispositivo"` y `title` completo
- Batería crítica con `aria-label="Batería crítica: 15%"`
- Stat card "Desconectados" con `aria-live="polite"` para actualizaciones
- Modal con `role="dialog"`, `aria-modal="true"`, foco atrapado

---

## Integración con Servicios

```typescript
// DevicesService — métodos utilizados

DevicesService.getAll(): Observable<Dispositivo[]>
// GET /devices

DevicesService.getByUsuario(usuarioId: number): Observable<Dispositivo>
// GET /devices?usuarioId=:id

DevicesService.create(datos: Partial<Dispositivo>): Observable<Dispositivo>
// POST /devices

DevicesService.update(id: number, datos: Partial<Dispositivo>): Observable<Dispositivo>
// PUT /devices/:id

DevicesService.updateEstado(id: number, estado: string): Observable<Dispositivo>
// PATCH /devices/:id/estado

DevicesService.delete(id: number): Observable<void>
// DELETE /devices/:id

// Modelo de datos
interface Dispositivo {
  id          : number;
  usuarioId   : number;
  uuid        : string;
  modelo      : string;
  os          : string;
  versionApp  : string;
  battery     : number;
  gpsSignal   : 'Fuerte' | 'Moderada' | 'Débil';
  estado      : 'Conectado' | 'Desconectado' | 'Inactivo';
  lastSync    : string;
}
```

---

## Casos de Uso

| # | Actor | Acción | Resultado |
|---|-------|--------|-----------|
| 1 | Admin | Navega a `/admin/devices-management` | Ve stat cards + tabla de todos los dispositivos |
| 2 | Admin | Filtra por "Desconectados" | Solo dispositivos sin señal activa |
| 3 | Admin | Busca por UUID parcial | Tabla filtra el dispositivo específico |
| 4 | Admin | Click "➕ Registrar" | Modal con formulario para vincular nuevo dispositivo |
| 5 | Admin | Click en fila | Modal con detalle completo del dispositivo + usuaria |
| 6 | Admin | Cambia estado a "Inactivo" en modal | Badge actualizado; dispositivo no recibirá notificaciones |
| 7 | Admin | Dispositivo con batería < 20% | Batería en rojo con indicador de alerta |
