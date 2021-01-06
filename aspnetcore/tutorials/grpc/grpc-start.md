---
title: ASP.NET Core .NET Core gRPC istemcisi ve sunucusu oluşturma
author: juntaoluo
description: Bu öğreticide, ASP.NET Core bir gRPC hizmeti ve gRPC istemcisinin nasıl oluşturulacağı gösterilmektedir. GRPC hizmeti projesi oluşturmayı, Proto dosyasını düzenlemeyi ve çift yönlü akış araması eklemeyi öğrenin.
ms.author: johluo
ms.date: 10/23/2020
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
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 9388a2f814008ebb50171f85b8baccf6dadfac27
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057030"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="02e1a-104">Öğretici: ASP.NET Core bir gRPC istemcisi ve sunucusu oluşturma</span><span class="sxs-lookup"><span data-stu-id="02e1a-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="02e1a-105">[John Luo](https://github.com/juntaoluo) tarafından</span><span class="sxs-lookup"><span data-stu-id="02e1a-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="02e1a-106">Bu öğreticide, bir .NET Core [GRPC](https://grpc.io/docs/guides/) istemcisinin ve bir ASP.NET Core GRPC sunucusunun nasıl oluşturulacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="02e1a-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="02e1a-107">Sonda, gRPC Greeter hizmeti ile iletişim kuran bir gRPC istemcisine sahip olacaksınız.</span><span class="sxs-lookup"><span data-stu-id="02e1a-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="02e1a-108">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="02e1a-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="02e1a-109">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="02e1a-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="02e1a-110">GRPC sunucusu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="02e1a-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="02e1a-111">GRPC istemcisi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="02e1a-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="02e1a-112">GRPC istemci hizmetini gRPC Greeter hizmeti ile test edin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="02e1a-113">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="02e1a-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02e1a-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02e1a-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="02e1a-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02e1a-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="02e1a-116">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02e1a-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="02e1a-117">Mac için Visual Studio Sürüm 8,7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="02e1a-117">Visual Studio for Mac version 8.7 or later</span></span>](/visualstudio/releasenotes/vs2019-mac-relnotes)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]
---

## <a name="create-a-grpc-service"></a><span data-ttu-id="02e1a-118">GRPC hizmeti oluşturma</span><span class="sxs-lookup"><span data-stu-id="02e1a-118">Create a gRPC service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02e1a-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02e1a-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="02e1a-120">Visual Studio 'Yu başlatın ve **Yeni proje oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-120">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="02e1a-121">Alternatif olarak, Visual Studio **Dosya** menüsünden **Yeni**  >  **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-121">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="02e1a-122">**Yeni proje oluştur** Iletişim kutusunda **GRPC hizmeti** ' ni seçin ve **İleri**' yi seçin:</span><span class="sxs-lookup"><span data-stu-id="02e1a-122">In the **Create a new project** dialog, select **gRPC Service** and select **Next**:</span></span>

  ![Visual Studio 'da yeni bir proje iletişim kutusu oluşturma](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="02e1a-124">Projeyi **Grpcgreeter** olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="02e1a-124">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="02e1a-125">Kodu kopyaladığınızda ve yapıştırdığınızda ad alanlarının eşleşmesi için, proje *Grpcgreeter* adında bir ad vermek önemlidir.</span><span class="sxs-lookup"><span data-stu-id="02e1a-125">It's important to name the project *GrpcGreeter* so the namespaces match when you copy and paste code.</span></span>
* <span data-ttu-id="02e1a-126">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-126">Select **Create**.</span></span>
* <span data-ttu-id="02e1a-127">**Yeni bir gRPC hizmeti oluştur** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="02e1a-127">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="02e1a-128">**GRPC hizmeti** şablonu seçilidir.</span><span class="sxs-lookup"><span data-stu-id="02e1a-128">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="02e1a-129">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-129">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="02e1a-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02e1a-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="02e1a-131">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="02e1a-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="02e1a-132">Dizinleri ( `cd` ) proje için bir klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-132">Change directories (`cd`) to a folder for the project.</span></span>
* <span data-ttu-id="02e1a-133">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="02e1a-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="02e1a-134">`dotnet new`Komut *Grpcgreeter* klasöründe yeni bir GRPC hizmeti oluşturur.</span><span class="sxs-lookup"><span data-stu-id="02e1a-134">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="02e1a-135">`code`Komut, Visual Studio Code yeni bir örneğinde *Grpcgreeter* klasörünü açar.</span><span class="sxs-lookup"><span data-stu-id="02e1a-135">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="02e1a-136">**Gerekli varlıkların derlenmesi ve hata ayıklaması için ' GrpcGreeter ' içinde eksik bir iletişim kutusu görüntülenir. Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="02e1a-136">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="02e1a-137">**Evet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-137">Select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="02e1a-138">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02e1a-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="02e1a-139">Mac için Visual Studio başlatın ve **Yeni proje oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-139">Start Visual Studio for Mac and select **Create a new project**.</span></span> <span data-ttu-id="02e1a-140">Alternatif olarak, Visual Studio **Dosya** menüsünden **Yeni**  >  **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-140">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="02e1a-141">**Yeni proje oluştur** iletişim kutusunda **Web ve konsol**  >  **uygulaması**  >  **GRPC hizmeti** ' ni seçin ve **İleri**' yi seçin:</span><span class="sxs-lookup"><span data-stu-id="02e1a-141">In the **Create a new project** dialog, select **Web and Console** > **App** > **gRPC Service** and select **Next**:</span></span>

  ![MacOS 'ta yeni bir proje iletişim kutusu oluşturma](~/tutorials/grpc/grpc-start/static/cnp-mac.png)

* <span data-ttu-id="02e1a-143">Hedef çerçeve için **.NET Core 3,1** ' ı seçin ve **İleri ' yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-143">Select **.NET Core 3.1** for the target framework and select **Next**.</span></span>
* <span data-ttu-id="02e1a-144">Projeyi **Grpcgreeter** olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="02e1a-144">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="02e1a-145">Kodu kopyaladığınızda ve yapıştırdığınızda ad alanlarının eşleşmesi için, proje *Grpcgreeter* adında bir ad vermek önemlidir.</span><span class="sxs-lookup"><span data-stu-id="02e1a-145">It's important to name the project *GrpcGreeter* so the namespaces match when you copy and paste code.</span></span>
* <span data-ttu-id="02e1a-146">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-146">Select **Create**.</span></span>
---

### <a name="run-the-service"></a><span data-ttu-id="02e1a-147">Hizmeti çalıştırma</span><span class="sxs-lookup"><span data-stu-id="02e1a-147">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="02e1a-148">Günlükler hizmeti dinlediği hizmetini gösterir `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="02e1a-148">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="02e1a-149">GRPC şablonu, [Aktarım Katmanı Güvenliği (TLS)](https://tools.ietf.org/html/rfc5246)kullanmak üzere yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="02e1a-149">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="02e1a-150">gRPC istemcilerinin, sunucuyu çağırmak için HTTPS kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="02e1a-150">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="02e1a-151">macOS, TLS ile ASP.NET Core gRPC 'yi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="02e1a-151">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="02e1a-152">MacOS 'ta gRPC hizmetlerini başarıyla çalıştırmak için ek yapılandırma gerekir.</span><span class="sxs-lookup"><span data-stu-id="02e1a-152">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="02e1a-153">Daha fazla bilgi için bkz. [macOS üzerinde gRPC uygulaması ASP.NET Core başlatılamıyor](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="02e1a-153">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="02e1a-154">Proje dosyalarını inceleyin</span><span class="sxs-lookup"><span data-stu-id="02e1a-154">Examine the project files</span></span>

<span data-ttu-id="02e1a-155">*Grpcgreeter* proje dosyaları:</span><span class="sxs-lookup"><span data-stu-id="02e1a-155">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="02e1a-156">*Greet. proto*: *prototips/Greet. proto* dosyası `Greeter` gRPC 'yi tanımlar ve GRPC sunucu varlıklarını oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="02e1a-156">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="02e1a-157">Daha fazla bilgi için bkz. [gRPC 'ye giriş](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="02e1a-157">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="02e1a-158">*Hizmetler* klasörü: hizmetin uygulamasını içerir `Greeter` .</span><span class="sxs-lookup"><span data-stu-id="02e1a-158">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="02e1a-159">*appSettings.js*: Kestrel tarafından kullanılan protokol gibi yapılandırma verilerini içerir.</span><span class="sxs-lookup"><span data-stu-id="02e1a-159">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="02e1a-160">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="02e1a-160">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="02e1a-161">*Program.cs*: GRPC hizmeti için giriş noktasını içerir.</span><span class="sxs-lookup"><span data-stu-id="02e1a-161">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="02e1a-162">Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="02e1a-162">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="02e1a-163">*Startup.cs*: uygulama davranışını yapılandıran kodu içerir.</span><span class="sxs-lookup"><span data-stu-id="02e1a-163">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="02e1a-164">Daha fazla bilgi için bkz. [uygulama başlatma](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="02e1a-164">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="02e1a-165">Bir .NET konsol uygulamasında gRPC istemcisini oluşturma</span><span class="sxs-lookup"><span data-stu-id="02e1a-165">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02e1a-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02e1a-166">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="02e1a-167">Visual Studio 'nun ikinci bir örneğini açın ve **Yeni proje oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-167">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="02e1a-168">**Yeni proje oluştur** Iletişim kutusunda **konsol uygulaması (.NET Core)** öğesini seçin ve **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-168">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="02e1a-169">**Proje adı** metin kutusuna **Grpcgreeterclient** girin ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-169">In the **Project name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="02e1a-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02e1a-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="02e1a-171">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="02e1a-171">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="02e1a-172">Dizinleri ( `cd` ) proje için bir klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-172">Change directories (`cd`) to a folder for the project.</span></span>
* <span data-ttu-id="02e1a-173">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="02e1a-173">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="02e1a-174">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02e1a-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="02e1a-175">*Grpcgreeterclient* adlı bir konsol uygulaması oluşturmak için [Mac için Visual Studio kullanarak MacOS 'ta kapsamlı bir .NET Core çözümü oluşturma](/dotnet/core/tutorials/using-on-mac-vs-full-solution) konusundaki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-175">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="02e1a-176">Gerekli paketleri Ekle</span><span class="sxs-lookup"><span data-stu-id="02e1a-176">Add required packages</span></span>

<span data-ttu-id="02e1a-177">GRPC istemci projesi aşağıdaki paketleri gerektirir:</span><span class="sxs-lookup"><span data-stu-id="02e1a-177">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="02e1a-178">.NET Core istemcisini içeren [GRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client).</span><span class="sxs-lookup"><span data-stu-id="02e1a-178">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="02e1a-179">C# için prototipsiz ileti API 'Leri içeren [Google. Protoarabellek](https://www.nuget.org/packages/Google.Protobuf/).</span><span class="sxs-lookup"><span data-stu-id="02e1a-179">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="02e1a-180">Prototipleme dosyaları için C# araçları desteği içeren [GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="02e1a-180">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="02e1a-181">Araç çalışma zamanında gerekli değildir, bu nedenle bağımlılık ile işaretlenir `PrivateAssets="All"` .</span><span class="sxs-lookup"><span data-stu-id="02e1a-181">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02e1a-182">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02e1a-182">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="02e1a-183">Paket Yöneticisi Konsolu (PMC) veya NuGet Paketlerini Yönet ' i kullanarak paketleri yükler.</span><span class="sxs-lookup"><span data-stu-id="02e1a-183">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="02e1a-184">Paket yüklemek için PMC seçeneği</span><span class="sxs-lookup"><span data-stu-id="02e1a-184">PMC option to install packages</span></span>

* <span data-ttu-id="02e1a-185">Visual Studio 'da **Araçlar**  >  **NuGet Paket Yöneticisi**  >  **Paket Yöneticisi konsolu** ' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-185">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="02e1a-186">**Paket Yöneticisi konsol** penceresinde, `cd GrpcGreeterClient` dizini *Grpcgreeterclient. csproj* dosyalarını içeren klasöre değiştirmek için komutunu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="02e1a-186">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="02e1a-187">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="02e1a-187">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="02e1a-188">Paket yüklemek için NuGet Paketlerini Yönet seçeneği</span><span class="sxs-lookup"><span data-stu-id="02e1a-188">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="02e1a-189">**Çözüm Gezgini**  >  **NuGet Paketlerini Yönet**' de projeye sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="02e1a-189">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="02e1a-190">**Gözat** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-190">Select the **Browse** tab.</span></span>
* <span data-ttu-id="02e1a-191">Arama kutusuna **GRPC .net. Client** girin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-191">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="02e1a-192">**Araştır** sekmesinden **GRPC .net. Client** paketini seçin ve ardından **Install**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-192">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="02e1a-193">Ve için `Google.Protobuf` tekrarlayın `Grpc.Tools` .</span><span class="sxs-lookup"><span data-stu-id="02e1a-193">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="02e1a-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02e1a-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="02e1a-195">**Tümleşik terminalden** aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="02e1a-195">Run the following commands from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="02e1a-196">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02e1a-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="02e1a-197">**Çözüm bölmesi** **Grpcgreeterclient** projesi öğesine sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-197">Right-click **GrpcGreeterClient** project in the **Solution Pad** and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="02e1a-198">Arama kutusuna **GRPC .net. Client** girin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-198">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="02e1a-199">Sonuçlar bölmesinden **GRPC .net. Client** paketini seçin ve **paket Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-199">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**.</span></span>
* <span data-ttu-id="02e1a-200">**Lisansı kabul et** Iletişim kutusunda **kabul et** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-200">Select the **Accept** button on the **Accept License** dialog.</span></span>
* <span data-ttu-id="02e1a-201">Ve için `Google.Protobuf` tekrarlayın `Grpc.Tools` .</span><span class="sxs-lookup"><span data-stu-id="02e1a-201">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="02e1a-202">Greet. proto Ekle</span><span class="sxs-lookup"><span data-stu-id="02e1a-202">Add greet.proto</span></span>

* <span data-ttu-id="02e1a-203">GRPC istemci projesinde bir *Prototips* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="02e1a-203">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="02e1a-204">*Protos\bilgisem. proto* dosyasını GRPC Greeter hizmetinden GRPC istemci projesine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="02e1a-204">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="02e1a-205">Dosyanın içindeki ad alanını `greet.proto` projenin ad alanına güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="02e1a-205">Update the namespace inside the `greet.proto` file to the project's namespace:</span></span>

  ```
  option csharp_namespace = "GrpcGreeterClient";
  ```

* <span data-ttu-id="02e1a-206">*Grpcgreeterclient. csproj* proje dosyasını düzenleyin:</span><span class="sxs-lookup"><span data-stu-id="02e1a-206">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studio"></a>[<span data-ttu-id="02e1a-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02e1a-207">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="02e1a-208">Projeye sağ tıklayın ve **Proje dosyasını Düzenle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-208">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-code"></a>[<span data-ttu-id="02e1a-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02e1a-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="02e1a-210">*Grpcgreeterclient. csproj* dosyasını seçin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-210">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="02e1a-211">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02e1a-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="02e1a-212">Projeye sağ tıklayın ve **Proje dosyasını Düzenle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="02e1a-212">Right-click the project and select **Edit Project File**.</span></span>

  ---

* <span data-ttu-id="02e1a-213">`<Protobuf>` *Greet. proto* dosyasına başvuran bir öğesi olan bir öğe grubu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="02e1a-213">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="02e1a-214">Greeter istemcisini oluşturma</span><span class="sxs-lookup"><span data-stu-id="02e1a-214">Create the Greeter client</span></span>

<span data-ttu-id="02e1a-215">Ad alanındaki türleri oluşturmak için istemci projesi oluşturun `GrpcGreeter` .</span><span class="sxs-lookup"><span data-stu-id="02e1a-215">Build the client project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="02e1a-216">`GrpcGreeter`Türler yapı işlemi tarafından otomatik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="02e1a-216">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="02e1a-217">GRPC Client *program.cs* dosyasını aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="02e1a-217">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="02e1a-218">*Program.cs* , GRPC istemcisinin giriş noktasını ve mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="02e1a-218">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="02e1a-219">Greeter istemcisi şu şekilde oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="02e1a-219">The Greeter client is created by:</span></span>

* <span data-ttu-id="02e1a-220">`GrpcChannel`GRPC hizmetine bağlantı oluşturmak için bilgileri içeren bir örneği oluşturma.</span><span class="sxs-lookup"><span data-stu-id="02e1a-220">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="02e1a-221">`GrpcChannel`Greeter istemcisini oluşturmak için kullanarak:</span><span class="sxs-lookup"><span data-stu-id="02e1a-221">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="02e1a-222">Greeter istemcisi zaman uyumsuz yöntemi çağırır `SayHello` .</span><span class="sxs-lookup"><span data-stu-id="02e1a-222">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="02e1a-223">`SayHello`Çağrının sonucu görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="02e1a-223">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="02e1a-224">GRPC istemcisini gRPC Greeter hizmeti ile test etme</span><span class="sxs-lookup"><span data-stu-id="02e1a-224">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02e1a-225">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02e1a-225">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="02e1a-226">Greeter hizmetinde, `Ctrl+F5` hata ayıklayıcı olmadan sunucuyu başlatmak için tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="02e1a-226">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="02e1a-227">`GrpcGreeterClient`Projede, `Ctrl+F5` hata ayıklayıcı olmadan istemcisini başlatmak için tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="02e1a-227">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="02e1a-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02e1a-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="02e1a-229">Greeter hizmetini başlatın.</span><span class="sxs-lookup"><span data-stu-id="02e1a-229">Start the Greeter service.</span></span>
* <span data-ttu-id="02e1a-230">İstemcisini başlatın.</span><span class="sxs-lookup"><span data-stu-id="02e1a-230">Start the client.</span></span>


# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="02e1a-231">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02e1a-231">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="02e1a-232">[MacOS geçici çözümde daha önce bahsedilen http/2 TLS sorunu](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)nedeniyle, istemcideki kanal adresini "" olarak güncelleştirmeniz gerekir http://localhost:5000 .</span><span class="sxs-lookup"><span data-stu-id="02e1a-232">Due to the previously mentioned [HTTP/2 TLS issue on macOS workaround](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos), you'll need to update the channel address in the client to "http://localhost:5000".</span></span> <span data-ttu-id="02e1a-233">Şu satırı okumak için 13 **. Grpcgreeterclient/program. cs** güncelleştirmesi:</span><span class="sxs-lookup"><span data-stu-id="02e1a-233">Update line 13 of **GrpcGreeterClient/Program.cs** to read:</span></span>
  ```csharp
  using var channel = GrpcChannel.ForAddress("http://localhost:5000");
  ``` 
* <span data-ttu-id="02e1a-234">Greeter hizmetini başlatın.</span><span class="sxs-lookup"><span data-stu-id="02e1a-234">Start the Greeter service.</span></span>
* <span data-ttu-id="02e1a-235">İstemcisini başlatın.</span><span class="sxs-lookup"><span data-stu-id="02e1a-235">Start the client.</span></span>

---

<span data-ttu-id="02e1a-236">İstemci, adı *Greeterclient* olan bir iletiyle hizmete bir tebrik gönderir.</span><span class="sxs-lookup"><span data-stu-id="02e1a-236">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="02e1a-237">Hizmet, "Hello GreeterClient" iletisini yanıt olarak gönderir.</span><span class="sxs-lookup"><span data-stu-id="02e1a-237">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="02e1a-238">Komut isteminde "Hello GreeterClient" yanıtı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="02e1a-238">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="02e1a-239">GRPC hizmeti, komut istemine yazılan günlüklerde başarılı çağrının ayrıntılarını kaydeder:</span><span class="sxs-lookup"><span data-stu-id="02e1a-239">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

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
> <span data-ttu-id="02e1a-240">Bu makaledeki kod, gRPC hizmetini güvenli hale getirmek için ASP.NET Core HTTPS geliştirme sertifikası gerektirir.</span><span class="sxs-lookup"><span data-stu-id="02e1a-240">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="02e1a-241">.NET gRPC istemcisi iletiyle başarısız olursa `The remote certificate is invalid according to the validation procedure.` veya `The SSL connection could not be established.` geliştirme sertifikası güvenilir olmaz.</span><span class="sxs-lookup"><span data-stu-id="02e1a-241">If the .NET gRPC client fails with the message `The remote certificate is invalid according to the validation procedure.` or `The SSL connection could not be established.`, the development certificate isn't trusted.</span></span> <span data-ttu-id="02e1a-242">Bu sorunu onarmak için bkz. [Güvenilmeyen/geçersiz sertifikayla gRPC hizmeti çağırma](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span><span class="sxs-lookup"><span data-stu-id="02e1a-242">To fix this issue, see [Call a gRPC service with an untrusted/invalid certificate](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="02e1a-243">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="02e1a-243">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
