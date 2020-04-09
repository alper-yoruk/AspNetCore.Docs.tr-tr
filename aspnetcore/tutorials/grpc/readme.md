---
page_type: sample
description: Bu öğretici, ASP.NET Core'da bir gRPC Hizmeti ve gRPC istemcisi oluşturmanın nasıl yapılacağını gösterir.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: create-grpc-client
ms.openlocfilehash: b9feb9eed62177358fffc0d7da582f625a431e32
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660932"
---
# <a name="create-a-grpc-client-and-server-in-aspnet-core-30-using-visual-studio"></a>Visual Studio'ASP.NET Core 3.0'da bir gRPC istemcisi ve sunucusu oluşturun

Bu öğretici, bir .NET Core gRPC istemcisi ve ASP.NET Core [gRPC](https://grpc.io/docs/guides/) Server nasıl oluşturulacak gösterir.

Sonunda, gRPC Greeter hizmeti ile iletişim sağlayan bir gRPC istemciniz olacak.

Bu öğreticide, siz;

* Bir gRPC Sunucusu oluşturun.
* Bir gRPC istemcisi oluşturun.
* gRPC Müşteri Hizmetlerini gRPC Greeter hizmetiyle test edin.

## <a name="create-a-grpc-service"></a>gRPC hizmeti oluşturma

* Visual Studio **File** menüsünden **Yeni** > **Proje'yi**seçin.
* Yeni **bir proje oluştur** iletişim **kutusunda, Çekirdek Web Uygulaması ASP.NET'yi**seçin.
* **Sonraki'ni** Seçin
* Proje **GrpcGreeter**adı . Proje *GrpcGreeter* adını önemli, böylece kod kopyalayıp yapıştırdığınızda ad boşlukları eşleşecektir.
* **Oluştur**’u seçin
* Yeni **bir ASP.NET Çekirdek Web Uygulaması Oluştur** iletişim kutusunda:
  * Açılan menülerde **.NET Core** ve **ASP.NET Core 3.0'ı** seçin. 
  * **gRPC Hizmeti** şablonu'nu seçin.
  * **Oluştur**’u seçin

### <a name="run-the-service"></a>Hizmeti çalıştırma

* Hata `Ctrl+F5` ayıklayıcı olmadan gRPC hizmetini çalıştırmak için basın.

  Visual Studio hizmeti komut istemiyle çalıştırUr.

Günlükler, hizmeti dinlerken `https://localhost:5001`gösterir.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

> [!NOTE]
> gRPC [şablonu, Aktarım Katmanı Güvenliği 'ni (TLS)](https://tools.ietf.org/html/rfc5246)kullanacak şekilde yapılandırıldı. gRPC istemcilerinin sunucuyu aramak için HTTPS'yi kullanması gerekir.
>
> macOS, TLS ile Core gRPC ASP.NET desteklemez. macOS'ta gRPC hizmetlerini başarıyla çalıştırmak için ek yapılandırma gereklidir. Daha fazla bilgi için [macOS'ta gRPC ve ASP.NET Core'a](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos)bakın.

### <a name="examine-the-project-files"></a>Proje dosyalarını inceleyin

*GrpcGreeter* proje dosyaları:

* *greet.proto*: *Protos/greet.proto* dosyası `Greeter` gRPC'yi tanımlar ve gRPC sunucu varlıklarını oluşturmak için kullanılır. Daha fazla bilgi için [gRPC'ye Giriş 'e](xref:grpc/index)bakın.
* *Hizmetler* klasörü: `Greeter` Hizmetin uygulanmasını içerir.
* *appSettings.json*: Kestrel tarafından kullanılan protokol gibi yapılandırma verilerini içerir. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.
* *Program.cs*: gRPC hizmetinin giriş noktasını içerir. Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.
* *Startup.cs*: Uygulama davranışını yapılandıran kod içerir. Daha fazla bilgi için [Uygulama başlangıç](xref:fundamentals/startup)bilgisine bakın.

## <a name="create-the-grpc-client-in-a-net-console-app"></a>bir .NET konsol uygulamasında gRPC istemcisini oluşturma

* Visual Studio'nun ikinci örneğini açın.
* Menü çubuğundan**Yeni** > **Proje** **Dosyası'nı** > seçin.
* Yeni **bir proje oluştur** iletişim kutusunda **Konsol Uygulaması'nı (.NET Core)** seçin.
* **Sonraki'ni** Seçin
* **Ad** metin kutusuna "GrpcGreeterClient" girin.
* **Oluştur'u**seçin.

### <a name="add-required-packages"></a>Gerekli paketleri ekleme

gRPC istemci projesi aşağıdaki paketleri gerektirir:

* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), .NET Çekirdek istemcisi içerir.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), C# için protobuf mesaj API'leri içerir.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), protobuf dosyaları için C# araç desteği içerir. Takım paketi çalışma zamanında gerekli değildir, bu nedenle bağımlılık `PrivateAssets="All"`.

