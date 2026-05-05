# 📘 Chapter 5: Pointers & References in C++
 
> Pointers and References are among the most powerful — and most frequently tested — features of C++. A solid understanding of both is non-negotiable for any C++ interview.
 
---
 
## Table of Contents
 
- [1. Pointers — Introduction](#1-pointers--introduction)
- [2. Pointer Declaration & Initialization](#2-pointer-declaration--initialization)
- [3. Dereferencing a Pointer](#3-dereferencing-a-pointer)
- [4. Pointer Arithmetic](#4-pointer-arithmetic)
- [5. Pointers and Arrays](#5-pointers-and-arrays)
- [6. Pointer to Pointer](#6-pointer-to-pointer)
- [7. Types of Pointers](#7-types-of-pointers)
- [8. References — Introduction](#8-references--introduction)
- [9. Pointers vs References](#9-pointers-vs-references)
- [10. `const` with Pointers and References](#10-const-with-pointers-and-references)
- [11. `this` Pointer](#11-this-pointer)
- [12. Function Pointers](#12-function-pointers)
- [13. Interview Q&A — Theoretical](#13-interview-qa--theoretical)
- [14. Interview Q&A — Coding](#14-interview-qa--coding)
- [15. Practice Questions](#15-practice-questions)
---
 
## 1. Pointers — Introduction
 
A pointer is a variable that **stores the memory address** of another variable. Instead of holding a value directly, it holds the location in memory where the value is stored.
 
### Why Pointers?
 
- Dynamic memory allocation
- Passing large data to functions efficiently (without copying)
- Building data structures like linked lists, trees, graphs
- Direct memory access and manipulation
```
Variable:   int x = 10;
Memory:     Address 1000 → Value 10
 
Pointer:    int* ptr = &x;
Memory:     Address 2000 → Value 1000  (ptr stores address of x)
```
 
---
 
## 2. Pointer Declaration & Initialization
 
```cpp
int x = 10;
 
int* ptr;       // Declaration (uninitialized — dangerous!)
ptr = &x;       // Assign address of x to ptr
 
// Or in one line
int* ptr = &x;
 
cout << x;    // 10  — value of x
cout << &x;   // 1000 (some address) — address of x
cout << ptr;  // 1000 — value stored in ptr (address of x)
cout << *ptr; // 10  — value at the address ptr points to
```
 
### The `&` and `*` Operators
 
| Operator | Name | Meaning |
|----------|------|---------|
| `&` | Address-of | Returns the memory address of a variable |
| `*` | Dereference | Returns the value at the address a pointer holds |
 
---
 
## 3. Dereferencing a Pointer
 
Dereferencing means **accessing the value** stored at the address the pointer points to.
 
```cpp
int x = 42;
int* ptr = &x;
 
cout << *ptr;   // 42 — reads the value at address
 
*ptr = 100;     // Modifies x through the pointer
cout << x;      // 100 — x is now changed
```
 
> **Interview Trap:** Dereferencing an uninitialized or `nullptr` pointer causes **undefined behavior** or a **segmentation fault**.
 
---
 
## 4. Pointer Arithmetic
 
You can perform arithmetic on pointers. The pointer moves by the **size of the data type** it points to, not by 1 byte.
 
```cpp
int arr[] = {10, 20, 30, 40, 50};
int* ptr = arr;  // Points to arr[0]
 
cout << *ptr;       // 10
ptr++;              // Moves forward by sizeof(int) = 4 bytes
cout << *ptr;       // 20
cout << *(ptr + 2); // 40 — jumps 2 positions ahead
 
// Pointer difference
int* p1 = &arr[1];
int* p2 = &arr[4];
cout << p2 - p1;    // 3 — number of elements between them
```
 
### Valid Pointer Arithmetic Operations
 
| Operation | Valid? |
|-----------|--------|
| `ptr + n` | ✅ Move forward n elements |
| `ptr - n` | ✅ Move backward n elements |
| `ptr++` / `ptr--` | ✅ Increment / decrement |
| `ptr1 - ptr2` | ✅ Difference (number of elements) |
| `ptr1 + ptr2` | ❌ Adding two pointers is invalid |
| `ptr * n` | ❌ Multiplication is invalid |
 
---
 
## 5. Pointers and Arrays
 
An array name is essentially a **constant pointer** to the first element.
 
```cpp
int arr[] = {1, 2, 3, 4, 5};
 
cout << arr;      // Address of arr[0]
cout << &arr[0];  // Same address
 
// Accessing elements via pointer
int* ptr = arr;
cout << ptr[2];    // 3 — same as arr[2]
cout << *(ptr+2);  // 3 — pointer arithmetic
```
 
> **Interview Point:** `arr` and `&arr[0]` give the same address, but `arr + 1` and `&arr + 1` are **different**. `arr + 1` moves by `sizeof(int)`, while `&arr + 1` moves by `sizeof(entire array)`.
 
---
 
## 6. Pointer to Pointer
 
A pointer that stores the address of another pointer.
 
```cpp
int x = 10;
int* ptr = &x;     // ptr → x
int** pptr = &ptr; // pptr → ptr → x
 
cout << x;      // 10
cout << *ptr;   // 10
cout << **pptr; // 10
 
**pptr = 99;
cout << x;      // 99 — x was modified
```
 
```
pptr → ptr → x
2000 → 1000 → 10
```
 
> **Use case:** Used in dynamic 2D arrays, passing pointers to functions so the pointer itself can be changed.
 
---
 
## 7. Types of Pointers
 
### Null Pointer
 
A pointer that does not point to any valid memory location.
 
```cpp
int* ptr = nullptr;  // C++11 preferred
int* ptr = NULL;     // C-style
 
if(ptr == nullptr)
    cout << "Pointer is null";
```
 
> Always initialize pointers to `nullptr` if not assigning immediately.
 
---
 
### Void Pointer
 
A pointer that can point to any data type. Must be cast before dereferencing.
 
```cpp
int x = 10;
float f = 3.14;
 
void* ptr;
ptr = &x;
cout << *(int*)ptr;    // 10 — cast to int* before dereferencing
 
ptr = &f;
cout << *(float*)ptr;  // 3.14
```
 
---
 
### Dangling Pointer
 
A pointer that points to memory that has already been **freed or gone out of scope**.
 
```cpp
// Case 1 — Pointer after delete
int* ptr = new int(10);
delete ptr;
// ptr is now dangling — accessing it is undefined behavior
ptr = nullptr;  // Fix: set to nullptr after delete
 
// Case 2 — Returning address of local variable
int* getPointer()
{
    int x = 5;
    return &x;  // DANGER: x is destroyed after function returns
}
```
 
> **Interview Point:** A dangling pointer is one of the most common sources of bugs and security vulnerabilities in C++. Always set pointers to `nullptr` after `delete`.
 
---
 
### Wild Pointer
 
An uninitialized pointer that points to some random memory location.
 
```cpp
int* ptr;         // Wild pointer — points to garbage address
*ptr = 10;        // Undefined behavior — very dangerous!
 
// Fix:
int* ptr = nullptr;  // Always initialize
```
 
---
 
### Summary of Special Pointers
 
| Type | Description | Risk |
|------|-------------|------|
| Null pointer | Points to nothing (`nullptr`) | Safe if checked before use |
| Void pointer | Can point to any type | Must cast before dereferencing |
| Dangling pointer | Points to freed/out-of-scope memory | Undefined behavior |
| Wild pointer | Uninitialized pointer | Undefined behavior |
 
---
 
## 8. References — Introduction
 
A reference is an **alias** — another name for an already existing variable. It is not a separate variable; it refers to the same memory location.
 
```cpp
int x = 10;
int& ref = x;  // ref is an alias for x
 
cout << x;    // 10
cout << ref;  // 10 — same value
 
ref = 50;     // Modifies x through the reference
cout << x;    // 50
```
 
### Key Rules for References
 
- Must be **initialized at declaration** — `int& ref;` is illegal
- Cannot be reassigned to refer to a different variable
- Cannot be `nullptr`
- No need to dereference — used just like the original variable
---
 
## 9. Pointers vs References
 
| Feature | Pointer | Reference |
|---------|---------|-----------|
| Syntax | `int* ptr = &x` | `int& ref = x` |
| Can be null | Yes (`nullptr`) | No — must always refer to something |
| Initialization | Not required (but dangerous) | Must be initialized at declaration |
| Reassignment | Can point to different variables | Cannot be reassigned |
| Dereferencing | Needs `*` to access value | Used directly like a variable |
| Arithmetic | Supports pointer arithmetic | No arithmetic |
| Memory | Has its own memory address | Shares address with original variable |
| Use case | Dynamic memory, optional reference | Function parameters, aliases |
 
```cpp
int x = 10, y = 20;
 
// Pointer — can be reassigned
int* ptr = &x;
ptr = &y;   // Now points to y — valid
 
// Reference — cannot be reassigned
int& ref = x;
ref = y;    // This ASSIGNS y's value to x, doesn't rebind ref
```
 
---
 
## 10. `const` with Pointers and References
 
There are three important combinations with `const` and pointers:
 
### Pointer to Const — value cannot be changed
 
```cpp
int x = 10;
const int* ptr = &x;
 
*ptr = 20;   // ❌ Error — cannot modify value through ptr
ptr = &y;    // ✅ OK — pointer itself can be reassigned
```
 
### Const Pointer — pointer cannot be reassigned
 
```cpp
int x = 10, y = 20;
int* const ptr = &x;
 
*ptr = 20;   // ✅ OK — can modify value
ptr = &y;    // ❌ Error — pointer cannot be reassigned
```
 
### Const Pointer to Const — neither can change
 
```cpp
int x = 10;
const int* const ptr = &x;
 
*ptr = 20;   // ❌ Error
ptr = &y;    // ❌ Error
```
 
### Memory Aid
 
```
const int* ptr   → "what ptr points to" is const  (read right to left: ptr is a pointer to const int)
int* const ptr   → "ptr itself" is const           (ptr is a const pointer to int)
const int* const → both are const
```
 
### Const Reference
 
```cpp
void display(const int& val)
{
    cout << val;    // ✅ Can read
    // val = 10;   // ❌ Cannot modify
}
```
 
> **Best Practice:** Always use `const&` for function parameters when you don't need to modify the argument — avoids copying and prevents accidental modification.
 
---
 
## 11. `this` Pointer
 
The `this` pointer is an **implicit pointer** available inside every non-static member function of a class. It points to the object that called the function.
 
```cpp
class Student
{
    int marks;
public:
    void setMarks(int marks)
    {
        this->marks = marks;  // this->marks = member variable
                               // marks = parameter
    }
 
    Student& setName(string name)
    {
        this->name = name;
        return *this;  // Returns current object — enables method chaining
    }
};
```
 
### Key Points about `this`
 
- Available in all non-static member functions
- Not available in static member functions (they have no object context)
- Used to resolve naming conflicts between member variables and parameters
- Used to return the current object (`return *this`) for method chaining
---
 
## 12. Function Pointers
 
A function pointer stores the **address of a function** and can be used to call it indirectly.
 
```cpp
// Normal function
int add(int a, int b)
{
    return a + b;
}
 
// Declare a function pointer
int (*funcPtr)(int, int);
 
// Assign function address
funcPtr = add;
 
// Call through function pointer
cout << funcPtr(3, 4);   // 7
cout << (*funcPtr)(3, 4); // 7 — same result
```
 
### Use Case — Callbacks
 
```cpp
void applyOperation(int a, int b, int (*op)(int, int))
{
    cout << op(a, b);
}
 
int multiply(int a, int b) { return a * b; }
 
int main()
{
    applyOperation(3, 4, add);       // 7
    applyOperation(3, 4, multiply);  // 12
}
```
 
---
 
## 13. Interview Q&A — Theoretical
 
**Q1. What is a pointer? Why do we use pointers in C++?**
 
A pointer is a variable that stores the memory address of another variable. We use pointers for dynamic memory allocation, passing large objects to functions without copying, building data structures like linked lists and trees, and for direct memory manipulation in systems programming.
 
---
 
**Q2. What is the difference between a pointer and a reference?**
 
A pointer stores a memory address and requires `*` to dereference. It can be null, reassigned, and supports arithmetic. A reference is an alias for an existing variable — it cannot be null, cannot be reassigned to refer to a different variable, and is used directly without dereferencing. References are generally safer and cleaner for function parameters.
 
---
 
**Q3. What is a dangling pointer? How do you avoid it?**
 
A dangling pointer points to memory that has already been freed or gone out of scope. It arises when you delete a pointer but don't set it to `nullptr`, or when you return the address of a local variable from a function. Avoid it by setting pointers to `nullptr` after `delete`, using smart pointers, and never returning addresses of local variables.
 
---
 
**Q4. What is the difference between a null pointer, void pointer, dangling pointer, and wild pointer?**
 
| Pointer Type | Definition |
|-------------|------------|
| Null pointer | Explicitly set to `nullptr` — points to nothing |
| Void pointer | Points to any data type — must cast before use |
| Dangling pointer | Points to freed/out-of-scope memory |
| Wild pointer | Uninitialized — points to random memory |
 
---
 
**Q5. What is the `this` pointer? When is it used?**
 
`this` is an implicit pointer available inside all non-static member functions of a class. It points to the object that invoked the function. It is used to resolve name conflicts between member variables and local parameters, and to return the current object from a function for method chaining.
 
---
 
**Q6. What is `const int* ptr` vs `int* const ptr`?**
 
`const int* ptr` is a pointer to a constant integer — the value it points to cannot be modified, but the pointer itself can be reassigned. `int* const ptr` is a constant pointer to an integer — the pointer cannot be reassigned to a different address, but the value at the address can be modified.
 
---
 
**Q7. Can a reference be null? Why or why not?**
 
No. A reference must be initialized when declared and must always refer to a valid variable. There is no concept of a null reference in standard C++. This is one of the key safety advantages references have over pointers.
 
---
 
**Q8. What is pointer arithmetic and how does it work?**
 
Pointer arithmetic allows adding or subtracting integers from pointers. When you increment a pointer by 1, it moves forward by `sizeof(pointed type)` bytes, not by 1 byte. So for an `int*`, `ptr + 1` moves 4 bytes forward. This makes it easy to traverse arrays using pointers.
 
---
 
## 14. Interview Q&A — Coding
 
**Q1. What is the output?**
 
```cpp
int x = 5;
int* ptr = &x;
*ptr = *ptr + 10;
cout << x;
```
 
**Output:** `15`
 
> `*ptr` accesses `x` through the pointer. `*ptr + 10` = 15, assigned back to `x`.
 
---
 
**Q2. What is the output?**
 
```cpp
int arr[] = {10, 20, 30, 40};
int* ptr = arr;
ptr += 2;
cout << *ptr;
```
 
**Output:** `30`
 
> `ptr` starts at `arr[0]`. `ptr += 2` moves it to `arr[2]`, which holds `30`.
 
---
 
**Q3. What is wrong with this code?**
 
```cpp
int* getVal()
{
    int x = 100;
    return &x;
}
 
int main()
{
    int* p = getVal();
    cout << *p;
}
```
 
**Problem:** This returns the address of a local variable `x`. Once `getVal()` returns, `x` is destroyed and `p` becomes a **dangling pointer**. Dereferencing `p` is undefined behavior.
 
**Fix:** Allocate on heap or return by value.
 
```cpp
int* getVal()
{
    int* x = new int(100);
    return x;  // Heap memory — persists after function returns
}
```
 
---
 
**Q4. Write a function to swap two numbers using pointers.**
 
```cpp
void swap(int* a, int* b)
{
    int temp = *a;
    *a = *b;
    *b = temp;
}
 
int main()
{
    int x = 5, y = 10;
    swap(&x, &y);
    cout << x << " " << y;  // 10 5
}
```
 
---
 
**Q5. Write a function to reverse a string in-place using pointers.**
 
```cpp
void reverseString(char* str)
{
    char* end = str;
    while(*end != '\0') end++;  // Move end to last character
    end--;
 
    while(str < end)
    {
        char temp = *str;
        *str = *end;
        *end = temp;
        str++;
        end--;
    }
}
 
int main()
{
    char s[] = "Hello";
    reverseString(s);
    cout << s;  // olleH
}
```
 
---
 
## 15. Practice Questions
 
**Theoretical Q1. Why should you prefer references over pointers for function parameters?**
 
References are safer because they cannot be null and cannot accidentally point to wrong memory. They also have cleaner syntax — no need for `*` or `&` at the call site. Pointers should be preferred when the parameter is optional (can be null) or when you need to reassign it.
 
---
 
**Theoretical Q2. Is it possible to have a pointer to a reference?**
 
No. A reference is not an object — it is an alias. Since pointers store addresses of objects, and references are not objects with their own address, you cannot have a pointer to a reference. However, you can have a reference to a pointer.
 
```cpp
int x = 10;
int* ptr = &x;
int*& refToPtr = ptr;  // ✅ Reference to a pointer — valid
```
 
---
 
**Coding Q1. What is the output?**
 
```cpp
int x = 10;
int& ref = x;
ref = 20;
int* ptr = &ref;
*ptr = 50;
cout << x << " " << ref;
```
 
**Output:** `50 50`
 
> `ref` is an alias for `x`. `ptr` points to the same memory. All three — `x`, `ref`, and `*ptr` — refer to the same location.
 
---
 
**Coding Q2. What is the output?**
 
```cpp
int a = 1, b = 2;
int* ptr = &a;
*ptr = 10;
ptr = &b;
*ptr = 20;
cout << a << " " << b;
```
 
**Output:** `10 20`
 
> First `ptr` modifies `a` to 10, then reassigned to `b` and modifies it to 20.
 
---
 
**Coding Q3. What is the output?**
 
```cpp
int x = 5;
int* p = &x;
int** pp = &p;
 
**pp = 100;
cout << x;
```
 
**Output:** `100`
 
> `pp` points to `p`, which points to `x`. `**pp` reaches `x` through two levels of indirection.
 
---
 
## Quick Recap
 
| Concept | Key Point |
|---------|-----------|
| Pointer | Stores address of a variable; needs `*` to dereference |
| Reference | Alias for a variable; no dereferencing needed |
| Null pointer | `nullptr` — points to nothing; safe |
| Dangling pointer | Points to freed memory — undefined behavior |
| Wild pointer | Uninitialized — always initialize to `nullptr` |
| `const int* ptr` | Cannot change value through pointer |
| `int* const ptr` | Cannot reassign pointer to different address |
| `this` pointer | Points to current object inside member functions |
| Pointer arithmetic | Moves by `sizeof(type)` bytes per step |
| Function pointer | Stores address of a function for indirect calls |
 
---
 
📌 **Next Chapter: Dynamic Memory Allocation**
