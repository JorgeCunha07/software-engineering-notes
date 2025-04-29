# JavaScript

## Fundamentals

### Closure

A closure is a feature in JavaScript where an inner function has access to the outer (enclosing) function's variables - the scope chain - even after the outer function has finished executing. The inner function "closes over" the environment of the outer function, preserving the values of variables in its scope.

```javascript
function createCounter() {
  let count = 0; // 'count' is part of the lexical environment of the inner function

  // This inner function is a closure
  return function increment() {
    count++;
    console.log(count);
    return count;
  };
}

const counter1 = createCounter(); // Creates one lexical environment { count: 0 }
const counter2 = createCounter(); // Creates another lexical environment { count: 0 }

counter1(); // Output: 1 (accesses its own 'count')
counter1(); // Output: 2
counter2(); // Output: 1 (accesses its own 'count')
// Even though createCounter() finished, increment() still remembers 'count'.
```

### Hoisting

The process whereby the interpreter appears to move the declaration of functions, variables, classes, or imports to the top of their scope, prior to execution of the code.
Hoisting is a JavaScript default behaviour of moving declarations (of variables and functions) to the top of their current scope (either the global or the function scope) before the code execution. Importantly, only the declarations are hoisted, not the initializations (assignments). Function declarations are fully hoisted (name and body), while variables declared with var are hoisted and initialized with undeifined. Variables declared with let and const are hoisted but not initialized; accessing them before their declaration results in a ReferenceError (this is often called the "Temporal Dead Zone").

```javascript
console.log(myVar); // Output: undefined (due to var hoisting)
// console.log(myLet); // Throws ReferenceError: Cannot access 'myLet' before initialization

myFunction(); // Output: "Hello from function!" (Function declarations are fully hoisted)

var myVar = "I am a var";
let myLet = "I am a let";

function myFunction() {
  console.log("Hello from function!");
}

// What the engine conceptually sees after hoisting:
/*
function myFunction() { // Declaration and body hoisted
  console.log("Hello from function!");
}
var myVar; // Declaration hoisted, initialized to undefined
// let myLet; // Declaration hoisted, but not initialized (in Temporal Dead Zone)

console.log(myVar); 
// console.log(myLet); 

myFunction(); 

myVar = "I am a var"; // Initialization happens here
myLet = "I am a let";  // Initialization happens here
*/
```

### Event Loop

JavaScript has a single-threaded execution model, meaning it can only do one thing at a time. The Eent Loop is the mechanism that enables non-blocking asynchronous operations (like setTimeout, DOM events, network requests). It works with the Call Stack and various Queues (like the Callback Queue/Task Queue and the Microtask Queue).

1. Synchronous code runs ont the Call Stack.
2. When an async operation (like setTimeout) is encountered, it's handed off to a Web Api (in the browser) or system API (in Node.js). The main thread continues.
3. When the async operation completes (e.g., the timer finishes), it's callback function is placed in the Callback Queue.
4. The Event Loop constantly checks if the Call Stack is empty.
5. If the Call Stack is empty, the Event Loop takes the first callback from the Callback Queue and pushs it onto the Call Stack for execution.

```javascript
console.log("1. Start"); // Runs first (sync)

setTimeout(() => {
  console.log("2. Timeout callback"); // Added to Callback Queue after ~0ms
}, 0);

Promise.resolve().then(() => {
  console.log("3. Promise resolved (microtask)"); // Added to Microtask Queue immediately
});

console.log("4. End"); // Runs second (sync)

// Expected Output Order:
// 1. Start
// 4. End
// 3. Promise resolved (microtask)  <-- Microtasks run before next tick/Callback Queue
// 2. Timeout callback           <-- Callback Queue runs after stack is clear & microtasks done
```

### Prototypal Inheritance

JavaScript uses prototypal inheritance, unlike the calssical inheritance found in languages like Java or C++. Every JavaScript object has a hidden internal property (often accessible via **proto** or Object.getPrototypeOf()) that links it to another object called it's "prototype". When you try to access a property or method on an object, JavaScript forst looks at the object itself. If it doesn't find it, it looks at the object's prototype, then the prototype's prototype and so on, up the "prototype chain" until it finds the property/method or reaches the end of the chain (usually Object.prototype, which has null as its prototype). ES6 class syntax is primarily syntactic sugar over this prototypal inheritance mechanism.

