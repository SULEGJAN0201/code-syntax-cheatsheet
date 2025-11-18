# Complete Syntax Cheat Sheet - JavaScript, TypeScript & C#

A quick reference guide for Angular/React/.NET developers who need to switch between languages.

---

## Table of Contents
1. [Variables & Types](#variables--types)
2. [Console Output / Printing](#console-output--printing)
3. [Arrays & Collections](#arrays--collections)
4. [Loops & Iteration](#loops--iteration)
5. [Functions & Methods](#functions--methods)
6. [Classes & Objects](#classes--objects)
7. [Conditionals](#conditionals)
8. [String Operations](#string-operations)
9. [Null Handling](#null-handling)
10. [Async/Await](#asyncawait)
11. [Error Handling](#error-handling)
12. [LINQ vs Array Methods](#linq-vs-array-methods)
13. [Interfaces & Types](#interfaces--types)
14. [Generics](#generics)
15. [Destructuring](#destructuring)
16. [Spread/Rest Operators](#spreadrest-operators)
17. [Arrow Functions & Lambdas](#arrow-functions--lambdas)
18. [Date & Time](#date--time)
19. [JSON Operations](#json-operations)
20. [File Operations](#file-operations)

---

## Variables & Types

### Variable Declaration

| JavaScript | TypeScript | C# |
|------------|------------|-----|
| `let name = "John";` | `let name: string = "John";` | `var name = "John";` |
| `const age = 30;` | `const age: number = 30;` | `const int age = 30;` |
| `var old = true;` *(avoid)* | `var old: boolean = true;` | `string name = "John";` |

### Type Annotations

| JavaScript | TypeScript | C# |
|------------|------------|-----|
| *No types* | `let count: number;` | `int count;` |
| *Duck typing* | `let name: string;` | `string name;` |
| - | `let isActive: boolean;` | `bool isActive;` |
| - | `let data: any;` | `object data;` or `dynamic data;` |
| - | `let items: string[];` | `string[] items;` |
| - | `let tuple: [string, number];` | `Tuple<string, int> tuple;` |

### Type Inference

```javascript
// JavaScript
let name = "John";  // inferred as string
```

```typescript
// TypeScript
let name = "John";  // inferred as string
let age = 30;       // inferred as number
```

```csharp
// C#
var name = "John";  // inferred as string
var age = 30;       // inferred as int
```

---

## Console Output / Printing

### Basic Printing

| JavaScript | TypeScript | C# |
|------------|------------|-----|
| `console.log("Hello");` | `console.log("Hello");` | `Console.WriteLine("Hello");` |
| `console.log(name);` | `console.log(name);` | `Console.WriteLine(name);` |
| `console.error("Error");` | `console.error("Error");` | `Console.Error.WriteLine("Error");` |
| `console.warn("Warning");` | `console.warn("Warning");` | `Debug.WriteLine("Warning");` |

### String Interpolation

```javascript
// JavaScript
const name = "John";
const age = 30;
console.log(`Name: ${name}, Age: ${age}`);
console.log("Name: " + name + ", Age: " + age);
```

```typescript
// TypeScript
const name: string = "John";
const age: number = 30;
console.log(`Name: ${name}, Age: ${age}`);
```

```csharp
// C#
var name = "John";
var age = 30;
Console.WriteLine($"Name: {name}, Age: {age}");
Console.WriteLine("Name: " + name + ", Age: " + age);
```

### Formatted Output

```javascript
// JavaScript
console.log("Total: $%.2f", 123.456);  // Limited support
console.log(`Total: $${(123.456).toFixed(2)}`);
```

```typescript
// TypeScript
console.log(`Total: $${(123.456).toFixed(2)}`);
```

```csharp
// C#
Console.WriteLine("Total: ${0:F2}", 123.456);
Console.WriteLine($"Total: ${123.456:F2}");
```

---

## Arrays & Collections

### Array Declaration

| JavaScript | TypeScript | C# |
|------------|------------|-----|
| `const arr = [1, 2, 3];` | `const arr: number[] = [1, 2, 3];` | `int[] arr = {1, 2, 3};` |
| `const arr = [];` | `const arr: string[] = [];` | `var arr = new int[] {1, 2, 3};` |
| `const arr = new Array(5);` | `const arr: number[] = new Array(5);` | `var arr = new int[5];` |

### Lists (Dynamic Arrays)

| JavaScript | TypeScript | C# |
|------------|------------|-----|
| `const list = [];` | `const list: number[] = [];` | `var list = new List<int>();` |
| `list.push(1);` | `list.push(1);` | `list.Add(1);` |
| `list.pop();` | `list.pop();` | `list.RemoveAt(list.Count - 1);` |
| `list.length` | `list.length` | `list.Count` |

### Common Array Operations

```javascript
// JavaScript
const arr = [1, 2, 3, 4, 5];

// Add elements
arr.push(6);              // Add to end
arr.unshift(0);           // Add to beginning
arr.splice(2, 0, 1.5);    // Insert at index 2

// Remove elements
arr.pop();                // Remove from end
arr.shift();              // Remove from beginning
arr.splice(2, 1);         // Remove 1 element at index 2

// Access
const first = arr[0];
const last = arr[arr.length - 1];

// Find
const index = arr.indexOf(3);
const exists = arr.includes(3);
const found = arr.find(x => x > 2);
const foundIndex = arr.findIndex(x => x > 2);

// Slice
const slice = arr.slice(1, 3);  // [arr[1], arr[2]]

// Copy
const copy = [...arr];
const copy2 = arr.slice();
```

```typescript
// TypeScript
const arr: number[] = [1, 2, 3, 4, 5];

// Same as JavaScript with type safety
arr.push(6);
arr.unshift(0);
arr.splice(2, 0, 1.5);

arr.pop();
arr.shift();
arr.splice(2, 1);

const first: number = arr[0];
const last: number = arr[arr.length - 1];

const index: number = arr.indexOf(3);
const exists: boolean = arr.includes(3);
const found: number | undefined = arr.find(x => x > 2);
const foundIndex: number = arr.findIndex(x => x > 2);

const slice: number[] = arr.slice(1, 3);

const copy: number[] = [...arr];
```

```csharp
// C#
var list = new List<int> {1, 2, 3, 4, 5};

// Add elements
list.Add(6);              // Add to end
list.Insert(0, 0);        // Add to beginning
list.Insert(2, 1);        // Insert at index 2

// Remove elements
list.RemoveAt(list.Count - 1);  // Remove from end
list.RemoveAt(0);               // Remove from beginning
list.RemoveAt(2);               // Remove at index 2
list.Remove(3);                 // Remove first occurrence of value 3

// Access
var first = list[0];
var last = list[list.Count - 1];
var first2 = list.First();
var last2 = list.Last();

// Find
var index = list.IndexOf(3);
var exists = list.Contains(3);
var found = list.Find(x => x > 2);
var foundIndex = list.FindIndex(x => x > 2);

// Slice
var slice = list.GetRange(1, 2);  // Start at index 1, take 2 elements
var slice2 = list.Skip(1).Take(2).ToList();

// Copy
var copy = new List<int>(list);
var copy2 = list.ToList();
```

### Multi-dimensional Arrays

```javascript
// JavaScript
const matrix = [
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9]
];
console.log(matrix[1][2]);  // 6
```

```typescript
// TypeScript
const matrix: number[][] = [
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9]
];
console.log(matrix[1][2]);  // 6
```

```csharp
// C#
// Jagged array
int[][] matrix = new int[][] {
    new int[] {1, 2, 3},
    new int[] {4, 5, 6},
    new int[] {7, 8, 9}
};
Console.WriteLine(matrix[1][2]);  // 6

// Multi-dimensional array
int[,] matrix2 = new int[,] {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};
Console.WriteLine(matrix2[1, 2]);  // 6
```

---

## Loops & Iteration

### For Loop

```javascript
// JavaScript
for (let i = 0; i < 10; i++) {
  console.log(i);
}

// For...of (values)
const arr = [1, 2, 3];
for (const item of arr) {
  console.log(item);
}

// For...in (indices/keys)
for (const index in arr) {
  console.log(index, arr[index]);
}
```

```typescript
// TypeScript
for (let i: number = 0; i < 10; i++) {
  console.log(i);
}

const arr: number[] = [1, 2, 3];
for (const item of arr) {
  console.log(item);
}

for (const index in arr) {
  console.log(index, arr[index]);
}
```

```csharp
// C#
for (int i = 0; i < 10; i++) {
    Console.WriteLine(i);
}

// Foreach
var arr = new int[] {1, 2, 3};
foreach (var item in arr) {
    Console.WriteLine(item);
}

// With index
foreach (var (item, index) in arr.Select((value, i) => (value, i))) {
    Console.WriteLine($"{index}: {item}");
}
```

### While Loop

```javascript
// JavaScript
let i = 0;
while (i < 10) {
  console.log(i);
  i++;
}

do {
  console.log(i);
  i++;
} while (i < 10);
```

```typescript
// TypeScript
let i: number = 0;
while (i < 10) {
  console.log(i);
  i++;
}

do {
  console.log(i);
  i++;
} while (i < 10);
```

```csharp
// C#
int i = 0;
while (i < 10) {
    Console.WriteLine(i);
    i++;
}

do {
    Console.WriteLine(i);
    i++;
} while (i < 10);
```

### ForEach

```javascript
// JavaScript
const arr = [1, 2, 3];
arr.forEach((item, index) => {
  console.log(index, item);
});
```

```typescript
// TypeScript
const arr: number[] = [1, 2, 3];
arr.forEach((item: number, index: number) => {
  console.log(index, item);
});
```

```csharp
// C#
var arr = new List<int> {1, 2, 3};
arr.ForEach(item => {
    Console.WriteLine(item);
});

// Or with LINQ
arr.ForEach(item => Console.WriteLine(item));
```

### Break & Continue

| JavaScript | TypeScript | C# |
|------------|------------|-----|
| `break;` | `break;` | `break;` |
| `continue;` | `continue;` | `continue;` |

---

## Functions & Methods

### Function Declaration

```javascript
// JavaScript
function add(a, b) {
  return a + b;
}

const multiply = function(a, b) {
  return a * b;
};

const divide = (a, b) => a / b;

const subtract = (a, b) => {
  return a - b;
};
```

```typescript
// TypeScript
function add(a: number, b: number): number {
  return a + b;
}

const multiply = function(a: number, b: number): number {
  return a * b;
};

const divide = (a: number, b: number): number => a / b;

const subtract = (a: number, b: number): number => {
  return a - b;
};
```

```csharp
// C#
public int Add(int a, int b) {
    return a + b;
}

// Expression-bodied method
public int Multiply(int a, int b) => a * b;

// Local function
int Subtract(int a, int b) {
    return a - b;
}

// Lambda
Func<int, int, int> divide = (a, b) => a / b;
```

### Optional Parameters

```javascript
// JavaScript
function greet(name = "Guest") {
  return `Hello, ${name}`;
}
```

```typescript
// TypeScript
function greet(name: string = "Guest"): string {
  return `Hello, ${name}`;
}

function greet2(name?: string): string {
  return `Hello, ${name ?? "Guest"}`;
}
```

```csharp
// C#
public string Greet(string name = "Guest") {
    return $"Hello, {name}";
}

public string Greet2(string? name = null) {
    return $"Hello, {name ?? "Guest"}";
}
```

### Rest/Params Parameters

```javascript
// JavaScript
function sum(...numbers) {
  return numbers.reduce((a, b) => a + b, 0);
}
sum(1, 2, 3, 4);  // 10
```

```typescript
// TypeScript
function sum(...numbers: number[]): number {
  return numbers.reduce((a, b) => a + b, 0);
}
sum(1, 2, 3, 4);  // 10
```

```csharp
// C#
public int Sum(params int[] numbers) {
    return numbers.Sum();
}
Sum(1, 2, 3, 4);  // 10
```

### Named Parameters

```javascript
// JavaScript (using object destructuring)
function createUser({name, age, email}) {
  return {name, age, email};
}
createUser({name: "John", age: 30, email: "john@example.com"});
```

```typescript
// TypeScript
interface UserParams {
  name: string;
  age: number;
  email: string;
}

function createUser({name, age, email}: UserParams): UserParams {
  return {name, age, email};
}
createUser({name: "John", age: 30, email: "john@example.com"});
```

```csharp
// C#
public User CreateUser(string name, int age, string email) {
    return new User { Name = name, Age = age, Email = email };
}
// Named arguments
CreateUser(name: "John", age: 30, email: "john@example.com");
CreateUser(email: "john@example.com", name: "John", age: 30);
```

---

## Classes & Objects

### Class Declaration

```javascript
// JavaScript
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  greet() {
    return `Hello, I'm ${this.name}`;
  }

  static create(name, age) {
    return new Person(name, age);
  }
}

const person = new Person("John", 30);
```

```typescript
// TypeScript
class Person {
  name: string;
  age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }

  greet(): string {
    return `Hello, I'm ${this.name}`;
  }

  static create(name: string, age: number): Person {
    return new Person(name, age);
  }
}

const person = new Person("John", 30);
```

```csharp
// C#
public class Person {
    public string Name { get; set; }
    public int Age { get; set; }

    public Person(string name, int age) {
        Name = name;
        Age = age;
    }

    public string Greet() {
        return $"Hello, I'm {Name}";
    }

    public static Person Create(string name, int age) {
        return new Person(name, age);
    }
}

var person = new Person("John", 30);
```

### Properties & Access Modifiers

```javascript
// JavaScript (ES2022+)
class User {
  #privateField;      // Private field
  publicField;        // Public field

  constructor(name) {
    this.#privateField = name;
    this.publicField = "public";
  }

  #privateMethod() {
    return "private";
  }

  publicMethod() {
    return this.#privateMethod();
  }
}
```

```typescript
// TypeScript
class User {
  private privateField: string;
  protected protectedField: string;
  public publicField: string;

  constructor(name: string) {
    this.privateField = name;
    this.protectedField = "protected";
    this.publicField = "public";
  }

  private privateMethod(): string {
    return "private";
  }

  public publicMethod(): string {
    return this.privateMethod();
  }
}
```

```csharp
// C#
public class User {
    private string privateField;
    protected string protectedField;
    public string PublicField { get; set; }

    public User(string name) {
        privateField = name;
        protectedField = "protected";
        PublicField = "public";
    }

    private string PrivateMethod() {
        return "private";
    }

    public string PublicMethod() {
        return PrivateMethod();
    }
}
```

### Getters & Setters

```javascript
// JavaScript
class User {
  constructor(firstName, lastName) {
    this._firstName = firstName;
    this._lastName = lastName;
  }

  get fullName() {
    return `${this._firstName} ${this._lastName}`;
  }

  set fullName(value) {
    [this._firstName, this._lastName] = value.split(' ');
  }
}
```

```typescript
// TypeScript
class User {
  private _firstName: string;
  private _lastName: string;

  constructor(firstName: string, lastName: string) {
    this._firstName = firstName;
    this._lastName = lastName;
  }

  get fullName(): string {
    return `${this._firstName} ${this._lastName}`;
  }

  set fullName(value: string) {
    [this._firstName, this._lastName] = value.split(' ');
  }
}
```

```csharp
// C#
public class User {
    private string _firstName;
    private string _lastName;

    public User(string firstName, string lastName) {
        _firstName = firstName;
        _lastName = lastName;
    }

    public string FullName {
        get { return $"{_firstName} {_lastName}"; }
        set {
            var parts = value.Split(' ');
            _firstName = parts[0];
            _lastName = parts[1];
        }
    }

    // Auto-property
    public string Email { get; set; }

    // Read-only property
    public string Id { get; }

    // Init-only property (C# 9+)
    public string Username { get; init; }
}
```

### Inheritance

```javascript
// JavaScript
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
}
```

```typescript
// TypeScript
class Animal {
  name: string;

  constructor(name: string) {
    this.name = name;
  }

  speak(): string {
    return `${this.name} makes a sound`;
  }
}

class Dog extends Animal {
  breed: string;

  constructor(name: string, breed: string) {
    super(name);
    this.breed = breed;
  }

  speak(): string {
    return `${this.name} barks`;
  }
}
```

```csharp
// C#
public class Animal {
    public string Name { get; set; }

    public Animal(string name) {
        Name = name;
    }

    public virtual string Speak() {
        return $"{Name} makes a sound";
    }
}

public class Dog : Animal {
    public string Breed { get; set; }

    public Dog(string name, string breed) : base(name) {
        Breed = breed;
    }

    public override string Speak() {
        return $"{Name} barks";
    }
}
```

---

## Conditionals

### If-Else

```javascript
// JavaScript
if (condition) {
  // code
} else if (otherCondition) {
  // code
} else {
  // code
}

// Ternary
const result = condition ? "yes" : "no";

// Nullish coalescing
const value = null ?? "default";

// Optional chaining
const name = user?.profile?.name;
```

```typescript
// TypeScript
if (condition) {
  // code
} else if (otherCondition) {
  // code
} else {
  // code
}

const result: string = condition ? "yes" : "no";

const value: string = null ?? "default";

const name: string | undefined = user?.profile?.name;
```

```csharp
// C#
if (condition) {
    // code
} else if (otherCondition) {
    // code
} else {
    // code
}

// Ternary
var result = condition ? "yes" : "no";

// Null coalescing
var value = nullableValue ?? "default";

// Null conditional
var name = user?.Profile?.Name;
```

### Switch Statement

```javascript
// JavaScript
switch (value) {
  case 1:
    console.log("One");
    break;
  case 2:
    console.log("Two");
    break;
  default:
    console.log("Other");
}
```

```typescript
// TypeScript
switch (value) {
  case 1:
    console.log("One");
    break;
  case 2:
    console.log("Two");
    break;
  default:
    console.log("Other");
}
```

```csharp
// C#
switch (value) {
    case 1:
        Console.WriteLine("One");
        break;
    case 2:
        Console.WriteLine("Two");
        break;
    default:
        Console.WriteLine("Other");
        break;
}

// Switch expression (C# 8+)
var message = value switch {
    1 => "One",
    2 => "Two",
    _ => "Other"
};

// Pattern matching
var result = obj switch {
    string s => $"String: {s}",
    int i => $"Int: {i}",
    null => "Null",
    _ => "Unknown"
};
```

---

## String Operations

### String Methods

```javascript
// JavaScript
const str = "Hello World";

str.length                    // 11
str.toUpperCase()             // "HELLO WORLD"
str.toLowerCase()             // "hello world"
str.trim()                    // Remove whitespace
str.includes("World")         // true
str.startsWith("Hello")       // true
str.endsWith("World")         // true
str.indexOf("World")          // 6
str.substring(0, 5)           // "Hello"
str.slice(0, 5)               // "Hello"
str.split(" ")                // ["Hello", "World"]
str.replace("World", "JS")    // "Hello JS"
str.replaceAll("o", "0")      // "Hell0 W0rld"
str.charAt(0)                 // "H"
str.repeat(2)                 // "Hello WorldHello World"
str.padStart(15, "*")         // "****Hello World"
str.padEnd(15, "*")           // "Hello World****"
```

```typescript
// TypeScript
const str: string = "Hello World";

str.length                    // 11
str.toUpperCase()             // "HELLO WORLD"
str.toLowerCase()             // "hello world"
str.trim()                    // Remove whitespace
str.includes("World")         // true
str.startsWith("Hello")       // true
str.endsWith("World")         // true
str.indexOf("World")          // 6
str.substring(0, 5)           // "Hello"
str.slice(0, 5)               // "Hello"
str.split(" ")                // ["Hello", "World"]
str.replace("World", "TS")    // "Hello TS"
str.replaceAll("o", "0")      // "Hell0 W0rld"
str.charAt(0)                 // "H"
```

```csharp
// C#
var str = "Hello World";

str.Length                        // 11
str.ToUpper()                     // "HELLO WORLD"
str.ToLower()                     // "hello world"
str.Trim()                        // Remove whitespace
str.Contains("World")             // true
str.StartsWith("Hello")           // true
str.EndsWith("World")             // true
str.IndexOf("World")              // 6
str.Substring(0, 5)               // "Hello"
str.Split(" ")                    // ["Hello", "World"]
str.Replace("World", "C#")        // "Hello C#"
str[0]                            // 'H'
string.Concat(str, str)           // "Hello WorldHello World"
str.PadLeft(15, '*')              // "****Hello World"
str.PadRight(15, '*')             // "Hello World****"
```

### String Interpolation

```javascript
// JavaScript
const name = "John";
const age = 30;
const message = `${name} is ${age} years old`;
```

```typescript
// TypeScript
const name: string = "John";
const age: number = 30;
const message: string = `${name} is ${age} years old`;
```

```csharp
// C#
var name = "John";
var age = 30;
var message = $"{name} is {age} years old";
```

### Multi-line Strings

```javascript
// JavaScript
const multiline = `
  Line 1
  Line 2
  Line 3
`;
```

```typescript
// TypeScript
const multiline: string = `
  Line 1
  Line 2
  Line 3
`;
```

```csharp
// C#
var multiline = @"
  Line 1
  Line 2
  Line 3
";

// Or (C# 11+)
var multiline2 = """
  Line 1
  Line 2
  Line 3
  """;
```

---

## Null Handling

### Null/Undefined Checks

```javascript
// JavaScript
if (value === null) { }
if (value === undefined) { }
if (value == null) { }  // Checks both null and undefined
if (!value) { }         // Checks falsy values

// Nullish coalescing
const result = value ?? "default";

// Optional chaining
const name = user?.name;
const street = user?.address?.street;
```

```typescript
// TypeScript
if (value === null) { }
if (value === undefined) { }
if (value == null) { }

const result: string = value ?? "default";

const name: string | undefined = user?.name;
const street: string | undefined = user?.address?.street;

// Type guards
if (value !== null && value !== undefined) {
  // value is not null or undefined here
}
```

```csharp
// C#
if (value == null) { }
if (value is null) { }

// Null coalescing
var result = value ?? "default";

// Null conditional
var name = user?.Name;
var street = user?.Address?.Street;

// Null forgiving operator
var length = value!.Length;  // Assert not null

// Pattern matching
if (value is not null) {
    // value is not null here
}
```

---

## Async/Await

### Promises & Async/Await

```javascript
// JavaScript
// Promise
function fetchData() {
  return new Promise((resolve, reject) => {
    setTimeout(() => resolve("Data"), 1000);
  });
}

// Async/Await
async function getData() {
  try {
    const data = await fetchData();
    console.log(data);
  } catch (error) {
    console.error(error);
  }
}

// Multiple awaits
async function getMultipleData() {
  const [data1, data2] = await Promise.all([
    fetchData1(),
    fetchData2()
  ]);
}
```

```typescript
// TypeScript
function fetchData(): Promise<string> {
  return new Promise((resolve, reject) => {
    setTimeout(() => resolve("Data"), 1000);
  });
}

async function getData(): Promise<void> {
  try {
    const data: string = await fetchData();
    console.log(data);
  } catch (error) {
    console.error(error);
  }
}

async function getMultipleData(): Promise<void> {
  const [data1, data2] = await Promise.all([
    fetchData1(),
    fetchData2()
  ]);
}
```

```csharp
// C#
public async Task<string> FetchDataAsync() {
    await Task.Delay(1000);
    return "Data";
}

public async Task GetDataAsync() {
    try {
        var data = await FetchDataAsync();
        Console.WriteLine(data);
    } catch (Exception error) {
        Console.Error.WriteLine(error);
    }
}

// Multiple awaits
public async Task GetMultipleDataAsync() {
    var tasks = new[] {
        FetchData1Async(),
        FetchData2Async()
    };
    var results = await Task.WhenAll(tasks);
}

// ConfigureAwait
var data = await FetchDataAsync().ConfigureAwait(false);
```

---

## Error Handling

### Try-Catch

```javascript
// JavaScript
try {
  // code
} catch (error) {
  console.error(error.message);
} finally {
  // cleanup
}

// Throwing errors
throw new Error("Something went wrong");
throw new TypeError("Invalid type");
```

```typescript
// TypeScript
try {
  // code
} catch (error) {
  if (error instanceof Error) {
    console.error(error.message);
  }
} finally {
  // cleanup
}

throw new Error("Something went wrong");
```

```csharp
// C#
try {
    // code
} catch (Exception ex) {
    Console.Error.WriteLine(ex.Message);
} finally {
    // cleanup
}

// Specific exceptions
try {
    // code
} catch (ArgumentNullException ex) {
    // handle
} catch (InvalidOperationException ex) {
    // handle
} catch (Exception ex) {
    // handle all others
}

// Throwing exceptions
throw new Exception("Something went wrong");
throw new ArgumentNullException(nameof(parameter));
```

---

## LINQ vs Array Methods

### Map / Select

```javascript
// JavaScript
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map(x => x * 2);  // [2, 4, 6, 8, 10]
```

```typescript
// TypeScript
const numbers: number[] = [1, 2, 3, 4, 5];
const doubled: number[] = numbers.map(x => x * 2);
```

```csharp
// C#
var numbers = new List<int> {1, 2, 3, 4, 5};
var doubled = numbers.Select(x => x * 2).ToList();
```

### Filter / Where

```javascript
// JavaScript
const numbers = [1, 2, 3, 4, 5];
const evens = numbers.filter(x => x % 2 === 0);  // [2, 4]
```

```typescript
// TypeScript
const numbers: number[] = [1, 2, 3, 4, 5];
const evens: number[] = numbers.filter(x => x % 2 === 0);
```

```csharp
// C#
var numbers = new List<int> {1, 2, 3, 4, 5};
var evens = numbers.Where(x => x % 2 == 0).ToList();
```

### Reduce / Aggregate

```javascript
// JavaScript
const numbers = [1, 2, 3, 4, 5];
const sum = numbers.reduce((acc, x) => acc + x, 0);  // 15
```

```typescript
// TypeScript
const numbers: number[] = [1, 2, 3, 4, 5];
const sum: number = numbers.reduce((acc, x) => acc + x, 0);
```

```csharp
// C#
var numbers = new List<int> {1, 2, 3, 4, 5};
var sum = numbers.Aggregate(0, (acc, x) => acc + x);
// Or simpler
var sum2 = numbers.Sum();
```

### Find / FirstOrDefault

```javascript
// JavaScript
const numbers = [1, 2, 3, 4, 5];
const found = numbers.find(x => x > 3);  // 4
const foundIndex = numbers.findIndex(x => x > 3);  // 3
```

```typescript
// TypeScript
const numbers: number[] = [1, 2, 3, 4, 5];
const found: number | undefined = numbers.find(x => x > 3);
```

```csharp
// C#
var numbers = new List<int> {1, 2, 3, 4, 5};
var found = numbers.FirstOrDefault(x => x > 3);  // 4
var foundOrNull = numbers.FirstOrDefault(x => x > 10);  // 0 (default for int)
```

### Every / All

```javascript
// JavaScript
const numbers = [1, 2, 3, 4, 5];
const allPositive = numbers.every(x => x > 0);  // true
```

```typescript
// TypeScript
const numbers: number[] = [1, 2, 3, 4, 5];
const allPositive: boolean = numbers.every(x => x > 0);
```

```csharp
// C#
var numbers = new List<int> {1, 2, 3, 4, 5};
var allPositive = numbers.All(x => x > 0);  // true
```

### Some / Any

```javascript
// JavaScript
const numbers = [1, 2, 3, 4, 5];
const hasEven = numbers.some(x => x % 2 === 0);  // true
```

```typescript
// TypeScript
const numbers: number[] = [1, 2, 3, 4, 5];
const hasEven: boolean = numbers.some(x => x % 2 === 0);
```

```csharp
// C#
var numbers = new List<int> {1, 2, 3, 4, 5};
var hasEven = numbers.Any(x => x % 2 == 0);  // true
```

### Sort / OrderBy

```javascript
// JavaScript
const numbers = [3, 1, 4, 1, 5];
numbers.sort();  // Mutates: [1, 1, 3, 4, 5]
numbers.sort((a, b) => b - a);  // Descending: [5, 4, 3, 1, 1]

const sorted = [...numbers].sort();  // Non-mutating
```

```typescript
// TypeScript
const numbers: number[] = [3, 1, 4, 1, 5];
const sorted: number[] = [...numbers].sort((a, b) => a - b);
```

```csharp
// C#
var numbers = new List<int> {3, 1, 4, 1, 5};
var sorted = numbers.OrderBy(x => x).ToList();  // [1, 1, 3, 4, 5]
var sortedDesc = numbers.OrderByDescending(x => x).ToList();

// In-place sort
numbers.Sort();
```

### GroupBy

```javascript
// JavaScript
const people = [
  {name: "John", age: 30},
  {name: "Jane", age: 25},
  {name: "Bob", age: 30}
];

const grouped = people.reduce((acc, person) => {
  (acc[person.age] = acc[person.age] || []).push(person);
  return acc;
}, {});
```

```typescript
// TypeScript
interface Person {
  name: string;
  age: number;
}

const people: Person[] = [
  {name: "John", age: 30},
  {name: "Jane", age: 25},
  {name: "Bob", age: 30}
];

const grouped = people.reduce((acc, person) => {
  (acc[person.age] = acc[person.age] || []).push(person);
  return acc;
}, {} as Record<number, Person[]>);
```

```csharp
// C#
var people = new List<Person> {
    new Person {Name = "John", Age = 30},
    new Person {Name = "Jane", Age = 25},
    new Person {Name = "Bob", Age = 30}
};

var grouped = people.GroupBy(p => p.Age)
    .ToDictionary(g => g.Key, g => g.ToList());

// Or as IGrouping
var grouped2 = people.GroupBy(p => p.Age);
foreach (var group in grouped2) {
    Console.WriteLine($"Age {group.Key}: {group.Count()} people");
}
```

### Distinct

```javascript
// JavaScript
const numbers = [1, 2, 2, 3, 3, 3, 4];
const unique = [...new Set(numbers)];  // [1, 2, 3, 4]
```

```typescript
// TypeScript
const numbers: number[] = [1, 2, 2, 3, 3, 3, 4];
const unique: number[] = [...new Set(numbers)];
```

```csharp
// C#
var numbers = new List<int> {1, 2, 2, 3, 3, 3, 4};
var unique = numbers.Distinct().ToList();  // [1, 2, 3, 4]
```

### Skip / Take

```javascript
// JavaScript
const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const skipped = numbers.slice(3);  // [4, 5, 6, 7, 8, 9, 10]
const taken = numbers.slice(0, 3);  // [1, 2, 3]
const skipAndTake = numbers.slice(3, 6);  // [4, 5, 6]
```

```typescript
// TypeScript
const numbers: number[] = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const skipped: number[] = numbers.slice(3);
const taken: number[] = numbers.slice(0, 3);
```

```csharp
// C#
var numbers = new List<int> {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
var skipped = numbers.Skip(3).ToList();
var taken = numbers.Take(3).ToList();
var skipAndTake = numbers.Skip(3).Take(3).ToList();
```

### Join

```javascript
// JavaScript
const users = [
  {id: 1, name: "John"},
  {id: 2, name: "Jane"}
];
const orders = [
  {userId: 1, product: "Book"},
  {userId: 2, product: "Pen"}
];

const joined = users.map(user => ({
  ...user,
  orders: orders.filter(o => o.userId === user.id)
}));
```

```typescript
// TypeScript
interface User {
  id: number;
  name: string;
}
interface Order {
  userId: number;
  product: string;
}

const users: User[] = [
  {id: 1, name: "John"},
  {id: 2, name: "Jane"}
];
const orders: Order[] = [
  {userId: 1, product: "Book"},
  {userId: 2, product: "Pen"}
];

const joined = users.map(user => ({
  ...user,
  orders: orders.filter(o => o.userId === user.id)
}));
```

```csharp
// C#
var users = new List<User> {
    new User {Id = 1, Name = "John"},
    new User {Id = 2, Name = "Jane"}
};
var orders = new List<Order> {
    new Order {UserId = 1, Product = "Book"},
    new Order {UserId = 2, Product = "Pen"}
};

var joined = users.Join(
    orders,
    user => user.Id,
    order => order.UserId,
    (user, order) => new { user.Name, order.Product }
).ToList();

// Or with group join
var grouped = users.GroupJoin(
    orders,
    user => user.Id,
    order => order.UserId,
    (user, userOrders) => new { user.Name, Orders = userOrders.ToList() }
).ToList();
```

---

## Interfaces & Types

### Interface Definition

```javascript
// JavaScript (no interfaces, just JSDoc)
/**
 * @typedef {Object} User
 * @property {string} name
 * @property {number} age
 */
```

```typescript
// TypeScript
interface User {
  name: string;
  age: number;
  email?: string;  // Optional
  readonly id: string;  // Readonly
}

// Type alias
type UserType = {
  name: string;
  age: number;
};

// Extending
interface Admin extends User {
  role: string;
}

// Implementing
class UserImpl implements User {
  name: string;
  age: number;
  id: string;

  constructor(name: string, age: number, id: string) {
    this.name = name;
    this.age = age;
    this.id = id;
  }
}
```

```csharp
// C#
public interface IUser {
    string Name { get; set; }
    int Age { get; set; }
    string Email { get; set; }  // Optional via nullable
    string Id { get; }  // Readonly (get only)
}

// Implementing
public class User : IUser {
    public string Name { get; set; }
    public int Age { get; set; }
    public string? Email { get; set; }
    public string Id { get; }

    public User(string id) {
        Id = id;
    }
}

// Multiple interfaces
public class Admin : IUser, IAdmin {
    // Implementation
}
```

### Union & Intersection Types

```typescript
// TypeScript
type ID = string | number;  // Union
type Admin = User & { role: string };  // Intersection

function printId(id: ID) {
  if (typeof id === "string") {
    console.log(id.toUpperCase());
  } else {
    console.log(id);
  }
}
```

```csharp
// C# (no direct equivalent, use generics or inheritance)
// Can use 'object' but loses type safety
public void PrintId(object id) {
    if (id is string s) {
        Console.WriteLine(s.ToUpper());
    } else if (id is int i) {
        Console.WriteLine(i);
    }
}

// Or use generics with constraints
public void PrintId<T>(T id) where T : IConvertible {
    Console.WriteLine(id);
}
```

---

## Generics

### Generic Functions

```typescript
// TypeScript
function identity<T>(value: T): T {
  return value;
}

const num = identity<number>(42);
const str = identity<string>("hello");
const auto = identity(42);  // Type inference
```

```csharp
// C#
public T Identity<T>(T value) {
    return value;
}

var num = Identity<int>(42);
var str = Identity<string>("hello");
var auto = Identity(42);  // Type inference
```

### Generic Classes

```typescript
// TypeScript
class Box<T> {
  private value: T;

  constructor(value: T) {
    this.value = value;
  }

  getValue(): T {
    return this.value;
  }
}

const numBox = new Box<number>(42);
const strBox = new Box<string>("hello");
```

```csharp
// C#
public class Box<T> {
    private T value;

    public Box(T value) {
        this.value = value;
    }

    public T GetValue() {
        return value;
    }
}

var numBox = new Box<int>(42);
var strBox = new Box<string>("hello");
```

### Generic Constraints

```typescript
// TypeScript
interface HasLength {
  length: number;
}

function logLength<T extends HasLength>(item: T): void {
  console.log(item.length);
}

logLength("hello");  // OK
logLength([1, 2, 3]);  // OK
// logLength(42);  // Error
```

```csharp
// C#
public interface IHasLength {
    int Length { get; }
}

public void LogLength<T>(T item) where T : IHasLength {
    Console.WriteLine(item.Length);
}

// Common constraints
public void Example<T>() where T : class { }  // Reference type
public void Example<T>() where T : struct { }  // Value type
public void Example<T>() where T : new() { }  // Has parameterless constructor
public void Example<T>() where T : BaseClass { }  // Inherits from BaseClass
public void Example<T>() where T : IInterface { }  // Implements interface
```

---

## Destructuring

### Array Destructuring

```javascript
// JavaScript
const arr = [1, 2, 3, 4, 5];
const [first, second] = arr;
const [a, , c] = arr;  // Skip elements
const [head, ...tail] = arr;  // Rest
```

```typescript
// TypeScript
const arr: number[] = [1, 2, 3, 4, 5];
const [first, second]: number[] = arr;
const [a, , c]: number[] = arr;
const [head, ...tail]: number[] = arr;
```

```csharp
// C# (Deconstruction)
var arr = new[] {1, 2, 3, 4, 5};
var (first, second) = (arr[0], arr[1]);

// Tuple deconstruction
var (a, b, c) = (1, 2, 3);

// Custom deconstruction
public class Point {
    public int X { get; set; }
    public int Y { get; set; }

    public void Deconstruct(out int x, out int y) {
        x = X;
        y = Y;
    }
}

var point = new Point { X = 10, Y = 20 };
var (x, y) = point;
```

### Object Destructuring

```javascript
// JavaScript
const user = {name: "John", age: 30, email: "john@example.com"};
const {name, age} = user;
const {name: userName, age: userAge} = user;  // Rename
const {name, ...rest} = user;  // Rest
const {address = "Unknown"} = user;  // Default
```

```typescript
// TypeScript
interface User {
  name: string;
  age: number;
  email: string;
}

const user: User = {name: "John", age: 30, email: "john@example.com"};
const {name, age}: {name: string, age: number} = user;
const {name: userName, age: userAge} = user;
const {name, ...rest} = user;
```

```csharp
// C# (limited support)
// Named tuples
var user = (Name: "John", Age: 30, Email: "john@example.com");
var (name, age, email) = user;
var (userName, _, _) = user;  // Discard

// With records (C# 9+)
public record User(string Name, int Age, string Email);
var user = new User("John", 30, "john@example.com");
var (name, age, email) = user;
```

---

## Spread/Rest Operators

### Spread

```javascript
// JavaScript
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const combined = [...arr1, ...arr2];

const obj1 = {a: 1, b: 2};
const obj2 = {c: 3, d: 4};
const merged = {...obj1, ...obj2};

// Function arguments
Math.max(...arr1);
```

```typescript
// TypeScript
const arr1: number[] = [1, 2, 3];
const arr2: number[] = [4, 5, 6];
const combined: number[] = [...arr1, ...arr2];

const obj1: {a: number, b: number} = {a: 1, b: 2};
const obj2: {c: number, d: number} = {c: 3, d: 4};
const merged = {...obj1, ...obj2};
```

```csharp
// C# (no direct equivalent)
var arr1 = new[] {1, 2, 3};
var arr2 = new[] {4, 5, 6};
var combined = arr1.Concat(arr2).ToArray();

// For objects, manual copy or use libraries
var obj1 = new {a = 1, b = 2};
var obj2 = new {c = 3, d = 4};
// No built-in spread for objects
```

### Rest

```javascript
// JavaScript
function sum(...numbers) {
  return numbers.reduce((a, b) => a + b, 0);
}
sum(1, 2, 3, 4);
```

```typescript
// TypeScript
function sum(...numbers: number[]): number {
  return numbers.reduce((a, b) => a + b, 0);
}
```

```csharp
// C#
public int Sum(params int[] numbers) {
    return numbers.Sum();
}
Sum(1, 2, 3, 4);
```

---

## Arrow Functions & Lambdas

### Arrow Functions

```javascript
// JavaScript
const add = (a, b) => a + b;
const square = x => x * x;
const greet = () => "Hello";
const complex = (a, b) => {
  const result = a + b;
  return result;
};
```

```typescript
// TypeScript
const add = (a: number, b: number): number => a + b;
const square = (x: number): number => x * x;
const greet = (): string => "Hello";
```

```csharp
// C#
Func<int, int, int> add = (a, b) => a + b;
Func<int, int> square = x => x * x;
Func<string> greet = () => "Hello";
Func<int, int, int> complex = (a, b) => {
    var result = a + b;
    return result;
};

// Or use local functions
int Add(int a, int b) => a + b;
```

### Higher-order Functions

```javascript
// JavaScript
const multiplyBy = (factor) => (number) => number * factor;
const double = multiplyBy(2);
double(5);  // 10
```

```typescript
// TypeScript
const multiplyBy = (factor: number) => (number: number): number => number * factor;
const double = multiplyBy(2);
double(5);  // 10
```

```csharp
// C#
Func<int, Func<int, int>> MultiplyBy = factor => number => number * factor;
var double = MultiplyBy(2);
double(5);  // 10
```

---

## Date & Time

### Creating Dates

```javascript
// JavaScript
const now = new Date();
const specific = new Date(2024, 0, 15);  // Month is 0-indexed
const fromString = new Date("2024-01-15");
const fromTimestamp = new Date(1705276800000);
```

```typescript
// TypeScript
const now: Date = new Date();
const specific: Date = new Date(2024, 0, 15);
const fromString: Date = new Date("2024-01-15");
```

```csharp
// C#
var now = DateTime.Now;
var utcNow = DateTime.UtcNow;
var specific = new DateTime(2024, 1, 15);  // Month is 1-indexed
var fromString = DateTime.Parse("2024-01-15");
var fromStringExact = DateTime.ParseExact("2024-01-15", "yyyy-MM-dd", null);
```

### Date Operations

```javascript
// JavaScript
const date = new Date();
date.getFullYear();
date.getMonth();  // 0-11
date.getDate();   // 1-31
date.getHours();
date.getMinutes();
date.getSeconds();

date.setFullYear(2025);
date.setMonth(11);
date.setDate(25);

const timestamp = date.getTime();
const isoString = date.toISOString();
const dateString = date.toDateString();
```

```typescript
// TypeScript
const date: Date = new Date();
const year: number = date.getFullYear();
const month: number = date.getMonth();
```

```csharp
// C#
var date = DateTime.Now;
date.Year;
date.Month;  // 1-12
date.Day;    // 1-31
date.Hour;
date.Minute;
date.Second;

var modified = date.AddYears(1);
modified = date.AddMonths(1);
modified = date.AddDays(1);

var timestamp = new DateTimeOffset(date).ToUnixTimeMilliseconds();
var isoString = date.ToString("o");
var dateString = date.ToString("yyyy-MM-dd");
```

### Date Comparison

```javascript
// JavaScript
const date1 = new Date("2024-01-15");
const date2 = new Date("2024-01-20");

date1 < date2;   // true
date1 > date2;   // false
date1.getTime() === date2.getTime();
```

```typescript
// TypeScript
const date1: Date = new Date("2024-01-15");
const date2: Date = new Date("2024-01-20");
const isEqual: boolean = date1.getTime() === date2.getTime();
```

```csharp
// C#
var date1 = new DateTime(2024, 1, 15);
var date2 = new DateTime(2024, 1, 20);

date1 < date2;   // true
date1 > date2;   // false
date1 == date2;  // false

// Compare
DateTime.Compare(date1, date2);  // -1, 0, or 1
```

---

## JSON Operations

### JSON Stringify/Parse

```javascript
// JavaScript
const obj = {name: "John", age: 30};
const json = JSON.stringify(obj);
const parsed = JSON.parse(json);

// Pretty print
const pretty = JSON.stringify(obj, null, 2);
```

```typescript
// TypeScript
interface User {
  name: string;
  age: number;
}

const obj: User = {name: "John", age: 30};
const json: string = JSON.stringify(obj);
const parsed: User = JSON.parse(json);
```

```csharp
// C# (using System.Text.Json)
using System.Text.Json;

var obj = new {Name = "John", Age = 30};
var json = JsonSerializer.Serialize(obj);
var parsed = JsonSerializer.Deserialize<User>(json);

// Pretty print
var options = new JsonSerializerOptions { WriteIndented = true };
var pretty = JsonSerializer.Serialize(obj, options);

// Or with Newtonsoft.Json
using Newtonsoft.Json;
var json2 = JsonConvert.SerializeObject(obj);
var parsed2 = JsonConvert.DeserializeObject<User>(json);
```

---

## File Operations

### Reading Files

```javascript
// JavaScript (Node.js)
const fs = require('fs');

// Sync
const content = fs.readFileSync('file.txt', 'utf8');

// Async (callback)
fs.readFile('file.txt', 'utf8', (err, data) => {
  if (err) throw err;
  console.log(data);
});

// Async (Promise)
const fsPromises = require('fs').promises;
const content = await fsPromises.readFile('file.txt', 'utf8');
```

```typescript
// TypeScript (Node.js)
import * as fs from 'fs';
import { promises as fsPromises } from 'fs';

const content: string = fs.readFileSync('file.txt', 'utf8');

const contentAsync: string = await fsPromises.readFile('file.txt', 'utf8');
```

```csharp
// C#
using System.IO;

// Sync
var content = File.ReadAllText("file.txt");
var lines = File.ReadAllLines("file.txt");
var bytes = File.ReadAllBytes("file.txt");

// Async
var contentAsync = await File.ReadAllTextAsync("file.txt");
var linesAsync = await File.ReadAllLinesAsync("file.txt");
```

### Writing Files

```javascript
// JavaScript (Node.js)
const fs = require('fs');

// Sync
fs.writeFileSync('file.txt', 'Hello World');

// Async
const fsPromises = require('fs').promises;
await fsPromises.writeFile('file.txt', 'Hello World');

// Append
fs.appendFileSync('file.txt', 'More content');
```

```typescript
// TypeScript
import { promises as fsPromises } from 'fs';

await fsPromises.writeFile('file.txt', 'Hello World');
await fsPromises.appendFile('file.txt', 'More content');
```

```csharp
// C#
// Sync
File.WriteAllText("file.txt", "Hello World");
File.WriteAllLines("file.txt", new[] {"Line 1", "Line 2"});

// Async
await File.WriteAllTextAsync("file.txt", "Hello World");
await File.WriteAllLinesAsync("file.txt", new[] {"Line 1", "Line 2"});

// Append
File.AppendAllText("file.txt", "More content");
await File.AppendAllTextAsync("file.txt", "More content");
```

### File System Operations

```javascript
// JavaScript (Node.js)
const fs = require('fs');

// Check existence
fs.existsSync('file.txt');

// Delete
fs.unlinkSync('file.txt');

// Directory operations
fs.mkdirSync('mydir');
fs.rmdirSync('mydir');
fs.readdirSync('mydir');
```

```typescript
// TypeScript
import * as fs from 'fs';

const exists: boolean = fs.existsSync('file.txt');
fs.unlinkSync('file.txt');
fs.mkdirSync('mydir');
const files: string[] = fs.readdirSync('mydir');
```

```csharp
// C#
// Check existence
File.Exists("file.txt");
Directory.Exists("mydir");

// Delete
File.Delete("file.txt");

// Directory operations
Directory.CreateDirectory("mydir");
Directory.Delete("mydir");
var files = Directory.GetFiles("mydir");
var dirs = Directory.GetDirectories("mydir");
```

---

## Additional Quick References

### Dictionary/Map/Object

```javascript
// JavaScript
const map = new Map();
map.set('key', 'value');
map.get('key');
map.has('key');
map.delete('key');
map.size;

// Object as map
const obj = {};
obj['key'] = 'value';
obj.key = 'value';
delete obj.key;
Object.keys(obj);
Object.values(obj);
Object.entries(obj);
```

```typescript
// TypeScript
const map: Map<string, string> = new Map();
map.set('key', 'value');
const value: string | undefined = map.get('key');

// Record type
const obj: Record<string, string> = {};
obj['key'] = 'value';
```

```csharp
// C#
var dict = new Dictionary<string, string>();
dict["key"] = "value";
dict.Add("key2", "value2");
var value = dict["key"];
dict.ContainsKey("key");
dict.Remove("key");
dict.Count;

// Iteration
foreach (var kvp in dict) {
    Console.WriteLine($"{kvp.Key}: {kvp.Value}");
}

dict.Keys;
dict.Values;
```

### Set

```javascript
// JavaScript
const set = new Set([1, 2, 3]);
set.add(4);
set.has(2);
set.delete(2);
set.size;
set.clear();

// Convert to array
const arr = [...set];
```

```typescript
// TypeScript
const set: Set<number> = new Set([1, 2, 3]);
set.add(4);
const has: boolean = set.has(2);
```

```csharp
// C#
var set = new HashSet<int> {1, 2, 3};
set.Add(4);
set.Contains(2);
set.Remove(2);
set.Count;
set.Clear();

// Convert to array
var arr = set.ToArray();
```

### Regular Expressions

```javascript
// JavaScript
const regex = /pattern/gi;
const regex2 = new RegExp('pattern', 'gi');

'hello'.match(/l+/g);
'hello'.replace(/l/g, 'L');
/hello/.test('hello world');
regex.exec('hello');
```

```typescript
// TypeScript
const regex: RegExp = /pattern/gi;
const matches: RegExpMatchArray | null = 'hello'.match(/l+/g);
```

```csharp
// C#
using System.Text.RegularExpressions;

var regex = new Regex(@"pattern", RegexOptions.IgnoreCase);
var matches = regex.Matches("hello");
var isMatch = regex.IsMatch("hello");
var replaced = regex.Replace("hello", "HELLO");

// Static methods
Regex.IsMatch("hello", @"pattern");
Regex.Replace("hello", @"l", "L");
```

### Math Operations

```javascript
// JavaScript
Math.abs(-5);
Math.ceil(4.3);
Math.floor(4.7);
Math.round(4.5);
Math.max(1, 2, 3);
Math.min(1, 2, 3);
Math.pow(2, 3);
Math.sqrt(16);
Math.random();  // 0 to 1
Math.PI;
Math.E;
```

```typescript
// TypeScript
const abs: number = Math.abs(-5);
const max: number = Math.max(1, 2, 3);
```

```csharp
// C#
Math.Abs(-5);
Math.Ceiling(4.3);
Math.Floor(4.7);
Math.Round(4.5);
Math.Max(1, 2);
Math.Min(1, 2);
Math.Pow(2, 3);
Math.Sqrt(16);
new Random().NextDouble();  // 0 to 1
Math.PI;
Math.E;
```

---

## Common Patterns & Best Practices

### Null Safety

```javascript
// JavaScript
const value = obj?.property ?? "default";
const result = arr?.[0];
```

```typescript
// TypeScript
const value: string = obj?.property ?? "default";
const result: number | undefined = arr?.[0];

// Non-null assertion (use carefully)
const length: number = str!.length;
```

```csharp
// C#
var value = obj?.Property ?? "default";
var result = arr?[0];

// Null-forgiving operator
var length = str!.Length;
```

### Immutability

```javascript
// JavaScript
const arr = [1, 2, 3];
const newArr = [...arr, 4];  // Don't mutate original

const obj = {a: 1};
const newObj = {...obj, b: 2};
```

```typescript
// TypeScript
const arr: readonly number[] = [1, 2, 3];
// arr.push(4);  // Error

interface User {
  readonly id: string;
  name: string;
}
```

```csharp
// C#
var arr = new[] {1, 2, 3}.ToImmutableList();
var newArr = arr.Add(4);

// Readonly collection
IReadOnlyList<int> readonlyList = new List<int> {1, 2, 3};

// Init-only properties
public string Id { get; init; }
```

---

This cheat sheet covers the most common syntax patterns you'll encounter when switching between JavaScript, TypeScript, and C#. Bookmark it for quick reference!
