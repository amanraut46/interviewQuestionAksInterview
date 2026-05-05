# 1.Difference between Middleware and Action Filters in ASP.NET (Core or MVC) lies in their purpose, scope, and where they fit in the request pipeline

## 🔹 1. Middleware

### ✅ Definition

Middleware is **global logic** that runs early in the request pipeline—before routing, MVC, and filters. It's used to process or modify HTTP requests/responses.

### ✅ Characteristics

* Runs **before and after** the routing to controller/actions.
* Works on **all requests** (not just MVC).
* Applied in `Startup.cs` (`Program.cs` in .NET 6+).
* Can short-circuit the pipeline (e.g., return a response early).

### ✅ Common Use Cases

* Authentication
* Logging
* CORS
* Exception handling
* Custom headers

### ✅ Example

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task Invoke(HttpContext context)
    {
        // Do something before controller
        await _next(context);
        // Do something after controller
    }
}
```

---

## 🔹 2. **Action Filter**

### ✅ Definition

Action Filters are part of the **MVC pipeline** and are used to execute logic **before or after** a controller action executes.

### ✅ Characteristics

* Runs **within the MVC pipeline** (after routing).
* Can be applied **globally, to a controller, or to an action**.
* Only works on **MVC or Web API requests**.
* More granular than middleware.

### ✅ Types of Filters

* `ActionFilter`
* `AuthorizationFilter`
* `ExceptionFilter`
* `ResultFilter`

### ✅ Common Use Cases

* Logging input/output of actions
* Validation
* Auditing
* Modifying action parameters or result

### ✅ Example

```csharp
public class LogActionFilter : IActionFilter
{
    public void OnActionExecuting(ActionExecutingContext context)
    {
        // Code before action executes
    }

    public void OnActionExecuted(ActionExecutedContext context)
    {
        // Code after action executes
    }
}
```

---

## 🆚 **Middleware vs Action Filter – Quick Comparison**

| Feature               | Middleware                  | Action Filter                  |
| --------------------- | --------------------------- | ------------------------------ |
| Scope                 | Application-wide            | Controller/action-specific     |
| Executes              | Before/after routing        | Before/after controller action |
| Applies to            | All requests                | Only MVC/Web API               |
| Setup Location        | `Startup.cs` / `Program.cs` | Attribute or MVC configuration |
| Access to HttpContext | Yes                         | Yes                            |
| Use Cases             | Auth, logging, headers      | Logging, validation, caching   |

---

# **2.ASP.NET Core, dependency injection (DI) is built-in, and the three main service lifetimes you can register are:**

---

## 🔹 `AddSingleton`, `AddScoped`, and `AddTransient` – Key Differences

| Lifetime       | Created                         | Shared                               | Use Case                                              |
| -------------- | ------------------------------- | ------------------------------------ | ----------------------------------------------------- |
| `AddSingleton` | Once for the entire application | Shared across all requests and users | Expensive services that are thread-safe and stateless |
| `AddScoped`    | Once per **HTTP request**       | Shared within that single request    | Services that maintain request-level state            |
| `AddTransient` | Every time requested            | Never shared                         | Lightweight, stateless services                       |

---

### ✅ 1. `AddSingleton<TInterface, TImplementation>()`

* Creates a **single instance** for the **entire application lifetime**.
* All components that depend on this service **share the same instance**.
* **Good for**: Caching, configuration, stateless utilities.

**Example:**

```csharp
services.AddSingleton<IMyService, MyService>();
```

---

### ✅ 2. `AddScoped<TInterface, TImplementation>()`

* Creates a **new instance per HTTP request**.
* **Same instance** is reused within that request.
* **Good for**: Services that need to maintain state per request (like DB context).

**Example:**

```csharp
services.AddScoped<IMyService, MyService>();
```

---

### ✅ 3. `AddTransient<TInterface, TImplementation>()`

* Creates a **new instance every time** it’s requested.
* **Never shared** — even within the same request.
* **Good for**: Lightweight, stateless services, or short-lived objects.

**Example:**

```csharp
services.AddTransient<IMyService, MyService>();
```

---

## 🧠 Example Scenario

Suppose you inject the same service into both a controller and a repository:

* **Singleton** → Both will get **same object**, even across requests.
* **Scoped** → Both will get **same object in same request**, but different between requests.
* **Transient** → Both will get **different object** every time it's injected.

---

## ⚠️ Important Notes

* Never use **`AddSingleton` for services that depend on `Scoped` services** (like `DbContext`) — this can cause serious threading issues.
* Use **`AddScoped` for Entity Framework DbContext** or anything request-bound.

---

Absolutely! Here's a breakdown of **use cases** for each service lifetime in **ASP.NET Core Dependency Injection**:

---

## 🔹 1. `AddSingleton` – ✅ **Use Cases**

### 🟢 Characteristics

* Created **once** and reused for the **entire app lifetime**.
* **Shared across all requests** and users.
* Best for **thread-safe** and **stateless** objects.

### ✅ Use Cases

| Use Case                  | Why                                                |
| ------------------------- | -------------------------------------------------- |
| **Configuration readers** | Load once and use everywhere.                      |
| **Logging services**      | Can be reused across components.                   |
| **Caching services**      | In-memory caches are usually singleton.            |
| **Static data providers** | Like country/state lists.                          |
| **API client wrappers**   | e.g., `HttpClientFactory` registered as singleton. |

---

## 🔹 2. `AddScoped` – ✅ **Use Cases**

### 🟡 Characteristics:

* Created **once per HTTP request**.
* Same instance used **throughout the request lifecycle**.

### ✅ Use Cases:

| Use Case                                          | Why                                                      |
| ------------------------------------------------- | -------------------------------------------------------- |
| **Database context (`DbContext`)**                | Ensures consistent unit of work per request.             |
| **Repository services**                           | Shared within a single web request.                      |
| **Unit of work patterns**                         | Keep operations grouped within a request.                |
| **Business services** that maintain request state | Useful for aggregating data or caching during a request. |

---

## 🔹 3. `AddTransient` – ✅ **Use Cases**

### 🔵 Characteristics:

* **New instance every time** it's requested.
* Ideal for **lightweight, stateless** services.

### ✅ Use Cases:

| Use Case                                                  | Why                                              |
| --------------------------------------------------------- | ------------------------------------------------ |
| **Email or SMS sender service**                           | Usually short-lived, no internal state.          |
| **Input validators**                                      | Created, used, and discarded quickly.            |
| **Helper utilities** (e.g., date converters, calculators) | No shared state needed.                          |
| **Logging decorators**                                    | When per-operation instance isolation is needed. |

---

## 🧠 Summary Table

| Lifetime    | Use For                                    | Avoid For                         |
| ----------- | ------------------------------------------ | --------------------------------- |
| `Singleton` | Caching, Logging, Configs, Utilities       | Services depending on Scoped ones |
| `Scoped`    | EF `DbContext`, Repositories, Unit of Work | Long-lived objects or static data |
| `Transient` | Validators, lightweight utilities          | Heavy objects or shared services  |

---

# 3 How security API?

To help you better, here are a few interpretations of your question:

### 1. **How to Secure an API?**

If you're asking **how to secure your API**, here are common methods:

#### 🔐 **API Security Techniques**:

* **Authentication & Authorization**:

  * OAuth2 with tokens (e.g., JWT)
  * API Key (less secure)
  * Azure Active Directory (for enterprise apps)
* **HTTPS Only** – Enforce TLS to protect data in transit.
* **Rate Limiting & Throttling** – To prevent abuse.
* **Input Validation** – Prevent SQL Injection, XSS, etc.
* **CORS Configuration** – Allow only trusted domains.
* **IP Whitelisting** – Limit access to known IPs.
* **Use Gateway/Firewall** – Like Azure API Management or AWS API Gateway.
* **Logging & Monitoring** – Track suspicious behavior.

---

### 2. **How Does a Security API Work?**

If you're asking **how a Security API works** (like Microsoft Graph Security API or any third-party API), it typically:

* Provides alerts, secure score, identity protection info.
* Requires secure access via OAuth2.
* Integrates with SIEM tools or security dashboards.

---

### 3. **Examples Based on Technology**:

#### ✅ **.NET Core Web API Security Example with JWT**:

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options => {
        options.TokenValidationParameters = new TokenValidationParameters {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,
            ValidIssuer = "your-issuer",
            ValidAudience = "your-audience",
            IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("your-secret-key"))
        };
    });
```

