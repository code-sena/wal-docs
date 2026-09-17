# Gestión de Reportes

**Ruta:** `/admin/report-management`
**Acceso:** ADMIN
**Estado de implementación:** Implementada con lazy loading — `ReportManagementComponent` registrado; consume `ReportService` (generate, download); interfaz de generación y descarga de reportes en distintos formatos.

## Estilo base — design tokens
```css
/* Fondo de página */
background: var(--bg-base); /* #f5f4ff */

/* Cards */
background: var(--bg-card);
border: 1.5px solid var(--border);   /* #c4b5fd */
border-radius: var(--dash-radius-card); /* 12px */
box-shadow: var(--dash-shadow-card);

/* Tarjeta de tipo de reporte */
background: var(--bg-card);
border: 1.5px solid var(--border);
border-radius: var(--dash-radius-card);

/* Hover tarjeta reporte */
background: var(--bg-card-hover); /* #f0eeff */
border-color: var(--purple-deep); /* #7c3aed */
transform: translateY(-2px);

/* Ícono tipo reporte — Alertas */
background: var(--red-pale); color: var(--red);

/* Ícono tipo reporte — Usuarios */
background: var(--purple-pale); color: var(--purple-deep);

/* Ícono tipo reporte — Zonas */
background: var(--green-pale); color: var(--green);

/* Ícono tipo reporte — Dispositivos */
background: var(--amber-pale); color: var(--amber);

/* Badge formato PDF */
background: #fee2e2; color: #dc2626;

/* Badge formato Excel */
background: #dcfce7; color: #16a34a;

/* Badge formato CSV */
background: #fef3c7; color: #d97706;

/* Botón descargar */
color: var(--green); /* #16a34a */

/* Chip filtro período activo */
background: var(--purple-pale); border: 1.5px solid var(--purple-deep);

/* Título página */
font-size: 2rem; font-weight: 700; color: var(--text-primary);

/* Nombre tipo reporte */
font-size: 1rem; font-weight: 700; color: var(--text-primary);

/* Descripción */
font-size: 0.875rem; color: var(--text-muted);

/* Grid tipos de reporte */
display: grid; grid-template-columns: repeat(2, 1fr); gap: 16px; padding: 24px;
```

## Objetivo
Permitir al administrador generar y descargar reportes del sistema (alertas, usuarios, zonas, dispositivos) en distintos formatos (PDF, Excel, CSV) con filtros de período y rango de fechas.

## Estructura (de arriba a abajo)
1. AdminLayout: Sidebar + Topbar
2. Título: "📋 Gestión de Reportes"
3. Card "Generar Reporte":
   - Selector de período: `app-period-badge` + inputs de fecha personalizada
   - Grid 2×2 de tarjetas de tipo de reporte:
     - 🔔 Reporte de Alertas (filtros: estado, tipo, período)
     - 👥 Reporte de Usuarios (filtros: estado, rol, período de registro)
     - 🗺️ Reporte de Zonas (filtros: nivel de riesgo)
     - 📱 Reporte de Dispositivos (filtros: estado de conexión)
   - Cada tarjeta: ícono coloreado + nombre + descripción + badges de formato disponible (PDF/Excel/CSV) + botón "⬇️ GENERAR Y DESCARGAR"
4. Card "Historial de reportes generados":
   - Tabla: Tipo | Período | Formato | Fecha generación | Acciones [⬇️]
   - Paginación

## Componentes usados
- `app-card` — card de generación e historial
- `app-period-badge` con `conDropdown: true` — selector de período global
- `app-button` variant `primary`, size `sm` — "Generar y descargar" en cada tarjeta
- `app-spinner` size `sm`, inline — generando reporte (puede tardar)
- `app-chip` — badges de formato (PDF/Excel/CSV)

## Estados
- **listo:** Grid de tarjetas de reporte + historial vacío o con registros
- **generando:** Botón con spinner + texto "Generando..." + barra de progreso indeterminada
- **descargando:** Toast "Descarga iniciada" en verde
- **sin-historial:** "No hay reportes generados aún" + ícono 📋
- **error-generacion:** Toast rojo "Error al generar el reporte. Intenta de nuevo."
- **error-descarga:** Toast rojo "No se pudo descargar el archivo."

## Restricciones
- **Mobile < 760px:** Grid 1 columna; sidebar colapsado
- **Tablet 760–1024px:** Grid 2 columnas; tabla historial compacta
- **Desktop > 1024px:** Grid 2×2; tabla historial completa
- **WCAG AA:** Botones con `aria-label` descriptivo ("Generar reporte de alertas en PDF"); spinner con `aria-label="Generando reporte"`; historial tabla con `<caption>`; badges de formato con texto visible; toasts con `role="status"` / `role="alert"`
- **Modo oscuro:** Tokens dark activos

## Prompt listo para Google Stitch
> Diseña la pantalla Gestión de Reportes de AlertaMujer Web en Angular para el rol Admin. Plataforma web, layout AdminLayout con sidebar y topbar. Fondo `#f5f4ff`. La pantalla tiene título "📋 Gestión de Reportes" en `2rem 700 #1e1a3c` y dos cards blancas con `border: 1.5px solid #c4b5fd` y `border-radius 12px`: (1) Card "Generar Reporte" con `app-period-badge` en el header y un grid 2×2 de tarjetas de tipo de reporte, cada una con `border-radius 12px`, hover `#f0eeff` y borde `#7c3aed`, ícono en fondo coloreado (🔔 Alertas en `#fee2e2`/`#dc2626`, 👥 Usuarios en `#ede9fe`/`#7c3aed`, 🗺️ Zonas en `#dcfce7`/`#16a34a`, 📱 Dispositivos en `#fef3c7`/`#d97706`), nombre en `1rem 700`, descripción en `0.875rem #6b7280`, badges de formato disponible (PDF en rojo, Excel en verde, CSV en amber) y botón "⬇️ GENERAR Y DESCARGAR" con estado loading con spinner; (2) Card "Historial de reportes" con tabla de columnas Tipo | Período | Formato | Fecha | Descargar, con botón ⬇️ en `#16a34a` por fila y paginación. Estado vacío muestra "No hay reportes generados aún". En mobile el grid colapsa a 1 columna. Cumple WCAG AA.
