---
title: Esquemas
description: Definiciones JSON Schema de las credenciales en EUDIStack
---

# Esquemas JSON

Esta pagina documenta los esquemas JSON Schema utilizados para validar las credenciales verificables en EUDIStack, siguiendo el estandar **W3C Verifiable Credentials Data Model v2.0**.

## Esquema base (W3C VC DM v2.0)

Todas las credenciales verificables deben cumplir con el siguiente esquema base:

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://credentials.eudistack.eu/schemas/verifiable-credential-v2.json",
  "title": "W3C Verifiable Credential v2.0",
  "type": "object",
  "required": ["@context", "type", "issuer", "validFrom", "credentialSubject"],
  "properties": {
    "@context": {
      "type": "array",
      "items": { "type": "string", "format": "uri" },
      "contains": { "const": "https://www.w3.org/ns/credentials/v2" }
    },
    "id": {
      "type": "string",
      "pattern": "^urn:uuid:[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}$"
    },
    "type": {
      "type": "array",
      "items": { "type": "string" },
      "contains": { "const": "VerifiableCredential" }
    },
    "issuer": {
      "type": "object",
      "required": ["id"],
      "properties": {
        "id": { "type": "string", "pattern": "^did:" },
        "organization": { "type": "string" },
        "organizationIdentifier": { "type": "string" },
        "country": { "type": "string", "pattern": "^[A-Z]{2}$" },
        "commonName": { "type": "string" },
        "serialNumber": { "type": "string" }
      }
    },
    "validFrom": {
      "type": "string",
      "format": "date-time"
    },
    "validUntil": {
      "type": "string",
      "format": "date-time"
    },
    "credentialSubject": {
      "type": "object"
    },
    "credentialStatus": {
      "type": "object",
      "required": ["id", "type", "statusPurpose", "statusListIndex", "statusListCredential"],
      "properties": {
        "id": { "type": "string", "format": "uri" },
        "type": { "type": "string" },
        "statusPurpose": { "type": "string", "enum": ["revocation", "suspension"] },
        "statusListIndex": { "type": "string" },
        "statusListCredential": { "type": "string", "format": "uri" }
      }
    }
  }
}
```

!!! note "Sin bloque proof"
    En EUDIStack las credenciales se firman externamente como JWT, por lo que no incluyen un bloque `proof` embebido.

---

## LEARCredentialEmployee

Esquema para credenciales de representacion legal de empleados.

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://credentials.eudistack.eu/schemas/lear-credential-employee-v3.json",
  "title": "LEARCredentialEmployee",
  "description": "Credencial de representacion legal para empleados (e-Mandato)",
  "allOf": [
    { "$ref": "verifiable-credential-v2.json" }
  ],
  "properties": {
    "type": {
      "contains": { "const": "LEARCredentialEmployee" }
    },
    "credentialSubject": {
      "type": "object",
      "required": ["mandate"],
      "properties": {
        "mandate": {
          "type": "object",
          "required": ["mandator", "mandatee", "power"],
          "properties": {
            "id": {
              "type": "string",
              "pattern": "^urn:uuid:"
            },
            "mandator": { "$ref": "#/$defs/mandator" },
            "mandatee": { "$ref": "#/$defs/mandateeEmployee" },
            "power": {
              "type": "array",
              "items": { "$ref": "#/$defs/power" },
              "minItems": 1
            }
          }
        }
      }
    }
  },
  "$defs": {
    "mandator": {
      "type": "object",
      "required": ["id", "organizationIdentifier"],
      "properties": {
        "id": { "type": "string", "pattern": "^did:elsi:" },
        "organization": { "type": "string" },
        "organizationIdentifier": {
          "type": "string",
          "pattern": "^VAT[A-Z]{2}-"
        },
        "country": { "type": "string", "pattern": "^[A-Z]{2}$" },
        "commonName": { "type": "string" },
        "serialNumber": { "type": "string" },
        "email": { "type": "string", "format": "email" }
      }
    },
    "mandateeEmployee": {
      "type": "object",
      "required": ["id", "firstName", "lastName"],
      "properties": {
        "id": { "type": "string", "pattern": "^did:" },
        "firstName": { "type": "string" },
        "lastName": { "type": "string" },
        "email": { "type": "string", "format": "email" },
        "employeeId": { "type": "string" }
      }
    },
    "power": {
      "type": "object",
      "required": ["type", "domain", "function", "action"],
      "properties": {
        "type": { "type": "string" },
        "domain": { "type": "string" },
        "function": { "type": "string" },
        "action": {
          "type": "array",
          "items": { "type": "string" },
          "minItems": 1
        }
      }
    }
  }
}
```

