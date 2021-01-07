---
title: ASP.NET Core için Docker görüntüleri
author: rick-anderson
description: Docker kayıt defterinden yayınlanan ASP.NET Core Docker görüntülerini nasıl kullanacağınızı öğrenin. Kendi görüntülerinizi çekin ve oluşturun.
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2021
no-loc:
- appsettings.json
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
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 2cd21722082af88e536bc1001b606ee96e7cf59b
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972060"
---
# <a name="docker-images-for-aspnet-core"></a>ASP.NET Core için Docker görüntüleri

Bu öğreticide, Docker kapsayıcılarında ASP.NET Core uygulamasının nasıl çalıştırılacağı gösterilmektedir.

Bu öğreticide şunları yaptınız:
> [!div class="checklist"]
> * Docker görüntüleri ASP.NET Core hakkında bilgi edinin
> * ASP.NET Core örnek uygulaması indirin
> * Örnek uygulamayı yerel olarak çalıştırma
> * Linux kapsayıcılarında örnek uygulamayı çalıştırma
> * Örnek uygulamayı Windows kapsayıcılarında çalıştırma
> * El ile derleyin ve dağıtın

## <a name="aspnet-core-docker-images"></a>Docker görüntülerini ASP.NET Core

Bu öğreticide, ASP.NET Core örnek bir uygulama indirir ve Docker kapsayıcılarında çalıştırırsınız. Örnek, hem Linux hem de Windows kapsayıcılarıyla birlikte çalışmaktadır.

Örnek Dockerfile, farklı kapsayıcılarda derlemek ve çalıştırmak için [Docker Multi-Stage derleme özelliğini](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) kullanır. Derleme ve çalıştırma kapsayıcıları, Docker Hub 'ında Microsoft tarafından sunulan görüntülerden oluşturulur:

::: moniker range=">= aspnetcore-5.0"

* `dotnet/sdk`

  Örnek, uygulamayı oluşturmak için bu görüntüyü kullanır. Görüntü, komut satırı araçları 'nı (CLı) içeren .NET SDK 'sını içerir. Görüntü yerel geliştirme, hata ayıklama ve birim testi için iyileştirilmiştir. Geliştirme ve derleme için yüklenen araçlar görüntüyü görece büyük yapar.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/sdk`

  Örnek, uygulamayı oluşturmak için bu görüntüyü kullanır. Görüntü, komut satırı araçlarını (CLı) içeren .NET Core SDK içerir. Görüntü yerel geliştirme, hata ayıklama ve birim testi için iyileştirilmiştir. Geliştirme ve derleme için yüklenen araçlar görüntüyü görece büyük yapar.

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `dotnet/aspnet`

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/aspnet`

::: moniker-end

   Örnek, uygulamayı çalıştırmak için bu görüntüyü kullanır. Görüntü, ASP.NET Core çalışma zamanını ve kitaplıklarını içerir ve üretimde uygulamaları çalıştırmak için iyileştirilmiştir. Dağıtım ve uygulama başlatma hızı için tasarlanan görüntü görece küçüktür, bu nedenle Docker kayıt defterinden Docker ana bilgisayarına ağ performansı en iyi duruma getirilmiştir. Yalnızca bir uygulamayı çalıştırmak için gereken ikili dosyalar ve içerikler kapsayıcıya kopyalanır. İçerik çalıştırılmaya hazırlanmaya, en hızlı süreyi uygulama başlatmaya kadar etkinleştirir `docker run` . Docker modelinde dinamik kod derlemesi gerekli değildir.

## <a name="prerequisites"></a>Önkoşullar

::: moniker range=">= aspnetcore-5.0"

