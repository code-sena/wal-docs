# Sistema de Diseño - AlertaMujer

## 🎨 Design Tokens

### Paleta de Colores - Móvil (React Native)

#### Tema LIGHT (Por defecto - Morado/Lila)
```typescript
// Colores Principales
headerBackground: rgb(202,171,222)      // #CAABDD
headerText: #1a1a1a
background: #f5f0fa
containerBackground: #ffffff
text: #1a1a1a
card: #ffffff
tabBackground: rgb(202,171,222)
tabActiveColor: #45046b
tabInactiveColor: rgb(255, 255, 255)
icono: #9b009b
headercolor1: #c4a8d6
headercolor2: #7b1db2

// Colores de Contactos
contactCardBg: #ffffff
contactCardBorder: #e0d0f0
contactNombre: #2a0045
contactSubtext: #7a6090
contactAccent: #7B1FA2
contactBadgeBg: #F3E5F5
contactBadgeText: #6A1B9A
contactBadgeBorder: #CE93D8
contactAvatarBorder: #AB47BC
contactDivider: #EDE7F6
contactIconCallBg: #F3E5F5
contactIconCallColor: #7B1FA2
contactIconWaBg: #E8F5E9
contactIconLocBg: #E8EAF6
contactIconLocColor: #3949AB
contactBotonEditar: #F3E5F5
contactBotonEditarIcon: #7B1FA2
contactSugerenciaBg: #FAF7FF
contactSugerenciaBorder: #CE93D8
contactSugerenciaIcon: #AB47BC
contactBotonAgregarBg: #7B1FA2
contactFlotanteBg: #7B1FA2
contactCousserBg: #F3E5F5
contactCousserIcon: #7B1FA2
contactBuscadorBg: #F3EEF8
contactBuscadorText: #2a0045

// Gradientes de Asistencia
asistenciaEmergenciaGradiente: ["#c4a8d6", "#7b1db2"]
asistenciaViolenciaGradiente: ["rgb(240, 87, 87)", "rgb(132, 0, 255)"]
asistenciaMentalGradiente: ["rgb(142, 189, 255)", "rgb(255, 0, 191)"]
asistenciaIconoUserBg: "purple"
asistenciaIconoBombilloBg: "rgba(205, 117, 230, 0.29)"
asistenciaIconoBombilloColor: "#1E1228"
asistenciaIconoEstrellaBg: "purple"
```

#### Tema DARK
```typescript
headerBackground: #000000
headerText: #fefeff
background: #3d3d42
containerBackground: #1e1e22
text: #f0e6ff
card: #2e2e38
tabBackground: #000000
tabActiveColor: #9718ff
tabInactiveColor: rgba(230, 217, 247, 0.89)
icono: #c084fc
headercolor1: #030303
headercolor2: #200138

// Contactos Dark
contactCardBg: #28282f
contactCardBorder: #3d3d50
contactNombre: #e8d5ff
contactSubtext: #9e8fbb
contactAccent: #c084fc
contactBadgeBg: #3a2d55
contactBadgeText: #d8b4fe
contactBadgeBorder: #6d28d9
contactAvatarBorder: #a855f7
contactDivider: #3a3a48
contactIconCallBg: #2d2040
contactIconCallColor: #c084fc
contactIconWaBg: #1a2e22
contactIconLocBg: #1a233a
contactIconLocColor: #60a5fa
contactBotonEditar: #2d2040
contactBotonEditarIcon: #c084fc
contactSugerenciaBg: #22202e
contactSugerenciaBorder: #5b21b6
contactSugerenciaIcon: #a78bfa
contactBotonAgregarBg: #7c3aed
contactFlotanteBg: #7c3aed
contactCousserBg: #2d2040
contactCousserIcon: #c084fc
contactBuscadorBg: #232330
contactBuscadorText: #e8d5ff
```

#### Tema ROSA
```typescript
headerBackground: #cf3389
background: #fce4f0
containerBackground: #fffbfe
text: #3a0020
card: #fff0f7
tabBackground: #cf3389
icono: #eb0a86
headercolor1: #cf3389
headercolor2: #860e52
```

#### Tema VINO
```typescript
headerBackground: #770736
background: #fff5f5
containerBackground: #ffffff
text: #2a0000
card: #fff0f0
tabBackground: #770736
icono: #770736
headercolor1: #770736
headercolor2: #4a0020
```

