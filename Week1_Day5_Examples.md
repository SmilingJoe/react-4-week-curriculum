# Week 1 - Day 5: Code Examples

## Example 1: Basic Controlled Form

```jsx
// BasicForm.jsx
import { useState } from 'react';

function BasicForm() {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    message: ''
  });

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({
      ...prev,
      [name]: value
    }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Form submitted:', formData);
    alert(`Thank you, ${formData.name}!`);
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="name">Name:</label>
        <input
          id="name"
          name="name"
          type="text"
          value={formData.name}
          onChange={handleChange}
          required
        />
      </div>

      <div>
        <label htmlFor="email">Email:</label>
        <input
          id="email"
          name="email"
          type="email"
          value={formData.email}
          onChange={handleChange}
          required
        />
      </div>

      <div>
        <label htmlFor="message">Message:</label>
        <textarea
          id="message"
          name="message"
          value={formData.message}
          onChange={handleChange}
          rows="4"
        />
      </div>

      <button type="submit">Submit</button>
    </form>
  );
}

export default BasicForm;
```

**Explanation:**
- Single state object for all form fields
- Generic handleChange works for all inputs
- Controlled components (value prop)
- Form submission prevents default behavior
- Uses name attribute to update correct field

---

## Example 2: Checkbox and Radio Inputs

```jsx
// CheckboxRadioForm.jsx
import { useState } from 'react';

function CheckboxRadioForm() {
  const [formData, setFormData] = useState({
    interests: {
      coding: false,
      reading: false,
      gaming: false
    },
    experience: 'beginner'
  });

  const handleCheckboxChange = (e) => {
    const { name, checked } = e.target;
    setFormData(prev => ({
      ...prev,
      interests: {
        ...prev.interests,
        [name]: checked
      }
    }));
  };

  const handleRadioChange = (e) => {
    setFormData(prev => ({
      ...prev,
      experience: e.target.value
    }));
  };

  return (
    <form>
      <fieldset>
        <legend>Interests</legend>
        <label>
          <input
            type="checkbox"
            name="coding"
            checked={formData.interests.coding}
            onChange={handleCheckboxChange}
          />
          Coding
        </label>
        <label>
          <input
            type="checkbox"
            name="reading"
            checked={formData.interests.reading}
            onChange={handleCheckboxChange}
          />
          Reading
        </label>
        <label>
          <input
            type="checkbox"
            name="gaming"
            checked={formData.interests.gaming}
            onChange={handleCheckboxChange}
          />
          Gaming
        </label>
      </fieldset>

      <fieldset>
        <legend>Experience Level</legend>
        {['beginner', 'intermediate', 'advanced'].map(level => (
          <label key={level}>
            <input
              type="radio"
              name="experience"
              value={level}
              checked={formData.experience === level}
              onChange={handleRadioChange}
            />
            {level.charAt(0).toUpperCase() + level.slice(1)}
          </label>
        ))}
      </fieldset>

      <pre>{JSON.stringify(formData, null, 2)}</pre>
    </form>
  );
}

export default CheckboxRadioForm;
```

**Key Points:**
- Checkboxes use `checked` prop, not `value`
- Radio buttons check if value matches state
- Separate handlers for different input types
- Nested state updates for checkbox groups

---

## Example 3: Select Dropdown and Multiple Select

```jsx
// SelectForm.jsx
import { useState } from 'react';

function SelectForm() {
  const [country, setCountry] = useState('');
  const [skills, setSkills] = useState([]);

  const handleCountryChange = (e) => {
    setCountry(e.target.value);
  };

  const handleSkillsChange = (e) => {
    const options = e.target.options;
    const selected = [];
    for (let i = 0; i < options.length; i++) {
      if (options[i].selected) {
        selected.push(options[i].value);
      }
    }
    setSkills(selected);
  };

  return (
    <form>
      <div>
        <label htmlFor="country">Country:</label>
        <select
          id="country"
          value={country}
          onChange={handleCountryChange}
        >
          <option value="">Select a country</option>
          <option value="us">United States</option>
          <option value="uk">United Kingdom</option>
          <option value="ca">Canada</option>
          <option value="au">Australia</option>
        </select>
      </div>

      <div>
        <label htmlFor="skills">Skills (hold Ctrl/Cmd for multiple):</label>
        <select
          id="skills"
          multiple
          value={skills}
          onChange={handleSkillsChange}
          size="5"
        >
          <option value="react">React</option>
          <option value="vue">Vue</option>
          <option value="angular">Angular</option>
          <option value="node">Node.js</option>
          <option value="python">Python</option>
        </select>
      </div>

      <div>
        <p>Selected country: {country || 'None'}</p>
        <p>Selected skills: {skills.join(', ') || 'None'}</p>
      </div>
    </form>
  );
}

export default SelectForm;
```

