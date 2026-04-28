# Issuer API

<!-- TODO: enlazar/embeber spec OpenAPI desde eudistack-core-issuer -->

API para emitir credenciales verificables y gestionar su ciclo de vida.

## Endpoints principales

| Método | Path | Descripción |
|---|---|---|
| POST | `/credential-offer` | Crea una oferta de emisión OID4VCI. |
| GET | `/credential-offer/{id}` | Consulta el estado de una oferta. |
| POST | `/credential` | Endpoint OID4VCI de canje (lo usa el wallet). |
| POST | `/.well-known/openid-credential-issuer` | Metadata pública del Issuer. |
| POST | `/credentials/{id}/revoke` | Revoca una credencial emitida. |

<!-- TODO: spec OpenAPI inline -->

## Autenticación

Operaciones administrativas (`/credential-offer`, revocación) requieren un access token con scope `issuer:write`. Operaciones del wallet (`/credential`) siguen el flujo OID4VCI estándar.
