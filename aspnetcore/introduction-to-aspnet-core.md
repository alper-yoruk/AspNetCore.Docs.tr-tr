---
title: ASP.NET Core’a Giriş
author: rick-anderson
description: Modern, bulut özellikli, Internet'e bağlı uygulamalar oluşturmak için çapraz platform, yüksek performanslı, açık kaynak kodlu bir çerçeve olan ASP.NET Core'a giriş yapın.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- SignalR
uid: index
ms.openlocfilehash: c5a5a0ada996d88cb9252da25b5580fe0cf46f0b
ms.sourcegitcommit: 636efd1afc0a1e6fd4b12ae3a542917b356abb93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81615944"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="29267-103">ASP.NET Core’a Giriş</span><span class="sxs-lookup"><span data-stu-id="29267-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="29267-104">[Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Shaun Luttin](https://twitter.com/dicshaunary) tarafından hazırlanmıştır</span><span class="sxs-lookup"><span data-stu-id="29267-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="29267-105">ASP.NET Core, modern, bulut özellikli, Internet'e bağlı uygulamalar oluşturmak için bir çapraz platform, yüksek performanslı, [açık kaynak kodlu](https://github.com/dotnet/aspnetcore) bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="29267-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="29267-106">ASP.NET Core ile şunları yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="29267-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="29267-107">Web uygulamaları ve hizmetleri, [Nesnelerin İnterneti (IoT)](https://www.microsoft.com/internet-of-things/) uygulamaları ve mobil arka uçlar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="29267-107">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="29267-108">Windows, macOS ve Linux üzerinde tercih ettiğiniz geliştirme araçlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="29267-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="29267-109">Buluta veya şirket içine dağıtın.</span><span class="sxs-lookup"><span data-stu-id="29267-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="29267-110">[.NET Core'da](/dotnet/core/introduction)çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="29267-110">Run on [.NET Core](/dotnet/core/introduction).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="29267-111">Neden ASP.NET Core'u seçmelisiniz?</span><span class="sxs-lookup"><span data-stu-id="29267-111">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="29267-112">Milyonlarca geliştirici web uygulamaları oluşturmak için [4.x ASP.NET](/aspnet/overview) kullanır veya kullanır.</span><span class="sxs-lookup"><span data-stu-id="29267-112">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="29267-113">ASP.NET Core, daha yalın, daha modüler bir çerçeveyle sonuçlanan mimari değişiklikler de dahil olmak üzere 4.x ASP.NET yeniden tasarlanmasıdır.</span><span class="sxs-lookup"><span data-stu-id="29267-113">ASP.NET Core is a redesign of ASP.NET 4.x, including architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="29267-114">ASP.NET Core MVC kullanarak web API'leri ve web kullanıcı arabirimi oluşturma</span><span class="sxs-lookup"><span data-stu-id="29267-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="29267-115">ASP.NET Core MVC, [web API’leri](xref:tutorials/first-web-api) ve [web uygulamaları](xref:tutorials/razor-pages/index) oluşturmaya yönelik özellikler sağlar:</span><span class="sxs-lookup"><span data-stu-id="29267-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="29267-116">[Model Görünümü Denetleyicisi (MVC) deseni,](xref:mvc/overview) web API'lerinizi ve web uygulamalarınızı test edilebilir hale getirmeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="29267-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="29267-117">[Razor Pages](xref:razor-pages/index), web kullanıcı arabirimi oluşturmayı daha kolay ve üretken bir hale getiren, sayfa tabanlı bir programlama modelidir.</span><span class="sxs-lookup"><span data-stu-id="29267-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="29267-118">[Razor işaretlemesi](xref:mvc/views/razor), [Razor Sayfaları](xref:razor-pages/index) ve [MVC görünümleri](xref:mvc/views/overview) için üretken bir söz dizimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="29267-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="29267-119">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), Razor dosyalarında HTML öğelerinin oluşturulmasına ve işlenmesine sunucu tarafı kodun katılmasını etkinleştir.</span><span class="sxs-lookup"><span data-stu-id="29267-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="29267-120">[Birden çok veri biçimi ve içerik anlaşması](xref:web-api/advanced/formatting) için sunulan yerleşik destek, web API'lerinizin tarayıcılar ve mobil cihazlar dahil olmak üzere birçok çeşit istemciye ulaşmasına imkan tanır.</span><span class="sxs-lookup"><span data-stu-id="29267-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="29267-121">[Model bağlama](xref:mvc/models/model-binding), HTTP isteklerinden alınan verileri otomatik olarak eylem metodu parametreleriyle eşleştirir.</span><span class="sxs-lookup"><span data-stu-id="29267-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="29267-122">[Model doğrulama](xref:mvc/models/validation), otomatik olarak istemci ve sunucu tarafı doğrulama gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="29267-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="29267-123">İstemci tarafı geliştirme</span><span class="sxs-lookup"><span data-stu-id="29267-123">Client-side development</span></span>

<span data-ttu-id="29267-124">ASP.NET Core [blazor](xref:blazor/index)dahil popüler istemci tarafı çerçeveleri ve kütüphaneleri ile sorunsuz entegre, [Açısal](xref:spa/angular), [React](xref:spa/react), ve [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="29267-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="29267-125">Daha fazla bilgi <xref:blazor/index> için, bkz ve *Müşteri tarafı geliştirme*altında ilgili konular.</span><span class="sxs-lookup"><span data-stu-id="29267-125">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a><span data-ttu-id="29267-126">ASP.NET Temel hedef çerçeveleri</span><span class="sxs-lookup"><span data-stu-id="29267-126">ASP.NET Core target frameworks</span></span>

<span data-ttu-id="29267-127">ASP.NET Core 3.x ve daha sonra sadece .NET Core hedefleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="29267-127">ASP.NET Core 3.x and later can only target .NET Core.</span></span> <span data-ttu-id="29267-128">Genellikle ASP.NET Core [,NET Standart](/dotnet/standard/net-standard) kitaplıklarından oluşur.</span><span class="sxs-lookup"><span data-stu-id="29267-128">Generally, ASP.NET Core is composed of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="29267-129">.NET Standard 2.0 ile yazılmış [kitaplıklar .NET Standard 2.0'ı uygulayan](/dotnet/standard/net-standard#net-implementation-support)herhangi bir .NET platformunda çalışır.</span><span class="sxs-lookup"><span data-stu-id="29267-129">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="29267-130">.NET Core hedeflemesinin çeşitli avantajları vardır ve bu avantajlar her yeni sürümle birlikte artmaktadır.</span><span class="sxs-lookup"><span data-stu-id="29267-130">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="29267-131">.NET Framework'e göre .NET Core'un bazı avantajları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="29267-131">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="29267-132">Platformlar arası.</span><span class="sxs-lookup"><span data-stu-id="29267-132">Cross-platform.</span></span> <span data-ttu-id="29267-133">Windows, macOS ve Linux'ta çalışır.</span><span class="sxs-lookup"><span data-stu-id="29267-133">Runs on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="29267-134">Geliştirilmiş performans</span><span class="sxs-lookup"><span data-stu-id="29267-134">Improved performance</span></span>
* [<span data-ttu-id="29267-135">Yan yana sürüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="29267-135">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* <span data-ttu-id="29267-136">Yeni API'ler</span><span class="sxs-lookup"><span data-stu-id="29267-136">New APIs</span></span>
* <span data-ttu-id="29267-137">Açık kaynak</span><span class="sxs-lookup"><span data-stu-id="29267-137">Open source</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="29267-138">Önerilen öğrenme yolu</span><span class="sxs-lookup"><span data-stu-id="29267-138">Recommended learning path</span></span>

<span data-ttu-id="29267-139">ASP.NET Core uygulamaları geliştirmeye giriş için aşağıdaki öğreticidizisini öneririz:</span><span class="sxs-lookup"><span data-stu-id="29267-139">We recommend the following sequence of tutorials for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="29267-140">Geliştirmek veya sürdürmek istediğiniz uygulama türü için bir öğretici izleyin.</span><span class="sxs-lookup"><span data-stu-id="29267-140">Follow a tutorial for the app type you want to develop or maintain.</span></span>

   |<span data-ttu-id="29267-141">Uygulama türü</span><span class="sxs-lookup"><span data-stu-id="29267-141">App type</span></span>  |<span data-ttu-id="29267-142">Senaryo</span><span class="sxs-lookup"><span data-stu-id="29267-142">Scenario</span></span>  |<span data-ttu-id="29267-143">Öğretici</span><span class="sxs-lookup"><span data-stu-id="29267-143">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="29267-144">Web uygulaması</span><span class="sxs-lookup"><span data-stu-id="29267-144">Web app</span></span>                   | <span data-ttu-id="29267-145">Yeni sunucu tarafı web ui geliştirme</span><span class="sxs-lookup"><span data-stu-id="29267-145">New server-side web UI development</span></span> |[<span data-ttu-id="29267-146">Razor Sayfaları kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="29267-146">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="29267-147">Web uygulaması</span><span class="sxs-lookup"><span data-stu-id="29267-147">Web app</span></span>                   | <span data-ttu-id="29267-148">Bir MVC uygulamasını koruma</span><span class="sxs-lookup"><span data-stu-id="29267-148">Maintaining an MVC app</span></span> |[<span data-ttu-id="29267-149">MVC ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="29267-149">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="29267-150">Web uygulaması</span><span class="sxs-lookup"><span data-stu-id="29267-150">Web app</span></span>                   | <span data-ttu-id="29267-151">İstemci tarafı web ui geliştirme</span><span class="sxs-lookup"><span data-stu-id="29267-151">Client-side web UI development</span></span> |[<span data-ttu-id="29267-152">Blazor ile başlayın</span><span class="sxs-lookup"><span data-stu-id="29267-152">Get started with Blazor</span></span>](xref:tutorials/first-blazor-app) |
   |<span data-ttu-id="29267-153">Web API</span><span class="sxs-lookup"><span data-stu-id="29267-153">Web API</span></span>                   | <span data-ttu-id="29267-154">RESTful HTTP hizmetleri</span><span class="sxs-lookup"><span data-stu-id="29267-154">RESTful HTTP services</span></span> |<span data-ttu-id="29267-155">[Web API'si oluşturma](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="29267-155">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="29267-156">Uzaktan Yordam Çağrı uygulaması</span><span class="sxs-lookup"><span data-stu-id="29267-156">Remote Procedure Call app</span></span> | <span data-ttu-id="29267-157">Protokol Arabelleklerini kullanarak sözleşme ilk hizmetleri</span><span class="sxs-lookup"><span data-stu-id="29267-157">Contract-first services using Protocol Buffers</span></span> |[<span data-ttu-id="29267-158">gRPC hizmetini kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="29267-158">Get started with a gRPC service</span></span>](xref:tutorials/grpc/grpc-start) |
   |<span data-ttu-id="29267-159">Gerçek zamanlı uygulama</span><span class="sxs-lookup"><span data-stu-id="29267-159">Real-time app</span></span>             | <span data-ttu-id="29267-160">Sunucular ve bağlı istemciler arasında çift yönlü iletişim</span><span class="sxs-lookup"><span data-stu-id="29267-160">Bidirectional communication between servers and connected clients</span></span> |[<span data-ttu-id="29267-161">SignalR ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="29267-161">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="29267-162">Temel veri erişiminin nasıl yapılacağını gösteren bir öğretici izleyin.</span><span class="sxs-lookup"><span data-stu-id="29267-162">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="29267-163">Senaryo</span><span class="sxs-lookup"><span data-stu-id="29267-163">Scenario</span></span>  |<span data-ttu-id="29267-164">Öğretici</span><span class="sxs-lookup"><span data-stu-id="29267-164">Tutorial</span></span>  |
   |----------|----------|
   |<span data-ttu-id="29267-165">Yeni gelişme</span><span class="sxs-lookup"><span data-stu-id="29267-165">New development</span></span>        |[<span data-ttu-id="29267-166">Entity Framework Core ile Razor Pages</span><span class="sxs-lookup"><span data-stu-id="29267-166">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   |<span data-ttu-id="29267-167">Bir MVC uygulamasını koruma</span><span class="sxs-lookup"><span data-stu-id="29267-167">Maintaining an MVC app</span></span> |[<span data-ttu-id="29267-168">Entity Framework Core ile MVC</span><span class="sxs-lookup"><span data-stu-id="29267-168">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="29267-169">Tüm uygulama türleri için geçerli olan ASP.NET Temel [temelleri](xref:fundamentals/index) hakkında genel bir bakış okuyun.</span><span class="sxs-lookup"><span data-stu-id="29267-169">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="29267-170">İlgi nizi çeken diğer konular için içerik tablosuna göz atın.</span><span class="sxs-lookup"><span data-stu-id="29267-170">Browse the table of contents for other topics of interest.</span></span>

<span data-ttu-id="29267-171">&dagger;Ayrıca interaktif bir [web API öğretici](/learn/modules/build-web-api-net-core)var.</span><span class="sxs-lookup"><span data-stu-id="29267-171">&dagger;There's also an [interactive web API tutorial](/learn/modules/build-web-api-net-core).</span></span> <span data-ttu-id="29267-172">Geliştirme araçlarının yerel yüklemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="29267-172">No local installation of development tools is required.</span></span> <span data-ttu-id="29267-173">Kod tarayıcınızda bir [Azure Bulut Kabuğunda](https://azure.microsoft.com/features/cloud-shell/) çalışır ve [curl](https://curl.haxx.se/) test etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="29267-173">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) in your browser, and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="29267-174">.NET Framework'den geçirin</span><span class="sxs-lookup"><span data-stu-id="29267-174">Migrate from .NET Framework</span></span>

<span data-ttu-id="29267-175">Core'a ASP.NET 4.x uygulamaları ASP.NET için bir <xref:migration/proper-to-2x/index>başvuru kılavuzu için bkz.</span><span class="sxs-lookup"><span data-stu-id="29267-175">For a reference guide to migrating ASP.NET 4.x apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="29267-176">ASP.NET Core, modern, bulut özellikli, Internet'e bağlı uygulamalar oluşturmak için bir çapraz platform, yüksek performanslı, [açık kaynak kodlu](https://github.com/dotnet/aspnetcore) bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="29267-176">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="29267-177">ASP.NET Core ile şunları yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="29267-177">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="29267-178">Web uygulamaları ve hizmetleri, [Nesnelerin İnterneti (IoT)](https://www.microsoft.com/internet-of-things/) uygulamaları ve mobil arka uçlar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="29267-178">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="29267-179">Windows, macOS ve Linux üzerinde tercih ettiğiniz geliştirme araçlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="29267-179">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="29267-180">Buluta veya şirket içine dağıtın.</span><span class="sxs-lookup"><span data-stu-id="29267-180">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="29267-181">[.NET Core veya .NET Framework](/dotnet/articles/standard/choosing-core-framework-server) üzerinde çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="29267-181">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="29267-182">Neden ASP.NET Core'u seçmelisiniz?</span><span class="sxs-lookup"><span data-stu-id="29267-182">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="29267-183">Milyonlarca geliştirici web uygulamaları oluşturmak için [4.x ASP.NET](/aspnet/overview) kullanır veya kullanır.</span><span class="sxs-lookup"><span data-stu-id="29267-183">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="29267-184">ASP.NET Core, ASP.NET 4.x sürümünün daha yalın, daha modüler bir çerçeve elde edilmesini sağlayan mimari değişikliklerle yeniden tasarlanmış halidir.</span><span class="sxs-lookup"><span data-stu-id="29267-184">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="29267-185">ASP.NET Core MVC kullanarak web API'leri ve web kullanıcı arabirimi oluşturma</span><span class="sxs-lookup"><span data-stu-id="29267-185">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="29267-186">ASP.NET Core MVC, [web API’leri](xref:tutorials/first-web-api) ve [web uygulamaları](xref:tutorials/razor-pages/index) oluşturmaya yönelik özellikler sağlar:</span><span class="sxs-lookup"><span data-stu-id="29267-186">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="29267-187">[Model Görünümü Denetleyicisi (MVC) deseni,](xref:mvc/overview) web API'lerinizi ve web uygulamalarınızı test edilebilir hale getirmeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="29267-187">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="29267-188">[Razor Pages](xref:razor-pages/index), web kullanıcı arabirimi oluşturmayı daha kolay ve üretken bir hale getiren, sayfa tabanlı bir programlama modelidir.</span><span class="sxs-lookup"><span data-stu-id="29267-188">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="29267-189">[Razor işaretlemesi](xref:mvc/views/razor), [Razor Sayfaları](xref:razor-pages/index) ve [MVC görünümleri](xref:mvc/views/overview) için üretken bir söz dizimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="29267-189">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="29267-190">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), Razor dosyalarında HTML öğelerinin oluşturulmasına ve işlenmesine sunucu tarafı kodun katılmasını etkinleştir.</span><span class="sxs-lookup"><span data-stu-id="29267-190">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="29267-191">[Birden çok veri biçimi ve içerik anlaşması](xref:web-api/advanced/formatting) için sunulan yerleşik destek, web API'lerinizin tarayıcılar ve mobil cihazlar dahil olmak üzere birçok çeşit istemciye ulaşmasına imkan tanır.</span><span class="sxs-lookup"><span data-stu-id="29267-191">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="29267-192">[Model bağlama](xref:mvc/models/model-binding), HTTP isteklerinden alınan verileri otomatik olarak eylem metodu parametreleriyle eşleştirir.</span><span class="sxs-lookup"><span data-stu-id="29267-192">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="29267-193">[Model doğrulama](xref:mvc/models/validation), otomatik olarak istemci ve sunucu tarafı doğrulama gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="29267-193">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="29267-194">İstemci tarafı geliştirme</span><span class="sxs-lookup"><span data-stu-id="29267-194">Client-side development</span></span>

<span data-ttu-id="29267-195">ASP.NET Core [blazor](xref:blazor/index)dahil popüler istemci tarafı çerçeveleri ve kütüphaneleri ile sorunsuz entegre, [Açısal](xref:spa/angular), [React](xref:spa/react), ve [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="29267-195">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="29267-196">Daha fazla bilgi <xref:blazor/index> için, bkz ve *Müşteri tarafı geliştirme*altında ilgili konular.</span><span class="sxs-lookup"><span data-stu-id="29267-196">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="29267-197">.NET Framework'ü hedefleyen ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29267-197">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="29267-198">ASP.NET Core 2.x, .NET Core'u veya .NET Framework'ü hedefleyebilir.</span><span class="sxs-lookup"><span data-stu-id="29267-198">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="29267-199">.NET Framework'ü hedefleyen ASP.NET Core uygulamaları platformlar arası çalışmaz; bunlar yalnızca Windows üzerinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="29267-199">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="29267-200">Genel olarak, ASP.NET Core 2.x [.NET Standard](/dotnet/standard/net-standard) kitaplıklarından oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="29267-200">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="29267-201">.NET Standard 2.0 ile yazılmış [kitaplıklar .NET Standard 2.0'ı uygulayan](/dotnet/standard/net-standard#net-implementation-support)herhangi bir .NET platformunda çalışır.</span><span class="sxs-lookup"><span data-stu-id="29267-201">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="29267-202">ASP.NET Core 2.x .NET Standard 2.0 uygulayan .NET Framework sürümlerinde desteklenir:</span><span class="sxs-lookup"><span data-stu-id="29267-202">ASP.NET Core 2.x is supported on .NET Framework versions that implement .NET Standard 2.0:</span></span>

* <span data-ttu-id="29267-203">.NET Framework en son sürümü önerilir.</span><span class="sxs-lookup"><span data-stu-id="29267-203">.NET Framework latest version is recommended.</span></span>
* <span data-ttu-id="29267-204">.NET Framework 4.6.1 ve üzeri.</span><span class="sxs-lookup"><span data-stu-id="29267-204">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="29267-205">ASP.NET Core 3.0 ve üzeri yalnızca .NET Core’da çalışır.</span><span class="sxs-lookup"><span data-stu-id="29267-205">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="29267-206">Bu değişiklik hakkında daha fazla bilgi için bkz. [ASP.NET Core 3.0’daki değişikliklere ilk bakış](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="29267-206">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="29267-207">.NET Core hedeflemesinin çeşitli avantajları vardır ve bu avantajlar her yeni sürümle birlikte artmaktadır.</span><span class="sxs-lookup"><span data-stu-id="29267-207">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="29267-208">.NET Framework'e göre .NET Core'un bazı avantajları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="29267-208">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="29267-209">Platformlar arası.</span><span class="sxs-lookup"><span data-stu-id="29267-209">Cross-platform.</span></span> <span data-ttu-id="29267-210">macOS, Linux ve Windows üzerinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="29267-210">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="29267-211">Geliştirilmiş performans</span><span class="sxs-lookup"><span data-stu-id="29267-211">Improved performance</span></span>
* [<span data-ttu-id="29267-212">Yan yana sürüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="29267-212">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* <span data-ttu-id="29267-213">Yeni API'ler</span><span class="sxs-lookup"><span data-stu-id="29267-213">New APIs</span></span>
* <span data-ttu-id="29267-214">Açık kaynak</span><span class="sxs-lookup"><span data-stu-id="29267-214">Open source</span></span>

<span data-ttu-id="29267-215">.NET Framework ile .NET Core arasındaki API açığını kapatmak için çok çalışıyoruz.</span><span class="sxs-lookup"><span data-stu-id="29267-215">We're working hard to close the API gap from .NET Framework to .NET Core.</span></span> <span data-ttu-id="29267-216">[Windows Uyumluluk Paketi](/dotnet/core/porting/windows-compat-pack), .NET Core'da yalnızca Windows'a yönelik binlerce API'yi kullanıma sunmaktadır.</span><span class="sxs-lookup"><span data-stu-id="29267-216">The [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="29267-217">Bu API'ler .NET Core 1.x'te sağlanmamıştı.</span><span class="sxs-lookup"><span data-stu-id="29267-217">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="29267-218">Önerilen öğrenme yolu</span><span class="sxs-lookup"><span data-stu-id="29267-218">Recommended learning path</span></span>

<span data-ttu-id="29267-219">ASP.NET Core uygulamaları geliştirmeye başlamak için şu öğreticileri ve makaleleri takip etmenizi öneririz:</span><span class="sxs-lookup"><span data-stu-id="29267-219">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="29267-220">Geliştirmek veya sürdürmek istediğiniz uygulama türü için bir öğretici izleyin.</span><span class="sxs-lookup"><span data-stu-id="29267-220">Follow a tutorial for the type of app you want to develop or maintain.</span></span>

   |<span data-ttu-id="29267-221">Uygulama türü</span><span class="sxs-lookup"><span data-stu-id="29267-221">App type</span></span>  |<span data-ttu-id="29267-222">Senaryo</span><span class="sxs-lookup"><span data-stu-id="29267-222">Scenario</span></span>  |<span data-ttu-id="29267-223">Öğretici</span><span class="sxs-lookup"><span data-stu-id="29267-223">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="29267-224">Web uygulaması</span><span class="sxs-lookup"><span data-stu-id="29267-224">Web app</span></span>                   | <span data-ttu-id="29267-225">Yeni proje geliştirmek için</span><span class="sxs-lookup"><span data-stu-id="29267-225">For new development</span></span>        |[<span data-ttu-id="29267-226">Razor Sayfaları kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="29267-226">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="29267-227">Web uygulaması</span><span class="sxs-lookup"><span data-stu-id="29267-227">Web app</span></span>                   | <span data-ttu-id="29267-228">MVC uygulaması yönetmek için</span><span class="sxs-lookup"><span data-stu-id="29267-228">For maintaining an MVC app</span></span> |[<span data-ttu-id="29267-229">MVC ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="29267-229">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="29267-230">Web API</span><span class="sxs-lookup"><span data-stu-id="29267-230">Web API</span></span>                   |                            |<span data-ttu-id="29267-231">[Web API'si oluşturma](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="29267-231">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="29267-232">Gerçek zamanlı uygulama</span><span class="sxs-lookup"><span data-stu-id="29267-232">Real-time app</span></span>             |                            |[<span data-ttu-id="29267-233">SignalR ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="29267-233">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="29267-234">Temel veri erişiminin nasıl yapılacağını gösteren bir öğretici izleyin.</span><span class="sxs-lookup"><span data-stu-id="29267-234">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="29267-235">Senaryo</span><span class="sxs-lookup"><span data-stu-id="29267-235">Scenario</span></span>  |<span data-ttu-id="29267-236">Öğretici</span><span class="sxs-lookup"><span data-stu-id="29267-236">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="29267-237">Yeni proje geliştirmek için</span><span class="sxs-lookup"><span data-stu-id="29267-237">For new development</span></span>        |[<span data-ttu-id="29267-238">Entity Framework Core ile Razor Pages</span><span class="sxs-lookup"><span data-stu-id="29267-238">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   | <span data-ttu-id="29267-239">MVC uygulaması yönetmek için</span><span class="sxs-lookup"><span data-stu-id="29267-239">For maintaining an MVC app</span></span> |[<span data-ttu-id="29267-240">Entity Framework Core ile MVC</span><span class="sxs-lookup"><span data-stu-id="29267-240">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="29267-241">Tüm uygulama türleri için geçerli olan ASP.NET Temel [temelleri](xref:fundamentals/index) hakkında genel bir bakış okuyun.</span><span class="sxs-lookup"><span data-stu-id="29267-241">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="29267-242">İlgilendiğiniz diğer konular için İçindekiler Tablosu’na göz atın.</span><span class="sxs-lookup"><span data-stu-id="29267-242">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="29267-243">&dagger;Ayrıca [tarayıcıda tamamen takip bir web API öğretici](/learn/modules/build-web-api-net-core)var, hiçbir yerel IDE yükleme gerekli.</span><span class="sxs-lookup"><span data-stu-id="29267-243">&dagger;There's also a [web API tutorial that you follow entirely in the browser](/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span> <span data-ttu-id="29267-244">Kod [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)’de çalışır, [curl](https://curl.haxx.se/) ise test için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="29267-244">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="29267-245">.NET Framework'den geçirin</span><span class="sxs-lookup"><span data-stu-id="29267-245">Migrate from .NET Framework</span></span>

<span data-ttu-id="29267-246">ASP.NET Core'a ASP.NET uygulamaları nın geçişini gösteren bir başvuru kılavuzu için bkz. <xref:migration/proper-to-2x/index></span><span class="sxs-lookup"><span data-stu-id="29267-246">For a reference guide to migrating ASP.NET apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

## <a name="how-to-download-a-sample"></a><span data-ttu-id="29267-247">Örnek indirme</span><span class="sxs-lookup"><span data-stu-id="29267-247">How to download a sample</span></span>

<span data-ttu-id="29267-248">Çoğu makale ve öğretici örnek koda bağlantılar içerir.</span><span class="sxs-lookup"><span data-stu-id="29267-248">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="29267-249">[ASP.NET depo zip dosyasını indirin](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="29267-249">[Download the ASP.NET repository zip file](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span></span>
1. <span data-ttu-id="29267-250">*Docs-master.zip* dosyasının sıkıştırmasını açın.</span><span class="sxs-lookup"><span data-stu-id="29267-250">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="29267-251">Örnek dizinde gezinmek için örnek bağlantıdaki URL’yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="29267-251">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="29267-252">Örnek kodda ön işlemci yönergeleri</span><span class="sxs-lookup"><span data-stu-id="29267-252">Preprocessor directives in sample code</span></span>

<span data-ttu-id="29267-253">Örnek uygulamalar, birden çok senaryo `#define` `#if-#else/#elif-#endif` göstermek için, örnek kodun farklı bölümlerini seçip derlemek ve çalıştırmak için ve önişlemci yönergelerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="29267-253">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` preprocessor directives to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="29267-254">Bu yaklaşımı kullanan örnekler için, çalıştırmak `#define` istediğiniz senaryoyla ilişkili simgeyi tanımlamak için yönergeyi C# dosyalarının en üstünde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="29267-254">For those samples that make use of this approach, set the `#define` directive at the top of the C# files to define the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="29267-255">Bazı örnekler, bir senaryoyu çalıştırmak için birden çok dosyanın üstündeki sembolün tanımlanmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="29267-255">Some samples require defining the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="29267-256">Örneğin, aşağıdaki simge listesi `#define` dört senaryonun kullanılabilir olduğunu gösterir (her simge için bir senaryo).</span><span class="sxs-lookup"><span data-stu-id="29267-256">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="29267-257">Geçerli örnek yapılandırması `TemplateCode` senaryosunu çalıştırır:</span><span class="sxs-lookup"><span data-stu-id="29267-257">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="29267-258">Örneği `ExpandDefault` senaryosunu çalıştıracak şekilde değiştirmek için `ExpandDefault` simgesini tanımlayın ve kalan simgeleri açıklama satırı yapılmış şekilde bırakın:</span><span class="sxs-lookup"><span data-stu-id="29267-258">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="29267-259">Kod bölümlerini seçmeli olarak derlemek üzere [C# ön işlemci yönergelerini](/dotnet/csharp/language-reference/preprocessor-directives/) kullanma hakkında daha fazla bilgi için bkz. [#define (C# Başvurusu)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) ve [#if (C# Başvurusu)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span><span class="sxs-lookup"><span data-stu-id="29267-259">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="29267-260">Örnek kodda bölgeler</span><span class="sxs-lookup"><span data-stu-id="29267-260">Regions in sample code</span></span>

<span data-ttu-id="29267-261">Bazı örnek [uygulamalar, #region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) ve [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# yönergeleriyle çevrili kod bölümlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="29267-261">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# directives.</span></span> <span data-ttu-id="29267-262">Belge derleme sistemi bu bölgeleri işlenmiş belge konularının içine ekler.</span><span class="sxs-lookup"><span data-stu-id="29267-262">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="29267-263">Bölge adları çoğunlukla şu sözcüğü içerir: "snippet."</span><span class="sxs-lookup"><span data-stu-id="29267-263">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="29267-264">Aşağıdaki örnekte `snippet_WebHostDefaults` adlı bir bölge gösterilir:</span><span class="sxs-lookup"><span data-stu-id="29267-264">The following example shows a region named `snippet_WebHostDefaults`:</span></span>

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

<span data-ttu-id="29267-265">Önündeki C# kod parçacığına, konunun markdown dosyasında aşağıdaki satırla başvurulur:</span><span class="sxs-lookup"><span data-stu-id="29267-265">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

<span data-ttu-id="29267-266">Kodu çevreleyen yönergeleri `#region` ve yönergeleri güvenle `#endregion` yok sayabilir (veya kaldırabilirsiniz).</span><span class="sxs-lookup"><span data-stu-id="29267-266">You may safely ignore (or remove) the `#region` and `#endregion` directives that surround the code.</span></span> <span data-ttu-id="29267-267">Konuyla açıklanan örnek senaryoları çalıştırmayı planlıyorsanız, bu yönergeler içindeki kodu değiştirmeyin.</span><span class="sxs-lookup"><span data-stu-id="29267-267">Don't alter the code within these directives if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="29267-268">Başka senaryolarla denemeler yaparken kodu rahatça değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="29267-268">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="29267-269">Daha fazla bilgi için bkz. [ASP.NET belgelerine katkıda bulunma: Kod parçacıkları](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span><span class="sxs-lookup"><span data-stu-id="29267-269">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="29267-270">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="29267-270">Next steps</span></span>

<span data-ttu-id="29267-271">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="29267-271">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="29267-272">ASP.NET Core temelleri</span><span class="sxs-lookup"><span data-stu-id="29267-272">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="29267-273">[Haftalık ASP.NET topluluğu toplantısında](https://live.asp.net/), takımın ilerleme durumu ve planları ele alınır.</span><span class="sxs-lookup"><span data-stu-id="29267-273">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="29267-274">Yeni bloglara ve üçüncü taraf yazılıma yer verilir.</span><span class="sxs-lookup"><span data-stu-id="29267-274">It features new blogs and third-party software.</span></span>