---

## LEARCredentialMachine

Esquema para credenciales de representacion legal de entidades tecnicas (maquinas, servicios).

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://credentials.eudistack.eu/schemas/lear-credential-machine-v2.json",
  "title": "LEARCredentialMachine",
  "description": "Credencial de representacion legal para entidades tecnicas",
  "allOf": [
    { "$ref": "verifiable-credential-v2.json" }
  ],
  "properties": {
    "type": {
      "contains": { "const": "LEARCredentialMachine" }
    },
    "credentialSubject": {
      "type": "object",
      "required": ["mandate"],
      "properties": {
        "mandate": {
          "type": "object",
          "required": ["mandator", "mandatee", "power"],
          "properties": {
            "id": {
              "type": "string",
              "pattern": "^urn:uuid:"
            },
            "mandator": { "$ref": "#/$defs/mandator" },
            "mandatee": { "$ref": "#/$defs/mandateeMachine" },
            "power": {
              "type": "array",
              "items": { "$ref": "#/$defs/power" },
              "minItems": 1
            }
          }
        }
      }
    }
  },
  "$defs": {
    "mandator": {
      "type": "object",
      "required": ["id", "organizationIdentifier"],
      "properties": {
        "id": { "type": "string", "pattern": "^did:elsi:" },
        "organization": { "type": "string" },
        "organizationIdentifier": {
          "type": "string",
          "pattern": "^VAT[A-Z]{2}-"
        },
        "country": { "type": "string", "pattern": "^[A-Z]{2}$" },
        "commonName": { "type": "string" },
        "serialNumber": { "type": "string" },
        "email": { "type": "string", "format": "email" }
      }
    },
    "mandateeMachine": {
      "type": "object",
      "required": ["id"],
      "properties": {
        "id": { "type": "string", "pattern": "^did:" },
        "serviceName": { "type": "string" },
        "serviceType": { "type": "string" },
        "domain": {
          "type": "string",
          "format": "hostname"
        },
        "ipAddress": {
          "type": "string",
          "format": "ipv4"
        }
      }
    },
    "power": {
      "type": "object",
      "required": ["type", "domain", "function", "action"],
      "properties": {
        "type": { "type": "string" },
        "domain": { "type": "string" },
        "function": { "type": "string" },
        "action": {
          "type": "array",
          "items": { "type": "string" },
          "minItems": 1
        }
      }
    }
  }
}
```

---

## gx:LabelCredential

Esquema para credenciales de cumplimiento Gaia-X.

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://credentials.eudistack.eu/schemas/gx-label-credential.json",
  "title": "gx:LabelCredential",
  "description": "Credencial de cumplimiento Gaia-X para Cloud Service Providers",
  "allOf": [
    { "$ref": "verifiable-credential-v2.json" }
  ],
  "properties": {
    "type": {
      "contains": { "const": "gx:LabelCredential" }
    },
    "credentialSubject": {
      "type": "object",
      "required": ["id", "gx:labelLevel", "gx:compliantCredentials"],
      "properties": {
        "id": {
          "type": "string",
          "description": "Identificador del servicio certificado"
        },
        "gx:labelLevel": {
          "type": "string",
          "enum": ["BL", "L1", "L2", "L3"],
          "description": "Nivel de cumplimiento (BL=Baseline)"
        },
        "gx:engineVersion": {
          "type": "string",
          "pattern": "^\\d+\\.\\d+\\.\\d+$"
        },
        "gx:rulesVersion": {
          "type": "string",
          "pattern": "^CD\\d{2}\\.\\d{2}$"
        },
        "gx:compliantCredentials": {
          "type": "array",
          "items": { "$ref": "#/$defs/compliantCredential" },
          "minItems": 1
        },
        "gx:validatedCriteria": {
          "type": "array",
          "items": { "type": "string", "format": "uri" }
        }
      }
    }
  },
  "$defs": {
    "compliantCredential": {
      "type": "object",
      "required": ["id", "type"],
      "properties": {
        "id": { "type": "string", "pattern": "^urn:criteria:" },
        "type": {
          "type": "string",
          "enum": [
            "gx:DataProtection",
            "gx:Cybersecurity",
            "gx:Portability",
            "gx:Sustainability",
            "gx:EuropeanControl"
          ]
        },
        "gx:digestSRI": {
          "type": "string",
          "pattern": "^sha256-"
        }
      }
    }
  }
}
```

