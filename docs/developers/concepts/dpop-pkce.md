# DPoP & PKCE

<!-- TODO: ampliar con cabeceras DPoP de ejemplo y diagrama PKCE -->

Dos mecanismos de seguridad que EUDIStack aplica de forma transparente en sus flujos OAuth/OID4VCI.

## PKCE

**Proof Key for Code Exchange** protege el flujo Authorization Code: el cliente genera un `code_verifier` aleatorio y envía solo su hash (`code_challenge`) al servidor. Solo quien posea el `code_verifier` original podrá canjear el code por un token. EUDIStack lo exige siempre — incluso para clientes confidenciales.

## DPoP

**Demonstrating Proof-of-Possession at the Application Layer** vincula los access tokens a una clave del cliente. Cada request al recurso protegido lleva una cabecera `DPoP` firmada con esa clave. Si un atacante roba el token sin la clave privada, no puede usarlo.

```http
GET /credential
Authorization: DPoP eyJ0eXAi...
DPoP: eyJhbGciOi...
```

EUDIStack aplica DPoP en:

- Token endpoint del Issuer.
- Credential endpoint (canje OID4VCI).
- Endpoints de gestión protegidos del Wallet EBW.

## Referencias

- [PKCE (RFC 7636)](https://datatracker.ietf.org/doc/rfc7636/)
- [DPoP (RFC 9449)](https://datatracker.ietf.org/doc/rfc9449/)
