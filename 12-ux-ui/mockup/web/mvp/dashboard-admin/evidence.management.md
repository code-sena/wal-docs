# Gestión de Evidencias (Admin)

**Ruta:** `/admin/evidence-management`
**Acceso:** ADMIN
**Estado de implementación:** Implementada con lazy loading — `EvidencesManagementComponent` registrado; consume `EvidenceService` (getAll, update, updateEstado, delete); vista de todas las evidencias del sistema con capacidad de moderación.

## Estilo base — design tokens
```css
/* Fondo de página */
background: var(--bg-base); /* #f5f4ff */

/* Cards */
background: var(--bg-card);
border: 1.5px solid var(--border);   /* #c4b5fd */
border-radius: var(--dash-radius-card); /* 12px */
box-shadow: var(--dash-shadow-card);

/* Stat card — número */
font-size: 2rem; font-weight: 700; color: var(--text-primary);

/* Ícono tipo Video */
background: var(--purple-pale); color: var(--purple-deep); /* #ede9fe / #7c3aed */

/* Ícono tipo Audio */
background: var(--green-pale); color: var(--green); /* #dcfce7 / #16a34a */

/* Ícono tipo Foto */
background: var(--amber-pale); color: var(--amber); /* #fef3c7 / #d97706 */

/* Badge estado Activa */
background: var(--green-pale); color: var(--green);

/* Badge estado Archivada */
background: #f3f4f6; color: var(--text-dim); /* #9ca3af */

/* Badge estado Eliminada */
background: var(--red-pale); color: var(--red);

/* Chip filtro activo */
background: var(--purple-pale); border: 1.5px solid var(--purple-deep);
color: var(--purple-deep);

/* Fila hover */
background: var(--bg-card-hover); cursor: pointer;

/* Botón ver/reproducir */
color: var(--purple-deep);

/* Botón descargar */
color: var(--green);

/* Botón archivar */
color: var(--amber);

/* Botón eliminar */
color: var(--red);

/* Modal danger */
/* encabezado rojo, botón confirmar rojo */

/* Stat cards grid */
display: grid; grid-template-columns: repeat(3, 1fr); gap: 16px;

/* Título página */
font-size: 2rem; font-weight: 700; color: var(--text-primary);

/* Cabecera tabla */
font-size: 0.875rem; font-weight: 600; color: var(--text-muted);

/* Página padding */
padding: 24px; gap: 16px;
```

## Objetivo
Permitir al administrador visualizar, moderar (archivar) y eliminar todas las evidencias del sistema generadas por alertas SOS, con acceso a reproductor/visor y filtrado por tipo, estado y usuaria.

## Estructura (de arriba a abajo)
1. AdminLayout: Sidebar + Topbar
2. Título: "🎬 Gestión de Evidencias"
3. **Grid 3 stat cards:** Total evidencias | Videos | Fotos + Audios
4. **Card Filtros:**
   - Input 🔍 "Buscar por usuaria, alerta..."
   - Chips tipo: Todas● / Video / Audio / Foto
   - Chips estado: Activa / Archivada / Eliminada
   - `app-period-badge` dropdown
5. **Card Evidencias (N archivos):**
   - Tabla paginada: # | Tipo | Usuaria | Alerta | Tamaño | Fecha | Estado | Acciones
   - Acciones por fila: [▶️/🔍 Ver] [⬇️ Descargar] [📦 Archivar] [🗑️ Eliminar]
   - Paginación
6. **Modal Ver Evidencia:**
   - Reproductor de video / visor de imagen
   - Metadatos: tipo, usuaria, alerta asociada, tamaño, fecha
   - Botón descargar
7. **Modal Confirmar Eliminar (danger)**

## Componentes usados
- `app-card` — stat cards, filtros, tabla
- `app-chip` con `active: boolean` — filtros tipo y estado
- `app-period-badge` con `conDropdown: true` — período
- `app-modal` size `lg`, default — reproductor/visor
- `app-modal` size `sm`, danger — confirmar eliminar
- `app-button` variant `danger`, size `sm` — eliminar en modal
- `app-spinner` size `sm`, inline — estados loading

## Estados
- **cargando:** Skeleton stat cards + tabla
- **listo:** Stat cards + tabla paginada
- **filtrado:** Tabla actualizada por tipo, estado o período
- **visualizando:** Modal con reproductor/visor abierto
- **archivando:** Badge cambia a "Archivada"; fila se actualiza
- **eliminando:** Modal danger activo
- **sin-datos:** "No hay evidencias registradas en el sistema"
- **error:** Toast rojo

## Restricciones
- **Mobile < 760px:** Stat cards apiladas; tabla scroll horizontal; sidebar colapsado
- **Tablet 760–1024px:** Stat cards 2 cols; tabla sin columna tamaño
- **Desktop > 1024px:** Stat cards 3 cols; tabla completa
- **WCAG AA:** Stat cards con `role="status"`; tabla con `<caption>`; botones de acción con `aria-label` descriptivo; modal reproductor con `aria-label`; modal danger con `aria-describedby`; chips con `role="checkbox"` y `aria-checked`; contraste WCAG AA
- **Modo oscuro:** Tokens dark activos

## Prompt listo para Google Stitch
> Diseña la pantalla Gestión de Evidencias de AlertaMujer Web en Angular para el rol Admin. Plataforma web, layout AdminLayout con sidebar y topbar. Fondo `#f5f4ff`. La pantalla tiene título "🎬 Gestión de Evidencias" en `2rem 700 #1e1a3c`, grid de 3 stat cards blancas `border: 1.5px solid #c4b5fd` con Total evidencias, Videos y Fotos+Audios en `2rem 700`; card de filtros con input 🔍, chips de tipo (Todas, Video, Audio, Foto) y de estado (Activa, Archivada, Eliminada) activos en `#ede9fe`/`#7c3aed`, y `app-period-badge`; card con tabla paginada de columnas # | Tipo (ícono con fondo coloreado: Video `#ede9fe`/`#7c3aed`, Audio `#dcfce7`/`#16a34a`, Foto `#fef3c7`/`#d97706`) | Usuaria | Alerta | Tamaño | Fecha | Estado (Activa verde, Archivada gris, Eliminada rojo) | Acciones con botones ▶️/🔍 en `#7c3aed`, ⬇️ en `#16a34a`, 📦 en `#d97706` y 🗑️ en `#dc2626`, fila con hover `#f0eeff` y paginación. Al hacer click en ▶️/🔍 se abre modal grande con reproductor/visor y metadatos. Al hacer click en 🗑️ se abre modal danger de confirmación. Cumple WCAG AA.
