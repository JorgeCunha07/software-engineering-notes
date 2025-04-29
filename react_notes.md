# React

## Fundamentals

### What is react?

- A library (not a framework) for building user interfaces using components.
- React builds a Virtual DOM to efficiently update and render only the components that change.

### Components

Two main types:

```javascript
// Functional component (modern, preferred)
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

```javascript
// Class component (old, still important to recognize)
class Welcome extends React.component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

Modern React uses functional components with Hooks.

### Hooks

#### useState

Manages local state inside functional components.

```javascript
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Count: {count}</button>
    </div>
  );
}
```

**Notes:**

- `useState` triggers re-render when updated.
- Initial value can be primitive (0, true, '') or objects/arrays.

#### useReducer

Alternative to `useState` for:

- Complex state logic (e.g., multiple related state values)
- State updates depend on the previous state.
- Centralized way to manage actions (like a mini Redux inside a component).

```javascript
import { useReducer } from "react";

// Step 1: Reducer function
function counterReducer(state, action) {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    case "decrement":
      return { count: state.count - 1 };
    case "reset":
      return { count: 0 };
    default:
      throw new Error("Unknown action type");
  }
}

// Step 2: Component
function Counter() {
  const initialState = { count: 0 };
  const [state, dispatch] = useReducer(counterReducer, initialState);

  return (
    <div>
      <h1>Count: {state.count}</h1>
      <button onClick={() => dispatch({ type: "decrement" })}>-</button>
      <button onClick={() => dispatch({ type: "increment" })}>+</button>
      <button onClick={() => dispatch({ type: "reset" })}>Reset</button>
    </div>
  );
}

export default Counter;
```

#### useEffect

**Runs side effects:** data fetching, subscriptions, timers, DOM updates.

```javascript
import { useEffect } from "react";

function Example() {
  useEffect(() => {
    console.log("Component mounted or updated.");

    return () => {
      console.log("Cleanup on unmount or before re-running effect");
    };
  }, []); // Dependency array
}
```

Types of useEffect:

- `[]` - Run once on mount (e.g., fetch data).
- `[var]` - Run when var changes.
- **No dependency array** - Run on every render (usually avoid).

#### useRef

Keeps mutable reference accross renders without causing re-render.

```javascript
import { useRef } from "react";

function Timer() {
  const timerRef = useRef(null);

  function startTimer() {
    timerRef.current = setInterval(() => console.log("tick"), 1000);
  }

  function stopTimer() {
    clearInterval(timerRef.current);
  }

  return (
    <div>
      <button onClick={startTimer}>Start</button>
      <button onClick={stopTimer}>Stop</button>
    </div>
  );
}
```

Also used to reference DOM elements:

```javascript
const inputRef = useRef();
<input ref={inputRef} />;
```

#### useCallback

Memoizes a callback function to prevent unnecessary recreation.

```javascript
import { useCallback, useState } from "react";

function Button({ handleClick }) {
  return <button onClick={handleClick}>Click me</button>;
}

function Parent() {
  const [count, setCount] = useState(0);

  const handleClick = useCallback(() => {
    console.log("Clicked");
  }, []);

  return (
    <div>
      <Button handleClick={handleClick} />
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

Without `useCallback`, `handleClick` would recreate on each re-render -> unnecessary re-renders of children.

#### useMemo

Memoizes a computed value to optimize expensive calculations.

```javascript
import { useMemo, useState } from "react";

function ExpensiveComponent({ number }) {
  const factorial = useMemo(() => {
    console.log("Calculating factorial...");
    let result = 1;

    for (let i = 1; i <= number; i++) result *= i;

    return result;
  }, [number]);

  return <h2>Factorial: {factorial}</h2>;
}
```

Saves performance if calculation is heavy.

### Virtual DOM

- Virtual DOM is a lightweight, in-memory representation of the real DOM elements.
- Is is a plain JavaScript object (or tree of objects) that mirrors the structure of the real DOM.
- React uses the Virtual DOM to minimize direct DOM manipulations, which are expensive operations.

#### Why?

Accessing or updating the browser DOM (with `document.createElement`, `element.appendChild`, etc.) is slow because the browser must repaint, reflow and re-render the page.
React's Virtual DOM batches changes, compares the old and the new virtual tree, and applies the minimal changes to the real DOM.
This makes UI updates faster and smoother.

#### How it works (step-by-step)

##### 1. Render phase

- React calls the component functions and builds a Virtual DOM tree describing what the UI should look like.

##### 2. Diffing phase

- React compares the previous Virtual DOM with the new Virtual DOM.
- It computes the difference (diff) to figure out what actually changed.

##### 3. Reconciliation phase

- Based on the diff, React calculates the minimal set of changes.
- Updates only the changed parts of the real DOM, not the entire page.

##### Example

```javascript
function App() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h1>Counter: {count}</h1>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

