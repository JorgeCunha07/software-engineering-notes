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

Now you have a reference to the _Class_ object for the _MyObject_ class.\
If you don't know the name at compile time, but have the class name as a string at runtime, you can do like this:

```java
String className = "MyClass";

Class class = Class.forName(className);
```

When using the _Class.forName()_ method you must supply the fully qualified class name. That is the class name including all package names. For instance, if MyObject is located in package _com.example.myapp_ then the fully qualified class name is _com.example.myapp.MyObject_.\
The _Class.forName()_ method may throw a _ClassNotFoundException_ if the class cannot be found on the classpath at runtime.

### Class Name

From a _Class_ object you can obtain its name in two versions. The fully qualified class name (including package name) is obtained using the _getName()_ method like this:

```java
Class aClass = MyObject.class;

String className = aClass.getName();
```

If you want the class name without the package name you can obtain it using the _getSimpleName()_ method, like this:

```java
Class aClass = MyObject.class;

String simpleClass = aClass.getSimpleName();
```

### Modifiers

You can access the modifiers of a class via the _Class_ object. The class modifiers are the keywords "public", "private", "static", etc. You obtain the class modifiers like this:

```java
Class aClass = MyObject.class;

int modifiers = aClass.getModifiers();
```

The modifiers are packed into an _int_ where each modifier is a flag bit that is either set or cleared. You can check the modifiers using these methods in the class _java.lang.reflect.Modifier_:

```java
Modifier.isAbstract(int modifiers)
Modifier.isFinal(int modifiers)
Modifier.isInterface(int modifiers)
Modifier.isNative(int modifiers)
Modifier.isPrivate(int modifiers)
Modifier.isProtected(int modifiers)
Modifier.isPublic(int modifiers)
Modifier.isStatic(int modifiers)
Modifier.isStrict(int modifiers)
Modifier.isSynchronized(int modifiers)
Modifier.isTransient(int modifiers)
Modifier.isVolatile(int modifiers)
```

### Package Info

You can obtain information about the package from a _Class_ object like this:

```java
Class aClass = MyObject.class;

Package package = aClass.getPackage();
```

From the _Package_ object you have access to information about the package like its name. You can also access information specified for this package in the _Manifest_ file of the JAR file this package is located in on the classpath. For instance, you can specify package version numbers in the _Manifest_ file.

### Superclass

From the _Class_ object you can access the superclass of the class. Here is how:

```java
Class superclass = aClass.getSuperclass();
```

The superclass class object is a _Class_ object like any other, so you can continue doing class reflection on that too.

### Implemented Interfaces

It is possible to get a list of the interfaces implemented by a given class. Here is how:

```java
Class aClass = MyObject.class;

Class[] interfaces = aClass.getInterfaces();
```

A class can implement many interfaces. Therefore an array of _Class_ is returned. Interfaces are also represented by _Class_ objects in Java Reflection.

**Note:** Only the interfaces specifically declared implemented by a given class is returned. If a superclass of the class implements an interface, but the class doesn't specifically state that it also implements that interface, that interface will not be returned in the array. Even if the class in practice implements that interface, because the superclass does.

To get a complete list of the interfaces implemented by a given class you will have to consult both the class and its superclasses recursively.

## Fields

Once you have a reference to the _Class_ object representing some class, you can see what fields that class contains and get/set them at runtime.

### Obtaining Field Objects

The _Field_ class is obtained from the _Class_ object. Here is an example:

```java
Class aClass = MyObject.class;

Field[] fields = aClass.getFields();
```

The _Field[]_ array will have one _Field_ instance for each public field declared in the class.

If you know the name of the field you want to access, you can access it like this:

```java
Class aClass = MyObject.class;

Field field = aClass.getField("someField");
```

The example above will return the _Field_ instance corresponding to the field _someField_ as declared in the _MyObject_ below:

```java
public class MyObject {
  public String someField = null;
}
```

If no field exists with the name given as parameter to the _getField()_ method, a _NoSuchFieldException_ is thrown.

### Field Name

Once you have obtained a _Field_ instance, you can get its field name using the _Field.getName()_ method, like this:

```java
String fieldName = field.getName();
```

### Field Type

You can determine the field type (String, int, etc.) of a field using the _Field.getType()_ method:

