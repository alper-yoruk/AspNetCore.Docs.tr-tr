---
title: ASP.NET MVC 'den ASP.NET Core MVC 'ye geçirmeyi öğrenin
author: wadepickett
description: ASP.NET MVC projesini ASP.NET Core MVC 'ye geçirmeye nasıl başlayacağınızı öğrenin.
ms.author: wpickett
ms.date: 06/18/2020
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
uid: migration/mvc
ms.openlocfilehash: 226ac6da508378c7b3c81779d38dd2e0840f1fed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051519"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="873af-103">ASP.NET MVC 'den ASP.NET Core MVC 'ye geçiş</span><span class="sxs-lookup"><span data-stu-id="873af-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="873af-104">Bu makalede, ASP.NET MVC projesini [ASP.NET Core MVC](xref:mvc/overview)'ye geçirmeye nasıl başlayayapılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="873af-104">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview).</span></span> <span data-ttu-id="873af-105">Sürecinde, ASP.NET MVC 'den ilgili değişiklikleri vurgular.</span><span class="sxs-lookup"><span data-stu-id="873af-105">In the process, it highlights related changes from ASP.NET MVC.</span></span>

<span data-ttu-id="873af-106">ASP.NET MVC 'den geçiş çok adımlı bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="873af-106">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="873af-107">Bu makalede şunları ele alınmaktadır:</span><span class="sxs-lookup"><span data-stu-id="873af-107">This article covers:</span></span>

* <span data-ttu-id="873af-108">İlk kurulum.</span><span class="sxs-lookup"><span data-stu-id="873af-108">Initial setup.</span></span>
* <span data-ttu-id="873af-109">Temel denetleyiciler ve görünümler.</span><span class="sxs-lookup"><span data-stu-id="873af-109">Basic controllers and views.</span></span>
* <span data-ttu-id="873af-110">Statik içerik.</span><span class="sxs-lookup"><span data-stu-id="873af-110">Static content.</span></span>
* <span data-ttu-id="873af-111">İstemci tarafı bağımlılıkları.</span><span class="sxs-lookup"><span data-stu-id="873af-111">Client-side dependencies.</span></span>

<span data-ttu-id="873af-112">Yapılandırma ve kodu geçirmek için :::no-loc(Identity)::: bkz. [yapılandırmayı ASP.NET Core](xref:migration/configuration) ve [geçiş kimlik doğrulaması ve :::no-loc(Identity)::: ASP.NET Core geçirme](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="873af-112">For migrating configuration and :::no-loc(Identity)::: code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core](xref:migration/identity).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="873af-113">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="873af-113">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="873af-114">Başlatıcı ASP.NET MVC projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="873af-114">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="873af-115">Geçirmek için Visual Studio 'da örnek bir ASP.NET MVC projesi oluşturun:</span><span class="sxs-lookup"><span data-stu-id="873af-115">Create an example ASP.NET MVC project in Visual Studio to migrate:</span></span>

1. <span data-ttu-id="873af-116">**Dosya** menüsünden **Yeni** > **Proje** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="873af-116">From the **File** menu, select **New** > **Project** .</span></span>
1. <span data-ttu-id="873af-117">**ASP.NET Web uygulaması (.NET Framework)** öğesini seçin ve ardından **İleri** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="873af-117">Select **ASP.NET Web Application (.NET Framework)** and then select **Next** .</span></span>
1. <span data-ttu-id="873af-118">Ad alanı, bir sonraki adımda oluşturulan ASP.NET Core projesi ile eşleşen proje *WebApp1* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="873af-118">Name the project *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span> <span data-ttu-id="873af-119">**Oluştur** ’u seçin.</span><span class="sxs-lookup"><span data-stu-id="873af-119">Select **Create** .</span></span>
1. <span data-ttu-id="873af-120">**MVC** ' yi ve ardından **Oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="873af-120">Select **MVC** , and then select **Create** .</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="873af-121">ASP.NET Core projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="873af-121">Create the ASP.NET Core project</span></span>

<span data-ttu-id="873af-122">Geçirilecek yeni bir ASP.NET Core projesi ile yeni bir çözüm oluşturun:</span><span class="sxs-lookup"><span data-stu-id="873af-122">Create a new solution with a new ASP.NET Core project to migrate to:</span></span>

1. <span data-ttu-id="873af-123">Visual Studio 'nun ikinci bir örneğini başlatın.</span><span class="sxs-lookup"><span data-stu-id="873af-123">Launch a second instance of Visual Studio.</span></span>
1. <span data-ttu-id="873af-124">**Dosya** menüsünden **Yeni** > **Proje** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="873af-124">From the **File** menu, select **New** > **Project** .</span></span>
1. <span data-ttu-id="873af-125">**ASP.NET Core Web uygulaması** ' nı seçin ve ardından **İleri** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="873af-125">Select **ASP.NET Core Web Application** and then select **Next** .</span></span>
1. <span data-ttu-id="873af-126">**Yeni projenizi yapılandırın** iletişim kutusunda projeyi *WebApp1* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="873af-126">In the **Configure your new project** dialog, Name the project *WebApp1* .</span></span>
1. <span data-ttu-id="873af-127">Aynı proje adını kullanmak için konumu önceki projeden farklı bir dizine ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="873af-127">Set the location to a different directory than the previous project to use the same project name.</span></span> <span data-ttu-id="873af-128">Aynı ad alanının kullanılması, kodu iki proje arasında kopyalamayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="873af-128">Using the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="873af-129">**Oluştur** ’u seçin.</span><span class="sxs-lookup"><span data-stu-id="873af-129">Select **Create** .</span></span>
1. <span data-ttu-id="873af-130">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 3,1** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="873af-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="873af-131">**Web uygulaması (Model-View-Controller)** proje şablonunu seçin ve **Oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="873af-131">Select the **Web Application (Model-View-Controller)** project template, and select **Create** .</span></span>

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a><span data-ttu-id="873af-132">ASP.NET Core sitesini MVC kullanacak şekilde yapılandırma</span><span class="sxs-lookup"><span data-stu-id="873af-132">Configure the ASP.NET Core site to use MVC</span></span>

