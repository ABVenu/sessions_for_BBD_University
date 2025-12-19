
# **MongoDB – Performance, Scaling & Production Concepts**

---

# **A. Indexing (Core of MongoDB Performance)**

## **Why Indexing Is Critical**

* MongoDB stores documents on disk
* Without indexes → MongoDB must scan **every document** (`COLLSCAN`)
* Indexes allow MongoDB to **jump directly to matching documents** (`IXSCAN`)
* Indexes trade:

  * 📈 Faster reads
  * 📉 Slightly slower writes & extra storage

> Rule of thumb: **Indexes are for read performance**

---

## **1. Single-Field Index**

### Concept

* Index on **one field**
* Most basic and common index

### Syntax

```js
db.users.createIndex({ email: 1 })
```

### Usage

```js
db.users.find({ email: "abc@gmail.com" })
```

### When to Use

* Frequently queried fields
* Filters, sorts

---

## **2. Compound Index**

### Concept

* Index on **multiple fields**
* Order of fields **matters**

### Syntax

```js
db.orders.createIndex({ userId: 1, createdAt: -1 })
```

### Query Matching Rule (Prefix Rule)

Index works for:

```js
{ userId }
{ userId, createdAt }
```

❌ Not for:

```js
{ createdAt }
```

### When to Use

* Queries filtering on multiple fields
* Sorting + filtering together

---

## **3. Multikey Index**

### Concept

* Automatically created when indexing **array fields**

### Example Data

```js
{
  name: "Amit",
  skills: ["React", "Node", "Mongo"]
}
```

### Create Index

```js
db.users.createIndex({ skills: 1 })
```

### Query

```js
db.users.find({ skills: "Mongo" })
```

> Mongo creates **multiple index entries** per array value

---

## **4. Unique Index**

### Concept

* Ensures **no duplicate values**

### Syntax

```js
db.users.createIndex(
  { email: 1 },
  { unique: true }
)
```

### Behavior

* Duplicate insert → ❌ error
* Commonly used for:

  * email
  * username
  * phone number

---

## **5. TTL (Time To Live) Index**

### Concept

* Automatically deletes documents **after a time**
* Used for:

  * logs
  * sessions
  * OTPs
  * temp data

### Example

```js
db.sessions.createIndex(
  { createdAt: 1 },
  { expireAfterSeconds: 3600 }
)
```

> MongoDB background thread deletes expired docs
> ⚠️ Works only on **Date fields**

---

## **6. Text Index**

### Concept

* Full-text search on string content

### Create

```js
db.posts.createIndex({ content: "text" })
```

### Query

```js
db.posts.find({
  $text: { $search: "mongodb performance" }
})
```

### Notes

* Supports stemming & relevance score
* Only **one text index per collection**

---

## **7. Wildcard Index**

### Concept

* Index **unknown or dynamic fields**
* Useful in flexible schemas

### Syntax

```js
db.logs.createIndex({ "$**": 1 })
```

### When to Use

* Logging systems
* Dynamic metadata fields

⚠️ Use carefully → can increase index size

---

## **Index Performance Analysis**

---

## **1. explain() – Understanding Query Execution**

### Syntax

```js
db.users.find({ email: "a@gmail.com" }).explain("executionStats")
```

### Key Terms

| Term              | Meaning                |
| ----------------- | ---------------------- |
| COLLSCAN          | Full collection scan ❌ |
| IXSCAN            | Index scan ✅           |
| nReturned         | Docs returned          |
| totalDocsExamined | Docs Mongo checked     |

> Goal: **IXSCAN + fewer documents examined**

---

## **2. Covered Queries**

### Concept

Query satisfied **entirely from index**

* No document fetch
* Extremely fast

### Example

```js
db.users.createIndex({ email: 1, name: 1 })

db.users.find(
  { email: "a@gmail.com" },
  { email: 1, name: 1, _id: 0 }
)
```

---

## **3. Cardinality & Selectivity**

* **Cardinality** → number of unique values
* **High selectivity** = better index

