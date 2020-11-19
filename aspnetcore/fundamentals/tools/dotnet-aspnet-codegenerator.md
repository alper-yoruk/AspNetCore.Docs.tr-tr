---
title: DotNet ASPNET-CodeGenerator komutu
author: rick-anderson
description: DotNet ASPNET-CodeGenerator komutu yapı ASP.NET Core projeler.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/16/2020
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
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 8844b0014cac58f414d79df4c64bc0efac75bfe1
ms.sourcegitcommit: d29535ea0b4197443fd884aaa6e5b4b763d04fc7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920709"
---
# <a name="dotnet-aspnet-codegenerator"></a>DotNet ASPNET-CodeGenerator

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

`dotnet aspnet-codegenerator` -ASP.NET Core scafkatlama altyapısını çalıştırır. `dotnet aspnet-codegenerator` yalnızca komut satırından yapı iskelesi sağlamak için gereklidir, Visual Studio ile scafkatlamayı kullanmak gerekli değildir.

## <a name="install-and-update-aspnet-codegenerator"></a>ASPNET-CodeGenerator 'ı yükleyip güncelleştirme

[.NET SDK 'yı](https://dotnet.microsoft.com/download)yükler.

`dotnet-aspnet-codegenerator` yüklenmesi gereken [küresel bir araçtır](/dotnet/core/tools/global-tools) . Aşağıdaki komut aracın en son kararlı sürümünü yüklüyor `dotnet-aspnet-codegenerator` :

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Aşağıdaki komut, `dotnet-aspnet-codegenerator` yüklü .NET Core SDK 'larında kullanılabilen en son kararlı sürümü güncelleştirir:

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="uninstall-aspnet-codegenerator"></a>ASPNET-CodeGenerator 'ı kaldır

`aspnet-codegenerator`Sorunları çözmek için kaldırmak gerekli olabilir. Örneğin, bir önizleme sürümünü yüklediyseniz `aspnet-codegenerator` , yayınlanan sürümü yüklemeden önce bu sürümü kaldırın.

Aşağıdaki komutlar `dotnet-aspnet-codegenerator` Aracı kaldırır ve en son kararlı sürümü kurar:

```dotnetcli
dotnet tool uninstall -g dotnet-aspnet-codegenerator
dotnet tool install -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a>Özeti

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a>Description

`dotnet aspnet-codegenerator`Genel komut ASP.NET Core kod Oluşturucu ve yapı iskelesi altyapısını çalıştırır.

## <a name="arguments"></a>Arguments

`generator`

Çalıştırılacak kod Oluşturucu. Aşağıdaki oluşturucular kullanılabilir:

| Oluşturucu  | İşlem                                                            |
| ---------- | -------------------------------------------------------------------- |
| alan       | [Bir alanı dolandırın](xref:mvc/controllers/areas)                      |
| denetleyici | [Bir denetleyiciyi yapı iskelesi](xref:tutorials/first-mvc-app/adding-model)  |
| identity   | [İşleyicilerin Identity](xref:security/authentication/scaffold-identity) |
| razorpage  | [Yapı iskelesi Razor sayfaları](xref:tutorials/razor-pages/model)            |
| görüntüle       | [Bir görünümü dolandırın](xref:mvc/views/overview)                          |

## <a name="options"></a>Seçenekler

`-n|--nuget-package-dir`

NuGet paket dizinini belirtir.

`-c|--configuration {Debug|Release}`

Yapı yapılandırmasını tanımlar. `Debug` varsayılan değerdir.

`-tfm|--target-framework`

Kullanılacak hedef [çerçeve](/dotnet/standard/frameworks) . Örneğin, `net46`.

`-b|--build-base-path`

Yapı temel yolu.

`-h|--help`

Komut için kısa bir yardım yazdırır.

`--no-build`

Çalıştırmadan önce projeyi oluşturmaz. Ayrıca bayrağı örtülü olarak ayarlar `--no-restore` .

`-p|--project <PATH>`

Çalıştırılacak proje dosyasının yolunu belirtir (klasör adı veya tam yol). Belirtilmezse, varsayılan olarak geçerli dizini alır.

## <a name="generator-options"></a>Oluşturucu seçenekleri

Aşağıdaki bölümler, desteklenen oluşturucular için kullanılabilen seçenekleri ayrıntılandırır:

* Alan
* Kumandasını
* Identity  
* Razorpage (sayfa) 
* Görüntüle

<a name="area"></a>

### <a name="area-options"></a>Alan seçenekleri

Bu araç, denetleyiciler ve görünümler içeren ASP.NET Core Web projelerine yöneliktir. Bu, Razor sayfa uygulamalarına yönelik değildir.

Kullanım: `dotnet aspnet-codegenerator area AreaNameToGenerate`

Yukarıdaki komut aşağıdaki klasörleri oluşturur:

* *Alanlar*
  * *AreaNameToGenerate*
    * *Denetleyiciler*
    * *Veriler*
    * *Modeller*
    * *Görünümler*

<a name="ctl"></a>

### <a name="controller-options"></a>Denetleyici Seçenekleri

Aşağıdaki tabloda ve seçenekleri listelenmiştir  `aspnet-codegenerator` `controller` `razorpage` :

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

Aşağıdaki tabloda aşağıdakiler için benzersiz seçenekler listelenmektedir  `aspnet-codegenerator controller` :

| Seçenek                         | Açıklama                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| --controllerName veya-Name      | Denetleyicinin adı.                                                                                   |
| --Kullanılan Asyncactions veya-async    | Zaman uyumsuz denetleyici eylemleri oluştur.                                                                        |
| --noViews veya-NV               | **Hiçbir** görünüm oluşturun.                                                                                    |
| --restWithNoViews veya-API      | REST stili API ile bir denetleyici oluşturun. `noViews` varsayılır ve tüm görünümle ilgili seçenekler yok sayılır. |
| --readWriteActions veya-Actions | Model olmadan okuma/yazma eylemleri ile denetleyici oluşturun.                                              |

`-h`Komutuyla ilgili yardım için anahtarı kullanın `aspnet-codegenerator controller` :

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

Bir örneği için bkz. [film modelini yapı iskelesi](xref:tutorials/first-mvc-app/adding-model) `dotnet aspnet-codegenerator controller` .

### <a name="no-locrazorpage"></a>Razorpage (sayfa) 

<a name="rp"></a>

Razor Sayfalar, yeni sayfanın adı ve kullanılacak şablon belirtilerek tek tek iskele alınabilir. Desteklenen şablonlar şunlardır:

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Örneğin, aşağıdaki komut *myedit. cshtml* ve *MyEdit.cshtml.cs* oluşturmak için düzenleme şablonunu kullanır:

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

Genellikle, şablon ve oluşturulan dosya adı belirtilmez ve aşağıdaki şablonlar oluşturulur:

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Aşağıdaki tabloda ve seçenekleri listelenmiştir  `aspnet-codegenerator` `razorpage` `controller` :

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

Aşağıdaki tabloda aşağıdakiler için benzersiz seçenekler listelenmektedir  `aspnet-codegenerator razorpage` :

| Seçenek                        | Açıklama                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| --namespaceName veya-Namespace | Oluşturulan PageModel için kullanılacak ad alanının adı                          |
| --partialView veya-Partial     | Kısmi bir görünüm oluşturun. Bu belirtilirse, düzen seçenekleri-l ve-UDL yok sayılır. |
| --noPageModel veya-NPM         | Boş şablon için bir PageModel sınıfı oluşturmamı geç                           |

`-h`Komutuyla ilgili yardım için anahtarı kullanın `aspnet-codegenerator razorpage` :

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

Bir örneği için bkz. [film modelini yapı iskelesi](xref:tutorials/razor-pages/model) `dotnet aspnet-codegenerator razorpage` .

### Identity

Bkz. [Yapı Iskelesi Identity ](xref:security/authentication/scaffold-identity)
