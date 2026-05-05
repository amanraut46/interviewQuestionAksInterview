
# 1. **Can we use ADO.NET and Entity Framework together in the same project?**
 Yes, but it should be done carefully. EF is built on top of ADO.NET, so mixing them can cause **transaction, connection, and change tracking issues** if not managed properly.

---

# 2. **What problems occur when mixing ADO.NET and EF?**
* Transaction conflicts
* Multiple connections
* EF not tracking changes made via ADO.NET
* Data inconsistency
* Increased code complexity

---

# 3. **When would you prefer ADO.NET over Entity Framework in a project?**
* For **bulk operations** (large inserts/updates).
* When you need **fine-grained performance tuning**.
* For calling **complex stored procedures**.

---

# 4. **How can you make sure EF and ADO.NET use the same transaction?**

 Use `context.Database.BeginTransaction()` and then share the connection and transaction object with ADO.NET code.

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
# 8 **How do you create a many-to-many relationship in Entity Framework Core (EF Core)?**

### ✅ Answer  

In EF Core, a many-to-many relationship is created by defining navigation properties on both entities. EF Core automatically generates a join table, or you can explicitly configure one if you need more control.

---

#### 🔹 Example 1: Implicit Join Table  
```csharp
public class Student
{
    public int Id { get; set; }
    public string Name { get; set; }

    public List<Course> Courses { get; set; } = new();
}

public class Course
{
    public int Id { get; set; }
    public string Title { get; set; }

    public List<Student> Students { get; set; } = new();
}
```

- EF Core will create a join table (e.g., `StudentCourse`) automatically.  
- No need to define the join entity unless you want extra columns.

---

#### 🔹 Example 2: Fluent API Customization  
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Student>()
        .HasMany(s => s.Courses)
        .WithMany(c => c.Students)
        .UsingEntity(j => j.ToTable("Enrollments"));
}
```

- This renames the join table to **Enrollments**.  
- EF Core still manages the relationship automatically.

---

#### 🔹 Example 3: Explicit Join Entity (with extra fields)  
```csharp
public class Enrollment
{
    public int StudentId { get; set; }
    public Student Student { get; set; }

    public int CourseId { get; set; }
    public Course Course { get; set; }

    public DateTime EnrollmentDate { get; set; }
}
```

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Enrollment>()
        .HasKey(e => new { e.StudentId, e.CourseId });

    modelBuilder.Entity<Enrollment>()
        .HasOne(e => e.Student)
        .WithMany(s => s.Enrollments)
        .HasForeignKey(e => e.StudentId);

    modelBuilder.Entity<Enrollment>()
        .HasOne(e => e.Course)
        .WithMany(c => c.Enrollments)
        .HasForeignKey(e => e.CourseId);
}
```

---

### 📌 Key Takeaways
- Use **implicit join tables** when you don’t need extra columns.  
- Use **explicit join entities** when you need metadata (like `EnrollmentDate`).  
- EF Core automatically handles composite keys and cascade deletes.  

---
#  9 At the start of the program, the `users` collection has **112 rows** with `Id > 100`.  
What will be the output of the following code?

```csharp
var data = (from k in users
            where k.Id > 100
            select k);

var coll = (from k in users
            where k.Id > 100
            select k).ToList(); // 112 records

users = users.Add(new User(){Id = 158});

int dataCount = data.Count();
int colCount = coll.Count();

print(dataCount);
print(colCount);
```

---

### ✅ Answer:

1. **`data`**  
   - This is a **deferred LINQ query** (`IEnumerable`).  
   - It is not executed until you call `.Count()`.  
   - When `data.Count()` runs, it re-executes the query on the updated `users` collection.  
   - Since a new user with `Id = 158` was added, the count becomes **113**.

2. **`coll`**  
   - This is a **materialized list** created by `.ToList()`.  
   - It was executed immediately at the time of definition (before adding the new user).  
   - So it contains only the original 112 records.  
   - `coll.Count()` remains **112**.

---

### 📌 Final Output:
```
113
112
```

---

👉 **Key takeaway:**  
- `IEnumerable` queries (`data`) are **deferred** and reflect changes in the source collection.  
- `.ToList()` (`coll`) is **immediate execution** and does not reflect later changes.  

---

