---
title: ASP.NET Core temelleri
author: rick-anderson
description: ASP.NET Core uygulamalar oluşturmaya yönelik temel kavramları öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- appsettings.json
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
uid: fundamentals/index
ms.openlocfilehash: 27b182394abe12a1631e5ba350942904bf4094aa
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035898"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="d5da4-103">ASP.NET Core temelleri</span><span class="sxs-lookup"><span data-stu-id="d5da4-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d5da4-104">Bu makalede, ASP.NET Core uygulamaları geliştirmeyi anlamak için temel konulara genel bakış sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-104">This article provides an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="d5da4-105">Başlangıç sınıfı</span><span class="sxs-lookup"><span data-stu-id="d5da4-105">The Startup class</span></span>

<span data-ttu-id="d5da4-106">`Startup`Sınıfı şu konumda:</span><span class="sxs-lookup"><span data-stu-id="d5da4-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="d5da4-107">Uygulamanın gerektirdiği hizmetler yapılandırıldı.</span><span class="sxs-lookup"><span data-stu-id="d5da4-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="d5da4-108">Uygulamanın istek işleme işlem hattı, bir dizi ara yazılım bileşeni olarak tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-108">The app's request handling pipeline is defined, as a series of middleware components.</span></span>

<span data-ttu-id="d5da4-109">Örnek bir sınıf aşağıda verilmiştir `Startup` :</span><span class="sxs-lookup"><span data-stu-id="d5da4-109">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="d5da4-110">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-110">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="d5da4-111">Bağımlılık ekleme (hizmetler)</span><span class="sxs-lookup"><span data-stu-id="d5da4-111">Dependency injection (services)</span></span>

<span data-ttu-id="d5da4-112">ASP.NET Core, yapılandırılmış Hizmetleri bir uygulama genelinde kullanılabilir hale getiren yerleşik bağımlılık ekleme (dı) çerçevesini içerir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-112">ASP.NET Core includes a built-in dependency injection (DI) framework that makes configured services available throughout an app.</span></span> <span data-ttu-id="d5da4-113">Örneğin, bir günlük bileşeni bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-113">For example, a logging component is a service.</span></span>

<span data-ttu-id="d5da4-114">Hizmetleri yapılandırmak (veya *kaydettirmek*) için kod `Startup.ConfigureServices` yöntemine eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-114">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d5da4-115">Örnek:</span><span class="sxs-lookup"><span data-stu-id="d5da4-115">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

<span data-ttu-id="d5da4-116">Hizmetler genellikle Oluşturucu Ekleme kullanılarak dı 'den çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-116">Services are typically resolved from DI using constructor injection.</span></span> <span data-ttu-id="d5da4-117">Oluşturucu ekleme ile bir sınıf, gerekli türde veya bir arabirimin Oluşturucu parametresini bildirir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-117">With constructor injection, a class declares a constructor parameter of either the required type or an interface.</span></span> <span data-ttu-id="d5da4-118">Dı çerçevesi, çalışma zamanında bu hizmetin bir örneğini sağlar.</span><span class="sxs-lookup"><span data-stu-id="d5da4-118">The DI framework provides an instance of this service at runtime.</span></span>

<span data-ttu-id="d5da4-119">Aşağıdaki örnek, from kaynağından çözümlemek için Oluşturucu Ekleme kullanır `RazorPagesMovieContext` :</span><span class="sxs-lookup"><span data-stu-id="d5da4-119">The following example uses constructor injection to resolve a `RazorPagesMovieContext` from DI:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="d5da4-120">Yerleşik bir denetim (IOC) kapsayıcısının bir uygulamanın ihtiyaçlarını karşılamıyorsa, bunun yerine üçüncü taraf bir IOC kapsayıcısı kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-120">If the built-in Inversion of Control (IoC) container doesn't meet all of an app's needs, a third-party IoC container can be used instead.</span></span>

<span data-ttu-id="d5da4-121">Daha fazla bilgi için bkz. <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-121">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="d5da4-122">Ara yazılım</span><span class="sxs-lookup"><span data-stu-id="d5da4-122">Middleware</span></span>

<span data-ttu-id="d5da4-123">İstek işleme işlem hattı, bir dizi ara yazılım bileşeni olarak oluşur.</span><span class="sxs-lookup"><span data-stu-id="d5da4-123">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="d5da4-124">Her bileşen bir üzerinde işlemler gerçekleştirir `HttpContext` ve sıradaki bir sonraki ara yazılımı çağırır ya da isteği sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-124">Each component performs operations on an `HttpContext` and either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="d5da4-125">Kurala göre, yöntemde bir genişletme yöntemi çağırarak işlem hattına bir ara yazılım bileşeni eklenir `Use...` `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d5da4-125">By convention, a middleware component is added to the pipeline by invoking a `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="d5da4-126">Örneğin, statik dosyaları işlemeyi etkinleştirmek için çağırın `UseStaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="d5da4-126">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="d5da4-127">Aşağıdaki örnekte bir istek işleme işlem hattı yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="d5da4-127">The following example configures a request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

<span data-ttu-id="d5da4-128">ASP.NET Core zengin bir yerleşik ara yazılım kümesi içerir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-128">ASP.NET Core includes a rich set of built-in middleware.</span></span> <span data-ttu-id="d5da4-129">Özel ara yazılım bileşenleri de yazılabilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-129">Custom middleware components can also be written.</span></span>

<span data-ttu-id="d5da4-130">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-130">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="d5da4-131">Yönetici</span><span class="sxs-lookup"><span data-stu-id="d5da4-131">Host</span></span>

<span data-ttu-id="d5da4-132">Başlangıçta ASP.NET Core bir uygulama bir *konak* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d5da4-132">On startup, an ASP.NET Core app builds a *host*.</span></span> <span data-ttu-id="d5da4-133">Konak, uygulamanın tüm kaynaklarını kapsüller, örneğin:</span><span class="sxs-lookup"><span data-stu-id="d5da4-133">The host encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="d5da4-134">Bir HTTP sunucusu uygulama</span><span class="sxs-lookup"><span data-stu-id="d5da4-134">An HTTP server implementation</span></span>
* <span data-ttu-id="d5da4-135">Ara yazılım bileşenleri</span><span class="sxs-lookup"><span data-stu-id="d5da4-135">Middleware components</span></span>
* <span data-ttu-id="d5da4-136">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="d5da4-136">Logging</span></span>
* <span data-ttu-id="d5da4-137">Bağımlılık ekleme (dı) Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="d5da4-137">Dependency injection (DI) services</span></span>
* <span data-ttu-id="d5da4-138">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d5da4-138">Configuration</span></span>

<span data-ttu-id="d5da4-139">İki farklı ana bilgisayar vardır:</span><span class="sxs-lookup"><span data-stu-id="d5da4-139">There are two different hosts:</span></span> 

* <span data-ttu-id="d5da4-140">.NET genel ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="d5da4-140">.NET Generic Host</span></span>
* <span data-ttu-id="d5da4-141">ASP.NET Core Web ana bilgisayarı</span><span class="sxs-lookup"><span data-stu-id="d5da4-141">ASP.NET Core Web Host</span></span>

