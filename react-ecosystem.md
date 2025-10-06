# React Ecosystem and Tools

## React Router

React Router is the standard routing library for React applications.

### Basic Setup

```javascript
import { BrowserRouter, Routes, Route, Link, useNavigate } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Link to="/users">Users</Link>
      </nav>
      
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/users" element={<Users />} />
        <Route path="/users/:id" element={<UserDetail />} />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}
```

### Route Parameters

```javascript
import { useParams, useNavigate } from 'react-router-dom';

function UserDetail() {
  const { id } = useParams();
  const navigate = useNavigate();
  
  return (
    <div>
      <h1>User {id}</h1>
      <button onClick={() => navigate('/users')}>
        Back to Users
      </button>
    </div>
  );
}
```

### Nested Routes

```javascript
function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      <nav>
        <Link to="profile">Profile</Link>
        <Link to="settings">Settings</Link>
      </nav>
      
      <Routes>
        <Route path="profile" element={<Profile />} />
        <Route path="settings" element={<Settings />} />
      </Routes>
    </div>
  );
}
```

### Protected Routes

```javascript
function ProtectedRoute({ children }) {
  const { isAuthenticated } = useAuth();
  
  if (!isAuthenticated) {
    return <Navigate to="/login" replace />;
  }
  
  return children;
}

// Usage
<Route
  path="/dashboard"
  element={
    <ProtectedRoute>
      <Dashboard />
    </ProtectedRoute>
  }
/>
```

### Query Parameters

```javascript
import { useSearchParams } from 'react-router-dom';

function SearchPage() {
  const [searchParams, setSearchParams] = useSearchParams();
  
  const query = searchParams.get('q') || '';
  const page = searchParams.get('page') || '1';
  
  const handleSearch = (newQuery) => {
    setSearchParams({ q: newQuery, page: '1' });
  };
  
  return (
    <div>
      <input
        value={query}
        onChange={(e) => handleSearch(e.target.value)}
      />
      <p>Searching for: {query}</p>
      <p>Page: {page}</p>
    </div>
  );
}
```

## State Management Libraries

### Redux Toolkit

Modern Redux with less boilerplate:

```javascript
import { createSlice, configureStore } from '@reduxjs/toolkit';
import { Provider, useSelector, useDispatch } from 'react-redux';

// Create a slice
const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0 },
  reducers: {
    increment: (state) => {
      state.value += 1;
    },
    decrement: (state) => {
      state.value -= 1;
    },
    incrementByAmount: (state, action) => {
      state.value += action.payload;
    }
  }
});

export const { increment, decrement, incrementByAmount } = counterSlice.actions;

// Configure store
const store = configureStore({
  reducer: {
    counter: counterSlice.reducer
  }
});

// Provider
function App() {
  return (
    <Provider store={store}>
      <Counter />
    </Provider>
  );
}

// Component
function Counter() {
  const count = useSelector((state) => state.counter.value);
  const dispatch = useDispatch();
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => dispatch(increment())}>+</button>
      <button onClick={() => dispatch(decrement())}>-</button>
      <button onClick={() => dispatch(incrementByAmount(5))}>+5</button>
    </div>
  );
}
```

### Zustand

A small, fast state management solution:

```javascript
import create from 'zustand';

// Create store
const useStore = create((set) => ({
  count: 0,
  user: null,
  increment: () => set((state) => ({ count: state.count + 1 })),
  decrement: () => set((state) => ({ count: state.count - 1 })),
  setUser: (user) => set({ user }),
  reset: () => set({ count: 0, user: null })
}));

// Use in component
function Counter() {
  const count = useStore((state) => state.count);
  const increment = useStore((state) => state.increment);
  const decrement = useStore((state) => state.decrement);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
    </div>
  );
}
```

### Recoil

Facebook's state management library:

