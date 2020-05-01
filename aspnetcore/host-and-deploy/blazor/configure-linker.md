---
title: ASP.NET Core için bağlayıcı yapılandırmaBlazor
author: guardrex
description: Blazor Uygulama oluştururken ara DIL (IL) bağlayıcı denetimini nasıl denetleyeceğinizi öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/29/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: 94cf1f09ddff47aa41181e9f5c52b4c65dc2ecf1
ms.sourcegitcommit: 6318d2bdd63116e178c34492a904be85ec9ac108
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82604785"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a>ASP.NET Core Blazor için bağlayıcı yapılandırma

[Luke Latham](https://github.com/guardrex) tarafından

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor WebAssembly, uygulamanın çıkış derlemelerinden gereksiz Il 'yi kırpmak için bir derleme sırasında [ara dil (IL)](/dotnet/standard/managed-code#intermediate-language--execution) bağlamayı gerçekleştirir. Hata ayıklama yapılandırmasında oluşturulurken bağlayıcı devre dışı bırakıldı. Bağlayıcı etkinleştirmek için uygulamaların yayın yapılandırmasında derlenmesi gerekir. Blazor WebAssembly uygulamalarınızı dağıttığınızda yayında derleme yapmanız önerilir. 

Uygulama bağlama boyutu için en iyi duruma getirir, ancak bu etkilere sebep olabilir. Bağlayıcı bu dinamik davranışı öğrenmediği ve çalışma zamanında yansıma için hangi türlerin gerekli olduğunu belirleyemediği için yansıma veya ilgili dinamik özellikleri kullanan uygulamalar kırpılmayabilir. Bu tür uygulamaları kırpmak için bağlayıcı, koddaki yansıma tarafından gerek duyulan herhangi bir tür ve uygulamanın bağımlı olduğu paketler veya çerçeveler hakkında bilgilendirmelidir. 

Kırpılan uygulamanın dağıtıldıktan sonra düzgün çalıştığından emin olmak için, geliştirme sırasında uygulamanın yayın derlemelerini test etmek önemlidir.

Blazor uygulamaları için bağlama, bu MSBuild özellikleri kullanılarak yapılandırılabilir:

* Bir [MSBuild özelliği](#control-linking-with-an-msbuild-property)ile genel olarak bağlamayı yapılandırın.
* [Yapılandırma dosyası](#control-linking-with-a-configuration-file)ile derleme temelinde bağlama denetimi.

## <a name="control-linking-with-an-msbuild-property"></a>MSBuild özelliği ile bağlamayı denetleme

Bir uygulama `Release` yapılandırmada derlenmediğinde bağlama etkinleştirilir. Bunu değiştirmek için, proje dosyasında `BlazorWebAssemblyEnableLinking` MSBuild özelliğini yapılandırın:

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>Yapılandırma dosyası ile bağlamayı denetleme

Bir XML yapılandırma dosyası sağlayarak ve dosyayı proje dosyasında MSBuild öğesi olarak belirterek, derleme başına temelinde bağlamayı denetleyin:

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

*Linkerconfig. xml*:

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

Daha fazla bilgi için bkz. [XML dosyası örneklerini bağlama (mono/bağlayıcı GitHub deposu)](https://github.com/mono/linker#link-xml-file-examples).

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a>Kitaplığa bir XML bağlayıcı yapılandırma dosyası ekleme

Bağlayıcıyı belirli bir kitaplık için yapılandırmak için, kitaplığa katıştırılmış kaynak olarak bir XML bağlayıcı yapılandırma dosyası ekleyin. Katıştırılmış kaynak, derlemeyle aynı ada sahip olmalıdır.

Aşağıdaki örnekte, *Linkerconfig. xml* dosyası, kitaplığın derlemesi ile aynı ada sahip gömülü bir kaynak olarak belirtilir:

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a>Bağlayıcıyı uluslararası duruma getirme için yapılandırma

Varsayılan olarak, Blazor 'in Blazor WebAssembly uygulamaları için bağlayıcı yapılandırması, açıkça istenen yerel ayarlar dışında uluslararası duruma getirme bilgilerini kaldırır. Bu derlemelerin kaldırılması uygulamanın boyutunu en aza indirir.

Hangi I18N derlemelerinin korunacağını denetlemek için, proje dosyasında `<BlazorWebAssemblyI18NAssemblies>` MSBuild özelliğini ayarlayın:

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| Bölge değeri     | Mono bölgesi derlemesi    |
| ---------------- | ----------------------- |
| `all`            | Tüm derlemeler dahil |
| `cjk`            | *I18N.CJK.dll*          |
| `mideast`        | *I18N.MidEast.dll*      |
| `none`varsayılanını | Hiçbiri                    |
| `other`          | *I18N.Other.dll*        |
| `rare`           | *I18N.Rare.dll*         |
| `west`           | *I18N.West.dll*         |

Birden çok değeri ayırmak için virgül kullanın (örneğin, `mideast,west`).

Daha fazla bilgi için bkz. [I18N: Pnetlib uluslararası duruma getirme çerçeve kitaplığı (Mono/Mono GitHub deposu)](https://github.com/mono/mono/tree/master/mcs/class/I18N).