Paketleri Paket Yöneticisi Konsolu (PMC) veya NuGet Paketlerini Yönet'i kullanarak yükleyin.

#### <a name="pmc-option-to-install-packages"></a>Paketleri yüklemek için PMC seçeneği

* Visual Studio'dan **Araçlar** > **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu'nu** seçin
* Paket **Yöneticisi Konsol** penceresinden, *GrpcGreeterClient.csproj* dosyasının bulunduğu dizine gidin.
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

### <a name="add-greetproto"></a>greet.proto ekle

* gRPC istemci projesinde bir **Protos** klasörü oluşturun.
* **Protos\greet.proto** dosyasını gRPC Greeter hizmetinden gRPC istemci projesine kopyalayın.
* *GrpcGreeterClient.csproj* proje dosyasını edin:

  Projeyi sağ tıklatın ve **Proje Dosyasını Edit'i**seçin.

* `<Protobuf>` **Greet.proto** dosyasına başvuran bir öğe grubu ekleyin:

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Greeter istemcisini oluşturma

Ad alanındaki türleri oluşturmak için `GrpcGreeter` projeyi oluşturun. Türler, `GrpcGreeter` yapı işlemi tarafından otomatik olarak oluşturulur.

gRPC *istemciProgram.cs* dosyasını aşağıdaki kodla güncelleştirin:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using GrpcGreeter;
using Grpc.Net.Client;

namespace GrpcGreeterClient
{
    class Program
    {
        static async Task Main(string[] args)
        {
            // The port number(5001) must match the port of the gRPC server.
            var channel = GrpcChannel.ForAddress("https://localhost:5001");
            var client = new Greeter.GreeterClient(channel);
            var reply = await client.SayHelloAsync(
                              new HelloRequest { Name = "GreeterClient" });
            Console.WriteLine("Greeting: " + reply.Message);
            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

*Program.cs* gRPC istemcisinin giriş noktasını ve mantığını içerir.

Greeter istemcisi tarafından oluşturulur:

* gRPC hizmetine bağlantı oluşturmak için bilgileri içeren bir `GrpcChannel` anında.
* Greeter `GrpcChannel` istemcisini oluşturmak için kullanma.

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>gRPC Karşılama hizmeti ile gRPC istemcisini test edin

* Greeter hizmetinde, `Ctrl+F5` hata ayıklayıcı olmadan sunucuyu başlatmak için basın.
* Projede, `GrpcGreeterClient` hata `Ctrl+F5` ayıklama olmadan istemciyi başlatmak için basın.

İstemci, "GreeterClient" adını içeren bir iletiyle hizmete bir karşılama gönderir. Hizmet yanıt olarak "Merhaba GreeterClient" iletisini gönderir. "Merhaba GreeterClient" yanıtı komut isteminde görüntülenir:

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

gRPC hizmeti komut istemine yazılan günlüklerde başarılı aramanın ayrıntılarını kaydeder.

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

### <a name="docs-help--next-steps-for-grpc"></a>Dokümanlar gRPC için sonraki adımlar & yardımcı olur

* [ASP.NET Core'da gRPC'ye giriş](https://docs.microsoft.com/aspnet/core/grpc/index?view=aspnetcore-3.0)
* [C# içeren gRPC hizmetleri](https://docs.microsoft.com/aspnet/core/grpc/basics?view=aspnetcore-3.0)
* [GRPC hizmetlerini C-core'dan ASP.NET Core'a geçirme](https://docs.microsoft.com/aspnet/core/grpc/migration?view=aspnetcore-3.0)
