# SD-JWT VC

<!-- TODO: ampliar con descomposición de un SD-JWT real -->

**SD-JWT VC** (Selective Disclosure JWT Verifiable Credentials) es el formato de credencial que EUDIStack usa por defecto. Permite que el holder revele **solo los atributos necesarios** en cada presentación, manteniendo la firma del emisor verificable.

## Por qué SD-JWT

- **Privacidad por diseño**: el wallet decide qué se revela, no el verifier.
- **Sin nuevas firmas por cada presentación**: la credencial se emite una vez, se presenta muchas.
- **Compatibilidad con OAuth/JWT**: stack ya conocido por la mayoría de developers.

## Estructura

Una credencial SD-JWT VC se compone de:

1. **Issuer-signed JWT**: contiene los claims base + hashes de los disclosures + clave del holder.
2. **Disclosures**: una lista de tripletas `(salt, claim_name, claim_value)` que el holder puede entregar selectivamente.
3. **Key Binding JWT**: prueba al verifier que quien presenta es el holder legítimo (firmado con la clave del holder).

<!-- TODO: ejemplo completo de cada parte -->

## Referencias

- Especificación: [SD-JWT VC (RFC 9901)](https://datatracker.ietf.org/doc/rfc9901/)
- HAIP profile: [estándares](../../reference/standards.md)
