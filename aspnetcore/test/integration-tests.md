---
<span data-ttu-id="2a1e9-101">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-102">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-103">'Identity'</span></span>
- <span data-ttu-id="2a1e9-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-105">'Razor'</span></span>
- <span data-ttu-id="2a1e9-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-106">'SignalR' uid:</span></span> 

---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="2a1e9-107">ASP.NET Core tümleştirme testleri</span><span class="sxs-lookup"><span data-stu-id="2a1e9-107">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="2a1e9-108">, [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/)ve [Jos van der til](https://jvandertil.nl)</span><span class="sxs-lookup"><span data-stu-id="2a1e9-108">By [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/), and [Jos van der Til](https://jvandertil.nl)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2a1e9-109">Tümleştirme sınamaları, uygulamanın bileşenlerinin veritabanı, dosya sistemi ve ağ gibi destekleyici altyapısını içeren bir düzeyde doğru şekilde çalışmasını güvence altına alır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-109">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="2a1e9-110">ASP.NET Core, test Web ana bilgisayarı ve bellek içi test sunucusu olan bir birim testi çerçevesini kullanarak tümleştirme testlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-110">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="2a1e9-111">Bu konuda, birim testlerinin temel bir şekilde anlaşıldığı varsayılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-111">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="2a1e9-112">Test kavramları hakkında bilgi sahibi değilseniz, [.NET Core 'Da birim testine ve .NET Standard](/dotnet/core/testing/) konusuna ve bağlı içeriğine bakın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-112">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="2a1e9-113">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2a1e9-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2a1e9-114">Örnek uygulama bir Razor Sayfalar uygulamasıdır ve sayfaların temel olarak anlaşıldığını varsayar Razor .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-114">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="2a1e9-115">Sayfalarla alışkın değilseniz Razor , aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-115">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="2a1e9-116">[Sayfalara giriş Razor](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="2a1e9-116">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="2a1e9-117">[Sayfalarla çalışmaya başlama Razor](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="2a1e9-117">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="2a1e9-118">[RazorSayfalar birim testleri](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="2a1e9-118">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="2a1e9-119">Maça 'Ları test etmek için, bir tarayıcıyı otomatikleştirebilen [Selenium](https://www.seleniumhq.org/)gibi bir araç öneririz.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-119">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="2a1e9-120">Tümleştirme testlerine giriş</span><span class="sxs-lookup"><span data-stu-id="2a1e9-120">Introduction to integration tests</span></span>

<span data-ttu-id="2a1e9-121">Tümleştirme testleri, bir uygulamanın bileşenlerini [birim testlerinden](/dotnet/core/testing/)daha geniş bir düzeyde değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-121">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="2a1e9-122">Birim testleri, ayrı sınıf yöntemleri gibi yalıtılmış yazılım bileşenlerini test etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-122">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="2a1e9-123">Tümleştirme testleri iki veya daha fazla uygulama bileşeninin beklenen bir sonuç üretmek için birlikte çalıştığını ve muhtemelen bir isteği tam olarak işlemek için gereken her bileşeni de dahil olduğunu onaylar.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-123">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="2a1e9-124">Bu geniş testler, uygulamanın altyapısını ve tüm çatısını test etmek için kullanılır, genellikle aşağıdaki bileşenler dahil:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-124">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="2a1e9-125">Veritabanı</span><span class="sxs-lookup"><span data-stu-id="2a1e9-125">Database</span></span>
* <span data-ttu-id="2a1e9-126">Dosya sistemi</span><span class="sxs-lookup"><span data-stu-id="2a1e9-126">File system</span></span>
* <span data-ttu-id="2a1e9-127">Ağ gereçleri</span><span class="sxs-lookup"><span data-stu-id="2a1e9-127">Network appliances</span></span>
* <span data-ttu-id="2a1e9-128">İstek-yanıt işlem hattı</span><span class="sxs-lookup"><span data-stu-id="2a1e9-128">Request-response pipeline</span></span>

<span data-ttu-id="2a1e9-129">Birim testleri, altyapı bileşenlerinin yerine, *Fakes* veya *sahte nesneler*olarak bilinen fabriccomponents bileşenlerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-129">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="2a1e9-130">Birim testlerinin aksine, tümleştirme testleri:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-130">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="2a1e9-131">Uygulamanın üretimde kullandığı gerçek bileşenleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-131">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="2a1e9-132">Daha fazla kod ve veri işleme gerektir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-132">Require more code and data processing.</span></span>
* <span data-ttu-id="2a1e9-133">Çalıştırmak daha uzun sürer.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-133">Take longer to run.</span></span>

<span data-ttu-id="2a1e9-134">Bu nedenle, tümleştirme testlerinin kullanımını en önemli altyapı senaryolarıyla sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-134">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="2a1e9-135">Bir davranış, birim testi veya tümleştirme testi kullanılarak test edilebilir ise, birim testini seçin.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-135">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="2a1e9-136">Veritabanları ve dosya sistemleriyle ilgili her olası veri ve dosya erişimi için tümleştirme testleri yazma.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-136">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="2a1e9-137">Bir uygulamadaki kaç yerde veritabanlarıyla ve dosya sistemleriyle etkileşime girdiğinize bakılmaksızın, bir dizi Read, Write, Update ve DELETE tümleştirme testi, genellikle veritabanı ve dosya sistemi bileşenlerinin yeterli şekilde test edilmesine sahip olur.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-137">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="2a1e9-138">Bu bileşenlerle etkileşime geçen Yöntem mantığının rutin testleri için birim testleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-138">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="2a1e9-139">Birim testlerinde, altyapı kullanımı ve moizler 'in kullanılması daha hızlı test yürütmesiyle sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-139">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="2a1e9-140">Tümleştirme testlerinin tartışmalarında, test edilen proje genellikle *test altındaki sistem*veya kısa IÇIN "sut" olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-140">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="2a1e9-141">*Bu konu başlığı altında, sınanan ASP.NET Core uygulamasına başvurmak için "SUT" kullanılır.*</span><span class="sxs-lookup"><span data-stu-id="2a1e9-141">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="2a1e9-142">ASP.NET Core tümleştirme testleri</span><span class="sxs-lookup"><span data-stu-id="2a1e9-142">ASP.NET Core integration tests</span></span>

<span data-ttu-id="2a1e9-143">ASP.NET Core tümleştirme testleri şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-143">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="2a1e9-144">Testleri içermesi ve yürütmek için bir test projesi kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-144">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="2a1e9-145">Test projesinin SUT 'a bir başvurusu vardır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-145">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="2a1e9-146">Test projesi SUT için bir test Web Konağı oluşturur ve SUT ile istekleri ve yanıtları işlemek için bir test sunucusu istemcisi kullanır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-146">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="2a1e9-147">Test Çalıştırıcısı, testleri yürütmek ve test sonuçlarını raporlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-147">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="2a1e9-148">Tümleştirme testleri, olağan *düzenleme*, *hareket*ve *onaylama* testi adımlarını içeren bir olay dizisini izler:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-148">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="2a1e9-149">SUT 'un web ana bilgisayarı yapılandırıldı.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-149">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="2a1e9-150">İstekleri uygulamaya göndermek için bir test sunucusu istemcisi oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-150">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="2a1e9-151">Testi *Düzenle* adımı yürütülür: test uygulaması bir istek hazırlar.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-151">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="2a1e9-152">*Davran* test adımı yürütülür: istemci, isteği gönderir ve yanıtını alır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-152">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="2a1e9-153">*Onaylama* testi adımı yürütülür: *Gerçek* yanıt, *beklenen* bir yanıta bağlı olarak *başarılı* veya *başarısız* olarak onaylanır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-153">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="2a1e9-154">İşlem, tüm testler yürütülene kadar devam eder.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-154">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="2a1e9-155">Test sonuçları raporlanır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-155">The test results are reported.</span></span>

<span data-ttu-id="2a1e9-156">Genellikle, test Web ana bilgisayarı, test çalıştırmaları için uygulamanın normal web ana bilgisayarında farklı şekilde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-156">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="2a1e9-157">Örneğin, testler için farklı bir veritabanı veya farklı uygulama ayarları kullanılıyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-157">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="2a1e9-158">Test Web Konağı ve bellek içi test sunucusu ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) gibi altyapı bileşenleri, [Microsoft. Aspnetcore. Mvc. Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) paketi tarafından sağlanır veya yönetilir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-158">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="2a1e9-159">Bu paketin kullanımı, test oluşturma ve yürütmeyi kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-159">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="2a1e9-160">`Microsoft.AspNetCore.Mvc.Testing`Paket aşağıdaki görevleri gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-160">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="2a1e9-161">Bağımlılıklar dosyasını (*. Deps*) sut 'den test projesinin *bin* dizinine kopyalar.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-161">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="2a1e9-162">, Testler yürütüldüğünde statik dosya ve sayfa/görünümlerin bulunması için [içerik kökünü](xref:fundamentals/index#content-root) sut 'nin proje köküne ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-162">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="2a1e9-163">, İle SUT önyükleyiciyi kolaylaştırmak için [Webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) sınıfını sağlar `TestServer` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-163">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="2a1e9-164">[Birim](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) testi belgeleri bir test projesi ve Test Çalıştırıcısı ayarlamayı ve testlerin ve test sınıflarının nasıl belirleneceğini gösteren testlerin ve önerilerin nasıl çalıştırılacağını gösteren ayrıntılı yönergelerle birlikte açıklanır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-164">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="2a1e9-165">Bir uygulama için test projesi oluştururken, birim testlerini tümleştirme testlerinden farklı projelere ayırın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-165">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="2a1e9-166">Bu, altyapı testi bileşenlerinin birim testlerine yanlışlıkla dahil olmamasını sağlamaya yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-166">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="2a1e9-167">Birim ve tümleştirme testlerinin ayrımı, hangi test kümesinin çalıştırılmasına da izin verir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-167">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="2a1e9-168">Uygulamaların ve MVC uygulamalarının testleri için yapılandırma arasında neredeyse fark yoktur Razor .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-168">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="2a1e9-169">Tek fark testlerin adlandırılmasınlardır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-169">The only difference is in how the tests are named.</span></span> <span data-ttu-id="2a1e9-170">Bir Razor Sayfalar uygulamasında sayfa uç noktalarının testleri genellikle sayfa modeli sınıfından sonra (örneğin, `IndexPageTests` Dizin sayfasına yönelik bileşen tümleştirmesini test etmek için) adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-170">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="2a1e9-171">MVC uygulamasında, testler genellikle denetleyici sınıfları tarafından düzenlenir ve test ettikleri denetleyicilerde (örneğin, `HomeControllerTests` ana denetleyiciye yönelik bileşen tümleştirmesini test etmek için) adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-171">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="2a1e9-172">Test uygulaması önkoşulları</span><span class="sxs-lookup"><span data-stu-id="2a1e9-172">Test app prerequisites</span></span>

<span data-ttu-id="2a1e9-173">Test projesi şunları vermelidir:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-173">The test project must:</span></span>

* <span data-ttu-id="2a1e9-174">[Microsoft. AspNetCore. Mvc. Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) paketine başvurun.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-174">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="2a1e9-175">() Proje dosyasında Web SDK 'sını belirtin `<Project Sdk="Microsoft.NET.Sdk.Web">` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-175">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="2a1e9-176">Bu Önkoşullar [örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)görülebilir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-176">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="2a1e9-177">*Testler/RazorPagesProject. Tests/RazorPagesProject. Tests. csproj* dosyasını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-177">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="2a1e9-178">Örnek uygulama, [xUnit](https://xunit.github.io/) test çerçevesini ve [anglesharp](https://anglesharp.github.io/) Parser kitaplığını kullanır, bu nedenle örnek uygulama de şu şekilde başvurur:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-178">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="2a1e9-179">xUnit</span><span class="sxs-lookup"><span data-stu-id="2a1e9-179">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="2a1e9-180">xUnit. Çalıştırıcısı. VisualStudio</span><span class="sxs-lookup"><span data-stu-id="2a1e9-180">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="2a1e9-181">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="2a1e9-181">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="2a1e9-182">Entity Framework Core, testlerde de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-182">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="2a1e9-183">Uygulama başvuru:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-183">The app references:</span></span>

* [<span data-ttu-id="2a1e9-184">Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="2a1e9-184">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* <span data-ttu-id="2a1e9-185">[Microsoft. AspNetCore. Identity . EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="2a1e9-185">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span></span>
* [<span data-ttu-id="2a1e9-186">Microsoft. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="2a1e9-186">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="2a1e9-187">Microsoft. EntityFrameworkCore. InMemory</span><span class="sxs-lookup"><span data-stu-id="2a1e9-187">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="2a1e9-188">Microsoft. EntityFrameworkCore. Tools</span><span class="sxs-lookup"><span data-stu-id="2a1e9-188">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="2a1e9-189">SUT ortamı</span><span class="sxs-lookup"><span data-stu-id="2a1e9-189">SUT environment</span></span>

<span data-ttu-id="2a1e9-190">SUT [ortamı](xref:fundamentals/environments) ayarlanmamışsa, ortam varsayılan olarak geliştirme olur.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-190">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="2a1e9-191">Varsayılan WebApplicationFactory ile temel testler</span><span class="sxs-lookup"><span data-stu-id="2a1e9-191">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="2a1e9-192">[Webapplicationfactory \< TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) , tümleştirme testleri için bir [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) oluşturmak üzere kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-192">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="2a1e9-193">`TEntryPoint`, genellikle sınıfının, SUT 'ın giriş noktası sınıfıdır `Startup` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-193">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="2a1e9-194">Test sınıfları sınıfı testlerin içerdiğini göstermek ve sınıftaki testler arasında paylaşılan nesne örnekleri sağlamak için bir *sınıf armatürü* arabirimi ([ıssfixture](https://xunit.github.io/docs/shared-context#class-fixture)) uygular.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-194">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="2a1e9-195">Aşağıdaki test sınıfı, `BasicTests` SUT 'yi önyüklemek `WebApplicationFactory` ve bir test yöntemine [HttpClient](/dotnet/api/system.net.http.httpclient) sağlamak için öğesini kullanır `Get_EndpointsReturnSuccessAndCorrectContentType` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-195">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="2a1e9-196">Yöntemi, yanıt durum kodunun başarılı olup olmadığını denetler (200-299 aralığındaki durum kodları) ve `Content-Type` üst bilgi `text/html; charset=utf-8` birçok uygulama sayfasına yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-196">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="2a1e9-197">[Createclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) , `HttpClient` yeniden yönlendirmeleri otomatik olarak izleyen ve tanımlama bilgilerini işleyen bir örneği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-197">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="2a1e9-198">Varsayılan olarak, [GDPR onay ilkesi](xref:security/gdpr) etkinleştirildiğinde, önemli olmayan tanımlama bilgileri istekler arasında korunmaz.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-198">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="2a1e9-199">TempData sağlayıcısı tarafından kullanılanlar gibi, önemli olmayan tanımlama bilgilerini korumak için, bunları testlerinizde gerekli olarak işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-199">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="2a1e9-200">Tanımlama bilgisini önemli olarak işaretleme hakkında yönergeler için bkz. [temel tanımlama bilgileri](xref:security/gdpr#essential-cookies).</span><span class="sxs-lookup"><span data-stu-id="2a1e9-200">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="2a1e9-201">WebApplicationFactory 'yi özelleştirme</span><span class="sxs-lookup"><span data-stu-id="2a1e9-201">Customize WebApplicationFactory</span></span>

<span data-ttu-id="2a1e9-202">Web ana bilgisayar yapılandırması, öğesinden devralarak `WebApplicationFactory` bir veya daha fazla özel fabrika oluşturmak için test sınıflarından bağımsız olarak oluşturulabilir:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-202">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="2a1e9-203">`WebApplicationFactory` [Configurewebhost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)'ten devralma ve geçersiz kılma.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-203">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="2a1e9-204">[Iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) , hizmet koleksiyonunun [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)ile yapılandırılmasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-204">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="2a1e9-205">[Örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) veritabanı dengeli dağıtımı, yöntemi tarafından gerçekleştirilir `InitializeDbForTests` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-205">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="2a1e9-206">Yöntemi, [tümleştirme testleri örneği: test uygulaması kuruluşu](#test-app-organization) bölümünde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-206">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="2a1e9-207">SUT 'un veritabanı bağlamı `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-207">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2a1e9-208">Test uygulamasının `builder.ConfigureServices` geri çağırması, uygulamanın kodu yürütüldükten *sonra* yürütülür `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-208">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="2a1e9-209">Yürütme sırası, [genel ana bilgisayar](xref:fundamentals/host/generic-host) için ASP.NET Core 3,0 sürümüne sahip bir son değişiklikten oluşur.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-209">The execution order is a breaking change for the [Generic Host](xref:fundamentals/host/generic-host) with the release of ASP.NET Core 3.0.</span></span> <span data-ttu-id="2a1e9-210">Uygulamanın veritabanından farklı testler için farklı bir veritabanı kullanmak istiyorsanız, uygulamanın veritabanı bağlamı içinde değiştirilmelidir `builder.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-210">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="2a1e9-211">[Web Host} (XREF: temelleri/Host/Web-Host) kullanan sımlar için, test uygulamasının `builder.ConfigureServices` geri çağırması SUT kodundan *önce* yürütülür `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-211">For SUTs that still use the [Web Host}(xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="2a1e9-212">Test uygulamasının `builder.ConfigureTestServices` geri çağırması *sonra*yürütülür.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-212">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

   <span data-ttu-id="2a1e9-213">Örnek uygulama, veritabanı bağlamı için hizmet tanımlayıcısını bulur ve hizmet kaydını kaldırmak için tanımlayıcıyı kullanır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-213">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="2a1e9-214">Ardından, fabrika, `ApplicationDbContext` testler için bellek içi veritabanı kullanan yeni bir ekler.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-214">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="2a1e9-215">Bellek içi veritabanından farklı bir veritabanına bağlanmak için, `UseInMemoryDatabase` bağlamı farklı bir veritabanına bağlama çağrısını değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-215">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="2a1e9-216">SQL Server test veritabanı kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-216">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="2a1e9-217">Proje dosyasındaki [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet paketine başvurun.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-217">Reference the [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="2a1e9-218">`UseSqlServer`Veritabanına bir bağlantı dizesiyle çağrı yapın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-218">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="2a1e9-219">Özel `CustomWebApplicationFactory` Test sınıflarında kullanın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-219">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="2a1e9-220">Aşağıdaki örnek, sınıfında fabrikası kullanır `IndexPageTests` :</span><span class="sxs-lookup"><span data-stu-id="2a1e9-220">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="2a1e9-221">Örnek uygulamanın istemcisi, aşağıdaki yeniden yönlendirmeleri engelleyecek şekilde yapılandırılmıştır `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-221">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="2a1e9-222">Daha sonra, [sahte kimlik doğrulama](#mock-authentication) bölümünde açıklandığı gibi, bu, testlerin uygulamanın ilk yanıtının sonucunu denetlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-222">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="2a1e9-223">İlk yanıt, bu testlerin çoğunda üst bilgiyle bir yeniden yönlendirmelidir `Location` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-223">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="2a1e9-224">Tipik bir test, `HttpClient` isteği ve yanıtı işlemek için ve yardımcı yöntemlerini kullanır:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-224">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="2a1e9-225">SUT 'a yönelik herhangi bir POST isteği, uygulamanın [veri koruma antiforgery sistemi](xref:security/data-protection/introduction)tarafından otomatik olarak oluşturulan antiforgery denetimini karşılamalıdır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-225">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="2a1e9-226">Bir testin POST isteğini düzenlemek için test uygulaması şunları kullanmalıdır:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-226">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="2a1e9-227">Sayfa için bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-227">Make a request for the page.</span></span>
1. <span data-ttu-id="2a1e9-228">Antiforgery tanımlama bilgisini ayrıştırın ve yanıt doğrulama belirtecini istekten isteyin.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-228">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="2a1e9-229">POST isteğini, antiforgery tanımlama bilgisiyle ve istek doğrulama belirteciyle birlikte yapın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-229">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="2a1e9-230">`SendAsync`Örnek uygulamadaki yardımcı uzantı yöntemleri (*yardımcılar/Httpclienconversionsions. cs*) ve `GetDocumentAsync` yardımcı yöntemi (*yardımcılar/htmlyardımcıları. cs*), aşağıdaki [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) yöntemlerle Antiforgery denetimini işlemek için [anglesharp](https://anglesharp.github.io/) ayrıştırıcısını kullanır:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-230">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="2a1e9-231">`GetDocumentAsync`&ndash; [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) 'ı alır ve döndürür `IHtmlDocument` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-231">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="2a1e9-232">`GetDocumentAsync`, orijinalin temelinde bir *sanal yanıt* hazırlayan bir fabrika kullanır `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-232">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="2a1e9-233">Daha fazla bilgi için bkz. [Anglesharp belgeleri](https://github.com/AngleSharp/AngleSharp#documentation).</span><span class="sxs-lookup"><span data-stu-id="2a1e9-233">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="2a1e9-234">`SendAsync``HttpClient`bir [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) oluşturun ve istekleri sut 'A göndermek Için [Sendadsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-234">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="2a1e9-235">`SendAsync`HTML biçimini kabul etmek için aşırı yüklemeler ( `IHtmlFormElement` ) ve şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-235">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="2a1e9-236">Formun () Gönder düğmesi `IHtmlElement`</span><span class="sxs-lookup"><span data-stu-id="2a1e9-236">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="2a1e9-237">Form değerleri koleksiyonu ( `IEnumerable<KeyValuePair<string, string>>` )</span><span class="sxs-lookup"><span data-stu-id="2a1e9-237">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="2a1e9-238">Düğme ( `IHtmlElement` ) ve form değerlerini ( `IEnumerable<KeyValuePair<string, string>>` ) gönder</span><span class="sxs-lookup"><span data-stu-id="2a1e9-238">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="2a1e9-239">[Anglesharp](https://anglesharp.github.io/) , bu konuda ve örnek uygulamada Gösterim amacıyla kullanılan bir üçüncü taraf ayrıştırma kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-239">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="2a1e9-240">ASP.NET Core uygulamalarının tümleştirme testi için AngleSharp desteklenmez veya gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-240">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="2a1e9-241">Diğer çözümleyiciler, [HTML çevikliği paketi (HAP)](https://html-agility-pack.net/)gibi kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-241">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="2a1e9-242">Diğer bir yaklaşım ise, antiforgery sisteminin istek doğrulama belirtecini ve antiforgery tanımlama bilgisini doğrudan işlemek için kod yazmaktır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-242">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="2a1e9-243">WithWebHostBuilder ile istemciyi özelleştirme</span><span class="sxs-lookup"><span data-stu-id="2a1e9-243">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="2a1e9-244">Bir test yönteminde ek yapılandırma gerektiğinde, [Withwebhostbuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) , `WebApplicationFactory` yapılandırmaya göre daha fazla özelleştirilmiş bir [ıwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) ile yeni bir oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-244">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="2a1e9-245">`Post_DeleteMessageHandler_ReturnsRedirectToRoot` [Örnek uygulamanın](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) test yöntemi öğesinin kullanımını gösterir `WithWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-245">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="2a1e9-246">Bu test, SUT 'da form gönderimini tetikleyerek veritabanında silme işlemini gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-246">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="2a1e9-247">Sınıftaki başka bir test `IndexPageTests` veritabanındaki tüm kayıtları silen ve yönteminden önce çalışabilen bir işlem gerçekleştirdiğinden `Post_DeleteMessageHandler_ReturnsRedirectToRoot` , sut 'in silinmesine yönelik bir kaydın mevcut olduğundan emin olmak için veritabanı bu test yönteminde yeniden uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-247">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="2a1e9-248">SUT içindeki formun ilk Sil düğmesini seçmek, `messages` sut isteğine göre benzetilir:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-248">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="2a1e9-249">İstemci seçenekleri</span><span class="sxs-lookup"><span data-stu-id="2a1e9-249">Client options</span></span>

<span data-ttu-id="2a1e9-250">Aşağıdaki tabloda, örnek oluştururken kullanılabilen varsayılan [Webapplicationfactoryclientoptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) gösterilmektedir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-250">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="2a1e9-251">Seçenek</span><span class="sxs-lookup"><span data-stu-id="2a1e9-251">Option</span></span> | <span data-ttu-id="2a1e9-252">Açıklama</span><span class="sxs-lookup"><span data-stu-id="2a1e9-252">Description</span></span> | <span data-ttu-id="2a1e9-253">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="2a1e9-253">Default</span></span> |
| ---
<span data-ttu-id="2a1e9-254">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-255">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-256">'Identity'</span></span>
- <span data-ttu-id="2a1e9-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-258">'Razor'</span></span>
- <span data-ttu-id="2a1e9-259">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-259">'SignalR' uid:</span></span> 

<span data-ttu-id="2a1e9-260">--- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-260">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-261">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-262">'Identity'</span></span>
- <span data-ttu-id="2a1e9-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-264">'Razor'</span></span>
- <span data-ttu-id="2a1e9-265">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-266">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-267">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-268">'Identity'</span></span>
- <span data-ttu-id="2a1e9-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-270">'Razor'</span></span>
- <span data-ttu-id="2a1e9-271">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-272">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-273">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-274">'Identity'</span></span>
- <span data-ttu-id="2a1e9-275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-275">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-276">'Razor'</span></span>
- <span data-ttu-id="2a1e9-277">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-277">'SignalR' uid:</span></span> 

<span data-ttu-id="2a1e9-278">------ | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-278">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-279">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-280">'Identity'</span></span>
- <span data-ttu-id="2a1e9-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-282">'Razor'</span></span>
- <span data-ttu-id="2a1e9-283">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-283">'SignalR' uid:</span></span> 

<span data-ttu-id="2a1e9-284">---- | | [Allowoto yeniden yönlendirme](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | `HttpClient`Örneklerin otomatik olarak yeniden yönlendirme yanıtlarını izleyip izmeyeceğini alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="2a1e9-285"> | `true`| | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Örneklerin temel adresini alır veya ayarlar `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-285"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="2a1e9-286"> | `http://localhost`| | [Handlecookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | `HttpClient`Örneklerin tanımlama bilgilerini işlemesinin gerekip gerekmediğini alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-286"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="2a1e9-287"> | `true`| | [Maxautomaticyönlendirmeler](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Örneklerin izlemesi gereken en fazla yeniden yönlendirme yanıtı sayısını alır veya ayarlar `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-287"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="2a1e9-288">| 7 |</span><span class="sxs-lookup"><span data-stu-id="2a1e9-288">| 7 |</span></span>

<span data-ttu-id="2a1e9-289">Sınıfını oluşturun `WebApplicationFactoryClientOptions` ve [createclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) yöntemine geçirin (varsayılan değerler kod örneğinde gösterilir):</span><span class="sxs-lookup"><span data-stu-id="2a1e9-289">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="2a1e9-290">Sahte hizmetler ekleme</span><span class="sxs-lookup"><span data-stu-id="2a1e9-290">Inject mock services</span></span>

<span data-ttu-id="2a1e9-291">Hizmetler, ana bilgisayar tasarımcısında [Configuretestservices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) çağrısıyla bir testte geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-291">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="2a1e9-292">**Sahte hizmetleri eklemek için SUT, `Startup` yöntemi olan bir sınıfa sahip olmalıdır `Startup.ConfigureServices` .**</span><span class="sxs-lookup"><span data-stu-id="2a1e9-292">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="2a1e9-293">Örnek SUT, bir teklif döndüren kapsamlı bir hizmet içerir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-293">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="2a1e9-294">Dizin sayfası istendiğinde, teklif Dizin sayfasındaki gizli bir alana katıştırılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-294">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="2a1e9-295">*Hizmetler/ıquoteservice. cs*:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-295">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="2a1e9-296">*Hizmetler/QuoteService. cs*:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-296">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="2a1e9-297">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-297">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="2a1e9-298">*Pages/Index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-298">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="2a1e9-299">*Sayfa/dizin. cs*:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-299">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="2a1e9-300">Aşağıdaki biçimlendirme, SUT uygulaması çalıştırıldığında oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-300">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="2a1e9-301">Bir tümleştirme testinde hizmet ve teklif ekleme işlemini test etmek için, test tarafından SUT 'ye bir sahte hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-301">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="2a1e9-302">Sahte hizmet, uygulamanın `QuoteService` adı verilen test uygulaması tarafından verilen bir hizmetle değiştirilir `TestQuoteService` :</span><span class="sxs-lookup"><span data-stu-id="2a1e9-302">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="2a1e9-303">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-303">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="2a1e9-304">`ConfigureTestServices`çağrılır ve kapsamlı hizmet kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-304">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="2a1e9-305">Testin yürütülmesi sırasında üretilen biçimlendirme tarafından sağlanan teklif metnini yansıtır `TestQuoteService` , bu nedenle onaylama işlemi geçer:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-305">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="2a1e9-306">Sahte kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="2a1e9-306">Mock authentication</span></span>

<span data-ttu-id="2a1e9-307">`AuthTests`Sınıftaki testler güvenli bir uç noktanın bulunup bulunmadığını denetler:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-307">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="2a1e9-308">Kimliği doğrulanmamış bir kullanıcıyı uygulamanın oturum açma sayfasına yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-308">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="2a1e9-309">Kimliği doğrulanmış bir kullanıcı için içerik döndürür.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-309">Returns content for an authenticated user.</span></span>

<span data-ttu-id="2a1e9-310">SUT sayfasında, sayfada bir `/SecurePage` [Authorizefilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) uygulamak için Bu sayfa bir [authorizepage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) kuralı kullanır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-310">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="2a1e9-311">Daha fazla bilgi için bkz. [ Razor Sayfalar yetkilendirme kuralları](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span><span class="sxs-lookup"><span data-stu-id="2a1e9-311">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="2a1e9-312">`Get_SecurePageRedirectsAnUnauthenticatedUser`Testte, bir [Webapplicationfactoryclientoptions, Allowclıredirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) ' i ayarlayarak [yeniden](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) yönlendirmeye izin vermez olarak ayarlanır `false` :</span><span class="sxs-lookup"><span data-stu-id="2a1e9-312">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="2a1e9-313">İstemcinin yeniden yönlendirmeyi izlemeye izin vererek aşağıdaki denetimler yapılabilir:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-313">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="2a1e9-314">SUT tarafından döndürülen durum kodu, oturum açma sayfasına yeniden yönlendirmeden sonra, [HttpStatusCode. Tamam](/dotnet/api/system.net.httpstatuscode)olacak şekilde, son durum koduna değil beklenen [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) sonucuyla denetlenebilir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-314">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="2a1e9-315">`Location`Yanıt üst bilgilerindeki üst bilgi değeri, `http://localhost/Identity/Account/Login` üst bilgi bulunmayan son oturum açma sayfası yanıtı değil, ile başladığı onaylanacak şekilde denetlenir `Location` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-315">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="2a1e9-316">Test uygulaması, <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> kimlik doğrulama ve yetkilendirme işlemlerini test etmek için bir [Configuretestservices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) içinde bir model oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-316">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="2a1e9-317">En az bir senaryo, bir [kimlik doğrulayan Poesult. Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)döndürür:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-317">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="2a1e9-318">, `TestAuthHandler` Kimlik doğrulama şeması, için kaydedildiği yere ayarlandığında bir kullanıcının kimliğini doğrulamak için `Test` çağrılır `AddAuthentication` `ConfigureTestServices` :</span><span class="sxs-lookup"><span data-stu-id="2a1e9-318">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="2a1e9-319">Hakkında daha fazla bilgi için `WebApplicationFactoryClientOptions` [istemci seçenekleri](#client-options) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-319">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="2a1e9-320">Ortamı ayarlama</span><span class="sxs-lookup"><span data-stu-id="2a1e9-320">Set the environment</span></span>

<span data-ttu-id="2a1e9-321">Varsayılan olarak, SUT 'nin ana bilgisayar ve uygulama ortamı geliştirme ortamını kullanacak şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-321">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="2a1e9-322">Kullanırken SUT ortamını geçersiz kılmak için `IHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="2a1e9-322">To override the SUT's environment when using `IHostBuilder`:</span></span>

* <span data-ttu-id="2a1e9-323">`ASPNETCORE_ENVIRONMENT`Ortam değişkenini (örneğin,,, `Staging` veya gibi `Production` başka bir özel değeri `Testing` ) ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-323">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="2a1e9-324">`CreateHostBuilder`Ön eki olan ortam değişkenlerini okumak için test uygulamasında geçersiz kılın `ASPNETCORE` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-324">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() =>
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

<span data-ttu-id="2a1e9-325">SUT Web konağını ( `IWebHostBuilder` ) kullanıyorsa, geçersiz kıl `CreateWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="2a1e9-325">If the SUT uses the Web Host (`IWebHostBuilder`), override `CreateWebHostBuilder`:</span></span>

```csharp
protected override IWebHostBuilder CreateWebHostBuilder() =>
    base.CreateWebHostBuilder().UseEnvironment("Testing");
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="2a1e9-326">Test altyapısının uygulama içeriği kök yolunu nasıl öğrendiğini</span><span class="sxs-lookup"><span data-stu-id="2a1e9-326">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="2a1e9-327">`WebApplicationFactory`Oluşturucu, derleme üzerinde bir anahtarla eşit anahtar olan tümleştirme testlerini içeren bir [Webapplicationfactorycontentrootattribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) arayarak uygulama [içeriği kök](xref:fundamentals/index#content-root) yolunu alır `TEntryPoint` `System.Reflection.Assembly.FullName` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-327">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="2a1e9-328">Doğru anahtara sahip bir özniteliğin bulunamaması durumunda, `WebApplicationFactory` bir çözüm dosyası (*. sln*) aramaya geri döner ve `TEntryPoint` derleme adını çözüm dizinine ekler.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-328">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="2a1e9-329">Uygulama kök dizini (içerik kök yolu) görünümleri ve içerik dosyalarını saptamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-329">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="2a1e9-330">Gölge kopyalamayı devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="2a1e9-330">Disable shadow copying</span></span>

<span data-ttu-id="2a1e9-331">Gölge kopyalama, testlerin çıkış dizininden farklı bir dizinde yürütülmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-331">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="2a1e9-332">Testlerin düzgün çalışması için gölge kopyalama devre dışı bırakılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-332">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="2a1e9-333">[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) xUnit kullanır ve doğru yapılandırma ayarıyla bir *xUnit. Runner. JSON* dosyası ekleyerek xUnit için gölge kopyalamayı devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-333">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="2a1e9-334">Daha fazla bilgi için bkz. [JSON Ile xUnit yapılandırma](https://xunit.github.io/docs/configuring-with-json.html).</span><span class="sxs-lookup"><span data-stu-id="2a1e9-334">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="2a1e9-335">Aşağıdaki içeriğe sahip test projesinin köküne *xUnit. Runner. JSON* dosyasını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-335">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="2a1e9-336">Nesnelerin elden çıkarılması</span><span class="sxs-lookup"><span data-stu-id="2a1e9-336">Disposal of objects</span></span>

<span data-ttu-id="2a1e9-337">`IClassFixture`Uygulamanın testleri yürütüldükten sonra, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) ve [HttpClient](/dotnet/api/system.net.http.httpclient) , [webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)'nin xUnit 'i çıkardığı zaman yürütülür.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-337">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="2a1e9-338">Geliştirici tarafından oluşturulan nesneler elden çıkarma gerektiriyorsa, bunları `IClassFixture` uygulamada atın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-338">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="2a1e9-339">Daha fazla bilgi için bkz. [Dispose yöntemi uygulama](/dotnet/standard/garbage-collection/implementing-dispose).</span><span class="sxs-lookup"><span data-stu-id="2a1e9-339">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="2a1e9-340">Tümleştirme Testleri örneği</span><span class="sxs-lookup"><span data-stu-id="2a1e9-340">Integration tests sample</span></span>

<span data-ttu-id="2a1e9-341">[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) iki uygulamalardan oluşur:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-341">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="2a1e9-342">Uygulama</span><span class="sxs-lookup"><span data-stu-id="2a1e9-342">App</span></span> | <span data-ttu-id="2a1e9-343">Proje dizini</span><span class="sxs-lookup"><span data-stu-id="2a1e9-343">Project directory</span></span> | <span data-ttu-id="2a1e9-344">Açıklama</span><span class="sxs-lookup"><span data-stu-id="2a1e9-344">Description</span></span> |
| --- | ---
<span data-ttu-id="2a1e9-345">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-346">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-347">'Identity'</span></span>
- <span data-ttu-id="2a1e9-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-349">'Razor'</span></span>
- <span data-ttu-id="2a1e9-350">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-351">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-352">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-353">'Identity'</span></span>
- <span data-ttu-id="2a1e9-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-355">'Razor'</span></span>
- <span data-ttu-id="2a1e9-356">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-357">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-358">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-359">'Identity'</span></span>
- <span data-ttu-id="2a1e9-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-361">'Razor'</span></span>
- <span data-ttu-id="2a1e9-362">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-363">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-364">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-365">'Identity'</span></span>
- <span data-ttu-id="2a1e9-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-367">'Razor'</span></span>
- <span data-ttu-id="2a1e9-368">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-369">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-370">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-371">'Identity'</span></span>
- <span data-ttu-id="2a1e9-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-373">'Razor'</span></span>
- <span data-ttu-id="2a1e9-374">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-375">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-376">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-377">'Identity'</span></span>
- <span data-ttu-id="2a1e9-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-379">'Razor'</span></span>
- <span data-ttu-id="2a1e9-380">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-380">'SignalR' uid:</span></span> 

<span data-ttu-id="2a1e9-381">--------- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-381">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-382">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-383">'Identity'</span></span>
- <span data-ttu-id="2a1e9-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-385">'Razor'</span></span>
- <span data-ttu-id="2a1e9-386">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-387">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-388">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-389">'Identity'</span></span>
- <span data-ttu-id="2a1e9-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-391">'Razor'</span></span>
- <span data-ttu-id="2a1e9-392">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-393">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-394">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-395">'Identity'</span></span>
- <span data-ttu-id="2a1e9-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-397">'Razor'</span></span>
- <span data-ttu-id="2a1e9-398">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-398">'SignalR' uid:</span></span> 

<span data-ttu-id="2a1e9-399">------ | | İleti uygulaması (SUT) | *src/RazorPagesProject* | Bir kullanıcının, iletileri eklemesini, silmesini, silmesini ve analiz etmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-399">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="2a1e9-400">| | Test uygulaması | *testler/RazorPagesProject. testler* | SUT test tümleştirmesi için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-400">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="2a1e9-401">Testler, [Visual Studio](https://visualstudio.microsoft.com)gıbı bir IDE 'nin yerleşik test özellikleri kullanılarak çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-401">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="2a1e9-402">[Visual Studio Code](https://code.visualstudio.com/) veya komut satırı kullanıyorsanız, *testler/RazorPagesProject. Tests* dizinindeki bir komut isteminde aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-402">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="2a1e9-403">İleti uygulaması (SUT) kuruluşu</span><span class="sxs-lookup"><span data-stu-id="2a1e9-403">Message app (SUT) organization</span></span>

<span data-ttu-id="2a1e9-404">SUT, Razor aşağıdaki özelliklere sahip bir sayfalar ileti sistemidir:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-404">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="2a1e9-405">Uygulamanın (*Pages/Index. cshtml* ve *Pages/index. cshtml. cs*) dizin sayfası, iletilerin eklenmesi, silinmesini ve ANALIZINI denetlemek için bir UI ve sayfa modeli yöntemleri sağlar (ileti başına ortalama sözcük).</span><span class="sxs-lookup"><span data-stu-id="2a1e9-405">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="2a1e9-406">Bir ileti, `Message` sınıfı (*Data/Message. cs*) ile iki özelliği olan ( `Id` anahtar) ve `Text` (ileti) açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-406">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="2a1e9-407">`Text`Özelliği gereklidir ve 200 karakterle sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-407">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="2a1e9-408">İletiler, [Entity Framework bellek içi veritabanı](/ef/core/providers/in-memory/)&#8224; kullanılarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-408">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="2a1e9-409">Uygulama, veritabanı bağlamı sınıfında ( `AppDbContext` *Data/AppDbContext. cs*) bir veri ERIŞIM katmanı (dal) içerir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-409">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="2a1e9-410">Veritabanı uygulama başlangıcında boşsa, ileti deposu üç iletiyle başlatılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-410">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="2a1e9-411">Uygulama `/SecurePage` yalnızca kimliği doğrulanmış bir kullanıcı tarafından erişilebilen bir içerir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-411">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="2a1e9-412">EF konusunda, [InMemory Ile Test](/ef/core/miscellaneous/testing/in-memory)&#8224;, MSTest ile testler için bellek içi bir veritabanının nasıl kullanılacağını açıklar.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-412">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="2a1e9-413">Bu konu [xUnit](https://xunit.github.io/) test çerçevesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-413">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="2a1e9-414">Farklı test çerçeveleri genelinde test kavramları ve test uygulamaları benzerdir ancak aynı değildir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-414">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="2a1e9-415">Uygulama, depo desenini kullanmıyor ve [Iş birimi (UoW) düzeninin](https://martinfowler.com/eaaCatalog/unitOfWork.html)etkin bir örneği olmamasına karşın, Razor Sayfalar bu geliştirme düzenlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-415">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="2a1e9-416">Daha fazla bilgi için bkz. [altyapı Kalıcılık katmanını tasarlama](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) ve [Test denetleyicisi mantığı](/aspnet/core/mvc/controllers/testing) (örnek, depo modelini uygular).</span><span class="sxs-lookup"><span data-stu-id="2a1e9-416">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="2a1e9-417">Test uygulaması kuruluşu</span><span class="sxs-lookup"><span data-stu-id="2a1e9-417">Test app organization</span></span>

<span data-ttu-id="2a1e9-418">Test uygulaması, *testler/RazorPagesProject. Tests* dizini içindeki bir konsol uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-418">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="2a1e9-419">Uygulama dizinini test et</span><span class="sxs-lookup"><span data-stu-id="2a1e9-419">Test app directory</span></span> | <span data-ttu-id="2a1e9-420">Açıklama</span><span class="sxs-lookup"><span data-stu-id="2a1e9-420">Description</span></span> |
| ---
<span data-ttu-id="2a1e9-421">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-422">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-423">'Identity'</span></span>
- <span data-ttu-id="2a1e9-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-425">'Razor'</span></span>
- <span data-ttu-id="2a1e9-426">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-427">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-428">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-429">'Identity'</span></span>
- <span data-ttu-id="2a1e9-430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-430">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-431">'Razor'</span></span>
- <span data-ttu-id="2a1e9-432">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-433">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-434">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-435">'Identity'</span></span>
- <span data-ttu-id="2a1e9-436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-436">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-437">'Razor'</span></span>
- <span data-ttu-id="2a1e9-438">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-439">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-440">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-441">'Identity'</span></span>
- <span data-ttu-id="2a1e9-442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-442">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-443">'Razor'</span></span>
- <span data-ttu-id="2a1e9-444">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-445">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-446">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-447">'Identity'</span></span>
- <span data-ttu-id="2a1e9-448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-448">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-449">'Razor'</span></span>
- <span data-ttu-id="2a1e9-450">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-451">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-452">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-453">'Identity'</span></span>
- <span data-ttu-id="2a1e9-454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-454">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-455">'Razor'</span></span>
- <span data-ttu-id="2a1e9-456">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-457">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-458">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-459">'Identity'</span></span>
- <span data-ttu-id="2a1e9-460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-461">'Razor'</span></span>
- <span data-ttu-id="2a1e9-462">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-462">'SignalR' uid:</span></span> 

<span data-ttu-id="2a1e9-463">--------- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-463">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-464">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-465">'Identity'</span></span>
- <span data-ttu-id="2a1e9-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-467">'Razor'</span></span>
- <span data-ttu-id="2a1e9-468">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-469">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-470">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-471">'Identity'</span></span>
- <span data-ttu-id="2a1e9-472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-472">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-473">'Razor'</span></span>
- <span data-ttu-id="2a1e9-474">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-475">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-476">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-477">'Identity'</span></span>
- <span data-ttu-id="2a1e9-478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-478">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-479">'Razor'</span></span>
- <span data-ttu-id="2a1e9-480">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-480">'SignalR' uid:</span></span> 

<span data-ttu-id="2a1e9-481">------ | | *Authtests* | İçin test yöntemleri içerir:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-481">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="2a1e9-482">Güvenli bir sayfaya, kimliği doğrulanmamış bir kullanıcıyla erişme.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-482">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="2a1e9-483">Bir sahte ile kimliği doğrulanmış bir kullanıcı tarafından güvenli bir sayfaya erişme <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-483">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="2a1e9-484">GitHub kullanıcı profilini alma ve profilin Kullanıcı oturum açma bilgilerini denetleme.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-484">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="2a1e9-485">| | *Basictests* | Yönlendirme ve içerik türü için bir test yöntemi içerir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-485">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="2a1e9-486">| | *Integrationtests* | Özel sınıf kullanan dizin sayfası için tümleştirme testlerini içerir `WebApplicationFactory` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-486">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="2a1e9-487">| | *Yardımcılar/yardımcı programlar* | </span><span class="sxs-lookup"><span data-stu-id="2a1e9-487">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="2a1e9-488">*Utilities.cs* , `InitializeDbForTests` veritabanını test verileriyle tohum için kullanılan yöntemi içerir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-488">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="2a1e9-489">*HtmlHelpers.cs* `IHtmlDocument` , test yöntemleri tarafından kullanılmak üzere anglesharp döndüren bir yöntem sağlar.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-489">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="2a1e9-490">*HttpClientExtensions.cs* `SendAsync` , istekleri sut 'a göndermek için için aşırı yüklemeler sağlar.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-490">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="2a1e9-491">Test çerçevesi [xUnit](https://xunit.github.io/)' dir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-491">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="2a1e9-492">Tümleştirme testleri, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)içeren [Microsoft. Aspnetcore. testhost](/dotnet/api/microsoft.aspnetcore.testhost)kullanılarak yürütülür.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-492">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="2a1e9-493">Test ana bilgisayarı ve test sunucusunu yapılandırmak için [Microsoft. AspNetCore. Mvc. Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) paketi kullanıldığından, ve paketleri test uygulamasındaki `TestHost` `TestServer` Proje dosyasında veya geliştirici yapılandırmasında doğrudan paket başvuruları gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-493">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="2a1e9-494">**Test için veritabanının temelini sağlama**</span><span class="sxs-lookup"><span data-stu-id="2a1e9-494">**Seeding the database for testing**</span></span>

<span data-ttu-id="2a1e9-495">Tümleştirme testleri genellikle veritabanında test yürütmeden önce küçük bir veri kümesi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-495">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="2a1e9-496">Örneğin, veritabanı kaydı silme için bir silme testi çağrılarında, silme isteğinin başarılı olması için veritabanının en az bir kaydı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-496">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="2a1e9-497">Örnek uygulama, *Utilities.cs* içinde testlerin, yürütme sırasında kullanabileceği üç iletiyle birlikte veritabanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-497">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="2a1e9-498">SUT 'un veritabanı bağlamı `Startup.ConfigureServices` yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-498">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2a1e9-499">Test uygulamasının `builder.ConfigureServices` geri çağırması, uygulamanın kodu yürütüldükten *sonra* yürütülür `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-499">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="2a1e9-500">Testler için farklı bir veritabanı kullanmak istiyorsanız, uygulamanın veritabanı bağlamı içinde değiştirilmelidir `builder.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-500">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="2a1e9-501">Daha fazla bilgi için, [WebApplicationFactory 'Yi özelleştirme](#customize-webapplicationfactory) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-501">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

<span data-ttu-id="2a1e9-502">[Web Host} (XREF: temelleri/Host/Web-Host) kullanan sımlar için, test uygulamasının `builder.ConfigureServices` geri çağırması SUT kodundan *önce* yürütülür `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-502">For SUTs that still use the [Web Host}(xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="2a1e9-503">Test uygulamasının `builder.ConfigureTestServices` geri çağırması *sonra*yürütülür.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-503">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2a1e9-504">Tümleştirme sınamaları, uygulamanın bileşenlerinin veritabanı, dosya sistemi ve ağ gibi destekleyici altyapısını içeren bir düzeyde doğru şekilde çalışmasını güvence altına alır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-504">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="2a1e9-505">ASP.NET Core, test Web ana bilgisayarı ve bellek içi test sunucusu olan bir birim testi çerçevesini kullanarak tümleştirme testlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-505">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="2a1e9-506">Bu konuda, birim testlerinin temel bir şekilde anlaşıldığı varsayılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-506">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="2a1e9-507">Test kavramları hakkında bilgi sahibi değilseniz, [.NET Core 'Da birim testine ve .NET Standard](/dotnet/core/testing/) konusuna ve bağlı içeriğine bakın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-507">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="2a1e9-508">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2a1e9-508">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2a1e9-509">Örnek uygulama bir Razor Sayfalar uygulamasıdır ve sayfaların temel olarak anlaşıldığını varsayar Razor .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-509">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="2a1e9-510">Sayfalarla alışkın değilseniz Razor , aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-510">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="2a1e9-511">[Sayfalara giriş Razor](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="2a1e9-511">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="2a1e9-512">[Sayfalarla çalışmaya başlama Razor](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="2a1e9-512">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="2a1e9-513">[RazorSayfalar birim testleri](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="2a1e9-513">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="2a1e9-514">Maça 'Ları test etmek için, bir tarayıcıyı otomatikleştirebilen [Selenium](https://www.seleniumhq.org/)gibi bir araç öneririz.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-514">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="2a1e9-515">Tümleştirme testlerine giriş</span><span class="sxs-lookup"><span data-stu-id="2a1e9-515">Introduction to integration tests</span></span>

<span data-ttu-id="2a1e9-516">Tümleştirme testleri, bir uygulamanın bileşenlerini [birim testlerinden](/dotnet/core/testing/)daha geniş bir düzeyde değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-516">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="2a1e9-517">Birim testleri, ayrı sınıf yöntemleri gibi yalıtılmış yazılım bileşenlerini test etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-517">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="2a1e9-518">Tümleştirme testleri iki veya daha fazla uygulama bileşeninin beklenen bir sonuç üretmek için birlikte çalıştığını ve muhtemelen bir isteği tam olarak işlemek için gereken her bileşeni de dahil olduğunu onaylar.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-518">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="2a1e9-519">Bu geniş testler, uygulamanın altyapısını ve tüm çatısını test etmek için kullanılır, genellikle aşağıdaki bileşenler dahil:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-519">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="2a1e9-520">Veritabanı</span><span class="sxs-lookup"><span data-stu-id="2a1e9-520">Database</span></span>
* <span data-ttu-id="2a1e9-521">Dosya sistemi</span><span class="sxs-lookup"><span data-stu-id="2a1e9-521">File system</span></span>
* <span data-ttu-id="2a1e9-522">Ağ gereçleri</span><span class="sxs-lookup"><span data-stu-id="2a1e9-522">Network appliances</span></span>
* <span data-ttu-id="2a1e9-523">İstek-yanıt işlem hattı</span><span class="sxs-lookup"><span data-stu-id="2a1e9-523">Request-response pipeline</span></span>

<span data-ttu-id="2a1e9-524">Birim testleri, altyapı bileşenlerinin yerine, *Fakes* veya *sahte nesneler*olarak bilinen fabriccomponents bileşenlerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-524">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="2a1e9-525">Birim testlerinin aksine, tümleştirme testleri:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-525">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="2a1e9-526">Uygulamanın üretimde kullandığı gerçek bileşenleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-526">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="2a1e9-527">Daha fazla kod ve veri işleme gerektir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-527">Require more code and data processing.</span></span>
* <span data-ttu-id="2a1e9-528">Çalıştırmak daha uzun sürer.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-528">Take longer to run.</span></span>

<span data-ttu-id="2a1e9-529">Bu nedenle, tümleştirme testlerinin kullanımını en önemli altyapı senaryolarıyla sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-529">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="2a1e9-530">Bir davranış, birim testi veya tümleştirme testi kullanılarak test edilebilir ise, birim testini seçin.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-530">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="2a1e9-531">Veritabanları ve dosya sistemleriyle ilgili her olası veri ve dosya erişimi için tümleştirme testleri yazma.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-531">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="2a1e9-532">Bir uygulamadaki kaç yerde veritabanlarıyla ve dosya sistemleriyle etkileşime girdiğinize bakılmaksızın, bir dizi Read, Write, Update ve DELETE tümleştirme testi, genellikle veritabanı ve dosya sistemi bileşenlerinin yeterli şekilde test edilmesine sahip olur.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-532">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="2a1e9-533">Bu bileşenlerle etkileşime geçen Yöntem mantığının rutin testleri için birim testleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-533">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="2a1e9-534">Birim testlerinde, altyapı kullanımı ve moizler 'in kullanılması daha hızlı test yürütmesiyle sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-534">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="2a1e9-535">Tümleştirme testlerinin tartışmalarında, test edilen proje genellikle *test altındaki sistem*veya kısa IÇIN "sut" olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-535">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="2a1e9-536">*Bu konu başlığı altında, sınanan ASP.NET Core uygulamasına başvurmak için "SUT" kullanılır.*</span><span class="sxs-lookup"><span data-stu-id="2a1e9-536">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="2a1e9-537">ASP.NET Core tümleştirme testleri</span><span class="sxs-lookup"><span data-stu-id="2a1e9-537">ASP.NET Core integration tests</span></span>

<span data-ttu-id="2a1e9-538">ASP.NET Core tümleştirme testleri şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-538">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="2a1e9-539">Testleri içermesi ve yürütmek için bir test projesi kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-539">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="2a1e9-540">Test projesinin SUT 'a bir başvurusu vardır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-540">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="2a1e9-541">Test projesi SUT için bir test Web Konağı oluşturur ve SUT ile istekleri ve yanıtları işlemek için bir test sunucusu istemcisi kullanır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-541">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="2a1e9-542">Test Çalıştırıcısı, testleri yürütmek ve test sonuçlarını raporlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-542">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="2a1e9-543">Tümleştirme testleri, olağan *düzenleme*, *hareket*ve *onaylama* testi adımlarını içeren bir olay dizisini izler:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-543">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="2a1e9-544">SUT 'un web ana bilgisayarı yapılandırıldı.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-544">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="2a1e9-545">İstekleri uygulamaya göndermek için bir test sunucusu istemcisi oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-545">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="2a1e9-546">Testi *Düzenle* adımı yürütülür: test uygulaması bir istek hazırlar.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-546">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="2a1e9-547">*Davran* test adımı yürütülür: istemci, isteği gönderir ve yanıtını alır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-547">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="2a1e9-548">*Onaylama* testi adımı yürütülür: *Gerçek* yanıt, *beklenen* bir yanıta bağlı olarak *başarılı* veya *başarısız* olarak onaylanır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-548">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="2a1e9-549">İşlem, tüm testler yürütülene kadar devam eder.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-549">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="2a1e9-550">Test sonuçları raporlanır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-550">The test results are reported.</span></span>

<span data-ttu-id="2a1e9-551">Genellikle, test Web ana bilgisayarı, test çalıştırmaları için uygulamanın normal web ana bilgisayarında farklı şekilde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-551">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="2a1e9-552">Örneğin, testler için farklı bir veritabanı veya farklı uygulama ayarları kullanılıyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-552">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="2a1e9-553">Test Web Konağı ve bellek içi test sunucusu ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) gibi altyapı bileşenleri, [Microsoft. Aspnetcore. Mvc. Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) paketi tarafından sağlanır veya yönetilir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-553">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="2a1e9-554">Bu paketin kullanımı, test oluşturma ve yürütmeyi kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-554">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="2a1e9-555">`Microsoft.AspNetCore.Mvc.Testing`Paket aşağıdaki görevleri gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-555">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="2a1e9-556">Bağımlılıklar dosyasını (*. Deps*) sut 'den test projesinin *bin* dizinine kopyalar.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-556">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="2a1e9-557">, Testler yürütüldüğünde statik dosya ve sayfa/görünümlerin bulunması için [içerik kökünü](xref:fundamentals/index#content-root) sut 'nin proje köküne ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-557">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="2a1e9-558">, İle SUT önyükleyiciyi kolaylaştırmak için [Webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) sınıfını sağlar `TestServer` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-558">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="2a1e9-559">[Birim](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) testi belgeleri bir test projesi ve Test Çalıştırıcısı ayarlamayı ve testlerin ve test sınıflarının nasıl belirleneceğini gösteren testlerin ve önerilerin nasıl çalıştırılacağını gösteren ayrıntılı yönergelerle birlikte açıklanır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-559">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="2a1e9-560">Bir uygulama için test projesi oluştururken, birim testlerini tümleştirme testlerinden farklı projelere ayırın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-560">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="2a1e9-561">Bu, altyapı testi bileşenlerinin birim testlerine yanlışlıkla dahil olmamasını sağlamaya yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-561">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="2a1e9-562">Birim ve tümleştirme testlerinin ayrımı, hangi test kümesinin çalıştırılmasına da izin verir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-562">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="2a1e9-563">Uygulamaların ve MVC uygulamalarının testleri için yapılandırma arasında neredeyse fark yoktur Razor .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-563">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="2a1e9-564">Tek fark testlerin adlandırılmasınlardır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-564">The only difference is in how the tests are named.</span></span> <span data-ttu-id="2a1e9-565">Bir Razor Sayfalar uygulamasında sayfa uç noktalarının testleri genellikle sayfa modeli sınıfından sonra (örneğin, `IndexPageTests` Dizin sayfasına yönelik bileşen tümleştirmesini test etmek için) adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-565">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="2a1e9-566">MVC uygulamasında, testler genellikle denetleyici sınıfları tarafından düzenlenir ve test ettikleri denetleyicilerde (örneğin, `HomeControllerTests` ana denetleyiciye yönelik bileşen tümleştirmesini test etmek için) adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-566">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="2a1e9-567">Test uygulaması önkoşulları</span><span class="sxs-lookup"><span data-stu-id="2a1e9-567">Test app prerequisites</span></span>

<span data-ttu-id="2a1e9-568">Test projesi şunları vermelidir:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-568">The test project must:</span></span>

* <span data-ttu-id="2a1e9-569">Aşağıdaki paketlere başvurun:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-569">Reference the following packages:</span></span>
  * [<span data-ttu-id="2a1e9-570">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="2a1e9-570">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="2a1e9-571">Microsoft. AspNetCore. Mvc. test</span><span class="sxs-lookup"><span data-stu-id="2a1e9-571">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="2a1e9-572">() Proje dosyasında Web SDK 'sını belirtin `<Project Sdk="Microsoft.NET.Sdk.Web">` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-572">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="2a1e9-573">[Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'e başvururken Web SDK 'sı gereklidir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-573">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="2a1e9-574">Bu Önkoşullar [örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)görülebilir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-574">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="2a1e9-575">*Testler/RazorPagesProject. Tests/RazorPagesProject. Tests. csproj* dosyasını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-575">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="2a1e9-576">Örnek uygulama, [xUnit](https://xunit.github.io/) test çerçevesini ve [anglesharp](https://anglesharp.github.io/) Parser kitaplığını kullanır, bu nedenle örnek uygulama de şu şekilde başvurur:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-576">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="2a1e9-577">xUnit</span><span class="sxs-lookup"><span data-stu-id="2a1e9-577">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="2a1e9-578">xUnit. Çalıştırıcısı. VisualStudio</span><span class="sxs-lookup"><span data-stu-id="2a1e9-578">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="2a1e9-579">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="2a1e9-579">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="2a1e9-580">SUT ortamı</span><span class="sxs-lookup"><span data-stu-id="2a1e9-580">SUT environment</span></span>

<span data-ttu-id="2a1e9-581">SUT [ortamı](xref:fundamentals/environments) ayarlanmamışsa, ortam varsayılan olarak geliştirme olur.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-581">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="2a1e9-582">Varsayılan WebApplicationFactory ile temel testler</span><span class="sxs-lookup"><span data-stu-id="2a1e9-582">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="2a1e9-583">[Webapplicationfactory \< TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) , tümleştirme testleri için bir [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) oluşturmak üzere kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-583">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="2a1e9-584">`TEntryPoint`, genellikle sınıfının, SUT 'ın giriş noktası sınıfıdır `Startup` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-584">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="2a1e9-585">Test sınıfları sınıfı testlerin içerdiğini göstermek ve sınıftaki testler arasında paylaşılan nesne örnekleri sağlamak için bir *sınıf armatürü* arabirimi ([ıssfixture](https://xunit.github.io/docs/shared-context#class-fixture)) uygular.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-585">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="2a1e9-586">Aşağıdaki test sınıfı, `BasicTests` SUT 'yi önyüklemek `WebApplicationFactory` ve bir test yöntemine [HttpClient](/dotnet/api/system.net.http.httpclient) sağlamak için öğesini kullanır `Get_EndpointsReturnSuccessAndCorrectContentType` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-586">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="2a1e9-587">Yöntemi, yanıt durum kodunun başarılı olup olmadığını denetler (200-299 aralığındaki durum kodları) ve `Content-Type` üst bilgi `text/html; charset=utf-8` birçok uygulama sayfasına yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-587">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="2a1e9-588">[Createclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) , `HttpClient` yeniden yönlendirmeleri otomatik olarak izleyen ve tanımlama bilgilerini işleyen bir örneği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-588">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="2a1e9-589">Varsayılan olarak, [GDPR onay ilkesi](xref:security/gdpr) etkinleştirildiğinde, önemli olmayan tanımlama bilgileri istekler arasında korunmaz.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-589">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="2a1e9-590">TempData sağlayıcısı tarafından kullanılanlar gibi, önemli olmayan tanımlama bilgilerini korumak için, bunları testlerinizde gerekli olarak işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-590">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="2a1e9-591">Tanımlama bilgisini önemli olarak işaretleme hakkında yönergeler için bkz. [temel tanımlama bilgileri](xref:security/gdpr#essential-cookies).</span><span class="sxs-lookup"><span data-stu-id="2a1e9-591">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="2a1e9-592">WebApplicationFactory 'yi özelleştirme</span><span class="sxs-lookup"><span data-stu-id="2a1e9-592">Customize WebApplicationFactory</span></span>

<span data-ttu-id="2a1e9-593">Web ana bilgisayar yapılandırması, öğesinden devralarak `WebApplicationFactory` bir veya daha fazla özel fabrika oluşturmak için test sınıflarından bağımsız olarak oluşturulabilir:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-593">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="2a1e9-594">`WebApplicationFactory` [Configurewebhost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)'ten devralma ve geçersiz kılma.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-594">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="2a1e9-595">[Iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) , uygulama öncesinde yürütülen [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)ile hizmet koleksiyonu yapılandırmasına izin verir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-595">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), which is executed before the app's `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2a1e9-596">[Iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) , [configuretestservices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)ile hizmet koleksiyonundaki kayıtlı hizmetlerin üzerine yazma ve değiştirme olanağı sağlar:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-596">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows overriding and modifying registered services in the service collection with [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="2a1e9-597">[Örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) veritabanı dengeli dağıtımı, yöntemi tarafından gerçekleştirilir `InitializeDbForTests` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-597">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="2a1e9-598">Yöntemi, [tümleştirme testleri örneği: test uygulaması kuruluşu](#test-app-organization) bölümünde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-598">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="2a1e9-599">Özel `CustomWebApplicationFactory` Test sınıflarında kullanın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-599">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="2a1e9-600">Aşağıdaki örnek, sınıfında fabrikası kullanır `IndexPageTests` :</span><span class="sxs-lookup"><span data-stu-id="2a1e9-600">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="2a1e9-601">Örnek uygulamanın istemcisi, aşağıdaki yeniden yönlendirmeleri engelleyecek şekilde yapılandırılmıştır `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-601">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="2a1e9-602">Daha sonra, [sahte kimlik doğrulama](#mock-authentication) bölümünde açıklandığı gibi, bu, testlerin uygulamanın ilk yanıtının sonucunu denetlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-602">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="2a1e9-603">İlk yanıt, bu testlerin çoğunda üst bilgiyle bir yeniden yönlendirmelidir `Location` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-603">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="2a1e9-604">Tipik bir test, `HttpClient` isteği ve yanıtı işlemek için ve yardımcı yöntemlerini kullanır:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-604">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="2a1e9-605">SUT 'a yönelik herhangi bir POST isteği, uygulamanın [veri koruma antiforgery sistemi](xref:security/data-protection/introduction)tarafından otomatik olarak oluşturulan antiforgery denetimini karşılamalıdır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-605">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="2a1e9-606">Bir testin POST isteğini düzenlemek için test uygulaması şunları kullanmalıdır:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-606">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="2a1e9-607">Sayfa için bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-607">Make a request for the page.</span></span>
1. <span data-ttu-id="2a1e9-608">Antiforgery tanımlama bilgisini ayrıştırın ve yanıt doğrulama belirtecini istekten isteyin.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-608">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="2a1e9-609">POST isteğini, antiforgery tanımlama bilgisiyle ve istek doğrulama belirteciyle birlikte yapın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-609">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="2a1e9-610">`SendAsync`Örnek uygulamadaki yardımcı uzantı yöntemleri (*yardımcılar/Httpclienconversionsions. cs*) ve `GetDocumentAsync` yardımcı yöntemi (*yardımcılar/htmlyardımcıları. cs*), aşağıdaki [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) yöntemlerle Antiforgery denetimini işlemek için [anglesharp](https://anglesharp.github.io/) ayrıştırıcısını kullanır:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-610">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="2a1e9-611">`GetDocumentAsync`&ndash; [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) 'ı alır ve döndürür `IHtmlDocument` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-611">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="2a1e9-612">`GetDocumentAsync`, orijinalin temelinde bir *sanal yanıt* hazırlayan bir fabrika kullanır `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-612">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="2a1e9-613">Daha fazla bilgi için bkz. [Anglesharp belgeleri](https://github.com/AngleSharp/AngleSharp#documentation).</span><span class="sxs-lookup"><span data-stu-id="2a1e9-613">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="2a1e9-614">`SendAsync``HttpClient`bir [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) oluşturun ve istekleri sut 'A göndermek Için [Sendadsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) çağrısı yapın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-614">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="2a1e9-615">`SendAsync`HTML biçimini kabul etmek için aşırı yüklemeler ( `IHtmlFormElement` ) ve şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-615">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="2a1e9-616">Formun () Gönder düğmesi `IHtmlElement`</span><span class="sxs-lookup"><span data-stu-id="2a1e9-616">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="2a1e9-617">Form değerleri koleksiyonu ( `IEnumerable<KeyValuePair<string, string>>` )</span><span class="sxs-lookup"><span data-stu-id="2a1e9-617">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="2a1e9-618">Düğme ( `IHtmlElement` ) ve form değerlerini ( `IEnumerable<KeyValuePair<string, string>>` ) gönder</span><span class="sxs-lookup"><span data-stu-id="2a1e9-618">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="2a1e9-619">[Anglesharp](https://anglesharp.github.io/) , bu konuda ve örnek uygulamada Gösterim amacıyla kullanılan bir üçüncü taraf ayrıştırma kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-619">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="2a1e9-620">ASP.NET Core uygulamalarının tümleştirme testi için AngleSharp desteklenmez veya gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-620">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="2a1e9-621">Diğer çözümleyiciler, [HTML çevikliği paketi (HAP)](https://html-agility-pack.net/)gibi kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-621">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="2a1e9-622">Diğer bir yaklaşım ise, antiforgery sisteminin istek doğrulama belirtecini ve antiforgery tanımlama bilgisini doğrudan işlemek için kod yazmaktır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-622">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="2a1e9-623">WithWebHostBuilder ile istemciyi özelleştirme</span><span class="sxs-lookup"><span data-stu-id="2a1e9-623">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="2a1e9-624">Bir test yönteminde ek yapılandırma gerektiğinde, [Withwebhostbuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) , `WebApplicationFactory` yapılandırmaya göre daha fazla özelleştirilmiş bir [ıwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) ile yeni bir oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-624">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="2a1e9-625">`Post_DeleteMessageHandler_ReturnsRedirectToRoot` [Örnek uygulamanın](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) test yöntemi öğesinin kullanımını gösterir `WithWebHostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-625">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="2a1e9-626">Bu test, SUT 'da form gönderimini tetikleyerek veritabanında silme işlemini gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-626">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="2a1e9-627">Sınıftaki başka bir test `IndexPageTests` veritabanındaki tüm kayıtları silen ve yönteminden önce çalışabilen bir işlem gerçekleştirdiğinden `Post_DeleteMessageHandler_ReturnsRedirectToRoot` , sut 'in silinmesine yönelik bir kaydın mevcut olduğundan emin olmak için veritabanı bu test yönteminde yeniden uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-627">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="2a1e9-628">SUT içindeki formun ilk Sil düğmesini seçmek, `messages` sut isteğine göre benzetilir:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-628">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="2a1e9-629">İstemci seçenekleri</span><span class="sxs-lookup"><span data-stu-id="2a1e9-629">Client options</span></span>

<span data-ttu-id="2a1e9-630">Aşağıdaki tabloda, örnek oluştururken kullanılabilen varsayılan [Webapplicationfactoryclientoptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) gösterilmektedir `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-630">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="2a1e9-631">Seçenek</span><span class="sxs-lookup"><span data-stu-id="2a1e9-631">Option</span></span> | <span data-ttu-id="2a1e9-632">Açıklama</span><span class="sxs-lookup"><span data-stu-id="2a1e9-632">Description</span></span> | <span data-ttu-id="2a1e9-633">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="2a1e9-633">Default</span></span> |
| ---
<span data-ttu-id="2a1e9-634">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-635">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-636">'Identity'</span></span>
- <span data-ttu-id="2a1e9-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-638">'Razor'</span></span>
- <span data-ttu-id="2a1e9-639">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-639">'SignalR' uid:</span></span> 

<span data-ttu-id="2a1e9-640">--- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-640">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-641">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-642">'Identity'</span></span>
- <span data-ttu-id="2a1e9-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-644">'Razor'</span></span>
- <span data-ttu-id="2a1e9-645">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-645">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-646">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-647">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-647">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-648">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-648">'Identity'</span></span>
- <span data-ttu-id="2a1e9-649">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-649">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-650">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-650">'Razor'</span></span>
- <span data-ttu-id="2a1e9-651">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-651">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-652">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-653">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-653">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-654">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-654">'Identity'</span></span>
- <span data-ttu-id="2a1e9-655">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-655">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-656">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-656">'Razor'</span></span>
- <span data-ttu-id="2a1e9-657">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-657">'SignalR' uid:</span></span> 

<span data-ttu-id="2a1e9-658">------ | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-658">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-659">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-659">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-660">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-660">'Identity'</span></span>
- <span data-ttu-id="2a1e9-661">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-661">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-662">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-662">'Razor'</span></span>
- <span data-ttu-id="2a1e9-663">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-663">'SignalR' uid:</span></span> 

<span data-ttu-id="2a1e9-664">---- | | [Allowoto yeniden yönlendirme](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | `HttpClient`Örneklerin otomatik olarak yeniden yönlendirme yanıtlarını izleyip izmeyeceğini alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="2a1e9-665"> | `true`| | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Örneklerin temel adresini alır veya ayarlar `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-665"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="2a1e9-666"> | `http://localhost`| | [Handlecookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | `HttpClient`Örneklerin tanımlama bilgilerini işlemesinin gerekip gerekmediğini alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-666"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="2a1e9-667"> | `true`| | [Maxautomaticyönlendirmeler](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Örneklerin izlemesi gereken en fazla yeniden yönlendirme yanıtı sayısını alır veya ayarlar `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-667"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="2a1e9-668">| 7 |</span><span class="sxs-lookup"><span data-stu-id="2a1e9-668">| 7 |</span></span>

<span data-ttu-id="2a1e9-669">Sınıfını oluşturun `WebApplicationFactoryClientOptions` ve [createclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) yöntemine geçirin (varsayılan değerler kod örneğinde gösterilir):</span><span class="sxs-lookup"><span data-stu-id="2a1e9-669">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="2a1e9-670">Sahte hizmetler ekleme</span><span class="sxs-lookup"><span data-stu-id="2a1e9-670">Inject mock services</span></span>

<span data-ttu-id="2a1e9-671">Hizmetler, ana bilgisayar tasarımcısında [Configuretestservices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) çağrısıyla bir testte geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-671">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="2a1e9-672">**Sahte hizmetleri eklemek için SUT, `Startup` yöntemi olan bir sınıfa sahip olmalıdır `Startup.ConfigureServices` .**</span><span class="sxs-lookup"><span data-stu-id="2a1e9-672">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="2a1e9-673">Örnek SUT, bir teklif döndüren kapsamlı bir hizmet içerir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-673">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="2a1e9-674">Dizin sayfası istendiğinde, teklif Dizin sayfasındaki gizli bir alana katıştırılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-674">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="2a1e9-675">*Hizmetler/ıquoteservice. cs*:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-675">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="2a1e9-676">*Hizmetler/QuoteService. cs*:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-676">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="2a1e9-677">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-677">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="2a1e9-678">*Pages/Index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-678">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="2a1e9-679">*Sayfa/dizin. cs*:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-679">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="2a1e9-680">Aşağıdaki biçimlendirme, SUT uygulaması çalıştırıldığında oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-680">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="2a1e9-681">Bir tümleştirme testinde hizmet ve teklif ekleme işlemini test etmek için, test tarafından SUT 'ye bir sahte hizmet eklenir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-681">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="2a1e9-682">Sahte hizmet, uygulamanın `QuoteService` adı verilen test uygulaması tarafından verilen bir hizmetle değiştirilir `TestQuoteService` :</span><span class="sxs-lookup"><span data-stu-id="2a1e9-682">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="2a1e9-683">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-683">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="2a1e9-684">`ConfigureTestServices`çağrılır ve kapsamlı hizmet kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-684">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="2a1e9-685">Testin yürütülmesi sırasında üretilen biçimlendirme tarafından sağlanan teklif metnini yansıtır `TestQuoteService` , bu nedenle onaylama işlemi geçer:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-685">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="2a1e9-686">Sahte kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="2a1e9-686">Mock authentication</span></span>

<span data-ttu-id="2a1e9-687">`AuthTests`Sınıftaki testler güvenli bir uç noktanın bulunup bulunmadığını denetler:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-687">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="2a1e9-688">Kimliği doğrulanmamış bir kullanıcıyı uygulamanın oturum açma sayfasına yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-688">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="2a1e9-689">Kimliği doğrulanmış bir kullanıcı için içerik döndürür.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-689">Returns content for an authenticated user.</span></span>

<span data-ttu-id="2a1e9-690">SUT sayfasında, sayfada bir `/SecurePage` [Authorizefilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) uygulamak için Bu sayfa bir [authorizepage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) kuralı kullanır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-690">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="2a1e9-691">Daha fazla bilgi için bkz. [ Razor Sayfalar yetkilendirme kuralları](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span><span class="sxs-lookup"><span data-stu-id="2a1e9-691">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="2a1e9-692">`Get_SecurePageRedirectsAnUnauthenticatedUser`Testte, bir [Webapplicationfactoryclientoptions, Allowclıredirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) ' i ayarlayarak [yeniden](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) yönlendirmeye izin vermez olarak ayarlanır `false` :</span><span class="sxs-lookup"><span data-stu-id="2a1e9-692">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="2a1e9-693">İstemcinin yeniden yönlendirmeyi izlemeye izin vererek aşağıdaki denetimler yapılabilir:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-693">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="2a1e9-694">SUT tarafından döndürülen durum kodu, oturum açma sayfasına yeniden yönlendirmeden sonra, [HttpStatusCode. Tamam](/dotnet/api/system.net.httpstatuscode)olacak şekilde, son durum koduna değil beklenen [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) sonucuyla denetlenebilir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-694">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="2a1e9-695">`Location`Yanıt üst bilgilerindeki üst bilgi değeri, `http://localhost/Identity/Account/Login` üst bilgi bulunmayan son oturum açma sayfası yanıtı değil, ile başladığı onaylanacak şekilde denetlenir `Location` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-695">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="2a1e9-696">Test uygulaması, <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> kimlik doğrulama ve yetkilendirme işlemlerini test etmek için bir [Configuretestservices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) içinde bir model oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-696">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="2a1e9-697">En az bir senaryo, bir [kimlik doğrulayan Poesult. Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)döndürür:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-697">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="2a1e9-698">, `TestAuthHandler` Kimlik doğrulama şeması, için kaydedildiği yere ayarlandığında bir kullanıcının kimliğini doğrulamak için `Test` çağrılır `AddAuthentication` `ConfigureTestServices` :</span><span class="sxs-lookup"><span data-stu-id="2a1e9-698">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="2a1e9-699">Hakkında daha fazla bilgi için `WebApplicationFactoryClientOptions` [istemci seçenekleri](#client-options) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-699">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="2a1e9-700">Ortamı ayarlama</span><span class="sxs-lookup"><span data-stu-id="2a1e9-700">Set the environment</span></span>

<span data-ttu-id="2a1e9-701">Varsayılan olarak, SUT 'nin ana bilgisayar ve uygulama ortamı geliştirme ortamını kullanacak şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-701">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="2a1e9-702">SUT ortamını geçersiz kılmak için:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-702">To override the SUT's environment:</span></span>

* <span data-ttu-id="2a1e9-703">`ASPNETCORE_ENVIRONMENT`Ortam değişkenini (örneğin,,, `Staging` veya gibi `Production` başka bir özel değeri `Testing` ) ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-703">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="2a1e9-704">`CreateWebHostBuilder`Ortam değişkenini okumak için test uygulaması içinde geçersiz kılın `ASPNETCORE_ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-704">Override `CreateWebHostBuilder` in the test app to read the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override IWebHostBuilder CreateWebHostBuilder()
    {
        return base.CreateWebHostBuilder()
            .UseEnvironment(
                Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    }

    ...
}
```

<span data-ttu-id="2a1e9-705">Ortam ayrıca doğrudan konak Oluşturucu üzerinde özel olarak ayarlanabilir <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="2a1e9-705">The environment can also be set directly on the host builder in a custom <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601>:</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.UseEnvironment(
            Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    
        ...
    }

    ...
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="2a1e9-706">Test altyapısının uygulama içeriği kök yolunu nasıl öğrendiğini</span><span class="sxs-lookup"><span data-stu-id="2a1e9-706">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="2a1e9-707">`WebApplicationFactory`Oluşturucu, derleme üzerinde bir anahtarla eşit anahtar olan tümleştirme testlerini içeren bir [Webapplicationfactorycontentrootattribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) arayarak uygulama [içeriği kök](xref:fundamentals/index#content-root) yolunu alır `TEntryPoint` `System.Reflection.Assembly.FullName` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-707">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="2a1e9-708">Doğru anahtara sahip bir özniteliğin bulunamaması durumunda, `WebApplicationFactory` bir çözüm dosyası (*. sln*) aramaya geri döner ve `TEntryPoint` derleme adını çözüm dizinine ekler.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-708">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="2a1e9-709">Uygulama kök dizini (içerik kök yolu) görünümleri ve içerik dosyalarını saptamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-709">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="2a1e9-710">Gölge kopyalamayı devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="2a1e9-710">Disable shadow copying</span></span>

<span data-ttu-id="2a1e9-711">Gölge kopyalama, testlerin çıkış dizininden farklı bir dizinde yürütülmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-711">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="2a1e9-712">Testlerin düzgün çalışması için gölge kopyalama devre dışı bırakılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-712">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="2a1e9-713">[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) xUnit kullanır ve doğru yapılandırma ayarıyla bir *xUnit. Runner. JSON* dosyası ekleyerek xUnit için gölge kopyalamayı devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-713">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="2a1e9-714">Daha fazla bilgi için bkz. [JSON Ile xUnit yapılandırma](https://xunit.github.io/docs/configuring-with-json.html).</span><span class="sxs-lookup"><span data-stu-id="2a1e9-714">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="2a1e9-715">Aşağıdaki içeriğe sahip test projesinin köküne *xUnit. Runner. JSON* dosyasını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-715">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="2a1e9-716">Visual Studio kullanıyorsanız, dosyanın **Çıkış Dizinine Kopyala** özelliğini **her zaman Kopyala**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-716">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="2a1e9-717">Visual Studio kullanmıyorsanız, `Content` Test uygulamasının proje dosyasına bir hedef ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-717">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="2a1e9-718">Nesnelerin elden çıkarılması</span><span class="sxs-lookup"><span data-stu-id="2a1e9-718">Disposal of objects</span></span>

<span data-ttu-id="2a1e9-719">`IClassFixture`Uygulamanın testleri yürütüldükten sonra, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) ve [HttpClient](/dotnet/api/system.net.http.httpclient) , [webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)'nin xUnit 'i çıkardığı zaman yürütülür.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-719">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="2a1e9-720">Geliştirici tarafından oluşturulan nesneler elden çıkarma gerektiriyorsa, bunları `IClassFixture` uygulamada atın.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-720">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="2a1e9-721">Daha fazla bilgi için bkz. [Dispose yöntemi uygulama](/dotnet/standard/garbage-collection/implementing-dispose).</span><span class="sxs-lookup"><span data-stu-id="2a1e9-721">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="2a1e9-722">Tümleştirme Testleri örneği</span><span class="sxs-lookup"><span data-stu-id="2a1e9-722">Integration tests sample</span></span>

<span data-ttu-id="2a1e9-723">[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) iki uygulamalardan oluşur:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-723">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="2a1e9-724">Uygulama</span><span class="sxs-lookup"><span data-stu-id="2a1e9-724">App</span></span> | <span data-ttu-id="2a1e9-725">Proje dizini</span><span class="sxs-lookup"><span data-stu-id="2a1e9-725">Project directory</span></span> | <span data-ttu-id="2a1e9-726">Açıklama</span><span class="sxs-lookup"><span data-stu-id="2a1e9-726">Description</span></span> |
| --- | ---
<span data-ttu-id="2a1e9-727">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-728">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-729">'Identity'</span></span>
- <span data-ttu-id="2a1e9-730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-730">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-731">'Razor'</span></span>
- <span data-ttu-id="2a1e9-732">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-733">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-734">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-735">'Identity'</span></span>
- <span data-ttu-id="2a1e9-736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-736">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-737">'Razor'</span></span>
- <span data-ttu-id="2a1e9-738">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-739">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-740">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-741">'Identity'</span></span>
- <span data-ttu-id="2a1e9-742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-742">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-743">'Razor'</span></span>
- <span data-ttu-id="2a1e9-744">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-745">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-746">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-747">'Identity'</span></span>
- <span data-ttu-id="2a1e9-748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-748">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-749">'Razor'</span></span>
- <span data-ttu-id="2a1e9-750">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-751">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-752">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-753">'Identity'</span></span>
- <span data-ttu-id="2a1e9-754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-754">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-755">'Razor'</span></span>
- <span data-ttu-id="2a1e9-756">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-757">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-758">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-759">'Identity'</span></span>
- <span data-ttu-id="2a1e9-760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-760">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-761">'Razor'</span></span>
- <span data-ttu-id="2a1e9-762">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-762">'SignalR' uid:</span></span> 

<span data-ttu-id="2a1e9-763">--------- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-763">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-764">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-765">'Identity'</span></span>
- <span data-ttu-id="2a1e9-766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-766">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-767">'Razor'</span></span>
- <span data-ttu-id="2a1e9-768">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-768">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-769">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-770">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-770">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-771">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-771">'Identity'</span></span>
- <span data-ttu-id="2a1e9-772">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-772">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-773">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-773">'Razor'</span></span>
- <span data-ttu-id="2a1e9-774">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-774">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-775">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-776">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-776">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-777">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-777">'Identity'</span></span>
- <span data-ttu-id="2a1e9-778">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-778">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-779">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-779">'Razor'</span></span>
- <span data-ttu-id="2a1e9-780">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-780">'SignalR' uid:</span></span> 

<span data-ttu-id="2a1e9-781">------ | | İleti uygulaması (SUT) | *src/RazorPagesProject* | Bir kullanıcının, iletileri eklemesini, silmesini, silmesini ve analiz etmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-781">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="2a1e9-782">| | Test uygulaması | *testler/RazorPagesProject. testler* | SUT test tümleştirmesi için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-782">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="2a1e9-783">Testler, [Visual Studio](https://visualstudio.microsoft.com)gıbı bir IDE 'nin yerleşik test özellikleri kullanılarak çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-783">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="2a1e9-784">[Visual Studio Code](https://code.visualstudio.com/) veya komut satırı kullanıyorsanız, *testler/RazorPagesProject. Tests* dizinindeki bir komut isteminde aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-784">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="2a1e9-785">İleti uygulaması (SUT) kuruluşu</span><span class="sxs-lookup"><span data-stu-id="2a1e9-785">Message app (SUT) organization</span></span>

<span data-ttu-id="2a1e9-786">SUT, Razor aşağıdaki özelliklere sahip bir sayfalar ileti sistemidir:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-786">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="2a1e9-787">Uygulamanın (*Pages/Index. cshtml* ve *Pages/index. cshtml. cs*) dizin sayfası, iletilerin eklenmesi, silinmesini ve ANALIZINI denetlemek için bir UI ve sayfa modeli yöntemleri sağlar (ileti başına ortalama sözcük).</span><span class="sxs-lookup"><span data-stu-id="2a1e9-787">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="2a1e9-788">Bir ileti, `Message` sınıfı (*Data/Message. cs*) ile iki özelliği olan ( `Id` anahtar) ve `Text` (ileti) açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-788">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="2a1e9-789">`Text`Özelliği gereklidir ve 200 karakterle sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-789">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="2a1e9-790">İletiler, [Entity Framework bellek içi veritabanı](/ef/core/providers/in-memory/)&#8224; kullanılarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-790">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="2a1e9-791">Uygulama, veritabanı bağlamı sınıfında ( `AppDbContext` *Data/AppDbContext. cs*) bir veri ERIŞIM katmanı (dal) içerir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-791">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="2a1e9-792">Veritabanı uygulama başlangıcında boşsa, ileti deposu üç iletiyle başlatılır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-792">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="2a1e9-793">Uygulama `/SecurePage` yalnızca kimliği doğrulanmış bir kullanıcı tarafından erişilebilen bir içerir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-793">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="2a1e9-794">EF konusunda, [InMemory Ile Test](/ef/core/miscellaneous/testing/in-memory)&#8224;, MSTest ile testler için bellek içi bir veritabanının nasıl kullanılacağını açıklar.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-794">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="2a1e9-795">Bu konu [xUnit](https://xunit.github.io/) test çerçevesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-795">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="2a1e9-796">Farklı test çerçeveleri genelinde test kavramları ve test uygulamaları benzerdir ancak aynı değildir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-796">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="2a1e9-797">Uygulama, depo desenini kullanmıyor ve [Iş birimi (UoW) düzeninin](https://martinfowler.com/eaaCatalog/unitOfWork.html)etkin bir örneği olmamasına karşın, Razor Sayfalar bu geliştirme düzenlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-797">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="2a1e9-798">Daha fazla bilgi için bkz. [altyapı Kalıcılık katmanını tasarlama](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) ve [Test denetleyicisi mantığı](/aspnet/core/mvc/controllers/testing) (örnek, depo modelini uygular).</span><span class="sxs-lookup"><span data-stu-id="2a1e9-798">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="2a1e9-799">Test uygulaması kuruluşu</span><span class="sxs-lookup"><span data-stu-id="2a1e9-799">Test app organization</span></span>

<span data-ttu-id="2a1e9-800">Test uygulaması, *testler/RazorPagesProject. Tests* dizini içindeki bir konsol uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-800">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="2a1e9-801">Uygulama dizinini test et</span><span class="sxs-lookup"><span data-stu-id="2a1e9-801">Test app directory</span></span> | <span data-ttu-id="2a1e9-802">Açıklama</span><span class="sxs-lookup"><span data-stu-id="2a1e9-802">Description</span></span> |
| ---
<span data-ttu-id="2a1e9-803">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-803">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-804">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-804">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-805">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-805">'Identity'</span></span>
- <span data-ttu-id="2a1e9-806">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-806">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-807">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-807">'Razor'</span></span>
- <span data-ttu-id="2a1e9-808">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-808">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-809">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-809">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-810">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-810">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-811">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-811">'Identity'</span></span>
- <span data-ttu-id="2a1e9-812">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-812">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-813">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-813">'Razor'</span></span>
- <span data-ttu-id="2a1e9-814">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-814">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-815">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-815">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-816">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-816">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-817">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-817">'Identity'</span></span>
- <span data-ttu-id="2a1e9-818">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-818">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-819">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-819">'Razor'</span></span>
- <span data-ttu-id="2a1e9-820">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-820">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-821">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-821">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-822">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-822">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-823">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-823">'Identity'</span></span>
- <span data-ttu-id="2a1e9-824">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-824">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-825">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-825">'Razor'</span></span>
- <span data-ttu-id="2a1e9-826">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-826">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-827">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-827">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-828">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-828">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-829">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-829">'Identity'</span></span>
- <span data-ttu-id="2a1e9-830">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-830">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-831">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-831">'Razor'</span></span>
- <span data-ttu-id="2a1e9-832">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-832">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-833">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-833">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-834">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-834">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-835">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-835">'Identity'</span></span>
- <span data-ttu-id="2a1e9-836">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-836">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-837">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-837">'Razor'</span></span>
- <span data-ttu-id="2a1e9-838">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-838">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-839">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-839">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-840">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-840">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-841">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-841">'Identity'</span></span>
- <span data-ttu-id="2a1e9-842">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-842">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-843">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-843">'Razor'</span></span>
- <span data-ttu-id="2a1e9-844">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-844">'SignalR' uid:</span></span> 

<span data-ttu-id="2a1e9-845">--------- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-845">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-846">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-846">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-847">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-847">'Identity'</span></span>
- <span data-ttu-id="2a1e9-848">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-848">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-849">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-849">'Razor'</span></span>
- <span data-ttu-id="2a1e9-850">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-850">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-851">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-851">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-852">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-852">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-853">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-853">'Identity'</span></span>
- <span data-ttu-id="2a1e9-854">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-854">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-855">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-855">'Razor'</span></span>
- <span data-ttu-id="2a1e9-856">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-856">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2a1e9-857">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-857">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2a1e9-858">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-858">'Blazor'</span></span>
- <span data-ttu-id="2a1e9-859">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-859">'Identity'</span></span>
- <span data-ttu-id="2a1e9-860">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-860">'Let's Encrypt'</span></span>
- <span data-ttu-id="2a1e9-861">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2a1e9-861">'Razor'</span></span>
- <span data-ttu-id="2a1e9-862">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-862">'SignalR' uid:</span></span> 

<span data-ttu-id="2a1e9-863">------ | | *Authtests* | İçin test yöntemleri içerir:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-863">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="2a1e9-864">Güvenli bir sayfaya, kimliği doğrulanmamış bir kullanıcıyla erişme.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-864">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="2a1e9-865">Bir sahte ile kimliği doğrulanmış bir kullanıcı tarafından güvenli bir sayfaya erişme <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-865">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="2a1e9-866">GitHub kullanıcı profilini alma ve profilin Kullanıcı oturum açma bilgilerini denetleme.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-866">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="2a1e9-867">| | *Basictests* | Yönlendirme ve içerik türü için bir test yöntemi içerir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-867">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="2a1e9-868">| | *Integrationtests* | Özel sınıf kullanan dizin sayfası için tümleştirme testlerini içerir `WebApplicationFactory` .</span><span class="sxs-lookup"><span data-stu-id="2a1e9-868">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="2a1e9-869">| | *Yardımcılar/yardımcı programlar* | </span><span class="sxs-lookup"><span data-stu-id="2a1e9-869">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="2a1e9-870">*Utilities.cs* , `InitializeDbForTests` veritabanını test verileriyle tohum için kullanılan yöntemi içerir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-870">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="2a1e9-871">*HtmlHelpers.cs* `IHtmlDocument` , test yöntemleri tarafından kullanılmak üzere anglesharp döndüren bir yöntem sağlar.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-871">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="2a1e9-872">*HttpClientExtensions.cs* `SendAsync` , istekleri sut 'a göndermek için için aşırı yüklemeler sağlar.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-872">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="2a1e9-873">Test çerçevesi [xUnit](https://xunit.github.io/)' dir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-873">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="2a1e9-874">Tümleştirme testleri, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)içeren [Microsoft. Aspnetcore. testhost](/dotnet/api/microsoft.aspnetcore.testhost)kullanılarak yürütülür.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-874">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="2a1e9-875">Test ana bilgisayarı ve test sunucusunu yapılandırmak için [Microsoft. AspNetCore. Mvc. Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) paketi kullanıldığından, ve paketleri test uygulamasındaki `TestHost` `TestServer` Proje dosyasında veya geliştirici yapılandırmasında doğrudan paket başvuruları gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-875">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="2a1e9-876">**Test için veritabanının temelini sağlama**</span><span class="sxs-lookup"><span data-stu-id="2a1e9-876">**Seeding the database for testing**</span></span>

<span data-ttu-id="2a1e9-877">Tümleştirme testleri genellikle veritabanında test yürütmeden önce küçük bir veri kümesi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-877">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="2a1e9-878">Örneğin, veritabanı kaydı silme için bir silme testi çağrılarında, silme isteğinin başarılı olması için veritabanının en az bir kaydı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2a1e9-878">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="2a1e9-879">Örnek uygulama, *Utilities.cs* içinde testlerin, yürütme sırasında kullanabileceği üç iletiyle birlikte veritabanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="2a1e9-879">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="2a1e9-880">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2a1e9-880">Additional resources</span></span>

* [<span data-ttu-id="2a1e9-881">Birim testleri</span><span class="sxs-lookup"><span data-stu-id="2a1e9-881">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
