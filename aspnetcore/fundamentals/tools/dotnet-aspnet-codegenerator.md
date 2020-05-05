---
title: DotNet ASPNET-CodeGenerator komutu
author: rick-anderson
description: DotNet ASPNET-CodeGenerator komutu yapı ASP.NET Core projeler.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 58f7aa30d3e916307437d56c61e80765ac0c21cf
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766478"
---
# <a name="dotnet-aspnet-codegenerator"></a>DotNet ASPNET-CodeGenerator

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

`dotnet aspnet-codegenerator`-ASP.NET Core scafkatlama altyapısını çalıştırır. `dotnet aspnet-codegenerator`yalnızca komut satırından yapı iskelesi sağlamak için gereklidir, Visual Studio ile scafkatlamayı kullanmak gerekli değildir.

Bu makale [.NET Core 2,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) ve üzeri için geçerlidir.

## <a name="installing-aspnet-codegenerator"></a>ASPNET-CodeGenerator yükleniyor

`dotnet-aspnet-codegenerator`yüklenmesi gereken [küresel bir araçtır](/dotnet/core/tools/global-tools) . Aşağıdaki komut `dotnet-aspnet-codegenerator` aracın en son kararlı sürümünü yüklüyor:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Aşağıdaki komut, yüklü `dotnet-aspnet-codegenerator` .NET Core SDK 'larında kullanılabilen en son kararlı sürümü güncelleştirir:

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a>Özeti

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a>Açıklama

`dotnet aspnet-codegenerator` Genel komut ASP.NET Core kod Oluşturucu ve yapı iskelesi altyapısını çalıştırır.

## <a name="arguments"></a>Bağımsız Değişkenler

`generator`

Çalıştırılacak kod Oluşturucu. Aşağıdaki oluşturucular kullanılabilir:

| Oluşturucu | İşlem |
| ----------------- | ------------ | 
| alan      | [Bir alanı dolandırın](/aspnet/core/mvc/controllers/areas) |
  denetleyici| [Bir denetleyiciyi yapı iskelesi](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  identity  | [Yapı iskelesi kimliği](/aspnet/core/security/authentication/scaffold-identity) |
  razorpage | [Yapı iskelesi Razor Pages](/aspnet/core/tutorials/razor-pages/model) |
  görüntüle      | [Bir görünümü dolandırın](/aspnet/core/mvc/views/overview) |

## <a name="options"></a>Seçenekler

`-n|--nuget-package-dir`

NuGet paket dizinini belirtir.

`-c|--configuration {Debug|Release}`

Yapı yapılandırmasını tanımlar. Varsayılan değer: `Debug`.

`-tfm|--target-framework`

Kullanılacak hedef [çerçeve](/dotnet/standard/frameworks) . Örneğin, `net46`.

`-b|--build-base-path`

Yapı temel yolu.

`-h|--help`

Komut için kısa bir yardım yazdırır.

`--no-build`

Çalıştırmadan önce projeyi oluşturmaz. Ayrıca `--no-restore` bayrağı örtülü olarak ayarlar.

`-p|--project <PATH>`

Çalıştırılacak proje dosyasının yolunu belirtir (klasör adı veya tam yol). Belirtilmezse, varsayılan olarak geçerli dizini alır.

## <a name="generator-options"></a>Oluşturucu seçenekleri

Aşağıdaki bölümler, desteklenen oluşturucular için kullanılabilen seçenekleri ayrıntılandırır:

* Alan
* Kumandasını
* Kimlik  
* Razorpage
* Görüntüle

<a name="area"></a>

### <a name="area-options"></a>Alan seçenekleri

Bu araç, denetleyiciler ve görünümler içeren ASP.NET Core Web projelerine yöneliktir. Razor Pages uygulamalarına yönelik değildir.

Kullanım: `dotnet aspnet-codegenerator area AreaNameToGenerate`

Yukarıdaki komut aşağıdaki klasörleri oluşturur:

* *Alanlar*
  * *AreaNameToGenerate*
    * *Denetleyiciler*
    * *Veri*
    * *Modeller*
    * *Görünümler*

<a name="ctl"></a>

### <a name="controller-options"></a>Denetleyici Seçenekleri

Aşağıdaki tabloda `aspnet-codegenerator` `controller` ve `razorpage`seçenekleri listelenmiştir:

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

Aşağıdaki tabloda aşağıdakiler için `aspnet-codegenerator controller`benzersiz seçenekler listelenmektedir:

| Seçenek               | Açıklama|
| ----------------- | ------------ |
| --controllerName veya-Name | Denetleyicinin adı. |
| --Kullanılan Asyncactions veya-async | Zaman uyumsuz denetleyici eylemleri oluştur. |
| --noViews veya-NV | **Hiçbir** görünüm oluşturun. |
| --restWithNoViews veya-API  | REST stili API ile bir denetleyici oluşturun. `noViews`varsayılır ve tüm görünümle ilgili seçenekler yok sayılır. |
| --readWriteActions veya-Actions | Model olmadan okuma/yazma eylemleri ile denetleyici oluşturun. |

`aspnet-codegenerator controller` Komutuyla ilgili yardım için `-h` anahtarı kullanın:

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

Bir örneği için bkz `dotnet aspnet-codegenerator controller`. [film modelini yapı iskelesi](/aspnet/core/tutorials/razor-pages/model) .

### <a name="razorpage"></a>Razorpage

<a name="rp"></a>

Razor Pages yeni sayfanın adı ve kullanılacak şablon belirtilerek tek tek iskele alınabilir. Desteklenen şablonlar şunlardır:

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Örneğin, aşağıdaki komut *myedit. cshtml* ve *MyEdit.cshtml.cs*oluşturmak için düzenleme şablonunu kullanır:

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

Genellikle, şablon ve oluşturulan dosya adı belirtilmez ve aşağıdaki şablonlar oluşturulur:

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Aşağıdaki tabloda `aspnet-codegenerator` `razorpage` ve `controller`seçenekleri listelenmiştir:

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

Aşağıdaki tabloda aşağıdakiler için `aspnet-codegenerator razorpage`benzersiz seçenekler listelenmektedir:

| Seçenek               | Açıklama|
| ----------------- | ------------ |
|   --namespaceName veya-Namespace | Oluşturulan PageModel için kullanılacak ad alanının adı |
| --partialView veya-Partial | Kısmi bir görünüm oluşturun. Bu belirtilirse, düzen seçenekleri-l ve-UDL yok sayılır. |
| --noPageModel veya-NPM | Boş şablon için bir PageModel sınıfı oluşturmamı geç |

`aspnet-codegenerator razorpage` Komutuyla ilgili yardım için `-h` anahtarı kullanın:

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

Bir örneği için bkz `dotnet aspnet-codegenerator razorpage`. [film modelini yapı iskelesi](/aspnet/core/tutorials/razor-pages/model) .

### Identity

Bkz. [Yapı Iskelesi Identity ](/aspnet/core/security/authentication/scaffold-identity)
