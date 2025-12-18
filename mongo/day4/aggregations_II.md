## **Aggregating Across Multiple Collections**

### 🧩 `$lookup` – Joining Two Collections

📝 **Use Case: Get user info with their orders**

**Collections:**

- `users` with fields `{ _id, name }`
- `orders` with field `userId`

```js
db.users.aggregate([
  {
    $lookup: {
      from: "orders",
      localField: "_id",
      foreignField: "userId",
      as: "userOrders",
    },
  },
]);
```

➡️ Joins `orders` to `users` where `_id === userId`

---

### 🪄 `$unwind` – Flattening Array Fields

**When to use:**
After `$lookup`, the joined data (`userOrders`) is an **array**.
To work on each order individually, **flatten it**.

```js
{
  $unwind: "$userOrders";
}
```

---

### 💡 Other Interesting Stages

| Stage     | Use Case                                                             |
| --------- | -------------------------------------------------------------------- |
| `$facet`  | Run multiple pipelines in parallel and combine results               |
| `$bucket` | Group documents into dynamic ranges (like age groups, price buckets) |
| `$cond`   | Conditional logic (if/else) inside aggregation                       |

---

## ⚠️ **Pitfalls & Gotchas**

### ❌ Confusing Aggregation with Query

- Aggregation returns **processed data**, not raw documents.
- Fields must be accessed using `$` (e.g., `"$amount"` not just `amount`).

### ❌ Not Using Correct Stage Order

- `$match` before `$group` for filtering (better performance)
- `$project` to clean up result structure (can be used before/after grouping)

### ❌ Forgetting `$unwind` After `$lookup`

- If you want to group or filter nested results, `$unwind` is often necessary.

---

## 📌 Summary Cheatsheet

| Stage        | Summary                                    |
| ------------ | ------------------------------------------ |
| `$match`     | Filter documents like `.find()`            |
| `$group`     | Group by field and perform aggregation     |
| `$project`   | Select and shape fields                    |
| `$lookup`    | Perform join-like operations               |
| `$unwind`    | Deconstruct arrays to individual documents |
| `$sort`      | Sort documents                             |
| `$limit`     | Return N documents                         |
| `$addFields` | Add computed or new fields                 |

---