#### ✅ **Azure AD Authentication for API**:

Configure your API in `appsettings.json`:

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "TenantId": "your-tenant-id",
  "ClientId": "your-api-client-id",
  "Audience": "api://your-api-client-id"
}
```

And use `[Authorize]` on controllers.

---

# 4 **How to optimize an API**:

---

## ✅ 1. **Optimize Response Time**

### a. **Use Efficient Data Structures**

* Use pagination for large data sets (`/api/products?page=1&pageSize=50`)
* Avoid sending unnecessary fields (use DTOs)

### b. **Compress Responses**

* Enable **GZIP** or **Brotli** compression (e.g., in ASP.NET Core):

```csharp
services.AddResponseCompression();
```

### c. **Caching**

* Use response caching for read-heavy endpoints

  * MemoryCache, Redis, or HTTP Cache Headers
* E.g., In ASP.NET Core:

```csharp
[ResponseCache(Duration = 60)]
public IActionResult Get() => Ok(data);
```

---

## ✅ 2. **Reduce Database Load**

### a. **Use Query Optimization**

* Use `SELECT` with only needed columns
* Add **indexes** on frequently queried fields
* Avoid `N+1` queries (use `Include()` in EF Core)

### b. **Use Stored Procedures or Raw SQL**

* For complex operations or bulk processing

### c. **Connection Pooling**

* Ensure you're reusing DB connections

---

## ✅ 3. **Asynchronous Programming**

Use `async`/`await` in .NET or `Promise` in Node.js to prevent thread blocking:

```csharp
public async Task<IActionResult> GetUsers() => Ok(await _db.Users.ToListAsync());
```

---

## ✅ 4. **Rate Limiting & Throttling**

Prevent abuse and optimize traffic:

* ASP.NET Core: Use **AspNetCoreRateLimit** library
* Azure API Management: Built-in policies

---

## ✅ 5. **Use Content Delivery Networks (CDNs)**

* For static content or large file APIs (images, videos)
* Reduces load on your backend servers

---

## ✅ 6. **Use Logging & Monitoring**

* Use Application Insights, Serilog, or ELK Stack to identify slow APIs
* Monitor:

  * Response time
  * Exceptions
  * CPU/memory usage

---

## ✅ 7. **Enable HTTP/2 or gRPC**

* Faster, smaller overhead
* Especially useful for microservices communication

---

## ✅ 8. **Use Dependency Injection Properly**

* Register services as Singleton, Scoped, or Transient wisely to reduce overhead

---

## ✅ 9. **Use CDN or Caching for Static APIs**

For APIs that return constant data (like country list, config), serve them via CDN or cache.

---

## ✅ 10. **Security Overhead Optimization**

* Avoid excessive security checks in non-sensitive endpoints
* Use lightweight JWT validation strategies

---

## Tools for API Performance Testing

* **Postman** (for manual testing)
* **Apache JMeter** (load testing)
* **k6** (modern performance testing tool)
* **Azure Application Insights** / **New Relic**

---

If you tell me:

* What language or framework you are using (e.g., ASP.NET Core, Node.js)
* What kind of API (REST, GraphQL, etc.)
* Where it's hosted (e.g., Azure, AWS, on-prem)

---

# 5  What is SOLID?

| Letter | Principle Name                            | Description                                                                               |
| ------ | ----------------------------------------- | ----------------------------------------------------------------------------------------- |
| **S**  | **Single Responsibility Principle (SRP)** | A class should have **only one reason to change**.                                        |
| **O**  | **Open/Closed Principle (OCP)**           | Software should be **open for extension**, but **closed for modification**.               |
| **L**  | **Liskov Substitution Principle (LSP)**   | Subtypes should be **substitutable for their base types** without breaking functionality. |
| **I**  | **Interface Segregation Principle (ISP)** | Don’t force a class to implement interfaces it doesn’t use.                               |
| **D**  | **Dependency Inversion Principle (DIP)**  | Depend on **abstractions**, not on **concrete classes**.                                  |

---

## 💡 SOLID Example in C# (.NET)

Let’s create a small example around an **Order Processing System**:

---

### ✅ **1. Single Responsibility Principle**

```csharp
public class InvoiceService {
    public void GenerateInvoice(Order order) {
        // Logic to generate invoice
    }
}

