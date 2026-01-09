---
title: Guias de Integracion
description: Guias paso a paso para integrar EUDIStack en tu aplicacion
---

# Guias de Integracion

Esta seccion proporciona guias detalladas para integrar EUDIStack en tu aplicacion.

<div class="grid cards" markdown>

-   :material-flash:{ .lg .middle } **Inicio Rapido**

    ---

    Configura tu entorno y ejecuta tu primera integracion en minutos

    [:octicons-arrow-right-24: Ir](inicio-rapido.md)

-   :material-cog:{ .lg .middle } **Configuracion**

    ---

    Opciones de configuracion avanzadas para personalizar EUDIStack

    [:octicons-arrow-right-24: Ir](configuracion.md)

-   :material-shield-key:{ .lg .middle } **Autenticacion**

    ---

    Implementa flujos de autenticacion con EUDI Wallet

    [:octicons-arrow-right-24: Ir](autenticacion.md)

</div>

## Prerequisitos

Antes de comenzar, asegurate de tener:

- [ ] Docker instalado (version 20.10+)
- [ ] Git instalado
- [ ] Conocimiento basico de OAuth 2.0 / OpenID Connect
- [ ] Acceso a las credenciales de configuracion (si aplica)

## Arquitectura de integracion

El siguiente diagrama muestra como tu aplicacion se integra con EUDIStack:

```mermaid
flowchart LR
    subgraph Tu Aplicacion
        APP[Backend]
        FE[Frontend]
    end

    subgraph EUDIStack
        API[API Gateway]
        IS[Issuer Service]
        VS[Verifier Service]
    end

    subgraph Usuario
        WALLET[EUDI Wallet]
    end

    FE --> APP
    APP --> API
    API --> IS
    API --> VS
    IS --> WALLET
    WALLET --> VS
```

## Flujo tipico de integracion

1. **Configurar EUDIStack** - Despliega los servicios necesarios
2. **Registrar tu aplicacion** - Obtiene credenciales de cliente
3. **Implementar flujos** - Integra emision o verificacion de credenciales
4. **Probar** - Valida la integracion en entorno de pruebas
5. **Desplegar** - Pasa a produccion

## Soporte

Si encuentras problemas durante la integracion:

- :material-github: [Abre un issue en GitHub](https://github.com/in2workspace/eudistack/issues)
- :material-book: Consulta la [Referencia API](../referencia-api/index.md)
