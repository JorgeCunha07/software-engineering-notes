# Java Reflection

Java Reflection makes it possible to inspect classes, interfaces, fields and methods at runtime, without knowing the names of the classes, methods, etc., at compile time. It is also possible to instantiate new objects, invoke methods and get/set field values using reflection.

Java Reflection is quite powerful and can be very useful. For instance, Java Reflection can be used to map properties in JSON files to getter/setter methods in Java objects, like Jackson, GSON, Boon, etc., does. Or, Reflection can be used to map the column names of a JDBC ResultsSet to getter/setter methods in a Java object.

## Java Reflection Example

Here is a quick Java Reflection example to show you what using reflection looks like:

```java
Method[] methods = MyObject.class.getMethods();

for(Method method: methods) {
  System.out.println("method = " + method.getName());
}
```

This example obtains the _Class_ object from the class called _MyObject_. Using the class object the example gets a list of the methods in that class, iterates the methods and print out their names.

## Java Class Object

When using Java reflection, the starting point is often a _Class_ object representing some Java class you want to inspect via reflection.
For instance, to obtain the _Class_ object for a class named _MyObject_ you could write:

```java
Class myObjectClass = MyObject.class;
```

Now you have a reference to the _Class_ object for the _MyObject_ class.

## Fields

Once you have a reference to the _Class_ object representing some class, you can see what fields that class contains.\
Here is an example of accessing fields of a Java class:

```java
Class myObjectClass = MyObject.class;

Field[] fields = myObjectClass.getFields();
```

With a reference to a Java reflection _Field_ instance you can start inspecting the field. You can read its name, access modifiers, etc.

## Constructors

Using Java Reflection it is possible to find out what constructors a given Java class contains and what parameters they take, etc.

## Methods

You can also see what methods a given class has from its _Class_ object. Here is an example of accessing the methods a given class via Java reflection:

```java
Class myObjectClass = MyObject.class;

Method[] methods = myObjectClass.getMethods();
```

Once you have references to a Java reflection _Method_ instance you can start inspecting it.

## Getters and Setters

You can also use Java reflection to find out what getter and setter methods a class has.

## Private Fields and Methods

You can even access private fields and methods via Java reflection - even from outside the class that owns the private field or method.

## Annotations

Some Java Annotations are still accessible at runtime. In case a Java class has annotations which are available at runtime, you can access them via Java reflection too.

## Arrays

You can use Java Reflection to introspect Java arrays. For instance, you can determine what type of class the array is an array of. for instance, if you are introspecting a String array, you can detect that the element type is String by inspecting the array class.

## Dynamic Proxies

Java reflection has a special _Proxy_ class that can implement a Java Interface dynamically at runtime, rather than at compile time. The dynamic proxy is given a handler object which intercepts all method calls on the dynamic proxy. This can be a very handy way to solve some types of problems, like adding transaction management around method calls, logging, or other types of desired behavior.

## Generic Types

It is possible to introspect the generic types of fields, method parameters and method return parameters, in case these are declared with a generic type.

## Modules

Is is also possible to introspect a Java Module using reflection too.

## Dynamic Class Loading and Reloading

In Java it is possible to dynamically load and also reload classes using a Java _ClassLoader_. The _ClassLoader_ class is actually not part of the Java Reflection API.