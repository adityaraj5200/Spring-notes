### **#1. What is the Spring Framework?**

Spring is an open-source Java framework for building enterprise-grade applications.

Its main purpose is to simplify Java development by:

- **Managing objects** through an IoC (Inversion of Control) container.
- **Decoupling components** using Dependency Injection (DI).
- **Reducing boilerplate code** with built-in abstractions.
- **Providing modular architecture** — developers can use only the parts they need, like Spring MVC for web apps, Spring Data for persistence, or Spring Security for authentication.

Example usage in real life:

Instead of writing all the JDBC boilerplate code for DB connections, transactions, and exception handling, Spring Data abstracts it, so you just write repository interfaces.

---

### **#2. What is Inversion of Control (IoC) in Spring?**

IoC is a design principle where the control of object creation, configuration, and lifecycle is shifted from the developer to a framework (in this case, the Spring IoC container).

**Without IoC (Traditional Java)**:

```java
java
CopyEdit
Car car = new Car(new Engine());

```

Here, the `Car` class is responsible for creating the `Engine` — tightly coupled.

**With IoC (Spring)**:

```java
java
CopyEdit
@Component
class Car {
    private final Engine engine;
    @Autowired
    Car(Engine engine) { this.engine = engine; }
}

```

The Spring container **injects** the `Engine` instance automatically.

**Why it matters in interviews:**

IoC enables **loose coupling**, better testability (via mocks), and flexible configuration.

---

### **#3. What is Dependency Injection (DI) in Spring and its types?**

Dependency Injection is the process of providing required dependencies to a class from outside rather than creating them internally. In Spring, the IoC container manages DI.

**Types in Spring**:

1. **Constructor Injection** (Preferred in most cases)
    
    ```java
    java
    CopyEdit
    @Component
    class Car {
        private final Engine engine;
        @Autowired
        Car(Engine engine) { this.engine = engine; }
    }
    
    ```
    
    - Immutable dependencies
    - Easier testing
2. **Setter Injection**
    
    ```java
    java
    CopyEdit
    @Component
    class Car {
        private Engine engine;
        @Autowired
        public void setEngine(Engine engine) { this.engine = engine; }
    }
    
    ```
    
    - Allows changing dependencies later.
3. **Field Injection** (Not recommended — hard to test)
    
    ```java
    java
    CopyEdit
    @Autowired
    private Engine engine;
    
    ```
    

**Common interview pitfall:**

If you mix constructor and field injection without care, you may introduce circular dependencies.

---

### **#4. What is a Spring Bean and how is it created?**

A Spring Bean is any object that is **managed by the Spring IoC container**.

It goes through a defined **lifecycle**: instantiation → dependency injection → initialization → destruction.

**Ways to define a Bean**:

1. **Annotation-based**
    
    ```java
    java
    CopyEdit
    @Component
    public class Engine {}
    
    ```
    
2. **Java Configuration**
    
    ```java
    java
    CopyEdit
    @Configuration
    public class AppConfig {
        @Bean
        public Engine engine() {
            return new Engine();
        }
    }
    
    ```
    
3. **XML Configuration** (legacy)

**Key point for interviews:**

All Beans are singletons by default unless scope is changed.

---

### **#5. Explain Bean Scopes in Spring.**

Bean scope defines how many instances of a bean are created and how they are shared.

**Common scopes**:

1. `singleton` (default) — One instance per Spring container.
2. `prototype` — New instance every time it’s requested.
3. `request` — One instance per HTTP request (web apps only).
4. `session` — One instance per HTTP session.
5. `application` — One instance per ServletContext.

**Example**:

```java
java
CopyEdit
@Scope("prototype")
@Component
public class Engine {}

```

**Interview insight:**

Using `prototype` for heavy objects can cause performance issues; `singleton` is better unless you need per-use instantiation.

### Easy #6–10

**#6. What is the difference between `@Component`, `@Service`, `@Repository`, and `@Controller`?**

All four are stereotype annotations in Spring that mark classes as beans for component scanning.

- `@Component` – Generic bean, used when the class doesn’t fit in any specialized category.
- `@Service` – Specialization of `@Component` for service layer classes, often containing business logic.
- `@Repository` – Specialization of `@Component` for DAO classes. It also translates database exceptions into Spring’s `DataAccessException`.
- `@Controller` – Specialization of `@Component` for MVC controllers that return views.
- `@RestController` – Combination of `@Controller` and `@ResponseBody` for REST APIs.

---

**#7. Explain the Spring Bean lifecycle.**

