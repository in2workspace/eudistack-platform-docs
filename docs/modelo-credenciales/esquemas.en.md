---
title: Schemas
description: JSON Schema definitions for credentials in EUDIStack
---

# Schemas

This page documents the JSON Schemas used to validate credentials in EUDIStack.

## Base Credential Schema

All verifiable credentials must comply with the following base schema:

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://eudistack.example.com/schemas/verifiable-credential.json",
  "title": "Verifiable Credential",
  "type": "object",
  "required": ["@context", "type", "issuer", "issuanceDate", "credentialSubject"],
  "properties": {
    "@context": {
      "type": "array",
      "items": { "type": "string" },
      "contains": { "const": "https://www.w3.org/2018/credentials/v1" }
    },
    "id": {
      "type": "string",
      "format": "uri"
    },
    "type": {
      "type": "array",
      "items": { "type": "string" },
      "contains": { "const": "VerifiableCredential" }
    },
    "issuer": {
      "oneOf": [
        { "type": "string", "format": "uri" },
        {
          "type": "object",
          "required": ["id"],
          "properties": {
            "id": { "type": "string", "format": "uri" },
            "name": { "type": "string" }
          }
        }
      ]
    },
    "issuanceDate": {
      "type": "string",
      "format": "date-time"
    },
    "expirationDate": {
      "type": "string",
      "format": "date-time"
    },
    "credentialSubject": {
      "type": "object",
      "properties": {
        "id": { "type": "string" }
      }
    },
    "credentialStatus": {
      "type": "object",
      "properties": {
        "id": { "type": "string", "format": "uri" },
        "type": { "type": "string" }
      }
    },
    "proof": {
      "type": "object"
    }
  }
}
```

## Specific Credential Schemas

### VerifiableId (PID)

Personal identity credential based on the Person Identification Data (PID) from ARF:

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://eudistack.example.com/schemas/verifiable-id.json",
  "title": "Verifiable ID",
  "allOf": [
    { "$ref": "verifiable-credential.json" }
  ],
  "properties": {
    "type": {
      "contains": { "const": "VerifiableId" }
    },
    "credentialSubject": {
      "type": "object",
      "required": ["given_name", "family_name", "birth_date"],
      "properties": {
        "id": { "type": "string" },
        "given_name": {
          "type": "string",
          "description": "First name(s)"
        },
        "family_name": {
          "type": "string",
          "description": "Family name(s)"
        },
        "birth_date": {
          "type": "string",
          "format": "date",
          "description": "Date of birth (YYYY-MM-DD)"
        },
        "age_over_18": {
          "type": "boolean",
          "description": "Age over 18 indicator"
        },
        "age_over_21": {
          "type": "boolean",
          "description": "Age over 21 indicator"
        },
        "nationality": {
          "type": "string",
          "pattern": "^[A-Z]{2}$",
          "description": "ISO 3166-1 alpha-2 code"
        },
        "document_number": {
          "type": "string",
          "description": "Identity document number"
        },
        "administrative_number": {
          "type": "string",
          "description": "National administrative number"
        },
        "gender": {
          "type": "integer",
          "enum": [0, 1, 2, 9],
          "description": "ISO/IEC 5218 (0=unknown, 1=male, 2=female, 9=not applicable)"
        },
        "birth_place": {
          "type": "string",
          "description": "Place of birth"
        },
        "resident_address": {
          "type": "string",
          "description": "Residential address"
        },
        "resident_city": {
          "type": "string"
        },
        "resident_postal_code": {
          "type": "string"
        },
        "resident_country": {
          "type": "string",
          "pattern": "^[A-Z]{2}$"
        },
        "portrait": {
          "type": "string",
          "contentEncoding": "base64",
          "description": "Holder's photograph"
        }
      }
    }
  }
}
```

### VerifiableDiploma

Academic credential for degrees and diplomas:

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://eudistack.example.com/schemas/verifiable-diploma.json",
  "title": "Verifiable Diploma",
  "allOf": [
    { "$ref": "verifiable-credential.json" }
  ],
  "properties": {
    "type": {
      "contains": { "const": "VerifiableDiploma" }
    },
    "credentialSubject": {
      "type": "object",
      "required": ["degree_name", "awarding_institution"],
      "properties": {
        "id": { "type": "string" },
        "given_name": { "type": "string" },
        "family_name": { "type": "string" },
        "degree_name": {
          "type": "string",
          "description": "Degree name"
        },
        "degree_type": {
          "type": "string",
          "enum": ["Bachelor", "Master", "Doctorate", "Certificate"],
          "description": "Type of degree"
        },
        "awarding_institution": {
          "type": "object",
          "required": ["name"],
          "properties": {
            "name": { "type": "string" },
            "id": { "type": "string", "format": "uri" }
          }
        },
        "graduation_date": {
          "type": "string",
          "format": "date"
        },
        "field_of_study": {
          "type": "string"
        },
        "final_grade": {
          "type": "string"
        },
        "ects_credits": {
          "type": "number"
        }
      }
    }
  }
}
```

### VerifiableAttestation

Generic credential for attestations:

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://eudistack.example.com/schemas/verifiable-attestation.json",
  "title": "Verifiable Attestation",
  "allOf": [
    { "$ref": "verifiable-credential.json" }
  ],
  "properties": {
    "type": {
      "contains": { "const": "VerifiableAttestation" }
    },
    "credentialSubject": {
      "type": "object",
      "required": ["attestation_type"],
      "properties": {
        "id": { "type": "string" },
        "attestation_type": {
          "type": "string",
          "description": "Type of attestation"
        },
        "attestation_value": {
          "type": "string",
          "description": "Attestation value or content"
        },
        "effective_date": {
          "type": "string",
          "format": "date"
        },
        "expiry_date": {
          "type": "string",
          "format": "date"
        }
      }
    }
  }
}
```

## Credential Validation

### Python Validation Example

```python
import jsonschema
import json

# Load schema
with open('schemas/verifiable-id.json') as f:
    schema = json.load(f)

# Credential to validate
credential = {
    "@context": ["https://www.w3.org/2018/credentials/v1"],
    "type": ["VerifiableCredential", "VerifiableId"],
    "issuer": "did:web:issuer.example.com",
    "issuanceDate": "2024-01-15T10:00:00Z",
    "credentialSubject": {
        "given_name": "Maria",
        "family_name": "Garcia",
        "birth_date": "1990-05-20"
    }
}

# Validate
try:
    jsonschema.validate(credential, schema)
    print("Valid credential")
except jsonschema.ValidationError as e:
    print(f"Validation error: {e.message}")
```

### JavaScript Validation Example

```javascript
const Ajv = require('ajv');
const addFormats = require('ajv-formats');

const ajv = new Ajv();
addFormats(ajv);

const schema = require('./schemas/verifiable-id.json');
const validate = ajv.compile(schema);

const credential = {
  "@context": ["https://www.w3.org/2018/credentials/v1"],
  "type": ["VerifiableCredential", "VerifiableId"],
  "issuer": "did:web:issuer.example.com",
  "issuanceDate": "2024-01-15T10:00:00Z",
  "credentialSubject": {
    "given_name": "Maria",
    "family_name": "Garcia",
    "birth_date": "1990-05-20"
  }
};

if (validate(credential)) {
  console.log('Valid credential');
} else {
  console.log('Errors:', validate.errors);
}
```

## Next Step

[:material-card-account-details: View credential types](tipos-credencial.md){ .md-button }
