## **Aggregation Framework — Part II**

### **Advanced Aggregation Concepts**

# 1️⃣ Data to Insert

---

## 📘 `courses` Collection (Master Data)

```js
db.courses.insertMany([
  { courseId: 101, courseName: "MongoDB", credits: 4 },
  { courseId: 102, courseName: "React", credits: 3 },
  { courseId: 103, courseName: "Node.js", credits: 5 }
])
```

---

## 🎓 `students` Collection (Embedded Courses Array)

```js
db.students.insertMany([
  {
    studentId: 1,
    rollNo: 1,
    name: "Amit",
    gender: "Male",
    state: "Karnataka",
    courses: [
      { courseId: 101, score: 78 },
      { courseId: 102, score: 72 }
    ],
    email: "amit@gmail.com"
  },
  {
    studentId: 2,
    rollNo: 2,
    name: "Sneha",
    gender: "Female",
    state: "Karnataka",
    courses: [
      { courseId: 101, score: 88 },
      { courseId: 103, score: 91 }
    ]
  },
  {
    studentId: 3,
    rollNo: 3,
    name: "Rahul",
    gender: "Male",
    state: "Maharashtra",
    courses: [
      { courseId: 102, score: 65 }
    ],
    email: null
  },
  {
    studentId: 4,
    rollNo: 4,
    name: "Pooja",
    gender: "Female",
    state: "Maharashtra",
    courses: [
      { courseId: 101, score: 92 },
      { courseId: 102, score: 89 },
      { courseId: 103, score: 95 }
    ]
  },
  {
    studentId: 5,
    rollNo: 5,
    name: "Arun",
    gender: "Male",
    state: "Tamil Nadu",
    courses: [
      { courseId: 102, score: 58 }
    ]
  },
  {
    studentId: 6,
    rollNo: 6,
    name: "Divya",
    gender: "Female",
    state: "Tamil Nadu",
    courses: [
      { courseId: 103, score: 90 },
      { courseId: 101, score: 85 }
    ]
  }
])
```

---
## `$unwind` — Flatten Courses Array

```js
db.students.aggregate([
  { $unwind: "$courses" }
])
```

📌 Required for **grouping, joining, bucketing**

---

## `$lookup` — Join Course Details

```js
db.students.aggregate([
  { $unwind: "$courses" },
  {
    $lookup: {
      from: "courses",
      localField: "courses.courseId",
      foreignField: "courseId",
      as: "courseInfo"
    }
  },
  { $unwind: "$courseInfo" }
])
```

---

## `$project` — Computed Fields

### ➤ Show Student + Course + Score

```js
db.students.aggregate([
  { $unwind: "$courses" },
  {
    $project: {
      name: 1,
      courseId: "$courses.courseId",
      score: "$courses.score"
    }
  }
])
```

---

## Computed Field — Percentage (Out of 100)

```js
db.students.aggregate([
  { $unwind: "$courses" },
  {
    $project: {
      name: 1,
      percentage: "$courses.score"
    }
  }
])
```

---

## `$cond` — Pass / Fail Per Course

```js
db.students.aggregate([
  { $unwind: "$courses" },
  {
    $project: {
      name: 1,
      courseId: "$courses.courseId",
      result: {
        $cond: [
          { $gte: ["$courses.score", 60] },
          "Pass",
          "Fail"
        ]
      }
    }
  }
])
```

---

## `$switch` — Grade Classification

```js
db.students.aggregate([
  { $unwind: "$courses" },
  {
    $project: {
      name: 1,
      courseId: "$courses.courseId",
      grade: {
        $switch: {
          branches: [
            { case: { $gte: ["$courses.score", 85] }, then: "Distinction" },
            { case: { $gte: ["$courses.score", 65] }, then: "First Class" }
          ],
          default: "Fail"
        }
      }
    }
  }
])
```

---

## `$group` — Average Score Per State

```js
db.students.aggregate([
  { $unwind: "$courses" },
  {
    $group: {
      _id: "$state",
      avgScore: { $avg: "$courses.score" }
    }
  }
])
```

---

## `$group` — Average Score Per Course

```js
db.students.aggregate([
  { $unwind: "$courses" },
  {
    $group: {
      _id: "$courses.courseId",
      avgScore: { $avg: "$courses.score" }
    }
  }
])
```

---

## `$facet` — Multiple Results in One Query

```js
db.students.aggregate([
  { $unwind: "$courses" },
  {
    $facet: {
      avgScoreByState: [
        { $group: { _id: "$state", avg: { $avg: "$courses.score" } } }
      ],
      countByGender: [
        { $group: { _id: "$gender", count: { $count: {} } } }
      ]
    }
  }
])
```

---

## `$bucket` — Manual Score Ranges

```js
db.students.aggregate([
  { $unwind: "$courses" },
  {
    $bucket: {
      groupBy: "$courses.score",
      boundaries: [0, 60, 75, 90, 100],
      default: "Others",
      output: {
        students: { $count: {} }
      }
    }
  }
])
```

---

## `$bucketAuto` — Automatic Histogram

```js
db.students.aggregate([
  { $unwind: "$courses" },
  {
    $bucketAuto: {
      groupBy: "$courses.score",
      buckets: 4
    }
  }
])
```

---

## Array Operators

### ➤ `$map` — Extract Scores Array

```js
db.students.aggregate([
  {
    $project: {
      name: 1,
      scores: {
        $map: {
          input: "$courses",
          as: "c",
          in: "$$c.score"
        }
      }
    }
  }
])
```

---

### ➤ `$filter` — Courses with Score ≥ 80

```js
db.students.aggregate([
  {
    $project: {
      name: 1,
      highScoreCourses: {
        $filter: {
          input: "$courses",
          as: "c",
          cond: { $gte: ["$$c.score", 80] }
        }
      }
    }
  }
])
```

---

### ➤ `$reduce` — Total Score Per Student

```js
db.students.aggregate([
  {
    $project: {
      name: 1,
      totalScore: {
        $reduce: {
          input: "$courses",
          initialValue: 0,
          in: { $add: ["$$value", "$$this.score"] }
        }
      }
    }
  }
])
```

---

## `$ifNull`

```js
db.students.aggregate([
  {
    $project: {
      name: 1,
      email: { $ifNull: ["$email", "Not Provided"] }
    }
  }
])
```

---

## `$replaceRoot` — Only Course Info

```js
db.students.aggregate([
  { $unwind: "$courses" },
  {
    $lookup: {
      from: "courses",
      localField: "courses.courseId",
      foreignField: "courseId",
      as: "course"
    }
  },
  { $unwind: "$course" },
  { $replaceRoot: { newRoot: "$course" } }
])
```

---

## `$out`

```js
db.students.aggregate([
  { $match: { state: "Karnataka" } },
  { $out: "ka_students" }
])
```

---

## `$merge`

```js
db.students.aggregate([
  {
    $project: {
      studentId: 1,
      name: 1,
      totalCourses: { $size: "$courses" }
    }
  },
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

---