---

## Validacion de credenciales

### LEARCredentialEmployee

=== "Java"

    ```java
    import com.networknt.schema.*;
    import com.fasterxml.jackson.databind.JsonNode;
    import com.fasterxml.jackson.databind.ObjectMapper;
    import java.util.Set;

    public class LEARCredentialValidator {

        private final JsonSchema schema;
        private final ObjectMapper mapper = new ObjectMapper();

        public LEARCredentialValidator() throws Exception {
            JsonSchemaFactory factory = JsonSchemaFactory.getInstance(SpecVersion.VersionFlag.V202012);
            schema = factory.getSchema(
                getClass().getResourceAsStream("/schemas/lear-credential-employee-v3.json")
            );
        }

        public ValidationResult validate(String credentialJson) {
            try {
                JsonNode credential = mapper.readTree(credentialJson);
                Set<ValidationMessage> errors = schema.validate(credential);

                if (errors.isEmpty()) {
                    return ValidationResult.valid();
                }
                return ValidationResult.invalid(errors);
            } catch (Exception e) {
                return ValidationResult.error(e.getMessage());
            }
        }

        // Ejemplo de uso
        public static void main(String[] args) throws Exception {
            LEARCredentialValidator validator = new LEARCredentialValidator();

            String credential = """
                {
                  "@context": ["https://www.w3.org/ns/credentials/v2"],
                  "type": ["VerifiableCredential", "LEARCredentialEmployee"],
                  "issuer": {
                    "id": "did:elsi:VATES-A12345678",
                    "organizationIdentifier": "VATES-A12345678"
                  },
                  "validFrom": "2025-01-01T00:00:00Z",
                  "credentialSubject": {
                    "mandate": {
                      "mandator": {
                        "id": "did:elsi:VATES-A12345678",
                        "organizationIdentifier": "VATES-A12345678"
                      },
                      "mandatee": {
                        "id": "did:key:z6Mk...",
                        "firstName": "Jane",
                        "lastName": "Smith"
                      },
                      "power": [{
                        "type": "domain",
                        "domain": "Marketplace",
                        "function": "Onboarding",
                        "action": ["Execute"]
                      }]
                    }
                  }
                }
                """;

            ValidationResult result = validator.validate(credential);
            System.out.println("Valido: " + result.isValid());
        }
    }
    ```

=== "JavaScript"

    ```javascript
    const Ajv = require('ajv');
    const addFormats = require('ajv-formats');

    const ajv = new Ajv({ strict: false });
    addFormats(ajv);

    const schema = require('./schemas/lear-credential-employee-v3.json');
    const validate = ajv.compile(schema);

    const credential = {
      "@context": ["https://www.w3.org/ns/credentials/v2"],
      "type": ["VerifiableCredential", "LEARCredentialEmployee"],
      "issuer": {
        "id": "did:elsi:VATES-A12345678",
        "organizationIdentifier": "VATES-A12345678"
      },
      "validFrom": "2025-01-01T00:00:00Z",
      "credentialSubject": {
        "mandate": {
          "mandator": {
            "id": "did:elsi:VATES-A12345678",
            "organizationIdentifier": "VATES-A12345678"
          },
          "mandatee": {
            "id": "did:key:z6Mk...",
            "firstName": "Jane",
            "lastName": "Smith"
          },
          "power": [{
            "type": "domain",
            "domain": "Marketplace",
            "function": "Onboarding",
            "action": ["Execute"]
          }]
        }
      }
    };

    if (validate(credential)) {
      console.log('LEARCredentialEmployee valida');
    } else {
      console.log('Errores:', validate.errors);
    }
    ```

