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

Credencial de representacion legal para empleados basada en la ontologia **RPaM** (Representation of Powers and Mandates).

| Atributo | Descripcion |
|----------|-------------|
| **Nombre** | LEARCredentialEmployee |
| **Ambito** | Representacion empresarial / Mandato digital |
| **Emisores tipicos** | Proveedores de servicios de confianza (TSP), organizaciones |
| **Validez** | Variable (tipicamente 1 ano) |
| **Formatos** | W3C VC DM v2.0, SD-JWT VC |

!!! info "Mandato digital"
    Representa un e-Mandato conforme al marco eIDAS 2, formalizando la delegacion de poderes de una organizacion a un empleado identificado.

#### Estructura del mandato

| Claim | Tipo | Descripcion | Requerido |
|-------|------|-------------|-----------|
| `mandate.id` | string | Identificador unico del mandato (URN/UUID) | Si |
| `mandate.mandator` | object | Entidad juridica que otorga el mandato | Si |
| `mandate.mandatee` | object | Persona fisica que recibe los poderes | Si |
| `mandate.power` | array | Lista de poderes otorgados | Si |

#### Atributos del mandator

| Claim | Tipo | Descripcion |
|-------|------|-------------|
| `id` | string | DID de la organizacion (did:elsi:VAT...) |
| `organization` | string | Nombre de la organizacion |
| `organizationIdentifier` | string | Identificador fiscal (VATES-..., VATFR-...) |
| `country` | string | Codigo ISO del pais |
| `commonName` | string | Nombre del representante legal |
| `serialNumber` | string | Numero de identificacion del representante |
| `email` | string | Email de contacto |

#### Atributos del mandatee

| Claim | Tipo | Descripcion |
|-------|------|-------------|
| `id` | string | DID del empleado (vinculado criptograficamente) |
| `firstName` | string | Nombre |
| `lastName` | string | Apellidos |
| `email` | string | Email corporativo |
| `employeeId` | string | Identificador interno del empleado |

#### Estructura del power

| Claim | Tipo | Descripcion |
|-------|------|-------------|
| `type` | string | Tipo de poder (ej: "domain") |
| `domain` | string | Dominio de aplicacion (ej: "DOME") |
| `function` | string | Funcion autorizada (ej: "Onboarding") |
| `action` | array | Acciones permitidas (ej: ["execute", "create"]) |

#### Ejemplo (W3C VC)

```json title="sample/lear-credential-employee-v3.json"
--8<-- "docs/modelo-credenciales/sample/lear-credential-employee-v3.json"
```

[:material-graph: Ver ontologia completa](ontologia.md#learCredentialemployee){ .md-button .md-button--primary }

---

### LEARCredentialMachine

Credencial de representacion legal para entidades tecnicas (maquinas, servicios, dispositivos) que actuan en nombre de una organizacion.

| Atributo | Descripcion |
|----------|-------------|
| **Nombre** | LEARCredentialMachine |
| **Ambito** | Autenticacion M2M / Agentes tecnicos |
| **Emisores tipicos** | Proveedores de servicios de confianza (TSP) |
| **Validez** | Variable |
| **Formatos** | W3C VC DM v2.0 |

!!! info "Niveles semanticos"
    Distingue dos niveles: **identidades funcionales basicas** (autenticacion simple) y **agentes delegados** (con poderes explicitos para control de acceso).

#### Atributos especificos del mandatee (maquina)

| Claim | Tipo | Descripcion |
|-------|------|-------------|
| `id` | string | DID de la maquina |
| `domain` | string | Dominio asociado (ej: "dpas.goodair.fr") |
| `ipAddress` | string | Direccion IP del servicio |

#### Ejemplo

```json title="sample/lear-credential-machine-v2.json"
--8<-- "docs/modelo-credenciales/sample/lear-credential-machine-v2.json"
```

[:material-graph: Ver ontologia completa](ontologia.md#learcredentialmachine){ .md-button .md-button--primary }

---

### gx:LabelCredential

Credencial de cumplimiento Gaia-X que certifica que un Cloud Service Provider (CSP) cumple con criterios tecnicos, organizativos y legales.

| Atributo | Descripcion |
|----------|-------------|
| **Nombre** | gx:LabelCredential |
| **Ambito** | Certificacion de cumplimiento cloud |
| **Emisores tipicos** | Entidades de conformidad Gaia-X, TSPs |
| **Validez** | 3 meses (tipicamente) |
| **Formatos** | W3C VC DM v2.0 |

!!! info "Perfil DOME"
    El ecosistema DOME define 20 criterios obligatorios basados en el Gaia-X Compliance Document CD24.06.

#### Atributos principales

| Claim | Tipo | Descripcion |
|-------|------|-------------|
| `gx:labelLevel` | string | Nivel de cumplimiento ("BL" = Baseline) |
| `gx:engineVersion` | string | Version del compliance engine |
| `gx:rulesVersion` | string | Version del rulesBook (ej: "CD25.03") |
| `gx:compliantCredentials` | array | Criterios cumplidos con digest |
| `gx:validatedCriteria` | array | URIs de criterios validados |

#### Tipos de cumplimiento

| Tipo | Area |
|------|------|
| `gx:DataProtection` | Proteccion de datos |
| `gx:Cybersecurity` | Ciberseguridad |
| `gx:Portability` | Portabilidad |
| `gx:Sustainability` | Sostenibilidad |
| `gx:EuropeanControl` | Control europeo |

#### Ejemplo

```json title="sample/gx-label-credential.json"
--8<-- "docs/modelo-credenciales/sample/gx-label-credential.json"
```

[:material-graph: Ver ontologia completa](ontologia.md#gxlabelcredential){ .md-button .md-button--primary }

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
