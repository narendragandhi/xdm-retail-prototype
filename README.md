# XDM-Based Retail Customer Form Data Model Prototype

A comprehensive prototype demonstrating **XDM-style JSON Schema with inheritance** for AEM Forms FDM.

## Quick Start

1. **Read the Documentation:** [IMPLEMENTATION.md](IMPLEMENTATION.md)
2. **Import Schema:** `schema/retail-customer-inheritance.json.schema.json`
3. **Create FDM:** Import `fdm/retail-customer-fdm.xml`
4. **Build Form:** Use `forms/customer-registration-form.xml`

## What Was Built

### Files Created

| File | Description |
|------|-------------|
| `IMPLEMENTATION.md` | Full documentation of the implementation |
| `schema/retail-customer-inheritance.json.schema.json` | Main schema with inheritance |
| `schema/definitions/address.schema.json` | Reusable address component |
| `schema/definitions/contactInfo.schema.json` | Reusable contact component |
| `schema/definitions/loyaltyProgram.schema.json` | Reusable loyalty component |
| `fdm/retail-customer-fdm.xml` | Form Data Model configuration |
| `forms/customer-registration-form.xml` | Adaptive Form template |

### Architecture

```
JSON Schema (allOf + $defs)
         │
         ▼
    FDM Entities
         │
         ▼
Adaptive Form
```

### Key Features

- ✅ JSON Schema 2020-12 with inheritance
- ✅ Reusable components (address, contact, loyalty)
- ✅ FDM with CRUD services
- ✅ 5-tab Adaptive Form
- ✅ Validation rules
- ✅ No AEP required
- ✅ AEM 6.5 + Cloud Service compatible

## Documentation

See [IMPLEMENTATION.md](IMPLEMENTATION.md) for:
- Detailed explanation of each component
- How the schema inheritance works
- Data flow diagrams
- Reusability examples
- Extension guide
