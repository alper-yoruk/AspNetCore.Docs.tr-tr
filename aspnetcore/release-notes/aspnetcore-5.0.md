---
title: ASP.NET Core 5,0 ' deki yenilikler
author: rick-anderson
description: ASP.NET Core 5,0 ' deki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
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
- ':::no-loc(Kestrel):::'
uid: aspnetcore-5.0
ms.openlocfilehash: e9c74f7b45ebcdffc19a0483b4e98ad2f44d5747
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061779"
---
# <a name="whats-new-in-aspnet-core-50"></a><span data-ttu-id="cad65-103">ASP.NET Core 5,0 ' deki yenilikler</span><span class="sxs-lookup"><span data-stu-id="cad65-103">What's new in ASP.NET Core 5.0</span></span>

<span data-ttu-id="cad65-104">Bu makalede, ASP.NET Core 5,0 ' deki en önemli değişiklikler ilgili belgelerin bağlantılarıyla vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="cad65-104">This article highlights the most significant changes in ASP.NET Core 5.0 with links to relevant documentation.</span></span>

## <a name="aspnet-core-mvc-and-no-locrazor-improvements"></a><span data-ttu-id="cad65-105">ASP.NET Core MVC ve :::no-loc(Razor)::: iyileştirmeler</span><span class="sxs-lookup"><span data-stu-id="cad65-105">ASP.NET Core MVC and :::no-loc(Razor)::: improvements</span></span>

### <a name="model-binding-datetime-as-utc"></a><span data-ttu-id="cad65-106">Model bağlama DateTime değeri UTC olarak</span><span class="sxs-lookup"><span data-stu-id="cad65-106">Model binding DateTime as UTC</span></span>

<span data-ttu-id="cad65-107">Model bağlama artık UTC zaman dizelerinin bağlantısını destekler `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="cad65-107">Model binding now supports binding UTC time strings to `DateTime`.</span></span> <span data-ttu-id="cad65-108">İstek bir UTC zaman dizesi içeriyorsa, model bağlama onu bir UTC 'ye bağlar `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="cad65-108">If the request contains a UTC time string, model binding binds it to a UTC `DateTime`.</span></span> <span data-ttu-id="cad65-109">Örneğin, aşağıdaki zaman dizesi UTC 'ye bağımlıdır `DateTime` : `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`</span><span class="sxs-lookup"><span data-stu-id="cad65-109">For example, the following time string is bound the UTC `DateTime`: `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`</span></span>

### <a name="model-binding-and-validation-with-c-9-record-types"></a><span data-ttu-id="cad65-110">C# 9 kayıt türleriyle model bağlama ve doğrulama</span><span class="sxs-lookup"><span data-stu-id="cad65-110">Model binding and validation with C# 9 record types</span></span>

<span data-ttu-id="cad65-111">[C# 9 kayıt türleri](/dotnet/csharp/whats-new/csharp-9#record-types) , bir MVC denetleyicisi veya sayfasında model bağlama ile birlikte kullanılabilir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="cad65-111">[C# 9 record types](/dotnet/csharp/whats-new/csharp-9#record-types) can be used with model binding in an MVC controller or a :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="cad65-112">Kayıt türleri, ağ üzerinden aktarılan verileri modeletmenin iyi bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="cad65-112">Record types are a good way to model data being transmitted over the network.</span></span>

<span data-ttu-id="cad65-113">Örneğin, aşağıdaki `PersonController` `Person` model bağlama ve form doğrulama ile kayıt türünü kullanır:</span><span class="sxs-lookup"><span data-stu-id="cad65-113">For example, the following `PersonController` uses the `Person` record type with model binding and form validation:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
          // ...
   }
}
```

<span data-ttu-id="cad65-114">`Person/Index.cshtml`Dosya:</span><span class="sxs-lookup"><span data-stu-id="cad65-114">The `Person/Index.cshtml` file:</span></span>

```cshtml
@model Person

Name: <input asp-for="Model.Name" />
<span asp-validation-for="Model.Name" />

Age: <input asp-for="Model.Age" />
<span asp-validation-for="Model.Age" />
```

### <a name="improvements-to-dynamicroutevaluetransformer"></a><span data-ttu-id="cad65-115">Dynamikro Utevaluetranseski geliştirmeleri</span><span class="sxs-lookup"><span data-stu-id="cad65-115">Improvements to DynamicRouteValueTransformer</span></span>

<span data-ttu-id="cad65-116">ASP.NET Core 3,1, bir <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> MVC denetleyicisi eylemini veya sayfasını dinamik olarak seçmek için özel uç nokta kullanmanın bir yolu olarak sunulmuştur :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="cad65-116">ASP.NET Core 3.1 introduced <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> as a way to use custom endpoint to dynamically select an MVC controller action or a :::no-loc(Razor)::: page.</span></span> <span data-ttu-id="cad65-117">ASP.NET Core 5,0 uygulamaları, durum ' a geçirebilir `DynamicRouteValueTransformer` ve seçilen uç nokta kümesini filtreleyebilir.</span><span class="sxs-lookup"><span data-stu-id="cad65-117">ASP.NET Core 5.0 apps can pass state to a `DynamicRouteValueTransformer` and filter the set of endpoints chosen.</span></span>

### <a name="miscellaneous"></a><span data-ttu-id="cad65-118">Çeşitli</span><span class="sxs-lookup"><span data-stu-id="cad65-118">Miscellaneous</span></span>

* <span data-ttu-id="cad65-119">[[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) özniteliği bir :::no-loc(Razor)::: sayfa modelindeki özelliklere uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="cad65-119">The [[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute can be applied to properties on a :::no-loc(Razor)::: Page model.</span></span>
* <span data-ttu-id="cad65-120">Gövdeden bağlantılı parametreler ve özellikler varsayılan olarak gerekli kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="cad65-120">Parameters and properties bound from the body are considered required by default.</span></span> <!-- Review: How is this different from 3.1
The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a [Required] attribute.
see https://docs.microsoft.com/aspnet/core/mvc/models/validation?view=aspnetcore-3.1   
-->

## <a name="web-api"></a><span data-ttu-id="cad65-121">Web API</span><span class="sxs-lookup"><span data-stu-id="cad65-121">Web API</span></span>

### <a name="openapi-specification-on-by-default"></a><span data-ttu-id="cad65-122">Varsayılan olarak üzerinde Openapı belirtimi</span><span class="sxs-lookup"><span data-stu-id="cad65-122">OpenAPI Specification on by default</span></span>

<span data-ttu-id="cad65-123">[Openapı belirtimi](http://spec.openapis.org/oas/v3.0.3) , HTTP API 'lerini açıklamak ve bunları karmaşık iş süreçleriyle veya üçüncü taraflarla tümleştirmeye yönelik bir sektör standardıdır.</span><span class="sxs-lookup"><span data-stu-id="cad65-123">[OpenAPI Specification](http://spec.openapis.org/oas/v3.0.3) is an industry standard for describing HTTP APIs and integrating them into complex business processes or with third parties.</span></span> <span data-ttu-id="cad65-124">Openapı, tüm bulut sağlayıcıları ve birçok API kayıt defterleri tarafından yaygın olarak desteklenir.</span><span class="sxs-lookup"><span data-stu-id="cad65-124">OpenAPI is widely supported by all cloud providers and many API registries.</span></span> <span data-ttu-id="cad65-125">Web API 'Lerinden Openapı belgelerinin yaydıkları uygulamalar, bu API 'Lerin kullanılabileceği çeşitli yeni fırsatlara sahiptir.</span><span class="sxs-lookup"><span data-stu-id="cad65-125">Apps that emit OpenAPI documents from web APIs have a variety of new opportunities in which those APIs can be used.</span></span> <span data-ttu-id="cad65-126">Açık kaynaklı projenin, [swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/)'un bakım ve ASP.NET Core API şablonu, [swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore)üzerinde bir NuGet bağımlılığı içerir.</span><span class="sxs-lookup"><span data-stu-id="cad65-126">In partnership with the maintainers of the open-source project [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/), the ASP.NET Core API template contains a NuGet dependency on [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).</span></span> <span data-ttu-id="cad65-127">Swashbuckle, Openapı belgelerini dinamik olarak yayar, popüler bir açık kaynaklı NuGet paketidir.</span><span class="sxs-lookup"><span data-stu-id="cad65-127">Swashbuckle is a popular open-source NuGet package that emits OpenAPI documents dynamically.</span></span> <span data-ttu-id="cad65-128">Swashbuckle bunu API denetleyicileri üzerinden introspecting ve çalışma zamanında Openapı belgesini veya swashbuckle CLı kullanarak derleme zamanında oluşturur.</span><span class="sxs-lookup"><span data-stu-id="cad65-128">Swashbuckle does this by introspecting over the API controllers and generating the OpenAPI document at run-time, or at build time using the Swashbuckle CLI.</span></span>

<span data-ttu-id="cad65-129">ASP.NET Core 5,0 ' de Web API 'SI şablonları, Openapı desteğini varsayılan olarak etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="cad65-129">In ASP.NET Core 5.0, the web API templates enable the OpenAPI support by default.</span></span> <span data-ttu-id="cad65-130">Openapı 'yi devre dışı bırakmak için:</span><span class="sxs-lookup"><span data-stu-id="cad65-130">To disable OpenAPI:</span></span>

* <span data-ttu-id="cad65-131">Komut satırından:</span><span class="sxs-lookup"><span data-stu-id="cad65-131">From the command line:</span></span>

    ```dotnetcli
    dotnet new webapi --no-openapi true
    ```
* <span data-ttu-id="cad65-132">Visual Studio 'dan: **Openapı desteğini etkinleştir** seçeneğinin işaretini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="cad65-132">From Visual Studio: Uncheck **Enable OpenAPI support** .</span></span>

<span data-ttu-id="cad65-133">Web API projeleri için oluşturulan tüm *. csproj* dosyaları [swashbuckle. aspnetcore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) NuGet paketi başvurusunu içerir.</span><span class="sxs-lookup"><span data-stu-id="cad65-133">All *.csproj* files created for web API projects contain the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) NuGet package reference.</span></span>

```xml
<ItemGroup>
    <PackageReference Include="Swashbuckle.AspNetCore" Version="5.5.1" />
