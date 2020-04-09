---
title: ASP.NET Core'da LoggerMessage ile yüksek performanslı günlüğe kaydetme
author: rick-anderson
description: Yüksek performanslı günlük senaryoları için daha az nesne ayırması gerektiren önbelleğe uygun temsilciler oluşturmak için LoggerMessage'ı nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2019
uid: fundamentals/logging/loggermessage
ms.openlocfilehash: 48ebba69b5c15a0f9a42f7f6b3d2c1fcb0a2211c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663221"
---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a>ASP.NET Core'da LoggerMessage ile yüksek performanslı günlüğe kaydetme

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.Extensions.Logging.LoggerMessage>özellikler, [logger uzantı yöntemlerine](xref:Microsoft.Extensions.Logging.LoggerExtensions)göre daha az nesne ayırması ve azaltılmış hesaplama ek <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>yükü gerektiren önbelleğe alınan temsilciler oluşturur. Yüksek performanslı günlüğe kaydetme <xref:Microsoft.Extensions.Logging.LoggerMessage> senaryoları için deseni kullanın.

<xref:Microsoft.Extensions.Logging.LoggerMessage>Logger uzantı yöntemlerine göre aşağıdaki performans avantajlarını sağlar:

* Logger uzantı yöntemleri , `int` `object`" içine "kutulama" (dönüştürme) değer türleri, gerektirir . Desen, <xref:Microsoft.Extensions.Logging.LoggerMessage> güçlü bir şekilde <xref:System.Action> yazılan parametrelere sahip statik alanlar ve uzatma yöntemleri kullanarak kutulamayı önler.
* Logger uzantı yöntemleri, günlük iletisi her yazıldığında ileti şablonu (adlandırılmış biçim dizesi) ayrıştırmalıdır. <xref:Microsoft.Extensions.Logging.LoggerMessage>ileti tanımlandığında şablonu yalnızca bir kez ayrıştırma gerektirir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

Örnek uygulama, <xref:Microsoft.Extensions.Logging.LoggerMessage> temel bir teklif izleme sistemine sahip özellikleri gösterir. Uygulama, bellek içi bir veritabanı kullanarak tırnak eklemeve silme. Bu işlemler gerçekleştiğinde, günlük iletileri <xref:Microsoft.Extensions.Logging.LoggerMessage> desen kullanılarak oluşturulur.

## <a name="loggermessagedefine"></a>LoggerMessage.Define

