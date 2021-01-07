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
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="20d2d-104">ASP.NET Core için Docker görüntüleri</span><span class="sxs-lookup"><span data-stu-id="20d2d-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="20d2d-105">Bu öğreticide, Docker kapsayıcılarında ASP.NET Core uygulamasının nasıl çalıştırılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="20d2d-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="20d2d-106">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="20d2d-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="20d2d-107">Docker görüntüleri ASP.NET Core hakkında bilgi edinin</span><span class="sxs-lookup"><span data-stu-id="20d2d-107">Learn about ASP.NET Core Docker images</span></span>
> * <span data-ttu-id="20d2d-108">ASP.NET Core örnek uygulaması indirin</span><span class="sxs-lookup"><span data-stu-id="20d2d-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="20d2d-109">Örnek uygulamayı yerel olarak çalıştırma</span><span class="sxs-lookup"><span data-stu-id="20d2d-109">Run the sample app locally</span></span>
> * <span data-ttu-id="20d2d-110">Linux kapsayıcılarında örnek uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="20d2d-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="20d2d-111">Örnek uygulamayı Windows kapsayıcılarında çalıştırma</span><span class="sxs-lookup"><span data-stu-id="20d2d-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="20d2d-112">El ile derleyin ve dağıtın</span><span class="sxs-lookup"><span data-stu-id="20d2d-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="20d2d-113">Docker görüntülerini ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="20d2d-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="20d2d-114">Bu öğreticide, ASP.NET Core örnek bir uygulama indirir ve Docker kapsayıcılarında çalıştırırsınız.</span><span class="sxs-lookup"><span data-stu-id="20d2d-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="20d2d-115">Örnek, hem Linux hem de Windows kapsayıcılarıyla birlikte çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="20d2d-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="20d2d-116">Örnek Dockerfile, farklı kapsayıcılarda derlemek ve çalıştırmak için [Docker Multi-Stage derleme özelliğini](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) kullanır.</span><span class="sxs-lookup"><span data-stu-id="20d2d-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="20d2d-117">Derleme ve çalıştırma kapsayıcıları, Docker Hub 'ında Microsoft tarafından sunulan görüntülerden oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="20d2d-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

::: moniker range=">= aspnetcore-5.0"

* `dotnet/sdk`

  <span data-ttu-id="20d2d-118">Örnek, uygulamayı oluşturmak için bu görüntüyü kullanır.</span><span class="sxs-lookup"><span data-stu-id="20d2d-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="20d2d-119">Görüntü, komut satırı araçları 'nı (CLı) içeren .NET SDK 'sını içerir.</span><span class="sxs-lookup"><span data-stu-id="20d2d-119">The image contains the .NET SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="20d2d-120">Görüntü yerel geliştirme, hata ayıklama ve birim testi için iyileştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="20d2d-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="20d2d-121">Geliştirme ve derleme için yüklenen araçlar görüntüyü görece büyük yapar.</span><span class="sxs-lookup"><span data-stu-id="20d2d-121">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/sdk`

  <span data-ttu-id="20d2d-122">Örnek, uygulamayı oluşturmak için bu görüntüyü kullanır.</span><span class="sxs-lookup"><span data-stu-id="20d2d-122">The sample uses this image for building the app.</span></span> <span data-ttu-id="20d2d-123">Görüntü, komut satırı araçlarını (CLı) içeren .NET Core SDK içerir.</span><span class="sxs-lookup"><span data-stu-id="20d2d-123">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="20d2d-124">Görüntü yerel geliştirme, hata ayıklama ve birim testi için iyileştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="20d2d-124">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="20d2d-125">Geliştirme ve derleme için yüklenen araçlar görüntüyü görece büyük yapar.</span><span class="sxs-lookup"><span data-stu-id="20d2d-125">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `dotnet/aspnet`

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/aspnet`

