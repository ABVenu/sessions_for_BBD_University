

# Aggregation Framework

---

## 1. Why Aggregation?

In real applications, data is rarely used **as-is**.
We often need to **analyze**, **summarize**, or **transform** data before showing it in dashboards, reports, or APIs.

### 1.1 What Is Aggregation?

The **Aggregation Framework** in MongoDB allows you to:

* Process documents step-by-step
* Transform data into a new shape
* Produce **calculated results**, not raw documents

Think of aggregation as:

> “SQL `GROUP BY` + calculations + transformations — but much more powerful”

---

### 1.2 When Do We Need Aggregation?

Aggregation is used when we need:

#### ✔ Data Transformation

* Convert raw data into meaningful formats
* Combine fields
* Rename fields
* Create new computed fields

#### ✔ Reporting & Analytics

* Total sales per day
* Average marks per subject
* Number of users per city
* Max / Min / Average values

#### ✔ Replacing Complex Queries

Some problems are **hard or impossible** with normal `find()` queries:

* Grouping documents
* Calculations across multiple documents
* Conditional logic

👉 Aggregation **replaces multiple queries + application-side logic** with one pipeline.

---

## 2. Aggregation vs find()

| Feature        | find() | aggregate() |
| -------------- | ------ | ----------- |
| Filtering      | ✅      | ✅           |
| Projection     | ✅      | ✅           |
| Grouping       | ❌      | ✅           |
| Calculations   | ❌      | ✅           |
| Multiple steps | ❌      | ✅           |

👉 **Rule of thumb:**
If you only need documents → `find()`
If you need **analysis or transformation** → `aggregate()`

---

## 3. Core Pipeline Structure

### 3.1 What Is a Pipeline?

An aggregation pipeline is:

* An **array of stages**
* Each stage performs **one operation**
* Output of one stage → input to the next

```js
db.collection.aggregate([
  { stage1 },
  { stage2 },
  { stage3 }
])
```

---

### 3.2 Stages

Each stage:

* Starts with `$`
* Has a specific responsibility
* Processes **all incoming documents**

Examples:

* `$match` → filters documents
* `$project` → reshapes documents
* `$group` → aggregates data
* `$sort` → orders documents

---

### 3.3 Document Flow Behavior

Important concept:

> Documents **flow like water** through a pipeline

* Stage 1 filters / modifies documents
* Stage 2 works only on what Stage 1 outputs
* Removed fields or documents are **gone forever**

📌 Order of stages **matters**

---

## 4. Important Aggregation Stages

---

## 4.1 `$match` — Filtering Documents

### Purpose

* Filters documents
* Works like `find()` conditions
* Should be placed **as early as possible**

### Example

```js
db.orders.aggregate([
  {
    $match: { status: "DELIVERED" }
  }
])
```

✔ Only delivered orders move to the next stage

📌 **Performance Tip:**
Filtering early reduces the number of documents processed later.

---

## 4.2 `$project` — Shaping Documents

### Purpose

* Select fields
* Rename fields
* Remove fields
* Create new computed fields

### Example: Select Fields

```js
{
  $project: {
    customerName: 1,
    totalAmount: 1,
    _id: 0
  }
}
```

✔ Output contains only selected fields

---

### Example: Rename Fields

```js
{
  $project: {
    name: "$customerName",
    amount: "$totalAmount"
  }
}
```

---

## 4.3 `$group` — Grouping & Aggregation

### Purpose

* Combine multiple documents into one
* Perform calculations

### Structure

```js
{
  $group: {
    _id: <grouping key>,
    fieldName: { $accumulator: <value> }
  }
}
```

---

### Common Accumulators

| Accumulator | Meaning             |
| ----------- | ------------------- |
| `$sum`      | Total               |
| `$avg`      | Average             |
| `$min`      | Minimum             |
| `$max`      | Maximum             |
| `$count`    | Number of documents |

---

### Example: Total Orders per Customer

```js
{
  $group: {
    _id: "$customerId",
    totalOrders: { $sum: 1 }
  }
}
```

---

### Example: Average Marks per Subject

```js
{
  $group: {
    _id: "$subject",
    avgMarks: { $avg: "$marks" }
  }
}
```

📌 After `$group`, **original fields are lost** unless explicitly included.

---

## 4.4 `$sort` — Ordering Results

### Purpose

* Sort output documents

### Example

```js
{
  $sort: { totalAmount: -1 }
}
```

✔ `1` → ascending
✔ `-1` → descending

---

## 4.5 `$skip` and `$limit` — Pagination

### `$skip`

Skips first N documents

```js
{ $skip: 10 }
```

---

### `$limit`

Limits number of documents

```js
{ $limit: 5 }
```

---

### Pagination Example

```js
[
  { $sort: { createdAt: -1 } },
  { $skip: 10 },
  { $limit: 5 }
]
```

---

## 5. Computed Fields

Aggregation allows **calculations inside the pipeline**

---

## 5.1 Mathematical Operations

### Example: Total Price

```js
{
  $project: {
    totalPrice: { $multiply: ["$price", "$quantity"] }
  }
}
```

---

### Example: Add Bonus

```js
{
  $project: {
    finalScore: { $add: ["$score", 5] }
  }
}
```

---

## 5.2 Conditional Projections

### Example: Pass / Fail

```js
{
  $project: {
    name: 1,
    result: {
      $cond: {
        if: { $gte: ["$marks", 40] },
        then: "PASS",
        else: "FAIL"
      }
    }
  }
}
```

✔ Introduces **decision-making** inside MongoDB

---

## 6. Putting It All Together (Pipeline Example)

```js
db.orders.aggregate([
  { $match: { status: "DELIVERED" } },
  {
    $group: {
      _id: "$customerId",
      totalAmount: { $sum: "$amount" },
      orderCount: { $sum: 1 }
    }
  },
  { $sort: { totalAmount: -1 } },
  { $limit: 5 }
])
```

📌 This pipeline:

1. Filters delivered orders
2. Groups by customer
3. Calculates totals
4. Sorts
5. Returns top 5 customers

---

## 7. Performance Concepts

---

### 7.1 Match Early → Faster Pipelines

Bad:

```js
[ { $group }, { $match } ]
```

Good:

```js
[ { $match }, { $group } ]
```

✔ Fewer documents → faster aggregation

---

### 7.2 Use Indexes Before Aggregation

* `$match` can use indexes
* Indexes are used **only before grouping**
* After `$group`, indexes no longer apply

📌 Index fields used in:

* `$match`
* `$sort` (before `$group`)

---

## 8. Key Takeaways

* Aggregation is for **analysis & transformation**
* Pipeline is **stage-by-stage**
* Order of stages matters
* `$match` early improves performance
* `$group` changes document structure
* Aggregation reduces application-side logic

---


