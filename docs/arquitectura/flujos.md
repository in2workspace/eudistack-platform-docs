---
title: Flujos
description: Flujos de trabajo y secuencias de operacion en EUDIStack
---

# Flujos

Esta pagina documenta los principales flujos de trabajo del sistema EUDIStack.

## Flujo de emision de credenciales

El flujo completo de emision de una credencial verificable.

```mermaid
sequenceDiagram
    autonumber
    participant U as Usuario
    participant IA as Issuing Authority
    participant IS as Issuer Service
    participant W as EUDI Wallet
    participant DB as Database

    U->>IA: Solicita credencial
    IA->>IA: Verifica elegibilidad
    IA->>IS: POST /credentials/offer
    IS->>DB: Guarda oferta pendiente
    IS-->>IA: Credential Offer URI

    IA-->>U: Muestra QR/Link
    U->>W: Escanea QR
    W->>IS: GET /.well-known/openid-credential-issuer
    IS-->>W: Issuer Metadata

    W->>IS: POST /token (Pre-authorized code)
    IS-->>W: Access Token

    W->>IS: POST /credential
    IS->>IS: Genera y firma VC
    IS->>DB: Guarda credencial emitida
    IS-->>W: Verifiable Credential

    W->>W: Almacena en wallet
    W-->>U: Credencial recibida
```

### Pasos detallados

1. **Solicitud inicial**: El usuario solicita una credencial a la autoridad emisora
2. **Verificacion**: La autoridad verifica que el usuario es elegible
3. **Creacion de oferta**: Se genera una oferta de credencial con los datos
4. **Presentacion**: El usuario ve un QR o link para aceptar
5. **Escaneo**: El wallet escanea y obtiene la oferta
6. **Metadata**: El wallet obtiene la configuracion del emisor
7. **Token**: El wallet obtiene un token de acceso
8. **Emision**: El wallet solicita la credencial
9. **Firma**: El servicio genera y firma la credencial
10. **Almacenamiento**: La credencial se guarda en el wallet

---

## Flujo de verificacion de credenciales

El flujo completo de verificacion de una presentacion.

```mermaid
sequenceDiagram
    autonumber
    participant U as Usuario
    participant W as EUDI Wallet
    participant RP as Relying Party
    participant VS as Verifier Service
    participant TL as Trusted List

    RP->>VS: POST /presentations/request
    VS-->>RP: Authorization Request URI

    RP-->>U: Muestra QR/Link
    U->>W: Escanea QR
    W->>VS: GET Authorization Request

    VS-->>W: Presentation Definition
    W->>U: Muestra credenciales requeridas
    U->>W: Autoriza presentacion

    W->>W: Genera VP con claims seleccionados
    W->>VS: POST /presentations/response

    VS->>VS: Verifica firma VP
    VS->>VS: Verifica firmas VCs
    VS->>TL: Verifica emisor en Trusted List
    VS->>VS: Verifica no revocacion

    VS-->>RP: Webhook con resultado
    RP-->>U: Acceso concedido
```

### Pasos detallados

1. **Solicitud**: El RP solicita una presentacion al verifier
2. **URI de autorizacion**: Se genera la URL/QR para el wallet
3. **Presentacion al usuario**: El RP muestra el QR
4. **Escaneo**: El usuario escanea con su wallet
5. **Definicion**: El wallet obtiene que credenciales se requieren
6. **Consentimiento**: El usuario ve y autoriza los datos a compartir
7. **Generacion VP**: El wallet genera la presentacion firmada
8. **Envio**: La presentacion se envia al verifier
9. **Verificacion de firmas**: Se validan todas las firmas
10. **Verificacion de emisor**: Se comprueba que el emisor es confiable
11. **Verificacion de revocacion**: Se comprueba que no este revocada
12. **Resultado**: El RP recibe la confirmacion

---

## Flujo de revocacion

Proceso de revocacion de una credencial emitida.

