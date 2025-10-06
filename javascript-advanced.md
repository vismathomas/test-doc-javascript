# JavaScript Advanced Topics

## Asynchronous JavaScript

### Callbacks

A callback is a function passed as an argument to another function, to be executed later.

```javascript
function fetchData(callback) {
  setTimeout(() => {
    const data = { id: 1, name: "John" };
    callback(data);
  }, 1000);
}

fetchData((data) => {
  console.log(data);
});
```

### Promises

Promises represent the eventual completion or failure of an asynchronous operation.

```javascript
// Creating a Promise
const myPromise = new Promise((resolve, reject) => {
  const success = true;
  
  setTimeout(() => {
    if (success) {
      resolve("Operation successful!");
    } else {
      reject("Operation failed!");
    }
  }, 1000);
});

// Using a Promise
myPromise
  .then(result => console.log(result))
  .catch(error => console.error(error))
  .finally(() => console.log("Operation completed"));
```

### Promise Chaining

```javascript
fetch('https://api.example.com/users')
  .then(response => response.json())
  .then(data => {
    console.log(data);
    return fetch(`https://api.example.com/users/${data[0].id}`);
  })
  .then(response => response.json())
  .then(user => console.log(user))
  .catch(error => console.error(error));
```

### Async/Await

Async/await is syntactic sugar built on top of Promises, making asynchronous code look more like synchronous code.

```javascript
// Async function declaration
async function fetchUserData() {
  try {
    const response = await fetch('https://api.example.com/users');
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Error fetching data:', error);
    throw error;
  }
}

// Using the async function
fetchUserData()
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

### Multiple Async Operations

```javascript
// Promise.all - Wait for all promises
async function fetchAllData() {
  const [users, posts, comments] = await Promise.all([
    fetch('/api/users').then(r => r.json()),
    fetch('/api/posts').then(r => r.json()),
    fetch('/api/comments').then(r => r.json())
  ]);
  
  return { users, posts, comments };
}

// Promise.race - Return first resolved promise
async function fetchFastest() {
  const result = await Promise.race([
    fetch('/api/server1'),
    fetch('/api/server2'),
    fetch('/api/server3')
  ]);
  
  return result.json();
}

// Promise.allSettled - Wait for all promises regardless of outcome
async function fetchWithAllResults() {
  const results = await Promise.allSettled([
    fetch('/api/endpoint1'),
    fetch('/api/endpoint2'),
    fetch('/api/endpoint3')
  ]);
  
  results.forEach((result, index) => {
    if (result.status === 'fulfilled') {
      console.log(`Request ${index} succeeded:`, result.value);
    } else {
      console.log(`Request ${index} failed:`, result.reason);
    }
  });
}
```

## Closures

A closure is a function that has access to variables in its outer (enclosing) lexical scope, even after the outer function has returned.

```javascript
function createCounter() {
  let count = 0;
  
  return {
    increment: function() {
      count++;
      return count;
    },
    decrement: function() {
      count--;
      return count;
    },
    getCount: function() {
      return count;
    }
  };
}

const counter = createCounter();
console.log(counter.increment()); // 1
console.log(counter.increment()); // 2
console.log(counter.getCount());  // 2
console.log(counter.decrement()); // 1
```

### Practical Closure Examples

```javascript
// Data privacy
function bankAccount(initialBalance) {
  let balance = initialBalance;
  
  return {
    deposit: (amount) => {
      balance += amount;
      return balance;
    },
    withdraw: (amount) => {
      if (amount <= balance) {
        balance -= amount;
        return balance;
      }
      return "Insufficient funds";
    },
    getBalance: () => balance
  };
}

// Function factories
function createMultiplier(multiplier) {
  return function(value) {
    return value * multiplier;
  };
}

const double = createMultiplier(2);
const triple = createMultiplier(3);

console.log(double(5));  // 10
console.log(triple(5));  // 15
```

## Higher-Order Functions

Functions that take other functions as arguments or return functions.

```javascript
// Array methods using higher-order functions
const numbers = [1, 2, 3, 4, 5];

// map - Transform each element
const doubled = numbers.map(num => num * 2);
console.log(doubled); // [2, 4, 6, 8, 10]

// filter - Select elements based on condition
const evens = numbers.filter(num => num % 2 === 0);
console.log(evens); // [2, 4]

// reduce - Accumulate values
const sum = numbers.reduce((acc, num) => acc + num, 0);
console.log(sum); // 15

// forEach - Execute function for each element
numbers.forEach(num => console.log(num));

// find - Find first matching element
const found = numbers.find(num => num > 3);
console.log(found); // 4

// some - Check if at least one element satisfies condition
const hasEven = numbers.some(num => num % 2 === 0);
console.log(hasEven); // true

// every - Check if all elements satisfy condition
const allPositive = numbers.every(num => num > 0);
console.log(allPositive); // true
```

