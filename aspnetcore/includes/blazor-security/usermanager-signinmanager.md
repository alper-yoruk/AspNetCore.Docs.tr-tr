## <a name="usermanager-and-signinmanager"></a>UserManager ve SignInManager

Bir sunucu uygulaması gerektirdiğinde Kullanıcı tanımlayıcısı talep türünü ayarlayın:

* <xref:Microsoft.AspNetCore.Identity.UserManager%601>ya <xref:Microsoft.AspNetCore.Identity.SignInManager%601> da bir API uç noktasında.
* <xref:Microsoft.AspNetCore.Identity.IdentityUser>kullanıcının adı, e-posta adresi veya kilitleme bitiş saati gibi ayrıntılar.

`Startup.ConfigureServices` içinde:

```csharp
services.Configure<IdentityOptions>(options => 
    options.ClaimsIdentity.UserIdClaimType = ClaimTypes.NameIdentifier);
```

Aşağıdaki `WeatherForecastController` , `Get` yöntemi çağrıldığında <xref:Microsoft.AspNetCore.Identity.IdentityUser%601.UserName> günlüğe kaydedilir:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Logging;
using {APP NAMESPACE}.Server.Models;
using {APP NAMESPACE}.Shared;

namespace {APP NAMESPACE}.Server.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        private readonly UserManager<ApplicationUser> _userManager;

        private static readonly string[] Summaries = new[]
        {
            "Freezing", "Bracing", "Chilly", "Cool", "Mild", "Warm", 
            "Balmy", "Hot", "Sweltering", "Scorching"
        };

        private readonly ILogger<WeatherForecastController> logger;

        public WeatherForecastController(ILogger<WeatherForecastController> logger, 
            UserManager<ApplicationUser> userManager)
        {
            this.logger = logger;
            _userManager = userManager;
        }

        [HttpGet]
        public async Task<IEnumerable<WeatherForecast>> Get()
        {
            var rng = new Random();

            var user = await _userManager.GetUserAsync(User);

            if (user != null)
            {
                logger.LogInformation($"User.Identity.Name: {user.UserName}");
            }

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = DateTime.Now.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = Summaries[rng.Next(Summaries.Length)]
            })
            .ToArray();
        }
    }
}
```