public class EmailService {
    public void SendEmail(string to, string message) {
        // Logic to send email
    }
}
```

> Each class has only **one reason to change**.

---

### ✅ **2. Open/Closed Principle**

```csharp
public abstract class Discount {
    public abstract decimal ApplyDiscount(decimal total);
}

public class NoDiscount : Discount {
    public override decimal ApplyDiscount(decimal total) => total;
}

public class PercentageDiscount : Discount {
    public override decimal ApplyDiscount(decimal total) => total * 0.9M;
}
```

> You can **extend** the behavior (new discounts) without modifying existing code.

---

### ✅ **3. Liskov Substitution Principle**

```csharp
public class Bird {
    public virtual void Fly() => Console.WriteLine("Flying");
}

public class Sparrow : Bird {}

public class Ostrich : Bird {
    public override void Fly() => throw new NotSupportedException("Ostrich can't fly");
}
```

❌ **Ostrich violates LSP**. A solution is to redesign with interfaces:

```csharp
public interface IBird {}
public interface IFlyingBird : IBird {
    void Fly();
}
```

---

### ✅ **4. Interface Segregation Principle**

```csharp
public interface IPrinter {
    void Print();
}

public interface IScanner {
    void Scan();
}

public class AllInOnePrinter : IPrinter, IScanner {
    public void Print() { }
    public void Scan() { }
}

public class SimplePrinter : IPrinter {
    public void Print() { }
}
```

> No need for `SimplePrinter` to implement `IScanner`.

---

### ✅ **5. Dependency Inversion Principle**

```csharp
public interface IMessageSender {
    void Send(string message);
}

public class EmailSender : IMessageSender {
    public void Send(string message) {
        Console.WriteLine($"Sending Email: {message}");
    }
}

public class NotificationService {
    private readonly IMessageSender _sender;

    public NotificationService(IMessageSender sender) {
        _sender = sender;
    }

    public void Notify(string message) {
        _sender.Send(message);
    }
}
```

> `NotificationService` depends on **abstraction**, not the concrete `EmailSender`.

---

## ⚙️ Summary

| Principle | Benefit                                 |
| --------- | --------------------------------------- |
| SRP       | Easier to maintain                      |
| OCP       | Easier to extend features               |
| LSP       | Ensures code reliability                |
| ISP       | Avoids unnecessary dependencies         |
| DIP       | Easier to test and swap implementations |

---

Would you like a **full working C# project** that demonstrates all SOLID principles in a simple API?

# 6 Write the repeting charactor string fullname repeated number
```csharp
// Online C# Editor for free
// Write, Edit and Run your C# code using C# Online Compiler
//string fullname repeated number
using System;
using System.Collections.Generic;


public class HelloWorld
{
    public static void Main(string[] args)
    {
        string name= "aman raut";
        Dictionary<char,int> dict= new();
        foreach(char c in name)
        {
            if (dict.ContainsKey(c))
            {
                dict[c]+=1;
            }
            else
            {
              dict[c]=1;
            }
        }
        
        foreach(KeyValuePair<char, int> item in dict)
        {
            if(item.Value>1)
            {
                Console.WriteLine(item.Key);
            }
        }
    }
}
```
To remove duplicates **and sort the result in descending order**, you can use **LINQ** like this:

---

# 7  Full Example: Remove Duplicates & Sort in Descending Order

```csharp
using System;
using System.Linq;

class Program
{
    static void Main()
    {
        int[] arr = new int[] { 4, 5, 2, 8, 5, 2, 9 };

        int[] result = arr
            .Distinct()            // Remove duplicates
            .OrderByDescending(x => x)  // Sort in descending order
            .ToArray();

        Console.WriteLine("Array without duplicates in descending order:");
        foreach (int num in result)
        {
            Console.Write(num + " ");
        }
    }
}
```

---

### 🧾 Output:

```
Array without duplicates in descending order:
9 8 5 4 2
```

Sure! Here's a **single complete example** of a **custom logging middleware** in ASP.NET Core:

---

# 8 Example: Logging Middleware (Request + Response Logging)

### 🔹 `MyLoggingMiddleware.cs`

```csharp
public class MyLoggingMiddleware
{
    private readonly RequestDelegate _next;

    public MyLoggingMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        // Log request
        Console.WriteLine($"[Request] {context.Request.Method} {context.Request.Path}");

        await _next(context); // Call the next middleware

        // Log response
        Console.WriteLine($"[Response] {context.Response.StatusCode}");
    }
}
```

---

### 🔹 `MyLoggingMiddlewareExtensions.cs`

```csharp
public static class MyLoggingMiddlewareExtensions
{
    public static IApplicationBuilder UseMyLoggingMiddleware(this IApplicationBuilder builder)
    {
        return builder.UseMiddleware<MyLoggingMiddleware>();
    }
}
```

---

### 🔹 `Program.cs` (ASP.NET Core 6+)

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

// Register custom middleware
app.UseMyLoggingMiddleware();

app.MapGet("/", () => "Hello from the home route!");
app.MapGet("/test", () => "Test route works!");

app.Run();
```

---

### 🧾 Output (Console)

```
[Request] GET /
[Response] 200

[Request] GET /test
[Response] 200
```

---

# 10 difference between `FirstOrDefault()` and `SingleOrDefault()` in C#:

---

### 🔹 `FirstOrDefault()`

* **Returns:** The **first element** in a sequence, or `default(T)` if the sequence is empty.
* **Throws Exception?** ❌ No — it never throws, even if multiple elements exist.
* **Use When:** You expect **0 or more elements** and want the **first one** if available.

```csharp
var list = new List<int> { 1, 2, 3 };
var result = list.FirstOrDefault(); // returns 1
```

---

### 🔹 `SingleOrDefault()`

* **Returns:** The **only element** in a sequence, or `default(T)` if the sequence is empty.
* **Throws Exception?** ✅ Yes — if there is **more than one element**, it throws `InvalidOperationException`.
* **Use When:** You expect **0 or 1 element only**, and having more is **an error**.

```csharp
var list = new List<int> { 1 };
var result = list.SingleOrDefault(); // returns 1

var badList = new List<int> { 1, 2 };
var result2 = badList.SingleOrDefault(); // ❌ throws exception
```

---

### ✅ Summary Table

| Feature            | `FirstOrDefault()`      | `SingleOrDefault()`            |
| ------------------ | ----------------------- | ------------------------------ |
| Returns            | First item or default   | Single item or default         |
| Throws if multiple | ❌ No                    | ✅ Yes                          |
| Safe with empty    | ✅ Yes (returns default) | ✅ Yes (returns default)        |
| Use case           | Get first of many       | Ensure only one or none exists |

