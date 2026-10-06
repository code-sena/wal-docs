# 07 — Contratos de API

> **Qué es esto.** La especificación formal de cómo se comunican entre sí las piezas del backend y
> con el exterior. En Alerta Mujer el contrato vigente de cara a web y móvil es el del monolito en
> `:8080` (ver [`../api-contract.md`](../api-contract.md)); este directorio guarda los contratos
> OpenAPI por servicio para cuando el monolito se separe en los cuatro servicios de ADR-002.

## Por qué los contratos son críticos en microservicios

En un monolito, cambiar una función es fácil: el compilador dice qué rompes. En microservicios,
cambiar una API puede romper otros servicios en producción sin que nadie avise.

**Desarrollo contract-first:** se diseña la API antes de implementarla. Así web, móvil y los
servicios trabajan en paralelo con un contrato acordado.

---

## Qué hay aquí y cómo llenarlo

### `contracts/openapi/api-gateway.yaml` ✅ REAL (parcial)
Punto único de entrada previsto: valida JWT, enruta y aplica rate limiting/CORS. Hoy los clientes
apuntan directo al monolito; este contrato es la promesa del gateway.

### `contracts/openapi/auth-service.yaml` ✅ REAL (parcial)
El contrato del servicio de identidad (registro, login, renovación de sesión). Nota de divergencia:
el código actual expone estos endpoints bajo `/api/auth/**` en el monolito y en
`am-identity-service` (:3001), que es el único servicio con código funcional.

### `contracts/openapi/_shared.yaml`
Esquemas y componentes reutilizables entre contratos (paginación, errores, seguridad).

### `contracts/openapi/_template-service.yaml`
Plantilla para añadir el contrato de un nuevo servicio. Duplícala como `service-name.yaml`.

### Faltan (deuda)
| Servicio | Puerto | Bounded contexts | Estado |
|---|---|---|---|
| identity-service | 3001 | Identity and Accounts | Código parcial; falta `identity-service.yaml` propio |
| alert-service | 3002 | Alert Management, contactos, evidencia, notificaciones | Esqueleto → falta `alert-service.yaml` |
| resource-service | 3003 | Zonas y recursos de ayuda | Esqueleto → falta `resource-service.yaml` |
| admin-service | 3004 | Administración y moderación | Esqueleto → falta `admin-service.yaml` |

### Estructura mínima de cada contrato

```yaml
openapi: 3.0.3
info:
  title: [Nombre del servicio] API
  version: 1.0.0
servers:
  - url: http://localhost:3001
paths:
  /recurso:
    get:
      summary: Listar recursos
      responses:
        '200': { description: OK }
components:
  securitySchemes:
    bearerAuth: { type: http, scheme: bearer, bearerFormat: JWT }
security:
  - bearerAuth: []
```

---

## Correlación con el resto del proyecto

| Este directorio | Se alimenta de | Alimenta a |
|---|---|---|
| `contracts/openapi/` | `../api-contract.md`, `wal-docs/04-requirements/` | `09-microservices/services/` (implementación) |
| `_shared.yaml` | `../api-contract.md` §1.2 (errores) | Todos los contratos |
| `api-gateway.yaml` | `wal-docs/05-architecture/overview.md` | Clientes web y móvil |

## Preguntas que esta sección debe responder

- ¿Cómo se llama cada endpoint y qué hace? → `../api-contract.md`
- ¿Qué datos recibe y devuelve? → esquemas en cada `*.yaml`
- ¿Cómo se autentica el cliente? → `bearerAuth` JWT, 24 h
- ¿Qué errores devuelve? → `_shared.yaml` y `api-contract.md` §1.2
- ¿Cómo versiono sin romper consumidores? → prefijo `/api/v1` al publicar la v1 formal (pendiente)
