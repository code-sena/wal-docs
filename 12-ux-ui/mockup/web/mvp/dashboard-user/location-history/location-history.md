# Wireframe: Historial de Ubicaciones

**Ruta:** `/dashboard/location-history`  
**Componente:** `LocationHistoryComponent`  
**Layout:** `DashboardLayout` (Sidebar + Topbar + BottomNav)  
**Rol requerido:** USER (operador)  
**Guards:** `authGuard` + `userGuard`  
**Lazy loading:** sí  
**Estado:** ✅ Implementada  

---

## Layout ASCII

```
┌─────────────────────────────────────────────────────────────────┐
│  ☰  AlertaMujer              [🔔]  [☀️/🌙]  [ Ana García ▼ ]   │
├──────────┬──────────────────────────────────────────────────────┤
│          │                                                        │
│  🏠 Home │   🗺️ Historial de Ubicaciones                         │
│  📍 Ubic.│                                                        │
│  🔔 Alert│   ┌─────────────────────────────────────────────────┐ │
│  🎬 Evid.│   │  MAPA CON TRAYECTORIA                           │ │
│  📱 Disp.│   │                                                  │ │
│          │   │  ┌──────────────────────────────────────────┐   │ │
│          │   │  │                                          │   │ │
│          │   │  │         [ MAPA LEAFLET ]                 │   │ │
│          │   │  │                                          │   │ │
│          │   │  │  📍 Punto inicio (verde)                 │   │ │
│          │   │  │  ·····················                   │   │ │
│          │   │  │  📍 Punto final (rojo)                   │   │ │
│          │   │  │                                          │   │ │
│          │   │  │  ── Trayectoria polilínea punteada        │   │ │
│          │   │  └──────────────────────────────────────────┘   │ │
│          │   │                                                  │ │
│          │   │  [ 🗑️ LIMPIAR SELECCIÓN ]   [app-period-badge ▼] │ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │  HISTORIAL DE PUNTOS  (18 registros)            │ │
│          │   │                                                  │ │
│          │   │  [ 🔍 Buscar por dirección...           ]       │ │
│          │   │                                                  │ │
│          │   │  ┌────┬─────────────┬──────────────┬──────┬───┐ │ │
│          │   │  │ #  │ Fecha/Hora  │ Dirección    │ GPS  │ 🔋│ │ │
│          │   │  ├────┼─────────────┼──────────────┼──────┼───┤ │ │
│          │   │  │  1 │ Hoy 10:32  │ Cra 7 #34-12 │ 💪   │85%│ │ │
│          │   │  │  2 │ Hoy 10:27  │ Av. El Dorado│ 💪   │84%│ │ │
│          │   │  │  3 │ Hoy 10:15  │ Calle 26     │ ⚡   │82%│ │ │
│          │   │  │  4 │ Hoy 09:50  │ Cra 30       │ 💪   │80%│ │ │
│          │   │  │  5 │ Hoy 09:30  │ Parque Simón │ 💪   │79%│ │ │
│          │   │  └────┴─────────────┴──────────────┴──────┴───┘ │ │
│          │   │                                                  │ │
│          │   │  [ < Anterior ]              [ Siguiente > ]    │ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
└──────────┴──────────────────────────────────────────────────────┘
```

---

## Campos del Formulario

No hay formulario de creación. La pantalla es de visualización e historial.

### Controles de filtro

| Control | Tipo | Descripción |
|---------|------|-------------|
| Buscador de dirección | `text` input | Filtra la tabla por coincidencia en el campo `address` |
| Período | `app-period-badge` | Hoy · Última semana · Último mes · Personalizado |

### Datos mostrados — Tabla

| Columna | Fuente | Descripción |
|---------|--------|-------------|
| # | índice | Número de fila |
| Fecha/Hora | `UbicacionEntry.time` | Timestamp formateado |
| Dirección | `UbicacionEntry.address` | Dirección formateada completa |
| GPS | `UbicacionEntry.gpsSignal` | Ícono de nivel de señal |
| Batería | `UbicacionEntry.battery` | Porcentaje con mini barra de color |

### Mapa — Trayectoria

| Elemento | Descripción |
|----------|-------------|
| Polilínea | Línea punteada que une todos los puntos del historial en orden temporal |
| Marcador inicio | Pin verde → punto más antiguo del período seleccionado |
| Marcador final | Pin rojo → punto más reciente |
| Marcadores intermedios | Círculos pequeños púrpura al hacer click en fila |
| Popup | Click en marcador muestra: dirección + hora + batería + GPS |

---

## Validaciones

```typescript
// Filtro buscador (client-side)
busqueda: no requerido, filtra en tiempo real con debounce 300ms
```

---

## Estados

| Estado | Descripción | UI |
|--------|-------------|-----|
| `cargando` | Obteniendo historial | Spinner sobre mapa + skeleton en tabla |
| `listo` | Datos cargados | Mapa con polilínea + tabla con registros |
| `filtrado-busqueda` | Texto en buscador | Tabla actualizada; mapa solo muestra puntos filtrados |
| `filtrado-periodo` | Período cambiado | Fetch nuevo; mapa y tabla actualizados |
| `seleccion-fila` | Click en fila | Mapa hace flyTo + popup abierto en marcador |
| `sin-datos` | Sin historial | Mapa vacío + mensaje "No hay registros de ubicación" |
| `error` | Fallo API | Banner rojo + botón reintentar |

---

## Flujo

