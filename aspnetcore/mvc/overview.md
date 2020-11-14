---
title: ASP.NET Core MVC’ye Genel Bakış
author: ardalis
description: ASP.NET Core MVC 'nin, Model-View-Controller tasarım modelini kullanarak Web uygulamaları ve API 'Ler oluşturmak için zengin bir çatı olduğunu öğrenin.
ms.author: riande
ms.date: 02/12/2020
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
uid: mvc/overview
ms.openlocfilehash: 4f0e98057c03769a672ef350cc3ec724a4f7e721
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431075"
---
# <a name="overview-of-aspnet-core-mvc"></a><span data-ttu-id="38f8f-103">ASP.NET Core MVC’ye Genel Bakış</span><span class="sxs-lookup"><span data-stu-id="38f8f-103">Overview of ASP.NET Core MVC</span></span>

<span data-ttu-id="38f8f-104">[Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="38f8f-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="38f8f-105">ASP.NET Core MVC, Model-View-Controller tasarım modelini kullanarak Web uygulamaları ve API 'Ler oluşturmaya yönelik zengin bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-105">ASP.NET Core MVC is a rich framework for building web apps and APIs using the Model-View-Controller design pattern.</span></span>

## <a name="what-is-the-mvc-pattern"></a><span data-ttu-id="38f8f-106">MVC deseninin anlamı nedir?</span><span class="sxs-lookup"><span data-stu-id="38f8f-106">What is the MVC pattern?</span></span>

<span data-ttu-id="38f8f-107">Model-View-Controller (MVC) mimari modeli, bir uygulamayı üç ana bileşen grubuna ayırır: modeller, görünümler ve denetleyiciler.</span><span class="sxs-lookup"><span data-stu-id="38f8f-107">The Model-View-Controller (MVC) architectural pattern separates an application into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="38f8f-108">Bu model [, kaygıları ayrımı](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)elde etmeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="38f8f-108">This pattern helps to achieve [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span> <span data-ttu-id="38f8f-109">Bu modeli kullanarak Kullanıcı istekleri, kullanıcı eylemlerini gerçekleştirmek ve/veya sorguların sonuçlarını almak için modeliyle çalışmaktan sorumlu bir denetleyiciye yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-109">Using this pattern, user requests are routed to a Controller which is responsible for working with the Model to perform user actions and/or retrieve results of queries.</span></span> <span data-ttu-id="38f8f-110">Denetleyici, kullanıcıya görüntülenecek görünümü seçer ve gereken model verilerini sağlar.</span><span class="sxs-lookup"><span data-stu-id="38f8f-110">The Controller chooses the View to display to the user, and provides it with any Model data it requires.</span></span>

<span data-ttu-id="38f8f-111">Aşağıdaki diyagramda üç ana bileşen gösterilmektedir ve bunlar diğerlerine başvuramazlar:</span><span class="sxs-lookup"><span data-stu-id="38f8f-111">The following diagram shows the three main components and which ones reference the others:</span></span>

![MVC kalıbı](overview/_static/mvc.png)

<span data-ttu-id="38f8f-113">Bu sorumlulukların bu şekilde çıkarılması, tek bir işi olan bir şeyi (model, görünüm veya denetleyici) kodlanmasını, hata ayıklamayı ve test etmek daha kolay olduğundan, uygulamayı karmaşıklık bakımından ölçeklendirmenize yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="38f8f-113">This delineation of responsibilities helps you scale the application in terms of complexity because it's easier to code, debug, and test something (model, view, or controller) that has a single job.</span></span> <span data-ttu-id="38f8f-114">Bu üç alandan oluşan iki veya daha fazla bağımlılığı kapsayan bağımlılıklara sahip kodu güncelleştirmek, test etmek ve hata ayıklamak daha zordur.</span><span class="sxs-lookup"><span data-stu-id="38f8f-114">It's more difficult to update, test, and debug code that has dependencies spread across two or more of these three areas.</span></span> <span data-ttu-id="38f8f-115">Örneğin, Kullanıcı arabirimi mantığı iş mantığından daha sık değişmeyi eğilimi gösterir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-115">For example, user interface logic tends to change more frequently than business logic.</span></span> <span data-ttu-id="38f8f-116">Sunum kodu ve iş mantığı tek bir nesnede birleştirilirse, Kullanıcı arabirimi her değiştirildiğinde iş mantığı içeren bir nesne değiştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-116">If presentation code and business logic are combined in a single object, an object containing business logic must be modified every time the user interface is changed.</span></span> <span data-ttu-id="38f8f-117">Bu genellikle hataları tanıtır ve her bir en az kullanıcı arabirimi değişikliğinden sonra iş mantığının yeniden test edilmesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-117">This often introduces errors and requires the retesting of business logic after every minimal user interface change.</span></span>

> [!NOTE]
> <span data-ttu-id="38f8f-118">Hem görünüm hem de denetleyici modele bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="38f8f-118">Both the view and the controller depend on the model.</span></span> <span data-ttu-id="38f8f-119">Ancak, model ne görünüm ne de yoksa denetleyiciye bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="38f8f-119">However, the model depends on neither the view nor the controller.</span></span> <span data-ttu-id="38f8f-120">Bu, ayrımı önemli avantajlarından biridir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-120">This is one of the key benefits of the separation.</span></span> <span data-ttu-id="38f8f-121">Bu ayrım, modelin görsel sunudan bağımsız olarak oluşturulup test etmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="38f8f-121">This separation allows the model to be built and tested independent of the visual presentation.</span></span>

### <a name="model-responsibilities"></a><span data-ttu-id="38f8f-122">Model sorumlulukları</span><span class="sxs-lookup"><span data-stu-id="38f8f-122">Model Responsibilities</span></span>

<span data-ttu-id="38f8f-123">MVC uygulamasındaki model, uygulamanın durumunu ve bunun gerçekleştirilmesi gereken tüm iş mantığını veya işlemlerini temsil eder.</span><span class="sxs-lookup"><span data-stu-id="38f8f-123">The Model in an MVC application represents the state of the application and any business logic or operations that should be performed by it.</span></span> <span data-ttu-id="38f8f-124">İş mantığı, uygulamanın durumunu kalıcı hale getirme için herhangi bir uygulama mantığıyla birlikte, modelde kapsüllenmelidir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-124">Business logic should be encapsulated in the model, along with any implementation logic for persisting the state of the application.</span></span> <span data-ttu-id="38f8f-125">Türü kesin belirlenmiş görünümler genellikle bu görünümde görüntülenecek verileri içerecek şekilde tasarlanan ViewModel türlerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="38f8f-125">Strongly-typed views typically use ViewModel types designed to contain the data to display on that view.</span></span> <span data-ttu-id="38f8f-126">Denetleyici bu ViewModel örneklerini modelden oluşturur ve doldurur.</span><span class="sxs-lookup"><span data-stu-id="38f8f-126">The controller creates and populates these ViewModel instances from the model.</span></span>

### <a name="view-responsibilities"></a><span data-ttu-id="38f8f-127">Sorumlulukları görüntüle</span><span class="sxs-lookup"><span data-stu-id="38f8f-127">View Responsibilities</span></span>

<span data-ttu-id="38f8f-128">Görünümler, kullanıcı arabiriminden içerik sunmadan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="38f8f-128">Views are responsible for presenting content through the user interface.</span></span> <span data-ttu-id="38f8f-129">HTML biçimlendirmesinde .NET kodu eklemek için [ Razor Görünüm altyapısını](#razor-view-engine) kullanır.</span><span class="sxs-lookup"><span data-stu-id="38f8f-129">They use the [Razor view engine](#razor-view-engine) to embed .NET code in HTML markup.</span></span> <span data-ttu-id="38f8f-130">Görünümler içinde en az mantık olmalıdır ve içerdikleri tüm mantığın içerik sunumu ile ilişkilendirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-130">There should be minimal logic within views, and any logic in them should relate to presenting content.</span></span> <span data-ttu-id="38f8f-131">Karmaşık bir modelden veri görüntülemek için dosyaları görüntüle bölümünde harika bir mantık kullanımı gereksinimini fark ederseniz, görünümü basitleştirmek için bir [Görünüm bileşeni](views/view-components.md), ViewModel veya görünüm şablonu kullanmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="38f8f-131">If you find the need to perform a great deal of logic in view files in order to display data from a complex model, consider using a [View Component](views/view-components.md), ViewModel, or view template to simplify the view.</span></span>

### <a name="controller-responsibilities"></a><span data-ttu-id="38f8f-132">Denetleyici sorumlulukları</span><span class="sxs-lookup"><span data-stu-id="38f8f-132">Controller Responsibilities</span></span>

<span data-ttu-id="38f8f-133">Denetleyiciler, kullanıcı etkileşimini işleyen, modeliyle çalışan ve sonunda işlenecek bir görünüm olan bileşenleridir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-133">Controllers are the components that handle user interaction, work with the model, and ultimately select a view to render.</span></span> <span data-ttu-id="38f8f-134">MVC uygulamasında, görünüm yalnızca bilgileri görüntüler; denetleyici, Kullanıcı girişini ve etkileşimini işler ve yanıtlar.</span><span class="sxs-lookup"><span data-stu-id="38f8f-134">In an MVC application, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="38f8f-135">MVC modelinde, denetleyici ilk giriş noktasıdır ve hangi model türlerinin birlikte çalışacağını ve hangi görünümün işleneceğini seçmekten sorumludur (Bu nedenle adı, uygulamanın belirli bir istek için nasıl yanıt verdiğini denetler).</span><span class="sxs-lookup"><span data-stu-id="38f8f-135">In the MVC pattern, the controller is the initial entry point, and is responsible for selecting which model types to work with and which view to render (hence its name - it controls how the app responds to a given request).</span></span>

> [!NOTE]
> <span data-ttu-id="38f8f-136">Denetleyiciler çok fazla sorumluluk tarafından aşırı karmaşık olmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="38f8f-136">Controllers shouldn't be overly complicated by too many responsibilities.</span></span> <span data-ttu-id="38f8f-137">Denetleyici mantığının aşırı karmaşık hale gelmesini sağlamak için, denetleyiciden ve etki alanı modeline kadar iş mantığı gönderin.</span><span class="sxs-lookup"><span data-stu-id="38f8f-137">To keep controller logic from becoming overly complex, push business logic out of the controller and into the domain model.</span></span>

>[!TIP]
> <span data-ttu-id="38f8f-138">Denetleyici eylemlerinizin aynı tür eylemleri sıklıkla gerçekleştirmesini fark ederseniz, bu ortak eylemleri [filtrelere](#filters)taşıyın.</span><span class="sxs-lookup"><span data-stu-id="38f8f-138">If you find that your controller actions frequently perform the same kinds of actions, move these common actions into [filters](#filters).</span></span>

## <a name="what-is-aspnet-core-mvc"></a><span data-ttu-id="38f8f-139">ASP.NET Core MVC nedir?</span><span class="sxs-lookup"><span data-stu-id="38f8f-139">What is ASP.NET Core MVC</span></span>

<span data-ttu-id="38f8f-140">ASP.NET Core MVC çerçevesi, ASP.NET Core birlikte kullanılmak üzere en iyi duruma getirilmiş hafif, açık kaynaklı ve yüksek düzeyde bir sunum çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-140">The ASP.NET Core MVC framework is a lightweight, open source, highly testable presentation framework optimized for use with ASP.NET Core.</span></span>

<span data-ttu-id="38f8f-141">ASP.NET Core MVC, sorunların temiz bir şekilde ayrılmasını sağlayan dinamik Web siteleri oluşturmak için desen tabanlı bir yol sağlar.</span><span class="sxs-lookup"><span data-stu-id="38f8f-141">ASP.NET Core MVC provides a patterns-based way to build dynamic websites that enables a clean separation of concerns.</span></span> <span data-ttu-id="38f8f-142">Biçimlendirme üzerinde tam denetim elde etmenizi sağlar, TDD kullanımı kolay geliştirmeyi destekler ve en son web standartlarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="38f8f-142">It gives you full control over markup, supports TDD-friendly development and uses the latest web standards.</span></span>

## <a name="features"></a><span data-ttu-id="38f8f-143">Özellikler</span><span class="sxs-lookup"><span data-stu-id="38f8f-143">Features</span></span>

<span data-ttu-id="38f8f-144">ASP.NET Core MVC şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="38f8f-144">ASP.NET Core MVC includes the following:</span></span>

* [<span data-ttu-id="38f8f-145">Yönlendirme</span><span class="sxs-lookup"><span data-stu-id="38f8f-145">Routing</span></span>](#routing)
* [<span data-ttu-id="38f8f-146">Model bağlama</span><span class="sxs-lookup"><span data-stu-id="38f8f-146">Model binding</span></span>](#model-binding)
* [<span data-ttu-id="38f8f-147">Model doğrulaması</span><span class="sxs-lookup"><span data-stu-id="38f8f-147">Model validation</span></span>](#model-validation)
* [<span data-ttu-id="38f8f-148">Bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="38f8f-148">Dependency injection</span></span>](../fundamentals/dependency-injection.md)
* [<span data-ttu-id="38f8f-149">Filtreler</span><span class="sxs-lookup"><span data-stu-id="38f8f-149">Filters</span></span>](#filters)
* [<span data-ttu-id="38f8f-150">Alanlar</span><span class="sxs-lookup"><span data-stu-id="38f8f-150">Areas</span></span>](#areas)
* [<span data-ttu-id="38f8f-151">Web API'leri</span><span class="sxs-lookup"><span data-stu-id="38f8f-151">Web APIs</span></span>](#web-apis)
* [<span data-ttu-id="38f8f-152">Test edilebilirlik</span><span class="sxs-lookup"><span data-stu-id="38f8f-152">Testability</span></span>](#testability)
* [<span data-ttu-id="38f8f-153">Razor altyapıyı görüntüle</span><span class="sxs-lookup"><span data-stu-id="38f8f-153">Razor view engine</span></span>](#razor-view-engine)
* [<span data-ttu-id="38f8f-154">Türü kesin belirlenmiş görünümler</span><span class="sxs-lookup"><span data-stu-id="38f8f-154">Strongly typed views</span></span>](#strongly-typed-views)
* [<span data-ttu-id="38f8f-155">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="38f8f-155">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="38f8f-156">Bileşenleri görüntüle</span><span class="sxs-lookup"><span data-stu-id="38f8f-156">View Components</span></span>](#view-components)

### <a name="routing"></a><span data-ttu-id="38f8f-157">Yönlendirme</span><span class="sxs-lookup"><span data-stu-id="38f8f-157">Routing</span></span>

<span data-ttu-id="38f8f-158">ASP.NET Core MVC, gelişmiş ve aranabilir URL 'Ler içeren uygulamalar oluşturmanıza olanak tanıyan güçlü bir URL eşleme bileşeni olan [ASP.NET Core yönlendirmenin](../fundamentals/routing.md)üzerine kurulmuştur.</span><span class="sxs-lookup"><span data-stu-id="38f8f-158">ASP.NET Core MVC is built on top of [ASP.NET Core's routing](../fundamentals/routing.md), a powerful URL-mapping component that lets you build applications that have comprehensible and searchable URLs.</span></span> <span data-ttu-id="38f8f-159">Bu, uygulamanızın URL adlandırma düzenlerini, Web sunucunuzdaki dosyaların nasıl düzenleneceğine bakılmaksızın, arama motoru iyileştirmesi (SEO) ve bağlantı oluşturma için iyi bir şekilde tanımlamanıza olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="38f8f-159">This enables you to define your application's URL naming patterns that work well for search engine optimization (SEO) and for link generation, without regard for how the files on your web server are organized.</span></span> <span data-ttu-id="38f8f-160">Yol değer kısıtlamalarını, Varsayılanları ve isteğe bağlı değerleri destekleyen uygun bir yol şablonu sözdizimi kullanarak rotalarınızı tanımlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="38f8f-160">You can define your routes using a convenient route template syntax that supports route value constraints, defaults and optional values.</span></span>

<span data-ttu-id="38f8f-161">*Kural tabanlı yönlendirme* , uygulamanızın kabul ettiği URL biçimlerini ve bu biçimlerin her birinin verilen denetleyicide belirli bir eylem yöntemiyle nasıl eşlendiğini genel olarak tanımlamanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="38f8f-161">*Convention-based routing* enables you to globally define the URL formats that your application accepts and how each of those formats maps to a specific action method on given controller.</span></span> <span data-ttu-id="38f8f-162">Gelen bir istek alındığında, yönlendirme altyapısı URL 'YI ayrıştırır ve tanımlanan URL biçimlerinden biriyle eşleştirir ve ardından ilişkili denetleyicinin eylem yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="38f8f-162">When an incoming request is received, the routing engine parses the URL and matches it to one of the defined URL formats, and then calls the associated controller's action method.</span></span>

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="38f8f-163">*Öznitelik yönlendirme* , denetleyicilerinizi ve eylemlerinizi uygulamanızın yollarını tanımlayan özniteliklerle süsleyerek yönlendirme bilgilerini belirtmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="38f8f-163">*Attribute routing* enables you to specify routing information by decorating your controllers and actions with attributes that define your application's routes.</span></span> <span data-ttu-id="38f8f-164">Bu, yol tanımlarınızın ilişkili oldukları denetleyicinin ve eylemin yanına yerleştirildiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-164">This means that your route definitions are placed next to the controller and action with which they're associated.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
    [HttpGet("{id}")]
    public IActionResult GetProduct(int id)
    {
      ...
    }
}
```

### <a name="model-binding"></a><span data-ttu-id="38f8f-165">Model bağlama</span><span class="sxs-lookup"><span data-stu-id="38f8f-165">Model binding</span></span>

<span data-ttu-id="38f8f-166">ASP.NET Core MVC [model bağlama](models/model-binding.md) , istemci isteği verilerini (form değerleri, rota verileri, sorgu dizesi PARAMETRELERI, http üstbilgileri) denetleyicinin işleyebileceği nesnelere dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="38f8f-166">ASP.NET Core MVC [model binding](models/model-binding.md) converts client request data  (form values, route data, query string parameters, HTTP headers) into objects that the controller can handle.</span></span> <span data-ttu-id="38f8f-167">Sonuç olarak, denetleyici mantığınızın gelen istek verilerini oluşturma işini yapması gerekmez; Yalnızca Eylem yöntemlerine parametre olarak verileri içerir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-167">As a result, your controller logic doesn't have to do the work of figuring out the incoming request data; it simply has the data as parameters to its action methods.</span></span>

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
```

### <a name="model-validation"></a><span data-ttu-id="38f8f-168">Model doğrulaması</span><span class="sxs-lookup"><span data-stu-id="38f8f-168">Model validation</span></span>

<span data-ttu-id="38f8f-169">ASP.NET Core MVC, model nesneniz veri ek açıklaması doğrulama öznitelikleriyle süsleyerek [doğrulamayı](models/validation.md) destekler.</span><span class="sxs-lookup"><span data-stu-id="38f8f-169">ASP.NET Core MVC supports [validation](models/validation.md) by decorating your model object with data annotation validation attributes.</span></span> <span data-ttu-id="38f8f-170">Doğrulama öznitelikleri, değerler sunucuya gönderilmeden önce istemci tarafında ve denetleyici eylemi çağrılmadan önce sunucuda denetlenir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-170">The validation attributes are checked on the client side before values are posted to the server, as well as on the server before the controller action is called.</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
public class LoginViewModel
{
    [Required]
    [EmailAddress]
    public string Email { get; set; }

    [Required]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    [Display(Name = "Remember me?")]
    public bool RememberMe { get; set; }
}
```

<span data-ttu-id="38f8f-171">Bir denetleyici eylemi:</span><span class="sxs-lookup"><span data-stu-id="38f8f-171">A controller action:</span></span>

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
{
    if (ModelState.IsValid)
    {
      // work with the model
    }
    // At this point, something failed, redisplay form
    return View(model);
}
```

<span data-ttu-id="38f8f-172">Çerçeve, istek verilerini hem istemcide hem de sunucuda doğrulamayı işler.</span><span class="sxs-lookup"><span data-stu-id="38f8f-172">The framework handles validating request data both on the client and on the server.</span></span> <span data-ttu-id="38f8f-173">Model türlerinde belirtilen doğrulama mantığı, işlenen görünümlere obtrusive ek açıklamaları olarak eklenir ve [jQuery doğrulaması](https://jqueryvalidation.org/)ile tarayıcıda zorlanır.</span><span class="sxs-lookup"><span data-stu-id="38f8f-173">Validation logic specified on model types is added to the rendered views as unobtrusive annotations and is enforced in the browser with [jQuery Validation](https://jqueryvalidation.org/).</span></span>

### <a name="dependency-injection"></a><span data-ttu-id="38f8f-174">Bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="38f8f-174">Dependency injection</span></span>

<span data-ttu-id="38f8f-175">ASP.NET Core, [bağımlılık ekleme (dı)](../fundamentals/dependency-injection.md)için yerleşik desteğe sahiptir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-175">ASP.NET Core has built-in support for [dependency injection (DI)](../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="38f8f-176">ASP.NET Core MVC 'de, [denetleyiciler](controllers/dependency-injection.md) oluşturucuları aracılığıyla gerekli hizmetleri talep edebilir ve bu kullanıcıların [Açık bağımlılıklar ilkesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)izleyebilmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="38f8f-176">In ASP.NET Core MVC, [controllers](controllers/dependency-injection.md) can request needed services through their constructors, allowing them to follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

<span data-ttu-id="38f8f-177">Uygulamanız Ayrıca, yönergeyi [görüntüleme dosyalarını kullanarak bağımlılık ekleme](views/dependency-injection.md)işlemini de kullanabilir `@inject` :</span><span class="sxs-lookup"><span data-stu-id="38f8f-177">Your app can also use [dependency injection in view files](views/dependency-injection.md), using the `@inject` directive:</span></span>

```cshtml
@inject SomeService ServiceName

<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ServiceName.GetTitle</title>
</head>
<body>
    <h1>@ServiceName.GetTitle</h1>
</body>
</html>
```

### <a name="filters"></a><span data-ttu-id="38f8f-178">Filtreler</span><span class="sxs-lookup"><span data-stu-id="38f8f-178">Filters</span></span>

<span data-ttu-id="38f8f-179">[Filtreler](controllers/filters.md) , geliştiricilerin özel durum işleme veya yetkilendirme gibi çapraz sorunları yalıtmalarına yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="38f8f-179">[Filters](controllers/filters.md) help developers encapsulate cross-cutting concerns, like exception handling or authorization.</span></span> <span data-ttu-id="38f8f-180">Filtreler, eylem yöntemleri için özel ön ve son işlem dışı mantığı çalıştırmayı etkinleştirir ve belirli bir istek için yürütme işlem hattının içindeki belirli noktalarda çalışacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-180">Filters enable running custom pre- and post-processing logic for action methods, and can be configured to run at certain points within the execution pipeline for a given request.</span></span> <span data-ttu-id="38f8f-181">Filtreler, denetleyicilere veya eylemlere öznitelik olarak uygulanabilir (veya küresel olarak çalıştırılabilir).</span><span class="sxs-lookup"><span data-stu-id="38f8f-181">Filters can be applied to controllers or actions as attributes (or can be run globally).</span></span> <span data-ttu-id="38f8f-182">Birçok filtre (gibi `Authorize` ) çerçeveye dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-182">Several filters (such as `Authorize`) are included in the framework.</span></span> <span data-ttu-id="38f8f-183">`[Authorize]` , MVC yetkilendirme filtrelerini oluşturmak için kullanılan özniteliktir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-183">`[Authorize]` is the attribute that is used to create MVC authorization filters.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
```

### <a name="areas"></a><span data-ttu-id="38f8f-184">Alanlar</span><span class="sxs-lookup"><span data-stu-id="38f8f-184">Areas</span></span>

<span data-ttu-id="38f8f-185">[Bölgeler](controllers/areas.md) , büyük BIR ASP.NET Core MVC web uygulamasını daha küçük işlevsel gruplandırmalar halinde bölümlemek için bir yol sağlar.</span><span class="sxs-lookup"><span data-stu-id="38f8f-185">[Areas](controllers/areas.md) provide a way to partition a large ASP.NET Core MVC Web app into smaller functional groupings.</span></span> <span data-ttu-id="38f8f-186">Bir alan, bir uygulamanın içindeki bir MVC yapısıdır.</span><span class="sxs-lookup"><span data-stu-id="38f8f-186">An area is an MVC structure inside an application.</span></span> <span data-ttu-id="38f8f-187">MVC projesinde model, denetleyici ve görünüm gibi mantıksal bileşenler farklı klasörlerde tutulur ve MVC bu bileşenler arasındaki ilişkiyi oluşturmak için adlandırma kurallarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="38f8f-187">In an MVC project, logical components like Model, Controller, and View are kept in different folders, and MVC uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="38f8f-188">Büyük bir uygulama için, uygulamayı işlevlerin ayrı üst düzey alanlarında bölümlemek avantajlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-188">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="38f8f-189">Örneğin, kullanıma alma, faturalandırma ve arama gibi birden çok iş birimi içeren bir e-ticaret uygulaması. Bu birimlerin her birinin kendi mantıksal bileşen görünümleri, denetleyicileri ve modelleri vardır.</span><span class="sxs-lookup"><span data-stu-id="38f8f-189">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search etc. Each of these units have their own logical component views, controllers, and models.</span></span>

### <a name="web-apis"></a><span data-ttu-id="38f8f-190">Web API'leri</span><span class="sxs-lookup"><span data-stu-id="38f8f-190">Web APIs</span></span>

<span data-ttu-id="38f8f-191">Web siteleri oluşturmak için harika bir platform olmanın yanı sıra, ASP.NET Core MVC, Web API 'Leri oluşturmaya yönelik harika destek içerir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-191">In addition to being a great platform for building web sites, ASP.NET Core MVC has great support for building Web APIs.</span></span> <span data-ttu-id="38f8f-192">Tarayıcılar ve mobil cihazlar dahil olmak üzere çok çeşitli istemcilere ulaşan hizmetler oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="38f8f-192">You can build services that reach a broad range of clients including browsers and mobile devices.</span></span>

<span data-ttu-id="38f8f-193">Framework, verileri JSON veya XML olarak [biçimlendirmeye](xref:web-api/advanced/formatting) yönelik yerleşik destek ile http içerik anlaşması için destek içerir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-193">The framework includes support for HTTP content-negotiation with built-in support to [format data](xref:web-api/advanced/formatting) as JSON or XML.</span></span> <span data-ttu-id="38f8f-194">Kendi biçimlerinizin desteğini eklemek için [özel](xref:web-api/advanced/custom-formatters) biçimleri yazın.</span><span class="sxs-lookup"><span data-stu-id="38f8f-194">Write [custom formatters](xref:web-api/advanced/custom-formatters) to add support for your own formats.</span></span>

<span data-ttu-id="38f8f-195">Hiper medya desteğini etkinleştirmek için bağlantı oluşturma kullanın.</span><span class="sxs-lookup"><span data-stu-id="38f8f-195">Use link generation to enable support for hypermedia.</span></span> <span data-ttu-id="38f8f-196">Web API 'lerinizin birden çok Web uygulaması arasında paylaşılabilmesi için, [çıkış noktaları arası kaynak paylaşımı (CORS)](https://www.w3.org/TR/cors/) desteğini kolayca etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="38f8f-196">Easily enable support for [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) so that your Web APIs can be shared across multiple Web applications.</span></span>

### <a name="testability"></a><span data-ttu-id="38f8f-197">Test edilebilirlik</span><span class="sxs-lookup"><span data-stu-id="38f8f-197">Testability</span></span>

<span data-ttu-id="38f8f-198">Çerçevenin arabirimlerin ve bağımlılık ekleme özelliğinin kullanımı, birim testine uygun hale getirir ve Framework, [tümleştirme testlerini](xref:test/integration-tests) hızlı ve kolay hale getirmek için özellikler (Entity Framework Için bir testhost ve InMemory sağlayıcısı gibi) içerir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-198">The framework's use of interfaces and dependency injection make it well-suited to unit testing, and the framework includes features (like a TestHost and InMemory provider for Entity Framework) that make [integration tests](xref:test/integration-tests) quick and easy as well.</span></span> <span data-ttu-id="38f8f-199">[Denetleyici mantığını test etme](controllers/testing.md)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="38f8f-199">Learn more about [how to test controller logic](controllers/testing.md).</span></span>

### <a name="no-locrazor-view-engine"></a><span data-ttu-id="38f8f-200">Razor altyapıyı görüntüle</span><span class="sxs-lookup"><span data-stu-id="38f8f-200">Razor view engine</span></span>

<span data-ttu-id="38f8f-201">[MVC görünümlerini ASP.NET Core](views/overview.md) görünümleri oluşturmak için [ Razor Görünüm altyapısını](views/razor.md) kullanın.</span><span class="sxs-lookup"><span data-stu-id="38f8f-201">[ASP.NET Core MVC views](views/overview.md) use the [Razor view engine](views/razor.md) to render views.</span></span> <span data-ttu-id="38f8f-202">Razor , katıştırılmış C# kodu kullanarak görünümleri tanımlamaya yönelik kompakt, ifade ve akışkan şablonu biçimlendirme dilidir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-202">Razor is a compact, expressive and fluid template markup language for defining views using embedded C# code.</span></span> <span data-ttu-id="38f8f-203">Razor , sunucusunda dinamik olarak Web içeriği oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="38f8f-203">Razor is used to dynamically generate web content on the server.</span></span> <span data-ttu-id="38f8f-204">Sunucu kodunu istemci tarafı içeriğiyle ve kodla düzgün bir şekilde karıştırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="38f8f-204">You can cleanly mix server code with client side content and code.</span></span>

```cshtml
<ul>
    @for (int i = 0; i < 5; i++) {
        <li>List item @i</li>
    }
</ul>
```

<span data-ttu-id="38f8f-205">RazorGörünüm altyapısını kullanarak [düzenler](views/layout.md), [kısmi görünümler](views/partial.md) ve değiştirilebilir bölümler tanımlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="38f8f-205">Using the Razor view engine you can define [layouts](views/layout.md), [partial views](views/partial.md) and replaceable sections.</span></span>

### <a name="strongly-typed-views"></a><span data-ttu-id="38f8f-206">Türü kesin belirlenmiş görünümler</span><span class="sxs-lookup"><span data-stu-id="38f8f-206">Strongly typed views</span></span>

<span data-ttu-id="38f8f-207">Razor MVC 'deki görünümler modelinize göre kesin bir şekilde yazılabilir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-207">Razor views in MVC can be strongly typed based on your model.</span></span> <span data-ttu-id="38f8f-208">Denetleyiciler, görünümlerinizin tür denetlemesi ve IntelliSense desteği olmasını sağlayan görünümlere kesin olarak belirlenmiş bir model geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-208">Controllers can pass a strongly typed model to views enabling your views to have type checking and IntelliSense support.</span></span>

<span data-ttu-id="38f8f-209">Örneğin, aşağıdaki görünüm türünde bir model işler `IEnumerable<Product>` :</span><span class="sxs-lookup"><span data-stu-id="38f8f-209">For example, the following view renders a model of type `IEnumerable<Product>`:</span></span>

```cshtml
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a><span data-ttu-id="38f8f-210">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="38f8f-210">Tag Helpers</span></span>

<span data-ttu-id="38f8f-211">[Etiket Yardımcıları](views/tag-helpers/intro.md) , sunucu tarafı kodunun dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="38f8f-211">[Tag Helpers](views/tag-helpers/intro.md) enable server side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="38f8f-212">Etiket Yardımcıları kullanarak özel Etiketler tanımlayabilir (örneğin, `<environment>` ) veya varolan etiketlerin davranışını değiştirebilirsiniz (örneğin, `<label>` ).</span><span class="sxs-lookup"><span data-stu-id="38f8f-212">You can use tag helpers to define custom tags (for example, `<environment>`) or to modify the behavior of existing tags (for example, `<label>`).</span></span> <span data-ttu-id="38f8f-213">Etiket Yardımcıları, öğe adı ve öznitelikleri temelinde belirli öğelere bağlanır.</span><span class="sxs-lookup"><span data-stu-id="38f8f-213">Tag Helpers bind to specific elements based on the element name and its attributes.</span></span> <span data-ttu-id="38f8f-214">Bunlar, hala HTML düzenlemesi deneyimini korurken sunucu tarafı işlemenin avantajlarını sağlar.</span><span class="sxs-lookup"><span data-stu-id="38f8f-214">They provide the benefits of server-side rendering while still preserving an HTML editing experience.</span></span>

<span data-ttu-id="38f8f-215">Yaygın görevler için, genel GitHub depolarında ve NuGet paketleri olarak formlar, bağlantılar, yükleme varlıkları ve daha fazlasını ve daha fazlasını oluşturma gibi birçok yerleşik etiket yardımcıları vardır.</span><span class="sxs-lookup"><span data-stu-id="38f8f-215">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="38f8f-216">Etiket Yardımcıları C# dilinde yazılır ve öğe adı, öznitelik adı veya üst etikete göre HTML öğelerini hedefler.</span><span class="sxs-lookup"><span data-stu-id="38f8f-216">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="38f8f-217">Örneğin, yerleşik Linakghelper, eyleminin bir bağlantısını oluşturmak için kullanılabilir `Login` `AccountsController` :</span><span class="sxs-lookup"><span data-stu-id="38f8f-217">For example, the built-in LinkTagHelper can be used to create a link to the `Login` action of the `AccountsController`:</span></span>

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

<span data-ttu-id="38f8f-218">, `EnvironmentTagHelper` Geliştirme, hazırlama veya üretim gibi çalışma zamanı ortamına göre görünümlerinizde farklı betikler (örneğin, ham veya küçültülmüş) dahil etmek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="38f8f-218">The `EnvironmentTagHelper` can be used to include different scripts in your views (for example, raw or minified) based on the runtime environment, such as Development, Staging, or Production:</span></span>

```cshtml
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.1.4.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery">
    </script>
</environment>
```

<span data-ttu-id="38f8f-219">Etiket Yardımcıları, HTML ve biçimlendirme oluşturmaya yönelik zengin bir IntelliSense ortamı ve HTML kullanımı kolay bir geliştirme deneyimi sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="38f8f-219">Tag Helpers provide an HTML-friendly development experience and a rich IntelliSense environment for creating HTML and Razor markup.</span></span> <span data-ttu-id="38f8f-220">Yerleşik etiket yardımcıların çoğu, var olan HTML öğelerini hedefleyin ve öğesi için sunucu tarafı öznitelikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="38f8f-220">Most of the built-in Tag Helpers target existing HTML elements and provide server-side attributes for the element.</span></span>

### <a name="view-components"></a><span data-ttu-id="38f8f-221">Bileşenleri görüntüle</span><span class="sxs-lookup"><span data-stu-id="38f8f-221">View Components</span></span>

<span data-ttu-id="38f8f-222">[Görünüm bileşenleri](views/view-components.md) , işleme mantığını paketlemenize ve uygulamanın tamamında yeniden kullanmanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="38f8f-222">[View Components](views/view-components.md) allow you to package rendering logic and reuse it throughout the application.</span></span> <span data-ttu-id="38f8f-223">Bunlar, [kısmen görünümlere](views/partial.md)benzer ancak ilişkili mantığa benzer.</span><span class="sxs-lookup"><span data-stu-id="38f8f-223">They're similar to [partial views](views/partial.md), but with associated logic.</span></span>

## <a name="compatibility-version"></a><span data-ttu-id="38f8f-224">Uyumluluk sürümü</span><span class="sxs-lookup"><span data-stu-id="38f8f-224">Compatibility version</span></span>

<span data-ttu-id="38f8f-225"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>Yöntemi, bir uygulamanın ASP.NET Core MVC 2,1 veya sonraki bir sürümde sunulmuş olabilecek davranış değişikliklerinin kabul etmesine veya devre dışı olmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="38f8f-225">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="38f8f-226">Daha fazla bilgi için bkz. <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="38f8f-226">For more information, see <xref:mvc/compatibility-version>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="38f8f-227">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="38f8f-227">Additional resources</span></span>

* <span data-ttu-id="38f8f-228">[ASP.NET Core MVC Için Mysınanan. AspNetCore. Mvc-Floent test Kitaplığı](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): MVC ve Web API uygulamalarını test etmek için akıcı bir arabirim sağlayan, türü kesin belirlenmiş birim testi kitaplığı.</span><span class="sxs-lookup"><span data-stu-id="38f8f-228">[MyTested.AspNetCore.Mvc - Fluent Testing Library for ASP.NET Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): Strongly-typed unit testing library, providing a fluent interface for testing MVC and web API apps.</span></span> <span data-ttu-id="38f8f-229">( *Microsoft tarafından korunmaz veya desteklenmez.* )</span><span class="sxs-lookup"><span data-stu-id="38f8f-229">( *Not maintained or supported by Microsoft.* )</span></span>
* <xref:blazor/components/prerendering-and-integration>
