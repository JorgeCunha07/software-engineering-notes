# Design Patterns in Java OOP

Deisgn patterns are reusable solutions to commonly occurring problems in software design. They represent best practices and provide a common vocabulary for discussing software design soltutions. Design patterns are generally categorized into three types: creations, structural, and behavioral.

## Creational Design Patterns

Deal with object creation mechanisms, aiming to create objects in a controlled and flexible way.

| Pattern Name     | Intent                                                                                                                               | Key Use Cases                                                                    | Identification                                                                                                                                                                                                                                                          |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Singleton        | Ensure a class has only one instance and provide a global point of access.                                                           | Logging, configuration, thread pools, caching.                                   | Singleton can be recognized by a static creation method, which returns the same cached object.                                                                                                                                                                          |
| Factory Method   | Provides an interface for creating objects in a superclass, but allows subclasses to alter the type of objects that will be created. | When the exact type of object needed at runtime is unknown.                      | Factory methods can be recognized by creation methods that construct objects from concrete classes. While concrete classes are used during the object creation, the return type of the factory methods is usually declared as either an abstract class or an interface. |
| Abstract Factory | Create families of related or dependent objects without specifying their concrete classes.                                           | When a system needs to be independent of how its products are created.           | The pattern is easy to recognize by methods, which return a factory object.Then, the factory is used for creating specific sub-components.                                                                                                                              |
| Builder          | Separate the construction of a complex object from its representation.                                                               | When an object has many optional parameters or needs to be created step by step. | The Builder pattern can be recognized in a class, which has a single creation method and several methods to configure the resulting object. Builder methods often support chaining (for example, `someBuilder.setValueA(1).setValueB(2).create()`).                     |
| Prototype        | Create new objects by copying an existing object.                                                                                    | When creating an object is expensive or complex.                                 | The prototype can be easily recognized by a `clone` or `copy` methods, etc.                                                                                                                                                                                             |

### Code Examples

#### Singleton

```java
public final class Singleton {
    private static Singleton instance;
    public String value;

    private Singleton(String value) {
        this.value = value;
    }

    public static Singleton getInstance(String value) {
        if(instance == null) {
            instance = new Singleton(value);
        }

        return instance;
    }
}
```

#### Factory Method

##### buttons/Button.java: Common product interface

```java
/**
 *  Common interface for all buttons.
 */
public interface Button {
    void render();
    void onClick();
}
```

##### buttons/HtmlButton.java: Concrete product

```java
public class HtmlButton implements Button {

    public void render() {
        System.out.println("<button>Test Button</button>");
        onClick();
    }

    public void onClick() {
        System.out.println("Click! Button says - 'Hello World!'");
    }
}
```

##### factory/dialog.java: Base creator

```java
/**
 * Base factory class. Not that "factory" is merely a role for the class. It
 * should have some core business logic which needs different products
 * to be created.
 */
public abstract class Dialog {

  public void renderWindow() {
    // ... other code ...

    Button okButton = createButton();
    okButton.render();
  }

  /**
   * Subclasses will override this method in order to create specific button
   * objects.
   */
  public abstract Button createButton();
}
```

##### factory/HtmlDialog.java: Concrete creator

```java
/**
 * HTML Dialog will produce HTML buttons.
 */
public class HtmlDialog extends Dialog {

  @Override
  public Button createButton() {
    return new HtmlButton();
  }
}
```

#### Abstract Factory

##### buttons: first Product hierarchy

##### buttons/Button.java

```java
/**
 * Abstract Factory assumes that you have several families of products,
 * structured into separate class hierarchies (Button/Checkbox). All products of
 * the same family have the common interface.
 *
 * This is the common interface for buttons family.
 */
public interface Button {
    void paint();
}
```

##### buttons/MacOSButton.java

```java
/**
 * All products families have the same varieties (MacOS/Windows).
 *
 * This is a MacOS variant of a button.
 */
public class MacOSButton implements Button {

    @Override
    public void paint() {
        System.out.println("You have created MacOSButton.");
    }
}
```

##### buttons/WindowsButton.java

```java
/**
 * All products families have the same varieties (MacOS/Windows).
 *
 * This is another variant of a button.
 */
public class WindowsButton implements Button {

    @Override
    public void paint() {
        System.out.println("You have created WindowsButton.");
    }
}
```

##### checkboxes: Second product hierarchy

##### checkboxes/Checkbox.java

```java
/**
 * Checkboxes is the second product family. It has the same variants as buttons.
 */
public interface Checkbox {
    void paint();
}
```

##### checkboxes/MacOSCheckbox.java

```java
/**
 * All products families have the same varieties (MacOS/Windows).
 *
 * This is a variant of a checkbox.
 */
public class MacOSCheckbox implements Checkbox {

    @Override
    public void paint() {
        System.out.println("You have created MacOSCheckbox.");
    }
}
```

##### checkboxes/WindowsCheckbox.java

```java
/**
 * All products families have the same varieties (MacOS/Windows).
 *
 * This is another variant of a checkbox.
 */
public class WindowsCheckbox implements Checkbox {

    @Override
    public void paint() {
        System.out.println("You have created WindowsCheckbox.");
    }
}
```

##### factories

##### factories/GUIFactory.java: Abstract factory

```java
/**
 * Abstract factory knows about all (abstract) product types.
 */
public interface GUIFactory {
    Button createButton();
    Checkbox createCheckbox();
}

```

##### factories/MacOSFactory.java: Concrete factory (macOS)

```java
/**
 * Each concrete factory extends basic factory and responsible for creating
 * products of a single variety.
 */
public class MacOSFactory implements GUIFactory {

    @Override
    public Button createButton() {
        return new MacOSButton();
    }

    @Override
    public Checkbox createCheckbox() {
        return new MacOSCheckbox();
    }
}
```

