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
# <a name="create-a-grpc-client-and-server-in-aspnet-core-30-using-visual-studio"></a><span data-ttu-id="9cd1c-102">Visual Studio'ASP.NET Core 3.0'da bir gRPC istemcisi ve sunucusu oluşturun</span><span class="sxs-lookup"><span data-stu-id="9cd1c-102">Create a gRPC client and server in ASP.NET Core 3.0 using Visual Studio</span></span>

<span data-ttu-id="9cd1c-103">Bu öğretici, bir .NET Core gRPC istemcisi ve ASP.NET Core [gRPC](https://grpc.io/docs/guides/) Server nasıl oluşturulacak gösterir.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-103">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="9cd1c-104">Sonunda, gRPC Greeter hizmeti ile iletişim sağlayan bir gRPC istemciniz olacak.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-104">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="9cd1c-105">Bu öğreticide, siz;</span><span class="sxs-lookup"><span data-stu-id="9cd1c-105">In this tutorial, you;</span></span>

* <span data-ttu-id="9cd1c-106">Bir gRPC Sunucusu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-106">Create a gRPC Server.</span></span>
* <span data-ttu-id="9cd1c-107">Bir gRPC istemcisi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-107">Create a gRPC client.</span></span>
* <span data-ttu-id="9cd1c-108">gRPC Müşteri Hizmetlerini gRPC Greeter hizmetiyle test edin.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-108">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="create-a-grpc-service"></a><span data-ttu-id="9cd1c-109">gRPC hizmeti oluşturma</span><span class="sxs-lookup"><span data-stu-id="9cd1c-109">Create a gRPC service</span></span>

* <span data-ttu-id="9cd1c-110">Visual Studio **File** menüsünden **Yeni** > **Proje'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-110">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="9cd1c-111">Yeni **bir proje oluştur** iletişim **kutusunda, Çekirdek Web Uygulaması ASP.NET'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-111">In the **Create a new project** dialog, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="9cd1c-112">**Sonraki'ni** Seçin</span><span class="sxs-lookup"><span data-stu-id="9cd1c-112">Select **Next**</span></span>
* <span data-ttu-id="9cd1c-113">Proje **GrpcGreeter**adı .</span><span class="sxs-lookup"><span data-stu-id="9cd1c-113">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="9cd1c-114">Proje *GrpcGreeter* adını önemli, böylece kod kopyalayıp yapıştırdığınızda ad boşlukları eşleşecektir.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-114">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="9cd1c-115">**Oluştur**’u seçin</span><span class="sxs-lookup"><span data-stu-id="9cd1c-115">Select **Create**</span></span>
* <span data-ttu-id="9cd1c-116">Yeni **bir ASP.NET Çekirdek Web Uygulaması Oluştur** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="9cd1c-116">In the **Create a new ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="9cd1c-117">Açılan menülerde **.NET Core** ve **ASP.NET Core 3.0'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-117">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdown menus.</span></span> 
  * <span data-ttu-id="9cd1c-118">**gRPC Hizmeti** şablonu'nu seçin.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-118">Select the **gRPC Service** template.</span></span>
  * <span data-ttu-id="9cd1c-119">**Oluştur**’u seçin</span><span class="sxs-lookup"><span data-stu-id="9cd1c-119">Select **Create**</span></span>

### <a name="run-the-service"></a><span data-ttu-id="9cd1c-120">Hizmeti çalıştırma</span><span class="sxs-lookup"><span data-stu-id="9cd1c-120">Run the service</span></span>

* <span data-ttu-id="9cd1c-121">Hata `Ctrl+F5` ayıklayıcı olmadan gRPC hizmetini çalıştırmak için basın.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-121">Press `Ctrl+F5` to run the gRPC service without the debugger.</span></span>

  <span data-ttu-id="9cd1c-122">Visual Studio hizmeti komut istemiyle çalıştırUr.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-122">Visual Studio runs the service in a command prompt.</span></span>

<span data-ttu-id="9cd1c-123">Günlükler, hizmeti dinlerken `https://localhost:5001`gösterir.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-123">The logs show the service listening on `https://localhost:5001`.</span></span>

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
> <span data-ttu-id="9cd1c-124">gRPC [şablonu, Aktarım Katmanı Güvenliği 'ni (TLS)](https://tools.ietf.org/html/rfc5246)kullanacak şekilde yapılandırıldı.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-124">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="9cd1c-125">gRPC istemcilerinin sunucuyu aramak için HTTPS'yi kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-125">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="9cd1c-126">macOS, TLS ile Core gRPC ASP.NET desteklemez.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-126">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="9cd1c-127">macOS'ta gRPC hizmetlerini başarıyla çalıştırmak için ek yapılandırma gereklidir.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-127">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="9cd1c-128">Daha fazla bilgi için [macOS'ta gRPC ve ASP.NET Core'a](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos)bakın.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-128">For more information, see [gRPC and ASP.NET Core on macOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="9cd1c-129">Proje dosyalarını inceleyin</span><span class="sxs-lookup"><span data-stu-id="9cd1c-129">Examine the project files</span></span>

<span data-ttu-id="9cd1c-130">*GrpcGreeter* proje dosyaları:</span><span class="sxs-lookup"><span data-stu-id="9cd1c-130">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="9cd1c-131">*greet.proto*: *Protos/greet.proto* dosyası `Greeter` gRPC'yi tanımlar ve gRPC sunucu varlıklarını oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-131">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="9cd1c-132">Daha fazla bilgi için [gRPC'ye Giriş 'e](xref:grpc/index)bakın.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-132">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="9cd1c-133">*Hizmetler* klasörü: `Greeter` Hizmetin uygulanmasını içerir.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-133">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="9cd1c-134">*appSettings.json*: Kestrel tarafından kullanılan protokol gibi yapılandırma verilerini içerir.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-134">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="9cd1c-135">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-135">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="9cd1c-136">*Program.cs*: gRPC hizmetinin giriş noktasını içerir.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-136">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="9cd1c-137">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-137">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="9cd1c-138">*Startup.cs*: Uygulama davranışını yapılandıran kod içerir.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-138">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="9cd1c-139">Daha fazla bilgi için [Uygulama başlangıç](xref:fundamentals/startup)bilgisine bakın.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-139">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="9cd1c-140">bir .NET konsol uygulamasında gRPC istemcisini oluşturma</span><span class="sxs-lookup"><span data-stu-id="9cd1c-140">Create the gRPC client in a .NET console app</span></span>

* <span data-ttu-id="9cd1c-141">Visual Studio'nun ikinci örneğini açın.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-141">Open a second instance of Visual Studio.</span></span>
* <span data-ttu-id="9cd1c-142">Menü çubuğundan**Yeni** > **Proje** **Dosyası'nı** > seçin.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-142">Select **File** > **New** > **Project** from the menu bar.</span></span>
* <span data-ttu-id="9cd1c-143">Yeni **bir proje oluştur** iletişim kutusunda **Konsol Uygulaması'nı (.NET Core)** seçin.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-143">In the **Create a new project** dialog, select **Console App (.NET Core)**.</span></span>
* <span data-ttu-id="9cd1c-144">**Sonraki'ni** Seçin</span><span class="sxs-lookup"><span data-stu-id="9cd1c-144">Select **Next**</span></span>
* <span data-ttu-id="9cd1c-145">**Ad** metin kutusuna "GrpcGreeterClient" girin.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-145">In the **Name** text box, enter "GrpcGreeterClient".</span></span>
* <span data-ttu-id="9cd1c-146">**Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-146">Select **Create**.</span></span>

### <a name="add-required-packages"></a><span data-ttu-id="9cd1c-147">Gerekli paketleri ekleme</span><span class="sxs-lookup"><span data-stu-id="9cd1c-147">Add required packages</span></span>

<span data-ttu-id="9cd1c-148">gRPC istemci projesi aşağıdaki paketleri gerektirir:</span><span class="sxs-lookup"><span data-stu-id="9cd1c-148">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="9cd1c-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), .NET Çekirdek istemcisi içerir.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="9cd1c-150">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), C# için protobuf mesaj API'leri içerir.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-150">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="9cd1c-151">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), protobuf dosyaları için C# araç desteği içerir.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-151">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="9cd1c-152">Takım paketi çalışma zamanında gerekli değildir, bu nedenle bağımlılık `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-152">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

<span data-ttu-id="9cd1c-153">Paketleri Paket Yöneticisi Konsolu (PMC) veya NuGet Paketlerini Yönet'i kullanarak yükleyin.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-153">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="9cd1c-154">Paketleri yüklemek için PMC seçeneği</span><span class="sxs-lookup"><span data-stu-id="9cd1c-154">PMC option to install packages</span></span>

* <span data-ttu-id="9cd1c-155">Visual Studio'dan **Araçlar** > **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu'nu** seçin</span><span class="sxs-lookup"><span data-stu-id="9cd1c-155">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="9cd1c-156">Paket **Yöneticisi Konsol** penceresinden, *GrpcGreeterClient.csproj* dosyasının bulunduğu dizine gidin.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-156">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="9cd1c-157">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="9cd1c-157">Run the following commands:</span></span>

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="9cd1c-158">Paketleri yüklemek için NuGet Paketlerini yönetme seçeneğini yönetin</span><span class="sxs-lookup"><span data-stu-id="9cd1c-158">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="9cd1c-159">**Solution Explorer** > **Manage NuGet Paketleri'nde** projeye sağ tıklayın</span><span class="sxs-lookup"><span data-stu-id="9cd1c-159">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="9cd1c-160">**Gözat** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-160">Select the **Browse** tab.</span></span>
* <span data-ttu-id="9cd1c-161">Arama kutusuna **Grpc.Net.Client** girin.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-161">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="9cd1c-162">Gözat sekmesinden **Grpc.Net.Client** **paketini** seçin ve **Yükle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-162">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="9cd1c-163">Için `Google.Protobuf` tekrarlayın ve `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-163">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

### <a name="add-greetproto"></a><span data-ttu-id="9cd1c-164">greet.proto ekle</span><span class="sxs-lookup"><span data-stu-id="9cd1c-164">Add greet.proto</span></span>

* <span data-ttu-id="9cd1c-165">gRPC istemci projesinde bir **Protos** klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-165">Create a **Protos** folder in the gRPC client project.</span></span>
* <span data-ttu-id="9cd1c-166">**Protos\greet.proto** dosyasını gRPC Greeter hizmetinden gRPC istemci projesine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-166">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="9cd1c-167">*GrpcGreeterClient.csproj* proje dosyasını edin:</span><span class="sxs-lookup"><span data-stu-id="9cd1c-167">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  <span data-ttu-id="9cd1c-168">Projeyi sağ tıklatın ve **Proje Dosyasını Edit'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-168">Right-click the project and select **Edit Project File**.</span></span>

* <span data-ttu-id="9cd1c-169">`<Protobuf>` **Greet.proto** dosyasına başvuran bir öğe grubu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9cd1c-169">Add an item group with a `<Protobuf>` element that refers to the **greet.proto** file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="9cd1c-170">Greeter istemcisini oluşturma</span><span class="sxs-lookup"><span data-stu-id="9cd1c-170">Create the Greeter client</span></span>

<span data-ttu-id="9cd1c-171">Ad alanındaki türleri oluşturmak için `GrpcGreeter` projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-171">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="9cd1c-172">Türler, `GrpcGreeter` yapı işlemi tarafından otomatik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-172">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="9cd1c-173">gRPC *istemciProgram.cs* dosyasını aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="9cd1c-173">Update the gRPC client *Program.cs* file with the following code:</span></span>

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

<span data-ttu-id="9cd1c-174">*Program.cs* gRPC istemcisinin giriş noktasını ve mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-174">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="9cd1c-175">Greeter istemcisi tarafından oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="9cd1c-175">The Greeter client is created by:</span></span>

* <span data-ttu-id="9cd1c-176">gRPC hizmetine bağlantı oluşturmak için bilgileri içeren bir `GrpcChannel` anında.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-176">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="9cd1c-177">Greeter `GrpcChannel` istemcisini oluşturmak için kullanma.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-177">Using the `GrpcChannel` to construct the Greeter client.</span></span>

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="9cd1c-178">gRPC Karşılama hizmeti ile gRPC istemcisini test edin</span><span class="sxs-lookup"><span data-stu-id="9cd1c-178">Test the gRPC client with the gRPC Greeter service</span></span>

* <span data-ttu-id="9cd1c-179">Greeter hizmetinde, `Ctrl+F5` hata ayıklayıcı olmadan sunucuyu başlatmak için basın.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-179">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="9cd1c-180">Projede, `GrpcGreeterClient` hata `Ctrl+F5` ayıklama olmadan istemciyi başlatmak için basın.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-180">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

<span data-ttu-id="9cd1c-181">İstemci, "GreeterClient" adını içeren bir iletiyle hizmete bir karşılama gönderir.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-181">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="9cd1c-182">Hizmet yanıt olarak "Merhaba GreeterClient" iletisini gönderir.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-182">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="9cd1c-183">"Merhaba GreeterClient" yanıtı komut isteminde görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="9cd1c-183">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="9cd1c-184">gRPC hizmeti komut istemine yazılan günlüklerde başarılı aramanın ayrıntılarını kaydeder.</span><span class="sxs-lookup"><span data-stu-id="9cd1c-184">The gRPC service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="docs-help--next-steps-for-grpc"></a><span data-ttu-id="9cd1c-185">Dokümanlar gRPC için sonraki adımlar & yardımcı olur</span><span class="sxs-lookup"><span data-stu-id="9cd1c-185">Docs help & next steps for gRPC</span></span>

* [<span data-ttu-id="9cd1c-186">ASP.NET Core'da gRPC'ye giriş</span><span class="sxs-lookup"><span data-stu-id="9cd1c-186">Introduction to gRPC on ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/grpc/index?view=aspnetcore-3.0)
* [<span data-ttu-id="9cd1c-187">C# içeren gRPC hizmetleri</span><span class="sxs-lookup"><span data-stu-id="9cd1c-187">gRPC services with C#</span></span>](https://docs.microsoft.com/aspnet/core/grpc/basics?view=aspnetcore-3.0)
* [<span data-ttu-id="9cd1c-188">GRPC hizmetlerini C-core'dan ASP.NET Core'a geçirme</span><span class="sxs-lookup"><span data-stu-id="9cd1c-188">Migrating gRPC services from C-core to ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/grpc/migration?view=aspnetcore-3.0)
