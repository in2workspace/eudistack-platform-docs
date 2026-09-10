# Solución de problemas — Developers

Errores frecuentes durante la integración y cómo resolverlos.

=== "OID4VCI"

    ??? bug "`invalid_grant` al canjear el código preautorizado"

        - **Causa probable**: el código ya fue consumido o caducó (TTL típico: 5 minutos).
        - **Solución**: pide al Issuer una nueva oferta. El código preautorizado es de un solo uso.

    ??? bug "El wallet rechaza la credencial tras emitirla"

        - **Causa probable**: discrepancia en el `vct` (Verifiable Credential Type) o falta de claves de verificación públicas en `/.well-known/jwt-issuer`.
        - **Solución**: comprueba que el `vct` declarado en metadata coincide con el de la credencial emitida.

=== "OID4VP"

    ??? bug "El verifier no recibe la presentación"

        - **Causa probable**: el `response_uri` no es accesible públicamente o tiene certificado inválido.
        - **Solución**: el wallet hace `direct_post` desde el dispositivo del usuario; el endpoint debe ser HTTPS público con cert válido.

    ??? bug "`invalid_presentation_submission`"

        - **Causa probable**: la consulta DCQL pide claims que la credencial no contiene, o la firma del Key Binding JWT no valida.
        - **Solución**: revisa que la credencial presentada cumple la query y que el `nonce` del KB-JWT coincide con el de la solicitud.

=== "DPoP"

    ??? bug "`invalid_dpop_proof`"

        - **Causa probable**: el `htu` o `htm` del DPoP no coincide con la URL/método real, o el `iat` está fuera de ventana.
        - **Solución**: verifica que regeneras el DPoP en cada request; no reutilices uno entre llamadas.

=== "Multi-tenant"

    ??? bug "`404` o `tenant not found` con dominio correcto"

        - **Causa probable**: el subdominio no está aprovisionado o la sesión apunta a otro tenant.
        - **Solución**: confirma con soporte que el tenant está activo y que tu cliente OAuth está registrado en él.

=== "SSO entre aplicaciones"

    ??? bug "`prompt=none` siempre devuelve `login_required`, aunque el usuario acaba de hacer login en otra aplicación"

        - **Causa probable**: el SSO no está habilitado para tu tenant, o las dos aplicaciones no cuelgan del mismo dominio raíz configurado (`rootDomain`) — sin eso, el navegador no comparte la cookie de sesión entre ellas.
        - **Solución**: confirma con tu administrador de tenant que el SSO está activo y que ambas aplicaciones sirven bajo el mismo dominio raíz. Ver [guía de administración](../admin/verifier-sso.md).

    ??? bug "`prompt=none` devuelve `interaction_required`"

        - **Causa probable**: hay una sesión SSO vigente, pero tu `client_id` no está en el catálogo de aplicaciones elegibles del tenant.
        - **Solución**: pide a tu administrador de tenant que dé de alta tu `client_id` en el catálogo SSO. Consulta la [guía de SSO entre aplicaciones](guides/sso-multi-app.md).

    ??? bug "El `id_token` de una reutilización silenciosa no trae el claim `sid`"

        - **Causa probable**: la petición no incluyó `prompt=none`, o cayó al flujo de login completo (por ejemplo, por `login_required`/`interaction_required` previos) — `sid` solo se emite cuando hay una sesión SSO activa detrás del token.
        - **Solución**: verifica en la respuesta de autorización si recibiste `code` directo (reutilización) o tuviste que pasar por el QR (login completo).

---

## ¿Sigues sin resolverlo?

[Contacta con soporte](../support.md). Adjunta:

- Endpoint y método exacto.
- Request completa (sanitizando secretos).
- Respuesta recibida.
- `trace-id` si la respuesta lo incluye.
