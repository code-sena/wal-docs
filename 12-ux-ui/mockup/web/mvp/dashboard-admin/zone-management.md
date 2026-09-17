# Wireframe: Gestión de Zonas de Riesgo

**Ruta:** `/admin/zone-management`  
**Componente:** `ZoneManagementComponent`  
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
│  📊 Dash │   🗺️ Gestión de Zonas de Riesgo                       │
│  👥 Usuar│                                                        │
│  🔔 Alert│   ┌──────────────────────────┬────────────────────┐   │
│  🗺️ Zonas│   │  MAPA INTERACTIVO        │  PANEL DE ZONAS    │   │
│  🚨 Emerg│   │                          │                    │   │
│  📋 Moder│   │  ┌────────────────────┐  │  [ 🔍 Buscar zona ]│   │
│  📱 Disp.│   │  │                    │  │                    │   │
│          │   │  │  [ MAPA LEAFLET ]  │  │  [➕ NUEVA ZONA]   │   │
│          │   │  │                    │  │                    │   │
│          │   │  │  🔴 Zona alta      │  │  ┌──────────────┐  │   │
│          │   │  │  🟡 Zona media     │  │  │ 🔴 Campoa.   │  │   │
│          │   │  │  🟢 Zona baja      │  │  │ 24 alertas   │  │   │
│          │   │  │                    │  │  │ ALTO — 42%   │  │   │
│          │   │  │  [+ Agregar punto] │  │  │ [✏️][🗑️]      │  │   │
│          │   │  │  [✕ Cancelar]      │  │  └──────────────┘  │   │
│          │   │  │                    │  │                    │   │
│          │   │  └────────────────────┘  │  ┌──────────────┐  │   │
│          │   │                          │  │ 🟡 Av. Sant. │  │   │
│          │   │  Leyenda:                │  │ 15 alertas   │  │   │
│          │   │  ● Alto  ● Medio  ● Bajo │  │ MEDIO — 26%  │  │   │
│          │   │                          │  │ [✏️][🗑️]      │  │   │
│          │   │                          │  └──────────────┘  │   │
│          │   │                          │                    │   │
│          │   │                          │  ┌──────────────┐  │   │
│          │   │                          │  │ 🟢 Parq. Gzn │  │   │
│          │   │                          │  │ 6 alertas    │  │   │
│          │   │                          │  │ BAJO — 10%   │  │   │
│          │   │                          │  │ [✏️][🗑️]      │  │   │
│          │   │                          │  └──────────────┘  │   │
│          │   └──────────────────────────┴────────────────────┘   │
│          │                                                        │
└──────────┴──────────────────────────────────────────────────────┘
```

---

## Layout ASCII — Modal Crear / Editar Zona

```
                  ┌──────────────────────────────────────┐
                  │  ➕ Nueva zona de riesgo              │  [✕]
                  │                                       │
                  │  Nombre de la zona *                  │
                  │  [ Campoalegre Centro               ] │
                  │                                       │
                  │  Nivel de riesgo *                    │
                  │  ● Alto   ○ Medio   ○ Bajo            │
                  │                                       │
                  │  Descripción                          │
                  │  [ Zona con alta concentración de..  ]│
                  │                                       │
                  │  Coordenadas (centro de la zona) *    │
                  │  Lat: [ 2.2964  ]  Lng: [ -75.0199 ] │
                  │                                       │
                  │  Radio (metros) *                     │
                  │  [ 500                              ] │
                  │                                       │
                  │  [ O seleccionar en el mapa ↗️ ]       │
                  │                                       │
                  │  [ CANCELAR ]   [ 💾 GUARDAR ZONA ]   │
                  └──────────────────────────────────────┘
```

---

## Layout ASCII — Modal Eliminar Zona (danger)

```
                  ┌──────────────────────────────────────┐
                  │  🗑️ Eliminar zona                     │  [✕]
                  │                                       │
                  │  ¿Eliminar "Campoalegre Centro"?     │
                  │                                       │
                  │  Esta zona tiene 24 alertas           │
                  │  asociadas. Las alertas no serán      │
                  │  eliminadas.                          │
                  │                                       │
                  │  [ CANCELAR ]   [ 🗑️ ELIMINAR ]       │
                  └──────────────────────────────────────┘
