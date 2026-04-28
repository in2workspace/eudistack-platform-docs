# Documentación para developers

EUDIStack expone una plataforma SaaS multi-tenant para emitir, custodiar y verificar credenciales digitales conformes a eIDAS 2.0. Esta sección está pensada para integradores que conectan EUDIStack con sus sistemas (IdP, ERP, portales corporativos, etc.).

<div class="grid cards" markdown>

-   :material-rocket-launch: [**Primeros pasos**](getting-started/index.md)

    Quickstart con el sandbox de EUDIStack y tu primera integración funcional en menos de 30 minutos.

-   :material-book-open-page-variant: [**Conceptos**](concepts/index.md)

    Los protocolos clave: OID4VCI, OID4VP, SD-JWT VC, DPoP, multi-tenant. Lo mínimo que necesitas entender antes de programar.

-   :material-format-list-checks: [**Guías**](guides/index.md)

    Recetas para casos de uso concretos: OIDC IdP, SCIM, API Direct Issuance.

-   :material-api: [**Referencia API**](api-reference/index.md)

    Endpoints, ejemplos request/response y especificaciones OpenAPI completas.

</div>

---

## ¿Algo no funciona?

Revisa la sección de [solución de problemas para developers](troubleshooting.md) — cubre los errores más habituales con su causa raíz y solución.

!!! eudistack "EUDIStack es SaaS"
    No necesitas desplegar nada. Trabajamos en modo gestionado: tú integras tus sistemas con nuestras APIs y nosotros operamos la plataforma. Si necesitas un entorno aislado, te aprovisionamos un **tenant dedicado** con sus propias URLs, base de datos y configuración.
