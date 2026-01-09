---
title: Autorizacion
description: Como autenticarte y autorizar peticiones a la API de EUDIStack
---

# Autorizacion

Esta guia explica como obtener y utilizar tokens de acceso para autenticarte en la API de EUDIStack.

## Metodos de autenticacion

EUDIStack soporta los siguientes metodos de autenticacion:

| Metodo | Caso de uso |
|--------|-------------|
| **OAuth 2.0 Client Credentials** | Aplicaciones servidor a servidor |
| **API Keys** | Desarrollo y testing |

## OAuth 2.0 Client Credentials

### Obtener credenciales de cliente

1. Registra tu aplicacion en el portal de desarrolladores
2. Obtendras un `client_id` y `client_secret`
3. Guarda el `client_secret` de forma segura (no se puede recuperar)

### Solicitar token de acceso

```bash
curl -X POST https://auth.eudistack.example.com/oauth/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=client_credentials" \
  -d "client_id=YOUR_CLIENT_ID" \
  -d "client_secret=YOUR_CLIENT_SECRET" \
  -d "scope=credentials:read credentials:write presentations:read presentations:write"
```

**Respuesta:**

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "scope": "credentials:read credentials:write presentations:read presentations:write"
}
```

### Usar el token

Incluye el token en el header `Authorization`:

```bash
curl -X GET https://api.eudistack.example.com/v1/credentials \
  -H "Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9..."
```

## Scopes disponibles

| Scope | Descripcion |
|-------|-------------|
| `credentials:read` | Leer credenciales |
| `credentials:write` | Crear y revocar credenciales |
| `presentations:read` | Leer solicitudes de presentacion |
| `presentations:write` | Crear solicitudes de presentacion |
| `admin` | Acceso administrativo completo |

## API Keys

Para desarrollo y testing, puedes usar API Keys.

!!! warning "Solo para desarrollo"
    Las API Keys no deben usarse en produccion. Usa OAuth 2.0 para entornos productivos.

### Obtener API Key

1. Accede al portal de desarrolladores
2. Navega a "API Keys"
3. Genera una nueva key

### Usar API Key

```bash
curl -X GET https://api.eudistack.example.com/v1/credentials \
  -H "X-API-Key: sk_test_abc123..."
```

## Ejemplo de integracion

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

# Uso
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

// Uso
const client = new EUDIStackClient('your_client_id', 'your_client_secret');
const credentials = await client.listCredentials();
console.log(credentials);
```

## Renovacion de tokens

Los tokens expiran despues del tiempo indicado en `expires_in`. Implementa renovacion automatica:

```python
import time

class EUDIStackClient:
    def __init__(self, client_id, client_secret):
        self.client_id = client_id
        self.client_secret = client_secret
        self.token = None
        self.token_expires_at = 0

    def get_headers(self):
        # Renovar si expira en menos de 60 segundos
        if time.time() > self.token_expires_at - 60:
            self.authenticate()
        return {"Authorization": f"Bearer {self.token}"}

    def authenticate(self):
        response = requests.post(...)
        data = response.json()
        self.token = data["access_token"]
        self.token_expires_at = time.time() + data["expires_in"]
```

## Errores de autenticacion

| Codigo | Error | Solucion |
|--------|-------|----------|
| `401` | `invalid_token` | El token ha expirado o es invalido. Obtén uno nuevo. |
| `401` | `invalid_client` | Client ID o Secret incorrectos |
| `403` | `insufficient_scope` | El token no tiene los permisos necesarios |

## Seguridad

!!! danger "Protege tus credenciales"
    - Nunca expongas el `client_secret` en codigo frontend
    - Usa variables de entorno para almacenar credenciales
    - Rota las credenciales periodicamente
    - Usa HTTPS siempre

```bash
# Buena practica: usar variables de entorno
export EUDISTACK_CLIENT_ID="your_client_id"
export EUDISTACK_CLIENT_SECRET="your_client_secret"
```

```python
import os

client_id = os.environ.get("EUDISTACK_CLIENT_ID")
client_secret = os.environ.get("EUDISTACK_CLIENT_SECRET")
```

## Siguiente paso

[:material-code-braces: Ver ejemplos de codigo](ejemplos.md){ .md-button }
