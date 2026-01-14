# Week 1 - Day 1: React Basics, JSX, and Components

## Learning Objectives
- Understand what React is and why it's valuable for SPAs
- Learn JSX syntax and how it differs from Vue templates
- Create functional components (similar to Vue's Composition API)
- Understand the Virtual DOM concept
- Set up a modern React development environment

## Key Concepts

### 1. What is React?
React is a declarative, component-based JavaScript library for building user interfaces. As a Vue developer, you'll find many familiar concepts:

**Comparison with Vue:**
- **React**: Uses JSX (JavaScript + XML) for templates
- **Vue**: Uses HTML-based template syntax
- **React**: Component composition through props and children
- **Vue**: Slots and scoped slots for composition
- **React**: Unidirectional data flow (props down, events up)
- **Vue**: Similar unidirectional flow with props and $emit

### 2. JSX Fundamentals
JSX is a syntax extension for JavaScript that looks similar to XML/HTML but has the full power of JavaScript.

**Key Differences from Vue Templates:**
- JSX is JavaScript, so you use `{}` for expressions instead of Vue's `{{ }}`
- Attributes use camelCase: `className` instead of `class`, `onClick` instead of `@click`
- Conditional rendering uses JavaScript operators (ternary, &&) instead of `v-if`
- List rendering uses `.map()` instead of `v-for`

### 3. Functional Components
Modern React uses functional components as the primary building blocks (similar to Vue 3's Composition API).

**Component Structure:**
```jsx
function ComponentName(props) {
  // Component logic here
  return (
    // JSX markup
  );
}
```

### 4. Virtual DOM
React uses a Virtual DOM to efficiently update the UI:
- Creates a lightweight copy of the actual DOM
- Compares changes (reconciliation/diffing)
- Updates only what changed in the real DOM
- Similar to Vue's reactivity system but with different implementation

## Code Examples
See [Week1_Day1_Examples.md](./Week1_Day1_Examples.md) for detailed code samples.

## Daily Assignment

### Problem 1: Create a Profile Card Component
**Description:**  
Build a reusable `ProfileCard` component that displays user information. This is similar to creating a Vue component but using React's JSX syntax.

**Requirements:**
- Accept props: `name`, `title`, `email`, `avatarUrl`, `bio`
- Display the avatar image
- Show name and title prominently
- Include email as a clickable link
- Display bio text
- Apply basic styling (you can use inline styles or a style object)

**Acceptance Criteria:**
- Component should be functional (not class-based)
- All props should be properly typed/documented
- Handle missing optional props gracefully
- Use semantic HTML elements

### Problem 2: Build a Simple Navigation Component
**Description:**  
Create a `Navigation` component that renders a list of navigation links.

**Requirements:**
- Accept an array of navigation items (objects with `id`, `label`, `url`)
- Render each item as a list element with a link
- Highlight the active link based on a prop
- Use proper React key attributes for list items

**Acceptance Criteria:**
- Use `.map()` for rendering the list
- Each list item must have a unique `key` prop
- Active link should have different styling
- Component should handle an empty array gracefully

### Problem 3: Create a Conditional Rendering Dashboard
**Description:**  
Build a `Dashboard` component that conditionally displays different content based on user authentication status and loading state.

**Requirements:**
- Accept props: `isLoading`, `isAuthenticated`, `userName`
- Show "Loading..." when `isLoading` is true
- Show "Welcome, [userName]!" when authenticated
- Show "Please log in" message when not authenticated
- Use conditional rendering (ternary operators or logical &&)

**Acceptance Criteria:**
- Implement proper conditional logic in JSX
- No unnecessary re-renders
- Clear, readable code structure
- Handle all state combinations correctly

## Best Practices for Senior Developers

1. **Component Design:**
   - Keep components small and focused (Single Responsibility Principle)
   - Prefer composition over inheritance
   - Use descriptive prop names

2. **JSX Guidelines:**
   - Keep JSX shallow and readable
   - Extract complex expressions into variables
   - Use fragments (`<>...</>`) to avoid unnecessary wrapper divs

3. **Performance Considerations:**
   - Understand that every state change can trigger re-renders
   - Keep component rendering logic pure (no side effects)
   - Use keys properly in lists for efficient reconciliation

4. **Code Organization:**
   - One component per file (typically)
   - Co-locate related components
   - Consistent file naming (PascalCase for components)

## Additional Resources
- [React Documentation](https://react.dev)
- [JSX In Depth](https://react.dev/learn/writing-markup-with-jsx)
- [Thinking in React](https://react.dev/learn/thinking-in-react)

## Next Steps
Tomorrow, we'll dive deeper into Props, State, and Event Handling - the core of React's interactive capabilities.