=== "Python"

    ```python
    import jsonschema
    import json

    # Cargar el esquema
    with open('schemas/lear-credential-employee-v3.json') as f:
        schema = json.load(f)

    credential = {
        "@context": ["https://www.w3.org/ns/credentials/v2"],
        "type": ["VerifiableCredential", "LEARCredentialEmployee"],
        "issuer": {
            "id": "did:elsi:VATES-A12345678",
            "organizationIdentifier": "VATES-A12345678"
        },
        "validFrom": "2025-01-01T00:00:00Z",
        "credentialSubject": {
            "mandate": {
                "mandator": {
                    "id": "did:elsi:VATES-A12345678",
                    "organizationIdentifier": "VATES-A12345678"
                },
                "mandatee": {
                    "id": "did:key:z6Mk...",
                    "firstName": "Jane",
                    "lastName": "Smith"
                },
                "power": [{
                    "type": "domain",
                    "domain": "Marketplace",
                    "function": "Onboarding",
                    "action": ["Execute"]
                }]
            }
        }
    }

    try:
        jsonschema.validate(credential, schema)
        print("LEARCredentialEmployee valida")
    except jsonschema.ValidationError as e:
        print(f"Error de validacion: {e.message}")
    ```

---

### LEARCredentialMachine

=== "Java"

    ```java
    import com.networknt.schema.*;
    import com.fasterxml.jackson.databind.JsonNode;
    import com.fasterxml.jackson.databind.ObjectMapper;
    import java.util.Set;

    public class LEARMachineValidator {

        private final JsonSchema schema;
        private final ObjectMapper mapper = new ObjectMapper();

        public LEARMachineValidator() throws Exception {
            JsonSchemaFactory factory = JsonSchemaFactory.getInstance(SpecVersion.VersionFlag.V202012);
            schema = factory.getSchema(
                getClass().getResourceAsStream("/schemas/lear-credential-machine-v2.json")
            );
        }

        public boolean validate(String credentialJson) {
            try {
                JsonNode credential = mapper.readTree(credentialJson);
                Set<ValidationMessage> errors = schema.validate(credential);

                if (errors.isEmpty()) {
                    System.out.println("LEARCredentialMachine valida");
                    return true;
                }

                errors.forEach(error ->
                    System.err.println("Error: " + error.getMessage())
                );
                return false;
            } catch (Exception e) {
                System.err.println("Error de parsing: " + e.getMessage());
                return false;
            }
        }

        public static void main(String[] args) throws Exception {
            LEARMachineValidator validator = new LEARMachineValidator();

            String credential = """
                {
                  "@context": ["https://www.w3.org/ns/credentials/v2"],
                  "type": ["VerifiableCredential", "LEARCredentialMachine"],
                  "issuer": {
                    "id": "did:elsi:VATES-A12345678",
                    "organizationIdentifier": "VATES-A12345678"
                  },
                  "validFrom": "2025-01-01T00:00:00Z",
                  "credentialSubject": {
                    "mandate": {
                      "mandator": {
                        "id": "did:elsi:VATES-A12345678",
                        "organizationIdentifier": "VATES-A12345678"
                      },
                      "mandatee": {
                        "id": "did:key:zDnae...",
                        "serviceName": "DPAS Service",
                        "domain": "dpas.acme.es",
                        "ipAddress": "195.70.63.244"
                      },
                      "power": [{
                        "type": "domain",
                        "domain": "Marketplace",
                        "function": "ProductOffering",
                        "action": ["Create", "Update"]
                      }]
                    }
                  }
                }
                """;

            validator.validate(credential);
        }
    }
    ```

