---
title: IIS ve ASP.NET Core ile işlem içi barındırma
author: rick-anderson
description: IIS ve ASP.NET Core modülüyle Işlem içi barındırma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: ecf873e6ad2044aae87a5e7dc07316eae0e10912
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755289"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="46ef7-103">IIS ve ASP.NET Core ile işlem içi barındırma</span><span class="sxs-lookup"><span data-stu-id="46ef7-103">In-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="46ef7-104">İşlem içi barındırma, IIS çalışan işlemiyle aynı işlemde bir ASP.NET Core uygulaması çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="46ef7-104">In-process hosting runs an ASP.NET Core app in the same process as its IIS worker process.</span></span> <span data-ttu-id="46ef7-105">İşlem içi barındırma, istek dışı barındırmak için gelişmiş performans sağlar çünkü istekler, giden ağ trafiği ile aynı makineye geri döndürülen bir ağ arabirimidir.</span><span class="sxs-lookup"><span data-stu-id="46ef7-105">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="46ef7-106">Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve süreçte barındırılan bir uygulama arasındaki ilişki gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="46ef7-106">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![İşlem içi barındırma senaryosunda modül ASP.NET Core](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a><span data-ttu-id="46ef7-108">İşlem içi barındırmayı etkinleştir</span><span class="sxs-lookup"><span data-stu-id="46ef7-108">Enable in-process hosting</span></span>

<span data-ttu-id="46ef7-109">ASP.NET Core 3,0 ' den itibaren, işlem içi barındırma IIS 'e dağıtılan tüm uygulamalar için varsayılan olarak etkinleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="46ef7-109">Since ASP.NET Core 3.0, in-process hosting has been enabled by default for all app deployed to IIS.</span></span>