**Explanation:**
- Single select uses string value
- Multiple select uses array value
- Loop through options to get selected values
- Empty string for "no selection" placeholder

---

## Example 4: Form Validation with Error Display

```jsx
// ValidatedContactForm.jsx
import { useState } from 'react';

function ValidatedContactForm() {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    phone: '',
    message: ''
  });

  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});

  const validate = (name, value) => {
    switch (name) {
      case 'name':
        if (!value.trim()) return 'Name is required';
        if (value.length < 2) return 'Name must be at least 2 characters';
        return '';

      case 'email':
        if (!value) return 'Email is required';
        if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value)) {
          return 'Invalid email format';
        }
        return '';

      case 'phone':
        if (value && !/^\d{10}$/.test(value.replace(/\D/g, ''))) {
          return 'Phone must be 10 digits';
        }
        return '';

      case 'message':
        if (!value.trim()) return 'Message is required';
        if (value.length < 10) return 'Message must be at least 10 characters';
        return '';

      default:
        return '';
    }
  };

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({ ...prev, [name]: value }));

    // Validate if field has been touched
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

    // Mark all as touched
    const allTouched = Object.keys(formData).reduce((acc, key) => {
      acc[key] = true;
      return acc;
    }, {});
    setTouched(allTouched);

    if (Object.keys(newErrors).length > 0) {
      setErrors(newErrors);
      return;
    }

    // Submit form
    console.log('Form submitted:', formData);
    alert('Form submitted successfully!');

    // Reset
    setFormData({ name: '', email: '', phone: '', message: '' });
    setErrors({});
    setTouched({});
  };

  const isFormValid = () => {
    return Object.keys(formData).every(key => {
      const value = formData[key];
      if (key === 'phone') return true; // Phone is optional
      return value && !validate(key, value);
    });
  };

  return (
    <form onSubmit={handleSubmit} className="contact-form">
      <h2>Contact Us</h2>

      <div className="form-group">
        <label htmlFor="name">Name *</label>
        <input
          id="name"
          name="name"
          type="text"
          value={formData.name}
          onChange={handleChange}
          onBlur={handleBlur}
          className={touched.name && errors.name ? 'error' : ''}
        />
        {touched.name && errors.name && (
          <span className="error-message">{errors.name}</span>
        )}
      </div>

      <div className="form-group">
        <label htmlFor="email">Email *</label>
        <input
          id="email"
          name="email"
          type="email"
          value={formData.email}
          onChange={handleChange}
          onBlur={handleBlur}
          className={touched.email && errors.email ? 'error' : ''}
        />
        {touched.email && errors.email && (
          <span className="error-message">{errors.email}</span>
        )}
      </div>

      <div className="form-group">
        <label htmlFor="phone">Phone (optional)</label>
        <input
          id="phone"
          name="phone"
          type="tel"
          value={formData.phone}
          onChange={handleChange}
          onBlur={handleBlur}
          placeholder="1234567890"
          className={touched.phone && errors.phone ? 'error' : ''}
        />
        {touched.phone && errors.phone && (
          <span className="error-message">{errors.phone}</span>
        )}
      </div>

      <div className="form-group">
        <label htmlFor="message">Message *</label>
        <textarea
          id="message"
          name="message"
          value={formData.message}
          onChange={handleChange}
          onBlur={handleBlur}
          rows="5"
          className={touched.message && errors.message ? 'error' : ''}
        />
        {touched.message && errors.message && (
          <span className="error-message">{errors.message}</span>
        )}
      </div>

      <button type="submit" disabled={!isFormValid()}>
        Submit
      </button>
    </form>
  );
}

export default ValidatedContactForm;
```

**Best Practices:**
- Separate validation logic
- Show errors only after touch
- Validate on blur and submit
- Disabled submit until valid
- Clear error messages

---

## Example 5: Uncontrolled Component with useRef

