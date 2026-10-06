# Arquitectura, vista panorámica — Alerta Mujer

**Qué habla con qué, por dónde y con qué permiso.** Un solo documento para tener el conjunto en la
cabeza antes de abrir cualquier otro.

- **Fecha:** 2026-10-06
- **Alcance:** la forma del sistema y la comunicación entre `backend/Backend-ALERTA-MUJER`,
  `web/Frond-end-Web/alerta-mujer-web`, `movile/Frontend-movil` y `wal-docs`.
- **De dónde sale cada afirmación:** del `docker-compose.yml`, de `application.properties` y de
  `wal-docs/05-architecture`. La documentación manda en la intención; el código manda en lo
  observable.

**Lo que este documento no trae, a propósito:**

| No está aquí | Está en |
|---|---|
| La forma exacta de cada petición y respuesta | [`api-contract.md`](api-contract.md) |
| El modelo de datos tabla por tabla | [`data-model.md`](data-model.md) |
| El porqué de la decisión de microservicios | `wal-docs/05-architecture/decisions/records/ADR-002-architectural-style.md` |
| Requisitos y casos de uso | `wal-docs/04-requirements/` y `wal-docs/01-context/` |

> Si este documento contradice alguna vez al código levantado, gana el código.

---

## 1. Tres clientes, un backend, una base

El sistema tiene **tres puntas** y un corazón:

- **App móvil** (`movile/Frontend-movil`) — React Native 0.81 + Expo 54, arquitectura MVVM por
  feature. Es el canal principal: botón de pánico, contactos, mapa, historial.
- **Web** (`web/Frond-end-Web/alerta-mujer-web`) — Angular 21, SCSS/Tailwind/Bootstrap, Leaflet.
  Landing pública, portal de usuaria y panel administrador.
- **Backend** (`backend/Backend-ALERTA-MUJER/backend-monolith`) — Spring Boot 3.2, Java 17,
  Spring Security + JWT, Spring Data JPA. Puerto **8080**.
- **PostgreSQL 16** — base `alerta_mujer`, esquema cargado por `db/init.sql`, host **5433** vía
  Docker.

Flujo: las dos apps hablan **solo con el backend por HTTP** (`http://localhost:8080` en web; URL
inyectable `EXPO_PUBLIC_API_URL` en móvil), con `Authorization: Bearer <jwt>` puesto por un
interceptor (`jwt.interceptor.ts` en web, `api.ts` de axios en móvil). El backend es el único que
toca la base. La base **nunca** se expone a los clientes.

Servicios externos previstos (solo los llama el backend): **Firebase Cloud Messaging** (push),
proveedor SMS (Twilio) y servicio de email.

---

## 2. La forma objetivo vs. la forma real

Aquí vive la decisión estructural más importante del conjunto, y hay que decirla con honestidad:

- **Objetivo (ADR-002, documentado en `wal-docs`):** cuatro microservicios Spring Boot con
  arquitectura hexagonal — `identity-service` (:3001), `alert-service` (:3002),
  `resource-service` (:3003), `admin-service` (:3004) — detrás de un `gateway` que valida JWT, con
  ownership lógico de tablas por servicio sobre un PostgreSQL compartido.
- **Realidad hoy (código):** un **monolito** (`backend-monolith`, :8080) que implementa todos los
  dominios con la misma silueta interna (`identity`, `alerts`, `devices`, `evidence`, `zones`,
  `resources`, `admin`, `infrastructure`), y los servicios `am-*` existen como esqueleto (solo
  `am-identity-service` tiene código funcional duplicado). El `gateway/` rutea pero en la práctica
  los clientes apuntan al monolito.

Implicación práctica: **todo lo nuevo se documenta contra el contrato del monolito** ([
`api-contract.md`](api-contract.md)), que es lo que web y móvil realmente consumen, y los
contratos OpenAPI de `07-api/contracts/openapi/` son la promesa que deberían cumplir los cuatro
servicios cuando se separen.

---

## 3. Qué cruza cada frontera

