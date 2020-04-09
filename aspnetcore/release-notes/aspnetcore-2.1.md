---
title: ASP.NET Core 2.1'deki yenilikler
author: isaac2004
description: ASP.NET Core 2.1'deki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: aspnetcore-2.1
ms.openlocfilehash: af5807b782d4acec8c7d40111dc508dfa6127057
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667547"
---
# <a name="whats-new-in-aspnet-core-21"></a><span data-ttu-id="c8d51-103">ASP.NET Core 2.1'deki yenilikler</span><span class="sxs-lookup"><span data-stu-id="c8d51-103">What's new in ASP.NET Core 2.1</span></span>

<span data-ttu-id="c8d51-104">Bu makalede, ilgili belgelere bağlantılar ile ASP.NET Core 2.1 en önemli değişiklikleri vurgulamaktadır.</span><span class="sxs-lookup"><span data-stu-id="c8d51-104">This article highlights the most significant changes in ASP.NET Core 2.1, with links to relevant documentation.</span></span>

## SignalR

SignalR<span data-ttu-id="c8d51-105">Core 2.1 ASP.NET için yeniden yazılmıştır.</span><span class="sxs-lookup"><span data-stu-id="c8d51-105"> has been rewritten for ASP.NET Core 2.1.</span></span> <span data-ttu-id="c8d51-106">ASP.NET SignalR Core bir dizi iyileştirme içerir:</span><span class="sxs-lookup"><span data-stu-id="c8d51-106">ASP.NET Core SignalR includes a number of improvements:</span></span>

* <span data-ttu-id="c8d51-107">Basitleştirilmiş bir ölçeklendirme modeli.</span><span class="sxs-lookup"><span data-stu-id="c8d51-107">A simplified scale-out model.</span></span>
* <span data-ttu-id="c8d51-108">JQuery bağımlılığı olmayan yeni bir JavaScript istemcisi.</span><span class="sxs-lookup"><span data-stu-id="c8d51-108">A new JavaScript client with no jQuery dependency.</span></span>
* <span data-ttu-id="c8d51-109">MessagePack tabanlı yeni bir kompakt ikili protokol.</span><span class="sxs-lookup"><span data-stu-id="c8d51-109">A new compact binary protocol based on MessagePack.</span></span>
* <span data-ttu-id="c8d51-110">Özel protokoller için destek.</span><span class="sxs-lookup"><span data-stu-id="c8d51-110">Support for custom protocols.</span></span>
* <span data-ttu-id="c8d51-111">Yeni bir akış yanıt modeli.</span><span class="sxs-lookup"><span data-stu-id="c8d51-111">A new streaming response model.</span></span>
* <span data-ttu-id="c8d51-112">Çıplak WebSockets dayalı istemciler için destek.</span><span class="sxs-lookup"><span data-stu-id="c8d51-112">Support for clients based on bare WebSockets.</span></span>

<span data-ttu-id="c8d51-113">Daha fazla bilgi için [core SignalRASP.NET ](xref:signalr/introduction)bakın.</span><span class="sxs-lookup"><span data-stu-id="c8d51-113">For more information, see [ASP.NET Core SignalR](xref:signalr/introduction).</span></span>

## <a name="razor-class-libraries"></a><span data-ttu-id="c8d51-114">Razor sınıf kitaplıkları</span><span class="sxs-lookup"><span data-stu-id="c8d51-114">Razor class libraries</span></span>

<span data-ttu-id="c8d51-115">ASP.NET Core 2.1, bir kitaplıkta Jilet tabanlı uI oluşturmayı ve eklemeyi ve birden çok proje arasında paylaşmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="c8d51-115">ASP.NET Core 2.1 makes it easier to build and include Razor-based UI in a library and share it across multiple projects.</span></span> <span data-ttu-id="c8d51-116">Yeni Razor SDK, NuGet paketine paketlenebilen bir sınıf kitaplığı projesine Razor dosyaları oluşturmayı sağlar.</span><span class="sxs-lookup"><span data-stu-id="c8d51-116">The new Razor SDK enables building Razor files into a class library project that can be packaged into a NuGet package.</span></span> <span data-ttu-id="c8d51-117">Kitaplıktaki görünümler ve sayfalar otomatik olarak keşfedilir ve uygulama tarafından geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="c8d51-117">Views and pages in libraries are automatically discovered and can be overridden by the app.</span></span> <span data-ttu-id="c8d51-118">Razor derlemesini yapıya entegre ederek:</span><span class="sxs-lookup"><span data-stu-id="c8d51-118">By integrating Razor compilation into the build:</span></span>