```jsx
// UncontrolledForm.jsx
import { useRef } from 'react';

function UncontrolledForm() {
  const nameRef = useRef(null);
  const emailRef = useRef(null);
  const fileRef = useRef(null);

  const handleSubmit = (e) => {
    e.preventDefault();
    
    const formData = {
      name: nameRef.current.value,
      email: emailRef.current.value,
      file: fileRef.current.files[0]
    };

    console.log('Form data:', formData);
    console.log('File:', formData.file?.name);

    // Reset form
    e.target.reset();
  };

  const focusName = () => {
    nameRef.current.focus();
  };

  return (
    <div>
      <h3>Uncontrolled Form Example</h3>
      <form onSubmit={handleSubmit}>
        <div>
          <label htmlFor="name">Name:</label>
          <input
            id="name"
            ref={nameRef}
            type="text"
            defaultValue="John Doe"
          />
        </div>

        <div>
          <label htmlFor="email">Email:</label>
          <input
            id="email"
            ref={emailRef}
            type="email"
            defaultValue="john@example.com"
          />
        </div>

        <div>
          <label htmlFor="file">File:</label>
          <input
            id="file"
            ref={fileRef}
            type="file"
          />
        </div>

        <button type="submit">Submit</button>
        <button type="button" onClick={focusName}>
          Focus Name
        </button>
      </form>
    </div>
  );
}

export default UncontrolledForm;
```

**When to Use Uncontrolled:**
- File inputs (must be uncontrolled)
- Simple forms without validation
- Integrating with non-React code
- Performance-critical scenarios

**Note:** Controlled components are preferred for most cases.

---

## Example 6: Dynamic Form Fields

```jsx
// DynamicFieldsForm.jsx
import { useState } from 'react';

function DynamicFieldsForm() {
  const [emails, setEmails] = useState(['']);

  const addEmail = () => {
    setEmails([...emails, '']);
  };

  const removeEmail = (index) => {
    setEmails(emails.filter((_, i) => i !== index));
  };

  const updateEmail = (index, value) => {
    const newEmails = [...emails];
    newEmails[index] = value;
    setEmails(newEmails);
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Emails:', emails.filter(email => email.trim()));
  };

  return (
    <form onSubmit={handleSubmit}>
      <h3>Email Addresses</h3>
      
      {emails.map((email, index) => (
        <div key={index} style={{ marginBottom: '10px' }}>
          <input
            type="email"
            value={email}
            onChange={(e) => updateEmail(index, e.target.value)}
            placeholder={`Email ${index + 1}`}
            style={{ marginRight: '10px' }}
          />
          {emails.length > 1 && (
            <button
              type="button"
              onClick={() => removeEmail(index)}
            >
              Remove
            </button>
          )}
        </div>
      ))}

      <button type="button" onClick={addEmail}>
        Add Email
      </button>
      <button type="submit">Submit</button>
    </form>
  );
}

export default DynamicFieldsForm;
```

**Key Points:**
- Array state for dynamic fields
- Use index for keys (acceptable here since order is stable)
- Immutable updates (filter, spread)
- At least one field always present

---

## Example 7: Form with useReducer

