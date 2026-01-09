---
title: Visio General
description: Vista d'alt nivell de l'arquitectura d'EUDIStack
---

# Visio General

Aquesta pagina proporciona una vista d'alt nivell de l'arquitectura d'EUDIStack i com s'alinea amb l'ecosistema EUDI Wallet europeu.

## Rols de l'ecosistema

EUDIStack implementa els tres rols principals definits a l'ARF:

```mermaid
flowchart LR
    subgraph Issuer["Emissor (Issuer)"]
        direction TB
        I1[Autoritat emissora]
        I2[Genera credencials]
        I3[Signa amb clau privada]
    end

    subgraph Holder["Titular (Holder)"]
        direction TB
        H1[Usuari amb wallet]
        H2[Emmagatzema credencials]
        H3[Controla presentacio]
    end

    subgraph Verifier["Verificador (Verifier)"]
        direction TB
        V1[Servei/aplicacio]
        V2[Sollicita credencials]
        V3[Valida presentacions]
    end

    Issuer -->|Emet VC| Holder
    Holder -->|Presenta VP| Verifier
```

### Emissor (Issuer)

Entitat autoritzada per emetre credencials verificables:

- **Governs**: Documents d'identitat (PID)
- **Universitats**: Titols academics
- **Empreses**: Certificats professionals
- **Organismes publics**: Atestacions

### Titular (Holder)

Usuari que posseeix i controla les seves credencials:

- Emmagatzema credencials al seu wallet
- Decideix quins atributs compartir
- Autoritza cada presentacio

### Verificador (Verifier)

Entitat que sollicita i verifica credencials:

- Serveis online (Relying Parties)
- Aplicacions mobils
- Punts de control fisics

## Capes de l'arquitectura

```mermaid
flowchart TB
    subgraph Presentation["Capa de Presentacio"]
        WA[Wallet App<br/>React Native]
        AP[Admin Portal<br/>React]
        SDK[SDK Integracio]
    end

    subgraph API["Capa d'API"]
        GW[API Gateway]
        AUTH[Auth Service]
    end

    subgraph Business["Capa de Negoci"]
        IS[Issuer Service]
        VS[Verifier Service]
        WBS[Wallet Backend Service]
    end

    subgraph Data["Capa de Dades"]
        DB[(PostgreSQL)]
        CACHE[(Redis)]
        VAULT[Vault]
    end

    subgraph External["Serveis Externs"]
        TSP[Trust Service Provider]
        TL[Trusted List]
        HSM[HSM]
    end

    WA --> GW
    AP --> GW
    SDK --> GW

    GW --> AUTH
    AUTH --> IS
    AUTH --> VS
    AUTH --> WBS

    IS --> DB
    VS --> DB
    WBS --> DB

    IS --> CACHE
    VS --> CACHE

    IS --> VAULT
    VS --> VAULT
    WBS --> VAULT

    IS --> TSP
    IS --> HSM
    VS --> TL
```

### Capa de presentacio

- **Wallet App**: Aplicacio mobil per a usuaris finals
- **Admin Portal**: Panell d'administracio per a emissors
- **SDK**: Kit de desenvolupament per a integradors

### Capa d'API

- **API Gateway**: Punt d'entrada unificat, routing, rate limiting
- **Auth Service**: Autenticacio OAuth 2.0 / OpenID Connect

### Capa de negoci

- **Issuer Service**: Emissio i gestio de credencials
- **Verifier Service**: Verificacio de presentacions
- **Wallet Backend**: Sincronitzacio i backup del wallet

### Capa de dades

- **PostgreSQL**: Emmagatzematge persistent
- **Redis**: Cache i sessions
- **Vault**: Gestio de secrets i claus

## Model de desplegament

### Docker Compose (Desenvolupament)

```yaml
services:
  issuer:
    image: eudistack/issuer:latest
    ports:
      - "8081:8080"
    environment:
      - DB_HOST=postgres
      - VAULT_ADDR=http://vault:8200

  verifier:
    image: eudistack/verifier:latest
    ports:
      - "8082:8080"
    environment:
      - DB_HOST=postgres
      - VAULT_ADDR=http://vault:8200

  postgres:
    image: postgres:15
    volumes:
      - pgdata:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine

  vault:
    image: vault:1.15
```

### Kubernetes (Produccio)

```mermaid
flowchart TB
    subgraph Ingress
        ING[Ingress Controller]
    end

    subgraph Services
        subgraph Issuer
            IS1[Issuer Pod 1]
            IS2[Issuer Pod 2]
            IS3[Issuer Pod 3]
        end

        subgraph Verifier
            VS1[Verifier Pod 1]
            VS2[Verifier Pod 2]
            VS3[Verifier Pod 3]
        end
    end

    subgraph Data
        PG[(PostgreSQL<br/>Primary)]
        PGR[(PostgreSQL<br/>Replica)]
        RD[(Redis Cluster)]
    end

    ING --> IS1
    ING --> IS2
    ING --> IS3
    ING --> VS1
    ING --> VS2
    ING --> VS3

    IS1 --> PG
    IS2 --> PG
    IS3 --> PG
    VS1 --> PG
    VS2 --> PG
    VS3 --> PG

    PG --> PGR

    IS1 --> RD
    VS1 --> RD
```

## Seguretat

### Seguretat en transit

- TLS 1.3 obligatori per a totes les comunicacions
- Certificats gestionats automaticament (Let's Encrypt / cert-manager)
- mTLS entre serveis interns

### Seguretat en repos

- Xifrat de base de dades (AES-256)
- Claus criptografiques en HSM o Vault
- Backups xifrats

### Autenticacio i autoritzacio

```mermaid
sequenceDiagram
    participant C as Client
    participant GW as API Gateway
    participant AUTH as Auth Service
    participant SVC as Service

    C->>GW: Request + Token
    GW->>AUTH: Validate Token
    AUTH-->>GW: Token Valid + Scopes
    GW->>SVC: Request + User Context
    SVC-->>GW: Response
    GW-->>C: Response
```

## Alta disponibilitat

| Component | Estrategia |
|-----------|------------|
| **API Gateway** | Multiples repliques + Load Balancer |
| **Serveis** | 3+ repliques per servei |
| **PostgreSQL** | Primary + Read Replicas |
| **Redis** | Cluster mode |
| **Vault** | HA mode amb Raft |

## Seguent pas

[:material-puzzle: Veure components detallats](componentes.md){ .md-button }
