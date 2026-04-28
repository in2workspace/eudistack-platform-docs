# Multi-tenant

<!-- TODO: confirmar URLs y modelo de subdominio definitivo -->

EUDIStack es una plataforma **multi-tenant**: cada cliente es un *tenant* aislado con su propia base de datos, configuración, branding y URLs.

## Aislamiento por tenant

| Recurso | Aislamiento |
|---|---|
| Base de datos | Schema dedicado en PostgreSQL (`schema-per-tenant`). |
| Frontends (Wallet, Portal) | Despliegue/distribución dedicada con branding propio. |
| Backends (Issuer, Verifier, EBW) | **Compartidos**, distinguen tenant por host header. |
| Claves criptográficas | Independientes por tenant. |
| Status Lists | URLs propias por tenant. |

## Identificación del tenant

El tenant se resuelve a partir del **subdominio** de la petición. Por ejemplo:

- `issuer.acme.eudistack.net` → tenant `acme`.
- `verifier.bizco.eudistack.net` → tenant `bizco`.

Tu integración no necesita pasar el tenant explícitamente: viene implícito en la URL que usas.

## Implicaciones para integradores

- **Cross-tenant no es posible**: una credencial emitida en tenant A no la puede recibir un wallet del tenant B (mismo origen requerido).
- **Una integración por tenant**: si trabajas con varios tenants, configura un cliente por cada uno.
- **Branding**: cada tenant ve su logo, paleta y nombres en wallet, portales y emails. La identidad es transparente para tu integración.
