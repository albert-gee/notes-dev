# Spring

## Spring Context

The **Spring context** is a container that manages the lifecycle and dependencies of application components known as *
*beans**. These beans are stored in memory and created, injected, and destroyed by Spring as needed.

Spring follows the **Inversion of Control (IoC)** principle, where the framework, not the developer, manages object
creation and wiring. To benefit from Spring features, you need to tell Spring which objects to manage.

---

## Adding Beans to the Spring Context

Beans can be added to the Spring context in three main ways:

### 1. `@Bean` Annotation

- Used in `@Configuration` classes.
- Provides full control over instance creation and configuration.
- Can add any object type, even ones not defined in your application.
- Allows adding multiple beans of the same type.
- Requires writing a separate method for each bean (more boilerplate).

```java

@Configuration
public class ProjectConfig {

    @Bean
    public Parrot parrot() {
        Parrot p = new Parrot();
        p.setName("Koko");
        return p;
    }

    @Bean
    @Primary
    public Parrot parrot2() {
        Parrot p = new Parrot();
        p.setName("Miki");
        return p;
    }

    @Bean
    public Person person() {
        Person p = new Person();
        p.setName("Ella");
        p.setParrot(parrot());
        return p;
    }
}
````

### 2. Stereotype Annotations (`@Component`, `@Service`, etc.)

* Less boilerplate than `@Bean`.
* Automatically picks up classes with stereotype annotations via `@ComponentScan`.
* Only works with classes defined in your app.
* Only one instance of each class can be added.
* Limited control over instantiation.

```java

@Component
public class Parrot {
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @PostConstruct
    public void init() {
        this.name = "Kiki";
    }
}

@Configuration
@ComponentScan(basePackages = "main")
public class ProjectConfig {
}
```

### 3. Programmatically (e.g., `registerBean()`)

* Available since Spring 5.
* Allows dynamic, custom registration logic.
* Useful for conditionally or externally defined beans.

```java
public class Main {
    public static void main(String[] args) {
        var context = new AnnotationConfigApplicationContext(ProjectConfig.class);

        Parrot x = new Parrot();
        x.setName("Kiki");

        Supplier<Parrot> supplier = () -> x;
        context.registerBean("parrot1", Parrot.class, supplier);

        Parrot p = context.getBean(Parrot.class);
        System.out.println(p.getName());
    }
}
```

---

## Wiring Beans (Dependency Injection)

Spring uses **Dependency Injection (DI)** to wire beans together.

### Manual Wiring (Method Calls)

```java

@Configuration
public class ProjectConfig {
    @Bean
    public Parrot parrot() {
        Parrot p = new Parrot();
        p.setName("Koko");
        return p;
    }

    @Bean
    public Person person() {
        Person p = new Person();
        p.setName("Ella");
        p.setParrot(parrot());
        return p;
    }
}
```

### Auto-Wiring (Method Parameters)

```java

@Configuration
public class ProjectConfig {
    @Bean
    public Parrot parrot() {
        Parrot p = new Parrot();
        p.setName("Koko");
        return p;
    }

    @Bean
    public Person person(Parrot parrot) {
        Person p = new Person();
        p.setName("Ella");
        p.setParrot(parrot);
        return p;
    }
}
```

### Using `@Autowired`

* Inject via constructor (preferred), field (not recommended for production), or setter (rarely used).

```java

@Component
public class Person {
    private String name = "Ella";
    private final Parrot parrot;

    @Autowired
    public Person(Parrot parrot) {
        this.parrot = parrot;
    }
}
```

---

## Bean Scopes and Lifecycle

Spring manages bean creation and lifecycle using **scopes**:

* **singleton** (default): one instance per context.
* **prototype**: new instance every time requested.
* **request**: one instance per HTTP request (`@RequestScope`).
* **session**: one instance per HTTP session (`@SessionScope`).
* **application**: one instance per application (`@ApplicationScope`).

To use custom scopes:

```java

@Scope(BeanDefinition.SCOPE_PROTOTYPE)
@Bean
public Parrot parrot() {
    return new Parrot();
}
```

---

## Aspect-Oriented Programming (AOP)

**Aspects** let you intercept method calls and inject logic (e.g., logging, security).

### AOP Concepts

* **Aspect**: logic to apply.
* **Advice**: when to apply it (before, after, around).
* **Pointcut**: which methods to apply it to.

Spring uses **proxies** for weaving: instead of returning the original bean, Spring returns a proxy that wraps it with
aspect logic.

### Steps to Create an Aspect

1. Enable AOP:

```java

@Configuration
@ComponentScan(basePackages = "services")
@EnableAspectJAutoProxy
public class ProjectConfig {
}
```

2. Create an Aspect:

```java

@Aspect
@Component
public class LoggingAspect {
    @Around("execution(* services.*.*(..))")
    public Object log(ProceedingJoinPoint joinPoint) throws Throwable {
        String methodName = joinPoint.getSignature().getName();
        Object[] args = joinPoint.getArgs();

        // before method call
        Object result = joinPoint.proceed();
        // after method call

        return result;
    }
}
```

3. To define the order of multiple aspects:

```java

@Aspect
@Order(1)
public class FirstAspect {
}

@Aspect
@Order(2)
public class SecondAspect {
}
```
