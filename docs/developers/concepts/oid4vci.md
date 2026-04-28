# OID4VCI — OpenID for Verifiable Credential Issuance

<!-- TODO: completar con ejemplos JSON y diagramas detallados -->

OID4VCI es el protocolo estándar (basado en OAuth 2.0) que define cómo un Issuer entrega una credencial verificable a un wallet.

## Flujos soportados

EUDIStack soporta los dos flujos del estándar:

- **Pre-Authorized Code** — la oferta lleva un código preautorizado; el wallet lo canjea sin login adicional. Pensado para flujos en los que el Issuer ya conoce al usuario.
- **Authorization Code** — el wallet inicia un flujo OAuth completo con autenticación. Pensado para autoservicio.

## Anatomía de una oferta

```json
{
  "credential_issuer": "https://issuer.sandbox.eudistack.net",
  "credential_configuration_ids": ["LEARCredentialEmployee_jwt_vc_json"],
  "grants": {
    "urn:ietf:params:oauth:grant-type:pre-authorized_code": {
      "pre-authorized_code": "..."
    }
  }
}
```

<!-- TODO: ampliar con metadata, token endpoint, credential endpoint y ejemplos -->

## Referencias

- Especificación: [OID4VCI 1.0](https://openid.net/specs/openid-4-verifiable-credential-issuance-1_0.html)
- Perfil HAIP: [estándares](../../reference/standards.md)
