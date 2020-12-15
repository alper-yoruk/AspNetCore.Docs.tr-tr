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
ms.openlocfilehash: 29f1a1f211688a1edcd31c7230e7216df7c89eef
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506818"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a>ASP.NET Core için araç oluşturma Blazor

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

::: zone pivot="windows"

1. **ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) ' in en son sürümünü yükler.

1. Yeni bir proje oluşturma.

1. **Blazor Uygulama** seçin. **İleri**’yi seçin.

1. **Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin. **Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin. **Oluştur**’u seçin.

1. Bir Blazor WebAssembly deneyim için **Blazor WebAssembly uygulama** şablonunu seçin. Bir Blazor Server deneyim için **Blazor Server uygulama** şablonunu seçin. **Oluştur**’u seçin.

   Barındırılan bir Blazor WebAssembly deneyim için **ASP.NET Core barındırılan** onay kutusunu seçin.

   İki barındırma modeli hakkında daha fazla bilgi için Blazor *Blazor WebAssembly* (tek başına ve barındırılan) ve *Blazor Server* bkz <xref:blazor/hosting-models> ..

1. <kbd></kbd> + Uygulamayı çalıştırmak için CTRL<kbd>F5</kbd> tuşuna basın.

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

   Barındırılan bir Blazor WebAssembly deneyim için, barındırılan seçenek ( `-ho` veya `--hosted` ) seçeneğini komuta ekleyin:
   
   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```
   
   Bir Blazor Server deneyim için komut kabuğu 'nda aşağıdaki komutu yürütün:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   İki barındırma modeli hakkında daha fazla bilgi için Blazor *Blazor WebAssembly* (tek başına ve barındırılan) ve *Blazor Server* bkz <xref:blazor/hosting-models> ..

1. `WebApplication1`Visual Studio Code klasörü açın.

1. IDE, projeyi derlemek ve hatalarını ayıklamak için varlık eklemenizi ister. **Evet**’i seçin.

1. <kbd></kbd> + Uygulamayı çalıştırmak için CTRL<kbd>F5</kbd> tuşuna basın.

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

1. Yan çubukta **Web ve konsol**  >  **uygulaması**' nı seçin.

   Bir Blazor WebAssembly deneyim için **Blazor WebAssembly uygulama** şablonunu seçin. Bir Blazor Server deneyim için **Blazor Server uygulama** şablonunu seçin. **İleri**’yi seçin.

   İki barındırma modeli hakkında daha fazla bilgi için Blazor *Blazor WebAssembly* (tek başına ve barındırılan) ve *Blazor Server* bkz <xref:blazor/hosting-models> ..

1. **Kimlik** doğrulamasının **kimlik doğrulaması yok** olarak ayarlandığını onaylayın. **İleri**’yi seçin.

1. Barındırılan bir Blazor WebAssembly deneyim için **ASP.NET Core barındırılan** onay kutusunu seçin.

1. **Proje adı** alanında, uygulamayı adlandırın `WebApplication1` . **Oluştur**’u seçin.

1.   >  Uygulamayı hata *ayıklayıcı olmadan* çalıştırmak için **hata ayıklama olmadan Başlat** ' ı seçin. Uygulamayı   >  *hata ayıklayıcıyla* çalıştırmak için uygulamayı **Başlat hata ayıklaması** veya Çalıştır (&#9654;) düğmesini çalıştırın.

Geliştirme sertifikasına güvenmek için bir istem görünürse, sertifikaya güvenin ve devam edin. Sertifikaya güvenmek için Kullanıcı ve anahtarlık parolaların olması gerekir. ASP.NET Core HTTPS geliştirme sertifikasını güvenme hakkında daha fazla bilgi için bkz <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> ..

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-no-locblazor-development"></a>Platformlar arası geliştirme için Visual Studio Code kullanma Blazor

[Visual Studio Code](https://code.visualstudio.com/) , uygulama geliştirmek için kullanılabilen, açık kaynaklı, platformlar arası tümleşik geliştirme ORTAMıDıR (IDE) Blazor . Visual Studio Code ile geliştirme için yeni bir uygulama oluşturmak üzere .NET CLı 'yi kullanın Blazor . Daha fazla bilgi için [Bu makalenin Linux sürümüne](/aspnet/core/blazor/tooling?pivots=linux)bakın.

## <a name="no-locblazor-template-options"></a>Blazor Şablon seçenekleri

BlazorFramework, iki barındırma modelinin her biri için yeni uygulamalar oluşturmaya yönelik şablonlar sağlar Blazor . Şablonlar, Blazor geliştirme için seçtiğiniz araç Blazor (Visual Studio, Mac için Visual Studio, Visual Studio Code veya .net CLI) ne olursa olsun yeni projeler ve çözümler oluşturmak için kullanılır:

* Blazor WebAssembly Proje şablonu: `blazorwasm`
* Blazor Server Proje şablonu: `blazorserver`

Barındırma modelleri hakkında daha fazla bilgi için Blazor bkz <xref:blazor/hosting-models> ..

Şablon seçenekleri, `-h` `--help` [`dotnet new`](/dotnet/core/tools/dotnet-new) komut kabuğu 'ndaki CLI komutuna yardım seçeneği (veya) geçirerek kullanılabilir:

```dotnetcli
dotnet new blazorwasm --h
dotnet new blazorserver --h
```
