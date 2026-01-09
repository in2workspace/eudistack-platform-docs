---
title: Fluxos
description: Fluxos de treball i sequencies d'operacio a EUDIStack
---

# Fluxos

Aquesta pagina documenta els principals fluxos de treball del sistema EUDIStack.

## Flux d'emissio de credencials

El flux complet d'emissio d'una credencial verificable.

```mermaid
sequenceDiagram
    autonumber
    participant U as Usuari
    participant IA as Issuing Authority
    participant IS as Issuer Service
    participant W as EUDI Wallet
    participant DB as Database

    U->>IA: Sollicita credencial
    IA->>IA: Verifica elegibilitat
    IA->>IS: POST /credentials/offer
    IS->>DB: Guarda oferta pendent
    IS-->>IA: Credential Offer URI

    IA-->>U: Mostra QR/Link
    U->>W: Escaneja QR
    W->>IS: GET /.well-known/openid-credential-issuer
    IS-->>W: Issuer Metadata

    W->>IS: POST /token (Pre-authorized code)
    IS-->>W: Access Token

    W->>IS: POST /credential
    IS->>IS: Genera i signa VC
    IS->>DB: Guarda credencial emesa
    IS-->>W: Verifiable Credential

    W->>W: Emmagatzema al wallet
    W-->>U: Credencial rebuda
```

### Passos detallats

1. **Sollicitud inicial**: L'usuari sollicita una credencial a l'autoritat emissora
2. **Verificacio**: L'autoritat verifica que l'usuari es elegible
3. **Creacio d'oferta**: Es genera una oferta de credencial amb les dades
4. **Presentacio**: L'usuari veu un QR o link per acceptar
5. **Escaneig**: El wallet escaneja i obte l'oferta
6. **Metadata**: El wallet obte la configuracio de l'emissor
7. **Token**: El wallet obte un token d'acces
8. **Emissio**: El wallet sollicita la credencial
9. **Signatura**: El servei genera i signa la credencial
10. **Emmagatzematge**: La credencial es guarda al wallet

---

## Flux de verificacio de credencials

El flux complet de verificacio d'una presentacio.

```mermaid
sequenceDiagram
    autonumber
    participant U as Usuari
    participant W as EUDI Wallet
    participant RP as Relying Party
    participant VS as Verifier Service
    participant TL as Trusted List

    RP->>VS: POST /presentations/request
    VS-->>RP: Authorization Request URI

    RP-->>U: Mostra QR/Link
    U->>W: Escaneja QR
    W->>VS: GET Authorization Request

    VS-->>W: Presentation Definition
    W->>U: Mostra credencials requerides
    U->>W: Autoritza presentacio

    W->>W: Genera VP amb claims seleccionats
    W->>VS: POST /presentations/response

    VS->>VS: Verifica signatura VP
    VS->>VS: Verifica signatures VCs
    VS->>TL: Verifica emissor a Trusted List
    VS->>VS: Verifica no revocacio

    VS-->>RP: Webhook amb resultat
    RP-->>U: Acces concedit
```

---

## Flux de revocacio

Proces de revocacio d'una credencial emesa.

```mermaid
sequenceDiagram
    autonumber
    participant IA as Issuing Authority
    participant IS as Issuer Service
    participant DB as Database
    participant SL as Status List

    IA->>IS: DELETE /credentials/{id}
    IS->>DB: Obte credencial
    IS->>IS: Valida autoritzacio

    IS->>DB: Marca com revocada
    IS->>SL: Actualitza Status List
    IS->>IS: Registra a audit log

    IS-->>IA: Confirmacio de revocacio
```

---

## Flux d'autenticacio Same-Device

Quan el wallet i el servei estan al mateix dispositiu.

```mermaid
sequenceDiagram
    autonumber
    participant U as Usuari
    participant APP as App Mobil
    participant W as EUDI Wallet
    participant VS as Verifier Service

    U->>APP: Inicia login
    APP->>VS: POST /presentations/request
    VS-->>APP: authorization_url

    APP->>W: Deep link a wallet
    W->>U: Sollicita autoritzacio
    U->>W: Autoritza

    W->>VS: POST /presentations/response
    VS-->>W: redirect_uri amb code

    W->>APP: Redirect amb code
    APP->>VS: Verifica code
    VS-->>APP: Claims verificats

    APP-->>U: Login exitós
```

---

## Flux d'autenticacio Cross-Device

Quan el wallet esta a un dispositiu diferent (ex: QR a web).

```mermaid
sequenceDiagram
    autonumber
    participant U as Usuari
    participant WEB as Web Browser
    participant W as EUDI Wallet (Mobil)
    participant VS as Verifier Service

    U->>WEB: Inicia login
    WEB->>VS: POST /presentations/request
    VS-->>WEB: request_id + QR data

    WEB->>WEB: Mostra QR
    WEB->>VS: WebSocket/Polling (espera)

    U->>W: Escaneja QR
    W->>VS: GET Authorization Request
    VS-->>W: Presentation Definition

    W->>U: Sollicita autoritzacio
    U->>W: Autoritza
    W->>VS: POST /presentations/response

    VS->>VS: Verifica presentacio
    VS-->>WEB: WebSocket: completat + claims

    WEB-->>U: Login exitós
```

---

## Flux de backup i restauracio

Proces de backup i recuperacio del wallet.

```mermaid
sequenceDiagram
    autonumber
    participant U as Usuari
    participant W as EUDI Wallet
    participant WB as Wallet Backend
    participant VAULT as Vault

    Note over U,VAULT: Backup
    U->>W: Sollicita backup
    W->>W: Xifra credencials amb PIN
    W->>WB: POST /wallet/backup
    WB->>VAULT: Emmagatzema backup xifrat
    WB-->>W: Backup ID
    W-->>U: Backup completat

    Note over U,VAULT: Restauracio
    U->>W: Inicia restauracio
    W->>U: Sollicita PIN
    U->>W: Introdueix PIN
    W->>WB: POST /wallet/restore
    WB->>VAULT: Obte backup xifrat
    WB-->>W: Backup xifrat
    W->>W: Desxifra amb PIN
    W-->>U: Wallet restaurat
```

---

## Gestio d'errors

### Errors comuns i respostes

| Escenari | Codi | Accio |
|----------|------|-------|
| Token expirat | 401 | Renovar token |
| Credencial revocada | 403 | Informar a l'usuari |
| Emissor no confiable | 403 | Rebutjar presentacio |
| Signatura invalida | 400 | Rebutjar credencial |
| Timeout | 408 | Reintentar |

### Diagrama de gestio d'errors

```mermaid
flowchart TD
    A[Rep Peticio] --> B{Token valid?}
    B -->|No| C[401 Unauthorized]
    B -->|Si| D{Te permisos?}
    D -->|No| E[403 Forbidden]
    D -->|Si| F{Dades valides?}
    F -->|No| G[400 Bad Request]
    F -->|Si| H{Processar}
    H -->|Error intern| I[500 Internal Error]
    H -->|Exit| J[200 OK]
```

## Recursos addicionals

- [:material-home: Tornar a l'inici](../index.md)
- [:material-api: Referencia API](../referencia-api/index.md)
- [:material-certificate: Model de credencials](../modelo-credenciales/index.md)
