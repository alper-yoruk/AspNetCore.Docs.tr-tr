---
title: Yapılandırmayı ASP.NET Core geçir
author: ardalis
description: ASP.NET MVC projesinden yapılandırmayı ASP.NET Core MVC projesine geçirmeyi öğrenin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/configuration
ms.openlocfilehash: 7c1f2feb40e115d71fb087201acdf52197a52c88
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015107"
---
# <a name="migrate-configuration-to-aspnet-core"></a><span data-ttu-id="bbc72-103">Yapılandırmayı ASP.NET Core geçir</span><span class="sxs-lookup"><span data-stu-id="bbc72-103">Migrate configuration to ASP.NET Core</span></span>

<span data-ttu-id="bbc72-104">, [Steve Smith](https://ardalis.com/) ve [Scott Ade](https://scottaddie.com) tarafından</span><span class="sxs-lookup"><span data-stu-id="bbc72-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="bbc72-105">Önceki makalede, [bir ASP.NET MVC projesini ASP.NET Core MVC 'ye geçirmeye](xref:migration/mvc)başladık.</span><span class="sxs-lookup"><span data-stu-id="bbc72-105">In the previous article, we began to [migrate an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/mvc).</span></span> <span data-ttu-id="bbc72-106">Bu makalede, yapılandırmayı geçiririz.</span><span class="sxs-lookup"><span data-stu-id="bbc72-106">In this article, we migrate configuration.</span></span>

<span data-ttu-id="bbc72-107">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bbc72-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="setup-configuration"></a><span data-ttu-id="bbc72-108">Kurulum yapılandırması</span><span class="sxs-lookup"><span data-stu-id="bbc72-108">Setup configuration</span></span>

<span data-ttu-id="bbc72-109">ASP.NET Core artık, önceki ASP.NET sürümleri tarafından kullanılan *Global. asax* ve *web.config* dosyalarını kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="bbc72-109">ASP.NET Core no longer uses the *Global.asax* and *web.config* files that previous versions of ASP.NET utilized.</span></span> <span data-ttu-id="bbc72-110">Önceki ASP.NET sürümlerinde, uygulama başlangıç mantığı `Application_StartUp` *Global. asax*içindeki bir yönteme yerleştirildi.</span><span class="sxs-lookup"><span data-stu-id="bbc72-110">In the earlier versions of ASP.NET, application startup logic was placed in an `Application_StartUp` method within *Global.asax*.</span></span> <span data-ttu-id="bbc72-111">Daha sonra, ASP.NET MVC 'de projenin köküne bir *Startup.cs* dosyası eklenmiştir; ve uygulama başlatıldığında çağırılır.</span><span class="sxs-lookup"><span data-stu-id="bbc72-111">Later, in ASP.NET MVC, a *Startup.cs* file was included in the root of the project; and, it was called when the application started.</span></span> <span data-ttu-id="bbc72-112">ASP.NET Core, tüm başlangıç mantığını *Startup.cs* dosyasına yerleştirerek bu yaklaşımı tamamen benimsemiştir.</span><span class="sxs-lookup"><span data-stu-id="bbc72-112">ASP.NET Core has adopted this approach completely by placing all startup logic in the *Startup.cs* file.</span></span>

<span data-ttu-id="bbc72-113">*web.config* dosya Ayrıca ASP.NET Core değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="bbc72-113">The *web.config* file has also been replaced in ASP.NET Core.</span></span> <span data-ttu-id="bbc72-114">Yapılandırma, *Startup.cs*bölümünde açıklanan uygulama başlatma yordamının bir parçası olarak artık yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="bbc72-114">Configuration itself can now be configured, as part of the application startup procedure described in *Startup.cs*.</span></span> <span data-ttu-id="bbc72-115">Yapılandırma XML dosyalarını kullanmaya devam edebilir, ancak genellikle ASP.NET Core projeler yapılandırma değerlerini, *appsettings.js*gibi JSON biçimli bir dosyaya yerleştirir.</span><span class="sxs-lookup"><span data-stu-id="bbc72-115">Configuration can still utilize XML files, but typically ASP.NET Core projects will place configuration values in a JSON-formatted file, such as *appsettings.json*.</span></span> <span data-ttu-id="bbc72-116">ASP.NET Core yapılandırma sistemi, ortama özgü değerler için [daha güvenli ve sağlam bir konum](xref:security/app-secrets) sağlayabilen ortam değişkenlerine de kolayca erişebilir.</span><span class="sxs-lookup"><span data-stu-id="bbc72-116">ASP.NET Core's configuration system can also easily access environment variables, which can provide a [more secure and robust location](xref:security/app-secrets) for environment-specific values.</span></span> <span data-ttu-id="bbc72-117">Bu, kaynak denetimine denetlenmemelidir bağlantı dizeleri ve API anahtarları gibi gizli diziler için özellikle doğrudur.</span><span class="sxs-lookup"><span data-stu-id="bbc72-117">This is especially true for secrets like connection strings and API keys that shouldn't be checked into source control.</span></span> <span data-ttu-id="bbc72-118">ASP.NET Core yapılandırma hakkında daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="bbc72-118">See [Configuration](xref:fundamentals/configuration/index) to learn more about configuration in ASP.NET Core.</span></span>

<span data-ttu-id="bbc72-119">Bu makalede, [önceki makaleden](xref:migration/mvc)kısmen geçirilmiş ASP.NET Core projesi ile başlıyoruz.</span><span class="sxs-lookup"><span data-stu-id="bbc72-119">For this article, we are starting with the partially migrated ASP.NET Core project from [the previous article](xref:migration/mvc).</span></span> <span data-ttu-id="bbc72-120">Yapılandırmayı ayarlamak için, aşağıdaki oluşturucuyu ve özelliğini projenin kökünde bulunan *Startup.cs* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="bbc72-120">To setup configuration, add the following constructor and property to the *Startup.cs* file located in the root of the project:</span></span>

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

<span data-ttu-id="bbc72-121">Bu noktada, *Startup.cs* dosyasının derlenmeyeceğini, ancak yine de aşağıdaki ifadeyi eklememiz gerektiğini unutmayın `using` :</span><span class="sxs-lookup"><span data-stu-id="bbc72-121">Note that at this point, the *Startup.cs* file won't compile, as we still need to add the following `using` statement:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="bbc72-122">Uygun öğe şablonunu kullanarak, projenin köküne bir *appsettings.js* ekleyin:</span><span class="sxs-lookup"><span data-stu-id="bbc72-122">Add an *appsettings.json* file to the root of the project using the appropriate item template:</span></span>

![AppSettings JSON Ekle](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a><span data-ttu-id="bbc72-124">Yapılandırma ayarlarını web.config geçir</span><span class="sxs-lookup"><span data-stu-id="bbc72-124">Migrate configuration settings from web.config</span></span>

<span data-ttu-id="bbc72-125">ASP.NET MVC projemiz, gereken *web.config*veritabanı bağlantı dizesini `<connectionStrings>` öğesine içeriyordu.</span><span class="sxs-lookup"><span data-stu-id="bbc72-125">Our ASP.NET MVC project included the required database connection string in *web.config*, in the `<connectionStrings>` element.</span></span> <span data-ttu-id="bbc72-126">ASP.NET Core projemizdeki bu bilgileri dosyada *appsettings.js* depolayacağız.</span><span class="sxs-lookup"><span data-stu-id="bbc72-126">In our ASP.NET Core project, we are going to store this information in the *appsettings.json* file.</span></span> <span data-ttu-id="bbc72-127">*Üzerindeappsettings.js*açın ve bunun zaten şunları içerdiğini unutmayın:</span><span class="sxs-lookup"><span data-stu-id="bbc72-127">Open *appsettings.json*, and note that it already includes the following:</span></span>

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

<span data-ttu-id="bbc72-128">Yukarıda gösterilen vurgulanan satırda veritabanının adını **_CHANGE_ME** ' dan veritabanınızın adına değiştirin.</span><span class="sxs-lookup"><span data-stu-id="bbc72-128">In the highlighted line depicted above, change the name of the database from **_CHANGE_ME** to the name of your database.</span></span>

## <a name="summary"></a><span data-ttu-id="bbc72-129">Özet</span><span class="sxs-lookup"><span data-stu-id="bbc72-129">Summary</span></span>

<span data-ttu-id="bbc72-130">ASP.NET Core, uygulamanın tüm başlangıç mantığını, gerekli hizmetlerin ve bağımlılıkların tanımlanmasının ve yapılandırılabileceği tek bir dosyaya koyar.</span><span class="sxs-lookup"><span data-stu-id="bbc72-130">ASP.NET Core places all startup logic for the application in a single file, in which the necessary services and dependencies can be defined and configured.</span></span> <span data-ttu-id="bbc72-131">*web.config* dosyasını, JSON gibi çeşitli dosya biçimlerinden ve ortam değişkenlerinin yanı sıra çeşitli dosya biçimlerinden faydalanabilir esnek bir yapılandırma özelliği ile değiştirir.</span><span class="sxs-lookup"><span data-stu-id="bbc72-131">It replaces the *web.config* file with a flexible configuration feature that can leverage a variety of file formats, such as JSON, as well as environment variables.</span></span>
