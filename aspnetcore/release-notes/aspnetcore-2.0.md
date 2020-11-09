---
title: ASP.NET Core 2,0 ' deki yenilikler
author: rick-anderson
description: ASP.NET Core 2,0 ' deki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: aspnetcore-2.0
ms.openlocfilehash: f9fad5a63c76a3b21341a12fd40baafcdf2c9dac
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059734"
---
# <a name="whats-new-in-aspnet-core-20"></a><span data-ttu-id="b41a9-103">ASP.NET Core 2,0 ' deki yenilikler</span><span class="sxs-lookup"><span data-stu-id="b41a9-103">What's new in ASP.NET Core 2.0</span></span>

<span data-ttu-id="b41a9-104">Bu makalede, ASP.NET Core 2,0 ' deki en önemli değişiklikler, ilgili belgelerin bağlantılarıyla vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="b41a9-104">This article highlights the most significant changes in ASP.NET Core 2.0, with links to relevant documentation.</span></span>

## <a name="no-locrazor-pages"></a><span data-ttu-id="b41a9-105">Razor Sayfaları</span><span class="sxs-lookup"><span data-stu-id="b41a9-105">Razor Pages</span></span>

<span data-ttu-id="b41a9-106">Razor Sayfalar, kod odaklı senaryoları daha kolay ve daha üretken hale getiren ASP.NET Core MVC 'nin yeni bir özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="b41a9-106">Razor Pages is a new feature of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="b41a9-107">Daha fazla bilgi için bkz. giriş ve öğretici:</span><span class="sxs-lookup"><span data-stu-id="b41a9-107">For more information, see the introduction and tutorial:</span></span>

