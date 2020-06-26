---
title: ASP.NET Core .NET Core gRPC istemcisi ve sunucusu oluşturma
author: juntaoluo
description: Bu öğreticide, ASP.NET Core bir gRPC hizmeti ve gRPC istemcisinin nasıl oluşturulacağı gösterilmektedir. GRPC hizmeti projesi oluşturmayı, Proto dosyasını düzenlemeyi ve çift yönlü akış araması eklemeyi öğrenin.
ms.author: johluo
ms.date: 04/08/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: c2ec543bd73c0c15c65358c95def0109c295a0f8
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403475"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a>Öğretici: ASP.NET Core bir gRPC istemcisi ve sunucusu oluşturma

[John Luo](https://github.com/juntaoluo) tarafından

Bu öğreticide, bir .NET Core [GRPC](https://grpc.io/docs/guides/) istemcisinin ve bir ASP.NET Core GRPC sunucusunun nasıl oluşturulacağı gösterilmektedir.

Sonda, gRPC Greeter hizmeti ile iletişim kuran bir gRPC istemcisine sahip olacaksınız.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * GRPC sunucusu oluşturun.
> * GRPC istemcisi oluşturun.
> * GRPC istemci hizmetini gRPC Greeter hizmeti ile test edin.

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-grpc-service"></a>GRPC hizmeti oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio 'Yu başlatın ve **Yeni proje oluştur**' u seçin. Alternatif olarak, Visual Studio **Dosya** menüsünden **Yeni**  >  **Proje**' yi seçin.
* **Yeni proje oluştur** Iletişim kutusunda **GRPC hizmeti** ' ni seçin ve **İleri**' yi seçin:

  ![Yeni proje iletişim kutusu oluştur](~/tutorials/grpc/grpc-start/static/cnp.png)

* Projeyi **Grpcgreeter**olarak adlandırın. Kodu kopyalayıp yapıştırdığınızda ad alanlarının eşleşmesi için, proje *Grpcgreeter* adında bir ad vermek önemlidir.
* **Oluştur**'u seçin.
* **Yeni bir gRPC hizmeti oluştur** iletişim kutusunda:
  * **GRPC hizmeti** şablonu seçilidir.
  * **Oluştur**'u seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.
* Dizinleri ( `cd` ), projeyi içerecek bir klasöre değiştirin.
* Aşağıdaki komutları çalıştırın:

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * `dotnet new`Komut *Grpcgreeter* klasöründe yeni bir GRPC hizmeti oluşturur.
  * `code`Komut, Visual Studio Code yeni bir örneğinde *Grpcgreeter* klasörünü açar.

  **Gerekli varlıkların derlenmesi ve hata ayıklaması için ' GrpcGreeter ' içinde eksik bir iletişim kutusu görüntülenir. Bunları ekleyin mi?**
* **Evet**' i seçin.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Terminalden aşağıdaki komutları çalıştırın:

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

Yukarıdaki komutlar, gRPC hizmeti oluşturmak için [.NET Core CLI](/dotnet/core/tools/dotnet) kullanır.

### <a name="open-the-project"></a>Projeyi açın

Visual Studio 'da **Dosya**  >  **Aç**' ı seçin ve ardından *grpcgreeter. csproj* dosyasını seçin.

---

### <a name="run-the-service"></a>Hizmeti çalıştırma

  [!INCLUDE[](~/includes/run-the-app.md)]

Günlükler hizmeti dinlediği hizmetini gösterir `https://localhost:5001` .

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> GRPC şablonu, [Aktarım Katmanı Güvenliği (TLS)](https://tools.ietf.org/html/rfc5246)kullanmak üzere yapılandırılmıştır. gRPC istemcilerinin, sunucuyu çağırmak için HTTPS kullanması gerekir.
>
> macOS, TLS ile ASP.NET Core gRPC 'yi desteklemez. MacOS 'ta gRPC hizmetlerini başarıyla çalıştırmak için ek yapılandırma gerekir. Daha fazla bilgi için bkz. [macOS üzerinde gRPC uygulaması ASP.NET Core başlatılamıyor](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

### <a name="examine-the-project-files"></a>Proje dosyalarını inceleyin

*Grpcgreeter* proje dosyaları:

* *Greet. proto*: *prototips/Greet. proto* dosyası `Greeter` gRPC 'yi tanımlar ve GRPC sunucu varlıklarını oluşturmak için kullanılır. Daha fazla bilgi için bkz. [gRPC 'ye giriş](xref:grpc/index).
* *Hizmetler* klasörü: hizmetin uygulamasını içerir `Greeter` .
* *appSettings.js*: Kestrel tarafından kullanılan protokol gibi yapılandırma verilerini içerir. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.
* *Program.cs*: GRPC hizmeti için giriş noktasını içerir. Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.
* *Startup.cs*: uygulama davranışını yapılandıran kodu içerir. Daha fazla bilgi için bkz. [uygulama başlatma](xref:fundamentals/startup).

## <a name="create-the-grpc-client-in-a-net-console-app"></a>Bir .NET konsol uygulamasında gRPC istemcisini oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio 'nun ikinci bir örneğini açın ve **Yeni proje oluştur**' u seçin.
* **Yeni proje oluştur** Iletişim kutusunda **konsol uygulaması (.NET Core)** öğesini seçin ve **İleri**' yi seçin.
* **Proje adı** metin kutusuna **Grpcgreeterclient** girin ve **Oluştur**' u seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.
* Dizinleri ( `cd` ), projeyi içerecek bir klasöre değiştirin.
* Aşağıdaki komutları çalıştırın:

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

*Grpcgreeterclient*adlı bir konsol uygulaması oluşturmak için [Mac için Visual Studio kullanarak MacOS 'ta kapsamlı bir .NET Core çözümü oluşturma](/dotnet/core/tutorials/using-on-mac-vs-full-solution) konusundaki yönergeleri izleyin.

---

### <a name="add-required-packages"></a>Gerekli paketleri Ekle

GRPC istemci projesi aşağıdaki paketleri gerektirir:

* .NET Core istemcisini içeren [GRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client).
* C# için prototipsiz ileti API 'Leri içeren [Google. Protoarabellek](https://www.nuget.org/packages/Google.Protobuf/).
* Prototipleme dosyaları için C# araçları desteği içeren [GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/). Araç çalışma zamanında gerekli değildir, bu nedenle bağımlılık ile işaretlenir `PrivateAssets="All"` .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Paket Yöneticisi Konsolu (PMC) veya NuGet Paketlerini Yönet ' i kullanarak paketleri yükler.

#### <a name="pmc-option-to-install-packages"></a>Paket yüklemek için PMC seçeneği

* Visual Studio 'da **Araçlar**  >  **NuGet Paket Yöneticisi**  >  **Paket Yöneticisi konsolu** ' nu seçin.
* **Paket Yöneticisi konsol** penceresinde, `cd GrpcGreeterClient` dizini *Grpcgreeterclient. csproj* dosyalarını içeren klasöre değiştirmek için komutunu çalıştırın.
* Aşağıdaki komutları çalıştırın:

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>Paket yüklemek için NuGet Paketlerini Yönet seçeneği

* **Çözüm Gezgini**  >  **NuGet Paketlerini Yönet** ' de projeye sağ tıklayın
* **Gözat** sekmesini seçin.
* Arama kutusuna **GRPC .net. Client** girin.
* **Araştır** sekmesinden **GRPC .net. Client** paketini seçin ve ardından **Install**' ı seçin.
* Ve için `Google.Protobuf` tekrarlayın `Grpc.Tools` .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**Tümleşik terminalden**aşağıdaki komutları çalıştırın:

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Packages** **Çözüm bölmesi**  >  **paket Ekle** ' de paketler klasörüne sağ tıklayın
* Arama kutusuna **GRPC .net. Client** girin.
* Sonuçlar bölmesinden **GRPC .net. Client** paketini seçin ve **paket Ekle** ' yi seçin.
* Ve için `Google.Protobuf` tekrarlayın `Grpc.Tools` .

---

### <a name="add-greetproto"></a>Greet. proto Ekle

* GRPC istemci projesinde bir *Prototips* klasörü oluşturun.
* *Protos\bilgisem. proto* dosyasını GRPC Greeter hizmetinden GRPC istemci projesine kopyalayın.
* *Grpcgreeterclient. csproj* proje dosyasını düzenleyin:

  # <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  Projeye sağ tıklayın ve **Proje dosyasını Düzenle**' yi seçin.

  # <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  *Grpcgreeterclient. csproj* dosyasını seçin.

  # <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

  Projeye sağ tıklayın ve **Araçlar**  >  **Dosya Düzenle**' yi seçin.

  ---

* `<Protobuf>` *Greet. proto* dosyasına başvuran bir öğesi olan bir öğe grubu ekleyin:

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Greeter istemcisini oluşturma

Ad alanındaki türleri oluşturmak için projeyi derleyin `GrpcGreeter` . `GrpcGreeter`Türler yapı işlemi tarafından otomatik olarak oluşturulur.

GRPC Client *program.cs* dosyasını aşağıdaki kodla güncelleştirin:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

*Program.cs* , GRPC istemcisinin giriş noktasını ve mantığını içerir.

Greeter istemcisi şu şekilde oluşturulur:

* `GrpcChannel`GRPC hizmetine bağlantı oluşturmak için bilgileri içeren bir örneği oluşturma.
* `GrpcChannel`Greeter istemcisini oluşturmak için kullanarak:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

Greeter istemcisi zaman uyumsuz yöntemi çağırır `SayHello` . `SayHello`Çağrının sonucu görüntülenir:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>GRPC istemcisini gRPC Greeter hizmeti ile test etme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Greeter hizmetinde, `Ctrl+F5` hata ayıklayıcı olmadan sunucuyu başlatmak için tuşuna basın.
* `GrpcGreeterClient`Projede, `Ctrl+F5` hata ayıklayıcı olmadan istemcisini başlatmak için tuşuna basın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Greeter hizmetini başlatın.
* İstemcisini başlatın.


# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Greeter hizmetini başlatın.
* İstemcisini başlatın.

---

İstemci, adı *Greeterclient*olan bir iletiyle hizmete bir tebrik gönderir. Hizmet, "Hello GreeterClient" iletisini yanıt olarak gönderir. Komut isteminde "Hello GreeterClient" yanıtı görüntülenir:

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

GRPC hizmeti, komut istemine yazılan günlüklerde başarılı çağrının ayrıntılarını kaydeder:

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
> Bu makaledeki kod, gRPC hizmetini güvenli hale getirmek için ASP.NET Core HTTPS geliştirme sertifikası gerektirir. .NET gRPC istemcisi iletiyle başarısız olursa `The remote certificate is invalid according to the validation procedure.` veya `The SSL connection could not be established.` geliştirme sertifikası güvenilir olmaz. Bu sorunu onarmak için bkz. [Güvenilmeyen/geçersiz sertifikayla gRPC hizmeti çağırma](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a>Sonraki adımlar

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
