---
title: Bireysel kullanıcı hesaplarıyla oluşturulan ASP.NET Core projelerine dayalı makaleler
author: rick-anderson
description: Bireysel kullanıcı hesaplarıyla oluşturulan ASP.NET Core projelerine göre makaleleri bulun.
ms.author: riande
ms.date: 12/11/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/individual
ms.openlocfilehash: 6b72612c12d52cfc1736c141bdad95ace6c84546
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022296"
---
# <a name="articles-based-on-aspnet-core-projects-created-with-individual-user-accounts"></a>Bireysel kullanıcı hesaplarıyla oluşturulan ASP.NET Core projelerine dayalı makaleler

ASP.NET Core Identity , Visual Studio 'daki proje şablonlarına "bireysel kullanıcı hesapları" seçeneği ile dahildir.

Kimlik doğrulama şablonları ile .NET Core CLI kullanılabilir `-au Individual` :

::: moniker range=">= aspnetcore-2.1"

```dotnetcli
dotnet new mvc -au Individual
dotnet new webapp -au Individual
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```dotnetcli
dotnet new mvc -au Individual
dotnet new razor -au Individual
```

::: moniker-end

Web API kimlik doğrulaması için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/5833) bakın.

<a name="no"></a>

## <a name="no-authentication"></a>Kimlik Doğrulaması Yok

Kimlik doğrulaması, .NET Core CLI `-au` seçeneğiyle belirtilir. Visual Studio 'da **kimlik doğrulaması Değiştir** iletişim kutusu yeni Web uygulamaları için kullanılabilir. Visual Studio 'da yeni Web uygulamaları için varsayılan değer **kimlik doğrulaması**değildir.

Kimlik doğrulaması olmadan oluşturulan projeler:

* Oturum açmak ve oturumu kapatmak için Web sayfalarını ve Kullanıcı arabirimini bulundurmayın.
* Kimlik doğrulama kodu içermiyor.

<a name="win"></a>

## <a name="windows-authentication"></a>Windows Kimlik Doğrulaması

.NET Core CLI yeni Web uygulamaları için Windows kimlik doğrulaması, `-au Windows` seçeneğiyle belirtilir. Visual Studio 'da **kimlik doğrulaması Değiştir** Iletişim kutusu **Windows kimlik doğrulama** seçeneklerini sağlar.

Windows kimlik doğrulaması seçilirse, uygulama [Windows kimlik doğrulama IIS modülünü](xref:host-and-deploy/iis/modules)kullanacak şekilde yapılandırılır. Windows kimlik doğrulaması, Intranet web sitelerine yöneliktir.

## <a name="dotnet-new-webapp-authentication-options"></a>DotNet yeni WebApp kimlik doğrulama seçenekleri

Aşağıdaki tabloda yeni Web uygulamaları için kullanılabilen kimlik doğrulama seçenekleri gösterilmektedir:

| Seçenek | Kimlik doğrulama türü | Daha fazla bilgi için bağlantı |
 | ----------------- | ------------ | ---------- |
| Yok            |  Kimlik doğrulaması yok | | 
| Ye      |  Tek kimlik doğrulama | <xref:security/authentication/identity>
| IndividualB2C   |  Azure AD B2C ile bulutta barındırılan bireysel kimlik doğrulaması | [Azure AD B2C](/azure/active-directory-b2c/) |
| SingleOrg       |  Tek bir kiracı için kuruluş kimlik doğrulaması | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| MultiOrg        |  Birden çok kiracı için kuruluş kimlik doğrulaması | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Windows kimlik doğrulaması | [Windows Kimlik Doğrulaması](xref:security/authentication/windowsauth)

## <a name="visual-studio-new-webapp-authentication-options"></a>Visual Studio yeni WebApp kimlik doğrulama seçenekleri

Aşağıdaki tabloda, Visual Studio ile yeni bir Web uygulaması oluştururken kullanılabilen kimlik doğrulama seçenekleri gösterilmektedir:

| Seçenek | Kimlik doğrulama türü | Daha fazla bilgi için bağlantı |
 | ----------------- | ------------ | ---------- |
| Yok            |  Kimlik doğrulaması yok | | 
| Uygulama içi bireysel kullanıcı hesapları/mağaza Kullanıcı hesapları |  Tek kimlik doğrulama | <xref:security/authentication/identity> |
| Bireysel kullanıcı hesapları/buluttaki mevcut bir Kullanıcı deposuna bağlanma |  Azure AD B2C ile bulutta barındırılan bireysel kimlik doğrulaması | [Azure AD B2C](/azure/active-directory-b2c/) |
| İş veya okul bulutu/tek kuruluş  |  Tek bir kiracı için kuruluş kimlik doğrulaması | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| İş veya okul bulutu/birden çok kuruluş |  Birden çok kiracı için kuruluş kimlik doğrulaması | [Azure AD](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) |
| Windows         |  Windows kimlik doğrulaması | [Windows Kimlik Doğrulaması](xref:security/authentication/windowsauth)

## <a name="additional-resources"></a>Ek kaynaklar

Aşağıdaki makalelerde, bireysel kullanıcı hesapları kullanan ASP.NET Core şablonlarda oluşturulan kodun nasıl kullanılacağı gösterilmektedir:

* [ASP.NET Core hesap onaylama ve parola kurtarma](xref:security/authentication/accconfirm)
* [Yetkilendirme ile korunan kullanıcı verileriyle ASP.NET Core uygulama oluşturma](xref:security/authorization/secure-data)
