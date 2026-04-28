# API Direct Issuance

<!-- TODO: completar con curl ejemplos y casos reales -->

Emite credenciales programáticamente desde tu backend, sin pasar por la UI del Portal Issuer. Útil para integraciones masivas o automatizaciones por evento.

## Cuándo usar esta guía

- Tu sistema ya sabe **cuándo** debe emitir una credencial (alta de empleado, finalización de un curso, etc.).
- No quieres que un humano entre al Portal Issuer cada vez.
- Necesitas emitir en lote o desde un flujo automatizado (CI, webhooks, ETL).

## Flujo

1. **Autentícate** contra el Issuer usando un client credentials flow (M2M).
2. **POST `/credential-offer`** con el destinatario y los atributos.
3. **Recoge la URL/QR** y entrégasela al destinatario por tu canal (email transaccional, SMS, push).
4. **Suscríbete a webhooks** (opcional) para recibir el cambio de estado cuando el destinatario acepte.

<!-- TODO: ejemplos curl autenticación, oferta y webhook -->

## Buenas prácticas

- **Idempotencia**: incluye un `client_request_id` único por oferta para evitar duplicados.
- **Retries**: usa backoff exponencial — los endpoints son idempotentes con el ID anterior.
- **Privacidad**: no almacenes los atributos de la credencial en tu sistema más tiempo del necesario.
