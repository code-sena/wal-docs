# 02 - Dominio del problema

> **¿Qué es esto?** El modelo mental del negocio. No es tecnología, es comprensión.
> el problema que resuelve el sistema antes de escribir el código. Esta sección proviene del diseño basado en dominios (DDD).

## Por qué existe esta sección

Los errores más costosos en el software no son errores: son malentendidos en el dominio.
Cuando los desarrolladores no comprenden profundamente el negocio:
- Crean abstracciones incorrectas que deben reescribirse.
- Los nombres en el código no coinciden con los nombres de la empresa → confusión permanente
- Los límites de los microservicios están trazados incorrectamente.

Esta sección captura el conocimiento del dominio **antes** de diseñar la arquitectura.

---

## Conceptos clave que debes conocer

**Entidad:** Objeto de dominio con una identidad única (por ejemplo: un `Estudiante` identificado por su código).

**Objeto de valor:** Objeto sin identidad propia, definido por sus atributos (p. ej.: `Dirección`, `Precio`).

**Agregado:** Grupo de entidades tratadas como una unidad. Sólo la raíz agregada
Se puede hacer referencia desde el exterior.

**Evento de dominio:** Algo que ocurrió en el negocio y que otras partes del sistema deben conocer.
(por ejemplo: "Estudiante matriculado", "Pago aprobado"). Son hechos, expresados ​​en tiempo pasado.

**Contexto acotado:** Área del sistema donde se aplica un modelo particular.
Cada microservicio generalmente corresponde a un contexto acotado.

---

## ¿Qué hay aquí y cómo completarlo?

### `dominio-mapa.md` ⭐
Mapa de todos los contextos acotados y cómo se relacionan.
**Rellenar:** dibuja los contextos como rectángulos y las relaciones entre ellos
(upstream/downstream, kernel compartido, capa anticorrupción).

**Formato:**
```rebaja
## Contextos acotados

### [Nombre del contexto]
**Responsabilidad:** [lo que gestiona este contexto]
**Entidades principales:** [lista]
**Equipo propietario:** [equipo]

## Mapa de relaciones
[Diagrama ASCII o descripción de cómo se relacionan los contextos]

| Contexto A | Relación | Contexto B | Descripción |
|-----------|-------------|-----------|-------------|
| [Un] | aguas abajo de | [B] | [A] consume eventos de [B] |
```

### `entidades-y-reglas.md` ⭐
Catálogo de entidades, objetos de valor y reglas de negocio.
**Rellene:** para cada entidad: nombre, atributos, invariantes (reglas que SIEMPRE DEBEN cumplirse),
comportamientos.

**Formato:**
```rebaja
## Entidad: [Nombre de la entidad]
**Pertenece a:** [Contexto acotado]
**Identificador:** [campo que lo hace único]

### Atributos
| Atributo | Tipo | Descripción | Requerido | Reglas |
|-----------|------|-------------|-----------------|-------|

### Reglas de negocio (invariantes)
- [ ] [Una regla que siempre debe cumplirse]

### Comportamientos (métodos de dominio)
- `[nombre()]`: [qué hace]
```

### `dominio-eventos.md` ⭐
Lista de todos los eventos que ocurren en el dominio.
**Rellene:** nombre del evento (tiempo pasado), qué lo desencadena, qué datos transporta, quién lo consume.

**Formato:**
```rebaja
| Evento | Activado por | Datos | Consumidores | Contexto acotado |
|-------|-------------|--------------|-----------|----------------|
| [NombreEnTiempoPasado] | [acción que lo provoca] | [campos de eventos] | [qué escucha esto] | [contexto] |
```

---

## Correlaciones con otras secciones

| Esta sección alimenta... | Por qué |
|-----------------------|-----|
| `05-arquitectura/overview.md` | Contextos acotados → microservicios |
| `06-datos/modelos.md` | Entidades → tablas/colecciones de datos |
| `07-api/contratos/` | Eventos de dominio → eventos en API asíncronas |
| `09-microservicios/event-catalog.md` | Todos los eventos del dominio quedan registrados allí |
| `04-requisitos/historias-de-usuario.md` | Reglas comerciales → criterios de aceptación |

---

## Herramienta recomendada: tormenta de eventos

**Event Storming** es un taller de descubrimiento de dominios con notas adhesivas:
1. 🟠 Naranja: Eventos de dominio (tiempo pasado)
2. 🔵 Azul: Comandos (lo que desencadena el evento)
3. 🟡 Amarillo: Actores (quien ejecuta el comando)
4. 🟣 Púrpura: Políticas (reacciones automáticas)
5. 🟦 Azul claro: Sistemas externos

Realizar una sesión de Event Storming con el equipo antes de completar esta sección ahorra semanas de rediseño.

---

## Preguntas que esta sección debe responder

- ¿Cuáles son las principales entidades comerciales?
- ¿Qué reglas NUNCA se pueden violar en el sistema?
- ¿Qué acontecimientos importantes ocurren en el dominio?
- ¿Dónde están los límites naturales del sistema (para definir microservicios)?