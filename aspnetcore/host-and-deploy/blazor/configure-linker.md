---
title: ASP.NET Çekirdek için Bağlayıcıyı YapılandırınBlazor
author: guardrex
description: Bir Blazor uygulama kurarken Ara Dil (IL) Bağlantı cının nasıl kontrol ediliş olduğunu öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: 109da5ef400c3b9d64ccf3ceb33a5387ea6b5618
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218667"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="00f5c-103">ASP.NET Core Blazor için Bağlayıcıyı Yapılandırın</span><span class="sxs-lookup"><span data-stu-id="00f5c-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="00f5c-104">Yazar: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="00f5c-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="00f5c-105">Blazor WebAssembly, uygulamanın çıktı derlemelerinden gereksiz IL'yi kırpmak için bir yapı sırasında [Ara Dil (IL)](/dotnet/standard/managed-code#intermediate-language--execution) bağlantı gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="00f5c-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="00f5c-106">Hata Ayıklama yapılandırmasında bina bağlanırken bağlayıcı devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="00f5c-106">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="00f5c-107">Bağlantıyı etkinleştirmek için uygulamaların Sürüm yapılandırmasında oluşturması gerekir.</span><span class="sxs-lookup"><span data-stu-id="00f5c-107">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="00f5c-108">Blazor WebAssembly uygulamalarınızı dağıtırken Sürüm'de oluşturmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="00f5c-108">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="00f5c-109">Bir uygulamayı bağlamak boyut için en iyi duruma getirin, ancak zararlı etkileri olabilir.</span><span class="sxs-lookup"><span data-stu-id="00f5c-109">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="00f5c-110">Bağlantı cılız bu dinamik davranışı bilmediği nden ve genel olarak çalışma zamanında yansıma için hangi türlerin gerekli olduğunu belirleyemediğinden, yansıma veya ilgili dinamik özellikleri kullanan uygulamalar kırıldığında kırılabilir.</span><span class="sxs-lookup"><span data-stu-id="00f5c-110">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="00f5c-111">Bu tür uygulamaları kırpmak için, bağlayıcının kodda ve uygulamanın bağlı olduğu paketlerde veya çerçevelerde yansıması nın gerektirdiği türler hakkında bilgilendirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="00f5c-111">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="00f5c-112">Kırpılan uygulamanın dağıtıldıktan sonra doğru çalıştığından emin olmak için, geliştirme sırasında uygulamanın Sürüm yapılarını sık sık test etmek önemlidir.</span><span class="sxs-lookup"><span data-stu-id="00f5c-112">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="00f5c-113">Blazor uygulamaları için bağlantı şu MSBuild özellikleri kullanılarak yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="00f5c-113">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="00f5c-114">Bir [MSBuild özelliği](#control-linking-with-an-msbuild-property)ile küresel bağlantı yapılandırma.</span><span class="sxs-lookup"><span data-stu-id="00f5c-114">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="00f5c-115">[Yapılandırma dosyasıyla](#control-linking-with-a-configuration-file)montaj başına bağlamayı denetleme.</span><span class="sxs-lookup"><span data-stu-id="00f5c-115">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="00f5c-116">MSBuild özelliğiyle bağlantı kurma denetimi</span><span class="sxs-lookup"><span data-stu-id="00f5c-116">Control linking with an MSBuild property</span></span>

<span data-ttu-id="00f5c-117">Bir uygulama yapılandırmada `Release` yerleşik olduğunda bağlantı etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="00f5c-117">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="00f5c-118">Bunu değiştirmek için, `BlazorWebAssemblyEnableLinking` proje dosyasındaki MSBuild özelliğini yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="00f5c-118">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="00f5c-119">Yapılandırma dosyasıyla bağlanmayı denetleme</span><span class="sxs-lookup"><span data-stu-id="00f5c-119">Control linking with a configuration file</span></span>

<span data-ttu-id="00f5c-120">XML yapılandırma dosyası sağlayarak ve dosyayı proje dosyasında MSBuild öğesi olarak belirterek montaj başına bağlamayı denetleyin:</span><span class="sxs-lookup"><span data-stu-id="00f5c-120">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="00f5c-121">*LinkerConfig.xml*:</span><span class="sxs-lookup"><span data-stu-id="00f5c-121">*LinkerConfig.xml*:</span></span>

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

