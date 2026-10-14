# Query API

Query from your backend what your organization has issued: the detail of an issuance by its identifier, the list of what was issued, and the search for a person's attestations by a data item of your own, such as a national ID. Without going through the Administration Portal.

[:material-file-document: **Query OpenAPI contract**](../api-reference/issuer.en.md#query-api){ .md-button .md-button--primary }
[:material-cog-transfer: Issuance API](issuance-api.en.md){ .md-button }

??? tip "When to use this guide"
    - You need to know the status of an attestation you issued, for example before revoking it.
    - You want to feed your own systems with what was issued.
    - You know the person by a data item of your own (national ID, registration number), not by the issuance process identifier.

??? info "Prerequisites"
    - An `access_token` from the Verifier, obtained with your system's machine credential, as in [step 1 of the Issuance API](issuance-api.en.md#step-1-get-the-access-token).
    - The `Onboarding · Execute` power in your tenant's domain. You see your organization's issuance processes; the tenant administrator sees the whole tenant.

---

## Feature status

This guide describes the full API contract. Some parts are not deployed yet: they are marked **Coming soon** in their section and should not be used until they are.

| Feature | Status |
|---|---|
| Detail of an issuance by its identifier | Available, with fewer fields than the contract |
| Listing of what was issued | Partial: today it returns your whole scope in one response and ignores the parameters |
| Filters by status and type, and pagination | Coming soon |
| Search by a person identifier | Coming soon |
| Errors with the codes in this guide | Partial: today a missing detail returns `credential_not_found` |
| Rate limit | Coming soon |

---

## Detail of an issuance

!!! info "Available, with fewer fields"
    Today the response includes `procedure_id`, `credential_configuration_id`, `lifeCycleStatus`, `credential` and `email`. The other fields of the contract will come in a later release.

`GET /issuer/api/v1/issuances/{id}`

`{id}` is the issuance process identifier: the one the issuance returns in the `Location` header, or the `procedure_id` of a listing or a search. The response includes the attestation data in `credential`.

```http
GET /issuer/api/v1/issuances/0b6f1d2e-8c4a-4b1f-9e2d-7a5c3f8e1b90 HTTP/1.1
Host: sandbox.stg.eudistack.net
Authorization: Bearer <access_token>
```

```json
{
  "procedure_id": "0b6f1d2e-8c4a-4b1f-9e2d-7a5c3f8e1b90",
  "organization_identifier": "VATES-B00000000",
  "credential_configuration_id": "learcredential.employee.w3c.4",
  "credential_format": "jwt_vc_json",
  "lifeCycleStatus": "VALID",
  "subject": "María García López",
  "email": "maria.garcia@company.example",
  "delivery": "email",
  "valid_from": "2026-10-01T09:12:44Z",
  "valid_until": "2027-10-01T09:12:44Z",
  "created": "2026-10-01T09:10:02Z",
  "created_by": "hr-system@company.example",
  "updated": "2026-10-01T09:12:44Z",
  "credential": { "...": "attestation data" }
}
```

Every access to the detail is recorded, because it returns personal data. Request it only when you need the attestation data: to know the status, the listing or the search is enough.

A process that does not exist and one outside your scope get the same response: 404 `issuance_not_found`.

### Issuance process states

| State | Meaning |
|---|---|
| `DRAFT` | Issuance created; the holder has not collected the attestation yet. |
| `WITHDRAWN` | Issuance withdrawn before the holder collected it. |
| `ISSUED` | Attestation delivered with a validity start in the future. |
| `VALID` | Valid attestation. The only state that can be revoked. |
| `REVOKED` | Revoked attestation. It is final. |
| `EXPIRED` | Expired attestation. |
| `ARCHIVED` | Finished and archived process. |

---

## List what was issued

!!! info "Coming soon"
    Today the listing returns every process in your scope in one response and ignores the parameters in this section. Filters and pagination will come in a later release. Until then, do not send parameters and do not rely on the result being paginated.

`GET /issuer/api/v1/issuances`

Returns the issuance processes in your scope, newest first and in every state. Filters can be combined:

| Parameter | Description |
|---|---|
| `status` | One or more states, comma-separated, for example `VALID,REVOKED`. |
| `credential_configuration_id` | A single attestation type. |
| `limit` | Results per page, from 1 to 200. Default 50. |
| `cursor` | The `next_cursor` value of the previous page. |

To walk the whole listing, repeat the request with `cursor` set to the `next_cursor` received until it is `null`. The listing does not include the attestation data: request the detail to see it.

```json
{
  "credential_procedures": [
    {
      "credential_procedure": {
        "procedure_id": "0b6f1d2e-8c4a-4b1f-9e2d-7a5c3f8e1b90",
        "organization_identifier": "VATES-B00000000",
        "credential_type": "learcredential.employee.w3c.4",
        "status": "VALID",
        "subject": "María García López",
        "updated": "2026-10-01T09:12:44Z"
      }
    }
  ],
  "next_cursor": null
}
```

---

## Search by a person identifier

!!! info "Coming soon"
    Search by identifier is not available yet. Today the listing ignores `identifier_type` and `identifier` and returns your whole scope: do not take it as a search result.

`GET /issuer/api/v1/issuances?identifier_type=<type>&identifier=<value>`

Each attestation type in your tenant's catalogue can declare **search identifiers**: attestation data used to search later, such as the national ID (`dni`) or the registration number (`colegiado`). The names are defined by your tenant's catalogue.

```http
GET /issuer/api/v1/issuances?identifier_type=dni&identifier=12345678Z&status=VALID HTTP/1.1
Host: sandbox.stg.eudistack.net
Authorization: Bearer <access_token>
```

- The match is exact. Send the value in the same format it was issued with, and URL-encode it.
- `identifier_type` and `identifier` always go together. One without the other is rejected with 400 `BAD_REQUEST`.
- An `identifier_type` that no catalogue type declares is rejected with 400 `unknown_identifier_type`.
- A search with no results is not an error: it returns 200 with an empty list.
- It combines with the listing filters, for example `status=VALID` to find what can be revoked.

EUDIStack does not search the value in clear. At issuance it stores a fingerprint of each identifier, computed with a key of your tenant, and at search time it compares the fingerprint of the value received. Fingerprints are also computed for what was issued before the identifier existed in the catalogue.

---

## Errors

Errors are returned as `application/problem+json` (RFC 9457). Build your logic on `type`, not on `title` or `detail`.

| HTTP | `type` | Cause | What to do |
|---|---|---|---|
| 400 | `BAD_REQUEST` | Parameter with a wrong format, identifier without type or the other way round, invalid cursor, or an `{id}` that is not a UUID. | Fix the request. |
| 400 | `unknown_identifier_type` | Your tenant's catalogue does not declare that search identifier. | Check `identifier_type`. |
| 401 | `invalid_token`, `authentication_error` | The token is missing, invalid or expired. | Get a new token. |
| 403 | `insufficient_permission` | Your credential's powers do not allow queries. | Check the machine credential powers. |
| 404 | `issuance_not_found` | The process does not exist or is outside your scope. | Check the identifier. |
| 429 | `rate_limited` | The rate limit was exceeded. | Wait for `Retry-After`. |

The search rate limit is lower than the listing and detail one (60 against 300 requests per minute per organization for systems), to prevent walking the census of people.

??? warning "Security considerations"
    - The detail returns the holder's personal data. Request it only when you need it and do not keep it longer than necessary.
    - Search only for identifiers of people your organization has a relationship with: every search is recorded, with who made it and the identifier type.

---

## Full reference

The full query API contract, with every field, response and error code, is in the [Issuer API reference](../api-reference/issuer.en.md#query-api).
