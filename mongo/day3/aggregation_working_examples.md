## Aggregation Examples 
# Sample Dataset — `students` Collection

## Create Database & Collection

```js
use aggregationDB
db.createCollection("students")
```

---

## Insert Sample Students Data

```js
db.students.insertMany([
  {
    rollNo: 1,
    name: "Amit",
    gender: "Male",
    state: "Karnataka",
    marks: 78,
    subject: "Maths"
  },
  {
    rollNo: 2,
    name: "Sneha",
    gender: "Female",
    state: "Karnataka",
    marks: 85,
    subject: "Maths"
  },
  {
    rollNo: 3,
    name: "Rahul",
    gender: "Male",
    state: "Maharashtra",
    marks: 72,
    subject: "Maths"
  },
  {
    rollNo: 4,
    name: "Pooja",
    gender: "Female",
    state: "Maharashtra",
    marks: 90,
    subject: "Maths"
  },
  {
    rollNo: 5,
    name: "Arun",
    gender: "Male",
    state: "Tamil Nadu",
    marks: 65,
    subject: "Maths"
  },
  {
    rollNo: 6,
    name: "Divya",
    gender: "Female",
    state: "Tamil Nadu",
    marks: 88,
    subject: "Maths"
  },
  {
    rollNo: 7,
    name: "Kiran",
    gender: "Male",
    state: "Karnataka",
    marks: 55,
    subject: "Maths"
  },
  {
    rollNo: 8,
    name: "Anjali",
    gender: "Female",
    state: "Karnataka",
    marks: 92,
    subject: "Maths"
  }
])
```

---



---

## `$group` — Students Count State-wise

```js
db.students.aggregate([
  {
    $group: {
      _id: "$state",
      totalStudents: { $sum: 1 }
    }
  }
])
```

Grouping documents based on a field.

---

## `$group` — Students Count Gender-wise

```js
db.students.aggregate([
  {
    $group: {
      _id: "$gender",
      totalStudents: { $sum: 1 }
    }
  }
])
```

---

## `$group` — Average Marks State-wise

```js
db.students.aggregate([
  {
    $group: {
      _id: "$state",
      avgMarks: { $avg: "$marks" }
    }
  }
])
```


> `$avg` works across **multiple documents**

---

## `$group` — Average Marks Gender-wise

```js
db.students.aggregate([
  {
    $group: {
      _id: "$gender",
      avgMarks: { $avg: "$marks" }
    }
  }
])
```

---

## `$match` — Filter Before Grouping 

### 👉 Karnataka students only

```js
db.students.aggregate([
  { $match: { state: "Karnataka" } },
  {
    $group: {
      _id: "$gender",
      avgMarks: { $avg: "$marks" }
    }
  }
])
```


> “Filter first, then group — fewer documents, faster execution”

---

## `$project` — Show Selected Fields Only

```js
db.students.aggregate([
  {
    $project: {
      name: 1,
      state: 1,
      marks: 1,
      _id: 0
    }
  }
])
```

---

## `$project` — Rename Fields

```js
db.students.aggregate([
  {
    $project: {
      studentName: "$name",
      location: "$state",
      score: "$marks",
      _id: 0
    }
  }
])
```

📌 Concept:

> `$project` is used to **shape the output**

---

## `$sort` — Highest Marks First

```js
db.students.aggregate([
  { $sort: { marks: -1 } }
])
```

---

## `$skip` & `$limit` — Pagination Example

### 👉 Top 3 students by marks

```js
db.students.aggregate([
  { $sort: { marks: -1 } },
  { $limit: 3 }
])
```

---

### 👉 Skip first 2, show next 3

```js
db.students.aggregate([
  { $sort: { marks: -1 } },
  { $skip: 2 },
  { $limit: 3 }
])
```

📌 Real-world mapping:

> Pagination in dashboards / tables

---

## 🔁 Combined Example 

```js
db.students.aggregate([
  { $match: { state: "Karnataka" } },
  {
    $group: {
      _id: "$gender",
      avgMarks: { $avg: "$marks" }
    }
  },
  { $sort: { avgMarks: -1 } }
])
```

---

