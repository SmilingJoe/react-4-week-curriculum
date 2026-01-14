# Week 1 - Day 3: Component Lifecycle & Introduction to Hooks

## Learning Objectives
- Understand React component lifecycle in functional components
- Master the useEffect hook for side effects
- Learn cleanup patterns and dependency arrays
- Compare with Vue lifecycle hooks
- Handle async operations in components

## Key Concepts

### 1. Component Lifecycle in React
Unlike class components with explicit lifecycle methods, functional components use the `useEffect` hook to handle lifecycle events.

**Lifecycle Phases:**
1. **Mounting**: Component is created and inserted into the DOM
2. **Updating**: Component re-renders due to state or prop changes
3. **Unmounting**: Component is removed from the DOM

**Comparison with Vue:**
- **React `useEffect(() => {}, [])`** ≈ **Vue `onMounted()`**
- **React `useEffect(() => {})`** ≈ **Vue `onUpdated()` + onMounted()`**
- **React `useEffect(() => { return cleanup }, [])`** ≈ **Vue `onUnmounted()`**

### 2. useEffect Hook
The useEffect hook lets you perform side effects in functional components.

**Syntax:**
```jsx
useEffect(() => {
  // Side effect code
  return () => {
    // Cleanup code (optional)
  };
}, [dependencies]);
```

**Dependency Array Behaviors:**
- **No array**: Runs after every render
- **Empty array `[]`**: Runs once on mount (like componentDidMount)
- **With dependencies `[a, b]`**: Runs when a or b changes

### 3. Common Side Effects
- Data fetching
- Setting up subscriptions
- Manually changing the DOM
- Timers and intervals
- Event listeners
- Local storage operations

### 4. Cleanup Functions
Always clean up side effects to prevent memory leaks:
- Clear timers/intervals
- Unsubscribe from subscriptions
- Remove event listeners
- Cancel ongoing requests

## Code Examples
See [Week1_Day3_Examples.md](./Week1_Day3_Examples.md) for detailed code samples.

## Daily Assignment

### Problem 1: Document Title Updater
**Description:**  
Create a component that updates the document title based on component state and cleans up on unmount.

**Requirements:**
- State for page name and notification count
- Update document title to show: `(count) Page Name | My App`
- Reset title to default when component unmounts
- Buttons to change page name and increment notifications

**Acceptance Criteria:**
- Use useEffect with proper dependencies
- Implement cleanup function
- Title updates immediately when state changes
- Title resets when component unmounts

### Problem 2: Real-Time Clock Component
**Description:**  
Build a clock that shows the current time and updates every second.

**Requirements:**
- Display current time (hours:minutes:seconds)
- Update every second using setInterval
- Start/stop functionality
- Clean up interval on unmount or when stopped
- Show elapsed time since component mounted

**Acceptance Criteria:**
- Use useEffect to set up and clean up interval
- Proper cleanup to prevent memory leaks
- State management for time and isRunning
- Format time properly (leading zeros)

### Problem 3: Window Resize Tracker
**Description:**  
Create a component that tracks and displays window dimensions, updating in real-time.

**Requirements:**
- Display current window width and height
- Update dimensions when window is resized
- Show device category (mobile < 768px, tablet < 1024px, desktop)
- Debounce resize events (update max once per 200ms)
- Clean up event listener on unmount

**Acceptance Criteria:**
- Use useEffect to add/remove resize listener
- Implement debouncing for performance
- Proper cleanup of event listeners
- Responsive display of device category

### Problem 4: Data Fetcher with Loading States
**Description:**  
Build a component that fetches user data from an API and handles loading/error states.

**Requirements:**
- Fetch data from: `https://jsonplaceholder.typicode.com/users/{id}`
- Input to change user ID (1-10)
- Show loading spinner while fetching
- Display user data when loaded
- Show error message if fetch fails
- Cancel ongoing requests if user ID changes

**Acceptance Criteria:**
- Use useEffect with user ID as dependency
- Implement loading, error, and success states
- Handle cleanup for cancelled requests
- Proper error handling and display

### Problem 5: Local Storage Sync
**Description:**  
Create a settings component that persists user preferences to localStorage.

**Requirements:**
- Settings: theme (light/dark), language, notifications (on/off)
- Load initial values from localStorage
- Update localStorage whenever settings change
- Provide reset to defaults button
- Show "Settings saved!" message briefly after changes

**Acceptance Criteria:**
- Use useEffect to sync with localStorage
- Load from localStorage on mount
- Save to localStorage on every change
- Handle case when localStorage is not available
- Clear localStorage on reset

## Best Practices for Senior Developers

1. **useEffect Dependencies:**
   - Always specify dependencies accurately
   - Use ESLint plugin `eslint-plugin-react-hooks` for warnings
   - Don't lie about dependencies to skip effects

2. **Cleanup Functions:**
   - Always clean up subscriptions, timers, and listeners
   - Test cleanup by mounting/unmounting components
   - Use AbortController for fetch request cancellation

3. **Effect Organization:**
   - Separate effects by concern (one effect per purpose)
   - Keep effects focused and simple
   - Consider custom hooks for reusable effects

4. **Performance:**
   - Avoid effects that run on every render when unnecessary
   - Use empty dependency array for mount-only effects
   - Be careful with object/array dependencies (consider useCallback/useMemo)

5. **Async in useEffect:**
   - Can't make useEffect callback async directly
   - Create async function inside and call it
   - Handle cleanup for async operations

## Common Patterns

### Fetch Data on Mount
```jsx
useEffect(() => {
  fetchData();
}, []); // Empty array = mount only
```

### Subscribe to External Data
```jsx
useEffect(() => {
  const subscription = someAPI.subscribe();
  return () => subscription.unsubscribe();
}, []);
```

### Run Effect When Specific Value Changes
```jsx
useEffect(() => {
  doSomething(value);
}, [value]); // Re-run when value changes
```

### Cleanup Previous Effect
```jsx
useEffect(() => {
  const timer = setTimeout(() => {
    doSomething();
  }, 1000);
  
  return () => clearTimeout(timer); // Cleanup
}, [dependency]);
```

## Additional Resources
- [useEffect Hook Reference](https://react.dev/reference/react/useEffect)
- [Synchronizing with Effects](https://react.dev/learn/synchronizing-with-effects)
- [You Might Not Need an Effect](https://react.dev/learn/you-might-not-need-an-effect)
- [Lifecycle Diagram](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)

## Next Steps
Tomorrow, we'll take a deeper dive into useState and useEffect with advanced patterns, including optimization techniques and common pitfalls to avoid.