#### Tema AZUL
```typescript
headerBackground: #0b013b
background: #f0f0ff
containerBackground: #ffffff
text: #0b013b
card: #eef2ff
tabBackground: #0b013b
icono: #1565C0
headercolor1: #0b013b
headercolor2: #1a237e
```

#### Tema MAGENTA
```typescript
headerBackground: #490449
background: #fdf0fd
containerBackground: #ffffff
text: #2a002a
card: #fce8fc
tabBackground: #490449
icono: #6a0572
headercolor1: #490449
headercolor2: #860386
```

### Paleta de Colores - Web (Angular)

#### Modo Claro - :root
```css
/* Púrpuras */
--color-purple-dark   : #c205b8;
--color-purple        : #c900ae;
--color-purple-mid    : #c52dca;
--color-purple-light  : #b502bb;

/* Rosas / Pinks */
--color-pink          : #a70ac7;
--color-pink-hot      : #ae10df;
--color-pink-light    : #410166;

/* Textos */
--color-text-dark     : #2d1457;
--color-text-mid      : #000000;
--color-text-muted    : #570ea0;

/* Superficies */
--color-white         : #680ca5;
--color-card-bg       : rgba(142, 10, 160, 0.918);
--color-input-bg      : #5509ac;

/* Bordes */
--color-border        : rgba(124, 58, 237, 0.322);
--color-border-focus  : var(--color-purple-light);

/* Gradientes */
--gradient-bg  : linear-gradient(135deg, #db079c 0%, #af0763 35%, #9a07a7 70%, #430261 100%);
--gradient-btn : linear-gradient(90deg, #650dc9 0%, #54048a 50%, #5c048f 100%);

/* Panel Admin / Dashboard */
--bg-base       : #f5f4ff;
--bg-card       : #ffffff;
--bg-card-hover : #f0eeff;
--border        : #c4b5fd;
--text-primary  : #1e1a3c;
--text-muted    : #6b7280;
--text-dim      : #9ca3af;

--purple-deep       : #7c3aed;
--purple-mid        : #a78bfa;
--purple-light      : #c4b5fd;
--purple-pale       : #ede9fe;
--purple-pale-hover : #e0d8fc;
--pink              : #ec4899;

/* Colores Semánticos */
--green         : #16a34a;
--green-pale    : #dcfce7;
--red           : #dc2626;
--red-light     : #f3a5a5;
--red-pale      : #fee2e2;
--red-pale-hover: #fdc9c9;
--amber         : #d97706;
--amber-pale    : #fef3c7;
```

#### Modo Oscuro - [data-theme="dark"]
```css
--bg-base       : #14101f;
--bg-card       : #1e1833;
--bg-card-hover : #241d3d;
--border        : #4c3a80;
--text-primary  : #ede9fe;
--text-muted    : #a8a0c4;
--text-dim      : #766e94;

--purple-deep       : #9d6ff0;
--purple-mid        : #b79bf5;
--purple-light      : #6d4fae;
--purple-pale       : #2b204a;
--purple-pale-hover : #342659;
--pink              : #ff5fa8;

--green         : #4ade80;
--green-pale    : #113322;
--red           : #f87171;
--red-light     : #c65c5c;
--red-pale      : #3a1414;
--red-pale-hover: #4a1a1a;
--amber         : #fbbf24;
--amber-pale    : #3a2a0a;
```

### Colores Semánticos Universales

| Propósito | Móvil | Web (Claro) | Web (Oscuro) |
|-----------|-------|-------------|--------------|
| Éxito | #27ae60 | #16a34a | #4ade80 |
| Error | #e74c3c | #dc2626 | #f87171 |
| Advertencia | rgb(255, 189, 65) | #d97706 | #fbbf24 |
| Info | #7B2CBF | #3b82f6 | #60a5fa |
| Primario | #7B1FA2 | #7c3aed | #9d6ff0 |
| Secundario | #3949AB | #a78bfa | #b79bf5 |

## 📝 Sistema de Tipografía

### Móvil (React Native)

**Fuentes por plataforma:**
- Android: Roboto (sistema)

| Uso | Tamaño | Peso | Línea | Letter Spacing |
|-----|--------|------|-------|---------------|
| Títulos de card | 24px | 700 | 32px | -0.5px |
| Subtítulos | 14-16px | 400-600 | 18-22px | 0px |
| Texto cuerpo | 13-14px | 400-500 | 20-22px | 0px |
| Texto pequeño | 10-12px | 500-700 | 14-16px | 0.2px |
| Botones primarios | 16px | 700 | 20px | 0.5px |
| Botones secundarios | 16px | 600 | 20px | 0.5px |
| Input text | 16px | 400 | 20px | 0px |
| Header títulos | 22-24px | bold | 28px | -0.5px |
| Tab labels | 11px | 700 | 14px | 0.5px |
| Números grandes | 32-42px | bold | 48px | -1px |