| Tramo | Cómo viaja | Quién autoriza | Qué lleva |
|---|---|---|---|
| Móvil → backend | HTTPS/HTTP a `:8080`, axios + interceptor | JWT en cabecera | JSON (alertas, contactos, ubicación…) |
| Web → backend | HTTP a `:8080`, HttpClient + interceptor | JWT en cabecera | JSON |
| Backend → Postgres | TCP 5432 (interno Docker), JPA/Hibernate | Cadena `DATABASE_URL` | SQL |
| Docker host → Postgres | TCP `:5433`, **solo desarrollo** | `POSTGRES_USER/PASSWORD` | psql / herramientas |
| Backend → FCM/SMS/email | HTTPS saliente | Claves en entorno | Push, SMS, correo |

Tres cosas que la lista de endpoints no enseña:

- **El token lo pone un interceptor en cada cliente.** Ninguna pantalla ni componente sabe que
  existe la cabecera; 401/403 se traducen en redirigir a login (web: `role.guard.ts`).
- **`ddl-auto=none`.** El backend **no** crea ni altera el esquema: lo hace `db/init.sql` al crear
  el volumen. Un cambio de entidad sin migración SQL no se manifiesta (y ahí nace la deuda D3 de
  [`data-model.md`](data-model.md)).
- **CORS abierto (`*`) en desarrollo.** Web (`localhost:4200`) y Expo no cruzan origen contra el
  mismo host en producción; el valor está parametrizado por `CORS_ORIGIN`.

---

## 4. La misma silueta a los dos lados del backend

El monolito repite, por dominio, el mismo corte:

```
<dominio>/
  domain/model/          ← entidades JPA (la regla de negocio)
  application/service/   ← casos de uso
  infrastructure/
    controller/          ← adaptador REST
    repository/          ← adaptador JPA
```

No es simetría decorativa: permite levantar el slice `identity` sin tocar `alerts`, probar la
regla de una alerta sin levantar el servidor, y es el paso previo natural a separar
`am-identity-service`, `am-alert-service`, etc. El riesgo a vigilar: que un `new` de
infraestructura aparezca fuera de su capa y rompa la hexagonal en silencio — igual que en el
objetivo de ADR-002.

---

## 5. La regla de dependencia

Dentro de cada dominio la flecha va una sola vez: `controller → service → repository → model`, y el
modelo **no** conoce a Spring Data ni a la base. Es lo que hace comprobable la arquitectura: se
verifica abriendo los paquetes/`pom.xml`, no confiando en la disciplina. En movil se repite la
misma disciplina con MVVM por feature (`view` / `viewModel` / `models`), y en web con
`core/services` + `features/` + `shared/`.

---

## 6. El arranque, que es donde el orden importa

`docker-compose.yml` encadena las dos puertas por condición, no por orden:

1. `postgres` arranca y solo cuando `pg_isready` responde (healthcheck)…
2. …arranca `backend`, que exige `DATABASE_URL` alcanzable y `JWT_SECRET` presente, aplica
   `ddl-auto=none` (no toca el esquema) y escucha en `:8080`.
3. Web (`ng serve`) y móvil (Expo) arrancan aparte y apuntan a `:8080`.

Dos detalles que valen por sí solos:

- **Sin `JWT_SECRET` el backend no debería firmar.** El compose trae un default de desarrollo; en
  cualquier entorno real hay que sobreescribirlo en `.env`, porque un default publicado reparte
  tokens falsificables.
- **`db/init.sql` solo se ejecuta con el volumen vacío.** Recrear el esquema exige borrar el
  volumen `postgres_data`; para evolucionar sin borrar, hace falta el changelog (deuda D3).

Mezclar arranques (levantar el monolito a mano y a la vez el compose) recrea contenedores y quita
puertos publicados sin avisar.

---

## 7. Divergencia encontrada al dibujar esto

`wal-docs/05-architecture/overview.md` §4 describe **cuatro servicios** y ownership de tablas por
servicio; `wal-docs/09-microservices/service-catalog.md` aún habla de `auth-service :3001`; el
código real es un monolito en `:8080` con cuatro esquemas en una sola base. No se corrige a la
fuerza: queda declarado como deuda A5/D4 en [`api-contract.md`](api-contract.md) y
[`data-model.md`](data-model.md), y este documento manda la anatomía real de hoy.

## Correlaciones

- Endpoints reales → [`api-contract.md`](api-contract.md)
- Tablas y reglas → [`data-model.md`](data-model.md)
- Decisiones y patrones → `wal-docs/05-architecture/`
- Cómo levantar todo → raíz de `backend/Backend-ALERTA-MUJER` (`COMO_INICIAR.md`)
