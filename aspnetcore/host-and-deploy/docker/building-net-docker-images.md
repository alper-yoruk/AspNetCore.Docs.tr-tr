---
title: ASP.NET Core için Docker görüntüleri
author: rick-anderson
description: Docker Registry'den yayınlanan .NET Core Docker görüntülerini nasıl kullanacağınızı öğrenin. Görüntüleri çekin ve kendi görüntüleri oluşturun.
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2020
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 31161d6841986cb0bd8080468e5d523d59400490
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80405930"
---
# <a name="docker-images-for-aspnet-core"></a>ASP.NET Core için Docker görüntüleri

Bu öğretici, Docker kaplarında bir ASP.NET Core uygulamasının nasıl çalıştırılabildiğini gösterir.

Bu öğreticide şunları yaptınız:
> [!div class="checklist"]
> * Microsoft .NET Core Docker görüntüleri hakkında bilgi edinin
> * ASP.NET Core örnek uygulamasını indirin
> * Örnek uygulamayı yerel olarak çalıştırma
> * Örnek uygulamayı Linux kaplarında çalıştırın
> * Örnek uygulamayı Windows kapsayıcılarında çalıştırma
> * El ile oluşturma ve dağıtma

## <a name="aspnet-core-docker-images"></a>ASP.NET Core Docker görüntüleri

Bu öğretici için, bir ASP.NET Core örnek uygulaması indirin ve Docker kaplarında çalıştırın. Örnek, hem Linux hem de Windows kapsayıcılarıyla çalışır.

Örnek Dockerfile farklı kapsayıcılarda oluşturmak ve çalıştırmak için [Docker çok aşamalı yapı özelliğini](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) kullanır. Yapı ve çalıştır kapsayıcıları, Microsoft tarafından Docker Hub'da sağlanan resimlerden oluşturulur:

* `dotnet/core/sdk`

  Örnek, uygulamayı oluşturmak için bu görüntüyü kullanır. Görüntü, Komut Satırı Araçlarını (CLI) içeren .NET Core SDK'yı içerir. Görüntü yerel geliştirme, hata ayıklama ve birim testi için optimize edilsin. Geliştirme ve derleme için yüklenen araçlar bu nispeten büyük bir görüntü olun. 

* `dotnet/core/aspnet`

   Örnek, uygulamayı çalıştırmak için bu görüntüyü kullanır. Görüntü, Core çalışma zamanı ve kitaplıklar ASP.NET içerir ve üretimdeki uygulamaları çalıştırmak için optimize edilmiştir. Dağıtım ve uygulama başlatma hızı için tasarlanan görüntü nispeten küçüktür, bu nedenle Docker Registry'den Docker ana bilgisayara kadar ağ performansı optimize edilir. Yalnızca bir uygulamayı çalıştırmak için gereken ikililer ve içerik kapsayıcıya kopyalanır. İçerikler çalışmaya hazır dır ve uygulama nın `Docker run` başlatılmasından en hızlı şekilde başlatılmasını sağlar. Docker modelinde dinamik kod derlemesi gerekmez.

## <a name="prerequisites"></a>Ön koşullar
::: moniker range="< aspnetcore-3.0"

