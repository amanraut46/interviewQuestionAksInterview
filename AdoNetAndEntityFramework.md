
# 1. **Can we use ADO.NET and Entity Framework together in the same project?**

👉 Yes, but it should be done carefully. EF is built on top of ADO.NET, so mixing them can cause **transaction, connection, and change tracking issues** if not managed properly.

---

# 2. **What problems occur when mixing ADO.NET and EF?**

👉

* Transaction conflicts
* Multiple connections
* EF not tracking changes made via ADO.NET
* Data inconsistency
* Increased code complexity

---

# 3. **When would you prefer ADO.NET over Entity Framework in a project?**

👉

* For **bulk operations** (large inserts/updates).
* When you need **fine-grained performance tuning**.
* For calling **complex stored procedures**.

---

# 4. **How can you make sure EF and ADO.NET use the same transaction?**

👉 Use `context.Database.BeginTransaction()` and then share the connection and transaction object with ADO.NET code.

Example:

```csharp
using (var transaction = context.Database.BeginTransaction())
{
    // EF operation
    context.Users.Add(new User { Name = "Aman" });
    context.SaveChanges();

    // ADO.NET operation with same connection
    using (var command = context.Database.GetDbConnection().CreateCommand())
    {
        command.Transaction = transaction.GetDbTransaction();
        command.CommandText = "INSERT INTO Logs VALUES ('User Added')";
        command.ExecuteNonQuery();
    }

    transaction.Commit();
}
```

---

# 5. **Which is better: ADO.NET or Entity Framework?**

👉

* **ADO.NET** → Best for **performance-critical, low-level control**.
* **EF** → Best for **developer productivity and maintainability**.
* Use EF for most scenarios, ADO.NET only when needed.