```
/dashboard/location-history
  ├── onInit → PhoneLocationService.getHistory(usuarioId)
  │             → Llena tabla
  │             → Dibuja polilínea + marcadores en mapa
  │
  ├── Input buscador (debounce 300ms)
  │     → Filtra tabla localmente por `address`
  │     → Mapa actualiza puntos visibles
  │
  ├── Cambio en app-period-badge
  │     → PhoneLocationService.getHistory(usuarioId, período)
  │     → Actualiza tabla y redibuja polilínea
  │
  ├── Click en fila de tabla
  │     → Mapa flyTo(lat, lng) de esa entrada
  │     → Popup con datos de esa ubicación
  │     → Fila resaltada con fondo var(--purple-pale)
  │
  └── [Limpiar selección]
        → Quita highlight de fila y cierra popup del mapa
        → Vuelve a vista de polilínea completa
```

---

## Estilos Aplicados

### Colores
```css
/* Fondo de página */
background: var(--bg-base); /* #f5f4ff */

/* Cards */
background: var(--bg-card);
border: 1.5px solid var(--border);
border-radius: var(--dash-radius-card); /* 12px */
box-shadow: var(--dash-shadow-card);

/* Polilínea en mapa */
color: var(--purple-deep); /* #7c3aed */
stroke-dasharray: 6, 4;
stroke-width: 3;

/* Marcador inicio */
color: var(--green); /* #16a34a */

/* Marcador final */
color: var(--red); /* #dc2626 */

/* Marcadores intermedios */
color: var(--purple-mid); /* #a78bfa */
radius: 5px;

/* Señal GPS */
--gps-fuerte   : var(--green);
--gps-moderada : var(--amber);
--gps-debil    : var(--red);

/* Batería inline */
--bat-high   : var(--green);
--bat-medium : var(--amber);
--bat-low    : var(--red);

/* Fila seleccionada */
background: var(--purple-pale);   /* #ede9fe */
border-left: 3px solid var(--purple-deep);

/* Modo oscuro — filtro mapa */
filter: invert(1) hue-rotate(180deg) brightness(0.85) contrast(0.9) saturate(0.6);
```

### Tipografía
```css
/* Título página */
font-size: 2rem; font-weight: 700; color: var(--text-primary);

/* Cabecera tabla */
font-size: 0.875rem; font-weight: 600; color: var(--text-muted);

/* Celdas tabla */
font-size: 0.875rem; color: var(--text-primary);

/* Buscador */
font-size: 0.9rem;
```

### Espaciado
```css
padding: 24px;          /* página */
gap: 24px;              /* entre card mapa y card tabla */

/* Mapa */
height: 380px;
border-radius: var(--dash-radius-card);

/* Tabla */
padding: 12px 16px;     /* filas */
```

---

## Componentes Utilizados

| Componente | Variante/Config | Uso |
|------------|----------------|-----|
| `app-card` | — | Contenedor mapa y contenedor tabla |
| `app-period-badge` | `conDropdown: true` | Selector de período temporal |
| `app-button` | `secondary`, `sm` | "Limpiar selección" |
| `app-spinner` | `md`, `inline` | Estado cargando |
| Mapa Leaflet | tiles Google Maps | Visualización de trayectoria |
| Input búsqueda nativo | `text` con ícono 🔍 | Filtro de dirección |

---

## Responsive

| Breakpoint | Comportamiento |
|------------|---------------|
| Desktop `>1024px` | Mapa 380px; tabla con todas las columnas |
| Tablet `760–1024px` | Mapa 280px; tabla sin columna GPS detallada |
| Mobile `<760px` | Mapa 220px; tabla con scroll horizontal; buscador a ancho completo; sidebar → BottomNav |

---

## Accesibilidad

- Mapa con `aria-label="Mapa de trayectoria de ubicaciones"`
- Tabla con `<caption>` "Historial de ubicaciones del dispositivo"
- Buscador con `aria-label="Filtrar por dirección"` y `role="searchbox"`
- Filas de tabla como `role="button"` + `aria-label="Ver en mapa: [dirección]"`
- Polilínea con descripción textual en `aria-description` del contenedor del mapa
- Popup del mapa accesible con texto completo (no solo visual)

---

## Integración con Servicios

```typescript
// PhoneLocationService — método utilizado

// Obtener historial de ubicaciones
PhoneLocationService.getHistory(usuarioId: number): Observable<UbicacionEntry[]>
// GET /phone-location/:usuarioId/history

// Con filtro de período
PhoneLocationService.getHistory(usuarioId: number, desde: string, hasta: string): Observable<UbicacionEntry[]>
// GET /phone-location/:usuarioId/history?desde=...&hasta=...

// Modelo de datos
interface UbicacionEntry {
  id        : number;
  usuarioId : number;
  time      : string;
  address   : string;
  gpsSignal : 'Fuerte' | 'Moderada' | 'Débil';
  battery   : number;
  lat       : number;
  lng       : number;
}
```

---

## Casos de Uso

| # | Actor | Acción | Resultado |
|---|-------|--------|-----------|
| 1 | Usuaria | Navega a `/dashboard/location-history` | Ve mapa con trayectoria del día + tabla completa |
| 2 | Usuaria | Cambia período a "Última semana" | Mapa redibuja polilínea semanal; tabla actualiza |
| 3 | Usuaria | Escribe "Cra 7" en buscador | Tabla filtra; mapa muestra solo esos puntos |
| 4 | Usuaria | Click en fila | Mapa hace zoom al punto; popup con detalle |
| 5 | Usuaria | Click "Limpiar selección" | Mapa vuelve a vista global de la polilínea |
| 6 | Usuaria | Sin historial en período | Mapa vacío + mensaje descriptivo |
