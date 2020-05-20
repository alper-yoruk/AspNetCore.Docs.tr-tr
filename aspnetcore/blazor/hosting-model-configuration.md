---
<span data-ttu-id="e69f6-101">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-101">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-103">'Blazor'</span></span>
- <span data-ttu-id="e69f6-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-104">'Identity'</span></span>
- <span data-ttu-id="e69f6-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-106">'Razor'</span></span>
- <span data-ttu-id="e69f6-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="e69f6-108">ASP.NET Core Blazor barındırma modeli yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e69f6-108">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="e69f6-109">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="e69f6-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e69f6-110">Bu makalede barındırma modeli yapılandırması ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e69f6-110">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="e69f6-111">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="e69f6-111"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="e69f6-112">Ortam</span><span class="sxs-lookup"><span data-stu-id="e69f6-112">Environment</span></span>

<span data-ttu-id="e69f6-113">Bir uygulamayı yerel olarak çalıştırırken, ortam varsayılan olarak geliştirme aşamasındadır.</span><span class="sxs-lookup"><span data-stu-id="e69f6-113">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="e69f6-114">Uygulama yayımlandığında, ortam varsayılan olarak üretim olur.</span><span class="sxs-lookup"><span data-stu-id="e69f6-114">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="e69f6-115">Barındırılan Blazor webassembly uygulaması, üst bilgiyi ekleyerek ortamı tarayıcıya ileten bir ara yazılım aracılığıyla sunucudan alır `blazor-environment` .</span><span class="sxs-lookup"><span data-stu-id="e69f6-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="e69f6-116">Üstbilginin değeri ortamıdır.</span><span class="sxs-lookup"><span data-stu-id="e69f6-116">The value of the header is the environment.</span></span> <span data-ttu-id="e69f6-117">Barındırılan Blazor uygulama ve sunucu uygulaması aynı ortamı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="e69f6-117">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="e69f6-118">Ortamın nasıl yapılandırılacağı dahil olmak üzere daha fazla bilgi için bkz <xref:fundamentals/environments> ..</span><span class="sxs-lookup"><span data-stu-id="e69f6-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="e69f6-119">Yerel olarak çalışan tek başına bir uygulama için geliştirme sunucusu, `blazor-environment` geliştirme ortamını belirtmek için üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="e69f6-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="e69f6-120">Diğer barındırma ortamlarının ortamını belirtmek için `blazor-environment` üst bilgiyi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e69f6-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="e69f6-121">IIS için aşağıdaki örnekte, yayınlanan *Web. config* dosyasına özel üstbilgiyi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e69f6-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="e69f6-122">*Web. config* dosyası *bin/Release/{Target Framework}/Publish* klasöründe bulunur:</span><span class="sxs-lookup"><span data-stu-id="e69f6-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="e69f6-123">Uygulama *Yayımlama* klasörüne yayımlandığında üzerine YAZıLMAYAN IIS için özel *Web. config* dosyasını kullanmak için, bkz <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> ..</span><span class="sxs-lookup"><span data-stu-id="e69f6-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="e69f6-124">Ekleme tarafından bir bileşende uygulamanın ortamını edinin `IWebAssemblyHostEnvironment` ve `Environment` özelliği okuyun:</span><span class="sxs-lookup"><span data-stu-id="e69f6-124">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="e69f6-125">Başlangıç sırasında, `WebAssemblyHostBuilder` `IWebAssemblyHostEnvironment` `HostEnvironment` geliştiricilerin kendi kodlarında ortama özgü mantığa sahip olmasını sağlayan özelliği aracılığıyla kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="e69f6-125">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="e69f6-126">Aşağıdaki kullanışlı uzantı yöntemleri, geliştirme, üretim, hazırlama ve özel ortam adları için geçerli ortamı denetlemeye izin verir:</span><span class="sxs-lookup"><span data-stu-id="e69f6-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="e69f6-127">' Isenvironment ("{ortam adı}")</span><span class="sxs-lookup"><span data-stu-id="e69f6-127">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

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

<span data-ttu-id="e69f6-128">`IWebAssemblyHostEnvironment.BaseAddress`Özelliği, hizmet kullanılamadığında başlatma sırasında kullanılabilir `NavigationManager` .</span><span class="sxs-lookup"><span data-stu-id="e69f6-128">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="e69f6-129">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e69f6-129">Configuration</span></span>

