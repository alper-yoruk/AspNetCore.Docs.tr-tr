---
title: ASP.NET Core için Microsoft. AspNetCore. app metapackage
author: Rick-Anderson
description: Microsoft. AspNetCore. app Shared Framework
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
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
uid: fundamentals/metapackage-app
ms.openlocfilehash: 225bb1f55c099a476319191726c5a661f9a4893a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057810"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a>ASP.NET Core için Microsoft. AspNetCore. app

::: moniker range=">= aspnetcore-3.0"

 ASP.NET Core paylaşılan Framework ( `Microsoft.AspNetCore.App` ), Microsoft tarafından geliştirilen ve desteklenen derlemeler içerir. `Microsoft.AspNetCore.App` , [.NET Core 3,0 veya sonraki BIR SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) yüklendiğinde yüklenir. *Paylaşılan çerçeve* , makinede yüklü olan derlemeler ( *. dll* dosyaları) kümesidir ve bir çalışma zamanı bileşeni ve hedefleme paketi içerir. Daha fazla bilgi için bkz. [paylaşılan çerçeve](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

* SDK 'yı hedefleyen projeler `Microsoft.NET.Sdk.Web` çerçeveye dolaylı olarak başvurur `Microsoft.AspNetCore.App` .

Bu projeler için ek başvuru gerekli değildir:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

ASP.NET Core paylaşılan çerçeve:

* Üçüncü taraf bağımlılıklarını içermez.
* ASP.NET Core ekibi tarafından desteklenen tüm paketleri içerir.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu özellik, .NET Core 2. x 'i hedefleyen ASP.NET Core 2. x gerektirir.

ASP.NET Core için [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [metapackage](/dotnet/core/packages#metapackages) :

* [JSON.net](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion. LINQ](https://www.nuget.org/packages/Remotion.Linq/)ve [x zaman uyumsuz](https://www.nuget.org/packages/System.Interactive.Async/)dışında üçüncü taraf bağımlılıklarını içermez. Bu üçüncü taraf bağımlılıklar, ana çerçeveler özelliklerinin çalışmasını sağlamak için gereklidir.
* ASP.NET Core ekibine, üçüncü taraf bağımlılıklar (daha önce bahsedilen) dışında, desteklenen tüm paketleri içerir.
* Entity Framework Core ekibine, üçüncü taraf bağımlılıklar (daha önce bahsedilen) dışında, desteklenen tüm paketleri içerir.

ASP.NET Core 2. x ve Entity Framework Core 2. x özelliklerinin tümü `Microsoft.AspNetCore.App` pakete dahildir. ASP.NET Core 2. x ' i hedefleyen varsayılan proje şablonları bu paketi kullanın. ASP.NET Core 2. x ve Entity Framework Core 2. x ' i hedefleyen uygulamaların paketini kullanması önerilir `Microsoft.AspNetCore.App` .

`Microsoft.AspNetCore.App`Metapackage sürüm numarası en düşük ASP.NET Core sürümü ve Entity Framework Core sürümünü temsil eder.

`Microsoft.AspNetCore.App`Metapackage 'in kullanılması, uygulamanızı koruyan sürüm kısıtlamalarını sağlar:

* İçindeki bir pakette geçişli (doğrudan) bağımlılığı olan bir paket varsa `Microsoft.AspNetCore.App` ve bu sürüm numaraları farklıysa, NuGet bir hata oluşturur.
* Uygulamanıza eklenen diğer paketler, ' de yer alan paketlerin sürümünü değiştiremez `Microsoft.AspNetCore.App` .
* Sürüm tutarlılığı, güvenilir bir deneyim sağlar. `Microsoft.AspNetCore.App` , ilişkili bitlerin test edilmemiş sürüm birleşimlerinin aynı uygulamada birlikte kullanılmaları önleyecek şekilde tasarlandı.

Metapackage kullanan uygulamalar `Microsoft.AspNetCore.App` ASP.NET Core paylaşılan çerçeveden otomatik olarak yararlanır. `Microsoft.AspNetCore.App`Metapackage kullandığınızda başvurulan ASP.NET Core NuGet paketlerinden **hiçbir** varlık, &mdash; ASP.NET Core paylaşılan Framework 'ün bu varlıkları içerdiği uygulamayla birlikte dağıtılır. Paylaşılan çerçevede bulunan varlıklar, uygulama başlatma süresini artırmak için önceden derlenmiş. Daha fazla bilgi için bkz. [paylaşılan çerçeve](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

Aşağıdaki proje dosyası `Microsoft.AspNetCore.App` ASP.NET Core için metapackage 'e başvurur ve tipik bir ASP.NET Core 2,2 şablonunu temsil eder:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

Yukarıdaki biçimlendirme tipik bir ASP.NET Core 2. x şablonunu temsil eder. Paket başvurusu için bir sürüm numarası belirtmiyor `Microsoft.AspNetCore.App` . Sürüm belirtilmediğinde, SDK tarafından [örtük](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) bir sürüm belirtilir, diğer bir deyişle, `Microsoft.NET.Sdk.Web` . SDK tarafından belirtilen örtük sürüme güvenmek ve paket başvurusunda sürüm numarasını açıkça ayarlamamanız önerilir. Bu yaklaşım hakkında sorularınız varsa, [Microsoft. AspNetCore. app örtük sürümü Için tartışmada](https://github.com/dotnet/AspNetCore.Docs/issues/6430)bir GitHub yorumu bırakın.

Örtük sürüm, `major.minor.0` Taşınabilir uygulamalar için olarak ayarlanır. Paylaşılan Framework toplaması-iletme mekanizması, uygulamayı yüklü paylaşılan Çerçeveler arasındaki en son uyumlu sürümde çalıştırır. Geliştirme, test ve üretimde aynı sürümün kullanıldığını güvence altına almak için, paylaşılan Framework 'ün aynı sürümünün tüm ortamlarda yüklü olduğundan emin olun. Kendi içindeki uygulamalar için, örtük sürüm numarası `major.minor.patch` yüklü SDK 'da paketlenmiş paylaşılan çerçevenin öğesine ayarlanır.

Başvuru üzerinde bir sürüm numarası belirtilmesi, `Microsoft.AspNetCore.App` paylaşılan Çerçeve sürümünün seçilmeyeceği garantisi **vermez** . Örneğin, "2.2.1" sürümünün belirtildiğini, ancak "2.2.3" nin yüklü olduğunu varsayalım. Bu durumda, uygulama "2.2.3" kullanacaktır. Önerilmese de, iletmeyi (Patch ve/veya Minor) devre dışı bırakabilirsiniz. DotNet ana bilgisayar alma hakkında daha fazla bilgi ve davranışını yapılandırma hakkında daha fazla bilgi için bkz. [DotNet Host top Forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).

::: moniker-end

::: moniker range="= aspnetcore-2.1"

`<Project Sdk``Microsoft.NET.Sdk.Web`örtük sürümü kullanmak için olarak ayarlanmalıdır `Microsoft.AspNetCore.App` . Ne zaman `<Project Sdk="Microsoft.NET.Sdk">` (sondaki olmadan `.Web` ) kullanıldığında:

* Aşağıdaki uyarı oluşturulur:

  *Uyarı NU1604: proje bağımlılığı Microsoft. AspNetCore. uygulama, kapsamlı bir alt sınır içermez. Tutarlı geri yükleme sonuçlarının sağlanması için bağımlılık sürümüne bir alt sınır ekleyin.*

* Bu, .NET Core 2,1 SDK ile ilgili bilinen bir sorundur.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a>Güncelleştirme ASP.NET Core

`Microsoft.AspNetCore.App` [Metapackage](/dotnet/core/packages#metapackages) , NuGet 'den güncelleştirilmiş geleneksel bir paket değildir. Benzer şekilde `Microsoft.NETCore.App` , `Microsoft.AspNetCore.App` NuGet dışında işlenen özel sürüm oluşturma semantiğinin bulunduğu paylaşılan bir çalışma zamanını temsil eder. Daha fazla bilgi için bkz. [paketler, Metapackages ve çerçeveler](/dotnet/core/packages).

ASP.NET Core güncelleştirmek için:

* Geliştirme makinelerinde ve yapı sunucularında: [.NET Core SDK](https://dotnet.microsoft.com/download)indirin ve yükleyin.
* Dağıtım sunucularında: [.NET Core çalışma zamanını](https://dotnet.microsoft.com/download)indirin ve yükleyin.

 Uygulamalar, uygulama yeniden başlatıldığında en son yüklenen sürüme ileri alınacaktır. `Microsoft.AspNetCore.App`Proje dosyasındaki sürüm numarasını güncelleştirmek gerekli değildir. Daha fazla bilgi için bkz. [çerçeveye bağımlı uygulamalar ileri alma](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).

Uygulamanız daha önce kullanılıyorsa `Microsoft.AspNetCore.All` bkz. [Microsoft. Aspnetcore. All 'dan Microsoft. Aspnetcore. app 'e geçiş](xref:fundamentals/metapackage#migrate).

::: moniker-end
