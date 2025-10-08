# Lab 5: Threaded Math Calculator

#### Learning Objectives

- Understand thread creation and the Runnable interface
- Learn the difference between `run()` and `start()` methods
- Practice thread lifecycle management with `join()`
- Observe concurrent execution and thread interleaving
- Collect results from multiple threads

#### Prerequisites

- Basic Java programming knowledge
- Understanding of classes and interfaces
- Familiarity with basic mathematical operations
- Completion of previous labs on process basics

#### Introduction

Threading is a fundamental concept in concurrent programming that allows multiple tasks to execute simultaneously within
a single process. Unlike processes (which you studied in Lab 3), threads share the same memory space, making them
lighter weight and more efficient for certain types of parallel work.

This lab explores thread creation, execution, and coordination through a practical math calculator. You'll create
threads that independently calculate different mathematical operations: Fibonacci numbers and sum of squares. The
calculations themselves are just busy work - the real learning is understanding how threads execute concurrently and how
to coordinate their completion.

#### What You'll Implement

You need to complete **2 methods** in a single Java file:

1. **fibonacciCalculator()** - Computes Fibonacci number in a separate thread
2. **sumOfSquaresCalculator()** - Computes sum of squares in a separate thread

The main method and base structure are provided for you. Each method will create and return a `Runnable` that performs
the calculation when executed in a thread.

#### Lab File Structure

**MathCalculatorLab.java** - Single file containing:

- **CalculatorBase class** (PROVIDED) - Stores calculation parameters and results
- **fibonacciCalculator() method** (TODO) - Returns a Runnable that calculates Fibonacci
- **sumOfSquaresCalculator() method** (TODO) - Returns a Runnable that calculates sum of squares
- **main() method** (PROVIDED) - Entry point with menu-driven interface

You only need to complete the **2 TODO methods**.

#### Project Setup

1. Download MathCalculatorLab.java
2. Complete the **2 TODOs** (fibonacciCalculator and sumOfSquaresCalculator methods)
3. Compile: `javac MathCalculatorLab.java`
4. Run: `java MathCalculatorLab`

#### The Methods You'll Implement

**Method 1: fibonacciCalculator(CalculatorBase calc)**

This method creates and returns a `Runnable` that calculates the nth Fibonacci number using an iterative approach. The
Fibonacci sequence is: 0, 1, 1, 2, 3, 5, 8, 13, 21...

**What you'll learn:**

- How to create a Runnable using a lambda expression or anonymous class
- Working with iterative algorithms in threads
- Managing state within a thread's execution
- How different threads can run different algorithms simultaneously

**Parameters:**

- `calc` - CalculatorBase object containing input value `n` and storage for `result`

**Returns:** `Runnable` that performs the Fibonacci calculation

**Example:** fibonacci(7) = 13

**Formula:** fib(n) = fib(n-1) + fib(n-2), with fib(0) = 0, fib(1) = 1

**Method 2: sumOfSquaresCalculator(CalculatorBase calc)**

This method creates and returns a `Runnable` that calculates the sum of squares from 1 to n (1² + 2² + 3² + ... + n²).

**What you'll learn:**

- Implementing loop-based calculations in threads
- How threads maintain their own execution context
- Comparing execution patterns across different calculation types

**Parameters:**

- `calc` - CalculatorBase object containing input value `n` and storage for `result`

**Returns:** `Runnable` that performs the sum of squares calculation

**Example:** sumOfSquares(5) = 1 + 4 + 9 + 16 + 25 = 55

**Formula:** sum = 1² + 2² + 3² + ... + n²

#### Expected Output

**Option 1: Single Calculation Demo**

```
=== Threaded Math Calculator ===
1. Single calculation demo
2. Multiple concurrent calculations
3. Exit

Enter your choice: 1

=== Single Calculation Demo ===
1. Fibonacci
2. Sum of Squares

Choose calculator: 1
Enter a number (1-20): 8

Creating Fibonacci calculator thread...
Thread-0 starting calculation for 8
Thread-0 computing: fib(8)
Thread-0 completed: fibonacci(8) = 21

Main thread waiting for calculation to complete...
Thread-0 has finished execution

Result: fibonacci(8) = 21
Execution time: 45ms
```

**Option 2: Multiple Concurrent Calculations**

