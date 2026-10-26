# Basic of React and Hook's
-----------------------------------------------------------------------------------------------------------------------------------------------------------------

### How react how?
- Whenever state or props change, React re-renders the component, compares the previous and next UI representations through reconciliation, and applies the necessary changes to the real DOM. The browser then displays the updated UI.

The simple workflow is:
1. State or props change → React schedules a re-render.
2. Render → React runs the component again to determine the next UI output.
3. Reconciliation → React compares the previous and next UI representations to determine what needs to change.
4. DOM update (commit) → React DOM applies the necessary changes to the real DOM.
5. Browser displays the update → The browser paints the updated UI when needed.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------


### Props vs State:
- Basically props is used to passing data from one component to other where there is one parent component and we pass data to it's child component 
where State is used to store data that can change over time, mean that store a mutable data


- Props: Props are used to pass data from a parent component to a child component. The receiving component treats props as read-only.
- State: State stores data that can change over time and affect the UI. In React, we update state using its setter function, such as setCount(), rather than modifying it directly.

```
import { useState } from "react";

function Child({ name }) {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h2>Hello, {name}</h2>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        Increase
      </button>
    </div>
  );
}

function App() {
  return <Child name="Jayesh" />;
}

export default App;
```

- name is a prop passed from App to Child
- count is state managed inside Child
- Clicking the button updates count and triggers a re-render

-> `Props are used to pass data between components, while state manages data that changes over time and affects the UI.`
-----------------------------------------------------------------------------------------------------------------------------------------------------------------

#### lazy loading: loading a component or resource only when it needed, instead of loading everything at the beginning.

- Reduces the amount of code or resources needed initially.
- Can improve initial loading speed.
- Can reduce unnecessary network usage by not downloading resources the user never accesses.

-> Pinterest example: Images can be loaded as the user scrolls, rather than downloading every image on the page at once. This is commonly called lazy loading images.


```
laxy loading mean: loading or rendering the compoenet in ui interface when it needed instead of loading everything at the beginning 
- it effective for user, bcz if more data heavy component then first it's useage user internet data and take time
- most common example pinterest where it have millions of pics and short video
- if al once loading then user data gone + very slow and not afficient also
```

----------------------------------------------------------------------------------------------------

---
### Props drilling: Passing props through multiple layers of components

- **Props drilling** means passing props through multiple layers of components, even when some middle components do not need that data.
- It happens when a parent passes data to a child, and that child passes the same data to another child.
- **Solution:** Context API can help when many components need the same shared data.

---

----------------------------------------------------------------------------------------------------
### useState: useState is a React Hook that allows functional components to manage and persist state variables that change over time, such as user input or UI toggles. 

CODE: 
```
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h1>Count: {count}</h1>

      <button onClick={() => setCount(count + 1)}>
        Increase
      </button>
    </div>
  );
}

export default Counter;
```

@Important: setCount doesn't immediately change the count variable inside the current render. It requests a state update for the next render.


-> updating objects and arrays

```
const [products, setProducts] = useState([
  { id: 1, name: "Keyboard" },
  { id: 2, name: "Mouse" }
]);

// to add product

setProducts(previousProducts => [
  ...previousProducts,
  { id: 3, name: "Monitor" }
]);
```

----------------------------------------------------------------------------------------------------

### useEffect: 
- react hook that let functional component to perform side effect after rendering/(mounting), such as fetching data, updating the DOM and etc
- it's take 2 argument:
	1. function: what effect u want to perform | side-effect logic
	2. dependency array(optional): which control the side-effect

@SYNTAX:
```
useEffect(() => {
  // runs ONCE on mount
  return () => {
    // runs ONCE on unmount
  };
}, []);   
```

ex:

```
useEffect(() => {
	console.log('rendering')
}, []) // empty array: 
``` 

// API that returns posts
```
import { useEffect, useState } from "react";

function Products() {
  const [products, setProducts] = useState([]);

  useEffect(() => {
    fetch("https://jsonplaceholder.typicode.com/posts")
      .then(res => res.json())
      .then(data => setProducts(data));
  }, []);

  return (
    <div>
      <h2>Products</h2>

      {products.map(product => (
        <p key={product.id}>{product.title}</p>
      ))}
    </div>
  );
}

export default Products;
```