### Web (Angular)

**Fuente base:** Nunito, sans-serif

| Uso | Tamaño | Peso | Línea | Letter Spacing |
|-----|--------|------|-------|---------------|
| H1 Landing | 2.5rem | 800 | 3rem | -0.02em |
| H2 Secciones | 2rem | 700 | 2.5rem | -0.01em |
| H3 Cards | 1.5rem | 600 | 2rem | 0px |
| Body text | 1rem | 400 | 1.6rem | 0px |
| Small text | 0.875rem | 400 | 1.4rem | 0px |
| Buttons | 0.9rem | 600 | 1.2rem | 0.5px |
| Inputs | 0.9rem | 400 | 1.4rem | 0px |
| Nav links | 0.85rem | 500 | 1.2rem | 0.5px |
| Tagline | 0.62rem | 700 | 1rem | 0.1em |

## 📐 Sistema de Espaciado y Grid

### Móvil (React Native)

**Base:** Sistema de 4px

| Propósito | Valor |
|-----------|-------|
| Espacio mínimo | 4px |
| Espacio pequeño | 8px |
| Espacio medio | 12-16px |
| Espacio grande | 20-24px |
| Espacio extra grande | 30-40px |
| Padding de cards | 20-24px |
| Margin horizontal | 16-20px |
| Gap entre elementos | 8-12px |


### Web (Angular)

**Base:** Sistema de 4px (Tailwind)

| Propósito | Valor | Tailwind |
|-----------|-------|---------|
| Espacio mínimo | 4px | space-1 |
| Espacio pequeño | 8px | space-2 |
| Espacio medio | 16px | space-4 |
| Espacio grande | 24px | space-6 |
| Espacio extra grande | 32px | space-8 |
| Padding cards | 24px | p-6 |
| Margin cards | 16px | m-4 |
| Gap form rows | 14px | gap-3.5 |
| Margin top forms | 28px | mt-7 |

**Breakpoints:**
- Mobile: < 760px
- Tablet: 760px - 1024px
- Desktop: > 1024px

## 🔲 Border Radius

### Móvil
| Elemento | Radio |
|----------|-------|
| Botones primarios | 12-16px |
| Botones grandes | 25-30px |
| Cards estándar | 20-24px |
| Cards grandes | 28px |
| Inputs | 20px |
| Modales | 30px |
| Avatar | 35-40px |
| Iconos circulares | 10-15px |
| Badges | 12-20px |
| Botón SOS | 50px (circular) |

### Web
| Elemento | Variable CSS | Valor |
|----------|--------------|-------|
| Auth cards | --radius-card | 28px |
| Inputs | --radius-input | 14px |
| Buttons | --radius-btn | 50px (pill) |
| Checkboxes | --radius-checkbox | 5px |
| Dashboard cards | --dash-radius-card | 12px |
| Small elements | --dash-radius-sm | 6px |

## 🌑 Sombras

### Móvil
```typescript
// Cards
shadowColor: COLORS.purpleDark o "#000"
shadowOffset: { width: 0, height: 8 }
shadowOpacity: 0.15
shadowRadius: 24
elevation: 10

// Botones
shadowColor: COLORS.purpleDark
shadowOffset: { width: 0, height: 4 }
shadowOpacity: 0.3
shadowRadius: 8
elevation: 6

// Tabs
shadowColor: "#7B2CBF"
shadowOffset: { width: 0, height: -3 }
shadowOpacity: 0.15
shadowRadius: 8
elevation: 10

// Floating button
shadowColor: "#000"
shadowOffset: { width: 0, height: 4 }
shadowOpacity: 0.4
shadowRadius: 8
elevation: 8
```

### Web
```css
/* Auth cards */
--shadow-card       : 0 24px 60px rgba(78, 16, 177, 0.18), 0 4px 16px rgba(91, 33, 182, 0.10);
--shadow-btn        : 0 8px 24px rgba(233, 30, 140, 0.38);
--shadow-btn-hover  : 0 12px 32px rgba(233, 30, 140, 0.48);
--shadow-card-badge : 0 12px 36px rgba(80, 20, 180, 0.22);

/* Dashboard cards */
--dash-shadow-card : 0 0 0 1.5px var(--border), 0 4px 16px rgba(124, 58, 237, 0.10);
```

