---
title: HTTPS üzerinden Docker ile görüntüleri barındırma ASP.NET Core
author: rick-anderson
description: HTTPS üzerinden Docker ile ASP.NET Core görüntülerini nasıl barındıralabileceğinizi öğrenin
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/docker-https
ms.openlocfilehash: fd2338f433e1651fb974c95b293eaa3fb2009046
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403111"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="23604-103">HTTPS üzerinden Docker ile görüntüleri barındırma ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="23604-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="23604-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="23604-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="23604-105">ASP.NET Core [Varsayılan olarak https](/aspnet/core/security/enforcing-ssl)kullanır.</span><span class="sxs-lookup"><span data-stu-id="23604-105">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="23604-106">[Https](https://en.wikipedia.org/wiki/HTTPS) , güven, kimlik ve şifreleme için [sertifikalara](https://en.wikipedia.org/wiki/Public_key_certificate) bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="23604-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="23604-107">Bu belgede önceden oluşturulmuş kapsayıcı görüntülerinin HTTPS ile nasıl çalıştırılacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="23604-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="23604-108">Geliştirme senaryoları için bkz. [https üzerinden Docker ile ASP.NET Core uygulamaları geliştirme](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) .</span><span class="sxs-lookup"><span data-stu-id="23604-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="23604-109">Bu örnek, [Docker Istemcisinin](https://www.docker.com/products/docker) [Docker 17,06](https://docs.docker.com/release-notes/docker-ce) veya sonraki bir sürümünü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="23604-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="23604-110">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="23604-110">Prerequisites</span></span>

<span data-ttu-id="23604-111">Bu belgedeki bazı yönergeler için [.NET Core 2,2 SDK](https://dotnet.microsoft.com/download) veya üzeri gereklidir.</span><span class="sxs-lookup"><span data-stu-id="23604-111">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="23604-112">Sertifikalar</span><span class="sxs-lookup"><span data-stu-id="23604-112">Certificates</span></span>

<span data-ttu-id="23604-113">Bir etki alanı için [Üretim barındırma](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) için bir [sertifika yetkilisinden](https://wikipedia.org/wiki/Certificate_authority) bir sertifika gereklidir.</span><span class="sxs-lookup"><span data-stu-id="23604-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="23604-114">[Let's Encrypt](https://letsencrypt.org/), ücretsiz sertifikalar sunan bir sertifika yetkilisindir.</span><span class="sxs-lookup"><span data-stu-id="23604-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="23604-115">Bu belge, üzerine önceden oluşturulmuş görüntüleri barındırmak için [otomatik olarak imzalanan geliştirme sertifikaları](https://en.wikipedia.org/wiki/Self-signed_certificate) kullanır `localhost` .</span><span class="sxs-lookup"><span data-stu-id="23604-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="23604-116">Yönergeler, üretim sertifikalarını kullanmaya benzerdir.</span><span class="sxs-lookup"><span data-stu-id="23604-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="23604-117">Üretim sertifikaları için:</span><span class="sxs-lookup"><span data-stu-id="23604-117">For production certs:</span></span>

* <span data-ttu-id="23604-118">`dotnet dev-certs`Araç gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="23604-118">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="23604-119">Sertifikaların, yönergelerde kullanılan konumda depolanması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="23604-119">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="23604-120">Herhangi bir konumun çalışması gerekir, ancak bu sertifikalar site dizininizde depolanarak önerilmez.</span><span class="sxs-lookup"><span data-stu-id="23604-120">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="23604-121">Aşağıdaki bölümde yer alan yönergeler, Docker 'ın komut satırı seçeneğini kullanarak sertifikaları kapsayıcılara bağlama `-v` .</span><span class="sxs-lookup"><span data-stu-id="23604-121">The instructions contained in the following section volume mount certificates into containers using Docker's `-v` command-line option.</span></span> <span data-ttu-id="23604-122">`COPY` *Dockerfile*dosyasında bir komutla kapsayıcı görüntülerine sertifika ekleyebilirsiniz, ancak bunu yapmanız önerilmez.</span><span class="sxs-lookup"><span data-stu-id="23604-122">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="23604-123">Sertifikaları bir görüntüye kopyalamak aşağıdaki nedenlerden dolayı önerilmez:</span><span class="sxs-lookup"><span data-stu-id="23604-123">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="23604-124">Geliştirici sertifikaları ile test etmek için aynı görüntünün kullanımını zorlaştırır.</span><span class="sxs-lookup"><span data-stu-id="23604-124">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="23604-125">Üretim sertifikaları ile barındırmak için aynı görüntünün kullanımını zorlaştırır.</span><span class="sxs-lookup"><span data-stu-id="23604-125">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="23604-126">Sertifika açıklanmasının önemli bir riski vardır.</span><span class="sxs-lookup"><span data-stu-id="23604-126">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="23604-127">HTTPS ile önceden oluşturulmuş kapsayıcı görüntülerini çalıştırma</span><span class="sxs-lookup"><span data-stu-id="23604-127">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="23604-128">İşletim sistemi yapılandırmanız için aşağıdaki yönergeleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="23604-128">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="23604-129">Linux kapsayıcıları kullanan Windows</span><span class="sxs-lookup"><span data-stu-id="23604-129">Windows using Linux containers</span></span>

<span data-ttu-id="23604-130">Sertifika Oluştur ve yerel makineyi Yapılandır:</span><span class="sxs-lookup"><span data-stu-id="23604-130">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="23604-131">Yukarıdaki komutlarda, öğesini parolayla değiştirin `{ password here }` .</span><span class="sxs-lookup"><span data-stu-id="23604-131">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="23604-132">Bir komut kabuğu 'nda HTTPS için yapılandırılan ASP.NET Core kapsayıcı görüntüsünü çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="23604-132">Run the container image with ASP.NET Core configured for HTTPS in a command shell:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="23604-133">[PowerShell](/powershell/scripting/overview)kullanırken `%USERPROFILE%` ile değiştirin `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="23604-133">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="23604-134">Parolanın, sertifika için kullanılan parolayla eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="23604-134">The password must match the password used for the certificate.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="23604-135">macOS veya Linux</span><span class="sxs-lookup"><span data-stu-id="23604-135">macOS or Linux</span></span>

<span data-ttu-id="23604-136">Sertifika Oluştur ve yerel makineyi Yapılandır:</span><span class="sxs-lookup"><span data-stu-id="23604-136">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="23604-137">`dotnet dev-certs https --trust`yalnızca macOS ve Windows 'da desteklenir.</span><span class="sxs-lookup"><span data-stu-id="23604-137">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="23604-138">Linux 'ta sertifikalarınızın desteklediği şekilde sertifika almanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="23604-138">You need to trust certs on Linux in the way that is supported by your distro.</span></span> <span data-ttu-id="23604-139">Büyük olasılıkla, tarayıcınızda sertifikaya güvenmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="23604-139">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="23604-140">Yukarıdaki komutlarda, öğesini parolayla değiştirin `{ password here }` .</span><span class="sxs-lookup"><span data-stu-id="23604-140">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="23604-141">Kapsayıcı görüntüsünü HTTPS için yapılandırılan ASP.NET Core çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="23604-141">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="23604-142">Parolanın, sertifika için kullanılan parolayla eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="23604-142">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="23604-143">Windows kapsayıcıları kullanan pencereler</span><span class="sxs-lookup"><span data-stu-id="23604-143">Windows using Windows containers</span></span>

<span data-ttu-id="23604-144">Sertifika Oluştur ve yerel makineyi Yapılandır:</span><span class="sxs-lookup"><span data-stu-id="23604-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="23604-145">Yukarıdaki komutlarda, öğesini parolayla değiştirin `{ password here }` .</span><span class="sxs-lookup"><span data-stu-id="23604-145">In the preceding commands, replace `{ password here }` with a password.</span></span> <span data-ttu-id="23604-146">[PowerShell](/powershell/scripting/overview)kullanırken `%USERPROFILE%` ile değiştirin `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="23604-146">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="23604-147">Kapsayıcı görüntüsünü HTTPS için yapılandırılan ASP.NET Core çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="23604-147">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="23604-148">Parolanın, sertifika için kullanılan parolayla eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="23604-148">The password must match the password used for the certificate.</span></span> <span data-ttu-id="23604-149">[PowerShell](/powershell/scripting/overview)kullanırken `%USERPROFILE%` ile değiştirin `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="23604-149">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>
