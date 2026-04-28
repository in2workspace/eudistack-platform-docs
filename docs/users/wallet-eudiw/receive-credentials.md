# Recibir credenciales — EUDIW

<!-- TODO: contenido pendiente -->

Cómo aceptar una credencial verificable emitida por un Issuer.

## Métodos disponibles

- **Escaneo de QR**: el Issuer presenta un QR; lo escaneas desde el wallet.
- **Enlace directo**: recibes un link `openid-credential-offer://...` por email/SMS y lo abres en el dispositivo.

## Flujo paso a paso

1. **Recibes la oferta**: QR o enlace.
2. **El wallet abre la oferta**: muestra el emisor, el tipo de credencial y los atributos que recibirás.
3. **Confirmas con tu passkey**: tras la confirmación, la credencial se almacena en el wallet.
4. **Listo**: la credencial aparece en la pestaña *Credenciales*.

<!-- TODO: insertar capturas del flujo OID4VCI desde la perspectiva del usuario -->

## Errores comunes

Consulta [solución de problemas](../troubleshooting.md) si la oferta no se abre, el QR no escanea o aparece un error tras la confirmación.
