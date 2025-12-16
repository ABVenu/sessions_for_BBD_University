
# **B. Relationship Modeling in MongoDB**

## 1. Need for Relationships

### Why Relationships Are Needed

- When your project grows, embedding large numbers of nested documents (like orders inside customers) can make documents too big and slow to query.
- For example, a customer may have hundreds or thousands of orders. Embedding all orders inside the customer document makes it heavy and hard to maintain.
- Instead, use **separate schemas and collections**, and **link** them using references (IDs).
- This keeps main documents lightweight and improves querying and updating.

---

## 2. Types of Relationships in MongoDB

### 2.1 Classification

- **One-to-One (1:1)**
- **One-to-Many (1\:N)**
- **Many-to-Many (M\:N)**

You can implement these using **embedding** or **referencing**.

---

### 2.2 One-to-One Example

![One to One Relationship](https://coding-platform.s3.amazonaws.com/dev/lms/tickets/8a4a8d74-636b-4ce2-991c-39870923eda6/FlsAhzAihlapZNry.png)

User and Profile:

- Embedding (for small profile data):

```js
{
  _id: "user123",
  name: "Alice",
  profile: {
    age: 25,
    bio: "Software Engineer"
  }
}
```

- Referencing (for larger or optional profiles):

```js
// User
{
  _id: "user123",
  name: "Alice",
  profileId: "profile123"
}

// Profile
{
  _id: "profile123",
  age: 25,
  bio: "Software Engineer"
}
```

---

### 2.3 One-to-Many Example

![One to Many Relationship](https://coding-platform.s3.amazonaws.com/dev/lms/tickets/b38d8743-7d93-4a4b-b6c2-36005fa98e74/jYs0pYdHbDjxLAhs.png)
Customer and Orders:

### **Parent Document: Post**

```js
// Post
{
  _id: "post001",
  title: "Understanding JavaScript Closures",
  content: "Closures are functions that have access to variables from another function’s scope...",
  author: "Alice"
}
```

---

### **Child Documents: Comments**

```js
// Comment 1
{
  _id: "comm001",
  postId: "post001",
  commenter: "Bob",
  comment: "Great explanation! Really helped me understand closures."
}

// Comment 2
{
  _id: "comm002",
  postId: "post001",
  commenter: "Charlie",
  comment: "Can you give more examples of practical use cases?"
}
```

---

### Key Idea:

- This is a **One-to-Many (1\:N)** relationship:

  - **One Post** ➝ can have **many Comments**.
  - Comments hold the foreign key: `postId` pointing to the Post’s `_id`.

---

### 2.4 Many-to-Many Example

![Many To Many Relationship](https://coding-platform.s3.amazonaws.com/dev/lms/tickets/85a3b9c4-0176-4c1b-951f-9f03fd2af158/QjesjTv3eVpnv3NY.png)

Students and Courses:

```js
// Student
{
  _id: "stu001",
  name: "Charlie",
  enrolledCourses: ["course001", "course002"]
}

// Course
{
  _id: "course001",
  title: "Node.js Mastery",
  enrolledStudents: ["stu001", "stu002"]
}
```

---



---

## **Modeling Approaches**

### **1. Embedding Documents**

* Related data stored in same document
* Faster reads
* Atomic updates

Example use:

* User with address
* Order with items

---

### **2. Referencing Documents**

* Store ObjectId references
* Data stored in separate collections

Example use:

* Large datasets
* Frequently changing data

---

### **3. Hybrid Approach**

* Embed frequently accessed data
* Reference large or growing data

---

## **When to Embed vs Reference**

| Scenario              | Recommended           |
| --------------------- | --------------------- |
| High read performance | Embed                 |
| Large arrays          | Reference             |
| Data grows unbounded  | Reference             |
| Need joins            | Reference + `$lookup` |

---
