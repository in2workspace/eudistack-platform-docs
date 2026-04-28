# SCIM Provisioning

<!-- TODO: ejemplos concretos con Okta y Entra ID -->

Sincroniza usuarios desde tu IdP corporativo hacia EUDIStack usando SCIM 2.0. Cuando el alta/baja/modificación pasa por tu directorio (Okta, Entra ID, Workday...), el usuario queda automáticamente provisionado en EUDIStack y listo para recibir su credencial.

## Cuándo usar esta guía

- Tu organización ya gestiona el ciclo de vida del empleado en un IdP/HRIS.
- Quieres automatizar la emisión de credenciales sin intervención manual.

## Endpoints expuestos

EUDIStack actúa como **SCIM Service Provider**. Endpoints disponibles:

- `GET/POST /scim/v2/Users`
- `GET/PUT/PATCH/DELETE /scim/v2/Users/{id}`
- `GET/POST /scim/v2/Groups`

<!-- TODO: detallar atributos requeridos vs opcionales y mapping a credenciales -->

## Configuración en tu IdP

1. **Crea una app SCIM** apuntando a `https://scim.<tenant>.eudistack.net/scim/v2`.
2. **Configura el bearer token** que te entregaremos.
3. **Define el mapping de atributos** entre tu directorio y EUDIStack.

## Comportamiento

- **Crear usuario** → se provisiona la cuenta y se programa la emisión de credencial.
- **Modificar atributos** → se renueva la credencial con los nuevos valores.
- **Desactivar/eliminar** → se revoca la credencial vigente.
