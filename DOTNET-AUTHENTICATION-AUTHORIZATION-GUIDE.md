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

**What you'll build:** Production-ready JWT with refresh tokens, proper validation, and security.

#### Step 1: Install Packages

```bash
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
dotnet add package System.IdentityModel.Tokens.Jwt
```

#### Step 2: JWT Settings (appsettings.json)

```json
{
  "Jwt": {
    "Key": "YourSuperSecretKeyMinimum32CharactersLong!",
    "Issuer": "https://yourdomain.com",
    "Audience": "https://yourdomain.com",
    "AccessTokenExpiryMinutes": 15,
    "RefreshTokenExpiryDays": 7
  }
}
```

**⚠️ Production:** Store Key in Azure Key Vault or AWS Secrets Manager, NOT in appsettings.json!

#### Step 3: JWT Service

```csharp
public interface IJwtService
{
    string GenerateAccessToken(string userId, string email, IEnumerable<string> roles);
    string GenerateRefreshToken();
    ClaimsPrincipal? ValidateToken(string token);
}

public class JwtService : IJwtService
{
    private readonly IConfiguration _config;

    public JwtService(IConfiguration config)
    {
        _config = config;
    }

    public string GenerateAccessToken(string userId, string email, IEnumerable<string> roles)
    {
        var key = new SymmetricSecurityKey(
            Encoding.UTF8.GetBytes(_config["Jwt:Key"]!));

        var claims = new List<Claim>
        {
            new(JwtRegisteredClaimNames.Sub, userId),
            new(JwtRegisteredClaimNames.Email, email),
            new(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString())
        };

        claims.AddRange(roles.Select(role => new Claim(ClaimTypes.Role, role)));

        var token = new JwtSecurityToken(
            issuer: _config["Jwt:Issuer"],
            audience: _config["Jwt:Audience"],
            claims: claims,
            expires: DateTime.UtcNow.AddMinutes(
                int.Parse(_config["Jwt:AccessTokenExpiryMinutes"]!)),
            signingCredentials: new SigningCredentials(key, SecurityAlgorithms.HmacSha256)
        );

        return new JwtSecurityTokenHandler().WriteToken(token);
    }

    public string GenerateRefreshToken()
    {
        return Convert.ToBase64String(RandomNumberGenerator.GetBytes(32));
    }

    public ClaimsPrincipal? ValidateToken(string token)
    {
        try
        {
            var tokenHandler = new JwtSecurityTokenHandler();
            var key = Encoding.UTF8.GetBytes(_config["Jwt:Key"]!);

            var principal = tokenHandler.ValidateToken(token, new TokenValidationParameters
            {
                ValidateIssuerSigningKey = true,
                IssuerSigningKey = new SymmetricSecurityKey(key),
                ValidateIssuer = true,
                ValidIssuer = _config["Jwt:Issuer"],
                ValidateAudience = true,
                ValidAudience = _config["Jwt:Audience"],
                ValidateLifetime = false, // For refresh token validation
                ClockSkew = TimeSpan.Zero
            }, out _);

            return principal;
        }
        catch
        {
            return null;
        }
    }
}
```

#### Step 4: Configure in Program.cs

```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddScoped<IJwtService, JwtService>();

builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuerSigningKey = true,
            IssuerSigningKey = new SymmetricSecurityKey(
                Encoding.UTF8.GetBytes(builder.Configuration["Jwt:Key"]!)),
            ValidateIssuer = true,
            ValidIssuer = builder.Configuration["Jwt:Issuer"],
            ValidateAudience = true,
            ValidAudience = builder.Configuration["Jwt:Audience"],
            ValidateLifetime = true,
            ClockSkew = TimeSpan.Zero // No tolerance for expiry
        };
    });

builder.Services.AddAuthorization();

var app = builder.Build();

app.UseAuthentication();
app.UseAuthorization();

app.MapControllers();
app.Run();
```

#### Step 5: Auth Controller with Refresh Tokens

