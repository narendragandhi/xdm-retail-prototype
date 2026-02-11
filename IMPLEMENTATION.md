# XDM-Based Retail Customer Form Data Model Prototype

## Documentation

### Overview

This prototype demonstrates how to build a **Form Data Model (FDM)** for AEM Forms using **XDM-style JSON Schema with inheritance patterns**. The implementation focuses on modular, reusable schema components that can be composed together using JSON Schema's `allOf` and `$defs` keywords.

### What Was Built

#### 1. JSON Schema with Inheritance

Created a main schema file that demonstrates XDM-style composition:

**File:** `schema/retail-customer-inheritance.json.schema.json`

**Key Features:**
- Uses JSON Schema 2020-12 specification
- Implements composition via `allOf` keyword
- Defines reusable components in `$defs` section
- Includes base person, contact, loyalty, and address definitions

**Schema Structure:**
```json
{
  "$defs": {
    "basePerson": { "type": "object", "properties": { "firstName", "lastName", "email" } },
    "contactInfo": { "type": "object", "properties": { "phoneNumbers", "preferredContactMethod" } },
    "loyaltyProgram": { "type": "object", "properties": { "loyaltyID", "tier", "pointsBalance" } },
    "address": { "type": "object", "properties": { "street1", "city", "state", "postalCode" } },
    "marketingConsent": { "type": "object", "properties": { "emailConsent", "smsConsent" } }
  },
  "allOf": [
    { "$ref": "#/$defs/basePerson" },
    { "$ref": "#/$defs/contactInfo" },
    { "properties": { "loyaltyInfo": { "$ref": "#/$defs/loyaltyProgram" } } },
    { "properties": { "addresses": { "items": { "$ref": "#/$defs/address" } } } },
    { "properties": { "consentInfo": { "$ref": "#/$defs/marketingConsent" } } }
  ]
}
```

#### 2. Reusable Component Definitions

Created modular schema components that can be shared across multiple forms:

**Files:**
- `schema/definitions/address.schema.json` - Address with validation (street, city, state, postalCode, country)
- `schema/definitions/contactInfo.schema.json` - Contact info (email, phones, preferences)
- `schema/definitions/loyaltyProgram.schema.json` - Loyalty data (tier, points, benefits)

**Benefits:**
- Single source of truth for common entities
- Easy to update across all forms
- Consistent data structure
- Reduced maintenance overhead

#### 3. Form Data Model (FDM) Configuration

**File:** `fdm/retail-customer-fdm.xml`

**What It Does:**
- Maps JSON Schema components to FDM entities
- Defines read/write services
- Implements validation rules
- Configures caching

**Entity Mappings:**
| FDM Entity | Schema $def | Properties |
|------------|-------------|------------|
| `basePerson` | `basePerson` | firstName, lastName, email |
| `contactInfo` | `contactInfo` | phoneNumbers, preferredContactMethod |
| `address` | `address` | street1, city, state, postalCode, country, addressType |
| `loyaltyProgram` | `loyaltyProgram` | loyaltyID, tier, pointsBalance, joinDate |
| `marketingConsent` | `marketingConsent` | emailConsent, smsConsent, directMailConsent |

**Services Defined:**
- `readCustomer` - Query and search operations
- `writeCustomer` - Create and update operations
- `loyaltyService` - Points management

#### 4. Adaptive Form Template

**File:** `forms/customer-registration-form.xml`

**Form Structure:**
1. **Personal Information Tab**
   - First Name, Last Name (required)
   - Email with validation

2. **Contact Information Tab**
   - Phone numbers (repeater)
   - Phone type (mobile, home, work, fax)
   - SMS opt-in checkbox
   - Preferred contact method

3. **Address Tab**
   - Addresses (repeater)
   - Address type (shipping, billing, home, work)
   - Full address fields with postal code validation

4. **Loyalty Program Tab**
   - Join loyalty checkbox
   - Loyalty ID (read-only)
   - Tier display
   - Points balance

5. **Marketing Preferences Tab**
   - Email consent
   - SMS consent
   - Direct mail consent

**FDM Bindings:**
- Each field binds to FDM entity
- Repeaters handle arrays
- Validation rules integrated with FDM

### How It Works Together

```
┌─────────────────────────────────────────────────────────────────┐
│                    Adaptive Form                                 │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐          │
│  │ Personal│  │ Contact │  │ Address │  │ Loyalty │          │
│  └────┬────┘  └────┬────┘  └────┬────┘  └────┬────┘          │
│       │             │             │             │                 │
│       └────────────┼─────────────┼─────────────┘                 │
│                     │             │                                │
└─────────────────────┼─────────────┼────────────────────────────────┘
                      │             │
                      ▼             ▼
              ┌──────┴──────┐  ┌───┴────────┐
              │  FDM Entity │  │ FDM Entity │
              └──────┬──────┘  └─────┬──────┘
                     │               │
                     └───────┬───────┘
                             │
                             ▼
              ┌──────────────┴──────────────┐
              │   JSON Schema (allOf +     │
              │   $defs inheritance)       │
              └────────────────────────────┘
```

### Data Flow

