---
title: ASP.NET Core ile LibMan CLı kullanın
author: scottaddie
description: ASP.NET Core projesindeki LibMan CLı 'yı nasıl kullanacağınızı öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/libman/libman-cli
ms.openlocfilehash: ed5dffb83a2f1a40f3d6596d23135c0fa5b6791f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403189"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a>ASP.NET Core ile LibMan CLı kullanın

[Scott Ade](https://twitter.com/Scott_Addie) tarafından

[Libman](xref:client-side/libman/index) CLI, .NET Core 'un desteklendiği her yerde desteklenen platformlar arası bir araçtır.

## <a name="prerequisites"></a>Ön koşullar

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>Yükleme

LibMan CLı 'yı yüklemek için:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

[.NET Core küresel aracı](/dotnet/core/tools/global-tools#install-a-global-tool) , [Microsoft. Web. LibraryManager. CLI](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet paketinden yüklenir.

Belirli bir NuGet paket kaynağından LibMan CLı 'yı yüklemek için:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

Yukarıdaki örnekte, yerel Windows makinenin *C:\Temp\Microsoft.Web.LibraryManager.cli.1.0.94-g606058a278.nupkg* dosyasından bir .NET Core genel aracı yüklenir.

## <a name="usage"></a>Kullanım

CLı başarıyla yüklendikten sonra aşağıdaki komut kullanılabilir:

```console
libman
```

Yüklü CLı sürümünü görüntülemek için:

```console
libman --version
```

Kullanılabilir CLı komutlarını görüntülemek için:

```console
libman --help
```

Yukarıdaki komut aşağıdakine benzer bir çıktı görüntüler:

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

Aşağıdaki bölümlerde kullanılabilir CLı komutları ana hatlarıyla verilmiştir.

## <a name="initialize-libman-in-the-project"></a>Projedeki LibMan 'ı Başlat

`libman init`Bir dosya yoksa, komut dosyada bir *libman.js* oluşturur. Dosya varsayılan öğe şablonu içeriğiyle oluşturulur.

### <a name="synopsis"></a>Özeti

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>Seçenekler

Komutu için aşağıdaki seçenekler kullanılabilir `libman init` :

* `-d|--default-destination <PATH>`

  Geçerli klasöre göreli bir yol. Kitaplık dosyaları, `destination` *üzerindelibman.js*bir kitaplık için tanımlı bir özellik yoksa bu konuma yüklenir. `<PATH>`Değer, `defaultDestination` *üzerindelibman.js*özelliğine yazılır.

* `-p|--default-provider <PROVIDER>`

  Belirli bir kitaplık için hiçbir sağlayıcı tanımlanmamışsa kullanılacak sağlayıcı. `<PROVIDER>`Değer, `defaultProvider` *üzerindelibman.js*özelliğine yazılır. `<PROVIDER>`Aşağıdaki değerlerden biriyle değiştirin:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Örnekler

Bir ASP.NET Core projesindeki dosyada *libman.js* oluşturmak için:

* Proje köküne gidin.
* Şu komutu çalıştırın:

  ```console
  libman init
  ```

* Varsayılan sağlayıcının adını yazın veya `Enter` varsayılan CDNJS sağlayıcısını kullanmak için ' a basın. Geçerli değerler şunlardır:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![Libman başlatma komutu-varsayılan sağlayıcı](_static/libman-init-provider.png)

Aşağıdaki içeriğe sahip proje köküne bir *libman.js* dosyası eklenir:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>Kitaplık dosyaları Ekle

`libman install`Komutu, kitaplık dosyalarını indirir ve projeye yükler. Bir *libman.js* dosya yoksa eklenir. Dosyadaki *libman.js* , kitaplık dosyaları için yapılandırma ayrıntılarını depolayacak şekilde değiştirilir.

### <a name="synopsis"></a>Özeti

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>Arguments

`LIBRARY`

Yüklenecek kitaplığın adı. Bu ad, sürüm numarası gösterimi içerebilir (örneğin, `@1.2.0` ).

### <a name="options"></a>Seçenekler

Komutu için aşağıdaki seçenekler kullanılabilir `libman install` :

* `-d|--destination <PATH>`

  Kitaplığın yükleneceği konum. Belirtilmemişse, varsayılan konum kullanılır. `defaultDestination` *Üzerindelibman.js*hiçbir özellik belirtilmemişse, bu seçenek gereklidir.

* `--files <FILE>`

  Kitaplıktan yüklenecek dosyanın adını belirtin. Belirtilmemişse, kitaplıktaki tüm dosyalar yüklenir. `--files`Yüklenecek dosya başına bir seçenek belirtin. Göreli yollar da desteklenir. Örneğin: `--files dist/browser/signalr.js`.

* `-p|--provider <PROVIDER>`

  Kitaplık alımı için kullanılacak sağlayıcının adı. `<PROVIDER>`Aşağıdaki değerlerden biriyle değiştirin:
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  Belirtilmemişse, `defaultProvider` *libman.json* özelliği kullanılır. `defaultProvider` *Üzerindelibman.js*hiçbir özellik belirtilmemişse, bu seçenek gereklidir.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Örnekler

Dosyasında aşağıdaki *libman.js* göz önünde bulundurun:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

JQuery sürüm 3.2.1 *jquery.min.js* dosyasını CDNJS sağlayıcısını kullanarak *Wwwroot/Scripts/jQuery* klasörüne yüklemek için:

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

Dosyadaki *libman.js* aşağıdakine benzer:

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

Dosya sistemi sağlayıcısını kullanarak *C: \\ Temp \\ \\ contosocalendar* öğesinden *calendar.js* ve *Calendar. css* dosyalarını yüklemek için:

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

Aşağıdaki istem iki nedenden dolayı görünür:

* Dosyadaki *libman.js* bir `defaultDestination` özellik içermiyor.
* `libman install`Komut, `-d|--destination` seçeneğini içermez.

![Libman Install komut-hedef](_static/libman-install-destination.png)

Varsayılan hedefi kabul ettikten sonra, dosyadaki *libman.js* aşağıdakine benzer:

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

## <a name="restore-library-files"></a>Kitaplık dosyalarını geri yükleme

`libman restore`Komut, *üzerindelibman.js*tanımlı kitaplık dosyalarını yüklüyor. Aşağıdaki kurallar geçerlidir:

* Proje kökünde *libman.js* dosya varsa, bir hata döndürülür.
* Bir kitaplık bir sağlayıcıyı belirtiyorsa, `defaultProvider` *libman.json* özelliği yok sayılır.
* Bir kitaplık bir hedef belirtiyorsa, `defaultDestination` *libman.json* özelliği yok sayılır.

### <a name="synopsis"></a>Özeti

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>Seçenekler

Komutu için aşağıdaki seçenekler kullanılabilir `libman restore` :

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Örnekler

*Üzerindelibman.js*tanımlanan kitaplık dosyalarını geri yüklemek için:

```console
libman restore
```

## <a name="delete-library-files"></a>Kitaplık dosyalarını sil

`libman clean`Komut daha önce LibMan aracılığıyla geri yüklenen kitaplık dosyalarını siler. Bu işlemden sonra boş olacak klasörler silinir. `libraries` *Üzerindelibman.js* özelliğindeki kitaplık dosyalarının ilişkili yapılandırma kaldırılmamıştır.

### <a name="synopsis"></a>Özeti

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>Seçenekler

Komutu için aşağıdaki seçenekler kullanılabilir `libman clean` :

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Örnekler

LibMan aracılığıyla yüklenen kitaplık dosyalarını silmek için:

```console
libman clean
```

## <a name="uninstall-library-files"></a>Kitaplık dosyalarını kaldır

`libman uninstall`Komut:

* Belirtilen kitaplıkla ilişkili tüm dosyaları, *libman.jsüzerindeki*hedefle siler.
* *Üzerindelibman.js*ilişkili kitaplık yapılandırmasını kaldırır.

Şu durumlarda bir hata oluşur:

* Proje kökünde dosya *libman.js* yok.
* Belirtilen kitaplık yok.

Aynı ada sahip birden fazla kitaplık yüklüyse, bir tane seçmeniz istenir.

### <a name="synopsis"></a>Özeti

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>Arguments

`LIBRARY`

Kaldırılacak kitaplığın adı. Bu ad, sürüm numarası gösterimi içerebilir (örneğin, `@1.2.0` ).

### <a name="options"></a>Seçenekler

Komutu için aşağıdaki seçenekler kullanılabilir `libman uninstall` :

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Örnekler

Dosyasında aşağıdaki *libman.js* göz önünde bulundurun:

[!code-json[](samples/LibManSample/libman.json)]

* JQuery 'i kaldırmak için aşağıdaki komutlardan biri başarılı olur:

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* Sağlayıcı aracılığıyla yüklenen Lodash dosyalarını kaldırmak için `filesystem` :

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>Kitaplık sürümünü Güncelleştir

`libman update`Komutu LibMan aracılığıyla yüklenen bir kitaplığı belirtilen sürüme güncelleştirir.

Şu durumlarda bir hata oluşur:

* Proje kökünde dosya *libman.js* yok.
* Belirtilen kitaplık yok.

Aynı ada sahip birden fazla kitaplık yüklüyse, bir tane seçmeniz istenir.

### <a name="synopsis"></a>Özeti

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>Arguments

`LIBRARY`

Güncelleştirilecek kitaplığın adı.

### <a name="options"></a>Seçenekler

Komutu için aşağıdaki seçenekler kullanılabilir `libman update` :

* `-pre`

  Kitaplığın en son yayın öncesi sürümünü edinin.

* `--to <VERSION>`

  Kitaplığın belirli bir sürümünü edinin.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Örnekler

* JQuery 'i en son sürüme güncelleştirmek için:

  ```console
  libman update jquery
  ```

* JQuery 'i 3.3.1 sürümüne güncelleştirmek için:

  ```console
  libman update jquery --to 3.3.1
  ```

* JQuery 'i en son ön sürüm sürümüne güncelleştirmek için:

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>Kitaplık önbelleğini Yönet

`libman cache`Komut, LibMan kitaplık önbelleğini yönetir. `filesystem`Sağlayıcı kitaplık önbelleğini kullanmaz.

### <a name="synopsis"></a>Özeti

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>Arguments

`PROVIDER`

Yalnızca komutla birlikte kullanılır `clean` . Temizleyen sağlayıcı önbelleğini belirtir. Geçerli değerler şunlardır:

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>Seçenekler

Komutu için aşağıdaki seçenekler kullanılabilir `libman cache` :

* `--files`

  Önbelleğe alınan dosyaların adlarını listeleyin.

* `--libraries`

  Önbelleğe alınan kitaplıkların adlarını listeleyin.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Örnekler

* Sağlayıcı başına önbelleğe alınmış kitaplıkların adlarını görüntülemek için aşağıdaki komutlardan birini kullanın:

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  Aşağıdakine benzer bir çıktı görüntülenir:

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

* Sağlayıcı başına önbelleğe alınmış kitaplık dosyalarının adlarını görüntülemek için:

  ```console
  libman cache list --files
  ```

  Aşağıdakine benzer bir çıktı görüntülenir:

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

  Yukarıdaki çıktıda, 3.2.1 ve 3.3.1 jQuery sürümlerinin CDNJS sağlayıcısı altında önbelleğe alındığını görürsünüz.

* CDNJS sağlayıcısı için kitaplık önbelleğini boşaltmak için:

  ```console
  libman cache clean cdnjs
  ```

  CDNJS sağlayıcı önbelleğini boşaltdıktan sonra `libman cache list` komut şunları görüntüler:

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

* Tüm desteklenen sağlayıcıların önbelleğini boşaltmak için:

  ```console
  libman cache clean
  ```

  Tüm sağlayıcı önbellekleri boşaltıldıktan sonra `libman cache list` komut şunları görüntüler:

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>Ek kaynaklar

* [Küresel bir araç yükler](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [LibMan GitHub deposu](https://github.com/aspnet/LibraryManager)