=== "JavaScript"

    ```javascript
    const Ajv = require('ajv');
    const addFormats = require('ajv-formats');

    const ajv = new Ajv({ strict: false });
    addFormats(ajv);

    const schema = require('./schemas/lear-credential-machine-v2.json');
    const validate = ajv.compile(schema);

    const credential = {
      "@context": ["https://www.w3.org/ns/credentials/v2"],
      "type": ["VerifiableCredential", "LEARCredentialMachine"],
      "issuer": {
        "id": "did:elsi:VATES-A12345678",
        "organizationIdentifier": "VATES-A12345678"
      },
      "validFrom": "2025-01-01T00:00:00Z",
      "credentialSubject": {
        "mandate": {
          "mandator": {
            "id": "did:elsi:VATES-A12345678",
            "organizationIdentifier": "VATES-A12345678"
          },
          "mandatee": {
            "id": "did:key:zDnae...",
            "serviceName": "DPAS Service",
            "domain": "dpas.acme.es",
            "ipAddress": "195.70.63.244"
          },
          "power": [{
            "type": "domain",
            "domain": "Marketplace",
            "function": "ProductOffering",
            "action": ["Create", "Update"]
          }]
        }
      }
    };

    if (validate(credential)) {
      console.log('LEARCredentialMachine valida');
    } else {
      console.log('Errores:', validate.errors);
    }
    ```

=== "Python"

    ```python
    import jsonschema
    import json

    with open('schemas/lear-credential-machine-v2.json') as f:
        schema = json.load(f)

    credential = {
        "@context": ["https://www.w3.org/ns/credentials/v2"],
        "type": ["VerifiableCredential", "LEARCredentialMachine"],
        "issuer": {
            "id": "did:elsi:VATES-A12345678",
            "organizationIdentifier": "VATES-A12345678"
        },
        "validFrom": "2025-01-01T00:00:00Z",
        "credentialSubject": {
            "mandate": {
                "mandator": {
                    "id": "did:elsi:VATES-A12345678",
                    "organizationIdentifier": "VATES-A12345678"
                },
                "mandatee": {
                    "id": "did:key:zDnae...",
                    "serviceName": "DPAS Service",
                    "domain": "dpas.acme.es",
                    "ipAddress": "195.70.63.244"
                },
                "power": [{
                    "type": "domain",
                    "domain": "Marketplace",
                    "function": "ProductOffering",
                    "action": ["Create", "Update"]
                }]
            }
        }
    }

    try:
        jsonschema.validate(credential, schema)
        print("LEARCredentialMachine valida")
    except jsonschema.ValidationError as e:
        print(f"Error: {e.message}")
    ```

---

### gx:LabelCredential

=== "Java"

    ```java
    import com.networknt.schema.*;
    import com.fasterxml.jackson.databind.JsonNode;
    import com.fasterxml.jackson.databind.ObjectMapper;
    import java.util.Set;

    public class GaiaXLabelValidator {

        private final JsonSchema schema;
        private final ObjectMapper mapper = new ObjectMapper();

        public GaiaXLabelValidator() throws Exception {
            JsonSchemaFactory factory = JsonSchemaFactory.getInstance(SpecVersion.VersionFlag.V202012);
            schema = factory.getSchema(
                getClass().getResourceAsStream("/schemas/gx-label-credential.json")
            );
        }

        public boolean validate(String credentialJson) {
            try {
                JsonNode credential = mapper.readTree(credentialJson);
                Set<ValidationMessage> errors = schema.validate(credential);
                return errors.isEmpty();
            } catch (Exception e) {
                return false;
            }
        }

        public static void main(String[] args) throws Exception {
            GaiaXLabelValidator validator = new GaiaXLabelValidator();

            String credential = """
                {
                  "@context": [
                    "https://www.w3.org/ns/credentials/v2",
                    "https://w3id.org/gaia-x/development#"
                  ],
                  "type": ["VerifiableCredential", "gx:LabelCredential"],
                  "issuer": {
                    "id": "did:elsi:VATES-B60645900"
                  },
                  "validFrom": "2025-05-19T09:36:24.038Z",
                  "validUntil": "2025-08-19T09:36:24.038Z",
                  "credentialSubject": {
                    "id": "urn:ngsi-ld:product-specification:abc123",
                    "gx:labelLevel": "BL",
                    "gx:engineVersion": "1.3.0",
                    "gx:rulesVersion": "CD25.03",
                    "gx:compliantCredentials": [
                      {
                        "id": "urn:criteria:dp-1",
                        "type": "gx:DataProtection",
                        "gx:digestSRI": "sha256-abc123..."
                      }
                    ]
                  }
                }
                """;

            if (validator.validate(credential)) {
                System.out.println("gx:LabelCredential valida");
            } else {
                System.out.println("gx:LabelCredential invalida");
            }
        }
    }
    ```

