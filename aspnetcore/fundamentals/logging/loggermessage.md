---
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a>ASP.NET Core 'de LoggerMessage ile yüksek performanslı günlüğe kaydetme

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.Extensions.Logging.LoggerMessage>özellikler, ve gibi [günlükçü uzantısı yöntemlerine](xref:Microsoft.Extensions.Logging.LoggerExtensions)kıyasla daha az nesne ayırma ve daha düşük hesaplama yükü gerektiren önbelleğe alınabilir temsilciler oluşturur <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*> . Yüksek performanslı günlük senaryoları için, bu kalıbı kullanın <xref:Microsoft.Extensions.Logging.LoggerMessage> .

<xref:Microsoft.Extensions.Logging.LoggerMessage>Günlükçü uzantı yöntemlerine göre aşağıdaki performans avantajlarını sağlar:

* Günlükçü uzantı yöntemleri, gibi "kutulama" (dönüştürme) değer türlerini gerektirir `int` `object` . Bu <xref:Microsoft.Extensions.Logging.LoggerMessage> model, <xref:System.Action> kesin türü belirtilmiş parametrelerle statik alanlar ve genişletme yöntemleri kullanarak kutulamayı önler.
* Günlükçü uzantısı yöntemlerinin her bir günlük iletisi yazıldığında ileti şablonunu (biçim dizesi olarak adlandırılır) ayrıştırması gerekir. <xref:Microsoft.Extensions.Logging.LoggerMessage>yalnızca ileti tanımlandığında bir şablonu ayrıştırmayı gerektirir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Örnek uygulama, <xref:Microsoft.Extensions.Logging.LoggerMessage> temel bir Quote izleme sistemine sahip özellikleri gösterir. Uygulama, bellek içi veritabanı kullanarak tırnak ekler ve siler. Bu işlemler gerçekleştiğinde, günlük iletileri model kullanılarak oluşturulur <xref:Microsoft.Extensions.Logging.LoggerMessage> .

## <a name="loggermessagedefine"></a>LoggerMessage. define

