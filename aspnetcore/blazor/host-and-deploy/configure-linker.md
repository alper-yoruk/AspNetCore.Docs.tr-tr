---
title: ASP.NET Core için bağlayıcı yapılandırma Blazor
author: guardrex
description: Uygulama oluştururken ara dil (IL) bağlayıcı denetimini nasıl denetleyeceğinizi öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1 <= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
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
uid: blazor/host-and-deploy/configure-linker
ms.openlocfilehash: f6b0412540f33aac7b0b35c7fc64ab110e68a340
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080296"
---
# <a name="configure-the-linker-for-aspnet-core-no-locblazor"></a><span data-ttu-id="7970d-103">ASP.NET Core için bağlayıcı yapılandırma Blazor</span><span class="sxs-lookup"><span data-stu-id="7970d-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="7970d-104">[Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="7970d-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7970d-105">Blazor WebAssembly uygulamanın çıkış derlemelerinden gereksiz Il 'yi kırpmak için bir derleme sırasında [ara dil (IL)](/dotnet/standard/managed-code#intermediate-language--execution) bağlamayı gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="7970d-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="7970d-106">Hata ayıklama yapılandırmasında oluşturulurken bağlayıcı devre dışı bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="7970d-106">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="7970d-107">Bağlayıcı etkinleştirmek için uygulamaların yayın yapılandırmasında derlenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="7970d-107">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="7970d-108">Uygulamalarınızı dağıttığınızda yayında derleme yapmanızı öneririz Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="7970d-108">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="7970d-109">Uygulama bağlama boyutu için en iyi duruma getirir, ancak bu etkilere sebep olabilir.</span><span class="sxs-lookup"><span data-stu-id="7970d-109">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="7970d-110">Bağlayıcı bu dinamik davranışı öğrenmediği ve çalışma zamanında yansıma için hangi türlerin gerekli olduğunu belirleyemediği için yansıma veya ilgili dinamik özellikleri kullanan uygulamalar kırpılmayabilir.</span><span class="sxs-lookup"><span data-stu-id="7970d-110">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="7970d-111">Bu tür uygulamaları kırpmak için bağlayıcı, koddaki yansıma tarafından gerek duyulan herhangi bir tür ve uygulamanın bağımlı olduğu paketler veya çerçeveler hakkında bilgilendirmelidir.</span><span class="sxs-lookup"><span data-stu-id="7970d-111">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span>

