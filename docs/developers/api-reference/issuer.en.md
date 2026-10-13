# Issuer API

The EUDIStack issuer APIs for the systems that integrate with it: issue attestations, query what was issued, revoke it and check its status. This page publishes the contract of each API, the same for every customer. Step-by-step usage is in the [guides](../guides/index.en.md).

[:material-book-open-variant: **Issuance API guide**](../guides/issuance-api.en.md){ .md-button .md-button--primary }
[:material-language-java: Java SDK — M2M authentication](../guides/verifier-m2m-sdk-java.en.md){ .md-button }

## Before you start

| | |
|---|---|
| Base address | `https://<tenant>.<environment>.eudistack.net/issuer`, for example `https://sandbox.stg.eudistack.net/issuer`. Contract paths are relative to it. |
| Authentication | `Authorization: Bearer <access_token>`. The token is issued by your tenant's Verifier to your system, authenticated with its machine credential (see the [SDK guide](../guides/verifier-m2m-sdk-java.en.md)). Status lists need no authentication. |
| Tenant | Derived from the address. Do not send a tenant header. |
| Authorization | Decided by the powers of the credential you authenticate with. |
| Errors | `application/problem+json` (RFC 9457). `type` is a stable code; each contract response lists its own. |

## Status of each API

| API | What it is for | Status |
|---|---|---|
| [Issuance](#issuance-api) | Issue an attestation and renew its offer while it is a draft. | Available, with parts **coming soon**: see the [guide](../guides/issuance-api.en.md#feature-status). |
| [Query](#query-api) | Get the detail of an issuance, list what was issued and search by a person identifier. | The detail is available. Filtered listing and search: **coming soon**. |
| [Revocation](#revocation-api) | Revoke a valid attestation. | **Coming soon** for systems. |
| [Status lists](#status-lists) | Publish the revocation status for verifiers. | Available. |

The contracts describe the full version of each API. What is marked coming soon is defined but not deployed yet: do not use it until this page shows it as available.

---

## Issuance API

[:material-download: Download OpenAPI](openapi/issuer-issuance.yaml){ .md-button download="issuer-issuance.yaml" }

<swagger-ui src="openapi/issuer-issuance.yaml"/>

---

## Query API

!!! info "Coming soon"
    Of this API, only the issuance detail (`GET /api/v1/issuances/{id}`) is available today. Filtered, paginated listing and search by identifier will come in a later release.

[:material-download: Download OpenAPI](openapi/issuer-query.yaml){ .md-button download="issuer-query.yaml" }

<swagger-ui src="openapi/issuer-query.yaml"/>

---

## Revocation API

!!! info "Coming soon"
    Revocation through the API is not available to systems yet.

[:material-download: Download OpenAPI](openapi/issuer-revocation.yaml){ .md-button download="issuer-revocation.yaml" }

<swagger-ui src="openapi/issuer-revocation.yaml"/>

---

## Status lists

[:material-download: Download OpenAPI](openapi/issuer-status-lists.yaml){ .md-button download="issuer-status-lists.yaml" }

<swagger-ui src="openapi/issuer-status-lists.yaml"/>

---

## OID4VCI endpoints

The endpoints the wallet uses to collect the attestation (offer, authorization, token, nonce, credential and notification) follow [OpenID4VCI 1.0](https://openid.net/specs/openid-4-verifiable-credential-issuance-1_0.html). An integrating system does not call them. The exact addresses of each tenant are published in the issuer metadata:

```text
https://<tenant>.<environment>.eudistack.net/.well-known/openid-credential-issuer/issuer
```