```mermaid
sequenceDiagram
    autonumber
    participant IA as Issuing Authority
    participant IS as Issuer Service
    participant DB as Database
    participant SL as Status List

    IA->>IS: DELETE /credentials/{id}
    IS->>DB: Obtiene credencial
    IS->>IS: Valida autorizacion

    IS->>DB: Marca como revocada
    IS->>SL: Actualiza Status List
    IS->>IS: Registra en audit log

    IS-->>IA: Confirmacion de revocacion
```

---

## Flujo de autenticacion Same-Device

Cuando el wallet y el servicio estan en el mismo dispositivo.

```mermaid
sequenceDiagram
    autonumber
    participant U as Usuario
    participant APP as App Movil
    participant W as EUDI Wallet
    participant VS as Verifier Service

    U->>APP: Inicia login
    APP->>VS: POST /presentations/request
    VS-->>APP: authorization_url

    APP->>W: Deep link a wallet
    W->>U: Solicita autorizacion
    U->>W: Autoriza

    W->>VS: POST /presentations/response
    VS-->>W: redirect_uri con code

    W->>APP: Redirect con code
    APP->>VS: Verifica code
    VS-->>APP: Claims verificados

    APP-->>U: Login exitoso
```

---

## Flujo de autenticacion Cross-Device

Cuando el wallet esta en un dispositivo diferente (ej: QR en web).

```mermaid
sequenceDiagram
    autonumber
    participant U as Usuario
    participant WEB as Web Browser
    participant W as EUDI Wallet (Movil)
    participant VS as Verifier Service

    U->>WEB: Inicia login
    WEB->>VS: POST /presentations/request
    VS-->>WEB: request_id + QR data

    WEB->>WEB: Muestra QR
    WEB->>VS: WebSocket/Polling (espera)

    U->>W: Escanea QR
    W->>VS: GET Authorization Request
    VS-->>W: Presentation Definition

    W->>U: Solicita autorizacion
    U->>W: Autoriza
    W->>VS: POST /presentations/response

    VS->>VS: Verifica presentacion
    VS-->>WEB: WebSocket: completado + claims

    WEB-->>U: Login exitoso
```

---

## Flujo de backup y restauracion

Proceso de backup y recuperacion del wallet.

```mermaid
sequenceDiagram
    autonumber
    participant U as Usuario
    participant W as EUDI Wallet
    participant WB as Wallet Backend
    participant VAULT as Vault

    Note over U,VAULT: Backup
    U->>W: Solicita backup
    W->>W: Cifra credenciales con PIN
    W->>WB: POST /wallet/backup
    WB->>VAULT: Almacena backup cifrado
    WB-->>W: Backup ID
    W-->>U: Backup completado

    Note over U,VAULT: Restauracion
    U->>W: Inicia restauracion
    W->>U: Solicita PIN
    U->>W: Introduce PIN
    W->>WB: POST /wallet/restore
    WB->>VAULT: Obtiene backup cifrado
    WB-->>W: Backup cifrado
    W->>W: Descifra con PIN
    W-->>U: Wallet restaurado
```

---

## Manejo de errores

### Errores comunes y respuestas

| Escenario | Codigo | Accion |
|-----------|--------|--------|
| Token expirado | 401 | Renovar token |
| Credencial revocada | 403 | Informar al usuario |
| Emisor no confiable | 403 | Rechazar presentacion |
| Firma invalida | 400 | Rechazar credencial |
| Timeout | 408 | Reintentar |

### Diagrama de manejo de errores

```mermaid
flowchart TD
    A[Recibe Peticion] --> B{Token valido?}
    B -->|No| C[401 Unauthorized]
    B -->|Si| D{Tiene permisos?}
    D -->|No| E[403 Forbidden]
    D -->|Si| F{Datos validos?}
    F -->|No| G[400 Bad Request]
    F -->|Si| H{Procesar}
    H -->|Error interno| I[500 Internal Error]
    H -->|Exito| J[200 OK]
```

## Recursos adicionales

- [:material-home: Volver al inicio](../index.md)
- [:material-api: Referencia API](../referencia-api/index.md)
- [:material-certificate: Modelo de credenciales](../modelo-credenciales/index.md)
