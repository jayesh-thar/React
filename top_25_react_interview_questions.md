# Top 25 React Interview Questions

Simple answers, small examples, and key words for revision.

## 1. What is React?
React is a JavaScript library for building user interfaces with reusable components.

```jsx
function Welcome() {
  return <h1>Hello!</h1>;
}
```
**Component:** A reusable part of the UI.

## 2. How does React work internally?
When state or props change, React renders the component again, compares the previous and next UI output, and updates the necessary parts of the real DOM.

**Flow:** State/props change → Render → Reconciliation → DOM update → Browser displays UI.  
**Reconciliation:** Working out what changed between the previous and next UI output.

## 3. Props vs state?
- **Props:** Data passed from a parent component to a child. The child treats props as read-only.
- **State:** Data managed by a component that can change and update the UI.

```jsx
function Greeting({ name }) {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(c => c + 1)}>
    Hello {name}: {count}
  </button>;
}
```

## 4. What is `useState`?
`useState` stores changing data in a function component. Its setter requests a re-render.

```jsx
const [count, setCount] = useState(0);
<button onClick={() => setCount(c => c + 1)}>{count}</button>
```
**Setter:** The function that requests a state update, e.g. `setCount`.

## 5. What is `useEffect`?
`useEffect` synchronizes a component with external systems, such as APIs, timers, or event listeners.

```jsx
useEffect(() => {
  console.log("Mounted");
}, []);
```

| Code | When it runs |
|---|---|
| `useEffect(() => { ... })` | After every committed render |
| `useEffect(() => { ... }, [])` | After initial mount (Strict Mode may run an extra setup/cleanup cycle in development) |
| `useEffect(() => { ... }, [userId])` | After mount and when `userId` changes |

**Cleanup:** A returned function that removes listeners, clears timers, or stops work.

## 6. What are prop drilling and `useContext`?
**Prop drilling** means passing props through several component layers, even when middle components do not need them. `useContext` lets a component read shared data without passing props through every layer.

```jsx
const theme = useContext(ThemeContext);
```
The value comes from the nearest matching Provider, or the context default if no Provider exists.

## 7. What is `useRef`?
`useRef` stores a value between renders without causing a re-render when `.current` changes. It can also reference a DOM element.

```jsx
const inputRef = useRef(null);
<input ref={inputRef} />
<button onClick={() => inputRef.current.focus()}>Focus</button>
```
**Remember:** `useState` updates the UI; changing `useRef.current` alone does not.

## 8. `useMemo` vs `useCallback`?
- `useMemo` caches a calculated value.
- `useCallback` caches a function reference.

```jsx
const total = useMemo(() => calculateTotal(items), [items]);
const handleClick = useCallback(() => console.log("Clicked"), []);
```
Use them when they help performance, not for everything.

## 9. What is a re-render?
A re-render means React runs a component again to work out what its UI should look like. State changes, context changes, or a parent render can cause it. A re-render does not mean the whole real DOM is rebuilt.

## 10. How can you avoid unnecessary re-renders?
Keep state close to where it is needed. Use `React.memo` for suitable child components, `useMemo` for expensive calculations, and `useCallback` when a stable function reference helps.

```jsx
const Child = React.memo(function Child({ name }) {
  return <p>Hello, {name}</p>;
});
```
**Optimization:** Improving performance where needed.

## 11. What is the Virtual DOM? Does it get destroyed after an update?
The Virtual DOM is an in-memory representation of the UI. React compares previous and next UI output and updates the necessary parts of the real DOM. React does not destroy the entire Virtual DOM after each update; it keeps current UI information for future updates. Internally, React uses its Fiber architecture.

## 12. What are keys in React?
Keys help React identify list items that changed, were added, or were removed.

```jsx
{users.map(user => <p key={user.id}>{user.name}</p>)}
```
Use stable IDs when possible. Avoid array indexes for lists that can change order.

## 13. What is conditional rendering?
Showing different UI depending on a condition.

```jsx
{isLoggedIn ? <p>Welcome!</p> : <p>Please log in.</p>}
```

## 14. What is a controlled component?
A controlled input gets its value from React state, and an event handler updates that state.

```jsx
const [name, setName] = useState("");
<input value={name} onChange={e => setName(e.target.value)} />
```

