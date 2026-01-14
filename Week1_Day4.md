# Week 1 - Day 4: useState & useEffect Deep Dive

## Learning Objectives
- Master advanced useState patterns and optimizations
- Understand useEffect execution order and timing
- Learn to avoid common pitfalls and anti-patterns
- Implement complex state management scenarios
- Handle race conditions and stale closures

## Key Concepts

### 1. Advanced useState Patterns

**Lazy Initialization:**
Use a function to compute expensive initial state only once:
```jsx
const [state, setState] = useState(() => {
  return expensiveComputation();
});
```

**Functional Updates:**
When new state depends on previous state, always use the functional form:
```jsx
setState(prevState => prevState + 1);
```

**State Batching:**
React batches multiple setState calls in event handlers for performance:
```jsx
// These three updates are batched into one re-render
setState1(newValue1);
setState2(newValue2);
setState3(newValue3);
```

### 2. useEffect Advanced Patterns

**Effect Execution Order:**
1. Component renders with current state/props
2. React updates the DOM
3. Browser paints the screen
4. useEffect callbacks run
5. Cleanup functions run before next effect (if dependencies changed)

**Multiple Effects:**
- Effects run in the order they're defined
- Each effect is independent
- Separate concerns into different effects

**Effect Dependencies Deep Dive:**
- Primitives: Compare by value
- Objects/Arrays: Compare by reference (can cause issues)
- Functions: New reference each render (use useCallback)

### 3. Common Pitfalls and Solutions

**Stale Closures:**
```jsx
// ❌ Problem: closure captures old count
const [count, setCount] = useState(0);
useEffect(() => {
  const timer = setInterval(() => {
    setCount(count + 1); // Always uses count from when effect was created
  }, 1000);
  return () => clearInterval(timer);
}, []); // Missing dependency

// ✅ Solution: Functional update
useEffect(() => {
  const timer = setInterval(() => {
    setCount(c => c + 1); // Uses current count
  }, 1000);
  return () => clearInterval(timer);
}, []);
```

**Infinite Loops:**
```jsx
// ❌ Creates infinite loop
const [data, setData] = useState([]);
useEffect(() => {
  setData([...data, newItem]); // data changes, effect runs again
}, [data]);

// ✅ Solution: Remove from dependencies or use functional update
useEffect(() => {
  setData(prev => [...prev, newItem]);
}, []); // Or proper dependencies
```

**Race Conditions:**
```jsx
// ❌ Race condition in async effect
useEffect(() => {
  fetchData(id).then(data => setData(data));
}, [id]); // If id changes quickly, responses may arrive out of order

// ✅ Solution: Use cleanup to ignore stale responses
useEffect(() => {
  let cancelled = false;
  fetchData(id).then(data => {
    if (!cancelled) setData(data);
  });
  return () => { cancelled = true; };
}, [id]);
```

### 4. Performance Considerations

**Avoid Unnecessary Effects:**
- Don't use effects for derived state (calculate during render)
- Don't use effects to handle user events (use event handlers)
- Don't use effects to initialize state (use useState initializer)

