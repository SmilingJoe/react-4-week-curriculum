# Week 1 - Day 3: Code Examples

## Example 1: Basic useEffect - Component Lifecycle

```jsx
// LifecycleDemo.jsx
import { useState, useEffect } from 'react';

function LifecycleDemo() {
  const [count, setCount] = useState(0);

  // Runs after EVERY render
  useEffect(() => {
    console.log('Effect ran - after every render');
  });

  // Runs ONCE on mount (like componentDidMount)
  useEffect(() => {
    console.log('Component mounted');
  }, []);

  // Runs when count changes
  useEffect(() => {
    console.log(`Count changed to: ${count}`);
  }, [count]);

  // Cleanup on unmount (like componentWillUnmount)
  useEffect(() => {
    console.log('Setting up...');
    
    return () => {
      console.log('Cleaning up...');
    };
  }, []);

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

export default LifecycleDemo;
```

**Vue Equivalent:**
```vue
<script setup>
import { ref, onMounted, onUpdated, onUnmounted, watch } from 'vue';

const count = ref(0);

// Runs once on mount
onMounted(() => {
  console.log('Component mounted');
});

// Runs after every update
onUpdated(() => {
  console.log('Effect ran - after every render');
});

// Runs when count changes
watch(count, (newVal) => {
  console.log(`Count changed to: ${newVal}`);
});

// Cleanup on unmount
onUnmounted(() => {
  console.log('Cleaning up...');
});
</script>
```

---

## Example 2: Document Title Update

```jsx
// DocumentTitle.jsx
import { useState, useEffect } from 'react';

function DocumentTitle() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState('Home');

  useEffect(() => {
    // Update document title
    document.title = `${name} ${count > 0 ? `(${count})` : ''} | My App`;

    // Cleanup: reset title when component unmounts
    return () => {
      document.title = 'My App';
    };
  }, [name, count]); // Re-run when name or count changes

  return (
    <div>
      <h2>Document Title Updater</h2>
      <input 
        value={name} 
        onChange={(e) => setName(e.target.value)}
        placeholder="Page name"
      />
      <button onClick={() => setCount(count + 1)}>
        Notifications: {count}
      </button>
    </div>
  );
}

export default DocumentTitle;
```

**Explanation:**
- Effect runs when `name` or `count` changes
- Cleanup function resets title when component unmounts
- Dependencies array ensures effect only runs when needed

---

## Example 3: Timer with Cleanup

```jsx
// Timer.jsx
import { useState, useEffect } from 'react';

function Timer() {
  const [seconds, setSeconds] = useState(0);
  const [isActive, setIsActive] = useState(false);

  useEffect(() => {
    let interval = null;

    if (isActive) {
      interval = setInterval(() => {
        setSeconds(seconds => seconds + 1);
      }, 1000);
    }

    // Cleanup function
    return () => {
      if (interval) {
        clearInterval(interval);
      }
    };
  }, [isActive]); // Re-run when isActive changes

  const toggle = () => {
    setIsActive(!isActive);
  };

  const reset = () => {
    setSeconds(0);
    setIsActive(false);
  };

  return (
    <div>
      <h2>Timer: {seconds}s</h2>
      <button onClick={toggle}>
        {isActive ? 'Pause' : 'Start'}
      </button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}

export default Timer;
```

**Explanation:**
- setInterval is created when isActive becomes true
- Cleanup function clears interval to prevent memory leaks
- Functional update `setSeconds(seconds => seconds + 1)` ensures correct increment

---

## Example 4: Event Listeners with Cleanup

```jsx
// WindowDimensions.jsx
import { useState, useEffect } from 'react';

function WindowDimensions() {
  const [dimensions, setDimensions] = useState({
    width: window.innerWidth,
    height: window.innerHeight
  });

  useEffect(() => {
    // Event handler
    const handleResize = () => {
      setDimensions({
        width: window.innerWidth,
        height: window.innerHeight
      });
    };

    // Add event listener
    window.addEventListener('resize', handleResize);

    // Cleanup: remove event listener
    return () => {
      window.removeEventListener('resize', handleResize);
    };
  }, []); // Empty array = set up once on mount

  const getDeviceType = () => {
    if (dimensions.width < 768) return 'Mobile';
    if (dimensions.width < 1024) return 'Tablet';
    return 'Desktop';
  };

  return (
    <div>
      <h2>Window Dimensions</h2>
      <p>Width: {dimensions.width}px</p>
      <p>Height: {dimensions.height}px</p>
      <p>Device: {getDeviceType()}</p>
    </div>
  );
}

export default WindowDimensions;
```

**Explanation:**
- Event listener added on mount
- Cleanup removes listener on unmount
- Prevents memory leaks and duplicate listeners
- Empty dependency array because handler function is stable

---

## Example 5: Debounced Resize with Custom Logic

