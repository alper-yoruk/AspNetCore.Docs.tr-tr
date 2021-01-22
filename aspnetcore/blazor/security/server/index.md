---
title: Güvenli ASP.NET Core Blazor Server uygulamaları
author: guardrex
description: Uygulamaları ASP.NET Core uygulamalar olarak güvenli hale getirme hakkında bilgi edinin Blazor Server .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 5031273c3395be4365b3a6d239ebce7aaf9b66ac
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658644"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a>Güvenli ASP.NET Core Blazor Server uygulamaları

[Luke Latham](https://github.com/guardrex) tarafından

Blazor Server uygulamalar, güvenlik için ASP.NET Core uygulamalarla aynı şekilde yapılandırılır. Daha fazla bilgi için, altındaki makalelere bakın <xref:security/index> . Bu genel bakışın altındaki konular özellikle için geçerlidir Blazor Server .

## <a name="no-locblazor-server-project-template"></a>Blazor Server Proje şablonu

Proje Blazor Server oluşturulduğunda proje şablonu kimlik doğrulaması için yapılandırılabilir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<xref:blazor/tooling>Kimlik doğrulama mekanizmasına sahip yeni bir proje oluşturmak için Içindeki Visual Studio kılavuzunu izleyin Blazor Server .

**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda **Blazor Server uygulama** şablonunu seçtikten sonra, **kimlik doğrulaması** altında **Değiştir** ' i seçin.

Diğer ASP.NET Core projelerine yönelik aynı kimlik doğrulama mekanizması kümesini sunmak için bir iletişim kutusu açılır:

* **Kimlik doğrulaması yok**
* **Bireysel kullanıcı hesapları**: Kullanıcı hesapları depolanabilir:
  * ASP.NET Core sistemi kullanılarak uygulama içinde [Identity](xref:security/authentication/identity) .
  * [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **İş veya okul hesapları**
* **Windows Kimlik Doğrulaması**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<xref:blazor/tooling>Kimlik doğrulama mekanizmasıyla yeni bir proje oluşturmak için içindeki Visual Studio Code kılavuzunu izleyin Blazor Server :

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

İzin verilen kimlik doğrulama değerleri ( `{AUTHENTICATION}` ) aşağıdaki tabloda gösterilmiştir.

| Kimlik doğrulama mekanizması | Description |
| ------------------------ | ----------- |
| `None` varsayılanını         | Kimlik doğrulaması yok |
| `Individual`             | Uygulamada depolanan kullanıcılar ASP.NET Core Identity |
| `IndividualB2C`          | [Azure AD B2C](xref:security/authentication/azure-ad-b2c) depolanan kullanıcılar |
| `SingleOrg`              | Tek bir kiracı için kuruluş kimlik doğrulaması |
| `MultiOrg`               | Birden çok kiracı için kuruluş kimlik doğrulaması |
| `Windows`                | Windows Kimlik Doğrulaması |

Bu `-o|--output` seçeneği kullanarak, komut yer tutucusu için belirtilen değeri kullanır `{APP NAME}` :

* Proje için bir klasör oluşturun.
* Projeyi adlandırın.

Daha fazla bilgi için [`dotnet new`](/dotnet/core/tools/dotnet-new) .NET Core kılavuzundaki komutuna bakın.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. İçindeki Mac için Visual Studio kılavuzunu izleyin <xref:blazor/tooling> .

1. **Yeni Blazor Server uygulamanızı yapılandırın** adımındaki **kimlik doğrulaması** açılan listesinden **bireysel kimlik doğrulaması (uygulama içi)** seçeneğini belirleyin.

1. Uygulama, uygulamasında depolanan bireysel kullanıcılar için oluşturulur ASP.NET Core Identity .

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

Bir Blazor Server komut kabuğunda aşağıdaki komutu kullanarak kimlik doğrulama mekanizması ile yeni bir proje oluşturun:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

İzin verilen kimlik doğrulama değerleri ( `{AUTHENTICATION}` ) aşağıdaki tabloda gösterilmiştir.

| Kimlik doğrulama mekanizması | Description |
| ------------------------ | ----------- |
| `None` varsayılanını         | Kimlik doğrulaması yok |
| `Individual`             | Uygulamada depolanan kullanıcılar ASP.NET Core Identity |
| `IndividualB2C`          | [Azure AD B2C](xref:security/authentication/azure-ad-b2c) depolanan kullanıcılar |
| `SingleOrg`              | Tek bir kiracı için kuruluş kimlik doğrulaması |
| `MultiOrg`               | Birden çok kiracı için kuruluş kimlik doğrulaması |
| `Windows`                | Windows Kimlik Doğrulaması |

Bu `-o|--output` seçeneği kullanarak, komut yer tutucusu için belirtilen değeri kullanır `{APP NAME}` :

* Proje için bir klasör oluşturun.
* Projeyi adlandırın.

Daha fazla bilgi için:

* [`dotnet new`](/dotnet/core/tools/dotnet-new).NET Core kılavuzundaki komutuna bakın.
* Blazor ServerBir komut kabuğu 'nda () şablonu için Yardım komutunu yürütün `blazorserver` :

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a>İskele Identity

IdentityBir projeye yapı iskelesi Blazor Server :

* [Mevcut yetkilendirme olmadan](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).
* [Yetkilendirme ile](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).

## <a name="azure-app-service-on-linux-with-no-locidentity-server"></a>Sunucu ile Linux üzerinde Azure App Service Identity

Sunucu ile Linux üzerinde Azure App Service dağıtım sırasında sertifikayı vereni açıkça belirtin Identity . Daha fazla bilgi için bkz. <xref:security/authentication/identity/spa#azure-app-service-on-linux>.

## <a name="additional-resources"></a>Ek kaynaklar

* [Hızlı başlangıç: Microsoft 'a ASP.NET Core Web uygulamasına oturum açma ekleme](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [Hızlı başlangıç: ASP.NET Core Web API 'sini Microsoft Identity platformu ile koruma](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <xref:host-and-deploy/proxy-load-balancer>: Üzerine yönergeler içerir:
  * WAN sunucularında ve iç ağlarda HTTPS şema bilgilerini korumak için Iletilen üstbilgiler ara yazılımı kullanma.
  * El ile düzen yapılandırması, doğru istek yönlendirme için istek yolu değişiklikleri ve Linux ve IIS olmayan ters proxy 'ler için istek düzenini iletme dahil ek senaryolar ve kullanım örnekleri.
