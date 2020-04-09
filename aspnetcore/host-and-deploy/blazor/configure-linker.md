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
# <a name="configure-the-linker-for-aspnet-core-blazor"></a>ASP.NET Core Blazor için Bağlayıcıyı Yapılandırın

Yazar: [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor WebAssembly, uygulamanın çıktı derlemelerinden gereksiz IL'yi kırpmak için bir yapı sırasında [Ara Dil (IL)](/dotnet/standard/managed-code#intermediate-language--execution) bağlantı gerçekleştirir. Hata Ayıklama yapılandırmasında bina bağlanırken bağlayıcı devre dışı bırakılır. Bağlantıyı etkinleştirmek için uygulamaların Sürüm yapılandırmasında oluşturması gerekir. Blazor WebAssembly uygulamalarınızı dağıtırken Sürüm'de oluşturmanızı öneririz. 

Bir uygulamayı bağlamak boyut için en iyi duruma getirin, ancak zararlı etkileri olabilir. Bağlantı cılız bu dinamik davranışı bilmediği nden ve genel olarak çalışma zamanında yansıma için hangi türlerin gerekli olduğunu belirleyemediğinden, yansıma veya ilgili dinamik özellikleri kullanan uygulamalar kırıldığında kırılabilir. Bu tür uygulamaları kırpmak için, bağlayıcının kodda ve uygulamanın bağlı olduğu paketlerde veya çerçevelerde yansıması nın gerektirdiği türler hakkında bilgilendirilmelidir. 

Kırpılan uygulamanın dağıtıldıktan sonra doğru çalıştığından emin olmak için, geliştirme sırasında uygulamanın Sürüm yapılarını sık sık test etmek önemlidir.

Blazor uygulamaları için bağlantı şu MSBuild özellikleri kullanılarak yapılandırılabilir:

* Bir [MSBuild özelliği](#control-linking-with-an-msbuild-property)ile küresel bağlantı yapılandırma.
* [Yapılandırma dosyasıyla](#control-linking-with-a-configuration-file)montaj başına bağlamayı denetleme.

## <a name="control-linking-with-an-msbuild-property"></a>MSBuild özelliğiyle bağlantı kurma denetimi

Bir uygulama yapılandırmada `Release` yerleşik olduğunda bağlantı etkinleştirilir. Bunu değiştirmek için, `BlazorWebAssemblyEnableLinking` proje dosyasındaki MSBuild özelliğini yapılandırın:

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>Yapılandırma dosyasıyla bağlanmayı denetleme

XML yapılandırma dosyası sağlayarak ve dosyayı proje dosyasında MSBuild öğesi olarak belirterek montaj başına bağlamayı denetleyin:

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

*LinkerConfig.xml*:

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

Daha fazla bilgi için bkz: [Link xml dosya örnekleri (mono/linker GitHub deposu)](https://github.com/mono/linker#link-xml-file-examples).

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a>Kitaplık için XML bağlayıcı yapılandırma dosyası ekleme

Bağlayıcıyı belirli bir kitaplık için yapılandırmak için, katıştırılmış kaynak olarak kitaplığa bir XML bağlayıcı yapılandırma dosyası ekleyin. Katıştırılmış kaynak derlemeyle aynı ada sahip olmalıdır.

Aşağıdaki örnekte, *LinkerConfig.xml* dosyası, kitaplığın derlemesi ile aynı ada sahip katıştırılmış bir kaynak olarak belirtilir:

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a>Uluslararasılaştırma için bağlayıcıyı yapılandırma

Varsayılan olarak, Blazor'un Blazor WebAssembly uygulamaları için bağlayıcı yapılandırması, açıkça talep edilen yerel durumlar dışında uluslararasılaştırma bilgilerini siler. Bu derlemeleri kaldırmak uygulamanın boyutunu en aza indirir.

Hangi I18N derlemelerinin tutulduğunu denetlemek `<MonoLinkerI18NAssemblies>` için, MSBuild özelliğini proje dosyasında ayarlayın:

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| Bölge Değeri     | Mono bölge montajı    |
| ---------------- | ----------------------- |
| `all`            | Tüm derlemeler dahil |
| `cjk`            | *I18N.CJK.dll*          |
| `mideast`        | *I18N.MidEast.dll*      |
| `none`(varsayılan) | None                    |
| `other`          | *I18N.Other.dll*        |
| `rare`           | *I18N.Rare.dll*         |
| `west`           | *I18N.West.dll*         |

Birden çok değeri ayırmak için virgül `mideast,west`kullanın (örneğin, ).

Daha fazla bilgi için [Bkz. I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub deposu)](https://github.com/mono/mono/tree/master/mcs/class/I18N).
