
## **Aggregation Framework — Part II**

### **Advanced Aggregation Concepts**

---

## Computed Fields

Computed fields are **derived fields created during aggregation**.
They exist **only in the output** and do not modify stored documents.
Commonly used for **calculations, reports, and analytics**.

---

### 1.1 Adding & Multiplying Values

Arithmetic operators allow MongoDB to **perform calculations inside the pipeline**.
They are usually used inside `$project` or `$addFields`.
These operations replace application-side calculations.

#### Example Data

```json
{
  "name": "Asha",
  "marks1": 70,
  "marks2": 80
}
```

---

### ➤ Add Two Fields

Adds numeric fields to create a **new calculated value**.
Original fields remain unchanged in the collection.

```js
db.students.aggregate([
  {
    $project: {
      name: 1,
      totalMarks: { $add: ["$marks1", "$marks2"] }
    }
  }
])
```

---

### ➤ Multiply Values

Used to **scale or normalize values**, such as percentages.
Supports **nested calculations** within a single stage.

```js
db.students.aggregate([
  {
    $project: {
      name: 1,
      percentage: {
        $multiply: [
          { $divide: [{ $add: ["$marks1", "$marks2"] }, 200] },
          100
        ]
      }
    }
  }
])
```

> **Arithmetic Operators:** `$add`, `$subtract`, `$multiply`, `$divide`

---

### 1.2 Conditional Projections

Conditional projections apply **if–else logic** during aggregation.
Used to **derive status, grades, or labels** dynamically.
Reduces the need for conditional logic in application code.

```js
db.students.aggregate([
  {
    $project: {
      name: 1,
      result: {
        $cond: {
          if: { $gte: ["$totalMarks", 100] },
          then: "Pass",
          else: "Fail"
        }
      }
    }
  }
])
```

---

## Advanced Pipeline Stages

Advanced stages help in **joining collections**, **flattening data**,
and **performing complex analytics** in a single pipeline.

---

## 2.1 `$lookup` — Joining Collections

`$lookup` combines documents from **two collections**.
It is the MongoDB equivalent of a **SQL JOIN**.
Result of `$lookup` is **always an array**.

---

### Join Example

```js
db.students.aggregate([
  {
    $lookup: {
      from: "courses",
      localField: "courseId",
      foreignField: "courseId",
      as: "courseDetails"
    }
  }
])
```

---

## 2.2 `$unwind` — Flatten Arrays

`$unwind` converts an **array field into individual documents**.
Commonly used after `$lookup`.
Required before grouping or projecting joined data.

```js
db.students.aggregate([
  { $lookup: { from: "courses", localField: "courseId", foreignField: "courseId", as: "course" }},
  { $unwind: "$course" }
])
```

---

## 2.3 `$facet` — Parallel Pipelines

`$facet` runs **multiple aggregations in parallel**.
Each facet produces a **separate result set**.
Useful for dashboards and summary reports.

```js
db.students.aggregate([
  {
    $facet: {
      avgMarks: [{ $group: { _id: null, avg: { $avg: "$marks" }}}],
      countByState: [{ $group: { _id: "$state", count: { $count: {} }}}]
    }
  }
])
```

---

## 2.4 `$bucket` & `$bucketAuto`

Used to **group numeric values into ranges**.
Common in **histograms, grading systems, and analytics**.

---

### `$bucket` — Manual Ranges

Ranges are **explicitly defined by the user**.
Documents outside ranges go into a default bucket.

```js
db.students.aggregate([
  {
    $bucket: {
      groupBy: "$marks",
      boundaries: [0, 50, 75, 100],
      default: "Others",
      output: { count: { $count: {} } }
    }
  }
])
```

---

### `$bucketAuto` — Automatic Ranges

MongoDB automatically creates **balanced ranges**.
Useful when distribution is unknown beforehand.

```js
db.students.aggregate([
  {
    $bucketAuto: {
      groupBy: "$marks",
      buckets: 4
    }
  }
])
```

---

## 2.5 `$replaceRoot` / `$replaceWith`

Replaces the **entire document structure**.
Used to promote nested objects to the top level.
Simplifies final output shape.

