# API de consulta

Consulta desde tu backend lo que ha emitido tu organización: el detalle de una emisión por su identificador, el listado de lo emitido y la búsqueda de las atestaciones de una persona por un dato propio, como el DNI. Sin pasar por el Portal de Administración.

[:material-file-document: **Contrato OpenAPI de consulta**](../api-reference/issuer.md#api-de-consulta){ .md-button .md-button--primary }
[:material-cog-transfer: API de emisión](issuance-api.md){ .md-button }

??? tip "¿Cuándo usar esta guía?"
    - Necesitas saber en qué estado está una atestación que emitiste, por ejemplo antes de revocarla.
    - Quieres alimentar tus propios sistemas con lo emitido.
    - Conoces a la persona por un dato tuyo (DNI, número de colegiado) y no por el identificador del proceso de emisión.

??? info "Requisitos previos"
    - Un `access_token` del Verifier, obtenido con la credencial de máquina de tu sistema, como en el [paso 1 de la API de emisión](issuance-api.md#paso-1-obten-el-token-de-acceso).
    - El poder `Onboarding · Execute` en el dominio de tu tenant. Ves los procesos de emisión de tu organización; el administrador del tenant ve los de todo el tenant.

---

## Estado de las funciones

La guía describe el contrato completo de la API. Algunas partes aún no están desplegadas: están marcadas como **Próximamente** en su sección y no conviene usarlas hasta que lo estén.

| Función | Estado |
|---|---|
| Detalle de una emisión por su identificador | Disponible, con menos campos que el contrato |
| Listado de lo emitido | Parcial: hoy devuelve todo tu ámbito en una sola respuesta e ignora los parámetros |
| Filtros por estado y por tipo, y paginación | Próximamente |
| Búsqueda por un identificador de la persona | Próximamente |
| Errores con los códigos de esta guía | Parcial: hoy un detalle que no existe devuelve `credential_not_found` |
| Límite de volumen | Próximamente |

---

## Detalle de una emisión

!!! info "Disponible, con menos campos"
    Hoy la respuesta incluye `procedure_id`, `credential_configuration_id`, `lifeCycleStatus`, `credential` y `email`. El resto de campos del contrato llegarán en una próxima versión.

`GET /issuer/api/v1/issuances/{id}`

`{id}` es el identificador del proceso de emisión: el que devuelve la emisión en la cabecera `Location`, o el `procedure_id` de un listado o una búsqueda. La respuesta incluye los datos de la atestación en `credential`.

```http
GET /issuer/api/v1/issuances/0b6f1d2e-8c4a-4b1f-9e2d-7a5c3f8e1b90 HTTP/1.1
Host: sandbox.stg.eudistack.net
Authorization: Bearer <access_token>
```

```json
{
  "procedure_id": "0b6f1d2e-8c4a-4b1f-9e2d-7a5c3f8e1b90",
  "organization_identifier": "VATES-B00000000",
  "credential_configuration_id": "learcredential.employee.w3c.4",
  "credential_format": "jwt_vc_json",
  "lifeCycleStatus": "VALID",
  "subject": "María García López",
  "email": "maria.garcia@empresa.example",
  "delivery": "email",
  "valid_from": "2026-10-01T09:12:44Z",
  "valid_until": "2027-10-01T09:12:44Z",
  "created": "2026-10-01T09:10:02Z",
  "created_by": "sistema-rrhh@empresa.example",
  "updated": "2026-10-01T09:12:44Z",
  "credential": { "...": "datos de la atestación" }
}
```

Cada acceso al detalle queda registrado, porque devuelve datos personales. Pídelo solo cuando necesites los datos de la atestación: para saber el estado basta el listado o la búsqueda.

Un proceso que no existe y uno que está fuera de tu ámbito reciben la misma respuesta: 404 `issuance_not_found`.

### Estados de un proceso de emisión

| Estado | Significado |
|---|---|
| `DRAFT` | Emisión creada; el titular aún no ha recogido la atestación. |
| `WITHDRAWN` | Emisión retirada antes de que el titular la recogiera. |
| `ISSUED` | Atestación entregada con una fecha de inicio de vigencia futura. |
| `VALID` | Atestación vigente. Es la única que se puede revocar. |
| `REVOKED` | Atestación revocada. Es definitivo. |
| `EXPIRED` | Atestación caducada. |
| `ARCHIVED` | Proceso terminado y archivado. |

---

## Listar lo emitido

!!! info "Próximamente"
    Hoy el listado devuelve todos los procesos de tu ámbito en una sola respuesta e ignora los parámetros de esta sección. Los filtros y la paginación llegarán en una próxima versión. Mientras tanto, no envíes parámetros ni dependas de que el resultado esté paginado.

`GET /issuer/api/v1/issuances`

Devuelve los procesos de emisión de tu ámbito, del más reciente al más antiguo y en todos los estados. Los filtros se combinan entre sí:

| Parámetro | Descripción |
|---|---|
| `status` | Uno o varios estados separados por comas, por ejemplo `VALID,REVOKED`. |
| `credential_configuration_id` | Solo un tipo de atestación. |
| `limit` | Resultados por página, de 1 a 200. Por defecto, 50. |
| `cursor` | Valor de `next_cursor` de la página anterior. |

Para recorrer todo el listado, repite la petición con `cursor` igual al `next_cursor` recibido hasta que valga `null`. El listado no incluye los datos de la atestación: para verlos, pide el detalle.

```json
{
  "credential_procedures": [
    {
      "credential_procedure": {
        "procedure_id": "0b6f1d2e-8c4a-4b1f-9e2d-7a5c3f8e1b90",
        "organization_identifier": "VATES-B00000000",
        "credential_type": "learcredential.employee.w3c.4",
        "status": "VALID",
        "subject": "María García López",
        "updated": "2026-10-01T09:12:44Z"
      }
    }
  ],
  "next_cursor": null
}
```

---

## Buscar por un identificador de la persona

!!! info "Próximamente"
    La búsqueda por identificador aún no está disponible. Hoy el listado ignora `identifier_type` e `identifier` y devuelve todo tu ámbito: no lo tomes como un resultado de búsqueda.

`GET /issuer/api/v1/issuances?identifier_type=<tipo>&identifier=<valor>`

Cada tipo de atestación del catálogo de tu tenant puede declarar **identificadores de búsqueda**: datos de la atestación por los que después se busca, como el DNI (`dni`) o el número de colegiado (`colegiado`). Los nombres los define el catálogo de tu tenant.

```http
GET /issuer/api/v1/issuances?identifier_type=dni&identifier=12345678Z&status=VALID HTTP/1.1
Host: sandbox.stg.eudistack.net
Authorization: Bearer <access_token>
```

- La coincidencia es exacta. Envía el valor con el mismo formato con el que se emitió y codifícalo en la URL.
- `identifier_type` e `identifier` van siempre juntos. Uno sin el otro se rechaza con 400 `BAD_REQUEST`.
- Un `identifier_type` que no declara ningún tipo del catálogo se rechaza con 400 `unknown_identifier_type`.
- Una búsqueda sin resultados no es un error: devuelve 200 con la lista vacía.
- Se combina con los filtros del listado, por ejemplo con `status=VALID` para encontrar lo que se puede revocar.

EUDIStack no busca el valor en claro. Al emitir guarda una huella de cada identificador, calculada con una clave propia de tu tenant, y al buscar compara la huella del valor recibido. Las huellas se calculan también para lo emitido antes de que el identificador existiera en el catálogo.

---

## Errores

Los errores se devuelven como `application/problem+json` (RFC 9457). Programa tu lógica sobre `type`, no sobre `title` ni `detail`.

| HTTP | `type` | Causa | Qué hacer |
|---|---|---|---|
| 400 | `BAD_REQUEST` | Parámetro con formato incorrecto, identificador sin tipo o al revés, cursor no válido, o `{id}` que no es un UUID. | Corrige la petición. |
| 400 | `unknown_identifier_type` | El catálogo de tu tenant no declara ese identificador de búsqueda. | Revisa `identifier_type`. |
| 401 | `invalid_token`, `authentication_error` | Falta el token, no es válido o ha caducado. | Pide un token nuevo. |
| 403 | `insufficient_permission` | Los poderes de tu credencial no permiten consultar. | Revisa los poderes de la credencial de máquina. |
| 404 | `issuance_not_found` | El proceso no existe o está fuera de tu ámbito. | Revisa el identificador. |
| 429 | `rate_limited` | Se ha superado el límite de volumen. | Espera lo que indique `Retry-After`. |

El límite de volumen de la búsqueda es más bajo que el del listado y el detalle (60 frente a 300 peticiones por minuto y organización para sistemas), para impedir que se recorra el censo de personas.

??? warning "Consideraciones de seguridad"
    - El detalle devuelve datos personales del titular. Pídelo solo cuando los necesites y no los guardes más tiempo del necesario.
    - Busca solo por identificadores de personas con las que tu organización tiene relación: cada búsqueda queda registrada, con quién la hizo y el tipo de identificador.

---

## Referencia completa

El contrato completo de la API de consulta, con todos los campos, respuestas y códigos de error, está en la [referencia de la API del Issuer](../api-reference/issuer.md#api-de-consulta).
