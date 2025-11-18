# 🏋️ Practice Exercises
## Test Your Knowledge - JavaScript, TypeScript & C#

> Learn by doing! Try to solve these without looking at the answers first.

**Difficulty Levels**: 🟢 Beginner | 🟡 Intermediate | 🔴 Advanced

---

## Table of Contents
1. [Arrays & Loops](#arrays--loops)
2. [Functions](#functions)
3. [Objects & Classes](#objects--classes)
4. [Array Methods](#array-methods)
5. [Async Programming](#async-programming)
6. [Challenges](#challenges)

---

## Arrays & Loops

### 🟢 Exercise 1: Sum of Array

**Task**: Write a function that returns the sum of all numbers in an array.

**Example**:
```
Input: [1, 2, 3, 4, 5]
Output: 15
```

<details>
<summary>💡 Click for hints</summary>

- Use a loop or array method
- Keep a running total
- Return the total at the end
</details>

<details>
<summary>✅ Click for solution</summary>

**JavaScript**:
```javascript
function sum(numbers) {
  let total = 0;
  for (const num of numbers) {
    total += num;
  }
  return total;
}

// Or using reduce
const sum = (numbers) => numbers.reduce((total, num) => total + num, 0);

console.log(sum([1, 2, 3, 4, 5]));  // 15
```

**TypeScript**:
```typescript
function sum(numbers: number[]): number {
  return numbers.reduce((total, num) => total + num, 0);
}
```

**C#**:
```csharp
public int Sum(int[] numbers) {
    int total = 0;
    foreach (var num in numbers) {
        total += num;
    }
    return total;
}

// Or using LINQ
public int Sum(int[] numbers) {
    return numbers.Sum();
}
```
</details>

---

### 🟢 Exercise 2: Find Maximum

**Task**: Write a function that finds the largest number in an array.

**Example**:
```
Input: [3, 7, 2, 9, 1]
Output: 9
```

<details>
<summary>✅ Click for solution</summary>

**JavaScript**:
```javascript
function findMax(numbers) {
  let max = numbers[0];
  for (const num of numbers) {
    if (num > max) {
      max = num;
    }
  }
  return max;
}

// Or simpler
const findMax = (numbers) => Math.max(...numbers);

console.log(findMax([3, 7, 2, 9, 1]));  // 9
```

**C#**:
```csharp
public int FindMax(int[] numbers) {
    int max = numbers[0];
    foreach (var num in numbers) {
        if (num > max) {
            max = num;
        }
    }
    return max;
}

// Or using LINQ
public int FindMax(int[] numbers) {
    return numbers.Max();
}
```
</details>

---

### 🟡 Exercise 3: Count Occurrences

**Task**: Write a function that counts how many times each value appears in an array.

**Example**:
```
Input: ["apple", "banana", "apple", "orange", "banana", "apple"]
Output: { apple: 3, banana: 2, orange: 1 }
```

<details>
<summary>✅ Click for solution</summary>

**JavaScript**:
```javascript
function countOccurrences(arr) {
  const counts = {};

  for (const item of arr) {
    counts[item] = (counts[item] || 0) + 1;
  }

  return counts;
}

// Or using reduce
const countOccurrences = (arr) =>
  arr.reduce((counts, item) => {
    counts[item] = (counts[item] || 0) + 1;
    return counts;
  }, {});

console.log(countOccurrences(["apple", "banana", "apple", "orange", "banana", "apple"]));
// { apple: 3, banana: 2, orange: 1 }
```

**TypeScript**:
```typescript
function countOccurrences(arr: string[]): Record<string, number> {
  return arr.reduce((counts, item) => {
    counts[item] = (counts[item] || 0) + 1;
    return counts;
  }, {} as Record<string, number>);
}
```

**C#**:
```csharp
public Dictionary<string, int> CountOccurrences(string[] arr) {
    var counts = new Dictionary<string, int>();

    foreach (var item in arr) {
        if (counts.ContainsKey(item)) {
            counts[item]++;
        } else {
            counts[item] = 1;
        }
    }

    return counts;
}

// Or using LINQ
public Dictionary<string, int> CountOccurrences(string[] arr) {
    return arr.GroupBy(x => x)
              .ToDictionary(g => g.Key, g => g.Count());
}
```
</details>

---

## Functions

### 🟢 Exercise 4: Temperature Converter

**Task**: Write a function that converts Celsius to Fahrenheit.

**Formula**: `F = (C × 9/5) + 32`

**Example**:
```
Input: 0
Output: 32

Input: 100
Output: 212
```

<details>
<summary>✅ Click for solution</summary>

**JavaScript**:
```javascript
function celsiusToFahrenheit(celsius) {
  return (celsius * 9/5) + 32;
}

console.log(celsiusToFahrenheit(0));    // 32
console.log(celsiusToFahrenheit(100));  // 212
```

**C#**:
```csharp
public double CelsiusToFahrenheit(double celsius) {
    return (celsius * 9/5) + 32;
}

Console.WriteLine(CelsiusToFahrenheit(0));    // 32
Console.WriteLine(CelsiusToFahrenheit(100));  // 212
```
</details>

---

### 🟡 Exercise 5: Palindrome Checker

**Task**: Write a function that checks if a string is a palindrome (reads the same forwards and backwards).

**Example**:
```
Input: "racecar"
Output: true

Input: "hello"
Output: false

Input: "A man a plan a canal Panama" (ignore spaces and case)
Output: true
```

<details>
<summary>✅ Click for solution</summary>

**JavaScript**:
```javascript
function isPalindrome(str) {
  // Remove spaces and convert to lowercase
  const cleaned = str.toLowerCase().replace(/[^a-z0-9]/g, '');

  // Compare with reversed version
  const reversed = cleaned.split('').reverse().join('');

  return cleaned === reversed;
}

console.log(isPalindrome("racecar"));  // true
console.log(isPalindrome("hello"));    // false
console.log(isPalindrome("A man a plan a canal Panama"));  // true
```

**C#**:
```csharp
using System.Text.RegularExpressions;

public bool IsPalindrome(string str) {
    // Remove spaces and convert to lowercase
    var cleaned = Regex.Replace(str.ToLower(), @"[^a-z0-9]", "");

    // Compare with reversed version
    var reversed = new string(cleaned.Reverse().ToArray());

    return cleaned == reversed;
}

Console.WriteLine(IsPalindrome("racecar"));  // True
Console.WriteLine(IsPalindrome("hello"));    // False
```
</details>

---

## Objects & Classes

### 🟡 Exercise 6: Person Class

**Task**: Create a `Person` class with:
- Properties: `name`, `age`, `email`
- Method: `introduce()` that returns "Hi, I'm {name} and I'm {age} years old"
- Method: `isAdult()` that returns true if age >= 18

**Example**:
```
const person = new Person("John", 25, "john@example.com");
person.introduce();  // "Hi, I'm John and I'm 25 years old"
person.isAdult();    // true
```

<details>
<summary>✅ Click for solution</summary>

**JavaScript**:
```javascript
class Person {
  constructor(name, age, email) {
    this.name = name;
    this.age = age;
    this.email = email;
  }

  introduce() {
    return `Hi, I'm ${this.name} and I'm ${this.age} years old`;
  }

  isAdult() {
    return this.age >= 18;
  }
}

const person = new Person("John", 25, "john@example.com");
console.log(person.introduce());  // "Hi, I'm John and I'm 25 years old"
console.log(person.isAdult());    // true
```

**TypeScript**:
```typescript
class Person {
  name: string;
  age: number;
  email: string;

  constructor(name: string, age: number, email: string) {
    this.name = name;
    this.age = age;
    this.email = email;
  }

  introduce(): string {
    return `Hi, I'm ${this.name} and I'm ${this.age} years old`;
  }

  isAdult(): boolean {
    return this.age >= 18;
  }
}
```

**C#**:
```csharp
public class Person {
    public string Name { get; set; }
    public int Age { get; set; }
    public string Email { get; set; }

    public Person(string name, int age, string email) {
        Name = name;
        Age = age;
        Email = email;
    }

    public string Introduce() {
        return $"Hi, I'm {Name} and I'm {Age} years old";
    }

    public bool IsAdult() {
        return Age >= 18;
    }
}

var person = new Person("John", 25, "john@example.com");
Console.WriteLine(person.Introduce());  // "Hi, I'm John and I'm 25 years old"
Console.WriteLine(person.IsAdult());    // True
```
</details>

---

## Array Methods

### 🟡 Exercise 7: Get Adult Names

**Task**: Given an array of people, return an array of names of people who are 18 or older, sorted alphabetically.

**Example**:
```
Input: [
  { name: "Alice", age: 25 },
  { name: "Bob", age: 17 },
  { name: "Charlie", age: 30 },
  { name: "David", age: 16 }
]

Output: ["Alice", "Charlie"]
```

<details>
<summary>✅ Click for solution</summary>

**JavaScript**:
```javascript
function getAdultNames(people) {
  return people
    .filter(person => person.age >= 18)
    .map(person => person.name)
    .sort();
}

const people = [
  { name: "Alice", age: 25 },
  { name: "Bob", age: 17 },
  { name: "Charlie", age: 30 },
  { name: "David", age: 16 }
];

console.log(getAdultNames(people));  // ["Alice", "Charlie"]
```

**C#**:
```csharp
public List<string> GetAdultNames(List<Person> people) {
    return people
        .Where(p => p.Age >= 18)
        .Select(p => p.Name)
        .OrderBy(name => name)
        .ToList();
}
```
</details>

---

### 🔴 Exercise 8: Group By Age Range

**Task**: Group people into age ranges: "0-17", "18-64", "65+".

**Example**:
```
Input: [
  { name: "Alice", age: 25 },
  { name: "Bob", age: 17 },
  { name: "Charlie", age: 70 },
  { name: "David", age: 16 }
]

Output: {
  "0-17": ["Bob", "David"],
  "18-64": ["Alice"],
  "65+": ["Charlie"]
}
```

<details>
<summary>✅ Click for solution</summary>

**JavaScript**:
```javascript
function groupByAgeRange(people) {
  const groups = {
    "0-17": [],
    "18-64": [],
    "65+": []
  };

  for (const person of people) {
    if (person.age < 18) {
      groups["0-17"].push(person.name);
    } else if (person.age < 65) {
      groups["18-64"].push(person.name);
    } else {
      groups["65+"].push(person.name);
    }
  }

  return groups;
}

// Or using reduce
function groupByAgeRange(people) {
  return people.reduce((groups, person) => {
    const range = person.age < 18 ? "0-17" :
                  person.age < 65 ? "18-64" : "65+";

    groups[range] = groups[range] || [];
    groups[range].push(person.name);

    return groups;
  }, {});
}
```

**C#**:
```csharp
public Dictionary<string, List<string>> GroupByAgeRange(List<Person> people) {
    var groups = new Dictionary<string, List<string>> {
        {"0-17", new List<string>()},
        {"18-64", new List<string>()},
        {"65+", new List<string>()}
    };

    foreach (var person in people) {
        if (person.Age < 18) {
            groups["0-17"].Add(person.Name);
        } else if (person.Age < 65) {
            groups["18-64"].Add(person.Name);
        } else {
            groups["65+"].Add(person.Name);
        }
    }

    return groups;
}

// Or using LINQ
public Dictionary<string, List<string>> GroupByAgeRange(List<Person> people) {
    return people.GroupBy(p =>
        p.Age < 18 ? "0-17" :
        p.Age < 65 ? "18-64" : "65+")
    .ToDictionary(g => g.Key, g => g.Select(p => p.Name).ToList());
}
```
</details>

---

## Async Programming

### 🟡 Exercise 9: Fetch User Data

**Task**: Write an async function that fetches user data from an API and returns just the names.

**API**: `https://jsonplaceholder.typicode.com/users`

**Example Output**: `["Leanne Graham", "Ervin Howell", ...]`

<details>
<summary>✅ Click for solution</summary>

**JavaScript**:
```javascript
async function getUserNames() {
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/users');

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }

    const users = await response.json();

    return users.map(user => user.name);
  } catch (error) {
    console.error('Error fetching users:', error);
    return [];
  }
}

getUserNames().then(names => console.log(names));
```

**TypeScript**:
```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

async function getUserNames(): Promise<string[]> {
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/users');

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }

    const users: User[] = await response.json();

    return users.map(user => user.name);
  } catch (error) {
    console.error('Error fetching users:', error);
    return [];
  }
}
```

**C#**:
```csharp
using System.Net.Http.Json;

public class User {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Email { get; set; }
}

public async Task<List<string>> GetUserNamesAsync() {
    using var client = new HttpClient();

    try {
        var users = await client.GetFromJsonAsync<List<User>>(
            "https://jsonplaceholder.typicode.com/users"
        );

        return users.Select(u => u.Name).ToList();
    } catch (Exception ex) {
        Console.WriteLine($"Error fetching users: {ex.Message}");
        return new List<string>();
    }
}
```
</details>

---

## Challenges

### 🔴 Challenge 1: FizzBuzz

**Task**: Write a function that prints numbers from 1 to 100, but:
- For multiples of 3, print "Fizz"
- For multiples of 5, print "Buzz"
- For multiples of both 3 and 5, print "FizzBuzz"

**Example**:
```
1, 2, Fizz, 4, Buzz, Fizz, 7, 8, Fizz, Buzz, 11, Fizz, 13, 14, FizzBuzz, ...
```

<details>
<summary>✅ Click for solution</summary>

**JavaScript**:
```javascript
function fizzBuzz() {
  for (let i = 1; i <= 100; i++) {
    if (i % 15 === 0) {
      console.log("FizzBuzz");
    } else if (i % 3 === 0) {
      console.log("Fizz");
    } else if (i % 5 === 0) {
      console.log("Buzz");
    } else {
      console.log(i);
    }
  }
}

fizzBuzz();
```

**C#**:
```csharp
public void FizzBuzz() {
    for (int i = 1; i <= 100; i++) {
        if (i % 15 == 0) {
            Console.WriteLine("FizzBuzz");
        } else if (i % 3 == 0) {
            Console.WriteLine("Fizz");
        } else if (i % 5 == 0) {
            Console.WriteLine("Buzz");
        } else {
            Console.WriteLine(i);
        }
    }
}
```
</details>

---

### 🔴 Challenge 2: Remove Duplicates

**Task**: Write a function that removes duplicate values from an array while maintaining order.

**Example**:
```
Input: [1, 2, 2, 3, 4, 4, 5]
Output: [1, 2, 3, 4, 5]

Input: ["a", "b", "a", "c", "b"]
Output: ["a", "b", "c"]
```

<details>
<summary>✅ Click for solution</summary>

**JavaScript**:
```javascript
function removeDuplicates(arr) {
  return [...new Set(arr)];
}

// Or without Set
function removeDuplicates(arr) {
  return arr.filter((item, index) => arr.indexOf(item) === index);
}

console.log(removeDuplicates([1, 2, 2, 3, 4, 4, 5]));  // [1, 2, 3, 4, 5]
```

**C#**:
```csharp
public List<T> RemoveDuplicates<T>(List<T> list) {
    return list.Distinct().ToList();
}

// Or maintaining original order
public List<T> RemoveDuplicates<T>(List<T> list) {
    var seen = new HashSet<T>();
    var result = new List<T>();

    foreach (var item in list) {
        if (seen.Add(item)) {
            result.Add(item);
        }
    }

    return result;
}
```
</details>

---

### 🔴 Challenge 3: Flatten Nested Array

**Task**: Write a function that flattens a nested array (converts multi-dimensional array to single-dimensional).

**Example**:
```
Input: [1, [2, 3], [4, [5, 6]]]
Output: [1, 2, 3, 4, 5, 6]
```

<details>
<summary>✅ Click for solution</summary>

**JavaScript**:
```javascript
function flatten(arr) {
  return arr.flat(Infinity);
}

// Or recursive solution
function flatten(arr) {
  return arr.reduce((result, item) => {
    if (Array.isArray(item)) {
      return result.concat(flatten(item));
    } else {
      return result.concat(item);
    }
  }, []);
}

console.log(flatten([1, [2, 3], [4, [5, 6]]]));  // [1, 2, 3, 4, 5, 6]
```

**C#**:
```csharp
// Using nested lists
public List<int> Flatten(object obj) {
    var result = new List<int>();

    if (obj is int number) {
        result.Add(number);
    } else if (obj is IEnumerable<object> list) {
        foreach (var item in list) {
            result.AddRange(Flatten(item));
        }
    }

    return result;
}
```
</details>

---

## 🎯 Keep Practicing!

Try to solve these exercises yourself before looking at the solutions. The best way to learn programming is by actually writing code!

**Next Steps**:
1. Try variations of these exercises
2. Implement them in all three languages
3. Optimize your solutions
4. Create your own exercises!

**💡 For more examples, see [REAL-WORLD-EXAMPLES.md](REAL-WORLD-EXAMPLES.md)**