```
=== Threaded Math Calculator ===
1. Single calculation demo
2. Multiple concurrent calculations
3. Exit

Enter your choice: 2

=== Multiple Concurrent Calculations ===
Enter a number (1-20): 10

Creating all calculator threads...
Creating thread for Fibonacci...
Creating thread for Sum of Squares...

Starting both threads concurrently...

Thread-0 starting calculation for 10
Thread-1 starting calculation for 10
Thread-0 computing: fib(10)
Thread-1 computing: 1² + 2² + 3² + ... + 10²
Thread-1 completed: sumOfSquares(10) = 385
Thread-0 completed: fibonacci(10) = 55

Main thread waiting for all calculations to complete...
Thread-0 has finished execution
Thread-1 has finished execution

=== Results ===
Fibonacci(10) = 55
Sum of Squares(10) = 385

Total execution time: 62ms
All calculations completed successfully!
```

#### Key Threading Concepts You'll Use

**The Runnable Interface**

The `Runnable` interface is Java's way of defining a task that can be executed by a thread. It has a single
method: `run()`. When you implement Runnable, you're creating a blueprint for work that a thread can perform.

You can create a Runnable in several ways:

**Using a lambda expression (modern Java):**

```java
Runnable task=()->{
    System.out.println("Running in thread: "+Thread.currentThread().getName());
    // Do calculation work
};
```

**Using an anonymous class (traditional approach):**

```java
Runnable task = new Runnable() {
    @Override
    public void run() {
        System.out.println("Running in thread: " + Thread.currentThread().getName());
        // Do calculation work
    }
};
```

**Creating and Starting Threads**

This is where many students get confused. There's a critical difference between calling `run()` directly and
using `start()`:

```java
// WRONG - This just calls the method in the current thread
Runnable task=()->System.out.println("Hello");
task.run(); // Runs in main thread - no concurrency!

// CORRECT - This creates a new thread
Runnable task=()->System.out.println("Hello");
Thread thread=new Thread(task);
thread.start(); // Runs in new thread - true concurrency!
```

**Key point:** `start()` tells the JVM to create a new thread and call `run()` in that thread. Calling `run()` directly
is just a normal method call.

**Thread.join() - Waiting for Completion**

The `join()` method makes the calling thread wait until the target thread completes. This is essential for coordinating
threads and collecting results:

```java
Thread thread=new Thread(task);
thread.start(); // Thread begins execution

// Do other work here...
thread.join(); // Wait for thread to finish

// Now we can safely access the results
System.out.println("Thread completed!");
```

#### Thread Lifecycle

Understanding the thread lifecycle helps you reason about concurrent execution:

1. **NEW** - Thread object created but not started
2. **RUNNABLE** - Thread is executing (or ready to execute)
3. **BLOCKED/WAITING** - Thread is waiting for a resource or condition
4. **TERMINATED** - Thread has completed execution

```java
Runnable task=()->System.out.println("Working...");
        Thread thread=new Thread(task);  // NEW state
        thread.start();                    // RUNNABLE state
// thread executes run() method
        thread.join();                     // Wait for TERMINATED state
```

#### Implementation Guide

**TODO 1: fibonacciCalculator(CalculatorBase calc)**

**Instructions:**
Create and return a Runnable that:

1. Prints a message showing which thread is computing and the input value
2. Handles base cases (n <= 1): result should be n
3. Uses an iterative loop with two variables (prev and curr) to calculate Fibonacci
4. Adds `Thread.sleep(5)` inside the loop to simulate computation work
5. Stores the final result in `calc.result`
6. Prints a completion message with the result

**Hint:** Use a lambda expression or anonymous class to create the Runnable. Access `calc.n` for the input value and
store in `calc.result`.

**Expected output format:**

```
Thread-X computing: fib(n)
Thread-X completed: fibonacci(n) = result
```

**TODO 2: sumOfSquaresCalculator(CalculatorBase calc)**

**Instructions:**
Create and return a Runnable that:

1. Prints a message showing which thread is computing
2. Initializes a sum variable to 0
3. Loops from 1 to n, adding i² to the sum
4. Adds `Thread.sleep(5)` inside the loop to simulate computation work
5. Stores the final result in `calc.result`
6. Prints a completion message with the result

**Hint:** Use a lambda expression or anonymous class to create the Runnable. Access `calc.n` for the input value and
store in `calc.result`.

**Expected output format:**

```
Thread-X computing: 1² + 2² + 3² + ... + n²
Thread-X completed: sumOfSquares(n) = result
```

#### Complete File Template

Here's the structure of **MathCalculatorLab.java** that you'll be working with:

```java
package academy.javapro.lab05;

import java.util.Scanner;

public class MathCalculatorLab {

    // Simple class to hold calculation data (PROVIDED - DO NOT MODIFY)
    static class CalculatorBase {
        int n;
        long result;

        public CalculatorBase(int n) {
            this.n = n;
            this.result = 0;
        }
    }

    // TODO 1: Implement this method
    // Creates a Runnable that calculates the nth Fibonacci number
    // Fibonacci sequence: 0, 1, 1, 2, 3, 5, 8, 13, 21, 34...
    // Formula: fib(n) = fib(n-1) + fib(n-2)
    // Base cases: fib(0) = 0, fib(1) = 1
    //
    // The Runnable should:
    // 1. Print: "Thread-X computing: fib(n)"
    // 2. Calculate Fibonacci iteratively using two variables (prev, curr)
    // 3. Include Thread.sleep(5) in the loop to simulate work
    // 4. Store result in calc.result
    // 5. Print: "Thread-X completed: fibonacci(n) = result"
    //
    // Example: fibonacci(8) = 21
    public static Runnable fibonacciCalculator(CalculatorBase calc) {
        // TODO: Return a Runnable (use lambda or anonymous class)
        return null; // Replace this
    }

    // TODO 2: Implement this method
    // Creates a Runnable that calculates sum of squares from 1 to n
    // Formula: 1² + 2² + 3² + ... + n²
    //
    // The Runnable should:
    // 1. Print: "Thread-X computing: 1² + 2² + 3² + ... + n²"
    // 2. Loop from 1 to n, adding i*i to result
    // 3. Include Thread.sleep(5) in the loop to simulate work
    // 4. Store result in calc.result
    // 5. Print: "Thread-X completed: sumOfSquares(n) = result"
    //
    // Example: sumOfSquares(5) = 1 + 4 + 9 + 16 + 25 = 55
    public static Runnable sumOfSquaresCalculator(CalculatorBase calc) {
        // TODO: Return a Runnable (use lambda or anonymous class)
        return null; // Replace this
    }

    // Main method (PROVIDED - DO NOT MODIFY)
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        boolean running = true;

        while (running) {
            System.out.println("\n=== Threaded Math Calculator ===");
            System.out.println("1. Single calculation demo");
            System.out.println("2. Multiple concurrent calculations");
            System.out.println("3. Exit");
            System.out.print("\nEnter your choice: ");

            int choice = scanner.nextInt();

            switch (choice) {
                case 1:
                    runSingleDemo(scanner);
                    break;
                case 2:
                    runMultipleDemo(scanner);
                    break;
                case 3:
                    running = false;
                    System.out.println("Goodbye!");
                    break;
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        }

        scanner.close();
    }

    // Helper method for single calculation (PROVIDED - DO NOT MODIFY)
    private static void runSingleDemo(Scanner scanner) {
        System.out.println("\n=== Single Calculation Demo ===");
        System.out.println("1. Fibonacci");
        System.out.println("2. Sum of Squares");
        System.out.print("\nChoose calculator: ");

        int calcChoice = scanner.nextInt();
        System.out.print("Enter a number (1-20): ");
        int n = scanner.nextInt();

        if (n < 1 || n > 20) {
            System.out.println("Number must be between 1 and 20");
            return;
        }

        CalculatorBase calc = new CalculatorBase(n);
        Runnable task = null;
        String calcName = "";

        if (calcChoice == 1) {
            calcName = "Fibonacci";
            task = fibonacciCalculator(calc);
        } else if (calcChoice == 2) {
            calcName = "Sum of Squares";
            task = sumOfSquaresCalculator(calc);
        } else {
            System.out.println("Invalid calculator choice");
            return;
        }

        System.out.println("\nCreating " + calcName + " calculator thread...");
        long startTime = System.currentTimeMillis();

        Thread thread = new Thread(task);
        System.out.println(thread.getName() + " starting calculation for " + n);
        thread.start();

        try {
            System.out.println("\nMain thread waiting for calculation to complete...");
            thread.join();
            System.out.println(thread.getName() + " has finished execution");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        long endTime = System.currentTimeMillis();
        System.out.println("\nResult: " + calcName.toLowerCase() + "(" + n + ") = " + calc.result);
        System.out.println("Execution time: " + (endTime - startTime) + "ms");
    }

    // Helper method for multiple calculations (PROVIDED - DO NOT MODIFY)
    private static void runMultipleDemo(Scanner scanner) {
        System.out.println("\n=== Multiple Concurrent Calculations ===");
        System.out.print("Enter a number (1-20): ");
        int n = scanner.nextInt();

        if (n < 1 || n > 20) {
            System.out.println("Number must be between 1 and 20");
            return;
        }

        System.out.println("\nCreating all calculator threads...");

        CalculatorBase fibCalc = new CalculatorBase(n);
        CalculatorBase sumCalc = new CalculatorBase(n);

        System.out.println("Creating thread for Fibonacci...");
        Runnable fibTask = fibonacciCalculator(fibCalc);
        Thread fibThread = new Thread(fibTask);

        System.out.println("Creating thread for Sum of Squares...");
        Runnable sumTask = sumOfSquaresCalculator(sumCalc);
        Thread sumThread = new Thread(sumTask);

        System.out.println("\nStarting both threads concurrently...\n");
        long startTime = System.currentTimeMillis();

        fibThread.start();
        sumThread.start();

        try {
            System.out.println("\nMain thread waiting for all calculations to complete...");
            fibThread.join();
            System.out.println(fibThread.getName() + " has finished execution");

            sumThread.join();
            System.out.println(sumThread.getName() + " has finished execution");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        long endTime = System.currentTimeMillis();

        System.out.println("\n=== Results ===");
        System.out.println("Fibonacci(" + n + ") = " + fibCalc.result);
        System.out.println("Sum of Squares(" + n + ") = " + sumCalc.result);
        System.out.println("\nTotal execution time: " + (endTime - startTime) + "ms");
        System.out.println("All calculations completed successfully!");
    }
}
```