1. **Instantiation** – The bean is created.
2. **Populate Properties** – Dependencies are injected.
3. **BeanNameAware** – The bean name is provided.
4. **BeanFactoryAware/ApplicationContextAware** – The container reference is provided.
5. **PostConstruct** – Methods annotated with `@PostConstruct` run.
6. **InitializingBean.afterPropertiesSet()** – Initialization callback.
7. **Custom init-method** – If configured.
8. **BeanPostProcessor.beforeInitialization** – Runs before initialization methods.
9. **BeanPostProcessor.afterInitialization** – Runs after initialization methods.
10. **Usage** – Bean is ready for use.
11. **PreDestroy** – Methods annotated with `@PreDestroy` run before destruction.
12. **DisposableBean.destroy()** – Destruction callback.
13. **Custom destroy-method** – If configured.

---

**#8. What is the role of `@Configuration` and `@Bean` in Spring?**

- `@Configuration` marks a class as a source of bean definitions.
- `@Bean` defines a bean method inside a `@Configuration` class. The returned object is registered as a bean in the Spring container.

Example:

```java
@Configuration
public class AppConfig {
    @Bean
    public Engine engine() {
        return new Engine();
    }
}

```

`@Bean` is used when you want fine-grained control over bean instantiation, configuration, or when integrating third-party classes not annotated with `@Component`.

---

**#9. What is `@Autowired` and how does it work?**

`@Autowired` tells Spring to resolve and inject a bean dependency automatically. By default, it uses **by-type autowiring**.

It can be used on:

- Constructors
- Setter methods
- Fields

Example:

```java
@Component
public class Car {
    private final Engine engine;
    @Autowired
    public Car(Engine engine) {
        this.engine = engine;
    }
}

```

If multiple beans of the same type exist, use `@Qualifier` to specify which bean to inject.

---

**#10. What is the difference between constructor injection and setter injection?**

- **Constructor Injection**
    - Dependencies are provided at object creation time.
    - Promotes immutability.
    - Mandatory dependencies are clearly expressed.
    - Example:
        
        ```java
        @Autowired
        public Car(Engine engine) { this.engine = engine; }
        
        ```
        
- **Setter Injection**
    - Dependencies can be changed after object creation.
    - Useful for optional dependencies.
    - Example:
        
        ```java
        @Autowired
        public void setEngine(Engine engine) { this.engine = engine; }
        
        ```
        

Constructor injection is generally preferred because it makes the bean immutable and ensures required dependencies are not null.

### Easy #11–15

**#11. What is the difference between `@ComponentScan` and `@EnableAutoConfiguration`?**

- `@ComponentScan` tells Spring where to look for components (`@Component`, `@Service`, `@Repository`, `@Controller`).
    
    Example:
    
    ```java
    @Configuration
    @ComponentScan(basePackages = "com.example.project")
    public class AppConfig {}
    
    ```
    
- `@EnableAutoConfiguration` (in Spring Boot) automatically configures beans based on classpath dependencies. For example, if `spring-boot-starter-data-jpa` is on the classpath, it configures a `DataSource` automatically.

**Pitfall:**

If you rely only on `@EnableAutoConfiguration` without controlling `@ComponentScan`, you may unintentionally load unwanted beans, causing conflicts or performance issues.

---

**#12. What is the difference between `ApplicationContext` and `BeanFactory`?**

- `BeanFactory` is the core IoC container that instantiates and manages beans. It’s lazy — beans are created only when requested.
- `ApplicationContext` is a superset of `BeanFactory` that includes:
    - Eager bean loading by default.
    - Event publishing.
    - Internationalization support.
    - AOP integration.
    - Environment property resolution.

**Gotcha:**

In real-world apps, you almost always use `ApplicationContext` because it includes `BeanFactory` features plus many enterprise-level capabilities.

---

**#13. What is `@Qualifier` and when do you use it?**

`@Qualifier` is used along with `@Autowired` to resolve ambiguity when multiple beans of the same type exist.

Example:

```java
@Component("dieselEngine")
public class DieselEngine implements Engine {}

@Component("petrolEngine")
public class PetrolEngine implements Engine {}

@Component
public class Car {
    @Autowired
    @Qualifier("dieselEngine")
    private Engine engine;
}

```

**Pitfall:**

If you forget to use `@Qualifier` when multiple beans of the same type exist, Spring will throw `NoUniqueBeanDefinitionException`.

---

**#14. Explain the difference between `@Primary` and `@Qualifier`.**

- `@Primary` marks a bean as the default candidate when multiple beans of the same type exist.
- `@Qualifier` explicitly specifies the bean name to be injected.

Example:

```java
@Component
@Primary
public class DieselEngine implements Engine {}

@Component
public class PetrolEngine implements Engine {}

```

