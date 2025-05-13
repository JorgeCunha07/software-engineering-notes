# Spring Boot

## Key Concepts

### IoC vs Dependency Injection

DI refers to the composition of the classes when injecting a dependency
Ex. @Autowired creates and injects a service in the constructor.

IoC shifts control of the dependencies to the framework. Is a design principle where the control of object creation
and flow is transferred from the application to the framework.
Ex. In the @Service annotation we don't manually create the service, Spring IoC does it for us.

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
