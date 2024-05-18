# ASP.NET Core Rate Limiting Example

This documentation describes how to set up and configure rate limiting in an ASP.NET Core application using the `AspNetCoreRateLimit` package.

## Prerequisites

Ensure you have the following installed:
- .NET SDK
- Visual Studio or any code editor
- NuGet packages: `AspNetCoreRateLimit`, `Microsoft.AspNetCore.Mvc`, `Swashbuckle.AspNetCore`

## Setup

### Configure Services

In `Program.cs`, configure the necessary services for rate limiting:

1. **Add required services**:
    ```csharp
    builder.Services.AddOptions();
    builder.Services.AddMemoryCache();
    ```

2. **Define rate limit rules**:
    Define the rules for rate limiting which specify how many requests are allowed in a given period. In this example, we limit all endpoints to 2 requests every 5 seconds. This helps prevent abuse and ensures fair use of your API.
    ```csharp
    var rateLimitRules = new List<RateLimitRule> { 
        new RateLimitRule
        {
            Endpoint = "*",
            Limit = 2,
            Period = "5s"
        }
    };
    ```

3. **Configure rate limiting options**:
    ```csharp
    builder.Services.Configure<IpRateLimitOptions>(opt => { 
        opt.GeneralRules = rateLimitRules;
    });
    ```

4. **Add rate limiting services**:
    ```csharp
    builder.Services.AddSingleton<IRateLimitCounterStore, MemoryCacheRateLimitCounterStore>();
    builder.Services.AddSingleton<IIpPolicyStore, MemoryCacheIpPolicyStore>();
    builder.Services.AddSingleton<IRateLimitConfiguration, RateLimitConfiguration>();
    builder.Services.AddSingleton<IProcessingStrategy, AsyncKeyLockProcessingStrategy>();
    ```

### Configure Middleware

In `Program.cs`, add the rate limiting middleware to the request pipeline:

1. **Add IP rate limiting middleware**:
    ```csharp
    app.UseIpRateLimiting();
    ```

2. **Additional middlewares** (optional but recommended):
    - HTTPS Redirection
    - Authorization
    - Swagger (for development)

## Example Usage

Create a controller to test the rate limiting:

```csharp
using Microsoft.AspNetCore.Mvc;

namespace WebApiRateLimiting.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        [HttpGet("/api/GetWeatherForecast")]
        public IEnumerable<WeatherForecast> Get()
        {
            // Example implementation
        }
    }
}
```

## Testing
Use Postman to test the rate limiting functionality. The screenshot below shows the response when the rate limit is exceeded.

![Screenshot](https://raw.githubusercontent.com/mehedihasan9339/WebApiRateLimiting/master/WebApiRateLimiting/RateLimit%20Test%20in%20Postman.png)

## Conclusion
This documentation outlines the setup and configuration of an ASP.NET Core application with rate limiting using the AspNetCoreRateLimit package. For more details on advanced configurations, refer to the [AspNetCoreRateLimit documentation](https://github.com/stefanprodan/AspNetCoreRateLimit)
.

