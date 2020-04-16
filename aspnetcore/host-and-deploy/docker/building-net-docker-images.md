---
title: ASP.NET Core için Docker görüntüleri
author: rick-anderson
description: Docker Registry'den yayınlanan .NET Core Docker görüntülerini nasıl kullanacağınızı öğrenin. Görüntüleri çekin ve kendi görüntüleri oluşturun.
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2020
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: ced0cb7cbeed1b8811813a70035c2e0b42c3e35a
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440785"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="34293-104">ASP.NET Core için Docker görüntüleri</span><span class="sxs-lookup"><span data-stu-id="34293-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="34293-105">Bu öğretici, Docker kaplarında bir ASP.NET Core uygulamasının nasıl çalıştırılabildiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="34293-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="34293-106">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="34293-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="34293-107">Microsoft .NET Core Docker görüntüleri hakkında bilgi edinin</span><span class="sxs-lookup"><span data-stu-id="34293-107">Learn about Microsoft .NET Core Docker images</span></span>
> * <span data-ttu-id="34293-108">ASP.NET Core örnek uygulamasını indirin</span><span class="sxs-lookup"><span data-stu-id="34293-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="34293-109">Örnek uygulamayı yerel olarak çalıştırma</span><span class="sxs-lookup"><span data-stu-id="34293-109">Run the sample app locally</span></span>
> * <span data-ttu-id="34293-110">Örnek uygulamayı Linux kaplarında çalıştırın</span><span class="sxs-lookup"><span data-stu-id="34293-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="34293-111">Örnek uygulamayı Windows kapsayıcılarında çalıştırma</span><span class="sxs-lookup"><span data-stu-id="34293-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="34293-112">El ile oluşturma ve dağıtma</span><span class="sxs-lookup"><span data-stu-id="34293-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="34293-113">ASP.NET Core Docker görüntüleri</span><span class="sxs-lookup"><span data-stu-id="34293-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="34293-114">Bu öğretici için, bir ASP.NET Core örnek uygulaması indirin ve Docker kaplarında çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="34293-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="34293-115">Örnek, hem Linux hem de Windows kapsayıcılarıyla çalışır.</span><span class="sxs-lookup"><span data-stu-id="34293-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="34293-116">Örnek Dockerfile farklı kapsayıcılarda oluşturmak ve çalıştırmak için [Docker çok aşamalı yapı özelliğini](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) kullanır.</span><span class="sxs-lookup"><span data-stu-id="34293-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="34293-117">Yapı ve çalıştır kapsayıcıları, Microsoft tarafından Docker Hub'da sağlanan resimlerden oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="34293-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

* `dotnet/core/sdk`

  <span data-ttu-id="34293-118">Örnek, uygulamayı oluşturmak için bu görüntüyü kullanır.</span><span class="sxs-lookup"><span data-stu-id="34293-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="34293-119">Görüntü, Komut Satırı Araçlarını (CLI) içeren .NET Core SDK'yı içerir.</span><span class="sxs-lookup"><span data-stu-id="34293-119">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="34293-120">Görüntü yerel geliştirme, hata ayıklama ve birim testi için optimize edilsin.</span><span class="sxs-lookup"><span data-stu-id="34293-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="34293-121">Geliştirme ve derleme için yüklenen araçlar bu nispeten büyük bir görüntü olun.</span><span class="sxs-lookup"><span data-stu-id="34293-121">The tools installed for development and compilation make this a relatively large image.</span></span> 

