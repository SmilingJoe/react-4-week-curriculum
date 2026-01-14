# Week 1 - Day 1: Code Examples

## Example 1: Hello World Component

### Basic Functional Component
```jsx
// HelloWorld.jsx
function HelloWorld() {
  return <h1>Hello, React!</h1>;
}

export default HelloWorld;
```

### Using the Component
```jsx
// App.jsx
import HelloWorld from './HelloWorld';

function App() {
  return (
    <div className="app">
      <HelloWorld />
    </div>
  );
}

export default App;
```

**Explanation:**
- Functional components are just JavaScript functions that return JSX
- Component names must start with a capital letter
- `export default` makes the component available for import
- Similar to Vue's `export default { name: 'HelloWorld', ... }` but simpler

---

## Example 2: Component with Props

### Vue vs React Comparison

**Vue (Composition API):**
```vue
<template>
  <div class="greeting">
    <h2>{{ greeting }}, {{ name }}!</h2>
    <p>{{ message }}</p>
  </div>
</template>

<script setup>
defineProps({
  greeting: String,
  name: String,
  message: String
});
</script>
```

**React:**
```jsx
// Greeting.jsx
function Greeting({ greeting, name, message }) {
  return (
    <div className="greeting">
      <h2>{greeting}, {name}!</h2>
      <p>{message}</p>
    </div>
  );
}

export default Greeting;
```

**Usage:**
```jsx
<Greeting 
  greeting="Hello" 
  name="Sarah" 
  message="Welcome to React!" 
/>
```

**Explanation:**
- Props are passed as function parameters (destructured for clarity)
- Use `{}` for JavaScript expressions in JSX
- `className` instead of `class` (because `class` is a reserved word in JS)
- No template syntax needed - it's all JavaScript

---

## Example 3: JSX Expressions and Dynamic Content

```jsx
// UserInfo.jsx
function UserInfo({ user, showEmail = true }) {
  const fullName = `${user.firstName} ${user.lastName}`;
  const memberSince = new Date(user.joinDate).getFullYear();
  const yearsActive = new Date().getFullYear() - memberSince;

  return (
    <div className="user-info">
      <h3>{fullName.toUpperCase()}</h3>
      <p>Member since: {memberSince}</p>
      <p>Years active: {yearsActive}</p>
      {showEmail && <p>Email: {user.email}</p>}
      <p>Status: {user.isPremium ? 'Premium Member' : 'Standard Member'}</p>
    </div>
  );
}

export default UserInfo;
```

**Usage:**
```jsx
const user = {
  firstName: 'John',
  lastName: 'Doe',
  email: 'john.doe@example.com',
  joinDate: '2020-03-15',
  isPremium: true
};

<UserInfo user={user} showEmail={true} />
```

**Explanation:**
- Any JavaScript expression can go inside `{}`
- Default parameters work just like regular JavaScript functions
- Conditional rendering with `&&` operator: `{condition && <Element />}`
- Ternary operator for if-else: `{condition ? <A /> : <B />}`
- You can create variables and use them in JSX

---

## Example 4: Rendering Lists

### Vue vs React Comparison

**Vue:**
```vue
<template>
  <ul>
    <li v-for="item in items" :key="item.id">
      {{ item.name }}
    </li>
  </ul>
</template>
```

**React:**
```jsx
// ProductList.jsx
function ProductList({ products }) {
  return (
    <ul className="product-list">
      {products.map((product) => (
        <li key={product.id} className="product-item">
          <h4>{product.name}</h4>
          <p>${product.price.toFixed(2)}</p>
          <span className={product.inStock ? 'in-stock' : 'out-of-stock'}>
            {product.inStock ? 'In Stock' : 'Out of Stock'}
          </span>
        </li>
      ))}
    </ul>
  );
}

export default ProductList;
```

**Usage:**
```jsx
const products = [
  { id: 1, name: 'Laptop', price: 999.99, inStock: true },
  { id: 2, name: 'Mouse', price: 29.99, inStock: true },
  { id: 3, name: 'Keyboard', price: 79.99, inStock: false },
];

<ProductList products={products} />
```

