# 📘 Chapter 8: OOP — Encapsulation & Abstraction in C++
 
> Two of the four pillars of OOP — Encapsulation and Abstraction — are among the most frequently asked theory topics in every fresher and internship interview. Understanding the difference between them is critical.
 
---
 
## Table of Contents
 
- [1. Encapsulation](#1-encapsulation)
- [2. How Encapsulation is Implemented](#2-how-encapsulation-is-implemented)
- [3. Getters & Setters](#3-getters--setters)
- [4. Why Encapsulation Matters](#4-why-encapsulation-matters)
- [5. Abstraction](#5-abstraction)
- [6. How Abstraction is Implemented](#6-how-abstraction-is-implemented)
- [7. Abstract Class & Pure Virtual Function](#7-abstract-class--pure-virtual-function)
- [8. Encapsulation vs Abstraction](#8-encapsulation-vs-abstraction)
- [9. Interview Q&A — Theoretical](#9-interview-qa--theoretical)
- [10. Interview Q&A — Coding](#10-interview-qa--coding)
- [11. Practice Questions](#11-practice-questions)
---
 
## 1. Encapsulation
 
Encapsulation means **bundling data (variables) and the functions that operate on that data together inside a class**, and **restricting direct access** to the data from outside.
 
Think of it like a **capsule (pill)** — everything is packed inside and the outside world only interacts through a defined interface.
 
### Real-world Analogy
 
A **bank account** — you cannot directly go and change your balance in the bank's database. You interact through controlled operations: deposit, withdraw, check balance. The internal data is hidden; only safe, validated operations are exposed.
 
```
Without encapsulation:             With encapsulation:
acc.balance = -5000; ✅ (risky)    acc.withdraw(5000);  ✅ (safe, validated)
```
 
---
 
## 2. How Encapsulation is Implemented
 
Encapsulation in C++ is achieved using:
 
1. Making data members **`private`**
2. Providing **public getter and setter** functions to access/modify them
```cpp
class BankAccount
{
private:
    string owner;
    double balance;    // Hidden from outside
 
public:
    // Constructor
    BankAccount(string name, double initialBalance)
    {
        owner   = name;
        balance = initialBalance;
    }
 
    // Getter — read-only access
    double getBalance()
    {
        return balance;
    }
 
    // Controlled operation — not direct access
    void deposit(double amount)
    {
        if(amount > 0)
            balance += amount;
        else
            cout << "Invalid deposit amount\n";
    }
 
    void withdraw(double amount)
    {
        if(amount > 0 && amount <= balance)
            balance -= amount;
        else
            cout << "Invalid or insufficient funds\n";
    }
 
    void display()
    {
        cout << "Owner: "   << owner   << "\n";
        cout << "Balance: " << balance << "\n";
    }
};
 
int main()
{
    BankAccount acc("Aryan", 1000);
 
    acc.deposit(500);
    acc.withdraw(200);
    acc.display();
 
    // acc.balance = -9999;  // ❌ Error — private, cannot access directly
}
```
 
**Output:**
```
Owner: Aryan
Balance: 1300
```
 
---
 
## 3. Getters & Setters
 
Getters and setters are public functions that provide **controlled access** to private data.
 
```cpp
class Student
{
private:
    string name;
    int age;
    float marks;
 
public:
    // Setters — with validation
    void setName(string n)
    {
        name = n;
    }
 
    void setAge(int a)
    {
        if(a > 0 && a < 150)
            age = a;
        else
            cout << "Invalid age\n";
    }
 
    void setMarks(float m)
    {
        if(m >= 0 && m <= 100)
            marks = m;
        else
            cout << "Invalid marks\n";
    }
 
    // Getters — read-only access
    string getName()  { return name;  }
    int    getAge()   { return age;   }
    float  getMarks() { return marks; }
};
 
int main()
{
    Student s;
    s.setName("Priya");
    s.setAge(20);
    s.setMarks(95.5);
 
    cout << s.getName()  << "\n";  // Priya
    cout << s.getAge()   << "\n";  // 20
    cout << s.getMarks() << "\n";  // 95.5
 
    s.setAge(-5);  // Invalid age
}
```
 
> **Interview Point:** Setters allow **validation** before data is stored. Direct access (`obj.age = -5`) would store invalid data with no checks.
 
---
 
## 4. Why Encapsulation Matters
 
| Benefit | Explanation |
|---------|-------------|
| Data protection | Private data cannot be accidentally or maliciously modified |
| Validation | Setters can check if the value is valid before storing |
| Flexibility | Internal implementation can change without affecting outside code |
| Maintainability | Changes to one class don't break other parts of the program |
| Read-only data | A getter without a setter makes a variable read-only |
 
---
 
## 5. Abstraction
 
Abstraction means **showing only what is necessary** and **hiding the internal details**.
 
The user knows **what** something does, but not **how** it does it.
 
### Real-world Analogies
 
| Example | What you see | What is hidden |
|---------|-------------|----------------|
| TV remote | Press a button to change channel | Complex electronics inside |
| Car | Press accelerator to go faster | Engine, fuel injection, mechanics |
| ATM | Enter PIN and withdraw cash | Banking backend, network, security |
| `cout << "Hello"` | Text appears on screen | OS calls, buffer, I/O driver |
 
---
 
## 6. How Abstraction is Implemented
 
In C++, abstraction is implemented in two ways:
 
### Using Access Specifiers (Simple Abstraction)
 
Hide internal implementation details by making them `private`, exposing only what's needed as `public`.
 
```cpp
class TV
{
private:
    // Internal details — hidden
    void connectToAntenna()  { /* complex logic */ }
    void decodeSignal()      { /* complex logic */ }
    void processAudio()      { /* complex logic */ }
 
public:
    // Simple interface — all user needs to know
    void turnOn()
    {
        connectToAntenna();
        decodeSignal();
        processAudio();
        cout << "TV is ON\n";
    }
 
    void changeChannel(int ch)
    {
        cout << "Channel changed to " << ch << "\n";
    }
};
 
int main()
{
    TV tv;
    tv.turnOn();         // User doesn't know how it works internally
    tv.changeChannel(5);
}
```
 
### Using Abstract Classes (Formal Abstraction)
 
An **abstract class** defines a common interface that all derived classes must follow. Covered in depth next.
 
---
 
## 7. Abstract Class & Pure Virtual Function
 
### Pure Virtual Function
 
A function declared in a base class with `= 0` — it has **no implementation** in the base class. Every derived class **must** provide its own implementation.
 
```cpp
virtual void functionName() = 0;   // Pure virtual function
```
 
### Abstract Class
 
A class that has **at least one pure virtual function** is called an abstract class.
 
- Cannot be instantiated (you cannot create objects of it directly)
- Acts as a contract — forces derived classes to implement the function
- Used to define a common interface for a group of related classes
```cpp
class Shape                        // Abstract class
{
public:
    virtual void draw()  = 0;      // Pure virtual — must be overridden
    virtual float area() = 0;      // Pure virtual — must be overridden
 
    void display()                 // Normal function — can have implementation
    {
        cout << "I am a Shape\n";
    }
};
 
class Circle : public Shape
{
    float radius;
public:
    Circle(float r) { radius = r; }
 
    void draw() override           // Must implement
    {
        cout << "Drawing Circle\n";
    }
 
    float area() override          // Must implement
    {
        return 3.14 * radius * radius;
    }
};
 
class Rectangle : public Shape
{
    float l, w;
public:
    Rectangle(float l, float w) { this->l = l; this->w = w; }
 
    void draw() override
    {
        cout << "Drawing Rectangle\n";
    }
 
    float area() override
    {
        return l * w;
    }
};
 
int main()
{
    // Shape s;           // ❌ Error — cannot instantiate abstract class
 
    Shape* s1 = new Circle(5);
    Shape* s2 = new Rectangle(4, 6);
 
    s1->draw();            // Drawing Circle
    s2->draw();            // Drawing Rectangle
 
    cout << s1->area();    // 78.5
    cout << s2->area();    // 24
 
    delete s1;
    delete s2;
}
```
 
> **Interview Point:** If a derived class does **not** implement all pure virtual functions of the base class, the derived class also becomes abstract and cannot be instantiated.
 
### Abstract Class Rules
 
| Rule | Detail |
|------|--------|
| Cannot create objects | `Shape s;` → compile error |
| Can have pointer/reference | `Shape* ptr = new Circle();` → valid |
| Can have normal functions | Not everything needs to be pure virtual |
| Derived class must implement all | Otherwise derived class is also abstract |
| Used for | Defining a common interface for related classes |
 
---
 
## 8. Encapsulation vs Abstraction
 
This is one of the **most commonly asked** questions in fresher interviews. Many candidates confuse the two.
 
| Feature | Encapsulation | Abstraction |
|---------|--------------|-------------|
| Focus | **How** data is protected | **What** to show to the user |
| Goal | Hiding data from direct access | Hiding implementation complexity |
| Achieved using | `private` + getters/setters | Abstract classes, access specifiers |
| Deals with | Data security | Design simplicity |
| Real analogy | Medicine capsule — ingredients packed inside | TV remote — hides internal circuitry |
| Level | Implementation level | Design level |
 
### Simple way to remember
 
> **Encapsulation** = Wrapping data + protecting it (data hiding)
>
> **Abstraction** = Showing only what's needed (implementation hiding)
 
```cpp
class ATM
{
private:
    int pin;
    double balance;          // Encapsulation — data is hidden
 
    void connectToBank()     // Abstraction — user doesn't know this exists
    {
        /* complex banking logic */
    }
 
public:
    void withdraw(double amount)   // Simple interface exposed to user
    {
        connectToBank();           // Complexity hidden
        balance -= amount;         // Controlled data access
    }
};
```
 
In the example above:
- `pin` and `balance` being `private` = **Encapsulation**
- `connectToBank()` being hidden, `withdraw()` being the simple interface = **Abstraction**
---
 
## 9. Interview Q&A — Theoretical
 
**Q1. What is encapsulation? Give a real-world example.**
 
Encapsulation is the process of bundling data and the functions that operate on it into a single unit (class), and restricting direct access to the data from outside. A real-world example is a bank account — you cannot directly modify the balance; you interact through controlled operations like deposit and withdraw that validate the transaction before modifying the data.
 
---
 
**Q2. What is abstraction? Give a real-world example.**
 
Abstraction means showing only the necessary features of an object and hiding the internal implementation details. A real-world example is a TV remote — you press a button to change the channel without knowing anything about the electronics, signal decoding, or circuitry happening inside the TV. In C++, abstraction is achieved through access specifiers and abstract classes.
 
---
 
**Q3. What is the difference between encapsulation and abstraction?**
 
Encapsulation is about **data hiding** — wrapping data and functions together and protecting data from direct outside access using `private` members and getters/setters. Abstraction is about **implementation hiding** — showing only what the user needs to interact with and hiding the internal complexity. Encapsulation focuses on data security at the implementation level; abstraction focuses on design simplicity at the interface level. Both often work together in the same class.
 
---
 
**Q4. What is an abstract class in C++?**
 
An abstract class is a class that has at least one pure virtual function (declared with `= 0`). It cannot be instantiated directly — you cannot create objects of an abstract class. It serves as a base class that defines a common interface, and all derived classes must implement the pure virtual functions. It is used to enforce a contract across related classes.
 
---
 
**Q5. What is a pure virtual function?**
 
A pure virtual function is a virtual function declared in a base class that has no implementation in that class — it is written as `virtual void func() = 0`. Any class containing a pure virtual function becomes abstract. Derived classes must override and provide an implementation for it, otherwise they too become abstract.
 
---
 
**Q6. Can we create an object of an abstract class?**
 
No. An abstract class has at least one pure virtual function that has no implementation. Creating an object of it would mean calling a function with no body, which is not allowed. The compiler gives a compile-time error if you try to instantiate an abstract class.
 
---
 
**Q7. Can an abstract class have a constructor?**
 
Yes. Even though you cannot create objects of an abstract class directly, it can have a constructor. The constructor is called when a derived class object is created, as part of the initialization chain (base class constructor is always called before derived class constructor).
 
---
 
**Q8. What is the difference between data hiding and abstraction?**
 
Data hiding is specifically about protecting data members from direct outside access — it is part of encapsulation and is done using `private` access specifier. Abstraction is broader — it is about hiding complexity and showing only relevant details. All data hiding contributes to abstraction, but abstraction is not limited to data hiding; it also includes hiding function implementations through abstract classes.
 
---
 
**Q9. Why are getters and setters used instead of making data public?**
 
Making data `public` allows anyone to set any value with no control. Getters and setters provide controlled access — setters can validate the incoming value (e.g., reject negative age), and getters can perform computation or formatting before returning a value. They also allow making a variable read-only (provide getter but no setter), and the internal implementation can change without breaking external code.
 
---
 
**Q10. What happens if a derived class does not implement a pure virtual function?**
 
If a derived class inherits from an abstract class but does not provide an implementation for all pure virtual functions, the derived class also becomes abstract. It cannot be instantiated either, until a further derived class provides all the implementations.
 
---
 
## 10. Interview Q&A — Coding
 
**Q1. What is the output?**
 
```cpp
class Animal
{
public:
    virtual void sound() = 0;   // Pure virtual
    void breathe()
    {
        cout << "Breathing\n";
    }
};
 
class Dog : public Animal
{
public:
    void sound() override
    {
        cout << "Woof\n";
    }
};
 
int main()
{
    Dog d;
    d.sound();
    d.breathe();
}
```
 
**Output:**
```
Woof
Breathing
```
 
> `Dog` implements `sound()` so it's no longer abstract and can be instantiated. `breathe()` is inherited from `Animal`.
 
---
 
**Q2. Find the error.**
 
```cpp
class Vehicle
{
public:
    virtual void fuelType() = 0;
};
 
int main()
{
    Vehicle v;
    v.fuelType();
}
```
 
**Error:** Cannot create an object of abstract class `Vehicle` — it has an unimplemented pure virtual function `fuelType()`.
 
**Fix:** Create a derived class that implements `fuelType()`.
 
```cpp
class Car : public Vehicle
{
public:
    void fuelType() override
    {
        cout << "Petrol\n";
    }
};
 
int main()
{
    Car c;
    c.fuelType();   // Petrol
}
```
 
---
 
**Q3. What is the output?**
 
```cpp
class Person
{
private:
    int age;
 
public:
    void setAge(int a)
    {
        if(a > 0 && a < 120)
            age = a;
        else
            cout << "Invalid\n";
    }
 
    int getAge() { return age; }
};
 
int main()
{
    Person p;
    p.setAge(25);
    cout << p.getAge() << "\n";
    p.setAge(-5);
}
```
 
**Output:**
```
25
Invalid
```
 
> Setter validates the input — negative age is rejected. This is encapsulation in action.
 
---
 
**Q4. Write an abstract class `Shape` with a pure virtual function `area()`. Create two derived classes `Circle` and `Square` that implement it.**
 
```cpp
class Shape
{
public:
    virtual float area() = 0;   // Pure virtual
};
 
class Circle : public Shape
{
    float r;
public:
    Circle(float r) { this->r = r; }
    float area() override { return 3.14f * r * r; }
};
 
class Square : public Shape
{
    float side;
public:
    Square(float s) { side = s; }
    float area() override { return side * side; }
};
 
int main()
{
    Shape* s1 = new Circle(7);
    Shape* s2 = new Square(4);
 
    cout << "Circle area: "  << s1->area() << "\n";  // 153.86
    cout << "Square area: "  << s2->area() << "\n";  // 16
 
    delete s1;
    delete s2;
}
```
 
---
 
**Q5. What is the output?**
 
```cpp
class Base
{
public:
    virtual void show() = 0;
    Base() { cout << "Base constructor\n"; }
};
 
class Derived : public Base
{
public:
    Derived() { cout << "Derived constructor\n"; }
    void show() override { cout << "Derived show\n"; }
};
 
int main()
{
    Derived d;
    d.show();
}
```
 
**Output:**
```
Base constructor
Derived constructor
Derived show
```
 
> Even though `Base` is abstract, its constructor runs first when `Derived` object is created.
 
---
 
## 11. Practice Questions
 
**Theoretical Q1. Is abstraction possible without encapsulation?**
 
They are closely related but technically separate. Abstraction is about the design — defining what operations are visible. Encapsulation is about the implementation — hiding the data. In practice, encapsulation is the mechanism through which abstraction is often achieved in C++, but they address different concerns. You can have a class that hides data (encapsulation) without being an abstract class.
 
---
 
**Theoretical Q2. Can an abstract class have non-virtual functions?**
 
Yes. An abstract class can have regular (non-virtual) member functions with full implementations. Only functions declared with `= 0` are pure virtual. The derived class inherits the non-virtual functions directly.
 
---
 
**Theoretical Q3. What is the difference between a virtual function and a pure virtual function?**
 
| Feature | Virtual Function | Pure Virtual Function |
|---------|-----------------|----------------------|
| Has implementation in base class | ✅ Yes | ❌ No (`= 0`) |
| Must be overridden in derived class | ❌ Optional | ✅ Mandatory |
| Makes class abstract | ❌ No | ✅ Yes |
| Can be called on base class object | ✅ Yes | ❌ No (can't instantiate) |
 
---
 
**Coding Q1. What is the output?**
 
```cpp
class Printer
{
private:
    int inkLevel;
 
public:
    Printer() { inkLevel = 100; }
 
    void print()
    {
        if(inkLevel > 0)
        {
            inkLevel -= 10;
            cout << "Printing... Ink: " << inkLevel << "\n";
        }
        else
            cout << "Out of ink\n";
    }
 
    int getInkLevel() { return inkLevel; }
};
 
int main()
{
    Printer p;
    p.print();
    p.print();
    cout << "Ink left: " << p.getInkLevel();
}
```
 
**Output:**
```
Printing... Ink: 90
Printing... Ink: 80
Ink left: 80
```
 
> `inkLevel` is private — encapsulated. User only interacts through `print()` and `getInkLevel()`.
 
---
 
**Coding Q2. Will this compile? Why or why not?**
 
```cpp
class A
{
public:
    virtual void show() = 0;
};
 
class B : public A
{
public:
    virtual void display() = 0;
};
 
class C : public B
{
public:
    void show()    override { cout << "show\n";    }
    void display() override { cout << "display\n"; }
};
 
int main()
{
    C obj;
    obj.show();
    obj.display();
}
```
 
**Answer:** Yes, it compiles. `B` inherits `show()` as a pure virtual function from `A` and adds its own `display()` pure virtual — so `B` is also abstract. `C` implements both, so `C` is concrete and can be instantiated.
 
**Output:**
```
show
display
```
 
---
 
## Quick Recap
 
| Concept | Key Point |
|---------|-----------|
| Encapsulation | Bundle data + functions; make data `private`; use getters/setters |
| Why encapsulation | Data protection, validation, flexibility, maintainability |
| Abstraction | Show only what's needed; hide internal complexity |
| Why abstraction | Simplifies usage, reduces complexity for the user |
| Getter | Public function to read private data |
| Setter | Public function to write/validate private data |
| Abstract class | Has at least one pure virtual function; cannot be instantiated |
| Pure virtual function | `virtual void func() = 0;` — must be overridden in derived class |
| Encapsulation vs Abstraction | Encapsulation = data hiding; Abstraction = implementation hiding |
| Abstract class constructor | Allowed — called when derived class object is created |
 
---
