## **Introduction**


![introduction-of-dbms](https://coding-platform.s3.amazonaws.com/dev/lms/tickets/8eede83e-2303-45ce-bed8-4cade1f4a474/VSpweicvCW2UIY3A.webp)

## **What is a Database (DB)?**

A **database** is an organized collection of data that allows:

* Efficient **storage**
* Easy **retrieval**
* Controlled **updates**
* Secure **management** of data

Instead of storing data in files (CSV, text), databases provide:

* Structure
* Querying capability
* Concurrency handling
* Data safety

### **Real-World Examples**

* Student records in a university system
* Orders in an e-commerce application
* User profiles in a social media app

---

## **Ideal Characteristics of a Database**

An ideal database should provide:

1. **Data Integrity**

   * Data should remain accurate and consistent
2. **Concurrency Control**

   * Multiple users can access data simultaneously
3. **Scalability**

   * Handle growth in data and users
4. **Performance**

   * Fast reads and writes
5. **Security**

   * Authentication, authorization, encryption
6. **Reliability**

   * No data loss on failures
7. **Flexibility**

   * Adapt to changing data requirements

---

## **What is an SQL (Relational) Database?**

An **SQL database** stores data in **structured tables** with:

* Fixed schema
* Rows & columns
* Relationships between tables

Data is queried using **Structured Query Language (SQL)**.

### **Behavior of SQL Databases**

* Schema must be defined **before inserting data**
* Strong rules and constraints
* Relationships via **foreign keys**
* ACID transactions by default

---

## **Use Cases of SQL Databases**

SQL databases are best when:

* Data structure is **stable**
* Strong **consistency** is required
* Complex **joins** are common
* Financial accuracy matters

### **Examples**

* Banking systems
* Payroll systems
* Inventory management
* ERP systems

---

## **Types of SQL Databases**

| Type          | Examples                 | Notes                         |
| ------------- | ------------------------ | ----------------------------- |
| Open Source   | MySQL, PostgreSQL        | Widely used, community driven |
| Enterprise    | Oracle DB, MS SQL Server | High cost, enterprise support |
| Embedded      | SQLite                   | Lightweight, local apps       |
| Cloud-Managed | Amazon RDS, Azure SQL    | Scalable, managed             |

---

## **What is a NoSQL Database?**

A **NoSQL database** is designed to handle:

* Large-scale data
* High traffic
* Flexible or evolving schemas

### **Behavior of NoSQL Databases**

* Schema-less or flexible schema
* Optimized for horizontal scaling
* High availability
* Often follows **BASE** principles

---

## **Use Cases of NoSQL Databases**

NoSQL is preferred when:

* Data structure changes frequently
* Huge volume of data
* Low-latency responses required
* Distributed systems

### **Examples**

* Social media feeds
* IoT sensor data
* Real-time analytics
* Recommendation systems

---

## **Types of NoSQL Databases**

| Type          | Description            | Examples             |
| ------------- | ---------------------- | -------------------- |
| Key-Value     | Simple key-value pairs | Redis, DynamoDB      |
| Document      | JSON-like documents    | **MongoDB**, CouchDB |
| Column-Family | Column-oriented        | Cassandra, HBase     |
| Graph         | Relationship-centric   | Neo4j                |

---

---

### **SQL vs NoSQL**
![sql-vs-nosql](https://coding-platform.s3.amazonaws.com/dev/lms/tickets/23fca46f-3637-4d98-9f71-e1976e6a8823/yjfqNOj3hF3KzAXp.png)

| Aspect        | SQL (Relational)        | NoSQL (Non-Relational)        |
| ------------- | ----------------------- | ----------------------------- |
| Data Model    | Tables (Rows & Columns) | Documents / Key-Value / Graph |
| Schema        | Fixed, predefined       | Flexible / Schema-less        |
| Scalability   | Vertical (scale up)     | Horizontal (scale out)        |
| Relationships | Joins                   | Embedded / Referenced         |
| Examples      | MySQL, PostgreSQL       | MongoDB, Cassandra            |

---

### **Why MongoDB? (Use Cases)**

![MongoDB](https://coding-platform.s3.amazonaws.com/dev/lms/tickets/bb3530d7-85f6-4a55-9ac0-5a6f07eeb484/ubVbG3sy1bYnZnaX.png)

MongoDB is a **document-oriented NoSQL database**.

**Why it is widely adopted:**

* Flexible schema (ideal for evolving applications)
* JSON-like data representation (developer friendly)
* High performance for read/write heavy workloads
* Horizontal scalability using sharding

**Typical Use Cases:**

* Web & mobile applications
* Learning Management Systems
* Real-time analytics dashboards
* Content management systems
* Microservices architectures

---

### **MongoDB Architecture**

![Data Organisation in MongoDB](https://coding-platform.s3.amazonaws.com/dev/lms/tickets/db11557f-9f40-4f95-9033-e91a117feb93/WosNP0yiwadjKtcg.png)


#### **Core Hierarchy**

```bash
Database
 └── Collection
      └── Document
```

* **Database**: Logical container for collections
* **Collection**: Group of documents (similar to tables)
* **Document**: Individual record stored in BSON format

![Mongo Interms of SQL](https://coding-platform.s3.amazonaws.com/dev/lms/tickets/3171694c-0ac6-4a2d-a974-e63807c1c6a1/G3BElafrMekaDIvY.png)

---

### **BSON vs JSON**

| JSON               | BSON                                     |
| ------------------ | ---------------------------------------- |
| Text-based         | Binary-based                             |
| Human readable     | Machine optimized                        |
| Limited data types | Rich data types (Date, ObjectId, Binary) |

**Important Concept:**
MongoDB stores data internally as **BSON** for performance, but developers interact using **JSON-like syntax**.

---

## **Mongo Shell / Compass Basics**

### **Connecting to MongoDB**

#### **Local Connection**

* MongoDB runs as a service on the system
* Default port: `27017`

#### **Remote / Cloud Connection**

* MongoDB Atlas (cloud-hosted)
* Connection string includes:

  * Username
  * Password
  * Cluster URL
---

### **MongoDB Shell (mongosh)**

The shell allows:

* Direct interaction with the database
* Executing queries
* Inspecting collections

Common shell operations:

* Switching databases
* Listing collections
* Executing CRUD commands

---

### **MongoDB Compass**

GUI tool for:

* Visualizing databases & collections
* Running queries
* Inspecting documents
* Teaching beginners database structure visually

**Pedagogical Advantage:**
Compass is ideal for classroom demos and beginner understanding.

---

### **Creating Databases & Collections**

* Databases are created **implicitly**
* A database is created when:

  * First collection is created
  * First document is inserted

Collections can be created:

* Explicitly
* Automatically during insert operations

---

## **CRUD Operations**

CRUD stands for:

* **Create**
* **Read**
* **Update**
* **Delete**

These are the foundational database operations.

---

### **Create Operations**

#### **insertOne**

* Inserts a single document
* Automatically generates `_id` if not provided

#### **insertMany**

* Inserts multiple documents in a single operation
* Improves performance for bulk inserts

**Note**
MongoDB does not enforce uniform document structure within a collection.

---

### **Read Operations**

#### **find**

* Retrieves documents from a collection
* Can accept:

  * Filters (conditions)
  * Projections (field selection)

#### **Filters**

* Match documents based on field values

#### **Projections**

* Include or exclude specific fields
* Useful for performance optimization

---

### **Update Operations**

#### **updateOne**

* Updates the first matching document

#### **updateMany**

* Updates all matching documents

**Important Concept:**
Updates use **update operators** (`$set`, `$inc`, etc.)
Without operators, the entire document is replaced.

---

### **Delete Operations**

#### **deleteOne**

* Deletes the first matching document

#### **deleteMany**

* Deletes all matching documents

**Note:**
Deletion operations are irreversible unless backed up.

---

## **Query Operators**

MongoDB provides powerful operators for flexible querying.

---

### **4.1 Comparison Operators**

| Operator | Meaning               |
| -------- | --------------------- |
| `$gt`    | Greater than          |
| `$lt`    | Less than             |
| `$gte`   | Greater than or equal |
| `$lte`   | Less than or equal    |
| `$ne`    | Not equal             |

Used for numeric, date, and comparable values.

---

### **Logical Operators**

| Operator | Purpose                           |
| -------- | --------------------------------- |
| `$and`   | All conditions must match         |
| `$or`    | At least one condition must match |
| `$not`   | Negates a condition               |



---

### **Array Operators**

Used when documents contain arrays.

| Operator     | Use                                                               |
| ------------ | ----------------------------------------------------------------- |
| `$all`       | Matches arrays containing all specified values                    |
| `$elemMatch` | Matches documents with array elements meeting multiple conditions |
| `$size`      | Matches array length                                              |

---

### **Pattern Search**

#### **$regex**

* Enables pattern-based string search
* Supports partial matches
* Useful for search functionality

**Note**
Regex queries can impact performance if indexes are not used.

---

### **Set Operators**

| Operator | Description                         |
| -------- | ----------------------------------- |
| `$in`    | Matches any value from a given list |
| `$nin`   | Excludes values from a given list   |

Commonly used in filtering user roles, categories, or statuses.

---