#### Why These Concepts Matter

Threading is everywhere in modern software. When you browse the web, one thread renders the page while another downloads
images. When you use a word processor, one thread handles your typing while another does spell checking. Mobile apps use
threads to keep the UI responsive while doing background work.

Understanding threads is essential for:

- **Responsive user interfaces** - Keep UI thread free while doing work
- **Server applications** - Handle multiple client requests simultaneously
- **Data processing** - Process large datasets in parallel
- **Game development** - Update physics, render graphics, and play sounds concurrently
- **Scientific computing** - Distribute calculations across CPU cores

Even though you're just calculating math operations, you're learning the fundamental patterns for coordinating
concurrent work - patterns used in everything from web servers to video games to scientific simulations.

The math calculations are just vehicles for understanding concurrency. The real lessons are:

- **Concurrent execution** - Multiple tasks running simultaneously
- **Thread coordination** - Using join() to synchronize threads
- **Non-determinism** - Understanding that thread execution order varies
- **Thread lifecycle** - Creating, starting, and completing threads
- **Result collection** - Safely gathering data from concurrent computations

#### Analysis Questions

After completing the lab, consider:

1. **What's the difference between calling `task.run()` and `new Thread(task).start()`?** Try both and observe what
   happens. Which one creates actual concurrency?

2. **Why do the threads finish in different orders each time you run option 2?** What does this tell you about
   concurrent execution?

3. **What would happen if you didn't call `join()` before accessing the results?** Try removing the join() calls - what
   goes wrong?

4. **How does thread execution interleave?** Notice how the console output from different threads mixes together. Is
   this random?

5. **Why do we need the `Thread.sleep()` calls in the calculations?** What happens if you remove them? Does it affect
   correctness or just visibility?

6. **What happens to the `calc` object when you create the Runnable?** How does the thread access the same
   CalculatorBase instance?

7. **Can you use a lambda expression instead of an anonymous class?** Which style is more concise? Which is clearer?

#### Submission Requirements

After completing your work:

```bash
git add MathCalculatorLab.java
git commit -m "completed lab 4 - threaded math calculator"
git push origin main
```

**Include:**

- Complete MathCalculatorLab.java with both TODOs implemented
- Screenshots showing both menu options working correctly
- Test with various input values (try 5, 8, 10, and 15)
- Verify threads execute concurrently (check for interleaved output in option 2)
- Ensure all results are mathematically correct:
    - fibonacci(5) = 5
    - fibonacci(8) = 21
    - fibonacci(10) = 55
    - sumOfSquares(5) = 55
    - sumOfSquares(8) = 204
    - sumOfSquares(10) = 385

**Testing Checklist:**

- [ ] Code compiles without errors or warnings
- [ ] Option 1 works for both Fibonacci and Sum of Squares
- [ ] Option 2 shows interleaved output from both threads
- [ ] Results are mathematically correct
- [ ] Thread names appear in output (Thread-0, Thread-1)
- [ ] "Main thread waiting..." messages appear
- [ ] Both threads complete successfully
- [ ] No errors or exceptions occur

---

**Pro Tip:** Run option 2 (multiple calculations) several times and observe how the output order changes each time. This
demonstrates the non-deterministic nature of concurrent execution - a fundamental concept in parallel programming!