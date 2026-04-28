# Glosario

<!-- TODO: ampliar con términos específicos EUDIStack -->

## Identidad y credenciales

**Verifiable Credential (VC)**
: Documento digital firmado por un emisor que afirma propiedades sobre un sujeto (titular). En EUDIStack se usa habitualmente en formato SD-JWT VC.

**Verifiable Presentation (VP)**
: Conjunto de una o más VCs (o porciones selectivas) presentadas por el holder a un verifier, firmadas para probar la posesión.

**Holder**
: La persona u organización que posee la credencial en su wallet.

**Issuer**
: La entidad que emite credenciales (universidad, empresa, autoridad).

**Verifier (Relying Party)**
: La entidad que solicita y valida una presentación.

## Protocolos

**OID4VCI**
: OpenID for Verifiable Credential Issuance — protocolo estándar para la emisión.

**OID4VP**
: OpenID for Verifiable Presentations — protocolo estándar para la presentación.

**DCQL**
: Digital Credentials Query Language — lenguaje declarativo para que un verifier exprese qué credenciales/atributos quiere.

**DPoP**
: Demonstrating Proof-of-Possession — vincula access tokens a una clave del cliente.

**PKCE**
: Proof Key for Code Exchange — protege el flujo Authorization Code OAuth.

## Formatos

**SD-JWT VC**
: Selective Disclosure JWT Verifiable Credential — formato que permite al holder revelar solo los claims necesarios.

**mdoc / mDL**
: ISO/IEC 18013-5 — formato binario usado típicamente para Mobile Driving Licence.

**W3C VC Data Model**
: Especificación general de credenciales verificables del W3C; menos usado en el ecosistema EUDI actual.

## Multi-tenant

**Tenant**
: Cliente aislado dentro de la plataforma EUDIStack. Cada tenant tiene su propia BD (schema), branding y URLs.

**Schema-per-tenant**
: Estrategia de aislamiento de datos en PostgreSQL: un schema por tenant dentro de la misma base de datos.