<span data-ttu-id="46ef7-110">Bir uygulamayı işlem içi barındırma için açıkça yapılandırmak üzere, `<AspNetCoreHostingModel>` özelliğinin değerini `InProcess` Proje dosyasında () olarak ayarlayın `.csproj` :</span><span class="sxs-lookup"><span data-stu-id="46ef7-110">To explicitly configure an app for in-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `InProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a><span data-ttu-id="46ef7-111">Genel mimari</span><span class="sxs-lookup"><span data-stu-id="46ef7-111">General architecture</span></span>

<span data-ttu-id="46ef7-112">Bir isteğin genel akışı aşağıdaki gibidir:</span><span class="sxs-lookup"><span data-stu-id="46ef7-112">The general flow of a request is as follows:</span></span>

1. <span data-ttu-id="46ef7-113">Web 'den çekirdek modu HTTP.sys sürücüsüne bir istek ulaşır.</span><span class="sxs-lookup"><span data-stu-id="46ef7-113">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="46ef7-114">Sürücü, yerel isteği Web sitesinin yapılandırılmış bağlantı noktasında IIS 'ye yönlendirir, genellikle 80 (HTTP) veya 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="46ef7-114">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="46ef7-115">ASP.NET Core modülü yerel isteği alır ve IIS HTTP sunucusuna ( `IISHttpServer` ) geçirir.</span><span class="sxs-lookup"><span data-stu-id="46ef7-115">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="46ef7-116">IIS HTTP sunucusu, isteği yerelden yönetilene dönüştüren bir IIS için işlem içi sunucu uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="46ef7-116">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="46ef7-117">IIS HTTP sunucusu isteği tamamladıktan sonra:</span><span class="sxs-lookup"><span data-stu-id="46ef7-117">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="46ef7-118">İstek ASP.NET Core ara yazılım ardışık düzenine gönderilir.</span><span class="sxs-lookup"><span data-stu-id="46ef7-118">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="46ef7-119">Ara yazılım ardışık düzeni isteği işler ve `HttpContext` uygulamanın mantığına bir örnek olarak geçirir.</span><span class="sxs-lookup"><span data-stu-id="46ef7-119">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="46ef7-120">Uygulamanın yanıtı IIS HTTP sunucusu aracılığıyla IIS 'e geri geçirilir.</span><span class="sxs-lookup"><span data-stu-id="46ef7-120">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="46ef7-121">IIS yanıtı, isteği başlatan istemciye gönderir.</span><span class="sxs-lookup"><span data-stu-id="46ef7-121">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="46ef7-122">`CreateDefaultBuilder`<xref:Microsoft.AspNetCore.Hosting.Server.IServer> <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> [CoreCLR](/dotnet/standard/glossary#coreclr) 'YI önyüklemek ve uygulamayı IIS çalışan işleminin (veya) içinde barındırmak için yöntemini çağırarak bir örnek ekler `w3wp.exe` `iisexpress.exe` .</span><span class="sxs-lookup"><span data-stu-id="46ef7-122">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (`w3wp.exe` or `iisexpress.exe`).</span></span> <span data-ttu-id="46ef7-123">Performans testleri, bir .NET Core uygulamasını işlem içinde barındırmanın, uygulamayı işlem dışı ve [Kestrel](xref:fundamentals/servers/kestrel)'e yönelik proxy istekleri barındırmakla karşılaştırıldığında önemli ölçüde daha yüksek istek aktarım hızı sağladığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="46ef7-123">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="46ef7-124">Tek bir dosya yürütülebilir dosyası olarak yayınlanan uygulamalar, işlem içi barındırma modeliyle yüklenemez.</span><span class="sxs-lookup"><span data-stu-id="46ef7-124">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="46ef7-125">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="46ef7-125">Application configuration</span></span>

<span data-ttu-id="46ef7-126">IIS seçeneklerini yapılandırmak için, içinde için bir hizmet yapılandırması <xref:Microsoft.AspNetCore.Builder.IISServerOptions> ekleyin <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> .</span><span class="sxs-lookup"><span data-stu-id="46ef7-126">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="46ef7-127">Aşağıdaki örnek, Automatıcauthentication 'ı devre dışı bırakır:</span><span class="sxs-lookup"><span data-stu-id="46ef7-127">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="46ef7-128">Seçenek</span><span class="sxs-lookup"><span data-stu-id="46ef7-128">Option</span></span> | <span data-ttu-id="46ef7-129">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="46ef7-129">Default</span></span> | <span data-ttu-id="46ef7-130">Ayar</span><span class="sxs-lookup"><span data-stu-id="46ef7-130">Setting</span></span> |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | <span data-ttu-id="46ef7-131">`true`IIS sunucusu, `HttpContext.User` [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)tarafından kimliği doğrulanmış olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="46ef7-131">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="46ef7-132">İse `false` , sunucu yalnızca bir kimlik sağlar `HttpContext.User` ve tarafından açıkça istendiğinde sorunları yanıtlar `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="46ef7-132">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="46ef7-133">' Nin çalışması için IIS 'de Windows kimlik doğrulamasının etkinleştirilmesi gerekir `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="46ef7-133">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="46ef7-134">Daha fazla bilgi için bkz. [Windows kimlik doğrulaması](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="46ef7-134">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName` | `null` | <span data-ttu-id="46ef7-135">Oturum açma sayfalarındaki kullanıcılara gösterilen görünen adı ayarlar.</span><span class="sxs-lookup"><span data-stu-id="46ef7-135">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO` | `false` | <span data-ttu-id="46ef7-136">Ve için zaman uyumlu g/ç 'ye izin verilip verilmeyeceğini belirtir `HttpContext.Request` `HttpContext.Response` .</span><span class="sxs-lookup"><span data-stu-id="46ef7-136">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize` | `30000000` | <span data-ttu-id="46ef7-137">İçin en büyük istek gövdesi boyutunu alır veya ayarlar `HttpRequest` .</span><span class="sxs-lookup"><span data-stu-id="46ef7-137">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="46ef7-138">IIS 'nin `maxAllowedContentLength` , içindeki kümesinden önce işlenecek sınıra sahip olduğunu unutmayın `MaxRequestBodySize` `IISServerOptions` .</span><span class="sxs-lookup"><span data-stu-id="46ef7-138">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="46ef7-139">' Nin değiştirilmesi `MaxRequestBodySize` , etkilemez `maxAllowedContentLength` .</span><span class="sxs-lookup"><span data-stu-id="46ef7-139">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="46ef7-140">Arttırmak için `maxAllowedContentLength` ' de bir giriş ekleyerek `web.config` `maxAllowedContentLength` daha yüksek bir değere ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="46ef7-140">To increase `maxAllowedContentLength`, add an entry in the `web.config` to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="46ef7-141">Daha fazla ayrıntı için bkz. [yapılandırma](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span><span class="sxs-lookup"><span data-stu-id="46ef7-141">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a><span data-ttu-id="46ef7-142">İşlem içi ve işlem dışı barındırma arasındaki farklar</span><span class="sxs-lookup"><span data-stu-id="46ef7-142">Differences between in-process and out-of-process hosting</span></span>

