---
title: ASP.NET Core 2,1 ' deki yenilikler
author: isaac2004
description: ASP.NET Core 2,1 ' deki yeni özellikler hakkında bilgi edinin.
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
uid: aspnetcore-2.1
ms.openlocfilehash: 62fc9d866adcf05ff024501db68cce8bb8b11a98
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059721"
---
# <a name="whats-new-in-aspnet-core-21"></a><span data-ttu-id="cf8af-103">ASP.NET Core 2,1 ' deki yenilikler</span><span class="sxs-lookup"><span data-stu-id="cf8af-103">What's new in ASP.NET Core 2.1</span></span>

<span data-ttu-id="cf8af-104">Bu makalede, ASP.NET Core 2,1 ' deki en önemli değişiklikler, ilgili belgelerin bağlantılarıyla vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="cf8af-104">This article highlights the most significant changes in ASP.NET Core 2.1, with links to relevant documentation.</span></span>

## SignalR

<span data-ttu-id="cf8af-105">SignalR ASP.NET Core 2,1 için yeniden yazıldı.</span><span class="sxs-lookup"><span data-stu-id="cf8af-105">SignalR has been rewritten for ASP.NET Core 2.1.</span></span> <span data-ttu-id="cf8af-106">ASP.NET Core SignalR birkaç geliştirme içerir:</span><span class="sxs-lookup"><span data-stu-id="cf8af-106">ASP.NET Core SignalR includes a number of improvements:</span></span>

* <span data-ttu-id="cf8af-107">Basitleştirilmiş bir genişleme modeli.</span><span class="sxs-lookup"><span data-stu-id="cf8af-107">A simplified scale-out model.</span></span>
* <span data-ttu-id="cf8af-108">JQuery bağımlılığı olmayan yeni bir JavaScript istemcisi.</span><span class="sxs-lookup"><span data-stu-id="cf8af-108">A new JavaScript client with no jQuery dependency.</span></span>
* <span data-ttu-id="cf8af-109">MessagePack tabanlı yeni bir Compact binary protokolü.</span><span class="sxs-lookup"><span data-stu-id="cf8af-109">A new compact binary protocol based on MessagePack.</span></span>
* <span data-ttu-id="cf8af-110">Özel protokoller için destek.</span><span class="sxs-lookup"><span data-stu-id="cf8af-110">Support for custom protocols.</span></span>
* <span data-ttu-id="cf8af-111">Yeni bir akış yanıt modeli.</span><span class="sxs-lookup"><span data-stu-id="cf8af-111">A new streaming response model.</span></span>
* <span data-ttu-id="cf8af-112">Çıplak WebSockets tabanlı istemciler için destek.</span><span class="sxs-lookup"><span data-stu-id="cf8af-112">Support for clients based on bare WebSockets.</span></span>

