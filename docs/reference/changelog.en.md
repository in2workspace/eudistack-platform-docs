# Changelog

Here you will find a summary of the most relevant new features and improvements in the EUDIStack platform, organized by version and component. If you need the full technical detail for each component, you can consult the individual changelogs on GitHub.

---

## Platform Components

??? "Issuer"
    === "v.3"
        **Multi-organization platform and advanced issuance**

        The 3.x release series turned the issuer into a robust platform capable of serving multiple organizations simultaneously, with support for demanding enterprise environments and cloud deployments.

        **Main highlights for integrators:**

        - **Multi-organization support (multi-tenant):** The platform can now manage multiple organizations in a fully isolated way. Each organization has its own data space, configuration and users, with no interference between them.

        - **More complete administration roles:** Three clear access levels have been defined: platform administrator, organization administrator and operational user (LEAR), so each actor has exactly the permissions they need.

        - **Integration with qualified signing services (QTSP):** Organizations can now delegate credential signing to external qualified electronic signature providers, removing the need to manage certificates locally.

        - **Compatibility with the latest standards:** The issuer has been migrated to the VCDM 2.0 credential format and compatibility with SD-JWT and DPoP has been improved, the most current standards in the EUDI ecosystem.

        - **Improved cloud deployment:** Fixes and adjustments for a more reliable deployment in AWS environments, including correct domain resolution in multi-organization setups.

        - **More reliable communications:** Email templates and credential offer flows have been modernized, improving the end-user experience.

        - **Integrated observability:** Distributed tracing (OpenTelemetry) has been added to make diagnostics and monitoring in production easier.

---

??? "Verifier"

    === "v.3"

        **Multi-organization verifier with stronger security**

        The 3.x series turned a significant evolution of the verifier, focused on security, compatibility with the latest standards and the ability to serve multiple organizations.

        **Main highlights for integrators:**

        - **Renewed and more maintainable architecture:** The code has been reorganized into well-defined modules, which makes integrations easier and reduces the risk of regressions. It has been upgraded to the latest versions of Java and Spring Boot.

        - **Stronger security at every level:** Multiple layers of protection have been implemented, including improved authentication with PKCE, protection against redirect attacks, IP-based rate limiting, refresh token rotation and HTTP security headers. Cryptographic verification covers SD-JWT, Token Status Lists and RSA and EC algorithms.

        - **Full support for the latest credential standards:** Simultaneous compatibility with VCDM 1.1 and 2.0, support for SD-JWT VC (RFC 9901), DCQL and generic credentials without dependencies on proprietary schemas.

        - **Improved presentation flow:** WebSockets have been replaced by Server-Sent Events (SSE) for the cross-device authentication flow, providing greater stability and compatibility.

        - **Externally configurable visual customization:** The frontend can now be configured and branded (logos, colors, translations) without modifying the verifier code.

        - **Multi-organization cloud deployment:** Compatibility with AWS CloudFront and ALB, including correct routing for deployments with multiple organizations.

        - **API documentation available:** The REST API is now documented with OpenAPI/Swagger.

    === "v.2"

        **Stabilization of authentication flows and visual experience**

        Version 2 consolidated the standard authentication flows and improved the visual customization of the verifier.

        **Main highlights:**

        - **More robust authentication flows:** Full implementation of the Authorization Code flow with PKCE, refresh token support, audience validation and nonce validation for verifiable presentations.

        - **Expanded compatibility with credential types:** Support for new enterprise and machine credential types, together with revocation and expiration checks.

        - **Dynamic visual customization:** Logos, favicons and colors configurable per organization, with internationalization support based on the user's browser language.

        - **Accessibility and responsive design improvements.**

    === "v.1"

        **Verifier core features**

        Version 1 introduced the foundational capabilities for decentralized credential verification.

        **Initial features:**

        - Implementation of OpenID Connect and OpenID for Verifiable Presentations (OID4VP).
        - Support for person-to-machine and machine-to-machine authentication.
        - QR code login.
        - Verification of verifiable presentations and credentials, including revocation and signature validation.
        - First version of the login frontend with corporate branding.