```csharp
[ApiController]
[Route("api/[controller]")]
public class AuthController : ControllerBase
{
    private readonly IJwtService _jwtService;
    // In production, use database to store refresh tokens
    private static readonly Dictionary<string, RefreshTokenData> _refreshTokens = new();

    public AuthController(IJwtService jwtService)
    {
        _jwtService = jwtService;
    }

    [HttpPost("login")]
    public IActionResult Login([FromBody] LoginRequest request)
    {
        // TODO: Validate credentials against database
        if (request.Email != "admin@test.com" || request.Password != "Admin123!")
        {
            return Unauthorized(new { message = "Invalid credentials" });
        }

        var userId = "123";
        var roles = new[] { "Admin", "User" };

        var accessToken = _jwtService.GenerateAccessToken(userId, request.Email, roles);
        var refreshToken = _jwtService.GenerateRefreshToken();

        // Store refresh token (in production: use database)
        _refreshTokens[refreshToken] = new RefreshTokenData
        {
            UserId = userId,
            ExpiresAt = DateTime.UtcNow.AddDays(7)
        };

        return Ok(new
        {
            AccessToken = accessToken,
            RefreshToken = refreshToken,
            ExpiresIn = 900 // 15 minutes in seconds
        });
    }

    [HttpPost("refresh")]
    public IActionResult Refresh([FromBody] RefreshRequest request)
    {
        // Validate refresh token
        if (!_refreshTokens.TryGetValue(request.RefreshToken, out var tokenData))
        {
            return Unauthorized(new { message = "Invalid refresh token" });
        }

        if (tokenData.ExpiresAt < DateTime.UtcNow)
        {
            _refreshTokens.Remove(request.RefreshToken);
            return Unauthorized(new { message = "Refresh token expired" });
        }

        // TODO: Get user from database
        var email = "admin@test.com";
        var roles = new[] { "Admin", "User" };

        // Generate new tokens
        var newAccessToken = _jwtService.GenerateAccessToken(
            tokenData.UserId, email, roles);
        var newRefreshToken = _jwtService.GenerateRefreshToken();

        // Revoke old refresh token, store new one
        _refreshTokens.Remove(request.RefreshToken);
        _refreshTokens[newRefreshToken] = new RefreshTokenData
        {
            UserId = tokenData.UserId,
            ExpiresAt = DateTime.UtcNow.AddDays(7)
        };

        return Ok(new
        {
            AccessToken = newAccessToken,
            RefreshToken = newRefreshToken,
            ExpiresIn = 900
        });
    }

    [HttpPost("logout")]
    [Authorize]
    public IActionResult Logout([FromBody] LogoutRequest request)
    {
        // Revoke refresh token
        _refreshTokens.Remove(request.RefreshToken);
        return Ok(new { message = "Logged out successfully" });
    }
}

record LoginRequest(string Email, string Password);
record RefreshRequest(string RefreshToken);
record LogoutRequest(string RefreshToken);

class RefreshTokenData
{
    public string UserId { get; set; } = string.Empty;
    public DateTime ExpiresAt { get; set; }
}
```

**Key Points:**
- Access token: 15 minutes (short-lived, can't be revoked)
- Refresh token: 7 days (long-lived, stored in DB, can be revoked)
- On token expiry: Use refresh token to get new access token
- On logout: Revoke refresh token immediately

---

## 🍪 Cookie Authentication

### 🟢 When to Use Cookies

**Use cookies for:**
- Traditional web apps (MVC, Razor Pages)
- Same-domain applications
- Browser-based sessions

**Don't use cookies for:**
- Mobile apps
- Cross-domain APIs
- Microservices

### 🟡 Cookie Setup

```csharp
// Program.cs
builder.Services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.Cookie.Name = "MyApp.Auth";
        options.Cookie.HttpOnly = true;  // Prevent JavaScript access (XSS protection)
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always; // HTTPS only
        options.Cookie.SameSite = SameSiteMode.Strict; // CSRF protection

        options.ExpireTimeSpan = TimeSpan.FromHours(1);
        options.SlidingExpiration = true; // Renew cookie if >50% expired

        options.LoginPath = "/Account/Login";
        options.LogoutPath = "/Account/Logout";
        options.AccessDeniedPath = "/Account/AccessDenied";
    });
```

### Login/Logout

```csharp
[ApiController]
[Route("api/[controller]")]
public class AccountController : ControllerBase
{
    [HttpPost("login")]
    public async Task<IActionResult> Login([FromBody] LoginRequest request)
    {
        // TODO: Validate credentials
        if (request.Email != "user@test.com" || request.Password != "Pass123!")
        {
            return Unauthorized();
        }

        var claims = new List<Claim>
        {
            new(ClaimTypes.NameIdentifier, "123"),
            new(ClaimTypes.Email, request.Email),
            new(ClaimTypes.Role, "User")
        };

        var identity = new ClaimsIdentity(claims, CookieAuthenticationDefaults.AuthenticationScheme);
        var principal = new ClaimsPrincipal(identity);

        await HttpContext.SignInAsync(
            CookieAuthenticationDefaults.AuthenticationScheme,
            principal,
            new AuthenticationProperties
            {
                IsPersistent = request.RememberMe,
                ExpiresUtc = request.RememberMe
                    ? DateTimeOffset.UtcNow.AddDays(30)
                    : DateTimeOffset.UtcNow.AddHours(1)
            });

        return Ok(new { message = "Logged in" });
    }

    [HttpPost("logout")]
    [Authorize]
    public async Task<IActionResult> Logout()
    {
        await HttpContext.SignOutAsync(CookieAuthenticationDefaults.AuthenticationScheme);
        return Ok(new { message = "Logged out" });
    }
}

record LoginRequest(string Email, string Password, bool RememberMe);
```

---

## 👤 ASP.NET Core Identity

### 🟢 What is Identity?

**Identity = Complete user management system**
- User registration
- Password hashing
- Email confirmation
- Password reset
- Two-factor authentication (2FA)
- Roles & claims management
- Lockout on failed login attempts

### 🟡 Quick Setup

#### Step 1: Install Packages

```bash
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

#### Step 2: Create User Model

```csharp
public class ApplicationUser : IdentityUser<int>
{
    public string FirstName { get; set; } = string.Empty;
    public string LastName { get; set; } = string.Empty;
}
```

#### Step 3: Create DbContext

```csharp
public class AppDbContext : IdentityDbContext<ApplicationUser, IdentityRole<int>, int>
{
    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }
}
```

#### Step 4: Configure (Program.cs)

```csharp
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));

