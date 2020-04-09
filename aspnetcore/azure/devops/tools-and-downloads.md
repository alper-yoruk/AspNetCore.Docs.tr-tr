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
# <a name="tools-and-downloads"></a><span data-ttu-id="528c4-103">Araçlar ve indirmeler</span><span class="sxs-lookup"><span data-stu-id="528c4-103">Tools and downloads</span></span>

<span data-ttu-id="528c4-104">Azure'un, [Azure portalı, Azure](https://portal.azure.com) [CLI, Azure](/cli/azure/) [PowerShell,](/powershell/azure/overview)Azure Bulut [Shell](https://shell.azure.com/bash)ve Visual Studio gibi kaynakları sağlama ve yönetme gibi çeşitli arabirimleri vardır.</span><span class="sxs-lookup"><span data-stu-id="528c4-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="528c4-105">Bu kılavuz minimalist bir yaklaşım benimser ve gerekli adımları azaltmak için mümkün olduğunda Azure Bulut Kabuğu'nu kullanır.</span><span class="sxs-lookup"><span data-stu-id="528c4-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="528c4-106">Ancak, Azure portalı bazı bölümler için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="528c4-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="528c4-107">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="528c4-107">Prerequisites</span></span>

<span data-ttu-id="528c4-108">Aşağıdaki abonelikler gereklidir:</span><span class="sxs-lookup"><span data-stu-id="528c4-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="528c4-109">Azure &mdash; Hesabınız yoksa, [ücretsiz deneme sürümü alın.](https://azure.microsoft.com/free/)</span><span class="sxs-lookup"><span data-stu-id="528c4-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/).</span></span>
* <span data-ttu-id="528c4-110">Azure DevOps &mdash; Hizmetleri, Azure DevOps aboneliğiniz ve kuruluşunuz Bölüm 4'te oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="528c4-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="528c4-111">GitHub &mdash; Hesabınız [yoksa, ücretsiz kaydolun.](https://github.com/join)</span><span class="sxs-lookup"><span data-stu-id="528c4-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="528c4-112">Aşağıdaki araçlar gereklidir:</span><span class="sxs-lookup"><span data-stu-id="528c4-112">The following tools are required:</span></span>

* <span data-ttu-id="528c4-113">[Git](https://git-scm.com/downloads) &mdash; Git temel bir anlayış bu kılavuz için tavsiye edilir.</span><span class="sxs-lookup"><span data-stu-id="528c4-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="528c4-114">Git [dokümantasyonlarını](https://git-scm.com/doc)gözden geçirin, özellikle [git remote](https://git-scm.com/docs/git-remote) ve [git itin.](https://git-scm.com/docs/git-push)</span><span class="sxs-lookup"><span data-stu-id="528c4-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="528c4-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Sürüm 2.1.300 veya sonraki örnek uygulamayı oluşturmak ve çalıştırmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="528c4-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="528c4-116">Visual Studio **.NET Core çapraz platform geliştirme iş** yüküyle yüklüyse, .NET Core SDK zaten yüklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="528c4-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="528c4-117">.NET Core SDK kurulumunuzu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="528c4-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="528c4-118">Komut kabuğunu açın ve aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="528c4-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="528c4-119">Önerilen araçlar (yalnızca Windows)</span><span class="sxs-lookup"><span data-stu-id="528c4-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="528c4-120">[Visual Studio'nun](https://visualstudio.microsoft.com)sağlam Azure araçları, bu kılavuzda açıklanan işlevlerin çoğu için bir GUI sağlar.</span><span class="sxs-lookup"><span data-stu-id="528c4-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="528c4-121">Visual Studio herhangi bir sürümü, ücretsiz Visual Studio Community Edition da dahil olmak üzere çalışacaktır.</span><span class="sxs-lookup"><span data-stu-id="528c4-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="528c4-122">Öğreticiler, Visual Studio ile ve Visual Studio olmadan geliştirme, dağıtım ve DevOps göstermek için yazılmıştır.</span><span class="sxs-lookup"><span data-stu-id="528c4-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="528c4-123">Visual Studio'nun aşağıdaki [iş yüklerinin](/visualstudio/install/modify-visual-studio) yüklü olduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="528c4-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="528c4-124">ASP.NET ve web geliştirme</span><span class="sxs-lookup"><span data-stu-id="528c4-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="528c4-125">Azure geliştirme</span><span class="sxs-lookup"><span data-stu-id="528c4-125">Azure development</span></span>
  * <span data-ttu-id="528c4-126">.NET Core çoklu platform geliştirme</span><span class="sxs-lookup"><span data-stu-id="528c4-126">.NET Core cross-platform development</span></span>