<span data-ttu-id="d5da4-142">.NET genel ana bilgisayarı önerilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-142">The .NET Generic Host is recommended.</span></span> <span data-ttu-id="d5da4-143">ASP.NET Core Web konağı yalnızca geriye dönük uyumluluk için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-143">The ASP.NET Core Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="d5da4-144">Aşağıdaki örnek bir .NET genel ana bilgisayarı oluşturur:</span><span class="sxs-lookup"><span data-stu-id="d5da4-144">The following example creates a .NET Generic Host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

<span data-ttu-id="d5da4-145">`CreateDefaultBuilder`Ve `ConfigureWebHostDefaults` yöntemleri, bir ana bilgisayarı varsayılan bir seçenek kümesiyle yapılandırır, örneğin:</span><span class="sxs-lookup"><span data-stu-id="d5da4-145">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with a set of default options, such as:</span></span>

* <span data-ttu-id="d5da4-146">Web sunucusu olarak [Kestrel](#servers) kullanın ve IIS tümleştirmesini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="d5da4-146">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="d5da4-147">Yapılandırmayı yükleme *appsettings.json* , *appSettings. { Ortam adı}. JSON*, ortam değişkenleri, komut satırı bağımsız değişkenleri ve diğer yapılandırma kaynakları.</span><span class="sxs-lookup"><span data-stu-id="d5da4-147">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="d5da4-148">Günlüğe kaydetme çıkışını konsola ve hata ayıklama sağlayıcılarına gönderin.</span><span class="sxs-lookup"><span data-stu-id="d5da4-148">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="d5da4-149">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-149">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="d5da4-150">Web dışı senaryolar</span><span class="sxs-lookup"><span data-stu-id="d5da4-150">Non-web scenarios</span></span>

<span data-ttu-id="d5da4-151">Genel ana bilgisayar, diğer uygulama türlerinin günlüğe kaydetme, bağımlılık ekleme (dı), yapılandırma ve uygulama ömür yönetimi gibi çapraz kesme çerçevesi uzantıları kullanmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-151">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="d5da4-152">Daha fazla bilgi için <xref:fundamentals/host/generic-host> ve <xref:fundamentals/host/hosted-services> bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="d5da4-152">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="d5da4-153">Sunucular</span><span class="sxs-lookup"><span data-stu-id="d5da4-153">Servers</span></span>

<span data-ttu-id="d5da4-154">Bir ASP.NET Core uygulaması HTTP isteklerini dinlemek için HTTP sunucu uygulamasını kullanır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-154">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="d5da4-155">Sunucu, bir öğesine oluşturulan [istek özellikleri](xref:fundamentals/request-features) kümesi olarak uygulamaya istekleri ister `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="d5da4-155">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="d5da4-156">Windows</span><span class="sxs-lookup"><span data-stu-id="d5da4-156">Windows</span></span>](#tab/windows)

<span data-ttu-id="d5da4-157">ASP.NET Core aşağıdaki sunucu uygulamalarını sağlar:</span><span class="sxs-lookup"><span data-stu-id="d5da4-157">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="d5da4-158">*Kestrel* , platformlar arası bir Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="d5da4-158">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="d5da4-159">Kestrel, genellikle [IIS](https://www.iis.net/)kullanılarak ters bir ara sunucu yapılandırmasında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-159">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="d5da4-160">ASP.NET Core 2,0 veya üzeri sürümlerde, Kestrel doğrudan Internet 'e açık olan bir genel kullanıma yönelik uç sunucu olarak çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-160">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="d5da4-161">*IIS HTTP sunucusu* , IIS kullanan bir Windows sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="d5da4-161">*IIS HTTP Server* is a server for Windows that uses IIS.</span></span> <span data-ttu-id="d5da4-162">Bu sunucu ile, ASP.NET Core uygulaması ve IIS aynı işlemde çalışır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-162">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="d5da4-163">*HTTP.sys* , IIS ile kullanılmayan bir Windows sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="d5da4-163">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="d5da4-164">macOS</span><span class="sxs-lookup"><span data-stu-id="d5da4-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="d5da4-165">ASP.NET Core, *Kestrel* platformlar arası sunucu uygulamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="d5da4-165">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="d5da4-166">ASP.NET Core 2,0 veya üzeri sürümlerde, Kestrel doğrudan Internet 'Te kullanıma sunulan bir genel kullanıma yönelik uç sunucu olarak çalışabilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-166">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="d5da4-167">Kestrel, genellikle [NGINX](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmasında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-167">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="d5da4-168">Linux</span><span class="sxs-lookup"><span data-stu-id="d5da4-168">Linux</span></span>](#tab/linux)

<span data-ttu-id="d5da4-169">ASP.NET Core, *Kestrel* platformlar arası sunucu uygulamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="d5da4-169">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="d5da4-170">ASP.NET Core 2,0 veya üzeri sürümlerde, Kestrel doğrudan Internet 'Te kullanıma sunulan bir genel kullanıma yönelik uç sunucu olarak çalışabilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-170">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="d5da4-171">Kestrel, genellikle [NGINX](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmasında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-171">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="d5da4-172">Daha fazla bilgi için bkz. <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-172">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="d5da4-173">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d5da4-173">Configuration</span></span>

<span data-ttu-id="d5da4-174">ASP.NET Core, ayarları sıralı bir yapılandırma sağlayıcıları kümesinden ad-değer çiftleri olarak alan bir yapılandırma çerçevesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="d5da4-174">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="d5da4-175">Yerleşik yapılandırma sağlayıcıları *. JSON* dosyaları, *. xml* dosyaları, ortam değişkenleri ve komut satırı bağımsız değişkenleri gibi çeşitli kaynaklar için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-175">Built-in configuration providers are available for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="d5da4-176">Diğer kaynakları desteklemek için özel yapılandırma sağlayıcıları yazın.</span><span class="sxs-lookup"><span data-stu-id="d5da4-176">Write custom configuration providers to support other sources.</span></span>

<span data-ttu-id="d5da4-177">[Varsayılan](xref:fundamentals/configuration/index#default)olarak, ASP.NET Core uygulamalar *appsettings.json* , ortam değişkenleri, komut satırı ve daha fazlasını okumak üzere yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-177">By [default](xref:fundamentals/configuration/index#default), ASP.NET Core apps are configured to read from *appsettings.json*, environment variables, the command line, and more.</span></span> <span data-ttu-id="d5da4-178">Uygulamanın yapılandırması yüklendiğinde, ortam değişkenlerinin değerleri, değerlerini geçersiz kılar *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="d5da4-178">When the app's configuration is loaded, values from environment variables override values from *appsettings.json*.</span></span>

<span data-ttu-id="d5da4-179">İlgili yapılandırma değerlerini okumak için tercih edilen yol, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-179">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="d5da4-180">Daha fazla bilgi için bkz. [Seçenekler modelini kullanarak hiyerarşik yapılandırma verilerini bağlama](xref:fundamentals/configuration/index#optpat).</span><span class="sxs-lookup"><span data-stu-id="d5da4-180">For more information, see [Bind hierarchical configuration data using the options pattern](xref:fundamentals/configuration/index#optpat).</span></span>

<span data-ttu-id="d5da4-181">Parolalar gibi gizli yapılandırma verilerini yönetmek için, .NET Core, gizli dizi [Yöneticisi](xref:security/app-secrets#secret-manager)sağlar.</span><span class="sxs-lookup"><span data-stu-id="d5da4-181">For managing confidential configuration data such as passwords, .NET Core provides the [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="d5da4-182">Üretim gizli dizileri için [Azure Key Vault](xref:security/key-vault-configuration)önerilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-182">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="d5da4-183">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-183">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="environments"></a><span data-ttu-id="d5da4-184">Ortamlar</span><span class="sxs-lookup"><span data-stu-id="d5da4-184">Environments</span></span>

<span data-ttu-id="d5da4-185">, Ve gibi yürütme ortamları `Development` `Staging` `Production` ASP.NET Core ilk sınıf kavramlardır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-185">Execution environments, such as `Development`, `Staging`, and `Production`, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="d5da4-186">Ortam değişkenini ayarlayarak bir uygulamanın çalıştığı ortamı belirtin `ASPNETCORE_ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="d5da4-186">Specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="d5da4-187">ASP.NET Core, uygulamanın başlangıcında bu ortam değişkenini okur ve değeri bir `IWebHostEnvironment` uygulamada depolar.</span><span class="sxs-lookup"><span data-stu-id="d5da4-187">ASP.NET Core reads that environment variable at app startup and stores the value in an `IWebHostEnvironment` implementation.</span></span> <span data-ttu-id="d5da4-188">Bu uygulama, bağımlılık ekleme (dı) yoluyla bir uygulamada herhangi bir yerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-188">This implementation is available anywhere in an app via dependency injection (DI).</span></span>

<span data-ttu-id="d5da4-189">Aşağıdaki örnek, uygulamayı ortamda çalışırken ayrıntılı hata bilgileri sunacak şekilde yapılandırır `Development` :</span><span class="sxs-lookup"><span data-stu-id="d5da4-189">The following example configures the app to provide detailed error information when running in the `Development` environment:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="d5da4-190">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-190">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="d5da4-191">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="d5da4-191">Logging</span></span>

<span data-ttu-id="d5da4-192">ASP.NET Core, çeşitli yerleşik ve üçüncü taraf günlük sağlayıcılarıyla birlikte çalışarak bir günlüğe kaydetme API 'sini destekler.</span><span class="sxs-lookup"><span data-stu-id="d5da4-192">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="d5da4-193">Kullanılabilir sağlayıcılar şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="d5da4-193">Available providers include:</span></span>

* <span data-ttu-id="d5da4-194">Konsol</span><span class="sxs-lookup"><span data-stu-id="d5da4-194">Console</span></span>
* <span data-ttu-id="d5da4-195">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="d5da4-195">Debug</span></span>
* <span data-ttu-id="d5da4-196">Windows üzerinde olay Izleme</span><span class="sxs-lookup"><span data-stu-id="d5da4-196">Event Tracing on Windows</span></span>
* <span data-ttu-id="d5da4-197">Windows olay günlüğü</span><span class="sxs-lookup"><span data-stu-id="d5da4-197">Windows Event Log</span></span>
* <span data-ttu-id="d5da4-198">TraceSource</span><span class="sxs-lookup"><span data-stu-id="d5da4-198">TraceSource</span></span>
* <span data-ttu-id="d5da4-199">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="d5da4-199">Azure App Service</span></span>
* <span data-ttu-id="d5da4-200">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="d5da4-200">Azure Application Insights</span></span>

<span data-ttu-id="d5da4-201">Günlükler oluşturmak için, bir <xref:Microsoft.Extensions.Logging.ILogger%601> hizmeti bağımlılık ekleme (dı) ve çağrı günlüğü yöntemlerinden bir hizmete çözümleyin <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> .</span><span class="sxs-lookup"><span data-stu-id="d5da4-201">To create logs, resolve an <xref:Microsoft.Extensions.Logging.ILogger%601> service from dependency injection (DI) and call logging methods such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>.</span></span> <span data-ttu-id="d5da4-202">Örnek:</span><span class="sxs-lookup"><span data-stu-id="d5da4-202">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

<span data-ttu-id="d5da4-203">Gibi günlüğe kaydetme yöntemleri `LogInformation` , herhangi bir sayıda alanı destekler.</span><span class="sxs-lookup"><span data-stu-id="d5da4-203">Logging methods such as `LogInformation` support any number of fields.</span></span> <span data-ttu-id="d5da4-204">Bu alanlar yaygın olarak bir ileti oluşturmak için kullanılır `string` , ancak bazı günlük sağlayıcıları bunları ayrı alanlar olarak bir veri deposuna gönderir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-204">These fields are commonly used to construct a message `string`, but some logging providers send these to a data store as separate fields.</span></span> <span data-ttu-id="d5da4-205">Bu özellik, günlük sağlayıcılarının [yapılandırılmış günlük olarak da bilinen anlam günlüğü](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)uygulamasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-205">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="d5da4-206">Daha fazla bilgi için bkz. <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-206">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="d5da4-207">Yönlendirme</span><span class="sxs-lookup"><span data-stu-id="d5da4-207">Routing</span></span>

<span data-ttu-id="d5da4-208">*Yol* , bir işleyiciye EŞLENMIŞ bir URL örüncidir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-208">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="d5da4-209">İşleyici genellikle bir Razor sayfa, MVC denetleyicisindeki bir eylem yöntemi veya bir ara yazılım olur.</span><span class="sxs-lookup"><span data-stu-id="d5da4-209">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="d5da4-210">ASP.NET Core yönlendirme, uygulamanız tarafından kullanılan URL 'Ler üzerinde denetim sağlar.</span><span class="sxs-lookup"><span data-stu-id="d5da4-210">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="d5da4-211">Daha fazla bilgi için bkz. <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-211">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="d5da4-212">Hata işleme</span><span class="sxs-lookup"><span data-stu-id="d5da4-212">Error handling</span></span>

<span data-ttu-id="d5da4-213">ASP.NET Core, hataları işlemeye yönelik yerleşik özellikler içerir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="d5da4-213">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="d5da4-214">Geliştirici özel durum sayfası</span><span class="sxs-lookup"><span data-stu-id="d5da4-214">A developer exception page</span></span>
* <span data-ttu-id="d5da4-215">Özel hata sayfaları</span><span class="sxs-lookup"><span data-stu-id="d5da4-215">Custom error pages</span></span>
* <span data-ttu-id="d5da4-216">Statik durum kodu sayfaları</span><span class="sxs-lookup"><span data-stu-id="d5da4-216">Static status code pages</span></span>
* <span data-ttu-id="d5da4-217">Başlatma özel durum işleme</span><span class="sxs-lookup"><span data-stu-id="d5da4-217">Startup exception handling</span></span>

<span data-ttu-id="d5da4-218">Daha fazla bilgi için bkz. <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-218">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="d5da4-219">HTTP isteğinde bulunma</span><span class="sxs-lookup"><span data-stu-id="d5da4-219">Make HTTP requests</span></span>

<span data-ttu-id="d5da4-220">Uygulamasının bir uygulamasına `IHttpClientFactory` örnek oluşturmak için kullanılabilir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="d5da4-220">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="d5da4-221">Fabrika:</span><span class="sxs-lookup"><span data-stu-id="d5da4-221">The factory:</span></span>

* <span data-ttu-id="d5da4-222">, Mantıksal örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="d5da4-222">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="d5da4-223">Örneğin, GitHub 'a erişmek için bir *GitHub* istemcisini kaydedin ve yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="d5da4-223">For example, register and configure a *github* client for accessing GitHub.</span></span> <span data-ttu-id="d5da4-224">Varsayılan bir istemciyi başka amaçlar için kaydedin ve yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="d5da4-224">Register and configure a default client for other purposes.</span></span>
* <span data-ttu-id="d5da4-225">, Bir giden istek ara yazılım işlem hattı oluşturmak için birden çok temsilci seçme işleyicisinin kaydını ve zincirlemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="d5da4-225">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="d5da4-226">Bu düzen ASP.NET Core gelen ara yazılım ardışık düzenine benzer.</span><span class="sxs-lookup"><span data-stu-id="d5da4-226">This pattern is similar to ASP.NET Core's inbound middleware pipeline.</span></span> <span data-ttu-id="d5da4-227">Bu model, önbelleğe alma, hata işleme, serileştirme ve günlüğe kaydetme dahil olmak üzere HTTP istekleri için çapraz kesme sorunlarını yönetmek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="d5da4-227">The pattern provides a mechanism to manage cross-cutting concerns for HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="d5da4-228">Geçici hata işleme için popüler bir üçüncü taraf kitaplığı olan *Polly* ile tümleşir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-228">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="d5da4-229">`HttpClientHandler`Yaşam sürelerini el ile yönetirken oluşan YAYGıN DNS sorunlarından kaçınmak için temeldeki örneklerin biriktirmesini ve ömrünü yönetir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="d5da4-229">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when managing `HttpClient` lifetimes manually.</span></span>
* <span data-ttu-id="d5da4-230"><xref:Microsoft.Extensions.Logging.ILogger>Fabrika tarafından oluşturulan istemciler aracılığıyla gönderilen tüm istekler için aracılığıyla yapılandırılabilir bir günlük deneyimi ekler.</span><span class="sxs-lookup"><span data-stu-id="d5da4-230">Adds a configurable logging experience via <xref:Microsoft.Extensions.Logging.ILogger> for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="d5da4-231">Daha fazla bilgi için bkz. <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-231">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="d5da4-232">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="d5da4-232">Content root</span></span>

<span data-ttu-id="d5da4-233">İçerik kökü, için temel yoldur:</span><span class="sxs-lookup"><span data-stu-id="d5da4-233">The content root is the base path for:</span></span>

* <span data-ttu-id="d5da4-234">Uygulamayı barındıran yürütülebilir dosya (*. exe*).</span><span class="sxs-lookup"><span data-stu-id="d5da4-234">The executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="d5da4-235">Uygulamayı oluşturan derlenmiş derlemeler (*. dll*).</span><span class="sxs-lookup"><span data-stu-id="d5da4-235">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="d5da4-236">Uygulama tarafından kullanılan içerik dosyaları, örneğin:</span><span class="sxs-lookup"><span data-stu-id="d5da4-236">Content files used by the app, such as:</span></span>
  * <span data-ttu-id="d5da4-237">Razor dosyalar (*. cshtml*, *. Razor*)</span><span class="sxs-lookup"><span data-stu-id="d5da4-237">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="d5da4-238">Yapılandırma dosyaları (*. JSON*, *. xml*)</span><span class="sxs-lookup"><span data-stu-id="d5da4-238">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="d5da4-239">Veri dosyaları (*. db*)</span><span class="sxs-lookup"><span data-stu-id="d5da4-239">Data files (*.db*)</span></span>
* <span data-ttu-id="d5da4-240">[Web kökü](#web-root), genellikle *Wwwroot* klasörü.</span><span class="sxs-lookup"><span data-stu-id="d5da4-240">The [Web root](#web-root), typically the *wwwroot* folder.</span></span>

<span data-ttu-id="d5da4-241">Geliştirme sırasında, içerik kökü, projenin kök dizinini varsayılan olarak belirler.</span><span class="sxs-lookup"><span data-stu-id="d5da4-241">During development, the content root defaults to the project's root directory.</span></span> <span data-ttu-id="d5da4-242">Bu dizin Ayrıca uygulamanın içerik dosyaları ve [Web kökünün](#web-root)temel yoludur.</span><span class="sxs-lookup"><span data-stu-id="d5da4-242">This directory is also the base path for both the app's content files and the [Web root](#web-root).</span></span> <span data-ttu-id="d5da4-243">[Konağı oluştururken](#host)yolunu ayarlayarak farklı bir içerik kökü belirtin.</span><span class="sxs-lookup"><span data-stu-id="d5da4-243">Specify a different content root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="d5da4-244">Daha fazla bilgi için bkz. [içerik kökü](xref:fundamentals/host/generic-host#contentroot).</span><span class="sxs-lookup"><span data-stu-id="d5da4-244">For more information, see [Content root](xref:fundamentals/host/generic-host#contentroot).</span></span>

## <a name="web-root"></a><span data-ttu-id="d5da4-245">Web kökü</span><span class="sxs-lookup"><span data-stu-id="d5da4-245">Web root</span></span>

<span data-ttu-id="d5da4-246">Web kökü, genel, statik kaynak dosyaları için temel yoldur, örneğin:</span><span class="sxs-lookup"><span data-stu-id="d5da4-246">The web root is the base path for public, static resource files, such as:</span></span>

* <span data-ttu-id="d5da4-247">Stil sayfaları (*. css*)</span><span class="sxs-lookup"><span data-stu-id="d5da4-247">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="d5da4-248">JavaScript (*. js*)</span><span class="sxs-lookup"><span data-stu-id="d5da4-248">JavaScript (*.js*)</span></span>
* <span data-ttu-id="d5da4-249">Görüntüler (*. png*, *. jpg*)</span><span class="sxs-lookup"><span data-stu-id="d5da4-249">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="d5da4-250">Varsayılan olarak, statik dosyalar yalnızca Web kök dizininden ve alt dizinlerinden sunulur.</span><span class="sxs-lookup"><span data-stu-id="d5da4-250">By default, static files are served only from the web root directory and its sub-directories.</span></span> <span data-ttu-id="d5da4-251">Web kök yolu, varsayılan olarak *{Content root}/Wwwroot* olarak belirlenmiştir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-251">The web root path defaults to *{content root}/wwwroot*.</span></span> <span data-ttu-id="d5da4-252">[Konağı oluştururken](#host)yolunu ayarlayarak farklı bir Web kökü belirtin.</span><span class="sxs-lookup"><span data-stu-id="d5da4-252">Specify a different web root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="d5da4-253">Daha fazla bilgi için bkz. [Web root](xref:fundamentals/host/generic-host#webroot).</span><span class="sxs-lookup"><span data-stu-id="d5da4-253">For more information, see [Web root](xref:fundamentals/host/generic-host#webroot).</span></span>

<span data-ttu-id="d5da4-254">Proje dosyasındaki [ \<Content> Proje öğesi](/visualstudio/msbuild/common-msbuild-project-items#content) ile *Wwwroot* 'da dosya yayımlamayı önleyin.</span><span class="sxs-lookup"><span data-stu-id="d5da4-254">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="d5da4-255">Aşağıdaki örnek, *Wwwroot/yerel* ve alt dizinlerinde içerik yayımlamayı engeller:</span><span class="sxs-lookup"><span data-stu-id="d5da4-255">The following example prevents publishing content in *wwwroot/local* and its sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="d5da4-256">Razor *. Cshtml* dosyalarında, tilde-eğik çizgi ( `~/` ) Web köküne işaret eder.</span><span class="sxs-lookup"><span data-stu-id="d5da4-256">In Razor *.cshtml* files, tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="d5da4-257">İle başlayan bir yol `~/` , *sanal yol* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-257">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="d5da4-258">Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-258">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d5da4-259">Bu makale, ASP.NET Core uygulamaları geliştirmeyi anlamak için önemli konulara genel bakış sağlar.</span><span class="sxs-lookup"><span data-stu-id="d5da4-259">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="d5da4-260">Başlangıç sınıfı</span><span class="sxs-lookup"><span data-stu-id="d5da4-260">The Startup class</span></span>

<span data-ttu-id="d5da4-261">`Startup`Sınıfı şu konumda:</span><span class="sxs-lookup"><span data-stu-id="d5da4-261">The `Startup` class is where:</span></span>

* <span data-ttu-id="d5da4-262">Uygulamanın gerektirdiği hizmetler yapılandırıldı.</span><span class="sxs-lookup"><span data-stu-id="d5da4-262">Services required by the app are configured.</span></span>
* <span data-ttu-id="d5da4-263">İstek işleme işlem hattı tanımlandı.</span><span class="sxs-lookup"><span data-stu-id="d5da4-263">The request handling pipeline is defined.</span></span>

<span data-ttu-id="d5da4-264">*Hizmetler* , uygulama tarafından kullanılan bileşenlerdir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-264">*Services* are components that are used by the app.</span></span> <span data-ttu-id="d5da4-265">Örneğin, bir günlük bileşeni bir hizmettir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-265">For example, a logging component is a service.</span></span> <span data-ttu-id="d5da4-266">Hizmetleri yapılandırmak (veya *kaydettirmek*) için kod `Startup.ConfigureServices` yöntemine eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-266">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="d5da4-267">İstek işleme işlem hattı, bir dizi *Ara yazılım* bileşeni olarak oluşur.</span><span class="sxs-lookup"><span data-stu-id="d5da4-267">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="d5da4-268">Örneğin, bir ara yazılım statik dosyalar için istekleri işleyebilir veya HTTP isteklerini HTTPS 'ye yeniden yönlendirebilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-268">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="d5da4-269">Her bir ara yazılım bir üzerinde zaman uyumsuz işlemler gerçekleştirir `HttpContext` ve ardından işlem hattında sonraki ara yazılımı çağırır veya isteği sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-269">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="d5da4-270">İstek işleme işlem hattını yapılandırma kodu `Startup.Configure` yöntemine eklenir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-270">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="d5da4-271">Örnek bir sınıf aşağıda verilmiştir `Startup` :</span><span class="sxs-lookup"><span data-stu-id="d5da4-271">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="d5da4-272">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="d5da4-273">Bağımlılık ekleme (hizmetler)</span><span class="sxs-lookup"><span data-stu-id="d5da4-273">Dependency injection (services)</span></span>

<span data-ttu-id="d5da4-274">ASP.NET Core, yapılandırılmış hizmetlerin bir uygulamanın sınıfları tarafından kullanılabilmesini sağlayan yerleşik bir bağımlılık ekleme (dı) çerçevesine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-274">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="d5da4-275">Bir sınıftaki hizmetin bir örneğini almanın bir yolu, gerekli türde bir parametreye sahip bir Oluşturucu oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-275">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="d5da4-276">Parametresi hizmet türü veya arabirim olabilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-276">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="d5da4-277">Dı sistemi, çalışma zamanında hizmeti sağlar.</span><span class="sxs-lookup"><span data-stu-id="d5da4-277">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="d5da4-278">İşte bir Entity Framework Core bağlam nesnesi almak için DI kullanan bir sınıf.</span><span class="sxs-lookup"><span data-stu-id="d5da4-278">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="d5da4-279">Vurgulanan çizgi bir Oluşturucu Ekleme örneğidir:</span><span class="sxs-lookup"><span data-stu-id="d5da4-279">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="d5da4-280">Dı yerleşik olarak kullanılıyorsa, isterseniz Control (IOC) kapsayıcısının bir üçüncü taraf Inversion öğesini eklemenize olanak sağlamak üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-280">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="d5da4-281">Daha fazla bilgi için bkz. <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-281">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="d5da4-282">Ara yazılım</span><span class="sxs-lookup"><span data-stu-id="d5da4-282">Middleware</span></span>

<span data-ttu-id="d5da4-283">İstek işleme işlem hattı, bir dizi ara yazılım bileşeni olarak oluşur.</span><span class="sxs-lookup"><span data-stu-id="d5da4-283">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="d5da4-284">Her bileşen bir üzerinde zaman uyumsuz işlemler gerçekleştirir `HttpContext` ve ardından işlem hattında sonraki ara yazılımı çağırır veya isteği sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-284">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="d5da4-285">Kurala göre, `Use...` yöntemi içindeki genişletme yöntemi çağrılarak işlem hattına bir ara yazılım bileşeni eklenir `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d5da4-285">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="d5da4-286">Örneğin, statik dosyaları işlemeyi etkinleştirmek için çağırın `UseStaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="d5da4-286">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="d5da4-287">Aşağıdaki örnekteki vurgulanan kod, istek işleme işlem hattını yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="d5da4-287">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

<span data-ttu-id="d5da4-288">ASP.NET Core, zengin bir yerleşik ara yazılım kümesi içerir ve özel ara yazılım yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d5da4-288">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="d5da4-289">Daha fazla bilgi için bkz. <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-289">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="d5da4-290">Yönetici</span><span class="sxs-lookup"><span data-stu-id="d5da4-290">Host</span></span>

<span data-ttu-id="d5da4-291">ASP.NET Core bir uygulama, başlangıçta bir *konak* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d5da4-291">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="d5da4-292">Ana bilgisayar, uygulamanın tüm kaynaklarını kapsülleyen bir nesnedir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="d5da4-292">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="d5da4-293">Bir HTTP sunucusu uygulama</span><span class="sxs-lookup"><span data-stu-id="d5da4-293">An HTTP server implementation</span></span>
* <span data-ttu-id="d5da4-294">Ara yazılım bileşenleri</span><span class="sxs-lookup"><span data-stu-id="d5da4-294">Middleware components</span></span>
* <span data-ttu-id="d5da4-295">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="d5da4-295">Logging</span></span>
* <span data-ttu-id="d5da4-296">IÇERIK</span><span class="sxs-lookup"><span data-stu-id="d5da4-296">DI</span></span>
* <span data-ttu-id="d5da4-297">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d5da4-297">Configuration</span></span>

<span data-ttu-id="d5da4-298">Uygulamanın tüm birbirine bağlı kaynaklarını tek bir nesnede dahil etmek için başlıca neden, yaşam süresi yönetimi: uygulama başlatma ve düzgün kapanma üzerinde denetim.</span><span class="sxs-lookup"><span data-stu-id="d5da4-298">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="d5da4-299">İki konak mevcuttur: Web Konağı ve genel ana bilgisayar.</span><span class="sxs-lookup"><span data-stu-id="d5da4-299">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="d5da4-300">ASP.NET Core 2. x içinde, genel konak yalnızca Web dışı senaryolar içindir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-300">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="d5da4-301">Bir konak oluşturma kodu `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="d5da4-301">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

<span data-ttu-id="d5da4-302">`CreateDefaultBuilder`Yöntemi, aşağıdaki gibi yaygın olarak kullanılan seçeneklere sahip bir ana bilgisayar yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="d5da4-302">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="d5da4-303">Web sunucusu olarak [Kestrel](#servers) kullanın ve IIS tümleştirmesini etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="d5da4-303">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="d5da4-304">Yapılandırmayı yükleme *appsettings.json* , *appSettings. { Ortam adı}. JSON*, ortam değişkenleri, komut satırı bağımsız değişkenleri ve diğer yapılandırma kaynakları.</span><span class="sxs-lookup"><span data-stu-id="d5da4-304">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="d5da4-305">Günlüğe kaydetme çıkışını konsola ve hata ayıklama sağlayıcılarına gönderin.</span><span class="sxs-lookup"><span data-stu-id="d5da4-305">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="d5da4-306">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-306">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="d5da4-307">Web dışı senaryolar</span><span class="sxs-lookup"><span data-stu-id="d5da4-307">Non-web scenarios</span></span>

<span data-ttu-id="d5da4-308">Genel ana bilgisayar, diğer uygulama türlerinin günlüğe kaydetme, bağımlılık ekleme (dı), yapılandırma ve uygulama ömür yönetimi gibi çapraz kesme çerçevesi uzantıları kullanmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-308">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="d5da4-309">Daha fazla bilgi için <xref:fundamentals/host/generic-host> ve <xref:fundamentals/host/hosted-services> bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="d5da4-309">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="d5da4-310">Sunucular</span><span class="sxs-lookup"><span data-stu-id="d5da4-310">Servers</span></span>

<span data-ttu-id="d5da4-311">Bir ASP.NET Core uygulaması HTTP isteklerini dinlemek için HTTP sunucu uygulamasını kullanır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-311">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="d5da4-312">Sunucu, bir öğesine oluşturulan [istek özellikleri](xref:fundamentals/request-features) kümesi olarak uygulamaya istekleri ister `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="d5da4-312">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="d5da4-313">Windows</span><span class="sxs-lookup"><span data-stu-id="d5da4-313">Windows</span></span>](#tab/windows)

<span data-ttu-id="d5da4-314">ASP.NET Core aşağıdaki sunucu uygulamalarını sağlar:</span><span class="sxs-lookup"><span data-stu-id="d5da4-314">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="d5da4-315">*Kestrel* , platformlar arası bir Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="d5da4-315">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="d5da4-316">Kestrel, genellikle [IIS](https://www.iis.net/)kullanılarak ters bir ara sunucu yapılandırmasında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-316">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="d5da4-317">Kestrel, doğrudan Internet 'e açık olan bir genel kullanıma yönelik uç sunucu olarak çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-317">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="d5da4-318">*IIS HTTP sunucusu* , IIS kullanan bir Windows sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="d5da4-318">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="d5da4-319">Bu sunucu ile, ASP.NET Core uygulaması ve IIS aynı işlemde çalışır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-319">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="d5da4-320">*HTTP.sys* , IIS ile kullanılmayan bir Windows sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="d5da4-320">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="d5da4-321">macOS</span><span class="sxs-lookup"><span data-stu-id="d5da4-321">macOS</span></span>](#tab/macos)

<span data-ttu-id="d5da4-322">ASP.NET Core, *Kestrel* platformlar arası sunucu uygulamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="d5da4-322">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="d5da4-323">Kestrel, doğrudan Internet 'e açık olan bir genel kullanıma yönelik uç sunucu olarak çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-323">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="d5da4-324">Kestrel, genellikle [NGINX](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmasında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-324">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="d5da4-325">Linux</span><span class="sxs-lookup"><span data-stu-id="d5da4-325">Linux</span></span>](#tab/linux)

<span data-ttu-id="d5da4-326">ASP.NET Core, *Kestrel* platformlar arası sunucu uygulamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="d5da4-326">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="d5da4-327">Kestrel, doğrudan Internet 'e açık olan bir genel kullanıma yönelik uç sunucu olarak çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-327">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="d5da4-328">Kestrel, genellikle [NGINX](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmasında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-328">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="d5da4-329">Windows</span><span class="sxs-lookup"><span data-stu-id="d5da4-329">Windows</span></span>](#tab/windows)

<span data-ttu-id="d5da4-330">ASP.NET Core aşağıdaki sunucu uygulamalarını sağlar:</span><span class="sxs-lookup"><span data-stu-id="d5da4-330">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="d5da4-331">*Kestrel* , platformlar arası bir Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="d5da4-331">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="d5da4-332">Kestrel, genellikle [IIS](https://www.iis.net/)kullanılarak ters bir ara sunucu yapılandırmasında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-332">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="d5da4-333">Kestrel, doğrudan Internet 'e açık olan bir genel kullanıma yönelik uç sunucu olarak çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-333">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="d5da4-334">*HTTP.sys* , IIS ile kullanılmayan bir Windows sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="d5da4-334">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="d5da4-335">macOS</span><span class="sxs-lookup"><span data-stu-id="d5da4-335">macOS</span></span>](#tab/macos)

<span data-ttu-id="d5da4-336">ASP.NET Core, *Kestrel* platformlar arası sunucu uygulamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="d5da4-336">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="d5da4-337">Kestrel, doğrudan Internet 'e açık olan bir genel kullanıma yönelik uç sunucu olarak çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-337">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="d5da4-338">Kestrel, genellikle [NGINX](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmasında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-338">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="d5da4-339">Linux</span><span class="sxs-lookup"><span data-stu-id="d5da4-339">Linux</span></span>](#tab/linux)

<span data-ttu-id="d5da4-340">ASP.NET Core, *Kestrel* platformlar arası sunucu uygulamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="d5da4-340">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="d5da4-341">Kestrel, doğrudan Internet 'e açık olan bir genel kullanıma yönelik uç sunucu olarak çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-341">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="d5da4-342">Kestrel, genellikle [NGINX](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmasında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-342">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d5da4-343">Daha fazla bilgi için bkz. <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-343">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="d5da4-344">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d5da4-344">Configuration</span></span>

<span data-ttu-id="d5da4-345">ASP.NET Core, ayarları sıralı bir yapılandırma sağlayıcıları kümesinden ad-değer çiftleri olarak alan bir yapılandırma çerçevesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="d5da4-345">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="d5da4-346">*. JSON* dosyaları, *. xml* dosyaları, ortam değişkenleri ve komut satırı bağımsız değişkenleri gibi çeşitli kaynaklar için yerleşik yapılandırma sağlayıcıları vardır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-346">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="d5da4-347">Ayrıca, özel yapılandırma sağlayıcıları da yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d5da4-347">You can also write custom configuration providers.</span></span>

<span data-ttu-id="d5da4-348">Örneğin, yapılandırmanın *appsettings.json* ve ortam değişkenlerinden geldiğini belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d5da4-348">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="d5da4-349">Ardından, *ConnectionString* değeri istendiğinde, çerçeve dosyada ilk olarak görünür *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="d5da4-349">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="d5da4-350">Değer aynı zamanda bir ortam değişkeninde bulunursa, ortam değişkeninin değeri öncelikli olur.</span><span class="sxs-lookup"><span data-stu-id="d5da4-350">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="d5da4-351">Parolalar gibi gizli yapılandırma verilerinin yönetilmesi için, .NET Core bir gizli dizi [Yöneticisi aracı](xref:security/app-secrets)sağlar.</span><span class="sxs-lookup"><span data-stu-id="d5da4-351">For managing confidential configuration data such as passwords, .NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="d5da4-352">Üretim gizli dizileri için [Azure Key Vault](xref:security/key-vault-configuration)önerilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-352">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="d5da4-353">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-353">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="d5da4-354">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="d5da4-354">Options</span></span>

<span data-ttu-id="d5da4-355">Mümkün olduğunda yapılandırma değerlerini depolamak ve almak için *Seçenekler deseninin* ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d5da4-355">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="d5da4-356">Seçenekler stili, ilişkili ayarların gruplarını temsil etmek için sınıfları kullanır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-356">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="d5da4-357">Örneğin, aşağıdaki kod WebSockets seçeneklerini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="d5da4-357">For example, the following code sets WebSockets options:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

<span data-ttu-id="d5da4-358">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-358">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="d5da4-359">Ortamlar</span><span class="sxs-lookup"><span data-stu-id="d5da4-359">Environments</span></span>

<span data-ttu-id="d5da4-360">*Geliştirme*, *hazırlık* ve *üretim* gibi yürütme ortamları, ASP.NET Core birinci sınıf kavramlardır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-360">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="d5da4-361">Ortam değişkenini ayarlayarak bir uygulamanın çalıştığı ortamı belirtebilirsiniz `ASPNETCORE_ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="d5da4-361">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="d5da4-362">ASP.NET Core, uygulamanın başlangıcında bu ortam değişkenini okur ve değeri bir `IHostingEnvironment` uygulamada depolar.</span><span class="sxs-lookup"><span data-stu-id="d5da4-362">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="d5da4-363">Ortam nesnesi, uygulama tarafından her yerde DI aracılığıyla kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-363">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="d5da4-364">Sınıfından aşağıdaki örnek kod, `Startup` uygulamayı yalnızca geliştirmede çalıştırıldığında ayrıntılı hata bilgilerini sunacak şekilde yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="d5da4-364">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="d5da4-365">Daha fazla bilgi için bkz. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-365">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="d5da4-366">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="d5da4-366">Logging</span></span>

<span data-ttu-id="d5da4-367">ASP.NET Core, çeşitli yerleşik ve üçüncü taraf günlük sağlayıcılarıyla birlikte çalışarak bir günlüğe kaydetme API 'sini destekler.</span><span class="sxs-lookup"><span data-stu-id="d5da4-367">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="d5da4-368">Kullanılabilir sağlayıcılar şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="d5da4-368">Available providers include the following:</span></span>

* <span data-ttu-id="d5da4-369">Konsol</span><span class="sxs-lookup"><span data-stu-id="d5da4-369">Console</span></span>
* <span data-ttu-id="d5da4-370">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="d5da4-370">Debug</span></span>
* <span data-ttu-id="d5da4-371">Windows üzerinde olay Izleme</span><span class="sxs-lookup"><span data-stu-id="d5da4-371">Event Tracing on Windows</span></span>
* <span data-ttu-id="d5da4-372">Windows olay günlüğü</span><span class="sxs-lookup"><span data-stu-id="d5da4-372">Windows Event Log</span></span>
* <span data-ttu-id="d5da4-373">TraceSource</span><span class="sxs-lookup"><span data-stu-id="d5da4-373">TraceSource</span></span>
* <span data-ttu-id="d5da4-374">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="d5da4-374">Azure App Service</span></span>
* <span data-ttu-id="d5da4-375">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="d5da4-375">Azure Application Insights</span></span>

<span data-ttu-id="d5da4-376">Dı ve çağrı günlüğü yöntemlerinden bir nesne alarak uygulamanın kodundaki her yerden günlükleri yazın `ILogger` .</span><span class="sxs-lookup"><span data-stu-id="d5da4-376">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="d5da4-377">`ILogger`Oluşturucu Ekleme ve günlük yöntemi çağrılarını vurgulanmış bir nesne kullanan örnek kod aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-377">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="d5da4-378">`ILogger`Arabirim, günlük sağlayıcısına istediğiniz sayıda alanı geçirmenize olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="d5da4-378">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="d5da4-379">Alanlar genellikle bir ileti dizesi oluşturmak için kullanılır, ancak sağlayıcı bunları bir veri deposuna ayrı alanlar olarak da gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-379">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="d5da4-380">Bu özellik, günlük sağlayıcılarının [yapılandırılmış günlük olarak da bilinen anlam günlüğü](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)uygulamasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-380">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="d5da4-381">Daha fazla bilgi için bkz. <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-381">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="d5da4-382">Yönlendirme</span><span class="sxs-lookup"><span data-stu-id="d5da4-382">Routing</span></span>

<span data-ttu-id="d5da4-383">*Yol* , bir işleyiciye EŞLENMIŞ bir URL örüncidir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-383">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="d5da4-384">İşleyici genellikle bir Razor sayfa, MVC denetleyicisindeki bir eylem yöntemi veya bir ara yazılım olur.</span><span class="sxs-lookup"><span data-stu-id="d5da4-384">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="d5da4-385">ASP.NET Core yönlendirme, uygulamanız tarafından kullanılan URL 'Ler üzerinde denetim sağlar.</span><span class="sxs-lookup"><span data-stu-id="d5da4-385">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="d5da4-386">Daha fazla bilgi için bkz. <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-386">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="d5da4-387">Hata işleme</span><span class="sxs-lookup"><span data-stu-id="d5da4-387">Error handling</span></span>

<span data-ttu-id="d5da4-388">ASP.NET Core, hataları işlemeye yönelik yerleşik özellikler içerir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="d5da4-388">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="d5da4-389">Geliştirici özel durum sayfası</span><span class="sxs-lookup"><span data-stu-id="d5da4-389">A developer exception page</span></span>
* <span data-ttu-id="d5da4-390">Özel hata sayfaları</span><span class="sxs-lookup"><span data-stu-id="d5da4-390">Custom error pages</span></span>
* <span data-ttu-id="d5da4-391">Statik durum kodu sayfaları</span><span class="sxs-lookup"><span data-stu-id="d5da4-391">Static status code pages</span></span>
* <span data-ttu-id="d5da4-392">Başlatma özel durum işleme</span><span class="sxs-lookup"><span data-stu-id="d5da4-392">Startup exception handling</span></span>

<span data-ttu-id="d5da4-393">Daha fazla bilgi için bkz. <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-393">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="d5da4-394">HTTP isteğinde bulunma</span><span class="sxs-lookup"><span data-stu-id="d5da4-394">Make HTTP requests</span></span>

<span data-ttu-id="d5da4-395">Uygulamasının bir uygulamasına `IHttpClientFactory` örnek oluşturmak için kullanılabilir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="d5da4-395">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="d5da4-396">Fabrika:</span><span class="sxs-lookup"><span data-stu-id="d5da4-396">The factory:</span></span>

* <span data-ttu-id="d5da4-397">, Mantıksal örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="d5da4-397">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="d5da4-398">Örneğin, *GitHub istemcisi kayıtlı ve GitHub 'a* erişebilecek şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-398">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="d5da4-399">Varsayılan istemci, diğer amaçlar için kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-399">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="d5da4-400">, Bir giden istek ara yazılım işlem hattı oluşturmak için birden çok temsilci seçme işleyicisinin kaydını ve zincirlemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="d5da4-400">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="d5da4-401">Bu düzen, ASP.NET Core gelen ara yazılım ardışık düzenine benzer.</span><span class="sxs-lookup"><span data-stu-id="d5da4-401">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="d5da4-402">Bu model, önbelleğe alma, hata işleme, serileştirme ve günlüğe kaydetme dahil olmak üzere HTTP istekleri etrafında çapraz kesme sorunlarını yönetmek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="d5da4-402">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="d5da4-403">Geçici hata işleme için popüler bir üçüncü taraf kitaplığı olan *Polly* ile tümleşir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-403">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="d5da4-404">`HttpClientHandler`Yaşam sürelerini el ile yönetirken gerçekleşen YAYGıN DNS sorunlarından kaçınmak için temeldeki örneklerin biriktirmesini ve ömrünü yönetir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="d5da4-404">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="d5da4-405">`ILogger`Fabrika tarafından oluşturulan istemciler aracılığıyla gönderilen tüm istekler için yapılandırılabilir bir günlüğe kaydetme deneyimi ekler (aracılığıyla).</span><span class="sxs-lookup"><span data-stu-id="d5da4-405">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="d5da4-406">Daha fazla bilgi için bkz. <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-406">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="d5da4-407">İçerik kökü</span><span class="sxs-lookup"><span data-stu-id="d5da4-407">Content root</span></span>

<span data-ttu-id="d5da4-408">İçerik kökü, için temel yoldur:</span><span class="sxs-lookup"><span data-stu-id="d5da4-408">The content root is the base path to the:</span></span>

* <span data-ttu-id="d5da4-409">Uygulamayı barındıran yürütülebilir dosya (*. exe*).</span><span class="sxs-lookup"><span data-stu-id="d5da4-409">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="d5da4-410">Uygulamayı oluşturan derlenmiş derlemeler (*. dll*).</span><span class="sxs-lookup"><span data-stu-id="d5da4-410">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="d5da4-411">Uygulama tarafından kullanılan kod olmayan içerik dosyaları, örneğin:</span><span class="sxs-lookup"><span data-stu-id="d5da4-411">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="d5da4-412">Razor dosyalar (*. cshtml*, *. Razor*)</span><span class="sxs-lookup"><span data-stu-id="d5da4-412">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="d5da4-413">Yapılandırma dosyaları (*. JSON*, *. xml*)</span><span class="sxs-lookup"><span data-stu-id="d5da4-413">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="d5da4-414">Veri dosyaları (*. db*)</span><span class="sxs-lookup"><span data-stu-id="d5da4-414">Data files (*.db*)</span></span>
* <span data-ttu-id="d5da4-415">[Web kökü](#web-root), genellikle yayınlanan *Wwwroot* klasörü.</span><span class="sxs-lookup"><span data-stu-id="d5da4-415">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="d5da4-416">Geliştirme sırasında:</span><span class="sxs-lookup"><span data-stu-id="d5da4-416">During development:</span></span>

* <span data-ttu-id="d5da4-417">İçerik kökü, projenin kök dizinini varsayılan olarak belirler.</span><span class="sxs-lookup"><span data-stu-id="d5da4-417">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="d5da4-418">Projenin kök dizini şunu oluşturmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="d5da4-418">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="d5da4-419">Uygulamanın, projenin kök dizinindeki kod olmayan içerik dosyalarının yolu.</span><span class="sxs-lookup"><span data-stu-id="d5da4-419">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="d5da4-420">[Web kökü](#web-root), genellikle projenin kök dizinindeki *Wwwroot* klasörü.</span><span class="sxs-lookup"><span data-stu-id="d5da4-420">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="d5da4-421">[Konak oluşturulurken](#host)alternatif bir içerik kök yolu belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-421">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="d5da4-422">Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#content-root>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-422">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="d5da4-423">Web kökü</span><span class="sxs-lookup"><span data-stu-id="d5da4-423">Web root</span></span>

<span data-ttu-id="d5da4-424">Web kökü, genel, kod olmayan statik kaynak dosyalarının temel yoludur, örneğin:</span><span class="sxs-lookup"><span data-stu-id="d5da4-424">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="d5da4-425">Stil sayfaları (*. css*)</span><span class="sxs-lookup"><span data-stu-id="d5da4-425">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="d5da4-426">JavaScript (*. js*)</span><span class="sxs-lookup"><span data-stu-id="d5da4-426">JavaScript (*.js*)</span></span>
* <span data-ttu-id="d5da4-427">Görüntüler (*. png*, *. jpg*)</span><span class="sxs-lookup"><span data-stu-id="d5da4-427">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="d5da4-428">Statik dosyalar yalnızca Web kök dizininden (ve alt dizinlerde) varsayılan olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="d5da4-428">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="d5da4-429">Web kök yolu varsayılan olarak *{Content root}/Wwwroot* olarak belirlenir, ancak [konak oluşturulurken](#host)farklı bir Web kökü belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="d5da4-429">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="d5da4-430">Daha fazla bilgi için bkz. [Web root](xref:fundamentals/host/web-host#web-root).</span><span class="sxs-lookup"><span data-stu-id="d5da4-430">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="d5da4-431">Proje dosyasındaki [ \<Content> Proje öğesi](/visualstudio/msbuild/common-msbuild-project-items#content) ile *Wwwroot* 'da dosya yayımlamayı önleyin.</span><span class="sxs-lookup"><span data-stu-id="d5da4-431">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="d5da4-432">Aşağıdaki örnek, *Wwwroot/yerel* dizin ve alt dizinlerde içerik yayımlamayı engeller:</span><span class="sxs-lookup"><span data-stu-id="d5da4-432">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="d5da4-433">Razor(*. Cshtml*) dosyalarında, tilde eğik çizgi ( `~/` ) Web köküne işaret eder.</span><span class="sxs-lookup"><span data-stu-id="d5da4-433">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="d5da4-434">İle başlayan bir yol `~/` , *sanal yol* olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="d5da4-434">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="d5da4-435">Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="d5da4-435">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end
