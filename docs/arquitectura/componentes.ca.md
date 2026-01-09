---
title: Components
description: Descripcio detallada dels components d'EUDIStack
---

# Components

Aquesta pagina descriu en detall cada component del sistema EUDIStack.

## Issuer Service

Servei responsable de l'emissio de credencials verificables.

### Responsabilitats

- Rebre sollicituds d'emissio de credencials
- Validar les dades d'entrada
- Generar i signar credencials
- Gestionar el cicle de vida de les credencials
- Publicar estat de revocacio

### Endpoints principals

| Endpoint | Metode | Descripcio |
|----------|--------|------------|
| `/credentials/offer` | POST | Crear oferta de credencial |
| `/credentials/{id}` | GET | Obtenir credencial |
| `/credentials/{id}` | DELETE | Revocar credencial |
| `/.well-known/openid-credential-issuer` | GET | Metadata de l'emissor |

### Dependencies

```mermaid
flowchart LR
    IS[Issuer Service]
    DB[(PostgreSQL)]
    VAULT[Vault]
    HSM[HSM]
    TSP[Trust Service]

    IS --> DB
    IS --> VAULT
    IS --> HSM
    IS --> TSP
```

---

## Verifier Service

Servei responsable de la verificacio de presentacions de credencials.

### Responsabilitats

- Generar sollicituds de presentacio
- Rebre i validar presentacions
- Verificar signatures criptografiques
- Comprovar estat de revocacio
- Validar contra llista d'emissors confiables

### Endpoints principals

| Endpoint | Metode | Descripcio |
|----------|--------|------------|
| `/presentations/request` | POST | Crear sollicitud |
| `/presentations/verify` | POST | Verificar presentacio |
| `/presentations/requests/{id}` | GET | Estat de sollicitud |

### Dependencies

```mermaid
flowchart LR
    VS[Verifier Service]
    DB[(PostgreSQL)]
    CACHE[(Redis)]
    TL[Trusted List]

    VS --> DB
    VS --> CACHE
    VS --> TL
```

---

## Wallet Backend Service

Servei de backend per a l'aplicacio wallet.

### Responsabilitats

- Sincronitzacio de credencials entre dispositius
- Backup xifrat de credencials
- Gestio de notificacions push
- Recuperacio de compte

### Endpoints principals

| Endpoint | Metode | Descripcio |
|----------|--------|------------|
| `/wallet/sync` | POST | Sincronitzar estat |
| `/wallet/backup` | POST | Crear backup |
| `/wallet/restore` | POST | Restaurar backup |
| `/wallet/devices` | GET | Llistar dispositius |

---

## Auth Service

Servei d'autenticacio i autoritzacio.

### Responsabilitats

- Autenticacio OAuth 2.0
- Emissio de tokens JWT
- Gestio de sessions
- Validacio de tokens

### Fluxos suportats

| Flux | Descripcio |
|------|------------|
| Client Credentials | Aplicacions servidor |
| Authorization Code + PKCE | Aplicacions client |
| Refresh Token | Renovacio de tokens |

### Endpoints principals

| Endpoint | Metode | Descripcio |
|----------|--------|------------|
| `/oauth/token` | POST | Obtenir token |
| `/oauth/authorize` | GET | Inici d'autoritzacio |
| `/oauth/revoke` | POST | Revocar token |
| `/.well-known/openid-configuration` | GET | Metadata OIDC |

---

## API Gateway

Punt d'entrada unificat per a totes les peticions.

### Responsabilitats

- Routing de peticions
- Rate limiting
- Autenticacio de peticions
- Logging i metriques
- CORS

### Configuracio de rutes

```yaml
routes:
  - path: /api/v1/credentials/**
    service: issuer-service
    rate_limit: 100/minute

  - path: /api/v1/presentations/**
    service: verifier-service
    rate_limit: 200/minute

  - path: /api/v1/wallet/**
    service: wallet-backend
    rate_limit: 50/minute
```

---

## Base de dades (PostgreSQL)

Emmagatzematge persistent principal.

### Esquema principal

```mermaid
erDiagram
    CREDENTIAL {
        uuid id PK
        string type
        string issuer_did
        string subject_did
        jsonb claims
        timestamp issued_at
        timestamp expires_at
        string status
    }

    PRESENTATION_REQUEST {
        uuid id PK
        jsonb definition
        string callback_url
        string status
        timestamp created_at
        timestamp completed_at
    }

    REVOCATION_LIST {
        uuid id PK
        string issuer_did
        bytea bitstring
        timestamp updated_at
    }

    AUDIT_LOG {
        uuid id PK
        string action
        string actor
        jsonb details
        timestamp created_at
    }
```

---

## Cache (Redis)

Cache distribuida per millorar rendiment.

### Casos d'us

| Clau | TTL | Descripcio |
|------|-----|------------|
| `session:{id}` | 1h | Sessions d'usuari |
| `token:{jti}` | 24h | Tokens revocats |
| `rate:{ip}` | 1min | Comptadors rate limit |
| `issuer:{did}` | 1h | Metadata d'emissors |

---

## Vault

Gestio de secrets i claus criptografiques.

### Secrets emmagatzemats

| Path | Descripcio |
|------|------------|
| `secret/issuer/keys` | Claus de signatura de l'emissor |
| `secret/database` | Credencials de BD |
| `secret/api-keys` | API keys de serveis |
| `transit/issuer` | Motor de xifrat |

## Seguent pas

[:material-arrow-decision: Veure fluxos de treball](flujos.md){ .md-button }
