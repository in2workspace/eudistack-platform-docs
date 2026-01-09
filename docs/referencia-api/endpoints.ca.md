---
title: Endpoints
description: Llistat complet d'endpoints de l'API d'EUDIStack
---

# Endpoints

Documentacio detallada de tots els endpoints disponibles a l'API.

## Credencials

### Llistar credencials

<span class="api-method get">GET</span> `/credentials`

Obte una llista de credencials emeses.

**Query Parameters**

| Parametre | Tipus | Descripcio |
|-----------|-------|------------|
| `type` | string | Filtrar per tipus de credencial |
| `status` | string | Filtrar per estat (active, revoked, expired) |
| `page` | integer | Numero de pagina (default: 1) |
| `per_page` | integer | Elements per pagina (default: 20, max: 100) |

**Resposta exitosa** `200 OK`

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

Crea una oferta de credencial que l'usuari pot acceptar al seu wallet.

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

**Resposta exitosa** `201 Created`

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

### Obtenir credencial

<span class="api-method get">GET</span> `/credentials/{credential_id}`

Obte els detalls d'una credencial especifica.

**Path Parameters**

| Parametre | Tipus | Descripcio |
|-----------|-------|------------|
| `credential_id` | string | ID unic de la credencial |

**Resposta exitosa** `200 OK`

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

Revoca una credencial emesa.

**Path Parameters**

| Parametre | Tipus | Descripcio |
|-----------|-------|------------|
| `credential_id` | string | ID unic de la credencial |

**Request Body**

```json
{
  "reason": "user_request",
  "reason_text": "Sollicitud de l'usuari"
}
```

**Resposta exitosa** `200 OK`

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

## Presentacions

### Sollicitar presentacio

<span class="api-method post">POST</span> `/presentations/request`

Crea una sollicitud de presentacio de credencials.

**Request Body**

```json
{
  "presentation_definition": {
    "id": "identity_verification",
    "input_descriptors": [
      {
        "id": "id_card",
        "name": "ID Card",
        "purpose": "Verificar identitat",
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

**Resposta exitosa** `201 Created`

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

### Verificar presentacio

<span class="api-method post">POST</span> `/presentations/verify`

Verifica una presentacio de credencials rebuda.

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

**Resposta exitosa** `200 OK`

```json
{
  "success": true,
  "data": {
    "verified": true,
    "verification_time": "2024-01-15T10:30:00Z",
    "credentials": [
      {
        "type": "VerifiableId",
        "issuer": "did:web:govern.es",
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

### Obtenir estat de sollicitud

<span class="api-method get">GET</span> `/presentations/requests/{request_id}`

Obte l'estat d'una sollicitud de presentacio.

**Path Parameters**

| Parametre | Tipus | Descripcio |
|-----------|-------|------------|
| `request_id` | string | ID de la sollicitud |

**Resposta exitosa** `200 OK`

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

## Estat i salut

### Health check

<span class="api-method get">GET</span> `/health`

Verifica l'estat de salut de l'API.

**Resposta exitosa** `200 OK`

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

### Informacio de l'API

<span class="api-method get">GET</span> `/info`

Obte informacio sobre l'API i l'emissor.

**Resposta exitosa** `200 OK`

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

## Seguent pas

[:material-shield-key: Veure autoritzacio](autorizacion.md){ .md-button }
