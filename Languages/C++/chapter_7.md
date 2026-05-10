📘 Chapter 7: OOP — Classes & Objects in C++

Object-Oriented Programming (OOP) is a programming approach that organizes code around objects rather than functions. It is the most important topic for any C++ interview — fresher or experienced.


Table of Contents

1. What is OOP?
2. The 4 Pillars of OOP
3. Class & Object
4. Access Specifiers
5. Constructors
6. Destructor
7. static Members
8. friend Function & Class
9. struct vs class
10. Interview Q&A — Theoretical
11. Interview Q&A — Coding
12. Practice Questions


1. What is OOP?
OOP is a programming paradigm that models real-world entities as objects. Each object has:

Data (attributes / member variables)
Behavior (functions / member functions)

Why OOP?
BenefitMeaningModularityCode is organized into self-contained objectsReusabilityClasses can be reused across programsMaintainabilityEasier to update and debugSecurityData can be hidden using access specifiers

2. The 4 Pillars of OOP
These are the most commonly asked OOP theory questions in every fresher interview.
PillarOne-line DefinitionEncapsulationWrapping data and functions together inside a class and hiding data from outsideAbstractionShowing only what is necessary, hiding the internal detailsInheritanceA class acquiring properties of another classPolymorphismOne entity behaving in multiple ways

These are covered in depth in their own chapters. Here we focus on Classes & Objects.


3. Class & Object
Class
A class is a blueprint or template. It defines what data and functions an object will have. It does not occupy memory by itself.
cppclass Student
{
public:
    string name;
    int age;
    float marks;

    void display()
    {
        cout << "Name: " << name << endl;
        cout << "Age: " << age << endl;
        cout << "Marks: " << marks << endl;
    }
};
Object
An object is an instance of a class. When you create an object, memory is allocated.
cppint main()
{
    Student s1;          // Object created — memory allocated

    s1.name  = "Aryan";
    s1.age   = 20;
    s1.marks = 89.5;

    s1.display();
}
Output:
Name: Aryan
Age: 20
Marks: 89.5
Key Points

A class is like a recipe — the object is the dish made from it
Multiple objects can be created from a single class
Each object has its own copy of member variables
Member functions are shared across all objects

cppStudent s1, s2, s3;  // 3 separate objects, each with their own data

4. Access Specifiers
Access specifiers control who can access the members of a class.
SpecifierAccessible FrompublicAnywhere — inside and outside the classprivateOnly inside the class itselfprotectedInside the class and its derived (child) classes
cppclass BankAccount
{
private:
    double balance;      // Cannot be accessed directly from outside

public:
    string owner;        // Can be accessed from anywhere

    void deposit(double amount)
    {
        balance += amount;   // Private member accessed inside class — OK
    }

    double getBalance()
    {
        return balance;      // Controlled access to private data
    }
};

int main()
{
    BankAccount acc;
    acc.owner = "Raj";        // ✅ Public — accessible
    acc.deposit(1000);        // ✅ Public function
    cout << acc.getBalance(); // ✅ Controlled access

    // acc.balance = 5000;   // ❌ Private — compile error
}

Interview Point: By default, all members of a class are private. All members of a struct are public.


5. Constructors
A constructor is a special member function that is automatically called when an object is created. It is used to initialize the object's data.
Rules for Constructors

Same name as the class
No return type (not even void)
Called automatically when object is created
Can be overloaded

Types of Constructors
Default Constructor
No parameters. Called when object is created without arguments.
cppclass Student
{
public:
    string name;
    int age;

    Student()                  // Default constructor
    {
        name = "Unknown";
        age  = 0;
        cout << "Default constructor called\n";
    }
};

int main()
{
    Student s1;       // Default constructor called automatically
    cout << s1.name;  // Unknown
}

Parameterized Constructor
Takes arguments to initialize with specific values.
cppclass Student
{
public:
    string name;
    int age;

    Student(string n, int a)   // Parameterized constructor
    {
        name = n;
        age  = a;
    }
};

