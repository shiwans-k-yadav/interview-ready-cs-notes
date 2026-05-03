📘 Chapter 2: Control Structures in C++

Control structures allow a program to make decisions and repeat actions.

🔹 1. Conditional Statements
✅ (A) if Statement
int age = 18;

if(age >= 18)
{
    cout << "Eligible to vote";
}

👉 Executes only when condition is true

✅ (B) if-else Statement
int age = 16;

if(age >= 18)
{
    cout << "Eligible";
}
else
{
    cout << "Not Eligible";
}
✅ (C) if-else if Ladder
int marks = 85;

if(marks >= 90)
{
    cout << "Grade A";
}
else if(marks >= 75)
{
    cout << "Grade B";
}
else
{
    cout << "Grade C";
}

👉 Used when multiple conditions are present

⚠️ Interview Point
Multiple if → all conditions checked
if-else if → stops at first true condition
🔹 2. Switch Statement

Used when multiple cases depend on a single variable.

int day = 2;

switch(day)
{
    case 1:
        cout << "Monday";
        break;

    case 2:
        cout << "Tuesday";
        break;

    default:
        cout << "Invalid";
}

👉 break prevents fall-through

⚠️ Interview Trap (Fall-through)
int x = 1;

switch(x)
{
    case 1:
        cout << "A";
    case 2:
        cout << "B";
}

👉 Output: AB

🔹 3. Loops

Used to repeat a block of code.

✅ (A) for Loop
for(int i = 1; i <= 5; i++)
{
    cout << i;
}

👉 Best when number of iterations is known

✅ (B) while Loop
int i = 1;

while(i <= 5)
{
    cout << i;
    i++;
}

👉 Condition checked before execution

✅ (C) do-while Loop
int i = 1;

do
{
    cout << i;
    i++;
} while(i <= 5);

👉 Executes at least once

⚠️ Interview Difference
Loop	Condition Check	Executes at least once
for	Before	❌ No
while	Before	❌ No
do-while	After	✅ Yes
🔹 4. break Statement

Used to exit loop or switch

for(int i = 1; i <= 5; i++)
{
    if(i == 3)
        break;

    cout << i;
}

👉 Output: 1 2

🔹 5. continue Statement

Skips current iteration

for(int i = 1; i <= 5; i++)
{
    if(i == 3)
        continue;

    cout << i;
}

👉 Output: 1 2 4 5

🔹 6. Nested Control Structures

Control statements inside another

for(int i = 1; i <= 3; i++)
{
    for(int j = 1; j <= 2; j++)
    {
        cout << i << " " << j << endl;
    }
}
⚡ Quick Interview Recap
if → decision making
switch → multiple cases
loops → repetition
break → exit
continue → skip
do-while → runs at least once ⭐
🧪 Practice Questions
Q1
int x = 5;

if(x > 3)
    cout << "A";
else
    cout << "B";

👉 Output = ?

Q2
for(int i = 1; i <= 3; i++)
{
    cout << i;
}

👉 Output = ?

Q3
int i = 1;

while(i <= 3)
{
    cout << i;
    i++;
}

👉 Output = ?

📌 Next Chapter: Functions
