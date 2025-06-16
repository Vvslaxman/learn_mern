### react life cycle
The **React component lifecycle** refers to the series of methods that are invoked in different stages of a component's life. This lifecycle allows you to control and perform actions in your component at various points in its existence, such as when it is created, updated, or removed from the DOM.

React components can either be **class components** or **functional components**. The lifecycle methods differ slightly depending on the type of component, but the general flow is quite similar. I'll break down the lifecycle in detail for **class components** (as the lifecycle methods are more explicit here) and explain how it relates to **functional components** with **React Hooks**.

---

### **Class Component Lifecycle**

In class components, the lifecycle methods can be divided into **three main phases**:

1. **Mounting** – When the component is being created and inserted into the DOM.
2. **Updating** – When the component is being re-rendered due to changes in state or props.
3. **Unmounting** – When the component is being removed from the DOM.

#### 1. **Mounting Phase**

When a component is being created and inserted into the DOM, the following methods are called in order:

* **constructor(props)**:

  * Called when the component is being instantiated.
  * Used for initializing state, binding methods, or any setup needed before the component renders.
  * Note: If you’re using state, you would typically initialize it here.

  ```jsx
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }
  ```

* **static getDerivedStateFromProps(props, state)**:

  * Called **before every render** (including the first render). It allows you to modify the state based on changes to props.
  * It **returns an object** to update the state, or `null` if no update is necessary.
  * Rarely used, as it can cause confusing behavior if misused.

  ```jsx
  static getDerivedStateFromProps(nextProps, nextState) {
    if (nextProps.value !== nextState.value) {
      return { value: nextProps.value };
    }
    return null;
  }
  ```

* **render()**:

  * This is the most important method, as it determines the JSX structure to be displayed.
  * **Required method** in class components.
  * It returns JSX, which React will then render to the DOM.

  ```jsx
  render() {
    return <h1>{this.state.count}</h1>;
  }
  ```

* **componentDidMount()**:

  * Called **once the component is inserted into the DOM**.
  * Perfect for performing **network requests** or **side effects** like subscribing to events or timers.
  * Can be used to interact with third-party libraries or APIs.

  ```jsx
  componentDidMount() {
    console.log('Component mounted!');
  }
  ```

#### 2. **Updating Phase**

A component is updated whenever there is a change in state or props. The following methods are called when a component re-renders:

* **static getDerivedStateFromProps(props, state)**:

  * Same as in the mounting phase.

* **shouldComponentUpdate(nextProps, nextState)**:

  * Invoked **before rendering** to decide if a re-render is necessary.
  * It can return `true` to allow the re-render or `false` to prevent it.
  * Used for performance optimization, especially in cases where you know the component doesn’t need to re-render.

  ```jsx
  shouldComponentUpdate(nextProps, nextState) {
    return nextState.count !== this.state.count;
  }
  ```

* **render()**:

  * Same as in the mounting phase, but it gets called on every update.

* **getSnapshotBeforeUpdate(prevProps, prevState)**:

  * Called **right before the DOM is updated**. It allows you to capture information from the DOM (e.g., scroll position) before the update occurs.
  * It returns a value that will be passed to `componentDidUpdate`.

  ```jsx
  getSnapshotBeforeUpdate(prevProps, prevState) {
    return prevState.count;
  }
  ```

* **componentDidUpdate(prevProps, prevState, snapshot)**:

  * Called **after the component updates** and the DOM is updated.
  * Can be used for tasks like **fetching new data**, or interacting with the DOM based on the previous state or props.

  ```jsx
  componentDidUpdate(prevProps, prevState, snapshot) {
    if (this.state.count !== prevState.count) {
      console.log('Count updated!');
    }
  }
  ```

#### 3. **Unmounting Phase**

When a component is about to be removed from the DOM, the following method is called:

* **componentWillUnmount()**:

  * Called **just before the component is removed** from the DOM.
  * This is a great place to clean up any side effects like timers, subscriptions, or listeners.

  ```jsx
  componentWillUnmount() {
    console.log('Component will unmount!');
  }
  ```

### **1. Class Component Example**

We’ll start with a **class component** and then replicate the same functionality using **functional components**.

#### **Class Component Example**: Lifecycle Methods in Action

We'll simulate a simple **counter** app, where:

* On mount, we fetch some data from an API.
* On update, we log changes to the `count`.
* On unmount, we clear any intervals or subscriptions.

**Class Component**: `LifecycleExample.js`

