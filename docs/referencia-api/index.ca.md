---
title: Referencia API
description: Documentacio completa de l'API d'EUDIStack
---

# Referencia API

Documentacio completa de tots els endpoints disponibles a l'API d'EUDIStack.

<div class="grid cards" markdown>

-   :material-routes:{ .lg .middle } **Endpoints**

    ---

    Llistat complet de tots els endpoints disponibles

    [:octicons-arrow-right-24: Veure endpoints](endpoints.md)

-   :material-shield-key:{ .lg .middle } **Autoritzacio**

    ---

    Com autenticar-te i autoritzar peticions

    [:octicons-arrow-right-24: Veure autoritzacio](autorizacion.md)

-   :material-code-braces:{ .lg .middle } **Exemples**

    ---

    Exemples de codi en diferents llenguatges

    [:octicons-arrow-right-24: Veure exemples](ejemplos.md)

</div>

## Base URL

=== "Produccio"

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

## Autenticacio

Totes les peticions a l'API requereixen autenticacio mitjancant Bearer token:

```http
Authorization: Bearer <your-access-token>
```

Veure [Autoritzacio](autorizacion.md) per mes detalls sobre com obtenir tokens.

## Format de respostes

Totes les respostes segueixen un format JSON estandard:

### Resposta exitosa

```json
{
  "success": true,
  "data": {
    // Dades de la resposta
  },
  "meta": {
    "request_id": "req_abc123",
    "timestamp": "2024-01-15T10:30:00Z"
  }
}
```

### Resposta amb error

```json
{
  "success": false,
  "error": {
    "code": "INVALID_REQUEST",
    "message": "Descripcio de l'error",
    "details": {}
  },
  "meta": {
    "request_id": "req_abc123",
    "timestamp": "2024-01-15T10:30:00Z"
  }
}
```

## Codis d'estat HTTP

| Codi | Nom | Descripcio |
|------|-----|------------|
| `200` | OK | Peticio exitosa |
| `201` | Created | Recurs creat correctament |
| `204` | No Content | Peticio exitosa sense contingut |
| `400` | Bad Request | Peticio malformada o invalida |
| `401` | Unauthorized | Token d'autenticacio faltant o invalid |
| `403` | Forbidden | Sense permisos per accedir al recurs |
| `404` | Not Found | Recurs no trobat |
| `409` | Conflict | Conflicte amb l'estat actual del recurs |
| `422` | Unprocessable Entity | Dades d'entrada invalides |
| `429` | Too Many Requests | Limit de peticions excedit |
| `500` | Internal Server Error | Error intern del servidor |

## Codis d'error

| Codi | Descripcio |
|------|------------|
| `INVALID_REQUEST` | Peticio malformada |
| `INVALID_TOKEN` | Token d'acces invalid |
| `TOKEN_EXPIRED` | Token d'acces expirat |
| `INSUFFICIENT_SCOPE` | Token sense els permisos necessaris |
| `RESOURCE_NOT_FOUND` | Recurs no trobat |
| `CREDENTIAL_EXPIRED` | Credencial expirada |
| `CREDENTIAL_REVOKED` | Credencial revocada |
| `INVALID_SIGNATURE` | Signatura criptografica invalida |
| `RATE_LIMIT_EXCEEDED` | Limit de peticions excedit |

## Paginacio

Els endpoints que retornen llistes suporten paginacio:

```http
GET /api/v1/credentials?page=1&per_page=20
```

Resposta paginada:

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

L'API implementa rate limiting per protegir el servei:

| Pla | Limit | Finestra |
|-----|-------|----------|
| Free | 100 peticions | 1 minut |
| Pro | 1000 peticions | 1 minut |
| Enterprise | Personalitzat | Personalitzat |

Els headers de resposta inclouen informacio sobre el rate limit:

```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1705312200
```

## Versionat

L'API usa versionat a la URL. La versio actual es `v1`:

```
https://api.eudistack.example.com/v1/...
```

!!! info "Versionat semantic"
    Seguim versionat semantic. Els canvis incompatibles resultaran en una nova versio major.

## SDKs disponibles

| Llenguatge | Paquet | Installacio |
|------------|--------|-------------|
| Python | `eudistack-sdk` | `pip install eudistack-sdk` |
| JavaScript | `@eudistack/sdk` | `npm install @eudistack/sdk` |
| Java | `com.eudistack:sdk` | Maven Central |
| Go | `github.com/eudistack/sdk-go` | `go get` |

## Seguent pas

[:material-routes: Veure tots els endpoints](endpoints.md){ .md-button .md-button--primary }