```jsx
// DebouncedResize.jsx
import { useState, useEffect } from 'react';

function DebouncedResize() {
  const [dimensions, setDimensions] = useState({
    width: window.innerWidth,
    height: window.innerHeight
  });

  useEffect(() => {
    let timeoutId = null;

    const handleResize = () => {
      // Clear previous timeout
      if (timeoutId) {
        clearTimeout(timeoutId);
      }

      // Set new timeout
      timeoutId = setTimeout(() => {
        setDimensions({
          width: window.innerWidth,
          height: window.innerHeight
        });
      }, 200); // Debounce by 200ms
    };

    window.addEventListener('resize', handleResize);

    return () => {
      if (timeoutId) {
        clearTimeout(timeoutId);
      }
      window.removeEventListener('resize', handleResize);
    };
  }, []);

  return (
    <div>
      <h2>Debounced Window Size</h2>
      <p>{dimensions.width} x {dimensions.height}</p>
    </div>
  );
}

export default DebouncedResize;
```

**Explanation:**
- Debouncing prevents excessive state updates during resize
- Cleanup clears both timeout and event listener
- Improves performance for expensive operations

---

## Example 6: Data Fetching with useEffect

```jsx
// UserProfile.jsx
import { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    // Reset states when userId changes
    setLoading(true);
    setError(null);

    // Fetch user data
    fetch(`https://jsonplaceholder.typicode.com/users/${userId}`)
      .then(response => {
        if (!response.ok) {
          throw new Error('Failed to fetch user');
        }
        return response.json();
      })
      .then(data => {
        setUser(data);
        setLoading(false);
      })
      .catch(err => {
        setError(err.message);
        setLoading(false);
      });
  }, [userId]); // Re-fetch when userId changes

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!user) return <div>No user found</div>;

  return (
    <div className="user-profile">
      <h2>{user.name}</h2>
      <p>Email: {user.email}</p>
      <p>Phone: {user.phone}</p>
      <p>Website: {user.website}</p>
    </div>
  );
}

export default UserProfile;
```

**Usage:**
```jsx
function App() {
  const [userId, setUserId] = useState(1);

  return (
    <div>
      <button onClick={() => setUserId(userId > 1 ? userId - 1 : 1)}>
        Previous
      </button>
      <button onClick={() => setUserId(userId < 10 ? userId + 1 : 10)}>
        Next
      </button>
      <UserProfile userId={userId} />
    </div>
  );
}
```

---

## Example 7: Async/Await in useEffect

```jsx
// AsyncDataFetch.jsx
import { useState, useEffect } from 'react';

function AsyncDataFetch() {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    // Can't make useEffect callback async directly
    // Create async function inside and call it
    const fetchPosts = async () => {
      try {
        setLoading(true);
        const response = await fetch('https://jsonplaceholder.typicode.com/posts?_limit=5');
        const data = await response.json();
        setPosts(data);
      } catch (error) {
        console.error('Error fetching posts:', error);
      } finally {
        setLoading(false);
      }
    };

    fetchPosts();
  }, []); // Run once on mount

  if (loading) return <div>Loading posts...</div>;

  return (
    <div>
      <h2>Latest Posts</h2>
      {posts.map(post => (
        <div key={post.id} className="post">
          <h3>{post.title}</h3>
          <p>{post.body}</p>
        </div>
      ))}
    </div>
  );
}

export default AsyncDataFetch;
```

**Explanation:**
- Create async function inside useEffect and call it
- Can't make useEffect callback itself async
- Handle errors with try/catch
- Use finally for cleanup actions

---

## Example 8: Fetch with Abort Controller (Cancellation)

```jsx
// CancellableFetch.jsx
import { useState, useEffect } from 'react';

function CancellableFetch({ query }) {
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    // Create AbortController for this effect
    const abortController = new AbortController();

    const searchPosts = async () => {
      try {
        setLoading(true);
        const response = await fetch(
          `https://jsonplaceholder.typicode.com/posts?q=${query}`,
          { signal: abortController.signal } // Pass abort signal
        );
        const data = await response.json();
        setResults(data);
        setLoading(false);
      } catch (error) {
        if (error.name === 'AbortError') {
          console.log('Fetch cancelled');
        } else {
          console.error('Error:', error);
          setLoading(false);
        }
      }
    };

    if (query) {
      searchPosts();
    }

    // Cleanup: cancel fetch if component unmounts or query changes
    return () => {
      abortController.abort();
    };
  }, [query]);

  return (
    <div>
      {loading && <div>Searching...</div>}
      <div>
        {results.map(result => (
          <div key={result.id}>{result.title}</div>
        ))}
      </div>
    </div>
  );
}

export default CancellableFetch;
```

**Explanation:**
- AbortController cancels ongoing fetch requests
- Essential when dependencies change frequently
- Prevents race conditions and memory leaks
- Cleanup function aborts the request

---

## Example 9: LocalStorage Sync

```jsx
// LocalStorageSync.jsx
import { useState, useEffect } from 'react';