```

---

## Campos del Formulario

### Crear / Editar zona

| Campo | Tipo | Requerido | Validación |
|-------|------|-----------|------------|
| Nombre | `text` | ✅ | Mín. 3 caracteres |
| Nivel de riesgo | `radio` | ✅ | `Alto` / `Medio` / `Bajo` |
| Descripción | `textarea` | ❌ | Máx. 300 caracteres |
| Latitud | `number` | ✅ | Rango válido para Colombia (-4 a 13) |
| Longitud | `number` | ✅ | Rango válido para Colombia (-79 a -67) |
| Radio (metros) | `number` | ✅ | Mín. 100m, máx. 10,000m |

---

## Validaciones

```typescript
// Formulario zona
nombre      : required, minLength(3)
nivel       : required, enum['Alto','Medio','Bajo']
descripcion : optional, maxLength(300)
lat         : required, min(-4), max(13)
lng         : required, min(-79), max(-67)
radio       : required, min(100), max(10000)
```

---

## Estados

| Estado | Descripción | UI |
|--------|-------------|-----|
| `cargando` | Obteniendo zonas | Spinner sobre mapa + skeleton en panel |
| `listo` | Zonas cargadas | Mapa con círculos + lista en panel |
| `buscando` | Texto en buscador | Panel filtra zonas por nombre |
| `modal-crear` | Modal nueva zona | Formulario vacío |
| `modal-editar` | Modal editar | Formulario pre-cargado |
| `modo-mapa` | Click "seleccionar en mapa" | Cursor especial en mapa; click guarda coordenadas en form |
| `guardando` | POST/PUT en progreso | Botón con spinner |
| `modal-eliminar` | Confirmar eliminar | Modal danger |
| `error` | Fallo API | Toast rojo |

---

## Flujo

```
/admin/zone-management
  ├── onInit
  │   ├── RiskZonesService.getZonas() → dibuja círculos + llena panel
  │   └── RiskZonesService.getPuntos() → dibuja CAIs y hospitales
  │
  ├── Click en zona del mapa o del panel
  │     → Mapa flyTo(lat, lng) de la zona
  │     → Destaca círculo con borde más grueso
  │
  ├── [➕ Nueva zona]
  │     → Modal formulario vacío
  │     → [Guardar] → RiskZonesService.createZona(datos)
  │     → Nuevo círculo aparece en mapa + card en panel
  │
  ├── [✏️ Editar]
  │     → Modal pre-cargado con datos de la zona
  │     → [Guardar] → RiskZonesService.updateZona(id, datos)
  │     → Círculo y card actualizados
  │
  ├── [Seleccionar en mapa]
  │     → Activa modo de selección en Leaflet
  │     → Click en mapa → coordenadas se copian al formulario
  │
  └── [🗑️ Eliminar]
        → Modal danger con advertencia de alertas asociadas
        → [Confirmar] → RiskZonesService.deleteZona(id)
        → Círculo eliminado del mapa + card eliminada del panel
```

---

## Estilos Aplicados

### Colores
```css
background: var(--bg-base);

/* Layout split — mapa + panel */
display: grid;
grid-template-columns: 1fr 360px;
gap: 16px;

/* Cards en panel */
background: var(--bg-card);
border: 1.5px solid var(--border);
border-radius: var(--dash-radius-card);
box-shadow: var(--dash-shadow-card);

/* Círculos en mapa según nivel */
--zona-alto  : fill #ef4444 / stroke #dc2626 / opacity 0.25;
--zona-medio : fill #f59e0b / stroke #d97706 / opacity 0.25;
--zona-bajo  : fill #22c55e / stroke #16a34a / opacity 0.25;

/* Círculo seleccionado */
stroke-width: 3; opacity: 0.4;

/* Card en panel — nivel badge */
--badge-alto  : background var(--red-pale)   / color var(--red);
--badge-medio : background var(--amber-pale) / color var(--amber);
--badge-bajo  : background var(--green-pale) / color var(--green);

