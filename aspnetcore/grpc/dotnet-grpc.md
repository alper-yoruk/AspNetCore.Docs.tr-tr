---
title: dotnet-grpc ile Protobuf başvurularını yönetme
author: juntaoluo
description: DotNet-GRPC küresel aracıyla prototip başvuruları ekleme, güncelleştirme, kaldırma ve listeleme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
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
uid: grpc/dotnet-grpc
ms.openlocfilehash: f34e1543d9695e138a85db3b79e013cf5fb6d138
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059916"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="6211b-103">dotnet-grpc ile Protobuf başvurularını yönetme</span><span class="sxs-lookup"><span data-stu-id="6211b-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="6211b-104">[John Luo](https://github.com/juntaoluo) tarafından</span><span class="sxs-lookup"><span data-stu-id="6211b-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="6211b-105">`dotnet-grpc` , bir .NET gRPC projesindeki [prototip (*. proto*)](xref:grpc/basics#proto-file) başvurularını yönetmeye yönelik .NET Core küresel bir araçtır.</span><span class="sxs-lookup"><span data-stu-id="6211b-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="6211b-106">Araç, prototipleme başvurularını eklemek, yenilemek, kaldırmak ve listelemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6211b-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="6211b-107">Yükleme</span><span class="sxs-lookup"><span data-stu-id="6211b-107">Installation</span></span>

<span data-ttu-id="6211b-108">`dotnet-grpc` [.NET Core küresel aracını](/dotnet/core/tools/global-tools)yüklemek için şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="6211b-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="6211b-109">Başvuru Ekle</span><span class="sxs-lookup"><span data-stu-id="6211b-109">Add references</span></span>

<span data-ttu-id="6211b-110">`dotnet-grpc` , `<Protobuf />` *. csproj* dosyasına öğe olarak prototip başvuruları eklemek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="6211b-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="6211b-111">Prototip başvuruları, C# istemcisi ve/veya sunucu varlıklarını oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6211b-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="6211b-112">`dotnet-grpc`Araç şunları yapabilir:</span><span class="sxs-lookup"><span data-stu-id="6211b-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="6211b-113">Diskteki yerel dosyalardan Prototipsiz başvuru oluştur.</span><span class="sxs-lookup"><span data-stu-id="6211b-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="6211b-114">Bir URL ile belirtilen uzak dosyadan Protobir başvuru oluştur.</span><span class="sxs-lookup"><span data-stu-id="6211b-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="6211b-115">Projeye doğru gRPC paket bağımlılıklarının eklendiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="6211b-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="6211b-116">Örneğin, `Grpc.AspNetCore` paket bir Web uygulamasına eklenir.</span><span class="sxs-lookup"><span data-stu-id="6211b-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="6211b-117">`Grpc.AspNetCore` gRPC sunucusu ve istemci kitaplıklarını ve araç desteğini içerir.</span><span class="sxs-lookup"><span data-stu-id="6211b-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="6211b-118">Alternatif olarak, `Grpc.Net.Client` `Grpc.Tools` ve `Google.Protobuf` yalnızca GRPC istemci kitaplıklarını ve araç desteğini içeren paketleri konsol uygulamasına eklenir.</span><span class="sxs-lookup"><span data-stu-id="6211b-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="6211b-119">Dosya ekle</span><span class="sxs-lookup"><span data-stu-id="6211b-119">Add file</span></span>

<span data-ttu-id="6211b-120">`add-file`Komut, disk üzerindeki yerel dosyaları prototip başvuruları olarak eklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6211b-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="6211b-121">Belirtilen dosya yolları:</span><span class="sxs-lookup"><span data-stu-id="6211b-121">The file paths provided:</span></span>

* <span data-ttu-id="6211b-122">Geçerli dizin veya mutlak yollarla göreli olabilir.</span><span class="sxs-lookup"><span data-stu-id="6211b-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="6211b-123">, [Glob](https://wikipedia.org/wiki/Glob_(programming))model tabanlı dosya için joker karakterler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="6211b-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="6211b-124">Herhangi bir dosya proje dizininin dışındaysa, `Link` dosyayı `Protos` Visual Studio 'daki klasörü altında göstermek için bir öğe eklenir.</span><span class="sxs-lookup"><span data-stu-id="6211b-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="6211b-125">Kullanım</span><span class="sxs-lookup"><span data-stu-id="6211b-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="6211b-126">Arguments</span><span class="sxs-lookup"><span data-stu-id="6211b-126">Arguments</span></span>

| <span data-ttu-id="6211b-127">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="6211b-127">Argument</span></span> | <span data-ttu-id="6211b-128">Açıklama</span><span class="sxs-lookup"><span data-stu-id="6211b-128">Description</span></span> |
|-|-|
| <span data-ttu-id="6211b-129">files</span><span class="sxs-lookup"><span data-stu-id="6211b-129">files</span></span> | <span data-ttu-id="6211b-130">Prototip dosyası başvuruları.</span><span class="sxs-lookup"><span data-stu-id="6211b-130">The protobuf file references.</span></span> <span data-ttu-id="6211b-131">Bunlar yerel prototipli dosyalar için glob 'nin bir yolu olabilir.</span><span class="sxs-lookup"><span data-stu-id="6211b-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="6211b-132">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="6211b-132">Options</span></span>

| <span data-ttu-id="6211b-133">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="6211b-133">Short option</span></span> | <span data-ttu-id="6211b-134">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="6211b-134">Long option</span></span> | <span data-ttu-id="6211b-135">Açıklama</span><span class="sxs-lookup"><span data-stu-id="6211b-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="6211b-136">-p</span><span class="sxs-lookup"><span data-stu-id="6211b-136">-p</span></span> | <span data-ttu-id="6211b-137">--Proje</span><span class="sxs-lookup"><span data-stu-id="6211b-137">--project</span></span> | <span data-ttu-id="6211b-138">Üzerinde çalışılacak proje dosyasının yolu.</span><span class="sxs-lookup"><span data-stu-id="6211b-138">The path to the project file to operate on.</span></span> <span data-ttu-id="6211b-139">Bir dosya belirtilmemişse, komut geçerli dizinde bir arama yapar.</span><span class="sxs-lookup"><span data-stu-id="6211b-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="6211b-140">-s</span><span class="sxs-lookup"><span data-stu-id="6211b-140">-s</span></span> | <span data-ttu-id="6211b-141">--Hizmetler</span><span class="sxs-lookup"><span data-stu-id="6211b-141">--services</span></span> | <span data-ttu-id="6211b-142">Oluşturulması gereken gRPC Hizmetleri türü.</span><span class="sxs-lookup"><span data-stu-id="6211b-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="6211b-143">`Default`Belirtilmişse, `Both` Web projeleri için kullanılır ve `Client` Web dışı projeler için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6211b-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="6211b-144">Kabul edilen değerler,,,, `Both` `Client` `Default` `None` `Server` .</span><span class="sxs-lookup"><span data-stu-id="6211b-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="6211b-145">-i</span><span class="sxs-lookup"><span data-stu-id="6211b-145">-i</span></span> | <span data-ttu-id="6211b-146">--ek-içeri aktarma-Dizin</span><span class="sxs-lookup"><span data-stu-id="6211b-146">--additional-import-dirs</span></span> | <span data-ttu-id="6211b-147">Prototip dosyaları için içeri aktarmalar çözümlenirken kullanılacak ek dizinler.</span><span class="sxs-lookup"><span data-stu-id="6211b-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="6211b-148">Bu, yolların noktalı virgülle ayrılmış listesidir.</span><span class="sxs-lookup"><span data-stu-id="6211b-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="6211b-149">--erişim</span><span class="sxs-lookup"><span data-stu-id="6211b-149">--access</span></span> | <span data-ttu-id="6211b-150">Oluşturulan C# sınıfları için kullanılacak erişim değiştiricisi.</span><span class="sxs-lookup"><span data-stu-id="6211b-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="6211b-151">`Public` varsayılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="6211b-151">The default value is `Public`.</span></span> <span data-ttu-id="6211b-152">Kabul edilen değerler `Internal` şunlardır `Public` .</span><span class="sxs-lookup"><span data-stu-id="6211b-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="6211b-153">URL Ekle</span><span class="sxs-lookup"><span data-stu-id="6211b-153">Add URL</span></span>

<span data-ttu-id="6211b-154">`add-url`Komut, kaynak URL tarafından belirtilen bir uzak dosyayı prototipde başvuru olarak eklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6211b-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="6211b-155">Uzak dosyanın nereye indirileceği belirtmek için bir dosya yolu belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="6211b-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="6211b-156">Dosya yolu, geçerli dizin veya mutlak bir yol ile ilişkili olabilir.</span><span class="sxs-lookup"><span data-stu-id="6211b-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="6211b-157">Dosya yolu proje dizininin dışındaysa, `Link` dosyayı `Protos` Visual Studio 'daki sanal klasör altında göstermek için bir öğe eklenir.</span><span class="sxs-lookup"><span data-stu-id="6211b-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="6211b-158">Kullanım</span><span class="sxs-lookup"><span data-stu-id="6211b-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="6211b-159">Arguments</span><span class="sxs-lookup"><span data-stu-id="6211b-159">Arguments</span></span>

| <span data-ttu-id="6211b-160">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="6211b-160">Argument</span></span> | <span data-ttu-id="6211b-161">Açıklama</span><span class="sxs-lookup"><span data-stu-id="6211b-161">Description</span></span> |
|-|-|
| <span data-ttu-id="6211b-162">url</span><span class="sxs-lookup"><span data-stu-id="6211b-162">url</span></span> | <span data-ttu-id="6211b-163">Uzak protoarabellek dosyasının URL 'SI.</span><span class="sxs-lookup"><span data-stu-id="6211b-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="6211b-164">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="6211b-164">Options</span></span>

| <span data-ttu-id="6211b-165">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="6211b-165">Short option</span></span> | <span data-ttu-id="6211b-166">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="6211b-166">Long option</span></span> | <span data-ttu-id="6211b-167">Açıklama</span><span class="sxs-lookup"><span data-stu-id="6211b-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="6211b-168">-o</span><span class="sxs-lookup"><span data-stu-id="6211b-168">-o</span></span> | <span data-ttu-id="6211b-169">--output</span><span class="sxs-lookup"><span data-stu-id="6211b-169">--output</span></span> | <span data-ttu-id="6211b-170">Uzak protoarabellek dosyası için indirme yolunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="6211b-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="6211b-171">Bu gerekli bir seçenektir.</span><span class="sxs-lookup"><span data-stu-id="6211b-171">This is a required option.</span></span>
| <span data-ttu-id="6211b-172">-p</span><span class="sxs-lookup"><span data-stu-id="6211b-172">-p</span></span> | <span data-ttu-id="6211b-173">--Proje</span><span class="sxs-lookup"><span data-stu-id="6211b-173">--project</span></span> | <span data-ttu-id="6211b-174">Üzerinde çalışılacak proje dosyasının yolu.</span><span class="sxs-lookup"><span data-stu-id="6211b-174">The path to the project file to operate on.</span></span> <span data-ttu-id="6211b-175">Bir dosya belirtilmemişse, komut geçerli dizinde bir arama yapar.</span><span class="sxs-lookup"><span data-stu-id="6211b-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="6211b-176">-s</span><span class="sxs-lookup"><span data-stu-id="6211b-176">-s</span></span> | <span data-ttu-id="6211b-177">--Hizmetler</span><span class="sxs-lookup"><span data-stu-id="6211b-177">--services</span></span> | <span data-ttu-id="6211b-178">Oluşturulması gereken gRPC Hizmetleri türü.</span><span class="sxs-lookup"><span data-stu-id="6211b-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="6211b-179">`Default`Belirtilmişse, `Both` Web projeleri için kullanılır ve `Client` Web dışı projeler için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6211b-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="6211b-180">Kabul edilen değerler,,,, `Both` `Client` `Default` `None` `Server` .</span><span class="sxs-lookup"><span data-stu-id="6211b-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="6211b-181">-i</span><span class="sxs-lookup"><span data-stu-id="6211b-181">-i</span></span> | <span data-ttu-id="6211b-182">--ek-içeri aktarma-Dizin</span><span class="sxs-lookup"><span data-stu-id="6211b-182">--additional-import-dirs</span></span> | <span data-ttu-id="6211b-183">Prototip dosyaları için içeri aktarmalar çözümlenirken kullanılacak ek dizinler.</span><span class="sxs-lookup"><span data-stu-id="6211b-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="6211b-184">Bu, yolların noktalı virgülle ayrılmış listesidir.</span><span class="sxs-lookup"><span data-stu-id="6211b-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="6211b-185">--erişim</span><span class="sxs-lookup"><span data-stu-id="6211b-185">--access</span></span> | <span data-ttu-id="6211b-186">Oluşturulan C# sınıfları için kullanılacak erişim değiştiricisi.</span><span class="sxs-lookup"><span data-stu-id="6211b-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="6211b-187">Varsayılan değer `Public` olarak belirlenmiştir.</span><span class="sxs-lookup"><span data-stu-id="6211b-187">Default value is `Public`.</span></span> <span data-ttu-id="6211b-188">Kabul edilen değerler `Internal` şunlardır `Public` .</span><span class="sxs-lookup"><span data-stu-id="6211b-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="6211b-189">Kaldır</span><span class="sxs-lookup"><span data-stu-id="6211b-189">Remove</span></span>

<span data-ttu-id="6211b-190">`remove`Komut, *. csproj* dosyasından prototipsiz başvuruları kaldırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6211b-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="6211b-191">Komut bağımsız değişken olarak yol bağımsız değişkenlerini ve kaynak URL 'Lerini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="6211b-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="6211b-192">Araç:</span><span class="sxs-lookup"><span data-stu-id="6211b-192">The tool:</span></span>

* <span data-ttu-id="6211b-193">Yalnızca Prototipsiz başvuruyu kaldırır.</span><span class="sxs-lookup"><span data-stu-id="6211b-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="6211b-194">, İlk olarak uzak bir URL 'den indirilse bile, *. proto* dosyasını silmez.</span><span class="sxs-lookup"><span data-stu-id="6211b-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="6211b-195">Kullanım</span><span class="sxs-lookup"><span data-stu-id="6211b-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="6211b-196">Arguments</span><span class="sxs-lookup"><span data-stu-id="6211b-196">Arguments</span></span>

| <span data-ttu-id="6211b-197">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="6211b-197">Argument</span></span> | <span data-ttu-id="6211b-198">Açıklama</span><span class="sxs-lookup"><span data-stu-id="6211b-198">Description</span></span> |
|-|-|
| <span data-ttu-id="6211b-199">başvurular</span><span class="sxs-lookup"><span data-stu-id="6211b-199">references</span></span> | <span data-ttu-id="6211b-200">Kaldırılacak prototip başvurularının URL 'Leri veya dosya yolları.</span><span class="sxs-lookup"><span data-stu-id="6211b-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="6211b-201">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="6211b-201">Options</span></span>

| <span data-ttu-id="6211b-202">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="6211b-202">Short option</span></span> | <span data-ttu-id="6211b-203">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="6211b-203">Long option</span></span> | <span data-ttu-id="6211b-204">Açıklama</span><span class="sxs-lookup"><span data-stu-id="6211b-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="6211b-205">-p</span><span class="sxs-lookup"><span data-stu-id="6211b-205">-p</span></span> | <span data-ttu-id="6211b-206">--Proje</span><span class="sxs-lookup"><span data-stu-id="6211b-206">--project</span></span> | <span data-ttu-id="6211b-207">Üzerinde çalışılacak proje dosyasının yolu.</span><span class="sxs-lookup"><span data-stu-id="6211b-207">The path to the project file to operate on.</span></span> <span data-ttu-id="6211b-208">Bir dosya belirtilmemişse, komut geçerli dizinde bir arama yapar.</span><span class="sxs-lookup"><span data-stu-id="6211b-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="6211b-209">Yenile</span><span class="sxs-lookup"><span data-stu-id="6211b-209">Refresh</span></span>

<span data-ttu-id="6211b-210">`refresh`Komut, kaynak URL 'den en son içerikle uzak bir başvuruyu güncelleştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6211b-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="6211b-211">Yalnızca indirme dosyası yolu ve kaynak URL 'SI, güncellenmek üzere olan başvuruyu belirtmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6211b-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="6211b-212">Not:</span><span class="sxs-lookup"><span data-stu-id="6211b-212">Note:</span></span>

* <span data-ttu-id="6211b-213">Dosya içeriğinin karmaları yerel dosyanın güncelleştirilip güncelleştirilmediğini belirleme ile karşılaştırılır.</span><span class="sxs-lookup"><span data-stu-id="6211b-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="6211b-214">Zaman damgası bilgisi karşılaştırılmaz.</span><span class="sxs-lookup"><span data-stu-id="6211b-214">No timestamp information is compared.</span></span>

<span data-ttu-id="6211b-215">Bir güncelleştirme gerekiyorsa araç her zaman yerel dosyayı uzak dosya ile değiştirir.</span><span class="sxs-lookup"><span data-stu-id="6211b-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="6211b-216">Kullanım</span><span class="sxs-lookup"><span data-stu-id="6211b-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="6211b-217">Arguments</span><span class="sxs-lookup"><span data-stu-id="6211b-217">Arguments</span></span>

| <span data-ttu-id="6211b-218">Bağımsız Değişken</span><span class="sxs-lookup"><span data-stu-id="6211b-218">Argument</span></span> | <span data-ttu-id="6211b-219">Açıklama</span><span class="sxs-lookup"><span data-stu-id="6211b-219">Description</span></span> |
|-|-|
| <span data-ttu-id="6211b-220">başvurular</span><span class="sxs-lookup"><span data-stu-id="6211b-220">references</span></span> | <span data-ttu-id="6211b-221">Güncellenmesi gereken uzak prototip başvurularına yönelik URL veya dosya yolları.</span><span class="sxs-lookup"><span data-stu-id="6211b-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="6211b-222">Tüm Uzak başvuruları yenilemek için bu bağımsız değişkeni boş bırakın.</span><span class="sxs-lookup"><span data-stu-id="6211b-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="6211b-223">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="6211b-223">Options</span></span>

| <span data-ttu-id="6211b-224">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="6211b-224">Short option</span></span> | <span data-ttu-id="6211b-225">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="6211b-225">Long option</span></span> | <span data-ttu-id="6211b-226">Açıklama</span><span class="sxs-lookup"><span data-stu-id="6211b-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="6211b-227">-p</span><span class="sxs-lookup"><span data-stu-id="6211b-227">-p</span></span> | <span data-ttu-id="6211b-228">--Proje</span><span class="sxs-lookup"><span data-stu-id="6211b-228">--project</span></span> | <span data-ttu-id="6211b-229">Üzerinde çalışılacak proje dosyasının yolu.</span><span class="sxs-lookup"><span data-stu-id="6211b-229">The path to the project file to operate on.</span></span> <span data-ttu-id="6211b-230">Bir dosya belirtilmemişse, komut geçerli dizinde bir arama yapar.</span><span class="sxs-lookup"><span data-stu-id="6211b-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="6211b-231">--Kuru-Çalıştır</span><span class="sxs-lookup"><span data-stu-id="6211b-231">--dry-run</span></span> | <span data-ttu-id="6211b-232">Herhangi bir yeni içerik indirilmeden güncelleştirilenecek dosyaların listesini verir.</span><span class="sxs-lookup"><span data-stu-id="6211b-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="6211b-233">Liste</span><span class="sxs-lookup"><span data-stu-id="6211b-233">List</span></span>

<span data-ttu-id="6211b-234">`list`Komut, proje dosyasındaki tüm prototipsiz başvuruları göstermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6211b-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="6211b-235">Bir sütunun tüm değerleri varsayılan değerler ise, sütun atlanabilir.</span><span class="sxs-lookup"><span data-stu-id="6211b-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="6211b-236">Kullanım</span><span class="sxs-lookup"><span data-stu-id="6211b-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="6211b-237">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="6211b-237">Options</span></span>

| <span data-ttu-id="6211b-238">Short seçeneği</span><span class="sxs-lookup"><span data-stu-id="6211b-238">Short option</span></span> | <span data-ttu-id="6211b-239">Long seçeneği</span><span class="sxs-lookup"><span data-stu-id="6211b-239">Long option</span></span> | <span data-ttu-id="6211b-240">Açıklama</span><span class="sxs-lookup"><span data-stu-id="6211b-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="6211b-241">-p</span><span class="sxs-lookup"><span data-stu-id="6211b-241">-p</span></span> | <span data-ttu-id="6211b-242">--Proje</span><span class="sxs-lookup"><span data-stu-id="6211b-242">--project</span></span> | <span data-ttu-id="6211b-243">Üzerinde çalışılacak proje dosyasının yolu.</span><span class="sxs-lookup"><span data-stu-id="6211b-243">The path to the project file to operate on.</span></span> <span data-ttu-id="6211b-244">Bir dosya belirtilmemişse, komut geçerli dizinde bir arama yapar.</span><span class="sxs-lookup"><span data-stu-id="6211b-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6211b-245">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6211b-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
