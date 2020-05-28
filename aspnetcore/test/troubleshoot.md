---
<span data-ttu-id="66c25-101">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="66c25-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="66c25-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="66c25-102">'Blazor'</span></span>
- <span data-ttu-id="66c25-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="66c25-103">'Identity'</span></span>
- <span data-ttu-id="66c25-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="66c25-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="66c25-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="66c25-105">'Razor'</span></span>
- <span data-ttu-id="66c25-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="66c25-106">'SignalR' uid:</span></span> 

---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="66c25-107">ASP.NET Core projelerinde sorun giderme ve hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="66c25-107">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="66c25-108">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="66c25-108">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="66c25-109">Aşağıdaki bağlantılar sorun giderme kılavuzunu sağlar:</span><span class="sxs-lookup"><span data-stu-id="66c25-109">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="66c25-110">NDC Konferansı (Londra, 2018): ASP.NET Core uygulamalarda sorunları tanılama</span><span class="sxs-lookup"><span data-stu-id="66c25-110">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="66c25-111">ASP.NET blogu: sorun giderme ASP.NET Core performans sorunları</span><span class="sxs-lookup"><span data-stu-id="66c25-111">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="66c25-112">.NET Core SDK uyarılar</span><span class="sxs-lookup"><span data-stu-id="66c25-112">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="66c25-113">.NET Core SDK hem 32-bit hem de 64-bit sürümleri yüklenir</span><span class="sxs-lookup"><span data-stu-id="66c25-113">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="66c25-114">**Yeni proje** iletişim kutusunda ASP.NET Core için aşağıdaki uyarıyı görebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="66c25-114">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="66c25-115">.NET Core SDK hem 32-bit hem de 64-bit sürümleri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="66c25-115">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="66c25-116">Yalnızca ' C: \\ Program Files DotNet SDK ' konumunda yüklü olan 64 bitlik sürümlerden alınan şablonlar \\ \\ \\ görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="66c25-116">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="66c25-117">Bu uyarı, [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) hem 32-bit (x86) hem de 64-bit (x64) sürümleri yüklendiğinde görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="66c25-117">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) are installed.</span></span> <span data-ttu-id="66c25-118">Her iki sürümün de sık yüklenebileceği yaygın nedenler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="66c25-118">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="66c25-119">İlk olarak 32 bitlik bir makine kullanarak .NET Core SDK yükleyicisini indirdiniz, ancak bu dosyayı bir 64 bit makineye kopyaladınız ve bu makinede yüklediniz.</span><span class="sxs-lookup"><span data-stu-id="66c25-119">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="66c25-120">32 bit .NET Core SDK başka bir uygulama tarafından yüklendi.</span><span class="sxs-lookup"><span data-stu-id="66c25-120">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="66c25-121">Yanlış sürüm indirildi ve yüklendi.</span><span class="sxs-lookup"><span data-stu-id="66c25-121">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="66c25-122">Bu uyarıyı engellemek için 32 bit .NET Core SDK kaldırın.</span><span class="sxs-lookup"><span data-stu-id="66c25-122">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="66c25-123">**Denetim Masası**  >  **Programlar ve Özellikler**'den Kaldır  >  **bir programı kaldırma veya değiştirme**.</span><span class="sxs-lookup"><span data-stu-id="66c25-123">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="66c25-124">Uyarının neden oluştuğunu ve etkilerini anladıysanız, uyarıyı yoksayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="66c25-124">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="66c25-125">.NET Core SDK birden çok konuma yüklendi</span><span class="sxs-lookup"><span data-stu-id="66c25-125">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="66c25-126">**Yeni proje** iletişim kutusunda ASP.NET Core için aşağıdaki uyarıyı görebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="66c25-126">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="66c25-127">.NET Core SDK birden çok konuma yüklenir.</span><span class="sxs-lookup"><span data-stu-id="66c25-127">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="66c25-128">Yalnızca ' C: \\ Program Files DotNet SDK ' konumunda yüklü SDK 'lardan şablonlar \\ \\ \\ görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="66c25-128">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="66c25-129">\*C: \\ Program Files \\ DotNet \\ SDK \\ \*dışında bir dizinde en az bir .NET Core SDK yüklemeniz olduğunda bu iletiyi görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="66c25-129">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="66c25-130">Genellikle bu, .NET Core SDK MSI yükleyicisi yerine Kopyala/Yapıştır kullanılarak bir makineye dağıtıldığında meydana gelir.</span><span class="sxs-lookup"><span data-stu-id="66c25-130">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="66c25-131">Bu uyarıyı engellemek için tüm 32-bit .NET Core SDK 'larını ve çalışma zamanlarını kaldırın.</span><span class="sxs-lookup"><span data-stu-id="66c25-131">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="66c25-132">**Denetim Masası**  >  **Programlar ve Özellikler**'den Kaldır  >  **bir programı kaldırma veya değiştirme**.</span><span class="sxs-lookup"><span data-stu-id="66c25-132">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="66c25-133">Uyarının neden oluştuğunu ve etkilerini anladıysanız, uyarıyı yoksayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="66c25-133">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="66c25-134">.NET Core SDK 'Ları algılanmadı</span><span class="sxs-lookup"><span data-stu-id="66c25-134">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="66c25-135">ASP.NET Core için Visual Studio **Yeni proje** iletişim kutusunda aşağıdaki uyarıyı görebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="66c25-135">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="66c25-136">.NET Core SDK 'Ları algılanmadı, ortam değişkenine dahil olduklarından emin olun `PATH` .</span><span class="sxs-lookup"><span data-stu-id="66c25-136">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="66c25-137">Bir komut yürütürken `dotnet` , uyarı şöyle görünür:</span><span class="sxs-lookup"><span data-stu-id="66c25-137">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="66c25-138">Yüklü olan DotNet SDK 'Ları bulmak mümkün değildi.</span><span class="sxs-lookup"><span data-stu-id="66c25-138">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="66c25-139">Bu uyarılar, ortam değişkeni `PATH` makinede herhangi bir .NET Core SDK 'sı üzerine işaret etmez görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="66c25-139">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="66c25-140">Bu sorunu çözmek için:</span><span class="sxs-lookup"><span data-stu-id="66c25-140">To resolve this problem:</span></span>