```java
Field field = aClass.getField("someField");

Object fieldType = field.getType();
```

### Getting and Setting Field Values

Once you have obtained a _Field_ reference you can get and set its values using the _Field.get()_ and _Field.set()_ method like this:

```java
Class aClass = MyObject.class;
Field field = aClass.getField("someField");

MyObject objectInstance = new MyObject();

Object value = field.get(objectInstance);

field.set(objectInstance, value);
```

The _objectInstance_ parameter passed to the _get_ and _set_ method should be an instance of the class that owns the field. In the above example an instance of _MyObject_ is used, because the _someField_ is an instance member of the _MyObject_ class.

If the field is a static field, pass _null_ as parameter to the _get_ and _set_ methods, instead of the _objectInstance_ parameter passed above.

## Constructors

Using Java Reflection it is possible to find out what constructors a given Java class contains and what parameters they take, etc. This is done via the Java class _java.lang.reflect.Constructor_.

### Obtaining Constructor Objects

The _Constructor_ class is obtained from the _Class_ object. Here is an example:

```java
Class aClass = MyObject.class;

Constructor[] constructors = aClass.getConstructors();
```

The _Constructor[]_ array will have one _Constructor_ instance for each public constructor declared in the class.

If you know the precise parameter types of the constructor you want to access, you can do so rather than obtain the array of all constructors. This example returns the public constructor of the given class which takes a _String_ as a parameter:

```java
Class aClass = MyObject.class;

Constructor constructor = aClass.getConstructor(new Class[]{String.class});
```

If no constructor matches the given constructor arguments, in this case _String.class_, a _NoSuchMethodException_ is thrown.

### Constructor Parameters

You can read what parameters a given constructor takes like this:

```java
Class[] parameterTypes = constructor.getParameterTypes();
```

### Instantiating Objects using Constructor Object

You can instantiate an object like this:

```java
Constructor constructor = MyObject.class.getConstructor(String.class);

MyObject myObject = (MyObject) constructor.newInstance("constructor-arg1");
```

The _Constructor.newInstance()_ method takes an optional amount of parameters, but you must supply exactly one parameter per argument in the constructor you are invoking. In this case it was a constructor taking a _String_, so one _String_ must be supplied.

## Methods

You can also see what methods a given class has from its _Class_ object.

### Obtaining Method Objects

The _Method_ class is obtained from the _Class_ object. Here is an example:

```java
Class aClass = MyObject.class;

Method[] methods = aClass.getMethods();
```

The _Method[]_ array will have one _Method_ instance for each public method declared in the class.

If you know the precise parameter types of the method you want to access, you can do so rather than obtain the array of all methods. This example returns the public method named "doSomething", in the given class which takes a _String_ as parameter:

```java
Class aClass = MyObject.class;

Method method = aClass.getMethod("doSomething", new Class[]{String.class});
```

If no method matches the given method name and arguments, in this case _String.class_, a _NoSuchMethodException_ is thrown.

If the method you are trying to access takes no parameters, pass _null_ as the parameter type array, like this:

```java
Class aClass = MyObject.class;

Method method = aClass.getMethod("doSomething", null);
```

### Method Parameters and Return Types

You can read what parameters a given method takes like this:

```java
Class[] parameterTypes = method.getParameterTypes();
```

You can access the return type of a method like this:

```java
Class returnType = method.getReturnType();
```

### Invoking Methods using Method Object

You can invoke a method like this:

```java
Method method = MyObject.class.getMethod("doSomething", String.class);

Object returnValue = method.invoke(null, "parameter-value1");
```

The _null_ parameter is the object you want to invoke the method on. If the method is static you supply _null_ instead of an object instance. In this example, if _doSomething(String.class)_ is not static, you need to supply a valid _MyObject_ instance instead of _null_;

The _Method.invoke(Object target, Object ... parameters)_ method takes an optional amount of parameters, but you must supply exactly one parameter per argument in the method you are invoking. In this case, it was a method taking a _String_, so one _String_ must be supplied.

## Getters and Setters

You can also use Java reflection to find out what getter and setter methods a class has.
You cannot ask for getters and setters explicitly, so you will have to scan through all the methods of a class and check if each method is a getter or a setter yourself - by checking if the method signature matched what you would expect from a getter or setter.