**Explanation:**
- Use `.map()` instead of `v-for`
- Each element in a list must have a unique `key` prop
- Keys help React identify which items have changed
- Return JSX directly from the map function

---

## Example 5: Nested Components and Composition

```jsx
// Card.jsx
function Card({ children, title, footer }) {
  return (
    <div className="card">
      {title && <div className="card-header">{title}</div>}
      <div className="card-body">
        {children}
      </div>
      {footer && <div className="card-footer">{footer}</div>}
    </div>
  );
}

// Button.jsx
function Button({ label, variant = 'primary', onClick }) {
  return (
    <button 
      className={`btn btn-${variant}`}
      onClick={onClick}
    >
      {label}
    </button>
  );
}

// UserCard.jsx - Composing components
function UserCard({ user }) {
  const handleClick = () => {
    console.log(`Clicked on ${user.name}`);
  };

  return (
    <Card 
      title={user.name}
      footer={<Button label="View Profile" onClick={handleClick} />}
    >
      <p>Role: {user.role}</p>
      <p>Department: {user.department}</p>
      <p>Email: {user.email}</p>
    </Card>
  );
}

export default UserCard;
```

**Usage:**
```jsx
const user = {
  name: 'Alice Johnson',
  role: 'Senior Developer',
  department: 'Engineering',
  email: 'alice@example.com'
};

<UserCard user={user} />
```

**Explanation:**
- `children` is a special prop that contains content between component tags
- Similar to Vue's default slot: `<slot />`
- Components can accept other components as props
- This enables powerful composition patterns

---

## Example 6: Multiple Conditional Rendering Patterns

```jsx
// Dashboard.jsx
function Dashboard({ user, isLoading, error }) {
  // Pattern 1: Early return for loading/error states
  if (isLoading) {
    return <div className="loading">Loading dashboard...</div>;
  }

  if (error) {
    return (
      <div className="error">
        <h3>Error</h3>
        <p>{error.message}</p>
      </div>
    );
  }

  if (!user) {
    return <div className="no-data">No user data available</div>;
  }

  // Pattern 2: Conditional rendering in JSX
  return (
    <div className="dashboard">
      <h2>Welcome, {user.name}!</h2>
      
      {/* Using && for conditional rendering */}
      {user.isAdmin && (
        <div className="admin-panel">
          <h3>Admin Controls</h3>
          <button>Manage Users</button>
        </div>
      )}

      {/* Using ternary operator */}
      <div className="status">
        Status: {user.isActive ? (
          <span className="active">Active</span>
        ) : (
          <span className="inactive">Inactive</span>
        )}
      </div>

      {/* Multiple conditions */}
      {user.notifications && user.notifications.length > 0 && (
        <div className="notifications">
          <h3>You have {user.notifications.length} notifications</h3>
          <ul>
            {user.notifications.map((notif, index) => (
              <li key={index}>{notif}</li>
            ))}
          </ul>
        </div>
      )}
    </div>
  );
}

export default Dashboard;
```

**Explanation:**
- Early returns for different states keep code clean
- Use `&&` when you want to render something OR nothing
- Use ternary when you need to choose between two alternatives
- Can combine multiple conditions for complex logic

---

## Example 7: Fragments to Avoid Unnecessary Divs

```jsx
// Without Fragment (adds extra div to DOM)
function BadExample() {
  return (
    <div>
      <h1>Title</h1>
      <p>Paragraph</p>
    </div>
  );
}

// With Fragment (no extra DOM element)
function GoodExample() {
  return (
    <>
      <h1>Title</h1>
      <p>Paragraph</p>
    </>
  );
}

// Fragment with key (when rendering in a list)
function TableRows({ data }) {
  return (
    <table>
      <tbody>
        {data.map((item) => (
          <React.Fragment key={item.id}>
            <tr>
              <td>{item.name}</td>
              <td>{item.value}</td>
            </tr>
            <tr>
              <td colSpan="2">{item.description}</td>
            </tr>
          </React.Fragment>
        ))}
      </tbody>
    </table>
  );
}
```

