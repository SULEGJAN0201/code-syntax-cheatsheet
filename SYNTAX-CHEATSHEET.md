# Beginner-Friendly Syntax Cheat Sheet
## JavaScript, TypeScript & C# - Side by Side

> **Quick Guide**: This cheat sheet helps you quickly remember syntax when switching between JavaScript, TypeScript, and C#. Each example includes comments showing the output!

---

## Table of Contents

### 🟢 Basics (Start Here!)
1. [Variables & Types](#variables--types)
2. [Console Output / Printing](#console-output--printing)
3. [Type Conversion & Parsing](#type-conversion--parsing)
4. [Operators & Math](#operators--math)

### 🟢 Working with Data
5. [Arrays & Collections](#arrays--collections)
6. [String Operations](#string-operations)
7. [Common Data Structures](#common-data-structures)
8. [Working with Objects](#working-with-objects)

### 🟡 Control Flow
9. [Conditionals (If/Else/Switch)](#conditionals)
10. [Loops & Iteration](#loops--iteration)
11. [Functions & Methods](#functions--methods)
12. [Arrow Functions & Lambdas](#arrow-functions--lambdas)

### 🟡 Object-Oriented Programming
13. [Classes & Objects](#classes--objects)
14. [Interfaces & Types](#interfaces--types)
15. [Enums](#enums)
16. [Generics](#generics)

### 🟡 Advanced Concepts
17. [Array Methods & LINQ](#array-methods--linq)
18. [Destructuring](#destructuring)
19. [Spread/Rest Operators](#spreadrest-operators)
20. [Null Handling](#null-handling)
21. [Regular Expressions](#regular-expressions)

### 🔴 Asynchronous Programming
22. [Async/Await](#asyncawait)
23. [Promises & Tasks](#promises--tasks)
24. [HTTP/API Calls](#httpapi-calls)

### 🔴 Modules & I/O
25. [Modules & Imports](#modules--imports)
26. [Date & Time](#date--time)
27. [JSON Operations](#json-operations)
28. [File Operations](#file-operations)
29. [Local Storage](#local-storage)

### ⚠️ Error Handling & Best Practices
30. [Error Handling](#error-handling)
31. [Common Gotchas & Pitfalls](#common-gotchas--pitfalls)
32. [Tips & Common Mistakes](#tips--common-mistakes)

---

## Variables & Types

**What it does**: Variables store data. Types define what kind of data (text, numbers, true/false, etc.)

### Basic Variable Declaration

```javascript
// JavaScript - use let for variables that can change, const for constants
let name = "John";           // Can be changed later
const age = 30;              // Cannot be changed
var old = true;              // Old way - avoid using var

console.log(name);           // Output: John
console.log(age);            // Output: 30
```

```typescript
// TypeScript - same as JavaScript but you can specify types
let name: string = "John";   // This is text (string)
const age: number = 30;      // This is a number
let isActive: boolean = true; // This is true/false

console.log(name);           // Output: John
console.log(age);            // Output: 30
```

```csharp
// C# - you must specify types (or use 'var' to let C# figure it out)
string name = "John";        // This is text (string)
int age = 30;                // This is a whole number (integer)
bool isActive = true;        // This is true/false (boolean)
const int maxUsers = 100;    // Constant value

// Using 'var' - C# figures out the type automatically
var name2 = "John";          // C# knows this is a string
var age2 = 30;               // C# knows this is an int

Console.WriteLine(name);     // Output: John
Console.WriteLine(age);      // Output: 30
```

### Common Types Comparison

| Type | JavaScript | TypeScript | C# | Example |
|------|------------|------------|-----|---------|
| Text | `let name = "John"` | `let name: string = "John"` | `string name = "John"` | "Hello", "ABC" |
| Number | `let age = 30` | `let age: number = 30` | `int age = 30` | 25, 100, -5 |
| Decimal | `let price = 9.99` | `let price: number = 9.99` | `double price = 9.99` | 3.14, 0.5 |
| True/False | `let active = true` | `let active: boolean = true` | `bool active = true` | true, false |
| Any type | `let data = anything` | `let data: any = anything` | `object data = anything` | Mixed types |

---

## Console Output / Printing

**What it does**: Shows text/data in the console (for debugging or displaying information)

### Simple Printing

```javascript
// JavaScript - console.log() prints to the console
console.log("Hello World");           // Output: Hello World
console.log(42);                      // Output: 42
console.log(true);                    // Output: true

// Printing multiple values
console.log("Age:", 30);              // Output: Age: 30
console.log("Name:", "John", "Age:", 30); // Output: Name: John Age: 30
```

```typescript
// TypeScript - exactly the same as JavaScript
console.log("Hello World");           // Output: Hello World
console.log("Age:", 30);              // Output: Age: 30
```

```csharp
// C# - Console.WriteLine() prints to the console
Console.WriteLine("Hello World");     // Output: Hello World
Console.WriteLine(42);                // Output: 42
Console.WriteLine(true);              // Output: True

// Printing multiple values
Console.WriteLine("Age: " + 30);      // Output: Age: 30
Console.WriteLine("Name: John, Age: 30"); // Output: Name: John, Age: 30
```

### String Interpolation (Inserting Variables into Text)

**What it does**: Easily mix variables with text

```javascript
// JavaScript - use backticks ` and ${variable}
const name = "John";
const age = 30;

console.log(`My name is ${name}`);                    // Output: My name is John
console.log(`${name} is ${age} years old`);          // Output: John is 30 years old
console.log(`Next year I'll be ${age + 1}`);         // Output: Next year I'll be 31

// Old way (still works but harder to read)
console.log("My name is " + name);                    // Output: My name is John
```

```typescript
// TypeScript - same as JavaScript
const name: string = "John";
const age: number = 30;

console.log(`My name is ${name}`);                    // Output: My name is John
console.log(`${name} is ${age} years old`);          // Output: John is 30 years old
```

```csharp
// C# - use $ before the string and {variable}
var name = "John";
var age = 30;

Console.WriteLine($"My name is {name}");              // Output: My name is John
Console.WriteLine($"{name} is {age} years old");      // Output: John is 30 years old
Console.WriteLine($"Next year I'll be {age + 1}");    // Output: Next year I'll be 31

// Old way (still works)
Console.WriteLine("My name is " + name);              // Output: My name is John
```

### Formatting Numbers

**What it does**: Display numbers with specific formats (like currency, decimals, etc.)

```javascript
// JavaScript
const price = 123.456;
console.log(price.toFixed(2));                        // Output: 123.46 (2 decimal places)
console.log(`$${price.toFixed(2)}`);                  // Output: $123.46
```

```typescript
// TypeScript - same as JavaScript
const price: number = 123.456;
console.log(price.toFixed(2));                        // Output: 123.46
console.log(`$${price.toFixed(2)}`);                  // Output: $123.46
```

```csharp
// C#
var price = 123.456;
Console.WriteLine($"{price:F2}");                     // Output: 123.46 (2 decimal places)
Console.WriteLine($"${price:F2}");                    // Output: $123.46
Console.WriteLine($"{price:C}");                      // Output: $123.46 (currency format)
```

---

## Arrays & Collections

**What it does**: Arrays store multiple values in a single variable (like a list of items)

### Creating Arrays

```javascript
// JavaScript
const numbers = [1, 2, 3, 4, 5];                     // Array of numbers
const names = ["John", "Jane", "Bob"];               // Array of strings
const mixed = [1, "two", true];                      // Can mix types

console.log(numbers);                                 // Output: [1, 2, 3, 4, 5]
console.log(numbers[0]);                              // Output: 1 (first item)
console.log(numbers[2]);                              // Output: 3 (third item)
console.log(numbers.length);                          // Output: 5 (how many items)
```

```typescript
// TypeScript - specify the type of array
const numbers: number[] = [1, 2, 3, 4, 5];           // Only numbers
const names: string[] = ["John", "Jane", "Bob"];      // Only strings

console.log(numbers[0]);                              // Output: 1
console.log(numbers.length);                          // Output: 5
```

```csharp
// C#
var numbers = new int[] {1, 2, 3, 4, 5};             // Array of integers
var names = new string[] {"John", "Jane", "Bob"};     // Array of strings

// Shorter syntax
int[] numbers2 = {1, 2, 3, 4, 5};

Console.WriteLine(numbers[0]);                        // Output: 1
Console.WriteLine(numbers.Length);                    // Output: 5 (capital L!)
```

### Adding and Removing Items

```javascript
// JavaScript
const fruits = ["apple", "banana"];

// Add items
fruits.push("orange");                                // Add to end
console.log(fruits);                                  // Output: ["apple", "banana", "orange"]

fruits.unshift("mango");                              // Add to beginning
console.log(fruits);                                  // Output: ["mango", "apple", "banana", "orange"]

// Remove items
const lastFruit = fruits.pop();                       // Remove from end
console.log(lastFruit);                               // Output: "orange"
console.log(fruits);                                  // Output: ["mango", "apple", "banana"]

const firstFruit = fruits.shift();                    // Remove from beginning
console.log(firstFruit);                              // Output: "mango"
console.log(fruits);                                  // Output: ["apple", "banana"]
```

```typescript
// TypeScript - same as JavaScript
const fruits: string[] = ["apple", "banana"];

fruits.push("orange");                                // Add to end
console.log(fruits);                                  // Output: ["apple", "banana", "orange"]

const lastFruit = fruits.pop();                       // Remove from end
console.log(lastFruit);                               // Output: "orange"
```

```csharp
// C# - use List<T> for dynamic arrays (can grow/shrink)
var fruits = new List<string> {"apple", "banana"};

// Add items
fruits.Add("orange");                                 // Add to end
Console.WriteLine(string.Join(", ", fruits));         // Output: apple, banana, orange

fruits.Insert(0, "mango");                            // Add to beginning (at index 0)
Console.WriteLine(string.Join(", ", fruits));         // Output: mango, apple, banana, orange

// Remove items
fruits.RemoveAt(fruits.Count - 1);                    // Remove from end
Console.WriteLine(string.Join(", ", fruits));         // Output: mango, apple, banana

fruits.RemoveAt(0);                                   // Remove from beginning
Console.WriteLine(string.Join(", ", fruits));         // Output: apple, banana

// Remove by value
fruits.Remove("banana");                              // Removes "banana"
Console.WriteLine(string.Join(", ", fruits));         // Output: apple
```

### Finding Items in Arrays

```javascript
// JavaScript
const numbers = [10, 20, 30, 40, 50];

// Check if item exists
console.log(numbers.includes(30));                    // Output: true
console.log(numbers.includes(100));                   // Output: false

// Find the position (index) of an item
console.log(numbers.indexOf(30));                     // Output: 2 (third position, starts at 0)
console.log(numbers.indexOf(100));                    // Output: -1 (not found)

// Find first item matching a condition
const found = numbers.find(num => num > 25);
console.log(found);                                   // Output: 30 (first number > 25)

const foundIndex = numbers.findIndex(num => num > 25);
console.log(foundIndex);                              // Output: 2 (position of 30)
```

```typescript
// TypeScript - same as JavaScript
const numbers: number[] = [10, 20, 30, 40, 50];

console.log(numbers.includes(30));                    // Output: true
console.log(numbers.indexOf(30));                     // Output: 2

const found: number | undefined = numbers.find(num => num > 25);
console.log(found);                                   // Output: 30
```

```csharp
// C#
var numbers = new List<int> {10, 20, 30, 40, 50};

// Check if item exists
Console.WriteLine(numbers.Contains(30));              // Output: True
Console.WriteLine(numbers.Contains(100));             // Output: False

// Find the position (index) of an item
Console.WriteLine(numbers.IndexOf(30));               // Output: 2
Console.WriteLine(numbers.IndexOf(100));              // Output: -1 (not found)

// Find first item matching a condition
var found = numbers.Find(num => num > 25);
Console.WriteLine(found);                             // Output: 30

var foundIndex = numbers.FindIndex(num => num > 25);
Console.WriteLine(foundIndex);                        // Output: 2
```

---

## Loops & Iteration

**What it does**: Loops repeat code multiple times (like doing the same thing for each item in a list)

### For Loop (Count from X to Y)

```javascript
// JavaScript
// Loop from 0 to 4 (5 times)
for (let i = 0; i < 5; i++) {
  console.log(i);
}
// Output:
// 0
// 1
// 2
// 3
// 4

// Loop from 1 to 10
for (let i = 1; i <= 10; i++) {
  console.log(i);
}
// Output: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10
```

```typescript
// TypeScript - same as JavaScript
for (let i: number = 0; i < 5; i++) {
  console.log(i);
}
// Output: 0, 1, 2, 3, 4
```

```csharp
// C#
// Loop from 0 to 4 (5 times)
for (int i = 0; i < 5; i++) {
    Console.WriteLine(i);
}
// Output:
// 0
// 1
// 2
// 3
// 4
```

### For Each (Loop Through Array)

**What it does**: Go through each item in an array one by one

```javascript
// JavaScript
const fruits = ["apple", "banana", "orange"];

// Method 1: for...of (recommended)
for (const fruit of fruits) {
  console.log(fruit);
}
// Output:
// apple
// banana
// orange

// Method 2: forEach
fruits.forEach(fruit => {
  console.log(fruit);
});
// Output: (same as above)

// With index (position)
fruits.forEach((fruit, index) => {
  console.log(`${index}: ${fruit}`);
});
// Output:
// 0: apple
// 1: banana
// 2: orange
```

```typescript
// TypeScript - same as JavaScript
const fruits: string[] = ["apple", "banana", "orange"];

for (const fruit of fruits) {
  console.log(fruit);
}
// Output: apple, banana, orange

fruits.forEach((fruit: string, index: number) => {
  console.log(`${index}: ${fruit}`);
});
// Output: 0: apple, 1: banana, 2: orange
```

```csharp
// C#
var fruits = new List<string> {"apple", "banana", "orange"};

// Method 1: foreach (recommended)
foreach (var fruit in fruits) {
    Console.WriteLine(fruit);
}
// Output:
// apple
// banana
// orange

// Method 2: ForEach with lambda
fruits.ForEach(fruit => Console.WriteLine(fruit));
// Output: (same as above)

// With index
for (int i = 0; i < fruits.Count; i++) {
    Console.WriteLine($"{i}: {fruits[i]}");
}
// Output:
// 0: apple
// 1: banana
// 2: orange
```

### While Loop (Repeat While Condition is True)

```javascript
// JavaScript
let count = 0;
while (count < 3) {
  console.log(`Count is ${count}`);
  count++;
}
// Output:
// Count is 0
// Count is 1
// Count is 2

// Do-while (runs at least once)
let x = 0;
do {
  console.log(`X is ${x}`);
  x++;
} while (x < 3);
// Output: (same as above)
```

```typescript
// TypeScript - same as JavaScript
let count: number = 0;
while (count < 3) {
  console.log(`Count is ${count}`);
  count++;
}
// Output: Count is 0, Count is 1, Count is 2
```

```csharp
// C#
int count = 0;
while (count < 3) {
    Console.WriteLine($"Count is {count}");
    count++;
}
// Output:
// Count is 0
// Count is 1
// Count is 2

// Do-while
int x = 0;
do {
    Console.WriteLine($"X is {x}");
    x++;
} while (x < 3);
```

### Break and Continue

**What it does**: `break` exits the loop, `continue` skips to next iteration

```javascript
// JavaScript
// Break - stop the loop
for (let i = 0; i < 10; i++) {
  if (i === 5) break;                                 // Stop when i is 5
  console.log(i);
}
// Output: 0, 1, 2, 3, 4 (stops at 5)

// Continue - skip current iteration
for (let i = 0; i < 5; i++) {
  if (i === 2) continue;                              // Skip when i is 2
  console.log(i);
}
// Output: 0, 1, 3, 4 (skips 2)
```

```typescript
// TypeScript - same as JavaScript
for (let i = 0; i < 10; i++) {
  if (i === 5) break;
  console.log(i);
}
// Output: 0, 1, 2, 3, 4
```

```csharp
// C# - same keywords
for (int i = 0; i < 10; i++) {
    if (i == 5) break;
    Console.WriteLine(i);
}
// Output: 0, 1, 2, 3, 4

for (int i = 0; i < 5; i++) {
    if (i == 2) continue;
    Console.WriteLine(i);
}
// Output: 0, 1, 3, 4
```

---

## Functions & Methods

**What it does**: Functions are reusable blocks of code that do a specific task

### Basic Functions

```javascript
// JavaScript
// Function that adds two numbers
function add(a, b) {
  return a + b;
}

console.log(add(5, 3));                               // Output: 8
console.log(add(10, 20));                             // Output: 30

// Function that doesn't return anything
function greet(name) {
  console.log(`Hello, ${name}!`);
}

greet("John");                                        // Output: Hello, John!

// Arrow function (shorter way)
const multiply = (a, b) => a * b;
console.log(multiply(4, 5));                          // Output: 20
```

```typescript
// TypeScript - specify parameter types and return type
function add(a: number, b: number): number {
  return a + b;
}

console.log(add(5, 3));                               // Output: 8

// Function with no return value (void)
function greet(name: string): void {
  console.log(`Hello, ${name}!`);
}

greet("John");                                        // Output: Hello, John!

// Arrow function
const multiply = (a: number, b: number): number => a * b;
console.log(multiply(4, 5));                          // Output: 20
```

```csharp
// C# - specify parameter types and return type
public int Add(int a, int b) {
    return a + b;
}

Console.WriteLine(Add(5, 3));                         // Output: 8

// Method with no return value (void)
public void Greet(string name) {
    Console.WriteLine($"Hello, {name}!");
}

Greet("John");                                        // Output: Hello, John!

// Expression-bodied method (shorter)
public int Multiply(int a, int b) => a * b;
Console.WriteLine(Multiply(4, 5));                    // Output: 20

// Lambda (like arrow function)
Func<int, int, int> multiply = (a, b) => a * b;
Console.WriteLine(multiply(4, 5));                    // Output: 20
```

### Functions with Optional Parameters

**What it does**: Parameters that have default values if not provided

```javascript
// JavaScript
function greet(name = "Guest") {
  return `Hello, ${name}!`;
}

console.log(greet("John"));                           // Output: Hello, John!
console.log(greet());                                 // Output: Hello, Guest!
```

```typescript
// TypeScript
function greet(name: string = "Guest"): string {
  return `Hello, ${name}!`;
}

console.log(greet("John"));                           // Output: Hello, John!
console.log(greet());                                 // Output: Hello, Guest!

// Using ? for optional (no default value)
function greet2(name?: string): string {
  return `Hello, ${name ?? "Guest"}!`;
}
```

```csharp
// C#
public string Greet(string name = "Guest") {
    return $"Hello, {name}!";
}

Console.WriteLine(Greet("John"));                     // Output: Hello, John!
Console.WriteLine(Greet());                           // Output: Hello, Guest!
```

### Functions with Multiple Parameters (Variable Length)

**What it does**: Accept any number of parameters

```javascript
// JavaScript - use ...rest
function sum(...numbers) {
  let total = 0;
  for (const num of numbers) {
    total += num;
  }
  return total;
}

console.log(sum(1, 2, 3));                            // Output: 6
console.log(sum(1, 2, 3, 4, 5));                      // Output: 15
console.log(sum(10, 20));                             // Output: 30
```

```typescript
// TypeScript
function sum(...numbers: number[]): number {
  let total = 0;
  for (const num of numbers) {
    total += num;
  }
  return total;
}

console.log(sum(1, 2, 3));                            // Output: 6
console.log(sum(1, 2, 3, 4, 5));                      // Output: 15
```

```csharp
// C# - use params
public int Sum(params int[] numbers) {
    int total = 0;
    foreach (var num in numbers) {
        total += num;
    }
    return total;
    // Or simply: return numbers.Sum();
}

Console.WriteLine(Sum(1, 2, 3));                      // Output: 6
Console.WriteLine(Sum(1, 2, 3, 4, 5));                // Output: 15
Console.WriteLine(Sum(10, 20));                       // Output: 30
```

---

## Classes & Objects

**What it does**: Classes are blueprints for creating objects. Objects store related data and functions together.

### Creating a Basic Class

```javascript
// JavaScript
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  greet() {
    return `Hi, I'm ${this.name} and I'm ${this.age} years old`;
  }
}

// Create an object from the class
const person1 = new Person("John", 30);
console.log(person1.name);                            // Output: John
console.log(person1.age);                             // Output: 30
console.log(person1.greet());                         // Output: Hi, I'm John and I'm 30 years old

const person2 = new Person("Jane", 25);
console.log(person2.greet());                         // Output: Hi, I'm Jane and I'm 25 years old
```

```typescript
// TypeScript - define property types
class Person {
  name: string;
  age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }

  greet(): string {
    return `Hi, I'm ${this.name} and I'm ${this.age} years old`;
  }
}

const person1 = new Person("John", 30);
console.log(person1.greet());                         // Output: Hi, I'm John and I'm 30 years old
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
        return $"Hi, I'm {Name} and I'm {Age} years old";
    }
}

// Create an object
var person1 = new Person("John", 30);
Console.WriteLine(person1.Name);                      // Output: John
Console.WriteLine(person1.Age);                       // Output: 30
Console.WriteLine(person1.Greet());                   // Output: Hi, I'm John and I'm 30 years old
```

### Inheritance (Creating Child Classes)

**What it does**: A child class inherits properties and methods from a parent class

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
    super(name);                                      // Call parent constructor
    this.breed = breed;
  }

  speak() {
    return `${this.name} barks!`;
  }
}

const animal = new Animal("Generic Animal");
console.log(animal.speak());                          // Output: Generic Animal makes a sound

const dog = new Dog("Buddy", "Golden Retriever");
console.log(dog.speak());                             // Output: Buddy barks!
console.log(dog.breed);                               // Output: Golden Retriever
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
    return `${this.name} barks!`;
  }
}

const dog = new Dog("Buddy", "Golden Retriever");
console.log(dog.speak());                             // Output: Buddy barks!
```

```csharp
// C#
public class Animal {
    public string Name { get; set; }

    public Animal(string name) {
        Name = name;
    }

    public virtual string Speak() {                   // 'virtual' allows overriding
        return $"{Name} makes a sound";
    }
}

public class Dog : Animal {
    public string Breed { get; set; }

    public Dog(string name, string breed) : base(name) {
        Breed = breed;
    }

    public override string Speak() {                  // 'override' replaces parent method
        return $"{Name} barks!";
    }
}

var animal = new Animal("Generic Animal");
Console.WriteLine(animal.Speak());                    // Output: Generic Animal makes a sound

var dog = new Dog("Buddy", "Golden Retriever");
Console.WriteLine(dog.Speak());                       // Output: Buddy barks!
Console.WriteLine(dog.Breed);                         // Output: Golden Retriever
```

---

## Conditionals

**What it does**: Make decisions in your code (if this is true, do that)

### If-Else Statements

```javascript
// JavaScript
const age = 18;

if (age >= 18) {
  console.log("You are an adult");                    // Output: You are an adult
} else {
  console.log("You are a minor");
}

// Multiple conditions
const score = 85;

if (score >= 90) {
  console.log("Grade: A");
} else if (score >= 80) {
  console.log("Grade: B");                            // Output: Grade: B
} else if (score >= 70) {
  console.log("Grade: C");
} else {
  console.log("Grade: F");
}

// Ternary operator (shorthand)
const status = age >= 18 ? "adult" : "minor";
console.log(status);                                  // Output: adult
```

```typescript
// TypeScript - same as JavaScript
const age: number = 18;

if (age >= 18) {
  console.log("You are an adult");                    // Output: You are an adult
} else {
  console.log("You are a minor");
}

const status: string = age >= 18 ? "adult" : "minor";
console.log(status);                                  // Output: adult
```

```csharp
// C#
var age = 18;

if (age >= 18) {
    Console.WriteLine("You are an adult");            // Output: You are an adult
} else {
    Console.WriteLine("You are a minor");
}

// Multiple conditions
var score = 85;

if (score >= 90) {
    Console.WriteLine("Grade: A");
} else if (score >= 80) {
    Console.WriteLine("Grade: B");                    // Output: Grade: B
} else if (score >= 70) {
    Console.WriteLine("Grade: C");
} else {
    Console.WriteLine("Grade: F");
}

// Ternary operator
var status = age >= 18 ? "adult" : "minor";
Console.WriteLine(status);                            // Output: adult
```

### Switch Statement (Multiple Options)

**What it does**: Check a value against multiple possible cases

```javascript
// JavaScript
const day = "Monday";

switch (day) {
  case "Monday":
    console.log("Start of the work week");            // Output: Start of the work week
    break;
  case "Friday":
    console.log("Almost weekend!");
    break;
  case "Saturday":
  case "Sunday":
    console.log("It's the weekend!");
    break;
  default:
    console.log("It's a regular day");
}

// With numbers
const number = 2;

switch (number) {
  case 1:
    console.log("One");
    break;
  case 2:
    console.log("Two");                               // Output: Two
    break;
  default:
    console.log("Other number");
}
```

```typescript
// TypeScript - same as JavaScript
const day: string = "Monday";

switch (day) {
  case "Monday":
    console.log("Start of the work week");            // Output: Start of the work week
    break;
  case "Friday":
    console.log("Almost weekend!");
    break;
  default:
    console.log("It's a regular day");
}
```

```csharp
// C#
var day = "Monday";

switch (day) {
    case "Monday":
        Console.WriteLine("Start of the work week");  // Output: Start of the work week
        break;
    case "Friday":
        Console.WriteLine("Almost weekend!");
        break;
    case "Saturday":
    case "Sunday":
        Console.WriteLine("It's the weekend!");
        break;
    default:
        Console.WriteLine("It's a regular day");
        break;
}

// Modern C# switch expression (shorter)
var message = day switch {
    "Monday" => "Start of the work week",
    "Friday" => "Almost weekend!",
    "Saturday" or "Sunday" => "It's the weekend!",
    _ => "It's a regular day"
};
Console.WriteLine(message);                           // Output: Start of the work week
```

---

## String Operations

**What it does**: Common operations you can do with text (strings)

### Basic String Methods

```javascript
// JavaScript
const text = "Hello World";

console.log(text.length);                             // Output: 11 (number of characters)
console.log(text.toUpperCase());                      // Output: HELLO WORLD
console.log(text.toLowerCase());                      // Output: hello world

// Check if string contains something
console.log(text.includes("World"));                  // Output: true
console.log(text.includes("xyz"));                    // Output: false

// Check start and end
console.log(text.startsWith("Hello"));                // Output: true
console.log(text.endsWith("World"));                  // Output: true

// Find position
console.log(text.indexOf("World"));                   // Output: 6 (position of "World")
console.log(text.indexOf("xyz"));                     // Output: -1 (not found)

// Get part of string
console.log(text.substring(0, 5));                    // Output: Hello (from 0 to 5)
console.log(text.slice(6));                           // Output: World (from 6 to end)

// Split into array
const words = text.split(" ");
console.log(words);                                   // Output: ["Hello", "World"]

// Replace text
console.log(text.replace("World", "JavaScript"));     // Output: Hello JavaScript

// Remove whitespace
const messy = "  hello  ";
console.log(messy.trim());                            // Output: "hello"
```

```typescript
// TypeScript - same as JavaScript
const text: string = "Hello World";

console.log(text.length);                             // Output: 11
console.log(text.toUpperCase());                      // Output: HELLO WORLD
console.log(text.includes("World"));                  // Output: true
console.log(text.substring(0, 5));                    // Output: Hello
```

```csharp
// C#
var text = "Hello World";

Console.WriteLine(text.Length);                       // Output: 11 (capital L!)
Console.WriteLine(text.ToUpper());                    // Output: HELLO WORLD
Console.WriteLine(text.ToLower());                    // Output: hello world

// Check if string contains something
Console.WriteLine(text.Contains("World"));            // Output: True
Console.WriteLine(text.Contains("xyz"));              // Output: False

// Check start and end
Console.WriteLine(text.StartsWith("Hello"));          // Output: True
Console.WriteLine(text.EndsWith("World"));            // Output: True

// Find position
Console.WriteLine(text.IndexOf("World"));             // Output: 6
Console.WriteLine(text.IndexOf("xyz"));               // Output: -1

// Get part of string
Console.WriteLine(text.Substring(0, 5));              // Output: Hello
Console.WriteLine(text.Substring(6));                 // Output: World

// Split into array
var words = text.Split(" ");
Console.WriteLine(string.Join(", ", words));          // Output: Hello, World

// Replace text
Console.WriteLine(text.Replace("World", "C#"));       // Output: Hello C#

// Remove whitespace
var messy = "  hello  ";
Console.WriteLine(messy.Trim());                      // Output: "hello"
```

### Combining Strings

```javascript
// JavaScript
const first = "John";
const last = "Doe";

// Method 1: Concatenation with +
console.log(first + " " + last);                      // Output: John Doe

// Method 2: Template literals (better)
console.log(`${first} ${last}`);                      // Output: John Doe
console.log(`Name: ${first} ${last}`);                // Output: Name: John Doe
```

```typescript
// TypeScript - same as JavaScript
const first: string = "John";
const last: string = "Doe";

console.log(`${first} ${last}`);                      // Output: John Doe
```

```csharp
// C#
var first = "John";
var last = "Doe";

// Method 1: Concatenation with +
Console.WriteLine(first + " " + last);                // Output: John Doe

// Method 2: String interpolation (better)
Console.WriteLine($"{first} {last}");                 // Output: John Doe
Console.WriteLine($"Name: {first} {last}");           // Output: Name: John Doe
```

---

## Null Handling

**What it does**: Handle missing or empty values safely (avoid crashes)

### Checking for Null/Undefined

```javascript
// JavaScript
let name = null;
let age = undefined;
let email = "test@test.com";

// Check for null or undefined
if (name === null) {
  console.log("Name is null");                        // Output: Name is null
}

if (age === undefined) {
  console.log("Age is undefined");                    // Output: Age is undefined
}

// Check for both null and undefined
if (name == null) {
  console.log("Name is null or undefined");           // Output: Name is null or undefined
}

// Provide default value if null/undefined
const displayName = name ?? "Guest";
console.log(displayName);                             // Output: Guest

const displayAge = age ?? 0;
console.log(displayAge);                              // Output: 0

// Safe navigation (optional chaining)
const user = { name: "John", address: { city: "NYC" } };
console.log(user?.address?.city);                     // Output: NYC

const missingUser = null;
console.log(missingUser?.address?.city);              // Output: undefined (no error!)
```

```typescript
// TypeScript
let name: string | null = null;
let age: number | undefined = undefined;

// Nullish coalescing
const displayName: string = name ?? "Guest";
console.log(displayName);                             // Output: Guest

// Optional chaining
interface User {
  name: string;
  address?: {
    city: string;
  };
}

const user: User = { name: "John", address: { city: "NYC" } };
console.log(user?.address?.city);                     // Output: NYC
```

```csharp
// C#
string? name = null;                                  // ? means nullable
int? age = null;

// Check for null
if (name == null) {
    Console.WriteLine("Name is null");                // Output: Name is null
}

// Provide default value if null
var displayName = name ?? "Guest";
Console.WriteLine(displayName);                       // Output: Guest

var displayAge = age ?? 0;
Console.WriteLine(displayAge);                        // Output: 0

// Safe navigation (null conditional operator)
class Address {
    public string? City { get; set; }
}

class User {
    public string? Name { get; set; }
    public Address? Address { get; set; }
}

var user = new User { Name = "John", Address = new Address { City = "NYC" } };
Console.WriteLine(user?.Address?.City);               // Output: NYC

User? missingUser = null;
Console.WriteLine(missingUser?.Address?.City);        // Output: (nothing - no error!)
```

---

## Async/Await

**What it does**: Handle operations that take time (like fetching data from internet) without freezing your program

### Basic Async/Await

```javascript
// JavaScript
// Function that takes time (simulated with setTimeout)
function fetchData() {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve("Data loaded!");
    }, 2000);                                         // Wait 2 seconds
  });
}

// Using async/await (cleaner way)
async function getData() {
  console.log("Fetching data...");
  const data = await fetchData();                     // Wait for data
  console.log(data);                                  // Output: Data loaded!
  console.log("Done!");
}

getData();
// Output:
// Fetching data...
// (waits 2 seconds)
// Data loaded!
// Done!

// With error handling
async function getDataSafe() {
  try {
    const data = await fetchData();
    console.log(data);
  } catch (error) {
    console.log("Error:", error);
  }
}
```

```typescript
// TypeScript
function fetchData(): Promise<string> {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve("Data loaded!");
    }, 2000);
  });
}

async function getData(): Promise<void> {
  console.log("Fetching data...");
  const data: string = await fetchData();
  console.log(data);                                  // Output: Data loaded!
}

getData();
```

```csharp
// C#
public async Task<string> FetchDataAsync() {
    await Task.Delay(2000);                           // Wait 2 seconds
    return "Data loaded!";
}

public async Task GetDataAsync() {
    Console.WriteLine("Fetching data...");
    var data = await FetchDataAsync();                // Wait for data
    Console.WriteLine(data);                          // Output: Data loaded!
    Console.WriteLine("Done!");
}

// Call it
GetDataAsync().Wait();
// Output:
// Fetching data...
// (waits 2 seconds)
// Data loaded!
// Done!

// With error handling
public async Task GetDataSafeAsync() {
    try {
        var data = await FetchDataAsync();
        Console.WriteLine(data);
    } catch (Exception error) {
        Console.WriteLine($"Error: {error.Message}");
    }
}
```

---

## Error Handling

**What it does**: Catch and handle errors gracefully (so your program doesn't crash)

### Try-Catch

```javascript
// JavaScript
try {
  // Code that might cause an error
  const result = 10 / 0;
  console.log(result);                                // Output: Infinity

  // This will cause an error
  const data = JSON.parse("invalid json");            // Error!
} catch (error) {
  console.log("An error occurred:", error.message);   // Output: An error occurred: ...
} finally {
  console.log("This always runs");                    // Output: This always runs
}

// Throwing your own errors
function divide(a, b) {
  if (b === 0) {
    throw new Error("Cannot divide by zero!");
  }
  return a / b;
}

try {
  console.log(divide(10, 2));                         // Output: 5
  console.log(divide(10, 0));                         // Throws error
} catch (error) {
  console.log("Error:", error.message);               // Output: Error: Cannot divide by zero!
}
```

```typescript
// TypeScript
try {
  const data = JSON.parse("invalid json");
} catch (error) {
  if (error instanceof Error) {
    console.log("Error:", error.message);
  }
} finally {
  console.log("This always runs");
}

function divide(a: number, b: number): number {
  if (b === 0) {
    throw new Error("Cannot divide by zero!");
  }
  return a / b;
}
```

```csharp
// C#
try {
    // Code that might cause an error
    var data = int.Parse("not a number");             // Error!
} catch (Exception ex) {
    Console.WriteLine($"An error occurred: {ex.Message}");
} finally {
    Console.WriteLine("This always runs");            // Output: This always runs
}

// Specific error types
try {
    var result = 10 / 0;
} catch (DivideByZeroException ex) {
    Console.WriteLine("Cannot divide by zero!");
} catch (Exception ex) {
    Console.WriteLine($"Other error: {ex.Message}");
}

// Throwing your own errors
public int Divide(int a, int b) {
    if (b == 0) {
        throw new ArgumentException("Cannot divide by zero!");
    }
    return a / b;
}

try {
    Console.WriteLine(Divide(10, 2));                 // Output: 5
    Console.WriteLine(Divide(10, 0));                 // Throws error
} catch (Exception ex) {
    Console.WriteLine($"Error: {ex.Message}");        // Output: Error: Cannot divide by zero!
}
```

---

## Array Methods & LINQ

**What it does**: Powerful ways to transform, filter, and work with arrays/lists

### Map / Select (Transform Each Item)

**What it does**: Create a new array by transforming each item

```javascript
// JavaScript
const numbers = [1, 2, 3, 4, 5];

// Double each number
const doubled = numbers.map(num => num * 2);
console.log(doubled);                                 // Output: [2, 4, 6, 8, 10]

// Convert to strings
const strings = numbers.map(num => `Number ${num}`);
console.log(strings);                                 // Output: ["Number 1", "Number 2", ...]
```

```typescript
// TypeScript
const numbers: number[] = [1, 2, 3, 4, 5];
const doubled: number[] = numbers.map(num => num * 2);
console.log(doubled);                                 // Output: [2, 4, 6, 8, 10]
```

```csharp
// C# - use Select()
var numbers = new List<int> {1, 2, 3, 4, 5};

// Double each number
var doubled = numbers.Select(num => num * 2).ToList();
Console.WriteLine(string.Join(", ", doubled));        // Output: 2, 4, 6, 8, 10

// Convert to strings
var strings = numbers.Select(num => $"Number {num}").ToList();
Console.WriteLine(string.Join(", ", strings));        // Output: Number 1, Number 2, ...
```

### Filter / Where (Keep Only Some Items)

**What it does**: Create a new array with only items that match a condition

```javascript
// JavaScript
const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

// Keep only even numbers
const evens = numbers.filter(num => num % 2 === 0);
console.log(evens);                                   // Output: [2, 4, 6, 8, 10]

// Keep only numbers greater than 5
const bigNumbers = numbers.filter(num => num > 5);
console.log(bigNumbers);                              // Output: [6, 7, 8, 9, 10]

const people = [
  { name: "John", age: 30 },
  { name: "Jane", age: 25 },
  { name: "Bob", age: 35 }
];

// Keep only people over 30
const adults = people.filter(person => person.age >= 30);
console.log(adults);                                  // Output: [{name: "John", age: 30}, {name: "Bob", age: 35}]
```

```typescript
// TypeScript
const numbers: number[] = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const evens: number[] = numbers.filter(num => num % 2 === 0);
console.log(evens);                                   // Output: [2, 4, 6, 8, 10]
```

```csharp
// C# - use Where()
var numbers = new List<int> {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};

// Keep only even numbers
var evens = numbers.Where(num => num % 2 == 0).ToList();
Console.WriteLine(string.Join(", ", evens));          // Output: 2, 4, 6, 8, 10

// Keep only numbers greater than 5
var bigNumbers = numbers.Where(num => num > 5).ToList();
Console.WriteLine(string.Join(", ", bigNumbers));     // Output: 6, 7, 8, 9, 10
```

### Reduce / Aggregate (Combine All Items into One Value)

**What it does**: Combine all array items into a single value

```javascript
// JavaScript
const numbers = [1, 2, 3, 4, 5];

// Sum all numbers
const sum = numbers.reduce((total, num) => total + num, 0);
console.log(sum);                                     // Output: 15 (1+2+3+4+5)

// Find maximum
const max = numbers.reduce((highest, num) => num > highest ? num : highest);
console.log(max);                                     // Output: 5

// Combine strings
const words = ["Hello", "World", "!"];
const sentence = words.reduce((result, word) => result + " " + word);
console.log(sentence);                                // Output: " Hello World !"
```

```typescript
// TypeScript
const numbers: number[] = [1, 2, 3, 4, 5];
const sum: number = numbers.reduce((total, num) => total + num, 0);
console.log(sum);                                     // Output: 15
```

```csharp
// C# - use Aggregate() or built-in methods
var numbers = new List<int> {1, 2, 3, 4, 5};

// Sum all numbers (easy way)
var sum = numbers.Sum();
Console.WriteLine(sum);                               // Output: 15

// Sum with Aggregate
var sum2 = numbers.Aggregate(0, (total, num) => total + num);
Console.WriteLine(sum2);                              // Output: 15

// Find maximum (easy way)
var max = numbers.Max();
Console.WriteLine(max);                               // Output: 5
```

### Sort / OrderBy

**What it does**: Sort array items in order

```javascript
// JavaScript
const numbers = [5, 2, 8, 1, 9];

// Sort ascending (⚠️ modifies original array!)
numbers.sort((a, b) => a - b);
console.log(numbers);                                 // Output: [1, 2, 5, 8, 9]

// Sort descending
numbers.sort((a, b) => b - a);
console.log(numbers);                                 // Output: [9, 8, 5, 2, 1]

// Sort without modifying original
const original = [5, 2, 8, 1, 9];
const sorted = [...original].sort((a, b) => a - b);
console.log(original);                                // Output: [5, 2, 8, 1, 9] (unchanged)
console.log(sorted);                                  // Output: [1, 2, 5, 8, 9]

// Sort objects
const people = [
  { name: "John", age: 30 },
  { name: "Jane", age: 25 },
  { name: "Bob", age: 35 }
];

people.sort((a, b) => a.age - b.age);
console.log(people);                                  // Output: Jane(25), John(30), Bob(35)
```

```typescript
// TypeScript
const numbers: number[] = [5, 2, 8, 1, 9];
const sorted: number[] = [...numbers].sort((a, b) => a - b);
console.log(sorted);                                  // Output: [1, 2, 5, 8, 9]
```

```csharp
// C# - use OrderBy() (doesn't modify original)
var numbers = new List<int> {5, 2, 8, 1, 9};

// Sort ascending
var sorted = numbers.OrderBy(x => x).ToList();
Console.WriteLine(string.Join(", ", sorted));         // Output: 1, 2, 5, 8, 9

// Sort descending
var sortedDesc = numbers.OrderByDescending(x => x).ToList();
Console.WriteLine(string.Join(", ", sortedDesc));     // Output: 9, 8, 5, 2, 1

// Original list unchanged
Console.WriteLine(string.Join(", ", numbers));        // Output: 5, 2, 8, 1, 9

// Sort objects
var people = new List<Person> {
    new Person { Name = "John", Age = 30 },
    new Person { Name = "Jane", Age = 25 },
    new Person { Name = "Bob", Age = 35 }
};

var sortedPeople = people.OrderBy(p => p.Age).ToList();
// Output: Jane(25), John(30), Bob(35)
```

### Chaining Multiple Operations

**What it does**: Combine multiple array operations together

```javascript
// JavaScript
const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

// Filter even numbers, double them, then sum
const result = numbers
  .filter(num => num % 2 === 0)                       // [2, 4, 6, 8, 10]
  .map(num => num * 2)                                // [4, 8, 12, 16, 20]
  .reduce((sum, num) => sum + num, 0);                // 60

console.log(result);                                  // Output: 60
```

```typescript
// TypeScript
const numbers: number[] = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

const result: number = numbers
  .filter(num => num % 2 === 0)
  .map(num => num * 2)
  .reduce((sum, num) => sum + num, 0);

console.log(result);                                  // Output: 60
```

```csharp
// C#
var numbers = new List<int> {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};

// Filter even numbers, double them, then sum
var result = numbers
    .Where(num => num % 2 == 0)                       // [2, 4, 6, 8, 10]
    .Select(num => num * 2)                           // [4, 8, 12, 16, 20]
    .Sum();                                           // 60

Console.WriteLine(result);                            // Output: 60
```

---

## Common Data Structures

### Dictionary / Map / Object (Key-Value Pairs)

**What it does**: Store data as pairs (like a real dictionary: word → definition)

```javascript
// JavaScript
// Using Map
const userAges = new Map();
userAges.set("John", 30);
userAges.set("Jane", 25);
userAges.set("Bob", 35);

console.log(userAges.get("John"));                    // Output: 30
console.log(userAges.has("Jane"));                    // Output: true
console.log(userAges.size);                           // Output: 3

userAges.delete("Bob");
console.log(userAges.size);                           // Output: 2

// Using plain object
const scores = {
  "John": 95,
  "Jane": 87,
  "Bob": 92
};

console.log(scores["John"]);                          // Output: 95
console.log(scores.Jane);                             // Output: 87

// Get all keys and values
console.log(Object.keys(scores));                     // Output: ["John", "Jane", "Bob"]
console.log(Object.values(scores));                   // Output: [95, 87, 92]
```

```typescript
// TypeScript
const userAges: Map<string, number> = new Map();
userAges.set("John", 30);
userAges.set("Jane", 25);

console.log(userAges.get("John"));                    // Output: 30

// Using Record type
const scores: Record<string, number> = {
  "John": 95,
  "Jane": 87,
  "Bob": 92
};

console.log(scores["John"]);                          // Output: 95
```

```csharp
// C# - Dictionary
var userAges = new Dictionary<string, int>();
userAges["John"] = 30;
userAges["Jane"] = 25;
userAges["Bob"] = 35;

// Or initialize directly
var userAges2 = new Dictionary<string, int> {
    {"John", 30},
    {"Jane", 25},
    {"Bob", 35}
};

Console.WriteLine(userAges["John"]);                  // Output: 30
Console.WriteLine(userAges.ContainsKey("Jane"));      // Output: True
Console.WriteLine(userAges.Count);                    // Output: 3

userAges.Remove("Bob");
Console.WriteLine(userAges.Count);                    // Output: 2

// Get all keys and values
Console.WriteLine(string.Join(", ", userAges.Keys));  // Output: John, Jane
Console.WriteLine(string.Join(", ", userAges.Values)); // Output: 30, 25

// Loop through dictionary
foreach (var kvp in userAges) {
    Console.WriteLine($"{kvp.Key}: {kvp.Value}");     // Output: John: 30, Jane: 25
}
```

### Set (Unique Values Only)

**What it does**: Store unique values (automatically removes duplicates)

```javascript
// JavaScript
const numbers = new Set([1, 2, 3, 3, 4, 4, 5]);
console.log(numbers);                                 // Output: Set {1, 2, 3, 4, 5} (duplicates removed!)

numbers.add(6);
numbers.add(3);                                       // Already exists, won't add again
console.log(numbers);                                 // Output: Set {1, 2, 3, 4, 5, 6}

console.log(numbers.has(3));                          // Output: true
console.log(numbers.has(10));                         // Output: false
console.log(numbers.size);                            // Output: 6

numbers.delete(3);
console.log(numbers.has(3));                          // Output: false

// Convert Set to Array
const uniqueArray = [...numbers];
console.log(uniqueArray);                             // Output: [1, 2, 4, 5, 6]

// Remove duplicates from array
const withDuplicates = [1, 2, 2, 3, 3, 3, 4];
const unique = [...new Set(withDuplicates)];
console.log(unique);                                  // Output: [1, 2, 3, 4]
```

```typescript
// TypeScript
const numbers: Set<number> = new Set([1, 2, 3, 3, 4, 4, 5]);
console.log(numbers);                                 // Output: Set {1, 2, 3, 4, 5}

numbers.add(6);
console.log(numbers.has(3));                          // Output: true
console.log(numbers.size);                            // Output: 6
```

```csharp
// C# - HashSet
var numbers = new HashSet<int> {1, 2, 3, 3, 4, 4, 5};
Console.WriteLine(string.Join(", ", numbers));        // Output: 1, 2, 3, 4, 5 (duplicates removed!)

numbers.Add(6);
numbers.Add(3);                                       // Already exists, won't add
Console.WriteLine(string.Join(", ", numbers));        // Output: 1, 2, 3, 4, 5, 6

Console.WriteLine(numbers.Contains(3));               // Output: True
Console.WriteLine(numbers.Contains(10));              // Output: False
Console.WriteLine(numbers.Count);                     // Output: 6

numbers.Remove(3);
Console.WriteLine(numbers.Contains(3));               // Output: False

// Convert to array
var uniqueArray = numbers.ToArray();
Console.WriteLine(string.Join(", ", uniqueArray));    // Output: 1, 2, 4, 5, 6

// Remove duplicates from list
var withDuplicates = new List<int> {1, 2, 2, 3, 3, 3, 4};
var unique = withDuplicates.Distinct().ToList();
Console.WriteLine(string.Join(", ", unique));         // Output: 1, 2, 3, 4
```

---

## Quick Comparison Tables

### How to Print

| Task | JavaScript | TypeScript | C# |
|------|------------|------------|-----|
| Print text | `console.log("Hello")` | `console.log("Hello")` | `Console.WriteLine("Hello")` |
| Print variable | `console.log(name)` | `console.log(name)` | `Console.WriteLine(name)` |
| Print with variable | `` console.log(`Hi ${name}`) `` | `` console.log(`Hi ${name}`) `` | `Console.WriteLine($"Hi {name}")` |

### How to Create Arrays

| Task | JavaScript | TypeScript | C# |
|------|------------|------------|-----|
| Array of numbers | `[1, 2, 3]` | `const arr: number[] = [1, 2, 3]` | `new List<int> {1, 2, 3}` |
| Array of strings | `["a", "b"]` | `const arr: string[] = ["a", "b"]` | `new List<string> {"a", "b"}` |
| Add to array | `arr.push(4)` | `arr.push(4)` | `arr.Add(4)` |
| Remove from array | `arr.pop()` | `arr.pop()` | `arr.RemoveAt(arr.Count - 1)` |
| Array length | `arr.length` | `arr.length` | `arr.Count` |

### How to Loop

| Task | JavaScript | TypeScript | C# |
|------|------------|------------|-----|
| For loop | `for (let i = 0; i < 10; i++)` | `for (let i = 0; i < 10; i++)` | `for (int i = 0; i < 10; i++)` |
| For each | `for (const item of arr)` | `for (const item of arr)` | `foreach (var item in arr)` |
| While loop | `while (condition)` | `while (condition)` | `while (condition)` |

---

## Type Conversion & Parsing

**What it does**: Convert between different data types (string to number, number to string, etc.)

### String to Number

```javascript
// JavaScript
const str = "42";

const num1 = parseInt(str);                           // Output: 42 (integer)
const num2 = parseFloat("3.14");                      // Output: 3.14 (decimal)
const num3 = Number("42");                            // Output: 42
const num4 = +"42";                                   // Output: 42 (shorthand)

console.log(parseInt("42px"));                        // Output: 42 (ignores non-numeric)
console.log(parseInt("abc"));                         // Output: NaN (Not a Number)

// Check if it's a valid number
console.log(isNaN("abc"));                            // Output: true
console.log(isNaN("42"));                             // Output: false
```

```typescript
// TypeScript
const str: string = "42";

const num1: number = parseInt(str);                   // Output: 42
const num2: number = parseFloat("3.14");              // Output: 3.14
const num3: number = Number("42");                    // Output: 42
```

```csharp
// C#
var str = "42";

var num1 = int.Parse(str);                            // Output: 42 (throws error if invalid)
var num2 = double.Parse("3.14");                      // Output: 3.14

// Safe parsing (doesn't throw errors)
if (int.TryParse("42", out int num3)) {
    Console.WriteLine(num3);                          // Output: 42
}

if (int.TryParse("abc", out int num4)) {
    Console.WriteLine(num4);
} else {
    Console.WriteLine("Invalid number");              // Output: Invalid number
}
```

### Number to String

```javascript
// JavaScript
const num = 42;

const str1 = num.toString();                          // Output: "42"
const str2 = String(num);                             // Output: "42"
const str3 = `${num}`;                                // Output: "42" (template literal)
const str4 = num + "";                                // Output: "42" (concatenation)

// With formatting
const price = 99.99;
console.log(price.toFixed(2));                        // Output: "99.99"
console.log(price.toFixed(0));                        // Output: "100"
```

```typescript
// TypeScript
const num: number = 42;

const str1: string = num.toString();                  // Output: "42"
const str2: string = String(num);                     // Output: "42"
```

```csharp
// C#
var num = 42;

var str1 = num.ToString();                            // Output: "42"
var str2 = Convert.ToString(num);                     // Output: "42"

// With formatting
var price = 99.99;
Console.WriteLine(price.ToString("F2"));              // Output: "99.99"
Console.WriteLine(price.ToString("C"));               // Output: "$99.99" (currency)
```

### Boolean Conversions

```javascript
// JavaScript
// Truthy and Falsy values
console.log(Boolean(1));                              // Output: true
console.log(Boolean(0));                              // Output: false
console.log(Boolean(""));                             // Output: false (empty string)
console.log(Boolean("text"));                         // Output: true
console.log(Boolean(null));                           // Output: false
console.log(Boolean(undefined));                      // Output: false

// Common pattern
const value = userInput || "default";                 // Use default if userInput is falsy
```

```typescript
// TypeScript
const isTrue: boolean = Boolean(1);                   // Output: true
const isFalse: boolean = Boolean(0);                  // Output: false
```

```csharp
// C#
var bool1 = Convert.ToBoolean(1);                     // Output: True
var bool2 = Convert.ToBoolean(0);                     // Output: False

// String to boolean
var bool3 = bool.Parse("true");                       // Output: True
var bool4 = bool.Parse("false");                      // Output: False

if (bool.TryParse("true", out bool bool5)) {
    Console.WriteLine(bool5);                         // Output: True
}
```

---

## Operators & Math

**What it does**: Perform calculations and comparisons

### Arithmetic Operators

```javascript
// JavaScript
const a = 10;
const b = 3;

console.log(a + b);                                   // Output: 13 (addition)
console.log(a - b);                                   // Output: 7 (subtraction)
console.log(a * b);                                   // Output: 30 (multiplication)
console.log(a / b);                                   // Output: 3.333... (division)
console.log(a % b);                                   // Output: 1 (remainder/modulo)
console.log(a ** b);                                  // Output: 1000 (power: 10^3)

// Increment/Decrement
let count = 5;
count++;                                              // count = 6
count--;                                              // count = 5
```

```typescript
// TypeScript
const a: number = 10;
const b: number = 3;

console.log(a + b);                                   // Output: 13
console.log(a % b);                                   // Output: 1
console.log(a ** b);                                  // Output: 1000
```

```csharp
// C#
var a = 10;
var b = 3;

Console.WriteLine(a + b);                             // Output: 13
Console.WriteLine(a - b);                             // Output: 7
Console.WriteLine(a * b);                             // Output: 30
Console.WriteLine(a / b);                             // Output: 3 (integer division!)
Console.WriteLine(a / (double)b);                     // Output: 3.333... (convert to double)
Console.WriteLine(a % b);                             // Output: 1
Console.WriteLine(Math.Pow(a, b));                    // Output: 1000

// Increment/Decrement
var count = 5;
count++;                                              // count = 6
count--;                                              // count = 5
```

### Comparison Operators

```javascript
// JavaScript
console.log(5 == "5");                                // Output: true (loose equality)
console.log(5 === "5");                               // Output: false (strict equality)
console.log(5 != "5");                                // Output: false
console.log(5 !== "5");                               // Output: true

console.log(10 > 5);                                  // Output: true
console.log(10 < 5);                                  // Output: false
console.log(10 >= 10);                                // Output: true
console.log(10 <= 5);                                 // Output: false
```

```typescript
// TypeScript
console.log(5 === 5);                                 // Output: true
console.log(5 !== "5");                               // Output: true (type-safe)
```

```csharp
// C#
Console.WriteLine(5 == 5);                            // Output: True
Console.WriteLine(5 != 3);                            // Output: True
Console.WriteLine(10 > 5);                            // Output: True
Console.WriteLine(10 <= 10);                          // Output: True
```

### Logical Operators

```javascript
// JavaScript
console.log(true && false);                           // Output: false (AND)
console.log(true || false);                           // Output: true (OR)
console.log(!true);                                   // Output: false (NOT)

// Combining conditions
const age = 25;
const hasLicense = true;

if (age >= 18 && hasLicense) {
  console.log("Can drive");                           // Output: Can drive
}
```

```typescript
// TypeScript
const isAdult: boolean = age >= 18 && age < 65;
const canVote: boolean = age >= 18 || hasPermission;
const isNotActive: boolean = !isActive;
```

```csharp
// C#
Console.WriteLine(true && false);                     // Output: False
Console.WriteLine(true || false);                     // Output: True
Console.WriteLine(!true);                             // Output: False

var age = 25;
var hasLicense = true;

if (age >= 18 && hasLicense) {
    Console.WriteLine("Can drive");                   // Output: Can drive
}
```

### Math Operations

```javascript
// JavaScript
console.log(Math.abs(-5));                            // Output: 5 (absolute value)
console.log(Math.ceil(4.3));                          // Output: 5 (round up)
console.log(Math.floor(4.7));                         // Output: 4 (round down)
console.log(Math.round(4.5));                         // Output: 5 (round nearest)
console.log(Math.max(1, 5, 3));                       // Output: 5
console.log(Math.min(1, 5, 3));                       // Output: 1
console.log(Math.pow(2, 3));                          // Output: 8 (2^3)
console.log(Math.sqrt(16));                           // Output: 4
console.log(Math.random());                           // Output: 0.xxx (0 to 1)

// Random number between 1 and 10
const random = Math.floor(Math.random() * 10) + 1;
console.log(random);                                  // Output: 1-10
```

```typescript
// TypeScript
const abs: number = Math.abs(-5);                     // Output: 5
const max: number = Math.max(1, 5, 3);                // Output: 5
const random: number = Math.random();                 // Output: 0.xxx
```

```csharp
// C#
Console.WriteLine(Math.Abs(-5));                      // Output: 5
Console.WriteLine(Math.Ceiling(4.3));                 // Output: 5
Console.WriteLine(Math.Floor(4.7));                   // Output: 4
Console.WriteLine(Math.Round(4.5));                   // Output: 4 (banker's rounding)
Console.WriteLine(Math.Max(1, 5));                    // Output: 5
Console.WriteLine(Math.Min(1, 5));                    // Output: 1
Console.WriteLine(Math.Pow(2, 3));                    // Output: 8
Console.WriteLine(Math.Sqrt(16));                     // Output: 4

// Random number
var random = new Random();
Console.WriteLine(random.Next(1, 11));                // Output: 1-10 (11 is exclusive)
Console.WriteLine(random.NextDouble());               // Output: 0.xxx
```

---

## Working with Objects

**What it does**: Create and manipulate objects (key-value pairs)

### Creating Objects

```javascript
// JavaScript
const person = {
  name: "John",
  age: 30,
  city: "NYC"
};

console.log(person.name);                             // Output: John
console.log(person["age"]);                           // Output: 30

// Adding properties
person.email = "john@example.com";
person["phone"] = "555-1234";

console.log(person);                                  // Output: {name: "John", age: 30, city: "NYC", email: "john@example.com", phone: "555-1234"}
```

```typescript
// TypeScript
interface Person {
  name: string;
  age: number;
  city: string;
  email?: string;  // Optional
}

const person: Person = {
  name: "John",
  age: 30,
  city: "NYC"
};

person.email = "john@example.com";
```

```csharp
// C#
// Using anonymous object
var person = new {
    Name = "John",
    Age = 30,
    City = "NYC"
};

Console.WriteLine(person.Name);                       // Output: John

// Using Dictionary for dynamic properties
var personDict = new Dictionary<string, object> {
    {"name", "John"},
    {"age", 30},
    {"city", "NYC"}
};

Console.WriteLine(personDict["name"]);                // Output: John

// Adding properties
personDict["email"] = "john@example.com";
```

### Object Methods

```javascript
// JavaScript
const user = {
  name: "John",
  age: 30,
  city: "NYC"
};

// Get all keys
const keys = Object.keys(user);
console.log(keys);                                    // Output: ["name", "age", "city"]

// Get all values
const values = Object.values(user);
console.log(values);                                  // Output: ["John", 30, "NYC"]

// Get key-value pairs
const entries = Object.entries(user);
console.log(entries);                                 // Output: [["name", "John"], ["age", 30], ["city", "NYC"]]

// Check if property exists
console.log("name" in user);                          // Output: true
console.log(user.hasOwnProperty("name"));             // Output: true

// Copy object (shallow)
const copy = { ...user };
const copy2 = Object.assign({}, user);

// Merge objects
const extra = { email: "john@example.com" };
const merged = { ...user, ...extra };
console.log(merged);                                  // Output: {name: "John", age: 30, city: "NYC", email: "john@example.com"}

// Delete property
delete user.city;
console.log(user);                                    // Output: {name: "John", age: 30}
```

```typescript
// TypeScript
const user: Record<string, any> = {
  name: "John",
  age: 30,
  city: "NYC"
};

const keys: string[] = Object.keys(user);
const values: any[] = Object.values(user);
const entries: [string, any][] = Object.entries(user);
```

```csharp
// C#
var user = new Dictionary<string, object> {
    {"name", "John"},
    {"age", 30},
    {"city", "NYC"}
};

// Get all keys
var keys = user.Keys;
Console.WriteLine(string.Join(", ", keys));           // Output: name, age, city

// Get all values
var values = user.Values;
Console.WriteLine(string.Join(", ", values));         // Output: John, 30, NYC

// Check if property exists
Console.WriteLine(user.ContainsKey("name"));          // Output: True

// Remove property
user.Remove("city");
Console.WriteLine(string.Join(", ", user.Keys));      // Output: name, age
```

---

## Enums

**What it does**: Define a set of named constants (like days of week, status codes, etc.)

```javascript
// JavaScript - No built-in enums, use objects
const Status = {
  PENDING: 'pending',
  APPROVED: 'approved',
  REJECTED: 'rejected'
};

let orderStatus = Status.PENDING;
console.log(orderStatus);                             // Output: pending

if (orderStatus === Status.APPROVED) {
  console.log("Order approved!");
}

// Or use Object.freeze to prevent changes
const DayOfWeek = Object.freeze({
  MONDAY: 1,
  TUESDAY: 2,
  WEDNESDAY: 3,
  THURSDAY: 4,
  FRIDAY: 5,
  SATURDAY: 6,
  SUNDAY: 7
});

console.log(DayOfWeek.MONDAY);                        // Output: 1
```

```typescript
// TypeScript - Has real enums
enum Status {
  Pending = 'pending',
  Approved = 'approved',
  Rejected = 'rejected'
}

let orderStatus: Status = Status.Pending;
console.log(orderStatus);                             // Output: pending

if (orderStatus === Status.Approved) {
  console.log("Order approved!");
}

// Numeric enums
enum DayOfWeek {
  Monday = 1,
  Tuesday = 2,
  Wednesday = 3,
  Thursday = 4,
  Friday = 5,
  Saturday = 6,
  Sunday = 7
}

console.log(DayOfWeek.Monday);                        // Output: 1
console.log(DayOfWeek[1]);                            // Output: "Monday" (reverse mapping)

// Auto-incrementing
enum Color {
  Red,      // 0
  Green,    // 1
  Blue      // 2
}

console.log(Color.Green);                             // Output: 1
```

```csharp
// C#
enum Status {
    Pending,
    Approved,
    Rejected
}

var orderStatus = Status.Pending;
Console.WriteLine(orderStatus);                       // Output: Pending

if (orderStatus == Status.Approved) {
    Console.WriteLine("Order approved!");
}

// With explicit values
enum DayOfWeek {
    Monday = 1,
    Tuesday = 2,
    Wednesday = 3,
    Thursday = 4,
    Friday = 5,
    Saturday = 6,
    Sunday = 7
}

Console.WriteLine(DayOfWeek.Monday);                  // Output: Monday
Console.WriteLine((int)DayOfWeek.Monday);             // Output: 1

// String representation
Console.WriteLine(DayOfWeek.Monday.ToString());       // Output: "Monday"

// Parse from string
var day = Enum.Parse<DayOfWeek>("Monday");
Console.WriteLine(day);                               // Output: Monday

// Get all values
foreach (var d in Enum.GetValues<DayOfWeek>()) {
    Console.WriteLine(d);                             // Output: Monday, Tuesday, ...
}
```

---

## Regular Expressions

**What it does**: Pattern matching and text manipulation using patterns

### Basic Pattern Matching

```javascript
// JavaScript
const text = "My email is john@example.com";

// Create regex
const emailPattern = /\w+@\w+\.\w+/;
const emailPattern2 = new RegExp("\\w+@\\w+\\.\\w+");

// Test if pattern exists
console.log(emailPattern.test(text));                 // Output: true

// Find first match
const match = text.match(emailPattern);
console.log(match[0]);                                // Output: john@example.com

// Find all matches (with 'g' flag)
const text2 = "Emails: john@test.com, jane@test.com";
const allMatches = text2.match(/\w+@\w+\.\w+/g);
console.log(allMatches);                              // Output: ["john@test.com", "jane@test.com"]

// Replace
const censored = text.replace(emailPattern, "[REDACTED]");
console.log(censored);                                // Output: My email is [REDACTED]

// Common flags
// g = global (find all)
// i = case-insensitive
// m = multiline
const pattern = /hello/gi;  // Case-insensitive, find all
```

```typescript
// TypeScript
const text: string = "My email is john@example.com";
const emailPattern: RegExp = /\w+@\w+\.\w+/;

const match: RegExpMatchArray | null = text.match(emailPattern);
if (match) {
    console.log(match[0]);                            // Output: john@example.com
}
```

```csharp
// C#
using System.Text.RegularExpressions;

var text = "My email is john@example.com";
var emailPattern = @"\w+@\w+\.\w+";  // @ prefix for verbatim string

// Test if pattern exists
var isMatch = Regex.IsMatch(text, emailPattern);
Console.WriteLine(isMatch);                           // Output: True

// Find first match
var match = Regex.Match(text, emailPattern);
Console.WriteLine(match.Value);                       // Output: john@example.com

// Find all matches
var text2 = "Emails: john@test.com, jane@test.com";
var matches = Regex.Matches(text2, emailPattern);
foreach (Match m in matches) {
    Console.WriteLine(m.Value);                       // Output: john@test.com, jane@test.com
}

// Replace
var censored = Regex.Replace(text, emailPattern, "[REDACTED]");
Console.WriteLine(censored);                          // Output: My email is [REDACTED]

// With options (flags)
var pattern = new Regex(@"hello", RegexOptions.IgnoreCase);
```

### Common Patterns

```javascript
// JavaScript
// Email validation
const email = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
console.log(email.test("user@example.com"));          // Output: true

// Phone number (US)
const phone = /^\d{3}-\d{3}-\d{4}$/;
console.log(phone.test("555-123-4567"));              // Output: true

// URL
const url = /https?:\/\/[\w\.-]+/;
console.log(url.test("https://example.com"));         // Output: true

// Only numbers
const onlyNumbers = /^\d+$/;
console.log(onlyNumbers.test("12345"));               // Output: true

// Only letters
const onlyLetters = /^[a-zA-Z]+$/;
console.log(onlyLetters.test("Hello"));               // Output: true

// Extract numbers from text
const text = "I have 5 apples and 3 oranges";
const numbers = text.match(/\d+/g);
console.log(numbers);                                 // Output: ["5", "3"]
```

```typescript
// TypeScript
const email: RegExp = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
const isValid: boolean = email.test("user@example.com");
console.log(isValid);                                 // Output: true
```

```csharp
// C#
// Email validation
var email = @"^[^\s@]+@[^\s@]+\.[^\s@]+$";
Console.WriteLine(Regex.IsMatch("user@example.com", email));  // Output: True

// Phone number (US)
var phone = @"^\d{3}-\d{3}-\d{4}$";
Console.WriteLine(Regex.IsMatch("555-123-4567", phone));      // Output: True

// Only numbers
var onlyNumbers = @"^\d+$";
Console.WriteLine(Regex.IsMatch("12345", onlyNumbers));       // Output: True

// Extract numbers from text
var text = "I have 5 apples and 3 oranges";
var numbers = Regex.Matches(text, @"\d+");
foreach (Match m in numbers) {
    Console.WriteLine(m.Value);                       // Output: 5, 3
}
```

---

## Promises & Tasks

**What it does**: Handle operations that complete in the future (before async/await was common)

### Creating Promises/Tasks

```javascript
// JavaScript
// Create a promise
const myPromise = new Promise((resolve, reject) => {
  setTimeout(() => {
    const success = true;
    if (success) {
      resolve("Operation succeeded!");
    } else {
      reject("Operation failed!");
    }
  }, 1000);
});

// Using the promise
myPromise
  .then(result => {
    console.log(result);                              // Output: Operation succeeded!
  })
  .catch(error => {
    console.log(error);
  })
  .finally(() => {
    console.log("Done!");                             // Output: Done!
  });
```

```typescript
// TypeScript
const myPromise: Promise<string> = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("Operation succeeded!");
  }, 1000);
});

myPromise
  .then((result: string) => {
    console.log(result);                              // Output: Operation succeeded!
  })
  .catch((error: any) => {
    console.log(error);
  });
```

```csharp
// C#
// Create a task
public Task<string> MyTaskAsync() {
    return Task.Run(() => {
        Thread.Sleep(1000);  // Simulate work
        var success = true;
        if (success) {
            return "Operation succeeded!";
        } else {
            throw new Exception("Operation failed!");
        }
    });
}

// Using the task
MyTaskAsync()
    .ContinueWith(task => {
        if (task.IsCompletedSuccessfully) {
            Console.WriteLine(task.Result);           // Output: Operation succeeded!
        } else {
            Console.WriteLine(task.Exception?.Message);
        }
    });

// Or with async/await (better)
try {
    var result = await MyTaskAsync();
    Console.WriteLine(result);                        // Output: Operation succeeded!
} catch (Exception ex) {
    Console.WriteLine(ex.Message);
} finally {
    Console.WriteLine("Done!");                       // Output: Done!
}
```

### Promise.all / Task.WhenAll

**What it does**: Run multiple async operations in parallel

```javascript
// JavaScript
const promise1 = Promise.resolve(1);
const promise2 = Promise.resolve(2);
const promise3 = Promise.resolve(3);

Promise.all([promise1, promise2, promise3])
  .then(results => {
    console.log(results);                             // Output: [1, 2, 3]
  });

// With async/await
async function runAll() {
  const results = await Promise.all([
    fetchUser(),
    fetchPosts(),
    fetchComments()
  ]);
  console.log(results);  // All three results
}
```

```typescript
// TypeScript
const promises: Promise<number>[] = [
  Promise.resolve(1),
  Promise.resolve(2),
  Promise.resolve(3)
];

Promise.all(promises).then((results: number[]) => {
  console.log(results);                               // Output: [1, 2, 3]
});
```

```csharp
// C#
var task1 = Task.FromResult(1);
var task2 = Task.FromResult(2);
var task3 = Task.FromResult(3);

var results = await Task.WhenAll(task1, task2, task3);
Console.WriteLine(string.Join(", ", results));        // Output: 1, 2, 3

// With multiple async methods
var results2 = await Task.WhenAll(
    FetchUserAsync(),
    FetchPostsAsync(),
    FetchCommentsAsync()
);
```

---

## HTTP/API Calls

**What it does**: Make requests to web servers/APIs to get or send data

### Fetch API (JavaScript/TypeScript)

```javascript
// JavaScript
// GET request
fetch('https://api.example.com/users')
  .then(response => response.json())
  .then(data => {
    console.log(data);                                // Output: Array of users
  })
  .catch(error => {
    console.error('Error:', error);
  });

// With async/await (cleaner)
async function getUsers() {
  try {
    const response = await fetch('https://api.example.com/users');

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }

    const data = await response.json();
    console.log(data);                                // Output: Array of users
  } catch (error) {
    console.error('Error:', error);
  }
}

// POST request
async function createUser(user) {
  try {
    const response = await fetch('https://api.example.com/users', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(user)
    });

    const data = await response.json();
    console.log('Created:', data);
  } catch (error) {
    console.error('Error:', error);
  }
}

createUser({ name: 'John', email: 'john@example.com' });

// PUT/PATCH request (update)
async function updateUser(id, updates) {
  const response = await fetch(`https://api.example.com/users/${id}`, {
    method: 'PUT',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(updates)
  });
  return response.json();
}

// DELETE request
async function deleteUser(id) {
  const response = await fetch(`https://api.example.com/users/${id}`, {
    method: 'DELETE'
  });
  return response.json();
}
```

```typescript
// TypeScript
interface User {
  id: number;
  name: string;
  email: string;
}

async function getUsers(): Promise<User[]> {
  try {
    const response = await fetch('https://api.example.com/users');

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }

    const data: User[] = await response.json();
    return data;
  } catch (error) {
    console.error('Error:', error);
    throw error;
  }
}

async function createUser(user: Omit<User, 'id'>): Promise<User> {
  const response = await fetch('https://api.example.com/users', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(user)
  });

  return response.json();
}
```

### HttpClient (C#)

```csharp
// C#
using System.Net.Http;
using System.Net.Http.Json;

public class User {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Email { get; set; }
}

// GET request
public async Task<List<User>> GetUsersAsync() {
    using var client = new HttpClient();

    try {
        var response = await client.GetAsync("https://api.example.com/users");
        response.EnsureSuccessStatusCode();

        var users = await response.Content.ReadFromJsonAsync<List<User>>();
        return users;
    } catch (HttpRequestException ex) {
        Console.WriteLine($"Error: {ex.Message}");
        throw;
    }
}

// Shorter version
public async Task<List<User>> GetUsersAsync() {
    using var client = new HttpClient();
    var users = await client.GetFromJsonAsync<List<User>>("https://api.example.com/users");
    return users;
}

// POST request
public async Task<User> CreateUserAsync(User user) {
    using var client = new HttpClient();

    var response = await client.PostAsJsonAsync("https://api.example.com/users", user);
    response.EnsureSuccessStatusCode();

    var created = await response.Content.ReadFromJsonAsync<User>();
    return created;
}

// PUT request (update)
public async Task<User> UpdateUserAsync(int id, User user) {
    using var client = new HttpClient();

    var response = await client.PutAsJsonAsync($"https://api.example.com/users/{id}", user);
    response.EnsureSuccessStatusCode();

    return await response.Content.ReadFromJsonAsync<User>();
}

// DELETE request
public async Task DeleteUserAsync(int id) {
    using var client = new HttpClient();

    var response = await client.DeleteAsync($"https://api.example.com/users/{id}");
    response.EnsureSuccessStatusCode();
}

// With headers
public async Task<List<User>> GetUsersWithAuthAsync(string token) {
    using var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Authorization", $"Bearer {token}");

    var users = await client.GetFromJsonAsync<List<User>>("https://api.example.com/users");
    return users;
}
```

---

## Modules & Imports

**What it does**: Split code into separate files and import/export functionality

### ES6 Modules (JavaScript/TypeScript)

```javascript
// JavaScript/TypeScript

// ========== math.js ==========
// Named exports
export function add(a, b) {
  return a + b;
}

export function subtract(a, b) {
  return a - b;
}

export const PI = 3.14159;

// ========== user.js ==========
// Default export
export default class User {
  constructor(name) {
    this.name = name;
  }
}

// Can have both default and named exports
export function formatUser(user) {
  return user.name.toUpperCase();
}

// ========== main.js ==========
// Import named exports
import { add, subtract, PI } from './math.js';

console.log(add(5, 3));                               // Output: 8
console.log(PI);                                      // Output: 3.14159

// Import all as namespace
import * as math from './math.js';

console.log(math.add(5, 3));                          // Output: 8
console.log(math.PI);                                 // Output: 3.14159

// Import default export
import User from './user.js';

const user = new User('John');

// Import both default and named
import User, { formatUser } from './user.js';

console.log(formatUser(user));                        // Output: JOHN

// Rename imports
import { add as sum, subtract as diff } from './math.js';

console.log(sum(5, 3));                               // Output: 8
```

```typescript
// TypeScript

// ========== math.ts ==========
export function add(a: number, b: number): number {
  return a + b;
}

export const PI: number = 3.14159;

// ========== user.ts ==========
export default class User {
  constructor(public name: string) {}
}

export function formatUser(user: User): string {
  return user.name.toUpperCase();
}

// ========== main.ts ==========
import { add, PI } from './math';
import User, { formatUser } from './user';

const result: number = add(5, 3);
const user: User = new User('John');
```

### C# Using and Namespaces

```csharp
// C#

// ========== Math.cs ==========
namespace MyApp.Utilities
{
    public static class MathHelper
    {
        public static int Add(int a, int b)
        {
            return a + b;
        }

        public static int Subtract(int a, int b)
        {
            return a - b;
        }

        public const double PI = 3.14159;
    }
}

// ========== User.cs ==========
namespace MyApp.Models
{
    public class User
    {
        public string Name { get; set; }

        public User(string name)
        {
            Name = name;
        }
    }

    public static class UserHelper
    {
        public static string FormatUser(User user)
        {
            return user.Name.ToUpper();
        }
    }
}

// ========== Program.cs ==========
using MyApp.Utilities;
using MyApp.Models;
// Or alias
using MathUtil = MyApp.Utilities.MathHelper;

class Program
{
    static void Main()
    {
        // Use imported namespaces
        var result = MathHelper.Add(5, 3);
        Console.WriteLine(result);                    // Output: 8

        var user = new User("John");
        var formatted = UserHelper.FormatUser(user);
        Console.WriteLine(formatted);                 // Output: JOHN

        // Using alias
        var sum = MathUtil.Add(5, 3);
    }
}

// C# 10+ Global usings (in one file)
global using System;
global using System.Linq;
global using MyApp.Utilities;
```

---

## Local Storage

**What it does**: Store data in the browser (JavaScript/TypeScript only - persists between sessions)

### LocalStorage (JavaScript/TypeScript)

```javascript
// JavaScript

// Save data
localStorage.setItem('username', 'John');
localStorage.setItem('age', '30');

// Save object (must stringify)
const user = { name: 'John', age: 30 };
localStorage.setItem('user', JSON.stringify(user));

// Get data
const username = localStorage.getItem('username');
console.log(username);                                // Output: John

// Get object (must parse)
const userStr = localStorage.getItem('user');
const user2 = JSON.parse(userStr);
console.log(user2.name);                              // Output: John

// Remove item
localStorage.removeItem('username');

// Clear all
localStorage.clear();

// Check if exists
if (localStorage.getItem('username')) {
  console.log('Username exists');
}

// Get number of items
console.log(localStorage.length);                     // Output: number of items

// Get key by index
const key = localStorage.key(0);
console.log(key);                                     // Output: first key name
```

```typescript
// TypeScript

interface User {
  name: string;
  age: number;
}

// Save with type safety
const user: User = { name: 'John', age: 30 };
localStorage.setItem('user', JSON.stringify(user));

// Get with type safety
const userStr: string | null = localStorage.getItem('user');
if (userStr) {
  const user: User = JSON.parse(userStr);
  console.log(user.name);                             // Output: John
}

// Helper functions
function saveToStorage<T>(key: string, value: T): void {
  localStorage.setItem(key, JSON.stringify(value));
}

function getFromStorage<T>(key: string): T | null {
  const item = localStorage.getItem(key);
  return item ? JSON.parse(item) : null;
}

// Usage
saveToStorage<User>('user', { name: 'John', age: 30 });
const retrievedUser = getFromStorage<User>('user');
```

### SessionStorage (Similar but clears when tab closes)

```javascript
// JavaScript

// Same API as localStorage, but data clears when tab closes
sessionStorage.setItem('tempData', 'value');
const temp = sessionStorage.getItem('tempData');
sessionStorage.removeItem('tempData');
sessionStorage.clear();
```

### C# Equivalent (Not browser-based)

```csharp
// C# - No built-in localStorage (it's a browser feature)
// But you can use:

// 1. File-based storage
using System.IO;

public static class LocalStorage
{
    private static string GetFilePath(string key)
    {
        var appData = Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData);
        return Path.Combine(appData, "MyApp", $"{key}.json");
    }

    public static void SetItem<T>(string key, T value)
    {
        var json = JsonSerializer.Serialize(value);
        var filePath = GetFilePath(key);
        Directory.CreateDirectory(Path.GetDirectoryName(filePath));
        File.WriteAllText(filePath, json);
    }

    public static T GetItem<T>(string key)
    {
        var filePath = GetFilePath(key);
        if (!File.Exists(filePath)) return default(T);

        var json = File.ReadAllText(filePath);
        return JsonSerializer.Deserialize<T>(json);
    }

    public static void RemoveItem(string key)
    {
        var filePath = GetFilePath(key);
        if (File.Exists(filePath))
            File.Delete(filePath);
    }
}

// Usage
LocalStorage.SetItem("user", new User { Name = "John", Age = 30 });
var user = LocalStorage.GetItem<User>("user");
LocalStorage.RemoveItem("user");
```

---

## Common Gotchas & Pitfalls

**What it does**: Common mistakes developers make and how to avoid them

### JavaScript/TypeScript Gotchas

```javascript
// ⚠️ GOTCHA 1: Floating point precision
console.log(0.1 + 0.2);                               // Output: 0.30000000000004 (NOT 0.3!)
console.log(0.1 + 0.2 === 0.3);                       // Output: false

// ✅ FIX: Use toFixed() or multiply by 10
console.log((0.1 + 0.2).toFixed(1));                  // Output: "0.3"
console.log((0.1 * 10 + 0.2 * 10) / 10);              // Output: 0.3

// ⚠️ GOTCHA 2: == vs ===
console.log(5 == "5");                                // Output: true (type coercion!)
console.log(5 === "5");                               // Output: false

console.log(0 == false);                              // Output: true
console.log("" == false);                             // Output: true

// ✅ FIX: Always use === and !==
console.log(5 === 5);                                 // Output: true

// ⚠️ GOTCHA 3: Array.sort() doesn't work as expected with numbers
const numbers = [1, 5, 2, 10, 20];
numbers.sort();
console.log(numbers);                                 // Output: [1, 10, 2, 20, 5] (WRONG!)

// ✅ FIX: Provide compare function
numbers.sort((a, b) => a - b);
console.log(numbers);                                 // Output: [1, 2, 5, 10, 20]

// ⚠️ GOTCHA 4: map() doesn't mutate, must save result
const arr = [1, 2, 3];
arr.map(x => x * 2);                                  // Does nothing!
console.log(arr);                                     // Output: [1, 2, 3]

// ✅ FIX: Save the result
const doubled = arr.map(x => x * 2);
console.log(doubled);                                 // Output: [2, 4, 6]

// ⚠️ GOTCHA 5: forEach() returns undefined
const result = [1, 2, 3].forEach(x => x * 2);
console.log(result);                                  // Output: undefined

// ✅ FIX: Use map() if you need a result
const result2 = [1, 2, 3].map(x => x * 2);
console.log(result2);                                 // Output: [2, 4, 6]

// ⚠️ GOTCHA 6: Async in forEach doesn't work
const urls = ['url1', 'url2', 'url3'];
urls.forEach(async (url) => {
  await fetch(url);  // Doesn't wait!
});
console.log('Done');  // Prints immediately!

// ✅ FIX: Use for...of or Promise.all
for (const url of urls) {
  await fetch(url);
}

// Or
await Promise.all(urls.map(url => fetch(url)));

// ⚠️ GOTCHA 7: const doesn't make objects immutable
const obj = { name: 'John' };
obj.name = 'Jane';  // This works!
console.log(obj.name);                                // Output: Jane

obj = {};  // ❌ Error: Cannot reassign const

// ✅ FIX: Use Object.freeze() for true immutability
const frozen = Object.freeze({ name: 'John' });
frozen.name = 'Jane';  // Silently fails (or error in strict mode)
console.log(frozen.name);                             // Output: John

// ⚠️ GOTCHA 8: Callback function loses 'this'
class Counter {
  constructor() {
    this.count = 0;
  }

  increment() {
    setTimeout(function() {
      this.count++;  // 'this' is undefined or window!
    }, 1000);
  }
}

// ✅ FIX: Use arrow function
class Counter {
  constructor() {
    this.count = 0;
  }

  increment() {
    setTimeout(() => {
      this.count++;  // 'this' works correctly!
    }, 1000);
  }
}
```

### C# Gotchas

```csharp
// ⚠️ GOTCHA 1: Integer division
var result = 5 / 2;
Console.WriteLine(result);                            // Output: 2 (NOT 2.5!)

// ✅ FIX: Cast to double
var result2 = 5 / 2.0;
Console.WriteLine(result2);                           // Output: 2.5

var result3 = (double)5 / 2;
Console.WriteLine(result3);                           // Output: 2.5

// ⚠️ GOTCHA 2: String comparison case-sensitivity
var str1 = "Hello";
var str2 = "hello";
Console.WriteLine(str1 == str2);                      // Output: False

// ✅ FIX: Use case-insensitive comparison
Console.WriteLine(str1.Equals(str2, StringComparison.OrdinalIgnoreCase));  // Output: True

// ⚠️ GOTCHA 3: Null reference exceptions
string name = null;
Console.WriteLine(name.Length);                       // ❌ NullReferenceException!

// ✅ FIX: Check for null first
if (name != null) {
    Console.WriteLine(name.Length);
}

// Or use null-conditional operator
Console.WriteLine(name?.Length);                      // Output: (nothing, no error)

// ⚠️ GOTCHA 4: DateTime is a struct (value type)
DateTime date = null;                                 // ❌ Error: Cannot be null

// ✅ FIX: Use nullable type
DateTime? date = null;  // OK
if (date.HasValue) {
    Console.WriteLine(date.Value);
}

// ⚠️ GOTCHA 5: String is immutable
var str = "Hello";
str.ToUpper();  // Doesn't change str!
Console.WriteLine(str);                               // Output: Hello

// ✅ FIX: Reassign the result
str = str.ToUpper();
Console.WriteLine(str);                               // Output: HELLO

// ⚠️ GOTCHA 6: Modifying collection while iterating
var list = new List<int> {1, 2, 3, 4, 5};
foreach (var item in list) {
    if (item % 2 == 0) {
        list.Remove(item);  // ❌ InvalidOperationException!
    }
}

// ✅ FIX: Use for loop or create new list
for (int i = list.Count - 1; i >= 0; i--) {
    if (list[i] % 2 == 0) {
        list.RemoveAt(i);
    }
}

// Or
var filtered = list.Where(x => x % 2 != 0).ToList();
```

---

## Tips & Common Mistakes

### JavaScript/TypeScript Tips

✅ **DO**: Use `const` for values that don't change, `let` for variables
❌ **DON'T**: Use `var` (old way, causes bugs)

✅ **DO**: Use template literals for strings: `` `Hello ${name}` ``
❌ **DON'T**: Concatenate with +: `"Hello " + name` (harder to read)

✅ **DO**: Use `===` for comparison (checks type too)
❌ **DON'T**: Use `==` (can cause unexpected results)

### C# Tips

✅ **DO**: Use `var` when the type is obvious: `var name = "John"`
❌ **DON'T**: Overuse `var` when it makes code unclear

✅ **DO**: Remember C# is case-sensitive: `.Length` not `.length`
❌ **DON'T**: Forget semicolons - C# requires them!

✅ **DO**: Use LINQ methods like `.Where()`, `.Select()` for lists
❌ **DON'T**: Write manual loops when LINQ can do it cleaner

---

## 🎯 Most Common Operations Quick Reference

### Print something
- JS/TS: `console.log("text")`
- C#: `Console.WriteLine("text")`

### Create a list of numbers
- JS/TS: `const numbers = [1, 2, 3]`
- C#: `var numbers = new List<int> {1, 2, 3}`

### Loop through a list
- JS/TS: `for (const item of list) { }`
- C#: `foreach (var item in list) { }`

### Filter a list
- JS/TS: `list.filter(x => x > 5)`
- C#: `list.Where(x => x > 5).ToList()`

### Transform each item
- JS/TS: `list.map(x => x * 2)`
- C#: `list.Select(x => x * 2).ToList()`

### Check if value is null
- JS/TS: `value ?? "default"`
- C#: `value ?? "default"`

### String interpolation
- JS/TS: `` `Hello ${name}` ``
- C#: `$"Hello {name}"`

---

**Remember**: The best way to learn is by practicing! Try writing small programs using these examples. When you forget syntax, come back to this cheat sheet! 🚀
