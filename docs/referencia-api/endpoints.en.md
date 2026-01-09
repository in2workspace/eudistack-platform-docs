---
title: Endpoints
description: Complete list of EUDIStack API endpoints
---

# Endpoints

Detailed documentation of all endpoints available in the API.

## Credentials

### List credentials

<span class="api-method get">GET</span> `/credentials`

Gets a list of issued credentials.

**Query Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `type` | string | Filter by credential type |
| `status` | string | Filter by status (active, revoked, expired) |
| `page` | integer | Page number (default: 1) |
| `per_page` | integer | Items per page (default: 20, max: 100) |

**Successful Response** `200 OK`

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

### Create credential offer

<span class="api-method post">POST</span> `/credentials/offer`

Creates a credential offer that the user can accept in their wallet.

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

**Successful Response** `201 Created`

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

### Get credential

<span class="api-method get">GET</span> `/credentials/{credential_id}`

Gets the details of a specific credential.

**Path Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `credential_id` | string | Unique credential ID |

**Successful Response** `200 OK`

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

### Revoke credential

<span class="api-method delete">DELETE</span> `/credentials/{credential_id}`

Revokes an issued credential.

**Path Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `credential_id` | string | Unique credential ID |

**Request Body**

```json
{
  "reason": "user_request",
  "reason_text": "User request"
}
```

**Successful Response** `200 OK`

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

## Presentations

### Request presentation

<span class="api-method post">POST</span> `/presentations/request`

Creates a credential presentation request.

**Request Body**

```json
{
  "presentation_definition": {
    "id": "identity_verification",
    "input_descriptors": [
      {
        "id": "id_card",
        "name": "ID Card",
        "purpose": "Verify identity",
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

**Successful Response** `201 Created`

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

### Verify presentation

<span class="api-method post">POST</span> `/presentations/verify`

Verifies a received credential presentation.

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

**Successful Response** `200 OK`

```json
{
  "success": true,
  "data": {
    "verified": true,
    "verification_time": "2024-01-15T10:30:00Z",
    "credentials": [
      {
        "type": "VerifiableId",
        "issuer": "did:web:government.es",
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

### Get request status

<span class="api-method get">GET</span> `/presentations/requests/{request_id}`

Gets the status of a presentation request.

**Path Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `request_id` | string | Request ID |

**Successful Response** `200 OK`

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

## Status and Health

### Health check

<span class="api-method get">GET</span> `/health`

Checks the API health status.

**Successful Response** `200 OK`

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

### API information

<span class="api-method get">GET</span> `/info`

Gets information about the API and issuer.

**Successful Response** `200 OK`

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

## Next Step

[:material-shield-key: View authorization](autorizacion.md){ .md-button }
