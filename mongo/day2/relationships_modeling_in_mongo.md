
# **B. Relationship Modeling in MongoDB**

MongoDB supports relationships but **models them differently than SQL**.

---

## **Types of Relationships**

### **1. One-to-One**

* Example: User → Profile

### **2. One-to-Many**

* Example: User → Orders

### **3. Many-to-Many**

* Example: Students ↔ Courses

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

## **Anti-Patterns**

* Overly deep nesting (hard to query)
* Massive unbounded arrays
* Duplicating fast-changing data

---