First let's establish the rules that characterizes getters and setters:

- **Getter** - A getter method have its name start with "get", take 0 parameters, and returns a value.

- **Setter** - A setter method have its name start with "set", and takes 1 parameter.

Setters may or may not return a value. Some setters return void, some the value set, others the object the setter were called on for use in method chaining. Therefor you should make no assumptions about the return type of a setter.

Here is a code example that finds getter and setters of a class:

```java
public static void printGettersSetters(Class aClass) {
  Method[] methods = aClass.getMethods();

  for (Method method: methods) {
    if(isGetter(method)) System.out.println("getter: " + method);
    if(isSetter(method)) System.out.println("setter: " + method);
  }
}

public static boolean isGetter(Method method) {
  if(!method.getName().startsWith("get")) return false;
  if(method.getParameterTypes().length != 0) return false;
  if(void.class.equals(method.getReturnType())) return false;

  return true;
}

public static boolean isSetter(Method method) {
  if(!method.getName().startsWith("set")) return false;
  if(method.getParameterTypes().length != 1) return false;

  return true;
}
```

## Private Fields and Methods

Despite the common belief, it is actually possible to access private fields and methods of other classes via Java Reflection. It is not even that difficult. This can be very handy during unit testing.

**Note:** This only works when running the code as a standalone Java application, like you do with unit tests and regular applications. If you try to do this inside a Java Applet, you will need to fiddle around with the SecurityManager.

### Accessing Private Fields

To access a private field you will need to call the _Class.getDeclaredField(String name)_ or _Class.getDeclaredFields()_ method. The method _Class.getField(String name)_ and _Class.getFields()_ methods only return public fields, so they won't work. Here is a simple example of a class with a private field, and below that the code to access that field via Java Reflection:

```java
public class PrivateObject {
  private String privateString = null;

  public PrivateObject(String privateString) {
    this.privateString = privateString;
  }
}
```

```java
PrivateObject privateObject = new PrivateObject("The Private Value");

Field privateStringField = PrivateObject.class.getDeclaredField("privateString");

privateStringField.setAccessible(true);

String fieldValue = (String) privateStringField.get(privateObject);
System.out.println("fieldValue = " + fieldValue);
```

This code example will print out the text "fieldValue = The Private Value", which is the value of the private field _privateString_ of the _PrivateObject_ instance created at the beginning of th code sample.

Notice the use of the method _PrivateObject.ckass.getDeclaredField("privateString")_. It is this method call that returns the private field. This method only returns fields declared in that particular class, not fields declared in any superclasses.

By calling _Field.setAccessible(true)_ you turn off the access checks for this particular _Field_ instance, for reflection only. Now you can access it even if it is private, protected or package scope, even if the caller is not part of those scopes. You still can't access the field using normal code. The compiler won't allow it.

### Accessing Private Methods

To access a private method you will need to call the _Class.getDeclaredMethod(String name, Class[] parameterTypes)_ or _Class.getDeclaredMethods()_ method. The methods _Class.getMethod(String name, Class[] parameterTypes)_ and _Class.getMethods()_ only return public methods, so they won't work. Here is a simple example of a class with a private method, and below that the code to access that method via Java Reflection:

```java
public class PrivateObject {
  private String privateString = null;

  public PrivateObject(String privateString) {
    this.privateString = privateString;
  }

  private String getPrivateString() {
    return this.privateString;
  }
}
```

```java
PrivateObject privateObject = new PrivateObject("The Private Value");

Method privateStringMethod = PrivateObject.class.getDeclaredMethod("getPrivateString", null);

privateStringMethod.setAccessible(true);

String returnValue = (String) privateStringMethod.invoke(privateObject, null);

System.out.println("returnValue = " + returnValue);
```

This code example will print out the text "returnValue = The Private Value", which is the value returned by the method _getPrivateString()_ when invoked on the _PrivateObject_ instance created at the beginning of the code sample.

Notice the use of the method _PrivateObject.class.getDeclaredMethod("privateString")_. It is this method call that returns the private method. This method only returns methods declared in that particular class, not methods declared in any superclasses.

