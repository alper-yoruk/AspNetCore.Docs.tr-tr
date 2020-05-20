---
<span data-ttu-id="b29dd-101">Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="b29dd-101">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="b29dd-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b29dd-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b29dd-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-103">'Blazor'</span></span>
- <span data-ttu-id="b29dd-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b29dd-104">'Identity'</span></span>
- <span data-ttu-id="b29dd-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b29dd-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="b29dd-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-106">'Razor'</span></span>
- <span data-ttu-id="b29dd-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="b29dd-107">'SignalR' uid:</span></span> 

---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="b29dd-108">ASP.NET Core için bağlayıcı yapılandırmaBlazor</span><span class="sxs-lookup"><span data-stu-id="b29dd-108">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="b29dd-109">[Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="b29dd-109">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor<span data-ttu-id="b29dd-110">WebAssembly, uygulamanın çıkış derlemelerinden gereksiz Il 'yi kırpmak için bir derleme sırasında [ara dil (IL)](/dotnet/standard/managed-code#intermediate-language--execution) bağlamayı gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="b29dd-110"> WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="b29dd-111">Hata ayıklama yapılandırmasında oluşturulurken bağlayıcı devre dışı bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="b29dd-111">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="b29dd-112">Bağlayıcı etkinleştirmek için uygulamaların yayın yapılandırmasında derlenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="b29dd-112">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="b29dd-113">Webassembly uygulamalarınızı dağıttığınızda yayında derleme yapmanızı öneririz Blazor .</span><span class="sxs-lookup"><span data-stu-id="b29dd-113">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="b29dd-114">Uygulama bağlama boyutu için en iyi duruma getirir, ancak bu etkilere sebep olabilir.</span><span class="sxs-lookup"><span data-stu-id="b29dd-114">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="b29dd-115">Bağlayıcı bu dinamik davranışı öğrenmediği ve çalışma zamanında yansıma için hangi türlerin gerekli olduğunu belirleyemediği için yansıma veya ilgili dinamik özellikleri kullanan uygulamalar kırpılmayabilir.</span><span class="sxs-lookup"><span data-stu-id="b29dd-115">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="b29dd-116">Bu tür uygulamaları kırpmak için bağlayıcı, koddaki yansıma tarafından gerek duyulan herhangi bir tür ve uygulamanın bağımlı olduğu paketler veya çerçeveler hakkında bilgilendirmelidir.</span><span class="sxs-lookup"><span data-stu-id="b29dd-116">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="b29dd-117">Kırpılan uygulamanın dağıtıldıktan sonra düzgün çalıştığından emin olmak için, geliştirme sırasında uygulamanın yayın derlemelerini test etmek önemlidir.</span><span class="sxs-lookup"><span data-stu-id="b29dd-117">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="b29dd-118">Uygulamalar için bağlama Blazor , bu MSBuild özellikleri kullanılarak yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="b29dd-118">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="b29dd-119">Bir [MSBuild özelliği](#control-linking-with-an-msbuild-property)ile genel olarak bağlamayı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="b29dd-119">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="b29dd-120">[Yapılandırma dosyası](#control-linking-with-a-configuration-file)ile derleme temelinde bağlama denetimi.</span><span class="sxs-lookup"><span data-stu-id="b29dd-120">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="b29dd-121">MSBuild özelliği ile bağlamayı denetleme</span><span class="sxs-lookup"><span data-stu-id="b29dd-121">Control linking with an MSBuild property</span></span>

<span data-ttu-id="b29dd-122">Bir uygulama yapılandırmada derlenmediğinde bağlama etkinleştirilir `Release` .</span><span class="sxs-lookup"><span data-stu-id="b29dd-122">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="b29dd-123">Bunu değiştirmek için, `BlazorWebAssemblyEnableLinking` Proje dosyasında MSBuild özelliğini yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="b29dd-123">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="b29dd-124">Yapılandırma dosyası ile bağlamayı denetleme</span><span class="sxs-lookup"><span data-stu-id="b29dd-124">Control linking with a configuration file</span></span>

<span data-ttu-id="b29dd-125">Bir XML yapılandırma dosyası sağlayarak ve dosyayı proje dosyasında MSBuild öğesi olarak belirterek, derleme başına temelinde bağlamayı denetleyin:</span><span class="sxs-lookup"><span data-stu-id="b29dd-125">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="b29dd-126">*Linkerconfig. xml*:</span><span class="sxs-lookup"><span data-stu-id="b29dd-126">*LinkerConfig.xml*:</span></span>

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

<span data-ttu-id="b29dd-127">Daha fazla bilgi ve örnek için bkz. [veri biçimleri (mono/bağlayıcı GitHub deposu)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span><span class="sxs-lookup"><span data-stu-id="b29dd-127">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="b29dd-128">Kitaplığa bir XML bağlayıcı yapılandırma dosyası ekleme</span><span class="sxs-lookup"><span data-stu-id="b29dd-128">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="b29dd-129">Bağlayıcıyı belirli bir kitaplık için yapılandırmak için, kitaplığa katıştırılmış kaynak olarak bir XML bağlayıcı yapılandırma dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b29dd-129">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="b29dd-130">Katıştırılmış kaynak, derlemeyle aynı ada sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b29dd-130">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="b29dd-131">Aşağıdaki örnekte, *Linkerconfig. xml* dosyası, kitaplığın derlemesi ile aynı ada sahip gömülü bir kaynak olarak belirtilir:</span><span class="sxs-lookup"><span data-stu-id="b29dd-131">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="b29dd-132">Bağlayıcıyı uluslararası duruma getirme için yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b29dd-132">Configure the linker for internationalization</span></span>

<span data-ttu-id="b29dd-133">Varsayılan olarak, Blazor Blazor webassembly uygulamaları için bağlayıcı yapılandırması, açıkça istenen yerel ayarlar dışında uluslararası duruma getirme bilgilerini şeritleri.</span><span class="sxs-lookup"><span data-stu-id="b29dd-133">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="b29dd-134">Bu derlemelerin kaldırılması uygulamanın boyutunu en aza indirir.</span><span class="sxs-lookup"><span data-stu-id="b29dd-134">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="b29dd-135">Hangi I18N derlemelerinin korunacağını denetlemek için, `<BlazorWebAssemblyI18NAssemblies>` Proje dosyasında MSBuild özelliğini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="b29dd-135">To control which I18N assemblies are retained, set the `<BlazorWebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="b29dd-136">Bölge değeri</span><span class="sxs-lookup"><span data-stu-id="b29dd-136">Region Value</span></span>     | <span data-ttu-id="b29dd-137">Mono bölgesi derlemesi</span><span class="sxs-lookup"><span data-stu-id="b29dd-137">Mono region assembly</span></span>    |
| ---
<span data-ttu-id="b29dd-138">Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="b29dd-138">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="b29dd-139">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b29dd-139">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b29dd-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-140">'Blazor'</span></span>
- <span data-ttu-id="b29dd-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b29dd-141">'Identity'</span></span>
- <span data-ttu-id="b29dd-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b29dd-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="b29dd-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-143">'Razor'</span></span>
- <span data-ttu-id="b29dd-144">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="b29dd-144">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b29dd-145">Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="b29dd-145">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="b29dd-146">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b29dd-146">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b29dd-147">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-147">'Blazor'</span></span>
- <span data-ttu-id="b29dd-148">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b29dd-148">'Identity'</span></span>
- <span data-ttu-id="b29dd-149">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b29dd-149">'Let's Encrypt'</span></span>
- <span data-ttu-id="b29dd-150">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-150">'Razor'</span></span>
- <span data-ttu-id="b29dd-151">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="b29dd-151">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b29dd-152">Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="b29dd-152">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="b29dd-153">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b29dd-153">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b29dd-154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-154">'Blazor'</span></span>
- <span data-ttu-id="b29dd-155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b29dd-155">'Identity'</span></span>
- <span data-ttu-id="b29dd-156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b29dd-156">'Let's Encrypt'</span></span>
- <span data-ttu-id="b29dd-157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-157">'Razor'</span></span>
- <span data-ttu-id="b29dd-158">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="b29dd-158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b29dd-159">Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="b29dd-159">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="b29dd-160">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b29dd-160">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b29dd-161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-161">'Blazor'</span></span>
- <span data-ttu-id="b29dd-162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b29dd-162">'Identity'</span></span>
- <span data-ttu-id="b29dd-163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b29dd-163">'Let's Encrypt'</span></span>
- <span data-ttu-id="b29dd-164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-164">'Razor'</span></span>
- <span data-ttu-id="b29dd-165">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="b29dd-165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b29dd-166">Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="b29dd-166">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="b29dd-167">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b29dd-167">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b29dd-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-168">'Blazor'</span></span>
- <span data-ttu-id="b29dd-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b29dd-169">'Identity'</span></span>
- <span data-ttu-id="b29dd-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b29dd-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="b29dd-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-171">'Razor'</span></span>
- <span data-ttu-id="b29dd-172">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="b29dd-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b29dd-173">Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="b29dd-173">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="b29dd-174">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b29dd-174">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b29dd-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-175">'Blazor'</span></span>
- <span data-ttu-id="b29dd-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b29dd-176">'Identity'</span></span>
- <span data-ttu-id="b29dd-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b29dd-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="b29dd-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-178">'Razor'</span></span>
- <span data-ttu-id="b29dd-179">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="b29dd-179">'SignalR' uid:</span></span> 

<span data-ttu-id="b29dd-180">-------- | ---title: ' ASP.NET Core için bağlayıcı yapılandırma: Blazor Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcı denetimini nasıl denetleyeceğinizi öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="b29dd-180">-------- | --- title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="b29dd-181">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b29dd-181">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b29dd-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-182">'Blazor'</span></span>
- <span data-ttu-id="b29dd-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b29dd-183">'Identity'</span></span>
- <span data-ttu-id="b29dd-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b29dd-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="b29dd-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-185">'Razor'</span></span>
- <span data-ttu-id="b29dd-186">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="b29dd-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b29dd-187">Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="b29dd-187">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="b29dd-188">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b29dd-188">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b29dd-189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-189">'Blazor'</span></span>
- <span data-ttu-id="b29dd-190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b29dd-190">'Identity'</span></span>
- <span data-ttu-id="b29dd-191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b29dd-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="b29dd-192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-192">'Razor'</span></span>
- <span data-ttu-id="b29dd-193">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="b29dd-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b29dd-194">Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="b29dd-194">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="b29dd-195">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b29dd-195">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b29dd-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-196">'Blazor'</span></span>
- <span data-ttu-id="b29dd-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b29dd-197">'Identity'</span></span>
- <span data-ttu-id="b29dd-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b29dd-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="b29dd-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-199">'Razor'</span></span>
- <span data-ttu-id="b29dd-200">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="b29dd-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b29dd-201">Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="b29dd-201">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="b29dd-202">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b29dd-202">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b29dd-203">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-203">'Blazor'</span></span>
- <span data-ttu-id="b29dd-204">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b29dd-204">'Identity'</span></span>
- <span data-ttu-id="b29dd-205">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b29dd-205">'Let's Encrypt'</span></span>
- <span data-ttu-id="b29dd-206">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-206">'Razor'</span></span>
- <span data-ttu-id="b29dd-207">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="b29dd-207">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b29dd-208">Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="b29dd-208">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="b29dd-209">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b29dd-209">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b29dd-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-210">'Blazor'</span></span>
- <span data-ttu-id="b29dd-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b29dd-211">'Identity'</span></span>
- <span data-ttu-id="b29dd-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b29dd-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="b29dd-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-213">'Razor'</span></span>
- <span data-ttu-id="b29dd-214">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="b29dd-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b29dd-215">Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="b29dd-215">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="b29dd-216">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b29dd-216">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b29dd-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-217">'Blazor'</span></span>
- <span data-ttu-id="b29dd-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b29dd-218">'Identity'</span></span>
- <span data-ttu-id="b29dd-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b29dd-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="b29dd-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-220">'Razor'</span></span>
- <span data-ttu-id="b29dd-221">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="b29dd-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b29dd-222">Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="b29dd-222">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="b29dd-223">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b29dd-223">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b29dd-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-224">'Blazor'</span></span>
- <span data-ttu-id="b29dd-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b29dd-225">'Identity'</span></span>
- <span data-ttu-id="b29dd-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b29dd-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="b29dd-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-227">'Razor'</span></span>
- <span data-ttu-id="b29dd-228">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="b29dd-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b29dd-229">Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="b29dd-229">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="b29dd-230">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b29dd-230">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b29dd-231">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-231">'Blazor'</span></span>
- <span data-ttu-id="b29dd-232">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b29dd-232">'Identity'</span></span>
- <span data-ttu-id="b29dd-233">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b29dd-233">'Let's Encrypt'</span></span>
- <span data-ttu-id="b29dd-234">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-234">'Razor'</span></span>
- <span data-ttu-id="b29dd-235">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="b29dd-235">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b29dd-236">Başlık: ' ASP.NET Core için bağlayıcıyı yapılandırın Blazor ' Yazar: Açıklama: ' bir uygulama oluştururken ara dil (IL) bağlayıcının nasıl kontrol Leyeceğinizi öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="b29dd-236">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="b29dd-237">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b29dd-237">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b29dd-238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-238">'Blazor'</span></span>
- <span data-ttu-id="b29dd-239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b29dd-239">'Identity'</span></span>
- <span data-ttu-id="b29dd-240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b29dd-240">'Let's Encrypt'</span></span>
- <span data-ttu-id="b29dd-241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b29dd-241">'Razor'</span></span>
- <span data-ttu-id="b29dd-242">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="b29dd-242">'SignalR' uid:</span></span> 

<span data-ttu-id="b29dd-243">------------ | | `all`            | Tüm derlemeler dahil | | `cjk`             |  *I18N. ÇJK. dll* | | `mideast`         |  *I18N. MIDEAST. dll* | | `none`(varsayılan) | Hiçbiri | | `other`           |  *I18N. Diğer. dll* | | `rare`            |  *I18N. Nadir. dll* | | `west`            |  *I18N. Batı. dll*         |</span><span class="sxs-lookup"><span data-stu-id="b29dd-243">------------ | | `all`            | All assemblies included | | `cjk`            | *I18N.CJK.dll*          | | `mideast`        | *I18N.MidEast.dll*      | | `none` (default) | None                    | | `other`          | *I18N.Other.dll*        | | `rare`           | *I18N.Rare.dll*         | | `west`           | *I18N.West.dll*         |</span></span>

<span data-ttu-id="b29dd-244">Birden çok değeri ayırmak için virgül kullanın (örneğin, `mideast,west` ).</span><span class="sxs-lookup"><span data-stu-id="b29dd-244">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="b29dd-245">Daha fazla bilgi için bkz. [I18N: Pnetlib uluslararası duruma getirme çerçeve kitaplığı (Mono/Mono GitHub deposu)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="b29dd-245">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b29dd-246">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b29dd-246">Additional resources</span></span>

* <xref:performance/blazor/webassembly-best-practices#intermediate-language-il-linking>
