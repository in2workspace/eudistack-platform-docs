---
title: Esquemes
description: Definicions JSON Schema de les credencials a EUDIStack
---

# Esquemes

Aquesta pagina documenta els esquemes JSON Schema utilitzats per validar les credencials a EUDIStack.

## Esquema base de credencial

Totes les credencials verificables han de complir amb el seguent esquema base:

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

## Esquemes de credencials especifiques

### VerifiableId (PID)

Credencial d'identitat personal basada en el Person Identification Data (PID) de l'ARF:

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
          "description": "Nom(s) de pila"
        },
        "family_name": {
          "type": "string",
          "description": "Cognom(s)"
        },
        "birth_date": {
          "type": "string",
          "format": "date",
          "description": "Data de naixement (YYYY-MM-DD)"
        },
        "age_over_18": {
          "type": "boolean",
          "description": "Indicador de majoria d'edat"
        },
        "age_over_21": {
          "type": "boolean",
          "description": "Indicador d'edat superior a 21"
        },
        "nationality": {
          "type": "string",
          "pattern": "^[A-Z]{2}$",
          "description": "Codi ISO 3166-1 alpha-2"
        },
        "document_number": {
          "type": "string",
          "description": "Numero del document d'identitat"
        },
        "administrative_number": {
          "type": "string",
          "description": "Numero administratiu nacional"
        },
        "gender": {
          "type": "integer",
          "enum": [0, 1, 2, 9],
          "description": "ISO/IEC 5218 (0=desconegut, 1=masculi, 2=femeni, 9=no aplicable)"
        },
        "birth_place": {
          "type": "string",
          "description": "Lloc de naixement"
        },
        "resident_address": {
          "type": "string",
          "description": "Adreca de residencia"
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
          "description": "Fotografia del titular"
        }
      }
    }
  }
}
```

### VerifiableDiploma

Credencial academica per a titols i diplomes:

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
          "description": "Nom del titol"
        },
        "degree_type": {
          "type": "string",
          "enum": ["Bachelor", "Master", "Doctorate", "Certificate"],
          "description": "Tipus de titol"
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

Credencial generica per a atestacions:

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
          "description": "Tipus d'atestacio"
        },
        "attestation_value": {
          "type": "string",
          "description": "Valor o contingut de l'atestacio"
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

## Validacio de credencials

### Exemple de validacio en Python

```python
import jsonschema
import json

# Carregar l'esquema
with open('schemas/verifiable-id.json') as f:
    schema = json.load(f)

# Credencial a validar
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

# Validar
try:
    jsonschema.validate(credential, schema)
    print("Credencial valida")
except jsonschema.ValidationError as e:
    print(f"Error de validacio: {e.message}")
```

### Exemple de validacio en JavaScript

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
  console.log('Credencial valida');
} else {
  console.log('Errors:', validate.errors);
}
```

## Seguent pas

[:material-card-account-details: Veure tipus de credencial](tipos-credencial.md){ .md-button }
