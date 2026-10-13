# API Direct Issuance

Emite credenciales programáticamente desde tu backend, sin pasar por la UI del Portal Issuer. Útil para integraciones masivas o automatizaciones por evento.

!!! warning "Guía en revisión"
    Esta guía no refleja el contrato actual de la API de emisión y se va a sustituir. En particular:

    - Los sistemas **no** se autentican ante el Issuer con `client_id` y `client_secret`. Se autentican ante el **Verifier** con su credencial de máquina (`LEARCredentialMachine`). Consulta [SDK Java — Autenticación M2M](verifier-m2m-sdk-java.md).
    - La emisión es `POST /api/v1/issuances` en la dirección del tenant, y la idempotencia se indica con la cabecera `X-Idempotency-Key`.
    - No hay notificaciones por webhook.

    Si vas a integrarte ahora, contacta con tu interlocutor de EUDIStack para recibir el contrato vigente.

??? tip "¿Cuándo usar esta guía?"
    - Tu sistema ya sabe **cuándo** debe emitir una credencial (alta de empleado, finalización de un curso, etc.).
    - No quieres que un humano entre al Portal Issuer cada vez.
    - Necesitas emitir en lote o desde un flujo automatizado (CI, webhooks, ETL).

---

## Flujo resumido

1. **Autentícate** contra el Issuer usando OAuth 2.0 client credentials (M2M).
2. **Crea la oferta** con `POST /issuer/api/v1/issuances`.
3. **Entrega la `credential_offer_uri`** al destinatario (email, QR, push).
4. **Opcional:** suscríbete a webhooks para recibir notificaciones de estado.

---

## Paso 1: Autenticación M2M

El Issuer autentica sistemas backend mediante OAuth 2.0 Client Credentials. Las credenciales (`client_id` y `client_secret`) se obtienen al aprovisionar el tenant.

??? example "Token request / response"

    === "Request"

        ```http
        POST /issuer/oid4vci/v1/token
        Host: sandbox.stg.eudistack.net
        Content-Type: application/x-www-form-urlencoded

        grant_type=client_credentials&
        client_id=mi-sistema-backend&
        client_secret=s3cr3t&
        scope=credential:issue
        ```

    === "Response"

        ```json
        {
          "access_token": "eyJhbGci...",
          "token_type": "Bearer",
          "expires_in": 3600
        }
        ```

---

## Paso 2: Crear la oferta de credencial

Con el `access_token` obtenido, crea la oferta. El campo `client_request_id` garantiza **idempotencia**: si repites la llamada con el mismo valor, el Issuer devuelve la misma oferta sin crear un duplicado.

??? example "Crear oferta — request / body / response"

    === "Request"

        ```http
        POST /issuer/api/v1/issuances
        Host: sandbox.stg.eudistack.net
        Authorization: Bearer eyJhbGci...
        Content-Type: application/json
        ```

    === "Body"

        ```json
        {
          "client_request_id": "onboarding-2026-05-18-emp-00342",
          "credential_configuration_id": "learcredential.employee.sd.1",
          "payload": {
            "mandator": {
              "organizationIdentifier": "VATES-12345678A",
              "organization": "EUDIStack Demo",
              "commonName": "Admin User",
              "email": "admin@eudistack.com",
              "country": "ES"
            },
            "mandatee": {
              "firstName": "Ana",
              "lastName": "García",
              "email": "ana.garcia@eudistack.com"
            },
            "power": [
              { "function": "Admin", "action": ["Execute"] }
            ]
          },
          "delivery": "email",
          "email": "ana.garcia@eudistack.com",
          "grant_type": "urn:ietf:params:oauth:grant-type:pre-authorized_code"
        }
        ```

    === "Response"

        ```json
        {
          "issuance_id": "a3f1b2c4-7d8e-9f0a-b1c2-d3e4f5a6b7c8",
          "credential_offer_uri": "openid-credential-offer://?credential_offer_uri=https://sandbox.stg.eudistack.net/issuer/oid4vci/v1/credential-offer/XXXX",
          "status": "pending",
          "expires_at": "2026-05-18T18:00:00Z"
        }
        ```

        | Campo | Descripción |
        |---|---|
        | `issuance_id` | Identificador interno. Úsalo para consultar estado o correlacionar webhooks. |
        | `credential_offer_uri` | URL o deep link que debes entregar al destinatario. |
        | `status` | Estado inicial: `pending` (el destinatario aún no ha aceptado). |
        | `expires_at` | Fecha de expiración del código preautorizado. |

---

## Paso 3: Entregar la oferta al destinatario

=== "Email transaccional"
    Incluye el `credential_offer_uri` como enlace, o genera un QR y adjúntalo al email.

=== "Push notification"
    Envía el deep link `openid-credential-offer://...` directamente a la app del usuario.

=== "QR en pantalla"
    Genera el QR en tiempo real en tu aplicación web cuando el usuario esté presente.

---

## (Opcional) Paso 4: Webhooks de estado

Registra un endpoint HTTPS en la configuración del tenant para recibir notificaciones de cambio de estado.

??? info "Eventos disponibles"

    | Evento | Descripción |
    |---|---|
    | `issuance.accepted` | El destinatario aceptó la credencial en su wallet. |
    | `issuance.rejected` | El destinatario rechazó la oferta o caducó sin ser aceptada. |
    | `issuance.error` | Error durante la emisión (plantilla no encontrada, datos inválidos, etc.). |

??? example "Payload de ejemplo"

    ```json
    {
      "event": "issuance.accepted",
      "issuance_id": "a3f1b2c4-7d8e-9f0a-b1c2-d3e4f5a6b7c8",
      "client_request_id": "onboarding-2026-05-18-emp-00342",
      "credential_id": "urn:uuid:c1d2e3f4-...",
      "timestamp": "2026-05-18T14:35:22Z"
    }
    ```

---

??? tip "Buenas prácticas"
    - **Idempotencia:** incluye un `client_request_id` único por oferta para evitar duplicados si tu sistema reintenta.
    - **Retries:** usa backoff exponencial — los endpoints son idempotentes con el mismo `client_request_id`.
    - **Privacidad:** no almacenes los atributos de la credencial en tu sistema más tiempo del necesario.
    - **Expiración:** supervisa el campo `expires_at`; si la oferta caduca sin ser aceptada, crea una nueva.
