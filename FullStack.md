
## 🔹 Data Types

**1. Name all data types in JavaScript.**
JavaScript has **7 primitive types**:

* `string`, `number`, `bigint`, `boolean`, `undefined`, `symbol`, `null`
  and **reference types**: `Object`, `Array`, `Function`, `Map`, `Set`, `Date`, etc.

**2. Compare the reference types and primitive types.**

* **Primitive types** → immutable, stored by value in memory, copied directly. Example: changing one string does not affect another.
* **Reference types** → stored as objects, copied by reference, so two variables can point to the same object.

**3. Ways of checking data types.**

* `typeof` → works for most primitives, but `typeof null` = `"object"` (quirk).
* `instanceof` → checks if object belongs to a prototype.
* `Array.isArray()` → specifically for arrays.
* `Object.prototype.toString.call(x)` → most reliable for built-ins.

**4. Abstract vs Strict Equality.**

* `==` (abstract equality) → allows type coercion, e.g. `"5" == 5 → true`.
* `===` (strict equality) → checks type + value, `"5" === 5 → false`.

**5. Type coercion.**
JavaScript automatically converts types in some cases:

* String + Number → string concatenation.
* Boolean in comparison → `true → 1`, `false → 0`.
* Rules can cause confusion, so prefer `===`.

**6. Built-in vs Wrappers.**

* Built-in objects: `Object`, `Array`, `Date`, `Math`.
* Wrappers: objects around primitives (`new String("hi")`, `new Number(5)`). Generally avoided, but used internally when you call methods on primitives.

**7. Data types that can/cannot contain values.**

* **Primitives** hold only single values.
* **Objects, Arrays, Maps, Sets** can hold collections.

**8. Practical use of Symbol.**
Symbols create **unique keys for object properties** so they don’t clash with other keys, even if the names are the same. Useful in libraries and frameworks.

---

## 🔹 Grammar

**9. Statements vs Expressions.**

* **Expression** → produces a value (`2+3`, `x>10`).
* **Statement** → performs an action (`if`, `for`, `return`).
  Side effects: statements may change variables, expressions usually don’t.

**10. Operator precedence.**
Defines which operator executes first. For example, `*` executes before `+`. Logical operators (`&&`, `||`) short-circuit, meaning they stop evaluating once the result is known.

**11. Destructuring.**
A convenient syntax to unpack values:

```js
const [a,b] = [1,2];
const {name, age} = person;
```

**12. Loops, Conditions, Execution Context.**

* Loops: `for`, `while`, `for...of`, `for...in`.
* Conditions: `if`, `switch`.
* Execution context: environment in which JS runs (global or function).

**13. Error handling.**
Use `try...catch...finally`. Example:

```js
try { JSON.parse("bad") } 
catch(e) { console.error("Invalid JSON") }
```

**14. "use strict".**
Enables strict mode → prevents silent errors (like assigning to undeclared variables), makes JS safer.

---

## 🔹 Scope & Closure

**15. var / let / const.**

* `var` → function-scoped, hoisted.
* `let` → block-scoped, not accessible before declaration.
* `const` → block-scoped, must be initialized, immutable binding.

**16. Determine if variable declared in scope.**

* Use `typeof varName !== "undefined"`.

**17. Scope types.**

* Global, Function, Block.
* ES6 introduced block scope (`let`, `const`).

**18. Hoisting & Temporal Dead Zone (TDZ).**

* Hoisting → variable/function declarations moved to top.
* TDZ → `let` and `const` exist in memory but not usable until declared.

**19. Scope chain.**
When a variable is used, JS searches in current scope → outer scope → global.

**20. Closure.**
A closure is a function that remembers variables from its outer scope even after that scope has finished executing.

**21. Memory lifecycle in closures.**
Variables captured by closures remain in memory as long as the closure exists. This can be useful or cause memory leaks if not handled.

---

## 🔹 Functions

**22. Function declarations & invocations.**

* Function Declaration → hoisted.
* Function Expression → not hoisted.
* Arrow Functions → no `this`, concise syntax.
* IIFE → runs immediately.

**23. First-class functions.**
Functions are treated like objects: stored in variables, passed as arguments, returned from functions.

**24. Execution context vs Scope.**

* Execution context → runtime environment (what is `this`, variable object).
* Scope → lexical environment created at definition.

**25. this keyword.**
Refers to the context:

* In global → `window`/`global`.
* In method → the object.
* In arrow → lexical `this`.

**26. Partial application vs Currying.**

* Partial: fixing some arguments of a function.
* Currying: breaking a function into multiple single-argument functions.

