---
title: Güvenli ASP.NET Core Blazor Server uygulamaları
author: guardrex
description: BlazorSunucu uygulamalarının ASP.NET Core uygulamalar olarak nasıl güvenli hale alınacağını öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: a8604ca6ea60386bb3c54c950205ee695d37c689
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103792"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Güvenli ASP.NET Core Blazor Server uygulamaları

[Luke Latham](https://github.com/guardrex) tarafından

## <a name="blazor-server-project-template"></a>BlazorSunucu projesi şablonu

BlazorProje oluşturulduğunda sunucu projesi şablonu kimlik doğrulama için yapılandırılabilir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<xref:blazor/get-started> Blazor Kimlik doğrulama mekanizmasına sahip yeni bir sunucu projesi oluşturmak Için makalesindeki Visual Studio kılavuzunu izleyin.

**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda ** Blazor sunucu uygulama** şablonunu seçtikten sonra, **kimlik doğrulaması**altında **Değiştir** ' i seçin.

Diğer ASP.NET Core projelerine yönelik aynı kimlik doğrulama mekanizması kümesini sunmak için bir iletişim kutusu açılır:

* **Kimlik Doğrulaması Yok**
* **Bireysel kullanıcı hesapları**: Kullanıcı hesapları depolanabilir:
  * ASP.NET Core sistemi kullanılarak uygulama içinde [Identity](xref:security/authentication/identity) .
  * [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **İş veya okul hesapları**
* **Windows Kimlik Doğrulaması**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<xref:blazor/get-started> Blazor Kimlik doğrulama mekanizmasına sahip yeni bir sunucu projesi oluşturmak için makalesindeki Visual Studio Code kılavuzunu izleyin:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

İzin verilen kimlik doğrulama değerleri ( `{AUTHENTICATION}` ) aşağıdaki tabloda gösterilmiştir.

| Kimlik doğrulama mekanizması | Açıklama |
| ------------------------ | ----------- |
| `None`varsayılanını         | Kimlik doğrulaması yok |
| `Individual`             | ASP.NET Core ile uygulamada depolanan kullanıcılarIdentity |
| `IndividualB2C`          | [Azure AD B2C](xref:security/authentication/azure-ad-b2c) depolanan kullanıcılar |
| `SingleOrg`              | Tek bir kiracı için kuruluş kimlik doğrulaması |
| `MultiOrg`               | Birden çok kiracı için kuruluş kimlik doğrulaması |
| `Windows`                | Windows Kimlik Doğrulaması |

Bu `-o|--output` seçeneği kullanarak, komut yer tutucusu için belirtilen değeri kullanır `{APP NAME}` :

* Proje için bir klasör oluşturun.
* Projeyi adlandırın.

Daha fazla bilgi için .NET Core kılavuzundaki [DotNet New](/dotnet/core/tools/dotnet-new) komutuna bakın.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. Makalesindeki Mac için Visual Studio kılavuzunu izleyin <xref:blazor/get-started> .

1. **Yeni Blazor sunucu uygulamanızı yapılandırın** adımında **kimlik doğrulaması** açılan listesinden **bağımsız kimlik doğrulaması (uygulama içi)** seçeneğini belirleyin.

1. Uygulama, ASP.NET Core uygulamada depolanan bireysel kullanıcılar için oluşturulur Identity .

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

<xref:blazor/get-started> Blazor Kimlik doğrulama mekanizmasına sahip yeni bir sunucu projesi oluşturmak için makalesindeki .NET Core CLI kılavuzunu izleyin:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

İzin verilen kimlik doğrulama değerleri ( `{AUTHENTICATION}` ) aşağıdaki tabloda gösterilmiştir.

| Kimlik doğrulama mekanizması | Açıklama |
| ------------------------ | ----------- |
| `None`varsayılanını         | Kimlik doğrulaması yok |
| `Individual`             | ASP.NET Core ile uygulamada depolanan kullanıcılarIdentity |
| `IndividualB2C`          | [Azure AD B2C](xref:security/authentication/azure-ad-b2c) depolanan kullanıcılar |
| `SingleOrg`              | Tek bir kiracı için kuruluş kimlik doğrulaması |
| `MultiOrg`               | Birden çok kiracı için kuruluş kimlik doğrulaması |
| `Windows`                | Windows Kimlik Doğrulaması |

Bu `-o|--output` seçeneği kullanarak, komut yer tutucusu için belirtilen değeri kullanır `{APP NAME}` :

* Proje için bir klasör oluşturun.
* Projeyi adlandırın.

Daha fazla bilgi için .NET Core kılavuzundaki [DotNet New](/dotnet/core/tools/dotnet-new) komutuna bakın.

---

## <a name="secure-an-existing-app"></a>Mevcut bir uygulamanın güvenliğini sağlama

BlazorSunucu uygulamaları güvenlik için ASP.NET Core uygulamalarla aynı şekilde yapılandırılır. Daha fazla bilgi için, altındaki makalelere bakın <xref:security/index> .

## <a name="scaffold-identity"></a>İskeleIdentity

IdentitySunucu projesine bir yapı iskelesi ekleyin Blazor :

* [Mevcut yetkilendirme olmadan](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).
* [Yetkilendirme ile](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).
