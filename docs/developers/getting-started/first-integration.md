# Tu primera integración

<!-- TODO: completar con ejemplos curl reales contra sandbox -->

Esta guía te lleva paso a paso por un flujo completo: emitir una credencial, recibirla en un wallet de prueba y presentarla a un Verifier.

## Esquema del flujo

```mermaid
sequenceDiagram
    participant Tu sistema
    participant Issuer API
    participant Wallet
    participant Verifier API

    Tu sistema->>Issuer API: 1. Crear oferta de credencial
    Issuer API-->>Tu sistema: credential_offer (URL/QR)
    Tu sistema-->>Wallet: 2. Entregar oferta al usuario
    Wallet->>Issuer API: 3. Canjear oferta (OID4VCI)
    Issuer API-->>Wallet: Credencial SD-JWT VC
    Verifier API->>Wallet: 4. Solicitar presentación (OID4VP)
    Wallet-->>Verifier API: Presentación firmada
    Verifier API-->>Tu sistema: 5. Resultado de verificación
```

## Pasos

1. **Crea una oferta** en el Issuer (POST `/credential-offer`).
2. **Entrega la URL/QR** al usuario.
3. **El usuario canjea** desde su wallet (OID4VCI pre-autorizado).
4. **Inicia una verificación** desde el Verifier (POST `/authorization-request`).
5. **Recoge el resultado** vía callback o polling.

<!-- TODO: ejemplos curl completos para cada paso, contra sandbox -->

## Próximos pasos

- [Conceptos: OID4VCI](../concepts/oid4vci.md)
- [Conceptos: OID4VP](../concepts/oid4vp.md)
- [Referencia API](../api-reference/index.md)