**Explanation:**
- React components must return a single root element
- Fragments (`<>...</>`) let you group elements without adding extra nodes to DOM
- Similar to Vue's multiple root nodes in Vue 3
- Use `<React.Fragment key={...}>` when you need to add a key

---

## Example 8: Inline Styles and Dynamic Class Names

```jsx
// StyleExample.jsx
function StyleExample({ isHighlighted, status, customColor }) {
  // Inline styles as objects
  const containerStyle = {
    padding: '20px',
    backgroundColor: customColor || '#f0f0f0',
    borderRadius: '8px',
    boxShadow: isHighlighted ? '0 4px 8px rgba(0,0,0,0.2)' : 'none'
  };

  const titleStyle = {
    color: '#333',
    fontSize: '24px',
    marginBottom: '10px'
  };

  // Dynamic class names
  const statusClass = `status status-${status.toLowerCase()}`;
  const containerClass = `container ${isHighlighted ? 'highlighted' : ''}`;

  return (
    <div className={containerClass} style={containerStyle}>
      <h2 style={titleStyle}>Styled Component</h2>
      <p className={statusClass}>Status: {status}</p>
    </div>
  );
}

export default StyleExample;
```

**CSS:**
```css
/* styles.css */
.container {
  transition: all 0.3s ease;
}

.container.highlighted {
  transform: scale(1.05);
}

.status {
  padding: 5px 10px;
  border-radius: 4px;
  display: inline-block;
}

.status-active {
  background-color: #4caf50;
  color: white;
}

.status-pending {
  background-color: #ff9800;
  color: white;
}

.status-inactive {
  background-color: #f44336;
  color: white;
}
```

**Explanation:**
- Inline styles use JavaScript objects with camelCase properties
- Class names can be dynamically constructed with template strings
- For complex class logic, consider using libraries like `classnames`
- CSS-in-JS is popular in React ecosystem (styled-components, emotion)

---

## Example 9: Creating a Reusable Layout Component

```jsx
// Layout.jsx
function Layout({ children, header, sidebar, footer }) {
  return (
    <div className="layout">
      {header && <header className="layout-header">{header}</header>}
      
      <div className="layout-main">
        {sidebar && <aside className="layout-sidebar">{sidebar}</aside>}
        <main className="layout-content">{children}</main>
      </div>
      
      {footer && <footer className="layout-footer">{footer}</footer>}
    </div>
  );
}

// Usage Example
function App() {
  return (
    <Layout
      header={
        <div>
          <h1>My Application</h1>
          <nav>
            <a href="/">Home</a>
            <a href="/about">About</a>
          </nav>
        </div>
      }
      sidebar={
        <ul>
          <li>Menu Item 1</li>
          <li>Menu Item 2</li>
          <li>Menu Item 3</li>
        </ul>
      }
      footer={
        <p>&copy; 2024 My Company</p>
      }
    >
      {/* Main content goes here */}
      <h2>Welcome to the Dashboard</h2>
      <p>This is the main content area.</p>
    </Layout>
  );
}

export default App;
```

**Explanation:**
- Layout components are common patterns for consistent UI structure
- Props can accept JSX elements, not just primitives
- Similar to Vue's named slots but passed as props
- Enables flexible, reusable layouts

---

## Example 10: Setting up a React Project (Vite)

### For Senior Developers: Modern Setup with Vite

```bash
# Create a new React project with Vite (fast modern build tool)
npm create vite@latest my-react-app -- --template react

# Navigate to project
cd my-react-app

# Install dependencies
npm install

# Start development server
npm run dev
```

### Project Structure
```
my-react-app/
├── node_modules/
├── public/
│   └── vite.svg
├── src/
│   ├── assets/
│   ├── App.css
│   ├── App.jsx
│   ├── index.css
│   └── main.jsx
├── .gitignore
├── index.html
├── package.json
└── vite.config.js
```

