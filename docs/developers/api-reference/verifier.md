# Verifier API

<!-- TODO: enlazar/embeber spec OpenAPI desde eudistack-core-verifier -->

API para iniciar verificaciones de credenciales y validar presentaciones.

## Endpoints principales

| Método | Path | Descripción |
|---|---|---|
| POST | `/authorization-request` | Crea una solicitud de presentación OID4VP. |
| GET | `/authorization-request/{id}` | Consulta el estado de una solicitud. |
| POST | `/response` | Endpoint OID4VP donde el wallet entrega la presentación. |
| GET | `/.well-known/openid-configuration` | Metadata OIDC (cuando se usa como IdP). |

<!-- TODO: spec OpenAPI inline -->

## Modos de respuesta

- **direct_post**: el wallet hace POST directo al Verifier con la presentación.
- **direct_post.jwt**: respuesta cifrada con la clave del Verifier.