<span data-ttu-id="46ef7-143">İşlem içi barındırma sırasında aşağıdaki özellikler geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="46ef7-143">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="46ef7-144">Kestrel Server yerine IIS HTTP sunucusu ( `IISHttpServer` ) kullanılır [Kestrel](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="46ef7-144">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="46ef7-145">İşlem içi için şunu [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> :</span><span class="sxs-lookup"><span data-stu-id="46ef7-145">For in-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> to:</span></span>

  * <span data-ttu-id="46ef7-146">Kaydolun `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="46ef7-146">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="46ef7-147">ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="46ef7-147">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="46ef7-148">Konağı, başlatma hatalarını yakalamak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="46ef7-148">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="46ef7-149">[ `requestTimeout` Özniteliği](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) işlem içi barındırma için uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="46ef7-149">The [`requestTimeout` attribute](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="46ef7-150">Uygulama havuzunu uygulamalar arasında paylaşma desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="46ef7-150">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="46ef7-151">Uygulama başına bir uygulama havuzu kullanın.</span><span class="sxs-lookup"><span data-stu-id="46ef7-151">Use one app pool per app.</span></span>

* <span data-ttu-id="46ef7-152">Uygulamanın mimarisi (bit genişliği) ve yüklü çalışma zamanının (x64 veya x86) uygulama havuzunun mimarisiyle eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="46ef7-152">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span> <span data-ttu-id="46ef7-153">Örneğin, 32 bit (x86) için yayımlanan uygulamalarda IIS uygulama havuzları için 32 bit etkinleştirilmiş olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="46ef7-153">For example, apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="46ef7-154">Daha fazla bilgi için [IIS sitesini oluşturma](xref:tutorials/publish-to-iis#create-the-iis-site) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="46ef7-154">For more information, see the [Create the IIS site](xref:tutorials/publish-to-iis#create-the-iis-site) section.</span></span>

* <span data-ttu-id="46ef7-155">İstemci bağlantısı kesiliyor algılandı.</span><span class="sxs-lookup"><span data-stu-id="46ef7-155">Client disconnects are detected.</span></span> <span data-ttu-id="46ef7-156">[`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A)İstemci bağlantısı kesildiğinde iptal belirteci iptal edilir.</span><span class="sxs-lookup"><span data-stu-id="46ef7-156">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="46ef7-157">İşlem içi barındırma sırasında, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> bir kullanıcıyı başlatmak için dahili olarak çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="46ef7-157">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> isn't called internally to initialize a user.</span></span> <span data-ttu-id="46ef7-158">Bu nedenle, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> her kimlik doğrulaması sonrasında talepleri dönüştürmek için kullanılan bir uygulama varsayılan olarak etkinleştirilmez.</span><span class="sxs-lookup"><span data-stu-id="46ef7-158">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="46ef7-159">Talepleri bir <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> uygulamayla dönüştürürken, <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> kimlik doğrulama hizmetleri Ekle ' yi çağırın:</span><span class="sxs-lookup"><span data-stu-id="46ef7-159">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
* <span data-ttu-id="46ef7-160">[Web paketi (tek dosya) dağıtımları](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="46ef7-160">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>