```jsx
// FormWithReducer.jsx
import { useReducer } from 'react';

const initialState = {
  formData: {
    username: '',
    email: '',
    password: ''
  },
  errors: {},
  touched: {},
  isSubmitting: false
};

function formReducer(state, action) {
  switch (action.type) {
    case 'UPDATE_FIELD':
      return {
        ...state,
        formData: {
          ...state.formData,
          [action.field]: action.value
        }
      };

    case 'SET_TOUCHED':
      return {
        ...state,
        touched: {
          ...state.touched,
          [action.field]: true
        }
      };

    case 'SET_ERROR':
      return {
        ...state,
        errors: {
          ...state.errors,
          [action.field]: action.error
        }
      };

    case 'SET_SUBMITTING':
      return {
        ...state,
        isSubmitting: action.value
      };

    case 'RESET_FORM':
      return initialState;

    default:
      return state;
  }
}

function FormWithReducer() {
  const [state, dispatch] = useReducer(formReducer, initialState);

  const validateField = (field, value) => {
    switch (field) {
      case 'username':
        return value.length < 3 ? 'Username must be at least 3 characters' : '';
      case 'email':
        return !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value) ? 'Invalid email' : '';
      case 'password':
        return value.length < 8 ? 'Password must be at least 8 characters' : '';
      default:
        return '';
    }
  };

  const handleChange = (e) => {
    const { name, value } = e.target;
    dispatch({ type: 'UPDATE_FIELD', field: name, value });

    if (state.touched[name]) {
      const error = validateField(name, value);
      dispatch({ type: 'SET_ERROR', field: name, error });
    }
  };

  const handleBlur = (e) => {
    const { name, value } = e.target;
    dispatch({ type: 'SET_TOUCHED', field: name });
    const error = validateField(name, value);
    dispatch({ type: 'SET_ERROR', field: name, error });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    dispatch({ type: 'SET_SUBMITTING', value: true });

    // Simulate API call
    await new Promise(resolve => setTimeout(resolve, 1000));

    console.log('Submitted:', state.formData);
    dispatch({ type: 'RESET_FORM' });
  };

  return (
    <form onSubmit={handleSubmit}>
      <h3>Form with useReducer</h3>

      <div>
        <input
          name="username"
          value={state.formData.username}
          onChange={handleChange}
          onBlur={handleBlur}
          placeholder="Username"
        />
        {state.touched.username && state.errors.username && (
          <span className="error">{state.errors.username}</span>
        )}
      </div>

      <div>
        <input
          name="email"
          type="email"
          value={state.formData.email}
          onChange={handleChange}
          onBlur={handleBlur}
          placeholder="Email"
        />
        {state.touched.email && state.errors.email && (
          <span className="error">{state.errors.email}</span>
        )}
      </div>

      <div>
        <input
          name="password"
          type="password"
          value={state.formData.password}
          onChange={handleChange}
          onBlur={handleBlur}
          placeholder="Password"
        />
        {state.touched.password && state.errors.password && (
          <span className="error">{state.errors.password}</span>
        )}
      </div>

      <button type="submit" disabled={state.isSubmitting}>
        {state.isSubmitting ? 'Submitting...' : 'Submit'}
      </button>
    </form>
  );
}

export default FormWithReducer;
```

**Benefits of useReducer:**
- Centralized state logic
- Easier to test
- Better for complex state
- More predictable updates

---

## Example 8: File Upload with Preview

```jsx
// FileUpload.jsx
import { useState } from 'react';

function FileUpload() {
  const [file, setFile] = useState(null);
  const [preview, setPreview] = useState(null);
  const [error, setError] = useState('');
  const [uploading, setUploading] = useState(false);
  const [progress, setProgress] = useState(0);

  const handleFileChange = (e) => {
    const selectedFile = e.target.files[0];
    
    if (!selectedFile) {
      return;
    }

    // Validate file type
    if (!selectedFile.type.startsWith('image/')) {
      setError('Please select an image file');
      return;
    }

    // Validate file size (2MB)
    if (selectedFile.size > 2 * 1024 * 1024) {
      setError('File size must be less than 2MB');
      return;
    }

    setError('');
    setFile(selectedFile);

    // Create preview
    const reader = new FileReader();
    reader.onloadend = () => {
      setPreview(reader.result);
    };
    reader.readAsDataURL(selectedFile);
  };

  const handleUpload = () => {
    if (!file) return;

    setUploading(true);
    setProgress(0);

    // Simulate upload progress
    const interval = setInterval(() => {
      setProgress(prev => {
        if (prev >= 100) {
          clearInterval(interval);
          setUploading(false);
          alert('Upload complete!');
          return 100;
        }
        return prev + 10;
      });
    }, 200);
  };

  const handleClear = () => {
    setFile(null);
    setPreview(null);
    setError('');
    setProgress(0);
  };

  return (
    <div className="file-upload">
      <h3>Image Upload</h3>

      <div>
        <input
          type="file"
          onChange={handleFileChange}
          accept="image/*"
        />
      </div>

      {error && <div className="error">{error}</div>}

      {preview && (
        <div className="preview">
          <h4>Preview:</h4>
          <img 
            src={preview} 
            alt="Preview" 
            style={{ maxWidth: '300px', maxHeight: '300px' }}
          />
          
          <div className="file-info">
            <p>Name: {file.name}</p>
            <p>Size: {(file.size / 1024).toFixed(2)} KB</p>
            <p>Type: {file.type}</p>
          </div>

          {uploading && (
            <div className="progress-bar">
              <div 
                className="progress-fill"
                style={{ width: `${progress}%`, background: '#4caf50', height: '20px' }}
              >
                {progress}%
              </div>
            </div>
          )}

          <div>
            <button onClick={handleUpload} disabled={uploading}>
              Upload
            </button>
            <button onClick={handleClear} disabled={uploading}>
              Clear
            </button>
          </div>
        </div>
      )}
    </div>
  );
}

export default FileUpload;
```

