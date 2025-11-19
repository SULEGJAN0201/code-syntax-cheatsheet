# ASP.NET Core Authentication & Authorization Guide
## Complete Guide for Senior Developers - .NET 8/9

> **Production-Ready Guide**: Master authentication and authorization in ASP.NET Core with JWT, OAuth 2.0, OpenID Connect, and Identity. Based on official Microsoft documentation and industry best practices.

---

## 📚 Table of Contents

1. [Authentication vs Authorization](#-authentication-vs-authorization)
2. [Authentication Fundamentals](#-authentication-fundamentals)
3. [JWT Bearer Authentication](#-jwt-bearer-authentication)
4. [Cookie Authentication](#-cookie-authentication)
5. [ASP.NET Core Identity](#-aspnet-core-identity)
6. [OAuth 2.0 & OpenID Connect](#-oauth-20--openid-connect)
7. [Authorization Fundamentals](#-authorization-fundamentals)
8. [Role-Based Authorization (RBAC)](#-role-based-authorization-rbac)
9. [Claims-Based Authorization](#-claims-based-authorization)
10. [Policy-Based Authorization](#-policy-based-authorization)
11. [Resource-Based Authorization](#-resource-based-authorization)
12. [API Security Best Practices](#-api-security-best-practices)
13. [Multi-Tenant Authorization](#-multi-tenant-authorization)
14. [Real-World Scenarios](#-real-world-scenarios)
15. [Common Security Pitfalls](#-common-security-pitfalls)
16. [Troubleshooting Guide](#-troubleshooting-guide)

---

## 🔐 Authentication vs Authorization

### Key Differences

```csharp
// AUTHENTICATION: "Who are you?"
// Validates identity - username/password, JWT token, OAuth, etc.

// AUTHORIZATION: "What can you do?"
// Validates permissions - roles, claims, policies, resources
```

| Aspect | Authentication | Authorization |
|--------|---------------|---------------|
| **Question** | Who are you? | What can you access? |
| **Process** | Verify identity | Check permissions |
| **Happens** | First (login) | After authentication |
| **Result** | ClaimsPrincipal with identity | Allow or Deny access |
| **HTTP Status** | 401 Unauthorized | 403 Forbidden |
| **Attributes** | N/A | `[Authorize]` |
| **Examples** | Login, JWT, OAuth | Roles, Claims, Policies |

### ASP.NET Core Pipeline

```
HTTP Request
    ↓
Authentication Middleware (validates identity)
    ↓
ClaimsPrincipal created (User object)
    ↓
Authorization Middleware (checks permissions)
    ↓
Controller/Action executes (if authorized)
    ↓
HTTP Response
```

---

## 🎯 Authentication Fundamentals

### How Authentication Works in ASP.NET Core

```csharp
// Authentication is handled by IAuthenticationService
// Uses registered authentication handlers (schemes)

// 1. User provides credentials
// 2. Authentication handler validates
// 3. Handler creates ClaimsPrincipal
// 4. Principal is set on HttpContext.User
// 5. Authentication cookie/token is issued
```

### Authentication Schemes

```csharp
// Program.cs (ASP.NET Core 8/9)

var builder = WebApplication.CreateBuilder(args);

// Register multiple authentication schemes
builder.Services.AddAuthentication(options =>
{
    // Default scheme for challenges
    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
})
.AddJwtBearer(options =>
{
    // JWT configuration (see JWT section)
})
.AddCookie("CookieAuth", options =>
{
    // Cookie configuration (see Cookie section)
})
.AddGoogle(options =>
{
    // External provider (see OAuth section)
    options.ClientId = builder.Configuration["Google:ClientId"];
    options.ClientSecret = builder.Configuration["Google:ClientSecret"];
});

var app = builder.Build();

// CRITICAL: Order matters!
app.UseAuthentication();  // Must come BEFORE UseAuthorization
app.UseAuthorization();

app.MapControllers();
app.Run();
```

### The ClaimsPrincipal

```csharp
// Every authenticated user is represented as a ClaimsPrincipal
// Contains one or more ClaimsIdentity objects
// Each ClaimsIdentity contains Claims

public class AuthExampleController : ControllerBase
{
    [HttpGet("me")]
    [Authorize]
    public IActionResult GetCurrentUser()
    {
        // Access current user
        var user = HttpContext.User;  // ClaimsPrincipal

        // Get specific claims
        var userId = user.FindFirst(ClaimTypes.NameIdentifier)?.Value;
        var email = user.FindFirst(ClaimTypes.Email)?.Value;
        var roles = user.FindAll(ClaimTypes.Role).Select(c => c.Value);

        // Check if authenticated
        if (!user.Identity.IsAuthenticated)
        {
            return Unauthorized();
        }

        return Ok(new
        {
            UserId = userId,
            Email = email,
            Roles = roles,
            AuthenticationType = user.Identity.AuthenticationType,
            AllClaims = user.Claims.Select(c => new { c.Type, c.Value })
        });
    }
}
```

---

## 🔑 JWT Bearer Authentication

### Complete JWT Implementation

**Step 1: Install Package**

```bash
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
dotnet add package System.IdentityModel.Tokens.Jwt
```

**Step 2: Configuration (appsettings.json)**

```json
{
  "Jwt": {
    "Key": "YourSuperSecretKeyMinimum32CharactersLong!",
    "Issuer": "https://yourdomain.com",
    "Audience": "https://yourdomain.com",
    "ExpiryMinutes": 60,
    "RefreshTokenExpiryDays": 7
  }
}
```

**Step 3: JWT Settings Model**

```csharp
public class JwtSettings
{
    public string Key { get; set; } = string.Empty;
    public string Issuer { get; set; } = string.Empty;
    public string Audience { get; set; } = string.Empty;
    public int ExpiryMinutes { get; set; } = 60;
    public int RefreshTokenExpiryDays { get; set; } = 7;
}
```

**Step 4: JWT Service**

```csharp
public interface IJwtService
{
    string GenerateToken(string userId, string email, IEnumerable<string> roles);
    string GenerateRefreshToken();
    ClaimsPrincipal? GetPrincipalFromExpiredToken(string token);
}

public class JwtService : IJwtService
{
    private readonly JwtSettings _jwtSettings;

    public JwtService(IOptions<JwtSettings> jwtSettings)
    {
        _jwtSettings = jwtSettings.Value;
    }

    public string GenerateToken(string userId, string email, IEnumerable<string> roles)
    {
        var claims = new List<Claim>
        {
            new Claim(ClaimTypes.NameIdentifier, userId),
            new Claim(ClaimTypes.Email, email),
            new Claim(JwtRegisteredClaimNames.Sub, userId),
            new Claim(JwtRegisteredClaimNames.Email, email),
            new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString()),
            new Claim(JwtRegisteredClaimNames.Iat,
                DateTimeOffset.UtcNow.ToUnixTimeSeconds().ToString(),
                ClaimValueTypes.Integer64)
        };

        // Add roles as claims
        foreach (var role in roles)
        {
            claims.Add(new Claim(ClaimTypes.Role, role));
        }

        var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_jwtSettings.Key));
        var creds = new SigningCredentials(key, SecurityAlgorithms.HmacSha256);

        var token = new JwtSecurityToken(
            issuer: _jwtSettings.Issuer,
            audience: _jwtSettings.Audience,
            claims: claims,
            expires: DateTime.UtcNow.AddMinutes(_jwtSettings.ExpiryMinutes),
            signingCredentials: creds
        );

        return new JwtSecurityTokenHandler().WriteToken(token);
    }

    public string GenerateRefreshToken()
    {
        var randomNumber = new byte[32];
        using var rng = RandomNumberGenerator.Create();
        rng.GetBytes(randomNumber);
        return Convert.ToBase64String(randomNumber);
    }

    public ClaimsPrincipal? GetPrincipalFromExpiredToken(string token)
    {
        var tokenValidationParameters = new TokenValidationParameters
        {
            ValidateAudience = false,
            ValidateIssuer = false,
            ValidateIssuerSigningKey = true,
            IssuerSigningKey = new SymmetricSecurityKey(
                Encoding.UTF8.GetBytes(_jwtSettings.Key)),
            ValidateLifetime = false // Don't validate expiry for refresh
        };

        var tokenHandler = new JwtSecurityTokenHandler();
        var principal = tokenHandler.ValidateToken(token, tokenValidationParameters,
            out SecurityToken securityToken);

        if (securityToken is not JwtSecurityToken jwtSecurityToken ||
            !jwtSecurityToken.Header.Alg.Equals(SecurityAlgorithms.HmacSha256,
                StringComparison.InvariantCultureIgnoreCase))
        {
            throw new SecurityTokenException("Invalid token");
        }

        return principal;
    }
}
```

**Step 5: Register JWT in Program.cs**

```csharp
// Program.cs

var builder = WebApplication.CreateBuilder(args);

// Bind JWT settings
builder.Services.Configure<JwtSettings>(
    builder.Configuration.GetSection("Jwt"));

// Register JWT service
builder.Services.AddScoped<IJwtService, JwtService>();

// Configure JWT authentication
builder.Services.AddAuthentication(options =>
{
    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
})
.AddJwtBearer(options =>
{
    var jwtSettings = builder.Configuration.GetSection("Jwt").Get<JwtSettings>();

    options.SaveToken = true;
    options.RequireHttpsMetadata = true; // ALWAYS true in production
    options.TokenValidationParameters = new TokenValidationParameters
    {
        ValidateIssuer = true,
        ValidateAudience = true,
        ValidateLifetime = true,
        ValidateIssuerSigningKey = true,
        ValidIssuer = jwtSettings.Issuer,
        ValidAudience = jwtSettings.Audience,
        IssuerSigningKey = new SymmetricSecurityKey(
            Encoding.UTF8.GetBytes(jwtSettings.Key)),
        ClockSkew = TimeSpan.Zero // Remove default 5-minute tolerance
    };

    // Optional: Handle authentication events
    options.Events = new JwtBearerEvents
    {
        OnAuthenticationFailed = context =>
        {
            if (context.Exception.GetType() == typeof(SecurityTokenExpiredException))
            {
                context.Response.Headers.Add("Token-Expired", "true");
            }
            return Task.CompletedTask;
        },
        OnTokenValidated = context =>
        {
            // Custom validation logic
            var userId = context.Principal?.FindFirst(ClaimTypes.NameIdentifier)?.Value;
            // Check if user is still active in database, etc.
            return Task.CompletedTask;
        }
    };
});

var app = builder.Build();

app.UseAuthentication();
app.UseAuthorization();

app.MapControllers();
app.Run();
```

**Step 6: Authentication Controller**

```csharp
[ApiController]
[Route("api/[controller]")]
public class AuthController : ControllerBase
{
    private the IJwtService _jwtService;
    private readonly IUserRepository _userRepository;

    public AuthController(IJwtService jwtService, IUserRepository userRepository)
    {
        _jwtService = jwtService;
        _userRepository = userRepository;
    }

    [HttpPost("login")]
    public async Task<IActionResult> Login([FromBody] LoginRequest request)
    {
        // Validate user credentials (use ASP.NET Core Identity in production)
        var user = await _userRepository.ValidateCredentialsAsync(
            request.Email, request.Password);

        if (user == null)
        {
            return Unauthorized(new { message = "Invalid credentials" });
        }

        // Get user roles
        var roles = await _userRepository.GetUserRolesAsync(user.Id);

        // Generate tokens
        var accessToken = _jwtService.GenerateToken(
            user.Id.ToString(), user.Email, roles);
        var refreshToken = _jwtService.GenerateRefreshToken();

        // Save refresh token to database
        await _userRepository.SaveRefreshTokenAsync(user.Id, refreshToken,
            DateTime.UtcNow.AddDays(7));

        return Ok(new
        {
            AccessToken = accessToken,
            RefreshToken = refreshToken,
            ExpiresIn = 3600, // seconds
            TokenType = "Bearer"
        });
    }

    [HttpPost("refresh")]
    public async Task<IActionResult> Refresh([FromBody] RefreshTokenRequest request)
    {
        var principal = _jwtService.GetPrincipalFromExpiredToken(request.AccessToken);
        if (principal == null)
        {
            return BadRequest(new { message = "Invalid token" });
        }

        var userId = principal.FindFirst(ClaimTypes.NameIdentifier)?.Value;
        if (string.IsNullOrEmpty(userId))
        {
            return BadRequest(new { message = "Invalid token" });
        }

        // Validate refresh token from database
        var isValid = await _userRepository.ValidateRefreshTokenAsync(
            int.Parse(userId), request.RefreshToken);

        if (!isValid)
        {
            return Unauthorized(new { message = "Invalid refresh token" });
        }

        // Generate new tokens
        var user = await _userRepository.GetByIdAsync(int.Parse(userId));
        var roles = await _userRepository.GetUserRolesAsync(user.Id);

        var newAccessToken = _jwtService.GenerateToken(
            user.Id.ToString(), user.Email, roles);
        var newRefreshToken = _jwtService.GenerateRefreshToken();

        // Update refresh token in database
        await _userRepository.SaveRefreshTokenAsync(user.Id, newRefreshToken,
            DateTime.UtcNow.AddDays(7));

        return Ok(new
        {
            AccessToken = newAccessToken,
            RefreshToken = newRefreshToken,
            ExpiresIn = 3600
        });
    }

    [HttpPost("logout")]
    [Authorize]
    public async Task<IActionResult> Logout()
    {
        var userId = User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
        if (!string.IsNullOrEmpty(userId))
        {
            // Revoke refresh token
            await _userRepository.RevokeRefreshTokenAsync(int.Parse(userId));
        }

        return Ok(new { message = "Logged out successfully" });
    }
}

public record LoginRequest(string Email, string Password);
public record RefreshTokenRequest(string AccessToken, string RefreshToken);
```

**Step 7: Using JWT in Controllers**

```csharp
[ApiController]
[Route("api/[controller]")]
[Authorize] // Requires valid JWT token
public class ProductsController : ControllerBase
{
    [HttpGet]
    public IActionResult GetAll()
    {
        // User is authenticated (has valid JWT)
        var userId = User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
        return Ok(new { message = $"Hello user {userId}" });
    }

    [HttpGet("{id}")]
    [AllowAnonymous] // Override [Authorize] on controller
    public IActionResult Get(int id)
    {
        // Anyone can access this endpoint
        return Ok(new { id, name = "Product" });
    }
}
```

**Client-Side Usage (JavaScript/Fetch)**

```javascript
// Login
const loginResponse = await fetch('/api/auth/login', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ email: 'user@example.com', password: 'password123' })
});

const { accessToken, refreshToken } = await loginResponse.json();

// Store tokens (use httpOnly cookies in production for better security)
localStorage.setItem('accessToken', accessToken);
localStorage.setItem('refreshToken', refreshToken);

// Make authenticated request
const response = await fetch('/api/products', {
    headers: {
        'Authorization': `Bearer ${localStorage.getItem('accessToken')}`
    }
});

// Handle 401 (token expired) - refresh token
if (response.status === 401) {
    const refreshResponse = await fetch('/api/auth/refresh', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
            accessToken: localStorage.getItem('accessToken'),
            refreshToken: localStorage.getItem('refreshToken')
        })
    });

    const newTokens = await refreshResponse.json();
    localStorage.setItem('accessToken', newTokens.accessToken);
    localStorage.setItem('refreshToken', newTokens.refreshToken);

    // Retry original request
}
```

### JWT Security Best Practices

```csharp
// ✅ DO: Use strong, random secret keys (minimum 256 bits / 32 characters)
"Jwt:Key": "YourSuperSecretKeyMinimum32CharactersLong!Random123456"

// ✅ DO: Store keys in secure configuration (Azure Key Vault, AWS Secrets Manager)
// ❌ DON'T: Store keys in appsettings.json in production

// ✅ DO: Use short expiry times for access tokens (5-60 minutes)
"ExpiryMinutes": 15

// ✅ DO: Use refresh tokens for long-lived sessions
"RefreshTokenExpiryDays": 7

// ✅ DO: Validate issuer and audience
ValidateIssuer = true,
ValidateAudience = true,

// ✅ DO: Use HTTPS in production
RequireHttpsMetadata = true

// ✅ DO: Set ClockSkew to zero for strict expiry
ClockSkew = TimeSpan.Zero

// ❌ DON'T: Store sensitive data in JWT payload (it's base64, not encrypted)
// JWT payload is readable by anyone

// ✅ DO: Revoke refresh tokens on logout
await _userRepository.RevokeRefreshTokenAsync(userId);

// ✅ DO: Implement token rotation (issue new refresh token on each refresh)
var newRefreshToken = _jwtService.GenerateRefreshToken();

// ✅ DO: Validate refresh tokens against database/cache
var isValid = await _userRepository.ValidateRefreshTokenAsync(userId, refreshToken);
```

---

## 🍪 Cookie Authentication

### When to Use Cookie vs JWT

```
Cookie Authentication:
✅ Traditional web apps (MVC, Razor Pages)
✅ Same domain/subdomain
✅ Browser-based sessions
✅ Built-in CSRF protection
❌ Not suitable for mobile apps
❌ Not suitable for cross-domain APIs

JWT Authentication:
✅ REST APIs
✅ Mobile apps
✅ Cross-domain requests
✅ Microservices
✅ Stateless authentication
❌ More complex to implement securely
❌ No built-in CSRF protection
```

### Cookie Authentication Implementation

```csharp
// Program.cs

builder.Services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.LoginPath = "/Account/Login";
        options.LogoutPath = "/Account/Logout";
        options.AccessDeniedPath = "/Account/AccessDenied";

        options.ExpireTimeSpan = TimeSpan.FromHours(1);
        options.SlidingExpiration = true; // Renew cookie if more than half expired

        options.Cookie.Name = "YourApp.Auth";
        options.Cookie.HttpOnly = true; // Prevent JavaScript access (XSS protection)
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always; // HTTPS only
        options.Cookie.SameSite = SameSiteMode.Strict; // CSRF protection

        options.Events = new CookieAuthenticationEvents
        {
            OnValidatePrincipal = async context =>
            {
                // Validate user is still active in database
                var userPrincipal = context.Principal;
                var userId = userPrincipal?.FindFirst(ClaimTypes.NameIdentifier)?.Value;

                if (!string.IsNullOrEmpty(userId))
                {
                    var userRepository = context.HttpContext
                        .RequestServices.GetRequiredService<IUserRepository>();
                    var user = await userRepository.GetByIdAsync(int.Parse(userId));

                    if (user == null || !user.IsActive)
                    {
                        context.RejectPrincipal();
                        await context.HttpContext.SignOutAsync(
                            CookieAuthenticationDefaults.AuthenticationScheme);
                    }
                }
            }
        };
    });
```

### Cookie Login/Logout

```csharp
[ApiController]
[Route("[controller]")]
public class AccountController : ControllerBase
{
    private readonly IUserRepository _userRepository;

    public AccountController(IUserRepository userRepository)
    {
        _userRepository = userRepository;
    }

    [HttpPost("login")]
    public async Task<IActionResult> Login([FromBody] LoginRequest request)
    {
        var user = await _userRepository.ValidateCredentialsAsync(
            request.Email, request.Password);

        if (user == null)
        {
            return Unauthorized();
        }

        var claims = new List<Claim>
        {
            new Claim(ClaimTypes.NameIdentifier, user.Id.ToString()),
            new Claim(ClaimTypes.Name, user.Email),
            new Claim(ClaimTypes.Email, user.Email),
        };

        // Add roles
        var roles = await _userRepository.GetUserRolesAsync(user.Id);
        foreach (var role in roles)
        {
            claims.Add(new Claim(ClaimTypes.Role, role));
        }

        var claimsIdentity = new ClaimsIdentity(claims,
            CookieAuthenticationDefaults.AuthenticationScheme);

        var authProperties = new AuthenticationProperties
        {
            IsPersistent = request.RememberMe,
            ExpiresUtc = request.RememberMe
                ? DateTimeOffset.UtcNow.AddDays(30)
                : DateTimeOffset.UtcNow.AddHours(1)
        };

        await HttpContext.SignInAsync(
            CookieAuthenticationDefaults.AuthenticationScheme,
            new ClaimsPrincipal(claimsIdentity),
            authProperties);

        return Ok(new { message = "Logged in successfully" });
    }

    [HttpPost("logout")]
    [Authorize]
    public async Task<IActionResult> Logout()
    {
        await HttpContext.SignOutAsync(
            CookieAuthenticationDefaults.AuthenticationScheme);

        return Ok(new { message = "Logged out successfully" });
    }
}
```

---

## 👤 ASP.NET Core Identity

### Complete Identity Setup

**Step 1: Install Packages**

```bash
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

**Step 2: Create Application User**

```csharp
public class ApplicationUser : IdentityUser<int> // Use int instead of string for Id
{
    public string FirstName { get; set; } = string.Empty;
    public string LastName { get; set; } = string.Empty;
    public DateTime CreatedAt { get; set; } = DateTime.UtcNow;
    public bool IsActive { get; set; } = true;

    // Navigation properties
    public ICollection<RefreshToken> RefreshTokens { get; set; } = new List<RefreshToken>();
}

public class RefreshToken
{
    public int Id { get; set; }
    public int UserId { get; set; }
    public string Token { get; set; } = string.Empty;
    public DateTime ExpiresAt { get; set; }
    public DateTime CreatedAt { get; set; } = DateTime.UtcNow;
    public bool IsRevoked { get; set; }

    public ApplicationUser User { get; set; } = null!;
}
```

**Step 3: Create DbContext**

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser, IdentityRole<int>, int>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    public DbSet<RefreshToken> RefreshTokens { get; set; }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);

        // Customize Identity table names (optional)
        builder.Entity<ApplicationUser>(entity =>
        {
            entity.ToTable("Users");
        });

        builder.Entity<IdentityRole<int>>(entity =>
        {
            entity.ToTable("Roles");
        });

        builder.Entity<IdentityUserRole<int>>(entity =>
        {
            entity.ToTable("UserRoles");
        });

        builder.Entity<IdentityUserClaim<int>>(entity =>
        {
            entity.ToTable("UserClaims");
        });

        builder.Entity<IdentityUserLogin<int>>(entity =>
        {
            entity.ToTable("UserLogins");
        });

        builder.Entity<IdentityRoleClaim<int>>(entity =>
        {
            entity.ToTable("RoleClaims");
        });

        builder.Entity<IdentityUserToken<int>>(entity =>
        {
            entity.ToTable("UserTokens");
        });

        // Seed roles
        builder.Entity<IdentityRole<int>>().HasData(
            new IdentityRole<int> { Id = 1, Name = "Admin", NormalizedName = "ADMIN" },
            new IdentityRole<int> { Id = 2, Name = "User", NormalizedName = "USER" },
            new IdentityRole<int> { Id = 3, Name = "Manager", NormalizedName = "MANAGER" }
        );
    }
}
```

**Step 4: Configure Identity in Program.cs**

```csharp
// Program.cs

var builder = WebApplication.CreateBuilder(args);

// Add DbContext
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));

// Configure Identity
builder.Services.AddIdentity<ApplicationUser, IdentityRole<int>>(options =>
{
    // Password settings
    options.Password.RequireDigit = true;
    options.Password.RequireLowercase = true;
    options.Password.RequireUppercase = true;
    options.Password.RequireNonAlphanumeric = true;
    options.Password.RequiredLength = 8;
    options.Password.RequiredUniqueChars = 1;

    // Lockout settings
    options.Lockout.DefaultLockoutTimeSpan = TimeSpan.FromMinutes(15);
    options.Lockout.MaxFailedAccessAttempts = 5;
    options.Lockout.AllowedForNewUsers = true;

    // User settings
    options.User.RequireUniqueEmail = true;

    // Sign-in settings
    options.SignIn.RequireConfirmedEmail = true;
    options.SignIn.RequireConfirmedAccount = false;
})
.AddEntityFrameworkStores<ApplicationDbContext>()
.AddDefaultTokenProviders();

// Configure JWT (combine with Identity)
builder.Services.Configure<JwtSettings>(builder.Configuration.GetSection("Jwt"));
builder.Services.AddScoped<IJwtService, JwtService>();

builder.Services.AddAuthentication(options =>
{
    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
})
.AddJwtBearer(options =>
{
    // JWT configuration (see JWT section)
});

var app = builder.Build();

app.UseAuthentication();
app.UseAuthorization();

app.MapControllers();
app.Run();
```

**Step 5: Run Migrations**

```bash
dotnet ef migrations add InitialIdentity
dotnet ef database update
```

**Step 6: Identity-Based Auth Controller**

```csharp
[ApiController]
[Route("api/[controller]")]
public class AuthController : ControllerBase
{
    private readonly UserManager<ApplicationUser> _userManager;
    private readonly SignInManager<ApplicationUser> _signInManager;
    private readonly IJwtService _jwtService;
    private readonly ApplicationDbContext _context;

    public AuthController(
        UserManager<ApplicationUser> userManager,
        SignInManager<ApplicationUser> signInManager,
        IJwtService jwtService,
        ApplicationDbContext context)
    {
        _userManager = userManager;
        _signInManager = signInManager;
        _jwtService = jwtService;
        _context = context;
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

        // Add to default role
        await _userManager.AddToRoleAsync(user, "User");

        // Generate email confirmation token
        var emailToken = await _userManager.GenerateEmailConfirmationTokenAsync(user);

        // TODO: Send confirmation email

        return Ok(new { message = "User registered successfully", userId = user.Id });
    }

    [HttpPost("login")]
    public async Task<IActionResult> Login([FromBody] LoginRequest request)
    {
        var user = await _userManager.FindByEmailAsync(request.Email);

        if (user == null)
        {
            return Unauthorized(new { message = "Invalid credentials" });
        }

        if (!user.IsActive)
        {
            return Unauthorized(new { message = "Account is disabled" });
        }

        var result = await _signInManager.CheckPasswordSignInAsync(
            user, request.Password, lockoutOnFailure: true);

        if (!result.Succeeded)
        {
            if (result.IsLockedOut)
            {
                return Unauthorized(new { message = "Account locked out" });
            }

            if (result.RequiresTwoFactor)
            {
                return Ok(new { requiresTwoFactor = true });
            }

            return Unauthorized(new { message = "Invalid credentials" });
        }

        // Get user roles
        var roles = await _userManager.GetRolesAsync(user);

        // Generate JWT
        var accessToken = _jwtService.GenerateToken(
            user.Id.ToString(), user.Email!, roles);
        var refreshToken = _jwtService.GenerateRefreshToken();

        // Save refresh token
        _context.RefreshTokens.Add(new RefreshToken
        {
            UserId = user.Id,
            Token = refreshToken,
            ExpiresAt = DateTime.UtcNow.AddDays(7)
        });
        await _context.SaveChangesAsync();

        return Ok(new
        {
            AccessToken = accessToken,
            RefreshToken = refreshToken,
            ExpiresIn = 3600,
            User = new
            {
                user.Id,
                user.Email,
                user.FirstName,
                user.LastName,
                Roles = roles
            }
        });
    }

    [HttpPost("confirm-email")]
    public async Task<IActionResult> ConfirmEmail([FromBody] ConfirmEmailRequest request)
    {
        var user = await _userManager.FindByIdAsync(request.UserId.ToString());

        if (user == null)
        {
            return NotFound();
        }

        var result = await _userManager.ConfirmEmailAsync(user, request.Token);

        if (!result.Succeeded)
        {
            return BadRequest(new { errors = result.Errors });
        }

        return Ok(new { message = "Email confirmed successfully" });
    }

    [HttpPost("forgot-password")]
    public async Task<IActionResult> ForgotPassword([FromBody] ForgotPasswordRequest request)
    {
        var user = await _userManager.FindByEmailAsync(request.Email);

        if (user == null)
        {
            // Don't reveal that user doesn't exist
            return Ok(new { message = "Password reset email sent if account exists" });
        }

        var token = await _userManager.GeneratePasswordResetTokenAsync(user);

        // TODO: Send password reset email

        return Ok(new { message = "Password reset email sent if account exists" });
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

        return Ok(new { message = "Password reset successfully" });
    }

    [HttpPost("change-password")]
    [Authorize]
    public async Task<IActionResult> ChangePassword([FromBody] ChangePasswordRequest request)
    {
        var userId = User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
        var user = await _userManager.FindByIdAsync(userId!);

        if (user == null)
        {
            return NotFound();
        }

        var result = await _userManager.ChangePasswordAsync(
            user, request.CurrentPassword, request.NewPassword);

        if (!result.Succeeded)
        {
            return BadRequest(new { errors = result.Errors });
        }

        return Ok(new { message = "Password changed successfully" });
    }
}

public record RegisterRequest(
    string Email,
    string Password,
    string FirstName,
    string LastName);

public record LoginRequest(string Email, string Password);
public record ConfirmEmailRequest(int UserId, string Token);
public record ForgotPasswordRequest(string Email);
public record ResetPasswordRequest(string Email, string Token, string NewPassword);
public record ChangePasswordRequest(string CurrentPassword, string NewPassword);
```

---

*This is Part 1 of the guide. The file is getting long. Should I continue with:*
- *Part 2: OAuth 2.0, OpenID Connect*
- *Part 3: Authorization (RBAC, Claims, Policies, Resource-based)*
- *Part 4: Security Best Practices, Real-World Scenarios, Troubleshooting*

*Or would you like me to complete it all in one file?*
