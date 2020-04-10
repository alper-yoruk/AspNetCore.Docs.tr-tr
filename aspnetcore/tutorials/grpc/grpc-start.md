---
title: ASP.NET Core'da bir .NET Core gRPC istemcisi ve sunucusu oluşturma
author: juntaoluo
description: Bu öğretici, ASP.NET Core'da bir gRPC Hizmeti ve gRPC istemcisi oluşturmanın nasıl yapılacağını gösterir. GRPC Service projesi oluşturmayı, bir proto dosyasını nasıl döşeyecek lerini ve çift yönlü akış çağrısı eklemeyi öğrenin.
ms.author: johluo
ms.date: 04/08/2020
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 2bbd40b4b89af170dae40b8a5277749d6bcd5faf
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994635"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a>Öğretici: ASP.NET Core'da bir gRPC istemcisi ve sunucusu oluşturun

Yazar: [John Luo](https://github.com/juntaoluo)

Bu öğretici, bir .NET Core gRPC istemcisi ve ASP.NET Core [gRPC](https://grpc.io/docs/guides/) Server nasıl oluşturulacak gösterir.

Sonunda, gRPC Greeter hizmeti ile iletişim sağlayan bir gRPC istemciniz olacak.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([nasıl indirilir).](xref:index#how-to-download-a-sample)

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Bir gRPC Sunucusu oluşturun.
> * Bir gRPC istemcisi oluşturun.
> * gRPC Müşteri Hizmetlerini gRPC Greeter hizmetiyle test edin.

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a>gRPC hizmeti oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio'yı başlatın ve **yeni bir proje oluştur'u**seçin. Alternatif olarak, Visual Studio **File** menüsünden **Yeni** > **Proje'yi**seçin.
* Yeni **bir proje oluştur** iletişim kutusunda **gRPC Hizmeti'ni** seçin ve **İleri'yi**seçin:

  ![Yeni bir proje iletişim kutusu oluşturma](~/tutorials/grpc/grpc-start/static/cnp.png)

* Proje **GrpcGreeter**adı . Proje *GrpcGreeter* adını önemli, böylece kod kopyalayıp yapıştırdığınızda ad boşlukları eşleşecektir.
* **Oluştur'u**seçin.
* Yeni **bir gRPC hizmeti oluştur** iletişim kutusunda:
  * **gRPC Hizmeti** şablonu seçilir.
  * **Oluştur'u**seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Entegre [terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.
* Dizinleri`cd`( ) projeyi içeren bir klasörle değiştirin.
* Aşağıdaki komutları çalıştırın:

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * Komut `dotnet new` *GrpcGreeter* klasöründe yeni bir gRPC hizmeti oluşturur.
  * Komut `code` Visual Studio Code yeni bir örnek *te GrpcGreeter* klasörünü açar.

  Oluşturmak için Gerekli varlıklarla birlikte bir iletişim kutusu görüntülenir **ve hata ayıklama 'GrpcGreeter'dan eksiktir. Onları eklemek mi?**
* **Evet'i**seçin.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Bir terminalden aşağıdaki komutları çalıştırın:

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

Önceki komutlar bir gRPC hizmeti oluşturmak için [.NET Core CLI](/dotnet/core/tools/dotnet) kullanın.

### <a name="open-the-project"></a>Projeyi açın

Visual Studio'dan **Dosya** > **Aç'ı**seçin ve *ardından GrpcGreeter.csproj* dosyasını seçin.

---

### <a name="run-the-service"></a>Hizmeti çalıştırma

  [!INCLUDE[](~/includes/run-the-app.md)]

Günlükler, hizmeti dinlerken `https://localhost:5001`gösterir.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> gRPC [şablonu, Aktarım Katmanı Güvenliği 'ni (TLS)](https://tools.ietf.org/html/rfc5246)kullanacak şekilde yapılandırıldı. gRPC istemcilerinin sunucuyu aramak için HTTPS'yi kullanması gerekir.
>
> macOS, TLS ile Core gRPC ASP.NET desteklemez. macOS'ta gRPC hizmetlerini başarıyla çalıştırmak için ek yapılandırma gereklidir. Daha fazla bilgi için bkz: [macOS'ta Core gRPC ASP.NET başlatılamıyor.](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)

### <a name="examine-the-project-files"></a>Proje dosyalarını inceleyin

*GrpcGreeter* proje dosyaları:

* *greet.proto* &ndash; *Protos/greet.proto* dosyası gRPC'yi `Greeter` tanımlar ve gRPC sunucu varlıklarını oluşturmak için kullanılır. Daha fazla bilgi için [gRPC'ye Giriş 'e](xref:grpc/index)bakın.
* *Hizmetler* klasörü: `Greeter` Hizmetin uygulanmasını içerir.
* *appSettings.json* &ndash; Kestrel tarafından kullanılan protokol gibi yapılandırma verilerini içerir. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.
* *Program.cs* &ndash; gRPC hizmetinin giriş noktasını içerir. Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.
* *Startup.cs* &ndash; Uygulama davranışını yapılandıran kod içerir. Daha fazla bilgi için [Uygulama başlangıç](xref:fundamentals/startup)bilgisine bakın.

## <a name="create-the-grpc-client-in-a-net-console-app"></a>bir .NET konsol uygulamasında gRPC istemcisini oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio'nun ikinci bir örneğini açın ve **yeni bir proje oluştur'u**seçin.
* Yeni **bir proje oluştur** iletişim kutusunda **Konsol Uygulaması'nı (.NET Core)** seçin ve **İleri'yi**seçin.
* **Ad** metin kutusuna **GrpcGreeterClient'ı** girin ve **Oluştur'u**seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Entegre [terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.
* Dizinleri`cd`( ) projeyi içeren bir klasörle değiştirin.
* Aşağıdaki komutları çalıştırın:

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Mac için Visual Studio for [Mac'i kullanarak macOS'ta tam bir .NET Core çözümü oluşturarak](/dotnet/core/tutorials/using-on-mac-vs-full-solution) *GrpcGreeterClient*adında bir konsol uygulaması oluşturma yönergelerini izleyin.

---

### <a name="add-required-packages"></a>Gerekli paketleri ekleme

gRPC istemci projesi aşağıdaki paketleri gerektirir:

* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), .NET Çekirdek istemcisi içerir.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), C# için protobuf mesaj API'leri içerir.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), protobuf dosyaları için C# araç desteği içerir. Takım paketi çalışma zamanında gerekli değildir, bu nedenle bağımlılık `PrivateAssets="All"`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Paketleri Paket Yöneticisi Konsolu (PMC) veya NuGet Paketlerini Yönet'i kullanarak yükleyin.

#### <a name="pmc-option-to-install-packages"></a>Paketleri yüklemek için PMC seçeneği

* Visual Studio'dan **Araçlar** > **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu'nu** seçin
* Paket **Yöneticisi Konsolu** penceresinden, dizinleri `cd GrpcGreeterClient` *GrpcGreeterClient.csproj* dosyalarını içeren klasöre değiştirmek için çalıştırın.
* Aşağıdaki komutları çalıştırın:

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>Paketleri yüklemek için NuGet Paketlerini yönetme seçeneğini yönetin

* **Solution Explorer** > **Manage NuGet Paketleri'nde** projeye sağ tıklayın
* **Gözat** sekmesini seçin.
* Arama kutusuna **Grpc.Net.Client** girin.
* Gözat sekmesinden **Grpc.Net.Client** **paketini** seçin ve **Yükle'yi**seçin.
* Için `Google.Protobuf` tekrarlayın ve `Grpc.Tools`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**Entegre Terminal'den**aşağıdaki komutları çalıştırın:

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Çözüm Pad** > **Ekle Paketleri'ndeki** **Paketler** klasörüne sağ tıklayın
* Arama kutusuna **Grpc.Net.Client** girin.
* Sonuç bölmesinden **Grpc.Net.Client** paketini seçin ve **Paket Ekle'yi** seçin
* Için `Google.Protobuf` tekrarlayın ve `Grpc.Tools`.

---

### <a name="add-greetproto"></a>greet.proto ekle

* gRPC istemci projesinde bir *Protos* klasörü oluşturun.
* *Protos\greet.proto* dosyasını gRPC Greeter hizmetinden gRPC istemci projesine kopyalayın.
* *GrpcGreeterClient.csproj* proje dosyasını edin:

  # <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  Projeyi sağ tıklatın ve **Proje Dosyasını Edit'i**seçin.

  # <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  *GrpcGreeterClient.csproj* dosyasını seçin.

  # <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

  Projeyi sağ tıklatın ve **Araçları** > **Dosyayı Düzelt'i**seçin.

  ---

* `<Protobuf>` *Greet.proto* dosyasına başvuran bir öğe grubu ekleyin:

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Greeter istemcisini oluşturma

Ad alanındaki türleri oluşturmak için `GrpcGreeter` projeyi oluşturun. Türler, `GrpcGreeter` yapı işlemi tarafından otomatik olarak oluşturulur.

gRPC *istemciProgram.cs* dosyasını aşağıdaki kodla güncelleştirin:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

*Program.cs* gRPC istemcisinin giriş noktasını ve mantığını içerir.

Greeter istemcisi tarafından oluşturulur:

* gRPC hizmetine bağlantı oluşturmak için bilgileri içeren bir `GrpcChannel` anında.
* Greeter `GrpcChannel` istemcisini oluşturmak için kullanma:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

Greeter istemcisi asynchronous `SayHello` yöntemiçağırır. Aramanın `SayHello` sonucu görüntülenir:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>gRPC Karşılama hizmeti ile gRPC istemcisini test edin

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Greeter hizmetinde, `Ctrl+F5` hata ayıklayıcı olmadan sunucuyu başlatmak için basın.
* Projede, `GrpcGreeterClient` hata `Ctrl+F5` ayıklama olmadan istemciyi başlatmak için basın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Greeter hizmetini başlatın.
* İstemciyi başlatın.


# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Greeter hizmetini başlatın.
* İstemciyi başlatın.

---

İstemci, hizmete, *greeterClient*adını içeren bir ileti içeren bir karşılama gönderir. Hizmet yanıt olarak "Merhaba GreeterClient" iletisini gönderir. "Merhaba GreeterClient" yanıtı komut isteminde görüntülenir:

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

gRPC hizmeti komut istemine yazılan günlüklerde başarılı aramanın ayrıntılarını kaydeder:

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

> [!NOTE]
> Bu makaledeki kod, gRPC hizmetini güvence altına almak için ASP.NET Çekirdek HTTPS geliştirme sertifikasını gerektirir. .NET gRPC istemcisi `The remote certificate is invalid according to the validation procedure.` iletide `The SSL connection could not be established.`başarısız olursa veya geliştirme sertifikasına güvenilmez. Bu sorunu gidermek için, [güvenilmeyen/geçersiz sertifikaya sahip bir gRPC hizmetini arayın'a](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate)bakın.

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a>Sonraki adımlar

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