[Define (LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) bir iletiyi günlüğe kaydetmek için bir <xref:System.Action> temsilci oluşturur. <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>aşırı yüklemeler, altı tür parametresinin adlandırılmış biçim dizesine (şablon) geçirilmesine izin verir.

<xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> Yönteme sağlanan dize, enterpolasyonlu bir dize değil şablondur. Yer tutucular, türlerin belirtildiği sırayla doldurulur. Şablondaki yer tutucu adları şablonlar arasında açıklayıcı ve tutarlı olmalıdır. Yapılandırılmış günlük verileri içinde özellik adları olarak hizmet verirler. Yer tutucu adları için [Pascal kaplaması](/dotnet/standard/design-guidelines/capitalization-conventions) öneririz. Örneğin, `{Count}`. `{FirstName}`

Her günlük iletisi <xref:System.Action> [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*)tarafından oluşturulan statik bir alanda tutulan bir . Örneğin, örnek uygulama Dizin sayfası için bir GET isteği için bir günlük iletisi açıklamak için bir alan oluşturur (*İç / LoggerExtensions.cs):*

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

Için <xref:System.Action>, belirtin:

* Günlük düzeyi.
* Statik uzantı yönteminin adı<xref:Microsoft.Extensions.Logging.EventId>ile benzersiz bir olay tanımlayıcısı .
* İleti şablonu (biçim dizesi adlı). 

Örnek uygulamanın Dizin sayfası için bir istek şu şekilde ayarlar:

* Günlük düzeyi `Information`' ye .
* Yöntemin `1` adı ile olay kimliği. `IndexPageRequested`
* İleti şablonu (adlandırılmış biçim dizesi) bir dize.

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

Yapılandırılmış günlük depoları, etkinlik kimliğiyle birlikte verildiğinde, günlük günlüğe kaydetmeyi zenginleştirmek için etkinlik adını kullanabilir. Örneğin, [Serilog](https://github.com/serilog/serilog-extensions-logging) olay adını kullanır.

Güçlü <xref:System.Action> bir şekilde yazılan bir uzantı yöntemi yle çağrılır. Yöntem, `IndexPageRequested` örnek uygulamada bir Dizin sayfası GET isteği için bir ileti kaydeder:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

`IndexPageRequested``OnGetAsync` *Pages/Index.cshtml.cs'deki*yöntemde logger'a denir:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

Uygulamanın konsol çıktısını inceleyin:

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

Parametreleri bir günlük iletisine geçirmek için statik alanı oluştururken en fazla altı tür tanımlayın. Örnek uygulama, alan için bir tür tanımlayarak `string` bir <xref:System.Action> teklif eklerken bir dize kaydeder:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

Temsilcinin günlük iletisi şablonu, yer tutucu değerlerini sağlanan türlerden alır. Örnek uygulama, teklif parametresinin bulunduğu bir teklif eklemek `string`için bir temsilci tanımlar:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

Teklif eklemek için statik uzantı yöntemi, `QuoteAdded`teklif bağımsız değişkeni <xref:System.Action> değerini alır ve temsilciye aktarIr:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

Dizin sayfasının sayfa modelinde *(Pages/Index.cshtml.cs),* `QuoteAdded` iletiyi günlüğe kaydetmek için çağrılır:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

Uygulamanın konsol çıktısını inceleyin:

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

Örnek uygulama, teklif silme için bir [deneme&ndash;yakalama](/dotnet/csharp/language-reference/keywords/try-catch) deseni uygular. Başarılı bir silme işlemi için bir bilgilendirme iletisi günlüğe kaydedilir. Bir özel durum atıldığında bir silme işlemi için bir hata iletisi günlüğe kaydedilir. Başarısız silme işleminin günlük iletisi özel durum yığını izleme *(Internal/LoggerExtensions.cs):*

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

Özel durum temsilciye nasıl geçirildiğini not `QuoteDeleteFailed`edin:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

Dizin sayfasının sayfa modelinde, başarılı bir teklif `QuoteDeleted` silme, logger'daki yöntemi çağırır. Bir teklif silinme için bulunmazsa, <xref:System.ArgumentNullException> bir atılır. Özel durum [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) deyimi tarafından sıkışıp kalır `QuoteDeleteFailed` ve [catch](/dotnet/csharp/language-reference/keywords/try-catch) bloğundaki logger'daki yöntemi arayarak kaydedilir (*Sayfalar/Index.cshtml.cs):*

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

Bir teklif başarıyla silindiğinde, uygulamanın konsol çıktısını inceleyin:

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

Teklif silme başarısız olduğunda, uygulamanın konsol çıktısını inceleyin. Özel durum günlük iletisinde yer olduğunu unutmayın:

```console
LoggerMessageSample.Pages.IndexModel: Error: Quote delete failed (Id = 999)

System.NullReferenceException: Object reference not set to an instance of an object.
   at lambda_method(Closure , ValueBuffer )
   at System.Linq.Enumerable.SelectEnumerableIterator`2.MoveNext()
   at Microsoft.EntityFrameworkCore.InMemory.Query.Internal.InMemoryShapedQueryCompilingExpressionVisitor.AsyncQueryingEnumerable`1.AsyncEnumerator.MoveNextAsync()
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at LoggerMessageSample.Pages.IndexModel.OnPostDeleteQuoteAsync(Int32 id) in c:\Users\guard\Documents\GitHub\Docs\aspnetcore\fundamentals\logging\loggermessage\samples\3.x\LoggerMessageSample\Pages\Index.cshtml.cs:line 77
```

## <a name="loggermessagedefinescope"></a>LoggerMessage.DefineScope

[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) günlük <xref:System.Func%601> [kapsamını](xref:fundamentals/logging/index#log-scopes)tanımlamak için bir temsilci oluşturur. <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>aşırı yüklemeler, üç tür parametresinin adlandırılmış biçim dizesine (şablon) geçirilmesine izin verir.

Yöntemde <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> olduğu gibi, <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> yönteme sağlanan dize bir şablondur, enterpolasyonlu bir dize değildir. Yer tutucular, türlerin belirtildiği sırayla doldurulur. Şablondaki yer tutucu adları şablonlar arasında açıklayıcı ve tutarlı olmalıdır. Yapılandırılmış günlük verileri içinde özellik adları olarak hizmet verirler. Yer tutucu adları için [Pascal kaplaması](/dotnet/standard/design-guidelines/capitalization-conventions) öneririz. Örneğin, `{Count}`. `{FirstName}`

Yöntemi [log scope](xref:fundamentals/logging/index#log-scopes) kullanarak bir dizi günlük iletisine uygulamak <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> için günlük kapsamı tanımlayın.

Örnek uygulama, veritabanındaki tüm teklifleri silen tüm tüm bunları **temizle** düğmesine sahiptir. Tırnak işaretleri teker teker kaldırılarak silinir. Bir teklif her silinir, `QuoteDeleted` yöntem logger çağrılır. Bu günlük iletilerine günlük kapsamı eklenir.

`IncludeScopes` *appsettings.json*konsol logger bölümünde etkinleştirin:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

Günlük kapsamı oluşturmak için, kapsam için <xref:System.Func%601> bir temsilci tutmak için bir alan ekleyin. Örnek uygulama ( `_allQuotesDeletedScope` *Dahili/LoggerExtensions.cs)* adlı bir alan oluşturur:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

Temsilcioluşturmak için kullanın. <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> Temsilci çağrıldığınızda şablon bağımsız değişkenleri olarak kullanılmak üzere en fazla üç tür belirtilebilir. Örnek uygulama, silinen tırnak sayısı (bir tür) içeren bir `int` ileti şablonu kullanır:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

Günlük iletisi için statik bir uzantı yöntemi sağlayın. İleti şablonunda görünen adlandırılmış özellikler için herhangi bir tür parametresi ekleyin. Örnek uygulama silmek `count` için tırnak bir `_allQuotesDeletedScope`alır ve döner:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

Kapsam, günlük uzantısı çağrılarını [kullanarak](/dotnet/csharp/language-reference/keywords/using-statement) bir blokta sarar:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

Uygulamanın konsol çıkışındaki günlük iletilerini inceleyin. Aşağıdaki sonuç, günlük kapsamı iletisi dahil üç tırnak gösterir:

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.Extensions.Logging.LoggerMessage>özellikler, [logger uzantı yöntemlerine](xref:Microsoft.Extensions.Logging.LoggerExtensions)göre daha az nesne ayırması ve azaltılmış hesaplama ek <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>yükü gerektiren önbelleğe alınan temsilciler oluşturur. Yüksek performanslı günlüğe kaydetme <xref:Microsoft.Extensions.Logging.LoggerMessage> senaryoları için deseni kullanın.

<xref:Microsoft.Extensions.Logging.LoggerMessage>Logger uzantı yöntemlerine göre aşağıdaki performans avantajlarını sağlar:

* Logger uzantı yöntemleri , `int` `object`" içine "kutulama" (dönüştürme) değer türleri, gerektirir . Desen, <xref:Microsoft.Extensions.Logging.LoggerMessage> güçlü bir şekilde <xref:System.Action> yazılan parametrelere sahip statik alanlar ve uzatma yöntemleri kullanarak kutulamayı önler.
* Logger uzantı yöntemleri, günlük iletisi her yazıldığında ileti şablonu (adlandırılmış biçim dizesi) ayrıştırmalıdır. <xref:Microsoft.Extensions.Logging.LoggerMessage>ileti tanımlandığında şablonu yalnızca bir kez ayrıştırma gerektirir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

Örnek uygulama, <xref:Microsoft.Extensions.Logging.LoggerMessage> temel bir teklif izleme sistemine sahip özellikleri gösterir. Uygulama, bellek içi bir veritabanı kullanarak tırnak eklemeve silme. Bu işlemler gerçekleştiğinde, günlük iletileri <xref:Microsoft.Extensions.Logging.LoggerMessage> desen kullanılarak oluşturulur.

## <a name="loggermessagedefine"></a>LoggerMessage.Define

[Define (LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) bir iletiyi günlüğe kaydetmek için bir <xref:System.Action> temsilci oluşturur. <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>aşırı yüklemeler, altı tür parametresinin adlandırılmış biçim dizesine (şablon) geçirilmesine izin verir.

<xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> Yönteme sağlanan dize, enterpolasyonlu bir dize değil şablondur. Yer tutucular, türlerin belirtildiği sırayla doldurulur. Şablondaki yer tutucu adları şablonlar arasında açıklayıcı ve tutarlı olmalıdır. Yapılandırılmış günlük verileri içinde özellik adları olarak hizmet verirler. Yer tutucu adları için [Pascal kaplaması](/dotnet/standard/design-guidelines/capitalization-conventions) öneririz. Örneğin, `{Count}`. `{FirstName}`

Her günlük iletisi <xref:System.Action> [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*)tarafından oluşturulan statik bir alanda tutulan bir . Örneğin, örnek uygulama Dizin sayfası için bir GET isteği için bir günlük iletisi açıklamak için bir alan oluşturur (*İç / LoggerExtensions.cs):*

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

Için <xref:System.Action>, belirtin:

* Günlük düzeyi.
* Statik uzantı yönteminin adı<xref:Microsoft.Extensions.Logging.EventId>ile benzersiz bir olay tanımlayıcısı .
* İleti şablonu (biçim dizesi adlı). 

Örnek uygulamanın Dizin sayfası için bir istek şu şekilde ayarlar:

* Günlük düzeyi `Information`' ye .
* Yöntemin `1` adı ile olay kimliği. `IndexPageRequested`
* İleti şablonu (adlandırılmış biçim dizesi) bir dize.

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

Yapılandırılmış günlük depoları, etkinlik kimliğiyle birlikte verildiğinde, günlük günlüğe kaydetmeyi zenginleştirmek için etkinlik adını kullanabilir. Örneğin, [Serilog](https://github.com/serilog/serilog-extensions-logging) olay adını kullanır.

Güçlü <xref:System.Action> bir şekilde yazılan bir uzantı yöntemi yle çağrılır. Yöntem, `IndexPageRequested` örnek uygulamada bir Dizin sayfası GET isteği için bir ileti kaydeder:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

`IndexPageRequested``OnGetAsync` *Pages/Index.cshtml.cs'deki*yöntemde logger'a denir:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

Uygulamanın konsol çıktısını inceleyin:

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

Parametreleri bir günlük iletisine geçirmek için statik alanı oluştururken en fazla altı tür tanımlayın. Örnek uygulama, alan için bir tür tanımlayarak `string` bir <xref:System.Action> teklif eklerken bir dize kaydeder:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

Temsilcinin günlük iletisi şablonu, yer tutucu değerlerini sağlanan türlerden alır. Örnek uygulama, teklif parametresinin bulunduğu bir teklif eklemek `string`için bir temsilci tanımlar:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

Teklif eklemek için statik uzantı yöntemi, `QuoteAdded`teklif bağımsız değişkeni <xref:System.Action> değerini alır ve temsilciye aktarIr:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

Dizin sayfasının sayfa modelinde *(Pages/Index.cshtml.cs),* `QuoteAdded` iletiyi günlüğe kaydetmek için çağrılır:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

Uygulamanın konsol çıktısını inceleyin:

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

Örnek uygulama, teklif silme için bir [deneme&ndash;yakalama](/dotnet/csharp/language-reference/keywords/try-catch) deseni uygular. Başarılı bir silme işlemi için bir bilgilendirme iletisi günlüğe kaydedilir. Bir özel durum atıldığında bir silme işlemi için bir hata iletisi günlüğe kaydedilir. Başarısız silme işleminin günlük iletisi özel durum yığını izleme *(Internal/LoggerExtensions.cs):*

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

Özel durum temsilciye nasıl geçirildiğini not `QuoteDeleteFailed`edin:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

Dizin sayfasının sayfa modelinde, başarılı bir teklif `QuoteDeleted` silme, logger'daki yöntemi çağırır. Bir teklif silinme için bulunmazsa, <xref:System.ArgumentNullException> bir atılır. Özel durum [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) deyimi tarafından sıkışıp kalır `QuoteDeleteFailed` ve [catch](/dotnet/csharp/language-reference/keywords/try-catch) bloğundaki logger'daki yöntemi arayarak kaydedilir (*Sayfalar/Index.cshtml.cs):*

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

Bir teklif başarıyla silindiğinde, uygulamanın konsol çıktısını inceleyin:

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

Teklif silme başarısız olduğunda, uygulamanın konsol çıktısını inceleyin. Özel durum günlük iletisinde yer olduğunu unutmayın:

```console
fail: LoggerMessageSample.Pages.IndexModel[5]
      => RequestId:0HL90M6E7PHK5:00000010 RequestPath:/ => /Index
      Quote delete failed (Id = 999)
System.ArgumentNullException: Value cannot be null.
Parameter name: entity
   at Microsoft.EntityFrameworkCore.Utilities.Check.NotNull[T]
       (T value, String parameterName)
   at Microsoft.EntityFrameworkCore.DbContext.Remove[TEntity](TEntity entity)
   at Microsoft.EntityFrameworkCore.Internal.InternalDbSet`1.Remove(TEntity entity)
   at LoggerMessageSample.Pages.IndexModel.<OnPostDeleteQuoteAsync>d__14.MoveNext() 
      in <PATH>\sample\Pages\Index.cshtml.cs:line 87
```

## <a name="loggermessagedefinescope"></a>LoggerMessage.DefineScope

[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) günlük <xref:System.Func%601> [kapsamını](xref:fundamentals/logging/index#log-scopes)tanımlamak için bir temsilci oluşturur. <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>aşırı yüklemeler, üç tür parametresinin adlandırılmış biçim dizesine (şablon) geçirilmesine izin verir.

Yöntemde <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> olduğu gibi, <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> yönteme sağlanan dize bir şablondur, enterpolasyonlu bir dize değildir. Yer tutucular, türlerin belirtildiği sırayla doldurulur. Şablondaki yer tutucu adları şablonlar arasında açıklayıcı ve tutarlı olmalıdır. Yapılandırılmış günlük verileri içinde özellik adları olarak hizmet verirler. Yer tutucu adları için [Pascal kaplaması](/dotnet/standard/design-guidelines/capitalization-conventions) öneririz. Örneğin, `{Count}`. `{FirstName}`

Yöntemi [log scope](xref:fundamentals/logging/index#log-scopes) kullanarak bir dizi günlük iletisine uygulamak <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> için günlük kapsamı tanımlayın.

Örnek uygulama, veritabanındaki tüm teklifleri silen tüm tüm bunları **temizle** düğmesine sahiptir. Tırnak işaretleri teker teker kaldırılarak silinir. Bir teklif her silinir, `QuoteDeleted` yöntem logger çağrılır. Bu günlük iletilerine günlük kapsamı eklenir.

`IncludeScopes` *appsettings.json*konsol logger bölümünde etkinleştirin:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

Günlük kapsamı oluşturmak için, kapsam için <xref:System.Func%601> bir temsilci tutmak için bir alan ekleyin. Örnek uygulama ( `_allQuotesDeletedScope` *Dahili/LoggerExtensions.cs)* adlı bir alan oluşturur:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

Temsilcioluşturmak için kullanın. <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> Temsilci çağrıldığınızda şablon bağımsız değişkenleri olarak kullanılmak üzere en fazla üç tür belirtilebilir. Örnek uygulama, silinen tırnak sayısı (bir tür) içeren bir `int` ileti şablonu kullanır:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

Günlük iletisi için statik bir uzantı yöntemi sağlayın. İleti şablonunda görünen adlandırılmış özellikler için herhangi bir tür parametresi ekleyin. Örnek uygulama silmek `count` için tırnak bir `_allQuotesDeletedScope`alır ve döner:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

Kapsam, günlük uzantısı çağrılarını [kullanarak](/dotnet/csharp/language-reference/keywords/using-statement) bir blokta sarar:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

Uygulamanın konsol çıkışındaki günlük iletilerini inceleyin. Aşağıdaki sonuç, günlük kapsamı iletisi dahil üç tırnak gösterir:

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* [Günlüğe kaydetme](xref:fundamentals/logging/index)
