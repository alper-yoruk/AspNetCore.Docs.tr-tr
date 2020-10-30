---
title: Web API Çözümleyicileri kullanma
author: pranavkm
description: ASP.NET Core MVC web API Çözümleyicileri paketi hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
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
uid: web-api/advanced/analyzers
ms.openlocfilehash: cf0415e7d72e21a48db8bbeb4540f05e0b0a4198
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057927"
---
# <a name="use-web-api-analyzers"></a>Web API Çözümleyicileri kullanma

ASP.NET Core 2,2 ve üzeri, Web API projeleriyle kullanılması amaçlanan bir MVC Çözümleyicileri paketi sağlar. Çözümleyiciler <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> , [Web API kuralları](xref:web-api/advanced/conventions)üzerinde oluşturma sırasında ile açıklanmış olan denetleyicilerle birlikte çalışır.

Çözümleyiciler paketi şu şekilde bir denetleyici eylemi bildirir:

* Bildirilmemiş bir durum kodu döndürür.
* Bildirilmemiş başarı sonucunu döndürür.
* Döndürülen bir durum kodunu belgeler.
* Açık model doğrulama denetimi içerir.

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a>Çözümleyici paketine başvur

ASP.NET Core 3,0 veya sonraki sürümlerde, çözümleyiciler .NET Core SDK dahil edilmiştir. Projenizdeki çözümleyici 'yi etkinleştirmek için, `IncludeOpenAPIAnalyzers` proje dosyasına özelliği ekleyin:

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a>Paket yüklemesi

Aşağıdaki yaklaşımlardan biriyle [Microsoft. AspNetCore. Mvc. api. çözümleyiciler](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet paketini yükler:

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Paket Yöneticisi konsol** penceresinde:
  * **View** > **Diğer Windows** > **Paket Yöneticisi konsolunu** görüntüle ' ye gidin.
  * *Apiconventions. csproj* dosyasının bulunduğu dizine gidin.
  * Şu kodu yürütün:

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* *Packages* **Çözüm bölmesi** > **paket Ekle...** ' da paketler klasörüne sağ tıklayın.
* **Paket Ekle** penceresinin **kaynak** açılan penceresini "NuGet.org" olarak ayarlayın.
* Arama kutusuna "Microsoft. AspNetCore. Mvc. api. çözümleyiciler" yazın.
* Sonuçlar bölmesinden "Microsoft. AspNetCore. Mvc. api. çözümleyiciler" paketini seçin ve **paket Ekle** ' ye tıklayın.

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**Tümleşik terminalden** aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Şu komutu çalıştırın:

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a>Web API kuralları için çözümleyiciler

Openapı belgeleri, bir eylemin döndürebildiği durum kodlarını ve yanıt türlerini içerir. ASP.NET Core MVC 'de, ve gibi öznitelikler <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> bir eylemi belgelemek için kullanılır. <xref:tutorials/web-api-help-pages-using-swagger> Web API 'nizi belgeleme hakkında daha fazla ayrıntıya gider.

Paketteki çözümleyicilerden biri ile açıklanmış denetimleri inceler <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> ve yanıtlarını tamamen belgemeyen eylemleri tanımlar. Aşağıdaki örneği inceleyin:

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

Yukarıdaki eylem HTTP 200 başarılı dönüş türünü belgeler, ancak HTTP 404 hata durumu kodunu belgeetmez. Çözümleyici, HTTP 404 durum kodu için eksik belgeleri uyarı olarak bildirir. Sorunu gidermeye yönelik bir seçenek sağlanır.

![Çözümleyici bir uyarı bildiriyor](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