- useEffect runs its setup after React commits the initial render.
- fetch() requests data from the API.
- res.json() converts the response into JavaScript data.
- setProducts(data) saves the response in state.
- [] means the Effect has no reactive dependencies.


NOTE:

| Code | When it runs |
|---|---|
| `useEffect(() => { ... })` | After every committed render |
| `useEffect(() => { ... }, [])` | After the initial mount |
| `useEffect(() => { ... }, [userId])` | After the initial mount and when `userId` changes |

```
import { useState, useEffect } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  // logs every time count changes
  useEffect(() => {
    console.log(`Count is now: ${count}`);
  }, [count]);

  return (
    <>
      <button onClick={() => setCount(count - 1)}>−</button>
      <span>{count}</span>
      <button onClick={() => setCount(count + 1)}>+</button>
      <button onClick={() => setCount(0)}>Reset</button>
    </>
  );
}   
```

Mount (initial render)	Count is now: 0
Click +			Count is now: 1
Click + again		Count is now: 2
Click −			Count is now: 1
Click Reset		Count is now: 0

------------------------------------------------------------------------------------------------------------------------------------------------

### useContext:
-> it allow  component to read shared data without passing the props at every level 
-> read a value from a context with out prop drilling:
-> it return the object with one parameter of current value:

- useContext does NOT take a function. It takes a Context object (created by createContext) & return it's value in useContext 
```
const UserContext = createContext({ name: "default", age: 0 });
... other code
const user = useContext(UserContext); // returns the value (or default if no Provider)
```
so props drilling is gone where we have to pass props to every level to end if end component want value from first parent component or provider component 


@ Note: In useContext, the current value means the value you pass to the context Provider through its value prop.

EX:

```

import Component2 from "./Component2";

function App() {
  return (
    <div>
      <h1>Prop Drilling Demo</h1>
      <Component2 name="Rahul" age={25} />
    </div>
  );
}

export default App;   

-------------

import Component3 from "./Component3";

function Component2({ name, age }) {
  return (
    <div>
      <p>Component2 received: {name}, {age}</p>
      {/* passing props further down */}
      <Component3 name={name} age={age} />
    </div>
  );
}

export default Component2;   

-------------

import { useState, useEffect } from "react";

function Component3({ name, age }) {
  const [likes, setLikes] = useState(0);

  useEffect(() => {
    console.log(`${name} (${age}) has ${likes} likes`);
  }, [likes]);

  return (
    <div>
      <p>Component3 received: {name}, {age}</p>
      <button onClick={() => setLikes(likes + 1)}>
        Like ({likes})
      </button>
    </div>
  );
}

export default Component3;   

```

```folder structure flow:
App (File 1)
 └── passes name, age
      Component2 (File 2)
       └── receives + re-passes name, age
            Component3 (File 3)
             └── receives props + uses useState + useEffect   
```


----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### useRef: 
- it let component to store value(mutable) without causing a re-render when that value changes.
- commonly use when Accessing DOM elements directly, such as focusing an input & Remembering values that should not trigger a UI update, such as a timer ID.

what it solve?
- Suppose you have a search input and want to automatically focus it when the user clicks a button.
- You could try to access the DOM manually, but React provides a cleaner way using useRef.


EX: 
```
import React from 'react';
import { useState, useRef, useEffect } from 'react'

function App() {
  // const [count, setCount] = useState(0);

  // console.log("RE-RENDERED! count =", count); // ← fires EVERY click

  // return (
  //   <button onClick={() => setCount(count + 1)}>
  //     Count: {count}
  //   </button>
  // );

  const count = useRef(0); // ← mutable, no re-render

  console.log("RE-RENDERED!"); // ← fires ONLY on mount

  return (
    <div>
    <button onClick={() => { count.current += 1; }}>
      Clicked {count.current} times
    </button>
    <button onClick={() => { console.log(count) }}>
      print
    </button>
    </div>
  );
}   

export default App

```
OUTPUT: 
```
RE-RENDERED!
{current: 12}
{current: 18}
{current: 20}
```
- NOTE here 'The button text won't update visually because React doesn't know to re-render'

