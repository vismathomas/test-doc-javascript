# React Advanced Patterns

## Context API and State Management

### Advanced Context Patterns

```javascript
import React, { createContext, useContext, useReducer, useCallback } from 'react';

// Define action types
const ActionTypes = {
  LOGIN: 'LOGIN',
  LOGOUT: 'LOGOUT',
  UPDATE_PROFILE: 'UPDATE_PROFILE'
};

// Reducer
const authReducer = (state, action) => {
  switch (action.type) {
    case ActionTypes.LOGIN:
      return {
        ...state,
        isAuthenticated: true,
        user: action.payload
      };
    case ActionTypes.LOGOUT:
      return {
        ...state,
        isAuthenticated: false,
        user: null
      };
    case ActionTypes.UPDATE_PROFILE:
      return {
        ...state,
        user: { ...state.user, ...action.payload }
      };
    default:
      return state;
  }
};

// Create separate contexts for state and dispatch
const AuthStateContext = createContext();
const AuthDispatchContext = createContext();

// Provider component
export function AuthProvider({ children }) {
  const [state, dispatch] = useReducer(authReducer, {
    isAuthenticated: false,
    user: null
  });
  
  return (
    <AuthStateContext.Provider value={state}>
      <AuthDispatchContext.Provider value={dispatch}>
        {children}
      </AuthDispatchContext.Provider>
    </AuthStateContext.Provider>
  );
}

// Custom hooks for using auth context
export function useAuthState() {
  const context = useContext(AuthStateContext);
  if (context === undefined) {
    throw new Error('useAuthState must be used within an AuthProvider');
  }
  return context;
}

export function useAuthDispatch() {
  const context = useContext(AuthDispatchContext);
  if (context === undefined) {
    throw new Error('useAuthDispatch must be used within an AuthProvider');
  }
  return context;
}

// Action creators
export function useAuthActions() {
  const dispatch = useAuthDispatch();
  
  const login = useCallback((user) => {
    dispatch({ type: ActionTypes.LOGIN, payload: user });
  }, [dispatch]);
  
  const logout = useCallback(() => {
    dispatch({ type: ActionTypes.LOGOUT });
  }, [dispatch]);
  
  const updateProfile = useCallback((updates) => {
    dispatch({ type: ActionTypes.UPDATE_PROFILE, payload: updates });
  }, [dispatch]);
  
  return { login, logout, updateProfile };
}

// Usage
function App() {
  return (
    <AuthProvider>
      <Dashboard />
    </AuthProvider>
  );
}

function Dashboard() {
  const { isAuthenticated, user } = useAuthState();
  const { login, logout } = useAuthActions();
  
  return (
    <div>
      {isAuthenticated ? (
        <>
          <h1>Welcome, {user.name}!</h1>
          <button onClick={logout}>Logout</button>
        </>
      ) : (
        <button onClick={() => login({ name: 'John', id: 1 })}>
          Login
        </button>
      )}
    </div>
  );
}
```

## Higher-Order Components (HOC)

HOCs are functions that take a component and return a new component with additional props or behavior.

```javascript
// withAuth HOC
function withAuth(Component) {
  return function AuthenticatedComponent(props) {
    const { isAuthenticated } = useAuthState();
    
    if (!isAuthenticated) {
      return <Navigate to="/login" />;
    }
    
    return <Component {...props} />;
  };
}

// withLoading HOC
function withLoading(Component) {
  return function LoadingComponent({ isLoading, ...props }) {
    if (isLoading) {
      return <div>Loading...</div>;
    }
    
    return <Component {...props} />;
  };
}

// Usage
const ProtectedDashboard = withAuth(Dashboard);
const DashboardWithLoading = withLoading(Dashboard);

// Composing multiple HOCs
const EnhancedDashboard = withAuth(withLoading(Dashboard));
```

## Render Props Pattern

The render props pattern shares code between components using a prop whose value is a function.

