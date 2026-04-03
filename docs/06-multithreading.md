# 06 - Multithreading & Concurrency

Multithreading is a programming model that allows multiple threads to run concurrently within a single process.

---

## 🏗 Developing Threads
Threads can be created in two ways:
1. Extending the `Thread` class.
2. Implementing the `Runnable` interface (Recommended).

### 🖇 Extending Thread Class
```java
class MyThread extends Thread {
    public void run() {
        System.out.println("Thread is running!");
    }
}
MyThread mt = new MyThread();
mt.start();
```

### 🖇 Implementing Runnable Interface
Using `Runnable` is better for flexibility since a class can implement multiple interfaces.
```java
class MyRunnable implements Runnable {
    public void run() {
        System.out.println("Runnable is running!");
    }
}
Thread t = new Thread(new MyRunnable());
t.start();
```

## 🧩 Multithreaded Synchronization
Use `synchronized` to allow only one thread to access a resource at a time. This prevents data corruption.

```java
public class Counter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }

    public int getCount() {
        return count;
    }
}
```

## 🚦 Thread Life Cycle
- **New**: Thread instance created.
- **Runnable**: `start()` called.
- **Running**: Code in `run()` is executing.
- **Waiting/Blocked/Timed-waiting**: Waiting for another thread or sleep.
- **Terminated/Dead**: Thread execution finishes.

## ♾ Executors Framework
The `ExecutorService` provides a way to manage higher-level thread execution.

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

ExecutorService executor = Executors.newFixedThreadPool(5);
executor.execute(() -> {
    System.out.println("Asynchronous task running in executor.");
});
executor.shutdown();
```

---
[⬅ Back to Roadmap](../README.md)
