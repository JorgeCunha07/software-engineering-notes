# Spring and Spring Boot

## Spring Key Concepts

### Inversion of Control (IoC)

**Inversion Control** is a principle in software engineering which transfers the control of objects or portions of a program to a container or framework. We most oten use it in the context of object-oriented programming.
In contrast with traditional programming, in which our custom code makes calls to a library, IoC enables a framework to take control of the flow of a program and make calls to our custom code. To enable this, frameworks use abstractions with additional behavior built in. If we want to add our own behavior, we need to extend the classes of the framework or plugin our own classes.
The advantages of this architecture are:

- decoupling the execution of a task from its implementation.
- making it easier to switch between different implementations.
- greater modularity of a program.
- greater ease in testing a program by isolating a component or mocking its dependencies, and allowing components to communicate through contracts.

We can achieve Inversion of Control through various mechanisms such as: Strategy design pattern, Service Locator pattern, and Dependency Injection (DI).

### Dependency Injection (DI)

Dependency Injection is a pattern we can use to implement IoC, where the control being inverted is setting an object's dependencies.\
Connecting objects with other objects, or "injecting" objects into other object, is done by an assembler rather than by the objects themselves.\

### The Spring IoC Container

An IoC container is a common characteristic of framework that implement IoC.\
In the Spring framework, the interface _ApplicationContext_ represents the IoC container. The Spring container is responsible for instantiating, configuring and assembling objects known as _beans_, as well as managing their life cycles.\
The Spring framework provides several implementations of the _ApplicationContext_ interface: _AnnotationConfigApplicationContext_, _ClassPathXmlApplicationContext_ and _FileSystemXmlApplicationContext_ for standalone applications, and _WebApplicationContext_ for web applications.\
In order to assemble beans, the container uses configuration metadata, which can be in the form of XML configuration or annotations.|
Here's one way to manually instantiate a container:

```java
ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
```

And here's an example of manually instantiating a contaijer using _AnnotationConfigApplicationContext_:

```java
AnnotationConfigApplicationContext annotationContext = new AnnotationConfigApplicationContext();
```

When you create an instance of _AnnotationConfigApplicationContext_ and provide it with one or more configuration classes, it scans these classes for the _@Bean_ annotations and other relevant annotations. It then initializes and manages the beans defined in these classes, setting up their dependencies and managing their lifecycle.\
Dependency Injection in Spring can be done through constructors, setters or fields.

### Constructor-Based Dependency Injection

In the case of constructor-based dependency injection, the container will invoke a constructor with arguments each representing a dependency we want to set.\
Spring resolves each argument primarily by type, followed by name of the attribute, and index for disambiguation.\
Let's see the configuration of a bean and its dependencies using annotations:

```java
@Configuration
public class AppConfig {

  @Bean
  public Item item1(){
    return new ItemImpl1();
  }

  @Bean
  public Store store(){
    return new Store(item1());
  }
}
```

The _@Configuration_ annotation indicates that the class is a source of the bean definitions. We can also add it to multiple configuration classes.\
We use the _@Bean_ annotation on a method to define a bean. If we don't specify a custom name, then the bean name will default to the method name.\
For a bean with the default _singleton_ scope, Spring first checks if a cached instance of the bean already exists, and only creates a new one if it doesn't. If we're using the prototype scope, the container returns a new bean instance for each method call.\
Another way to create the configuration of the beans is through XML configuration:

```java
<bean id="item1" class="org.store.ItemImpl" />
<bean id="store" class="org.store.Store">
  <constructor-arg type="ItemImpl1" index="0" name="item" ref="item1" />
</bean>
```

### Setter-Based Dependency Injection

for setter-based DI, the container will call setter methods of our class after invoking a no-argument constructor or no-argument static factory method to instantiate the bean. Let's create this configuration using annotations:

```java
@Bean
public Store (){
  Store store = new Store();
  store.setItem(item1());

  return store;
}
```

We can also use XML for the same configuration of beans:

```java
<bean id="store" class="org.store.Store">
  <property name="item" ref="item1" />
</bean>
```