::: moniker-end

   <span data-ttu-id="20d2d-126">Örnek, uygulamayı çalıştırmak için bu görüntüyü kullanır.</span><span class="sxs-lookup"><span data-stu-id="20d2d-126">The sample uses this image for running the app.</span></span> <span data-ttu-id="20d2d-127">Görüntü, ASP.NET Core çalışma zamanını ve kitaplıklarını içerir ve üretimde uygulamaları çalıştırmak için iyileştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="20d2d-127">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="20d2d-128">Dağıtım ve uygulama başlatma hızı için tasarlanan görüntü görece küçüktür, bu nedenle Docker kayıt defterinden Docker ana bilgisayarına ağ performansı en iyi duruma getirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="20d2d-128">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="20d2d-129">Yalnızca bir uygulamayı çalıştırmak için gereken ikili dosyalar ve içerikler kapsayıcıya kopyalanır.</span><span class="sxs-lookup"><span data-stu-id="20d2d-129">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="20d2d-130">İçerik çalıştırılmaya hazırlanmaya, en hızlı süreyi uygulama başlatmaya kadar etkinleştirir `docker run` .</span><span class="sxs-lookup"><span data-stu-id="20d2d-130">The contents are ready to run, enabling the fastest time from `docker run` to app startup.</span></span> <span data-ttu-id="20d2d-131">Docker modelinde dinamik kod derlemesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="20d2d-131">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="20d2d-132">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="20d2d-132">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="20d2d-133">.NET SDK 5.0</span><span class="sxs-lookup"><span data-stu-id="20d2d-133">.NET SDK 5.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* [<span data-ttu-id="20d2d-134">.NET Core SDK 3.1</span><span class="sxs-lookup"><span data-stu-id="20d2d-134">.NET Core SDK 3.1</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="20d2d-135">.NET Core 2,2 SDK</span><span class="sxs-lookup"><span data-stu-id="20d2d-135">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

::: moniker-end

* <span data-ttu-id="20d2d-136">Docker Client 18,03 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="20d2d-136">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="20d2d-137">Linux dağıtımları</span><span class="sxs-lookup"><span data-stu-id="20d2d-137">Linux distributions</span></span>
    * [<span data-ttu-id="20d2d-138">CentOS</span><span class="sxs-lookup"><span data-stu-id="20d2d-138">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="20d2d-139">Debian</span><span class="sxs-lookup"><span data-stu-id="20d2d-139">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="20d2d-140">Fedora</span><span class="sxs-lookup"><span data-stu-id="20d2d-140">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="20d2d-141">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="20d2d-141">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="20d2d-142">macOS</span><span class="sxs-lookup"><span data-stu-id="20d2d-142">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="20d2d-143">Windows</span><span class="sxs-lookup"><span data-stu-id="20d2d-143">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="20d2d-144">Git</span><span class="sxs-lookup"><span data-stu-id="20d2d-144">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="20d2d-145">Örnek uygulamayı indirme</span><span class="sxs-lookup"><span data-stu-id="20d2d-145">Download the sample app</span></span>

* <span data-ttu-id="20d2d-146">[.Net Docker deposunu](https://github.com/dotnet/dotnet-docker)kopyalayarak örneği indirin:</span><span class="sxs-lookup"><span data-stu-id="20d2d-146">Download the sample by cloning the [.NET Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="20d2d-147">Uygulamayı yerel olarak çalıştırma</span><span class="sxs-lookup"><span data-stu-id="20d2d-147">Run the app locally</span></span>

* <span data-ttu-id="20d2d-148">*DotNet-Docker/Samples/aspnetapp/aspnetapp* konumundaki proje klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="20d2d-148">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="20d2d-149">Uygulamayı yerel olarak derlemek ve çalıştırmak için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="20d2d-149">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="20d2d-150">`http://localhost:5000`Uygulamayı test etmek için bir tarayıcıda adresine gidin.</span><span class="sxs-lookup"><span data-stu-id="20d2d-150">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="20d2d-151">Uygulamayı durdurmak için komut isteminde CTRL + C tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="20d2d-151">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="20d2d-152">Linux kapsayıcısında çalıştırma</span><span class="sxs-lookup"><span data-stu-id="20d2d-152">Run in a Linux container</span></span>

* <span data-ttu-id="20d2d-153">Docker istemcisinde [Linux kapsayıcıları](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)' na geçin.</span><span class="sxs-lookup"><span data-stu-id="20d2d-153">In the Docker client, [switch to Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

* <span data-ttu-id="20d2d-154">*DotNet-Docker/Samples/aspnetapp* konumundaki Dockerfile klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="20d2d-154">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="20d2d-155">Örneği Docker 'da derlemek ve çalıştırmak için aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="20d2d-155">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="20d2d-156">`build`Komut bağımsız değişkenleri:</span><span class="sxs-lookup"><span data-stu-id="20d2d-156">The `build` command arguments:</span></span>
  * <span data-ttu-id="20d2d-157">Görüntüyü aspnetapp olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="20d2d-157">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="20d2d-158">Geçerli klasörde Dockerfile dosyasını arayın (sonundaki süre).</span><span class="sxs-lookup"><span data-stu-id="20d2d-158">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="20d2d-159">Run komutu bağımsız değişkenleri:</span><span class="sxs-lookup"><span data-stu-id="20d2d-159">The run command arguments:</span></span>
  * <span data-ttu-id="20d2d-160">Bir sözde TTY ayırın ve ekli olmasa bile açık tutun.</span><span class="sxs-lookup"><span data-stu-id="20d2d-160">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="20d2d-161">(İle aynı efekt `--interactive --tty` )</span><span class="sxs-lookup"><span data-stu-id="20d2d-161">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="20d2d-162">Kapsayıcıyı çıkış sırasında otomatik olarak kaldır.</span><span class="sxs-lookup"><span data-stu-id="20d2d-162">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="20d2d-163">Yerel makinedeki 5000 numaralı bağlantı noktasını kapsayıcıda 80 numaralı bağlantı noktasına eşleyin.</span><span class="sxs-lookup"><span data-stu-id="20d2d-163">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="20d2d-164">Kapsayıcının aspnetcore_sample adı.</span><span class="sxs-lookup"><span data-stu-id="20d2d-164">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="20d2d-165">Aspnetapp görüntüsünü belirtin.</span><span class="sxs-lookup"><span data-stu-id="20d2d-165">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="20d2d-166">`http://localhost:5000`Uygulamayı test etmek için bir tarayıcıda adresine gidin.</span><span class="sxs-lookup"><span data-stu-id="20d2d-166">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="20d2d-167">Bir Windows kapsayıcısında Çalıştır</span><span class="sxs-lookup"><span data-stu-id="20d2d-167">Run in a Windows container</span></span>

* <span data-ttu-id="20d2d-168">Docker istemcisinde [Windows kapsayıcıları](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)' na geçin.</span><span class="sxs-lookup"><span data-stu-id="20d2d-168">In the Docker client, [switch to Windows containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

<span data-ttu-id="20d2d-169">Konumundaki Docker dosya klasörüne gidin `dotnet-docker/samples/aspnetapp` .</span><span class="sxs-lookup"><span data-stu-id="20d2d-169">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="20d2d-170">Örneği Docker 'da derlemek ve çalıştırmak için aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="20d2d-170">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="20d2d-171">Windows kapsayıcıları için kapsayıcının IP adresi gerekir (Bu işe göz atma `http://localhost:5000` çalışmayacak):</span><span class="sxs-lookup"><span data-stu-id="20d2d-171">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="20d2d-172">Başka bir komut istemi açın.</span><span class="sxs-lookup"><span data-stu-id="20d2d-172">Open up another command prompt.</span></span>
  * <span data-ttu-id="20d2d-173">`docker ps`Çalışan kapsayıcıları görmek için ' i çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="20d2d-173">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="20d2d-174">"Aspnetcore_sample" kapsayıcısının orada olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="20d2d-174">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="20d2d-175">`docker exec aspnetcore_sample ipconfig`KAPSAYıCıNıN IP adresini göstermek için öğesini çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="20d2d-175">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="20d2d-176">Komutun çıktısı şu örneğe benzer şekilde görünür:</span><span class="sxs-lookup"><span data-stu-id="20d2d-176">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="20d2d-177">Kapsayıcı IPv4 adresini (örneğin, 172.29.245.43) kopyalayın ve uygulamayı test etmek için tarayıcı adres çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="20d2d-177">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="20d2d-178">El ile derleyin ve dağıtın</span><span class="sxs-lookup"><span data-stu-id="20d2d-178">Build and deploy manually</span></span>

<span data-ttu-id="20d2d-179">Bazı senaryolarda, çalışma zamanında gereken varlıkları kopyalayarak bir kapsayıcıya uygulama dağıtmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="20d2d-179">In some scenarios, you might want to deploy an app to a container by copying its assets that are needed at run time.</span></span> <span data-ttu-id="20d2d-180">Bu bölümde nasıl el ile dağıtım yapılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="20d2d-180">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="20d2d-181">*DotNet-Docker/Samples/aspnetapp/aspnetapp* konumundaki proje klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="20d2d-181">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="20d2d-182">[DotNet Publish](/dotnet/core/tools/dotnet-publish) komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="20d2d-182">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="20d2d-183">Komut bağımsız değişkenleri:</span><span class="sxs-lookup"><span data-stu-id="20d2d-183">The command arguments:</span></span>
  * <span data-ttu-id="20d2d-184">Uygulamayı yayın modunda (varsayılan olarak hata ayıklama modu) oluşturun.</span><span class="sxs-lookup"><span data-stu-id="20d2d-184">Build the app in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="20d2d-185">*Yayımlanan* klasörde varlıklar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="20d2d-185">Create the assets in the *published* folder.</span></span>

* <span data-ttu-id="20d2d-186">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="20d2d-186">Run the app.</span></span>

  * <span data-ttu-id="20d2d-187">Windows:</span><span class="sxs-lookup"><span data-stu-id="20d2d-187">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="20d2d-188">Linux:</span><span class="sxs-lookup"><span data-stu-id="20d2d-188">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="20d2d-189">`http://localhost:5000`Giriş sayfasını görmek için öğesine gidin.</span><span class="sxs-lookup"><span data-stu-id="20d2d-189">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="20d2d-190">El ile yayınlanan uygulamayı bir Docker kapsayıcısı içinde kullanmak için yeni bir *Dockerfile* oluşturun ve `docker build .` kapsayıcıyı derlemek için komutunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="20d2d-190">To use the manually published app within a Docker container, create a new *Dockerfile* and use the `docker build .` command to build the container.</span></span>

::: moniker range=">= aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="20d2d-191">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="20d2d-191">The Dockerfile</span></span>

<span data-ttu-id="20d2d-192">Daha önce çalıştırdığınız komut tarafından kullanılan *Dockerfile* aşağıda verilmiştir `docker build` .</span><span class="sxs-lookup"><span data-stu-id="20d2d-192">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="20d2d-193">`dotnet publish`Bu bölümde derlemek ve dağıtmak için kullandığınız şekilde aynı şekilde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="20d2d-193">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

<span data-ttu-id="20d2d-194">Yukarıdaki *Dockerfile*'da, `*.csproj` dosyalar ayrı *Katmanlar* olarak kopyalanır ve geri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="20d2d-194">In the preceding *Dockerfile*, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="20d2d-195">`docker build`Komut bir görüntü oluşturduğunda, yerleşik bir önbellek kullanır.</span><span class="sxs-lookup"><span data-stu-id="20d2d-195">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="20d2d-196">`*.csproj`Komutun son çalıştırılmasından bu yana dosyalar değiştirilmediyse `docker build` , `dotnet restore` komutun yeniden çalıştırılması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="20d2d-196">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="20d2d-197">Bunun yerine, karşılık gelen katman için yerleşik önbellek `dotnet restore` yeniden kullanılır.</span><span class="sxs-lookup"><span data-stu-id="20d2d-197">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="20d2d-198">Daha fazla bilgi için bkz. [Dockerfiles yazmak Için en iyi uygulamalar](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span><span class="sxs-lookup"><span data-stu-id="20d2d-198">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="20d2d-199">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="20d2d-199">The Dockerfile</span></span>

<span data-ttu-id="20d2d-200">Daha önce çalıştırdığınız komut tarafından kullanılan *Dockerfile* aşağıda verilmiştir `docker build` .</span><span class="sxs-lookup"><span data-stu-id="20d2d-200">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="20d2d-201">`dotnet publish`Bu bölümde derlemek ve dağıtmak için kullandığınız şekilde aynı şekilde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="20d2d-201">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

<span data-ttu-id="20d2d-202">Önceki Dockerfile içinde belirtildiği gibi, `*.csproj` dosyalar ayrı *Katmanlar* olarak kopyalanır ve geri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="20d2d-202">As noted in the preceding Dockerfile, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="20d2d-203">`docker build`Komut bir görüntü oluşturduğunda, yerleşik bir önbellek kullanır.</span><span class="sxs-lookup"><span data-stu-id="20d2d-203">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="20d2d-204">`*.csproj`Komutun son çalıştırılmasından bu yana dosyalar değiştirilmediyse `docker build` , `dotnet restore` komutun yeniden çalıştırılması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="20d2d-204">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="20d2d-205">Bunun yerine, karşılık gelen katman için yerleşik önbellek `dotnet restore` yeniden kullanılır.</span><span class="sxs-lookup"><span data-stu-id="20d2d-205">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="20d2d-206">Daha fazla bilgi için bkz. [Dockerfiles yazmak Için en iyi uygulamalar](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span><span class="sxs-lookup"><span data-stu-id="20d2d-206">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="20d2d-207">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="20d2d-207">The Dockerfile</span></span>

<span data-ttu-id="20d2d-208">Daha önce çalıştırdığınız komut tarafından kullanılan *Dockerfile* aşağıda verilmiştir `docker build` .</span><span class="sxs-lookup"><span data-stu-id="20d2d-208">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span> <span data-ttu-id="20d2d-209">`dotnet publish`Bu bölümde derlemek ve dağıtmak için kullandığınız şekilde aynı şekilde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="20d2d-209">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

## <a name="additional-resources"></a><span data-ttu-id="20d2d-210">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="20d2d-210">Additional resources</span></span>

* [<span data-ttu-id="20d2d-211">Docker derleme komutu</span><span class="sxs-lookup"><span data-stu-id="20d2d-211">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="20d2d-212">Docker Run komutu</span><span class="sxs-lookup"><span data-stu-id="20d2d-212">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="20d2d-213">[Docker örneğine ASP.NET Core](https://github.com/dotnet/dotnet-docker) (Bu öğreticide kullanılan bir tane).</span><span class="sxs-lookup"><span data-stu-id="20d2d-213">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="20d2d-214">Proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma</span><span class="sxs-lookup"><span data-stu-id="20d2d-214">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](../proxy-load-balancer.md)
* [<span data-ttu-id="20d2d-215">Visual Studio Docker araçlarıyla çalışma</span><span class="sxs-lookup"><span data-stu-id="20d2d-215">Working with Visual Studio Docker Tools</span></span>](./visual-studio-tools-for-docker.md)
* [<span data-ttu-id="20d2d-216">Visual Studio Code ile hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="20d2d-216">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="20d2d-217">Docker ve küçük kapsayıcılar kullanılarak GC</span><span class="sxs-lookup"><span data-stu-id="20d2d-217">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="20d2d-218">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="20d2d-218">Next steps</span></span>

<span data-ttu-id="20d2d-219">Örnek uygulamayı içeren git deposu, belgeleri de içerir.</span><span class="sxs-lookup"><span data-stu-id="20d2d-219">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="20d2d-220">Depodaki kullanılabilir kaynaklara genel bir bakış için [Readme dosyasına](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md)bakın.</span><span class="sxs-lookup"><span data-stu-id="20d2d-220">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="20d2d-221">Özellikle, HTTPS 'yi uygulamayı öğrenin:</span><span class="sxs-lookup"><span data-stu-id="20d2d-221">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="20d2d-222">HTTPS üzerinden Docker ile ASP.NET Core uygulamaları geliştirme</span><span class="sxs-lookup"><span data-stu-id="20d2d-222">Developing ASP.NET Core Applications with Docker over HTTPS</span></span>](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)
