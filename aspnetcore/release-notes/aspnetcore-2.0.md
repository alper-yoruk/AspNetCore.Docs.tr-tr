---
title: ASP.NET Core 2.0'daki yenilikler
author: rick-anderson
description: ASP.NET Core 2.0'daki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: aspnetcore-2.0
ms.openlocfilehash: 5ca43bab1496aa9fda65282cbb0b1177ad8689eb
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667260"
---
# <a name="whats-new-in-aspnet-core-20"></a><span data-ttu-id="b70d7-103">ASP.NET Core 2.0'daki yenilikler</span><span class="sxs-lookup"><span data-stu-id="b70d7-103">What's new in ASP.NET Core 2.0</span></span>

<span data-ttu-id="b70d7-104">Bu makalede, ilgili belgelere bağlantılar ile ASP.NET Core 2.0 en önemli değişiklikleri vurgulamaktadır.</span><span class="sxs-lookup"><span data-stu-id="b70d7-104">This article highlights the most significant changes in ASP.NET Core 2.0, with links to relevant documentation.</span></span>

## <a name="razor-pages"></a><span data-ttu-id="b70d7-105">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="b70d7-105">Razor Pages</span></span>

<span data-ttu-id="b70d7-106">Razor Pages, ASP.NET Core MVC'nin sayfa odaklı senaryoları kodlamayı daha kolay ve üretken hale getiren yeni bir özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="b70d7-106">Razor Pages is a new feature of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="b70d7-107">Daha fazla bilgi için giriş ve öğreticiye bakın:</span><span class="sxs-lookup"><span data-stu-id="b70d7-107">For more information, see the introduction and tutorial:</span></span>

