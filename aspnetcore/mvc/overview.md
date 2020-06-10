---
title: ASP.NET Core MVC’ye Genel Bakış
author: ardalis
description: ASP.NET Core MVC 'nin, Model-View-Controller tasarım modelini kullanarak Web uygulamaları ve API 'Ler oluşturmak için zengin bir çatı olduğunu öğrenin.
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/overview
ms.openlocfilehash: ae382feb152f490e46df969887401060965d8c4e
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106539"
---
# <a name="overview-of-aspnet-core-mvc"></a>ASP.NET Core MVC’ye Genel Bakış

[Steve Smith](https://ardalis.com/) tarafından

ASP.NET Core MVC, Model-View-Controller tasarım modelini kullanarak Web uygulamaları ve API 'Ler oluşturmaya yönelik zengin bir çerçevedir.

## <a name="what-is-the-mvc-pattern"></a>MVC deseninin anlamı nedir?

Model-View-Controller (MVC) mimari modeli, bir uygulamayı üç ana bileşen grubuna ayırır: modeller, görünümler ve denetleyiciler. Bu model [, kaygıları ayrımı](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)elde etmeye yardımcı olur. Bu modeli kullanarak Kullanıcı istekleri, kullanıcı eylemlerini gerçekleştirmek ve/veya sorguların sonuçlarını almak için modeliyle çalışmaktan sorumlu bir denetleyiciye yönlendirilir. Denetleyici, kullanıcıya görüntülenecek görünümü seçer ve gereken model verilerini sağlar.

Aşağıdaki diyagramda üç ana bileşen gösterilmektedir ve bunlar diğerlerine başvuramazlar:

![MVC kalıbı](overview/_static/mvc.png)

Bu sorumlulukların bu şekilde çıkarılması, tek bir işi olan bir şeyi (model, görünüm veya denetleyici) kodlanmasını, hata ayıklamayı ve test etmek daha kolay olduğundan, uygulamayı karmaşıklık bakımından ölçeklendirmenize yardımcı olur. Bu üç alandan oluşan iki veya daha fazla bağımlılığı kapsayan bağımlılıklara sahip kodu güncelleştirmek, test etmek ve hata ayıklamak daha zordur. Örneğin, Kullanıcı arabirimi mantığı iş mantığından daha sık değişmeyi eğilimi gösterir. Sunum kodu ve iş mantığı tek bir nesnede birleştirilirse, Kullanıcı arabirimi her değiştirildiğinde iş mantığı içeren bir nesne değiştirilmelidir. Bu genellikle hataları tanıtır ve her bir en az kullanıcı arabirimi değişikliğinden sonra iş mantığının yeniden test edilmesini gerektirir.

> [!NOTE]
> Hem görünüm hem de denetleyici modele bağlıdır. Ancak, model ne görünüm ne de yoksa denetleyiciye bağlıdır. Bu, ayrımı önemli avantajlarından biridir. Bu ayrım, modelin görsel sunudan bağımsız olarak oluşturulup test etmesine olanak tanır.

### <a name="model-responsibilities"></a>Model sorumlulukları

MVC uygulamasındaki model, uygulamanın durumunu ve bunun gerçekleştirilmesi gereken tüm iş mantığını veya işlemlerini temsil eder. İş mantığı, uygulamanın durumunu kalıcı hale getirme için herhangi bir uygulama mantığıyla birlikte, modelde kapsüllenmelidir. Türü kesin belirlenmiş görünümler genellikle bu görünümde görüntülenecek verileri içerecek şekilde tasarlanan ViewModel türlerini kullanır. Denetleyici bu ViewModel örneklerini modelden oluşturur ve doldurur.

### <a name="view-responsibilities"></a>Sorumlulukları görüntüle

Görünümler, kullanıcı arabiriminden içerik sunmadan sorumludur. HTML biçimlendirmesinde .NET kodu eklemek için [ Razor Görünüm altyapısını](#razor-view-engine) kullanır. Görünümler içinde en az mantık olmalıdır ve içerdikleri tüm mantığın içerik sunumu ile ilişkilendirilmesi gerekir. Karmaşık bir modelden veri görüntülemek için dosyaları görüntüle bölümünde harika bir mantık kullanımı gereksinimini fark ederseniz, görünümü basitleştirmek için bir [Görünüm bileşeni](views/view-components.md), ViewModel veya görünüm şablonu kullanmayı düşünün.

### <a name="controller-responsibilities"></a>Denetleyici sorumlulukları

Denetleyiciler, kullanıcı etkileşimini işleyen, modeliyle çalışan ve sonunda işlenecek bir görünüm olan bileşenleridir. MVC uygulamasında, görünüm yalnızca bilgileri görüntüler; denetleyici, Kullanıcı girişini ve etkileşimini işler ve yanıtlar. MVC modelinde, denetleyici ilk giriş noktasıdır ve hangi model türlerinin birlikte çalışacağını ve hangi görünümün işleneceğini seçmekten sorumludur (Bu nedenle adı, uygulamanın belirli bir istek için nasıl yanıt verdiğini denetler).

> [!NOTE]
> Denetleyiciler çok fazla sorumluluk tarafından aşırı karmaşık olmamalıdır. Denetleyici mantığının aşırı karmaşık hale gelmesini sağlamak için, denetleyiciden ve etki alanı modeline kadar iş mantığı gönderin.

>[!TIP]
> Denetleyici eylemlerinizin aynı tür eylemleri sıklıkla gerçekleştirmesini fark ederseniz, bu ortak eylemleri [filtrelere](#filters)taşıyın.

## <a name="what-is-aspnet-core-mvc"></a>ASP.NET Core MVC nedir?

ASP.NET Core MVC çerçevesi, ASP.NET Core birlikte kullanılmak üzere en iyi duruma getirilmiş hafif, açık kaynaklı ve yüksek düzeyde bir sunum çerçevesidir.

ASP.NET Core MVC, sorunların temiz bir şekilde ayrılmasını sağlayan dinamik Web siteleri oluşturmak için desen tabanlı bir yol sağlar. Biçimlendirme üzerinde tam denetim elde etmenizi sağlar, TDD kullanımı kolay geliştirmeyi destekler ve en son web standartlarını kullanır.

## <a name="features"></a>Özellikler

ASP.NET Core MVC şunları içerir:

* [Yönlendirme](#routing)
* [Model bağlama](#model-binding)
* [Model doğrulaması](#model-validation)
* [Bağımlılık ekleme](../fundamentals/dependency-injection.md)
* [Filtreler](#filters)
* [Alanlar](#areas)
* [Web API'leri](#web-apis)
* [Test edilebilirlik](#testability)
* [Razoraltyapıyı görüntüle](#razor-view-engine)
* [Türü kesin belirlenmiş görünümler](#strongly-typed-views)
* [Etiket Yardımcıları](#tag-helpers)
* [Bileşenleri görüntüle](#view-components)

### <a name="routing"></a>Yönlendirme

ASP.NET Core MVC, gelişmiş ve aranabilir URL 'Ler içeren uygulamalar oluşturmanıza olanak tanıyan güçlü bir URL eşleme bileşeni olan [ASP.NET Core yönlendirmenin](../fundamentals/routing.md)üzerine kurulmuştur. Bu, uygulamanızın URL adlandırma düzenlerini, Web sunucunuzdaki dosyaların nasıl düzenleneceğine bakılmaksızın, arama motoru iyileştirmesi (SEO) ve bağlantı oluşturma için iyi bir şekilde tanımlamanıza olanak sağlar. Yol değer kısıtlamalarını, Varsayılanları ve isteğe bağlı değerleri destekleyen uygun bir yol şablonu sözdizimi kullanarak rotalarınızı tanımlayabilirsiniz.

*Kural tabanlı yönlendirme* , uygulamanızın kabul ettiği URL biçimlerini ve bu biçimlerin her birinin verilen denetleyicide belirli bir eylem yöntemiyle nasıl eşlendiğini genel olarak tanımlamanızı sağlar. Gelen bir istek alındığında, yönlendirme altyapısı URL 'YI ayrıştırır ve tanımlanan URL biçimlerinden biriyle eşleştirir ve ardından ilişkili denetleyicinin eylem yöntemini çağırır.

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

*Öznitelik yönlendirme* , denetleyicilerinizi ve eylemlerinizi uygulamanızın yollarını tanımlayan özniteliklerle süsleyerek yönlendirme bilgilerini belirtmenizi sağlar. Bu, yol tanımlarınızın ilişkili oldukları denetleyicinin ve eylemin yanına yerleştirildiği anlamına gelir.

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

### <a name="model-binding"></a>Model bağlama

ASP.NET Core MVC [model bağlama](models/model-binding.md) , istemci isteği verilerini (form değerleri, rota verileri, sorgu dizesi PARAMETRELERI, http üstbilgileri) denetleyicinin işleyebileceği nesnelere dönüştürür. Sonuç olarak, denetleyici mantığınızın gelen istek verilerini oluşturma işini yapması gerekmez; Yalnızca Eylem yöntemlerine parametre olarak verileri içerir.

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
```

### <a name="model-validation"></a>Model doğrulaması

ASP.NET Core MVC, model nesneniz veri ek açıklaması doğrulama öznitelikleriyle süsleyerek [doğrulamayı](models/validation.md) destekler. Doğrulama öznitelikleri, değerler sunucuya gönderilmeden önce istemci tarafında ve denetleyici eylemi çağrılmadan önce sunucuda denetlenir.

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

Bir denetleyici eylemi:

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

Çerçeve, istek verilerini hem istemcide hem de sunucuda doğrulamayı işler. Model türlerinde belirtilen doğrulama mantığı, işlenen görünümlere obtrusive ek açıklamaları olarak eklenir ve [jQuery doğrulaması](https://jqueryvalidation.org/)ile tarayıcıda zorlanır.

### <a name="dependency-injection"></a>Bağımlılık ekleme

ASP.NET Core, [bağımlılık ekleme (dı)](../fundamentals/dependency-injection.md)için yerleşik desteğe sahiptir. ASP.NET Core MVC 'de, [denetleyiciler](controllers/dependency-injection.md) oluşturucuları aracılığıyla gerekli hizmetleri talep edebilir ve bu kullanıcıların [Açık bağımlılıklar ilkesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)izleyebilmesine olanak tanır.

Uygulamanız Ayrıca, yönergeyi [görüntüleme dosyalarını kullanarak bağımlılık ekleme](views/dependency-injection.md)işlemini de kullanabilir `@inject` :

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

### <a name="filters"></a>FilTReleri

[Filtreler](controllers/filters.md) , geliştiricilerin özel durum işleme veya yetkilendirme gibi çapraz sorunları yalıtmalarına yardımcı olur. Filtreler, eylem yöntemleri için özel ön ve son işlem dışı mantığı çalıştırmayı etkinleştirir ve belirli bir istek için yürütme işlem hattının içindeki belirli noktalarda çalışacak şekilde yapılandırılabilir. Filtreler, denetleyicilere veya eylemlere öznitelik olarak uygulanabilir (veya küresel olarak çalıştırılabilir). Birçok filtre (gibi `Authorize` ) çerçeveye dahil edilir. `[Authorize]`, MVC yetkilendirme filtrelerini oluşturmak için kullanılan özniteliktir.

```csharp
[Authorize]
public class AccountController : Controller
```

### <a name="areas"></a>Alanlar

[Bölgeler](controllers/areas.md) , büyük BIR ASP.NET Core MVC web uygulamasını daha küçük işlevsel gruplandırmalar halinde bölümlemek için bir yol sağlar. Bir alan, bir uygulamanın içindeki bir MVC yapısıdır. MVC projesinde model, denetleyici ve görünüm gibi mantıksal bileşenler farklı klasörlerde tutulur ve MVC bu bileşenler arasındaki ilişkiyi oluşturmak için adlandırma kurallarını kullanır. Büyük bir uygulama için, uygulamayı işlevlerin ayrı üst düzey alanlarında bölümlemek avantajlı olabilir. Örneğin, kullanıma alma, faturalandırma ve arama gibi birden çok iş birimi içeren bir e-ticaret uygulaması. Bu birimlerin her birinin kendi mantıksal bileşen görünümleri, denetleyicileri ve modelleri vardır.

### <a name="web-apis"></a>Web API'leri

Web siteleri oluşturmak için harika bir platform olmanın yanı sıra, ASP.NET Core MVC, Web API 'Leri oluşturmaya yönelik harika destek içerir. Tarayıcılar ve mobil cihazlar dahil olmak üzere çok çeşitli istemcilere ulaşan hizmetler oluşturabilirsiniz.

Framework, verileri JSON veya XML olarak [biçimlendirmeye](xref:web-api/advanced/formatting) yönelik yerleşik destek ile http içerik anlaşması için destek içerir. Kendi biçimlerinizin desteğini eklemek için [özel](xref:web-api/advanced/custom-formatters) biçimleri yazın.

Hiper medya desteğini etkinleştirmek için bağlantı oluşturma kullanın. Web API 'lerinizin birden çok Web uygulaması arasında paylaşılabilmesi için, [çıkış noktaları arası kaynak paylaşımı (CORS)](https://www.w3.org/TR/cors/) desteğini kolayca etkinleştirin.

### <a name="testability"></a>Test edilebilirlik

Çerçevenin arabirimlerin ve bağımlılık ekleme özelliğinin kullanımı, birim testine uygun hale getirir ve Framework, [tümleştirme testlerini](xref:test/integration-tests) hızlı ve kolay hale getirmek için özellikler (Entity Framework Için bir testhost ve InMemory sağlayıcısı gibi) içerir. [Denetleyici mantığını test etme](controllers/testing.md)hakkında daha fazla bilgi edinin.

### <a name="razor-view-engine"></a>Razoraltyapıyı görüntüle

[MVC görünümlerini ASP.NET Core](views/overview.md) görünümleri oluşturmak için [ Razor Görünüm altyapısını](views/razor.md) kullanın. Razor, katıştırılmış C# kodu kullanarak görünümleri tanımlamaya yönelik kompakt, ifade ve akışkan şablonu biçimlendirme dilidir. Razor, sunucusunda dinamik olarak Web içeriği oluşturmak için kullanılır. Sunucu kodunu istemci tarafı içeriğiyle ve kodla düzgün bir şekilde karıştırabilirsiniz.

```cshtml
<ul>
    @for (int i = 0; i < 5; i++) {
        <li>List item @i</li>
    }
</ul>
```

RazorGörünüm altyapısını kullanarak [düzenler](views/layout.md), [kısmi görünümler](views/partial.md) ve değiştirilebilir bölümler tanımlayabilirsiniz.

### <a name="strongly-typed-views"></a>Türü kesin belirlenmiş görünümler

RazorMVC 'deki görünümler modelinize göre kesin bir şekilde yazılabilir. Denetleyiciler, görünümlerinizin tür denetlemesi ve IntelliSense desteği olmasını sağlayan görünümlere kesin olarak belirlenmiş bir model geçirebilir.

Örneğin, aşağıdaki görünüm türünde bir model işler `IEnumerable<Product>` :

```cshtml
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a>Etiket Yardımcıları

[Etiket Yardımcıları](views/tag-helpers/intro.md) , sunucu tarafı kodunun dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar Razor . Etiket Yardımcıları kullanarak özel Etiketler tanımlayabilir (örneğin, `<environment>` ) veya varolan etiketlerin davranışını değiştirebilirsiniz (örneğin, `<label>` ). Etiket Yardımcıları, öğe adı ve öznitelikleri temelinde belirli öğelere bağlanır. Bunlar, hala HTML düzenlemesi deneyimini korurken sunucu tarafı işlemenin avantajlarını sağlar.

Yaygın görevler için, genel GitHub depolarında ve NuGet paketleri olarak formlar, bağlantılar, yükleme varlıkları ve daha fazlasını ve daha fazlasını oluşturma gibi birçok yerleşik etiket yardımcıları vardır. Etiket Yardımcıları C# dilinde yazılır ve öğe adı, öznitelik adı veya üst etikete göre HTML öğelerini hedefler. Örneğin, yerleşik Linakghelper, eyleminin bir bağlantısını oluşturmak için kullanılabilir `Login` `AccountsController` :

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

, `EnvironmentTagHelper` Geliştirme, hazırlama veya üretim gibi çalışma zamanı ortamına göre görünümlerinizde farklı betikler (örneğin, ham veya küçültülmüş) dahil etmek için kullanılabilir:

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

Etiket Yardımcıları, HTML ve biçimlendirme oluşturmaya yönelik zengin bir IntelliSense ortamı ve HTML kullanımı kolay bir geliştirme deneyimi sağlar Razor . Yerleşik etiket yardımcıların çoğu, var olan HTML öğelerini hedefleyin ve öğesi için sunucu tarafı öznitelikleri sağlar.

### <a name="view-components"></a>Bileşenleri görüntüle

[Görünüm bileşenleri](views/view-components.md) , işleme mantığını paketlemenize ve uygulamanın tamamında yeniden kullanmanıza olanak tanır. Bunlar, [kısmen görünümlere](views/partial.md)benzer ancak ilişkili mantığa benzer.

## <a name="compatibility-version"></a>Uyumluluk sürümü

<xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>Yöntemi, bir uygulamanın ASP.NET Core MVC 2,1 veya sonraki bir sürümde sunulmuş olabilecek davranış değişikliklerinin kabul etmesine veya devre dışı olmasına izin verir.

Daha fazla bilgi için bkz. <xref:mvc/compatibility-version>.

## <a name="additional-resources"></a>Ek kaynaklar

* [ASP.NET Core MVC Için Mysınanan. AspNetCore. Mvc-Floent test Kitaplığı](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): MVC ve Web API uygulamalarını test etmek için akıcı bir arabirim sağlayan, türü kesin belirlenmiş birim testi kitaplığı. (*Microsoft tarafından korunmaz veya desteklenmez.*)
* <xref:blazor/integrate-components>
