# C++

## Session recording
Recording: [YouTube Link](https://youtu.be/LokfvY8z3z4?si=xLLAWei-9JJyWEV5)

---

## Tooling: Compilers & IDEs

### Compilers on Different Platforms
- **Windows**
  - [MinGW-w64](http://mingw-w64.org/) → `g++` for Windows.
  - [Microsoft Visual C++ (MSVC)](https://visualstudio.microsoft.com/) → integrated with Visual Studio.
- **macOS**
  - Apple’s `clang++` comes with Xcode Command Line Tools (`xcode-select --install`).
  - `g++` available via [Homebrew](https://brew.sh/).
- **Linux**
  - `g++` (GNU Compiler Collection) is standard on most distros (`sudo apt install g++`).
  - `clang++` often available as an alternative (`sudo apt install clang`).

---

### IDE & Editor Options

| Tool              | Platform        | Pros                                                                 | Cons                                                      |
|-------------------|-----------------|----------------------------------------------------------------------|-----------------------------------------------------------|
| **Visual Studio** | Windows only    | Complete IDE, strong debugger, GUI tools, MSVC integration           | Heavy install, Windows-only, project setup can be complex |
| **CLion**         | Cross-platform  | Smart refactoring, deep C++ support, CMake built-in, JetBrains polish | Paid (student license free), resource-heavy               |
| **VS Code**       | Cross-platform  | Lightweight, extensible via plugins, good IntelliSense + debugging    | Requires setup of extensions, less deep C++ refactoring   |
| **Vim/Neovim**    | Cross-platform  | Fast, customizable, works in terminal, great for remote dev           | Steep learning curve, needs plugin setup                  |
| **Emacs**         | Cross-platform  | Highly extensible (LSP, org-mode), powerful for advanced users        | Steep learning curve, fewer modern IDE features           |
| **[Online Editor](https://www.onlinegdb.com/online_c++_compiler)**        | Web page		  | Easy access | Small code only |
| **Peng’s Choice** | Mac, Windows, Linux | CLion on Mac/Windows for consistent IDE workflow; Vim on Linux for fast editing & remote work | Students may prefer simpler editors when starting out     |

## 0. Baby Steps in C++ (Homework-style warmup)

```cpp
#include <iostream>
#include <string>
#include <cstdio>

int main() {
    std::string message = "Hello C++ BootUp!";
    std::cout << message << std::endl;
    printf("%s\n", message.c_str());

    // primitive variable
    int n = 5;
    std::string messages[] = {
            "C++",
            "this",
            "is",
            "so",
            "much",
            "fun"
    };

    // for loop with condition
    for (int i = 0; i < n; ++i) {
        if (i % 2 == 0) {
            std::cout << messages[i] << std::endl;
        }
    }

    // infinite loop with break
    for (int i = 0;; ++i) {
        if (i == n) {
            break;
        }
        std::cout << messages[i] << std::endl;
    }

    // while loop with break
    int i = 0;
    while (true) {
        if (i == n) {
            break;
        }
        std::cout << messages[i++] << std::endl;
    }
}
```

👉 These baby steps cover: strings, arrays, `for` loops, and `while` loops. Great practice before diving into memory and pointers.

### In-class Exercise A

You are given two choices:

- Take $1,000,000 today, no conditions.
- Take $0.01 today (yes one cent that is), but it will grow every day for 30 days by a fixed percentage rate.

Every day, your money doubles. For example, one day 1, your have 1 cent. On day 2, you have 2 cent. On day 3, you have 4 cent.

Before writing any code, answer this:

Which option would you choose — the guaranteed $1,000,000, or the 30-day compounding penny?

Now write a program that simulates the second option.

```c++
#include <iostream>

int main() {
    /*
     * For 30 days.
     * Starting from 1 cent.
     * Double the money from the day before.
     * How much end up till end of 30 days?
     */

    const float start_in_dollar = 0.01; // 1 cent
    float start_rate_in_percentage = 100;
    int days = 30;
    const float one_million = 1000000.0;

    for (int rate = start_rate_in_percentage; rate <= 100; rate += 1) {
        float ending_balance = start_in_dollar;
        for (int day = 1; day < days; ++day) {
            ending_balance += (float) rate / 100.0f * ending_balance;
        }
        if (ending_balance > one_million) {
            std::cout << "rate " << rate << " beats lump sump with ending balance " << ending_balance / one_million <<
                    " million!" << std::endl;
        }
    }
}
```

### In-class Exercise B

1. Given a number, determine whether it is a prime number.
2. Given the product of two prime numbers as int, find out the two prime numbers. For example, give 35, the result is 5 and 7.
3. Increate the number size using *long*, and then *long long*. See the running time increase.
4. Increase the number to very large (boost::multiprecision::cpp_int), and see the running time explode.
5. Explore how this can be used for public key encryption.

```c++
#include <iostream>
#include <chrono>
#include <boost/multiprecision/cpp_int.hpp>

using boost::multiprecision::cpp_int;

// One pair type for both sizes
template<typename T>
struct PrimePairT {
    T p;
    T q;
};

// One factoring algorithm for both sizes
template<typename T>
void find_two_primes(const T &product) {
    using namespace std::chrono;

    auto start = high_resolution_clock::now();
    T iterations = 0;

    for (T i = 2; i <= product / i; ++i) {
        ++iterations;

        if (product % i == 0) {
            auto end = high_resolution_clock::now();
            auto ms = duration_cast<milliseconds>(end - start).count();

            std::cout << "prime 1: " << i << "\n";
            std::cout << "prime 2: " << product / i << "\n";
            std::cout << "iterations: " << iterations << "\n";
            std::cout << "time: " << ms << " ms\n";
            return;
        }
    }

    auto end = high_resolution_clock::now();
    auto ms = duration_cast<milliseconds>(end - start).count();

    std::cout << "no prime pair found\n";
    std::cout << "iterations: " << iterations << "\n";
    std::cout << "time: " << ms << " ms\n";
}

int main() {
    // Crackable numbers (long long)
    PrimePairT<long long> ll_cases[] = {
        {10007, 10009},
        {1000003, 1000033},
        {10000019, 10000079},
        {100000007, 100000037},
        {1000000007LL, 1000000009LL}
    };

    std::cout << "===== long long =====\n";
    for (auto &t: ll_cases) {
        long long n = t.p * t.q;
        std::cout << "\nFactoring n = " << n << "\n";
        find_two_primes(n);
    }

    // RSA-scale numbers (cpp_int)
    PrimePairT<cpp_int> big_cases[] = {
        {(cpp_int(1) << 17) - 1, (cpp_int(1) << 19) - 1},
        {(cpp_int(1) << 31) - 1, (cpp_int(1) << 61) - 1},
        {(cpp_int(1) << 61) - 1, (cpp_int(1) << 89) - 1},
        {(cpp_int(1) << 61) - 1, (cpp_int(1) << 127) - 1}
    };

    std::cout << "\n===== cpp_int =====\n";
    for (auto &t: big_cases) {
        cpp_int n = t.p * t.q;
        std::cout << "\nFactoring n (bits=" << msb(n) + 1 << ")\n";
        find_two_primes(n);
    }
}
```

#### How Prime Numbers Are Used for Encryption

Imagine we choose two small prime numbers, p = 13 and q = 7. We multiply them to get n = 13 × 7 = 91. We publish n for everyone to see, but we keep p and q secret. Now suppose someone wants to send us a secret number m, say m = 20. Using our public key (which includes n), they transform the message using a mathematical formula based on modular arithmetic, turning 20 into a scrambled number c. Anyone can do this because n is public. But to turn c back into 20, you need a special number d that depends on φ(n) = (13 − 1)(7 − 1) = 12 × 6 = 72, and you can only compute φ(n) if you know p and q. An attacker who only sees n = 91 would have to factor it into 13 and 7 to get φ(n), which is easy here but becomes impossible when p and q are hundreds of digits long. That’s how multiplying two primes creates a public lock that only the person who knows the primes can unlock.


### ✍️ Student Exercise 1

Write a program that checks whether a given `std::string` reads the same **forward** and **backward** (a **palindrome**).  

**Requirements:**  
1. Ask the user to enter a word.  
2. Write a function `isPalindrome(const std::string&)` that returns `true` if the word is a palindrome, `false` otherwise.  
3. Use a loop to compare characters from the front and back.  
4. Print `"Palindrome!"` if it is, otherwise `"Not a palindrome."`.

**Code Skeleton:**  

```cpp
#include <iostream>
#include <string>

// Function that checks if a word is a palindrome
bool isPalindrome(const std::string& word) {
	// TODO: loop through word and compare characters from front and end
    // if mismatch, return false
    return true; // placeholder
}

/*
 Manual test function to mimic unit testing.

 Key ideas:
 1. Tests should do the verification, not the human.
 2. A test compares expected vs actual results automatically.
 3. If they don't match, flag a failure.
*/
void testPalindrome(const std::string& word, bool expected) {
	bool result = isPalindrome(word);

    if (result == expected) {
        std::cout << "PASS: " << word << std::endl;
    } else {
        std::cout << "FAIL: " << word 
                  << " (expected " << (expected ? "true" : "false") 
                  << ", got " << (result ? "true" : "false") 
                  << ")" << std::endl;
    }
}

int main() {
	// 1. Ask the user to enter a word
    std::string word;
    std::cout << "Enter a word: ";
    std::cin >> word;

    // 2. Call isPalindrome(word)
    // 3. Print "Palindrome!" if true, else "Not a palindrome."
}
```

### ✍️ Student Exercise 2: Prime Numbers (Primitive Arrays)

Write a program that finds all prime numbers **less than a given positive integer `n`**.  
For example, if `n = 10`, the program should return `[2, 3, 5, 7]`.  

**Requirements:**  
1. Write a function `findPrimesUpTo(int, int[])` that fills an array with primes less than `n`.  
   - The function should return the number of primes found (`primeCount`).  
2. Use a helper function `isPrime(int)` if needed.  
3. In `main()`, ask the user for `n`, call the function, and print the primes.  
4. Write a simple test function to verify correctness.  

**Code Skeleton:**  

```cpp
#include <iostream>

// Helper function: check if a number is prime
bool isPrime(int number) {
    // TODO:
    // 1. Handle edge cases: numbers <= 1 are not prime.
    // 2. Loop from 2 up to sqrt(number).
    // 3. If divisible by any number, return false.
    // 4. Otherwise return true.
    return true; // placeholder
}

// Fill result[] with all primes less than n.
// Return the count of primes found.
int findPrimesUpTo(int n, int result[]) {
    int primeCount = 0;
    // TODO:
    // 1. Loop from 2 to n-1.
    // 2. If isPrime(i) is true, store in result[primeCount] and increment primeCount.
    return primeCount; // placeholder
}

/*
 Manual test function to mimic unit testing.

 Key ideas:
 - Define input n and the expected list of primes.
 - Call findPrimesUpTo(n, ...).
 - Compare output array with expected values.
 - Print PASS or FAIL.
*/
void testFindPrimesUpTo(int n, const int expected[], int expectedCount) {
    // TODO:
    // 1. Create a local array large enough to hold results.
    // 2. Call findPrimesUpTo(n, result).
    // 3. Compare returned count with expectedCount.
    // 4. Compare each element with expected[i].
    // 5. Print PASS or FAIL.
}

int main() {
    int n;
    std::cout << "Enter a positive integer: ";
    std::cin >> n;

    int primes[100]; // assume at most 100 primes for demo
    int primeCount = findPrimesUpTo(n, primes);

    std::cout << "Primes less than " << n << ": ";
    for (int i = 0; i < primeCount; i++) {
        std::cout << primes[i] << " ";
    }
    std::cout << std::endl;

    /* TODO:
       Add manual tests:
       - testFindPrimesUpTo(10, {2, 3, 5, 7}, 4);
       - testFindPrimesUpTo(5, {2, 3}, 2);
       - testFindPrimesUpTo(2, {}, 0);
    */

    return 0;
}
```

### ✍️ Student Exercise 3: Prime Numbers (std::vector + TestCase)

Rewrite the previous exercise to use `std::vector<int>` instead of primitive arrays, and bundle test inputs/expectations into a `TestCase` class.

**Code Skeleton (with TODOs):**
```cpp
#include <iostream>
#include <vector>

// Optional helper: check if a number is prime
bool isPrime(int number) {
    // TODO:
    // 1) Handle numbers <= 1 (not prime)
    // 2) Check divisibility from 2 up to sqrt(number)
    // 3) Return false if divisible; true otherwise
    return true; // placeholder
}

// Return all primes strictly less than n
std::vector<int> findPrimesUpTo(int n) {
    std::vector<int> primes;
    // TODO:
    // 1) Loop i from 2 to n-1
    // 2) If isPrime(i) is true, push_back(i) into primes
    return primes; // placeholder
}

// Encapsulate one test case worth of data
class TestCase {
public:
    int n;                       // input upper bound (exclusive)
    std::vector<int> expected;   // expected primes < n

    TestCase(int n_, std::vector<int> exp)
        : n(n_), expected(std::move(exp)) {}
};

/*
 Manual test function to mimic unit testing.

 Key ideas:
 - Tests should verify automatically, not rely on the human to compare.
 - Compare size() and each element in-order.
 - Print PASS/FAIL with enough details for debugging.
*/
void testFindPrimesUpTo(const TestCase& tc) {
    // TODO:
    // 1) Call findPrimesUpTo(tc.n) and store the result.
    // 2) Compare result.size() with tc.expected.size().
    // 3) If sizes match, compare each element result[i] vs tc.expected[i].
    // 4) If any mismatch, print FAIL (include n, index, expected vs actual).
    // 5) If all good, print PASS (include n and size).
}

int main() {
    // Interactive run
    int n;
    std::cout << "Enter a positive integer: ";
    std::cin >> n;

    std::vector<int> primes = findPrimesUpTo(n);
    std::cout << "Primes less than " << n << ": ";
    for (int p : primes) std::cout << p << " ";
    std::cout << std::endl;

    /* TODO: add tests in a loop
       std::vector<TestCase> tests = {
           TestCase(10, {2, 3, 5, 7}),
           TestCase(5,  {2, 3}),
           TestCase(2,  {})
       };

       for (const auto& tc : tests) {
           testFindPrimesUpTo(tc);
       }
    */

    return 0;
}
```

---

## 1. Compilation: Code → Binary

> “Programs must be written for people to read, and only incidentally for machines to execute.”  
> — Harold Abelson, *Structure and Interpretation of Computer Programs*

Unlike Java (compile to bytecode + JVM), C++ compiles directly to native machine code.

**What does that mean?**  
- **Bytecode (Java):** When you compile Java, the compiler translates your `.java` source files into an intermediate format called **bytecode** (stored in `.class` files). Bytecode is not tied to any specific CPU. Instead, it runs inside the **Java Virtual Machine (JVM)**, which interprets or JIT-compiles it into real instructions for the host system. This makes Java **portable** across platforms, because the same `.class` file can run anywhere there’s a JVM.  
- **Native Machine Code (C++):** When you compile C++ with `g++` or `clang++`, your source files are translated directly into the **machine instructions** that your CPU executes (an `.exe` on Windows, or a binary on Linux/macOS). This means the program runs **fast**, without the overhead of a virtual machine — but it’s also tied to the specific platform and architecture you compiled for (e.g., x86 vs ARM).


**Minimal program:**

```cpp
#include <iostream>
int main() {
    std::cout << "Hello C++ BootUp!" << std::endl;
    return 0;
}
```

**Compile & run:**

```bash
# g++        → GNU C++ compiler
# -std=c++17 → Use the C++17 language standard
# -O2        → Enable level 2 optimizations (good balance of speed and compile time)
# -Wall      → Enable most common warnings
# -Wextra    → Enable additional (extra) warnings
# -o helloworld → Name of the output binary (instead of default a.out)
# helloworld.cpp → Your source code file
g++ -std=c++17 -O2 -Wall -Wextra -o helloworld helloworld.cpp
./helloworld
```

👉 The `-o helloworld` flag tells the compiler the **name of the output binary**.  
- On **Linux and macOS**, executables usually don’t have a `.exe` extension — you just run them with `./helloworld`.  
- On **Windows**, compilers typically add `.exe` automatically (e.g., `helloworld.exe`).  
- If you omit `-o`, the default output file is usually named `a.out`.  

---

## 2. Assembly Peek (Stack Frames)

```cpp
#include <iostream>
int max2(int a, int b) { return (a > b) ? a : b; }
int main() {
    std::cout << max2(41, 42) << "\n";
}
```

👉 Function calls push a **stack frame**, locals live in it, and `ret` uses the return address stored on the stack.

When you call a function like `max2(41, 42)`, the computer creates a **stack frame**, which is like opening a new “workspace folder” on top of a pile: inside it you put the function’s inputs (`a=41`, `b=42`), any local notes (variables), and a sticky note reminding you where to return in `main()` when done. When the function finishes, the answer (42) is handed back to the caller, and that workspace folder is immediately **closed and removed from the pile**. The sticky note is read so the program knows exactly where to continue in `main()`. This pile of folders is called the **call stack**, and just like a stack of plates, the last one you put on is always the first one you take off. 

The **call stack** lives inside a special region of memory called **stack memory**. When your program starts, the operating system gives it a block of memory reserved for stack use. Each time a function is called, a new stack frame is allocated in this memory, growing downward like adding new pages to a notepad. When the function finishes, its stack frame is popped off, and the memory is automatically reclaimed. This is why local variables only exist while the function is running — their space in stack memory disappears as soon as the function’s frame is removed. Unlike heap memory (which we manage with `new` and `delete`), stack memory is automatically managed by the system with a simple push/pop discipline.

**TL;DR:**  
Calling a function adds a new “folder” (stack frame) on the call stack, holding its inputs, locals, and a return address. When the function finishes, the folder is popped off and memory is automatically freed. This whole process happens in **stack memory**, a region managed by the operating system that grows and shrinks as functions are called and returned.

### ✍️ Exploration: Estimate Stack Size (MB)

```cpp
/*
Goal: Hold a ~1 MB local array in each recursive call and count how many
  calls we can make before the program runs out of stack space.

  How it works:
  - Each call to foo() allocates a 1,048,576-byte array on the stack.
  - We print the recursion depth (i) to show how many frames are active.
  - Recursing indefinitely will eventually overflow the stack and crash.
  - The approximate stack used at the moment of crash ≈ i × 1 MB.

  How to check your system's stack limit (macOS/Linux):
    ulimit -s      # prints stack size in kilobytes (e.g., 8192 = 8 MB)

  You can temporarily raise it for testing:
    ulimit -s 65536   # sets stack to 64 MB for this shell

  Notes:
  - This intentionally crashes to reveal the stack limit. Run only in a safe environment.
  - Compilers may optimize unused arrays away, so we write into the array to keep it.
  - The actual limit depends on OS, shell limits, compiler, and build type.
  - A SIGSEGV handler could be used to print the last value before crashing, but we
    keep it simple here.
*/

#include <cstdio>

void foo(int i) {
    // 1 MB on the stack per call
    constexpr int size = 1024 * 1024;
    char data[size];

    // Touch the memory so the compiler can't optimize it away
    data[0] = static_cast<char>(i);

    // Print current recursion depth and flush so it shows before crash
    std::printf("%d\n", i);
    std::fflush(stdout);

    // Recurse to allocate another 1 MB stack frame
    foo(i + 1);
}

int main() {
    foo(0);
}
```

Food for thoughts: What do you get when running this? And what does it mean?

---

## 3. Memory System

### 3.1 Stack vs Heap

In C++, memory is managed through the stack and the heap, two distinct areas that balance speed, size, and flexibility in different ways.

The stack is like cash in your pocket: it’s right there for you to use instantly, no paperwork, no waiting — but your pocket can only hold a small amount, and once you leave a function it all gets emptied out. The heap, on the other hand, is like money in the bank: there’s a lot more available and it’s shared across your whole program, but using it takes more effort — you have to request space, wait for it, and when you’re done you must return it properly, or else you risk “losing” it. Knowing when to use the stack for speed and simplicity, and when to use the heap for flexibility and size, is key to writing efficient and reliable C++ programs.

| Concept | Analogy | Characteristics | In C++ |
|---------|----------|-----------------|--------|
| **Stack** | Your **wallet** | - Small, fast, automatic<br>- Last in, first out (LIFO)<br>- Cleared when you leave a function | Local variables (e.g., `int x = 5;`) |
| **Heap** | Your **bank account** | - Large, flexible, manual<br>- Access is slower<br>- Must manage deposits/withdrawals yourself | Dynamically allocated objects (e.g., `new int(5);`) |

<img width="50%" height="1472" alt="image" src="https://github.com/user-attachments/assets/696fa1c2-8532-4093-b896-b732b9329c16" />

### 3.2 Accessing Memory Locations (Addressing)

In C++, there are three common ways to work with memory: variable names, references, and pointers. A variable name gives you direct access to a piece of memory by its declared identifier, while a reference acts as an alias — essentially another name for the same memory, ensuring you always work with the original object. A pointer, on the other hand, is a variable that holds a memory address, giving you the power to point to different objects and control memory manually with * and &. One key distinction between C++ and Java is that in C++, a pointer truly stores a raw address in memory, which you can manipulate directly, while in Java, what we call a “reference” is a managed handle to an object — you don’t see or control the actual memory address, and the garbage collector ensures cleanup instead of requiring you to free memory yourself.

- **Variable name:** Direct access to a memory location by its declared name (e.g., `int a = 10;`).  
- **Reference:** An alias to an existing variable; another name for the same memory (e.g., `int& ref = a;`).  
- **Pointer:** Stores the memory address of a variable; use `*` to access or modify the value (e.g., `int* p = &a;`).  

```cpp
#include <iostream>

void bumpByValue(int x) { x++; }
void bumpByRef(int& x) { x++; }
void bumpByPtr(int* x) { if (x) (*x)++; }

void swapByValue(int a, int b) { int t=a; a=b; b=t; }
void swapByRef(int& a, int& b) { int t=a; a=b; b=t; }
void swapByPtr(int* a, int* b) { int t=*a; *a=*b; *b=t; }

int main() {
    int a = 10, b = 20;
    bumpByValue(a);
    bumpByRef(a);
    bumpByPtr(&a);

    swapByValue(a, b);
    std::cout << a << "," << b << "\n";

    swapByRef(a, b);
    std::cout << a << "," << b << "\n";

    swapByPtr(&a, &b);
    std::cout << a << "," << b << "\n";
}
```

👉 In Java, objects are always passed by value **of reference**. In C++, you explicitly choose **value/ref/pointer**.

---

## 3.3 Exercise 1: Pointer Arithmetic with Arrays

```cpp
#include <iostream>

// Finds the element-wise maximum between two arrays of length n
void maxArrays(const int* arr1, const int* arr2, int* result, int n) {
    const int* p1 = arr1;
    const int* p2 = arr2;
    int* pr = result;

    for (int i = 0; i < n; ++i) {
        *pr = (*p1 > *p2) ? *p1 : *p2;
        p1++;
        p2++;
        pr++;
    }
}

int main() {
    int a[] = {1, 7, 3, 9, 5};
    int b[] = {2, 6, 4, 8, 10};
    int result[5];

    maxArrays(a, b, result, 5);

    std::cout << "Element-wise max array: ";
    for (int i = 0; i < 5; ++i) {
        std::cout << result[i] << " ";
    }
    std::cout << "\n";
}
```

👉 Demonstrates how pointers can walk through arrays: `p++` moves the pointer by `sizeof(int)` and `*p` accesses the value. This is equivalent to indexing (`arr[i]`), but shows how arrays and pointers are deeply connected in C++.

---

## 3.4 Exercise 2: Pointer Arithmetic with Arrays (Returning a New Result Array)

```cpp
#include <iostream>

// Allocate a new array and return pointer to result
int* maxArraysNew(const int* arr1, const int* arr2, int n) {
    int* result = new int[n]; // caller must remember to delete[] this

    const int* p1 = arr1;
    const int* p2 = arr2;
    int* pr = result;

    for (int i = 0; i < n; ++i) {
        *pr = (*p1 > *p2) ? *p1 : *p2;
        p1++;
        p2++;
        pr++;
    }

    return result;
}

int main() {
    int a[] = {1, 7, 3, 9, 5};
    int b[] = {2, 6, 4, 8, 10};

    int* result = maxArraysNew(a, b, 5);

    std::cout << "Element-wise max array: ";
    for (int i = 0; i < 5; ++i) {
        std::cout << result[i] << " ";
    }
    std::cout << "\n";

    delete[] result; // important to avoid memory leak
}
```

👉 This version dynamically allocates the result array and returns it. In production C++, prefer `std::vector<int>` or smart pointers to manage memory automatically.

⚠️ **Memory Leak Warning:** If you forget the `delete[] result;` line, tools like Valgrind will show output.

---

## 4. Object-Oriented Programming (OOP)

### 4.1 Class Basics

```cpp
#include <iostream>
#include <string>

class Dog {
private:
    std::string name;   // private data member (only accessible inside the class)

public:
    // Constructor: runs when object is created
    Dog(const std::string& n) : name(n) {
        std::cout << "Dog constructor: " << name << "\n";
    }

    // Public method: accessible from outside
    void bark() const {
        std::cout << name << " says Woof!\n";
    }

    // Destructor: runs automatically when object goes out of scope
    ~Dog() {
        std::cout << "Dog destructor: " << name << "\n";
    }
};

int main() {
    Dog d("Buddy");  // Constructor runs here
    d.bark();        // Call public method
} // Destructor runs automatically here
```

### Key Concepts
- **Class**: A blueprint that groups data (fields) and behavior (methods).  
- **Private**: Data or methods hidden from outside code; used for encapsulation.  
- **Public**: Data or methods accessible to everyone.  
- **Constructor**: Special function that sets up the object when created.  
- **Destructor**: Special function that cleans up when the object is destroyed.  


### 4.2 Objects & Copying

```cpp
#include <iostream>
#include <string>

class Character {
protected:
    std::string name;
public:
    Character(const std::string& n) : name(n) {
        std::cout << "Character ctor\n";
    }
  
    virtual ~Character() { std::cout << "Character dtor\n"; }

    virtual std::string ability() const { return "?"; }

};

class Mario : public Character {
public:
    Mario(const std::string& n) : Character(n) { std::cout << "Mario ctor\n"; }
    Mario(const Mario& m) : Character(m) { std::cout << "Mario copy ctor\n"; }
    ~Mario() override { std::cout << "Mario dtor\n"; }
    std::string ability() const override { return "Fireball"; }
};

void trainByValue(Mario m) { (void)m; }
void trainByRef(Mario& m) { (void)m; }
void trainByPtr(Mario* m) { (void)m; }

int main() {
    Mario hero("Mario");
    trainByValue(hero);
    trainByRef(hero);
    trainByPtr(&hero);
}
```

---

## 4. Polymorphism & Slicing (Mario Theme)

```cpp
#include <iostream>
#include <vector>

class Character {
protected:
    std::string name;
public:
    Character(const std::string& n)
    : name(n)
    {
        std::cout << "Character ctor\n";
    }
    virtual ~Character() {}
    virtual std::string ability() const { return "?"; }

};

class Mario : public Character {
public:
    Mario(const std::string& n) : Character(n) { std::cout << "Mario ctor\n"; }
    std::string ability() const override { return "Fireball"; }
};

class Luigi : public Character {
public:
    Luigi(const std::string& n) : Character(n) { std::cout << "Luigi ctor\n"; }
    std::string ability() const override { return "High Jump"; }
};

class Bowser : public Character {
public:
    Bowser(const std::string& n) : Character(n) { std::cout << "Bowser ctor\n"; }
    std::string ability() const override { return "Flame Breath"; }
};

void showByValue(Character c) {
    std::cout << c.ability() << "\n";
}
void showByRef(const Character& c) {
    std::cout << c.ability() << "\n";
}

int main() {
    Mario mario("Mario");
    Luigi luigi("Luigi");
    Bowser bowser("Bowser");

    // --- Slicing Demo ---
    showByValue(mario); // prints "?" (sliced)
    showByRef(mario);   // prints "Fireball" (no slicing)

    std::vector<Character> byValue;
    byValue.push_back(mario);
    std::cout << byValue[0].ability() << "\n"; // prints "?" (sliced)

    std::vector<Character*> party;
    party.push_back(new Mario("Mario"));
    party.push_back(new Luigi("Luigi"));
    party.push_back(new Bowser("Bowser"));

    for (auto p : party) std::cout << p->ability() << "\n"; // correct polymorphism

    // cleanup
    for (auto p : party) delete p;
}
```

---

## 5. Stack vs Heap & RAII (Mario Theme)

```cpp
#include <iostream>
#include <string>

class Character {
protected:
    std::string name;
public:
    Character(const std::string& n)
    : name(n)
    {
        std::cout << "Character ctor\n";
    }
    virtual ~Character() { std::cout << "~Character for " << name << "\n"; }
    virtual std::string ability() const { return "?"; }
};

class Luigi : public Character {
public:
    Luigi(const std::string& n) : Character(n) { std::cout << "Luigi ctor\n"; }
    ~Luigi() override { std::cout << "~Luigi cleanup for " << name << "\n"; }
    std::string ability() const override { return "High Jump"; }
};

int main() {
    Luigi stackLuigi("Luigi");
    std::cout << stackLuigi.ability() << "\n";

    Luigi* heapLuigi = new Luigi("Luigi");
    std::cout << heapLuigi->ability() << "\n";
    delete heapLuigi;

    Luigi* rawPtr = new Luigi("Luigi");
    std::cout << rawPtr->ability() << "\n";
    delete rawPtr;
}
```

---

## 6. Virtual Destructors (Mario Theme)

```cpp
#include <iostream>
#include <string>

class Character {
protected:
    std::string name;
public:
    Character(const std::string& n)
    : name(n)
    {
        std::cout << "Character ctor\n";
    }
    virtual ~Character() { std::cout << "~Character\n"; }
    virtual std::string ability() const { return "?"; }
};

class Bowser : public Character {
    int* heavy = new int[256];
public:
    Bowser(const std::string& n) : Character(n) { std::cout << "Bowser ctor\n"; }
    ~Bowser() override {
        std::cout << "~Bowser releasing resources\n";
        delete[] heavy;
    }
    std::string ability() const override { return "Flame Breath"; }
};

int main() {
    Character* p = new Bowser("Bowser");
    delete p;
}
```

---

## Key Differences vs Java

1. **Compilation:** Java → bytecode/JVM. C++ → native binary.
2. **Memory model:** Java hides addresses; C++ exposes `&`, pointers.
3. **Passing:** Java passes primitives by value and objects by value of reference. C++ lets you choose value, reference, or pointer for any type.
4. **Polymorphism:** Java methods are virtual by default. In C++, you must mark with `virtual` and use references/pointers to enable dynamic dispatch; value semantics can slice.
5. **Lifetime:** Java has GC. C++ uses RAII and destructors; raw pointers require explicit delete.
6. **Slicing:** Only in C++ if you store/pass a derived object **by value** as its base.

---

## Appendix: Build Files

Before diving into the examples, it’s important to understand the different ways to compile and build C++ projects:  
1. **Directly with a compiler (e.g., `g++`)** – You can invoke the compiler manually to turn your `.cpp` source files into executables. This works well for very small projects but quickly becomes tedious when there are many files or libraries to link.  
2. **Using `make` with a Makefile** – `make` is a build automation tool. Instead of typing long compiler commands, you describe the build rules once in a Makefile. `make` then only recompiles the files that have changed, which saves time and reduces mistakes.  
3. **Using CMake** – CMake is a higher-level, cross-platform build system generator. Instead of hardcoding compiler commands, you describe your project in `CMakeLists.txt`, and CMake generates the proper build files for your platform (Makefiles, Visual Studio solutions, Ninja build files, etc.). This makes it more portable and easier to manage large projects.  

Each option has trade-offs: using the compiler directly is simplest but least scalable, Make is lightweight but Unix-centric, while CMake offers portability and advanced configuration but introduces an extra layer of tooling. Finally, when working in an IDE like CLion or Visual Studio, the **green “Run/Build” button** is essentially a shortcut that calls into your configured build system (often CMake under the hood) and then launches the executable.


### Makefile Example

```make
CXX := g++
CXXFLAGS := -std=c++17 -O2 -Wall -Wextra
SRC := $(wildcard src/*.cpp)
OBJ := $(SRC:.cpp=.o)
BIN := mariolab

all: $(BIN)

$(BIN): $(OBJ)
	$(CXX) $(CXXFLAGS) $^ -o $@

clean:
	rm -f $(OBJ) $(BIN)
```

### CMakeLists.txt Example

```cmake
cmake_minimum_required(VERSION 3.15)
project(mariolab LANGUAGES CXX)

# Require C++17
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Default build type = Release if not set
if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
  set(CMAKE_BUILD_TYPE Release CACHE STRING "Build type" FORCE)
endif()

# Collect all .cpp files from src/
file(GLOB SRC_FILES CONFIGURE_DEPENDS "${CMAKE_SOURCE_DIR}/src/*.cpp")

# Define executable
add_executable(mariolab ${SRC_FILES})

# Compiler options
if(MSVC)
  target_compile_options(mariolab PRIVATE /W4 /permissive-)
else()
  target_compile_options(mariolab PRIVATE -Wall -Wextra)
  target_compile_options(mariolab PRIVATE $<$<CONFIG:Release>:-O2>)
endif()

set(CMAKE_POSITION_INDEPENDENT_CODE ON)
```

---

**End of Notes — Good luck for your study and happy coding!**