builder.Services.AddIdentity<ApplicationUser, IdentityRole<int>>(options =>
{
    // Password settings
    options.Password.RequireDigit = true;
    options.Password.RequiredLength = 8;
    options.Password.RequireNonAlphanumeric = true;
    options.Password.RequireUppercase = true;

    // Lockout settings
    options.Lockout.MaxFailedAccessAttempts = 5;
    options.Lockout.DefaultLockoutTimeSpan = TimeSpan.FromMinutes(15);

    // User settings
    options.User.RequireUniqueEmail = true;
    options.SignIn.RequireConfirmedEmail = true;
})
.AddEntityFrameworkStores<AppDbContext>()
.AddDefaultTokenProviders();
```

#### Step 5: Migrations

```bash
dotnet ef migrations add InitialIdentity
dotnet ef database update
```

#### Step 6: Register & Login

```csharp
[ApiController]
[Route("api/[controller]")]
public class AuthController : ControllerBase
{
    private readonly UserManager<ApplicationUser> _userManager;
    private readonly SignInManager<ApplicationUser> _signInManager;
    private readonly IJwtService _jwtService;

    public AuthController(
        UserManager<ApplicationUser> userManager,
        SignInManager<ApplicationUser> signInManager,
        IJwtService jwtService)
    {
        _userManager = userManager;
        _signInManager = signInManager;
        _jwtService = jwtService;
    }

    [HttpPost("register")]
    public async Task<IActionResult> Register([FromBody] RegisterRequest request)
    {
        var user = new ApplicationUser
        {
            UserName = request.Email,
            Email = request.Email,
            FirstName = request.FirstName,
            LastName = request.LastName
        };

        var result = await _userManager.CreateAsync(user, request.Password);

        if (!result.Succeeded)
        {
            return BadRequest(new { errors = result.Errors });
        }

        await _userManager.AddToRoleAsync(user, "User");

        return Ok(new { message = "User registered", userId = user.Id });
    }

    [HttpPost("login")]
    public async Task<IActionResult> Login([FromBody] LoginRequest request)
    {
        var user = await _userManager.FindByEmailAsync(request.Email);
        if (user == null)
        {
            return Unauthorized(new { message = "Invalid credentials" });
        }

        var result = await _signInManager.CheckPasswordSignInAsync(
            user, request.Password, lockoutOnFailure: true);

        if (result.IsLockedOut)
        {
            return Unauthorized(new { message = "Account locked" });
        }

        if (!result.Succeeded)
        {
            return Unauthorized(new { message = "Invalid credentials" });
        }

        var roles = await _userManager.GetRolesAsync(user);
        var token = _jwtService.GenerateAccessToken(
            user.Id.ToString(), user.Email!, roles);

        return Ok(new { token });
    }

    [HttpPost("forgot-password")]
    public async Task<IActionResult> ForgotPassword([FromBody] ForgotPasswordRequest request)
    {
        var user = await _userManager.FindByEmailAsync(request.Email);
        if (user == null)
        {
            // Don't reveal user doesn't exist
            return Ok(new { message = "If email exists, reset link sent" });
        }

        var token = await _userManager.GeneratePasswordResetTokenAsync(user);
        // TODO: Send email with token
        return Ok(new { message = "If email exists, reset link sent" });
    }

    [HttpPost("reset-password")]
    public async Task<IActionResult> ResetPassword([FromBody] ResetPasswordRequest request)
    {
        var user = await _userManager.FindByEmailAsync(request.Email);
        if (user == null)
        {
            return BadRequest(new { message = "Invalid request" });
        }

        var result = await _userManager.ResetPasswordAsync(
            user, request.Token, request.NewPassword);

        if (!result.Succeeded)
        {
            return BadRequest(new { errors = result.Errors });
        }

        return Ok(new { message = "Password reset successful" });
    }
}

record RegisterRequest(string Email, string Password, string FirstName, string LastName);
record LoginRequest(string Email, string Password);
record ForgotPasswordRequest(string Email);
record ResetPasswordRequest(string Email, string Token, string NewPassword);
```

**Identity Features:**
- ✅ Automatic password hashing (bcrypt)
- ✅ Account lockout after 5 failed attempts
- ✅ Email confirmation tokens
- ✅ Password reset tokens
- ✅ Role management
- ✅ Claims management
- ✅ 2FA support

---

## 🔗 OAuth 2.0 & External Providers

### 🟢 What is OAuth?

**OAuth = Login with Google/Facebook/Microsoft**

Instead of creating accounts, users login with existing accounts.

### 🟡 Google Login Setup

#### Step 1: Get Google Credentials

1. Go to [Google Cloud Console](https://console.cloud.google.com)
2. Create project
3. Enable Google+ API
4. Create OAuth 2.0 credentials
5. Set redirect URI: `https://localhost:5001/signin-google`
6. Copy Client ID & Client Secret

#### Step 2: Install Package

