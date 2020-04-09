---
title: Araçlar ve indirmeler - ASP.NET Core ve Azure ile DevOps
author: CamSoper
description: ASP.NET Core ve Azure ile DevOps için gerekli araçlar ve indirmeler.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 9c1042dd48b9167209b46e97a09e011b80e2609c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511151"
---
# <a name="tools-and-downloads"></a>Araçlar ve indirmeler

Azure'un, [Azure portalı, Azure](https://portal.azure.com) [CLI, Azure](/cli/azure/) [PowerShell,](/powershell/azure/overview)Azure Bulut [Shell](https://shell.azure.com/bash)ve Visual Studio gibi kaynakları sağlama ve yönetme gibi çeşitli arabirimleri vardır. Bu kılavuz minimalist bir yaklaşım benimser ve gerekli adımları azaltmak için mümkün olduğunda Azure Bulut Kabuğu'nu kullanır. Ancak, Azure portalı bazı bölümler için kullanılmalıdır.

## <a name="prerequisites"></a>Ön koşullar

Aşağıdaki abonelikler gereklidir:

* Azure &mdash; Hesabınız yoksa, [ücretsiz deneme sürümü alın.](https://azure.microsoft.com/free/)
* Azure DevOps &mdash; Hizmetleri, Azure DevOps aboneliğiniz ve kuruluşunuz Bölüm 4'te oluşturulur.
* GitHub &mdash; Hesabınız [yoksa, ücretsiz kaydolun.](https://github.com/join)

Aşağıdaki araçlar gereklidir:

* [Git](https://git-scm.com/downloads) &mdash; Git temel bir anlayış bu kılavuz için tavsiye edilir. Git [dokümantasyonlarını](https://git-scm.com/doc)gözden geçirin, özellikle [git remote](https://git-scm.com/docs/git-remote) ve [git itin.](https://git-scm.com/docs/git-push)
* [.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Sürüm 2.1.300 veya sonraki örnek uygulamayı oluşturmak ve çalıştırmak için gereklidir. Visual Studio **.NET Core çapraz platform geliştirme iş** yüküyle yüklüyse, .NET Core SDK zaten yüklenmiştir.

    .NET Core SDK kurulumunuzu doğrulayın. Komut kabuğunu açın ve aşağıdaki komutu çalıştırın:

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Önerilen araçlar (yalnızca Windows)

* [Visual Studio'nun](https://visualstudio.microsoft.com)sağlam Azure araçları, bu kılavuzda açıklanan işlevlerin çoğu için bir GUI sağlar. Visual Studio herhangi bir sürümü, ücretsiz Visual Studio Community Edition da dahil olmak üzere çalışacaktır. Öğreticiler, Visual Studio ile ve Visual Studio olmadan geliştirme, dağıtım ve DevOps göstermek için yazılmıştır.

  Visual Studio'nun aşağıdaki [iş yüklerinin](/visualstudio/install/modify-visual-studio) yüklü olduğunu doğrulayın:

  * ASP.NET ve web geliştirme
  * Azure geliştirme
  * .NET Core çoklu platform geliştirme
