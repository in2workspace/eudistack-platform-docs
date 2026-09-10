# Verifier — Single Sign-On

Esta página es para quien administra la configuración del tenant, no para quien integra código. Si buscas cómo llamar a la petición `prompt=none` desde tu aplicación, consulta la [guía de integración para desarrolladores](../developers/guides/sso-multi-app.md).

El SSO entre aplicaciones permite que un usuario presente su credencial verificable una sola vez y que **todas las aplicaciones registradas bajo tu tenant** reutilicen esa autenticación durante un tiempo acotado, sin volver a pedir el QR.

---

## Antes de empezar: requisitos de activación

!!! warning "El SSO no se activa por sí solo"
    A diferencia de otras capacidades del tenant, el SSO entre aplicaciones requiere una activación explícita por parte del equipo de EUDIStack, porque depende de infraestructura que no es self-service hoy:

    1. **Dominio raíz común.** Todas las aplicaciones que quieras que compartan sesión deben colgar del mismo dominio (por ejemplo `*.miorganizacion.eudistack.net`). Es el dominio que se configura en la cookie de sesión (`Domain=<rootDomain>`) para que el navegador la comparta entre aplicaciones hermanas.
    2. **Flag `ssoEnabled` en tu tenant.** Sin él, el Verifier sigue funcionando exactamente igual que hoy (login individual por aplicación) y no persiste ninguna sesión ni cookie de SSO.
    3. **Cada aplicación cliente ya registrada como cliente OIDC** del tenant (precondición independiente del SSO: es la misma que necesitas para el [login con credencial](../developers/guides/oidc-idp.md)).

    [Contacta con soporte](../support.md) para solicitar la activación en tu entorno, indicando el dominio raíz que quieres usar y los `client_id` de las aplicaciones que participarán.

---

## Catálogo de aplicaciones elegibles

Que una aplicación esté registrada como cliente OIDC del tenant **no** basta para que reutilice la sesión SSO: debe estar además en el catálogo de aplicaciones elegibles. Es una lista de permitidos explícita y deliberada — por defecto, ninguna aplicación puede reutilizar la sesión aunque el tenant tenga SSO activo.

Se gestiona con una API REST protegida (requiere un token de acceso con rol de administrador de tenant, obtenido con tu credencial de administrador a través del propio Verifier):

=== "Listar el catálogo"
    ```http
    GET /tenant/sso/eligible-clients
    Authorization: Bearer <admin-access-token>
    ```

    ```json
    {
      "eligible_clients": ["portal-gestion", "portal-facturacion"]
    }
    ```

=== "Dar de alta una aplicación"
    ```http
    POST /tenant/sso/eligible-clients
    Authorization: Bearer <admin-access-token>
    Content-Type: application/json

    {
      "client_id": "portal-facturacion"
    }
    ```

    `201 Created`. La operación es idempotente: repetir el alta del mismo `client_id` no crea entradas duplicadas.

=== "Dar de baja una aplicación"
    ```http
    DELETE /tenant/sso/eligible-clients/{clientId}
    Authorization: Bearer <admin-access-token>
    ```

    `204 No Content`. El efecto es inmediato: la siguiente petición silenciosa de esa aplicación recibe `interaction_required`. No afecta a sesiones SSO ya activas de otros clientes.

!!! info "El cambio no exige que el usuario vuelva a autenticarse"
    Dar de alta una aplicación en el catálogo la habilita para reutilizar **cualquier sesión SSO ya activa** del tenant, no solo las que se establezcan a partir de ese momento. No hace falta pedir a los usuarios que vuelvan a presentar su credencial tras un cambio de catálogo.

---

## Duración de la sesión (TTL)

La sesión SSO expira por dos criterios independientes, el que se cumpla antes:

| Dimensión | Default del sistema | Rango permitido |
|---|---|---|
| TTL absoluto (desde el establecimiento) | 8 horas | 1 h – 24 h |
| TTL de inactividad (sin ninguna reutilización) | 30 minutos | 5 min – 60 min |

Los overrides por tenant se configuran junto con la activación inicial del SSO — [contacta con soporte](../support.md) si tu organización necesita valores distintos al default. Un valor fuera de rango se rechaza automáticamente y el sistema conserva el último válido: nunca se llega a establecer una sesión con TTL sin acotar.

Cambiar el TTL de un tenant **no** recalcula sesiones ya establecidas: siguen expirando según el valor vigente en el momento en que se crearon. El TTL de inactividad, en cambio, sí se evalúa siempre contra el valor vigente.

---

## Qué ve el usuario y qué queda registrado

- El usuario presenta su credencial una vez; en las siguientes aplicaciones del catálogo no ve QR ni pantalla de login mientras la sesión esté vigente.
- Cada establecimiento, reutilización, denegación (`interaction_required`) y expiración detectada queda registrado como evento auditable, sin exponer nunca el identificador del usuario en claro ni el identificador de sesión completo en los logs.
- El `id_token` que recibe cada aplicación durante una reutilización incluye el claim `sid`, común a todas las aplicaciones que comparten esa sesión — útil como criterio de correlación si necesitas cruzar trazas entre aplicaciones.

---

## Resolución de problemas

??? bug "Una aplicación del catálogo sigue recibiendo `interaction_required`"
    - Confirma que el `client_id` que usa la aplicación en su petición OIDC coincide exactamente (mayúsculas/minúsculas incluidas) con el dado de alta en el catálogo.
    - Comprueba que la aplicación cuelga del mismo dominio raíz configurado para el SSO del tenant — si el dominio no coincide, el navegador ni siquiera envía la cookie de sesión, y el resultado observado también es `interaction_required`/`login_required` según el caso.

??? bug "Los usuarios tienen que volver a autenticarse más a menudo de lo esperado"
    - Revisa el TTL de inactividad configurado: si el usuario deja pasar más tiempo del configurado entre aplicaciones, la sesión expira aunque el TTL absoluto no se haya cumplido.
    - Verifica que ninguna aplicación está enviando `prompt=login` o un `max_age` más restrictivo de lo previsto — ambos fuerzan una autenticación fresca aunque la sesión SSO sea válida.

??? bug "Quiero dar de baja a todos los usuarios de golpe (incidente de seguridad)"
    Contacta con el equipo de EUDIStack a través de la [página de soporte](../support.md) indicando el tenant afectado. La invalidación masiva de sesiones activas es una operación asistida por el equipo de plataforma, no una acción de autoservicio en esta primera iteración.

---

¿Necesitas más detalle del lado de integración (manejo de `prompt=none`, contenido del `id_token`, forzar autenticación fresca)? Consulta la [guía para desarrolladores](../developers/guides/sso-multi-app.md).