**Optimize Expensive Operations:**
- Use lazy initialization for expensive initial state
- Memoize expensive calculations (we'll cover useMemo later)
- Debounce/throttle effects that run frequently

## Code Examples
See [Week1_Day4_Examples.md](./Week1_Day4_Examples.md) for detailed code samples.

## Daily Assignment

### Problem 1: Advanced Counter with History
**Description:**  
Build a counter that maintains a complete history of operations with undo/redo functionality.

**Requirements:**
- Counter with increment/decrement buttons
- History array storing all operations (type, value, timestamp)
- Undo button (goes back one operation)
- Redo button (goes forward one operation)
- Clear history button
- Display current value and position in history
- Disable undo/redo when at boundaries

**Acceptance Criteria:**
- Use multiple useState calls for state organization
- Functional state updates where appropriate
- History should be immutable
- Edge cases handled (empty history, boundaries)
- Performance optimized (no unnecessary re-renders)

### Problem 2: Debounced Search with API Calls
**Description:**  
Create a search component that queries an API with debouncing to reduce unnecessary requests.

**Requirements:**
- Input field for search query
- Debounce API calls by 500ms (only search after user stops typing)
- Show loading indicator during search
- Display results in a list
- Cancel pending requests when query changes
- Handle empty results and errors gracefully
- Use: `https://jsonplaceholder.typicode.com/users?name_like={query}`

**Acceptance Criteria:**
- Implement debouncing with useEffect
- Use AbortController to cancel stale requests
- Loading, error, and success states managed properly
- No search triggered for empty input
- Cleanup functions prevent memory leaks

### Problem 3: Multi-Step Form with Validation
**Description:**  
Build a multi-step registration form with state management across steps and validation.

**Requirements:**
- 3 steps: Personal Info, Account Details, Preferences
- Step 1: First name, last name, email
- Step 2: Username, password, confirm password
- Step 3: Newsletter subscription, theme preference
- Next/Previous buttons (disabled appropriately)
- Validation before allowing next step
- Review screen showing all data before submit
- Persist form data to localStorage as user progresses

**Acceptance Criteria:**
- Single state object for all form data
- Separate state for current step
- Validation logic extracted to helper functions
- localStorage sync with useEffect
- Form data preserved on page refresh
- Submit button only enabled when all steps valid

### Problem 4: Real-Time Data Dashboard
**Description:**  
Create a dashboard that fetches and displays real-time data with auto-refresh capability.

**Requirements:**
- Fetch data from: `https://jsonplaceholder.typicode.com/posts?_limit=5`
- Auto-refresh toggle (on/off)
- Refresh interval selector (5s, 10s, 30s, 60s)
- Manual refresh button
- Display last updated timestamp
- Show loading state during refresh
- Pause auto-refresh when user is viewing details

**Acceptance Criteria:**
- useEffect manages auto-refresh timer
- Cleanup prevents memory leaks
- Interval changes don't cause stale closures
- Loading state doesn't clear previous data
- Proper dependency management

### Problem 5: Synchronized Tabs Component
**Description:**  
Build a tabbed interface that syncs state across multiple browser tabs using localStorage events.

**Requirements:**
- Multiple tabs (Home, Profile, Settings, About)
- Active tab highlighted
- Tab content displayed based on active tab
- Active tab synced across browser tabs/windows
- Use localStorage and storage event
- Smooth transitions between tabs

**Acceptance Criteria:**
- useEffect sets up storage event listener
- Changes in one tab reflect in other tabs immediately
- Cleanup removes event listeners
- Handle case when localStorage is unavailable
- No infinite loops or unnecessary renders

## Best Practices for Senior Developers

1. **State Organization:**
   - Keep related state together in objects
   - Split unrelated state into separate useState calls
   - Consider when to lift state up vs keep local
   - Use lazy initialization for expensive computations

2. **Effect Optimization:**
   - Only include necessary dependencies
   - Use separate effects for independent concerns
   - Extract repeated effect logic into custom hooks
   - Consider if you really need an effect

3. **Avoiding Pitfalls:**
   - Always handle cleanup in effects
   - Use functional updates to avoid stale closures
   - Cancel async operations on cleanup
   - Test components with fast state changes

4. **Debugging:**
   - Use React DevTools to inspect state/props
   - Add console.logs in effects to understand execution
   - Check dependency array warnings from ESLint
   - Test edge cases: fast clicks, quick navigation, etc.

5. **Performance:**
   - Don't optimize prematurely
   - Profile before optimizing
   - Understand when React batches updates
   - Consider useCallback/useMemo for expensive operations

## Common Patterns

### Derived State (Don't Use Effect)
```jsx
// ❌ Don't do this
const [fullName, setFullName] = useState('');
useEffect(() => {
  setFullName(`${firstName} ${lastName}`);
}, [firstName, lastName]);

// ✅ Do this - calculate during render
const fullName = `${firstName} ${lastName}`;
```

### Debouncing with useEffect
```jsx
useEffect(() => {
  const timeoutId = setTimeout(() => {
    performSearch(query);
  }, 500);
  return () => clearTimeout(timeoutId);
}, [query]);
```

### Managing Multiple Related States
```jsx
// Consider using a single object
const [formData, setFormData] = useState({
  name: '',
  email: '',
  phone: ''
});

// Update specific field
setFormData(prev => ({ ...prev, name: newName }));
```

## Additional Resources
- [useState Deep Dive](https://react.dev/reference/react/useState)
- [useEffect Deep Dive](https://react.dev/reference/react/useEffect)
- [Rules of Hooks](https://react.dev/warnings/invalid-hook-call-warning)
- [React Hooks FAQ](https://react.dev/reference/react)

## Next Steps
Tomorrow, we'll explore Forms and Controlled Components in detail, including form libraries and advanced form patterns.