**Form Submission Flow:**
```
1. User fills form
   ↓
2. Field validation (built-in + FDM rules)
   ↓
3. Data mapped to FDM entities
   ↓
4. Submit to FDM write service
   ↓
5. Service processes data
   ↓
6. Response returned to form
```

### Key Design Patterns

#### Pattern 1: Composition via `allOf`
```json
{
  "allOf": [
    { "$ref": "#/$defs/basePerson" },
    { "$ref": "#/$defs/contactInfo" }
  ]
}
```

#### Pattern 2: Reusable References
```json
{
  "properties": {
    "billingAddress": { "$ref": "#/$defs/address" },
    "shippingAddress": { "$ref": "#/$defs/address" }
  }
}
```

#### Pattern 3: Nested Arrays
```json
{
  "properties": {
    "phoneNumbers": {
      "type": "array",
      "items": { "$ref": "#/$defs/phoneNumber" }
    }
  }
}
```

### Validation Rules

FDM includes validation rules that execute during form submission:

```xml
<rule name="emailRequired">
    <condition>basePerson.email != null</condition>
    <message>Email is required</message>
</rule>

<rule name="smsRequiresMobile">
    <condition>contactInfo.preferredContactMethod == 'sms' implies 
                contactInfo.phoneNumbers.some(p => p.type == 'mobile')</condition>
    <message>SMS requires a mobile phone number</message>
</rule>
```

### Reusability Example

The same schema definitions can be used across multiple forms:

```
Form A: Customer Registration
├─ basePerson ✓
├─ contactInfo ✓
├─ address ✓
├─ loyaltyProgram (join) ✓
└─ marketingConsent ✓

Form B: Profile Update
├─ basePerson ✓
├─ contactInfo ✓
├─ preferences (extends loyaltyProgram)
└─ marketingConsent ✓

Form C: Loyalty Dashboard (read-only)
├─ basePerson ✓
└─ loyaltyProgram ✓
```

### Files Reference

| File | Purpose | Type |
|------|---------|------|
| `schema/retail-customer-inheritance.json.schema.json` | Main schema with inheritance | JSON Schema |
| `schema/definitions/address.schema.json` | Reusable address | JSON Schema |
| `schema/definitions/contactInfo.schema.json` | Reusable contact | JSON Schema |
| `schema/definitions/loyaltyProgram.schema.json` | Reusable loyalty | JSON Schema |
| `fdm/retail-customer-fdm.xml` | FDM configuration | XML |
| `forms/customer-registration-form.xml` | Form template | XML |

### AEM Compatibility

- **AEM Forms 6.5:** ✅ Full support
- **AEM as a Cloud Service:** ✅ Full support
- **Core Components:** ✅ Compatible
- **Foundation Components:** ✅ Compatible

### How to Use

1. **Import JSON Schema**
   - AEM Forms → Data Integrations
   - Create New Form Data Model
   - Select JSON Schema
   - Upload `retail-customer-inheritance.json.schema.json`

2. **FDM Auto-Creation**
   - AEM auto-creates entities from `$defs`
   - Properties map to entity properties
   - Arrays become repeatable entities

3. **Create Adaptive Form**
   - Create form using FDM
   - Drag fields from Data Model panel
   - Configure bindings and validation

4. **Test and Deploy**
   - Test form submission
   - Verify data mapping
   - Deploy to publish instance

### Benefits of This Approach

1. **Modularity**
   - Schema components are reusable
   - Easy to maintain and update
   - Consistent data structure

2. **Inheritance**
   - Compose complex types from simple ones
   - Avoid duplication
   - Clear hierarchy

3. **FDM Integration**
   - Automatic entity mapping
   - Built-in services
   - Validation rules

4. **No AEP Required**
   - Works standalone
   - No external dependencies
   - Simpler architecture

### Extending the Schema

To add new functionality:

1. Create new `$def`:
```json
{
  "$defs": {
    "newComponent": {
      "type": "object",
      "properties": {
        "customField": { "type": "string" }
      }
    }
  }
}
```

2. Add to `allOf`:
```json
{
  "allOf": [
    { "$ref": "#/$defs/basePerson" },
    { "properties": { "customData": { "$ref": "#/$defs/newComponent" } } }
  ]
}
```

3. Update FDM:
```xml
<entity name="newComponent">
    <property name="customField" path="customData/customField"/>
</entity>
```

### Validation

Schema includes:
- Email format validation
- Postal code pattern matching
- Required field enforcement
- Enum constraints for categorical data
- Min/max constraints for numbers

### Performance Considerations

FDM configuration includes caching:
```xml
<cache>
    <enabled>true</enabled>
    <ttl>300</ttl>
    <max-entries>100</max-entries>
</cache>
```

### Summary

This prototype demonstrates a modern approach to AEM Forms development using:
- **JSON Schema** for data modeling
- **Inheritance patterns** for reusability
- **FDM** for data integration
- **Adaptive Forms** for user interface

The architecture provides a clean separation between schema definition, data model, and form presentation, making it easy to maintain and extend.

---

**Created:** February 2026  
**AEM Version:** 6.5 and Cloud Service  
**Schema Version:** 2020-12
