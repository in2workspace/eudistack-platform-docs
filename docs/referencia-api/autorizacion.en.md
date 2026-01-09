---
title: Authorization
description: How to authenticate and authorize requests to the EUDIStack API
---

# Authorization

This guide explains how to obtain and use access tokens to authenticate with the EUDIStack API.

## Authentication Methods

EUDIStack supports the following authentication methods:

| Method | Use Case |
|--------|----------|
| **OAuth 2.0 Client Credentials** | Server-to-server applications |
| **API Keys** | Development and testing |

## OAuth 2.0 Client Credentials

### Get Client Credentials

1. Register your application in the developer portal
2. You will receive a `client_id` and `client_secret`
3. Store the `client_secret` securely (it cannot be recovered)

### Request Access Token

```bash
curl -X POST https://auth.eudistack.example.com/oauth/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=client_credentials" \
  -d "client_id=YOUR_CLIENT_ID" \
  -d "client_secret=YOUR_CLIENT_SECRET" \
  -d "scope=credentials:read credentials:write presentations:read presentations:write"
```

**Response:**

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "scope": "credentials:read credentials:write presentations:read presentations:write"
}
```

### Use the Token

Include the token in the `Authorization` header:

```bash
curl -X GET https://api.eudistack.example.com/v1/credentials \
  -H "Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9..."
```

## Available Scopes

| Scope | Description |
|-------|-------------|
| `credentials:read` | Read credentials |
| `credentials:write` | Create and revoke credentials |
| `presentations:read` | Read presentation requests |
| `presentations:write` | Create presentation requests |
| `admin` | Full administrative access |

## API Keys

For development and testing, you can use API Keys.

!!! warning "Development Only"
    API Keys should not be used in production. Use OAuth 2.0 for production environments.

### Get API Key

1. Access the developer portal
2. Navigate to "API Keys"
3. Generate a new key

### Use API Key

```bash
curl -X GET https://api.eudistack.example.com/v1/credentials \
  -H "X-API-Key: sk_test_abc123..."
```

## Integration Example

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

# Usage
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

// Usage
const client = new EUDIStackClient('your_client_id', 'your_client_secret');
const credentials = await client.listCredentials();
console.log(credentials);
```

## Token Renewal

Tokens expire after the time indicated in `expires_in`. Implement automatic renewal:

```python
import time

class EUDIStackClient:
    def __init__(self, client_id, client_secret):
        self.client_id = client_id
        self.client_secret = client_secret
        self.token = None
        self.token_expires_at = 0

    def get_headers(self):
        # Renew if expiring in less than 60 seconds
        if time.time() > self.token_expires_at - 60:
            self.authenticate()
        return {"Authorization": f"Bearer {self.token}"}

    def authenticate(self):
        response = requests.post(...)
        data = response.json()
        self.token = data["access_token"]
        self.token_expires_at = time.time() + data["expires_in"]
```

## Authentication Errors

| Code | Error | Solution |
|------|-------|----------|
| `401` | `invalid_token` | Token has expired or is invalid. Get a new one. |
| `401` | `invalid_client` | Incorrect Client ID or Secret |
| `403` | `insufficient_scope` | Token lacks required permissions |

## Security

!!! danger "Protect Your Credentials"
    - Never expose `client_secret` in frontend code
    - Use environment variables to store credentials
    - Rotate credentials periodically
    - Always use HTTPS

```bash
# Best practice: use environment variables
export EUDISTACK_CLIENT_ID="your_client_id"
export EUDISTACK_CLIENT_SECRET="your_client_secret"
```

```python
import os

client_id = os.environ.get("EUDISTACK_CLIENT_ID")
client_secret = os.environ.get("EUDISTACK_CLIENT_SECRET")
```

## Next Step

[:material-code-braces: View code examples](ejemplos.md){ .md-button }
