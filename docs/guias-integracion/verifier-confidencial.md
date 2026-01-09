---
title: Integracion Cliente Confidencial
description: Guia de integracion para clientes confidenciales usando Authorization Code Flow con client_secret_jwt
---

# Integracion Cliente Confidencial

Esta guia explica como un cliente confidencial (backend o aplicacion web segura) puede integrarse con el Verifier usando Authorization Code Flow con autenticacion client_secret_jwt.

## Introduccion

### Proposito

Esta guia proporciona una vision completa del flujo, desde la solicitud de autorizacion firmada hasta la adquisicion de tokens, siguiendo las mejores practicas de OAuth 2.1.

### Alcance

- Integracion de clientes confidenciales con el Verifier usando Authorization Code Flow + client_secret_jwt
- Uso de JWT firmado como metodo de autenticacion del cliente
- Perfil OAuth 2.1 authorization_code con request_uri apuntando a un Authorization Request Object firmado
- Adquisicion y uso de tokens para APIs protegidas

### Audiencia

- Desarrolladores integrando backends o clientes confidenciales
- Integradores tecnicos configurando clientes OAuth 2.1 seguros
- Ingenieros de seguridad auditando autenticacion basada en tokens

## Arquitectura

```mermaid
sequenceDiagram
    participant C as Cliente Confidencial
    participant V as Verifier (AS)
    participant U as Usuario

    C->>C: Crea y firma Authorization Request Object (JWT)
    C->>V: GET /authorize (request_uri)
    V->>V: Obtiene y valida JWT desde request_uri
    V->>U: Pantalla de autenticacion
    U->>V: Autentica con Wallet
    V-->>C: Redirect con code
    C->>V: POST /token (client_secret_jwt)
    V-->>C: access_token + id_token + refresh_token
```

## Flujo de alto nivel

1. El cliente crea y firma un JWT con los parametros de autorizacion, disponible en su `request_uri`
2. El usuario es redirigido al Verifier, que obtiene y valida el JWT
3. Tras autenticacion y consentimiento exitosos, el Verifier emite un codigo de autorizacion
4. El cliente intercambia el codigo por tokens, autenticandose con client_secret_jwt
5. El Verifier valida el JWT y emite access, ID y refresh tokens
6. El cliente usa el access token para llamar APIs protegidas

## Pasos de integracion

### Prerrequisitos

- La entidad legal ha completado el onboarding en el ecosistema
- El LEAR ha obtenido una **LEARCredentialMachine** valida a traves del servicio Issuer
- Metodo DID soportado: `did:key`
- El cliente confidencial esta registrado en el Verifier con:
    - `client_id` (DID o URI)
    - `redirect_uri` (para respuesta de autorizacion)
    - `jwks_uri` (endpoint exponiendo las claves publicas del cliente)
- La clave privada del cliente esta almacenada de forma segura (HSM o vault)

---

### Paso 1: Emision de credencial de maquina

El LEAR de la organizacion emite una **LEARCredentialMachine** al servicio backend (el cliente confidencial).

- La credencial es una Verifiable Credential (VC) en formato JWT
- Esta vinculada al DID de la maquina, derivado de su clave publica
- La clave privada debe almacenarse de forma segura y nunca compartirse

**Resultado**: La maquina (cliente confidencial) posee una LEARCredentialMachine valida y su par de claves DID asociado.

---

### Paso 2: Configuracion del cliente

**Tipo de cliente**: Confidencial

1. Registrar el cliente en el Authorization Server del Verifier
2. Proporcionar el `jwks_uri` exponiendo las claves publicas usadas para firmar JWTs
3. Asegurar que `redirect_uri` esta pre-registrada y usa HTTPS
4. Implementar autenticacion JWT (client_secret_jwt)

**Resultado**: El cliente confidencial esta completamente configurado para autenticarse usando JWTs firmados.

---

### Paso 3: Solicitud de autorizacion

El cliente inicia el proceso de autorizacion redirigiendo al usuario al **Authorization Endpoint** con un **Authorization Request Object** firmado.

Este objeto es un JWT que contiene todos los parametros de autorizacion, alojado en un `request_uri`.

El Authorization Server obtiene este JWT, valida su firma contra el `jwks_uri` registrado del cliente, y procede con el flujo.

