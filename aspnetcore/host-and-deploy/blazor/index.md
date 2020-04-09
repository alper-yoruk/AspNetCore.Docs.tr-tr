---
title: ASP.NET Core'u barındırma ve dağıtmaBlazor
author: guardrex
description: Uygulamaları nasıl barındırıp Blazor dağıtılayın keşfedin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: ddf70da29a82d462422c1bdf74ff45b92bb10b56
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434271"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Core BlazorASP.NET ana bilgisayar ve dağıtım

Luke [Latham](https://github.com/guardrex)tarafından , [Rainer Stropek](https://www.timecockpit.com), ve [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a>Uygulamayı yayımlama

Uygulamalar Sürüm yapılandırmasında dağıtım için yayımlanır.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Gezinti çubuğundan **Yapı** > **Yayımla {APPLICATION}** seçeneğini belirleyin.
1. *Yayımlama hedefini*seçin. Yerel olarak yayımlamak için **Klasör'ü**seçin.
1. Klasör alanı **seç'teki** varsayılan konumu kabul edin veya farklı bir konum belirtin. **Yayımla** düğmesini seçin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Uygulamayı Yayın yapılandırmasıyla yayınlamak için [dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutunu kullanın:

```dotnetcli
dotnet publish -c Release
```

---

Uygulamayı yayımlama, projenin bağımlılıklarının [geri yüklenmesini](/dotnet/core/tools/dotnet-restore) tetikler ve dağıtım için varlıkları oluşturmadan önce projeyi [oluşturur.](/dotnet/core/tools/dotnet-build) Yapı işleminin bir parçası olarak, uygulama indirme boyutunu ve yükleme sürelerini azaltmak için kullanılmayan yöntemler ve derlemeler kaldırılır.

Konumları yayımla:

* BlazorWebAssembly
  * Bağımsız &ndash; Uygulama */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* klasöründe yayınlanır. Uygulamayı statik bir site olarak dağıtmak *için, wwwroot* klasörünün içeriğini statik site ana bilgisayarına kopyalayın.
  * &ndash; Barındırılan Blazor istemci WebAssembly uygulaması sunucu uygulamasının */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* klasöründe, sunucu uygulamasının diğer statik web varlıklarıyla birlikte yayınlanır. *Yayımlama* klasörünün içeriğini ana bilgisayara dağıtın.
* BlazorServer &ndash; Uygulama */bin/Release/{TARGET FRAMEWORK}/publish* klasöründe yayınlanır. *Yayımlama* klasörünün içeriğini ana bilgisayara dağıtın.

Klasördeki varlıklar web sunucusuna dağıtılır. Dağıtım, kullanımdaki geliştirme araçlarına bağlı olarak el ile veya otomatik bir işlem olabilir.

## <a name="app-base-path"></a>Uygulama tabanı yolu

*Uygulama temel yolu,* uygulamanın kök URL yoludur. Aşağıdaki ASP.NET Core uygulamasını Blazor ve alt uygulamalarını göz önünde bulundurun:

* ASP.NET Core uygulamasının `MyApp`adı:
  * Uygulama fiziksel olarak *d:/MyApp*adresinde bulunur.
  * İstekler `https://www.contoso.com/{MYAPP RESOURCE}`.
* Adlı Blazor `CoolApp` bir uygulama bir alt `MyApp`uygulamadır:
  * Alt uygulama fiziksel olarak *d:/MyApp/CoolApp*adresinde bulunur.
  * İstekler `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.

Için `CoolApp`ek yapılandırma belirtmeden, bu senaryoda alt uygulama sunucuda nerede bulunduğu hakkında hiçbir bilgiye sahiptir. Örneğin, uygulama, göreli URL yolunda `/CoolApp/`bulunduğunu bilmeden kaynaklarına doğru göreli URL'ler oluşturamaz.

Uygulamanın Blazor temel yolu için yapılandırma `https://www.contoso.com/CoolApp/`sağlamak `<base>` için, `href` etiketin özniteliği *Sayfalar/_Host.cshtml* dosyasındaki (Sunucu)Blazor veya *wwwroot/index.html* dosyasındakiBlazor (WebAssembly) göreli kök yoluna ayarlanır:

```html
<base href="/CoolApp/">
```

BlazorSunucu uygulamaları ayrıca uygulamanın istek ardışık <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> hattını arayarak sunucu `Startup.Configure`tarafındaki temel yolu ayarlar:

```csharp
app.UsePathBase("/CoolApp");
```

Göreli URL yolunu sağlayarak, kök dizinde olmayan bir bileşen, uygulamanın kök yoluna göre URL'ler oluşturabilirsiniz. Dizin yapısının farklı düzeylerindeki bileşenler, uygulama nın dışındaki konumlarda diğer kaynaklara bağlantılar oluşturabilir. Uygulama temel yolu, bağlantının `href` hedefinin uygulama temel yolu URI alanı içinde olduğu seçili köprüleri kesmek için de kullanılır. Blazor Yönlendirici dahili gezinmeyi işler.

Birçok barındırma senaryosunda, uygulamaya giden göreli URL yolu uygulamanın köküdür. Bu gibi durumlarda, uygulamanın göreli URL taban`<base href="/" />`yolu, bir Blazor uygulama için varsayılan yapılandırma olan bir ileri eğik çizgidir. GitHub Sayfaları ve IIS alt uygulamaları gibi diğer barındırma senaryolarında, uygulama temel yolu sunucunun uygulamanın göreli URL yoluna ayarlanmalıdır.

Uygulamanın temel yolunu ayarlamak için, `<base>` *Sayfalar/_Host.cshtml* dosyasının (Sunucu)Blazor veya *wwwroot/index.html* dosyasınınBlazor (WebAssembly) `<head>` etiket öğeleri içindeki etiketi güncelleştirin. Öznitelik `href` değerini `/{RELATIVE URL PATH}/` uygulamanın tam göreli URL `{RELATIVE URL PATH}` yolunun olduğu yere (sondaki eğik çizgi gereklidir) ayarlayın.

Kök Blazor olmayan göreli URL yolu olan bir WebAssembly `<base href="/CoolApp/">`uygulaması için (örneğin,), uygulama *yerel olarak çalıştırıldığında*kaynaklarını bulamıyor. Yerel geliştirme ve sınama sırasında bu sorunun üstesinden gelmek `href` için, `<base>` çalışma zamanında etiketin değeriyle eşleşen bir *yol temel* bağımsız değişkeni sağlayabilirsiniz. Bir çizgi eklemeyin. Uygulamayı yerel olarak çalıştırırken yol temel bağımsız `dotnet run` değişkenini geçmek için, `--pathbase` uygulamanın dizininden komutu aşağıdaki seçenekle uygulayın:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Göreceli Blazor URL yolu `/CoolApp/` olan bir WebAssembly`<base href="/CoolApp/">`uygulaması için ( ), komutu:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

Blazor WebAssembly uygulaması yerel olarak `http://localhost:port/CoolApp`yanıt verir.

## <a name="deployment"></a>Dağıtım

Dağıtım kılavuzu için aşağıdaki konulara bakın:

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
