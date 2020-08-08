---
title: ASP.NET Core ile LibMan CLı kullanın
author: scottaddie
description: ASP.NET Core projesindeki LibMan CLı 'yı nasıl kullanacağınızı öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/libman/libman-cli
ms.openlocfilehash: 6e1ab9c540e1714f2f8cd6e6f2603e4d589a7d2b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013482"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a><span data-ttu-id="e4856-103">ASP.NET Core ile LibMan CLı kullanın</span><span class="sxs-lookup"><span data-stu-id="e4856-103">Use the LibMan CLI with ASP.NET Core</span></span>

<span data-ttu-id="e4856-104">[Scott Ade](https://twitter.com/Scott_Addie) tarafından</span><span class="sxs-lookup"><span data-stu-id="e4856-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="e4856-105">[Libman](xref:client-side/libman/index) CLI, .NET Core 'un desteklendiği her yerde desteklenen platformlar arası bir araçtır.</span><span class="sxs-lookup"><span data-stu-id="e4856-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e4856-106">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="e4856-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="e4856-107">Yükleme</span><span class="sxs-lookup"><span data-stu-id="e4856-107">Installation</span></span>

<span data-ttu-id="e4856-108">LibMan CLı 'yı yüklemek için:</span><span class="sxs-lookup"><span data-stu-id="e4856-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="e4856-109">[.NET Core küresel aracı](/dotnet/core/tools/global-tools#install-a-global-tool) , [Microsoft. Web. LibraryManager. CLI](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet paketinden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="e4856-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="e4856-110">Belirli bir NuGet paket kaynağından LibMan CLı 'yı yüklemek için:</span><span class="sxs-lookup"><span data-stu-id="e4856-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="e4856-111">Yukarıdaki örnekte, yerel Windows makinenin *C:\Temp\Microsoft.Web.LibraryManager.cli.1.0.94-g606058a278.nupkg* dosyasından bir .NET Core genel aracı yüklenir.</span><span class="sxs-lookup"><span data-stu-id="e4856-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="e4856-112">Kullanım</span><span class="sxs-lookup"><span data-stu-id="e4856-112">Usage</span></span>

<span data-ttu-id="e4856-113">CLı başarıyla yüklendikten sonra aşağıdaki komut kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="e4856-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="e4856-114">Yüklü CLı sürümünü görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="e4856-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="e4856-115">Kullanılabilir CLı komutlarını görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="e4856-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="e4856-116">Yukarıdaki komut aşağıdakine benzer bir çıktı görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e4856-116">The preceding command displays output similar to the following:</span></span>

```console
 1.0.163+g45474d37ed

Usage: libman [options] [command]

Options:
  --help|-h  Show help information
  --version  Show version information

Commands:
  cache      List or clean libman cache contents
  clean      Deletes all library files defined in libman.json from the project
  init       Create a new libman.json
  install    Add a library definition to the libman.json file, and download the 
             library to the specified location
  restore    Downloads all files from provider and saves them to specified 
             destination
  uninstall  Deletes all files for the specified library from their specified 
             destination, then removes the specified library definition from 
             libman.json
  update     Updates the specified library

Use "libman [command] --help" for more information about a command.
```

<span data-ttu-id="e4856-117">Aşağıdaki bölümlerde kullanılabilir CLı komutları ana hatlarıyla verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="e4856-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="e4856-118">Projedeki LibMan 'ı Başlat</span><span class="sxs-lookup"><span data-stu-id="e4856-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="e4856-119">`libman init`Bir dosya yoksa, komut dosyada bir *libman.js* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e4856-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="e4856-120">Dosya varsayılan öğe şablonu içeriğiyle oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e4856-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="e4856-121">Özeti</span><span class="sxs-lookup"><span data-stu-id="e4856-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="e4856-122">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="e4856-122">Options</span></span>

<span data-ttu-id="e4856-123">Komutu için aşağıdaki seçenekler kullanılabilir `libman init` :</span><span class="sxs-lookup"><span data-stu-id="e4856-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="e4856-124">Geçerli klasöre göreli bir yol.</span><span class="sxs-lookup"><span data-stu-id="e4856-124">A path relative to the current folder.</span></span> <span data-ttu-id="e4856-125">Kitaplık dosyaları, `destination` *üzerindelibman.js*bir kitaplık için tanımlı bir özellik yoksa bu konuma yüklenir.</span><span class="sxs-lookup"><span data-stu-id="e4856-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="e4856-126">`<PATH>`Değer, `defaultDestination` *üzerindelibman.js*özelliğine yazılır.</span><span class="sxs-lookup"><span data-stu-id="e4856-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="e4856-127">Belirli bir kitaplık için hiçbir sağlayıcı tanımlanmamışsa kullanılacak sağlayıcı.</span><span class="sxs-lookup"><span data-stu-id="e4856-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="e4856-128">`<PROVIDER>`Değer, `defaultProvider` *üzerindelibman.js*özelliğine yazılır.</span><span class="sxs-lookup"><span data-stu-id="e4856-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="e4856-129">`<PROVIDER>`Aşağıdaki değerlerden biriyle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="e4856-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="e4856-130">Örnekler</span><span class="sxs-lookup"><span data-stu-id="e4856-130">Examples</span></span>

<span data-ttu-id="e4856-131">Bir ASP.NET Core projesindeki dosyada *libman.js* oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="e4856-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="e4856-132">Proje köküne gidin.</span><span class="sxs-lookup"><span data-stu-id="e4856-132">Navigate to the project root.</span></span>
* <span data-ttu-id="e4856-133">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e4856-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="e4856-134">Varsayılan sağlayıcının adını yazın veya `Enter` varsayılan CDNJS sağlayıcısını kullanmak için ' a basın.</span><span class="sxs-lookup"><span data-stu-id="e4856-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="e4856-135">Geçerli değerler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="e4856-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![Libman başlatma komutu-varsayılan sağlayıcı](_static/libman-init-provider.png)

<span data-ttu-id="e4856-137">Aşağıdaki içeriğe sahip proje köküne bir *libman.js* dosyası eklenir:</span><span class="sxs-lookup"><span data-stu-id="e4856-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="e4856-138">Kitaplık dosyaları Ekle</span><span class="sxs-lookup"><span data-stu-id="e4856-138">Add library files</span></span>

<span data-ttu-id="e4856-139">`libman install`Komutu, kitaplık dosyalarını indirir ve projeye yükler.</span><span class="sxs-lookup"><span data-stu-id="e4856-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="e4856-140">Bir *libman.js* dosya yoksa eklenir.</span><span class="sxs-lookup"><span data-stu-id="e4856-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="e4856-141">Dosyadaki *libman.js* , kitaplık dosyaları için yapılandırma ayrıntılarını depolayacak şekilde değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="e4856-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="e4856-142">Özeti</span><span class="sxs-lookup"><span data-stu-id="e4856-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="e4856-143">Arguments</span><span class="sxs-lookup"><span data-stu-id="e4856-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="e4856-144">Yüklenecek kitaplığın adı.</span><span class="sxs-lookup"><span data-stu-id="e4856-144">The name of the library to install.</span></span> <span data-ttu-id="e4856-145">Bu ad, sürüm numarası gösterimi içerebilir (örneğin, `@1.2.0` ).</span><span class="sxs-lookup"><span data-stu-id="e4856-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="e4856-146">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="e4856-146">Options</span></span>

<span data-ttu-id="e4856-147">Komutu için aşağıdaki seçenekler kullanılabilir `libman install` :</span><span class="sxs-lookup"><span data-stu-id="e4856-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="e4856-148">Kitaplığın yükleneceği konum.</span><span class="sxs-lookup"><span data-stu-id="e4856-148">The location to install the library.</span></span> <span data-ttu-id="e4856-149">Belirtilmemişse, varsayılan konum kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e4856-149">If not specified, the default location is used.</span></span> <span data-ttu-id="e4856-150">`defaultDestination` *Üzerindelibman.js*hiçbir özellik belirtilmemişse, bu seçenek gereklidir.</span><span class="sxs-lookup"><span data-stu-id="e4856-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="e4856-151">Kitaplıktan yüklenecek dosyanın adını belirtin.</span><span class="sxs-lookup"><span data-stu-id="e4856-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="e4856-152">Belirtilmemişse, kitaplıktaki tüm dosyalar yüklenir.</span><span class="sxs-lookup"><span data-stu-id="e4856-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="e4856-153">`--files`Yüklenecek dosya başına bir seçenek belirtin.</span><span class="sxs-lookup"><span data-stu-id="e4856-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="e4856-154">Göreli yollar da desteklenir.</span><span class="sxs-lookup"><span data-stu-id="e4856-154">Relative paths are supported too.</span></span> <span data-ttu-id="e4856-155">Örneğin: `--files dist/browser/signalr.js`.</span><span class="sxs-lookup"><span data-stu-id="e4856-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="e4856-156">Kitaplık alımı için kullanılacak sağlayıcının adı.</span><span class="sxs-lookup"><span data-stu-id="e4856-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="e4856-157">`<PROVIDER>`Aşağıdaki değerlerden biriyle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="e4856-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="e4856-158">Belirtilmemişse, `defaultProvider` *libman.json* özelliği kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e4856-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="e4856-159">`defaultProvider` *Üzerindelibman.js*hiçbir özellik belirtilmemişse, bu seçenek gereklidir.</span><span class="sxs-lookup"><span data-stu-id="e4856-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="e4856-160">Örnekler</span><span class="sxs-lookup"><span data-stu-id="e4856-160">Examples</span></span>

<span data-ttu-id="e4856-161">Dosyasında aşağıdaki *libman.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="e4856-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="e4856-162">JQuery sürüm 3.2.1 *jquery.min.js* dosyasını CDNJS sağlayıcısını kullanarak *Wwwroot/Scripts/jQuery* klasörüne yüklemek için:</span><span class="sxs-lookup"><span data-stu-id="e4856-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="e4856-163">Dosyadaki *libman.js* aşağıdakine benzer:</span><span class="sxs-lookup"><span data-stu-id="e4856-163">The *libman.json* file resembles the following:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    }
  ]
}
```

<span data-ttu-id="e4856-164">Dosya sistemi sağlayıcısını kullanarak *C: \\ Temp \\ \\ contosocalendar* öğesinden *calendar.js* ve *Calendar. css* dosyalarını yüklemek için:</span><span class="sxs-lookup"><span data-stu-id="e4856-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="e4856-165">Aşağıdaki istem iki nedenden dolayı görünür:</span><span class="sxs-lookup"><span data-stu-id="e4856-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="e4856-166">Dosyadaki *libman.js* bir `defaultDestination` özellik içermiyor.</span><span class="sxs-lookup"><span data-stu-id="e4856-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="e4856-167">`libman install`Komut, `-d|--destination` seçeneğini içermez.</span><span class="sxs-lookup"><span data-stu-id="e4856-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![Libman Install komut-hedef](_static/libman-install-destination.png)

<span data-ttu-id="e4856-169">Varsayılan hedefi kabul ettikten sonra, dosyadaki *libman.js* aşağıdakine benzer:</span><span class="sxs-lookup"><span data-stu-id="e4856-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    },
    {
      "library": "C:\\temp\\contosoCalendar\\",
      "provider": "filesystem",
      "destination": "wwwroot/lib/contosoCalendar",
      "files": [
        "calendar.js",
        "calendar.css"
      ]
    }
  ]
}
```