```javascript
import { RecoilRoot, atom, selector, useRecoilState, useRecoilValue } from 'recoil';

// Atom (state)
const countState = atom({
  key: 'countState',
  default: 0
});

// Selector (derived state)
const doubleCountState = selector({
  key: 'doubleCountState',
  get: ({ get }) => {
    const count = get(countState);
    return count * 2;
  }
});

// Provider
function App() {
  return (
    <RecoilRoot>
      <Counter />
    </RecoilRoot>
  );
}

// Component
function Counter() {
  const [count, setCount] = useRecoilState(countState);
  const doubleCount = useRecoilValue(doubleCountState);
  
  return (
    <div>
      <p>Count: {count}</p>
      <p>Double: {doubleCount}</p>
      <button onClick={() => setCount(count + 1)}>+</button>
    </div>
  );
}
```

## Data Fetching

### React Query (TanStack Query)

Powerful data fetching and caching:

```javascript
import { QueryClient, QueryClientProvider, useQuery, useMutation } from '@tanstack/react-query';

const queryClient = new QueryClient();

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Users />
    </QueryClientProvider>
  );
}

function Users() {
  // Fetch data
  const { data, isLoading, error, refetch } = useQuery({
    queryKey: ['users'],
    queryFn: () => fetch('/api/users').then(res => res.json())
  });
  
  // Mutation
  const createUser = useMutation({
    mutationFn: (newUser) => 
      fetch('/api/users', {
        method: 'POST',
        body: JSON.stringify(newUser)
      }),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['users'] });
    }
  });
  
  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  
  return (
    <div>
      <button onClick={() => createUser.mutate({ name: 'John' })}>
        Add User
      </button>
      <ul>
        {data.map(user => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
}
```

### SWR (Stale-While-Revalidate)

React Hooks library for data fetching by Vercel:

```javascript
import useSWR from 'swr';

const fetcher = (url) => fetch(url).then(res => res.json());

function Profile() {
  const { data, error, isLoading, mutate } = useSWR('/api/user', fetcher);
  
  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Failed to load</div>;
  
  return (
    <div>
      <h1>{data.name}</h1>
      <button onClick={() => mutate()}>Refresh</button>
    </div>
  );
}

// With auto-revalidation
function UserList() {
  const { data } = useSWR('/api/users', fetcher, {
    refreshInterval: 3000, // Refresh every 3 seconds
    revalidateOnFocus: true,
    revalidateOnReconnect: true
  });
  
  return (
    <ul>
      {data?.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

## Form Management

### React Hook Form

Performant form library with easy validation:

```javascript
import { useForm } from 'react-hook-form';

function RegistrationForm() {
  const {
    register,
    handleSubmit,
    watch,
    formState: { errors }
  } = useForm();
  
  const onSubmit = (data) => {
    console.log(data);
  };
  
  const password = watch('password');
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input
        {...register('username', {
          required: 'Username is required',
          minLength: {
            value: 3,
            message: 'Username must be at least 3 characters'
          }
        })}
        placeholder="Username"
      />
      {errors.username && <p>{errors.username.message}</p>}
      
      <input
        type="email"
        {...register('email', {
          required: 'Email is required',
          pattern: {
            value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
            message: 'Invalid email address'
          }
        })}
        placeholder="Email"
      />
      {errors.email && <p>{errors.email.message}</p>}
      
      <input
        type="password"
        {...register('password', {
          required: 'Password is required',
          minLength: {
            value: 8,
            message: 'Password must be at least 8 characters'
          }
        })}
        placeholder="Password"
      />
      {errors.password && <p>{errors.password.message}</p>}
      
      <input
        type="password"
        {...register('confirmPassword', {
          required: 'Please confirm password',
          validate: (value) =>
            value === password || 'Passwords do not match'
        })}
        placeholder="Confirm Password"
      />
      {errors.confirmPassword && <p>{errors.confirmPassword.message}</p>}
      
      <button type="submit">Register</button>
    </form>
  );
}
```

### Formik

Popular form library with Yup validation:

```javascript
import { Formik, Form, Field, ErrorMessage } from 'formik';
import * as Yup from 'yup';

