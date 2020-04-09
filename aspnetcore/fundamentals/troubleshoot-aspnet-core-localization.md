---
title: Sorun Giderme ASP.NET Çekirdek Yerelleştirme
author: hishamco
description: ASP.NET Core uygulamalarında yerelleştirmeyle ilgili sorunları nasıl tanılayarak tanılamayı öğrenin.
ms.author: riande
ms.date: 01/24/2019
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 229e274a22e170d984a16d3b1ee64ebc38c4ef77
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660379"
---
# <a name="troubleshoot-aspnet-core-localization"></a><span data-ttu-id="37cd2-103">Sorun Giderme ASP.NET Çekirdek Yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="37cd2-103">Troubleshoot ASP.NET Core Localization</span></span>

<span data-ttu-id="37cd2-104">Yazar: [Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="37cd2-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="37cd2-105">Bu makalede, ASP.NET Core uygulaması yerelleştirme sorunları nın nasıl tanılanacak hakkında yönergeler verilmektedir.</span><span class="sxs-lookup"><span data-stu-id="37cd2-105">This article provides instructions on how to diagnose ASP.NET Core app localization issues.</span></span>

## <a name="localization-configuration-issues"></a><span data-ttu-id="37cd2-106">Yerelleştirme yapılandırma sorunları</span><span class="sxs-lookup"><span data-stu-id="37cd2-106">Localization configuration issues</span></span>

<span data-ttu-id="37cd2-107">**Yerelleştirme ara yazılım sırası**</span><span class="sxs-lookup"><span data-stu-id="37cd2-107">**Localization middleware order**</span></span>  
<span data-ttu-id="37cd2-108">Yerelleştirme ara ware beklendiği gibi sipariş değil, çünkü uygulama yerelleştiremeyebilir.</span><span class="sxs-lookup"><span data-stu-id="37cd2-108">The app may not localize because the localization middleware isn't ordered as expected.</span></span>

<span data-ttu-id="37cd2-109">Bu sorunu gidermek için, yerelleştirme ara ware MVC ara önce kayıtlı olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="37cd2-109">To resolve this issue, ensure that localization middleware is registered before MVC middleware.</span></span> <span data-ttu-id="37cd2-110">Aksi takdirde, yerelleştirme ara ware uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="37cd2-110">Otherwise, the localization middleware isn't applied.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

<span data-ttu-id="37cd2-111">**Yerelleştirme kaynakları yolu bulunamadı**</span><span class="sxs-lookup"><span data-stu-id="37cd2-111">**Localization resources path not found**</span></span>

<span data-ttu-id="37cd2-112">**RequestCultureProvider'da Desteklenen Kültürler Bir kez kayıtlı ile eşleşmiyor**</span><span class="sxs-lookup"><span data-stu-id="37cd2-112">**Supported Cultures in RequestCultureProvider don't match with registered once**</span></span>  

## <a name="resource-file-naming-issues"></a><span data-ttu-id="37cd2-113">Kaynak dosyası adlandırma sorunları</span><span class="sxs-lookup"><span data-stu-id="37cd2-113">Resource file naming issues</span></span>

<span data-ttu-id="37cd2-114">ASP.NET Core [burada](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)ayrıntılı olarak açıklanan yerelleştirme kaynakları dosya adlandırma için önceden tanımlanmış kurallar ve yönergeler vardır.</span><span class="sxs-lookup"><span data-stu-id="37cd2-114">ASP.NET Core has predefined rules and guidelines for localization resources file naming, which are described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span></span>

## <a name="missing-resources"></a><span data-ttu-id="37cd2-115">Eksik kaynaklar</span><span class="sxs-lookup"><span data-stu-id="37cd2-115">Missing resources</span></span>

<span data-ttu-id="37cd2-116">Kaynakların bulunamaz yaygın nedenleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="37cd2-116">Common causes of resources not being found include:</span></span>

- <span data-ttu-id="37cd2-117">`resx` Kaynak adları dosyada veya yerelleştirici istekte yanlış yazılır.</span><span class="sxs-lookup"><span data-stu-id="37cd2-117">Resource names are misspelled in either the `resx` file or the localizer request.</span></span>
- <span data-ttu-id="37cd2-118">Kaynak bazı diller `resx` için eksik, ancak diğerlerinde var.</span><span class="sxs-lookup"><span data-stu-id="37cd2-118">The resource is missing from the `resx` for some languages, but exists in others.</span></span>
- <span data-ttu-id="37cd2-119">Sorun olmaya devam ediyorsanız, eksik kaynaklar hakkında daha fazla `Debug` bilgi için yerelleştirme günlük iletilerini (günlük düzeyinde) denetleyin.</span><span class="sxs-lookup"><span data-stu-id="37cd2-119">If you're still having trouble, check the localization log messages (which are at `Debug` log level) for more details about the missing resources.</span></span>

<span data-ttu-id="37cd2-120">_**İpucu:** Kullanırken, `CookieRequestCultureProvider`tek tırnak yerelleştirme çerez değeri içinde kültürleri ile kullanılmaz doğrulayın. Örneğin, `c='en-UK'|uic='en-US'` geçersiz bir çerez değeri, `c=en-UK|uic=en-US` geçerli bir ise._</span><span class="sxs-lookup"><span data-stu-id="37cd2-120">_**Hint:** When using `CookieRequestCultureProvider`, verify single quotes are not used with the cultures inside the localization cookie value. For example, `c='en-UK'|uic='en-US'` is an invalid cookie value, while `c=en-UK|uic=en-US` is a valid._</span></span>

## <a name="resources--class-libraries-issues"></a><span data-ttu-id="37cd2-121">Sınıflar & Kitaplıklar sorunları</span><span class="sxs-lookup"><span data-stu-id="37cd2-121">Resources & Class Libraries issues</span></span>

<span data-ttu-id="37cd2-122">ASP.NET Core varsayılan olarak sınıf kitaplıklarının [Kaynak KonumAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1)üzerinden kaynak dosyalarını bulmasına izin vermek için bir yol sağlar.</span><span class="sxs-lookup"><span data-stu-id="37cd2-122">ASP.NET Core by default provides a way to allow the class libraries to find their resource files via [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span></span>

<span data-ttu-id="37cd2-123">Sınıf kitaplıklarıyla ilgili sık karşılaşılan sorunlar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="37cd2-123">Common issues with class libraries include:</span></span>
- <span data-ttu-id="37cd2-124">Sınıf `ResourceLocationAttribute` kitaplığında eksik `ResourceManagerStringLocalizerFactory` olan kaynak bulunmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="37cd2-124">Missing the `ResourceLocationAttribute` in a class library will prevent `ResourceManagerStringLocalizerFactory` from discovering the resources.</span></span>
- <span data-ttu-id="37cd2-125">Kaynak dosyası adlandırma.</span><span class="sxs-lookup"><span data-stu-id="37cd2-125">Resource file naming.</span></span> <span data-ttu-id="37cd2-126">Daha fazla bilgi için [Kaynak dosyası adlandırma sorunları](#resource-file-naming-issues) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="37cd2-126">For more information, see [Resource file naming issues](#resource-file-naming-issues) section.</span></span>
- <span data-ttu-id="37cd2-127">Sınıf kitaplığınkök ad alanını değiştirme.</span><span class="sxs-lookup"><span data-stu-id="37cd2-127">Changing the root namespace of the class library.</span></span> <span data-ttu-id="37cd2-128">Daha fazla bilgi için [Kök Ad Alanı sorunları](#root-namespace-issues) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="37cd2-128">For more information, see [Root Namespace issues](#root-namespace-issues) section.</span></span>

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a><span data-ttu-id="37cd2-129">CustomRequestCultureProvider beklendiği gibi çalışmıyor</span><span class="sxs-lookup"><span data-stu-id="37cd2-129">CustomRequestCultureProvider doesn't work as expected</span></span>

<span data-ttu-id="37cd2-130">Sınıfın `RequestLocalizationOptions` üç varsayılan sağlayıcısı vardır:</span><span class="sxs-lookup"><span data-stu-id="37cd2-130">The `RequestLocalizationOptions` class has three default providers:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="37cd2-131">[CustomRequestCultureProvider,](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) uygulamanızda yerelleştirme kültürünün nasıl sağlandığını özelleştirmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="37cd2-131">The [CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) allows you to customize how the localization culture is provided in your app.</span></span> <span data-ttu-id="37cd2-132">Varsayılan `CustomRequestCultureProvider` sağlayıcılar gereksinimlerinizi karşılamadığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="37cd2-132">The `CustomRequestCultureProvider` is used when the default providers don't meet your requirements.</span></span>

- <span data-ttu-id="37cd2-133">Özel sağlayıcının düzgün çalışmamasının yaygın bir nedeni, `RequestCultureProviders` listedeki ilk sağlayıcı olmamasıdır.</span><span class="sxs-lookup"><span data-stu-id="37cd2-133">A common reason custom provider don't work properly is that it isn't the first provider in the `RequestCultureProviders` list.</span></span> <span data-ttu-id="37cd2-134">Bu sorunu çözmek için:</span><span class="sxs-lookup"><span data-stu-id="37cd2-134">To resolve this issue:</span></span>

- <span data-ttu-id="37cd2-135">Özel sağlayıcıyı listedeki 0 konumuna `RequestCultureProviders` aşağıdaki gibi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="37cd2-135">Insert the custom provider at the position 0 in the `RequestCultureProviders` list as the following:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

- <span data-ttu-id="37cd2-136">Özel `AddInitialRequestCultureProvider` sağlayıcıyı başlangıç sağlayıcısı olarak ayarlamak için uzantı yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="37cd2-136">Use `AddInitialRequestCultureProvider` extension method to set the custom provider as initial provider.</span></span>

## <a name="root-namespace-issues"></a><span data-ttu-id="37cd2-137">Kök Ad Alanı sorunları</span><span class="sxs-lookup"><span data-stu-id="37cd2-137">Root Namespace issues</span></span>

<span data-ttu-id="37cd2-138">Bir derlemenin kök ad alanı derleme adından farklı olduğunda, yerelleştirme varsayılan olarak çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="37cd2-138">When the root namespace of an assembly is different than the assembly name, localization doesn't work by default.</span></span> <span data-ttu-id="37cd2-139">Bu sorunu önlemek için [burada](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming) ayrıntılı olarak açıklanan [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1)kullanın</span><span class="sxs-lookup"><span data-stu-id="37cd2-139">To avoid this issue use [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), which is described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)</span></span>

> [!WARNING]
> <span data-ttu-id="37cd2-140">Bu, bir projenin adı geçerli bir .NET tanımlayıcısı olmadığında oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="37cd2-140">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="37cd2-141">Örneğin, `my-project-name.csproj` kök ad alanını `my_project_name` ve bu `my-project-name` hataya yol açan derleme adını kullanır.</span><span class="sxs-lookup"><span data-stu-id="37cd2-141">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

## <a name="resources--build-action"></a><span data-ttu-id="37cd2-142">Kaynak & Oluşturma Eylemi</span><span class="sxs-lookup"><span data-stu-id="37cd2-142">Resources & Build Action</span></span>

<span data-ttu-id="37cd2-143">Yerelleştirme için kaynak dosyaları kullanıyorsanız, uygun bir yapı eylemi olması önemlidir.</span><span class="sxs-lookup"><span data-stu-id="37cd2-143">If you use resource files for localization, it's important that they have an appropriate build action.</span></span> <span data-ttu-id="37cd2-144">Bunlar **Gömülü Kaynak**olmalıdır, `ResourceStringLocalizer` aksi takdirde bu kaynakları bulmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="37cd2-144">They should be **Embedded Resource**, otherwise the `ResourceStringLocalizer` is not able to find these resources.</span></span>