## <a name="restore-library-files"></a><span data-ttu-id="e4856-170">Kitaplık dosyalarını geri yükleme</span><span class="sxs-lookup"><span data-stu-id="e4856-170">Restore library files</span></span>

<span data-ttu-id="e4856-171">`libman restore`Komut, *üzerindelibman.js*tanımlı kitaplık dosyalarını yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="e4856-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="e4856-172">Aşağıdaki kurallar geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="e4856-172">The following rules apply:</span></span>

* <span data-ttu-id="e4856-173">Proje kökünde *libman.js* dosya varsa, bir hata döndürülür.</span><span class="sxs-lookup"><span data-stu-id="e4856-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="e4856-174">Bir kitaplık bir sağlayıcıyı belirtiyorsa, `defaultProvider` *libman.json* özelliği yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="e4856-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="e4856-175">Bir kitaplık bir hedef belirtiyorsa, `defaultDestination` *libman.json* özelliği yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="e4856-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="e4856-176">Özeti</span><span class="sxs-lookup"><span data-stu-id="e4856-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="e4856-177">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="e4856-177">Options</span></span>

<span data-ttu-id="e4856-178">Komutu için aşağıdaki seçenekler kullanılabilir `libman restore` :</span><span class="sxs-lookup"><span data-stu-id="e4856-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="e4856-179">Örnekler</span><span class="sxs-lookup"><span data-stu-id="e4856-179">Examples</span></span>

