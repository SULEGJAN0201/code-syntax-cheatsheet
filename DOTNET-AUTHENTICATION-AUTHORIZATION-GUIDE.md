# ASP.NET Core Authentication & Authorization Guide
## Complete Guide from Beginner to Advanced - .NET 8/9

> **Beginner-Friendly & Production-Ready**: Master authentication and authorization in ASP.NET Core with step-by-step examples, real-world scenarios, and best practices. Based on official Microsoft documentation.

---

## 📚 Table of Contents

### 🟢 Part 1: Getting Started (Beginner)
1. [ELI5: Authentication Explained Simply](#-eli5-authentication-explained-simply)
2. [Prerequisites & Setup](#-prerequisites--setup)
3. [Glossary of Terms](#-glossary-of-terms)
4. [Quick Start: Working JWT in 10 Minutes](#-quick-start-working-jwt-in-10-minutes)
5. [Common Questions (FAQ)](#-common-questions-faq)

### 🟡 Part 2: Authentication (Intermediate)
6. [Authentication vs Authorization](#-authentication-vs-authorization)
7. [How Authentication Works in ASP.NET Core](#-how-authentication-works-in-aspnet-core)
8. [JWT Bearer Authentication](#-jwt-bearer-authentication)
9. [Cookie Authentication](#-cookie-authentication)
10. [ASP.NET Core Identity](#-aspnet-core-identity)
11. [OAuth 2.0 & External Providers](#-oauth-20--external-providers)

### 🟡 Part 3: Authorization (Intermediate)
12. [Authorization Fundamentals](#-authorization-fundamentals)
13. [Role-Based Authorization (RBAC)](#-role-based-authorization-rbac)
14. [Claims-Based Authorization](#-claims-based-authorization)
15. [Policy-Based Authorization](#-policy-based-authorization)
16. [Resource-Based Authorization](#-resource-based-authorization)

### 🔴 Part 4: Production & Best Practices (Advanced)
17. [API Security Best Practices](#-api-security-best-practices)
18. [Common Security Pitfalls](#-common-security-pitfalls)
19. [Multi-Tenant Authorization](#-multi-tenant-authorization)
20. [Real-World Scenarios](#-real-world-scenarios)
21. [Troubleshooting Guide](#-troubleshooting-guide)

### 📖 Appendix
22. [Decision Trees](#-decision-trees)
23. [Testing Strategies](#-testing-strategies)
24. [Deployment Checklist](#-deployment-checklist)

---

# 🟢 Part 1: Getting Started

## 🎯 ELI5: Authentication Explained Simply

### The Concert Analogy

Imagine you're going to a concert:

**🎫 Authentication = Showing Your Ticket at the Door**
- Question: "Who are you?"
- Action: You show your ticket (or ID)
- Result: Security confirms you're a real ticket holder
- In code: Username + Password → JWT Token

**🚪 Authorization = Getting Access to Different Areas**
- Question: "What are you allowed to do?"
- Action: Security checks your ticket type
- Results:
  - General Admission → Standing area only
  - VIP Pass → Backstage + lounge access
  - Staff Badge → Everywhere + control room
- In code: Roles, Claims, Policies → Allow/Deny access

### Real-World Example

```
You arrive at the concert venue:

Step 1: AUTHENTICATION (at entrance)
👤 You: "Here's my ticket"
🛡️ Security: *scans ticket* "Valid! You're John Smith"
✅ You're IN (authenticated)

Step 2: AUTHORIZATION (throughout venue)
👤 You try to enter VIP lounge
🛡️ Security: *checks ticket type* "General admission - ACCESS DENIED"
❌ You can't enter (not authorized)

👤 You try to enter standing area
🛡️ Security: *checks ticket type* "General admission - ACCESS GRANTED"
✅ You can enter (authorized)
```

### In Code

```csharp
// AUTHENTICATION: Prove who you are
POST /api/auth/login
{
  "email": "john@example.com",
  "password": "SecurePass123!"
}
// ✅ Returns: JWT token (your "ticket")

// AUTHORIZATION: Access protected resources
GET /api/products          // ✅ Anyone can access
GET /api/admin/users       // ❌ Admins only (you get 403 Forbidden)
GET /api/my/orders         // ✅ Authenticated users only
```

---

## 📋 Prerequisites & Setup

### What You Need

**Software (Required)**
- [ ] .NET 8 SDK or .NET 9 SDK ([Download](https://dot.net))
- [ ] Visual Studio 2022 or VS Code ([Download](https://code.visualstudio.com))
- [ ] Postman or similar API testing tool ([Download](https://www.postman.com))

**Knowledge (Minimum)**
- [ ] Basic C# (classes, methods, variables)
- [ ] HTTP basics (GET, POST, headers)
- [ ] JSON format
- [ ] Command line basics

**Optional but Helpful**
- [ ] SQL Server or PostgreSQL (for ASP.NET Core Identity)
- [ ] Understanding of async/await
- [ ] Basic Entity Framework Core knowledge

### Verify Your Setup

```bash
# Check .NET version (should be 8.0 or 9.0)
dotnet --version

# Create test project
dotnet new webapi -n AuthTest
cd AuthTest

# Run it
dotnet run

# You should see: "Now listening on: http://localhost:5000"
```

### Learning Path

```
Total time: ~2-3 hours (can be split across multiple sessions)

🟢 Beginner Section (30 minutes)
├─ Read ELI5 explanation (5 min)
├─ Complete Quick Start (10 min)
├─ Read FAQ (10 min)
└─ Review Glossary (5 min)

🟡 Intermediate Section (90 minutes)
├─ JWT Authentication (30 min)
├─ Cookie Authentication (15 min)
├─ ASP.NET Core Identity (30 min)
└─ Authorization basics (15 min)

🔴 Advanced Section (60 minutes)
├─ Security best practices (20 min)
├─ Real-world scenarios (20 min)
└─ Troubleshooting (20 min)
```

---

## 📖 Glossary of Terms

### Authentication Terms

**Authentication**
- **What**: Proving who you are
- **Example**: Logging in with email + password
- **Result**: You get a token or cookie

**JWT (JSON Web Token)**
- **What**: A signed token containing user information
- **Format**: Three parts separated by dots: `header.payload.signature`
- **Example**: `eyJhbGci.eyJzdWIi.SflKxwRJ`
- **Size**: ~200-500 characters

**Bearer Token**
- **What**: A type of access token
- **How to send**: `Authorization: Bearer <your-token>`
- **Example**: `Authorization: Bearer eyJhbGci...`

**Refresh Token**
- **What**: Long-lived token to get new access tokens
- **Why needed**: Access tokens expire quickly (15-60 min)
- **Flow**: Access token expires → Use refresh token → Get new access token

**Claims**
- **What**: Key-value pairs about the user
- **Example**: `{ "email": "user@example.com", "role": "admin", "userId": "123" }`
- **Usage**: Build the user's identity

**ClaimsPrincipal**
- **What**: The current authenticated user in ASP.NET Core
- **Access via**: `HttpContext.User` or `User` in controllers
- **Contains**: All claims about the user

### Authorization Terms

**Authorization**
- **What**: Checking what you can access
- **Example**: Checking if user is an admin before allowing delete
- **Result**: 200 OK or 403 Forbidden

**Role**
- **What**: User category/group
- **Examples**: Admin, User, Manager, Guest
- **Usage**: `[Authorize(Roles = "Admin")]`

**Policy**
- **What**: Custom authorization rule
- **Example**: "Must be over 18 AND have verified email"
- **Usage**: `[Authorize(Policy = "Over18")]`

**RBAC (Role-Based Access Control)**
- **What**: Authorization based on user roles
- **Example**: Admins can delete, Users can only view

**PBAC (Policy-Based Access Control)**
- **What**: Authorization based on custom rules
- **Example**: Can edit if (is owner OR is admin) AND (document not locked)

### HTTP Status Codes

```
200 OK           - Success
201 Created      - Resource created successfully
401 Unauthorized - Not authenticated (no token or invalid token)
403 Forbidden    - Authenticated but not authorized (insufficient permissions)
404 Not Found    - Resource doesn't exist
500 Server Error - Something went wrong on server
```

### Common Acronyms

```
JWT   = JSON Web Token
RBAC  = Role-Based Access Control
PBAC  = Policy-Based Access Control
CORS  = Cross-Origin Resource Sharing
XSS   = Cross-Site Scripting
CSRF  = Cross-Site Request Forgery
OIDC  = OpenID Connect (built on OAuth 2.0)
2FA   = Two-Factor Authentication
MFA   = Multi-Factor Authentication
```

---

## 🚀 Quick Start: Working JWT in 10 Minutes

### Goal
Build a working JWT authentication API from scratch in 10 minutes.

### Step 1: Create Project (1 minute)

```bash
# Create new Web API project
dotnet new webapi -n MyAuthApi
cd MyAuthApi

# Install JWT package
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
```

### Step 2: Add JWT Configuration (2 minutes)

**Open `Program.cs` and replace ALL content with:**

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.IdentityModel.Tokens;
using System.IdentityModel.Tokens.Jwt;
using System.Security.Claims;
using System.Text;

var builder = WebApplication.CreateBuilder(args);

// Add JWT Authentication
var secretKey = "MySecretKey32CharactersLongForDemo!"; // TODO: Move to config
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuerSigningKey = true,
            IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(secretKey)),
            ValidateIssuer = false,  // Simplified for demo
            ValidateAudience = false // Simplified for demo
        };
    });

builder.Services.AddAuthorization();

var app = builder.Build();

// ⚠️ ORDER MATTERS!
app.UseAuthentication();  // Must come BEFORE UseAuthorization
app.UseAuthorization();

// === ENDPOINTS ===

// 1. PUBLIC endpoint - anyone can access
app.MapGet("/public", () => "✅ This is public - anyone can see this!")
    .WithName("PublicEndpoint");

// 2. PROTECTED endpoint - requires valid JWT token
app.MapGet("/protected", (HttpContext context) =>
{
    var userEmail = context.User.FindFirst(ClaimTypes.Email)?.Value;
    return $"✅ This is protected - Hello {userEmail}! You are authenticated.";
})
.RequireAuthorization()
.WithName("ProtectedEndpoint");

// 3. LOGIN endpoint - generates JWT token
app.MapPost("/login", (LoginRequest request) =>
{
    // 🚨 DEMO ONLY - In production, validate against database!
    if (request.Email == "admin@test.com" && request.Password == "Admin123!")
    {
        // Create claims (user information)
        var claims = new[]
        {
            new Claim(ClaimTypes.Email, request.Email),
            new Claim(ClaimTypes.Role, "Admin"),
            new Claim("userId", "123")
        };

        // Create signing key
        var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(secretKey));
        var creds = new SigningCredentials(key, SecurityAlgorithms.HmacSha256);

        // Create token
        var token = new JwtSecurityToken(
            claims: claims,
            expires: DateTime.UtcNow.AddMinutes(30),
            signingCredentials: creds
        );

        var jwt = new JwtSecurityTokenHandler().WriteToken(token);

        return Results.Ok(new { token = jwt });
    }

    return Results.Unauthorized();
})
.WithName("Login");

app.Run();

// Request model
record LoginRequest(string Email, string Password);
```

### Step 3: Run the API (1 minute)

```bash
dotnet run
```

You should see:
```
info: Now listening on: http://localhost:5000
```

### Step 4: Test with cURL (6 minutes)

**Test 1: Public endpoint (anyone can access)**

```bash
curl http://localhost:5000/public
```

✅ **Result**: `This is public - anyone can see this!`

---

**Test 2: Protected endpoint without token (fails)**

```bash
curl http://localhost:5000/protected
```

❌ **Result**: `401 Unauthorized` (no token provided)

---

**Test 3: Login to get token**

```bash
curl -X POST http://localhost:5000/login \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@test.com","password":"Admin123!"}'
```

✅ **Result**:
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJodHRwOi8v..."
}
```

**Copy the token value** (everything inside the quotes after "token":)

---

**Test 4: Access protected endpoint WITH token**

```bash
# Replace YOUR_TOKEN_HERE with the token from step 3
curl http://localhost:5000/protected \
  -H "Authorization: Bearer YOUR_TOKEN_HERE"
```

✅ **Result**: `This is protected - Hello admin@test.com! You are authenticated.`

---

### Step 5: Test with Postman (Alternative)

**1. GET /public**
- Method: GET
- URL: `http://localhost:5000/public`
- Click Send
- ✅ Should work

**2. POST /login**
- Method: POST
- URL: `http://localhost:5000/login`
- Headers: `Content-Type: application/json`
- Body (raw JSON):
  ```json
  {
    "email": "admin@test.com",
    "password": "Admin123!"
  }
  ```
- Click Send
- ✅ Copy the token from response

**3. GET /protected**
- Method: GET
- URL: `http://localhost:5000/protected`
- Headers: `Authorization: Bearer <paste-token-here>`
- Click Send
- ✅ Should work with token

---

### ✅ Checkpoint

You now have:
- ✅ Working JWT authentication
- ✅ Public endpoint (no auth needed)
- ✅ Protected endpoint (auth required)
- ✅ Login endpoint (generates tokens)

**What's Next?**
- This is a simplified demo (hardcoded credentials)
- In production, you'll validate against a database
- Continue reading to learn proper implementation

---

## ❓ Common Questions (FAQ)

### Q1: JWT vs Cookie - Which should I use?

**Short answer:**
```
Building an API for mobile/SPA? → Use JWT
Building a traditional web app? → Use Cookies
```

**Detailed comparison:**

| Scenario | Best Choice | Why |
|----------|-------------|-----|
| React/Vue/Angular frontend | JWT | Stateless, works cross-domain |
| Mobile app (iOS/Android) | JWT | Mobile has no cookie support |
| Traditional MVC/Razor app | Cookie | Simpler, more secure by default |
| Microservices | JWT | Stateless, shared across services |
| Internal admin panel | Cookie | Easier to implement |
| Public API | JWT | Standard for APIs |

---

### Q2: Where should I store JWT tokens?

**Options ranked by security:**

```
🔴 localStorage (Least secure)
└─ Vulnerable to XSS attacks
└─ JavaScript can steal token
└─ Use only for learning/demos

🟡 sessionStorage (Better)
└─ Cleared when tab closes
└─ Still vulnerable to XSS
└─ Slightly better than localStorage

🟢 httpOnly Cookie (Most secure)
└─ JavaScript cannot access
└─ Sent automatically by browser
└─ Immune to XSS (but needs CSRF protection)
```

**Production recommendation:**

```csharp
// ✅ BEST: Store JWT in httpOnly cookie
Response.Cookies.Append("token", jwt, new CookieOptions
{
    HttpOnly = true,      // JavaScript can't access
    Secure = true,        // HTTPS only
    SameSite = SameSiteMode.Strict, // CSRF protection
    Expires = DateTimeOffset.UtcNow.AddHours(1)
});
```

---

### Q3: How long should tokens last?

**Recommended expiry times:**

```
Access Token (JWT):
├─ High security apps: 5-15 minutes
├─ Normal apps: 15-60 minutes
└─ Low security apps: 1-24 hours

Refresh Token:
├─ Web apps: 7-30 days
├─ Mobile apps: 30-90 days
└─ Trusted devices: 90-365 days
```

**Why short-lived access tokens?**
- If stolen, attacker has limited time window
- Forces re-validation more frequently
- Refresh tokens can be revoked

---

### Q4: What happens when token expires?

**Flow:**

```
1. Client makes request with expired token
   ↓
2. Server returns 401 Unauthorized
   ↓
3. Client detects 401
   ↓
4. Client sends refresh token to /refresh endpoint
   ↓
5. Server validates refresh token
   ↓
6. Server issues new access token + refresh token
   ↓
7. Client retries original request with new token
   ↓
8. Success!
```

**JavaScript example:**

```javascript
async function apiCall(url) {
    let response = await fetch(url, {
        headers: { 'Authorization': `Bearer ${accessToken}` }
    });

    // Token expired?
    if (response.status === 401) {
        // Refresh token
        const refreshResponse = await fetch('/api/auth/refresh', {
            method: 'POST',
            body: JSON.stringify({ refreshToken })
        });

        const { newAccessToken } = await refreshResponse.json();
        accessToken = newAccessToken;

        // Retry original request
        response = await fetch(url, {
            headers: { 'Authorization': `Bearer ${accessToken}` }
        });
    }

    return response.json();
}
```

---

### Q5: Can I have multiple roles per user?

**Yes!** This is common:

```csharp
// User can have multiple roles
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Email, "john@example.com"),
    new Claim(ClaimTypes.Role, "User"),    // Role 1
    new Claim(ClaimTypes.Role, "Manager"), // Role 2
    new Claim(ClaimTypes.Role, "Editor")   // Role 3
};

// Check for ANY role
[Authorize(Roles = "Admin,Manager")] // User OR Manager can access

// Check for ALL roles (use policy)
[Authorize(Policy = "AdminAndManager")] // Must have BOTH
```

---

### Q6: 401 Unauthorized vs 403 Forbidden - What's the difference?

```
401 Unauthorized:
├─ Meaning: "Who are you? I don't know you."
├─ Reason: No token, invalid token, or expired token
├─ Fix: Login again / provide valid token
└─ Example: Accessing /api/products without token

403 Forbidden:
├─ Meaning: "I know who you are, but you can't do this."
├─ Reason: Authenticated but insufficient permissions
├─ Fix: Contact admin to grant permissions
└─ Example: Regular user trying to access /api/admin
```

**Visual:**

```
Request to /api/admin/users
    ↓
No token? → 401 (authenticate first)
    ↓
Has token but not admin? → 403 (forbidden)
    ↓
Has token AND is admin? → 200 (success)
```

---

### Q7: Should I validate JWT on every request?

**Yes!** This is automatic in ASP.NET Core:

```
Every request with JWT:
    ↓
1. Authentication middleware extracts token
    ↓
2. Validates signature (not tampered)
    ↓
3. Checks expiry (not expired)
    ↓
4. Creates ClaimsPrincipal
    ↓
5. Sets HttpContext.User
    ↓
6. Authorization middleware checks permissions
    ↓
7. Controller action executes (if authorized)
```

**Performance impact**: Minimal (microseconds)
- Signature validation is fast
- No database hit needed (stateless)
- Claims are cached in ClaimsPrincipal

---

### Q8: Can JWT be hacked?

**JWT Security Facts:**

```
✅ JWT is SIGNED (tamper-proof)
└─ Attacker can't modify claims without detection

❌ JWT is NOT ENCRYPTED (readable by anyone)
└─ Anyone can decode and read the payload
└─ Never store passwords or sensitive data in JWT!

⚠️ JWT can be STOLEN
└─ If attacker gets your token, they can impersonate you
└─ Until token expires
└─ Store securely (httpOnly cookies)
```

**What can go wrong:**

```
1. Weak secret key → Attacker can forge tokens
   Fix: Use strong, random, 32+ character key

2. Token stored in localStorage → XSS attack steals it
   Fix: Use httpOnly cookies

3. No HTTPS → Man-in-the-middle intercepts token
   Fix: Always use HTTPS in production

4. Long expiry time → Stolen token valid for days
   Fix: Short-lived access tokens (15-60 min)
```

---

### Q9: Do I need a database for JWT?

**Short answer**: No (stateless), but recommended for production.

**Without database:**
- ✅ Stateless - no server-side session storage
- ✅ Scales easily - no shared session store needed
- ❌ Can't revoke tokens (must wait for expiry)
- ❌ Can't invalidate on logout
- ❌ Can't detect compromised tokens

**With database (recommended):**
- ✅ Can revoke tokens immediately
- ✅ Can track active sessions
- ✅ Can force logout
- ✅ Can detect unusual activity
- ⚠️ Requires database check (small overhead)

**Hybrid approach (best):**
- Use JWT for authentication (no DB check)
- Store refresh tokens in DB (can revoke)
- Short-lived access tokens (15-30 min)
- Long-lived refresh tokens (7-30 days in DB)

---

### Q10: How do I test protected endpoints?

**Option 1: cURL**

```bash
# 1. Login first
TOKEN=$(curl -s -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@test.com","password":"Admin123!"}' \
  | grep -o '"token":"[^"]*' | cut -d'"' -f4)

# 2. Use token in protected endpoint
curl http://localhost:5000/api/products \
  -H "Authorization: Bearer $TOKEN"
```

**Option 2: Postman**

```
1. Create environment variable "authToken"
2. In login request, add test script:
   pm.environment.set("authToken", pm.response.json().token);
3. In other requests, use:
   Authorization: Bearer {{authToken}}
```

**Option 3: Swagger (built-in UI)**

```csharp
// In Program.cs
builder.Services.AddSwaggerGen(c =>
{
    c.AddSecurityDefinition("Bearer", new OpenApiSecurityScheme
    {
        Description = "JWT Authorization header using the Bearer scheme.",
        Name = "Authorization",
        In = ParameterLocation.Header,
        Type = SecuritySchemeType.Http,
        Scheme = "bearer"
    });

    c.AddSecurityRequirement(new OpenApiSecurityRequirement
    {
        {
            new OpenApiSecurityScheme
            {
                Reference = new OpenApiReference
                {
                    Type = ReferenceType.SecurityScheme,
                    Id = "Bearer"
                }
            },
            Array.Empty<string>()
        }
    });
});

// Then navigate to: http://localhost:5000/swagger
// Click "Authorize" button, paste token
```

---

# 🟡 Part 2: Authentication (Intermediate)

## 🔐 Authentication vs Authorization

### The Complete Picture

```csharp
// AUTHENTICATION: Proving identity
// Question: "Who are you?"
// Process: Validate credentials → Create identity
// Result: ClaimsPrincipal with user information
// HTTP Status: 401 if fails

// AUTHORIZATION: Checking permissions
// Question: "What can you access?"
// Process: Check roles/claims/policies
// Result: Allow or Deny access
// HTTP Status: 403 if denied
```

### Side-by-Side Comparison

| Aspect | Authentication | Authorization |
|--------|----------------|---------------|
| **Question** | Who are you? | What can you do? |
| **Input** | Credentials (email/password) | User identity + resource |
| **Output** | Identity (ClaimsPrincipal) | Decision (Allow/Deny) |
| **When** | Once (at login) | Every request |
| **HTTP Status** | 401 Unauthorized | 403 Forbidden |
| **Attribute** | None | `[Authorize]` |
| **Examples** | Login, JWT, OAuth, Cookies | Roles, Claims, Policies |

### Request Pipeline

```
HTTP Request arrives
    ↓
┌─────────────────────────────────────────┐
│ 1. Authentication Middleware           │
│    - Reads token/cookie                │
│    - Validates signature & expiry      │
│    - Creates ClaimsPrincipal           │
│    - Sets HttpContext.User             │
└────────────┬────────────────────────────┘
             ↓
         Authenticated?
         ├─ No → 401 Unauthorized (stops here)
         └─ Yes → Continue...
             ↓
┌─────────────────────────────────────────┐
│ 2. Authorization Middleware            │
│    - Checks [Authorize] attributes     │
│    - Evaluates roles/claims/policies   │
│    - Decides: Allow or Deny            │
└────────────┬────────────────────────────┘
             ↓
         Authorized?
         ├─ No → 403 Forbidden (stops here)
         └─ Yes → Continue...
             ↓
┌─────────────────────────────────────────┐
│ 3. Controller Action Executes          │
│    - Your code runs                    │
│    - Can access User.Claims            │
└─────────────────────────────────────────┘
             ↓
        HTTP Response
```

### Code Example

```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    // NO [Authorize] - anyone can access
    [HttpGet("public")]
    public IActionResult GetPublicProducts()
    {
        return Ok("Public products - no auth needed");
    }

    // [Authorize] - must be authenticated
    [HttpGet]
    [Authorize]
    public IActionResult GetAll()
    {
        // AUTHENTICATION happened before this runs
        // HttpContext.User is set

        var userId = User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
        return Ok($"Hello user {userId}");
    }

    // [Authorize] with role - must be authenticated AND have role
    [HttpDelete("{id}")]
    [Authorize(Roles = "Admin")]
    public IActionResult Delete(int id)
    {
        // AUTHENTICATION: User is logged in ✓
        // AUTHORIZATION: User has "Admin" role ✓

        return Ok($"Deleted product {id}");
    }

    // [Authorize] with policy - custom authorization logic
    [HttpPost]
    [Authorize(Policy = "CanCreateProducts")]
    public IActionResult Create([FromBody] Product product)
    {
        // AUTHENTICATION: User is logged in ✓
        // AUTHORIZATION: User meets policy requirements ✓

        return Created($"/api/products/{product.Id}", product);
    }
}
```

---

## 🔧 How Authentication Works in ASP.NET Core

### Authentication Schemes

An **authentication scheme** is a named configuration that defines how to authenticate users.

```csharp
// Program.cs

builder.Services.AddAuthentication(options =>
{
    // Which scheme to use by default
    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
})
.AddJwtBearer(options =>
{
    // JWT-specific configuration
})
.AddCookie("CookieScheme", options =>
{
    // Cookie-specific configuration
})
.AddGoogle(options =>
{
    // Google OAuth configuration
});
```

**Common schemes:**
- `JwtBearer` - For APIs (stateless)
- `Cookies` - For web apps (stateful)
- `Google`, `Microsoft`, `Facebook` - External providers (OAuth)

### The ClaimsPrincipal

Every authenticated user is represented as a `ClaimsPrincipal`.

```
ClaimsPrincipal (User)
    │
    ├── ClaimsIdentity (JWT)
    │   ├── Claim: NameIdentifier = "123"
    │   ├── Claim: Email = "user@example.com"
    │   ├── Claim: Role = "Admin"
    │   └── Claim: Role = "Manager"
    │
    └── ClaimsIdentity (Cookie) [optional - can have multiple]
        ├── Claim: Name = "John Doe"
        └── Claim: Department = "IT"
```

**Accessing claims in code:**

```csharp
[ApiController]
[Route("api/[controller]")]
public class UserController : ControllerBase
{
    [HttpGet("me")]
    [Authorize]
    public IActionResult GetCurrentUser()
    {
        // The entire authenticated user
        ClaimsPrincipal user = HttpContext.User;
        // OR simply: User (available in controllers)

        // Check if authenticated
        if (!User.Identity.IsAuthenticated)
        {
            return Unauthorized();
        }

        // Get specific claim (first match)
        var userId = User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
        var email = User.FindFirst(ClaimTypes.Email)?.Value;
        var name = User.FindFirst(ClaimTypes.Name)?.Value;

        // Get all values for a claim type (useful for roles)
        var roles = User.FindAll(ClaimTypes.Role)
            .Select(c => c.Value)
            .ToList();

        // Check if user has specific claim
        bool isAdmin = User.HasClaim(ClaimTypes.Role, "Admin");

        // Check if user is in role (shorthand)
        bool isManager = User.IsInRole("Manager");

        // Get authentication type
        var authType = User.Identity.AuthenticationType; // "Bearer" or "Cookie"

        return Ok(new
        {
            UserId = userId,
            Email = email,
            Name = name,
            Roles = roles,
            IsAdmin = isAdmin,
            AuthenticationType = authType,
            AllClaims = User.Claims.Select(c => new
            {
                Type = c.Type,
                Value = c.Value
            })
        });
    }
}
```

### Standard Claim Types

```csharp
using System.Security.Claims;

// Common claim types from ClaimTypes class
ClaimTypes.NameIdentifier     // "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier"
ClaimTypes.Name               // "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
ClaimTypes.Email              // "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"
ClaimTypes.Role               // "http://schemas.microsoft.com/ws/2008/06/identity/claims/role"
ClaimTypes.DateOfBirth        // "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/dateofbirth"
ClaimTypes.Country            // "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country"

// JWT registered claim names (shorter, preferred for JWT)
JwtRegisteredClaimNames.Sub   // "sub" (subject/user ID)
JwtRegisteredClaimNames.Email // "email"
JwtRegisteredClaimNames.Jti   // "jti" (JWT ID)
JwtRegisteredClaimNames.Iat   // "iat" (issued at)
JwtRegisteredClaimNames.Exp   // "exp" (expiration)

// Custom claims (use your own names)
new Claim("userId", "123")
new Claim("tenantId", "456")
new Claim("subscriptionLevel", "premium")
```

---

## 🔑 JWT Bearer Authentication

### 🟢 Beginner: What is JWT?

**JWT = JSON Web Token**

A JWT is a secure way to pass user information between client and server.

**Structure:** Three parts separated by dots

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9  ← HEADER (algorithm)
.
eyJzdWIiOiIxMjM0NSIsImVtYWlsIjoi...   ← PAYLOAD (user data)
.
SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_...  ← SIGNATURE (verification)
```

**Decoded JWT:**

```json
// HEADER (base64 decoded)
{
  "alg": "HS256",
  "typ": "JWT"
}

// PAYLOAD (base64 decoded) - THIS IS READABLE!
{
  "sub": "12345",
  "email": "user@example.com",
  "role": "Admin",
  "exp": 1735689600
}

// SIGNATURE (verifies token wasn't tampered with)
// HMACSHA256(
//   base64UrlEncode(header) + "." + base64UrlEncode(payload),
//   secret-key
// )
```

**⚠️ Important:** JWT payload is NOT encrypted, only signed!
- Anyone can read the payload (it's just base64)
- But only server can create/verify signature
- Never put passwords in JWT!

### How JWT Flow Works

```
┌──────────┐                           ┌──────────┐
│  Client  │                           │  Server  │
└────┬─────┘                           └────┬─────┘
     │                                      │
     │  1. POST /login                      │
     │     { email, password }              │
     │─────────────────────────────────────>│
     │                                      │
     │                    2. Validate user  │
     │                    3. Create JWT  ←──┤
     │                                      │
     │  4. Return JWT                       │
     │<─────────────────────────────────────│
     │  { "token": "eyJhbG..." }            │
     │                                      │
     │  5. Store token (localStorage)       │
     │                                      │
     │  6. GET /api/products                │
     │     Header: Authorization: Bearer... │
     │─────────────────────────────────────>│
     │                                      │
     │           7. Validate JWT signature  │
     │           8. Check expiry         ←──┤
     │           9. Extract claims       ←──┤
     │                                      │
     │  10. Return products                 │
     │<─────────────────────────────────────│
     │  [ { id: 1, name: "Product" } ]      │
     │                                      │
```

### 🟡 Intermediate: Complete JWT Implementation

I'll continue with the complete JWT implementation, Cookie Authentication, ASP.NET Core Identity, and all remaining sections. This is getting comprehensive!

**Should I continue adding:**
- Complete JWT implementation with refresh tokens
- Cookie authentication
- ASP.NET Core Identity setup
- OAuth 2.0 & external providers
- All authorization types (RBAC, Claims, Policies, Resource-based)
- Security best practices
- Real-world scenarios
- Troubleshooting guide

**The file is getting large. Would you like me to:**
1. **Continue in the same file** (complete guide, ~3000+ lines)
2. **Split into multiple files** (Part 1: Auth, Part 2: Authorization, Part 3: Advanced)

Which approach do you prefer?