```http
GET /authorize?
response_type=code
&client_id=did:key:wejkdew87fwhef9833f4
&request_uri=https%3A%2F%2Fapp.cliente.com%2Frequest.jwt%2F3Gr...AdM
&state=af0ifjsldkj
&nonce=n-0S6_WzA2Mj
&scope=openid%20learcredential
HTTP/1.1
Host: verifier.eudistack.com
```

El Authorization Server:

1. Ejecuta la request_uri para obtener el Authorization Request Object
2. Valida el JWT usando la clave publica registrada bajo el jwks_uri del cliente

**Resultado**: El Authorization Server valida exitosamente la solicitud firmada y muestra la pantalla de login al usuario.

---

### Paso 4: Respuesta de autorizacion

Despues de que el usuario se autentica y autoriza el acceso, el Authorization Server redirige de vuelta al `redirect_uri` del cliente con un codigo de autorizacion.

```http
HTTP/1.1 302 FOUND
Location: https://app.cliente.com/callback?
code=SplxlOBeZQQYbYS6WxSbIA
&state=af0ifjsldkj
```

**Resultado**: El cliente confidencial recibe el codigo de autorizacion y verifica que el state coincida con su solicitud original para prevenir ataques CSRF.

---

### Paso 5: Solicitud de token

El cliente intercambia el codigo de autorizacion por tokens llamando al **Token Endpoint**. En este paso, el cliente se autentica usando `client_secret_jwt`, enviando un JWT firmado en el parametro client_assertion.

```http
POST /oauth/token HTTP/1.1
Host: verifier.eudistack.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&code=SplxlOBeZQQYbYS6WxSbIA
&redirect_uri=https%3A%2F%2Fapp.cliente.com%2Fcallback
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJFUzI1NiIsImtpZCI6IjIyIn0...
```

**Resultado**: El Authorization Server valida:

- La firma client_secret_jwt
- El codigo de autorizacion y redirect URI

Si es valido, emite access, ID y refresh tokens.

---

### Paso 6: Respuesta de token

```http
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store

{
  "access_token": "eyJhbGciOiJFQ0RILUVTIiwiZ...qtAlx1oFIUpQQ",
  "token_type": "Bearer",
  "expires_in": 3600,
  "refresh_token": "8xLOxBtZp8",
  "id_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...p-QV30"
}
```

| Token | Uso |
|-------|-----|
| `access_token` | Concede acceso a APIs protegidas |
| `id_token` | Identifica al sujeto autenticado |
| `refresh_token` | Permite obtener nuevos tokens sin interaccion del usuario |

---

### Paso 7: Usar access token

El cliente confidencial usa el access_token para llamar APIs protegidas:

```http
GET /api/v1/resource HTTP/1.1
Host: api.eudistack.com
Authorization: Bearer eyJhbGciOiJFQ0RILUVTIiwiZ...
```

---

## Construir el Client Assertion JWT

Para autenticarse con client_secret_jwt, el cliente debe construir un JWT firmado.

### Claims requeridos

| Claim | Descripcion |
|-------|-------------|
| `iss` | client_id del cliente |
| `sub` | client_id del cliente |
| `aud` | URL del Token Endpoint del Verifier |
| `jti` | UUID unico (previene replay) |
| `iat` | Tiempo actual en segundos |
| `exp` | iat + 60 segundos (corta duracion) |

### Ejemplo de payload

```json
{
  "iss": "did:key:wejkdew87fwhef9833f4",
  "sub": "did:key:wejkdew87fwhef9833f4",
  "aud": "https://verifier.eudistack.com/oauth/token",
  "jti": "3978344f-8596-4c3a-a978-8fcaba3903c5",
  "iat": 1541493724,
  "exp": 1541493784
}
```

### Header

```json
{
  "alg": "ES256",
  "kid": "did:key:wejkdew87fwhef9833f4#key-1"
}
```

!!! warning "Seguridad"
    El JWT debe firmarse con la clave privada del cliente y tener una expiracion corta para prevenir ataques de replay.

---

## Renovar tokens

Cuando el access_token expira, usa el refresh_token para obtener uno nuevo:

```http
POST /oauth/token HTTP/1.1
Host: verifier.eudistack.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token
&refresh_token=8xLOxBtZp8
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJFUzI1NiIs...
```

## Siguiente paso

[:material-api: Ver referencia API completa](../referencia-api/index.md){ .md-button }