By calling _Method.setAccessible(true)_ you turn off the access checks for this particular _Method_ instance, for reflection only. Now you can access it even if it is private, protected or package scope, even if the caller is not part of those scopes. You still can't access the method using normal code. The compiler won't allow it.

## Annotations

Some Java Annotations are still accessible at runtime. In case a Java class has annotations which are available at runtime, you can access them via Java reflection too.

### What are Java Annotations?

Java Annotations is a feature introduced in Java 5. Annotations are a kind of comment or meta data you can insert in your Java code. These annotations can then be processed at compile time by pre-compiler tools, or at runtime via the Java Reflection. Here is an example of a class annotation:

```java
@MyAnnotation(name="someName", value="Hello World")
public class TheClass {
}
```

The class _TheClass_ has the annotation _@MyAnnotation_ written on top. Annotations are defined like interfaces. Here is the _MyAnnotation_ definition:

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)

public @interface MyAnnotation {
  public String name();
  public String value();
}
```

The @ in front of the _interface_ marks it as an annotation. Once you have defined the annotation you can use it in your code, as shown in the earlier examples.

The two directives in the annotation definition, _@Retention(RetentionPolicy.RUNTIME)_ and _@Target(ElementType.TYPE)_, specifies how the annotation is to be used.

_@Retention(RetentionPolicy.RUNTIME)_ means that the annotation can be accessed via reflection at runtime. If you do not set this directive, the annotation will not be preserved at runtime, and thus not available via reflection.

_@Target(ElementType.TYPE)_ means that the annotation can only be used on top of types (classes and interfaces typically). You can also specify _METHOD_ or _FIELD_, or you can leave the target out alltogether so the annotation can be used for both classes, methods and fields.

### Class Annotations

You can access the annotations of a class, method or field at runtime. Here is an example that accesses the class annotations:

```java
Class aClass = TheClass.class;
Annotation[] annotations = aClass.getAnnotations();

for(Annotation annotation: annotations) {
  if(annotation instanceof MyAnnotation) {
    MyAnnotation myAnnotation = (MyAnnotation) annotation;
    System.out.println("name: " + myAnnotation.name());
    System.out.println("value: " + myAnnotation.value());
  }
}
```

You can also access a specific class annotation like this:

```java
Class aClass = TheClass.class;
Annotation annotation = aClass.getAnnotation(MyAnnotation.class);

if(annotation instanceof MyAnnotation) {
  MyAnnotation myAnnotation = (MyAnnotation) annotation;
  System.out.println("name: " + myAnnotation.name());
  System.out.println("value: " + myAnnotation.value());
}
```

### Method Annotations

Here is an example of a method with annotations:

```java
public class TheClass {
  @MyAnnotation(name="someName", value="Hello World")
  public void doSomething(){}
}
```

You can access method annotations like this:

```java
Annotation annotation = method.getAnnotation(MyAnnotation.class);

if(annotation instanceof MyAnnotation) {
  MyAnnotation myAnnotation = (MyAnnotation) annotation;
  System.out.println("name: " + myAnnotation.name());
  system.out.println("value: " + myAnnotation.value());
}
```

### Parameter Annotations

It is possible to add annotations to method parameter declarations too. Here is how that looks:

```java
public class TheClass {
  public static void doSomethingElse(
    @MyAnnotation(name="aName", value="aValue") String parameter
  ) {}
}
```

You can access parameter annotations from the _Method_ object like this:

```java
Annotation[][] parameterAnnotations = method.getParameterAnnotations();
Class[] parameterTypes = method.getParameterTypes();

int i = 0;

for(Annotation[] annotations : parameterAnnotations) {
  Class parameterType = parameterTypes[i++];

  for(Annotation annotation : annotations) {
    if(annotation instanceof MyAnnotation) {
      MyAnnotation myAnnotation = (MyAnnotation) annotation;
      System.out.println("param: " + parameterType.getName());
      System.out.println("name: " + myAnnotation.name());
      System.out.println("value: " + myAnnotation.value());
    }
  }
}
```

Notice how the _Method.getParameterAnnotations()_ method returns a two-dimensional _Annotation_ array, containing an array of annotations for each method parameter.

### Field Annotations

Here is an example of a field with annotations:

```java
public class TheClass {
  @MyAnnotation(name="someName", value="Hello World")
  public String myField = null;
}
```

You can access field annotations like this:

```java
Annotation[] annotations = field.getDeclaredAnnotations();

