
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


