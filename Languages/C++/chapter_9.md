# 📘 Chapter 9: OOP — Inheritance in C++
 
> Inheritance is one of the most powerful features of OOP. It allows a class to acquire the properties and behaviors of another class — promoting code reuse, reducing redundancy, and building logical class hierarchies.
 
---
 
## Table of Contents
 
- [1. What is Inheritance?](#1-what-is-inheritance)
- [2. Basic Syntax](#2-basic-syntax)
- [3. Access Specifiers in Inheritance](#3-access-specifiers-in-inheritance)
- [4. Types of Inheritance](#4-types-of-inheritance)
- [5. Single Inheritance](#5-single-inheritance)
- [6. Multilevel Inheritance](#6-multilevel-inheritance)
- [7. Multiple Inheritance](#7-multiple-inheritance)
- [8. Hierarchical Inheritance](#8-hierarchical-inheritance)
- [9. Hybrid Inheritance](#9-hybrid-inheritance)
- [10. Constructor & Destructor in Inheritance](#10-constructor--destructor-in-inheritance)
- [11. Function Overriding](#11-function-overriding)
- [12. The Diamond Problem](#12-the-diamond-problem)
- [13. Interview Q&A — Theoretical](#13-interview-qa--theoretical)
- [14. Interview Q&A — Coding](#14-interview-qa--coding)
- [15. Practice Questions](#15-practice-questions)
---
 
## 1. What is Inheritance?
 
Inheritance is the mechanism by which a **derived class (child)** acquires the properties and behaviors of a **base class (parent)**.
 
### Why Inheritance?
 
| Benefit | Explanation |
|---------|-------------|
| Code reusability | Write once in base class, reuse in all derived classes |
| Reduces redundancy | No need to rewrite common code |
| Extensibility | Derived class can add new features on top of base class |
| Logical hierarchy | Models real-world "is-a" relationships |
 
### Real-world Example
 
```
Animal (base)
  ├── Dog   (derived) — is-a Animal
  ├── Cat   (derived) — is-a Animal
  └── Bird  (derived) — is-a Animal
```
 
> **"is-a" rule:** Use inheritance only when the derived class "is a" type of the base class. A `Dog` is an `Animal` ✅. A `Car` is a `Vehicle` ✅. An `Engine` is NOT a `Car` ❌ — that's composition.
 
---
 
## 2. Basic Syntax
 
```cpp
class BaseClass
{
    // base class members
};
 
class DerivedClass : access_specifier BaseClass
{
    // derived class members
};
```
 
```cpp
class Animal
{
public:
    string name;
 
    void eat()
    {
        cout << "Eating...\n";
    }
 
    void sleep()
    {
        cout << "Sleeping...\n";
    }
};
 
class Dog : public Animal       // Dog inherits from Animal
{
public:
    void bark()
    {
        cout << "Barking...\n";
    }
};
 
int main()
{
    Dog d;
    d.name  = "Bruno";
    d.eat();    // Inherited from Animal ✅
    d.sleep();  // Inherited from Animal ✅
    d.bark();   // Own function ✅
}
```
 
**Output:**
```
Eating...
Sleeping...
Barking...
```
 
---
 
## 3. Access Specifiers in Inheritance
 
The access specifier used during inheritance controls how base class members are accessible in the derived class.
 
| Base Member | `public` Inheritance | `protected` Inheritance | `private` Inheritance |
|-------------|---------------------|------------------------|-----------------------|
| `public` | `public` | `protected` | `private` |
| `protected` | `protected` | `protected` | `private` |
| `private` | ❌ Not accessible | ❌ Not accessible | ❌ Not accessible |
 
> **Most commonly used:** `public` inheritance — the "is-a" relationship. `private` inheritance means "implemented-in-terms-of" and is rarely used.
 
> **Key Rule:** Private members of the base class are **never** directly accessible in the derived class, regardless of the inheritance type. Use `protected` if you want derived classes to access them.
 
```cpp
class Base
{
public:    int x;      // Accessible in derived class
protected: int y;      // Accessible in derived class
private:   int z;      // NOT accessible in derived class
};
 
class Derived : public Base
{
    void show()
    {
        cout << x;   // ✅ public → still public
        cout << y;   // ✅ protected → still protected
        cout << z;   // ❌ private → inaccessible
    }
};
```
 
---
 
## 4. Types of Inheritance
 
C++ supports **5 types** of inheritance:
 
```
1. Single          → One base, one derived
2. Multilevel      → Chain: A → B → C
3. Multiple        → One derived, multiple bases
4. Hierarchical    → One base, multiple derived
5. Hybrid          → Combination of two or more types
```
 
---
 
## 5. Single Inheritance
 
One derived class inherits from **one base class**.
 
```
  Vehicle
     │
    Car
```
 
```cpp
class Vehicle
{
public:
    string brand;
    int speed;
 
    void showInfo()
    {
        cout << "Brand: " << brand << ", Speed: " << speed << "\n";
    }
};
 
class Car : public Vehicle
{
public:
    int doors;
 
    void showDoors()
    {
        cout << "Doors: " << doors << "\n";
    }
};
 
int main()
{
    Car c;
    c.brand = "Toyota";
    c.speed = 180;
    c.doors = 4;
 
    c.showInfo();   // Inherited
    c.showDoors();  // Own
}
```
 
**Output:**
```
Brand: Toyota, Speed: 180
Doors: 4
```
 
---
 
## 6. Multilevel Inheritance
 
A class inherits from a derived class — forming a **chain**.
 
```
  Animal
     │
   Mammal
     │
    Dog
```
 
```cpp
class Animal
{
public:
    void eat() { cout << "Eating\n"; }
};
 
class Mammal : public Animal
{
public:
    void breathe() { cout << "Breathing\n"; }
};
 
class Dog : public Mammal
{
public:
    void bark() { cout << "Barking\n"; }
};
 
int main()
{
    Dog d;
    d.eat();     // From Animal   (2 levels up)
    d.breathe(); // From Mammal   (1 level up)
    d.bark();    // Own function
}
```
 
**Output:**
```
Eating
Breathing
Barking
```
 
> **Interview Point:** Inheritance is **transitive** — `Dog` gets everything from both `Mammal` and `Animal`.
 
---
 
## 7. Multiple Inheritance
 
A derived class inherits from **more than one base class**.
 
```
  Father    Mother
      \     /
       Child
```
 
```cpp
class Father
{
public:
    void height() { cout << "Tall height from Father\n"; }
};
 
class Mother
{
public:
    void intelligence() { cout << "High intelligence from Mother\n"; }
};
 
class Child : public Father, public Mother
{
public:
    void skill() { cout << "Good at sports\n"; }
};
 
int main()
{
    Child c;
    c.height();        // From Father
    c.intelligence();  // From Mother
    c.skill();         // Own
}
```
 
**Output:**
```
Tall height from Father
High intelligence from Mother
Good at sports
```
 
> **Interview Trap:** Multiple inheritance can cause the **Diamond Problem** (covered in section 12).
 
---
 
## 8. Hierarchical Inheritance
 
**Multiple derived classes** inherit from a **single base class**.
 
```
       Animal
      /   |   \
    Dog  Cat  Bird
```
 
```cpp
class Animal
{
public:
    void eat()   { cout << "Eating\n";   }
    void sleep() { cout << "Sleeping\n"; }
};
 
class Dog : public Animal
{
public:
    void bark() { cout << "Barking\n"; }
};
 
class Cat : public Animal
{
public:
    void meow() { cout << "Meowing\n"; }
};
 
class Bird : public Animal
{
public:
    void fly() { cout << "Flying\n"; }
};
 
int main()
{
    Dog d;  d.eat(); d.bark();
    Cat c;  c.eat(); c.meow();
    Bird b; b.eat(); b.fly();
}
```
 
---
 
## 9. Hybrid Inheritance
 
A **combination of two or more** types of inheritance. Often involves hierarchical + multiple inheritance.
 
```
        Animal
       /      \
     Dog      Cat
       \      /
        Hybrid
```
 
```cpp
class Animal
{
public:
    void eat() { cout << "Eating\n"; }
};
 
class Dog : public Animal
{
public:
    void bark() { cout << "Barking\n"; }
};
 
class Cat : public Animal
{
public:
    void meow() { cout << "Meowing\n"; }
};
 
class Hybrid : public Dog, public Cat  // Multiple + Hierarchical
{
public:
    void show() { cout << "Hybrid animal\n"; }
};
```
 
> **Note:** Hybrid inheritance involving the same base class at multiple paths causes the **Diamond Problem**. Solved using `virtual` inheritance (covered in section 12).
 
---
 
## 10. Constructor & Destructor in Inheritance
 
### Constructor Order
 
When a derived class object is created, the **base class constructor runs first**, then the derived class constructor.
 
```cpp
class Base
{
public:
    Base()  { cout << "Base Constructor\n";  }
    ~Base() { cout << "Base Destructor\n";   }
};
 
class Derived : public Base
{
public:
    Derived()  { cout << "Derived Constructor\n";  }
    ~Derived() { cout << "Derived Destructor\n";   }
};
 
int main()
{
    Derived d;
}
```
 
**Output:**
```
Base Constructor
Derived Constructor
Derived Destructor
Base Destructor
```
 
> **Interview Point:** Constructors run **top to bottom** (base first). Destructors run **bottom to top** (derived first) — exact reverse.
 
---
 
### Passing Arguments to Base Constructor
 
Use an **initializer list** to pass arguments to the base class constructor.
 
```cpp
class Animal
{
public:
    string name;
    Animal(string n) { name = n; cout << "Animal: " << name << "\n"; }
};
 
class Dog : public Animal
{
public:
    string breed;
 
    Dog(string n, string b) : Animal(n)   // Pass n to Animal's constructor
    {
        breed = b;
        cout << "Dog breed: " << breed << "\n";
    }
};
 
int main()
{
    Dog d("Bruno", "Labrador");
}
```
 
**Output:**
```
Animal: Bruno
Dog breed: Labrador
```
 
---
 
## 11. Function Overriding
 
When a derived class defines a function with the **same name and signature** as a base class function, it **overrides** the base class version.
 
```cpp
class Animal
{
public:
    void sound()
    {
        cout << "Some animal sound\n";
    }
};
 
class Dog : public Animal
{
public:
    void sound()          // Overrides Animal's sound()
    {
        cout << "Woof!\n";
    }
};
 
class Cat : public Animal
{
public:
    void sound()          // Overrides Animal's sound()
    {
        cout << "Meow!\n";
    }
};
 
int main()
{
    Animal a;  a.sound();   // Some animal sound
    Dog d;     d.sound();   // Woof!
    Cat c;     c.sound();   // Meow!
}
```
 
### Calling Base Class Function from Derived Class
 
Use the scope resolution operator `::` to call the base version explicitly.
 
```cpp
class Dog : public Animal
{
public:
    void sound()
    {
        Animal::sound();    // Calls base class version
        cout << "Woof!\n";
    }
};
```
 
### Function Overriding vs Function Overloading
 
| Feature | Overriding | Overloading |
|---------|-----------|-------------|
| Where | Base and derived class | Same class |
| Signature | Must be **same** | Must be **different** |
| Resolved at | Runtime (with `virtual`) | Compile time |
| Requires inheritance | ✅ Yes | ❌ No |
 
---
 
## 12. The Diamond Problem
 
The diamond problem occurs in **multiple inheritance** when two parent classes inherit from the same grandparent, causing **ambiguity** — the derived class gets **two copies** of the grandparent.
 
```
       A
      / \
     B   C
      \ /
       D
```
 
```cpp
class A
{
public:
    void show() { cout << "Class A\n"; }
};
 
class B : public A {};
class C : public A {};
 
class D : public B, public C {};
 
int main()
{
    D obj;
    obj.show();   // ❌ Error — ambiguous: B::A::show() or C::A::show()?
}
```
 
### Solution — Virtual Inheritance
 
Use `virtual` keyword when `B` and `C` inherit from `A`. This ensures only **one copy** of `A` exists in `D`.
 
```cpp
class A
{
public:
    void show() { cout << "Class A\n"; }
};
 
class B : virtual public A {};   // virtual inheritance
class C : virtual public A {};   // virtual inheritance
 
class D : public B, public C {};
 
int main()
{
    D obj;
    obj.show();   // ✅ No ambiguity — only one copy of A
}
```
 
**Output:**
```
Class A
```
 
> **Interview Point:** The diamond problem is the classic argument against multiple inheritance. Virtual inheritance solves it but adds complexity — this is one reason some languages (like Java) don't support multiple inheritance of classes at all.
 
---
 
## 13. Interview Q&A — Theoretical
 
**Q1. What is inheritance? Why is it used?**
 
Inheritance is an OOP mechanism where a derived class acquires the properties and behaviors of a base class. It is used to promote code reusability — common code is written once in the base class and reused across multiple derived classes. It also models real-world "is-a" relationships and enables building logical class hierarchies.
 
---
 
**Q2. What are the types of inheritance in C++?**
 
C++ supports five types: Single (one base, one derived), Multilevel (chain of inheritance), Multiple (one derived from multiple bases), Hierarchical (multiple derived from one base), and Hybrid (combination of two or more types).
 
---
 
**Q3. What is the difference between function overloading and function overriding?**
 
Function overloading means multiple functions in the **same class** with the same name but different parameters — resolved at compile time. Function overriding means a derived class defines a function with the **same name and signature** as the base class — resolved at runtime when used with `virtual`.
 
---
 
**Q4. What is the order of constructor and destructor calls in inheritance?**
 
Constructors are called from **base to derived** — the base class constructor runs first. Destructors are called in **reverse order**, from derived to base — the derived class destructor runs first. This is the LIFO principle applied to the class hierarchy.
 
---
 
**Q5. Can a derived class access private members of the base class?**
 
No. Private members of the base class are not directly accessible in the derived class, regardless of the type of inheritance. If a base class member needs to be accessible in derived classes but not from outside, it should be declared `protected`.
 
---
 
**Q6. What is the diamond problem in C++?**
 
The diamond problem occurs in multiple inheritance when two parent classes (B and C) both inherit from a common grandparent (A), and a class D inherits from both B and C. D ends up with two copies of A, causing ambiguity when accessing A's members. It is solved using virtual inheritance — declaring B and C as `virtual public A` ensures only one shared copy of A exists in D.
 
---
 
**Q7. What is virtual inheritance?**
 
Virtual inheritance is a C++ mechanism used to solve the diamond problem. When a class is inherited with the `virtual` keyword, only one instance of that base class exists in the entire inheritance hierarchy, regardless of how many paths lead to it. This eliminates ambiguity in multiple inheritance scenarios.
 
---
 
**Q8. What is the difference between `public`, `protected`, and `private` inheritance?**
 
In `public` inheritance, public members of the base remain public and protected remain protected in the derived class — this is the standard "is-a" relationship. In `protected` inheritance, both public and protected base members become protected in the derived class. In `private` inheritance, all base members become private in the derived class. Private members are never accessible in any case.
 
---
 
**Q9. When should you use inheritance vs composition?**
 
Use inheritance for "is-a" relationships — a `Dog` is an `Animal`. Use composition for "has-a" relationships — a `Car` has an `Engine`. Composition is generally preferred when in doubt, as it leads to more flexible and loosely coupled designs. Inheritance creates tight coupling between base and derived classes.
 
---
 
**Q10. Can a derived class override a non-virtual function?**
 
Yes, but the behavior depends on how the function is called. If called through a base class pointer, the base version runs (no polymorphism). If called through a derived class object directly, the derived version runs. True runtime polymorphism only works with `virtual` functions — covered in Chapter 10.
 
---
 
## 14. Interview Q&A — Coding
 
**Q1. What is the output?**
 
```cpp
class A
{
public:
    A() { cout << "A Constructor\n"; }
    ~A() { cout << "A Destructor\n"; }
};
 
class B : public A
{
public:
    B() { cout << "B Constructor\n"; }
    ~B() { cout << "B Destructor\n"; }
};
 
class C : public B
{
public:
    C() { cout << "C Constructor\n"; }
    ~C() { cout << "C Destructor\n"; }
};
 
int main()
{
    C obj;
}
```
 
**Output:**
```
A Constructor
B Constructor
C Constructor
C Destructor
B Destructor
A Destructor
```
 
> Constructors: top to bottom. Destructors: bottom to top.
 
---
 
**Q2. What is the output?**
 
```cpp
class Animal
{
public:
    void sound() { cout << "Animal sound\n"; }
};
 
class Dog : public Animal
{
public:
    void sound() { cout << "Woof\n"; }
};
 
int main()
{
    Dog d;
    d.sound();           // Calls Dog's version
    d.Animal::sound();   // Calls Animal's version explicitly
}
```
 
**Output:**
```
Woof
Animal sound
```
 
---
 
**Q3. Find the error.**
 
```cpp
class Base
{
private:
    int secret;
 
public:
    Base() { secret = 42; }
};
 
class Derived : public Base
{
public:
    void show()
    {
        cout << secret;   // ?
    }
};
```
 
**Error:** `secret` is `private` in `Base` — not accessible in `Derived`. Even with `public` inheritance, private members remain inaccessible.
 
**Fix:** Change `secret` to `protected` in `Base`.
 
```cpp
protected:
    int secret;
```
 
---
 
**Q4. What is the output?**
 
```cpp
class Shape
{
public:
    void draw() { cout << "Drawing Shape\n"; }
};
 
class Circle : public Shape
{
public:
    void draw() { cout << "Drawing Circle\n"; }
};
 
class Square : public Shape
{
public:
    void draw() { cout << "Drawing Square\n"; }
};
 
int main()
{
    Shape s;    s.draw();
    Circle c;   c.draw();
    Square sq;  sq.draw();
}
```
 
**Output:**
```
Drawing Shape
Drawing Circle
Drawing Square
```
 
---
 
**Q5. Write a class hierarchy: `Person` as base with name and age. `Student` derived from `Person` with marks. `Employee` derived from `Person` with salary. Each should have a `display()` function.**
 
```cpp
class Person
{
protected:
    string name;
    int age;
 
public:
    Person(string n, int a) : name(n), age(a) {}
 
    void display()
    {
        cout << "Name: " << name << ", Age: " << age << "\n";
    }
};
 
class Student : public Person
{
    float marks;
 
public:
    Student(string n, int a, float m) : Person(n, a), marks(m) {}
 
    void display()
    {
        Person::display();
        cout << "Marks: " << marks << "\n";
    }
};
 
class Employee : public Person
{
    float salary;
 
public:
    Employee(string n, int a, float s) : Person(n, a), salary(s) {}
 
    void display()
    {
        Person::display();
        cout << "Salary: " << salary << "\n";
    }
};
 
int main()
{
    Student s("Aryan", 20, 92.5);
    Employee e("Priya", 28, 75000);
 
    s.display();
    cout << "---\n";
    e.display();
}
```
 
**Output:**
```
Name: Aryan, Age: 20
Marks: 92.5
---
Name: Priya, Age: 28
Salary: 75000
```
 
---
 
## 15. Practice Questions
 
**Theoretical Q1. Can a class inherit from itself?**
 
No. A class cannot inherit from itself — it causes a compile-time error and doesn't make logical sense. Inheritance requires two distinct classes.
 
---
 
**Theoretical Q2. What is the difference between multilevel and multiple inheritance?**
 
In multilevel inheritance, a class inherits from a derived class forming a chain (A → B → C). In multiple inheritance, a single class inherits from two or more base classes simultaneously (A and B → C). Multilevel models a "chain of is-a" relationships; multiple models a class that is a combination of two different types.
 
---
 
**Theoretical Q3. Is constructor inheritance automatic in C++?**
 
No. Constructors are not inherited automatically. Each class must define its own constructor. However, in C++11 and later, you can use `using BaseClass::BaseClass;` in the derived class to inherit constructors explicitly.
 
---
 
**Coding Q1. What is the output?**
 
```cpp
class X
{
public:
    X() { cout << "X\n"; }
};
 
class Y : public X
{
public:
    Y() { cout << "Y\n"; }
};
 
class Z : public Y
{
public:
    Z() { cout << "Z\n"; }
};
 
int main()
{
    Z obj;
}
```
 
**Output:**
```
X
Y
Z
```
 
> Multilevel inheritance — constructors fire from the topmost base down to the most derived.
 
---
 
**Coding Q2. What is the output?**
 
```cpp
class A
{
public:
    void greet() { cout << "Hello from A\n"; }
};
 
class B : public A
{
public:
    void greet() { cout << "Hello from B\n"; }
};
 
int main()
{
    B obj;
    obj.greet();     // Which greet?
    obj.A::greet();  // Force base version
}
```
 
**Output:**
```
Hello from B
Hello from A
```
 
---
 
**Coding Q3. What is the output?**
 
```cpp
class Base
{
public:
    int x;
    Base(int val) : x(val) {}
};
 
class Derived : public Base
{
public:
    int y;
    Derived(int a, int b) : Base(a), y(b) {}
 
    void show()
    {
        cout << "x=" << x << " y=" << y << "\n";
    }
};
 
int main()
{
    Derived d(10, 20);
    d.show();
}
```
 
**Output:** `x=10 y=20`
 
> `Base(a)` in the initializer list passes `a` to the base class constructor setting `x = 10`.
 
---
 
## Quick Recap
 
| Concept | Key Point |
|---------|-----------|
| Inheritance | Derived class acquires base class members |
| "is-a" rule | Use inheritance only for is-a relationships |
| Single | One base → one derived |
| Multilevel | Chain: A → B → C |
| Multiple | One derived → two+ bases |
| Hierarchical | One base → multiple derived |
| Hybrid | Combination of types |
| Constructor order | Base first → derived last |
| Destructor order | Derived first → base last (reverse) |
| Private members | Never accessible in derived class |
| Protected members | Accessible in derived, not outside |
| Function overriding | Same name+signature in derived class |
| Diamond problem | Ambiguity in multiple inheritance with shared base |
| Virtual inheritance | Solves diamond problem — one shared copy of base |
 
---
 
📌 **Next Chapter: OOP — Polymorphism**
