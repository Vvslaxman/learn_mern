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

Perfect — let’s **master Node.js** step by step **from scratch to advanced**, so you can ace any **Node.js interview** question, build **robust backend APIs**, and **connect it later with MongoDB, React, and Express**.

---

## ✅ NODE.JS — FROM SCRATCH TO PRO INTERVIEW LEVEL

### 🔷 What is Node.js?

* **Node.js** is a **JavaScript runtime built on Chrome's V8 engine**.
* It lets you run **JavaScript on the server** (outside the browser).
* It uses **non-blocking I/O** (asynchronous), perfect for fast APIs and microservices.

📌 **Use cases**:

* REST APIs
* Real-time apps (chat, notifications)
* File system tools (CLI tools)
* Microservices and backend logic

---

## 📦 1. Setting Up Node.js Project

```bash
mkdir node-crash
cd node-crash
npm init -y       # creates package.json
```

Install packages:

```bash
npm install express cors dotenv
```

Create an entry file:

```bash
touch index.js
```

---

## 🔑 2. Core Concepts of Node.js

### ✅ `require` and `module.exports`

```js
// greet.js
function sayHello(name) {
  return `Hello, ${name}`;
}
module.exports = sayHello;

// index.js
const sayHello = require('./greet');
console.log(sayHello("Skailama"));  // Hello, Skailama
```

---

### ✅ Built-in Core Modules

1. **`fs`** – File system
2. **`path`** – Work with file paths
3. **`os`** – System info
4. **`http`** – Build web servers

#### 🔸 `fs` Example:

```js
const fs = require('fs');
fs.writeFileSync('file.txt', 'Hello from Node!');
const data = fs.readFileSync('file.txt', 'utf8');
console.log(data); // Hello from Node!
```

#### 🔸 `http` Server:

```js
const http = require('http');

http.createServer((req, res) => {
  res.write('Server Running');
  res.end();
}).listen(3000);
```

---

## 🧠 3. Event-Driven & Async Programming

### ✅ Event Loop

* Node.js handles concurrent operations using an **event loop**.

### ✅ Callbacks

```js
fs.readFile('file.txt', 'utf8', (err, data) => {
  if (err) return console.error(err);
  console.log(data);
});
```

### ✅ Promises

```js
const readFile = (file) => {
  return new Promise((resolve, reject) => {
    fs.readFile(file, 'utf8', (err, data) => {
      if (err) reject(err);
      else resolve(data);
    });
  });
};

readFile('file.txt').then(console.log).catch(console.error);
```

### ✅ Async/Await

```js
const read = async () => {
  try {
    const data = await readFile('file.txt');
    console.log(data);
  } catch (err) {
    console.error(err);
  }
};
read();
```

---

## 🛠 4. Building a Node.js REST API (without Express)

```js
const http = require('http');
const fs = require('fs');

const server = http.createServer((req, res) => {
  if (req.url === "/api") {
    res.writeHead(200, { "Content-Type": "application/json" });
    res.end(JSON.stringify({ message: "Hello from raw Node.js API" }));
  }
});

server.listen(5000, () => console.log("Server on 5000"));
```

---

## 🌐 5. NPM Modules & `package.json`

* `npm install axios` — installs 3rd party module
* `npm install -D nodemon` — for dev-only auto reload

### `package.json`:

```json
{
  "name": "app",
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js"
  }
}
```

Run in dev mode:

```bash
npm run dev
```

---

## 🔁 6. Environment Variables with `dotenv`

Install:

```bash
npm i dotenv
```

`.env`:

```
PORT=5000
```

Usage:

```js
require('dotenv').config();
const port = process.env.PORT || 3000;
```

---

## 🔐 7. Important Interview Concepts

| Concept              | Explain                                             |
| -------------------- | --------------------------------------------------- |
| **Single-threaded**  | Uses one thread with event loop for concurrency     |
| **Non-blocking I/O** | Async operations so server isn’t blocked            |
| **Event loop**       | Handles async callbacks in phases                   |
| **Streams**          | For handling large data efficiently (videos, files) |
| **Buffer**           | Raw binary data                                     |
| **Cluster**          | For scaling Node.js using multiple cores            |

