---
title: ASP.NET Core ile Visual Studio kapsayıcı araçları
author: spboyer
description: Visual Studio Araçları 'nı kullanmayı öğrenin ve bir ASP.NET Core uygulamasını kapsayıleştirmek için Docker for Windows.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: 391cc18e499cdfaa67beeced382fd53b16a668a2
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84105226"
---
# <a name="visual-studio-container-tools-with-aspnet-core"></a>ASP.NET Core ile Visual Studio kapsayıcı araçları

Visual Studio 2017 ve üzeri sürümleri, .NET Core 'u hedefleyen ASP.NET Core Kapsayıcılı uygulama oluşturma, hata ayıklama ve çalıştırma desteği sağlar. Hem Windows hem de Linux kapsayıcıları desteklenir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Önkoşullar

* [Docker for Windows](https://docs.docker.com/docker-for-windows/install/)
* **.NET Core platformlar arası geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="installation-and-setup"></a>Yükleme ve kurulum

Docker yüklemesi için öncelikle [Docker for Windows: yüklemeden önce bilmeniz](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)gereken bilgileri gözden geçirin. Ardından, [Windows Için Docker](https://docs.docker.com/docker-for-windows/install/)'ı yüklersiniz.

Docker for Windows içindeki **[paylaşılan sürücüler](https://docs.docker.com/docker-for-windows/#shared-drives)** , birim eşlemesini ve hata ayıklamayı destekleyecek şekilde yapılandırılmalıdır. Sistem tepsisinin Docker simgesine sağ tıklayın, **Ayarlar**' ı seçin ve **paylaşılan sürücüler**' i seçin. Docker 'ın dosyaları depoladığı sürücüyü seçin. **Uygula**'ya tıklayın.

![Kapsayıcılar için yerel C sürücüsü paylaşımını seçme iletişim kutusu](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> Visual Studio 2017 sürümleri 15,6 ve sonraki sürümler, **paylaşılan sürücülerin** yapılandırılmadığı zaman sorar.

## <a name="add-a-project-to-a-docker-container"></a>Docker kapsayıcısına proje ekleme

ASP.NET Core bir projeyi Kapsayıcılı hale getirmek için, projenin .NET Core 'u hedeflemesi gerekir. Hem Linux hem de Windows kapsayıcıları desteklenir.

Bir projeye Docker desteği eklerken, bir Windows veya Linux kapsayıcısı seçin. Docker ana bilgisayarı aynı kapsayıcı türünü çalıştırıyor olmalıdır. Çalışan Docker örneğindeki kapsayıcı türünü değiştirmek için, sistem tepsisinin Docker simgesine sağ tıklayın ve **Windows kapsayıcılarına geç...** ' i seçin veya **Linux kapsayıcılarına geç..**..

### <a name="new-app"></a>Yeni uygulama

**ASP.NET Core Web uygulaması** proje şablonlarıyla yeni bir uygulama oluştururken **Docker desteğini etkinleştir** onay kutusunu seçin:

![Docker desteğini etkinleştir onay kutusu](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

Hedef çerçeve .NET Core ise, **Işletim sistemi** açılır listesi bir kapsayıcı türü seçimine izin verir.

### <a name="existing-app"></a>Mevcut uygulama

.NET Core 'u hedefleyen ASP.NET Core projeleri için, araç aracılığıyla Docker desteği eklemenin iki seçeneği vardır. Visual Studio 'da projeyi açın ve aşağıdaki seçeneklerden birini belirleyin:

* **Proje** menüsünden **Docker desteği** ' ni seçin.
* **Çözüm Gezgini** projeye sağ tıklayın ve **Add**  >  **Docker desteği**Ekle ' yi seçin.

Visual Studio kapsayıcı araçları, .NET Framework hedefleme ASP.NET Core var olan bir projeye Docker eklemeyi desteklemez.

## <a name="dockerfile-overview"></a>Dockerfile genel bakış

Bir *Dockerfile*, son bir Docker görüntüsü oluşturmak için tarif, proje köküne eklenir. İçindeki komutları anlamak için [Dockerfile başvurusuna](https://docs.docker.com/engine/reference/builder/) bakın. Bu belirli *Dockerfile* , dört farklı, adlandırılmış derleme aşamaları ile [çok aşamalı bir yapı](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) kullanır:

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

Yukarıdaki *Dockerfile* , [Microsoft/DotNet](https://hub.docker.com/r/microsoft/dotnet/) görüntüsünü temel alır. Bu temel görüntü, ASP.NET Core çalışma zamanı ve NuGet paketlerini içerir. Paketler, başlangıç performansını geliştirmek için derlenen tek zaman (JıT).

Yeni proje iletişim kutusunun **https Için Yapılandır** onay kutusu Işaretlendiğinde, *dockerfile* iki bağlantı noktasını kullanıma sunar. HTTP trafiği için bir bağlantı noktası kullanılır; diğer bağlantı noktası HTTPS için kullanılır. Onay kutusu işaretli değilse, HTTP trafiği için tek bir bağlantı noktası (80) gösterilir.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

Yukarıdaki *Dockerfile* , [Microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) görüntüsünü temel alır. Bu temel görüntüde, başlangıç performansını geliştirmek için tek seferlik (JıT) derlenen ASP.NET Core NuGet paketleri bulunur.

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a>Bir uygulamaya kapsayıcı Orchestrator desteği ekleme

Visual Studio 2017, tek kapsayıcı düzenleme çözümü olarak [Docker Compose](https://docs.docker.com/compose/overview/) 15,7 veya önceki bir sürümü destekler. Docker Compose yapıtlar, **Add**  >  **Docker desteği**Ekle aracılığıyla eklenir.

Visual Studio 2017 sürümleri 15,8 veya üzeri bir düzenleme çözümünü yalnızca sorulduğunda ekleyin. **Çözüm Gezgini** ' de projeye sağ tıklayın ve **Add**  >  **kapsayıcı Orchestrator desteği**Ekle ' yi seçin. Aşağıdaki seçenekler kullanılabilir: 

* [Docker Compose](#docker-compose)
* [Service Fabric](#service-fabric)
* [Kubernetes/Held](https://helm.sh/)

### <a name="docker-compose"></a>Docker Compose

Visual Studio kapsayıcı araçları, aşağıdaki dosyalarla çözüme bir *Docker-Compose* projesi ekler:

* *Docker-Compose. dcproj*: projeyi temsil eden dosya. `<DockerTargetOS>`Kullanılacak işletim sistemini belirleyen bir öğesi içerir.
* *. dockerıgnore*: derleme bağlamı oluşturulurken dışlanacak dosya ve Dizin düzenlerini listeler.
* *Docker-Compose. yml*: sırasıyla ve ile oluşturulan ve çalıştıran görüntülerin koleksiyonunu tanımlamak için kullanılan temel [Docker Compose](https://docs.docker.com/compose/overview/) dosyası `docker-compose build` `docker-compose run` .
* *Docker-Compose. override. yml*: isteğe bağlı bir dosya, Docker Compose tarafından okunan hizmetler için yapılandırma geçersiz kılmalarıyla. Visual Studio `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` Bu dosyaları birleştirmek için yürütülür.

*Docker-Compose. yml* dosyası, proje çalışırken oluşturulan görüntünün adına başvurur:

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

Yukarıdaki örnekte, `image: hellodockertools` `hellodockertools:dev` uygulama **hata ayıklama** modunda çalıştırıldığında görüntüyü oluşturur. `hellodockertools:latest`Uygulama **yayın** modunda çalıştığında görüntü oluşturulur.

Görüntü kayıt defterine itilse, görüntü adını [Docker Hub](https://hub.docker.com/) Kullanıcı adı (örneğin,) ile önek yapın `dockerhubusername/hellodockertools` . Alternatif olarak, yapılandırmaya bağlı olarak, görüntü adını özel kayıt defteri URL 'sini (örneğin,) içerecek şekilde değiştirin `privateregistry.domain.com/hellodockertools` .

Yapı yapılandırmasına (örneğin, hata ayıklama veya sürüm) göre farklı davranışlar istiyorsanız, yapılandırmaya özgü *Docker-Compose* dosyaları ekleyin. Dosyalar derleme yapılandırmasına göre adlandırılmalıdır (örneğin, *Docker-Compose. vs. Debug. yıml* ve *Docker-Compose. vs. Release. yıml*) ve *Docker-Compose-override. yıml* dosyasıyla aynı konuma yerleştirildi. 

Yapılandırmaya özgü geçersiz kılma dosyalarını kullanarak, hata ayıklama ve yayın derleme yapılandırmaları için farklı yapılandırma ayarları (ortam değişkenleri veya giriş noktaları gibi) belirtebilirsiniz.

Docker Compose Visual Studio 'da çalıştırma seçeneğini göstermek için, Docker projesinin başlangıç projesi olması gerekir.

### <a name="service-fabric"></a>Service Fabric

Temel [önkoşullara](#prerequisites)ek olarak, [Service Fabric](/azure/service-fabric/) Orchestration çözümü aşağıdaki önkoşulları ister:

* [MICROSOFT Azure SERVICE fabrıc SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) sürüm 2,6 veya üzeri
* Visual Studio 'nun **Azure geliştirme** iş yükü

Service Fabric, Windows 'da yerel geliştirme kümesinde Linux kapsayıcıları çalıştırmayı desteklemez. Proje zaten bir Linux kapsayıcısı kullanıyorsa, Visual Studio Windows kapsayıcılarına geçiş yapmanızı ister.

Visual Studio kapsayıcı araçları aşağıdaki görevleri yapılır:

* Çözüme bir * &lt; project_name &gt; uygulaması* **Service Fabric uygulama** projesi ekler.
* ASP.NET Core projesine bir *dockerfile* ve *. dockerıgnore* dosyası ekler. ASP.NET Core projesinde zaten bir *Dockerfile* varsa, *dockerfile. orijinal*olarak yeniden adlandırılır. Aşağıdakine benzer yeni bir *Dockerfile*oluşturulur:

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* `<IsServiceFabricServiceProject>`ASP.NET Core projenin *. csproj* dosyasına bir öğe ekler:

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* ASP.NET Core projesine bir *PackageRoot* klasörü ekler. Klasör, yeni hizmet için hizmet bildirimini ve ayarlarını içerir.

Daha fazla bilgi için bkz. bir [Windows kapsayıcısında .NET uygulamasını Azure Service Fabric dağıtma](/azure/service-fabric/service-fabric-host-app-in-a-container).

## <a name="debug"></a>Hata ayıklama

Araç çubuğundaki hata ayıklama açılır listesinden **Docker** ' ı seçin ve uygulamada hata ayıklamayı başlatın. **Çıkış** penceresinin **Docker** görünümü aşağıdaki işlemleri gerçekleşirken gösterir:

::: moniker range=">= aspnetcore-2.1"

* *Microsoft/DotNet* çalışma zamanı görüntüsünün *2,1-aspnetcore-Runtime* etiketi elde edilir (zaten önbellekte değilse). Görüntüde ASP.NET Core ve .NET Core çalışma zamanları ile ilişkili Kitaplıklar yüklenir. Üretimde ASP.NET Core uygulamaları çalıştırmak için en iyi duruma getirilmiştir.
* `ASPNETCORE_ENVIRONMENT`Ortam değişkeni kapsayıcı içinde olarak ayarlanır `Development` .
* Dinamik olarak atanan iki bağlantı noktası sunulur: bir HTTP ve diğeri HTTPS için. Localhost 'a atanan bağlantı noktası `docker ps` komutla sorgulanabilir.
* Uygulama kapsayıcıya kopyalanır.
* Varsayılan tarayıcı, dinamik olarak atanan bağlantı noktası kullanılarak kapsayıcıya eklenmiş hata ayıklayıcı ile başlatılır.

Uygulamanın elde edilen Docker görüntüsü *dev*olarak etiketlendi. Görüntü, *Microsoft/DotNet* temel görüntüsünün *2,1-aspnetcore-Runtime* etiketine dayalıdır. `docker images`Komutunu **Paket Yöneticisi konsolu** (PMC) penceresinde çalıştırın. Makinedeki görüntüler görüntülenir:

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* *Microsoft/aspnetcore* çalışma zamanı görüntüsü alındı (zaten önbellekte yoksa).
* `ASPNETCORE_ENVIRONMENT`Ortam değişkeni kapsayıcı içinde olarak ayarlanır `Development` .
* 80 numaralı bağlantı noktası, localhost için dinamik olarak atanmış bir bağlantı noktasıyla gösterilir ve eşleştirilir. Bağlantı noktası Docker ana bilgisayarı tarafından belirlenir ve `docker ps` komutla sorgulanabilir.
* Uygulama kapsayıcıya kopyalanır.
* Varsayılan tarayıcı, dinamik olarak atanan bağlantı noktası kullanılarak kapsayıcıya eklenmiş hata ayıklayıcı ile başlatılır.

Uygulamanın elde edilen Docker görüntüsü *dev*olarak etiketlendi. Görüntü, *Microsoft/aspnetcore* temel görüntüsünü temel alır. `docker images`Komutunu **Paket Yöneticisi konsolu** (PMC) penceresinde çalıştırın. Makinedeki görüntüler görüntülenir:

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> **Hata ayıklama** yapılandırmalarının, yinelemeli deneyim sağlamak için birim bağlama kullanması nedeniyle *geliştirme* görüntüsünde uygulama içeriği eksik. Bir görüntüyü göndermek için **yayın** yapılandırmasını kullanın.

`docker ps`Komutu PMC 'de çalıştırın. Uygulamanın, kapsayıcıyı kullanarak çalıştığını unutmayın:

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a>Düzenle ve devam et

Statik dosya ve görünümlerde yapılan değişiklikler Razor , derleme adımına gerek kalmadan otomatik olarak güncelleştirilir. Güncelleştirmeyi görüntülemek için değişikliği yapıp tarayıcıyı kaydedin ve yenileyin.

Kod dosyası değişiklikleri derleme gerektirir ve kapsayıcı içinde Kestrel yeniden başlatılır. Değişikliği yaptıktan sonra, `CTRL+F5` işlemi gerçekleştirmek ve uygulamayı kapsayıcı içinde başlatmak için öğesini kullanın. Docker kapsayıcısı yeniden derlenmez veya durdurulmaz. `docker ps`Komutu PMC 'de çalıştırın. Özgün kapsayıcının 10 dakikadan önce çalışmaya devam ettiğini fark edin:

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a>Docker görüntülerini yayımlama

Uygulamanın geliştirme ve hata ayıklama döngüsünü tamamladıktan sonra, Visual Studio kapsayıcı araçları uygulamanın üretim görüntüsünü oluşturmaya yardımcı olur. Yapılandırma açılır öğesini değiştirerek uygulamayı **serbest bırakın** ve oluşturun. Araç, Docker Hub 'dan (zaten önbellekte değilse) derleme/yayımlama görüntüsünü alır. Bir görüntü, özel kayıt defterine veya Docker Hub 'ına gönderilebilecek *en son* etiketle oluşturulur.

`docker images`Görüntülerin listesini görmek için bu komutu PMC 'de çalıştırın. Aşağıdakine benzer bir çıktı görüntülenir:

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

`microsoft/aspnetcore-build` `microsoft/aspnetcore` Önceki çıktıda listelenen ve görüntüleri `microsoft/dotnet` .NET Core 2,1 ' deki görüntülerle değiştirilmiştir. Daha fazla bilgi için bkz. [Docker depoları geçiş duyurusu](https://github.com/aspnet/Announcements/issues/298).

::: moniker-end

> [!NOTE]
> `docker images`Komut, havuz adları ve etiketleri *\<none>* (Yukarıda listelenmeyen) ile tanımlanmış olan aracı görüntülerini döndürür. Bu adlandırılmamış görüntüler, [çok aşamalı derleme](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *dockerfile*tarafından üretilir. Son görüntünün oluşturulması verimliliği artırır &mdash; ancak değişiklikler gerçekleştiğinde yalnızca gerekli katmanlar yeniden oluşturulur. Ara görüntülere artık gerek kalmadığında [Docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) komutunu kullanarak bunları silin.

*Geliştirme* görüntüsüne kıyasla üretim veya yayın görüntüsünün boyutunun daha küçük olması için bir beklentisi olabilir. Birim eşleme nedeniyle, hata ayıklayıcı ve uygulama, kapsayıcıda değil, yerel makineden çalıştırılıyor. *En son* görüntü, uygulamayı bir konak makinesinde çalıştırmak için gerekli uygulama kodunu paketlendi. Bu nedenle, Delta uygulama kodunun boyutudur.

## <a name="additional-resources"></a>Ek kaynaklar

* [Visual Studio ile kapsayıcı geliştirme](/visualstudio/containers)
* [Azure Service Fabric: geliştirme ortamınızı hazırlama](/azure/service-fabric/service-fabric-get-started)
* [Bir Windows kapsayıcısında .NET uygulamasını Azure 'a dağıtma Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [Docker ile Visual Studio geliştirme sorunlarını giderme](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [Visual Studio kapsayıcı araçları GitHub deposu](https://github.com/Microsoft/DockerTools)
* [Docker ve küçük kapsayıcılar kullanılarak GC](xref:performance/memory#sc)
