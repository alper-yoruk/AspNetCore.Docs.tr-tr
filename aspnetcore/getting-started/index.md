---
title: ASP.NET Core kullanmaya başlayın
author: rick-anderson
description: Core'u kullanarak temel bir Hello World uygulaması oluşturan ve çalıştıran kısa ASP.NET bir öğretici.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
uid: getting-started
ms.openlocfilehash: 86a0c8d017138a949fddc0356f3de548d368a4c0
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417605"
---
# <a name="tutorial-get-started-with-aspnet-core"></a><span data-ttu-id="72931-103">Öğretici: ASP.NET Core ile başlayın</span><span class="sxs-lookup"><span data-stu-id="72931-103">Tutorial: Get started with ASP.NET Core</span></span>

<span data-ttu-id="72931-104">Bu öğretici, .NET Core CLI'yi kullanarak bir ASP.NET Core web uygulamasının nasıl oluşturulup çalıştırılabildiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="72931-104">This tutorial shows how to create and run an ASP.NET Core web app using the .NET Core CLI.</span></span>

<span data-ttu-id="72931-105">Şunları öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="72931-105">You'll learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="72931-106">Bir web uygulaması projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="72931-106">Create a web app project.</span></span>
> * <span data-ttu-id="72931-107">Geliştirme sertifikasına güvenin.</span><span class="sxs-lookup"><span data-stu-id="72931-107">Trust the development certificate.</span></span>
> * <span data-ttu-id="72931-108">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="72931-108">Run the app.</span></span>
> * <span data-ttu-id="72931-109">Razor sayfasını edin.</span><span class="sxs-lookup"><span data-stu-id="72931-109">Edit a Razor page.</span></span>

<span data-ttu-id="72931-110">Sonunda, yerel makinenizde çalışan bir web uygulamanız olur.</span><span class="sxs-lookup"><span data-stu-id="72931-110">At the end, you'll have a working web app running on your local machine.</span></span>

![Web uygulaması giriş sayfası](_static/home-page.png)

## <a name="prerequisites"></a><span data-ttu-id="72931-112">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="72931-112">Prerequisites</span></span>

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a><span data-ttu-id="72931-113">Bir web uygulaması projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="72931-113">Create a web app project</span></span>

<span data-ttu-id="72931-114">Komut kabuğunu açın ve aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="72931-114">Open a command shell, and enter the following command:</span></span>

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

<span data-ttu-id="72931-115">Yukarıdaki komut:</span><span class="sxs-lookup"><span data-stu-id="72931-115">The preceding command:</span></span>

* <span data-ttu-id="72931-116">Yeni bir web uygulaması oluşturur.</span><span class="sxs-lookup"><span data-stu-id="72931-116">Creates a new web app.</span></span>  
* <span data-ttu-id="72931-117">Parametre, `-o aspnetcoreapp` uygulamanın kaynak dosyalarıyla *aspnetcoreapp* adında bir dizin oluşturur.</span><span class="sxs-lookup"><span data-stu-id="72931-117">The `-o aspnetcoreapp` parameter creates a directory named *aspnetcoreapp* with the source files for the app.</span></span>

### <a name="trust-the-development-certificate"></a><span data-ttu-id="72931-118">Geliştirme sertifikasına güvenin</span><span class="sxs-lookup"><span data-stu-id="72931-118">Trust the development certificate</span></span>

<span data-ttu-id="72931-119">HTTPS geliştirme sertifikasına güvenin:</span><span class="sxs-lookup"><span data-stu-id="72931-119">Trust the HTTPS development certificate:</span></span>

# <a name="windows"></a>[<span data-ttu-id="72931-120">Windows</span><span class="sxs-lookup"><span data-stu-id="72931-120">Windows</span></span>](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="72931-121">Önceki komut aşağıdaki iletişim kutusunu görüntüler:</span><span class="sxs-lookup"><span data-stu-id="72931-121">The preceding command displays the following dialog:</span></span>

![Güvenlik uyarısı iletişim kutusu](~/getting-started/_static/cert.png)

<span data-ttu-id="72931-123">Geliştirme sertifikasına güvenmeyi kabul ederseniz **Evet'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="72931-123">Select **Yes** if you agree to trust the development certificate.</span></span>

