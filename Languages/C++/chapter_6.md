# 📘 Chapter 6: Dynamic Memory Allocation in C++
 
> Dynamic memory allocation lets a program request memory at **runtime** instead of at compile time — useful when you don't know the size of data in advance.
 
---
 
## Table of Contents
 
- [1. Types of Memory in C++](#1-types-of-memory-in-c)
- [2. Stack vs Heap Memory](#2-stack-vs-heap-memory)
- [3. The `new` Operator](#3-the-new-operator)
- [4. The `delete` Operator](#4-the-delete-operator)
- [5. Dynamic Arrays](#5-dynamic-arrays)
- [6. Memory Leak](#6-memory-leak)
- [7. Dangling Pointer](#7-dangling-pointer)
- [8. `new` vs `malloc`](#8-new-vs-malloc)
- [9. Interview Q&A — Theoretical](#9-interview-qa--theoretical)
- [10. Interview Q&A — Coding](#10-interview-qa--coding)
- [11. Practice Questions](#11-practice-questions)
---
 
## 1. Types of Memory in C++
 
When a C++ program runs, memory is divided into two main areas:
 
```
┌─────────────────────┐
│       Stack         │  ← Local variables (automatic)
├─────────────────────┤
│       Heap          │  ← Dynamic memory (manual)
├─────────────────────┤
│    Global/Static    │  ← Global variables
├─────────────────────┤
│    Program Code     │  ← Instructions
└─────────────────────┘
```
 
- **Stack** — memory managed automatically by the compiler
- **Heap** — memory you manage manually using `new` and `delete`
---
 
## 2. Stack vs Heap Memory
 
| Feature | Stack | Heap |
|---------|-------|------|
| Management | Automatic | Manual (`new` / `delete`) |
| Size | Small (few MB) | Large |
| Speed | Fast | Slower |
| Lifetime | Until function ends | Until you call `delete` |
| Risk | Stack overflow | Memory leak |
 
```cpp
void example()
{
    int x = 10;          // Stack — freed automatically when function ends
    int* p = new int(5); // Heap — YOU must free this
    delete p;            // Manually freed
}
```
 
---
 
## 3. The `new` Operator
 
`new` allocates memory on the **heap** at runtime and returns a pointer to it.
 
```cpp
// Allocate a single variable
int* p = new int;        // Uninitialized
int* p = new int(42);    // Initialized to 42
 
cout << *p;  // 42
```
 
> `new` returns the **address** of the allocated memory, so you always store it in a pointer.
 
---
 
## 4. The `delete` Operator
 
`delete` frees the heap memory that was allocated with `new`.
 
```cpp
int* p = new int(10);
cout << *p;  // 10
 
delete p;    // Free the memory
p = nullptr; // Good practice — avoid dangling pointer
```
 
> **Rule:** Every `new` must have exactly one matching `delete`. No `delete` = memory leak.
 
---
 
## 5. Dynamic Arrays
 
Use `new[]` and `delete[]` when the array size is known only at runtime.
 
```cpp
int n;
cin >> n;
 
int* arr = new int[n];   // Array of n integers on heap
 
for(int i = 0; i < n; i++)
    arr[i] = i + 1;
 
for(int i = 0; i < n; i++)
    cout << arr[i] << " ";
 
delete[] arr;   // Must use delete[] for arrays, not delete
arr = nullptr;
```
 
> **Interview Trap:** Using `delete` instead of `delete[]` on an array is **undefined behavior**. Always use `delete[]` for arrays.
 
```cpp
int* arr = new int[5];
delete arr;     // ❌ Wrong
delete[] arr;   // ✅ Correct
```
 
---
 
## 6. Memory Leak
 
A memory leak happens when you allocate memory with `new` but **never free it** with `delete`. The memory stays occupied for the entire life of the program.
 
```cpp
void leaky()
{
    int* p = new int(100);
    // Function ends — p is destroyed
    // But the heap memory is NEVER freed = memory leak
}
```
 
**Fixed:**
 
```cpp
void safe()
{
    int* p = new int(100);
    cout << *p;
    delete p;    // Free before leaving
    p = nullptr;
}
```
 
> **Real-world impact:** In long-running programs (servers, apps), memory leaks gradually eat up RAM and eventually crash the program.
 
---
 
## 7. Dangling Pointer
 
A dangling pointer is a pointer that **still holds an address after the memory has been freed**.
 
```cpp
int* p = new int(10);
delete p;       // Memory is freed
cout << *p;     // ❌ Dangling pointer — undefined behavior
```
 
**Fix:** Always set to `nullptr` after `delete`.
 
```cpp
int* p = new int(10);
delete p;
p = nullptr;    // Safe — no longer dangling
```
 
> **Interview Point:** Accessing a dangling pointer leads to **undefined behavior** — the program may crash, print garbage, or appear to work incorrectly.
 
---
 
## 8. `new` vs `malloc`
 
Both allocate heap memory, but they differ in important ways.
 
| Feature | `new` | `malloc` |
|---------|-------|----------|
| Language | C++ | C |
| Calls constructor | ✅ Yes | ❌ No |
| Returns type | Typed pointer | `void*` (needs cast) |
| On failure | Throws exception | Returns `NULL` |
| Preferred in C++ | ✅ Yes | ❌ Avoid |
 
```cpp
// C++ way — preferred
int* p = new int(10);
delete p;
 
// C way — avoid in C++
int* p = (int*)malloc(sizeof(int));
*p = 10;
free(p);
```
 
> **Rule:** Never mix them. Don't use `free` with `new`, and don't use `delete` with `malloc`.
 
---
 
## 9. Interview Q&A — Theoretical
 
**Q1. What is dynamic memory allocation?**
 
It is the process of allocating memory at **runtime** from the heap using `new`. Unlike stack memory (which is fixed and automatic), heap memory can be requested as needed and must be manually released using `delete`.
 
---
 
**Q2. What is the difference between stack and heap memory?**
 
Stack memory is automatically managed — local variables are created on the stack and freed when the function returns. Heap memory is manually managed — you allocate it with `new` and must free it with `delete`. Stack is faster but limited in size; heap is larger but slower and prone to memory leaks if not managed properly.
 
---
 
**Q3. What is a memory leak?**
 
A memory leak occurs when heap memory is allocated with `new` but never freed with `delete`. The memory remains occupied for the lifetime of the program, wasting resources. In long-running applications this can cause the program to slow down or crash.
 
---
 
**Q4. What is a dangling pointer?**
 
A dangling pointer is a pointer that holds the address of memory that has already been freed. Dereferencing it leads to undefined behavior — garbage values, crashes, or silent data corruption. The fix is to set the pointer to `nullptr` immediately after calling `delete`.
 
---
 
**Q5. What is the difference between `delete` and `delete[]`?**
 
`delete` is used to free a single object allocated with `new`. `delete[]` is used to free an array allocated with `new[]`. Using `delete` on an array is undefined behavior — it may only free part of the memory.
 
---
 
**Q6. What is the difference between `new` and `malloc`?**
 
`new` is a C++ operator that allocates memory and also calls the constructor of the object. `malloc` is a C function that only allocates raw bytes — it does not call any constructor. `new` throws an exception on failure; `malloc` returns `NULL`. In C++, `new`/`delete` should always be preferred.
 
---
 
**Q7. Is it safe to call `delete` on a `nullptr`?**
 
Yes. The C++ standard guarantees that calling `delete` on a `nullptr` is a no-op — it does nothing and does not cause any error. This is why setting pointers to `nullptr` after deletion makes the code safer.
 
---
 
## 10. Interview Q&A — Coding
 
**Q1. What is wrong with this code?**
 
```cpp
void show()
{
    int* p = new int(50);
    cout << *p;
}
```
 
**Problem:** Memory leak. `p` is allocated on the heap but never deleted. When `show()` returns, `p` is gone but the heap memory stays allocated.
 
**Fix:**
 
```cpp
void show()
{
    int* p = new int(50);
    cout << *p;
    delete p;
    p = nullptr;
}
```
 
---
 
**Q2. Find the bug.**
 
```cpp
int* arr = new int[10];
// ... use arr ...
delete arr;
```
 
**Bug:** Should be `delete[] arr` not `delete arr`. Using `delete` on an array is undefined behavior.
 
**Fix:** `delete[] arr;`
 
---
 
**Q3. What is the output?**
 
```cpp
int* p = new int(25);
int* q = p;
delete p;
p = nullptr;
cout << (p == nullptr);
cout << (q == nullptr);
```
 
**Output:** `10`
 
> `p` is set to `nullptr` → `p == nullptr` is true (1). `q` was never updated — it still holds the old freed address → `q == nullptr` is false (0). `q` is now a dangling pointer.
 
---
 
**Q4. Write a program to take array size from user, fill it with even numbers, and free memory.**
 
```cpp
int main()
{
    int n;
    cout << "Enter size: ";
    cin >> n;
 
    int* arr = new int[n];
 
    for(int i = 0; i < n; i++)
        arr[i] = (i + 1) * 2;
 
    for(int i = 0; i < n; i++)
        cout << arr[i] << " ";
 
    delete[] arr;
    arr = nullptr;
    return 0;
}
// Input: 5 → Output: 2 4 6 8 10
```
 
---
 
## 11. Practice Questions
 
**Theoretical Q1. Why do we need dynamic memory allocation if we already have arrays?**
 
Normal arrays have a fixed size decided at compile time. If you need an array whose size depends on user input or runtime conditions, you need dynamic allocation. Also, stack size is limited — large data structures must live on the heap.
 
---
 
**Theoretical Q2. What happens if you use `delete` instead of `delete[]` for an array?**
 
It is undefined behavior. The destructor is only called for the first element, and the rest of the memory may not be freed correctly. It can cause memory leaks, crashes, or heap corruption.
 
---
 
**Coding Q1. What is the output?**
 
```cpp
int* p = new int(10);
*p = *p + 5;
cout << *p;
delete p;
```
 
**Output:** `15`
 
> `*p` is 10, adding 5 makes it 15. Memory is then properly freed.
 
---
 
**Coding Q2. How many bugs can you find?**
 
```cpp
int n = 5;
int* arr = new int[n];
for(int i = 0; i <= n; i++)
    arr[i] = i;
delete arr;
```
 
**Bug 1:** `i <= n` goes out of bounds — `arr[5]` doesn't exist (valid: 0 to 4).
 
**Bug 2:** `delete arr` should be `delete[] arr`.
 
**Fixed:**
 
```cpp
int n = 5;
int* arr = new int[n];
for(int i = 0; i < n; i++)
    arr[i] = i;
delete[] arr;
arr = nullptr;
```
 
---
 
**Coding Q3. What is the difference between these two?**
 
```cpp
int* a = new int(5);    // Line 1
int* b = new int[5];    // Line 2
```
 
**Answer:**
 
Line 1 allocates a **single integer** initialized to `5`. Free with `delete a`.
 
Line 2 allocates an **array of 5 integers** (uninitialized). Free with `delete[] b`.
 
---
 
## Quick Recap
 
| Concept | Key Point |
|---------|-----------|
| Stack | Automatic, fast, freed when function ends |
| Heap | Manual, flexible, freed with `delete` |
| `new` | Allocates on heap, returns pointer |
| `delete` | Frees single object |
| `delete[]` | Frees array — must match `new[]` |
| Memory leak | `new` without `delete` |
| Dangling pointer | Pointer used after `delete` — set to `nullptr` |
| `new` vs `malloc` | `new` calls constructor, is type-safe |
 
---
 
📌 **Next Chapter: OOP — Classes & Objects**
 
