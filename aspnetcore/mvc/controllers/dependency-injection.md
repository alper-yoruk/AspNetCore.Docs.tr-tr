---
title: ASP.NET Core denetleyicilere bağımlılık ekleme
author: ardalis
description: ASP.NET Core MVC denetleyicilerinin bağımlılıklarını açıkça nasıl isteyeceğini, ASP.NET Core bağımlılık ekleme ile oluşturucuları aracılığıyla nasıl isteyeceğini öğrenin.
ms.author: riande
ms.date: 02/24/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: bae31e38c3b4146ec5e4b7a398a2e0fa290fd34c
ms.sourcegitcommit: 99c784a873b62fbd97a73c5c07f4fe7a7f5db638
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/27/2020
ms.locfileid: "85503545"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a>ASP.NET Core denetleyicilere bağımlılık ekleme

::: moniker range=">= aspnetcore-3.0"

By [shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT)ve [Steve Smith](https://github.com/ardalis)

ASP.NET Core MVC denetleyicileri, oluşturucular aracılığıyla bağımlılıkları doğrudan ister. ASP.NET Core, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)için yerleşik desteğe sahiptir. Dı, uygulamaların test ve bakımını daha kolay hale getirir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="constructor-injection"></a>Oluşturucu Ekleme

Hizmetler bir oluşturucu parametresi olarak eklenir ve çalışma zamanı hizmeti hizmet kapsayıcısından çözer. Hizmetler genellikle arabirimler kullanılarak tanımlanır. Örneğin, geçerli zamanı gerektiren bir uygulamayı düşünün. Aşağıdaki Arabirim hizmeti kullanıma sunar `IDateTime` :

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

Aşağıdaki kod `IDateTime` arabirimini uygular:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

Hizmeti hizmet kapsayıcısına ekleyin:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

Hakkında daha fazla bilgi için <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> bkz. [dı hizmeti yaşam süreleri](xref:fundamentals/dependency-injection#service-lifetimes).

Aşağıdaki kod, günün saatine göre kullanıcıya bir karşılama görüntüler:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

Uygulamayı çalıştırın ve zamana göre bir ileti görüntülenir.

## <a name="action-injection-with-fromservices"></a>FromServices ile eylem ekleme

, <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> Oluşturucu Ekleme kullanılmadan ekleme a hizmetini doğrudan bir eylem yöntemine sunar:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a>Denetleyiciden erişim ayarları

Bir denetleyici içinden uygulama veya yapılandırma ayarlarına erişmek ortak bir modeldir. ' Da açıklanan *Seçenekler deseninin* <xref:fundamentals/configuration/options> ayarları yönetmek için tercih edilen yaklaşım vardır. Genellikle, <xref:Microsoft.Extensions.Configuration.IConfiguration> bir denetleyiciye doğrudan eklemeyin.

Seçenekleri temsil eden bir sınıf oluşturun. Örneğin:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

Yapılandırma sınıfını hizmetler koleksiyonuna ekleyin:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

Bir JSON biçimli dosyadan ayarları okumak için uygulamayı yapılandırın:

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

Aşağıdaki kod, `IOptions<SampleWebSettings>` hizmet kapsayıcısından ayarları ister ve bu yöntemleri kullanarak kullanır `Index` :

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/controllers/testing>Denetleyicilerde açıkça bağımlılıklar isteyerek kodu daha kolay test etme hakkında bilgi edinmek için bkz..

* [Varsayılan bağımlılık ekleme kapsayıcısını üçüncü taraf bir uygulamayla değiştirin](xref:fundamentals/dependency-injection#default-service-container-replacement).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

By [shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT)ve [Steve Smith](https://github.com/ardalis)

ASP.NET Core MVC denetleyicileri, oluşturucular aracılığıyla bağımlılıkları doğrudan ister. ASP.NET Core, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)için yerleşik desteğe sahiptir. Dı, uygulamaların test ve bakımını daha kolay hale getirir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="constructor-injection"></a>Oluşturucu Ekleme

Hizmetler bir oluşturucu parametresi olarak eklenir ve çalışma zamanı hizmeti hizmet kapsayıcısından çözer. Hizmetler genellikle arabirimler kullanılarak tanımlanır. Örneğin, geçerli zamanı gerektiren bir uygulamayı düşünün. Aşağıdaki Arabirim hizmeti kullanıma sunar `IDateTime` :

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

Aşağıdaki kod `IDateTime` arabirimini uygular:

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

Hizmeti hizmet kapsayıcısına ekleyin:

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

Hakkında daha fazla bilgi için <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> bkz. [dı hizmeti yaşam süreleri](xref:fundamentals/dependency-injection#service-lifetimes).

Aşağıdaki kod, günün saatine göre kullanıcıya bir karşılama görüntüler:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

Uygulamayı çalıştırın ve zamana göre bir ileti görüntülenir.

## <a name="action-injection-with-fromservices"></a>FromServices ile eylem ekleme

, <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> Oluşturucu Ekleme kullanılmadan ekleme a hizmetini doğrudan bir eylem yöntemine sunar:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a>Denetleyiciden erişim ayarları

Bir denetleyici içinden uygulama veya yapılandırma ayarlarına erişmek ortak bir modeldir. ' Da açıklanan *Seçenekler deseninin* <xref:fundamentals/configuration/options> ayarları yönetmek için tercih edilen yaklaşım vardır. Genellikle, <xref:Microsoft.Extensions.Configuration.IConfiguration> bir denetleyiciye doğrudan eklemeyin.

Seçenekleri temsil eden bir sınıf oluşturun. Örneğin:

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

Yapılandırma sınıfını hizmetler koleksiyonuna ekleyin:

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

Bir JSON biçimli dosyadan ayarları okumak için uygulamayı yapılandırın:

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

Aşağıdaki kod, `IOptions<SampleWebSettings>` hizmet kapsayıcısından ayarları ister ve bu yöntemleri kullanarak kullanır `Index` :

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/controllers/testing>Denetleyicilerde açıkça bağımlılıklar isteyerek kodu daha kolay test etme hakkında bilgi edinmek için bkz..

* [Varsayılan bağımlılık ekleme kapsayıcısını üçüncü taraf bir uygulamayla değiştirin](xref:fundamentals/dependency-injection#default-service-container-replacement).

::: moniker-end