We can combine constructor-based and setter-based types of injection for the same bean. The Spring documentation recommends using constructor-based injection for mandatory dependencies, and setter-based injection for optional ones.

### Field-Based Dependency Injection

In case of Field-Based DI, we can inject the dependencies by marking them with an _@Autowired_ annotation:

```java
public class Store {
  @Autowired
  private Item item;
}
```

While constructing the Store object, if there's no constructor or setter method to inject, the _Item_ bean, the container will use reflection to inject _Item_ into _Store_.\
We can also achieve this using XML configuration.\
This approach might look simpler and cleaner, but we don't recommend using it because it has a few drawbacks such as:

- This method uses reflection to inject the dependencies, which is costlier than constructor-based or setter-based injection.
- It's really easy to keep adding multiple dependencies using this apporach. If we were using constructor injection, having multiple arguments would make us think that the class does more than one thing, which can violate the Single Responsibility Principle.

### Autowiring Dependencies

Wiring allows Spring container to automatically resolve dependencies between collaborating beans by inspecting the beans that have been defined.\
There are four modes of autowiring a bean using an XML configuration:

- **no:** the default value - this means no autowiring is used for the bean and we have to explicitly name the dependencies.
- **byName:** autowiring is done based on the name of the property, therefore Spring will look for a bean with the same name as the property that needs to be set.
- **byType:** similar to the _byName_ autowiring, only based on the type of the property. This means Spring will look for a bean with the same type of the property to set. If there's more than one bean of that type, the framework throws an exception.
- **constructor:** autowiring is done based on constructor arguments, meaning Spring will liik for beans with the same type as the constructor arguments.

For example, let's autowire the _item_ bean defined above by type into the _store_ bean:

```java
@Bean(autowire = Autowire.BY_TYPE)
public class Store {
  private Item item;
  public setItem(Item item) {
    this.item = item;
  }
}
```

Note that the _autowire_ property is deprecated as of Spring 5.1.\
We can also inject beans using the _@Autowired_ annotation for autowiring by type:

```java
public class Store {
  @Autowired
  private Item item;
}
```

If there's more than one bean of the same type, we can use the _@Qualifier_ annotation to reference a bean by name:

```java
public class Store {
  @Autowired
  @Qualifier("item1")
  private Item item;
}
```

Now let's autowire beans by type through XML configuration:

```java
<bean id="store" class="org.store.Store" autowire="byType"> </bean>
```

Next, let's inject a bean named item into the _item_property of \_store_ bean by name through XML:

```java
<bean id="item" class="org.store.ItemImpl1" />

<bean id="store" class="org.store.Store" autowire="byName">
</bean>
```

We can also override the autowiring by defining dependencies explicitly through constructor arguments or setters.

### Lazy Initialized Beans

By default, the container creates and configures all singleton beans during initialization. To avoid this, we can use the _lazy-init_ attribute with value _true_ on the bean configuration:

```java
<bean id="item1" class="org.store.ItemImpl1" lazy-init="true" />
```

Consequently, the _item1_ bean will only be initialized when it's first requested, and not at startup. The advantage of this is faster initialization time, but the trade-off is that we won't discover any configuration errors until after the bean is requested, which could be several hours or even days after the appliation has already been running.

### IoC vs Dependency Injection

DI refers to the composition of the classes when injecting a dependency
Ex. @Autowired creates and injects a service in the constructor.

IoC shifts control of the dependencies to the framework. Is a design principle where the control of object creation and flow is transferred from the application to the framework.\
Ex. In the @Service annotation we don't manually create the service, Spring IoC does it for us.

### Beans

**Beans** are objects that are instantiated, assembled, and otherwise managed by a Spring IoC container. These are the building blocks of a Spring application.

#### Declaring Beans

- **Annotation-based:** Using stereotype annotations like `@Component`, `@Service` (for business logic), `@Repository` (for data access), `@Controller` (for web layers).\
  Spring scans for these annotations.
- **Java-based Configuration:** Using `@Configuration` on a class and `@Bean` on methods within that class. This gives you a programmatic control over bean creation.

