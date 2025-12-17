# **C. MongoDB Schema Validation (JSON Schema)**

---

## **Why Validation in NoSQL?**

Even though MongoDB is schema-less:

* Applications expect structure
* Bad data causes runtime errors
* Validation improves data quality

---

## **JSON Schema-Based Validation**

MongoDB supports **JSON Schema rules**.

### **Key Validation Rules**

#### **bsonType**

* Enforces data type
* Example: string, int, array

#### **required**

* Mandatory fields

#### **enum**

* Allowed values only

#### **Numeric Validation**

* `minimum`
* `maximum`

#### **String Validation**

* `minLength`
* `maxLength`
* pattern (regex)

---

## **Implementing Validation**

### **At Collection Creation**

```js
db.createCollection("users", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "email"],
      properties: {
        name: { bsonType: "string" },
        email: { bsonType: "string" },
        age: { bsonType: "int", minimum: 18 }
      }
    }
  }
})
```
**or**
### Paste the following directly into the Validation Tab in Compass
```bash
{
  "$jsonSchema": {
    "bsonType": "object",
    "required": ["name", "email"],
    "properties": {
      "name": {
        "bsonType": "string",
        "description": "Name must be a string and is required"
      },
      "email": {
        "bsonType": "string",
        "description": "Email must be a string and is required"
      },
      "age": {
        "bsonType": "int",
        "minimum": 18,
        "description": "Age must be an integer >= 18"
      }
    }
  }
}
```
---

## **Validation Modes**

### **Strict**

* Invalid documents are rejected
* Best for production

### **Moderate**

* Existing invalid docs allowed
* New inserts validated
* Useful during migration

---

