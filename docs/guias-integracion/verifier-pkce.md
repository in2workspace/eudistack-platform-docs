---
title: Integracion Verifier con PKCE
description: Guia de integracion para clientes publicos usando Authorization Code Flow con PKCE
---

# Integracion Verifier con PKCE

Esta guia explica como un cliente publico (aplicacion web o movil) puede integrarse con el Verifier usando Authorization Code Flow con PKCE.

## Introduccion

### Proposito

Esta guia proporciona los pasos completos para obtener y usar tokens de forma segura, desde iniciar la solicitud de autorizacion hasta intercambiar el codigo y llamar APIs protegidas.

### Alcance

- Integracion de clientes publicos con el Verifier usando Authorization Code Flow + PKCE
- Uso seguro de PKCE (Proof Key for Code Exchange) para prevenir intercepcion de codigos
- Perfil OAuth 2.1 authorization_code con code_verifier y code_challenge
- Adquisicion y uso de tokens para recursos protegidos

### Audiencia

- Desarrolladores frontend integrando aplicaciones web o moviles
- Integradores tecnicos configurando clientes publicos
- Ingenieros de seguridad revisando compliance

## Arquitectura

```mermaid
sequenceDiagram
    participant U as Usuario
    participant C as Cliente Publico
    participant V as Verifier (AS)
    participant W as Wallet

    C->>V: GET /authorize (code_challenge)
    V->>U: Pantalla de login
    U->>W: Escanea QR
    W->>V: Presenta credencial
    V-->>C: Redirect con code
    C->>V: POST /token (code + code_verifier)
    V-->>C: access_token + id_token
```

## Flujo de alto nivel

1. El usuario inicia el login desde el cliente publico
2. El cliente envia una solicitud de autorizacion al Verifier con PKCE
3. El usuario se autentica presentando su credencial desde el Wallet
4. El Verifier devuelve un codigo de autorizacion
5. El cliente intercambia el codigo por tokens usando el code_verifier
6. El Verifier emite access token e id token

## Pasos de integracion

### Prerrequisitos

- La entidad legal ha completado el onboarding en el ecosistema
- El usuario final tiene una **Verifiable Credential** emitida por su organizacion
- Metodo DID soportado: `did:key`
- El cliente publico esta registrado con su `redirect_uri` y `client_id`

---

### Paso 1: Emision de credencial de usuario

La organizacion usa el servicio Issuer para emitir una Verifiable Credential al empleado o usuario.

- La credencial esta vinculada al DID del usuario
- Incluye roles o permisos dentro de la organizacion
- Se almacena en la aplicacion Wallet del usuario

**Resultado**: El usuario tiene una credencial valida en su wallet, lista para presentar durante el login.

---

### Paso 2: Configuracion del cliente

**Tipo de cliente**: Publico (web o movil)

1. Registrar el cliente en el Verifier con su `redirect_uri`
2. Obtener y almacenar el `client_id` asignado
3. Implementar soporte PKCE (code_challenge / code_verifier)
4. Asegurar manejo seguro de redirects y parametro state

**Resultado**: El cliente esta configurado para iniciar el flujo Authorization Code con PKCE.

---

### Paso 3: Solicitud de autorizacion

El cliente inicia la autenticacion redirigiendo al usuario al Authorization Endpoint del Verifier.

**Parametros requeridos**:

| Parametro | Descripcion |
|-----------|-------------|
| `client_id` | Identificador del cliente |
| `redirect_uri` | URL de callback registrada |
| `response_type` | `code` |
| `scope` | ej: `openid learcredential` |
| `state` | Valor aleatorio para prevencion CSRF |
| `nonce` | Valor aleatorio para el id_token |
| `code_challenge` | Hash SHA-256 del code_verifier, codificado en base64url |
| `code_challenge_method` | `S256` |

Ejemplo:

```http
GET /oidc/auth?
client_id=https%3A%2F%2Fapp.cliente.com
&redirect_uri=https%3A%2F%2Fapp.cliente.com%2Fcallback
&response_type=code
&scope=openid%20learcredential
&nonce=1234567890abcdef
&state=abcdef1234567890
&code_challenge=AbCdEfGhIjKlMnOpQrStUvWxYz1234567890
&code_challenge_method=S256
HTTP/1.1
Host: verifier.eudistack.com
```

**Resultado**: El usuario es redirigido a la pantalla de login del Verifier y se autentica usando su Wallet.