```jsx
import React, { Component } from 'react';

class LifecycleExample extends Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0,
      data: null,
    };
    this.interval = null; // For demonstration purposes
  }

  // Called before the component mounts (first render)
  static getDerivedStateFromProps(nextProps, nextState) {
    console.log('getDerivedStateFromProps');
    // You could update the state based on props here
    return null; // No state update
  }

  // Called once component is mounted
  componentDidMount() {
    console.log('componentDidMount');
    // Simulate fetching data from an API
    setTimeout(() => {
      this.setState({ data: 'Fetched Data from API!' });
    }, 2000);

    // Start an interval
    this.interval = setInterval(() => {
      console.log('Interval Running');
    }, 1000);
  }

  // Called before rendering when state or props change
  shouldComponentUpdate(nextProps, nextState) {
    console.log('shouldComponentUpdate');
    return nextState.count !== this.state.count; // Prevent unnecessary re-renders
  }

  // Called after the update occurs (DOM is updated)
  componentDidUpdate(prevProps, prevState) {
    console.log('componentDidUpdate');
    if (prevState.count !== this.state.count) {
      console.log(`Count has been updated to: ${this.state.count}`);
    }
  }

  // Cleanup before the component unmounts
  componentWillUnmount() {
    console.log('componentWillUnmount');
    // Clear the interval to prevent memory leaks
    clearInterval(this.interval);
  }

  // Increment the count
  increment = () => {
    this.setState(prevState => ({ count: prevState.count + 1 }));
  };

  render() {
    const { count, data } = this.state;
    console.log('render');

    return (
      <div>
        <h1>Counter: {count}</h1>
        <button onClick={this.increment}>Increment</button>
        <p>{data ? data : 'Loading data...'}</p>
      </div>
    );
  }
}

export default LifecycleExample;
```

### **What’s Happening in the Class Component:**

1. **Mounting Phase:**

   * `constructor`: Initializes state (`count` and `data`).
   * `getDerivedStateFromProps`: Not used here, but it would be called before every render.
   * `componentDidMount`: Simulates a network request by setting data after 2 seconds and starts an interval that logs a message every second.

2. **Updating Phase:**

   * `shouldComponentUpdate`: Prevents re-renders if the count hasn’t changed.
   * `componentDidUpdate`: Logs the count change whenever `count` is updated.

3. **Unmounting Phase:**

   * `componentWillUnmount`: Cleans up the interval before the component is removed from the DOM.

---


### **Functional Components with React Hooks**

In functional components, you don't have explicit lifecycle methods. Instead, you manage the lifecycle using **React Hooks**, particularly the `useEffect` hook, which can replicate the behavior of class component lifecycle methods.

#### 1. **Mounting** (`componentDidMount` in class components)

Use the `useEffect` hook with an empty dependency array (`[]`) to replicate the `componentDidMount` lifecycle method:

```jsx
useEffect(() => {
  console.log('Component mounted!');
}, []);
```

#### 2. **Updating** (`componentDidUpdate` in class components)

To replicate `componentDidUpdate`, use the `useEffect` hook with specific dependencies. This effect will run when any of the dependencies change:

```jsx
useEffect(() => {
  console.log('Component updated!');
}, [count]);  // Runs when 'count' changes
```

#### 3. **Unmounting** (`componentWillUnmount` in class components)

To handle cleanup, you return a **cleanup function** from the `useEffect` hook:

```jsx
useEffect(() => {
  // Set up side effects

  return () => {
    console.log('Component will unmount!');
  };
}, []);  // Cleanup when the component unmounts
```

#### 4. **Handling Side Effects** (`getDerivedStateFromProps`, `shouldComponentUpdate`)

You can handle updates to state or props within the `useEffect` hook. However, for controlling performance or derived state, you might also use `useMemo` or `useCallback` hooks.

---

### **Summary**

To summarize, the React lifecycle methods give you control over the following stages in your component's life:

* **Mounting**: The component is created and added to the DOM. Methods: `constructor`, `getDerivedStateFromProps`, `render`, `componentDidMount`.
* **Updating**: The component re-renders due to changes in state or props. Methods: `getDerivedStateFromProps`, `shouldComponentUpdate`, `render`, `getSnapshotBeforeUpdate`, `componentDidUpdate`.
* **Unmounting**: The component is removed from the DOM. Method: `componentWillUnmount`.

For functional components, you’ll use hooks like `useEffect`, `useState`, `useMemo`, and `useCallback` to handle side effects and updates at appropriate times during the component's lifecycle.



### **2. Functional Component with Hooks**

Now let’s replicate the same logic using **functional components** with **React hooks** (`useState`, `useEffect`).

**Functional Component**: `LifecycleExampleFunctional.js`

