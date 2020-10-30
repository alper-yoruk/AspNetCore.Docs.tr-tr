---
title: 'ASP.NET Core için bağlayıcı yapılandırma :::no-loc(Blazor):::'
author: guardrex
description: 'Uygulama oluştururken ara dil (IL) bağlayıcı denetimini nasıl denetleyeceğinizi öğrenin :::no-loc(Blazor)::: .'
monikerRange: '>= aspnetcore-3.1 < aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/host-and-deploy/configure-linker
ms.openlocfilehash: 0c99056053356133e901d6cf468fec8034dfb845
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055834"
---
# <a name="configure-the-linker-for-aspnet-core-no-locblazor"></a><span data-ttu-id="c3b53-103">ASP.NET Core için bağlayıcı yapılandırma :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="c3b53-103">Configure the Linker for ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="c3b53-104">[Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="c3b53-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c3b53-105">:::no-loc(Blazor WebAssembly)::: uygulamanın çıkış derlemelerinden gereksiz Il 'yi kırpmak için bir derleme sırasında [ara dil (IL)](/dotnet/standard/managed-code#intermediate-language--execution) bağlamayı gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="c3b53-105">:::no-loc(Blazor WebAssembly)::: performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="c3b53-106">Hata ayıklama yapılandırmasında oluşturulurken bağlayıcı devre dışı bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="c3b53-106">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="c3b53-107">Bağlayıcı etkinleştirmek için uygulamaların yayın yapılandırmasında derlenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="c3b53-107">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="c3b53-108">Uygulamalarınızı dağıttığınızda yayında derleme yapmanızı öneririz :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="c3b53-108">We recommend building in Release when deploying your :::no-loc(Blazor WebAssembly)::: apps.</span></span> 

<span data-ttu-id="c3b53-109">Uygulama bağlama boyutu için en iyi duruma getirir, ancak bu etkilere sebep olabilir.</span><span class="sxs-lookup"><span data-stu-id="c3b53-109">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="c3b53-110">Bağlayıcı bu dinamik davranışı öğrenmediği ve çalışma zamanında yansıma için hangi türlerin gerekli olduğunu belirleyemediği için yansıma veya ilgili dinamik özellikleri kullanan uygulamalar kırpılmayabilir.</span><span class="sxs-lookup"><span data-stu-id="c3b53-110">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="c3b53-111">Bu tür uygulamaları kırpmak için bağlayıcı, koddaki yansıma tarafından gerek duyulan herhangi bir tür ve uygulamanın bağımlı olduğu paketler veya çerçeveler hakkında bilgilendirmelidir.</span><span class="sxs-lookup"><span data-stu-id="c3b53-111">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span>