* [.NET SDK 5.0](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* [.NET Core SDK 3.1](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [.NET Core 2,2 SDK](https://dotnet.microsoft.com/download/dotnet-core)

::: moniker-end

* Docker Client 18,03 veya üzeri

  * Linux dağıtımları
    * [CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [Debian](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [macOS](https://docs.docker.com/docker-for-mac/install/)
  * [Windows](https://docs.docker.com/docker-for-windows/install/)

* [Git](https://git-scm.com/download)

## <a name="download-the-sample-app"></a>Örnek uygulamayı indirme

* [.Net Docker deposunu](https://github.com/dotnet/dotnet-docker)kopyalayarak örneği indirin: 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a>Uygulamayı yerel olarak çalıştırma

* *DotNet-Docker/Samples/aspnetapp/aspnetapp* konumundaki proje klasörüne gidin.

* Uygulamayı yerel olarak derlemek ve çalıştırmak için aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet run
  ```

* `http://localhost:5000`Uygulamayı test etmek için bir tarayıcıda adresine gidin.

* Uygulamayı durdurmak için komut isteminde CTRL + C tuşlarına basın.

## <a name="run-in-a-linux-container"></a>Linux kapsayıcısında çalıştırma

* Docker istemcisinde [Linux kapsayıcıları](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)' na geçin.

* *DotNet-Docker/Samples/aspnetapp* konumundaki Dockerfile klasörüne gidin.

* Örneği Docker 'da derlemek ve çalıştırmak için aşağıdaki komutları çalıştırın:

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  `build`Komut bağımsız değişkenleri:
  * Görüntüyü aspnetapp olarak adlandırın.
  * Geçerli klasörde Dockerfile dosyasını arayın (sonundaki süre).

  Run komutu bağımsız değişkenleri:
  * Bir sözde TTY ayırın ve ekli olmasa bile açık tutun. (İle aynı efekt `--interactive --tty` )
  * Kapsayıcıyı çıkış sırasında otomatik olarak kaldır.
  * Yerel makinedeki 5000 numaralı bağlantı noktasını kapsayıcıda 80 numaralı bağlantı noktasına eşleyin.
  * Kapsayıcının aspnetcore_sample adı.
  * Aspnetapp görüntüsünü belirtin.

* `http://localhost:5000`Uygulamayı test etmek için bir tarayıcıda adresine gidin.

## <a name="run-in-a-windows-container"></a>Bir Windows kapsayıcısında Çalıştır

* Docker istemcisinde [Windows kapsayıcıları](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)' na geçin.

Konumundaki Docker dosya klasörüne gidin `dotnet-docker/samples/aspnetapp` .

* Örneği Docker 'da derlemek ve çalıştırmak için aşağıdaki komutları çalıştırın:

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* Windows kapsayıcıları için kapsayıcının IP adresi gerekir (Bu işe göz atma `http://localhost:5000` çalışmayacak):
  * Başka bir komut istemi açın.
  * `docker ps`Çalışan kapsayıcıları görmek için ' i çalıştırın. "Aspnetcore_sample" kapsayıcısının orada olduğunu doğrulayın.
  * `docker exec aspnetcore_sample ipconfig`KAPSAYıCıNıN IP adresini göstermek için öğesini çalıştırın. Komutun çıktısı şu örneğe benzer şekilde görünür:

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* Kapsayıcı IPv4 adresini (örneğin, 172.29.245.43) kopyalayın ve uygulamayı test etmek için tarayıcı adres çubuğuna yapıştırın.

## <a name="build-and-deploy-manually"></a>El ile derleyin ve dağıtın

Bazı senaryolarda, çalışma zamanında gereken varlıkları kopyalayarak bir kapsayıcıya uygulama dağıtmak isteyebilirsiniz. Bu bölümde nasıl el ile dağıtım yapılacağı gösterilmektedir.

* *DotNet-Docker/Samples/aspnetapp/aspnetapp* konumundaki proje klasörüne gidin.

* [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutunu çalıştırın:

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  Komut bağımsız değişkenleri:
  * Uygulamayı yayın modunda (varsayılan olarak hata ayıklama modu) oluşturun.
  * *Yayımlanan* klasörde varlıklar oluşturun.

* Uygulamayı çalıştırın.

  * Windows:

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * Linux:

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* `http://localhost:5000`Giriş sayfasını görmek için öğesine gidin.

El ile yayınlanan uygulamayı bir Docker kapsayıcısı içinde kullanmak için yeni bir *Dockerfile* oluşturun ve `docker build .` kapsayıcıyı derlemek için komutunu kullanın.

::: moniker range=">= aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>Dockerfile

Daha önce çalıştırdığınız komut tarafından kullanılan *Dockerfile* aşağıda verilmiştir `docker build` .  `dotnet publish`Bu bölümde derlemek ve dağıtmak için kullandığınız şekilde aynı şekilde kullanılır.  

```dockerfile
# https://hub.docker.com/_/microsoft-dotnet
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /source

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /source/aspnetapp
RUN dotnet publish -c release -o /app --no-restore

# final stage/image
FROM mcr.microsoft.com/dotnet/aspnet:5.0
WORKDIR /app
COPY --from=build /app ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

Yukarıdaki *Dockerfile*'da, `*.csproj` dosyalar ayrı *Katmanlar* olarak kopyalanır ve geri yüklenir. `docker build`Komut bir görüntü oluşturduğunda, yerleşik bir önbellek kullanır. `*.csproj`Komutun son çalıştırılmasından bu yana dosyalar değiştirilmediyse `docker build` , `dotnet restore` komutun yeniden çalıştırılması gerekmez. Bunun yerine, karşılık gelen katman için yerleşik önbellek `dotnet restore` yeniden kullanılır. Daha fazla bilgi için bkz. [Dockerfiles yazmak Için en iyi uygulamalar](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>Dockerfile

Daha önce çalıştırdığınız komut tarafından kullanılan *Dockerfile* aşağıda verilmiştir `docker build` .  `dotnet publish`Bu bölümde derlemek ve dağıtmak için kullandığınız şekilde aynı şekilde kullanılır.  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

Önceki Dockerfile içinde belirtildiği gibi, `*.csproj` dosyalar ayrı *Katmanlar* olarak kopyalanır ve geri yüklenir. `docker build`Komut bir görüntü oluşturduğunda, yerleşik bir önbellek kullanır. `*.csproj`Komutun son çalıştırılmasından bu yana dosyalar değiştirilmediyse `docker build` , `dotnet restore` komutun yeniden çalıştırılması gerekmez. Bunun yerine, karşılık gelen katman için yerleşik önbellek `dotnet restore` yeniden kullanılır. Daha fazla bilgi için bkz. [Dockerfiles yazmak Için en iyi uygulamalar](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>Dockerfile

Daha önce çalıştırdığınız komut tarafından kullanılan *Dockerfile* aşağıda verilmiştir `docker build` . `dotnet publish`Bu bölümde derlemek ve dağıtmak için kullandığınız şekilde aynı şekilde kullanılır.  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* [Docker derleme komutu](https://docs.docker.com/engine/reference/commandline/build)
* [Docker Run komutu](https://docs.docker.com/engine/reference/commandline/run)
* [Docker örneğine ASP.NET Core](https://github.com/dotnet/dotnet-docker) (Bu öğreticide kullanılan bir tane).
* [Proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](../proxy-load-balancer.md)
* [Visual Studio Docker araçlarıyla çalışma](./visual-studio-tools-for-docker.md)
* [Visual Studio Code ile hata ayıklama](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [Docker ve küçük kapsayıcılar kullanılarak GC](xref:performance/memory#sc)

## <a name="next-steps"></a>Sonraki adımlar

Örnek uygulamayı içeren git deposu, belgeleri de içerir. Depodaki kullanılabilir kaynaklara genel bir bakış için [Readme dosyasına](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md)bakın. Özellikle, HTTPS 'yi uygulamayı öğrenin:

> [!div class="nextstepaction"]
> [HTTPS üzerinden Docker ile ASP.NET Core uygulamaları geliştirme](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)
