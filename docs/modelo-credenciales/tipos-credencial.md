---
title: Tipos de Credencial
description: Catalogo de tipos de credencial soportados en EUDIStack
---

# Tipos de Credencial

Este catalogo documenta los tipos de credencial soportados por EUDIStack, alineados con las especificaciones del ARF de la Comision Europea.

## Credenciales de identidad

### VerifiableId (PID)

Credencial de identidad personal basada en el **Person Identification Data (PID)**.

| Atributo | Descripcion | Obligatorio |
|----------|-------------|-------------|
| **Nombre** | VerifiableId | - |
| **Ambito** | Identidad personal | - |
| **Emisores tipicos** | Gobiernos, autoridades de identidad | - |
| **Validez** | 5-10 anos | - |

#### Atributos disponibles

| Claim | Tipo | Descripcion | Requerido |
|-------|------|-------------|-----------|
| `given_name` | string | Nombre(s) de pila | Si |
| `family_name` | string | Apellido(s) | Si |
| `birth_date` | date | Fecha de nacimiento | Si |
| `age_over_18` | boolean | Mayor de 18 anos | No |
| `age_over_21` | boolean | Mayor de 21 anos | No |
| `nationality` | string | Nacionalidad (ISO 3166-1) | No |
| `document_number` | string | Numero de documento | No |
| `gender` | integer | Genero (ISO 5218) | No |
| `birth_place` | string | Lugar de nacimiento | No |
| `portrait` | base64 | Fotografia | No |

#### Ejemplo

```json
{
  "@context": ["https://www.w3.org/2018/credentials/v1"],
  "type": ["VerifiableCredential", "VerifiableId"],
  "issuer": {
    "id": "did:web:gobierno.es",
    "name": "Gobierno de Espana"
  },
  "issuanceDate": "2024-01-15T10:00:00Z",
  "expirationDate": "2034-01-15T10:00:00Z",
  "credentialSubject": {
    "id": "did:key:z6Mk...",
    "given_name": "Maria",
    "family_name": "Garcia Lopez",
    "birth_date": "1990-05-20",
    "nationality": "ES",
    "age_over_18": true
  }
}
```

---

## Credenciales academicas

### VerifiableDiploma

Credencial para titulos academicos y diplomas universitarios.

| Atributo | Descripcion |
|----------|-------------|
| **Nombre** | VerifiableDiploma |
| **Ambito** | Educacion superior |
| **Emisores tipicos** | Universidades, centros de formacion |
| **Validez** | Permanente |

#### Atributos disponibles

| Claim | Tipo | Descripcion | Requerido |
|-------|------|-------------|-----------|
| `degree_name` | string | Nombre del titulo | Si |
| `degree_type` | string | Tipo (Bachelor, Master, etc.) | No |
| `awarding_institution` | object | Institucion que otorga | Si |
| `graduation_date` | date | Fecha de graduacion | No |
| `field_of_study` | string | Campo de estudio | No |
| `final_grade` | string | Calificacion final | No |
| `ects_credits` | number | Creditos ECTS | No |

#### Ejemplo

```json
{
  "@context": ["https://www.w3.org/2018/credentials/v1"],
  "type": ["VerifiableCredential", "VerifiableDiploma"],
  "issuer": {
    "id": "did:web:upc.edu",
    "name": "Universitat Politecnica de Catalunya"
  },
  "issuanceDate": "2024-06-15T12:00:00Z",
  "credentialSubject": {
    "id": "did:key:z6Mk...",
    "given_name": "Carlos",
    "family_name": "Martinez",
    "degree_name": "Grado en Ingenieria Informatica",
    "degree_type": "Bachelor",
    "awarding_institution": {
      "name": "Universitat Politecnica de Catalunya",
      "id": "https://www.upc.edu"
    },
    "graduation_date": "2024-06-01",
    "field_of_study": "Computer Science",
    "final_grade": "Sobresaliente",
    "ects_credits": 240
  }
}
```

---

## Credenciales empresariales

### LEARCredentialEmployee

Credencial de representacion legal para empleados (**Legal Entity Appointed Representative**).

| Atributo | Descripcion |
|----------|-------------|
| **Nombre** | LEARCredentialEmployee |
| **Ambito** | Representacion empresarial |
| **Emisores tipicos** | Organizaciones, empresas |
| **Validez** | Variable (tipicamente 1 ano) |

#### Atributos disponibles

| Claim | Tipo | Descripcion | Requerido |
|-------|------|-------------|-----------|
| `mandator` | object | Organizacion que otorga poderes | Si |
| `mandatee` | object | Persona que recibe los poderes | Si |
| `power` | array | Lista de poderes otorgados | Si |

#### Ejemplo

