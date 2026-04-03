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

### Interview Question
*What happens if you inject a Prototype bean into a Singleton bean?*
The Prototype bean is only instantiated *once* during the Singleton's creation. Every request to the Singleton will use the exact same instance of the Prototype bean, defeating its purpose.
**Fix**: Use `@Lookup` on a method, or inject `ObjectFactory<MyPrototypeBean>`.

---
[⬅ Back to Interview Roadmap](../README.md)
