# Guides

Recipes for concrete use cases. Each guide assumes you are already familiar with the [core concepts](../concepts/index.en.md) and have access to the [sandbox](../getting-started/sandbox.en.md).

<div class="grid cards" markdown>

-   :material-login: [**OIDC IdP — login with verifiable credential**](oidc-idp.en.md)

    Use EUDIStack as an OIDC Identity Provider for your application: your users will log in by presenting a verifiable credential.

-   :material-account-sync: [**SSO across applications**](sso-multi-app.en.md)

    Share authentication between several applications in your tenant: the user presents their credential once and the others reuse it.

-   :material-account-multiple-plus: [**SCIM Provisioning**](scim-provisioning.en.md)

    Synchronise users and attributes from your corporate IdP (Okta, Entra ID, etc.) into EUDIStack via SCIM 2.0.

-   :material-cog-transfer: [**Issuance API**](issuance-api.en.md)

    Issue attestations from your backend and choose how they are delivered to the holder: by email, as an offer for your application, or signed directly.

-   :material-language-java: [**Java SDK — M2M Authentication**](verifier-m2m-sdk-java.en.md)

    Authenticate your backend service against the Verifier with no human user involved, using the Java SDK published on Maven Central.

</div>