```bash
dotnet add package Microsoft.AspNetCore.Authentication.Google
```

#### Step 3: Configure

```csharp
// appsettings.json
{
  "Google": {
    "ClientId": "your-client-id.apps.googleusercontent.com",
    "ClientSecret": "your-client-secret"
  }
}

// Program.cs
builder.Services.AddAuthentication()
    .AddJwtBearer() // Your existing JWT
    .AddGoogle(options =>
    {
        options.ClientId = builder.Configuration["Google:ClientId"]!;
        options.ClientSecret = builder.Configuration["Google:ClientSecret"]!;
        options.CallbackPath = "/signin-google";
    });
```

#### Step 4: Login Endpoint

```csharp
[ApiController]
[Route("api/[controller]")]
public class AuthController : ControllerBase
{
    [HttpGet("google-login")]
    public IActionResult GoogleLogin()
    {
        var properties = new AuthenticationProperties
        {
            RedirectUri = "/api/auth/google-callback"
        };
        return Challenge(properties, GoogleDefaults.AuthenticationScheme);
    }

    [HttpGet("google-callback")]
    public async Task<IActionResult> GoogleCallback()
    {
        var result = await HttpContext.AuthenticateAsync(
            GoogleDefaults.AuthenticationScheme);

        if (!result.Succeeded)
        {
            return Unauthorized();
        }

        var email = result.Principal.FindFirst(ClaimTypes.Email)?.Value;
        var name = result.Principal.FindFirst(ClaimTypes.Name)?.Value;

        // TODO: Create or find user in your database
        // Generate your JWT token

        return Ok(new { email, name });
    }
}
```

**Supported Providers:**
- Google: `AddGoogle()`
- Microsoft: `AddMicrosoftAccount()`
- Facebook: `AddFacebook()`
- Twitter: `AddTwitter()`
- GitHub: `AddGitHub()` (requires package)

---

# 🟡 Part 3: Authorization

## 🚪 Authorization Fundamentals

### What is Authorization?

**Authorization = "What can you do?"**

After authentication proves WHO you are, authorization determines WHAT you can access.

### The `[Authorize]` Attribute

```csharp
// No attribute = Public (anyone can access)
[HttpGet("public")]
public IActionResult Public() => Ok("Public");

// [Authorize] = Authenticated users only
[HttpGet]
[Authorize]
public IActionResult Protected() => Ok("Authenticated users only");

// [Authorize(Roles)] = Specific roles
[HttpDelete("{id}")]
[Authorize(Roles = "Admin")]
public IActionResult Delete(int id) => Ok($"Deleted {id}");

// [Authorize(Policy)] = Custom rules
[HttpPost]
[Authorize(Policy = "CanCreate")]
public IActionResult Create() => Ok("Created");

// [AllowAnonymous] = Override controller-level [Authorize]
[AllowAnonymous]
[HttpGet("override")]
public IActionResult Override() => Ok("Anyone can access");
```

---

## 👥 Role-Based Authorization (RBAC)

### 🟢 What are Roles?

**Role = User category** (Admin, Manager, User, Guest)

### Simple Role Check

```csharp
[Authorize(Roles = "Admin")]
public IActionResult AdminOnly() => Ok("Admin access");

// Multiple roles (OR logic - Admin OR Manager)
[Authorize(Roles = "Admin,Manager")]
public IActionResult AdminOrManager() => Ok("Admin or Manager");

// Check in code
[Authorize]
public IActionResult CheckRole()
{
    if (User.IsInRole("Admin"))
    {
        return Ok("You are admin");
    }
    return Forbid();
}
```

### Add Roles to JWT

```csharp
var claims = new List<Claim>
{
    new(ClaimTypes.NameIdentifier, "123"),
    new(ClaimTypes.Email, "user@test.com"),
    new(ClaimTypes.Role, "Admin"),   // Role 1
    new(ClaimTypes.Role, "Manager")  // Role 2
};
```

### Role-Based Endpoints Example

```csharp
[ApiController]
[Route("api/[controller]")]
public class UsersController : ControllerBase
{
    // Anyone authenticated
    [HttpGet("{id}")]
    [Authorize]
    public IActionResult Get(int id) => Ok(new { id, name = "John" });

    // Admins only
    [HttpPost]
    [Authorize(Roles = "Admin")]
    public IActionResult Create([FromBody] User user) => Created("", user);

    // Admins or Managers
    [HttpPut("{id}")]
    [Authorize(Roles = "Admin,Manager")]
    public IActionResult Update(int id, [FromBody] User user) => Ok(user);

    // Admins only
    [HttpDelete("{id}")]
    [Authorize(Roles = "Admin")]
    public IActionResult Delete(int id) => NoContent();
}
```

---

## 🎫 Claims-Based Authorization

### 🟢 What are Claims?

**Claim = Piece of information about user**

Examples:
- `{ "email": "user@test.com" }`
- `{ "department": "IT" }`
- `{ "subscriptionLevel": "Premium" }`

### Check Claims in Code

