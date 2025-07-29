
# MongoDB Schema Validation

MongoDB allows you to enforce rules on documents within a collection using **schema validation**. This helps ensure data consistency and correctness.

## How to Set Validation Rules

When creating a collection or modifying it, you can specify validation rules using the `validator` option with a MongoDB query language expression or JSON Schema.

### Example: Creating a Collection with Validation

```js
db.createCollection("users", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "email", "age"],
      properties: {
        name: {
          bsonType: "string",
          description: "must be a string and is required"
        },
        email: {
          bsonType: "string",
          pattern: "^.+@.+$",
          description: "must be a valid email address and is required"
        },
        age: {
          bsonType: "int",
          minimum: 18,
          maximum: 100,
          description: "must be an integer between 18 and 100"
        }
      }
    }
  },
  validationLevel: "strict",  // Enforces validation on all inserts and updates
  validationAction: "error"   // Rejects documents that violate rules
})
```

### Explanation:
- **`validator`**: Defines the validation rules.
- **`$jsonSchema`**: Uses JSON Schema to describe document structure.
- **`required`**: Lists fields that must be present.
- **`bsonType`**: Specifies the expected data type.
- **`pattern`**: For string fields, enforces regex pattern.
- **`validationLevel`**: `strict` applies validation on all writes.
- **`validationAction`**: `error` rejects invalid documents; `warn` allows but logs warnings.


---

Using validation improves data quality by preventing incorrect or incomplete documents from being inserted or updated in your MongoDB collections.