```jsx
import React, { useState, useEffect } from 'react';

const LifecycleExampleFunctional = () => {
  const [count, setCount] = useState(0);
  const [data, setData] = useState(null);

  // Mounting Phase (Simulate componentDidMount)
  useEffect(() => {
    console.log('componentDidMount (Effect with [] as dependency)');

    // Simulate fetching data from an API
    setTimeout(() => {
      setData('Fetched Data from API!');
    }, 2000);

    // Start an interval (like componentDidMount)
    const interval = setInterval(() => {
      console.log('Interval Running');
    }, 1000);

    // Cleanup before unmounting (like componentWillUnmount)
    return () => {
      console.log('Cleanup (componentWillUnmount)');
      clearInterval(interval); // Clear the interval to prevent memory leak
    };
  }, []); // Empty array means this effect runs once on mount

  // Updating Phase (Simulate componentDidUpdate)
  useEffect(() => {
    if (count > 0) {
      console.log('componentDidUpdate: Count updated to', count);
    }
  }, [count]); // This effect runs every time 'count' changes

  const increment = () => {
    setCount(prevCount => prevCount + 1);
  };

  return (
    <div>
      <h1>Counter: {count}</h1>
      <button onClick={increment}>Increment</button>
      <p>{data ? data : 'Loading data...'}</p>
    </div>
  );
};

export default LifecycleExampleFunctional;
```

### **What’s Happening in the Functional Component:**

1. **Mounting Phase**:

   * `useEffect` with an empty dependency array (`[]`): Runs after the component mounts, simulating `componentDidMount`.
   * Starts an interval to log every second and fetches data (simulating an API call).
   * The return function in `useEffect` acts like `componentWillUnmount`, cleaning up the interval when the component unmounts.

2. **Updating Phase**:

   * `useEffect` with `[count]` as the dependency: Runs every time the `count` value changes, simulating `componentDidUpdate`.

3. **Unmounting Phase**:

   * Cleanup function inside `useEffect`: Clears the interval, simulating `componentWillUnmount`.

---

### **3. Key Differences Between Class Components and Functional Components**

* **State Management**:

  * In class components, you use `this.state` and `this.setState`. In functional components, you use `useState`.

* **Lifecycle Methods**:

  * Class components have lifecycle methods like `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount`.
  * Functional components rely on the `useEffect` hook to manage side effects. The cleanup mechanism is also built into `useEffect` through the return function.

* **Performance Optimizations**:

  * Class components can use `shouldComponentUpdate` for optimization.
  * In functional components, you can optimize using `useMemo` or `useCallback`.

---



4. Observe the lifecycle methods in action by checking the browser's console. You'll see logs for when the component mounts, updates, and unmounts.

---

### **Summary**

* **Class components** have explicit lifecycle methods like `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount`.
* **Functional components** rely on the `useEffect` hook for handling side effects and cleanup, making them more concise and functional.


## props vs context
In React, **props** and **context** are both mechanisms for passing data, but they serve different purposes and have different use cases:

---

### 🔹 **Props**

**Definition:**
Props (short for *properties*) are how data is passed **from parent to child** components.

**Key Characteristics:**

* Unidirectional (top-down) data flow.
* Explicit: the parent passes props to the child.
* Ideal for isolated, reusable components.
* No global scope—each parent must explicitly pass data to each child.

**Example:**

```jsx
function Greeting({ name }) {
  return <h1>Hello, {name}!</h1>;
}

<Greeting name="Alice" />
```

---

### 🔹 **Context**

**Definition:**
Context provides a way to share data **globally** (or deeply) across the component tree **without passing props manually at every level**.

**Key Characteristics:**

* Good for global state (like theme, language, auth).
* Avoids "prop drilling" (passing props through many intermediate components).
* Can be consumed anywhere in the component tree.

**Example:**

```jsx
const ThemeContext = React.createContext('light');

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function Toolbar() {
  return <ThemedButton />;
}

function ThemedButton() {
  const theme = React.useContext(ThemeContext);
  return <button className={theme}>Click me</button>;
}
```

---

### 🔄 **Summary Comparison**

| Feature       | Props                              | Context                                   |
| ------------- | ---------------------------------- | ----------------------------------------- |
| Data Flow     | Parent → Child                     | Any → Any (within Provider tree)          |
| Scope         | Local to component tree            | Global (within Context Provider)          |
| Use Case      | Customization per component        | Global settings or shared data            |
| Performance   | More predictable, fewer re-renders | Can cause unwanted re-renders if overused |
| Prop Drilling | Required                           | Avoided                                   |

---

Use **props** for specific, controlled data sharing.
Use **context** when you need to share **state globally or deeply** across many layers.

