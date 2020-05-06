---
title: ASP.NET Core MVC 'de önbellek etiketi Yardımcısı
author: pkellner
description: Önbellek etiketi yardımcısını kullanmayı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: ced10a7b7b221188fdac2a4e3c54f66292110ece
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773948"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a><span data-ttu-id="203a8-103">ASP.NET Core MVC 'de önbellek etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="203a8-103">Cache Tag Helper in ASP.NET Core MVC</span></span>

<span data-ttu-id="203a8-104">By [Peter Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="203a8-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="203a8-105">Önbellek etiketi Yardımcısı, içeriğini dahili ASP.NET Core önbellek sağlayıcısına önbelleğe alarak ASP.NET Core uygulamanızın performansını iyileştirebilme olanağı sağlar.</span><span class="sxs-lookup"><span data-stu-id="203a8-105">The Cache Tag Helper provides the ability to improve the performance of your ASP.NET Core app by caching its content to the internal ASP.NET Core cache provider.</span></span>

<span data-ttu-id="203a8-106">Etiket Yardımcıları hakkında genel bilgi için bkz <xref:mvc/views/tag-helpers/intro>..</span><span class="sxs-lookup"><span data-stu-id="203a8-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="203a8-107">Şu Razor işaretlemesi geçerli tarihi önbelleğe alır:</span><span class="sxs-lookup"><span data-stu-id="203a8-107">The following Razor markup caches the current date:</span></span>

```cshtml
<cache>@DateTime.Now</cache>
```

<span data-ttu-id="203a8-108">Etiket Yardımcısını içeren sayfanın ilk isteği geçerli tarihi görüntüler.</span><span class="sxs-lookup"><span data-stu-id="203a8-108">The first request to the page that contains the Tag Helper displays the current date.</span></span> <span data-ttu-id="203a8-109">Önbellek süresi dolana kadar veya önbelleğe alınmış tarih önbellekten çıkarılana kadar ek istekler önbelleğe alınmış değeri gösterir.</span><span class="sxs-lookup"><span data-stu-id="203a8-109">Additional requests show the cached value until the cache expires (default 20 minutes) or until the cached date is evicted from the cache.</span></span>

## <a name="cache-tag-helper-attributes"></a><span data-ttu-id="203a8-110">Önbellek etiketi yardımcı öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="203a8-110">Cache Tag Helper Attributes</span></span>

### <a name="enabled"></a><span data-ttu-id="203a8-111">enabled</span><span class="sxs-lookup"><span data-stu-id="203a8-111">enabled</span></span>

| <span data-ttu-id="203a8-112">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="203a8-112">Attribute Type</span></span>  | <span data-ttu-id="203a8-113">Örnekler</span><span class="sxs-lookup"><span data-stu-id="203a8-113">Examples</span></span>        | <span data-ttu-id="203a8-114">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="203a8-114">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="203a8-115">Boole</span><span class="sxs-lookup"><span data-stu-id="203a8-115">Boolean</span></span>         | <span data-ttu-id="203a8-116">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="203a8-116">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="203a8-117">`enabled`Önbellek etiketi Yardımcısı tarafından eklenen içeriğin önbelleğe alınıp alınmayacağını belirler.</span><span class="sxs-lookup"><span data-stu-id="203a8-117">`enabled` determines if the content enclosed by the Cache Tag Helper is cached.</span></span> <span data-ttu-id="203a8-118">Varsayılan değer: `true`.</span><span class="sxs-lookup"><span data-stu-id="203a8-118">The default is `true`.</span></span> <span data-ttu-id="203a8-119">Olarak `false`ayarlanırsa, işlenen çıktı önbelleğe **alınmaz** .</span><span class="sxs-lookup"><span data-stu-id="203a8-119">If set to `false`, the rendered output is **not** cached.</span></span>

<span data-ttu-id="203a8-120">Örnek:</span><span class="sxs-lookup"><span data-stu-id="203a8-120">Example:</span></span>

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a><span data-ttu-id="203a8-121">süre sonu-açık</span><span class="sxs-lookup"><span data-stu-id="203a8-121">expires-on</span></span>

| <span data-ttu-id="203a8-122">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="203a8-122">Attribute Type</span></span>   | <span data-ttu-id="203a8-123">Örnek</span><span class="sxs-lookup"><span data-stu-id="203a8-123">Example</span></span>                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

<span data-ttu-id="203a8-124">`expires-on`önbelleğe alınmış öğe için mutlak bir sona erme tarihi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="203a8-124">`expires-on` sets an absolute expiration date for the cached item.</span></span>

<span data-ttu-id="203a8-125">Aşağıdaki örnek, 29 Ocak 2025 ' de 5:02 PM 'e kadar önbellek etiketi Yardımcısı 'nın içeriğini önbelleğe alır:</span><span class="sxs-lookup"><span data-stu-id="203a8-125">The following example caches the contents of the Cache Tag Helper until 5:02 PM on January 29, 2025:</span></span>

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a><span data-ttu-id="203a8-126">süre sonu-sonra</span><span class="sxs-lookup"><span data-stu-id="203a8-126">expires-after</span></span>

| <span data-ttu-id="203a8-127">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="203a8-127">Attribute Type</span></span> | <span data-ttu-id="203a8-128">Örnek</span><span class="sxs-lookup"><span data-stu-id="203a8-128">Example</span></span>                      | <span data-ttu-id="203a8-129">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="203a8-129">Default</span></span>    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | <span data-ttu-id="203a8-130">20 dakika</span><span class="sxs-lookup"><span data-stu-id="203a8-130">20 minutes</span></span> |

<span data-ttu-id="203a8-131">`expires-after`içeriği önbelleğe almak için ilk istek saatinden geçen sürenin uzunluğunu ayarlar.</span><span class="sxs-lookup"><span data-stu-id="203a8-131">`expires-after` sets the length of time from the first request time to cache the contents.</span></span>

<span data-ttu-id="203a8-132">Örnek:</span><span class="sxs-lookup"><span data-stu-id="203a8-132">Example:</span></span>

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="203a8-133">Razor Görünüm altyapısı varsayılan `expires-after` değeri yirmi dakika olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="203a8-133">The Razor View Engine sets the default `expires-after` value to twenty minutes.</span></span>

### <a name="expires-sliding"></a><span data-ttu-id="203a8-134">süre sonu-kayan</span><span class="sxs-lookup"><span data-stu-id="203a8-134">expires-sliding</span></span>

| <span data-ttu-id="203a8-135">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="203a8-135">Attribute Type</span></span> | <span data-ttu-id="203a8-136">Örnek</span><span class="sxs-lookup"><span data-stu-id="203a8-136">Example</span></span>                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

<span data-ttu-id="203a8-137">Değerine erişilmediyse önbellek girişinin çıkarılme süresini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="203a8-137">Sets the time that a cache entry should be evicted if its value hasn't been accessed.</span></span>

<span data-ttu-id="203a8-138">Örnek:</span><span class="sxs-lookup"><span data-stu-id="203a8-138">Example:</span></span>

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a><span data-ttu-id="203a8-139">üst bilgiye göre değişiklik</span><span class="sxs-lookup"><span data-stu-id="203a8-139">vary-by-header</span></span>

| <span data-ttu-id="203a8-140">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="203a8-140">Attribute Type</span></span> | <span data-ttu-id="203a8-141">Örnekler</span><span class="sxs-lookup"><span data-stu-id="203a8-141">Examples</span></span>                                    |
| -------------- | ------------------------------------------- |
| <span data-ttu-id="203a8-142">Dize</span><span class="sxs-lookup"><span data-stu-id="203a8-142">String</span></span>         | <span data-ttu-id="203a8-143">`User-Agent`, `User-Agent,content-encoding`</span><span class="sxs-lookup"><span data-stu-id="203a8-143">`User-Agent`, `User-Agent,content-encoding`</span></span> |

<span data-ttu-id="203a8-144">`vary-by-header`değişiklik yaparken önbellek yenilemeyi tetikleyen üst bilgi değerlerinin virgülle ayrılmış listesini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="203a8-144">`vary-by-header` accepts a comma-delimited list of header values that trigger a cache refresh when they change.</span></span>

<span data-ttu-id="203a8-145">Aşağıdaki örnek, üst bilgi değerini `User-Agent`izler.</span><span class="sxs-lookup"><span data-stu-id="203a8-145">The following example monitors the header value `User-Agent`.</span></span> <span data-ttu-id="203a8-146">Örnek, Web sunucusuna sunulan her farklı `User-Agent` için içeriği önbelleğe alır:</span><span class="sxs-lookup"><span data-stu-id="203a8-146">The example caches the content for every different `User-Agent` presented to the web server:</span></span>

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a><span data-ttu-id="203a8-147">sorguya göre değişiklik</span><span class="sxs-lookup"><span data-stu-id="203a8-147">vary-by-query</span></span>

| <span data-ttu-id="203a8-148">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="203a8-148">Attribute Type</span></span> | <span data-ttu-id="203a8-149">Örnekler</span><span class="sxs-lookup"><span data-stu-id="203a8-149">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="203a8-150">Dize</span><span class="sxs-lookup"><span data-stu-id="203a8-150">String</span></span>         | <span data-ttu-id="203a8-151">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="203a8-151">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="203a8-152">`vary-by-query`listelenen herhangi bir anahtarın değeri değiştiğinde önbellek <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> yenilemeyi tetikleyen bir sorgu dizesinde<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>(), virgülle ayrılmış bir listesini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="203a8-152">`vary-by-query` accepts a comma-delimited list of <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> in a query string (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) that trigger a cache refresh when the value of any listed key changes.</span></span>

<span data-ttu-id="203a8-153">Aşağıdaki örnek, `Make` ve `Model`değerlerini izler.</span><span class="sxs-lookup"><span data-stu-id="203a8-153">The following example monitors the values of `Make` and `Model`.</span></span> <span data-ttu-id="203a8-154">Örnek, içeriği her farklı `Make` ve `Model` Web sunucusuna sunulan her bir için önbelleğe alır:</span><span class="sxs-lookup"><span data-stu-id="203a8-154">The example caches the content for every different `Make` and `Model` presented to the web server:</span></span>

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a><span data-ttu-id="203a8-155">Yönlendirme ölçütü</span><span class="sxs-lookup"><span data-stu-id="203a8-155">vary-by-route</span></span>

| <span data-ttu-id="203a8-156">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="203a8-156">Attribute Type</span></span> | <span data-ttu-id="203a8-157">Örnekler</span><span class="sxs-lookup"><span data-stu-id="203a8-157">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="203a8-158">Dize</span><span class="sxs-lookup"><span data-stu-id="203a8-158">String</span></span>         | <span data-ttu-id="203a8-159">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="203a8-159">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="203a8-160">`vary-by-route`yol verileri parametre değeri değiştiğinde önbellek yenilemesi tetikleyen bir virgülle ayrılmış yol parametresi listesini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="203a8-160">`vary-by-route` accepts a comma-delimited list of route parameter names that trigger a cache refresh when the route data parameter value changes.</span></span>

<span data-ttu-id="203a8-161">Örnek:</span><span class="sxs-lookup"><span data-stu-id="203a8-161">Example:</span></span>

<span data-ttu-id="203a8-162">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="203a8-162">*Startup.cs*:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

<span data-ttu-id="203a8-163">*Index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="203a8-163">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-cookie"></a><span data-ttu-id="203a8-164">tanımlama bilgisine göre farklılık</span><span class="sxs-lookup"><span data-stu-id="203a8-164">vary-by-cookie</span></span>

| <span data-ttu-id="203a8-165">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="203a8-165">Attribute Type</span></span> | <span data-ttu-id="203a8-166">Örnekler</span><span class="sxs-lookup"><span data-stu-id="203a8-166">Examples</span></span>                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| <span data-ttu-id="203a8-167">Dize</span><span class="sxs-lookup"><span data-stu-id="203a8-167">String</span></span>         | <span data-ttu-id="203a8-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span><span class="sxs-lookup"><span data-stu-id="203a8-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span></span> |

<span data-ttu-id="203a8-169">`vary-by-cookie`tanımlama bilgisi değerleri değiştiğinde önbellek yenilemeyi tetikleyen tanımlama bilgisi adlarının virgülle ayrılmış listesini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="203a8-169">`vary-by-cookie` accepts a comma-delimited list of cookie names that trigger a cache refresh when the cookie values change.</span></span>

<span data-ttu-id="203a8-170">Aşağıdaki örnek ASP.NET Core kimlikle ilişkili tanımlama bilgisini izler.</span><span class="sxs-lookup"><span data-stu-id="203a8-170">The following example monitors the cookie associated with ASP.NET Core Identity.</span></span> <span data-ttu-id="203a8-171">Bir kullanıcının kimliği doğrulandığında, kimlik tanımlama bilgisindeki bir değişiklik önbellek yenilemeyi tetikler:</span><span class="sxs-lookup"><span data-stu-id="203a8-171">When a user is authenticated, a change in the Identity cookie triggers a cache refresh:</span></span>

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a><span data-ttu-id="203a8-172">kullanıcıya göre değişiklik</span><span class="sxs-lookup"><span data-stu-id="203a8-172">vary-by-user</span></span>

| <span data-ttu-id="203a8-173">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="203a8-173">Attribute Type</span></span>  | <span data-ttu-id="203a8-174">Örnekler</span><span class="sxs-lookup"><span data-stu-id="203a8-174">Examples</span></span>        | <span data-ttu-id="203a8-175">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="203a8-175">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="203a8-176">Boole</span><span class="sxs-lookup"><span data-stu-id="203a8-176">Boolean</span></span>         | <span data-ttu-id="203a8-177">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="203a8-177">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="203a8-178">`vary-by-user`oturum açan kullanıcı (veya bağlam sorumlusu) değiştiğinde önbelleğin sıfırlanıp sıfırlanmayacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="203a8-178">`vary-by-user` specifies whether or not the cache resets when the signed-in user (or Context Principal) changes.</span></span> <span data-ttu-id="203a8-179">Geçerli Kullanıcı, Istek bağlamı sorumlusu olarak da bilinir ve başvurarak `@User.Identity.Name`Razor görünümünde görüntülenebilir.</span><span class="sxs-lookup"><span data-stu-id="203a8-179">The current user is also known as the Request Context Principal and can be viewed in a Razor view by referencing `@User.Identity.Name`.</span></span>

<span data-ttu-id="203a8-180">Aşağıdaki örnek, bir önbellek yenilemeyi tetiklemek için geçerli oturum açan kullanıcıyı izler:</span><span class="sxs-lookup"><span data-stu-id="203a8-180">The following example monitors the current logged in user to trigger a cache refresh:</span></span>

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="203a8-181">Bu özniteliğin kullanılması, oturum açma ve oturum kapatma döngüsüyle önbellekteki içerikleri saklar.</span><span class="sxs-lookup"><span data-stu-id="203a8-181">Using this attribute maintains the contents in cache through a sign-in and sign-out cycle.</span></span> <span data-ttu-id="203a8-182">Değer olarak `true`ayarlandığında, bir kimlik doğrulama çevrimi kimliği doğrulanmış kullanıcı için önbelleği geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="203a8-182">When the value is set to `true`, an authentication cycle invalidates the cache for the authenticated user.</span></span> <span data-ttu-id="203a8-183">Bir kullanıcının kimliği doğrulandığında yeni bir benzersiz tanımlama bilgisi değeri oluşturulduğundan önbellek geçersiz kılındı.</span><span class="sxs-lookup"><span data-stu-id="203a8-183">The cache is invalidated because a new unique cookie value is generated when a user is authenticated.</span></span> <span data-ttu-id="203a8-184">Bir tanımlama bilgisi yoksa veya tanımlama bilgisinin süresi dolduğunda önbellek, anonim durum için korunur.</span><span class="sxs-lookup"><span data-stu-id="203a8-184">Cache is maintained for the anonymous state when no cookie is present or the cookie has expired.</span></span> <span data-ttu-id="203a8-185">Kullanıcının kimliği **doğrulanmıyorsa** , önbellek korunur.</span><span class="sxs-lookup"><span data-stu-id="203a8-185">If the user is **not** authenticated, the cache is maintained.</span></span>

### <a name="vary-by"></a><span data-ttu-id="203a8-186">değişiklik ölçütü-</span><span class="sxs-lookup"><span data-stu-id="203a8-186">vary-by</span></span>

| <span data-ttu-id="203a8-187">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="203a8-187">Attribute Type</span></span> | <span data-ttu-id="203a8-188">Örnek</span><span class="sxs-lookup"><span data-stu-id="203a8-188">Example</span></span>  |
| -------------- | -------- |
| <span data-ttu-id="203a8-189">Dize</span><span class="sxs-lookup"><span data-stu-id="203a8-189">String</span></span>         | `@Model` |

<span data-ttu-id="203a8-190">`vary-by`önbelleğe alınan verilerin özelleştirilmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="203a8-190">`vary-by` allows for customization of what data is cached.</span></span> <span data-ttu-id="203a8-191">Özniteliğin dize değeri tarafından başvurulan nesne değiştiğinde, önbellek etiketi Yardımcısı 'nın içeriği güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="203a8-191">When the object referenced by the attribute's string value changes, the content of the Cache Tag Helper is updated.</span></span> <span data-ttu-id="203a8-192">Genellikle, model değerlerinin dize birleştirmesi bu özniteliğe atanır.</span><span class="sxs-lookup"><span data-stu-id="203a8-192">Often, a string-concatenation of model values are assigned to this attribute.</span></span> <span data-ttu-id="203a8-193">Etkin olarak, bu, herhangi bir birleştirilmiş değerden bir güncelleştirmenin önbelleği geçersiz hale getirildiği bir senaryoya neden olur.</span><span class="sxs-lookup"><span data-stu-id="203a8-193">Effectively, this results in a scenario where an update to any of the concatenated values invalidates the cache.</span></span>

<span data-ttu-id="203a8-194">Aşağıdaki örnek, görünümü işleyen denetleyici yönteminin, iki yol parametrelerinin `myParam1` `myParam2`tamsayı değerini toplamasını ve ve toplamı tek model özelliği olarak döndürdüğünü varsayar.</span><span class="sxs-lookup"><span data-stu-id="203a8-194">The following example assumes the controller method rendering the view sums the integer value of the two route parameters, `myParam1` and `myParam2`, and returns the sum as the single model property.</span></span> <span data-ttu-id="203a8-195">Bu toplam değiştiğinde, önbellek etiketi Yardımcısı 'nın içeriği işlenir ve yeniden önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="203a8-195">When this sum changes, the content of the Cache Tag Helper is rendered and cached again.</span></span>  

<span data-ttu-id="203a8-196">Eylem:</span><span class="sxs-lookup"><span data-stu-id="203a8-196">Action:</span></span>

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

<span data-ttu-id="203a8-197">*Index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="203a8-197">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a><span data-ttu-id="203a8-198">Priority</span><span class="sxs-lookup"><span data-stu-id="203a8-198">priority</span></span>

| <span data-ttu-id="203a8-199">Öznitelik türü</span><span class="sxs-lookup"><span data-stu-id="203a8-199">Attribute Type</span></span>      | <span data-ttu-id="203a8-200">Örnekler</span><span class="sxs-lookup"><span data-stu-id="203a8-200">Examples</span></span>                               | <span data-ttu-id="203a8-201">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="203a8-201">Default</span></span>  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | <span data-ttu-id="203a8-202">`High`, `Low`, `NeverRemove`, `Normal`</span><span class="sxs-lookup"><span data-stu-id="203a8-202">`High`, `Low`, `NeverRemove`, `Normal`</span></span> | `Normal` |

<span data-ttu-id="203a8-203">`priority`yerleşik önbellek sağlayıcısına önbellek çıkarma kılavuzu sağlar.</span><span class="sxs-lookup"><span data-stu-id="203a8-203">`priority` provides cache eviction guidance to the built-in cache provider.</span></span> <span data-ttu-id="203a8-204">Web sunucusu, bellek baskısı `Low` altında olduğunda ön belleği önbelleğe alabilir.</span><span class="sxs-lookup"><span data-stu-id="203a8-204">The web server evicts `Low` cache entries first when it's under memory pressure.</span></span>

<span data-ttu-id="203a8-205">Örnek:</span><span class="sxs-lookup"><span data-stu-id="203a8-205">Example:</span></span>

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="203a8-206">`priority` Özniteliği belirli bir önbellek saklama düzeyini garanti etmez.</span><span class="sxs-lookup"><span data-stu-id="203a8-206">The `priority` attribute doesn't guarantee a specific level of cache retention.</span></span> <span data-ttu-id="203a8-207">`CacheItemPriority`yalnızca bir öneridir.</span><span class="sxs-lookup"><span data-stu-id="203a8-207">`CacheItemPriority` is only a suggestion.</span></span> <span data-ttu-id="203a8-208">Bu özniteliğin olarak `NeverRemove` ayarlanması, önbelleğe alınmış öğelerin her zaman korunmamasını garanti etmez.</span><span class="sxs-lookup"><span data-stu-id="203a8-208">Setting this attribute to `NeverRemove` doesn't guarantee that cached items are always retained.</span></span> <span data-ttu-id="203a8-209">Daha fazla bilgi için [ek kaynaklar](#additional-resources) bölümündeki konulara bakın.</span><span class="sxs-lookup"><span data-stu-id="203a8-209">See the topics in the [Additional Resources](#additional-resources) section for more information.</span></span>

<span data-ttu-id="203a8-210">Önbellek etiketi Yardımcısı, [bellek önbelleği hizmetine](xref:performance/caching/memory)bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="203a8-210">The Cache Tag Helper is dependent on the [memory cache service](xref:performance/caching/memory).</span></span> <span data-ttu-id="203a8-211">Önbellek etiketi Yardımcısı eklenmemişse hizmeti ekler.</span><span class="sxs-lookup"><span data-stu-id="203a8-211">The Cache Tag Helper adds the service if it hasn't been added.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="203a8-212">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="203a8-212">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
