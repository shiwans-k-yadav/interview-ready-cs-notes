# 📘 Chapter 6: Dynamic Memory Allocation in C++
 
> Dynamic memory allocation gives a program the ability to request and release memory at **runtime**, enabling flexible and efficient use of memory beyond what compile-time allocation offers.
 
---
 
## Table of Contents
 
- [1. Memory Layout of a C++ Program](#1-memory-layout-of-a-c-program)
- [2. Stack vs Heap Memory](#2-stack-vs-heap-memory)
- [3. The `new` Operator](#3-the-new-operator)
- [4. The `delete` Operator](#4-the-delete-operator)
- [5. Dynamic Arrays](#5-dynamic-arrays)
- [6. Dynamic 2D Arrays](#6-dynamic-2d-arrays)
- [7. Common Memory Problems](#7-common-memory-problems)
- [8. `new` vs `malloc` / `delete` vs `free`](#8-new-vs-malloc--delete-vs-free)
- [9. RAII — Resource Acquisition Is Initialization](#9-raii--resource-acquisition-is-initialization)
- [10. Introduction to Smart Pointers](#10-introduction-to-smart-pointers)
- [11. Interview Q&A — Theoretical](#11-interview-qa--theoretical)
- [12. Interview Q&A — Coding](#12-interview-qa--coding)
- [13. Practice Questions](#13-practice-questions)
---
 
## 1. Memory Layout of a C++ Program
 
When a C++ program runs, the operating system divides memory into distinct segments:
 
```
High Address
┌─────────────────────┐
│      Stack          │  ← Local variables, function call frames
│         ↓           │    (grows downward)
├─────────────────────┤
│                     │
│    (free space)     │
│                     │
├─────────────────────┤
│         ↑           │
│      Heap           │  ← Dynamic memory (new / malloc)
│                     │    (grows upward)
├─────────────────────┤
│   BSS Segment       │  ← Uninitialized global/static variables
├─────────────────────┤
│   Data Segment      │  ← Initialized global/static variables
├─────────────────────┤
│   Text Segment      │  ← Program code (read-only)
└─────────────────────┘
Low Address
```
 
---
 
## 2. Stack vs Heap Memory
 
| Feature | Stack | Heap |
|---------|-------|------|
| Management | Automatic (compiler) | Manual (`new` / `delete`) |
| Size | Small (typically 1–8 MB) | Large (limited by RAM) |
| Speed | Very fast (LIFO order) | Slower (fragmentation possible) |
| Lifetime | Until function returns | Until explicitly deleted |
| Allocation failure | Stack overflow | `bad_alloc` exception |
| Use case | Local variables, function frames | Dynamic/large data structures |
| Memory leaks | Not possible | Possible if not deleted |
 
```cpp
void example()
{
    int x = 10;           // Stack — automatically freed when function ends
    int* ptr = new int;   // Heap — must be manually deleted
    *ptr = 20;
    delete ptr;           // Manually freeing heap memory
}
```
 
> **Interview Point:** Stack memory is automatically managed — no need to free it. Heap memory is your responsibility — always `delete` what you `new`.
 
---
 
## 3. The `new` Operator
 
`new` allocates memory on the **heap** at runtime and returns a pointer to the allocated memory. It also **calls the constructor** for objects.
 
### Allocating a Single Variable
 
```cpp
// Syntax
pointer = new data_type;
pointer = new data_type(initial_value);
 
// Examples
int* p = new int;        // Allocates int, uninitialized
int* p = new int(42);    // Allocates int, initialized to 42
float* f = new float(3.14);
 
cout << *p;  // 42
```
 
### What happens internally
 
1. Requests memory from the free store (heap)
2. If memory is available — allocates and returns pointer
3. If memory is **not** available — throws `std::bad_alloc` exception
### Handling Allocation Failure
 
```cpp
#include <new>
 
try
{
    int* p = new int[1000000000];  // Large allocation
    // use p...
    delete[] p;
}
catch(bad_alloc& e)
{
    cout << "Memory allocation failed: " << e.what();
}
```
 
---
 
## 4. The `delete` Operator
 
`delete` frees memory that was previously allocated with `new`. It also **calls the destructor** for objects.
 
```cpp
int* p = new int(10);
cout << *p;   // 10
 
delete p;     // Free the memory
p = nullptr;  // Best practice — avoid dangling pointer
```
 
> **Critical Rule:** Every `new` must have a matching `delete`. Forgetting `delete` causes a **memory leak**.
 
### What happens after `delete`
 
```cpp
int* p = new int(10);
delete p;
 
// p is now a dangling pointer — points to freed memory
cout << *p;   // Undefined behavior — crash or garbage value
 
// Fix: always set to nullptr after delete
p = nullptr;
cout << *p;   // Will crash cleanly (nullptr dereference) — easier to debug
```
 
---
 
## 5. Dynamic Arrays
 
When you need an array whose size is determined at runtime, use `new[]` and `delete[]`.
 
```cpp
int n;
cout << "Enter size: ";
cin >> n;
 
// Allocate array of n integers on heap
int* arr = new int[n];
 
// Use the array
for(int i = 0; i < n; i++)
    arr[i] = i * 10;
 
for(int i = 0; i < n; i++)
    cout << arr[i] << " ";
 
// MUST use delete[] for arrays (not delete)
delete[] arr;
arr = nullptr;
```
 
> **Interview Trap:** Using `delete` instead of `delete[]` for an array is **undefined behavior**. Only the first element's destructor gets called, and the rest of the memory may not be properly freed.
 
```cpp
int* arr = new int[5];
 
delete arr;    // ❌ Wrong — undefined behavior for arrays
delete[] arr;  // ✅ Correct — frees entire array
```
 
---
 
## 6. Dynamic 2D Arrays
 
### Method 1 — Array of Pointers (Most Common)
 
```cpp
int rows = 3, cols = 4;
 
// Step 1: Allocate array of row pointers
int** matrix = new int*[rows];
 
// Step 2: Allocate each row
for(int i = 0; i < rows; i++)
    matrix[i] = new int[cols];
 
// Use it like a normal 2D array
matrix[1][2] = 42;
cout << matrix[1][2];  // 42
 
// Step 3: Free each row first, then the pointer array
for(int i = 0; i < rows; i++)
    delete[] matrix[i];
delete[] matrix;
matrix = nullptr;
```
 
### Method 2 — Single Block (More Efficient)
 
```cpp
int rows = 3, cols = 4;
int* matrix = new int[rows * cols];
 
// Access element at row i, col j
matrix[i * cols + j] = value;
 
// Free — single delete
delete[] matrix;
```
 
---
 
## 7. Common Memory Problems
 
### Memory Leak
 
Memory is allocated but never freed. The program gradually consumes more and more memory.
 
```cpp
void leakyFunction()
{
    int* ptr = new int(100);
    // Function ends — ptr is destroyed
    // But the heap memory it pointed to is NEVER freed
    // This is a memory leak
}
 
// Fix:
void safeFunction()
{
    int* ptr = new int(100);
    // ... use ptr ...
    delete ptr;   // Free before leaving
    ptr = nullptr;
}
```
 
---
 
### Double Delete
 
Calling `delete` on the same pointer twice — leads to **undefined behavior** (often a crash).
 
```cpp
int* ptr = new int(10);
delete ptr;
delete ptr;    // ❌ Double delete — undefined behavior
 
// Fix:
delete ptr;
ptr = nullptr;
delete ptr;    // ✅ Safe — deleting nullptr does nothing
```
 
> **Interview Point:** It is safe to call `delete` on a `nullptr`. The standard guarantees it does nothing.
 
---
 
### Memory Leak with Exception
 
```cpp
void riskyFunction()
{
    int* ptr = new int(10);
    doSomething();   // If this throws an exception...
    delete ptr;      // ...this line is NEVER reached = memory leak
}
 
// Fix: Use smart pointers (covered next section)
```
 
---
 
### Accessing Freed Memory
 
```cpp
int* ptr = new int(10);
delete ptr;
cout << *ptr;   // ❌ Undefined behavior — memory is freed
```
 
---
 
### Summary of Memory Problems
 
| Problem | Cause | Effect |
|---------|-------|--------|
| Memory leak | `new` without `delete` | Program uses more and more memory |
| Dangling pointer | Accessing memory after `delete` | Undefined behavior / crash |
| Double delete | `delete` called twice on same pointer | Crash / heap corruption |
| Wild pointer | Uninitialized pointer dereferenced | Undefined behavior |
| Stack overflow | Infinite recursion or huge local array | Program crash |
 
---
 
## 8. `new` vs `malloc` / `delete` vs `free`
 
| Feature | `new` / `delete` | `malloc` / `free` |
|---------|-----------------|-------------------|
| Language | C++ | C (also available in C++) |
| Calls constructor/destructor | ✅ Yes | ❌ No |
| Returns type | Typed pointer | `void*` (needs cast) |
| On failure | Throws `bad_alloc` | Returns `NULL` |
| Can be overloaded | ✅ Yes | ❌ No |
| Size calculation | Automatic | Manual (`sizeof`) |
| Preferred in C++ | ✅ Always | ❌ Avoid unless interfacing with C |
 
```cpp
// C++ style — preferred
int* p = new int(10);
delete p;
 
// C style — avoid in C++
int* p = (int*)malloc(sizeof(int));
*p = 10;
free(p);
```
 
> **Interview Point:** Never mix `new` with `free` or `malloc` with `delete`. Each pair must be used together.
 
---
 
## 9. RAII — Resource Acquisition Is Initialization
 
RAII is a fundamental C++ programming principle: **tie resource lifetime to object lifetime**. When an object is created, it acquires a resource. When it is destroyed, it releases the resource automatically.
 
```cpp
class ManagedArray
{
    int* data;
public:
    ManagedArray(int size)
    {
        data = new int[size];   // Acquire resource in constructor
        cout << "Memory allocated\n";
    }
 
    ~ManagedArray()
    {
        delete[] data;           // Release resource in destructor
        cout << "Memory freed\n";
    }
};
 
void example()
{
    ManagedArray arr(10);  // Constructor called — memory allocated
    // ... use arr ...
}   // Destructor called automatically — memory freed, even if exception occurs
```
 
> **Interview Point:** RAII is the foundation behind smart pointers, file handles, mutexes, and virtually all resource management in modern C++. The destructor always runs when an object goes out of scope — even during exception unwinding.
 
---
 
## 10. Introduction to Smart Pointers
 
Smart pointers are RAII wrappers around raw pointers. They automatically `delete` the memory when they go out of scope. Defined in `<memory>`.
 
### `unique_ptr` — Exclusive Ownership
 
Only one `unique_ptr` can own a resource at a time. Cannot be copied, only moved.
 
```cpp
#include <memory>
 
unique_ptr<int> ptr = make_unique<int>(42);
cout << *ptr;   // 42
 
// Automatically deleted when ptr goes out of scope
// No need to call delete
```
 
### `shared_ptr` — Shared Ownership
 
Multiple `shared_ptr`s can own the same resource. Memory is freed when the **last** `shared_ptr` to it is destroyed (reference counting).
 
```cpp
shared_ptr<int> p1 = make_shared<int>(100);
shared_ptr<int> p2 = p1;   // Both own the same resource
 
cout << *p1;              // 100
cout << p1.use_count();   // 2 — two owners
 
p1.reset();               // p1 releases ownership
cout << p2.use_count();   // 1 — only p2 owns it now
// Memory freed when p2 goes out of scope
```
 
### `weak_ptr` — Non-Owning Observer
 
Refers to an object managed by `shared_ptr` without increasing the reference count. Used to break **circular references**.
 
```cpp
shared_ptr<int> sp = make_shared<int>(10);
weak_ptr<int> wp = sp;   // Does not increase use_count
 
cout << sp.use_count();  // 1 — wp doesn't count
 
// To use wp, must lock it to get a shared_ptr
if(auto locked = wp.lock())
    cout << *locked;     // 10
```
 
### Smart Pointer Comparison
 
| Feature | `unique_ptr` | `shared_ptr` | `weak_ptr` |
|---------|-------------|-------------|------------|
| Ownership | Exclusive (one owner) | Shared (many owners) | Non-owning |
| Copy allowed | ❌ No | ✅ Yes | ✅ Yes |
| Move allowed | ✅ Yes | ✅ Yes | ✅ Yes |
| Reference count | No | Yes | No (observes) |
| Overhead | Minimal | Small (ref count) | Minimal |
| Use case | Sole ownership | Shared ownership | Breaking cycles |
 
> **Best Practice in Modern C++:** Prefer `make_unique` and `make_shared` over raw `new`. Use smart pointers wherever possible — they eliminate memory leaks, double deletes, and dangling pointers automatically.
 
---
 
## 11. Interview Q&A — Theoretical
 
**Q1. What is dynamic memory allocation? Why do we need it?**
 
Dynamic memory allocation is the process of requesting memory from the heap at runtime using `new`. We need it when the size of data is not known at compile time (e.g., user-defined array size), when data needs to outlive the function that created it, or when working with large data structures like linked lists and trees that grow and shrink during execution.
 
---
 
**Q2. What is the difference between stack and heap memory?**
 
Stack memory is automatically managed by the compiler. Local variables and function call frames live on the stack and are automatically freed when the function returns. It is fast but limited in size. Heap memory is manually managed — you allocate with `new` and must free with `delete`. It is larger and slower, and memory persists until explicitly freed. Failure to free heap memory causes memory leaks.
 
---
 
**Q3. What is a memory leak? How do you prevent it?**
 
A memory leak occurs when dynamically allocated heap memory is never freed — typically because the pointer to it is lost or `delete` is never called. Over time this exhausts available memory. Prevention: always pair `new` with `delete`, prefer smart pointers (`unique_ptr`, `shared_ptr`) which automatically manage lifetime, and use tools like Valgrind or AddressSanitizer to detect leaks.
 
---
 
**Q4. What is the difference between `delete` and `delete[]`?**
 
`delete` is used to free a single object allocated with `new`. `delete[]` is used to free an array allocated with `new[]`. Using `delete` on an array only destructs the first element and causes undefined behavior for the rest. Using `delete[]` properly destructs all elements and frees the entire block.
 
---
 
**Q5. What is the difference between `new`/`delete` and `malloc`/`free`?**
 
`new` calls the constructor and is type-safe; `malloc` only allocates raw bytes with no initialization and returns `void*`. `delete` calls the destructor; `free` just releases memory. `new` throws `std::bad_alloc` on failure; `malloc` returns `NULL`. They must never be mixed — `new` with `free` or `malloc` with `delete` is undefined behavior.
 
---
 
**Q6. What is RAII? Why is it important?**
 
RAII (Resource Acquisition Is Initialization) is the principle of tying resource lifetime to object lifetime. A resource (memory, file handle, mutex) is acquired in a constructor and released in the destructor. Since destructors run automatically when objects go out of scope — even during exceptions — RAII guarantees resources are always properly released. It is the foundation behind smart pointers and modern C++ resource management.
 
---
 
**Q7. What is the difference between `unique_ptr` and `shared_ptr`?**
 
`unique_ptr` represents exclusive ownership — only one `unique_ptr` can own a resource at a time and it cannot be copied (only moved). `shared_ptr` uses reference counting to allow multiple pointers to share ownership of a resource. The memory is freed only when the last `shared_ptr` owning it is destroyed. `unique_ptr` has no overhead beyond a raw pointer; `shared_ptr` has a small overhead for the reference count.
 
---
 
**Q8. Is it safe to call `delete` on a `nullptr`?**
 
Yes. The C++ standard explicitly guarantees that `delete nullptr` and `delete[] nullptr` are no-ops — they do nothing and do not cause undefined behavior. This is why setting a pointer to `nullptr` after `delete` makes subsequent accidental deletions safe.
 
---
 
## 12. Interview Q&A — Coding
 
**Q1. What is wrong with this code?**
 
```cpp
void allocate()
{
    int* arr = new int[100];
    cout << arr[0];
}
```
 
**Problem:** Memory leak. The array is allocated on the heap but never freed. Once `allocate()` returns, `arr` is destroyed (it's a local variable) and the 100 integers on the heap are permanently lost.
 
**Fix:**
 
```cpp
void allocate()
{
    int* arr = new int[100];
    cout << arr[0];
    delete[] arr;   // Free before returning
    arr = nullptr;
}
```
 
---
 
**Q2. What is the output and what is the problem?**
 
```cpp
int* ptr = new int(10);
delete ptr;
cout << *ptr;
```
 
**Output:** Undefined behavior — could print garbage, 0, 10, or crash.
 
**Problem:** `ptr` is a dangling pointer after `delete`. Dereferencing it is undefined behavior.
 
**Fix:**
 
```cpp
int* ptr = new int(10);
delete ptr;
ptr = nullptr;
// cout << *ptr;  // Would crash cleanly — easier to debug
```
 
---
 
**Q3. Find all the bugs.**
 
```cpp
int* p = new int[5];
for(int i = 0; i <= 5; i++)
    p[i] = i;
delete p;
```
 
**Bug 1:** Loop runs from `0` to `5` inclusive — `p[5]` is **out of bounds** (valid indices are 0–4).
 
**Bug 2:** `delete p` should be `delete[] p` for an array.
 
**Fixed:**
 
```cpp
int* p = new int[5];
for(int i = 0; i < 5; i++)   // < not <=
    p[i] = i;
delete[] p;                    // [] for arrays
p = nullptr;
```
 
---
 
**Q4. Write a function that dynamically allocates an array, fills it with squares, prints it, and cleans up properly.**
 
```cpp
void squaresArray(int n)
{
    int* arr = new int[n];
 
    for(int i = 0; i < n; i++)
        arr[i] = (i + 1) * (i + 1);
 
    for(int i = 0; i < n; i++)
        cout << arr[i] << " ";
 
    delete[] arr;
    arr = nullptr;
}
 
int main()
{
    squaresArray(5);  // Output: 1 4 9 16 25
}
```
 
---
 
**Q5. Rewrite this using `unique_ptr` to avoid manual memory management.**
 
```cpp
// Original (raw pointer)
int* ptr = new int(99);
cout << *ptr;
delete ptr;
 
// Rewritten with unique_ptr
#include <memory>
unique_ptr<int> ptr = make_unique<int>(99);
cout << *ptr;
// No delete needed — freed automatically when ptr goes out of scope
```
 
---
 
## 13. Practice Questions
 
**Theoretical Q1. What happens when `new` fails to allocate memory?**
 
In modern C++, `new` throws a `std::bad_alloc` exception if it cannot allocate the requested memory. The program should handle this with a `try-catch` block. In older code, you may see `new(nothrow)` which returns `nullptr` instead of throwing.
 
```cpp
int* p = new(nothrow) int[999999999];
if(p == nullptr)
    cout << "Allocation failed";
else
{
    // use p
    delete[] p;
}
```
 
---
 
**Theoretical Q2. Can you use `free()` to release memory allocated with `new`?**
 
No. Mixing `new` with `free` or `malloc` with `delete` is undefined behavior. `new` may use a different memory manager than `malloc`, and `delete` triggers the destructor while `free` does not. Always use `delete` with `new` and `free` with `malloc`.
 
---
 
**Coding Q1. What is the output?**
 
```cpp
int* p = new int(5);
int* q = p;
delete p;
p = nullptr;
cout << (p == nullptr) << " " << (q == nullptr);
```
 
**Output:** `1 0`
 
> `p` is set to `nullptr` so `p == nullptr` is true (1). `q` still holds the old address — it was not updated and is now a dangling pointer. `q == nullptr` is false (0).
 
---
 
**Coding Q2. What is the output?**
 
```cpp
#include <memory>
unique_ptr<int> a = make_unique<int>(10);
unique_ptr<int> b = move(a);
 
cout << (a == nullptr) << " " << *b;
```
 
**Output:** `1 10`
 
> After `move(a)`, ownership transfers to `b`. `a` becomes `nullptr`. `*b` gives the value `10`.
 
---
 
**Coding Q3. How many times is memory allocated and how many times freed?**
 
```cpp
int* arr = new int[3];
int* ptr = arr;
delete[] arr;
```
 
**Answer:** Allocated once (`new int[3]`), freed once (`delete[] arr`). `ptr` is just a copy of the address — it does not own the memory. After `delete[] arr`, both `arr` and `ptr` are dangling pointers.
 
---
 
## Quick Recap
 
| Concept | Key Point |
|---------|-----------|
| Stack | Automatic, fast, limited, freed on scope exit |
| Heap | Manual, flexible, large, freed with `delete` |
| `new` | Allocates on heap, calls constructor, throws on failure |
| `delete` | Frees single object, calls destructor |
| `delete[]` | Frees array — must match `new[]` |
| Memory leak | `new` without `delete` — pointer lost |
| Dangling pointer | Pointer used after `delete` — undefined behavior |
| Double delete | `delete` twice — undefined behavior |
| Safe after `delete` | Always set pointer to `nullptr` |
| `new` vs `malloc` | `new` calls constructor, is type-safe, throws on failure |
| RAII | Tie resource lifetime to object scope |
| `unique_ptr` | Exclusive ownership, no overhead |
| `shared_ptr` | Shared ownership, reference counting |
| `weak_ptr` | Non-owning, breaks circular references |
 
---
 
📌 **Next Chapter: OOP — Classes & Objects**
