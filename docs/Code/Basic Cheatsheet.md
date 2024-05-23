## How to ES6
### Built-ins
```js
// Array
let numbers = [1, 2, 3, 4, 5];

// Array methods
let squaredNumbers = numbers.map(num => num ** 2);
let evenNumbers = numbers.filter(num => num % 2 === 0);
let sum = numbers.reduce((acc, curr) => acc + curr, 0);
let firstEvenNumber = numbers.find(num => num % 2 === 0);
numbers.forEach(num => console.log(num));

// String
let str = "   Hello, World!   ";

// String methods
console.log(str.startsWith("Hello")); // true
console.log(str.endsWith("World!")); // true
console.log(str.includes("World")); // true
console.log(str.trim()); // "Hello, World!"
console.log(str.padStart(20, "x")); // "xxxxxHello, World!"
console.log(str.padEnd(20, "x")); // "Hello, World!xxxxx"

// Object
let obj = { name: "John", age: 30, city: "New York" };

// Object methods
console.log(Object.keys(obj)); // ["name", "age", "city"]
console.log(Object.values(obj)); // ["John", 30, "New York"]
console.log(Object.entries(obj)); // [["name", "John"], ["age", 30], ["city", "New York"]]

// Promise
let promise1 = new Promise((resolve) => setTimeout(resolve, 1000, 'Promise 1 resolved'));
let promise2 = new Promise((resolve) => setTimeout(resolve, 500, 'Promise 2 resolved'));

// Promise methods
Promise.all([promise1, promise2])
    .then(values => console.log(values)); // ["Promise 1 resolved", "Promise 2 resolved"]

Promise.race([promise1, promise2])
    .then(value => console.log(value)); // "Promise 2 resolved"

// Miscellaneous
let targetObj = { a: 1 };
let sourceObj = { b: 2, c: 3 };
Object.assign(targetObj, sourceObj);
console.log(targetObj); // { a: 1, b: 2, c: 3 }

let arrayLikeObject = { 0: 'a', 1: 'b', 2: 'c', length: 3 };
let newArray = Array.from(arrayLikeObject);
console.log(newArray); // ['a', 'b', 'c']

console.log(Array.isArray(numbers)); // true

console.log(Math.trunc(4.7)); // 4
```

### Arrow Functions
```js
// Traditional function
function add(a, b) {
    return a + b;
}

// Arrow function
const add = (a, b) => a + b;
```

### Template Literals
```js
let name = "Alice";
let greeting = `Hello, ${name}!`;
console.log(greeting); // Hello, Alice!
```

### Destructuring
```js
// Object destructuring
let person = { name: "John", age: 30 };
let { name, age } = person;
console.log(name, age); // John 30

// Array destructuring
let numbers = [1, 2, 3];
let [x, y, z] = numbers;
console.log(x, y, z); // 1 2 3
```

### Spread Operator
```js
let arr1 = [1, 2, 3];
let arr2 = [4, 5, 6];
let combined = [...arr1, ...arr2];
console.log(combined); // [1, 2, 3, 4, 5, 6]
```

### Classes
```js
class Animal {
    constructor(name) {
        this.name = name;
    }
    speak() {
        console.log(`${this.name} makes a sound.`);
    }
}

let dog = new Animal('Dog');
dog.speak(); // Dog makes a sound.
```

### Modules
```js
// Module export
export function add(a, b) {
    return a + b;
}

// Module import
import { add } from './math';
```

## How to install
### CRA with TypeScript
```sh
npx create-react-app my-app --template typescript
```

### Add TypeScript in an existing CRA
```sh
npm init -y
npm install --save typescript @types/node @types/react @types/react-dom @types/jest
```

### Express-TypeScript
```sh
npm init -y
npm install express @types/express
npm install typescript @types/node
npm install jsonwebtoken
npm install body-parser
```

