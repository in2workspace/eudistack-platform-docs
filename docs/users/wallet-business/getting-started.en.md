# Getting started — Wallet

First access to the wallet: verifying the corporate email and creating the passkey you will use from then on, with the device's fingerprint, facial recognition or PIN, or with your phone.

---

## Requirements

- Corporate email address.
- Up-to-date browser that supports passkeys (Chrome, Edge, Safari or Firefox).
- Internet connection.
- To save the passkey on your phone from a computer: Bluetooth turned on on both devices.

!!! info "How you sign in to the wallet"
    - **The first time**, or on a device without a passkey, you sign in with your **email**: the wallet sends a verification code, and then you create or use a passkey.
    - **From then on**, you sign in directly with the **passkey**, with no email or code.
    - Credentials are bound to the passkey used to obtain them. To see them from another device, sign in with that same passkey: for example, the one saved on your phone, or a synced passkey (iCloud, Google).

---

## Steps

??? "1. Open the wallet"
    Navigate to the organization's wallet URL.

    If the browser can install the wallet as an app, the **Access EUDIStack securely** screen appears first (the name may vary by organization):

    - **Continue with EUDIStack Wallet** installs the wallet as an app on the device. This is the recommended option.
    - **Continue in browser** carries on without installing it.

    On iPhone, iPad and Safari on Mac, instead of installing directly, the wallet shows the steps to add it to the Home Screen or the Dock.

    <figure markdown style="margin-left: 0;">
      ![«Access EUDIStack securely» screen with the options to install the wallet or continue in the browser](../../assets/img/users/wallet-buisiness/access-install.webp){ width="420" }
      <figcaption>Install suggestion</figcaption>
    </figure>

??? "2. Choose «Sign in with my email»"
    On the **Sign in to your wallet** screen, the first time you must tap **Sign in with my email**, below *First time, or no passkey here?*.

    **Sign in with a passkey** only works once a wallet passkey already exists on this device or on your phone.

    <figure markdown style="margin-left: 0;">
      ![«Sign in to your wallet» screen with the «Sign in with a passkey» button and the «Sign in with my email» link](../../assets/img/users/wallet-buisiness/access-sign-in.webp){ width="360" }
      <figcaption>Sign-in screen</figcaption>
    </figure>

??? "3. Enter the corporate email"
    Type the corporate email and tap **Send verification code**.

    <figure markdown style="margin-left: 0;">
      ![«Continue with your email» screen with the email field and the «Send verification code» button](../../assets/img/users/wallet-buisiness/access-email.webp){ width="360" }
      <figcaption>Entering the email</figcaption>
    </figure>

??? "4. Verify the code <small>*(One-Time Password)*</small>"
    The wallet sends a 6-digit code to the email. Enter it in **Verify your email address** and tap **Continue**.

    - The code is valid for a limited time.
    - If it does not arrive, you can request another one when the countdown ends.
    - **Change email** goes back to the previous step.

    <figure markdown style="margin-left: 0;">
      ![Verification email with 6-digit OTP code](../../assets/img/users/wallet-buisiness/email-code.png){ width="400" }
      <figcaption>Email with the 6-digit code</figcaption>
    </figure>

    <figure markdown style="margin-left: 0;">
      ![«Verify your email address» screen with the 6 code boxes](../../assets/img/users/wallet-buisiness/access-code.webp){ width="420" }
      <figcaption>Entering the code</figcaption>
    </figure>

