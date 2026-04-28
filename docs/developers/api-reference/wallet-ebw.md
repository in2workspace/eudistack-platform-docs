# Wallet (EBW) API

<!-- TODO: enlazar/embeber spec OpenAPI desde eudistack-core-wallet-ebw -->

API server-side del European Business Wallet. Solo aplica al modo *Server* (Business Wallet); el modo *Browser* (EUDIW personal) opera 100% en el dispositivo del usuario.

## Endpoints principales

| Método | Path | Descripción |
|---|---|---|
| POST | `/users/register` | Alta de usuario corporativo. |
| POST | `/users/{id}/devices` | Registrar un nuevo dispositivo (passkey). |
| DELETE | `/users/{id}/devices/{deviceId}` | Revocar un dispositivo. |
| POST | `/credentials` | Almacenar una credencial recibida (interno OID4VCI). |
| POST | `/presentations` | Generar una presentación firmada (interno OID4VP). |

<!-- TODO: spec OpenAPI inline -->

## Notas

- Todos los endpoints requieren autenticación de la sesión del usuario más DPoP.
- La gestión de claves criptográficas vive en HSM/KMS y nunca sale al cliente.