* [<span data-ttu-id="b70d7-108">Razor Pages’e giriş</span><span class="sxs-lookup"><span data-stu-id="b70d7-108">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="b70d7-109">Razor Sayfaları kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="b70d7-109">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a><span data-ttu-id="b70d7-110">ASP.NET Çekirdek metapaketi</span><span class="sxs-lookup"><span data-stu-id="b70d7-110">ASP.NET Core metapackage</span></span>

<span data-ttu-id="b70d7-111">Yeni bir ASP.NET Core meta paketi, ASP.NET Çekirdek ve Entity Framework Core ekipleri tarafından yapılan ve desteklenen tüm paketlerive iç ve üçüncü taraf bağımlılıklarını içerir.</span><span class="sxs-lookup"><span data-stu-id="b70d7-111">A new ASP.NET Core metapackage includes all of the packages made and supported by the ASP.NET Core and Entity Framework Core teams, along with their internal and 3rd-party dependencies.</span></span> <span data-ttu-id="b70d7-112">Artık pakete göre tek tek ASP.NET Core özelliklerini seçmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="b70d7-112">You no longer need to choose individual ASP.NET Core features by package.</span></span> <span data-ttu-id="b70d7-113">Tüm özellikler [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) paketinde yer alıyor.</span><span class="sxs-lookup"><span data-stu-id="b70d7-113">All features are included in the [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) package.</span></span> <span data-ttu-id="b70d7-114">Varsayılan şablonlar bu paketi kullanır.</span><span class="sxs-lookup"><span data-stu-id="b70d7-114">The default templates use this package.</span></span>

<span data-ttu-id="b70d7-115">Daha fazla bilgi için, [Core 2.0 ASP.NET için Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage)bakın.</span><span class="sxs-lookup"><span data-stu-id="b70d7-115">For more information, see [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0](xref:fundamentals/metapackage).</span></span>

## <a name="runtime-store"></a><span data-ttu-id="b70d7-116">Runtime Mağazası</span><span class="sxs-lookup"><span data-stu-id="b70d7-116">Runtime Store</span></span>

<span data-ttu-id="b70d7-117">`Microsoft.AspNetCore.All` Metapaketi kullanan uygulamalar otomatik olarak yeni .NET Core Runtime Store'dan yararlanır.</span><span class="sxs-lookup"><span data-stu-id="b70d7-117">Applications that use the `Microsoft.AspNetCore.All` metapackage automatically take advantage of the new .NET Core Runtime Store.</span></span> <span data-ttu-id="b70d7-118">Mağaza, Core 2.0 uygulamalarını çalıştırmak için gereken tüm çalışma zamanı varlıklarını içerir ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="b70d7-118">The Store contains all the runtime assets needed to run ASP.NET Core 2.0 applications.</span></span> <span data-ttu-id="b70d7-119">`Microsoft.AspNetCore.All` Meta paketi kullandığınızda, başvurulan ASP.NET Core NuGet paketlerinden hiçbir varlık uygulamayla birlikte dağıtılan çünkü zaten hedef sistemde bulunmaz.</span><span class="sxs-lookup"><span data-stu-id="b70d7-119">When you use the `Microsoft.AspNetCore.All` metapackage, no assets from the referenced ASP.NET Core NuGet packages are deployed with the application because they already reside on the target system.</span></span> <span data-ttu-id="b70d7-120">Runtime Store'daki varlıklar da uygulama başlatma süresini iyileştirmek için önceden derlenir.</span><span class="sxs-lookup"><span data-stu-id="b70d7-120">The assets in the Runtime Store are also precompiled to improve application startup time.</span></span>

<span data-ttu-id="b70d7-121">Daha fazla bilgi için [Runtime mağazasına](/dotnet/core/deploying/runtime-store) bakın</span><span class="sxs-lookup"><span data-stu-id="b70d7-121">For more information, see [Runtime store](/dotnet/core/deploying/runtime-store)</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="b70d7-122">.NET Standart 2.0</span><span class="sxs-lookup"><span data-stu-id="b70d7-122">.NET Standard 2.0</span></span>

<span data-ttu-id="b70d7-123">ASP.NET Core 2.0 paketleri hedef .NET Standart 2.0.</span><span class="sxs-lookup"><span data-stu-id="b70d7-123">The ASP.NET Core 2.0 packages target .NET Standard 2.0.</span></span> <span data-ttu-id="b70d7-124">Paketler diğer .NET Standart 2.0 kitaplıkları tarafından başvurulabilir ve .NET Core 2.0 ve .NET Framework 4.6.1 dahil olmak üzere .NET Standard 2.0 uyumlu uygulamalarda çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="b70d7-124">The packages can be referenced by other .NET Standard 2.0 libraries, and they can run on .NET Standard 2.0-compliant implementations of .NET, including .NET Core 2.0 and .NET Framework 4.6.1.</span></span> 

<span data-ttu-id="b70d7-125">Meta `Microsoft.AspNetCore.All` paket sadece .NET Core 2.0'ı hedefler, çünkü .NET Core 2.0 Runtime Store ile kullanılmak üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="b70d7-125">The `Microsoft.AspNetCore.All` metapackage targets .NET Core 2.0 only, because it's intended to be used with the .NET Core 2.0 Runtime Store.</span></span>

## <a name="configuration-update"></a><span data-ttu-id="b70d7-126">Yapılandırma güncelleştirmesi</span><span class="sxs-lookup"><span data-stu-id="b70d7-126">Configuration update</span></span>

<span data-ttu-id="b70d7-127">Bir `IConfiguration` örnek, varsayılan olarak ASP.NET Core 2.0'da hizmet kapsayıcısına eklenir.</span><span class="sxs-lookup"><span data-stu-id="b70d7-127">An `IConfiguration` instance is added to the services container by default in ASP.NET Core 2.0.</span></span> <span data-ttu-id="b70d7-128">`IConfiguration`hizmet kapsayıcısında, uygulamaların konfigürasyon değerlerini kapsayıcıdan almalarını kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="b70d7-128">`IConfiguration` in the services container makes it easier for applications to retrieve configuration values from the container.</span></span>

<span data-ttu-id="b70d7-129">Planlanan belgelerin durumu hakkında bilgi için [GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/3387)bakın.</span><span class="sxs-lookup"><span data-stu-id="b70d7-129">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3387).</span></span>

## <a name="logging-update"></a><span data-ttu-id="b70d7-130">Günlük güncelleştirmesi</span><span class="sxs-lookup"><span data-stu-id="b70d7-130">Logging update</span></span>

