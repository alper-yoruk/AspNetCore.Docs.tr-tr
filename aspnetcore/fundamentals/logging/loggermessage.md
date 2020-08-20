---
title: ASP.NET Core 'de LoggerMessage ile yüksek performanslı günlüğe kaydetme
author: rick-anderson
description: Yüksek performanslı günlük senaryolarında daha az sayıda nesne ayırması gerektiren önbelleğe alınabilir temsilciler oluşturmak için LoggerMessage kullanmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2019
no-loc:
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
uid: fundamentals/logging/loggermessage
ms.openlocfilehash: 9bd31682e9fb816110e814790a93ab53b1454cc9
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634026"
---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a><span data-ttu-id="0babb-103">ASP.NET Core 'de LoggerMessage ile yüksek performanslı günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="0babb-103">High-performance logging with LoggerMessage in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0babb-104"><xref:Microsoft.Extensions.Logging.LoggerMessage> özellikler, ve gibi [günlükçü uzantısı yöntemlerine](xref:Microsoft.Extensions.Logging.LoggerExtensions)kıyasla daha az nesne ayırma ve daha düşük hesaplama yükü gerektiren önbelleğe alınabilir temsilciler oluşturur <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*> .</span><span class="sxs-lookup"><span data-stu-id="0babb-104"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="0babb-105">Yüksek performanslı günlük senaryoları için, bu kalıbı kullanın <xref:Microsoft.Extensions.Logging.LoggerMessage> .</span><span class="sxs-lookup"><span data-stu-id="0babb-105">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="0babb-106"><xref:Microsoft.Extensions.Logging.LoggerMessage> Günlükçü uzantı yöntemlerine göre aşağıdaki performans avantajlarını sağlar:</span><span class="sxs-lookup"><span data-stu-id="0babb-106"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="0babb-107">Günlükçü uzantı yöntemleri, gibi "kutulama" (dönüştürme) değer türlerini gerektirir `int` `object` .</span><span class="sxs-lookup"><span data-stu-id="0babb-107">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="0babb-108">Bu <xref:Microsoft.Extensions.Logging.LoggerMessage> model, <xref:System.Action> kesin türü belirtilmiş parametrelerle statik alanlar ve genişletme yöntemleri kullanarak kutulamayı önler.</span><span class="sxs-lookup"><span data-stu-id="0babb-108">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="0babb-109">Günlükçü uzantısı yöntemlerinin her bir günlük iletisi yazıldığında ileti şablonunu (biçim dizesi olarak adlandırılır) ayrıştırması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0babb-109">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="0babb-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> yalnızca ileti tanımlandığında bir şablonu ayrıştırmayı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="0babb-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="0babb-111">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0babb-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0babb-112">Örnek uygulama, <xref:Microsoft.Extensions.Logging.LoggerMessage> temel bir Quote izleme sistemine sahip özellikleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="0babb-112">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="0babb-113">Uygulama, bellek içi veritabanı kullanarak tırnak ekler ve siler.</span><span class="sxs-lookup"><span data-stu-id="0babb-113">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="0babb-114">Bu işlemler gerçekleştiğinde, günlük iletileri model kullanılarak oluşturulur <xref:Microsoft.Extensions.Logging.LoggerMessage> .</span><span class="sxs-lookup"><span data-stu-id="0babb-114">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="0babb-115">LoggerMessage. define</span><span class="sxs-lookup"><span data-stu-id="0babb-115">LoggerMessage.Define</span></span>

