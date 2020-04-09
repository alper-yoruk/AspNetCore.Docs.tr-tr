---
title: ASP.NET Core ile Visual Studio Konteyner Araçları
author: spboyer
description: ASP.NET Core uygulamasını kapsayıcı hale getirmek için Visual Studio aracını ve Windows için Docker'ı nasıl kullanacağınızı öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: 0e6747a3de220b97cc7a84f9cd42b0da54b57ee9
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664068"
---
# <a name="visual-studio-container-tools-with-aspnet-core"></a><span data-ttu-id="1c657-103">ASP.NET Core ile Visual Studio Konteyner Araçları</span><span class="sxs-lookup"><span data-stu-id="1c657-103">Visual Studio Container Tools with ASP.NET Core</span></span>

<span data-ttu-id="1c657-104">Visual Studio 2017 ve sonraki sürümleri ,NET Core'u hedefleyen konteynerleştirilmiş ASP.NET Core uygulamalarını oluşturmayı, hata ayıklamayı ve çalıştırmayı destekler.</span><span class="sxs-lookup"><span data-stu-id="1c657-104">Visual Studio 2017 and later versions support building, debugging, and running containerized ASP.NET Core apps targeting .NET Core.</span></span> <span data-ttu-id="1c657-105">Hem Windows hem de Linux kapsayıcıları desteklenir.</span><span class="sxs-lookup"><span data-stu-id="1c657-105">Both Windows and Linux containers are supported.</span></span>

<span data-ttu-id="1c657-106">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1c657-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1c657-107">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="1c657-107">Prerequisites</span></span>

