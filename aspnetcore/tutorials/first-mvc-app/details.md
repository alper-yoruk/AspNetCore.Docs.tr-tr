---
title: ASP.NET Core uygulamasının Ayrıntılarını ve Silme yöntemlerini inceleyin
author: rick-anderson
description: Temel bir ASP.NET Core MVC uygulamasında Ayrıntılar denetleyici yöntemi ve görünümü hakkında bilgi edinin.
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 04eb2efa4e67d84e575580a6248d0b5b567064af
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662913"
---
# <a name="examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a><span data-ttu-id="049bb-103">ASP.NET Core uygulamasının Ayrıntılarını ve Silme yöntemlerini inceleyin</span><span class="sxs-lookup"><span data-stu-id="049bb-103">Examine the Details and Delete methods of an ASP.NET Core app</span></span>

<span data-ttu-id="049bb-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="049bb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="049bb-105">Film denetleyicisini açın `Details` ve yöntemi inceleyin:</span><span class="sxs-lookup"><span data-stu-id="049bb-105">Open the Movie controller and examine the `Details` method:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

<span data-ttu-id="049bb-106">Bu eylem yöntemini oluşturan MVC iskele altyapısı, yöntemi çağıran bir HTTP isteğini gösteren bir yorum ekler.</span><span class="sxs-lookup"><span data-stu-id="049bb-106">The MVC scaffolding engine that created this action method adds a comment showing an HTTP request that invokes the method.</span></span> <span data-ttu-id="049bb-107">Bu durumda, üç URL segmenti, `Movies` denetleyici, `Details` yöntem ve bir değer `id` içeren bir GET isteğidir.</span><span class="sxs-lookup"><span data-stu-id="049bb-107">In this case it's a GET request with three URL segments, the `Movies` controller, the `Details` method, and an `id` value.</span></span> <span data-ttu-id="049bb-108">Bu kesimlerin *Startup.cs*olarak tanımlandığını hatırlayın.</span><span class="sxs-lookup"><span data-stu-id="049bb-108">Recall these segments are defined in *Startup.cs*.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="049bb-109">EF, `FirstOrDefaultAsync` yöntemi kullanarak veri aramayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="049bb-109">EF makes it easy to search for data using the `FirstOrDefaultAsync` method.</span></span> <span data-ttu-id="049bb-110">Yöntemde yerleşik önemli bir güvenlik özelliği, kodun arama yönteminin bir film bulduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="049bb-110">An important security feature built into the method is that the code verifies that the search method has found a movie before it tries to do anything with it.</span></span> <span data-ttu-id="049bb-111">Örneğin, bir bilgisayar korsanı, bağlantılar `http://localhost:{PORT}/Movies/Details/1` tarafından oluşturulan URL'yi benzer `http://localhost:{PORT}/Movies/Details/12345` bir şeye (veya gerçek bir filmi temsil etmeyen başka bir değere) değiştirerek siteye hatalar getirebilir.</span><span class="sxs-lookup"><span data-stu-id="049bb-111">For example, a hacker could introduce errors into the site by changing the URL created by the links from `http://localhost:{PORT}/Movies/Details/1` to something like  `http://localhost:{PORT}/Movies/Details/12345` (or some other value that doesn't represent an actual movie).</span></span> <span data-ttu-id="049bb-112">Null film için kontrol etmediyseniz, uygulama bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="049bb-112">If you didn't check for a null movie, the app would throw an exception.</span></span>

<span data-ttu-id="049bb-113">Yöntemleri `Delete` ve `DeleteConfirmed` yöntemleriince.</span><span class="sxs-lookup"><span data-stu-id="049bb-113">Examine the `Delete` and `DeleteConfirmed` methods.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

<span data-ttu-id="049bb-114">Yöntemin `HTTP GET Delete` belirtilen filmi silmediğini, silme işlemini (HttpPost) gönderebileceğiniz bir film görünümünü döndürtüğünü unutmayın.</span><span class="sxs-lookup"><span data-stu-id="049bb-114">Note that the `HTTP GET Delete` method doesn't delete the specified movie, it returns a view of the movie where you can submit (HttpPost) the deletion.</span></span> <span data-ttu-id="049bb-115">GET isteğine yanıt olarak bir silme işlemi gerçekleştirmek (veya bu konuda, bir düzenleme işlemi gerçekleştirmek, işlem oluşturmak veya verileri değiştiren başka bir işlem) bir güvenlik açığı açar.</span><span class="sxs-lookup"><span data-stu-id="049bb-115">Performing a delete operation in response to a GET request (or for that matter, performing an edit operation, create operation, or any other operation that changes data) opens up a security hole.</span></span>

<span data-ttu-id="049bb-116">Verileri `[HttpPost]` silen yöntem, `DeleteConfirmed` HTTP POST yöntemine benzersiz bir imza veya ad vermek için adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="049bb-116">The `[HttpPost]` method that deletes the data is named `DeleteConfirmed` to give the HTTP POST method a unique signature or name.</span></span> <span data-ttu-id="049bb-117">İki yöntem imzaları aşağıda gösterilmiştir:</span><span class="sxs-lookup"><span data-stu-id="049bb-117">The two method signatures are shown below:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

<span data-ttu-id="049bb-118">Ortak dil çalışma süresi (CLR) benzersiz bir parametre imzası (aynı yöntem adı ama farklı parametre listesi) için aşırı yüklenen yöntemler gerektirir.</span><span class="sxs-lookup"><span data-stu-id="049bb-118">The common language runtime (CLR) requires overloaded methods to have a unique parameter signature (same method name but different list of parameters).</span></span> <span data-ttu-id="049bb-119">Ancak, burada iki `Delete` yöntem gerekir - get için bir ve POST için bir - her ikisi de aynı parametre imzaya sahip.</span><span class="sxs-lookup"><span data-stu-id="049bb-119">However, here you need two `Delete` methods -- one for GET and one for POST -- that both have the same parameter signature.</span></span> <span data-ttu-id="049bb-120">(Her ikisi de bir parametre olarak tek bir tamsayı kabul etmek gerekir.)</span><span class="sxs-lookup"><span data-stu-id="049bb-120">(They both need to accept a single integer as a parameter.)</span></span>

<span data-ttu-id="049bb-121">Bu soruna iki yaklaşım vardır, bir yöntemleri farklı adlar vermektir.</span><span class="sxs-lookup"><span data-stu-id="049bb-121">There are two approaches to this problem, one is to give the methods different names.</span></span> <span data-ttu-id="049bb-122">İskele mekanizması nın önceki örnekte yaptığı da buydu.</span><span class="sxs-lookup"><span data-stu-id="049bb-122">That's what the scaffolding mechanism did in the preceding example.</span></span> <span data-ttu-id="049bb-123">Ancak, bu küçük bir sorun ortaya çıkarır: ASP.NET eşler bir URL'nin kesimlerini adlarına göre eylem yöntemleriyle eşler ve bir yöntemi yeniden adlarsanız, yönlendirme normalde bu yöntemi bulamaz.</span><span class="sxs-lookup"><span data-stu-id="049bb-123">However, this introduces a small problem: ASP.NET maps segments of a URL to action methods by name, and if you rename a method, routing normally wouldn't be able to find that method.</span></span> <span data-ttu-id="049bb-124">Çözüm, `ActionName("Delete")` `DeleteConfirmed` yönteme öznitelik eklemek için örnekte gördüğünüz şeydir.</span><span class="sxs-lookup"><span data-stu-id="049bb-124">The solution is what you see in the example, which is to add the `ActionName("Delete")` attribute to the `DeleteConfirmed` method.</span></span> <span data-ttu-id="049bb-125">Bu öznitelik, yönlendirme sistemi için eşleme gerçekleştirir, böylece bir POST isteği için `DeleteConfirmed` /Delete/ içeren bir URL yöntemi bulur.</span><span class="sxs-lookup"><span data-stu-id="049bb-125">That attribute performs mapping for the routing system so that a URL that includes /Delete/ for a POST request will find the `DeleteConfirmed` method.</span></span>

<span data-ttu-id="049bb-126">Aynı adlara ve imzalara sahip yöntemler için yapılan bir diğer yaygın çözüm de, POST yönteminin imzasını yapay olarak ekstra (kullanılmayan) bir parametre içerecek şekilde değiştirmektir.</span><span class="sxs-lookup"><span data-stu-id="049bb-126">Another common work around for methods that have identical names and signatures is to artificially change the signature of the POST method to include an extra (unused) parameter.</span></span> <span data-ttu-id="049bb-127">`notUsed` Parametreyi eklediğimizde bir önceki yazıda yaptığımız buydu.</span><span class="sxs-lookup"><span data-stu-id="049bb-127">That's what we did in a previous post when we added the `notUsed` parameter.</span></span> <span data-ttu-id="049bb-128">Burada `[HttpPost] Delete` yöntem için aynı şeyi yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="049bb-128">You could do the same thing here for the `[HttpPost] Delete` method:</span></span>

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a><span data-ttu-id="049bb-129">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="049bb-129">Publish to Azure</span></span>

<span data-ttu-id="049bb-130">Azure'a dağıtım hakkında daha fazla bilgi için [Bkz.](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)</span><span class="sxs-lookup"><span data-stu-id="049bb-130">For information on deploying to Azure, see [Tutorial: Build a .NET Core and SQL Database web app in Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="049bb-131">Önceki</span><span class="sxs-lookup"><span data-stu-id="049bb-131">Previous</span></span>](validation.md)
