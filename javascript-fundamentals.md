# JavaScript Fundamentals

## Introduction to JavaScript

JavaScript is a versatile, high-level programming language that is primarily used for web development. It enables interactive web pages and is an essential part of web applications alongside HTML and CSS.

## Variables and Data Types

### Variable Declarations

JavaScript provides three ways to declare variables:

- **var**: Function-scoped or globally-scoped variable declaration
- **let**: Block-scoped variable declaration (ES6+)
- **const**: Block-scoped constant declaration (ES6+)

```javascript
var x = 10;
let y = 20;
const z = 30;
```

### Primitive Data Types

JavaScript has seven primitive data types:

1. **String**: Textual data
   ```javascript
   let name = "John";
   let greeting = 'Hello';
   ```

2. **Number**: Numeric values (integers and floating-point)
   ```javascript
   let age = 25;
   let price = 19.99;
   ```

3. **Boolean**: True or false values
   ```javascript
   let isActive = true;
   let hasPermission = false;
   ```

4. **Undefined**: Variable declared but not assigned
   ```javascript
   let value;
   console.log(value); // undefined
   ```

5. **Null**: Intentional absence of value
   ```javascript
   let data = null;
   ```

6. **Symbol**: Unique identifier (ES6+)
   ```javascript
   let id = Symbol('id');
   ```

7. **BigInt**: Large integers (ES2020+)
   ```javascript
   let bigNumber = 1234567890123456789012345678901234567890n;
   ```

### Complex Data Types

**Object**: Collection of key-value pairs
```javascript
let person = {
  name: "Alice",
  age: 30,
  city: "New York"
};
```

**Array**: Ordered collection of values
```javascript
let fruits = ["apple", "banana", "orange"];
let numbers = [1, 2, 3, 4, 5];
```

## Functions

### Function Declaration

```javascript
function greet(name) {
  return `Hello, ${name}!`;
}
```

### Function Expression

```javascript
const greet = function(name) {
  return `Hello, ${name}!`;
};
```

### Arrow Functions (ES6+)

```javascript
const greet = (name) => `Hello, ${name}!`;

// With multiple parameters
const add = (a, b) => a + b;

// With function body
const multiply = (a, b) => {
  const result = a * b;
  return result;
};
```

## Operators

### Arithmetic Operators

```javascript
let a = 10, b = 5;
console.log(a + b);  // Addition: 15
console.log(a - b);  // Subtraction: 5
console.log(a * b);  // Multiplication: 50
console.log(a / b);  // Division: 2
console.log(a % b);  // Modulus: 0
console.log(a ** b); // Exponentiation: 100000
```

### Comparison Operators

```javascript
console.log(5 == "5");   // true (loose equality)
console.log(5 === "5");  // false (strict equality)
console.log(5 != "5");   // false
console.log(5 !== "5");  // true
console.log(5 > 3);      // true
console.log(5 < 3);      // false
```

### Logical Operators

```javascript
console.log(true && false);  // AND: false
console.log(true || false);  // OR: true
console.log(!true);          // NOT: false
```

## Control Flow

### Conditional Statements

```javascript
// if-else
if (age >= 18) {
  console.log("Adult");
} else {
  console.log("Minor");
}

// switch statement
switch (day) {
  case "Monday":
    console.log("Start of the week");
    break;
  case "Friday":
    console.log("End of the week");
    break;
  default:
    console.log("Middle of the week");
}

// Ternary operator
let status = age >= 18 ? "Adult" : "Minor";
```

### Loops

```javascript
// for loop
for (let i = 0; i < 5; i++) {
  console.log(i);
}

// while loop
let i = 0;
while (i < 5) {
  console.log(i);
  i++;
}

// do-while loop
let j = 0;
do {
  console.log(j);
  j++;
} while (j < 5);

// for...of loop (arrays)
for (let fruit of fruits) {
  console.log(fruit);
}

// for...in loop (objects)
for (let key in person) {
  console.log(`${key}: ${person[key]}`);
}
```

## Type Conversion

### Implicit Conversion (Coercion)

```javascript
console.log("5" + 2);    // "52" (string concatenation)
console.log("5" - 2);    // 3 (numeric subtraction)
console.log("5" * "2");  // 10 (numeric multiplication)
```

### Explicit Conversion

```javascript
// To String
String(123);        // "123"
(123).toString();   // "123"

// To Number
Number("123");      // 123
parseInt("123");    // 123
parseFloat("12.34"); // 12.34

// To Boolean
Boolean(1);         // true
Boolean(0);         // false
Boolean("");        // false
Boolean("text");    // true
```

## Best Practices

1. **Use `const` by default**, `let` when needed, avoid `var`
2. **Use strict equality (`===`) instead of loose equality (`==`)**
3. **Use meaningful variable names**
4. **Follow consistent naming conventions (camelCase)**
5. **Add comments for complex logic**
6. **Keep functions small and focused**
7. **Use template literals for string interpolation**