If you inject `Engine` without a qualifier, `DieselEngine` will be used.

**Gotcha:**

If both `@Primary` and `@Qualifier` are present, `@Qualifier` always overrides `@Primary`.

---

**#15. What are Spring Profiles and how are they used?**

Spring Profiles allow you to define different configurations for different environments (dev, test, prod).

Example:

```java
@Configuration
@Profile("dev")
public class DevConfig {
    @Bean
    public DataSource dataSource() { ... }
}

@Configuration
@Profile("prod")
public class ProdConfig {
    @Bean
    public DataSource dataSource() { ... }
}

```

Activate a profile:

- Command-line: `-spring.profiles.active=dev`
- application.properties: `spring.profiles.active=prod`

**Pitfall:**

If no active profile is set and beans are profile-specific, you may get `NoSuchBeanDefinitionException`. Always have a default configuration if possible.

### Easy #16–20

**#16. What is the difference between `@PostConstruct` and `@PreDestroy`?**

- `@PostConstruct` – Runs after dependency injection is done and before the bean is put into service. Used for initialization logic.
- `@PreDestroy` – Runs just before the bean is destroyed. Used for cleanup logic.

Example:

```java
@Component
public class ResourceHandler {
    @PostConstruct
    public void init() { System.out.println("Init resource"); }

    @PreDestroy
    public void cleanup() { System.out.println("Cleanup resource"); }
}

```

**Pitfall:**

These annotations require a JSR-250 dependency (`jakarta.annotation-api` in recent versions). If missing, they won’t work silently.

---

**#17. How does Spring handle singleton beans in a multi-threaded environment?**

Spring’s singleton scope means **one bean instance per container**, not per thread.

If the bean is **stateless**, it’s safe in multi-threaded environments.

If the bean is **stateful** (holds mutable state), you must ensure thread safety manually (e.g., `synchronized`, thread-local storage).

**Gotcha:**

Many assume singleton beans are thread-safe by default — they are not if they have mutable fields.

---

**#18. What are the different bean scopes in Spring?**

- **singleton** (default) – One instance per Spring IoC container.
- **prototype** – New instance each time it’s requested.
- **request** – One instance per HTTP request (web-aware context).
- **session** – One instance per HTTP session.
- **application** – One instance per `ServletContext`.
- **websocket** – One instance per WebSocket session.

Example:

```java
@Component
@Scope("prototype")
public class PrototypeBean {}

```

**Pitfall:**

If you inject a prototype bean into a singleton bean, it will still behave like a singleton unless you use `ObjectFactory` or `Provider` to fetch a new instance each time.

---

**#19. Explain the difference between `BeanPostProcessor` and `BeanFactoryPostProcessor`.**

- **BeanFactoryPostProcessor** – Runs before bean instantiation. Used to modify bean definitions (metadata).
- **BeanPostProcessor** – Runs after bean instantiation but before initialization methods. Used to modify bean instances.

Example:

```java
@Component
public class CustomBeanPostProcessor implements BeanPostProcessor {
    public Object postProcessBeforeInitialization(Object bean, String beanName) { return bean; }
}

```

**Gotcha:**

Mixing them up is common — `BeanFactoryPostProcessor` changes definitions, `BeanPostProcessor` changes actual objects.

---

**#20. How does Spring resolve circular dependencies?**

- For **singleton beans**, Spring uses **early references** by creating bean instances first, then injecting dependencies afterward.
- For **constructor injection**, circular dependencies cause a `BeanCurrentlyInCreationException`.
- For **prototype beans**, circular dependencies are not resolved automatically.

**Pitfall:**

Many assume Spring can handle all circular dependencies — but it only works for setter injection with singletons. Constructor cycles must be refactored.

### Easy #21–25

**#21. What is the role of `@Configuration` in Spring?**

`@Configuration` marks a class as a source of bean definitions for the Spring container. Methods annotated with `@Bean` inside it are processed to produce and register beans.

Example:

```java
@Configuration
public class AppConfig {
    @Bean
    public MyService myService() {
        return new MyServiceImpl();
    }
}

```

**Gotcha:**

If you forget `@Configuration` (and only use `@Bean`), Spring will still register the bean but without CGLIB enhancement — meaning `@Bean` methods calling other `@Bean` methods will create **new instances** instead of returning the same singleton.

---

**#22. Difference between `@Component`, `@Service`, `@Repository`, and `@Controller`?**

All are specializations of `@Component`, used for stereotype annotation and readability:

- **`@Component`** – Generic Spring-managed component.
- **`@Service`** – Marks a service-layer class; can carry business logic.
- **`@Repository`** – Marks a data-access object (DAO); adds exception translation for persistence exceptions.
- **`@Controller`** – Marks a Spring MVC controller.

**Pitfall:**

Mixing them doesn’t affect functionality much, but using correct stereotypes improves clarity and lets Spring apply role-specific features (like `@Repository` exception translation).

---

**#23. What is dependency injection in Spring and its types?**

Dependency Injection (DI) is a design pattern where the container supplies dependencies to objects instead of objects creating them.

Types in Spring:

1. **Constructor Injection**
    
    ```java
    @Component
    public class Car {
        private final Engine engine;
        @Autowired
        public Car(Engine engine) { this.engine = engine; }
    }
    
    ```
    
2. **Setter Injection**
    
    ```java
    @Component
    public class Car {
        private Engine engine;
        @Autowired
        public void setEngine(Engine engine) { this.engine = engine; }
    }
    
    ```
    
3. **Field Injection** (not recommended for testing and immutability reasons)
    
    ```java
    @Autowired
    private Engine engine;
    
    ```
    

**Gotcha:**

Constructor injection is preferred for immutability and ensuring required dependencies at creation time.

---

**#24. What is the difference between `@Bean` and `@Component`?**

- `@Bean` is used in `@Configuration` classes to define beans manually.
- `@Component` is used on the class itself to let Spring autodetect and register it via classpath scanning.

Example:

```java
@Configuration
public class AppConfig {
    @Bean
    public MyService myService() { return new MyServiceImpl(); }
}

```

vs.

```java
@Component
public class MyServiceImpl implements MyService {}

```

**Gotcha:**

`@Bean` gives full control over bean creation logic (including calling constructors with parameters, conditional creation, etc.), while `@Component` is limited to default constructor or Spring-injected dependencies.

---

**#25. What is `@Lazy` in Spring?**

`@Lazy` tells Spring to delay bean initialization until it is first requested, rather than at container startup (default for singleton beans is eager loading).

Example:

```java
@Component
@Lazy
public class HeavyService { ... }

```

You can also mark dependencies as lazy:

```java
@Autowired
@Lazy
private HeavyService service;

```

**Pitfall:**

Lazy loading can hide initialization errors until runtime, which might make them harder to detect early in development.

### Easy #26–30

**#26. How does `@Primary` work in Spring?**

When multiple beans of the same type are present, Spring needs a way to decide which one to inject by default.

`@Primary` marks a bean as the preferred choice when no qualifier is specified.

Example:

```java
@Component
@Primary
public class MySqlDataSource implements DataSource {}

@Component
public class PostgresDataSource implements DataSource {}

```

If you `@Autowired` `DataSource` without `@Qualifier`, Spring injects `MySqlDataSource`.

**Gotcha:**

`@Primary` only resolves conflicts if no `@Qualifier` is used. Once `@Qualifier` is applied, `@Primary` is ignored.

---

**#27. Difference between `@Autowired` and `@Qualifier`?**

- `@Autowired` tells Spring to inject a dependency automatically by type.
- If multiple candidates exist, you use `@Qualifier` to specify the exact bean by name.

Example:

```java
@Autowired
@Qualifier("postgresDataSource")
private DataSource dataSource;

```

**Gotcha:**

If you have multiple beans and don’t use `@Qualifier` or `@Primary`, Spring throws `NoUniqueBeanDefinitionException`.

---

**#28. What is the default bean initialization order in Spring?**

Spring initializes beans in the following general sequence:

1. Instantiate bean
2. Set properties (DI)
3. Call `BeanNameAware`, `BeanFactoryAware` methods (if implemented)
4. Call `BeanPostProcessor.postProcessBeforeInitialization()`
5. Call `@PostConstruct` or `InitializingBean.afterPropertiesSet()`
6. Call `BeanPostProcessor.postProcessAfterInitialization()`

**Pitfall:**

The actual order can vary if `@DependsOn` or bean dependencies influence the initialization sequence.

---

**#29. How does `@DependsOn` work?**

`@DependsOn` forces Spring to initialize certain beans before the annotated bean.

Example:

```java
@Component
@DependsOn("dataSource")
public class Repository {}

```

**Gotcha:**

`@DependsOn` only controls *initialization order*, not *injection order*. Injection still happens normally based on dependency requirements.

---

**#30. How to create a bean without using `@Component` or `@Bean`?**

- Use XML configuration:

```xml
<bean id="myService" class="com.example.MyServiceImpl"/>

```

- Or register it programmatically in a `BeanDefinitionRegistryPostProcessor` or using `GenericApplicationContext.registerBean()`.

**Gotcha:**