```js
db.students.aggregate([
  { $lookup: { from: "courses", localField: "courseId", foreignField: "courseId", as: "course" }},
  { $unwind: "$course" },
  { $replaceRoot: { newRoot: "$course" }}
])
```

---

## 3️⃣ Array Transformation Operators

Array operators transform or aggregate **array fields inside documents**.
They work similarly to JavaScript array methods.

---

## 3.1 `$map` — Transform Each Element

Applies a transformation to **every element** in an array.
Returns a new transformed array.

```js
{
  $map: {
    input: "$scores",
    as: "s",
    in: { $add: ["$$s", 5] }
  }
}
```

---

## 3.2 `$filter` — Select Specific Elements

Filters array elements based on a **condition**.
Returns only matching values.

```js
{
  $filter: {
    input: "$scores",
    as: "s",
    cond: { $gte: ["$$s", 60] }
  }
}
```

---

## 3.3 `$reduce` — Aggregate Array to Single Value

Reduces an array to **one final value**.
Used for custom sums, totals, or string concatenation.

```js
{
  $reduce: {
    input: "$scores",
    initialValue: 0,
    in: { $add: ["$$value", "$$this"] }
  }
}
```

---

## Conditional Operators

Conditional operators apply **decision logic** in aggregation.
They help derive categories and labels.

---

### `$cond`

Simple **if–else** condition.

```js
{ $cond: [ { $gte: ["$marks", 50] }, "Pass", "Fail" ] }
```

---

### `$switch`

Handles **multiple conditions** sequentially.
More readable than nested `$cond`.

```js
{
  $switch: {
    branches: [
      { case: { $gte: ["$marks", 80] }, then: "Distinction" },
      { case: { $gte: ["$marks", 60] }, then: "First Class" }
    ],
    default: "Fail"
  }
}
```

---

### `$ifNull`

Provides a **default value** when field is missing or null.
Useful for optional fields.

```js
{
  $project: {
    email: { $ifNull: ["$email", "Not Provided"] }
  }
}
```

---

## Output Stages

Output stages **write aggregation results back to collections**.
They should be used carefully in production systems.

---

## 5.1 `$out`

Writes aggregation output to a **new collection**.
If collection exists, it is **fully replaced**.
Risky if used on important collections.

```js
$out → replaces entire collection
```
```js
db.students.aggregate([
  // Step 1: Filter required documents
  { $match: { state: "KA" } },

  // Step 2: Shape the output
  {
    $project: {
      _id: 0,
      studentId: 1,
      name: 1,
      state: 1,
      marks: 1
    }
  },

  // Step 3: Write result to a new collection
  { $out: "ka_students" }
])
```

**Behavior:**

* Creates `ka_students` collection
* If it already exists → **all existing data is replaced**
* Use only when full replacement is intended

---

## 5.2 `$merge`

Writes results by **matching documents using a key**.
Existing documents are **updated**, not deleted.
Safer for production and incremental updates.

```js
$merge → update or insert based on match condition
```
```js
db.students.aggregate([
  // Step 1: Prepare summary data
  {
    $project: {
      studentId: 1,
      name: 1,
      totalMarks: { $add: ["$marks1", "$marks2"] }
    }
  },

  // Step 2: Merge into target collection
  {
    $merge: {
      into: "student_summary",   // target collection
      on: "studentId",           // matching key
      whenMatched: "merge",      // update existing document
      whenNotMatched: "insert"   // insert if not found
    }
  }
])
```

**Behavior:**

* Matches documents using `studentId`
* Updates existing records
* Inserts new records if no match
* Existing unrelated data remains untouched

---

## Quick Comparison (For Students)

| Feature               | `$out`       | `$merge`            |
| --------------------- | ------------ | ------------------- |
| Deletes existing data | ✅ Yes        | ❌ No                |
| Supports updates      | ❌ No         | ✅ Yes               |
| Safe for production   | ❌ Risky      | ✅ Recommended       |
| Use case              | Full rebuild | Incremental updates |


---

## Aggregation Best Practices

* Filter early using `$match`
* Keep `$project` minimal
* Avoid unnecessary `$lookup`
* Ensure indexes support filters
* Push `$group` after `$match`
* Avoid large in-memory operations

---