<span data-ttu-id="00f5c-122">Daha fazla bilgi için bkz: [Link xml dosya örnekleri (mono/linker GitHub deposu)](https://github.com/mono/linker#link-xml-file-examples).</span><span class="sxs-lookup"><span data-stu-id="00f5c-122">For more information, see [Link xml file examples (mono/linker GitHub repository)](https://github.com/mono/linker#link-xml-file-examples).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="00f5c-123">Kitaplık için XML bağlayıcı yapılandırma dosyası ekleme</span><span class="sxs-lookup"><span data-stu-id="00f5c-123">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="00f5c-124">Bağlayıcıyı belirli bir kitaplık için yapılandırmak için, katıştırılmış kaynak olarak kitaplığa bir XML bağlayıcı yapılandırma dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="00f5c-124">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="00f5c-125">Katıştırılmış kaynak derlemeyle aynı ada sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="00f5c-125">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="00f5c-126">Aşağıdaki örnekte, *LinkerConfig.xml* dosyası, kitaplığın derlemesi ile aynı ada sahip katıştırılmış bir kaynak olarak belirtilir:</span><span class="sxs-lookup"><span data-stu-id="00f5c-126">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="00f5c-127">Uluslararasılaştırma için bağlayıcıyı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="00f5c-127">Configure the linker for internationalization</span></span>

<span data-ttu-id="00f5c-128">Varsayılan olarak, Blazor'un Blazor WebAssembly uygulamaları için bağlayıcı yapılandırması, açıkça talep edilen yerel durumlar dışında uluslararasılaştırma bilgilerini siler.</span><span class="sxs-lookup"><span data-stu-id="00f5c-128">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="00f5c-129">Bu derlemeleri kaldırmak uygulamanın boyutunu en aza indirir.</span><span class="sxs-lookup"><span data-stu-id="00f5c-129">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="00f5c-130">Hangi I18N derlemelerinin tutulduğunu denetlemek `<MonoLinkerI18NAssemblies>` için, MSBuild özelliğini proje dosyasında ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="00f5c-130">To control which I18N assemblies are retained, set the `<MonoLinkerI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="00f5c-131">Bölge Değeri</span><span class="sxs-lookup"><span data-stu-id="00f5c-131">Region Value</span></span>     | <span data-ttu-id="00f5c-132">Mono bölge montajı</span><span class="sxs-lookup"><span data-stu-id="00f5c-132">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="00f5c-133">Tüm derlemeler dahil</span><span class="sxs-lookup"><span data-stu-id="00f5c-133">All assemblies included</span></span> |
| `cjk`            | <span data-ttu-id="00f5c-134">*I18N.CJK.dll*</span><span class="sxs-lookup"><span data-stu-id="00f5c-134">*I18N.CJK.dll*</span></span>          |
| `mideast`        | <span data-ttu-id="00f5c-135">*I18N.MidEast.dll*</span><span class="sxs-lookup"><span data-stu-id="00f5c-135">*I18N.MidEast.dll*</span></span>      |
| <span data-ttu-id="00f5c-136">`none`(varsayılan)</span><span class="sxs-lookup"><span data-stu-id="00f5c-136">`none` (default)</span></span> | <span data-ttu-id="00f5c-137">None</span><span class="sxs-lookup"><span data-stu-id="00f5c-137">None</span></span>                    |
| `other`          | <span data-ttu-id="00f5c-138">*I18N.Other.dll*</span><span class="sxs-lookup"><span data-stu-id="00f5c-138">*I18N.Other.dll*</span></span>        |
| `rare`           | <span data-ttu-id="00f5c-139">*I18N.Rare.dll*</span><span class="sxs-lookup"><span data-stu-id="00f5c-139">*I18N.Rare.dll*</span></span>         |
| `west`           | <span data-ttu-id="00f5c-140">*I18N.West.dll*</span><span class="sxs-lookup"><span data-stu-id="00f5c-140">*I18N.West.dll*</span></span>         |

<span data-ttu-id="00f5c-141">Birden çok değeri ayırmak için virgül `mideast,west`kullanın (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="00f5c-141">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="00f5c-142">Daha fazla bilgi için [Bkz. I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub deposu)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="00f5c-142">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>
