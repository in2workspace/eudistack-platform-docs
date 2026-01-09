---
title: Flows
description: Workflows and operation sequences in EUDIStack
---

# Flows

This page documents the main workflows of the EUDIStack system.

## Credential Issuance Flow

The complete flow for issuing a verifiable credential.

```mermaid
sequenceDiagram
    autonumber
    participant U as User
    participant IA as Issuing Authority
    participant IS as Issuer Service
    participant W as EUDI Wallet
    participant DB as Database

    U->>IA: Request credential
    IA->>IA: Verify eligibility
    IA->>IS: POST /credentials/offer
    IS->>DB: Save pending offer
    IS-->>IA: Credential Offer URI

    IA-->>U: Show QR/Link
    U->>W: Scan QR
    W->>IS: GET /.well-known/openid-credential-issuer
    IS-->>W: Issuer Metadata

    W->>IS: POST /token (Pre-authorized code)
    IS-->>W: Access Token

    W->>IS: POST /credential
    IS->>IS: Generate and sign VC
    IS->>DB: Save issued credential
    IS-->>W: Verifiable Credential

    W->>W: Store in wallet
    W-->>U: Credential received
```

### Detailed Steps

1. **Initial request**: User requests a credential from the issuing authority
2. **Verification**: Authority verifies user eligibility
3. **Offer creation**: A credential offer is generated with the data
4. **Presentation**: User sees a QR or link to accept
5. **Scanning**: Wallet scans and gets the offer
6. **Metadata**: Wallet obtains issuer configuration
7. **Token**: Wallet obtains an access token
8. **Issuance**: Wallet requests the credential
9. **Signing**: Service generates and signs the credential
10. **Storage**: Credential is stored in the wallet

---

## Credential Verification Flow

The complete flow for verifying a presentation.

```mermaid
sequenceDiagram
    autonumber
    participant U as User
    participant W as EUDI Wallet
    participant RP as Relying Party
    participant VS as Verifier Service
    participant TL as Trusted List

    RP->>VS: POST /presentations/request
    VS-->>RP: Authorization Request URI

    RP-->>U: Show QR/Link
    U->>W: Scan QR
    W->>VS: GET Authorization Request

    VS-->>W: Presentation Definition
    W->>U: Show required credentials
    U->>W: Authorize presentation

    W->>W: Generate VP with selected claims
    W->>VS: POST /presentations/response

    VS->>VS: Verify VP signature
    VS->>VS: Verify VC signatures
    VS->>TL: Verify issuer in Trusted List
    VS->>VS: Verify non-revocation

    VS-->>RP: Webhook with result
    RP-->>U: Access granted
```

### Detailed Steps

1. **Request**: RP requests a presentation from verifier
2. **Authorization URI**: URL/QR is generated for wallet
3. **User presentation**: RP shows the QR
4. **Scanning**: User scans with their wallet
5. **Definition**: Wallet gets what credentials are required
6. **Consent**: User sees and authorizes data to share
7. **VP Generation**: Wallet generates signed presentation
8. **Submission**: Presentation is sent to verifier
9. **Signature verification**: All signatures are validated
10. **Issuer verification**: Issuer is checked against trusted list
11. **Revocation check**: Credential is checked for revocation
12. **Result**: RP receives confirmation

---

## Revocation Flow

Process for revoking an issued credential.

```mermaid
sequenceDiagram
    autonumber
    participant IA as Issuing Authority
    participant IS as Issuer Service
    participant DB as Database
    participant SL as Status List

    IA->>IS: DELETE /credentials/{id}
    IS->>DB: Get credential
    IS->>IS: Validate authorization

    IS->>DB: Mark as revoked
    IS->>SL: Update Status List
    IS->>IS: Log in audit log

    IS-->>IA: Revocation confirmation
```

---

## Same-Device Authentication Flow

When wallet and service are on the same device.

```mermaid
sequenceDiagram
    autonumber
    participant U as User
    participant APP as Mobile App
    participant W as EUDI Wallet
    participant VS as Verifier Service

    U->>APP: Start login
    APP->>VS: POST /presentations/request
    VS-->>APP: authorization_url

    APP->>W: Deep link to wallet
    W->>U: Request authorization
    U->>W: Authorize

    W->>VS: POST /presentations/response
    VS-->>W: redirect_uri with code

    W->>APP: Redirect with code
    APP->>VS: Verify code
    VS-->>APP: Verified claims

    APP-->>U: Login successful
```

---

## Cross-Device Authentication Flow

When wallet is on a different device (e.g., QR on web).

```mermaid
sequenceDiagram
    autonumber
    participant U as User
    participant WEB as Web Browser
    participant W as EUDI Wallet (Mobile)
    participant VS as Verifier Service

    U->>WEB: Start login
    WEB->>VS: POST /presentations/request
    VS-->>WEB: request_id + QR data

    WEB->>WEB: Show QR
    WEB->>VS: WebSocket/Polling (wait)

    U->>W: Scan QR
    W->>VS: GET Authorization Request
    VS-->>W: Presentation Definition

    W->>U: Request authorization
    U->>W: Authorize
    W->>VS: POST /presentations/response

    VS->>VS: Verify presentation
    VS-->>WEB: WebSocket: completed + claims

    WEB-->>U: Login successful
```

---

## Backup and Restore Flow

Wallet backup and recovery process.

```mermaid
sequenceDiagram
    autonumber
    participant U as User
    participant W as EUDI Wallet
    participant WB as Wallet Backend
    participant VAULT as Vault

    Note over U,VAULT: Backup
    U->>W: Request backup
    W->>W: Encrypt credentials with PIN
    W->>WB: POST /wallet/backup
    WB->>VAULT: Store encrypted backup
    WB-->>W: Backup ID
    W-->>U: Backup completed

    Note over U,VAULT: Restore
    U->>W: Start restore
    W->>U: Request PIN
    U->>W: Enter PIN
    W->>WB: POST /wallet/restore
    WB->>VAULT: Get encrypted backup
    WB-->>W: Encrypted backup
    W->>W: Decrypt with PIN
    W-->>U: Wallet restored
```

---

## Error Handling

### Common Errors and Responses

| Scenario | Code | Action |
|----------|------|--------|
| Expired token | 401 | Renew token |
| Revoked credential | 403 | Inform user |
| Untrusted issuer | 403 | Reject presentation |
| Invalid signature | 400 | Reject credential |
| Timeout | 408 | Retry |

### Error Handling Diagram

```mermaid
flowchart TD
    A[Receive Request] --> B{Valid token?}
    B -->|No| C[401 Unauthorized]
    B -->|Yes| D{Has permissions?}
    D -->|No| E[403 Forbidden]
    D -->|Yes| F{Valid data?}
    F -->|No| G[400 Bad Request]
    F -->|Yes| H{Process}
    H -->|Internal error| I[500 Internal Error]
    H -->|Success| J[200 OK]
```

## Additional Resources

- [:material-home: Back to home](../index.md)
- [:material-api: API Reference](../referencia-api/index.md)
- [:material-certificate: Credential model](../modelo-credenciales/index.md)
