# Beginner-Friendly Syntax Cheat Sheet
## JavaScript, TypeScript & C# - Side by Side

> **Quick Guide**: This cheat sheet helps you quickly remember syntax when switching between JavaScript, TypeScript, and C#. Each example includes comments showing the output!

---

## Table of Contents
1. [Variables & Types](#variables--types)
2. [Console Output / Printing](#console-output--printing)
3. [Arrays & Collections](#arrays--collections)
4. [Loops & Iteration](#loops--iteration)
5. [Functions & Methods](#functions--methods)
6. [Classes & Objects](#classes--objects)
7. [Conditionals (If/Else/Switch)](#conditionals)
8. [String Operations](#string-operations)
9. [Null Handling](#null-handling)
10. [Async/Await](#asyncawait)
11. [Error Handling](#error-handling)
12. [Array Methods & LINQ](#array-methods--linq)
13. [Interfaces & Types](#interfaces--types)
14. [Generics](#generics)
15. [Destructuring](#destructuring)
16. [Spread/Rest Operators](#spreadrest-operators)
17. [Arrow Functions & Lambdas](#arrow-functions--lambdas)
18. [Date & Time](#date--time)
19. [JSON Operations](#json-operations)
20. [File Operations](#file-operations)
21. [Common Data Structures](#common-data-structures)

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
