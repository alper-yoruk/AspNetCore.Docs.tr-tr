---
<span data-ttu-id="d49b0-101">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Author: guardrex açıklaması: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="d49b0-101">title: 'ASP.NET Core Blazor hosting model configuration' author: guardrex description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="d49b0-102">monikerRange: ' >= aspnetcore-3,1 ' MS. Author: Riande MS. Custom: MVC MS. Date: 05/28/2020 No-loc:</span><span class="sxs-lookup"><span data-stu-id="d49b0-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/28/2020 no-loc:</span></span>
- <span data-ttu-id="d49b0-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d49b0-103">'Blazor'</span></span>
- <span data-ttu-id="d49b0-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d49b0-104">'Identity'</span></span>
- <span data-ttu-id="d49b0-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d49b0-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="d49b0-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d49b0-106">'Razor'</span></span>
- <span data-ttu-id="d49b0-107">' SignalR ' uid: blazor/Hosting-model-yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d49b0-107">'SignalR' uid: blazor/hosting-model-configuration</span></span>

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="d49b0-108">ASP.NET Core Blazor barındırma modeli yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d49b0-108">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="d49b0-109">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="d49b0-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d49b0-110">Bu makalede barındırma modeli yapılandırması ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d49b0-110">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="d49b0-111">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="d49b0-111"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="d49b0-112">Ortam</span><span class="sxs-lookup"><span data-stu-id="d49b0-112">Environment</span></span>

<span data-ttu-id="d49b0-113">Bir uygulamayı yerel olarak çalıştırırken, ortam varsayılan olarak geliştirme aşamasındadır.</span><span class="sxs-lookup"><span data-stu-id="d49b0-113">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="d49b0-114">Uygulama yayımlandığında, ortam varsayılan olarak üretim olur.</span><span class="sxs-lookup"><span data-stu-id="d49b0-114">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="d49b0-115">Barındırılan Blazor webassembly uygulaması, üst bilgiyi ekleyerek ortamı tarayıcıya ileten bir ara yazılım aracılığıyla sunucudan alır `blazor-environment` .</span><span class="sxs-lookup"><span data-stu-id="d49b0-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="d49b0-116">Üstbilginin değeri ortamıdır.</span><span class="sxs-lookup"><span data-stu-id="d49b0-116">The value of the header is the environment.</span></span> <span data-ttu-id="d49b0-117">Barındırılan Blazor uygulama ve sunucu uygulaması aynı ortamı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="d49b0-117">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="d49b0-118">Ortamın nasıl yapılandırılacağı dahil olmak üzere daha fazla bilgi için bkz <xref:fundamentals/environments> ..</span><span class="sxs-lookup"><span data-stu-id="d49b0-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d49b0-119">Yerel olarak çalışan tek başına bir uygulama için geliştirme sunucusu, `blazor-environment` geliştirme ortamını belirtmek için üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="d49b0-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="d49b0-120">Diğer barındırma ortamlarının ortamını belirtmek için `blazor-environment` üst bilgiyi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d49b0-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="d49b0-121">IIS için aşağıdaki örnekte, yayınlanan *Web. config* dosyasına özel üstbilgiyi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d49b0-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="d49b0-122">*Web. config* dosyası *bin/Release/{Target Framework}/Publish* klasöründe bulunur:</span><span class="sxs-lookup"><span data-stu-id="d49b0-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> <span data-ttu-id="d49b0-123">Uygulama *Yayımlama* klasörüne yayımlandığında üzerine YAZıLMAYAN IIS için özel *Web. config* dosyasını kullanmak için, bkz <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> ..</span><span class="sxs-lookup"><span data-stu-id="d49b0-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="d49b0-124">Ekleme tarafından bir bileşende uygulamanın ortamını edinin <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> ve <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> özelliği okuyun:</span><span class="sxs-lookup"><span data-stu-id="d49b0-124">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="d49b0-125">Başlangıç sırasında, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> geliştiricilerin kendi kodlarında ortama özgü mantığa sahip olmasını sağlayan özelliği aracılığıyla kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="d49b0-125">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="d49b0-126">Aşağıdaki kullanışlı uzantı yöntemleri, geliştirme, üretim, hazırlama ve özel ortam adları için geçerli ortamı denetlemeye izin verir:</span><span class="sxs-lookup"><span data-stu-id="d49b0-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