Manual registration is useful for dynamic beans (e.g., loaded from config at runtime) but can reduce readability and maintainability.

### Easy #31–35

**#31. What is `ApplicationContext` in Spring?**

`ApplicationContext` is the central interface for accessing the Spring container. It manages bean creation, dependency injection, lifecycle management, and resource loading.

Example:

```java
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
MyService service = context.getBean(MyService.class);

```

**Gotcha:**

`ApplicationContext` loads all singleton beans eagerly by default, unlike `BeanFactory` which is lazy.

---

**#32. Difference between `ApplicationContext` and `BeanFactory`?**

- **`BeanFactory`**: Basic container, lazy initialization, lightweight.
- **`ApplicationContext`**: Extends `BeanFactory`, adds internationalization, event publication, annotation processing, and eager singleton loading.

**Interview trap:**

If asked which to use — always prefer `ApplicationContext` for modern applications unless you have extreme memory constraints.

---

**#33. How does Spring handle bean scopes?**

Common scopes:

1. **singleton** – one instance per Spring container (default).
2. **prototype** – new instance each time requested.
3. **request** – one instance per HTTP request (Web apps only).
4. **session** – one per HTTP session.
5. **application** – one per `ServletContext`.

Example:

```java
@Component
@Scope("prototype")
public class MyBean {}

```

**Gotcha:**

Injecting a prototype bean into a singleton bean will still behave like a singleton unless retrieved via `ObjectFactory` or `Provider`.

---

**#34. What is a `BeanPostProcessor` in Spring?**

`BeanPostProcessor` allows custom modification of new bean instances before and after initialization callbacks.

Example:

```java
@Component
public class MyPostProcessor implements BeanPostProcessor {
    public Object postProcessBeforeInitialization(Object bean, String name) {
        return bean;
    }
    public Object postProcessAfterInitialization(Object bean, String name) {
        return bean;
    }
}

```

**Gotcha:**

A common trap is expecting constructor logic to be affected by `BeanPostProcessor` — it runs *after* instantiation.

---

**#35. What is the use of `@Value` annotation?**

`@Value` injects values into bean fields from property files, system properties, or SpEL (Spring Expression Language).

Example:

```java
@Value("${app.name}")
private String appName;

```

Supports expressions:

```java
@Value("#{2 * T(Math).PI}")
private double circle;

```

**Gotcha:**

`@Value` injection happens after the constructor — so you cannot use injected values inside constructor initialization logic without `@PostConstruct`.

### Easy #36–40

**#36. How does Spring resolve property values from `.properties` or `.yml` files?**

Spring uses `PropertySourcesPlaceholderConfigurer` (in classic Spring) or auto-configured environment processing in Spring Boot to resolve `${...}` placeholders from property files.

Example in Boot:

```java
// application.properties
app.name=MyApp

```

```java
@Value("${app.name}")
private String appName;

```

**Gotcha:**

In classic Spring (non-Boot), you must explicitly declare `PropertySourcesPlaceholderConfigurer` as a bean; without it, `${...}` placeholders will not resolve.

---

**#37. Difference between `@Configuration` and `@Component`?**

- **`@Component`**: Marks a class as a candidate for component scanning, creating a bean of that class.
- **`@Configuration`**: A specialized `@Component` that also allows defining beans via `@Bean` methods. It enables full CGLIB proxying for method calls to other `@Bean` methods within the same class.

Example:

```java
@Configuration
public class AppConfig {
    @Bean
    public MyService service() { return new MyServiceImpl(); }
}

```

**Gotcha:**

If `@Configuration` is replaced with `@Component`, `@Bean` methods will not be proxied, and each call will create a new instance instead of returning the singleton from the context.

---

**#38. What is the difference between `@PostConstruct` and `InitializingBean.afterPropertiesSet()`?**

Both are used to run initialization logic after dependency injection:

- **`@PostConstruct`**: Preferred, framework-independent, works with any managed bean.
- **`afterPropertiesSet()`**: Part of Spring’s `InitializingBean` interface.

Example:

```java
@PostConstruct
public void init() {
    // initialization logic
}

```

**Gotcha:**

`@PostConstruct` runs after `afterPropertiesSet()` when both are present in the same bean.

---

**#39. What is `BeanFactoryPostProcessor`?**

It allows modification of bean definitions (metadata) before the beans are instantiated.

Example:

```java
@Component
public class MyBeanFactoryPostProcessor implements BeanFactoryPostProcessor {
    public void postProcessBeanFactory(ConfigurableListableBeanFactory factory) {
        BeanDefinition bd = factory.getBeanDefinition("myBean");
        bd.setScope(BeanDefinition.SCOPE_PROTOTYPE);
    }
}

```

