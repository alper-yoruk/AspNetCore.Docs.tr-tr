---
title: ASP.NET Core denetleyicilere bağımlılık ekleme
author: ardalis
description: ASP.NET Core MVC denetleyicilerinin bağımlılıklarını açıkça nasıl isteyeceğini, ASP.NET Core bağımlılık ekleme ile oluşturucuları aracılığıyla nasıl isteyeceğini öğrenin.
ms.author: riande
ms.date: 02/24/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: a7df6a5fa2d49efc332c4684ea8192f143cdebf5
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775706"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a>ASP.NET Core denetleyicilere bağımlılık ekleme

<a name="dependency-injection-controllers"></a>

By [shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT)ve [Steve Smith](https://github.com/ardalis)

ASP.NET Core MVC denetleyicileri, oluşturucular aracılığıyla bağımlılıkları doğrudan ister. ASP.NET Core, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)için yerleşik desteğe sahiptir. Dı, uygulamaların test ve bakımını daha kolay hale getirir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="constructor-injection"></a>Oluşturucu Ekleme

Hizmetler bir oluşturucu parametresi olarak eklenir ve çalışma zamanı hizmeti hizmet kapsayıcısından çözer. Hizmetler genellikle arabirimler kullanılarak tanımlanır. Örneğin, geçerli zamanı gerektiren bir uygulamayı düşünün. Aşağıdaki arabirim `IDateTime` hizmeti kullanıma sunar:

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

Aşağıdaki kod `IDateTime` arabirimini uygular:

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

Hizmeti hizmet kapsayıcısına ekleyin:

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

Hakkında <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>daha fazla bilgi için bkz. [dı hizmeti yaşam süreleri](xref:fundamentals/dependency-injection#service-lifetimes).

Aşağıdaki kod, günün saatine göre kullanıcıya bir karşılama görüntüler:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

Uygulamayı çalıştırın ve zamana göre bir ileti görüntülenir.

## <a name="action-injection-with-fromservices"></a>FromServices ile eylem ekleme

, <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> Oluşturucu Ekleme kullanılmadan ekleme a hizmetini doğrudan bir eylem yöntemine sunar:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a>Denetleyiciden erişim ayarları

Bir denetleyici içinden uygulama veya yapılandırma ayarlarına erişmek ortak bir modeldir. ' <xref:fundamentals/configuration/options> Da açıklanan *Seçenekler deseninin* ayarları yönetmek için tercih edilen yaklaşım vardır. Genellikle, bir denetleyiciye doğrudan <xref:Microsoft.Extensions.Configuration.IConfiguration> eklemeyin.

Seçenekleri temsil eden bir sınıf oluşturun. Örneğin:

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

Yapılandırma sınıfını hizmetler koleksiyonuna ekleyin:

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

Bir JSON biçimli dosyadan ayarları okumak için uygulamayı yapılandırın:

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

Aşağıdaki kod, hizmet kapsayıcısından `IOptions<SampleWebSettings>` ayarları ister ve bu `Index` yöntemleri kullanarak kullanır:

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a>Ek kaynaklar

* Denetleyicilerde açıkça bağımlılıklar isteyerek kodu daha kolay test etme hakkında bilgi edinmek için bkz <xref:mvc/controllers/testing> ..

* [Varsayılan bağımlılık ekleme kapsayıcısını üçüncü taraf bir uygulamayla değiştirin](xref:fundamentals/dependency-injection#default-service-container-replacement).