<span data-ttu-id="873af-133">ASP.NET Core 3,0 ve sonraki projelerde .NET Framework artık desteklenen bir hedef çerçeve değildir.</span><span class="sxs-lookup"><span data-stu-id="873af-133">In ASP.NET Core 3.0 and later projects, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="873af-134">Projenizin .NET Core 'ı hedeflemesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="873af-134">Your project must target .NET Core.</span></span> <span data-ttu-id="873af-135">MVC 'yi içeren ASP.NET Core paylaşılan Framework, .NET Core çalışma zamanı yüklemesinin bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="873af-135">The ASP.NET Core shared framework, which includes MVC, is part of the .NET Core runtime installation.</span></span> <span data-ttu-id="873af-136">Proje dosyasında SDK kullanılırken paylaşılan çerçeveye otomatik olarak başvurulur `Microsoft.NET.Sdk.Web` :</span><span class="sxs-lookup"><span data-stu-id="873af-136">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="873af-137">Daha fazla bilgi için bkz. [Framework başvurusu](xref:migration/22-to-30#framework-reference).</span><span class="sxs-lookup"><span data-stu-id="873af-137">For more information, see [Framework reference](xref:migration/22-to-30#framework-reference).</span></span>

<span data-ttu-id="873af-138">ASP.NET Core, `Startup` Sınıfı:</span><span class="sxs-lookup"><span data-stu-id="873af-138">In ASP.NET Core, the `Startup` class:</span></span>

* <span data-ttu-id="873af-139">*Global. asax* öğesini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="873af-139">Replaces *Global.asax* .</span></span>
* <span data-ttu-id="873af-140">Tüm uygulama başlangıç görevlerini işler.</span><span class="sxs-lookup"><span data-stu-id="873af-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="873af-141">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="873af-141">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="873af-142">ASP.NET Core projesinde, *Startup.cs* dosyasını açın:</span><span class="sxs-lookup"><span data-stu-id="873af-142">In the ASP.NET Core project, open the *Startup.cs* file:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

<span data-ttu-id="873af-143">ASP.NET Core uygulamalar, ara yazılım ile Framework özelliklerini kabul etmelidir.</span><span class="sxs-lookup"><span data-stu-id="873af-143">ASP.NET Core apps must opt in to framework features with middleware.</span></span> <span data-ttu-id="873af-144">Şablon tarafından oluşturulan önceki kod, aşağıdaki hizmetleri ve ara yazılımı ekler:</span><span class="sxs-lookup"><span data-stu-id="873af-144">The previous template-generated code adds the following services and middleware:</span></span>

* <span data-ttu-id="873af-145"><xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A>Genişletme yöntemi denetleyiciler, API ile ilgili özellikler ve görünümler IÇIN MVC hizmeti desteğini kaydeder.</span><span class="sxs-lookup"><span data-stu-id="873af-145">The <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> extension method registers MVC service support for controllers, API-related features, and views.</span></span> <span data-ttu-id="873af-146">MVC hizmeti kayıt seçenekleri hakkında daha fazla bilgi için bkz. [MVC hizmeti kaydı](xref:migration/22-to-30#mvc-service-registration)</span><span class="sxs-lookup"><span data-stu-id="873af-146">For more information on MVC service registration options, see [MVC service registration](xref:migration/22-to-30#mvc-service-registration)</span></span>
* <span data-ttu-id="873af-147"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>Genişletme yöntemi statik dosya işleyicisini ekler `Microsoft.AspNetCore.StaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="873af-147">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> extension method adds the static file handler `Microsoft.AspNetCore.StaticFiles`.</span></span> <span data-ttu-id="873af-148">`UseStaticFiles`Uzantı yönteminin önce çağrılması gerekir `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="873af-148">The `UseStaticFiles` extension method must be called before `UseRouting`.</span></span> <span data-ttu-id="873af-149">Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="873af-149">For more information, see <xref:fundamentals/static-files>.</span></span>
* <span data-ttu-id="873af-150"><xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>Uzantı yöntemi yönlendirme ekler.</span><span class="sxs-lookup"><span data-stu-id="873af-150">The <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> extension method adds routing.</span></span> <span data-ttu-id="873af-151">Daha fazla bilgi için bkz. <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="873af-151">For more information, see <xref:fundamentals/routing>.</span></span>

<span data-ttu-id="873af-152">Bu mevcut yapılandırma, örnek ASP.NET MVC projesini geçirmek için gerekenleri içerir.</span><span class="sxs-lookup"><span data-stu-id="873af-152">This existing configuration includes what is needed to migrate the example ASP.NET MVC project.</span></span> <span data-ttu-id="873af-153">ASP.NET Core ara yazılım seçenekleri hakkında daha fazla bilgi için bkz <xref:fundamentals/startup> ..</span><span class="sxs-lookup"><span data-stu-id="873af-153">For more information on ASP.NET Core middleware options, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-controllers-and-views"></a><span data-ttu-id="873af-154">Denetleyicileri ve görünümleri geçirme</span><span class="sxs-lookup"><span data-stu-id="873af-154">Migrate controllers and views</span></span>

<span data-ttu-id="873af-155">ASP.NET Core projesinde, geçiş için herhangi bir ASP.NET MVC projesindeki denetleyici ve görünüm sınıflarıyla aynı adları kullanarak yer tutucu olarak görev yapmak üzere yeni bir boş denetleyici sınıfı ve görünüm sınıfı eklenir.</span><span class="sxs-lookup"><span data-stu-id="873af-155">In the ASP.NET Core project, a new empty controller class and view class would be added to serve as placeholders using the same names as the controller and view classes in any ASP.NET MVC project to migrate from.</span></span>

<span data-ttu-id="873af-156">ASP.NET Core *WebApp1* projesi zaten en az bir örnek denetleyici içeriyor ve ASP.NET MVC projesiyle aynı ada sahip bir görünüm içeriyor.</span><span class="sxs-lookup"><span data-stu-id="873af-156">The ASP.NET Core *WebApp1* project already includes a minimal example controller and view by the same name as the ASP.NET MVC project.</span></span> <span data-ttu-id="873af-157">Bu nedenle, ASP.NET MVC denetleyicisi ve görünümleri ASP.NET MVC *WebApp1* projesinden geçirilecek görünümler için yer tutucu olarak görev yapar.</span><span class="sxs-lookup"><span data-stu-id="873af-157">So those will serve as placeholders for the ASP.NET MVC controller and views to be migrated from the ASP.NET MVC *WebApp1* project.</span></span>

1. <span data-ttu-id="873af-158">`HomeController`Yeni ASP.NET Core yöntemlerini değiştirmek için ASP.NET MVC içindeki yöntemleri kopyalayın `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="873af-158">Copy the methods from the ASP.NET MVC `HomeController` to replace the new ASP.NET Core `HomeController` methods.</span></span> <span data-ttu-id="873af-159">Eylem yöntemlerinin dönüş türünü değiştirmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="873af-159">There's no need to change the return type of the action methods.</span></span> <span data-ttu-id="873af-160">ASP.NET MVC yerleşik şablonunun denetleyici eylem yöntemi dönüş türü <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; ASP.NET Core MVC içinde eylem yöntemleri döndürülür `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="873af-160">The ASP.NET MVC built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="873af-161">`ActionResult` uygular `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="873af-161">`ActionResult` implements `IActionResult`.</span></span>
1. <span data-ttu-id="873af-162">ASP.NET Core projesinde, *Görünümler/giriş* dizinine sağ tıklayın, **Add** > **Varolan öğe** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="873af-162">In the ASP.NET Core project, right-click the *Views/Home* directory, select **Add** > **Existing Item** .</span></span>
1. <span data-ttu-id="873af-163">**Varolan öğe Ekle** iletişim kutusunda ASP.NET MVC *WebApp1* projesinin *Görünümler/giriş* dizinine gidin.</span><span class="sxs-lookup"><span data-stu-id="873af-163">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Home* directory.</span></span>
1. <span data-ttu-id="873af-164">*Hakkında. cshtml* , *Contact. cshtml* ve *Index. cshtml* :::no-loc(Razor)::: Görünüm dosyalarını seçin ve ardından **Ekle** ' yi seçerek var olan dosyaları değiştirin.</span><span class="sxs-lookup"><span data-stu-id="873af-164">Select the *About.cshtml* , *Contact.cshtml* , and *Index.cshtml* :::no-loc(Razor)::: view files, then select **Add** , replacing the existing files.</span></span>

<span data-ttu-id="873af-165">Daha fazla bilgi için <xref:mvc/controllers/actions> ve <xref:mvc/views/overview> bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="873af-165">For more information, see <xref:mvc/controllers/actions> and <xref:mvc/views/overview>.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="873af-166">Her yöntemi test et</span><span class="sxs-lookup"><span data-stu-id="873af-166">Test each method</span></span>

<span data-ttu-id="873af-167">Her denetleyici uç noktası test edilebilir, ancak düzen ve stiller belgede daha sonra ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="873af-167">Each controller endpoint can be tested, however, layout and styles are covered later in the document.</span></span>

1. <span data-ttu-id="873af-168">ASP.NET Core uygulamasını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="873af-168">Run the ASP.NET Core app.</span></span>
1. <span data-ttu-id="873af-169">Geçerli bağlantı noktası numarasını ASP.NET Core projesinde kullanılan bağlantı noktası numarasıyla değiştirerek, çalışan ASP.NET Core uygulamasındaki tarayıcıdan işlenen görünümleri çağırın.</span><span class="sxs-lookup"><span data-stu-id="873af-169">Invoke the rendered views from the browser on the running ASP.NET Core app by replacing the current port number with the port number used in the ASP.NET Core project.</span></span> <span data-ttu-id="873af-170">Örneğin, `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="873af-170">For example, `https://localhost:44375/home/about`.</span></span>

## <a name="migrate-static-content"></a><span data-ttu-id="873af-171">Statik içeriği geçirme</span><span class="sxs-lookup"><span data-stu-id="873af-171">Migrate static content</span></span>

<span data-ttu-id="873af-172">ASP.NET MVC 5 ve önceki sürümlerde, statik içerik Web projesinin kök dizininden barındırılıyor ve sunucu tarafı dosyalarıyla karıştı.</span><span class="sxs-lookup"><span data-stu-id="873af-172">In ASP.NET MVC 5 and earlier, static content was hosted from the web project's root directory and was intermixed with server-side files.</span></span> <span data-ttu-id="873af-173">ASP.NET Core, statik dosyalar projenin [Web kök](xref:fundamentals/index#web-root) dizininde saklanır.</span><span class="sxs-lookup"><span data-stu-id="873af-173">In ASP.NET Core, static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="873af-174">Varsayılan dizin *{Content root}/Wwwroot* , ancak değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="873af-174">The default directory is *{content root}/wwwroot* , but it can be changed.</span></span> <span data-ttu-id="873af-175">Daha fazla bilgi için [ASP.NET Core Içindeki statik dosyalar](xref:fundamentals/static-files#serve-static-files)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="873af-175">For more information, see [Static files in ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span></span>

<span data-ttu-id="873af-176">ASP.NET MVC *WebApp1* projesinden statik Içeriği ASP.NET Core *WebApp1* projesindeki *Wwwroot* dizinine kopyalayın:</span><span class="sxs-lookup"><span data-stu-id="873af-176">Copy the static content from the ASP.NET MVC *WebApp1* project to the *wwwroot* directory in the ASP.NET Core *WebApp1* project:</span></span>

1. <span data-ttu-id="873af-177">ASP.NET Core projesinde, *Wwwroot* dizinine sağ tıklayın, **Add** > **Varolan öğe** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="873af-177">In the ASP.NET Core project, right-click the *wwwroot* directory, select **Add** > **Existing Item** .</span></span>
1. <span data-ttu-id="873af-178">**Varolan öğe Ekle** iletişim kutusunda ASP.NET MVC *WebApp1* projesine gidin.</span><span class="sxs-lookup"><span data-stu-id="873af-178">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project.</span></span>
1. <span data-ttu-id="873af-179">*Tercih simgesi. ico* dosyasını seçin ve ardından **Ekle** ' yi seçerek var olan dosyayı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="873af-179">Select the *favicon.ico* file, then select **Add** , replacing the existing file.</span></span>

## <a name="migrate-the-layout-files"></a><span data-ttu-id="873af-180">Düzen dosyalarını geçirme</span><span class="sxs-lookup"><span data-stu-id="873af-180">Migrate the layout files</span></span>

<span data-ttu-id="873af-181">ASP.NET MVC proje düzeni dosyalarını ASP.NET Core projesine kopyalayın:</span><span class="sxs-lookup"><span data-stu-id="873af-181">Copy the ASP.NET MVC project layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="873af-182">ASP.NET Core projesinde, *Görünümler* dizinine sağ tıklayın, **Add** > **Varolan öğe** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="873af-182">In the ASP.NET Core project, right-click the *Views* directory, select **Add** > **Existing Item** .</span></span>
1. <span data-ttu-id="873af-183">**Varolan öğe Ekle** iletişim kutusunda ASP.NET MVC *WebApp1* projesinin *Görünümler* dizinine gidin.</span><span class="sxs-lookup"><span data-stu-id="873af-183">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views* directory.</span></span>
1. <span data-ttu-id="873af-184">*_ViewStart. cshtml* dosyasını seçin ve ardından **Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="873af-184">Select the *_ViewStart.cshtml* file then select **Add** .</span></span>

<span data-ttu-id="873af-185">ASP.NET MVC proje paylaşılan düzen dosyalarını ASP.NET Core projesine kopyalayın:</span><span class="sxs-lookup"><span data-stu-id="873af-185">Copy the ASP.NET MVC project shared layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="873af-186">ASP.NET Core projesinde, *Görünümler/paylaşılan* dizine sağ tıklayın, **Add** > **Varolan öğe** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="873af-186">In the ASP.NET Core project, right-click the *Views/Shared* directory, select **Add** > **Existing Item** .</span></span>
1. <span data-ttu-id="873af-187">**Varolan öğe Ekle** iletişim kutusunda ASP.NET MVC *WebApp1* projesinin *Görünümler/paylaşılan* dizinine gidin.</span><span class="sxs-lookup"><span data-stu-id="873af-187">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Shared* directory.</span></span>
1. <span data-ttu-id="873af-188">*_Layout. cshtml* dosyasını seçin ve ardından **Ekle** ' yi seçerek var olan dosyayı değiştirin.</span><span class="sxs-lookup"><span data-stu-id="873af-188">Select the *_Layout.cshtml* file, then select **Add** , replacing the existing file.</span></span>

<span data-ttu-id="873af-189">ASP.NET Core projesinde, *_Layout. cshtml* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="873af-189">In the ASP.NET Core project, open the *_Layout.cshtml* file.</span></span> <span data-ttu-id="873af-190">Aşağıda gösterilen tamamlanan kodla eşleştirmek için aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="873af-190">Make the following changes to match the completed code shown below:</span></span>

<span data-ttu-id="873af-191">Önyükleme CSS 'sini aşağıdaki tamamlanan kodla eşleşecek şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="873af-191">Update the Bootstrap CSS inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="873af-192">`@Styles.Render("~/Content/css")` `<link>` *Bootstrap. css* ' nin yükleneceği bir öğeyle değiştirin (aşağıya bakın).</span><span class="sxs-lookup"><span data-stu-id="873af-192">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>
1. <span data-ttu-id="873af-193">Kaldırın `@Scripts.Render("~/bundles/modernizr")` .</span><span class="sxs-lookup"><span data-stu-id="873af-193">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

<span data-ttu-id="873af-194">Önyükleme CSS ekleme için tamamlanan değiştirme biçimlendirmesi:</span><span class="sxs-lookup"><span data-stu-id="873af-194">The completed replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="873af-195">JQuery ve Bootstrap JavaScript 'ı aşağıdaki tamamlanan kodla eşleşecek şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="873af-195">Update the jQuery and Bootstrap JavaScript inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="873af-196">`@Scripts.Render("~/bundles/jquery")`Bir öğesiyle değiştirin `<script>` (aşağıya bakın).</span><span class="sxs-lookup"><span data-stu-id="873af-196">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>
1. <span data-ttu-id="873af-197">`@Scripts.Render("~/bundles/bootstrap")`Bir öğesiyle değiştirin `<script>` (aşağıya bakın).</span><span class="sxs-lookup"><span data-stu-id="873af-197">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="873af-198">JQuery ve Bootstrap JavaScript ekleme için tamamlanan değiştirme biçimlendirmesi:</span><span class="sxs-lookup"><span data-stu-id="873af-198">The completed replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="873af-199">Güncelleştirilmiş *_Layout. cshtml* dosyası aşağıda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="873af-199">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

<span data-ttu-id="873af-200">Siteyi tarayıcıda görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="873af-200">View the site in the browser.</span></span> <span data-ttu-id="873af-201">Beklenen stillerle birlikte işlenmelidir.</span><span class="sxs-lookup"><span data-stu-id="873af-201">It should render with the expected styles in place.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="873af-202">Paketlemeyi ve küçültmeye göre yapılandırma</span><span class="sxs-lookup"><span data-stu-id="873af-202">Configure bundling and minification</span></span>

<span data-ttu-id="873af-203">ASP.NET Core, [Weboptimizer](https://github.com/ligershark/WebOptimizer) ve diğer benzer kitaplıklar gibi çeşitli açık kaynaklı paket oluşturma ve küçültmeye yönelik çözümlerle uyumludur.</span><span class="sxs-lookup"><span data-stu-id="873af-203">ASP.NET Core is compatible with several open-source bundling and minification solutions such as [WebOptimizer](https://github.com/ligershark/WebOptimizer) and other similar libraries.</span></span> <span data-ttu-id="873af-204">ASP.NET Core, yerel bir paketleme ve küçültmeye yönelik çözüm sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="873af-204">ASP.NET Core doesn't provide a native bundling and minification solution.</span></span> <span data-ttu-id="873af-205">Paketleme ve küçültmeye yönelik yapılandırma hakkında daha fazla bilgi için bkz. [paketleme ve küçültmeye](xref:client-side/bundling-and-minification)yönelik.</span><span class="sxs-lookup"><span data-stu-id="873af-205">For information on configuring bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="873af-206">HTTP 500 hatalarını çözme</span><span class="sxs-lookup"><span data-stu-id="873af-206">Solve HTTP 500 errors</span></span>

<span data-ttu-id="873af-207">Sorunun kaynağı hakkında bilgi içermeyen bir HTTP 500 hata iletisine neden olabilecek birçok sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="873af-207">There are many problems that can cause an HTTP 500 error message that contains no information on the source of the problem.</span></span> <span data-ttu-id="873af-208">Örneğin, *views/_ViewImports. cshtml* dosyası projede mevcut olmayan bir ad alanı içeriyorsa, bir http 500 hatası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="873af-208">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, an HTTP 500 error is generated.</span></span> <span data-ttu-id="873af-209">ASP.NET Core uygulamalarda varsayılan olarak, `UseDeveloperExceptionPage` uzantı öğesine eklenir `IApplicationBuilder` ve ortam *geliştirme* sırasında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="873af-209">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the environment is *Development* .</span></span> <span data-ttu-id="873af-210">Bu, aşağıdaki kodda ayrıntılı olarak verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="873af-210">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

<span data-ttu-id="873af-211">ASP.NET Core işlenmeyen özel durumları HTTP 500 hata yanıtlarına dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="873af-211">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="873af-212">Normalde, sunucu hakkında potansiyel olarak hassas bilgilerin açıklanmasını engellemek için bu yanıtlara hata ayrıntıları dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="873af-212">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="873af-213">Daha fazla bilgi için bkz. [Geliştirici özel durum sayfası](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="873af-213">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="next-steps"></a><span data-ttu-id="873af-214">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="873af-214">Next steps</span></span>

* <xref:migration/identity>

## <a name="additional-resources"></a><span data-ttu-id="873af-215">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="873af-215">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="873af-216">Bu makalede, bir ASP.NET MVC projesinin [ASP.NET Core MVC](xref:mvc/overview) 2,2 ' ye nasıl geçirilerek yapılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="873af-216">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.2.</span></span> <span data-ttu-id="873af-217">Sürecinde, ASP.NET MVC 'den değiştirilen birçok şeyi vurgular.</span><span class="sxs-lookup"><span data-stu-id="873af-217">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="873af-218">ASP.NET MVC 'den geçiş çok adımlı bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="873af-218">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="873af-219">Bu makalede şunları ele alınmaktadır:</span><span class="sxs-lookup"><span data-stu-id="873af-219">This article covers:</span></span>

* <span data-ttu-id="873af-220">İlk kurulum</span><span class="sxs-lookup"><span data-stu-id="873af-220">Initial setup</span></span>
* <span data-ttu-id="873af-221">Temel denetleyiciler ve görünümler</span><span class="sxs-lookup"><span data-stu-id="873af-221">Basic controllers and views</span></span>
* <span data-ttu-id="873af-222">Statik içerik</span><span class="sxs-lookup"><span data-stu-id="873af-222">Static content</span></span>
* <span data-ttu-id="873af-223">İstemci tarafı bağımlılıkları.</span><span class="sxs-lookup"><span data-stu-id="873af-223">Client-side dependencies.</span></span>

<span data-ttu-id="873af-224">Yapılandırma ve kodu geçirmek için :::no-loc(Identity)::: bkz <xref:migration/configuration> . ve <xref:migration/identity> .</span><span class="sxs-lookup"><span data-stu-id="873af-224">For migrating configuration and :::no-loc(Identity)::: code, see <xref:migration/configuration> and <xref:migration/identity>.</span></span>

> [!NOTE]
> <span data-ttu-id="873af-225">Örneklerdeki sürüm numaraları güncel olmayabilir, projeleri uygun şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="873af-225">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="873af-226">Başlatıcı ASP.NET MVC projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="873af-226">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="873af-227">Yükseltmeyi göstermek için, bir ASP.NET MVC uygulaması oluşturarak başlayacağız.</span><span class="sxs-lookup"><span data-stu-id="873af-227">To demonstrate the upgrade, we'll start by creating an ASP.NET MVC app.</span></span> <span data-ttu-id="873af-228">Ad alanı, bir sonraki adımda oluşturulan ASP.NET Core projesiyle eşleşmesi için *WebApp1* adıyla oluşturun.</span><span class="sxs-lookup"><span data-stu-id="873af-228">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Visual Studio yeni proje iletişim kutusu](mvc/_static/new-project.png)

![Yeni Web uygulaması iletişim kutusu: ASP.NET şablonlar panelinde MVC proje şablonu seçildi](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="873af-231">*Isteğe bağlı:* Çözümün adını *WebApp1* ile *Mvc5* arasında değiştirin.</span><span class="sxs-lookup"><span data-stu-id="873af-231">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5* .</span></span> <span data-ttu-id="873af-232">Visual Studio yeni çözüm adını ( *Mvc5* ) görüntüler, bu da projeyi bir sonraki projeden daha kolay bir şekilde anlatmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="873af-232">Visual Studio displays the new solution name ( *Mvc5* ), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="873af-233">ASP.NET Core projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="873af-233">Create the ASP.NET Core project</span></span>

<span data-ttu-id="873af-234">Önceki projeyle aynı ada sahip yeni bir *boş* ASP.NET Core Web uygulaması oluşturun ( *WebApp1* ), böylece iki projedeki ad alanları eşleşir.</span><span class="sxs-lookup"><span data-stu-id="873af-234">Create a new *empty* ASP.NET Core web app with the same name as the previous project ( *WebApp1* ) so the namespaces in the two projects match.</span></span> <span data-ttu-id="873af-235">Aynı ad alanına sahip olmak, kodu iki proje arasında kopyalamayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="873af-235">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="873af-236">Aynı adı kullanmak için bu projeyi önceki projeden farklı bir dizinde oluşturun.</span><span class="sxs-lookup"><span data-stu-id="873af-236">Create this project in a different directory than the previous project to use the same name.</span></span>

![Yeni Proje iletişim kutusu](mvc/_static/new_core.png)

![Yeni ASP.NET Web uygulaması iletişim kutusu: ASP.NET Core şablonlar panelinde boş proje şablonu seçildi](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="873af-239">*Isteğe bağlı:* *Web uygulaması* proje şablonunu kullanarak yeni bir ASP.NET Core uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="873af-239">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="873af-240">Projeyi *WebApp1* olarak adlandırın ve **bireysel kullanıcı hesaplarının** bir kimlik doğrulama seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="873af-240">Name the project *WebApp1* , and select an authentication option of **Individual User Accounts** .</span></span> <span data-ttu-id="873af-241">Bu uygulamayı *Fullaspnetcore* olarak yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="873af-241">Rename this app to *FullAspNetCore* .</span></span> <span data-ttu-id="873af-242">Bu projenin oluşturulması, dönüştürmeye zaman kazandırır.</span><span class="sxs-lookup"><span data-stu-id="873af-242">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="873af-243">Nihai sonuç, şablon tarafından üretilen kodda görüntülenebilir, kod dönüştürme projesine kopyalanabilir veya şablon tarafından oluşturulan projeyle karşılaştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="873af-243">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="873af-244">Siteyi MVC kullanacak şekilde yapılandırma</span><span class="sxs-lookup"><span data-stu-id="873af-244">Configure the site to use MVC</span></span>

* <span data-ttu-id="873af-245">.NET Core 'u hedeflerken, varsayılan olarak [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) öğesine başvurulur.</span><span class="sxs-lookup"><span data-stu-id="873af-245">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="873af-246">Bu paket, MVC uygulamaları tarafından yaygın olarak kullanılan paketleri içerir.</span><span class="sxs-lookup"><span data-stu-id="873af-246">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="873af-247">.NET Framework hedefliyorsanız, paket başvurularının proje dosyasında tek tek listelenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="873af-247">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="873af-248">`Microsoft.AspNetCore.Mvc` ASP.NET Core MVC çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="873af-248">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="873af-249">`Microsoft.AspNetCore.StaticFiles` , statik dosya işleyicisidir.</span><span class="sxs-lookup"><span data-stu-id="873af-249">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="873af-250">ASP.NET Core uygulamalar, statik dosyalar sunma gibi bir ara yazılım için açıkça kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="873af-250">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="873af-251">Daha fazla bilgi için bkz. [statik dosyalar](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="873af-251">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="873af-252">*Startup.cs* dosyasını açın ve kodu aşağıdakiler ile eşleşecek şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="873af-252">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="873af-253"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>Genişletme yöntemi statik dosya işleyicisini ekler.</span><span class="sxs-lookup"><span data-stu-id="873af-253">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="873af-254">Daha fazla bilgi için bkz. [uygulama başlatma](xref:fundamentals/startup) ve [yönlendirme](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="873af-254">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="873af-255">Denetleyici ekleme ve görüntüleme</span><span class="sxs-lookup"><span data-stu-id="873af-255">Add a controller and view</span></span>

<span data-ttu-id="873af-256">Bu bölümde, bir sonraki bölümde geçirilen ASP.NET MVC denetleyicisi ve görünümleri için yer tutucu olarak kullanılacak en az bir denetleyici ve görünüm eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="873af-256">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="873af-257">Bir *Controllers* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="873af-257">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="873af-258">*Controllers* dizinine *HomeController.cs* adlı bir **Denetleyici sınıfı** ekleyin.</span><span class="sxs-lookup"><span data-stu-id="873af-258">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![Yeni öğe Ekle iletişim kutusu](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="873af-260">Bir *Görünümler* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="873af-260">Add a *Views* directory.</span></span>

* <span data-ttu-id="873af-261">Bir *Görünüm/giriş* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="873af-261">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="873af-262">*Görünümler/giriş* dizinine *Index. cshtml* adlı bir **:::no-loc(Razor)::: Görünüm** ekleyin.</span><span class="sxs-lookup"><span data-stu-id="873af-262">Add a **:::no-loc(Razor)::: View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![Yeni öğe Ekle iletişim kutusu](mvc/_static/view.png)

<span data-ttu-id="873af-264">Proje yapısı aşağıda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="873af-264">The project structure is shown below:</span></span>

![WebApp1 dosyalarının ve dizinlerinin gösterildiği Çözüm Gezgini](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="873af-266">*Views/Home/Index. cshtml* dosyasının içeriğini aşağıdaki biçimlendirme ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="873af-266">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="873af-267">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="873af-267">Run the app.</span></span>

![Microsoft Edge 'de açık Web uygulaması](mvc/_static/hello-world.png)

<span data-ttu-id="873af-269">Daha fazla bilgi için bkz. [denetleyiciler](xref:mvc/controllers/actions) ve [Görünümler](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="873af-269">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="873af-270">Aşağıdaki işlev örnek ASP.NET MVC projesinden ASP.NET Core projesine geçiş gerektirir:</span><span class="sxs-lookup"><span data-stu-id="873af-270">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="873af-271">istemci tarafı içerik (CSS, yazı tipleri ve betikler)</span><span class="sxs-lookup"><span data-stu-id="873af-271">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="873af-272">denetleyiciler</span><span class="sxs-lookup"><span data-stu-id="873af-272">controllers</span></span>

* <span data-ttu-id="873af-273">görünümler</span><span class="sxs-lookup"><span data-stu-id="873af-273">views</span></span>

* <span data-ttu-id="873af-274">modeller</span><span class="sxs-lookup"><span data-stu-id="873af-274">models</span></span>

* <span data-ttu-id="873af-275">Paketleme</span><span class="sxs-lookup"><span data-stu-id="873af-275">bundling</span></span>

* <span data-ttu-id="873af-276">filtreler</span><span class="sxs-lookup"><span data-stu-id="873af-276">filters</span></span>

* <span data-ttu-id="873af-277">Oturum açma/kapatma :::no-loc(Identity)::: (Bu, sonraki öğreticide yapılır.)</span><span class="sxs-lookup"><span data-stu-id="873af-277">Log in/out, :::no-loc(Identity)::: (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="873af-278">Denetleyiciler ve görünümler</span><span class="sxs-lookup"><span data-stu-id="873af-278">Controllers and views</span></span>

* <span data-ttu-id="873af-279">Yöntemlerin her birini ASP.NET MVC 'den `HomeController` New öğesine kopyalayın `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="873af-279">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="873af-280">ASP.NET MVC 'de, yerleşik şablonun denetleyici eylem yöntemi dönüş türü ' dir <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; ASP.NET Core MVC 'de, eylem yöntemleri `IActionResult` bunun yerine döndürülür.</span><span class="sxs-lookup"><span data-stu-id="873af-280">In ASP.NET MVC, the built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="873af-281">`ActionResult` uyguladığı `IActionResult` için, eylem yöntemlerinin dönüş türünü değiştirmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="873af-281">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="873af-282">ASP.NET MVC projesindeki *. cshtml* , *Contact. cshtml* ve *Index. cshtml* :::no-loc(Razor)::: Görünüm dosyalarını ASP.NET Core projesine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="873af-282">Copy the *About.cshtml* , *Contact.cshtml* , and *Index.cshtml* :::no-loc(Razor)::: view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="873af-283">Her yöntemi test et</span><span class="sxs-lookup"><span data-stu-id="873af-283">Test each method</span></span>

<span data-ttu-id="873af-284">Düzen dosyası ve stiller henüz geçirilmemiştir, bu nedenle işlenmiş görünümler yalnızca görünüm dosyalarındaki içeriği içerir.</span><span class="sxs-lookup"><span data-stu-id="873af-284">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="873af-285">Ve görünümleri için Düzen dosyası tarafından oluşturulan `About` Bağlantılar `Contact` henüz kullanılamayacak.</span><span class="sxs-lookup"><span data-stu-id="873af-285">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

<span data-ttu-id="873af-286">Geçerli bağlantı noktası numarasını ASP.NET Core projesinde kullanılan bağlantı noktası numarasıyla değiştirerek, çalışan ASP.NET Core uygulamasındaki tarayıcıdan işlenmiş görünümleri çağırın.</span><span class="sxs-lookup"><span data-stu-id="873af-286">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="873af-287">Örneğin: `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="873af-287">For example: `https://localhost:44375/home/about`.</span></span>

![Kişi sayfası](mvc/_static/contact-page.png)

<span data-ttu-id="873af-289">Stil ve menü öğelerinin eksikliğine göz önünde.</span><span class="sxs-lookup"><span data-stu-id="873af-289">Note the lack of styling and menu items.</span></span> <span data-ttu-id="873af-290">Stil, sonraki bölümde düzeltilecektir.</span><span class="sxs-lookup"><span data-stu-id="873af-290">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="873af-291">Statik içerik</span><span class="sxs-lookup"><span data-stu-id="873af-291">Static content</span></span>

<span data-ttu-id="873af-292">ASP.NET MVC 5 ve önceki sürümlerde, statik içerik Web projesinin kökünden barındırılır ve sunucu tarafı dosyalarıyla karıştı.</span><span class="sxs-lookup"><span data-stu-id="873af-292">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="873af-293">ASP.NET Core, statik içerik *Wwwroot* dizininde barındırılır.</span><span class="sxs-lookup"><span data-stu-id="873af-293">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="873af-294">ASP.NET MVC uygulamasındaki statik içeriği ASP.NET Core projesindeki *Wwwroot* dizinine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="873af-294">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="873af-295">Bu örnek dönüştürmede:</span><span class="sxs-lookup"><span data-stu-id="873af-295">In this sample conversion:</span></span>

* <span data-ttu-id="873af-296">ASP.NET MVC projesinden ASP.NET Core projesindeki *Wwwroot* dizinine MVC *. ico* dosyasını kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="873af-296">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="873af-297">ASP.NET MVC projesi, kendi stili için [önyükleme](https://getbootstrap.com/) kullanır ve önyükleme dosyalarını *içerik* ve *betikler* dizinlerinde depolar.</span><span class="sxs-lookup"><span data-stu-id="873af-297">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="873af-298">ASP.NET MVC projesini oluşturan şablon, düzen dosyasında önyüklenmesine başvurur ( *Görünümler/paylaşılan/_Layout. cshtml* ).</span><span class="sxs-lookup"><span data-stu-id="873af-298">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file ( *Views/Shared/_Layout.cshtml* ).</span></span> <span data-ttu-id="873af-299">*bootstrap.js* ve *Bootstrap. css* dosyaları, ASP.NET MVC projesinden yeni projedeki *Wwwroot* dizinine kopyalanabilir.</span><span class="sxs-lookup"><span data-stu-id="873af-299">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="873af-300">Bunun yerine, bu belge sonraki bölümde CDNs kullanarak önyükleme (ve diğer istemci tarafı kitaplıkları) için destek ekler.</span><span class="sxs-lookup"><span data-stu-id="873af-300">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="873af-301">Düzen dosyasını geçirme</span><span class="sxs-lookup"><span data-stu-id="873af-301">Migrate the layout file</span></span>

* <span data-ttu-id="873af-302">*_ViewStart. cshtml* dosyasını ASP.NET MVC projesinin *Görünümler* dizininden ASP.NET Core projenin *Görünümler* dizinine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="873af-302">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="873af-303">*_ViewStart. cshtml* dosyası ASP.NET Core MVC 'de değişmemiştir.</span><span class="sxs-lookup"><span data-stu-id="873af-303">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="873af-304">Bir *Görünüm/paylaşılan* dizin oluşturun.</span><span class="sxs-lookup"><span data-stu-id="873af-304">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="873af-305">*Isteğe bağlı:* *_ViewImports. cshtml* 'Yi *Fullaspnetcore* MVC projesinin *views* dizininden ASP.NET Core projenin *views* dizinine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="873af-305">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="873af-306">*_ViewImports. cshtml* dosyasındaki herhangi bir ad alanı bildirimini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="873af-306">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="873af-307">*_ViewImports. cshtml* dosyası, tüm görünüm dosyaları için ad alanları sağlar ve [etiket yardımcılarını](xref:mvc/views/tag-helpers/intro)getirir.</span><span class="sxs-lookup"><span data-stu-id="873af-307">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="873af-308">Etiket Yardımcıları yeni düzen dosyasında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="873af-308">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="873af-309">*_ViewImports. cshtml* dosyası ASP.NET Core için yenidir.</span><span class="sxs-lookup"><span data-stu-id="873af-309">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="873af-310">*_Layout. cshtml* dosyasını ASP.NET MVC projesinin *Görünümler/paylaşılan* dizininden ASP.NET Core projenin *Görünümler/paylaşılan* dizinine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="873af-310">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="873af-311">*_Layout. cshtml* dosyasını açın ve aşağıdaki değişiklikleri yapın (tamamlanan kod aşağıda gösterilmiştir):</span><span class="sxs-lookup"><span data-stu-id="873af-311">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="873af-312">`@Styles.Render("~/Content/css")` `<link>` *Bootstrap. css* ' nin yükleneceği bir öğeyle değiştirin (aşağıya bakın).</span><span class="sxs-lookup"><span data-stu-id="873af-312">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="873af-313">Kaldırın `@Scripts.Render("~/bundles/modernizr")` .</span><span class="sxs-lookup"><span data-stu-id="873af-313">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="873af-314">`@Html.Partial("_LoginPartial")`Çizgiyi açıklama (çizgi ile çevreleyin `@*...*@` ).</span><span class="sxs-lookup"><span data-stu-id="873af-314">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="873af-315">Daha fazla bilgi için bkz. [kimlik doğrulamasını geçirme ve :::no-loc(Identity)::: ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="873af-315">For more information, see [Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="873af-316">`@Scripts.Render("~/bundles/jquery")`Bir öğesiyle değiştirin `<script>` (aşağıya bakın).</span><span class="sxs-lookup"><span data-stu-id="873af-316">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="873af-317">`@Scripts.Render("~/bundles/bootstrap")`Bir öğesiyle değiştirin `<script>` (aşağıya bakın).</span><span class="sxs-lookup"><span data-stu-id="873af-317">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="873af-318">Önyükleme CSS ekleme için değiştirme biçimlendirmesi:</span><span class="sxs-lookup"><span data-stu-id="873af-318">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="873af-319">JQuery ve Bootstrap JavaScript ekleme için değiştirme biçimlendirmesi:</span><span class="sxs-lookup"><span data-stu-id="873af-319">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="873af-320">Güncelleştirilmiş *_Layout. cshtml* dosyası aşağıda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="873af-320">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="873af-321">Siteyi tarayıcıda görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="873af-321">View the site in the browser.</span></span> <span data-ttu-id="873af-322">Artık beklenen stillerle birlikte doğru şekilde yüklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="873af-322">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="873af-323">*Isteğe bağlı:* Yeni düzen dosyasını kullanmayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="873af-323">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="873af-324">Düzen dosyasını *Fullaspnetcore* projesinden kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="873af-324">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="873af-325">Yeni düzen dosyası [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) kullanır ve başka iyileştirmeler içerir.</span><span class="sxs-lookup"><span data-stu-id="873af-325">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="873af-326">Paketlemeyi ve küçültmeye göre yapılandırma</span><span class="sxs-lookup"><span data-stu-id="873af-326">Configure bundling and minification</span></span>

<span data-ttu-id="873af-327">Paketleme ve küçültmeye yönelik yapılandırma hakkında daha fazla bilgi için bkz. [paketleme ve küçültmeye](xref:client-side/bundling-and-minification)yönelik.</span><span class="sxs-lookup"><span data-stu-id="873af-327">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="873af-328">HTTP 500 hatalarını çözme</span><span class="sxs-lookup"><span data-stu-id="873af-328">Solve HTTP 500 errors</span></span>

<span data-ttu-id="873af-329">Sorunun kaynağı hakkında bilgi içermeyen bir HTTP 500 hata iletisine neden olabilecek birçok sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="873af-329">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="873af-330">Örneğin, *views/_ViewImports. cshtml* dosyası projede mevcut olmayan bir ad alanı içeriyorsa, bir http 500 hatası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="873af-330">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="873af-331">ASP.NET Core uygulamalarda varsayılan olarak, `UseDeveloperExceptionPage` uzantı öğesine eklenir `IApplicationBuilder` ve yapılandırma *geliştirme* sırasında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="873af-331">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development* .</span></span> <span data-ttu-id="873af-332">Aşağıdaki kodda bir örnek görebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="873af-332">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="873af-333">ASP.NET Core işlenmeyen özel durumları HTTP 500 hata yanıtlarına dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="873af-333">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="873af-334">Normalde, sunucu hakkında potansiyel olarak hassas bilgilerin açıklanmasını engellemek için bu yanıtlara hata ayrıntıları dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="873af-334">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="873af-335">Daha fazla bilgi için bkz. [Geliştirici özel durum sayfası](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="873af-335">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="873af-336">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="873af-336">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="873af-337">Bu makalede, bir ASP.NET MVC projesinin [ASP.NET Core MVC](xref:mvc/overview) 2,1 ' ye nasıl geçirilerek yapılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="873af-337">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.1.</span></span> <span data-ttu-id="873af-338">Sürecinde, ASP.NET MVC 'den değiştirilen birçok şeyi vurgular.</span><span class="sxs-lookup"><span data-stu-id="873af-338">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="873af-339">ASP.NET MVC 'den geçiş çok adımlı bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="873af-339">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="873af-340">Bu makalede şunları ele alınmaktadır:</span><span class="sxs-lookup"><span data-stu-id="873af-340">This article covers:</span></span>

* <span data-ttu-id="873af-341">İlk kurulum</span><span class="sxs-lookup"><span data-stu-id="873af-341">Initial setup</span></span>
* <span data-ttu-id="873af-342">Temel denetleyiciler ve görünümler</span><span class="sxs-lookup"><span data-stu-id="873af-342">Basic controllers and views</span></span>
* <span data-ttu-id="873af-343">Statik içerik</span><span class="sxs-lookup"><span data-stu-id="873af-343">Static content</span></span>
* <span data-ttu-id="873af-344">İstemci tarafı bağımlılıkları.</span><span class="sxs-lookup"><span data-stu-id="873af-344">Client-side dependencies.</span></span>

<span data-ttu-id="873af-345">Yapılandırma ve kodu geçirmek için :::no-loc(Identity)::: bkz. [yapılandırmayı ASP.NET Core](xref:migration/configuration) ve [geçiş kimlik doğrulaması ve :::no-loc(Identity)::: ASP.NET Core geçirme](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="873af-345">For migrating configuration and :::no-loc(Identity)::: code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core](xref:migration/identity).</span></span>

> [!NOTE]
> <span data-ttu-id="873af-346">Örneklerdeki sürüm numaraları güncel olmayabilir, projeleri uygun şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="873af-346">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="873af-347">Başlatıcı ASP.NET MVC projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="873af-347">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="873af-348">Yükseltmeyi göstermek için, bir ASP.NET MVC uygulaması oluşturarak başlayacağız.</span><span class="sxs-lookup"><span data-stu-id="873af-348">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="873af-349">Ad alanı, bir sonraki adımda oluşturulan ASP.NET Core projesiyle eşleşmesi için *WebApp1* adıyla oluşturun.</span><span class="sxs-lookup"><span data-stu-id="873af-349">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Visual Studio yeni proje iletişim kutusu](mvc/_static/new-project.png)

![Yeni Web uygulaması iletişim kutusu: ASP.NET şablonlar panelinde MVC proje şablonu seçildi](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="873af-352">*Isteğe bağlı:* Çözümün adını *WebApp1* ile *Mvc5* arasında değiştirin.</span><span class="sxs-lookup"><span data-stu-id="873af-352">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5* .</span></span> <span data-ttu-id="873af-353">Visual Studio yeni çözüm adını ( *Mvc5* ) görüntüler, bu da projeyi bir sonraki projeden daha kolay bir şekilde anlatmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="873af-353">Visual Studio displays the new solution name ( *Mvc5* ), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="873af-354">ASP.NET Core projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="873af-354">Create the ASP.NET Core project</span></span>

<span data-ttu-id="873af-355">Önceki projeyle aynı ada sahip yeni bir *boş* ASP.NET Core Web uygulaması oluşturun ( *WebApp1* ), böylece iki projedeki ad alanları eşleşir.</span><span class="sxs-lookup"><span data-stu-id="873af-355">Create a new *empty* ASP.NET Core web app with the same name as the previous project ( *WebApp1* ) so the namespaces in the two projects match.</span></span> <span data-ttu-id="873af-356">Aynı ad alanına sahip olmak, kodu iki proje arasında kopyalamayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="873af-356">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="873af-357">Aynı adı kullanmak için bu projeyi önceki projeden farklı bir dizinde oluşturun.</span><span class="sxs-lookup"><span data-stu-id="873af-357">Create this project in a different directory than the previous project to use the same name.</span></span>

![Yeni Proje iletişim kutusu](mvc/_static/new_core.png)

![Yeni ASP.NET Web uygulaması iletişim kutusu: ASP.NET Core şablonlar panelinde boş proje şablonu seçildi](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="873af-360">*Isteğe bağlı:* *Web uygulaması* proje şablonunu kullanarak yeni bir ASP.NET Core uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="873af-360">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="873af-361">Projeyi *WebApp1* olarak adlandırın ve **bireysel kullanıcı hesaplarının** bir kimlik doğrulama seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="873af-361">Name the project *WebApp1* , and select an authentication option of **Individual User Accounts** .</span></span> <span data-ttu-id="873af-362">Bu uygulamayı *Fullaspnetcore* olarak yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="873af-362">Rename this app to *FullAspNetCore* .</span></span> <span data-ttu-id="873af-363">Bu projenin oluşturulması, dönüştürmeye zaman kazandırır.</span><span class="sxs-lookup"><span data-stu-id="873af-363">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="873af-364">Nihai sonuç, şablon tarafından üretilen kodda görüntülenebilir, kod dönüştürme projesine kopyalanabilir veya şablon tarafından oluşturulan projeyle karşılaştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="873af-364">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="873af-365">Siteyi MVC kullanacak şekilde yapılandırma</span><span class="sxs-lookup"><span data-stu-id="873af-365">Configure the site to use MVC</span></span>

* <span data-ttu-id="873af-366">.NET Core 'u hedeflerken, varsayılan olarak [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) öğesine başvurulur.</span><span class="sxs-lookup"><span data-stu-id="873af-366">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="873af-367">Bu paket, MVC uygulamaları tarafından yaygın olarak kullanılan paketleri içerir.</span><span class="sxs-lookup"><span data-stu-id="873af-367">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="873af-368">.NET Framework hedefliyorsanız, paket başvurularının proje dosyasında tek tek listelenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="873af-368">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="873af-369">`Microsoft.AspNetCore.Mvc` ASP.NET Core MVC çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="873af-369">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="873af-370">`Microsoft.AspNetCore.StaticFiles` , statik dosya işleyicisidir.</span><span class="sxs-lookup"><span data-stu-id="873af-370">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="873af-371">ASP.NET Core uygulamalar, statik dosyalar sunma gibi bir ara yazılım için açıkça kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="873af-371">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="873af-372">Daha fazla bilgi için bkz. [statik dosyalar](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="873af-372">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="873af-373">*Startup.cs* dosyasını açın ve kodu aşağıdakiler ile eşleşecek şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="873af-373">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="873af-374"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>Genişletme yöntemi statik dosya işleyicisini ekler.</span><span class="sxs-lookup"><span data-stu-id="873af-374">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="873af-375">`UseMvc`Uzantı yöntemi yönlendirme ekler.</span><span class="sxs-lookup"><span data-stu-id="873af-375">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="873af-376">Daha fazla bilgi için bkz. [uygulama başlatma](xref:fundamentals/startup) ve [yönlendirme](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="873af-376">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="873af-377">Denetleyici ekleme ve görüntüleme</span><span class="sxs-lookup"><span data-stu-id="873af-377">Add a controller and view</span></span>

<span data-ttu-id="873af-378">Bu bölümde, bir sonraki bölümde geçirilen ASP.NET MVC denetleyicisi ve görünümleri için yer tutucu olarak kullanılacak en az bir denetleyici ve görünüm eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="873af-378">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="873af-379">Bir *Controllers* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="873af-379">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="873af-380">*Controllers* dizinine *HomeController.cs* adlı bir **Denetleyici sınıfı** ekleyin.</span><span class="sxs-lookup"><span data-stu-id="873af-380">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![Yeni öğe Ekle iletişim kutusu](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="873af-382">Bir *Görünümler* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="873af-382">Add a *Views* directory.</span></span>

* <span data-ttu-id="873af-383">Bir *Görünüm/giriş* dizini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="873af-383">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="873af-384">*Görünümler/giriş* dizinine *Index. cshtml* adlı bir **:::no-loc(Razor)::: Görünüm** ekleyin.</span><span class="sxs-lookup"><span data-stu-id="873af-384">Add a **:::no-loc(Razor)::: View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![Yeni öğe Ekle iletişim kutusu](mvc/_static/view.png)

<span data-ttu-id="873af-386">Proje yapısı aşağıda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="873af-386">The project structure is shown below:</span></span>

![WebApp1 dosyalarının ve dizinlerinin gösterildiği Çözüm Gezgini](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="873af-388">*Views/Home/Index. cshtml* dosyasının içeriğini aşağıdaki biçimlendirme ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="873af-388">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="873af-389">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="873af-389">Run the app.</span></span>

![Microsoft Edge 'de açık Web uygulaması](mvc/_static/hello-world.png)

<span data-ttu-id="873af-391">Daha fazla bilgi için bkz. [denetleyiciler](xref:mvc/controllers/actions) ve [Görünümler](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="873af-391">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="873af-392">Aşağıdaki işlev örnek ASP.NET MVC projesinden ASP.NET Core projesine geçiş gerektirir:</span><span class="sxs-lookup"><span data-stu-id="873af-392">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="873af-393">istemci tarafı içerik (CSS, yazı tipleri ve betikler)</span><span class="sxs-lookup"><span data-stu-id="873af-393">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="873af-394">denetleyiciler</span><span class="sxs-lookup"><span data-stu-id="873af-394">controllers</span></span>

* <span data-ttu-id="873af-395">görünümler</span><span class="sxs-lookup"><span data-stu-id="873af-395">views</span></span>

* <span data-ttu-id="873af-396">modeller</span><span class="sxs-lookup"><span data-stu-id="873af-396">models</span></span>

* <span data-ttu-id="873af-397">Paketleme</span><span class="sxs-lookup"><span data-stu-id="873af-397">bundling</span></span>

* <span data-ttu-id="873af-398">filtreler</span><span class="sxs-lookup"><span data-stu-id="873af-398">filters</span></span>

* <span data-ttu-id="873af-399">Oturum açma/kapatma :::no-loc(Identity)::: (Bu, sonraki öğreticide yapılır.)</span><span class="sxs-lookup"><span data-stu-id="873af-399">Log in/out, :::no-loc(Identity)::: (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="873af-400">Denetleyiciler ve görünümler</span><span class="sxs-lookup"><span data-stu-id="873af-400">Controllers and views</span></span>

* <span data-ttu-id="873af-401">Yöntemlerin her birini ASP.NET MVC 'den `HomeController` New öğesine kopyalayın `HomeController` .</span><span class="sxs-lookup"><span data-stu-id="873af-401">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="873af-402">ASP.NET MVC 'de, yerleşik şablonun denetleyici eylem yöntemi dönüş türü ' dir <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; ASP.NET Core MVC 'de, eylem yöntemleri `IActionResult` bunun yerine döndürülür.</span><span class="sxs-lookup"><span data-stu-id="873af-402">In ASP.NET MVC, the built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="873af-403">`ActionResult` uyguladığı `IActionResult` için, eylem yöntemlerinin dönüş türünü değiştirmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="873af-403">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="873af-404">ASP.NET MVC projesindeki *. cshtml* , *Contact. cshtml* ve *Index. cshtml* :::no-loc(Razor)::: Görünüm dosyalarını ASP.NET Core projesine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="873af-404">Copy the *About.cshtml* , *Contact.cshtml* , and *Index.cshtml* :::no-loc(Razor)::: view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="873af-405">Her yöntemi test et</span><span class="sxs-lookup"><span data-stu-id="873af-405">Test each method</span></span>

<span data-ttu-id="873af-406">Düzen dosyası ve stiller henüz geçirilmemiştir, bu nedenle işlenmiş görünümler yalnızca görünüm dosyalarındaki içeriği içerir.</span><span class="sxs-lookup"><span data-stu-id="873af-406">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="873af-407">Ve görünümleri için Düzen dosyası tarafından oluşturulan `About` Bağlantılar `Contact` henüz kullanılamayacak.</span><span class="sxs-lookup"><span data-stu-id="873af-407">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

* <span data-ttu-id="873af-408">Geçerli bağlantı noktası numarasını ASP.NET Core projesinde kullanılan bağlantı noktası numarasıyla değiştirerek, çalışan ASP.NET Core uygulamasındaki tarayıcıdan işlenmiş görünümleri çağırın.</span><span class="sxs-lookup"><span data-stu-id="873af-408">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="873af-409">Örneğin: `https://localhost:44375/home/about`.</span><span class="sxs-lookup"><span data-stu-id="873af-409">For example: `https://localhost:44375/home/about`.</span></span>

![Kişi sayfası](mvc/_static/contact-page.png)

<span data-ttu-id="873af-411">Stil ve menü öğelerinin eksikliğine göz önünde.</span><span class="sxs-lookup"><span data-stu-id="873af-411">Note the lack of styling and menu items.</span></span> <span data-ttu-id="873af-412">Stil, sonraki bölümde düzeltilecektir.</span><span class="sxs-lookup"><span data-stu-id="873af-412">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="873af-413">Statik içerik</span><span class="sxs-lookup"><span data-stu-id="873af-413">Static content</span></span>

<span data-ttu-id="873af-414">ASP.NET MVC 5 ve önceki sürümlerde, statik içerik Web projesinin kökünden barındırılır ve sunucu tarafı dosyalarıyla karıştı.</span><span class="sxs-lookup"><span data-stu-id="873af-414">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="873af-415">ASP.NET Core, statik içerik *Wwwroot* dizininde barındırılır.</span><span class="sxs-lookup"><span data-stu-id="873af-415">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="873af-416">ASP.NET MVC uygulamasındaki statik içeriği ASP.NET Core projesindeki *Wwwroot* dizinine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="873af-416">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="873af-417">Bu örnek dönüştürmede:</span><span class="sxs-lookup"><span data-stu-id="873af-417">In this sample conversion:</span></span>

* <span data-ttu-id="873af-418">ASP.NET MVC projesinden ASP.NET Core projesindeki *Wwwroot* dizinine MVC *. ico* dosyasını kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="873af-418">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="873af-419">ASP.NET MVC projesi, kendi stili için [önyükleme](https://getbootstrap.com/) kullanır ve önyükleme dosyalarını *içerik* ve *betikler* dizinlerinde depolar.</span><span class="sxs-lookup"><span data-stu-id="873af-419">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="873af-420">ASP.NET MVC projesini oluşturan şablon, düzen dosyasında önyüklenmesine başvurur ( *Görünümler/paylaşılan/_Layout. cshtml* ).</span><span class="sxs-lookup"><span data-stu-id="873af-420">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file ( *Views/Shared/_Layout.cshtml* ).</span></span> <span data-ttu-id="873af-421">*bootstrap.js* ve *Bootstrap. css* dosyaları, ASP.NET MVC projesinden yeni projedeki *Wwwroot* dizinine kopyalanabilir.</span><span class="sxs-lookup"><span data-stu-id="873af-421">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="873af-422">Bunun yerine, bu belge sonraki bölümde CDNs kullanarak önyükleme (ve diğer istemci tarafı kitaplıkları) için destek ekler.</span><span class="sxs-lookup"><span data-stu-id="873af-422">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="873af-423">Düzen dosyasını geçirme</span><span class="sxs-lookup"><span data-stu-id="873af-423">Migrate the layout file</span></span>

* <span data-ttu-id="873af-424">*_ViewStart. cshtml* dosyasını ASP.NET MVC projesinin *Görünümler* dizininden ASP.NET Core projenin *Görünümler* dizinine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="873af-424">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="873af-425">*_ViewStart. cshtml* dosyası ASP.NET Core MVC 'de değişmemiştir.</span><span class="sxs-lookup"><span data-stu-id="873af-425">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="873af-426">Bir *Görünüm/paylaşılan* dizin oluşturun.</span><span class="sxs-lookup"><span data-stu-id="873af-426">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="873af-427">*Isteğe bağlı:* *_ViewImports. cshtml* 'Yi *Fullaspnetcore* MVC projesinin *views* dizininden ASP.NET Core projenin *views* dizinine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="873af-427">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="873af-428">*_ViewImports. cshtml* dosyasındaki herhangi bir ad alanı bildirimini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="873af-428">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="873af-429">*_ViewImports. cshtml* dosyası, tüm görünüm dosyaları için ad alanları sağlar ve [etiket yardımcılarını](xref:mvc/views/tag-helpers/intro)getirir.</span><span class="sxs-lookup"><span data-stu-id="873af-429">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="873af-430">Etiket Yardımcıları yeni düzen dosyasında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="873af-430">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="873af-431">*_ViewImports. cshtml* dosyası ASP.NET Core için yenidir.</span><span class="sxs-lookup"><span data-stu-id="873af-431">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="873af-432">*_Layout. cshtml* dosyasını ASP.NET MVC projesinin *Görünümler/paylaşılan* dizininden ASP.NET Core projenin *Görünümler/paylaşılan* dizinine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="873af-432">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="873af-433">*_Layout. cshtml* dosyasını açın ve aşağıdaki değişiklikleri yapın (tamamlanan kod aşağıda gösterilmiştir):</span><span class="sxs-lookup"><span data-stu-id="873af-433">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="873af-434">`@Styles.Render("~/Content/css")` `<link>` *Bootstrap. css* ' nin yükleneceği bir öğeyle değiştirin (aşağıya bakın).</span><span class="sxs-lookup"><span data-stu-id="873af-434">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="873af-435">Kaldırın `@Scripts.Render("~/bundles/modernizr")` .</span><span class="sxs-lookup"><span data-stu-id="873af-435">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="873af-436">`@Html.Partial("_LoginPartial")`Çizgiyi açıklama (çizgi ile çevreleyin `@*...*@` ).</span><span class="sxs-lookup"><span data-stu-id="873af-436">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="873af-437">Daha fazla bilgi için bkz. [kimlik doğrulamasını geçirme ve :::no-loc(Identity)::: ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="873af-437">For more information, see [Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="873af-438">`@Scripts.Render("~/bundles/jquery")`Bir öğesiyle değiştirin `<script>` (aşağıya bakın).</span><span class="sxs-lookup"><span data-stu-id="873af-438">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="873af-439">`@Scripts.Render("~/bundles/bootstrap")`Bir öğesiyle değiştirin `<script>` (aşağıya bakın).</span><span class="sxs-lookup"><span data-stu-id="873af-439">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="873af-440">Önyükleme CSS ekleme için değiştirme biçimlendirmesi:</span><span class="sxs-lookup"><span data-stu-id="873af-440">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="873af-441">JQuery ve Bootstrap JavaScript ekleme için değiştirme biçimlendirmesi:</span><span class="sxs-lookup"><span data-stu-id="873af-441">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="873af-442">Güncelleştirilmiş *_Layout. cshtml* dosyası aşağıda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="873af-442">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="873af-443">Siteyi tarayıcıda görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="873af-443">View the site in the browser.</span></span> <span data-ttu-id="873af-444">Artık beklenen stillerle birlikte doğru şekilde yüklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="873af-444">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="873af-445">*Isteğe bağlı:* Yeni düzen dosyasını kullanmayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="873af-445">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="873af-446">Düzen dosyasını *Fullaspnetcore* projesinden kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="873af-446">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="873af-447">Yeni düzen dosyası [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) kullanır ve başka iyileştirmeler içerir.</span><span class="sxs-lookup"><span data-stu-id="873af-447">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="873af-448">Paketlemeyi ve küçültmeye göre yapılandırma</span><span class="sxs-lookup"><span data-stu-id="873af-448">Configure bundling and minification</span></span>

<span data-ttu-id="873af-449">Paketleme ve küçültmeye yönelik yapılandırma hakkında daha fazla bilgi için bkz. [paketleme ve küçültmeye](xref:client-side/bundling-and-minification)yönelik.</span><span class="sxs-lookup"><span data-stu-id="873af-449">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="873af-450">HTTP 500 hatalarını çözme</span><span class="sxs-lookup"><span data-stu-id="873af-450">Solve HTTP 500 errors</span></span>

<span data-ttu-id="873af-451">Sorunun kaynağı hakkında bilgi içermeyen bir HTTP 500 hata iletisine neden olabilecek birçok sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="873af-451">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="873af-452">Örneğin, *views/_ViewImports. cshtml* dosyası projede mevcut olmayan bir ad alanı içeriyorsa, bir http 500 hatası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="873af-452">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="873af-453">ASP.NET Core uygulamalarda varsayılan olarak, `UseDeveloperExceptionPage` uzantı öğesine eklenir `IApplicationBuilder` ve yapılandırma *geliştirme* sırasında yürütülür.</span><span class="sxs-lookup"><span data-stu-id="873af-453">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development* .</span></span> <span data-ttu-id="873af-454">Aşağıdaki kodda bir örnek görebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="873af-454">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="873af-455">ASP.NET Core işlenmeyen özel durumları HTTP 500 hata yanıtlarına dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="873af-455">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="873af-456">Normalde, sunucu hakkında potansiyel olarak hassas bilgilerin açıklanmasını engellemek için bu yanıtlara hata ayrıntıları dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="873af-456">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="873af-457">Daha fazla bilgi için bkz. [Geliştirici özel durum sayfası](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="873af-457">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="873af-458">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="873af-458">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