<span data-ttu-id="b70d7-131">Core 2.0ASP.NET, günlük varsayılan olarak bağımlılık enjeksiyon (DI) sistemine dahil edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="b70d7-131">In ASP.NET Core 2.0, logging is incorporated into the dependency injection (DI) system by default.</span></span> <span data-ttu-id="b70d7-132">*Sağlayıcılar* ekler ve Startup.cs dosyası yerine *Program.cs* dosyasında filtreleme yapılandırmak.</span><span class="sxs-lookup"><span data-stu-id="b70d7-132">You add providers and configure filtering in the *Program.cs* file instead of in the *Startup.cs* file.</span></span> <span data-ttu-id="b70d7-133">Ve varsayılan `ILoggerFactory` filtreleme, hem çapraz sağlayıcı filtreleme hem de belirli sağlayıcı filtreleme için tek bir esnek yaklaşım kullanmanızı sağlayacak şekilde filtrelemeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="b70d7-133">And the default `ILoggerFactory` supports filtering in a way that lets you use one flexible approach for both cross-provider filtering and specific-provider filtering.</span></span>

<span data-ttu-id="b70d7-134">Daha fazla bilgi için, [bkz.](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="b70d7-134">For more information, see [Introduction to Logging](xref:fundamentals/logging/index).</span></span>

## <a name="authentication-update"></a><span data-ttu-id="b70d7-135">Kimlik doğrulama güncelleştirmesi</span><span class="sxs-lookup"><span data-stu-id="b70d7-135">Authentication update</span></span>

<span data-ttu-id="b70d7-136">Yeni bir kimlik doğrulama modeli, DI kullanarak bir uygulama için kimlik doğrulamayapılandırmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="b70d7-136">A new authentication model makes it easier to configure authentication for an application using DI.</span></span>

<span data-ttu-id="b70d7-137">[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)kullanarak web uygulamaları ve web API'leri için kimlik doğrulamayı yapılandırmak için yeni şablonlar mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="b70d7-137">New templates are available for configuring authentication for web apps and web APIs using [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).</span></span>

<span data-ttu-id="b70d7-138">Planlanan belgelerin durumu hakkında bilgi için [GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/3054)bakın.</span><span class="sxs-lookup"><span data-stu-id="b70d7-138">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3054).</span></span>

## <a name="identity-update"></a><span data-ttu-id="b70d7-139">Kimlik güncelleştirmesi</span><span class="sxs-lookup"><span data-stu-id="b70d7-139">Identity update</span></span>