* <span data-ttu-id="c8d51-119">Uygulama başlatma süresi önemli ölçüde daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="c8d51-119">The app startup time is significantly faster.</span></span>
* <span data-ttu-id="c8d51-120">Yinelemeli geliştirme iş akışının bir parçası olarak, çalışma zamanında Kizizatgörünümleri ve sayfalarına yönelik hızlı güncelleştirmeler hala kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="c8d51-120">Fast updates to Razor views and pages at runtime are still available as part of an iterative development workflow.</span></span>

<span data-ttu-id="c8d51-121">Daha fazla bilgi için, [Razor Class Kitaplığı projesini kullanarak yeniden kullanılabilir kullanıcı arabirimi oluşturma'ya](xref:razor-pages/ui-class)bakın.</span><span class="sxs-lookup"><span data-stu-id="c8d51-121">For more information, see [Create reusable UI using the Razor Class Library project](xref:razor-pages/ui-class).</span></span>

## <a name="identity-ui-library--scaffolding"></a><span data-ttu-id="c8d51-122">Kimlik UI kitaplığı & iskele</span><span class="sxs-lookup"><span data-stu-id="c8d51-122">Identity UI library & scaffolding</span></span>

<span data-ttu-id="c8d51-123">ASP.NET Core 2.1, Razor [Sınıf Kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Çekirdek Kimlik](xref:security/authentication/identity) sağlar.</span><span class="sxs-lookup"><span data-stu-id="c8d51-123">ASP.NET Core 2.1 provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="c8d51-124">Kimlik içeren uygulamalar, Kimlik Jilet Sınıf Kitaplığı'nda (RCL) bulunan kaynak kodunu seçarak eklemek için yeni Kimlik iskelesini uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="c8d51-124">Apps that include Identity can apply the new Identity scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="c8d51-125">Kodu değiştirebilmek ve davranışı değiştirmek için kaynak kodu oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c8d51-125">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="c8d51-126">Örneğin, iskele klasörüne kayıtta kullanılan kodu oluşturması için talimat verebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c8d51-126">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="c8d51-127">Oluşturulan kod, Identity RCL'de aynı koddan önce gelir.</span><span class="sxs-lookup"><span data-stu-id="c8d51-127">Generated code takes precedence over the same code in the Identity RCL.</span></span>

<span data-ttu-id="c8d51-128">Kimlik **doğrulamaiçermeyen** uygulamalar, RCL Identity paketini eklemek için Kimlik iskelesini uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="c8d51-128">Apps that do **not** include authentication can apply the Identity scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="c8d51-129">Oluşturulacak Kimlik kodu seçme seçeneğiniz vardır.</span><span class="sxs-lookup"><span data-stu-id="c8d51-129">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="c8d51-130">Daha fazla bilgi için [ASP.NET Core projelerinde İskele Kimliği'ne](xref:security/authentication/scaffold-identity)bakın.</span><span class="sxs-lookup"><span data-stu-id="c8d51-130">For more information, see [Scaffold Identity in ASP.NET Core projects](xref:security/authentication/scaffold-identity).</span></span>

## <a name="https"></a><span data-ttu-id="c8d51-131">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c8d51-131">HTTPS</span></span>

<span data-ttu-id="c8d51-132">Güvenlik ve gizlilik üzerindeki artan odak la birlikte, web uygulamaları için HTTPS'yi etkinleştirmek önemlidir.</span><span class="sxs-lookup"><span data-stu-id="c8d51-132">With the increased focus on security and privacy, enabling HTTPS for web apps is important.</span></span> <span data-ttu-id="c8d51-133">HTTPS uygulama web üzerinde giderek daha sıkı hale gelmektedir.</span><span class="sxs-lookup"><span data-stu-id="c8d51-133">HTTPS enforcement is becoming increasingly strict on the web.</span></span> <span data-ttu-id="c8d51-134">HTTPS kullanmayan siteler güvensiz olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="c8d51-134">Sites that don't use HTTPS are considered insecure.</span></span> <span data-ttu-id="c8d51-135">Tarayıcılar (Krom, Mozilla) web özelliklerinin güvenli bir bağlamda kullanılması gerektiğini zorlamaya başlıyor.</span><span class="sxs-lookup"><span data-stu-id="c8d51-135">Browsers (Chromium, Mozilla) are starting to enforce that web features must be used from a secure context.</span></span> <span data-ttu-id="c8d51-136">[GDPR,](xref:security/gdpr) kullanıcı gizliliğini korumak için HTTPS kullanımını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="c8d51-136">[GDPR](xref:security/gdpr) requires the use of HTTPS to protect user privacy.</span></span> <span data-ttu-id="c8d51-137">Üretimde HTTPS kullanmak kritik olmakla birlikte, https'yi geliştirme de dağıtımdaki sorunları (örneğin, güvensiz bağlantılar) önlemeye yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="c8d51-137">While using HTTPS in production is critical, using HTTPS in development can help prevent issues in deployment (for example, insecure links).</span></span> <span data-ttu-id="c8d51-138">ASP.NET Core 2.1, HTTPS'nin geliştirme aşamasında kullanılmasını ve üretimde HTTPS'yi yapılandırmayı kolaylaştıran bir dizi iyileştirme içerir.</span><span class="sxs-lookup"><span data-stu-id="c8d51-138">ASP.NET Core 2.1 includes a number of improvements that make it easier to use HTTPS in development and to configure HTTPS in production.</span></span> <span data-ttu-id="c8d51-139">Daha fazla bilgi için [bkz.](xref:security/enforcing-ssl)</span><span class="sxs-lookup"><span data-stu-id="c8d51-139">For more information, see [Enforce HTTPS](xref:security/enforcing-ssl).</span></span>

### <a name="on-by-default"></a><span data-ttu-id="c8d51-140">Varsayılan olarak</span><span class="sxs-lookup"><span data-stu-id="c8d51-140">On by default</span></span>

<span data-ttu-id="c8d51-141">Güvenli web sitesi geliştirmeyi kolaylaştırmak için, HTTPS artık varsayılan olarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="c8d51-141">To facilitate secure website development, HTTPS  is now enabled by default.</span></span> <span data-ttu-id="c8d51-142">2.1'den başlayarak, Kestrel yerel bir geliştirme sertifikasının `https://localhost:5001` ne zaman mevcut olduğunu dinler.</span><span class="sxs-lookup"><span data-stu-id="c8d51-142">Starting in 2.1, Kestrel listens on `https://localhost:5001` when a local development certificate is present.</span></span> <span data-ttu-id="c8d51-143">Geliştirme sertifikası oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="c8d51-143">A development certificate is created:</span></span>

* <span data-ttu-id="c8d51-144">.NET Core SDK ilk çalıştırma deneyiminin bir parçası olarak, SDK'yı ilk kez kullandığınızda.</span><span class="sxs-lookup"><span data-stu-id="c8d51-144">As part of the .NET Core SDK first-run experience, when you use the SDK for the first time.</span></span>
* <span data-ttu-id="c8d51-145">Yeni `dev-certs` aracı el ile kullanma.</span><span class="sxs-lookup"><span data-stu-id="c8d51-145">Manually using the new `dev-certs` tool.</span></span>

<span data-ttu-id="c8d51-146">Sertifikaya güvenmek için çalıştırın. `dotnet dev-certs https --trust`</span><span class="sxs-lookup"><span data-stu-id="c8d51-146">Run `dotnet dev-certs https --trust` to trust the certificate.</span></span>

### <a name="https-redirection-and-enforcement"></a><span data-ttu-id="c8d51-147">HTTPS yeniden yönlendirme ve uygulama</span><span class="sxs-lookup"><span data-stu-id="c8d51-147">HTTPS redirection and enforcement</span></span>

<span data-ttu-id="c8d51-148">Web uygulamalarının genellikle hem HTTP hem de HTTPS'yi dinlemesi gerekir, ancak daha sonra tüm HTTP trafiğini HTTPS'ye yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="c8d51-148">Web apps typically need to listen on both HTTP and HTTPS, but then redirect all HTTP traffic to HTTPS.</span></span> <span data-ttu-id="c8d51-149">2.1'de, yapılandırma veya bağlı sunucu bağlantı noktalarının varlığına göre akıllıca yeniden yönlendiren özel LEŞTIRILMIŞ HTTPS yeniden yönlendirme ara ware'i kullanıma sunulmuştur.</span><span class="sxs-lookup"><span data-stu-id="c8d51-149">In 2.1, specialized HTTPS redirection middleware that intelligently redirects based on the presence of configuration or bound server ports has been introduced.</span></span>

<span data-ttu-id="c8d51-150">HTTPS kullanımı HTTP Sıkı [Taşıma Güvenlik Protokolü (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)kullanılarak daha da zorlanabilir.</span><span class="sxs-lookup"><span data-stu-id="c8d51-150">Use of HTTPS can be further enforced using [HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="c8d51-151">HSTS, tarayıcılara siteye her zaman HTTPS üzerinden erişmelerini bildirir.</span><span class="sxs-lookup"><span data-stu-id="c8d51-151">HSTS instructs browsers to always access the site via HTTPS.</span></span> <span data-ttu-id="c8d51-152">ASP.NET Core 2.1, maksimum yaş, alt etki alanları ve HSTS ön yükleme listesi seçeneklerini destekleyen HSTS ara yazılımlarını ekler.</span><span class="sxs-lookup"><span data-stu-id="c8d51-152">ASP.NET Core 2.1 adds HSTS middleware that supports options for max age, subdomains, and the HSTS preload list.</span></span>

### <a name="configuration-for-production"></a><span data-ttu-id="c8d51-153">Üretim için yapılandırma</span><span class="sxs-lookup"><span data-stu-id="c8d51-153">Configuration for production</span></span>

<span data-ttu-id="c8d51-154">Üretimde, HTTPS açıkça yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c8d51-154">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="c8d51-155">2.1'de, Kestrel için HTTPS yapılandırmak için varsayılan yapılandırma şeması eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="c8d51-155">In 2.1, default configuration schema for configuring HTTPS for Kestrel has been added.</span></span> <span data-ttu-id="c8d51-156">Uygulamalar şu şekilde yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="c8d51-156">Apps can be configured to use:</span></span>

* <span data-ttu-id="c8d51-157">URL'ler de dahil olmak üzere birden çok uç nokta.</span><span class="sxs-lookup"><span data-stu-id="c8d51-157">Multiple endpoints including the URLs.</span></span> <span data-ttu-id="c8d51-158">Daha fazla bilgi için [Kestrel web sunucusu uygulamasına bakın: Bitiş Noktası yapılandırması.](xref:fundamentals/servers/kestrel#endpoint-configuration)</span><span class="sxs-lookup"><span data-stu-id="c8d51-158">For more information, see [Kestrel web server implementation: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>
* <span data-ttu-id="c8d51-159">HTTPS için kullanılacak sertifika diskteki bir dosyadan veya bir sertifika deposundan.</span><span class="sxs-lookup"><span data-stu-id="c8d51-159">The certificate to use for HTTPS either from a file on disk or from a certificate store.</span></span>

## <a name="gdpr"></a><span data-ttu-id="c8d51-160">GDPR</span><span class="sxs-lookup"><span data-stu-id="c8d51-160">GDPR</span></span>

<span data-ttu-id="c8d51-161">ASP.NET Core, [AB Genel Veri Koruma Yönetmeliği (GDPR)](https://www.eugdpr.org/) gerekliliklerinin bazılarını karşılamaya yardımcı olmak için API'ler ve şablonlar sağlar.</span><span class="sxs-lookup"><span data-stu-id="c8d51-161">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements.</span></span> <span data-ttu-id="c8d51-162">Daha fazla bilgi için [ASP.NET Core'daki GDPR desteğine](xref:security/gdpr)bakın.</span><span class="sxs-lookup"><span data-stu-id="c8d51-162">For more information, see [GDPR support in ASP.NET Core](xref:security/gdpr).</span></span> <span data-ttu-id="c8d51-163">Örnek bir [uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) nasıl kullanılacağını gösterir ve ASP.NET Core 2.1 şablonlarına eklenen GDPR uzantı noktalarının ve API'lerin çoğunu test etmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="c8d51-163">A [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) shows how to use and lets you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span>

## <a name="integration-tests"></a><span data-ttu-id="c8d51-164">Tümleştirme testleri</span><span class="sxs-lookup"><span data-stu-id="c8d51-164">Integration tests</span></span>

<span data-ttu-id="c8d51-165">Test oluşturma ve yürütmeyi kolaylaştıran yeni bir paket tanıtıldı.</span><span class="sxs-lookup"><span data-stu-id="c8d51-165">A new package is introduced that streamlines test creation and execution.</span></span> <span data-ttu-id="c8d51-166">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) paketi aşağıdaki görevleri işler:</span><span class="sxs-lookup"><span data-stu-id="c8d51-166">The [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) package handles the following tasks:</span></span>

* <span data-ttu-id="c8d51-167">Bağımlılık dosyasını (*\*.deps)* test uygulamasından test projesinin *çöp kutusu* klasörüne kopyalar.</span><span class="sxs-lookup"><span data-stu-id="c8d51-167">Copies the dependency file (*\*.deps*) from the tested app into the test project's *bin* folder.</span></span>
* <span data-ttu-id="c8d51-168">Testler yürütüldüğünde statik dosyaların ve sayfaların/görünümlerin bulunması için içerik kökünü test edilen uygulamanın proje köküne ayarlar.</span><span class="sxs-lookup"><span data-stu-id="c8d51-168">Sets the content root to the tested app's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="c8d51-169">[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)ile test edilen uygulamayı yeniden düzene sokmak için [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) sınıfını sağlar.</span><span class="sxs-lookup"><span data-stu-id="c8d51-169">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the tested app with [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span>

<span data-ttu-id="c8d51-170">Aşağıdaki test, Dizin sayfasının başarı durum kodu ve doğru İçerik Türü üstbilgisi ile yükleyip yüklemediğini kontrol etmek için [xUnit'i](https://xunit.github.io/) kullanır:</span><span class="sxs-lookup"><span data-stu-id="c8d51-170">The following test uses [xUnit](https://xunit.github.io/) to check that the Index page loads with a success status code and with the correct Content-Type header:</span></span>

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

<span data-ttu-id="c8d51-171">Daha fazla bilgi için [Tümleştirme testleri](xref:test/integration-tests) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="c8d51-171">For more information, see the [Integration tests](xref:test/integration-tests) topic.</span></span>

## <a name="apicontroller-actionresultt"></a><span data-ttu-id="c8d51-172">[ApiController], ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="c8d51-172">[ApiController], ActionResult\<T></span></span>

<span data-ttu-id="c8d51-173">ASP.NET Core 2.1, temiz ve açıklayıcı web API'leri oluşturmayı kolaylaştıran yeni programlama kuralları ekler.</span><span class="sxs-lookup"><span data-stu-id="c8d51-173">ASP.NET Core 2.1 adds new programming conventions that make it easier to build clean and descriptive web APIs.</span></span> <span data-ttu-id="c8d51-174">`ActionResult<T>`bir uygulamanın yanıt türünü veya başka bir eylem sonucunu (IActionResult'a benzer) döndürmesine izin vermek için eklenen yeni bir türdür.</span><span class="sxs-lookup"><span data-stu-id="c8d51-174">`ActionResult<T>` is a new type added to allow an app to return either a response type or any other action result (similar to IActionResult), while still indicating the response type.</span></span> <span data-ttu-id="c8d51-175">Öznitelik, `[ApiController]` Web API'ye özgü kuralları ve davranışları seçmenin yolu olarak da eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="c8d51-175">The `[ApiController]` attribute has also been added as the way to opt in to Web API-specific conventions and behaviors.</span></span>

<span data-ttu-id="c8d51-176">Daha fazla bilgi için ASP.NET [Core ile Web API'leri Oluştur'a](xref:web-api/index)bakın.</span><span class="sxs-lookup"><span data-stu-id="c8d51-176">For more information, see [Build Web APIs with ASP.NET Core](xref:web-api/index).</span></span>

## <a name="ihttpclientfactory"></a><span data-ttu-id="c8d51-177">ihttpclientfactory</span><span class="sxs-lookup"><span data-stu-id="c8d51-177">IHttpClientFactory</span></span>

<span data-ttu-id="c8d51-178">ASP.NET Core 2.1, `IHttpClientFactory` `HttpClient` uygulamalardaki örnekleri yapılandırmayı ve tüketmeyi kolaylaştıran yeni bir hizmet içerir.</span><span class="sxs-lookup"><span data-stu-id="c8d51-178">ASP.NET Core 2.1 includes a new `IHttpClientFactory` service that makes it easier to configure and consume instances of `HttpClient` in apps.</span></span> <span data-ttu-id="c8d51-179">`HttpClient`zaten giden HTTP istekleri için birbirine bağlanabilir işleyicileri delegating kavramı vardır.</span><span class="sxs-lookup"><span data-stu-id="c8d51-179">`HttpClient` already has the concept of delegating handlers that could be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="c8d51-180">Fabrika:</span><span class="sxs-lookup"><span data-stu-id="c8d51-180">The factory:</span></span>

* <span data-ttu-id="c8d51-181">Adlandırılmış istemci `HttpClient` başına örneklerinin kaydedilmesi daha sezgisel hale getirir.</span><span class="sxs-lookup"><span data-stu-id="c8d51-181">Makes registering of instances of `HttpClient` per named client more intuitive.</span></span>
* <span data-ttu-id="c8d51-182">Polly politikalarının Retry, CircuitBreakers, vb. için kullanılmasını sağlayan bir Polly işleyicisi uygular.</span><span class="sxs-lookup"><span data-stu-id="c8d51-182">Implements a Polly handler that allows Polly policies to be used for Retry, CircuitBreakers, etc.</span></span>

<span data-ttu-id="c8d51-183">Daha fazla bilgi için http [isteklerini başlat'a](xref:fundamentals/http-requests)bakın.</span><span class="sxs-lookup"><span data-stu-id="c8d51-183">For more information, see [Initiate HTTP Requests](xref:fundamentals/http-requests).</span></span>

## <a name="kestrel-transport-configuration"></a><span data-ttu-id="c8d51-184">Kerkenez taşıma yapılandırması</span><span class="sxs-lookup"><span data-stu-id="c8d51-184">Kestrel transport configuration</span></span>

<span data-ttu-id="c8d51-185">Core 2.1ASP.NET piyasaya sürülmesiyle, Kestrel'in varsayılan taşıması artık Libuv'a değil, yönetilen soketlere dayanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="c8d51-185">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="c8d51-186">Daha fazla bilgi için [Kestrel web sunucusu uygulamasına bakın: Aktarım yapılandırması.](xref:fundamentals/servers/kestrel#transport-configuration)</span><span class="sxs-lookup"><span data-stu-id="c8d51-186">For more information, see [Kestrel web server implementation: Transport configuration](xref:fundamentals/servers/kestrel#transport-configuration).</span></span>

## <a name="generic-host-builder"></a><span data-ttu-id="c8d51-187">Genel ana bilgisayar oluşturucu</span><span class="sxs-lookup"><span data-stu-id="c8d51-187">Generic host builder</span></span>

<span data-ttu-id="c8d51-188">Genel Ana Bilgisayar`HostBuilder`Oluşturucu ( ) tanıtıldı.</span><span class="sxs-lookup"><span data-stu-id="c8d51-188">The Generic Host Builder (`HostBuilder`) has been introduced.</span></span> <span data-ttu-id="c8d51-189">Bu oluşturucu, HTTP isteklerini işlemeyan uygulamalar (Mesajlaşma, arka plan görevleri, vb.) için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="c8d51-189">This builder can be used for apps that don't process HTTP requests (Messaging, background tasks, etc.).</span></span>

<span data-ttu-id="c8d51-190">Daha fazla bilgi için [bkz.](xref:fundamentals/host/generic-host)</span><span class="sxs-lookup"><span data-stu-id="c8d51-190">For more information, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

## <a name="updated-spa-templates"></a><span data-ttu-id="c8d51-191">Güncelleştirilmiş SPA şablonları</span><span class="sxs-lookup"><span data-stu-id="c8d51-191">Updated SPA templates</span></span>

<span data-ttu-id="c8d51-192">Açısal, Tepki ve Redux ile Tepki için Tek Sayfauygulaması şablonları, her bir çerçeve için standart proje yapılarını ve yapı sistemlerini kullanmak üzere güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="c8d51-192">The Single Page Application templates for Angular, React, and React with Redux are updated to use the standard project structures and build systems for each framework.</span></span>

<span data-ttu-id="c8d51-193">Açısal şablon Açısal CLI'yi, Tepki şablonları ise create-react-app'ı temel adatır.</span><span class="sxs-lookup"><span data-stu-id="c8d51-193">The Angular template is based on the Angular CLI, and the React templates are based on create-react-app.</span></span>

<span data-ttu-id="c8d51-194">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="c8d51-194">For more information, see:</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="razor-pages-search-for-razor-assets"></a><span data-ttu-id="c8d51-195">Razor Pages, Razor varlıkları arama</span><span class="sxs-lookup"><span data-stu-id="c8d51-195">Razor Pages search for Razor assets</span></span>

<span data-ttu-id="c8d51-196">2.1'de, Razor Pages listelenen sırada aşağıdaki dizinlerde Razor varlıklarını (düzenler ve kısmiler gibi) arar:</span><span class="sxs-lookup"><span data-stu-id="c8d51-196">In 2.1, Razor Pages search for Razor assets (such as layouts and partials) in the following directories in the listed order:</span></span>

1. <span data-ttu-id="c8d51-197">Geçerli Sayfalar klasörü.</span><span class="sxs-lookup"><span data-stu-id="c8d51-197">Current Pages folder.</span></span>
1. <span data-ttu-id="c8d51-198">*/Sayfalar/Paylaşılan/*</span><span class="sxs-lookup"><span data-stu-id="c8d51-198">*/Pages/Shared/*</span></span>
1. <span data-ttu-id="c8d51-199">*/Görünümler/Paylaşılanlar/*</span><span class="sxs-lookup"><span data-stu-id="c8d51-199">*/Views/Shared/*</span></span>

## <a name="razor-pages-in-an-area"></a><span data-ttu-id="c8d51-200">Bir alandaki Jilet Sayfaları</span><span class="sxs-lookup"><span data-stu-id="c8d51-200">Razor Pages in an area</span></span>

<span data-ttu-id="c8d51-201">Razor Pages artık [alanları](xref:mvc/controllers/areas)destekliyor.</span><span class="sxs-lookup"><span data-stu-id="c8d51-201">Razor Pages now support [areas](xref:mvc/controllers/areas).</span></span> <span data-ttu-id="c8d51-202">Alanların bir örneğini görmek için, tek tek kullanıcı hesaplarıiçeren yeni bir Razor Pages web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c8d51-202">To see an example of areas, create a new Razor Pages web app with individual user accounts.</span></span> <span data-ttu-id="c8d51-203">Bireysel kullanıcı hesapları ile bir Razor Pages web uygulaması */ Alanlar / Kimlik / Sayfalar*içerir.</span><span class="sxs-lookup"><span data-stu-id="c8d51-203">A Razor Pages web app with individual user accounts includes */Areas/Identity/Pages*.</span></span>

## <a name="mvc-compatibility-version"></a><span data-ttu-id="c8d51-204">MVC uyumluluk sürümü</span><span class="sxs-lookup"><span data-stu-id="c8d51-204">MVC compatibility version</span></span>

<span data-ttu-id="c8d51-205">Yöntem, <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> bir uygulamanın Core MVC 2.1 veya sonraki ASP.NET'da tanıtılan olası ihlal davranış değişikliklerini kabul etmesine veya devre dışı bırakmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="c8d51-205">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="c8d51-206">Daha fazla bilgi için bkz. <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="c8d51-206">For more information, see <xref:mvc/compatibility-version>.</span></span>

## <a name="migrate-from-20-to-21"></a><span data-ttu-id="c8d51-207">2,0'dan 2,1'e geçiş</span><span class="sxs-lookup"><span data-stu-id="c8d51-207">Migrate from 2.0 to 2.1</span></span>

<span data-ttu-id="c8d51-208">Bkz. [ASP.NET Çekirdek 2.0'dan 2.1'e geçir.](xref:migration/20_21)</span><span class="sxs-lookup"><span data-stu-id="c8d51-208">See [Migrate from ASP.NET Core 2.0 to 2.1](xref:migration/20_21).</span></span>

## <a name="additional-information"></a><span data-ttu-id="c8d51-209">Ek bilgiler</span><span class="sxs-lookup"><span data-stu-id="c8d51-209">Additional information</span></span>

<span data-ttu-id="c8d51-210">Değişikliklerin tam listesi [için, ASP.NET Core 2.1 Sürüm Notları](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="c8d51-210">For the complete list of changes, see the [ASP.NET Core 2.1 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0).</span></span>