<span data-ttu-id="e4856-180">*Üzerindelibman.js*tanımlanan kitaplık dosyalarını geri yüklemek için:</span><span class="sxs-lookup"><span data-stu-id="e4856-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="e4856-181">Kitaplık dosyalarını sil</span><span class="sxs-lookup"><span data-stu-id="e4856-181">Delete library files</span></span>

<span data-ttu-id="e4856-182">`libman clean`Komut daha önce LibMan aracılığıyla geri yüklenen kitaplık dosyalarını siler.</span><span class="sxs-lookup"><span data-stu-id="e4856-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="e4856-183">Bu işlemden sonra boş olacak klasörler silinir.</span><span class="sxs-lookup"><span data-stu-id="e4856-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="e4856-184">`libraries` *Üzerindelibman.js* özelliğindeki kitaplık dosyalarının ilişkili yapılandırma kaldırılmamıştır.</span><span class="sxs-lookup"><span data-stu-id="e4856-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="e4856-185">Özeti</span><span class="sxs-lookup"><span data-stu-id="e4856-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="e4856-186">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="e4856-186">Options</span></span>

<span data-ttu-id="e4856-187">Komutu için aşağıdaki seçenekler kullanılabilir `libman clean` :</span><span class="sxs-lookup"><span data-stu-id="e4856-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="e4856-188">Örnekler</span><span class="sxs-lookup"><span data-stu-id="e4856-188">Examples</span></span>

