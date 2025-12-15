# **Step 0: Create & Switch Database**

```js
use universityDB
```

---

# **Step 1: Insert Sample Data (Single Collection)**

### **Collection: `students`**

```js
db.students.insertMany([
  {
    name: "Ravi Kumar",
    age: 21,
    dept: "CSE",
    year: 3,
    cgpa: 8.2,
    skills: ["C", "Java", "DSA"],
    subjects: [
      { name: "DBMS", marks: 78 },
      { name: "OS", marks: 82 }
    ],
    city: "Bengaluru",
    active: true
  },
  {
    name: "Meena Sharma",
    age: 22,
    dept: "CSE",
    year: 4,
    cgpa: 8.9,
    skills: ["Python", "ML"],
    subjects: [
      { name: "ML", marks: 88 },
      { name: "AI", marks: 91 }
    ],
    city: "Hyderabad",
    active: true
  },
  {
    name: "Arjun Verma",
    age: 20,
    dept: "ECE",
    year: 2,
    cgpa: 7.4,
    skills: ["C", "Embedded"],
    subjects: [
      { name: "Networks", marks: 72 }
    ],
    city: "Pune",
    active: false
  },
  {
    name: "Sneha Iyer",
    age: 23,
    dept: "ME",
    year: 4,
    cgpa: 6.9,
    skills: ["AutoCAD", "Design"],
    subjects: [
      { name: "Thermodynamics", marks: 65 }
    ],
    city: "Chennai",
    active: true
  },
  {
    name: "Rahul Singh",
    age: 21,
    dept: "CSE",
    year: 3,
    cgpa: 7.8,
    skills: ["JavaScript", "React"],
    subjects: [
      { name: "Web Tech", marks: 84 }
    ],
    city: "Delhi",
    active: true
  }
])
```

✔ Database is now **created**
✔ Collection is **implicitly created**
✔ Rich data supports **all query operators**

---

# **Step 2: READ Operations (Find & Projection)**

### Find all students

```js
db.students.find()
```

---

### Pretty print

```js
db.students.find().pretty()
```

---

### Find students from CSE

```js
db.students.find({ dept: "CSE" })
```

---

### Projection (show only name and dept)

```js
db.students.find(
  { dept: "CSE" },
  { name: 1, dept: 1, _id: 0 }
)
```

---

# **Step 3: UPDATE Operations**

### Update ONE document

```js
db.students.updateOne(
  { name: "Ravi Kumar" },
  { $set: { cgpa: 8.4 } }
)
```

---

### Update MANY documents

```js
db.students.updateMany(
  { dept: "CSE" },
  { $set: { placementEligible: true } }
)
```

---

### Replace a document completely

```js
db.students.replaceOne(
  { name: "Arjun Verma" },
  {
    name: "Arjun Verma",
    dept: "ECE",
    cgpa: 7.6,
    active: true
  }
)
```

(Use this to explain **data loss risk**)

---

# **Step 4: DELETE Operations**

### Delete one student

```js
db.students.deleteOne({ name: "Sneha Iyer" })
```

---

### Delete many students

```js
db.students.deleteMany({ active: false })
```

---

### Delete all documents (collection stays)

```js
db.students.deleteMany({})
```

---

# **Step 5: QUERY OPERATORS (With This Data)**

---

## **Comparison Operators**

### Students with CGPA > 8

```js
db.students.find({ cgpa: { $gt: 8 } })
```

---

### Students with age ≤ 21

```js
db.students.find({ age: { $lte: 21 } })
```

---

## **Logical Operators**

### CSE students with CGPA ≥ 8

```js
db.students.find({
  $and: [
    { dept: "CSE" },
    { cgpa: { $gte: 8 } }
  ]
})
```

---

### Students from CSE or ECE

```js
db.students.find({
  $or: [{ dept: "CSE" }, { dept: "ECE" }]
})
```

---

## **Array Operators**

### Students who know both C and Java

```js
db.students.find({
  skills: { $all: ["C", "Java"] }
})
```

---

### Students with exactly 2 skills

```js
db.students.find({
  skills: { $size: 2 }
})
```

---

### Subject with marks ≥ 80 (nested array)

```js
db.students.find({
  subjects: {
    $elemMatch: { marks: { $gte: 80 } }
  }
})
```

---

## **Regex (Pattern Search)**

### Names starting with "R"

```js
db.students.find({
  name: { $regex: "^R" }
})
```

---

### Names containing "sh"

```js
db.students.find({
  name: { $regex: "sh", $options: "i" }
})
```

---

## **Set Operators**

### Students from selected cities

```js
db.students.find({
  city: { $in: ["Bengaluru", "Delhi"] }
})
```

---

### Exclude ME department

```js
db.students.find({
  dept: { $nin: ["ME"] }
})
```

---
