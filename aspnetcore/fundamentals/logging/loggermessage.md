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
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a><span data-ttu-id="32787-103">ASP.NET Core'da LoggerMessage ile yüksek performanslı günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="32787-103">High-performance logging with LoggerMessage in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="32787-104"><xref:Microsoft.Extensions.Logging.LoggerMessage>özellikler, [logger uzantı yöntemlerine](xref:Microsoft.Extensions.Logging.LoggerExtensions)göre daha az nesne ayırması ve azaltılmış hesaplama ek <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>yükü gerektiren önbelleğe alınan temsilciler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="32787-104"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="32787-105">Yüksek performanslı günlüğe kaydetme <xref:Microsoft.Extensions.Logging.LoggerMessage> senaryoları için deseni kullanın.</span><span class="sxs-lookup"><span data-stu-id="32787-105">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="32787-106"><xref:Microsoft.Extensions.Logging.LoggerMessage>Logger uzantı yöntemlerine göre aşağıdaki performans avantajlarını sağlar:</span><span class="sxs-lookup"><span data-stu-id="32787-106"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="32787-107">Logger uzantı yöntemleri , `int` `object`" içine "kutulama" (dönüştürme) değer türleri, gerektirir .</span><span class="sxs-lookup"><span data-stu-id="32787-107">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="32787-108">Desen, <xref:Microsoft.Extensions.Logging.LoggerMessage> güçlü bir şekilde <xref:System.Action> yazılan parametrelere sahip statik alanlar ve uzatma yöntemleri kullanarak kutulamayı önler.</span><span class="sxs-lookup"><span data-stu-id="32787-108">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="32787-109">Logger uzantı yöntemleri, günlük iletisi her yazıldığında ileti şablonu (adlandırılmış biçim dizesi) ayrıştırmalıdır.</span><span class="sxs-lookup"><span data-stu-id="32787-109">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="32787-110"><xref:Microsoft.Extensions.Logging.LoggerMessage>ileti tanımlandığında şablonu yalnızca bir kez ayrıştırma gerektirir.</span><span class="sxs-lookup"><span data-stu-id="32787-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="32787-111">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="32787-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="32787-112">Örnek uygulama, <xref:Microsoft.Extensions.Logging.LoggerMessage> temel bir teklif izleme sistemine sahip özellikleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="32787-112">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="32787-113">Uygulama, bellek içi bir veritabanı kullanarak tırnak eklemeve silme.</span><span class="sxs-lookup"><span data-stu-id="32787-113">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="32787-114">Bu işlemler gerçekleştiğinde, günlük iletileri <xref:Microsoft.Extensions.Logging.LoggerMessage> desen kullanılarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="32787-114">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="32787-115">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="32787-115">LoggerMessage.Define</span></span>

