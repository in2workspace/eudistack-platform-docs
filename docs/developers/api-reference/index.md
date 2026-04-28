# Referencia API

<!-- TODO: integrar render OpenAPI inline (swagger-ui-tag o neoteroi) cuando haya specs publicadas -->

Las APIs públicas de EUDIStack están organizadas por servicio. Cada una se expone vía OpenAPI 3.0.

<div class="grid cards" markdown>

-   :material-certificate-outline: [**Issuer API**](issuer.md)

    Emisión de credenciales (OID4VCI), gestión de ofertas, revocación.

-   :material-shield-check: [**Verifier API**](verifier.md)

    Solicitudes de presentación (OID4VP), validación de credenciales.

-   :material-wallet-outline: [**Wallet (EBW) API**](wallet-ebw.md)

    APIs server-side del European Business Wallet (gestión de claves, dispositivos, etc.).

</div>

## Convenciones comunes

- **Autenticación**: OAuth 2.0 client credentials. Tokens vinculados con DPoP.
- **Tenant**: derivado del subdominio de la URL.
- **Errores**: respuestas RFC 7807 (`application/problem+json`).
- **Paginación**: cursor-based (`page_token` / `next_page_token`).
- **Versionado**: en el path (`/v1/...`); cambios breaking incrementan la versión.
