# Week 1 - Day 2: Props, State, and Event Handling

## Learning Objectives
- Master props passing and prop types validation
- Understand state management with useState hook
- Handle events in React (comparison with Vue event handling)
- Learn controlled vs uncontrolled components
- Implement two-way data binding patterns

## Key Concepts

### 1. Props Deep Dive
Props (properties) are read-only inputs to components, flowing from parent to child.

**Key Points:**
- Props are immutable within the receiving component
- Can be any JavaScript value: primitives, objects, arrays, functions
- Destructuring props improves code readability
- Default props can be set using default parameters

**Comparison with Vue:**
- **React**: Props passed as JSX attributes, received as function parameters
- **Vue**: Props defined with `defineProps()`, accessed via `props.propName`
- **React**: No built-in props validation (use PropTypes or TypeScript)
- **Vue**: Built-in prop validation with types and validators

### 2. State with useState Hook
State is mutable data that belongs to a component. When state changes, React re-renders the component.

**useState Hook:**
```jsx
const [stateValue, setStateValue] = useState(initialValue);
```

**Comparison with Vue:**
- **React**: `const [count, setCount] = useState(0)`
- **Vue**: `const count = ref(0)` or `const state = reactive({ count: 0 })`
- **React**: State updates are asynchronous and batched
- **Vue**: Reactivity is automatic through Proxy

### 3. Event Handling
React events use camelCase and pass functions as handlers.

**Key Differences from Vue:**
- **React**: `onClick={handleClick}` (function reference)
- **Vue**: `@click="handleClick"` or `@click="handleClick()"`
- **React**: Use `event.preventDefault()` explicitly
- **Vue**: Can use `.prevent` modifier
- **React**: Events are synthetic (cross-browser wrapper)
- **Vue**: Can use native events with `.native` modifier

### 4. Controlled Components
In React, form inputs are typically "controlled" - their value is driven by React state.

**Pattern:**
```jsx
const [value, setValue] = useState('');
<input value={value} onChange={(e) => setValue(e.target.value)} />
```

This is similar to `v-model` in Vue but more explicit.

## Code Examples
See [Week1_Day2_Examples.md](./Week1_Day2_Examples.md) for detailed code samples.

## Daily Assignment

### Problem 1: Interactive Counter with Multiple Operations
**Description:**  
Build an enhanced counter component that demonstrates state management and event handling.

**Requirements:**
- Initialize counter at 0
- Buttons for: increment, decrement, increment by 5, reset
- Display current count
- Display count history (last 5 operations)
- Disable decrement when count is 0
- Highlight count in red when negative, green when > 10

**Acceptance Criteria:**
- Use useState for count and history
- Implement proper event handlers
- Conditional styling based on count value
- Buttons should have appropriate disabled states
- History should show operation type and resulting value

### Problem 2: Real-Time Form Validator
**Description:**  
Create a registration form with real-time validation feedback.

**Requirements:**
- Fields: username, email, password, confirm password
- Real-time validation:
  - Username: min 3 characters, max 20, alphanumeric only
  - Email: valid email format
  - Password: min 8 characters, must include number and special char
  - Confirm Password: must match password
- Show validation errors below each field
- Submit button disabled until form is valid
- Clear form after successful submission

**Acceptance Criteria:**
- Use controlled components for all inputs
- Separate state for each field and its validation error
- Validation runs on every keystroke
- Visual feedback (red border for invalid, green for valid)
- Form data logged to console on submit

### Problem 3: Dynamic Task List Manager
**Description:**  
Build a task manager with add, complete, and delete functionality.

**Requirements:**
- Input field to add new tasks
- Each task shows: text, completion status, delete button
- Click task text to toggle completion (strike-through when done)
- Delete button removes task from list
- Show count of total tasks and completed tasks
- Filter view: All, Active, Completed
- "Clear Completed" button

**Acceptance Criteria:**
- Tasks stored in state as array of objects
- Each task has: id, text, completed properties
- Use proper event handling for all interactions
- Immutable state updates (don't mutate arrays directly)
- Filter functionality updates display without mutating state

### Problem 4: Parent-Child Communication
**Description:**  
Create a parent component that manages a list of products, and child components for individual products and a summary.

**Requirements:**
- Parent component: `ProductManager`
  - Maintains array of products (name, price, quantity)
  - Add new product functionality
  - Pass products to child components
- Child component: `ProductCard`
  - Display product details
  - Emit events to parent for: increase/decrease quantity, remove product
- Child component: `ProductSummary`
  - Display total items and total value
  - Receive data via props from parent

**Acceptance Criteria:**
- Demonstrate props passing down
- Demonstrate callbacks for child-to-parent communication
- Parent manages all state
- Children are presentation components (mostly)
- Use proper prop destructuring

## Best Practices for Senior Developers

1. **State Management:**
   - Keep state as local as possible
   - Lift state up only when necessary
   - Use multiple useState calls for unrelated state
   - Don't store derived values in state

2. **Event Handlers:**
   - Define handlers outside JSX when complex
   - Use arrow functions for handlers needing parameters
   - Prevent default behavior explicitly when needed
   - Remember that setState is asynchronous

3. **Props:**
   - Destructure props for clarity
   - Use default parameters for optional props
   - Consider prop-types or TypeScript for validation
   - Keep prop drilling shallow (we'll cover Context later)

4. **Performance:**
   - Don't create new function instances in render unnecessarily
   - Be mindful of when components re-render
   - Use functional updates when new state depends on old state

5. **Immutability:**
   - Never mutate state directly
   - Use spread operator for objects: `{...obj, key: newValue}`
   - Use array methods that return new arrays: `map`, `filter`, `concat`
   - For complex updates, consider immer library

## Common Patterns

### Functional State Updates
When new state depends on previous state:
```jsx
// ❌ Don't do this
setCount(count + 1);

// ✅ Do this
setCount(prevCount => prevCount + 1);
```

### Handling Multiple Form Inputs
```jsx
const [formData, setFormData] = useState({ name: '', email: '' });

const handleChange = (e) => {
  setFormData({
    ...formData,
    [e.target.name]: e.target.value
  });
};
```

### Passing Data Up
```jsx
// Parent
function Parent() {
  const handleDataFromChild = (data) => {
    console.log('Received from child:', data);
  };
  
  return <Child onData={handleDataFromChild} />;
}

// Child
function Child({ onData }) {
  const sendData = () => {
    onData({ message: 'Hello Parent!' });
  };
  
  return <button onClick={sendData}>Send to Parent</button>;
}
```

## Additional Resources
- [useState Hook Reference](https://react.dev/reference/react/useState)
- [Handling Events in React](https://react.dev/learn/responding-to-events)
- [Managing State](https://react.dev/learn/managing-state)
- [Passing Props to a Component](https://react.dev/learn/passing-props-to-a-component)

## Next Steps
Tomorrow, we'll explore Component Lifecycle and introduce more React Hooks, including useEffect for side effects.
