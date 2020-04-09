---
title: Microsoft.AspNetCore.App ASP.NET Core için metapackage
author: Rick-Anderson
description: Microsoft.AspNetCore.App paylaşılan çerçeve
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
uid: fundamentals/metapackage-app
ms.openlocfilehash: b30c90116f5a53ba487f88544514f36e388233d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511385"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a>Microsoft.AspNetCore.App ASP.NET Core için

::: moniker range=">= aspnetcore-3.0"

 ASP.NET Core paylaşılan`Microsoft.AspNetCore.App`çerçevesi , Microsoft tarafından geliştirilen ve desteklenen derlemeler içerir. `Microsoft.AspNetCore.App`[.NET Core 3.0 veya sonraki SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) yüklendiğinde yüklenir. *Paylaşılan çerçeve,* makineye yüklenen *.dll* ve çalışma zamanı bileşeni ve hedefleme paketi içeren derlemeler kümesidir. Daha fazla bilgi için [paylaşılan çerçeveye](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)bakın.

* SDK'yı `Microsoft.NET.Sdk.Web` hedefleyen projeler çerçeveye dolaylı olarak başvurur. `Microsoft.AspNetCore.App`

Bu projeler için ek başvuru gerekmez:

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

Bu özellik ASP.NET Core 2.x hedefleme .NET Core 2.x gerektirir.

