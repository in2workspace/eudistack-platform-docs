---
title: Endpoints
description: Listado completo de endpoints de la API de EUDIStack
---

# Endpoints

Documentacion detallada de todos los endpoints disponibles en la API.

## Credenciales

### Listar credenciales

<span class="api-method get">GET</span> `/credentials`

Obtiene una lista de credenciales emitidas.

**Query Parameters**

| Parametro | Tipo | Descripcion |
|-----------|------|-------------|
| `type` | string | Filtrar por tipo de credencial |
| `status` | string | Filtrar por estado (active, revoked, expired) |
| `page` | integer | Numero de pagina (default: 1) |
| `per_page` | integer | Elementos por pagina (default: 20, max: 100) |

**Respuesta exitosa** `200 OK`

```json
{
  "success": true,
  "data": [
    {
      "id": "cred_abc123",
      "type": "VerifiableId",
      "status": "active",
      "issued_at": "2024-01-15T10:00:00Z",
      "expires_at": "2034-01-15T10:00:00Z",
      "subject_did": "did:key:z6Mk..."
    }
  ],
  "pagination": {
    "page": 1,
    "per_page": 20,
    "total_items": 1,
    "total_pages": 1
  }
}
```

---

### Crear oferta de credencial

<span class="api-method post">POST</span> `/credentials/offer`

Crea una oferta de credencial que el usuario puede aceptar en su wallet.

**Request Body**

```json
{
  "credential_type": "VerifiableId",
  "claims": {
    "given_name": "Maria",
    "family_name": "Garcia",
    "birth_date": "1990-05-20"
  },
  "pin_required": false,
  "validity_period_days": 365
}
```

**Respuesta exitosa** `201 Created`

```json
{
  "success": true,
  "data": {
    "offer_id": "offer_xyz789",
    "credential_offer_uri": "openid-credential-offer://?credential_offer=...",
    "qr_code_url": "https://api.example.com/qr/offer_xyz789.png",
    "pin": null,
    "expires_at": "2024-01-15T11:00:00Z"
  }
}
```

---

### Obtener credencial

<span class="api-method get">GET</span> `/credentials/{credential_id}`

Obtiene los detalles de una credencial especifica.

**Path Parameters**

| Parametro | Tipo | Descripcion |
|-----------|------|-------------|
| `credential_id` | string | ID unico de la credencial |

**Respuesta exitosa** `200 OK`

```json
{
  "success": true,
  "data": {
    "id": "cred_abc123",
    "type": "VerifiableId",
    "status": "active",
    "issued_at": "2024-01-15T10:00:00Z",
    "expires_at": "2034-01-15T10:00:00Z",
    "subject_did": "did:key:z6Mk...",
    "claims": {
      "given_name": "Maria",
      "family_name": "Garcia",
      "birth_date": "1990-05-20"
    }
  }
}
```

---

### Revocar credencial

<span class="api-method delete">DELETE</span> `/credentials/{credential_id}`

Revoca una credencial emitida.

**Path Parameters**

| Parametro | Tipo | Descripcion |
|-----------|------|-------------|
| `credential_id` | string | ID unico de la credencial |

**Request Body**

```json
{
  "reason": "user_request",
  "reason_text": "Solicitud del usuario"
}
```

**Respuesta exitosa** `200 OK`

```json
{
  "success": true,
  "data": {
    "id": "cred_abc123",
    "status": "revoked",
    "revoked_at": "2024-01-15T15:00:00Z",
    "revocation_reason": "user_request"
  }
}
```

---

## Presentaciones

### Solicitar presentacion

<span class="api-method post">POST</span> `/presentations/request`

Crea una solicitud de presentacion de credenciales.

**Request Body**

```json
{
  "presentation_definition": {
    "id": "identity_verification",
    "input_descriptors": [
      {
        "id": "id_card",
        "name": "ID Card",
        "purpose": "Verificar identidad",
        "constraints": {
          "fields": [
            {
              "path": ["$.credentialSubject.given_name"]
            },
            {
              "path": ["$.credentialSubject.family_name"]
            }
          ]
        }
      }
    ]
  },
  "callback_url": "https://your-app.com/callback",
  "state": "session_abc123"
}
```

**Respuesta exitosa** `201 Created`

```json
{
  "success": true,
  "data": {
    "request_id": "req_abc123",
    "authorization_url": "openid4vp://authorize?request_uri=...",
    "qr_code_url": "https://api.example.com/qr/req_abc123.png",
    "expires_at": "2024-01-15T10:35:00Z"
  }
}
```

---

### Verificar presentacion

<span class="api-method post">POST</span> `/presentations/verify`

Verifica una presentacion de credenciales recibida.

**Request Body**

```json
{
  "vp_token": "eyJhbGciOiJFUzI1NiIs...",
  "presentation_submission": {
    "id": "submission_xyz",
    "definition_id": "identity_verification",
    "descriptor_map": [...]
  }
}
```

**Respuesta exitosa** `200 OK`

```json
{
  "success": true,
  "data": {
    "verified": true,
    "verification_time": "2024-01-15T10:30:00Z",
    "credentials": [
      {
        "type": "VerifiableId",
        "issuer": "did:web:gobierno.es",
        "valid": true,
        "claims": {
          "given_name": "Maria",
          "family_name": "Garcia"
        }
      }
    ]
  }
}
```

---

### Obtener estado de solicitud

<span class="api-method get">GET</span> `/presentations/requests/{request_id}`

Obtiene el estado de una solicitud de presentacion.

**Path Parameters**

| Parametro | Tipo | Descripcion |
|-----------|------|-------------|
| `request_id` | string | ID de la solicitud |

**Respuesta exitosa** `200 OK`