---

## 🎯 Hands-On Challenge for You

Build a **mini CLI tool** that:

* Takes input from the user (`process.argv`)
* Writes it into a file using `fs`
* Reads the file and logs to console

Example:

```bash
node cli.js "Skailama is awesome"
```

---

## 🧪 Node.js Sample Interview Questions

1. **How does Node.js handle concurrency if it's single-threaded?**

   * Event loop and callback queue handle multiple requests asynchronously using non-blocking I/O.

2. **What is the difference between synchronous and asynchronous functions in Node?**

   * Sync blocks the execution, async continues the execution while waiting for callback.

3. **What are streams and buffers in Node.js?**

   * Streams let you read/write data in chunks (e.g., video file).
   * Buffers are used to temporarily store binary data.

4. **What is `process.nextTick()` vs `setImmediate()`?**

   * `nextTick()` runs before I/O tasks; `setImmediate()` after.

5. **What is a middleware in context of Express/Node?**

   * A function that runs before final route logic to manipulate `req` or `res`.

---

## ✅ Summary Cheatsheet

| Feature    | Summary                              |
| ---------- | ------------------------------------ |
| Node.js    | Server-side JS runtime               |
| Modules    | `require()`, `module.exports`        |
| Built-ins  | `fs`, `http`, `path`, `os`           |
| Async      | `callback`, `promise`, `async/await` |
| API Server | `http.createServer()`                |
| NPM        | Manage dependencies                  |
| Env        | `dotenv`, `process.env`              |
| Event loop | Handles all async tasks in phases    |

---

Awesome! Let’s now **master Express.js** — the most important backend framework in the MERN stack.

---

## 🔥 EXPRESS.JS — FROM SCRATCH TO PRO INTERVIEW LEVEL

### 🔷 What is Express.js?

* A **web application framework** for **Node.js**.
* Helps build APIs, web servers, and apps faster.
* Think of it as **simplified backend logic** built on top of Node.js’s `http` module.

📌 Used in Skailama-like SaaS apps to:

* Build **RESTful APIs**
* Add **authentication**
* Handle **routes**, **middlewares**, **requests/responses**

---

## ⚙️ 1. Setup Express Project

```bash
mkdir express-api
cd express-api
npm init -y
npm install express cors dotenv
touch index.js
```

```js
// index.js
const express = require('express');
const app = express();
const PORT = process.env.PORT || 5000;

app.use(express.json());

app.get('/', (req, res) => {
  res.send('Skailama Backend Running');
});

app.listen(PORT, () => console.log(`Server running on ${PORT}`));
```

---

## 📌 2. Routes, Params, and CRUD API

### Basic Routing

```js
app.get('/hello', (req, res) => {
  res.send('Hello World');
});
```

### Route with Params

```js
app.get('/user/:id', (req, res) => {
  res.send(`User ID: ${req.params.id}`);
});
```

### RESTful CRUD Example

```js
let users = [];

app.post('/users', (req, res) => {
  const user = req.body;
  users.push(user);
  res.status(201).json({ msg: 'User created', user });
});

app.get('/users', (req, res) => res.json(users));

app.put('/users/:id', (req, res) => {
  const id = req.params.id;
  const updated = req.body;
  users[id] = updated;
  res.json({ msg: 'User updated', updated });
});

app.delete('/users/:id', (req, res) => {
  const id = req.params.id;
  users.splice(id, 1);
  res.json({ msg: 'User deleted' });
});
```

---

## 🔐 3. Middleware in Express

### ✅ What is Middleware?

* Functions that **run before** route handlers.
* Used for **auth**, **validation**, **logging**, etc.

```js
// Custom middleware
const logger = (req, res, next) => {
  console.log(`${req.method} ${req.url}`);
  next(); // Move to next middleware or route
};

app.use(logger);
```

