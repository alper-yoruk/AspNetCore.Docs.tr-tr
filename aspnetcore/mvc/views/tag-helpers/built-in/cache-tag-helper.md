---
title: ASP.NET Core MVC 'de önbellek etiketi Yardımcısı
author: pkellner
description: Önbellek etiketi yardımcısını kullanmayı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: 7d2ff774b7654993e2cd9b126db252f81a3032d3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018773"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a><span data-ttu-id="fa4bc-103">ASP.NET Core MVC 'de önbellek etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="fa4bc-103">Cache Tag Helper in ASP.NET Core MVC</span></span>

<span data-ttu-id="fa4bc-104">By [Peter Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="fa4bc-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="fa4bc-105">Önbellek etiketi Yardımcısı, içeriğini dahili ASP.NET Core önbellek sağlayıcısına önbelleğe alarak ASP.NET Core uygulamanızın performansını iyileştirebilme olanağı sağlar.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-105">The Cache Tag Helper provides the ability to improve the performance of your ASP.NET Core app by caching its content to the internal ASP.NET Core cache provider.</span></span>

<span data-ttu-id="fa4bc-106">Etiket Yardımcıları hakkında genel bilgi için bkz <xref:mvc/views/tag-helpers/intro> ..</span><span class="sxs-lookup"><span data-stu-id="fa4bc-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="fa4bc-107">Aşağıdaki Razor biçimlendirme geçerli tarihi önbelleğe alır:</span><span class="sxs-lookup"><span data-stu-id="fa4bc-107">The following Razor markup caches the current date:</span></span>

```cshtml
<cache>@DateTime.Now</cache>
```

<span data-ttu-id="fa4bc-108">Etiket Yardımcısını içeren sayfanın ilk isteği geçerli tarihi görüntüler.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-108">The first request to the page that contains the Tag Helper displays the current date.</span></span> <span data-ttu-id="fa4bc-109">Önbellek süresi dolana kadar veya önbelleğe alınmış tarih önbellekten çıkarılana kadar ek istekler önbelleğe alınmış değeri gösterir.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-109">Additional requests show the cached value until the cache expires (default 20 minutes) or until the cached date is evicted from the cache.</span></span>

## <a name="cache-tag-helper-attributes"></a><span data-ttu-id="fa4bc-110">Önbellek etiketi yardımcı öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="fa4bc-110">Cache Tag Helper Attributes</span></span>

### <a name="enabled"></a><span data-ttu-id="fa4bc-111">enabled</span><span class="sxs-lookup"><span data-stu-id="fa4bc-111">enabled</span></span>

| <span data-ttu-id="fa4bc-112">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="fa4bc-112">Attribute Type</span></span>  | <span data-ttu-id="fa4bc-113">Örnekler</span><span class="sxs-lookup"><span data-stu-id="fa4bc-113">Examples</span></span>        | <span data-ttu-id="fa4bc-114">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="fa4bc-114">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="fa4bc-115">Boole</span><span class="sxs-lookup"><span data-stu-id="fa4bc-115">Boolean</span></span>         | <span data-ttu-id="fa4bc-116">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="fa4bc-116">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="fa4bc-117">`enabled`Önbellek etiketi Yardımcısı tarafından eklenen içeriğin önbelleğe alınıp alınmayacağını belirler.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-117">`enabled` determines if the content enclosed by the Cache Tag Helper is cached.</span></span> <span data-ttu-id="fa4bc-118">Varsayılan değer: `true`.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-118">The default is `true`.</span></span> <span data-ttu-id="fa4bc-119">Olarak ayarlanırsa `false` , işlenen çıktı önbelleğe **alınmaz** .</span><span class="sxs-lookup"><span data-stu-id="fa4bc-119">If set to `false`, the rendered output is **not** cached.</span></span>

<span data-ttu-id="fa4bc-120">Örnek:</span><span class="sxs-lookup"><span data-stu-id="fa4bc-120">Example:</span></span>

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a><span data-ttu-id="fa4bc-121">süre sonu-açık</span><span class="sxs-lookup"><span data-stu-id="fa4bc-121">expires-on</span></span>

| <span data-ttu-id="fa4bc-122">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="fa4bc-122">Attribute Type</span></span>   | <span data-ttu-id="fa4bc-123">Örnek</span><span class="sxs-lookup"><span data-stu-id="fa4bc-123">Example</span></span>                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

<span data-ttu-id="fa4bc-124">`expires-on`önbelleğe alınmış öğe için mutlak bir sona erme tarihi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-124">`expires-on` sets an absolute expiration date for the cached item.</span></span>

<span data-ttu-id="fa4bc-125">Aşağıdaki örnek, 29 Ocak 2025 ' de 5:02 PM 'e kadar önbellek etiketi Yardımcısı 'nın içeriğini önbelleğe alır:</span><span class="sxs-lookup"><span data-stu-id="fa4bc-125">The following example caches the contents of the Cache Tag Helper until 5:02 PM on January 29, 2025:</span></span>

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a><span data-ttu-id="fa4bc-126">süre sonu-sonra</span><span class="sxs-lookup"><span data-stu-id="fa4bc-126">expires-after</span></span>

| <span data-ttu-id="fa4bc-127">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="fa4bc-127">Attribute Type</span></span> | <span data-ttu-id="fa4bc-128">Örnek</span><span class="sxs-lookup"><span data-stu-id="fa4bc-128">Example</span></span>                      | <span data-ttu-id="fa4bc-129">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="fa4bc-129">Default</span></span>    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | <span data-ttu-id="fa4bc-130">20 dakika</span><span class="sxs-lookup"><span data-stu-id="fa4bc-130">20 minutes</span></span> |

<span data-ttu-id="fa4bc-131">`expires-after`içeriği önbelleğe almak için ilk istek saatinden geçen sürenin uzunluğunu ayarlar.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-131">`expires-after` sets the length of time from the first request time to cache the contents.</span></span>

<span data-ttu-id="fa4bc-132">Örnek:</span><span class="sxs-lookup"><span data-stu-id="fa4bc-132">Example:</span></span>

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="fa4bc-133">RazorGörünüm altyapısı varsayılan `expires-after` değeri yirmi dakika olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-133">The Razor View Engine sets the default `expires-after` value to twenty minutes.</span></span>

### <a name="expires-sliding"></a><span data-ttu-id="fa4bc-134">süre sonu-kayan</span><span class="sxs-lookup"><span data-stu-id="fa4bc-134">expires-sliding</span></span>

| <span data-ttu-id="fa4bc-135">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="fa4bc-135">Attribute Type</span></span> | <span data-ttu-id="fa4bc-136">Örnek</span><span class="sxs-lookup"><span data-stu-id="fa4bc-136">Example</span></span>                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

<span data-ttu-id="fa4bc-137">Değerine erişilmediyse önbellek girişinin çıkarılme süresini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-137">Sets the time that a cache entry should be evicted if its value hasn't been accessed.</span></span>

<span data-ttu-id="fa4bc-138">Örnek:</span><span class="sxs-lookup"><span data-stu-id="fa4bc-138">Example:</span></span>

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a><span data-ttu-id="fa4bc-139">üst bilgiye göre değişiklik</span><span class="sxs-lookup"><span data-stu-id="fa4bc-139">vary-by-header</span></span>

| <span data-ttu-id="fa4bc-140">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="fa4bc-140">Attribute Type</span></span> | <span data-ttu-id="fa4bc-141">Örnekler</span><span class="sxs-lookup"><span data-stu-id="fa4bc-141">Examples</span></span>                                    |
| -------------- | ------------------------------------------- |
| <span data-ttu-id="fa4bc-142">Dize</span><span class="sxs-lookup"><span data-stu-id="fa4bc-142">String</span></span>         | <span data-ttu-id="fa4bc-143">`User-Agent`, `User-Agent,content-encoding`</span><span class="sxs-lookup"><span data-stu-id="fa4bc-143">`User-Agent`, `User-Agent,content-encoding`</span></span> |

<span data-ttu-id="fa4bc-144">`vary-by-header`değişiklik yaparken önbellek yenilemeyi tetikleyen üst bilgi değerlerinin virgülle ayrılmış listesini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-144">`vary-by-header` accepts a comma-delimited list of header values that trigger a cache refresh when they change.</span></span>

<span data-ttu-id="fa4bc-145">Aşağıdaki örnek, üst bilgi değerini izler `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="fa4bc-145">The following example monitors the header value `User-Agent`.</span></span> <span data-ttu-id="fa4bc-146">Örnek, Web sunucusuna sunulan her farklı için içeriği önbelleğe alır `User-Agent` :</span><span class="sxs-lookup"><span data-stu-id="fa4bc-146">The example caches the content for every different `User-Agent` presented to the web server:</span></span>

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a><span data-ttu-id="fa4bc-147">sorguya göre değişiklik</span><span class="sxs-lookup"><span data-stu-id="fa4bc-147">vary-by-query</span></span>

| <span data-ttu-id="fa4bc-148">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="fa4bc-148">Attribute Type</span></span> | <span data-ttu-id="fa4bc-149">Örnekler</span><span class="sxs-lookup"><span data-stu-id="fa4bc-149">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="fa4bc-150">Dize</span><span class="sxs-lookup"><span data-stu-id="fa4bc-150">String</span></span>         | <span data-ttu-id="fa4bc-151">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="fa4bc-151">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="fa4bc-152">`vary-by-query`<xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> <xref:Microsoft.AspNetCore.Http.HttpRequest.Query*> listelenen herhangi bir anahtarın değeri değiştiğinde önbellek yenilemeyi tetikleyen bir sorgu dizesinde (), virgülle ayrılmış bir listesini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-152">`vary-by-query` accepts a comma-delimited list of <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> in a query string (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) that trigger a cache refresh when the value of any listed key changes.</span></span>

<span data-ttu-id="fa4bc-153">Aşağıdaki örnek, ve değerlerini izler `Make` `Model` .</span><span class="sxs-lookup"><span data-stu-id="fa4bc-153">The following example monitors the values of `Make` and `Model`.</span></span> <span data-ttu-id="fa4bc-154">Örnek, içeriği her farklı `Make` ve Web sunucusuna sunulan her bir için önbelleğe alır `Model` :</span><span class="sxs-lookup"><span data-stu-id="fa4bc-154">The example caches the content for every different `Make` and `Model` presented to the web server:</span></span>

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a><span data-ttu-id="fa4bc-155">Yönlendirme ölçütü</span><span class="sxs-lookup"><span data-stu-id="fa4bc-155">vary-by-route</span></span>

| <span data-ttu-id="fa4bc-156">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="fa4bc-156">Attribute Type</span></span> | <span data-ttu-id="fa4bc-157">Örnekler</span><span class="sxs-lookup"><span data-stu-id="fa4bc-157">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="fa4bc-158">Dize</span><span class="sxs-lookup"><span data-stu-id="fa4bc-158">String</span></span>         | <span data-ttu-id="fa4bc-159">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="fa4bc-159">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="fa4bc-160">`vary-by-route`yol verileri parametre değeri değiştiğinde önbellek yenilemesi tetikleyen bir virgülle ayrılmış yol parametresi listesini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-160">`vary-by-route` accepts a comma-delimited list of route parameter names that trigger a cache refresh when the route data parameter value changes.</span></span>

<span data-ttu-id="fa4bc-161">Örnek:</span><span class="sxs-lookup"><span data-stu-id="fa4bc-161">Example:</span></span>

<span data-ttu-id="fa4bc-162">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="fa4bc-162">*Startup.cs*:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

<span data-ttu-id="fa4bc-163">*Index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="fa4bc-163">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-no-loccookie"></a><span data-ttu-id="fa4bc-164">değişiklik ölçütü-cookie</span><span class="sxs-lookup"><span data-stu-id="fa4bc-164">vary-by-cookie</span></span>

| <span data-ttu-id="fa4bc-165">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="fa4bc-165">Attribute Type</span></span> | <span data-ttu-id="fa4bc-166">Örnekler</span><span class="sxs-lookup"><span data-stu-id="fa4bc-166">Examples</span></span>                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| <span data-ttu-id="fa4bc-167">Dize</span><span class="sxs-lookup"><span data-stu-id="fa4bc-167">String</span></span>         | <span data-ttu-id="fa4bc-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span><span class="sxs-lookup"><span data-stu-id="fa4bc-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span></span> |

<span data-ttu-id="fa4bc-169">`vary-by-cookie`cookiedeğerler değiştiğinde önbellek yenilemeyi tetikleyen adların virgülle ayrılmış bir listesini kabul eder cookie .</span><span class="sxs-lookup"><span data-stu-id="fa4bc-169">`vary-by-cookie` accepts a comma-delimited list of cookie names that trigger a cache refresh when the cookie values change.</span></span>

<span data-ttu-id="fa4bc-170">Aşağıdaki örnek ASP.NET Core ilişkili ' i izler cookie Identity .</span><span class="sxs-lookup"><span data-stu-id="fa4bc-170">The following example monitors the cookie associated with ASP.NET Core Identity.</span></span> <span data-ttu-id="fa4bc-171">Bir kullanıcının kimliği doğrulandığında, içindeki bir değişiklik Identity cookie bir önbellek yenilemesi tetikler:</span><span class="sxs-lookup"><span data-stu-id="fa4bc-171">When a user is authenticated, a change in the Identity cookie triggers a cache refresh:</span></span>

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a><span data-ttu-id="fa4bc-172">kullanıcıya göre değişiklik</span><span class="sxs-lookup"><span data-stu-id="fa4bc-172">vary-by-user</span></span>

| <span data-ttu-id="fa4bc-173">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="fa4bc-173">Attribute Type</span></span>  | <span data-ttu-id="fa4bc-174">Örnekler</span><span class="sxs-lookup"><span data-stu-id="fa4bc-174">Examples</span></span>        | <span data-ttu-id="fa4bc-175">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="fa4bc-175">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="fa4bc-176">Boole</span><span class="sxs-lookup"><span data-stu-id="fa4bc-176">Boolean</span></span>         | <span data-ttu-id="fa4bc-177">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="fa4bc-177">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="fa4bc-178">`vary-by-user`oturum açan kullanıcı (veya bağlam sorumlusu) değiştiğinde önbelleğin sıfırlanıp sıfırlanmayacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-178">`vary-by-user` specifies whether or not the cache resets when the signed-in user (or Context Principal) changes.</span></span> <span data-ttu-id="fa4bc-179">Geçerli Kullanıcı, Istek bağlamı sorumlusu olarak da bilinir ve Razor başvuru yaparak bir görünümde görüntülenebilir `@User.Identity.Name` .</span><span class="sxs-lookup"><span data-stu-id="fa4bc-179">The current user is also known as the Request Context Principal and can be viewed in a Razor view by referencing `@User.Identity.Name`.</span></span>

<span data-ttu-id="fa4bc-180">Aşağıdaki örnek, bir önbellek yenilemeyi tetiklemek için geçerli oturum açan kullanıcıyı izler:</span><span class="sxs-lookup"><span data-stu-id="fa4bc-180">The following example monitors the current logged in user to trigger a cache refresh:</span></span>

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="fa4bc-181">Bu özniteliğin kullanılması, oturum açma ve oturum kapatma döngüsüyle önbellekteki içerikleri saklar.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-181">Using this attribute maintains the contents in cache through a sign-in and sign-out cycle.</span></span> <span data-ttu-id="fa4bc-182">Değer olarak ayarlandığında `true` , bir kimlik doğrulama çevrimi kimliği doğrulanmış kullanıcı için önbelleği geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-182">When the value is set to `true`, an authentication cycle invalidates the cache for the authenticated user.</span></span> <span data-ttu-id="fa4bc-183">cookieBir kullanıcının kimliği doğrulandığında yeni bir benzersiz değer oluşturulduğundan önbellek geçersiz kılındı.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-183">The cache is invalidated because a new unique cookie value is generated when a user is authenticated.</span></span> <span data-ttu-id="fa4bc-184">No yoksa cookie veya süresi dolduğunda önbellek, anonim durum için korunur cookie .</span><span class="sxs-lookup"><span data-stu-id="fa4bc-184">Cache is maintained for the anonymous state when no cookie is present or the cookie has expired.</span></span> <span data-ttu-id="fa4bc-185">Kullanıcının kimliği **doğrulanmıyorsa** , önbellek korunur.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-185">If the user is **not** authenticated, the cache is maintained.</span></span>

### <a name="vary-by"></a><span data-ttu-id="fa4bc-186">değişiklik ölçütü-</span><span class="sxs-lookup"><span data-stu-id="fa4bc-186">vary-by</span></span>

| <span data-ttu-id="fa4bc-187">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="fa4bc-187">Attribute Type</span></span> | <span data-ttu-id="fa4bc-188">Örnek</span><span class="sxs-lookup"><span data-stu-id="fa4bc-188">Example</span></span>  |
| -------------- | -------- |
| <span data-ttu-id="fa4bc-189">Dize</span><span class="sxs-lookup"><span data-stu-id="fa4bc-189">String</span></span>         | `@Model` |

<span data-ttu-id="fa4bc-190">`vary-by`önbelleğe alınan verilerin özelleştirilmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-190">`vary-by` allows for customization of what data is cached.</span></span> <span data-ttu-id="fa4bc-191">Özniteliğin dize değeri tarafından başvurulan nesne değiştiğinde, önbellek etiketi Yardımcısı 'nın içeriği güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-191">When the object referenced by the attribute's string value changes, the content of the Cache Tag Helper is updated.</span></span> <span data-ttu-id="fa4bc-192">Genellikle, model değerlerinin dize birleştirmesi bu özniteliğe atanır.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-192">Often, a string-concatenation of model values are assigned to this attribute.</span></span> <span data-ttu-id="fa4bc-193">Etkin olarak, bu, herhangi bir birleştirilmiş değerden bir güncelleştirmenin önbelleği geçersiz hale getirildiği bir senaryoya neden olur.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-193">Effectively, this results in a scenario where an update to any of the concatenated values invalidates the cache.</span></span>

<span data-ttu-id="fa4bc-194">Aşağıdaki örnek, görünümü işleyen denetleyici yönteminin, iki yol parametrelerinin tamsayı değerini toplamasını ve ve `myParam1` `myParam2` toplamı tek model özelliği olarak döndürdüğünü varsayar.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-194">The following example assumes the controller method rendering the view sums the integer value of the two route parameters, `myParam1` and `myParam2`, and returns the sum as the single model property.</span></span> <span data-ttu-id="fa4bc-195">Bu toplam değiştiğinde, önbellek etiketi Yardımcısı 'nın içeriği işlenir ve yeniden önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-195">When this sum changes, the content of the Cache Tag Helper is rendered and cached again.</span></span>  

<span data-ttu-id="fa4bc-196">Eylem:</span><span class="sxs-lookup"><span data-stu-id="fa4bc-196">Action:</span></span>

```csharp
public IActionResult Index(string myParam1, string myParam2, string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

<span data-ttu-id="fa4bc-197">*Index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="fa4bc-197">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a><span data-ttu-id="fa4bc-198">Priority</span><span class="sxs-lookup"><span data-stu-id="fa4bc-198">priority</span></span>

| <span data-ttu-id="fa4bc-199">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="fa4bc-199">Attribute Type</span></span>      | <span data-ttu-id="fa4bc-200">Örnekler</span><span class="sxs-lookup"><span data-stu-id="fa4bc-200">Examples</span></span>                               | <span data-ttu-id="fa4bc-201">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="fa4bc-201">Default</span></span>  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | <span data-ttu-id="fa4bc-202">`High`, `Low`, `NeverRemove`, `Normal`</span><span class="sxs-lookup"><span data-stu-id="fa4bc-202">`High`, `Low`, `NeverRemove`, `Normal`</span></span> | `Normal` |

<span data-ttu-id="fa4bc-203">`priority`yerleşik önbellek sağlayıcısına önbellek çıkarma kılavuzu sağlar.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-203">`priority` provides cache eviction guidance to the built-in cache provider.</span></span> <span data-ttu-id="fa4bc-204">Web sunucusu, `Low` bellek baskısı altında olduğunda ön belleği önbelleğe alabilir.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-204">The web server evicts `Low` cache entries first when it's under memory pressure.</span></span>

<span data-ttu-id="fa4bc-205">Örnek:</span><span class="sxs-lookup"><span data-stu-id="fa4bc-205">Example:</span></span>

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="fa4bc-206">`priority`Özniteliği belirli bir önbellek saklama düzeyini garanti etmez.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-206">The `priority` attribute doesn't guarantee a specific level of cache retention.</span></span> <span data-ttu-id="fa4bc-207">`CacheItemPriority`yalnızca bir öneridir.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-207">`CacheItemPriority` is only a suggestion.</span></span> <span data-ttu-id="fa4bc-208">Bu özniteliğin olarak ayarlanması `NeverRemove` , önbelleğe alınmış öğelerin her zaman korunmamasını garanti etmez.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-208">Setting this attribute to `NeverRemove` doesn't guarantee that cached items are always retained.</span></span> <span data-ttu-id="fa4bc-209">Daha fazla bilgi için [ek kaynaklar](#additional-resources) bölümündeki konulara bakın.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-209">See the topics in the [Additional Resources](#additional-resources) section for more information.</span></span>

<span data-ttu-id="fa4bc-210">Önbellek etiketi Yardımcısı, [bellek önbelleği hizmetine](xref:performance/caching/memory)bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-210">The Cache Tag Helper is dependent on the [memory cache service](xref:performance/caching/memory).</span></span> <span data-ttu-id="fa4bc-211">Önbellek etiketi Yardımcısı eklenmemişse hizmeti ekler.</span><span class="sxs-lookup"><span data-stu-id="fa4bc-211">The Cache Tag Helper adds the service if it hasn't been added.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fa4bc-212">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="fa4bc-212">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