* [.NET Çekirdek 2.2 SDK](https://dotnet.microsoft.com/download/dotnet-core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [.NET Çekirdek SDK 3.0](https://dotnet.microsoft.com/download)

::: moniker-end

* Docker istemcisi 18.03 veya sonrası

  * Linux dağıtımları
    * [CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [Debian](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [macOS](https://docs.docker.com/docker-for-mac/install/)
  * [Windows](https://docs.docker.com/docker-for-windows/install/)

* [Git](https://git-scm.com/download)

## <a name="download-the-sample-app"></a>Örnek uygulamayı indirme

* [.NET Core Docker deposunu](https://github.com/dotnet/dotnet-docker)klonlayarak örneği indirin: 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a>Uygulamayı yerel olarak çalıştırma

* *Dotnet-docker/samples/aspnetapp/aspnetapp*adresindeki proje klasörüne gidin.

* Uygulamayı yerel olarak oluşturmak ve çalıştırmak için aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet run
  ```

* Uygulamayı `http://localhost:5000` test etmek için tarayıcıya gidin.

* Uygulamayı durdurmak için komut isteminde Ctrl+C tuşuna basın.

## <a name="run-in-a-linux-container"></a>Linux konteynırında çalıştırın

* Docker istemcisinde, Linux kaplarına geçin.

* *Dotnet-docker/samples/aspnetapp*adresindeki Dockerfile klasörüne gidin.

* Örneği Docker'da oluşturmak ve çalıştırmak için aşağıdaki komutları çalıştırın:

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  `build` Komut bağımsız değişkenleri:
  * Resmi aspnetapp olarak adlandırın.
  * Geçerli klasördeki Dockerfile'ı (sondaki dönem) arayın.

  Run komut uyşu argümanlari:
  * Sözde TTY tahsis edin ve bağlı olmasa bile açık tutun. (Aynı etki `--interactive --tty`.)
  * Dışarı çıktığında kapsayıcıyı otomatik olarak çıkarın.
  * Yerel makinedeki 5000 portu konteynerdeki 80 bağlantı noktasına yerleştirin.
  * Konteynerin adını aspnetcore_sample.
  * Aspnetapp görüntüsünü belirtin.

* Uygulamayı `http://localhost:5000` test etmek için tarayıcıya gidin.

## <a name="run-in-a-windows-container"></a>Windows kapsayıcısında çalıştırın

* Docker istemcisinde, Windows kapsayıcılarına geçin.

`dotnet-docker/samples/aspnetapp`Docker dosya klasörüne gidin.

* Örneği Docker'da oluşturmak ve çalıştırmak için aşağıdaki komutları çalıştırın:

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* Windows kapsayıcıları için kapsayıcının IP adresine ihtiyacınız `http://localhost:5000` vardır (gezinmeişe yaramaz):
  * Başka bir komut istemi açın.
  * Çalışan `docker ps` kapları görmek için çalıştırın. "aspnetcore_sample" kapsayıcısının orada olduğunu doğrulayın.
  * Kapsayıcının IP adresini görüntülemek için çalıştırın. `docker exec aspnetcore_sample ipconfig` Komutun çıktısı aşağıdaki örneğe benzer:

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* Kapsayıcı IPv4 adresini kopyalayın (örneğin, 172.29.245.43) ve uygulamayı test etmek için tarayıcı adresi çubuğuna yapıştırın.

## <a name="build-and-deploy-manually"></a>El ile oluşturma ve dağıtma

Bazı senaryolarda, bir uygulamayı çalışma zamanında gereken uygulama dosyalarını kopyalayarak kapsayıcıya dağıtmak isteyebilirsiniz. Bu bölümde el ile nasıl dağıtılanın gösterilmektedir.

* *Dotnet-docker/samples/aspnetapp/aspnetapp*adresindeki proje klasörüne gidin.

* [dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutunu çalıştırın:

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  Komut bağımsız değişkenleri:
  * Uygulamayı sürüm modunda oluşturun (varsayılan hata ayıklama modudur).
  * *Yayımlanmış* klasördeki dosyaları oluşturun.

* Uygulamayı çalıştırın.

  * Windows:

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * Linux:

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* Ana sayfayı görmek için `http://localhost:5000` göz atın.

Docker kapsayıcısı içinde el ile yayınlanan uygulamayı kullanmak için yeni `docker build .` bir Dockerfile oluşturun ve kapsayıcıyı oluşturmak için komutu kullanın.

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>Dockerdosyası

İşte daha önce çalıştırdığınız `docker build` komuttarafından kullanılan *Dockerdosyası.*  Oluşturmak `dotnet publish` ve dağıtmak için bu bölümde yaptığınız gibi kullanır.  

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

::: moniker range=">= aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>Dockerdosyası

İşte daha önce çalıştırdığınız `docker build` komuttarafından kullanılan *Dockerdosyası.*  Oluşturmak `dotnet publish` ve dağıtmak için bu bölümde yaptığınız gibi kullanır.  

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

::: moniker-end

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

## <a name="additional-resources"></a>Ek kaynaklar

* [Docker yapı komutu](https://docs.docker.com/engine/reference/commandline/build)
* [Docker çalıştır komutu](https://docs.docker.com/engine/reference/commandline/run)
* [ASP.NET Core Docker örneği](https://github.com/dotnet/dotnet-docker) (Bu eğitimde kullanılan.)
* [Proxy sunucuları ve yük dengeleyicileri ile çalışmak için ASP.NET Core yapılandırma](/aspnet/core/host-and-deploy/proxy-load-balancer)
* [Visual Studio Docker Tools ile çalışma](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker)
* [Visual Studio Code ile Hata Ayıklama](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers) 

## <a name="next-steps"></a>Sonraki adımlar

Örnek uygulamayı içeren Git deposu da belgeler içerir. Depoda bulunan kaynaklara genel bakış için [README dosyasına](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md)bakın. Özellikle, HTTPS'yi nasıl uygulayacağınızı öğrenin:

> [!div class="nextstepaction"]
> [HTTPS üzerinden Docker ile ASP.NET Temel Uygulamalar Geliştirme](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)