</ItemGroup>
```

<span data-ttu-id="cad65-134">Şablon tarafından oluşturulan kod, `Startup.ConfigureServices` Openapı belge oluşturmayı etkinleştiren ' de kod içerir:</span><span class="sxs-lookup"><span data-stu-id="cad65-134">The template generated code contains code in `Startup.ConfigureServices` that activates OpenAPI document generation:</span></span>

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet)]

<span data-ttu-id="cad65-135">`Startup.Configure`Yöntemi, şunu sağlayan swashbuckle ara yazılımını ekler:</span><span class="sxs-lookup"><span data-stu-id="cad65-135">The `Startup.Configure` method adds the Swashbuckle middleware, which enables the:</span></span>

* <span data-ttu-id="cad65-136">Belge oluşturma işlemi.</span><span class="sxs-lookup"><span data-stu-id="cad65-136">Document generation process.</span></span>
* <span data-ttu-id="cad65-137">Varsayılan olarak, geliştirme modunda Swagger Kullanıcı arabirimi sayfası.</span><span class="sxs-lookup"><span data-stu-id="cad65-137">Swagger UI page by default in development mode.</span></span>

<span data-ttu-id="cad65-138">Şablon tarafından oluşturulan kod yanlışlıkla üretime yayımlarken API 'nin açıklamasını sunmaz.</span><span class="sxs-lookup"><span data-stu-id="cad65-138">The template generated code won't accidentally expose the API's description when publishing to production.</span></span>

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet2)]

#### <a name="azure-api-management-import"></a><span data-ttu-id="cad65-139">Azure API Management Içeri aktarma</span><span class="sxs-lookup"><span data-stu-id="cad65-139">Azure API Management Import</span></span>

<span data-ttu-id="cad65-140">ASP.NET Core API projeleri Openapı 'yi etkinleştirdeğiştiğinde, Visual Studio 2019 sürüm 16,8 ve sonraki sürümlerde yayımlama akışında ek bir adım otomatik olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="cad65-140">When ASP.NET Core API projects enable OpenAPI, the Visual Studio 2019 version 16.8 and later publishing automatically offer an additional step in the publishing flow.</span></span> <span data-ttu-id="cad65-141">[Azure API Management](xref:tutorials/publish-to-azure-api-management-using-vs) kullanan geliştiriciler, yayımlama akışı sırasında API 'leri otomatik olarak Azure API Management 'ye içeri aktarmaya yönelik bir fırsata sahiptir:</span><span class="sxs-lookup"><span data-stu-id="cad65-141">Developers who use [Azure API Management](xref:tutorials/publish-to-azure-api-management-using-vs) have an opportunity to automatically import the APIs into Azure API Management during the publish flow:</span></span>

![Azure API Management Içeri aktarma VS yayımlama](~/release-notes/static/publish-to-apim.png)

### <a name="better-launch-experience-for-web-api-projects"></a><span data-ttu-id="cad65-143">Web API projeleri için daha iyi başlatma deneyimi</span><span class="sxs-lookup"><span data-stu-id="cad65-143">Better launch experience for web API projects</span></span>

<span data-ttu-id="cad65-144">Openapı varsayılan olarak etkinken, Web API geliştiricileri için uygulama başlatma deneyimi (F5) önemli ölçüde gelişir.</span><span class="sxs-lookup"><span data-stu-id="cad65-144">With OpenAPI enabled by default, the app launching experience (F5) for web API developers significantly improves.</span></span> <span data-ttu-id="cad65-145">ASP.NET Core 5,0 ile Web API şablonu, Swagger Kullanıcı Arabirimi sayfasını yüklemek için önceden yapılandırılmış olarak gelir.</span><span class="sxs-lookup"><span data-stu-id="cad65-145">With ASP.NET Core 5.0, the web API template comes pre-configured to load up the Swagger UI page.</span></span> <span data-ttu-id="cad65-146">Swagger Kullanıcı arabirimi sayfası, hem yayımlanan API için eklenen belgeleri hem de API 'Lerin tek bir tıklama ile test edilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="cad65-146">The Swagger UI page provides both the documentation added for the published API, and enables testing the APIs with a single click.</span></span>

![Swagger/index.html görünümü](~/release-notes/static/swagger-ui-page-rc1.png)

## :::no-loc(Blazor):::

### <a name="performance-improvements"></a><span data-ttu-id="cad65-148">Performans iyileştirmeleri</span><span class="sxs-lookup"><span data-stu-id="cad65-148">Performance improvements</span></span>

<span data-ttu-id="cad65-149">.NET 5 için, :::no-loc(Blazor WebAssembly)::: karmaşık UI işleme ve JSON serileştirmesi üzerinde belirli bir odak ile çalışma zamanı performansı için önemli geliştirmeler yaptık.</span><span class="sxs-lookup"><span data-stu-id="cad65-149">For .NET 5, we made significant improvements to :::no-loc(Blazor WebAssembly)::: runtime performance with a specific focus on complex UI rendering and JSON serialization.</span></span> <span data-ttu-id="cad65-150">Performans testlerimizde, :::no-loc(Blazor WebAssembly)::: .NET 5 ' te çoğu senaryo için iki-üç kat daha hızlıdır.</span><span class="sxs-lookup"><span data-stu-id="cad65-150">In our performance tests, :::no-loc(Blazor WebAssembly)::: in .NET 5 is two to three times faster for most scenarios.</span></span> <span data-ttu-id="cad65-151">Daha fazla bilgi için bkz. [ASP.net Blog: .NET 5 Release Candidate 1 ' de ASP.NET Core Updates](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements).</span><span class="sxs-lookup"><span data-stu-id="cad65-151">For more information, see [ASP.NET Blog: ASP.NET Core updates in .NET 5 Release Candidate 1](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements).</span></span>

### <a name="css-isolation"></a><span data-ttu-id="cad65-152">CSS yalıtımı</span><span class="sxs-lookup"><span data-stu-id="cad65-152">CSS isolation</span></span>

<span data-ttu-id="cad65-153">:::no-loc(Blazor)::: artık belirli bir bileşen kapsamındaki CSS stillerinin tanımlanmasını destekler.</span><span class="sxs-lookup"><span data-stu-id="cad65-153">:::no-loc(Blazor)::: now supports defining CSS styles that are scoped to a given component.</span></span> <span data-ttu-id="cad65-154">Bileşene özgü CSS stilleri, bir uygulamadaki stiller hakkında sebebini kolaylaştırır ve genel stillerin yanlışlıkla yan etkileriyle kaçınılmasını önler.</span><span class="sxs-lookup"><span data-stu-id="cad65-154">Component-specific CSS styles make it easier to reason about the styles in an app and to avoid unintentional side effects of global styles.</span></span> <span data-ttu-id="cad65-155">Daha fazla bilgi için bkz. <xref:blazor/components/css-isolation>.</span><span class="sxs-lookup"><span data-stu-id="cad65-155">For more information, see <xref:blazor/components/css-isolation>.</span></span>

### <a name="new-inputfile-component"></a><span data-ttu-id="cad65-156">Yeni `InputFile` bileşen</span><span class="sxs-lookup"><span data-stu-id="cad65-156">New `InputFile` component</span></span>

<span data-ttu-id="cad65-157">`InputFile`Bileşen, bir kullanıcı tarafından karşıya yüklenmek üzere seçilen bir veya daha fazla dosyanın okunmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="cad65-157">The `InputFile` component allows reading one or more files selected by a user for upload.</span></span> <span data-ttu-id="cad65-158">Daha fazla bilgi için bkz. <xref:blazor/file-uploads>.</span><span class="sxs-lookup"><span data-stu-id="cad65-158">For more information, see <xref:blazor/file-uploads>.</span></span>

### <a name="new-inputradio-and-inputradiogroup-components"></a><span data-ttu-id="cad65-159">Yeni `InputRadio` ve `InputRadioGroup` Bileşenler</span><span class="sxs-lookup"><span data-stu-id="cad65-159">New `InputRadio` and `InputRadioGroup` components</span></span>

<span data-ttu-id="cad65-160">:::no-loc(Blazor):::`InputRadio` `InputRadioGroup` Tümleşik doğrulama ile radyo düğmesi gruplarına veri bağlamayı kolaylaştıran yerleşik ve bileşenlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="cad65-160">:::no-loc(Blazor)::: has built-in `InputRadio` and `InputRadioGroup` components that simplify data binding to radio button groups with integrated validation.</span></span> <span data-ttu-id="cad65-161">Daha fazla bilgi için bkz. <xref:blazor/forms-validation>.</span><span class="sxs-lookup"><span data-stu-id="cad65-161">For more information, see <xref:blazor/forms-validation>.</span></span>

### <a name="component-virtualization"></a><span data-ttu-id="cad65-162">Bileşen sanallaştırma</span><span class="sxs-lookup"><span data-stu-id="cad65-162">Component virtualization</span></span>

<span data-ttu-id="cad65-163">Framework 'ün yerleşik sanallaştırma desteğini kullanarak bileşen işlemenin algılanan performansını geliştirir :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="cad65-163">Improve the perceived performance of component rendering using the :::no-loc(Blazor)::: framework's built-in virtualization support.</span></span> <span data-ttu-id="cad65-164">Daha fazla bilgi için bkz. <xref:blazor/forms-validation#radio-buttons>.</span><span class="sxs-lookup"><span data-stu-id="cad65-164">For more information, see <xref:blazor/forms-validation#radio-buttons>.</span></span>

### <a name="ontoggle-event-support"></a><span data-ttu-id="cad65-165">`ontoggle` olay desteği</span><span class="sxs-lookup"><span data-stu-id="cad65-165">`ontoggle` event support</span></span>

<span data-ttu-id="cad65-166">:::no-loc(Blazor)::: Olaylar artık `ontoggle` Dom olayını destekliyor.</span><span class="sxs-lookup"><span data-stu-id="cad65-166">:::no-loc(Blazor)::: events now support the `ontoggle` DOM event.</span></span> <span data-ttu-id="cad65-167">Daha fazla bilgi için bkz. <xref:blazor/components/event-handling#event-argument-types>.</span><span class="sxs-lookup"><span data-stu-id="cad65-167">For more information, see <xref:blazor/components/event-handling#event-argument-types>.</span></span>

### <a name="set-ui-focus-in-no-locblazor-apps"></a><span data-ttu-id="cad65-168">Uygulamalarda UI odağı ayarlama :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="cad65-168">Set UI focus in :::no-loc(Blazor)::: apps</span></span>

<span data-ttu-id="cad65-169">`FocusAsync`UI odağını bu öğeye ayarlamak için öğe başvurularında kolaylık olan yöntemi kullanın.</span><span class="sxs-lookup"><span data-stu-id="cad65-169">Use the `FocusAsync` convenience method on element references to set the UI focus to that element.</span></span> <span data-ttu-id="cad65-170">Daha fazla bilgi için bkz. <xref:blazor/components/event-handling#focus-an-element>.</span><span class="sxs-lookup"><span data-stu-id="cad65-170">For more information, see <xref:blazor/components/event-handling#focus-an-element>.</span></span>

### <a name="custom-validation-class-attributes"></a><span data-ttu-id="cad65-171">Özel doğrulama sınıfı öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="cad65-171">Custom validation class attributes</span></span>

<span data-ttu-id="cad65-172">Özel doğrulama sınıfı adları, önyükleme gibi CSS çerçeveleri ile tümleştirilirken faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="cad65-172">Custom validation class names are useful when integrating with CSS frameworks, such as Bootstrap.</span></span> <span data-ttu-id="cad65-173">Daha fazla bilgi için bkz. <xref:blazor/forms-validation#custom-validation-class-attributes>.</span><span class="sxs-lookup"><span data-stu-id="cad65-173">For more information, see <xref:blazor/forms-validation#custom-validation-class-attributes>.</span></span>

### <a name="iasyncdisposable-support"></a><span data-ttu-id="cad65-174">IAsyncDisposable desteği</span><span class="sxs-lookup"><span data-stu-id="cad65-174">IAsyncDisposable support</span></span>

<span data-ttu-id="cad65-175">:::no-loc(Blazor)::: bileşenler artık <xref:System.IAsyncDisposable> ayrılmış kaynakların zaman uyumsuz sürümünün arabirimini destekler.</span><span class="sxs-lookup"><span data-stu-id="cad65-175">:::no-loc(Blazor)::: components now support the <xref:System.IAsyncDisposable> interface for the asynchronous release of allocated resources.</span></span>

### <a name="javascript-isolation-and-object-references"></a><span data-ttu-id="cad65-176">JavaScript yalıtımı ve nesne başvuruları</span><span class="sxs-lookup"><span data-stu-id="cad65-176">JavaScript isolation and object references</span></span>

<span data-ttu-id="cad65-177">:::no-loc(Blazor)::: Standart [JavaScript modüllerinde](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)JavaScript yalıtımına izin vermez.</span><span class="sxs-lookup"><span data-stu-id="cad65-177">:::no-loc(Blazor)::: enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="cad65-178">Daha fazla bilgi için bkz. <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span><span class="sxs-lookup"><span data-stu-id="cad65-178">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

### <a name="form-components-support-display-name"></a><span data-ttu-id="cad65-179">Form bileşenleri destek görünen adı</span><span class="sxs-lookup"><span data-stu-id="cad65-179">Form components support display name</span></span>

<span data-ttu-id="cad65-180">Aşağıdaki yerleşik bileşenler parametresiyle görünen adları destekler `DisplayName` :</span><span class="sxs-lookup"><span data-stu-id="cad65-180">The following built-in components support display names with the `DisplayName` parameter:</span></span>

* `InputDate`
* `InputNumber`
* `InputSelect`

<span data-ttu-id="cad65-181">Daha fazla bilgi için bkz. <xref:blazor/forms-validation#display-name-support>.</span><span class="sxs-lookup"><span data-stu-id="cad65-181">For more information, see <xref:blazor/forms-validation#display-name-support>.</span></span>

### <a name="catch-all-route-parameters"></a><span data-ttu-id="cad65-182">Catch-all Route parametreleri</span><span class="sxs-lookup"><span data-stu-id="cad65-182">Catch-all route parameters</span></span>

<span data-ttu-id="cad65-183">Birden çok klasör sınırlarındaki yolları yakalayan catch-all yol parametreleri, bileşenlerinde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="cad65-183">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span> <span data-ttu-id="cad65-184">Daha fazla bilgi için bkz. <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span><span class="sxs-lookup"><span data-stu-id="cad65-184">For more information, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="debugging-improvements"></a><span data-ttu-id="cad65-185">Hata ayıklama geliştirmeleri</span><span class="sxs-lookup"><span data-stu-id="cad65-185">Debugging improvements</span></span>

<span data-ttu-id="cad65-186">:::no-loc(Blazor WebAssembly):::ASP.NET Core 5,0 ' de uygulamalarda hata ayıklama geliştirildi.</span><span class="sxs-lookup"><span data-stu-id="cad65-186">Debugging :::no-loc(Blazor WebAssembly)::: apps is improved in ASP.NET Core 5.0.</span></span> <span data-ttu-id="cad65-187">Ayrıca, hata ayıklama artık Mac için Visual Studio desteklenir.</span><span class="sxs-lookup"><span data-stu-id="cad65-187">Additionally, debugging is now supported on Visual Studio for Mac.</span></span> <span data-ttu-id="cad65-188">Daha fazla bilgi için bkz. <xref:blazor/debug>.</span><span class="sxs-lookup"><span data-stu-id="cad65-188">For more information, see <xref:blazor/debug>.</span></span>

### <a name="microsoft-no-locidentity-v20-and-msal-v20"></a><span data-ttu-id="cad65-189">Microsoft :::no-loc(Identity)::: v 2.0 ve msal v 2.0</span><span class="sxs-lookup"><span data-stu-id="cad65-189">Microsoft :::no-loc(Identity)::: v2.0 and MSAL v2.0</span></span>

<span data-ttu-id="cad65-190">:::no-loc(Blazor)::: Güvenlik artık Microsoft :::no-loc(Identity)::: v 2.0 ( [`Microsoft.:::no-loc(Identity):::.Web`](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web) ve [`Microsoft.:::no-loc(Identity):::.Web.UI`](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web.UI) ) ve msal v 2.0 kullanır.</span><span class="sxs-lookup"><span data-stu-id="cad65-190">:::no-loc(Blazor)::: security now uses Microsoft :::no-loc(Identity)::: v2.0 ([`Microsoft.:::no-loc(Identity):::.Web`](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web) and [`Microsoft.:::no-loc(Identity):::.Web.UI`](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web.UI)) and MSAL v2.0.</span></span> <span data-ttu-id="cad65-191">Daha fazla bilgi için [ :::no-loc(Blazor)::: güvenlik ve :::no-loc(Identity)::: düğümdeki](xref:blazor/security/index)konulara bakın.</span><span class="sxs-lookup"><span data-stu-id="cad65-191">For more information, see the topics in the [:::no-loc(Blazor)::: Security and :::no-loc(Identity)::: node](xref:blazor/security/index).</span></span>

### <a name="protected-browser-storage-for-no-locblazor-server"></a><span data-ttu-id="cad65-192">İçin korumalı tarayıcı depolaması :::no-loc(Blazor Server):::</span><span class="sxs-lookup"><span data-stu-id="cad65-192">Protected Browser Storage for :::no-loc(Blazor Server):::</span></span>

<span data-ttu-id="cad65-193">:::no-loc(Blazor Server)::: uygulamalar artık uygulama durumunun ASP.NET Core veri koruma kullanılarak yapılan değişikliklere karşı korunması için yerleşik desteğini kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="cad65-193">:::no-loc(Blazor Server)::: apps can now use built-in support for storing app state in the browser that has been protected from tampering using ASP.NET Core data protection.</span></span> <span data-ttu-id="cad65-194">Veriler, yerel tarayıcı depolama veya oturum depolama alanında depolanabilir.</span><span class="sxs-lookup"><span data-stu-id="cad65-194">Data can be stored in either local browser storage or session storage.</span></span> <span data-ttu-id="cad65-195">Daha fazla bilgi için bkz. <xref:blazor/state-management>.</span><span class="sxs-lookup"><span data-stu-id="cad65-195">For more information, see <xref:blazor/state-management>.</span></span>

### <a name="no-locblazor-webassembly-prerendering"></a><span data-ttu-id="cad65-196">:::no-loc(Blazor WebAssembly)::: prerendering</span><span class="sxs-lookup"><span data-stu-id="cad65-196">:::no-loc(Blazor WebAssembly)::: prerendering</span></span>

<span data-ttu-id="cad65-197">Bileşen tümleştirme, barındırma modelleri arasında geliştirilmiştir ve :::no-loc(Blazor WebAssembly)::: uygulamalar artık sunucuda bir çıktıyı açabilir.</span><span class="sxs-lookup"><span data-stu-id="cad65-197">Component integration is improved across hosting models, and :::no-loc(Blazor WebAssembly)::: apps can now prerender output on the server.</span></span> <!-- UNCOMMENT AFTER https://github.com/dotnet/AspNetCore.Docs/pull/19887 MERGES: For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps> and <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>. -->

### <a name="trimminglinking-improvements"></a><span data-ttu-id="cad65-198">Kırpma/bağlama iyileştirmeleri</span><span class="sxs-lookup"><span data-stu-id="cad65-198">Trimming/linking improvements</span></span>

<span data-ttu-id="cad65-199">:::no-loc(Blazor WebAssembly)::: uygulamanın çıkış derlemelerinden gereksiz Il 'yi kırpmak için bir derleme sırasında ara dil (IL) kırpması/bağlamayı gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="cad65-199">:::no-loc(Blazor WebAssembly)::: performs Intermediate Language (IL) trimming/linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="cad65-200">ASP.NET Core 5,0 sürümü ile :::no-loc(Blazor WebAssembly)::: ek yapılandırma seçenekleriyle Gelişmiş kırpma gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="cad65-200">With the release of ASP.NET Core 5.0, :::no-loc(Blazor WebAssembly)::: performs improved trimming with additional configuration options.</span></span> <span data-ttu-id="cad65-201">Daha fazla bilgi için bkz <xref:blazor/host-and-deploy/configure-trimmer> . ve [kırpma seçenekleri](/dotnet/core/deploying/trimming-options).</span><span class="sxs-lookup"><span data-stu-id="cad65-201">For more information, see <xref:blazor/host-and-deploy/configure-trimmer> and [Trimming options](/dotnet/core/deploying/trimming-options).</span></span>

### <a name="browser-compatibility-analyzer"></a><span data-ttu-id="cad65-202">Tarayıcı uyumluluğu Çözümleyicisi</span><span class="sxs-lookup"><span data-stu-id="cad65-202">Browser compatibility analyzer</span></span>

<span data-ttu-id="cad65-203">:::no-loc(Blazor WebAssembly)::: uygulamalar tam .NET API yüzey alanını hedefletir, ancak tüm .NET API 'Leri, tarayıcı korumalı alan kısıtlamaları nedeniyle WebAssembly üzerinde desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="cad65-203">:::no-loc(Blazor WebAssembly)::: apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="cad65-204"><xref:System.PlatformNotSupportedException>WebAssembly üzerinde çalışırken desteklenmeyen API 'ler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="cad65-204">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="cad65-205">Uygulama, uygulamanın hedef platformları tarafından desteklenmeyen API 'Ler kullandığında, bir platform uyumluluğu Çözümleyicisi geliştiriciyi uyarır.</span><span class="sxs-lookup"><span data-stu-id="cad65-205">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="cad65-206">Daha fazla bilgi için bkz. <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>.</span><span class="sxs-lookup"><span data-stu-id="cad65-206">For more information, see <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>.</span></span>

### <a name="lazy-load-assemblies"></a><span data-ttu-id="cad65-207">Yavaş yük derlemeleri</span><span class="sxs-lookup"><span data-stu-id="cad65-207">Lazy load assemblies</span></span>

<span data-ttu-id="cad65-208">:::no-loc(Blazor WebAssembly)::: Uygulama başlatma performansı, bazı uygulama derlemelerinin gerekli olana kadar yüklenmesi erteleyerek artırılabilir.</span><span class="sxs-lookup"><span data-stu-id="cad65-208">:::no-loc(Blazor WebAssembly)::: app startup performance can be improved by deferring the loading of some application assemblies until they're required.</span></span> <span data-ttu-id="cad65-209">Daha fazla bilgi için bkz. <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="cad65-209">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="updated-globalization-support"></a><span data-ttu-id="cad65-210">Genelleştirme desteği güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="cad65-210">Updated globalization support</span></span>

<span data-ttu-id="cad65-211">Genelleştirme desteği, :::no-loc(Blazor WebAssembly)::: Unicode (ıCU) Için Uluslararası bileşenlere bağlı olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="cad65-211">Globalization support is available for :::no-loc(Blazor WebAssembly)::: based on International Components for Unicode (ICU).</span></span> <span data-ttu-id="cad65-212">Daha fazla bilgi için bkz. <xref:blazor/globalization-localization>.</span><span class="sxs-lookup"><span data-stu-id="cad65-212">For more information, see <xref:blazor/globalization-localization>.</span></span>

## <a name="grpc"></a><span data-ttu-id="cad65-213">gRPC</span><span class="sxs-lookup"><span data-stu-id="cad65-213">gRPC</span></span>

<span data-ttu-id="cad65-214">[GRPC](https://grpc.io/)'de çok preformance geliştirmeleri yapılmıştır.</span><span class="sxs-lookup"><span data-stu-id="cad65-214">Many preformance improvements have been made in [gRPC](https://grpc.io/).</span></span> <span data-ttu-id="cad65-215">Daha fazla bilgi için bkz. [.NET 5 ' teki GRPC performansı geliştirmeleri](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/).</span><span class="sxs-lookup"><span data-stu-id="cad65-215">For more information, see [gRPC performance improvements in .NET 5](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/).</span></span>

<span data-ttu-id="cad65-216">Daha fazla gRPC bilgisi için bkz <xref:grpc/index> ..</span><span class="sxs-lookup"><span data-stu-id="cad65-216">For more gRPC information, see <xref:grpc/index>.</span></span>

## :::no-loc(SignalR):::

<span data-ttu-id="cad65-217">:::no-loc(SignalR)::: ASP.NET ' de hub işlem hatları adı verilen Merkez filtreleri, :::no-loc(SignalR)::: kodun hub yöntemleri çağrılmadan önce ve sonra çalışmasına izin veren bir özelliktir.</span><span class="sxs-lookup"><span data-stu-id="cad65-217">:::no-loc(SignalR)::: Hub filters, called Hub pipelines in ASP.NET :::no-loc(SignalR):::, is a feature that allows code to run before and after Hub methods are called.</span></span> <span data-ttu-id="cad65-218">Hub yöntemleri çağrılmadan önce ve sonra kodu çalıştırmak, ara yazılımın bir HTTP isteğinden önce ve sonra kodu çalıştırma yeteneğine benzer.</span><span class="sxs-lookup"><span data-stu-id="cad65-218">Running code before and after Hub methods are called is similar to how middleware has the ability to run code before and after an HTTP request.</span></span> <span data-ttu-id="cad65-219">Ortak kullanımlar arasında günlük kaydı, hata işleme ve bağımsız değişken doğrulama.</span><span class="sxs-lookup"><span data-stu-id="cad65-219">Common uses include logging, error handling, and argument validation.</span></span>

<span data-ttu-id="cad65-220">Daha fazla bilgi için bkz. [ASP.NET Core :::no-loc(SignalR)::: hub filtrelerini kullanma ](xref:signalr/hub-filters).</span><span class="sxs-lookup"><span data-stu-id="cad65-220">For more information, see [Use hub filters in ASP.NET Core :::no-loc(SignalR):::](xref:signalr/hub-filters).</span></span>

### <a name="no-locsignalr-parallel-hub-invocations"></a><span data-ttu-id="cad65-221">:::no-loc(SignalR)::: paralel hub etkinleştirmeleri</span><span class="sxs-lookup"><span data-stu-id="cad65-221">:::no-loc(SignalR)::: parallel hub invocations</span></span>

<span data-ttu-id="cad65-222">ASP.NET Core :::no-loc(SignalR)::: artık paralel hub çağırmaları işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="cad65-222">ASP.NET Core :::no-loc(SignalR)::: is now capable of handling parallel hub invocations.</span></span> <span data-ttu-id="cad65-223">Varsayılan davranış, istemcilerin tek seferde birden fazla hub yöntemi çağırmasına izin verecek şekilde değiştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="cad65-223">The default behavior can be changed to allow clients to invoke more than one hub method at a time:</span></span>

[!code-csharp[](~/release-notes/sample/Startup:::no-loc(SignalR):::hubs.cs?name=snippet)]

### <a name="added-messagepack-support-in-no-locsignalr-java-client"></a><span data-ttu-id="cad65-224">Java istemcisinde MessagePack desteği eklendi :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="cad65-224">Added Messagepack support in :::no-loc(SignalR)::: Java client</span></span>

<span data-ttu-id="cad65-225">Yeni bir [com. Microsoft. SignalR. MessagePack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack)paketi, Java Istemcisine MessagePack desteği ekler :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="cad65-225">A new package, [com.microsoft.signalr.messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack), adds MessagePack support to the :::no-loc(SignalR)::: Java client.</span></span> <span data-ttu-id="cad65-226">MessagePack hub protokolünü kullanmak için `.withHubProtocol(new MessagePackHubProtocol())` bağlantı oluşturucusuna ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cad65-226">To use the MessagePack hub protocol, add `.withHubProtocol(new MessagePackHubProtocol())` to the connection builder:</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create(
                           "http://localhost:53353/MyHub")
               .withHubProtocol(new MessagePackHubProtocol())
               .build();
```

