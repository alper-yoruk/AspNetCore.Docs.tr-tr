---
title: ASP.NET Core için araç oluşturma Blazor
author: guardrex
description: Uygulama derlemek için kullanılabilen araç hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 500342ac979efdee824ac0d4b5757ca9804f3b30
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054820"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a>ASP.NET Core için araç oluşturma Blazor

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

::: zone pivot="windows"

1. **ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) ' in en son sürümünü yükler.

1. Yeni bir proje oluşturma.

1. **Blazor Uygulama** seçin. **İleri** ’yi seçin.

1. **Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin. **Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin. **Oluştur** ’u seçin.

1. Bir Blazor WebAssembly deneyim için **Blazor WebAssembly uygulama** şablonunu seçin. Bir Blazor Server deneyim için **Blazor Server uygulama** şablonunu seçin. **Oluştur** ’u seçin.

   İki barındırma modeli hakkında daha fazla bilgi için, Blazor *Blazor WebAssembly* *Blazor Server* bkz <xref:blazor/hosting-models> ..

1. <kbd>Ctrl</kbd> + Uygulamayı çalıştırmak için CTRL<kbd>F5</kbd> tuşuna basın.

ASP.NET Core HTTPS geliştirme sertifikasını güvenme hakkında daha fazla bilgi için bkz <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> ..

::: zone-end

::: zone pivot="linux"

1. [.NET Core SDK](https://dotnet.microsoft.com/download)en son sürümünü yükler. SDK 'Yı daha önce yüklediyseniz, bir komut kabuğu 'nda aşağıdaki komutu yürüterek yüklü sürümünüzü belirleyebilirsiniz:

   ```dotnetcli
   dotnet --version
   ```

1. En son [Visual Studio Code](https://code.visualstudio.com)sürümünü yükler.

1. [Visual Studio Code uzantısı için](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)en son C# ' i yükler.

1. Bir Blazor WebAssembly deneyim için komut kabuğu 'nda aşağıdaki komutu yürütün:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Bir Blazor Server deneyim için komut kabuğu 'nda aşağıdaki komutu yürütün:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   İki barındırma modeli hakkında daha fazla bilgi için, Blazor *Blazor WebAssembly* *Blazor Server* bkz <xref:blazor/hosting-models> ..

1. `WebApplication1`Visual Studio Code klasörü açın.

1. IDE, projeyi derlemek ve hatalarını ayıklamak için varlık eklemenizi ister. **Evet** ’i seçin.

1. <kbd>Ctrl</kbd> + Uygulamayı çalıştırmak için CTRL<kbd>F5</kbd> tuşuna basın.

## <a name="trust-a-development-certificate"></a>Geliştirme sertifikasına güven

Linux 'ta sertifikaya güvenmenin merkezi bir yolu yoktur. Genellikle aşağıdaki yaklaşımlardan biri benimsenmiştir:

* Uygulamanın URL 'sini tarayıcının dışlama listesinde hariç tutun.
* İçin otomatik olarak imzalanan tüm sertifikalara güvenin `localhost` .
* Sertifikayı tarayıcıdaki güvenilen sertifikalar listesine ekleyin.

Daha fazla bilgi için, tarayıcı üreticiniz ve Linux dağıtım tarafından sunulan kılavuza bakın.

::: zone-end

::: zone pivot="macos"

1. [Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/)'i yükler.

1. **Dosya**  >  **yeni çözüm** ' ı seçin veya **Başlangıç penceresinden** **Yeni** bir proje oluşturun.

1. Yan çubukta **Web ve konsol**  >  **uygulaması** ' nı seçin.

   Bir Blazor WebAssembly deneyim için **Blazor WebAssembly uygulama** şablonunu seçin. Bir Blazor Server deneyim için **Blazor Server uygulama** şablonunu seçin. **İleri** ’yi seçin.

   İki barındırma modeli hakkında daha fazla bilgi için, Blazor *Blazor WebAssembly* *Blazor Server* bkz <xref:blazor/hosting-models> ..

1. **Kimlik** doğrulamasının **kimlik doğrulaması yok** olarak ayarlandığını onaylayın. **İleri** ’yi seçin.

1. **Proje adı** alanında, uygulamayı adlandırın `WebApplication1` . **Oluştur** ’u seçin.

1. **Run**  >  Uygulamayı hata *ayıklayıcı olmadan* çalıştırmak için **hata ayıklama olmadan Başlat** ' ı seçin. Uygulamayı **Run**  >  *hata ayıklayıcıyla* çalıştırmak için uygulamayı **Başlat hata ayıklaması** veya Çalıştır (&#9654;) düğmesini çalıştırın.

Geliştirme sertifikasına güvenmek için bir istem görünürse, sertifikaya güvenin ve devam edin. Sertifikaya güvenmek için Kullanıcı ve anahtarlık parolaların olması gerekir. ASP.NET Core HTTPS geliştirme sertifikasını güvenme hakkında daha fazla bilgi için bkz <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> ..

::: zone-end