<span data-ttu-id="e4856-189">LibMan aracılığıyla yüklenen kitaplık dosyalarını silmek için:</span><span class="sxs-lookup"><span data-stu-id="e4856-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="e4856-190">Kitaplık dosyalarını kaldır</span><span class="sxs-lookup"><span data-stu-id="e4856-190">Uninstall library files</span></span>

<span data-ttu-id="e4856-191">`libman uninstall`Komut:</span><span class="sxs-lookup"><span data-stu-id="e4856-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="e4856-192">Belirtilen kitaplıkla ilişkili tüm dosyaları, *libman.jsüzerindeki*hedefle siler.</span><span class="sxs-lookup"><span data-stu-id="e4856-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="e4856-193">*Üzerindelibman.js*ilişkili kitaplık yapılandırmasını kaldırır.</span><span class="sxs-lookup"><span data-stu-id="e4856-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="e4856-194">Şu durumlarda bir hata oluşur:</span><span class="sxs-lookup"><span data-stu-id="e4856-194">An error occurs when:</span></span>

* <span data-ttu-id="e4856-195">Proje kökünde dosya *libman.js* yok.</span><span class="sxs-lookup"><span data-stu-id="e4856-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="e4856-196">Belirtilen kitaplık yok.</span><span class="sxs-lookup"><span data-stu-id="e4856-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="e4856-197">Aynı ada sahip birden fazla kitaplık yüklüyse, bir tane seçmeniz istenir.</span><span class="sxs-lookup"><span data-stu-id="e4856-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="e4856-198">Özeti</span><span class="sxs-lookup"><span data-stu-id="e4856-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="e4856-199">Arguments</span><span class="sxs-lookup"><span data-stu-id="e4856-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="e4856-200">Kaldırılacak kitaplığın adı.</span><span class="sxs-lookup"><span data-stu-id="e4856-200">The name of the library to uninstall.</span></span> <span data-ttu-id="e4856-201">Bu ad, sürüm numarası gösterimi içerebilir (örneğin, `@1.2.0` ).</span><span class="sxs-lookup"><span data-stu-id="e4856-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="e4856-202">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="e4856-202">Options</span></span>

