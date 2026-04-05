# 02 - Advanced OOP & Design Architecture

At a senior level, OOP isn't just about inheritance; it's about robust API design, SOLID principles, and concurrency-safe patterns.

---

## 🏗 SOLID Principles (Deep Dive)

### 1. Single Responsibility Principle (SRP)
**"A class should have one, and only one, reason to change."**

*   **Logic:** Avoid "God Classes". If a class handles database logic, UI logic, and business rules, any change to one will risk breaking the others.
*   **Example:**
    ```java
    // ❌ Bad: One class handles user data, DB saving, and email notifications.
    class UserHandler {
        void saveUser(User u) { /* DB logic */ }
        void sendWelcomeEmail(User u) { /* Email logic */ }
    }

    // ✅ Good: Separate responsibilities.
    class UserRepository { void save(User u) { ... } }
    class EmailService { void sendWelcomeEmail(User u) { ... } }
    ```

### 2. Open/Closed Principle (OCP)
**"Software entities should be open for extension, but closed for modification."**

*   **Logic:** You should be able to add new features without touching (and potentially breaking) existing, tested code.
*   **Example:** Use the **Strategy Pattern**.
    ```java
    // ❌ Bad: Adding a new shape requires modifying the 'AreaCalculator' class.
    class AreaCalculator {
        public double calculate(Object shape) {
            if (shape instanceof Square) return ...;
            if (shape instanceof Circle) return ...; // Constant modification
        }
    }

    // ✅ Good: New shapes implement the 'Shape' interface; calculator remains untouched.
    interface Shape { double getArea(); }
    class Square implements Shape { ... }
    class AreaCalculator {
        public double calculate(Shape s) { return s.getArea(); }
    }
    ```

### 3. Liskov Substitution Principle (LSP)
**"Subtypes must be substitutable for their base types without altering the correctness of the program."**

*   **Logic:** A child class must be able to do everything the parent can do, in a way that doesn't surprise the caller.
*   **Example:** The classic "Square-Rectangle" problem.
    ```java
    // ❌ Bad: A Square inheriting from Rectangle. 
    // Setting width on a Square also changes height, which violates expectations of a Rectangle.
    class Rectangle { void setWidth(int w); void setHeight(int h); }
    class Square extends Rectangle { 
        @Override void setWidth(int w) { super.setWidth(w); super.setHeight(w); } // Breaks LSP
    }
    ```

### 4. Interface Segregation Principle (ISP)
**"Clients should not be forced to depend on methods they do not use."**

*   **Logic:** Larger interfaces should be split into smaller, more specific ones.
*   **Example:**
    ```java
    // ❌ Bad: Robot is forced to implement eat() and sleep().
    interface Worker { void work(); void eat(); void sleep(); }

    // ✅ Good: Small, granular interfaces.
    interface Workable { void work(); }
    interface Feedable { void eat(); }
    class Robot implements Workable { public void work() { ... } }
    ```

### 5. Dependency Inversion Principle (DIP)
**"Depend on abstractions (interfaces), not on concretions (classes)."**

*   **Logic:** High-level business logic shouldn't depend on low-level implementation details (like a specific database driver).
*   **Example:** **Dependency Injection**.
    ```java
    // ❌ Bad: SwitchService is tightly coupled to LightBulb class.
    class SwitchService {
        private LightBulb bulb = new LightBulb(); // Direct dependency
        void operate() { bulb.turnOn(); }
    }

    // ✅ Good: Switch depends on a Switchable interface.
    interface Switchable { void turnOn(); }
    class SwitchService {
        private Switchable device; 
        public SwitchService(Switchable s) { this.device = s; } // Injectable!
    }
    ```


## 🏗 Immutability & The String Pool
### Why Immutability?
Immutable objects are naturally thread-safe. They have no synchronized bottlenecks, make excellent HashMap keys, and can be easily cached.

### Custom Immutable Class Rules:
1. Declare class as `final`.
2. Make fields `private final`.
3. Do not provide setters.
4. Deep copy mutable objects in constructors and getters.
```java
public final class ImmutableCar {
    private final Engine engine; // Engine must also be immutable or deep-copied
    
    public ImmutableCar(Engine engine) {
        this.engine = new Engine(engine.getType()); // Deep copy
    }
}
```
### String Internals
- **Interview Question**: *Why are Strings immutable in Java?*
  - **Answer**: Security (Strings pass DB credentials, URLs), performance (String Pool avoids duplicate allocations), and thread-safety.

## 🏗 GoF Design Patterns (Senior Context)
- **Singleton**: Often an anti-pattern when used as global state. If needed, use Enums for thread-safe, serialization-safe singletons.
- **Strategy Pattern**: Eliminates large switch statements. Commonly used with Spring by autowiring a `List<StrategyInterface>` or `Map<String, StrategyInterface>`.
- **Factory/Abstract Factory**: Hides construction complexity.
- **Observer**: Found in event-driven systems (Spring `ApplicationEventPublisher`).

### Interview Question: Composition vs. Inheritance
*Why favor composition over inheritance?*
Inheritance breaks encapsulation (the subclass depends on the implementation details of the superclass). It also creates tight coupling. Composition allows changing behavior dynamically at runtime and promotes code reuse without deep hierarchies.

---
[⬅ Back to Interview Roadmap](../README.md)
