---
title: 10. kısım, ASP.NET Core uygulamasının ayrıntılarını ve silme yöntemlerini inceleyin
author: rick-anderson
description: ASP.NET Core MVC 'de öğretici serisinin Bölüm 10 ' dur.
ms.author: riande
ms.date: 12/13/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 9ceb6c3c8a6622d6e203641c2ce97a483d3d4325
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050751"
---
# <a name="part-10-examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a><span data-ttu-id="d7a84-103">10. kısım, ASP.NET Core uygulamasının ayrıntılarını ve silme yöntemlerini inceleyin</span><span class="sxs-lookup"><span data-stu-id="d7a84-103">Part 10, examine the Details and Delete methods of an ASP.NET Core app</span></span>

<span data-ttu-id="d7a84-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d7a84-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d7a84-105">Film denetleyicisini açın ve `Details` yöntemi inceleyin:</span><span class="sxs-lookup"><span data-stu-id="d7a84-105">Open the Movie controller and examine the `Details` method:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

<span data-ttu-id="d7a84-106">Bu eylem yöntemini oluşturan MVC yapı iskelesi altyapısı, yöntemi çağıran bir HTTP isteğini gösteren bir açıklama ekler.</span><span class="sxs-lookup"><span data-stu-id="d7a84-106">The MVC scaffolding engine that created this action method adds a comment showing an HTTP request that invokes the method.</span></span> <span data-ttu-id="d7a84-107">Bu durumda, üç URL segmentine, `Movies` denetleyiciye, `Details` yönteme ve bir değere sahıp bir get isteği olur `id` .</span><span class="sxs-lookup"><span data-stu-id="d7a84-107">In this case it's a GET request with three URL segments, the `Movies` controller, the `Details` method, and an `id` value.</span></span> <span data-ttu-id="d7a84-108">Bu kesimleri geri çağır *Startup.cs* içinde tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d7a84-108">Recall these segments are defined in *Startup.cs* .</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="d7a84-109">EF, yöntemi kullanarak verileri aramanızı kolaylaştırır `FirstOrDefaultAsync` .</span><span class="sxs-lookup"><span data-stu-id="d7a84-109">EF makes it easy to search for data using the `FirstOrDefaultAsync` method.</span></span> <span data-ttu-id="d7a84-110">Yöntemi içinde yerleşik olarak bulunan önemli bir güvenlik özelliği, kodun, onunla herhangi bir şey yapmayı denemeden önce arama yönteminin bir filmi buldığını doğrulamasından kaynaklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d7a84-110">An important security feature built into the method is that the code verifies that the search method has found a movie before it tries to do anything with it.</span></span> <span data-ttu-id="d7a84-111">Örneğin, bir korsan bağlantıları tarafından oluşturulan URL 'yi `http://localhost:{PORT}/Movies/Details/1`  `http://localhost:{PORT}/Movies/Details/12345` (veya gerçek bir filmi temsil eden başka bir değer) gibi bir konuma değiştirerek siteye hata verebilir.</span><span class="sxs-lookup"><span data-stu-id="d7a84-111">For example, a hacker could introduce errors into the site by changing the URL created by the links from `http://localhost:{PORT}/Movies/Details/1` to something like  `http://localhost:{PORT}/Movies/Details/12345` (or some other value that doesn't represent an actual movie).</span></span> <span data-ttu-id="d7a84-112">Null bir filmi denetmediyseniz, uygulama bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d7a84-112">If you didn't check for a null movie, the app would throw an exception.</span></span>

<span data-ttu-id="d7a84-113">`Delete`Ve yöntemlerini inceleyin `DeleteConfirmed` .</span><span class="sxs-lookup"><span data-stu-id="d7a84-113">Examine the `Delete` and `DeleteConfirmed` methods.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

<span data-ttu-id="d7a84-114">`HTTP GET Delete`Yöntemin belirtilen filmi silmediğini unutmayın. Bu, silme işlemini gönderebileceğiniz (HttpPost) filmin bir görünümünü döndürür.</span><span class="sxs-lookup"><span data-stu-id="d7a84-114">Note that the `HTTP GET Delete` method doesn't delete the specified movie, it returns a view of the movie where you can submit (HttpPost) the deletion.</span></span> <span data-ttu-id="d7a84-115">Bir GET isteğine yanıt olarak silme işlemi gerçekleştirme (veya bu konuyla ilgili olarak, düzenleme işlemi gerçekleştirme, oluşturma işlemi yapma veya verileri değiştiren başka bir işlem) bir güvenlik deliği açılır.</span><span class="sxs-lookup"><span data-stu-id="d7a84-115">Performing a delete operation in response to a GET request (or for that matter, performing an edit operation, create operation, or any other operation that changes data) opens up a security hole.</span></span>

<span data-ttu-id="d7a84-116">`[HttpPost]`Verileri silen yöntemi, `DeleteConfirmed` http post yöntemine benzersiz bir imza veya ad vermek için olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="d7a84-116">The `[HttpPost]` method that deletes the data is named `DeleteConfirmed` to give the HTTP POST method a unique signature or name.</span></span> <span data-ttu-id="d7a84-117">İki yöntem imzası aşağıda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="d7a84-117">The two method signatures are shown below:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

<span data-ttu-id="d7a84-118">Ortak dil çalışma zamanı (CLR), aşırı yüklenmiş yöntemlerin benzersiz bir parametre imzasına sahip olmasını gerektirir (aynı yöntem adı ancak farklı parametre listesi).</span><span class="sxs-lookup"><span data-stu-id="d7a84-118">The common language runtime (CLR) requires overloaded methods to have a unique parameter signature (same method name but different list of parameters).</span></span> <span data-ttu-id="d7a84-119">Ancak, `Delete` her ikisi de aynı parametre imzasına sahip olan--get için bir tane olmak üzere iki yöntem gereklidir.</span><span class="sxs-lookup"><span data-stu-id="d7a84-119">However, here you need two `Delete` methods -- one for GET and one for POST -- that both have the same parameter signature.</span></span> <span data-ttu-id="d7a84-120">(Her ikisi de parametre olarak tek bir tamsayıyı kabul etmelidir.)</span><span class="sxs-lookup"><span data-stu-id="d7a84-120">(They both need to accept a single integer as a parameter.)</span></span>

<span data-ttu-id="d7a84-121">Bu sorunun iki yaklaşımı vardır, biri yöntemlere farklı adlar vermektir.</span><span class="sxs-lookup"><span data-stu-id="d7a84-121">There are two approaches to this problem, one is to give the methods different names.</span></span> <span data-ttu-id="d7a84-122">Bu, önceki örnekte bulunan yapı iskelesi mekanizmasına göre yapılır.</span><span class="sxs-lookup"><span data-stu-id="d7a84-122">That's what the scaffolding mechanism did in the preceding example.</span></span> <span data-ttu-id="d7a84-123">Ancak, bu küçük bir sorun ortaya çıkarır: ASP.NET bir URL 'nin segmentlerini ada göre eylem yöntemlerine eşler ve bir yöntemi yeniden adlandırırsanız, yönlendirme normalde bu yöntemi bulamaz.</span><span class="sxs-lookup"><span data-stu-id="d7a84-123">However, this introduces a small problem: ASP.NET maps segments of a URL to action methods by name, and if you rename a method, routing normally wouldn't be able to find that method.</span></span> <span data-ttu-id="d7a84-124">Çözüm, örnekte gördüğünüz şeydir. Bu, `ActionName("Delete")` yöntemine özniteliği eklemektir `DeleteConfirmed` .</span><span class="sxs-lookup"><span data-stu-id="d7a84-124">The solution is what you see in the example, which is to add the `ActionName("Delete")` attribute to the `DeleteConfirmed` method.</span></span> <span data-ttu-id="d7a84-125">Bu öznitelik, yönlendirme sistemi için eşleme gerçekleştirerek, bir POST isteği için/Delete/içeren bir URL `DeleteConfirmed` yöntemi bulur.</span><span class="sxs-lookup"><span data-stu-id="d7a84-125">That attribute performs mapping for the routing system so that a URL that includes /Delete/ for a POST request will find the `DeleteConfirmed` method.</span></span>

<span data-ttu-id="d7a84-126">Özdeş adlara ve imzalara sahip yöntemler için bir diğer yaygın çalışma yapay, POST yönteminin imzasını bir ek (kullanılmamış) parametre içerecek şekilde değiştirecek.</span><span class="sxs-lookup"><span data-stu-id="d7a84-126">Another common work around for methods that have identical names and signatures is to artificially change the signature of the POST method to include an extra (unused) parameter.</span></span> <span data-ttu-id="d7a84-127">Bu, parametreyi eklediğimiz sırada önceki bir gönderimiz tarafından yaptığımız `notUsed` .</span><span class="sxs-lookup"><span data-stu-id="d7a84-127">That's what we did in a previous post when we added the `notUsed` parameter.</span></span> <span data-ttu-id="d7a84-128">Yöntemi için burada aynı şeyi yapabilirsiniz `[HttpPost] Delete` :</span><span class="sxs-lookup"><span data-stu-id="d7a84-128">You could do the same thing here for the `[HttpPost] Delete` method:</span></span>

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a><span data-ttu-id="d7a84-129">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="d7a84-129">Publish to Azure</span></span>

<span data-ttu-id="d7a84-130">Azure 'a dağıtma hakkında bilgi için bkz. [öğretici: Azure App Service ASP.NET Core ve SQL veritabanı uygulaması oluşturma](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span><span class="sxs-lookup"><span data-stu-id="d7a84-130">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core and SQL Database app in Azure App Service](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="d7a84-131">Önceki</span><span class="sxs-lookup"><span data-stu-id="d7a84-131">Previous</span></span>](validation.md)
