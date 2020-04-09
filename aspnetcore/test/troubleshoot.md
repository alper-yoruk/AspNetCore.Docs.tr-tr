---
title: Sorun giderme ve Hata Ayıklama ASP.NET Core projeleri
author: Rick-Anderson
description: ASP.NET Core projeleri ile uyarıları ve hataları anlayın ve sorun giderin.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: 345967f08cf99ef5f18d0c9bcd59ab29c74454f1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511515"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="19cc5-103">Sorun giderme ve Hata Ayıklama ASP.NET Core projeleri</span><span class="sxs-lookup"><span data-stu-id="19cc5-103">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="19cc5-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="19cc5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="19cc5-105">Aşağıdaki bağlantılar sorun giderme kılavuzu sağlar:</span><span class="sxs-lookup"><span data-stu-id="19cc5-105">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="19cc5-106">NDC Konferansı (Londra, 2018): ASP.NET Temel Uygulamalarda Sorunların Teşhisi</span><span class="sxs-lookup"><span data-stu-id="19cc5-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="19cc5-107">ASP.NET Blog: Temel Performans Sorunları ASP.NET Sorun Giderme</span><span class="sxs-lookup"><span data-stu-id="19cc5-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="19cc5-108">.NET Çekirdek SDK uyarıları</span><span class="sxs-lookup"><span data-stu-id="19cc5-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="19cc5-109">.NET Core SDK'nın hem 32 bit hem de 64 bit sürümleri yüklenir</span><span class="sxs-lookup"><span data-stu-id="19cc5-109">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="19cc5-110">ASP.NET Core için **Yeni Proje** iletişim kutusunda aşağıdaki uyarıyı görebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="19cc5-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="19cc5-111">.NET Core SDK'nın hem 32 bit hem de 64 bit sürümleri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="19cc5-111">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="19cc5-112">Yalnızca\\'C: Program\\Files dotnet\\sdk'\\adresinde yüklü 64 bit sürümlerden şablonlar görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="19cc5-112">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="19cc5-113">Bu uyarı, [.NET Core SDK'nın](https://dotnet.microsoft.com/download/dotnet-core) hem 32 bit (x86) hem de 64 bit (x64) sürümleri yüklendiğinde görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="19cc5-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) are installed.</span></span> <span data-ttu-id="19cc5-114">Her iki sürümün de yüklenmesinin yaygın nedenleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="19cc5-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="19cc5-115">.NET Core SDK yükleyicisini 32 bit lik bir makine kullanarak indirdiniz, ancak sonra kopyalayıp 64 bit lik bir makineye yükledin.</span><span class="sxs-lookup"><span data-stu-id="19cc5-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="19cc5-116">32-bit .NET Core SDK başka bir uygulama tarafından yüklendi.</span><span class="sxs-lookup"><span data-stu-id="19cc5-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="19cc5-117">Yanlış sürüm indirildi ve yüklendi.</span><span class="sxs-lookup"><span data-stu-id="19cc5-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="19cc5-118">Bu uyarıyı önlemek için 32 bit .NET Core SDK'yı kaldırın.</span><span class="sxs-lookup"><span data-stu-id="19cc5-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="19cc5-119">**Denetim Masası** > **Programlarından ve Özelliklerinden** > Kaldırma veya**Programı Değiştir.**</span><span class="sxs-lookup"><span data-stu-id="19cc5-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="19cc5-120">Uyarının neden oluştuğunu ve bunun sonuçlarını anlıyorsanız, uyarıyı yok sayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="19cc5-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="19cc5-121">.NET Core SDK birden çok konuma yüklenir</span><span class="sxs-lookup"><span data-stu-id="19cc5-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="19cc5-122">ASP.NET Core için **Yeni Proje** iletişim kutusunda aşağıdaki uyarıyı görebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="19cc5-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="19cc5-123">.NET Core SDK birden çok konuma yüklenir.</span><span class="sxs-lookup"><span data-stu-id="19cc5-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="19cc5-124">Yalnızca\\'C: Program\\Files dotnet\\sdk'\\yüklü SDK'lardan şablonlar görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="19cc5-124">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="19cc5-125">*C:\\Program Dosyaları\\dotnet\\sdk\\*dışında bir dizinde .NET Core SDK en az bir yükleme varsa bu mesajı görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="19cc5-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="19cc5-126">Genellikle bu, .NET Core SDK MSI yükleyicisi yerine kopyala/yapıştır kullanarak bir makineye dağıtıldığında gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="19cc5-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="19cc5-127">Bu uyarıyı önlemek için tüm 32 bit .NET Core SDK'ları ve çalışma sürelerini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="19cc5-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="19cc5-128">**Denetim Masası** > **Programlarından ve Özelliklerinden** > Kaldırma veya**Programı Değiştir.**</span><span class="sxs-lookup"><span data-stu-id="19cc5-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="19cc5-129">Uyarının neden oluştuğunu ve bunun sonuçlarını anlıyorsanız, uyarıyı yok sayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="19cc5-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="19cc5-130">.NET Çekirdek SDK'sı algılanmadı</span><span class="sxs-lookup"><span data-stu-id="19cc5-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="19cc5-131">Visual Studio **Yeni Proje** ASP.NET Core iletişim kutusunda aşağıdaki uyarıyı görebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="19cc5-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="19cc5-132">.NET Çekirdek SDK'ları algılanmadı, ortam değişkenine `PATH`dahil edildiklerinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="19cc5-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="19cc5-133">Bir `dotnet` komut uyrurken, uyarı şu şekilde görünür:</span><span class="sxs-lookup"><span data-stu-id="19cc5-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="19cc5-134">Yüklü dotnet SDK'ları bulmak mümkün değildi.</span><span class="sxs-lookup"><span data-stu-id="19cc5-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="19cc5-135">Bu uyarılar, ortam `PATH` değişkeni makinedeki herhangi bir .NET Core SDK'ya işaret etmediğinde görünür.</span><span class="sxs-lookup"><span data-stu-id="19cc5-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="19cc5-136">Bu sorunu çözmek için:</span><span class="sxs-lookup"><span data-stu-id="19cc5-136">To resolve this problem:</span></span>

* <span data-ttu-id="19cc5-137">.NET Core SDK'yı yükleyin.</span><span class="sxs-lookup"><span data-stu-id="19cc5-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="19cc5-138">[.NET İndirmelerden](https://dotnet.microsoft.com/download)en son yükleyiciyi edinin.</span><span class="sxs-lookup"><span data-stu-id="19cc5-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="19cc5-139">Ortam değişkeninin `PATH` SDK'nın yüklü olduğu konuma`C:\Program Files\dotnet\` (64-bit/x64 `C:\Program Files (x86)\dotnet\` veya 32-bit/x86) işaret ettiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="19cc5-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="19cc5-140">SDK yükleyicisi normalde `PATH`.</span><span class="sxs-lookup"><span data-stu-id="19cc5-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="19cc5-141">Aynı makineye her zaman aynı bitlikteki SDK'ları ve çalışma sürelerini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="19cc5-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="19cc5-142">.NET Core Hosting Paketini yükledikten sonra Eksik SDK</span><span class="sxs-lookup"><span data-stu-id="19cc5-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="19cc5-143">[.NET Çekirdek Barındırma Paketi'nin](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) `PATH` yüklenmesi, .NET Core 'un 32 bit (x86) sürümünü işaret etmek`C:\Program Files (x86)\dotnet\`için .NET Core çalışma zamanını değiştirir.</span><span class="sxs-lookup"><span data-stu-id="19cc5-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="19cc5-144">Bu, 32 bit (x86) .NET Core `dotnet` komutu kullanıldığında SDK'ların eksik olması yla sonuçlanabilir ([.NET Çekirdek SDK'ları algılanmaz).](#no-net-core-sdks-were-detected)</span><span class="sxs-lookup"><span data-stu-id="19cc5-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="19cc5-145">Bu sorunu gidermek `C:\Program Files\dotnet\` için, `C:\Program Files (x86)\dotnet\` ''de `PATH`önce bir konuma geçin.</span><span class="sxs-lookup"><span data-stu-id="19cc5-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="19cc5-146">Bir uygulamadan veri alma</span><span class="sxs-lookup"><span data-stu-id="19cc5-146">Obtain data from an app</span></span>

<span data-ttu-id="19cc5-147">Bir uygulama isteklere yanıt verebiliyorsa, ara yazılımkullanarak uygulamadan aşağıdaki verileri elde edebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="19cc5-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="19cc5-148">İstek &ndash; Yöntemi, şema, ana bilgisayar, yol tabanı, yol, sorgu dizesi, üstbilgi</span><span class="sxs-lookup"><span data-stu-id="19cc5-148">Request &ndash; Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="19cc5-149">Bağlantı &ndash; Uzak IP adresi, uzak bağlantı noktası, yerel IP adresi, yerel bağlantı noktası, istemci sertifikası</span><span class="sxs-lookup"><span data-stu-id="19cc5-149">Connection &ndash; Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="19cc5-150">Kimlik &ndash; Adı, görüntü adı</span><span class="sxs-lookup"><span data-stu-id="19cc5-150">Identity &ndash; Name, display name</span></span>
* <span data-ttu-id="19cc5-151">Yapılandırma ayarları</span><span class="sxs-lookup"><span data-stu-id="19cc5-151">Configuration settings</span></span>
* <span data-ttu-id="19cc5-152">Ortam değişkenleri</span><span class="sxs-lookup"><span data-stu-id="19cc5-152">Environment variables</span></span>

<span data-ttu-id="19cc5-153">Yöntemin [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) istek işleme ardışık hattının `Startup.Configure` başına aşağıdaki ara yazılım kodunu yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="19cc5-153">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="19cc5-154">Ortamın, kodun yalnızca Geliştirme ortamında yürütülmediğinden emin olmak için ara yazılım çalıştırılmadan önce denetlenir.</span><span class="sxs-lookup"><span data-stu-id="19cc5-154">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="19cc5-155">Ortamı elde etmek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="19cc5-155">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="19cc5-156">`IHostingEnvironment` Yöntemiçine `Startup.Configure` enjekte ve yerel değişken ile çevre kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="19cc5-156">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="19cc5-157">Aşağıdaki örnek kod bu yaklaşımı göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="19cc5-157">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="19cc5-158">Ortamı sınıftaki `Startup` bir özelliğe atayın.</span><span class="sxs-lookup"><span data-stu-id="19cc5-158">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="19cc5-159">Özelliği kullanarak ortamı denetleyin (örneğin, `if (Environment.IsDevelopment())`).</span><span class="sxs-lookup"><span data-stu-id="19cc5-159">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

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

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="19cc5-160">Temel uygulamaları ASP.NET hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="19cc5-160">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="19cc5-161">Aşağıdaki bağlantılar, ASP.NET Core uygulamaları hakkında bilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="19cc5-161">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="19cc5-162">Linux'ta ASP Core Hata Ayıklama</span><span class="sxs-lookup"><span data-stu-id="19cc5-162">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="19cc5-163">SSH üzerinden Unix üzerinde .NET Core hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="19cc5-163">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="19cc5-164">Quickstart: Visual Studio hata ayıklama ile ASP.NET hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="19cc5-164">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="19cc5-165">Daha fazla hata ayıklama bilgisi için [bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/2960) bakın.</span><span class="sxs-lookup"><span data-stu-id="19cc5-165">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
