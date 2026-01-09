---
title: Quick Start
description: Set up your environment and run your first integration with EUDIStack
---

# Quick Start

This guide will help you set up EUDIStack and run your first integration in just a few minutes.

## Prerequisites

Make sure you have installed:

| Requirement | Minimum Version | Check Installation |
|-------------|-----------------|-------------------|
| Docker | 20.10+ | `docker --version` |
| Docker Compose | 2.0+ | `docker compose version` |
| Git | 2.0+ | `git --version` |

## Step 1: Clone the Repository

```bash
git clone https://github.com/in2workspace/eudistack.git
cd eudistack
```

## Step 2: Configure Environment Variables

Copy the example configuration file:

```bash
cp .env.example .env
```

Edit the `.env` file with your settings:

```env
# Basic configuration
DOMAIN=localhost
PROTOCOL=http

# Ports
ISSUER_PORT=8081
VERIFIER_PORT=8082

# Database
DB_HOST=postgres
DB_PORT=5432
DB_NAME=eudistack
DB_USER=eudistack
DB_PASSWORD=your_secure_password
```

## Step 3: Start the Services

```bash
docker compose up -d
```

Verify that all services are running:

```bash
docker compose ps
```

You should see something similar to:

```
NAME                  STATUS
eudistack-issuer      running
eudistack-verifier    running
eudistack-postgres    running
```

## Step 4: Verify the Installation

### Verify Issuer Service

```bash
curl http://localhost:8081/health
```

Expected response:

```json
{
  "status": "UP",
  "version": "1.0.0"
}
```

### Verify Verifier Service

```bash
curl http://localhost:8082/health
```

## Step 5: First Credential

Issue your first test credential:

=== "cURL"

    ```bash
    curl -X POST http://localhost:8081/api/v1/credentials/offer \
      -H "Content-Type: application/json" \
      -d '{
        "credential_type": "VerifiableId",
        "claims": {
          "given_name": "John",
          "family_name": "Smith",
          "birth_date": "1990-01-15"
        }
      }'
    ```

=== "Python"

    ```python
    import requests

    response = requests.post(
        "http://localhost:8081/api/v1/credentials/offer",
        json={
            "credential_type": "VerifiableId",
            "claims": {
                "given_name": "John",
                "family_name": "Smith",
                "birth_date": "1990-01-15"
            }
        }
    )
    print(response.json())
    ```

=== "JavaScript"

    ```javascript
    const response = await fetch('http://localhost:8081/api/v1/credentials/offer', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        credential_type: 'VerifiableId',
        claims: {
          given_name: 'John',
          family_name: 'Smith',
          birth_date: '1990-01-15'
        }
      })
    });
    const data = await response.json();
    console.log(data);
    ```

## Next Steps

!!! success "Congratulations!"
    You have completed the initial EUDIStack setup.

Now you can:

- [:material-cog: Configure advanced options](configuracion.md)
- [:material-shield-key: Implement authentication flows](autenticacion.md)
- [:material-api: Explore the full API](../referencia-api/index.md)

## Common Issues

??? question "Services won't start"
    Verify that ports are not in use:
    ```bash
    lsof -i :8081
    lsof -i :8082
    ```

??? question "Database connection error"
    Make sure the PostgreSQL container is running:
    ```bash
    docker compose logs postgres
    ```

??? question "Need more help"
    - Check the logs: `docker compose logs -f`
    - [Open an issue on GitHub](https://github.com/in2workspace/eudistack/issues)