<span data-ttu-id="7970d-112">Kırpılan uygulamanın dağıtıldıktan sonra düzgün çalıştığından emin olmak için, geliştirme sırasında uygulamanın yayın derlemelerini test etmek önemlidir.</span><span class="sxs-lookup"><span data-stu-id="7970d-112">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="7970d-113">Uygulamalar için bağlama Blazor , bu MSBuild özellikleri kullanılarak yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="7970d-113">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="7970d-114">Bir [MSBuild özelliği](#control-linking-with-an-msbuild-property)ile genel olarak bağlamayı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="7970d-114">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="7970d-115">[Yapılandırma dosyası](#control-linking-with-a-configuration-file)ile derleme temelinde bağlama denetimi.</span><span class="sxs-lookup"><span data-stu-id="7970d-115">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="7970d-116">MSBuild özelliği ile bağlamayı denetleme</span><span class="sxs-lookup"><span data-stu-id="7970d-116">Control linking with an MSBuild property</span></span>

<span data-ttu-id="7970d-117">Bir uygulama yapılandırmada derlenmediğinde bağlama etkinleştirilir `Release` .</span><span class="sxs-lookup"><span data-stu-id="7970d-117">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="7970d-118">Bunu değiştirmek için, `BlazorWebAssemblyEnableLinking` Proje dosyasında MSBuild özelliğini yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="7970d-118">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="7970d-119">Yapılandırma dosyası ile bağlamayı denetleme</span><span class="sxs-lookup"><span data-stu-id="7970d-119">Control linking with a configuration file</span></span>

<span data-ttu-id="7970d-120">Bir XML yapılandırma dosyası sağlayarak ve dosyayı proje dosyasında MSBuild öğesi olarak belirterek, derleme başına temelinde bağlamayı denetleyin:</span><span class="sxs-lookup"><span data-stu-id="7970d-120">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="7970d-121">`LinkerConfig.xml`:</span><span class="sxs-lookup"><span data-stu-id="7970d-121">`LinkerConfig.xml`:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

<span data-ttu-id="7970d-122">Daha fazla bilgi ve örnek için bkz. [veri biçimleri (mono/bağlayıcı GitHub deposu)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span><span class="sxs-lookup"><span data-stu-id="7970d-122">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="7970d-123">Kitaplığa bir XML bağlayıcı yapılandırma dosyası ekleme</span><span class="sxs-lookup"><span data-stu-id="7970d-123">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="7970d-124">Bağlayıcıyı belirli bir kitaplık için yapılandırmak için, kitaplığa katıştırılmış kaynak olarak bir XML bağlayıcı yapılandırma dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7970d-124">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="7970d-125">Katıştırılmış kaynak, derlemeyle aynı ada sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7970d-125">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="7970d-126">Aşağıdaki örnekte, `LinkerConfig.xml` Dosya, kitaplığın derlemesi ile aynı ada sahip gömülü bir kaynak olarak belirtilir:</span><span class="sxs-lookup"><span data-stu-id="7970d-126">In the following example, the `LinkerConfig.xml` file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="7970d-127">Bağlayıcıyı uluslararası duruma getirme için yapılandırma</span><span class="sxs-lookup"><span data-stu-id="7970d-127">Configure the linker for internationalization</span></span>

<span data-ttu-id="7970d-128">Varsayılan olarak, Blazor uygulamalar için bağlayıcı yapılandırması, Blazor WebAssembly açıkça istenen yerel ayarlar dışında uluslararası duruma getirme bilgilerini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="7970d-128">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="7970d-129">Bu derlemelerin kaldırılması uygulamanın boyutunu en aza indirir.</span><span class="sxs-lookup"><span data-stu-id="7970d-129">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="7970d-130">Hangi I18N derlemelerinin korunacağını denetlemek için, `<BlazorWebAssemblyI18NAssemblies>` Proje dosyasında MSBuild özelliğini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="7970d-130">To control which I18N assemblies are retained, set the `<BlazorWebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="7970d-131">Bölge değeri</span><span class="sxs-lookup"><span data-stu-id="7970d-131">Region Value</span></span>     | <span data-ttu-id="7970d-132">Mono bölgesi derlemesi</span><span class="sxs-lookup"><span data-stu-id="7970d-132">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="7970d-133">Tüm derlemeler dahil</span><span class="sxs-lookup"><span data-stu-id="7970d-133">All assemblies included</span></span> |
| `cjk`            | `I18N.CJK.dll`          |
| `mideast`        | `I18N.MidEast.dll`      |
| <span data-ttu-id="7970d-134">`none` varsayılanını</span><span class="sxs-lookup"><span data-stu-id="7970d-134">`none` (default)</span></span> | <span data-ttu-id="7970d-135">Yok</span><span class="sxs-lookup"><span data-stu-id="7970d-135">None</span></span>                    |
| `other`          | `I18N.Other.dll`        |
| `rare`           | `I18N.Rare.dll`         |
| `west`           | `I18N.West.dll`         |

<span data-ttu-id="7970d-136">Birden çok değeri ayırmak için virgül kullanın (örneğin, `mideast,west` ).</span><span class="sxs-lookup"><span data-stu-id="7970d-136">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="7970d-137">Daha fazla bilgi için bkz. [I18N: Pnetlib uluslararası duruma getirme çerçeve kitaplığı (Mono/Mono GitHub deposu)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="7970d-137">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7970d-138">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7970d-138">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-linking>
