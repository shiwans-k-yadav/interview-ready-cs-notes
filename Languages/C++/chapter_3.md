📘 Chapter 3: Functions in C++

Functions are reusable blocks of code used to perform specific tasks.

🔹 1. What is a Function?

A function is a block of code that runs when it is called.

#include <iostream>
using namespace std;

void greet()
{
    cout << "Hello";
}

int main()
{
    greet();
}

👉 Helps in code reuse and modular programming

🔹 2. Function Syntax
return_type function_name(parameters)
{
    // body
}

Example:

int add(int a, int b)
{
    return a + b;
}
🔹 3. Types of Functions
✅ (A) No Return, No Parameters
void show()
{
    cout << "Hello";
}
✅ (B) No Return, With Parameters
void printSum(int a, int b)
{
    cout << a + b;
}
✅ (C) Return, No Parameters
int getNumber()
{
    return 10;
}
✅ (D) Return, With Parameters
int add(int a, int b)
{
    return a + b;
}

👉 Most commonly used in interviews

🔹 4. Function Declaration & Definition
Declaration (Prototype)
int add(int, int);
Definition
int add(int a, int b)
{
    return a + b;
}

👉 Declaration tells compiler about function before use

🔹 5. Function Call
int result = add(5, 3);
cout << result;
🔹 6. Call by Value vs Call by Reference
✅ Call by Value
void change(int x)
{
    x = 10;
}

int main()
{
    int a = 5;
    change(a);
    cout << a;
}

👉 Output: 5 (original value not changed)

✅ Call by Reference
void change(int &x)
{
    x = 10;
}

int main()
{
    int a = 5;
    change(a);
    cout << a;
}

👉 Output: 10 (original value changed)

⚠️ Interview Difference
Type	Effect
Call by Value	Copy passed
Call by Reference	Original variable modified
🔹 7. Default Arguments
int add(int a, int b = 5)
{
    return a + b;
}

int main()
{
    cout << add(10);
}

👉 Output: 15

🔹 8. Inline Functions
inline int square(int x)
{
    return x * x;
}

👉 Used for small functions to reduce function call overhead

🔹 9. Recursion

A function calling itself.

int fact(int n)
{
    if(n == 0)
        return 1;
    return n * fact(n - 1);
}

👉 Must have a base case to stop recursion

⚠️ Interview Trap
Missing base case → infinite recursion
Stack overflow risk
🔹 10. Function Overloading

Same function name with different parameters.

int add(int a, int b)
{
    return a + b;
}

double add(double a, double b)
{
    return a + b;
}

👉 Compile-time polymorphism

⚡ Quick Interview Recap
Concept	Key Point
Function	Reusable code block
Prototype	Declaration before use
Call by Value	Copy
Call by Reference	Original modified ⭐
Default Arguments	Predefined values
Recursion	Function calling itself
Overloading	Same name, different params
🧪 Practice Questions
Q1
void fun(int x)
{
    x = x + 5;
}

int main()
{
    int a = 10;
    fun(a);
    cout << a;
}
<details> <summary>👉 Click to see Output</summary>

Output: 10

</details>
Q2
void fun(int &x)
{
    x = x + 5;
}

int main()
{
    int a = 10;
    fun(a);
    cout << a;
}
<details> <summary>👉 Click to see Output</summary>

Output: 15

</details>
Q3
int fact(int n)
{
    if(n == 0)
        return 1;
    return n * fact(n - 1);
}

int main()
{
    cout << fact(3);
}
<details> <summary>👉 Click to see Output</summary>

Output: 6

</details>

📌 Next Chapter: Arrays & Strings 🚀
