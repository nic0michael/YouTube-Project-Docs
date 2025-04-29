---
title: "Threads in Java"
date: 2024-07-17T06:56:22Z
draft: false
---
## A. Threads Simplified

### Summary of Threads in Java

Threads in Java allow concurrent execution of two or more parts of a program to maximize the utilization of CPU. Java provides built-in support for multithreaded programming.

### Creating Threads in Java

#### 1. Extending the `Thread` Class

1. **Description**: Create a new class that extends `Thread` and override the `run` method.
2. **Code Example**:
   ```java
   class MyThread extends Thread {
       public void run() {
           System.out.println("Thread is running");
       }
   
       public static void main(String[] args) {
           MyThread t1 = new MyThread();
           t1.start(); // Start the thread
       }
   }
   ```

#### 2. Implementing the `Runnable` Interface

1. **Description**: Create a class that implements the `Runnable` interface and pass an instance of the class to a `Thread` object.
2. **Code Example**:
   ```java
   class MyRunnable implements Runnable {
       public void run() {
           System.out.println("Thread is running");
       }
   
       public static void main(String[] args) {
           MyRunnable myRunnable = new MyRunnable();
           Thread t1 = new Thread(myRunnable);
           t1.start(); // Start the thread
       }
   }
   ```

#### 3. Using Lambda Expressions (Java 8 and later)

1. **Description**: Use lambda expressions to create a `Runnable` object.
2. **Code Example**:
   ```java
   public class Main {
       public static void main(String[] args) {
           Thread t1 = new Thread(() -> System.out.println("Thread is running"));
           t1.start(); // Start the thread
       }
   }
   ```

These methods provide the basic framework for creating and starting threads in Java, allowing for concurrent execution of code.

## B. Detailed Explanation of Threads in Java

Threads are a fundamental part of Java programming that allow for concurrent execution of two or more parts of a program, leading to better utilization of CPU resources. Java provides built-in support for multithreading through the `Thread` class and the `Runnable` interface.

### Creating Threads in Java

#### 1. Extending the `Thread` Class

When a class extends `Thread`, it must override the `run` method, which is the entry point for the new thread.

**Steps**:
1. Create a new class that extends the `Thread` class.
2. Override the `run` method to define the code that should be executed by the thread.
3. Create an instance of the new class and call the `start` method to begin execution.

**Code Example**:
```java
// Step 1: Define a class that extends Thread
class MyThread extends Thread {
    // Step 2: Override the run method
    public void run() {
        // Code to be executed by the thread
        for (int i = 0; i < 5; i++) {
            System.out.println("Thread running: " + i);
            try {
                Thread.sleep(1000); // Pause for 1 second
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    // Step 3: Main method to start the thread
    public static void main(String[] args) {
        MyThread t1 = new MyThread();
        t1.start(); // Start the thread
    }
}
```

**Explanation**:
- `MyThread` extends `Thread`, so it inherits all its methods.
- The `run` method is overridden to specify the actions of the thread.
- `start` is called to start the new thread, which will call the `run` method.

#### 2. Implementing the `Runnable` Interface

When a class implements the `Runnable` interface, it must implement the `run` method. This approach is more flexible as the class can extend another class if needed.

**Steps**:
1. Create a new class that implements the `Runnable` interface.
2. Implement the `run` method to define the code that should be executed by the thread.
3. Create an instance of `Thread` and pass an instance of the new class to the `Thread` constructor.
4. Call the `start` method on the `Thread` instance to begin execution.

**Code Example**:
```java
// Step 1: Define a class that implements Runnable
class MyRunnable implements Runnable {
    // Step 2: Implement the run method
    public void run() {
        // Code to be executed by the thread
        for (int i = 0; i < 5; i++) {
            System.out.println("Thread running: " + i);
            try {
                Thread.sleep(1000); // Pause for 1 second
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    // Step 3: Main method to start the thread
    public static void main(String[] args) {
        MyRunnable myRunnable = new MyRunnable();
        Thread t1 = new Thread(myRunnable); // Pass MyRunnable instance to Thread
        t1.start(); // Start the thread
    }
}
```

**Explanation**:
- `MyRunnable` implements `Runnable`, so it must define the `run` method.
- A `Thread` instance is created, passing the `MyRunnable` instance to its constructor.
- `start` is called to start the new thread, which will call the `run` method.

#### 3. Using Lambda Expressions (Java 8 and later)

Lambda expressions simplify the creation of `Runnable` instances by eliminating the need for an anonymous class.

**Steps**:
1. Use a lambda expression to define the `run` method.
2. Create an instance of `Thread` and pass the lambda expression to the `Thread` constructor.
3. Call the `start` method on the `Thread` instance to begin execution.

