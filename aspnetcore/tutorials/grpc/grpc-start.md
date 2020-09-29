---
title: ASP.NET Core .NET Core gRPC istemcisi ve sunucusu oluşturma
author: juntaoluo
description: Bu öğreticide, ASP.NET Core bir gRPC hizmeti ve gRPC istemcisinin nasıl oluşturulacağı gösterilmektedir. GRPC hizmeti projesi oluşturmayı, Proto dosyasını düzenlemeyi ve çift yönlü akış araması eklemeyi öğrenin.
ms.author: johluo
ms.date: 04/08/2020
no-loc:
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
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 13eb57bbe671dcc70a1678222a98590f4edc6e6f
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424262"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="ea181-104">Öğretici: ASP.NET Core bir gRPC istemcisi ve sunucusu oluşturma</span><span class="sxs-lookup"><span data-stu-id="ea181-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="ea181-105">[John Luo](https://github.com/juntaoluo) tarafından</span><span class="sxs-lookup"><span data-stu-id="ea181-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="ea181-106">Bu öğreticide, bir .NET Core [GRPC](https://grpc.io/docs/guides/) istemcisinin ve bir ASP.NET Core GRPC sunucusunun nasıl oluşturulacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="ea181-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="ea181-107">Sonda, gRPC Greeter hizmeti ile iletişim kuran bir gRPC istemcisine sahip olacaksınız.</span><span class="sxs-lookup"><span data-stu-id="ea181-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="ea181-108">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ea181-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="ea181-109">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="ea181-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ea181-110">GRPC sunucusu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ea181-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="ea181-111">GRPC istemcisi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ea181-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="ea181-112">GRPC istemci hizmetini gRPC Greeter hizmeti ile test edin.</span><span class="sxs-lookup"><span data-stu-id="ea181-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ea181-113">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="ea181-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ea181-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea181-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ea181-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ea181-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ea181-116">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea181-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="ea181-117">GRPC hizmeti oluşturma</span><span class="sxs-lookup"><span data-stu-id="ea181-117">Create a gRPC service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ea181-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea181-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ea181-119">Visual Studio 'Yu başlatın ve **Yeni proje oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="ea181-119">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="ea181-120">Alternatif olarak, Visual Studio **Dosya** menüsünden **Yeni**  >  **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="ea181-120">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ea181-121">**Yeni proje oluştur** Iletişim kutusunda **GRPC hizmeti** ' ni seçin ve **İleri**' yi seçin:</span><span class="sxs-lookup"><span data-stu-id="ea181-121">In the **Create a new project** dialog, select **gRPC Service** and select **Next**:</span></span>

  ![Yeni proje iletişim kutusu oluştur](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="ea181-123">Projeyi **Grpcgreeter**olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="ea181-123">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="ea181-124">Kodu kopyalayıp yapıştırdığınızda ad alanlarının eşleşmesi için, proje *Grpcgreeter* adında bir ad vermek önemlidir.</span><span class="sxs-lookup"><span data-stu-id="ea181-124">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="ea181-125">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="ea181-125">Select **Create**.</span></span>
* <span data-ttu-id="ea181-126">**Yeni bir gRPC hizmeti oluştur** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="ea181-126">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="ea181-127">**GRPC hizmeti** şablonu seçilidir.</span><span class="sxs-lookup"><span data-stu-id="ea181-127">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="ea181-128">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="ea181-128">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ea181-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ea181-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ea181-130">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="ea181-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ea181-131">Dizinleri ( `cd` ), projeyi içerecek bir klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="ea181-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="ea181-132">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="ea181-132">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="ea181-133">`dotnet new`Komut *Grpcgreeter* klasöründe yeni bir GRPC hizmeti oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ea181-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="ea181-134">`code`Komut, Visual Studio Code yeni bir örneğinde *Grpcgreeter* klasörünü açar.</span><span class="sxs-lookup"><span data-stu-id="ea181-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="ea181-135">**Gerekli varlıkların derlenmesi ve hata ayıklaması için ' GrpcGreeter ' içinde eksik bir iletişim kutusu görüntülenir. Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="ea181-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="ea181-136">**Evet** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="ea181-136">Select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ea181-137">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea181-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ea181-138">Terminalden aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="ea181-138">From a terminal, run the following commands:</span></span>

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

<span data-ttu-id="ea181-139">Yukarıdaki komutlar, gRPC hizmeti oluşturmak için [.NET Core CLI](/dotnet/core/tools/dotnet) kullanır.</span><span class="sxs-lookup"><span data-stu-id="ea181-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="ea181-140">Projeyi açın</span><span class="sxs-lookup"><span data-stu-id="ea181-140">Open the project</span></span>

<span data-ttu-id="ea181-141">Visual Studio 'da **Dosya**  >  **Aç**' ı seçin ve ardından *grpcgreeter. csproj* dosyasını seçin.</span><span class="sxs-lookup"><span data-stu-id="ea181-141">From Visual Studio, select **File** > **Open**, and then select the *GrpcGreeter.csproj* file.</span></span>

---

### <a name="run-the-service"></a><span data-ttu-id="ea181-142">Hizmeti çalıştırma</span><span class="sxs-lookup"><span data-stu-id="ea181-142">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="ea181-143">Günlükler hizmeti dinlediği hizmetini gösterir `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="ea181-143">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="ea181-144">GRPC şablonu, [Aktarım Katmanı Güvenliği (TLS)](https://tools.ietf.org/html/rfc5246)kullanmak üzere yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="ea181-144">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="ea181-145">gRPC istemcilerinin, sunucuyu çağırmak için HTTPS kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="ea181-145">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="ea181-146">macOS, TLS ile ASP.NET Core gRPC 'yi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="ea181-146">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="ea181-147">MacOS 'ta gRPC hizmetlerini başarıyla çalıştırmak için ek yapılandırma gerekir.</span><span class="sxs-lookup"><span data-stu-id="ea181-147">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="ea181-148">Daha fazla bilgi için bkz. [macOS üzerinde gRPC uygulaması ASP.NET Core başlatılamıyor](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="ea181-148">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="ea181-149">Proje dosyalarını inceleyin</span><span class="sxs-lookup"><span data-stu-id="ea181-149">Examine the project files</span></span>

<span data-ttu-id="ea181-150">*Grpcgreeter* proje dosyaları:</span><span class="sxs-lookup"><span data-stu-id="ea181-150">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="ea181-151">*Greet. proto*: *prototips/Greet. proto* dosyası `Greeter` gRPC 'yi tanımlar ve GRPC sunucu varlıklarını oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ea181-151">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="ea181-152">Daha fazla bilgi için bkz. [gRPC 'ye giriş](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="ea181-152">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="ea181-153">*Hizmetler* klasörü: hizmetin uygulamasını içerir `Greeter` .</span><span class="sxs-lookup"><span data-stu-id="ea181-153">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="ea181-154">*appSettings.js*: Kestrel tarafından kullanılan protokol gibi yapılandırma verilerini içerir.</span><span class="sxs-lookup"><span data-stu-id="ea181-154">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="ea181-155">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="ea181-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="ea181-156">*Program.cs*: GRPC hizmeti için giriş noktasını içerir.</span><span class="sxs-lookup"><span data-stu-id="ea181-156">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="ea181-157">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="ea181-157">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="ea181-158">*Startup.cs*: uygulama davranışını yapılandıran kodu içerir.</span><span class="sxs-lookup"><span data-stu-id="ea181-158">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="ea181-159">Daha fazla bilgi için bkz. [uygulama başlatma](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="ea181-159">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="ea181-160">Bir .NET konsol uygulamasında gRPC istemcisini oluşturma</span><span class="sxs-lookup"><span data-stu-id="ea181-160">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ea181-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea181-161">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ea181-162">Visual Studio 'nun ikinci bir örneğini açın ve **Yeni proje oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="ea181-162">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="ea181-163">**Yeni proje oluştur** Iletişim kutusunda **konsol uygulaması (.NET Core)** öğesini seçin ve **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="ea181-163">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="ea181-164">**Proje adı** metin kutusuna **Grpcgreeterclient** girin ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="ea181-164">In the **Project name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ea181-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ea181-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ea181-166">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="ea181-166">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ea181-167">Dizinleri ( `cd` ), projeyi içerecek bir klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="ea181-167">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="ea181-168">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="ea181-168">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ea181-169">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea181-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ea181-170">*Grpcgreeterclient*adlı bir konsol uygulaması oluşturmak için [Mac için Visual Studio kullanarak MacOS 'ta kapsamlı bir .NET Core çözümü oluşturma](/dotnet/core/tutorials/using-on-mac-vs-full-solution) konusundaki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="ea181-170">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="ea181-171">Gerekli paketleri Ekle</span><span class="sxs-lookup"><span data-stu-id="ea181-171">Add required packages</span></span>

<span data-ttu-id="ea181-172">GRPC istemci projesi aşağıdaki paketleri gerektirir:</span><span class="sxs-lookup"><span data-stu-id="ea181-172">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="ea181-173">.NET Core istemcisini içeren [GRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client).</span><span class="sxs-lookup"><span data-stu-id="ea181-173">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="ea181-174">C# için prototipsiz ileti API 'Leri içeren [Google. Protoarabellek](https://www.nuget.org/packages/Google.Protobuf/).</span><span class="sxs-lookup"><span data-stu-id="ea181-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="ea181-175">Prototipleme dosyaları için C# araçları desteği içeren [GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="ea181-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="ea181-176">Araç çalışma zamanında gerekli değildir, bu nedenle bağımlılık ile işaretlenir `PrivateAssets="All"` .</span><span class="sxs-lookup"><span data-stu-id="ea181-176">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ea181-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea181-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ea181-178">Paket Yöneticisi Konsolu (PMC) veya NuGet Paketlerini Yönet ' i kullanarak paketleri yükler.</span><span class="sxs-lookup"><span data-stu-id="ea181-178">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="ea181-179">Paket yüklemek için PMC seçeneği</span><span class="sxs-lookup"><span data-stu-id="ea181-179">PMC option to install packages</span></span>

* <span data-ttu-id="ea181-180">Visual Studio 'da **Araçlar**  >  **NuGet Paket Yöneticisi**  >  **Paket Yöneticisi konsolu** ' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="ea181-180">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="ea181-181">**Paket Yöneticisi konsol** penceresinde, `cd GrpcGreeterClient` dizini *Grpcgreeterclient. csproj* dosyalarını içeren klasöre değiştirmek için komutunu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="ea181-181">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="ea181-182">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="ea181-182">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="ea181-183">Paket yüklemek için NuGet Paketlerini Yönet seçeneği</span><span class="sxs-lookup"><span data-stu-id="ea181-183">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="ea181-184">**Çözüm Gezgini**  >  **NuGet Paketlerini Yönet** ' de projeye sağ tıklayın</span><span class="sxs-lookup"><span data-stu-id="ea181-184">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="ea181-185">**Gözat** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="ea181-185">Select the **Browse** tab.</span></span>
* <span data-ttu-id="ea181-186">Arama kutusuna **GRPC .net. Client** girin.</span><span class="sxs-lookup"><span data-stu-id="ea181-186">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="ea181-187">**Araştır** sekmesinden **GRPC .net. Client** paketini seçin ve ardından **Install**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="ea181-187">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="ea181-188">Ve için `Google.Protobuf` tekrarlayın `Grpc.Tools` .</span><span class="sxs-lookup"><span data-stu-id="ea181-188">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ea181-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ea181-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ea181-190">**Tümleşik terminalden**aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="ea181-190">Run the following commands from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ea181-191">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea181-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ea181-192">**Packages** **Çözüm bölmesi**  >  **paket Ekle** ' de paketler klasörüne sağ tıklayın</span><span class="sxs-lookup"><span data-stu-id="ea181-192">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="ea181-193">Arama kutusuna **GRPC .net. Client** girin.</span><span class="sxs-lookup"><span data-stu-id="ea181-193">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="ea181-194">Sonuçlar bölmesinden **GRPC .net. Client** paketini seçin ve **paket Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="ea181-194">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="ea181-195">Ve için `Google.Protobuf` tekrarlayın `Grpc.Tools` .</span><span class="sxs-lookup"><span data-stu-id="ea181-195">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="ea181-196">Greet. proto Ekle</span><span class="sxs-lookup"><span data-stu-id="ea181-196">Add greet.proto</span></span>

* <span data-ttu-id="ea181-197">GRPC istemci projesinde bir *Prototips* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ea181-197">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="ea181-198">*Protos\bilgisem. proto* dosyasını GRPC Greeter hizmetinden GRPC istemci projesine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="ea181-198">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="ea181-199">Dosyanın içindeki ad alanını `greet.proto` projenin ad alanına güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ea181-199">Update the namespace inside the `greet.proto` file to the project's namespace:</span></span>

  ```
  option csharp_namespace = "GrpcGreeterClient";
  ```

* <span data-ttu-id="ea181-200">*Grpcgreeterclient. csproj* proje dosyasını düzenleyin:</span><span class="sxs-lookup"><span data-stu-id="ea181-200">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studio"></a>[<span data-ttu-id="ea181-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea181-201">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="ea181-202">Projeye sağ tıklayın ve **Proje dosyasını Düzenle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="ea181-202">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-code"></a>[<span data-ttu-id="ea181-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ea181-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="ea181-204">*Grpcgreeterclient. csproj* dosyasını seçin.</span><span class="sxs-lookup"><span data-stu-id="ea181-204">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ea181-205">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea181-205">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="ea181-206">Projeye sağ tıklayın ve **Araçlar**  >  **Dosya Düzenle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="ea181-206">Right-click the project and select **Tools** > **Edit File**.</span></span>

  ---

* <span data-ttu-id="ea181-207">`<Protobuf>` *Greet. proto* dosyasına başvuran bir öğesi olan bir öğe grubu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ea181-207">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="ea181-208">Greeter istemcisini oluşturma</span><span class="sxs-lookup"><span data-stu-id="ea181-208">Create the Greeter client</span></span>

<span data-ttu-id="ea181-209">Ad alanındaki türleri oluşturmak için projeyi derleyin `GrpcGreeter` .</span><span class="sxs-lookup"><span data-stu-id="ea181-209">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="ea181-210">`GrpcGreeter`Türler yapı işlemi tarafından otomatik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ea181-210">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="ea181-211">GRPC Client *program.cs* dosyasını aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ea181-211">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="ea181-212">*Program.cs* , GRPC istemcisinin giriş noktasını ve mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="ea181-212">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="ea181-213">Greeter istemcisi şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="ea181-213">The Greeter client is created by:</span></span>

* <span data-ttu-id="ea181-214">`GrpcChannel`GRPC hizmetine bağlantı oluşturmak için bilgileri içeren bir örneği oluşturma.</span><span class="sxs-lookup"><span data-stu-id="ea181-214">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="ea181-215">`GrpcChannel`Greeter istemcisini oluşturmak için kullanarak:</span><span class="sxs-lookup"><span data-stu-id="ea181-215">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="ea181-216">Greeter istemcisi zaman uyumsuz yöntemi çağırır `SayHello` .</span><span class="sxs-lookup"><span data-stu-id="ea181-216">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="ea181-217">`SayHello`Çağrının sonucu görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="ea181-217">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="ea181-218">GRPC istemcisini gRPC Greeter hizmeti ile test etme</span><span class="sxs-lookup"><span data-stu-id="ea181-218">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ea181-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea181-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ea181-220">Greeter hizmetinde, `Ctrl+F5` hata ayıklayıcı olmadan sunucuyu başlatmak için tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="ea181-220">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="ea181-221">`GrpcGreeterClient`Projede, `Ctrl+F5` hata ayıklayıcı olmadan istemcisini başlatmak için tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="ea181-221">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ea181-222">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ea181-222">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ea181-223">Greeter hizmetini başlatın.</span><span class="sxs-lookup"><span data-stu-id="ea181-223">Start the Greeter service.</span></span>
* <span data-ttu-id="ea181-224">İstemcisini başlatın.</span><span class="sxs-lookup"><span data-stu-id="ea181-224">Start the client.</span></span>


# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ea181-225">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea181-225">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ea181-226">Greeter hizmetini başlatın.</span><span class="sxs-lookup"><span data-stu-id="ea181-226">Start the Greeter service.</span></span>
* <span data-ttu-id="ea181-227">İstemcisini başlatın.</span><span class="sxs-lookup"><span data-stu-id="ea181-227">Start the client.</span></span>

---

<span data-ttu-id="ea181-228">İstemci, adı *Greeterclient*olan bir iletiyle hizmete bir tebrik gönderir.</span><span class="sxs-lookup"><span data-stu-id="ea181-228">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="ea181-229">Hizmet, "Hello GreeterClient" iletisini yanıt olarak gönderir.</span><span class="sxs-lookup"><span data-stu-id="ea181-229">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="ea181-230">Komut isteminde "Hello GreeterClient" yanıtı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="ea181-230">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="ea181-231">GRPC hizmeti, komut istemine yazılan günlüklerde başarılı çağrının ayrıntılarını kaydeder:</span><span class="sxs-lookup"><span data-stu-id="ea181-231">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

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
> <span data-ttu-id="ea181-232">Bu makaledeki kod, gRPC hizmetini güvenli hale getirmek için ASP.NET Core HTTPS geliştirme sertifikası gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ea181-232">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="ea181-233">.NET gRPC istemcisi iletiyle başarısız olursa `The remote certificate is invalid according to the validation procedure.` veya `The SSL connection could not be established.` geliştirme sertifikası güvenilir olmaz.</span><span class="sxs-lookup"><span data-stu-id="ea181-233">If the .NET gRPC client fails with the message `The remote certificate is invalid according to the validation procedure.` or `The SSL connection could not be established.`, the development certificate isn't trusted.</span></span> <span data-ttu-id="ea181-234">Bu sorunu onarmak için bkz. [Güvenilmeyen/geçersiz sertifikayla gRPC hizmeti çağırma](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span><span class="sxs-lookup"><span data-stu-id="ea181-234">To fix this issue, see [Call a gRPC service with an untrusted/invalid certificate](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="ea181-235">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="ea181-235">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