<span data-ttu-id="c3b53-112">Kırpılan uygulamanın dağıtıldıktan sonra düzgün çalıştığından emin olmak için, geliştirme sırasında uygulamanın yayın derlemelerini test etmek önemlidir.</span><span class="sxs-lookup"><span data-stu-id="c3b53-112">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="c3b53-113">Uygulamalar için bağlama :::no-loc(Blazor)::: , bu MSBuild özellikleri kullanılarak yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="c3b53-113">Linking for :::no-loc(Blazor)::: apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="c3b53-114">Bir [MSBuild özelliği](#control-linking-with-an-msbuild-property)ile genel olarak bağlamayı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c3b53-114">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="c3b53-115">[Yapılandırma dosyası](#control-linking-with-a-configuration-file)ile derleme temelinde bağlama denetimi.</span><span class="sxs-lookup"><span data-stu-id="c3b53-115">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="c3b53-116">MSBuild özelliği ile bağlamayı denetleme</span><span class="sxs-lookup"><span data-stu-id="c3b53-116">Control linking with an MSBuild property</span></span>

<span data-ttu-id="c3b53-117">Bir uygulama yapılandırmada derlenmediğinde bağlama etkinleştirilir `Release` .</span><span class="sxs-lookup"><span data-stu-id="c3b53-117">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="c3b53-118">Bunu değiştirmek için, `:::no-loc(Blazor):::WebAssemblyEnableLinking` Proje dosyasında MSBuild özelliğini yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="c3b53-118">To change this, configure the `:::no-loc(Blazor):::WebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <:::no-loc(Blazor):::WebAssemblyEnableLinking>false</:::no-loc(Blazor):::WebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="c3b53-119">Yapılandırma dosyası ile bağlamayı denetleme</span><span class="sxs-lookup"><span data-stu-id="c3b53-119">Control linking with a configuration file</span></span>

<span data-ttu-id="c3b53-120">Bir XML yapılandırma dosyası sağlayarak ve dosyayı proje dosyasında MSBuild öğesi olarak belirterek, derleme başına temelinde bağlamayı denetleyin:</span><span class="sxs-lookup"><span data-stu-id="c3b53-120">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <:::no-loc(Blazor):::LinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="c3b53-121">`LinkerConfig.xml`:</span><span class="sxs-lookup"><span data-stu-id="c3b53-121">`LinkerConfig.xml`:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or :::no-loc(Blazor)::: packages must not be
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
  <assembly fullname="MyCool:::no-loc(Blazor):::App" />
</linker>
```

<span data-ttu-id="c3b53-122">Daha fazla bilgi ve örnek için bkz. [veri biçimleri (mono/bağlayıcı GitHub deposu)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span><span class="sxs-lookup"><span data-stu-id="c3b53-122">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="c3b53-123">Kitaplığa bir XML bağlayıcı yapılandırma dosyası ekleme</span><span class="sxs-lookup"><span data-stu-id="c3b53-123">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="c3b53-124">Bağlayıcıyı belirli bir kitaplık için yapılandırmak için, kitaplığa katıştırılmış kaynak olarak bir XML bağlayıcı yapılandırma dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c3b53-124">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="c3b53-125">Katıştırılmış kaynak, derlemeyle aynı ada sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c3b53-125">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="c3b53-126">Aşağıdaki örnekte, `LinkerConfig.xml` Dosya, kitaplığın derlemesi ile aynı ada sahip gömülü bir kaynak olarak belirtilir:</span><span class="sxs-lookup"><span data-stu-id="c3b53-126">In the following example, the `LinkerConfig.xml` file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="c3b53-127">Bağlayıcıyı uluslararası duruma getirme için yapılandırma</span><span class="sxs-lookup"><span data-stu-id="c3b53-127">Configure the linker for internationalization</span></span>

<span data-ttu-id="c3b53-128">Varsayılan olarak, :::no-loc(Blazor)::: uygulamalar için bağlayıcı yapılandırması, :::no-loc(Blazor WebAssembly)::: açıkça istenen yerel ayarlar dışında uluslararası duruma getirme bilgilerini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="c3b53-128">By default, :::no-loc(Blazor):::'s linker configuration for :::no-loc(Blazor WebAssembly)::: apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="c3b53-129">Bu derlemelerin kaldırılması uygulamanın boyutunu en aza indirir.</span><span class="sxs-lookup"><span data-stu-id="c3b53-129">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="c3b53-130">Hangi I18N derlemelerinin korunacağını denetlemek için, `<:::no-loc(Blazor):::WebAssemblyI18NAssemblies>` Proje dosyasında MSBuild özelliğini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="c3b53-130">To control which I18N assemblies are retained, set the `<:::no-loc(Blazor):::WebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <:::no-loc(Blazor):::WebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</:::no-loc(Blazor):::WebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="c3b53-131">Bölge değeri</span><span class="sxs-lookup"><span data-stu-id="c3b53-131">Region Value</span></span>     | <span data-ttu-id="c3b53-132">Mono bölgesi derlemesi</span><span class="sxs-lookup"><span data-stu-id="c3b53-132">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="c3b53-133">Tüm derlemeler dahil</span><span class="sxs-lookup"><span data-stu-id="c3b53-133">All assemblies included</span></span> |
| `cjk`            | `I18N.CJK.dll`          |
| `mideast`        | `I18N.MidEast.dll`      |
| <span data-ttu-id="c3b53-134">`none` varsayılanını</span><span class="sxs-lookup"><span data-stu-id="c3b53-134">`none` (default)</span></span> | <span data-ttu-id="c3b53-135">Yok</span><span class="sxs-lookup"><span data-stu-id="c3b53-135">None</span></span>                    |
| `other`          | `I18N.Other.dll`        |
| `rare`           | `I18N.Rare.dll`         |
| `west`           | `I18N.West.dll`         |

<span data-ttu-id="c3b53-136">Birden çok değeri ayırmak için virgül kullanın (örneğin, `mideast,west` ).</span><span class="sxs-lookup"><span data-stu-id="c3b53-136">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="c3b53-137">Daha fazla bilgi için bkz. [I18N: Pnetlib uluslararası duruma getirme çerçeve kitaplığı (Mono/Mono GitHub deposu)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="c3b53-137">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c3b53-138">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c3b53-138">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-linking>
