---
title: Web API 'SI kurallarını kullanma
author: pranavkm
description: ASP.NET Core Web API kuralları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.2'
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/conventions
ms.openlocfilehash: 0c5ea8ba69e4c6287afce1771ac9cee65bb188a8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052545"
---
# <a name="use-web-api-conventions"></a>Web API 'SI kurallarını kullanma

, [Pranav Krishnamoorthy](https://github.com/pranavkm) ve [Scott Ade](https://github.com/scottaddie) tarafından

ASP.NET Core 2,2 ve üzeri, ortak [API belgelerini](xref:tutorials/web-api-help-pages-using-swagger) ayıklamanızı ve bunu birden çok eyleme, denetleyiciye veya bir derleme içindeki tüm denetleyicilere uygulamanıza yönelik bir yol içerir. Web API kuralları, ile tek tek eylemleri dekorasyon bir yerdir [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) .

Bir kural şunları yapmanıza olanak sağlar:

* Belirli bir eylem türünden döndürülen en yaygın dönüş türlerini ve durum kodlarını tanımlayın.
* Tanımlanan standartta saptacak eylemleri belirler.

ASP.NET Core MVC 2,2 ve üzeri, içinde bir dizi varsayılan kural içerir <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName> . Kurallar ASP.NET Core **API** proje şablonunda belirtilen denetleyiciyi ( *ValuesController.cs* ) temel alır. Eylemleriniz şablondaki desenleri izledikten sonra varsayılan kuralları kullanarak başarılı olmanız gerekir. Varsayılan kurallar ihtiyaçlarınızı karşılamıyorsa, bkz. [Web API kuralları oluşturma](#create-web-api-conventions).

Çalışma zamanında, <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> kuralları anlamıştır. `ApiExplorer` , [Openapı](https://www.openapis.org/) (Swagger olarak da bilinir) belge oluşturucuları ile iletişim kurmak için MVC 'nin soyutlamasıdır. Uygulanan kuraldaki öznitelikler bir eylemle ilişkilendirilir ve eylemin Openapı belgelerine dahil edilir. [API Çözümleyicileri](xref:web-api/advanced/analyzers) , kuralları da anlalar. Eyleminiz geleneksel değilse (örneğin, uygulanan kural tarafından belgelenmemiş bir durum kodu döndürürse), durum kodunu belgeleyerek bir uyarı görürsünüz.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="apply-web-api-conventions"></a>Web API 'SI kurallarını Uygula

Kurallar oluşturma; Her eylem, tam olarak bir kurala göre ilişkilendirilebilir. Daha belirgin kuralların daha az belirli kurallara göre daha fazla olması. Aynı önceliğe sahip iki veya daha fazla kural bir eyleme uygulandığınızda seçim belirleyici değildir. Aşağıdaki seçenekler, en çok belirli olan en az belirli bir eyleme bir kural uygulamak için mevcuttur:

1. `Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute`&mdash;Tek tek eylemler için geçerlidir ve uygulanan kural türünü ve kural yöntemini belirtir.

    Aşağıdaki örnekte, varsayılan kural türünün `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` kural yöntemi `Update` eyleme uygulanır:

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionMethod&highlight=3)]

    `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put`Kural yöntemi eyleme aşağıdaki öznitelikleri uygular:

    ```csharp
    [ProducesDefaultResponseType]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    ```

    Hakkında daha fazla bilgi için `[ProducesDefaultResponseType]` bkz. [Varsayılan Yanıt](https://swagger.io/docs/specification/describing-responses/#default).

1. `Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` bir denetleyiciye uygulanan &mdash; , belirtilen kural türünü denetleyicideki tüm eylemlere uygular. Kural yöntemi, kural yönteminin uygulandığı eylemleri belirleyen ipuçlarıyla işaretlenir. İpuçları hakkında daha fazla bilgi için bkz. [Web API kuralları oluşturma](#create-web-api-conventions)).

    Aşağıdaki örnekte, varsayılan kurallar kümesi *ContactsConventionController* içindeki tüm eylemlere uygulanır:

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionTypeAttribute&highlight=2)]

1. `Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` bir derlemeye uygulanan, &mdash; belirtilen kural türünü geçerli derlemedeki tüm denetleyicilere uygular. Öneri olarak, *Startup.cs* dosyasına derleme düzeyi öznitelikleri uygulayın.

    Aşağıdaki örnekte, varsayılan kural kümesi derlemedeki tüm denetleyicilere uygulanır:

    [!code-csharp[](conventions/sample/Startup.cs?name=snippet_ApiConventionTypeAttribute&highlight=1)]

## <a name="create-web-api-conventions"></a>Web API kuralları oluşturma

Varsayılan API kuralları gereksinimlerinizi karşılamıyorsa, kendi kurallarınızı oluşturun. Bir kural:

* Metotları olan statik bir tür.
* Eylemlerde [Yanıt türleri](#response-types) ve [adlandırma gereksinimleri](#naming-requirements) tanımlama özelliğine sahiptir.

### <a name="response-types"></a>Yanıt türleri

Bu yöntemlere `[ProducesResponseType]` veya öznitelikleriyle açıklama eklenir `[ProducesDefaultResponseType]` . Örneğin:

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

Daha özel meta veri öznitelikleri yoksa, bu kuralı bir derlemeye uygulamak şunları uygular:

* Kural yöntemi adlı tüm eylemler için geçerlidir `Find` .
* Eylemde adlı bir parametre bulunur `id` `Find` .

### <a name="naming-requirements"></a>Adlandırma gereksinimleri

`[ApiConventionNameMatch]`Ve `[ApiConventionTypeMatch]` öznitelikleri, uygulandıkları eylemleri belirleyen kural yöntemine uygulanabilir. Örneğin:

```csharp
[ProducesResponseType(StatusCodes.Status200OK)]
[ProducesResponseType(StatusCodes.Status404NotFound)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

Yukarıdaki örnekte:

* `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix`Yöntemine uygulanan seçenek, kuralının "Find" önekini ön eki eklenmiş herhangi bir eylemle eşleştirdiğini gösterir. Eşleşen eylemlere örnek `Find` olarak, `FindPet` , ve verilebilir `FindById` .
* `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix`Parametresine uygulanan, yöntemin sonek tanımlayıcıda biten tek bir parametreye sahip yöntemlerle eşleştiğini gösterir. Örnekler, veya gibi parametreleri `id` içerir `petId` . `ApiConventionTypeMatch` benzer şekilde, parametre türünü kısıtlamak için türlere uygulanabilir. `params[]`Bağımsız değişken, açıkça eşleştirilmesinin gerekli olmadığı kalan parametreleri gösterir.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:web-api/advanced/analyzers>
* <xref:tutorials/web-api-help-pages-using-swagger>
