# 📘 Theoretical Questions

### **1. What main data structures do you know?**

* **Linear**: Array, List (ArrayList, LinkedList), Stack, Queue, Deque.
* **Non-linear**: Trees (Binary Tree, BST, AVL, B-Tree, Heap), Graphs.
* **Hash-based**: Hash Table / Hash Map, Hash Set.
* **Specialized**: Tries, Bloom filters, Disjoint Set (Union-Find).

---

### **2. Array vs List (main differences)**

| Feature            | **Array**                     | **List (Linked List)**                            |
| ------------------ | ----------------------------- | ------------------------------------------------- |
| Memory             | Fixed-size, contiguous memory | Dynamic size, scattered memory (nodes + pointers) |
| Access             | O(1) (direct index access)    | O(n) (must traverse)                              |
| Insertion/Deletion | Costly in middle (O(n))       | Cheaper in middle if node known (O(1))            |
| Cache-friendliness | High (sequential memory)      | Low (pointers cause cache misses)                 |

---

### **3. Singly vs Doubly Linked List**

* **Singly**: Each node has `data` + `next`. Traversal only forward.
* **Doubly**: Each node has `data` + `next` + `prev`. Traversal both ways.
* **Tradeoff**: Doubly uses more memory, but allows faster deletion/insertion if pointer given.

---

### **4. Stack vs Queue**

* **Stack**: LIFO (Last In First Out), operations → `push`, `pop`.
* **Queue**: FIFO (First In First Out), operations → `enqueue`, `dequeue`.
* **Variants**: Priority Queue, Deque (double-ended queue).

---

### **5. What is algorithm complexity?**

* **Time Complexity**: How runtime grows with input size (Big-O, Big-Ω, Big-Θ).
* **Space Complexity**: Extra memory used.
* **Examples**: Linear Search O(n), Binary Search O(log n).

---

### **6. Associative Unordered vs Ordered Containers**

* **Ordered (like TreeMap, std::map in C++):** Elements stored in sorted order. Lookup O(log n).
* **Unordered (like HashMap, std::unordered\_map):** Based on hash tables. Lookup average O(1).

---

### **7. Explain what the binary tree is.**

* A **hierarchical data structure** where each node has:

  * Data
  * Left child
  * Right child
* **Special cases**: BST (binary search tree), Complete Binary Tree, Heap.

---

### **8. Search time complexity in Binary Tree**

* **Binary Tree (not BST):** O(n) (must check all nodes).
* **BST (balanced):** O(log n).
* **Unbalanced BST:** O(n).
  👉 So, not always guaranteed unless tree is balanced.

---

### **9. DFS vs BFS in Binary Tree**

* **DFS (Depth-First Search):** Preorder, Inorder, Postorder. Uses stack (recursion/explicit).
* **BFS (Breadth-First Search):** Level-order traversal. Uses queue.

---

### **10. Hash Table Structure & Working**

* **Structure**: Array of buckets. Each bucket stores a linked list or tree of key-value pairs.
* **Working**:

  1. Hash function computes index = `hash(key) % table_size`.
  2. Insert into that bucket.
  3. Resolve collisions via chaining (linked list) or open addressing.

---

### **11. Time Complexity in Hash Table**

* **Average**: O(1) (insert/search/delete).
* **Worst case**: O(n) (if collisions poorly handled).

---

### **12. Binary Search Algorithm**

* Search in **sorted array** by halving the search space each step.
* **Steps**: Compare middle element → move left or right.
* **Time Complexity**: O(log n).
* **Space**: O(1) (iterative) or O(log n) (recursive).

---

### **13. Sorting Algorithms Known**

* **Comparison-based**: Bubble Sort, Insertion Sort, Selection Sort, Merge Sort, Quick Sort, Heap Sort.
* **Non-comparison**: Counting Sort, Radix Sort, Bucket Sort.
* **Complexities**:

  * Bubble/Insertion/Selection → O(n²)
  * Merge/Heap/Quick (avg) → O(n log n)
  * Counting/Radix → O(n) (with constraints)

---

# 🛠 Practical Tasks

### **1. Effectively delete sequence from middle of array**

* Use `memmove()` (in C) or `Array.Copy()` (in C#).
* **Time Complexity**: O(n).

---

### **2. Missing element from 0–99 array**

* **If unsorted**: Sum formula → `expected_sum = n*(n+1)/2`, subtract from actual sum.
* **If sorted**: Binary search for first mismatch index.

---

### **3. Find duplicates in array**

* **HashSet**: Store visited, check for repeats. O(n).
* **Sorting + Scan**: Sort O(n log n), check neighbors.
* **Chars**: Use frequency array (size 256).

---

### **4. Middle element of singly linked list in one pass**

* Use **slow & fast pointer** method.
* Slow moves 1 step, fast moves 2 steps. When fast reaches end, slow is middle.

---

### **5. Detect loop in singly linked list**

* **Floyd’s Cycle Detection (Tortoise and Hare Algorithm):**

  * Slow pointer = 1 step, Fast = 2 steps.
  * If they meet, loop exists.

---

### **6. Sorted binary tree from given array: \[20, 17, 30, 21, 45, 2, 18]**

```
         20
        /  \
      17    30
     / \      \
    2  18     45
          \
           21
```

* **Worst BST sequence**: Sorted sequence (ascending or descending). Example: \[2, 17, 18, 20, 21, 30, 45]. This degenerates into a linked list (O(n) search).

---

### **7. Recursive & Non-Recursive Tree Traversal**

* **Recursive Inorder (LNR):**

```python
def inorder(node):
    if node:
        inorder(node.left)
        print(node.val)
        inorder(node.right)
```

* **Non-Recursive Inorder (stack):**

```python
def inorder_iter(root):
    stack = []
    curr = root
    while curr or stack:
        while curr:
            stack.append(curr)
            curr = curr.left
        curr = stack.pop()
        print(curr.val)
        curr = curr.right
```

(Same can be done for preorder & postorder using recursion/stack.)

---

# 📚 Literature

1. **Algorithms** – Robert Sedgewick, Kevin Wayne (practical + Java-based).
2. **Introduction to Algorithms** – Cormen et al. (CLRS, detailed theory).
3. **Programming Pearls** – Jon Bentley (problem-solving approach).


