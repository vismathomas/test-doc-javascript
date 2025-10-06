# React Basics

## Introduction to React

React is a popular JavaScript library for building user interfaces, particularly single-page applications. It was developed by Facebook and is maintained by Meta and a community of individual developers and companies.

## Key Concepts

### Component-Based Architecture

React applications are built using components - reusable, independent pieces of UI. Components can be composed together to create complex user interfaces.

### Virtual DOM

React uses a virtual DOM to optimize rendering performance. When the state changes, React creates a new virtual DOM tree, compares it with the previous one, and only updates the actual DOM where necessary.

### Declarative UI

React allows you to describe what your UI should look like based on the current state, and React takes care of updating the DOM to match that state.

## Components

### Function Components

Modern React primarily uses function components:

```javascript
import React from 'react';

function Welcome() {
  return <h1>Hello, World!</h1>;
}

export default Welcome;
```

### Components with Props

Props (short for properties) allow you to pass data from parent to child components:

```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}!</h1>;
}

// Usage
<Welcome name="Alice" />
```

### Destructuring Props

```javascript
function UserCard({ name, age, email }) {
  return (
    <div className="user-card">
      <h2>{name}</h2>
      <p>Age: {age}</p>
      <p>Email: {email}</p>
    </div>
  );
}

// Usage
<UserCard name="John" age={30} email="john@example.com" />
```

### Default Props

```javascript
function Button({ text, variant = "primary" }) {
  return <button className={`btn btn-${variant}`}>{text}</button>;
}
```

### Children Prop

```javascript
function Card({ title, children }) {
  return (
    <div className="card">
      <h3>{title}</h3>
      <div className="card-content">
        {children}
      </div>
    </div>
  );
}

// Usage
<Card title="My Card">
  <p>This is the card content</p>
  <button>Click me</button>
</Card>
```

## JSX (JavaScript XML)

JSX is a syntax extension for JavaScript that looks similar to HTML.

### Basic JSX

```javascript
const element = <h1>Hello, World!</h1>;

const user = {
  firstName: 'John',
  lastName: 'Doe'
};

const greeting = (
  <h1>
    Hello, {user.firstName} {user.lastName}!
  </h1>
);
```

### JSX Expressions

```javascript
function Greeting({ name, timeOfDay }) {
  return (
    <div>
      <h1>Good {timeOfDay}, {name}!</h1>
      <p>Current time: {new Date().toLocaleTimeString()}</p>
      <p>2 + 2 = {2 + 2}</p>
    </div>
  );
}
```

### Conditional Rendering

```javascript
// Using && operator
function Notification({ message, isVisible }) {
  return (
    <div>
      {isVisible && <p>{message}</p>}
    </div>
  );
}

// Using ternary operator
function UserStatus({ isLoggedIn }) {
  return (
    <div>
      {isLoggedIn ? <p>Welcome back!</p> : <p>Please log in</p>}
    </div>
  );
}

// Using if-else (outside JSX)
function Dashboard({ user }) {
  if (!user) {
    return <LoginPage />;
  }
  
  return <HomePage user={user} />;
}
```

### Lists and Keys

```javascript
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>
          {todo.text}
        </li>
      ))}
    </ul>
  );
}

// Usage
const todos = [
  { id: 1, text: 'Learn React' },
  { id: 2, text: 'Build a project' },
  { id: 3, text: 'Deploy application' }
];

<TodoList todos={todos} />
```

### JSX Attributes

```javascript
//ClassName instead of class
<div className="container">Content</div>

// camelCase for properties
<input 
  type="text" 
  onChange={handleChange}
  onClick={handleClick}
  maxLength={100}
/>

// Style as object
<div style={{ 
  color: 'blue', 
  fontSize: '16px',
  backgroundColor: 'lightgray' 
}}>
  Styled content
</div>

// Boolean attributes
<input type="checkbox" disabled={true} />
<input type="text" readOnly />
```

## Event Handling

### Handling Events

```javascript
function Button() {
  const handleClick = (event) => {
    event.preventDefault();
    console.log('Button clicked!');
  };
  
  return <button onClick={handleClick}>Click me</button>;
}
```

### Common Events

```javascript
function EventExamples() {
  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Form submitted');
  };
  
  const handleChange = (e) => {
    console.log('Input value:', e.target.value);
  };
  
  const handleKeyPress = (e) => {
    if (e.key === 'Enter') {
      console.log('Enter key pressed');
    }
  };
  
  const handleMouseEnter = () => {
    console.log('Mouse entered');
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        onChange={handleChange}
        onKeyPress={handleKeyPress}
      />
      <div onMouseEnter={handleMouseEnter}>
        Hover over me
      </div>
      <button type="submit">Submit</button>
    </form>
  );
}
```