/* Modo oscuro — filtro mapa */
filter: invert(1) hue-rotate(180deg) brightness(0.85) contrast(0.9) saturate(0.6);
```

### Tipografía
```css
font-size: 2rem; font-weight: 700;  /* título página */
font-size: 1rem; font-weight: 700;  /* nombre zona en card */
font-size: 0.875rem;                /* alertas count + porcentaje */
```

### Espaciado
```css
padding: 24px;
gap: 16px;

/* Panel de zonas */
overflow-y: auto;
max-height: calc(100vh - 200px);

/* Mapa */
height: calc(100vh - 200px);
min-height: 500px;
border-radius: var(--dash-radius-card);
```

---

## Componentes Utilizados

| Componente | Variante/Config | Uso |
|------------|----------------|-----|
| `app-card` | — | Cards de zona en panel |
| `app-button` | `primary`, `sm` | "➕ Nueva zona" |
| `app-button` | `secondary`, `sm` | "Seleccionar en mapa" |
| `app-modal` | `zona`, `default` | Modal crear/editar zona |
| `app-modal` | `sm`, `danger` | Confirmar eliminar zona |
| `app-spinner` | `lg`, `fullscreen` | Cargando zonas inicial |
| Mapa Leaflet | tiles Google Maps | Mapa interactivo con círculos |

---

## Responsive

| Breakpoint | Comportamiento |
|------------|---------------|
| Desktop `>1024px` | Layout split: mapa izquierda + panel derecha (360px) |
| Tablet `760–1024px` | Panel encima del mapa (apilado vertical); panel con scroll |
| Mobile `<760px` | Tabs: Mapa / Lista; sin layout split; sidebar colapsado |

---

## Accesibilidad

- Mapa con `aria-label="Mapa de zonas de riesgo"`
- Círculos con `role="button"` y `aria-label="Zona [nombre], nivel [nivel], [n] alertas"`
- Panel de zonas con `role="list"` y cards como `role="listitem"`
- Modal con `role="dialog"`, `aria-modal="true"`, foco atrapado
- Inputs de coordenadas con `aria-describedby` explicando el rango válido
- Contraste mínimo en badges de nivel (texto sobre fondo claro/oscuro)

---

## Integración con Servicios

```typescript
// RiskZonesService — métodos utilizados

RiskZonesService.getZonas(): Observable<ZonaRiesgo[]>
// GET /zones

RiskZonesService.createZona(datos: Omit<ZonaRiesgo, 'id' | 'alertas'>): Observable<ZonaRiesgo>
// POST /zones

RiskZonesService.updateZona(id: number, datos: Partial<ZonaRiesgo>): Observable<ZonaRiesgo>
// PUT /zones/:id

RiskZonesService.deleteZona(id: number): Observable<void>
// DELETE /zones/:id

RiskZonesService.getPuntos(): Observable<PuntoInteres[]>
// GET /zones/puntos  (CAIs, hospitales)

// Modelo de datos
interface ZonaRiesgo {
  id          : number;
  nombre      : string;
  nivel       : 'Alto' | 'Medio' | 'Bajo';
  descripcion : string;
  lat         : number;
  lng         : number;
  radio       : number;        // metros
  alertas     : number;        // count de alertas asociadas
  porcentaje  : number;        // % del total
}

interface PuntoInteres {
  id    : number;
  tipo  : 'CAI' | 'Hospital';
  nombre: string;
  lat   : number;
  lng   : number;
}
```

---

## Casos de Uso

| # | Actor | Acción | Resultado |
|---|-------|--------|-----------|
| 1 | Admin | Navega a `/admin/zone-management` | Mapa con círculos de riesgo + lista en panel |
| 2 | Admin | Click en círculo del mapa | Mapa hace zoom + popup con info de la zona |
| 3 | Admin | Click en card del panel | Mapa vuela a esa zona |
| 4 | Admin | Click "➕ Nueva zona" | Modal de formulario vacío |
| 5 | Admin | Click "Seleccionar en mapa" en modal | Cursor especial; click guarda coordenadas |
| 6 | Admin | Guarda zona nueva | Nuevo círculo aparece en mapa + nueva card en panel |
| 7 | Admin | Click [✏️] en card | Modal pre-cargado con datos de la zona |
| 8 | Admin | Click [🗑️] y confirma | Zona eliminada del mapa y del panel |
