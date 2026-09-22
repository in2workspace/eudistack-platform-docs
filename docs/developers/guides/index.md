# Guías

Recetas para casos de uso concretos. Cada guía asume que ya conoces los [conceptos básicos](../concepts/index.md) y tienes acceso al [sandbox](../getting-started/sandbox.md).

<div class="grid cards" markdown>

-   :material-login: [**OIDC IdP — login con credencial**](oidc-idp.md)

    Usa EUDIStack como Identity Provider OIDC para tu aplicación: tus usuarios harán login presentando una credencial verificable.

-   :material-account-sync: [**SSO entre aplicaciones**](sso-multi-app.md)

    Comparte la autenticación entre varias aplicaciones de tu tenant: el usuario presenta su credencial una vez y las demás la reutilizan.

-   :material-account-multiple-plus: [**SCIM Provisioning**](scim-provisioning.md)

    Sincroniza usuarios y atributos desde tu IdP corporativo (Okta, Entra ID, etc.) hacia EUDIStack vía SCIM 2.0.

-   :material-cog-transfer: [**API Direct Issuance**](api-direct-issuance.md)

    Emite credenciales programáticamente desde tu backend (sin pasar por el Portal Issuer).

-   :material-language-java: [**SDK Java — Autenticación M2M**](verifier-m2m-sdk-java.md)

    Autentica tu servicio backend contra el Verifier sin usuario humano de por medio, usando el SDK Java publicado en Maven Central.

</div>