* [<span data-ttu-id="1c657-108">Windows için Docker</span><span class="sxs-lookup"><span data-stu-id="1c657-108">Docker for Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)
* <span data-ttu-id="1c657-109">**.NET Core çapraz platform geliştirme** iş yükü ile Visual Studio [2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="1c657-109">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **.NET Core cross-platform development** workload</span></span>

## <a name="installation-and-setup"></a><span data-ttu-id="1c657-110">Kurulum ve kurulum</span><span class="sxs-lookup"><span data-stu-id="1c657-110">Installation and setup</span></span>

<span data-ttu-id="1c657-111">Docker yüklemesi için, yüklemeden önce Docker for Windows'daki bilgileri gözden [geçirin: Yüklemeden önce bilinmesi gerekenler.](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)</span><span class="sxs-lookup"><span data-stu-id="1c657-111">For Docker installation, first review the information at [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).</span></span> <span data-ttu-id="1c657-112">Ardından, [Windows için Docker'ı](https://docs.docker.com/docker-for-windows/install/)yükleyin.</span><span class="sxs-lookup"><span data-stu-id="1c657-112">Next, install [Docker For Windows](https://docs.docker.com/docker-for-windows/install/).</span></span>

<span data-ttu-id="1c657-113">Windows için Docker'daki **[Paylaşılan Sürücüler,](https://docs.docker.com/docker-for-windows/#shared-drives)** ses düzeyini eşleme ve hata ayıklamayı destekleyecek şekilde yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1c657-113">**[Shared Drives](https://docs.docker.com/docker-for-windows/#shared-drives)** in Docker for Windows must be configured to support volume mapping and debugging.</span></span> <span data-ttu-id="1c657-114">Sistem Tepsisi'nin Docker simgesine sağ tıklayın, **Ayarlar'ı**seçin ve **Paylaşılan Sürücüler'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="1c657-114">Right-click the System Tray's Docker icon, select **Settings**, and select **Shared Drives**.</span></span> <span data-ttu-id="1c657-115">Docker'ın dosyaları depoladığı sürücüyü seçin.</span><span class="sxs-lookup"><span data-stu-id="1c657-115">Select the drive where Docker stores files.</span></span> <span data-ttu-id="1c657-116">**Uygula**’ya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="1c657-116">Click **Apply**.</span></span>

![Kapsayıcılar için yerel C sürücü paylaşımını seçmek için iletişim kutusu](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> <span data-ttu-id="1c657-118">Visual Studio 2017 sürümleri 15.6 ve paylaşılan **sürücüler** yapılandırılmadı sonra istemi.</span><span class="sxs-lookup"><span data-stu-id="1c657-118">Visual Studio 2017 versions 15.6 and later prompt when **Shared Drives** aren't configured.</span></span>

## <a name="add-a-project-to-a-docker-container"></a><span data-ttu-id="1c657-119">Docker kapsayıcısına proje ekleme</span><span class="sxs-lookup"><span data-stu-id="1c657-119">Add a project to a Docker container</span></span>

<span data-ttu-id="1c657-120">Bir ASP.NET Core projesini kapsayıcı hale getirmek için projenin .NET Core'u hedeflemesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="1c657-120">To containerize an ASP.NET Core project, the project must target .NET Core.</span></span> <span data-ttu-id="1c657-121">Hem Linux hem de Windows kapsayıcıları desteklenir.</span><span class="sxs-lookup"><span data-stu-id="1c657-121">Both Linux and Windows containers are supported.</span></span>

<span data-ttu-id="1c657-122">Bir projeye Docker desteği eklerken, bir Windows veya Linux kapsayıcısı seçin.</span><span class="sxs-lookup"><span data-stu-id="1c657-122">When adding Docker support to a project, choose either a Windows or a Linux container.</span></span> <span data-ttu-id="1c657-123">Docker ana bilgisayar aynı kapsayıcı türünü çalıştırıyor olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1c657-123">The Docker host must be running the same container type.</span></span> <span data-ttu-id="1c657-124">Çalışan Docker örneğinde kapsayıcı türünü değiştirmek için Sistem Tepsisi Docker simgesine sağ tıklayın ve **Windows kapsayıcılarına geçiş'i seçin...** veya **Linux kapsayıcılarına geçiş...**.</span><span class="sxs-lookup"><span data-stu-id="1c657-124">To change the container type in the running Docker instance, right-click the System Tray's Docker icon and choose **Switch to Windows containers...** or **Switch to Linux containers...**.</span></span>

### <a name="new-app"></a><span data-ttu-id="1c657-125">Yeni uygulama</span><span class="sxs-lookup"><span data-stu-id="1c657-125">New app</span></span>

<span data-ttu-id="1c657-126">**ASP.NET Core Web Application** proje şablonları ile yeni bir uygulama oluştururken, Docker Destek onay kutusunu **etkinleştir'i** seçin:</span><span class="sxs-lookup"><span data-stu-id="1c657-126">When creating a new app with the **ASP.NET Core Web Application** project templates, select the **Enable Docker Support** check box:</span></span>

![Docker Destek onay kutusunu etkinleştir](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

<span data-ttu-id="1c657-128">Hedef çerçeve .NET Core ise, **işletim sistemi** açılır bir kapsayıcı türü seçimine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="1c657-128">If the target framework is .NET Core, the **OS** drop-down allows for the selection of a container type.</span></span>

### <a name="existing-app"></a><span data-ttu-id="1c657-129">Mevcut uygulama</span><span class="sxs-lookup"><span data-stu-id="1c657-129">Existing app</span></span>

<span data-ttu-id="1c657-130">.NET Core'u hedefleyen ASP.NET Core projeleri için, takım lama yoluyla Docker desteği eklemek için iki seçenek vardır.</span><span class="sxs-lookup"><span data-stu-id="1c657-130">For ASP.NET Core projects targeting .NET Core, there are two options for adding Docker support via the tooling.</span></span> <span data-ttu-id="1c657-131">Projeyi Visual Studio'da açın ve aşağıdaki seçeneklerden birini seçin:</span><span class="sxs-lookup"><span data-stu-id="1c657-131">Open the project in Visual Studio, and choose one of the following options:</span></span>

* <span data-ttu-id="1c657-132">**Proje** menüsünden **Docker Desteği'ni** seçin.</span><span class="sxs-lookup"><span data-stu-id="1c657-132">Select **Docker Support** from the **Project** menu.</span></span>
* <span data-ttu-id="1c657-133">**Solution Explorer'da** projeye sağ tıklayın ve**Docker Desteği** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="1c657-133">Right-click the project in **Solution Explorer** and select **Add** > **Docker Support**.</span></span>

<span data-ttu-id="1c657-134">Visual Studio Container Tools, .NET Framework'u hedefleyen mevcut bir ASP.NET Core projesine Docker'ın eklenmesini desteklemez.</span><span class="sxs-lookup"><span data-stu-id="1c657-134">The Visual Studio Container Tools don't support adding Docker to an existing ASP.NET Core project targeting .NET Framework.</span></span>

## <a name="dockerfile-overview"></a><span data-ttu-id="1c657-135">Dockerfile'a genel bakış</span><span class="sxs-lookup"><span data-stu-id="1c657-135">Dockerfile overview</span></span>

<span data-ttu-id="1c657-136">Bir *Dockerfile*, son docker görüntü oluşturmak için reçete, proje köküne eklenir.</span><span class="sxs-lookup"><span data-stu-id="1c657-136">A *Dockerfile*, the recipe for creating a final Docker image, is added to the project root.</span></span> <span data-ttu-id="1c657-137">İçindeki komutların anlaşılması için [Dockerfile başvurusuna](https://docs.docker.com/engine/reference/builder/) bakın.</span><span class="sxs-lookup"><span data-stu-id="1c657-137">Refer to [Dockerfile reference](https://docs.docker.com/engine/reference/builder/) for an understanding of the commands within it.</span></span> <span data-ttu-id="1c657-138">Bu özel *Dockerfile* dört farklı, adlı yapı aşamaları ile [çok aşamalı](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) bir yapı kullanır:</span><span class="sxs-lookup"><span data-stu-id="1c657-138">This particular *Dockerfile* uses a [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) with four distinct, named build stages:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

<span data-ttu-id="1c657-139">Önceki *Dockerfile* [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/) resmine dayanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="1c657-139">The preceding *Dockerfile* is based on the [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/) image.</span></span> <span data-ttu-id="1c657-140">Bu temel görüntü, Core çalışma zamanı ve NuGet paketleriASP.NET içerir.</span><span class="sxs-lookup"><span data-stu-id="1c657-140">This base image includes the ASP.NET Core runtime and NuGet packages.</span></span> <span data-ttu-id="1c657-141">Paketler başlangıç performansını artırmak için tam zamanında (JIT) derlenmiştir.</span><span class="sxs-lookup"><span data-stu-id="1c657-141">The packages are just-in-time (JIT) compiled to improve startup performance.</span></span>

<span data-ttu-id="1c657-142">Yeni proje iletişim kutusunun HTTPS için **Yapılandırma** onay kutusu işaretlendiğinde, *Dockerfile* iki bağlantı noktasını ortaya çıkarır.</span><span class="sxs-lookup"><span data-stu-id="1c657-142">When the new project dialog's **Configure for HTTPS** check box is checked, the *Dockerfile* exposes two ports.</span></span> <span data-ttu-id="1c657-143">Bir bağlantı noktası HTTP trafiği için kullanılır; diğer bağlantı noktası HTTPS için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1c657-143">One port is used for HTTP traffic; the other port is used for HTTPS.</span></span> <span data-ttu-id="1c657-144">Onay kutusu işaretlenmezse, HTTP trafiği için tek bir bağlantı noktası (80) açığa çıkarır.</span><span class="sxs-lookup"><span data-stu-id="1c657-144">If the check box isn't checked, a single port (80) is exposed for HTTP traffic.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

<span data-ttu-id="1c657-145">Önceki *Dockerfile* [microsoft/ aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) görüntü dayanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="1c657-145">The preceding *Dockerfile* is based on the [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) image.</span></span> <span data-ttu-id="1c657-146">Bu temel görüntü, başlangıç performansını artırmak için derlenen tam zamanında (JIT) ASP.NET Core NuGet paketlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="1c657-146">This base image includes the ASP.NET Core NuGet packages, which are just-in-time (JIT) compiled to improve startup performance.</span></span>

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a><span data-ttu-id="1c657-147">Bir uygulamaya konteyner orkestrator desteği ekleme</span><span class="sxs-lookup"><span data-stu-id="1c657-147">Add container orchestrator support to an app</span></span>

<span data-ttu-id="1c657-148">Visual Studio 2017 sürümleri 15.7 veya önceki destek [Docker Compose](https://docs.docker.com/compose/overview/) tek konteyner orkestrasyon çözümü olarak.</span><span class="sxs-lookup"><span data-stu-id="1c657-148">Visual Studio 2017 versions 15.7 or earlier support [Docker Compose](https://docs.docker.com/compose/overview/) as the sole container orchestration solution.</span></span> <span data-ttu-id="1c657-149">Docker Compose **yapıları, Ekle** > **Docker Desteği**ile eklenir.</span><span class="sxs-lookup"><span data-stu-id="1c657-149">The Docker Compose artifacts are added via **Add** > **Docker Support**.</span></span>

<span data-ttu-id="1c657-150">Visual Studio 2017 sürümleri 15.8 veya daha sonra sadece talimat verildiğinde bir orkestrasyon çözümü ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1c657-150">Visual Studio 2017 versions 15.8 or later add an orchestration solution only when instructed.</span></span> <span data-ttu-id="1c657-151">**Solution Explorer'da** projeye sağ tıklayın ve**Kapsayıcı Orkestrator Desteği** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="1c657-151">Right-click the project in **Solution Explorer** and select **Add** > **Container Orchestrator Support**.</span></span> <span data-ttu-id="1c657-152">İki farklı seçenek sunulmaktadır: [Docker Compose](#docker-compose) ve [Servis Kumaşı.](#service-fabric)</span><span class="sxs-lookup"><span data-stu-id="1c657-152">Two different choices are offered: [Docker Compose](#docker-compose) and [Service Fabric](#service-fabric).</span></span>

### <a name="docker-compose"></a><span data-ttu-id="1c657-153">Docker Oluştur</span><span class="sxs-lookup"><span data-stu-id="1c657-153">Docker Compose</span></span>

<span data-ttu-id="1c657-154">Visual Studio Container Tools aşağıdaki dosyalarla çözüme bir *docker-beste* projesi ekler:</span><span class="sxs-lookup"><span data-stu-id="1c657-154">The Visual Studio Container Tools add a *docker-compose* project to the solution with the following files:</span></span>

* <span data-ttu-id="1c657-155">*docker-compose.dcproj* &ndash; Projeyi temsil eden dosya.</span><span class="sxs-lookup"><span data-stu-id="1c657-155">*docker-compose.dcproj* &ndash; The file representing the project.</span></span> <span data-ttu-id="1c657-156">Kullanılacak `<DockerTargetOS>` işletim sistemi belirten bir öğe içerir.</span><span class="sxs-lookup"><span data-stu-id="1c657-156">Includes a `<DockerTargetOS>` element specifying the OS to be used.</span></span>
* <span data-ttu-id="1c657-157">*.dockerignore* &ndash; Yapı bağlamı oluştururken dışlanması gereken dosya ve dizin desenleri listeler.</span><span class="sxs-lookup"><span data-stu-id="1c657-157">*.dockerignore* &ndash; Lists the file and directory patterns to exclude when generating a build context.</span></span>
* <span data-ttu-id="1c657-158">*docker-compose.yml* &ndash; Temel [Docker Compose](https://docs.docker.com/compose/overview/) dosyası, sırasıyla, sırasıyla, `docker-compose run`birlikte oluşturulmuş ve çalıştırılan `docker-compose build` görüntülerin toplanmasını tanımlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1c657-158">*docker-compose.yml* &ndash; The base [Docker Compose](https://docs.docker.com/compose/overview/) file used to define the collection of images built and run with `docker-compose build` and `docker-compose run`, respectively.</span></span>
* <span data-ttu-id="1c657-159">*docker-compose.override.yml* &ndash; İsteğe bağlı bir dosya, Docker Compose tarafından okunan, hizmetler için yapılandırma geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="1c657-159">*docker-compose.override.yml* &ndash; An optional file, read by Docker Compose, with configuration overrides for services.</span></span> <span data-ttu-id="1c657-160">Visual Studio `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` bu dosyaları birleştirmek için yürütür.</span><span class="sxs-lookup"><span data-stu-id="1c657-160">Visual Studio executes `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` to merge these files.</span></span>

<span data-ttu-id="1c657-161">*Docker-compose.yml* dosyası, proje çalıştığında oluşturulan görüntünün adına başvurur:</span><span class="sxs-lookup"><span data-stu-id="1c657-161">The *docker-compose.yml* file references the name of the image that's created when the project runs:</span></span>

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

<span data-ttu-id="1c657-162">Önceki örnekte, `image: hellodockertools` uygulama Hata `hellodockertools:dev` **Ayıklama** modunda çalıştığında görüntüyü oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1c657-162">In the preceding example, `image: hellodockertools` generates the image `hellodockertools:dev` when the app runs in **Debug** mode.</span></span> <span data-ttu-id="1c657-163">Uygulama `hellodockertools:latest` **Sürüm** modunda çalıştığında görüntü oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="1c657-163">The `hellodockertools:latest` image is generated when the app runs in **Release** mode.</span></span>

<span data-ttu-id="1c657-164">Görüntü kayıt defterine itilirse, görüntü `dockerhubusername/hellodockertools`adını Docker [Hub](https://hub.docker.com/) kullanıcı adı (örneğin, ) ile önek.</span><span class="sxs-lookup"><span data-stu-id="1c657-164">Prefix the image name with the [Docker Hub](https://hub.docker.com/) username (for example, `dockerhubusername/hellodockertools`) if the image is pushed to the registry.</span></span> <span data-ttu-id="1c657-165">Alternatif olarak, yapılandırmaya bağlı olarak resim adını özel `privateregistry.domain.com/hellodockertools`kayıt defteri URL'sini (örneğin) içerecek şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="1c657-165">Alternatively, change the image name to include the private registry URL (for example, `privateregistry.domain.com/hellodockertools`) depending on the configuration.</span></span>

<span data-ttu-id="1c657-166">Yapı yapılandırmasını temel alan farklı davranışlar (örneğin Hata Ayıklama veya Sürüm), yapılandırmaya özgü *docker-compose* dosyaları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1c657-166">If you want different behavior based on the build configuration (for example, Debug or Release), add configuration-specific *docker-compose* files.</span></span> <span data-ttu-id="1c657-167">Dosyalar yapı yapılandırmasına göre adlandırılmalı (örneğin, *docker-compose.vs.debug.yml* ve *docker-compose.vs.release.yml)* ve *docker-compose-override.yml* dosyasıyla aynı konuma yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="1c657-167">The files should be named according to the build configuration (for example, *docker-compose.vs.debug.yml* and *docker-compose.vs.release.yml*) and placed in the same location as the *docker-compose-override.yml* file.</span></span> 

<span data-ttu-id="1c657-168">Yapılandırmaya özgü geçersiz kılma dosyalarını kullanarak Hata Ayıklama ve Sürüm yapı yapılandırmaları için farklı yapılandırma ayarlarını (ortam değişkenleri veya giriş noktaları gibi) belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1c657-168">Using the configuration-specific override files, you can specify different configuration settings (such as environment variables or entry points) for Debug and Release build configurations.</span></span>

<span data-ttu-id="1c657-169">Docker Compose'in Visual Studio'da çalışma seçeneği ni görüntüleyebilmek için docker projesinin başlangıç projesi olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="1c657-169">For Docker Compose to display an option to run in Visual Studio, the docker project must be the startup project.</span></span>

### <a name="service-fabric"></a><span data-ttu-id="1c657-170">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="1c657-170">Service Fabric</span></span>

<span data-ttu-id="1c657-171">Temel [Önkoşullara](#prerequisites)ek olarak, [Service Fabric](/azure/service-fabric/) orkestrasyon çözümü aşağıdaki ön koşulları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="1c657-171">In addition to the base [Prerequisites](#prerequisites), the [Service Fabric](/azure/service-fabric/) orchestration solution demands the following prerequisites:</span></span>

* <span data-ttu-id="1c657-172">[Microsoft Azure Hizmet Kumaşı SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) sürüm 2.6 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="1c657-172">[Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) version 2.6 or later</span></span>
* <span data-ttu-id="1c657-173">Visual Studio'nun **Azure Geliştirme** iş yükü</span><span class="sxs-lookup"><span data-stu-id="1c657-173">Visual Studio's **Azure Development** workload</span></span>

<span data-ttu-id="1c657-174">Service Fabric, Windows'daki yerel geliştirme kümesinde Linux kapsayıcıları çalıştırmayı desteklemez.</span><span class="sxs-lookup"><span data-stu-id="1c657-174">Service Fabric doesn't support running Linux containers in the local development cluster on Windows.</span></span> <span data-ttu-id="1c657-175">Proje zaten bir Linux kapsayıcısı kullanıyorsa, Visual Studio Windows kapsayıcılarına geçmek ister.</span><span class="sxs-lookup"><span data-stu-id="1c657-175">If the project is already using a Linux container, Visual Studio prompts to switch to Windows containers.</span></span>

<span data-ttu-id="1c657-176">Visual Studio Konteyner Araçları aşağıdaki görevleri yapar:</span><span class="sxs-lookup"><span data-stu-id="1c657-176">The Visual Studio Container Tools do the following tasks:</span></span>

* <span data-ttu-id="1c657-177">Çözüme \* &lt;&gt;project_name Uygulama\* **Hizmeti Kumaş Uygulaması** projesi ekler.</span><span class="sxs-lookup"><span data-stu-id="1c657-177">Adds a *&lt;project_name&gt;Application* **Service Fabric Application** project to the solution.</span></span>
* <span data-ttu-id="1c657-178">ASP.NET Core projesine *dockerdosyası* ve *.dockerignore* dosyası ekler.</span><span class="sxs-lookup"><span data-stu-id="1c657-178">Adds a *Dockerfile* and a *.dockerignore* file to the ASP.NET Core project.</span></span> <span data-ttu-id="1c657-179">Bir *Dockerdosyası* ASP.NET Core projesinde zaten varsa, *dockerfile.original*olarak yeniden adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="1c657-179">If a *Dockerfile* already exists in the ASP.NET Core project, it's renamed to *Dockerfile.original*.</span></span> <span data-ttu-id="1c657-180">Aşağıdakilere benzer yeni bir *Dockerfile*oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="1c657-180">A new *Dockerfile*, similar to the following, is created:</span></span>

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* <span data-ttu-id="1c657-181">ASP.NET `<IsServiceFabricServiceProject>` Core projesinin *.csproj* dosyasına bir öğe ekler:</span><span class="sxs-lookup"><span data-stu-id="1c657-181">Adds an `<IsServiceFabricServiceProject>` element to the ASP.NET Core project's *.csproj* file:</span></span>

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* <span data-ttu-id="1c657-182">ASP.NET Core projesine bir *PackageRoot* klasörü ekler.</span><span class="sxs-lookup"><span data-stu-id="1c657-182">Adds a *PackageRoot* folder to the ASP.NET Core project.</span></span> <span data-ttu-id="1c657-183">Klasör, yeni hizmetin hizmet bildirimini ve ayarlarını içerir.</span><span class="sxs-lookup"><span data-stu-id="1c657-183">The folder includes the service manifest and settings for the new service.</span></span>

<span data-ttu-id="1c657-184">Daha fazla bilgi için windows [kapsayıcısında bir .NET uygulamasını Azure Hizmet Kumaşı'na dağıt'a](/azure/service-fabric/service-fabric-host-app-in-a-container)bakın.</span><span class="sxs-lookup"><span data-stu-id="1c657-184">For more information, see [Deploy a .NET app in a Windows container to Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).</span></span>

## <a name="debug"></a><span data-ttu-id="1c657-185">Hata ayıklama</span><span class="sxs-lookup"><span data-stu-id="1c657-185">Debug</span></span>

<span data-ttu-id="1c657-186">Araç çubuğundaki hata ayıklama açılır tarihinden **Docker'ı** seçin ve uygulamayı hata ayıklamaya başlayın.</span><span class="sxs-lookup"><span data-stu-id="1c657-186">Select **Docker** from the debug drop-down in the toolbar, and start debugging the app.</span></span> <span data-ttu-id="1c657-187">**Çıkış** penceresinin **Docker** görünümü aşağıdaki eylemleri gösterir:</span><span class="sxs-lookup"><span data-stu-id="1c657-187">The **Docker** view of the **Output** window shows the following actions taking place:</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="1c657-188">*Microsoft/dotnet* runtime görüntüsünün *2.1-aspnetcore-runtime* etiketi elde edilir (önbellekte zaten değilse).</span><span class="sxs-lookup"><span data-stu-id="1c657-188">The *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* runtime image is acquired (if not already in the cache).</span></span> <span data-ttu-id="1c657-189">Görüntü, Core ve .NET Core çalışma saatleri ve ilişkili kitaplıklar ASP.NET yükler.</span><span class="sxs-lookup"><span data-stu-id="1c657-189">The image installs the ASP.NET Core and .NET Core runtimes and associated libraries.</span></span> <span data-ttu-id="1c657-190">Core uygulamalarını üretimde çalıştırmak için optimize ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="1c657-190">It's optimized for running ASP.NET Core apps in production.</span></span>
* <span data-ttu-id="1c657-191">`ASPNETCORE_ENVIRONMENT` Ortam değişkeni kapsayıcı `Development` içinde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="1c657-191">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="1c657-192">Dinamik olarak atanmış iki bağlantı noktası açığa alınır: biri HTTP, diğeri HTTPS için.</span><span class="sxs-lookup"><span data-stu-id="1c657-192">Two dynamically assigned ports are exposed: one for HTTP and one for HTTPS.</span></span> <span data-ttu-id="1c657-193">Localhost atanan bağlantı noktası `docker ps` komutu ile sorgulanabilir.</span><span class="sxs-lookup"><span data-stu-id="1c657-193">The port assigned to localhost can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="1c657-194">Uygulama kapsayıcıya kopyalanır.</span><span class="sxs-lookup"><span data-stu-id="1c657-194">The app is copied to the container.</span></span>
* <span data-ttu-id="1c657-195">Varsayılan tarayıcı, dinamik olarak atanan bağlantı noktasını kullanarak kapsayıcıya bağlı hata ayıklama ile başlatılır.</span><span class="sxs-lookup"><span data-stu-id="1c657-195">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="1c657-196">Uygulamanın ortaya çıkan Docker görüntüsü *dev*olarak etiketlenir.</span><span class="sxs-lookup"><span data-stu-id="1c657-196">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="1c657-197">Görüntü, *microsoft/dotnet* taban görüntüsünün *2.1-aspnetcore-runtime* etiketine dayanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="1c657-197">The image is based on the *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* base image.</span></span> <span data-ttu-id="1c657-198">Paketi `docker images` **Yöneticisi Konsolu** (PMC) penceresinde komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1c657-198">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="1c657-199">Makinedeki görüntüler görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="1c657-199">The images on the machine are displayed:</span></span>

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* <span data-ttu-id="1c657-200">*Microsoft/aspnetcore* çalışma zamanı görüntüsü elde edilir (önbellekte zaten yoksa).</span><span class="sxs-lookup"><span data-stu-id="1c657-200">The *microsoft/aspnetcore* runtime image is acquired (if not already in the cache).</span></span>
* <span data-ttu-id="1c657-201">`ASPNETCORE_ENVIRONMENT` Ortam değişkeni kapsayıcı `Development` içinde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="1c657-201">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="1c657-202">Port 80 açığa ve localhost için dinamik olarak atanmış bir bağlantı noktasına eşlenir.</span><span class="sxs-lookup"><span data-stu-id="1c657-202">Port 80 is exposed and mapped to a dynamically assigned port for localhost.</span></span> <span data-ttu-id="1c657-203">Bağlantı noktası Docker ana bilgisayar tarafından belirlenir ve `docker ps` komutu ile sorgulanabilir.</span><span class="sxs-lookup"><span data-stu-id="1c657-203">The port is determined by the Docker host and can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="1c657-204">Uygulama kapsayıcıya kopyalanır.</span><span class="sxs-lookup"><span data-stu-id="1c657-204">The app is copied to the container.</span></span>
* <span data-ttu-id="1c657-205">Varsayılan tarayıcı, dinamik olarak atanan bağlantı noktasını kullanarak kapsayıcıya bağlı hata ayıklama ile başlatılır.</span><span class="sxs-lookup"><span data-stu-id="1c657-205">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="1c657-206">Uygulamanın ortaya çıkan Docker görüntüsü *dev*olarak etiketlenir.</span><span class="sxs-lookup"><span data-stu-id="1c657-206">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="1c657-207">Görüntü *microsoft/aspnetcore* temel resmine dayanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="1c657-207">The image is based on the *microsoft/aspnetcore* base image.</span></span> <span data-ttu-id="1c657-208">Paketi `docker images` **Yöneticisi Konsolu** (PMC) penceresinde komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1c657-208">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="1c657-209">Makinedeki görüntüler görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="1c657-209">The images on the machine are displayed:</span></span>

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="1c657-210">**Hata ayıklama** yapılandırmaları yinelemeli deneyimi sağlamak için ses montajını kullandığından, *dev* görüntüde uygulama içeriği yok.</span><span class="sxs-lookup"><span data-stu-id="1c657-210">The *dev* image lacks the app contents, as **Debug** configurations use volume mounting to provide the iterative experience.</span></span> <span data-ttu-id="1c657-211">Görüntüyü itmek için **Release** yapılandırmasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="1c657-211">To push an image, use the **Release** configuration.</span></span>

<span data-ttu-id="1c657-212">PMC'de komutu çalıştırın. `docker ps`</span><span class="sxs-lookup"><span data-stu-id="1c657-212">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="1c657-213">Uygulamanın kapsayıcıyı kullanarak çalıştığını fark edin:</span><span class="sxs-lookup"><span data-stu-id="1c657-213">Notice the app is running using the container:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a><span data-ttu-id="1c657-214">Edin ve devam edin</span><span class="sxs-lookup"><span data-stu-id="1c657-214">Edit and continue</span></span>

<span data-ttu-id="1c657-215">Statik dosyalardaki değişiklikler ve Razor görünümleri derleme adımına gerek kalmadan otomatik olarak güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="1c657-215">Changes to static files and Razor views are automatically updated without the need for a compilation step.</span></span> <span data-ttu-id="1c657-216">Güncelleştirmeyi görüntülemek için tarayıcıyı değiştirin, kaydedin ve yenileyin.</span><span class="sxs-lookup"><span data-stu-id="1c657-216">Make the change, save, and refresh the browser to view the update.</span></span>

<span data-ttu-id="1c657-217">Kod dosyası modifikasyonları derleme ve kapsayıcı içinde Kerkenez bir yeniden başlatma gerektirir.</span><span class="sxs-lookup"><span data-stu-id="1c657-217">Code file modifications require compilation and a restart of Kestrel within the container.</span></span> <span data-ttu-id="1c657-218">Değişikliği yaptıktan sonra, `CTRL+F5` işlemi gerçekleştirmek ve uygulamayı kapsayıcı içinde başlatmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="1c657-218">After making the change, use `CTRL+F5` to perform the process and start the app within the container.</span></span> <span data-ttu-id="1c657-219">Docker konteyneri yeniden oluşturulmamaveya durdurulmaz.</span><span class="sxs-lookup"><span data-stu-id="1c657-219">The Docker container isn't rebuilt or stopped.</span></span> <span data-ttu-id="1c657-220">PMC'de komutu çalıştırın. `docker ps`</span><span class="sxs-lookup"><span data-stu-id="1c657-220">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="1c657-221">Orijinal kapsayıcının 10 dakika önce hala çalışmaya devam edebildiğinize dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="1c657-221">Notice the original container is still running as of 10 minutes ago:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a><span data-ttu-id="1c657-222">Docker resimlerini yayımla</span><span class="sxs-lookup"><span data-stu-id="1c657-222">Publish Docker images</span></span>

<span data-ttu-id="1c657-223">Uygulamanın geliştirme ve hata ayıklama döngüsü tamamlandıktan sonra, Visual Studio Container Tools uygulamanın üretim görüntüsünü oluşturmaya yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="1c657-223">Once the develop and debug cycle of the app is completed, the Visual Studio Container Tools assist in creating the production image of the app.</span></span> <span data-ttu-id="1c657-224">Yapılandırma açılır düşüşünü **Release** olarak değiştirin ve uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1c657-224">Change the configuration drop-down to **Release** and build the app.</span></span> <span data-ttu-id="1c657-225">Araç, Docker Hub'dan derleme/yayımlama görüntüsünü (önbellekte zaten değilse) edinir.</span><span class="sxs-lookup"><span data-stu-id="1c657-225">The tooling acquires the compile/publish image from Docker Hub (if not already in the cache).</span></span> <span data-ttu-id="1c657-226">Bir görüntü, özel kayıt defterine veya Docker Hub'a itilebilen *en son* etiketle üretilir.</span><span class="sxs-lookup"><span data-stu-id="1c657-226">An image is produced with the *latest* tag, which can be pushed to the private registry or Docker Hub.</span></span>

<span data-ttu-id="1c657-227">Görüntülerin `docker images` listesini görmek için PMC'deki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1c657-227">Run the `docker images` command in PMC to see the list of images.</span></span> <span data-ttu-id="1c657-228">Aşağıdakilere benzer çıktı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="1c657-228">Output similar to the following is displayed:</span></span>

::: moniker range=">= aspnetcore-2.1"

```console
REPOSITORY        TAG                     IMAGE ID      CREATED             SIZE
hellodockertools  latest                  e3984a64230c  About a minute ago  258MB
hellodockertools  dev                     d72ce0f1dfe7  4 minutes ago       255MB
microsoft/dotnet  2.1-sdk                 9e243db15f91  6 days ago          1.7GB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago          255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

```console
REPOSITORY                  TAG     IMAGE ID      CREATED         SIZE
hellodockertools            latest  cd28f0d4abbd  12 seconds ago  349MB
hellodockertools            dev     5fafe5d1ad5b  23 minutes ago  347MB
microsoft/aspnetcore-build  2.0     7fed40fbb647  13 days ago     2.02GB
microsoft/aspnetcore        2.0     c69d39472da9  13 days ago     347MB
```

<span data-ttu-id="1c657-229">`microsoft/aspnetcore-build` Önceki `microsoft/aspnetcore` çıktıda listelenen görüntüler ve görüntüler `microsoft/dotnet` .NET Core 2.1 olarak görüntülerle değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="1c657-229">The `microsoft/aspnetcore-build` and `microsoft/aspnetcore` images listed in the preceding output are replaced with `microsoft/dotnet` images as of .NET Core 2.1.</span></span> <span data-ttu-id="1c657-230">Daha fazla bilgi için [Docker depoları geçiş duyurusuna](https://github.com/aspnet/Announcements/issues/298)bakın.</span><span class="sxs-lookup"><span data-stu-id="1c657-230">For more information, see [the Docker repositories migration announcement](https://github.com/aspnet/Announcements/issues/298).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="1c657-231">Komut, `docker images` \* \<hiçbir>\* (yukarıda listelenmemiş) olarak tanımlanan depo adları ve etiketleri içeren ara görüntüleri döndürür.</span><span class="sxs-lookup"><span data-stu-id="1c657-231">The `docker images` command returns intermediary images with repository names and tags identified as *\<none>* (not listed above).</span></span> <span data-ttu-id="1c657-232">Bu isimsiz görüntüler [çok aşamalı inşa](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile*tarafından üretilmektedir.</span><span class="sxs-lookup"><span data-stu-id="1c657-232">These unnamed images are produced by the [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile*.</span></span> <span data-ttu-id="1c657-233">Son görüntüyü&mdash;oluşturma verimliliğini artırırlar, yalnızca değişiklikler meydana geldiğinde gerekli katmanlar yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="1c657-233">They improve the efficiency of building the final image&mdash;only the necessary layers are rebuilt when changes occur.</span></span> <span data-ttu-id="1c657-234">Ara görüntülere artık gerek kalmadığında docker [rmi](https://docs.docker.com/engine/reference/commandline/rmi/) komutunu kullanarak silin.</span><span class="sxs-lookup"><span data-stu-id="1c657-234">When the intermediary images are no longer needed, delete them using the [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) command.</span></span>

<span data-ttu-id="1c657-235">*Dev* görüntüye kıyasla üretim veya sürüm görüntüsünün boyutu daha küçük olması beklentisi olabilir.</span><span class="sxs-lookup"><span data-stu-id="1c657-235">There may be an expectation for the production or release image to be smaller in size by comparison to the *dev* image.</span></span> <span data-ttu-id="1c657-236">Ses hataayıklaması ve uygulama, birim eşleme nedeniyle konteynerin içinde değil, yerel makineden çalışıyorlardı.</span><span class="sxs-lookup"><span data-stu-id="1c657-236">Because of the volume mapping, the debugger and app were running from the local machine and not within the container.</span></span> <span data-ttu-id="1c657-237">En *son* görüntü, uygulamayı ana bilgisayarda çalıştırmak için gerekli uygulama kodunu paketlemiştir.</span><span class="sxs-lookup"><span data-stu-id="1c657-237">The *latest* image has packaged the necessary app code to run the app on a host machine.</span></span> <span data-ttu-id="1c657-238">Bu nedenle, delta uygulama kodunun boyutudur.</span><span class="sxs-lookup"><span data-stu-id="1c657-238">Therefore, the delta is the size of the app code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1c657-239">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1c657-239">Additional resources</span></span>

* [<span data-ttu-id="1c657-240">Visual Studio ile konteyner geliştirme</span><span class="sxs-lookup"><span data-stu-id="1c657-240">Container development with Visual Studio</span></span>](/visualstudio/containers)
* [<span data-ttu-id="1c657-241">Azure Hizmet Kumaşı: Geliştirme ortamınızı hazırlayın</span><span class="sxs-lookup"><span data-stu-id="1c657-241">Azure Service Fabric: Prepare your development environment</span></span>](/azure/service-fabric/service-fabric-get-started)
* [<span data-ttu-id="1c657-242">Bir .NET uygulamasını Windows kapsayıcısında Azure Hizmet Kumaşı'na dağıtma</span><span class="sxs-lookup"><span data-stu-id="1c657-242">Deploy a .NET app in a Windows container to Azure Service Fabric</span></span>](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [<span data-ttu-id="1c657-243">Docker ile Visual Studio geliştirme sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="1c657-243">Troubleshoot Visual Studio development with Docker</span></span>](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [<span data-ttu-id="1c657-244">Görsel Stüdyo Konteyner Araçları GitHub deposu</span><span class="sxs-lookup"><span data-stu-id="1c657-244">Visual Studio Container Tools GitHub repository</span></span>](https://github.com/Microsoft/DockerTools)