## How to React-TypeScript
### Basic
```tsx
import React from 'react';

interface MyComponentProps {
  name: string;
  age: number;
}

const MyComponent: React.FC<MyComponentProps> = ({ name, age }) => {
  return (
    <div>
      <h1>{name}</h1>
      <p>{age}</p>
    </div>
  );
};

export default MyComponent;
```

### ToDo
```tsx
import React, { useState, useEffect } from 'react';

interface Todo {
  id: number;
  title: string;
  completed: boolean;
}

const App: React.FC = () => {
  const [todos, setTodos] = useState<Todo[]>([]);

  useEffect(() => {
    fetchTodos();
  }, []);

  const fetchTodos = async () => {
    try {
      const response = await fetch('https://jsonplaceholder.typicode.com/todos');
      if (!response.ok) {
        throw new Error('Failed to fetch todos');
      }
      const data = await response.json();
      setTodos(data);
    } catch (error) {
      console.error(error);
    }
  };

  const handleToggleTodo = (id: number) => {
    setTodos(prevTodos =>
      prevTodos.map(todo =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  };

  return (
    <div>
      <h1>Todos</h1>
      <ul>
        {todos.map(todo => (
          <li
            key={todo.id}
            style={{ textDecoration: todo.completed ? 'line-through' : 'none', cursor: 'pointer' }}
            onClick={() => handleToggleTodo(todo.id)}
          >
            {todo.title}
          </li>
        ))}
      </ul>
    </div>
  );
};
  
export default App;
```

## How to Express-TypeScript
### Basic
```tsx
import express, { Request, Response } from 'express';

const app = express();
const port = 3000;

app.use(express.json());

app.get('/api/hello', (req: Request, res: Response) => {
  res.json({ message: 'Hello, World!' });
});

app.listen(port, () => {
  console.log(`Server is running on http://localhost:${port}`);
});
```

### Auth
```ts
import express, { Request, Response, NextFunction } from 'express';
import jwt from 'jsonwebtoken';
import bodyParser from 'body-parser';

interface User {
  id: number;
  username: string;
  password: string;
}

const users: User[] = [
  { id: 1, username: 'user1', password: 'password1' },
  { id: 2, username: 'user2', password: 'password2' }
];

const app = express();

app.use(bodyParser.json());

app.post('/login', (req: Request, res: Response) => {
  const { username, password } = req.body as { username: string; password: string };

const user = users.find(u => u.username === username && u.password === password);
  if (user) {
    const token = jwt.sign({ userId: user.id }, 'secret_key', { expiresIn: '1h' });
    res.json({ token });
  } else {
    res.status(401).json({ message: 'Invalid credentials' });
  }
});

const authenticateToken = (req: Request, res: Response, next: NextFunction) => {
  const token = req.headers['authorization'] as string;
  if (!token) return res.status(401).json({ message: 'Token required' });

  jwt.verify(token, 'secret_key', (err, decoded) => {
    if (err) return res.status(403).json({ message: 'Invalid token' });
    req.userId = (decoded as { userId: number }).userId;
    next();
  });
};

app.get('/protected', authenticateToken, (req: Request, res: Response) => {
  res.json({ message: 'Access granted' });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
``` 

## How to Fetch
### HTTP Module
```js
const http = require('http');

http.get('http://api.example.com/data', (res) => {
    let data = '';

    res.on('data', (chunk) => {
        data += chunk;
    });

    res.on('end', () => {
        console.log(JSON.parse(data));
    });
}).on('error', (err) => {
    console.error('Error: ', err);
});
```

### Fetch API
```js
fetch('http://api.example.com/data')
    .then(response => response.json())
    .then(data => console.log(data))
    .catch(error => console.error('Error: ', error));
```
### Axios
```sh
npm install axios
```

```js
const axios = require('axios');

axios.get('http://api.example.com/data')
    .then(response => {
        console.log(response.data);
    })
    .catch(error => {
        console.error('Error: ', error);
    });
```