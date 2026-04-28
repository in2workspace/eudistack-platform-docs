# Verificación remota

<!-- TODO: contenido pendiente -->

Verificación de credenciales en flujos web/móvil donde el usuario y el verificador no están en el mismo espacio físico.

## Flujo

1. **Solicitud**: el verifier inicia una solicitud de presentación (`OID4VP`) y muestra un QR o un deep-link.
2. **Wallet del usuario**: escanea el QR o abre el link; revisa qué se le pide y confirma.
3. **Resultado**: el verifier recibe la presentación, valida firma, status y políticas, y muestra el resultado.

## Casos típicos

- Onboarding de clientes (KYC).
- Acceso a portales que exigen credencial corporativa.
- Validación de cualificación profesional online.

<!-- TODO: capturas del flujo desde la perspectiva del operador del verifier -->
