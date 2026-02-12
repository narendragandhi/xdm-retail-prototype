# XDM-Based Retail Customer Form Data Model Prototype

A comprehensive prototype demonstrating **XDM-style JSON Schema with inheritance** for AEM Forms FDM and **Interactive Communications**.

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
| `schema/retail-customer-inheritance.json.schema.json` | Main schema with inheritance (IC extended) |
| `schema/definitions/address.schema.json` | Reusable address component |
| `schema/definitions/contactInfo.schema.json` | Reusable contact component |
| `schema/definitions/loyaltyProgram.schema.json` | Reusable loyalty component |
| `schema/definitions/transaction.schema.json` | Transaction history (NEW) |
| `schema/definitions/account.schema.json` | Account & subscription (NEW) |
| `schema/definitions/communicationPreferences.schema.json` | IC preferences (NEW) |
| `schema/definitions/interactionHistory.schema.json` | Engagement tracking (NEW) |
| `fdm/retail-customer-fdm.xml` | Form Data Model configuration (extended) |
| `forms/customer-registration-form.xml` | Adaptive Form template |

### Architecture

```
JSON Schema (allOf + $defs)
         │
         ▼
    FDM Entities
         │
         ▼
Adaptive Form / Interactive Communication
```

### Key Features

- ✅ JSON Schema 2020-12 with inheritance
- ✅ Reusable components (address, contact, loyalty, transaction, account, communication, interaction)
- ✅ FDM with CRUD services
- ✅ 5-tab Adaptive Form
- ✅ Interactive Communication support (statements, personalized documents)
- ✅ Engagement tracking and journey staging
- ✅ Multi-language and accessibility support
- ✅ No AEM Platform Edition required
- ✅ AEM 6.5 + Cloud Service compatible

## Documentation

See [IMPLEMENTATION.md](IMPLEMENTATION.md) for:
- Detailed explanation of each component
- How the schema inheritance works
- Interactive Communication extensions
- Data flow diagrams
- Reusability examples
- Extension guide
