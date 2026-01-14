# Week 1 - Day 4: Code Examples

## Example 1: Lazy State Initialization

```jsx
// LazyInitialization.jsx
import { useState } from 'react';

// ❌ Bad: Expensive function runs on every render
function BadExample() {
  const expensiveComputation = () => {
    console.log('Computing...');
    let result = 0;
    for (let i = 0; i < 1000000; i++) {
      result += i;
    }
    return result;
  };

  const [value, setValue] = useState(expensiveComputation());
  // expensiveComputation() runs on EVERY render!

  return <div>{value}</div>;
}

// ✅ Good: Function runs only once
function GoodExample() {
  const expensiveComputation = () => {
    console.log('Computing... (only once)');
    let result = 0;
    for (let i = 0; i < 1000000; i++) {
      result += i;
    }
    return result;
  };

  // Pass a function, not the result
  const [value, setValue] = useState(() => expensiveComputation());
  // Function runs only on initial render

  return <div>{value}</div>;
}

// Another example: Reading from localStorage
function UserPreferences() {
  const [preferences, setPreferences] = useState(() => {
    // This only runs once
    const saved = localStorage.getItem('userPreferences');
    return saved ? JSON.parse(saved) : { theme: 'light', lang: 'en' };
  });

  return <div>Theme: {preferences.theme}</div>;
}

export default GoodExample;
```

**Explanation:**
- Lazy initialization: pass a function to useState
- Function executes only on initial mount
- Useful for expensive computations or I/O operations
- Reading from localStorage, sessionStorage, or other APIs

---

## Example 2: Stale Closure Problem and Solution

```jsx
// StaleClosureProblem.jsx
import { useState, useEffect } from 'react';

function StaleClosureProblem() {
  const [count, setCount] = useState(0);

  // ❌ Problematic: Stale closure
  useEffect(() => {
    const timer = setInterval(() => {
      console.log('Count in interval:', count); // Always 0!
      setCount(count + 1); // Always sets to 1
    }, 1000);

    return () => clearInterval(timer);
  }, []); // Empty deps - count is captured from first render

  return <div>Count: {count}</div>;
}

function StaleClosureSolution() {
  const [count, setCount] = useState(0);

  // ✅ Solution 1: Functional update
  useEffect(() => {
    const timer = setInterval(() => {
      setCount(c => {
        console.log('Current count:', c);
        return c + 1; // Uses latest value
      });
    }, 1000);

    return () => clearInterval(timer);
  }, []);

  // ✅ Solution 2: Include count in dependencies
  // useEffect(() => {
  //   const timer = setInterval(() => {
  //     setCount(count + 1);
  //   }, 1000);
  //   return () => clearInterval(timer);
  // }, [count]); // Re-creates interval when count changes

  return <div>Count: {count}</div>;
}

export default StaleClosureSolution;
```

**Explanation:**
- Closures capture variables from their creation scope
- Empty dependency array means variables are captured once
- Use functional updates when new state depends on old state
- Or include dependencies (but may cause other issues)

---

## Example 3: Race Condition in Data Fetching

```jsx
// RaceConditionExample.jsx
import { useState, useEffect } from 'react';

// ❌ Race condition problem
function ProblematicUserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch(`https://jsonplaceholder.typicode.com/users/${userId}`)
      .then(res => res.json())
      .then(data => {
        // If userId changes quickly, older response might arrive last
        setUser(data);
      });
  }, [userId]);

  return user ? <div>{user.name}</div> : <div>Loading...</div>;
}

// ✅ Solution: Ignore stale responses
function SafeUserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    let cancelled = false;
    setLoading(true);

    fetch(`https://jsonplaceholder.typicode.com/users/${userId}`)
      .then(res => res.json())
      .then(data => {
        if (!cancelled) {
          setUser(data);
          setLoading(false);
        }
      })
      .catch(error => {
        if (!cancelled) {
          console.error('Error:', error);
          setLoading(false);
        }
      });

    return () => {
      cancelled = true; // Ignore response if component unmounts or userId changes
    };
  }, [userId]);

  if (loading) return <div>Loading...</div>;
  return user ? <div>{user.name}</div> : <div>No user found</div>;
}

// ✅ Better solution: AbortController
function BestUserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const abortController = new AbortController();
    setLoading(true);

    fetch(`https://jsonplaceholder.typicode.com/users/${userId}`, {
      signal: abortController.signal
    })
      .then(res => res.json())
      .then(data => {
        setUser(data);
        setLoading(false);
      })
      .catch(error => {
        if (error.name !== 'AbortError') {
          console.error('Error:', error);
          setLoading(false);
        }
      });

    return () => {
      abortController.abort(); // Actually cancel the request
    };
  }, [userId]);

  if (loading) return <div>Loading...</div>;
  return user ? <div>{user.name}</div> : <div>No user found</div>;
}