## 🧩 Componentes Base

### Botones

#### Móvil - Botón Primario (Aceptar)
**Archivo:** `src/components/ui/button/aceptar.tsx`

**Props:**
- `title: string` - Texto del botón
- `onPress: () => void` - Función al presionar
- `color?: string` - Color personalizado (default: "#331753")

**Estados:**
- Normal: opacidad 1
- Pressed: opacidad 0.85, scale 0.92
- Disabled: opacidad 0.5

**Estilos:**
```typescript
width: "90%"
padding: 15
borderRadius: 12
backgroundColor: customizable
textColor: "#fff"
fontWeight: "bold"
fontSize: 16
```

#### Móvil - Botón Secundario (Cancelar)
**Archivo:** `src/components/ui/button/cancelar.tsx`

**Props:**
- `title: string` - Texto del botón
- `onPress: () => void` - Función al presionar
- `bgColor?: string` - Color de fondo (default: "#ffffff")
- `textColor?: string` - Color de texto (default: "#000000")

**Estilos:**
```typescript
width: "90%"
padding: 16
borderRadius: 12
borderWidth: 1
borderColor: "#ffffff"
```

#### Web - Button Component
**Archivo:** `app/shared/components/button/button.scss`

**Variantes:**
- `primary`: Gradiente púrpura/rosa con sombra
- `secondary`: Transparente con borde púrpura
- `danger`: Transparente con borde rojo

**Tamaños:**
- `sm`: 8px 18px padding, 13px font
- `md`: 12px 28px padding, 15px font
- `lg`: 15px 36px padding, 17px font

**Estados:**
- `disabled`: opacity 0.6, cursor not-allowed
- `loading`: muestra spinner componente
- `hover`: transform translateY(-1px), sombra aumentada
- `active`: transform translateY(0)

### Inputs

#### Móvil - TextInput

**Estados:**
```typescript
// Normal
backgroundColor: theme.contactBuscadorBg
borderColor: theme.contactCardBorder

// Focus
borderColor: theme.contactAccent
shadowColor: theme.contactAccent

// Error
borderColor: #e74c3c
backgroundColor: "#FFF5F5"
```

**Validaciones:**
- Error inline debajo del input
- Icono de error visible
- Mensaje descriptivo

#### Web - Input Fields

**Estados:**
```css
/* Normal */
background: rgba(245, 233, 233, 0.9)
border: 2px solid rgba(236, 72, 153, 0.15)

/* Focus */
background: var(--bg-card)
border: 2px solid #db1879
box-shadow: 0 0 0 4px rgba(236, 72, 153, 0.12)

/* Error */
border-color: var(--red)
error-message visible
```

### Cards

#### Móvil - Card Base
**Archivo:** `src/components/ui/card/card.tsx`

**Props:**
- `title?: string` - Título de la card
- `titleHighlight?: string` - Texto destacado
- `children?: React.ReactNode` - Contenido
- `style?: any` - Estilos adicionales
- `gradientColors?: readonly [string, string, ...string[]]` - Colores del gradiente

**Estilos:**
```typescript
marginHorizontal: 14
marginTop: 24
marginBottom: 20
borderRadius: 28
shadowColor: COLORS.purpleDark
shadowOffset: { width: 0, height: 8 }
shadowOpacity: 0.15
shadowRadius: 24
elevation: 10
borderWidth: 1
borderColor: COLORS.purpleLight
padding: 24
```

#### Web - Card Component
**Archivo:** `app/shared/components/card/card.scss`

**Estilos:**
```css
background: var(--bg-card)
border: 1.5px solid var(--border)
radius: var(--dash-radius-card)
shadow: var(--dash-shadow-card)
hover: background var(--bg-card-hover)
```

### Modales

#### Móvil - Modal de Confirmación
**Archivo:** `src/components/ui/modalConfirmacion/confirmacion.tsx`

**Props:**
- `visible: boolean` - Visibilidad
- `departamento: string` - Departamento a confirmar
- `municipio: string` - Municipio a confirmar
- `onConfirmar: () => void` - Callback confirmar
- `onRegresar: () => void` - Callback regresar
- `soloConfirmar: boolean` - Mostrar solo botón confirmar