# <a name="macos"></a>[<span data-ttu-id="72931-124">macOS</span><span class="sxs-lookup"><span data-stu-id="72931-124">macOS</span></span>](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="72931-125">Önceki komut aşağıdaki iletiyi görüntüler:</span><span class="sxs-lookup"><span data-stu-id="72931-125">The preceding command displays the following message:</span></span>

<span data-ttu-id="72931-126">*HTTPS geliştirme sertifikasına güvenmek istendi. Sertifikaya zaten güvenilen değilse, aşağıdaki komutu çalıştıracağız:*`'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span><span class="sxs-lookup"><span data-stu-id="72931-126">*Trusting the HTTPS development certificate was requested. If the certificate is not already trusted, we will run the following command:* `'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`</span></span>

<span data-ttu-id="72931-127">Bu komut, parolanızın sertifikayı sistem anahtarlığına yüklemesini isteyebilir.</span><span class="sxs-lookup"><span data-stu-id="72931-127">This command might prompt you for your password to install the certificate on the system keychain.</span></span> <span data-ttu-id="72931-128">Geliştirme sertifikasına güvenmeyi kabul ederseniz parolanızı girin.</span><span class="sxs-lookup"><span data-stu-id="72931-128">Enter your password if you agree to trust the development certificate.</span></span>

# <a name="linux"></a>[<span data-ttu-id="72931-129">Linux</span><span class="sxs-lookup"><span data-stu-id="72931-129">Linux</span></span>](#tab/linux)

<span data-ttu-id="72931-130">HTTPS geliştirme sertifikasına nasıl güveninizle ilgili Linux dağıtımınızın belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="72931-130">See the documentation for your Linux distribution on how to trust the HTTPS development certificate.</span></span>

---

<span data-ttu-id="72931-131">Daha fazla bilgi için bkz: [ASP.NET Çekirdek HTTPS geliştirme sertifikasına güven](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span><span class="sxs-lookup"><span data-stu-id="72931-131">For more information, see [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)</span></span>

## <a name="run-the-app"></a><span data-ttu-id="72931-132">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="72931-132">Run the app</span></span>

<span data-ttu-id="72931-133">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="72931-133">Run the following commands:</span></span>

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

<span data-ttu-id="72931-134">Komut kabuğu uygulamanın başladığını söyledikten sonra, `https://localhost:5001`'ye göz atın.</span><span class="sxs-lookup"><span data-stu-id="72931-134">After the command shell indicates that the app has started, browse to `https://localhost:5001`.</span></span>

## <a name="edit-a-razor-page"></a><span data-ttu-id="72931-135">Jilet sayfasını edin</span><span class="sxs-lookup"><span data-stu-id="72931-135">Edit a Razor page</span></span>

<span data-ttu-id="72931-136">*Sayfaları/Index.cshtml'i* açın ve sayfayı aşağıdaki vurgulanan biçimlendirmeyle değiştirin ve kaydedin:</span><span class="sxs-lookup"><span data-stu-id="72931-136">Open *Pages/Index.cshtml* and modify and save the page with the following highlighted markup:</span></span>

[!code-cshtml[](sample/index.cshtml?highlight=9)]

<span data-ttu-id="72931-137">Sayfaya `https://localhost:5001`göz atın, yenileyin ve değişikliklerin görüntülendiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="72931-137">Browse to `https://localhost:5001`, refresh the page, and verify the changes are displayed.</span></span>

## <a name="next-steps"></a><span data-ttu-id="72931-138">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="72931-138">Next steps</span></span>

<span data-ttu-id="72931-139">Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:</span><span class="sxs-lookup"><span data-stu-id="72931-139">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="72931-140">Bir web uygulaması projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="72931-140">Create a web app project.</span></span>
> * <span data-ttu-id="72931-141">Geliştirme sertifikasına güvenin.</span><span class="sxs-lookup"><span data-stu-id="72931-141">Trust the development certificate.</span></span>
> * <span data-ttu-id="72931-142">Projeyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="72931-142">Run the project.</span></span>
> * <span data-ttu-id="72931-143">Bir değişiklik yap.</span><span class="sxs-lookup"><span data-stu-id="72931-143">Make a change.</span></span>

<span data-ttu-id="72931-144">ASP.NET Core hakkında daha fazla bilgi edinmek için, girişte önerilen öğrenme yoluna bakın:</span><span class="sxs-lookup"><span data-stu-id="72931-144">To learn more about ASP.NET Core, see the recommended learning path in the introduction:</span></span>

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
