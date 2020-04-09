---
title: ASP.NET Core'da bir .NET Core gRPC istemcisi ve sunucusu oluşturma
author: juntaoluo
description: Bu öğretici, ASP.NET Core'da bir gRPC Hizmeti ve gRPC istemcisi oluşturmanın nasıl yapılacağını gösterir. GRPC Service projesi oluşturmayı, bir proto dosyasını nasıl döşeyecek lerini ve çift yönlü akış çağrısı eklemeyi öğrenin.
ms.author: johluo
ms.date: 12/05/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 0cedeb021427455c3f60a8a8cc36b52794a055bc
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665825"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="807f1-104">Öğretici: ASP.NET Core'da bir gRPC istemcisi ve sunucusu oluşturun</span><span class="sxs-lookup"><span data-stu-id="807f1-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="807f1-105">Yazar: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="807f1-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="807f1-106">Bu öğretici, bir .NET Core gRPC istemcisi ve ASP.NET Core [gRPC](https://grpc.io/docs/guides/) Server nasıl oluşturulacak gösterir.</span><span class="sxs-lookup"><span data-stu-id="807f1-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="807f1-107">Sonunda, gRPC Greeter hizmeti ile iletişim sağlayan bir gRPC istemciniz olacak.</span><span class="sxs-lookup"><span data-stu-id="807f1-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="807f1-108">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([nasıl indirilir).](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="807f1-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="807f1-109">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="807f1-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="807f1-110">Bir gRPC Sunucusu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="807f1-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="807f1-111">Bir gRPC istemcisi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="807f1-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="807f1-112">gRPC Müşteri Hizmetlerini gRPC Greeter hizmetiyle test edin.</span><span class="sxs-lookup"><span data-stu-id="807f1-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="807f1-113">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="807f1-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="807f1-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="807f1-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="807f1-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="807f1-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="807f1-116">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="807f1-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="807f1-117">gRPC hizmeti oluşturma</span><span class="sxs-lookup"><span data-stu-id="807f1-117">Create a gRPC service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="807f1-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="807f1-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="807f1-119">Visual Studio'yı başlatın ve **yeni bir proje oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="807f1-119">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="807f1-120">Alternatif olarak, Visual Studio **File** menüsünden **Yeni** > **Proje'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="807f1-120">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="807f1-121">Yeni **bir proje oluştur** iletişim kutusunda **gRPC Hizmeti'ni** seçin ve **İleri'yi**seçin:</span><span class="sxs-lookup"><span data-stu-id="807f1-121">In the **Create a new project** dialog, select **gRPC Service** and select **Next**:</span></span>

  ![Yeni bir proje iletişim kutusu oluşturma](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="807f1-123">Proje **GrpcGreeter**adı .</span><span class="sxs-lookup"><span data-stu-id="807f1-123">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="807f1-124">Proje *GrpcGreeter* adını önemli, böylece kod kopyalayıp yapıştırdığınızda ad boşlukları eşleşecektir.</span><span class="sxs-lookup"><span data-stu-id="807f1-124">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="807f1-125">**Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="807f1-125">Select **Create**.</span></span>
* <span data-ttu-id="807f1-126">Yeni **bir gRPC hizmeti oluştur** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="807f1-126">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="807f1-127">**gRPC Hizmeti** şablonu seçilir.</span><span class="sxs-lookup"><span data-stu-id="807f1-127">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="807f1-128">**Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="807f1-128">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="807f1-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="807f1-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="807f1-130">Entegre [terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="807f1-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="807f1-131">Dizinleri`cd`( ) projeyi içeren bir klasörle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="807f1-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="807f1-132">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="807f1-132">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="807f1-133">Komut `dotnet new` *GrpcGreeter* klasöründe yeni bir gRPC hizmeti oluşturur.</span><span class="sxs-lookup"><span data-stu-id="807f1-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="807f1-134">Komut `code` Visual Studio Code yeni bir örnek *te GrpcGreeter* klasörünü açar.</span><span class="sxs-lookup"><span data-stu-id="807f1-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="807f1-135">Oluşturmak için Gerekli varlıklarla birlikte bir iletişim kutusu görüntülenir **ve hata ayıklama 'GrpcGreeter'dan eksiktir. Onları eklemek mi?**</span><span class="sxs-lookup"><span data-stu-id="807f1-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="807f1-136">**Evet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="807f1-136">Select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="807f1-137">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="807f1-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="807f1-138">Bir terminalden aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="807f1-138">From a terminal, run the following commands:</span></span>

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

<span data-ttu-id="807f1-139">Önceki komutlar bir gRPC hizmeti oluşturmak için [.NET Core CLI](/dotnet/core/tools/dotnet) kullanın.</span><span class="sxs-lookup"><span data-stu-id="807f1-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="807f1-140">Projeyi açın</span><span class="sxs-lookup"><span data-stu-id="807f1-140">Open the project</span></span>

<span data-ttu-id="807f1-141">Visual Studio'dan **Dosya** > **Aç'ı**seçin ve *ardından GrpcGreeter.csproj* dosyasını seçin.</span><span class="sxs-lookup"><span data-stu-id="807f1-141">From Visual Studio, select **File** > **Open**, and then select the *GrpcGreeter.csproj* file.</span></span>

---

### <a name="run-the-service"></a><span data-ttu-id="807f1-142">Hizmeti çalıştırma</span><span class="sxs-lookup"><span data-stu-id="807f1-142">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="807f1-143">Günlükler, hizmeti dinlerken `https://localhost:5001`gösterir.</span><span class="sxs-lookup"><span data-stu-id="807f1-143">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="807f1-144">gRPC [şablonu, Aktarım Katmanı Güvenliği 'ni (TLS)](https://tools.ietf.org/html/rfc5246)kullanacak şekilde yapılandırıldı.</span><span class="sxs-lookup"><span data-stu-id="807f1-144">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="807f1-145">gRPC istemcilerinin sunucuyu aramak için HTTPS'yi kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="807f1-145">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="807f1-146">macOS, TLS ile Core gRPC ASP.NET desteklemez.</span><span class="sxs-lookup"><span data-stu-id="807f1-146">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="807f1-147">macOS'ta gRPC hizmetlerini başarıyla çalıştırmak için ek yapılandırma gereklidir.</span><span class="sxs-lookup"><span data-stu-id="807f1-147">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="807f1-148">Daha fazla bilgi için bkz: [macOS'ta Core gRPC ASP.NET başlatılamıyor.](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)</span><span class="sxs-lookup"><span data-stu-id="807f1-148">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="807f1-149">Proje dosyalarını inceleyin</span><span class="sxs-lookup"><span data-stu-id="807f1-149">Examine the project files</span></span>

<span data-ttu-id="807f1-150">*GrpcGreeter* proje dosyaları:</span><span class="sxs-lookup"><span data-stu-id="807f1-150">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="807f1-151">*greet.proto* &ndash; *Protos/greet.proto* dosyası gRPC'yi `Greeter` tanımlar ve gRPC sunucu varlıklarını oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="807f1-151">*greet.proto* &ndash; The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="807f1-152">Daha fazla bilgi için [gRPC'ye Giriş 'e](xref:grpc/index)bakın.</span><span class="sxs-lookup"><span data-stu-id="807f1-152">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="807f1-153">*Hizmetler* klasörü: `Greeter` Hizmetin uygulanmasını içerir.</span><span class="sxs-lookup"><span data-stu-id="807f1-153">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="807f1-154">*appSettings.json* &ndash; Kestrel tarafından kullanılan protokol gibi yapılandırma verilerini içerir.</span><span class="sxs-lookup"><span data-stu-id="807f1-154">*appSettings.json* &ndash; Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="807f1-155">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="807f1-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="807f1-156">*Program.cs* &ndash; gRPC hizmetinin giriş noktasını içerir.</span><span class="sxs-lookup"><span data-stu-id="807f1-156">*Program.cs* &ndash; Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="807f1-157">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="807f1-157">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="807f1-158">*Startup.cs* &ndash; Uygulama davranışını yapılandıran kod içerir.</span><span class="sxs-lookup"><span data-stu-id="807f1-158">*Startup.cs* &ndash; Contains code that configures app behavior.</span></span> <span data-ttu-id="807f1-159">Daha fazla bilgi için [Uygulama başlangıç](xref:fundamentals/startup)bilgisine bakın.</span><span class="sxs-lookup"><span data-stu-id="807f1-159">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="807f1-160">bir .NET konsol uygulamasında gRPC istemcisini oluşturma</span><span class="sxs-lookup"><span data-stu-id="807f1-160">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="807f1-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="807f1-161">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="807f1-162">Visual Studio'nun ikinci bir örneğini açın ve **yeni bir proje oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="807f1-162">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="807f1-163">Yeni **bir proje oluştur** iletişim kutusunda **Konsol Uygulaması'nı (.NET Core)** seçin ve **İleri'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="807f1-163">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="807f1-164">**Ad** metin kutusuna **GrpcGreeterClient'ı** girin ve **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="807f1-164">In the **Name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="807f1-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="807f1-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="807f1-166">Entegre [terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="807f1-166">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="807f1-167">Dizinleri`cd`( ) projeyi içeren bir klasörle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="807f1-167">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="807f1-168">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="807f1-168">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="807f1-169">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="807f1-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="807f1-170">Mac için Visual Studio for [Mac'i kullanarak macOS'ta tam bir .NET Core çözümü oluşturarak](/dotnet/core/tutorials/using-on-mac-vs-full-solution) *GrpcGreeterClient*adında bir konsol uygulaması oluşturma yönergelerini izleyin.</span><span class="sxs-lookup"><span data-stu-id="807f1-170">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="807f1-171">Gerekli paketleri ekleme</span><span class="sxs-lookup"><span data-stu-id="807f1-171">Add required packages</span></span>

<span data-ttu-id="807f1-172">gRPC istemci projesi aşağıdaki paketleri gerektirir:</span><span class="sxs-lookup"><span data-stu-id="807f1-172">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="807f1-173">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), .NET Çekirdek istemcisi içerir.</span><span class="sxs-lookup"><span data-stu-id="807f1-173">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="807f1-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), C# için protobuf mesaj API'leri içerir.</span><span class="sxs-lookup"><span data-stu-id="807f1-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="807f1-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), protobuf dosyaları için C# araç desteği içerir.</span><span class="sxs-lookup"><span data-stu-id="807f1-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="807f1-176">Takım paketi çalışma zamanında gerekli değildir, bu nedenle bağımlılık `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="807f1-176">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="807f1-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="807f1-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="807f1-178">Paketleri Paket Yöneticisi Konsolu (PMC) veya NuGet Paketlerini Yönet'i kullanarak yükleyin.</span><span class="sxs-lookup"><span data-stu-id="807f1-178">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="807f1-179">Paketleri yüklemek için PMC seçeneği</span><span class="sxs-lookup"><span data-stu-id="807f1-179">PMC option to install packages</span></span>

* <span data-ttu-id="807f1-180">Visual Studio'dan **Araçlar** > **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu'nu** seçin</span><span class="sxs-lookup"><span data-stu-id="807f1-180">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="807f1-181">Paket **Yöneticisi Konsolu** penceresinden, dizinleri `cd GrpcGreeterClient` *GrpcGreeterClient.csproj* dosyalarını içeren klasöre değiştirmek için çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="807f1-181">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="807f1-182">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="807f1-182">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="807f1-183">Paketleri yüklemek için NuGet Paketlerini yönetme seçeneğini yönetin</span><span class="sxs-lookup"><span data-stu-id="807f1-183">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="807f1-184">**Solution Explorer** > **Manage NuGet Paketleri'nde** projeye sağ tıklayın</span><span class="sxs-lookup"><span data-stu-id="807f1-184">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="807f1-185">**Gözat** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="807f1-185">Select the **Browse** tab.</span></span>
* <span data-ttu-id="807f1-186">Arama kutusuna **Grpc.Net.Client** girin.</span><span class="sxs-lookup"><span data-stu-id="807f1-186">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="807f1-187">Gözat sekmesinden **Grpc.Net.Client** **paketini** seçin ve **Yükle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="807f1-187">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="807f1-188">Için `Google.Protobuf` tekrarlayın ve `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="807f1-188">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="807f1-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="807f1-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="807f1-190">**Entegre Terminal'den**aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="807f1-190">Run the following commands from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="807f1-191">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="807f1-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="807f1-192">**Çözüm Pad** > **Ekle Paketleri'ndeki** **Paketler** klasörüne sağ tıklayın</span><span class="sxs-lookup"><span data-stu-id="807f1-192">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="807f1-193">Arama kutusuna **Grpc.Net.Client** girin.</span><span class="sxs-lookup"><span data-stu-id="807f1-193">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="807f1-194">Sonuç bölmesinden **Grpc.Net.Client** paketini seçin ve **Paket Ekle'yi** seçin</span><span class="sxs-lookup"><span data-stu-id="807f1-194">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="807f1-195">Için `Google.Protobuf` tekrarlayın ve `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="807f1-195">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="807f1-196">greet.proto ekle</span><span class="sxs-lookup"><span data-stu-id="807f1-196">Add greet.proto</span></span>

* <span data-ttu-id="807f1-197">gRPC istemci projesinde bir *Protos* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="807f1-197">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="807f1-198">*Protos\greet.proto* dosyasını gRPC Greeter hizmetinden gRPC istemci projesine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="807f1-198">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="807f1-199">*GrpcGreeterClient.csproj* proje dosyasını edin:</span><span class="sxs-lookup"><span data-stu-id="807f1-199">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studio"></a>[<span data-ttu-id="807f1-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="807f1-200">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="807f1-201">Projeyi sağ tıklatın ve **Proje Dosyasını Edit'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="807f1-201">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-code"></a>[<span data-ttu-id="807f1-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="807f1-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="807f1-203">*GrpcGreeterClient.csproj* dosyasını seçin.</span><span class="sxs-lookup"><span data-stu-id="807f1-203">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="807f1-204">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="807f1-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="807f1-205">Projeyi sağ tıklatın ve **Araçları** > **Dosyayı Düzelt'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="807f1-205">Right-click the project and select **Tools** > **Edit File**.</span></span>

  ---

* <span data-ttu-id="807f1-206">`<Protobuf>` *Greet.proto* dosyasına başvuran bir öğe grubu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="807f1-206">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="807f1-207">Greeter istemcisini oluşturma</span><span class="sxs-lookup"><span data-stu-id="807f1-207">Create the Greeter client</span></span>

<span data-ttu-id="807f1-208">Ad alanındaki türleri oluşturmak için `GrpcGreeter` projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="807f1-208">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="807f1-209">Türler, `GrpcGreeter` yapı işlemi tarafından otomatik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="807f1-209">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="807f1-210">gRPC *istemciProgram.cs* dosyasını aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="807f1-210">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="807f1-211">*Program.cs* gRPC istemcisinin giriş noktasını ve mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="807f1-211">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="807f1-212">Greeter istemcisi tarafından oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="807f1-212">The Greeter client is created by:</span></span>

* <span data-ttu-id="807f1-213">gRPC hizmetine bağlantı oluşturmak için bilgileri içeren bir `GrpcChannel` anında.</span><span class="sxs-lookup"><span data-stu-id="807f1-213">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="807f1-214">Greeter `GrpcChannel` istemcisini oluşturmak için kullanma:</span><span class="sxs-lookup"><span data-stu-id="807f1-214">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="807f1-215">Greeter istemcisi asynchronous `SayHello` yöntemiçağırır.</span><span class="sxs-lookup"><span data-stu-id="807f1-215">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="807f1-216">Aramanın `SayHello` sonucu görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="807f1-216">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="807f1-217">gRPC Karşılama hizmeti ile gRPC istemcisini test edin</span><span class="sxs-lookup"><span data-stu-id="807f1-217">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="807f1-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="807f1-218">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="807f1-219">Greeter hizmetinde, `Ctrl+F5` hata ayıklayıcı olmadan sunucuyu başlatmak için basın.</span><span class="sxs-lookup"><span data-stu-id="807f1-219">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="807f1-220">Projede, `GrpcGreeterClient` hata `Ctrl+F5` ayıklama olmadan istemciyi başlatmak için basın.</span><span class="sxs-lookup"><span data-stu-id="807f1-220">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="807f1-221">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="807f1-221">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="807f1-222">Greeter hizmetini başlatın.</span><span class="sxs-lookup"><span data-stu-id="807f1-222">Start the Greeter service.</span></span>
* <span data-ttu-id="807f1-223">İstemciyi başlatın.</span><span class="sxs-lookup"><span data-stu-id="807f1-223">Start the client.</span></span>


# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="807f1-224">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="807f1-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="807f1-225">Greeter hizmetini başlatın.</span><span class="sxs-lookup"><span data-stu-id="807f1-225">Start the Greeter service.</span></span>
* <span data-ttu-id="807f1-226">İstemciyi başlatın.</span><span class="sxs-lookup"><span data-stu-id="807f1-226">Start the client.</span></span>

---

<span data-ttu-id="807f1-227">İstemci, hizmete, *greeterClient*adını içeren bir ileti içeren bir karşılama gönderir.</span><span class="sxs-lookup"><span data-stu-id="807f1-227">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="807f1-228">Hizmet yanıt olarak "Merhaba GreeterClient" iletisini gönderir.</span><span class="sxs-lookup"><span data-stu-id="807f1-228">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="807f1-229">"Merhaba GreeterClient" yanıtı komut isteminde görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="807f1-229">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="807f1-230">gRPC hizmeti komut istemine yazılan günlüklerde başarılı aramanın ayrıntılarını kaydeder:</span><span class="sxs-lookup"><span data-stu-id="807f1-230">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

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
> <span data-ttu-id="807f1-231">Bu makaledeki kod, gRPC hizmetini güvence altına almak için ASP.NET Çekirdek HTTPS geliştirme sertifikasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="807f1-231">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="807f1-232">İstemci iletide `The remote certificate is invalid according to the validation procedure.`başarısız olursa, geliştirme sertifikasına güvenilmez.</span><span class="sxs-lookup"><span data-stu-id="807f1-232">If the client fails with the message `The remote certificate is invalid according to the validation procedure.`, the development certificate is not trusted.</span></span> <span data-ttu-id="807f1-233">Bu sorunu gidermek için yönergeleri gidermek için Windows [ve macOS'taki ASP.NET Çekirdek HTTPS geliştirme sertifikasına güven'e](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)bakın.</span><span class="sxs-lookup"><span data-stu-id="807f1-233">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="807f1-234">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="807f1-234">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