---

## 🌍 4. Working with JSON & Body Data

```js
app.use(express.json());  // parse JSON body
```

```js
app.post('/data', (req, res) => {
  const data = req.body;
  res.json({ received: data });
});
```

---

## 🌐 5. Enable CORS (Cross-Origin)

Install:

```bash
npm install cors
```

Use:

```js
const cors = require('cors');
app.use(cors());  // allow all origins
```

---

## 🗂 6. Split Routes into Files

👉 Clean architecture for large apps:

### `/routes/userRoutes.js`

```js
const express = require('express');
const router = express.Router();

router.get('/', (req, res) => {
  res.send("User route root");
});

module.exports = router;
```

### `index.js`

```js
const userRoutes = require('./routes/userRoutes');
app.use('/users', userRoutes);
```

---

## ✅ 7. Environment Setup with `.env`

```bash
npm install dotenv
```

`.env`:

```
PORT=5000
```

```js
require('dotenv').config();
```

---

## 🧪 8. Error Handling Middleware

```js
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).send('Something broke!');
});
```

---

## 📁 9. Serve Static Files

```js
app.use('/static', express.static('public'));

// GET /static/logo.png → /public/logo.png
```

---

## 🧠 Express.js Interview Questions

| Question                                                   | Sample Answer                                                                        |
| ---------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| What is Express?                                           | Lightweight framework on top of Node.js for building APIs and web servers            |
| What is middleware?                                        | Functions that execute before route handlers, used for logging, auth, error handling |
| What’s the difference between `app.use()` and `app.get()`? | `use()` is for middleware, `get()` is for GET route                                  |
| How do you structure a large Express app?                  | Separate into routes, controllers, middlewares                                       |
| How is error handling done in Express?                     | Using error-handling middleware: `app.use((err, req, res, next) => {})`              |
| What is the role of `next()`?                              | It passes control to the next middleware/handler                                     |

---

## ⚡️ Final Express.js Practice Task

### Build a mini CRUD API:

* `POST /tasks` – add task
* `GET /tasks` – get all
* `PUT /tasks/:id` – update task
* `DELETE /tasks/:id` – delete task

---

## 🚀 Summary Cheatsheet

| Feature           | Summary                               |
| ----------------- | ------------------------------------- |
| Setup             | `npm install express cors dotenv`     |
| Routing           | `app.get()`, `app.post()` etc.        |
| Params            | `req.params`, `req.body`, `req.query` |
| Middleware        | Auth, logging, error handling         |
| CORS              | Allow front-end access                |
| Env               | Store port/API keys securely          |
| Project Structure | Modularize routes/controllers         |

---


# 🧠 MongoDB + Mongoose – From Scratch to Advanced

> 📦 MongoDB is a **NoSQL database** that stores data as **JSON-like documents**.
>
> 🛠️ Mongoose is an **ODM** (Object Data Modeling) library for MongoDB in Node.js that helps you:
>
> * define schemas
> * model relationships
> * validate data
> * interact with MongoDB easily

---

## ✅ 1. Setup MongoDB + Mongoose

### Step 1: Install

```bash
npm install mongoose
```

### Step 2: Connect to MongoDB

If you're using **MongoDB Atlas** (recommended for interviews or real apps):

```js
const mongoose = require('mongoose');
const MONGO_URI = process.env.MONGO_URI;

mongoose.connect(MONGO_URI, {
  useNewUrlParser: true,
  useUnifiedTopology: true
})
.then(() => console.log("MongoDB Connected"))
.catch(err => console.log("DB Connection Error", err));
```

---

## 📄 2. Create a Mongoose Schema & Model

### Example: `Task.js` model

```js
const mongoose = require('mongoose');

const taskSchema = new mongoose.Schema({
  title: {
    type: String,
    required: true,
    trim: true
  },
  completed: {
    type: Boolean,
    default: false
  },
  createdAt: {
    type: Date,
    default: Date.now
  }
});

const Task = mongoose.model('Task', taskSchema);
module.exports = Task;
```

