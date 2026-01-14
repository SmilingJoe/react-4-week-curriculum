# Week 1 - Day 5: Forms and Controlled Components

## Learning Objectives
- Master form handling in React
- Understand controlled vs uncontrolled components in depth
- Implement complex form patterns with multiple input types
- Learn form validation strategies
- Explore form libraries (Formik, React Hook Form)

## Key Concepts

### 1. Controlled Components
In React, form inputs are typically "controlled" - their value is controlled by React state.

**Pattern:**
```jsx
const [value, setValue] = useState('');
<input value={value} onChange={(e) => setValue(e.target.value)} />
```

**Benefits:**
- Single source of truth (React state)
- Easy validation and formatting
- Can enforce rules (e.g., max length, uppercase)
- Better for dynamic forms

**Comparison with Vue:**
- **React**: Explicit two-way binding with value + onChange
- **Vue**: `v-model` provides automatic two-way binding
- **React**: More verbose but more control
- **Vue**: More concise but less explicit

### 2. Form Input Types

**Text Inputs:**
```jsx
<input type="text" value={value} onChange={handleChange} />
<textarea value={value} onChange={handleChange} />
```

**Checkboxes:**
```jsx
<input type="checkbox" checked={checked} onChange={handleChange} />
```

**Radio Buttons:**
```jsx
<input type="radio" checked={selected === value} onChange={handleChange} />
```

**Select Dropdowns:**
```jsx
<select value={selected} onChange={handleChange}>
  <option value="a">A</option>
  <option value="b">B</option>
</select>
```

### 3. Form Submission
Always prevent default form submission behavior:

```jsx
const handleSubmit = (e) => {
  e.preventDefault();
  // Process form data
};
```

### 4. Validation Strategies

**Client-Side Validation:**
- Real-time validation (on change/blur)
- Form-level validation (on submit)
- Field-level validation
- Custom validation rules

**Patterns:**
- Validate on blur for better UX
- Show errors only after user interaction
- Disable submit until form is valid
- Clear, helpful error messages

### 5. Form Libraries

**Formik:**
- Handles form state and validation
- Reduces boilerplate
- Good for complex forms

**React Hook Form:**
- Performance-focused
- Minimal re-renders
- Uses uncontrolled components with refs
- Smaller bundle size

## Code Examples
See [Week1_Day5_Examples.md](./Week1_Day5_Examples.md) for detailed code samples.

## Daily Assignment

### Problem 1: Complex Registration Form
**Description:**  
Build a comprehensive registration form with multiple input types and validation.

**Requirements:**
- Fields: email, password, confirm password, first name, last name
- Country dropdown (at least 5 countries)
- Age input (number, must be 18+)
- Terms and conditions checkbox
- Newsletter opt-in checkbox
- Real-time validation for all fields
- Show validation errors appropriately
- Disable submit until form is valid
- Success message after submission

**Acceptance Criteria:**
- All inputs are controlled components
- Validation runs on blur and submit
- Errors only shown after field is touched
- Password strength indicator
- Form data logged on successful submit
- Form resets after submission

### Problem 2: Dynamic Survey Form
**Description:**  
Create a survey form where questions can be added/removed dynamically.

**Requirements:**
- Add question button (adds text input for question)
- Each question has: question text, answer type (text/radio/checkbox)
- For radio/checkbox, allow adding options
- Remove question button for each question
- Reorder questions (up/down buttons)
- Collect all responses
- Export to JSON

**Acceptance Criteria:**
- Questions stored in array state
- Each question has unique ID
- Proper key props for list items
- Add/remove/reorder without bugs
- Export shows complete survey structure
- Clean, maintainable code

### Problem 3: File Upload with Preview
**Description:**  
Build a file upload component with image preview and validation.

**Requirements:**
- File input for image upload
- Preview uploaded image
- Validate file type (only images)
- Validate file size (max 2MB)
- Show upload progress (simulated)
- Remove/replace image functionality
- Display file metadata (name, size, type)

