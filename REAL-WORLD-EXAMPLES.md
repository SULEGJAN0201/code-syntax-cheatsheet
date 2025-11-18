# 🌍 Real-World Examples
## Complete Scenarios in JavaScript, TypeScript & C#

> Learn by doing! These are complete, practical examples you'll use in real projects.

---

## Table of Contents
1. [Fetch and Display API Data](#fetch-and-display-api-data)
2. [Form Validation](#form-validation)
3. [Search and Filter List](#search-and-filter-list)
4. [Pagination](#pagination)
5. [Debouncing (Rate Limiting)](#debouncing-rate-limiting)
6. [Sort Table by Column](#sort-table-by-column)
7. [Shopping Cart](#shopping-cart)
8. [Todo List (CRUD)](#todo-list-crud)

---

## Fetch and Display API Data

**Scenario**: Get users from an API and display them

### JavaScript

```javascript
// Fetch and display users
async function fetchAndDisplayUsers() {
  try {
    // Show loading
    document.getElementById('loading').style.display = 'block';

    // Fetch data
    const response = await fetch('https://jsonplaceholder.typicode.com/users');

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }

    const users = await response.json();

    // Hide loading
    document.getElementById('loading').style.display = 'none';

    // Display users
    const userList = document.getElementById('userList');
    userList.innerHTML = users.map(user => `
      <div class="user-card">
        <h3>${user.name}</h3>
        <p>Email: ${user.email}</p>
        <p>Company: ${user.company.name}</p>
      </div>
    `).join('');

  } catch (error) {
    document.getElementById('error').textContent = `Error: ${error.message}`;
    document.getElementById('loading').style.display = 'none';
  }
}

// Call when page loads
fetchAndDisplayUsers();
```

### TypeScript

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  company: {
    name: string;
  };
}

async function fetchAndDisplayUsers(): Promise<void> {
  try {
    const loadingEl = document.getElementById('loading') as HTMLElement;
    loadingEl.style.display = 'block';

    const response = await fetch('https://jsonplaceholder.typicode.com/users');

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }

    const users: User[] = await response.json();

    loadingEl.style.display = 'none';

    const userList = document.getElementById('userList') as HTMLElement;
    userList.innerHTML = users.map((user: User) => `
      <div class="user-card">
        <h3>${user.name}</h3>
        <p>Email: ${user.email}</p>
        <p>Company: ${user.company.name}</p>
      </div>
    `).join('');

  } catch (error) {
    const errorEl = document.getElementById('error') as HTMLElement;
    errorEl.textContent = `Error: ${(error as Error).message}`;
  }
}

fetchAndDisplayUsers();
```

### C#

```csharp
using System.Net.Http.Json;

public class Company {
    public string Name { get; set; }
}

public class User {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Email { get; set; }
    public Company Company { get; set; }
}

public async Task FetchAndDisplayUsersAsync() {
    using var client = new HttpClient();

    try {
        // Show loading
        Console.WriteLine("Loading...");

        // Fetch data
        var users = await client.GetFromJsonAsync<List<User>>(
            "https://jsonplaceholder.typicode.com/users"
        );

        // Display users
        foreach (var user in users) {
            Console.WriteLine($"Name: {user.Name}");
            Console.WriteLine($"Email: {user.Email}");
            Console.WriteLine($"Company: {user.Company.Name}");
            Console.WriteLine("---");
        }

    } catch (Exception ex) {
        Console.WriteLine($"Error: {ex.Message}");
    }
}
```

---

## Form Validation

**Scenario**: Validate user input before submitting

### JavaScript

```javascript
function validateForm(formData) {
  const errors = {};

  // Email validation
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  if (!formData.email) {
    errors.email = 'Email is required';
  } else if (!emailRegex.test(formData.email)) {
    errors.email = 'Invalid email format';
  }

  // Password validation
  if (!formData.password) {
    errors.password = 'Password is required';
  } else if (formData.password.length < 8) {
    errors.password = 'Password must be at least 8 characters';
  }

  // Name validation
  if (!formData.name || formData.name.trim() === '') {
    errors.name = 'Name is required';
  }

  // Age validation
  const age = parseInt(formData.age);
  if (isNaN(age)) {
    errors.age = 'Age must be a number';
  } else if (age < 18 || age > 100) {
    errors.age = 'Age must be between 18 and 100';
  }

  return {
    isValid: Object.keys(errors).length === 0,
    errors
  };
}

// Usage
const formData = {
  name: 'John Doe',
  email: 'john@example.com',
  password: 'password123',
  age: '25'
};

const validation = validateForm(formData);

if (validation.isValid) {
  console.log('Form is valid! Submitting...');
  // Submit form
} else {
  console.log('Validation errors:', validation.errors);
  // Show errors to user
}
```

### TypeScript

```typescript
interface FormData {
  name: string;
  email: string;
  password: string;
  age: string;
}

interface ValidationErrors {
  name?: string;
  email?: string;
  password?: string;
  age?: string;
}

interface ValidationResult {
  isValid: boolean;
  errors: ValidationErrors;
}

function validateForm(formData: FormData): ValidationResult {
  const errors: ValidationErrors = {};

  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  if (!formData.email) {
    errors.email = 'Email is required';
  } else if (!emailRegex.test(formData.email)) {
    errors.email = 'Invalid email format';
  }

  if (!formData.password) {
    errors.password = 'Password is required';
  } else if (formData.password.length < 8) {
    errors.password = 'Password must be at least 8 characters';
  }

  if (!formData.name || formData.name.trim() === '') {
    errors.name = 'Name is required';
  }

  const age = parseInt(formData.age);
  if (isNaN(age)) {
    errors.age = 'Age must be a number';
  } else if (age < 18 || age > 100) {
    errors.age = 'Age must be between 18 and 100';
  }

  return {
    isValid: Object.keys(errors).length === 0,
    errors
  };
}
```

### C#

```csharp
using System.Text.RegularExpressions;

public class FormData {
    public string Name { get; set; }
    public string Email { get; set; }
    public string Password { get; set; }
    public string Age { get; set; }
}

public class ValidationResult {
    public bool IsValid { get; set; }
    public Dictionary<string, string> Errors { get; set; } = new();
}

public ValidationResult ValidateForm(FormData formData) {
    var errors = new Dictionary<string, string>();

    // Email validation
    var emailRegex = new Regex(@"^[^\s@]+@[^\s@]+\.[^\s@]+$");
    if (string.IsNullOrEmpty(formData.Email)) {
        errors["email"] = "Email is required";
    } else if (!emailRegex.IsMatch(formData.Email)) {
        errors["email"] = "Invalid email format";
    }

    // Password validation
    if (string.IsNullOrEmpty(formData.Password)) {
        errors["password"] = "Password is required";
    } else if (formData.Password.Length < 8) {
        errors["password"] = "Password must be at least 8 characters";
    }

    // Name validation
    if (string.IsNullOrWhiteSpace(formData.Name)) {
        errors["name"] = "Name is required";
    }

    // Age validation
    if (!int.TryParse(formData.Age, out int age)) {
        errors["age"] = "Age must be a number";
    } else if (age < 18 || age > 100) {
        errors["age"] = "Age must be between 18 and 100";
    }

    return new ValidationResult {
        IsValid = errors.Count == 0,
        Errors = errors
    };
}

// Usage
var formData = new FormData {
    Name = "John Doe",
    Email = "john@example.com",
    Password = "password123",
    Age = "25"
};

var validation = ValidateForm(formData);

if (validation.IsValid) {
    Console.WriteLine("Form is valid! Submitting...");
} else {
    foreach (var error in validation.Errors) {
        Console.WriteLine($"{error.Key}: {error.Value}");
    }
}
```

---

## Search and Filter List

**Scenario**: Search/filter a list of items in real-time

### JavaScript

```javascript
class ProductFilter {
  constructor(products) {
    this.allProducts = products;
  }

  search(query) {
    const lowerQuery = query.toLowerCase();

    return this.allProducts.filter(product => {
      return (
        product.name.toLowerCase().includes(lowerQuery) ||
        product.category.toLowerCase().includes(lowerQuery) ||
        product.description.toLowerCase().includes(lowerQuery)
      );
    });
  }

  filterByCategory(category) {
    if (category === 'all') return this.allProducts;

    return this.allProducts.filter(product =>
      product.category === category
    );
  }

  filterByPriceRange(min, max) {
    return this.allProducts.filter(product =>
      product.price >= min && product.price <= max
    );
  }

  sortBy(field, order = 'asc') {
    const sorted = [...this.allProducts].sort((a, b) => {
      if (a[field] < b[field]) return order === 'asc' ? -1 : 1;
      if (a[field] > b[field]) return order === 'asc' ? 1 : -1;
      return 0;
    });

    return sorted;
  }

  // Combined filtering
  filter({ query, category, minPrice, maxPrice, sortBy, sortOrder }) {
    let results = this.allProducts;

    if (query) {
      results = results.filter(p =>
        p.name.toLowerCase().includes(query.toLowerCase())
      );
    }

    if (category && category !== 'all') {
      results = results.filter(p => p.category === category);
    }

    if (minPrice !== undefined || maxPrice !== undefined) {
      results = results.filter(p =>
        p.price >= (minPrice || 0) && p.price <= (maxPrice || Infinity)
      );
    }

    if (sortBy) {
      results.sort((a, b) => {
        if (a[sortBy] < b[sortBy]) return sortOrder === 'asc' ? -1 : 1;
        if (a[sortBy] > b[sortBy]) return sortOrder === 'asc' ? 1 : -1;
        return 0;
      });
    }

    return results;
  }
}

// Usage
const products = [
  { id: 1, name: 'Laptop', category: 'Electronics', price: 999, description: 'Gaming laptop' },
  { id: 2, name: 'Mouse', category: 'Electronics', price: 25, description: 'Wireless mouse' },
  { id: 3, name: 'Desk', category: 'Furniture', price: 199, description: 'Standing desk' }
];

const filter = new ProductFilter(products);

// Search
const laptops = filter.search('laptop');
console.log(laptops);  // [{ id: 1, name: 'Laptop', ... }]

// Filter by category
const electronics = filter.filterByCategory('Electronics');
console.log(electronics.length);  // 2

// Filter by price range
const affordable = filter.filterByPriceRange(0, 100);
console.log(affordable.length);  // 1

// Combined filtering
const results = filter.filter({
  category: 'Electronics',
  maxPrice: 500,
  sortBy: 'price',
  sortOrder: 'asc'
});
console.log(results);  // [{ id: 2, name: 'Mouse', ... }]
```

### TypeScript

```typescript
interface Product {
  id: number;
  name: string;
  category: string;
  price: number;
  description: string;
}

interface FilterOptions {
  query?: string;
  category?: string;
  minPrice?: number;
  maxPrice?: number;
  sortBy?: keyof Product;
  sortOrder?: 'asc' | 'desc';
}

class ProductFilter {
  constructor(private allProducts: Product[]) {}

  search(query: string): Product[] {
    const lowerQuery = query.toLowerCase();

    return this.allProducts.filter(product =>
      product.name.toLowerCase().includes(lowerQuery) ||
      product.category.toLowerCase().includes(lowerQuery) ||
      product.description.toLowerCase().includes(lowerQuery)
    );
  }

  filterByCategory(category: string): Product[] {
    if (category === 'all') return this.allProducts;

    return this.allProducts.filter(product =>
      product.category === category
    );
  }

  filter(options: FilterOptions): Product[] {
    let results = this.allProducts;

    if (options.query) {
      results = results.filter(p =>
        p.name.toLowerCase().includes(options.query!.toLowerCase())
      );
    }

    if (options.category && options.category !== 'all') {
      results = results.filter(p => p.category === options.category);
    }

    if (options.minPrice !== undefined || options.maxPrice !== undefined) {
      results = results.filter(p =>
        p.price >= (options.minPrice || 0) &&
        p.price <= (options.maxPrice || Infinity)
      );
    }

    if (options.sortBy) {
      results.sort((a, b) => {
        const aVal = a[options.sortBy!];
        const bVal = b[options.sortBy!];

        if (aVal < bVal) return options.sortOrder === 'asc' ? -1 : 1;
        if (aVal > bVal) return options.sortOrder === 'asc' ? 1 : -1;
        return 0;
      });
    }

    return results;
  }
}
```

### C#

```csharp
public class Product {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Category { get; set; }
    public double Price { get; set; }
    public string Description { get; set; }
}

public class ProductFilter {
    private readonly List<Product> allProducts;

    public ProductFilter(List<Product> products) {
        allProducts = products;
    }

    public List<Product> Search(string query) {
        var lowerQuery = query.ToLower();

        return allProducts.Where(product =>
            product.Name.ToLower().Contains(lowerQuery) ||
            product.Category.ToLower().Contains(lowerQuery) ||
            product.Description.ToLower().Contains(lowerQuery)
        ).ToList();
    }

    public List<Product> FilterByCategory(string category) {
        if (category == "all") return allProducts;

        return allProducts.Where(p => p.Category == category).ToList();
    }

    public List<Product> FilterByPriceRange(double min, double max) {
        return allProducts
            .Where(p => p.Price >= min && p.Price <= max)
            .ToList();
    }

    public List<Product> Filter(
        string query = null,
        string category = null,
        double? minPrice = null,
        double? maxPrice = null,
        string sortBy = null,
        string sortOrder = "asc"
    ) {
        var results = allProducts.AsEnumerable();

        if (!string.IsNullOrEmpty(query)) {
            results = results.Where(p =>
                p.Name.Contains(query, StringComparison.OrdinalIgnoreCase)
            );
        }

        if (!string.IsNullOrEmpty(category) && category != "all") {
            results = results.Where(p => p.Category == category);
        }

        if (minPrice.HasValue || maxPrice.HasValue) {
            results = results.Where(p =>
                p.Price >= (minPrice ?? 0) &&
                p.Price <= (maxPrice ?? double.MaxValue)
            );
        }

        if (!string.IsNullOrEmpty(sortBy)) {
            results = sortOrder == "asc"
                ? results.OrderBy(p => typeof(Product).GetProperty(sortBy)?.GetValue(p))
                : results.OrderByDescending(p => typeof(Product).GetProperty(sortBy)?.GetValue(p));
        }

        return results.ToList();
    }
}

// Usage
var products = new List<Product> {
    new Product { Id = 1, Name = "Laptop", Category = "Electronics", Price = 999, Description = "Gaming laptop" },
    new Product { Id = 2, Name = "Mouse", Category = "Electronics", Price = 25, Description = "Wireless mouse" },
    new Product { Id = 3, Name = "Desk", Category = "Furniture", Price = 199, Description = "Standing desk" }
};

var filter = new ProductFilter(products);

var laptops = filter.Search("laptop");
var electronics = filter.FilterByCategory("Electronics");
var affordable = filter.FilterByPriceRange(0, 100);

var results = filter.Filter(
    category: "Electronics",
    maxPrice: 500,
    sortBy: "Price",
    sortOrder: "asc"
);
```

---

## Debouncing (Rate Limiting)

**Scenario**: Limit how often a function runs (useful for search-as-you-type)

### JavaScript

```javascript
function debounce(func, delay) {
  let timeoutId;

  return function(...args) {
    // Clear previous timeout
    clearTimeout(timeoutId);

    // Set new timeout
    timeoutId = setTimeout(() => {
      func.apply(this, args);
    }, delay);
  };
}

// Usage: Search as user types (but wait for them to stop typing)
const searchInput = document.getElementById('search');

const performSearch = (query) => {
  console.log(`Searching for: ${query}`);
  // Make API call here
  fetch(`/api/search?q=${query}`)
    .then(r => r.json())
    .then(results => {
      console.log('Results:', results);
    });
};

// Debounce the search - only search 300ms after user stops typing
const debouncedSearch = debounce(performSearch, 300);

searchInput.addEventListener('input', (e) => {
  debouncedSearch(e.target.value);
});

// Without debouncing: If user types "hello", would make 5 API calls (h, he, hel, hell, hello)
// With debouncing: Makes only 1 API call after user stops typing
```

### TypeScript

```typescript
function debounce<T extends (...args: any[]) => any>(
  func: T,
  delay: number
): (...args: Parameters<T>) => void {
  let timeoutId: number | undefined;

  return function(this: any, ...args: Parameters<T>) {
    clearTimeout(timeoutId);

    timeoutId = window.setTimeout(() => {
      func.apply(this, args);
    }, delay);
  };
}

// Usage
const performSearch = (query: string): void => {
  console.log(`Searching for: ${query}`);
  fetch(`/api/search?q=${query}`)
    .then(r => r.json())
    .then(results => {
      console.log('Results:', results);
    });
};

const debouncedSearch = debounce(performSearch, 300);

const searchInput = document.getElementById('search') as HTMLInputElement;
searchInput.addEventListener('input', (e) => {
  debouncedSearch((e.target as HTMLInputElement).value);
});
```

### C# (for UI applications)

```csharp
using System.Timers;

public class Debouncer {
    private Timer timer;
    private readonly int delay;

    public Debouncer(int delayMs) {
        delay = delayMs;
    }

    public void Debounce(Action action) {
        timer?.Stop();
        timer?.Dispose();

        timer = new Timer(delay);
        timer.Elapsed += (sender, e) => {
            timer.Stop();
            action();
        };
        timer.AutoReset = false;
        timer.Start();
    }
}

// Usage
var searchDebouncer = new Debouncer(300);

void OnSearchTextChanged(string query) {
    searchDebouncer.Debounce(() => {
        Console.WriteLine($"Searching for: {query}");
        // Make API call here
        PerformSearch(query);
    });
}

async Task PerformSearch(string query) {
    using var client = new HttpClient();
    var results = await client.GetFromJsonAsync<List<SearchResult>>(
        $"/api/search?q={query}"
    );
    Console.WriteLine($"Found {results.Count} results");
}
```

---

## Shopping Cart

**Scenario**: Add/remove items, calculate totals

### JavaScript

```javascript
class ShoppingCart {
  constructor() {
    this.items = [];
  }

  addItem(product, quantity = 1) {
    const existingItem = this.items.find(item => item.product.id === product.id);

    if (existingItem) {
      existingItem.quantity += quantity;
    } else {
      this.items.push({ product, quantity });
    }

    console.log(`Added ${quantity} x ${product.name}`);
    return this.items;
  }

  removeItem(productId) {
    const index = this.items.findIndex(item => item.product.id === productId);

    if (index !== -1) {
      const removed = this.items.splice(index, 1)[0];
      console.log(`Removed ${removed.product.name}`);
    }

    return this.items;
  }

  updateQuantity(productId, quantity) {
    const item = this.items.find(item => item.product.id === productId);

    if (item) {
      item.quantity = quantity;

      if (quantity <= 0) {
        this.removeItem(productId);
      }
    }

    return this.items;
  }

  getTotal() {
    return this.items.reduce((total, item) => {
      return total + (item.product.price * item.quantity);
    }, 0);
  }

  getItemCount() {
    return this.items.reduce((count, item) => count + item.quantity, 0);
  }

  clear() {
    this.items = [];
    console.log('Cart cleared');
  }

  getCartSummary() {
    return {
      items: this.items,
      totalItems: this.getItemCount(),
      subtotal: this.getTotal(),
      tax: this.getTotal() * 0.1,  // 10% tax
      total: this.getTotal() * 1.1
    };
  }
}

// Usage
const cart = new ShoppingCart();

const laptop = { id: 1, name: 'Laptop', price: 999 };
const mouse = { id: 2, name: 'Mouse', price: 25 };

cart.addItem(laptop, 1);       // Added 1 x Laptop
cart.addItem(mouse, 2);        // Added 2 x Mouse
cart.addItem(laptop, 1);       // Added 1 x Laptop (now has 2)

console.log(cart.getTotal());  // 2048 (999*2 + 25*2)
console.log(cart.getItemCount());  // 4

const summary = cart.getCartSummary();
console.log(summary);
// {
//   items: [...],
//   totalItems: 4,
//   subtotal: 2048,
//   tax: 204.8,
//   total: 2252.8
// }
```

### C#

```csharp
public class Product {
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Price { get; set; }
}

public class CartItem {
    public Product Product { get; set; }
    public int Quantity { get; set; }
}

public class CartSummary {
    public List<CartItem> Items { get; set; }
    public int TotalItems { get; set; }
    public decimal Subtotal { get; set; }
    public decimal Tax { get; set; }
    public decimal Total { get; set; }
}

public class ShoppingCart {
    private List<CartItem> items = new();

    public List<CartItem> AddItem(Product product, int quantity = 1) {
        var existingItem = items.FirstOrDefault(i => i.Product.Id == product.Id);

        if (existingItem != null) {
            existingItem.Quantity += quantity;
        } else {
            items.Add(new CartItem { Product = product, Quantity = quantity });
        }

        Console.WriteLine($"Added {quantity} x {product.Name}");
        return items;
    }

    public List<CartItem> RemoveItem(int productId) {
        var item = items.FirstOrDefault(i => i.Product.Id == productId);

        if (item != null) {
            items.Remove(item);
            Console.WriteLine($"Removed {item.Product.Name}");
        }

        return items;
    }

    public List<CartItem> UpdateQuantity(int productId, int quantity) {
        var item = items.FirstOrDefault(i => i.Product.Id == productId);

        if (item != null) {
            item.Quantity = quantity;

            if (quantity <= 0) {
                RemoveItem(productId);
            }
        }

        return items;
    }

    public decimal GetTotal() {
        return items.Sum(item => item.Product.Price * item.Quantity);
    }

    public int GetItemCount() {
        return items.Sum(item => item.Quantity);
    }

    public void Clear() {
        items.Clear();
        Console.WriteLine("Cart cleared");
    }

    public CartSummary GetCartSummary() {
        var subtotal = GetTotal();
        var tax = subtotal * 0.1m;  // 10% tax

        return new CartSummary {
            Items = items,
            TotalItems = GetItemCount(),
            Subtotal = subtotal,
            Tax = tax,
            Total = subtotal + tax
        };
    }
}

// Usage
var cart = new ShoppingCart();

var laptop = new Product { Id = 1, Name = "Laptop", Price = 999 };
var mouse = new Product { Id = 2, Name = "Mouse", Price = 25 };

cart.AddItem(laptop, 1);
cart.AddItem(mouse, 2);
cart.AddItem(laptop, 1);

Console.WriteLine(cart.GetTotal());        // 2048
Console.WriteLine(cart.GetItemCount());    // 4

var summary = cart.GetCartSummary();
Console.WriteLine($"Total: ${summary.Total}");  // Total: $2252.80
```

---

**💡 More examples coming in v2!** (Authentication, File Upload, WebSockets, etc.)

**🏋️ Practice these by building them yourself! See [PRACTICE-EXERCISES.md](PRACTICE-EXERCISES.md)**
