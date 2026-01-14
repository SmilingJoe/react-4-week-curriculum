# Week 1 - Day 2: Code Examples

## Example 1: Props Destructuring and Default Values

```jsx
// ProductCard.jsx

// ❌ Without destructuring (less readable)
function ProductCardBad(props) {
  return (
    <div className="product">
      <h3>{props.name}</h3>
      <p>${props.price}</p>
      <p>Stock: {props.stock}</p>
    </div>
  );
}

// ✅ With destructuring and defaults
function ProductCard({ 
  name, 
  price = 0, 
  stock = 0, 
  onSale = false,
  discount = 0 
}) {
  const finalPrice = onSale ? price * (1 - discount) : price;
  
  return (
    <div className="product">
      <h3>{name}</h3>
      <p className="price">
        ${finalPrice.toFixed(2)}
        {onSale && <span className="sale-badge">SALE!</span>}
      </p>
      <p className={stock > 0 ? 'in-stock' : 'out-of-stock'}>
        Stock: {stock}
      </p>
    </div>
  );
}

export default ProductCard;
```

**Usage:**
```jsx
<ProductCard name="Laptop" price={999.99} stock={5} />
<ProductCard name="Mouse" price={29.99} stock={0} />
<ProductCard name="Keyboard" price={79.99} stock={12} onSale={true} discount={0.15} />
```

**Comparison with Vue:**
```vue
<!-- Vue equivalent -->
<script setup>
const props = defineProps({
  name: String,
  price: { type: Number, default: 0 },
  stock: { type: Number, default: 0 },
  onSale: { type: Boolean, default: false },
  discount: { type: Number, default: 0 }
});

const finalPrice = computed(() => 
  props.onSale ? props.price * (1 - props.discount) : props.price
);
</script>
```

---

## Example 2: useState Hook - Basic Counter

```jsx
// Counter.jsx
import { useState } from 'react';

function Counter() {
  // Declare state variable
  const [count, setCount] = useState(0);

  // Event handlers
  const increment = () => {
    setCount(count + 1);
  };

  const decrement = () => {
    setCount(count - 1);
  };

  const reset = () => {
    setCount(0);
  };

  return (
    <div className="counter">
      <h2>Count: {count}</h2>
      <div className="buttons">
        <button onClick={decrement}>-</button>
        <button onClick={reset}>Reset</button>
        <button onClick={increment}>+</button>
      </div>
    </div>
  );
}

export default Counter;
```

**Comparison with Vue:**
```vue
<script setup>
import { ref } from 'vue';

const count = ref(0);

const increment = () => count.value++;
const decrement = () => count.value--;
const reset = () => count.value = 0;
</script>

<template>
  <div class="counter">
    <h2>Count: {{ count }}</h2>
    <div class="buttons">
      <button @click="decrement">-</button>
      <button @click="reset">Reset</button>
      <button @click="increment">+</button>
    </div>
  </div>
</template>
```

**Explanation:**
- `useState` returns an array: [current value, setter function]
- Always use the setter function to update state
- State updates trigger component re-render
- Similar to Vue's `ref()` but requires explicit setter

---

## Example 3: Multiple State Variables

```jsx
// UserProfile.jsx
import { useState } from 'react';

function UserProfile() {
  const [name, setName] = useState('');
  const [age, setAge] = useState(0);
  const [email, setEmail] = useState('');
  const [isSubscribed, setIsSubscribed] = useState(false);

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log({ name, age, email, isSubscribed });
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Name"
      />
      
      <input
        type="number"
        value={age}
        onChange={(e) => setAge(Number(e.target.value))}
        placeholder="Age"
      />
      
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Email"
      />
      
      <label>
        <input
          type="checkbox"
          checked={isSubscribed}
          onChange={(e) => setIsSubscribed(e.target.checked)}
        />
        Subscribe to newsletter
      </label>
      
      <button type="submit">Submit</button>
    </form>
  );
}

export default UserProfile;
```