=== "JavaScript"

    ```javascript
    const Ajv = require('ajv');
    const addFormats = require('ajv-formats');

    const ajv = new Ajv({ strict: false });
    addFormats(ajv);

    const schema = require('./schemas/gx-label-credential.json');
    const validate = ajv.compile(schema);

    const credential = {
      "@context": [
        "https://www.w3.org/ns/credentials/v2",
        "https://w3id.org/gaia-x/development#"
      ],
      "type": ["VerifiableCredential", "gx:LabelCredential"],
      "issuer": {
        "id": "did:elsi:VATES-B60645900"
      },
      "validFrom": "2025-05-19T09:36:24.038Z",
      "validUntil": "2025-08-19T09:36:24.038Z",
      "credentialSubject": {
        "id": "urn:ngsi-ld:product-specification:abc123",
        "gx:labelLevel": "BL",
        "gx:engineVersion": "1.3.0",
        "gx:rulesVersion": "CD25.03",
        "gx:compliantCredentials": [
          {
            "id": "urn:criteria:dp-1",
            "type": "gx:DataProtection",
            "gx:digestSRI": "sha256-abc123..."
          }
        ]
      }
    };

    if (validate(credential)) {
      console.log('gx:LabelCredential valida');
    } else {
      console.log('Errores:', validate.errors);
    }
    ```

=== "Python"

    ```python
    import jsonschema
    import json

    with open('schemas/gx-label-credential.json') as f:
        schema = json.load(f)

    credential = {
        "@context": [
            "https://www.w3.org/ns/credentials/v2",
            "https://w3id.org/gaia-x/development#"
        ],
        "type": ["VerifiableCredential", "gx:LabelCredential"],
        "issuer": {
            "id": "did:elsi:VATES-B60645900"
        },
        "validFrom": "2025-05-19T09:36:24.038Z",
        "validUntil": "2025-08-19T09:36:24.038Z",
        "credentialSubject": {
            "id": "urn:ngsi-ld:product-specification:abc123",
            "gx:labelLevel": "BL",
            "gx:engineVersion": "1.3.0",
            "gx:rulesVersion": "CD25.03",
            "gx:compliantCredentials": [
                {
                    "id": "urn:criteria:dp-1",
                    "type": "gx:DataProtection",
                    "gx:digestSRI": "sha256-abc123..."
                }
            ]
        }
    }

    try:
        jsonschema.validate(credential, schema)
        print("gx:LabelCredential valida")
    except jsonschema.ValidationError as e:
        print(f"Error: {e.message}")
    ```

---

## Dependencias

### Java (Maven)

```xml
<dependencies>
    <!-- JSON Schema Validator -->
    <dependency>
        <groupId>com.networknt</groupId>
        <artifactId>json-schema-validator</artifactId>
        <version>1.0.87</version>
    </dependency>

    <!-- Jackson para JSON -->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.15.2</version>
    </dependency>
</dependencies>
```

### JavaScript (npm)

```bash
npm install ajv ajv-formats
```

### Python (pip)

```bash
pip install jsonschema
```

---

## Siguiente paso

[:material-card-account-details: Ver tipos de credencial](tipos-credencial.md){ .md-button }
