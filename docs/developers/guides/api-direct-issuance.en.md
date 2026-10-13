# API Direct Issuance

Issue credentials programmatically from your backend, without going through the Issuer Portal UI. Ideal for bulk integrations or event-driven automations.

!!! warning "Guide under review"
    This guide does not reflect the current issuance API contract and is being replaced. In particular:

    - Systems do **not** authenticate to the Issuer with `client_id` and `client_secret`. They authenticate to the **Verifier** with their machine credential (`LEARCredentialMachine`). See [Java SDK — M2M authentication](verifier-m2m-sdk-java.md).
    - Issuance is `POST /api/v1/issuances` on the tenant address, and idempotency is set with the `X-Idempotency-Key` header.
    - There are no webhook notifications.

    If you are integrating now, contact your EUDIStack representative to get the current contract.

??? tip "When to use this guide"
    - Your system already knows **when** to issue a credential (employee onboarding, course completion, etc.).
    - You don't want a human to log into the Issuer Portal each time.
    - You need to issue in bulk or from an automated flow (CI, webhooks, ETL).

---

## Flow summary

1. **Authenticate** against the Issuer using OAuth 2.0 client credentials (M2M).
2. **Create the offer** with `POST /issuer/api/v1/issuances`.
3. **Deliver the `credential_offer_uri`** to the recipient (email, QR, push).
4. **Optional:** subscribe to webhooks to receive status notifications.

---

## Step 1: M2M Authentication

The Issuer authenticates backend systems using OAuth 2.0 Client Credentials. Credentials (`client_id` and `client_secret`) are provided when provisioning the tenant.

??? example "Token request / response"

    === "Request"

        ```http
        POST /issuer/oid4vci/v1/token
        Host: sandbox.stg.eudistack.net
        Content-Type: application/x-www-form-urlencoded

        grant_type=client_credentials&
        client_id=my-backend-system&
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

## Step 2: Create the credential offer

With the obtained `access_token`, create the offer. The `client_request_id` field guarantees **idempotency**: if you repeat the call with the same value, the Issuer returns the same offer without creating a duplicate.

??? example "Create offer — request / body / response"

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
              "lastName": "Garcia",
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

        | Field | Description |
        |---|---|
        | `issuance_id` | Internal identifier. Use it to query the status or correlate webhook events. |
        | `credential_offer_uri` | URL or deep link to deliver to the recipient. |
        | `status` | Initial status: `pending` (recipient has not yet accepted). |
        | `expires_at` | Pre-authorized code expiration date. |

---

## Step 3: Deliver the offer to the recipient

=== "Transactional email"
    Include the `credential_offer_uri` as a link, or generate a QR code and attach it to the email.

=== "Push notification"
    Send the deep link `openid-credential-offer://...` directly to the user's app.

=== "QR on screen"
    Generate the QR code in real time in your web application when the user is present.

---

## (Optional) Step 4: Webhook notifications

Register an HTTPS endpoint in your tenant configuration to receive status change notifications.

??? info "Available events"

    | Event | Description |
    |---|---|
    | `issuance.accepted` | Recipient accepted the credential in their wallet. |
    | `issuance.rejected` | Recipient rejected the offer or it expired without being accepted. |
    | `issuance.error` | Error during issuance (template not found, invalid data, etc.). |

??? example "Example webhook payload"

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

??? tip "Best practices"
    - **Idempotency:** include a unique `client_request_id` per offer to avoid duplicates if your system retries.
    - **Retries:** use exponential backoff — endpoints are idempotent with the same `client_request_id`.
    - **Privacy:** do not store credential attributes in your system longer than necessary.
    - **Expiration:** monitor the `expires_at` field; if the offer expires without being accepted, create a new one.
