---
title: HTTPS ile komandite oluşturmayı kullanarak konteynerde ASP.NET Core görüntüsünü barındırma
author: ravipal
description: Docker Compose over HTTPS ile ASP.NET Core Görüntüleri nasıl barındırılanın öğrenin
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
no-loc:
- Let's Encrypt
uid: security/docker-compose-https
ms.openlocfilehash: 616ccf906e98534ffda08c0c2b6d0a171f063cc1
ms.sourcegitcommit: d03905aadf5ceac39fff17706481af7f6c130411
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80381826"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a><span data-ttu-id="ec181-103">Docker Compose ile ASP.NET Core görüntülerini HTTPS üzerinden barındırma</span><span class="sxs-lookup"><span data-stu-id="ec181-103">Hosting ASP.NET Core images with Docker Compose over HTTPS</span></span>


<span data-ttu-id="ec181-104">ASP.NET Core [varsayılan olarak HTTPS](/aspnet/core/security/enforcing-ssl)kullanır.</span><span class="sxs-lookup"><span data-stu-id="ec181-104">ASP.NET Core uses [HTTPS by default](/aspnet/core/security/enforcing-ssl).</span></span> <span data-ttu-id="ec181-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) güven, kimlik ve şifreleme [sertifikalarına](https://en.wikipedia.org/wiki/Public_key_certificate) dayanır.</span><span class="sxs-lookup"><span data-stu-id="ec181-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="ec181-106">Bu belge, önceden oluşturulmuş kapsayıcı görüntülerinin HTTPS ile nasıl çalıştırılabildiğini açıklar.</span><span class="sxs-lookup"><span data-stu-id="ec181-106">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="ec181-107">Geliştirme [senaryoları](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) için HTTPS üzerinden Docker ile ASP.NET Temel Uygulamalar Geliştirme bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="ec181-107">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="ec181-108">Bu örnek [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) veya daha sonra [Docker istemcisi](https://www.docker.com/products/docker)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ec181-108">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ec181-109">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="ec181-109">Prerequisites</span></span>

<span data-ttu-id="ec181-110">[.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) veya daha sonra bu belgedeki bazı talimatlar için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="ec181-110">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="ec181-111">Sertifikalar</span><span class="sxs-lookup"><span data-stu-id="ec181-111">Certificates</span></span>

<span data-ttu-id="ec181-112">Bir etki alanı için [üretim barındırma](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) için [sertifika yetkilisinden](https://wikipedia.org/wiki/Certificate_authority) bir sertifika gereklidir.</span><span class="sxs-lookup"><span data-stu-id="ec181-112">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="ec181-113">[Let's Encrypt](https://letsencrypt.org/)ücretsiz sertifika lar sunan bir sertifika yetkilisidir.</span><span class="sxs-lookup"><span data-stu-id="ec181-113">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="ec181-114">Bu belge, önceden oluşturulmuş görüntüleri barındırmak `localhost`için kendi imzalı geliştirme [sertifikalarını](https://wikipedia.org/wiki/Self-signed_certificate) kullanır.</span><span class="sxs-lookup"><span data-stu-id="ec181-114">This document uses [self-signed development certificates](https://wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="ec181-115">Yönergeler üretim sertifikaları kullanmaya benzer.</span><span class="sxs-lookup"><span data-stu-id="ec181-115">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="ec181-116">Üretim sertifikaları için:</span><span class="sxs-lookup"><span data-stu-id="ec181-116">For production certificates:</span></span>

* <span data-ttu-id="ec181-117">Araç `dotnet dev-certs` gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="ec181-117">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="ec181-118">Sertifikaların yönergelerde kullanılan konumda depolanmış olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="ec181-118">Certificates don't need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="ec181-119">Sertifikaları site dizininin dışındaki herhangi bir yerde saklayın.</span><span class="sxs-lookup"><span data-stu-id="ec181-119">Store the certificates in any location outside the site directory.</span></span>

<span data-ttu-id="ec181-120">Aşağıdaki bölümde yer alan talimatlar `volumes` *docker-compose.yml* özelliğini kullanarak konteyneriçine montaj sertifikaları.</span><span class="sxs-lookup"><span data-stu-id="ec181-120">The instructions contained in the following section volume mount certificates into containers using the `volumes` property in *docker-compose.yml.*</span></span> <span data-ttu-id="ec181-121">`COPY` *Dockerfile'da*komutu olan kapsayıcı görüntülere sertifika ekleyebilirsiniz, ancak bu önerilmez.</span><span class="sxs-lookup"><span data-stu-id="ec181-121">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="ec181-122">Sertifikaların görüntüye kopyalanması aşağıdaki nedenlerden dolayı önerilmez:</span><span class="sxs-lookup"><span data-stu-id="ec181-122">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="ec181-123">Geliştirici sertifikalarıyla test etmek için aynı görüntüyü kullanmayı zorlaştırır.</span><span class="sxs-lookup"><span data-stu-id="ec181-123">It makes it difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="ec181-124">Üretim sertifikaları ile Barındırma için aynı görüntüyü kullanmak zorlaştırır.</span><span class="sxs-lookup"><span data-stu-id="ec181-124">It makes it difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="ec181-125">Sertifika nın açıklanması nda önemli risk vardır.</span><span class="sxs-lookup"><span data-stu-id="ec181-125">There is significant risk of certificate disclosure.</span></span>

## <a name="starting-a-container-with-https-support-using-docker-compose"></a><span data-ttu-id="ec181-126">Docker oluşturmayı kullanarak https desteği ile konteyner başlatma</span><span class="sxs-lookup"><span data-stu-id="ec181-126">Starting a container with https support using docker compose</span></span>

<span data-ttu-id="ec181-127">İşletim sistemi yapılandırmanız için aşağıdaki yönergeleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="ec181-127">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="ec181-128">Linux kapsayıcılarını kullanan Windows</span><span class="sxs-lookup"><span data-stu-id="ec181-128">Windows using Linux containers</span></span>

<span data-ttu-id="ec181-129">Sertifika oluşturun ve yerel makineyi yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="ec181-129">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="ec181-130">Önceki komutlarda parolayla `{ password here }` değiştirin.</span><span class="sxs-lookup"><span data-stu-id="ec181-130">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="ec181-131">Aşağıdaki içeriği içeren bir _docker-compose.debug.yml_ dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="ec181-131">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
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
<span data-ttu-id="ec181-132">Docker oluşturma dosyasında belirtilen parola, sertifika için kullanılan parolayla eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="ec181-132">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="ec181-133">Kapsayıcıyı HTTPS için yapılandırılmış ASP.NET Core ile başlatın:</span><span class="sxs-lookup"><span data-stu-id="ec181-133">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a><span data-ttu-id="ec181-134">macOS veya Linux</span><span class="sxs-lookup"><span data-stu-id="ec181-134">macOS or Linux</span></span>

<span data-ttu-id="ec181-135">Sertifika oluşturun ve yerel makineyi yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="ec181-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="ec181-136">`dotnet dev-certs https --trust`yalnızca macOS ve Windows'da desteklenir.</span><span class="sxs-lookup"><span data-stu-id="ec181-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="ec181-137">Linux'taki sertifikalara dağıtımınız tarafından desteklenen şekilde güvenmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="ec181-137">You need to trust certificates on Linux in the way that is supported by your distro.</span></span> <span data-ttu-id="ec181-138">Tarayıcınızda sertifikaya güvenmeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="ec181-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="ec181-139">Önceki komutlarda parolayla `{ password here }` değiştirin.</span><span class="sxs-lookup"><span data-stu-id="ec181-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="ec181-140">Aşağıdaki içeriği içeren bir _docker-compose.debug.yml_ dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="ec181-140">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
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
<span data-ttu-id="ec181-141">Docker oluşturma dosyasında belirtilen parola, sertifika için kullanılan parolayla eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="ec181-141">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="ec181-142">Kapsayıcıyı HTTPS için yapılandırılmış ASP.NET Core ile başlatın:</span><span class="sxs-lookup"><span data-stu-id="ec181-142">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a><span data-ttu-id="ec181-143">Windows kapsayıcılarını kullanan Windows</span><span class="sxs-lookup"><span data-stu-id="ec181-143">Windows using Windows containers</span></span>

<span data-ttu-id="ec181-144">Sertifika oluşturun ve yerel makineyi yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="ec181-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="ec181-145">Önceki komutlarda parolayla `{ password here }` değiştirin.</span><span class="sxs-lookup"><span data-stu-id="ec181-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="ec181-146">Aşağıdaki içeriği içeren bir _docker-compose.debug.yml_ dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="ec181-146">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```json
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
<span data-ttu-id="ec181-147">Docker oluşturma dosyasında belirtilen parola, sertifika için kullanılan parolayla eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="ec181-147">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="ec181-148">Kapsayıcıyı HTTPS için yapılandırılmış ASP.NET Core ile başlatın:</span><span class="sxs-lookup"><span data-stu-id="ec181-148">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
