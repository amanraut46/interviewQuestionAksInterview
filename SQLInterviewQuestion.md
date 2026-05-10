
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
# 9 **What are ACID properties in databases?**

---

## ✅ Answer  

### 1. **Atomicity**  
- A transaction is **all-or-nothing**.  
- If any part of the transaction fails, the entire transaction is rolled back.  
- **Example:** Transferring ₹100 from Account A to Account B. If debit succeeds but credit fails, the whole transaction is undone.  

---

### 2. **Consistency**  
- Ensures the database moves from one **valid state to another valid state**.  
- All rules, constraints, and relationships must remain intact.  
- **Example:** In a banking system, the total balance across accounts must remain constant before and after a transfer.  

---

### 3. **Isolation**  
- Transactions run **independently** without interfering with each other.  
- Prevents problems like:  
  - **Dirty reads** (reading uncommitted data)  
  - **Non-repeatable reads** (data changes between two reads)  
  - **Phantom reads** (new rows appear during a transaction)  
- **Example:** Two users transferring money at the same time should not affect each other’s results.  

---

### 4. **Durability**  
- Once a transaction is **committed**, its changes are permanent—even if the system crashes.  
- Achieved through logging and backup mechanisms.  
- **Example:** After a successful transfer, the updated balances remain stored even if the server shuts down immediately.  

---

## 📌 Key Takeaway  
- **Atomicity** → All-or-nothing execution.  
- **Consistency** → Database remains valid.  
- **Isolation** → Transactions don’t interfere.  
- **Durability** → Changes persist permanently.  

# 10 **How do you find the sum of department salaries of employees in SQL?**

---

### ✅ Answer  

You can use the **`SUM()` aggregate function** along with **`GROUP BY`** to calculate the total salary for each department.

---

### 📌 Example Table  
Suppose you have an `Employees` table:

| EmpId | EmpName | Department | Salary |
|-------|----------|------------|--------|
| 1     | Aman     | IT         | 50000  |
| 2     | Ravi     | HR         | 40000  |
| 3     | Neha     | IT         | 60000  |
| 4     | Priya    | Finance    | 45000  |
| 5     | Arjun    | HR         | 35000  |

---

### 📌 SQL Query  
```sql
SELECT Department, SUM(Salary) AS TotalSalary
FROM Employees
GROUP BY Department;
```

---

### 📌 Output  
| Department | TotalSalary |
|------------|-------------|
| IT         | 110000      |
| HR         | 75000       |
| Finance    | 45000       |

---

### ⚖️ Key Takeaway  
- `SUM(Salary)` → adds up all salaries.  
- `GROUP BY Department` → groups employees by department before summing.  
- This query is widely used in **payroll systems, HR dashboards, and reporting tools**.  

---

# 11 📊 What are Dirty Reads?
- A **dirty read** occurs when a transaction reads data that has been modified by another transaction but not yet committed.
- If the other transaction rolls back, the first transaction has read invalid data.
- Example: Transaction A updates a balance but hasn’t committed. Transaction B reads that balance. If A rolls back, B’s read was “dirty.”

# 12 What is the difference between `RANK()` and `DENSE_RANK()` in SQL? Give an example.

---

### ✅ Answer:

**1. `RANK()`**
- Assigns ranks based on ordering.
- If multiple rows tie, they get the same rank.
- The next rank **skips numbers** depending on how many tied rows exist.

**Example:**
```sql
SELECT Name, Score, RANK() OVER (ORDER BY Score DESC) AS Rank
FROM Students;
```

| Name  | Score | Rank |
|-------|-------|------|
| Alice | 90    | 1    |
| Jane  | 90    | 1    |
| John  | 85    | 3    |
| Mark  | 85    | 3    |

👉 Notice that **Rank 2 is skipped** because two students tied at Rank 1.

---

**2. `DENSE_RANK()`**
- Similar to `RANK()`, but **does not skip ranks**.
- Tied rows get the same rank, and the next rank is consecutive.

**Example:**
```sql
SELECT Name, Score, DENSE_RANK() OVER (ORDER BY Score DESC) AS DenseRank
FROM Students;
```

| Name  | Score | DenseRank |
|-------|-------|-----------|
| Alice | 90    | 1         |
| Jane  | 90    | 1         |
| John  | 85    | 2         |
| Mark  | 85    | 2         |

👉 Here, ranks are **continuous** with no gaps.

---

### 📌 Quick Comparison

