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
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a>HTTPS üzerinden Docker ile görüntüleri barındırma ASP.NET Core

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core [Varsayılan olarak https](/aspnet/core/security/enforcing-ssl)kullanır. [Https](https://en.wikipedia.org/wiki/HTTPS) , güven, kimlik ve şifreleme için [sertifikalara](https://en.wikipedia.org/wiki/Public_key_certificate) bağımlıdır.

Bu belgede önceden oluşturulmuş kapsayıcı görüntülerinin HTTPS ile nasıl çalıştırılacağı açıklanmaktadır.

Geliştirme senaryoları için bkz. [https üzerinden Docker ile ASP.NET Core uygulamaları geliştirme](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) .

Bu örnek, [Docker Istemcisinin](https://www.docker.com/products/docker) [Docker 17,06](https://docs.docker.com/release-notes/docker-ce) veya sonraki bir sürümünü gerektirir.

## <a name="prerequisites"></a>Ön koşullar

Bu belgedeki bazı yönergeler için [.NET Core 2,2 SDK](https://dotnet.microsoft.com/download) veya üzeri gereklidir.

## <a name="certificates"></a>Sertifikalar

Bir etki alanı için [Üretim barındırma](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) için bir [sertifika yetkilisinden](https://wikipedia.org/wiki/Certificate_authority) bir sertifika gereklidir. [Let's Encrypt](https://letsencrypt.org/), ücretsiz sertifikalar sunan bir sertifika yetkilisindir.

Bu belge, üzerine önceden oluşturulmuş görüntüleri barındırmak için [otomatik olarak imzalanan geliştirme sertifikaları](https://en.wikipedia.org/wiki/Self-signed_certificate) kullanır `localhost` . Yönergeler, üretim sertifikalarını kullanmaya benzerdir.

Üretim sertifikaları için:

* `dotnet dev-certs`Araç gerekli değildir.
* Sertifikaların, yönergelerde kullanılan konumda depolanması gerekmez. Herhangi bir konumun çalışması gerekir, ancak bu sertifikalar site dizininizde depolanarak önerilmez.

Aşağıdaki bölümde yer alan yönergeler, Docker 'ın komut satırı seçeneğini kullanarak sertifikaları kapsayıcılara bağlama `-v` . `COPY` *Dockerfile*dosyasında bir komutla kapsayıcı görüntülerine sertifika ekleyebilirsiniz, ancak bunu yapmanız önerilmez. Sertifikaları bir görüntüye kopyalamak aşağıdaki nedenlerden dolayı önerilmez:

* Geliştirici sertifikaları ile test etmek için aynı görüntünün kullanımını zorlaştırır.
* Üretim sertifikaları ile barındırmak için aynı görüntünün kullanımını zorlaştırır.
* Sertifika açıklanmasının önemli bir riski vardır.

## <a name="running-pre-built-container-images-with-https"></a>HTTPS ile önceden oluşturulmuş kapsayıcı görüntülerini çalıştırma

İşletim sistemi yapılandırmanız için aşağıdaki yönergeleri kullanın.

### <a name="windows-using-linux-containers"></a>Linux kapsayıcıları kullanan Windows

Sertifika Oluştur ve yerel makineyi Yapılandır:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Yukarıdaki komutlarda, öğesini parolayla değiştirin `{ password here }` .

Bir komut kabuğu 'nda HTTPS için yapılandırılan ASP.NET Core kapsayıcı görüntüsünü çalıştırın:

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

[PowerShell](/powershell/scripting/overview)kullanırken `%USERPROFILE%` ile değiştirin `$env:USERPROFILE` .

Parolanın, sertifika için kullanılan parolayla eşleşmesi gerekir.

### <a name="macos-or-linux"></a>macOS veya Linux

Sertifika Oluştur ve yerel makineyi Yapılandır:

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust`yalnızca macOS ve Windows 'da desteklenir. Linux 'ta sertifikalarınızın desteklediği şekilde sertifika almanız gerekir. Büyük olasılıkla, tarayıcınızda sertifikaya güvenmeniz gerekir.

Yukarıdaki komutlarda, öğesini parolayla değiştirin `{ password here }` .

Kapsayıcı görüntüsünü HTTPS için yapılandırılan ASP.NET Core çalıştırın:

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

Parolanın, sertifika için kullanılan parolayla eşleşmesi gerekir.

### <a name="windows-using-windows-containers"></a>Windows kapsayıcıları kullanan pencereler

Sertifika Oluştur ve yerel makineyi Yapılandır:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Yukarıdaki komutlarda, öğesini parolayla değiştirin `{ password here }` . [PowerShell](/powershell/scripting/overview)kullanırken `%USERPROFILE%` ile değiştirin `$env:USERPROFILE` .

Kapsayıcı görüntüsünü HTTPS için yapılandırılan ASP.NET Core çalıştırın:

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

Parolanın, sertifika için kullanılan parolayla eşleşmesi gerekir. [PowerShell](/powershell/scripting/overview)kullanırken `%USERPROFILE%` ile değiştirin `$env:USERPROFILE` .