```java
@Configuration
public class AppConfig {
  @Bean
  public MyService myService() {
    return new MyService(anotherService());
  }

  @Bean
  public AnotherService anotherService() {
    return new AnotherService();
  }
}
```

- **XML-based Configuration (Legacy):** Defining beans in XML files. Good to be aware of if you encounter old projects.

#### Bean Scopes

Define the lifecycle and visibility of a bean instance.

- `singleton` (default): Only one instance of the bean is created per Spring IoC container.
- `prototype`: A new instance is created every time the bean is requested.
- `request`: (Web-aware applications) A new instance for each HTTP request.
- `session`: (Web-aware applications) A new instance for each HTTP session.
- `application`: (Web-aware application) Scoped to the lifecycle of the `ServletContext`.
- `websocket`: (Web-aware applications) Scoped to the lifecycle of a WebSocket.

#### Bean Lifecycle

Spring manages the complete lifecycle of a bean, from instantiation and dependency injection to initialization callbacks (e.g., methods annotated with `@PostConstruct` or implementing `InitializingBean`) and destruction callbacks (e.g., methods annotated with `@PreDestroy` or implementing `DisposableBean`).

#### Key Annotations for Bean Management

- `@Autowired`: Marks a constructor, field, setter method, or config method to be autowired by Spring's dependency injection facilities.
- `@Component`: generic setereotype for any Spring-managed component.
- `@Service`: Specialization of `@Component` for service layer classes.
- `@Repository`: Specialization of `@Component` for persistence layer classes (often enables automatic exception translation).
- `@Controller`: Specialization of `@Component` for presentation layer classes (used in Spring MVC).
- `@Configuration`: Indicates that a class declares one or more `@Bean`methods and may be processed by the Spring container to generate bean definitions.
- `@Bean`: Indicates that a method produces a bean to be managed by the Spring container.
- `@Qualifier("beanName")`: Used when you have multiple beans of the same type and want to specify which one to inject.
- `@Value`: Used to inject values from properties files, system properties, or expressions (SpEL - Spring Expression Language).

### Aspect-Oriented Programming (AOP) - Overview

#### Concept of AOP

AOP complements Object-Oriented Programming (OOP) by providing another way of thinking about program structure. OOP's main unit of modularity is the class, whereas AOP's is the aspect. Aspects enable the modularization of _cross-cutting concerns_ - concerns that affect multiple points in an application (e.g., logging, security, transaction management).

#### Key AOP Terminology

- **Aspect:** A module that encapsulates a cross-cutting concern. In Spring AOP, aspects are typically regular Java classes annotated with `@Aspect`.
- **Join Point:** A point during the execution of a program, such as the execution of a method or the handling of an exception. In Spring AOP, a join always represents a method execution.
- **Advice:** Action taken by an aspect at a particular join point. Different types of advice include "around", "before", and "after" advice.

  - `@Before`: Advice that executes before a join point.
  - `@AfterReturning`: Advice that executes after a join point completes.
  - `@AfterThrowing`: Advice that executes if a method exits by throwing an exception.
  - `@After`: Advice that executes regardless of the means by which a join point exits (normal or exceptional return).
  - `@Around`: Advice that surrounds a join point, such as a method invocation. This is the most powerful kind of advice.

- **Pointcut:** A predicate that matches join poins. Advice is associated with a pointcut expression and runs at any join point matched by the pointcut.
- **Target Object:** the object being advised by one or more aspects.
- **Proxy:** Spring AOP is proxy-based. The AOP framework creates a proxy object (either JDK dynamic proxy or CGLIB proxy) that wraps the target object to weave in the advice.

#### Spring's Use of AOP

Transactions (`@Transactional`), security, caching (`@Cacheable`), and logging are common use cases. For `@Transactional`, Spring AOP intercepts calls to methods marked with this annotation and starts/commits/rolls back a transaction automatically.

## Spring Boot

While Spring Framework provides immense flexibility, configuring it can sometimes be verbose. **Spring Boot** came along to radically simplify the bootstrapping and development of new Spring applications. It takes an "opinionated" view of building production-ready applications.

