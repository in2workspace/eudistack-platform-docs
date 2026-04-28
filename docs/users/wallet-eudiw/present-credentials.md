# Presentar credenciales — EUDIW

<!-- TODO: contenido pendiente -->

Cómo compartir credenciales con un Verifier de forma controlada y selectiva.

## Qué es la divulgación selectiva

Las credenciales SD-JWT permiten **revelar solo los atributos necesarios**. Por ejemplo, para demostrar que eres mayor de edad sin revelar tu fecha de nacimiento exacta.

## Flujo paso a paso

1. **El Verifier presenta una solicitud**: QR o redirección.
2. **El wallet muestra qué se está pidiendo**: emisor que confías, qué atributos, para qué fin.
3. **Eliges qué compartir**: marca/desmarca atributos (cuando proceda).
4. **Confirmas con tu passkey**: el wallet firma y envía la presentación.

<!-- TODO: insertar capturas del flujo OID4VP desde la perspectiva del usuario -->

## Privacidad

- Solo se envía lo que confirmas.
- El wallet registra cada presentación en *Actividad* (puedes consultarla en cualquier momento).
- Puedes denegar la presentación sin penalización.