##### factories/WindowsFactory.java: Concrete factory (Windows)

```java
/**
 * Each concrete factory extends basic factory and responsible for creating
 * products of a single variety.
 */
public class WindowsFactory implements GUIFactory {

    @Override
    public Button createButton() {
        return new WindowsButton();
    }

    @Override
    public Checkbox createCheckbox() {
        return new WindowsCheckbox();
    }
}
```

#### Prototype

##### shapes: Shape list

##### shapes/Shape.java: Common shape interface

```java
public abstract class Shape {
    public int x;
    public int y;
    public String color;

    public Shape() {
    }

    public Shape(Shape target) {
        if (target != null) {
            this.x = target.x;
            this.y = target.y;
            this.color = target.color;
        }
    }

    public abstract Shape clone();

    @Override
    public boolean equals(Object object2) {
        if (!(object2 instanceof Shape)) return false;
        Shape shape2 = (Shape) object2;
        return shape2.x == x && shape2.y == y && Objects.equals(shape2.color, color);
    }
}
```

##### shapes/Circle.java: Simple shape

```java
public class Circle extends Shape {
    public int radius;

    public Circle() {
    }

    public Circle(Circle target) {
        super(target);
        if (target != null) {
            this.radius = target.radius;
        }
    }

    @Override
    public Shape clone() {
        return new Circle(this);
    }

    @Override
    public boolean equals(Object object2) {
        if (!(object2 instanceof Circle) || !super.equals(object2)) return false;
        Circle shape2 = (Circle) object2;
        return shape2.radius == radius;
    }
}
```

##### shapes/Rectangle.java: Another shape

```java
public class Rectangle extends Shape {
    public int width;
    public int height;

    public Rectangle() {
    }

    public Rectangle(Rectangle target) {
        super(target);
        if (target != null) {
            this.width = target.width;
            this.height = target.height;
        }
    }

    @Override
    public Shape clone() {
        return new Rectangle(this);
    }

    @Override
    public boolean equals(Object object2) {
        if (!(object2 instanceof Rectangle) || !super.equals(object2)) return false;
        Rectangle shape2 = (Rectangle) object2;
        return shape2.width == width && shape2.height == height;
    }
}
```

## Strucural Design Patterns

Focus on how classes and objects are composed to form larger structures.

| Pattern Name | Intent                                                                    | Key Use Cases                                                            | Example in Core Java                                        |
| ------------ | ------------------------------------------------------------------------- | ------------------------------------------------------------------------ | ----------------------------------------------------------- |
| Adapter      | Allow objects with incompatible interfaces to work together.              | When you need to use an existing class with a different interface.       | `java.util.Arrays#asList()`                                 |
| Bridge       | decouple an abstraction from its implementation.                          | When both abstraction and implementation can vary independently.         | JDBC API                                                    |
| Composite    | Compose objects into tree structures representing part-whole hierarchies. | When you need to treat individual objects and compositions uniformly.    | `java.awt.Container`                                        |
| Decorator    | Dynamically add responsibilities to an object.                            | When you need to add behavior to objects without subclassing.            | Subclasses of `java.io.InputSretam`, `java.io.OutputStream` |
| Facade       | Provide a simplified interface to a complex subsystem.                    | To hide the complexity of a subsystem from the client.                   | Faces API's `ExternalContext`                               |
| Flyweight    | Reduce memory usage by sharing common parts of state between objects.     | When you need to create a large number of similar objects.               | `IntegerCache` in `java.lang.Integer`                       |
| Proxy        | Provide a placeholder for another object to control access to it.         | To control access to an object or add functionality before/after access. | `java.lang.reflect.Proxy`                                   |

## Behavioral Design Patterns

Are concerned with the interaction and communication between objects.

| Pattern Name            | Intent                                                                                      | Key Use Cases                                                                         | Example in Core Java                                                  |
| ----------------------- | ------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- | --------------------------------------------------------------------- |
| Chain of Responsibility | Pass a request along a chain of handlers.                                                   | When multiple objects can handle a request, and the handler isn't known in advance.   | Servlet filters                                                       |
| Command                 | Encapsulate a request as an object.                                                         | to support undoable operations, queuing, or logging of requests.                      | Swing `Action`                                                        |
| Iterator                | Provide a way to access elements of an aggregate object sequentially.                       | to traverse a collection without exposing its underlying structure.                   | `java.util.Iterator`                                                  |
| Mediator                | Define an object that encapsulates how a set of objects interact.                           | To reduce dependencies between objects by centralizing their interaction.             | JSM (Java Message Service)                                            |
| Memento                 | Capture and externalize an object's internal state for later restoration.                   | To implement "undo" functionality.                                                    | Implementing undo/redo operations                                     |
| Observer                | Define a one-to-many dependency so that when one object changes state, others are notified. | To implement event handling systems.                                                  | `java.util.Observer`                                                  |
| State                   | Allow an object to alter its behavior when its internal state changes.                      | When an object's behavior depends on its state, and it can transition between states. | A traffic light changing colors                                       |
| Strategy                | Define a family of algorithms and make them interchangeable.                                | To select an algorithm at runtime.                                                    | `java.util.Comparator` with `Collections.sort()`                      |
| Template Method         | Define the skeleton of an algorithm in a method, deferring some steps.                      | To allow subclasses to override specific steps of an algorithm.                       | Non-abstract methods in `java.io.InputStream`, `java.io.OutputStream` |
| Visitor                 | Represent an operation to be performed on elements of an object structure.                  | To add new operations to a structure without modifying the structure itself.          | Travesing and operating on nodes of a tree.                           |
