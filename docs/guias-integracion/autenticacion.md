---
title: Autenticacion
description: Implementa flujos de autenticacion con EUDI Wallet
---

# Autenticacion

Esta guia explica como implementar flujos de autenticacion utilizando EUDIStack y el EUDI Wallet.

## Protocolos soportados

EUDIStack implementa los siguientes protocolos de autenticacion:

| Protocolo | Descripcion | Caso de uso |
|-----------|-------------|-------------|
| **OpenID4VP** | OpenID for Verifiable Presentations | Verificacion de credenciales |
| **OpenID4VCI** | OpenID for Verifiable Credential Issuance | Emision de credenciales |
| **SIOPv2** | Self-Issued OpenID Provider v2 | Autenticacion descentralizada |

## Flujo de verificacion (OpenID4VP)

El flujo de verificacion permite a un servicio solicitar y verificar credenciales del wallet del usuario.

```mermaid
sequenceDiagram
    participant U as Usuario
    participant W as EUDI Wallet
    participant V as Verifier (Tu App)
    participant API as EUDIStack API

    U->>V: Accede al servicio
    V->>API: POST /presentations/request
    API-->>V: Authorization Request URL
    V->>W: Redirect/QR con URL
    W->>U: Solicita consentimiento
    U->>W: Autoriza presentacion
    W->>API: POST /presentations/response
    API->>API: Valida presentacion
    API-->>V: Webhook con resultado
    V-->>U: Acceso concedido
```

### Implementacion

#### 1. Crear solicitud de presentacion

```bash
curl -X POST https://api.example.com/api/v1/presentations/request \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "presentation_definition": {
      "id": "identity_verification",
      "input_descriptors": [
        {
          "id": "id_card",
          "name": "ID Card",
          "purpose": "Verificar identidad del usuario",
          "constraints": {
            "fields": [
              {
                "path": ["$.credentialSubject.given_name"],
                "filter": { "type": "string" }
              },
              {
                "path": ["$.credentialSubject.family_name"],
                "filter": { "type": "string" }
              },
              {
                "path": ["$.credentialSubject.birth_date"],
                "filter": { "type": "string", "format": "date" }
              }
            ]
          }
        }
      ]
    },
    "callback_url": "https://your-app.com/callback"
  }'
```

Respuesta:

```json
{
  "request_id": "req_abc123",
  "authorization_url": "openid4vp://authorize?request_uri=...",
  "qr_code_url": "https://api.example.com/qr/req_abc123.png",
  "expires_at": "2024-01-15T10:30:00Z"
}
```

#### 2. Mostrar QR o redirect

=== "QR Code"

    ```html
    <img src="https://api.example.com/qr/req_abc123.png" alt="Escanea con EUDI Wallet">
    ```

=== "Deep Link"

    ```javascript
    // En movil, redirect directo al wallet
    window.location.href = authorizationUrl;
    ```

#### 3. Recibir callback

Configura un endpoint para recibir el resultado:

```python
from flask import Flask, request

app = Flask(__name__)

@app.route('/callback', methods=['POST'])
def verification_callback():
    data = request.json

    if data['status'] == 'success':
        # Credenciales verificadas correctamente
        claims = data['verified_claims']
        user_name = claims['given_name']
        # Procesar autenticacion exitosa
        return {'status': 'ok'}
    else:
        # Error en la verificacion
        error = data['error']
        return {'status': 'error', 'message': error}, 400
```

## Flujo de emision (OpenID4VCI)

El flujo de emision permite a tu aplicacion emitir credenciales al wallet del usuario.

```mermaid
sequenceDiagram
    participant U as Usuario
    participant W as EUDI Wallet
    participant I as Issuer (Tu App)
    participant API as EUDIStack API

    U->>I: Solicita credencial
    I->>I: Verifica elegibilidad
    I->>API: POST /credentials/offer
    API-->>I: Credential Offer URL
    I->>W: Redirect/QR con URL
    W->>API: Solicita credencial
    API->>API: Genera y firma VC
    API-->>W: Credencial emitida
    W->>U: Credencial guardada
```

### Implementacion

#### 1. Crear oferta de credencial

```bash
curl -X POST https://api.example.com/api/v1/credentials/offer \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "credential_type": "VerifiableId",
    "claims": {
      "given_name": "Maria",
      "family_name": "Lopez",
      "birth_date": "1985-03-20",
      "nationality": "ES"
    },
    "pin_required": true
  }'
```

Respuesta:

```json
{
  "offer_id": "offer_xyz789",
  "credential_offer_uri": "openid-credential-offer://...",
  "qr_code_url": "https://api.example.com/qr/offer_xyz789.png",
  "pin": "1234",
  "expires_at": "2024-01-15T10:30:00Z"
}
```

## Seguridad

### Validacion de tokens

!!! warning "Importante"
    Siempre valida los tokens de acceso antes de procesar solicitudes.

```python
import jwt
from functools import wraps

def require_auth(f):
    @wraps(f)
    def decorated(*args, **kwargs):
        token = request.headers.get('Authorization', '').replace('Bearer ', '')

        try:
            payload = jwt.decode(
                token,
                PUBLIC_KEY,
                algorithms=['RS256'],
                audience='your-api'
            )
            request.user = payload
        except jwt.InvalidTokenError:
            return {'error': 'Invalid token'}, 401

        return f(*args, **kwargs)
    return decorated
```

### Lista de emisores confiables

Configura los emisores cuyas credenciales aceptaras:

```yaml
trusted_issuers:
  - did: did:web:government.example.com
    name: "Gobierno de Espana"
    credential_types:
      - VerifiableId
      - DriverLicense

  - did: did:web:university.example.com
    name: "Universidad de Barcelona"
    credential_types:
      - VerifiableDiploma
```

## Siguiente paso

[:material-api: Explorar la API completa](../referencia-api/index.md){ .md-button }
