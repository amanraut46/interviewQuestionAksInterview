 **1..Difference between Middleware and Action Filters** in ASP.NET (Core or MVC) lies in their **purpose, scope, and where they fit in the request pipeline**.

---

## 🔹 1. **Middleware**

### ✅ Definition:

Middleware is **global logic** that runs early in the request pipeline—before routing, MVC, and filters. It's used to process or modify HTTP requests/responses.

### ✅ Characteristics:

* Runs **before and after** the routing to controller/actions.
* Works on **all requests** (not just MVC).
* Applied in `Startup.cs` (`Program.cs` in .NET 6+).
* Can short-circuit the pipeline (e.g., return a response early).

### ✅ Common Use Cases:

* Authentication
* Logging
* CORS
* Exception handling
* Custom headers

### ✅ Example:

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

### ✅ Definition:

Action Filters are part of the **MVC pipeline** and are used to execute logic **before or after** a controller action executes.

### ✅ Characteristics:

* Runs **within the MVC pipeline** (after routing).
* Can be applied **globally, to a controller, or to an action**.
* Only works on **MVC or Web API requests**.
* More granular than middleware.

### ✅ Types of Filters:

* `ActionFilter`
* `AuthorizationFilter`
* `ExceptionFilter`
* `ResultFilter`

### ✅ Common Use Cases:

* Logging input/output of actions
* Validation
* Auditing
* Modifying action parameters or result

### ✅ Example:

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

**2.ASP.NET Core, dependency injection (DI) is built-in, and the three main service lifetimes you can register are:**

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

## 🧠 Example Scenario:

Suppose you inject the same service into both a controller and a repository:

* **Singleton** → Both will get **same object**, even across requests.
* **Scoped** → Both will get **same object in same request**, but different between requests.
* **Transient** → Both will get **different object** every time it's injected.

---

## ⚠️ Important Notes:

* Never use **`AddSingleton` for services that depend on `Scoped` services** (like `DbContext`) — this can cause serious threading issues.
* Use **`AddScoped` for Entity Framework DbContext** or anything request-bound.

---

Let me know if you want a working code demo showing how all three behave!