### Core Problems Solved by Spring Boot

- Reduces boilerplate configuration
- Provides sensible defaults for a lot of Spring and third-party libraries.
- Makes it easy to create standalone, production-grade Spring-based Applications.

### key Features and Concepts

#### `@SpringBootApplication`

This is a convenience that adds all of the following:

- `@Configuration`: Tags the class as a source of bean definitions for the application contex.
- `@EnableAutoConfiguration`: Tells Spring Boot to start adding beans based on classpath settings, other beans, and various property settings.
- `@ComponentScan`: Tells Spring to look for the other components, configurations, and services in the specified package (and its sub-packages), allowing it to find and register beans.

Typically, your main application class is annotated with this.

#### Starters (`spring-boot-starter-*`)

These are a set of convenient dependency descriptors that you can include in your application.\
You include a starter, and Spring Boot ensures that all compatible libraries needed for that specific functionality are added to your project with consistent versions.

**Examples:**

- `spring-boot-starter-web`: For buolding web applications using Spring MVC. Includes Tomcat as the default embedded servlet container.
- `spring-boot-starter-data-jpa`: For using Spring Data JPA with Hibernate.
- `spring-boot-starter-test`: For testing Spring Boot applications with libraries like JUnit, Hamcrest, and Mockito.
- `spring-boot-starter-actuator`: For production-ready features like monitoring and metrics.
- `spring-boot-starter-security`: For Spring Security.

#### Auto-Configuration

Spring Boot attempts to automatically configure your Spring application based on the JAR dependencies you have added.\
For example, if `HSQLDB` is on your classpath and you have not manually configured any database connection beans, the Spring Boot auto-configures an in-memory database.\
If you have `spring-boot-starter-web`, it automatically configures things like `DispatcherServlet`, a default error page, etc.\
You can always override auto-configuration by defining your own beans.\
**Conditional Annotations:** Auto configuration works heavily with conditional annotations (e.g., `@ConditionalOnClass`, `@ConditionalOnBean`, `@ConditionalOnProperty`) to decide whether a particular configuration should be applied.

#### Embedded Servers

Spring Boot applications can be easily packaged as executable JARs with embedded servlet containers like Tomcat (default), Jetty, or Undertow.\
This means you don't need to deploy WAR files to an external servlet container. You can just run `java -jar myapp.jar`.

#### Externalized Configuration

Spring Boot allows you to externalize your configuration so you can work with the same application code in different environments.\
It uses a very specific `Property` order, allowing you to override defaults using:

- Properties files (`application.properties` or `application.yml`)
- YAML files (`application.yml`)
- Command-line arguments
- Environment variables
- JNDI attributes

`@ConfigurationProperties`: A powerful way to bind external configuration properties to your Java objects in a type-safe manner.\
**Profiles** (`spring.profiles.active`): Allows you to define different configurations for different environments (e.g., `dev`, `qa`, `prod`). You can have `application-dev.properties`, `application-prod.properties`, etc.

#### Spring Boot Actuator

Provides production-ready features to help you monitor and manage your application.\
Exposes several endpoints (over HTTP or JMX) for health checkers, metrics, application info, environment details, etc.\
**Common Endpoints:**

- `/actuator/health`: Show application health information.
- `/actuator/info`: Displays arbitrary application info.
- `/actuator/metrics`: Show metris for your application.
- `/actuator/env`: Displays ConfigurableEnvironment properties.
- `/actuator/loggers`: View and modify logger levels.

These endpoints are securable and customizable.

### Annotations

@Controller:

- Register the class as a Spring MVC Controller;
- Maps HTTP requests to handler methods;
- Returns a View (for web applications, not APIs);

@Service:

- Specialization of a @Component used to define service layer classes;
- Registers the class as a Spring MVC service;

@Component:

- Registers class as a Spring bean (can be injected into controllers, services, etc)
- Used for general purpose beans (utility classes, custom logic, schedulers, etc)

@Repository:

- Specialization of @Component used for Data Access Objects (DAOs). Marks the class as
  responsible for interacting with the database.