??? "5. Create the passkey"
    On **Email verified! Set up a passkey**, enter a name for the device and tap **Create Passkey**.

    <figure markdown style="margin-left: 0;">
      ![«Email verified! Set up a passkey» screen with the device name and the «Create Passkey» button](../../assets/img/users/wallet-buisiness/access-create-passkey.webp){ width="300" }
      <figcaption>Creating the passkey</figcaption>
    </figure>

    The operating system asks where to save the passkey:

    - **This device**: Windows Hello, iCloud Passwords or Google Password Manager, depending on the system.
    - **iPhone, iPad or Android device**: the passkey is saved on your phone (see below).
    - **Security key**: a physical USB or NFC key.

    !!! tip "Shared computer?"
        On a computer used by other people, save the passkey on your phone or on a security key, not on the computer.

    <figure markdown style="margin-left: 0;">
      ![Windows dialog «Choose where to save the passkey» with the options this device, phone and security key](../../assets/img/users/wallet-buisiness/passkey-windows-save-choose.webp){ width="360" }
      <figcaption>Windows: where to save the passkey (Spanish system)</figcaption>
    </figure>

    **Saving the passkey on your phone from a computer**

    1. Choose **iPhone, iPad or Android device**. The computer shows a QR code.
    2. Scan the QR code with the phone's camera.
    3. On the phone, confirm where to save the passkey and approve it with your face, fingerprint or PIN.

    The computer and the phone need **Bluetooth turned on** (no pairing required) and an internet connection.

    <figure markdown style="margin-left: 0;">
      ![Windows dialog «Save the passkey» with the QR code to scan with the phone](../../assets/img/users/wallet-buisiness/passkey-windows-save-qr.webp){ width="360" }
      <figcaption>Windows: QR code to save the passkey on the phone (Spanish system)</figcaption>
    </figure>

    <figure markdown style="margin-left: 0;">
      ![iPhone sheet «Save a passkey?» to choose where to save the passkey](../../assets/img/users/wallet-buisiness/passkey-iphone-save.webp){ width="240" }
      <figcaption>iPhone: confirmation to save the passkey</figcaption>
    </figure>

    !!! warning "Passkey not compatible"
        Some organizations protect the wallet keys with the passkey itself. In that case, if the chosen passkey manager does not offer the required feature, the wallet shows **This passkey is not compatible**. Repeat the step with your system's manager (iCloud, Google or Windows Hello) or with another security key.

??? "6. Access completed"
    The wallet shows the credentials screen. From here you can receive and present credentials.

    <figure markdown style="margin-left: 0;">
      ![Wallet credentials screen, still without credentials, with the «Scan QR code» button](../../assets/img/users/wallet-buisiness/access-credentials.webp){ width="560" }
      <figcaption>Credentials screen</figcaption>
    </figure>

---

## If the account already has a passkey

When you sign in with your email from a new device, after the code you see **Welcome back**:

- **Sign in with Passkey** lets you use a passkey the account already has, for example the one on your phone by scanning the QR code.
- **Create a new passkey**, below *None at hand?*, creates another passkey for this device.

!!! warning "A new passkey does not give access to another passkey's credentials"
    Credentials obtained with a passkey are only visible when you sign in with that passkey. If you create a new passkey, its credentials start empty.

After a passkey is added, the wallet sends a notice by email and offers to close the other open sessions.

<figure markdown style="margin-left: 0;">
  ![«Welcome back» screen with the «Sign in with Passkey» button and the «Create a new passkey» link](../../assets/img/users/wallet-buisiness/access-welcome-back.webp){ width="300" }
  <figcaption>Account with a passkey</figcaption>
</figure>

---

## Signing in afterwards

On **Sign in to your wallet**, tap **Sign in with a passkey**. The system shows its passkeys for the wallet:

- If the passkey is on the device, just approve it with your face, fingerprint or PIN.
- If it is on your phone, choose **iPhone, iPad or Android device** and scan the QR code with the phone.

<figure markdown style="margin-left: 0;">
  ![Windows dialog «Choose a passkey» with the options phone and security key](../../assets/img/users/wallet-buisiness/passkey-windows-sign-in-choose.webp){ width="360" }
  <figcaption>Windows: choosing the passkey to sign in (Spanish system)</figcaption>
</figure>

The session ends after a period of inactivity. When you come back, the wallet shows *Your session has expired. Please sign in again.*

---

## Troubleshooting

| What happens | What to do |
|--------------|------------|
| When you tap **Sign in with a passkey**, the system says there is no passkey or shows an error | There is no wallet passkey on this device or on your phone. Use **Sign in with my email**. |
| When you scan the QR code with the phone, only text appears | Some manufacturers' cameras do not recognize passkey QR codes. Scan it with Google Lens. |
| The phone stays on «Connecting to your device» | Turn on Bluetooth on the computer and the phone, cancel on the computer and try again. |
| The phone says no passkeys are available | The wallet passkey is not on that phone. Use **Sign in with my email** and create one. |

---

## Next step

[Receive credentials →](receive-credentials.md)
