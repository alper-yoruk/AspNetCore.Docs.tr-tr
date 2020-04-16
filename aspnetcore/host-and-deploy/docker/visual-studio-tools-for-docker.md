---
title: ASP.NET Core ile Visual Studio Konteyner Araçları
author: spboyer
description: ASP.NET Core uygulamasını kapsayıcı hale getirmek için Visual Studio aracını ve Windows için Docker'ı nasıl kullanacağınızı öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: ba79f0af8192ad9e8b263d4304ccc0df36c50f00
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440759"
---
# <a name="visual-studio-container-tools-with-aspnet-core"></a>ASP.NET Core ile Visual Studio Konteyner Araçları

Visual Studio 2017 ve sonraki sürümleri ,NET Core'u hedefleyen konteynerleştirilmiş ASP.NET Core uygulamalarını oluşturmayı, hata ayıklamayı ve çalıştırmayı destekler. Hem Windows hem de Linux kapsayıcıları desteklenir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Ön koşullar

* [Windows için Docker](https://docs.docker.com/docker-for-windows/install/)
* **.NET Core çapraz platform geliştirme** iş yükü ile Visual Studio [2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="installation-and-setup"></a>Kurulum ve kurulum

Docker yüklemesi için, yüklemeden önce Docker for Windows'daki bilgileri gözden [geçirin: Yüklemeden önce bilinmesi gerekenler.](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install) Ardından, [Windows için Docker'ı](https://docs.docker.com/docker-for-windows/install/)yükleyin.

Windows için Docker'daki **[Paylaşılan Sürücüler,](https://docs.docker.com/docker-for-windows/#shared-drives)** ses düzeyini eşleme ve hata ayıklamayı destekleyecek şekilde yapılandırılmalıdır. Sistem Tepsisi'nin Docker simgesine sağ tıklayın, **Ayarlar'ı**seçin ve **Paylaşılan Sürücüler'i**seçin. Docker'ın dosyaları depoladığı sürücüyü seçin. **Uygula**’ya tıklayın.

![Kapsayıcılar için yerel C sürücü paylaşımını seçmek için iletişim kutusu](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> Visual Studio 2017 sürümleri 15.6 ve paylaşılan **sürücüler** yapılandırılmadı sonra istemi.

## <a name="add-a-project-to-a-docker-container"></a>Docker kapsayıcısına proje ekleme

Bir ASP.NET Core projesini kapsayıcı hale getirmek için projenin .NET Core'u hedeflemesi gerekir. Hem Linux hem de Windows kapsayıcıları desteklenir.

Bir projeye Docker desteği eklerken, bir Windows veya Linux kapsayıcısı seçin. Docker ana bilgisayar aynı kapsayıcı türünü çalıştırıyor olmalıdır. Çalışan Docker örneğinde kapsayıcı türünü değiştirmek için Sistem Tepsisi Docker simgesine sağ tıklayın ve **Windows kapsayıcılarına geçiş'i seçin...** veya **Linux kapsayıcılarına geçiş...**.

### <a name="new-app"></a>Yeni uygulama

**ASP.NET Core Web Application** proje şablonları ile yeni bir uygulama oluştururken, Docker Destek onay kutusunu **etkinleştir'i** seçin:

![Docker Destek onay kutusunu etkinleştir](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

Hedef çerçeve .NET Core ise, **işletim sistemi** açılır bir kapsayıcı türü seçimine olanak tanır.

### <a name="existing-app"></a>Mevcut uygulama

.NET Core'u hedefleyen ASP.NET Core projeleri için, takım lama yoluyla Docker desteği eklemek için iki seçenek vardır. Projeyi Visual Studio'da açın ve aşağıdaki seçeneklerden birini seçin:

* **Proje** menüsünden **Docker Desteği'ni** seçin.
* **Solution Explorer'da** projeye sağ tıklayın ve**Docker Desteği** **Ekle'yi** > seçin.

Visual Studio Container Tools, .NET Framework'u hedefleyen mevcut bir ASP.NET Core projesine Docker'ın eklenmesini desteklemez.

## <a name="dockerfile-overview"></a>Dockerfile'a genel bakış

Bir *Dockerfile*, son docker görüntü oluşturmak için reçete, proje köküne eklenir. İçindeki komutların anlaşılması için [Dockerfile başvurusuna](https://docs.docker.com/engine/reference/builder/) bakın. Bu özel *Dockerfile* dört farklı, adlı yapı aşamaları ile [çok aşamalı](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) bir yapı kullanır:

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

Önceki *Dockerfile* [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/) resmine dayanmaktadır. Bu temel görüntü, Core çalışma zamanı ve NuGet paketleriASP.NET içerir. Paketler başlangıç performansını artırmak için tam zamanında (JIT) derlenmiştir.

Yeni proje iletişim kutusunun HTTPS için **Yapılandırma** onay kutusu işaretlendiğinde, *Dockerfile* iki bağlantı noktasını ortaya çıkarır. Bir bağlantı noktası HTTP trafiği için kullanılır; diğer bağlantı noktası HTTPS için kullanılır. Onay kutusu işaretlenmezse, HTTP trafiği için tek bir bağlantı noktası (80) açığa çıkarır.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

Önceki *Dockerfile* [microsoft/ aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) görüntü dayanmaktadır. Bu temel görüntü, başlangıç performansını artırmak için derlenen tam zamanında (JIT) ASP.NET Core NuGet paketlerini içerir.

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a>Bir uygulamaya konteyner orkestrator desteği ekleme

Visual Studio 2017 sürümleri 15.7 veya önceki destek [Docker Compose](https://docs.docker.com/compose/overview/) tek konteyner orkestrasyon çözümü olarak. Docker Compose **yapıları, Ekle** > **Docker Desteği**ile eklenir.

Visual Studio 2017 sürümleri 15.8 veya daha sonra sadece talimat verildiğinde bir orkestrasyon çözümü ekleyin. **Solution Explorer'da** projeye sağ tıklayın ve**Kapsayıcı Orkestrator Desteği** **Ekle'yi** > seçin. İki farklı seçenek sunulmaktadır: [Docker Compose](#docker-compose) ve [Servis Kumaşı.](#service-fabric)

### <a name="docker-compose"></a>Docker Oluştur

Visual Studio Container Tools aşağıdaki dosyalarla çözüme bir *docker-beste* projesi ekler:

* *docker-compose.dcproj* &ndash; Projeyi temsil eden dosya. Kullanılacak `<DockerTargetOS>` işletim sistemi belirten bir öğe içerir.
* *.dockerignore* &ndash; Yapı bağlamı oluştururken dışlanması gereken dosya ve dizin desenleri listeler.
* *docker-compose.yml* &ndash; Temel [Docker Compose](https://docs.docker.com/compose/overview/) dosyası, sırasıyla, sırasıyla, `docker-compose run`birlikte oluşturulmuş ve çalıştırılan `docker-compose build` görüntülerin toplanmasını tanımlamak için kullanılır.
* *docker-compose.override.yml* &ndash; İsteğe bağlı bir dosya, Docker Compose tarafından okunan, hizmetler için yapılandırma geçersiz kılar. Visual Studio `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` bu dosyaları birleştirmek için yürütür.

*Docker-compose.yml* dosyası, proje çalıştığında oluşturulan görüntünün adına başvurur:

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

Önceki örnekte, `image: hellodockertools` uygulama Hata `hellodockertools:dev` **Ayıklama** modunda çalıştığında görüntüyü oluşturur. Uygulama `hellodockertools:latest` **Sürüm** modunda çalıştığında görüntü oluşturulur.

Görüntü kayıt defterine itilirse, görüntü `dockerhubusername/hellodockertools`adını Docker [Hub](https://hub.docker.com/) kullanıcı adı (örneğin, ) ile önek. Alternatif olarak, yapılandırmaya bağlı olarak resim adını özel `privateregistry.domain.com/hellodockertools`kayıt defteri URL'sini (örneğin) içerecek şekilde değiştirin.

Yapı yapılandırmasını temel alan farklı davranışlar (örneğin Hata Ayıklama veya Sürüm), yapılandırmaya özgü *docker-compose* dosyaları ekleyin. Dosyalar yapı yapılandırmasına göre adlandırılmalı (örneğin, *docker-compose.vs.debug.yml* ve *docker-compose.vs.release.yml)* ve *docker-compose-override.yml* dosyasıyla aynı konuma yerleştirilmelidir. 

Yapılandırmaya özgü geçersiz kılma dosyalarını kullanarak Hata Ayıklama ve Sürüm yapı yapılandırmaları için farklı yapılandırma ayarlarını (ortam değişkenleri veya giriş noktaları gibi) belirtebilirsiniz.

Docker Compose'in Visual Studio'da çalışma seçeneği ni görüntüleyebilmek için docker projesinin başlangıç projesi olması gerekir.

### <a name="service-fabric"></a>Service Fabric

Temel [Önkoşullara](#prerequisites)ek olarak, [Service Fabric](/azure/service-fabric/) orkestrasyon çözümü aşağıdaki ön koşulları gerektirir:

* [Microsoft Azure Hizmet Kumaşı SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) sürüm 2.6 veya sonrası
* Visual Studio'nun **Azure Geliştirme** iş yükü

Service Fabric, Windows'daki yerel geliştirme kümesinde Linux kapsayıcıları çalıştırmayı desteklemez. Proje zaten bir Linux kapsayıcısı kullanıyorsa, Visual Studio Windows kapsayıcılarına geçmek ister.

Visual Studio Konteyner Araçları aşağıdaki görevleri yapar:

* Çözüme * &lt;&gt;project_name Uygulama* **Hizmeti Kumaş Uygulaması** projesi ekler.
* ASP.NET Core projesine *dockerdosyası* ve *.dockerignore* dosyası ekler. Bir *Dockerdosyası* ASP.NET Core projesinde zaten varsa, *dockerfile.original*olarak yeniden adlandırılır. Aşağıdakilere benzer yeni bir *Dockerfile*oluşturulur:

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* ASP.NET `<IsServiceFabricServiceProject>` Core projesinin *.csproj* dosyasına bir öğe ekler:

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* ASP.NET Core projesine bir *PackageRoot* klasörü ekler. Klasör, yeni hizmetin hizmet bildirimini ve ayarlarını içerir.

Daha fazla bilgi için windows [kapsayıcısında bir .NET uygulamasını Azure Hizmet Kumaşı'na dağıt'a](/azure/service-fabric/service-fabric-host-app-in-a-container)bakın.

## <a name="debug"></a>Hata ayıklama

Araç çubuğundaki hata ayıklama açılır tarihinden **Docker'ı** seçin ve uygulamayı hata ayıklamaya başlayın. **Çıkış** penceresinin **Docker** görünümü aşağıdaki eylemleri gösterir:

::: moniker range=">= aspnetcore-2.1"

* *Microsoft/dotnet* runtime görüntüsünün *2.1-aspnetcore-runtime* etiketi elde edilir (önbellekte zaten değilse). Görüntü, Core ve .NET Core çalışma saatleri ve ilişkili kitaplıklar ASP.NET yükler. Core uygulamalarını üretimde çalıştırmak için optimize ASP.NET.
* `ASPNETCORE_ENVIRONMENT` Ortam değişkeni kapsayıcı `Development` içinde ayarlanır.
* Dinamik olarak atanmış iki bağlantı noktası açığa alınır: biri HTTP, diğeri HTTPS için. Localhost atanan bağlantı noktası `docker ps` komutu ile sorgulanabilir.
* Uygulama kapsayıcıya kopyalanır.
* Varsayılan tarayıcı, dinamik olarak atanan bağlantı noktasını kullanarak kapsayıcıya bağlı hata ayıklama ile başlatılır.

Uygulamanın ortaya çıkan Docker görüntüsü *dev*olarak etiketlenir. Görüntü, *microsoft/dotnet* taban görüntüsünün *2.1-aspnetcore-runtime* etiketine dayanmaktadır. Paketi `docker images` **Yöneticisi Konsolu** (PMC) penceresinde komutu çalıştırın. Makinedeki görüntüler görüntülenir:

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* *Microsoft/aspnetcore* çalışma zamanı görüntüsü elde edilir (önbellekte zaten yoksa).
* `ASPNETCORE_ENVIRONMENT` Ortam değişkeni kapsayıcı `Development` içinde ayarlanır.
* Port 80 açığa ve localhost için dinamik olarak atanmış bir bağlantı noktasına eşlenir. Bağlantı noktası Docker ana bilgisayar tarafından belirlenir ve `docker ps` komutu ile sorgulanabilir.
* Uygulama kapsayıcıya kopyalanır.
* Varsayılan tarayıcı, dinamik olarak atanan bağlantı noktasını kullanarak kapsayıcıya bağlı hata ayıklama ile başlatılır.

Uygulamanın ortaya çıkan Docker görüntüsü *dev*olarak etiketlenir. Görüntü *microsoft/aspnetcore* temel resmine dayanmaktadır. Paketi `docker images` **Yöneticisi Konsolu** (PMC) penceresinde komutu çalıştırın. Makinedeki görüntüler görüntülenir:

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> **Hata ayıklama** yapılandırmaları yinelemeli deneyimi sağlamak için ses montajını kullandığından, *dev* görüntüde uygulama içeriği yok. Görüntüyü itmek için **Release** yapılandırmasını kullanın.

PMC'de komutu çalıştırın. `docker ps` Uygulamanın kapsayıcıyı kullanarak çalıştığını fark edin:

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a>Edin ve devam edin

Statik dosyalardaki değişiklikler ve Razor görünümleri derleme adımına gerek kalmadan otomatik olarak güncelleştirilir. Güncelleştirmeyi görüntülemek için tarayıcıyı değiştirin, kaydedin ve yenileyin.

Kod dosyası modifikasyonları derleme ve kapsayıcı içinde Kerkenez bir yeniden başlatma gerektirir. Değişikliği yaptıktan sonra, `CTRL+F5` işlemi gerçekleştirmek ve uygulamayı kapsayıcı içinde başlatmak için kullanın. Docker konteyneri yeniden oluşturulmamaveya durdurulmaz. PMC'de komutu çalıştırın. `docker ps` Orijinal kapsayıcının 10 dakika önce hala çalışmaya devam edebildiğinize dikkat edin:

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a>Docker resimlerini yayımla

Uygulamanın geliştirme ve hata ayıklama döngüsü tamamlandıktan sonra, Visual Studio Container Tools uygulamanın üretim görüntüsünü oluşturmaya yardımcı olur. Yapılandırma açılır düşüşünü **Release** olarak değiştirin ve uygulamayı oluşturun. Araç, Docker Hub'dan derleme/yayımlama görüntüsünü (önbellekte zaten değilse) edinir. Bir görüntü, özel kayıt defterine veya Docker Hub'a itilebilen *en son* etiketle üretilir.

Görüntülerin `docker images` listesini görmek için PMC'deki komutu çalıştırın. Aşağıdakilere benzer çıktı görüntülenir:

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

`microsoft/aspnetcore-build` Önceki `microsoft/aspnetcore` çıktıda listelenen görüntüler ve görüntüler `microsoft/dotnet` .NET Core 2.1 olarak görüntülerle değiştirilir. Daha fazla bilgi için [Docker depoları geçiş duyurusuna](https://github.com/aspnet/Announcements/issues/298)bakın.

::: moniker-end

> [!NOTE]
> Komut, `docker images` * \<hiçbir>* (yukarıda listelenmemiş) olarak tanımlanan depo adları ve etiketleri içeren ara görüntüleri döndürür. Bu isimsiz görüntüler [çok aşamalı inşa](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile*tarafından üretilmektedir. Son görüntüyü&mdash;oluşturma verimliliğini artırırlar, yalnızca değişiklikler meydana geldiğinde gerekli katmanlar yeniden oluşturulur. Ara görüntülere artık gerek kalmadığında docker [rmi](https://docs.docker.com/engine/reference/commandline/rmi/) komutunu kullanarak silin.

*Dev* görüntüye kıyasla üretim veya sürüm görüntüsünün boyutu daha küçük olması beklentisi olabilir. Ses hataayıklaması ve uygulama, birim eşleme nedeniyle konteynerin içinde değil, yerel makineden çalışıyorlardı. En *son* görüntü, uygulamayı ana bilgisayarda çalıştırmak için gerekli uygulama kodunu paketlemiştir. Bu nedenle, delta uygulama kodunun boyutudur.

## <a name="additional-resources"></a>Ek kaynaklar

* [Visual Studio ile konteyner geliştirme](/visualstudio/containers)
* [Azure Hizmet Kumaşı: Geliştirme ortamınızı hazırlayın](/azure/service-fabric/service-fabric-get-started)
* [Bir .NET uygulamasını Windows kapsayıcısında Azure Hizmet Kumaşı'na dağıtma](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [Docker ile Visual Studio geliştirme sorunlarını giderme](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [Görsel Stüdyo Konteyner Araçları GitHub deposu](https://github.com/Microsoft/DockerTools)
* [Docker ve küçük kaplar kullanarak GC](xref:performance/memory#sc)
