# 02 - Advanced OOP & Design Architecture

At a senior level, OOP isn't just about inheritance; it's about robust API design, SOLID principles, and concurrency-safe patterns.

---

## 🏗 SOLID Principles in the Real World
- **Single Responsibility (SRP)**: God classes cause merge conflicts and test fragility. Break them down.
- **Open/Closed (OCP)**: Favor composition and strategy patterns over complex `if/else` ladders. Let new behavior be injected.
- **Liskov Substitution (LSP)**: Derived classes must be substitutable for base classes without breaking correctness. (e.g., A Square inheriting from Rectangle violates LSP if `setWidth` changes height too).
- **Interface Segregation (ISP)**: Create small, specific interfaces. Don't force clients to implement methods they don't use.
- **Dependency Inversion (DIP)**: Depend on abstractions (interfaces), not concretions. Spring DI uses this heavily.

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
