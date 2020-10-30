---
title: IIS ve ASP.NET Core ile işlem dışı barındırma
author: rick-anderson
description: IIS ve ASP.NET Core modülüyle işlem dışı barındırma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 78ead27bd1373237d1c0a48655d73a41a0a72279
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060423"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="e4bd6-103">IIS ve ASP.NET Core ile işlem dışı barındırma</span><span class="sxs-lookup"><span data-stu-id="e4bd6-103">Out-of-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="e4bd6-104">ASP.NET Core uygulamalar IIS çalışan işleminden ayrı bir işlemde çalıştığından, ASP.NET Core modülü işlem yönetimini işler.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-104">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="e4bd6-105">Modül, ilk istek ulaştığında ASP.NET Core uygulama için işlemi başlatır ve kapanırsa veya kilitlenirse uygulamayı yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-105">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="e4bd6-106">Bu aslında, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen işlem içi uygulamalarla birlikte görülen davranışdır.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-106">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="e4bd6-107">Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve işlem dışı barındırılan bir uygulama arasındaki ilişki gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="e4bd6-107">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![İşlem dışı barındırma senaryosunda modül ASP.NET Core](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="e4bd6-109">İstekler Web 'den çekirdek modu HTTP.sys sürücüsüne ulaşır.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-109">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="e4bd6-110">Sürücü, Web sitesinin yapılandırılmış bağlantı noktasında istekleri IIS 'ye yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-110">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="e4bd6-111">Yapılandırılmış bağlantı noktası genellikle 80 (HTTP) veya 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="e4bd6-111">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="e4bd6-112">Modül, isteği uygulama için rastgele bir bağlantı noktasında Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-112">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="e4bd6-113">Rastgele bağlantı noktası 80 veya 443 değildir.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-113">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="e4bd6-114">ASP.NET Core modülü başlatma sırasında bir ortam değişkeni aracılığıyla bağlantı noktasını belirtir.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-114">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="e4bd6-115"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A>Uzantı, sunucuyu dinlemek üzere yapılandırır `http://localhost:{PORT}` .</span><span class="sxs-lookup"><span data-stu-id="e4bd6-115">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="e4bd6-116">Ek denetimler gerçekleştirilir ve modülünden kaynaklanmayan istekler reddedilir.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-116">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="e4bd6-117">Modül HTTPS iletmeyi desteklemiyor.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-117">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="e4bd6-118">İstekler, HTTPS üzerinden IIS tarafından alınsa bile HTTP üzerinden iletilir.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-118">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="e4bd6-119">Kestrel, isteği modülden başlattıktan sonra, istek ASP.NET Core ara yazılım ardışık düzenine iletilir.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-119">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="e4bd6-120">Ara yazılım ardışık düzeni isteği işler ve `HttpContext` uygulamanın mantığına bir örnek olarak geçirir.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-120">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="e4bd6-121">IIS tümleştirmesi tarafından eklenen ara yazılım, isteği Kestrel iletmek için düzen, uzak IP ve pathbase 'i hesaba göre güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-121">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="e4bd6-122">Uygulamanın yanıtı IIS 'e geri geçirilir ve bu, isteği başlatan HTTP istemcisine geri iletilir.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-122">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="e4bd6-123">ASP.NET Core modülü yapılandırma kılavuzu için bkz <xref:host-and-deploy/aspnet-core-module> ..</span><span class="sxs-lookup"><span data-stu-id="e4bd6-123">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="e4bd6-124">Barındırma hakkında daha fazla bilgi için bkz. [ASP.NET Core ana bilgisayar](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="e4bd6-124">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="e4bd6-125">Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e4bd6-125">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="e4bd6-126">Iısıntegration bileşenlerini etkinleştirin</span><span class="sxs-lookup"><span data-stu-id="e4bd6-126">Enable the IISIntegration components</span></span>

<span data-ttu-id="e4bd6-127">() İçinde bir konak `CreateHostBuilder` oluştururken `Program.cs` <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> IIS tümleştirmesini etkinleştirmek için çağırın:</span><span class="sxs-lookup"><span data-stu-id="e4bd6-127">When building a host in `CreateHostBuilder` (`Program.cs`), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="e4bd6-128">Hakkında daha fazla bilgi için `CreateDefaultBuilder` bkz <xref:fundamentals/host/generic-host#default-builder-settings> ..</span><span class="sxs-lookup"><span data-stu-id="e4bd6-128">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>


<span data-ttu-id="e4bd6-129">**İşlem dışı barındırma modeli**</span><span class="sxs-lookup"><span data-stu-id="e4bd6-129">**Out-of-process hosting model**</span></span>

<span data-ttu-id="e4bd6-130">IIS seçeneklerini yapılandırmak için, içinde için bir hizmet yapılandırması <xref:Microsoft.AspNetCore.Builder.IISOptions> ekleyin <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> .</span><span class="sxs-lookup"><span data-stu-id="e4bd6-130">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="e4bd6-131">Aşağıdaki örnek, uygulamanın doldurulmasını önler `HttpContext.Connection.ClientCertificate` :</span><span class="sxs-lookup"><span data-stu-id="e4bd6-131">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="e4bd6-132">Seçenek</span><span class="sxs-lookup"><span data-stu-id="e4bd6-132">Option</span></span>                         | <span data-ttu-id="e4bd6-133">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="e4bd6-133">Default</span></span> | <span data-ttu-id="e4bd6-134">Ayar</span><span class="sxs-lookup"><span data-stu-id="e4bd6-134">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="e4bd6-135">`true` [IIS tümleştirme ara yazılımı](#enable-the-iisintegration-components) , `HttpContext.User` kimliği doğrulanmış [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)tarafından ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-135">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="e4bd6-136">İse `false` , ara yazılım için yalnızca bir kimlik sağlar `HttpContext.User` ve tarafından açıkça istendiğinde sorunları yanıtlar `AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="e4bd6-136">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="e4bd6-137">' Nin çalışması için IIS 'de Windows kimlik doğrulamasının etkinleştirilmesi gerekir `AutomaticAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="e4bd6-137">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="e4bd6-138">Daha fazla bilgi için [Windows kimlik doğrulaması](xref:security/authentication/windowsauth) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-138">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="e4bd6-139">Oturum açma sayfalarındaki kullanıcılara gösterilen görünen adı ayarlar.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-139">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="e4bd6-140">`true`Ve `MS-ASPNETCORE-CLIENTCERT` istek üst bilgisi varsa, `HttpContext.Connection.ClientCertificate` doldurulur.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-140">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |


### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e4bd6-141">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="e4bd6-141">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e4bd6-142">[IIS tümleştirme ara yazılımı](#enable-the-iisintegration-components) ve ASP.NET Core modülü, iletmek üzere yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="e4bd6-142">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="e4bd6-143">Düzen (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="e4bd6-143">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="e4bd6-144">İsteğin kaynaklandığı uzak IP adresi.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-144">Remote IP address where the request originated.</span></span>

<span data-ttu-id="e4bd6-145">[IIS tümleştirme ara yazılımı](#enable-the-iisintegration-components) , Iletilen üstbilgiler ara yazılımını yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-145">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="e4bd6-146">Ek proxy sunucularının ve yük dengeleyiciler arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-146">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="e4bd6-147">Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="e4bd6-147">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>


### <a name="out-of-process-hosting-model"></a><span data-ttu-id="e4bd6-148">İşlem dışı barındırma modeli</span><span class="sxs-lookup"><span data-stu-id="e4bd6-148">Out-of-process hosting model</span></span>

<span data-ttu-id="e4bd6-149">Bir uygulamayı işlem dışı barındırmak üzere yapılandırmak için, `<AspNetCoreHostingModel>` özelliğinin değerini `OutOfProcess` Proje dosyasında () olarak ayarlayın `.csproj` :</span><span class="sxs-lookup"><span data-stu-id="e4bd6-149">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="e4bd6-150">İşlem içi barındırma `InProcess` , varsayılan değer olan ile ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-150">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="e4bd6-151">Değeri `<AspNetCoreHostingModel>` büyük/küçük harfe duyarlıdır `inprocess` ve `outofprocess` geçerli değerlerdir.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-151">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="e4bd6-152">IIS HTTP Server () yerine [Kestrel](xref:fundamentals/servers/kestrel) Server kullanılır `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="e4bd6-152">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="e4bd6-153">İşlem dışı için şunu [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> :</span><span class="sxs-lookup"><span data-stu-id="e4bd6-153">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> to:</span></span>

* <span data-ttu-id="e4bd6-154">ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-154">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="e4bd6-155">Konağı, başlatma hatalarını yakalamak üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-155">Configure the host to capture startup errors.</span></span>

### <a name="process-name"></a><span data-ttu-id="e4bd6-156">İşlem adı</span><span class="sxs-lookup"><span data-stu-id="e4bd6-156">Process name</span></span>

<span data-ttu-id="e4bd6-157">`Process.GetCurrentProcess().ProcessName`raporlar `w3wp` / `iisexpress` (işlem içi) veya `dotnet` (işlem dışı).</span><span class="sxs-lookup"><span data-stu-id="e4bd6-157">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="e4bd6-158">Windows kimlik doğrulaması gibi birçok yerel modül etkin kalır.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-158">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="e4bd6-159">ASP.NET Core modülüyle etkin IIS modülleri hakkında daha fazla bilgi edinmek için bkz <xref:host-and-deploy/iis/modules> ..</span><span class="sxs-lookup"><span data-stu-id="e4bd6-159">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="e4bd6-160">ASP.NET Core modülü de şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="e4bd6-160">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="e4bd6-161">Çalışan işlem için ortam değişkenlerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-161">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="e4bd6-162">Başlatma sorunlarını gidermek için stdout çıkışını dosya depolama alanına kaydedin.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-162">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="e4bd6-163">Windows kimlik doğrulama belirteçlerini ilet.</span><span class="sxs-lookup"><span data-stu-id="e4bd6-163">Forward Windows authentication tokens.</span></span>
