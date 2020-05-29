---
<span data-ttu-id="ab871-101">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="ab871-101">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="ab871-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ab871-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ab871-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab871-103">'Blazor'</span></span>
- <span data-ttu-id="ab871-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab871-104">'Identity'</span></span>
- <span data-ttu-id="ab871-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab871-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab871-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab871-106">'Razor'</span></span>
- <span data-ttu-id="ab871-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ab871-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="ab871-108">ASP.NET Core Blazor barındırma modeli yapılandırması</span><span class="sxs-lookup"><span data-stu-id="ab871-108">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="ab871-109">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="ab871-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ab871-110">Bu makalede barındırma modeli yapılandırması ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="ab871-110">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="ab871-111">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="ab871-111"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="ab871-112">Ortam</span><span class="sxs-lookup"><span data-stu-id="ab871-112">Environment</span></span>

<span data-ttu-id="ab871-113">Bir uygulamayı yerel olarak çalıştırırken, ortam varsayılan olarak geliştirme aşamasındadır.</span><span class="sxs-lookup"><span data-stu-id="ab871-113">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="ab871-114">Uygulama yayımlandığında, ortam varsayılan olarak üretim olur.</span><span class="sxs-lookup"><span data-stu-id="ab871-114">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="ab871-115">Barındırılan Blazor webassembly uygulaması, üst bilgiyi ekleyerek ortamı tarayıcıya ileten bir ara yazılım aracılığıyla sunucudan alır `blazor-environment` .</span><span class="sxs-lookup"><span data-stu-id="ab871-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="ab871-116">Üstbilginin değeri ortamıdır.</span><span class="sxs-lookup"><span data-stu-id="ab871-116">The value of the header is the environment.</span></span> <span data-ttu-id="ab871-117">Barındırılan Blazor uygulama ve sunucu uygulaması aynı ortamı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="ab871-117">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="ab871-118">Ortamın nasıl yapılandırılacağı dahil olmak üzere daha fazla bilgi için bkz <xref:fundamentals/environments> ..</span><span class="sxs-lookup"><span data-stu-id="ab871-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="ab871-119">Yerel olarak çalışan tek başına bir uygulama için geliştirme sunucusu, `blazor-environment` geliştirme ortamını belirtmek için üst bilgi ekler.</span><span class="sxs-lookup"><span data-stu-id="ab871-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="ab871-120">Diğer barındırma ortamlarının ortamını belirtmek için `blazor-environment` üst bilgiyi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ab871-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="ab871-121">IIS için aşağıdaki örnekte, yayınlanan *Web. config* dosyasına özel üstbilgiyi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="ab871-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="ab871-122">*Web. config* dosyası *bin/Release/{Target Framework}/Publish* klasöründe bulunur:</span><span class="sxs-lookup"><span data-stu-id="ab871-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="ab871-123">Uygulama *Yayımlama* klasörüne yayımlandığında üzerine YAZıLMAYAN IIS için özel *Web. config* dosyasını kullanmak için, bkz <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> ..</span><span class="sxs-lookup"><span data-stu-id="ab871-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="ab871-124">Ekleme tarafından bir bileşende uygulamanın ortamını edinin <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> ve <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> özelliği okuyun:</span><span class="sxs-lookup"><span data-stu-id="ab871-124">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="ab871-125">Başlangıç sırasında, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> geliştiricilerin kendi kodlarında ortama özgü mantığa sahip olmasını sağlayan özelliği aracılığıyla kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="ab871-125">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="ab871-126">Aşağıdaki kullanışlı uzantı yöntemleri, geliştirme, üretim, hazırlama ve özel ortam adları için geçerli ortamı denetlemeye izin verir:</span><span class="sxs-lookup"><span data-stu-id="ab871-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

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

<span data-ttu-id="ab871-127"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>Özelliği, hizmet kullanılamadığında başlatma sırasında kullanılabilir <xref:Microsoft.AspNetCore.Components.NavigationManager> .</span><span class="sxs-lookup"><span data-stu-id="ab871-127">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="ab871-128">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ab871-128">Configuration</span></span>