ASP.NET Core için [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [metapaketi:](/dotnet/core/packages#metapackages)

* [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/)ve [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/)dışında üçüncü taraf bağımlılıkları içermez. Bu üçüncü taraf bağımlılıkları, ana çerçeve özelliklerinin işlevini sağlamak için gerekli kabul edilir.
* Üçüncü taraf bağımlılıkları (daha önce bahsedilenler dışında) içerenler dışında ASP.NET Çekirdek ekibi tarafından desteklenen tüm paketleri içerir.
* Üçüncü taraf bağımlılıkları (daha önce bahsedilenler dışında) içerenler dışında Entity Framework Core ekibi tarafından desteklenen tüm paketleri içerir.

ASP.NET Core 2.x ve Entity Framework Core 2.x'in `Microsoft.AspNetCore.App` tüm özellikleri pakete dahildir. Core 2.x ASP.NET hedefleyen varsayılan proje şablonları bu paketi kullanır. Core 2.x ASP.NET hedefleyen uygulamalar ve Entity Framework Core `Microsoft.AspNetCore.App` 2.x'in paketi kullanmasını öneririz.

`Microsoft.AspNetCore.App` Metapaketin sürüm numarası minimum ASP.NET Core sürümünü ve Entity Framework Core sürümünü temsil eder.

`Microsoft.AspNetCore.App` Metapaketi kullanmak, uygulamanızı koruyan sürüm kısıtlamaları sağlar:

* Bir pakette `Microsoft.AspNetCore.App`geçişli (doğrudan değil) bir bağımlılığa sahip bir paket eklenmişse ve bu sürüm numaraları farklıysa, NuGet bir hata oluşturur.
* Uygulamanıza eklenen diğer paketler, 'de `Microsoft.AspNetCore.App`yer alan paketlerin sürümünü değiştiremez.
* Sürüm tutarlılığı güvenilir bir deneyim sağlar. `Microsoft.AspNetCore.App`ilgili bitlerin test edilmemiş sürüm birleşimlerinin aynı uygulamada birlikte kullanılmasını önlemek için tasarlanmıştır.

`Microsoft.AspNetCore.App` Metapaketi kullanan uygulamalar otomatik olarak ASP.NET Core paylaşılan çerçevesinden yararlanır. Meta paketi kullandığınızda, başvurulan ASP.NET Core NuGet paketlerinden **hiçbir** varlık, ASP.NET Core paylaşılan çerçevesinin bu varlıkları içerdiği uygulamayla&mdash;birlikte dağıtılmaz. `Microsoft.AspNetCore.App` Paylaşılan çerçevedeki varlıklar, uygulama başlatma süresini iyileştirmek için önceden derlenir. Daha fazla bilgi için [paylaşılan çerçeveye](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)bakın.

Aşağıdaki proje dosyası `Microsoft.AspNetCore.App` ASP.NET Core için meta paketine başvurur ve tipik bir ASP.NET Core 2.2 şablonu temsil eder:

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

Önceki biçimlendirme tipik bir ASP.NET Core 2.x şablonu temsil eder. Paket başvurusu için bir sürüm `Microsoft.AspNetCore.App` numarası belirtmez. Sürüm belirtilmediğinde, [Örtülü](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) bir sürüm SDK tarafından belirtilir, `Microsoft.NET.Sdk.Web`yani. SDK tarafından belirtilen örtük sürüme güvenmenizi ve sürüm numarasını paket başvurusuna açıkça ayarlamamanızı öneririz. Bu yaklaşımla ilgili sorularınız varsa, [Microsoft.AspNetCore.App örtülü sürümü için Tartışma](https://github.com/dotnet/AspNetCore.Docs/issues/6430)bir GitHub yorum bırakın.

Örtük sürüm taşınabilir `major.minor.0` uygulamalar için ayarlanır. Paylaşılan çerçeve roll-forward mekanizması, uygulamayı yüklenen paylaşılan çerçeveler arasında en son uyumlu sürümde çalıştıracaktır. Aynı sürümün geliştirme, test ve üretimde kullanıldığını garanti etmek için paylaşılan çerçevenin aynı sürümünün tüm ortamlarda yüklendiğinden emin olun. Kendi kendine yeten uygulamalar için, örtülü `major.minor.patch` sürüm numarası yüklenen SDK'da bulunan paylaşılan çerçevenin olarak ayarlanır.

`Microsoft.AspNetCore.App` Başvuruüzerinde bir sürüm numarası belirtmek, paylaşılan çerçevenin sürümünün seçileceğigaranti **etmez.** Örneğin, "2.2.1" sürümünün belirtildiğini, ancak "2.2.3" sürümünün yüklü olduğunu varsayalım. Bu durumda, uygulama "2.2.3" kullanır. Önerilmese de, ileri yuvarlamayı (yama ve/veya küçük) devre dışı kullanabilirsiniz. Dotnet ana bilgisayar roll-forward ve davranışını nasıl yapılandırışla ilgili daha fazla bilgi için [dotnet ana bilgisayar adedine](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md)bakın.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

`<Project Sdk`örtülü sürümü `Microsoft.AspNetCore.App` `Microsoft.NET.Sdk.Web` kullanacak şekilde ayarlanmalıdır. Ne `<Project Sdk="Microsoft.NET.Sdk">` zaman (izleme `.Web`olmadan) kullanılır:

* Aşağıdaki uyarı oluşturulur:

  *Uyarı NU1604: Proje bağımlılığı Microsoft.AspNetCore.App kapsayıcı bir alt sınır içermez. Tutarlı geri yükleme sonuçları sağlamak için bağımlılık sürümüne daha düşük bir sınır ekleyin.*

* Bu.NET Core 2.1 SDK ile bilinen bir sorundur.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a>ASP.NET Çekirdek güncelleme

`Microsoft.AspNetCore.App` [Meta paket](/dotnet/core/packages#metapackages) NuGet'den güncellenen geleneksel bir paket değildir. `Microsoft.NETCore.App`Benzer , `Microsoft.AspNetCore.App` NuGet dışında ele özel sürüm semantik olan paylaşılan bir çalışma süresini temsil eder. Daha fazla bilgi için [paketlere, meta paketlere ve çerçevelere](/dotnet/core/packages)bakın.

Core ASP.NET güncellemek için:

* Geliştirme makineleri ve yapı sunucularında: [.NET Core SDK'yı](https://dotnet.microsoft.com/download)indirin ve kurun.
* Dağıtım sunucularında: [.NET Core çalışma süresini](https://dotnet.microsoft.com/download)indirin ve kurun.

 Uygulamalar, uygulama nın yeniden başlatılmasında en son yüklenen sürüme doğru iletilir. Proje dosyasındaki `Microsoft.AspNetCore.App` sürüm numarasını güncelleştirmek gerekli değildir. Daha fazla bilgi için [Çerçeveye bağımlı uygulamaların ileri ye doğru ilerlemesine](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward)bakın.

Uygulamanız daha önce `Microsoft.AspNetCore.All`kullanılmışsa, [Microsoft.AspNetCore.All'dan Microsoft.AspNetCore.App'a Geçiş](xref:fundamentals/metapackage#migrate)'e bakın.

::: moniker-end
