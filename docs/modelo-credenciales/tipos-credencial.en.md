---
title: Credential Types
description: Catalog of credential types supported in EUDIStack
---

# Credential Types

This catalog documents the credential types supported by EUDIStack, aligned with the ARF specifications from the European Commission.

## Identity Credentials

### VerifiableId (PID)

Personal identity credential based on **Person Identification Data (PID)**.

| Attribute | Description |
|-----------|-------------|
| **Name** | VerifiableId |
| **Scope** | Personal identity |
| **Typical issuers** | Governments, identity authorities |
| **Validity** | 5-10 years |

#### Available Attributes

| Claim | Type | Description | Required |
|-------|------|-------------|----------|
| `given_name` | string | First name(s) | Yes |
| `family_name` | string | Family name(s) | Yes |
| `birth_date` | date | Date of birth | Yes |
| `age_over_18` | boolean | Over 18 years old | No |
| `age_over_21` | boolean | Over 21 years old | No |
| `nationality` | string | Nationality (ISO 3166-1) | No |
| `document_number` | string | Document number | No |
| `gender` | integer | Gender (ISO 5218) | No |
| `birth_place` | string | Place of birth | No |
| `portrait` | base64 | Photograph | No |

#### Example

```json
{
  "@context": ["https://www.w3.org/2018/credentials/v1"],
  "type": ["VerifiableCredential", "VerifiableId"],
  "issuer": {
    "id": "did:web:government.es",
    "name": "Government of Spain"
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

## Academic Credentials

### VerifiableDiploma

Credential for academic degrees and university diplomas.

| Attribute | Description |
|-----------|-------------|
| **Name** | VerifiableDiploma |
| **Scope** | Higher education |
| **Typical issuers** | Universities, training centers |
| **Validity** | Permanent |

#### Available Attributes

| Claim | Type | Description | Required |
|-------|------|-------------|----------|
| `degree_name` | string | Degree name | Yes |
| `degree_type` | string | Type (Bachelor, Master, etc.) | No |
| `awarding_institution` | object | Awarding institution | Yes |
| `graduation_date` | date | Graduation date | No |
| `field_of_study` | string | Field of study | No |
| `final_grade` | string | Final grade | No |
| `ects_credits` | number | ECTS credits | No |

#### Example

```json
{
  "@context": ["https://www.w3.org/2018/credentials/v1"],
  "type": ["VerifiableCredential", "VerifiableDiploma"],
  "issuer": {
    "id": "did:web:upc.edu",
    "name": "Polytechnic University of Catalonia"
  },
  "issuanceDate": "2024-06-15T12:00:00Z",
  "credentialSubject": {
    "id": "did:key:z6Mk...",
    "given_name": "Carlos",
    "family_name": "Martinez",
    "degree_name": "Bachelor in Computer Engineering",
    "degree_type": "Bachelor",
    "awarding_institution": {
      "name": "Polytechnic University of Catalonia",
      "id": "https://www.upc.edu"
    },
    "graduation_date": "2024-06-01",
    "field_of_study": "Computer Science",
    "final_grade": "Distinction",
    "ects_credits": 240
  }
}
```

---

## Professional Credentials

### ProfessionalQualification

Credential for professional qualifications and licenses.

| Attribute | Description |
|-----------|-------------|
| **Name** | ProfessionalQualification |
| **Scope** | Professional qualifications |
| **Typical issuers** | Professional bodies, authorities |
| **Validity** | Variable (typically 1-5 years) |

#### Available Attributes

| Claim | Type | Description | Required |
|-------|------|-------------|----------|
| `qualification_name` | string | Qualification name | Yes |
| `profession` | string | Profession | Yes |
| `issuing_authority` | object | Issuing authority | Yes |
| `license_number` | string | License number | No |
| `valid_from` | date | Valid from date | No |
| `valid_until` | date | Valid until date | No |

#### Example

```json
{
  "@context": ["https://www.w3.org/2018/credentials/v1"],
  "type": ["VerifiableCredential", "ProfessionalQualification"],
  "issuer": {
    "id": "did:web:medical-council.es",
    "name": "Official Medical Council"
  },
  "issuanceDate": "2024-01-01T00:00:00Z",
  "expirationDate": "2025-12-31T23:59:59Z",
  "credentialSubject": {
    "id": "did:key:z6Mk...",
    "given_name": "Ana",
    "family_name": "Fernandez",
    "qualification_name": "Medical Practice License",
    "profession": "General Medicine",
    "issuing_authority": {
      "name": "Official Medical Council of Barcelona"
    },
    "license_number": "COM-BCN-12345",
    "valid_from": "2024-01-01",
    "valid_until": "2025-12-31"
  }
}
```

---

## Attestation Credentials

### VerifiableAttestation

Generic credential for specific attribute attestations.

| Attribute | Description |
|-----------|-------------|
| **Name** | VerifiableAttestation |
| **Scope** | General attestations |
| **Typical issuers** | Any authorized entity |
| **Validity** | Variable |

#### Common Use Cases

- Residence certificate
- Registration certificate
- Employment attestation
- Background check certificate

---

## Compatibility Matrix

| Type | JWT VC | SD-JWT VC | mDOC |
|------|--------|-----------|------|
| VerifiableId | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| VerifiableDiploma | :white_check_mark: | :white_check_mark: | :x: |
| ProfessionalQualification | :white_check_mark: | :white_check_mark: | :x: |
| VerifiableAttestation | :white_check_mark: | :white_check_mark: | :x: |

## Selective Disclosure

SD-JWT VC credentials allow disclosing only the necessary attributes:

```mermaid
flowchart LR
    subgraph Complete Credential
        A[given_name]
        B[family_name]
        C[birth_date]
        D[nationality]
        E[document_number]
    end

    subgraph Selective Presentation
        F[age_over_18]
    end

    Complete Credential -->|Selective disclosure| Selective Presentation
```

!!! tip "Privacy by Design"
    Use selective disclosure whenever possible to minimize shared data.

## Next Step

[:material-api: View API Reference](../referencia-api/index.md){ .md-button }