**Code Example**:
```java
public class Main {
    public static void main(String[] args) {
        // Step 1: Define a lambda expression for Runnable
        Runnable myRunnable = () -> {
            // Code to be executed by the thread
            for (int i = 0; i < 5; i++) {
                System.out.println("Thread running: " + i);
                try {
                    Thread.sleep(1000); // Pause for 1 second
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        };

        // Step 2: Create a Thread instance and pass the lambda expression
        Thread t1 = new Thread(myRunnable);

        // Step 3: Start the thread
        t1.start();
    }
}
```

**Explanation**:
- A `Runnable` is defined using a lambda expression, simplifying the syntax.
- A `Thread` instance is created, passing the lambda expression to its constructor.
- `start` is called to start the new thread, which will execute the code in the lambda expression.

### Important Methods in `Thread` Class

- **`start()`**: Starts the execution of the thread.
- **`run()`**: Contains the code to be executed by the thread.
- **`sleep(long millis)`**: Causes the current thread to pause execution for a specified number of milliseconds.
- **`join()`**: Waits for the thread to die.
- **`getName()`**: Returns the name of the thread.
- **`setName(String name)`**: Sets the name of the thread.
- **`getPriority()`**: Returns the priority of the thread.
- **`setPriority(int priority)`**: Sets the priority of the thread.
- **`isAlive()`**: Tests if the thread is alive.

By using these methods and approaches, Java developers can effectively manage and utilize threads for concurrent programming.

## C. Synchronized Methods
To prevent other threads from accessing a method while your thread is using it, you can use the `synchronized` keyword. This ensures that only one thread can execute the method at a time, providing thread-safe access to the method.

**Description**: 
The `synchronized` keyword is used to lock an object for any shared resource. When a method is synchronized, only one thread can access it at a time for a given object.

**Usage**:
```java
class Counter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }

    public int getCount() {
        return count;
    }
}

public class Main {
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();

        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) counter.increment();
        });

        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) counter.increment();
        });

        t1.start();
        t2.start();

        t1.join();
        t2.join();

        System.out.println("Count: " + counter.getCount()); // Expected: 2000
    }
}
```

In this example, the `increment` method is synchronized, ensuring that only one thread can execute it at a time for a given `Counter` object, thus preventing race conditions.
## D. Simplified Issues with Threads in Java

1. **Thread Synchronization**
   - **Issue**: Multiple threads accessing shared resources can cause data inconsistency.
   - **Solution**: Use `synchronized` keyword to control access.

   **Code Example**:
   ```java
   class Counter {
       private int count = 0;

       public synchronized void increment() {
           count++;
       }

       public int getCount() {
           return count;
       }
   }

   public class Main {
       public static void main(String[] args) throws InterruptedException {
           Counter counter = new Counter();

           Thread t1 = new Thread(() -> {
               for (int i = 0; i < 1000; i++) counter.increment();
           });

           Thread t2 = new Thread(() -> {
               for (int i = 0; i < 1000; i++) counter.increment();
           });

           t1.start();
           t2.start();

           t1.join();
           t2.join();

           System.out.println("Count: " + counter.getCount()); // Expected: 2000
       }
   }
   ```

2. **Deadlock**
   - **Issue**: Threads waiting on each other to release resources can lead to a standstill.
   - **Solution**: Avoid nested locks and use a consistent order for acquiring locks.

   **Code Example**:
   ```java
   class A {
       synchronized void methodA(B b) {
           System.out.println("Thread 1 starts methodA");
           try { Thread.sleep(100); } catch (InterruptedException e) {}
           b.last();
       }

       synchronized void last() {
           System.out.println("Inside A.last()");
       }
   }

   class B {
       synchronized void methodB(A a) {
           System.out.println("Thread 2 starts methodB");
           try { Thread.sleep(100); } catch (InterruptedException e) {}
           a.last();
       }

       synchronized void last() {
           System.out.println("Inside B.last()");
       }
   }

   public class DeadlockDemo implements Runnable {
       A a = new A();
       B b = new B();

       DeadlockDemo() {
           Thread t = new Thread(this);
           t.start();
           a.methodA(b);
       }

       public void run() {
           b.methodB(a);
       }

       public static void main(String[] args) {
           new DeadlockDemo();
       }
   }
   ```

3. **Race Conditions**
   - **Issue**: The outcome of a program depends on the timing of thread execution.
   - **Solution**: Synchronize access to shared resources.

4. **Thread Interference**
   - **Issue**: Threads modifying shared data at the same time can lead to unexpected results.
   - **Solution**: Use `synchronized` to prevent interference.