```json
{
  "success": true,
  "data": {
    "request_id": "req_abc123",
    "status": "completed",
    "created_at": "2024-01-15T10:30:00Z",
    "completed_at": "2024-01-15T10:31:00Z",
    "verified_claims": {
      "given_name": "Maria",
      "family_name": "Garcia"
    }
  }
}
```

---

## OID4VCI (Emision)

Endpoints que implementan el protocolo OpenID for Verifiable Credential Issuance.

### Credential Issuer Metadata

<span class="api-method get">GET</span> `/.well-known/openid-credential-issuer`

Obtiene los metadatos del emisor de credenciales.

**Respuesta exitosa** `200 OK`

```json
{
  "credential_issuer": "https://issuer.eudistack.com",
  "credential_endpoint": "https://issuer.eudistack.com/oid4vci/v1/credential",
  "deferred_credential_endpoint": "https://issuer.eudistack.com/oid4vci/v1/deferred-credential",
  "credential_configurations_supported": {
    "LEARCredentialEmployee": {
      "format": "jwt_vc_json",
      "scope": "lear_credential_employee",
      "cryptographic_binding_methods_supported": ["did:key"],
      "credential_signing_alg_values_supported": ["ES256"],
      "credential_definition": {
        "type": ["LEARCredentialEmployee", "VerifiableCredential"]
      },
      "proof_types_supported": {
        "jwt": {
          "proof_signing_alg_values_supported": ["ES256"]
        }
      }
    }
  }
}
```

---

### Authorization Server Metadata

<span class="api-method get">GET</span> `/.well-known/oauth-authorization-server`

Obtiene los metadatos del servidor de autorizacion OAuth 2.0.

**Respuesta exitosa** `200 OK`

```json
{
  "issuer": "https://issuer.eudistack.com",
  "token_endpoint": "https://issuer.eudistack.com/oauth/token",
  "response_types_supported": ["token"],
  "pre-authorized_grant_anonymous_access_supported": true
}
```

---

### Credential Offer

<span class="api-method get">GET</span> `/oid4vci/v1/credential-offer/{nonce}`

Obtiene el objeto Credential Offer a partir del nonce.

**Path Parameters**

| Parametro | Tipo | Descripcion |
|-----------|------|-------------|
| `nonce` | string | Nonce unico de la oferta |

**Respuesta exitosa** `200 OK`

```json
{
  "credential_issuer": "https://issuer.eudistack.com",
  "credential_configuration_ids": ["LEARCredentialEmployee"],
  "grants": {
    "urn:ietf:params:oauth:grant-type:pre-authorized_code": {
      "pre-authorized_code": "adhjhdjajkdkhjhdj",
      "tx_code": {
        "length": 4,
        "input_mode": "numeric",
        "description": "Enter your PIN Code."
      }
    }
  }
}
```

---

### Token Endpoint

<span class="api-method post">POST</span> `/oauth/token`

Obtiene un token de acceso usando el pre-authorized code.

**Request Body** (application/x-www-form-urlencoded)

```
grant_type=urn:ietf:params:oauth:grant-type:pre-authorized_code
&pre-authorized_code=adhjhdjajkdkhjhdj
&tx_code=1234
```

**Respuesta exitosa** `200 OK`

```json
{
  "access_token": "eyJhbGciOiJFUzI1NiIs...",
  "token_type": "Bearer",
  "expires_in": 600,
  "refresh_token": "dGhpcyBpcyBhIHJlZnJlc2..."
}
```

---

### Credential Endpoint

<span class="api-method post">POST</span> `/oid4vci/v1/credential`

Solicita la emision de una credencial.

**Headers**

```http
Authorization: Bearer <access_token>
Content-Type: application/json
```

**Request Body**

```json
{
  "credential_configuration_id": "LEARCredentialEmployee",
  "format": "jwt_vc_json",
  "proof": {
    "proof_type": "jwt",
    "jwt": "eyJhbGciOiJFUzI1NiIs..."
  }
}
```

**Respuesta exitosa - Sincrona** `200 OK`

```json
{
  "credentials": [
    "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCIs..."
  ]
}
```

**Respuesta exitosa - Diferida** `202 Accepted`

```json
{
  "transaction_id": "958e84cf-888b-488a-bf30-7f3b14f70699",
  "interval": 3600
}
```

---

### Deferred Credential Endpoint

<span class="api-method post">POST</span> `/oid4vci/v1/deferred-credential`

Recupera una credencial de emision diferida.

**Headers**

```http
Authorization: Bearer <access_token>
Content-Type: application/json
```

**Request Body**

```json
{
  "transaction_id": "958e84cf-888b-488a-bf30-7f3b14f70699"
}
```

**Respuesta exitosa** `200 OK`

```json
{
  "credentials": [
    "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCIs..."
  ]
}
```

---

## Estado y salud

### Health check

<span class="api-method get">GET</span> `/health`

Verifica el estado de salud de la API.

**Respuesta exitosa** `200 OK`

```json
{
  "status": "UP",
  "version": "1.0.0",
  "services": {
    "database": "UP",
    "cache": "UP",
    "external_apis": "UP"
  }
}
```

---

### Informacion de la API

<span class="api-method get">GET</span> `/info`

Obtiene informacion sobre la API y el emisor.

**Respuesta exitosa** `200 OK`

```json
{
  "success": true,
  "data": {
    "issuer_did": "did:web:api.eudistack.example.com",
    "issuer_name": "EUDIStack API",
    "supported_credential_types": [
      "VerifiableId",
      "VerifiableDiploma",
      "ProfessionalQualification"
    ],
    "supported_formats": [
      "jwt_vc",
      "sd-jwt_vc"
    ],
    "api_version": "1.0.0"
  }
}
```

## Siguiente paso

[:material-shield-key: Ver autorizacion](autorizacion.md){ .md-button }