<span data-ttu-id="b70d7-140">ASP.NET Core 2.0'da Identity'i kullanarak güvenli web API'leri oluşturmayı kolaylaştırdık.</span><span class="sxs-lookup"><span data-stu-id="b70d7-140">We've made it easier to build secure web APIs using Identity in ASP.NET Core 2.0.</span></span> <span data-ttu-id="b70d7-141">[Microsoft Kimlik Doğrulama Kitaplığı'nı (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client)kullanarak web API'larınıza erişmek için erişim belirteçleri edinebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b70d7-141">You can acquire access tokens for accessing your web APIs using the [Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span></span>

<span data-ttu-id="b70d7-142">2.0'daki kimlik doğrulama değişiklikleri hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="b70d7-142">For more information on authentication changes in 2.0, see the following resources:</span></span>

* [<span data-ttu-id="b70d7-143">ASP.NET Core'da hesap onayı ve parola kurtarma</span><span class="sxs-lookup"><span data-stu-id="b70d7-143">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="b70d7-144">ASP.NET Core'daki kimlik doğrulama uygulamaları için QR Kodu oluşturmayı etkinleştirin</span><span class="sxs-lookup"><span data-stu-id="b70d7-144">Enable QR Code generation for authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="b70d7-145">Kimlik Doğrulamayı ve Kimliği ASP.NET Core 2.0'a geçirin</span><span class="sxs-lookup"><span data-stu-id="b70d7-145">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a><span data-ttu-id="b70d7-146">SPA şablonları</span><span class="sxs-lookup"><span data-stu-id="b70d7-146">SPA templates</span></span>

<span data-ttu-id="b70d7-147">Angular, Aurelia, Knockout.js, React.js ve React.js redux için tek sayfalık uygulama (SPA) proje şablonları mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="b70d7-147">Single Page Application (SPA) project templates for Angular, Aurelia, Knockout.js, React.js, and React.js with Redux are available.</span></span> <span data-ttu-id="b70d7-148">Açısal şablon Açısal 4 olarak güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="b70d7-148">The Angular template has been updated to Angular 4.</span></span> <span data-ttu-id="b70d7-149">Açısal ve Tepki şablonları varsayılan olarak kullanılabilir; diğer şablonları nasıl edineceklerine ilişkin bilgi için [bkz.](xref:client-side/spa-services#create-a-new-project)</span><span class="sxs-lookup"><span data-stu-id="b70d7-149">The Angular and React templates are available by default; for information about how to get the other templates, see [Create a new SPA project](xref:client-side/spa-services#create-a-new-project).</span></span> <span data-ttu-id="b70d7-150">ASP.NET Core'da spa nasıl inşa edilebilenhakkında bilgi için bkz. <xref:client-side/spa-services></span><span class="sxs-lookup"><span data-stu-id="b70d7-150">For information about how to build a SPA in ASP.NET Core, see <xref:client-side/spa-services>.</span></span>

## <a name="kestrel-improvements"></a><span data-ttu-id="b70d7-151">Kerkenez iyileştirmeleri</span><span class="sxs-lookup"><span data-stu-id="b70d7-151">Kestrel improvements</span></span>

<span data-ttu-id="b70d7-152">Kestrel web sunucusu, internete bakan bir sunucu olarak daha uygun hale getiren yeni özelliklere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="b70d7-152">The Kestrel web server has new features that make it more suitable as an Internet-facing server.</span></span> <span data-ttu-id="b70d7-153">Sınıfın yeni `KestrelServerOptions` `Limits` özelliğine bir dizi sunucu kısıtlaması yapılandırma seçeneği eklenir.</span><span class="sxs-lookup"><span data-stu-id="b70d7-153">A number of server constraint configuration options are added in the `KestrelServerOptions` class's new `Limits` property.</span></span> <span data-ttu-id="b70d7-154">Aşağıdakiler için sınırlar ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b70d7-154">Add limits for the following:</span></span>

* <span data-ttu-id="b70d7-155">Maksimum istemci bağlantıları</span><span class="sxs-lookup"><span data-stu-id="b70d7-155">Maximum client connections</span></span>
* <span data-ttu-id="b70d7-156">Maksimum istek gövde boyutu</span><span class="sxs-lookup"><span data-stu-id="b70d7-156">Maximum request body size</span></span>
* <span data-ttu-id="b70d7-157">Minimum istek gövde veri oranı</span><span class="sxs-lookup"><span data-stu-id="b70d7-157">Minimum request body data rate</span></span>

<span data-ttu-id="b70d7-158">Daha fazla bilgi için [ASP.NET Core'daki Kestrel web sunucusu uygulamasına](xref:fundamentals/servers/kestrel)bakın.</span><span class="sxs-lookup"><span data-stu-id="b70d7-158">For more information, see [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel).</span></span>

## <a name="weblistener-renamed-to-httpsys"></a><span data-ttu-id="b70d7-159">WebListener http.sys olarak yeniden adlandırıldı</span><span class="sxs-lookup"><span data-stu-id="b70d7-159">WebListener renamed to HTTP.sys</span></span>

<span data-ttu-id="b70d7-160">Paketler `Microsoft.AspNetCore.Server.WebListener` ve `Microsoft.Net.Http.Server` yeni bir paket `Microsoft.AspNetCore.Server.HttpSys`içine birleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="b70d7-160">The packages `Microsoft.AspNetCore.Server.WebListener` and `Microsoft.Net.Http.Server` have been merged into a new package `Microsoft.AspNetCore.Server.HttpSys`.</span></span> <span data-ttu-id="b70d7-161">Ad alanları eşleşecek şekilde güncelleştirildi.</span><span class="sxs-lookup"><span data-stu-id="b70d7-161">The namespaces have been updated to match.</span></span>

<span data-ttu-id="b70d7-162">Daha fazla bilgi için, [ASP.NET Core HTTP.sys web sunucusu uygulamasıbakın.](xref:fundamentals/servers/httpsys)</span><span class="sxs-lookup"><span data-stu-id="b70d7-162">For more information, see [HTTP.sys web server implementation in ASP.NET Core](xref:fundamentals/servers/httpsys).</span></span>

## <a name="enhanced-http-header-support"></a><span data-ttu-id="b70d7-163">Geliştirilmiş HTTP üstbilgi desteği</span><span class="sxs-lookup"><span data-stu-id="b70d7-163">Enhanced HTTP header support</span></span>

<span data-ttu-id="b70d7-164">`FileStreamResult` Bir veya bir `FileContentResult`iletmek için MVC kullanırken, şimdi `ETag` ilettiğiniz içerik üzerinde bir veya tarih `LastModified` belirleme seçeneğiniz var.</span><span class="sxs-lookup"><span data-stu-id="b70d7-164">When using MVC to transmit a `FileStreamResult` or a `FileContentResult`, you now have the option to set an `ETag` or a `LastModified` date on the content you transmit.</span></span> <span data-ttu-id="b70d7-165">Bu değerleri döndürülen içeriğe aşağıdakilere benzer bir kodla ayarlayabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="b70d7-165">You can set these values on the returned content with code similar to the following:</span></span>

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

<span data-ttu-id="b70d7-166">Ziyaretçilerinize döndürülen dosya, değerler ve değerler `ETag` `LastModified` için uygun HTTP üstbilgilerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="b70d7-166">The file returned to your visitors has the appropriate HTTP headers for the `ETag` and `LastModified` values.</span></span>

<span data-ttu-id="b70d7-167">Bir uygulama ziyaretçisi Bir Aralık İsteği üstbilgisiyle içerik isterse, ASP.NET Core isteği tanır ve üstbilgiyi işler.</span><span class="sxs-lookup"><span data-stu-id="b70d7-167">If an application visitor requests content with a Range Request header, ASP.NET Core recognizes the request and handles the header.</span></span> <span data-ttu-id="b70d7-168">İstenen içerik kısmen teslim edilebilirse, ASP.NET Core yalnızca istenen bayt kümesini atlar ve döndürür.</span><span class="sxs-lookup"><span data-stu-id="b70d7-168">If the requested content can be partially delivered, ASP.NET Core appropriately skips and returns just the requested set of bytes.</span></span> <span data-ttu-id="b70d7-169">Bu özelliği uyarlamak veya işlemek için yöntemlerinize özel işleyiciler yazmanız gerekmez; sizin için otomatik olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="b70d7-169">You don't need to write any special handlers into your methods to adapt or handle this feature; it's automatically handled for you.</span></span>

## <a name="hosting-startup-and-application-insights"></a><span data-ttu-id="b70d7-170">Başlangıç ve Uygulama Öngörülerini Barındırma</span><span class="sxs-lookup"><span data-stu-id="b70d7-170">Hosting startup and Application Insights</span></span>

<span data-ttu-id="b70d7-171">Barındırma ortamları artık uygulamanın başlatılması sırasında, uygulamanın açıkça bir bağımlılık almasına veya herhangi bir yöntem emretmeye gerek kalmadan ek paket bağımlılıkları enjekte edebilir ve kodu yürütebilir.</span><span class="sxs-lookup"><span data-stu-id="b70d7-171">Hosting environments can now inject extra package dependencies and execute code during application startup, without the application needing to explicitly take a dependency or call any methods.</span></span> <span data-ttu-id="b70d7-172">Bu özellik, uygulamanın önceden bilmesine gerek kalmadan belirli ortamların o ortama özgü özellikleri "aydınlatmasını" sağlamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b70d7-172">This feature can be used to enable certain environments to "light-up" features unique to that environment without the application needing to know ahead of time.</span></span> 

<span data-ttu-id="b70d7-173">Core 2.0ASP.NETbu özellik, Visual Studio'da hata ayıklama yaparken ve Azure Uygulama Hizmetleri'nde çalışırken (kabul ettikten sonra) Uygulama Öngörüleri tanılamalarını otomatik olarak etkinleştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b70d7-173">In ASP.NET Core 2.0, this feature is used to automatically enable Application Insights diagnostics when debugging in Visual Studio and (after opting in) when running in Azure App Services.</span></span> <span data-ttu-id="b70d7-174">Sonuç olarak, proje şablonları artık varsayılan olarak Uygulama Öngörüleri paketleri ve kodu eklemez.</span><span class="sxs-lookup"><span data-stu-id="b70d7-174">As a result, the project templates no longer add Application Insights packages and code by default.</span></span>

<span data-ttu-id="b70d7-175">Planlanan belgelerin durumu hakkında bilgi için [GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/3389)bakın.</span><span class="sxs-lookup"><span data-stu-id="b70d7-175">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3389).</span></span>

## <a name="automatic-use-of-anti-forgery-tokens"></a><span data-ttu-id="b70d7-176">Sahteciliğin önleyici belirteçlerinin otomatik kullanımı</span><span class="sxs-lookup"><span data-stu-id="b70d7-176">Automatic use of anti-forgery tokens</span></span>

<span data-ttu-id="b70d7-177">ASP.NET Core her zaman varsayılan olarak HTML-kod içeriği yardımcı oldu, ancak yeni sürüm ile ekstra bir adım siteleri arası istek sahteciliği (XSRF) saldırılarını önlemeye yardımcı olmak için alınır.</span><span class="sxs-lookup"><span data-stu-id="b70d7-177">ASP.NET Core has always helped HTML-encode content by default, but with the new version an extra step is taken to help prevent cross-site request forgery (XSRF) attacks.</span></span> <span data-ttu-id="b70d7-178">ASP.NET Core şimdi varsayılan olarak sahtecilik karşıtı belirteçler yatacak ve bunları ekstra yapılandırma olmadan form POST eylemleri ve sayfalarında doğrulayacaktır.</span><span class="sxs-lookup"><span data-stu-id="b70d7-178">ASP.NET Core will now emit anti-forgery tokens by default and validate them on form POST actions and pages without extra configuration.</span></span>

<span data-ttu-id="b70d7-179">Daha fazla bilgi için [bkz.](xref:security/anti-request-forgery)</span><span class="sxs-lookup"><span data-stu-id="b70d7-179">For more information, see [Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks](xref:security/anti-request-forgery).</span></span>

## <a name="automatic-precompilation"></a><span data-ttu-id="b70d7-180">Otomatik precompilation</span><span class="sxs-lookup"><span data-stu-id="b70d7-180">Automatic precompilation</span></span>

<span data-ttu-id="b70d7-181">Razor view ön derlemesi varsayılan olarak yayımlama sırasında etkinleştirilerek çıktı boyutunu ve uygulama başlatma süresini azaltır.</span><span class="sxs-lookup"><span data-stu-id="b70d7-181">Razor view pre-compilation is enabled during publish by default, reducing the publish output size and application startup time.</span></span>

<span data-ttu-id="b70d7-182">Daha fazla bilgi için, [ASP.NET Core'da Razor görünümü derlemesi ve precompilation'a](xref:mvc/views/view-compilation)bakın.</span><span class="sxs-lookup"><span data-stu-id="b70d7-182">For more information, see [Razor view compilation and precompilation in ASP.NET Core](xref:mvc/views/view-compilation).</span></span>

## <a name="razor-support-for-c-71"></a><span data-ttu-id="b70d7-183">C# 7.1 için jilet desteği</span><span class="sxs-lookup"><span data-stu-id="b70d7-183">Razor support for C# 7.1</span></span>

<span data-ttu-id="b70d7-184">Razor görünüm motoru yeni Roslyn derleyicisi ile çalışmak üzere güncellendi.</span><span class="sxs-lookup"><span data-stu-id="b70d7-184">The Razor view engine has been updated to work with the new Roslyn compiler.</span></span> <span data-ttu-id="b70d7-185">Buna Varsayılan İfadeler, Çıkarılan Tuple Adları ve Genel Özelliklerle Desen Eşleştirme gibi C# 7.1 özellikleri desteği de dahildir.</span><span class="sxs-lookup"><span data-stu-id="b70d7-185">That includes support for C# 7.1 features like Default Expressions, Inferred Tuple Names, and Pattern-Matching with Generics.</span></span> <span data-ttu-id="b70d7-186">Projenizde C# 7.1'i kullanmak için proje dosyanıza aşağıdaki özelliği ekleyin ve ardından çözümü yeniden yükleyin:</span><span class="sxs-lookup"><span data-stu-id="b70d7-186">To use C# 7.1 in your project, add the following property in your project file and then reload the solution:</span></span>

```xml
<LangVersion>latest</LangVersion>
```

<span data-ttu-id="b70d7-187">C# 7.1 özelliklerinin durumu hakkında bilgi için [Roslyn GitHub deposuna](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="b70d7-187">For information about the status of C# 7.1 features, see [the Roslyn GitHub repository](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span></span>

## <a name="other-documentation-updates-for-20"></a><span data-ttu-id="b70d7-188">2.0 için diğer dokümantasyon güncellemeleri</span><span class="sxs-lookup"><span data-stu-id="b70d7-188">Other documentation updates for 2.0</span></span>

* [<span data-ttu-id="b70d7-189">Visual Studio ASP.NET Core uygulama dağıtımı için profiller yayınlamak</span><span class="sxs-lookup"><span data-stu-id="b70d7-189">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>](xref:host-and-deploy/visual-studio-publish-profiles)
* [<span data-ttu-id="b70d7-190">Anahtar Yönetimi</span><span class="sxs-lookup"><span data-stu-id="b70d7-190">Key Management</span></span>](xref:security/data-protection/implementation/key-management)
* [<span data-ttu-id="b70d7-191">Facebook kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b70d7-191">Configure Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="b70d7-192">Twitter kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b70d7-192">Configure Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="b70d7-193">Google kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b70d7-193">Configure Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="b70d7-194">Microsoft Hesabı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b70d7-194">Configure Microsoft Account authentication</span></span>](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a><span data-ttu-id="b70d7-195">Geçiş kılavuzu</span><span class="sxs-lookup"><span data-stu-id="b70d7-195">Migration guidance</span></span>

<span data-ttu-id="b70d7-196">Core 1.x ASP.NET uygulamalarının ASP.NET Core 2.0'a nasıl geçirilir ekiyle ilgili rehberlik için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="b70d7-196">For guidance on how to migrate ASP.NET Core 1.x applications to ASP.NET Core 2.0, see the following resources:</span></span>

* [<span data-ttu-id="b70d7-197">ASP.NET Core 1.x'ten ASP.NET Core 2.0'a geçirin</span><span class="sxs-lookup"><span data-stu-id="b70d7-197">Migrate from ASP.NET Core 1.x to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/index)
* [<span data-ttu-id="b70d7-198">Kimlik Doğrulamayı ve Kimliği ASP.NET Core 2.0'a geçirin</span><span class="sxs-lookup"><span data-stu-id="b70d7-198">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a><span data-ttu-id="b70d7-199">Ek Bilgi</span><span class="sxs-lookup"><span data-stu-id="b70d7-199">Additional Information</span></span>

<span data-ttu-id="b70d7-200">Değişikliklerin tam listesi için [Core 2.0 Sürüm Notları ASP.NET](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0)bakın.</span><span class="sxs-lookup"><span data-stu-id="b70d7-200">For the complete list of changes, see the [ASP.NET Core 2.0 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).</span></span>

<span data-ttu-id="b70d7-201">ASP.NET Core geliştirme ekibinin ilerleme ve planları ile bağlantı kurmak [için, ASP.NET Topluluk Standup'ı](https://live.asp.net/)ile bağlantı kurun.</span><span class="sxs-lookup"><span data-stu-id="b70d7-201">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
