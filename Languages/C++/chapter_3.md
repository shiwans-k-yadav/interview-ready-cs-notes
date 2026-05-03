# 📘 Chapter 3: Functions in C++

> A function is a block of reusable code that performs a specific task. Functions make programs modular, readable, and easier to maintain.

---

## Table of Contents

- [1. What is a Function?](#1-what-is-a-function)
- [2. Types of Functions](#2-types-of-functions)
- [3. Function Declaration, Definition & Call](#3-function-declaration-definition--call)
- [4. Function Arguments — Pass by Value vs Pass by Reference](#4-function-arguments--pass-by-value-vs-pass-by-reference)
- [5. Default Arguments](#5-default-arguments)
- [6. Inline Functions](#6-inline-functions)
- [7. Function Overloading](#7-function-overloading)
- [8. Recursion](#8-recursion)
- [9. Scope of Variables](#9-scope-of-variables)
- [10. Interview Q&A](#10-interview-qa)
- [11. Practice Questions](#11-practice-questions)

---

## 1. What is a Function?

A function is a named block of code that can be called multiple times. Every C++ program has at least one function — `main()`.

**Syntax:**

```cpp
return_type function_name(parameters)
{
    // body
    return value;
}
```

**Example:**

```cpp
int add(int a, int b)
{
    return a + b;
}

int main()
{
    int result = add(3, 4);
    cout << result;  // Output: 7
}
```

---

## 2. Types of Functions

| Type | Description |
|------|-------------|
| Built-in / Library | Provided by C++ (`sqrt()`, `pow()`, `strlen()`) |
| User-defined | Written by the programmer |
| Void function | Returns nothing (`void`) |
| Non-void function | Returns a value (`int`, `float`, etc.) |
| Recursive function | Calls itself |
| Inline function | Expanded at call site by compiler |

---

## 3. Function Declaration, Definition & Call

```cpp
// Declaration (prototype) — tells compiler about the function
int multiply(int a, int b);

// Definition — actual body
int multiply(int a, int b)
{
    return a * b;
}

// Call — using the function
int main()
{
    cout << multiply(3, 5);  // Output: 15
}
```

> **Note:** Declaration is needed only if the function is defined **after** `main()`.

---

## 4. Function Arguments — Pass by Value vs Pass by Reference

### Pass by Value

A **copy** of the argument is passed. Original variable is not changed.

```cpp
void square(int x)
{
    x = x * x;
    cout << x;  // 25
}

int main()
{
    int n = 5;
    square(n);
    cout << n;  // Still 5 — original unchanged
}
```

### Pass by Reference

The **actual variable** is passed. Changes affect the original.

```cpp
void square(int &x)
{
    x = x * x;
}

int main()
{
    int n = 5;
    square(n);
    cout << n;  // 25 — original changed
}
```

### Comparison Table

| Feature | Pass by Value | Pass by Reference |
|---------|--------------|-------------------|
| What is passed | Copy of variable | Address of variable |
| Original affected? | No | Yes |
| Memory usage | More (copy created) | Less |
| Use case | When you don't want to modify original | When you want to modify original |

---

## 5. Default Arguments

You can assign default values to parameters. If the caller doesn't pass a value, the default is used.

```cpp
int add(int a, int b = 10)
{
    return a + b;
}

int main()
{
    cout << add(5);     // Output: 15 (b = 10 default)
    cout << add(5, 3);  // Output: 8  (b = 3 provided)
}
```

> **Rule:** Default arguments must be at the **rightmost** side of the parameter list.

```cpp
// Valid
void show(int a, int b = 5, int c = 10);

// Invalid — non-default after default
void show(int a = 5, int b, int c = 10);  // ❌ Error
```

---

## 6. Inline Functions

An inline function is expanded **at the point of call** instead of a normal function call. This avoids function call overhead for small, frequently used functions.

```cpp
inline int square(int x)
{
    return x * x;
}

int main()
{
    cout << square(5);  // Compiler replaces with: cout << 5 * 5;
}
```

### Inline vs Normal Function

| Feature | Normal Function | Inline Function |
|---------|----------------|-----------------|
| Execution | Function call + return | Code inserted at call site |
| Overhead | Yes (stack frame) | No |
| Best for | Large/complex functions | Small, simple functions |
| Compiler behavior | Always called | May ignore `inline` request |

> **Interview Point:** `inline` is a **request**, not a command. The compiler can ignore it (e.g., for recursive or complex functions).

---

## 7. Function Overloading

Defining **multiple functions with the same name** but different parameter lists. The compiler selects the right one at **compile time**.

```cpp
int add(int a, int b)
{
    return a + b;
}

float add(float a, float b)
{
    return a + b;
}

int add(int a, int b, int c)
{
    return a + b + c;
}

int main()
{
    cout << add(2, 3);         // Calls int version → 5
    cout << add(2.5f, 3.5f);   // Calls float version → 6.0
    cout << add(1, 2, 3);      // Calls 3-param version → 6
}
```

### Rules for Overloading

- Functions must differ in **number** or **type** of parameters
- Return type alone is **not** enough to overload
- Default arguments can cause **ambiguity** — be careful

```cpp
// This causes ambiguity
int add(int a, int b);
int add(int a, int b = 0);  // ❌ Ambiguous when called as add(3, 4)
```

---

## 8. Recursion

A function that **calls itself** is called recursive. Every recursive function needs:

1. **Base case** — condition to stop recursion
2. **Recursive case** — the function calling itself with a smaller problem

### Example — Factorial

```cpp
int factorial(int n)
{
    if(n == 0 || n == 1)  // Base case
        return 1;
    return n * factorial(n - 1);  // Recursive case
}

int main()
{
    cout << factorial(5);  // Output: 120
}
```

**How it works:**
```
factorial(5)
→ 5 * factorial(4)
→ 5 * 4 * factorial(3)
→ 5 * 4 * 3 * factorial(2)
→ 5 * 4 * 3 * 2 * factorial(1)
→ 5 * 4 * 3 * 2 * 1 = 120
```

### Example — Fibonacci

```cpp
int fibonacci(int n)
{
    if(n <= 1)
        return n;
    return fibonacci(n - 1) + fibonacci(n - 2);
}

int main()
{
    cout << fibonacci(6);  // Output: 8
}
```

### Recursion vs Iteration

| Feature | Recursion | Iteration |
|---------|-----------|-----------|
| Code length | Shorter, elegant | Longer |
| Memory | More (call stack) | Less |
| Speed | Slower (overhead) | Faster |
| Risk | Stack overflow | None |
| Best for | Trees, divide & conquer | Simple loops |

> **Interview Trap:** Recursion without a base case = infinite recursion = **stack overflow**.

> **Interview Point:** A recursive inline function is possible to write but the compiler will likely **ignore** the inline request since it cannot determine recursion depth at compile time.

---

## 9. Scope of Variables

| Scope | Where Declared | Accessible |
|-------|---------------|------------|
| Local | Inside a function | Only in that function |
| Global | Outside all functions | Throughout the program |
| Block | Inside `{}` braces | Only within that block |

```cpp
int globalVar = 100;  // Global

void display()
{
    int localVar = 50;  // Local
    cout << globalVar;  // ✅ Accessible
    cout << localVar;   // ✅ Accessible
}

int main()
{
    cout << globalVar;  // ✅ Accessible
    cout << localVar;   // ❌ Error — not accessible here
}
```

---

## 10. Interview Q&A

**Q1. What is the difference between function overloading and function overriding?**

| Feature | Overloading | Overriding |
|---------|-------------|------------|
| Definition | Same name, different parameters | Same name and signature in derived class |
| Resolved at | Compile time | Runtime |
| Requires inheritance | No | Yes |
| Keyword | None | `virtual` |

---

**Q2. Can we overload a function based on return type alone?**

No. The compiler cannot distinguish functions based only on return type because at the call site, the return type is not always visible.

```cpp
int getValue();
float getValue();  // ❌ Not valid overloading
```

---

**Q3. What happens if there is no base case in recursion?**

The function keeps calling itself indefinitely, filling up the call stack until a **stack overflow** occurs and the program crashes.

---

**Q4. What is the difference between pass by value and pass by reference?**

In pass by value, a copy is made so the original is unaffected. In pass by reference, the actual variable is passed so changes reflect in the caller.

---

**Q5. Is `inline` a guarantee that the function will be inlined?**

No. `inline` is only a **suggestion** to the compiler. The compiler can ignore it, especially for complex or recursive functions.

---

**Q6. What is a recursive inline function?**

It is possible to declare an inline function as recursive, but the compiler will typically **not** inline it, because recursion depth cannot be determined at compile time.

---

**Q7. What are default arguments? What is their rule?**

Default arguments provide a fallback value when no argument is passed. They must always be on the **rightmost** side of the parameter list to avoid ambiguity.

---

## 11. Practice Questions

**Q1. What is the output?**

```cpp
void change(int x)
{
    x = 100;
}

int main()
{
    int a = 10;
    change(a);
    cout << a;
}
```

**Output:** `10`

> `x` is a copy of `a`. The original `a` is unchanged.

---

**Q2. What is the output?**

```cpp
int add(int a, int b = 5)
{
    return a + b;
}

int main()
{
    cout << add(3) << " " << add(3, 7);
}
```

**Output:** `8 10`

> First call uses default `b = 5`, second call uses `b = 7`.

---

**Q3. What is the output?**

```cpp
int fact(int n)
{
    if(n == 1) return 1;
    return n * fact(n - 1);
}

int main()
{
    cout << fact(4);
}
```

**Output:** `24`

> `4 * 3 * 2 * 1 = 24`

---

**Q4. How many times is the function called?**

```cpp
int fib(int n)
{
    if(n <= 1) return n;
    return fib(n - 1) + fib(n - 2);
}

int main()
{
    cout << fib(5);
}
```

**Output:** `5`

> `fib(5)` makes **15 total function calls** — this is why recursive Fibonacci is inefficient for large `n`.

---

**Q5. Will this compile? Why or why not?**

```cpp
int result(int a);
float result(int a);

int main()
{
    result(5);
}
```

**Answer:** No, it will **not compile**. Overloading based solely on return type is not allowed in C++.

---

## Quick Recap

| Concept | Key Point |
|---------|-----------|
| Pass by value | Copy is passed, original safe |
| Pass by reference | Original is modified |
| Default arguments | Must be rightmost in parameter list |
| Inline function | Compiler may ignore the hint |
| Function overloading | Same name, different params, resolved at compile time |
| Recursion | Needs base case; risk of stack overflow |

---

📌 **Next Chapter: Arrays & Strings**