export default BestUserProfile;
```

**Explanation:**
- Race conditions occur when async operations complete out of order
- Solution 1: Use a flag to ignore stale responses
- Solution 2: Use AbortController to actually cancel requests
- Both prevent setting state from outdated async operations

---

## Example 4: Debounced Search Input

```jsx
// DebouncedSearch.jsx
import { useState, useEffect } from 'react';

function DebouncedSearch() {
  const [query, setQuery] = useState('');
  const [debouncedQuery, setDebouncedQuery] = useState('');
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);

  // Debounce the query
  useEffect(() => {
    const timeoutId = setTimeout(() => {
      setDebouncedQuery(query);
    }, 500); // 500ms delay

    return () => clearTimeout(timeoutId);
  }, [query]);

  // Fetch when debounced query changes
  useEffect(() => {
    if (!debouncedQuery) {
      setResults([]);
      return;
    }

    const abortController = new AbortController();
    setLoading(true);

    fetch(
      `https://jsonplaceholder.typicode.com/users?name_like=${debouncedQuery}`,
      { signal: abortController.signal }
    )
      .then(res => res.json())
      .then(data => {
        setResults(data);
        setLoading(false);
      })
      .catch(error => {
        if (error.name !== 'AbortError') {
          console.error('Search error:', error);
          setLoading(false);
        }
      });

    return () => abortController.abort();
  }, [debouncedQuery]);

  return (
    <div className="search">
      <input
        type="text"
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Search users..."
      />
      
      {loading && <div>Searching...</div>}
      
      <ul>
        {results.map(user => (
          <li key={user.id}>
            {user.name} - {user.email}
          </li>
        ))}
      </ul>
      
      {!loading && debouncedQuery && results.length === 0 && (
        <div>No results found</div>
      )}
    </div>
  );
}

export default DebouncedSearch;
```

**Explanation:**
- Two separate effects: one for debouncing, one for fetching
- Debounce effect creates timeout and cleans it up
- Fetch effect runs when debounced value changes
- AbortController cancels pending requests
- Separation of concerns makes code cleaner

---

## Example 5: State Batching Demonstration

```jsx
// StateBatching.jsx
import { useState } from 'react';

function StateBatching() {
  const [count, setCount] = useState(0);
  const [flag, setFlag] = useState(false);
  console.log('Component rendered');

  // React batches updates in event handlers
  const handleClick = () => {
    console.log('Updating state...');
    setCount(c => c + 1);  // These three updates
    setFlag(f => !f);      // are batched into
    setCount(c => c + 1);  // a single re-render
    console.log('State updated');
  };

  // In setTimeout, updates might not be batched (React 17)
  // But in React 18+, they are batched automatically
  const handleAsyncClick = () => {
    setTimeout(() => {
      setCount(c => c + 1);
      setFlag(f => !f);
      setCount(c => c + 1);
    }, 1000);
  };

  return (
    <div>
      <p>Count: {count}</p>
      <p>Flag: {flag ? 'true' : 'false'}</p>
      <button onClick={handleClick}>Sync Update</button>
      <button onClick={handleAsyncClick}>Async Update</button>
      <p>Check console for render count</p>
    </div>
  );
}

export default StateBatching;
```

**Explanation:**
- React 18+ automatically batches all state updates
- Multiple setState calls in same event = one render
- Improves performance by reducing re-renders
- Use functional updates for dependent state changes

---

## Example 6: Avoiding Infinite Loops

```jsx
// AvoidInfiniteLoops.jsx
import { useState, useEffect } from 'react';

// ❌ Infinite loop examples
function InfiniteLoop1() {
  const [count, setCount] = useState(0);

  // Missing dependency array - runs after EVERY render
  useEffect(() => {
    setCount(count + 1); // Triggers re-render, which triggers effect...
  });

  return <div>{count}</div>;
}

function InfiniteLoop2() {
  const [items, setItems] = useState([]);

  // Object/array in dependency causes new reference each render
  useEffect(() => {
    console.log('Effect ran');
    // If items is updated here, effect runs again
  }, [items]); // items array reference changes = effect re-runs

  return <button onClick={() => setItems([...items, 1])}>Add</button>;
}

