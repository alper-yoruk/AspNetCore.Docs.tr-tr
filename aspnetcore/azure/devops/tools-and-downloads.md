---
title: Araçlar ve İndirmeler-ASP.NET Core ve Azure ile DevOps
author: CamSoper
description: ASP.NET Core ve Azure ile DevOps için gereken araçlar ve İndirmeler.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: ed8aee214ff9b9e941aeea01887882c3bdfc56a7
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400303"
---
# <a name="tools-and-downloads"></a><span data-ttu-id="41e04-103">Araçlar ve indirmeler</span><span class="sxs-lookup"><span data-stu-id="41e04-103">Tools and downloads</span></span>

<span data-ttu-id="41e04-104">Azure 'da [Azure Portal](https://portal.azure.com), [azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash)ve Visual Studio gibi kaynakları sağlamak ve yönetmek için çeşitli arabirimler vardır.</span><span class="sxs-lookup"><span data-stu-id="41e04-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="41e04-105">Bu kılavuz, en az aList yaklaşımla yararlanır ve gerekli adımları azaltmak için mümkün olan her durumda Azure Cloud Shell kullanır.</span><span class="sxs-lookup"><span data-stu-id="41e04-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="41e04-106">Ancak, bazı bölümlerinde Azure portal kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="41e04-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="41e04-107">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="41e04-107">Prerequisites</span></span>

<span data-ttu-id="41e04-108">Aşağıdaki abonelikler gereklidir:</span><span class="sxs-lookup"><span data-stu-id="41e04-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="41e04-109">Azure &mdash; hesabınız yoksa [ücretsiz deneme sürümü alın](https://azure.microsoft.com/free/dotnet/).</span><span class="sxs-lookup"><span data-stu-id="41e04-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/dotnet/).</span></span>
* <span data-ttu-id="41e04-110">Azure DevOps Services &mdash; , Azure DevOps aboneliğiniz ve kuruluşunuz Bölüm 4 ' te oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="41e04-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="41e04-111">GitHub &mdash; hesabınız yoksa [ücretsiz kaydolun](https://github.com/join).</span><span class="sxs-lookup"><span data-stu-id="41e04-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="41e04-112">Aşağıdaki araçlar gereklidir:</span><span class="sxs-lookup"><span data-stu-id="41e04-112">The following tools are required:</span></span>

* <span data-ttu-id="41e04-113">[Git](https://git-scm.com/downloads) &mdash; Bu kılavuz için git 'in temel olarak anlaşılmasına önerilir.</span><span class="sxs-lookup"><span data-stu-id="41e04-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="41e04-114">[Git belgelerini](https://git-scm.com/doc)gözden geçirin, özellikle [Git uzak](https://git-scm.com/docs/git-remote) ve [Git Push](https://git-scm.com/docs/git-push).</span><span class="sxs-lookup"><span data-stu-id="41e04-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="41e04-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Örnek uygulamayı derlemek ve çalıştırmak için sürüm 2.1.300 veya üzeri gereklidir.</span><span class="sxs-lookup"><span data-stu-id="41e04-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="41e04-116">Visual Studio, **.NET Core platformlar arası geliştirme** iş yüküne yüklenmişse, .NET Core SDK zaten yüklüdür.</span><span class="sxs-lookup"><span data-stu-id="41e04-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="41e04-117">.NET Core SDK yüklemenizi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="41e04-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="41e04-118">Bir komut kabuğu açın ve şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="41e04-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="41e04-119">Önerilen araçlar (yalnızca Windows)</span><span class="sxs-lookup"><span data-stu-id="41e04-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="41e04-120">[Visual Studio](https://visualstudio.microsoft.com)'Nun sağlam Azure Araçları, bu kılavuzda açıklanan işlevselliğin çoğu IÇIN bir GUI sağlar.</span><span class="sxs-lookup"><span data-stu-id="41e04-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="41e04-121">Visual Studio 'nun tüm sürümleri, ücretsiz Visual Studio Community Edition da dahil çalışacaktır.</span><span class="sxs-lookup"><span data-stu-id="41e04-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="41e04-122">Öğreticiler, Visual Studio ile birlikte ve olmadan geliştirme, dağıtım ve DevOps 'u göstermek için yazılmıştır.</span><span class="sxs-lookup"><span data-stu-id="41e04-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="41e04-123">Visual Studio 'Nun aşağıdaki [iş yüklerinin](/visualstudio/install/modify-visual-studio) yüklü olduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="41e04-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="41e04-124">ASP.NET ve web geliştirme</span><span class="sxs-lookup"><span data-stu-id="41e04-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="41e04-125">Azure geliştirme</span><span class="sxs-lookup"><span data-stu-id="41e04-125">Azure development</span></span>
  * <span data-ttu-id="41e04-126">.NET Core çoklu platform geliştirme</span><span class="sxs-lookup"><span data-stu-id="41e04-126">.NET Core cross-platform development</span></span>
