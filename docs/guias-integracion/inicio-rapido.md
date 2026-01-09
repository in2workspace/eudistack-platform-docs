---
title: Inicio Rapido
description: Configura tu entorno y ejecuta tu primera integracion con EUDIStack
---

# Inicio Rapido

Esta guia te ayudara a configurar EUDIStack y ejecutar tu primera integracion en pocos minutos.

## Requisitos previos

Asegurate de tener instalado:

| Requisito | Version minima | Verificar instalacion |
|-----------|----------------|----------------------|
| Docker | 20.10+ | `docker --version` |
| Docker Compose | 2.0+ | `docker compose version` |
| Git | 2.0+ | `git --version` |

## Paso 1: Clonar el repositorio

```bash
git clone https://github.com/in2workspace/eudistack.git
cd eudistack
```

## Paso 2: Configurar variables de entorno

Copia el archivo de configuracion de ejemplo:

```bash
cp .env.example .env
```

Edita el archivo `.env` con tus configuraciones:

```env
# Configuracion basica
DOMAIN=localhost
PROTOCOL=http

# Puertos
ISSUER_PORT=8081
VERIFIER_PORT=8082

# Base de datos
DB_HOST=postgres
DB_PORT=5432
DB_NAME=eudistack
DB_USER=eudistack
DB_PASSWORD=your_secure_password
```

## Paso 3: Iniciar los servicios

```bash
docker compose up -d
```

Verifica que todos los servicios esten corriendo:

```bash
docker compose ps
```

Deberias ver algo similar a:

```
NAME                  STATUS
eudistack-issuer      running
eudistack-verifier    running
eudistack-postgres    running
```

## Paso 4: Verificar la instalacion

### Verificar el Issuer Service

```bash
curl http://localhost:8081/health
```

Respuesta esperada:

```json
{
  "status": "UP",
  "version": "1.0.0"
}
```

### Verificar el Verifier Service

```bash
curl http://localhost:8082/health
```

## Paso 5: Primera credencial

Emite tu primera credencial de prueba. En este ejemplo emitiremos una **LEARCredentialEmployee** (Legal Entity Appointed Representative):

=== "cURL"

    ```bash
    curl -X POST http://localhost:8081/backoffice/v1/issuances \
      -H "Content-Type: application/json" \
      -H "Authorization: Bearer <your-access-token>" \
      -d '{
        "schema": "LEARCredentialEmployee",
        "format": "jwt_vc_json",
        "payload": {
          "mandator": {
            "email": "john.doe@acme.com",
            "organization": "ACME, SA",
            "country": "ES",
            "commonName": "John Doe",
            "serialNumber": "87654321Z",
            "organizationIdentifier": "VATES-A12345678"
          },
          "mandatee": {
            "firstName": "Jane",
            "lastName": "Smith",
            "email": "j.smith@acme.com",
            "employeeId": "8001"
          },
          "power": [{
            "type": "domain",
            "domain": "ProductOffering",
            "function": "Create",
            "action": ["Create", "Update", "Delete"]
          }]
        },
        "operation_mode": "S"
      }'
    ```

=== "Python"

    ```python
    import requests

    response = requests.post(
        "http://localhost:8081/backoffice/v1/issuances",
        headers={"Authorization": "Bearer <your-access-token>"},
        json={
            "schema": "LEARCredentialEmployee",
            "format": "jwt_vc_json",
            "payload": {
                "mandator": {
                    "email": "john.doe@acme.com",
                    "organization": "ACME, SA",
                    "country": "ES",
                    "commonName": "John Doe",
                    "serialNumber": "87654321Z",
                    "organizationIdentifier": "VATES-A12345678"
                },
                "mandatee": {
                    "firstName": "Jane",
                    "lastName": "Smith",
                    "email": "j.smith@acme.com",
                    "employeeId": "8001"
                },
                "power": [{
                    "type": "domain",
                    "domain": "ProductOffering",
                    "function": "Create",
                    "action": ["Create", "Update", "Delete"]
                }]
            },
            "operation_mode": "S"
        }
    )
    print(response.json())
    ```

=== "JavaScript"

    ```javascript
    const response = await fetch('http://localhost:8081/backoffice/v1/issuances', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': 'Bearer <your-access-token>'
      },
      body: JSON.stringify({
        schema: 'LEARCredentialEmployee',
        format: 'jwt_vc_json',
        payload: {
          mandator: {
            email: 'john.doe@acme.com',
            organization: 'ACME, SA',
            country: 'ES',
            commonName: 'John Doe',
            serialNumber: '87654321Z',
            organizationIdentifier: 'VATES-A12345678'
          },
          mandatee: {
            firstName: 'Jane',
            lastName: 'Smith',
            email: 'j.smith@acme.com',
            employeeId: '8001'
          },
          power: [{
            type: 'domain',
            domain: 'ProductOffering',
            function: 'Create',
            action: ['Create', 'Update', 'Delete']
          }]
        },
        operation_mode: 'S'
      })
    });
    const data = await response.json();
    console.log(data);
    ```

!!! info "Modos de operacion"
    - **S (Sincrono)**: La credencial se emite inmediatamente
    - **A (Asincrono)**: La credencial se emite de forma diferida, util cuando requiere validacion manual

## Siguiente pasos

!!! success "Felicidades!"
    Has completado la configuracion inicial de EUDIStack.

Ahora puedes:

- [:material-cog: Configurar opciones avanzadas](configuracion.md)
- [:material-shield-key: Implementar flujos de autenticacion](autenticacion.md)
- [:material-api: Explorar la API completa](../referencia-api/index.md)

## Problemas comunes

??? question "Los servicios no inician"
    Verifica que los puertos no esten en uso:
    ```bash
    lsof -i :8081
    lsof -i :8082
    ```

??? question "Error de conexion a la base de datos"
    Asegurate de que el contenedor de PostgreSQL este corriendo:
    ```bash
    docker compose logs postgres
    ```

??? question "Necesito mas ayuda"
    - Revisa los logs: `docker compose logs -f`
    - [Abre un issue en GitHub](https://github.com/in2workspace/eudistack/issues)