---

Sure! Here's the complete format with the **interview-style question**, **answer**, **code examples**, and **comparison table**:

---

# 11 **What is the difference between a `private` class and a `sealed` class in C#?**

---

### ✅ **Answer:**

In C#, `private` and `sealed` are two different keywords used for different purposes:

* **`private`** is an **access modifier** that controls **visibility**.
* **`sealed`** is an **inheritance modifier** that controls **inheritability**.

---

### 🔍 **Explanation with Examples**

#### 🔒 `private class`

* A `private` class **cannot be accessed outside the containing class**.
* It must be a **nested class** (you can't declare a private top-level class).
* Useful for **encapsulation**.

```csharp
public class OuterClass
{
    private class InnerClass
    {
        public void Display() => Console.WriteLine("Inside InnerClass");
    }

    public void AccessInner()
    {
        InnerClass obj = new InnerClass(); // ✅ OK: accessible inside OuterClass
        obj.Display();
    }
}

// ❌ This will NOT compile:
// OuterClass.InnerClass obj = new OuterClass.InnerClass();
```

---

#### 🔐 `sealed class`

* A `sealed` class **cannot be inherited**.
* Used when you want to **prevent other classes from deriving** from it.
* Commonly used with classes like `System.String`, `System.Math`, etc.

```csharp
public sealed class SealedClass
{
    public void Display() => Console.WriteLine("This is a sealed class");
}

// ❌ This will cause a compile-time error:
public class DerivedClass : SealedClass
{
    // Error: cannot derive from sealed class
}
```

---

### 📊 **Comparison Table**

| Feature           | `private class`                     | `sealed class`                                 |
| ----------------- | ----------------------------------- | ---------------------------------------------- |
| Keyword type      | Access modifier                     | Inheritance modifier                           |
| Can be top-level? | ❌ No (must be nested)               | ✅ Yes                                          |
| Accessibility     | Only inside the containing class    | Based on its access modifier (public/internal) |
| Can be inherited? | ✅ Yes, if accessible                | ❌ No                                           |
| Used for          | Hiding inner implementation details | Preventing inheritance                         |

---

# 12 **What is the difference between `GROUP BY` and `PARTITION BY` in SQL
### ✅ **Answer:**

Both `GROUP BY` and `PARTITION BY` are used to **organize data**, but they serve different purposes:

* **`GROUP BY`** is used to **aggregate rows into groups**, reducing the number of rows.
* **`PARTITION BY`** is used with **window functions** to divide the result set into partitions **without reducing rows**.

---

### 🔍 **Detailed Explanation**

#### 🔸 `GROUP BY`

* **Groups** rows with the same values in specified columns.
* Often used with aggregate functions like `SUM()`, `COUNT()`, `AVG()`, etc.
* **Reduces** the number of rows in the result.

**Example:**

```sql
SELECT Department, COUNT(*) AS EmployeeCount
FROM Employees
GROUP BY Department;
```

🧠 **Result**: One row per department with the number of employees.

---

#### 🔹 `PARTITION BY`

* Used with **window functions** like `ROW_NUMBER()`, `RANK()`, `SUM() OVER(...)`, etc.
* It **divides** the result set into partitions (like mini-groups), but **does not reduce** the number of rows.
* Each row retains its identity while calculations are performed over its partition.

**Example:**

```sql
SELECT Name, Department,
       RANK() OVER (PARTITION BY Department ORDER BY Salary DESC) AS DeptRank
FROM Employees;
```

🧠 **Result**: Each employee is listed, with their rank **within their department**.

---

### 📊 **Comparison Table**

| Feature         | `GROUP BY`                             | `PARTITION BY`                                       |
| --------------- | -------------------------------------- | ---------------------------------------------------- |
| Purpose         | Aggregate rows into groups             | Divide data into partitions for window functions     |
| Reduces rows?   | ✅ Yes                                  | ❌ No                                                 |
| Used with       | Aggregate functions (SUM, COUNT, etc.) | Window functions (RANK, ROW\_NUMBER, SUM OVER, etc.) |
| Output rows     | One row per group                      | Same number of rows as input                         |
| Clause location | In the main `SELECT` query             | Inside `OVER()` clause                               |

---

### 📝 Summary:

* Use **`GROUP BY`** when you want **summary data**.
* Use **`PARTITION BY`** when you want **row-level calculations grouped logically**.

# 13 **How do you perform a `LEFT JOIN` in LINQ, and what is its purpose?**

### ✅ **Answer:**

A **LEFT JOIN** in LINQ is used when you want to return **all records from the left collection**, even if there are **no matching records in the right collection**. It is implemented using **`join ... into`**, followed by **`DefaultIfEmpty()`**.

---

### 🧾 **Syntax (Method Syntax):**

```csharp
var result = from a in collectionA
             join b in collectionB on a.Id equals b.AId into gj
             from subB in gj.DefaultIfEmpty()
             select new
             {
                 AName = a.Name,
                 BName = subB != null ? subB.Name : "No Match"
             };
```

---

### 🛠️ **Example**

#### 📄 Input:

```csharp
public class Student
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Enrollment
{
    public int StudentId { get; set; }
    public string Course { get; set; }
}

var students = new List<Student>
{
    new Student { Id = 1, Name = "Alice" },
    new Student { Id = 2, Name = "Bob" },
    new Student { Id = 3, Name = "Charlie" }
};

var enrollments = new List<Enrollment>
{
    new Enrollment { StudentId = 1, Course = "Math" },
    new Enrollment { StudentId = 1, Course = "Science" },
    new Enrollment { StudentId = 2, Course = "History" }
};
```

#### ⚙️ LINQ LEFT JOIN:

```csharp
var query = from s in students
            join e in enrollments on s.Id equals e.StudentId into studentEnrollments
            from se in studentEnrollments.DefaultIfEmpty()
            select new
            {
                StudentName = s.Name,
                Course = se != null ? se.Course : "Not Enrolled"
            };
```

#### 🖨️ Output:

```
Alice    - Math  
Alice    - Science  
Bob      - History  
Charlie  - Not Enrolled
```

---

### 🧠 **Why use `LEFT JOIN` in LINQ?**

* To show unmatched items (e.g., students without enrollments).
* To replicate SQL-style reporting and summary.
* To avoid missing "left-side" data due to inner joins.
---
# 14 **What is the lifecycle of a .NET application?**

### ✅ **Answer:**

The **.NET application lifecycle** includes all the stages from writing the code to executing it and cleaning up resources when the application terminates. This lifecycle is managed by the **.NET runtime**, also known as the **Common Language Runtime (CLR)**.

---

### 🔄 **.NET Application Lifecycle – Step by Step**

#### 1. 📝 **Code Writing**

* Developer writes code in C#, F#, or VB.NET.

#### 2. ✅ **Compilation**

* Source code is compiled using the **C# compiler (`csc.exe`)** into **Intermediate Language (IL)** code and **metadata**.
* The result is a **.dll** or **.exe** file (called an **assembly**).

```plaintext
C# Code → Compiler → IL + Metadata → Assembly
```

---

#### 3. 📦 **Assembly Loading**

* At runtime, the **CLR** loads the assembly and its dependencies.
* CLR uses the metadata to understand types, methods, and references.

---

#### 4. ⚙️ **JIT Compilation (Just-In-Time)**

* The IL code is compiled to **native machine code** by the **JIT compiler**.
* This happens **on demand**, method by method.

---

#### 5. 🏃 **Execution**

* The native code runs under the supervision of the **CLR**, which manages:

  * **Memory**
  * **Threading**
  * **Security**
  * **Exception handling**

---

#### 6. 🧹 **Garbage Collection**

* CLR uses the **Garbage Collector** to automatically reclaim memory from unused objects.
* This reduces memory leaks and improves performance.

---

#### 7. ❌ **Application Exit**

* When the application ends:

  * Finalizers (destructors) run for remaining objects.
  * App domain is unloaded.
  * Resources are released.

---

### 📊 **Summary Diagram**

```
Source Code (.cs)
    ↓
C# Compiler (csc.exe)
    ↓
IL Code (.dll / .exe)
    ↓
CLR Loads Assembly
    ↓
JIT Compiler → Native Code
    ↓
Runtime Execution
    ↓
Garbage Collection
    ↓
Application Exit
```

---

### 🌐 **Bonus: ASP.NET Web Application Lifecycle**

If you're talking about an **ASP.NET or ASP.NET Core** web app, the lifecycle also includes:

| Stage                | Description                                       |
| -------------------- | ------------------------------------------------- |
| Application Start    | Configuration loaded; middleware pipeline set up. |
| Request Start        | Incoming HTTP request received.                   |
| Routing              | Request matched to endpoint/controller.           |
| Middleware Execution | Runs middleware components (auth, logging, etc.). |
| Controller/Action    | Controller and action method invoked.             |
| Result Execution     | View rendered or JSON returned.                   |
| Response Sent        | Response goes back to client.                     |
| Application End      | Application shutdown or restart triggers cleanup. |

---

### 🧠 **Key Components Involved**

* **CLR (Common Language Runtime)** – Runtime engine for .NET.
* **JIT (Just-In-Time compiler)** – Converts IL to machine code.
* **GC (Garbage Collector)** – Handles automatic memory management.
* **Assemblies** – Output files containing IL code and metadata.

---

Great question! Let's break down the difference between **Concurrency** and **Parallelism**, especially in the context of C# and real-world applications.

---
# 14 **What is the difference between concurrency and parallelism?**


### ✅ **Answer:**

| Concept         | Description                                                                                                        |
| --------------- | ------------------------------------------------------------------------------------------------------------------ |
| **Concurrency** | Deals with **managing multiple tasks at the same time**. Tasks may not run simultaneously but are **interleaved**. |
| **Parallelism** | Involves running **multiple tasks simultaneously** on **multiple cores/threads**.                                  |

---

### 🧠 **Simplified Definitions**

* **Concurrency** = multiple tasks **progressing together**, possibly switching back and forth.
* **Parallelism** = multiple tasks **executing at the same time**, literally in parallel.

---

### 🛠️ **Real-Life Analogy**

| Example         | Explanation                                                                      |
| --------------- | -------------------------------------------------------------------------------- |
| **Concurrency** | One chef cooking multiple dishes by switching between them (not simultaneously). |
| **Parallelism** | Multiple chefs each cooking one dish at the same time.                           |

---

### 💻 **In C# Terms**

#### 🔸 **Concurrency in C#**

* Uses **`async`/`await`**, **Tasks**, or **Threads**.
* Example: handling multiple web requests in an ASP.NET Core app.

```csharp
public async Task HandleRequestAsync()
{
    var data1 = await GetDataFromApi1();
    var data2 = await GetDataFromApi2();
    Console.WriteLine("Both requests processed.");
}
```

* Tasks run **logically in parallel**, but may share the same thread (via I/O waits).

---

#### 🔹 **Parallelism in C#**

* Uses **`Parallel.For`, `Parallel.ForEach`**, or **Task Parallel Library (TPL)**.
* Example: processing large data in parallel.

```csharp
Parallel.For(0, 10, i =>
{
    Console.WriteLine($"Processing item {i} on thread {Thread.CurrentThread.ManagedThreadId}");
});
```

* Each iteration may run **on a different core** at the **same time**.

---

### 📊 **Comparison Table**

| Feature          | Concurrency                           | Parallelism                           |
| ---------------- | ------------------------------------- | ------------------------------------- |
| Definition       | Multiple tasks progressing together   | Multiple tasks running simultaneously |
| Thread Usage     | May use a single thread or multiple   | Requires multiple threads/cores       |
| Goal             | Improve responsiveness                | Improve performance                   |
| Example Use Case | Web server handling multiple requests | Image processing, CPU-intensive tasks |

---

### ⚠️ Common Confusion

* Concurrency **can happen on a single core** (via task switching).
* Parallelism **requires multiple cores or processors**.

---

Let me know if you'd like:

* Diagrams of task switching vs parallel execution
* C# code to demo both side-by-side
* Interview scenarios or questions on async/await vs threads

Great question! Let's break it down clearly:

---
# 15 **What is a deadlock and when does it occur?**
## ✅ **Definition:**
A **deadlock** is a situation where **two or more threads/processes are waiting for each other to release resources**, and **none of them ever proceed**.

It’s like two people holding keys to each other’s lockers — and both are waiting for the other to open theirs first.

---

## 🧠 **When Does Deadlock Occur?**

Deadlock typically occurs when **four conditions** are met **simultaneously**:

| Condition            | Description                                                                       |
| -------------------- | --------------------------------------------------------------------------------- |
| **Mutual Exclusion** | At least one resource is held in a non-shareable mode.                            |
| **Hold and Wait**    | A process/thread holds one resource and waits for another.                        |
| **No Preemption**    | Resources cannot be forcibly taken from a process.                                |
| **Circular Wait**    | A circular chain of threads exists, each waiting for a resource held by the next. |

---

## 🔧 **Example in C# (with locks):**

```csharp
object lock1 = new object();
object lock2 = new object();

Thread thread1 = new Thread(() =>
{
    lock (lock1)
    {
        Thread.Sleep(100); // Simulate work
        lock (lock2)
        {
            Console.WriteLine("Thread 1 acquired both locks");
        }
    }
});

Thread thread2 = new Thread(() =>
{
    lock (lock2)
    {
        Thread.Sleep(100); // Simulate work
        lock (lock1)
        {
            Console.WriteLine("Thread 2 acquired both locks");
        }
    }
});

thread1.Start();
thread2.Start();
```

### ❌ What happens:

* Thread 1 locks `lock1` and waits for `lock2`
* Thread 2 locks `lock2` and waits for `lock1`
* **Deadlock!** Neither can proceed.

---

## 🔍 **How to Prevent Deadlocks**

1. ✅ **Always lock resources in the same order**
2. ❌ **Avoid holding multiple locks at once**
3. ✅ **Use timeouts when acquiring locks**
4. ✅ **Use lock-free or concurrent collections when possible**
5. ✅ **Detect circular waits if possible**

---

## 📌 **Real-World Deadlock Example**

In a **database (like SQL Server)**:

* Two transactions each lock a different row
* Then each tries to update the row locked by the other
* Deadlock occurs → one transaction is rolled back automatically

---

### ✅ Summary:

| Term            | Meaning                                                         |
| --------------- | --------------------------------------------------------------- |
| Deadlock        | Two or more threads/processes **wait forever** on each other    |
| When it happens | Holding resources **while waiting for others**                  |
| Prevention      | Lock ordering, timeouts, minimal locking, concurrent structures |

---
# 16 How would you find the 4th largest `SellValue` from a collection using LINQ query syntax in C#?**
---
### 🔸 **Answer:**

```csharp
var fourthLargest = (
    from s in sales
    orderby s.SellValue descending
    select s.SellValue
)
.Distinct()
.Skip(3)
.FirstOrDefault();

Console.WriteLine($"4th largest sell value is: {fourthLargest}");
```

---

### ✅ **Explanation:**

* `orderby s.SellValue descending`: Sorts SellValues in descending order.
* `Distinct()`: Ensures uniqueness (optional, depending on requirement).
* `Skip(3)`: Skips the top 3 values.
* `FirstOrDefault()`: Returns the 4th largest (or 0/default if not found).

---

# 17 How do you sort an array in ascending and descending order in C#?

Great! Here's a complete C# program that sorts an array in both **ascending** and **descending** order using the **Bubble Sort algorithm** (without using LINQ).

---
**Q: Write a C# program to sort an array in ascending and descending order using the Bubble Sort algorithm.**
---
### 🔸 **C# Program Using Bubble Sort:**

```csharp
using System;

class Program
{
    static void Main()
    {
        int[] numbers = { 5, 3, 8, 1, 9, 2 };

        Console.WriteLine("Original Array: " + string.Join(", ", numbers));

        // Sort Ascending
        int[] ascending = (int[])numbers.Clone();
        BubbleSortAscending(ascending);
        Console.WriteLine("Ascending Order: " + string.Join(", ", ascending));

        // Sort Descending
        int[] descending = (int[])numbers.Clone();
        BubbleSortDescending(descending);
        Console.WriteLine("Descending Order: " + string.Join(", ", descending));
    }

    static void BubbleSortAscending(int[] arr)
    {
        int n = arr.Length;
        for (int i = 0; i < n - 1; i++)
        {
            for (int j = 0; j < n - i - 1; j++)
            {
                if (arr[j] > arr[j + 1])
                {
                    // swap
                    int temp = arr[j];
                    arr[j] = arr[j + 1];
                    arr[j + 1] = temp;
                }
            }
        }
    }

    static void BubbleSortDescending(int[] arr)
    {
        int n = arr.Length;
        for (int i = 0; i < n - 1; i++)
        {
            for (int j = 0; j < n - i - 1; j++)
            {
                if (arr[j] < arr[j + 1])
                {
                    // swap
                    int temp = arr[j];
                    arr[j] = arr[j + 1];
                    arr[j + 1] = temp;
                }
            }
        }
    }
}
```

---

### 🟦 **Output:**

```
Original Array: 5, 3, 8, 1, 9, 2
Ascending Order: 1, 2, 3, 5, 8, 9
Descending Order: 9, 8, 5, 3, 2, 1
```

---

# 18 🔍 IQueryable vs IEnumerable vs List vs IList — Clearing the Confusion in .NET 💡

As .NET developers, we often use collections and queries daily — but knowing when to use each makes a big difference in performance and maintainability.

Here’s a quick breakdown 👇

📌 IEnumerable
 • Iterates over a collection in-memory.
 • Best when working with in-memory data (like arrays or lists).
 • Executes immediately.

📌 IQueryable
 • Builds queries that are translated to the data source (like SQL in EF Core).
 • Enables deferred execution and server-side filtering.
 • Best for large datasets where you don’t want to load everything into memory.

📌 List
 • A concrete implementation of IList<T> and IEnumerable<T>.
 • Provides fast indexing and manipulation of items.
 • Great for when you need a dynamic array-like structure.

📌 IList
 • An interface representing a collection of objects that can be accessed by index.
 • More flexible than List, since you can implement your own custom collections.
 • Often used when you want abstraction over the actual collection type.

⚡ Key Takeaway:
 • Use IEnumerable for in-memory iteration.
 • Use IQueryable for database queries.
 • Use List for general-purpose dynamic collections.
 • Use IList when you want flexibility or abstraction.
---

# 19. **What is the difference between `Task.Result` and `await` in C#?**

 Expected Answer:

* `Task.Result` is **blocking**, `await` is **non-blocking**.
* `Result` wraps exceptions in `AggregateException`, while `await` unwraps and throws them directly.
* `Result` can cause **deadlocks**, especially in UI/ASP.NET contexts.
* `await` is the **preferred approach** for async programming.

---

# 20. **What happens if you use `Task.Result` in an ASP.NET or WPF application?**

 Expected Answer:

* It may cause a **deadlock** because the synchronization context waits for the task to complete, while the task is waiting for the synchronization context to free up.
* Using `await` avoids this issue since it allows the thread to continue execution without blocking.

---

# 21. **How are exceptions handled differently between `Task.Result` and `await`?**

 Expected Answer:

* With `.Result`, exceptions are **wrapped inside `AggregateException`**.
* With `await`, exceptions are **unwrapped** and rethrown directly.
* Example:

  ```csharp
  try
  {
      var result = task.Result; // AggregateException
  }
  catch (AggregateException ex) { ... }

  try
  {
      var result = await task; // Original exception
  }
  catch (Exception ex) { ... }
  ```

---

# 22. **When would you use `Task.Result` instead of `await`?**

 Expected Answer:

* Rarely, but sometimes in a **synchronous context** (like `Main` in a console app before `async Main` was introduced in C# 7.1).
* Or when you really want to **block** execution intentionally.

---

# 23. **Can using `Task.Result` degrade performance? Why?**

 Expected Answer:

* Yes, because it blocks a thread, preventing it from doing other work.
* This reduces scalability in web applications where threads are a limited resource.
* `await` is more **efficient** as it frees the thread while waiting.

---

# 24 🔹 What is a Tuple in C#?

* A **Tuple** is a **data structure** that can hold a fixed number of items of **different types**.
* It was introduced in **.NET Framework 4.0**.
* Think of it like a lightweight object to group multiple values together **without creating a separate class**.

Example:

```csharp
var tuple = Tuple.Create(1, "Aman", true);
Console.WriteLine(tuple.Item1); // 1
Console.WriteLine(tuple.Item2); // Aman
Console.WriteLine(tuple.Item3); // True
```

---

## 🔹 ValueTuple (C# 7.0 and above)

* In C# 7.0, **ValueTuple** was introduced.
* It’s more efficient than `Tuple<>` (stored as **structs** instead of classes).
* Allows **named fields** for better readability.

Example:

```csharp
(string Name, int Age, bool IsActive) person = ("Aman", 27, true);
Console.WriteLine(person.Name);  // Aman
Console.WriteLine(person.Age);   // 27
Console.WriteLine(person.IsActive); // True
```

You can also use **tuple deconstruction**:

```csharp
var (name, age, isActive) = ("Aman", 27, true);
Console.WriteLine(name);  // Aman
```

---

## 🔹 Use of Tuples

1. **Return multiple values** from a method without creating a class.

   ```csharp
   (int Sum, int Product) Calculate(int a, int b)
   {
       return (a + b, a * b);
   }

   var result = Calculate(5, 10);
   Console.WriteLine(result.Sum);     // 15
   Console.WriteLine(result.Product); // 50
   ```

2. **Temporary grouping of data** (like key-value pairs but with more than 2 values).

3. **Quick prototyping** when you don’t want to create separate DTOs or classes.

4. **Pattern matching & deconstruction** for cleaner code.

---

# 25. **What is a Tuple in C#?**

 Answer:
A Tuple is a data structure that can hold multiple values of different types in a single object.

---

# 26. **What is the difference between `Tuple` and `ValueTuple`?**

 Answer:

* `Tuple` is a **class** (reference type), introduced in .NET 4.0.
* `ValueTuple` is a **struct** (value type), introduced in C# 7.0.
* `ValueTuple` supports **naming fields** and **deconstruction**, `Tuple` does not.
* `ValueTuple` is more **efficient** because it avoids heap allocations.

---

# 27. **When should you use a Tuple?**

 Answer:

* When you need to return multiple values from a method.
* When creating a new class just to hold temporary values would be overkill.

---

# 28. **What are the limitations of Tuples?**

 Answer:

* Tuples are not very descriptive → `Item1`, `Item2` (unless using `ValueTuple`).
* Harder to understand in complex scenarios.
* Better to use **classes/records** when data has business meaning.

---

# 29. **Can tuples be used as dictionary keys?**

 Answer:

* Yes, since `Tuple` implements equality checks.
* `ValueTuple` also implements structural equality, so it can be used as dictionary keys.


# 30 🔹 Middleware in ASP.NET Core (`Program.cs`)

In **.NET 6**, the request pipeline is configured inside `Program.cs` using middleware.

Middleware methods:

1. **`Use`**
2. **`Run`**
3. **`Map`**
4. **`Next`** (middleware delegate to call the next middleware)

---

## 1. `Use`

* Adds a **middleware component** to the pipeline.
* You can do something **before and after** the next middleware by calling `await next()`.
* Used for **logging, authentication, custom headers, etc.**

```csharp
app.Use(async (context, next) =>
{
    Console.WriteLine("Before request");
    await next();  // calls the next middleware
    Console.WriteLine("After request");
});
```

---

## 2. `Run`

* Adds a **terminal middleware** → it handles the request and does **not call the next middleware**.
* Ends the pipeline.
* Used when you want to **short-circuit** the request.

```csharp
app.Run(async context =>
{
    await context.Response.WriteAsync("Hello from Run middleware!");
});
```

---

## 3. `Next`

* Available inside a `Use` middleware.
* It passes control to the **next middleware** in the pipeline.
* If you don’t call `next()`, the pipeline stops there.

```csharp
app.Use(async (context, next) =>
{
    Console.WriteLine("Request received");
    await next(); // continues to the next middleware
    Console.WriteLine("Response sent");
});
```

---

## 4. `Map`

* Branches the pipeline based on a **request path**.
* Useful for handling specific routes without using controllers.

```csharp
app.Map("/hello", builder =>
{
    builder.Run(async context =>
    {
        await context.Response.WriteAsync("Hello from /hello endpoint!");
    });
});
```

---

## 🔹 Middleware Execution Order Example

```csharp
app.Use(async (context, next) =>
{
    Console.WriteLine("Middleware 1 - Before");
    await next();
    Console.WriteLine("Middleware 1 - After");
});

app.Use(async (context, next) =>
{
    Console.WriteLine("Middleware 2 - Before");
    await next();
    Console.WriteLine("Middleware 2 - After");
});

app.Run(async context =>
{
    await context.Response.WriteAsync("Hello World!");
});
```

➡️ Execution order:

```
Middleware 1 - Before
Middleware 2 - Before
Hello World!
Middleware 2 - After
Middleware 1 - After
```

# 31. **What is middleware in ASP.NET Core?**
    A middleware is a component in the request pipeline that can handle requests and responses.

# 32. **What is the difference between `Use` and `Run` in middleware?**

   * `Use`: Can call `next()` to pass control to the next middleware.
   * `Run`: Terminal middleware; does not call the next one.

# 33. **When would you use `Map` in ASP.NET Core?**
    To branch the pipeline based on the request path (e.g., `/api`, `/admin`).

# 34. **What is the role of `next()` inside `Use` middleware?**
    It calls the next middleware. Without `next()`, the pipeline is short-circuited.

# 35. **Can we use multiple `Run` middlewares? Why or why not?**
    Only the first `Run` executes, because it terminates the pipeline. Additional `Run` middlewares won’t be reached.

# 36. **What happens if you don’t call `next()` inside `Use` middleware?**
    The request won’t move forward to the next middleware → pipeline stops there.

# 37. **How does `Map` differ from using `Use` with conditions (like checking `context.Request.Path`)?**
    `Map` provides a cleaner way to branch by path, instead of writing `if` conditions manually inside `Use`.

# 38 How can you send or handle large files using Azure Service Bus, given that Service Bus has message size limits?

---

### ✅ Answer

Azure Service Bus has a **message size limit** (256 KB in Basic, 1 MB in Standard, and up to 100 MB in Premium). Because of this, we cannot directly send large files through Service Bus.

The recommended approach is to use the **Claim Check Pattern**:

1. **Store the large file** in **Azure Blob Storage** (or another durable storage).
2. **Send only a reference** (like a **Blob URL** or **SAS token**) through Service Bus.
3. The **consumer** retrieves the reference, then downloads the actual file from Blob Storage.

This way, Service Bus is used only for **lightweight metadata & orchestration**, while the actual file transfer happens via Blob Storage.

---

### 🔹 Example Flow (Claim Check Pattern)

1. **Producer** uploads file → Blob Storage.
2. **Producer** sends a Service Bus message containing the **Blob SAS URL**.
3. **Consumer** reads the message → fetches the file from Blob Storage.

---

### 🔹 Alternative Approach (if Blob not allowed)

* Split the file into **chunks** smaller than Service Bus max message size.
* Send each chunk as a separate message.
* Consumer reassembles the file.
* This is less efficient but sometimes required.

---


# 39  Q: Before .NET Core introduced built-in dependency injection with `AddSingleton`, `AddTransient`, and `AddScoped`, how did developers implement dependency injection and manage object lifetimes in the .NET Framework?**

---

### ✅ Answer

Before .NET Core (i.e., in **ASP.NET MVC 5 / Web API** era on .NET Framework), there was **no built-in DI container**. Developers had to use **third-party IoC (Inversion of Control) containers** to manage lifetimes (Singleton, Transient, Scoped). Some popular ones were:

* **Unity**
* **Autofac**
* **Ninject**
* **StructureMap**
* **Castle Windsor**

---

### 🛠 How lifetimes were managed

Equivalent mappings in IoC containers:

| .NET Core DI Method | Unity Example                                                                          | Autofac Example                                                              | Meaning                          |
| ------------------- | -------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------- | -------------------------------- |
| `AddSingleton<T>()` | `container.RegisterType<IService, Service>(new ContainerControlledLifetimeManager());` | `builder.RegisterType<Service>().As<IService>().SingleInstance();`           | Single instance for entire app   |
| `AddTransient<T>()` | `container.RegisterType<IService, Service>();`                                         | `builder.RegisterType<Service>().As<IService>().InstancePerDependency();`    | New instance each time           |
| `AddScoped<T>()`    | `container.RegisterType<IService, Service>(new HierarchicalLifetimeManager());`        | `builder.RegisterType<Service>().As<IService>().InstancePerLifetimeScope();` | One instance per request (scope) |

---

### Example in ASP.NET MVC 5 (Unity)

```csharp
var container = new UnityContainer();

// Singleton
container.RegisterType<IService, Service>(
    new ContainerControlledLifetimeManager());

// Transient
container.RegisterType<IRepository, Repository>();

// Scoped (per request)
container.RegisterType<IUnitOfWork, UnitOfWork>(
    new HierarchicalLifetimeManager());

// Set Dependency Resolver
DependencyResolver.SetResolver(new UnityDependencyResolver(container));
```

# 40 **Q: Why do we usually register logging services with `AddSingleton` in .NET Core?**

---

### ✅ Answer

We use **`AddSingleton` for logging** because:

1. **Single shared instance** – The logger does not need a new instance for every request. A single instance is sufficient for the entire application lifetime.
2. **Thread-safe & lightweight** – Logging libraries (like `ILogger<T>`) are designed to be thread-safe and efficient. A singleton ensures consistent logging across requests without overhead.
3. **Centralized resource usage** – A logger may maintain open file handles, connections, or buffers. Having multiple instances could cause file locks or resource contention.
4. **Performance** – Since logging is used frequently, keeping it as a singleton avoids repeated object creation, improving performance.

---

### ✅ Example

```csharp
builder.Services.AddSingleton<ILogger, FileLogger>();
```

# 41 🔑 What is OAuth?
- OAuth (Open Authorization) is an **open standard protocol** that allows applications to access resources on behalf of a user without sharing their credentials.
- Example: When you log into a website using Google or Facebook, OAuth lets the site access your profile data securely without ever seeing your password.
- It works by issuing **access tokens** after user consent, which the application uses to call APIs.

# 42 🔐 How to Implement Custom Authentication in .NET Core Web API
1. Create a **custom middleware** or **authentication handler**.
2. Validate credentials (e.g., API key, custom token).
3. On success, create a `ClaimsPrincipal` and attach it to `HttpContext.User`.
4. Register the scheme in `Startup.cs` using `AddAuthentication()` and `AddScheme<T>()`.
5. Apply `[Authorize]` attribute to controllers/actions.

# 43 🔑 Difference Between JWT and OAuth
- **JWT (JSON Web Token):**
  - A **token format** (self-contained, includes claims).
  - Used for authentication/authorization.
  - Example: API issues JWT after login, client sends it in headers.
- **OAuth:**
  - A **protocol/authorization framework**.
  - Defines how tokens (often JWTs) are issued and used.
  - Example: OAuth flow issues JWT access tokens.

👉 In short: OAuth is the **process**, JWT is one possible **token format** used in that process.