**Explanation:**
- Multiple `useState` calls for different pieces of state
- Each state variable is independent
- Event handlers update specific state variables
- Form submission prevents default browser behavior

---

## Example 4: State with Objects

```jsx
// FormWithObject.jsx
import { useState } from 'react';

function FormWithObject() {
  const [formData, setFormData] = useState({
    firstName: '',
    lastName: '',
    email: '',
    phone: ''
  });

  // Generic handler for all inputs
  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prevData => ({
      ...prevData,
      [name]: value
    }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Form submitted:', formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="firstName"
        value={formData.firstName}
        onChange={handleChange}
        placeholder="First Name"
      />
      
      <input
        name="lastName"
        value={formData.lastName}
        onChange={handleChange}
        placeholder="Last Name"
      />
      
      <input
        name="email"
        type="email"
        value={formData.email}
        onChange={handleChange}
        placeholder="Email"
      />
      
      <input
        name="phone"
        value={formData.phone}
        onChange={handleChange}
        placeholder="Phone"
      />
      
      <button type="submit">Submit</button>
    </form>
  );
}

export default FormWithObject;
```

**Explanation:**
- State as object for related values
- Spread operator to maintain immutability
- Computed property names `[name]: value` for dynamic updates
- Single handler function for all inputs

---

## Example 5: Event Handling Patterns

```jsx
// EventHandlingExamples.jsx
import { useState } from 'react';

function EventHandlingExamples() {
  const [message, setMessage] = useState('');

  // Pattern 1: Inline arrow function
  const pattern1 = (
    <button onClick={() => setMessage('Clicked with inline arrow function')}>
      Pattern 1
    </button>
  );

  // Pattern 2: Named function
  const handleClick2 = () => {
    setMessage('Clicked with named function');
  };

  // Pattern 3: Function with parameters
  const handleClickWithParam = (text) => {
    setMessage(text);
  };

  // Pattern 4: Event object access
  const handleInputChange = (e) => {
    setMessage(`Input value: ${e.target.value}`);
  };

  // Pattern 5: Preventing default
  const handleFormSubmit = (e) => {
    e.preventDefault();
    setMessage('Form submitted (default prevented)');
  };

  return (
    <div>
      <h3>Event Handling Patterns</h3>
      <p>Message: {message}</p>

      {pattern1}

      <button onClick={handleClick2}>
        Pattern 2
      </button>

      <button onClick={() => handleClickWithParam('Parameter passed!')}>
        Pattern 3
      </button>

      <input 
        type="text" 
        onChange={handleInputChange} 
        placeholder="Type something"
      />

      <form onSubmit={handleFormSubmit}>
        <button type="submit">Pattern 5 - Submit</button>
      </form>
    </div>
  );
}

export default EventHandlingExamples;
```

**Vue Comparison:**
```vue
<template>
  <!-- Pattern 1: Inline -->
  <button @click="message = 'Clicked inline'">Pattern 1</button>

  <!-- Pattern 2: Method reference -->
  <button @click="handleClick">Pattern 2</button>

  <!-- Pattern 3: Method with parameters -->
  <button @click="handleClickWithParam('Parameter!')">Pattern 3</button>

  <!-- Pattern 4: Event modifier -->
  <form @submit.prevent="handleSubmit">
    <button type="submit">Submit</button>
  </form>
</template>
```

---

## Example 6: Controlled vs Uncontrolled Components

