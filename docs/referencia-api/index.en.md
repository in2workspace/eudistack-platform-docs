---
title: API Reference
description: Complete documentation of the EUDIStack API
---

# API Reference

Complete documentation of all endpoints available in the EUDIStack API.

<div class="grid cards" markdown>

-   :material-routes:{ .lg .middle } **Endpoints**

    ---

    Complete listing of all available endpoints

    [:octicons-arrow-right-24: View endpoints](endpoints.md)

-   :material-shield-key:{ .lg .middle } **Authorization**

    ---

    How to authenticate and authorize requests

    [:octicons-arrow-right-24: View authorization](autorizacion.md)

-   :material-code-braces:{ .lg .middle } **Examples**

    ---

    Code examples in different languages

    [:octicons-arrow-right-24: View examples](ejemplos.md)

</div>

## Base URL

=== "Production"

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

## Authentication

All API requests require authentication via Bearer token:

```http
Authorization: Bearer <your-access-token>
```

See [Authorization](autorizacion.md) for details on how to obtain tokens.

## Response Format

All responses follow a standard JSON format:

### Successful Response

```json
{
  "success": true,
  "data": {
    // Response data
  },
  "meta": {
    "request_id": "req_abc123",
    "timestamp": "2024-01-15T10:30:00Z"
  }
}
```

### Error Response

```json
{
  "success": false,
  "error": {
    "code": "INVALID_REQUEST",
    "message": "Error description",
    "details": {}
  },
  "meta": {
    "request_id": "req_abc123",
    "timestamp": "2024-01-15T10:30:00Z"
  }
}
```

## HTTP Status Codes

| Code | Name | Description |
|------|------|-------------|
| `200` | OK | Successful request |
| `201` | Created | Resource created successfully |
| `204` | No Content | Successful request with no content |
| `400` | Bad Request | Malformed or invalid request |
| `401` | Unauthorized | Missing or invalid authentication token |
| `403` | Forbidden | No permission to access the resource |
| `404` | Not Found | Resource not found |
| `409` | Conflict | Conflict with current resource state |
| `422` | Unprocessable Entity | Invalid input data |
| `429` | Too Many Requests | Rate limit exceeded |
| `500` | Internal Server Error | Internal server error |

## Error Codes

| Code | Description |
|------|-------------|
| `INVALID_REQUEST` | Malformed request |
| `INVALID_TOKEN` | Invalid access token |
| `TOKEN_EXPIRED` | Expired access token |
| `INSUFFICIENT_SCOPE` | Token lacks required permissions |
| `RESOURCE_NOT_FOUND` | Resource not found |
| `CREDENTIAL_EXPIRED` | Expired credential |
| `CREDENTIAL_REVOKED` | Revoked credential |
| `INVALID_SIGNATURE` | Invalid cryptographic signature |
| `RATE_LIMIT_EXCEEDED` | Rate limit exceeded |

## Pagination

Endpoints that return lists support pagination:

```http
GET /api/v1/credentials?page=1&per_page=20
```

Paginated response:

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

## Rate Limiting

The API implements rate limiting to protect the service:

| Plan | Limit | Window |
|------|-------|--------|
| Free | 100 requests | 1 minute |
| Pro | 1000 requests | 1 minute |
| Enterprise | Custom | Custom |

Response headers include rate limit information:

```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1705312200
```

## Versioning

The API uses URL versioning. The current version is `v1`:

```
https://api.eudistack.example.com/v1/...
```

!!! info "Semantic Versioning"
    We follow semantic versioning. Breaking changes will result in a new major version.

## Available SDKs

| Language | Package | Installation |
|----------|---------|--------------|
| Python | `eudistack-sdk` | `pip install eudistack-sdk` |
| JavaScript | `@eudistack/sdk` | `npm install @eudistack/sdk` |
| Java | `com.eudistack:sdk` | Maven Central |
| Go | `github.com/eudistack/sdk-go` | `go get` |

## Next Step

[:material-routes: View all endpoints](endpoints.md){ .md-button .md-button--primary }
