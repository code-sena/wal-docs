# Wireframe: Gestión de Alertas (Admin)

**Ruta:** `/admin/alertas`  
**Componente:** `AlertAdminComponent`  
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
│  📊 Dash │   🔔 Gestión de Alertas                               │
│  👥 Usuar│                                                        │
│  🔔 Alert│   ┌───────────────┐ ┌───────────────┐ ┌────────────┐ │
│  🗺️ Zonas│   │ TOTAL ALERTAS │ │  PENDIENTES   │ │  ATENDIDAS │ │
│  🚨 Emerg│   │               │ │               │ │            │ │
│  📋 Moder│   │     247       │ │      12       │ │    218     │ │
│  📱 Disp.│   │  este mes     │ │   🔴 urgente  │ │  este mes  │ │
│          │   └───────────────┘ └───────────────┘ └────────────┘ │
│          │                                                        │
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │  FILTROS                                        │ │
│          │   │  [ 🔍 Buscar por ubicación, usuario... ]        │ │
│          │   │                                                  │ │
│          │   │  [app-chip: Todas●] [app-chip: Pendiente]       │ │
│          │   │  [app-chip: Atendida] [app-chip: Cancelada]     │ │
│          │   │                                                  │ │
│          │   │  Tipo: [app-chip: SOS] [app-chip: Robo]         │ │
│          │   │        [app-chip: Acoso] [app-chip: Medical]    │ │
│          │   │                            [app-period-badge ▼] │ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │  ALERTAS  (247 registros)                       │ │
│          │   │                                                  │ │
│          │   │  ┌───┬──────┬──────────┬───────────┬──────┬───┐ │ │
│          │   │  │ # │ Tipo │ Usuaria  │ Ubicación │ Hora │Est│ │ │
│          │   │  ├───┼──────┼──────────┼───────────┼──────┼───┤ │ │
│          │   │  │ 1 │🔴SOS │ Ana G.   │ Cra 7 #34 │10:32 │●Pe│ │ │
│          │   │  │ 2 │🟡Robo│ María L. │ Calle 12  │10:28 │●Pe│ │ │
│          │   │  │ 3 │🟣Aco.│ Laura T. │ Parq.Cntl │10:25 │✅At│ │ │
│          │   │  │ 4 │🩷Med.│ Paula M. │ Av. 68    │10:20 │✅At│ │ │
│          │   │  │ 5 │🔴SOS │ Sofía R. │ Cra 15    │10:15 │●Pe│ │ │
│          │   │  └───┴──────┴──────────┴───────────┴──────┴───┘ │ │
│          │   │                                                  │ │
│          │   │  [ < Anterior ]    Pág. 1 / 50  [ Siguiente > ] │ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
└──────────┴──────────────────────────────────────────────────────┘
```

---

## Layout ASCII — Modal Detalle de Alerta

```
                  ┌──────────────────────────────────────┐
                  │  🔔 Alerta #1 — SOS                  │  [✕]
                  │                                       │
                  │  Usuaria:   Ana García                │
                  │  Teléfono:  +57 300 123 4567          │
                  │  Tipo:      🔴 SOS                    │
                  │  Estado:    ● Pendiente               │
                  │  Fecha:     Hoy, 10:32 am             │
                  │                                       │
                  │  📍 Ubicación                         │
                  │  Cra 7 #34-12, Bogotá                 │
                  │  Lat: 4.7110  Lng: -74.0721           │
                  │  [ MINI MAPA ]                        │
                  │                                       │
                  │  📝 Descripción / Notas               │
                  │  [ Área de texto para notas admin ]  │
                  │                                       │
                  │  Cambiar estado:                      │
                  │  ○ Pendiente  ● Atendida  ○ Cancelada │
                  │                                       │
                  │  [ CANCELAR ]  [ 💾 GUARDAR CAMBIOS ] │
                  └──────────────────────────────────────┘
```

---

## Campos del Formulario

### Filtros (sin validación, solo lectura)

| Filtro | Tipo | Valores |
|--------|------|---------|
| Búsqueda libre | `text` | Ubicación, nombre de usuaria |
| Estado | `app-chip` | Todas · Pendiente · Atendida · Cancelada |
| Tipo | `app-chip` | SOS · Robo · Acoso · Medical |
| Período | `app-period-badge` | Hoy · Semana · Mes · Personalizado |

### Modal — Actualizar alerta

| Campo | Tipo | Requerido | Validación |
|-------|------|-----------|------------|
| Estado | `radio` | ✅ | `Pendiente` / `Atendida` / `Cancelada` |
| Notas | `textarea` | ❌ | Máx. 500 caracteres |

---

## Validaciones

```typescript
// Modal actualizar alerta
estado: required, enum['Pendiente','Atendida','Cancelada']
notas : optional, maxLength(500)
```

---

## Estados

| Estado | Descripción | UI |
|--------|-------------|-----|
| `cargando` | Obteniendo alertas | Skeleton stat cards + skeleton tabla |
| `listo` | Datos cargados | Stat cards con números + tabla paginada |
| `filtrado` | Chips / búsqueda activos | Tabla actualizada; contador cambia |
| `modal-detalle` | Click en fila | Modal con detalle completo + mini mapa |
| `guardando` | PATCH en progreso | Botón con spinner |
| `urgente` | Alerta SOS sin atender > 5 min | Fila con fondo `var(--red-pale)` + pulsación |
| `error` | Fallo API | Toast rojo |

---

## Flujo

```
/admin/alertas
  ├── onInit → AlertsService.getAll()
  │             → Llena stat cards + tabla paginada
  │
  ├── Búsqueda / Chips / Period-badge
  │     → AlertsService.getAll(filtros)
  │     → Actualiza tabla y contadores
  │
  ├── Click en fila
  │     → Abre modal con detalle completo
  │     → Mini mapa con marcador en lat/lng de la alerta
  │
  ├── Cambiar estado en modal
  │     → AlertsService.updateEstado(id, nuevoEstado)
  │     → Toast éxito → badge actualizado en tabla
  │
  └── Guardar notas
        → AlertsService.update(id, { notas })
        → Toast éxito → modal cierra
