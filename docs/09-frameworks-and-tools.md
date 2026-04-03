# 09 - Frameworks & Java Tools

After mastering core Java, developer tool knowledge is essential for real-world applications.

---

## 🏗 Maven & Gradle (Build Tools)
Build tools automate dependency management and project lifecycle management.

### 🖇 Maven (`pom.xml`)
A XML-based configuration tool for managing dependencies.
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <version>3.0.0</version>
    </dependency>
</dependencies>
```

### 🖇 Gradle (`build.gradle`)
A Groovy or Kotlin DSL-based build tool.
```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
}
```

## 🏗 Spring Boot (Modern Enterprise Framework)
Spring Boot provides the fastest way to build standalone, production-grade applications.
```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

## 🏗 Hibernate (ORM)
Hibernate maps Java objects to relational database tables.
```java
@Entity
@Table(name = "Users")
public class User {
    @Id
    private Long id;
    private String name;
}
```

---
[⬅ Back to Roadmap](../README.md)
