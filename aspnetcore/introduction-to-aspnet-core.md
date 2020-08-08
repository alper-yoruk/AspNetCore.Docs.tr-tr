---
title: ASP.NET Core’a Giriş
author: rick-anderson
description: Modern, bulut özellikli ve Internet 'e bağlı uygulamalar oluşturmaya yönelik platformlar arası, yüksek performanslı, açık kaynaklı bir çatı olan ASP.NET Core tanıtım edin.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
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
uid: index
ms.openlocfilehash: 9cf721b381d3c4c737c15225edcf7918dd1fcf5b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015354"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="d3b4c-103">ASP.NET Core’a Giriş</span><span class="sxs-lookup"><span data-stu-id="d3b4c-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="d3b4c-104">[Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Shaun Luttin](https://twitter.com/dicshaunary) tarafından hazırlanmıştır</span><span class="sxs-lookup"><span data-stu-id="d3b4c-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d3b4c-105">ASP.NET Core modern, bulut özellikli, Internet 'e bağlı uygulamalar oluşturmaya yönelik platformlar arası, yüksek performanslı, [Açık kaynaklı](https://github.com/dotnet/aspnetcore) bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="d3b4c-106">ASP.NET Core ile şunları yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="d3b4c-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="d3b4c-107">Web uygulamaları ve Hizmetleri, [nesnelerin interneti (IoT)](https://www.microsoft.com/internet-of-things/) uygulamaları ve mobil arka uçlar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-107">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="d3b4c-108">Windows, macOS ve Linux üzerinde tercih ettiğiniz geliştirme araçlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="d3b4c-109">Buluta veya şirket içine dağıtın.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="d3b4c-110">[.NET Core](/dotnet/core/introduction)üzerinde çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-110">Run on [.NET Core](/dotnet/core/introduction).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="d3b4c-111">Neden ASP.NET Core seçmelisiniz?</span><span class="sxs-lookup"><span data-stu-id="d3b4c-111">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="d3b4c-112">Milyonlarca geliştirici, Web uygulamaları oluşturmak için [ASP.NET 4. x](/aspnet/overview) kullanır veya kullandı.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-112">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="d3b4c-113">ASP.NET Core, daha modüler bir Framework ile sonuçlanan mimari değişiklikler dahil olmak üzere ASP.NET 4. x ' in bir yeniden tasarlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-113">ASP.NET Core is a redesign of ASP.NET 4.x, including architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="d3b4c-114">ASP.NET Core MVC kullanarak web API'leri ve web kullanıcı arabirimi oluşturma</span><span class="sxs-lookup"><span data-stu-id="d3b4c-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="d3b4c-115">ASP.NET Core MVC, [web API’leri](xref:tutorials/first-web-api) ve [web uygulamaları](xref:tutorials/razor-pages/index) oluşturmaya yönelik özellikler sağlar:</span><span class="sxs-lookup"><span data-stu-id="d3b4c-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="d3b4c-116">[Model-View-Controller (MVC) modeli](xref:mvc/overview) , Web API 'lerinizi ve Web uygulamalarınızı test etmenize yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="d3b4c-117">[ Razor Sayfalar](xref:razor-pages/index) , Web Kullanıcı arabirimi oluşturmayı daha kolay ve daha üretken hale getiren sayfa tabanlı bir programlama modelidir.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="d3b4c-118">[ Razor biçimlendirme](xref:mvc/views/razor) , [ Razor Sayfalar](xref:razor-pages/index) ve [MVC görünümleri](xref:mvc/views/overview)için üretken bir sözdizimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="d3b4c-119">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucu tarafı kodun dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="d3b4c-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="d3b4c-120">[Birden çok veri biçimi ve içerik anlaşması](xref:web-api/advanced/formatting) için sunulan yerleşik destek, web API'lerinizin tarayıcılar ve mobil cihazlar dahil olmak üzere birçok çeşit istemciye ulaşmasına imkan tanır.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="d3b4c-121">[Model bağlama](xref:mvc/models/model-binding), HTTP isteklerinden alınan verileri otomatik olarak eylem metodu parametreleriyle eşleştirir.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="d3b4c-122">[Model doğrulama](xref:mvc/models/validation), otomatik olarak istemci ve sunucu tarafı doğrulama gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="d3b4c-123">İstemci tarafı geliştirme</span><span class="sxs-lookup"><span data-stu-id="d3b4c-123">Client-side development</span></span>

<span data-ttu-id="d3b4c-124">ASP.NET Core [Blazor](xref:blazor/index) , [angular](xref:spa/angular), [tepki](xref:spa/react)verme ve [önyükleme](https://getbootstrap.com/)gibi popüler istemci tarafı çerçeveleri ve kitaplıkları ile sorunsuz bir şekilde tümleşir.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="d3b4c-125">Daha fazla bilgi için bkz <xref:blazor/index> . *istemci tarafı geliştirme*altındaki ilgili konular.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-125">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a><span data-ttu-id="d3b4c-126">ASP.NET Core hedef çerçeveler</span><span class="sxs-lookup"><span data-stu-id="d3b4c-126">ASP.NET Core target frameworks</span></span>

<span data-ttu-id="d3b4c-127">ASP.NET Core 3. x ve üzeri yalnızca .NET Core ' u hedefleyebilir.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-127">ASP.NET Core 3.x and later can only target .NET Core.</span></span> <span data-ttu-id="d3b4c-128">Genellikle, ASP.NET Core [.NET Standard](/dotnet/standard/net-standard) kitaplıklarından oluşur.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-128">Generally, ASP.NET Core is composed of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="d3b4c-129">.NET Standard 2,0 ile yazılan kitaplıklar .NET Standard 2,0 ' i uygulayan herhangi bir [.NET platformunda](/dotnet/standard/net-standard#net-implementation-support)çalışır.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-129">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="d3b4c-130">.NET Core hedeflemesinin çeşitli avantajları vardır ve bu avantajlar her yeni sürümle birlikte artmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-130">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="d3b4c-131">.NET Framework'e göre .NET Core'un bazı avantajları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="d3b4c-131">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="d3b4c-132">Platformlar arası.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-132">Cross-platform.</span></span> <span data-ttu-id="d3b4c-133">Windows, macOS ve Linux üzerinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-133">Runs on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="d3b4c-134">Geliştirilmiş performans</span><span class="sxs-lookup"><span data-stu-id="d3b4c-134">Improved performance</span></span>
* [<span data-ttu-id="d3b4c-135">Yan yana sürüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="d3b4c-135">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="d3b4c-136">Yeni API'ler</span><span class="sxs-lookup"><span data-stu-id="d3b4c-136">New APIs</span></span>
* <span data-ttu-id="d3b4c-137">Açık kaynak</span><span class="sxs-lookup"><span data-stu-id="d3b4c-137">Open source</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="d3b4c-138">Önerilen öğrenme yolu</span><span class="sxs-lookup"><span data-stu-id="d3b4c-138">Recommended learning path</span></span>

<span data-ttu-id="d3b4c-139">ASP.NET Core uygulamalar geliştirmeye giriş için aşağıdaki öğretici dizisini öneririz:</span><span class="sxs-lookup"><span data-stu-id="d3b4c-139">We recommend the following sequence of tutorials for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="d3b4c-140">Geliştirmek veya sürdürmek istediğiniz uygulama türü için bir öğreticiyi izleyin.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-140">Follow a tutorial for the app type you want to develop or maintain.</span></span>

   |<span data-ttu-id="d3b4c-141">Uygulama türü</span><span class="sxs-lookup"><span data-stu-id="d3b4c-141">App type</span></span>  |<span data-ttu-id="d3b4c-142">Senaryo</span><span class="sxs-lookup"><span data-stu-id="d3b4c-142">Scenario</span></span>  |<span data-ttu-id="d3b4c-143">Öğretici</span><span class="sxs-lookup"><span data-stu-id="d3b4c-143">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="d3b4c-144">Web uygulaması</span><span class="sxs-lookup"><span data-stu-id="d3b4c-144">Web app</span></span>                   | <span data-ttu-id="d3b4c-145">Yeni sunucu tarafı Web UI geliştirmesi</span><span class="sxs-lookup"><span data-stu-id="d3b4c-145">New server-side web UI development</span></span> |[<span data-ttu-id="d3b4c-146">Sayfalarla çalışmaya başlama Razor</span><span class="sxs-lookup"><span data-stu-id="d3b4c-146">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="d3b4c-147">Web uygulaması</span><span class="sxs-lookup"><span data-stu-id="d3b4c-147">Web app</span></span>                   | <span data-ttu-id="d3b4c-148">MVC uygulamasını sürdürme</span><span class="sxs-lookup"><span data-stu-id="d3b4c-148">Maintaining an MVC app</span></span> |[<span data-ttu-id="d3b4c-149">MVC ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="d3b4c-149">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="d3b4c-150">Web uygulaması</span><span class="sxs-lookup"><span data-stu-id="d3b4c-150">Web app</span></span>                   | <span data-ttu-id="d3b4c-151">İstemci tarafı Web UI geliştirmesi</span><span class="sxs-lookup"><span data-stu-id="d3b4c-151">Client-side web UI development</span></span> |[<span data-ttu-id="d3b4c-152">Kullanmaya başlayınBlazor</span><span class="sxs-lookup"><span data-stu-id="d3b4c-152">Get started with Blazor</span></span>](https://dotnet.microsoft.com/learn/aspnet/blazor-tutorial/intro) |
   |<span data-ttu-id="d3b4c-153">Web API</span><span class="sxs-lookup"><span data-stu-id="d3b4c-153">Web API</span></span>                   | <span data-ttu-id="d3b4c-154">Yeniden takip eden HTTP Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="d3b4c-154">RESTful HTTP services</span></span> |<span data-ttu-id="d3b4c-155">[Web API 'SI oluşturma](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="d3b4c-155">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="d3b4c-156">Uzak yordam çağrısı uygulaması</span><span class="sxs-lookup"><span data-stu-id="d3b4c-156">Remote Procedure Call app</span></span> | <span data-ttu-id="d3b4c-157">Sözleşme-protokol arabellekleri kullanan ilk hizmetler</span><span class="sxs-lookup"><span data-stu-id="d3b4c-157">Contract-first services using Protocol Buffers</span></span> |[<span data-ttu-id="d3b4c-158">gRPC hizmetini kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="d3b4c-158">Get started with a gRPC service</span></span>](xref:tutorials/grpc/grpc-start) |
   |<span data-ttu-id="d3b4c-159">Gerçek zamanlı uygulama</span><span class="sxs-lookup"><span data-stu-id="d3b4c-159">Real-time app</span></span>             | <span data-ttu-id="d3b4c-160">Sunucular ve bağlı istemciler arasında çift yönlü iletişim</span><span class="sxs-lookup"><span data-stu-id="d3b4c-160">Bidirectional communication between servers and connected clients</span></span> |[<span data-ttu-id="d3b4c-161">Kullanmaya başlayınSignalR</span><span class="sxs-lookup"><span data-stu-id="d3b4c-161">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="d3b4c-162">Temel veri erişiminin nasıl yapılacağını gösteren bir öğreticiyi izleyin.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-162">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="d3b4c-163">Senaryo</span><span class="sxs-lookup"><span data-stu-id="d3b4c-163">Scenario</span></span>  |<span data-ttu-id="d3b4c-164">Öğretici</span><span class="sxs-lookup"><span data-stu-id="d3b4c-164">Tutorial</span></span>  |
   |----------|----------|
   |<span data-ttu-id="d3b4c-165">Yeni geliştirme</span><span class="sxs-lookup"><span data-stu-id="d3b4c-165">New development</span></span>        |[<span data-ttu-id="d3b4c-166">RazorEntity Framework Core olan sayfalar</span><span class="sxs-lookup"><span data-stu-id="d3b4c-166">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   |<span data-ttu-id="d3b4c-167">MVC uygulamasını sürdürme</span><span class="sxs-lookup"><span data-stu-id="d3b4c-167">Maintaining an MVC app</span></span> |[<span data-ttu-id="d3b4c-168">Entity Framework Core ile MVC</span><span class="sxs-lookup"><span data-stu-id="d3b4c-168">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="d3b4c-169">Tüm uygulama türleri için uygulanan ASP.NET Core [temelleri](xref:fundamentals/index) hakkında genel bakış makalesini okuyun.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-169">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="d3b4c-170">İlgilendiğiniz diğer konular için içindekiler tablosuna gözatamazsınız.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-170">Browse the table of contents for other topics of interest.</span></span>

<span data-ttu-id="d3b4c-171">&dagger;Ayrıca [etkileşimli bir Web API öğreticisi](/learn/modules/build-web-api-net-core)de vardır.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-171">&dagger;There's also an [interactive web API tutorial](/learn/modules/build-web-api-net-core).</span></span> <span data-ttu-id="d3b4c-172">Geliştirme araçlarının yerel yüklemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-172">No local installation of development tools is required.</span></span> <span data-ttu-id="d3b4c-173">Kod tarayıcınızda bir [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) çalışır ve test etmek için [kıvrımlı](https://curl.haxx.se/) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-173">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) in your browser, and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="d3b4c-174">.NET Framework geçir</span><span class="sxs-lookup"><span data-stu-id="d3b4c-174">Migrate from .NET Framework</span></span>

<span data-ttu-id="d3b4c-175">ASP.NET 4. x uygulamalarını ASP.NET Core 'e geçirmeye yönelik bir başvuru kılavuzu için, bkz <xref:migration/proper-to-2x/index> ..</span><span class="sxs-lookup"><span data-stu-id="d3b4c-175">For a reference guide to migrating ASP.NET 4.x apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d3b4c-176">ASP.NET Core modern, bulut özellikli, Internet 'e bağlı uygulamalar oluşturmaya yönelik platformlar arası, yüksek performanslı, [Açık kaynaklı](https://github.com/dotnet/aspnetcore) bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-176">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="d3b4c-177">ASP.NET Core ile şunları yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="d3b4c-177">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="d3b4c-178">Web uygulamaları ve Hizmetleri, [nesnelerin interneti (IoT)](https://www.microsoft.com/internet-of-things/) uygulamaları ve mobil arka uçlar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-178">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="d3b4c-179">Windows, macOS ve Linux üzerinde tercih ettiğiniz geliştirme araçlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-179">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="d3b4c-180">Buluta veya şirket içine dağıtın.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-180">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="d3b4c-181">[.NET Core veya .NET Framework](/dotnet/articles/standard/choosing-core-framework-server) üzerinde çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-181">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="d3b4c-182">Neden ASP.NET Core seçmelisiniz?</span><span class="sxs-lookup"><span data-stu-id="d3b4c-182">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="d3b4c-183">Milyonlarca geliştirici, Web uygulamaları oluşturmak için [ASP.NET 4. x](/aspnet/overview) kullanır veya kullandı.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-183">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="d3b4c-184">ASP.NET Core, ASP.NET 4.x sürümünün daha yalın, daha modüler bir çerçeve elde edilmesini sağlayan mimari değişikliklerle yeniden tasarlanmış halidir.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-184">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="d3b4c-185">ASP.NET Core MVC kullanarak web API'leri ve web kullanıcı arabirimi oluşturma</span><span class="sxs-lookup"><span data-stu-id="d3b4c-185">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="d3b4c-186">ASP.NET Core MVC, [web API’leri](xref:tutorials/first-web-api) ve [web uygulamaları](xref:tutorials/razor-pages/index) oluşturmaya yönelik özellikler sağlar:</span><span class="sxs-lookup"><span data-stu-id="d3b4c-186">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="d3b4c-187">[Model-View-Controller (MVC) modeli](xref:mvc/overview) , Web API 'lerinizi ve Web uygulamalarınızı test etmenize yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-187">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="d3b4c-188">[ Razor Sayfalar](xref:razor-pages/index) , Web Kullanıcı arabirimi oluşturmayı daha kolay ve daha üretken hale getiren sayfa tabanlı bir programlama modelidir.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-188">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="d3b4c-189">[ Razor biçimlendirme](xref:mvc/views/razor) , [ Razor Sayfalar](xref:razor-pages/index) ve [MVC görünümleri](xref:mvc/views/overview)için üretken bir sözdizimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-189">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="d3b4c-190">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucu tarafı kodun dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="d3b4c-190">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="d3b4c-191">[Birden çok veri biçimi ve içerik anlaşması](xref:web-api/advanced/formatting) için sunulan yerleşik destek, web API'lerinizin tarayıcılar ve mobil cihazlar dahil olmak üzere birçok çeşit istemciye ulaşmasına imkan tanır.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-191">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="d3b4c-192">[Model bağlama](xref:mvc/models/model-binding), HTTP isteklerinden alınan verileri otomatik olarak eylem metodu parametreleriyle eşleştirir.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-192">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="d3b4c-193">[Model doğrulama](xref:mvc/models/validation), otomatik olarak istemci ve sunucu tarafı doğrulama gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-193">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="d3b4c-194">İstemci tarafı geliştirme</span><span class="sxs-lookup"><span data-stu-id="d3b4c-194">Client-side development</span></span>

<span data-ttu-id="d3b4c-195">ASP.NET Core [Blazor](xref:blazor/index) , [angular](xref:spa/angular), [tepki](xref:spa/react)verme ve [önyükleme](https://getbootstrap.com/)gibi popüler istemci tarafı çerçeveleri ve kitaplıkları ile sorunsuz bir şekilde tümleşir.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-195">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="d3b4c-196">Daha fazla bilgi için bkz <xref:blazor/index> . *istemci tarafı geliştirme*altındaki ilgili konular.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-196">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="d3b4c-197">.NET Framework'ü hedefleyen ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d3b4c-197">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="d3b4c-198">ASP.NET Core 2.x, .NET Core'u veya .NET Framework'ü hedefleyebilir.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-198">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="d3b4c-199">.NET Framework'ü hedefleyen ASP.NET Core uygulamaları platformlar arası çalışmaz; bunlar yalnızca Windows üzerinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-199">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="d3b4c-200">Genel olarak, ASP.NET Core 2.x [.NET Standard](/dotnet/standard/net-standard) kitaplıklarından oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-200">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="d3b4c-201">.NET Standard 2,0 ile yazılan kitaplıklar .NET Standard 2,0 ' i uygulayan herhangi bir [.NET platformunda](/dotnet/standard/net-standard#net-implementation-support)çalışır.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-201">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="d3b4c-202">ASP.NET Core 2. x, .NET Standard 2,0 ' i uygulayan .NET Framework sürümlerinde desteklenir:</span><span class="sxs-lookup"><span data-stu-id="d3b4c-202">ASP.NET Core 2.x is supported on .NET Framework versions that implement .NET Standard 2.0:</span></span>

* <span data-ttu-id="d3b4c-203">En son sürümü .NET Framework önerilir.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-203">.NET Framework latest version is recommended.</span></span>
* <span data-ttu-id="d3b4c-204">.NET Framework 4.6.1 ve üzeri.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-204">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="d3b4c-205">ASP.NET Core 3.0 ve üzeri yalnızca .NET Core’da çalışır.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-205">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="d3b4c-206">Bu değişiklik hakkında daha fazla bilgi için bkz. [ASP.NET Core 3.0’daki değişikliklere ilk bakış](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="d3b4c-206">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="d3b4c-207">.NET Core hedeflemesinin çeşitli avantajları vardır ve bu avantajlar her yeni sürümle birlikte artmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-207">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="d3b4c-208">.NET Framework'e göre .NET Core'un bazı avantajları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="d3b4c-208">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="d3b4c-209">Platformlar arası.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-209">Cross-platform.</span></span> <span data-ttu-id="d3b4c-210">macOS, Linux ve Windows üzerinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-210">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="d3b4c-211">Geliştirilmiş performans</span><span class="sxs-lookup"><span data-stu-id="d3b4c-211">Improved performance</span></span>
* [<span data-ttu-id="d3b4c-212">Yan yana sürüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="d3b4c-212">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="d3b4c-213">Yeni API'ler</span><span class="sxs-lookup"><span data-stu-id="d3b4c-213">New APIs</span></span>
* <span data-ttu-id="d3b4c-214">Açık kaynak</span><span class="sxs-lookup"><span data-stu-id="d3b4c-214">Open source</span></span>

<span data-ttu-id="d3b4c-215">.NET Framework API boşluğunu .NET Core 'a kapatmanıza yardımcı olması için, [Windows Uyumluluk Paketi](/dotnet/core/porting/windows-compat-pack) .NET Core 'da yalnızca binlerce Windows-yalnızca Windows API 'si yaptı.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-215">To help close the API gap from .NET Framework to .NET Core, the [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="d3b4c-216">Bu API'ler .NET Core 1.x'te sağlanmamıştı.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-216">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="d3b4c-217">Önerilen öğrenme yolu</span><span class="sxs-lookup"><span data-stu-id="d3b4c-217">Recommended learning path</span></span>

<span data-ttu-id="d3b4c-218">ASP.NET Core uygulamaları geliştirmeye başlamak için şu öğreticileri ve makaleleri takip etmenizi öneririz:</span><span class="sxs-lookup"><span data-stu-id="d3b4c-218">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="d3b4c-219">Geliştirmek veya sürdürmek istediğiniz uygulama türü için bir öğreticiyi izleyin.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-219">Follow a tutorial for the type of app you want to develop or maintain.</span></span>

   |<span data-ttu-id="d3b4c-220">Uygulama türü</span><span class="sxs-lookup"><span data-stu-id="d3b4c-220">App type</span></span>  |<span data-ttu-id="d3b4c-221">Senaryo</span><span class="sxs-lookup"><span data-stu-id="d3b4c-221">Scenario</span></span>  |<span data-ttu-id="d3b4c-222">Öğretici</span><span class="sxs-lookup"><span data-stu-id="d3b4c-222">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="d3b4c-223">Web uygulaması</span><span class="sxs-lookup"><span data-stu-id="d3b4c-223">Web app</span></span>                   | <span data-ttu-id="d3b4c-224">Yeni proje geliştirmek için</span><span class="sxs-lookup"><span data-stu-id="d3b4c-224">For new development</span></span>        |[<span data-ttu-id="d3b4c-225">Sayfalarla çalışmaya başlama Razor</span><span class="sxs-lookup"><span data-stu-id="d3b4c-225">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="d3b4c-226">Web uygulaması</span><span class="sxs-lookup"><span data-stu-id="d3b4c-226">Web app</span></span>                   | <span data-ttu-id="d3b4c-227">MVC uygulaması yönetmek için</span><span class="sxs-lookup"><span data-stu-id="d3b4c-227">For maintaining an MVC app</span></span> |[<span data-ttu-id="d3b4c-228">MVC ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="d3b4c-228">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="d3b4c-229">Web API</span><span class="sxs-lookup"><span data-stu-id="d3b4c-229">Web API</span></span>                   |                            |<span data-ttu-id="d3b4c-230">[Web API 'SI oluşturma](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="d3b4c-230">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="d3b4c-231">Gerçek zamanlı uygulama</span><span class="sxs-lookup"><span data-stu-id="d3b4c-231">Real-time app</span></span>             |                            |[<span data-ttu-id="d3b4c-232">Kullanmaya başlayınSignalR</span><span class="sxs-lookup"><span data-stu-id="d3b4c-232">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="d3b4c-233">Temel veri erişiminin nasıl yapılacağını gösteren bir öğreticiyi izleyin.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-233">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="d3b4c-234">Senaryo</span><span class="sxs-lookup"><span data-stu-id="d3b4c-234">Scenario</span></span>  |<span data-ttu-id="d3b4c-235">Öğretici</span><span class="sxs-lookup"><span data-stu-id="d3b4c-235">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="d3b4c-236">Yeni proje geliştirmek için</span><span class="sxs-lookup"><span data-stu-id="d3b4c-236">For new development</span></span>        |[<span data-ttu-id="d3b4c-237">RazorEntity Framework Core olan sayfalar</span><span class="sxs-lookup"><span data-stu-id="d3b4c-237">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   | <span data-ttu-id="d3b4c-238">MVC uygulaması yönetmek için</span><span class="sxs-lookup"><span data-stu-id="d3b4c-238">For maintaining an MVC app</span></span> |[<span data-ttu-id="d3b4c-239">Entity Framework Core ile MVC</span><span class="sxs-lookup"><span data-stu-id="d3b4c-239">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="d3b4c-240">Tüm uygulama türleri için uygulanan ASP.NET Core [temelleri](xref:fundamentals/index) hakkında genel bakış makalesini okuyun.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-240">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="d3b4c-241">İlgilendiğiniz diğer konular için İçindekiler Tablosu’na göz atın.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-241">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="d3b4c-242">&dagger;Ayrıca, [tamamen tarayıcıda izlemeniz gereken bir Web API öğreticisi](/learn/modules/build-web-api-net-core)de vardır, yerel IDE yüklemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-242">&dagger;There's also a [web API tutorial that you follow entirely in the browser](/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span> <span data-ttu-id="d3b4c-243">Kod [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)’de çalışır, [curl](https://curl.haxx.se/) ise test için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-243">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="d3b4c-244">.NET Framework geçir</span><span class="sxs-lookup"><span data-stu-id="d3b4c-244">Migrate from .NET Framework</span></span>

<span data-ttu-id="d3b4c-245">ASP.NET uygulamalarını ASP.NET Core geçirmeye yönelik bir başvuru kılavuzu için, bkz <xref:migration/proper-to-2x/index> ..</span><span class="sxs-lookup"><span data-stu-id="d3b4c-245">For a reference guide to migrating ASP.NET apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

## <a name="how-to-download-a-sample"></a><span data-ttu-id="d3b4c-246">Örnek indirme</span><span class="sxs-lookup"><span data-stu-id="d3b4c-246">How to download a sample</span></span>

<span data-ttu-id="d3b4c-247">Çoğu makale ve öğretici örnek koda bağlantılar içerir.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-247">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="d3b4c-248">[ASP.NET depo zip dosyasını indirin](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="d3b4c-248">[Download the ASP.NET repository zip file](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span></span>
1. <span data-ttu-id="d3b4c-249">*Docs-master.zip* dosyasının sıkıştırmasını açın.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-249">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="d3b4c-250">Örnek dizinde gezinmek için örnek bağlantıdaki URL’yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-250">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="d3b4c-251">Örnek kodda ön işlemci yönergeleri</span><span class="sxs-lookup"><span data-stu-id="d3b4c-251">Preprocessor directives in sample code</span></span>

<span data-ttu-id="d3b4c-252">Birden çok senaryoyu göstermek için örnek uygulamalar, `#define` `#if-#else/#elif-#endif` örnek kodun farklı bölümlerini seçmeli olarak derlemek ve çalıştırmak için ve ön işlemci yönergelerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-252">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` preprocessor directives to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="d3b4c-253">Bu yaklaşımı kullanan örnekler için, `#define` C# dosyalarının en üstündeki yönergesini, çalıştırmak istediğiniz senaryoyla ilişkili sembolü tanımlamak için ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-253">For those samples that make use of this approach, set the `#define` directive at the top of the C# files to define the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="d3b4c-254">Bazı örnekler, bir senaryoyu çalıştırmak için birden fazla dosyanın en üstünde sembolün tanımlanmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-254">Some samples require defining the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="d3b4c-255">Örneğin, aşağıdaki simge listesi `#define` dört senaryonun kullanılabilir olduğunu gösterir (her simge için bir senaryo).</span><span class="sxs-lookup"><span data-stu-id="d3b4c-255">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="d3b4c-256">Geçerli örnek yapılandırması `TemplateCode` senaryosunu çalıştırır:</span><span class="sxs-lookup"><span data-stu-id="d3b4c-256">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="d3b4c-257">Örneği `ExpandDefault` senaryosunu çalıştıracak şekilde değiştirmek için `ExpandDefault` simgesini tanımlayın ve kalan simgeleri açıklama satırı yapılmış şekilde bırakın:</span><span class="sxs-lookup"><span data-stu-id="d3b4c-257">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="d3b4c-258">Kod bölümlerini seçmeli olarak derlemek üzere [C# ön işlemci yönergelerini](/dotnet/csharp/language-reference/preprocessor-directives/) kullanma hakkında daha fazla bilgi için bkz. [#define (C# Başvurusu)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) ve [#if (C# Başvurusu)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span><span class="sxs-lookup"><span data-stu-id="d3b4c-258">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="d3b4c-259">Örnek kodda bölgeler</span><span class="sxs-lookup"><span data-stu-id="d3b4c-259">Regions in sample code</span></span>

<span data-ttu-id="d3b4c-260">Bazı örnek uygulamalar, [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) ve [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# yönergelerinin içine alınmış kod bölümlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-260">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# directives.</span></span> <span data-ttu-id="d3b4c-261">Belge derleme sistemi bu bölgeleri işlenmiş belge konularının içine ekler.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-261">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="d3b4c-262">Bölge adları çoğunlukla şu sözcüğü içerir: "snippet."</span><span class="sxs-lookup"><span data-stu-id="d3b4c-262">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="d3b4c-263">Aşağıdaki örnekte `snippet_WebHostDefaults` adlı bir bölge gösterilir:</span><span class="sxs-lookup"><span data-stu-id="d3b4c-263">The following example shows a region named `snippet_WebHostDefaults`:</span></span>

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

<span data-ttu-id="d3b4c-264">Önündeki C# kod parçacığına, konunun markdown dosyasında aşağıdaki satırla başvurulur:</span><span class="sxs-lookup"><span data-stu-id="d3b4c-264">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

<span data-ttu-id="d3b4c-265">`#region`Kodu çevreleyen ve yönergeleri güvenli bir şekilde yoksayabilir (veya kaldırabilirsiniz) `#endregion` .</span><span class="sxs-lookup"><span data-stu-id="d3b4c-265">You may safely ignore (or remove) the `#region` and `#endregion` directives that surround the code.</span></span> <span data-ttu-id="d3b4c-266">Konusunda açıklanan örnek senaryoları çalıştırmayı planlıyorsanız, bu yönergelerin içindeki kodu değiştirmeyin.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-266">Don't alter the code within these directives if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="d3b4c-267">Başka senaryolarla denemeler yaparken kodu rahatça değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-267">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="d3b4c-268">Daha fazla bilgi için bkz. [ASP.NET belgelerine katkıda bulunma: Kod parçacıkları](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span><span class="sxs-lookup"><span data-stu-id="d3b4c-268">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="d3b4c-269">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="d3b4c-269">Next steps</span></span>

<span data-ttu-id="d3b4c-270">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="d3b4c-270">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="d3b4c-271">ASP.NET Core temelleri</span><span class="sxs-lookup"><span data-stu-id="d3b4c-271">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="d3b4c-272">[Haftalık ASP.NET topluluğu toplantısında](https://live.asp.net/), takımın ilerleme durumu ve planları ele alınır.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-272">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="d3b4c-273">Yeni bloglara ve üçüncü taraf yazılıma yer verilir.</span><span class="sxs-lookup"><span data-stu-id="d3b4c-273">It features new blogs and third-party software.</span></span>