* <span data-ttu-id="66c25-141">.NET Core SDK 'i yükler.</span><span class="sxs-lookup"><span data-stu-id="66c25-141">Install the .NET Core SDK.</span></span> <span data-ttu-id="66c25-142">[.Net Indirmelerinde](https://dotnet.microsoft.com/download)en son yükleyiciyi edinin.</span><span class="sxs-lookup"><span data-stu-id="66c25-142">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="66c25-143">`PATH`Ortam DEĞIŞKENININ SDK 'nın yüklü olduğu konuma işaret ettiğini doğrulayın ( `C:\Program Files\dotnet\` 64 bit/x64 veya `C:\Program Files (x86)\dotnet\` 32 bit/x86 için).</span><span class="sxs-lookup"><span data-stu-id="66c25-143">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="66c25-144">SDK yükleyicisi normalde ' i ayarlar `PATH` .</span><span class="sxs-lookup"><span data-stu-id="66c25-144">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="66c25-145">Aynı bit genişliği SDK 'larını ve çalışma zamanlarını aynı makineye her zaman yükler.</span><span class="sxs-lookup"><span data-stu-id="66c25-145">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="66c25-146">.NET Core barındırma paketi yüklendikten sonra SDK eksik</span><span class="sxs-lookup"><span data-stu-id="66c25-146">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="66c25-147">.NET Core [barındırma](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) paketinin yüklenmesi, .net core `PATH` 'un 32-bit (x86) sürümünü () işaret etmek üzere .NET Core çalışma zamanını yüklerken değiştirir `C:\Program Files (x86)\dotnet\` .</span><span class="sxs-lookup"><span data-stu-id="66c25-147">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="66c25-148">Bu, 32-bit (x86) .NET Core `dotnet` komutu kullanıldığında ([.NET Core SDK 'ları algılanmadığında](#no-net-core-sdks-were-detected)) eksik SDK 'lara yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="66c25-148">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="66c25-149">Bu sorunu çözmek için, `C:\Program Files\dotnet\` öncesinde bir konuma geçin `C:\Program Files (x86)\dotnet\` `PATH` .</span><span class="sxs-lookup"><span data-stu-id="66c25-149">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="66c25-150">Uygulamadan veri alma</span><span class="sxs-lookup"><span data-stu-id="66c25-150">Obtain data from an app</span></span>

<span data-ttu-id="66c25-151">Bir uygulama isteklere yanıt veriyorsa, ara yazılım kullanarak uygulamadan aşağıdaki verileri alabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="66c25-151">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="66c25-152">İstek: Yöntem, düzen, ana bilgisayar, pathbase, yol, sorgu dizesi, üst bilgiler</span><span class="sxs-lookup"><span data-stu-id="66c25-152">Request: Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="66c25-153">Bağlantı: uzak IP adresi, uzak bağlantı noktası, yerel IP adresi, yerel bağlantı noktası, istemci sertifikası</span><span class="sxs-lookup"><span data-stu-id="66c25-153">Connection: Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* Identity: Name, display name
* <span data-ttu-id="66c25-154">Yapılandırma ayarları</span><span class="sxs-lookup"><span data-stu-id="66c25-154">Configuration settings</span></span>
* <span data-ttu-id="66c25-155">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="66c25-155">Environment variables</span></span>

<span data-ttu-id="66c25-156">Aşağıdaki [Ara yazılım](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) kodunu `Startup.Configure` metodun istek işleme işlem hattının başına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="66c25-156">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="66c25-157">Bu ortam, kodun yalnızca geliştirme ortamında yürütülmesini sağlamak için, ara yazılım çalıştırılmadan önce denetlenir.</span><span class="sxs-lookup"><span data-stu-id="66c25-157">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="66c25-158">Ortamı edinmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="66c25-158">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="66c25-159">`IHostingEnvironment` `Startup.Configure` Metodunu yöntemine ekleyin ve yerel değişkenle ortamı kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="66c25-159">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="66c25-160">Aşağıdaki örnek kodda bu yaklaşım gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="66c25-160">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="66c25-161">Ortamı sınıfındaki bir özelliğe atayın `Startup` .</span><span class="sxs-lookup"><span data-stu-id="66c25-161">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="66c25-162">Özelliğini kullanarak ortamı denetleyin (örneğin, `if (Environment.IsDevelopment())` ).</span><span class="sxs-lookup"><span data-stu-id="66c25-162">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="66c25-163">ASP.NET Core uygulamalarda hata ayıkla</span><span class="sxs-lookup"><span data-stu-id="66c25-163">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="66c25-164">Aşağıdaki bağlantılar ASP.NET Core uygulamalarda hata ayıklama hakkında bilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="66c25-164">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="66c25-165">Linux 'ta ASP Core hata ayıklaması</span><span class="sxs-lookup"><span data-stu-id="66c25-165">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="66c25-166">SSH üzerinde UNIX üzerinde .NET Core hata ayıklaması</span><span class="sxs-lookup"><span data-stu-id="66c25-166">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="66c25-167">Hızlı başlangıç: Visual Studio hata ayıklayıcısı ile ASP.NET hata ayıklayın</span><span class="sxs-lookup"><span data-stu-id="66c25-167">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="66c25-168">Daha fazla hata ayıklama bilgisi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/2960) bakın.</span><span class="sxs-lookup"><span data-stu-id="66c25-168">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