---

## 🛠 3. CRUD Operations with Mongoose

Import the model:

```js
const Task = require('./models/Task');
```

### ➕ Create

```js
app.post('/tasks', async (req, res) => {
  try {
    const newTask = new Task(req.body);
    await newTask.save();
    res.status(201).json(newTask);
  } catch (err) {
    res.status(400).json({ error: err.message });
  }
});
```

### 📥 Read All

```js
app.get('/tasks', async (req, res) => {
  const tasks = await Task.find();
  res.json(tasks);
});
```

### 🖊 Update

```js
app.put('/tasks/:id', async (req, res) => {
  const updated = await Task.findByIdAndUpdate(req.params.id, req.body, { new: true });
  res.json(updated);
});
```

### ❌ Delete

```js
app.delete('/tasks/:id', async (req, res) => {
  await Task.findByIdAndDelete(req.params.id);
  res.json({ msg: "Deleted Successfully" });
});
```

---

## 🧪 4. Advanced Mongoose Features

### ✅ Schema Validation

```js
title: {
  type: String,
  required: [true, "Title is required"],
  minlength: 3
}
```

### 🧬 Data Types

| Type     | Example                                             |
| -------- | --------------------------------------------------- |
| String   | `type: String`                                      |
| Number   | `type: Number`                                      |
| Boolean  | `type: Boolean`                                     |
| Date     | `type: Date`                                        |
| Array    | `type: [String]`                                    |
| ObjectId | `type: mongoose.Schema.Types.ObjectId, ref: 'User'` |

---

### 🔁 Query Helpers

```js
await Task.find({ completed: true });
await Task.findById(taskId);
await Task.deleteMany({ completed: true });
```

---

### 🔐 Pre/Post Hooks (Middleware)

```js
taskSchema.pre('save', function(next) {
  console.log('Saving task...');
  next();
});
```

---

### 🔗 Population (JOINs in MongoDB)

Example for blog with users:

```js
// Blog schema
author: {
  type: mongoose.Schema.Types.ObjectId,
  ref: 'User'
}

// Populate it
Blog.find().populate('author').exec();
```

---

## 🧠 Common MongoDB Interview Questions

| Question                               | Sample Answer                                                 |
| -------------------------------------- | ------------------------------------------------------------- |
| What is MongoDB?                       | NoSQL, document-based database storing data in JSON           |
| Why use Mongoose?                      | Adds schema, validation, easy querying to MongoDB             |
| How to define a schema?                | With `new mongoose.Schema({})`                                |
| Difference: `.find()` vs `.findById()` | `find()` returns multiple docs, `findById()` only one         |
| What is `populate()`?                  | Used to simulate JOINs, fetch related docs via references     |
| What is `lean()` in Mongoose?          | Returns plain JS object instead of Mongoose document (faster) |
| How to create relationship in MongoDB? | With `ObjectId` + `ref` + `populate()`                        |

---

## ⚡ Hands-On Final Task: Build & Connect to MongoDB

> 💡 Build a **Task Manager** API with:

* `/tasks [POST]` – create
* `/tasks [GET]` – list
* `/tasks/:id [PUT]` – update
* `/tasks/:id [DELETE]` – delete
* Connect with MongoDB Atlas
* Add `.env` file and use `process.env.MONGO_URI`

---

## 🧾 Cheat Sheet for Mongoose

| Task          | Code                                 |
| ------------- | ------------------------------------ |
| Connect       | `mongoose.connect(uri)`              |
| Define Schema | `new Schema({})`                     |
| Create Model  | `mongoose.model('Name', schema)`     |
| Save          | `doc.save()`                         |
| Query         | `Model.find({})`, `Model.findById()` |
| Update        | `Model.findByIdAndUpdate()`          |
| Delete        | `Model.findByIdAndDelete()`          |
| Populate      | `Model.find().populate('ref')`       |

---