| Feature              | RANK()                          | DENSE_RANK()                     |
|-----------------------|---------------------------------|----------------------------------|
| Handling ties         | Same rank for ties              | Same rank for ties               |
| Next rank             | Skips numbers after ties        | No skipping, consecutive ranks   |
| Example with ties     | 1, 1, 3, 3, 5                   | 1, 1, 2, 2, 3                    |
| Use case              | Competitions (where gaps matter)| Analytics/reporting (continuous) |

---

👉 In short:  
- Use **`RANK()`** when you want gaps (like sports tournaments).  
- Use **`DENSE_RANK()`** when you want continuous ranking (like categorizing levels).  

---

 
# 13 What is normalization in databases? Explain normal forms with rules and examples.

---

## 📖 Definition  
**Normalization** is the process of organizing data in a relational database into multiple related tables to reduce redundancy and improve data integrity. Each stage of normalization is called a **Normal Form (NF)**, and each has specific rules.

---

## 📊 Normal Forms with Examples  

### 🔹 **1NF (First Normal Form)**  
**Definition:** A table is in 1NF if all attributes contain only atomic (indivisible) values, and there are no repeating groups.  
**Rule:**  
- Each column must hold atomic values.  
- No repeating groups or multi-valued attributes.  

**Example (Before 1NF):**  
| StudentID | StudentName | Courses         |  
|-----------|-------------|-----------------|  
| 1         | Aman        | Math, Physics   |  

**After 1NF:**  
| StudentID | StudentName | Course   |  
|-----------|-------------|----------|  
| 1         | Aman        | Math     |  
| 1         | Aman        | Physics  |  

---

### 🔹 **2NF (Second Normal Form)**  
**Definition:** A table is in 2NF if it is in 1NF and all non-key attributes are fully dependent on the entire primary key (no partial dependency).  
**Rule:**  
- Must be in 1NF.  
- No partial dependency (non-key attributes must depend on the whole primary key).  

**Example (Before 2NF):**  
| StudentID | CourseID | StudentName | CourseName |  
|-----------|----------|-------------|------------|  
| 1         | 101      | Aman        | Math       |  

Here, `StudentName` depends only on `StudentID`, not on the composite key (`StudentID + CourseID`).  

**After 2NF:**  
**Students Table**  
| StudentID | StudentName |  
|-----------|-------------|  
| 1         | Aman        |  

**Courses Table**  
| CourseID | CourseName |  
|----------|------------|  
| 101      | Math       |  

**Enrollment Table**  
| StudentID | CourseID |  
|-----------|----------|  
| 1         | 101      |  

---

### 🔹 **3NF (Third Normal Form)**  
**Definition:** A table is in 3NF if it is in 2NF and has no transitive dependency (non-key attributes should not depend on other non-key attributes).  
**Rule:**  
- Must be in 2NF.  
- No transitive dependency.  

**Example (Before 3NF):**  
| StudentID | StudentName | DeptName | DeptHead   |  
|-----------|-------------|----------|------------|  
| 1         | Aman        | Science  | Dr. Mehta  |  

Here, `DeptHead` depends on `DeptName`, not directly on `StudentID`.  

**After 3NF:**  
**Students Table**  
| StudentID | StudentName | DeptName |  
|-----------|-------------|----------|  
| 1         | Aman        | Science  |  

**Departments Table**  
| DeptName | DeptHead   |  
|----------|------------|  
| Science  | Dr. Mehta  |  

---

### 🔹 **BCNF (Boyce-Codd Normal Form)**  
**Definition:** A stronger version of 3NF. A table is in BCNF if every determinant is a candidate key.  
**Rule:**  
- Must be in 3NF.  
- For every functional dependency (X → Y), X must be a candidate key.  

**Example (Before BCNF):**  
| CourseID | Instructor | Room |  
|----------|------------|------|  
| 101      | Dr. A      | R1   |  

If one instructor always teaches in one room, then `Instructor → Room` is a dependency, but `Instructor` is not a candidate key.  

**After BCNF:**  
**Course Table**  
| CourseID | Instructor |  
|----------|------------|  
| 101      | Dr. A      |  

**Instructor Table**  
| Instructor | Room |  
|------------|------|  
| Dr. A      | R1   |  

---

## 🎯 Benefits of Normalization
- Eliminates redundancy.  
- Ensures data consistency.  
- Simplifies updates and maintenance.  
- Improves query efficiency.  

---


