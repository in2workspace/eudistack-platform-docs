# EUDIStack Documentation

Documentación técnica oficial de **EUDIStack** - Implementación del European Business Wallet para la emisión, gestión y verificación de credenciales verificables conforme a eIDAS 2.

## Qué es EUDIStack

EUDIStack es una implementación open-source del **European Business Wallet** que permite a organizaciones:

- **Emitir** credenciales digitales verificables (Issuer)
- **Almacenar** credenciales en una cartera móvil (Wallet)
- **Verificar** credenciales de forma instantánea (Verifier)

## Contenidos de la Documentación

| Sección | Descripción |
|---------|-------------|
| **Arquitectura** | Visión general del sistema, componentes y flujos |
| **Modelo de Credenciales** | Ontología, esquemas JSON y tipos de credenciales |
| **Referencia API** | Endpoints, autenticación y ejemplos de código |
| **Guías de Integración** | Inicio rápido, configuración y autenticación |

## Inicio Rápido

```bash
# Clonar el repositorio
git clone https://github.com/in2workspace/eudistack-docs.git

# Instalar dependencias
pip install -r requirements.txt

# Ejecutar documentación localmente
mkdocs serve
```

La documentación estará disponible en `http://localhost:8000`

## Estándares Implementados

- **eIDAS 2** - Regulación europea de identidad digital
- **ARF (EUDIW)** - Architecture Reference Framework
- **OID4VCI** - OpenID for Verifiable Credential Issuance
- **OID4VP** - OpenID for Verifiable Presentations
- **W3C VC** - Verifiable Credentials Data Model 2.0
- **SD-JWT VC** - Selective Disclosure JWT
- **ISO/IEC 18013-5** - Mobile Driving License

## Sitio Web

Documentación online: [https://in2workspace.github.io/eudistack-platform-docs](https://in2workspace.github.io/eudistack-platform-docs)

## Estructura del Repositorio

```
eudistack-docs/
├── docs/                    # Contenido de la documentación
│   ├── index.md             # Página principal
│   ├── arquitectura/        # Documentación de arquitectura
│   ├── modelo-credenciales/ # Modelo de credenciales
│   ├── referencia-api/      # Referencia de APIs
│   └── guias-integracion/   # Guías de integración
├── mkdocs.yml               # Configuración MkDocs
├── requirements.txt         # Dependencias Python
├── CONTEXT.md               # Contexto del proyecto
└── README.md                # Este archivo
```

## Contribuir

Las contribuciones son bienvenidas. Por favor:

1. Fork del repositorio
2. Crea una rama para tu feature (`git checkout -b feature/nueva-seccion`)
3. Commit de tus cambios (`git commit -m 'Añade nueva sección'`)
4. Push a la rama (`git push origin feature/nueva-seccion`)
5. Abre un Pull Request

## Repositorios Relacionados

| Componente | Repositorio |
|------------|-------------|
| Issuer API | [in2workspace/issuer-api](https://github.com/in2workspace/issuer-api) |
| Issuer UI | [in2workspace/issuer-ui](https://github.com/in2workspace/issuer-ui) |
| Wallet API | [in2workspace/wallet-api](https://github.com/in2workspace/wallet-api) |
| Wallet UI | [in2workspace/wallet-ui](https://github.com/in2workspace/wallet-ui) |
| Verifier | [in2workspace/verifier](https://github.com/in2workspace/verifier) |

## Licencia

Apache 2.0 - Ver [LICENSE](LICENSE) para más detalles.

---

**Mantenido por:** IN2 - Unidad de Identidad Digital
**Copyright:** 2024-2026 IN2