<span data-ttu-id="32787-116">[Define (LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) bir iletiyi günlüğe kaydetmek için bir <xref:System.Action> temsilci oluşturur.</span><span class="sxs-lookup"><span data-stu-id="32787-116">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="32787-117"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>aşırı yüklemeler, altı tür parametresinin adlandırılmış biçim dizesine (şablon) geçirilmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="32787-117"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="32787-118"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> Yönteme sağlanan dize, enterpolasyonlu bir dize değil şablondur.</span><span class="sxs-lookup"><span data-stu-id="32787-118">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="32787-119">Yer tutucular, türlerin belirtildiği sırayla doldurulur.</span><span class="sxs-lookup"><span data-stu-id="32787-119">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="32787-120">Şablondaki yer tutucu adları şablonlar arasında açıklayıcı ve tutarlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="32787-120">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="32787-121">Yapılandırılmış günlük verileri içinde özellik adları olarak hizmet verirler.</span><span class="sxs-lookup"><span data-stu-id="32787-121">They serve as property names within structured log data.</span></span> <span data-ttu-id="32787-122">Yer tutucu adları için [Pascal kaplaması](/dotnet/standard/design-guidelines/capitalization-conventions) öneririz.</span><span class="sxs-lookup"><span data-stu-id="32787-122">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="32787-123">Örneğin, `{Count}`. `{FirstName}`</span><span class="sxs-lookup"><span data-stu-id="32787-123">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="32787-124">Her günlük iletisi <xref:System.Action> [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*)tarafından oluşturulan statik bir alanda tutulan bir .</span><span class="sxs-lookup"><span data-stu-id="32787-124">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="32787-125">Örneğin, örnek uygulama Dizin sayfası için bir GET isteği için bir günlük iletisi açıklamak için bir alan oluşturur (*İç / LoggerExtensions.cs):*</span><span class="sxs-lookup"><span data-stu-id="32787-125">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="32787-126">Için <xref:System.Action>, belirtin:</span><span class="sxs-lookup"><span data-stu-id="32787-126">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="32787-127">Günlük düzeyi.</span><span class="sxs-lookup"><span data-stu-id="32787-127">The log level.</span></span>
* <span data-ttu-id="32787-128">Statik uzantı yönteminin adı<xref:Microsoft.Extensions.Logging.EventId>ile benzersiz bir olay tanımlayıcısı .</span><span class="sxs-lookup"><span data-stu-id="32787-128">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="32787-129">İleti şablonu (biçim dizesi adlı).</span><span class="sxs-lookup"><span data-stu-id="32787-129">The message template (named format string).</span></span> 

<span data-ttu-id="32787-130">Örnek uygulamanın Dizin sayfası için bir istek şu şekilde ayarlar:</span><span class="sxs-lookup"><span data-stu-id="32787-130">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="32787-131">Günlük düzeyi `Information`' ye .</span><span class="sxs-lookup"><span data-stu-id="32787-131">Log level to `Information`.</span></span>
* <span data-ttu-id="32787-132">Yöntemin `1` adı ile olay kimliği. `IndexPageRequested`</span><span class="sxs-lookup"><span data-stu-id="32787-132">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="32787-133">İleti şablonu (adlandırılmış biçim dizesi) bir dize.</span><span class="sxs-lookup"><span data-stu-id="32787-133">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="32787-134">Yapılandırılmış günlük depoları, etkinlik kimliğiyle birlikte verildiğinde, günlük günlüğe kaydetmeyi zenginleştirmek için etkinlik adını kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="32787-134">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="32787-135">Örneğin, [Serilog](https://github.com/serilog/serilog-extensions-logging) olay adını kullanır.</span><span class="sxs-lookup"><span data-stu-id="32787-135">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="32787-136">Güçlü <xref:System.Action> bir şekilde yazılan bir uzantı yöntemi yle çağrılır.</span><span class="sxs-lookup"><span data-stu-id="32787-136">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="32787-137">Yöntem, `IndexPageRequested` örnek uygulamada bir Dizin sayfası GET isteği için bir ileti kaydeder:</span><span class="sxs-lookup"><span data-stu-id="32787-137">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="32787-138">`IndexPageRequested``OnGetAsync` *Pages/Index.cshtml.cs'deki*yöntemde logger'a denir:</span><span class="sxs-lookup"><span data-stu-id="32787-138">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="32787-139">Uygulamanın konsol çıktısını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="32787-139">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="32787-140">Parametreleri bir günlük iletisine geçirmek için statik alanı oluştururken en fazla altı tür tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="32787-140">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="32787-141">Örnek uygulama, alan için bir tür tanımlayarak `string` bir <xref:System.Action> teklif eklerken bir dize kaydeder:</span><span class="sxs-lookup"><span data-stu-id="32787-141">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="32787-142">Temsilcinin günlük iletisi şablonu, yer tutucu değerlerini sağlanan türlerden alır.</span><span class="sxs-lookup"><span data-stu-id="32787-142">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="32787-143">Örnek uygulama, teklif parametresinin bulunduğu bir teklif eklemek `string`için bir temsilci tanımlar:</span><span class="sxs-lookup"><span data-stu-id="32787-143">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="32787-144">Teklif eklemek için statik uzantı yöntemi, `QuoteAdded`teklif bağımsız değişkeni <xref:System.Action> değerini alır ve temsilciye aktarIr:</span><span class="sxs-lookup"><span data-stu-id="32787-144">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="32787-145">Dizin sayfasının sayfa modelinde *(Pages/Index.cshtml.cs),* `QuoteAdded` iletiyi günlüğe kaydetmek için çağrılır:</span><span class="sxs-lookup"><span data-stu-id="32787-145">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="32787-146">Uygulamanın konsol çıktısını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="32787-146">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="32787-147">Örnek uygulama, teklif silme için bir [deneme&ndash;yakalama](/dotnet/csharp/language-reference/keywords/try-catch) deseni uygular.</span><span class="sxs-lookup"><span data-stu-id="32787-147">The sample app implements a [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="32787-148">Başarılı bir silme işlemi için bir bilgilendirme iletisi günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="32787-148">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="32787-149">Bir özel durum atıldığında bir silme işlemi için bir hata iletisi günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="32787-149">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="32787-150">Başarısız silme işleminin günlük iletisi özel durum yığını izleme *(Internal/LoggerExtensions.cs):*</span><span class="sxs-lookup"><span data-stu-id="32787-150">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="32787-151">Özel durum temsilciye nasıl geçirildiğini not `QuoteDeleteFailed`edin:</span><span class="sxs-lookup"><span data-stu-id="32787-151">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="32787-152">Dizin sayfasının sayfa modelinde, başarılı bir teklif `QuoteDeleted` silme, logger'daki yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="32787-152">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="32787-153">Bir teklif silinme için bulunmazsa, <xref:System.ArgumentNullException> bir atılır.</span><span class="sxs-lookup"><span data-stu-id="32787-153">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="32787-154">Özel durum [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) deyimi tarafından sıkışıp kalır `QuoteDeleteFailed` ve [catch](/dotnet/csharp/language-reference/keywords/try-catch) bloğundaki logger'daki yöntemi arayarak kaydedilir (*Sayfalar/Index.cshtml.cs):*</span><span class="sxs-lookup"><span data-stu-id="32787-154">The exception is trapped by the [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

<span data-ttu-id="32787-155">Bir teklif başarıyla silindiğinde, uygulamanın konsol çıktısını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="32787-155">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="32787-156">Teklif silme başarısız olduğunda, uygulamanın konsol çıktısını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="32787-156">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="32787-157">Özel durum günlük iletisinde yer olduğunu unutmayın:</span><span class="sxs-lookup"><span data-stu-id="32787-157">Note that the exception is included in the log message:</span></span>

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

## <a name="loggermessagedefinescope"></a><span data-ttu-id="32787-158">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="32787-158">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="32787-159">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) günlük <xref:System.Func%601> [kapsamını](xref:fundamentals/logging/index#log-scopes)tanımlamak için bir temsilci oluşturur.</span><span class="sxs-lookup"><span data-stu-id="32787-159">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="32787-160"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>aşırı yüklemeler, üç tür parametresinin adlandırılmış biçim dizesine (şablon) geçirilmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="32787-160"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="32787-161">Yöntemde <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> olduğu gibi, <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> yönteme sağlanan dize bir şablondur, enterpolasyonlu bir dize değildir.</span><span class="sxs-lookup"><span data-stu-id="32787-161">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="32787-162">Yer tutucular, türlerin belirtildiği sırayla doldurulur.</span><span class="sxs-lookup"><span data-stu-id="32787-162">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="32787-163">Şablondaki yer tutucu adları şablonlar arasında açıklayıcı ve tutarlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="32787-163">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="32787-164">Yapılandırılmış günlük verileri içinde özellik adları olarak hizmet verirler.</span><span class="sxs-lookup"><span data-stu-id="32787-164">They serve as property names within structured log data.</span></span> <span data-ttu-id="32787-165">Yer tutucu adları için [Pascal kaplaması](/dotnet/standard/design-guidelines/capitalization-conventions) öneririz.</span><span class="sxs-lookup"><span data-stu-id="32787-165">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="32787-166">Örneğin, `{Count}`. `{FirstName}`</span><span class="sxs-lookup"><span data-stu-id="32787-166">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="32787-167">Yöntemi [log scope](xref:fundamentals/logging/index#log-scopes) kullanarak bir dizi günlük iletisine uygulamak <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> için günlük kapsamı tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="32787-167">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="32787-168">Örnek uygulama, veritabanındaki tüm teklifleri silen tüm tüm bunları **temizle** düğmesine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="32787-168">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="32787-169">Tırnak işaretleri teker teker kaldırılarak silinir.</span><span class="sxs-lookup"><span data-stu-id="32787-169">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="32787-170">Bir teklif her silinir, `QuoteDeleted` yöntem logger çağrılır.</span><span class="sxs-lookup"><span data-stu-id="32787-170">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="32787-171">Bu günlük iletilerine günlük kapsamı eklenir.</span><span class="sxs-lookup"><span data-stu-id="32787-171">A log scope is added to these log messages.</span></span>

<span data-ttu-id="32787-172">`IncludeScopes` *appsettings.json*konsol logger bölümünde etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="32787-172">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="32787-173">Günlük kapsamı oluşturmak için, kapsam için <xref:System.Func%601> bir temsilci tutmak için bir alan ekleyin.</span><span class="sxs-lookup"><span data-stu-id="32787-173">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="32787-174">Örnek uygulama ( `_allQuotesDeletedScope` *Dahili/LoggerExtensions.cs)* adlı bir alan oluşturur:</span><span class="sxs-lookup"><span data-stu-id="32787-174">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="32787-175">Temsilcioluşturmak için kullanın. <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*></span><span class="sxs-lookup"><span data-stu-id="32787-175">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="32787-176">Temsilci çağrıldığınızda şablon bağımsız değişkenleri olarak kullanılmak üzere en fazla üç tür belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="32787-176">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="32787-177">Örnek uygulama, silinen tırnak sayısı (bir tür) içeren bir `int` ileti şablonu kullanır:</span><span class="sxs-lookup"><span data-stu-id="32787-177">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="32787-178">Günlük iletisi için statik bir uzantı yöntemi sağlayın.</span><span class="sxs-lookup"><span data-stu-id="32787-178">Provide a static extension method for the log message.</span></span> <span data-ttu-id="32787-179">İleti şablonunda görünen adlandırılmış özellikler için herhangi bir tür parametresi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="32787-179">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="32787-180">Örnek uygulama silmek `count` için tırnak bir `_allQuotesDeletedScope`alır ve döner:</span><span class="sxs-lookup"><span data-stu-id="32787-180">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="32787-181">Kapsam, günlük uzantısı çağrılarını [kullanarak](/dotnet/csharp/language-reference/keywords/using-statement) bir blokta sarar:</span><span class="sxs-lookup"><span data-stu-id="32787-181">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="32787-182">Uygulamanın konsol çıkışındaki günlük iletilerini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="32787-182">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="32787-183">Aşağıdaki sonuç, günlük kapsamı iletisi dahil üç tırnak gösterir:</span><span class="sxs-lookup"><span data-stu-id="32787-183">The following result shows three quotes deleted with the log scope message included:</span></span>

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

<span data-ttu-id="32787-184"><xref:Microsoft.Extensions.Logging.LoggerMessage>özellikler, [logger uzantı yöntemlerine](xref:Microsoft.Extensions.Logging.LoggerExtensions)göre daha az nesne ayırması ve azaltılmış hesaplama ek <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>yükü gerektiren önbelleğe alınan temsilciler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="32787-184"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="32787-185">Yüksek performanslı günlüğe kaydetme <xref:Microsoft.Extensions.Logging.LoggerMessage> senaryoları için deseni kullanın.</span><span class="sxs-lookup"><span data-stu-id="32787-185">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="32787-186"><xref:Microsoft.Extensions.Logging.LoggerMessage>Logger uzantı yöntemlerine göre aşağıdaki performans avantajlarını sağlar:</span><span class="sxs-lookup"><span data-stu-id="32787-186"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="32787-187">Logger uzantı yöntemleri , `int` `object`" içine "kutulama" (dönüştürme) değer türleri, gerektirir .</span><span class="sxs-lookup"><span data-stu-id="32787-187">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="32787-188">Desen, <xref:Microsoft.Extensions.Logging.LoggerMessage> güçlü bir şekilde <xref:System.Action> yazılan parametrelere sahip statik alanlar ve uzatma yöntemleri kullanarak kutulamayı önler.</span><span class="sxs-lookup"><span data-stu-id="32787-188">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="32787-189">Logger uzantı yöntemleri, günlük iletisi her yazıldığında ileti şablonu (adlandırılmış biçim dizesi) ayrıştırmalıdır.</span><span class="sxs-lookup"><span data-stu-id="32787-189">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="32787-190"><xref:Microsoft.Extensions.Logging.LoggerMessage>ileti tanımlandığında şablonu yalnızca bir kez ayrıştırma gerektirir.</span><span class="sxs-lookup"><span data-stu-id="32787-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="32787-191">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="32787-191">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="32787-192">Örnek uygulama, <xref:Microsoft.Extensions.Logging.LoggerMessage> temel bir teklif izleme sistemine sahip özellikleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="32787-192">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="32787-193">Uygulama, bellek içi bir veritabanı kullanarak tırnak eklemeve silme.</span><span class="sxs-lookup"><span data-stu-id="32787-193">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="32787-194">Bu işlemler gerçekleştiğinde, günlük iletileri <xref:Microsoft.Extensions.Logging.LoggerMessage> desen kullanılarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="32787-194">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="32787-195">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="32787-195">LoggerMessage.Define</span></span>

<span data-ttu-id="32787-196">[Define (LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) bir iletiyi günlüğe kaydetmek için bir <xref:System.Action> temsilci oluşturur.</span><span class="sxs-lookup"><span data-stu-id="32787-196">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="32787-197"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>aşırı yüklemeler, altı tür parametresinin adlandırılmış biçim dizesine (şablon) geçirilmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="32787-197"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="32787-198"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> Yönteme sağlanan dize, enterpolasyonlu bir dize değil şablondur.</span><span class="sxs-lookup"><span data-stu-id="32787-198">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="32787-199">Yer tutucular, türlerin belirtildiği sırayla doldurulur.</span><span class="sxs-lookup"><span data-stu-id="32787-199">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="32787-200">Şablondaki yer tutucu adları şablonlar arasında açıklayıcı ve tutarlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="32787-200">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="32787-201">Yapılandırılmış günlük verileri içinde özellik adları olarak hizmet verirler.</span><span class="sxs-lookup"><span data-stu-id="32787-201">They serve as property names within structured log data.</span></span> <span data-ttu-id="32787-202">Yer tutucu adları için [Pascal kaplaması](/dotnet/standard/design-guidelines/capitalization-conventions) öneririz.</span><span class="sxs-lookup"><span data-stu-id="32787-202">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="32787-203">Örneğin, `{Count}`. `{FirstName}`</span><span class="sxs-lookup"><span data-stu-id="32787-203">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="32787-204">Her günlük iletisi <xref:System.Action> [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*)tarafından oluşturulan statik bir alanda tutulan bir .</span><span class="sxs-lookup"><span data-stu-id="32787-204">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="32787-205">Örneğin, örnek uygulama Dizin sayfası için bir GET isteği için bir günlük iletisi açıklamak için bir alan oluşturur (*İç / LoggerExtensions.cs):*</span><span class="sxs-lookup"><span data-stu-id="32787-205">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="32787-206">Için <xref:System.Action>, belirtin:</span><span class="sxs-lookup"><span data-stu-id="32787-206">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="32787-207">Günlük düzeyi.</span><span class="sxs-lookup"><span data-stu-id="32787-207">The log level.</span></span>
* <span data-ttu-id="32787-208">Statik uzantı yönteminin adı<xref:Microsoft.Extensions.Logging.EventId>ile benzersiz bir olay tanımlayıcısı .</span><span class="sxs-lookup"><span data-stu-id="32787-208">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="32787-209">İleti şablonu (biçim dizesi adlı).</span><span class="sxs-lookup"><span data-stu-id="32787-209">The message template (named format string).</span></span> 

<span data-ttu-id="32787-210">Örnek uygulamanın Dizin sayfası için bir istek şu şekilde ayarlar:</span><span class="sxs-lookup"><span data-stu-id="32787-210">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="32787-211">Günlük düzeyi `Information`' ye .</span><span class="sxs-lookup"><span data-stu-id="32787-211">Log level to `Information`.</span></span>
* <span data-ttu-id="32787-212">Yöntemin `1` adı ile olay kimliği. `IndexPageRequested`</span><span class="sxs-lookup"><span data-stu-id="32787-212">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="32787-213">İleti şablonu (adlandırılmış biçim dizesi) bir dize.</span><span class="sxs-lookup"><span data-stu-id="32787-213">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="32787-214">Yapılandırılmış günlük depoları, etkinlik kimliğiyle birlikte verildiğinde, günlük günlüğe kaydetmeyi zenginleştirmek için etkinlik adını kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="32787-214">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="32787-215">Örneğin, [Serilog](https://github.com/serilog/serilog-extensions-logging) olay adını kullanır.</span><span class="sxs-lookup"><span data-stu-id="32787-215">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="32787-216">Güçlü <xref:System.Action> bir şekilde yazılan bir uzantı yöntemi yle çağrılır.</span><span class="sxs-lookup"><span data-stu-id="32787-216">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="32787-217">Yöntem, `IndexPageRequested` örnek uygulamada bir Dizin sayfası GET isteği için bir ileti kaydeder:</span><span class="sxs-lookup"><span data-stu-id="32787-217">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="32787-218">`IndexPageRequested``OnGetAsync` *Pages/Index.cshtml.cs'deki*yöntemde logger'a denir:</span><span class="sxs-lookup"><span data-stu-id="32787-218">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="32787-219">Uygulamanın konsol çıktısını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="32787-219">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="32787-220">Parametreleri bir günlük iletisine geçirmek için statik alanı oluştururken en fazla altı tür tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="32787-220">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="32787-221">Örnek uygulama, alan için bir tür tanımlayarak `string` bir <xref:System.Action> teklif eklerken bir dize kaydeder:</span><span class="sxs-lookup"><span data-stu-id="32787-221">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="32787-222">Temsilcinin günlük iletisi şablonu, yer tutucu değerlerini sağlanan türlerden alır.</span><span class="sxs-lookup"><span data-stu-id="32787-222">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="32787-223">Örnek uygulama, teklif parametresinin bulunduğu bir teklif eklemek `string`için bir temsilci tanımlar:</span><span class="sxs-lookup"><span data-stu-id="32787-223">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="32787-224">Teklif eklemek için statik uzantı yöntemi, `QuoteAdded`teklif bağımsız değişkeni <xref:System.Action> değerini alır ve temsilciye aktarIr:</span><span class="sxs-lookup"><span data-stu-id="32787-224">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="32787-225">Dizin sayfasının sayfa modelinde *(Pages/Index.cshtml.cs),* `QuoteAdded` iletiyi günlüğe kaydetmek için çağrılır:</span><span class="sxs-lookup"><span data-stu-id="32787-225">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="32787-226">Uygulamanın konsol çıktısını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="32787-226">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="32787-227">Örnek uygulama, teklif silme için bir [deneme&ndash;yakalama](/dotnet/csharp/language-reference/keywords/try-catch) deseni uygular.</span><span class="sxs-lookup"><span data-stu-id="32787-227">The sample app implements a [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="32787-228">Başarılı bir silme işlemi için bir bilgilendirme iletisi günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="32787-228">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="32787-229">Bir özel durum atıldığında bir silme işlemi için bir hata iletisi günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="32787-229">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="32787-230">Başarısız silme işleminin günlük iletisi özel durum yığını izleme *(Internal/LoggerExtensions.cs):*</span><span class="sxs-lookup"><span data-stu-id="32787-230">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="32787-231">Özel durum temsilciye nasıl geçirildiğini not `QuoteDeleteFailed`edin:</span><span class="sxs-lookup"><span data-stu-id="32787-231">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="32787-232">Dizin sayfasının sayfa modelinde, başarılı bir teklif `QuoteDeleted` silme, logger'daki yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="32787-232">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="32787-233">Bir teklif silinme için bulunmazsa, <xref:System.ArgumentNullException> bir atılır.</span><span class="sxs-lookup"><span data-stu-id="32787-233">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="32787-234">Özel durum [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) deyimi tarafından sıkışıp kalır `QuoteDeleteFailed` ve [catch](/dotnet/csharp/language-reference/keywords/try-catch) bloğundaki logger'daki yöntemi arayarak kaydedilir (*Sayfalar/Index.cshtml.cs):*</span><span class="sxs-lookup"><span data-stu-id="32787-234">The exception is trapped by the [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

<span data-ttu-id="32787-235">Bir teklif başarıyla silindiğinde, uygulamanın konsol çıktısını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="32787-235">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="32787-236">Teklif silme başarısız olduğunda, uygulamanın konsol çıktısını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="32787-236">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="32787-237">Özel durum günlük iletisinde yer olduğunu unutmayın:</span><span class="sxs-lookup"><span data-stu-id="32787-237">Note that the exception is included in the log message:</span></span>

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

## <a name="loggermessagedefinescope"></a><span data-ttu-id="32787-238">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="32787-238">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="32787-239">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) günlük <xref:System.Func%601> [kapsamını](xref:fundamentals/logging/index#log-scopes)tanımlamak için bir temsilci oluşturur.</span><span class="sxs-lookup"><span data-stu-id="32787-239">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="32787-240"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>aşırı yüklemeler, üç tür parametresinin adlandırılmış biçim dizesine (şablon) geçirilmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="32787-240"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="32787-241">Yöntemde <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> olduğu gibi, <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> yönteme sağlanan dize bir şablondur, enterpolasyonlu bir dize değildir.</span><span class="sxs-lookup"><span data-stu-id="32787-241">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="32787-242">Yer tutucular, türlerin belirtildiği sırayla doldurulur.</span><span class="sxs-lookup"><span data-stu-id="32787-242">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="32787-243">Şablondaki yer tutucu adları şablonlar arasında açıklayıcı ve tutarlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="32787-243">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="32787-244">Yapılandırılmış günlük verileri içinde özellik adları olarak hizmet verirler.</span><span class="sxs-lookup"><span data-stu-id="32787-244">They serve as property names within structured log data.</span></span> <span data-ttu-id="32787-245">Yer tutucu adları için [Pascal kaplaması](/dotnet/standard/design-guidelines/capitalization-conventions) öneririz.</span><span class="sxs-lookup"><span data-stu-id="32787-245">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="32787-246">Örneğin, `{Count}`. `{FirstName}`</span><span class="sxs-lookup"><span data-stu-id="32787-246">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="32787-247">Yöntemi [log scope](xref:fundamentals/logging/index#log-scopes) kullanarak bir dizi günlük iletisine uygulamak <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> için günlük kapsamı tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="32787-247">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="32787-248">Örnek uygulama, veritabanındaki tüm teklifleri silen tüm tüm bunları **temizle** düğmesine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="32787-248">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="32787-249">Tırnak işaretleri teker teker kaldırılarak silinir.</span><span class="sxs-lookup"><span data-stu-id="32787-249">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="32787-250">Bir teklif her silinir, `QuoteDeleted` yöntem logger çağrılır.</span><span class="sxs-lookup"><span data-stu-id="32787-250">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="32787-251">Bu günlük iletilerine günlük kapsamı eklenir.</span><span class="sxs-lookup"><span data-stu-id="32787-251">A log scope is added to these log messages.</span></span>

<span data-ttu-id="32787-252">`IncludeScopes` *appsettings.json*konsol logger bölümünde etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="32787-252">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="32787-253">Günlük kapsamı oluşturmak için, kapsam için <xref:System.Func%601> bir temsilci tutmak için bir alan ekleyin.</span><span class="sxs-lookup"><span data-stu-id="32787-253">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="32787-254">Örnek uygulama ( `_allQuotesDeletedScope` *Dahili/LoggerExtensions.cs)* adlı bir alan oluşturur:</span><span class="sxs-lookup"><span data-stu-id="32787-254">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="32787-255">Temsilcioluşturmak için kullanın. <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*></span><span class="sxs-lookup"><span data-stu-id="32787-255">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="32787-256">Temsilci çağrıldığınızda şablon bağımsız değişkenleri olarak kullanılmak üzere en fazla üç tür belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="32787-256">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="32787-257">Örnek uygulama, silinen tırnak sayısı (bir tür) içeren bir `int` ileti şablonu kullanır:</span><span class="sxs-lookup"><span data-stu-id="32787-257">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="32787-258">Günlük iletisi için statik bir uzantı yöntemi sağlayın.</span><span class="sxs-lookup"><span data-stu-id="32787-258">Provide a static extension method for the log message.</span></span> <span data-ttu-id="32787-259">İleti şablonunda görünen adlandırılmış özellikler için herhangi bir tür parametresi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="32787-259">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="32787-260">Örnek uygulama silmek `count` için tırnak bir `_allQuotesDeletedScope`alır ve döner:</span><span class="sxs-lookup"><span data-stu-id="32787-260">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="32787-261">Kapsam, günlük uzantısı çağrılarını [kullanarak](/dotnet/csharp/language-reference/keywords/using-statement) bir blokta sarar:</span><span class="sxs-lookup"><span data-stu-id="32787-261">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="32787-262">Uygulamanın konsol çıkışındaki günlük iletilerini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="32787-262">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="32787-263">Aşağıdaki sonuç, günlük kapsamı iletisi dahil üç tırnak gösterir:</span><span class="sxs-lookup"><span data-stu-id="32787-263">The following result shows three quotes deleted with the log scope message included:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="32787-264">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="32787-264">Additional resources</span></span>

* [<span data-ttu-id="32787-265">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="32787-265">Logging</span></span>](xref:fundamentals/logging/index)
