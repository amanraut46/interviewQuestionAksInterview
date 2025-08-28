
# 1 🔹 What is a Transaction in SQL?

* A **Transaction** is a sequence of one or more SQL operations executed as a single unit of work.
* Either **all operations succeed (COMMIT)** or **all fail (ROLLBACK)**.
* Transactions ensure **data integrity** and follow the **ACID properties**:

  * **A**tomicity → All or nothing
  * **C**onsistency → Database remains valid
  * **I**solation → Multiple transactions don’t affect each other
  * **D**urability → Once committed, changes are permanent

---

## 🔹 Basic Syntax of Transaction in SQL Server

```sql
BEGIN TRANSACTION;

-- SQL statements
UPDATE Accounts SET Balance = Balance - 500 WHERE AccountId = 1;
UPDATE Accounts SET Balance = Balance + 500 WHERE AccountId = 2;

-- If all good
COMMIT TRANSACTION;

-- If any error
ROLLBACK TRANSACTION;
```

---

## 🔹 Example: Money Transfer

Imagine transferring money from **Account A to Account B**.
Both debit and credit must succeed; otherwise, rollback.

```sql
BEGIN TRANSACTION;

UPDATE Accounts
SET Balance = Balance - 1000
WHERE AccountId = 1;

UPDATE Accounts
SET Balance = Balance + 1000
WHERE AccountId = 2;

IF @@ERROR <> 0
    ROLLBACK TRANSACTION;
ELSE
    COMMIT TRANSACTION;
```

---

## 🔹 Using `TRY...CATCH` in SQL Server

A better way is with `TRY...CATCH`:

```sql
BEGIN TRY
    BEGIN TRANSACTION;

    UPDATE Accounts
    SET Balance = Balance - 1000
    WHERE AccountId = 1;

    UPDATE Accounts
    SET Balance = Balance + 1000
    WHERE AccountId = 2;

    COMMIT TRANSACTION;
END TRY
BEGIN CATCH
    ROLLBACK TRANSACTION;
    PRINT 'Error occurred: ' + ERROR_MESSAGE();
END CATCH
```

---

# 2. **What is a Transaction in SQL?**

👉 Answer: A group of SQL operations executed as a single unit of work, ensuring data integrity using ACID properties.

---

# 3. **What is the difference between COMMIT and ROLLBACK?**

👉

* **COMMIT** → Saves changes permanently.
* **ROLLBACK** → Undo changes made in the current transaction.

---

# 4. **What is the difference between Implicit and Explicit Transactions?**

👉

* **Implicit**: SQL automatically treats each statement as a transaction (auto-commit).
* **Explicit**: Developer controls using `BEGIN TRANSACTION`, `COMMIT`, `ROLLBACK`.

---

# 5. **What is a Savepoint in SQL Transactions?**

👉 Savepoints allow partial rollbacks inside a transaction.
Example:

```sql
BEGIN TRANSACTION;

UPDATE Accounts SET Balance = Balance - 500 WHERE AccountId = 1;
SAVE TRANSACTION SavePoint1;

UPDATE Accounts SET Balance = Balance + 500 WHERE AccountId = 2;

ROLLBACK TRANSACTION SavePoint1; -- Rollback only to SavePoint1
COMMIT TRANSACTION;
```

---

# 6. **What are Isolation Levels in SQL Transactions?**

👉 Defines how transaction integrity is visible to other transactions.

* **Read Uncommitted** (Dirty Read)
* **Read Committed** (Default in SQL Server)
* **Repeatable Read**
* **Serializable**
* **Snapshot**

Perfect 👍 — this is another classic SQL interview topic. Let’s structure it properly with **question + answer + comparison**.

# 7 What is the difference between a CTE, a Table Variable, and a Temporary Table in SQL Server?

---

### ✅ Answer

In SQL Server, **CTE (Common Table Expression)**, **Table Variables**, and **Temporary Tables** are used to hold intermediate results, but they differ in scope, performance, and usage.

---

## 🔹 1. CTE (Common Table Expression)

