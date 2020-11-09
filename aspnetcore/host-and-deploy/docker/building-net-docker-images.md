---
title: ASP.NET Core için Docker görüntüleri
author: rick-anderson
description: Docker kayıt defterinden yayınlanan .NET Core Docker görüntülerini nasıl kullanacağınızı öğrenin. Görüntüleri çekin ve kendi görüntülerinizi oluşturun.
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 81daa1d4a996519f44e513b4f61c27cdf2b6ef5e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059812"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="05a5e-104">ASP.NET Core için Docker görüntüleri</span><span class="sxs-lookup"><span data-stu-id="05a5e-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="05a5e-105">Bu öğreticide, Docker kapsayıcılarında ASP.NET Core uygulamasının nasıl çalıştırılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="05a5e-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="05a5e-106">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="05a5e-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="05a5e-107">Microsoft .NET Core Docker görüntüleri hakkında bilgi edinin</span><span class="sxs-lookup"><span data-stu-id="05a5e-107">Learn about Microsoft .NET Core Docker images</span></span>
> * <span data-ttu-id="05a5e-108">ASP.NET Core örnek uygulaması indirin</span><span class="sxs-lookup"><span data-stu-id="05a5e-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="05a5e-109">Örnek uygulamayı yerel olarak çalıştırma</span><span class="sxs-lookup"><span data-stu-id="05a5e-109">Run the sample app locally</span></span>
> * <span data-ttu-id="05a5e-110">Linux kapsayıcılarında örnek uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="05a5e-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="05a5e-111">Örnek uygulamayı Windows kapsayıcılarında çalıştırma</span><span class="sxs-lookup"><span data-stu-id="05a5e-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="05a5e-112">El ile derleyin ve dağıtın</span><span class="sxs-lookup"><span data-stu-id="05a5e-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="05a5e-113">Docker görüntülerini ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="05a5e-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="05a5e-114">Bu öğreticide, ASP.NET Core örnek bir uygulama indirir ve Docker kapsayıcılarında çalıştırırsınız.</span><span class="sxs-lookup"><span data-stu-id="05a5e-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="05a5e-115">Örnek, hem Linux hem de Windows kapsayıcılarıyla birlikte çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="05a5e-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="05a5e-116">Örnek Dockerfile, farklı kapsayıcılarda derlemek ve çalıştırmak için [Docker Multi-Stage derleme özelliğini](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) kullanır.</span><span class="sxs-lookup"><span data-stu-id="05a5e-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="05a5e-117">Derleme ve çalıştırma kapsayıcıları, Docker Hub 'ında Microsoft tarafından sunulan görüntülerden oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="05a5e-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

* `dotnet/core/sdk`

  <span data-ttu-id="05a5e-118">Örnek, uygulamayı oluşturmak için bu görüntüyü kullanır.</span><span class="sxs-lookup"><span data-stu-id="05a5e-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="05a5e-119">Görüntü, komut satırı araçlarını (CLı) içeren .NET Core SDK içerir.</span><span class="sxs-lookup"><span data-stu-id="05a5e-119">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="05a5e-120">Görüntü yerel geliştirme, hata ayıklama ve birim testi için iyileştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="05a5e-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="05a5e-121">Geliştirme ve derleme için yüklenen araçlar bunu görece büyük bir görüntü haline getirir.</span><span class="sxs-lookup"><span data-stu-id="05a5e-121">The tools installed for development and compilation make this a relatively large image.</span></span> 

* `dotnet/core/aspnet`

   <span data-ttu-id="05a5e-122">Örnek, uygulamayı çalıştırmak için bu görüntüyü kullanır.</span><span class="sxs-lookup"><span data-stu-id="05a5e-122">The sample uses this image for running the app.</span></span> <span data-ttu-id="05a5e-123">Görüntü, ASP.NET Core çalışma zamanını ve kitaplıklarını içerir ve üretimde uygulamaları çalıştırmak için iyileştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="05a5e-123">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="05a5e-124">Dağıtım ve uygulama başlatma hızı için tasarlanan görüntü görece küçüktür, bu nedenle Docker kayıt defterinden Docker ana bilgisayarına ağ performansı en iyi duruma getirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="05a5e-124">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="05a5e-125">Yalnızca bir uygulamayı çalıştırmak için gereken ikili dosyalar ve içerikler kapsayıcıya kopyalanır.</span><span class="sxs-lookup"><span data-stu-id="05a5e-125">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="05a5e-126">İçerik çalıştırılmaya hazırlanmaya, en hızlı süreyi uygulama başlatmaya kadar etkinleştirir `Docker run` .</span><span class="sxs-lookup"><span data-stu-id="05a5e-126">The contents are ready to run, enabling the fastest time from `Docker run` to app startup.</span></span> <span data-ttu-id="05a5e-127">Docker modelinde dinamik kod derlemesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="05a5e-127">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="05a5e-128">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="05a5e-128">Prerequisites</span></span>
