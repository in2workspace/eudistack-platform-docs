---
title: Tipus de Credencial
description: Cataleg de tipus de credencial suportats a EUDIStack
---

# Tipus de Credencial

Aquest cataleg documenta els tipus de credencial suportats per EUDIStack, alineats amb les especificacions de l'ARF de la Comissio Europea.

## Credencials d'identitat

### VerifiableId (PID)

Credencial d'identitat personal basada en el **Person Identification Data (PID)**.

| Atribut | Descripcio |
|---------|------------|
| **Nom** | VerifiableId |
| **Ambit** | Identitat personal |
| **Emissors tipics** | Governs, autoritats d'identitat |
| **Validesa** | 5-10 anys |

#### Atributs disponibles

| Claim | Tipus | Descripcio | Requerit |
|-------|-------|------------|----------|
| `given_name` | string | Nom(s) de pila | Si |
| `family_name` | string | Cognom(s) | Si |
| `birth_date` | date | Data de naixement | Si |
| `age_over_18` | boolean | Major de 18 anys | No |
| `age_over_21` | boolean | Major de 21 anys | No |
| `nationality` | string | Nacionalitat (ISO 3166-1) | No |
| `document_number` | string | Numero de document | No |
| `gender` | integer | Genere (ISO 5218) | No |
| `birth_place` | string | Lloc de naixement | No |
| `portrait` | base64 | Fotografia | No |

#### Exemple

```json
{
  "@context": ["https://www.w3.org/2018/credentials/v1"],
  "type": ["VerifiableCredential", "VerifiableId"],
  "issuer": {
    "id": "did:web:govern.es",
    "name": "Govern d'Espanya"
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

## Credencials academiques

### VerifiableDiploma

Credencial per a titols academics i diplomes universitaris.

| Atribut | Descripcio |
|---------|------------|
| **Nom** | VerifiableDiploma |
| **Ambit** | Educacio superior |
| **Emissors tipics** | Universitats, centres de formacio |
| **Validesa** | Permanent |

#### Atributs disponibles

| Claim | Tipus | Descripcio | Requerit |
|-------|-------|------------|----------|
| `degree_name` | string | Nom del titol | Si |
| `degree_type` | string | Tipus (Bachelor, Master, etc.) | No |
| `awarding_institution` | object | Institucio que atorga | Si |
| `graduation_date` | date | Data de graduacio | No |
| `field_of_study` | string | Camp d'estudi | No |
| `final_grade` | string | Qualificacio final | No |
| `ects_credits` | number | Credits ECTS | No |

#### Exemple

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
    "given_name": "Carles",
    "family_name": "Martinez",
    "degree_name": "Grau en Enginyeria Informatica",
    "degree_type": "Bachelor",
    "awarding_institution": {
      "name": "Universitat Politecnica de Catalunya",
      "id": "https://www.upc.edu"
    },
    "graduation_date": "2024-06-01",
    "field_of_study": "Computer Science",
    "final_grade": "Excel·lent",
    "ects_credits": 240
  }
}
```

---

## Credencials professionals

### ProfessionalQualification

Credencial per a qualificacions i llicencies professionals.

| Atribut | Descripcio |
|---------|------------|
| **Nom** | ProfessionalQualification |
| **Ambit** | Qualificacions professionals |
| **Emissors tipics** | Collegis professionals, autoritats |
| **Validesa** | Variable (1-5 anys tipicament) |

#### Atributs disponibles

| Claim | Tipus | Descripcio | Requerit |
|-------|-------|------------|----------|
| `qualification_name` | string | Nom de la qualificacio | Si |
| `profession` | string | Professio | Si |
| `issuing_authority` | object | Autoritat emissora | Si |
| `license_number` | string | Numero de llicencia | No |
| `valid_from` | date | Data d'inici de validesa | No |
| `valid_until` | date | Data de fi de validesa | No |

#### Exemple

```json
{
  "@context": ["https://www.w3.org/2018/credentials/v1"],
  "type": ["VerifiableCredential", "ProfessionalQualification"],
  "issuer": {
    "id": "did:web:collegi-metges.cat",
    "name": "Collegi Oficial de Metges"
  },
  "issuanceDate": "2024-01-01T00:00:00Z",
  "expirationDate": "2025-12-31T23:59:59Z",
  "credentialSubject": {
    "id": "did:key:z6Mk...",
    "given_name": "Anna",
    "family_name": "Fernandez",
    "qualification_name": "Llicencia d'Exercici Medic",
    "profession": "Medicina General",
    "issuing_authority": {
      "name": "Collegi Oficial de Metges de Barcelona"
    },
    "license_number": "COM-BCN-12345",
    "valid_from": "2024-01-01",
    "valid_until": "2025-12-31"
  }
}
```

---

## Credencials d'atestacio

### VerifiableAttestation

Credencial generica per a atestacions d'atributs especifics.

| Atribut | Descripcio |
|---------|------------|
| **Nom** | VerifiableAttestation |
| **Ambit** | Atestacions generals |
| **Emissors tipics** | Qualsevol entitat autoritzada |
| **Validesa** | Variable |

#### Casos d'us comuns

- Certificat de residencia
- Certificat d'empadronament
- Atestacio d'ocupacio
- Certificat d'antecedents

---

## Matriu de compatibilitat

| Tipus | JWT VC | SD-JWT VC | mDOC |
|-------|--------|-----------|------|
| VerifiableId | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| VerifiableDiploma | :white_check_mark: | :white_check_mark: | :x: |
| ProfessionalQualification | :white_check_mark: | :white_check_mark: | :x: |
| VerifiableAttestation | :white_check_mark: | :white_check_mark: | :x: |

## Divulgacio selectiva

Les credencials SD-JWT VC permeten divulgar nomes els atributs necessaris:

```mermaid
flowchart LR
    subgraph Credencial Completa
        A[given_name]
        B[family_name]
        C[birth_date]
        D[nationality]
        E[document_number]
    end

    subgraph Presentacio Selectiva
        F[age_over_18]
    end

    Credencial Completa -->|Divulgacio selectiva| Presentacio Selectiva
```

!!! tip "Privacitat per disseny"
    Usa divulgacio selectiva sempre que sigui possible per minimitzar les dades compartides.

## Seguent pas

[:material-api: Veure Referencia API](../referencia-api/index.md){ .md-button }
