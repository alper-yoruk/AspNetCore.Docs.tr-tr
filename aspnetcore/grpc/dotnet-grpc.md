---
title: dotnet-grpc ile Protobuf başvurularını yönetme
author: juntaoluo
description: Dotnet-grpc global aracıyla Protobuf referanslarını ekleme, güncelleme, kaldırma ve listeleme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: 994597c854a95bb33de1686ab025cb3744cf6845
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667337"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="d6aaa-103">dotnet-grpc ile Protobuf başvurularını yönetme</span><span class="sxs-lookup"><span data-stu-id="d6aaa-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="d6aaa-104">Yazar: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="d6aaa-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="d6aaa-105">`dotnet-grpc`bir .NET gRPC projesi içinde [Protobuf (*.proto*)](xref:grpc/basics#proto-file) referanslarını yönetmek için bir .NET Core Global Aracıdır.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="d6aaa-106">Araç, Protobuf başvuruları eklemek, yenilemek, kaldırmak ve listelemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="d6aaa-107">Yükleme</span><span class="sxs-lookup"><span data-stu-id="d6aaa-107">Installation</span></span>

<span data-ttu-id="d6aaa-108">`dotnet-grpc` [.NET Core Global Aracı'nı](/dotnet/core/tools/global-tools)yüklemek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d6aaa-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="d6aaa-109">Referans ekleme</span><span class="sxs-lookup"><span data-stu-id="d6aaa-109">Add references</span></span>

<span data-ttu-id="d6aaa-110">`dotnet-grpc`*.csproj* dosyasına öğe `<Protobuf />` olarak Protobuf başvuruları eklemek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="d6aaa-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="d6aaa-111">Protobuf başvuruları C# istemcisi ve/veya sunucu varlıklarını oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="d6aaa-112">`dotnet-grpc` Araç şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="d6aaa-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="d6aaa-113">Diskteki yerel dosyalardan bir Protobuf başvurusu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="d6aaa-114">URL tarafından belirtilen uzak bir dosyadan bir Protobuf başvurusu oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="d6aaa-115">Doğru gRPC paket bağımlılıklarının projeye eklenmediğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="d6aaa-116">Örneğin, `Grpc.AspNetCore` paket bir web uygulamasına eklenir.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="d6aaa-117">`Grpc.AspNetCore`gRPC sunucusu ve istemci kitaplıkları ve araç desteği içerir.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="d6aaa-118">Alternatif olarak, `Grpc.Net.Client` `Grpc.Tools` yalnızca `Google.Protobuf` gRPC istemci kitaplıkları ve araç desteği içeren , ve paketler konsol uygulamasına eklenir.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="d6aaa-119">Dosya ekle</span><span class="sxs-lookup"><span data-stu-id="d6aaa-119">Add file</span></span>

<span data-ttu-id="d6aaa-120">Komut, `add-file` Protobuf başvuruları olarak diske yerel dosyaları eklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="d6aaa-121">Sağlanan dosya yolları:</span><span class="sxs-lookup"><span data-stu-id="d6aaa-121">The file paths provided:</span></span>

* <span data-ttu-id="d6aaa-122">Geçerli dizin veya mutlak yollargöreli olabilir.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="d6aaa-123">Desen tabanlı dosya [parlaması](https://wikipedia.org/wiki/Glob_(programming))için joker karakterler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="d6aaa-124">Proje dizininin dışında herhangi bir `Link` dosya varsa, dosyayı Visual `Protos` Studio'daki klasörün altında görüntülemek için bir öğe eklenir.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="d6aaa-125">Kullanım</span><span class="sxs-lookup"><span data-stu-id="d6aaa-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="d6aaa-126">Bağımsız Değişkenler</span><span class="sxs-lookup"><span data-stu-id="d6aaa-126">Arguments</span></span>

| <span data-ttu-id="d6aaa-127">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="d6aaa-127">Argument</span></span> | <span data-ttu-id="d6aaa-128">Açıklama</span><span class="sxs-lookup"><span data-stu-id="d6aaa-128">Description</span></span> |
|-|-|
| <span data-ttu-id="d6aaa-129">files</span><span class="sxs-lookup"><span data-stu-id="d6aaa-129">files</span></span> | <span data-ttu-id="d6aaa-130">Protobuf dosyası referansları.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-130">The protobuf file references.</span></span> <span data-ttu-id="d6aaa-131">Bu yerel protobuf dosyaları için glob bir yol olabilir.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="d6aaa-132">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="d6aaa-132">Options</span></span>

| <span data-ttu-id="d6aaa-133">Kısa seçenek</span><span class="sxs-lookup"><span data-stu-id="d6aaa-133">Short option</span></span> | <span data-ttu-id="d6aaa-134">Uzun seçenek</span><span class="sxs-lookup"><span data-stu-id="d6aaa-134">Long option</span></span> | <span data-ttu-id="d6aaa-135">Açıklama</span><span class="sxs-lookup"><span data-stu-id="d6aaa-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="d6aaa-136">-p</span><span class="sxs-lookup"><span data-stu-id="d6aaa-136">-p</span></span> | <span data-ttu-id="d6aaa-137">--proje</span><span class="sxs-lookup"><span data-stu-id="d6aaa-137">--project</span></span> | <span data-ttu-id="d6aaa-138">Çalışacak proje dosyasına giden yol.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-138">The path to the project file to operate on.</span></span> <span data-ttu-id="d6aaa-139">Bir dosya belirtilmemişse, komut geçerli dizini arar.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="d6aaa-140">-s</span><span class="sxs-lookup"><span data-stu-id="d6aaa-140">-s</span></span> | <span data-ttu-id="d6aaa-141">--hizmetler</span><span class="sxs-lookup"><span data-stu-id="d6aaa-141">--services</span></span> | <span data-ttu-id="d6aaa-142">Oluşturulması gereken gRPC hizmetlerinin türü.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="d6aaa-143">`Default` Belirtilirse, `Both` Web projeleri için `Client` kullanılır ve Web olmayan projeler için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="d6aaa-144">Kabul edilen `Both`değerler `Client` `Default`, `None` `Server`, , , .</span><span class="sxs-lookup"><span data-stu-id="d6aaa-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="d6aaa-145">-i</span><span class="sxs-lookup"><span data-stu-id="d6aaa-145">-i</span></span> | <span data-ttu-id="d6aaa-146">--ek-alma-dirs</span><span class="sxs-lookup"><span data-stu-id="d6aaa-146">--additional-import-dirs</span></span> | <span data-ttu-id="d6aaa-147">Protobuf dosyaları için içeri aktarımları çözerken kullanılacak ek dizinler.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="d6aaa-148">Bu, yarım nokta nokta lı ayrılmış bir yol listesidir.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="d6aaa-149">--erişim</span><span class="sxs-lookup"><span data-stu-id="d6aaa-149">--access</span></span> | <span data-ttu-id="d6aaa-150">Oluşturulan C# sınıfları için kullanılacak erişim değiştirici.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="d6aaa-151">Varsayılan değer: `Public`.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-151">The default value is `Public`.</span></span> <span data-ttu-id="d6aaa-152">Kabul edilen `Internal` değerler `Public`ve .</span><span class="sxs-lookup"><span data-stu-id="d6aaa-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="d6aaa-153">URL ekle</span><span class="sxs-lookup"><span data-stu-id="d6aaa-153">Add URL</span></span>

<span data-ttu-id="d6aaa-154">Komut, `add-url` kaynak URL tarafından Protobuf başvurusu olarak belirtilen uzak bir dosya eklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="d6aaa-155">Uzak dosyanın nerede indirilir belirtilmesi için bir dosya yolu sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="d6aaa-156">Dosya yolu geçerli dizine veya mutlak bir yola göreolabilir.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="d6aaa-157">Dosya yolu proje dizininin dışındaysa, `Link` dosyayı Visual Studio'daki sanal `Protos` klasörün altında görüntülemek için bir öğe eklenir.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="d6aaa-158">Kullanım</span><span class="sxs-lookup"><span data-stu-id="d6aaa-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="d6aaa-159">Bağımsız Değişkenler</span><span class="sxs-lookup"><span data-stu-id="d6aaa-159">Arguments</span></span>

| <span data-ttu-id="d6aaa-160">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="d6aaa-160">Argument</span></span> | <span data-ttu-id="d6aaa-161">Açıklama</span><span class="sxs-lookup"><span data-stu-id="d6aaa-161">Description</span></span> |
|-|-|
| <span data-ttu-id="d6aaa-162">url</span><span class="sxs-lookup"><span data-stu-id="d6aaa-162">url</span></span> | <span data-ttu-id="d6aaa-163">Uzak bir protobuf dosyasının URL'si.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="d6aaa-164">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="d6aaa-164">Options</span></span>

| <span data-ttu-id="d6aaa-165">Kısa seçenek</span><span class="sxs-lookup"><span data-stu-id="d6aaa-165">Short option</span></span> | <span data-ttu-id="d6aaa-166">Uzun seçenek</span><span class="sxs-lookup"><span data-stu-id="d6aaa-166">Long option</span></span> | <span data-ttu-id="d6aaa-167">Açıklama</span><span class="sxs-lookup"><span data-stu-id="d6aaa-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="d6aaa-168">-o</span><span class="sxs-lookup"><span data-stu-id="d6aaa-168">-o</span></span> | <span data-ttu-id="d6aaa-169">--output</span><span class="sxs-lookup"><span data-stu-id="d6aaa-169">--output</span></span> | <span data-ttu-id="d6aaa-170">Uzaktan protobuf dosyasının indirme yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="d6aaa-171">Bu gerekli bir seçenektir.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-171">This is a required option.</span></span>
| <span data-ttu-id="d6aaa-172">-p</span><span class="sxs-lookup"><span data-stu-id="d6aaa-172">-p</span></span> | <span data-ttu-id="d6aaa-173">--proje</span><span class="sxs-lookup"><span data-stu-id="d6aaa-173">--project</span></span> | <span data-ttu-id="d6aaa-174">Çalışacak proje dosyasına giden yol.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-174">The path to the project file to operate on.</span></span> <span data-ttu-id="d6aaa-175">Bir dosya belirtilmemişse, komut geçerli dizini arar.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="d6aaa-176">-s</span><span class="sxs-lookup"><span data-stu-id="d6aaa-176">-s</span></span> | <span data-ttu-id="d6aaa-177">--hizmetler</span><span class="sxs-lookup"><span data-stu-id="d6aaa-177">--services</span></span> | <span data-ttu-id="d6aaa-178">Oluşturulması gereken gRPC hizmetlerinin türü.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="d6aaa-179">`Default` Belirtilirse, `Both` Web projeleri için `Client` kullanılır ve Web olmayan projeler için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="d6aaa-180">Kabul edilen `Both`değerler `Client` `Default`, `None` `Server`, , , .</span><span class="sxs-lookup"><span data-stu-id="d6aaa-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="d6aaa-181">-i</span><span class="sxs-lookup"><span data-stu-id="d6aaa-181">-i</span></span> | <span data-ttu-id="d6aaa-182">--ek-alma-dirs</span><span class="sxs-lookup"><span data-stu-id="d6aaa-182">--additional-import-dirs</span></span> | <span data-ttu-id="d6aaa-183">Protobuf dosyaları için içeri aktarımları çözerken kullanılacak ek dizinler.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="d6aaa-184">Bu, yarım nokta nokta lı ayrılmış bir yol listesidir.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="d6aaa-185">--erişim</span><span class="sxs-lookup"><span data-stu-id="d6aaa-185">--access</span></span> | <span data-ttu-id="d6aaa-186">Oluşturulan C# sınıfları için kullanılacak erişim değiştirici.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="d6aaa-187">Varsayılan değer. `Public`</span><span class="sxs-lookup"><span data-stu-id="d6aaa-187">Default value is `Public`.</span></span> <span data-ttu-id="d6aaa-188">Kabul edilen `Internal` değerler `Public`ve .</span><span class="sxs-lookup"><span data-stu-id="d6aaa-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="d6aaa-189">Kaldır</span><span class="sxs-lookup"><span data-stu-id="d6aaa-189">Remove</span></span>

<span data-ttu-id="d6aaa-190">Komut, `remove` *.csproj* dosyasından Protobuf başvurularını kaldırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="d6aaa-191">Komut, yol bağımsız değişkenlerini ve kaynak URL'lerini bağımsız değişken olarak kabul eder.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="d6aaa-192">Araç:</span><span class="sxs-lookup"><span data-stu-id="d6aaa-192">The tool:</span></span>

* <span data-ttu-id="d6aaa-193">Yalnızca Protobuf referansını kaldırır.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="d6aaa-194">Uzak bir URL'den ilk olarak indirilmiş olsa bile *.proto* dosyasını silmez.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="d6aaa-195">Kullanım</span><span class="sxs-lookup"><span data-stu-id="d6aaa-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="d6aaa-196">Bağımsız Değişkenler</span><span class="sxs-lookup"><span data-stu-id="d6aaa-196">Arguments</span></span>

| <span data-ttu-id="d6aaa-197">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="d6aaa-197">Argument</span></span> | <span data-ttu-id="d6aaa-198">Açıklama</span><span class="sxs-lookup"><span data-stu-id="d6aaa-198">Description</span></span> |
|-|-|
| <span data-ttu-id="d6aaa-199">başvurular</span><span class="sxs-lookup"><span data-stu-id="d6aaa-199">references</span></span> | <span data-ttu-id="d6aaa-200">Kaldırılacak protobuf başvurularının URL'leri veya dosya yolları.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="d6aaa-201">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="d6aaa-201">Options</span></span>

| <span data-ttu-id="d6aaa-202">Kısa seçenek</span><span class="sxs-lookup"><span data-stu-id="d6aaa-202">Short option</span></span> | <span data-ttu-id="d6aaa-203">Uzun seçenek</span><span class="sxs-lookup"><span data-stu-id="d6aaa-203">Long option</span></span> | <span data-ttu-id="d6aaa-204">Açıklama</span><span class="sxs-lookup"><span data-stu-id="d6aaa-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="d6aaa-205">-p</span><span class="sxs-lookup"><span data-stu-id="d6aaa-205">-p</span></span> | <span data-ttu-id="d6aaa-206">--proje</span><span class="sxs-lookup"><span data-stu-id="d6aaa-206">--project</span></span> | <span data-ttu-id="d6aaa-207">Çalışacak proje dosyasına giden yol.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-207">The path to the project file to operate on.</span></span> <span data-ttu-id="d6aaa-208">Bir dosya belirtilmemişse, komut geçerli dizini arar.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="d6aaa-209">Yenile</span><span class="sxs-lookup"><span data-stu-id="d6aaa-209">Refresh</span></span>

<span data-ttu-id="d6aaa-210">Komut, kaynak URL'deki `refresh` en son içerikle uzak bir başvuruyu güncelleştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="d6aaa-211">Hem karşıdan yükleme dosyası yolu hem de kaynak URL güncellenecek başvuruyu belirtmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="d6aaa-212">Not:</span><span class="sxs-lookup"><span data-stu-id="d6aaa-212">Note:</span></span>

* <span data-ttu-id="d6aaa-213">Yerel dosyanın güncelleştirilip güncelleştirilmeyeceğini belirlemek için dosya içeriğinin işlenme leri karşılaştırılır.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="d6aaa-214">Hiçbir zaman damgası bilgisi karşılaştırılmada.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-214">No timestamp information is compared.</span></span>

<span data-ttu-id="d6aaa-215">Bir güncelleştirme gerekiyorsa araç her zaman uzak dosya ile yerel dosya değiştirir.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="d6aaa-216">Kullanım</span><span class="sxs-lookup"><span data-stu-id="d6aaa-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="d6aaa-217">Bağımsız Değişkenler</span><span class="sxs-lookup"><span data-stu-id="d6aaa-217">Arguments</span></span>

| <span data-ttu-id="d6aaa-218">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="d6aaa-218">Argument</span></span> | <span data-ttu-id="d6aaa-219">Açıklama</span><span class="sxs-lookup"><span data-stu-id="d6aaa-219">Description</span></span> |
|-|-|
| <span data-ttu-id="d6aaa-220">başvurular</span><span class="sxs-lookup"><span data-stu-id="d6aaa-220">references</span></span> | <span data-ttu-id="d6aaa-221">URL'ler veya dosya yolları güncelleştirilmelidir uzak protobuf başvuruları için.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="d6aaa-222">Tüm uzak başvuruları yenilemek için bu bağımsız değişkeni boş bırakın.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="d6aaa-223">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="d6aaa-223">Options</span></span>

| <span data-ttu-id="d6aaa-224">Kısa seçenek</span><span class="sxs-lookup"><span data-stu-id="d6aaa-224">Short option</span></span> | <span data-ttu-id="d6aaa-225">Uzun seçenek</span><span class="sxs-lookup"><span data-stu-id="d6aaa-225">Long option</span></span> | <span data-ttu-id="d6aaa-226">Açıklama</span><span class="sxs-lookup"><span data-stu-id="d6aaa-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="d6aaa-227">-p</span><span class="sxs-lookup"><span data-stu-id="d6aaa-227">-p</span></span> | <span data-ttu-id="d6aaa-228">--proje</span><span class="sxs-lookup"><span data-stu-id="d6aaa-228">--project</span></span> | <span data-ttu-id="d6aaa-229">Çalışacak proje dosyasına giden yol.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-229">The path to the project file to operate on.</span></span> <span data-ttu-id="d6aaa-230">Bir dosya belirtilmemişse, komut geçerli dizini arar.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="d6aaa-231">--kuru-çalıştır</span><span class="sxs-lookup"><span data-stu-id="d6aaa-231">--dry-run</span></span> | <span data-ttu-id="d6aaa-232">Yeni içerik indirilmeden güncellenecek dosyaların listesini çıktırıyor.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="d6aaa-233">Liste</span><span class="sxs-lookup"><span data-stu-id="d6aaa-233">List</span></span>

<span data-ttu-id="d6aaa-234">Komut, `list` proje dosyasındaki tüm Protobuf başvurularını görüntülemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="d6aaa-235">Bir sütunun tüm değerleri varsayılan değerlerse, sütun atlanabilir.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="d6aaa-236">Kullanım</span><span class="sxs-lookup"><span data-stu-id="d6aaa-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="d6aaa-237">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="d6aaa-237">Options</span></span>

| <span data-ttu-id="d6aaa-238">Kısa seçenek</span><span class="sxs-lookup"><span data-stu-id="d6aaa-238">Short option</span></span> | <span data-ttu-id="d6aaa-239">Uzun seçenek</span><span class="sxs-lookup"><span data-stu-id="d6aaa-239">Long option</span></span> | <span data-ttu-id="d6aaa-240">Açıklama</span><span class="sxs-lookup"><span data-stu-id="d6aaa-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="d6aaa-241">-p</span><span class="sxs-lookup"><span data-stu-id="d6aaa-241">-p</span></span> | <span data-ttu-id="d6aaa-242">--proje</span><span class="sxs-lookup"><span data-stu-id="d6aaa-242">--project</span></span> | <span data-ttu-id="d6aaa-243">Çalışacak proje dosyasına giden yol.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-243">The path to the project file to operate on.</span></span> <span data-ttu-id="d6aaa-244">Bir dosya belirtilmemişse, komut geçerli dizini arar.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d6aaa-245">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d6aaa-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