Blazor<span data-ttu-id="ab871-129">WebAssembly yapılandırmayı şuradan yükler:</span><span class="sxs-lookup"><span data-stu-id="ab871-129"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="ab871-130">Uygulama ayarları dosyaları varsayılan olarak:</span><span class="sxs-lookup"><span data-stu-id="ab871-130">App settings files by default:</span></span>
  * <span data-ttu-id="ab871-131">*Wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="ab871-131">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="ab871-132">*Wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="ab871-132">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="ab871-133">Uygulama tarafından kaydedilen diğer [yapılandırma sağlayıcıları](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="ab871-133">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="ab871-134">Tüm sağlayıcılar Blazor webassembly uygulamalarına uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="ab871-134">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="ab871-135">Wezsembly için desteklenen sağlayıcıların açıklanması Blazor , [ Blazor ısstream (DotNet/Aspnetcore. docs #18134) Için yapılandırma sağlayıcıları açıklığa kavuşturarak](https://github.com/dotnet/AspNetCore.Docs/issues/18134)izlenir.</span><span class="sxs-lookup"><span data-stu-id="ab871-135">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="ab871-136">BlazorWebassembly uygulamasındaki yapılandırma kullanıcılar tarafından görülebilir.</span><span class="sxs-lookup"><span data-stu-id="ab871-136">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="ab871-137">**Yapılandırma bölümünde uygulama gizli dizilerini veya kimlik bilgilerini depolamamayın.**</span><span class="sxs-lookup"><span data-stu-id="ab871-137">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="ab871-138">Yapılandırma sağlayıcıları hakkında daha fazla bilgi için bkz <xref:fundamentals/configuration/index> ..</span><span class="sxs-lookup"><span data-stu-id="ab871-138">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="ab871-139">Uygulama ayarları yapılandırması</span><span class="sxs-lookup"><span data-stu-id="ab871-139">App settings configuration</span></span>

<span data-ttu-id="ab871-140">*Wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="ab871-140">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="ab871-141"><xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bileşene örnek ekleme:</span><span class="sxs-lookup"><span data-stu-id="ab871-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="ab871-142">Sağlayıcı yapılandırması</span><span class="sxs-lookup"><span data-stu-id="ab871-142">Provider configuration</span></span>

<span data-ttu-id="ab871-143">Aşağıdaki örnek, <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> ek yapılandırma sağlamak için bir kullanır:</span><span class="sxs-lookup"><span data-stu-id="ab871-143">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="ab871-144">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ab871-144">`Program.Main`:</span></span>

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

<span data-ttu-id="ab871-145"><xref:Microsoft.Extensions.Configuration.IConfiguration>Yapılandırma verilerine erişmek için bileşene örnek ekleme:</span><span class="sxs-lookup"><span data-stu-id="ab871-145">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="ab871-146">*Wwwroot* klasöründeki diğer yapılandırma dosyalarını yapılandırmaya okumak için <xref:System.Net.Http.HttpClient> dosyanın içeriğini almak üzere bir kullanın.</span><span class="sxs-lookup"><span data-stu-id="ab871-146">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="ab871-147">Bu yaklaşım kullanıldığında, mevcut <xref:System.Net.Http.HttpClient> hizmet kaydı, aşağıdaki örnekte gösterildiği gibi, dosyayı okumak için oluşturulan yerel istemciyi kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="ab871-147">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="ab871-148">*Wwwroot/otomobil. JSON*:</span><span class="sxs-lookup"><span data-stu-id="ab871-148">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="ab871-149">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ab871-149">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="ab871-150">Kimlik doğrulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="ab871-150">Authentication configuration</span></span>

<span data-ttu-id="ab871-151">*Wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="ab871-151">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="ab871-152">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ab871-152">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions);
```

#### <a name="logging-configuration"></a><span data-ttu-id="ab871-153">Günlüğe kaydetme yapılandırması</span><span class="sxs-lookup"><span data-stu-id="ab871-153">Logging configuration</span></span>

<span data-ttu-id="ab871-154">*Wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="ab871-154">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="ab871-155">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ab871-155">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="ab871-156">Konak Oluşturucu yapılandırması</span><span class="sxs-lookup"><span data-stu-id="ab871-156">Host builder configuration</span></span>

<span data-ttu-id="ab871-157">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ab871-157">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="ab871-158">Önbelleğe alınmış yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ab871-158">Cached configuration</span></span>

<span data-ttu-id="ab871-159">Yapılandırma dosyaları çevrimdışı kullanım için önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="ab871-159">Configuration files are cached for offline use.</span></span> <span data-ttu-id="ab871-160">[Aşamalı Web uygulamaları (PWAs)](xref:blazor/progressive-web-app)ile, yalnızca yeni bir dağıtım oluştururken yapılandırma dosyalarını güncelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ab871-160">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="ab871-161">Yapılandırma dosyalarının dağıtımlar arasında düzenlenmesinin hiçbir etkisi yoktur çünkü:</span><span class="sxs-lookup"><span data-stu-id="ab871-161">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="ab871-162">Kullanıcıların, kullanmaya devam ettikleri dosyaların önbelleğe alınmış sürümleri vardır.</span><span class="sxs-lookup"><span data-stu-id="ab871-162">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="ab871-163">PWA 'nın *Service-Worker. js* ve *Service-Worker-assets. js* dosyalarının derlemede yeniden oluşturulması gerekir. Bu, kullanıcının bir sonraki çevrimiçi sitesinde uygulamaya işaret eden uygulamanın yeniden dağıtıldığını belirten, derleme üzerinde yeniden oluşturulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ab871-163">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="ab871-164">Arka plan güncelleştirmelerinin PWAs tarafından nasıl işlendiği hakkında daha fazla bilgi için bkz <xref:blazor/progressive-web-app#background-updates> ..</span><span class="sxs-lookup"><span data-stu-id="ab871-164">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="ab871-165">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="ab871-165">Logging</span></span>

<span data-ttu-id="ab871-166">BlazorWebassembly günlüğü desteği hakkında bilgi için bkz <xref:fundamentals/logging/index#create-logs-in-blazor> ..</span><span class="sxs-lookup"><span data-stu-id="ab871-166">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="ab871-167">Server</span><span class="sxs-lookup"><span data-stu-id="ab871-167"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="ab871-168">Kullanıcı arabirimindeki bağlantı durumunu yansıtır</span><span class="sxs-lookup"><span data-stu-id="ab871-168">Reflect the connection state in the UI</span></span>

<span data-ttu-id="ab871-169">İstemci bağlantının kaybolduğunu algıladığında, istemci yeniden bağlanmayı denediğinde kullanıcıya varsayılan bir kullanıcı arabirimi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ab871-169">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="ab871-170">Yeniden bağlantı başarısız olursa, kullanıcıya yeniden deneme seçeneği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="ab871-170">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="ab871-171">Kullanıcı arabirimini özelleştirmek için, `id` `components-reconnect-modal` `<body>` *_Host. cshtml* sayfasında öğesinin içeren bir öğesi tanımlayın Razor :</span><span class="sxs-lookup"><span data-stu-id="ab871-171">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="ab871-172">Aşağıdaki tabloda öğesine uygulanan CSS sınıfları açıklanmaktadır `components-reconnect-modal` .</span><span class="sxs-lookup"><span data-stu-id="ab871-172">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="ab871-173">CSS sınıfı</span><span class="sxs-lookup"><span data-stu-id="ab871-173">CSS class</span></span>                       | <span data-ttu-id="ab871-174">Bildiren&hellip;</span><span class="sxs-lookup"><span data-stu-id="ab871-174">Indicates&hellip;</span></span> |
| ---
<span data-ttu-id="ab871-175">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="ab871-175">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="ab871-176">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ab871-176">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ab871-177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab871-177">'Blazor'</span></span>
- <span data-ttu-id="ab871-178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab871-178">'Identity'</span></span>
- <span data-ttu-id="ab871-179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab871-179">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab871-180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab871-180">'Razor'</span></span>
- <span data-ttu-id="ab871-181">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ab871-181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ab871-182">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="ab871-182">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="ab871-183">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ab871-183">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ab871-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab871-184">'Blazor'</span></span>
- <span data-ttu-id="ab871-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab871-185">'Identity'</span></span>
- <span data-ttu-id="ab871-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab871-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab871-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab871-187">'Razor'</span></span>
- <span data-ttu-id="ab871-188">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ab871-188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ab871-189">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="ab871-189">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="ab871-190">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ab871-190">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ab871-191">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab871-191">'Blazor'</span></span>
- <span data-ttu-id="ab871-192">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab871-192">'Identity'</span></span>
- <span data-ttu-id="ab871-193">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab871-193">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab871-194">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab871-194">'Razor'</span></span>
- <span data-ttu-id="ab871-195">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ab871-195">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ab871-196">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="ab871-196">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="ab871-197">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ab871-197">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ab871-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab871-198">'Blazor'</span></span>
- <span data-ttu-id="ab871-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab871-199">'Identity'</span></span>
- <span data-ttu-id="ab871-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab871-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab871-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab871-201">'Razor'</span></span>
- <span data-ttu-id="ab871-202">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ab871-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ab871-203">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="ab871-203">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="ab871-204">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ab871-204">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ab871-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab871-205">'Blazor'</span></span>
- <span data-ttu-id="ab871-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab871-206">'Identity'</span></span>
- <span data-ttu-id="ab871-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab871-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab871-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab871-208">'Razor'</span></span>
- <span data-ttu-id="ab871-209">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ab871-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ab871-210">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="ab871-210">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="ab871-211">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ab871-211">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ab871-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab871-212">'Blazor'</span></span>
- <span data-ttu-id="ab871-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab871-213">'Identity'</span></span>
- <span data-ttu-id="ab871-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab871-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab871-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab871-215">'Razor'</span></span>
- <span data-ttu-id="ab871-216">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ab871-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ab871-217">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="ab871-217">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="ab871-218">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ab871-218">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ab871-219">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab871-219">'Blazor'</span></span>
- <span data-ttu-id="ab871-220">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab871-220">'Identity'</span></span>
- <span data-ttu-id="ab871-221">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab871-221">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab871-222">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab871-222">'Razor'</span></span>
- <span data-ttu-id="ab871-223">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ab871-223">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ab871-224">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="ab871-224">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="ab871-225">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ab871-225">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ab871-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab871-226">'Blazor'</span></span>
- <span data-ttu-id="ab871-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab871-227">'Identity'</span></span>
- <span data-ttu-id="ab871-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab871-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab871-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab871-229">'Razor'</span></span>
- <span data-ttu-id="ab871-230">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ab871-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ab871-231">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="ab871-231">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="ab871-232">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ab871-232">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ab871-233">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab871-233">'Blazor'</span></span>
- <span data-ttu-id="ab871-234">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab871-234">'Identity'</span></span>
- <span data-ttu-id="ab871-235">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab871-235">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab871-236">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab871-236">'Razor'</span></span>
- <span data-ttu-id="ab871-237">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ab871-237">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ab871-238">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="ab871-238">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="ab871-239">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ab871-239">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ab871-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab871-240">'Blazor'</span></span>
- <span data-ttu-id="ab871-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab871-241">'Identity'</span></span>
- <span data-ttu-id="ab871-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab871-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab871-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab871-243">'Razor'</span></span>
- <span data-ttu-id="ab871-244">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ab871-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ab871-245">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="ab871-245">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="ab871-246">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ab871-246">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ab871-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab871-247">'Blazor'</span></span>
- <span data-ttu-id="ab871-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab871-248">'Identity'</span></span>
- <span data-ttu-id="ab871-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab871-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab871-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab871-250">'Razor'</span></span>
- <span data-ttu-id="ab871-251">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ab871-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ab871-252">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="ab871-252">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="ab871-253">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ab871-253">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ab871-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab871-254">'Blazor'</span></span>
- <span data-ttu-id="ab871-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab871-255">'Identity'</span></span>
- <span data-ttu-id="ab871-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab871-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab871-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab871-257">'Razor'</span></span>
- <span data-ttu-id="ab871-258">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ab871-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ab871-259">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="ab871-259">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="ab871-260">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ab871-260">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ab871-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab871-261">'Blazor'</span></span>
- <span data-ttu-id="ab871-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab871-262">'Identity'</span></span>
- <span data-ttu-id="ab871-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab871-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab871-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab871-264">'Razor'</span></span>
- <span data-ttu-id="ab871-265">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ab871-265">'SignalR' uid:</span></span> 

<span data-ttu-id="ab871-266">---------------- | ---title: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="ab871-266">---------------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="ab871-267">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ab871-267">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ab871-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab871-268">'Blazor'</span></span>
- <span data-ttu-id="ab871-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab871-269">'Identity'</span></span>
- <span data-ttu-id="ab871-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab871-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab871-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab871-271">'Razor'</span></span>
- <span data-ttu-id="ab871-272">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ab871-272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ab871-273">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="ab871-273">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="ab871-274">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ab871-274">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ab871-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab871-275">'Blazor'</span></span>
- <span data-ttu-id="ab871-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab871-276">'Identity'</span></span>
- <span data-ttu-id="ab871-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab871-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab871-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab871-278">'Razor'</span></span>
- <span data-ttu-id="ab871-279">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ab871-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ab871-280">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="ab871-280">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="ab871-281">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ab871-281">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ab871-282">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab871-282">'Blazor'</span></span>
- <span data-ttu-id="ab871-283">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab871-283">'Identity'</span></span>
- <span data-ttu-id="ab871-284">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab871-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab871-285">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab871-285">'Razor'</span></span>
- <span data-ttu-id="ab871-286">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ab871-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ab871-287">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="ab871-287">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="ab871-288">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ab871-288">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ab871-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab871-289">'Blazor'</span></span>
- <span data-ttu-id="ab871-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab871-290">'Identity'</span></span>
- <span data-ttu-id="ab871-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab871-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab871-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab871-292">'Razor'</span></span>
- <span data-ttu-id="ab871-293">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ab871-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ab871-294">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="ab871-294">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="ab871-295">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ab871-295">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ab871-296">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab871-296">'Blazor'</span></span>
- <span data-ttu-id="ab871-297">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab871-297">'Identity'</span></span>
- <span data-ttu-id="ab871-298">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab871-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab871-299">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab871-299">'Razor'</span></span>
- <span data-ttu-id="ab871-300">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ab871-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ab871-301">Başlık: ' ASP.NET Core Blazor barındırma modeli yapılandırması ' Yazar: Açıklama: ' Blazor Razor BILEŞENLERI Razor sayfalarla ve MVC uygulamalarına tümleştirme dahil olmak üzere barındırma modeli yapılandırması hakkında bilgi edinin. '</span><span class="sxs-lookup"><span data-stu-id="ab871-301">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="ab871-302">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ab871-302">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ab871-303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab871-303">'Blazor'</span></span>
- <span data-ttu-id="ab871-304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab871-304">'Identity'</span></span>
- <span data-ttu-id="ab871-305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab871-305">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab871-306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab871-306">'Razor'</span></span>
- <span data-ttu-id="ab871-307">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ab871-307">'SignalR' uid:</span></span> 

<span data-ttu-id="ab871-308">--------- | | `components-reconnect-show`     | Kayıp bir bağlantı.</span><span class="sxs-lookup"><span data-stu-id="ab871-308">--------- | | `components-reconnect-show`     | A lost connection.</span></span> <span data-ttu-id="ab871-309">İstemci yeniden bağlanmaya çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="ab871-309">The client is attempting to reconnect.</span></span> <span data-ttu-id="ab871-310">Kalıcı olarak göster.</span><span class="sxs-lookup"><span data-stu-id="ab871-310">Show the modal.</span></span> <span data-ttu-id="ab871-311">| | `components-reconnect-hide`     | Etkin bir bağlantı sunucuya yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ab871-311">| | `components-reconnect-hide`     | An active connection is re-established to the server.</span></span> <span data-ttu-id="ab871-312">Kalıcı olarak gizleyin.</span><span class="sxs-lookup"><span data-stu-id="ab871-312">Hide the modal.</span></span> <span data-ttu-id="ab871-313">| | `components-reconnect-failed`   | Muhtemelen bir ağ hatasından dolayı yeniden bağlantı başarısız oldu.</span><span class="sxs-lookup"><span data-stu-id="ab871-313">| | `components-reconnect-failed`   | Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="ab871-314">Yeniden bağlanmayı denemek için çağrısı yapın `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="ab871-314">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> <span data-ttu-id="ab871-315">| | `components-reconnect-rejected` | Yeniden bağlantı reddedildi.</span><span class="sxs-lookup"><span data-stu-id="ab871-315">| | `components-reconnect-rejected` | Reconnection rejected.</span></span> <span data-ttu-id="ab871-316">Sunucuya ulaşıldı ancak bağlantı reddedildi ve kullanıcının sunucudaki durumu kayboldu.</span><span class="sxs-lookup"><span data-stu-id="ab871-316">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="ab871-317">Uygulamayı yeniden yüklemek için çağrısı yapın `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="ab871-317">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="ab871-318">Bu bağlantı durumu şu durumlarda oluşabilir:</span><span class="sxs-lookup"><span data-stu-id="ab871-318">This connection state may result when:</span></span><ul><li><span data-ttu-id="ab871-319">Sunucu tarafında devre dışı bir kilitlenme oluşur.</span><span class="sxs-lookup"><span data-stu-id="ab871-319">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="ab871-320">Sunucunun kullanıcının durumunu bırakması için istemcinin bağlantısı yeterince uzun değil.</span><span class="sxs-lookup"><span data-stu-id="ab871-320">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="ab871-321">Kullanıcının etkileşimde bulunduğu bileşenlerin örnekleri atıldı.</span><span class="sxs-lookup"><span data-stu-id="ab871-321">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="ab871-322">Sunucu yeniden başlatıldı veya uygulamanın çalışan işlemi geri dönüştürüldü.</span><span class="sxs-lookup"><span data-stu-id="ab871-322">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="ab871-323">Oluşturma modu</span><span class="sxs-lookup"><span data-stu-id="ab871-323">Render mode</span></span>

Blazor<span data-ttu-id="ab871-324">Sunucu uygulamaları, sunucu bağlantısı kurumadan önce sunucudaki kullanıcı arabirimini varsayılan olarak PreRender 'a ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ab871-324"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="ab871-325">Bu, *_Host. cshtml* Razor sayfasında ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="ab871-325">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="ab871-326"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>bileşenin şunları yapıp kullanmadığını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="ab871-326"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="ab871-327">, Sayfaya ön gönderilir.</span><span class="sxs-lookup"><span data-stu-id="ab871-327">Is prerendered into the page.</span></span>
* <span data-ttu-id="ab871-328">, Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa Blazor .</span><span class="sxs-lookup"><span data-stu-id="ab871-328">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="ab871-329">Açıklama</span><span class="sxs-lookup"><span data-stu-id="ab871-329">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="ab871-330">Bileşeni statik HTML olarak işler ve sunucu uygulaması için bir işaret içerir Blazor .</span><span class="sxs-lookup"><span data-stu-id="ab871-330">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="ab871-331">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="ab871-331">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="ab871-332">Sunucu uygulaması için bir işaret oluşturur Blazor .</span><span class="sxs-lookup"><span data-stu-id="ab871-332">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="ab871-333">Bileşen çıkışı dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="ab871-333">Output from the component isn't included.</span></span> <span data-ttu-id="ab871-334">Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor .</span><span class="sxs-lookup"><span data-stu-id="ab871-334">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="ab871-335">Bileşeni statik HTML olarak işler.</span><span class="sxs-lookup"><span data-stu-id="ab871-335">Renders the component into static HTML.</span></span> |

<span data-ttu-id="ab871-336">Statik HTML sayfasından sunucu bileşenleri işleme desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="ab871-336">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="ab871-337">SignalRSunucu uygulamaları için istemciyi Blazor yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ab871-337">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="ab871-338">Bazen, SignalR sunucu uygulamaları tarafından kullanılan istemciyi yapılandırmanız gerekir Blazor .</span><span class="sxs-lookup"><span data-stu-id="ab871-338">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="ab871-339">Örneğin, SignalR bir bağlantı sorununu tanılamak için istemcide günlüğe kaydetmeyi yapılandırmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ab871-339">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="ab871-340">SignalRİstemciyi, *Pages/_Host. cshtml* dosyasında yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="ab871-340">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="ab871-341">`autostart="false"`Betiğin etiketine bir öznitelik ekleyin `<script>` `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="ab871-341">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="ab871-342">`Blazor.start`Oluşturucuyu belirten bir yapılandırma nesnesi çağırın ve geçirin SignalR .</span><span class="sxs-lookup"><span data-stu-id="ab871-342">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="ab871-343">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="ab871-343">Logging</span></span>

<span data-ttu-id="ab871-344">BlazorSunucu günlüğü desteği hakkında bilgi için bkz <xref:fundamentals/logging/index#create-logs-in-blazor> ..</span><span class="sxs-lookup"><span data-stu-id="ab871-344">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
