---
title: ASP.NET Core 'de test bileşenleri Blazor
author: guardrex
description: Uygulamalarda bileşen oluşturmayı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/10/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/test
ms.openlocfilehash: 30c5ead98c5da934c1e76577c5dc1a39c7224a79
ms.sourcegitcommit: 4df445e7d49a99f81625430f728c28e5d6bf2107
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88253729"
---
# <a name="test-components-in-aspnet-core-no-locblazor"></a><span data-ttu-id="a016e-103">ASP.NET Core 'de test bileşenleri Blazor</span><span class="sxs-lookup"><span data-stu-id="a016e-103">Test components in ASP.NET Core Blazor</span></span>

[<span data-ttu-id="a016e-104">Yumurıl Hansen</span><span class="sxs-lookup"><span data-stu-id="a016e-104">Egil Hansen</span></span>](https://egilhansen.com/)

<span data-ttu-id="a016e-105">Test, kararlı ve sürdürülebilir yazılım oluşturmanın önemli bir yönüdür.</span><span class="sxs-lookup"><span data-stu-id="a016e-105">Testing is an important aspect of building stable and maintainable software.</span></span>

<span data-ttu-id="a016e-106">Bir bileşeni test etmek için Blazor , test (kes) *altındaki bileşen* :</span><span class="sxs-lookup"><span data-stu-id="a016e-106">To test a Blazor component, the *Component Under Test* (CUT) is:</span></span>

* <span data-ttu-id="a016e-107">Test için ilgili girişle birlikte işlenir.</span><span class="sxs-lookup"><span data-stu-id="a016e-107">Rendered with relevant input for the test.</span></span>
* <span data-ttu-id="a016e-108">Gerçekleştirilen test türüne bağlı olarak etkileşime veya değişikliğe tabi olabilir.</span><span class="sxs-lookup"><span data-stu-id="a016e-108">Depending on the type of test performed, possibly subject to interaction or modification.</span></span> <span data-ttu-id="a016e-109">Örneğin, `onclick` bir düğme için olay gibi olay işleyicileri tetiklenebilir.</span><span class="sxs-lookup"><span data-stu-id="a016e-109">For example, event handlers can be triggered, such as an `onclick` event for a button.</span></span>
* <span data-ttu-id="a016e-110">Beklenen değerler için inceledi.</span><span class="sxs-lookup"><span data-stu-id="a016e-110">Inspected for expected values.</span></span>

## <a name="test-approaches"></a><span data-ttu-id="a016e-111">Test yaklaşımları</span><span class="sxs-lookup"><span data-stu-id="a016e-111">Test approaches</span></span>

<span data-ttu-id="a016e-112">Bileşenleri test etmek için iki yaygın yaklaşım Blazor , uçtan uca (e2e) test ve birim sınamalardır:</span><span class="sxs-lookup"><span data-stu-id="a016e-112">Two common approaches for testing Blazor components are end-to-end (E2E) testing and unit testing:</span></span>

* <span data-ttu-id="a016e-113">**Birim testi**: [birim testleri](/dotnet/core/testing/) şunları sağlayan bir birim testi kitaplığıyla yazılır:</span><span class="sxs-lookup"><span data-stu-id="a016e-113">**Unit testing**: [Unit tests](/dotnet/core/testing/) are written with a unit testing library that provides:</span></span>
  * <span data-ttu-id="a016e-114">Bileşen işleme.</span><span class="sxs-lookup"><span data-stu-id="a016e-114">Component rendering.</span></span>
  * <span data-ttu-id="a016e-115">Bileşen çıkışının ve durumunun incelemesi.</span><span class="sxs-lookup"><span data-stu-id="a016e-115">Inspection of component output and state.</span></span>
  * <span data-ttu-id="a016e-116">Olay işleyicileri ve yaşam döngüsü yöntemlerinin tetiklenmesi.</span><span class="sxs-lookup"><span data-stu-id="a016e-116">Triggering of event handlers and life cycle methods.</span></span>
  * <span data-ttu-id="a016e-117">Bileşen davranışının doğru olduğu Onaylamalar.</span><span class="sxs-lookup"><span data-stu-id="a016e-117">Assertions that component behavior is correct.</span></span>

  <span data-ttu-id="a016e-118">[bunbu](https://github.com/egil/bUnit) , bileşen birim testini sağlayan bir kitaplık örneğidir Razor .</span><span class="sxs-lookup"><span data-stu-id="a016e-118">[bUnit](https://github.com/egil/bUnit) is an example of a library that enables Razor component unit testing.</span></span>

* <span data-ttu-id="a016e-119">**E2e**testi: Test Çalıştırıcısı Blazor , BIR tarayıcı örneğini kesme ve otomatikleştirir olarak içeren bir uygulamayı çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="a016e-119">**E2E testing**: A test runner runs a Blazor app containing the CUT and automates a browser instance.</span></span> <span data-ttu-id="a016e-120">Test aracı tarayıcı aracılığıyla göz atma ve bunlarla etkileşim kurma.</span><span class="sxs-lookup"><span data-stu-id="a016e-120">The testing tool inspects and interacts with the CUT through the browser.</span></span> <span data-ttu-id="a016e-121">[Selenium](https://github.com/SeleniumHQ/selenium) , uygulamalarla KULLANıLABILECEK bir e2e test çerçevesinin örneğidir Blazor .</span><span class="sxs-lookup"><span data-stu-id="a016e-121">[Selenium](https://github.com/SeleniumHQ/selenium) is an example of an E2E testing framework that can be used with Blazor apps.</span></span>

<span data-ttu-id="a016e-122">Birim testinde yalnızca Blazor bileşen ( Razor /c #) dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="a016e-122">In unit testing, only the Blazor component (Razor/C#) is involved.</span></span> <span data-ttu-id="a016e-123">Hizmetler ve JS birlikte çalışma gibi dış bağımlılıklar, moclanmış olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a016e-123">External dependencies, such as services and JS interop, must be mocked.</span></span> <span data-ttu-id="a016e-124">E2E testinde, Blazor bileşeni ve tüm yardımcı altyapısı, CSS, js ve DOM ve tarayıcı API 'leri dahil olmak üzere testin bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="a016e-124">In E2E testing, the Blazor component and all of it's auxiliary infrastructure are part of the test, including CSS, JS, and the DOM and browser APIs.</span></span>

<span data-ttu-id="a016e-125">*Test kapsamı* , testlerin ne kadar geniş olduğunu tanımlar.</span><span class="sxs-lookup"><span data-stu-id="a016e-125">*Test scope* describes how extensive the tests are.</span></span> <span data-ttu-id="a016e-126">Test kapsamı genellikle testlerin hızını etkiler.</span><span class="sxs-lookup"><span data-stu-id="a016e-126">Test scope typically has an influence on the speed of the tests.</span></span> <span data-ttu-id="a016e-127">Birim testleri, uygulama alt sistemlerinin bir alt kümesinde çalışır ve genellikle milisaniye cinsinden yürütülür.</span><span class="sxs-lookup"><span data-stu-id="a016e-127">Unit tests run on a subset of the app's subsystems and usually execute in milliseconds.</span></span> <span data-ttu-id="a016e-128">Uygulama alt sistemlerinin geniş bir grubunu test eden E2E testleri, tamamlanması birkaç saniye sürebilir.</span><span class="sxs-lookup"><span data-stu-id="a016e-128">E2E tests, which test a broad group of the app's subsystems, can take several seconds to complete.</span></span> 

<span data-ttu-id="a016e-129">Birim testi Ayrıca, kes örneğine erişim sağlar ve bileşenin iç durumunun incelemesini ve doğrulanması için izin verir.</span><span class="sxs-lookup"><span data-stu-id="a016e-129">Unit testing also provides access to the instance of the CUT, allowing for inspection and verification of the component's internal state.</span></span> <span data-ttu-id="a016e-130">Bu, normal olarak E2E testinde mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="a016e-130">This normally isn't possible in E2E testing.</span></span>

<span data-ttu-id="a016e-131">Bileşenin ortamıyla ilgili olarak, E2E testlerin, doğrulama başlamadan önce beklenen çevresel duruma ulaşıldığından emin olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="a016e-131">With regard to the component's environment, E2E tests must make sure that the expected environmental state has been reached before verification starts.</span></span> <span data-ttu-id="a016e-132">Aksi takdirde, sonuç tahmin edilemez.</span><span class="sxs-lookup"><span data-stu-id="a016e-132">Otherwise, the result is unpredictable.</span></span> <span data-ttu-id="a016e-133">Birim testinde, testin kesme ve yaşam döngüsünün işlenmesi daha tümleşiktir ve bu da test kararlılığını geliştirir.</span><span class="sxs-lookup"><span data-stu-id="a016e-133">In unit testing, the rendering of the CUT and the life cycle of the test are more integrated, which improves test stability.</span></span>

<span data-ttu-id="a016e-134">E2E testi, genellikle zayıf test güvenilirliğini sağlayan birden çok işlem, ağ ve disk g/ç ve diğer alt sistem etkinliklerini başlatmayı içerir.</span><span class="sxs-lookup"><span data-stu-id="a016e-134">E2E testing involves launching multiple processes, network and disk I/O, and other subsystem activity that often lead to poor test reliability.</span></span> <span data-ttu-id="a016e-135">Birim testleri, genellikle bu tür sorunlardan yalıtımlı.</span><span class="sxs-lookup"><span data-stu-id="a016e-135">Unit tests are typically insulated from these sorts of issues.</span></span>

<span data-ttu-id="a016e-136">Aşağıdaki tabloda iki test yaklaşımının farkı özetlenmektedir.</span><span class="sxs-lookup"><span data-stu-id="a016e-136">The following table summarizes the difference between the two testing approaches.</span></span>

| <span data-ttu-id="a016e-137">Özellik</span><span class="sxs-lookup"><span data-stu-id="a016e-137">Capability</span></span>                       | <span data-ttu-id="a016e-138">Birim testi</span><span class="sxs-lookup"><span data-stu-id="a016e-138">Unit testing</span></span>                     | <span data-ttu-id="a016e-139">E2E testi</span><span class="sxs-lookup"><span data-stu-id="a016e-139">E2E testing</span></span>                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| <span data-ttu-id="a016e-140">Test kapsamı</span><span class="sxs-lookup"><span data-stu-id="a016e-140">Test scope</span></span>                       | <span data-ttu-id="a016e-141">Blazor yalnızca bileşen ( Razor /c #)</span><span class="sxs-lookup"><span data-stu-id="a016e-141">Blazor component (Razor/C#) only</span></span> | <span data-ttu-id="a016e-142">BlazorRazorCSS/JS ile bileşen (/c #)</span><span class="sxs-lookup"><span data-stu-id="a016e-142">Blazor component (Razor/C#) with CSS/JS</span></span> |
| <span data-ttu-id="a016e-143">Test yürütme süresi</span><span class="sxs-lookup"><span data-stu-id="a016e-143">Test execution time</span></span>              | <span data-ttu-id="a016e-144">Mayacak</span><span class="sxs-lookup"><span data-stu-id="a016e-144">Milliseconds</span></span>                     | <span data-ttu-id="a016e-145">Saniye</span><span class="sxs-lookup"><span data-stu-id="a016e-145">Seconds</span></span>                                 |
| <span data-ttu-id="a016e-146">Bileşen örneğine erişim</span><span class="sxs-lookup"><span data-stu-id="a016e-146">Access to the component instance</span></span> | <span data-ttu-id="a016e-147">Evet</span><span class="sxs-lookup"><span data-stu-id="a016e-147">Yes</span></span>                              | <span data-ttu-id="a016e-148">Hayır</span><span class="sxs-lookup"><span data-stu-id="a016e-148">No</span></span>                                      |
| <span data-ttu-id="a016e-149">Ortamla duyarlı</span><span class="sxs-lookup"><span data-stu-id="a016e-149">Sensitive to the environment</span></span>     | <span data-ttu-id="a016e-150">Hayır</span><span class="sxs-lookup"><span data-stu-id="a016e-150">No</span></span>                               | <span data-ttu-id="a016e-151">Evet</span><span class="sxs-lookup"><span data-stu-id="a016e-151">Yes</span></span>                                     |
| <span data-ttu-id="a016e-152">Güvenilirlik</span><span class="sxs-lookup"><span data-stu-id="a016e-152">Reliability</span></span>                      | <span data-ttu-id="a016e-153">Daha güvenilir</span><span class="sxs-lookup"><span data-stu-id="a016e-153">More reliable</span></span>                    | <span data-ttu-id="a016e-154">Daha az güvenilir</span><span class="sxs-lookup"><span data-stu-id="a016e-154">Less reliable</span></span>                           |

## <a name="choose-the-most-appropriate-test-approach"></a><span data-ttu-id="a016e-155">En uygun test yaklaşımını seçin</span><span class="sxs-lookup"><span data-stu-id="a016e-155">Choose the most appropriate test approach</span></span>

<span data-ttu-id="a016e-156">Gerçekleştirilecek test türünü seçerken senaryoyu göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="a016e-156">Consider the scenario when choosing the type of testing to perform.</span></span> <span data-ttu-id="a016e-157">Aşağıdaki tabloda bazı konular açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="a016e-157">Some considerations are described in the following table.</span></span>

| <span data-ttu-id="a016e-158">Senaryo</span><span class="sxs-lookup"><span data-stu-id="a016e-158">Scenario</span></span> | <span data-ttu-id="a016e-159">Önerilen yaklaşım</span><span class="sxs-lookup"><span data-stu-id="a016e-159">Suggested approach</span></span> | <span data-ttu-id="a016e-160">Açıklamalar</span><span class="sxs-lookup"><span data-stu-id="a016e-160">Remarks</span></span> |
| -------- | ------------------ | ------- |
| <span data-ttu-id="a016e-161">JS birlikte çalışma mantığı olmayan bileşen</span><span class="sxs-lookup"><span data-stu-id="a016e-161">Component without JS interop logic</span></span> | <span data-ttu-id="a016e-162">Birim testi</span><span class="sxs-lookup"><span data-stu-id="a016e-162">Unit testing</span></span> | <span data-ttu-id="a016e-163">Bir bileşende JS birlikte çalışabilirliğine bağımlılık olmadığında Blazor , BILEŞEN js veya DOM API 'sine erişim olmadan test edilebilir.</span><span class="sxs-lookup"><span data-stu-id="a016e-163">When there's no dependency on JS interop in a Blazor component, the component can be tested without access to JS or the DOM API.</span></span> <span data-ttu-id="a016e-164">Bu senaryoda birim testi seçmenin olumsuz yönleri yoktur.</span><span class="sxs-lookup"><span data-stu-id="a016e-164">In this scenario, there are no disadvantages to choosing unit testing.</span></span> |
| <span data-ttu-id="a016e-165">Basit JS birlikte çalışma mantığını içeren bileşen</span><span class="sxs-lookup"><span data-stu-id="a016e-165">Component with simple JS interop logic</span></span> | <span data-ttu-id="a016e-166">Birim testi</span><span class="sxs-lookup"><span data-stu-id="a016e-166">Unit testing</span></span> | <span data-ttu-id="a016e-167">Bileşenler, DOM 'yi sorgulamak veya JS birlikte çalışma yoluyla animasyonları tetiklemek için ortaktır.</span><span class="sxs-lookup"><span data-stu-id="a016e-167">It's common for components to query the DOM or trigger animations through JS interop.</span></span> <span data-ttu-id="a016e-168">Birim testi genellikle bu senaryoda tercih edilir, çünkü arabirim aracılığıyla JS etkileşimini yalındır <xref:Microsoft.JSInterop.IJSRuntime> .</span><span class="sxs-lookup"><span data-stu-id="a016e-168">Unit testing is usually preferred in this scenario, since it's straightforward to mock the JS interaction through the <xref:Microsoft.JSInterop.IJSRuntime> interface.</span></span> |
| <span data-ttu-id="a016e-169">Karmaşık JS koduna bağlı bileşen</span><span class="sxs-lookup"><span data-stu-id="a016e-169">Component that depends on complex JS code</span></span> | <span data-ttu-id="a016e-170">Birim testi ve ayrı JS testi</span><span class="sxs-lookup"><span data-stu-id="a016e-170">Unit testing and separate JS testing</span></span> | <span data-ttu-id="a016e-171">Bir bileşen büyük veya karmaşık bir JS kitaplığı çağırmak için JS birlikte çalışabilirliği kullanıyorsa ancak Blazor bileşen ve js kitaplığı arasındaki etkileşim basittir, en iyi yaklaşım, bileşen ve js kitaplığı veya kodu iki ayrı parça olarak değerlendirmek ve her birini test etmek olabilir.</span><span class="sxs-lookup"><span data-stu-id="a016e-171">If a component uses JS interop to call a large or complex JS library but the interaction between the Blazor component and JS library is simple, then the best approach is likely to treat the component and JS library or code as two separate parts and test each individually.</span></span> <span data-ttu-id="a016e-172">BlazorBir birim testi kitaplığıyla bileşeni test edin ve JS 'yi BIR js test kitaplığı ile test edin.</span><span class="sxs-lookup"><span data-stu-id="a016e-172">Test the Blazor component with a unit testing library, and test the JS with a JS testing library.</span></span> |
| <span data-ttu-id="a016e-173">Bir mantığı olan ve tarayıcı DOM 'ın JS işlemesini bağımlı bileşen</span><span class="sxs-lookup"><span data-stu-id="a016e-173">Component with logic that depends on JS manipulation of the browser DOM</span></span> | <span data-ttu-id="a016e-174">E2E testi</span><span class="sxs-lookup"><span data-stu-id="a016e-174">E2E testing</span></span> | <span data-ttu-id="a016e-175">Bir bileşenin işlevselliği JS 'ye bağımlıysa ve DOM üzerinde değişiklik yapıldığında, E2E testinde hem JS hem de Code 'u Blazor birlikte doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="a016e-175">When a component's functionality is dependent on JS and its manipulation of the DOM, verify both the JS and Blazor code together in an E2E test.</span></span> <span data-ttu-id="a016e-176">Bu, Blazor çerçeve geliştiricilerinin, Blazor sıkı şekilde bağlanmış C# ve js kodu bulunan tarayıcı işleme mantığı ile aldığı yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="a016e-176">This is the approach that the Blazor framework developers have taken with Blazor's browser rendering logic, which has tightly-coupled C# and JS code.</span></span> <span data-ttu-id="a016e-177">Bileşenleri bir tarayıcıda doğru şekilde işlemek için C# ve JS kodunun birlikte çalışması gerekir Blazor .</span><span class="sxs-lookup"><span data-stu-id="a016e-177">The C# and JS code must work together to correctly render Blazor components in a browser.</span></span>
| <span data-ttu-id="a016e-178">Sabit-sahte bağımlılıklarla 3. taraf bileşen kitaplığına bağlı olan bileşen</span><span class="sxs-lookup"><span data-stu-id="a016e-178">Component that depends on 3rd party component library with hard-to-mock dependencies</span></span> | <span data-ttu-id="a016e-179">E2E testi</span><span class="sxs-lookup"><span data-stu-id="a016e-179">E2E testing</span></span> | <span data-ttu-id="a016e-180">Bir bileşenin işlevselliği, JS birlikte çalışma gibi, bir üçüncü taraf bileşen kitaplığına bağımlıysa, E2E testi, bileşeni test etmeye yönelik tek seçenek olabilir.</span><span class="sxs-lookup"><span data-stu-id="a016e-180">When a component's functionality is dependent on a 3rd party component library that has hard-to-mock dependencies, such as JS interop, E2E testing might be the only option to test the component.</span></span> |

## <a name="test-components-with-bunit"></a><span data-ttu-id="a016e-181">BUnit ile bileşenleri test etme</span><span class="sxs-lookup"><span data-stu-id="a016e-181">Test components with bUnit</span></span>

<span data-ttu-id="a016e-182">İçin resmi Microsoft Test çerçevesi yoktur Blazor , ancak topluluk odaklı proje, birim test bileşenlerine [bUnit](https://github.com/egil/bUnit) kolay bir yol sağlar Blazor .</span><span class="sxs-lookup"><span data-stu-id="a016e-182">There's no official Microsoft testing framework for Blazor, but the community-driven project [bUnit](https://github.com/egil/bUnit) provides a convenient way to unit test Blazor components.</span></span>

> [!NOTE]
> <span data-ttu-id="a016e-183">Bu, üçüncü taraf bir test Kitaplığı olduğundan Microsoft tarafından desteklenmez veya korunmaz.</span><span class="sxs-lookup"><span data-stu-id="a016e-183">bUnit is a third-party testing library and isn't supported or maintained by Microsoft.</span></span>

<span data-ttu-id="a016e-184">Bunbu, [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/)ve [xUnit](https://xunit.github.io/)gibi genel amaçlı test çerçeveleri ile birlikte çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="a016e-184">bUnit works with general-purpose testing frameworks, such as [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/), and [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="a016e-185">Bu test çerçeveleri, Bunıt testlerini normal birim testleri gibi görünür hale getirir.</span><span class="sxs-lookup"><span data-stu-id="a016e-185">These testing frameworks make bUnit tests look and feel like regular unit tests.</span></span> <span data-ttu-id="a016e-186">Genel amaçlı bir test çerçevesiyle tümleştirilmiş, normalde şu şekilde yürütülen testler:</span><span class="sxs-lookup"><span data-stu-id="a016e-186">bUnit tests integrated with a general-purpose testing framework are ordinarily executed with:</span></span>

* <span data-ttu-id="a016e-187">[Visual Studio 'Nun test Gezgini](/visualstudio/test/run-unit-tests-with-test-explorer).</span><span class="sxs-lookup"><span data-stu-id="a016e-187">[Visual Studio's Test Explorer](/visualstudio/test/run-unit-tests-with-test-explorer).</span></span>
* <span data-ttu-id="a016e-188">[`dotnet test`](/dotnet/core/tools/dotnet-test) Bir komut kabuğunda CLı komutu.</span><span class="sxs-lookup"><span data-stu-id="a016e-188">[`dotnet test`](/dotnet/core/tools/dotnet-test) CLI command in a command shell.</span></span>
* <span data-ttu-id="a016e-189">Otomatikleştirilmiş bir DevOps test işlem hattı.</span><span class="sxs-lookup"><span data-stu-id="a016e-189">An automated DevOps testing pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="a016e-190">Farklı test çerçeveleri genelinde test kavramları ve test uygulamaları benzerdir ancak aynı değildir.</span><span class="sxs-lookup"><span data-stu-id="a016e-190">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span> <span data-ttu-id="a016e-191">Rehberlik için test çerçevesinin belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="a016e-191">Refer to the test framework's documentation for guidance.</span></span>

<span data-ttu-id="a016e-192">Aşağıda, bir proje şablonunu temel alan bir uygulamadaki bileşen üzerindeki bir bUnit testinin yapısı gösterilmektedir `Counter` Blazor .</span><span class="sxs-lookup"><span data-stu-id="a016e-192">The following demonstrates the structure of a bUnit test on the `Counter` component in an app based on a Blazor project template.</span></span> <span data-ttu-id="a016e-193">`Counter`Bileşen, sayfadaki bir düğmeyi seçerek kullanıcıya göre bir sayacı görüntüler ve arttırır:</span><span class="sxs-lookup"><span data-stu-id="a016e-193">The `Counter` component displays and increments a counter based on the user selecting a button in the page:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="a016e-194">Aşağıdaki BIT testi, düğme seçildiğinde KESILEN sayacın doğru şekilde arttırıldığının doğrular:</span><span class="sxs-lookup"><span data-stu-id="a016e-194">The following bUnit test verifies that the CUT's counter is incremented correctly when the button is selected:</span></span>

```csharp
[Fact]
public void CounterShouldIncrementWhenSelected()
{
    // Arrange
    using var cxt = new TestContext();
    var cut = ctx.RenderComponent<Counter>();
    var paraElm = cut.Find("p");

    // Act
    cut.Find("button").Click();
    var paraElmText = paraElm.TextContent;

    // Assert
    paraElmText.MarkupMatches("Current count: 1");
}
```

<span data-ttu-id="a016e-195">Testin her adımında aşağıdaki eylemler gerçekleşir:</span><span class="sxs-lookup"><span data-stu-id="a016e-195">The following actions take place at each step of the test:</span></span>

* <span data-ttu-id="a016e-196">*Düzenle*: `Counter` Bileşen, bunit ' i kullanılarak işlenir `TestContext` .</span><span class="sxs-lookup"><span data-stu-id="a016e-196">*Arrange*: The `Counter` component is rendered using bUnit's `TestContext`.</span></span> <span data-ttu-id="a016e-197">KESILEN paragraf öğesi ( `<p>` ) bulunur ve öğesine atanır `paraElm` .</span><span class="sxs-lookup"><span data-stu-id="a016e-197">The CUT's paragraph element (`<p>`) is found and assigned to `paraElm`.</span></span>

* <span data-ttu-id="a016e-198">*Sahne*: düğmenin öğesi ( `<button>` ) bulunur ve sonra öğesini çağırarak seçilir ve bu da `Click` sayacı artırmanız ve paragraf etiketinin () içeriğini güncelleştirmelidir `<p>` .</span><span class="sxs-lookup"><span data-stu-id="a016e-198">*Act*: The button's element (`<button>`) is located and then selected by calling `Click`, which should increment the counter and update the content of the paragraph tag (`<p>`).</span></span> <span data-ttu-id="a016e-199">Paragraf öğesi metin içeriği çağırarak elde edilir `TextContent` .</span><span class="sxs-lookup"><span data-stu-id="a016e-199">The paragraph element text content is obtained by calling `TextContent`.</span></span>

* <span data-ttu-id="a016e-200">*Assert*Onay: `MarkupMatches` metin içeriğinde, bir olan beklenen dizeyle eşleştiğini doğrulamak için çağrılır `Current count: 1` .</span><span class="sxs-lookup"><span data-stu-id="a016e-200">*Assert*: `MarkupMatches` is called on the text content to verify that it matches the expected string, which is `Current count: 1`.</span></span>

> [!NOTE]
> <span data-ttu-id="a016e-201">`MarkupMatches`Onaylama yöntemi bir normal dize karşılaştırma onaylamadan farklıdır (örneğin, `Assert.Equal("Current count: 1", paraElmText);` ) `MarkupMatches` girişin anlam KARŞıLAŞTıRMASıNı ve beklenen HTML işaretlemesini gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="a016e-201">The `MarkupMatches` assert method differs from a regular string comparison assertion (for example, `Assert.Equal("Current count: 1", paraElmText);`) `MarkupMatches` performs a semantic comparison of the input and expected HTML markup.</span></span> <span data-ttu-id="a016e-202">Anlamsal bir karşılaştırma, HTML semantiğinin farkındadır, yani çok önemli boşluk gibi şeyler yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="a016e-202">A semantic comparison is aware of HTML semantics, meaning things like insignificant whitespace is ignored.</span></span> <span data-ttu-id="a016e-203">Bu, daha kararlı testlere neden olur.</span><span class="sxs-lookup"><span data-stu-id="a016e-203">This results in more stable tests.</span></span> <span data-ttu-id="a016e-204">Daha fazla bilgi için bkz. [anlam HTML karşılaştırmasını özelleştirme](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).</span><span class="sxs-lookup"><span data-stu-id="a016e-204">For more information, see [Customizing the Semantic HTML Comparison](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a016e-205">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a016e-205">Additional resources</span></span>

* <span data-ttu-id="a016e-206">[Bunıt Ile çalışmaya](https://bunit.egilhansen.com/docs/getting-started/)başlama: bunıt yönergeleri test projesi oluşturma, test çerçevesi paketlerine başvurma ve Testleri oluşturma ve çalıştırma hakkında rehberlik içerir.</span><span class="sxs-lookup"><span data-stu-id="a016e-206">[Getting Started with bUnit](https://bunit.egilhansen.com/docs/getting-started/): bUnit instructions include guidance on creating a test project, referencing testing framework packages, and building and running tests.</span></span>