**27. Borrowing methods.**
Using `call`, `apply`, or `bind` to use one object’s method for another. Example:

```js
Array.prototype.slice.call(arguments);
```

---

## 🔹 OOP & FP

**28. OOP principles in JS.**

* Encapsulation (private fields).
* Inheritance (prototypes/classes).
* Polymorphism (method overriding).
* Abstraction (hiding details via functions).

**29. Classical vs Prototypal inheritance.**

* Classical → class-based, inheritance hierarchy.
* Prototypal → objects inherit from other objects via prototype chain.

**30. Ways to create objects.**

* `{}`, `new Object()`, `Object.create(proto)`, class constructor.

**31. Object attributes.**
Each property has → `writable`, `enumerable`, `configurable`.

**32. Access modifiers.**
No built-in. Can simulate using closures or new `#privateFields`.

**33. Pros/Cons FP vs OOP.**

* FP: simpler, predictable, testable. Harder with shared state.
* OOP: models real-world entities, but can lead to tight coupling.

**34. Functional programming.**
Style based on pure functions, immutability, higher-order functions.

**35. Higher-order function.**
Takes or returns another function (`map`, `filter`).

**36. Pure function.**
Always returns same output for same input, no side effects.

**37. Currying.**
Transforms function with multiple arguments into series of unary functions.

**38. Object composition over inheritance.**
Build objects by combining small, reusable functions instead of deep class hierarchies.

**39. Imperative vs Declarative.**

* Imperative = tells **how** (step by step).
* Declarative = tells **what** (focus on result).

**40. SOLID in JS.**
Applies as design principles: modular, maintainable code. Dependency injection/inversion of control = passing dependencies instead of hardcoding them.

---

## 🔹 Architecture

**41. Two-way vs One-way binding.**

* Two-way (Angular) → UI auto syncs with state.
* One-way (React) → Data flows downward, changes handled explicitly.

**42. Monolithic vs Microservices.**

* Monolithic: single deployable unit, simpler but less scalable.
* Microservices: modular services, scalable but complex.

---

## 🔹 Async JS

**43. Handling async code.**
Callbacks, Promises, async/await, Observables.

**44. Callback hell.**
Deeply nested callbacks → solved using Promises or async/await.

**45. async/await.**
Syntactic sugar over Promises. Code looks synchronous.

**46. Generators & Iterators.**
Generators (`function*`) yield values lazily. Useful for async tasks.

**47. yield delegation.**
`yield*` passes control to another generator.

**48. Web Workers.**
Run JS in background threads (not sharing main thread). SharedWorkers = multiple tabs.

**49. Micro vs Macro tasks.**

* Micro: Promises, queueMicrotask.
* Macro: setTimeout, setInterval, I/O.

---

## 🔹 Data Structures

**50. Common array methods.**
`map`, `filter`, `reduce`, `forEach`, `find`, `some`, `every`.

**51. reduce vs slice vs splice.**

* reduce → accumulates values.
* slice → returns shallow copy, non-mutating.
* splice → removes/inserts, mutates array.

**52. Map, Set, WeakMap, WeakSet.**

* Map: key-value pairs, any type of key.
* Set: unique values.
* WeakMap/WeakSet: keys must be objects, weakly held (GC-friendly).

**53. Traditional data structures.**
Stack, Queue, Linked List, Tree, Graph. Implemented via arrays/objects in JS.

---

## 🔹 Node.js

**54. Event loop.**
Single-threaded mechanism (libuv) that handles async I/O via queue.

**55. Pros/Cons of Node.js.**

* Pros: Fast, non-blocking, great for I/O heavy apps.
* Cons: Not ideal for CPU-intensive apps.

**56. Streams.**
Handle large data in chunks (files, network).

**57. Types of streams.**
Readable, Writable, Duplex, Transform.

**58. Multithreading.**
Node is single-threaded but supports Worker Threads for parallelism.

**59. Multiple cores.**
Use `cluster` module to run multiple Node processes.

**60. Node vs Browser JS.**

* Node: server-side, has `fs`, `http`.
* Browser: client-side, has `DOM`, `window`.

**61. REST API architecture in Node.**
Layered → Routes → Controllers → Services → DB layer.

---

## 🔹 Testing

**62. Test types.**

* Unit: individual functions.
* Integration: modules together.
* Functional/E2E: full app flow.

**63. TDD vs BDD.**

* TDD: write test first, then code.
* BDD: focus on describing behavior/specs.

**64. Best practices.**
Small, isolated tests, mocks for dependencies, automate with CI/CD.

---
