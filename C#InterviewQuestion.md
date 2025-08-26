
### **1. Difference between value and reference types. Is value type always stored on stack?**

* **Value types**: store the actual data (int, struct, bool, double).
* **Reference types**: store a reference (pointer) to data (class, string, object).
* **Myth**: Value types are *always* on stack ❌.

  * They can be stored in **stack** (local variables) or **heap** (if part of an object).

---

### **2. Output of given code**

```csharp
struct Num { public int i; }

class MyProgram {
    static void Main() {
        Num x = new Num();
        x.i = 10;
        Update(x);
        Console.Write(x.i);
    }
    static void Update(Num y) { y.i = 20; }
}
```

➡️ **Output: 10**
Reason: `struct` is a value type → passed **by value** (copy). Update modifies the copy, not original.

---

### **3. Difference between classes and structures. Can structs inherit? When to use?**

* **Class**:

  * Reference type, stored on heap.
  * Supports inheritance, polymorphism, destructors.
* **Struct**:

  * Value type, stored inline.
  * Cannot inherit (but can implement interfaces).
* **When to use structs**: small, immutable, short-lived data (e.g., Point, ComplexNumber).

---

### **4. Purpose of namespaces. Good practice?**

* **Purpose**: Organize code, avoid name conflicts.
* **Good practice**: Split logically (not everything in one namespace).

---

### **5. Is this code correct?**

```csharp
struct Num {
    public const double x = 1.0;
    public Num(double start) {
        x = start;
    }
}
```

❌ **Compile error**
Reason: `const` is compile-time constant, can’t be assigned in constructor.

---

### **6. Will this compile?**

```csharp
double d = 1.11;
int i = d;
```

❌ Error: Cannot implicitly convert double → int.
✅ Fix: `int i = (int)d;` or `int i = Convert.ToInt32(d);`.

---

### **7. Output of this code**

```csharp
int i = 5;
object b = i;
i++;
int c = (int)b;
c++;
Console.Write(i.ToString(), b);
```

Steps:

* `i = 5`, boxed → `b=5`.
* `i++` → `i=6`, but `b` still 5.
* `c=5` → `c++` → 6.
* `Console.Write(i.ToString(), b);` → ignores `b`.

➡️ **Output: 6**

---

### **8. Why lock() only accepts reference types?**

Because `lock` needs a **shared reference** to synchronize across threads.

* Locking on value types → boxing → different objects each time → no synchronization.

---

### **9. How are method arguments passed? Can this change?**

* By default:

  * **Value types** → by value.
  * **Reference types** → reference by value (reference is copied).
* Change using:

  * `ref` → pass by reference.
  * `out` → pass by reference, must assign inside method.
  * `in` (C# 7.2) → pass readonly reference.

---

### **10. Int.Parse vs Int.TryParse**

* `Int.Parse("123")` → returns int, throws exception on failure.
* `Int.TryParse("123", out int n)` → returns `bool` (true/false), safer.

---

### **11. Implicit vs Explicit Conversions**

* **Implicit**: No data loss (safe). Example: `int → long`.
* **Explicit**: Possible data loss, requires cast. Example: `double → int`.

---

### **12. Cast reference types without exception**

Use `as` operator → returns `null` if cast fails:

```csharp
MyClass obj = someObj as MyClass;
if(obj != null) { ... }
```

---

### **13. Why abstract class cannot be instantiated?**

Because it may have **abstract (incomplete) methods** without implementation.

---

### **14. Can you invoke a method from abstract class?**

Yes ✅ — if it has **implemented (non-abstract)** methods.

---

### **15. Interface can only contain method declarations?**

❌ Wrong.

* Interfaces can contain:

  * Methods (no body before C# 8).
  * Properties, indexers, events.
  * Default implementations (since C# 8).

---

### **16. Access modifiers inside interface?**

No ❌ — all members are **public** by default.

---

### **17. Inherit from two interfaces with same method name?**

Yes ✅ — implement both in one method, or explicitly implement per interface.

---

### **18. Two methods with same name & args, but different return type?**

❌ Not allowed in C#. Return type alone is not enough to differentiate.

---

### **19. Overriding vs Overloading**

* **Overriding**: Redefining base class virtual method. (Runtime polymorphism).
* **Overloading**: Same method name, different parameters. (Compile-time polymorphism).

---

### **20. protected internal**

* Accessible within:

  * Same assembly.
  * Derived classes (even in other assemblies).

---

### **21. Shape has only parameterized constructor. Can we do `new Shape()`?**

No ❌ — must define a **parameterless constructor** explicitly.

---

### **22. Override a non-virtual method?**

No ❌ — only methods marked `virtual`, `abstract`, or `override` can be overridden.

---

### **23. new vs override**

* **override**: Changes base class virtual method.
* **new**: Hides base class method (not polymorphic).

---

### **24. Explicitly call static constructor?**

No ❌ — CLR calls it once before first usage.

---

### **25. Override static constructor?**

No ❌ — static constructors cannot be inherited or overridden.

---

### **26. Use `this` inside static method?**

No ❌ — `this` refers to instance, not available in static context.

---

### **27. Static class vs Singleton**

* **Static class**: No instance, only static members.
* **Singleton**: Exactly one instance, controlled access. Can implement interfaces.

---

### **28. Immutable**

Means object cannot change after creation.
Examples: `string`, `System.DateTime`.

---

### **29. Create delegates**

```csharp
public delegate void MyDelegate(string msg);
MyDelegate d = Console.WriteLine;
d("Hello");
```

---

### **30. Delegates: Value or Reference type?**

✅ **Reference type**.

---

### **31. Events vs Multicast Delegates**

* **Delegate**: Points to one or many methods.
* **Event**: A safer wrapper around delegates (only `+=`/`-=` allowed).

---

### **32. Action vs Func**

* **Action<T>** → returns void.
* **Func\<T,TResult>** → returns value.

---

### **33. Lambda expressions**

Anonymous functions:

```csharp
(x,y) => x+y;
```

Used in LINQ, delegates, async code.

---

### **34. Access outside variables in lambda?**

Yes ✅ — lambdas can capture **closures** (outer scope variables).

---

### **35. LINQ**

Language Integrated Query → query collections/DB with SQL-like syntax.

---

### **36. What to do in catch block?**

Log error, handle, rethrow if necessary.
❌ Never silently swallow.

---

### **37. Does this make sense?**

```csharp
try { DoSomeWork(); }
catch(Exception ex){}
catch(StackOverflowException ex){}
```

❌ Wrong — second catch never reachable (StackOverflowException not catchable).

---

### **38. Reflection**

Inspect metadata, types, methods at runtime. Used in ORMs, serialization, testing frameworks.

---

### **39. Generics**

Type-safe data structures, avoid boxing. Example: `List<T>`.

---

### **40. Generic Constraints**

* `where T : class` (reference)
* `where T : struct` (value)
* `where T : new()` (must have default constructor)
* `where T : BaseClass`
* `where T : IInterface`

---

### **41. Force Garbage Collection?**

Yes, with `GC.Collect()`, but ❌ **not recommended** (let CLR manage).

---

### **42. GC Generations**

* Gen 0 → short-lived objects.
* Gen 1 → objects survived Gen 0.
* Gen 2 → long-lived objects.

---

### **43. IDisposable.Dispose**

Dispose unmanaged resources: file handles, DB connections, sockets.

---

### **44. Extend .NET class with your method?**

Yes ✅ — **Extension Methods**.

```csharp
public static class Extensions {
   public static bool IsEven(this int n) => n % 2 == 0;
}
```
