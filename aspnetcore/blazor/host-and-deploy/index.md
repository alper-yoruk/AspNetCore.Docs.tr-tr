---
title: ASP.NET Core barındırma ve dağıtma Blazor
author: guardrex
description: Uygulamaları nasıl barındırılacağını ve dağıtacağınızı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
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
uid: blazor/host-and-deploy/index
ms.openlocfilehash: 082072d2b70abfe60da8e2cd40daa8b93ebcc9ac
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055821"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor"></a>ASP.NET Core barındırma ve dağıtma Blazor

, [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)ve [Daniel Roth](https://github.com/danroth27) tarafından

## <a name="publish-the-app"></a>Uygulamayı yayımlama

Uygulamalar yayın yapılandırmasında dağıtım için yayımlanır.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Gezinti çubuğundan **Build**  >  **Publish {APPLICATION}** öğesini seçin.
1. *Yayımla hedefini* seçin. Yerel olarak yayımlamak için **klasör** ' ü seçin.
1. **Klasör seçin** alanında varsayılan konumu kabul edin veya farklı bir konum belirtin. **`Publish`** düğmesini seçin.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. **Derleme**  >  **yayımlama klasörü** ' nü seçin.
1. Yayınlanan varlıkların alınacağı klasörü onaylayın ve öğesini seçin **`Publish`** .

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

[`dotnet publish`](/dotnet/core/tools/dotnet-publish)Uygulamayı bir sürüm yapılandırmasıyla yayımlamak için komutunu kullanın:

```dotnetcli
dotnet publish -c Release
```

---

Uygulamanın yayımlanması, projenin bağımlılıklarını [geri yüklemeyi](/dotnet/core/tools/dotnet-restore) tetikler ve dağıtım için varlıkları oluşturmadan önce projeyi [oluşturur](/dotnet/core/tools/dotnet-build) . Yapı işleminin bir parçası olarak, uygulama indirme boyutunu ve yükleme sürelerini azaltmak için kullanılmayan Yöntemler ve derlemeler kaldırılır.

Yayımlama konumları:

* Blazor WebAssembly
  * Tek başına: uygulama `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` klasöre yayımlanır. Uygulamayı statik bir site olarak dağıtmak için, `wwwroot` klasörün içeriğini statik site konağına kopyalayın.
  * Barındırılan: istemci uygulama, sunucu uygulamasının Blazor WebAssembly `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` diğer statik Web varlıklarıyla birlikte sunucu uygulamasının klasörüne yayımlanır. `publish`Klasörün içeriğini konağa dağıtın.
* Blazor Server: Uygulama `/bin/Release/{TARGET FRAMEWORK}/publish` klasöründe yayımlanır. `publish`Klasörün içeriğini konağa dağıtın.

Klasördeki varlıklar Web sunucusuna dağıtılır. Dağıtım, kullanımdaki geliştirme araçlarına bağlı olarak el ile veya otomatik bir süreç olabilir.

## <a name="app-base-path"></a>Uygulama temel yolu

*Uygulama temel yolu* , UYGULAMANıN kök URL yoludur. Aşağıdaki ASP.NET Core uygulamayı ve alt uygulamayı göz önünde bulundurun Blazor :

* ASP.NET Core uygulaması şu şekilde adlandırılır `MyApp` :
  * Uygulama fiziksel olarak konumunda bulunur `d:/MyApp` .
  * İstekleri tarihinde alınır `https://www.contoso.com/{MYAPP RESOURCE}` .
* BlazorAdlı bir uygulama `CoolApp` , öğesinin bir alt uygulamasıdır `MyApp` :
  * Alt uygulama fiziksel olarak konumunda bulunur `d:/MyApp/CoolApp` .
  * İstekleri tarihinde alınır `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` .

İçin ek yapılandırma belirtmeden `CoolApp` , Bu senaryodaki alt uygulama, sunucuda nerede bulunduğu konusunda bilgi sahibi değildir. Örneğin, uygulama ilgili URL yolunda bulunduğunu bilmeden kaynaklarına doğru göreli URL 'Ler oluşturamıyoruz `/CoolApp/` .

Uygulamanın temel yolu için yapılandırma sağlamak üzere Blazor `https://www.contoso.com/CoolApp/` `<base>` etiketinin `href` özniteliği `Pages/_Host.cshtml` dosyadaki ( Blazor Server ) veya `wwwroot/index.html` dosyadaki () göreli kök yoluna ayarlanır Blazor WebAssembly :

```html
<base href="/CoolApp/">
```

Blazor Server uygulamalar Ayrıca <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> uygulamanın istek ardışık düzeninde çağırarak sunucu tarafı taban yolunu ayarlar `Startup.Configure` :

```csharp
app.UsePathBase("/CoolApp");
```

Göreli URL yolunu sağlayarak, kök dizinde olmayan bir bileşen, uygulamanın kök yoluna göre URL 'Ler oluşturabilir. Farklı dizin yapısı düzeylerindeki bileşenler, uygulama genelinde konumlardaki diğer kaynakların bağlantılarını oluşturabilir. Uygulama temel yolu, `href` bağlantının hedefinin uygulama temel yolu URI alanı içinde olduğu seçili köprüleri ele almak için de kullanılır. BlazorYönlendirici iç gezinmeyi işler.

Birçok barındırma senaryosunda, uygulamanın göreli URL yolu uygulamanın köküdür. Bu durumlarda, uygulamanın göreli URL taban yolu `<base href="/" />` , bir uygulamanın varsayılan yapılandırması olan bir eğik çizgi () olur Blazor . GitHub sayfaları ve IIS alt uygulamaları gibi diğer barındırma senaryolarında, uygulama temel yolu, sunucunun uygulamanın göreli URL 'SI yolu olarak ayarlanmalıdır.

Uygulamanın temel yolunu ayarlamak için `<base>` `<head>` `Pages/_Host.cshtml` dosyanın ( Blazor Server ) veya `wwwroot/index.html` dosyanın () etiket öğeleri içindeki etiketi güncelleştirin Blazor WebAssembly . `href`Öznitelik değerini olarak ayarlayın `/{RELATIVE URL PATH}/` (sondaki eğik çizgi gereklidir), burada `{RELATIVE URL PATH}` UYGULAMANıN tam göreli URL yoludur.

Blazor WebAssemblyKök olmayan GÖRELI URL yoluna (örneğin,) sahip bir uygulama için `<base href="/CoolApp/">` , uygulama *yerel olarak çalıştırıldığında* kaynaklarını bulamaz. Yerel geliştirme ve test sırasında bu sorunu aşmak için, *path base* `href` `<base>` çalışma zamanında etiketinin değeriyle eşleşen bir yol temel bağımsız değişkeni sağlayabilirsiniz. Sondaki eğik çizgi eklemeyin. Uygulamayı yerel olarak çalıştırırken yol temel bağımsız değişkenini geçirmek için, `dotnet run` komutu uygulamanın dizininden çalıştırın, `--pathbase` seçeneği:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Blazor WebAssemblyGÖRELI URL yolu () olan bir uygulama için `/CoolApp/` , `<base href="/CoolApp/">` komut şu şekilde olur:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

Blazor WebAssemblyUygulama üzerinde yerel olarak yanıt verir `http://localhost:port/CoolApp` .

**Blazor Server`MapFallbackToPage`yapılandırma**

Aşağıdaki yolu <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> içine geçirin `Startup.Configure` :

```csharp
endpoints.MapFallbackToPage("/{RELATIVE PATH}/{**path:nonfile}");
```

Yer tutucu, `{RELATIVE PATH}` sunucuda kök olmayan yoldur. Örneğin, `CoolApp` uygulamanın kök olmayan URL 'si ise yer tutucu segmentdir `https://{HOST}:{PORT}/CoolApp/` :

```csharp
endpoints.MapFallbackToPage("/CoolApp/{**path:nonfile}");
```

**Birden çok Blazor WebAssembly uygulamayı barındırma**

Barındırılan bir çözümde birden çok uygulamayı barındırma hakkında daha fazla bilgi için Blazor WebAssembly Blazor bkz <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps> ..

## <a name="deployment"></a>Dağıtım

Dağıtım Kılavuzu için aşağıdaki konulara bakın:

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