```javascript
// Using constructor functions (pre-ES6 style)
function Animal(name) {
  this.name = name;
}

// Add methods to the Animal's prototype
Animal.prototype.speak = function () {
  console.log(`${this.name} makes a noise.`);
};

function Dog(name, breed) {
  Animal.call(this, name); // Call parent constructor for properties
  this.breed = breed;
}

// Set up the prototype chain: Dog inherits from Animal
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog; // Reset constructor property

// Add Dog-specific method
Dog.prototype.speak = function () {
  console.log(`${this.name} barks.`); // Overrides Animal's speak
};

const dog1 = new Dog("Buddy", "Golden Retriever");

dog1.speak(); // Output: Buddy barks. (Found on Dog.prototype)
console.log(dog1.name); // Output: Buddy (Found directly on dog1 instance)
console.log(dog1 instanceof Dog); // Output: true
console.log(dog1 instanceof Animal); // Output: true (due to prototype chain)

// ES6 Class equivalent (syntactic sugar)
class AnimalES6 {
  constructor(name) {
    this.name = name;
  }
  speak() {
    console.log(`${this.name} makes a noise.`);
  }
}
class DogES6 extends AnimalES6 {
  constructor(name, breed) {
    super(name); // Calls parent constructor
    this.breed = breed;
  }
  speak() {
    console.log(`${this.name} barks.`);
  }
}
const dog2 = new DogES6("Max", "Labrador");
dog2.speak(); // Output: Max barks.
```

### Type Coercion

Type coercion is the automatic or implicit conversion of values from one data type to another (e.g., string to number, object to boolean) by the JavaScript engine when using operators like ==, +, if(), etc. While sometimes convenient, it can often lead to unexpected results if not understood well. Explicit type conversion (using functions like Number(), String(), Boolean()) is generally preferred for clarity. The === (strict equaly) operator checks for equality without performing the type coercion.

```javascript
console.log("5" == 5); // Output: true (String "5" is coerced to Number 5)
console.log("5" === 5); // Output: false (Strict equality checks type)

console.log(null == undefined); // Output: true (Special coercion rule)
console.log(null === undefined); // Output: false

console.log(true + 1); // Output: 2 (Boolean true coerced to Number 1)
console.log("10" + 5); // Output: "105" (Number 5 coerced to String "5", then concatenation)
console.log("10" - 5); // Output: 5 (String "10" coerced to Number 10, then subtraction)

if ("hello") {
  // Truthy coercion: Non-empty strings are coerced to true in boolean contexts
  console.log("String is truthy");
}
if (0) {
  // Falsy coercion: Number 0 is coerced to false
  // This block won't execute
} else {
  console.log("Number 0 is falsy");
}

// Common falsy values: false, 0, -0, 0n, "", null, undefined, NaN
```

### Debouncing and Throttling

Techniques used to limit the rate at which a function is called, often used for performance optimization whit events that fire frequently (like resize, scoll, input).

- Debouncing: Groups multiple sequential calls to a function into a single call after a certain period of inactivity. It ensures that the function is only executed after the user has stopped triggering the event for a specified delay. Useful for search input suggestions (wait until user stops typing) or saving actions.
- Throttling: Ensures that a function is called at most once within a specified time interval, regardless of how many times the event is triggered during that interval. Usefol for handling a scroll or resize events where you want updates periodically but not excessively.

```javascript
// --- Debounce ---
function debounce(func, delay) {
  let timeoutId;
  return function (...args) {
    clearTimeout(timeoutId); // Clear previous timer
    timeoutId = setTimeout(() => {
      func.apply(this, args); // Call the function after delay
    }, delay);
  };
}

const handleSearchInput = (event) => {
  console.log("Fetching suggestions for:", event.target.value);
  // Imagine an API call here
};

const debouncedSearch = debounce(handleSearchInput, 500); // Wait 500ms after last input
// Attach debouncedSearch to input element's 'input' event

// --- Throttle ---
function throttle(func, limit) {
  let inThrottle = false;
  return function (...args) {
    if (!inThrottle) {
      func.apply(this, args); // Call immediately if not in throttle period
      inThrottle = true;
      setTimeout(() => {
        inThrottle = false; // Allow next call after limit
      }, limit);
    }
  };
}

const handleScroll = () => {
  console.log("Scroll event handled - updating UI...");
  // Imagine UI update logic here
};

const throttledScroll = throttle(handleScroll, 200); // Handle scroll at most every 200ms
// Attach throttledScroll to window's 'scroll' event

// Note: Production apps often use implementations from libraries like Lodash/Underscore
```

### Web Workers