```jsx
// ControlledVsUncontrolled.jsx
import { useState, useRef } from 'react';

function ControlledVsUncontrolled() {
  // Controlled component
  const [controlledValue, setControlledValue] = useState('');

  // Uncontrolled component (ref)
  const uncontrolledRef = useRef(null);

  const handleControlledSubmit = (e) => {
    e.preventDefault();
    console.log('Controlled value:', controlledValue);
  };

  const handleUncontrolledSubmit = (e) => {
    e.preventDefault();
    console.log('Uncontrolled value:', uncontrolledRef.current.value);
  };

  return (
    <div>
      <h3>Controlled Component (Recommended)</h3>
      <form onSubmit={handleControlledSubmit}>
        <input
          type="text"
          value={controlledValue}
          onChange={(e) => setControlledValue(e.target.value)}
          placeholder="Controlled input"
        />
        <p>Current value: {controlledValue}</p>
        <button type="submit">Submit Controlled</button>
      </form>

      <h3>Uncontrolled Component</h3>
      <form onSubmit={handleUncontrolledSubmit}>
        <input
          type="text"
          ref={uncontrolledRef}
          defaultValue=""
          placeholder="Uncontrolled input"
        />
        <button type="submit">Submit Uncontrolled</button>
      </form>
    </div>
  );
}

export default ControlledVsUncontrolled;
```

**Explanation:**
- **Controlled**: React state drives the input value (like v-model in Vue)
- **Uncontrolled**: DOM maintains its own state, accessed via ref
- Controlled is preferred for most cases (validation, formatting, etc.)
- Uncontrolled can be useful for simple forms or file inputs

---

## Example 7: Array State Management

```jsx
// TodoList.jsx
import { useState } from 'react';

function TodoList() {
  const [todos, setTodos] = useState([]);
  const [inputValue, setInputValue] = useState('');

  // Add todo
  const addTodo = () => {
    if (inputValue.trim()) {
      const newTodo = {
        id: Date.now(),
        text: inputValue,
        completed: false
      };
      setTodos([...todos, newTodo]); // Immutable add
      setInputValue('');
    }
  };

  // Toggle completion
  const toggleTodo = (id) => {
    setTodos(todos.map(todo =>
      todo.id === id 
        ? { ...todo, completed: !todo.completed }
        : todo
    ));
  };

  // Delete todo
  const deleteTodo = (id) => {
    setTodos(todos.filter(todo => todo.id !== id));
  };

  // Clear completed
  const clearCompleted = () => {
    setTodos(todos.filter(todo => !todo.completed));
  };

  return (
    <div className="todo-list">
      <div className="input-section">
        <input
          type="text"
          value={inputValue}
          onChange={(e) => setInputValue(e.target.value)}
          onKeyPress={(e) => e.key === 'Enter' && addTodo()}
          placeholder="Add a todo"
        />
        <button onClick={addTodo}>Add</button>
      </div>

      <ul>
        {todos.map(todo => (
          <li key={todo.id} className={todo.completed ? 'completed' : ''}>
            <span onClick={() => toggleTodo(todo.id)}>
              {todo.text}
            </span>
            <button onClick={() => deleteTodo(todo.id)}>Delete</button>
          </li>
        ))}
      </ul>

      <div className="actions">
        <p>Total: {todos.length} | Completed: {todos.filter(t => t.completed).length}</p>
        <button onClick={clearCompleted}>Clear Completed</button>
      </div>
    </div>
  );
}

export default TodoList;
```

**Key Immutability Patterns:**
```jsx
// Add item
setArray([...array, newItem]);

// Update item
setArray(array.map(item => 
  item.id === targetId ? { ...item, updated: true } : item
));

// Delete item
setArray(array.filter(item => item.id !== targetId));

// Replace entire item
setArray(array.map(item =>
  item.id === targetId ? newItem : item
));
```

---

## Example 8: Parent-Child Communication

