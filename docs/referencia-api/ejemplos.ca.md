---
title: Exemples
description: Exemples de codi per integrar amb l'API d'EUDIStack
---

# Exemples

Aquesta pagina conte exemples de codi en diferents llenguatges per integrar amb l'API d'EUDIStack.

## Emissio de credencials

### Python

```python
import requests

BASE_URL = "https://api.eudistack.example.com/v1"
ACCESS_TOKEN = "your_access_token"

def issue_credential(credential_type, claims):
    """Emet una nova credencial."""
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

# Exemple d'us
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

// Exemple d'us
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

## Verificacio de credencials

### Python

```python
import requests

BASE_URL = "https://api.eudistack.example.com/v1"
ACCESS_TOKEN = "your_access_token"

def request_presentation(required_claims, callback_url):
    """Sollicita una presentacio de credencials."""
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
                        "purpose": "Verificar identitat",
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
    """Verifica l'estat d'una sollicitud de presentacio."""
    response = requests.get(
        f"{BASE_URL}/presentations/requests/{request_id}",
        headers={"Authorization": f"Bearer {ACCESS_TOKEN}"}
    )
    response.raise_for_status()
    return response.json()

# Exemple d'us
result = request_presentation(
    required_claims=["given_name", "family_name", "age_over_18"],
    callback_url="https://your-app.com/callback"
)

print(f"Authorization URL: {result['data']['authorization_url']}")
print(f"Request ID: {result['data']['request_id']}")

# Mes tard, verificar l'estat
status = check_presentation_status(result['data']['request_id'])
if status['data']['status'] == 'completed':
    print(f"Verified claims: {status['data']['verified_claims']}")
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
    """Verifica la signatura del webhook."""
    expected = hmac.new(
        WEBHOOK_SECRET.encode(),
        payload,
        hashlib.sha256
    ).hexdigest()
    return hmac.compare_digest(f"sha256={expected}", signature)

@app.route('/callback', methods=['POST'])
def presentation_callback():
    # Verificar signatura
    signature = request.headers.get('X-Signature')
    if not verify_signature(request.data, signature):
        return jsonify({"error": "Invalid signature"}), 401

    data = request.json

    if data['status'] == 'success':
        # Processar credencials verificades
        claims = data['verified_claims']
        print(f"Usuari verificat: {claims['given_name']} {claims['family_name']}")

        # Aqui pots crear una sessio, actualitzar BD, etc.
        return jsonify({"status": "ok"})

    elif data['status'] == 'error':
        print(f"Error de verificacio: {data['error']}")
        return jsonify({"status": "error"}), 400

    return jsonify({"status": "ok"})

if __name__ == '__main__':
    app.run(port=5000)
```

---

## Integracio completa

### Classe client completa (Python)

```python
import requests
import time
from typing import Dict, List, Optional

class EUDIStackClient:
    """Client complet per a l'API d'EUDIStack."""

    def __init__(self, client_id: str, client_secret: str, base_url: str = None):
        self.client_id = client_id
        self.client_secret = client_secret
        self.base_url = base_url or "https://api.eudistack.example.com/v1"
        self.auth_url = "https://auth.eudistack.example.com/oauth/token"
        self._token = None
        self._token_expires_at = 0

    def _get_token(self) -> str:
        """Obte o renova el token d'acces."""
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
        """Realitza una peticio autenticada."""
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

    # Credencials
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

# Us
client = EUDIStackClient(
    client_id="your_client_id",
    client_secret="your_client_secret"
)

# Emetre credencial
offer = client.create_credential_offer(
    credential_type="VerifiableId",
    claims={
        "given_name": "Maria",
        "family_name": "Garcia",
        "birth_date": "1990-05-20"
    }
)
print(f"Offer URL: {offer['data']['credential_offer_uri']}")
```

## Seguent pas

[:material-sitemap: Veure arquitectura del sistema](../arquitectura/index.md){ .md-button }
