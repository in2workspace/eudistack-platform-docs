# CONTEXT - EUDIStack Documentation

**Última actualización:** 2026-01-09
**Estado:** Activo

---

## TL;DR

Este repositorio contiene la **documentación pública** de EUDIStack, diseñada para facilitar el entendimiento e integración con nuestros componentes y credenciales verificables. Está orientada a desarrolladores, integradores y organizaciones que quieran implementar soluciones de identidad digital europea.

**EUDIStack** es una implementación open-source del **European Business Wallet**, que permite a organizaciones emitir, gestionar y verificar credenciales digitales conforme a eIDAS 2.

---

## Qué es EUDIStack

EUDIStack es una implementación del **European Business Wallet** que permite a organizaciones **emitir, gestionar y verificar credenciales digitales** para sus empleados, colaboradores y socios de negocio, cumpliendo con la normativa europea (eIDAS 2).

### Componentes Principales

| Componente | Descripción | Función |
|------------|-------------|---------|
| **Issuer** | Emisor de credenciales verificables | Crea y gestiona credenciales digitales |
| **Wallet** | Cartera de identidad digital | Almacena y presenta credenciales |
| **Verifier** | Verificador de credenciales | Valida credenciales de forma instantánea |

```text
┌─────────────────────────────────────────────────────────────────┐
│                      EUDIStack Platform                         │
├─────────────────┬─────────────────┬─────────────────────────────┤
│     ISSUER      │     WALLET      │         VERIFIER            │
│   (Para org.)   │  (Para usuario) │        (Para org.)          │
├─────────────────┼─────────────────┼─────────────────────────────┤
│ • Panel admin   │ • App móvil     │ • Widget/SDK verificación   │
│ • APIs emisión  │ • iOS + Android │ • APIs validación           │
│ • Integraciones │ • White-label   │ • Integración SSO           │
└─────────────────┴─────────────────┴─────────────────────────────┘
```

---

## Propósito de esta Documentación

Esta documentación pública está diseñada para:

### 1. Entender el Ecosistema

- Arquitectura general del sistema
- Roles y flujos de interacción (Emisor, Titular, Verificador)
- Estándares implementados (OID4VCI, OID4VP, W3C VC, eIDAS 2)

### 2. Integrar con EUDIStack

- Guías paso a paso para integradores
- Referencia completa de APIs
- Ejemplos de código en múltiples lenguajes
- Configuración y despliegue

### 3. Comprender el Modelo de Credenciales

- Tipos de credenciales soportadas
- Ontología y esquemas JSON
- Ciclo de vida de credenciales
- Mecanismos de revocación

---

## Estándares Implementados

EUDIStack implementa los principales estándares de identidad digital:

| Estándar | Descripción | Uso en EUDIStack |
|----------|-------------|------------------|
| **eIDAS 2** | Regulación europea de identidad digital | Marco normativo general |
| **ARF** | Architecture Reference Framework (EUDIW) | Arquitectura de referencia |
| **OID4VCI** | OpenID for Verifiable Credential Issuance | Protocolo de emisión |
| **OID4VP** | OpenID for Verifiable Presentations | Protocolo de presentación |
| **W3C VC** | Verifiable Credentials Data Model 2.0 | Formato de credenciales |
| **SD-JWT VC** | Selective Disclosure JWT | Divulgación selectiva |
| **ISO/IEC 18013-5** | Mobile Driving License (mDL) | Credenciales móviles |

---

## Estructura de la Documentación

```text
docs/
├── index.md                    # Página principal
├── arquitectura/               # Arquitectura del sistema
│   ├── vision-general.md       # Visión general y capas
│   ├── componentes.md          # Descripción de componentes
│   └── flujos.md               # Flujos de trabajo
├── modelo-credenciales/        # Modelo de credenciales
│   ├── ontologia.md            # Ontología semántica
│   ├── esquemas.md             # Esquemas JSON
│   └── tipos-credencial.md     # Catálogo de tipos
├── referencia-api/             # Referencia de APIs
│   ├── endpoints.md            # Listado de endpoints
│   ├── autorizacion.md         # Autenticación y autorización
│   └── ejemplos.md             # Ejemplos de código
└── guias-integracion/          # Guías de integración
    ├── inicio-rapido.md        # Quick start
    ├── configuracion.md        # Configuración avanzada
    └── autenticacion.md        # Flujos de autenticación
```

