# Solución de problemas — Usuarios

<!-- TODO: ampliar con incidencias reales detectadas en producción -->

Esta sección recoge los problemas más frecuentes y cómo resolverlos. Si tu caso no aparece, consulta [soporte](../support.md).

## Wallet

### El QR de la oferta no se abre

- **Síntoma**: escaneas el QR y no pasa nada, o sale un error.
- **Causa probable**: el QR ha caducado, la oferta ya se consumió o el wallet no es del tenant correcto.
- **Solución**: pide una nueva oferta al emisor; verifica que estás usando el wallet del mismo tenant que el emisor (mismo dominio).

### No puedo crear el passkey

- **Síntoma**: al activar el wallet, la creación del passkey falla.
- **Causa probable**: el navegador o sistema operativo no soporta WebAuthn con extensión PRF.
- **Solución**: actualiza navegador y SO a las últimas versiones. En iOS exige iOS 17+; en Android, Chrome 122+.

### La credencial aparece como revocada

- **Síntoma**: al presentarla a un Verifier, te informa de que la credencial está revocada.
- **Causa probable**: el emisor revocó la credencial (cambio de rol, baja en la organización).
- **Solución**: contacta con el emisor; necesitarás una nueva credencial.

## Portal Issuer

### No puedo iniciar sesión

- **Síntoma**: el login con credencial verificable falla.
- **Causa probable**: tu credencial corporativa ha caducado o ha sido revocada.
- **Solución**: contacta con el administrador de tu organización para que te emita una nueva.

### El destinatario no recibe la oferta

- **Síntoma**: emites la credencial pero el destinatario nunca la recibe.
- **Causa probable**: email/SMS bloqueado o dirección incorrecta.
- **Solución**: revisa en la pestaña *Credenciales* el estado y reenvía la oferta; verifica el contacto del destinatario.

## Verifier

### La presentación no llega

- **Síntoma**: el usuario confirma en su wallet pero el verifier no recibe nada.
- **Causa probable**: timeout de la sesión o problemas de red.
- **Solución**: reinicia la solicitud. Si persiste, abre un ticket con el ID de sesión.

---

## ¿Sigues sin resolverlo?

[Contacta con soporte](../support.md). Incluye:

- Wallet/aplicación afectada.
- Captura del error si la hay.
- Hora aproximada del incidente.
- Tu organización (tenant).