* `dotnet/core/aspnet`

   <span data-ttu-id="34293-122">Örnek, uygulamayı çalıştırmak için bu görüntüyü kullanır.</span><span class="sxs-lookup"><span data-stu-id="34293-122">The sample uses this image for running the app.</span></span> <span data-ttu-id="34293-123">Görüntü, Core çalışma zamanı ve kitaplıklar ASP.NET içerir ve üretimdeki uygulamaları çalıştırmak için optimize edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="34293-123">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="34293-124">Dağıtım ve uygulama başlatma hızı için tasarlanan görüntü nispeten küçüktür, bu nedenle Docker Registry'den Docker ana bilgisayara kadar ağ performansı optimize edilir.</span><span class="sxs-lookup"><span data-stu-id="34293-124">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="34293-125">Yalnızca bir uygulamayı çalıştırmak için gereken ikililer ve içerik kapsayıcıya kopyalanır.</span><span class="sxs-lookup"><span data-stu-id="34293-125">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="34293-126">İçerikler çalışmaya hazır dır ve uygulama nın `Docker run` başlatılmasından en hızlı şekilde başlatılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="34293-126">The contents are ready to run, enabling the fastest time from `Docker run` to app startup.</span></span> <span data-ttu-id="34293-127">Docker modelinde dinamik kod derlemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="34293-127">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="34293-128">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="34293-128">Prerequisites</span></span>
::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="34293-129">.NET Çekirdek 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="34293-129">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="34293-130">.NET Çekirdek SDK 3.0</span><span class="sxs-lookup"><span data-stu-id="34293-130">.NET Core SDK 3.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