```javascript
// Mouse tracker with render props
class Mouse extends React.Component {
  state = { x: 0, y: 0 };
  
  handleMouseMove = (event) => {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  };
  
  render() {
    return (
      <div style={{ height: '100vh' }} onMouseMove={this.handleMouseMove}>
        {this.props.render(this.state)}
      </div>
    );
  }
}

// Usage
function App() {
  return (
    <Mouse render={({ x, y }) => (
      <h1>The mouse position is ({x}, {y})</h1>
    )} />
  );
}

// Alternative: children as a function
function DataProvider({ url, children }) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(data => {
        setData(data);
        setLoading(false);
      });
  }, [url]);
  
  return children({ data, loading });
}

// Usage
<DataProvider url="/api/users">
  {({ data, loading }) => (
    loading ? <Spinner /> : <UserList users={data} />
  )}
</DataProvider>
```

## Compound Components Pattern

Compound components work together to share implicit state.

```javascript
// Tabs compound component
const TabsContext = createContext();

function Tabs({ children, defaultValue }) {
  const [activeTab, setActiveTab] = useState(defaultValue);
  
  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      <div className="tabs">{children}</div>
    </TabsContext.Provider>
  );
}

function TabList({ children }) {
  return <div className="tab-list">{children}</div>;
}

function Tab({ value, children }) {
  const { activeTab, setActiveTab } = useContext(TabsContext);
  const isActive = activeTab === value;
  
  return (
    <button
      className={`tab ${isActive ? 'active' : ''}`}
      onClick={() => setActiveTab(value)}
    >
      {children}
    </button>
  );
}

function TabPanel({ value, children }) {
  const { activeTab } = useContext(TabsContext);
  
  if (activeTab !== value) return null;
  
  return <div className="tab-panel">{children}</div>;
}

// Attach components to Tabs
Tabs.List = TabList;
Tabs.Tab = Tab;
Tabs.Panel = TabPanel;

// Usage
function App() {
  return (
    <Tabs defaultValue="tab1">
      <Tabs.List>
        <Tabs.Tab value="tab1">Tab 1</Tabs.Tab>
        <Tabs.Tab value="tab2">Tab 2</Tabs.Tab>
        <Tabs.Tab value="tab3">Tab 3</Tabs.Tab>
      </Tabs.List>
      
      <Tabs.Panel value="tab1">
        <h2>Content 1</h2>
      </Tabs.Panel>
      <Tabs.Panel value="tab2">
        <h2>Content 2</h2>
      </Tabs.Panel>
      <Tabs.Panel value="tab3">
        <h2>Content 3</h2>
      </Tabs.Panel>
    </Tabs>
  );
}
```

## Performance Optimization

### React.memo

Memoize components to prevent unnecessary re-renders:

```javascript
const ExpensiveComponent = React.memo(function ExpensiveComponent({ data }) {
  console.log('Rendering ExpensiveComponent');
  return <div>{data}</div>;
});

// With custom comparison function
const UserCard = React.memo(
  function UserCard({ user }) {
    return (
      <div>
        <h2>{user.name}</h2>
        <p>{user.email}</p>
      </div>
    );
  },
  (prevProps, nextProps) => {
    // Return true if props are equal (skip re-render)
    return prevProps.user.id === nextProps.user.id;
  }
);
```

### useMemo and useCallback

```javascript
function OptimizedComponent({ items, filter }) {
  // Memoize expensive calculations
  const filteredItems = useMemo(() => {
    return items.filter(item => item.name.includes(filter));
  }, [items, filter]);
  
  // Memoize callback functions
  const handleClick = useCallback((id) => {
    console.log('Clicked item:', id);
  }, []);
  
  return (
    <ul>
      {filteredItems.map(item => (
        <ListItem
          key={item.id}
          item={item}
          onClick={handleClick}
        />
      ))}
    </ul>
  );
}

const ListItem = React.memo(({ item, onClick }) => {
  return (
    <li onClick={() => onClick(item.id)}>
      {item.name}
    </li>
  );
});
```