```json
{
  "@context": ["https://www.w3.org/2018/credentials/v1"],
  "type": ["VerifiableCredential", "LEARCredentialEmployee"],
  "issuer": {
    "id": "did:elsi:VATES-A12345678",
    "name": "ACME, SA"
  },
  "issuanceDate": "2024-01-15T10:00:00Z",
  "expirationDate": "2025-01-15T10:00:00Z",
  "credentialSubject": {
    "id": "did:key:z6Mk...",
    "mandator": {
      "commonName": "John Doe",
      "email": "john.doe@acme.com",
      "organization": "ACME, SA",
      "organizationIdentifier": "VATES-A12345678",
      "country": "ES"
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
  }
}
```

---

### LEARCredentialMachine

Credencial de representacion legal para maquinas/servicios (**Machine-to-Machine**).

| Atributo | Descripcion |
|----------|-------------|
| **Nombre** | LEARCredentialMachine |
| **Ambito** | Autenticacion M2M |
| **Emisores tipicos** | Organizaciones, empresas |
| **Validez** | Variable |

!!! info "Uso tipico"
    Se utiliza para autenticar servicios automatizados que actuan en nombre de una organizacion.

---

## Credenciales profesionales

### ProfessionalQualification

Credencial para cualificaciones y licencias profesionales.

| Atributo | Descripcion |
|----------|-------------|
| **Nombre** | ProfessionalQualification |
| **Ambito** | Cualificaciones profesionales |
| **Emisores tipicos** | Colegios profesionales, autoridades |
| **Validez** | Variable (1-5 anos tipicamente) |

#### Atributos disponibles

| Claim | Tipo | Descripcion | Requerido |
|-------|------|-------------|-----------|
| `qualification_name` | string | Nombre de la cualificacion | Si |
| `profession` | string | Profesion | Si |
| `issuing_authority` | object | Autoridad emisora | Si |
| `license_number` | string | Numero de licencia | No |
| `valid_from` | date | Fecha de inicio de validez | No |
| `valid_until` | date | Fecha de fin de validez | No |

#### Ejemplo

```json
{
  "@context": ["https://www.w3.org/2018/credentials/v1"],
  "type": ["VerifiableCredential", "ProfessionalQualification"],
  "issuer": {
    "id": "did:web:colegio-medicos.es",
    "name": "Colegio Oficial de Medicos"
  },
  "issuanceDate": "2024-01-01T00:00:00Z",
  "expirationDate": "2025-12-31T23:59:59Z",
  "credentialSubject": {
    "id": "did:key:z6Mk...",
    "given_name": "Ana",
    "family_name": "Fernandez",
    "qualification_name": "Licencia de Ejercicio Medico",
    "profession": "Medicina General",
    "issuing_authority": {
      "name": "Colegio Oficial de Medicos de Barcelona"
    },
    "license_number": "COM-BCN-12345",
    "valid_from": "2024-01-01",
    "valid_until": "2025-12-31"
  }
}
```

---

## Credenciales de atestacion

### VerifiableAttestation

Credencial generica para atestaciones de atributos especificos.

| Atributo | Descripcion |
|----------|-------------|
| **Nombre** | VerifiableAttestation |
| **Ambito** | Atestaciones generales |
| **Emisores tipicos** | Cualquier entidad autorizada |
| **Validez** | Variable |

#### Casos de uso comunes

- Certificado de residencia
- Certificado de empadronamiento
- Atestacion de empleo
- Certificado de antecedentes

---

## Matriz de compatibilidad

| Tipo | JWT VC | SD-JWT VC | mDOC |
|------|--------|-----------|------|
| VerifiableId | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| VerifiableDiploma | :white_check_mark: | :white_check_mark: | :x: |
| LEARCredentialEmployee | :white_check_mark: | :white_check_mark: | :x: |
| LEARCredentialMachine | :white_check_mark: | :x: | :x: |
| ProfessionalQualification | :white_check_mark: | :white_check_mark: | :x: |
| VerifiableAttestation | :white_check_mark: | :white_check_mark: | :x: |

## Divulgacion selectiva

Las credenciales SD-JWT VC permiten divulgar solo los atributos necesarios:

```mermaid
flowchart LR
    subgraph Credencial Completa
        A[given_name]
        B[family_name]
        C[birth_date]
        D[nationality]
        E[document_number]
    end

    subgraph Presentacion Selectiva
        F[age_over_18]
    end

    Credencial Completa -->|Divulgacion selectiva| Presentacion Selectiva
```

!!! tip "Privacidad por diseno"
    Usa divulgacion selectiva siempre que sea posible para minimizar los datos compartidos.

## Siguiente paso

[:material-api: Ver Referencia API](../referencia-api/index.md){ .md-button }