---

## Audiencias

Esta documentación está diseñada para diferentes perfiles:

| Audiencia | Qué encontrarás | Secciones recomendadas |
|-----------|-----------------|------------------------|
| **Desarrolladores** | APIs, SDKs, ejemplos de código | Referencia API, Guías de integración |
| **Arquitectos** | Diseño del sistema, flujos, componentes | Arquitectura |
| **Product Managers** | Capacidades, casos de uso | Visión general, Modelo de credenciales |
| **Integradores** | Cómo conectar sistemas existentes | Guías de integración |

---

## Casos de Uso Principales

### Emisión de Credenciales (Issuer)

Una organización quiere emitir credenciales digitales a sus miembros:

1. Configurar el Issuer con los tipos de credenciales
2. Integrar con sistemas de RRHH o base de datos
3. Emitir credenciales individual o masivamente
4. Gestionar ciclo de vida (renovar, suspender, revocar)

### Almacenamiento y Presentación (Wallet)

Un usuario recibe y usa sus credenciales:

1. Recibir invitación para credencial
2. Instalar wallet y aceptar credencial
3. Presentar credencial cuando se requiera
4. Controlar qué datos comparte (divulgación selectiva)

### Verificación (Verifier)

Un servicio necesita verificar la identidad o atributos:

1. Solicitar presentación de credencial
2. Verificar firma criptográfica
3. Comprobar estado (no revocada)
4. Procesar atributos verificados

---

## Flujo Típico de Integración

```text
┌─────────────────────────────────────────────────────────────────┐
│                    Flujo de Credenciales                        │
└─────────────────────────────────────────────────────────────────┘

    ISSUER                      WALLET                     VERIFIER
       │                           │                           │
       │  1. Emite credencial      │                           │
       │──────────────────────────►│                           │
       │                           │                           │
       │                           │  2. Almacena credencial   │
       │                           │◄──────────────────────────│
       │                           │                           │
       │                           │  3. Presenta credencial   │
       │                           │──────────────────────────►│
       │                           │                           │
       │                           │  4. Verifica y acepta     │
       │                           │◄──────────────────────────│
       │                           │                           │
```

---

## Cómo Empezar

### Para Desarrolladores

1. Lee la [Guía de Inicio Rápido](guias-integracion/inicio-rapido.md)
2. Explora la [Referencia de APIs](referencia-api/)
3. Revisa los [Ejemplos de Código](referencia-api/ejemplos.md)

### Para Arquitectos

1. Comienza con la [Visión General de Arquitectura](arquitectura/vision-general.md)
2. Profundiza en [Componentes](arquitectura/componentes.md)
3. Estudia los [Flujos de Trabajo](arquitectura/flujos.md)

### Para Integradores

1. Revisa la [Guía de Configuración](guias-integracion/configuracion.md)
2. Implementa la [Autenticación](guias-integracion/autenticacion.md)
3. Conecta tus sistemas con las APIs

---

## Entornos Disponibles

| Entorno | URL Base | Propósito |
|---------|----------|-----------|
| **Sandbox** | `https://sandbox.eudistack.io/api` | Desarrollo y pruebas |
| **Production** | `https://api.eudistack.io` | Producción |
| **Local** | `http://localhost:8080` | Desarrollo local con Docker |

---

## Soporte y Comunidad

- **Documentación:** [https://in2workspace.github.io/eudistack-docs](https://in2workspace.github.io/eudistack-docs)
- **Repositorio:** [https://github.com/in2workspace](https://github.com/in2workspace)
- **Issues:** Reportar problemas en el repositorio correspondiente

---

## Licencia

EUDIStack es software open-source distribuido bajo licencia Apache 2.0.

---

**Mantenido por:** IN2 - Unidad de Identidad Digital
**Copyright:** 2024-2026 IN2