Example:

* gender → low cardinality ❌
* email → high cardinality ✅

---

# **B. Transactions**

---

## **ACID Properties**

| Property    | Meaning        |
| ----------- | -------------- |
| Atomicity   | All or nothing |
| Consistency | Valid state    |
| Isolation   | No dirty reads |
| Durability  | Data persists  |

MongoDB supports **multi-document transactions** (4.0+).

---

## **When Transactions Are Needed**

Mongo is atomic **at document level**, but needed when:

* Updating multiple collections
* Financial transfers
* Booking systems

---

## **Transaction Example**

```js
const session = db.getMongo().startSession();

session.startTransaction();

try {
  session.getDatabase("shop").orders.insertOne(
    { item: "Laptop" },
    { session }
  );

  session.getDatabase("shop").inventory.updateOne(
    { item: "Laptop" },
    { $inc: { stock: -1 } },
    { session }
  );

  session.commitTransaction();
} catch (e) {
  session.abortTransaction();
}
```

---

## **Transaction Limitations**

* Slower than normal writes
* Locks resources
* Should be **short-lived**
* Not ideal for high-throughput systems

---

# **C. Scaling & Replication**

---

## **Replica Sets (High Availability)**

### Concept

* Multiple copies of data
* Automatic failover

### Architecture

* **Primary** → writes
* **Secondary** → reads (optional)
* **Arbiter** → voting only

---

## **Read Preference**

```js
readPreference: "secondary"
```

| Mode      | Meaning             |
| --------- | ------------------- |
| primary   | Default             |
| secondary | Read from secondary |
| nearest   | Lowest latency      |

---

## **Write Concern**

Controls **acknowledgement level**

```js
{ w: "majority" }
```

* Higher safety → slower writes
* Lower safety → faster writes

---

## **Sharding (Horizontal Scaling)**

### Concept

* Split data across multiple machines
* Required for **huge datasets**

---

## **Shard Key**

### Most Important Decision

Good shard key:

* High cardinality
* Even distribution
* Frequently queried

Bad shard key:

* Low cardinality (gender, status)
* Monotonically increasing values

---

## **Sharded Cluster Architecture**

* **Shard servers** → store data
* **Config servers** → metadata
* **mongos** → query router

---

## **Balancer**

* Automatically redistributes chunks
* Keeps data evenly spread

---

# **D. Bulk Operations**

---

## **bulkWrite() Concept**

* Perform **multiple operations in one request**
* Reduces network overhead

---

## **Example**

```js
db.users.bulkWrite([
  { insertOne: { document: { name: "A" } } },
  { updateOne: { filter: { name: "B" }, update: { $set: { age: 30 } } } },
  { deleteOne: { filter: { name: "C" } } }
])
```

---

## **Ordered vs Unordered**

| Mode              | Behavior             |
| ----------------- | -------------------- |
| ordered (default) | Stops on first error |
| unordered         | Continues execution  |

```js
db.users.bulkWrite(ops, { ordered: false })
```

---

## **Use Cases**

* Data migration
* Batch processing
* ETL jobs

---

# **E. MongoDB Atlas (Production Cloud MongoDB)**

---

## **Creating a Cluster**

* Choose provider (AWS/GCP/Azure)
* Choose region
* Choose tier (M0 free, M10+ paid)

---

## **Connecting from Compass**

* Copy connection string
* Paste in Compass
* Add username/password

---

## **Performance Advisor**

* Analyzes slow queries
* Suggests indexes
* One-click index creation

---

## **Index Suggestions**

* Based on real query workload
* Shows impact before applying

---

## **Data Explorer**

* CRUD operations in browser
* Aggregations UI
* Index management

---

## **Backups & Snapshots**

* Automated backups
* Point-in-time restore
* Essential for production

---

## **Monitoring Metrics**

| Metric      | Meaning          |
| ----------- | ---------------- |
| CPU         | Compute usage    |
| IOPS        | Disk operations  |
| Connections | Client load      |
| Memory      | Cache efficiency |

---

