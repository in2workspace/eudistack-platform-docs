# EUDIStack Docs

Knowledge base oficial de la plataforma **EUDIStack**: emisión, custodia y verificación de credenciales digitales conformes a eIDAS 2.0.

Esta documentación está pensada para dos audiencias:

- **Usuarios finales** — guías de uso del Wallet (EUDIW y Business Wallet), Portal Issuer y Verifier.
- **Integradores / técnicos** — conceptos, guías de integración (OIDC, SCIM, API Direct Issuance) y referencia de API.

## Sitio publicado

- Producción: [https://docs.eudistack.net](https://docs.eudistack.net)
- Mirror GitHub Pages: [https://in2workspace.github.io/eudistack-platform-docs](https://in2workspace.github.io/eudistack-platform-docs)

## Trabajo local

```bash
# Clonar el repositorio
git clone https://github.com/in2workspace/eudistack-platform-docs.git
cd eudistack-platform-docs

# Crear venv e instalar dependencias
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

# Servir la documentación localmente (http://localhost:8000)
mkdocs serve

# O construir el sitio estático en site/
mkdocs build
```

## Estructura del repositorio

```
eudistack-platform-docs/
├── docs/                 # Contenido (Markdown, multilenguaje ES/EN)
│   ├── index.md          # Landing
│   ├── users/            # Guías para usuarios finales
│   ├── developers/       # Documentación para integradores
│   ├── reference/        # Glosario, estándares, changelog
│   └── support.md        # Soporte y contacto
├── overrides/            # Customización del theme Material
├── mkdocs.yml            # Configuración MkDocs
├── requirements.txt      # Dependencias Python
├── CNAME                 # Custom domain (docs.eudistack.net)
└── .github/workflows/    # Deploy automático a GitHub Pages
```

## Contribuir

1. Crea una rama (`git checkout -b feat/mi-cambio`).
2. Edita el contenido en `docs/`. Las páginas existen como pares `*.md` (Español, default) + `*.en.md` (Inglés).
3. Prueba localmente con `mkdocs serve --strict`.
4. Abre un Pull Request hacia `main`.

El despliegue a GitHub Pages (y por tanto a `docs.eudistack.net`) es automático en cada merge a `main`.

## Licencia

Apache 2.0 — ver [LICENSE](LICENSE) para más detalles.

---

**Mantenido por:** Altia Consultores, S.A.
**Copyright:** © 2024-2026 Altia Consultores, S.A.
