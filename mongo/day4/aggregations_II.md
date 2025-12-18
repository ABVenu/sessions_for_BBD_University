
# Aggregation Framework Part II

## **Advanced Aggregation Concepts**

---

## Computed Fields

Computed fields allow you to **create new fields dynamically** during aggregation without modifying original data.

---

### 1.1 Adding & Multiplying Values

Used mainly inside **$project** or **$addFields**.

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

> **Note:** MongoDB arithmetic operators
`$add`, `$subtract`, `$multiply`, `$divide`

---

### 1.2 Conditional Projections

Conditional logic inside **$project** to derive values based on conditions.

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

---

## 2.1 `$lookup` — Joining Collections

Equivalent of **SQL JOIN**.

### Collections

**students**

```json
{ "studentId": 1, "name": "Rahul", "courseId": 101 }
```

**courses**

```json
{ "courseId": 101, "courseName": "MongoDB" }
```

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

> Result: `courseDetails` will be an **array**

---

## 2.2 `$unwind` — Flatten Arrays

Used after `$lookup`.

```js
db.students.aggregate([
  { $lookup: { from: "courses", localField: "courseId", foreignField: "courseId", as: "course" }},
  { $unwind: "$course" }
])
```

- Converts array → object
- Essential before grouping or projecting joined data

---

## 2.3 `$facet` — Parallel Pipelines

Run **multiple aggregations in one query**.

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

📌 Returns **multiple result sets in one output**

---

## 2.4 `$bucket` & `$bucketAuto`

### `$bucket` — Manual Ranges

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

📌 Used for **histograms & analytics**

---

## 2.5 `$replaceRoot` / `$replaceWith`

Replace entire document structure.

```js
db.students.aggregate([
  { $lookup: { from: "courses", localField: "courseId", foreignField: "courseId", as: "course" }},
  { $unwind: "$course" },
  { $replaceRoot: { newRoot: "$course" }}
])
```

📌 Final output contains **only course document**

---

## 3️⃣Array Transformation Operators

---

## 3.1 `$map` — Transform Each Element

```js
db.students.aggregate([
  {
    $project: {
      scoresPlusFive: {
        $map: {
          input: "$scores",
          as: "s",
          in: { $add: ["$$s", 5] }
        }
      }
    }
  }
])
```

---

## 3.2 `$filter` — Select Specific Elements

```js
db.students.aggregate([
  {
    $project: {
      highScores: {
        $filter: {
          input: "$scores",
          as: "s",
          cond: { $gte: ["$$s", 60] }
        }
      }
    }
  }
])
```

---

## 3.3 `$reduce` — Aggregate Array to Single Value

```js
db.students.aggregate([
  {
    $project: {
      totalScore: {
        $reduce: {
          input: "$scores",
          initialValue: 0,
          in: { $add: ["$$value", "$$this"] }
        }
      }
    }
  }
])
```

📌 Used for **custom summations & concatenations**

---

## Conditional Operators

---

### `$cond`

```js
{ $cond: [ { $gte: ["$marks", 50] }, "Pass", "Fail" ] }
```

---

### `$switch` — Multiple Conditions

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

```js
{
  $project: {
    email: { $ifNull: ["$email", "Not Provided"] }
  }
}
```

---

## Output Stages

---

## 5.1 `$out` — Write to New Collection

```js
db.students.aggregate([
  { $match: { state: "KA" }},
  { $out: "ka_students" }
])
```

📌 Replaces existing collection if exists

---

## 5.2 `$merge` — Upsert into Collection

```js
db.students.aggregate([
  {
    $merge: {
      into: "student_summary",
      on: "studentId",
      whenMatched: "merge",
      whenNotMatched: "insert"
    }
  }
])
```

📌 Safer than `$out`

---

## Aggregation Best Practices

### Performance Guidelines

* **Filter early** using `$match`
* Keep `$project` minimal
* Avoid unnecessary `$lookup`
* Use indexes before aggregation
* Push `$group` **after filtering**
* Avoid large in-memory sorts
