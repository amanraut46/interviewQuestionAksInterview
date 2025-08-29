In **programming (especially in C# / Java / Python, etc.)**, when we copy an object, there are **two main types of copies**:

---

## 🔹 **Shallow Copy**

* A **shallow copy** creates a new object, but it **copies references** of nested objects instead of creating new copies of them.
* That means:

  * Primitive types (int, string, bool, etc.) are copied directly.
  * Reference types (objects, arrays, lists, etc.) are **not duplicated**—instead, both the original and the copy **share the same reference**.

👉 So, changing a nested object in one copy will reflect in the other.

✅ Example (C#):

```csharp
class Person
{
    public string Name;
    public Address Addr;
}

class Address
{
    public string City;
}

var p1 = new Person { Name = "Aman", Addr = new Address { City = "Pune" } };
var p2 = (Person)p1.MemberwiseClone(); // Shallow Copy

p2.Name = "Rahul";   // Only changes in p2
p2.Addr.City = "Mumbai"; // This will also update p1's Addr.City (shared reference)
```

---

## 🔹 **Deep Copy**

* A **deep copy** creates a new object, and it also **creates new copies of all nested objects**.
* No references are shared—everything is **duplicated**.
* Changing one object will **not affect** the other.

✅ Example (C#):

```csharp
class Person
{
    public string Name;
    public Address Addr;

    public Person DeepCopy()
    {
        return new Person
        {
            Name = this.Name,
            Addr = new Address { City = this.Addr.City }
        };
    }
}

var p1 = new Person { Name = "Aman", Addr = new Address { City = "Pune" } };
var p2 = p1.DeepCopy();

p2.Addr.City = "Mumbai"; // Does NOT affect p1
```

---

## 🔑 **Key Differences**

| Feature        | Shallow Copy                            | Deep Copy              |
| -------------- | --------------------------------------- | ---------------------- |
| Nested Objects | Copies references                       | Creates new objects    |
| Memory Usage   | Less                                    | More                   |
| Performance    | Faster                                  | Slower                 |
| Independence   | Shared references (changes affect both) | Completely independent |

---

### ✅ Interview Questions:

1. What is the difference between shallow copy and deep copy?
2. How do you implement deep copy in C# (or Python, Java)?
3. When should you use deep copy over shallow copy?
4. What happens if you modify a nested object after making a shallow copy?

---

Do you want me to also **write down ready-made short answers** for these 4 interview questions so you can answer them confidently?
