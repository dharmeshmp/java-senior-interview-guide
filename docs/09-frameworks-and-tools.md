# 09 - Enterprise Frameworks (Spring Architecture)

Most senior Java roles require deep expertise in Spring internals, not just knowing the annotations.

---

## 🏗 Spring Container: BeanFactory vs ApplicationContext
- `BeanFactory`: Basic DI container. Beans are lazily instantiated.
- `ApplicationContext`: Advanced container. Eagerly instantiates Singletons at startup. Provides Event Publishing, AOP, and i18n.

## 🏗 Spring Bean Lifecycle
1. Instantiate bean object.
2. Populate properties (Dependency Injection).
3. `BeanNameAware`, `BeanFactoryAware`.
4. `BeanPostProcessor.postProcessBeforeInitialization` (Custom logic).
5. `@PostConstruct` / `afterPropertiesSet()`.
6. `BeanPostProcessor.postProcessAfterInitialization` (Where AOP proxies are actually created).
7. Bean is ready for use.
8. `@PreDestroy` / `destroy()`.

## 🏗 Spring Boot Auto-Configuration Magic
Spring Boot works via `@EnableAutoConfiguration` (included in `@SpringBootApplication`).
- It uses `spring.factories` or `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`.
- Checks the classpath for conditions using `@ConditionalOnClass`, `@ConditionalOnMissingBean`. If Tomcat is on the classpath, it sets up an Embedded Tomcat.

## 🏗 AOP (Aspect-Oriented Programming)
Used for cross-cutting concerns (Logging, Security, Transactions).
- Spring AOP uses **Dynamic Proxies**.
- If a class implements an interface, Spring uses **JDK Dynamic Proxies**.
- If a class has no interface, Spring uses **CGLIB** to subclass the actual class.

## 🏗 Spring vs Spring Boot
While **Spring** is the core framework providing Dependency Injection and MVC, **Spring Boot** is built on top of Spring to solve the "configuration nightmare".

| Feature | Core Spring Framework | Spring Boot |
| :--- | :--- | :--- |
| **Setup** | Requires extensive XML or Java-based configuration. | Auto-configured based on classpath (`@EnableAutoConfiguration`). |
| **Server** | Needs an external web server (like Tomcat or WebLogic) to deploy WAR files. | Comes with Embedded Tomcat/Jetty/Undertow (runs as a standalone JAR). |
| **Dependencies** | Requires you to manage component versions individually, prone to version conflicts. | Uses **Starter POMs** (`spring-boot-starter-web`) which bundle compatible dependency versions. |
| **Monitoring** | Developers build their own health checks. | Provides **Spring Boot Actuator** out-of-the-box for production-ready metrics and health checks. |

## 🏗 Crucial Spring Boot Annotations
- **`@SpringBootApplication`**: A combination of `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan`.
- **`@RestController`**: A combination of `@Controller` and `@ResponseBody`. Tells Spring to serialize responses directly into JSON/XML instead of rendering a View.
- **`@Autowired`**: Injects dependencies by type. *(Note: Constructor injection is preferred over field injection for testing and immutability).*
- **`@Value`**: Injects values from properties files into variables.
- **`@ConfigurationProperties`**: Binds a group of related properties from `application.yml` directly into a strongly-typed Java POJO.
- **`@Qualifier`**: Used alongside `@Autowired` to resolve ambiguity when multiple beans of the same type exist.
- **`@Profile`**: Loads beans only when a specific environment profile is active (e.g., `@Profile("dev")`).

## 🏗 Core Application Properties
Spring Boot is highly configurable without touching code. Key `application.properties` or `application.yml` configurations:
```properties
# Server
server.port=8080
server.servlet.context-path=/api/v1

# Database
spring.datasource.url=jdbc:postgresql://localhost:5432/mydb
spring.datasource.username=admin
spring.datasource.password=secret
spring.jpa.hibernate.ddl-auto=validate     # Essential for Prod! Never use 'update'

# Actuator (Monitoring)
management.endpoints.web.exposure.include=health,info,metrics
```

### Interview Questions
**Q1: What happens if you inject a Prototype bean into a Singleton bean?**
The Prototype bean is only instantiated *once* during the Singleton's creation. Every request to the Singleton will use the exact same instance of the Prototype bean, defeating its purpose.
**Fix**: Use `@Lookup` on a method, or inject `ObjectFactory<MyPrototypeBean>`.

**Q2: How does Component Scanning work in Spring Boot?**
`@SpringBootApplication` implicitly adds `@ComponentScan`. It tells Spring to scan the package where the main class resides and all its sub-packages for components (`@Service`, `@Repository`, `@Component`). If you put a component in a parent package relative to the main class, it will *not* be scanned unless explicitly declared.

---
[⬅ Back to Interview Roadmap](../README.md)