const validationSchema = Yup.object({
  username: Yup.string()
    .min(3, 'Must be at least 3 characters')
    .required('Required'),
  email: Yup.string()
    .email('Invalid email address')
    .required('Required'),
  password: Yup.string()
    .min(8, 'Must be at least 8 characters')
    .required('Required')
});

function RegistrationForm() {
  return (
    <Formik
      initialValues={{
        username: '',
        email: '',
        password: ''
      }}
      validationSchema={validationSchema}
      onSubmit={(values, { setSubmitting }) => {
        console.log(values);
        setSubmitting(false);
      }}
    >
      {({ isSubmitting }) => (
        <Form>
          <Field name="username" type="text" placeholder="Username" />
          <ErrorMessage name="username" component="div" />
          
          <Field name="email" type="email" placeholder="Email" />
          <ErrorMessage name="email" component="div" />
          
          <Field name="password" type="password" placeholder="Password" />
          <ErrorMessage name="password" component="div" />
          
          <button type="submit" disabled={isSubmitting}>
            Register
          </button>
        </Form>
      )}
    </Formik>
  );
}
```

## Styling Solutions

### Styled Components

CSS-in-JS with tagged template literals:

```javascript
import styled from 'styled-components';

const Button = styled.button`
  background-color: ${props => props.primary ? 'blue' : 'white'};
  color: ${props => props.primary ? 'white' : 'blue'};
  font-size: 1rem;
  padding: 0.5rem 1rem;
  border: 2px solid blue;
  border-radius: 4px;
  cursor: pointer;
  
  &:hover {
    opacity: 0.8;
  }
`;

const Container = styled.div`
  max-width: 1200px;
  margin: 0 auto;
  padding: 2rem;
`;

function App() {
  return (
    <Container>
      <Button primary>Primary Button</Button>
      <Button>Secondary Button</Button>
    </Container>
  );
}
```

### Emotion

Another popular CSS-in-JS library:

```javascript
import { css } from '@emotion/react';
import styled from '@emotion/styled';

const buttonStyle = css`
  background-color: hotpink;
  color: white;
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 4px;
  cursor: pointer;
`;

const StyledButton = styled.button`
  background-color: ${props => props.bgColor || 'blue'};
  color: white;
  padding: 0.5rem 1rem;
`;

function App() {
  return (
    <div>
      <button css={buttonStyle}>Emotion CSS</button>
      <StyledButton bgColor="green">Styled Button</StyledButton>
    </div>
  );
}
```

### Tailwind CSS

Utility-first CSS framework:

```javascript
function UserCard({ user }) {
  return (
    <div className="max-w-sm rounded overflow-hidden shadow-lg p-6 bg-white">
      <img 
        className="w-full h-48 object-cover"
        src={user.avatar}
        alt={user.name}
      />
      <div className="mt-4">
        <h2 className="text-xl font-bold text-gray-900">{user.name}</h2>
        <p className="text-gray-600 mt-2">{user.bio}</p>
        <button className="mt-4 bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
          Follow
        </button>
      </div>
    </div>
  );
}
```

## Testing

### Jest and React Testing Library

```javascript
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import '@testing-library/jest-dom';
import Counter from './Counter';

describe('Counter', () => {
  test('renders counter with initial value', () => {
    render(<Counter initialValue={0} />);
    expect(screen.getByText(/count: 0/i)).toBeInTheDocument();
  });
  
  test('increments counter when button is clicked', () => {
    render(<Counter initialValue={0} />);
    const button = screen.getByRole('button', { name: /increment/i });
    
    fireEvent.click(button);
    expect(screen.getByText(/count: 1/i)).toBeInTheDocument();
  });
  
  test('handles async operations', async () => {
    render(<UserProfile userId={1} />);
    
    expect(screen.getByText(/loading/i)).toBeInTheDocument();
    
    await waitFor(() => {
      expect(screen.getByText(/john doe/i)).toBeInTheDocument();
    });
  });
  
  test('handles user interactions', async () => {
    const user = userEvent.setup();
    render(<SearchForm />);
    
    const input = screen.getByPlaceholderText(/search/i);
    await user.type(input, 'react');
    
    expect(input).toHaveValue('react');
  });
});
```

### Testing Hooks

```javascript
import { renderHook, act } from '@testing-library/react';
import useCounter from './useCounter';

