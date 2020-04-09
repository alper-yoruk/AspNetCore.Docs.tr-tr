---
title: LibMan CLI'yi ASP.NET Çekirdekli kullanın
author: scottaddie
description: LibMan CLI'yi ASP.NET Core projesinde nasıl kullanacağınızı öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: client-side/libman/libman-cli
ms.openlocfilehash: 02d88d09805bd23a86ef924766373245fec7ff52
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664635"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a><span data-ttu-id="3e227-103">LibMan CLI'yi ASP.NET Çekirdekli kullanın</span><span class="sxs-lookup"><span data-stu-id="3e227-103">Use the LibMan CLI with ASP.NET Core</span></span>

<span data-ttu-id="3e227-104">Yazar: [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="3e227-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="3e227-105">[LibMan](xref:client-side/libman/index) CLI, her yerde desteklenen bir çapraz platform aracıdır .NET Core desteklenir.</span><span class="sxs-lookup"><span data-stu-id="3e227-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3e227-106">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="3e227-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="3e227-107">Yükleme</span><span class="sxs-lookup"><span data-stu-id="3e227-107">Installation</span></span>

<span data-ttu-id="3e227-108">LibMan CLI'yi yüklemek için:</span><span class="sxs-lookup"><span data-stu-id="3e227-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="3e227-109">[Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet paketinden [bir .NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) yüklenir.</span><span class="sxs-lookup"><span data-stu-id="3e227-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="3e227-110">LibMan CLI'yi belirli bir NuGet paket kaynağından yüklemek için:</span><span class="sxs-lookup"><span data-stu-id="3e227-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="3e227-111">Önceki örnekte, yerel Windows makinesinin *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* dosyasından bir .NET Core Global Tool yüklenir.</span><span class="sxs-lookup"><span data-stu-id="3e227-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="3e227-112">Kullanım</span><span class="sxs-lookup"><span data-stu-id="3e227-112">Usage</span></span>

<span data-ttu-id="3e227-113">CLI'nin başarılı bir şekilde yüklenmesinden sonra aşağıdaki komut kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="3e227-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="3e227-114">Yüklü CLI sürümünü görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="3e227-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="3e227-115">Kullanılabilir CLI komutlarını görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="3e227-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="3e227-116">Önceki komut aşağıdakine benzer çıktı görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3e227-116">The preceding command displays output similar to the following:</span></span>

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

<span data-ttu-id="3e227-117">Aşağıdaki bölümlerde kullanılabilir CLI komutları sıralanmIst.)</span><span class="sxs-lookup"><span data-stu-id="3e227-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="3e227-118">Projede LibMan'ı başlatma</span><span class="sxs-lookup"><span data-stu-id="3e227-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="3e227-119">Komut, `libman init` yoksa *bir libman.json* dosyası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3e227-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="3e227-120">Dosya varsayılan öğe şablonu içeriği yle oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3e227-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3e227-121">Özet</span><span class="sxs-lookup"><span data-stu-id="3e227-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="3e227-122">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="3e227-122">Options</span></span>

<span data-ttu-id="3e227-123">`libman init` Komut için aşağıdaki seçenekler kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="3e227-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="3e227-124">Geçerli klasöre göre bir yol.</span><span class="sxs-lookup"><span data-stu-id="3e227-124">A path relative to the current folder.</span></span> <span data-ttu-id="3e227-125">*Libman.json'daki*bir `destination` kitaplık için özellik tanımlanmamışsa, kitaplık dosyaları bu konuma yüklenir.</span><span class="sxs-lookup"><span data-stu-id="3e227-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="3e227-126">Değeri `<PATH>` `defaultDestination` *libman.json*özelliğine yazılır.</span><span class="sxs-lookup"><span data-stu-id="3e227-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="3e227-127">Belirli bir kitaplık için sağlayıcı tanımlanmamışsa kullanılacak sağlayıcı.</span><span class="sxs-lookup"><span data-stu-id="3e227-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="3e227-128">Değeri `<PROVIDER>` `defaultProvider` *libman.json*özelliğine yazılır.</span><span class="sxs-lookup"><span data-stu-id="3e227-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="3e227-129">Aşağıdaki `<PROVIDER>` değerlerden biriyle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="3e227-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3e227-130">Örnekler</span><span class="sxs-lookup"><span data-stu-id="3e227-130">Examples</span></span>