## Destructuring

### Array Destructuring

```javascript
const colors = ["red", "green", "blue"];

// Basic destructuring
const [first, second, third] = colors;
console.log(first);  // "red"

// Skip elements
const [primary, , tertiary] = colors;
console.log(tertiary); // "blue"

// Rest operator
const [head, ...tail] = colors;
console.log(tail); // ["green", "blue"]

// Default values
const [a, b, c, d = "yellow"] = colors;
console.log(d); // "yellow"
```

### Object Destructuring

```javascript
const user = {
  name: "Alice",
  age: 30,
  email: "alice@example.com",
  address: {
    city: "New York",
    country: "USA"
  }
};

// Basic destructuring
const { name, age } = user;
console.log(name); // "Alice"

// Rename variables
const { name: userName, age: userAge } = user;
console.log(userName); // "Alice"

// Default values
const { name, phone = "N/A" } = user;
console.log(phone); // "N/A"

// Nested destructuring
const { address: { city, country } } = user;
console.log(city); // "New York"

// Function parameter destructuring
function greet({ name, age }) {
  console.log(`Hello, ${name}! You are ${age} years old.`);
}

greet(user);
```

## Spread and Rest Operators

### Spread Operator (...)

```javascript
// Array spreading
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const combined = [...arr1, ...arr2];
console.log(combined); // [1, 2, 3, 4, 5, 6]

// Object spreading
const defaults = { theme: "light", fontSize: 14 };
const userPrefs = { fontSize: 16, language: "en" };
const settings = { ...defaults, ...userPrefs };
console.log(settings); // { theme: "light", fontSize: 16, language: "en" }

// Function arguments
const numbers = [1, 2, 3, 4, 5];
console.log(Math.max(...numbers)); // 5
```

### Rest Operator (...)

```javascript
// Function parameters
function sum(...numbers) {
  return numbers.reduce((acc, num) => acc + num, 0);
}

console.log(sum(1, 2, 3, 4, 5)); // 15

// Array destructuring
const [first, second, ...rest] = [1, 2, 3, 4, 5];
console.log(rest); // [3, 4, 5]

// Object destructuring
const { name, age, ...otherDetails } = user;
console.log(otherDetails); // { email: "alice@example.com", address: {...} }
```

## Prototypes and Inheritance

### Prototype Chain

```javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
}

Person.prototype.greet = function() {
  return `Hello, my name is ${this.name}`;
};

const john = new Person("John", 30);
console.log(john.greet()); // "Hello, my name is John"
```

### ES6 Classes

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }
  
  speak() {
    return `${this.name} makes a sound`;
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name);
    this.breed = breed;
  }
  
  speak() {
    return `${this.name} barks`;
  }
  
  fetch() {
    return `${this.name} fetches the ball`;
  }
}

const dog = new Dog("Rex", "German Shepherd");
console.log(dog.speak());  // "Rex barks"
console.log(dog.fetch());  // "Rex fetches the ball"
```

## Modules (ES6)

### Exporting

```javascript
// Named exports
export const PI = 3.14159;
export function add(a, b) {
  return a + b;
}

// Default export
export default class Calculator {
  add(a, b) {
    return a + b;
  }
}
```

### Importing

```javascript
// Named imports
import { PI, add } from './math.js';

// Default import
import Calculator from './Calculator.js';

// Import all
import * as MathUtils from './math.js';

// Rename imports
import { add as sum } from './math.js';
```

## Error Handling

### Try-Catch-Finally

```javascript
try {
  const result = riskyOperation();
  console.log(result);
} catch (error) {
  console.error('An error occurred:', error.message);
} finally {
  console.log('Cleanup operations');
}

// Custom errors
class ValidationError extends Error {
  constructor(message) {
    super(message);
    this.name = "ValidationError";
  }
}

function validateUser(user) {
  if (!user.name) {
    throw new ValidationError("Name is required");
  }
}
```

## Best Practices

1. **Always use async/await for cleaner asynchronous code**
2. **Handle errors in async functions with try-catch**
3. **Use Promise.all for parallel async operations**
4. **Leverage closures for data privacy**
5. **Use higher-order functions for cleaner, more functional code**
6. **Prefer destructuring for cleaner code**
7. **Use spread operator for immutable updates**
8. **Use modules to organize code**
9. **Always handle promise rejections**
10. **Use custom error classes for better error handling**