**Estilos:**
```typescript
overlay: backgroundColor 'rgba(0,0,0,0.6)'
modalCard: 
  width: '85%'
  backgroundColor: '#FFF'
  borderRadius: 30
  padding: 25
  elevation: 10
iconCircle: 70x70px, borderRadius 35, backgroundColor '#F3E5F5'
btnConfirmar: height 55, backgroundColor '#471e63', borderRadius 15
btnRegresar: height 55, backgroundColor transparent, borderWidth 2
```

#### Web - Modal Component
**Archivo:** `app/shared/components/modal/modal.scss`

**Tamaños:**
- `sm`: Modal pequeño
- `md`: Modal mediano (default)
- `lg`: Modal grande
- `zona`: Modal especial para zonas

**Variantes:**
- `default`: Estilo normal
- `danger`: Estilo para acciones destructivas

**Comportamiento:**
- `cerrarAlClickFuera`: Cierra al hacer click fuera
- Evento `cerrar`: Emite cuando se cierra

### Badges y Chips

#### Móvil - Chips de Sugerencia
**Uso:** Sugerencias de nombre de ubicación

**Estilos:**
```typescript
backgroundColor: COLORS.white
borderWidth: 1.5
borderColor: COLORS.accent
borderRadius: 20
paddingVertical: 8
paddingHorizontal: 14
```

#### Web - Chip Component
**Archivo:** `app/shared/components/chip/chip.scss`

**Props:**
```typescript
label: string
active: boolean
dotColor?: string   // Color del punto opcional
count?: number     // Contador opcional
```

**Uso:** Filtros y etiquetas interactivas con estado activo/inactivo

### Navigation Bars

#### Móvil - Tabs de Navegación
**Archivo:** `src/components/ui/Tabs/TabsNavegationStyle.ts`

**Estilos:**
```typescript
tabBarLabelStyle: {
  fontSize: 11,
  fontWeight: "700",
  letterSpacing: 0.5,
  marginBottom: 6,
  marginTop: 2,
}
tabBarStyle: {
  height: 70,
  paddingBottom: 12,
  paddingTop: 4,
  elevation: 10,
  shadowColor: "#7B2CBF",
  shadowOffset: { width: 0, height: -3 },
  shadowOpacity: 0.15,
  shadowRadius: 8,
}
```

#### Web - Dashboard Navbar
**Archivo:** `app/shared/components/dashboard-navbar/dashboard-navbar.scss`

**Componentes:**
- Logo y branding
- Perfil de usuario
- Switch de tema
- Notificaciones
- Menú de logout

## ♿ Accesibilidad y Reglas UX

### Contraste de Color (WCAG AA)

**Estándares implementados:**
- Contraste automático de texto (getContrastText en móvil)
- Texto normal: mínimo 4.5:1
- Texto grande: mínimo 3:1
- Componentes UI: mínimo 3:1

**Verificación:**
- Todos los textos primarios cumplen WCAG AA
- Colores semánticos con contraste suficiente
- Modo oscuro con contraste ajustado

### Targets Táctiles Mínimos

**Móvil:**
- Botones: mínimo 44x44dp
- Botón SOS: 68% del ancho de pantalla
- Tabs: altura 70px
- Iconos circulares: mínimo 48x48dp

**Web:**
- Botones: mínimo 44px de altura
- Links en navegación: padding 12px
- Inputs: altura mínima 48px
- Touch targets en mobile: 48x48px

### Feedback Háptico

**Móvil:**
- Long press en botón SOS: vibración intensa
- Activación de alerta: patrón de vibración
- Confirmación de acción: vibración corta
- Error: vibración larga

**Implementación:**
```typescript
import { Vibration } from 'expo-haptics';

// Alerta activada
Vibration.vibrate([100, 50, 100]);

// Error
Vibration.vibrate(500);

// Confirmación
Vibration.vibrate(50);
```

### Mecanismos de Alerta Discreta

**Móvil:**
1. **Triple tap**: Tocar pantalla 3 veces rápido
2. **Botón físico**: Mantener presionado botón volumen 3 seg
3. **Activación por Shake**: Sacudir dispositivo vigorosamente

**Confirmación:**
- Cuenta regresiva de 3-5 segundos
- Vibración intensa
- Sonido de alarma
- Opción de cancelar

### Patrones de Recuperación de Error

**Inputs:**
- Validación en tiempo real (onChange)
- Error inline debajo del campo
- Icono de error visible
- Mensaje descriptivo del error
- Sugerencia de corrección

