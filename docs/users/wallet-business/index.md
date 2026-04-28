# Wallet — Business Wallet

El **European Business Wallet (EBW)** es la versión **organizacional** del wallet. A diferencia del EUDIW personal, las claves criptográficas se custodian en un servidor seguro y se desbloquean con autenticación reforzada (passkey vinculado al dispositivo del usuario). Pensado para empleados que actúan en nombre de su organización.

!!! tip "¿Cómo sé que estoy en Business Wallet?"
    Abre **Ajustes → Acerca de**. El indicador de modo mostrará `Business Wallet`. Además, en *Ajustes* aparecerá una sección **Dispositivos y claves** que no existe en el EUDIW personal.

## Diferencias clave frente al EUDIW

| Característica | EUDIW (personal) | Business Wallet |
|---|---|---|
| Custodia de claves | Dispositivo del usuario | Servidor (HSM) |
| Vinculación a organización | No | Sí |
| Multi-dispositivo | No | Sí (gestión de dispositivos) |
| Recuperación si pierdes el dispositivo | No | Sí, vía dispositivo de respaldo |

## En esta sección

<div class="grid cards" markdown>

-   :material-rocket-launch: [**Primeros pasos**](getting-started.md)

    Activa tu cuenta empresarial, registra el primer dispositivo y vincula tu passkey.

-   :material-key-chain: [**Dispositivos y claves**](devices.md)

    Gestiona los dispositivos autorizados desde los que puedes operar.

-   :material-briefcase-clock: [**Operativa diaria**](daily-operations.md)

    Recibir y presentar credenciales en nombre de la organización.

</div>

<!-- TODO: añadir capturas del wallet PWA en modo server -->