int main()
{
    Student s1("Aryan", 20);   // Constructor called with arguments
    Student s2("Priya", 21);

    cout << s1.name;  // Aryan
    cout << s2.name;  // Priya
}

Copy Constructor
Creates a new object as a copy of an existing object.
cppclass Student
{
public:
    string name;
    int age;

    Student(string n, int a)
    {
        name = n;
        age  = a;
    }

    Student(const Student& s)   // Copy constructor
    {
        name = s.name;
        age  = s.age;
        cout << "Copy constructor called\n";
    }
};

int main()
{
    Student s1("Aryan", 20);
    Student s2 = s1;    // Copy constructor called
    Student s3(s1);     // Also calls copy constructor

    cout << s2.name;    // Aryan
}

Constructor Overloading
Multiple constructors with different parameter lists.
cppclass Rectangle
{
public:
    int length, width;

    Rectangle()              // Default
    {
        length = 0; width = 0;
    }

    Rectangle(int l, int w)  // Parameterized
    {
        length = l; width = w;
    }

    Rectangle(int side)      // Square
    {
        length = side; width = side;
    }

    int area() { return length * width; }
};

int main()
{
    Rectangle r1;        // 0 x 0
    Rectangle r2(4, 6);  // 4 x 6
    Rectangle r3(5);     // 5 x 5

    cout << r2.area();   // 24
    cout << r3.area();   // 25
}

Constructor Summary
TypeWhen CalledUseDefaultStudent s1;No-argument initializationParameterizedStudent s1("Aryan", 20);Custom initializationCopyStudent s2 = s1;Creating a copy of an object

6. Destructor
A destructor is a special function called automatically when an object is destroyed (goes out of scope or is deleted). Used to clean up resources.
Rules for Destructors

Same name as the class, preceded by ~
No return type, no parameters
Cannot be overloaded — only one destructor per class
Called automatically — you don't call it manually

cppclass Student
{
public:
    string name;

    Student(string n)
    {
        name = n;
        cout << name << " created\n";
    }

    ~Student()
    {
        cout << name << " destroyed\n";
    }
};

int main()
{
    Student s1("Aryan");
    Student s2("Priya");
    cout << "End of main\n";
}
// Destructors called in REVERSE order of creation
Output:
Aryan created
Priya created
End of main
Priya destroyed
Aryan destroyed

Interview Point: Destructors are called in reverse order of construction — last created, first destroyed (LIFO).


7. static Members
Static Member Variable
Shared across all objects of a class. Only one copy exists regardless of how many objects are created.
cppclass Student
{
public:
    string name;
    static int count;   // Shared by all objects

    Student(string n)
    {
        name = n;
        count++;        // Incremented for each new object
    }
};

int Student::count = 0;   // Must be defined outside the class

int main()
{
    Student s1("Aryan");
    Student s2("Priya");
    Student s3("Rahul");

    cout << Student::count;  // 3 — accessed using class name
}

Static Member Function
Can be called without creating an object. Can only access static members.
cppclass MathHelper
{
public:
    static int square(int x)
    {
        return x * x;
    }
};

int main()
{
    cout << MathHelper::square(5);  // 25 — no object needed
}
Static Summary
FeatureStatic VariableStatic FunctionShared across objects✅ Yes—Needs object to call❌ No❌ NoCan access non-static members—❌ NoAccessed viaClassName::varClassName::func()

8. friend Function & Class
A friend function is a function that is not a member of the class but has access to its private and protected members.
Friend Function
cppclass Box
{
private:
    int length;

public:
    Box(int l) { length = l; }

    friend void printLength(Box b);   // Declaration
};

void printLength(Box b)               // Definition — not a member
{
    cout << "Length: " << b.length;   // Can access private member
}

int main()
{
    Box b(10);
    printLength(b);   // Length: 10
}
Friend Class
All member functions of the friend class can access private members.
cppclass Car
{
private:
    int speed;

public:
    Car(int s) { speed = s; }
    friend class Engine;   // Engine can access Car's private members
};

