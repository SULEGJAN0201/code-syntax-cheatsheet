# 🚀 Framework Patterns Guide
## React, Angular & ASP.NET Core - Common Patterns for Senior Developers

> This guide covers the most common patterns you'll use daily in React, Angular, and ASP.NET Core applications.

---

## Table of Contents

### ⚛️ React Patterns
1. [React Hooks](#react-hooks)
2. [Component Patterns](#react-component-patterns)
3. [State Management](#react-state-management)
4. [Custom Hooks](#react-custom-hooks)
5. [Context API](#react-context-api)

### 🅰️ Angular Patterns
6. [Decorators](#angular-decorators)
7. [Services & Dependency Injection](#angular-services--dependency-injection)
8. [RxJS Observables](#angular-rxjs-observables)
9. [Component Communication](#angular-component-communication)
10. [Forms](#angular-forms)

### 🟣 ASP.NET Core Patterns
11. [Controllers & Actions](#aspnet-controllers--actions)
12. [Dependency Injection](#aspnet-dependency-injection)
13. [Middleware](#aspnet-middleware)
14. [Entity Framework](#aspnet-entity-framework)
15. [Repository Pattern](#aspnet-repository-pattern)

---

# ⚛️ React Patterns

## React Hooks

### What are Hooks?
Hooks let you use state and other React features in functional components without writing classes.

### useState - Managing Component State

**What it does**: Lets you add state to functional components.

```javascript
import { useState } from 'react';

function Counter() {
  // Declare state variable 'count' with initial value 0
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      {/* Clicking button updates state and re-renders component */}
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
  // When count changes, component re-renders with new value
}
```

**Multiple state variables**:
```javascript
function UserForm() {
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [age, setAge] = useState(0);

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log({ name, email, age });
    // Output: { name: "John", email: "john@example.com", age: 30 }
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

### useEffect - Side Effects & Lifecycle

**What it does**: Runs code after render (like API calls, subscriptions, timers).

```javascript
import { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  // Runs after component renders
  useEffect(() => {
    async function fetchUser() {
      setLoading(true);
      const response = await fetch(`/api/users/${userId}`);
      const data = await response.json();
      setUser(data);                              // Updates state
      setLoading(false);
    }

    fetchUser();

    // Cleanup function (runs when component unmounts)
    return () => {
      console.log('Component unmounting');
    };
  }, [userId]); // Only re-run when userId changes

  if (loading) return <div>Loading...</div>;

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  );
}
```

**Common useEffect patterns**:

```javascript
// 1. Run once on mount (empty dependency array)
useEffect(() => {
  console.log('Component mounted');
}, []);

// 2. Run on every render (no dependency array)
useEffect(() => {
  console.log('Component rendered');
});

// 3. Run when specific values change
useEffect(() => {
  console.log('Count changed:', count);
}, [count]);

// 4. Cleanup (subscriptions, timers, etc.)
useEffect(() => {
  const timer = setInterval(() => {
    console.log('Tick');
  }, 1000);

  // Cleanup function
  return () => clearInterval(timer);
}, []);
```

### useContext - Share Data Across Components

**What it does**: Lets you pass data through component tree without props.

```javascript
import { createContext, useContext, useState } from 'react';

// 1. Create context
const ThemeContext = createContext();

// 2. Create provider component
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');

  const toggleTheme = () => {
    setTheme(theme === 'light' ? 'dark' : 'light');
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

// 3. Use context in child components
function ThemedButton() {
  const { theme, toggleTheme } = useContext(ThemeContext);

  return (
    <button
      style={{
        background: theme === 'light' ? '#fff' : '#333',
        color: theme === 'light' ? '#333' : '#fff'
      }}
      onClick={toggleTheme}
    >
      Current theme: {theme}
    </button>
  );
  // Button has access to theme without passing props!
}

// 4. Wrap app with provider
function App() {
  return (
    <ThemeProvider>
      <ThemedButton />
    </ThemeProvider>
  );
}
```

### useMemo - Memoize Expensive Calculations

**What it does**: Caches expensive calculation results to avoid re-computing on every render.

```javascript
import { useMemo, useState } from 'react';

function ProductList({ products }) {
  const [filter, setFilter] = useState('');

  // Only recalculate when products or filter changes
  const filteredProducts = useMemo(() => {
    console.log('Filtering products...');         // Only logs when needed
    return products.filter(p =>
      p.name.toLowerCase().includes(filter.toLowerCase())
    );
  }, [products, filter]);

  return (
    <div>
      <input
        value={filter}
        onChange={(e) => setFilter(e.target.value)}
        placeholder="Search products"
      />
      {filteredProducts.map(product => (
        <div key={product.id}>{product.name}</div>
      ))}
    </div>
  );
}
```

### useCallback - Memoize Functions

**What it does**: Caches function reference to prevent unnecessary re-renders.

```javascript
import { useCallback, useState } from 'react';

function TodoList() {
  const [todos, setTodos] = useState([]);

  // Function reference stays the same across renders
  const addTodo = useCallback((text) => {
    setTodos(prev => [...prev, { id: Date.now(), text }]);
  }, []); // No dependencies = function never changes

  return (
    <div>
      <TodoForm onAdd={addTodo} />
      {/* TodoForm won't re-render if addTodo reference is same */}
      <ul>
        {todos.map(todo => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
    </div>
  );
}
```

---

## React Component Patterns

### Container/Presentational Pattern

**What it does**: Separates logic (container) from UI (presentational).

```javascript
// Presentational Component (just displays UI)
function UserCard({ user, onDelete }) {
  return (
    <div className="user-card">
      <h3>{user.name}</h3>
      <p>{user.email}</p>
      <button onClick={() => onDelete(user.id)}>Delete</button>
    </div>
  );
}

// Container Component (handles logic and data)
function UserCardContainer({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(setUser);
  }, [userId]);

  const handleDelete = async (id) => {
    await fetch(`/api/users/${id}`, { method: 'DELETE' });
    // Handle deletion logic
  };

  if (!user) return <div>Loading...</div>;

  return <UserCard user={user} onDelete={handleDelete} />;
}
```

### Compound Components Pattern

**What it does**: Multiple components work together sharing state.

```javascript
// Parent component manages shared state
function Tabs({ children }) {
  const [activeTab, setActiveTab] = useState(0);

  return (
    <div className="tabs">
      {React.Children.map(children, (child, index) =>
        React.cloneElement(child, {
          isActive: index === activeTab,
          onClick: () => setActiveTab(index)
        })
      )}
    </div>
  );
}

// Child component
function Tab({ title, children, isActive, onClick }) {
  return (
    <div>
      <button
        onClick={onClick}
        style={{ fontWeight: isActive ? 'bold' : 'normal' }}
      >
        {title}
      </button>
      {isActive && <div>{children}</div>}
    </div>
  );
}

// Usage
function App() {
  return (
    <Tabs>
      <Tab title="Tab 1">Content 1</Tab>
      <Tab title="Tab 2">Content 2</Tab>
      <Tab title="Tab 3">Content 3</Tab>
    </Tabs>
  );
}
```

---

## React State Management

### Local State vs Global State

**Local State**: Data needed by one or few components
```javascript
function LoginForm() {
  const [username, setUsername] = useState('');  // Only LoginForm needs this
  const [password, setPassword] = useState('');
  // ...
}
```

**Global State**: Data needed by many components (use Context or libraries like Redux)

### Context API for Global State

```javascript
// 1. Create global state
const AppContext = createContext();

function AppProvider({ children }) {
  const [user, setUser] = useState(null);
  const [cart, setCart] = useState([]);

  const login = async (credentials) => {
    const user = await api.login(credentials);
    setUser(user);
  };

  const addToCart = (product) => {
    setCart(prev => [...prev, product]);
  };

  return (
    <AppContext.Provider value={{
      user,
      cart,
      login,
      addToCart
    }}>
      {children}
    </AppContext.Provider>
  );
}

// 2. Use anywhere in app
function Header() {
  const { user, cart } = useContext(AppContext);

  return (
    <header>
      <span>Welcome, {user?.name}</span>
      <span>Cart items: {cart.length}</span>
    </header>
  );
}
```

---

## React Custom Hooks

### What are Custom Hooks?
Reusable logic that can be shared across components.

```javascript
// Custom hook for fetching data
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    async function fetchData() {
      try {
        setLoading(true);
        const response = await fetch(url);
        const result = await response.json();
        setData(result);
      } catch (err) {
        setError(err);
      } finally {
        setLoading(false);
      }
    }

    fetchData();
  }, [url]);

  return { data, loading, error };
}

// Usage in any component
function Users() {
  const { data: users, loading, error } = useFetch('/api/users');

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

**More custom hook examples**:

```javascript
// Hook for local storage
function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    const stored = localStorage.getItem(key);
    return stored ? JSON.parse(stored) : initialValue;
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue];
}

// Usage
function App() {
  const [theme, setTheme] = useLocalStorage('theme', 'light');
  // theme is automatically saved to localStorage!
}

// Hook for window size
function useWindowSize() {
  const [size, setSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight
  });

  useEffect(() => {
    const handleResize = () => {
      setSize({
        width: window.innerWidth,
        height: window.innerHeight
      });
    };

    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);

  return size;
}

// Usage
function ResponsiveComponent() {
  const { width } = useWindowSize();

  return (
    <div>
      {width < 768 ? <MobileMenu /> : <DesktopMenu />}
    </div>
  );
}
```

---

# 🅰️ Angular Patterns

## Angular Decorators

### What are Decorators?
Special syntax that adds metadata to classes, methods, and properties.

### @Component - Define Components

**What it does**: Marks a class as an Angular component.

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-user-card',           // HTML tag: <app-user-card></app-user-card>
  templateUrl: './user-card.component.html',
  styleUrls: ['./user-card.component.css']
})
export class UserCardComponent {
  name = 'John Doe';                   // Component property
  age = 30;

  // Component method
  greet() {
    console.log(`Hello, I'm ${this.name}`);
    // Output: Hello, I'm John Doe
  }
}
```

**Inline template**:
```typescript
@Component({
  selector: 'app-greeting',
  template: `
    <div>
      <h1>{{ title }}</h1>
      <p>{{ message }}</p>
      <button (click)="handleClick()">Click me</button>
    </div>
  `,
  styles: [`
    h1 { color: blue; }
    p { font-size: 14px; }
  `]
})
export class GreetingComponent {
  title = 'Welcome';
  message = 'Hello Angular!';

  handleClick() {
    alert('Button clicked!');
  }
}
```

### @Input - Pass Data to Child Component

**What it does**: Allows parent component to pass data to child.

```typescript
// Child component
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-product-card',
  template: `
    <div class="card">
      <h3>{{ product.name }}</h3>
      <p>Price: ${{ product.price }}</p>
    </div>
  `
})
export class ProductCardComponent {
  @Input() product!: { name: string; price: number };
  // ! means "trust me, this will be set by parent"
}

// Parent component
@Component({
  selector: 'app-product-list',
  template: `
    <app-product-card
      *ngFor="let item of products"
      [product]="item">
    </app-product-card>
  `
})
export class ProductListComponent {
  products = [
    { name: 'Laptop', price: 999 },
    { name: 'Mouse', price: 25 }
  ];
}
```

### @Output - Emit Events from Child

**What it does**: Allows child component to send data to parent.

```typescript
import { Component, Output, EventEmitter } from '@angular/core';

// Child component
@Component({
  selector: 'app-counter',
  template: `
    <button (click)="increment()">Count: {{ count }}</button>
  `
})
export class CounterComponent {
  count = 0;
  @Output() countChanged = new EventEmitter<number>();

  increment() {
    this.count++;
    this.countChanged.emit(this.count);  // Send count to parent
  }
}

// Parent component
@Component({
  selector: 'app-parent',
  template: `
    <app-counter (countChanged)="onCountChanged($event)"></app-counter>
    <p>Parent received: {{ receivedCount }}</p>
  `
})
export class ParentComponent {
  receivedCount = 0;

  onCountChanged(count: number) {
    this.receivedCount = count;
    console.log('Count from child:', count);
    // Output: Count from child: 1, 2, 3...
  }
}
```

### @Injectable - Create Services

**What it does**: Marks a class as available for dependency injection.

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Injectable({
  providedIn: 'root'  // Available app-wide (singleton)
})
export class UserService {
  constructor(private http: HttpClient) {}

  getUsers() {
    return this.http.get<User[]>('/api/users');
  }

  getUserById(id: number) {
    return this.http.get<User>(`/api/users/${id}`);
  }
}
```

---

## Angular Services & Dependency Injection

### What is Dependency Injection?
Angular automatically provides (injects) service instances to components that need them.

### Creating and Using Services

```typescript
// 1. Create service
@Injectable({ providedIn: 'root' })
export class AuthService {
  private currentUser: User | null = null;

  login(credentials: { username: string; password: string }) {
    return this.http.post<User>('/api/login', credentials)
      .pipe(
        tap(user => this.currentUser = user)
      );
  }

  logout() {
    this.currentUser = null;
  }

  isAuthenticated(): boolean {
    return this.currentUser !== null;
  }
}

// 2. Inject service in component
@Component({
  selector: 'app-login',
  template: `
    <form (ngSubmit)="onSubmit()">
      <input [(ngModel)]="username" name="username">
      <input type="password" [(ngModel)]="password" name="password">
      <button type="submit">Login</button>
    </form>
  `
})
export class LoginComponent {
  username = '';
  password = '';

  // Service automatically injected by Angular
  constructor(private authService: AuthService) {}

  onSubmit() {
    this.authService.login({
      username: this.username,
      password: this.password
    }).subscribe(
      user => console.log('Logged in:', user),
      error => console.error('Login failed:', error)
    );
  }
}
```

### Service Hierarchies

```typescript
// App-wide singleton
@Injectable({ providedIn: 'root' })
export class GlobalService { }

// Component-level (new instance per component)
@Component({
  selector: 'app-my-component',
  providers: [LocalService]  // Creates new instance for this component
})
export class MyComponent {
  constructor(private localService: LocalService) {}
}
```

---

## Angular RxJS Observables

### What are Observables?
Streams of data over time (like Promises, but can emit multiple values).

### Basic Observable Usage

```typescript
import { Observable } from 'rxjs';

@Injectable({ providedIn: 'root' })
export class DataService {
  constructor(private http: HttpClient) {}

  // Returns Observable
  getUsers(): Observable<User[]> {
    return this.http.get<User[]>('/api/users');
  }
}

// Component subscribes to Observable
@Component({
  selector: 'app-users',
  template: `
    <div *ngIf="loading">Loading...</div>
    <div *ngIf="error">Error: {{ error }}</div>
    <ul *ngIf="users">
      <li *ngFor="let user of users">{{ user.name }}</li>
    </ul>
  `
})
export class UsersComponent implements OnInit {
  users: User[] = [];
  loading = false;
  error: string | null = null;

  constructor(private dataService: DataService) {}

  ngOnInit() {
    this.loading = true;
    this.dataService.getUsers().subscribe({
      next: (users) => {
        this.users = users;
        this.loading = false;
        console.log('Received users:', users);
      },
      error: (err) => {
        this.error = err.message;
        this.loading = false;
      }
    });
  }
}
```

### RxJS Operators

**What they do**: Transform, filter, and combine observables.

```typescript
import { map, filter, debounceTime, switchMap } from 'rxjs/operators';

@Component({
  selector: 'app-search',
  template: `
    <input (input)="onSearch($event.target.value)">
    <ul>
      <li *ngFor="let result of results">{{ result.name }}</li>
    </ul>
  `
})
export class SearchComponent {
  searchSubject = new Subject<string>();
  results: any[] = [];

  constructor(private searchService: SearchService) {
    // Chain operators
    this.searchSubject.pipe(
      debounceTime(300),           // Wait 300ms after user stops typing
      filter(term => term.length > 2),  // Only search if 3+ characters
      switchMap(term =>
        this.searchService.search(term)  // Cancel previous requests
      )
    ).subscribe(results => {
      this.results = results;
      console.log('Search results:', results);
    });
  }

  onSearch(term: string) {
    this.searchSubject.next(term);  // Emit search term
  }
}
```

**Common RxJS operators**:

```typescript
import { map, filter, tap, catchError } from 'rxjs/operators';
import { of } from 'rxjs';

// map - Transform data
this.http.get<User[]>('/api/users').pipe(
  map(users => users.filter(u => u.age > 18))  // Transform response
).subscribe(adults => console.log(adults));

// filter - Only emit values that pass condition
this.searchSubject.pipe(
  filter(term => term.length > 0)
).subscribe(term => console.log(term));

// tap - Side effects without changing data
this.http.get('/api/users').pipe(
  tap(data => console.log('Received:', data)),  // Log without changing
  map(users => users.length)
).subscribe(count => console.log('Count:', count));

// catchError - Handle errors
this.http.get('/api/users').pipe(
  catchError(error => {
    console.error('Error:', error);
    return of([]);  // Return empty array on error
  })
).subscribe(users => console.log(users));
```

---

## Angular Component Communication

### Parent to Child (@Input)

```typescript
// Child
@Component({
  selector: 'app-child',
  template: '<p>{{ message }}</p>'
})
export class ChildComponent {
  @Input() message!: string;
}

// Parent
@Component({
  template: '<app-child [message]="parentMessage"></app-child>'
})
export class ParentComponent {
  parentMessage = 'Hello from parent';
}
```

### Child to Parent (@Output)

```typescript
// Child
@Component({
  selector: 'app-child',
  template: '<button (click)="sendMessage()">Send</button>'
})
export class ChildComponent {
  @Output() messageSent = new EventEmitter<string>();

  sendMessage() {
    this.messageSent.emit('Hello from child');
  }
}

// Parent
@Component({
  template: `
    <app-child (messageSent)="onMessageReceived($event)"></app-child>
    <p>{{ received }}</p>
  `
})
export class ParentComponent {
  received = '';

  onMessageReceived(message: string) {
    this.received = message;
  }
}
```

### Siblings (via Service)

```typescript
// Shared service
@Injectable({ providedIn: 'root' })
export class MessageService {
  private messageSource = new Subject<string>();
  message$ = this.messageSource.asObservable();

  sendMessage(message: string) {
    this.messageSource.next(message);
  }
}

// Sibling 1 (sender)
@Component({
  selector: 'app-sibling1',
  template: '<button (click)="send()">Send to sibling</button>'
})
export class Sibling1Component {
  constructor(private messageService: MessageService) {}

  send() {
    this.messageService.sendMessage('Hello sibling!');
  }
}

// Sibling 2 (receiver)
@Component({
  selector: 'app-sibling2',
  template: '<p>{{ message }}</p>'
})
export class Sibling2Component implements OnInit {
  message = '';

  constructor(private messageService: MessageService) {}

  ngOnInit() {
    this.messageService.message$.subscribe(msg => {
      this.message = msg;
      console.log('Received:', msg);  // Output: Received: Hello sibling!
    });
  }
}
```

---

## Angular Forms

### Template-Driven Forms

**What it does**: Forms built in HTML template with directives.

```typescript
import { FormsModule } from '@angular/forms';

@Component({
  selector: 'app-user-form',
  template: `
    <form #userForm="ngForm" (ngSubmit)="onSubmit(userForm)">
      <input
        name="username"
        [(ngModel)]="user.username"
        required
        minlength="3"
        #username="ngModel"
      >
      <div *ngIf="username.invalid && username.touched">
        Username is required (min 3 characters)
      </div>

      <input
        name="email"
        [(ngModel)]="user.email"
        required
        email
        #email="ngModel"
      >
      <div *ngIf="email.invalid && email.touched">
        Valid email is required
      </div>

      <button type="submit" [disabled]="userForm.invalid">
        Submit
      </button>
    </form>
  `
})
export class UserFormComponent {
  user = {
    username: '',
    email: ''
  };

  onSubmit(form: any) {
    if (form.valid) {
      console.log('Form submitted:', this.user);
      // Output: Form submitted: { username: "john", email: "john@example.com" }
    }
  }
}
```

### Reactive Forms

**What it does**: Forms built programmatically with more control.

```typescript
import { FormBuilder, FormGroup, Validators } from '@angular/forms';

@Component({
  selector: 'app-user-form',
  template: `
    <form [formGroup]="userForm" (ngSubmit)="onSubmit()">
      <input formControlName="username">
      <div *ngIf="userForm.get('username')?.invalid && userForm.get('username')?.touched">
        Username is required (min 3 characters)
      </div>

      <input formControlName="email">
      <div *ngIf="userForm.get('email')?.invalid && userForm.get('email')?.touched">
        Valid email is required
      </div>

      <button type="submit" [disabled]="userForm.invalid">
        Submit
      </button>
    </form>
  `
})
export class UserFormComponent implements OnInit {
  userForm!: FormGroup;

  constructor(private fb: FormBuilder) {}

  ngOnInit() {
    this.userForm = this.fb.group({
      username: ['', [Validators.required, Validators.minLength(3)]],
      email: ['', [Validators.required, Validators.email]]
    });
  }

  onSubmit() {
    if (this.userForm.valid) {
      console.log('Form value:', this.userForm.value);
      // Output: Form value: { username: "john", email: "john@example.com" }
    }
  }
}
```

---

# 🟣 ASP.NET Core Patterns

## ASP.NET Controllers & Actions

### What are Controllers?
Classes that handle HTTP requests and return responses.

### Basic Controller

```csharp
using Microsoft.AspNetCore.Mvc;

[ApiController]                              // Marks as API controller
[Route("api/[controller]")]                  // Route: /api/users
public class UsersController : ControllerBase
{
    // GET: /api/users
    [HttpGet]
    public IActionResult GetAll()
    {
        var users = new[] {
            new { Id = 1, Name = "John" },
            new { Id = 2, Name = "Jane" }
        };

        return Ok(users);                    // Returns 200 with JSON
        // Response: [{"id":1,"name":"John"},{"id":2,"name":"Jane"}]
    }

    // GET: /api/users/5
    [HttpGet("{id}")]
    public IActionResult GetById(int id)
    {
        var user = new { Id = id, Name = "John" };

        if (user == null)
            return NotFound();               // Returns 404

        return Ok(user);                     // Returns 200 with JSON
    }

    // POST: /api/users
    [HttpPost]
    public IActionResult Create([FromBody] User user)
    {
        // Validate and save user
        return CreatedAtAction(
            nameof(GetById),                 // Returns 201 with location header
            new { id = user.Id },
            user
        );
    }

    // PUT: /api/users/5
    [HttpPut("{id}")]
    public IActionResult Update(int id, [FromBody] User user)
    {
        // Update user logic
        return NoContent();                  // Returns 204 (success, no content)
    }

    // DELETE: /api/users/5
    [HttpDelete("{id}")]
    public IActionResult Delete(int id)
    {
        // Delete user logic
        return NoContent();                  // Returns 204
    }
}
```

### Action Results

```csharp
public class ExamplesController : ControllerBase
{
    // Return different status codes
    [HttpGet("ok")]
    public IActionResult OkExample()
    {
        return Ok(new { message = "Success" });           // 200
    }

    [HttpGet("created")]
    public IActionResult CreatedExample()
    {
        var item = new { Id = 1, Name = "New Item" };
        return Created("/api/items/1", item);             // 201
    }

    [HttpGet("notfound")]
    public IActionResult NotFoundExample()
    {
        return NotFound(new { error = "Item not found" }); // 404
    }

    [HttpGet("badrequest")]
    public IActionResult BadRequestExample()
    {
        return BadRequest(new { error = "Invalid data" }); // 400
    }

    [HttpGet("unauthorized")]
    public IActionResult UnauthorizedExample()
    {
        return Unauthorized();                             // 401
    }
}
```

---

## ASP.NET Dependency Injection

### What is Dependency Injection?
ASP.NET automatically creates and provides service instances.

### Registering Services

```csharp
// Program.cs (or Startup.cs)
var builder = WebApplication.CreateBuilder(args);

// Register services with different lifetimes

// Transient: New instance every time
builder.Services.AddTransient<IEmailService, EmailService>();

// Scoped: One instance per HTTP request
builder.Services.AddScoped<IUserService, UserService>();

// Singleton: One instance for entire app lifetime
builder.Services.AddSingleton<IConfigService, ConfigService>();

var app = builder.Build();
```

### Using Injected Services

```csharp
// 1. Define interface
public interface IUserService
{
    Task<List<User>> GetUsersAsync();
    Task<User> GetUserByIdAsync(int id);
}

// 2. Implement service
public class UserService : IUserService
{
    private readonly ApplicationDbContext _context;
    private readonly ILogger<UserService> _logger;

    // Dependencies injected via constructor
    public UserService(
        ApplicationDbContext context,
        ILogger<UserService> logger)
    {
        _context = context;
        _logger = logger;
    }

    public async Task<List<User>> GetUsersAsync()
    {
        _logger.LogInformation("Fetching all users");
        return await _context.Users.ToListAsync();
        // Returns list of users from database
    }

    public async Task<User> GetUserByIdAsync(int id)
    {
        _logger.LogInformation("Fetching user {UserId}", id);
        return await _context.Users.FindAsync(id);
    }
}

// 3. Inject into controller
[ApiController]
[Route("api/[controller]")]
public class UsersController : ControllerBase
{
    private readonly IUserService _userService;

    // Service automatically injected
    public UsersController(IUserService userService)
    {
        _userService = userService;
    }

    [HttpGet]
    public async Task<IActionResult> GetAll()
    {
        var users = await _userService.GetUsersAsync();
        return Ok(users);
    }
}
```

---

## ASP.NET Middleware

### What is Middleware?
Components that handle requests and responses in a pipeline.

### Built-in Middleware

```csharp
// Program.cs
var app = builder.Build();

// Middleware executes in order

app.UseHttpsRedirection();        // Redirects HTTP to HTTPS

app.UseAuthentication();          // Authenticates users

app.UseAuthorization();           // Authorizes users

app.UseStaticFiles();             // Serves static files (CSS, JS, images)

app.MapControllers();             // Routes to controllers

app.Run();
```

### Custom Middleware

```csharp
// Custom middleware class
public class RequestLoggingMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<RequestLoggingMiddleware> _logger;

    public RequestLoggingMiddleware(
        RequestDelegate next,
        ILogger<RequestLoggingMiddleware> logger)
    {
        _next = next;
        _logger = logger;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        // Before next middleware
        _logger.LogInformation(
            "Request: {Method} {Path}",
            context.Request.Method,
            context.Request.Path
        );
        // Output: Request: GET /api/users

        await _next(context);  // Call next middleware

        // After next middleware
        _logger.LogInformation(
            "Response: {StatusCode}",
            context.Response.StatusCode
        );
        // Output: Response: 200
    }
}

// Register middleware
app.UseMiddleware<RequestLoggingMiddleware>();

// Or use extension method
public static class MiddlewareExtensions
{
    public static IApplicationBuilder UseRequestLogging(
        this IApplicationBuilder builder)
    {
        return builder.UseMiddleware<RequestLoggingMiddleware>();
    }
}

// Usage
app.UseRequestLogging();
```

### Inline Middleware

```csharp
// Simple inline middleware
app.Use(async (context, next) =>
{
    Console.WriteLine($"Request: {context.Request.Path}");
    await next();  // Call next middleware
    Console.WriteLine($"Response: {context.Response.StatusCode}");
});
```

---

## ASP.NET Entity Framework

### What is Entity Framework?
ORM (Object-Relational Mapper) that lets you work with databases using C# objects.

### Defining Models

```csharp
// Entity model
public class User
{
    public int Id { get; set; }                    // Primary key (auto-incremented)
    public string Name { get; set; } = string.Empty;
    public string Email { get; set; } = string.Empty;
    public DateTime CreatedAt { get; set; }

    // Navigation property (one-to-many)
    public List<Order> Orders { get; set; } = new();
}

public class Order
{
    public int Id { get; set; }
    public decimal Total { get; set; }
    public DateTime OrderDate { get; set; }

    // Foreign key
    public int UserId { get; set; }
    public User User { get; set; } = null!;
}
```

### DbContext

```csharp
using Microsoft.EntityFrameworkCore;

public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    // Tables
    public DbSet<User> Users { get; set; }
    public DbSet<Order> Orders { get; set; }

    // Configure relationships
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<User>()
            .HasMany(u => u.Orders)
            .WithOne(o => o.User)
            .HasForeignKey(o => o.UserId);
    }
}

// Register in Program.cs
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection"))
);
```

### CRUD Operations

```csharp
public class UserService
{
    private readonly ApplicationDbContext _context;

    public UserService(ApplicationDbContext context)
    {
        _context = context;
    }

    // CREATE
    public async Task<User> CreateUserAsync(User user)
    {
        _context.Users.Add(user);
        await _context.SaveChangesAsync();
        Console.WriteLine($"Created user with ID: {user.Id}");
        // Output: Created user with ID: 1
        return user;
    }

    // READ
    public async Task<List<User>> GetAllUsersAsync()
    {
        return await _context.Users.ToListAsync();
        // Returns all users from database
    }

    public async Task<User?> GetUserByIdAsync(int id)
    {
        return await _context.Users.FindAsync(id);
        // Returns user or null if not found
    }

    // UPDATE
    public async Task<User?> UpdateUserAsync(int id, User updatedUser)
    {
        var user = await _context.Users.FindAsync(id);
        if (user == null) return null;

        user.Name = updatedUser.Name;
        user.Email = updatedUser.Email;

        await _context.SaveChangesAsync();
        Console.WriteLine($"Updated user {id}");
        return user;
    }

    // DELETE
    public async Task<bool> DeleteUserAsync(int id)
    {
        var user = await _context.Users.FindAsync(id);
        if (user == null) return false;

        _context.Users.Remove(user);
        await _context.SaveChangesAsync();
        Console.WriteLine($"Deleted user {id}");
        return true;
    }
}
```

### LINQ Queries

```csharp
public class UserService
{
    // Filter
    public async Task<List<User>> GetActiveUsersAsync()
    {
        return await _context.Users
            .Where(u => u.IsActive)
            .ToListAsync();
        // SQL: SELECT * FROM Users WHERE IsActive = 1
    }

    // Sort
    public async Task<List<User>> GetUsersSortedByNameAsync()
    {
        return await _context.Users
            .OrderBy(u => u.Name)
            .ToListAsync();
        // SQL: SELECT * FROM Users ORDER BY Name
    }

    // Include related data
    public async Task<User?> GetUserWithOrdersAsync(int id)
    {
        return await _context.Users
            .Include(u => u.Orders)              // Load orders too
            .FirstOrDefaultAsync(u => u.Id == id);
        // SQL: SELECT * FROM Users LEFT JOIN Orders ON...
    }

    // Complex query
    public async Task<List<User>> GetUsersWithRecentOrdersAsync()
    {
        var cutoffDate = DateTime.Now.AddDays(-30);

        return await _context.Users
            .Where(u => u.Orders.Any(o => o.OrderDate > cutoffDate))
            .Include(u => u.Orders)
            .OrderByDescending(u => u.CreatedAt)
            .Take(10)
            .ToListAsync();
        // Get 10 most recent users who ordered in last 30 days
    }

    // Aggregation
    public async Task<int> GetTotalUsersCountAsync()
    {
        return await _context.Users.CountAsync();
        // SQL: SELECT COUNT(*) FROM Users
    }

    public async Task<decimal> GetAverageOrderTotalAsync()
    {
        return await _context.Orders.AverageAsync(o => o.Total);
        // SQL: SELECT AVG(Total) FROM Orders
    }
}
```

---

## ASP.NET Repository Pattern

### What is Repository Pattern?
Separates data access logic from business logic.

### Basic Repository

```csharp
// 1. Generic repository interface
public interface IRepository<T> where T : class
{
    Task<List<T>> GetAllAsync();
    Task<T?> GetByIdAsync(int id);
    Task<T> AddAsync(T entity);
    Task<T> UpdateAsync(T entity);
    Task<bool> DeleteAsync(int id);
}

// 2. Generic repository implementation
public class Repository<T> : IRepository<T> where T : class
{
    private readonly ApplicationDbContext _context;
    private readonly DbSet<T> _dbSet;

    public Repository(ApplicationDbContext context)
    {
        _context = context;
        _dbSet = context.Set<T>();
    }

    public async Task<List<T>> GetAllAsync()
    {
        return await _dbSet.ToListAsync();
    }

    public async Task<T?> GetByIdAsync(int id)
    {
        return await _dbSet.FindAsync(id);
    }

    public async Task<T> AddAsync(T entity)
    {
        await _dbSet.AddAsync(entity);
        await _context.SaveChangesAsync();
        return entity;
    }

    public async Task<T> UpdateAsync(T entity)
    {
        _dbSet.Update(entity);
        await _context.SaveChangesAsync();
        return entity;
    }

    public async Task<bool> DeleteAsync(int id)
    {
        var entity = await _dbSet.FindAsync(id);
        if (entity == null) return false;

        _dbSet.Remove(entity);
        await _context.SaveChangesAsync();
        return true;
    }
}

// 3. Entity-specific repository (for custom queries)
public interface IUserRepository : IRepository<User>
{
    Task<List<User>> GetActiveUsersAsync();
    Task<User?> GetByEmailAsync(string email);
}

public class UserRepository : Repository<User>, IUserRepository
{
    private readonly ApplicationDbContext _context;

    public UserRepository(ApplicationDbContext context) : base(context)
    {
        _context = context;
    }

    public async Task<List<User>> GetActiveUsersAsync()
    {
        return await _context.Users
            .Where(u => u.IsActive)
            .ToListAsync();
    }

    public async Task<User?> GetByEmailAsync(string email)
    {
        return await _context.Users
            .FirstOrDefaultAsync(u => u.Email == email);
    }
}

// 4. Register repositories
builder.Services.AddScoped(typeof(IRepository<>), typeof(Repository<>));
builder.Services.AddScoped<IUserRepository, UserRepository>();

// 5. Use in controller
[ApiController]
[Route("api/[controller]")]
public class UsersController : ControllerBase
{
    private readonly IUserRepository _userRepository;

    public UsersController(IUserRepository userRepository)
    {
        _userRepository = userRepository;
    }

    [HttpGet]
    public async Task<IActionResult> GetAll()
    {
        var users = await _userRepository.GetAllAsync();
        return Ok(users);
    }

    [HttpGet("active")]
    public async Task<IActionResult> GetActive()
    {
        var users = await _userRepository.GetActiveUsersAsync();
        return Ok(users);
    }

    [HttpGet("{id}")]
    public async Task<IActionResult> GetById(int id)
    {
        var user = await _userRepository.GetByIdAsync(id);
        if (user == null) return NotFound();
        return Ok(user);
    }

    [HttpPost]
    public async Task<IActionResult> Create([FromBody] User user)
    {
        var created = await _userRepository.AddAsync(user);
        return CreatedAtAction(nameof(GetById), new { id = created.Id }, created);
    }
}
```

---

## 🎯 Summary

### React Essentials
- **Hooks**: useState, useEffect, useContext for state and side effects
- **Custom Hooks**: Reusable logic across components
- **Patterns**: Container/Presentational, Compound Components
- **State Management**: Context API for global state

### Angular Essentials
- **Decorators**: @Component, @Input, @Output, @Injectable
- **Services**: Dependency injection for shared logic
- **RxJS**: Observables for async data streams
- **Forms**: Template-driven vs Reactive forms

### ASP.NET Core Essentials
- **Controllers**: Handle HTTP requests/responses
- **Dependency Injection**: Register and inject services
- **Middleware**: Request/response pipeline
- **Entity Framework**: ORM for database operations
- **Repository Pattern**: Separate data access logic

---

**🔗 Related Resources:**
- [Main Syntax Cheat Sheet](SYNTAX-CHEATSHEET.md)
- [Quick Reference](QUICK-REFERENCE.md)
- [Real-World Examples](REAL-WORLD-EXAMPLES.md)
- [Practice Exercises](PRACTICE-EXERCISES.md)
