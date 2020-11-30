---
title: HTTPS üzerinden Docker ile görüntüleri barındırma ASP.NET Core
author: rick-anderson
description: HTTPS üzerinden Docker ile ASP.NET Core görüntülerini nasıl barındıralabileceğinizi öğrenin
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
no-loc:
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
uid: security/docker-https
ms.openlocfilehash: a4aac2ce06fee20bdef157efc361f3099a217b1a
ms.sourcegitcommit: 619200f2981656ede6d89adb6a22ad1a0e16da22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96332160"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="1950e-103">HTTPS üzerinden Docker ile görüntüleri barındırma ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1950e-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="1950e-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1950e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1950e-105">ASP.NET Core [Varsayılan olarak https](./enforcing-ssl.md)kullanır.</span><span class="sxs-lookup"><span data-stu-id="1950e-105">ASP.NET Core uses [HTTPS by default](./enforcing-ssl.md).</span></span> <span data-ttu-id="1950e-106">[Https](https://en.wikipedia.org/wiki/HTTPS) , güven, kimlik ve şifreleme için [sertifikalara](https://en.wikipedia.org/wiki/Public_key_certificate) bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="1950e-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="1950e-107">Bu belgede önceden oluşturulmuş kapsayıcı görüntülerinin HTTPS ile nasıl çalıştırılacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="1950e-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="1950e-108">Geliştirme senaryoları için bkz. [https üzerinden Docker ile ASP.NET Core uygulamaları geliştirme](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) .</span><span class="sxs-lookup"><span data-stu-id="1950e-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="1950e-109">Bu örnek, [Docker Istemcisinin](https://www.docker.com/products/docker) [Docker 17,06](https://docs.docker.com/release-notes/docker-ce) veya sonraki bir sürümünü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="1950e-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1950e-110">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="1950e-110">Prerequisites</span></span>

<span data-ttu-id="1950e-111">Bu belgedeki bazı yönergeler için [.NET Core 2,2 SDK](https://dotnet.microsoft.com/download) veya üzeri gereklidir.</span><span class="sxs-lookup"><span data-stu-id="1950e-111">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="1950e-112">Sertifikalar</span><span class="sxs-lookup"><span data-stu-id="1950e-112">Certificates</span></span>

<span data-ttu-id="1950e-113">Bir etki alanı için [Üretim barındırma](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) için bir [sertifika yetkilisinden](https://wikipedia.org/wiki/Certificate_authority) bir sertifika gereklidir.</span><span class="sxs-lookup"><span data-stu-id="1950e-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="1950e-114">[Let's Encrypt](https://letsencrypt.org/) , ücretsiz sertifikalar sunan bir sertifika yetkilisindir.</span><span class="sxs-lookup"><span data-stu-id="1950e-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="1950e-115">Bu belge, üzerine önceden oluşturulmuş görüntüleri barındırmak için [otomatik olarak imzalanan geliştirme sertifikaları](https://en.wikipedia.org/wiki/Self-signed_certificate) kullanır `localhost` .</span><span class="sxs-lookup"><span data-stu-id="1950e-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="1950e-116">Yönergeler, üretim sertifikalarını kullanmaya benzerdir.</span><span class="sxs-lookup"><span data-stu-id="1950e-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="1950e-117">Geliştirme ve test için otomatik olarak imzalanan sertifikalar oluşturmak üzere [DotNet dev-CERT](/dotnet/core/additional-tools/self-signed-certificates-guide) kullanın.</span><span class="sxs-lookup"><span data-stu-id="1950e-117">Use [dotnet dev-certs](/dotnet/core/additional-tools/self-signed-certificates-guide) to create self-signed certificates for development and testing.</span></span>

<span data-ttu-id="1950e-118">Üretim sertifikaları için:</span><span class="sxs-lookup"><span data-stu-id="1950e-118">For production certs:</span></span>

* <span data-ttu-id="1950e-119">`dotnet dev-certs`Araç gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="1950e-119">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="1950e-120">Sertifikaların, yönergelerde kullanılan konumda depolanması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="1950e-120">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="1950e-121">Herhangi bir konumun çalışması gerekir, ancak bu sertifikalar site dizininizde depolanarak önerilmez.</span><span class="sxs-lookup"><span data-stu-id="1950e-121">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="1950e-122">Aşağıdaki bölümde yer alan yönergeler, Docker 'ın komut satırı seçeneğini kullanarak sertifikaları kapsayıcılara bağlama `-v` .</span><span class="sxs-lookup"><span data-stu-id="1950e-122">The instructions contained in the following section volume mount certificates into containers using Docker's `-v` command-line option.</span></span> <span data-ttu-id="1950e-123">`COPY` *Dockerfile* dosyasında bir komutla kapsayıcı görüntülerine sertifika ekleyebilirsiniz, ancak bunu yapmanız önerilmez.</span><span class="sxs-lookup"><span data-stu-id="1950e-123">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="1950e-124">Sertifikaları bir görüntüye kopyalamak aşağıdaki nedenlerden dolayı önerilmez:</span><span class="sxs-lookup"><span data-stu-id="1950e-124">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="1950e-125">Geliştirici sertifikaları ile test etmek için aynı görüntünün kullanımını zorlaştırır.</span><span class="sxs-lookup"><span data-stu-id="1950e-125">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="1950e-126">Üretim sertifikaları ile barındırmak için aynı görüntünün kullanımını zorlaştırır.</span><span class="sxs-lookup"><span data-stu-id="1950e-126">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="1950e-127">Sertifika açıklanmasının önemli bir riski vardır.</span><span class="sxs-lookup"><span data-stu-id="1950e-127">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="1950e-128">HTTPS ile önceden oluşturulmuş kapsayıcı görüntülerini çalıştırma</span><span class="sxs-lookup"><span data-stu-id="1950e-128">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="1950e-129">İşletim sistemi yapılandırmanız için aşağıdaki yönergeleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="1950e-129">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="1950e-130">Linux kapsayıcıları kullanan Windows</span><span class="sxs-lookup"><span data-stu-id="1950e-130">Windows using Linux containers</span></span>

<span data-ttu-id="1950e-131">Sertifika Oluştur ve yerel makineyi Yapılandır:</span><span class="sxs-lookup"><span data-stu-id="1950e-131">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="1950e-132">Yukarıdaki komutlarda, öğesini parolayla değiştirin `{ password here }` .</span><span class="sxs-lookup"><span data-stu-id="1950e-132">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="1950e-133">Bir komut kabuğu 'nda HTTPS için yapılandırılan ASP.NET Core kapsayıcı görüntüsünü çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1950e-133">Run the container image with ASP.NET Core configured for HTTPS in a command shell:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="1950e-134">[PowerShell](/powershell/scripting/overview)kullanırken `%USERPROFILE%` ile değiştirin `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="1950e-134">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="1950e-135">Parolanın, sertifika için kullanılan parolayla eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="1950e-135">The password must match the password used for the certificate.</span></span>


<span data-ttu-id="1950e-136">Note: Bu durumda sertifika bir `.pfx` dosya olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1950e-136">Note: The certificate in this case must be a `.pfx` file.</span></span>  <span data-ttu-id="1950e-137">`.crt` `.key` Parola ile veya olmayan bir veya dosya kullanmadan, örnek kapsayıcıda desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="1950e-137">Utilizing a `.crt` or `.key` file with or without the password isn't supported with the sample container.</span></span>  <span data-ttu-id="1950e-138">Örneğin, bir `.crt` dosya belirtirken kapsayıcı, ' sunucu modu SSL 'nin ilişkili özel anahtara sahip bir sertifika kullanması gerekir. ' gibi hata iletileri döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="1950e-138">For example, when specifying a `.crt` file, the container may return error messages such as 'The server mode SSL must use a certificate with the associated private key.'.</span></span> <span data-ttu-id="1950e-139">[WSL](/windows/wsl/about)kullanırken, sertifikanın doğru şekilde yüklendiğinden emin olmak için bağlama yolunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="1950e-139">When using [WSL](/windows/wsl/about), validate the mount path to ensure that the certificate loads correctly.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="1950e-140">macOS veya Linux</span><span class="sxs-lookup"><span data-stu-id="1950e-140">macOS or Linux</span></span>

<span data-ttu-id="1950e-141">Sertifika Oluştur ve yerel makineyi Yapılandır:</span><span class="sxs-lookup"><span data-stu-id="1950e-141">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="1950e-142">`dotnet dev-certs https --trust` yalnızca macOS ve Windows 'da desteklenir.</span><span class="sxs-lookup"><span data-stu-id="1950e-142">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="1950e-143">Kuruluşunuz tarafından desteklenen şekilde Linux üzerinde CERT 'ye güvenmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="1950e-143">You need to trust certs on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="1950e-144">Büyük olasılıkla, tarayıcınızda sertifikaya güvenmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="1950e-144">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="1950e-145">Yukarıdaki komutlarda, öğesini parolayla değiştirin `{ password here }` .</span><span class="sxs-lookup"><span data-stu-id="1950e-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="1950e-146">Kapsayıcı görüntüsünü HTTPS için yapılandırılan ASP.NET Core çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1950e-146">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="1950e-147">Parolanın, sertifika için kullanılan parolayla eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="1950e-147">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="1950e-148">Windows kapsayıcıları kullanan pencereler</span><span class="sxs-lookup"><span data-stu-id="1950e-148">Windows using Windows containers</span></span>

<span data-ttu-id="1950e-149">Sertifika Oluştur ve yerel makineyi Yapılandır:</span><span class="sxs-lookup"><span data-stu-id="1950e-149">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="1950e-150">Yukarıdaki komutlarda, öğesini parolayla değiştirin `{ password here }` .</span><span class="sxs-lookup"><span data-stu-id="1950e-150">In the preceding commands, replace `{ password here }` with a password.</span></span> <span data-ttu-id="1950e-151">[PowerShell](/powershell/scripting/overview)kullanırken `%USERPROFILE%` ile değiştirin `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="1950e-151">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="1950e-152">Kapsayıcı görüntüsünü HTTPS için yapılandırılan ASP.NET Core çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1950e-152">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="1950e-153">Parolanın, sertifika için kullanılan parolayla eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="1950e-153">The password must match the password used for the certificate.</span></span> <span data-ttu-id="1950e-154">[PowerShell](/powershell/scripting/overview)kullanırken `%USERPROFILE%` ile değiştirin `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="1950e-154">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>
