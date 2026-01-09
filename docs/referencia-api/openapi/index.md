---
title: Especificaciones OpenAPI
description: Especificaciones OpenAPI de todas las APIs de EUDIStack
---

# Especificaciones OpenAPI

Esta seccion contiene las especificaciones OpenAPI 3.1 completas de todas las APIs de EUDIStack. Estas especificaciones pueden importarse directamente en herramientas como Swagger UI, Postman, o usarse para generar clientes SDK.

## APIs Disponibles

<div class="grid cards" markdown>

-   :material-certificate:{ .lg .middle } **Issuer API**

    ---

    API para la emision de credenciales verificables siguiendo el estandar OpenID4VCI.

    [:octicons-arrow-right-24: Ver especificacion](issuer-api.yaml)

    **Endpoints principales:**

    - `/.well-known/openid-credential-issuer` - Metadatos del emisor
    - `/oid4vci/v1/credential` - Emision de credenciales
    - `/oauth/token` - Obtencion de tokens
    - `/backoffice/v1/*` - Administracion

-   :material-wallet:{ .lg .middle } **Wallet API**

    ---

    API para la gestion de credenciales verificables en el wallet del usuario.

    [:octicons-arrow-right-24: Ver especificacion](wallet-api.yaml)

    **Endpoints principales:**

    - `/api/v1/credentials` - Gestion de credenciales
    - `/api/v1/vp` - Presentaciones verificables
    - `/api/v1/execute-content` - Procesamiento QR
    - `/api/v1/ebsi-did` - DID EBSI

-   :material-shield-check:{ .lg .middle } **Verifier API**

    ---

    API para la verificacion de credenciales siguiendo el estandar OpenID4VP.

    [:octicons-arrow-right-24: Ver especificacion](verifier-api.yaml)

    **Endpoints principales:**

    - `/oidc/did/{id}` - Resolucion de DIDs
    - `/oid4vp/auth-request/{id}` - Solicitudes de autorizacion
    - `/oid4vp/auth-response` - Respuestas de autorizacion
    - `/login` - Login con QR

</div>

## Uso de las Especificaciones

### Swagger UI

Puedes visualizar cualquier especificacion usando Swagger UI online:

```
https://petstore.swagger.io/?url=https://raw.githubusercontent.com/in2workspace/eudistack-docs/main/docs/referencia-api/openapi/issuer-api.yaml
```

### Postman

1. Abre Postman
2. Click en **Import**
3. Selecciona **Link** y pega la URL del archivo YAML
4. La coleccion se importara automaticamente

### Generacion de Clientes SDK

Usando OpenAPI Generator:

=== "JavaScript/TypeScript"

    ```bash
    openapi-generator-cli generate \
      -i issuer-api.yaml \
      -g typescript-axios \
      -o ./sdk/issuer
    ```

=== "Python"

    ```bash
    openapi-generator-cli generate \
      -i issuer-api.yaml \
      -g python \
      -o ./sdk/issuer
    ```

=== "Java"

    ```bash
    openapi-generator-cli generate \
      -i issuer-api.yaml \
      -g java \
      -o ./sdk/issuer
    ```

=== "Go"

    ```bash
    openapi-generator-cli generate \
      -i issuer-api.yaml \
      -g go \
      -o ./sdk/issuer
    ```

## Resumen de Endpoints por API

### Issuer API (v2.2.6)

| Categoria | Endpoints |
|-----------|-----------|
| **Metadata** | 2 endpoints (`/.well-known/*`) |
| **OID4VCI** | 7 endpoints (credential offer, token, credential) |
| **Backoffice** | 12 endpoints (procedures, issuances, revocation) |
| **Signatures** | 6 endpoints (configuration, cloud providers) |
| **Operations** | 2 endpoints (audit, cloud providers) |

### Wallet API (v2.0.1)

| Categoria | Endpoints |
|-----------|-----------|
| **Credentials** | 3 endpoints (list, delete, request signed) |
| **Presentations** | 2 endpoints (VP, VP CBOR) |
| **QR Processing** | 1 endpoint (execute content) |
| **EBSI** | 1 endpoint (get DID) |
| **OpenID4VCI** | 1 endpoint (credential offer) |

### Verifier API (v2.0.6)

| Categoria | Endpoints |
|-----------|-----------|
| **DID Resolver** | 1 endpoint (resolve DID to JWK) |
| **OID4VP** | 2 endpoints (auth request, auth response) |
| **Frontend** | 2 endpoints (login, error page) |

## Versionado

Las APIs siguen versionado semantico. Los cambios incompatibles resultan en una nueva version mayor del path (ej. `/v1` -> `/v2`).

| API | Version Actual | Path Base |
|-----|----------------|-----------|
| Issuer | 2.2.6 | `/oid4vci/v1`, `/backoffice/v1` |
| Wallet | 2.0.1 | `/api/v1` |
| Verifier | 2.0.6 | `/oid4vp`, `/oidc` |
