---
title: Configuracion
description: Opciones de configuracion avanzadas para EUDIStack
---

# Configuracion

Esta guia detalla todas las opciones de configuracion disponibles en EUDIStack.

## Variables de entorno

### Configuracion general

| Variable | Descripcion | Valor por defecto |
|----------|-------------|-------------------|
| `DOMAIN` | Dominio del servidor | `localhost` |
| `PROTOCOL` | Protocolo HTTP/HTTPS | `http` |
| `LOG_LEVEL` | Nivel de logging | `INFO` |
| `ENVIRONMENT` | Entorno de ejecucion | `development` |

### Issuer Service

| Variable | Descripcion | Valor por defecto |
|----------|-------------|-------------------|
| `ISSUER_PORT` | Puerto del servicio | `8081` |
| `ISSUER_DID` | DID del emisor | - |
| `ISSUER_KEY_PATH` | Ruta a la clave privada | `/keys/issuer.pem` |

### Verifier Service

| Variable | Descripcion | Valor por defecto |
|----------|-------------|-------------------|
| `VERIFIER_PORT` | Puerto del servicio | `8082` |
| `VERIFIER_DID` | DID del verificador | - |
| `TRUSTED_ISSUERS` | Lista de emisores confiables | `[]` |

### Base de datos

| Variable | Descripcion | Valor por defecto |
|----------|-------------|-------------------|
| `DB_HOST` | Host de PostgreSQL | `postgres` |
| `DB_PORT` | Puerto de PostgreSQL | `5432` |
| `DB_NAME` | Nombre de la base de datos | `eudistack` |
| `DB_USER` | Usuario de la base de datos | `eudistack` |
| `DB_PASSWORD` | Contrasena de la base de datos | - |

## Archivo de configuracion

Ademas de variables de entorno, puedes usar un archivo `config.yaml`:

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

## Configuracion de credenciales

### Tipos de credencial soportados

Define los tipos de credencial que tu instancia puede emitir:

```yaml
credentials:
  types:
    VerifiableId:
      display_name: "Identidad Verificable"
      description: "Credencial de identidad personal"
      claims:
        - given_name
        - family_name
        - birth_date
        - nationality
      validity_period: 365  # dias

    VerifiableDiploma:
      display_name: "Diploma Verificable"
      description: "Credencial academica"
      claims:
        - degree_name
        - institution
        - graduation_date
        - student_id
      validity_period: -1  # sin expiracion
```

### Politicas de verificacion

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

## Configuracion de seguridad

### TLS/SSL

Para produccion, configura TLS:

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

## Ejemplos de configuracion

### Desarrollo local

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

### Produccion

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

## Siguiente paso

[:material-shield-key: Configurar autenticacion](autenticacion.md){ .md-button }
