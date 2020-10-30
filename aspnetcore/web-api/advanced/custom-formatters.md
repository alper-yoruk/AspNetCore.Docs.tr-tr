---
title: ASP.NET Core Web API 'sindeki özel formatıcılar
author: rick-anderson
description: ASP.NET Core Web API 'Leri için özel biçimleri oluşturma ve kullanma hakkında bilgi edinin.
ms.author: riande
ms.date: 06/25/2020
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
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: e4d73fdc0db3faeace5d68b3d71718315e68cae3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058928"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>ASP.NET Core Web API 'sindeki özel formatıcılar

, [Kirk Larkabağı](https://twitter.com/serpent5) ve [Tom Dykstra](https://github.com/tdykstra)tarafından yapılır.

ASP.NET Core MVC, giriş ve çıkış formatlayıcıları kullanılarak Web API 'Lerinde veri değişimini destekler. Giriş formatlayıcıları [model bağlama](xref:mvc/models/model-binding)tarafından kullanılır. Çıkış biçimleri, [yanıtları biçimlendirmek](xref:web-api/advanced/formatting)için kullanılır.

Framework, JSON ve XML için yerleşik giriş ve çıkış biçimleri sağlar. Düz metin için yerleşik bir çıkış biçimlendiricisi sağlar, ancak düz metin için bir giriş biçimlendiricisi sağlamaz.

Bu makalede, özel formatlayıcılar oluşturarak ek biçimler için nasıl destek ekleneceği gösterilmektedir. Özel düz metin girişi biçimlendiricisi örneği için GitHub 'da [Textplainınputformatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) bölümüne bakın.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>Özel formatlayıcılar ne zaman kullanılır?

Yerleşik biçimleyiciler tarafından işlenmeyen bir içerik türü için destek eklemek üzere özel bir biçimlendirici kullanın.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>Özel biçimlendirici kullanma konusuna genel bakış

Özel bir biçimlendirici oluşturmak için:

* İstemciye gönderilen verileri seri hale getirmek için bir çıkış biçimlendirici sınıfı oluşturun.
* İstemciden alınan verilerin serisini kaldırma için bir giriş biçimlendirici sınıfı oluşturun.
* İçindeki ve koleksiyonlarına biçimlendirici sınıf örnekleri ekleyin `InputFormatters` `OutputFormatters` <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .

## <a name="how-to-create-a-custom-formatter-class"></a>Özel bir biçimlendirici sınıfı oluşturma

Bir biçimlendirici oluşturmak için:

* Sınıfı uygun temel sınıftan türet. Örnek uygulama <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> ve ' den türetilir <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> .
* Oluşturucuda geçerli medya türleri ve kodlamalar belirtin.
* <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A>Ve yöntemlerini geçersiz kılın <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> .
* <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A>Ve yöntemlerini geçersiz kılın `WriteResponseBodyAsync` .

Aşağıdaki kod, `VcardOutputFormatter` [örnekten](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples)sınıfını gösterir:

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_Class)]
  
### <a name="derive-from-the-appropriate-base-class"></a>Uygun taban sınıftan türet

Metin medya türleri için (örneğin, vCard), <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> veya temel sınıftan türetirsiniz <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> .

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ClassDeclaration)]

İkili türler için <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> veya temel sınıftan türetirsiniz <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> .

### <a name="specify-valid-media-types-and-encodings"></a>Geçerli medya türlerini ve kodlamaları belirtin

Oluşturucuda, ve koleksiyonlarına ekleyerek geçerli medya türlerini ve kodlamaları belirtin `SupportedMediaTypes` `SupportedEncodings` .

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ctor)]

Bir biçimlendirici sınıfı, bağımlılıkları için Oluşturucu Ekleme **işlemini kullanamaz.** Örneğin, `ILogger<VcardOutputFormatter>` oluşturucuya bir parametre olarak eklenemez. Hizmetlere erişmek için yöntemlere geçirilen bağlam nesnesini kullanın. Bu makaledeki bir kod örneği ve [örnek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) bunun nasıl yapılacağını göstermektedir.

### <a name="override-canreadtype-and-canwritetype"></a>CanReadType ve CanWriteType geçersiz kılınır

Veya yöntemlerini geçersiz kılarak içinden seri durumdan çıkarılacak veya seri hale getirilecek türü belirtin `CanReadType` `CanWriteType` . Örneğin, bir türden vCard metni oluşturma `Contact` ve tam tersi.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_CanWriteType)]

#### <a name="the-canwriteresult-method"></a>CanWriteResult yöntemi

Bazı senaryolarda, `CanWriteResult` yerine geçersiz kılınmalıdır `CanWriteType` . `CanWriteResult`Aşağıdaki koşullar doğruysa kullanın:

* Eylem yöntemi bir model sınıfı döndürür.
* Çalışma zamanında döndürülebilecek türetilmiş sınıflar var.
* Eylem tarafından döndürülen türetilmiş sınıf, çalışma zamanında bilinmelidir.

Örneğin, eylem yöntemini varsayalım:

* İmza bir `Person` tür döndürür.
* , `Student` `Instructor` Öğesinden türetilen bir veya türünü döndürebilir `Person` . 

Biçimlendiricisi yalnızca nesneleri işlemek için `Student` , <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> metoduna sunulan bağlam nesnesindeki türünü denetleyin `CanWriteResult` . Eylem yöntemi şunu döndürdüğünde `IActionResult` :

* Kullanılması gerekli değildir `CanWriteResult` .
* `CanWriteType`Yöntemi, çalışma zamanı türünü alır.

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a>ReadRequestBodyAsync ve WriteResponseBodyAsync geçersiz kıl

Seri durumdan çıkarma veya serileştirme gerçekleştirilir `ReadRequestBodyAsync` `WriteResponseBodyAsync` . Aşağıdaki örnek, bağımlılık ekleme kapsayıcısından hizmetlerin nasıl alınacağını gösterir. Hizmetler, Oluşturucu parametrelerinden elde alınamaz.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_WriteResponseBodyAsync)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>MVC 'yi özel bir biçimlendirici kullanacak şekilde yapılandırma

Özel bir biçimlendirici kullanmak için, veya koleksiyonuna biçimlendirici sınıfının bir örneğini ekleyin `InputFormatters` `OutputFormatters` .

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/2.x/CustomFormattersSample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

Biçimlendiriciler, eklediğiniz sırada değerlendirilir. Birincisi bir öncelik alır.

## <a name="the-complete-vcardinputformatter-class"></a>Tüm `VcardInputFormatter` sınıf

Aşağıdaki kod, `VcardInputFormatter` [örnekten](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples)sınıfını gösterir:

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardInputFormatter.cs?name=snippet_Class)]

## <a name="test-the-app"></a>Uygulamayı test etme

[Bu makale için](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples), temel vCard giriş ve çıkış formatlayıcıları uygulayan örnek uygulamayı çalıştırın. Uygulama, vCard 'ları aşağıdakine benzer şekilde okur ve yazar:

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

VCard çıktısını görmek için, uygulamayı çalıştırın ve Accept üst bilgisine sahip bir get isteği gönderin `text/vcard` `https://localhost:5001/api/contacts` .

Bellek içi kişiler koleksiyonuna vCard eklemek için:

* `Post` `/api/contacts` Postman gibi bir araçla bir istek gönderin.
* `Content-Type`Üstbilgiyi olarak ayarlayın `text/vcard` .
* `vCard`Gövdedeki metni, önceki örnekte olduğu gibi ayarlayın.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