class Engine
{
public:
    void show(Car c)
    {
        cout << "Speed: " << c.speed;   // Accessing private member
    }
};

Interview Point: Friendship is not mutual and not inherited. If A is a friend of B, B is not automatically a friend of A.


9. struct vs class
FeaturestructclassDefault accesspublicprivateUsed forSimple data groupingFull OOP with methodsInheritance defaultpublicprivateSupports functions✅ Yes✅ YesSupports constructors✅ Yes✅ Yes
cppstruct Point
{
    int x, y;   // Public by default
};

class Circle
{
    int radius;  // Private by default
};

Interview Point: In C++, struct and class are nearly identical — the only real difference is the default access specifier.


10. Interview Q&A — Theoretical
Q1. What is the difference between a class and an object?
A class is a blueprint or template that defines the structure — what data and functions the object will have. It does not occupy memory. An object is an instance of a class — it is the actual entity created in memory based on the class definition. One class can have many objects.

Q2. What are the 4 pillars of OOP?
Encapsulation — bundling data and functions together and hiding data from outside access. Abstraction — showing only essential details and hiding implementation. Inheritance — a child class acquiring properties and behaviors of a parent class. Polymorphism — one function or operator behaving differently in different situations.

Q3. What is a constructor? How is it different from a normal function?
A constructor is a special member function with the same name as the class. It has no return type and is called automatically when an object is created. A normal function has a return type, has a different name from the class, and must be called explicitly.

Q4. What is the difference between a default and a parameterized constructor?
A default constructor takes no arguments and initializes members to default values. A parameterized constructor takes arguments and initializes members with those specific values. Both are called automatically when an object is created, but with different syntax.

Q5. What is a copy constructor? When is it called?
A copy constructor creates a new object as a copy of an existing object. It is called when an object is initialized from another object (Student s2 = s1), when an object is passed to a function by value, and when an object is returned from a function by value.

Q6. What is a destructor? When is it called?
A destructor is a special function with the same name as the class preceded by ~. It has no parameters and no return type. It is called automatically when an object goes out of scope or is explicitly deleted. It is used to release resources like heap memory or file handles held by the object.

Q7. What is the difference between private, public, and protected?
public members are accessible from anywhere. private members are accessible only within the class — not even from derived classes. protected members are accessible within the class and within derived (child) classes but not from outside.

Q8. What is a static member? Why is it used?
A static member variable is shared across all objects of a class — only one copy exists. A static member function can be called without creating an object and can only access static members. They are used for things like counting objects, utility functions, or shared configuration values.

Q9. What is a friend function? Is friendship mutual?
A friend function is a non-member function that has access to the private and protected members of a class. It is declared inside the class with the friend keyword. Friendship is not mutual — if A is a friend of B, B is not automatically a friend of A. Friendship is also not inherited.

Q10. What is the difference between struct and class in C++?
The only difference is the default access specifier. In a struct, all members are public by default. In a class, all members are private by default. Both support constructors, destructors, inheritance, and member functions in C++.

11. Interview Q&A — Coding
Q1. What is the output?
cppclass Demo
{
public:
    Demo()  { cout << "Constructor\n"; }
    ~Demo() { cout << "Destructor\n";  }
};

int main()
{
    Demo d1;
    Demo d2;
    cout << "Main running\n";
}
Output:
Constructor
Constructor
Main running
Destructor
Destructor

Constructors called in order of creation, destructors in reverse order.


Q2. What is the output?
cppclass Counter
{
public:
    static int count;
    Counter() { count++; }
};

int Counter::count = 0;

int main()
{
    Counter c1, c2, c3;
    cout << Counter::count;
}
Output: 3

count is shared across all objects. Each constructor call increments it.


Q3. Find the error.
cppclass Student
{
private:
    int marks;
public:
    Student(int m) { marks = m; }
};

