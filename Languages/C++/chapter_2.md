# 📘 Chapter 2: Control Structures in C++

> Control structures allow a program to make decisions and repeat actions.

---

## 🔹 1. Conditional Statements

### ✅ (A) `if` Statement

```cpp
int age = 18;
if(age >= 18)
{
    cout << "Eligible to vote";
}
```

> 👉 Executes only when condition is true

---

### ✅ (B) `if-else` Statement

```cpp
int age = 16;
if(age >= 18)
{
    cout << "Eligible";
}
else
{
    cout << "Not Eligible";
}
```

---

### ✅ (C) `if-else if` Ladder

```cpp
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
```

> 👉 Used when multiple conditions are present

### ⚠️ Interview Point

| Type | Behavior |
|------|----------|
| Multiple `if` | All conditions are checked |
| `if-else if` | Stops at the first true condition |

---

## 🔹 2. Switch Statement

Used when multiple cases depend on a single variable.

```cpp
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
```

> 👉 `break` prevents fall-through

### ⚠️ Interview Trap — Fall-through

```cpp
int x = 1;
switch(x)
{
    case 1:
        cout << "A";
    case 2:
        cout << "B";
}
```

> 👉 **Output:** `AB`

---

## 🔹 3. Loops

Used to repeat a block of code.

### ✅ (A) `for` Loop

```cpp
for(int i = 1; i <= 5; i++)
{
    cout << i;
}
```

> 👉 Best when number of iterations is known

---

### ✅ (B) `while` Loop

```cpp
int i = 1;
while(i <= 5)
{
    cout << i;
    i++;
}
```

> 👉 Condition checked before execution

---

### ✅ (C) `do-while` Loop

```cpp
int i = 1;
do
{
    cout << i;
    i++;
} while(i <= 5);
```

> 👉 Executes at least once

---

### ⚠️ Interview Difference

| Loop | Condition Check | Executes at least once |
|------|-----------------|------------------------|
| `for` | Before | ❌ No |
| `while` | Before | ❌ No |
| `do-while` | After | ✅ Yes |

---

## 🔹 4. `break` Statement

Used to exit a loop or switch.

```cpp
for(int i = 1; i <= 5; i++)
{
    if(i == 3)
        break;
    cout << i;
}
```

> 👉 **Output:** `1 2`

---

## 🔹 5. `continue` Statement

Skips the current iteration.

```cpp
for(int i = 1; i <= 5; i++)
{
    if(i == 3)
        continue;
    cout << i;
}
```

> 👉 **Output:** `1 2 4 5`

---

## 🔹 6. Nested Control Structures

Control statements inside another.

```cpp
for(int i = 1; i <= 3; i++)
{
    for(int j = 1; j <= 2; j++)
    {
        cout << i << " " << j << endl;
    }
}
```

---

## ⚡ Quick Interview Recap

| Concept | Purpose |
|---------|---------|
| `if` | Decision making |
| `switch` | Multiple cases |
| Loops | Repetition |
| `break` | Exit loop/switch |
| `continue` | Skip current iteration |
| `do-while` | Runs at least once ⭐ |

---

## 🧪 Practice Questions

### Q1
```cpp
int x = 5;
if(x > 3)
    cout << "A";
else
    cout << "B";
```
<details>
<summary>👉 Click to see Output</summary>

**Output:** `A`

</details>

---

### Q2
```cpp
for(int i = 1; i <= 3; i++)
{
    cout << i;
}
```
<details>
<summary>👉 Click to see Output</summary>

**Output:** `123`

</details>

---

### Q3
```cpp
int i = 1;
while(i <= 3)
{
    cout << i;
    i++;
}
```
<details>
<summary>👉 Click to see Output</summary>

**Output:** `123`

</details>

---

📌 **Next Chapter: Functions**
