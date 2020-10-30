---
title: ASP.NET Core MVC 'de denetleyicilerle istekleri işleme
author: ardalis
description: ''
ms.author: riande
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
uid: mvc/controllers/actions
ms.openlocfilehash: a9319e74d0213b178c2a71be69a0332270d9446c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061463"
---
# <a name="handle-requests-with-controllers-in-aspnet-core-mvc"></a>ASP.NET Core MVC 'de denetleyicilerle istekleri işleme

, [Steve Smith](https://ardalis.com/) ve [Scott Ade](https://github.com/scottaddie) tarafından

Denetleyiciler, Eylemler ve eylem sonuçları, geliştiricilerin ASP.NET Core MVC kullanarak uygulama oluşturma konusunda temel bir parçasıdır.

## <a name="what-is-a-controller"></a>Denetleyici nedir?

Bir denetleyici, bir dizi eylemi tanımlamak ve gruplandırmak için kullanılır. Bir eylem (veya *eylem yöntemi* ), bir denetleyicide istekleri işleyen bir yöntemdir. Denetleyiciler benzer eylemleri birlikte mantıksal olarak gruplayın. Bu eylemlerin toplamı, yönlendirme, önbelleğe alma ve yetkilendirme gibi ortak kural kümelerinin toplu olarak uygulanmasını sağlar. İstekler, [yönlendirme](xref:mvc/controllers/routing)aracılığıyla eylemlerle eşleştirilir.

Kurala göre, denetleyici sınıfları:

* Projenin kök düzeyi *denetleyiciler* klasöründe bulunur.
* Öğesinden devralma `Microsoft.AspNetCore.Mvc.Controller` .

Denetleyici, aşağıdaki koşullardan en az birinin doğru olduğu bir instantiable sınıfıdır:

* Sınıf adı ile sondüzeltildi `Controller` .
* Sınıfı, adı sonekli olan bir sınıftan devralır `Controller` .
* `[Controller]`Özniteliği sınıfına uygulanır.

Denetleyici sınıfı ilişkili bir özniteliğe sahip olmamalıdır `[NonController]` .

Denetleyiciler [Açık bağımlılıklar ilkesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)izlemelidir. Bu ilkeyi uygulamak için birkaç yaklaşım vardır. Birden çok denetleyici eylemi aynı hizmeti gerektiriyorsa, bu bağımlılıkları istemek için [Oluşturucu Ekleme](xref:mvc/controllers/dependency-injection#constructor-injection) kullanmayı düşünün. Hizmet yalnızca tek bir eylem yöntemiyle gerekliyse, bağımlılığı istemek için [eylem ekleme işlemini](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) kullanmayı düşünün.

**M** odel- **V** IEW- **C** ontroller düzeninde bir denetleyici, modelin istek ve örneklemesinin ilk işlemeden sorumludur. Genellikle, iş kararları model içinde gerçekleştirilmelidir.

Denetleyici, modelin işleme sonucunu alır (varsa) ve uygun görünümü ve ilgili görünüm verilerini ya da API çağrısının sonucunu döndürür. [ASP.NET Core MVC 'ye genel bakış](xref:mvc/overview) ve [ASP.NET Core MVC ve Visual Studio ile çalışmaya başlama](xref:tutorials/first-mvc-app/start-mvc)hakkında daha fazla bilgi edinin.

Denetleyici bir *UI düzeyi* soyutlamadır. Sorumlulukları, istek verilerinin geçerli olduğundan ve hangi görünümün (ya da bir API 'nin sonucunun) döndürüldüğünden emin sağlamaktır. İyi şekilde uyumlu olmayan uygulamalarda, doğrudan veri erişimi veya iş mantığı dahil değildir. Bunun yerine, denetleyici bu sorumlulukları işleyen hizmetlere temsilci seçer.

## <a name="defining-actions"></a>Eylemleri tanımlama

Bir denetleyicide özniteliği olanlar hariç genel yöntemler `[NonAction]` eylemlerdir. Eylemlerdeki Parametreler istek verilerine bağlıdır ve [model bağlama](xref:mvc/models/model-binding)kullanılarak onaylanır. Model-bağlantılı her şey için model doğrulaması oluşur. `ModelState.IsValid`Özellik değeri, model bağlamanın ve doğrulamanın başarılı olup olmadığını gösterir.

Eylem yöntemleri bir sorunu iş açısından eşlemek için mantık içermelidir. İş kaygıları genellikle denetleyicinin [bağımlılık ekleme](xref:mvc/controllers/dependency-injection)yoluyla eriştiği hizmetler olarak temsil edilmelidir. Eylemler daha sonra iş eyleminin sonucunu bir uygulama durumuna eşler.

Eylemler her şeyi döndürebilir, ancak `IActionResult` `Task<IActionResult>` bir yanıt üreten (ya da zaman uyumsuz metotlar için) bir örneğini döndürür. Eylem yöntemi, *ne tür bir yanıt* seçmekten sorumludur. Eylem sonucu *Yanıt* verir.

### <a name="controller-helper-methods"></a>Denetleyici Yardımcısı yöntemleri

Denetleyiciler genellikle [denetleyiciden](/dotnet/api/microsoft.aspnetcore.mvc.controller)devralınır, ancak bu gerekli değildir. Öğesinden türetme `Controller` , üç yardımcı yöntem kategorisine erişim sağlar:

#### <a name="1-methods-resulting-in-an-empty-response-body"></a>1. Yöntemler boş bir yanıt gövdesine yol açar

`Content-Type`Yanıt gövdesinde betimleyen içerik bulunmadığından http yanıt üst bilgisi dahil değildir.

Bu kategori içinde iki sonuç türü vardır: Redirect ve HTTP durum kodu.

* **HTTP durum kodu**

    Bu tür bir HTTP durum kodu döndürür. Bu türün birkaç yardımcı yöntemi `BadRequest` ,, ve ' dir `NotFound` `Ok` . Örneğin, `return BadRequest();` yürütüldüğünde 400 durum kodu üretir. , Ve gibi yöntemler `BadRequest` `NotFound` aşırı yüklendiğinde, `Ok` içerik anlaşması GERÇEKLEŞDIĞINDEN artık http durum kodu Yanıtlayıcıları olarak niteleyemez.

* **Yeniden yönlendirme**

    Bu tür bir eyleme veya hedefe yeniden yönlendirme döndürür (,, `Redirect` , `LocalRedirect` `RedirectToAction` veya kullanarak `RedirectToRoute` ). Örneğin, `return RedirectToAction("Complete", new {id = 123});` `Complete` bir anonim nesne geçirerek öğesine yönlendirir.

    Yeniden yönlendirme sonuç türü, birincil olarak `Location` http yanıt üst bilgisi ekleme IÇINDEKI http durum kodu türünden farklıdır.

#### <a name="2-methods-resulting-in-a-non-empty-response-body-with-a-predefined-content-type"></a>2. Yöntemler, önceden tanımlanmış bir içerik türüyle boş olmayan bir yanıt gövdesine yol açar

Bu kategorideki çoğu yardımcı yöntem bir özelliği içerir, bu da yanıt `ContentType` `Content-Type` gövdesini tanımlamaya yönelik yanıt üst bilgisini ayarlamanıza olanak sağlar.

Bu kategori içinde iki sonuç türü vardır: [görüntüleme](xref:mvc/views/overview) ve [biçimli yanıt](xref:web-api/advanced/formatting).

* **Görünüm**

    Bu tür, HTML işlemek için bir model kullanan bir görünüm döndürür. Örneğin, `return View(customer);` veri bağlama için bir modeli görünüme geçirir.

* **Biçimlendirilen yanıt**

    Bu tür, bir nesneyi belirli bir şekilde göstermek için JSON veya benzer bir veri değişimi biçimi döndürür. Örneğin, `return Json(customer);` belirtilen NESNEYI JSON biçimine dizleştirir.
    
    Bu türün diğer yaygın yöntemleri ve içerir `File` `PhysicalFile` . Örneğin, `return PhysicalFile(customerFilePath, "text/xml");` [Physicalfileresult](/dotnet/api/microsoft.aspnetcore.mvc.physicalfileresult)döndürür.

#### <a name="3-methods-resulting-in-a-non-empty-response-body-formatted-in-a-content-type-negotiated-with-the-client"></a>3. Yöntemler, istemci ile anlaşan bir içerik türünde biçimlendirilen boş olmayan bir yanıt gövdesinin oluşmasına neden olur

Bu kategori, **Içerik anlaşması** olarak daha iyi bilinir. [İçerik anlaşması](xref:web-api/advanced/formatting#content-negotiation) , bir eylem bir [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult) türü ya da [ıactionresult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult) uygulaması dışında bir şey döndürdüğünde geçerlidir. Uygulama olmayan bir eylem `IActionResult` (örneğin, `object` ), aynı zamanda biçimli bir yanıt döndürür.

Bu türün bazı yardımcı yöntemleri,, `BadRequest` `CreatedAtRoute` ve içerir `Ok` . Bu yöntemlerin örnekleri `return BadRequest(modelState);` sırasıyla,, `return CreatedAtRoute("routename", values, newobject);` ve `return Ok(value);` , içerir. Bu `BadRequest` `Ok` , yalnızca bir değer geçirildiğinde içerik anlaşması gerçekleştirdiğine; bir değer geçirilmeksizin, bunun yerine http durum kodu sonuç türleri olarak işlev görür. `CreatedAtRoute`Diğer yandan yöntemi her zaman içerik anlaşması gerçekleştirir, çünkü aşırı yüklemelerinin hepsi bir değer geçirilmesini gerektirir.

### <a name="cross-cutting-concerns"></a>Çapraz kesme konuları

Uygulamalar genellikle iş akışının parçalarını paylaşır. Örnek olarak, alışveriş sepetine erişmek için kimlik doğrulaması gerektiren bir uygulama veya bazı sayfalarda verileri önbelleğe alan bir uygulama verilebilir. Bir eylem yönteminden önce veya sonra mantık gerçekleştirmek için bir *filtre* kullanın. Çapraz kesme sorunları üzerinde [filtrelerin](xref:mvc/controllers/filters) kullanılması, yinelemeyi azaltabilir.

Gibi filtre özniteliklerinin çoğu, `[Authorize]` istenen ayrıntı düzeyi düzeyine bağlı olarak denetleyiciye veya eylem düzeyine uygulanabilir.

Hata işleme ve yanıt önbelleklemesi genellikle çapraz kesme kaygılardır:
* [Hataları işleme](xref:mvc/controllers/filters#exception-filters)
* [Yanıt önbelleğe alma](xref:performance/caching/response)

Birçok çapraz kesme konusu, filtreler veya özel [Ara yazılım](xref:fundamentals/middleware/index)kullanılarak işlenebilir.