### main.jsx (Entry Point)
```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App.jsx';
import './index.css';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

**Explanation:**
- Vite is much faster than Create React App (CRA)
- `ReactDOM.createRoot` is the React 18+ way to render
- `React.StrictMode` helps identify potential problems
- Similar to Vue CLI, but Vite works for both React and Vue

**Comparison with Node.js/Express:**
- React runs in the browser (client-side)
- Vite provides dev server with HMR (Hot Module Replacement)
- Similar to nodemon auto-restart, but for frontend
- For full-stack, you'd run both Express (backend) and Vite (frontend)

---

## Solutions to Daily Assignments

### Solution to Problem 1: Create a Profile Card Component

```jsx
// ProfileCard.jsx
function ProfileCard({ 
  name, 
  title, 
  email, 
  avatarUrl, 
  bio 
}) {
  // Handle missing props with defaults or conditional rendering
  const displayAvatar = avatarUrl || 'https://via.placeholder.com/150';
  
  return (
    <article className="profile-card">
      <div className="profile-header">
        <img 
          src={displayAvatar} 
          alt={`${name}'s avatar`}
          className="profile-avatar"
        />
        <div className="profile-info">
          <h2 className="profile-name">{name || 'Anonymous User'}</h2>
          <h3 className="profile-title">{title || 'No title'}</h3>
        </div>
      </div>
      
      <div className="profile-contact">
        <a href={`mailto:${email}`} className="profile-email">
          {email}
        </a>
      </div>
      
      {bio && (
        <div className="profile-bio">
          <p>{bio}</p>
        </div>
      )}
    </article>
  );
}

// CSS styles (inline for demonstration)
const styles = {
  profileCard: {
    border: '1px solid #ddd',
    borderRadius: '8px',
    padding: '20px',
    maxWidth: '400px',
    boxShadow: '0 2px 4px rgba(0,0,0,0.1)'
  },
  profileHeader: {
    display: 'flex',
    alignItems: 'center',
    marginBottom: '15px'
  },
  profileAvatar: {
    width: '80px',
    height: '80px',
    borderRadius: '50%',
    marginRight: '15px'
  },
  profileName: {
    margin: '0 0 5px 0',
    fontSize: '24px'
  },
  profileTitle: {
    margin: 0,
    fontSize: '16px',
    color: '#666'
  },
  profileEmail: {
    color: '#007bff',
    textDecoration: 'none'
  },
  profileBio: {
    marginTop: '15px',
    padding: '10px',
    backgroundColor: '#f8f9fa',
    borderRadius: '4px'
  }
};

// Usage example
function App() {
  return (
    <div>
      <ProfileCard
        name="Jane Doe"
        title="Senior Software Engineer"
        email="jane.doe@example.com"
        avatarUrl="https://i.pravatar.cc/150?img=1"
        bio="Passionate about building scalable web applications and mentoring junior developers."
      />
    </div>
  );
}

export default ProfileCard;
```

**Key Points:**
- Uses semantic HTML (`article`, `h2`, `h3`)
- Handles missing optional props (avatar with placeholder, conditional bio rendering)
- Props are properly destructured
- Email is a clickable mailto link
- Component is purely functional

---

### Solution to Problem 2: Build a Simple Navigation Component

```jsx
// Navigation.jsx
function Navigation({ items = [], activeId }) {
  // Handle empty array gracefully
  if (!items || items.length === 0) {
    return <nav className="navigation">No navigation items</nav>;
  }

  return (
    <nav className="navigation">
      <ul className="nav-list">
        {items.map((item) => {
          const isActive = item.id === activeId;
          
          return (
            <li 
              key={item.id} 
              className={`nav-item ${isActive ? 'active' : ''}`}
            >
              <a 
                href={item.url} 
                className={`nav-link ${isActive ? 'active' : ''}`}
              >
                {item.label}
              </a>
            </li>
          );
        })}
      </ul>
    </nav>
  );
}

