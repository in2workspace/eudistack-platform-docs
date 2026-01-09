---
title: Configuracio
description: Opcions de configuracio avancades per a EUDIStack
---

# Configuracio

Aquesta guia detalla totes les opcions de configuracio disponibles a EUDIStack.

## Variables d'entorn

### Configuracio general

| Variable | Descripcio | Valor per defecte |
|----------|------------|-------------------|
| `DOMAIN` | Domini del servidor | `localhost` |
| `PROTOCOL` | Protocol HTTP/HTTPS | `http` |
| `LOG_LEVEL` | Nivell de logging | `INFO` |
| `ENVIRONMENT` | Entorn d'execucio | `development` |

### Issuer Service

| Variable | Descripcio | Valor per defecte |
|----------|------------|-------------------|
| `ISSUER_PORT` | Port del servei | `8081` |
| `ISSUER_DID` | DID de l'emissor | - |
| `ISSUER_KEY_PATH` | Ruta a la clau privada | `/keys/issuer.pem` |

### Verifier Service

| Variable | Descripcio | Valor per defecte |
|----------|------------|-------------------|
| `VERIFIER_PORT` | Port del servei | `8082` |
| `VERIFIER_DID` | DID del verificador | - |
| `TRUSTED_ISSUERS` | Llista d'emissors confiables | `[]` |

### Base de dades

| Variable | Descripcio | Valor per defecte |
|----------|------------|-------------------|
| `DB_HOST` | Host de PostgreSQL | `postgres` |
| `DB_PORT` | Port de PostgreSQL | `5432` |
| `DB_NAME` | Nom de la base de dades | `eudistack` |
| `DB_USER` | Usuari de la base de dades | `eudistack` |
| `DB_PASSWORD` | Contrasenya de la base de dades | - |

## Arxiu de configuracio

A mes de variables d'entorn, pots usar un arxiu `config.yaml`:

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

## Configuracio de credencials

### Tipus de credencial suportats

Defineix els tipus de credencial que la teva instancia pot emetre:

```yaml
credentials:
  types:
    VerifiableId:
      display_name: "Identitat Verificable"
      description: "Credencial d'identitat personal"
      claims:
        - given_name
        - family_name
        - birth_date
        - nationality
      validity_period: 365  # dies

    VerifiableDiploma:
      display_name: "Diploma Verificable"
      description: "Credencial academica"
      claims:
        - degree_name
        - institution
        - graduation_date
        - student_id
      validity_period: -1  # sense expiracio
```

### Politiques de verificacio

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

## Configuracio de seguretat

### TLS/SSL

Per a produccio, configura TLS:

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

## Exemples de configuracio

### Desenvolupament local

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

### Produccio

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

## Seguent pas

[:material-shield-key: Configurar autenticacio](autenticacion.md){ .md-button }