```jsx
// ParentComponent.jsx
import { useState } from 'react';
import ChildComponent from './ChildComponent';

function ParentComponent() {
  const [messages, setMessages] = useState([]);

  // Callback function passed to child
  const handleMessageFromChild = (message) => {
    setMessages([...messages, {
      id: Date.now(),
      text: message,
      timestamp: new Date().toLocaleTimeString()
    }]);
  };

  return (
    <div className="parent">
      <h2>Parent Component</h2>
      <ChildComponent onSendMessage={handleMessageFromChild} />
      
      <div className="messages">
        <h3>Messages from Child:</h3>
        {messages.map(msg => (
          <div key={msg.id}>
            <strong>{msg.timestamp}:</strong> {msg.text}
          </div>
        ))}
      </div>
    </div>
  );
}

// ChildComponent.jsx
function ChildComponent({ onSendMessage }) {
  const [input, setInput] = useState('');

  const handleSend = () => {
    if (input.trim()) {
      onSendMessage(input); // Call parent's callback
      setInput('');
    }
  };

  return (
    <div className="child">
      <h3>Child Component</h3>
      <input
        type="text"
        value={input}
        onChange={(e) => setInput(e.target.value)}
        onKeyPress={(e) => e.key === 'Enter' && handleSend()}
        placeholder="Type a message"
      />
      <button onClick={handleSend}>Send to Parent</button>
    </div>
  );
}

export default ParentComponent;
```

**Comparison with Vue:**
```vue
<!-- Parent.vue -->
<script setup>
import { ref } from 'vue';
const messages = ref([]);

const handleMessage = (message) => {
  messages.value.push({ id: Date.now(), text: message });
};
</script>

<template>
  <Child @send-message="handleMessage" />
</template>

<!-- Child.vue -->
<script setup>
const emit = defineEmits(['send-message']);
const sendMessage = () => emit('send-message', input.value);
</script>
```

**Explanation:**
- React: Pass callback functions as props
- Vue: Use `$emit` to send events to parent
- React pattern is more explicit about data flow

---

## Example 9: Form Validation Example

```jsx
// ValidatedForm.jsx
import { useState } from 'react';

function ValidatedForm() {
  const [formData, setFormData] = useState({
    username: '',
    email: '',
    password: '',
    confirmPassword: ''
  });

  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});

  // Validation rules
  const validate = (name, value) => {
    switch (name) {
      case 'username':
        if (value.length < 3) return 'Username must be at least 3 characters';
        if (value.length > 20) return 'Username must be less than 20 characters';
        if (!/^[a-zA-Z0-9]+$/.test(value)) return 'Username must be alphanumeric';
        return '';

      case 'email':
        if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value)) return 'Invalid email format';
        return '';

      case 'password':
        if (value.length < 8) return 'Password must be at least 8 characters';
        if (!/\d/.test(value)) return 'Password must contain a number';
        if (!/[!@#$%^&*]/.test(value)) return 'Password must contain a special character';
        return '';

      case 'confirmPassword':
        if (value !== formData.password) return 'Passwords do not match';
        return '';

      default:
        return '';
    }
  };

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({ ...prev, [name]: value }));

    // Validate on change if field was touched
    if (touched[name]) {
      const error = validate(name, value);
      setErrors(prev => ({ ...prev, [name]: error }));
    }
  };

  const handleBlur = (e) => {
    const { name, value } = e.target;
    setTouched(prev => ({ ...prev, [name]: true }));
    const error = validate(name, value);
    setErrors(prev => ({ ...prev, [name]: error }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();

    // Validate all fields
    const newErrors = {};
    Object.keys(formData).forEach(key => {
      const error = validate(key, formData[key]);
      if (error) newErrors[key] = error;
    });

    setErrors(newErrors);
    setTouched({
      username: true,
      email: true,
      password: true,
      confirmPassword: true
    });

    if (Object.keys(newErrors).length === 0) {
      console.log('Form submitted:', formData);
      // Reset form
      setFormData({ username: '', email: '', password: '', confirmPassword: '' });
      setErrors({});
      setTouched({});
    }
  };

  const isFormValid = Object.keys(formData).every(key => 
    formData[key] && !validate(key, formData[key])
  );

  return (
    <form onSubmit={handleSubmit} className="validated-form">
      <div className="form-group">
        <input
          type="text"
          name="username"
          value={formData.username}
          onChange={handleChange}
          onBlur={handleBlur}
          placeholder="Username"
          className={touched.username && errors.username ? 'error' : ''}
        />
        {touched.username && errors.username && (
          <span className="error-message">{errors.username}</span>
        )}
      </div>

      <div className="form-group">
        <input
          type="email"
          name="email"
          value={formData.email}
          onChange={handleChange}
          onBlur={handleBlur}
          placeholder="Email"
          className={touched.email && errors.email ? 'error' : ''}
        />
        {touched.email && errors.email && (
          <span className="error-message">{errors.email}</span>
        )}
      </div>

      <div className="form-group">
        <input
          type="password"
          name="password"
          value={formData.password}
          onChange={handleChange}
          onBlur={handleBlur}
          placeholder="Password"
          className={touched.password && errors.password ? 'error' : ''}
        />
        {touched.password && errors.password && (
          <span className="error-message">{errors.password}</span>
        )}
      </div>

      <div className="form-group">
        <input
          type="password"
          name="confirmPassword"
          value={formData.confirmPassword}
          onChange={handleChange}
          onBlur={handleBlur}
          placeholder="Confirm Password"
          className={touched.confirmPassword && errors.confirmPassword ? 'error' : ''}
        />
        {touched.confirmPassword && errors.confirmPassword && (
          <span className="error-message">{errors.confirmPassword}</span>
        )}
      </div>

      <button type="submit" disabled={!isFormValid}>
        Submit
      </button>
    </form>
  );
}

export default ValidatedForm;
```