5. **Resource Starvation**
   - **Issue**: Threads are denied necessary resources, causing indefinite waiting.
   - **Solution**: Use proper thread management and priority settings.

6. **Thread Lifecycle Management**
   - **Issue**: Managing creation, execution, and termination of threads can be complex.
   - **Solution**: Use `ExecutorService` and thread pools.

   **Code Example**:
   ```java
   import java.util.concurrent.ExecutorService;
   import java.util.concurrent.Executors;

   public class Main {
       public static void main(String[] args) {
           ExecutorService executor = Executors.newFixedThreadPool(2);

           Runnable task = () -> {
               for (int i = 0; i < 5; i++) {
                   System.out.println(Thread.currentThread().getName() + " is running");
                   try { Thread.sleep(1000); } catch (InterruptedException e) { e.printStackTrace(); }
               }
           };

           executor.submit(task);
           executor.submit(task);
           executor.shutdown();
       }
   }
   ```

7. **Memory Consistency Errors**
   - **Issue**: Different threads have inconsistent views of shared data.
   - **Solution**: Use `volatile` variables and proper synchronization.

   **Code Example**:
   ```java
   class Counter {
       private volatile int count = 0;

       public void increment() {
           count++;
       }

       public int getCount() {
           return count;
       }
   }

   public class Main {
       public static void main(String[] args) throws InterruptedException {
           Counter counter = new Counter();

           Thread t1 = new Thread(() -> {
               for (int i = 0; i < 1000; i++) counter.increment();
           });

           Thread t2 = new Thread(() -> {
               for (int i = 0; i < 1000; i++) counter.increment();
           });

           t1.start();
           t2.start();

           t1.join();
           t2.join();

           System.out.println("Count: " + counter.getCount()); // Expected: 2000
       }
   }
   ```

8. **Performance Overhead**
   - **Issue**: Managing many threads can reduce performance.
   - **Solution**: Use thread pools and concurrency utilities.

9. **Exception Handling**
   - **Issue**: Unhandled exceptions in threads can cause issues.
   - **Solution**: Use proper exception handling within threads.

   **Code Example**:
   ```java
   public class Main {
       public static void main(String[] args) {
           Thread t1 = new Thread(() -> {
               try {
                   // Simulate some work
                   throw new RuntimeException("Exception in thread");
               } catch (Exception e) {
                   System.out.println("Caught exception: " + e.getMessage());
               }
           });

           t1.setUncaughtExceptionHandler((t, e) -> {
               System.out.println("Uncaught exception: " + e.getMessage());
           });

           t1.start();
       }
   }
   ```

## E. Issues with threads in Detail
When working with threads in Java, several issues and considerations must be addressed to ensure correct and efficient execution of concurrent programs. Here are some key issues:

### 1. **Thread Synchronization**
- **Issue**: When multiple threads access shared resources (like variables, objects, or files) simultaneously, it can lead to inconsistent or incorrect results.
- **Solution**: Use synchronization mechanisms such as the `synchronized` keyword, locks (`ReentrantLock`), or concurrent collections to control access to shared resources.

**Example**:
```java
class Counter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }

    public int getCount() {
        return count;
    }
}

public class Main {
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();
        
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });

        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });

        t1.start();
        t2.start();

        t1.join();
        t2.join();

        System.out.println("Count: " + counter.getCount()); // Expected: 2000
    }
}
```

### 2. **Deadlock**
- **Issue**: Occurs when two or more threads are waiting indefinitely for each other to release locks on resources, creating a cycle of dependencies.
- **Solution**: Avoid nested locks when possible, use try-lock mechanisms, or establish a strict lock acquisition order.

**Example of Deadlock**:
```java
class A {
    synchronized void methodA(B b) {
        System.out.println("Thread 1 starts execution of methodA");
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {}
        b.last();
    }
    
    synchronized void last() {
        System.out.println("Inside A.last()");
    }
}

class B {
    synchronized void methodB(A a) {
        System.out.println("Thread 2 starts execution of methodB");
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {}
        a.last();
    }
    
    synchronized void last() {
        System.out.println("Inside B.last()");
    }
}

public class DeadlockDemo implements Runnable {
    A a = new A();
    B b = new B();
    
    DeadlockDemo() {
        Thread t = new Thread(this);
        t.start();
        a.methodA(b); // Main thread
    }
    
    public void run() {
        b.methodB(a); // Other thread
    }
    
    public static void main(String[] args) {
        new DeadlockDemo();
    }
}
```

