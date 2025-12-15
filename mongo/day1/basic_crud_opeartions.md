# **MongoDB Shell — Database & Collection Level Operations**

## **0. MongoDB Shell (mongosh) – Context Setting**

MongoDB Shell (`mongosh`) is:

* An interactive JavaScript-based interface
* Used to interact directly with MongoDB servers
* Ideal for understanding **core database mechanics**

**Why teach shell first?**

* Builds foundational understanding
* GUI tools abstract these details
* Helps instructors explain *what Compass does behind the scenes*

---

## **1. Database-Level Operations**

---

### **1.1 List All Databases**

Shows all databases present on the server.

```js
show dbs
```

**Important Note:**

* Databases appear only if they contain at least one collection with data.

---

### **1.2 Check Current Database**

```js
db
```

Returns the name of the currently selected database.

---

### **1.3 Create / Switch Database**

```js
use universityDB
```

**Key Concept:**

* MongoDB does **not** create the database immediately.
* Database is created **only after data is inserted**.

**Teaching Emphasis:**
This is *lazy creation*.

---

### **1.4 Drop Database**

Deletes the currently selected database permanently.

```js
db.dropDatabase()
```

**Instructor Warning:**

* Operation is irreversible
* Removes all collections and documents

---

### **1.5 Database Statistics**

Provides metadata about the database.

```js
db.stats()
```

Includes:

* Number of collections
* Storage size
* Index count
* Data size

---

## **2. Collection-Level Operations**

---

### **2.1 List All Collections in Current Database**

```js
show collections
```

---

### **2.2 Create Collection (Explicit)**

```js
db.createCollection("students")
```

**When to use explicit creation?**

* Applying validation rules
* Setting capped collections
* Enterprise-level control

---

### **2.3 Create Collection (Implicit)**

Automatically created when inserting a document.

```js
db.students.insertOne({ name: "Asha", dept: "CSE" })
```

**Teaching Insight:**

* This is the most common real-world approach

---

### **2.4 Rename Collection**

```js
db.students.renameCollection("learners")
```

---

### **2.5 Drop Collection**

Deletes an entire collection.

```js
db.learners.drop()
```

---

### **2.6 Collection Statistics**

```js
db.students.stats()
```

Provides:

* Document count
* Storage usage
* Index information

---

## **3. Document-Level Operations (CRUD via Shell)**

Although CRUD is document-level, it is **executed via collections**, so it belongs in today’s shell session.

---

## **3.1 INSERT Operations**

---

### **Insert One Document**

```js
db.students.insertOne({
  name: "Ravi",
  age: 21,
  dept: "ECE",
  skills: ["C", "Java"]
})
```

**Key Concepts:**

* `_id` is auto-generated
* Documents can have nested arrays and objects

---

### **Insert Multiple Documents**

```js
db.students.insertMany([
  { name: "Meena", dept: "CSE" },
  { name: "Arjun", dept: "ME" }
])
```

---

## **3.2 READ Operations**

---

### **Find All Documents**

```js
db.students.find()
```

---

### **Pretty Print Output**

```js
db.students.find().pretty()
```

---

### **Filter Documents**

```js
db.students.find({ dept: "CSE" })
```

---

### **Projection (Select Fields)**

```js
db.students.find(
  { dept: "CSE" },
  { name: 1, _id: 0 }
)
```

---

## **3.3 UPDATE Operations**

---

### **Update One Document**

```js
db.students.updateOne(
  { name: "Ravi" },
  { $set: { age: 22 } }
)
```

---

### **Update Many Documents**

```js
db.students.updateMany(
  { dept: "CSE" },
  { $set: { batch: 2025 } }
)
```

---

### **Replace Entire Document**

```js
db.students.replaceOne(
  { name: "Meena" },
  { name: "Meena", dept: "AI" }
)
```

**Instructor Emphasis:**

* Replacement removes fields not specified

---

## **3.4 DELETE Operations**

---

### **Delete One Document**

```js
db.students.deleteOne({ name: "Arjun" })
```

---

### **Delete Many Documents**

```js
db.students.deleteMany({ dept: "ME" })
```

---

### **Delete All Documents (Collection Retained)**

```js
db.students.deleteMany({})
```

---

## **4. Query Operators (Shell Focused)**

---

### **4.1 Comparison Operators**

```js
db.students.find({ age: { $gt: 20 } })
```

Operators:

* `$gt`, `$lt`, `$gte`, `$lte`, `$ne`

---

### **4.2 Logical Operators**

```js
db.students.find({
  $and: [
    { dept: "CSE" },
    { age: { $gte: 21 } }
  ]
})
```

```js
db.students.find({
  $or: [{ dept: "CSE" }, { dept: "ECE" }]
})
```

---

### **4.3 Array Operators**

```js
db.students.find({ skills: { $all: ["Java", "C"] } })
```

```js
db.students.find({
  skills: { $elemMatch: { $eq: "Java" } }
})
```

```js
db.students.find({ skills: { $size: 2 } })
```

---

### **4.4 Regex Search**

```js
db.students.find({ name: { $regex: "^R" } })
```

---

### **4.5 Set Operators**

```js
db.students.find({ dept: { $in: ["CSE", "ECE"] } })
```

```js
db.students.find({ dept: { $nin: ["ME"] } })
```

---