### Passing Arguments to Event Handlers

```javascript
function ListItem({ id, name, onDelete }) {
  return (
    <div>
      <span>{name}</span>
      <button onClick={() => onDelete(id)}>Delete</button>
    </div>
  );
}

function ItemList() {
  const handleDelete = (id) => {
    console.log(`Deleting item ${id}`);
  };
  
  return (
    <div>
      <ListItem id={1} name="Item 1" onDelete={handleDelete} />
      <ListItem id={2} name="Item 2" onDelete={handleDelete} />
    </div>
  );
}
```

## State Management (Basic)

### useState Hook

```javascript
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setCount(count - 1)}>Decrement</button>
      <button onClick={() => setCount(0)}>Reset</button>
    </div>
  );
}
```

### Multiple State Variables

```javascript
function UserForm() {
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [age, setAge] = useState(0);
  
  const handleSubmit = (e) => {
    e.preventDefault();
    console.log({ name, email, age });
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Name"
      />
      <input
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Email"
      />
      <input
        type="number"
        value={age}
        onChange={(e) => setAge(Number(e.target.value))}
        placeholder="Age"
      />
      <button type="submit">Submit</button>
    </form>
  );
}
```

### State with Objects

```javascript
function UserProfile() {
  const [user, setUser] = useState({
    name: '',
    email: '',
    age: 0
  });
  
  const handleChange = (field, value) => {
    setUser(prevUser => ({
      ...prevUser,
      [field]: value
    }));
  };
  
  return (
    <div>
      <input
        value={user.name}
        onChange={(e) => handleChange('name', e.target.value)}
      />
      <input
        value={user.email}
        onChange={(e) => handleChange('email', e.target.value)}
      />
    </div>
  );
}
```

### State with Arrays

```javascript
function TodoApp() {
  const [todos, setTodos] = useState([]);
  const [input, setInput] = useState('');
  
  const addTodo = () => {
    setTodos([...todos, { id: Date.now(), text: input }]);
    setInput('');
  };
  
  const removeTodo = (id) => {
    setTodos(todos.filter(todo => todo.id !== id));
  };
  
  return (
    <div>
      <input
        value={input}
        onChange={(e) => setInput(e.target.value)}
      />
      <button onClick={addTodo}>Add Todo</button>
      <ul>
        {todos.map(todo => (
          <li key={todo.id}>
            {todo.text}
            <button onClick={() => removeTodo(todo.id)}>Remove</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

## Component Composition

### Composition vs Inheritance

React recommends using composition instead of inheritance to reuse code between components.

```javascript
function Dialog({ title, message, children }) {
  return (
    <div className="dialog">
      <h2>{title}</h2>
      <p>{message}</p>
      <div className="dialog-actions">
        {children}
      </div>
    </div>
  );
}

function ConfirmDialog() {
  return (
    <Dialog 
      title="Confirm Action" 
      message="Are you sure you want to proceed?"
    >
      <button>Yes</button>
      <button>No</button>
    </Dialog>
  );
}
```

### Container and Presentational Components

```javascript
// Presentational Component
function UserList({ users, onUserClick }) {
  return (
    <ul>
      {users.map(user => (
        <li key={user.id} onClick={() => onUserClick(user)}>
          {user.name}
        </li>
      ))}
    </ul>
  );
}

// Container Component
function UserListContainer() {
  const [users, setUsers] = useState([]);
  const [selectedUser, setSelectedUser] = useState(null);
  
  const handleUserClick = (user) => {
    setSelectedUser(user);
    console.log('Selected:', user);
  };
  
  return (
    <div>
      <UserList users={users} onUserClick={handleUserClick} />
      {selectedUser && <p>Selected: {selectedUser.name}</p>}
    </div>
  );
}
```

## PropTypes (Type Checking)

```javascript
import PropTypes from 'prop-types';

function User({ name, age, email, isActive }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>Age: {age}</p>
      <p>Email: {email}</p>
      <p>Status: {isActive ? 'Active' : 'Inactive'}</p>
    </div>
  );
}

User.propTypes = {
  name: PropTypes.string.isRequired,
  age: PropTypes.number.isRequired,
  email: PropTypes.string,
  isActive: PropTypes.bool
};

User.defaultProps = {
  isActive: true
};
```

## Best Practices

1. **Keep components small and focused**
2. **Use meaningful component names**
3. **Extract reusable logic into custom hooks**
4. **Always provide keys when rendering lists**
5. **Use prop destructuring for cleaner code**
6. **Keep state as local as possible**
7. **Use functional components over class components**
8. **Handle errors gracefully with error boundaries**
9. **Use PropTypes or TypeScript for type safety**
10. **Follow consistent naming conventions**
