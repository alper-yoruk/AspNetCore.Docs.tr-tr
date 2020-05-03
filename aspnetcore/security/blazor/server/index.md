---
title: Güvenli ASP.NET Core Blazor Server uygulamaları
author: guardrex
description: Sunucu uygulamalarının ASP.NET Core uygulamalar Blazor olarak nasıl güvenli hale alınacağını öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: 324b3e8ed2d931c81bb41381caa469b0ec2f2fda
ms.sourcegitcommit: c19e388c83c981232e6f128d97440262adfe06e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/02/2020
ms.locfileid: "82727807"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Güvenli ASP.NET Core Blazor Server uygulamaları

[Luke Latham](https://github.com/guardrex) tarafından

## <a name="blazor-server-project-template"></a>Blazor sunucusu proje şablonu

Blazor sunucusu proje şablonu, proje oluşturulduğunda kimlik doğrulama için yapılandırılabilir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Kimlik doğrulama mekanizması ile yeni bir Blazor <xref:blazor/get-started> Server projesi oluşturmak Için makalesindeki Visual Studio kılavuzunu izleyin.

**Yeni ASP.NET Core Web uygulaması oluştur** Iletişim kutusunda **Blazor Server uygulama** şablonunu seçtikten sonra, **kimlik doğrulaması**altında **Değiştir** ' i seçin.

Diğer ASP.NET Core projelerine yönelik aynı kimlik doğrulama mekanizması kümesini sunmak için bir iletişim kutusu açılır:

* **Kimlik Doğrulaması Yok**
* **Bireysel kullanıcı hesapları** &ndash; Kullanıcı hesapları depolanabilir:
  * ASP.NET Core [kimlik](xref:security/authentication/identity) sistemini kullanarak uygulama içinde.
  * [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **İş veya okul hesapları**
* **Windows Kimlik Doğrulaması**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Kimlik doğrulama mekanizması ile yeni bir <xref:blazor/get-started> Blazor Server projesi oluşturmak için makalesindeki Visual Studio Code kılavuzunu izleyin:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

İzin verilen kimlik doğrulama`{AUTHENTICATION}`değerleri () aşağıdaki tabloda gösterilmiştir.

| Kimlik doğrulama mekanizması | Açıklama |
| ------------------------ | ----------- |
| `None`varsayılanını         | Kimlik doğrulaması yok |
| `Individual`             | Uygulamada ASP.NET Core kimlikle depolanan kullanıcılar |
| `IndividualB2C`          | [Azure AD B2C](xref:security/authentication/azure-ad-b2c) depolanan kullanıcılar |
| `SingleOrg`              | Tek bir kiracı için kuruluş kimlik doğrulaması |
| `MultiOrg`               | Birden çok kiracı için kuruluş kimlik doğrulaması |
| `Windows`                | Windows Kimlik Doğrulaması |

Bu `-o|--output` seçeneği kullanarak, komut `{APP NAME}` yer tutucusu için belirtilen değeri kullanır:

* Proje için bir klasör oluşturun.
* Projeyi adlandırın.

Daha fazla bilgi için .NET Core kılavuzundaki [DotNet New](/dotnet/core/tools/dotnet-new) komutuna bakın.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. <xref:blazor/get-started> Makalesindeki Mac için Visual Studio kılavuzunu izleyin.

1. **Yeni Blazor Server uygulamanızı yapılandırın** adımında **kimlik doğrulaması** açılan listesinden **bağımsız kimlik doğrulaması (uygulama içi)** seçeneğini belirleyin.

1. Uygulama, ASP.NET Core kimlik ile uygulamada depolanan bireysel kullanıcılar için oluşturulur.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

Kimlik doğrulama mekanizması ile yeni bir <xref:blazor/get-started> Blazor Server projesi oluşturmak için makalesindeki .NET Core CLI kılavuzunu izleyin:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

İzin verilen kimlik doğrulama`{AUTHENTICATION}`değerleri () aşağıdaki tabloda gösterilmiştir.

| Kimlik doğrulama mekanizması | Açıklama |
| ------------------------ | ----------- |
| `None`varsayılanını         | Kimlik doğrulaması yok |
| `Individual`             | Uygulamada ASP.NET Core kimlikle depolanan kullanıcılar |
| `IndividualB2C`          | [Azure AD B2C](xref:security/authentication/azure-ad-b2c) depolanan kullanıcılar |
| `SingleOrg`              | Tek bir kiracı için kuruluş kimlik doğrulaması |
| `MultiOrg`               | Birden çok kiracı için kuruluş kimlik doğrulaması |
| `Windows`                | Windows Kimlik Doğrulaması |

Bu `-o|--output` seçeneği kullanarak, komut `{APP NAME}` yer tutucusu için belirtilen değeri kullanır:

* Proje için bir klasör oluşturun.
* Projeyi adlandırın.

Daha fazla bilgi için .NET Core kılavuzundaki [DotNet New](/dotnet/core/tools/dotnet-new) komutuna bakın.

---

## <a name="secure-an-existing-app"></a>Mevcut bir uygulamanın güvenliğini sağlama

BlazorSunucu uygulamaları güvenlik için ASP.NET Core uygulamalarla aynı şekilde yapılandırılır. Daha fazla bilgi için, altındaki <xref:security/index>makalelere bakın.
