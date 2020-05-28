---
<span data-ttu-id="f196a-101">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f196a-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f196a-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f196a-102">'Blazor'</span></span>
- <span data-ttu-id="f196a-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f196a-103">'Identity'</span></span>
- <span data-ttu-id="f196a-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f196a-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="f196a-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f196a-105">'Razor'</span></span>
- <span data-ttu-id="f196a-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f196a-106">'SignalR' uid:</span></span> 

---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a><span data-ttu-id="f196a-107">ASP.NET Core 'de LoggerMessage ile yüksek performanslı günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="f196a-107">High-performance logging with LoggerMessage in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f196a-108"><xref:Microsoft.Extensions.Logging.LoggerMessage>özellikler, ve gibi [günlükçü uzantısı yöntemlerine](xref:Microsoft.Extensions.Logging.LoggerExtensions)kıyasla daha az nesne ayırma ve daha düşük hesaplama yükü gerektiren önbelleğe alınabilir temsilciler oluşturur <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*> .</span><span class="sxs-lookup"><span data-stu-id="f196a-108"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="f196a-109">Yüksek performanslı günlük senaryoları için, bu kalıbı kullanın <xref:Microsoft.Extensions.Logging.LoggerMessage> .</span><span class="sxs-lookup"><span data-stu-id="f196a-109">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="f196a-110"><xref:Microsoft.Extensions.Logging.LoggerMessage>Günlükçü uzantı yöntemlerine göre aşağıdaki performans avantajlarını sağlar:</span><span class="sxs-lookup"><span data-stu-id="f196a-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="f196a-111">Günlükçü uzantı yöntemleri, gibi "kutulama" (dönüştürme) değer türlerini gerektirir `int` `object` .</span><span class="sxs-lookup"><span data-stu-id="f196a-111">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="f196a-112">Bu <xref:Microsoft.Extensions.Logging.LoggerMessage> model, <xref:System.Action> kesin türü belirtilmiş parametrelerle statik alanlar ve genişletme yöntemleri kullanarak kutulamayı önler.</span><span class="sxs-lookup"><span data-stu-id="f196a-112">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="f196a-113">Günlükçü uzantısı yöntemlerinin her bir günlük iletisi yazıldığında ileti şablonunu (biçim dizesi olarak adlandırılır) ayrıştırması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f196a-113">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="f196a-114"><xref:Microsoft.Extensions.Logging.LoggerMessage>yalnızca ileti tanımlandığında bir şablonu ayrıştırmayı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f196a-114"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="f196a-115">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f196a-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f196a-116">Örnek uygulama, <xref:Microsoft.Extensions.Logging.LoggerMessage> temel bir Quote izleme sistemine sahip özellikleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="f196a-116">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="f196a-117">Uygulama, bellek içi veritabanı kullanarak tırnak ekler ve siler.</span><span class="sxs-lookup"><span data-stu-id="f196a-117">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="f196a-118">Bu işlemler gerçekleştiğinde, günlük iletileri model kullanılarak oluşturulur <xref:Microsoft.Extensions.Logging.LoggerMessage> .</span><span class="sxs-lookup"><span data-stu-id="f196a-118">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="f196a-119">LoggerMessage. define</span><span class="sxs-lookup"><span data-stu-id="f196a-119">LoggerMessage.Define</span></span>

