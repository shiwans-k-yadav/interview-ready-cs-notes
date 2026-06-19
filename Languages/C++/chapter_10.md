# 📘 Chapter 10: OOP — Polymorphism in C++

> Polymorphism means "many forms." It allows the **same function or operator** to behave differently based on the object or context — making code flexible, extensible, and easier to maintain.

---

## Table of Contents

- [1. What is Polymorphism?](#1-what-is-polymorphism)
- [2. Types of Polymorphism](#2-types-of-polymorphism)
- [3. Compile-Time Polymorphism — Function Overloading](#3-compile-time-polymorphism--function-overloading)
- [4. Compile-Time Polymorphism — Operator Overloading](#4-compile-time-polymorphism--operator-overloading)
- [5. Runtime Polymorphism — Virtual Functions](#5-runtime-polymorphism--virtual-functions)
- [6. Virtual Functions in Depth](#6-virtual-functions-in-depth)
- [7. Pure Virtual Functions & Abstract Classes](#7-pure-virtual-functions--abstract-classes)
- [8. Virtual Destructor](#8-virtual-destructor)
- [9. vtable and vptr (How Runtime Polymorphism Works)](#9-vtable-and-vptr-how-runtime-polymorphism-works)
- [10. Function Overriding vs Function Overloading](#10-function-overriding-vs-function-overloading)
- [11. The `override` and `final` Keywords](#11-the-override-and-final-keywords)
- [12. Interview Q&A — Theoretical](#12-interview-qa--theoretical)
- [13. Interview Q&A — Coding](#13-interview-qa--coding)
- [14. Practice Questions](#14-practice-questions)

---

## 1. What is Polymorphism?

**Polymorphism** (from Greek: *poly* = many, *morphs* = forms) is the ability of a single entity — a function, operator, or object — to take **multiple forms** depending on the context.

### Why Polymorphism?

| Benefit | Explanation |
|---------|-------------|
| Flexibility | Same interface, different behavior for different types |
| Extensibility | Add new classes without changing existing code |
| Code reuse | Write generic code that works with any derived class |
| Real-world modeling | Objects of the same category can respond differently to the same action |

### Real-world Example

```
Shape (base)
  ├── Circle   → draw() draws a circle
  ├── Square   → draw() draws a square
  └── Triangle → draw() draws a triangle
```

> A single call `shape.draw()` produces different output depending on **which shape object** is being referred to at runtime. This is the essence of polymorphism.

---

## 2. Types of Polymorphism

```
Polymorphism
│
├── Compile-Time (Static Polymorphism)  — resolved at compile time
│       ├── Function Overloading
│       └── Operator Overloading
│
└── Runtime (Dynamic Polymorphism)      — resolved at runtime
        └── Virtual Functions (via base class pointers/references)
```

| Type | Also Called | Resolved At | Mechanism |
|------|-------------|-------------|-----------|
| Compile-Time | Static / Early Binding | Compile time | Function/Operator Overloading |
| Runtime | Dynamic / Late Binding | Runtime | Virtual Functions + Pointers |

---

## 3. Compile-Time Polymorphism — Function Overloading

Multiple functions with the **same name** but **different parameters** in the same class. The compiler decides which version to call at compile time based on the arguments.

```cpp
class Calculator
{
public:
    int add(int a, int b)
    {
        return a + b;
    }

    float add(float a, float b)     // Different parameter types
    {
        return a + b;
    }

    int add(int a, int b, int c)    // Different number of parameters
    {
        return a + b + c;
    }
};

int main()
{
    Calculator calc;

    cout << calc.add(2, 3);           // Calls int version → 5
    cout << calc.add(2.5f, 3.5f);    // Calls float version → 6.0
    cout << calc.add(1, 2, 3);       // Calls 3-param version → 6
}
```

**Output:**
```
5
6
6
```

### Rules for Function Overloading

| Rule | Detail |
|------|--------|
| Same name | Required |
| Different parameters | Required (type, count, or order) |
| Return type alone | ❌ NOT sufficient to overload |
| Same class | Must be in the same scope |

> **Common Mistake:** You cannot overload two functions that differ only in return type. The compiler uses parameter lists, not return types, to distinguish overloaded functions.

```cpp
int getValue()    { return 1; }
float getValue()  { return 1.0f; }   // ❌ Error — ambiguous, same parameters
```

---

## 4. Compile-Time Polymorphism — Operator Overloading

C++ allows you to redefine the behavior of built-in operators (`+`, `-`, `*`, `==`, `<<`, etc.) for user-defined types.

### Syntax

```cpp
return_type operator symbol (parameters)
{
    // custom behavior
}
```

### Example — Overloading `+` for a Complex Number class

```cpp
class Complex
{
public:
    float real, imag;

    Complex(float r = 0, float i = 0) : real(r), imag(i) {}

    // Overload + operator
    Complex operator+(const Complex& other)
    {
        return Complex(real + other.real, imag + other.imag);
    }

    void display()
    {
        cout << real << " + " << imag << "i\n";
    }
};

int main()
{
    Complex c1(2.0, 3.0);
    Complex c2(1.5, 2.5);

    Complex c3 = c1 + c2;   // Calls operator+()
    c3.display();
}
```

**Output:**
```
3.5 + 5.5i
```

### Example — Overloading `==` for comparison

```cpp
class Point
{
public:
    int x, y;
    Point(int x, int y) : x(x), y(y) {}

    bool operator==(const Point& other)
    {
        return (x == other.x && y == other.y);
    }
};

int main()
{
    Point p1(3, 4), p2(3, 4), p3(1, 2);

    cout << (p1 == p2) << "\n";   // 1 (true)
    cout << (p1 == p3) << "\n";   // 0 (false)
}
```

### Operators That Cannot Be Overloaded

| Operator | Name |
|----------|------|
| `::` | Scope resolution |
| `.*` | Pointer-to-member |
| `.` | Member access |
| `?:` | Ternary operator |
| `sizeof` | Size operator |

> **Key Rule:** Overloading does not change the **precedence or associativity** of an operator — only its behavior for user-defined types.

---

## 5. Runtime Polymorphism — Virtual Functions

Runtime polymorphism is achieved when a **base class pointer or reference** is used to call a function, and the correct derived class version is selected at runtime.

### Without `virtual` — Static Binding

```cpp
class Animal
{
public:
    void sound()   // NOT virtual
    {
        cout << "Some animal sound\n";
    }
};

class Dog : public Animal
{
public:
    void sound()
    {
        cout << "Woof!\n";
    }
};

int main()
{
    Animal* ptr = new Dog();
    ptr->sound();    // ❌ Calls Animal::sound() — wrong!
}
```

**Output:**
```
Some animal sound
```

> Even though `ptr` points to a `Dog`, it calls `Animal`'s version because there's no `virtual`. The call is resolved at **compile time** based on pointer type.

### With `virtual` — Dynamic Binding

```cpp
class Animal
{
public:
    virtual void sound()   // virtual keyword added
    {
        cout << "Some animal sound\n";
    }
};

class Dog : public Animal
{
public:
    void sound()
    {
        cout << "Woof!\n";
    }
};

class Cat : public Animal
{
public:
    void sound()
    {
        cout << "Meow!\n";
    }
};

int main()
{
    Animal* ptr;

    Dog d;
    Cat c;

    ptr = &d;
    ptr->sound();    // ✅ Woof! — resolved at runtime

    ptr = &c;
    ptr->sound();    // ✅ Meow! — resolved at runtime
}
```

**Output:**
```
Woof!
Meow!
```

> The `virtual` keyword tells the compiler: *"Don't decide now — wait until runtime to see what object this pointer actually holds."*

---

## 6. Virtual Functions in Depth

### Key Rules for Virtual Functions

| Rule | Detail |
|------|--------|
| Declared with `virtual` | In the base class only |
| Inherited automatically | Derived classes override without repeating `virtual` |
| Must use pointer/reference | Virtual dispatch only works through `Animal*` or `Animal&` |
| Cannot be `static` | Virtual and static are contradictory concepts |
| Can have a body | Unlike pure virtual functions |

### Array of Base Pointers — Power of Polymorphism

```cpp
class Shape
{
public:
    virtual void draw()
    {
        cout << "Drawing Shape\n";
    }
};

class Circle : public Shape
{
public:
    void draw()
    {
        cout << "Drawing Circle\n";
    }
};

class Rectangle : public Shape
{
public:
    void draw()
    {
        cout << "Drawing Rectangle\n";
    }
};

class Triangle : public Shape
{
public:
    void draw()
    {
        cout << "Drawing Triangle\n";
    }
};

int main()
{
    Shape* shapes[3];
    shapes[0] = new Circle();
    shapes[1] = new Rectangle();
    shapes[2] = new Triangle();

    for (int i = 0; i < 3; i++)
    {
        shapes[i]->draw();    // Correct derived version called each time
    }
}
```

**Output:**
```
Drawing Circle
Drawing Rectangle
Drawing Triangle
```

> This is the **real power of polymorphism** — a single loop with a base class pointer, yet each object does its own thing. Adding a new shape requires zero changes to the loop.

---

## 7. Pure Virtual Functions & Abstract Classes

### Pure Virtual Function

A pure virtual function has **no body** in the base class and forces every derived class to provide its own implementation.

```cpp
virtual return_type functionName() = 0;   // = 0 makes it pure virtual
```

### Abstract Class

A class that contains **at least one pure virtual function** is called an **abstract class**. You cannot create objects of an abstract class.

```cpp
class Shape        // Abstract class
{
public:
    virtual void draw()  = 0;      // Pure virtual
    virtual float area() = 0;      // Pure virtual

    void display()                 // Regular function — allowed
    {
        cout << "I am a shape\n";
    }
};

class Circle : public Shape
{
    float radius;

public:
    Circle(float r) : radius(r) {}

    void draw()    // Must override
    {
        cout << "Drawing Circle\n";
    }

    float area()   // Must override
    {
        return 3.14f * radius * radius;
    }
};

class Rectangle : public Shape
{
    float length, width;

public:
    Rectangle(float l, float w) : length(l), width(w) {}

    void draw()
    {
        cout << "Drawing Rectangle\n";
    }

    float area()
    {
        return length * width;
    }
};

int main()
{
    // Shape s;   ❌ Cannot instantiate abstract class

    Shape* s1 = new Circle(5.0f);
    Shape* s2 = new Rectangle(4.0f, 6.0f);

    s1->draw();
    cout << "Area: " << s1->area() << "\n";

    s2->draw();
    cout << "Area: " << s2->area() << "\n";
}
```

**Output:**
```
Drawing Circle
Area: 78.5
Drawing Rectangle
Area: 24
```

### Rules for Abstract Classes

| Rule | Detail |
|------|--------|
| Cannot instantiate | `Shape s;` — ❌ Error |
| Can use pointers/references | `Shape* s = new Circle();` — ✅ |
| Derived must override all | If even one pure virtual is not overridden, derived class also becomes abstract |
| Can have concrete members | Regular functions and data members are fine |

> **Interview Point:** Abstract classes in C++ serve as **interfaces** — they define a contract that all derived classes must fulfill. This is how C++ achieves interface-based design.

---

## 8. Virtual Destructor

If you delete a derived class object through a **base class pointer**, and the base destructor is NOT virtual, the derived class destructor is never called — causing a **memory/resource leak**.

### Problem — Non-Virtual Destructor

```cpp
class Base
{
public:
    Base()  { cout << "Base Constructor\n"; }
    ~Base() { cout << "Base Destructor\n";  }   // NOT virtual
};

class Derived : public Base
{
public:
    Derived()  { cout << "Derived Constructor\n"; }
    ~Derived() { cout << "Derived Destructor\n";  }
};

int main()
{
    Base* ptr = new Derived();
    delete ptr;    // ❌ Only Base destructor called — Derived destructor skipped!
}
```

**Output:**
```
Base Constructor
Derived Constructor
Base Destructor
```

> `Derived Destructor` was never called! If `Derived` allocated memory on the heap, this is a **memory leak**.

### Solution — Virtual Destructor

```cpp
class Base
{
public:
    Base()           { cout << "Base Constructor\n";    }
    virtual ~Base()  { cout << "Base Destructor\n";     }   // virtual!
};

class Derived : public Base
{
public:
    Derived()   { cout << "Derived Constructor\n";  }
    ~Derived()  { cout << "Derived Destructor\n";   }
};

int main()
{
    Base* ptr = new Derived();
    delete ptr;    // ✅ Both destructors called correctly
}
```

**Output:**
```
Base Constructor
Derived Constructor
Derived Destructor
Base Destructor
```

> **Golden Rule:** Whenever a class has **virtual functions**, it should also have a **virtual destructor**. This prevents resource leaks when derived objects are destroyed through base pointers.

---

## 9. vtable and vptr (How Runtime Polymorphism Works)

### Behind the Scenes

When the compiler sees a class with virtual functions, it creates a hidden data structure to enable runtime dispatch.

### vtable (Virtual Table)

A **vtable** is a static table of function pointers created by the compiler — **one per class** with virtual functions. Each entry points to the correct virtual function for that class.

```
Animal vtable:
  [0] → &Animal::sound

Dog vtable:
  [0] → &Dog::sound

Cat vtable:
  [0] → &Cat::sound
```

### vptr (Virtual Pointer)

Every object of a class with virtual functions contains a hidden pointer called `vptr`. It points to the vtable of the object's **actual class** (set at construction time).

```
Dog object:          Cat object:
┌──────────┐         ┌──────────┐
│  vptr ───┼──→ Dog vtable
│  name    │         │  vptr ───┼──→ Cat vtable
└──────────┘         └──────────┘
```

### How a Virtual Call Works

```cpp
Animal* ptr = new Dog();
ptr->sound();
```

1. Compiler looks at `ptr` — type is `Animal*`
2. `sound()` is virtual, so don't bind at compile time
3. At runtime: follow `ptr->vptr` → reaches `Dog`'s vtable
4. Call the function pointer at index 0 → `Dog::sound()`

> **Interview Point:** This lookup costs a tiny bit more than a direct call — one pointer dereference. This is the overhead of runtime polymorphism. For most programs, this cost is completely negligible.

---

## 10. Function Overriding vs Function Overloading

| Feature | Overriding | Overloading |
|---------|-----------|-------------|
| Where | Base and derived class | Same class |
| Function name | Same | Same |
| Parameters | Must be **identical** | Must be **different** |
| Return type | Should be same (or covariant) | Can be anything |
| Resolved at | Runtime (with `virtual`) | Compile time |
| Requires inheritance | ✅ Yes | ❌ No |
| `virtual` needed | ✅ For true polymorphism | ❌ Not applicable |
| Keyword | `override` (optional but recommended) | None |

---

## 11. The `override` and `final` Keywords

### `override` (C++11)

Explicitly tells the compiler: *"I intend to override a base class virtual function."* If the function signature doesn't match, it's a **compile-time error** — catching typos and signature mismatches early.

```cpp
class Animal
{
public:
    virtual void sound() { cout << "Animal sound\n"; }
};

class Dog : public Animal
{
public:
    void sound() override    // ✅ Compiler verifies this matches base class
    {
        cout << "Woof!\n";
    }

    void soud() override     // ❌ Compile error — no 'soud()' in base class
    {
        cout << "Typo!\n";
    }
};
```

> **Best Practice:** Always use `override` when overriding virtual functions. It turns silent bugs (wrong signature, typos) into compile errors.

### `final` (C++11)

- On a **class**: prevents the class from being inherited further.
- On a **virtual function**: prevents the function from being overridden further.

```cpp
class Animal
{
public:
    virtual void sound() { cout << "Animal\n"; }
};

class Dog : public Animal
{
public:
    void sound() override final   // No further override allowed
    {
        cout << "Woof!\n";
    }
};

class Poodle : public Dog
{
public:
    void sound() override   // ❌ Compile error — Dog::sound is final
    {
        cout << "Poodle!\n";
    }
};
```

```cpp
class Base final     // Cannot be inherited
{
public:
    void greet() { cout << "Hello\n"; }
};

class Child : public Base   // ❌ Compile error — Base is final
{
};
```

---

## 12. Interview Q&A — Theoretical

**Q1. What is polymorphism? What are its types?**

Polymorphism means "many forms" — the ability of the same function, operator, or object to behave differently based on context. C++ supports two types: compile-time polymorphism (function overloading and operator overloading, resolved at compile time) and runtime polymorphism (virtual functions via base class pointers, resolved at runtime).

---

**Q2. What is the difference between compile-time and runtime polymorphism?**

Compile-time polymorphism (static binding) is resolved at compile time. The compiler knows which function to call based on the argument types or the pointer type. Runtime polymorphism (dynamic binding) is resolved at runtime. The actual function called depends on the type of the object the pointer points to, not the pointer's type. Runtime polymorphism requires `virtual` functions and base class pointers or references.

---

**Q3. What is a virtual function? Why is it used?**

A virtual function is a member function declared in the base class with the `virtual` keyword. It tells the compiler to use dynamic dispatch — defer the function call resolution to runtime. It is used to achieve runtime polymorphism so that the correct derived class version of a function is called through a base class pointer, even without knowing the actual object type at compile time.

---

**Q4. What is an abstract class? How is it different from a regular class?**

An abstract class contains at least one pure virtual function (declared with `= 0`). Unlike a regular class, you cannot create objects of an abstract class. It acts as a blueprint or interface that all derived classes must fulfill by overriding the pure virtual functions. A regular class has full implementations and can be instantiated directly.

---

**Q5. What is a pure virtual function?**

A pure virtual function is a virtual function with no body in the base class, declared by appending `= 0` to its declaration. It makes the class abstract and forces any concrete (non-abstract) derived class to provide an implementation. If a derived class does not override all pure virtual functions, it also becomes abstract and cannot be instantiated.

---

**Q6. Why should destructors be virtual in a base class?**

When a derived class object is deleted through a base class pointer, without a virtual destructor, only the base class destructor is called. The derived class destructor is never invoked, which can cause memory leaks or resource corruption if the derived class allocated resources. Making the base class destructor `virtual` ensures the correct destructor chain is called (derived first, then base) regardless of which pointer type is used.

---

**Q7. What is a vtable and a vptr?**

A vtable (virtual table) is a static lookup table of function pointers created by the compiler for each class that has virtual functions. Each entry points to the most-derived override of that virtual function. A vptr (virtual pointer) is a hidden pointer added to every object of such a class at runtime. It points to the vtable of the object's actual class, enabling the correct function to be called at runtime regardless of the pointer type used to access the object.

---

**Q8. What is the difference between function overloading and operator overloading?**

Function overloading means defining multiple functions with the same name but different parameter lists in the same class. Operator overloading means redefining the behavior of a built-in operator (such as `+`, `==`, `<<`) for a user-defined type. Both are forms of compile-time polymorphism, but they apply to functions and operators respectively.

---

**Q9. Can a constructor be virtual in C++?**

No. Constructors cannot be virtual. When a constructor runs, the object is being created for the first time and the vtable is not yet fully set up, making virtual dispatch impossible. Also, virtual functions are designed for when you have a base class pointer pointing to a derived object — but constructors are always called on the exact type being created. Destructors, however, should often be virtual.

---

**Q10. What is the `override` keyword and why should you use it?**

The `override` keyword (introduced in C++11) explicitly states that a function in a derived class is intended to override a virtual function in the base class. The compiler verifies the signature matches. If there is no matching virtual function in the base class (due to a typo or wrong signature), it produces a compile-time error. This catches bugs that would otherwise silently create a new unrelated function instead of overriding.

---

## 13. Interview Q&A — Coding

**Q1. What is the output?**

```cpp
class Base
{
public:
    void show()          { cout << "Base show\n"; }
    virtual void print() { cout << "Base print\n"; }
};

class Derived : public Base
{
public:
    void show()  { cout << "Derived show\n"; }
    void print() { cout << "Derived print\n"; }
};

int main()
{
    Base* ptr = new Derived();
    ptr->show();    // Non-virtual
    ptr->print();   // Virtual
}
```

**Output:**
```
Base show
Derived print
```

> `show()` is not virtual — call is resolved at compile time using pointer type (`Base*`) → `Base::show()`. `print()` is virtual — resolved at runtime using the actual object (`Derived`) → `Derived::print()`.

---

**Q2. What is the output?**

```cpp
class A
{
public:
    virtual void hello() { cout << "Hello from A\n"; }
};

class B : public A
{
public:
    void hello() override { cout << "Hello from B\n"; }
};

class C : public B
{
public:
    void hello() override { cout << "Hello from C\n"; }
};

int main()
{
    A* ptr = new C();
    ptr->hello();
}
```

**Output:**
```
Hello from C
```

> Virtual dispatch always resolves to the **most-derived override**. Even though `ptr` is of type `A*` and `C` is two levels down, the vtable leads directly to `C::hello()`.

---

**Q3. Find the error.**

```cpp
class Shape
{
public:
    virtual void draw() = 0;
};

class Circle : public Shape
{
    // draw() not overridden
};

int main()
{
    Circle c;   // ?
    c.draw();
}
```

**Error:** `Circle` does not override `draw()`, so it is still an abstract class. `Circle c;` causes a compile-time error — you cannot instantiate an abstract class.

**Fix:** Override `draw()` in `Circle`:

```cpp
class Circle : public Shape
{
public:
    void draw() override
    {
        cout << "Drawing Circle\n";
    }
};
```

---

**Q4. What is the output and why?**

```cpp
class Animal
{
public:
    ~Animal() { cout << "Animal Destructor\n"; }   // NOT virtual
};

class Dog : public Animal
{
public:
    ~Dog() { cout << "Dog Destructor\n"; }
};

int main()
{
    Animal* ptr = new Dog();
    delete ptr;
}
```

**Output:**
```
Animal Destructor
```

> `Dog Destructor` is never called because `Animal`'s destructor is not virtual. This is a **resource leak** if `Dog` owns heap memory. Fix: make `Animal`'s destructor `virtual`.

---

**Q5. Write an abstract class `Employee` with a pure virtual function `calculatePay()`. Derive `FullTimeEmployee` (fixed salary) and `PartTimeEmployee` (hourly rate × hours worked). Call `calculatePay()` through a base pointer.**

```cpp
class Employee
{
protected:
    string name;

public:
    Employee(string n) : name(n) {}

    virtual void calculatePay() = 0;   // Pure virtual

    virtual ~Employee() {}
};

class FullTimeEmployee : public Employee
{
    float salary;

public:
    FullTimeEmployee(string n, float s) : Employee(n), salary(s) {}

    void calculatePay() override
    {
        cout << name << " (Full-Time) Pay: Rs " << salary << "\n";
    }
};

class PartTimeEmployee : public Employee
{
    float hourlyRate;
    int hoursWorked;

public:
    PartTimeEmployee(string n, float rate, int hours)
        : Employee(n), hourlyRate(rate), hoursWorked(hours) {}

    void calculatePay() override
    {
        cout << name << " (Part-Time) Pay: Rs "
             << hourlyRate * hoursWorked << "\n";
    }
};

int main()
{
    Employee* emp[2];
    emp[0] = new FullTimeEmployee("Aryan", 50000);
    emp[1] = new PartTimeEmployee("Priya", 200, 80);

    for (int i = 0; i < 2; i++)
    {
        emp[i]->calculatePay();
    }

    for (int i = 0; i < 2; i++)
    {
        delete emp[i];
    }
}
```

**Output:**
```
Aryan (Full-Time) Pay: Rs 50000
Priya (Part-Time) Pay: Rs 16000
```

---

## 14. Practice Questions

**Theoretical Q1. Can you overload the `=` operator? What about `sizeof`?**

Yes, the assignment operator `=` can be overloaded (in fact, a default one is provided by the compiler if you don't). However, `sizeof`, `::`, `.`, `.*`, and `?:` cannot be overloaded in C++.

---

**Theoretical Q2. What happens if a derived class does not override a pure virtual function?**

The derived class also becomes an abstract class and cannot be instantiated. You must override **all** pure virtual functions from all base classes in the inheritance chain before an object can be created.

---

**Theoretical Q3. Is it possible to have a virtual function in a class without a vtable?**

No. As soon as a class has at least one virtual function, the compiler creates a vtable for that class and adds a hidden vptr to every object. There is no way to have virtual dispatch without this mechanism in standard C++.

---

**Coding Q1. What is the output?**

```cpp
class Base
{
public:
    virtual void show() { cout << "Base\n"; }
};

class Derived : public Base
{
public:
    void show() override { cout << "Derived\n"; }
};

void callShow(Base obj)    // Passed by value — not by pointer!
{
    obj.show();
}

int main()
{
    Derived d;
    callShow(d);
}
```

**Output:**
```
Base
```

> Polymorphism requires a **pointer or reference**. When `d` is passed by value, it is **sliced** — the `Derived` part is lost and a fresh `Base` object is created. `obj.show()` calls `Base::show()`. This is the **object slicing** problem.

---

**Coding Q2. What is the output?**

```cpp
class X
{
public:
    virtual void func() { cout << "X\n"; }
};

class Y : public X
{
public:
    void func() override  { cout << "Y\n"; }
};

class Z : public Y
{
    // func() NOT overridden in Z
};

int main()
{
    X* ptr = new Z();
    ptr->func();
}
```

**Output:**
```
Y
```

> `Z` does not override `func()`, so it inherits `Y`'s version. The vtable for `Z` still points to `Y::func()` — the most-derived override in the hierarchy.

---

**Coding Q3. What is the output?**

```cpp
class Animal
{
public:
    virtual void sound() { cout << "Animal\n"; }
    void breathe()       { cout << "Breathing\n"; }
};

class Dog : public Animal
{
public:
    void sound() override { cout << "Woof\n"; }
    void breathe()        { cout << "Dog Breathing\n"; }
};

int main()
{
    Animal* a = new Dog();
    a->sound();      // virtual
    a->breathe();    // non-virtual
}
```

**Output:**
```
Woof
Breathing
```

> `sound()` is virtual → runtime dispatch → `Dog::sound()`. `breathe()` is not virtual → compile-time dispatch using pointer type (`Animal*`) → `Animal::breathe()`.

---

## Quick Recap

| Concept | Key Point |
|---------|-----------|
| Polymorphism | Same interface, different behavior |
| Compile-time | Resolved at compile time; function/operator overloading |
| Runtime | Resolved at runtime; virtual functions via pointers |
| Function overloading | Same name, different parameters, same class |
| Operator overloading | Redefine built-in operators for user-defined types |
| `virtual` function | Enables dynamic dispatch through base class pointer |
| Abstract class | Has at least one pure virtual function; cannot be instantiated |
| Pure virtual `= 0` | No body in base; derived must override |
| Virtual destructor | Prevents resource leaks when deleting via base pointer |
| vtable | Per-class table of function pointers for virtual dispatch |
| vptr | Per-object hidden pointer to the vtable |
| `override` keyword | Compile-time check that a function truly overrides a base virtual |
| `final` keyword | Prevents further inheritance (class) or overriding (function) |
| Object slicing | Passing derived by value to base parameter loses derived data |

---
