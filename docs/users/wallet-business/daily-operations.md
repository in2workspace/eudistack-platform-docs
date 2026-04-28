# Operativa diaria — Business Wallet

<!-- TODO: contenido pendiente -->

Las operaciones del día a día (recibir, presentar y gestionar credenciales) son **idénticas a las del EUDIW personal**, salvo por el factor de autenticación: cada operación criptográfica requiere desbloquear el passkey vinculado al dispositivo en uso.

## Recibir credenciales

Mismo flujo que en EUDIW. Consulta [Recibir credenciales (EUDIW)](../wallet-eudiw/receive-credentials.md).

## Presentar credenciales

Mismo flujo que en EUDIW. Consulta [Presentar credenciales (EUDIW)](../wallet-eudiw/present-credentials.md).

## Gestionar credenciales

Mismo flujo que en EUDIW. Consulta [Gestionar credenciales (EUDIW)](../wallet-eudiw/manage-credentials.md).

## Diferencias prácticas

- Cada confirmación pasa por una autenticación reforzada (passkey + servidor).
- Si tu passkey caduca o el servidor de claves no está accesible, el wallet mostrará un error explícito y no permitirá completar la operación.