```csharp
[Authorize]
public IActionResult CheckClaims()
{
    // Get claim value
    var email = User.FindFirst(ClaimTypes.Email)?.Value;
    var dept = User.FindFirst("department")?.Value;

    // Check if claim exists
    bool hasPremium = User.HasClaim("subscriptionLevel", "Premium");

    if (hasPremium)
    {
        return Ok("Premium feature unlocked");
    }

    return Forbid("Upgrade to Premium");
}
```

### Require Specific Claim

```csharp
// Program.cs
builder.Services.AddAuthorization(options =>
{
    options.AddPolicy("PremiumOnly", policy =>
        policy.RequireClaim("subscriptionLevel", "Premium"));
});

// Controller
[Authorize(Policy = "PremiumOnly")]
public IActionResult PremiumFeature() => Ok("Premium content");
```

---

## 📋 Policy-Based Authorization

### 🟢 What are Policies?

**Policy = Custom authorization rules**

Complex logic like: "User must be over 18 AND have verified email"

### Simple Policy

```csharp
// Program.cs
builder.Services.AddAuthorization(options =>
{
    // Policy: Must have Admin role
    options.AddPolicy("RequireAdmin", policy =>
        policy.RequireRole("Admin"));

    // Policy: Must have specific claim
    options.AddPolicy("RequirePremium", policy =>
        policy.RequireClaim("subscriptionLevel", "Premium"));

    // Policy: Must be authenticated
    options.AddPolicy("RequireAuth", policy =>
        policy.RequireAuthenticatedUser());
});

// Controller
[Authorize(Policy = "RequireAdmin")]
public IActionResult AdminOnly() => Ok();
```

### 🟡 Advanced: Custom Policy

**Scenario:** Only users over 18 can access

```csharp
// Requirement
public class MinimumAgeRequirement : IAuthorizationRequirement
{
    public int MinimumAge { get; }
    public MinimumAgeRequirement(int minimumAge) => MinimumAge = minimumAge;
}

// Handler
public class MinimumAgeHandler : AuthorizationHandler<MinimumAgeRequirement>
{
    protected override Task HandleRequirementAsync(
        AuthorizationHandlerContext context,
        MinimumAgeRequirement requirement)
    {
        var dobClaim = context.User.FindFirst(ClaimTypes.DateOfBirth);
        if (dobClaim == null)
        {
            return Task.CompletedTask; // Fail
        }

        var dob = DateTime.Parse(dobClaim.Value);
        var age = DateTime.Today.Year - dob.Year;

        if (age >= requirement.MinimumAge)
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }
}

// Register
builder.Services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
        policy.Requirements.Add(new MinimumAgeRequirement(18)));
});
builder.Services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();

// Use
[Authorize(Policy = "Over18")]
public IActionResult AdultContent() => Ok("18+ content");
```

### 🔴 Advanced: Combine Multiple Requirements

```csharp
builder.Services.AddAuthorization(options =>
{
    options.AddPolicy("PremiumAdult", policy =>
    {
        policy.RequireAuthenticatedUser();
        policy.RequireClaim("subscriptionLevel", "Premium");
        policy.Requirements.Add(new MinimumAgeRequirement(18));
    });
});
```

---

## 📦 Resource-Based Authorization

### 🟢 What is Resource-Based Auth?

**Check ownership before allowing action**

Example: Users can only edit THEIR OWN documents.

### 🟡 Implementation

```csharp
// Requirement
public class DocumentOwnerRequirement : IAuthorizationRequirement { }

// Handler
public class DocumentOwnerHandler
    : AuthorizationHandler<DocumentOwnerRequirement, Document>
{
    protected override Task HandleRequirementAsync(
        AuthorizationHandlerContext context,
        DocumentOwnerRequirement requirement,
        Document resource)
    {
        var userId = context.User.FindFirst(ClaimTypes.NameIdentifier)?.Value;

        // Check if user owns the document
        if (resource.OwnerId == userId)
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }
}

// Register
builder.Services.AddAuthorization(options =>
{
    options.AddPolicy("DocumentOwner", policy =>
        policy.Requirements.Add(new DocumentOwnerRequirement()));
});
builder.Services.AddSingleton<IAuthorizationHandler, DocumentOwnerHandler>();

// Controller
[ApiController]
[Route("api/[controller]")]
public class DocumentsController : ControllerBase
{
    private readonly IAuthorizationService _authService;

    public DocumentsController(IAuthorizationService authService)
    {
        _authService = authService;
    }

    [HttpPut("{id}")]
    [Authorize]
    public async Task<IActionResult> Update(int id, [FromBody] Document updated)
    {
        // Get document from database
        var document = GetDocument(id); // Your DB call

        // Check authorization
        var authResult = await _authService.AuthorizeAsync(
            User, document, "DocumentOwner");

        if (!authResult.Succeeded)
        {
            return Forbid(); // 403
        }

        // Update document
        return Ok(updated);
    }

    private Document GetDocument(int id) =>
        new Document { Id = id, OwnerId = "123", Title = "Doc" };
}

public class Document
{
    public int Id { get; set; }
    public string OwnerId { get; set; } = string.Empty;
    public string Title { get; set; } = string.Empty;
}
```

**Use case:** User can edit if:
- They own the document OR
- They are Admin

