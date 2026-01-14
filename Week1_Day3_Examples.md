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

---

## Solutions to Daily Assignments

### Solution to Problem 1: Document Title Updater

```jsx
// DocumentTitleUpdater.jsx
import { useState, useEffect } from 'react';

function DocumentTitleUpdater() {
  const [pageName, setPageName] = useState('Home');
  const [notificationCount, setNotificationCount] = useState(0);

  // Update document title
  useEffect(() => {
    const titleParts = [];
    
    if (notificationCount > 0) {
      titleParts.push(`(${notificationCount})`);
    }
    
    titleParts.push(pageName);
    titleParts.push('My App');
    
    document.title = titleParts.join(' | ');

    // Cleanup: reset title when component unmounts
    return () => {
      document.title = 'My App';
    };
  }, [pageName, notificationCount]);

  const pages = ['Home', 'Dashboard', 'Profile', 'Settings'];

  return (
    <div className="title-updater">
      <h2>Document Title Updater</h2>
      
      <div className="page-selector">
        <label>Select Page: </label>
        <select value={pageName} onChange={(e) => setPageName(e.target.value)}>
          {pages.map(page => (
            <option key={page} value={page}>{page}</option>
          ))}
        </select>
      </div>

      <div className="notification-controls">
        <p>Notifications: {notificationCount}</p>
        <button onClick={() => setNotificationCount(prev => prev + 1)}>
          Add Notification
        </button>
        <button onClick={() => setNotificationCount(0)}>
          Clear Notifications
        </button>
      </div>

      <p>Current title: {document.title}</p>
    </div>
  );
}

export default DocumentTitleUpdater;
```

---

### Solution to Problem 2: Real-Time Clock Component

```jsx
// RealtimeClock.jsx
import { useState, useEffect } from 'react';

function RealtimeClock() {
  const [currentTime, setCurrentTime] = useState(new Date());
  const [isRunning, setIsRunning] = useState(true);
  const [mountTime] = useState(new Date());

  useEffect(() => {
    let intervalId = null;

    if (isRunning) {
      intervalId = setInterval(() => {
        setCurrentTime(new Date());
      }, 1000);
    }

    // Cleanup function
    return () => {
      if (intervalId) {
        clearInterval(intervalId);
      }
    };
  }, [isRunning]);

  const formatTime = (date) => {
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    const seconds = String(date.getSeconds()).padStart(2, '0');
    return `${hours}:${minutes}:${seconds}`;
  };

  const getElapsedTime = () => {
    const elapsed = Math.floor((currentTime - mountTime) / 1000);
    const hours = Math.floor(elapsed / 3600);
    const minutes = Math.floor((elapsed % 3600) / 60);
    const seconds = elapsed % 60;
    
    return `${String(hours).padStart(2, '0')}:${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`;
  };

  return (
    <div className="realtime-clock">
      <h2>Real-Time Clock</h2>
      
      <div className="clock-display">
        <div className="current-time">
          <h3>Current Time</h3>
          <p style={{ fontSize: '48px', fontFamily: 'monospace' }}>
            {formatTime(currentTime)}
          </p>
        </div>

        <div className="elapsed-time">
          <h3>Elapsed Since Mount</h3>
          <p style={{ fontSize: '32px', fontFamily: 'monospace' }}>
            {getElapsedTime()}
          </p>
        </div>
      </div>

      <div className="controls">
        <button onClick={() => setIsRunning(!isRunning)}>
          {isRunning ? 'Stop' : 'Start'}
        </button>
      </div>
    </div>
  );
}

export default RealtimeClock;
```

---

### Solution to Problem 3: Window Resize Tracker

```jsx
// WindowResizeTracker.jsx
import { useState, useEffect } from 'react';

function WindowResizeTracker() {
  const [dimensions, setDimensions] = useState({
    width: window.innerWidth,
    height: window.innerHeight
  });

  useEffect(() => {
    let timeoutId = null;

    // Debounced resize handler
    const handleResize = () => {
      if (timeoutId) {
        clearTimeout(timeoutId);
      }

      timeoutId = setTimeout(() => {
        setDimensions({
          width: window.innerWidth,
          height: window.innerHeight
        });
      }, 200); // Debounce by 200ms
    };

    // Add event listener
    window.addEventListener('resize', handleResize);

    // Cleanup
    return () => {
      if (timeoutId) {
        clearTimeout(timeoutId);
      }
      window.removeEventListener('resize', handleResize);
    };
  }, []);

  const getDeviceCategory = () => {
    if (dimensions.width < 768) return 'Mobile';
    if (dimensions.width < 1024) return 'Tablet';
    return 'Desktop';
  };

  const getCategoryColor = () => {
    const category = getDeviceCategory();
    if (category === 'Mobile') return '#ff9800';
    if (category === 'Tablet') return '#2196f3';
    return '#4caf50';
  };

  return (
    <div className="resize-tracker">
      <h2>Window Resize Tracker</h2>
      
      <div className="dimensions">
        <p>Width: <strong>{dimensions.width}px</strong></p>
        <p>Height: <strong>{dimensions.height}px</strong></p>
      </div>

      <div 
        className="device-category"
        style={{
          padding: '20px',
          backgroundColor: getCategoryColor(),
          color: 'white',
          borderRadius: '8px',
          marginTop: '20px'
        }}
      >
        <h3>Device Category: {getDeviceCategory()}</h3>
      </div>

      <div className="breakpoints">
        <p><small>Mobile: &lt; 768px | Tablet: 768-1023px | Desktop: ≥ 1024px</small></p>
      </div>
    </div>
  );
}

export default WindowResizeTracker;
```

---

### Solution to Problem 4: Data Fetcher with Loading States

```jsx
// DataFetcher.jsx
import { useState, useEffect } from 'react';

function DataFetcher() {
  const [userId, setUserId] = useState(1);
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  useEffect(() => {
    const abortController = new AbortController();

    const fetchUser = async () => {
      setLoading(true);
      setError(null);

      try {
        const response = await fetch(
          `https://jsonplaceholder.typicode.com/users/${userId}`,
          { signal: abortController.signal }
        );

        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }

        const data = await response.json();
        setUser(data);
      } catch (err) {
        if (err.name !== 'AbortError') {
          setError(err.message);
        }
      } finally {
        if (!abortController.signal.aborted) {
          setLoading(false);
        }
      }
    };

    fetchUser();

    // Cleanup: cancel request if userId changes or component unmounts
    return () => {
      abortController.abort();
    };
  }, [userId]);

  const handlePrevious = () => {
    if (userId > 1) {
      setUserId(userId - 1);
    }
  };

  const handleNext = () => {
    if (userId < 10) {
      setUserId(userId + 1);
    }
  };

  return (
    <div className="data-fetcher">
      <h2>User Data Fetcher</h2>

      <div className="controls">
        <button onClick={handlePrevious} disabled={userId === 1}>
          Previous
        </button>
        <span>User ID: {userId}</span>
        <button onClick={handleNext} disabled={userId === 10}>
          Next
        </button>
      </div>

      {loading && (
        <div className="loading">
          <div className="spinner"></div>
          <p>Loading user data...</p>
        </div>
      )}

      {error && (
        <div className="error">
          <h3>Error</h3>
          <p>{error}</p>
        </div>
      )}

      {!loading && !error && user && (
        <div className="user-data">
          <h3>{user.name}</h3>
          <p><strong>Username:</strong> {user.username}</p>
          <p><strong>Email:</strong> {user.email}</p>
          <p><strong>Phone:</strong> {user.phone}</p>
          <p><strong>Website:</strong> {user.website}</p>
          <p><strong>Company:</strong> {user.company.name}</p>
          <p><strong>Address:</strong> {user.address.street}, {user.address.city}</p>
        </div>
      )}
    </div>
  );
}

