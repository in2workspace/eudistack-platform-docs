---
title: Configuration
description: Advanced configuration options for EUDIStack
---

# Configuration

This guide details all available configuration options in EUDIStack.

## Environment Variables

### General Configuration

| Variable | Description | Default Value |
|----------|-------------|---------------|
| `DOMAIN` | Server domain | `localhost` |
| `PROTOCOL` | HTTP/HTTPS protocol | `http` |
| `LOG_LEVEL` | Logging level | `INFO` |
| `ENVIRONMENT` | Execution environment | `development` |

### Issuer Service

| Variable | Description | Default Value |
|----------|-------------|---------------|
| `ISSUER_PORT` | Service port | `8081` |
| `ISSUER_DID` | Issuer DID | - |
| `ISSUER_KEY_PATH` | Path to private key | `/keys/issuer.pem` |

### Verifier Service

| Variable | Description | Default Value |
|----------|-------------|---------------|
| `VERIFIER_PORT` | Service port | `8082` |
| `VERIFIER_DID` | Verifier DID | - |
| `TRUSTED_ISSUERS` | List of trusted issuers | `[]` |

### Database

| Variable | Description | Default Value |
|----------|-------------|---------------|
| `DB_HOST` | PostgreSQL host | `postgres` |
| `DB_PORT` | PostgreSQL port | `5432` |
| `DB_NAME` | Database name | `eudistack` |
| `DB_USER` | Database user | `eudistack` |
| `DB_PASSWORD` | Database password | - |

## Configuration File

In addition to environment variables, you can use a `config.yaml` file:

```yaml
# config.yaml
server:
  domain: example.com
  protocol: https

issuer:
  port: 8081
  did: did:web:example.com:issuer
  credentials:
    supported:
      - VerifiableId
      - VerifiableDiploma
      - VerifiableAttestation

verifier:
  port: 8082
  did: did:web:example.com:verifier
  policies:
    - name: age_verification
      required_claims:
        - birth_date
    - name: identity_verification
      required_claims:
        - given_name
        - family_name
        - birth_date

database:
  host: postgres
  port: 5432
  name: eudistack
  ssl_mode: require

logging:
  level: INFO
  format: json
```

## Credential Configuration

### Supported Credential Types

Define the credential types your instance can issue:

```yaml
credentials:
  types:
    VerifiableId:
      display_name: "Verifiable ID"
      description: "Personal identity credential"
      claims:
        - given_name
        - family_name
        - birth_date
        - nationality
      validity_period: 365  # days

    VerifiableDiploma:
      display_name: "Verifiable Diploma"
      description: "Academic credential"
      claims:
        - degree_name
        - institution
        - graduation_date
        - student_id
      validity_period: -1  # no expiration
```

### Verification Policies

```yaml
verification:
  policies:
    strict:
      require_signature: true
      check_revocation: true
      max_age_seconds: 300

    relaxed:
      require_signature: true
      check_revocation: false
      max_age_seconds: 3600
```

## Security Configuration

### TLS/SSL

For production, configure TLS:

```yaml
tls:
  enabled: true
  cert_path: /certs/server.crt
  key_path: /certs/server.key
  ca_path: /certs/ca.crt
```

### CORS

```yaml
cors:
  allowed_origins:
    - https://app.example.com
    - https://admin.example.com
  allowed_methods:
    - GET
    - POST
    - PUT
    - DELETE
  allowed_headers:
    - Authorization
    - Content-Type
  max_age: 86400
```

## Configuration Examples

### Local Development

```env
DOMAIN=localhost
PROTOCOL=http
LOG_LEVEL=DEBUG
ENVIRONMENT=development
ISSUER_PORT=8081
VERIFIER_PORT=8082
DB_HOST=localhost
DB_PORT=5432
DB_NAME=eudistack_dev
DB_USER=developer
DB_PASSWORD=dev_password
```

### Production

```env
DOMAIN=api.eudistack.example.com
PROTOCOL=https
LOG_LEVEL=INFO
ENVIRONMENT=production
ISSUER_PORT=8081
VERIFIER_PORT=8082
DB_HOST=postgres.internal
DB_PORT=5432
DB_NAME=eudistack_prod
DB_USER=eudistack_prod
DB_PASSWORD=${DB_PASSWORD_SECRET}
```

## Next Step

[:material-shield-key: Configure authentication](autenticacion.md){ .md-button }
