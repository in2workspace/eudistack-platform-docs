# Emitir una credencial

<!-- TODO: contenido pendiente -->

Pasos para emitir una credencial desde el portal.

## Flujo general

1. **Inicia sesión** en el Portal Issuer con tu credencial corporativa.
2. **Selecciona el tipo de credencial** que quieres emitir (depende del catálogo configurado para tu organización).
3. **Rellena los atributos** del destinatario (nombre, email, atributos específicos del tipo).
4. **Confirma**: el portal genera una oferta y se la envía al destinatario por email/SMS, o muestra un QR para escaneo presencial.
5. **El destinatario acepta la oferta** desde su wallet — momento en el que la credencial queda emitida.

## Estados durante el flujo

- **Pendiente**: oferta enviada, todavía no aceptada.
- **Emitida**: el destinatario ha aceptado y la credencial está en su wallet.
- **Expirada**: la oferta ha caducado sin ser aceptada.

<!-- TODO: capturas del wizard de emisión -->