---

??? "EUDI Wallet"

    === "v.3"

        **Multi-organization wallet with an optimized mobile experience**

        Version 3 turned the wallet into an application that can adapt dynamically to multiple organizations, with significant improvements in security, accessibility and mobile compatibility.

        **Main highlights for integrators:**

        - **Runtime multi-organization support:** The wallet automatically adapts its configuration, appearance and connections according to the organization, with no need for separate deployments.

        - **Improved iOS experience:** The PWA installation flow has been optimized specifically for Safari on iOS, with improved camera and deep-link support.

        - **WCAG 2.1 AA accessibility:** Visual and screen-reader accessibility improvements have been added.

        - **More reliable presentation and issuance flows:** Improvements in the OID4VP and OID4VCI flows for both same-device and proximity scenarios. The SD-JWT parser has been rewritten in line with the latest RFC 9901.

        - **Stronger cryptographic security:** Master key derivation using PRF and use of the Web Crypto API for all sensitive operations.

        - **Multi-tab stability:** A mechanism has been implemented to avoid conflicts when the wallet is open in multiple tabs at the same time.

        - **Automatic updates:** The Service Worker updates automatically to ensure users always have the latest version.

    === "v.2"

        **Consolidation of cryptographic capabilities and customization**

        **Main highlights:**

        - Integrated cryptographic operations via Web Crypto API and IndexedDB.
        - Support for local signature generation (optional).
        - Configurable dynamic branding: logos, colors and favicons per organization.
        - Improvements in PIN entry flows, navigation and camera selection.
        - Improved lifecycle management for credentials and mandates.

    === "v.1"

        **Wallet core features**

        **Initial features:**

        - User login, registration and logout.
        - Decentralized identity (DIDs) and verifiable credentials management.
        - QR scanning and real-time communication.
        - Initial integration with EBSI.
        - Early OIDC flows and wallet-based authentication.

---

??? "Business Wallet"
    === "v.1"

        **Multi-organization enterprise backend**

        Version 1 built the Enterprise Business Wallet backend from scratch, with a focus on enterprise security and multi-organization support.

        **Main highlights for integrators:**

        - **Reactive, cloud-ready architecture:** Backend built on Java 25, Spring Boot and WebFlux, with native support for AWS deployments.

        - **Fully isolated multi-organization support:** Each organization has its own data space, with automatic migrations when new organizations are added. Aligned with the same model as the Issuer.

        - **Secure enterprise authentication:** Access via email OTP, JWT tokens with automatic rotation, token reuse detection and passkey management.

        - **Abuse protection:** Rate limiting by IP address and email account, plus HTTP security headers.

        - **Encrypted credential storage:** Wallet credentials are stored encrypted with AES-256-GCM. Their full lifecycle is managed (valid, suspended, revoked, expired) with traceable auditing.

        - **Integrated observability:** Standardized health endpoints, distributed tracing (OTLP) and Prometheus metrics, aligned with the rest of the platform components.

---

## Documentation

??? "Knowledge Base"
    === "2026-09"
        **Cross-application SSO guides**

        - New developer guide: [SSO across applications](../developers/guides/sso-multi-app.en.md), how to integrate silent session reuse (`prompt=none`) between several applications in the same tenant.
        - New administration guide: [Verifier — Single Sign-On](../admin/verifier-sso.en.md), managing the eligible applications catalog and the session duration.

    === "2026-07"
        **EUDIStack Sandbox URL update fix**

        - The Sandbox domain has been updated to sandbox.stg.eudistack.net (the old domain, sandbox-stg.eudistack.net, was still in use).

        **English guides now available**

        - The user guides (EUDIW Wallet and Business Wallet) and the administrator guides (Issuer Portal) are now fully available in English, in addition to Spanish.
        - You can switch between Spanish and English at any time from the language selector in the header.