**CSS:**
```css
.form-group {
  margin-bottom: 15px;
}

.form-group input {
  width: 100%;
  padding: 8px;
  border: 2px solid #ddd;
}

.form-group input.error {
  border-color: #f44336;
}

.error-message {
  color: #f44336;
  font-size: 12px;
  display: block;
  margin-top: 4px;
}

button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
```

**Explanation:**
- Separate state for form data, errors, and touched fields
- Validation on blur and on change (after first blur)
- Visual feedback for invalid fields
- Submit button disabled until form is valid
- This pattern is common in React (consider Formik or React Hook Form for production)

---

## Example 10: Functional State Updates

```jsx
// FunctionalUpdates.jsx
import { useState } from 'react';

function FunctionalUpdates() {
  const [count, setCount] = useState(0);

  // ❌ Problematic: Multiple updates might not work as expected
  const badIncrement = () => {
    setCount(count + 1);
    setCount(count + 1);
    setCount(count + 1);
    // Will only increment by 1, not 3!
  };

  // ✅ Correct: Functional updates based on previous state
  const goodIncrement = () => {
    setCount(prev => prev + 1);
    setCount(prev => prev + 1);
    setCount(prev => prev + 1);
    // Will correctly increment by 3
  };

  // Complex example with objects
  const [user, setUser] = useState({
    name: 'John',
    age: 30,
    address: { city: 'NYC', zip: '10001' }
  });

  const updateAge = () => {
    // ✅ Functional update with object spreading
    setUser(prev => ({
      ...prev,
      age: prev.age + 1
    }));
  };

  const updateNestedAddress = () => {
    // ✅ Nested object update
    setUser(prev => ({
      ...prev,
      address: {
        ...prev.address,
        city: 'Los Angeles'
      }
    }));
  };

  return (
    <div>
      <h3>Count: {count}</h3>
      <button onClick={badIncrement}>Bad Increment (by 1)</button>
      <button onClick={goodIncrement}>Good Increment (by 3)</button>

      <h3>User Info</h3>
      <p>Name: {user.name}, Age: {user.age}</p>
      <p>City: {user.address.city}, Zip: {user.address.zip}</p>
      <button onClick={updateAge}>Increase Age</button>
      <button onClick={updateNestedAddress}>Move to LA</button>
    </div>
  );
}

export default FunctionalUpdates;
```

**Key Takeaway:**
- When new state depends on previous state, use functional updates
- This ensures you're working with the most current state value
- Especially important when batching multiple updates