**Acciones destructivas:**
- Modal de confirmación antes de eliminar
- Botón de cancelar con icono rojo (#E53935)
- Texto claro de advertencia
- Acción reversible cuando es posible
- Historial de acciones

**Conexión de red:**
- Indicador de estado de conexión
- Reintento automático (exponential backoff)
- Mensaje de error específico
- Acción manual de reintentar
- Caché de datos offline

## ⏱️ Timings de Feedback

### Móvil
| Acción | Duración | Easing |
|--------|---------|--------|
| Botón press | 100ms | ease-out |
| Glow animation | 1600-2500ms | quad out |
| Tab button | 100ms cada dirección | ease-in-out |
| Navegación slide | 300ms | ease-in-out |
| Modal fade | 250ms | ease-out |
| Carga | Indefinido (ActivityIndicator) | - |

### Web
| Acción | Duración | Easing |
|--------|---------|--------|
| Hover states | 0.2s | ease |
| Transiciones generales | 0.3s | ease |
| Modales | 0.5s | ease |
| Transformaciones GPU | 0.2s | ease |
| Scroll suave | 0.5s | ease-in-out |

## 🌓 Modo Oscuro

### Móvil
**Implementación:**
- 6 modos de tema: light, dark, rosa, vino, azul, magenta
- Transición suave entre modos
- Todos los componentes adaptados
- Contraste automático de texto

**Cambios principales:**
- Fondos más oscuros (#3d3d42, #1e1e22)
- Textos más claros (#f0e6ff, #e8d5ff)
- Bordes más visibles (#3d3d50)
- Sombras aumentadas para contraste

### Web
**Implementación:**
- Activado mediante atributo `data-theme="dark"` en html
- Transición suave entre modos
- Todos los componentes adaptados

**Cambios principales:**
- Fondos más oscuros (#14101f, #1e1833)
- Textos más claros (#ede9fe, #a8a0c4)
- Bordes más visibles (#4c3a80)
- Sombras aumentadas para contraste
- Mapas con filtro invertido para modo oscuro

## 📱 Responsive Design

### Móvil
**Valores relativos:**
- Botón SOS: 68% del ancho de pantalla
- Títulos: width * 0.038
- Padding: basado en insets de safe area

**Orientación:**
- Portrait: Layout principal
- Landscape: Adaptación de grid
- Safe area: Insets dinámicos

### Web
**Breakpoints:**
- Mobile: < 760px
- Tablet: 760px - 1024px
- Desktop: > 1024px

**Adaptaciones:**
- Desktop: Layout de 3 columnas
- Tablet: Layout de 2 columnas
- Mobile: Layout de 1 columna

## 🔐 Seguridad y Privacidad

### Manejo de Datos Sensibles
- Contraseñas: toggle visibilidad
- Datos personales: encriptación en tránsito
- Ubicación: permisos explícitos
- Contactos: permisos de lectura
- Cámara/Micrófono: permisos por uso

### Permisos Runtime
**Móvil:**
- GPS: Obligatorio para funcionalidad principal
- SMS: Requerido para alertas
- Cámara: Opcional para evidencia
- Micrófono: Opcional para evidencia
- Contactos: Requerido para notificaciones
- Notificaciones: Requerido para alertas

**Web:**
- Geolocation API: Para ubicación
- Notifications API: Para alertas
- MediaDevices API: Para cámara/micrófono

## 🎯 Estados de Carga

### Móvil
```typescript
// ActivityIndicator
<ActivityIndicator 
  size="large" 
  color={COLORS.accent} 
/>

// Skeleton loading
<SkeletonPlaceholder>
  <SkeletonPlaceholder.Item width={200} height={20} />
  <SkeletonPlaceholder.Item width={150} height={15} />
</SkeletonPlaceholder>
```

### Web
```typescript
// Spinner component
<app-spinner size="md" mode="inline" />

// Skeleton cards
<div class="skeleton-card"></div>
<div class="skeleton-text"></div>
```

## 📊 Métricas de Performance

### Móvil
- Frame rate: 60fps objetivo
- Time to interactive: < 3s
- Bundle size: Optimizado con code splitting
- Imágenes: Lazy loading y compresión

### Web
- First Contentful Paint: < 1.5s
- Time to Interactive: < 3s
- Bundle size: Code splitting por ruta
- Imágenes: WebP y lazy loading

---

Este sistema de diseño combina las mejores prácticas de React Native y Angular para mantener consistencia visual y funcional entre la aplicación móvil y el panel web administrativo.