// CSS styles
const navStyles = `
  .navigation {
    background: #333;
    padding: 10px;
  }
  
  .nav-list {
    list-style: none;
    margin: 0;
    padding: 0;
    display: flex;
    gap: 20px;
  }
  
  .nav-item {
    display: inline-block;
  }
  
  .nav-link {
    color: #fff;
    text-decoration: none;
    padding: 8px 16px;
    border-radius: 4px;
    transition: background 0.3s;
  }
  
  .nav-link:hover {
    background: #555;
  }
  
  .nav-link.active {
    background: #007bff;
    font-weight: bold;
  }
`;

// Usage example
function App() {
  const navItems = [
    { id: 'home', label: 'Home', url: '/' },
    { id: 'about', label: 'About', url: '/about' },
    { id: 'services', label: 'Services', url: '/services' },
    { id: 'contact', label: 'Contact', url: '/contact' }
  ];

  return (
    <div>
      <Navigation items={navItems} activeId="home" />
      {/* Empty navigation example */}
      <Navigation items={[]} />
    </div>
  );
}

export default Navigation;
```

**Key Points:**
- Each item has a unique `key` prop (item.id)
- Uses `.map()` for list rendering
- Active link highlighted with conditional className
- Handles empty array gracefully
- Default parameter for items array

---

### Solution to Problem 3: Create a Conditional Rendering Dashboard

```jsx
// Dashboard.jsx
function Dashboard({ isLoading, isAuthenticated, userName }) {
  // Early return for loading state
  if (isLoading) {
    return (
      <div className="dashboard loading">
        <div className="spinner"></div>
        <p>Loading...</p>
      </div>
    );
  }

  // Conditional rendering based on authentication
  return (
    <div className="dashboard">
      {isAuthenticated ? (
        <div className="dashboard-authenticated">
          <h1>Welcome, {userName}!</h1>
          <div className="dashboard-content">
            <div className="card">
              <h3>Your Stats</h3>
              <p>Projects: 12</p>
              <p>Tasks: 45</p>
            </div>
            <div className="card">
              <h3>Recent Activity</h3>
              <p>Last login: Today at 9:00 AM</p>
            </div>
          </div>
        </div>
      ) : (
        <div className="dashboard-unauthenticated">
          <h2>Please log in</h2>
          <p>You need to be authenticated to view this dashboard.</p>
          <button className="login-button">Log In</button>
        </div>
      )}
    </div>
  );
}

// CSS styles
const dashboardStyles = `
  .dashboard {
    padding: 20px;
    min-height: 400px;
  }
  
  .dashboard.loading {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }
  
  .spinner {
    border: 4px solid #f3f3f3;
    border-top: 4px solid #3498db;
    border-radius: 50%;
    width: 40px;
    height: 40px;
    animation: spin 1s linear infinite;
  }
  
  @keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
  }
  
  .dashboard-content {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 20px;
    margin-top: 20px;
  }
  
  .card {
    border: 1px solid #ddd;
    border-radius: 8px;
    padding: 20px;
    background: white;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  }
  
  .login-button {
    padding: 10px 20px;
    background: #007bff;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 16px;
  }
  
  .login-button:hover {
    background: #0056b3;
  }
`;

// Usage examples
function App() {
  return (
    <div>
      {/* Loading state */}
      <Dashboard isLoading={true} />
      
      {/* Authenticated state */}
      <Dashboard 
        isLoading={false} 
        isAuthenticated={true} 
        userName="John Doe" 
      />
      
      {/* Unauthenticated state */}
      <Dashboard 
        isLoading={false} 
        isAuthenticated={false} 
      />
    </div>
  );
}

export default Dashboard;
```

**Key Points:**
- Early return pattern for loading state
- Ternary operator for authenticated vs unauthenticated
- All state combinations handled correctly
- Clear, readable conditional logic
- No unnecessary re-renders (pure component logic)
- Uses logical && for optional content within authenticated view
