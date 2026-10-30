# Devices & keys — Wallet

The wallet lets you access your credentials from several devices. Each device is registered with its own passkey (fingerprint, facial recognition or PIN), so that only you can authenticate from it.

---

## Devices section

Available in **[Settings → My Devices](../wallet-eudiw/settings.md#myDevices)**. 

Lists all the passkeys linked to the account with the following information:

- Device name.
- Active sessions.
- Registration date.

??? "view images"
    ![My Devices field in Settings](../../assets/img/users/wallet-buisiness/my-divices.png){ width="320" }

    ![My Devices screen — details of a registered device](../../assets/img/users/wallet-buisiness/detail-device.png){ width="320" }

---

## Available actions

From each device's details you can:

??? "Rename"
    Update the device name to make it easier to identify. 

    Tap the device's edit icon to access the *Rename* option.

    ![Device edit icon with the Rename option](../../assets/img/users/wallet-buisiness/rename-click.png){ width="320" }

    Enter the new name in the dialog. The name is updated immediately.

    ![Dialog for entering the device's new name](../../assets/img/users/wallet-buisiness/rename-device.png){ width="240" }

??? "Sign out"
    Ends the active session on that device without removing the passkey. The device stays registered and can be used to authenticate again.

    Tap *Sign out* to end all active sessions on the device.

    ![Device session sign-out screen](../../assets/img/users/wallet-buisiness/close-session.png){ width="240" }

    To access again, authenticate with the device's passkey.

    ![Sign-in screen after signing out](../../assets/img/users/wallet-buisiness/start-session.png){ width="240" }

    Access is confirmed and you can continue using the wallet from the same device.

    ![Access confirmation screen after re-authenticating](../../assets/img/users/wallet-buisiness/access-confirmation.png){ width="240" }

??? "Delete"
    Removes the passkey and the device's link. It will not be possible to authenticate from that device until it is registered again.

    Tap the device's delete icon. Upon confirmation, the passkey and the device's link are removed. 

    ![Device delete icon](../../assets/img/users/wallet-buisiness/delete-click.png){ width="320" }

    To register the device again, follow the steps in [Getting started](getting-started.md).


    ??? warning "Last device protection"
        The last registered passkey cannot be removed. To deregister the device in use, first register a new device from **[Settings → My Devices](../wallet-eudiw/settings.md#myDevices)**.

        ![Error when trying to remove the last registered passkey](../../assets/img/users/wallet-buisiness/not-delete.png){ width="400" }

---

## Best practices

- Register at least **two devices** (main + backup) to avoid losing access.
- Remove devices that are no longer in use (old phone, computer returned to the company).
