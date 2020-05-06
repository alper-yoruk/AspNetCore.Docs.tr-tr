---
title: Web API Çözümleyicileri kullanma
author: pranavkm
description: ASP.NET Core MVC web API Çözümleyicileri paketi hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/analyzers
ms.openlocfilehash: 530ce2d2a7f67f549f6d188a0c571a5d58518377
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776252"
---
# <a name="use-web-api-analyzers"></a>Web API Çözümleyicileri kullanma

ASP.NET Core 2,2 ve üzeri, Web API projeleriyle kullanılması amaçlanan bir MVC Çözümleyicileri paketi sağlar. Çözümleyiciler, [Web API kuralları](xref:web-api/advanced/conventions)üzerinde oluşturma sırasında <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>ile açıklanmış olan denetleyicilerle birlikte çalışır.

Çözümleyiciler paketi şu şekilde bir denetleyici eylemi bildirir:

* Bildirilmemiş bir durum kodu döndürür.
* Bildirilmemiş başarı sonucunu döndürür.
* Döndürülen bir durum kodunu belgeler.
* Açık model doğrulama denetimi içerir.

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a>Çözümleyici paketine başvur

ASP.NET Core 3,0 veya sonraki sürümlerde, çözümleyiciler .NET Core SDK dahil edilmiştir. Projenizdeki çözümleyici 'yi etkinleştirmek için, proje dosyasına `IncludeOpenAPIAnalyzers` özelliği ekleyin:

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
  * **View** > **Diğer** Windows > **Paket Yöneticisi konsolunu**görüntüle ' ye gidin.
  * *Apiconventions. csproj* dosyasının bulunduğu dizine gidin.
  * Aşağıdaki komutu yürütün:

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Solution Pad** Çözüm bölmesi > **paket Ekle...**' da *paketler* klasörüne sağ tıklayın.
* **Paket Ekle** penceresinin **kaynak** açılan penceresini "NuGet.org" olarak ayarlayın.
* Arama kutusuna "Microsoft. AspNetCore. Mvc. api. çözümleyiciler" yazın.
* Sonuçlar bölmesinden "Microsoft. AspNetCore. Mvc. api. çözümleyiciler" paketini seçin ve **paket Ekle**' ye tıklayın.

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**Tümleşik terminalden**aşağıdaki komutu çalıştırın:

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

Openapı belgeleri, bir eylemin döndürebildiği durum kodlarını ve yanıt türlerini içerir. ASP.NET Core MVC 'de, <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> ve <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> gibi öznitelikler bir eylemi belgelemek için kullanılır. <xref:tutorials/web-api-help-pages-using-swagger>Web API 'nizi belgeleme hakkında daha fazla ayrıntıya gider.

Paketteki çözümleyicilerden biri ile <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> açıklanmış denetimleri inceler ve yanıtlarını tamamen belgemeyen eylemleri tanımlar. Aşağıdaki örneği inceleyin:

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

Yukarıdaki eylem HTTP 200 başarılı dönüş türünü belgeler, ancak HTTP 404 hata durumu kodunu belgeetmez. Çözümleyici, HTTP 404 durum kodu için eksik belgeleri uyarı olarak bildirir. Sorunu gidermeye yönelik bir seçenek sağlanır.

![Çözümleyici bir uyarı bildiriyor](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