**Acceptance Criteria:**
- Use FileReader API for preview
- Proper error handling and messages
- Clear visual feedback
- Progress bar during upload
- Can clear selection

### Problem 4: Search Filter Form
**Description:**  
Create a product search form with multiple filters.

**Requirements:**
- Text search input
- Category dropdown (Electronics, Clothing, Books, etc.)
- Price range slider (or two inputs: min/max)
- Rating filter (1-5 stars, checkboxes or radio)
- In stock checkbox
- Results update as filters change (debounced search)
- Clear all filters button
- Show active filter count

**Acceptance Criteria:**
- Controlled components for all inputs
- Debounced search (500ms)
- Filters applied cumulatively
- URL parameters update with filters (bonus)
- Results filtered client-side from mock data

### Problem 5: Form with useReducer
**Description:**  
Build a complex form using useReducer instead of multiple useState calls.

**Requirements:**
- Contact form: name, email, subject, message, priority
- Use useReducer to manage all form state
- Actions: UPDATE_FIELD, RESET_FORM, SET_ERRORS, SUBMIT
- Validation logic in reducer
- Error state managed in reducer
- Submission state (idle, submitting, success, error)

**Acceptance Criteria:**
- Single reducer manages all form state
- Actions are clearly defined
- Reducer is pure function
- Form behavior same as useState version
- Code is more maintainable and scalable

## Best Practices for Senior Developers

1. **Form State Management:**
   - Group related form fields in single state object
   - Use useReducer for complex forms
   - Consider form libraries for large forms
   - Keep validation logic separate

2. **User Experience:**
   - Validate on blur, not on every keystroke
   - Show errors only after user interaction
   - Provide clear, actionable error messages
   - Disable submit button during submission
   - Show loading states

3. **Performance:**
   - Debounce expensive validation
   - Memoize validation functions
   - Avoid unnecessary re-renders
   - Use controlled components wisely

4. **Accessibility:**
   - Proper label associations
   - ARIA attributes for errors
   - Keyboard navigation
   - Screen reader support
   - Focus management

5. **Security:**
   - Always validate on server-side
   - Sanitize inputs
   - Use HTTPS for sensitive data
   - Don't trust client-side validation alone

## Common Patterns

### Generic Input Handler
```jsx
const handleChange = (e) => {
  const { name, value, type, checked } = e.target;
  setFormData({
    ...formData,
    [name]: type === 'checkbox' ? checked : value
  });
};
```

### Validation on Blur
```jsx
const handleBlur = (field) => {
  setTouched({ ...touched, [field]: true });
  validateField(field);
};
```

### Form Reset
```jsx
const resetForm = () => {
  setFormData(initialFormData);
  setErrors({});
  setTouched({});
};
```

## Form Libraries Comparison

### React Hook Form
```jsx
import { useForm } from 'react-hook-form';

function MyForm() {
  const { register, handleSubmit, formState: { errors } } = useForm();
  
  const onSubmit = (data) => console.log(data);
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('email', { required: true })} />
      {errors.email && <span>Required</span>}
      <button type="submit">Submit</button>
    </form>
  );
}
```

### Formik
```jsx
import { Formik, Form, Field } from 'formik';

function MyForm() {
  return (
    <Formik
      initialValues={{ email: '' }}
      onSubmit={(values) => console.log(values)}
    >
      <Form>
        <Field name="email" type="email" />
        <button type="submit">Submit</button>
      </Form>
    </Formik>
  );
}
```

## Additional Resources
- [Forms in React](https://react.dev/reference/react-dom/components/input)
- [React Hook Form](https://react-hook-form.com/)
- [Formik](https://formik.org/)
- [Yup Validation](https://github.com/jquense/yup)

## Next Steps
Next week, we'll dive into Advanced Hooks (useContext, useReducer) and learn custom hooks for reusable logic!
