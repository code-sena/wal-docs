# Wireframe: Gestión de Emergencias

**Ruta:** `/admin/emergency-management`  
**Componente:** `EmergencyManagementComponent`  
**Layout:** `AdminLayout` (Sidebar admin + Topbar admin)  
**Rol requerido:** ADMIN  
**Guards:** `authGuard` + `adminGuard`  
**Lazy loading:** sí  

> ⚠️ **Nota de implementación:** La ruta `/admin/emergency-management` existe en `navigation-map.md` y hay un `EmergencyManagementComponent` registrado. Sin embargo, según la tabla de servicios API de `navigation-map.md`, el servicio `EmergencyService` (dispatch, updateStatus) **no está listado** en la sección "Servicios API por Ruta" del mapa de navegación web — solo aparece en la petición original. La pantalla se documenta como **implementada parcialmente**: la ruta y el componente existen, pero el servicio puede estar en estado de integración. El wireframe refleja la funcionalidad esperada basada en los métodos `dispatch` y `updateStatus` del `EmergencyService`.

---

## Layout ASCII — Vista Principal

```
┌─────────────────────────────────────────────────────────────────┐
│  ☰  AlertaMujer Admin        [🔔]  [☀️/🌙]  [ Admin ▼ ]        │
├──────────┬──────────────────────────────────────────────────────┤
│          │                                                        │
│  📊 Dash │   🚨 Gestión de Emergencias                           │
│  👥 Usuar│                                                        │
│  🔔 Alert│   ┌──────────────┐ ┌──────────────┐ ┌─────────────┐  │
│  🗺️ Zonas│   │  ACTIVAS     │ │  DESPACHADAS │ │  RESUELTAS  │  │
│  🚨 Emerg│   │              │ │              │ │             │  │
│  📋 Moder│   │     3        │ │      7       │ │     42      │  │
│  📱 Disp.│   │ 🔴 urgente   │ │  en camino   │ │  este mes   │  │
│          │   └──────────────┘ └──────────────┘ └─────────────┘  │
│          │                                                        │
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │  FILTROS                                        │ │
│          │   │  [ 🔍 Buscar por ubicación, usuaria... ]        │ │
│          │   │  [app-chip: Todas●] [app-chip: Activa]          │ │
│          │   │  [app-chip: Despachada] [app-chip: Resuelta]    │ │
│          │   │                            [app-period-badge ▼] │ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │  EMERGENCIAS  (52 registros)                    │ │
│          │   │                                                  │ │
│          │   │  ┌───┬──────────────┬───────────┬──────┬──────┐ │ │
│          │   │  │ # │ Usuaria      │ Ubicación │ Hora │ Est. │ │ │
│          │   │  ├───┼──────────────┼───────────┼──────┼──────┤ │ │
│          │   │  │ 1 │ Ana García   │ Cra 7 #34 │10:32 │🔴Acti│ │ │
│          │   │  │ 2 │ María López  │ Calle 12  │10:15 │🟡Desp│ │ │
│          │   │  │ 3 │ Laura Torres │ Parq.Cntl │09:50 │🟡Desp│ │ │
│          │   │  │ 4 │ Sofía Ruíz   │ Av. 68    │09:30 │✅Res.│ │ │
│          │   │  │ 5 │ Paula Moreno │ Cra 15    │09:00 │✅Res.│ │ │
│          │   │  └───┴──────────────┴───────────┴──────┴──────┘ │ │
│          │   │                                                  │ │
│          │   │  [ < Anterior ]    Pág. 1 / 11  [ Siguiente > ] │ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
└──────────┴──────────────────────────────────────────────────────┘
```

---

## Layout ASCII — Modal Gestionar Emergencia

```
                  ┌──────────────────────────────────────┐
                  │  🚨 Emergencia #1 — Ana García        │  [✕]
                  │                                       │
                  │  Estado actual:  🔴 Activa            │
                  │  Hora inicio:    Hoy, 10:32 am        │
                  │                                       │
                  │  📍 Ubicación                         │
                  │  Cra 7 #34-12, Bogotá                 │
                  │  [ MINI MAPA ]                        │
                  │                                       │
                  │  Alerta asociada: #1 — SOS            │
                  │                                       │
                  │  ─────────────────────────────────    │
                  │  Acciones:                            │
                  │                                       │
                  │  [ 🚑 DESPACHAR UNIDAD ]              │
                  │  Envía señal de respuesta             │
                  │                                       │
                  │  Actualizar estado:                   │
                  │  ○ Activa  ● Despachada  ○ Resuelta   │
                  │                                       │
                  │  Notas de respuesta:                  │
                  │  [ Patrulla enviada al sector... ]    │
                  │                                       │
                  │  [ CANCELAR ]  [ 💾 ACTUALIZAR ]      │
                  └──────────────────────────────────────┘
```

---

## Campos del Formulario

### Modal — Gestionar emergencia

| Campo | Tipo | Requerido | Validación |
|-------|------|-----------|------------|
| Estado | `radio` | ✅ | `Activa` / `Despachada` / `Resuelta` |
| Notas de respuesta | `textarea` | ❌ | Máx. 500 caracteres |