**Key Points:**
- FileReader API for preview
- File validation (type, size)
- Simulated upload progress
- Metadata display
- Clear/reset functionality

---

## Solutions to Daily Assignments

### Solution to Problem 1: Complex Registration Form

```jsx
// ComplexRegistrationForm.jsx
import { useState } from 'react';

function ComplexRegistrationForm() {
  const [formData, setFormData] = useState({
    email: '',
    password: '',
    confirmPassword: '',
    firstName: '',
    lastName: '',
    country: '',
    age: '',
    terms: false,
    newsletter: false
  });

  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});
  const [submitted, setSubmitted] = useState(false);

  const countries = [
    { code: 'us', name: 'United States' },
    { code: 'uk', name: 'United Kingdom' },
    { code: 'ca', name: 'Canada' },
    { code: 'au', name: 'Australia' },
    { code: 'de', name: 'Germany' }
  ];

  const getPasswordStrength = (password) => {
    let strength = 0;
    if (password.length >= 8) strength++;
    if (password.length >= 12) strength++;
    if (/[a-z]/.test(password) && /[A-Z]/.test(password)) strength++;
    if (/\d/.test(password)) strength++;
    if (/[^a-zA-Z\d]/.test(password)) strength++;
    
    if (strength <= 2) return { level: 'Weak', color: '#f44336' };
    if (strength <= 3) return { level: 'Medium', color: '#ff9800' };
    return { level: 'Strong', color: '#4caf50' };
  };

  const validate = (name, value) => {
    switch (name) {
      case 'email':
        if (!value) return 'Email is required';
        if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value)) return 'Invalid email format';
        return '';

      case 'password':
        if (!value) return 'Password is required';
        if (value.length < 8) return 'Password must be at least 8 characters';
        if (!/\d/.test(value)) return 'Password must contain a number';
        if (!/[^a-zA-Z\d]/.test(value)) return 'Password must contain a special character';
        return '';

      case 'confirmPassword':
        if (!value) return 'Please confirm password';
        if (value !== formData.password) return 'Passwords do not match';
        return '';

      case 'firstName':
        if (!value.trim()) return 'First name is required';
        return '';

      case 'lastName':
        if (!value.trim()) return 'Last name is required';
        return '';

      case 'country':
        if (!value) return 'Please select a country';
        return '';

      case 'age':
        if (!value) return 'Age is required';
        if (parseInt(value) < 18) return 'You must be at least 18 years old';
        return '';

      case 'terms':
        if (!value) return 'You must accept the terms and conditions';
        return '';

      default:
        return '';
    }
  };

  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    const newValue = type === 'checkbox' ? checked : value;
    
    setFormData(prev => ({ ...prev, [name]: newValue }));

    if (touched[name]) {
      const error = validate(name, newValue);
      setErrors(prev => ({ ...prev, [name]: error }));
    }
  };

  const handleBlur = (e) => {
    const { name, value, type, checked } = e.target;
    const newValue = type === 'checkbox' ? checked : value;
    
    setTouched(prev => ({ ...prev, [name]: true }));
    const error = validate(name, newValue);
    setErrors(prev => ({ ...prev, [name]: error }));
  };

  const isFormValid = () => {
    const requiredFields = ['email', 'password', 'confirmPassword', 'firstName', 'lastName', 'country', 'age', 'terms'];
    return requiredFields.every(field => {
      const value = formData[field];
      return value && !validate(field, value);
    });
  };

  const handleSubmit = (e) => {
    e.preventDefault();

    // Validate all fields
    const newErrors = {};
    const newTouched = {};
    Object.keys(formData).forEach(key => {
      newTouched[key] = true;
      const error = validate(key, formData[key]);
      if (error) newErrors[key] = error;
    });

    setTouched(newTouched);
    setErrors(newErrors);

    if (Object.keys(newErrors).length === 0) {
      console.log('Registration data:', formData);
      setSubmitted(true);
      
      // Reset form after 3 seconds
      setTimeout(() => {
        setFormData({
          email: '', password: '', confirmPassword: '',
          firstName: '', lastName: '', country: '', age: '',
          terms: false, newsletter: false
        });
        setErrors({});
        setTouched({});
        setSubmitted(false);
      }, 3000);
    }
  };

  if (submitted) {
    return (
      <div className="success-message">
        <h2>✓ Registration Successful!</h2>
        <p>Welcome, {formData.firstName} {formData.lastName}!</p>
      </div>
    );
  }

  const passwordStrength = formData.password ? getPasswordStrength(formData.password) : null;

  return (
    <form onSubmit={handleSubmit} className="registration-form">
      <h2>Register</h2>

      <div className="form-row">
        <div className="form-group">
          <label htmlFor="firstName">First Name *</label>
          <input
            id="firstName"
            name="firstName"
            type="text"
            value={formData.firstName}
            onChange={handleChange}
            onBlur={handleBlur}
            className={touched.firstName && errors.firstName ? 'error' : ''}
          />
          {touched.firstName && errors.firstName && (
            <span className="error-message">{errors.firstName}</span>
          )}
        </div>

        <div className="form-group">
          <label htmlFor="lastName">Last Name *</label>
          <input
            id="lastName"
            name="lastName"
            type="text"
            value={formData.lastName}
            onChange={handleChange}
            onBlur={handleBlur}
            className={touched.lastName && errors.lastName ? 'error' : ''}
          />
          {touched.lastName && errors.lastName && (
            <span className="error-message">{errors.lastName}</span>
          )}
        </div>
      </div>

      <div className="form-group">
        <label htmlFor="email">Email *</label>
        <input
          id="email"
          name="email"
          type="email"
          value={formData.email}
          onChange={handleChange}
          onBlur={handleBlur}
          className={touched.email && errors.email ? 'error' : ''}
        />
        {touched.email && errors.email && (
          <span className="error-message">{errors.email}</span>
        )}
      </div>

      <div className="form-group">
        <label htmlFor="password">Password *</label>
        <input
          id="password"
          name="password"
          type="password"
          value={formData.password}
          onChange={handleChange}
          onBlur={handleBlur}
          className={touched.password && errors.password ? 'error' : ''}
        />
        {passwordStrength && (
          <div className="password-strength">
            Strength: <span style={{ color: passwordStrength.color }}>
              {passwordStrength.level}
            </span>
          </div>
        )}
        {touched.password && errors.password && (
          <span className="error-message">{errors.password}</span>
        )}
      </div>

      <div className="form-group">
        <label htmlFor="confirmPassword">Confirm Password *</label>
        <input
          id="confirmPassword"
          name="confirmPassword"
          type="password"
          value={formData.confirmPassword}
          onChange={handleChange}
          onBlur={handleBlur}
          className={touched.confirmPassword && errors.confirmPassword ? 'error' : ''}
        />
        {touched.confirmPassword && errors.confirmPassword && (
          <span className="error-message">{errors.confirmPassword}</span>
        )}
      </div>

      <div className="form-row">
        <div className="form-group">
          <label htmlFor="country">Country *</label>
          <select
            id="country"
            name="country"
            value={formData.country}
            onChange={handleChange}
            onBlur={handleBlur}
            className={touched.country && errors.country ? 'error' : ''}
          >
            <option value="">Select a country</option>
            {countries.map(country => (
              <option key={country.code} value={country.code}>
                {country.name}
              </option>
            ))}
          </select>
          {touched.country && errors.country && (
            <span className="error-message">{errors.country}</span>
          )}
        </div>

        <div className="form-group">
          <label htmlFor="age">Age *</label>
          <input
            id="age"
            name="age"
            type="number"
            min="1"
            max="120"
            value={formData.age}
            onChange={handleChange}
            onBlur={handleBlur}
            className={touched.age && errors.age ? 'error' : ''}
          />
          {touched.age && errors.age && (
            <span className="error-message">{errors.age}</span>
          )}
        </div>
      </div>

      <div className="form-group checkbox-group">
        <label>
          <input
            name="terms"
            type="checkbox"
            checked={formData.terms}
            onChange={handleChange}
            onBlur={handleBlur}
          />
          I accept the terms and conditions *
        </label>
        {touched.terms && errors.terms && (
          <span className="error-message">{errors.terms}</span>
        )}
      </div>

      <div className="form-group checkbox-group">
        <label>
          <input
            name="newsletter"
            type="checkbox"
            checked={formData.newsletter}
            onChange={handleChange}
          />
          Subscribe to newsletter
        </label>
      </div>

      <button type="submit" disabled={!isFormValid()}>
        Register
      </button>
    </form>
  );
}

export default ComplexRegistrationForm;
```