<span data-ttu-id="d49b0-127"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>Özelliği, hizmet kullanılamadığında başlatma sırasında kullanılabilir <xref:Microsoft.AspNetCore.Components.NavigationManager> .</span><span class="sxs-lookup"><span data-stu-id="d49b0-127">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="d49b0-128">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d49b0-128">Configuration</span></span>

Blazor<span data-ttu-id="d49b0-129">WebAssembly yapılandırmayı şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="d49b0-129"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="d49b0-130">Uygulama ayarları dosyaları varsayılan olarak:</span><span class="sxs-lookup"><span data-stu-id="d49b0-130">App settings files by default:</span></span>
  * <span data-ttu-id="d49b0-131">*Wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="d49b0-131">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="d49b0-132">*Wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="d49b0-132">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="d49b0-133">Uygulama tarafından kaydedilen diğer [yapılandırma sağlayıcıları](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="d49b0-133">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="d49b0-134">Tüm sağlayıcılar Blazor webassembly uygulamalarına uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="d49b0-134">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="d49b0-135">Wezsembly için desteklenen sağlayıcıların açıklanması Blazor , [ Blazor ısstream (DotNet/Aspnetcore. docs #18134) Için yapılandırma sağlayıcıları açıklığa kavuşturarak](https://github.com/dotnet/AspNetCore.Docs/issues/18134)izlenir.</span><span class="sxs-lookup"><span data-stu-id="d49b0-135">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="d49b0-136">BlazorWebassembly uygulamasındaki yapılandırma kullanıcılar tarafından görülebilir.</span><span class="sxs-lookup"><span data-stu-id="d49b0-136">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="d49b0-137">**Yapılandırma bölümünde uygulama gizli dizilerini veya kimlik bilgilerini depolamamayın.**</span><span class="sxs-lookup"><span data-stu-id="d49b0-137">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="d49b0-138">Yapılandırma sağlayıcıları hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/index> ..</span><span class="sxs-lookup"><span data-stu-id="d49b0-138">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="d49b0-139">Uygulama ayarları yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d49b0-139">App settings configuration</span></span>

<span data-ttu-id="d49b0-140">*Wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="d49b0-140">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="d49b0-141"><xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bileşene örnek ekleme:</span><span class="sxs-lookup"><span data-stu-id="d49b0-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="d49b0-142">Sağlayıcı yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d49b0-142">Provider configuration</span></span>

<span data-ttu-id="d49b0-143">Aşağıdaki örnek, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> ek yapılandırma sağlamak için bir kullanır:</span><span class="sxs-lookup"><span data-stu-id="d49b0-143">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="d49b0-144">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="d49b0-144">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;

...

var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

...

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="d49b0-145"><xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bileşene örnek ekleme:</span><span class="sxs-lookup"><span data-stu-id="d49b0-145">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

<span data-ttu-id="d49b0-146">*Wwwroot* klasöründeki diğer yapılandırma dosyalarını yapılandırmaya okumak için <xref:System.Net.Http.HttpClient> dosyanın içeriğini almak üzere bir kullanın.</span><span class="sxs-lookup"><span data-stu-id="d49b0-146">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="d49b0-147">Bu yaklaşım kullanıldığında, mevcut <xref:System.Net.Http.HttpClient> hizmet kaydı, aşağıdaki örnekte gösterildiği gibi, dosyayı okumak için oluşturulan yerel istemciyi kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="d49b0-147">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="d49b0-148">*Wwwroot/otomobil. JSON*:</span><span class="sxs-lookup"><span data-stu-id="d49b0-148">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="d49b0-149">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="d49b0-149">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddTransient(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

#### <a name="authentication-configuration"></a><span data-ttu-id="d49b0-150">Kimlik doğrulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d49b0-150">Authentication configuration</span></span>

<span data-ttu-id="d49b0-151">*Wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="d49b0-151">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="d49b0-152">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="d49b0-152">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions);
```

#### <a name="logging-configuration"></a><span data-ttu-id="d49b0-153">Günlüğe kaydetme yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d49b0-153">Logging configuration</span></span>

<span data-ttu-id="d49b0-154">[Microsoft. Extensions. Logging. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/)için bir paket başvurusu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d49b0-154">Add a package reference for [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="d49b0-155">*Wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="d49b0-155">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

<span data-ttu-id="d49b0-156">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="d49b0-156">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="d49b0-157">Konak Oluşturucu yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d49b0-157">Host builder configuration</span></span>

<span data-ttu-id="d49b0-158">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="d49b0-158">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="d49b0-159">Önbelleğe alınmış yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d49b0-159">Cached configuration</span></span>

<span data-ttu-id="d49b0-160">Yapılandırma dosyaları çevrimdışı kullanım için önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="d49b0-160">Configuration files are cached for offline use.</span></span> <span data-ttu-id="d49b0-161">[Aşamalı Web uygulamaları (PWAs)](xref:blazor/progressive-web-app)ile, yalnızca yeni bir dağıtım oluştururken yapılandırma dosyalarını güncelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d49b0-161">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="d49b0-162">Yapılandırma dosyalarının dağıtımlar arasında düzenlenmesinin hiçbir etkisi yoktur çünkü:</span><span class="sxs-lookup"><span data-stu-id="d49b0-162">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="d49b0-163">Kullanıcıların, kullanmaya devam ettikleri dosyaların önbelleğe alınmış sürümleri vardır.</span><span class="sxs-lookup"><span data-stu-id="d49b0-163">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="d49b0-164">PWA 'nın *Service-Worker. js* ve *Service-Worker-assets. js* dosyalarının derlemede yeniden oluşturulması gerekir. Bu, kullanıcının bir sonraki çevrimiçi sitesinde uygulamaya işaret eden uygulamanın yeniden dağıtıldığını belirten, derleme üzerinde yeniden oluşturulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d49b0-164">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="d49b0-165">Arka plan güncelleştirmelerinin PWAs tarafından nasıl işlendiği hakkında daha fazla bilgi için bkz <xref:blazor/progressive-web-app#background-updates> ..</span><span class="sxs-lookup"><span data-stu-id="d49b0-165">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="d49b0-166">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="d49b0-166">Logging</span></span>

<span data-ttu-id="d49b0-167">BlazorWebassembly günlüğü desteği hakkında bilgi için bkz <xref:fundamentals/logging/index#create-logs-in-blazor> ..</span><span class="sxs-lookup"><span data-stu-id="d49b0-167">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="d49b0-168">Server</span><span class="sxs-lookup"><span data-stu-id="d49b0-168"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="d49b0-169">Kullanıcı arabirimindeki bağlantı durumunu yansıtır</span><span class="sxs-lookup"><span data-stu-id="d49b0-169">Reflect the connection state in the UI</span></span>

<span data-ttu-id="d49b0-170">İstemci bağlantının kaybolduğunu algıladığında, istemci yeniden bağlanmayı denediğinde kullanıcıya varsayılan bir kullanıcı arabirimi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="d49b0-170">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="d49b0-171">Yeniden bağlantı başarısız olursa, kullanıcıya yeniden deneme seçeneği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="d49b0-171">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="d49b0-172">Kullanıcı arabirimini özelleştirmek için, `id` `components-reconnect-modal` `<body>` *_Host. cshtml* sayfasında öğesinin içeren bir öğesi tanımlayın Razor :</span><span class="sxs-lookup"><span data-stu-id="d49b0-172">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="d49b0-173">Aşağıdaki tabloda öğesine uygulanan CSS sınıfları açıklanmaktadır `components-reconnect-modal` .</span><span class="sxs-lookup"><span data-stu-id="d49b0-173">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="d49b0-174">CSS sınıfı</span><span class="sxs-lookup"><span data-stu-id="d49b0-174">CSS class</span></span>                       | <span data-ttu-id="d49b0-175">Bildiren&hellip;</span><span class="sxs-lookup"><span data-stu-id="d49b0-175">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="d49b0-176">Kayıp bir bağlantı.</span><span class="sxs-lookup"><span data-stu-id="d49b0-176">A lost connection.</span></span> <span data-ttu-id="d49b0-177">İstemci yeniden bağlanmaya çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="d49b0-177">The client is attempting to reconnect.</span></span> <span data-ttu-id="d49b0-178">Kalıcı olarak göster.</span><span class="sxs-lookup"><span data-stu-id="d49b0-178">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="d49b0-179">Etkin bir bağlantı sunucuya yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d49b0-179">An active connection is re-established to the server.</span></span> <span data-ttu-id="d49b0-180">Kalıcı olarak gizleyin.</span><span class="sxs-lookup"><span data-stu-id="d49b0-180">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="d49b0-181">Muhtemelen bir ağ hatasından dolayı yeniden bağlantı başarısız oldu.</span><span class="sxs-lookup"><span data-stu-id="d49b0-181">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="d49b0-182">Yeniden bağlanmayı denemek için çağrısı yapın `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="d49b0-182">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="d49b0-183">Yeniden bağlantı reddedildi.</span><span class="sxs-lookup"><span data-stu-id="d49b0-183">Reconnection rejected.</span></span> <span data-ttu-id="d49b0-184">Sunucuya ulaşıldı ancak bağlantı reddedildi ve kullanıcının sunucudaki durumu kayboldu.</span><span class="sxs-lookup"><span data-stu-id="d49b0-184">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="d49b0-185">Uygulamayı yeniden yüklemek için çağrısı yapın `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="d49b0-185">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="d49b0-186">Bu bağlantı durumu şu durumlarda oluşabilir:</span><span class="sxs-lookup"><span data-stu-id="d49b0-186">This connection state may result when:</span></span><ul><li><span data-ttu-id="d49b0-187">Sunucu tarafında devre dışı bir kilitlenme oluşur.</span><span class="sxs-lookup"><span data-stu-id="d49b0-187">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="d49b0-188">Sunucunun kullanıcının durumunu bırakması için istemcinin bağlantısı yeterince uzun değil.</span><span class="sxs-lookup"><span data-stu-id="d49b0-188">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="d49b0-189">Kullanıcının etkileşimde bulunduğu bileşenlerin örnekleri atıldı.</span><span class="sxs-lookup"><span data-stu-id="d49b0-189">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="d49b0-190">Sunucu yeniden başlatıldı veya uygulamanın çalışan işlemi geri dönüştürüldü.</span><span class="sxs-lookup"><span data-stu-id="d49b0-190">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="d49b0-191">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="d49b0-191">Render mode</span></span>

Blazor<span data-ttu-id="d49b0-192">Sunucu uygulamaları, sunucu bağlantısı kurumadan önce sunucudaki kullanıcı arabirimini varsayılan olarak PreRender 'a ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d49b0-192"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="d49b0-193">Bu, *_Host. cshtml* Razor sayfasında ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="d49b0-193">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="d49b0-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>bileşenin şunları yapıp kullanmadığını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="d49b0-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="d49b0-195">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="d49b0-195">Is prerendered into the page.</span></span>
* <span data-ttu-id="d49b0-196">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa Blazor .</span><span class="sxs-lookup"><span data-stu-id="d49b0-196">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="d49b0-197">Açıklama</span><span class="sxs-lookup"><span data-stu-id="d49b0-197">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="d49b0-198">Bileşeni statik HTML olarak işler ve sunucu uygulaması için bir işaret içerir Blazor .</span><span class="sxs-lookup"><span data-stu-id="d49b0-198">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="d49b0-199">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="d49b0-199">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="d49b0-200">Sunucu uygulaması için bir işaret oluşturur Blazor .</span><span class="sxs-lookup"><span data-stu-id="d49b0-200">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="d49b0-201">Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="d49b0-201">Output from the component isn't included.</span></span> <span data-ttu-id="d49b0-202">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="d49b0-202">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="d49b0-203">Bileşeni statik HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="d49b0-203">Renders the component into static HTML.</span></span> |

<span data-ttu-id="d49b0-204">Statik HTML sayfasından sunucu bileşenleri işleme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="d49b0-204">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="d49b0-205">SignalRSunucu uygulamaları için istemciyi Blazor yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d49b0-205">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="d49b0-206">Bazen, SignalR sunucu uygulamaları tarafından kullanılan istemciyi yapılandırmanız gerekir Blazor .</span><span class="sxs-lookup"><span data-stu-id="d49b0-206">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="d49b0-207">Örneğin, SignalR bir bağlantı sorununu tanılamak için istemcide günlüğe kaydetmeyi yapılandırmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d49b0-207">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="d49b0-208">SignalRİstemciyi, *Pages/_Host. cshtml* dosyasında yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="d49b0-208">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="d49b0-209">`autostart="false"`Betiğin etiketine bir öznitelik ekleyin `<script>` `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="d49b0-209">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="d49b0-210">`Blazor.start`Oluşturucuyu belirten bir yapılandırma nesnesi çağırın ve geçirin SignalR .</span><span class="sxs-lookup"><span data-stu-id="d49b0-210">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

### <a name="logging"></a><span data-ttu-id="d49b0-211">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="d49b0-211">Logging</span></span>

<span data-ttu-id="d49b0-212">BlazorSunucu günlüğü desteği hakkında bilgi için bkz <xref:fundamentals/logging/index#create-logs-in-blazor> ..</span><span class="sxs-lookup"><span data-stu-id="d49b0-212">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