----------------------
@ NOTE:
- useState → stores a value and triggers a re-render when its setter updates the state.
- useRef → stores a mutable value that persists between renders, but changing .current does not trigger a re-render.
----------------------



-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### useMemo: that caches the result of calculation so it only re-render when the dependencies changes.
- It caches the result of a calculation to avoid unnecessary recalculations.
- Commonly used for expensive calculations, such as filtering or sorting large lists.

Syntax:
const result = useMemo(() => calculateValue(), [dependencies]);

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### useCallback: that caches the functions
- It caches a function reference between renders until its dependencies change.
- Commonly used when passing functions to memoized child components to avoid unnecessary re-renders.

Syntax:
const handleClick = useCallback(() => {
  // logic
}, [dependencies]);

----------------------------------------------------
Note:
- useMemo → caches a value.
- useCallback → caches a function.

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Closure (JS Concpet):
- A **closure** happens when a function remembers variables from the scope where it was created, even after that outer function has finished running.
- In simple words, a function can still access the variables around it.

EX: 
```js
function outer() {
  let count = 0;

  return function inner() {
    count++;
    console.log(count);
  };
}

const counter = outer();

counter(); // 1
counter(); // 2
```

- `inner()` remembers the `count` variable from `outer()`.
- This is a closure.


---

### Higher-Order Component (HOC)

- A higher-order function in JavaScript is a function that takes one or more functions as arguments or returns a function as its result.
- In React we have Function Component. So, functions that take a component as an argument and return a new.
- It give Reusability(allow developers to reuse component logic across multiple components without duplicating code) 
  
Simple example:

```jsx
function withMessage(Component) {
  return function EnhancedComponent() {
    return <Component message="Hello!" />;
  };
}
```

Usage:

```jsx
function Greeting({ message }) {
  return <h1>{message}</h1>;
}

const GreetingWithMessage = withMessage(Greeting);
```

- `withMessage` receives a component and returns a new component.
- The returned component passes an extra prop called `message`.

---

### Component Lifecycle

- in React refers to the series of phases a component goes through from creation to destruction.

> There are three main phases:
1. Mounting: The component is created and inserted into the DOM for the first time. 
2. Updating: Triggered whenever props or state change, causing a re-render. Can happen multiple times.
3. Unmounting: The component is removed from the DOM.

- In function components, Hooks such as `useEffect` are commonly used to synchronize with external systems and clean them up.
- Class components have lifecycle methods such as `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount`.

`Key takeaway: Mounting happens once, updating can happen many times, and unmounting happens once when the component is removed. In functional components, useEffect with its dependency array and cleanup function covers all three phases in a single API.`

---

#### Custom Hook
-  It's a JavaScript function whose name starts with use that reuses stateful logic across multiple components.  It composes built-in hooks (useState, useEffect, etc.) and returns whatever the consuming component needs.

> Rules
1. Name must start with use + capital letter (e.g., useFetch, useCounter)
2. Only call at the top level of a component or another hook — never inside conditions, loops, or callbacks
3. Only call from function components or other custom hooks 

How to Create One
Step 1 — Create a file (e.g., useFetch.js):
```
import { useState, useEffect } from "react";

export function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    if (!url) return;
    const controller = new AbortController();

    async function loadData() {
      setLoading(true);
      setError(null);
      try {
        const res = await fetch(url, { signal: controller.signal });
        if (!res.ok) throw new Error(`Request failed: ${res.status}`);
        setData(await res.json());
      } catch (err) {
        if (err.name !== "AbortError") setError(err);
      } finally {
        setLoading(false);
      }
    }

    loadData();
    return () => controller.abort();
  }, [url]);

  return { data, loading, error };
}
```

Step 2 — Use it in any component:
```
function UserProfile({ userId }) {
  const { data, loading, error } = useFetch(`/api/users/${userId}`);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;
  return <h1>{data.name}</h1>;
}
```
> When to Use
- Logic is reused across 2+ components
- A single component's logic is getting too long
- You want to isolate side effects (subscriptions, timers, API calls) for testability

`This is the modern replacement for Higher-Order Components — instead of wrapping a component, you call a hook inside it.`

---

### Virtual DOM
- in-memory representation of what the UI should look like.