@Autowired:

- Mark dependency as managed by Spring Framework. It creates and injects the dependency;
- Now Spring does this automatically on the constructor;

@SpringBootTest:

- Tell jUnit that we want the application context to be loaded;

@Primary:

- Tell Spring that this is the prefered dependency to autowire;

@Qualifier:

- Tell Spring which dependency to inject if multiple are found;
  Ex. Service interface which is implemented by 2 different services.
  If I have 2 controllers. Each need to implement a different service, we use @Qualifier and specify
  which service class to inject

@Profile and @ActiveProfiles:

- Provides a way to enable/disable beans or configurations depending on the selected environment (ex. dev, test, prod);
  Ex:
  @Profile({"dev", default"}) uses this class if there is no active profile
  @Profile("dev") on a DevDataSourceConfig class (meant for database configuration for dev profile)
  @Profile("prod) on a ProdDataSourceConfig class (meant for database configuration for prod profile)
  @ActiveProfiles("dev") on the test class for the DevProfileTest
  @ActiveProfiles("prod") on the test class for the ProdProfileTest

@ControllerAdvice:

- Provides centralized exception handling for REST APIs. It allows to define a global exception handling for all controllers in the application, making it a powerful tool for managing errors in RESTful services.

@ExceptionHandler:

- Is used to handle exceptions within a specific controller class.

### Testing

#### Unit Tests

- Test individual functions in isolation;
- Run fast since they don't interact with external dependencies;

#### Integration Tests

- Test how multiple components work together;
- Use real database connections, API calls or external services (instead of mocks);
- Ensures the application works as a whole, not just isolated pieces;
- Detects issues with communication between components;

#### Functional Tests

- Tests business logic and expected outputs of an application;
- Can be written as user stories;
- Verifies that user interactions lead to expected results;

#### E2E

- Simulate real user behaviour across the entire application;
- Involves UI, backend, database and external services;

Test Spring MVC controllers:
Spring MockMVC is a testing environment for the testing of SpringMVC controllers - Provides mocks of the Servlet runtime environment - HTTP Request/Response, Dispatcher Servlet, etc; - Simulates the execution of controller as if it was running
under Spring withing Tomcat;
True unit testing when run without the Spring Context;
Technically an integration test when used in conjunction with Spring Context;

#### Spring Boot Test Slices

- Spring Boot supports a concept of what is called Test Slices;
- Test Slices bring up a targeted segment of the Auto-Configured Spring Environment;
- @WebMvcTest is a Spring Boot test slice which creates a MockMVC environment for
  the controller under test

#### Mocks

Controller dependencies must be added to the Spring Context in the test environment;
Types of mocks:

- Dummy: Object used just to get the code to compile;
- Fake: An object that has an implementation, but not production ready;
- Stub: An object with pre-defined answers to method calls;
- Mock: An object with pre-defined answers to method calls, and has
  expectations of executions;
- Spy: In mockito Spies are Mock like wrappers around the actual object;

### Other Concepts

- **AOP:** Aspect-oriented programming. It allows developers to separate cross-cutting concerns such as logging, security, and transaction management from the core business logic, making the codebase more modular and easier to maintain. It encapsulates them into reusable aspects.

Common Cross Cutting Concerns

| Concern      | Where it applies                      |
| ------------ | ------------------------------------- |
| Logging      | Every method entry/exit               |
| Security     | Before executing sensitive operations |
| Transactions | Around service methods (start/commit) |
| Metrics      | Measuring execution time              |
| Caching      | Controlling method result reuse       |

Key AOP Concepts

| Term       | Meaning                                                                |
| ---------- | ---------------------------------------------------------------------- |
| Aspect     | A module that encapsulates cross-cutting behavior (e.g. logging logic) |
| Advice     | Code that runs at a specific join point (e.g. before/after a method)   |
| Join Point | A point in a program execution (e.g. method call, exception)           |
| Pointcut   | An expression that selects join points (e.g. all methods in a package) |
| Weaving    | Linking to the code (can be compile-time, load-time, or runtime)       |