export default DataFetcher;
```

---

### Solution to Problem 5: Local Storage Sync

```jsx
// LocalStorageSettings.jsx
import { useState, useEffect } from 'react';

function LocalStorageSettings() {
  const [settings, setSettings] = useState(() => {
    // Load from localStorage on initial mount
    try {
      const saved = localStorage.getItem('appSettings');
      return saved ? JSON.parse(saved) : {
        theme: 'light',
        language: 'en',
        notifications: true
      };
    } catch (error) {
      console.error('Error loading settings:', error);
      return {
        theme: 'light',
        language: 'en',
        notifications: true
      };
    }
  });

  const [showSaved, setShowSaved] = useState(false);

  // Sync to localStorage whenever settings change
  useEffect(() => {
    try {
      localStorage.setItem('appSettings', JSON.stringify(settings));
      
      // Show "Settings saved!" message
      setShowSaved(true);
      const timer = setTimeout(() => {
        setShowSaved(false);
      }, 2000);

      return () => clearTimeout(timer);
    } catch (error) {
      console.error('Error saving settings:', error);
    }
  }, [settings]);

  const updateSetting = (key, value) => {
    setSettings(prev => ({
      ...prev,
      [key]: value
    }));
  };

  const resetToDefaults = () => {
    const defaults = {
      theme: 'light',
      language: 'en',
      notifications: true
    };
    setSettings(defaults);
    try {
      localStorage.removeItem('appSettings');
    } catch (error) {
      console.error('Error clearing localStorage:', error);
    }
  };

  return (
    <div className="settings-panel">
      <h2>Settings</h2>

      {showSaved && (
        <div className="save-notification">
          ✓ Settings saved!
        </div>
      )}

      <div className="setting-group">
        <label htmlFor="theme">Theme:</label>
        <select
          id="theme"
          value={settings.theme}
          onChange={(e) => updateSetting('theme', e.target.value)}
        >
          <option value="light">Light</option>
          <option value="dark">Dark</option>
          <option value="auto">Auto</option>
        </select>
      </div>

      <div className="setting-group">
        <label htmlFor="language">Language:</label>
        <select
          id="language"
          value={settings.language}
          onChange={(e) => updateSetting('language', e.target.value)}
        >
          <option value="en">English</option>
          <option value="es">Spanish</option>
          <option value="fr">French</option>
          <option value="de">German</option>
        </select>
      </div>

      <div className="setting-group">
        <label>
          <input
            type="checkbox"
            checked={settings.notifications}
            onChange={(e) => updateSetting('notifications', e.target.checked)}
          />
          Enable Notifications
        </label>
      </div>

      <button onClick={resetToDefaults} className="reset-button">
        Reset to Defaults
      </button>

      <div className="settings-preview">
        <h3>Current Settings (JSON)</h3>
        <pre>{JSON.stringify(settings, null, 2)}</pre>
      </div>
    </div>
  );
}

export default LocalStorageSettings;
```

**Key Points:**
- Lazy initialization loads from localStorage on mount
- useEffect syncs changes to localStorage
- Try-catch blocks handle localStorage unavailability
- "Settings saved!" message uses state + setTimeout with cleanup
- Reset functionality clears localStorage
- All requirements met with proper error handling
