---
title: Ejemplos
description: Ejemplos de codigo para integrar con la API de EUDIStack
---

# Ejemplos

Esta pagina contiene ejemplos de codigo en diferentes lenguajes para integrar con la API de EUDIStack.

## Emision de credenciales

### Python

```python
import requests

BASE_URL = "https://api.eudistack.example.com/v1"
ACCESS_TOKEN = "your_access_token"

def issue_credential(credential_type, claims):
    """Emite una nueva credencial."""
    response = requests.post(
        f"{BASE_URL}/credentials/offer",
        headers={
            "Authorization": f"Bearer {ACCESS_TOKEN}",
            "Content-Type": "application/json"
        },
        json={
            "credential_type": credential_type,
            "claims": claims,
            "pin_required": False
        }
    )
    response.raise_for_status()
    return response.json()

# Ejemplo de uso
result = issue_credential(
    credential_type="VerifiableId",
    claims={
        "given_name": "Maria",
        "family_name": "Garcia",
        "birth_date": "1990-05-20",
        "nationality": "ES"
    }
)

print(f"Credential offer URL: {result['data']['credential_offer_uri']}")
print(f"QR Code: {result['data']['qr_code_url']}")
```

### JavaScript/Node.js

```javascript
const BASE_URL = 'https://api.eudistack.example.com/v1';
const ACCESS_TOKEN = 'your_access_token';

async function issueCredential(credentialType, claims) {
  const response = await fetch(`${BASE_URL}/credentials/offer`, {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${ACCESS_TOKEN}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      credential_type: credentialType,
      claims: claims,
      pin_required: false
    })
  });

  if (!response.ok) {
    throw new Error(`HTTP error! status: ${response.status}`);
  }

  return response.json();
}

// Ejemplo de uso
const result = await issueCredential('VerifiableId', {
  given_name: 'Maria',
  family_name: 'Garcia',
  birth_date: '1990-05-20',
  nationality: 'ES'
});

console.log('Credential offer URL:', result.data.credential_offer_uri);
console.log('QR Code:', result.data.qr_code_url);
```

### cURL

```bash
curl -X POST https://api.eudistack.example.com/v1/credentials/offer \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "credential_type": "VerifiableId",
    "claims": {
      "given_name": "Maria",
      "family_name": "Garcia",
      "birth_date": "1990-05-20",
      "nationality": "ES"
    },
    "pin_required": false
  }'
```

---

## Verificacion de credenciales

### Python

```python
import requests

BASE_URL = "https://api.eudistack.example.com/v1"
ACCESS_TOKEN = "your_access_token"

def request_presentation(required_claims, callback_url):
    """Solicita una presentacion de credenciales."""
    response = requests.post(
        f"{BASE_URL}/presentations/request",
        headers={
            "Authorization": f"Bearer {ACCESS_TOKEN}",
            "Content-Type": "application/json"
        },
        json={
            "presentation_definition": {
                "id": "identity_verification",
                "input_descriptors": [
                    {
                        "id": "id_card",
                        "name": "ID Card",
                        "purpose": "Verificar identidad",
                        "constraints": {
                            "fields": [
                                {"path": [f"$.credentialSubject.{claim}"]}
                                for claim in required_claims
                            ]
                        }
                    }
                ]
            },
            "callback_url": callback_url
        }
    )
    response.raise_for_status()
    return response.json()

def check_presentation_status(request_id):
    """Verifica el estado de una solicitud de presentacion."""
    response = requests.get(
        f"{BASE_URL}/presentations/requests/{request_id}",
        headers={"Authorization": f"Bearer {ACCESS_TOKEN}"}
    )
    response.raise_for_status()
    return response.json()

# Ejemplo de uso
result = request_presentation(
    required_claims=["given_name", "family_name", "age_over_18"],
    callback_url="https://your-app.com/callback"
)

print(f"Authorization URL: {result['data']['authorization_url']}")
print(f"Request ID: {result['data']['request_id']}")

# Mas tarde, verificar el estado
status = check_presentation_status(result['data']['request_id'])
if status['data']['status'] == 'completed':
    print(f"Verified claims: {status['data']['verified_claims']}")
```