describe('useCounter', () => {
  test('should use counter', () => {
    const { result } = renderHook(() => useCounter(0));
    
    expect(result.current.count).toBe(0);
    
    act(() => {
      result.current.increment();
    });
    
    expect(result.current.count).toBe(1);
  });
});
```

## Build Tools

### Vite

Next-generation frontend tooling:

```javascript
// vite.config.js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  server: {
    port: 3000
  },
  build: {
    outDir: 'dist'
  }
});
```

### Create React App

Official React setup tool:

```bash
npx create-react-app my-app
cd my-app
npm start
```

### Next.js

React framework with server-side rendering:

```javascript
// pages/index.js
export default function Home({ posts }) {
  return (
    <div>
      <h1>Blog Posts</h1>
      {posts.map(post => (
        <article key={post.id}>
          <h2>{post.title}</h2>
          <p>{post.excerpt}</p>
        </article>
      ))}
    </div>
  );
}

// Server-side rendering
export async function getServerSideProps() {
  const res = await fetch('https://api.example.com/posts');
  const posts = await res.json();
  
  return {
    props: { posts }
  };
}

// Static generation
export async function getStaticProps() {
  const res = await fetch('https://api.example.com/posts');
  const posts = await res.json();
  
  return {
    props: { posts },
    revalidate: 60 // Regenerate page every 60 seconds
  };
}
```

## UI Component Libraries

### Material-UI (MUI)

```javascript
import { Button, TextField, Box, Typography } from '@mui/material';
import { ThemeProvider, createTheme } from '@mui/material/styles';

const theme = createTheme({
  palette: {
    primary: {
      main: '#1976d2'
    }
  }
});

function App() {
  return (
    <ThemeProvider theme={theme}>
      <Box sx={{ p: 3 }}>
        <Typography variant="h4" gutterBottom>
          Welcome
        </Typography>
        <TextField label="Name" variant="outlined" fullWidth />
        <Button variant="contained" color="primary">
          Submit
        </Button>
      </Box>
    </ThemeProvider>
  );
}
```

### Ant Design

```javascript
import { Button, Form, Input, Space } from 'antd';

function LoginForm() {
  const [form] = Form.useForm();
  
  const onFinish = (values) => {
    console.log('Success:', values);
  };
  
  return (
    <Form
      form={form}
      layout="vertical"
      onFinish={onFinish}
    >
      <Form.Item
        label="Username"
        name="username"
        rules={[{ required: true, message: 'Please input your username!' }]}
      >
        <Input />
      </Form.Item>
      
      <Form.Item
        label="Password"
        name="password"
        rules={[{ required: true, message: 'Please input your password!' }]}
      >
        <Input.Password />
      </Form.Item>
      
      <Form.Item>
        <Space>
          <Button type="primary" htmlType="submit">
            Submit
          </Button>
          <Button htmlType="reset">
            Reset
          </Button>
        </Space>
      </Form.Item>
    </Form>
  );
}
```

## Best Practices

1. **Choose the right state management** - Context for simple, Redux/Zustand for complex
2. **Use React Query for data fetching** - Better caching and synchronization
3. **Implement proper error boundaries** - Graceful error handling
4. **Use TypeScript** - Better type safety and developer experience
5. **Follow accessibility guidelines** - Use semantic HTML and ARIA attributes
6. **Optimize bundle size** - Code splitting and tree shaking
7. **Write tests** - Unit, integration, and e2e tests
8. **Use CSS-in-JS or utility frameworks** - Scoped styles and better maintainability
9. **Follow React Router conventions** - Proper route structure
10. **Keep dependencies updated** - Security and performance improvements