<span data-ttu-id="e4856-203">Komutu için aşağıdaki seçenekler kullanılabilir `libman uninstall` :</span><span class="sxs-lookup"><span data-stu-id="e4856-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="e4856-204">Örnekler</span><span class="sxs-lookup"><span data-stu-id="e4856-204">Examples</span></span>

<span data-ttu-id="e4856-205">Dosyasında aşağıdaki *libman.js* göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="e4856-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="e4856-206">JQuery 'i kaldırmak için aşağıdaki komutlardan biri başarılı olur:</span><span class="sxs-lookup"><span data-stu-id="e4856-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="e4856-207">Sağlayıcı aracılığıyla yüklenen Lodash dosyalarını kaldırmak için `filesystem` :</span><span class="sxs-lookup"><span data-stu-id="e4856-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="e4856-208">Kitaplık sürümünü Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="e4856-208">Update library version</span></span>

<span data-ttu-id="e4856-209">`libman update`Komutu LibMan aracılığıyla yüklenen bir kitaplığı belirtilen sürüme güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="e4856-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="e4856-210">Şu durumlarda bir hata oluşur:</span><span class="sxs-lookup"><span data-stu-id="e4856-210">An error occurs when:</span></span>

* <span data-ttu-id="e4856-211">Proje kökünde dosya *libman.js* yok.</span><span class="sxs-lookup"><span data-stu-id="e4856-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="e4856-212">Belirtilen kitaplık yok.</span><span class="sxs-lookup"><span data-stu-id="e4856-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="e4856-213">Aynı ada sahip birden fazla kitaplık yüklüyse, bir tane seçmeniz istenir.</span><span class="sxs-lookup"><span data-stu-id="e4856-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="e4856-214">Özeti</span><span class="sxs-lookup"><span data-stu-id="e4856-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="e4856-215">Arguments</span><span class="sxs-lookup"><span data-stu-id="e4856-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="e4856-216">Güncelleştirilecek kitaplığın adı.</span><span class="sxs-lookup"><span data-stu-id="e4856-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="e4856-217">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="e4856-217">Options</span></span>

<span data-ttu-id="e4856-218">Komutu için aşağıdaki seçenekler kullanılabilir `libman update` :</span><span class="sxs-lookup"><span data-stu-id="e4856-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="e4856-219">Kitaplığın en son yayın öncesi sürümünü edinin.</span><span class="sxs-lookup"><span data-stu-id="e4856-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="e4856-220">Kitaplığın belirli bir sürümünü edinin.</span><span class="sxs-lookup"><span data-stu-id="e4856-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="e4856-221">Örnekler</span><span class="sxs-lookup"><span data-stu-id="e4856-221">Examples</span></span>

* <span data-ttu-id="e4856-222">JQuery 'i en son sürüme güncelleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="e4856-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="e4856-223">JQuery 'i 3.3.1 sürümüne güncelleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="e4856-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="e4856-224">JQuery 'i en son ön sürüm sürümüne güncelleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="e4856-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="e4856-225">Kitaplık önbelleğini Yönet</span><span class="sxs-lookup"><span data-stu-id="e4856-225">Manage library cache</span></span>