### JavaScript/Node.js

```javascript
const BASE_URL = 'https://api.eudistack.example.com/v1';
const ACCESS_TOKEN = 'your_access_token';

async function requestPresentation(requiredClaims, callbackUrl) {
  const response = await fetch(`${BASE_URL}/presentations/request`, {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${ACCESS_TOKEN}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      presentation_definition: {
        id: 'identity_verification',
        input_descriptors: [{
          id: 'id_card',
          name: 'ID Card',
          purpose: 'Verify identity',
          constraints: {
            fields: requiredClaims.map(claim => ({
              path: [`$.credentialSubject.${claim}`]
            }))
          }
        }]
      },
      callback_url: callbackUrl
    })
  });

  if (!response.ok) {
    throw new Error(`HTTP error! status: ${response.status}`);
  }

  return response.json();
}

async function checkPresentationStatus(requestId) {
  const response = await fetch(
    `${BASE_URL}/presentations/requests/${requestId}`,
    {
      headers: { 'Authorization': `Bearer ${ACCESS_TOKEN}` }
    }
  );

  if (!response.ok) {
    throw new Error(`HTTP error! status: ${response.status}`);
  }

  return response.json();
}

// Ejemplo de uso
const result = await requestPresentation(
  ['given_name', 'family_name', 'age_over_18'],
  'https://your-app.com/callback'
);

console.log('Authorization URL:', result.data.authorization_url);
console.log('Request ID:', result.data.request_id);
```

---

## Webhook de callback

### Python (Flask)

```python
from flask import Flask, request, jsonify
import hmac
import hashlib

app = Flask(__name__)
WEBHOOK_SECRET = "your_webhook_secret"

def verify_signature(payload, signature):
    """Verifica la firma del webhook."""
    expected = hmac.new(
        WEBHOOK_SECRET.encode(),
        payload,
        hashlib.sha256
    ).hexdigest()
    return hmac.compare_digest(f"sha256={expected}", signature)

@app.route('/callback', methods=['POST'])
def presentation_callback():
    # Verificar firma
    signature = request.headers.get('X-Signature')
    if not verify_signature(request.data, signature):
        return jsonify({"error": "Invalid signature"}), 401

    data = request.json

    if data['status'] == 'success':
        # Procesar credenciales verificadas
        claims = data['verified_claims']
        print(f"Usuario verificado: {claims['given_name']} {claims['family_name']}")

        # Aqui puedes crear una sesion, actualizar BD, etc.
        return jsonify({"status": "ok"})

    elif data['status'] == 'error':
        print(f"Error de verificacion: {data['error']}")
        return jsonify({"status": "error"}), 400

    return jsonify({"status": "ok"})

if __name__ == '__main__':
    app.run(port=5000)
```

### JavaScript (Express)

```javascript
const express = require('express');
const crypto = require('crypto');

const app = express();
const WEBHOOK_SECRET = 'your_webhook_secret';

app.use(express.json({
  verify: (req, res, buf) => {
    req.rawBody = buf;
  }
}));

function verifySignature(payload, signature) {
  const expected = crypto
    .createHmac('sha256', WEBHOOK_SECRET)
    .update(payload)
    .digest('hex');
  return crypto.timingSafeEqual(
    Buffer.from(`sha256=${expected}`),
    Buffer.from(signature)
  );
}

app.post('/callback', (req, res) => {
  const signature = req.headers['x-signature'];

  if (!verifySignature(req.rawBody, signature)) {
    return res.status(401).json({ error: 'Invalid signature' });
  }

  const { status, verified_claims, error } = req.body;

  if (status === 'success') {
    console.log(`Usuario verificado: ${verified_claims.given_name} ${verified_claims.family_name}`);
    // Procesar credenciales verificadas
    return res.json({ status: 'ok' });
  }

  if (status === 'error') {
    console.log(`Error de verificacion: ${error}`);
    return res.status(400).json({ status: 'error' });
  }

  res.json({ status: 'ok' });
});

app.listen(5000, () => {
  console.log('Server running on port 5000');
});
```