```csharp
protected override Task HandleRequirementAsync(
    AuthorizationHandlerContext context,
    DocumentOwnerRequirement requirement,
    Document resource)
{
    var userId = context.User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
    var isAdmin = context.User.IsInRole("Admin");

    if (resource.OwnerId == userId || isAdmin)
    {
        context.Succeed(requirement);
    }

    return Task.CompletedTask;
}
```

---

# 🔴 Part 4: Production & Best Practices

## 🔒 API Security Best Practices

### 1. Always Use HTTPS in Production

```csharp
// Program.cs
if (app.Environment.IsProduction())
{
    app.UseHsts(); // HTTP Strict Transport Security
    app.UseHttpsRedirection();
}
```

### 2. Use Strong Secret Keys

```csharp
// ❌ NEVER DO THIS
var key = "MySecretKey123"; // Too short, predictable

// ✅ DO THIS
var key = "aB3$kL9@mN7#pQ2&vW8!xY5^zC1*dF4%gH6+"; // 32+ chars, random

// ✅ BEST: Use Azure Key Vault or AWS Secrets Manager
var key = builder.Configuration["KeyVault:JwtSecret"];
```

### 3. Set Token Expiry

```csharp
// Access token: Short-lived (15-60 min)
expires: DateTime.UtcNow.AddMinutes(15)

// Refresh token: Long-lived (7-30 days)
ExpiresAt = DateTime.UtcNow.AddDays(7)
```

### 4. Enable CORS Properly

```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("Production", policy =>
    {
        policy.WithOrigins("https://yourdomain.com") // Specific origin
              .AllowAnyMethod()
              .AllowAnyHeader()
              .AllowCredentials();
    });
});

app.UseCors("Production");
```

### 5. Rate Limiting

```csharp
// .NET 7+
builder.Services.AddRateLimiter(options =>
{
    options.AddFixedWindowLimiter("fixed", opt =>
    {
        opt.Window = TimeSpan.FromMinutes(1);
        opt.PermitLimit = 100; // 100 requests per minute
    });
});

app.UseRateLimiter();

// On endpoint
[EnableRateLimiting("fixed")]
public IActionResult LimitedEndpoint() => Ok();
```

### 6. Input Validation

```csharp
public class RegisterRequest
{
    [Required]
    [EmailAddress]
    public string Email { get; set; } = string.Empty;

    [Required]
    [MinLength(8)]
    [RegularExpression(@"^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]{8,}$")]
    public string Password { get; set; } = string.Empty;
}
```

### 7. Logging (But Not Secrets!)

```csharp
// ❌ DON'T log sensitive data
_logger.LogInformation("User logged in: {Email} with password {Password}", email, password);

// ✅ DO log safely
_logger.LogInformation("User logged in: {Email}", email);
_logger.LogWarning("Failed login attempt for: {Email}", email);
```

---

## ⚠️ Common Security Pitfalls

### Pitfall 1: Storing Secrets in Code

```csharp
// ❌ NEVER
var key = "MySecretKey";
var connectionString = "Server=...;Password=admin123;";

// ✅ ALWAYS use configuration
var key = _config["Jwt:Key"];
var connectionString = _config.GetConnectionString("Default");
```

### Pitfall 2: No HTTPS

```csharp
// ❌ Tokens sent over HTTP can be intercepted
RequireHttpsMetadata = false

// ✅ Always require HTTPS
RequireHttpsMetadata = true
```

### Pitfall 3: Long Token Expiry

```csharp
// ❌ Token valid for 30 days - if stolen, attacker has 30 days
expires: DateTime.UtcNow.AddDays(30)

// ✅ Short-lived access tokens
expires: DateTime.UtcNow.AddMinutes(15)
```

### Pitfall 4: Storing JWT in localStorage

```javascript
// ❌ Vulnerable to XSS attacks
localStorage.setItem('token', jwt);

// ✅ Use httpOnly cookies
// Set from server-side
Response.Cookies.Append("token", jwt, new CookieOptions
{
    HttpOnly = true,
    Secure = true,
    SameSite = SameSiteMode.Strict
});
```

### Pitfall 5: Not Validating Issuer/Audience

```csharp
// ❌ Anyone can create tokens
ValidateIssuer = false,
ValidateAudience = false

// ✅ Validate both
ValidateIssuer = true,
ValidIssuer = "https://yourdomain.com",
ValidateAudience = true,
ValidAudience = "https://yourdomain.com"
```

### Pitfall 6: SQL Injection

```csharp
// ❌ NEVER concatenate user input
var query = $"SELECT * FROM Users WHERE Email = '{email}'";

// ✅ ALWAYS use parameters
var query = "SELECT * FROM Users WHERE Email = @Email";
command.Parameters.AddWithValue("@Email", email);

// ✅ OR use EF Core (safe by default)
var user = await _context.Users.FirstOrDefaultAsync(u => u.Email == email);
```

### Pitfall 7: No Input Validation

```csharp
// ❌ Trust user input
public IActionResult Create(string name) => Ok();

// ✅ Validate input
public IActionResult Create([Required][MaxLength(100)] string name) => Ok();
```