```

---

## Estilos Aplicados

### Colores
```css
background: var(--bg-base);

/* Stat cards */
background: var(--bg-card);
border: 1.5px solid var(--border);
border-radius: var(--dash-radius-card);

/* Números de stat cards */
font-size: 2rem; font-weight: 700; color: var(--text-primary);

/* Badges tipo alerta */
--tipo-sos     : background var(--red-pale)    / color var(--red);
--tipo-robo    : background var(--amber-pale)  / color var(--amber);
--tipo-acoso   : background var(--purple-pale) / color var(--purple-deep);
--tipo-medical : background #fce7f3 / color var(--pink);

/* Badges estado */
--estado-pendiente : background var(--amber-pale) / color var(--amber);
--estado-atendida  : background var(--green-pale)  / color var(--green);
--estado-cancelada : background #f3f4f6 / color var(--text-dim);

/* Fila urgente (SOS > 5 min sin atender) */
background: var(--red-pale);
animation: pulse 2s infinite; /* opacidad 1 ↔ 0.7 */

/* Fila hover */
background: var(--bg-card-hover);
cursor: pointer;
```

### Tipografía
```css
font-size: 2rem; font-weight: 700; /* título página */
font-size: 0.875rem; font-weight: 600; /* cabecera tabla */
font-size: 0.875rem; /* celdas */
```

### Espaciado
```css
/* Stat cards — grid 3 columnas */
display: grid; grid-template-columns: repeat(3, 1fr); gap: 16px;
padding: 24px;
```

---

## Componentes Utilizados

| Componente | Variante/Config | Uso |
|------------|----------------|-----|
| `app-card` | — | Stat cards, filtros, tabla |
| `app-chip` | `active: boolean` | Filtros estado y tipo |
| `app-period-badge` | `conDropdown: true` | Período temporal |
| `app-modal` | `lg`, `default` | Modal detalle + edición de estado |
| `app-spinner` | `sm`, `inline` | Guardando estado |

---

## Responsive

| Breakpoint | Comportamiento |
|------------|---------------|
| Desktop `>1024px` | Stat cards en 3 columnas; tabla completa |
| Tablet `760–1024px` | Stat cards en 2 columnas; tabla compacta |
| Mobile `<760px` | Stat cards apiladas; tabla con scroll horizontal |

---

## Accesibilidad

- Stat cards con `role="status"` y `aria-live="polite"` para actualizaciones
- Tabla con `<caption>` "Lista de alertas del sistema"
- Filas urgentes con `aria-label="Alerta urgente sin atender"` + indicador visual
- Modal con `role="dialog"`, `aria-modal="true"`, foco atrapado
- Textarea de notas con `aria-label` y contador de caracteres restantes
- Chips con `role="checkbox"` y `aria-checked`

---

## Integración con Servicios

```typescript
// AlertsService — métodos utilizados

AlertsService.getAll(): Observable<Alerta[]>
// GET /alertas

AlertsService.update(id: number, datos: Partial<Alerta>): Observable<Alerta>
// PUT /alertas/:id

AlertsService.updateEstado(id: number, estado: string): Observable<Alerta>
// PATCH /alertas/:id/estado

// Modelo de datos
interface Alerta {
  id         : number;
  nombre     : string;
  descripcion: string;
  tipo       : 'SOS' | 'Robo' | 'Acoso' | 'Medical';
  tiempo     : string;
  ubicacion  : string;
  lat        : number;
  lng        : number;
  estado     : 'Pendiente' | 'Atendida' | 'Cancelada';
  usuarioId  : number;
  notas      : string | null;
}
```

---

## Casos de Uso

| # | Actor | Acción | Resultado |
|---|-------|--------|-----------|
| 1 | Admin | Navega a `/admin/alertas` | Ve stat cards + tabla paginada con todas las alertas |
| 2 | Admin | Filtra por "Pendiente" + chip "SOS" | Solo alertas SOS pendientes |
| 3 | Admin | Click en fila de alerta | Modal con detalle completo + mini mapa |
| 4 | Admin | Cambia estado a "Atendida" + escribe notas | Badge actualizado en tabla; toast éxito |
| 5 | Admin | Alerta SOS lleva > 5 min sin atender | Fila pulsante en rojo para llamar atención |
| 6 | Admin | Cambia período a "Esta semana" | Stat cards y tabla actualizan datos |