---

## Integracion completa

### Clase cliente completa (Python)

```python
import requests
import time
from typing import Dict, List, Optional

class EUDIStackClient:
    """Cliente completo para la API de EUDIStack."""

    def __init__(self, client_id: str, client_secret: str, base_url: str = None):
        self.client_id = client_id
        self.client_secret = client_secret
        self.base_url = base_url or "https://api.eudistack.example.com/v1"
        self.auth_url = "https://auth.eudistack.example.com/oauth/token"
        self._token = None
        self._token_expires_at = 0

    def _get_token(self) -> str:
        """Obtiene o renueva el token de acceso."""
        if time.time() < self._token_expires_at - 60:
            return self._token

        response = requests.post(
            self.auth_url,
            data={
                "grant_type": "client_credentials",
                "client_id": self.client_id,
                "client_secret": self.client_secret,
                "scope": "credentials:read credentials:write presentations:read presentations:write"
            }
        )
        response.raise_for_status()
        data = response.json()
        self._token = data["access_token"]
        self._token_expires_at = time.time() + data["expires_in"]
        return self._token

    def _request(self, method: str, endpoint: str, **kwargs) -> Dict:
        """Realiza una peticion autenticada."""
        headers = kwargs.pop("headers", {})
        headers["Authorization"] = f"Bearer {self._get_token()}"

        response = requests.request(
            method,
            f"{self.base_url}{endpoint}",
            headers=headers,
            **kwargs
        )
        response.raise_for_status()
        return response.json()

    # Credenciales
    def list_credentials(self, **params) -> Dict:
        return self._request("GET", "/credentials", params=params)

    def create_credential_offer(
        self,
        credential_type: str,
        claims: Dict,
        pin_required: bool = False
    ) -> Dict:
        return self._request(
            "POST",
            "/credentials/offer",
            json={
                "credential_type": credential_type,
                "claims": claims,
                "pin_required": pin_required
            }
        )

    def get_credential(self, credential_id: str) -> Dict:
        return self._request("GET", f"/credentials/{credential_id}")

    def revoke_credential(self, credential_id: str, reason: str = "user_request") -> Dict:
        return self._request(
            "DELETE",
            f"/credentials/{credential_id}",
            json={"reason": reason}
        )

    # Presentaciones
    def request_presentation(
        self,
        required_claims: List[str],
        callback_url: str,
        purpose: str = "Identity verification"
    ) -> Dict:
        return self._request(
            "POST",
            "/presentations/request",
            json={
                "presentation_definition": {
                    "id": "verification",
                    "input_descriptors": [{
                        "id": "credential",
                        "purpose": purpose,
                        "constraints": {
                            "fields": [
                                {"path": [f"$.credentialSubject.{claim}"]}
                                for claim in required_claims
                            ]
                        }
                    }]
                },
                "callback_url": callback_url
            }
        )

    def get_presentation_status(self, request_id: str) -> Dict:
        return self._request("GET", f"/presentations/requests/{request_id}")

# Uso
client = EUDIStackClient(
    client_id="your_client_id",
    client_secret="your_client_secret"
)

# Emitir credencial
offer = client.create_credential_offer(
    credential_type="VerifiableId",
    claims={
        "given_name": "Maria",
        "family_name": "Garcia",
        "birth_date": "1990-05-20"
    }
)
print(f"Offer URL: {offer['data']['credential_offer_uri']}")

# Verificar credencial
verification = client.request_presentation(
    required_claims=["given_name", "age_over_18"],
    callback_url="https://your-app.com/callback"
)
print(f"Auth URL: {verification['data']['authorization_url']}")
```

## Siguiente paso

[:material-sitemap: Ver arquitectura del sistema](../arquitectura/index.md){ .md-button }
