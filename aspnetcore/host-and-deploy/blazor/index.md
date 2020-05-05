---
title: ASP.NET Core barındırma ve dağıtmaBlazor
author: guardrex
description: Uygulamaları nasıl barındırılacağını ve dağıtacağınızı Blazor öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 9d57b81cd813d02a65b6d3a39c7f1a1aa8a069c7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775173"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>ASP.NET Core Blazor barındırma ve dağıtma

, [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)ve [Daniel Roth](https://github.com/danroth27) tarafından

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a>Uygulamayı yayımlama

Uygulamalar yayın yapılandırmasında dağıtım için yayımlanır.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Gezinti çubuğundan **Build** > **Publish {APPLICATION}** öğesini seçin.
1. *Yayımla hedefini*seçin. Yerel olarak yayımlamak için **klasör**' ü seçin.
1. **Klasör seçin** alanında varsayılan konumu kabul edin veya farklı bir konum belirtin. **Yayımla** düğmesini seçin.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. **Derleme** > **yayımlama klasörü**' nü seçin.
1. Yayınlanan varlıkların alınacağı klasörü onaylayın ve **Yayımla**' yı seçin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Uygulamayı bir sürüm yapılandırmasıyla yayımlamak için [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutunu kullanın:

```dotnetcli
dotnet publish -c Release
```

---

Uygulamanın yayımlanması, projenin bağımlılıklarını [geri yüklemeyi](/dotnet/core/tools/dotnet-restore) tetikler ve dağıtım için varlıkları oluşturmadan önce projeyi [oluşturur](/dotnet/core/tools/dotnet-build) . Yapı işleminin bir parçası olarak, uygulama indirme boyutunu ve yükleme sürelerini azaltmak için kullanılmayan Yöntemler ve derlemeler kaldırılır.

Yayımlama konumları:

* BlazorWebAssembly
  * Tek &ndash; başına uygulama */bin/Release/{Target Framework}/Publish/Wwwroot* klasöründe yayımlanır. Uygulamayı statik bir site olarak dağıtmak için *Wwwroot* klasörünün içeriğini statik site konağına kopyalayın.
  * İstemci &ndash; Blazor webassembly uygulaması barındırılan sunucu uygulamasının diğer statik Web varlıklarıyla bırlıkte sunucu uygulamasının */bin/Release/{Target Framework}/Publish/Wwwroot* klasöründe yayımlanır. *Yayımla* klasörünün içeriğini konağa dağıtın.
* BlazorUygulama &ndash; , */bin/Release/{Target Framework}/Publish* klasöründe yayımlanır. *Yayımla* klasörünün içeriğini konağa dağıtın.

Klasördeki varlıklar Web sunucusuna dağıtılır. Dağıtım, kullanımdaki geliştirme araçlarına bağlı olarak el ile veya otomatik bir süreç olabilir.

## <a name="app-base-path"></a>Uygulama temel yolu

*Uygulama temel yolu* , UYGULAMANıN kök URL yoludur. Aşağıdaki ASP.NET Core uygulamayı ve Blazor alt uygulamayı göz önünde bulundurun:

* ASP.NET Core uygulaması şu şekilde adlandırılır `MyApp`:
  * Uygulama fiziksel olarak *d:/MyApp*konumunda bulunur.
  * İstekleri tarihinde `https://www.contoso.com/{MYAPP RESOURCE}`alınır.
* Adlı Blazor `CoolApp` bir uygulama, öğesinin `MyApp`bir alt uygulamasıdır:
  * Alt uygulama fiziksel olarak *d:/MyApp/CoolApp*konumunda bulunur.
  * İstekleri tarihinde `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`alınır.

İçin `CoolApp`ek yapılandırma belirtmeden, Bu senaryodaki alt uygulama, sunucuda nerede bulunduğu konusunda bilgi sahibi değildir. Örneğin, uygulama ilgili URL yolunda `/CoolApp/`bulunduğunu bilmeden kaynaklarına doğru göreli URL 'ler oluşturamıyoruz.

Blazor `https://www.contoso.com/CoolApp/`Uygulamanın temel yolu için yapılandırma sağlamak üzere, `<base>` etiketin `href` özniteliği *Pages/_Host. cshtml* dosyasında (Blazor sunucu) veya *Wwwroot/index.html* dosyasında (Blazor webassembly) göreli kök yoluna ayarlanır:

```html
<base href="/CoolApp/">
```

BlazorSunucu uygulamaları Ayrıca, uygulamanın istek ardışık düzeninde çağırarak <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> sunucu tarafı taban yolunu ayarlar: `Startup.Configure`

```csharp
app.UsePathBase("/CoolApp");
```

Göreli URL yolunu sağlayarak, kök dizinde olmayan bir bileşen, uygulamanın kök yoluna göre URL 'Ler oluşturabilir. Farklı dizin yapısı düzeylerindeki bileşenler, uygulama genelinde konumlardaki diğer kaynakların bağlantılarını oluşturabilir. Uygulama temel yolu, bağlantının `href` hedefinin uygulama temel yolu URI alanı içinde olduğu seçili köprüleri ele almak için de kullanılır. Blazor Yönlendirici iç gezinmeyi işler.

Birçok barındırma senaryosunda, uygulamanın göreli URL yolu uygulamanın köküdür. Bu durumlarda, uygulamanın göreli URL taban yolu, bir`<base href="/" />` Blazor uygulamanın varsayılan yapılandırması olan bir eğik çizgi () olur. GitHub sayfaları ve IIS alt uygulamaları gibi diğer barındırma senaryolarında, uygulama temel yolu, sunucunun uygulamanın göreli URL 'SI yolu olarak ayarlanmalıdır.

Uygulamanın temel yolunu ayarlamak için, *sayfa/_Host. cshtml* Blazor dosyası ( `<head>` sunucu) veya *Wwwroot/index.html* dosyasının (Blazor webassembly) etiket öğeleri içindeki `<base>` etiketi güncelleştirin. `href` Öznitelik değerini olarak `/{RELATIVE URL PATH}/` ayarlayın (sondaki eğik çizgi gereklidir), burada `{RELATIVE URL PATH}` uygulamanın tam göreli URL yoludur.

Kök olmayan Blazor göreli URL yoluna (örneğin, `<base href="/CoolApp/">`) sahip bir webassembly uygulaması için, uygulama *yerel olarak çalıştırıldığında*kaynaklarını bulamaz. Yerel geliştirme ve test sırasında bu sorunu aşmak için, çalışma zamanında `href` `<base>` etiketinin değeriyle eşleşen bir *yol temel* bağımsız değişkeni sağlayabilirsiniz. Sondaki eğik çizgi eklemeyin. Uygulamayı yerel olarak çalıştırırken yol temel bağımsız değişkenini geçirmek için, `dotnet run` komutu uygulamanın dizininden çalıştırın, `--pathbase` seçeneği:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Göreli URL Blazor yolu `/CoolApp/` (`<base href="/CoolApp/">`) olan bir webassembly uygulaması için, komut şu şekilde olur:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

Blazor Webassembly uygulaması tarihinde `http://localhost:port/CoolApp`yerel olarak yanıt verir.

## <a name="deployment"></a>Dağıtım

Dağıtım Kılavuzu için aşağıdaki konulara bakın:

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
