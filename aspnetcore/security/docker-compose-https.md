---
title: HTTPS ile Docker Compose kullanarak kapsayıcıda ASP.NET Core görüntü barındırma
author: ravipal
description: HTTPS üzerinden Docker Compose ile ASP.NET Core görüntülerinin nasıl barındırılacağını öğrenin
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
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
uid: security/docker-compose-https
ms.openlocfilehash: cd46fdcbe10dc0b7829fbe7eaef821889f395df4
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393710"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a><span data-ttu-id="68258-103">HTTPS üzerinden Docker Compose olan ASP.NET Core görüntülerini barındırma</span><span class="sxs-lookup"><span data-stu-id="68258-103">Hosting ASP.NET Core images with Docker Compose over HTTPS</span></span>


<span data-ttu-id="68258-104">ASP.NET Core [Varsayılan olarak https](./enforcing-ssl.md)kullanır.</span><span class="sxs-lookup"><span data-stu-id="68258-104">ASP.NET Core uses [HTTPS by default](./enforcing-ssl.md).</span></span> <span data-ttu-id="68258-105">[Https](https://en.wikipedia.org/wiki/HTTPS) , güven, kimlik ve şifreleme için [sertifikalara](https://en.wikipedia.org/wiki/Public_key_certificate) bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="68258-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="68258-106">Bu belgede önceden oluşturulmuş kapsayıcı görüntülerinin HTTPS ile nasıl çalıştırılacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="68258-106">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="68258-107">Geliştirme senaryoları için bkz. [https üzerinden Docker ile ASP.NET Core uygulamaları geliştirme](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) .</span><span class="sxs-lookup"><span data-stu-id="68258-107">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="68258-108">Bu örnek, [Docker Istemcisinin](https://www.docker.com/products/docker) [Docker 17,06](https://docs.docker.com/release-notes/docker-ce) veya sonraki bir sürümünü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="68258-108">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="68258-109">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="68258-109">Prerequisites</span></span>

<span data-ttu-id="68258-110">Bu belgedeki bazı yönergeler için [.NET Core 2,2 SDK](https://dotnet.microsoft.com/download) veya üzeri gereklidir.</span><span class="sxs-lookup"><span data-stu-id="68258-110">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="68258-111">Sertifikalar</span><span class="sxs-lookup"><span data-stu-id="68258-111">Certificates</span></span>

<span data-ttu-id="68258-112">Bir etki alanı için [Üretim barındırma](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) için bir [sertifika yetkilisinden](https://wikipedia.org/wiki/Certificate_authority) bir sertifika gereklidir.</span><span class="sxs-lookup"><span data-stu-id="68258-112">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="68258-113">[Let's Encrypt](https://letsencrypt.org/) , ücretsiz sertifikalar sunan bir sertifika yetkilisindir.</span><span class="sxs-lookup"><span data-stu-id="68258-113">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="68258-114">Bu belge, üzerine önceden oluşturulmuş görüntüleri barındırmak için [otomatik olarak imzalanan geliştirme sertifikaları](https://wikipedia.org/wiki/Self-signed_certificate) kullanır `localhost` .</span><span class="sxs-lookup"><span data-stu-id="68258-114">This document uses [self-signed development certificates](https://wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="68258-115">Yönergeler, üretim sertifikalarını kullanmaya benzerdir.</span><span class="sxs-lookup"><span data-stu-id="68258-115">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="68258-116">Üretim sertifikaları için:</span><span class="sxs-lookup"><span data-stu-id="68258-116">For production certificates:</span></span>

* <span data-ttu-id="68258-117">`dotnet dev-certs`Araç gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="68258-117">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="68258-118">Sertifikaların, yönergelerde kullanılan konumda depolanması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="68258-118">Certificates don't need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="68258-119">Sertifikaları, site dizini dışında herhangi bir konumda depolayın.</span><span class="sxs-lookup"><span data-stu-id="68258-119">Store the certificates in any location outside the site directory.</span></span>

<span data-ttu-id="68258-120">Aşağıdaki bölümde yer alan yönergeler, `volumes` *Docker-Compose. yıml* içindeki özelliğini kullanarak sertifikaları kapsayıcılara bağlama.</span><span class="sxs-lookup"><span data-stu-id="68258-120">The instructions contained in the following section volume mount certificates into containers using the `volumes` property in *docker-compose.yml.*</span></span> <span data-ttu-id="68258-121">`COPY` *Dockerfile*dosyasında bir komutla kapsayıcı görüntülerine sertifika ekleyebilirsiniz, ancak bunu yapmanız önerilmez.</span><span class="sxs-lookup"><span data-stu-id="68258-121">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="68258-122">Sertifikaları bir görüntüye kopyalamak aşağıdaki nedenlerden dolayı önerilmez:</span><span class="sxs-lookup"><span data-stu-id="68258-122">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="68258-123">Geliştirici sertifikaları ile test için aynı görüntünün kullanımını zorlaştırır.</span><span class="sxs-lookup"><span data-stu-id="68258-123">It makes it difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="68258-124">Üretim sertifikaları ile barındırmak için aynı görüntünün kullanımını zorlaştırır.</span><span class="sxs-lookup"><span data-stu-id="68258-124">It makes it difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="68258-125">Sertifika açıklanmasının önemli bir riski vardır.</span><span class="sxs-lookup"><span data-stu-id="68258-125">There is significant risk of certificate disclosure.</span></span>

## <a name="starting-a-container-with-https-support-using-docker-compose"></a><span data-ttu-id="68258-126">Docker Compose kullanarak https desteğiyle kapsayıcı başlatma</span><span class="sxs-lookup"><span data-stu-id="68258-126">Starting a container with https support using docker compose</span></span>

<span data-ttu-id="68258-127">İşletim sistemi yapılandırmanız için aşağıdaki yönergeleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="68258-127">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="68258-128">Linux kapsayıcıları kullanan Windows</span><span class="sxs-lookup"><span data-stu-id="68258-128">Windows using Linux containers</span></span>

<span data-ttu-id="68258-129">Sertifika Oluştur ve yerel makineyi Yapılandır:</span><span class="sxs-lookup"><span data-stu-id="68258-129">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="68258-130">Yukarıdaki komutlarda, öğesini parolayla değiştirin `{ password here }` .</span><span class="sxs-lookup"><span data-stu-id="68258-130">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="68258-131">Aşağıdaki içeriğe sahip bir _Docker-Compose. Debug. yml_ dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="68258-131">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```yaml
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="68258-132">Docker Compose dosyasında belirtilen parolanın, sertifika için kullanılan parolayla eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="68258-132">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="68258-133">Kapsayıcıyı HTTPS için yapılandırılan ASP.NET Core başlatın:</span><span class="sxs-lookup"><span data-stu-id="68258-133">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a><span data-ttu-id="68258-134">macOS veya Linux</span><span class="sxs-lookup"><span data-stu-id="68258-134">macOS or Linux</span></span>

<span data-ttu-id="68258-135">Sertifika Oluştur ve yerel makineyi Yapılandır:</span><span class="sxs-lookup"><span data-stu-id="68258-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="68258-136">`dotnet dev-certs https --trust` yalnızca macOS ve Windows 'da desteklenir.</span><span class="sxs-lookup"><span data-stu-id="68258-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="68258-137">Kuruluşunuz tarafından desteklenen şekilde Linux üzerindeki sertifikalara güvenmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="68258-137">You need to trust certificates on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="68258-138">Büyük olasılıkla, tarayıcınızda sertifikaya güvenmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="68258-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="68258-139">Yukarıdaki komutlarda, öğesini parolayla değiştirin `{ password here }` .</span><span class="sxs-lookup"><span data-stu-id="68258-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="68258-140">Aşağıdaki içeriğe sahip bir _Docker-Compose. Debug. yml_ dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="68258-140">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```yaml
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="68258-141">Docker Compose dosyasında belirtilen parolanın, sertifika için kullanılan parolayla eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="68258-141">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="68258-142">Kapsayıcıyı HTTPS için yapılandırılan ASP.NET Core başlatın:</span><span class="sxs-lookup"><span data-stu-id="68258-142">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a><span data-ttu-id="68258-143">Windows kapsayıcıları kullanan pencereler</span><span class="sxs-lookup"><span data-stu-id="68258-143">Windows using Windows containers</span></span>

<span data-ttu-id="68258-144">Sertifika Oluştur ve yerel makineyi Yapılandır:</span><span class="sxs-lookup"><span data-stu-id="68258-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="68258-145">Yukarıdaki komutlarda, öğesini parolayla değiştirin `{ password here }` .</span><span class="sxs-lookup"><span data-stu-id="68258-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="68258-146">Aşağıdaki içeriğe sahip bir _Docker-Compose. Debug. yml_ dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="68258-146">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```yaml
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=C:\https\aspnetapp.pfx
    volumes:
      - ${USERPROFILE}\.aspnet\https:C:\https:ro
```
<span data-ttu-id="68258-147">Docker Compose dosyasında belirtilen parolanın, sertifika için kullanılan parolayla eşleşmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="68258-147">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="68258-148">Kapsayıcıyı HTTPS için yapılandırılan ASP.NET Core başlatın:</span><span class="sxs-lookup"><span data-stu-id="68258-148">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