* <span data-ttu-id="34293-131">Docker istemcisi 18.03 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="34293-131">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="34293-132">Linux dağıtımları</span><span class="sxs-lookup"><span data-stu-id="34293-132">Linux distributions</span></span>
    * [<span data-ttu-id="34293-133">CentOS</span><span class="sxs-lookup"><span data-stu-id="34293-133">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="34293-134">Debian</span><span class="sxs-lookup"><span data-stu-id="34293-134">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="34293-135">Fedora</span><span class="sxs-lookup"><span data-stu-id="34293-135">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="34293-136">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="34293-136">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="34293-137">macOS</span><span class="sxs-lookup"><span data-stu-id="34293-137">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="34293-138">Windows</span><span class="sxs-lookup"><span data-stu-id="34293-138">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="34293-139">Git</span><span class="sxs-lookup"><span data-stu-id="34293-139">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="34293-140">Örnek uygulamayı indirme</span><span class="sxs-lookup"><span data-stu-id="34293-140">Download the sample app</span></span>

* <span data-ttu-id="34293-141">[.NET Core Docker deposunu](https://github.com/dotnet/dotnet-docker)klonlayarak örneği indirin:</span><span class="sxs-lookup"><span data-stu-id="34293-141">Download the sample by cloning the [.NET Core Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="34293-142">Uygulamayı yerel olarak çalıştırma</span><span class="sxs-lookup"><span data-stu-id="34293-142">Run the app locally</span></span>

* <span data-ttu-id="34293-143">*Dotnet-docker/samples/aspnetapp/aspnetapp*adresindeki proje klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="34293-143">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="34293-144">Uygulamayı yerel olarak oluşturmak ve çalıştırmak için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="34293-144">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="34293-145">Uygulamayı `http://localhost:5000` test etmek için tarayıcıya gidin.</span><span class="sxs-lookup"><span data-stu-id="34293-145">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="34293-146">Uygulamayı durdurmak için komut isteminde Ctrl+C tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="34293-146">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="34293-147">Linux konteynırında çalıştırın</span><span class="sxs-lookup"><span data-stu-id="34293-147">Run in a Linux container</span></span>

* <span data-ttu-id="34293-148">Docker istemcisinde, Linux kaplarına geçin.</span><span class="sxs-lookup"><span data-stu-id="34293-148">In the Docker client, switch to Linux containers.</span></span>

* <span data-ttu-id="34293-149">*Dotnet-docker/samples/aspnetapp*adresindeki Dockerfile klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="34293-149">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="34293-150">Örneği Docker'da oluşturmak ve çalıştırmak için aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="34293-150">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="34293-151">`build` Komut bağımsız değişkenleri:</span><span class="sxs-lookup"><span data-stu-id="34293-151">The `build` command arguments:</span></span>
  * <span data-ttu-id="34293-152">Resmi aspnetapp olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="34293-152">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="34293-153">Geçerli klasördeki Dockerfile'ı (sondaki dönem) arayın.</span><span class="sxs-lookup"><span data-stu-id="34293-153">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="34293-154">Run komut uyşu argümanlari:</span><span class="sxs-lookup"><span data-stu-id="34293-154">The run command arguments:</span></span>
  * <span data-ttu-id="34293-155">Sözde TTY tahsis edin ve bağlı olmasa bile açık tutun.</span><span class="sxs-lookup"><span data-stu-id="34293-155">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="34293-156">(Aynı etki `--interactive --tty`.)</span><span class="sxs-lookup"><span data-stu-id="34293-156">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="34293-157">Dışarı çıktığında kapsayıcıyı otomatik olarak çıkarın.</span><span class="sxs-lookup"><span data-stu-id="34293-157">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="34293-158">Yerel makinedeki 5000 portu konteynerdeki 80 bağlantı noktasına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="34293-158">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="34293-159">Konteynerin adını aspnetcore_sample.</span><span class="sxs-lookup"><span data-stu-id="34293-159">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="34293-160">Aspnetapp görüntüsünü belirtin.</span><span class="sxs-lookup"><span data-stu-id="34293-160">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="34293-161">Uygulamayı `http://localhost:5000` test etmek için tarayıcıya gidin.</span><span class="sxs-lookup"><span data-stu-id="34293-161">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="34293-162">Windows kapsayıcısında çalıştırın</span><span class="sxs-lookup"><span data-stu-id="34293-162">Run in a Windows container</span></span>

* <span data-ttu-id="34293-163">Docker istemcisinde, Windows kapsayıcılarına geçin.</span><span class="sxs-lookup"><span data-stu-id="34293-163">In the Docker client, switch to Windows containers.</span></span>

<span data-ttu-id="34293-164">`dotnet-docker/samples/aspnetapp`Docker dosya klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="34293-164">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="34293-165">Örneği Docker'da oluşturmak ve çalıştırmak için aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="34293-165">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="34293-166">Windows kapsayıcıları için kapsayıcının IP adresine ihtiyacınız `http://localhost:5000` vardır (gezinmeişe yaramaz):</span><span class="sxs-lookup"><span data-stu-id="34293-166">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="34293-167">Başka bir komut istemi açın.</span><span class="sxs-lookup"><span data-stu-id="34293-167">Open up another command prompt.</span></span>
  * <span data-ttu-id="34293-168">Çalışan `docker ps` kapları görmek için çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="34293-168">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="34293-169">"aspnetcore_sample" kapsayıcısının orada olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="34293-169">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="34293-170">Kapsayıcının IP adresini görüntülemek için çalıştırın. `docker exec aspnetcore_sample ipconfig`</span><span class="sxs-lookup"><span data-stu-id="34293-170">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="34293-171">Komutun çıktısı aşağıdaki örneğe benzer:</span><span class="sxs-lookup"><span data-stu-id="34293-171">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="34293-172">Kapsayıcı IPv4 adresini kopyalayın (örneğin, 172.29.245.43) ve uygulamayı test etmek için tarayıcı adresi çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="34293-172">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="34293-173">El ile oluşturma ve dağıtma</span><span class="sxs-lookup"><span data-stu-id="34293-173">Build and deploy manually</span></span>

<span data-ttu-id="34293-174">Bazı senaryolarda, bir uygulamayı çalışma zamanında gereken uygulama dosyalarını kopyalayarak kapsayıcıya dağıtmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="34293-174">In some scenarios, you might want to deploy an app to a container by copying to it the application files that are needed at run time.</span></span> <span data-ttu-id="34293-175">Bu bölümde el ile nasıl dağıtılanın gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="34293-175">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="34293-176">*Dotnet-docker/samples/aspnetapp/aspnetapp*adresindeki proje klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="34293-176">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="34293-177">[dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="34293-177">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="34293-178">Komut bağımsız değişkenleri:</span><span class="sxs-lookup"><span data-stu-id="34293-178">The command arguments:</span></span>
  * <span data-ttu-id="34293-179">Uygulamayı sürüm modunda oluşturun (varsayılan hata ayıklama modudur).</span><span class="sxs-lookup"><span data-stu-id="34293-179">Build the application in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="34293-180">*Yayımlanmış* klasördeki dosyaları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="34293-180">Create the files in the *published* folder.</span></span>

* <span data-ttu-id="34293-181">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="34293-181">Run the application.</span></span>

  * <span data-ttu-id="34293-182">Windows:</span><span class="sxs-lookup"><span data-stu-id="34293-182">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="34293-183">Linux:</span><span class="sxs-lookup"><span data-stu-id="34293-183">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="34293-184">Ana sayfayı görmek için `http://localhost:5000` göz atın.</span><span class="sxs-lookup"><span data-stu-id="34293-184">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="34293-185">Docker kapsayıcısı içinde el ile yayınlanan uygulamayı kullanmak için yeni `docker build .` bir Dockerfile oluşturun ve kapsayıcıyı oluşturmak için komutu kullanın.</span><span class="sxs-lookup"><span data-stu-id="34293-185">To use the manually published application within a Docker container, create a new Dockerfile and use the `docker build .` command to build the container.</span></span>

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="34293-186">Dockerdosyası</span><span class="sxs-lookup"><span data-stu-id="34293-186">The Dockerfile</span></span>

<span data-ttu-id="34293-187">İşte daha önce çalıştırdığınız `docker build` komuttarafından kullanılan *Dockerdosyası.*</span><span class="sxs-lookup"><span data-stu-id="34293-187">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="34293-188">Oluşturmak `dotnet publish` ve dağıtmak için bu bölümde yaptığınız gibi kullanır.</span><span class="sxs-lookup"><span data-stu-id="34293-188">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

### <a name="the-dockerfile"></a><span data-ttu-id="34293-189">Dockerdosyası</span><span class="sxs-lookup"><span data-stu-id="34293-189">The Dockerfile</span></span>

<span data-ttu-id="34293-190">İşte daha önce çalıştırdığınız `docker build` komuttarafından kullanılan *Dockerdosyası.*</span><span class="sxs-lookup"><span data-stu-id="34293-190">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="34293-191">Oluşturmak `dotnet publish` ve dağıtmak için bu bölümde yaptığınız gibi kullanır.</span><span class="sxs-lookup"><span data-stu-id="34293-191">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

## <a name="additional-resources"></a><span data-ttu-id="34293-192">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="34293-192">Additional resources</span></span>

* [<span data-ttu-id="34293-193">Docker yapı komutu</span><span class="sxs-lookup"><span data-stu-id="34293-193">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="34293-194">Docker çalıştır komutu</span><span class="sxs-lookup"><span data-stu-id="34293-194">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="34293-195">[ASP.NET Core Docker örneği](https://github.com/dotnet/dotnet-docker) (Bu eğitimde kullanılan.)</span><span class="sxs-lookup"><span data-stu-id="34293-195">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="34293-196">Proxy sunucuları ve yük dengeleyicileri ile çalışmak için ASP.NET Core yapılandırma</span><span class="sxs-lookup"><span data-stu-id="34293-196">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](/aspnet/core/host-and-deploy/proxy-load-balancer)
* [<span data-ttu-id="34293-197">Visual Studio Docker Tools ile çalışma</span><span class="sxs-lookup"><span data-stu-id="34293-197">Working with Visual Studio Docker Tools</span></span>](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker)
* [<span data-ttu-id="34293-198">Visual Studio Code ile Hata Ayıklama</span><span class="sxs-lookup"><span data-stu-id="34293-198">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="34293-199">Docker ve küçük kaplar kullanarak GC</span><span class="sxs-lookup"><span data-stu-id="34293-199">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="34293-200">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="34293-200">Next steps</span></span>

<span data-ttu-id="34293-201">Örnek uygulamayı içeren Git deposu da belgeler içerir.</span><span class="sxs-lookup"><span data-stu-id="34293-201">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="34293-202">Depoda bulunan kaynaklara genel bakış için [README dosyasına](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="34293-202">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="34293-203">Özellikle, HTTPS'yi nasıl uygulayacağınızı öğrenin:</span><span class="sxs-lookup"><span data-stu-id="34293-203">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="34293-204">HTTPS üzerinden Docker ile ASP.NET Temel Uygulamalar Geliştirme</span><span class="sxs-lookup"><span data-stu-id="34293-204">Developing ASP.NET Core Applications with Docker over HTTPS</span></span>](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)
