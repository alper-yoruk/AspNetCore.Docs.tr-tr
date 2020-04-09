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
# <a name="use-the-libman-cli-with-aspnet-core"></a>LibMan CLI'yi ASP.NET Çekirdekli kullanın

Yazar: [Scott Addie](https://twitter.com/Scott_Addie)

[LibMan](xref:client-side/libman/index) CLI, her yerde desteklenen bir çapraz platform aracıdır .NET Core desteklenir.

## <a name="prerequisites"></a>Ön koşullar

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>Yükleme

LibMan CLI'yi yüklemek için:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

[Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet paketinden [bir .NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) yüklenir.

LibMan CLI'yi belirli bir NuGet paket kaynağından yüklemek için:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

Önceki örnekte, yerel Windows makinesinin *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* dosyasından bir .NET Core Global Tool yüklenir.

## <a name="usage"></a>Kullanım

CLI'nin başarılı bir şekilde yüklenmesinden sonra aşağıdaki komut kullanılabilir:

```console
libman
```

Yüklü CLI sürümünü görüntülemek için:

```console
libman --version
```

Kullanılabilir CLI komutlarını görüntülemek için:

```console
libman --help
```

Önceki komut aşağıdakine benzer çıktı görüntüler:

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

Aşağıdaki bölümlerde kullanılabilir CLI komutları sıralanmIst.)

## <a name="initialize-libman-in-the-project"></a>Projede LibMan'ı başlatma

Komut, `libman init` yoksa *bir libman.json* dosyası oluşturur. Dosya varsayılan öğe şablonu içeriği yle oluşturulur.

### <a name="synopsis"></a>Özet

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>Seçenekler

`libman init` Komut için aşağıdaki seçenekler kullanılabilir:

* `-d|--default-destination <PATH>`

  Geçerli klasöre göre bir yol. *Libman.json'daki*bir `destination` kitaplık için özellik tanımlanmamışsa, kitaplık dosyaları bu konuma yüklenir. Değeri `<PATH>` `defaultDestination` *libman.json*özelliğine yazılır.

* `-p|--default-provider <PROVIDER>`

  Belirli bir kitaplık için sağlayıcı tanımlanmamışsa kullanılacak sağlayıcı. Değeri `<PROVIDER>` `defaultProvider` *libman.json*özelliğine yazılır. Aşağıdaki `<PROVIDER>` değerlerden biriyle değiştirin:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Örnekler

ASP.NET Core projesinde *libman.json* dosyası oluşturmak için:

* Proje köküne gidin.
* Şu komutu çalıştırın:

  ```console
  libman init
  ```

* Varsayılan sağlayıcının adını yazın veya `Enter` varsayılan CDNJS sağlayıcısını kullanmak için basın. Geçerli değerler şunlardır:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman init komutu - varsayılan sağlayıcı](_static/libman-init-provider.png)

Proje köküne aşağıdaki içerikle *bir libman.json* dosyası eklenir:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>Kitaplık dosyaları ekleme

Komut, `libman install` kitaplık dosyalarını projeye karşı yükler ve yükler. Bir *libman.json* dosyası yoksa eklenir. *Libman.json* dosyası, kitaplık dosyalarının yapılandırma ayrıntılarını depolamak üzere değiştirilir.

### <a name="synopsis"></a>Özet

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>Bağımsız Değişkenler

`LIBRARY`

Yüklenmesi gereken kitaplığın adı. Bu ad sürüm numarası gösterimi içerebilir `@1.2.0`(örneğin, ).

### <a name="options"></a>Seçenekler

`libman install` Komut için aşağıdaki seçenekler kullanılabilir:

* `-d|--destination <PATH>`

  Kitaplığı yüklemek için konum. Belirtilmemişse, varsayılan konum kullanılır. `defaultDestination` *Libman.json'da*özellik belirtilmemişse, bu seçenek gereklidir.

* `--files <FILE>`

  Kitaplıktan yüklenmesi gereken dosyanın adını belirtin. Belirtilmemişse, kitaplıktaki tüm dosyalar yüklenir. Yüklenecek `--files` dosya başına bir seçenek sağlayın. Göreli yollar da desteklenir. Örneğin: `--files dist/browser/signalr.js`.

* `-p|--provider <PROVIDER>`

  Kitaplık edinimi için kullanılacak sağlayıcının adı. Aşağıdaki `<PROVIDER>` değerlerden biriyle değiştirin:
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  Belirtilmemişse, `defaultProvider` *libman.json'daki* özellik kullanılır. `defaultProvider` *Libman.json'da*özellik belirtilmemişse, bu seçenek gereklidir.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Örnekler

Aşağıdaki *libman.json* dosyasını göz önünde bulundurun:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

CDNJS sağlayıcısını kullanarak *wwwroot/scripts/jquery* klasörüne jQuery sürümünü 3.2.1 *jquery.min.js* dosyasını yüklemek için:

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

*Libman.json* dosyası aşağıdakilere benzer:

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

C *takvim.js* ve *calendar.css* dosyalarını yüklemek için: *\\\\\\ * dosya sistemi sağlayıcısını kullanarak contosoCalendar'ı geçici olarak kullanma:

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

Aşağıdaki istemi iki nedenden dolayı görünür:

* *Libman.json* dosyasında bir `defaultDestination` özellik yok.
* Komut `libman install` `-d|--destination` seçeneği içermez.

![libman yükleme komutu - hedef](_static/libman-install-destination.png)

Varsayılan hedefi kabul ettikten *sonra, libman.json* dosyası aşağıdakilere benzer:

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

`libman restore` Komut, *libman.json'da*tanımlanan kitaplık dosyalarını yükler. Aşağıdaki kurallar geçerlidir:

* Proje kökünde *libman.json* dosyası yoksa, bir hata döndürülür.
* Kitaplık bir sağlayıcı belirtirse, `defaultProvider` *libman.json'daki* özellik yoksayılır.
* Kitaplık bir hedef belirtirse, `defaultDestination` *libman.json'daki* özellik yoksayılır.

### <a name="synopsis"></a>Özet

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>Seçenekler

`libman restore` Komut için aşağıdaki seçenekler kullanılabilir:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Örnekler

*libman.json'da*tanımlanan kitaplık dosyalarını geri yüklemek için:

```console
libman restore
```

## <a name="delete-library-files"></a>Kitaplık dosyalarını silme

Komut, `libman clean` daha önce LibMan üzerinden geri yüklenen kitaplık dosyalarını siler. Bu işlemden sonra boşalan klasörler silinir. *Libman.json* özelliğindeki `libraries` kitaplık dosyalarının ilişkili yapılandırmaları kaldırılmaz.

### <a name="synopsis"></a>Özet

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>Seçenekler

`libman clean` Komut için aşağıdaki seçenekler kullanılabilir:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Örnekler

LibMan üzerinden yüklenen kitaplık dosyalarını silmek için:

```console
libman clean
```

## <a name="uninstall-library-files"></a>Kitaplık dosyalarını kaldırma

Komut: `libman uninstall`

* Belirtilen kitaplıkla ilişkili tüm dosyaları *libman.json'daki*hedeften siler.
* İlişkili kitaplık yapılandırmasını *libman.json'dan*kaldırır.

Bir hata oluşur:

* Proje kökünde *libman.json* dosyası yok.
* Belirtilen kitaplık yok.

Aynı ada sahip birden fazla kitaplık yüklenmişse, bir kitap seçmeniz istenir.

### <a name="synopsis"></a>Özet

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>Bağımsız Değişkenler

`LIBRARY`

Kaldırılabilmek için kitaplığın adı. Bu ad sürüm numarası gösterimi içerebilir `@1.2.0`(örneğin, ).

### <a name="options"></a>Seçenekler

`libman uninstall` Komut için aşağıdaki seçenekler kullanılabilir:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Örnekler

Aşağıdaki *libman.json* dosyasını göz önünde bulundurun:

[!code-json[](samples/LibManSample/libman.json)]

* jQuery'yi kaldırmak için aşağıdaki komutlardan biri başarılı dır:

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* Sağlayıcı üzerinden yüklenen Lodash dosyalarını `filesystem` kaldırmak için:

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>Kitaplık sürümünü güncelleştirme

Komut, `libman update` LibMan üzerinden yüklenen bir kitaplığı belirtilen sürüme güncelleştirir.

Bir hata oluşur:

* Proje kökünde *libman.json* dosyası yok.
* Belirtilen kitaplık yok.

Aynı ada sahip birden fazla kitaplık yüklenmişse, bir kitap seçmeniz istenir.

### <a name="synopsis"></a>Özet

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>Bağımsız Değişkenler

`LIBRARY`

Güncelleştirilen kitaplığın adı.

### <a name="options"></a>Seçenekler

`libman update` Komut için aşağıdaki seçenekler kullanılabilir:

* `-pre`

  Kitaplığın en son yayın öncesi sürümünü edinin.

* `--to <VERSION>`

  Kitaplığın belirli bir sürümünü edinin.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Örnekler

* jQuery'yi en son sürüme güncellemek için:

  ```console
  libman update jquery
  ```

* jQuery'yi sürüm 3.3.1'e güncellemek için:

  ```console
  libman update jquery --to 3.3.1
  ```

* jQuery'yi en son sürüm öncesi sürümüne güncellemek için:

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>Kitaplık önbelleğini yönetme

Komut, `libman cache` LibMan kitaplık önbelleğini yönetir. Sağlayıcı `filesystem` kitaplık önbelleğini kullanmaz.

### <a name="synopsis"></a>Özet

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>Bağımsız Değişkenler

`PROVIDER`

Yalnızca komutla `clean` kullanılır. Sağlayıcı önbelleğinin temizlenmesini belirtir. Geçerli değerler şunlardır:

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>Seçenekler

`libman cache` Komut için aşağıdaki seçenekler kullanılabilir:

* `--files`

  Önbelleğe alınan dosyaların adlarını listeleyin.

* `--libraries`

  Önbelleğe alınmış kitaplıkların adlarını listele.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Örnekler

* Sağlayıcı başına önbelleğe alınmış kitaplıkların adlarını görüntülemek için aşağıdaki komutlardan birini kullanın:

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  Aşağıdakilere benzer çıktı görüntülenir:

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

* Önbelleğe alınan kitaplık dosyalarının adlarını sağlayıcı başına görüntülemek için:

  ```console
  libman cache list --files
  ```

  Aşağıdakilere benzer çıktı görüntülenir:

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

  Önceki çıktının jQuery sürümlerinin 3.2.1 ve 3.3.1'in CDNJS sağlayıcısı altında önbelleğe alınmış olduğunu gösterdiğine dikkat edin.

* CDNJS sağlayıcısı için kitaplık önbelleğini boşaltmak için:

  ```console
  libman cache clean cdnjs
  ```

  CDNJS sağlayıcı önbelleğini boşalttınktan `libman cache list` sonra, komut aşağıdakileri görüntüler:

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

* Desteklenen tüm sağlayıcılar için önbelleği boşaltmak için:

  ```console
  libman cache clean
  ```

  Tüm sağlayıcı önbelleklerini boşalttınktan `libman cache list` sonra, komut aşağıdakileri görüntüler:

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>Ek kaynaklar

* [Genel Bir Araç Yükleme](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [LibMan GitHub deposu](https://github.com/aspnet/LibraryManager)
