---
title: ASP.NET Core 2,0 için Microsoft. AspNetCore. All metapackage
author: Rick-Anderson
description: Microsoft. AspNetCore. All metapackage, ASP.NET Core 2,1 ve üzeri için önerilmez.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/25/2018
no-loc:
- appsettings.json
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
uid: fundamentals/metapackage
ms.openlocfilehash: b739398c2a440f21c8bdfdc1f4d8e25412358a6a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060696"
---
# <a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-20"></a>ASP.NET Core 2,0 için Microsoft. AspNetCore. All metapackage

::: moniker range=">= aspnetcore-3.0"

`Microsoft.AspNetCore.All`Metapackage ASP.NET Core 3,0 ve üzeri bir sürüme dahil değildir. Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/aspnet/Announcements/issues/314)bakın.

::: moniker-end

> [!NOTE]
> ASP.NET Core 2,1 ' i hedefleyen uygulamaların ve daha sonra bu paket yerine [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) kullanılmasını öneririz. Bkz. Bu makaledeki [Microsoft. AspNetCore. All Ile Microsoft. aspnetcore. app 'e geçme](#migrate) .

Bu özellik, .NET Core 2. x 'i hedefleyen ASP.NET Core 2. x gerektirir.

[Microsoft. AspNetCore. All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) , paylaşılan bir çerçeveye başvuran bir metapackage. *Paylaşılan çerçeve* , uygulamanın klasörlerinde olmayan derlemelerin (*. dll* dosyaları) bir kümesidir. Uygulamayı çalıştırmak için makinede paylaşılan çerçeve yüklü olmalıdır. Daha fazla bilgi için bkz. [paylaşılan çerçeve](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Öğesine başvuran paylaşılan çerçeve şunları `Microsoft.AspNetCore.All` içerir:

* ASP.NET Core ekibi tarafından desteklenen tüm paketler.
* Entity Framework Core tarafından desteklenen tüm paketler.
* ASP.NET Core ve Entity Framework Core tarafından kullanılan dahili ve üçüncü taraf bağımlılıklar.

ASP.NET Core 2. x ve Entity Framework Core 2. x özelliklerinin tümü `Microsoft.AspNetCore.All` pakete dahildir. ASP.NET Core 2,0 ' i hedefleyen varsayılan proje şablonları bu paketi kullanır.

`Microsoft.AspNetCore.All`Metapackage sürüm numarası en düşük ASP.NET Core sürümü ve Entity Framework Core sürümünü temsil eder.

Aşağıdaki *. csproj* dosyası `Microsoft.AspNetCore.All` ASP.NET Core metapackage 'e başvuruyor:

[!code-xml[](metapackage/samples/Metapackage.All.Example.csproj?highlight=8)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implicit-versioning"></a>Örtük sürüm oluşturma

ASP.NET Core 2,1 veya üzeri sürümlerde `Microsoft.AspNetCore.All` paket başvurusunu bir sürüm olmadan belirtebilirsiniz. Sürüm belirtilmediğinde, SDK () tarafından örtük bir sürüm belirtilir `Microsoft.NET.Sdk.Web` . SDK tarafından belirtilen örtük sürüme güvenmek ve paket başvurusunda sürüm numarasını açıkça ayarlamamanız önerilir. Bu yaklaşım hakkında sorularınız varsa, [Microsoft. AspNetCore. app örtük sürümü Için tartışmada](https://github.com/dotnet/AspNetCore.Docs/issues/6430)bir GitHub yorumu bırakın.

Örtük sürüm, `major.minor.0` Taşınabilir uygulamalar için olarak ayarlanır. Paylaşılan Framework toplaması-iletme mekanizması, uygulamayı yüklü paylaşılan Çerçeveler arasındaki en son uyumlu sürümde çalıştırır. Geliştirme, test ve üretimde aynı sürümün kullanıldığını güvence altına almak için, paylaşılan Framework 'ün aynı sürümünün tüm ortamlarda yüklü olduğundan emin olun. Kendi içindeki uygulamalar için, örtük sürüm numarası `major.minor.patch` yüklü SDK 'da paketlenmiş paylaşılan çerçevenin öğesine ayarlanır.

Paket başvurusunda bir sürüm numarası belirtilmesi, `Microsoft.AspNetCore.All` paylaşılan Çerçeve sürümünün seçili olduğunu garanti **etmez** . Örneğin, "2.1.1" sürümünün belirtildiğini, ancak "2.1.3" nin yüklü olduğunu varsayalım. Bu durumda, uygulama "2.1.3" kullanacaktır. Önerilmese de, iletmeyi (Patch ve/veya Minor) devre dışı bırakabilirsiniz. DotNet ana bilgisayar alma hakkında daha fazla bilgi ve davranışını yapılandırma hakkında daha fazla bilgi için bkz. [DotNet Host top Forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).

Projenin SDK 'sının `Microsoft.NET.Sdk.Web` örtük sürümünü kullanmak için proje dosyasında olarak ayarlanması gerekir `Microsoft.AspNetCore.All` . `Microsoft.NET.Sdk`SDK belirtildiğinde ( `<Project Sdk="Microsoft.NET.Sdk">` Proje dosyasının en üstünde), aşağıdaki uyarı oluşturulur:

*Uyarı NU1604: proje bağımlılığı Microsoft. AspNetCore. All, kapsamlı bir alt sınır içermez. Tutarlı geri yükleme sonuçlarının sağlanması için bağımlılık sürümüne bir alt sınır ekleyin.*

Bu, .NET Core 2,1 SDK ile ilgili bilinen bir sorundur ve .NET Core 2,2 SDK 'sında düzeltilecektir.

::: moniker-end

<a name="migrate"></a>

## <a name="migrating-from-microsoftaspnetcoreall-to-microsoftaspnetcoreapp"></a>Microsoft. AspNetCore. All 'dan Microsoft. AspNetCore. app 'e geçiş

Aşağıdaki paketler `Microsoft.AspNetCore.All` paketine dahil değildir ancak `Microsoft.AspNetCore.App` pakete eklenmez.

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

`Microsoft.AspNetCore.All` `Microsoft.AspNetCore.App` Uygulamanız, yukarıdaki paketlerin veya bu paketler tarafından getirilen paketlerin herhangi bir API 'sini kullanıyorsa, ' dan ' a geçiş yapmak için projenizdeki bu paketlere başvurular ekleyin.

Önceki paketlerin bağımlılığı olmayan tüm bağımlılıkları `Microsoft.AspNetCore.App` örtük olarak dahil edilmez. Örneğin:

* `StackExchange.Redis` bağımlılığı olarak `Microsoft.Extensions.Caching.Redis`
* `Microsoft.ApplicationInsights` bağımlılığı olarak `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`

## <a name="update-aspnet-core-21"></a>Güncelleştirme ASP.NET Core 2,1

`Microsoft.AspNetCore.App`2,1 ve üzeri için metapackage 'e geçiş yapmanızı öneririz. Metapackage kullanmaya devam etmek `Microsoft.AspNetCore.All` ve en son düzeltme eki sürümünün dağıtıldığından emin olmak için:

* Geliştirme makinelerinde ve yapı sunucularında: en son [.NET Core SDK](https://dotnet.microsoft.com/download)yükler.
* Dağıtım sunucularında: en son [.NET Core çalışma zamanını](https://dotnet.microsoft.com/download)yükler.
 Uygulamanız, uygulama yeniden başlatıldığında en son yüklenen sürüme ileri alınacaktır.