[Define (LogLevel, EventID, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) <xref:System.Action> bir iletiyi günlüğe kaydetmek için bir temsilci oluşturur. <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>Aşırı Yüklemeler, adlandırılmış biçim dizesine (şablon) altı tür parametre geçişine izin verir.

Yöntemine girilen dize, <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> enterpolasyonlu bir dize değil, bir şablondur. Yer tutucular, türlerin belirtilme sırasına göre doldurulur. Şablondaki yer tutucu adları, şablonlar genelinde açıklayıcı ve tutarlı olmalıdır. Bunlar, yapılandırılmış günlük verileri içinde özellik adı olarak görev yapar. Yer tutucu adları için [Pascal büyük harfleri](/dotnet/standard/design-guidelines/capitalization-conventions) öneririz. Örneğin,, `{Count}` `{FirstName}` .

Her günlük iletisi, <xref:System.Action> [Loggermessage. define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*)tarafından oluşturulan statik bir alanda tutulur. Örneğin, örnek uygulama, Dizin sayfası (*iç/LoggerExtensions. cs*) IÇIN bir GET isteğinin günlük iletisini tanımlayacak bir alan oluşturur:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

İçin <xref:System.Action> şunu belirtin:

* Günlük düzeyi.
* <xref:Microsoft.Extensions.Logging.EventId>Statik Uzantı yönteminin adı ile benzersiz bir olay tanımlayıcısı ().
* İleti şablonu (biçim dizesi olarak adlandırılır). 

Örnek uygulamanın dizin sayfası için bir istek şunları ayarlar:

* Günlük düzeyi `Information` .
* `1`Yöntemin adı ile olay kimliği `IndexPageRequested` .
* Bir dizeye ileti şablonu (biçim dizesi olarak adlandırılır).

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

Yapılandırılmış günlük depoları, olay kimliği ile birlikte verileri zenginleştirmek için sağlandığında olay adını kullanabilir. Örneğin, [Serilog](https://github.com/serilog/serilog-extensions-logging) olay adını kullanır.

, <xref:System.Action> Türü kesin belirlenmiş bir uzantı yöntemiyle çağrılır. `IndexPageRequested`Yöntemi, örnek uygulamada bir dizin sayfası get isteği için bir ileti kaydeder:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

`IndexPageRequested`, `OnGetAsync` *sayfa/dizin. cshtml. cs*içindeki yöntemdeki günlükçü üzerinde çağrılır:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

Uygulamanın konsol çıkışını inceleyin:

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

Parametreleri bir günlük iletisine geçirmek için, statik alanı oluştururken en fazla altı tür tanımlayın. Örnek uygulama, alan için bir tür tanımlayarak bir alıntı eklerken bir dizeyi günlüğe kaydeder `string` <xref:System.Action> :

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

Temsilcinin günlük iletisi şablonu, belirtilen türlerden yer tutucu değerlerini alır. Örnek uygulama, quote parametresinin bir tırnak işareti eklemek için bir temsilci tanımlar `string` :

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

Tırnak eklemek için statik genişletme yöntemi, `QuoteAdded` quote bağımsız değişkeni değerini alır ve <xref:System.Action> temsilciye geçirir:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

Dizin sayfasının sayfa modelinde (*Sayfalar/Index. cshtml. cs*), `QuoteAdded` iletiyi günlüğe kaydetmek için çağrılır:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

Uygulamanın konsol çıkışını inceleyin:

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

Örnek uygulama, teklif silme için bir [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) kalıbı uygular. Başarılı silme işlemi için bir bilgilendirici ileti günlüğe kaydedilir. Bir özel durum oluştuğunda silme işlemi için bir hata iletisi günlüğe kaydedilir. Başarısız silme işleminin günlük iletisi, özel durum yığın izlemesini içerir (*iç/LoggerExtensions. cs*):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

Özel durumun içindeki temsilciye nasıl geçtiğini aklınızda yapın `QuoteDeleteFailed` :

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

Dizin sayfasının sayfa modelinde, başarılı bir teklif silme işlemi `QuoteDeleted` günlükçü üzerindeki yöntemi çağırır. Silinmek üzere bir teklif bulunamadığında, bir oluşturulur <xref:System.ArgumentNullException> . Özel durum, [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ifadesiyle `QuoteDeleteFailed` yakalar ve [catch](/dotnet/csharp/language-reference/keywords/try-catch) bloğunda (*Pages/Index. cshtml. cs*) günlükçü üzerindeki yöntemi çağırarak günlüğe kaydedilir:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

Bir teklif başarıyla silindiğinde, uygulamanın konsol çıkışını inceleyin:

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

Teklif silme başarısız olduğunda, uygulamanın konsol çıkışını inceleyin. Özel durumun günlük iletisine dahil edildiğini unutmayın:

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

## <a name="loggermessagedefinescope"></a>LoggerMessage. DefineScope

[DefineScope (String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) , <xref:System.Func%601> [günlük kapsamı](xref:fundamentals/logging/index#log-scopes)tanımlamak için bir temsilci oluşturur. <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>Aşırı Yüklemeler, adlandırılmış biçim dizesine (şablon) üç tür parametrenin geçirilmesine izin verir.

Yönteminde olduğu gibi <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> , yöntemi için girilen dize, ilişkili <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> dize değil, bir şablondur. Yer tutucular, türlerin belirtilme sırasına göre doldurulur. Şablondaki yer tutucu adları, şablonlar genelinde açıklayıcı ve tutarlı olmalıdır. Bunlar, yapılandırılmış günlük verileri içinde özellik adı olarak görev yapar. Yer tutucu adları için [Pascal büyük harfleri](/dotnet/standard/design-guidelines/capitalization-conventions) öneririz. Örneğin,, `{Count}` `{FirstName}` .

Yöntemini kullanarak bir dizi günlük mesajı için uygulanacak [günlük kapsamını](xref:fundamentals/logging/index#log-scopes) tanımlayın <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> .

Örnek uygulamanın, veritabanındaki tüm teklifleri silmek için **Tümünü Temizle** düğmesi vardır. Tırnak işaretleri tek seferde kaldırılarak silinir. Bir teklifin her silindiği her seferinde `QuoteDeleted` yöntemi günlükçü üzerinde çağrılır. Bu günlük iletilerine bir günlük kapsamı eklenir.

`IncludeScopes` *AppSettings. JSON*konsol günlükçü bölümünde etkinleştirin:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

Bir günlük kapsamı oluşturmak için, kapsam için bir temsilci tutacak bir alan ekleyin <xref:System.Func%601> . Örnek uygulama `_allQuotesDeletedScope` (*Iç/LoggerExtensions. cs*) adlı bir alan oluşturur:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>Temsilciyi oluşturmak için kullanın. Temsilci çağrıldığında Şablon bağımsız değişkenleri olarak kullanmak için en fazla üç tür belirlenebilir. Örnek uygulama, silinen tekliflerin sayısını (bir tür) içeren bir ileti şablonu kullanır `int` :

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

Günlük iletisi için bir statik genişletme yöntemi sağlayın. İleti şablonunda görünen adlandırılmış özellikler için herhangi bir tür parametresi ekleyin. Örnek uygulama, `count` silmek ve geri döndürmek için tırnak içine alır `_allQuotesDeletedScope` :

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

Kapsam, oturum açma uzantısı çağrılarını bir [using](/dotnet/csharp/language-reference/keywords/using-statement) bloğunda sarmalar:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

Uygulamanın konsol çıkışında günlük iletilerini inceleyin. Aşağıdaki sonuç, günlük kapsamı iletisi dahil olmak üzere silinen üç tırnak gösterir:

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

<xref:Microsoft.Extensions.Logging.LoggerMessage>özellikler, ve gibi [günlükçü uzantısı yöntemlerine](xref:Microsoft.Extensions.Logging.LoggerExtensions)kıyasla daha az nesne ayırma ve daha düşük hesaplama yükü gerektiren önbelleğe alınabilir temsilciler oluşturur <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*> . Yüksek performanslı günlük senaryoları için, bu kalıbı kullanın <xref:Microsoft.Extensions.Logging.LoggerMessage> .

<xref:Microsoft.Extensions.Logging.LoggerMessage>Günlükçü uzantı yöntemlerine göre aşağıdaki performans avantajlarını sağlar:

* Günlükçü uzantı yöntemleri, gibi "kutulama" (dönüştürme) değer türlerini gerektirir `int` `object` . Bu <xref:Microsoft.Extensions.Logging.LoggerMessage> model, <xref:System.Action> kesin türü belirtilmiş parametrelerle statik alanlar ve genişletme yöntemleri kullanarak kutulamayı önler.
* Günlükçü uzantısı yöntemlerinin her bir günlük iletisi yazıldığında ileti şablonunu (biçim dizesi olarak adlandırılır) ayrıştırması gerekir. <xref:Microsoft.Extensions.Logging.LoggerMessage>yalnızca ileti tanımlandığında bir şablonu ayrıştırmayı gerektirir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Örnek uygulama, <xref:Microsoft.Extensions.Logging.LoggerMessage> temel bir Quote izleme sistemine sahip özellikleri gösterir. Uygulama, bellek içi veritabanı kullanarak tırnak ekler ve siler. Bu işlemler gerçekleştiğinde, günlük iletileri model kullanılarak oluşturulur <xref:Microsoft.Extensions.Logging.LoggerMessage> .

## <a name="loggermessagedefine"></a>LoggerMessage. define

[Define (LogLevel, EventID, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) <xref:System.Action> bir iletiyi günlüğe kaydetmek için bir temsilci oluşturur. <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>Aşırı Yüklemeler, adlandırılmış biçim dizesine (şablon) altı tür parametre geçişine izin verir.

Yöntemine girilen dize, <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> enterpolasyonlu bir dize değil, bir şablondur. Yer tutucular, türlerin belirtilme sırasına göre doldurulur. Şablondaki yer tutucu adları, şablonlar genelinde açıklayıcı ve tutarlı olmalıdır. Bunlar, yapılandırılmış günlük verileri içinde özellik adı olarak görev yapar. Yer tutucu adları için [Pascal büyük harfleri](/dotnet/standard/design-guidelines/capitalization-conventions) öneririz. Örneğin,, `{Count}` `{FirstName}` .

Her günlük iletisi, <xref:System.Action> [Loggermessage. define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*)tarafından oluşturulan statik bir alanda tutulur. Örneğin, örnek uygulama, Dizin sayfası (*iç/LoggerExtensions. cs*) IÇIN bir GET isteğinin günlük iletisini tanımlayacak bir alan oluşturur:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

İçin <xref:System.Action> şunu belirtin:

* Günlük düzeyi.
* <xref:Microsoft.Extensions.Logging.EventId>Statik Uzantı yönteminin adı ile benzersiz bir olay tanımlayıcısı ().
* İleti şablonu (biçim dizesi olarak adlandırılır). 

Örnek uygulamanın dizin sayfası için bir istek şunları ayarlar:

* Günlük düzeyi `Information` .
* `1`Yöntemin adı ile olay kimliği `IndexPageRequested` .
* Bir dizeye ileti şablonu (biçim dizesi olarak adlandırılır).

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

Yapılandırılmış günlük depoları, olay kimliği ile birlikte verileri zenginleştirmek için sağlandığında olay adını kullanabilir. Örneğin, [Serilog](https://github.com/serilog/serilog-extensions-logging) olay adını kullanır.

, <xref:System.Action> Türü kesin belirlenmiş bir uzantı yöntemiyle çağrılır. `IndexPageRequested`Yöntemi, örnek uygulamada bir dizin sayfası get isteği için bir ileti kaydeder:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

`IndexPageRequested`, `OnGetAsync` *sayfa/dizin. cshtml. cs*içindeki yöntemdeki günlükçü üzerinde çağrılır:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

Uygulamanın konsol çıkışını inceleyin:

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

Parametreleri bir günlük iletisine geçirmek için, statik alanı oluştururken en fazla altı tür tanımlayın. Örnek uygulama, alan için bir tür tanımlayarak bir alıntı eklerken bir dizeyi günlüğe kaydeder `string` <xref:System.Action> :

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

Temsilcinin günlük iletisi şablonu, belirtilen türlerden yer tutucu değerlerini alır. Örnek uygulama, quote parametresinin bir tırnak işareti eklemek için bir temsilci tanımlar `string` :

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

Tırnak eklemek için statik genişletme yöntemi, `QuoteAdded` quote bağımsız değişkeni değerini alır ve <xref:System.Action> temsilciye geçirir:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

Dizin sayfasının sayfa modelinde (*Sayfalar/Index. cshtml. cs*), `QuoteAdded` iletiyi günlüğe kaydetmek için çağrılır:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

Uygulamanın konsol çıkışını inceleyin:

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

Örnek uygulama, teklif silme için bir [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) kalıbı uygular. Başarılı silme işlemi için bir bilgilendirici ileti günlüğe kaydedilir. Bir özel durum oluştuğunda silme işlemi için bir hata iletisi günlüğe kaydedilir. Başarısız silme işleminin günlük iletisi, özel durum yığın izlemesini içerir (*iç/LoggerExtensions. cs*):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

Özel durumun içindeki temsilciye nasıl geçtiğini aklınızda yapın `QuoteDeleteFailed` :

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

Dizin sayfasının sayfa modelinde, başarılı bir teklif silme işlemi `QuoteDeleted` günlükçü üzerindeki yöntemi çağırır. Silinmek üzere bir teklif bulunamadığında, bir oluşturulur <xref:System.ArgumentNullException> . Özel durum, [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ifadesiyle `QuoteDeleteFailed` yakalar ve [catch](/dotnet/csharp/language-reference/keywords/try-catch) bloğunda (*Pages/Index. cshtml. cs*) günlükçü üzerindeki yöntemi çağırarak günlüğe kaydedilir:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

Bir teklif başarıyla silindiğinde, uygulamanın konsol çıkışını inceleyin:

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

Teklif silme başarısız olduğunda, uygulamanın konsol çıkışını inceleyin. Özel durumun günlük iletisine dahil edildiğini unutmayın:

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

## <a name="loggermessagedefinescope"></a>LoggerMessage. DefineScope

[DefineScope (String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) , <xref:System.Func%601> [günlük kapsamı](xref:fundamentals/logging/index#log-scopes)tanımlamak için bir temsilci oluşturur. <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>Aşırı Yüklemeler, adlandırılmış biçim dizesine (şablon) üç tür parametrenin geçirilmesine izin verir.

Yönteminde olduğu gibi <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> , yöntemi için girilen dize, ilişkili <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> dize değil, bir şablondur. Yer tutucular, türlerin belirtilme sırasına göre doldurulur. Şablondaki yer tutucu adları, şablonlar genelinde açıklayıcı ve tutarlı olmalıdır. Bunlar, yapılandırılmış günlük verileri içinde özellik adı olarak görev yapar. Yer tutucu adları için [Pascal büyük harfleri](/dotnet/standard/design-guidelines/capitalization-conventions) öneririz. Örneğin,, `{Count}` `{FirstName}` .

Yöntemini kullanarak bir dizi günlük mesajı için uygulanacak [günlük kapsamını](xref:fundamentals/logging/index#log-scopes) tanımlayın <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> .

Örnek uygulamanın, veritabanındaki tüm teklifleri silmek için **Tümünü Temizle** düğmesi vardır. Tırnak işaretleri tek seferde kaldırılarak silinir. Bir teklifin her silindiği her seferinde `QuoteDeleted` yöntemi günlükçü üzerinde çağrılır. Bu günlük iletilerine bir günlük kapsamı eklenir.

`IncludeScopes` *AppSettings. JSON*konsol günlükçü bölümünde etkinleştirin:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

Bir günlük kapsamı oluşturmak için, kapsam için bir temsilci tutacak bir alan ekleyin <xref:System.Func%601> . Örnek uygulama `_allQuotesDeletedScope` (*Iç/LoggerExtensions. cs*) adlı bir alan oluşturur:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>Temsilciyi oluşturmak için kullanın. Temsilci çağrıldığında Şablon bağımsız değişkenleri olarak kullanmak için en fazla üç tür belirlenebilir. Örnek uygulama, silinen tekliflerin sayısını (bir tür) içeren bir ileti şablonu kullanır `int` :

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

Günlük iletisi için bir statik genişletme yöntemi sağlayın. İleti şablonunda görünen adlandırılmış özellikler için herhangi bir tür parametresi ekleyin. Örnek uygulama, `count` silmek ve geri döndürmek için tırnak içine alır `_allQuotesDeletedScope` :

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

Kapsam, oturum açma uzantısı çağrılarını bir [using](/dotnet/csharp/language-reference/keywords/using-statement) bloğunda sarmalar:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

Uygulamanın konsol çıkışında günlük iletilerini inceleyin. Aşağıdaki sonuç, günlük kapsamı iletisi dahil olmak üzere silinen üç tırnak gösterir:

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
