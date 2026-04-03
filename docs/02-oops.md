# 02 - Object-Oriented Programming (OOPs)

Java is a multi-paradigm language, but primarily known for its OOP features.

---

## 🏗 Classes & Objects
A **Class** is a blueprint/template, and an **Object** is an instance of a class.

```java
public class Person {
    String name;
    int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public void display() {
        System.out.println("Name: " + name + ", Age: " + age);
    }
}
```

## 🪜 Inheritance
Inheritance allows one class to inherit the properties and behavior of another.
```java
// Parent Class
class Animal {
    void eat() {
        System.out.println("Animal eats...");
    }
}

// Child Class
class Dog extends Animal {
    void bark() {
        System.out.println("Dog barks...");
    }
}
```

## ♾ Polymorphism
Polymorphism allows a single action to be performed in different ways.

### 🧩 Method Overloading (Compile-time)
```java
class Calculator {
    int add(int a, int b) { return a + b; }
    double add(double a, double b) { return a + b; }
}
```

### 🔁 Method Overriding (Run-time)
```java
class SuperClass {
    void show() { System.out.println("Superclass show()"); }
}

class SubClass extends SuperClass {
    @Override
    void show() { System.out.println("Subclass show()"); }
}
```

## 🧼 Abstraction
Abstraction hides the implementation details and only reveals the functionality.

### 🏛 Abstract Class
```java
abstract class Shape {
    abstract void draw();
    void description() { System.out.println("Drawing a shape..."); }
}
```

### 🖇 Interface
```java
interface Drawable {
    void draw();
}
```

## 🔒 Encapsulation
Encapsulation involves wrapping data (variables) and code (methods) together as a single unit. It's achieved through access modifiers.

```java
public class Employee {
    private String name; // Private variable

    public String getName() { // Public getter
        return name;
    }

    public void setName(String name) { // Public setter
        this.name = name;
    }
}
```

---
[⬅ Back to Roadmap](../README.md)