---

### Paso 4: Respuesta de autorizacion

Despues de autenticacion exitosa, el Verifier redirige al usuario de vuelta al cliente con el codigo de autorizacion.

```http
HTTP/1.1 302 Found
Location: https://app.cliente.com/callback?
code=A1b2C3d4E5f6G7h8I9j0K1l2M3n4O5p6
&state=abcdef1234567890
```

**Resultado**: El cliente recibe el codigo de autorizacion y verifica que el `state` coincida con el enviado originalmente.

---

### Paso 5: Solicitud de token

El cliente intercambia el codigo por tokens llamando al Token Endpoint.

Esta peticion debe incluir el `code_verifier` original usado para generar el challenge.

```http
POST /oauth2/token HTTP/1.1
Host: verifier.eudistack.com
Content-Type: application/json

{
  "grant_type": "authorization_code",
  "client_id": "https://app.cliente.com",
  "code_verifier": "b7f9a4b52e6347a1b8f2c3d1a6...9e0f1ABCxyz",
  "code": "A1b2C3d4E5f6G7h8I9j0K1l2M3n4O5p6",
  "redirect_uri": "https://app.cliente.com/callback"
}
```

**Resultado**: El Verifier valida el codigo y code_verifier, y emite los tokens.

---

### Paso 6: Respuesta de token

```json
{
  "access_token": "eyJhbGciOiJFQ0RILUVTIiwiZ...qtAlx1oFIUpQQ",
  "expires_in": 3600,
  "id_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...p-QV30",
  "scope": "openid profile",
  "token_type": "Bearer"
}
```

| Token | Uso |
|-------|-----|
| `access_token` | Llamar APIs protegidas |
| `id_token` | Identificar al usuario autenticado |

!!! note "Tiempo de vida"
    Los tokens tienen un tiempo de vida limitado (tipicamente 1 hora).

---

### Paso 7: Usar access token

El cliente incluye el access token en el header Authorization al llamar APIs protegidas:

```http
GET /api/v1/resource HTTP/1.1
Host: api.eudistack.com
Authorization: Bearer eyJhbGciOiJFQ0RILUVTIiwiZ...
```

El Verifier valida el token, verifica su firma y expiracion, y concede acceso a los recursos.

---

## Generacion de PKCE

### Generar code_verifier

El `code_verifier` es una cadena aleatoria criptograficamente segura de 43-128 caracteres.

=== "JavaScript"

    ```javascript
    function generateCodeVerifier() {
      const array = new Uint8Array(32);
      crypto.getRandomValues(array);
      return base64URLEncode(array);
    }

    function base64URLEncode(buffer) {
      return btoa(String.fromCharCode(...buffer))
        .replace(/\+/g, '-')
        .replace(/\//g, '_')
        .replace(/=/g, '');
    }
    ```

=== "Python"

    ```python
    import secrets
    import base64

    def generate_code_verifier():
        random_bytes = secrets.token_bytes(32)
        return base64.urlsafe_b64encode(random_bytes).rstrip(b'=').decode()
    ```

### Generar code_challenge

El `code_challenge` es el hash SHA-256 del `code_verifier`, codificado en base64url.

=== "JavaScript"

    ```javascript
    async function generateCodeChallenge(verifier) {
      const encoder = new TextEncoder();
      const data = encoder.encode(verifier);
      const hash = await crypto.subtle.digest('SHA-256', data);
      return base64URLEncode(new Uint8Array(hash));
    }
    ```

=== "Python"

    ```python
    import hashlib
    import base64

    def generate_code_challenge(verifier):
        digest = hashlib.sha256(verifier.encode()).digest()
        return base64.urlsafe_b64encode(digest).rstrip(b'=').decode()
    ```

---

## Errores comunes a evitar

| Error | Riesgo |
|-------|--------|
| Mismatch en redirect_uri | Fallo de autenticacion |
| code_verifier/code_challenge incorrecto | Intercambio de token fallido |
| Valor de state debil o predecible | Riesgo CSRF |
| Reutilizar codigos de autorizacion | Rechazo del token |
| No manejar expiracion de tokens | Errores de acceso |
| Olvidar `Cache-Control: no-store` | Fuga de tokens |

## Siguiente paso

[:material-lock: Ver integracion cliente confidencial](verifier-confidencial.md){ .md-button }