---

### Solution to Problem 2: Dynamic Survey Form

```jsx
// DynamicSurveyForm.jsx
import { useState } from 'react';

function DynamicSurveyForm() {
  const [questions, setQuestions] = useState([
    {
      id: 1,
      text: 'What is your name?',
      type: 'text',
      options: []
    }
  ]);

  const addQuestion = () => {
    const newQuestion = {
      id: Date.now(),
      text: '',
      type: 'text',
      options: []
    };
    setQuestions([...questions, newQuestion]);
  };

  const removeQuestion = (id) => {
    setQuestions(questions.filter(q => q.id !== id));
  };

  const updateQuestion = (id, field, value) => {
    setQuestions(questions.map(q =>
      q.id === id ? { ...q, [field]: value } : q
    ));
  };

  const addOption = (questionId) => {
    setQuestions(questions.map(q =>
      q.id === questionId
        ? { ...q, options: [...q.options, ''] }
        : q
    ));
  };

  const updateOption = (questionId, optionIndex, value) => {
    setQuestions(questions.map(q =>
      q.id === questionId
        ? {
            ...q,
            options: q.options.map((opt, idx) =>
              idx === optionIndex ? value : opt
            )
          }
        : q
    ));
  };

  const removeOption = (questionId, optionIndex) => {
    setQuestions(questions.map(q =>
      q.id === questionId
        ? { ...q, options: q.options.filter((_, idx) => idx !== optionIndex) }
        : q
    ));
  };

  const moveQuestion = (index, direction) => {
    const newQuestions = [...questions];
    const targetIndex = direction === 'up' ? index - 1 : index + 1;
    
    if (targetIndex < 0 || targetIndex >= questions.length) return;
    
    [newQuestions[index], newQuestions[targetIndex]] = 
    [newQuestions[targetIndex], newQuestions[index]];
    
    setQuestions(newQuestions);
  };

  const exportToJSON = () => {
    const surveyData = {
      title: 'My Survey',
      questions: questions.map(q => ({
        text: q.text,
        type: q.type,
        options: q.type !== 'text' ? q.options : undefined
      }))
    };
    console.log(JSON.stringify(surveyData, null, 2));
    alert('Survey exported to console!');
  };

  return (
    <div className="survey-builder">
      <h2>Survey Builder</h2>

      {questions.map((question, index) => (
        <div key={question.id} className="question-card">
          <div className="question-header">
            <h4>Question {index + 1}</h4>
            <div className="question-controls">
              <button
                onClick={() => moveQuestion(index, 'up')}
                disabled={index === 0}
              >
                ↑
              </button>
              <button
                onClick={() => moveQuestion(index, 'down')}
                disabled={index === questions.length - 1}
              >
                ↓
              </button>
              <button onClick={() => removeQuestion(question.id)}>
                Remove
              </button>
            </div>
          </div>

          <input
            type="text"
            value={question.text}
            onChange={(e) => updateQuestion(question.id, 'text', e.target.value)}
            placeholder="Enter question text"
            style={{ width: '100%', marginBottom: '10px' }}
          />

          <select
            value={question.type}
            onChange={(e) => updateQuestion(question.id, 'type', e.target.value)}
          >
            <option value="text">Text</option>
            <option value="radio">Radio</option>
            <option value="checkbox">Checkbox</option>
          </select>

          {(question.type === 'radio' || question.type === 'checkbox') && (
            <div className="options-section">
              <h5>Options:</h5>
              {question.options.map((option, optIdx) => (
                <div key={optIdx}>
                  <input
                    type="text"
                    value={option}
                    onChange={(e) => updateOption(question.id, optIdx, e.target.value)}
                    placeholder={`Option ${optIdx + 1}`}
                  />
                  <button onClick={() => removeOption(question.id, optIdx)}>
                    Remove
                  </button>
                </div>
              ))}
              <button onClick={() => addOption(question.id)}>
                Add Option
              </button>
            </div>
          )}
        </div>
      ))}

      <div className="actions">
        <button onClick={addQuestion}>Add Question</button>
        <button onClick={exportToJSON}>Export to JSON</button>
      </div>
    </div>
  );
}

export default DynamicSurveyForm;
```