### 3. **Race Conditions**
- **Issue**: Occurs when the outcome of a program depends on the sequence or timing of uncontrollable events such as thread scheduling.
- **Solution**: Use synchronization to control access to shared resources and ensure predictable execution order.

### 4. **Thread Interference**
- **Issue**: When multiple threads interfere with each other by simultaneously modifying shared data, leading to unexpected results.
- **Solution**: Ensure proper synchronization and use atomic variables or concurrent data structures.

### 5. **Resource Starvation**
- **Issue**: When one or more threads are perpetually denied access to resources needed for execution, typically due to improper thread priority settings.
- **Solution**: Avoid priority inversion by carefully managing thread priorities and ensuring fair access to resources.

### 6. **Thread Lifecycle Management**
- **Issue**: Managing the lifecycle of threads, such as creation, execution, and termination, can be complex.
- **Solution**: Use higher-level concurrency utilities like `ExecutorService`, thread pools, and frameworks that abstract thread management.

**Example using ExecutorService**:
```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class Main {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(2);

        Runnable task = () -> {
            for (int i = 0; i < 5; i++) {
                System.out.println(Thread.currentThread().getName() + " is running");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        };

        executor.submit(task);
        executor.submit(task);
        executor.shutdown();
    }
}
```

### 7. **Memory Consistency Errors**
- **Issue**: When different threads have inconsistent views of the same data due to caching and reordering of instructions by the compiler or CPU.
- **Solution**: Use `volatile` variables for visibility, and proper synchronization to ensure memory consistency.

**Example with volatile**:
```java
class Counter {
    private volatile int count = 0;

    public void increment() {
        count++;
    }

    public int getCount() {
        return count;
    }
}

public class Main {
    public static void main(String[] args) {
        Counter counter = new Counter();
        
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });

        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });

        t1.start();
        t2.start();

        try {
            t1.join();
            t2.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("Count: " + counter.getCount()); // Expected: 2000
    }
}
```

### 8. **Performance Overhead**
- **Issue**: Creating and managing a large number of threads can lead to significant performance overhead.
- **Solution**: Use thread pools and other concurrency utilities to manage threads efficiently.

### 9. **Exception Handling**
- **Issue**: Unhandled exceptions in one thread do not affect other threads, but can lead to resource leaks or incomplete execution.
- **Solution**: Use proper exception handling within threads and leverage uncaught exception handlers for global handling.

**Example**:
```java
public class Main {
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            try {
                // Simulate some work
                throw new RuntimeException("Exception in thread");
            } catch (Exception e) {
                System.out.println("Caught exception: " + e.getMessage());
            }
        });

        t1.setUncaughtExceptionHandler((t, e) -> {
            System.out.println("Uncaught exception: " + e.getMessage());
        });

        t1.start();
    }
}
```

By considering these issues and applying the appropriate solutions, developers can write robust, efficient, and correct multithreaded applications in Java.


## F. RECAP
Here's a quick recap of the important issues with Java threads that we covered:

1. **Thread Synchronization**: Use the `synchronized` keyword or locks to control access to shared resources.
   ```java
   public synchronized void increment() {
       count++;
   }
   ```

2. **Deadlock**: Avoid nested locks and use a consistent order for acquiring locks.
   ```java
   class A {
       synchronized void methodA(B b) { ... }
       synchronized void last() { ... }
   }
   ```

3. **Race Conditions**: Synchronize access to shared resources to ensure predictable execution.
   ```java
   public synchronized void increment() {
       count++;
   }
   ```

4. **Thread Interference**: Use `synchronized` to prevent threads from interfering with each other.
   ```java
   public synchronized void increment() {
       count++;
   }
   ```

5. **Resource Starvation**: Manage thread priorities and access to resources properly.
   ```java
   // Use fair locks or thread scheduling
   ```

6. **Thread Lifecycle Management**: Use `ExecutorService` and thread pools to manage thread lifecycles.
   ```java
   ExecutorService executor = Executors.newFixedThreadPool(2);
   executor.submit(task);
   ```

7. **Memory Consistency Errors**: Use `volatile` for visibility and proper synchronization.
   ```java
   private volatile int count = 0;
   ```

8. **Performance Overhead**: Use thread pools and concurrency utilities to reduce overhead.
   ```java
   ExecutorService executor = Executors.newFixedThreadPool(2);
   ```

9. **Exception Handling**: Handle exceptions within threads and use uncaught exception handlers.
   ```java
   t1.setUncaughtExceptionHandler((t, e) -> {
       System.out.println("Uncaught exception: " + e.getMessage());
   });
   ```

By understanding and addressing these issues, you can write robust and efficient multithreaded applications in Java. 