<!--
See [Update :::no-loc(SignalR)::: code](xref:migration/31-to-50#signalr) for migration instructions.
-->

## :::no-loc(Kestrel):::

* <span data-ttu-id="cad65-227">Yapılandırma aracılığıyla yeniden yüklenebilir uç noktalar: :::no-loc(Kestrel)::: var olan uç noktaların [ :::no-loc(Kestrel):::ServerOptions.Config](xref:Microsoft.AspNetCore.Server.:::no-loc(Kestrel):::.Core.:::no-loc(Kestrel):::ServerOptions.Configure%2A) Me ve bağını çözme ve parametre olduğunda bir uygulamanın yeniden başlatılmasına gerek kalmadan yeni uç noktalara bağlama gibi yapılandırma değişikliklerini algılayabilir `reloadOnChange` `true` .</span><span class="sxs-lookup"><span data-stu-id="cad65-227">Reloadable endpoints via configuration: :::no-loc(Kestrel)::: can detect changes to configuration passed to [:::no-loc(Kestrel):::ServerOptions.Configure](xref:Microsoft.AspNetCore.Server.:::no-loc(Kestrel):::.Core.:::no-loc(Kestrel):::ServerOptions.Configure%2A) and unbind from existing endpoints and bind to new endpoints without requiring an application restart when the `reloadOnChange` parameter is `true`.</span></span> <span data-ttu-id="cad65-228">Veya kullanırken varsayılan olarak <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> , :::no-loc(Kestrel)::: etkin olan [" :::no-loc(Kestrel)::: "](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) yapılandırma alt bölümüne bağlanır `reloadOnChange` .</span><span class="sxs-lookup"><span data-stu-id="cad65-228">By default when using <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> or <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, :::no-loc(Kestrel)::: binds to the [":::no-loc(Kestrel):::"](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) configuration subsection with `reloadOnChange` enabled.</span></span> <span data-ttu-id="cad65-229">`reloadOnChange: true` `:::no-loc(Kestrel):::ServerOptions.Configure` Yeniden yüklenebilir uç noktaları almak için uygulamalar el ile çağrılırken geçmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="cad65-229">Apps must pass `reloadOnChange: true` when calling `:::no-loc(Kestrel):::ServerOptions.Configure` manually to get reloadable endpoints.</span></span>
* <span data-ttu-id="cad65-230">HTTP/2 yanıt üst bilgileri geliştirmeleri.</span><span class="sxs-lookup"><span data-stu-id="cad65-230">HTTP/2 response headers improvements.</span></span> <span data-ttu-id="cad65-231">Daha fazla bilgi için, sonraki bölümde [performans iyileştirmeleri](#performance-improvements) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="cad65-231">For more information, see [Performance improvements](#performance-improvements) in the next section.</span></span>
* <span data-ttu-id="cad65-232">Yuva aktarımında ek uç nokta türleri için destek: ' de tanıtılan yeni API 'ye ekleme <xref:System.Net.Sockets?displayProperty=nameWithType> , içindeki yuva varsayılan taşıması, [:::no-loc(Kestrel):::](xref:fundamentals/servers/kestrel) var olan dosya tanıtıcıları ve UNIX etki alanı yuvaları bağlamaya olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="cad65-232">Support for additional endpoints types in the sockets transport: Adding to the new API introduced in <xref:System.Net.Sockets?displayProperty=nameWithType>, the sockets default transport in [:::no-loc(Kestrel):::](xref:fundamentals/servers/kestrel) allows binding to both existing file handles and Unix domain sockets.</span></span> <span data-ttu-id="cad65-233">Mevcut dosya işleyicilerine bağlama desteği `Systemd` , taşıma gerekmeden mevcut tümleştirmeyi kullanmayı sağlar `libuv` .</span><span class="sxs-lookup"><span data-stu-id="cad65-233">Support for binding to existing file handles enables using the existing `Systemd` integration without requiring the `libuv` transport.</span></span>
* <span data-ttu-id="cad65-234">İçinde özel üst bilgi kodu çözme :::no-loc(Kestrel)::: : uygulamalar, <xref:System.Text.Encoding> UTF-8 için varsayılan olarak, üst bilgi adına göre gelen üstbilgileri yorumlamak için hangisinin kullanılacağını belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="cad65-234">Custom header decoding in :::no-loc(Kestrel):::: Apps can specify which <xref:System.Text.Encoding> to use to interpret incoming headers based on the header name instead of defaulting to UTF-8.</span></span> <span data-ttu-id="cad65-235">Şunu ayarlayın</span><span class="sxs-lookup"><span data-stu-id="cad65-235">Set the</span></span> <!--<xref:Microsoft.AspNetCore.Server.:::no-loc(Kestrel):::.:::no-loc(Kestrel):::ServerOptions.RequestHeaderEncodingSelector> --> <span data-ttu-id="cad65-236">`Microsoft.AspNetCore.Server.:::no-loc(Kestrel):::.:::no-loc(Kestrel):::ServerOptions.RequestHeaderEncodingSelector` kullanılacak kodlamayı belirtmek için özelliği:</span><span class="sxs-lookup"><span data-stu-id="cad65-236">`Microsoft.AspNetCore.Server.:::no-loc(Kestrel):::.:::no-loc(Kestrel):::ServerOptions.RequestHeaderEncodingSelector` property to specify which encoding to use:</span></span>
 
  ```csharp
  public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.Configure:::no-loc(Kestrel):::(options =>
            {
                options.RequestHeaderEncodingSelector = encoding =>
                {
                      return encoding switch
                      {
                          "Host" => System.Text.Encoding.Latin1,
                          _ => System.Text.Encoding.UTF8,
                      };
                };
            });
            webBuilder.UseStartup<Startup>();
        });
  ```

### <a name="no-lockestrel-endpoint-specific-options-via-configuration"></a><span data-ttu-id="cad65-237">:::no-loc(Kestrel)::: yapılandırma aracılığıyla uç noktaya özel seçenekler</span><span class="sxs-lookup"><span data-stu-id="cad65-237">:::no-loc(Kestrel)::: endpoint-specific options via configuration</span></span>

<span data-ttu-id="cad65-238">:::no-loc(Kestrel)::: [Yapılandırma](xref:fundamentals/configuration/index)yoluyla uç noktaya özel seçenekleri yapılandırmak için destek eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="cad65-238">Support has been added for configuring :::no-loc(Kestrel):::’s endpoint-specific options via [configuration](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="cad65-239">Uç noktaya özgü yapılandırmalarda şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="cad65-239">The endpoint-specific configurations includes the:</span></span>

* <span data-ttu-id="cad65-240">Kullanılan HTTP protokolleri</span><span class="sxs-lookup"><span data-stu-id="cad65-240">HTTP protocols used</span></span>
* <span data-ttu-id="cad65-241">Kullanılan TLS protokolleri</span><span class="sxs-lookup"><span data-stu-id="cad65-241">TLS protocols used</span></span>
* <span data-ttu-id="cad65-242">Sertifika seçildi</span><span class="sxs-lookup"><span data-stu-id="cad65-242">Certificate selected</span></span>
* <span data-ttu-id="cad65-243">Cient sertifika modu</span><span class="sxs-lookup"><span data-stu-id="cad65-243">Cient certificate mode</span></span>

<span data-ttu-id="cad65-244">Yapılandırma, belirtilen sunucu adına göre hangi sertifikanın seçili olduğunu belirtmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="cad65-244">Configuration allows specifying which certificate is selected based on the specified server name.</span></span> <span data-ttu-id="cad65-245">Sunucu adı, istemci tarafından gösterilen TLS protokolüne Sunucu Adı Belirtme (SNı) uzantısının bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="cad65-245">The server name is part of the Server Name Indication (SNI) extension to the TLS protocol as indicated by the client.</span></span> <span data-ttu-id="cad65-246">:::no-loc(Kestrel):::yapılandırması aynı zamanda ana bilgisayar adında bir joker öneki de destekler.</span><span class="sxs-lookup"><span data-stu-id="cad65-246">:::no-loc(Kestrel):::'s configuration also supports a wildcard prefix in the host name.</span></span>

<span data-ttu-id="cad65-247">Aşağıdaki örnek, bir yapılandırma dosyası kullanarak uç noktaya özel belirtme gösterir:</span><span class="sxs-lookup"><span data-stu-id="cad65-247">The following example shows how to specify endpoint-specific using a configuration file:</span></span>

```json
{
  ":::no-loc(Kestrel):::": {
    "Endpoints": {
      "EndpointName": {
        "Url": "https://*",
        "Sni": {
          "a.example.org": {
            "Protocols": "Http1AndHttp2",
            "SslProtocols": [ "Tls11", "Tls12"],
            "Certificate": {
              "Path": "testCert.pfx",
              "Password": "testPassword"
            },
            "ClientCertificateMode" : "NoCertificate"
          },
          "*.example.org": {
            "Certificate": {
              "Path": "testCert2.pfx",
              "Password": "testPassword"
            }
          },
          "*": {
            // At least one sub-property needs to exist per
            // SNI section or it cannot be discovered via
            // IConfiguration
            "Protocols": "Http1",
          }
        }
      }
    }
  }
}
```

<span data-ttu-id="cad65-248">Sunucu Adı Belirtme (SNı), SSL anlaşmasının bir parçası olarak sanal etki alanı dahil etmek için bir TLS uzantısıdır.</span><span class="sxs-lookup"><span data-stu-id="cad65-248">Server Name Indication (SNI) is a TLS extension to include a virtual domain as a part of SSL negotiation.</span></span> <span data-ttu-id="cad65-249">Bunun ne kadar etkili olduğu, sanal etki alanı adının veya ana bilgisayar adının ağ uç noktasını tanımlamak için kullanılabileceği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="cad65-249">What this effectively means is that the virtual domain name, or a hostname, can be used to identify the network end point.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="cad65-250">Performans iyileştirmeleri</span><span class="sxs-lookup"><span data-stu-id="cad65-250">Performance improvements</span></span>

### <a name="http2"></a><span data-ttu-id="cad65-251">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="cad65-251">HTTP/2</span></span>

* <span data-ttu-id="cad65-252">HTTP/2 kod yolundaki ayırmalarda önemli indirimler.</span><span class="sxs-lookup"><span data-stu-id="cad65-252">Significant reductions in allocations in the HTTP/2 code path.</span></span>
* <span data-ttu-id="cad65-253">İçindeki HTTP/2 yanıt üst bilgilerinin [HPack dinamik sıkıştırması](https://tools.ietf.org/html/rfc7541) desteği [:::no-loc(Kestrel):::](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="cad65-253">Support for [HPack dynamic compression](https://tools.ietf.org/html/rfc7541) of HTTP/2 response headers in [:::no-loc(Kestrel):::](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="cad65-254">Daha fazla bilgi için bkz. [üst bilgi tablosu boyutu](xref:fundamentals/servers/kestrel#header-table-size) ve [HPack: http/2 sessiz Killer (özellik)](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/).</span><span class="sxs-lookup"><span data-stu-id="cad65-254">For more information, see [Header table size](xref:fundamentals/servers/kestrel#header-table-size) and [HPACK: the silent killer (feature) of HTTP/2](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/).</span></span>
* <span data-ttu-id="cad65-255">HTTP/2 PING çerçeveleri gönderme: HTTP/2, boşta bağlantının hala çalışır durumda olduğundan emin olmak için PING çerçeveleri göndermeye yönelik bir mekanizmaya sahiptir.</span><span class="sxs-lookup"><span data-stu-id="cad65-255">Sending HTTP/2 PING frames: HTTP/2 has a mechanism for sending PING frames to ensure an idle connection is still functional.</span></span> <span data-ttu-id="cad65-256">Uygun bir bağlantının, genellikle boşta olan uzun süreli akışlar ile çalışırken yararlı olması, ancak örneğin gRPC akışları gibi yalnızca zaman aralıklı etkinlik olması durumunda faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="cad65-256">Ensuring a viable connection is especially useful when working with long-lived streams that are often idle but only intermittently see activity, for example, gRPC streams.</span></span> <span data-ttu-id="cad65-257">Uygulamalar, ' de sınırları ayarlayarak düzenli olarak PING çerçeveleri gönderebilir [:::no-loc(Kestrel):::](xref:fundamentals/servers/kestrel) <xref:Microsoft.AspNetCore.Server.:::no-loc(Kestrel):::.:::no-loc(Kestrel):::ServerOptions> :</span><span class="sxs-lookup"><span data-stu-id="cad65-257">Apps can send periodic PING frames in [:::no-loc(Kestrel):::](xref:fundamentals/servers/kestrel) by setting limits on <xref:Microsoft.AspNetCore.Server.:::no-loc(Kestrel):::.:::no-loc(Kestrel):::ServerOptions>:</span></span>

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.Configure:::no-loc(Kestrel):::(options =>
            {
                options.Limits.Http2.KeepAlivePingInterval =
                                               TimeSpan.FromSeconds(10);
                options.Limits.Http2.KeepAlivePingTimeout =
                                               TimeSpan.FromSeconds(1);
            });
            webBuilder.UseStartup<Startup>();
        });
   ```
   <!-- review: KeepAlivePingInterval not found in RC1. Try testing with RC1. See https://github.com/dotnet/aspnetcore/pull/22565/files see C:/Users/riande/source/repos/WebApplication128/WebApplication128 -->

### <a name="containers"></a><span data-ttu-id="cad65-258">Kapsayıcılar</span><span class="sxs-lookup"><span data-stu-id="cad65-258">Containers</span></span>

<span data-ttu-id="cad65-259">.NET 5,0 ' den önce, bir ASP.NET Core uygulaması için bir *Dockerfile* oluşturup yayımlarken, tüm .NET Core SDK ve ASP.NET Core görüntüsünün çekmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="cad65-259">Prior to .NET 5.0, building and publishing a *Dockerfile* for an ASP.NET Core app required pulling the entire .NET Core SDK and the ASP.NET Core image.</span></span> <span data-ttu-id="cad65-260">Bu sürümle birlikte, SDK görüntüleri baytları çekililerek ASP.NET Core görüntü için çekilen baytlar büyük ölçüde ortadan kalkar.</span><span class="sxs-lookup"><span data-stu-id="cad65-260">With this release, pulling the SDK images bytes is reduced and the bytes pulled for the ASP.NET Core image is largely eliminated.</span></span> <span data-ttu-id="cad65-261">Daha fazla bilgi için bkz. [Bu GitHub sorun açıklaması](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750).</span><span class="sxs-lookup"><span data-stu-id="cad65-261">For more information, see [this GitHub issue comment](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750).</span></span>

## <a name="authentication-and-authorization"></a><span data-ttu-id="cad65-262">Kimlik doğrulaması ve yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="cad65-262">Authentication and authorization</span></span>

### <a name="azure-active-directory-authentication-with-microsoftno-locidentityweb"></a><span data-ttu-id="cad65-263">Microsoft ile kimlik doğrulaması Azure Active Directory. :::no-loc(Identity)::: . Web</span><span class="sxs-lookup"><span data-stu-id="cad65-263">Azure Active Directory authentication with Microsoft.:::no-loc(Identity):::.Web</span></span>

<span data-ttu-id="cad65-264">ASP.NET Core proje şablonları, <xref:Microsoft.:::no-loc(Identity):::.Web?displayProperty=fullName> [Azure etkinlik dizini](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) ile kimlik doğrulamasını işleyecek şekilde ile tümleşir.</span><span class="sxs-lookup"><span data-stu-id="cad65-264">The ASP.NET Core project templates now integrate with <xref:Microsoft.:::no-loc(Identity):::.Web?displayProperty=fullName> to handle authentication with [Azure Activity Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD).</span></span> <span data-ttu-id="cad65-265">[Microsoft. :::no-loc(Identity)::: . Web paketi](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web/) şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="cad65-265">The [Microsoft.:::no-loc(Identity):::.Web package](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web/) provides:</span></span>

* <span data-ttu-id="cad65-266">Azure AD ile kimlik doğrulaması için daha iyi bir deneyim.</span><span class="sxs-lookup"><span data-stu-id="cad65-266">A better experience for authentication through Azure AD.</span></span>
* <span data-ttu-id="cad65-267">[Microsoft Graph](/graph/overview)dahil olmak üzere, kullanıcılarınız adına Azure kaynaklarına erişmenin daha kolay bir yolu.</span><span class="sxs-lookup"><span data-stu-id="cad65-267">An easier way to access Azure resources on behalf of your users, including [Microsoft Graph](/graph/overview).</span></span> <span data-ttu-id="cad65-268">Bkz [. Microsoft. :::no-loc(Identity)::: . ](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)Temel bir oturum açma ile başlayan ve Azure API 'leri kullanarak, Microsoft Graph kullanarak ve kendi API 'lerinizi koruyan Web örneği.</span><span class="sxs-lookup"><span data-stu-id="cad65-268">See the [Microsoft.:::no-loc(Identity):::.Web sample](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2), which starts with a basic login and advances through multi-tenancy, using Azure APIs, using Microsoft Graph, and protecting your own APIs.</span></span> <span data-ttu-id="cad65-269">`Microsoft.:::no-loc(Identity):::.Web` , .NET 5 ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="cad65-269">`Microsoft.:::no-loc(Identity):::.Web` is available alongside .NET 5.</span></span>

### <a name="allow-anonymous-access-to-an-endpoint"></a><span data-ttu-id="cad65-270">Bir uç noktaya anonim erişime izin ver</span><span class="sxs-lookup"><span data-stu-id="cad65-270">Allow anonymous access to an endpoint</span></span>

<span data-ttu-id="cad65-271">`AllowAnonymous`Uzantı yöntemi bir uç noktaya anonim erişime izin verir:</span><span class="sxs-lookup"><span data-stu-id="cad65-271">The `AllowAnonymous` extension method allows anonymous access to an endpoint:</span></span>

[!code-csharp[](~/release-notes/sample/StartupAnonEndpoint.cs?name=snippet)]

### <a name="custom-handling-of-authorization-failures"></a><span data-ttu-id="cad65-272">Özel yetkilendirme hatalarının işlenmesi</span><span class="sxs-lookup"><span data-stu-id="cad65-272">Custom handling of authorization failures</span></span>

<span data-ttu-id="cad65-273">Yetkilendirme hatalarının özel işlenmesi artık, [Yetkilendirme](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) [ara yazılımı](xref:fundamentals/middleware/index)tarafından çağrılan yeni [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs) arabirimi ile daha kolay olur.</span><span class="sxs-lookup"><span data-stu-id="cad65-273">Custom handling of authorization failures is now easier with the new [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs) interface that is invoked by the [authorization](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="cad65-274">Varsayılan uygulama aynı kalır, ancak özel bir işleyici, kimlik doğrulaması neden başarısız olduğuna göre özel HTTP yanıtlarına izin veren bağımlılık ekleme ' ye kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="cad65-274">The default implementation remains the same, but a custom handler can be registered in [Dependency injection, which allows custom HTTP responses based on why authorization failed.</span></span> <span data-ttu-id="cad65-275">Öğesinin kullanımını gösteren [Bu örneğe](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs) bakın `IAuthorizationMiddlewareResultHandler` .</span><span class="sxs-lookup"><span data-stu-id="cad65-275">See [this sample](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs) that demonstrates usage of the `IAuthorizationMiddlewareResultHandler`.</span></span>

### <a name="authorization-when-using-endpoint-routing"></a><span data-ttu-id="cad65-276">Endpoint Routing kullanılırken yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="cad65-276">Authorization when using endpoint routing</span></span>

<span data-ttu-id="cad65-277">Endpoint Routing kullanılırken yetkilendirme, artık `HttpContext` uç nokta örneği yerine alır.</span><span class="sxs-lookup"><span data-stu-id="cad65-277">Authorization when using endpoint routing now receives the `HttpContext` rather than the endpoint instance.</span></span> <span data-ttu-id="cad65-278">Bu, yetkilendirme ara yazılımı tarafından `RouteData` erişilemeyen öğesinin ve diğer özelliklerine erişmesine olanak tanır `HttpContext` <xref:Microsoft.AspNetCore.Http.Endpoint> .</span><span class="sxs-lookup"><span data-stu-id="cad65-278">This allows the authorization middleware to access the `RouteData` and other properties of the `HttpContext` that were not accessible though the <xref:Microsoft.AspNetCore.Http.Endpoint> class.</span></span> <span data-ttu-id="cad65-279">Uç nokta bağlam kullanılarak içerikten getirilebilir [. GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A).</span><span class="sxs-lookup"><span data-stu-id="cad65-279">The endpoint can be fetched from the context using [context.GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A).</span></span>

### <a name="role-based-access-control-with-kerberos-authentication-and-ldap-on-linux"></a><span data-ttu-id="cad65-280">Linux üzerinde Kerberos kimlik doğrulaması ve LDAP ile rol tabanlı erişim denetimi</span><span class="sxs-lookup"><span data-stu-id="cad65-280">Role-based access control with Kerberos authentication and LDAP on Linux</span></span>

<span data-ttu-id="cad65-281">Bkz. [Kerberos kimlik doğrulaması ve rol tabanlı erişim denetimi (RBAC)](xref:security/authentication/windowsauth#rbac)</span><span class="sxs-lookup"><span data-stu-id="cad65-281">See [Kerberos authentication and role-based access control (RBAC)](xref:security/authentication/windowsauth#rbac)</span></span>

## <a name="api-improvements"></a><span data-ttu-id="cad65-282">API geliştirmeleri</span><span class="sxs-lookup"><span data-stu-id="cad65-282">API improvements</span></span>

### <a name="json-extension-methods-for-httprequest-and-httpresponse"></a><span data-ttu-id="cad65-283">HttpRequest ve HttpResponse için JSON uzantısı yöntemleri</span><span class="sxs-lookup"><span data-stu-id="cad65-283">JSON extension methods for HttpRequest and HttpResponse</span></span>

<span data-ttu-id="cad65-284">JSON verileri, ve ' dan ' a okunabilir ve yazılabilir ve `HttpRequest` `HttpResponse` Yeni <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> ve `WriteAsJsonAsync` genişletme yöntemleri kullanılarak yapılabilir.</span><span class="sxs-lookup"><span data-stu-id="cad65-284">JSON data can be read and written to from an `HttpRequest` and `HttpResponse` using the new <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> and `WriteAsJsonAsync` extension methods.</span></span> <span data-ttu-id="cad65-285">Bu uzantı yöntemleri JSON verilerini işlemek için serileştiricide [System.Text.Js](xref:System.Text.Json) kullanır.</span><span class="sxs-lookup"><span data-stu-id="cad65-285">These extension methods use the [System.Text.Json](xref:System.Text.Json) serializer to handle the JSON data.</span></span> <span data-ttu-id="cad65-286">Yeni `HasJsonContentType` genişletme yöntemi bir ISTEĞIN JSON içerik türüne sahip olup olmadığını da denetleyebilir.</span><span class="sxs-lookup"><span data-stu-id="cad65-286">The new `HasJsonContentType` extension method can also check if a request has a JSON content type.</span></span>

<span data-ttu-id="cad65-287">JSON uzantısı yöntemleri, JSON API 'Leri bir [endpoint routing](xref:fundamentals/routing) programlama stilinde, \* \*. **koda yol** \* çağırıyoruz.</span><span class="sxs-lookup"><span data-stu-id="cad65-287">The JSON extension methods can be combined with [endpoint routing](xref:fundamentals/routing) to create JSON APIs in a style of programming we call \* **route to code** _.</span></span> <span data-ttu-id="cad65-288">Basit bir şekilde temel JSON API 'Leri oluşturmak isteyen geliştiriciler için yeni bir seçenektir.</span><span class="sxs-lookup"><span data-stu-id="cad65-288">It is a new option for developers who want to create basic JSON APIs in a lightweight way.</span></span> <span data-ttu-id="cad65-289">Örneğin, yalnızca birkaç uç nokta içeren bir Web uygulaması, ASP.NET Core MVC 'nin tüm işlevleri yerine kod yolunu kullanmayı seçebilir:</span><span class="sxs-lookup"><span data-stu-id="cad65-289">For example, a web app that has only a handful of endpoints might choose to use route to code rather than the full functionality of ASP.NET Core MVC:</span></span>

```csharp
endpoints.MapGet("/weather/{city:alpha}", async context =>
{
    var city = (string)context.Request.RouteValues["city"];
    var weather = GetFromDatabase(city);

    await context.Response.WriteAsJsonAsync(weather);
});
```

<span data-ttu-id="cad65-290">Yeni JSON uzantısı yöntemleri ve koda yönlendirme hakkında daha fazla bilgi için bkz. [bu .net Show](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code).</span><span class="sxs-lookup"><span data-stu-id="cad65-290">For more information on the new JSON extension methods and route to code, see [this .NET show](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code).</span></span>

### <a name="systemdiagnosticsactivity"></a><span data-ttu-id="cad65-291">System. Diagnostics. Activity</span><span class="sxs-lookup"><span data-stu-id="cad65-291">System.Diagnostics.Activity</span></span>

<span data-ttu-id="cad65-292">Varsayılan biçimi varsayılan <xref:System.Diagnostics.Activity?displayProperty=fullName> olarak W3C biçimindedir.</span><span class="sxs-lookup"><span data-stu-id="cad65-292">The default format for <xref:System.Diagnostics.Activity?displayProperty=fullName> now defaults to the W3C format.</span></span> <span data-ttu-id="cad65-293">Bu, dağıtılmış izleme desteğini varsayılan olarak daha fazla çerçeve ile ASP.NET Core birlikte çalışabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="cad65-293">This makes distributed tracing support in ASP.NET Core interoperable with more frameworks by default.</span></span>

### <a name="frombodyattribute"></a><span data-ttu-id="cad65-294">FromBodyAttribute</span><span class="sxs-lookup"><span data-stu-id="cad65-294">FromBodyAttribute</span></span>

<span data-ttu-id="cad65-295"><xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> Ow, bu parametrelerin veya özelliklerin isteğe bağlı olarak kabul edilmesine izin veren bir seçeneğin yapılandırılmasını destekler:</span><span class="sxs-lookup"><span data-stu-id="cad65-295"><xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> ow supports configuring an option that allows these parameters or properties to be considered optional:</span></span>

```csharp
public IActionResult Post([FromBody(EmptyBodyBehavior = EmptyBodyBehavior.Allow)]
                           MyModel model) {
     ...
     }
```

## <a name="miscellaneous-improvements"></a><span data-ttu-id="cad65-296">Çeşitli geliştirmeler</span><span class="sxs-lookup"><span data-stu-id="cad65-296">Miscellaneous improvements</span></span>

<span data-ttu-id="cad65-297">ASP.NET Core derlemelerine [null yapılabilir ek açıklamalar](/dotnet/csharp/nullable-references#attributes-describe-apis) uygulamaya başladık.</span><span class="sxs-lookup"><span data-stu-id="cad65-297">We’ve started applying [nullable annotations](/dotnet/csharp/nullable-references#attributes-describe-apis) to ASP.NET Core assemblies.</span></span> <span data-ttu-id="cad65-298">.NET 5 çerçevesinin ortak genel API yüzeyinin çoğuna açıklama eklemek için planlıyoruz.</span><span class="sxs-lookup"><span data-stu-id="cad65-298">We plan to annotate most of the common public API surface of the .NET 5 framework.</span></span> <!-- Review: what's the impact of this? How does it work? Need more info.  Check the link I added -->

### <a name="control-startup-class-activation"></a><span data-ttu-id="cad65-299">Başlangıç sınıfı etkinleştirmeyi denetleme</span><span class="sxs-lookup"><span data-stu-id="cad65-299">Control Startup class activation</span></span>

<span data-ttu-id="cad65-300"><xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A>Uygulamanın sınıf etkinleştirmesini denetlemek için bir fabrika yöntemi sağlamasına imkan tanıyan ek bir aşırı yükleme eklenmiştir `Startup` .</span><span class="sxs-lookup"><span data-stu-id="cad65-300">An additional <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A> overload has been added that lets an app provide a factory method for controlling `Startup` class activation.</span></span> <span data-ttu-id="cad65-301">`Startup`Sınıf etkinleştirmeyi denetlemek, `Startup` ana bilgisayarla birlikte başlatılan ek parametreleri geçirmek için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="cad65-301">Controlling `Startup` class activation is useful to pass additional parameters to `Startup` that are initialized along with the host:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var logger = CreateLogger();
        var host = Host.CreateDefaultBuilder()
            .ConfigureWebHost(builder =>
            {
                builder.UseStartup(context => new Startup(logger));
            })
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="auto-refresh-with-dotnet-watch"></a><span data-ttu-id="cad65-302">DotNet Watch ile otomatik yenileme</span><span class="sxs-lookup"><span data-stu-id="cad65-302">Auto refresh with dotnet watch</span></span>

<span data-ttu-id="cad65-303">.NET 5 ' te, bir ASP.NET Core projesi üzerinde [DotNet izleme](xref:tutorials/dotnet-watch) çalıştırmak, her ikisi de varsayılan tarayıcıyı başlatır ve kodda değişiklik yapıldığında tarayıcıyı otomatik olarak yeniler.</span><span class="sxs-lookup"><span data-stu-id="cad65-303">In .NET 5, running [dotnet watch](xref:tutorials/dotnet-watch) on an ASP.NET Core project both launches the default browser and auto refreshes the browser as changes are made to the code.</span></span> <span data-ttu-id="cad65-304">Yani şunları yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="cad65-304">This means you can:</span></span>

<span data-ttu-id="cad65-305">_ Metin düzenleyicisinde bir ASP.NET Core projesi açın.</span><span class="sxs-lookup"><span data-stu-id="cad65-305">_ Open an ASP.NET Core project in a text editor.</span></span>
* <span data-ttu-id="cad65-306">Şu komutu çalıştırın: `dotnet watch`.</span><span class="sxs-lookup"><span data-stu-id="cad65-306">Run `dotnet watch`.</span></span>
* <span data-ttu-id="cad65-307">Araç, uygulamayı yeniden oluşturma, yeniden başlatma ve yeniden yükleme işlemini gerçekleştirirken kod değişikliğine odaklanırsınız.</span><span class="sxs-lookup"><span data-stu-id="cad65-307">Focus on the code changes while the tooling handles rebuilding, restarting, and reloading the app.</span></span>

<span data-ttu-id="cad65-308">Otomatik yenileme işlevini gelecekte Visual Studio 'ya getirmeyi umuyoruz.</span><span class="sxs-lookup"><span data-stu-id="cad65-308">We hope to bring the auto refresh functionality to Visual Studio in the future.</span></span>

### <a name="console-logger-formatter"></a><span data-ttu-id="cad65-309">Konsol günlükçü biçimlendiricisi</span><span class="sxs-lookup"><span data-stu-id="cad65-309">Console Logger Formatter</span></span>

<span data-ttu-id="cad65-310">Kitaplığındaki konsol günlüğü sağlayıcısında geliştirmeler yapılmıştır `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="cad65-310">Improvements have been made to the console log provider in the `Microsoft.Extensions.Logging` library.</span></span> <span data-ttu-id="cad65-311">Geliştiriciler artık `ConsoleFormatter` konsol çıktısının biçimlendirilmesi ve renklendirme üzerinde denetimi tamamen denetleyebilmesi için özel bir uygulama uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="cad65-311">Developers can now implement a custom `ConsoleFormatter` to exercise complete control over formatting and colorization of the console output.</span></span> <span data-ttu-id="cad65-312">Biçimlendirici API 'Leri, VT-100 kaçış sıralarının bir alt kümesini uygulayarak zengin biçimlendirme sağlar.</span><span class="sxs-lookup"><span data-stu-id="cad65-312">The formatter APIs allow for rich formatting by implementing a subset of the VT-100 escape sequences.</span></span> <span data-ttu-id="cad65-313">VT-100, çoğu modern terminalde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="cad65-313">VT-100 is supported by most modern terminals.</span></span> <span data-ttu-id="cad65-314">Konsol günlükçüsü, geliştiricilerin tüm terminallerde tek bir biçimlendirici yazarmasına izin veren desteklenmeyen terminallerde kaçış dizilerini ayrıştırabilirler.</span><span class="sxs-lookup"><span data-stu-id="cad65-314">The console logger can parse out escape sequences on unsupported terminals allowing developers to author a single formatter for all terminals.</span></span>

### <a name="json-console-logger"></a><span data-ttu-id="cad65-315">JSON konsol günlükçüsü</span><span class="sxs-lookup"><span data-stu-id="cad65-315">JSON Console Logger</span></span>

<span data-ttu-id="cad65-316">Özel formatlayıcılar için desteğe ek olarak, yapılandırılmış JSON günlüklerini konsola yayan bir yerleşik JSON biçimlendirici de ekledik.</span><span class="sxs-lookup"><span data-stu-id="cad65-316">In addition to support for custom formatters, we’ve also added a built-in JSON formatter that emits structured JSON logs to the console.</span></span> <span data-ttu-id="cad65-317">Aşağıdaki kod, varsayılan günlükçü 'dan JSON 'a nasıl geçiş yapılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="cad65-317">The following code shows how to switch from the default logger to JSON:</span></span>

[!code-csharp[](~/release-notes/sample/ProgramJsonLog.cs?name=snippet)]

<span data-ttu-id="cad65-318">Konsola yayılan günlük iletileri JSON olarak biçimlendirilir:</span><span class="sxs-lookup"><span data-stu-id="cad65-318">Log messages emitted to the console are JSON formatted:</span></span>

```json
{
  "EventId": 0,
  "LogLevel": "Information",
  "Category": "Microsoft.Hosting.Lifetime",
  "Message": "Now listening on: https://localhost:5001",
  "State": {
    "Message": "Now listening on: https://localhost:5001",
    "address": "https://localhost:5001",
    "{OriginalFormat}": "Now listening on: {address}"
  }
}
```