<span data-ttu-id="0babb-116">[Define (LogLevel, EventID, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) <xref:System.Action> bir iletiyi günlüğe kaydetmek için bir temsilci oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0babb-116">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="0babb-117"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> Aşırı Yüklemeler, adlandırılmış biçim dizesine (şablon) altı tür parametre geçişine izin verir.</span><span class="sxs-lookup"><span data-stu-id="0babb-117"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="0babb-118">Yöntemine girilen dize, <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> enterpolasyonlu bir dize değil, bir şablondur.</span><span class="sxs-lookup"><span data-stu-id="0babb-118">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="0babb-119">Yer tutucular, türlerin belirtilme sırasına göre doldurulur.</span><span class="sxs-lookup"><span data-stu-id="0babb-119">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="0babb-120">Şablondaki yer tutucu adları, şablonlar genelinde açıklayıcı ve tutarlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0babb-120">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="0babb-121">Bunlar, yapılandırılmış günlük verileri içinde özellik adı olarak görev yapar.</span><span class="sxs-lookup"><span data-stu-id="0babb-121">They serve as property names within structured log data.</span></span> <span data-ttu-id="0babb-122">Yer tutucu adları için [Pascal büyük harfleri](/dotnet/standard/design-guidelines/capitalization-conventions) öneririz.</span><span class="sxs-lookup"><span data-stu-id="0babb-122">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="0babb-123">Örneğin,, `{Count}` `{FirstName}` .</span><span class="sxs-lookup"><span data-stu-id="0babb-123">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="0babb-124">Her günlük iletisi, <xref:System.Action> [Loggermessage. define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*)tarafından oluşturulan statik bir alanda tutulur.</span><span class="sxs-lookup"><span data-stu-id="0babb-124">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="0babb-125">Örneğin, örnek uygulama, Dizin sayfası (*iç/LoggerExtensions. cs*) IÇIN bir GET isteğinin günlük iletisini tanımlayacak bir alan oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0babb-125">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="0babb-126">İçin <xref:System.Action> şunu belirtin:</span><span class="sxs-lookup"><span data-stu-id="0babb-126">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="0babb-127">Günlük düzeyi.</span><span class="sxs-lookup"><span data-stu-id="0babb-127">The log level.</span></span>
* <span data-ttu-id="0babb-128"><xref:Microsoft.Extensions.Logging.EventId>Statik Uzantı yönteminin adı ile benzersiz bir olay tanımlayıcısı ().</span><span class="sxs-lookup"><span data-stu-id="0babb-128">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="0babb-129">İleti şablonu (biçim dizesi olarak adlandırılır).</span><span class="sxs-lookup"><span data-stu-id="0babb-129">The message template (named format string).</span></span> 

<span data-ttu-id="0babb-130">Örnek uygulamanın dizin sayfası için bir istek şunları ayarlar:</span><span class="sxs-lookup"><span data-stu-id="0babb-130">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="0babb-131">Günlük düzeyi `Information` .</span><span class="sxs-lookup"><span data-stu-id="0babb-131">Log level to `Information`.</span></span>
* <span data-ttu-id="0babb-132">`1`Yöntemin adı ile olay kimliği `IndexPageRequested` .</span><span class="sxs-lookup"><span data-stu-id="0babb-132">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="0babb-133">Bir dizeye ileti şablonu (biçim dizesi olarak adlandırılır).</span><span class="sxs-lookup"><span data-stu-id="0babb-133">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="0babb-134">Yapılandırılmış günlük depoları, olay kimliği ile birlikte verileri zenginleştirmek için sağlandığında olay adını kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="0babb-134">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="0babb-135">Örneğin, [Serilog](https://github.com/serilog/serilog-extensions-logging) olay adını kullanır.</span><span class="sxs-lookup"><span data-stu-id="0babb-135">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="0babb-136">, <xref:System.Action> Türü kesin belirlenmiş bir uzantı yöntemiyle çağrılır.</span><span class="sxs-lookup"><span data-stu-id="0babb-136">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="0babb-137">`IndexPageRequested`Yöntemi, örnek uygulamada bir dizin sayfası get isteği için bir ileti kaydeder:</span><span class="sxs-lookup"><span data-stu-id="0babb-137">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="0babb-138">`IndexPageRequested` , `OnGetAsync` *sayfa/dizin. cshtml. cs*içindeki yöntemdeki günlükçü üzerinde çağrılır:</span><span class="sxs-lookup"><span data-stu-id="0babb-138">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="0babb-139">Uygulamanın konsol çıkışını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="0babb-139">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="0babb-140">Parametreleri bir günlük iletisine geçirmek için, statik alanı oluştururken en fazla altı tür tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="0babb-140">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="0babb-141">Örnek uygulama, alan için bir tür tanımlayarak bir alıntı eklerken bir dizeyi günlüğe kaydeder `string` <xref:System.Action> :</span><span class="sxs-lookup"><span data-stu-id="0babb-141">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="0babb-142">Temsilcinin günlük iletisi şablonu, belirtilen türlerden yer tutucu değerlerini alır.</span><span class="sxs-lookup"><span data-stu-id="0babb-142">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="0babb-143">Örnek uygulama, quote parametresinin bir tırnak işareti eklemek için bir temsilci tanımlar `string` :</span><span class="sxs-lookup"><span data-stu-id="0babb-143">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="0babb-144">Tırnak eklemek için statik genişletme yöntemi, `QuoteAdded` quote bağımsız değişkeni değerini alır ve <xref:System.Action> temsilciye geçirir:</span><span class="sxs-lookup"><span data-stu-id="0babb-144">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="0babb-145">Dizin sayfasının sayfa modelinde (*Sayfalar/Index. cshtml. cs*), `QuoteAdded` iletiyi günlüğe kaydetmek için çağrılır:</span><span class="sxs-lookup"><span data-stu-id="0babb-145">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="0babb-146">Uygulamanın konsol çıkışını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="0babb-146">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="0babb-147">Örnek uygulama, teklif silme için bir [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) kalıbı uygular.</span><span class="sxs-lookup"><span data-stu-id="0babb-147">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="0babb-148">Başarılı silme işlemi için bir bilgilendirici ileti günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="0babb-148">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="0babb-149">Bir özel durum oluştuğunda silme işlemi için bir hata iletisi günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="0babb-149">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="0babb-150">Başarısız silme işleminin günlük iletisi, özel durum yığın izlemesini içerir (*iç/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="0babb-150">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="0babb-151">Özel durumun içindeki temsilciye nasıl geçtiğini aklınızda yapın `QuoteDeleteFailed` :</span><span class="sxs-lookup"><span data-stu-id="0babb-151">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="0babb-152">Dizin sayfasının sayfa modelinde, başarılı bir teklif silme işlemi `QuoteDeleted` günlükçü üzerindeki yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="0babb-152">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="0babb-153">Silinmek üzere bir teklif bulunamadığında, bir oluşturulur <xref:System.ArgumentNullException> .</span><span class="sxs-lookup"><span data-stu-id="0babb-153">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="0babb-154">Özel durum, [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ifadesiyle `QuoteDeleteFailed` yakalar ve [catch](/dotnet/csharp/language-reference/keywords/try-catch) bloğunda (*Pages/Index. cshtml. cs*) günlükçü üzerindeki yöntemi çağırarak günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="0babb-154">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

<span data-ttu-id="0babb-155">Bir teklif başarıyla silindiğinde, uygulamanın konsol çıkışını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="0babb-155">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="0babb-156">Teklif silme başarısız olduğunda, uygulamanın konsol çıkışını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="0babb-156">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="0babb-157">Özel durumun günlük iletisine dahil edildiğini unutmayın:</span><span class="sxs-lookup"><span data-stu-id="0babb-157">Note that the exception is included in the log message:</span></span>

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

## <a name="loggermessagedefinescope"></a><span data-ttu-id="0babb-158">LoggerMessage. DefineScope</span><span class="sxs-lookup"><span data-stu-id="0babb-158">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="0babb-159">[DefineScope (String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) , <xref:System.Func%601> [günlük kapsamı](xref:fundamentals/logging/index#log-scopes)tanımlamak için bir temsilci oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0babb-159">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="0babb-160"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> Aşırı Yüklemeler, adlandırılmış biçim dizesine (şablon) üç tür parametrenin geçirilmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="0babb-160"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="0babb-161">Yönteminde olduğu gibi <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> , yöntemi için girilen dize, ilişkili <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> dize değil, bir şablondur.</span><span class="sxs-lookup"><span data-stu-id="0babb-161">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="0babb-162">Yer tutucular, türlerin belirtilme sırasına göre doldurulur.</span><span class="sxs-lookup"><span data-stu-id="0babb-162">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="0babb-163">Şablondaki yer tutucu adları, şablonlar genelinde açıklayıcı ve tutarlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0babb-163">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="0babb-164">Bunlar, yapılandırılmış günlük verileri içinde özellik adı olarak görev yapar.</span><span class="sxs-lookup"><span data-stu-id="0babb-164">They serve as property names within structured log data.</span></span> <span data-ttu-id="0babb-165">Yer tutucu adları için [Pascal büyük harfleri](/dotnet/standard/design-guidelines/capitalization-conventions) öneririz.</span><span class="sxs-lookup"><span data-stu-id="0babb-165">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="0babb-166">Örneğin,, `{Count}` `{FirstName}` .</span><span class="sxs-lookup"><span data-stu-id="0babb-166">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="0babb-167">Yöntemini kullanarak bir dizi günlük mesajı için uygulanacak [günlük kapsamını](xref:fundamentals/logging/index#log-scopes) tanımlayın <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> .</span><span class="sxs-lookup"><span data-stu-id="0babb-167">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="0babb-168">Örnek uygulamanın, veritabanındaki tüm teklifleri silmek için **Tümünü Temizle** düğmesi vardır.</span><span class="sxs-lookup"><span data-stu-id="0babb-168">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="0babb-169">Tırnak işaretleri tek seferde kaldırılarak silinir.</span><span class="sxs-lookup"><span data-stu-id="0babb-169">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="0babb-170">Bir teklifin her silindiği her seferinde `QuoteDeleted` yöntemi günlükçü üzerinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="0babb-170">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="0babb-171">Bu günlük iletilerine bir günlük kapsamı eklenir.</span><span class="sxs-lookup"><span data-stu-id="0babb-171">A log scope is added to these log messages.</span></span>

<span data-ttu-id="0babb-172">`IncludeScopes` *Üzerindeappsettings.js*konsol günlükçüsü bölümünde etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="0babb-172">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-json[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="0babb-173">Bir günlük kapsamı oluşturmak için, kapsam için bir temsilci tutacak bir alan ekleyin <xref:System.Func%601> .</span><span class="sxs-lookup"><span data-stu-id="0babb-173">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="0babb-174">Örnek uygulama `_allQuotesDeletedScope` (*Iç/LoggerExtensions. cs*) adlı bir alan oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0babb-174">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="0babb-175"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>Temsilciyi oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="0babb-175">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="0babb-176">Temsilci çağrıldığında Şablon bağımsız değişkenleri olarak kullanmak için en fazla üç tür belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="0babb-176">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="0babb-177">Örnek uygulama, silinen tekliflerin sayısını (bir tür) içeren bir ileti şablonu kullanır `int` :</span><span class="sxs-lookup"><span data-stu-id="0babb-177">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="0babb-178">Günlük iletisi için bir statik genişletme yöntemi sağlayın.</span><span class="sxs-lookup"><span data-stu-id="0babb-178">Provide a static extension method for the log message.</span></span> <span data-ttu-id="0babb-179">İleti şablonunda görünen adlandırılmış özellikler için herhangi bir tür parametresi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0babb-179">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="0babb-180">Örnek uygulama, `count` silmek ve geri döndürmek için tırnak içine alır `_allQuotesDeletedScope` :</span><span class="sxs-lookup"><span data-stu-id="0babb-180">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="0babb-181">Kapsam, oturum açma uzantısı çağrılarını bir [using](/dotnet/csharp/language-reference/keywords/using-statement) bloğunda sarmalar:</span><span class="sxs-lookup"><span data-stu-id="0babb-181">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="0babb-182">Uygulamanın konsol çıkışında günlük iletilerini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="0babb-182">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="0babb-183">Aşağıdaki sonuç, günlük kapsamı iletisi dahil olmak üzere silinen üç tırnak gösterir:</span><span class="sxs-lookup"><span data-stu-id="0babb-183">The following result shows three quotes deleted with the log scope message included:</span></span>

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

<span data-ttu-id="0babb-184"><xref:Microsoft.Extensions.Logging.LoggerMessage> özellikler, ve gibi [günlükçü uzantısı yöntemlerine](xref:Microsoft.Extensions.Logging.LoggerExtensions)kıyasla daha az nesne ayırma ve daha düşük hesaplama yükü gerektiren önbelleğe alınabilir temsilciler oluşturur <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*> .</span><span class="sxs-lookup"><span data-stu-id="0babb-184"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="0babb-185">Yüksek performanslı günlük senaryoları için, bu kalıbı kullanın <xref:Microsoft.Extensions.Logging.LoggerMessage> .</span><span class="sxs-lookup"><span data-stu-id="0babb-185">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="0babb-186"><xref:Microsoft.Extensions.Logging.LoggerMessage> Günlükçü uzantı yöntemlerine göre aşağıdaki performans avantajlarını sağlar:</span><span class="sxs-lookup"><span data-stu-id="0babb-186"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="0babb-187">Günlükçü uzantı yöntemleri, gibi "kutulama" (dönüştürme) değer türlerini gerektirir `int` `object` .</span><span class="sxs-lookup"><span data-stu-id="0babb-187">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="0babb-188">Bu <xref:Microsoft.Extensions.Logging.LoggerMessage> model, <xref:System.Action> kesin türü belirtilmiş parametrelerle statik alanlar ve genişletme yöntemleri kullanarak kutulamayı önler.</span><span class="sxs-lookup"><span data-stu-id="0babb-188">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="0babb-189">Günlükçü uzantısı yöntemlerinin her bir günlük iletisi yazıldığında ileti şablonunu (biçim dizesi olarak adlandırılır) ayrıştırması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0babb-189">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="0babb-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> yalnızca ileti tanımlandığında bir şablonu ayrıştırmayı gerektirir.</span><span class="sxs-lookup"><span data-stu-id="0babb-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="0babb-191">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0babb-191">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0babb-192">Örnek uygulama, <xref:Microsoft.Extensions.Logging.LoggerMessage> temel bir Quote izleme sistemine sahip özellikleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="0babb-192">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="0babb-193">Uygulama, bellek içi veritabanı kullanarak tırnak ekler ve siler.</span><span class="sxs-lookup"><span data-stu-id="0babb-193">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="0babb-194">Bu işlemler gerçekleştiğinde, günlük iletileri model kullanılarak oluşturulur <xref:Microsoft.Extensions.Logging.LoggerMessage> .</span><span class="sxs-lookup"><span data-stu-id="0babb-194">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="0babb-195">LoggerMessage. define</span><span class="sxs-lookup"><span data-stu-id="0babb-195">LoggerMessage.Define</span></span>

<span data-ttu-id="0babb-196">[Define (LogLevel, EventID, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) <xref:System.Action> bir iletiyi günlüğe kaydetmek için bir temsilci oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0babb-196">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="0babb-197"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> Aşırı Yüklemeler, adlandırılmış biçim dizesine (şablon) altı tür parametre geçişine izin verir.</span><span class="sxs-lookup"><span data-stu-id="0babb-197"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="0babb-198">Yöntemine girilen dize, <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> enterpolasyonlu bir dize değil, bir şablondur.</span><span class="sxs-lookup"><span data-stu-id="0babb-198">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="0babb-199">Yer tutucular, türlerin belirtilme sırasına göre doldurulur.</span><span class="sxs-lookup"><span data-stu-id="0babb-199">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="0babb-200">Şablondaki yer tutucu adları, şablonlar genelinde açıklayıcı ve tutarlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0babb-200">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="0babb-201">Bunlar, yapılandırılmış günlük verileri içinde özellik adı olarak görev yapar.</span><span class="sxs-lookup"><span data-stu-id="0babb-201">They serve as property names within structured log data.</span></span> <span data-ttu-id="0babb-202">Yer tutucu adları için [Pascal büyük harfleri](/dotnet/standard/design-guidelines/capitalization-conventions) öneririz.</span><span class="sxs-lookup"><span data-stu-id="0babb-202">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="0babb-203">Örneğin,, `{Count}` `{FirstName}` .</span><span class="sxs-lookup"><span data-stu-id="0babb-203">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="0babb-204">Her günlük iletisi, <xref:System.Action> [Loggermessage. define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*)tarafından oluşturulan statik bir alanda tutulur.</span><span class="sxs-lookup"><span data-stu-id="0babb-204">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="0babb-205">Örneğin, örnek uygulama, Dizin sayfası (*iç/LoggerExtensions. cs*) IÇIN bir GET isteğinin günlük iletisini tanımlayacak bir alan oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0babb-205">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="0babb-206">İçin <xref:System.Action> şunu belirtin:</span><span class="sxs-lookup"><span data-stu-id="0babb-206">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="0babb-207">Günlük düzeyi.</span><span class="sxs-lookup"><span data-stu-id="0babb-207">The log level.</span></span>
* <span data-ttu-id="0babb-208"><xref:Microsoft.Extensions.Logging.EventId>Statik Uzantı yönteminin adı ile benzersiz bir olay tanımlayıcısı ().</span><span class="sxs-lookup"><span data-stu-id="0babb-208">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="0babb-209">İleti şablonu (biçim dizesi olarak adlandırılır).</span><span class="sxs-lookup"><span data-stu-id="0babb-209">The message template (named format string).</span></span> 

<span data-ttu-id="0babb-210">Örnek uygulamanın dizin sayfası için bir istek şunları ayarlar:</span><span class="sxs-lookup"><span data-stu-id="0babb-210">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="0babb-211">Günlük düzeyi `Information` .</span><span class="sxs-lookup"><span data-stu-id="0babb-211">Log level to `Information`.</span></span>
* <span data-ttu-id="0babb-212">`1`Yöntemin adı ile olay kimliği `IndexPageRequested` .</span><span class="sxs-lookup"><span data-stu-id="0babb-212">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="0babb-213">Bir dizeye ileti şablonu (biçim dizesi olarak adlandırılır).</span><span class="sxs-lookup"><span data-stu-id="0babb-213">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="0babb-214">Yapılandırılmış günlük depoları, olay kimliği ile birlikte verileri zenginleştirmek için sağlandığında olay adını kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="0babb-214">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="0babb-215">Örneğin, [Serilog](https://github.com/serilog/serilog-extensions-logging) olay adını kullanır.</span><span class="sxs-lookup"><span data-stu-id="0babb-215">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="0babb-216">, <xref:System.Action> Türü kesin belirlenmiş bir uzantı yöntemiyle çağrılır.</span><span class="sxs-lookup"><span data-stu-id="0babb-216">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="0babb-217">`IndexPageRequested`Yöntemi, örnek uygulamada bir dizin sayfası get isteği için bir ileti kaydeder:</span><span class="sxs-lookup"><span data-stu-id="0babb-217">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="0babb-218">`IndexPageRequested` , `OnGetAsync` *sayfa/dizin. cshtml. cs*içindeki yöntemdeki günlükçü üzerinde çağrılır:</span><span class="sxs-lookup"><span data-stu-id="0babb-218">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="0babb-219">Uygulamanın konsol çıkışını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="0babb-219">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="0babb-220">Parametreleri bir günlük iletisine geçirmek için, statik alanı oluştururken en fazla altı tür tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="0babb-220">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="0babb-221">Örnek uygulama, alan için bir tür tanımlayarak bir alıntı eklerken bir dizeyi günlüğe kaydeder `string` <xref:System.Action> :</span><span class="sxs-lookup"><span data-stu-id="0babb-221">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="0babb-222">Temsilcinin günlük iletisi şablonu, belirtilen türlerden yer tutucu değerlerini alır.</span><span class="sxs-lookup"><span data-stu-id="0babb-222">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="0babb-223">Örnek uygulama, quote parametresinin bir tırnak işareti eklemek için bir temsilci tanımlar `string` :</span><span class="sxs-lookup"><span data-stu-id="0babb-223">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="0babb-224">Tırnak eklemek için statik genişletme yöntemi, `QuoteAdded` quote bağımsız değişkeni değerini alır ve <xref:System.Action> temsilciye geçirir:</span><span class="sxs-lookup"><span data-stu-id="0babb-224">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="0babb-225">Dizin sayfasının sayfa modelinde (*Sayfalar/Index. cshtml. cs*), `QuoteAdded` iletiyi günlüğe kaydetmek için çağrılır:</span><span class="sxs-lookup"><span data-stu-id="0babb-225">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="0babb-226">Uygulamanın konsol çıkışını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="0babb-226">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="0babb-227">Örnek uygulama, teklif silme için bir [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) kalıbı uygular.</span><span class="sxs-lookup"><span data-stu-id="0babb-227">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="0babb-228">Başarılı silme işlemi için bir bilgilendirici ileti günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="0babb-228">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="0babb-229">Bir özel durum oluştuğunda silme işlemi için bir hata iletisi günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="0babb-229">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="0babb-230">Başarısız silme işleminin günlük iletisi, özel durum yığın izlemesini içerir (*iç/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="0babb-230">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="0babb-231">Özel durumun içindeki temsilciye nasıl geçtiğini aklınızda yapın `QuoteDeleteFailed` :</span><span class="sxs-lookup"><span data-stu-id="0babb-231">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="0babb-232">Dizin sayfasının sayfa modelinde, başarılı bir teklif silme işlemi `QuoteDeleted` günlükçü üzerindeki yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="0babb-232">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="0babb-233">Silinmek üzere bir teklif bulunamadığında, bir oluşturulur <xref:System.ArgumentNullException> .</span><span class="sxs-lookup"><span data-stu-id="0babb-233">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="0babb-234">Özel durum, [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ifadesiyle `QuoteDeleteFailed` yakalar ve [catch](/dotnet/csharp/language-reference/keywords/try-catch) bloğunda (*Pages/Index. cshtml. cs*) günlükçü üzerindeki yöntemi çağırarak günlüğe kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="0babb-234">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

<span data-ttu-id="0babb-235">Bir teklif başarıyla silindiğinde, uygulamanın konsol çıkışını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="0babb-235">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="0babb-236">Teklif silme başarısız olduğunda, uygulamanın konsol çıkışını inceleyin.</span><span class="sxs-lookup"><span data-stu-id="0babb-236">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="0babb-237">Özel durumun günlük iletisine dahil edildiğini unutmayın:</span><span class="sxs-lookup"><span data-stu-id="0babb-237">Note that the exception is included in the log message:</span></span>

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

## <a name="loggermessagedefinescope"></a><span data-ttu-id="0babb-238">LoggerMessage. DefineScope</span><span class="sxs-lookup"><span data-stu-id="0babb-238">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="0babb-239">[DefineScope (String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) , <xref:System.Func%601> [günlük kapsamı](xref:fundamentals/logging/index#log-scopes)tanımlamak için bir temsilci oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0babb-239">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="0babb-240"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> Aşırı Yüklemeler, adlandırılmış biçim dizesine (şablon) üç tür parametrenin geçirilmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="0babb-240"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="0babb-241">Yönteminde olduğu gibi <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> , yöntemi için girilen dize, ilişkili <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> dize değil, bir şablondur.</span><span class="sxs-lookup"><span data-stu-id="0babb-241">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="0babb-242">Yer tutucular, türlerin belirtilme sırasına göre doldurulur.</span><span class="sxs-lookup"><span data-stu-id="0babb-242">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="0babb-243">Şablondaki yer tutucu adları, şablonlar genelinde açıklayıcı ve tutarlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0babb-243">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="0babb-244">Bunlar, yapılandırılmış günlük verileri içinde özellik adı olarak görev yapar.</span><span class="sxs-lookup"><span data-stu-id="0babb-244">They serve as property names within structured log data.</span></span> <span data-ttu-id="0babb-245">Yer tutucu adları için [Pascal büyük harfleri](/dotnet/standard/design-guidelines/capitalization-conventions) öneririz.</span><span class="sxs-lookup"><span data-stu-id="0babb-245">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="0babb-246">Örneğin,, `{Count}` `{FirstName}` .</span><span class="sxs-lookup"><span data-stu-id="0babb-246">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="0babb-247">Yöntemini kullanarak bir dizi günlük mesajı için uygulanacak [günlük kapsamını](xref:fundamentals/logging/index#log-scopes) tanımlayın <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> .</span><span class="sxs-lookup"><span data-stu-id="0babb-247">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="0babb-248">Örnek uygulamanın, veritabanındaki tüm teklifleri silmek için **Tümünü Temizle** düğmesi vardır.</span><span class="sxs-lookup"><span data-stu-id="0babb-248">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="0babb-249">Tırnak işaretleri tek seferde kaldırılarak silinir.</span><span class="sxs-lookup"><span data-stu-id="0babb-249">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="0babb-250">Bir teklifin her silindiği her seferinde `QuoteDeleted` yöntemi günlükçü üzerinde çağrılır.</span><span class="sxs-lookup"><span data-stu-id="0babb-250">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="0babb-251">Bu günlük iletilerine bir günlük kapsamı eklenir.</span><span class="sxs-lookup"><span data-stu-id="0babb-251">A log scope is added to these log messages.</span></span>

<span data-ttu-id="0babb-252">`IncludeScopes` *Üzerindeappsettings.js*konsol günlükçüsü bölümünde etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="0babb-252">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-json[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="0babb-253">Bir günlük kapsamı oluşturmak için, kapsam için bir temsilci tutacak bir alan ekleyin <xref:System.Func%601> .</span><span class="sxs-lookup"><span data-stu-id="0babb-253">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="0babb-254">Örnek uygulama `_allQuotesDeletedScope` (*Iç/LoggerExtensions. cs*) adlı bir alan oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0babb-254">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="0babb-255"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>Temsilciyi oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="0babb-255">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="0babb-256">Temsilci çağrıldığında Şablon bağımsız değişkenleri olarak kullanmak için en fazla üç tür belirlenebilir.</span><span class="sxs-lookup"><span data-stu-id="0babb-256">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="0babb-257">Örnek uygulama, silinen tekliflerin sayısını (bir tür) içeren bir ileti şablonu kullanır `int` :</span><span class="sxs-lookup"><span data-stu-id="0babb-257">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="0babb-258">Günlük iletisi için bir statik genişletme yöntemi sağlayın.</span><span class="sxs-lookup"><span data-stu-id="0babb-258">Provide a static extension method for the log message.</span></span> <span data-ttu-id="0babb-259">İleti şablonunda görünen adlandırılmış özellikler için herhangi bir tür parametresi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0babb-259">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="0babb-260">Örnek uygulama, `count` silmek ve geri döndürmek için tırnak içine alır `_allQuotesDeletedScope` :</span><span class="sxs-lookup"><span data-stu-id="0babb-260">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="0babb-261">Kapsam, oturum açma uzantısı çağrılarını bir [using](/dotnet/csharp/language-reference/keywords/using-statement) bloğunda sarmalar:</span><span class="sxs-lookup"><span data-stu-id="0babb-261">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="0babb-262">Uygulamanın konsol çıkışında günlük iletilerini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="0babb-262">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="0babb-263">Aşağıdaki sonuç, günlük kapsamı iletisi dahil olmak üzere silinen üç tırnak gösterir:</span><span class="sxs-lookup"><span data-stu-id="0babb-263">The following result shows three quotes deleted with the log scope message included:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="0babb-264">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0babb-264">Additional resources</span></span>

* [<span data-ttu-id="0babb-265">Günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="0babb-265">Logging</span></span>](xref:fundamentals/logging/index)
