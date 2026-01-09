---
title: Referencia API
description: Documentacion completa de la API de EUDIStack
---

# Referencia API

Documentacion completa de todos los endpoints disponibles en la API de EUDIStack.

<div class="grid cards" markdown>

-   :material-api:{ .lg .middle } **Especificaciones OpenAPI**

    ---

    Especificaciones OpenAPI 3.1 completas de todas las APIs

    [:octicons-arrow-right-24: Ver especificaciones](openapi/index.md)

-   :material-routes:{ .lg .middle } **Endpoints**

    ---

    Listado completo de todos los endpoints disponibles

    [:octicons-arrow-right-24: Ver endpoints](endpoints.md)

-   :material-shield-key:{ .lg .middle } **Autorizacion**

    ---

    Como autenticarte y autorizar peticiones

    [:octicons-arrow-right-24: Ver autorizacion](autorizacion.md)

-   :material-code-braces:{ .lg .middle } **Ejemplos**

    ---

    Ejemplos de codigo en diferentes lenguajes

    [:octicons-arrow-right-24: Ver ejemplos](ejemplos.md)

</div>

## APIs Disponibles

EUDIStack se compone de 3 APIs principales:

| API | Descripcion | Version |
|-----|-------------|---------|
| **Issuer API** | Emision de credenciales verificables (OpenID4VCI) | 2.2.6 |
| **Wallet API** | Gestion de credenciales en el wallet | 2.0.1 |
| **Verifier API** | Verificacion de credenciales (OpenID4VP) | 2.0.6 |

## Base URL

=== "Produccion"

    ```
    https://api.eudistack.example.com/v1
    ```

=== "Sandbox"

    ```
    https://sandbox.api.eudistack.example.com/v1
    ```

=== "Local"

    ```
    http://localhost:8080/api/v1
    ```

## Autenticacion

Todas las peticiones a la API requieren autenticacion mediante Bearer token:

```http
Authorization: Bearer <your-access-token>
```

Ver [Autorizacion](autorizacion.md) para mas detalles sobre como obtener tokens.

## Formato de respuestas

Todas las respuestas siguen un formato JSON estandar:

### Respuesta exitosa

```json
{
  "success": true,
  "data": {
    // Datos de la respuesta
  },
  "meta": {
    "request_id": "req_abc123",
    "timestamp": "2024-01-15T10:30:00Z"
  }
}
```

### Respuesta con error

```json
{
  "success": false,
  "error": {
    "code": "INVALID_REQUEST",
    "message": "Descripcion del error",
    "details": {}
  },
  "meta": {
    "request_id": "req_abc123",
    "timestamp": "2024-01-15T10:30:00Z"
  }
}
```

## Codigos de estado HTTP

| Codigo | Nombre | Descripcion |
|--------|--------|-------------|
| `200` | OK | Peticion exitosa |
| `201` | Created | Recurso creado correctamente |
| `204` | No Content | Peticion exitosa sin contenido |
| `400` | Bad Request | Peticion malformada o invalida |
| `401` | Unauthorized | Token de autenticacion faltante o invalido |
| `403` | Forbidden | Sin permisos para acceder al recurso |
| `404` | Not Found | Recurso no encontrado |
| `409` | Conflict | Conflicto con el estado actual del recurso |
| `422` | Unprocessable Entity | Datos de entrada invalidos |
| `429` | Too Many Requests | Limite de peticiones excedido |
| `500` | Internal Server Error | Error interno del servidor |

## Codigos de error

| Codigo | Descripcion |
|--------|-------------|
| `INVALID_REQUEST` | Peticion malformada |
| `INVALID_TOKEN` | Token de acceso invalido |
| `TOKEN_EXPIRED` | Token de acceso expirado |
| `INSUFFICIENT_SCOPE` | Token sin los permisos necesarios |
| `RESOURCE_NOT_FOUND` | Recurso no encontrado |
| `CREDENTIAL_EXPIRED` | Credencial expirada |
| `CREDENTIAL_REVOKED` | Credencial revocada |
| `INVALID_SIGNATURE` | Firma criptografica invalida |
| `RATE_LIMIT_EXCEEDED` | Limite de peticiones excedido |

## Paginacion

Los endpoints que devuelven listas soportan paginacion:

```http
GET /api/v1/credentials?page=1&per_page=20
```

Respuesta paginada:

```json
{
  "success": true,
  "data": [...],
  "pagination": {
    "page": 1,
    "per_page": 20,
    "total_items": 150,
    "total_pages": 8
  }
}
```

## Rate limiting

La API implementa rate limiting para proteger el servicio:

| Plan | Limite | Ventana |
|------|--------|---------|
| Free | 100 peticiones | 1 minuto |
| Pro | 1000 peticiones | 1 minuto |
| Enterprise | Personalizado | Personalizado |

Los headers de respuesta incluyen informacion sobre el rate limit:

```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1705312200
```

## Versionado

La API usa versionado en la URL. La version actual es `v1`:

```
https://api.eudistack.example.com/v1/...
```

!!! info "Versionado semantico"
    Seguimos versionado semantico. Los cambios incompatibles resultaran en una nueva version mayor.

## SDKs disponibles

| Lenguaje | Paquete | Instalacion |
|----------|---------|-------------|
| Python | `eudistack-sdk` | `pip install eudistack-sdk` |
| JavaScript | `@eudistack/sdk` | `npm install @eudistack/sdk` |
| Java | `com.eudistack:sdk` | Maven Central |
| Go | `github.com/eudistack/sdk-go` | `go get` |

## Siguiente paso

[:material-routes: Ver todos los endpoints](endpoints.md){ .md-button .md-button--primary }
