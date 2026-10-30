# Solución de problemas — Usuarios

Esta sección recoge los problemas más frecuentes y cómo resolverlos. Si el problema no aparece, consulta [soporte](../support.md).

---

## Wallet

??? "El QR de la oferta no se abre"
    - **Síntoma**: al escanear el QR no ocurre ninguna acción, o aparece un error.
    - **Causa probable**: el QR ha caducado, la oferta ya se consumió o el wallet no es del tenant correcto.
    - **Solución**: solicitar una nueva oferta al emisor; verificar que el wallet pertenece al mismo tenant que el emisor (mismo dominio).

    !!! info "Páginas relacionadas"
        - [Emitir una credencial](../admin/issuer-portal/issue-credential.md) — cómo el emisor genera una nueva oferta
        - [Recibir credenciales](wallet-eudiw/receive-credentials.md) — cómo aceptar la nueva oferta desde el wallet

??? "Error al crear el passkey"
    - **Síntoma**: al activar el wallet, la creación del passkey falla.
    - **Causa probable**: el navegador o sistema operativo no soporta WebAuthn con extensión PRF.
    - **Solución**: actualiza el navegador y SO a las últimas versiones. En iOS exige iOS 17+; en Android, Chrome 122+.

??? "Al pulsar «Entrar con passkey» no hay ninguna passkey o aparece un error"
    - **Síntoma**: el sistema dice que no hay ninguna passkey para el wallet, o muestra un error al iniciar sesión.
    - **Causa probable**: no hay ninguna passkey del wallet en este dispositivo ni en el móvil. Ocurre la primera vez o en un dispositivo nuevo.
    - **Solución**: entrar con **Entrar con mi email**, verificar el código y crear una passkey.

    !!! info "Páginas relacionadas"
        - [Primeros pasos](wallet-business/getting-started.md) — cómo entrar por primera vez y crear la passkey

??? "Al escanear con el móvil el QR de la passkey solo aparece texto"
    - **Síntoma**: al escanear con el móvil el QR que muestra el ordenador para usar o guardar la passkey, solo aparece un texto que empieza por `FIDO:/` o una búsqueda web.
    - **Causa probable**: la cámara de algunos fabricantes no reconoce el QR de las passkeys y no lo pasa al gestor de passkeys del móvil.
    - **Solución**: escanear el QR con Google Lens.

    !!! info "Páginas relacionadas"
        - [Primeros pasos](wallet-business/getting-started.md) — cómo guardar la passkey en el móvil desde un ordenador

??? "El móvil se queda en «Conectando con el dispositivo»"
    - **Síntoma**: después de escanear el QR de la passkey, el móvil se queda conectando y no avanza.
    - **Causa probable**: el Bluetooth está desactivado en el ordenador o en el móvil. Los dos dispositivos lo necesitan para comprobar que están cerca, aunque no hace falta emparejarlos.
    - **Solución**: activar el Bluetooth en el ordenador y en el móvil, cancelar en el ordenador y volver a intentarlo. Los dos dispositivos necesitan también conexión a internet.

??? "El móvil dice que no hay ninguna passkey disponible"
    - **Síntoma**: el ordenador indica que se ha conectado al móvil, pero el móvil responde que no tiene ninguna passkey disponible.
    - **Causa probable**: la passkey del wallet no está guardada en ese móvil. Con «Entrar con passkey» solo se pueden usar passkeys que ya existen.
    - **Solución**: entrar con **Entrar con mi email** y, al crear la passkey, elegir **iPhone, iPad o dispositivo Android** para guardarla en el móvil.

    !!! info "Páginas relacionadas"
        - [Primeros pasos](wallet-business/getting-started.md) — cómo guardar la passkey en el móvil desde un ordenador

??? "La credencial aparece como revocada"
    - **Síntoma**: al presentarla a un Verifier, este informa de que la credencial está revocada.
    - **Causa probable**: el emisor revocó la credencial (cambio de rol, baja en la organización).
    - **Solución**: antes de contactar con el emisor, comprobar el estado actual de la credencial: abrir el detalle de la credencial en el wallet y pulsar **Verificar credencial**.

    ![Pantalla de verificación de credencial](../assets/img/users/troubleshooting/revoke.png){ width="240" }

    Si la credencial aparece como revocada, contactar con el emisor; es necesario solicitar una nueva credencial.

    !!! info "Páginas relacionadas"
        - [Gestionar credenciales](wallet-eudiw/manage-credentials.md) — cómo verificar y gestionar el estado de las credenciales en el wallet

??? "La presentación no llega al verifier"
    - **Síntoma**: el usuario confirma en su wallet pero el verifier no recibe nada.
    - **Causa probable**: timeout de la sesión o problemas de red.
    - **Solución**: reiniciar la solicitud. Si persiste, [contactar con soporte](../support.md) proporcionando la URL del verifier y el código de QR si es posible.

---

## ¿Sigues sin resolverlo?

[Contacta con soporte](../support.md). Incluye:

- Wallet/aplicación afectada.
- Captura del error si la hay.
- Hora aproximada del incidente.
- La organización (tenant).
