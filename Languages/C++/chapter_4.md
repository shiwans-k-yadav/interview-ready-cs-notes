# 📘 Chapter 4: Arrays & Strings in C++
 
> Arrays and Strings are the most fundamental data structures in C++ and among the most frequently tested topics in interviews.
 
---
 
## Table of Contents
 
- [1. Arrays — Introduction](#1-arrays--introduction)
- [2. Array Declaration & Initialization](#2-array-declaration--initialization)
- [3. Accessing & Traversing Arrays](#3-accessing--traversing-arrays)
- [4. 2D Arrays](#4-2d-arrays)
- [5. Passing Arrays to Functions](#5-passing-arrays-to-functions)
- [6. Strings — Introduction](#6-strings--introduction)
- [7. C-Style Strings (char arrays)](#7-c-style-strings-char-arrays)
- [8. C++ String Class (`std::string`)](#8-c-string-class-stdstring)
- [9. Important String Functions](#9-important-string-functions)
- [10. Common Array & String Algorithms](#10-common-array--string-algorithms)
- [11. Interview Q&A — Theoretical](#11-interview-qa--theoretical)
- [12. Interview Q&A — Coding](#12-interview-qa--coding)
- [13. Practice Questions](#13-practice-questions)
---
 
## 1. Arrays — Introduction
 
An array is a collection of elements of the **same data type** stored in **contiguous memory locations**.
 
### Key Properties
 
| Property | Detail |
|----------|--------|
| Fixed size | Size must be known at compile time (for static arrays) |
| Same type | All elements must be of the same data type |
| Zero-indexed | First element is at index `0` |
| Contiguous memory | Elements are stored one after another in memory |
| Random access | Any element can be accessed in **O(1)** time using index |
 
### Why use Arrays?
 
- Efficient for storing and accessing multiple values of the same type
- Forms the basis of all other data structures
- Required for most algorithm problems in interviews
---
 
## 2. Array Declaration & Initialization
 
```cpp
// Declaration
int arr[5];
 
// Declaration + Initialization
int arr[5] = {10, 20, 30, 40, 50};
 
// Partial initialization (rest become 0)
int arr[5] = {1, 2};  // {1, 2, 0, 0, 0}
 
// Size inferred from initializer
int arr[] = {1, 2, 3, 4};  // size = 4
 
// All elements set to 0
int arr[5] = {0};
```
 
### Memory Layout
 
```
arr = {10, 20, 30, 40, 50}
 
Index:   0    1    2    3    4
Value:  10   20   30   40   50
Addr:  100  104  108  112  116   ← each int = 4 bytes apart
```
 
> **Interview Point:** Array name itself acts as a pointer to the first element. `arr` is the same as `&arr[0]`.
 
---
 
## 3. Accessing & Traversing Arrays
 
```cpp
int arr[5] = {10, 20, 30, 40, 50};
 
// Access single element
cout << arr[2];  // Output: 30
 
// Traverse using for loop
for(int i = 0; i < 5; i++)
{
    cout << arr[i] << " ";
}
// Output: 10 20 30 40 50
 
// Traverse using range-based for loop (C++11)
for(int x : arr)
{
    cout << x << " ";
}
```
 
### Finding Array Size
 
```cpp
int arr[] = {1, 2, 3, 4, 5};
int size = sizeof(arr) / sizeof(arr[0]);
cout << size;  // Output: 5
```
 
> **Interview Trap:** `sizeof(arr)` gives total bytes, not element count. Always divide by `sizeof(arr[0])` to get the number of elements.
 
---
 
## 4. 2D Arrays
 
A 2D array is an array of arrays — used to represent matrices and grids.
 
```cpp
// Declaration
int matrix[3][3];
 
// Declaration + Initialization
int matrix[2][3] = {
    {1, 2, 3},
    {4, 5, 6}
};
 
// Accessing elements
cout << matrix[1][2];  // Output: 6 (row 1, col 2)
 
// Traversal
for(int i = 0; i < 2; i++)
{
    for(int j = 0; j < 3; j++)
    {
        cout << matrix[i][j] << " ";
    }
    cout << endl;
}
```
 
**Output:**
```
1 2 3
4 5 6
```
 
> **Memory:** 2D arrays are stored in **row-major order** in C++ — row 0 elements come first, then row 1, and so on.
 
---
 
## 5. Passing Arrays to Functions
 
When you pass an array to a function, you are passing a **pointer to the first element**, not the entire array.
 
```cpp
void printArray(int arr[], int size)
{
    for(int i = 0; i < size; i++)
        cout << arr[i] << " ";
}
 
int main()
{
    int arr[] = {1, 2, 3, 4, 5};
    printArray(arr, 5);
}
```
 
> **Interview Point:** Because only the pointer is passed, `sizeof(arr)` inside the function gives the **size of the pointer** (4 or 8 bytes), not the array size. That's why we always pass the size separately.
 
---
 
## 6. Strings — Introduction
 
A string is a **sequence of characters**. In C++, strings can be handled in two ways:
 
| Type | What it is | Header |
|------|-----------|--------|
| C-style string | `char` array ending with `\0` | `<cstring>` |
| `std::string` | Class-based string object | `<string>` |
 
---
 
## 7. C-Style Strings (char arrays)
 
```cpp
#include <cstring>
 
char name[] = "Hello";
// Stored as: {'H', 'e', 'l', 'l', 'o', '\0'}
 
// Input
char str[50];
cin >> str;          // Stops at whitespace
cin.getline(str, 50); // Reads full line including spaces
```
 
### Common C-Style String Functions (`<cstring>`)
 
| Function | Purpose | Example |
|----------|---------|---------|
| `strlen(s)` | Returns length (excludes `\0`) | `strlen("Hello")` → 5 |
| `strcpy(dest, src)` | Copies src into dest | `strcpy(a, b)` |
| `strcat(dest, src)` | Appends src to dest | `strcat(a, b)` |
| `strcmp(s1, s2)` | Compares two strings | Returns 0 if equal |
| `strchr(s, c)` | Finds first occurrence of char | Returns pointer |
| `strstr(s1, s2)` | Finds substring | Returns pointer |
 
```cpp
char s1[20] = "Hello";
char s2[] = "World";
 
cout << strlen(s1);      // 5
strcat(s1, s2);
cout << s1;              // HelloWorld
cout << strcmp(s1, s2);  // non-zero (not equal)
```
 
> **Interview Trap:** C-style strings are **not** automatically bounds-checked. Writing beyond the allocated size causes undefined behavior — a common source of bugs and security vulnerabilities.
 
---
 
## 8. C++ String Class (`std::string`)
 
The `std::string` class is safer, easier, and more powerful than C-style strings.
 
```cpp
#include <string>
 
string s1 = "Hello";
string s2 = "World";
 
// Concatenation
string s3 = s1 + " " + s2;
cout << s3;  // Hello World
 
// Input (full line)
getline(cin, s1);
 
// Length
cout << s1.length();  // or s1.size()
 
// Access individual character
cout << s1[0];    // H
cout << s1.at(1); // e (with bounds checking)
```
 
### C-style string vs `std::string`
 
| Feature | `char[]` (C-style) | `std::string` |
|---------|-------------------|---------------|
| Size | Fixed at declaration | Dynamic |
| Null terminator | Manual (`\0`) | Handled automatically |
| Concatenation | `strcat()` | `+` operator |
| Comparison | `strcmp()` | `==` operator |
| Safety | No bounds checking | `at()` does bounds check |
| Ease of use | Complex | Simple |
 
---
 
## 9. Important String Functions
 
```cpp
string s = "Hello World";
 
s.length()          // 11 — number of characters
s.size()            // 11 — same as length()
s.empty()           // false — checks if string is empty
s.substr(6, 5)      // "World" — substr(start, length)
s.find("World")     // 6 — index of first occurrence
s.find("xyz")       // string::npos — not found
s.replace(6, 5, "C++")  // "Hello C++"
s.erase(5, 6)       // "Hello" — erase(start, length)
s.insert(5, "!!!")  // "Hello!!! World"
s.reverse(...)      // use algorithm header instead
 
// Convert to uppercase / lowercase
#include <algorithm>
transform(s.begin(), s.end(), s.begin(), ::toupper);
// s = "HELLO WORLD"
```
 
### Reversing a String
 
```cpp
#include <algorithm>
string s = "Hello";
reverse(s.begin(), s.end());
cout << s;  // olleH
```
 
### String to Integer and Back
 
```cpp
// String to int
string s = "42";
int n = stoi(s);      // n = 42
 
// Int to string
int x = 100;
string str = to_string(x);  // str = "100"
```
 
---
 
## 10. Common Array & String Algorithms
 
### Find Maximum Element
 
```cpp
int arr[] = {3, 1, 4, 1, 5, 9, 2, 6};
int n = sizeof(arr) / sizeof(arr[0]);
int maxVal = arr[0];
 
for(int i = 1; i < n; i++)
{
    if(arr[i] > maxVal)
        maxVal = arr[i];
}
cout << maxVal;  // 9
```
 
### Reverse an Array
 
```cpp
int arr[] = {1, 2, 3, 4, 5};
int n = 5;
int start = 0, end = n - 1;
 
while(start < end)
{
    swap(arr[start], arr[end]);
    start++;
    end--;
}
// arr = {5, 4, 3, 2, 1}
```
 
### Check if String is Palindrome
 
```cpp
string s = "racecar";
string rev = s;
reverse(rev.begin(), rev.end());
 
if(s == rev)
    cout << "Palindrome";
else
    cout << "Not Palindrome";
```
 
### Count Vowels in a String
 
```cpp
string s = "Hello World";
int count = 0;
 
for(char c : s)
{
    c = tolower(c);
    if(c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u')
        count++;
}
cout << count;  // 3
```
 
---
 
## 11. Interview Q&A — Theoretical
 
**Q1. What is an array? What are its advantages and disadvantages?**
 
An array is a collection of elements of the same data type stored in contiguous memory locations.
 
Advantages: constant-time random access O(1), cache-friendly due to contiguous memory, simple to use.
 
Disadvantages: fixed size (cannot grow/shrink), insertion and deletion in the middle is O(n) since elements need to shift, no built-in bounds checking.
 
---
 
**Q2. What is the difference between an array and a pointer in C++?**
 
| Feature | Array | Pointer |
|---------|-------|---------|
| What it is | Fixed-size block of memory | Variable storing a memory address |
| `sizeof` | Returns total array size | Returns size of pointer (4 or 8 bytes) |
| Reassignment | Cannot be reassigned | Can point to different addresses |
| Relationship | Array name decays to pointer to first element | Independent variable |
 
```cpp
int arr[5] = {1, 2, 3};
int *ptr = arr;  // ptr points to arr[0]
 
cout << arr[2];   // 3
cout << ptr[2];   // 3 — same result
cout << *(ptr+2); // 3 — pointer arithmetic
```
 
---
 
**Q3. What is the difference between `char[]` and `std::string`?**
 
`char[]` is a C-style character array that requires manual memory management and a null terminator `\0`. It has a fixed size and uses `<cstring>` functions for operations. `std::string` is a class that manages its own memory, supports dynamic sizing, provides operator overloading for `+` and `==`, and is generally safer and easier to use.
 
---
 
**Q4. What is `string::npos`?**
 
`string::npos` is a constant representing "not found" — returned by `find()` when the substring or character is not present. Its value is the maximum value of `size_t` (typically `4294967295` on 32-bit or a very large number on 64-bit systems).
 
```cpp
string s = "Hello";
if(s.find("xyz") == string::npos)
    cout << "Not found";
```
 
---
 
**Q5. How is a 2D array stored in memory?**
 
In C++, 2D arrays are stored in **row-major order**. This means all elements of row 0 are stored first, followed by all elements of row 1, and so on — forming one continuous block of memory.
 
```
int a[2][3] = {{1,2,3},{4,5,6}};
Memory: 1 2 3 4 5 6  ← all in one block
```
 
---
 
**Q6. What happens when you access an array out of bounds?**
 
C++ does **not** perform automatic bounds checking for raw arrays. Accessing out-of-bounds memory leads to **undefined behavior** — the program might crash, produce garbage values, or corrupt memory silently. Using `std::vector` or `std::string::at()` provides bounds-checked access that throws an exception instead.
 
---
 
**Q7. What is the difference between `length()` and `size()` for `std::string`?**
 
There is no difference. Both return the number of characters in the string. `size()` was added for consistency with other STL containers (like `vector`), while `length()` is kept for readability since it reads naturally for strings.
 
---
 
## 12. Interview Q&A — Coding
 
**Q1. Write a function to find the second largest element in an array.**
 
```cpp
int secondLargest(int arr[], int n)
{
    int first = INT_MIN, second = INT_MIN;
 
    for(int i = 0; i < n; i++)
    {
        if(arr[i] > first)
        {
            second = first;
            first = arr[i];
        }
        else if(arr[i] > second && arr[i] != first)
        {
            second = arr[i];
        }
    }
    return second;
}
```
 
---
 
**Q2. Write a function to check if two strings are anagrams.**
 
```cpp
#include <algorithm>
 
bool isAnagram(string s1, string s2)
{
    if(s1.length() != s2.length()) return false;
    sort(s1.begin(), s1.end());
    sort(s2.begin(), s2.end());
    return s1 == s2;
}
 
// "listen" and "silent" → true
// "hello" and "world"  → false
```
 
---
 
**Q3. Write a function to remove duplicates from a sorted array.**
 
```cpp
int removeDuplicates(int arr[], int n)
{
    if(n == 0) return 0;
    int j = 0;
 
    for(int i = 1; i < n; i++)
    {
        if(arr[i] != arr[j])
        {
            j++;
            arr[j] = arr[i];
        }
    }
    return j + 1;  // new length
}
```
 
---
 
## 13. Practice Questions
 
**Theoretical Q1. Can the size of an array be a variable in C++?**
 
In standard C++, array sizes must be **compile-time constants**. Variable-length arrays (VLAs) are supported in C99 but are **not** part of the C++ standard. Some compilers (like GCC) support them as an extension, but they are not portable and should be avoided. Use `std::vector` for dynamic sizing.
 
---
 
**Theoretical Q2. What is the difference between `s.substr(2, 3)` and `s.substr(2)`?**
 
`s.substr(2, 3)` returns 3 characters starting from index 2. `s.substr(2)` returns all characters from index 2 to the end of the string.
 
```cpp
string s = "HelloWorld";
cout << s.substr(2, 3);  // "llo"
cout << s.substr(5);     // "World"
```
 
---
 
**Coding Q1. What is the output?**
 
```cpp
int arr[5] = {1, 2, 3};
for(int i = 0; i < 5; i++)
    cout << arr[i] << " ";
```
 
**Output:** `1 2 3 0 0`
 
> Uninitialized elements in a partially initialized array are set to `0`.
 
---
 
**Coding Q2. What is the output?**
 
```cpp
string s = "Interview";
cout << s.length() << endl;
cout << s.substr(0, 5) << endl;
cout << s.find("view") << endl;
```
 
**Output:**
```
9
Inter
5
```
 
---
 
**Coding Q3. What is the output?**
 
```cpp
int arr[] = {5, 3, 8, 1, 9, 2};
int n = sizeof(arr) / sizeof(arr[0]);
int temp;
 
for(int i = 0; i < n - 1; i++)
{
    for(int j = 0; j < n - i - 1; j++)
    {
        if(arr[j] > arr[j+1])
            swap(arr[j], arr[j+1]);
    }
}
 
for(int i = 0; i < n; i++)
    cout << arr[i] << " ";
```
 
**Output:** `1 2 3 5 8 9`
 
> This is **Bubble Sort** — repeatedly swapping adjacent elements to sort in ascending order.
 
---
 
**Coding Q4. Write a program to count the frequency of each character in a string.**
 
```cpp
#include <iostream>
#include <string>
using namespace std;
 
int main()
{
    string s = "programming";
    int freq[26] = {0};
 
    for(char c : s)
        freq[c - 'a']++;
 
    for(int i = 0; i < 26; i++)
    {
        if(freq[i] > 0)
            cout << (char)('a' + i) << ": " << freq[i] << endl;
    }
}
```
 
**Output:**
```
a: 1
g: 2
i: 1
m: 2
n: 1
o: 1
p: 1
r: 2
```
 
---
 
## Quick Recap
 
| Concept | Key Point |
|---------|-----------|
| Array indexing | Starts at 0, ends at `size - 1` |
| Array size | `sizeof(arr) / sizeof(arr[0])` |
| Out of bounds | Undefined behavior — no automatic check |
| Pass to function | Pointer is passed, always pass size separately |
| 2D memory layout | Row-major order |
| C-style strings | Null-terminated `\0`, use `<cstring>` functions |
| `std::string` | Safer, dynamic, supports `+` and `==` operators |
| `string::npos` | Returned by `find()` when not found |
| `substr(pos, len)` | Extracts substring starting at `pos` for `len` chars |
 
---
 
📌 **Next Chapter: Pointers & References**