<span data-ttu-id="e4856-226">`libman cache`Komut, LibMan kitaplık önbelleğini yönetir.</span><span class="sxs-lookup"><span data-stu-id="e4856-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="e4856-227">`filesystem`Sağlayıcı kitaplık önbelleğini kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="e4856-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="e4856-228">Özeti</span><span class="sxs-lookup"><span data-stu-id="e4856-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="e4856-229">Arguments</span><span class="sxs-lookup"><span data-stu-id="e4856-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="e4856-230">Yalnızca komutla birlikte kullanılır `clean` .</span><span class="sxs-lookup"><span data-stu-id="e4856-230">Only used with the `clean` command.</span></span> <span data-ttu-id="e4856-231">Temizleyen sağlayıcı önbelleğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="e4856-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="e4856-232">Geçerli değerler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="e4856-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="e4856-233">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="e4856-233">Options</span></span>

<span data-ttu-id="e4856-234">Komutu için aşağıdaki seçenekler kullanılabilir `libman cache` :</span><span class="sxs-lookup"><span data-stu-id="e4856-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="e4856-235">Önbelleğe alınan dosyaların adlarını listeleyin.</span><span class="sxs-lookup"><span data-stu-id="e4856-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="e4856-236">Önbelleğe alınan kitaplıkların adlarını listeleyin.</span><span class="sxs-lookup"><span data-stu-id="e4856-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="e4856-237">Örnekler</span><span class="sxs-lookup"><span data-stu-id="e4856-237">Examples</span></span>

* <span data-ttu-id="e4856-238">Sağlayıcı başına önbelleğe alınmış kitaplıkların adlarını görüntülemek için aşağıdaki komutlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="e4856-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="e4856-239">Aşağıdakine benzer bir çıktı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="e4856-239">Output similar to the following is displayed:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      font-awesome
      jquery
      knockout
      lodash.js
      react
  ```

* <span data-ttu-id="e4856-240">Sağlayıcı başına önbelleğe alınmış kitaplık dosyalarının adlarını görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="e4856-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="e4856-241">Aşağıdakine benzer bir çıktı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="e4856-241">Output similar to the following is displayed:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout:
          <list omitted for brevity>
      react:
          <list omitted for brevity>
      vue:
          <list omitted for brevity>
  cdnjs:
      font-awesome
          metadata.json
      jquery
          metadata.json
          3.2.1\core.js
          3.2.1\jquery.js
          3.2.1\jquery.min.js
          3.2.1\jquery.min.map
          3.2.1\jquery.slim.js
          3.2.1\jquery.slim.min.js
          3.2.1\jquery.slim.min.map
          3.3.1\core.js
          3.3.1\jquery.js
          3.3.1\jquery.min.js
          3.3.1\jquery.min.map
          3.3.1\jquery.slim.js
          3.3.1\jquery.slim.min.js
          3.3.1\jquery.slim.min.map
      knockout
          metadata.json
          3.4.2\knockout-debug.js
          3.4.2\knockout-min.js
      lodash.js
          metadata.json
          4.17.10\lodash.js
          4.17.10\lodash.min.js
      react
          metadata.json
  ```

  <span data-ttu-id="e4856-242">Yukarıdaki çıktıda, 3.2.1 ve 3.3.1 jQuery sürümlerinin CDNJS sağlayıcısı altında önbelleğe alındığını görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="e4856-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="e4856-243">CDNJS sağlayıcısı için kitaplık önbelleğini boşaltmak için:</span><span class="sxs-lookup"><span data-stu-id="e4856-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="e4856-244">CDNJS sağlayıcı önbelleğini boşaltdıktan sonra `libman cache list` komut şunları görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e4856-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      (empty)
  ```

* <span data-ttu-id="e4856-245">Tüm desteklenen sağlayıcıların önbelleğini boşaltmak için:</span><span class="sxs-lookup"><span data-stu-id="e4856-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="e4856-246">Tüm sağlayıcı önbellekleri boşaltıldıktan sonra `libman cache list` komut şunları görüntüler:</span><span class="sxs-lookup"><span data-stu-id="e4856-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="e4856-247">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e4856-247">Additional resources</span></span>

* [<span data-ttu-id="e4856-248">Küresel bir araç yükler</span><span class="sxs-lookup"><span data-stu-id="e4856-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="e4856-249">LibMan GitHub deposu</span><span class="sxs-lookup"><span data-stu-id="e4856-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
