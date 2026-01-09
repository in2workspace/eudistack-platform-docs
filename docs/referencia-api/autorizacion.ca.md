---
title: Autoritzacio
description: Com autenticar-te i autoritzar peticions a l'API d'EUDIStack
---

# Autoritzacio

Aquesta guia explica com obtenir i utilitzar tokens d'acces per autenticar-te a l'API d'EUDIStack.

## Metodes d'autenticacio

EUDIStack suporta els seguents metodes d'autenticacio:

| Metode | Cas d'us |
|--------|----------|
| **OAuth 2.0 Client Credentials** | Aplicacions servidor a servidor |
| **API Keys** | Desenvolupament i testing |

## OAuth 2.0 Client Credentials

### Obtenir credencials de client

1. Registra la teva aplicacio al portal de desenvolupadors
2. Obtindras un `client_id` i `client_secret`
3. Guarda el `client_secret` de forma segura (no es pot recuperar)

### Sollicitar token d'acces

```bash
curl -X POST https://auth.eudistack.example.com/oauth/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=client_credentials" \
  -d "client_id=YOUR_CLIENT_ID" \
  -d "client_secret=YOUR_CLIENT_SECRET" \
  -d "scope=credentials:read credentials:write presentations:read presentations:write"
```

**Resposta:**

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "scope": "credentials:read credentials:write presentations:read presentations:write"
}
```

### Usar el token

Inclou el token al header `Authorization`:

```bash
curl -X GET https://api.eudistack.example.com/v1/credentials \
  -H "Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9..."
```

## Scopes disponibles

| Scope | Descripcio |
|-------|------------|
| `credentials:read` | Llegir credencials |
| `credentials:write` | Crear i revocar credencials |
| `presentations:read` | Llegir sollicituds de presentacio |
| `presentations:write` | Crear sollicituds de presentacio |
| `admin` | Acces administratiu complet |

## API Keys

Per a desenvolupament i testing, pots usar API Keys.

!!! warning "Nomes per a desenvolupament"
    Les API Keys no s'han d'usar en produccio. Usa OAuth 2.0 per a entorns productius.

### Obtenir API Key

1. Accedeix al portal de desenvolupadors
2. Navega a "API Keys"
3. Genera una nova key

### Usar API Key

```bash
curl -X GET https://api.eudistack.example.com/v1/credentials \
  -H "X-API-Key: sk_test_abc123..."
```

## Exemple d'integracio

### Python

```python
import requests

class EUDIStackClient:
    def __init__(self, client_id, client_secret):
        self.client_id = client_id
        self.client_secret = client_secret
        self.base_url = "https://api.eudistack.example.com/v1"
        self.token = None

    def authenticate(self):
        response = requests.post(
            "https://auth.eudistack.example.com/oauth/token",
            data={
                "grant_type": "client_credentials",
                "client_id": self.client_id,
                "client_secret": self.client_secret,
                "scope": "credentials:read credentials:write"
            }
        )
        response.raise_for_status()
        self.token = response.json()["access_token"]

    def get_headers(self):
        if not self.token:
            self.authenticate()
        return {"Authorization": f"Bearer {self.token}"}

    def list_credentials(self):
        response = requests.get(
            f"{self.base_url}/credentials",
            headers=self.get_headers()
        )
        response.raise_for_status()
        return response.json()

# Us
client = EUDIStackClient("your_client_id", "your_client_secret")
credentials = client.list_credentials()
print(credentials)
```

### JavaScript/Node.js

```javascript
class EUDIStackClient {
  constructor(clientId, clientSecret) {
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.baseUrl = 'https://api.eudistack.example.com/v1';
    this.token = null;
  }

  async authenticate() {
    const response = await fetch('https://auth.eudistack.example.com/oauth/token', {
      method: 'POST',
      headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
      body: new URLSearchParams({
        grant_type: 'client_credentials',
        client_id: this.clientId,
        client_secret: this.clientSecret,
        scope: 'credentials:read credentials:write'
      })
    });

    if (!response.ok) throw new Error('Authentication failed');
    const data = await response.json();
    this.token = data.access_token;
  }

  async getHeaders() {
    if (!this.token) await this.authenticate();
    return { Authorization: `Bearer ${this.token}` };
  }

  async listCredentials() {
    const response = await fetch(`${this.baseUrl}/credentials`, {
      headers: await this.getHeaders()
    });
    if (!response.ok) throw new Error('Request failed');
    return response.json();
  }
}

// Us
const client = new EUDIStackClient('your_client_id', 'your_client_secret');
const credentials = await client.listCredentials();
console.log(credentials);
```

## Renovacio de tokens

Els tokens expiren despres del temps indicat a `expires_in`. Implementa renovacio automatica:

```python
import time

class EUDIStackClient:
    def __init__(self, client_id, client_secret):
        self.client_id = client_id
        self.client_secret = client_secret
        self.token = None
        self.token_expires_at = 0

    def get_headers(self):
        # Renovar si expira en menys de 60 segons
        if time.time() > self.token_expires_at - 60:
            self.authenticate()
        return {"Authorization": f"Bearer {self.token}"}

    def authenticate(self):
        response = requests.post(...)
        data = response.json()
        self.token = data["access_token"]
        self.token_expires_at = time.time() + data["expires_in"]
```

## Errors d'autenticacio

| Codi | Error | Solucio |
|------|-------|---------|
| `401` | `invalid_token` | El token ha expirat o es invalid. Obten-ne un de nou. |
| `401` | `invalid_client` | Client ID o Secret incorrectes |
| `403` | `insufficient_scope` | El token no te els permisos necessaris |

## Seguretat

!!! danger "Protegeix les teves credencials"
    - Mai exposis el `client_secret` en codi frontend
    - Usa variables d'entorn per emmagatzemar credencials
    - Rota les credencials periodicament
    - Usa HTTPS sempre

```bash
# Bona practica: usar variables d'entorn
export EUDISTACK_CLIENT_ID="your_client_id"
export EUDISTACK_CLIENT_SECRET="your_client_secret"
```

```python
import os

client_id = os.environ.get("EUDISTACK_CLIENT_ID")
client_secret = os.environ.get("EUDISTACK_CLIENT_SECRET")
```

## Seguent pas

[:material-code-braces: Veure exemples de codi](ejemplos.md){ .md-button }