### Code Splitting and Lazy Loading

```javascript
import React, { lazy, Suspense } from 'react';

// Lazy load components
const Dashboard = lazy(() => import('./Dashboard'));
const Settings = lazy(() => import('./Settings'));
const Profile = lazy(() => import('./Profile'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Routes>
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/settings" element={<Settings />} />
        <Route path="/profile" element={<Profile />} />
      </Routes>
    </Suspense>
  );
}

// Lazy loading with error boundary
function LazyComponent() {
  return (
    <ErrorBoundary>
      <Suspense fallback={<Spinner />}>
        <HeavyComponent />
      </Suspense>
    </ErrorBoundary>
  );
}
```

### Virtualization

For long lists, use virtualization:

```javascript
import { FixedSizeList } from 'react-window';

function VirtualizedList({ items }) {
  const Row = ({ index, style }) => (
    <div style={style}>
      {items[index].name}
    </div>
  );
  
  return (
    <FixedSizeList
      height={600}
      itemCount={items.length}
      itemSize={35}
      width="100%"
    >
      {Row}
    </FixedSizeList>
  );
}
```

## Error Boundaries

Error boundaries catch JavaScript errors anywhere in their child component tree.

```javascript
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }
  
  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }
  
  componentDidCatch(error, errorInfo) {
    console.error('Error caught:', error, errorInfo);
    // Log to error reporting service
  }
  
  render() {
    if (this.state.hasError) {
      return (
        <div>
          <h1>Something went wrong</h1>
          <p>{this.state.error?.message}</p>
          <button onClick={() => this.setState({ hasError: false })}>
            Try again
          </button>
        </div>
      );
    }
    
    return this.props.children;
  }
}

// Usage
function App() {
  return (
    <ErrorBoundary>
      <MyComponent />
    </ErrorBoundary>
  );
}

// Functional error boundary (using react-error-boundary)
import { ErrorBoundary } from 'react-error-boundary';

function ErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div role="alert">
      <p>Something went wrong:</p>
      <pre>{error.message}</pre>
      <button onClick={resetErrorBoundary}>Try again</button>
    </div>
  );
}

function App() {
  return (
    <ErrorBoundary
      FallbackComponent={ErrorFallback}
      onReset={() => {
        // Reset app state
      }}
    >
      <MyComponent />
    </ErrorBoundary>
  );
}
```

## Portals

Portals provide a way to render children into a DOM node outside of the parent component's DOM hierarchy.

```javascript
import ReactDOM from 'react-dom';

function Modal({ isOpen, onClose, children }) {
  if (!isOpen) return null;
  
  return ReactDOM.createPortal(
    <div className="modal-overlay" onClick={onClose}>
      <div className="modal-content" onClick={(e) => e.stopPropagation()}>
        <button onClick={onClose}>Close</button>
        {children}
      </div>
    </div>,
    document.getElementById('modal-root')
  );
}

// Usage
function App() {
  const [isOpen, setIsOpen] = useState(false);
  
  return (
    <div>
      <button onClick={() => setIsOpen(true)}>Open Modal</button>
      <Modal isOpen={isOpen} onClose={() => setIsOpen(false)}>
        <h2>Modal Content</h2>
        <p>This is rendered in a portal</p>
      </Modal>
    </div>
  );
}
```

## Refs and Forwarding Refs

### useImperativeHandle

Customize the instance value exposed to parent components when using ref:

```javascript
import React, { forwardRef, useImperativeHandle, useRef } from 'react';

const FancyInput = forwardRef((props, ref) => {
  const inputRef = useRef();
  
  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    },
    scrollIntoView: () => {
      inputRef.current.scrollIntoView();
    },
    getValue: () => {
      return inputRef.current.value;
    }
  }));
  
  return <input ref={inputRef} {...props} />;
});

// Usage
function App() {
  const inputRef = useRef();
  
  const handleClick = () => {
    inputRef.current.focus();
    console.log(inputRef.current.getValue());
  };
  
  return (
    <div>
      <FancyInput ref={inputRef} />
      <button onClick={handleClick}>Focus Input</button>
    </div>
  );
}
```

