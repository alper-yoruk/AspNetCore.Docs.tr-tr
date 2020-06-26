---
title: ASP.NET Core Web API 'sindeki özel formatıcılar
author: rick-anderson
description: ASP.NET Core Web API 'Leri için özel biçimleri oluşturma ve kullanma hakkında bilgi edinin.
ms.author: riande
ms.date: 02/08/2017
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 27819f77cf86c946ab0415d3583dfbab80a24cf5
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408870"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>ASP.NET Core Web API 'sindeki özel formatıcılar

, [Kirk Larkabağı](https://twitter.com/serpent5) ve [Tom Dykstra](https://github.com/tdykstra)tarafından yapılır.

ASP.NET Core MVC, giriş ve çıkış formatlayıcıları kullanılarak Web API 'Lerinde veri değişimini destekler. Giriş formatlayıcıları [model bağlama](xref:mvc/models/model-binding)tarafından kullanılır. Çıkış biçimleri, [yanıtları biçimlendirmek](xref:web-api/advanced/formatting)için kullanılır.

Framework, JSON ve XML için yerleşik giriş ve çıkış biçimleri sağlar. Düz metin için yerleşik bir çıkış biçimlendiricisi sağlar, ancak düz metin için bir giriş biçimlendiricisi sağlamaz.

Bu makalede, özel formatlayıcılar oluşturarak ek biçimler için nasıl destek ekleneceği gösterilmektedir. Özel düz metin girişi biçimlendiricisi örneği için GitHub 'da [Textplainınputformatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) bölümüne bakın.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>Özel formatlayıcılar ne zaman kullanılır?

Bult-ın biçimleri tarafından işlenmeyen bir içerik türü için destek eklemek üzere özel bir biçimlendirici kullanın.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>Özel biçimlendirici kullanma konusuna genel bakış

Özel bir biçimlendirici oluşturmak için:

* İstemciye gönderilen verileri seri hale getirmek için bir çıkış biçimlendirici sınıfı oluşturun.
* İstemciden alınan verileri seri hale getirme için bir giriş biçimlendirici sınıfı oluşturun.
* `InputFormatters` `OutputFormatters` [Mvcoptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions)içindeki ve koleksiyonlarına biçimlendirici sınıf örnekleri ekleyin.

## <a name="how-to-create-a-custom-formatter-class"></a>Özel bir biçimlendirici sınıfı oluşturma

Bir biçimlendirici oluşturmak için:

* Sınıfı uygun temel sınıftan türet. Örnek uygulama <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> ve ' den türetilir <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> .
* Oluşturucuda geçerli medya türleri ve kodlamalar belirtin.
* <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A>Ve yöntemlerini geçersiz kılın <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> .
* <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A>Ve yöntemlerini geçersiz kılın `WriteResponseBodyAsync` .

Aşağıdaki kod, `VcardOutputFormatter` [örnekten](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample)sınıfını gösterir:

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=snippet)]
  
### <a name="derive-from-the-appropriate-base-class"></a>Uygun taban sınıftan türet

Metin medya türleri (örneğin, vCard) için, [Textinputformatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) veya [Textoutputformatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) temel sınıfından türetirsiniz.

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

İkili türler için [inputformatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) veya [outputformatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) temel sınıfından türetirsiniz.

### <a name="specify-valid-media-types-and-encodings"></a>Geçerli medya türlerini ve kodlamaları belirtin

Oluşturucuda, ve koleksiyonlarına ekleyerek geçerli medya türlerini ve kodlamaları belirtin `SupportedMediaTypes` `SupportedEncodings` .

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=ctor)]

Bir biçimlendirici sınıfı, bağımlılıkları için Oluşturucu Ekleme **işlemini kullanamaz.** Örneğin, `ILogger<VcardOutputFormatter>` oluşturucuya bir parametre olarak eklenemez. Hizmetlere erişmek için yöntemlere geçirilen bağlam nesnesini kullanın. Bu makaledeki bir kod örneği ve [örnek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample) bunun nasıl yapılacağını göstermektedir.

### <a name="override-canreadtype-and-canwritetype"></a>CanReadType ve CanWriteType geçersiz kılınır

Veya yöntemlerini geçersiz kılarak içinden seri durumdan çıkarılacak veya seri hale getirilecek türü belirtin `CanReadType` `CanWriteType` . Örneğin, bir türden vCard metni oluşturma `Contact` ve tam tersi.

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

#### <a name="the-canwriteresult-method"></a>CanWriteResult yöntemi

Bazı senaryolarda, `CanWriteResult` yerine geçersiz kılınmalıdır `CanWriteType` . `CanWriteResult`Aşağıdaki koşullar doğruysa kullanın:

* Eylem yöntemi bir model sınıfı döndürür.
* Çalışma zamanında döndürülebilecek türetilmiş sınıflar var.
* Eylem tarafından döndürülen türetilmiş sınıf, çalışma zamanında bilinmelidir.

Örneğin, eylem yöntemini varsayalım:

* İmza bir `Person` tür döndürür.
* , `Student` `Instructor` Öğesinden türetilen bir veya türünü döndürebilir `Person` . 

Biçimlendiricinin yalnızca nesneleri işlemesini sağlamak için `Student` , metoduna sunulan bağlam nesnesindeki [nesne](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) türünü denetleyin `CanWriteResult` . Eylem yöntemi şunu döndürdüğünde `IActionResult` :

* Kullanılması gerekli değildir `CanWriteResult` .
* `CanWriteType`Yöntemi, çalışma zamanı türünü alır.

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a>ReadRequestBodyAsync ve WriteResponseBodyAsync geçersiz kıl

Seri durumdan çıkarma veya serileştirme gerçekleştirilir `ReadRequestBodyAsync` `WriteResponseBodyAsync` . Aşağıdaki örnek, bağımlılık ekleme kapsayıcısından hizmetlerin nasıl alınacağını gösterir. Hizmetler, Oluşturucu parametrelerinden elde alınamaz.

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=writeresponse)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>MVC 'yi özel bir biçimlendirici kullanacak şekilde yapılandırma

Özel bir biçimlendirici kullanmak için, veya koleksiyonuna biçimlendirici sınıfının bir örneğini ekleyin `InputFormatters` `OutputFormatters` .

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/3.1sample/Startup.cs?name=mvcoptions)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

Biçimlendiriciler, eklediğiniz sırada değerlendirilir. Birincisi bir öncelik alır.

## <a name="the-completed-vcardinputformatter-class"></a>Tamamlanan `VcardInputFormatter` sınıf

Aşağıdaki kod, `VcardInputFormatter` [örnekten](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample)sınıfını gösterir:

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardInputFormatter.cs?name=snippet)]

## <a name="test-the-app"></a>Uygulamayı test edin

[Bu makale için](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), temel vCard giriş ve çıkış formatlayıcıları uygulayan örnek uygulamayı çalıştırın. Uygulama, vCard 'ları aşağıdakine benzer şekilde okur ve yazar:

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