Blazor<span data-ttu-id="e69f6-130">WebAssembly yapılandırmayı şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="e69f6-130"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="e69f6-131">Uygulama ayarları dosyaları varsayılan olarak:</span><span class="sxs-lookup"><span data-stu-id="e69f6-131">App settings files by default:</span></span>
  * <span data-ttu-id="e69f6-132">*Wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="e69f6-132">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="e69f6-133">*Wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="e69f6-133">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="e69f6-134">Uygulama tarafından kaydedilen diğer [yapılandırma sağlayıcıları](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="e69f6-134">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="e69f6-135">Tüm sağlayıcılar Blazor webassembly uygulamalarına uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="e69f6-135">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="e69f6-136">Wezsembly için desteklenen sağlayıcıların açıklanması Blazor , [ Blazor ısstream (DotNet/Aspnetcore. docs #18134) Için yapılandırma sağlayıcıları açıklığa kavuşturarak](https://github.com/dotnet/AspNetCore.Docs/issues/18134)izlenir.</span><span class="sxs-lookup"><span data-stu-id="e69f6-136">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="e69f6-137">BlazorWebassembly uygulamasındaki yapılandırma kullanıcılar tarafından görülebilir.</span><span class="sxs-lookup"><span data-stu-id="e69f6-137">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="e69f6-138">**Yapılandırma bölümünde uygulama gizli dizilerini veya kimlik bilgilerini depolamamayın.**</span><span class="sxs-lookup"><span data-stu-id="e69f6-138">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="e69f6-139">Yapılandırma sağlayıcıları hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/index> ..</span><span class="sxs-lookup"><span data-stu-id="e69f6-139">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="e69f6-140">Uygulama ayarları yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e69f6-140">App settings configuration</span></span>

<span data-ttu-id="e69f6-141">*Wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e69f6-141">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="e69f6-142"><xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bileşene örnek ekleme:</span><span class="sxs-lookup"><span data-stu-id="e69f6-142">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="e69f6-143">Sağlayıcı yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e69f6-143">Provider configuration</span></span>

<span data-ttu-id="e69f6-144">Aşağıdaki örnek, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> ek yapılandırma sağlamak için bir kullanır:</span><span class="sxs-lookup"><span data-stu-id="e69f6-144">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="e69f6-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e69f6-145">`Program.Main`:</span></span>

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

<span data-ttu-id="e69f6-146"><xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bileşene örnek ekleme:</span><span class="sxs-lookup"><span data-stu-id="e69f6-146">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="e69f6-147">*Wwwroot* klasöründeki diğer yapılandırma dosyalarını yapılandırmaya okumak için `HttpClient` dosyanın içeriğini almak üzere bir kullanın.</span><span class="sxs-lookup"><span data-stu-id="e69f6-147">To read other configuration files from the *wwwroot* folder into configuration, use an `HttpClient` to obtain the file's content.</span></span> <span data-ttu-id="e69f6-148">Bu yaklaşım kullanıldığında, mevcut `HttpClient` hizmet kaydı, aşağıdaki örnekte gösterildiği gibi, dosyayı okumak için oluşturulan yerel istemciyi kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="e69f6-148">When using this approach, the existing `HttpClient` service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="e69f6-149">*Wwwroot/otomobil. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e69f6-149">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="e69f6-150">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e69f6-150">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="e69f6-151">Kimlik doğrulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e69f6-151">Authentication configuration</span></span>

<span data-ttu-id="e69f6-152">*Wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e69f6-152">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="e69f6-153">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e69f6-153">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="e69f6-154">Günlüğe kaydetme yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e69f6-154">Logging configuration</span></span>

<span data-ttu-id="e69f6-155">*Wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e69f6-155">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="e69f6-156">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e69f6-156">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="e69f6-157">Konak Oluşturucu yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e69f6-157">Host builder configuration</span></span>

<span data-ttu-id="e69f6-158">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e69f6-158">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="e69f6-159">Önbelleğe alınmış yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e69f6-159">Cached configuration</span></span>

<span data-ttu-id="e69f6-160">Yapılandırma dosyaları çevrimdışı kullanım için önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="e69f6-160">Configuration files are cached for offline use.</span></span> <span data-ttu-id="e69f6-161">[Aşamalı Web uygulamaları (PWAs)](xref:blazor/progressive-web-app)ile, yalnızca yeni bir dağıtım oluştururken yapılandırma dosyalarını güncelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e69f6-161">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="e69f6-162">Yapılandırma dosyalarının dağıtımlar arasında düzenlenmesinin hiçbir etkisi yoktur çünkü:</span><span class="sxs-lookup"><span data-stu-id="e69f6-162">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="e69f6-163">Kullanıcıların, kullanmaya devam ettikleri dosyaların önbelleğe alınmış sürümleri vardır.</span><span class="sxs-lookup"><span data-stu-id="e69f6-163">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="e69f6-164">PWA 'nın *Service-Worker. js* ve *Service-Worker-assets. js* dosyalarının derlemede yeniden oluşturulması gerekir. Bu, kullanıcının bir sonraki çevrimiçi sitesinde uygulamaya işaret eden uygulamanın yeniden dağıtıldığını belirten, derleme üzerinde yeniden oluşturulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e69f6-164">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="e69f6-165">Arka plan güncelleştirmelerinin PWAs tarafından nasıl işlendiği hakkında daha fazla bilgi için bkz <xref:blazor/progressive-web-app#background-updates> ..</span><span class="sxs-lookup"><span data-stu-id="e69f6-165">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="e69f6-166">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="e69f6-166">Logging</span></span>

<span data-ttu-id="e69f6-167">BlazorWebassembly günlüğü desteği hakkında bilgi için bkz <xref:fundamentals/logging/index#create-logs-in-blazor> ..</span><span class="sxs-lookup"><span data-stu-id="e69f6-167">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="e69f6-168">Server</span><span class="sxs-lookup"><span data-stu-id="e69f6-168"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="e69f6-169">Kullanıcı arabirimindeki bağlantı durumunu yansıtır</span><span class="sxs-lookup"><span data-stu-id="e69f6-169">Reflect the connection state in the UI</span></span>

<span data-ttu-id="e69f6-170">İstemci bağlantının kaybolduğunu algıladığında, istemci yeniden bağlanmayı denediğinde kullanıcıya varsayılan bir kullanıcı arabirimi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="e69f6-170">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="e69f6-171">Yeniden bağlantı başarısız olursa, kullanıcıya yeniden deneme seçeneği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="e69f6-171">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="e69f6-172">Kullanıcı arabirimini özelleştirmek için, `id` `components-reconnect-modal` `<body>` *_Host. cshtml* sayfasında öğesinin içeren bir öğesi tanımlayın Razor :</span><span class="sxs-lookup"><span data-stu-id="e69f6-172">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="e69f6-173">Aşağıdaki tabloda öğesine uygulanan CSS sınıfları açıklanmaktadır `components-reconnect-modal` .</span><span class="sxs-lookup"><span data-stu-id="e69f6-173">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="e69f6-174">CSS sınıfı</span><span class="sxs-lookup"><span data-stu-id="e69f6-174">CSS class</span></span>                       | <span data-ttu-id="e69f6-175">Bildiren&hellip;</span><span class="sxs-lookup"><span data-stu-id="e69f6-175">Indicates&hellip;</span></span> |
| ---
<span data-ttu-id="e69f6-176">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-176">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-177">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-178">'Blazor'</span></span>
- <span data-ttu-id="e69f6-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-179">'Identity'</span></span>
- <span data-ttu-id="e69f6-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-181">'Razor'</span></span>
- <span data-ttu-id="e69f6-182">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-183">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-183">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-184">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-185">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-185">'Blazor'</span></span>
- <span data-ttu-id="e69f6-186">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-186">'Identity'</span></span>
- <span data-ttu-id="e69f6-187">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-187">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-188">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-188">'Razor'</span></span>
- <span data-ttu-id="e69f6-189">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-189">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-190">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-190">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-191">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-192">'Blazor'</span></span>
- <span data-ttu-id="e69f6-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-193">'Identity'</span></span>
- <span data-ttu-id="e69f6-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-195">'Razor'</span></span>
- <span data-ttu-id="e69f6-196">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-197">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-197">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-198">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-199">'Blazor'</span></span>
- <span data-ttu-id="e69f6-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-200">'Identity'</span></span>
- <span data-ttu-id="e69f6-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-202">'Razor'</span></span>
- <span data-ttu-id="e69f6-203">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-204">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-204">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-205">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-205">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-206">'Blazor'</span></span>
- <span data-ttu-id="e69f6-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-207">'Identity'</span></span>
- <span data-ttu-id="e69f6-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-209">'Razor'</span></span>
- <span data-ttu-id="e69f6-210">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-211">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-211">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-212">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-213">'Blazor'</span></span>
- <span data-ttu-id="e69f6-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-214">'Identity'</span></span>
- <span data-ttu-id="e69f6-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-216">'Razor'</span></span>
- <span data-ttu-id="e69f6-217">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-218">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-218">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-219">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-220">'Blazor'</span></span>
- <span data-ttu-id="e69f6-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-221">'Identity'</span></span>
- <span data-ttu-id="e69f6-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-223">'Razor'</span></span>
- <span data-ttu-id="e69f6-224">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-225">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-225">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-226">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-227">'Blazor'</span></span>
- <span data-ttu-id="e69f6-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-228">'Identity'</span></span>
- <span data-ttu-id="e69f6-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-230">'Razor'</span></span>
- <span data-ttu-id="e69f6-231">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-231">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-232">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-232">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-233">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-234">'Blazor'</span></span>
- <span data-ttu-id="e69f6-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-235">'Identity'</span></span>
- <span data-ttu-id="e69f6-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-237">'Razor'</span></span>
- <span data-ttu-id="e69f6-238">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-239">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-239">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-240">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-241">'Blazor'</span></span>
- <span data-ttu-id="e69f6-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-242">'Identity'</span></span>
- <span data-ttu-id="e69f6-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-244">'Razor'</span></span>
- <span data-ttu-id="e69f6-245">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-246">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-246">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-247">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-248">'Blazor'</span></span>
- <span data-ttu-id="e69f6-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-249">'Identity'</span></span>
- <span data-ttu-id="e69f6-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-251">'Razor'</span></span>
- <span data-ttu-id="e69f6-252">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-253">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-253">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-254">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-254">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-255">'Blazor'</span></span>
- <span data-ttu-id="e69f6-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-256">'Identity'</span></span>
- <span data-ttu-id="e69f6-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-258">'Razor'</span></span>
- <span data-ttu-id="e69f6-259">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-260">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-260">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-261">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-261">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-262">'Blazor'</span></span>
- <span data-ttu-id="e69f6-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-263">'Identity'</span></span>
- <span data-ttu-id="e69f6-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-265">'Razor'</span></span>
- <span data-ttu-id="e69f6-266">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-266">'SignalR' uid:</span></span> 

<span data-ttu-id="e69f6-267">---------------- | ---title: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-267">---------------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-268">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-268">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-269">'Blazor'</span></span>
- <span data-ttu-id="e69f6-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-270">'Identity'</span></span>
- <span data-ttu-id="e69f6-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-272">'Razor'</span></span>
- <span data-ttu-id="e69f6-273">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-274">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-274">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-275">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-275">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-276">'Blazor'</span></span>
- <span data-ttu-id="e69f6-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-277">'Identity'</span></span>
- <span data-ttu-id="e69f6-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-279">'Razor'</span></span>
- <span data-ttu-id="e69f6-280">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-281">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-281">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-282">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-282">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-283">'Blazor'</span></span>
- <span data-ttu-id="e69f6-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-284">'Identity'</span></span>
- <span data-ttu-id="e69f6-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-286">'Razor'</span></span>
- <span data-ttu-id="e69f6-287">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-288">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-288">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-289">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-289">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-290">'Blazor'</span></span>
- <span data-ttu-id="e69f6-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-291">'Identity'</span></span>
- <span data-ttu-id="e69f6-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-293">'Razor'</span></span>
- <span data-ttu-id="e69f6-294">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-295">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-295">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-296">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-296">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-297">'Blazor'</span></span>
- <span data-ttu-id="e69f6-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-298">'Identity'</span></span>
- <span data-ttu-id="e69f6-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-300">'Razor'</span></span>
- <span data-ttu-id="e69f6-301">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-302">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-302">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-303">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-303">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-304">'Blazor'</span></span>
- <span data-ttu-id="e69f6-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-305">'Identity'</span></span>
- <span data-ttu-id="e69f6-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-307">'Razor'</span></span>
- <span data-ttu-id="e69f6-308">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-308">'SignalR' uid:</span></span> 

<span data-ttu-id="e69f6-309">--------- | | `components-reconnect-show`     | Kayıp bir bağlantı.</span><span class="sxs-lookup"><span data-stu-id="e69f6-309">--------- | | `components-reconnect-show`     | A lost connection.</span></span> <span data-ttu-id="e69f6-310">İstemci yeniden bağlanmaya çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="e69f6-310">The client is attempting to reconnect.</span></span> <span data-ttu-id="e69f6-311">Kalıcı olarak göster.</span><span class="sxs-lookup"><span data-stu-id="e69f6-311">Show the modal.</span></span> <span data-ttu-id="e69f6-312">| | `components-reconnect-hide`     | Etkin bir bağlantı sunucuya yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e69f6-312">| | `components-reconnect-hide`     | An active connection is re-established to the server.</span></span> <span data-ttu-id="e69f6-313">Kalıcı olarak gizleyin.</span><span class="sxs-lookup"><span data-stu-id="e69f6-313">Hide the modal.</span></span> <span data-ttu-id="e69f6-314">| | `components-reconnect-failed`   | Muhtemelen bir ağ hatasından dolayı yeniden bağlantı başarısız oldu.</span><span class="sxs-lookup"><span data-stu-id="e69f6-314">| | `components-reconnect-failed`   | Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="e69f6-315">Yeniden bağlanmayı denemek için çağrısı yapın `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="e69f6-315">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> <span data-ttu-id="e69f6-316">| | `components-reconnect-rejected` | Yeniden bağlantı reddedildi.</span><span class="sxs-lookup"><span data-stu-id="e69f6-316">| | `components-reconnect-rejected` | Reconnection rejected.</span></span> <span data-ttu-id="e69f6-317">Sunucuya ulaşıldı ancak bağlantı reddedildi ve kullanıcının sunucudaki durumu kayboldu.</span><span class="sxs-lookup"><span data-stu-id="e69f6-317">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="e69f6-318">Uygulamayı yeniden yüklemek için çağrısı yapın `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="e69f6-318">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="e69f6-319">Bu bağlantı durumu şu durumlarda oluşabilir:</span><span class="sxs-lookup"><span data-stu-id="e69f6-319">This connection state may result when:</span></span><ul><li><span data-ttu-id="e69f6-320">Sunucu tarafında devre dışı bir kilitlenme oluşur.</span><span class="sxs-lookup"><span data-stu-id="e69f6-320">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="e69f6-321">Sunucunun kullanıcının durumunu bırakması için istemcinin bağlantısı yeterince uzun değil.</span><span class="sxs-lookup"><span data-stu-id="e69f6-321">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="e69f6-322">Kullanıcının etkileşimde bulunduğu bileşenlerin örnekleri atıldı.</span><span class="sxs-lookup"><span data-stu-id="e69f6-322">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="e69f6-323">Sunucu yeniden başlatıldı veya uygulamanın çalışan işlemi geri dönüştürüldü.</span><span class="sxs-lookup"><span data-stu-id="e69f6-323">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="e69f6-324">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="e69f6-324">Render mode</span></span>

Blazor<span data-ttu-id="e69f6-325">Sunucu uygulamaları, sunucu bağlantısı kurumadan önce sunucudaki kullanıcı arabirimini varsayılan olarak PreRender 'a ayarlar.</span><span class="sxs-lookup"><span data-stu-id="e69f6-325"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="e69f6-326">Bu, *_Host. cshtml* Razor sayfasında ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="e69f6-326">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="e69f6-327">`RenderMode`bileşenin şunları yapıp kullanmadığını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="e69f6-327">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="e69f6-328">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="e69f6-328">Is prerendered into the page.</span></span>
* <span data-ttu-id="e69f6-329">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa Blazor .</span><span class="sxs-lookup"><span data-stu-id="e69f6-329">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="e69f6-330">Açıklama</span><span class="sxs-lookup"><span data-stu-id="e69f6-330">Description</span></span> |
| ---
<span data-ttu-id="e69f6-331">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-331">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-332">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-332">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-333">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-333">'Blazor'</span></span>
- <span data-ttu-id="e69f6-334">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-334">'Identity'</span></span>
- <span data-ttu-id="e69f6-335">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-335">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-336">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-336">'Razor'</span></span>
- <span data-ttu-id="e69f6-337">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-337">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-338">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-338">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-339">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-339">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-340">'Blazor'</span></span>
- <span data-ttu-id="e69f6-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-341">'Identity'</span></span>
- <span data-ttu-id="e69f6-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-343">'Razor'</span></span>
- <span data-ttu-id="e69f6-344">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-345">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-345">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-346">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-346">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-347">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-347">'Blazor'</span></span>
- <span data-ttu-id="e69f6-348">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-348">'Identity'</span></span>
- <span data-ttu-id="e69f6-349">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-350">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-350">'Razor'</span></span>
- <span data-ttu-id="e69f6-351">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-352">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-352">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-353">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-353">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-354">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-354">'Blazor'</span></span>
- <span data-ttu-id="e69f6-355">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-355">'Identity'</span></span>
- <span data-ttu-id="e69f6-356">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-356">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-357">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-357">'Razor'</span></span>
- <span data-ttu-id="e69f6-358">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-358">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-359">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-359">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-360">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-360">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-361">'Blazor'</span></span>
- <span data-ttu-id="e69f6-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-362">'Identity'</span></span>
- <span data-ttu-id="e69f6-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-364">'Razor'</span></span>
- <span data-ttu-id="e69f6-365">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-366">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-366">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-367">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-367">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-368">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-368">'Blazor'</span></span>
- <span data-ttu-id="e69f6-369">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-369">'Identity'</span></span>
- <span data-ttu-id="e69f6-370">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-370">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-371">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-371">'Razor'</span></span>
- <span data-ttu-id="e69f6-372">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-372">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-373">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-373">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-374">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-374">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-375">'Blazor'</span></span>
- <span data-ttu-id="e69f6-376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-376">'Identity'</span></span>
- <span data-ttu-id="e69f6-377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-377">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-378">'Razor'</span></span>
- <span data-ttu-id="e69f6-379">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-379">'SignalR' uid:</span></span> 

<span data-ttu-id="e69f6-380">---------- | ---title: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-380">---------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-381">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-381">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-382">'Blazor'</span></span>
- <span data-ttu-id="e69f6-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-383">'Identity'</span></span>
- <span data-ttu-id="e69f6-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-385">'Razor'</span></span>
- <span data-ttu-id="e69f6-386">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-387">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-387">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-388">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-388">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-389">'Blazor'</span></span>
- <span data-ttu-id="e69f6-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-390">'Identity'</span></span>
- <span data-ttu-id="e69f6-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-392">'Razor'</span></span>
- <span data-ttu-id="e69f6-393">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e69f6-394">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="e69f6-394">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e69f6-395">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e69f6-395">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e69f6-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-396">'Blazor'</span></span>
- <span data-ttu-id="e69f6-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e69f6-397">'Identity'</span></span>
- <span data-ttu-id="e69f6-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e69f6-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="e69f6-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e69f6-399">'Razor'</span></span>
- <span data-ttu-id="e69f6-400">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e69f6-400">'SignalR' uid:</span></span> 

<span data-ttu-id="e69f6-401">------ | | `ServerPrerendered` | Bileşeni statik HTML olarak işler ve sunucu uygulaması için bir işaret içerir Blazor .</span><span class="sxs-lookup"><span data-stu-id="e69f6-401">------ | | `ServerPrerendered` | Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="e69f6-402">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="e69f6-402">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="e69f6-403">| | `Server`            | Sunucu uygulaması için bir işaret oluşturur Blazor .</span><span class="sxs-lookup"><span data-stu-id="e69f6-403">| | `Server`            | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="e69f6-404">Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="e69f6-404">Output from the component isn't included.</span></span> <span data-ttu-id="e69f6-405">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="e69f6-405">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="e69f6-406">| | `Static`            | Bileşeni statik HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="e69f6-406">| | `Static`            | Renders the component into static HTML.</span></span> |

<span data-ttu-id="e69f6-407">Statik HTML sayfasından sunucu bileşenleri işleme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="e69f6-407">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="e69f6-408">SignalRSunucu uygulamaları için istemciyi Blazor yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e69f6-408">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="e69f6-409">Bazen, SignalR sunucu uygulamaları tarafından kullanılan istemciyi yapılandırmanız gerekir Blazor .</span><span class="sxs-lookup"><span data-stu-id="e69f6-409">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="e69f6-410">Örneğin, SignalR bir bağlantı sorununu tanılamak için istemcide günlüğe kaydetmeyi yapılandırmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e69f6-410">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="e69f6-411">SignalRİstemciyi, *Pages/_Host. cshtml* dosyasında yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="e69f6-411">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="e69f6-412">`autostart="false"`Betiğin etiketine bir öznitelik ekleyin `<script>` `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="e69f6-412">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="e69f6-413">`Blazor.start`Oluşturucuyu belirten bir yapılandırma nesnesi çağırın ve geçirin SignalR .</span><span class="sxs-lookup"><span data-stu-id="e69f6-413">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="e69f6-414">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="e69f6-414">Logging</span></span>

<span data-ttu-id="e69f6-415">BlazorSunucu günlüğü desteği hakkında bilgi için bkz <xref:fundamentals/logging/index#create-logs-in-blazor> ..</span><span class="sxs-lookup"><span data-stu-id="e69f6-415">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
