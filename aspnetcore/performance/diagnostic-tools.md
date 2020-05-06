---
title: Performans Tanılama araçları
author: mjrousos
description: ASP.NET Core uygulamalarında performans sorunlarını tanılamaya yönelik faydalı araçlar.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/diagnostic-tools
ms.openlocfilehash: 82c724ec647dfe5547db775ebaf8c2479bb258bd
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775862"
---
# <a name="performance-diagnostic-tools"></a>Performans Tanılama Araçları

, [Mike Rousos](https://github.com/mjrousos) tarafından

Bu makalede ASP.NET Core performans sorunlarını tanılamaya yönelik araçlar listelenmektedir.

## <a name="visual-studio-diagnostic-tools"></a>Visual Studio Tanılama Araçları

Visual Studio 'da yerleşik olarak bulunan [profil oluşturma ve tanılama araçları](/visualstudio/profiling) , performans sorunlarını araştırmaya başlamak için iyi bir yerdir. Bu araçlar, Visual Studio geliştirme ortamından güçlü ve kullanımı kolay bir yöntemdir. Araç, ASP.NET Core uygulamalardaki CPU kullanımı, bellek kullanımı ve performans olaylarının analizine izin verir. Yerleşik olarak, geliştirme sırasında profil oluşturma kolaylaşır.

[Visual Studio belgelerinde](/visualstudio/profiling/profiling-overview)daha fazla bilgi bulabilirsiniz.

## <a name="application-insights"></a>Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) , uygulamanız için ayrıntılı performans verileri sağlar. Application Insights, yanıt oranları, hata oranları, bağımlılık yanıt süreleri ve daha fazlası için otomatik olarak veri toplar. Application Insights, uygulamanıza özel olayları ve ölçümleri günlüğe kaydetmeyi destekler.

Azure Application Insights, izlenen uygulamalar hakkında öngörü sağlamak için birden çok yol sağlar:

- [Uygulama eşlemesi](/azure/application-insights/app-insights-app-map) – Dağıtılmış uygulamaların tüm bileşenlerinde performans sorunlarını ve hata durumunda etkin noktaları belirlemesine yardımcı olur.
- [Azure Ölçüm Gezgini](/azure/azure-monitor/platform/metrics-getting-started) , grafikleri çizdirme, eğilimleri görsel olarak ilişkilendirme ve ölçüm değerlerinde ani artışları ve DIP 'leri araştırmanıza olanak tanıyan bir Microsoft Azure Portal bileşenidir.
- [Application Insights portalındaki performans dikey](/azure/application-insights/app-insights-tutorial-performance)penceresi:

  - İzlenen uygulamadaki farklı işlemlere yönelik performans ayrıntılarını gösterir.
  - Uzun bir süreye katkıda bulunan tüm parçaları/bağımlılıkları denetlemek için tek bir işleme detaya gitmeyi sağlar.
  - Profil Oluşturucu, isteğe bağlı performans izlemeleri toplamak için Buradan çağrılabilir.

- [Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) , .NET uygulamalarının düzenli ve isteğe bağlı olarak profil oluşturulmasına olanak sağlar.  Azure portal, çağrı yığınları ve etkin yollarla yakalanan performans izlerini gösterir. İzleme dosyaları PerfView kullanılarak daha derin analizler için de indirilebilir.

Application Insights, çeşitli ortamlarda kullanılabilir:

- Azure 'da çalışmak için iyileştirildi.
- Üretim, geliştirme ve hazırlama için geçerlidir.
- [Visual Studio](/azure/application-insights/app-insights-visual-studio) 'dan veya diğer barındırma ortamlarında yerel olarak çalışmaktadır.

Daha fazla bilgi için bkz. [ASP.NET Core Application Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="perfview"></a>PerfView

[PerfView](https://github.com/Microsoft/perfview) , .NET ekibi tarafından .net performans sorunlarını tanılamak için özel olarak oluşturulan bir performans analizi aracıdır. PerfView CPU kullanımı, bellek ve GC davranışı, performans olayları ve duvar saati saatinin analizine izin verir.

PerfView hakkında daha fazla bilgi edinmek ve [PerfView video öğreticileri](https://channel9.msdn.com/Series/PerfView-Tutorial) ile çalışmaya başlama ya da araç veya [GitHub üzerinde](https://github.com/Microsoft/perfview)bulunan kullanıcı kılavuzunu okuma hakkında daha fazla bilgi edinebilirsiniz.

## <a name="windows-performance-toolkit"></a>Windows performans araç seti

[Windows performans araç seti](/windows-hardware/test/wpt/) (WPT) iki bileşenden oluşur: Windows performans Kaydedicisi (WPR) ve Windows performans ÇÖZÜMLEYICISI (WPA). Araçlar, Windows işletim sistemlerinin ve uygulamalarının ayrıntılı performans profillerini üretir. WPT, verileri görselleştirmenin daha zengin yollarına sahiptir, ancak veri toplama işlemi PerfView 'dan daha az güçlüdür.

## <a name="perfcollect"></a>PerfCollect

PerfView, .NET senaryoları için faydalı bir performans Analizi Aracı olsa da, yalnızca Windows üzerinde çalışır, bu sayede Linux ortamlarında çalışan ASP.NET Core uygulamalardan izleme toplamak için kullanamazsınız.

[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) , PerfView tarafından çözümlenebilecek Linux üzerinde izlemeler toplamak Için yerel Linux profil oluşturma araçları 'Nı ([perf](https://perf.wiki.kernel.org/index.php/Main_Page) ve [lttng](https://lttng.org/)) kullanan bir bash betiğdir. PerfCollect, PerfView 'in doğrudan kullanılabileceği Linux ortamlarında görüntülendiğinde yararlı olur. Bunun yerine, PerfCollect .NET Core uygulamalarından daha sonra PerfView kullanılarak bir Windows bilgisayarında çözümlenen izlemeler toplayabilir.

PerfCollect 'i yüklemek ve kullanmaya başlamak hakkında daha fazla bilgiyi [GitHub ' da](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md)bulabilirsiniz.

## <a name="other-third-party-performance-tools"></a>Diğer üçüncü taraf performans araçları

Aşağıda, .NET Core uygulamalarının performans araştırmasına faydalı olan bazı üçüncü taraf performans araçları listelenmektedir.

- [Mini profil Oluşturucu](https://miniprofiler.com/)
- JetBrains 'den dotTrace ve dotMemory
- Intel 'ten sanal ayar
