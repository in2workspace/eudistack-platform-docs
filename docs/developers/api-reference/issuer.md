# Issuer API

APIs del emisor de EUDIStack para los sistemas que se integran con él: emitir atestaciones, consultar lo emitido, revocarlo y comprobar su estado. Esta página publica el contrato de cada API, el mismo para todos los clientes. Cómo usarlas paso a paso está en las [guías](../guides/index.md).

[:material-book-open-variant: **Guía de la API de emisión**](../guides/issuance-api.md){ .md-button .md-button--primary }
[:material-language-java: SDK Java — Autenticación M2M](../guides/verifier-m2m-sdk-java.md){ .md-button }

## Antes de empezar

| | |
|---|---|
| Dirección base | `https://<tenant>.<entorno>.eudistack.net/issuer`, por ejemplo `https://sandbox.stg.eudistack.net/issuer`. Las rutas de los contratos son relativas a ella. |
| Autenticación | `Authorization: Bearer <access_token>`. El token lo emite el Verifier de tu tenant a tu sistema, autenticado con su credencial de máquina (ver la [guía del SDK](../guides/verifier-m2m-sdk-java.md)). Las listas de estado no requieren autenticación. |
| Tenant | Se deduce de la dirección. No envíes cabecera de tenant. |
| Autorización | La deciden los poderes de la credencial con la que te autenticas. |
| Errores | `application/problem+json` (RFC 9457). `type` es un código estable; cada respuesta del contrato enumera los suyos. |

## Estado de cada API

| API | Para qué sirve | Estado |
|---|---|---|
| [Emisión](#api-de-emision) | Emitir una atestación y renovar su oferta mientras está en borrador. | Disponible, con partes **próximamente**: ver la [guía](../guides/issuance-api.md#estado-de-las-funciones). |
| [Consulta](#api-de-consulta) | Ver el detalle de una emisión, listar lo emitido y buscar por un identificador de la persona. | El detalle está disponible. Listado con filtros y búsqueda: **próximamente**. |
| [Revocación](#api-de-revocacion) | Revocar una atestación válida. | **Próximamente** para sistemas. |
| [Listas de estado](#listas-de-estado) | Publicar el estado de revocación para los verificadores. | Disponible. |

Los contratos describen la versión completa de cada API. Lo marcado como próximamente está definido pero aún no desplegado: no lo uses hasta que esta página lo indique como disponible.

---

## API de emisión

[:material-download: Descargar OpenAPI](openapi/issuer-issuance.yaml){ .md-button download="issuer-issuance.yaml" }

<swagger-ui src="openapi/issuer-issuance.yaml"/>

---

## API de consulta

!!! info "Próximamente"
    De esta API, hoy solo está disponible el detalle de una emisión (`GET /api/v1/issuances/{id}`). El listado con filtros y paginación y la búsqueda por identificador llegarán en una próxima versión.

[:material-book-open-variant: Guía de la API de consulta](../guides/query-api.md){ .md-button }
[:material-download: Descargar OpenAPI](openapi/issuer-query.yaml){ .md-button download="issuer-query.yaml" }

<swagger-ui src="openapi/issuer-query.yaml"/>

---

## API de revocación

!!! info "Próximamente"
    La revocación por API todavía no está disponible para sistemas.

[:material-book-open-variant: Guía de la API de revocación](../guides/revocation-api.md){ .md-button }
[:material-download: Descargar OpenAPI](openapi/issuer-revocation.yaml){ .md-button download="issuer-revocation.yaml" }

<swagger-ui src="openapi/issuer-revocation.yaml"/>

---

## Listas de estado

[:material-download: Descargar OpenAPI](openapi/issuer-status-lists.yaml){ .md-button download="issuer-status-lists.yaml" }

<swagger-ui src="openapi/issuer-status-lists.yaml"/>

---

## Endpoints OID4VCI

Los endpoints que usa el wallet para recoger la atestación (oferta, autorización, token, nonce, credencial y notificación) siguen [OpenID4VCI 1.0](https://openid.net/specs/openid-4-verifiable-credential-issuance-1_0.html). Un sistema integrador no los llama. Las direcciones exactas de cada tenant se publican en los metadatos del emisor:

```text
https://<tenant>.<entorno>.eudistack.net/.well-known/openid-credential-issuer/issuer
```
