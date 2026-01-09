---
title: Inici Rapid
description: Configura el teu entorn i executa la teva primera integracio amb EUDIStack
---

# Inici Rapid

Aquesta guia t'ajudara a configurar EUDIStack i executar la teva primera integracio en pocs minuts.

## Requisits previs

Assegura't de tenir installat:

| Requisit | Versio minima | Verificar installacio |
|----------|---------------|----------------------|
| Docker | 20.10+ | `docker --version` |
| Docker Compose | 2.0+ | `docker compose version` |
| Git | 2.0+ | `git --version` |

## Pas 1: Clonar el repositori

```bash
git clone https://github.com/in2workspace/eudistack.git
cd eudistack
```

## Pas 2: Configurar variables d'entorn

Copia l'arxiu de configuracio d'exemple:

```bash
cp .env.example .env
```

Edita l'arxiu `.env` amb les teves configuracions:

```env
# Configuracio basica
DOMAIN=localhost
PROTOCOL=http

# Ports
ISSUER_PORT=8081
VERIFIER_PORT=8082

# Base de dades
DB_HOST=postgres
DB_PORT=5432
DB_NAME=eudistack
DB_USER=eudistack
DB_PASSWORD=your_secure_password
```

## Pas 3: Iniciar els serveis

```bash
docker compose up -d
```

Verifica que tots els serveis estiguin corrent:

```bash
docker compose ps
```

Hauries de veure alguna cosa similar a:

```
NAME                  STATUS
eudistack-issuer      running
eudistack-verifier    running
eudistack-postgres    running
```

## Pas 4: Verificar la installacio

### Verificar l'Issuer Service

```bash
curl http://localhost:8081/health
```

Resposta esperada:

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

## Pas 5: Primera credencial

Emet la teva primera credencial de prova:

=== "cURL"

    ```bash
    curl -X POST http://localhost:8081/api/v1/credentials/offer \
      -H "Content-Type: application/json" \
      -d '{
        "credential_type": "VerifiableId",
        "claims": {
          "given_name": "Joan",
          "family_name": "Garcia",
          "birth_date": "1990-01-15"
        }
      }'
    ```

=== "Python"

    ```python
    import requests

    response = requests.post(
        "http://localhost:8081/api/v1/credentials/offer",
        json={
            "credential_type": "VerifiableId",
            "claims": {
                "given_name": "Joan",
                "family_name": "Garcia",
                "birth_date": "1990-01-15"
            }
        }
    )
    print(response.json())
    ```

=== "JavaScript"

    ```javascript
    const response = await fetch('http://localhost:8081/api/v1/credentials/offer', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        credential_type: 'VerifiableId',
        claims: {
          given_name: 'Joan',
          family_name: 'Garcia',
          birth_date: '1990-01-15'
        }
      })
    });
    const data = await response.json();
    console.log(data);
    ```

## Seguents passos

!!! success "Felicitats!"
    Has completat la configuracio inicial d'EUDIStack.

Ara pots:

- [:material-cog: Configurar opcions avancades](configuracion.md)
- [:material-shield-key: Implementar fluxos d'autenticacio](autenticacion.md)
- [:material-api: Explorar l'API completa](../referencia-api/index.md)

## Problemes comuns

??? question "Els serveis no inicien"
    Verifica que els ports no estiguin en us:
    ```bash
    lsof -i :8081
    lsof -i :8082
    ```

??? question "Error de connexio a la base de dades"
    Assegura't que el contenidor de PostgreSQL estigui corrent:
    ```bash
    docker compose logs postgres
    ```

??? question "Necessito mes ajuda"
    - Revisa els logs: `docker compose logs -f`
    - [Obre un issue a GitHub](https://github.com/in2workspace/eudistack/issues)
