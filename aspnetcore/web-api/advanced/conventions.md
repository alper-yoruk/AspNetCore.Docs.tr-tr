---
title: Web API 'SI kurallarını kullanma
author: pranavkm
description: ASP.NET Core Web API kuralları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.2'
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/conventions
ms.openlocfilehash: f74327cd5bb6a5794c90ffdd3896f2b343e175a6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774892"
---
# <a name="use-web-api-conventions"></a><span data-ttu-id="2b729-103">Web API 'SI kurallarını kullanma</span><span class="sxs-lookup"><span data-stu-id="2b729-103">Use web API conventions</span></span>

<span data-ttu-id="2b729-104">, [Pranav Krishnamoorthy](https://github.com/pranavkm) ve [Scott Ade](https://github.com/scottaddie) tarafından</span><span class="sxs-lookup"><span data-stu-id="2b729-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="2b729-105">ASP.NET Core 2,2 ve üzeri, ortak [API belgelerini](xref:tutorials/web-api-help-pages-using-swagger) ayıklamanızı ve bunu birden çok eyleme, denetleyiciye veya bir derleme içindeki tüm denetleyicilere uygulamanıza yönelik bir yol içerir.</span><span class="sxs-lookup"><span data-stu-id="2b729-105">ASP.NET Core 2.2 and later includes a way to extract common [API documentation](xref:tutorials/web-api-help-pages-using-swagger) and apply it to multiple actions, controllers, or all controllers within an assembly.</span></span> <span data-ttu-id="2b729-106">Web API kuralları, ile [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute)tek tek eylemleri dekorasyon bir yerdir.</span><span class="sxs-lookup"><span data-stu-id="2b729-106">Web API conventions are a substitute for decorating individual actions with [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).</span></span>

<span data-ttu-id="2b729-107">Bir kural şunları yapmanıza olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="2b729-107">A convention allows you to:</span></span>

* <span data-ttu-id="2b729-108">Belirli bir eylem türünden döndürülen en yaygın dönüş türlerini ve durum kodlarını tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="2b729-108">Define the most common return types and status codes returned from a specific type of action.</span></span>
* <span data-ttu-id="2b729-109">Tanımlanan standartta saptacak eylemleri belirler.</span><span class="sxs-lookup"><span data-stu-id="2b729-109">Identify actions that deviate from the defined standard.</span></span>

<span data-ttu-id="2b729-110">ASP.NET Core MVC 2,2 ve üzeri, içinde <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName>bir dizi varsayılan kural içerir.</span><span class="sxs-lookup"><span data-stu-id="2b729-110">ASP.NET Core MVC 2.2 and later includes a set of default conventions in <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName>.</span></span> <span data-ttu-id="2b729-111">Kurallar ASP.NET Core **API** proje şablonunda belirtilen denetleyiciyi (*ValuesController.cs*) temel alır.</span><span class="sxs-lookup"><span data-stu-id="2b729-111">The conventions are based on the controller (*ValuesController.cs*) provided in the ASP.NET Core **API** project template.</span></span> <span data-ttu-id="2b729-112">Eylemleriniz şablondaki desenleri izledikten sonra varsayılan kuralları kullanarak başarılı olmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="2b729-112">If your actions follow the patterns in the template, you should be successful using the default conventions.</span></span> <span data-ttu-id="2b729-113">Varsayılan kurallar ihtiyaçlarınızı karşılamıyorsa, bkz. [Web API kuralları oluşturma](#create-web-api-conventions).</span><span class="sxs-lookup"><span data-stu-id="2b729-113">If the default conventions don't meet your needs, see [Create web API conventions](#create-web-api-conventions).</span></span>

<span data-ttu-id="2b729-114">Çalışma zamanında, <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> kuralları anlamıştır.</span><span class="sxs-lookup"><span data-stu-id="2b729-114">At runtime, <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> understands conventions.</span></span> <span data-ttu-id="2b729-115">`ApiExplorer`, [Openapı](https://www.openapis.org/) (Swagger olarak da bilinir) belge oluşturucuları ile iletişim kurmak için MVC 'nin soyutlamasıdır.</span><span class="sxs-lookup"><span data-stu-id="2b729-115">`ApiExplorer` is MVC's abstraction to communicate with [OpenAPI](https://www.openapis.org/) (also known as Swagger) document generators.</span></span> <span data-ttu-id="2b729-116">Uygulanan kuraldaki öznitelikler bir eylemle ilişkilendirilir ve eylemin Openapı belgelerine dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="2b729-116">Attributes from the applied convention are associated with an action and are included in the action's OpenAPI documentation.</span></span> <span data-ttu-id="2b729-117">[API Çözümleyicileri](xref:web-api/advanced/analyzers) , kuralları da anlalar.</span><span class="sxs-lookup"><span data-stu-id="2b729-117">[API analyzers](xref:web-api/advanced/analyzers) also understand conventions.</span></span> <span data-ttu-id="2b729-118">Eyleminiz geleneksel değilse (örneğin, uygulanan kural tarafından belgelenmemiş bir durum kodu döndürürse), durum kodunu belgeleyerek bir uyarı görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="2b729-118">If your action is unconventional (for example, it returns a status code that isn't documented by the applied convention), a warning encourages you to document the status code.</span></span>

<span data-ttu-id="2b729-119">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2b729-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="apply-web-api-conventions"></a><span data-ttu-id="2b729-120">Web API 'SI kurallarını Uygula</span><span class="sxs-lookup"><span data-stu-id="2b729-120">Apply web API conventions</span></span>

<span data-ttu-id="2b729-121">Kurallar oluşturma; Her eylem, tam olarak bir kurala göre ilişkilendirilebilir.</span><span class="sxs-lookup"><span data-stu-id="2b729-121">Conventions don't compose; each action may be associated with exactly one convention.</span></span> <span data-ttu-id="2b729-122">Daha belirgin kuralların daha az belirli kurallara göre daha fazla olması.</span><span class="sxs-lookup"><span data-stu-id="2b729-122">More specific conventions take precedence over less specific conventions.</span></span> <span data-ttu-id="2b729-123">Aynı önceliğe sahip iki veya daha fazla kural bir eyleme uygulandığınızda seçim belirleyici değildir.</span><span class="sxs-lookup"><span data-stu-id="2b729-123">The selection is non-deterministic when two or more conventions of the same priority apply to an action.</span></span> <span data-ttu-id="2b729-124">Aşağıdaki seçenekler, en çok belirli olan en az belirli bir eyleme bir kural uygulamak için mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="2b729-124">The following options exist to apply a convention to an action, from the most specific to the least specific:</span></span>

1. <span data-ttu-id="2b729-125">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute`&mdash; Tek tek eylemler için geçerlidir ve uygulanan kural türünü ve kural yöntemini belirtir.</span><span class="sxs-lookup"><span data-stu-id="2b729-125">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; Applies to individual actions and specifies the convention type and the convention method that applies.</span></span>

    <span data-ttu-id="2b729-126">Aşağıdaki örnekte, varsayılan kural türünün `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` kural yöntemi `Update` eyleme uygulanır:</span><span class="sxs-lookup"><span data-stu-id="2b729-126">In the following example, the default convention type's `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` convention method is applied to the `Update` action:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionMethod&highlight=3)]

    <span data-ttu-id="2b729-127">`Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` Kural yöntemi eyleme aşağıdaki öznitelikleri uygular:</span><span class="sxs-lookup"><span data-stu-id="2b729-127">The `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` convention method applies the following attributes to the action:</span></span>

    ```csharp
    [ProducesDefaultResponseType]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    ```

    <span data-ttu-id="2b729-128">Hakkında `[ProducesDefaultResponseType]`daha fazla bilgi için bkz. [Varsayılan Yanıt](https://swagger.io/docs/specification/describing-responses/#default).</span><span class="sxs-lookup"><span data-stu-id="2b729-128">For more information on `[ProducesDefaultResponseType]`, see [Default Response](https://swagger.io/docs/specification/describing-responses/#default).</span></span>

1. <span data-ttu-id="2b729-129">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute`bir denetleyiciye &mdash; uygulanan, belirtilen kural türünü denetleyicideki tüm eylemlere uygular.</span><span class="sxs-lookup"><span data-stu-id="2b729-129">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to a controller &mdash; Applies the specified convention type to all actions on the controller.</span></span> <span data-ttu-id="2b729-130">Kural yöntemi, kural yönteminin uygulandığı eylemleri belirleyen ipuçlarıyla işaretlenir.</span><span class="sxs-lookup"><span data-stu-id="2b729-130">A convention method is marked with hints that determine the actions to which the convention method applies.</span></span> <span data-ttu-id="2b729-131">İpuçları hakkında daha fazla bilgi için bkz. [Web API kuralları oluşturma](#create-web-api-conventions)).</span><span class="sxs-lookup"><span data-stu-id="2b729-131">For more information on hints, see [Create web API conventions](#create-web-api-conventions)).</span></span>

    <span data-ttu-id="2b729-132">Aşağıdaki örnekte, varsayılan kurallar kümesi *ContactsConventionController*içindeki tüm eylemlere uygulanır:</span><span class="sxs-lookup"><span data-stu-id="2b729-132">In the following example, the default set of conventions is applied to all actions in *ContactsConventionController*:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionTypeAttribute&highlight=2)]

1. <span data-ttu-id="2b729-133">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute`bir derlemeye &mdash; uygulanan, belirtilen kural türünü geçerli derlemedeki tüm denetleyicilere uygular.</span><span class="sxs-lookup"><span data-stu-id="2b729-133">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to an assembly &mdash; Applies the specified convention type to all controllers in the current assembly.</span></span> <span data-ttu-id="2b729-134">Öneri olarak, *Startup.cs* dosyasına derleme düzeyi öznitelikleri uygulayın.</span><span class="sxs-lookup"><span data-stu-id="2b729-134">As a recommendation, apply assembly-level attributes in the *Startup.cs* file.</span></span>

    <span data-ttu-id="2b729-135">Aşağıdaki örnekte, varsayılan kural kümesi derlemedeki tüm denetleyicilere uygulanır:</span><span class="sxs-lookup"><span data-stu-id="2b729-135">In the following example, the default set of conventions is applied to all controllers in the assembly:</span></span>

    [!code-csharp[](conventions/sample/Startup.cs?name=snippet_ApiConventionTypeAttribute&highlight=1)]

## <a name="create-web-api-conventions"></a><span data-ttu-id="2b729-136">Web API kuralları oluşturma</span><span class="sxs-lookup"><span data-stu-id="2b729-136">Create web API conventions</span></span>

<span data-ttu-id="2b729-137">Varsayılan API kuralları gereksinimlerinizi karşılamıyorsa, kendi kurallarınızı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2b729-137">If the default API conventions don't meet your needs, create your own conventions.</span></span> <span data-ttu-id="2b729-138">Bir kural:</span><span class="sxs-lookup"><span data-stu-id="2b729-138">A convention is:</span></span>

* <span data-ttu-id="2b729-139">Metotları olan statik bir tür.</span><span class="sxs-lookup"><span data-stu-id="2b729-139">A static type with methods.</span></span>
* <span data-ttu-id="2b729-140">Eylemlerde [Yanıt türleri](#response-types) ve [adlandırma gereksinimleri](#naming-requirements) tanımlama özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2b729-140">Capable of defining [response types](#response-types) and [naming requirements](#naming-requirements) on actions.</span></span>

### <a name="response-types"></a><span data-ttu-id="2b729-141">Yanıt türleri</span><span class="sxs-lookup"><span data-stu-id="2b729-141">Response types</span></span>

<span data-ttu-id="2b729-142">Bu yöntemlere `[ProducesResponseType]` veya `[ProducesDefaultResponseType]` öznitelikleriyle açıklama eklenir.</span><span class="sxs-lookup"><span data-stu-id="2b729-142">These methods are annotated with `[ProducesResponseType]` or `[ProducesDefaultResponseType]` attributes.</span></span> <span data-ttu-id="2b729-143">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2b729-143">For example:</span></span>

```csharp
public static class MyAppConventions
{
    [ProducesResponseType(StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public static void Find(int id)
    {
    }
}
```

<span data-ttu-id="2b729-144">Daha özel meta veri öznitelikleri yoksa, bu kuralı bir derlemeye uygulamak şunları uygular:</span><span class="sxs-lookup"><span data-stu-id="2b729-144">If more specific metadata attributes are absent, applying this convention to an assembly enforces that:</span></span>

* <span data-ttu-id="2b729-145">Kural yöntemi adlı `Find`tüm eylemler için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="2b729-145">The convention method applies to any action named `Find`.</span></span>
* <span data-ttu-id="2b729-146">`Find` Eylemde adlı `id` bir parametre bulunur.</span><span class="sxs-lookup"><span data-stu-id="2b729-146">A parameter named `id` is present on the `Find` action.</span></span>

### <a name="naming-requirements"></a><span data-ttu-id="2b729-147">Adlandırma gereksinimleri</span><span class="sxs-lookup"><span data-stu-id="2b729-147">Naming requirements</span></span>

<span data-ttu-id="2b729-148">Ve `[ApiConventionNameMatch]` `[ApiConventionTypeMatch]` öznitelikleri, uygulandıkları eylemleri belirleyen kural yöntemine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="2b729-148">The `[ApiConventionNameMatch]` and `[ApiConventionTypeMatch]` attributes can be applied to the convention method that determines the actions to which they apply.</span></span> <span data-ttu-id="2b729-149">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2b729-149">For example:</span></span>

```csharp
[ProducesResponseType(StatusCodes.Status200OK)]
[ProducesResponseType(StatusCodes.Status404NotFound)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

<span data-ttu-id="2b729-150">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="2b729-150">In the preceding example:</span></span>

* <span data-ttu-id="2b729-151">Yöntemine `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` uygulanan seçenek, kuralının "Find" önekini ön eki eklenmiş herhangi bir eylemle eşleştirdiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="2b729-151">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` option applied to the method indicates that the convention matches any action prefixed with "Find".</span></span> <span data-ttu-id="2b729-152">Eşleşen eylemlere örnek `Find`olarak, `FindPet`, ve `FindById`verilebilir.</span><span class="sxs-lookup"><span data-stu-id="2b729-152">Examples of matching actions include `Find`, `FindPet`, and `FindById`.</span></span>
* <span data-ttu-id="2b729-153">Parametresine `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` uygulanan, yöntemin sonek tanımlayıcıda biten tek bir parametreye sahip yöntemlerle eşleştiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="2b729-153">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` applied to the parameter indicates that the convention matches methods with exactly one parameter ending in the suffix identifier.</span></span> <span data-ttu-id="2b729-154">Örnekler, `id` veya `petId`gibi parametreleri içerir.</span><span class="sxs-lookup"><span data-stu-id="2b729-154">Examples include parameters such as `id` or `petId`.</span></span> <span data-ttu-id="2b729-155">`ApiConventionTypeMatch`benzer şekilde, parametre türünü kısıtlamak için türlere uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="2b729-155">`ApiConventionTypeMatch` can be similarly applied to types to constrain the parameter type.</span></span> <span data-ttu-id="2b729-156">`params[]` Bağımsız değişken, açıkça eşleştirilmesinin gerekli olmadığı kalan parametreleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="2b729-156">A `params[]` argument indicates remaining parameters that don't need to be explicitly matched.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2b729-157">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2b729-157">Additional resources</span></span>

* <xref:web-api/advanced/analyzers>
* <xref:tutorials/web-api-help-pages-using-swagger>
