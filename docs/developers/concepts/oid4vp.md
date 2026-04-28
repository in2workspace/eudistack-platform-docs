# OID4VP — OpenID for Verifiable Presentations

<!-- TODO: completar con ejemplos JSON y diagramas detallados -->

OID4VP define cómo un Verifier solicita una credencial al wallet, y cómo este responde con una presentación firmada.

## Flujos soportados

- **Same-device** — verifier y wallet en el mismo dispositivo (deep-link).
- **Cross-device** — verifier en pantalla, wallet en móvil (QR).
- **Proximity** — wallet y verifier conectados por BLE/QR de cerca.

## DCQL

EUDIStack usa **DCQL (Digital Credentials Query Language)** para que el verifier exprese **qué credencial necesita** y **qué atributos**. Es el sucesor de Presentation Exchange en el ecosistema EUDI.

```json
{
  "credentials": [
    {
      "id": "employee",
      "format": "vc+sd-jwt",
      "meta": { "vct_values": ["LEARCredentialEmployee"] },
      "claims": [
        { "path": ["mandator", "first_name"] },
        { "path": ["mandator", "last_name"] }
      ]
    }
  ]
}
```

<!-- TODO: ejemplos completos request_uri, response_uri, response_mode -->

## Referencias

- Especificación: [OID4VP 1.0](https://openid.net/specs/openid-4-verifiable-presentations-1_0.html)
- DCQL: parte de la spec OID4VP §6