for(Annotation annotation : annotations) {
  if(annotation instanceof MyAnnotation) {
    MyAnnotation myAnnotation = (MyAnnotation) annotation;
    System.out.println("name: " + myAnnotation.name());
    System.out.println("value: " + myAnnotation.value());
  }
}
```

You can also access a specific field annotation like this:

```java
Annotation annotation = field.getAnnotation(MyAnnotation.class);

if(annotation instanceof MyAnnotation) {
  MyAnnotation myAnnotation = (MyAnnotation) annotation;
  System.out.println("name: " + myAnnotation.name());
  System.out.println("value: " + myAnnotation.value());
}
```

## Arrays

You can use Java Reflection to introspect Java arrays. For instance, you can determine what type of class the array is an array of. for instance, if you are introspecting a String array, you can detect that the element type is String by inspecting the array class.

## Dynamic Proxies

Java reflection has a special _Proxy_ class that can implement a Java Interface dynamically at runtime, rather than at compile time. The dynamic proxy is given a handler object which intercepts all method calls on the dynamic proxy. This can be a very handy way to solve some types of problems, like adding transaction management around method calls, logging, or other types of desired behavior.

## Generic Types

It is possible to introspect the generic types of fields, method parameters and method return parameters, in case these are declared with a generic type.

### The Generics Reflection Rule of Thumb

Using Java Generics typically falls into one of two different situations:

1. Declaring a class/interface as being parameterizable.
2. Using a parameterizable class.

When you write a class or interface you can specify that it shoul be prameterizable. This is the case with the _java.util.List_ interface. Rather than create a list of _Object_ you can parameterize _java.util.List_ to create a list of say _String_, like this:

```java
List<String> myList = new ArrayList<String>();
```

When inspecting a parameterizable type itself at runtime via reflection, like _java.util.List_, there is no way of knowing what type it has been parameterized to. The object itself does not know what is parameterized to.

However, the reference to the object knows what type including generic type it is referencing. That is, if it is not a local variable. If an object is referenced by a field in an object, then you can look at the Field declaration via reflection, to obtain information about the generic type declared by that field.

The same is possible if the object is referenced by a parameter in a method. Via the _Parameter_ object for that method (a Java reflection object) you can see what generic type that parameter is declared to.

Finally, you can also look at the return type of a method to see what generic type it is declared to. Again, you cannot see it from the actual object returned. You need to look at the method declaration via reflection to see what return type (including generic type) it declares.

To sum it up: You can only see from the declarations of references (fields, parameters, return types) what generic type an object referenced by these references would have. You cannot see it from the object itself.

### Generic Method Return Types

If you have obtained a _java.lang.reflect.Method_ object it is possible to obtain information about its generic return type. Here is an example class with a method having a parameterized return type:

```java
public class MyClass {
  protected List<String> stringList = ....;

  public List<String> getStringList() {
    return this.stringList;
  }
}
```

In this class it is possible to obtain the generic return type of the _getStringList()_ method. In other words, it is possible to detect that _getStringList()_ returns a _List<String>_ and not just a _List_. Here is how:

```java
Method method = MyClass.class.getMethod("getStringList", null);

Type returnType = method.getGenericReturnType();

if(returnType instanceof ParameterizedType) {

  ParameterizedType type = (ParameterizedType) returnType;
  Type[] typeArguments = type.getActualTypeArguments();

  for(Type typeArgument : typeArguments) {
    Class typeArgClass = (Class) typeArgument;
    System.out.println("typeArgClass = " + typeArgClass);
  }
}
```

This piece of code will print out the text "typeArgClass = java.lang.String". The _Type[]_ array _typeArguments_ array will contain one item - a _Class_ instance representing the class _java.lang.String_. _Class_ implements the _Type_ insterface.

## Modules

Is is also possible to introspect a Java Module using reflection too.

## Dynamic Class Loading and Reloading

In Java it is possible to dynamically load and also reload classes using a Java _ClassLoader_. The _ClassLoader_ class is actually not part of the Java Reflection API.
