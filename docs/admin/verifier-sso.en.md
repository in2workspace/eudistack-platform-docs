# Verifier — Single Sign-On

This page is for whoever administers the tenant configuration, not for whoever integrates code. If you're looking for how to call the `prompt=none` request from your application, see the [developer integration guide](../developers/guides/sso-multi-app.en.md).

Cross-application SSO lets a user present their verifiable credential once and have **every application registered under your tenant** reuse that authentication for a bounded time, without asking for the QR code again.

---

## Before you start: activation requirements

!!! warning "SSO does not activate itself"
    Unlike other tenant capabilities, cross-application SSO requires explicit activation by the EUDIStack team, because it depends on infrastructure that isn't self-service today:

    1. **Common root domain.** Every application you want to share a session must hang off the same domain (for example `*.myorganization.eudistack.net`). It's the domain configured on the session cookie (`Domain=<rootDomain>`) so the browser shares it between sibling applications.
    2. **`ssoEnabled` flag on your tenant.** Without it, the Verifier keeps working exactly as it does today (individual login per application) and never persists any SSO session or cookie.
    3. **Each client application already registered as an OIDC client** of the tenant (an independent precondition — the same one you need for [login with credential](../developers/guides/oidc-idp.en.md)).

    [Contact support](../support.en.md) to request activation in your environment, indicating the root domain you want to use and the `client_id`s of the applications that will take part.

---

## Eligible applications catalog

An application being registered as an OIDC client of the tenant is **not** enough for it to reuse the SSO session: it must also be on the eligible applications catalog. It's an explicit, deliberate allowlist — by default, no application can reuse the session even if the tenant has SSO enabled.

It's managed through a protected REST API (requires an access token with tenant administrator role, obtained with your administrator credential through the Verifier itself):

=== "List the catalog"
    ```http
    GET /tenant/sso/eligible-clients
    Authorization: Bearer <admin-access-token>
    ```

    ```json
    {
      "eligible_clients": ["management-portal", "billing-portal"]
    }
    ```

=== "Add an application"
    ```http
    POST /tenant/sso/eligible-clients
    Authorization: Bearer <admin-access-token>
    Content-Type: application/json

    {
      "client_id": "billing-portal"
    }
    ```

    `201 Created`. The operation is idempotent: repeating the enrollment of the same `client_id` doesn't create duplicate entries.

=== "Remove an application"
    ```http
    DELETE /tenant/sso/eligible-clients/{clientId}
    Authorization: Bearer <admin-access-token>
    ```

    `204 No Content`. The effect is immediate: the next silent request from that application receives `interaction_required`. It does not affect other clients' already-active SSO sessions.

!!! info "The change doesn't require users to authenticate again"
    Adding an application to the catalog enables it to reuse **any already-active SSO session** in the tenant, not just ones established after the change. There's no need to ask users to present their credential again after a catalog change.

---

## Session duration (TTL)

The SSO session expires on two independent criteria, whichever is reached first:

| Dimension | System default | Allowed range |
|---|---|---|
| Absolute TTL (from establishment) | 8 hours | 1 h – 24 h |
| Idle TTL (without any reuse) | 30 minutes | 5 min – 60 min |

Per-tenant overrides are configured together with the initial SSO activation — [contact support](../support.en.md) if your organization needs different values than the default. An out-of-range value is automatically rejected and the system keeps the last valid one: a session with an unbounded TTL is never established.

Changing a tenant's TTL does **not** recalculate already-established sessions: they keep expiring according to the value in effect when they were created. The idle TTL, on the other hand, is always evaluated against the value currently in effect.

---

## What the user sees and what gets logged

- The user presents their credential once; on the following applications in the catalog they see no QR code or login screen while the session is valid.
- Every establishment, reuse, denial (`interaction_required`) and detected expiry is logged as an auditable event, without ever exposing the user's identifier in the clear or the full session identifier in the logs.
- The `id_token` each application receives during a reuse includes the `sid` claim, common to every application sharing that session — useful as a correlation key if you need to cross-reference traces between applications.

---

## Troubleshooting

??? bug "An application on the catalog keeps getting `interaction_required`"
    - Confirm that the `client_id` the application uses in its OIDC request matches exactly (case included) the one enrolled in the catalog.
    - Check that the application hangs off the same root domain configured for the tenant's SSO — if the domain doesn't match, the browser doesn't even send the session cookie, and the observed result is `interaction_required`/`login_required` depending on the case.

??? bug "Users have to authenticate more often than expected"
    - Review the configured idle TTL: if the user lets more time pass than configured between applications, the session expires even if the absolute TTL hasn't been reached.
    - Verify that no application is sending `prompt=login` or a `max_age` more restrictive than intended — both force a fresh authentication even when the SSO session is valid.

??? bug "I need to log out every user at once (security incident)"
    Contact the EUDIStack team through the [support page](../support.en.md), indicating the affected tenant. Mass invalidation of active sessions is a platform team-assisted operation, not a self-service action in this first iteration.

---

Need more detail on the integration side (handling `prompt=none`, `id_token` contents, forcing a fresh authentication)? See the [developer guide](../developers/guides/sso-multi-app.en.md).
