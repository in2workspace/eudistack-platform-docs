# Solución de problemas — Developers

<!-- TODO: ampliar con incidencias reales detectadas en integraciones -->

Errores frecuentes durante la integración y cómo resolverlos.

## OID4VCI

### `invalid_grant` al canjear el código preautorizado

- **Causa probable**: el código ya fue consumido o caducó (TTL típico: 5 minutos).
- **Solución**: pide al Issuer una nueva oferta. El código preautorizado es de un solo uso.

### El wallet rechaza la credencial tras emitirla

- **Causa probable**: discrepancia en el `vct` (Verifiable Credential Type) o falta de claves de verificación públicas en `/.well-known/jwt-issuer`.
- **Solución**: comprueba que el `vct` declarado en metadata coincide con el de la credencial emitida.

## OID4VP

### El verifier no recibe la presentación

- **Causa probable**: el `response_uri` no es accesible públicamente o tiene certificado inválido.
- **Solución**: el wallet hace `direct_post` desde el dispositivo del usuario; el endpoint debe ser HTTPS público con cert válido.

### `invalid_presentation_submission`

- **Causa probable**: la consulta DCQL pide claims que la credencial no contiene, o la firma del Key Binding JWT no valida.
- **Solución**: revisa que la credencial presentada cumple la query y que el `nonce` del KB-JWT coincide con el de la solicitud.

## DPoP

### `invalid_dpop_proof`

- **Causa probable**: el `htu` o `htm` del DPoP no coincide con la URL/método real, o el `iat` está fuera de ventana.
- **Solución**: verifica que regeneras el DPoP en cada request; no reutilices uno entre llamadas.

## Multi-tenant

### `404` o `tenant not found` con dominio correcto

- **Causa probable**: el subdominio no está aprovisionado o la sesión apunta a otro tenant.
- **Solución**: confirma con soporte que el tenant está activo y que tu cliente OAuth está registrado en él.

---

## ¿Sigues sin resolverlo?

[Contacta con soporte](../support.md). Adjunta:

- Endpoint y método exacto.
- Request completa (sanitizando secretos).
- Respuesta recibida.
- `trace-id` si la respuesta lo incluye.
