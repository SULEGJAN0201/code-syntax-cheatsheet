# ⚡ Quick Reference Card
## JavaScript, TypeScript & C# - One-Page Essentials

> Print this page and keep it next to your monitor! 📄

---

## 🟢 Variables & Output

| Task | JavaScript | TypeScript | C# |
|------|------------|------------|-----|
| Variable | `let name = "John"` | `let name: string = "John"` | `var name = "John"` |
| Constant | `const age = 30` | `const age: number = 30` | `const int age = 30` |
| Print | `console.log("Hi")` | `console.log("Hi")` | `Console.WriteLine("Hi")` |
| Interpolation | `` `Hi ${name}` `` | `` `Hi ${name}` `` | `$"Hi {name}"` |

## 🟢 Arrays

| Task | JavaScript | TypeScript | C# |
|------|------------|------------|-----|
| Create | `[1, 2, 3]` | `const arr: number[] = [1, 2, 3]` | `new List<int> {1, 2, 3}` |
| Add | `arr.push(4)` | `arr.push(4)` | `arr.Add(4)` |
| Remove last | `arr.pop()` | `arr.pop()` | `arr.RemoveAt(arr.Count - 1)` |
| Length | `arr.length` | `arr.length` | `arr.Count` |
| Get item | `arr[0]` | `arr[0]` | `arr[0]` |

## 🟡 Loops

| Task | JavaScript | TypeScript | C# |
|------|------------|------------|-----|
| For loop | `for (let i = 0; i < 10; i++)` | `for (let i = 0; i < 10; i++)` | `for (int i = 0; i < 10; i++)` |
| For each | `for (const x of arr)` | `for (const x of arr)` | `foreach (var x in arr)` |
| While | `while (condition)` | `while (condition)` | `while (condition)` |

## 🟡 Functions

| Task | JavaScript | TypeScript | C# |
|------|------------|------------|-----|
| Function | `function add(a, b) { return a + b; }` | `function add(a: number, b: number): number { return a + b; }` | `public int Add(int a, int b) { return a + b; }` |
| Arrow/Lambda | `const add = (a, b) => a + b` | `const add = (a: number, b: number) => a + b` | `Func<int, int, int> add = (a, b) => a + b` |

## 🟡 Conditionals

| Task | JavaScript | TypeScript | C# |
|------|------------|------------|-----|
| If | `if (x > 5) { }` | `if (x > 5) { }` | `if (x > 5) { }` |
| If-else | `if (x) { } else { }` | `if (x) { } else { }` | `if (x) { } else { }` |
| Ternary | `const r = x ? "yes" : "no"` | `const r = x ? "yes" : "no"` | `var r = x ? "yes" : "no"` |
| Switch | `switch (x) { case 1: break; }` | `switch (x) { case 1: break; }` | `switch (x) { case 1: break; }` |

## 🟡 Classes

| Task | JavaScript | TypeScript | C# |
|------|------------|------------|-----|
| Define | `class User { constructor(name) { this.name = name; } }` | `class User { name: string; constructor(name: string) { this.name = name; } }` | `public class User { public string Name { get; set; } }` |
| Create | `const u = new User("John")` | `const u = new User("John")` | `var u = new User { Name = "John" }` |
| Inherit | `class Admin extends User` | `class Admin extends User` | `class Admin : User` |

## 🔴 Array Methods / LINQ

| Task | JavaScript | TypeScript | C# |
|------|------------|------------|-----|
| Transform | `arr.map(x => x * 2)` | `arr.map(x => x * 2)` | `arr.Select(x => x * 2).ToList()` |
| Filter | `arr.filter(x => x > 5)` | `arr.filter(x => x > 5)` | `arr.Where(x => x > 5).ToList()` |
| Sum | `arr.reduce((a, b) => a + b, 0)` | `arr.reduce((a, b) => a + b, 0)` | `arr.Sum()` |
| Find | `arr.find(x => x > 5)` | `arr.find(x => x > 5)` | `arr.FirstOrDefault(x => x > 5)` |
| Sort | `arr.sort((a, b) => a - b)` | `arr.sort((a, b) => a - b)` | `arr.OrderBy(x => x).ToList()` |
| Check all | `arr.every(x => x > 0)` | `arr.every(x => x > 0)` | `arr.All(x => x > 0)` |
| Check any | `arr.some(x => x > 5)` | `arr.some(x => x > 5)` | `arr.Any(x => x > 5)` |

## 🔴 String Methods