**Gotcha:**

You cannot access bean instances here — only definitions. Attempting to fetch a bean will cause premature instantiation.

---

**#40. How to load an external property file in Spring Boot?**

1. Place it in `src/main/resources` — automatically loaded.
2. Or specify location via `@PropertySource`:

```java
@Configuration
@PropertySource("classpath:custom.properties")
public class AppConfig {}

```

**Gotcha:**

When using YAML (`.yml`), `@PropertySource` doesn’t work — you need to rely on Spring Boot’s built-in YAML parsing or use `YamlPropertySourceLoader`.

### Easy #41–45

**#41. What is `@Primary` annotation in Spring?**

`@Primary` marks a bean as the default candidate for autowiring when multiple beans of the same type exist.

Example:

```java
@Component
@Primary
public class PrimaryService implements MyService {}

@Component
public class SecondaryService implements MyService {}

```

```java
@Autowired
private MyService service; // Injects PrimaryService

```

**Gotcha:**

`@Primary` resolves ambiguity globally. For selective injection, use `@Qualifier` instead.

---

**#42. How does `@Qualifier` work in Spring?**

`@Qualifier` specifies which bean to inject when multiple candidates exist.

Example:

```java
@Component("fastService")
public class FastService implements MyService {}

@Component("slowService")
public class SlowService implements MyService {}

@Autowired
@Qualifier("fastService")
private MyService service;

```

**Gotcha:**

If both `@Primary` and `@Qualifier` are present, `@Qualifier` takes precedence.

---

**#43. Explain `@Lazy` annotation in Spring.**

`@Lazy` delays bean initialization until it is first requested.

Example:

```java
@Component
@Lazy
public class HeavyBean {
    public HeavyBean() { System.out.println("Initialized"); }
}

```

**Gotcha:**

`@Lazy` on a singleton delays instantiation, but once created, it stays in memory. For `@Lazy` prototypes, Spring still creates new instances each time.

---

**#44. What is the difference between `@Bean` and `@Component` in Spring?**

- **`@Component`**: Marks a class for component scanning; Spring creates the bean automatically.
- **`@Bean`**: Declares a bean inside a `@Configuration` class manually.

Example:

```java
@Configuration
public class AppConfig {
    @Bean
    public MyService service() { return new MyServiceImpl(); }
}

```

**Gotcha:**

`@Bean` gives more control — you can configure bean parameters and dependencies programmatically.

---

**#45. How does Spring handle circular dependencies?**

Spring resolves circular dependencies for singleton beans by using early references before full initialization.

Example:

```java
@Component
public class A {
    @Autowired private B b;
}

@Component
public class B {
    @Autowired private A a;
}

```

**Gotcha:**

Constructor-based injection fails in circular dependencies — you must use setter or field injection. In Spring Boot 2.6+, circular references are disabled by default unless `spring.main.allow-circular-references=true`.

### Easy #46–50

**#46. What is `@Scope` in Spring?**

`@Scope` defines the lifecycle and visibility of a bean.

Common scopes:

- `singleton` — one instance per container (default).
- `prototype` — new instance per request.
- `request`, `session`, `application` — web-specific scopes.

Example:

```java
@Component
@Scope("prototype")
public class PrototypeBean {}

```

**Gotcha:**

When injecting a prototype into a singleton, the prototype is instantiated only once at singleton creation unless you use `ObjectFactory` or `Provider` to get fresh instances.

---

**#47. Explain `@DependsOn` annotation in Spring.**

`@DependsOn` forces Spring to initialize specified beans before the current bean.

Example:

```java
@Component
@DependsOn("dataSource")
public class MyRepository {}

```

**Gotcha:**

This affects initialization order, not dependency injection. It doesn’t create an actual dependency link — removing the bean name in `@DependsOn` may still compile but fail at runtime.

---

**#48. What is the difference between `ApplicationContext` and `BeanFactory`?**

- **BeanFactory** — basic container, lazy loads beans, no advanced features.
- **ApplicationContext** — extends BeanFactory, eager loads singletons, supports internationalization, event propagation, and AOP.

Example:

```java
ApplicationContext ctx = new ClassPathXmlApplicationContext("appConfig.xml");

```

**Gotcha:**

Use `ApplicationContext` in most applications; `BeanFactory` is typically used for lightweight or memory-critical environments.

---

**#49. How does `@Value` differ from `Environment` in property injection?**

- `@Value` — injects a single property directly into a field, constructor, or method parameter.
- `Environment` — retrieves properties programmatically.

Example:

```java
@Value("${app.timeout}")
private int timeout;

@Autowired
private Environment env;
int timeoutValue = env.getProperty("app.timeout", Integer.class);

```

**Gotcha:**

`@Value` is resolved during bean creation; `Environment` can fetch updated values at runtime if the source changes and refresh is supported.

---

**#50. How can you create conditional beans in Spring?**

Use `@Conditional` or Spring Boot’s `@ConditionalOn...` annotations.

Example:

```java
@Bean
@ConditionalOnProperty(name = "feature.enabled", havingValue = "true")
public FeatureService featureService() { return new FeatureService(); }

```

**Gotcha:**

Condition checks happen at context refresh time — you can’t change the condition later without restarting or refreshing the context.

### Easy #51–55

**#51. What is the `@Profile` annotation in Spring?**

`@Profile` allows you to register beans conditionally based on the active environment profile(s).

Example:

```java
@Component
@Profile("dev")
public class DevDataSourceConfig implements DataSourceConfig {}

@Component
@Profile("prod")
public class ProdDataSourceConfig implements DataSourceConfig {}

```

Profiles are activated via:

```
spring.profiles.active=dev

```

**Gotcha:**

If no profile is active and a bean is tied only to specific profiles, it won’t load — causing `NoSuchBeanDefinitionException`.

---

**#52. What is `@ConfigurationProperties` in Spring Boot?**

`@ConfigurationProperties` maps external properties into a Java bean.

Example:

```java
@Component
@ConfigurationProperties(prefix = "app")
public class AppConfig {
    private String name;
    private int timeout;
    // getters & setters
}

```

```
app.name=MyApp
app.timeout=5000

```

**Gotcha:**

Requires `@EnableConfigurationProperties` (if not annotated with `@Component`) and proper setters — otherwise binding will fail silently.

---

**#53. What is `ApplicationEventPublisher` in Spring?**

It’s used to publish events to listeners within the Spring context.

Example:

```java
@Component
public class OrderService {
    @Autowired
    private ApplicationEventPublisher publisher;

    public void placeOrder(Order order) {
        publisher.publishEvent(new OrderCreatedEvent(this, order));
    }
}

```

**Gotcha:**

Events are synchronous by default. Use `@Async` on listeners for async processing, but ensure an `AsyncConfigurer` or `@EnableAsync` is configured.

---

**#54. What is `@PostConstruct` and when is it called?**

`@PostConstruct` marks a method to run after dependency injection is done but before the bean is available for use.

Example:

```java
@PostConstruct
public void init() {
    cache.load();
}

```

**Gotcha:**

Only works on singletons by default. For prototype beans, it runs right after instantiation of each instance.

---

**#55. What is the `@PreDestroy` annotation in Spring?**

`@PreDestroy` marks a method to run before the bean is destroyed.

Example:

```java
@PreDestroy
public void cleanup() {
    connection.close();
}

```

**Gotcha:**

Called only for singleton beans in the application context. For prototype beans, Spring does not manage destruction lifecycle — you must clean up manually.

### Easy #56–60

**#56. How do you access the Spring ApplicationContext inside a bean?**

Implement `ApplicationContextAware` or use dependency injection.

Example (via interface):

```java
@Component
public class MyBean implements ApplicationContextAware {
    private ApplicationContext context;
    @Override
    public void setApplicationContext(ApplicationContext ctx) {
        this.context = ctx;
    }
}

```

**Gotcha:**

Directly accessing `ApplicationContext` breaks the dependency inversion principle — use only when absolutely necessary.

---

**#57. What is the difference between `@Component`, `@Service`, `@Repository`, and `@Controller`?**

All are specializations of `@Component` with semantic meaning:

- `@Component` — generic Spring bean.
- `@Service` — service-layer beans.
- `@Repository` — DAO beans, adds exception translation for persistence errors.
- `@Controller` — web controller for MVC.

**Gotcha:**

Functionally they behave the same for component scanning, but `@Repository` enables persistence exception translation automatically.

---

**#58. What is `BeanPostProcessor` in Spring?**

`BeanPostProcessor` allows custom modification of new bean instances after instantiation but before initialization.

Example:

```java
@Component
public class CustomBeanPostProcessor implements BeanPostProcessor {
    public Object postProcessBeforeInitialization(Object bean, String beanName) {
        return bean;
    }
    public Object postProcessAfterInitialization(Object bean, String beanName) {
        return bean;
    }
}

```

**Gotcha:**

It applies to all beans in the context, so logic must filter by `beanName` or `bean` type to avoid unnecessary overhead.

---

**#59. How does Spring resolve bean injection by type and by name?**

