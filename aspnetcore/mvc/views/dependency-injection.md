---
title: ASP.NET Core görünümlere bağımlılık ekleme
author: ardalis
description: ASP.NET Core MVC görünümlerine bağımlılık ekleme işlemini nasıl desteklediğini öğrenin.
ms.author: riande
ms.date: 10/14/2016
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
uid: mvc/views/dependency-injection
ms.openlocfilehash: 4ed3e1334994c815996bd3db073fab5e544e2ca8
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018227"
---
# <a name="dependency-injection-into-views-in-aspnet-core"></a><span data-ttu-id="2bdaf-103">ASP.NET Core görünümlere bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="2bdaf-103">Dependency injection into views in ASP.NET Core</span></span>

<span data-ttu-id="2bdaf-104">[Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="2bdaf-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="2bdaf-105">ASP.NET Core, görünümlere [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini destekler.</span><span class="sxs-lookup"><span data-stu-id="2bdaf-105">ASP.NET Core supports [dependency injection](xref:fundamentals/dependency-injection) into views.</span></span> <span data-ttu-id="2bdaf-106">Bu, yalnızca görünüm öğelerini doldurmak için gereken yerelleştirme veya veriler gibi görünüme özgü hizmetler için yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="2bdaf-106">This can be useful for view-specific services, such as localization or data required only for populating view elements.</span></span> <span data-ttu-id="2bdaf-107">Denetleyicileriniz ve görünümleriniz arasındaki [kaygıların ayrılmasını](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) korumayı denemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="2bdaf-107">You should try to maintain [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) between your controllers and views.</span></span> <span data-ttu-id="2bdaf-108">Görünümlerinizin görüntüleyeceği verilerin çoğu denetleyiciden geçirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="2bdaf-108">Most of the data your views display should be passed in from the controller.</span></span>

<span data-ttu-id="2bdaf-109">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2bdaf-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration-injection"></a><span data-ttu-id="2bdaf-110">Yapılandırma ekleme</span><span class="sxs-lookup"><span data-stu-id="2bdaf-110">Configuration injection</span></span>

<span data-ttu-id="2bdaf-111">Değerler *üzerindeappsettings.js* doğrudan bir görünüme eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="2bdaf-111">*appsettings.json* values can be injected directly into a view.</span></span>

<span data-ttu-id="2bdaf-112">Dosyadaki bir *appsettings.js* örneği:</span><span class="sxs-lookup"><span data-stu-id="2bdaf-112">Example of an *appsettings.json* file:</span></span>

```json
{
   "root": {
      "parent": {
         "child": "myvalue"
      }
   }
}
```

<span data-ttu-id="2bdaf-113">İçin sözdizimi `@inject` :`@inject <type> <name>`</span><span class="sxs-lookup"><span data-stu-id="2bdaf-113">The syntax for `@inject`: `@inject <type> <name>`</span></span>

<span data-ttu-id="2bdaf-114">Şunu kullanarak bir örnek `@inject` :</span><span class="sxs-lookup"><span data-stu-id="2bdaf-114">An example using `@inject`:</span></span>

```csharp
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration
@{
   string myValue = Configuration["root:parent:child"];
   ...
}
```

## <a name="service-injection"></a><span data-ttu-id="2bdaf-115">Hizmet ekleme</span><span class="sxs-lookup"><span data-stu-id="2bdaf-115">Service injection</span></span>

<span data-ttu-id="2bdaf-116">Bir hizmet, yönergesini kullanarak bir görünüme eklenebilir `@inject` .</span><span class="sxs-lookup"><span data-stu-id="2bdaf-116">A service can be injected into a view using the `@inject` directive.</span></span> <span data-ttu-id="2bdaf-117">`@inject`Görünüme özellik ekleme ve dı kullanarak özelliği doldurma gibi düşünebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2bdaf-117">You can think of `@inject` as adding a property to the view, and populating the property using DI.</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

<span data-ttu-id="2bdaf-118">Bu görünüm `ToDoItem` , örneklerin listesini, genel istatistikleri gösteren bir Özet ile birlikte görüntüler.</span><span class="sxs-lookup"><span data-stu-id="2bdaf-118">This view displays a list of `ToDoItem` instances, along with a summary showing overall statistics.</span></span> <span data-ttu-id="2bdaf-119">Özet, eklenen öğesinden doldurulur `StatisticsService` .</span><span class="sxs-lookup"><span data-stu-id="2bdaf-119">The summary is populated from the injected `StatisticsService`.</span></span> <span data-ttu-id="2bdaf-120">Bu hizmet, Startup.cs içinde bağımlılık ekleme için `ConfigureServices` kaydedilir *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2bdaf-120">This service is registered for dependency injection in `ConfigureServices` in *Startup.cs*:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

<span data-ttu-id="2bdaf-121">, `StatisticsService` `ToDoItem` Bir depo aracılığıyla eriştiği örnek kümesi üzerinde bazı hesaplamalar gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="2bdaf-121">The `StatisticsService` performs some calculations on the set of `ToDoItem` instances, which it accesses via a repository:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

<span data-ttu-id="2bdaf-122">Örnek depo, bellek içi bir koleksiyon kullanır.</span><span class="sxs-lookup"><span data-stu-id="2bdaf-122">The sample repository uses an in-memory collection.</span></span> <span data-ttu-id="2bdaf-123">Yukarıda gösterilen uygulama (bellekteki tüm veriler üzerinde çalışır), büyük ve uzaktan erişilen veri kümeleri için önerilmez.</span><span class="sxs-lookup"><span data-stu-id="2bdaf-123">The implementation shown above (which operates on all of the data in memory) isn't recommended for large, remotely accessed data sets.</span></span>

<span data-ttu-id="2bdaf-124">Örnek, görüntüleme ile bağlantılı modeldeki verileri ve görünüme eklenen hizmeti görüntüler:</span><span class="sxs-lookup"><span data-stu-id="2bdaf-124">The sample displays data from the model bound to the view and the service injected into the view:</span></span>

![Toplam öğe, tamamlanan öğe, ortalama öncelik ve tamamlanma düzeylerine sahip bir görev listesi ve tamamlanma belirten Boole değerlerini görüntülemek için.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a><span data-ttu-id="2bdaf-126">Arama verileri dolduruluyor</span><span class="sxs-lookup"><span data-stu-id="2bdaf-126">Populating Lookup Data</span></span>

<span data-ttu-id="2bdaf-127">Görünüm ekleme, açılan listeler gibi kullanıcı arabirimi öğelerindeki seçenekleri doldurmak için yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="2bdaf-127">View injection can be useful to populate options in UI elements, such as dropdown lists.</span></span> <span data-ttu-id="2bdaf-128">Cinsiyet, eyalet ve diğer tercihlerin belirtilmesine yönelik seçenekleri içeren bir kullanıcı profili formu düşünün.</span><span class="sxs-lookup"><span data-stu-id="2bdaf-128">Consider a user profile form that includes options for specifying gender, state, and other preferences.</span></span> <span data-ttu-id="2bdaf-129">Standart bir MVC yaklaşımı kullanarak bu tür bir formu işlemek, denetleyicinin Bu seçenek kümelerinin her biri için veri erişim Hizmetleri istemesi ve sonra bir modeli ya da `ViewBag` bağlanacak her bir seçenek kümesiyle doldurmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="2bdaf-129">Rendering such a form using a standard MVC approach would require the controller to request data access services for each of these sets of options, and then populate a model or `ViewBag` with each set of options to be bound.</span></span>

<span data-ttu-id="2bdaf-130">Alternatif yaklaşım, seçenekleri almak için Hizmetleri doğrudan görünüme çıkarır.</span><span class="sxs-lookup"><span data-stu-id="2bdaf-130">An alternative approach injects services directly into the view to obtain the options.</span></span> <span data-ttu-id="2bdaf-131">Bu, denetleyicinin gerektirdiği kod miktarını en aza indirir ve bu görünüm öğesi oluşturma mantığını görünümün içine taşır.</span><span class="sxs-lookup"><span data-stu-id="2bdaf-131">This minimizes the amount of code required by the controller, moving this view element construction logic into the view itself.</span></span> <span data-ttu-id="2bdaf-132">Bir profil düzenlemesi formunu görüntülemeye yönelik denetleyici eylemi yalnızca profil örneği formunu geçirmeniz gerekir:</span><span class="sxs-lookup"><span data-stu-id="2bdaf-132">The controller action to display a profile editing form only needs to pass the form the profile instance:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

<span data-ttu-id="2bdaf-133">Bu tercihleri güncelleştirmek için kullanılan HTML formu, özelliklerin üç yanındaki açılan listeleri içerir:</span><span class="sxs-lookup"><span data-stu-id="2bdaf-133">The HTML form used to update these preferences includes dropdown lists for three of the properties:</span></span>

![Ad, cinsiyet, eyalet ve sık kullanılan renk girişine izin veren bir formla profil görünümünü güncelleştirin.](dependency-injection/_static/updateprofile.png)

<span data-ttu-id="2bdaf-135">Bu listeler, görünüme eklenen bir hizmet tarafından doldurulur:</span><span class="sxs-lookup"><span data-stu-id="2bdaf-135">These lists are populated by a service that has been injected into the view:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

<span data-ttu-id="2bdaf-136">, `ProfileOptionsService` Yalnızca bu form için gereken verileri sağlamak üzere tasarlanan BIR UI düzeyi hizmettir:</span><span class="sxs-lookup"><span data-stu-id="2bdaf-136">The `ProfileOptionsService` is a UI-level service designed to provide just the data needed for this form:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> <span data-ttu-id="2bdaf-137">Uygulamasına bağımlılık ekleme yoluyla istek yaptığınız türleri kaydetmeyi unutmayın `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2bdaf-137">Don't forget to register types you request through dependency injection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2bdaf-138">Kaydedilmemiş bir tür, hizmet sağlayıcısı, [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice)aracılığıyla dahili olarak sorgulandığından çalışma zamanında bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2bdaf-138">An unregistered type throws an exception at runtime because the service provider is internally queried via [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span></span>

## <a name="overriding-services"></a><span data-ttu-id="2bdaf-139">Hizmetleri geçersiz kılma</span><span class="sxs-lookup"><span data-stu-id="2bdaf-139">Overriding Services</span></span>

<span data-ttu-id="2bdaf-140">Bu teknik, ekleme yeni hizmetlere ek olarak, bir sayfada önceden eklenen Hizmetleri geçersiz kılmak için de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2bdaf-140">In addition to injecting new services, this technique can also be used to override previously injected services on a page.</span></span> <span data-ttu-id="2bdaf-141">Aşağıdaki şekilde, ilk örnekte kullanılan sayfada bulunan tüm alanlar gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="2bdaf-141">The figure below shows all of the fields available on the page used in the first example:</span></span>

![HTML, bileşen, StatsService ve URL alanları için belirlenmiş bir @ symbol üzerinde IntelliSense bağlamsal menüsü](dependency-injection/_static/razor-fields.png)

<span data-ttu-id="2bdaf-143">Gördüğünüz gibi, varsayılan alanlar `Html` ,, `Component` ve `Url` ( `StatsService` eklediğimiz), ve ' i içerir.</span><span class="sxs-lookup"><span data-stu-id="2bdaf-143">As you can see, the default fields include `Html`, `Component`, and `Url` (as well as the `StatsService` that we injected).</span></span> <span data-ttu-id="2bdaf-144">Örneğin varsayılan HTML yardımcılarını kendi kendinize değiştirmek istiyorsanız, şunu kullanarak kolayca yapabilirsiniz `@inject` :</span><span class="sxs-lookup"><span data-stu-id="2bdaf-144">If for instance you wanted to replace the default HTML Helpers with your own, you could easily do so using `@inject`:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

<span data-ttu-id="2bdaf-145">Mevcut hizmetleri genişletmek istiyorsanız, bu tekniği kullanarak var olan uygulamadan devralma veya kaydırma yaparken bu tekniği kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2bdaf-145">If you want to extend existing services, you can simply use this technique while inheriting from or wrapping the existing implementation with your own.</span></span>

## <a name="see-also"></a><span data-ttu-id="2bdaf-146">Ayrıca Bkz.</span><span class="sxs-lookup"><span data-stu-id="2bdaf-146">See Also</span></span>

* <span data-ttu-id="2bdaf-147">Simon Timms blogu: [görünümünüze arama verileri alma](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span><span class="sxs-lookup"><span data-stu-id="2bdaf-147">Simon Timms Blog: [Getting Lookup Data Into Your View](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span></span>
