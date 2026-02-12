# AEM Forms Cloud Service - Schema Inheritance Test

## Purpose
Empirically test whether JSON Schema `allOf` inheritance works in AEM Forms as a Cloud Service.

## Test Schemas Created

| File | Schema Version | Inheritance | Notes |
|------|----------------|-------------|-------|
| `test-inheritance.schema.json` | 2020-12 | allOf + $defs | Primary test |
| `test-inheritance-v4.schema.json` | v4 | allOf + definitions | v4 comparison |
| `test-flat.schema.json` | v4 | None | Fallback |

## Test Steps

### Step 1: Upload Schema
1. Go to AEM Forms Cloud Service Author
2. Navigate to **Forms** > **Forms & Documents**
3. Click **Create** > **Form Data Model**
4. Select "JSON Schema" as data source type
5. Upload `test-inheritance.schema.json`
6. Observe: Does AEM accept the schema?

### Step 2: Create Adaptive Form
1. Click "Create Adaptive Form" 
2. Select form based on FDM created above
3. Check: Do all fields appear in data model?
   - firstName
   - lastName
   - email
   - phone
   - street
   - city
   - postalCode

### Step 3: Test Form Submission
1. Fill form with test data
2. Submit form
3. Verify: Is submitted JSON structure correct?

### Step 4: Test with v4 Schema
Repeat with `test-inheritance-v4.schema.json`

### Step 5: Test with Flat Schema
Repeat with `test-flat.schema.json`

## Expected Outcomes

### If allOf WORKS:
- All 7 fields appear in form
- Submit produces valid JSON with all fields
- No errors during schema upload

### If allOf FAILS:
- Schema upload may fail OR
- Only first-level properties appear
- Some fields missing from data model
- Errors in form creation

## Cloud Service JSON Schema Documentation

According to Adobe Experience League:

**Supported:**
- Simple object types
- Array types with homogeneous items
- Common constraints (minLength, maxLength, pattern, format, enum)

**NOT Supported (as of latest docs):**
- `allOf`, `anyOf`, `oneOf`, `not`
- Union types
- Null type
- Complex nested references

## Workarounds if allOf Fails

### Option A: Flat Schema (Recommended for Cloud Service)
```json
{
  "type": "object",
  "properties": {
    "firstName": { "type": "string" },
    "lastName": { "type": "string" },
    "email": { "type": "string", "format": "email" }
  }
}
```

### Option B: $ref to External Schemas (Cloud Service Compatible)
```json
{
  "$ref": "https://example.com/schemas/basePerson.schema.json"
}
```

### Option C: Form Fragments
- Create reusable form fragments in AEM
- Reference fragments in multiple forms
- No schema inheritance needed

## Testing Checklist

- [ ] Schema uploads successfully
- [ ] All properties appear in data model
- [ ] Form creates without errors
- [ ] Submit produces valid JSON
- [ ] Test with 2020-12 schema
- [ ] Test with v4 schema
- [ ] Document results

## Results Log

| Test | Schema | Result | Notes |
|------|--------|--------|-------|
| Upload | 2020-12 + allOf | TBD | |
| Upload | v4 + allOf | TBD | |
| Upload | Flat | TBD | |
| Fields | 2020-12 + allOf | TBD | |
| Fields | v4 + allOf | TBD | |
| Fields | Flat | TBD | |

## Document Your Findings

Update this file with:
1. Which schemas worked
2. Any error messages
3. Field visibility issues
4. Submission behavior

## References

- [JSON Schema for Adaptive Forms (Core Components)](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/forms/adaptive-forms-authoring/authoring-adaptive-forms-core-components/create-an-adaptive-form-on-forms-cs/adaptive-form-core-components-json-schema-form-model)
- [Use Form Data Model - Experience League](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/forms/integrate/use-form-data-model/using-form-data-model)
- [Known Issues - AEM Forms Cloud Service](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/forms/forms-overview/known-issues)