After clicking the "Increment" button:

- React builds a new Virtual DOM for `<h1>Counter: 1</h1>`.
- React diffs it against the old Virtual DOM `<h1>Counter: 0</h1>`.
- It sees that only the text inside `<h1>` changed.
- It updates only that text node in the real DOM.
- The rest of the page (like the `<button>`) is untouched.

## Interview Questions

### 1. What is a React Hook?

Hooks are functions that let us use state and other React features without writing a class. Hooks allow the usage of state, context, refs, and component lifecycle events by calling functio9ns instead of writing class methods. The additional flexibility of functions allow us to organize better the code, group related functionality together in a single hook call, and separate unrelated functionality by implementing it in separate function calls. Hooks offer a powerful and expressive way to compose logic inside a component.

Important React Hooks

- `useState` - allows to add state to functional components. State variables are preserved between re-renders. Triggers re-render when updated.
- `useEffect` - allows the performance of side effects in functional components. It combines the capabilities of `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount` into a single function call, reducing the required code
  and creating better code organization than class components.
  - `[]` - Run once on mount (e.g., fetch data).
  - `[var]` - Run when **var** changes
  - **No dependency array** - Run on every render;
- `useContext` - allows the consumption of context in function components.
- `useRef` - allows the creation of a mutable reference that persists for the lifetime of the component.
- `useCallback` - memorizes a function to prevent unnecessary recreation. It returns a function.
- `useMemo`- memorize computed value. Prevents recalculating a value unnecessarily. It returns the result of a function (value).

- **Custom Hooks** - to encapsulate reusable logic. This makes it easy to share logic across different components.

**Rules of Hooks:** Hooks must be used at the top level of React functions (not inside loops, conditions, or nested functions) and only in React function components or custom Hooks.

Hooks solved some common pain points with class components, such as the need to bind methods in the constructor, and the need to split functionality into multiple lifecycle methods. They also make it easier to share logic between components, and to reuse stateful logic without changing the component hierarchy.

### 2. How to create a click counter in React?

We can create a click counter in React by using the `useState` hook as follows:

```javascript
import React, { useState } from "react";

const ClickCounter = () => {
  const [count, setCount] = useState(0); // Initialize count to 0

  return (
    <div>
      <p>Button was clicked {count} times.</p>
      <button onClick={() => setCount((count) => count + 1)}>Click me!</button>
    </div>
  );
};

export default ClickCounter;
```

### 3. Explain how the Virtual DOM works

Explain render → diff → reconcile cycle.

Emphasize that it’s an optimization.

### 4. Why does React use keys in lists?

Keys give React a way to identify which list items have changed.
Without keys, React may re-render all list items unnecessarily.
Good keys are stable and unique (like IDs, not array indexes).

### 5. What is reconciliation?

The process of comparing the old Virtual DOM to the new one.
React decides how to update the real DOM with minimal operations.
Based on a set of assumptions (heuristics like "components with the same key are the same").

### 6. What are some performance pitfalls even with Virtual DOM?

Changing large props unnecessarily (prop drilling or deep prop trees).
Missing React.memo / useMemo when props are heavy.
Missing keys in lists → full re-renders.
Overly frequent state updates → even batching won't help.

### 7. What is React Fiber and how is it related to the Virtual DOM?

React Fiber is the internal reimplementation of the Virtual DOM and rendering algorithm.
Enables incremental rendering: React can pause work, prioritize, and restart rendering work.
Supports Concurrent Mode, useTransition, Suspense, etc.
It's more powerful than the "old" synchronous VDOM.
