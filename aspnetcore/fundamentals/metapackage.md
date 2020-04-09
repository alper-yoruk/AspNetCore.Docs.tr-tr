---
title: Microsoft.AspNetCore.ASP.NET Core 2.0 için tüm metapackage
author: Rick-Anderson
description: Microsoft.AspNetCore.All metapackage ASP.NET Core 2.1 ve sonrası için tavsiye edilmez.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/25/2018
uid: fundamentals/metapackage
ms.openlocfilehash: cc00c075909da5c17a4aa2fd252c9e662e5a0fc9
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511073"
---
# <a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-20"></a>Microsoft.AspNetCore.ASP.NET Core 2.0 için tüm metapackage

::: moniker range=">= aspnetcore-3.0"

Metapaket `Microsoft.AspNetCore.All` ASP.NET Core 3.0 ve sonraki dahildir. Daha fazla bilgi için [bu GitHub sorununa](https://github.com/aspnet/Announcements/issues/314)bakın.

::: moniker-end

> [!NOTE]
> Core 2.1ASP.NET hedefleyen uygulamaları öneririz ve daha sonra bu paket yerine [Microsoft.AspNetCore.App metapaketini](xref:fundamentals/metapackage-app) kullanırız. Bu makalede [Bkz. Microsoft.AspNetCore.All'dan Microsoft.AspNetCore.App'e geçiş.](#migrate)

Bu özellik ASP.NET Core 2.x hedefleme .NET Core 2.x gerektirir.

[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) paylaşılan bir çerçeveye başvuran bir metapakettir. *Paylaşılan çerçeve,* uygulamanın klasörlerinde *.dll* olmayan bir derlemeler kümesidir. Uygulamayı çalıştırmak için paylaşılan çerçevenin makineye yüklenmesi gerekir. Daha fazla bilgi için [paylaşılan çerçeveye](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)bakın.

`Microsoft.AspNetCore.All` Başvuran paylaşılan çerçeve şunları içerir:

* tüm desteklenmiş paketler ASP.NET Core ekibi tarafından desteklenir.
* Tüm desteklenen paketler Entity Framework Core tarafından desteklenir.
* ASP.NET Çekirdek ve Varlık Çerçeve Çekirdeği tarafından kullanılan iç ve üçüncü taraf bağımlılıkları.

ASP.NET Core 2.x ve Entity Framework Core 2.x'in `Microsoft.AspNetCore.All` tüm özellikleri pakete dahildir. Core 2.0 ASP.NET hedefleyen varsayılan proje şablonları bu paketi kullanır.

`Microsoft.AspNetCore.All` Metapaketin sürüm numarası minimum ASP.NET Core sürümünü ve Entity Framework Core sürümünü temsil eder.

Aşağıdaki *.csproj* dosyası `Microsoft.AspNetCore.All` ASP.NET Core için meta paketine başvurur:

[!code-xml[](metapackage/samples/Metapackage.All.Example.csproj?highlight=8)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implicit-versioning"></a>Örtük sürüm

ASP.NET Core 2.1 veya sonraki sürümlerde paket başvurularını `Microsoft.AspNetCore.All` belirtebilirsiniz. Sürüm belirtilmediği zaman, Örtülü bir sürüm SDK`Microsoft.NET.Sdk.Web`( ) tarafından belirtilir. SDK tarafından belirtilen örtük sürüme güvenmenizi ve sürüm numarasını paket başvurusuna açıkça ayarlamamanızı öneririz. Bu yaklaşım la ilgili sorularınız varsa, [Microsoft.AspNetCore.App örtülü sürümü için Tartışma](https://github.com/dotnet/AspNetCore.Docs/issues/6430)bir GitHub yorum bırakın.

Örtük sürüm taşınabilir `major.minor.0` uygulamalar için ayarlanır. Paylaşılan çerçeve roll-forward mekanizması, uygulamayı yüklenen paylaşılan çerçeveler arasında en son uyumlu sürümde çalıştırıyor. Aynı sürümün geliştirme, test ve üretimde kullanıldığını garanti etmek için paylaşılan çerçevenin aynı sürümünün tüm ortamlarda yüklendiğinden emin olun. Bağımsız uygulamalar için örtük sürüm numarası, `major.minor.patch` yüklenen SDK'da bulunan paylaşılan çerçevenin bölümüne ayarlanır.

`Microsoft.AspNetCore.All` Paket başvurusuüzerinde bir sürüm numarası belirtmek, paylaşılan çerçevenin sürümünün seçildiğini garanti **etmez.** Örneğin, "2.1.1" sürümünün belirtildiğini, ancak "2.1.3" sürümünün yüklü olduğunu varsayalım. Bu durumda, uygulama "2.1.3" kullanır. Önerilmese de, ileri yuvarlamayı (yama ve/veya küçük) devre dışı kullanabilirsiniz. Dotnet ana bilgisayar roll-forward ve davranışını nasıl yapılandırışla ilgili daha fazla bilgi için [dotnet ana bilgisayar adedine](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md)bakın.

Projenin SDK'sı, `Microsoft.NET.Sdk.Web` `Microsoft.AspNetCore.All`'nin örtülü sürümünü kullanmak için proje dosyasında ayarlanmalıdır. `Microsoft.NET.Sdk` SDK belirtildiğinde (proje`<Project Sdk="Microsoft.NET.Sdk">` dosyasının üst kısmında), aşağıdaki uyarı oluşturulur:

*Uyarı NU1604: Proje bağımlılığı Microsoft.AspNetCore.All kapsayıcı bir alt sınır içermez. Tutarlı geri yükleme sonuçları sağlamak için bağımlılık sürümüne daha düşük bir sınır ekleyin.*

Bu.NET Core 2.1 SDK ile bilinen bir sorundur ve .NET Core 2.2 SDK'da düzeltilecektir.

::: moniker-end

<a name="migrate"></a>

## <a name="migrating-from-microsoftaspnetcoreall-to-microsoftaspnetcoreapp"></a>Microsoft.AspNetCore.All'dan Microsoft.AspNetCore.App'e Geçiş

Aşağıdaki paketler pakete `Microsoft.AspNetCore.All` dahil edilir, ancak pakete `Microsoft.AspNetCore.App` dahil edilmez.

* `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServices.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServicesIntegration`
* `Microsoft.AspNetCore.DataProtection.AzureKeyVault`
* `Microsoft.AspNetCore.DataProtection.AzureStorage`
* `Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`
* `Microsoft.AspNetCore.SignalR.Redis`
* `Microsoft.Data.Sqlite`
* `Microsoft.Data.Sqlite.Core`
* `Microsoft.EntityFrameworkCore.Sqlite`
* `Microsoft.EntityFrameworkCore.Sqlite.Core`
* `Microsoft.Extensions.Caching.Redis`
* `Microsoft.Extensions.Configuration.AzureKeyVault`
* `Microsoft.Extensions.Logging.AzureAppServices`
* `Microsoft.VisualStudio.Web.BrowserLink`

`Microsoft.AspNetCore.All` Uygulamanız yukarıdaki paketlerden veya bu paketler tarafından getirilen paketlerden herhangi bir API kullanıyorsa, projenizdeki paketlere referanslar `Microsoft.AspNetCore.App`ekleyin.

Önceki paketlerin bağımlılıkları dolaylı `Microsoft.AspNetCore.App` olarak dahil edilmez. Örneğin:

* `StackExchange.Redis`bir bağımlılık olarak`Microsoft.Extensions.Caching.Redis`
* `Microsoft.ApplicationInsights`bir bağımlılık olarak`Microsoft.AspNetCore.ApplicationInsights.HostingStartup`

## <a name="update-aspnet-core-21"></a>Core 2.1ASP.NET güncelleştir

Meta pakete `Microsoft.AspNetCore.App` 2.1 ve sonrası için geçiş yapmanızı öneririz. `Microsoft.AspNetCore.All` Metapaketi kullanmaya devam etmek ve en son yama sürümünün dağıtıldığından emin olmak için:

* Geliştirme makineleri ve yapı sunucularında: En son [.NET Core SDK'yı](https://dotnet.microsoft.com/download)yükleyin.
* Dağıtım sunucularında: En son [.NET Core çalışma süresini](https://dotnet.microsoft.com/download)yükleyin.
 Uygulamanız, uygulamanın yeniden başlatılmasında en son yüklenen sürüme geçecektir.