Allow running scripts in background threads, separate from the main execution thread, to perform computationally intensive tasks without blocking the UI.

## Interview Questions

### 1. What is a closure?

A closure is basically defining a function inside a parent sope, and that function will have access to what is defined in the parents scope.

### 2. What is a pure function?

A pure function is a function that given an input, will always return the same output and won't change any state outside the function scope.

### 3. What is function composition?

Function composition is the process of combining two or more functions to producew a new function or perform some computation.

Example:

```javascript
const compose = (f, g) => (x) => f(g(x));

const g = (num) => num + 1;
const f = (num) => num * 2;

const h = compose(f, g);

h(20); // 42 => 20 + 1 = 21 => 21 * 2 = 42
```

### What is functional programming?

Functional programming is a programming paradigm that uses pure functions as the primary units of composition. Composition is so important in software development that virtually all programming paradigms are named after the units of composition they use:

- Object-oriented programming uses objects as the unit of composition.
- Procedural programming uses procedures as the unit of composition.
- Fuctional programming uses functions as the unit of composition.

Functional programming is a declarative programming paradigm, which means that programs are written in terms of what they do, rather than how they do it. This makes functional programs easier to understand, debug, and test than imperative programs. They also tend to be a lot more concise, which reduces code complexity and makes it easier to maintain.

Other key aspects of functional programming include:

- Immutability - immutable data structures are easier to reason about than mutable data structures.
- Higher-order functions - functions that take other functions as arguments or return functions as their result.
- Avoiding shared mutable state - shared mutable state makes programs difficult to understand, debug and test. It also makes it difficult to reason about the correctness of a program.

Since pure functions are easy to test, functional programming also tens to lead to better test coverage and fewer bugs.

### What is a Promise?

A promise in JavaScript is an object representing the eventual completion or failure of an asynchronous operation. It acts as a placeholder for a value that is initially unknown, typically because the computation of its value is not yet complete.

Key characteristics of Promises:

**Stateful:** A Promise is in one of three states:

- **Pending:** Initial state, neither fulfilled nor rejected.
- **Fulfilled:** The operation completed successfully.
- **Rejected:** The operation failed.

**Immutable:** Once a Promise is fulfilled or rejected, its state cannot change. It becomes immutable, permanently holding its result. This makes Promises reliable in asynchronous flow control.

**Chaining:** Promises can be chained, meaning the output of one Promise can be used as input for another. This is done using `.then()` for success or `.catch()` for handling failures, allowing for elegant and readable sequential asynchronous operations. Chaining is the async equivalent of function composition.

```javascript
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("Success!");
    // You could also reject with a new error on failure.
  }, 1000);
});

promise
  .then((value) => {
    console.log(value); // Success!
  })
  .catch((error) => {
    console.log(error);
  });
```

In JavaScript, we can treat promises and promise returning functions as if they are asynchronous, using the async/await syntax. This makes asynchronous code much eaier to read and reason about.

```javascript
const processData = async () => {
  try {
    const data = await fetchData(); // Wait until the Promise is resolved
    console.log("Processed:", data); // Process and display the data
  } catch (error) {
    console.error("Error:", error); // Handle any errors
  }
};
```

### What is TypeScript?

Typescript is a superset of JavaScript, developed and maintained by Microsoft. It adds static typing to JavaScript, which is a dinamically typed language. Static typing helps developers catch errors early in the development process, improving code quality and maintainability.

**Key Features of TypeScript:**

**Static Typing:** Define types for variables and function parameters to ensure consistency throughout the code.

**Enhanced IDE Support:** Integrated Development Environments (IDEs) can provide better autocompletion, navigation, and refactoring, making the development process more efficient.

**Compilation:** TypeScript code is transpiled into JavaScript, making it compatible with any browser or JavaScript environment. During this process, type errors are caught, making the code more robust.

**Interfaces:** Interfaces allow to specify abstract constracts that objects and functions must satisfy.

**Compatibility with JavaScript:** TypeScript is highly compatible with existing JavaScript code. JavaScript code can be gradually migrated to TypeScript, making the transition smooth for existing projects.

### Questions from [here](https://github.com/lydiahallie/javascript-questions/blob/master/README.md)

#### a) What's the output?

```javascript
function sayHi() {
  console.log(name);
  console.log(age);
  var name = "Lydia";
  let age = 21;
}

sayHi();
```

`undefined` and `ReferenceError`.

#### b) What's the output?

```javascript
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 1);
}

for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 1);
}
```