React itself creates the Virtual DOM. When a component renders, React calls the component function, collects the returned JSX (compiled to React.createElement() calls), and assembles a tree of plain JavaScript objects in memory.  Each node is a lightweight object like:
```
{ type: "div", props: { className: "app" }, children: [...] }
```
This happens on every render — both initial mount and every re-render triggered by state/props changes.

### Is It Destroyed After the Real DOM Update?
No — the current Virtual DOM persists in memory. 

``` life cycle
State change
     │
     ▼
New Virtual DOM tree is created (in memory)
     │
     ▼
React diffs: New VDOM vs Old VDOM (the previous snapshot)
     │
     ▼
Minimal changes applied to Real DOM
     │
     ▼
New VDOM becomes the "current" snapshot (stored in memory)
Old VDOM is no longer referenced → garbage collected
```

NOTE THAT:
`So the Virtual DOM is not a one-time thing that gets thrown away after painting.  It lives persistently in memory (internally represented by React's Fiber tree) and is replaced on each re-render — the old version is what gets garbage collected, not the new one.`

---

### SSR vs CSR
CSR: Browser builds the page. 
SSR: Server builds the page. 

```
CSR:  Browser → JavaScript → Data → UI
SSR:  Server → Data → HTML → Browser → Hydration
```

In CSR:
```
Browser requests page
       │
       ▼
Server sends: <div id="root"></div> + big JS bundle
       │
       ▼
Browser downloads & executes JS
       │
       ▼
JS fetches data from API
       │
       ▼
JS builds DOM → User sees content
```

Best for: Dashboards, admin panels, logged-in apps, SPAs where SEO doesn't matter.


In SSR: 
```
Browser requests page
       │
       ▼
Server runs React code → generates full HTML
       │
       ▼
Server sends: <div id="root"><h1>Actual Content</h1>...</div> + JS bundle
       │
       ▼
Browser shows content IMMEDIATELY
       │
       ▼
JS downloads → "Hydration" (attaches event listeners)
       │
       ▼
Page becomes interactive
```

Best
for: E-commerce, blogs, landing pages, job portals — anything where SEO + fast first paint matter. 

### What is "Hydration"?
After SSR sends the HTML, the browser still needs to make it interactive.  Hydration is the process where React:

1. Downloads the JS bundle
2. Walks the existing DOM
3. Attaches event listeners, initializes state
4. Takes over rendering for future updates

That's why SSR pages feel "alive" after a brief moment — the HTML was there first, then JS "woke it up."

`RSC lets you mark specific components as "use server" — they never ship JS to the client.  They're rendered on the server and streamed as a serialized payload. This is the modern direction in Next.js 13/14/15, complementing (not replacing) SSR and CSR.`


---

### Routing and React Router

- **Routing** means deciding which page or UI to show based on the URL.
- **React Router** is a library that lets React applications handle navigation between routes without requiring a full page reload for every navigation.

Example:

```jsx
import { BrowserRouter, Routes, Route, Link } from "react-router-dom";

function Home() {
  return <h1>Home Page</h1>;
}

function About() {
  return <h1>About Page</h1>;
}

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

- `/` displays the Home component.
- `/about` displays the About component.
- `Link` lets users navigate between routes.


---

### React Testing Library and Unit Testing

- **Testing** checks whether code behaves as expected.
- **Unit testing** checks a small piece of code or behavior in isolation.
- **React Testing Library (RTL)** helps test React components by interacting with them in ways similar to how users do.

Example test:

```jsx
import { render, screen } from "@testing-library/react";
import { expect, test } from "vitest";
import App from "./App";

test("shows the count button", () => {
  render(<App />);
  expect(screen.getByRole("button")).toBeInTheDocument();
});
```

- `render()` renders the component for testing.
- `screen.getByRole()` finds an element by its accessible role.
- `toBeInTheDocument()` checks that the element exists in the rendered UI.
- This example uses Vitest and assumes the appropriate testing setup and `jest-dom` matchers are configured.


---

### What Is a Bundler?

- A **bundler** takes your app's modules and related resources and combines or organizes them into files that can be served to the browser.
- It can also transform code, handle assets, and optimize files for production.
- Examples include **Vite** (which uses different tools for development and production builds), **Webpack**, and **esbuild**.

In simple words, a bundler prepares your application's code and resources so the browser can load them efficiently.


---

