* **Definition**: A temporary result set defined within a `WITH` clause, used only for the immediate query.
* **Scope**: Exists only for the duration of the query.
* **Storage**: Logical (not stored physically in tempdb).
* **Performance**: Good for readability, recursion, and breaking complex queries.
* **Limitations**: Cannot have indexes, statistics, or be reused across multiple queries (within same batch, can be referenced multiple times).

**Example:**

```sql
WITH CTE_Employee AS
(
    SELECT DepartmentId, COUNT(*) AS EmpCount
    FROM Employees
    GROUP BY DepartmentId
)
SELECT * FROM CTE_Employee WHERE EmpCount > 5;
```

---

## 🔹 2. Table Variable

* **Definition**: A variable that holds table data, declared with `DECLARE @`.
* **Scope**: Exists only within the batch, procedure, or function.
* **Storage**: Stored in **memory** (but spills to `tempdb` if too large).
* **Performance**: Faster for small datasets.
* **Limitations**: Limited indexing (only primary key, unique constraints), no statistics → query optimizer may choose poor plans.

**Example:**

```sql
DECLARE @Emp TABLE
(
    EmpId INT,
    EmpName NVARCHAR(100)
);

INSERT INTO @Emp VALUES (1, 'Aman'), (2, 'Revati');
SELECT * FROM @Emp;
```

---

## 🔹 3. Temporary Table

* **Definition**: A real table stored in **tempdb**, created with `#` (local) or `##` (global).
* **Scope**:

  * Local (`#Temp`) → available only in the session.
  * Global (`##Temp`) → available across all sessions until dropped.
* **Storage**: Stored physically in `tempdb`.
* **Performance**: Supports indexing, statistics, constraints → better for large datasets.
* **Limitations**: Slight overhead in creation/dropping compared to table variables.

**Example:**

```sql
CREATE TABLE #Emp
(
    EmpId INT,
    EmpName NVARCHAR(100)
);

INSERT INTO #Emp VALUES (1, 'Aman'), (2, 'Revati');
SELECT * FROM #Emp;
```

---

## 🔹 Key Differences Table

| Feature     | CTE                          | Table Variable                  | Temporary Table                 |
| ----------- | ---------------------------- | ------------------------------- | ------------------------------- |
| Scope       | Single query                 | Batch/procedure/function        | Session (local) or global       |
| Storage     | Logical only                 | Memory / tempdb (if large)      | tempdb (physical)               |
| Reusability | Only within same query       | Can be reused in same scope     | Can be reused within session    |
| Indexing    | ❌ No                         | Limited (PK/Unique only)        | ✅ Full indexing, constraints    |
| Statistics  | ❌ No                         | ❌ No                            | ✅ Yes                           |
| Best for    | Query readability, recursion | Small datasets, lightweight ops | Large datasets, complex queries |

---

### 🔹 Short Interview-Friendly Summary

👉 **CTE**: For query readability and recursion (no physical storage).
👉 **Table Variable**: Lightweight, good for small datasets (limited indexing/statistics).
👉 **Temporary Table**: Full-featured, good for large datasets (supports indexing, stats).

---

# 8 Write a single SQL query to update a table column named `Flag` such that:

* If the value is `0`, it should be updated to `1`.
* If the value is `1`, it should be updated to `0`.

---

### ✅ Answer

You can do this using a **CASE expression** in the `UPDATE` statement:

```sql
UPDATE YourTable
SET Flag = CASE 
              WHEN Flag = 0 THEN 1
              WHEN Flag = 1 THEN 0
              ELSE Flag  -- in case there are other values
           END;
```

---

### 🔄 Alternate Simple Way (Bitwise XOR for 0/1 toggle)

If the column only contains `0` and `1`, you can simply do:

```sql
UPDATE YourTable
SET Flag = 1 - Flag;
```

or

```sql
UPDATE YourTable
SET Flag = Flag ^ 1;
```

(both will flip 0→1 and 1→0).

---

👉 Between these, the `1 - Flag` or `Flag ^ 1` is the **cleanest and fastest**, but `CASE` is safer if `Flag` might have other values.

---

Do you want me to also frame this as a **SQL interview question with follow-ups** (like performance considerations and constraints)?


