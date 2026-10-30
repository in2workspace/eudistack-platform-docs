# Troubleshooting — Users

This section collects the most frequent problems and how to resolve them. If your problem is not listed, see [support](../support.md).

---

## Wallet

??? "The offer QR does not open"
    - **Symptom**: scanning the QR triggers no action, or an error appears.
    - **Likely cause**: the QR has expired, the offer was already consumed, or the wallet does not belong to the correct tenant.
    - **Solution**: request a new offer from the issuer; verify that the wallet belongs to the same tenant as the issuer (same domain).

    !!! info "Related pages"
        - [Issue a credential](../admin/issuer-portal/issue-credential.md) — how the issuer generates a new offer
        - [Receive credentials](wallet-eudiw/receive-credentials.md) — how to accept the new offer from the wallet

??? "Error creating the passkey"
    - **Symptom**: when activating the wallet, passkey creation fails.
    - **Likely cause**: the browser or operating system does not support WebAuthn with the PRF extension.
    - **Solution**: update the browser and OS to the latest versions. On iOS it requires iOS 17+; on Android, Chrome 122+.

??? "When you tap «Sign in with a passkey» there is no passkey or an error appears"
    - **Symptom**: the system says there is no passkey for the wallet, or shows an error when signing in.
    - **Likely cause**: there is no wallet passkey on this device or on your phone. This happens the first time or on a new device.
    - **Solution**: use **Sign in with my email**, verify the code and create a passkey.

    !!! info "Related pages"
        - [Getting started](wallet-business/getting-started.md) — how to sign in for the first time and create the passkey

??? "When you scan the passkey QR code with your phone, only text appears"
    - **Symptom**: when you scan with your phone the QR code the computer shows to use or save the passkey, only a text starting with `FIDO:/` or a web search appears.
    - **Likely cause**: some manufacturers' cameras do not recognize passkey QR codes and do not pass them to the phone's passkey manager.
    - **Solution**: scan the QR code with Google Lens.

    !!! info "Related pages"
        - [Getting started](wallet-business/getting-started.md) — how to save the passkey on your phone from a computer

??? "The phone stays on «Connecting to your device»"
    - **Symptom**: after scanning the passkey QR code, the phone keeps connecting and does not move on.
    - **Likely cause**: Bluetooth is turned off on the computer or the phone. Both devices need it to check they are close to each other, although no pairing is required.
    - **Solution**: turn on Bluetooth on the computer and the phone, cancel on the computer and try again. Both devices also need an internet connection.

??? "The phone says no passkeys are available"
    - **Symptom**: the computer says it has connected to the phone, but the phone replies that it has no passkeys available.
    - **Likely cause**: the wallet passkey is not saved on that phone. «Sign in with a passkey» can only use passkeys that already exist.
    - **Solution**: use **Sign in with my email** and, when creating the passkey, choose **iPhone, iPad or Android device** to save it on your phone.

    !!! info "Related pages"
        - [Getting started](wallet-business/getting-started.md) — how to save the passkey on your phone from a computer

??? "The credential appears as revoked"
    - **Symptom**: when presenting it to a Verifier, it reports that the credential is revoked.
    - **Likely cause**: the issuer revoked the credential (role change, leaving the organization).
    - **Solution**: before contacting the issuer, check the current status of the credential: open the credential detail in the wallet and tap **Verify credential**.

    ![Credential verification screen](../assets/img/users/troubleshooting/revoke.png){ width="240" }

    If the credential appears as revoked, contact the issuer; a new credential must be requested.

    !!! info "Related pages"
        - [Manage credentials](wallet-eudiw/manage-credentials.md) — how to verify and manage the status of credentials in the wallet

??? "The presentation does not reach the verifier"
    - **Symptom**: the user confirms in their wallet but the verifier receives nothing.
    - **Likely cause**: session timeout or network problems.
    - **Solution**: restart the request. If it persists, [contact support](../support.md) providing the verifier URL and the QR code if possible.

---

## Still not resolved?

[Contact support](../support.md). Include:

- Affected wallet/application.
- Screenshot of the error if available.
- Approximate time of the incident.
- The organization (tenant).
