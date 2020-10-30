---
title: Araçlar ve İndirmeler-ASP.NET Core ve Azure ile DevOps
author: CamSoper
description: ASP.NET Core ve Azure ile DevOps için gereken araçlar ve İndirmeler.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
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
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 8c7f10a6b6f8504efaba6054533aba034982820c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056575"
---
# <a name="tools-and-downloads"></a>Araçlar ve indirmeler

Azure 'da [Azure Portal](https://portal.azure.com), [azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash)ve Visual Studio gibi kaynakları sağlamak ve yönetmek için çeşitli arabirimler vardır. Bu kılavuz, en az aList yaklaşımla yararlanır ve gerekli adımları azaltmak için mümkün olan her durumda Azure Cloud Shell kullanır. Ancak, bazı bölümlerinde Azure portal kullanılması gerekir.

## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki abonelikler gereklidir:

* Azure &mdash; hesabınız yoksa [ücretsiz deneme sürümü alın](https://azure.microsoft.com/free/dotnet/).
* Azure DevOps Services &mdash; , Azure DevOps aboneliğiniz ve kuruluşunuz Bölüm 4 ' te oluşturulmuştur.
* GitHub &mdash; hesabınız yoksa [ücretsiz kaydolun](https://github.com/join).

Aşağıdaki araçlar gereklidir:

* [Git](https://git-scm.com/downloads) &mdash; Bu kılavuz için git 'in temel olarak anlaşılmasına önerilir. [Git belgelerini](https://git-scm.com/doc)gözden geçirin, özellikle [Git uzak](https://git-scm.com/docs/git-remote) ve [Git Push](https://git-scm.com/docs/git-push).
* [.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Örnek uygulamayı derlemek ve çalıştırmak için sürüm 2.1.300 veya üzeri gereklidir. Visual Studio, **.NET Core platformlar arası geliştirme** iş yüküne yüklenmişse, .NET Core SDK zaten yüklüdür.

    .NET Core SDK yüklemenizi doğrulayın. Bir komut kabuğu açın ve şu komutu çalıştırın:

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Önerilen araçlar (yalnızca Windows)

* [Visual Studio](https://visualstudio.microsoft.com)'Nun sağlam Azure Araçları, bu kılavuzda açıklanan işlevselliğin çoğu IÇIN bir GUI sağlar. Visual Studio 'nun tüm sürümleri, ücretsiz Visual Studio Community Edition da dahil çalışacaktır. Öğreticiler, Visual Studio ile birlikte ve olmadan geliştirme, dağıtım ve DevOps 'u göstermek için yazılmıştır.

  Visual Studio 'Nun aşağıdaki [iş yüklerinin](/visualstudio/install/modify-visual-studio) yüklü olduğunu doğrulayın:

  * ASP.NET ve web geliştirme
  * Azure geliştirme
  * .NET Core platformlar arası geliştirme