- By default, Spring injects by **type** (matching the bean’s class/interface).
- If multiple candidates match, Spring tries **by name** matching the field or parameter name to a bean’s name.

**Gotcha:**

Ambiguity without `@Qualifier` or `@Primary` leads to `NoUniqueBeanDefinitionException`.

---

**#60. What is `FactoryBean` in Spring?**

`FactoryBean` is a special bean that returns an object instance from its `getObject()` method instead of being injected directly.

Example:

```java
@Component
public class MyFactoryBean implements FactoryBean<MyService> {
    public MyService getObject() { return new MyServiceImpl(); }
    public Class<?> getObjectType() { return MyService.class; }
}

```

**Gotcha:**

To get the factory itself instead of its product, prefix the bean name with `&` when retrieving from the context.

### Easy #61–65

**#61. How do you inject a collection of beans in Spring?**

Spring can inject all beans of a given type into a `List`, `Set`, or `Map`.

Example:

```java
@Autowired
private List<PaymentProcessor> processors;

```

This injects all `PaymentProcessor` beans in the application context.

**Gotcha:**

Order is not guaranteed unless you use `@Order` or implement `Ordered` interface on beans.

---

**#62. What is the difference between `@Primary` and `@Qualifier`?**

- `@Primary` — sets a bean as the default when multiple candidates exist.
- `@Qualifier` — explicitly specifies which bean to inject.

Example:

```java
@Primary
@Component
public class PayPalProcessor implements PaymentProcessor {}

@Component
@Qualifier("stripe")
public class StripeProcessor implements PaymentProcessor {}

```

**Gotcha:**

`@Primary` works globally, while `@Qualifier` overrides it for specific injection points.

---

**#63. How can you define bean initialization and destruction methods without annotations?**

You can specify them in XML or in `@Bean` method attributes.

Example (Java Config):

```java
@Bean(initMethod = "init", destroyMethod = "shutdown")
public CacheService cacheService() {
    return new CacheService();
}

```

**Gotcha:**

When using `destroyMethod`, remember that Spring will attempt to call it on context shutdown — ensure it exists or set `destroyMethod=""` to disable.

---

**#64. What is a singleton bean in Spring and how is it different from the Singleton design pattern?**

In Spring, a singleton bean means **one instance per Spring container**, not per JVM like the classic Singleton pattern.

**Gotcha:**

Multiple application contexts mean multiple instances of a “singleton” bean.

---

**#65. How can you lazily initialize beans in Spring?**

Use `@Lazy` at class or injection point level.

Example:

```java
@Component
@Lazy
public class HeavyService {}

```

**Gotcha:**

Lazy initialization delays creation until first use, but for circular dependencies in singleton beans, this can cause runtime errors if the bean is needed during another bean's initialization.

### Easy #66–70

**#66. What is the role of `@Value` in Spring?**

`@Value` injects values from property files, system properties, or SpEL (Spring Expression Language) directly into fields, methods, or constructor parameters.

Example:

```java
@Value("${app.name}")
private String appName;

@Value("#{2 * 1024}")
private int bufferSize;

```

**Gotcha:**

If the property is missing, injection fails unless you provide a default:

```java
@Value("${app.name:DefaultApp}")

```

---

**#67. What is the `Environment` interface used for in Spring?**

The `Environment` interface provides access to environment properties, profiles, and property sources.

Example:

```java
@Autowired
private Environment env;

public void printProfile() {
    System.out.println(env.getActiveProfiles());
}

```

**Gotcha:**

Use `Environment` when you need programmatic property access; prefer `@Value` for static injection.

---

**#68. How do you make a bean conditional in Spring?**

Use `@Conditional` with a custom `Condition` implementation.

Example:

```java
@Bean
@Conditional(OnWindowsCondition.class)
public MyService windowsService() { return new WindowsService(); }

```

**Gotcha:**

`@Profile` is a special case of `@Conditional`, but `@Conditional` offers more flexibility.

---

**#69. What is a `BeanFactoryPostProcessor`?**

`BeanFactoryPostProcessor` allows modifying bean definitions before the container instantiates any beans.

Example:

```java
@Component
public class CustomBFPP implements BeanFactoryPostProcessor {
    public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) {
        // Modify bean definitions here
    }
}

```

**Gotcha:**

Runs before `BeanPostProcessor` and is used for configuration changes, not bean instance modification.

---

**#70. How can you define a default bean when multiple beans exist for a type?**

Mark it with `@Primary`.

Example:

```java
@Primary
@Component
public class DefaultProcessor implements Processor {}

```

**Gotcha:**

If `@Qualifier` is specified at injection point, it overrides `@Primary`.