<span data-ttu-id="3e227-131">ASP.NET Core projesinde *libman.json* dosyası oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="3e227-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="3e227-132">Proje köküne gidin.</span><span class="sxs-lookup"><span data-stu-id="3e227-132">Navigate to the project root.</span></span>
* <span data-ttu-id="3e227-133">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3e227-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="3e227-134">Varsayılan sağlayıcının adını yazın veya `Enter` varsayılan CDNJS sağlayıcısını kullanmak için basın.</span><span class="sxs-lookup"><span data-stu-id="3e227-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="3e227-135">Geçerli değerler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="3e227-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman init komutu - varsayılan sağlayıcı](_static/libman-init-provider.png)

<span data-ttu-id="3e227-137">Proje köküne aşağıdaki içerikle *bir libman.json* dosyası eklenir:</span><span class="sxs-lookup"><span data-stu-id="3e227-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="3e227-138">Kitaplık dosyaları ekleme</span><span class="sxs-lookup"><span data-stu-id="3e227-138">Add library files</span></span>

<span data-ttu-id="3e227-139">Komut, `libman install` kitaplık dosyalarını projeye karşı yükler ve yükler.</span><span class="sxs-lookup"><span data-stu-id="3e227-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="3e227-140">Bir *libman.json* dosyası yoksa eklenir.</span><span class="sxs-lookup"><span data-stu-id="3e227-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="3e227-141">*Libman.json* dosyası, kitaplık dosyalarının yapılandırma ayrıntılarını depolamak üzere değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="3e227-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3e227-142">Özet</span><span class="sxs-lookup"><span data-stu-id="3e227-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="3e227-143">Bağımsız Değişkenler</span><span class="sxs-lookup"><span data-stu-id="3e227-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="3e227-144">Yüklenmesi gereken kitaplığın adı.</span><span class="sxs-lookup"><span data-stu-id="3e227-144">The name of the library to install.</span></span> <span data-ttu-id="3e227-145">Bu ad sürüm numarası gösterimi içerebilir `@1.2.0`(örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="3e227-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="3e227-146">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="3e227-146">Options</span></span>

<span data-ttu-id="3e227-147">`libman install` Komut için aşağıdaki seçenekler kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="3e227-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="3e227-148">Kitaplığı yüklemek için konum.</span><span class="sxs-lookup"><span data-stu-id="3e227-148">The location to install the library.</span></span> <span data-ttu-id="3e227-149">Belirtilmemişse, varsayılan konum kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3e227-149">If not specified, the default location is used.</span></span> <span data-ttu-id="3e227-150">`defaultDestination` *Libman.json'da*özellik belirtilmemişse, bu seçenek gereklidir.</span><span class="sxs-lookup"><span data-stu-id="3e227-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="3e227-151">Kitaplıktan yüklenmesi gereken dosyanın adını belirtin.</span><span class="sxs-lookup"><span data-stu-id="3e227-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="3e227-152">Belirtilmemişse, kitaplıktaki tüm dosyalar yüklenir.</span><span class="sxs-lookup"><span data-stu-id="3e227-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="3e227-153">Yüklenecek `--files` dosya başına bir seçenek sağlayın.</span><span class="sxs-lookup"><span data-stu-id="3e227-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="3e227-154">Göreli yollar da desteklenir.</span><span class="sxs-lookup"><span data-stu-id="3e227-154">Relative paths are supported too.</span></span> <span data-ttu-id="3e227-155">Örneğin: `--files dist/browser/signalr.js`.</span><span class="sxs-lookup"><span data-stu-id="3e227-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="3e227-156">Kitaplık edinimi için kullanılacak sağlayıcının adı.</span><span class="sxs-lookup"><span data-stu-id="3e227-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="3e227-157">Aşağıdaki `<PROVIDER>` değerlerden biriyle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="3e227-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="3e227-158">Belirtilmemişse, `defaultProvider` *libman.json'daki* özellik kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3e227-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="3e227-159">`defaultProvider` *Libman.json'da*özellik belirtilmemişse, bu seçenek gereklidir.</span><span class="sxs-lookup"><span data-stu-id="3e227-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3e227-160">Örnekler</span><span class="sxs-lookup"><span data-stu-id="3e227-160">Examples</span></span>

<span data-ttu-id="3e227-161">Aşağıdaki *libman.json* dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="3e227-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="3e227-162">CDNJS sağlayıcısını kullanarak *wwwroot/scripts/jquery* klasörüne jQuery sürümünü 3.2.1 *jquery.min.js* dosyasını yüklemek için:</span><span class="sxs-lookup"><span data-stu-id="3e227-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="3e227-163">*Libman.json* dosyası aşağıdakilere benzer:</span><span class="sxs-lookup"><span data-stu-id="3e227-163">The *libman.json* file resembles the following:</span></span>

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

<span data-ttu-id="3e227-164">C *takvim.js* ve *calendar.css* dosyalarını yüklemek için: \*\\\\\\ \* dosya sistemi sağlayıcısını kullanarak contosoCalendar'ı geçici olarak kullanma:</span><span class="sxs-lookup"><span data-stu-id="3e227-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="3e227-165">Aşağıdaki istemi iki nedenden dolayı görünür:</span><span class="sxs-lookup"><span data-stu-id="3e227-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="3e227-166">*Libman.json* dosyasında bir `defaultDestination` özellik yok.</span><span class="sxs-lookup"><span data-stu-id="3e227-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="3e227-167">Komut `libman install` `-d|--destination` seçeneği içermez.</span><span class="sxs-lookup"><span data-stu-id="3e227-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![libman yükleme komutu - hedef](_static/libman-install-destination.png)

<span data-ttu-id="3e227-169">Varsayılan hedefi kabul ettikten *sonra, libman.json* dosyası aşağıdakilere benzer:</span><span class="sxs-lookup"><span data-stu-id="3e227-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

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

## <a name="restore-library-files"></a><span data-ttu-id="3e227-170">Kitaplık dosyalarını geri yükleme</span><span class="sxs-lookup"><span data-stu-id="3e227-170">Restore library files</span></span>

<span data-ttu-id="3e227-171">`libman restore` Komut, *libman.json'da*tanımlanan kitaplık dosyalarını yükler.</span><span class="sxs-lookup"><span data-stu-id="3e227-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="3e227-172">Aşağıdaki kurallar geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="3e227-172">The following rules apply:</span></span>

* <span data-ttu-id="3e227-173">Proje kökünde *libman.json* dosyası yoksa, bir hata döndürülür.</span><span class="sxs-lookup"><span data-stu-id="3e227-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="3e227-174">Kitaplık bir sağlayıcı belirtirse, `defaultProvider` *libman.json'daki* özellik yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="3e227-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="3e227-175">Kitaplık bir hedef belirtirse, `defaultDestination` *libman.json'daki* özellik yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="3e227-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3e227-176">Özet</span><span class="sxs-lookup"><span data-stu-id="3e227-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="3e227-177">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="3e227-177">Options</span></span>

<span data-ttu-id="3e227-178">`libman restore` Komut için aşağıdaki seçenekler kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="3e227-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3e227-179">Örnekler</span><span class="sxs-lookup"><span data-stu-id="3e227-179">Examples</span></span>

<span data-ttu-id="3e227-180">*libman.json'da*tanımlanan kitaplık dosyalarını geri yüklemek için:</span><span class="sxs-lookup"><span data-stu-id="3e227-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="3e227-181">Kitaplık dosyalarını silme</span><span class="sxs-lookup"><span data-stu-id="3e227-181">Delete library files</span></span>

<span data-ttu-id="3e227-182">Komut, `libman clean` daha önce LibMan üzerinden geri yüklenen kitaplık dosyalarını siler.</span><span class="sxs-lookup"><span data-stu-id="3e227-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="3e227-183">Bu işlemden sonra boşalan klasörler silinir.</span><span class="sxs-lookup"><span data-stu-id="3e227-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="3e227-184">*Libman.json* özelliğindeki `libraries` kitaplık dosyalarının ilişkili yapılandırmaları kaldırılmaz.</span><span class="sxs-lookup"><span data-stu-id="3e227-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3e227-185">Özet</span><span class="sxs-lookup"><span data-stu-id="3e227-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="3e227-186">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="3e227-186">Options</span></span>

<span data-ttu-id="3e227-187">`libman clean` Komut için aşağıdaki seçenekler kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="3e227-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3e227-188">Örnekler</span><span class="sxs-lookup"><span data-stu-id="3e227-188">Examples</span></span>

<span data-ttu-id="3e227-189">LibMan üzerinden yüklenen kitaplık dosyalarını silmek için:</span><span class="sxs-lookup"><span data-stu-id="3e227-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="3e227-190">Kitaplık dosyalarını kaldırma</span><span class="sxs-lookup"><span data-stu-id="3e227-190">Uninstall library files</span></span>

<span data-ttu-id="3e227-191">Komut: `libman uninstall`</span><span class="sxs-lookup"><span data-stu-id="3e227-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="3e227-192">Belirtilen kitaplıkla ilişkili tüm dosyaları *libman.json'daki*hedeften siler.</span><span class="sxs-lookup"><span data-stu-id="3e227-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="3e227-193">İlişkili kitaplık yapılandırmasını *libman.json'dan*kaldırır.</span><span class="sxs-lookup"><span data-stu-id="3e227-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="3e227-194">Bir hata oluşur:</span><span class="sxs-lookup"><span data-stu-id="3e227-194">An error occurs when:</span></span>

* <span data-ttu-id="3e227-195">Proje kökünde *libman.json* dosyası yok.</span><span class="sxs-lookup"><span data-stu-id="3e227-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="3e227-196">Belirtilen kitaplık yok.</span><span class="sxs-lookup"><span data-stu-id="3e227-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="3e227-197">Aynı ada sahip birden fazla kitaplık yüklenmişse, bir kitap seçmeniz istenir.</span><span class="sxs-lookup"><span data-stu-id="3e227-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3e227-198">Özet</span><span class="sxs-lookup"><span data-stu-id="3e227-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="3e227-199">Bağımsız Değişkenler</span><span class="sxs-lookup"><span data-stu-id="3e227-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="3e227-200">Kaldırılabilmek için kitaplığın adı.</span><span class="sxs-lookup"><span data-stu-id="3e227-200">The name of the library to uninstall.</span></span> <span data-ttu-id="3e227-201">Bu ad sürüm numarası gösterimi içerebilir `@1.2.0`(örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="3e227-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="3e227-202">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="3e227-202">Options</span></span>

<span data-ttu-id="3e227-203">`libman uninstall` Komut için aşağıdaki seçenekler kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="3e227-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3e227-204">Örnekler</span><span class="sxs-lookup"><span data-stu-id="3e227-204">Examples</span></span>

<span data-ttu-id="3e227-205">Aşağıdaki *libman.json* dosyasını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="3e227-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="3e227-206">jQuery'yi kaldırmak için aşağıdaki komutlardan biri başarılı dır:</span><span class="sxs-lookup"><span data-stu-id="3e227-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="3e227-207">Sağlayıcı üzerinden yüklenen Lodash dosyalarını `filesystem` kaldırmak için:</span><span class="sxs-lookup"><span data-stu-id="3e227-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="3e227-208">Kitaplık sürümünü güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="3e227-208">Update library version</span></span>

<span data-ttu-id="3e227-209">Komut, `libman update` LibMan üzerinden yüklenen bir kitaplığı belirtilen sürüme güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="3e227-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="3e227-210">Bir hata oluşur:</span><span class="sxs-lookup"><span data-stu-id="3e227-210">An error occurs when:</span></span>

* <span data-ttu-id="3e227-211">Proje kökünde *libman.json* dosyası yok.</span><span class="sxs-lookup"><span data-stu-id="3e227-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="3e227-212">Belirtilen kitaplık yok.</span><span class="sxs-lookup"><span data-stu-id="3e227-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="3e227-213">Aynı ada sahip birden fazla kitaplık yüklenmişse, bir kitap seçmeniz istenir.</span><span class="sxs-lookup"><span data-stu-id="3e227-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3e227-214">Özet</span><span class="sxs-lookup"><span data-stu-id="3e227-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="3e227-215">Bağımsız Değişkenler</span><span class="sxs-lookup"><span data-stu-id="3e227-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="3e227-216">Güncelleştirilen kitaplığın adı.</span><span class="sxs-lookup"><span data-stu-id="3e227-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="3e227-217">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="3e227-217">Options</span></span>

<span data-ttu-id="3e227-218">`libman update` Komut için aşağıdaki seçenekler kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="3e227-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="3e227-219">Kitaplığın en son yayın öncesi sürümünü edinin.</span><span class="sxs-lookup"><span data-stu-id="3e227-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="3e227-220">Kitaplığın belirli bir sürümünü edinin.</span><span class="sxs-lookup"><span data-stu-id="3e227-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3e227-221">Örnekler</span><span class="sxs-lookup"><span data-stu-id="3e227-221">Examples</span></span>

* <span data-ttu-id="3e227-222">jQuery'yi en son sürüme güncellemek için:</span><span class="sxs-lookup"><span data-stu-id="3e227-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="3e227-223">jQuery'yi sürüm 3.3.1'e güncellemek için:</span><span class="sxs-lookup"><span data-stu-id="3e227-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="3e227-224">jQuery'yi en son sürüm öncesi sürümüne güncellemek için:</span><span class="sxs-lookup"><span data-stu-id="3e227-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="3e227-225">Kitaplık önbelleğini yönetme</span><span class="sxs-lookup"><span data-stu-id="3e227-225">Manage library cache</span></span>

<span data-ttu-id="3e227-226">Komut, `libman cache` LibMan kitaplık önbelleğini yönetir.</span><span class="sxs-lookup"><span data-stu-id="3e227-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="3e227-227">Sağlayıcı `filesystem` kitaplık önbelleğini kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="3e227-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="3e227-228">Özet</span><span class="sxs-lookup"><span data-stu-id="3e227-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="3e227-229">Bağımsız Değişkenler</span><span class="sxs-lookup"><span data-stu-id="3e227-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="3e227-230">Yalnızca komutla `clean` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3e227-230">Only used with the `clean` command.</span></span> <span data-ttu-id="3e227-231">Sağlayıcı önbelleğinin temizlenmesini belirtir.</span><span class="sxs-lookup"><span data-stu-id="3e227-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="3e227-232">Geçerli değerler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="3e227-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="3e227-233">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="3e227-233">Options</span></span>

<span data-ttu-id="3e227-234">`libman cache` Komut için aşağıdaki seçenekler kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="3e227-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="3e227-235">Önbelleğe alınan dosyaların adlarını listeleyin.</span><span class="sxs-lookup"><span data-stu-id="3e227-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="3e227-236">Önbelleğe alınmış kitaplıkların adlarını listele.</span><span class="sxs-lookup"><span data-stu-id="3e227-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="3e227-237">Örnekler</span><span class="sxs-lookup"><span data-stu-id="3e227-237">Examples</span></span>

* <span data-ttu-id="3e227-238">Sağlayıcı başına önbelleğe alınmış kitaplıkların adlarını görüntülemek için aşağıdaki komutlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="3e227-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="3e227-239">Aşağıdakilere benzer çıktı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="3e227-239">Output similar to the following is displayed:</span></span>

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

* <span data-ttu-id="3e227-240">Önbelleğe alınan kitaplık dosyalarının adlarını sağlayıcı başına görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="3e227-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="3e227-241">Aşağıdakilere benzer çıktı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="3e227-241">Output similar to the following is displayed:</span></span>

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

  <span data-ttu-id="3e227-242">Önceki çıktının jQuery sürümlerinin 3.2.1 ve 3.3.1'in CDNJS sağlayıcısı altında önbelleğe alınmış olduğunu gösterdiğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="3e227-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="3e227-243">CDNJS sağlayıcısı için kitaplık önbelleğini boşaltmak için:</span><span class="sxs-lookup"><span data-stu-id="3e227-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="3e227-244">CDNJS sağlayıcı önbelleğini boşalttınktan `libman cache list` sonra, komut aşağıdakileri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3e227-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

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

* <span data-ttu-id="3e227-245">Desteklenen tüm sağlayıcılar için önbelleği boşaltmak için:</span><span class="sxs-lookup"><span data-stu-id="3e227-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="3e227-246">Tüm sağlayıcı önbelleklerini boşalttınktan `libman cache list` sonra, komut aşağıdakileri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3e227-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="3e227-247">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3e227-247">Additional resources</span></span>

* [<span data-ttu-id="3e227-248">Genel Bir Araç Yükleme</span><span class="sxs-lookup"><span data-stu-id="3e227-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="3e227-249">LibMan GitHub deposu</span><span class="sxs-lookup"><span data-stu-id="3e227-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
