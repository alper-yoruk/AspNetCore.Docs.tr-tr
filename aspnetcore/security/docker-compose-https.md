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
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a>Docker Compose ile ASP.NET Core görüntülerini HTTPS üzerinden barındırma


ASP.NET Core [varsayılan olarak HTTPS](/aspnet/core/security/enforcing-ssl)kullanır. [HTTPS](https://en.wikipedia.org/wiki/HTTPS) güven, kimlik ve şifreleme [sertifikalarına](https://en.wikipedia.org/wiki/Public_key_certificate) dayanır.

Bu belge, önceden oluşturulmuş kapsayıcı görüntülerinin HTTPS ile nasıl çalıştırılabildiğini açıklar.

Geliştirme [senaryoları](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) için HTTPS üzerinden Docker ile ASP.NET Temel Uygulamalar Geliştirme bölümüne bakın.

Bu örnek [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) veya daha sonra [Docker istemcisi](https://www.docker.com/products/docker)gerektirir.

## <a name="prerequisites"></a>Ön koşullar

[.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) veya daha sonra bu belgedeki bazı talimatlar için gereklidir.

## <a name="certificates"></a>Sertifikalar

Bir etki alanı için [üretim barındırma](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) için [sertifika yetkilisinden](https://wikipedia.org/wiki/Certificate_authority) bir sertifika gereklidir. [Let's Encrypt](https://letsencrypt.org/)ücretsiz sertifika lar sunan bir sertifika yetkilisidir.

Bu belge, önceden oluşturulmuş görüntüleri barındırmak `localhost`için kendi imzalı geliştirme [sertifikalarını](https://wikipedia.org/wiki/Self-signed_certificate) kullanır. Yönergeler üretim sertifikaları kullanmaya benzer.

Üretim sertifikaları için:

* Araç `dotnet dev-certs` gerekli değildir.
* Sertifikaların yönergelerde kullanılan konumda depolanmış olması gerekmez. Sertifikaları site dizininin dışındaki herhangi bir yerde saklayın.

Aşağıdaki bölümde yer alan talimatlar `volumes` *docker-compose.yml* özelliğini kullanarak konteyneriçine montaj sertifikaları. `COPY` *Dockerfile'da*komutu olan kapsayıcı görüntülere sertifika ekleyebilirsiniz, ancak bu önerilmez. Sertifikaların görüntüye kopyalanması aşağıdaki nedenlerden dolayı önerilmez:

* Geliştirici sertifikalarıyla test etmek için aynı görüntüyü kullanmayı zorlaştırır.
* Üretim sertifikaları ile Barındırma için aynı görüntüyü kullanmak zorlaştırır.
* Sertifika nın açıklanması nda önemli risk vardır.

## <a name="starting-a-container-with-https-support-using-docker-compose"></a>Docker oluşturmayı kullanarak https desteği ile konteyner başlatma

İşletim sistemi yapılandırmanız için aşağıdaki yönergeleri kullanın.

### <a name="windows-using-linux-containers"></a>Linux kapsayıcılarını kullanan Windows

Sertifika oluşturun ve yerel makineyi yapılandırın:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Önceki komutlarda parolayla `{ password here }` değiştirin.

Aşağıdaki içeriği içeren bir _docker-compose.debug.yml_ dosyası oluşturun:

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
Docker oluşturma dosyasında belirtilen parola, sertifika için kullanılan parolayla eşleşmelidir.

Kapsayıcıyı HTTPS için yapılandırılmış ASP.NET Core ile başlatın:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a>macOS veya Linux

Sertifika oluşturun ve yerel makineyi yapılandırın:

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust`yalnızca macOS ve Windows'da desteklenir. Linux'taki sertifikalara dağıtımınız tarafından desteklenen şekilde güvenmeniz gerekir. Tarayıcınızda sertifikaya güvenmeniz gerekebilir.

Önceki komutlarda parolayla `{ password here }` değiştirin.

Aşağıdaki içeriği içeren bir _docker-compose.debug.yml_ dosyası oluşturun:

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
Docker oluşturma dosyasında belirtilen parola, sertifika için kullanılan parolayla eşleşmelidir.

Kapsayıcıyı HTTPS için yapılandırılmış ASP.NET Core ile başlatın:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a>Windows kapsayıcılarını kullanan Windows

Sertifika oluşturun ve yerel makineyi yapılandırın:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Önceki komutlarda parolayla `{ password here }` değiştirin.

Aşağıdaki içeriği içeren bir _docker-compose.debug.yml_ dosyası oluşturun:

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
Docker oluşturma dosyasında belirtilen parola, sertifika için kullanılan parolayla eşleşmelidir.

Kapsayıcıyı HTTPS için yapılandırılmış ASP.NET Core ile başlatın:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