## 15. What is lazy loading?
Lazy loading means loading a component or resource only when needed, which can reduce initial downloads.

```jsx
const Dashboard = lazy(() => import("./Dashboard"));

<Suspense fallback={<p>Loading...</p>}>
  <Dashboard />
</Suspense>
```
**Suspense:** Shows fallback UI while a lazy component loads.

## 16. SSR vs CSR?
- **CSR (Client-Side Rendering):** The browser runs JavaScript to build the UI.
- **SSR (Server-Side Rendering):** The server generates HTML and sends it to the browser.
CSR is common for dashboards. SSR can show content sooner and help search engines access page content. Modern frameworks can combine both.
**Hydration:** React makes server-rendered HTML interactive in the browser.

## 17. What is routing and React Router?
Routing decides which UI to show based on the URL. React Router helps navigate between routes without a full page reload for each navigation.

```jsx
<Routes>
  <Route path="/" element={<Home />} />
  <Route path="/about" element={<About />} />
</Routes>
```

## 18. What is a custom Hook?
A custom Hook is a function starting with `use` that reuses logic using React Hooks.

**Steps:** Find reusable logic → create a `use...` function → use built-in Hooks → return values/functions → call it at the top level of a component or another Hook.

```jsx
function useCounter() {
  const [count, setCount] = useState(0);
  return { count, increment: () => setCount(c => c + 1) };
}
```
Each component calling it gets its own state unless state is shared separately.

## 19. What is a Higher-Order Component (HOC)?
An HOC is a function that takes a component and returns a new component with extra behavior.

```jsx
function withMessage(Component) {
  return function Enhanced() {
    return <Component message="Hello!" />;
  };
}
```
It is a pattern for reusing component logic.

## 20. What is a closure in JavaScript?
A closure happens when a function remembers variables from where it was created, even after the outer function finishes.

```js
function outer() {
  let count = 0;
  return () => ++count;
}
const counter = outer();
counter(); // 1
counter(); // 2
```
The returned function remembers `count`. A closure and an HOC are not the same concept.

## 21. What is the component lifecycle?
1. **Mounting:** Component first appears in the UI.
2. **Updating:** Component renders again.
3. **Unmounting:** Component is removed.
Function components often use `useEffect` and cleanup to synchronize with external systems. Class components have methods like `componentDidMount` and `componentWillUnmount`.

## 22. How do you fetch API data in React?
Use `fetch` or Axios, store the result in state, and handle loading/errors.

```jsx
const [users, setUsers] = useState([]);

useEffect(() => {
  fetch("/api/users")
    .then(res => {
      if (!res.ok) throw new Error("Request failed");
      return res.json();
    })
    .then(setUsers)
    .catch(console.error);
}, []);
```
In a real app, also show loading/error UI and consider cancelling requests.

## 23. What are React Testing Library and unit testing?
**Unit testing** checks a small piece of code or behavior. **React Testing Library (RTL)** helps test components in ways similar to user interaction.

```jsx
render(<App />);
expect(screen.getByRole("button")).toBeInTheDocument();
```
`render` mounts the component for the test; `getByRole` finds an accessible element. This example assumes the test setup includes the needed matchers.

## 24. What is a bundler?
A bundler prepares application modules and resources for the browser. It can combine files, transform code, handle assets, and optimize production builds. Examples: **Vite**, **Webpack**, and **esbuild**.

## 25. What is React Strict Mode?
Strict Mode helps find potential problems during development. It may run rendering and Effect setup/cleanup extra times to expose unsafe logic.
- This extra checking is development-only.
- Do not remove Strict Mode just to hide duplicate logs.
- Make Effects safe to repeat and clean up timers/listeners properly.

---

## Quick Revision

| Topic | Remember |
|---|---|
| Props | Data passed to a component |
| State | Changing data that can update UI |
| `useEffect` | Synchronizes with external systems |
| `useContext` | Reads shared context |
| `useRef` | Stores value without triggering render |
| `useMemo` | Caches a value |
| `useCallback` | Caches a function reference |
| Keys | Help React identify list items |
| Lazy loading | Loads resources when needed |
| Custom Hook | Reuses logic using Hooks |
| HOC | Takes a component and returns a component |
| Routing | Chooses UI based on URL |
| RTL | Tests UI from the user's point of view |
| Bundler | Prepares code/resources for the browser |