<span data-ttu-id="cf8af-113">Daha fazla bilgi için bkz [. SignalR ASP.NET Core ](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="cf8af-113">For more information, see [ASP.NET Core SignalR](xref:signalr/introduction).</span></span>

## <a name="no-locrazor-class-libraries"></a><span data-ttu-id="cf8af-114">Razor sınıf kitaplıkları</span><span class="sxs-lookup"><span data-stu-id="cf8af-114">Razor class libraries</span></span>

<span data-ttu-id="cf8af-115">ASP.NET Core 2,1, bir kitaplıkta derleme ve ekleme temelli Kullanıcı arabirimini daha kolay hale getirir Razor ve bunu birden fazla proje arasında paylaşabilir.</span><span class="sxs-lookup"><span data-stu-id="cf8af-115">ASP.NET Core 2.1 makes it easier to build and include Razor-based UI in a library and share it across multiple projects.</span></span> <span data-ttu-id="cf8af-116">Yeni Razor SDK, Razor bir NuGet paketine paketlenebilecek bir sınıf kitaplığı projesinde dosya oluşturulmasına izin vermez.</span><span class="sxs-lookup"><span data-stu-id="cf8af-116">The new Razor SDK enables building Razor files into a class library project that can be packaged into a NuGet package.</span></span> <span data-ttu-id="cf8af-117">Kitaplıklardaki görünümler ve sayfalar otomatik olarak keşfedilir ve uygulama tarafından geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="cf8af-117">Views and pages in libraries are automatically discovered and can be overridden by the app.</span></span> <span data-ttu-id="cf8af-118">RazorDerlemeyi derlemeyle tümleştirerek:</span><span class="sxs-lookup"><span data-stu-id="cf8af-118">By integrating Razor compilation into the build:</span></span>

* <span data-ttu-id="cf8af-119">Uygulama başlatma süresi önemli ölçüde daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="cf8af-119">The app startup time is significantly faster.</span></span>
* <span data-ttu-id="cf8af-120">RazorÇalışma zamanında görünümler ve sayfalar için hızlı güncelleştirmeler, yinelemeli bir geliştirme iş akışının bir parçası olarak hala kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="cf8af-120">Fast updates to Razor views and pages at runtime are still available as part of an iterative development workflow.</span></span>

<span data-ttu-id="cf8af-121">Daha fazla bilgi için bkz. [ Razor Sınıf Kitaplığı projesini kullanarak yeniden kullanılabilir kullanıcı arabirimi oluşturma](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="cf8af-121">For more information, see [Create reusable UI using the Razor Class Library project](xref:razor-pages/ui-class).</span></span>

## <a name="no-locidentity-ui-library--scaffolding"></a><span data-ttu-id="cf8af-122">Identity UI kitaplığı & yapı iskelesi</span><span class="sxs-lookup"><span data-stu-id="cf8af-122">Identity UI library & scaffolding</span></span>

<span data-ttu-id="cf8af-123">ASP.NET Core 2,1, [ASP.NET Core Identity](xref:security/authentication/identity) bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="cf8af-123">ASP.NET Core 2.1 provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="cf8af-124">Dahil edilen uygulamalar, Identity Identity Identity Razor sınıf kitaplığı 'nda (RCL) bulunan kaynak kodunu seçmeli olarak eklemek için yeni desteği uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="cf8af-124">Apps that include Identity can apply the new Identity scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="cf8af-125">Kodu değiştirebilmeniz ve davranışı değiştirebilmek için kaynak kodu oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cf8af-125">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="cf8af-126">Örneğin, kayıt sırasında kullanılan kodu oluşturmak için desteği ' ı söyleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cf8af-126">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="cf8af-127">Oluşturulan kod RCL 'deki aynı koda göre önceliklidir Identity .</span><span class="sxs-lookup"><span data-stu-id="cf8af-127">Generated code takes precedence over the same code in the Identity RCL.</span></span>

<span data-ttu-id="cf8af-128">Kimlik **doğrulaması içermeyen uygulamalar** , Identity RCL paketini eklemek için desteği uygulayabilir Identity .</span><span class="sxs-lookup"><span data-stu-id="cf8af-128">Apps that do **not** include authentication can apply the Identity scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="cf8af-129">Oluşturulacak kodu seçme seçeneğiniz vardır Identity .</span><span class="sxs-lookup"><span data-stu-id="cf8af-129">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="cf8af-130">Daha fazla bilgi için bkz. [ Identity ASP.NET Core projelerinde Scaffold](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="cf8af-130">For more information, see [Scaffold Identity in ASP.NET Core projects](xref:security/authentication/scaffold-identity).</span></span>

## <a name="https"></a><span data-ttu-id="cf8af-131">HTTPS</span><span class="sxs-lookup"><span data-stu-id="cf8af-131">HTTPS</span></span>

<span data-ttu-id="cf8af-132">Güvenlik ve gizlilikle ilgili daha fazla odaklanarak Web Apps için HTTPS 'nin etkinleştirilmesi önemlidir.</span><span class="sxs-lookup"><span data-stu-id="cf8af-132">With the increased focus on security and privacy, enabling HTTPS for web apps is important.</span></span> <span data-ttu-id="cf8af-133">HTTPS zorlaması Web üzerinde giderek daha sıkı hale geliyor.</span><span class="sxs-lookup"><span data-stu-id="cf8af-133">HTTPS enforcement is becoming increasingly strict on the web.</span></span> <span data-ttu-id="cf8af-134">HTTPS kullanmayan siteler güvensiz olarak değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="cf8af-134">Sites that don't use HTTPS are considered insecure.</span></span> <span data-ttu-id="cf8af-135">Tarayıcılar (Bermıum, Mozilla), Web özelliklerinin güvenli bir içerikten kullanılması gerektiğini zorlamaya başlıyor.</span><span class="sxs-lookup"><span data-stu-id="cf8af-135">Browsers (Chromium, Mozilla) are starting to enforce that web features must be used from a secure context.</span></span> <span data-ttu-id="cf8af-136">[GDPR](xref:security/gdpr) , Kullanıcı gizliliğini korumak için https kullanılmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="cf8af-136">[GDPR](xref:security/gdpr) requires the use of HTTPS to protect user privacy.</span></span> <span data-ttu-id="cf8af-137">Üretimde HTTPS kullanılması kritiktir, ancak geliştirme sırasında HTTPS kullanılması, dağıtımdaki sorunları önlemeye yardımcı olabilir (örneğin, güvenli olmayan bağlantılar).</span><span class="sxs-lookup"><span data-stu-id="cf8af-137">While using HTTPS in production is critical, using HTTPS in development can help prevent issues in deployment (for example, insecure links).</span></span> <span data-ttu-id="cf8af-138">ASP.NET Core 2,1, geliştirme sırasında HTTPS kullanmayı ve üretimde HTTPS 'yi yapılandırmayı kolaylaştıran bir dizi geliştirme içerir.</span><span class="sxs-lookup"><span data-stu-id="cf8af-138">ASP.NET Core 2.1 includes a number of improvements that make it easier to use HTTPS in development and to configure HTTPS in production.</span></span> <span data-ttu-id="cf8af-139">Daha fazla bilgi için bkz. [https 'Yi zorlama](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="cf8af-139">For more information, see [Enforce HTTPS](xref:security/enforcing-ssl).</span></span>

### <a name="on-by-default"></a><span data-ttu-id="cf8af-140">Varsayılan olarak açık</span><span class="sxs-lookup"><span data-stu-id="cf8af-140">On by default</span></span>

<span data-ttu-id="cf8af-141">Güvenli Web sitesi geliştirmeyi kolaylaştırmak için, HTTPS artık varsayılan olarak etkinleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="cf8af-141">To facilitate secure website development, HTTPS  is now enabled by default.</span></span> <span data-ttu-id="cf8af-142">2,1 ' den başlayarak, Kestrel `https://localhost:5001` bir yerel geliştirme sertifikası olduğunda dinler.</span><span class="sxs-lookup"><span data-stu-id="cf8af-142">Starting in 2.1, Kestrel listens on `https://localhost:5001` when a local development certificate is present.</span></span> <span data-ttu-id="cf8af-143">Geliştirme sertifikası oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="cf8af-143">A development certificate is created:</span></span>

* <span data-ttu-id="cf8af-144">İlk kez .NET Core SDK ilk çalıştırma deneyiminin bir parçası olarak SDK 'Yı ilk kez kullandığınızda.</span><span class="sxs-lookup"><span data-stu-id="cf8af-144">As part of the .NET Core SDK first-run experience, when you use the SDK for the first time.</span></span>
* <span data-ttu-id="cf8af-145">Yeni aracı kullanarak el ile `dev-certs` .</span><span class="sxs-lookup"><span data-stu-id="cf8af-145">Manually using the new `dev-certs` tool.</span></span>

<span data-ttu-id="cf8af-146">`dotnet dev-certs https --trust`Sertifikaya güvenmek için ' i çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="cf8af-146">Run `dotnet dev-certs https --trust` to trust the certificate.</span></span>

### <a name="https-redirection-and-enforcement"></a><span data-ttu-id="cf8af-147">HTTPS yönlendirmesi ve zorlaması</span><span class="sxs-lookup"><span data-stu-id="cf8af-147">HTTPS redirection and enforcement</span></span>

<span data-ttu-id="cf8af-148">Web uygulamalarının genellikle hem HTTP hem de HTTPS üzerinde dinlemesi gerekir, ancak tüm HTTP trafiğini HTTPS 'ye yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="cf8af-148">Web apps typically need to listen on both HTTP and HTTPS, but then redirect all HTTP traffic to HTTPS.</span></span> <span data-ttu-id="cf8af-149">2,1 ' de, yapılandırma veya bağlı sunucu bağlantı noktalarının varlığına göre daha fazla yeniden yönlendirilen özelleştirilmiş HTTPS yeniden yönlendirme ara yazılımı sunulmuştur.</span><span class="sxs-lookup"><span data-stu-id="cf8af-149">In 2.1, specialized HTTPS redirection middleware that intelligently redirects based on the presence of configuration or bound server ports has been introduced.</span></span>

<span data-ttu-id="cf8af-150">HTTPS kullanımı, [http katı aktarım güvenliği Protokolü (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)kullanılarak daha fazla zorlanabilir.</span><span class="sxs-lookup"><span data-stu-id="cf8af-150">Use of HTTPS can be further enforced using [HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="cf8af-151">HSTS, tarayıcıların siteye her zaman HTTPS aracılığıyla erişmesini söyler.</span><span class="sxs-lookup"><span data-stu-id="cf8af-151">HSTS instructs browsers to always access the site via HTTPS.</span></span> <span data-ttu-id="cf8af-152">ASP.NET Core 2,1, maksimum yaş, alt etki alanları ve HSTS önyükleme listesi seçeneklerini destekleyen HSTS ara yazılımı ekler.</span><span class="sxs-lookup"><span data-stu-id="cf8af-152">ASP.NET Core 2.1 adds HSTS middleware that supports options for max age, subdomains, and the HSTS preload list.</span></span>

### <a name="configuration-for-production"></a><span data-ttu-id="cf8af-153">Üretim için yapılandırma</span><span class="sxs-lookup"><span data-stu-id="cf8af-153">Configuration for production</span></span>

<span data-ttu-id="cf8af-154">Üretimde HTTPS 'nin açıkça yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="cf8af-154">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="cf8af-155">2,1 ' de, Kestrel için HTTPS yapılandırmasına yönelik varsayılan yapılandırma şeması eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="cf8af-155">In 2.1, default configuration schema for configuring HTTPS for Kestrel has been added.</span></span> <span data-ttu-id="cf8af-156">Uygulamalar aşağıdakileri kullanacak şekilde yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="cf8af-156">Apps can be configured to use:</span></span>

* <span data-ttu-id="cf8af-157">URL 'Ler dahil olmak üzere birden fazla uç nokta.</span><span class="sxs-lookup"><span data-stu-id="cf8af-157">Multiple endpoints including the URLs.</span></span> <span data-ttu-id="cf8af-158">Daha fazla bilgi için bkz. [Kestrel Web Server Implementation: Endpoint Configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="cf8af-158">For more information, see [Kestrel web server implementation: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>
* <span data-ttu-id="cf8af-159">Diskteki bir dosyadan veya bir sertifika deposundan HTTPS için kullanılacak sertifika.</span><span class="sxs-lookup"><span data-stu-id="cf8af-159">The certificate to use for HTTPS either from a file on disk or from a certificate store.</span></span>

## <a name="gdpr"></a><span data-ttu-id="cf8af-160">GDPR</span><span class="sxs-lookup"><span data-stu-id="cf8af-160">GDPR</span></span>

<span data-ttu-id="cf8af-161">ASP.NET Core, bazı [ab genel veri koruma yönetmeliği (GDPR)](https://www.eugdpr.org/) gereksinimlerini karşılamaya yardımcı olmak Için API 'ler ve şablonlar sağlar.</span><span class="sxs-lookup"><span data-stu-id="cf8af-161">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements.</span></span> <span data-ttu-id="cf8af-162">Daha fazla bilgi için bkz. [GDPR Support in ASP.NET Core](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="cf8af-162">For more information, see [GDPR support in ASP.NET Core](xref:security/gdpr).</span></span> <span data-ttu-id="cf8af-163">[Örnek bir uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) , ' nin nasıl kullanılacağını gösterir ve ASP.NET Core 2,1 ŞABLONLARıNA eklenen GDPR uzantı noktalarının ve API 'lerin çoğunu test etmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="cf8af-163">A [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) shows how to use and lets you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span>

## <a name="integration-tests"></a><span data-ttu-id="cf8af-164">Tümleştirme testleri</span><span class="sxs-lookup"><span data-stu-id="cf8af-164">Integration tests</span></span>

<span data-ttu-id="cf8af-165">Test oluşturma ve yürütmeyi kolaylaştıran yeni bir paket tanıtılmıştır.</span><span class="sxs-lookup"><span data-stu-id="cf8af-165">A new package is introduced that streamlines test creation and execution.</span></span> <span data-ttu-id="cf8af-166">[Microsoft. AspNetCore. Mvc. Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) paketi aşağıdaki görevleri gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="cf8af-166">The [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) package handles the following tasks:</span></span>

* <span data-ttu-id="cf8af-167">Bağımlılık dosyasını ( *\* . Deps* ) test projesinin *bin* klasörüne kopyalar.</span><span class="sxs-lookup"><span data-stu-id="cf8af-167">Copies the dependency file ( *\*.deps* ) from the tested app into the test project's *bin* folder.</span></span>
* <span data-ttu-id="cf8af-168">Testler yürütüldüğünde statik dosya ve sayfa/görünümlerin bulunması için, içerik kökünü test edilen uygulamanın proje köküne ayarlar.</span><span class="sxs-lookup"><span data-stu-id="cf8af-168">Sets the content root to the tested app's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="cf8af-169">[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)ile sınanan uygulamayı daha kolay hale getirmek Için [webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) sınıfını sağlar.</span><span class="sxs-lookup"><span data-stu-id="cf8af-169">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the tested app with [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span>

<span data-ttu-id="cf8af-170">Aşağıdaki test, dizin sayfasının başarılı durum kodu ve doğru Content-Type üst bilgisi ile yüklenip yüklenduğunu denetlemek için [xUnit](https://xunit.github.io/) kullanır:</span><span class="sxs-lookup"><span data-stu-id="cf8af-170">The following test uses [xUnit](https://xunit.github.io/) to check that the Index page loads with a success status code and with the correct Content-Type header:</span></span>

```csharp
public class BasicTests
    : IClassFixture<WebApplicationFactory<RazorPagesProject.Startup>>
{
    private readonly HttpClient _client;

    public BasicTests(WebApplicationFactory<RazorPagesProject.Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHomePage()
    {
        // Act
        var response = await _client.GetAsync("/");

        // Assert
        response.EnsureSuccessStatusCode(); // Status Code 200-299
        Assert.Equal("text/html; charset=utf-8",
            response.Content.Headers.ContentType.ToString());
    }
}
```

<span data-ttu-id="cf8af-171">Daha fazla bilgi için bkz. [tümleştirme testleri](xref:test/integration-tests) konusu.</span><span class="sxs-lookup"><span data-stu-id="cf8af-171">For more information, see the [Integration tests](xref:test/integration-tests) topic.</span></span>

## <a name="apicontroller-actionresultt"></a><span data-ttu-id="cf8af-172">[ApiController], ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="cf8af-172">[ApiController], ActionResult\<T></span></span>

<span data-ttu-id="cf8af-173">ASP.NET Core 2,1, temiz ve açıklayıcı Web API 'Leri oluşturmayı kolaylaştıran yeni programlama kuralları ekliyor.</span><span class="sxs-lookup"><span data-stu-id="cf8af-173">ASP.NET Core 2.1 adds new programming conventions that make it easier to build clean and descriptive web APIs.</span></span> <span data-ttu-id="cf8af-174">`ActionResult<T>` , bir uygulamanın yanıt türünü ya da başka bir eylem sonucunu (ıactionresult gibi) döndürmesini sağlamak için yeni bir tür eklenmiştir, ancak yine de yanıt türünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="cf8af-174">`ActionResult<T>` is a new type added to allow an app to return either a response type or any other action result (similar to IActionResult), while still indicating the response type.</span></span> <span data-ttu-id="cf8af-175">`[ApiController]`Özniteliği, Web API 'sine özgü kuralları ve davranışları kabul etmenin yolu olarak da eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="cf8af-175">The `[ApiController]` attribute has also been added as the way to opt in to Web API-specific conventions and behaviors.</span></span>

<span data-ttu-id="cf8af-176">Daha fazla bilgi için bkz. [ASP.NET Core Web API 'Leri oluşturma](xref:web-api/index).</span><span class="sxs-lookup"><span data-stu-id="cf8af-176">For more information, see [Build Web APIs with ASP.NET Core](xref:web-api/index).</span></span>

## <a name="ihttpclientfactory"></a><span data-ttu-id="cf8af-177">Ihttpclientfactory</span><span class="sxs-lookup"><span data-stu-id="cf8af-177">IHttpClientFactory</span></span>

<span data-ttu-id="cf8af-178">ASP.NET Core 2,1 `IHttpClientFactory` , uygulamalarda örneklerini yapılandırmayı ve kullanmayı kolaylaştıran yeni bir hizmet içerir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="cf8af-178">ASP.NET Core 2.1 includes a new `IHttpClientFactory` service that makes it easier to configure and consume instances of `HttpClient` in apps.</span></span> <span data-ttu-id="cf8af-179">`HttpClient` , giden HTTP istekleri için birlikte bağlanabilen işleyicileri temsilci seçme kavramına zaten sahiptir.</span><span class="sxs-lookup"><span data-stu-id="cf8af-179">`HttpClient` already has the concept of delegating handlers that could be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="cf8af-180">Fabrika:</span><span class="sxs-lookup"><span data-stu-id="cf8af-180">The factory:</span></span>

* <span data-ttu-id="cf8af-181">`HttpClient`Adlandırılmış istemci başına örnek kaydı daha sezgisel hale gelir.</span><span class="sxs-lookup"><span data-stu-id="cf8af-181">Makes registering of instances of `HttpClient` per named client more intuitive.</span></span>
* <span data-ttu-id="cf8af-182">Yeniden deneme, devre kesiciler vb. için, Polly ilkelerinin kullanılmasına izin veren bir Polly işleyici uygular.</span><span class="sxs-lookup"><span data-stu-id="cf8af-182">Implements a Polly handler that allows Polly policies to be used for Retry, CircuitBreakers, etc.</span></span>

<span data-ttu-id="cf8af-183">Daha fazla bilgi için bkz. [http Isteklerini başlatma](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="cf8af-183">For more information, see [Initiate HTTP Requests](xref:fundamentals/http-requests).</span></span>

## <a name="kestrel-transport-configuration"></a><span data-ttu-id="cf8af-184">Kestrel aktarma yapılandırması</span><span class="sxs-lookup"><span data-stu-id="cf8af-184">Kestrel transport configuration</span></span>

<span data-ttu-id="cf8af-185">ASP.NET Core 2,1 sürümü ile Kestrel 'in varsayılan taşıması artık libuv ' d i temel değildir ancak bunun yerine yönetilen yuvaları temel alır.</span><span class="sxs-lookup"><span data-stu-id="cf8af-185">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="cf8af-186">Daha fazla bilgi için bkz. [Kestrel Web sunucusu uygulama: aktarım yapılandırması](xref:fundamentals/servers/kestrel#transport-configuration).</span><span class="sxs-lookup"><span data-stu-id="cf8af-186">For more information, see [Kestrel web server implementation: Transport configuration](xref:fundamentals/servers/kestrel#transport-configuration).</span></span>

## <a name="generic-host-builder"></a><span data-ttu-id="cf8af-187">Genel konak Oluşturucu</span><span class="sxs-lookup"><span data-stu-id="cf8af-187">Generic host builder</span></span>

<span data-ttu-id="cf8af-188">Genel ana bilgisayar Oluşturucu ( `HostBuilder` ) sunuldu.</span><span class="sxs-lookup"><span data-stu-id="cf8af-188">The Generic Host Builder (`HostBuilder`) has been introduced.</span></span> <span data-ttu-id="cf8af-189">Bu Oluşturucu, HTTP isteklerini (mesajlaşma, arka plan görevleri vb.) işlemeyecek uygulamalar için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="cf8af-189">This builder can be used for apps that don't process HTTP requests (Messaging, background tasks, etc.).</span></span>

<span data-ttu-id="cf8af-190">Daha fazla bilgi için bkz. [.NET genel ana bilgisayar](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="cf8af-190">For more information, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

## <a name="updated-spa-templates"></a><span data-ttu-id="cf8af-191">Güncelleştirilmiş SPA şablonları</span><span class="sxs-lookup"><span data-stu-id="cf8af-191">Updated SPA templates</span></span>

<span data-ttu-id="cf8af-192">Redux ile ilgili angular, tepki verme ve tepki verme için tek sayfalı uygulama şablonları, her bir çerçeve için Standart proje yapılarını ve derleme sistemlerini kullanacak şekilde güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="cf8af-192">The Single Page Application templates for Angular, React, and React with Redux are updated to use the standard project structures and build systems for each framework.</span></span>

<span data-ttu-id="cf8af-193">Angular şablonu angular CLı 'yı temel alır ve yanıt verme şablonları Create-tepki-App ' i temel alır.</span><span class="sxs-lookup"><span data-stu-id="cf8af-193">The Angular template is based on the Angular CLI, and the React templates are based on create-react-app.</span></span>

<span data-ttu-id="cf8af-194">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="cf8af-194">For more information, see:</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="no-locrazor-pages-search-for-no-locrazor-assets"></a><span data-ttu-id="cf8af-195">RazorVarlıklar için arama sayfaları Razor</span><span class="sxs-lookup"><span data-stu-id="cf8af-195">Razor Pages search for Razor assets</span></span>

<span data-ttu-id="cf8af-196">2,1 ' de Razor Sayfalar, Razor listelenen sırada aşağıdaki dizinlerde bulunan varlıkları (örneğin, düzenler ve partileri) arar:</span><span class="sxs-lookup"><span data-stu-id="cf8af-196">In 2.1, Razor Pages search for Razor assets (such as layouts and partials) in the following directories in the listed order:</span></span>

1. <span data-ttu-id="cf8af-197">Geçerli sayfalar klasörü.</span><span class="sxs-lookup"><span data-stu-id="cf8af-197">Current Pages folder.</span></span>
1. <span data-ttu-id="cf8af-198">*/Pages/Shared/*</span><span class="sxs-lookup"><span data-stu-id="cf8af-198">*/Pages/Shared/*</span></span>
1. <span data-ttu-id="cf8af-199">*/Views/Shared/*</span><span class="sxs-lookup"><span data-stu-id="cf8af-199">*/Views/Shared/*</span></span>

## <a name="no-locrazor-pages-in-an-area"></a><span data-ttu-id="cf8af-200">Razor Bir alandaki sayfalar</span><span class="sxs-lookup"><span data-stu-id="cf8af-200">Razor Pages in an area</span></span>

<span data-ttu-id="cf8af-201">Razor Sayfalar artık [bölgeleri](xref:mvc/controllers/areas)destekliyor.</span><span class="sxs-lookup"><span data-stu-id="cf8af-201">Razor Pages now support [areas](xref:mvc/controllers/areas).</span></span> <span data-ttu-id="cf8af-202">Bir alan örneği görmek için Razor bireysel kullanıcı hesaplarıyla yeni bir sayfa Web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="cf8af-202">To see an example of areas, create a new Razor Pages web app with individual user accounts.</span></span> <span data-ttu-id="cf8af-203">RazorBireysel kullanıcı hesapları içeren bir sayfa Web uygulaması */Areas/ Identity /Pages* içerir.</span><span class="sxs-lookup"><span data-stu-id="cf8af-203">A Razor Pages web app with individual user accounts includes */Areas/Identity/Pages* .</span></span>

## <a name="mvc-compatibility-version"></a><span data-ttu-id="cf8af-204">MVC uyumluluk sürümü</span><span class="sxs-lookup"><span data-stu-id="cf8af-204">MVC compatibility version</span></span>

<span data-ttu-id="cf8af-205"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>Yöntemi, bir uygulamanın ASP.NET Core MVC 2,1 veya sonraki bir sürümde sunulmuş olabilecek davranış değişikliklerinin kabul etmesine veya devre dışı olmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="cf8af-205">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="cf8af-206">Daha fazla bilgi için bkz. <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="cf8af-206">For more information, see <xref:mvc/compatibility-version>.</span></span>

## <a name="migrate-from-20-to-21"></a><span data-ttu-id="cf8af-207">2,0 ' den 2,1 ' e geçiş</span><span class="sxs-lookup"><span data-stu-id="cf8af-207">Migrate from 2.0 to 2.1</span></span>

<span data-ttu-id="cf8af-208">Bkz. [ASP.NET Core 2,0 ' den 2,1 ' e geçiş](xref:migration/20_21).</span><span class="sxs-lookup"><span data-stu-id="cf8af-208">See [Migrate from ASP.NET Core 2.0 to 2.1](xref:migration/20_21).</span></span>

## <a name="additional-information"></a><span data-ttu-id="cf8af-209">Ek bilgiler</span><span class="sxs-lookup"><span data-stu-id="cf8af-209">Additional information</span></span>

<span data-ttu-id="cf8af-210">Değişikliklerin tamamı listesi için [ASP.NET Core 2,1 sürüm notlarına](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0)bakın.</span><span class="sxs-lookup"><span data-stu-id="cf8af-210">For the complete list of changes, see the [ASP.NET Core 2.1 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0).</span></span>
