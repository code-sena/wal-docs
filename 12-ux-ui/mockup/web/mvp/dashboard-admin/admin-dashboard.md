# Panel Administrador

**Ruta:** `/admin/dashboard`
**Acceso:** ADMIN
**Estado de implementación:** Implementada — `AdminDashboardComponent` con lazy loading; consume `AlertsService.getAll()`, `UsersService.getAll()` y `RiskZonesService.getZonas()`; polling de 30s para alertas activas.

## Estilo base — design tokens
```css
/* Fondo de página */
background: var(--bg-base); /* #f5f4ff */

/* Cards */
background: var(--bg-card);          /* #ffffff */
border: 1.5px solid var(--border);   /* #c4b5fd */
border-radius: var(--dash-radius-card); /* 12px */
box-shadow: var(--dash-shadow-card);
/* 0 0 0 1.5px var(--border), 0 4px 16px rgba(124,58,237,0.10) */

/* Stat card — número grande */
font-size: 2rem; font-weight: 700; color: var(--text-primary); /* #1e1a3c */

/* Stat card — label */
font-size: 0.875rem; color: var(--text-muted); /* #6b7280 */

/* Stat card "Alertas activas" — acento urgente */
color: var(--red); /* #dc2626 */

/* Badge alerta SOS — urgente */
background: var(--red-pale); color: var(--red);

/* Badge alerta Pendiente */
background: var(--amber-pale); color: var(--amber); /* #d97706 */

/* Badge alerta Atendida */
background: var(--green-pale); color: var(--green); /* #16a34a */

/* Fila urgente (SOS > 5 min sin atender) */
background: var(--red-pale);
animation: pulse 2s infinite; /* opacidad 1 ↔ 0.7 */

/* Fila hover */
background: var(--bg-card-hover); /* #f0eeff */ cursor: pointer;

/* Zona Alto */
fill: #ef4444; stroke: #dc2626; opacity: 0.25;

/* Zona Medio */
fill: #f59e0b; stroke: #d97706; opacity: 0.25;

/* Zona Bajo */
fill: #22c55e; stroke: #16a34a; opacity: 0.25;

/* Modo oscuro — filtro mapa */
filter: invert(1) hue-rotate(180deg) brightness(0.85) contrast(0.9) saturate(0.6);

/* Stat cards grid */
display: grid; grid-template-columns: repeat(4, 1fr); gap: 16px;

/* Título página */
font-size: 2rem; font-weight: 700; color: var(--text-primary);

/* Página padding */
padding: 24px; gap: 24px;
```

## Objetivo
Ofrecer al administrador una vista centralizada del estado del sistema en tiempo real: alertas activas, métricas de usuarios, zonas de riesgo en mapa y accesos rápidos a todas las secciones de gestión.

## Estructura (de arriba a abajo)
1. **AdminLayout:** Sidebar izquierdo (Dashboard, Usuarios, Alertas, Zonas, Emergencias, Moderadores, Dispositivos) + Topbar (logo, campana 🔔, toggle tema, avatar admin)
2. Título: "📊 Panel de Administración" + saludo + fecha
3. **Grid 4 stat cards:**
   - 🔔 Alertas activas (número en rojo si > 0)
   - 👥 Usuarios totales
   - 🗺️ Zonas de riesgo
   - 📱 Dispositivos conectados
4. **Card Alertas recientes (tabla paginada 5 filas):**
   - Columnas: # | Tipo | Usuaria | Ubicación | Hora | Estado
   - Filas SOS sin atender > 5 min con animación `pulse` en `var(--red-pale)`
   - Enlace "Ver todas →" → `/admin/alertas`
5. **Card Mapa — Zonas de riesgo:**
   - Mapa Leaflet con círculos coloreados (Alto/Medio/Bajo)
   - Marcadores de alertas activas en tiempo real
   - Leyenda de colores
6. **Grid 2×3 accesos rápidos:**
   - 👥 Usuarios · 🔔 Alertas · 🗺️ Zonas · 🚨 Emergencias · 📋 Moderadores · 📱 Dispositivos

## Componentes usados
- `app-card` — stat cards, tabla alertas, mapa, accesos rápidos
- `app-chip` con `dotColor` — badges de estado en tabla
- `app-button` variant `secondary`, size `sm` — accesos rápidos
- `app-spinner` size `md` — estado cargando
- Mapa Leaflet con tiles Google Maps — zonas y alertas en tiempo real

## Estados
- **cargando:** Skeleton en stat cards + spinner en mapa + skeleton en tabla
- **listo:** Todos los datos cargados y visualizados
- **alerta-urgente:** Fila pulsante roja para SOS > 5 min sin atender
- **polling-activo:** Tabla de alertas se refresca cada 30s sin recargar la página
- **sin-alertas-activas:** Stat card muestra 0 en verde + mensaje "Sin alertas pendientes"
- **error:** Banner rojo por sección + botón reintentar individual

## Restricciones
- **Mobile < 760px:** Grid stat cards 2×2; tabla simplificada; mapa 240px; accesos rápidos 2×3; sidebar → BottomNav
- **Tablet 760–1024px:** Grid stat cards 2×2; mapa 280px; sidebar colapsado (solo íconos)
- **Desktop > 1024px:** Grid 4 columnas stat cards; sidebar expandido; mapa + tabla en grid 2 columnas
- **WCAG AA:** Stat cards con `role="status"` y `aria-live="polite"`; tabla con `<caption>`; filas urgentes con `aria-label="Alerta urgente sin atender"`; mapa con `aria-label`; contraste texto sobre fondo rojo-pálido ≥ 4.5:1
- **Modo oscuro:** Tokens dark activos; filtro invertido en mapa

## Prompt listo para Google Stitch
> Diseña el Panel de Administración de AlertaMujer Web en Angular. Plataforma web, layout AdminLayout con sidebar izquierdo (Dashboard, Usuarios, Alertas, Zonas, Emergencias, Moderadores, Dispositivos) en fondo blanco `border-right: 1.5px solid #c4b5fd` y topbar con logo, campana, toggle tema y avatar. Fondo de página `#f5f4ff`. La pantalla contiene: título "📊 Panel de Administración" en `2rem 700 #1e1a3c`; grid de 4 stat cards blancas `border: 1.5px solid #c4b5fd` `border-radius 12px` mostrando Alertas activas (número en `#dc2626` si > 0), Usuarios totales, Zonas de riesgo y Dispositivos conectados, con número grande `2rem 700` y label `0.875rem #6b7280`; card con tabla de 5 alertas recientes con columnas # | Tipo | Usuaria | Ubicación | Hora | Estado, badges de estado (Pendiente amber `#d97706`, Atendida verde `#16a34a`), filas SOS urgentes con fondo `#fee2e2` y animación `pulse 2s`, y enlace "Ver todas →"; card con mapa Leaflet mostrando círculos de zonas de riesgo (Alto `#ef4444 0.25`, Medio `#f59e0b 0.25`, Bajo `#22c55e 0.25`) y marcadores de alertas activas; grid 2×3 de accesos rápidos a las 6 secciones admin. En mobile el grid de stat cards es 2×2 y el sidebar colapsa. Cumple WCAG AA.