---

## 🏢 Multi-Tenant Authorization

### 🟢 What is Multi-Tenant?

**Multiple customers (tenants) share the same application, but data is isolated.**

Example: Slack (each workspace is a tenant)

### 🟡 Tenant Isolation Strategy

```csharp
// Add tenantId to JWT claims
var claims = new List<Claim>
{
    new(ClaimTypes.NameIdentifier, "123"),
    new("tenantId", "tenant-456")
};

// Filter data by tenant
[Authorize]
public async Task<IActionResult> GetOrders()
{
    var tenantId = User.FindFirst("tenantId")?.Value;

    var orders = await _context.Orders
        .Where(o => o.TenantId == tenantId)
        .ToListAsync();

    return Ok(orders);
}
```

### 🔴 Advanced: Tenant-Specific Policies

```csharp
// Requirement
public class TenantRequirement : IAuthorizationRequirement
{
    public string TenantId { get; }
    public TenantRequirement(string tenantId) => TenantId = tenantId;
}

// Handler
public class TenantHandler : AuthorizationHandler<TenantRequirement>
{
    protected override Task HandleRequirementAsync(
        AuthorizationHandlerContext context,
        TenantRequirement requirement)
    {
        var userTenantId = context.User.FindFirst("tenantId")?.Value;

        if (userTenantId == requirement.TenantId)
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }
}

// Register
builder.Services.AddAuthorization(options =>
{
    options.AddPolicy("Tenant1Only", policy =>
        policy.Requirements.Add(new TenantRequirement("tenant-1")));
});
```

---

## 🌍 Real-World Scenarios

### Scenario 1: E-Commerce API

**Requirements:**
- Customers can view products (no auth)
- Customers can view THEIR orders (authenticated)
- Admins can view ALL orders
- Only Admins can create products

```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    // Public - anyone
    [HttpGet]
    public IActionResult GetAll() => Ok(new[] { "Product 1", "Product 2" });

    // Admin only
    [HttpPost]
    [Authorize(Roles = "Admin")]
    public IActionResult Create([FromBody] Product product) => Created("", product);
}

[ApiController]
[Route("api/[controller]")]
public class OrdersController : ControllerBase
{
    // Customers see THEIR orders, Admins see ALL
    [HttpGet]
    [Authorize]
    public IActionResult GetOrders()
    {
        var userId = User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
        var isAdmin = User.IsInRole("Admin");

        if (isAdmin)
        {
            // Return all orders
            return Ok(new[] { "All orders" });
        }

        // Return user's orders only
        return Ok(new[] { $"Orders for user {userId}" });
    }
}
```

### Scenario 2: SaaS Multi-Tenant

**Requirements:**
- Users belong to organizations (tenants)
- Users can only see data from THEIR organization
- Org Admins can manage users in THEIR org
- Super Admins can manage ANY org

```csharp
[Authorize]
public async Task<IActionResult> GetDocuments()
{
    var tenantId = User.FindFirst("tenantId")?.Value;
    var isSuperAdmin = User.IsInRole("SuperAdmin");

    if (isSuperAdmin)
    {
        // Super admin sees all tenants
        return Ok(await _context.Documents.ToListAsync());
    }

    // Regular users see only their tenant
    var docs = await _context.Documents
        .Where(d => d.TenantId == tenantId)
        .ToListAsync();

    return Ok(docs);
}
```

### Scenario 3: Document Sharing

**Requirements:**
- Owner can edit document
- Collaborators can view document
- Anyone with link can view (if public)

```csharp
public async Task<IActionResult> GetDocument(int id)
{
    var doc = await _context.Documents.FindAsync(id);
    if (doc == null) return NotFound();

    // Public document - anyone
    if (doc.IsPublic)
    {
        return Ok(doc);
    }

    // Must be authenticated for private docs
    if (!User.Identity.IsAuthenticated)
    {
        return Unauthorized();
    }

    var userId = User.FindFirst(ClaimTypes.NameIdentifier)?.Value;

    // Owner or collaborator
    if (doc.OwnerId == userId ||
        doc.Collaborators.Contains(userId))
    {
        return Ok(doc);
    }

    return Forbid();
}
```

---

## 🔧 Troubleshooting Guide

### Error: "No authentication handler is registered for the scheme 'Bearer'"

**Cause:** Forgot to call `AddAuthentication().AddJwtBearer()`

**Fix:**
```csharp
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options => { /* config */ });
```

### Error: "IDX10503: Signature validation failed"

**Cause:** Secret key mismatch between token generation and validation

**Fix:** Use same key for both
```csharp
// Generation
var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("YourKey"));

// Validation
IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("YourKey"))
```

### Error: "IDX10223: Lifetime validation failed"

**Cause:** Token expired

**Fix:** Generate new token or use refresh token

### Always Get 401 Unauthorized

**Check:**
1. Is `UseAuthentication()` called before `UseAuthorization()`?
2. Is token sent in header: `Authorization: Bearer <token>`?
3. Is token expired? Check `exp` claim
4. Is secret key correct?

### Always Get 403 Forbidden

**Cause:** Authenticated but not authorized (missing role/claim)