## Concurrent Features (React 18+)

### Transitions

```javascript
import { useState, useTransition } from 'react';

function SearchComponent() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);
  const [isPending, startTransition] = useTransition();
  
  const handleChange = (e) => {
    setQuery(e.target.value);
    
    // Mark expensive updates as transitions
    startTransition(() => {
      const filteredResults = expensiveSearch(e.target.value);
      setResults(filteredResults);
    });
  };
  
  return (
    <div>
      <input value={query} onChange={handleChange} />
      {isPending && <Spinner />}
      <ResultsList results={results} />
    </div>
  );
}
```

### useDeferredValue

```javascript
import { useState, useDeferredValue, useMemo } from 'react';

function SearchResults({ query }) {
  const deferredQuery = useDeferredValue(query);
  
  const results = useMemo(() => {
    return expensiveSearch(deferredQuery);
  }, [deferredQuery]);
  
  return (
    <div>
      {results.map(result => (
        <div key={result.id}>{result.name}</div>
      ))}
    </div>
  );
}
```

## Custom Hook Patterns

### useAsync Hook

```javascript
function useAsync(asyncFunction) {
  const [state, setState] = useState({
    loading: false,
    data: null,
    error: null
  });
  
  const execute = useCallback(async (...args) => {
    setState({ loading: true, data: null, error: null });
    
    try {
      const data = await asyncFunction(...args);
      setState({ loading: false, data, error: null });
      return data;
    } catch (error) {
      setState({ loading: false, data: null, error });
      throw error;
    }
  }, [asyncFunction]);
  
  return { ...state, execute };
}

// Usage
function UserProfile({ userId }) {
  const fetchUser = useCallback(
    (id) => fetch(`/api/users/${id}`).then(r => r.json()),
    []
  );
  
  const { data, loading, error, execute } = useAsync(fetchUser);
  
  useEffect(() => {
    execute(userId);
  }, [userId, execute]);
  
  if (loading) return <Spinner />;
  if (error) return <Error error={error} />;
  return <UserDetails user={data} />;
}
```

### useIntersectionObserver Hook

```javascript
function useIntersectionObserver(ref, options = {}) {
  const [isIntersecting, setIsIntersecting] = useState(false);
  
  useEffect(() => {
    const observer = new IntersectionObserver(([entry]) => {
      setIsIntersecting(entry.isIntersecting);
    }, options);
    
    if (ref.current) {
      observer.observe(ref.current);
    }
    
    return () => {
      observer.disconnect();
    };
  }, [ref, options]);
  
  return isIntersecting;
}

// Usage
function LazyImage({ src, alt }) {
  const imgRef = useRef();
  const isVisible = useIntersectionObserver(imgRef, {
    threshold: 0.1
  });
  
  return (
    <div ref={imgRef}>
      {isVisible ? (
        <img src={src} alt={alt} />
      ) : (
        <div>Loading...</div>
      )}
    </div>
  );
}
```

## Best Practices

1. **Separate concerns** - Use multiple contexts for different domains
2. **Optimize re-renders** - Use React.memo, useMemo, and useCallback wisely
3. **Code split** - Lazy load components to reduce initial bundle size
4. **Handle errors gracefully** - Use error boundaries
5. **Use portals for modals** - Render outside component hierarchy
6. **Virtualize long lists** - Use react-window or react-virtualized
7. **Use transitions** - Mark expensive updates as non-urgent
8. **Create reusable patterns** - Extract common patterns into custom hooks
9. **Forward refs when needed** - For library components
10. **Test thoroughly** - Test edge cases and error scenarios
