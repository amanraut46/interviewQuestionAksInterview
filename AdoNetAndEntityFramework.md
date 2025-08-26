
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
Got it 👍 you want the **interview-style question + answer** format.
Here’s how it can be framed:

---
# 6 **How does automatic connection closing work in ADO.NET connection-oriented architecture?**
In ADO.NET connection-oriented architecture, connections to the database are managed explicitly using classes like `SqlConnection`. However, there are ways where connections can be automatically closed:

1. **Using `using` Statement**
   Wrapping a `SqlConnection` in a `using` block ensures the connection is automatically closed and disposed when the block ends—even if an exception occurs.

   ```csharp
   using (SqlConnection con = new SqlConnection("connection_string"))
   {
       con.Open();
       SqlCommand cmd = new SqlCommand("SELECT * FROM Employees", con);
       SqlDataReader reader = cmd.ExecuteReader();
   } // con.Close() automatically called here
   ```

2. **Connection Pooling**
   When `Close()` or `Dispose()` is called, the connection is not physically closed but returned to the pool. If a developer forgets to close it, the garbage collector eventually releases it, but this is not reliable.

3. **DataAdapter with Fill()**
   When using `SqlDataAdapter` with `Fill()`, the connection is opened and closed automatically inside the method.

   ```csharp
   SqlDataAdapter da = new SqlDataAdapter("SELECT * FROM Employees", "connection_string");
   DataTable dt = new DataTable();
   da.Fill(dt); // opens & closes automatically
   ```

4. **SqlDataReader with CommandBehavior.CloseConnection**
   If a `SqlDataReader` is created with `CommandBehavior.CloseConnection`, the associated connection is automatically closed when the reader is closed.

   ```csharp
   SqlCommand cmd = new SqlCommand("SELECT * FROM Employees", con);
   SqlDataReader reader = cmd.ExecuteReader(CommandBehavior.CloseConnection);
   while (reader.Read())
   {
       Console.WriteLine(reader["Name"]);
   }
   reader.Close(); // automatically closes connection too
   ```
Got it 👍 Let me format it properly with the **question and answer**.

---

# 7 : What is Lazy Loading in Entity Framework?

**Answer:**
Lazy Loading in Entity Framework is a technique where related data (navigation properties) is **loaded from the database only when it is first accessed**, not when the parent entity is initially retrieved.

---

### **Example**

```csharp
public class Student
{
    public int Id { get; set; }
    public string Name { get; set; }

    // Virtual enables lazy loading
    public virtual ICollection<Course> Courses { get; set; }
}

public class Course
{
    public int Id { get; set; }
    public string Title { get; set; }
}
```

```csharp
var student = context.Students.FirstOrDefault(); 
// Only student is loaded here

var courses = student.Courses; 
// At this point, EF fires another query to load Courses
```

---

### **Key Points**

* Works when navigation properties are marked as **virtual** (EF creates proxies).
* Delays fetching until needed → improves initial performance.
* Can cause **N+1 query problem** if looping over many entities.

---

### **Comparison of Loading Types**

| Loading Type         | When Data Loads           | How to Use                      | Pros               | Cons                         |
| -------------------- | ------------------------- | ------------------------------- | ------------------ | ---------------------------- |
| **Lazy Loading**     | When property is accessed | `virtual` navigation properties | Saves initial cost | Multiple queries (N+1 issue) |
| **Eager Loading**    | With main query           | `.Include()`                    | Fewer DB trips     | Loads unused data            |
| **Explicit Loading** | On demand, manually       | `context.Entry(...).Load()`     | Full control       | More code                    |

---

✅ **In short:**
Lazy Loading = Data is retrieved **only when accessed**, not immediately.

---

Do you also want me to prepare **short 2–3 line interview-friendly answers** for such questions (like a quick revision sheet)?