* [<span data-ttu-id="b41a9-108">Sayfalara giriş Razor</span><span class="sxs-lookup"><span data-stu-id="b41a9-108">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="b41a9-109">Sayfalarla çalışmaya başlama Razor</span><span class="sxs-lookup"><span data-stu-id="b41a9-109">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a><span data-ttu-id="b41a9-110">ASP.NET Core metapackage</span><span class="sxs-lookup"><span data-stu-id="b41a9-110">ASP.NET Core metapackage</span></span>

<span data-ttu-id="b41a9-111">Yeni bir ASP.NET Core metapackage, ASP.NET Core ve Entity Framework Core ekipleri tarafından oluşturulan ve desteklenen tüm paketleri dahili ve üçüncü taraf bağımlılıklarıyla birlikte içerir.</span><span class="sxs-lookup"><span data-stu-id="b41a9-111">A new ASP.NET Core metapackage includes all of the packages made and supported by the ASP.NET Core and Entity Framework Core teams, along with their internal and 3rd-party dependencies.</span></span> <span data-ttu-id="b41a9-112">Artık pakete göre bireysel ASP.NET Core özelliklerini seçmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="b41a9-112">You no longer need to choose individual ASP.NET Core features by package.</span></span> <span data-ttu-id="b41a9-113">Tüm Özellikler [Microsoft. AspNetCore. All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) paketine dahildir.</span><span class="sxs-lookup"><span data-stu-id="b41a9-113">All features are included in the [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) package.</span></span> <span data-ttu-id="b41a9-114">Varsayılan Şablonlar bu paketi kullanır.</span><span class="sxs-lookup"><span data-stu-id="b41a9-114">The default templates use this package.</span></span>

<span data-ttu-id="b41a9-115">Daha fazla bilgi için bkz. [Microsoft. AspNetCore. All metapackage for ASP.NET Core 2,0](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="b41a9-115">For more information, see [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0](xref:fundamentals/metapackage).</span></span>

## <a name="runtime-store"></a><span data-ttu-id="b41a9-116">Çalışma zamanı deposu</span><span class="sxs-lookup"><span data-stu-id="b41a9-116">Runtime Store</span></span>

<span data-ttu-id="b41a9-117">Metapackage kullanan uygulamalar, `Microsoft.AspNetCore.All` yeni .NET Core çalışma zamanı deposundan otomatik olarak yararlanır.</span><span class="sxs-lookup"><span data-stu-id="b41a9-117">Applications that use the `Microsoft.AspNetCore.All` metapackage automatically take advantage of the new .NET Core Runtime Store.</span></span> <span data-ttu-id="b41a9-118">Mağaza ASP.NET Core 2,0 uygulamalarını çalıştırmak için gereken tüm çalışma zamanı varlıklarını içerir.</span><span class="sxs-lookup"><span data-stu-id="b41a9-118">The Store contains all the runtime assets needed to run ASP.NET Core 2.0 applications.</span></span> <span data-ttu-id="b41a9-119">`Microsoft.AspNetCore.All`Metapackage kullandığınızda başvurulan ASP.NET Core NuGet paketlerinden hiçbir varlık hedef sistemde zaten bulunduğundan uygulamayla birlikte dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="b41a9-119">When you use the `Microsoft.AspNetCore.All` metapackage, no assets from the referenced ASP.NET Core NuGet packages are deployed with the application because they already reside on the target system.</span></span> <span data-ttu-id="b41a9-120">Çalışma zamanı deposundaki varlıklar Ayrıca uygulama başlatma süresini geliştirmek için önceden derlenmiş.</span><span class="sxs-lookup"><span data-stu-id="b41a9-120">The assets in the Runtime Store are also precompiled to improve application startup time.</span></span>

<span data-ttu-id="b41a9-121">Daha fazla bilgi için bkz. [çalışma zamanı deposu](/dotnet/core/deploying/runtime-store)</span><span class="sxs-lookup"><span data-stu-id="b41a9-121">For more information, see [Runtime store](/dotnet/core/deploying/runtime-store)</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="b41a9-122">.NET Standard 2,0</span><span class="sxs-lookup"><span data-stu-id="b41a9-122">.NET Standard 2.0</span></span>

<span data-ttu-id="b41a9-123">ASP.NET Core 2,0 paketleri .NET Standard 2,0 ' dir.</span><span class="sxs-lookup"><span data-stu-id="b41a9-123">The ASP.NET Core 2.0 packages target .NET Standard 2.0.</span></span> <span data-ttu-id="b41a9-124">Paketlere diğer .NET Standard 2,0 kitaplıkları tarafından başvurulabilir ve .NET Core 2,0 ve .NET Framework 4.6.1 dahil olmak üzere .NET Standard 2,0 uyumlu .NET uygulamalarında çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="b41a9-124">The packages can be referenced by other .NET Standard 2.0 libraries, and they can run on .NET Standard 2.0-compliant implementations of .NET, including .NET Core 2.0 and .NET Framework 4.6.1.</span></span> 

<span data-ttu-id="b41a9-125">Metapackage, .NET Core `Microsoft.AspNetCore.All` 2,0 çalışma zamanı deposuyla kullanılması amaçlanan için yalnızca .net core 2,0 ' i hedefler.</span><span class="sxs-lookup"><span data-stu-id="b41a9-125">The `Microsoft.AspNetCore.All` metapackage targets .NET Core 2.0 only, because it's intended to be used with the .NET Core 2.0 Runtime Store.</span></span>

## <a name="configuration-update"></a><span data-ttu-id="b41a9-126">Yapılandırma güncelleştirmesi</span><span class="sxs-lookup"><span data-stu-id="b41a9-126">Configuration update</span></span>

<span data-ttu-id="b41a9-127">`IConfiguration`ASP.NET Core 2,0 ' de varsayılan olarak hizmetler kapsayıcısına bir örnek eklenir.</span><span class="sxs-lookup"><span data-stu-id="b41a9-127">An `IConfiguration` instance is added to the services container by default in ASP.NET Core 2.0.</span></span> <span data-ttu-id="b41a9-128">`IConfiguration` Hizmetler kapsayıcısında, uygulamaların kapsayıcıdan yapılandırma değerlerini almasına daha kolay hale gelir.</span><span class="sxs-lookup"><span data-stu-id="b41a9-128">`IConfiguration` in the services container makes it easier for applications to retrieve configuration values from the container.</span></span>

<span data-ttu-id="b41a9-129">Planlı belgelerin durumu hakkında daha fazla bilgi için bkz. [GitHub sorunu](https://github.com/dotnet/AspNetCore.Docs/issues/3387).</span><span class="sxs-lookup"><span data-stu-id="b41a9-129">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3387).</span></span>

## <a name="logging-update"></a><span data-ttu-id="b41a9-130">Günlük güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="b41a9-130">Logging update</span></span>

<span data-ttu-id="b41a9-131">ASP.NET Core 2,0 ' de günlüğe kaydetme, varsayılan olarak bağımlılık ekleme (dı) sistemine dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="b41a9-131">In ASP.NET Core 2.0, logging is incorporated into the dependency injection (DI) system by default.</span></span> <span data-ttu-id="b41a9-132">Sağlayıcılar ekler ve *Startup.cs* dosyası yerine *program.cs* dosyasında filtrelemeyi yapılandırırsınız.</span><span class="sxs-lookup"><span data-stu-id="b41a9-132">You add providers and configure filtering in the *Program.cs* file instead of in the *Startup.cs* file.</span></span> <span data-ttu-id="b41a9-133">Ve varsayılan, `ILoggerFactory` çapraz sağlayıcı filtreleme ve belirli sağlayıcı filtreleme için tek bir esnek yaklaşım kullanmanıza imkan tanıyan şekilde filtrelemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="b41a9-133">And the default `ILoggerFactory` supports filtering in a way that lets you use one flexible approach for both cross-provider filtering and specific-provider filtering.</span></span>

<span data-ttu-id="b41a9-134">Daha fazla bilgi için bkz. [günlüğe giriş](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="b41a9-134">For more information, see [Introduction to Logging](xref:fundamentals/logging/index).</span></span>

## <a name="authentication-update"></a><span data-ttu-id="b41a9-135">Kimlik doğrulama güncelleştirmesi</span><span class="sxs-lookup"><span data-stu-id="b41a9-135">Authentication update</span></span>

<span data-ttu-id="b41a9-136">Yeni bir kimlik doğrulama modeli, DI kullanarak bir uygulama için kimlik doğrulamasını yapılandırmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="b41a9-136">A new authentication model makes it easier to configure authentication for an application using DI.</span></span>

<span data-ttu-id="b41a9-137">[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)kullanarak Web uygulamaları ve Web API 'lerinin kimlik doğrulamasını yapılandırmak için yeni şablonlar kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b41a9-137">New templates are available for configuring authentication for web apps and web APIs using [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).</span></span>

<span data-ttu-id="b41a9-138">Planlı belgelerin durumu hakkında daha fazla bilgi için bkz. [GitHub sorunu](https://github.com/dotnet/AspNetCore.Docs/issues/3054).</span><span class="sxs-lookup"><span data-stu-id="b41a9-138">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3054).</span></span>

## <a name="no-locidentity-update"></a><span data-ttu-id="b41a9-139">Identity Update</span><span class="sxs-lookup"><span data-stu-id="b41a9-139">Identity update</span></span>

<span data-ttu-id="b41a9-140">ASP.NET Core 2,0 ' de kullanarak güvenli Web API 'Leri oluşturmayı daha kolay hale geçirdik Identity .</span><span class="sxs-lookup"><span data-stu-id="b41a9-140">We've made it easier to build secure web APIs using Identity in ASP.NET Core 2.0.</span></span> <span data-ttu-id="b41a9-141">[Microsoft kimlik doğrulama kitaplığı 'nı (msal)](https://www.nuget.org/packages/Microsoft.Identity.Client)kullanarak Web API 'lerinize erişmek için erişim belirteçleri edinebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b41a9-141">You can acquire access tokens for accessing your web APIs using the [Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span></span>

<span data-ttu-id="b41a9-142">2,0 sürümündeki kimlik doğrulama değişiklikleriyle ilgili daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="b41a9-142">For more information on authentication changes in 2.0, see the following resources:</span></span>

* [<span data-ttu-id="b41a9-143">ASP.NET Core hesap onaylama ve parola kurtarma</span><span class="sxs-lookup"><span data-stu-id="b41a9-143">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="b41a9-144">ASP.NET Core 'de Authenticator uygulamaları için QR kodu oluşturmayı etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="b41a9-144">Enable QR Code generation for authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="b41a9-145">Kimlik doğrulamasını Identity ASP.NET Core ve 2,0 'e geçirin</span><span class="sxs-lookup"><span data-stu-id="b41a9-145">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a><span data-ttu-id="b41a9-146">SPA şablonları</span><span class="sxs-lookup"><span data-stu-id="b41a9-146">SPA templates</span></span>

<span data-ttu-id="b41a9-147">Angular, Aurelia, Knockout.js, React.js ve Redux ile React.js için tek sayfalı uygulama (SPA) proje şablonları mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="b41a9-147">Single Page Application (SPA) project templates for Angular, Aurelia, Knockout.js, React.js, and React.js with Redux are available.</span></span> <span data-ttu-id="b41a9-148">Angular şablonu angular 4 ' e güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="b41a9-148">The Angular template has been updated to Angular 4.</span></span> <span data-ttu-id="b41a9-149">Angular ve tepki verme şablonları varsayılan olarak kullanılabilir; diğer şablonları alma hakkında daha fazla bilgi için bkz. [Yeni BIR Spa projesi oluşturma](xref:client-side/spa-services#create-a-new-project).</span><span class="sxs-lookup"><span data-stu-id="b41a9-149">The Angular and React templates are available by default; for information about how to get the other templates, see [Create a new SPA project](xref:client-side/spa-services#create-a-new-project).</span></span> <span data-ttu-id="b41a9-150">ASP.NET Core 'de SPA oluşturma hakkında daha fazla bilgi için bkz <xref:client-side/spa-services> ..</span><span class="sxs-lookup"><span data-stu-id="b41a9-150">For information about how to build a SPA in ASP.NET Core, see <xref:client-side/spa-services>.</span></span>

## <a name="kestrel-improvements"></a><span data-ttu-id="b41a9-151">Kestrel geliştirmeleri</span><span class="sxs-lookup"><span data-stu-id="b41a9-151">Kestrel improvements</span></span>

<span data-ttu-id="b41a9-152">Kestrel Web sunucusu, Internet 'e yönelik bir sunucu olarak daha uygun hale getirmek için yeni özelliklere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="b41a9-152">The Kestrel web server has new features that make it more suitable as an Internet-facing server.</span></span> <span data-ttu-id="b41a9-153">Sınıfın yeni özelliğine bir dizi sunucu kısıtlaması yapılandırma seçeneği eklenir `KestrelServerOptions` `Limits` .</span><span class="sxs-lookup"><span data-stu-id="b41a9-153">A number of server constraint configuration options are added in the `KestrelServerOptions` class's new `Limits` property.</span></span> <span data-ttu-id="b41a9-154">Aşağıdakiler için sınır ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b41a9-154">Add limits for the following:</span></span>

* <span data-ttu-id="b41a9-155">İstemci bağlantıları üst sınırı</span><span class="sxs-lookup"><span data-stu-id="b41a9-155">Maximum client connections</span></span>
* <span data-ttu-id="b41a9-156">En büyük istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="b41a9-156">Maximum request body size</span></span>
* <span data-ttu-id="b41a9-157">En az istek gövdesi veri hızı</span><span class="sxs-lookup"><span data-stu-id="b41a9-157">Minimum request body data rate</span></span>

<span data-ttu-id="b41a9-158">Daha fazla bilgi için bkz. [Kestrel Web Server Implementation For ASP.NET Core](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="b41a9-158">For more information, see [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel).</span></span>

## <a name="weblistener-renamed-to-httpsys"></a><span data-ttu-id="b41a9-159">WebListener HTTP.sys olarak yeniden adlandırıldı</span><span class="sxs-lookup"><span data-stu-id="b41a9-159">WebListener renamed to HTTP.sys</span></span>

<span data-ttu-id="b41a9-160">Paketler `Microsoft.AspNetCore.Server.WebListener` ve `Microsoft.Net.Http.Server` Yeni bir pakette birleştirildi `Microsoft.AspNetCore.Server.HttpSys` .</span><span class="sxs-lookup"><span data-stu-id="b41a9-160">The packages `Microsoft.AspNetCore.Server.WebListener` and `Microsoft.Net.Http.Server` have been merged into a new package `Microsoft.AspNetCore.Server.HttpSys`.</span></span> <span data-ttu-id="b41a9-161">Ad alanları eşleşecek şekilde güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="b41a9-161">The namespaces have been updated to match.</span></span>

<span data-ttu-id="b41a9-162">Daha fazla bilgi için bkz. [ ASP.NET Core Web sunucusu uygulamasınıHTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="b41a9-162">For more information, see [HTTP.sys web server implementation in ASP.NET Core](xref:fundamentals/servers/httpsys).</span></span>

## <a name="enhanced-http-header-support"></a><span data-ttu-id="b41a9-163">Gelişmiş HTTP üst bilgisi desteği</span><span class="sxs-lookup"><span data-stu-id="b41a9-163">Enhanced HTTP header support</span></span>

<span data-ttu-id="b41a9-164">Bir veya göndermek için MVC kullanırken `FileStreamResult` `FileContentResult` , artık gönderdiğiniz içerik için bir veya bir tarih ayarlama seçeneğiniz vardır `ETag` `LastModified` .</span><span class="sxs-lookup"><span data-stu-id="b41a9-164">When using MVC to transmit a `FileStreamResult` or a `FileContentResult`, you now have the option to set an `ETag` or a `LastModified` date on the content you transmit.</span></span> <span data-ttu-id="b41a9-165">Bu değerleri, döndürülen içerikte aşağıdakine benzer kodla ayarlayabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="b41a9-165">You can set these values on the returned content with code similar to the following:</span></span>

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

<span data-ttu-id="b41a9-166">Ziyaretçilere döndürülen dosya ve değerleri için uygun HTTP başlıklarına sahiptir `ETag` `LastModified` .</span><span class="sxs-lookup"><span data-stu-id="b41a9-166">The file returned to your visitors has the appropriate HTTP headers for the `ETag` and `LastModified` values.</span></span>

<span data-ttu-id="b41a9-167">Bir uygulama ziyaretçisi bir Aralık Isteği üstbilgisiyle içerik isterse, ASP.NET Core isteği tanır ve üstbilgiyi işler.</span><span class="sxs-lookup"><span data-stu-id="b41a9-167">If an application visitor requests content with a Range Request header, ASP.NET Core recognizes the request and handles the header.</span></span> <span data-ttu-id="b41a9-168">İstenen içerik kısmen teslim edilebiliyorsa, ASP.NET Core uygun şekilde atlar ve yalnızca istenen bayt kümesini döndürür.</span><span class="sxs-lookup"><span data-stu-id="b41a9-168">If the requested content can be partially delivered, ASP.NET Core appropriately skips and returns just the requested set of bytes.</span></span> <span data-ttu-id="b41a9-169">Bu özelliği uyarlamak veya işlemek için yöntemlerinize özel işleyiciler yazmanız gerekmez; sizin için otomatik olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="b41a9-169">You don't need to write any special handlers into your methods to adapt or handle this feature; it's automatically handled for you.</span></span>

## <a name="hosting-startup-and-application-insights"></a><span data-ttu-id="b41a9-170">Barındırma başlatma ve Application Insights</span><span class="sxs-lookup"><span data-stu-id="b41a9-170">Hosting startup and Application Insights</span></span>

<span data-ttu-id="b41a9-171">Barındırma ortamları artık uygulamanın başlatılması sırasında ek paket bağımlılıklarını ekleyebilir ve uygulama başlatma sırasında kodu yürütebilir.</span><span class="sxs-lookup"><span data-stu-id="b41a9-171">Hosting environments can now inject extra package dependencies and execute code during application startup, without the application needing to explicitly take a dependency or call any methods.</span></span> <span data-ttu-id="b41a9-172">Bu özellik, belirli ortamların, uygulamanın daha önce haberdar olması gerekmeden bu ortama özgü olan "hafif" özelliklere olanak tanımak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b41a9-172">This feature can be used to enable certain environments to "light-up" features unique to that environment without the application needing to know ahead of time.</span></span> 

<span data-ttu-id="b41a9-173">ASP.NET Core 2,0 ' de, bu özellik, Visual Studio 'da hata ayıklarken Application Insights tanılamayı otomatik olarak etkinleştirmek için kullanılır ve Azure Uygulama Hizmetleri 'nde çalışırken (uygulamasına geçtikten sonra).</span><span class="sxs-lookup"><span data-stu-id="b41a9-173">In ASP.NET Core 2.0, this feature is used to automatically enable Application Insights diagnostics when debugging in Visual Studio and (after opting in) when running in Azure App Services.</span></span> <span data-ttu-id="b41a9-174">Sonuç olarak, proje şablonları artık varsayılan olarak Application Insights paketleri ve kodu eklemez.</span><span class="sxs-lookup"><span data-stu-id="b41a9-174">As a result, the project templates no longer add Application Insights packages and code by default.</span></span>

<span data-ttu-id="b41a9-175">Planlı belgelerin durumu hakkında daha fazla bilgi için bkz. [GitHub sorunu](https://github.com/dotnet/AspNetCore.Docs/issues/3389).</span><span class="sxs-lookup"><span data-stu-id="b41a9-175">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3389).</span></span>

## <a name="automatic-use-of-anti-forgery-tokens"></a><span data-ttu-id="b41a9-176">Anti-forgery belirteçlerinin otomatik kullanımı</span><span class="sxs-lookup"><span data-stu-id="b41a9-176">Automatic use of anti-forgery tokens</span></span>

<span data-ttu-id="b41a9-177">ASP.NET Core, varsayılan olarak her zaman HTML kodsuz içeriğe yardımcı olur, ancak yeni sürüme, siteler arası istek sahteciliği (XSRF) saldırılarını önlemeye yardımcı olmak için ek bir adım alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="b41a9-177">ASP.NET Core has always helped HTML-encode content by default, but with the new version an extra step is taken to help prevent cross-site request forgery (XSRF) attacks.</span></span> <span data-ttu-id="b41a9-178">ASP.NET Core artık varsayılan olarak korsanlığa karşı koruma belirteçleri yayıp bunları, ek yapılandırma olmadan bu eylemleri ve sayfaları form SONRASı olarak doğrular.</span><span class="sxs-lookup"><span data-stu-id="b41a9-178">ASP.NET Core will now emit anti-forgery tokens by default and validate them on form POST actions and pages without extra configuration.</span></span>

<span data-ttu-id="b41a9-179">Daha fazla bilgi için bkz. <xref:security/anti-request-forgery>.</span><span class="sxs-lookup"><span data-stu-id="b41a9-179">For more information, see <xref:security/anti-request-forgery>.</span></span>

## <a name="automatic-precompilation"></a><span data-ttu-id="b41a9-180">Otomatik ön derleme</span><span class="sxs-lookup"><span data-stu-id="b41a9-180">Automatic precompilation</span></span>

<span data-ttu-id="b41a9-181">Razor Yayımlama ön derleme sırasında varsayılan olarak, yayımlama çıkış boyutunu ve uygulama başlatma süresini azaltarak etkin olarak görüntüle ' yi görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="b41a9-181">Razor view pre-compilation is enabled during publish by default, reducing the publish output size and application startup time.</span></span>

<span data-ttu-id="b41a9-182">Daha fazla bilgi için bkz. [ Razor ASP.NET Core derleme ve ön derlemeyi görüntüleme](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="b41a9-182">For more information, see [Razor view compilation and precompilation in ASP.NET Core](xref:mvc/views/view-compilation).</span></span>

## <a name="no-locrazor-support-for-c-71"></a><span data-ttu-id="b41a9-183">Razor C# 7,1 için destek</span><span class="sxs-lookup"><span data-stu-id="b41a9-183">Razor support for C# 7.1</span></span>

<span data-ttu-id="b41a9-184">RazorGörünüm altyapısı, yeni Roslyn derleyicisi ile çalışacak şekilde güncelleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="b41a9-184">The Razor view engine has been updated to work with the new Roslyn compiler.</span></span> <span data-ttu-id="b41a9-185">Bu, varsayılan Ifadeler gibi C# 7,1 özellikleri, gösterilen tanımlama grubu adları ve genel türler içeren Pattern-Matching için destek içerir.</span><span class="sxs-lookup"><span data-stu-id="b41a9-185">That includes support for C# 7.1 features like Default Expressions, Inferred Tuple Names, and Pattern-Matching with Generics.</span></span> <span data-ttu-id="b41a9-186">Projenizde C# 7,1 kullanmak için, proje dosyanıza aşağıdaki özelliği ekleyin ve çözümü yeniden yükleyin:</span><span class="sxs-lookup"><span data-stu-id="b41a9-186">To use C# 7.1 in your project, add the following property in your project file and then reload the solution:</span></span>

```xml
<LangVersion>latest</LangVersion>
```

<span data-ttu-id="b41a9-187">C# 7,1 özelliklerinin durumu hakkında daha fazla bilgi için bkz. [Roslyn GitHub deposu](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span><span class="sxs-lookup"><span data-stu-id="b41a9-187">For information about the status of C# 7.1 features, see [the Roslyn GitHub repository](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span></span>

## <a name="other-documentation-updates-for-20"></a><span data-ttu-id="b41a9-188">2,0 için diğer belge güncelleştirmeleri</span><span class="sxs-lookup"><span data-stu-id="b41a9-188">Other documentation updates for 2.0</span></span>

* [<span data-ttu-id="b41a9-189">ASP.NET Core uygulama dağıtımı için Visual Studio yayımlama profilleri</span><span class="sxs-lookup"><span data-stu-id="b41a9-189">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>](xref:host-and-deploy/visual-studio-publish-profiles)
* [<span data-ttu-id="b41a9-190">Anahtar yönetimi</span><span class="sxs-lookup"><span data-stu-id="b41a9-190">Key Management</span></span>](xref:security/data-protection/implementation/key-management)
* [<span data-ttu-id="b41a9-191">Facebook kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b41a9-191">Configure Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="b41a9-192">Twitter kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b41a9-192">Configure Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="b41a9-193">Google kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b41a9-193">Configure Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="b41a9-194">Microsoft hesabı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b41a9-194">Configure Microsoft Account authentication</span></span>](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a><span data-ttu-id="b41a9-195">Geçiş kılavuzu</span><span class="sxs-lookup"><span data-stu-id="b41a9-195">Migration guidance</span></span>

<span data-ttu-id="b41a9-196">ASP.NET Core 1. x uygulamalarının ASP.NET Core 2,0 ' e nasıl geçirileceğiyle ilgili yönergeler için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="b41a9-196">For guidance on how to migrate ASP.NET Core 1.x applications to ASP.NET Core 2.0, see the following resources:</span></span>

* [<span data-ttu-id="b41a9-197">ASP.NET Core 1. x öğesinden ASP.NET Core 2,0 'e geçirin</span><span class="sxs-lookup"><span data-stu-id="b41a9-197">Migrate from ASP.NET Core 1.x to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/index)
* [<span data-ttu-id="b41a9-198">Kimlik doğrulamasını Identity ASP.NET Core ve 2,0 'e geçirin</span><span class="sxs-lookup"><span data-stu-id="b41a9-198">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a><span data-ttu-id="b41a9-199">Ek Bilgi</span><span class="sxs-lookup"><span data-stu-id="b41a9-199">Additional Information</span></span>

<span data-ttu-id="b41a9-200">Değişikliklerin tamamı listesi için [ASP.NET Core 2,0 sürüm notlarına](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0)bakın.</span><span class="sxs-lookup"><span data-stu-id="b41a9-200">For the complete list of changes, see the [ASP.NET Core 2.0 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).</span></span>

<span data-ttu-id="b41a9-201">ASP.NET Core geliştirme ekibinin ilerleme ve planlarıyla bağlantı kurmak için, [ASP.net Community](https://live.asp.net/)' ye ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b41a9-201">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
