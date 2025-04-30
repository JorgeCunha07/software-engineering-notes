# Java

## Key Concepts

### Encapsulation

Is the mechanism of building data (attributes) and the methods that operate on that data into a single unit, often a class. This concept also involves data hiding, where the internal state of an object is protected from unauthorized access and modification from outside the object. In Java, encapsulation is achieved by declaring instance variables as private and providing public getter and setter methods to control access to these variables. This ensures that the internal implementation details are shielded, and data can only be accessed or modified through well-defined interfaces, promoting data intregrity and maintainability.

### Inheritance

Is a powerful mechanism that allows a class (subclass or child class) to inherit the properties (attributes and methods) of another class (superclass or parent class). This fosters code reusability, as common functionalities can be defined in a superclass and then extended by multiple subclasses, avoiding code duplication and adhering to the DRY (Don't Repeat Yourself) principle. Inheritance also establishes an "is-a" relationship between classes, where a sublaclass is a specialized type of its superclass. In Java, the `extends` keyword is used to implement inheritance. Sublasses can also add their own unique fields and methods, or override the methods inherited from the superclass to provide specific implementations.

### Polymorphism

Means "meany forms", is the ability of an object to take on many forms. In Java, polymorphism is primarily achieved through method overloading and method overriding. **Method overloading** (compile-time polymorphism) occurs when multiple methods in the same class have the same name but different parameter lists (different number, order, or types of parameters). The compiler determines which method to call based on the arguments passed during the method invocation. **Method overriding** (run-time polymorphism) happens when a subclass must have the same name, return type, and parameter list as the method in the superclass. The decision of which method to execute is made at runtime based on the actual type of the object.

### Abstraction

Is the process of hiding complex implementation details and showing only the essential information to the user. It allows developers to focus on what an object does rather than how it achieves it, by ignoring irrelevant details. In Java, abstraction is achieved through abstract classes and interfaces. Abstract classes can have abstract methods (methods without implementation) as well as concrete methods (methods with implementation). They cannot be instantiated directly but must be subclassed, and the subclasses typically provide implementations for the abstract methods. Interfaces, on the other hand, define a contract of methods that implementing classes must adhere to. Before Java 8, interfaces could only contain abstract methods and constants, providing 100% abstraction. However, with the introduction of default and static methods in Java 8 and private methods in Java 9, interfaces can now provide some level of implementation.

### Classes

Serve as blueprints or templates for creating objects. A class defines the attributes (data members or fields) and behaviors (methods) that will be shared by all objects of that class. A class declaration includes modifiers, the class name, and the class body containing fields and methods.

### Objects

Are instances of classes. They are fundamental building blocks of OOP, representing specific entities with their own unique state (values of attributes) and the ability to perform the behaviors defined by their class. Objects interact with each other by sending and receiving messages, typically through method invocations.

### Methods

Are blocks of code that perform specific tasks. They allow for organizing code into reusable units and can have parameters (input values) and return a value. Methods are defined within classes and encapsulate the behavior of the objects of that class.

### Access Modifiers

Control the visibility and accessibility of classes, interfaces, variables, methods, and constructors. Java provides four access mofifiers: `public`, `protected`, `private`, and the default (package-private). `public` members are accessible from anywhere, `private` members are accessible only within the same class, `protected` members are accessible within the same package and by subclasses, and package-private members are accessible within the same package. Choosing the appropriate access modifier is crucial for encapsulation and controlling the scope of class members.

## Advanced Java OOP Features

## SOLID Principles of OOP

- S - Single Responsibility Principle
- O - Open Closed Principle
- L - Liskov Substitution Principle
- I - Interface Segregation Principle
- D - Dependency Inversion Principle

### Single Responsibility Principle

Every class should have a single responsibility

### Open Closed Principle

Classes should be open for extension but closed for modification
Should be able to extend a class behaviour, without modifying it
Use private variables with getters and setters only when needed
Use abstract base classes

#### Example

```Java
class Footballer {
    private String name;
    private int age;
    private String role;

    public Footballer(String name, int age, String role) {
        this.name = name;
        this.age = age;
        this.role = role;
    }

    public void getFootballerRole() {
        switch (role.toLowerCase()) {
            case "goalkeeper":
                System.out.println("The footballer, " + name + " is a goalkeeper");
                break;
            case "defender":
                System.out.println("The footballer, " + name + " is a defender");
                break;
            case "midfielder":
                System.out.println("The footballer, " + name + " is a midfielder");
                break;
            case "forward":
                System.out.println("The footballer, " + name + " plays in the forward line");
                break;
            default:
                throw new IllegalArgumentException("Unsupported role: " + role);
        }
    }

    public static void main(String[] args) {
        Footballer player1 = new Footballer("Cristiano Ronaldo", 39, "forward");
        Footballer player2 = new Footballer("Virgil van Dijk", 32, "defender");

        player1.getFootballerRole(); // Output: The footballer, Cristiano Ronaldo plays in the forward line
        player2.getFootballerRole(); // Output: The footballer, Virgil van Dijk is a defender
    }
}
```

The class above violates the Open Closed Principle as there might be more roles to add on the class. The class is not closed for modification.
Instead, the code below should be implemented:

```Java
// Abstract class PlayerRole
abstract class PlayerRole {
    // Abstract method that must be implemented by subclasses
    public abstract String getRole();
}

// Subclasses implementing the abstract method
class GoalkeeperRole extends PlayerRole {
    @Override
    public String getRole() {
        return "goalkeeper";
    }
}

class DefenderRole extends PlayerRole {
    @Override
    public String getRole() {
        return "defender";
    }
}

class MidfieldRole extends PlayerRole {
    @Override
    public String getRole() {
        return "midfielder";
    }
}

class ForwardRole extends PlayerRole {
    @Override
    public String getRole() {
        return "forward";
    }
}

// Footballer class that depends on PlayerRole
class Footballer {
    private String name;
    private int age;
    private PlayerRole role;

    public Footballer(String name, int age, PlayerRole role) {
        this.name = name;
        this.age = age;
        this.role = role;
    }

    public String getRole() {
        return role.getRole();
    }
}

Footballer player1 = new Footballer("Cristiano Ronaldo", 30, new ForwardRole());
Footballer player2 = new Footballer("Virgil van Dijk", 32, new DefenderRole());
```

Now, if a player can have multiple roles, we should use interfaces, as Java allows to implement multiple interfaces but only extend 1 class.
In that case, the code below should be implemented:

```Java
interface PlayerRole {
    String getRole();
}

class GoalkeeperRole implements PlayerRole {
    public String getRole() {
        return "goalkeeper";
    }
}

class DefenderRole implements PlayerRole {
    public String getRole() {
        return "defender";
    }
}

class MidfieldRole implements PlayerRole {
    public String getRole() {
        return "midfielder";
    }
}

class ForwardRole implements PlayerRole {
    public String getRole() {
        return "forward";
    }
}

class Footballer {
    private String name;
    private int age;
    private List<PlayerRole> roles; // A player can have multiple roles

    public Footballer(String name, int age, List<PlayerRole> roles) {
        this.name = name;
        this.age = age;
        this.roles = roles;
    }

    public List<String> getRoles() {
        return roles.stream()
                    .map(PlayerRole::getRole)
                    .toList(); // Collects role names
    }

    public void addRole(PlayerRole role) {
        roles.add(role);
    }
}

List<PlayerRole> roles = new ArrayList<>();
        roles.add(new ForwardRole());
        roles.add(new MidfieldRole());

Footballer player = new Footballer("Cristiano Ronaldo", 39, roles);
```

### Liskov Substitution Principle

Objects in a program would be replaceable with instances of their subtypes without
changing the correctness of the program
If class B extends class A, then objects of type A should be replaceable with objects
of type B without breaking the behavior.
A subclass should not remove or modify functionality in a way that violates the
expected behavior of the parent class.

Ex. Square should not extend a Rectangle class as the square forces all sides to be
the same. Instead, a shape interface/abstract class should be created with a getArea method.
This way, Square and Rectangle can implement/extend Shape and override the area implementation.

### Interface Segregation Principle

Make fine grained interfaces that are client specific
Many client specific interfaces are better than one "general purpose" interface
Keep your components focused and minimize dependencies between them
Notice relationship to the Single Responsibility Principle
A class should only implement the methods that are relevant to its behaviour

Ex. Interface worker with work(), eat() and sleep() methods should not be implemented
by both HumanWorker and RobotWorker.
Instead create 3 interfaces Workable, Eatable and Sleepable. HumanWorker will implement all,
but RobotWorker will only implement the Workable interface

### Dependency Inversion Principle

Abstractions should not depend upon details
Details should not depend upon Abstractions
Important that higher level and lower level objects depend on the same abstract interaction
This is not the same as Dependency Injection - which is how objects obtain dependent objects

Ex. Bad Example (Violation of DIP):

```Java
class EmailSender {
    public void sendEmail(String message) {
        System.out.println("Sending Email: " + message);
    }
}

class NotificationService {
    private EmailSender emailSender;

    public NotificationService() {
        this.emailSender = new EmailSender(); // ❌ DIRECT dependency on EmailSender
    }

    public void notifyUser(String message) {
        emailSender.sendEmail(message);
    }
}
```

NotificationService (high-level module) depends directly on EmailSender (low-level module).
If we want to send SMS or Push notifications, we must modify NotificationService, breaking the Open/Closed Principle.
Hard to test because EmailSender is hardcoded.

Instead of hardcoding EmailSender, we introduce an abstraction (Notifier).

```Java
interface Notifier {
    void send(String message);
}

class EmailNotifier implements Notifier {
    @Override
    public void send(String message) {
        System.out.println("Sending Email: " + message);
    }
}

class SMSNotifier implements Notifier {
    @Override
    public void send(String message) {
        System.out.println("Sending SMS: " + message);
    }
}

class PushNotifier implements Notifier {
    @Override
    public void send(String message) {
        System.out.println("Sending Push Notification: " + message);
    }
}

class NotificationService {
    private Notifier notifier; // ✅ DEPENDS ON ABSTRACTION

    public NotificationService(Notifier notifier) {
        this.notifier = notifier;
    }

    public void notifyUser(String message) {
        notifier.send(message);
    }
}
```

- Each NotificationService is responsible for only one notification type → Clean separation of concerns.
- More flexibility → The controller chooses which notification(s) to send.
- Follows DIP → NotificationService still depends on abstractions (Notifier).
- Easier Testing → We can test emailNotificationService, smsNotificationService, or pushNotificationService separately.

### Interfaces vs Abstract Classes

Interface:

- You only need methods (You can also have default method implementations).
- You want multiple inheritance.
- You want to keep it flexible.
- Not meant for static methods.
- Grants flexibility as we can create another implementation of the interface without modifying the old one.

Abstract Class:
All classes share fields or constructors.
You want default behavior and state together.