| Task | JavaScript | TypeScript | C# |
|------|------------|------------|-----|
| Length | `str.length` | `str.length` | `str.Length` |
| Uppercase | `str.toUpperCase()` | `str.toUpperCase()` | `str.ToUpper()` |
| Lowercase | `str.toLowerCase()` | `str.toLowerCase()` | `str.ToLower()` |
| Contains | `str.includes("text")` | `str.includes("text")` | `str.Contains("text")` |
| Split | `str.split(" ")` | `str.split(" ")` | `str.Split(" ")` |
| Replace | `str.replace("old", "new")` | `str.replace("old", "new")` | `str.Replace("old", "new")` |
| Substring | `str.substring(0, 5)` | `str.substring(0, 5)` | `str.Substring(0, 5)` |

## 🔴 Type Conversion

| Task | JavaScript | TypeScript | C# |
|------|------------|------------|-----|
| String → Number | `parseInt("42")`, `Number("42")` | `parseInt("42")`, `Number("42")` | `int.Parse("42")`, `int.TryParse("42", out int n)` |
| Number → String | `num.toString()`, `String(num)` | `num.toString()`, `String(num)` | `num.ToString()` |
| To Boolean | `Boolean(value)` | `Boolean(value)` | `Convert.ToBoolean(value)` |

## 🔴 Null Handling

| Task | JavaScript | TypeScript | C# |
|------|------------|------------|-----|
| Check null | `if (x === null)` | `if (x === null)` | `if (x == null)` |
| Default value | `const v = x ?? "default"` | `const v = x ?? "default"` | `var v = x ?? "default"` |
| Safe access | `user?.address?.city` | `user?.address?.city` | `user?.Address?.City` |

## 🔴 Async/Await

| Task | JavaScript | TypeScript | C# |
|------|------------|------------|-----|
| Async function | `async function f() { }` | `async function f(): Promise<void> { }` | `public async Task F() { }` |
| Await | `const data = await fetch(url)` | `const data = await fetch(url)` | `var data = await GetDataAsync()` |
| Try-catch | `try { await f() } catch (e) { }` | `try { await f() } catch (e) { }` | `try { await F() } catch (Exception e) { }` |

## 🔴 HTTP Calls

| Task | JavaScript/TypeScript | C# |
|------|------------|-----|
| GET | `const r = await fetch(url); const data = await r.json();` | `var data = await client.GetFromJsonAsync<T>(url);` |
| POST | `await fetch(url, { method: 'POST', body: JSON.stringify(data) })` | `await client.PostAsJsonAsync(url, data);` |

## ⚡ Common Operators

| Operator | Meaning | Example |
|----------|---------|---------|
| `+` | Add | `5 + 3 = 8` |
| `-` | Subtract | `5 - 3 = 2` |
| `*` | Multiply | `5 * 3 = 15` |
| `/` | Divide | `6 / 3 = 2` |
| `%` | Modulo (remainder) | `5 % 2 = 1` |
| `===` | Equals (strict) | `5 === 5` ✅ `5 === "5"` ❌ |
| `!==` | Not equals (strict) | `5 !== 3` ✅ |
| `>` | Greater than | `5 > 3` ✅ |
| `<` | Less than | `3 < 5` ✅ |
| `&&` | AND | `true && false = false` |
| `||` | OR | `true || false = true` |
| `!` | NOT | `!true = false` |

## ⚠️ Common Mistakes to Avoid

❌ **DON'T**:
- JS/TS: Use `var` → Use `let` or `const`
- JS/TS: Use `==` → Use `===`
- JS/TS: Forget `await` in async functions
- C#: Forget semicolons `;`
- C#: Use `.length` → Use `.Length` (capital L!)
- All: Modify array while looping through it

✅ **DO**:
- Always check for null before accessing properties
- Use meaningful variable names
- Handle errors with try-catch
- Comment complex logic
- Use const/readonly when values don't change

## 💡 Pro Tips

1. **JS/TS**: `console.log()` is your best friend for debugging
2. **C#**: Use LINQ methods (`.Where()`, `.Select()`) instead of manual loops
3. **All**: Use arrow functions/lambdas for cleaner code
4. **All**: Break long functions into smaller, reusable ones
5. **All**: Use version control (git) always!

---

**📚 For detailed examples, see the main [SYNTAX-CHEATSHEET.md](SYNTAX-CHEATSHEET.md)**
**🎯 For real-world examples, see [REAL-WORLD-EXAMPLES.md](REAL-WORLD-EXAMPLES.md)**
**🏋️ For practice, see [PRACTICE-EXERCISES.md](PRACTICE-EXERCISES.md)**