::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="05a5e-129">.NET Core 2,2 SDK</span><span class="sxs-lookup"><span data-stu-id="05a5e-129">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="05a5e-130">.NET Core SDK 3,0</span><span class="sxs-lookup"><span data-stu-id="05a5e-130">.NET Core SDK 3.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

* <span data-ttu-id="05a5e-131">Docker Client 18,03 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="05a5e-131">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="05a5e-132">Linux dağıtımları</span><span class="sxs-lookup"><span data-stu-id="05a5e-132">Linux distributions</span></span>
    * [<span data-ttu-id="05a5e-133">CentOS</span><span class="sxs-lookup"><span data-stu-id="05a5e-133">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="05a5e-134">Debian</span><span class="sxs-lookup"><span data-stu-id="05a5e-134">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="05a5e-135">Fedora</span><span class="sxs-lookup"><span data-stu-id="05a5e-135">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="05a5e-136">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="05a5e-136">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="05a5e-137">macOS</span><span class="sxs-lookup"><span data-stu-id="05a5e-137">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="05a5e-138">Windows</span><span class="sxs-lookup"><span data-stu-id="05a5e-138">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="05a5e-139">Git</span><span class="sxs-lookup"><span data-stu-id="05a5e-139">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="05a5e-140">Örnek uygulamayı indirme</span><span class="sxs-lookup"><span data-stu-id="05a5e-140">Download the sample app</span></span>

* <span data-ttu-id="05a5e-141">[.NET Core Docker deposunu](https://github.com/dotnet/dotnet-docker)kopyalayarak örneği indirin:</span><span class="sxs-lookup"><span data-stu-id="05a5e-141">Download the sample by cloning the [.NET Core Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="05a5e-142">Uygulamayı yerel olarak çalıştırma</span><span class="sxs-lookup"><span data-stu-id="05a5e-142">Run the app locally</span></span>

* <span data-ttu-id="05a5e-143">*DotNet-Docker/Samples/aspnetapp/aspnetapp* konumundaki proje klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="05a5e-143">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp* .</span></span>

* <span data-ttu-id="05a5e-144">Uygulamayı yerel olarak derlemek ve çalıştırmak için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="05a5e-144">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="05a5e-145">`http://localhost:5000`Uygulamayı test etmek için bir tarayıcıda adresine gidin.</span><span class="sxs-lookup"><span data-stu-id="05a5e-145">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="05a5e-146">Uygulamayı durdurmak için komut isteminde CTRL + C tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="05a5e-146">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="05a5e-147">Linux kapsayıcısında çalıştırma</span><span class="sxs-lookup"><span data-stu-id="05a5e-147">Run in a Linux container</span></span>

* <span data-ttu-id="05a5e-148">Docker istemcisinde [Linux kapsayıcıları](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)' na geçin.</span><span class="sxs-lookup"><span data-stu-id="05a5e-148">In the Docker client, [switch to Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

* <span data-ttu-id="05a5e-149">*DotNet-Docker/Samples/aspnetapp* konumundaki Dockerfile klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="05a5e-149">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp* .</span></span>

* <span data-ttu-id="05a5e-150">Örneği Docker 'da derlemek ve çalıştırmak için aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="05a5e-150">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="05a5e-151">`build`Komut bağımsız değişkenleri:</span><span class="sxs-lookup"><span data-stu-id="05a5e-151">The `build` command arguments:</span></span>
  * <span data-ttu-id="05a5e-152">Görüntüyü aspnetapp olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="05a5e-152">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="05a5e-153">Geçerli klasörde Dockerfile dosyasını arayın (sonundaki süre).</span><span class="sxs-lookup"><span data-stu-id="05a5e-153">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="05a5e-154">Run komutu bağımsız değişkenleri:</span><span class="sxs-lookup"><span data-stu-id="05a5e-154">The run command arguments:</span></span>
  * <span data-ttu-id="05a5e-155">Bir sözde TTY ayırın ve ekli olmasa bile açık tutun.</span><span class="sxs-lookup"><span data-stu-id="05a5e-155">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="05a5e-156">(İle aynı efekt `--interactive --tty` )</span><span class="sxs-lookup"><span data-stu-id="05a5e-156">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="05a5e-157">Kapsayıcıyı çıkış sırasında otomatik olarak kaldır.</span><span class="sxs-lookup"><span data-stu-id="05a5e-157">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="05a5e-158">Yerel makinedeki 5000 numaralı bağlantı noktasını kapsayıcıda 80 numaralı bağlantı noktasına eşleyin.</span><span class="sxs-lookup"><span data-stu-id="05a5e-158">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="05a5e-159">Kapsayıcının aspnetcore_sample adı.</span><span class="sxs-lookup"><span data-stu-id="05a5e-159">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="05a5e-160">Aspnetapp görüntüsünü belirtin.</span><span class="sxs-lookup"><span data-stu-id="05a5e-160">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="05a5e-161">`http://localhost:5000`Uygulamayı test etmek için bir tarayıcıda adresine gidin.</span><span class="sxs-lookup"><span data-stu-id="05a5e-161">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="05a5e-162">Bir Windows kapsayıcısında Çalıştır</span><span class="sxs-lookup"><span data-stu-id="05a5e-162">Run in a Windows container</span></span>

* <span data-ttu-id="05a5e-163">Docker istemcisinde [Windows kapsayıcıları](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)' na geçin.</span><span class="sxs-lookup"><span data-stu-id="05a5e-163">In the Docker client, [switch to Windows containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

<span data-ttu-id="05a5e-164">Konumundaki Docker dosya klasörüne gidin `dotnet-docker/samples/aspnetapp` .</span><span class="sxs-lookup"><span data-stu-id="05a5e-164">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="05a5e-165">Örneği Docker 'da derlemek ve çalıştırmak için aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="05a5e-165">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="05a5e-166">Windows kapsayıcıları için kapsayıcının IP adresi gerekir (Bu işe göz atma `http://localhost:5000` çalışmayacak):</span><span class="sxs-lookup"><span data-stu-id="05a5e-166">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="05a5e-167">Başka bir komut istemi açın.</span><span class="sxs-lookup"><span data-stu-id="05a5e-167">Open up another command prompt.</span></span>
  * <span data-ttu-id="05a5e-168">`docker ps`Çalışan kapsayıcıları görmek için ' i çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="05a5e-168">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="05a5e-169">"Aspnetcore_sample" kapsayıcısının orada olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="05a5e-169">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="05a5e-170">`docker exec aspnetcore_sample ipconfig`KAPSAYıCıNıN IP adresini göstermek için öğesini çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="05a5e-170">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="05a5e-171">Komutun çıktısı şu örneğe benzer şekilde görünür:</span><span class="sxs-lookup"><span data-stu-id="05a5e-171">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="05a5e-172">Kapsayıcı IPv4 adresini (örneğin, 172.29.245.43) kopyalayın ve uygulamayı test etmek için tarayıcı adres çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="05a5e-172">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="05a5e-173">El ile derleyin ve dağıtın</span><span class="sxs-lookup"><span data-stu-id="05a5e-173">Build and deploy manually</span></span>

<span data-ttu-id="05a5e-174">Bazı senaryolarda, çalışma zamanında gerekli olan uygulama dosyalarını kopyalayarak bir kapsayıcıya uygulama dağıtmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="05a5e-174">In some scenarios, you might want to deploy an app to a container by copying to it the application files that are needed at run time.</span></span> <span data-ttu-id="05a5e-175">Bu bölümde nasıl el ile dağıtım yapılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="05a5e-175">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="05a5e-176">*DotNet-Docker/Samples/aspnetapp/aspnetapp* konumundaki proje klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="05a5e-176">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp* .</span></span>

* <span data-ttu-id="05a5e-177">[DotNet Publish](/dotnet/core/tools/dotnet-publish) komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="05a5e-177">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="05a5e-178">Komut bağımsız değişkenleri:</span><span class="sxs-lookup"><span data-stu-id="05a5e-178">The command arguments:</span></span>
  * <span data-ttu-id="05a5e-179">Uygulamayı yayın modunda (varsayılan olarak hata ayıklama modu) oluşturun.</span><span class="sxs-lookup"><span data-stu-id="05a5e-179">Build the application in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="05a5e-180">*Yayımlanan* klasörde dosyaları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="05a5e-180">Create the files in the *published* folder.</span></span>

* <span data-ttu-id="05a5e-181">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="05a5e-181">Run the application.</span></span>

  * <span data-ttu-id="05a5e-182">Windows:</span><span class="sxs-lookup"><span data-stu-id="05a5e-182">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="05a5e-183">Linux:</span><span class="sxs-lookup"><span data-stu-id="05a5e-183">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="05a5e-184">`http://localhost:5000`Giriş sayfasını görmek için öğesine gidin.</span><span class="sxs-lookup"><span data-stu-id="05a5e-184">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="05a5e-185">El ile yayınlanan uygulamayı bir Docker kapsayıcısı içinde kullanmak için yeni bir Dockerfile oluşturun ve `docker build .` kapsayıcıyı derlemek için komutunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="05a5e-185">To use the manually published application within a Docker container, create a new Dockerfile and use the `docker build .` command to build the container.</span></span>

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="05a5e-186">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="05a5e-186">The Dockerfile</span></span>

<span data-ttu-id="05a5e-187">Daha önce çalıştırdığınız komut tarafından kullanılan *Dockerfile* aşağıda verilmiştir `docker build` .</span><span class="sxs-lookup"><span data-stu-id="05a5e-187">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="05a5e-188">`dotnet publish`Bu bölümde derlemek ve dağıtmak için kullandığınız şekilde aynı şekilde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="05a5e-188">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

### <a name="the-dockerfile"></a><span data-ttu-id="05a5e-189">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="05a5e-189">The Dockerfile</span></span>

<span data-ttu-id="05a5e-190">Daha önce çalıştırdığınız komut tarafından kullanılan *Dockerfile* aşağıda verilmiştir `docker build` .</span><span class="sxs-lookup"><span data-stu-id="05a5e-190">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="05a5e-191">`dotnet publish`Bu bölümde derlemek ve dağıtmak için kullandığınız şekilde aynı şekilde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="05a5e-191">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

<span data-ttu-id="05a5e-192">Önceki Dockerfile içinde belirtildiği gibi, `*.csproj` dosyalar ayrı *Katmanlar* olarak kopyalanır ve geri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="05a5e-192">As noted in the preceding Dockerfile, the `*.csproj` files are copied and restored as distinct *layers* .</span></span> <span data-ttu-id="05a5e-193">`docker build`Komut bir görüntü oluşturduğunda, yerleşik bir önbellek kullanır.</span><span class="sxs-lookup"><span data-stu-id="05a5e-193">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="05a5e-194">`*.csproj`Komutun son çalıştırılmasından bu yana dosyalar değiştirilmediyse `docker build` , `dotnet restore` komutun yeniden çalıştırılması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="05a5e-194">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="05a5e-195">Bunun yerine, karşılık gelen katman için yerleşik önbellek `dotnet restore` yeniden kullanılır.</span><span class="sxs-lookup"><span data-stu-id="05a5e-195">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="05a5e-196">Daha fazla bilgi için bkz. [Dockerfiles yazmak Için en iyi uygulamalar](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span><span class="sxs-lookup"><span data-stu-id="05a5e-196">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="05a5e-197">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="05a5e-197">Additional resources</span></span>

* [<span data-ttu-id="05a5e-198">Docker derleme komutu</span><span class="sxs-lookup"><span data-stu-id="05a5e-198">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="05a5e-199">Docker Run komutu</span><span class="sxs-lookup"><span data-stu-id="05a5e-199">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="05a5e-200">[Docker örneğine ASP.NET Core](https://github.com/dotnet/dotnet-docker) (Bu öğreticide kullanılan bir tane).</span><span class="sxs-lookup"><span data-stu-id="05a5e-200">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="05a5e-201">Proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma</span><span class="sxs-lookup"><span data-stu-id="05a5e-201">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](../proxy-load-balancer.md)
* [<span data-ttu-id="05a5e-202">Visual Studio Docker araçlarıyla çalışma</span><span class="sxs-lookup"><span data-stu-id="05a5e-202">Working with Visual Studio Docker Tools</span></span>](./visual-studio-tools-for-docker.md)
* [<span data-ttu-id="05a5e-203">Visual Studio Code ile hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="05a5e-203">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="05a5e-204">Docker ve küçük kapsayıcılar kullanılarak GC</span><span class="sxs-lookup"><span data-stu-id="05a5e-204">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="05a5e-205">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="05a5e-205">Next steps</span></span>

<span data-ttu-id="05a5e-206">Örnek uygulamayı içeren git deposu, belgeleri de içerir.</span><span class="sxs-lookup"><span data-stu-id="05a5e-206">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="05a5e-207">Depodaki kullanılabilir kaynaklara genel bir bakış için [Readme dosyasına](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="05a5e-207">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="05a5e-208">Özellikle, HTTPS 'yi uygulamayı öğrenin:</span><span class="sxs-lookup"><span data-stu-id="05a5e-208">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="05a5e-209">HTTPS üzerinden Docker ile ASP.NET Core uygulamaları geliştirme</span><span class="sxs-lookup"><span data-stu-id="05a5e-209">Developing ASP.NET Core Applications with Docker over HTTPS</span></span>](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)