<span data-ttu-id="f196a-120">[Define (LogLevel, EventID, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) <xref:System.Action> bir iletiyi günlüğe kaydetmek için bir temsilci oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f196a-120">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="f196a-121"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>Aşırı Yüklemeler, adlandırılmış biçim dizesine (şablon) altı tür parametre geçişine izin verir.</span><span class="sxs-lookup"><span data-stu-id="f196a-121"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="f196a-122">Yöntemine girilen dize, <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> enterpolasyonlu bir dize değil, bir şablondur.</span><span class="sxs-lookup"><span data-stu-id="f196a-122">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="f196a-123">Yer tutucular, türlerin belirtilme sırasına göre doldurulur.</span><span class="sxs-lookup"><span data-stu-id="f196a-123">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="f196a-124">Şablondaki yer tutucu adları, şablonlar genelinde açıklayıcı ve tutarlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f196a-124">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="f196a-125">Bunlar, yapılandırılmış günlük verileri içinde özellik adı olarak görev yapar.</span><span class="sxs-lookup"><span data-stu-id="f196a-125">They serve as property names within structured log data.</span></span> <span data-ttu-id="f196a-126">Yer tutucu adları için [Pascal büyük harfleri](/dotnet/standard/design-guidelines/capitalization-conventions) öneririz.</span><span class="sxs-lookup"><span data-stu-id="f196a-126">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="f196a-127">Örneğin,, `{Count}` `{FirstName}` .</span><span class="sxs-lookup"><span data-stu-id="f196a-127">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="f196a-128">Her günlük iletisi, <xref:System.Action> [Loggermessage. define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*)tarafından oluşturulan statik bir alanda tutulur.</span><span class="sxs-lookup"><span data-stu-id="f196a-128">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="f196a-129">Örneğin, örnek uygulama, Dizin sayfası (*iç/LoggerExtensions. cs*) IÇIN bir GET isteğinin günlük iletisini tanımlayacak bir alan oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f196a-129">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="f196a-130">İçin <xref:System.Action> şunu belirtin:</span><span class="sxs-lookup"><span data-stu-id="f196a-130">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="f196a-131">Günlük düzeyi.</span><span class="sxs-lookup"><span data-stu-id="f196a-131">The log level.</span></span>
* <span data-ttu-id="f196a-132"><xref:Microsoft.Extensions.Logging.EventId>Statik Uzantı yönteminin adı ile benzersiz bir olay tanımlayıcısı ().</span><span class="sxs-lookup"><span data-stu-id="f196a-132">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="f196a-133">İleti şablonu (biçim dizesi olarak adlandırılır).</span><span class="sxs-lookup"><span data-stu-id="f196a-133">The message template (named format string).</span></span> 

<span data-ttu-id="f196a-134">Örnek uygulamanın dizin sayfası için bir istek şunları ayarlar:</span><span class="sxs-lookup"><span data-stu-id="f196a-134">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="f196a-135">Günlük düzeyi `Information` .</span><span class="sxs-lookup"><span data-stu-id="f196a-135">Log level to `Information`.</span></span>
* <span data-ttu-id="f196a-136">`1`Yöntemin adı ile olay kimliği `IndexPageRequested` .</span><span class="sxs-lookup"><span data-stu-id="f196a-136">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="f196a-137">Bir dizeye ileti şablonu (biçim dizesi olarak adlandırılır).</span><span class="sxs-lookup"><span data-stu-id="f196a-137">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="f196a-138">Yapılandırılmış günlük depoları, olay kimliği ile birlikte verileri zenginleştirmek için sağlandığında olay adını kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="f196a-138">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="f196a-139">Örneğin, [Serilog](https://github.com/serilog/serilog-extensions-logging) olay adını kullanır.</span><span class="sxs-lookup"><span data-stu-id="f196a-139">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="f196a-140">, <xref:System.Action> Türü kesin belirlenmiş bir uzantı yöntemiyle çağrılır.</span><span class="sxs-lookup"><span data-stu-id="f196a-140">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="f196a-141">`IndexPageRequested`Yöntemi, örnek uygulamada bir dizin sayfası get isteği için bir ileti kaydeder:</span><span class="sxs-lookup"><span data-stu-id="f196a-141">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="f196a-142">`IndexPageRequested`, `OnGetAsync` *sayfa/dizin. cshtml. cs*içindeki yöntemdeki günlükçü üzerinde çağrılır:</span><span class="sxs-lookup"><span data-stu-id="f196a-142">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="f196a-143">Uygulamanın konsol çıkışını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="f196a-143">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="f196a-144">Parametreleri bir günlük iletisine geçirmek için, statik alanı oluştururken en fazla altı tür tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="f196a-144">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="f196a-145">Örnek uygulama, alan için bir tür tanımlayarak bir alıntı eklerken bir dizeyi günlüğe kaydeder `string` <xref:System.Action> :</span><span class="sxs-lookup"><span data-stu-id="f196a-145">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="f196a-146">Temsilcinin günlük iletisi şablonu, belirtilen türlerden yer tutucu değerlerini alır.</span><span class="sxs-lookup"><span data-stu-id="f196a-146">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="f196a-147">Örnek uygulama, quote parametresinin bir tırnak işareti eklemek için bir temsilci tanımlar `string` :</span><span class="sxs-lookup"><span data-stu-id="f196a-147">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="f196a-148">Tırnak eklemek için statik genişletme yöntemi, `QuoteAdded` quote bağımsız değişkeni değerini alır ve <xref:System.Action> temsilciye geçirir:</span><span class="sxs-lookup"><span data-stu-id="f196a-148">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="f196a-149">Dizin sayfasının sayfa modelinde (*Sayfalar/Index. cshtml. cs*), `QuoteAdded` iletiyi günlüğe kaydetmek için çağrılır:</span><span class="sxs-lookup"><span data-stu-id="f196a-149">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="f196a-150">Uygulamanın konsol çıkışını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="f196a-150">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="f196a-151">Örnek uygulama, teklif silme için bir [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) kalıbı uygular.</span><span class="sxs-lookup"><span data-stu-id="f196a-151">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="f196a-152">Başarılı silme işlemi için bir bilgilendirici ileti günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f196a-152">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="f196a-153">Bir özel durum oluştuğunda silme işlemi için bir hata iletisi günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f196a-153">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="f196a-154">Başarısız silme işleminin günlük iletisi, özel durum yığın izlemesini içerir (*iç/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="f196a-154">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="f196a-155">Özel durumun içindeki temsilciye nasıl geçtiğini aklınızda yapın `QuoteDeleteFailed` :</span><span class="sxs-lookup"><span data-stu-id="f196a-155">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="f196a-156">Dizin sayfasının sayfa modelinde, başarılı bir teklif silme işlemi `QuoteDeleted` günlükçü üzerindeki yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="f196a-156">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="f196a-157">Silinmek üzere bir teklif bulunamadığında, bir oluşturulur <xref:System.ArgumentNullException> .</span><span class="sxs-lookup"><span data-stu-id="f196a-157">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="f196a-158">Özel durum, [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ifadesiyle `QuoteDeleteFailed` yakalar ve [catch](/dotnet/csharp/language-reference/keywords/try-catch) bloğunda (*Pages/Index. cshtml. cs*) günlükçü üzerindeki yöntemi çağırarak günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="f196a-158">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

<span data-ttu-id="f196a-159">Bir teklif başarıyla silindiğinde, uygulamanın konsol çıkışını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="f196a-159">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="f196a-160">Teklif silme başarısız olduğunda, uygulamanın konsol çıkışını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="f196a-160">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="f196a-161">Özel durumun günlük iletisine dahil edildiğini unutmayın:</span><span class="sxs-lookup"><span data-stu-id="f196a-161">Note that the exception is included in the log message:</span></span>

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

## <a name="loggermessagedefinescope"></a><span data-ttu-id="f196a-162">LoggerMessage. DefineScope</span><span class="sxs-lookup"><span data-stu-id="f196a-162">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="f196a-163">[DefineScope (String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) , <xref:System.Func%601> [günlük kapsamı](xref:fundamentals/logging/index#log-scopes)tanımlamak için bir temsilci oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f196a-163">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="f196a-164"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>Aşırı Yüklemeler, adlandırılmış biçim dizesine (şablon) üç tür parametrenin geçirilmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="f196a-164"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="f196a-165">Yönteminde olduğu gibi <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> , yöntemi için girilen dize, ilişkili <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> dize değil, bir şablondur.</span><span class="sxs-lookup"><span data-stu-id="f196a-165">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="f196a-166">Yer tutucular, türlerin belirtilme sırasına göre doldurulur.</span><span class="sxs-lookup"><span data-stu-id="f196a-166">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="f196a-167">Şablondaki yer tutucu adları, şablonlar genelinde açıklayıcı ve tutarlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f196a-167">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="f196a-168">Bunlar, yapılandırılmış günlük verileri içinde özellik adı olarak görev yapar.</span><span class="sxs-lookup"><span data-stu-id="f196a-168">They serve as property names within structured log data.</span></span> <span data-ttu-id="f196a-169">Yer tutucu adları için [Pascal büyük harfleri](/dotnet/standard/design-guidelines/capitalization-conventions) öneririz.</span><span class="sxs-lookup"><span data-stu-id="f196a-169">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="f196a-170">Örneğin,, `{Count}` `{FirstName}` .</span><span class="sxs-lookup"><span data-stu-id="f196a-170">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="f196a-171">Yöntemini kullanarak bir dizi günlük mesajı için uygulanacak [günlük kapsamını](xref:fundamentals/logging/index#log-scopes) tanımlayın <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> .</span><span class="sxs-lookup"><span data-stu-id="f196a-171">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="f196a-172">Örnek uygulamanın, veritabanındaki tüm teklifleri silmek için **Tümünü Temizle** düğmesi vardır.</span><span class="sxs-lookup"><span data-stu-id="f196a-172">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="f196a-173">Tırnak işaretleri tek seferde kaldırılarak silinir.</span><span class="sxs-lookup"><span data-stu-id="f196a-173">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="f196a-174">Bir teklifin her silindiği her seferinde `QuoteDeleted` yöntemi günlükçü üzerinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="f196a-174">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="f196a-175">Bu günlük iletilerine bir günlük kapsamı eklenir.</span><span class="sxs-lookup"><span data-stu-id="f196a-175">A log scope is added to these log messages.</span></span>

<span data-ttu-id="f196a-176">`IncludeScopes` *AppSettings. JSON*konsol günlükçü bölümünde etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="f196a-176">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="f196a-177">Bir günlük kapsamı oluşturmak için, kapsam için bir temsilci tutacak bir alan ekleyin <xref:System.Func%601> .</span><span class="sxs-lookup"><span data-stu-id="f196a-177">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="f196a-178">Örnek uygulama `_allQuotesDeletedScope` (*Iç/LoggerExtensions. cs*) adlı bir alan oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f196a-178">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="f196a-179"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>Temsilciyi oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="f196a-179">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="f196a-180">Temsilci çağrıldığında Şablon bağımsız değişkenleri olarak kullanmak için en fazla üç tür belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="f196a-180">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="f196a-181">Örnek uygulama, silinen tekliflerin sayısını (bir tür) içeren bir ileti şablonu kullanır `int` :</span><span class="sxs-lookup"><span data-stu-id="f196a-181">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="f196a-182">Günlük iletisi için bir statik genişletme yöntemi sağlayın.</span><span class="sxs-lookup"><span data-stu-id="f196a-182">Provide a static extension method for the log message.</span></span> <span data-ttu-id="f196a-183">İleti şablonunda görünen adlandırılmış özellikler için herhangi bir tür parametresi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f196a-183">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="f196a-184">Örnek uygulama, `count` silmek ve geri döndürmek için tırnak içine alır `_allQuotesDeletedScope` :</span><span class="sxs-lookup"><span data-stu-id="f196a-184">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="f196a-185">Kapsam, oturum açma uzantısı çağrılarını bir [using](/dotnet/csharp/language-reference/keywords/using-statement) bloğunda sarmalar:</span><span class="sxs-lookup"><span data-stu-id="f196a-185">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="f196a-186">Uygulamanın konsol çıkışında günlük iletilerini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="f196a-186">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="f196a-187">Aşağıdaki sonuç, günlük kapsamı iletisi dahil olmak üzere silinen üç tırnak gösterir:</span><span class="sxs-lookup"><span data-stu-id="f196a-187">The following result shows three quotes deleted with the log scope message included:</span></span>

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

<span data-ttu-id="f196a-188"><xref:Microsoft.Extensions.Logging.LoggerMessage>özellikler, ve gibi [günlükçü uzantısı yöntemlerine](xref:Microsoft.Extensions.Logging.LoggerExtensions)kıyasla daha az nesne ayırma ve daha düşük hesaplama yükü gerektiren önbelleğe alınabilir temsilciler oluşturur <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*> .</span><span class="sxs-lookup"><span data-stu-id="f196a-188"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="f196a-189">Yüksek performanslı günlük senaryoları için, bu kalıbı kullanın <xref:Microsoft.Extensions.Logging.LoggerMessage> .</span><span class="sxs-lookup"><span data-stu-id="f196a-189">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="f196a-190"><xref:Microsoft.Extensions.Logging.LoggerMessage>Günlükçü uzantı yöntemlerine göre aşağıdaki performans avantajlarını sağlar:</span><span class="sxs-lookup"><span data-stu-id="f196a-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="f196a-191">Günlükçü uzantı yöntemleri, gibi "kutulama" (dönüştürme) değer türlerini gerektirir `int` `object` .</span><span class="sxs-lookup"><span data-stu-id="f196a-191">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="f196a-192">Bu <xref:Microsoft.Extensions.Logging.LoggerMessage> model, <xref:System.Action> kesin türü belirtilmiş parametrelerle statik alanlar ve genişletme yöntemleri kullanarak kutulamayı önler.</span><span class="sxs-lookup"><span data-stu-id="f196a-192">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="f196a-193">Günlükçü uzantısı yöntemlerinin her bir günlük iletisi yazıldığında ileti şablonunu (biçim dizesi olarak adlandırılır) ayrıştırması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f196a-193">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="f196a-194"><xref:Microsoft.Extensions.Logging.LoggerMessage>yalnızca ileti tanımlandığında bir şablonu ayrıştırmayı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f196a-194"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="f196a-195">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f196a-195">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f196a-196">Örnek uygulama, <xref:Microsoft.Extensions.Logging.LoggerMessage> temel bir Quote izleme sistemine sahip özellikleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="f196a-196">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="f196a-197">Uygulama, bellek içi veritabanı kullanarak tırnak ekler ve siler.</span><span class="sxs-lookup"><span data-stu-id="f196a-197">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="f196a-198">Bu işlemler gerçekleştiğinde, günlük iletileri model kullanılarak oluşturulur <xref:Microsoft.Extensions.Logging.LoggerMessage> .</span><span class="sxs-lookup"><span data-stu-id="f196a-198">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="f196a-199">LoggerMessage. define</span><span class="sxs-lookup"><span data-stu-id="f196a-199">LoggerMessage.Define</span></span>

<span data-ttu-id="f196a-200">[Define (LogLevel, EventID, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) <xref:System.Action> bir iletiyi günlüğe kaydetmek için bir temsilci oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f196a-200">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="f196a-201"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>Aşırı Yüklemeler, adlandırılmış biçim dizesine (şablon) altı tür parametre geçişine izin verir.</span><span class="sxs-lookup"><span data-stu-id="f196a-201"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="f196a-202">Yöntemine girilen dize, <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> enterpolasyonlu bir dize değil, bir şablondur.</span><span class="sxs-lookup"><span data-stu-id="f196a-202">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="f196a-203">Yer tutucular, türlerin belirtilme sırasına göre doldurulur.</span><span class="sxs-lookup"><span data-stu-id="f196a-203">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="f196a-204">Şablondaki yer tutucu adları, şablonlar genelinde açıklayıcı ve tutarlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f196a-204">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="f196a-205">Bunlar, yapılandırılmış günlük verileri içinde özellik adı olarak görev yapar.</span><span class="sxs-lookup"><span data-stu-id="f196a-205">They serve as property names within structured log data.</span></span> <span data-ttu-id="f196a-206">Yer tutucu adları için [Pascal büyük harfleri](/dotnet/standard/design-guidelines/capitalization-conventions) öneririz.</span><span class="sxs-lookup"><span data-stu-id="f196a-206">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="f196a-207">Örneğin,, `{Count}` `{FirstName}` .</span><span class="sxs-lookup"><span data-stu-id="f196a-207">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="f196a-208">Her günlük iletisi, <xref:System.Action> [Loggermessage. define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*)tarafından oluşturulan statik bir alanda tutulur.</span><span class="sxs-lookup"><span data-stu-id="f196a-208">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="f196a-209">Örneğin, örnek uygulama, Dizin sayfası (*iç/LoggerExtensions. cs*) IÇIN bir GET isteğinin günlük iletisini tanımlayacak bir alan oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f196a-209">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="f196a-210">İçin <xref:System.Action> şunu belirtin:</span><span class="sxs-lookup"><span data-stu-id="f196a-210">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="f196a-211">Günlük düzeyi.</span><span class="sxs-lookup"><span data-stu-id="f196a-211">The log level.</span></span>
* <span data-ttu-id="f196a-212"><xref:Microsoft.Extensions.Logging.EventId>Statik Uzantı yönteminin adı ile benzersiz bir olay tanımlayıcısı ().</span><span class="sxs-lookup"><span data-stu-id="f196a-212">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="f196a-213">İleti şablonu (biçim dizesi olarak adlandırılır).</span><span class="sxs-lookup"><span data-stu-id="f196a-213">The message template (named format string).</span></span> 

<span data-ttu-id="f196a-214">Örnek uygulamanın dizin sayfası için bir istek şunları ayarlar:</span><span class="sxs-lookup"><span data-stu-id="f196a-214">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="f196a-215">Günlük düzeyi `Information` .</span><span class="sxs-lookup"><span data-stu-id="f196a-215">Log level to `Information`.</span></span>
* <span data-ttu-id="f196a-216">`1`Yöntemin adı ile olay kimliği `IndexPageRequested` .</span><span class="sxs-lookup"><span data-stu-id="f196a-216">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="f196a-217">Bir dizeye ileti şablonu (biçim dizesi olarak adlandırılır).</span><span class="sxs-lookup"><span data-stu-id="f196a-217">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="f196a-218">Yapılandırılmış günlük depoları, olay kimliği ile birlikte verileri zenginleştirmek için sağlandığında olay adını kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="f196a-218">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="f196a-219">Örneğin, [Serilog](https://github.com/serilog/serilog-extensions-logging) olay adını kullanır.</span><span class="sxs-lookup"><span data-stu-id="f196a-219">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="f196a-220">, <xref:System.Action> Türü kesin belirlenmiş bir uzantı yöntemiyle çağrılır.</span><span class="sxs-lookup"><span data-stu-id="f196a-220">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="f196a-221">`IndexPageRequested`Yöntemi, örnek uygulamada bir dizin sayfası get isteği için bir ileti kaydeder:</span><span class="sxs-lookup"><span data-stu-id="f196a-221">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="f196a-222">`IndexPageRequested`, `OnGetAsync` *sayfa/dizin. cshtml. cs*içindeki yöntemdeki günlükçü üzerinde çağrılır:</span><span class="sxs-lookup"><span data-stu-id="f196a-222">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="f196a-223">Uygulamanın konsol çıkışını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="f196a-223">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="f196a-224">Parametreleri bir günlük iletisine geçirmek için, statik alanı oluştururken en fazla altı tür tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="f196a-224">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="f196a-225">Örnek uygulama, alan için bir tür tanımlayarak bir alıntı eklerken bir dizeyi günlüğe kaydeder `string` <xref:System.Action> :</span><span class="sxs-lookup"><span data-stu-id="f196a-225">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="f196a-226">Temsilcinin günlük iletisi şablonu, belirtilen türlerden yer tutucu değerlerini alır.</span><span class="sxs-lookup"><span data-stu-id="f196a-226">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="f196a-227">Örnek uygulama, quote parametresinin bir tırnak işareti eklemek için bir temsilci tanımlar `string` :</span><span class="sxs-lookup"><span data-stu-id="f196a-227">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="f196a-228">Tırnak eklemek için statik genişletme yöntemi, `QuoteAdded` quote bağımsız değişkeni değerini alır ve <xref:System.Action> temsilciye geçirir:</span><span class="sxs-lookup"><span data-stu-id="f196a-228">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="f196a-229">Dizin sayfasının sayfa modelinde (*Sayfalar/Index. cshtml. cs*), `QuoteAdded` iletiyi günlüğe kaydetmek için çağrılır:</span><span class="sxs-lookup"><span data-stu-id="f196a-229">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="f196a-230">Uygulamanın konsol çıkışını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="f196a-230">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="f196a-231">Örnek uygulama, teklif silme için bir [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) kalıbı uygular.</span><span class="sxs-lookup"><span data-stu-id="f196a-231">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="f196a-232">Başarılı silme işlemi için bir bilgilendirici ileti günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f196a-232">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="f196a-233">Bir özel durum oluştuğunda silme işlemi için bir hata iletisi günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f196a-233">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="f196a-234">Başarısız silme işleminin günlük iletisi, özel durum yığın izlemesini içerir (*iç/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="f196a-234">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="f196a-235">Özel durumun içindeki temsilciye nasıl geçtiğini aklınızda yapın `QuoteDeleteFailed` :</span><span class="sxs-lookup"><span data-stu-id="f196a-235">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="f196a-236">Dizin sayfasının sayfa modelinde, başarılı bir teklif silme işlemi `QuoteDeleted` günlükçü üzerindeki yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="f196a-236">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="f196a-237">Silinmek üzere bir teklif bulunamadığında, bir oluşturulur <xref:System.ArgumentNullException> .</span><span class="sxs-lookup"><span data-stu-id="f196a-237">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="f196a-238">Özel durum, [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ifadesiyle `QuoteDeleteFailed` yakalar ve [catch](/dotnet/csharp/language-reference/keywords/try-catch) bloğunda (*Pages/Index. cshtml. cs*) günlükçü üzerindeki yöntemi çağırarak günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="f196a-238">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

<span data-ttu-id="f196a-239">Bir teklif başarıyla silindiğinde, uygulamanın konsol çıkışını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="f196a-239">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="f196a-240">Teklif silme başarısız olduğunda, uygulamanın konsol çıkışını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="f196a-240">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="f196a-241">Özel durumun günlük iletisine dahil edildiğini unutmayın:</span><span class="sxs-lookup"><span data-stu-id="f196a-241">Note that the exception is included in the log message:</span></span>

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

## <a name="loggermessagedefinescope"></a><span data-ttu-id="f196a-242">LoggerMessage. DefineScope</span><span class="sxs-lookup"><span data-stu-id="f196a-242">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="f196a-243">[DefineScope (String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) , <xref:System.Func%601> [günlük kapsamı](xref:fundamentals/logging/index#log-scopes)tanımlamak için bir temsilci oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f196a-243">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="f196a-244"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>Aşırı Yüklemeler, adlandırılmış biçim dizesine (şablon) üç tür parametrenin geçirilmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="f196a-244"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="f196a-245">Yönteminde olduğu gibi <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> , yöntemi için girilen dize, ilişkili <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> dize değil, bir şablondur.</span><span class="sxs-lookup"><span data-stu-id="f196a-245">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="f196a-246">Yer tutucular, türlerin belirtilme sırasına göre doldurulur.</span><span class="sxs-lookup"><span data-stu-id="f196a-246">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="f196a-247">Şablondaki yer tutucu adları, şablonlar genelinde açıklayıcı ve tutarlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f196a-247">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="f196a-248">Bunlar, yapılandırılmış günlük verileri içinde özellik adı olarak görev yapar.</span><span class="sxs-lookup"><span data-stu-id="f196a-248">They serve as property names within structured log data.</span></span> <span data-ttu-id="f196a-249">Yer tutucu adları için [Pascal büyük harfleri](/dotnet/standard/design-guidelines/capitalization-conventions) öneririz.</span><span class="sxs-lookup"><span data-stu-id="f196a-249">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="f196a-250">Örneğin,, `{Count}` `{FirstName}` .</span><span class="sxs-lookup"><span data-stu-id="f196a-250">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="f196a-251">Yöntemini kullanarak bir dizi günlük mesajı için uygulanacak [günlük kapsamını](xref:fundamentals/logging/index#log-scopes) tanımlayın <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> .</span><span class="sxs-lookup"><span data-stu-id="f196a-251">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="f196a-252">Örnek uygulamanın, veritabanındaki tüm teklifleri silmek için **Tümünü Temizle** düğmesi vardır.</span><span class="sxs-lookup"><span data-stu-id="f196a-252">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="f196a-253">Tırnak işaretleri tek seferde kaldırılarak silinir.</span><span class="sxs-lookup"><span data-stu-id="f196a-253">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="f196a-254">Bir teklifin her silindiği her seferinde `QuoteDeleted` yöntemi günlükçü üzerinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="f196a-254">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="f196a-255">Bu günlük iletilerine bir günlük kapsamı eklenir.</span><span class="sxs-lookup"><span data-stu-id="f196a-255">A log scope is added to these log messages.</span></span>

<span data-ttu-id="f196a-256">`IncludeScopes` *AppSettings. JSON*konsol günlükçü bölümünde etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="f196a-256">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="f196a-257">Bir günlük kapsamı oluşturmak için, kapsam için bir temsilci tutacak bir alan ekleyin <xref:System.Func%601> .</span><span class="sxs-lookup"><span data-stu-id="f196a-257">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="f196a-258">Örnek uygulama `_allQuotesDeletedScope` (*Iç/LoggerExtensions. cs*) adlı bir alan oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f196a-258">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="f196a-259"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>Temsilciyi oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="f196a-259">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="f196a-260">Temsilci çağrıldığında Şablon bağımsız değişkenleri olarak kullanmak için en fazla üç tür belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="f196a-260">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="f196a-261">Örnek uygulama, silinen tekliflerin sayısını (bir tür) içeren bir ileti şablonu kullanır `int` :</span><span class="sxs-lookup"><span data-stu-id="f196a-261">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="f196a-262">Günlük iletisi için bir statik genişletme yöntemi sağlayın.</span><span class="sxs-lookup"><span data-stu-id="f196a-262">Provide a static extension method for the log message.</span></span> <span data-ttu-id="f196a-263">İleti şablonunda görünen adlandırılmış özellikler için herhangi bir tür parametresi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f196a-263">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="f196a-264">Örnek uygulama, `count` silmek ve geri döndürmek için tırnak içine alır `_allQuotesDeletedScope` :</span><span class="sxs-lookup"><span data-stu-id="f196a-264">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="f196a-265">Kapsam, oturum açma uzantısı çağrılarını bir [using](/dotnet/csharp/language-reference/keywords/using-statement) bloğunda sarmalar:</span><span class="sxs-lookup"><span data-stu-id="f196a-265">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="f196a-266">Uygulamanın konsol çıkışında günlük iletilerini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="f196a-266">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="f196a-267">Aşağıdaki sonuç, günlük kapsamı iletisi dahil olmak üzere silinen üç tırnak gösterir:</span><span class="sxs-lookup"><span data-stu-id="f196a-267">The following result shows three quotes deleted with the log scope message included:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="f196a-268">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f196a-268">Additional resources</span></span>

* [<span data-ttu-id="f196a-269">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="f196a-269">Logging</span></span>](xref:fundamentals/logging/index)
