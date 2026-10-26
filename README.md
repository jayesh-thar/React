### Basic of React and Hook's
-----------------------------------------------------------------------------------------------------------------------------------------------------------------

# How react how?
- Whenever state or props change, React re-renders the component, compares the previous and next UI representations through reconciliation, and applies the necessary changes to the real DOM. The browser then displays the updated UI.

The simple workflow is:
1. State or props change → React schedules a re-render.
2. Render → React runs the component again to determine the next UI output.
3. Reconciliation → React compares the previous and next UI representations to determine what needs to change.
4. DOM update (commit) → React DOM applies the necessary changes to the real DOM.
5. Browser displays the update → The browser paints the updated UI when needed.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------


# Props vs State:
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

# lazy loading: loading a component or resource only when it needed, instead of loading everything at the beginning.

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
# useState:
useState is a React Hook that allows functional components to manage and persist state variables that change over time, such as user input or UI toggles. 

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

# useEffect: 
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

	@ Code:					@ When it runs:
useEffect(() => { ... })		After every committed render
useEffect(() => { ... }, [])		After the initial mount
useEffect(() => { ... }, [userId])	After the initial mount and when userId changes

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

# useContext:
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

# useRef: 
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

# useMemo: that caches the result of calculation so it only re-render when the dependencies changes.
- It caches the result of a calculation to avoid unnecessary recalculations.
- Commonly used for expensive calculations, such as filtering or sorting large lists.

Syntax:
const result = useMemo(() => calculateValue(), [dependencies]);

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

useCallback
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


















































































