**Check:**
1. Does user have required role?
2. Does token contain correct claims?
3. Is policy configured correctly?

---

# 📖 Appendix

## 🌳 Decision Trees

### Choose Authentication Method

```
What are you building?
├─ Mobile App → JWT
├─ SPA (React/Vue/Angular) → JWT
├─ Traditional Web App (MVC) → Cookie
├─ Microservices → JWT
└─ Public API → JWT
```

### Choose Authorization Method

```
Authorization requirement?
├─ Simple role check → [Authorize(Roles = "Admin")]
├─ Check user property → Claims-based
├─ Complex logic (multiple conditions) → Policy-based
└─ Resource ownership → Resource-based authorization
```

---

## 🧪 Testing Strategies

### Unit Test JWT Generation

```csharp
[Fact]
public void GenerateToken_ReturnsValidJwt()
{
    // Arrange
    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(new Dictionary<string, string>
        {
            {"Jwt:Key", "TestKey32CharactersLongForTesting!"},
            {"Jwt:Issuer", "TestIssuer"},
            {"Jwt:Audience", "TestAudience"}
        })
        .Build();

    var jwtService = new JwtService(config);

    // Act
    var token = jwtService.GenerateAccessToken("123", "test@test.com", new[] { "User" });

    // Assert
    Assert.NotNull(token);
    Assert.Contains(".", token); // JWT has dots
}
```

### Integration Test Protected Endpoint

```csharp
[Fact]
public async Task ProtectedEndpoint_WithoutToken_Returns401()
{
    // Arrange
    var client = _factory.CreateClient();

    // Act
    var response = await client.GetAsync("/api/products");

    // Assert
    Assert.Equal(HttpStatusCode.Unauthorized, response.StatusCode);
}

[Fact]
public async Task ProtectedEndpoint_WithToken_Returns200()
{
    // Arrange
    var client = _factory.CreateClient();
    var token = GetValidToken(); // Your helper method

    client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", token);

    // Act
    var response = await client.GetAsync("/api/products");

    // Assert
    Assert.Equal(HttpStatusCode.OK, response.StatusCode);
}
```

---

## ✅ Deployment Checklist

### Before Production

- [ ] Move secrets to Azure Key Vault / AWS Secrets Manager
- [ ] Enable HTTPS (RequireHttpsMetadata = true)
- [ ] Set short token expiry (15-30 min access tokens)
- [ ] Implement refresh tokens
- [ ] Enable rate limiting
- [ ] Add logging (without sensitive data)
- [ ] Enable CORS with specific origins
- [ ] Validate all user input
- [ ] Use parameterized queries (prevent SQL injection)
- [ ] Test with real SSL certificate
- [ ] Enable HSTS
- [ ] Set up monitoring/alerts
- [ ] Document API endpoints
- [ ] Load test authentication endpoints

### Security Checklist

- [ ] No secrets in code
- [ ] Strong random secret keys (32+ characters)
- [ ] HTTPS everywhere
- [ ] HttpOnly cookies
- [ ] SameSite cookie policy
- [ ] Input validation on all endpoints
- [ ] SQL injection prevention
- [ ] XSS protection
- [ ] CSRF protection
- [ ] Rate limiting
- [ ] Account lockout after failed attempts
- [ ] Password complexity requirements
- [ ] Email confirmation
- [ ] 2FA option for admins

---

## 🎯 Summary

### Authentication Methods

| Method | Use Case | Pros | Cons |
|--------|----------|------|------|
| **JWT** | APIs, Mobile, SPAs | Stateless, scalable | Can't revoke easily |
| **Cookie** | Traditional web apps | Simple, secure by default | Requires server-side state |
| **OAuth** | Social login | Easy for users | Dependency on provider |
| **Identity** | Full user management | Everything included | Complex setup |

### Authorization Methods

| Method | Use Case | Example |
|--------|----------|---------|
| **Role-Based** | Simple permissions | Admin, User, Manager |
| **Claims-Based** | User properties | Department, Subscription |
| **Policy-Based** | Complex rules | Age > 18 AND verified email |
| **Resource-Based** | Ownership | User can edit THEIR documents |

### Key Takeaways

1. **Authentication** = WHO you are (login)
2. **Authorization** = WHAT you can do (permissions)
3. **JWT** = Best for APIs and mobile
4. **Cookies** = Best for traditional web apps
5. **Short-lived access tokens** (15 min) + long-lived refresh tokens (7 days)
6. **Never store secrets in code** - use Key Vault
7. **Always use HTTPS** in production
8. **Validate input** to prevent attacks
9. **Test authentication flows** thoroughly
10. **Monitor and log** security events

---

**🎉 You've completed the guide!**

You now know:
- ✅ Authentication (JWT, Cookies, Identity, OAuth)
- ✅ Authorization (Roles, Claims, Policies, Resource-based)
- ✅ Security best practices
- ✅ Production deployment
- ✅ Real-world scenarios
- ✅ Troubleshooting

**Next steps:**
1. Build a sample project using what you learned
2. Implement authentication in your real application
3. Add authorization based on your requirements
4. Deploy to production with proper security

**Happy coding! 🚀**