// ✅ Solutions
function ProperExample1() {
  const [count, setCount] = useState(0);

  // Empty array - runs once
  useEffect(() => {
    console.log('Effect ran once');
  }, []);

  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

function ProperExample2() {
  const [items, setItems] = useState([]);
  const [itemCount, setItemCount] = useState(0);

  // Use primitive dependency
  useEffect(() => {
    console.log(`Item count changed: ${itemCount}`);
  }, [itemCount]); // Primitive value, not array reference

  const addItem = () => {
    setItems([...items, Date.now()]);
    setItemCount(c => c + 1);
  };

  return <button onClick={addItem}>Add Item</button>;
}

export default ProperExample1;
```

**Common Causes:**
1. Effect without dependency array that updates state
2. Effect with object/array dependency that gets recreated
3. Effect that updates its own dependency

**Solutions:**
1. Always use dependency array
2. Use primitive dependencies when possible
3. Consider if you really need the effect

---

## Example 7: Effect Execution Order

```jsx
// EffectExecutionOrder.jsx
import { useState, useEffect } from 'react';

function EffectExecutionOrder() {
  const [count, setCount] = useState(0);

  console.log('1. Component rendering, count:', count);

  useEffect(() => {
    console.log('3. Effect 1 (no deps) - runs after every render');
  });

  useEffect(() => {
    console.log('4. Effect 2 (empty deps) - runs once on mount');
    return () => {
      console.log('Cleanup 2 - runs on unmount');
    };
  }, []);

  useEffect(() => {
    console.log('5. Effect 3 (count dep) - runs when count changes');
    return () => {
      console.log('Cleanup 3 - runs before next effect or unmount');
    };
  }, [count]);

  console.log('2. Component render complete');

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

export default EffectExecutionOrder;
```

**Execution Flow:**
1. Component function executes (render phase)
2. React updates DOM
3. Browser paints screen
4. Effects run in order they're defined
5. On update: cleanup functions run first, then new effects
6. On unmount: all cleanup functions run

---

## Example 8: Complex State Management with useState

```jsx
// ComplexStateManagement.jsx
import { useState } from 'react';

function ComplexStateManagement() {
  // Single object for related data
  const [user, setUser] = useState({
    profile: {
      name: '',
      email: '',
      age: 0
    },
    settings: {
      theme: 'light',
      notifications: true
    },
    preferences: {
      language: 'en',
      timezone: 'UTC'
    }
  });

  // Update nested property
  const updateProfile = (field, value) => {
    setUser(prev => ({
      ...prev,
      profile: {
        ...prev.profile,
        [field]: value
      }
    }));
  };

  const updateSettings = (field, value) => {
    setUser(prev => ({
      ...prev,
      settings: {
        ...prev.settings,
        [field]: value
      }
    }));
  };

  // Reset all to defaults
  const resetUser = () => {
    setUser({
      profile: { name: '', email: '', age: 0 },
      settings: { theme: 'light', notifications: true },
      preferences: { language: 'en', timezone: 'UTC' }
    });
  };

  return (
    <div>
      <h3>Profile</h3>
      <input
        value={user.profile.name}
        onChange={(e) => updateProfile('name', e.target.value)}
        placeholder="Name"
      />
      
      <h3>Settings</h3>
      <select
        value={user.settings.theme}
        onChange={(e) => updateSettings('theme', e.target.value)}
      >
        <option value="light">Light</option>
        <option value="dark">Dark</option>
      </select>

      <button onClick={resetUser}>Reset All</button>
      
      <pre>{JSON.stringify(user, null, 2)}</pre>
    </div>
  );
}

export default ComplexStateManagement;
```

**Best Practices:**
- Keep related data together
- Use spread operator for immutability
- Extract update logic to helper functions
- Consider useReducer for very complex state

---

## Example 9: Multiple useEffect for Different Concerns

```jsx
// MultipleEffects.jsx
import { useState, useEffect } from 'react';

function MultipleEffects() {
  const [user, setUser] = useState(null);
  const [posts, setPosts] = useState([]);
  const [online, setOnline] = useState(navigator.onLine);

  // Effect 1: Fetch user data on mount
  useEffect(() => {
    console.log('Fetching user...');
    fetch('https://jsonplaceholder.typicode.com/users/1')
      .then(res => res.json())
      .then(data => setUser(data))
      .catch(err => console.error('User fetch error:', err));
  }, []);

  // Effect 2: Fetch posts when user is available
  useEffect(() => {
    if (!user) return;

    console.log('Fetching posts for user:', user.id);
    fetch(`https://jsonplaceholder.typicode.com/posts?userId=${user.id}`)
      .then(res => res.json())
      .then(data => setPosts(data))
      .catch(err => console.error('Posts fetch error:', err));
  }, [user]);

  // Effect 3: Monitor online status
  useEffect(() => {
    const handleOnline = () => setOnline(true);
    const handleOffline = () => setOnline(false);

    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);

    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  // Effect 4: Update document title
  useEffect(() => {
    document.title = user ? `${user.name}'s Profile` : 'Loading...';
    
    return () => {
      document.title = 'My App';
    };
  }, [user]);

  // Effect 5: Log analytics
  useEffect(() => {
    if (user && posts.length > 0) {
      console.log('Analytics: User loaded with', posts.length, 'posts');
    }
  }, [user, posts]);

  return (
    <div>
      <div className={online ? 'online' : 'offline'}>
        Status: {online ? 'Online' : 'Offline'}
      </div>
      {user && (
        <>
          <h2>{user.name}</h2>
          <p>Posts: {posts.length}</p>
        </>
      )}
    </div>
  );
}

export default MultipleEffects;
```

**Benefits of Separate Effects:**
- Each effect has a single responsibility
- Easier to understand and debug
- Different dependency arrays for different purposes
- Can add/remove effects independently

---

## Example 10: Derived State (Avoiding Unnecessary Effects)

```jsx
// DerivedState.jsx
import { useState } from 'react';

// ❌ Don't use effect for derived state
function BadDerivedState() {
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');
  const [fullName, setFullName] = useState('');

  useEffect(() => {
    setFullName(`${firstName} ${lastName}`);
  }, [firstName, lastName]);

  return <div>{fullName}</div>;
}

// ✅ Calculate during render
function GoodDerivedState() {
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');

  // Derived state - no effect needed
  const fullName = `${firstName} ${lastName}`.trim();

  return <div>{fullName}</div>;
}

// Another example: filtering
function TodoList() {
  const [todos, setTodos] = useState([]);
  const [filter, setFilter] = useState('all'); // 'all', 'active', 'completed'

  // ❌ Don't do this
  // const [filteredTodos, setFilteredTodos] = useState([]);
  // useEffect(() => {
  //   setFilteredTodos(todos.filter(/* ... */));
  // }, [todos, filter]);

  // ✅ Do this - derive during render
  const filteredTodos = todos.filter(todo => {
    if (filter === 'active') return !todo.completed;
    if (filter === 'completed') return todo.completed;
    return true;
  });

  return (
    <div>
      <select value={filter} onChange={(e) => setFilter(e.target.value)}>
        <option value="all">All</option>
        <option value="active">Active</option>
        <option value="completed">Completed</option>
      </select>
      
      <ul>
        {filteredTodos.map(todo => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
    </div>
  );
}

export default GoodDerivedState;
```

**Key Principle:**
- If you can calculate something from existing state/props, do it during render
- Don't create additional state for derived values
- Effects are for synchronization with external systems, not for data transformation
- Calculating during render is simpler and less error-prone

---

## Example 11: Auto-Save with useEffect

```jsx
// AutoSave.jsx
import { useState, useEffect } from 'react';

function AutoSave() {
  const [content, setContent] = useState('');
  const [lastSaved, setLastSaved] = useState(null);
  const [saving, setSaving] = useState(false);

  // Auto-save effect with debouncing
  useEffect(() => {
    // Don't save empty content
    if (!content.trim()) return;

    setSaving(true);
    const timeoutId = setTimeout(() => {
      // Simulate API call
      console.log('Saving:', content);
      
      // Simulate async save
      setTimeout(() => {
        setLastSaved(new Date());
        setSaving(false);
      }, 500);
    }, 2000); // Wait 2 seconds after user stops typing

    return () => clearTimeout(timeoutId);
  }, [content]);

  return (
    <div>
      <textarea
        value={content}
        onChange={(e) => setContent(e.target.value)}
        placeholder="Start typing... (auto-saves after 2s)"
        rows="10"
        cols="50"
      />
      
      <div className="status">
        {saving && <span>Saving...</span>}
        {!saving && lastSaved && (
          <span>Last saved: {lastSaved.toLocaleTimeString()}</span>
        )}
      </div>
    </div>
  );
}

export default AutoSave;
```

**Explanation:**
- Debounced save: only saves after user stops typing
- Cleanup cancels pending save if content changes again
- Visual feedback for save status
- Common pattern for auto-save features