### Filtros

| Filtro | Tipo | Valores |
|--------|------|---------|
| Búsqueda libre | `text` | Ubicación, nombre de usuaria |
| Estado | `app-chip` | Todas · Activa · Despachada · Resuelta |
| Período | `app-period-badge` | Hoy · Semana · Mes |

---

## Validaciones

```typescript
// Modal actualizar emergencia
estado: required, enum['Activa','Despachada','Resuelta']
notas : optional, maxLength(500)
```

---

## Estados

| Estado | Descripción | UI |
|--------|-------------|-----|
| `cargando` | Obteniendo emergencias | Skeleton stat cards + tabla |
| `listo` | Datos cargados | Stat cards + tabla paginada |
| `filtrado` | Chips / búsqueda | Tabla actualizada |
| `modal-detalle` | Click en fila | Modal con detalle + mapa |
| `despachando` | Click "Despachar" | Spinner + estado cambia a Despachada |
| `actualizando` | PATCH en progreso | Botón con spinner |
| `urgente` | Emergencia activa > 10 min | Fila en rojo pulsante |
| `error` | Fallo API | Toast rojo |

---

## Flujo

```
/admin/emergency-management
  ├── onInit → EmergencyService (fetch emergencias)
  │             → Stat cards + tabla paginada
  │
  ├── Filtros / búsqueda
  │     → Actualiza tabla
  │
  ├── Click en fila
  │     → Abre modal con detalle
  │
  ├── [🚑 Despachar unidad] en modal
  │     → EmergencyService.dispatch(id)
  │     → Estado cambia a "Despachada"
  │     → Toast confirmación
  │
  └── Actualizar estado + notas
        → EmergencyService.updateStatus(id, { estado, notas })
        → Toast éxito → tabla refresca
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

/* Badges de estado */
--estado-activa     : background var(--red-pale)   / color var(--red);
--estado-despachada : background var(--amber-pale) / color var(--amber);
--estado-resuelta   : background var(--green-pale)  / color var(--green);

/* Stat card "Activas" — número */
color: var(--red); font-size: 2rem; font-weight: 700;

/* Fila urgente */
background: var(--red-pale); animation: pulse 2s infinite;

/* Fila hover */
background: var(--bg-card-hover); cursor: pointer;
```

---

## Componentes Utilizados

| Componente | Variante/Config | Uso |
|------------|----------------|-----|
| `app-card` | — | Stat cards, filtros, tabla |
| `app-chip` | `active: boolean` | Filtros de estado |
| `app-period-badge` | `conDropdown: true` | Período temporal |
| `app-button` | `primary`, `md` | "Despachar unidad" en modal |
| `app-modal` | `lg`, `default` | Modal detalle + actualización |
| `app-spinner` | `sm`, `inline` | Estados loading |

---

## Responsive

| Breakpoint | Comportamiento |
|------------|---------------|
| Desktop `>1024px` | Stat cards 3 columnas; tabla completa |
| Tablet `760–1024px` | Stat cards 2 columnas; tabla compacta |
| Mobile `<760px` | Stat cards apiladas; tabla scroll horizontal |

---

## Accesibilidad

- Stat cards con `role="status"` y `aria-live="polite"`
- Tabla con `<caption>` "Lista de emergencias"
- Filas urgentes con `aria-label="Emergencia urgente sin despachar"`
- Modal con `role="dialog"`, `aria-modal="true"`, foco atrapado
- Botón despachar con `aria-label` descriptivo

---

## Integración con Servicios

```typescript
// EmergencyService — métodos utilizados

EmergencyService.dispatch(id: number): Observable<Emergencia>
// POST /emergencies/:id/dispatch
// Cambia estado a 'Despachada' y registra timestamp de despacho

EmergencyService.updateStatus(id: number, datos: { estado: string; notas?: string }): Observable<Emergencia>
// PATCH /emergencies/:id/status

// Modelo de datos (inferido de los métodos del servicio)
interface Emergencia {
  id            : number;
  alertaId      : number;     // Alerta SOS que generó la emergencia
  usuarioId     : number;
  usuarioNombre : string;
  ubicacion     : string;
  lat           : number;
  lng           : number;
  estado        : 'Activa' | 'Despachada' | 'Resuelta';
  horaInicio    : string;     // ISO timestamp
  horaDespacho  : string | null;
  notas         : string | null;
}
```

---

## Casos de Uso

| # | Actor | Acción | Resultado |
|---|-------|--------|-----------|
| 1 | Admin | Navega a `/admin/emergency-management` | Ve stat cards + tabla de emergencias |
| 2 | Admin | Emergencia activa sin despachar > 10 min | Fila pulsante en rojo para llamar atención |
| 3 | Admin | Click en fila | Modal con detalle + mini mapa de ubicación |
| 4 | Admin | Click "Despachar unidad" | Estado cambia a "Despachada"; toast confirmación |
| 5 | Admin | Cambia estado a "Resuelta" + agrega notas | Badge actualizado en tabla; notas guardadas |
| 6 | Admin | Filtra por "Despachadas" | Solo emergencias en tránsito visibles |