int main()
{
    Student s(90);
    cout << s.marks;
}
Error: marks is private — cannot be accessed directly from main().
Fix: Add a public getter function.
cppint getMarks() { return marks; }

// In main:
cout << s.getMarks();

Q4. Write a class Rectangle with a parameterized constructor and functions for area and perimeter.
cppclass Rectangle
{
private:
    int length, width;

public:
    Rectangle(int l, int w)
    {
        length = l;
        width  = w;
    }

    int area()      { return length * width; }
    int perimeter() { return 2 * (length + width); }
};

int main()
{
    Rectangle r(5, 3);
    cout << "Area: "      << r.area();       // 15
    cout << "Perimeter: " << r.perimeter();  // 16
}

Q5. What is the output?
cppclass Box
{
public:
    int val;
    Box(int v) { val = v; }
    Box(const Box& b)
    {
        val = b.val;
        cout << "Copy constructor\n";
    }
};

void show(Box b)
{
    cout << b.val << "\n";
}

int main()
{
    Box b1(10);
    show(b1);
}
Output:
Copy constructor
10

When b1 is passed to show() by value, a copy is made — copy constructor is called.


12. Practice Questions
Theoretical Q1. Can a class have more than one constructor?
Yes. This is called constructor overloading. A class can have multiple constructors as long as they differ in the number or type of parameters. The compiler calls the matching one based on how the object is created.

Theoretical Q2. Can a constructor be private?
Yes. A private constructor prevents objects from being created directly from outside the class. This is used in the Singleton design pattern where only one instance of a class is allowed.

Theoretical Q3. Can a destructor be overloaded?
No. A destructor cannot be overloaded because it takes no parameters and has no return type. There can only be one destructor per class.

Coding Q1. What is the output?
cppclass Sample
{
public:
    int x;
    Sample()      { x = 0; cout << "Default\n"; }
    Sample(int n) { x = n; cout << "Param\n";   }
};

int main()
{
    Sample a;
    Sample b(5);
    cout << a.x << " " << b.x;
}
Output:
Default
Param
0 5

Coding Q2. What is the output?
cppclass Test
{
public:
    static int n;
    Test()  { n++; }
    ~Test() { n--; }
};

int Test::n = 0;

int main()
{
    Test t1, t2;
    cout << Test::n << "\n";
    {
        Test t3;
        cout << Test::n << "\n";
    }
    cout << Test::n << "\n";
}
Output:
2
3
2

t3 is created inside an inner block. When the block ends, t3's destructor runs — count drops back to 2.


Coding Q3. Write a Student class with name and marks, a parameterized constructor, and a function to print grade (A ≥ 90, B ≥ 75, else C).
cppclass Student
{
private:
    string name;
    int marks;

public:
    Student(string n, int m)
    {
        name  = n;
        marks = m;
    }

    void printGrade()
    {
        cout << name << ": ";
        if(marks >= 90)      cout << "Grade A\n";
        else if(marks >= 75) cout << "Grade B\n";
        else                 cout << "Grade C\n";
    }
};

int main()
{
    Student s1("Aryan", 92);
    Student s2("Priya", 78);
    Student s3("Rahul", 60);

    s1.printGrade();  // Aryan: Grade A
    s2.printGrade();  // Priya: Grade B
    s3.printGrade();  // Rahul: Grade C
}

Quick Recap
ConceptKey PointClassBlueprint — no memory allocatedObjectInstance of class — memory allocatedpublicAccessible from anywhereprivateAccessible only inside the classprotectedAccessible in class and derived classesDefault constructorNo arguments, auto-called on ClassName obj;Parameterized constructorTakes arguments for custom initCopy constructorCreates object from another objectDestructor~ClassName(), auto-called, reverse order of constructionstatic variableShared across all objects, one copy onlystatic functionCalled without object, accesses only static membersfriend functionNon-member with private access — not mutual, not inheritedstruct vs classDefault access — public vs private

📌 Next Chapter: OOP — Encapsulation & Abstraction
