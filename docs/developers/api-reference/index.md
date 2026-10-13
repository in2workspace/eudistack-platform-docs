# Referencia API

Las APIs públicas de EUDIStack están organizadas por servicio. Cada una se expone vía OpenAPI 3.0; en cada página de servicio encontrarás los enlaces a su documentación y especificación OpenAPI disponibles según el entorno.

<div class="grid cards" markdown>

-   :material-certificate-outline: [**Issuer API**](issuer.md)

    Emisión, consulta y revocación de atestaciones, y listas de estado. Contratos OpenAPI interactivos.

-   :material-shield-check: [**Verifier API**](verifier.md)

    Solicitudes de presentación (OID4VP), validación de credenciales.

-   :material-wallet-outline: [**Wallet (EBW) API**](wallet-ebw.md)

    APIs server-side del European Business Wallet (gestión de claves, dispositivos, etc.).

</div>

## Convenciones comunes

??? info "Convenciones que aplican a todos los endpoints"

    - **Autenticación**: OAuth 2.0 client credentials. Tokens vinculados con DPoP.
    - **Tenant**: derivado del subdominio de la URL.
    - **Errores**: respuestas RFC 7807 (`application/problem+json`).
    - **Paginación**: cursor-based (`page_token` / `next_page_token`).
    - **Versionado**: en el path (`/v1/...`); cambios breaking incrementan la versión.