function LocalStorageSync() {
  // Initialize from localStorage or default
  const [settings, setSettings] = useState(() => {
    const saved = localStorage.getItem('userSettings');
    return saved ? JSON.parse(saved) : {
      theme: 'light',
      language: 'en',
      notifications: true
    };
  });

  // Sync to localStorage whenever settings change
  useEffect(() => {
    localStorage.setItem('userSettings', JSON.stringify(settings));
    console.log('Settings saved to localStorage');
  }, [settings]);

  const updateSetting = (key, value) => {
    setSettings(prev => ({
      ...prev,
      [key]: value
    }));
  };

  const resetSettings = () => {
    const defaults = {
      theme: 'light',
      language: 'en',
      notifications: true
    };
    setSettings(defaults);
    localStorage.removeItem('userSettings');
  };

  return (
    <div>
      <h2>Settings</h2>
      
      <div>
        <label>
          Theme:
          <select 
            value={settings.theme} 
            onChange={(e) => updateSetting('theme', e.target.value)}
          >
            <option value="light">Light</option>
            <option value="dark">Dark</option>
          </select>
        </label>
      </div>

      <div>
        <label>
          Language:
          <select 
            value={settings.language}
            onChange={(e) => updateSetting('language', e.target.value)}
          >
            <option value="en">English</option>
            <option value="es">Spanish</option>
            <option value="fr">French</option>
          </select>
        </label>
      </div>

      <div>
        <label>
          <input
            type="checkbox"
            checked={settings.notifications}
            onChange={(e) => updateSetting('notifications', e.target.checked)}
          />
          Enable Notifications
        </label>
      </div>

      <button onClick={resetSettings}>Reset to Defaults</button>
      
      <pre>{JSON.stringify(settings, null, 2)}</pre>
    </div>
  );
}

export default LocalStorageSync;
```

**Explanation:**
- Lazy initialization with function in useState
- Sync to localStorage on every settings change
- Handle JSON serialization/deserialization
- Reset functionality clears localStorage

---

## Example 10: Multiple Effects for Separation of Concerns

```jsx
// MultipleEffects.jsx
import { useState, useEffect } from 'react';

function MultipleEffects() {
  const [count, setCount] = useState(0);
  const [user, setUser] = useState(null);

  // Effect 1: Update document title
  useEffect(() => {
    document.title = `Count: ${count}`;
  }, [count]);

  // Effect 2: Fetch user data
  useEffect(() => {
    fetch('https://jsonplaceholder.typicode.com/users/1')
      .then(res => res.json())
      .then(data => setUser(data));
  }, []); // Only on mount

  // Effect 3: Log to console
  useEffect(() => {
    console.log('Component rendered');
  }); // Every render

  // Effect 4: Set up and clean up timer
  useEffect(() => {
    const timer = setInterval(() => {
      console.log('Timer tick');
    }, 5000);

    return () => clearInterval(timer);
  }, []);

  return (
    <div>
      <h2>Multiple Effects Demo</h2>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      {user && <p>User: {user.name}</p>}
    </div>
  );
}

export default MultipleEffects;
```

**Best Practice:**
- Separate effects by concern
- Each effect handles one specific side effect
- Easier to understand, maintain, and debug
- Different dependency arrays for different purposes

---

## Example 11: Effect Dependencies - Common Pitfall

```jsx
// EffectDependencies.jsx
import { useState, useEffect } from 'react';

function EffectDependencies() {
  const [count, setCount] = useState(0);
  const [user, setUser] = useState({ name: 'John', age: 30 });

  // ❌ Missing dependency - ESLint will warn
  useEffect(() => {
    console.log('Count is:', count);
  }, []); // Should include [count]

  // ❌ Object/array in dependency will cause infinite loop
  useEffect(() => {
    console.log('User changed');
  }, [user]); // New object reference every render if not careful

  // ✅ Correct: Include all dependencies
  useEffect(() => {
    console.log(`Count: ${count}, User: ${user.name}`);
  }, [count, user.name]); // Specific properties instead of whole object

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setUser({ ...user, age: user.age + 1 })}>
        Age++
      </button>
    </div>
  );
}
```

**Key Points:**
- Include ALL values used from outer scope in dependencies
- Use ESLint plugin: `eslint-plugin-react-hooks`
- Be careful with objects/arrays (they have new references)
- Consider primitive values or specific properties as dependencies

---

## Example 12: Conditional Effects

```jsx
// ConditionalEffects.jsx
import { useState, useEffect } from 'react';

function ConditionalEffects() {
  const [isOnline, setIsOnline] = useState(true);
  const [serverData, setServerData] = useState(null);

  useEffect(() => {
    // Only set up subscription if online
    if (!isOnline) {
      console.log('Offline - skipping subscription');
      return;
    }

    console.log('Setting up server connection');
    // Simulate server connection
    const interval = setInterval(() => {
      setServerData({ timestamp: Date.now() });
    }, 2000);

    return () => {
      console.log('Cleaning up server connection');
      clearInterval(interval);
    };
  }, [isOnline]); // Re-run when online status changes

  return (
    <div>
      <button onClick={() => setIsOnline(!isOnline)}>
        {isOnline ? 'Go Offline' : 'Go Online'}
      </button>
      <p>Status: {isOnline ? 'Online' : 'Offline'}</p>
      {serverData && <p>Last update: {new Date(serverData.timestamp).toLocaleTimeString()}</p>}
    </div>
  );
}

export default ConditionalEffects;
```

**Explanation:**
- Early return from effect if condition not met
- Cleanup only runs if effect body ran
- Useful for conditional subscriptions or connections
