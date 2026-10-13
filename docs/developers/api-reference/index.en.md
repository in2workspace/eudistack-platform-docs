# API Reference

EUDIStack's public APIs are organized by service. Each is exposed via OpenAPI 3.0; on each service page you'll find links to its documentation and OpenAPI specification available per environment.

<div class="grid cards" markdown>

-   :material-certificate-outline: [**Issuer API**](issuer.md)

    Issuance, query and revocation of attestations, and status lists. Interactive OpenAPI contracts.

-   :material-shield-check: [**Verifier API**](verifier.md)

    Presentation requests (OID4VP), credential validation.

-   :material-wallet-outline: [**Wallet (EBW) API**](wallet-ebw.md)

    Server-side APIs of the European Business Wallet (key management, devices, etc.).

</div>

## Common Conventions

??? info "Conventions that apply to all endpoints"

    - **Authentication**: OAuth 2.0 client credentials. Tokens bound with DPoP.
    - **Tenant**: derived from the URL subdomain.
    - **Errors**: RFC 7807 responses (`application/problem+json`).
    - **Pagination**: cursor-based (`page_token` / `next_page_token`).
    - **Versioning**: in the path (`/v1/...`); breaking changes increment the version.