---

### Solution to Problem 3: File Upload with Preview

(See Example 8 above - this meets all requirements)

---

### Solution to Problem 4: Search Filter Form

```jsx
// SearchFilterForm.jsx
import { useState, useEffect } from 'react';

function SearchFilterForm() {
  const [filters, setFilters] = useState({
    search: '',
    category: 'all',
    minPrice: '',
    maxPrice: '',
    rating: [],
    inStock: false
  });

  const [debouncedSearch, setDebouncedSearch] = useState('');
  const [products] = useState([
    { id: 1, name: 'Laptop', category: 'electronics', price: 999, rating: 4, inStock: true },
    { id: 2, name: 'T-Shirt', category: 'clothing', price: 29, rating: 5, inStock: true },
    { id: 3, name: 'React Book', category: 'books', price: 45, rating: 4, inStock: false },
    { id: 4, name: 'Mouse', category: 'electronics', price: 25, rating: 3, inStock: true },
    { id: 5, name: 'Jeans', category: 'clothing', price: 79, rating: 4, inStock: true },
  ]);

  // Debounce search
  useEffect(() => {
    const timeoutId = setTimeout(() => {
      setDebouncedSearch(filters.search);
    }, 500);
    return () => clearTimeout(timeoutId);
  }, [filters.search]);

  const updateFilter = (key, value) => {
    setFilters(prev => ({ ...prev, [key]: value }));
  };

  const toggleRating = (rating) => {
    setFilters(prev => ({
      ...prev,
      rating: prev.rating.includes(rating)
        ? prev.rating.filter(r => r !== rating)
        : [...prev.rating, rating]
    }));
  };

  const clearFilters = () => {
    setFilters({
      search: '',
      category: 'all',
      minPrice: '',
      maxPrice: '',
      rating: [],
      inStock: false
    });
  };

  const filteredProducts = products.filter(product => {
    // Search filter
    if (debouncedSearch && !product.name.toLowerCase().includes(debouncedSearch.toLowerCase())) {
      return false;
    }

    // Category filter
    if (filters.category !== 'all' && product.category !== filters.category) {
      return false;
    }

    // Price filters
    if (filters.minPrice && product.price < parseFloat(filters.minPrice)) {
      return false;
    }
    if (filters.maxPrice && product.price > parseFloat(filters.maxPrice)) {
      return false;
    }

    // Rating filter
    if (filters.rating.length > 0 && !filters.rating.includes(product.rating)) {
      return false;
    }

    // In stock filter
    if (filters.inStock && !product.inStock) {
      return false;
    }

    return true;
  });

  const activeFilterCount = [
    filters.search,
    filters.category !== 'all',
    filters.minPrice,
    filters.maxPrice,
    filters.rating.length > 0,
    filters.inStock
  ].filter(Boolean).length;

  return (
    <div className="search-filter-form">
      <h2>Product Search</h2>

      <div className="filters">
        <input
          type="text"
          value={filters.search}
          onChange={(e) => updateFilter('search', e.target.value)}
          placeholder="Search products..."
        />

        <select
          value={filters.category}
          onChange={(e) => updateFilter('category', e.target.value)}
        >
          <option value="all">All Categories</option>
          <option value="electronics">Electronics</option>
          <option value="clothing">Clothing</option>
          <option value="books">Books</option>
        </select>

        <div className="price-range">
          <input
            type="number"
            value={filters.minPrice}
            onChange={(e) => updateFilter('minPrice', e.target.value)}
            placeholder="Min Price"
          />
          <input
            type="number"
            value={filters.maxPrice}
            onChange={(e) => updateFilter('maxPrice', e.target.value)}
            placeholder="Max Price"
          />
        </div>

        <div className="rating-filter">
          <label>Rating:</label>
          {[5, 4, 3, 2, 1].map(rating => (
            <label key={rating}>
              <input
                type="checkbox"
                checked={filters.rating.includes(rating)}
                onChange={() => toggleRating(rating)}
              />
              {rating} ⭐
            </label>
          ))}
        </div>

        <label>
          <input
            type="checkbox"
            checked={filters.inStock}
            onChange={(e) => updateFilter('inStock', e.target.checked)}
          />
          In Stock Only
        </label>

        <button onClick={clearFilters}>
          Clear All Filters ({activeFilterCount} active)
        </button>
      </div>

      <div className="results">
        <h3>Results ({filteredProducts.length})</h3>
        {filteredProducts.map(product => (
          <div key={product.id} className="product-item">
            <h4>{product.name}</h4>
            <p>Category: {product.category}</p>
            <p>Price: ${product.price}</p>
            <p>Rating: {product.rating} ⭐</p>
            <p>{product.inStock ? '✓ In Stock' : '✗ Out of Stock'}</p>
          </div>
        ))}
      </div>
    </div>
  );
}

export default SearchFilterForm;
```

---

### Solution to Problem 5: Form with useReducer

(See Example 7 above - this demonstrates useReducer pattern with forms)
