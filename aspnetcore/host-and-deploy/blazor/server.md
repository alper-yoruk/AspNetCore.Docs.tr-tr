---
title: ASP.NET Core Blazor sunucusu barındırma ve dağıtma
author: guardrex
description: ASP.NET Core kullanarak Blazor sunucu uygulamasını nasıl barındırılacağını ve dağıtacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: e69b91035c65739dde724330e83793c0b8b5481a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775160"
---
# <a name="host-and-deploy-blazor-server"></a>Sunucu barındırma ve Blazor dağıtma

, [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com)ve [Daniel Roth](https://github.com/danroth27) tarafından

## <a name="host-configuration-values"></a>Ana bilgisayar yapılandırma değerleri

Sunucu uygulamaları [genel ana bilgisayar yapılandırma değerlerini](xref:fundamentals/host/generic-host#host-configuration)kabul edebilir. [ Blazor ](xref:blazor/hosting-models#blazor-server)

## <a name="deployment"></a>Dağıtım

Sunucu barındırma modelinin kullanımı, Blazor sunucuda bir ASP.NET Core uygulamasının içinden yürütülür. [ Blazor ](xref:blazor/hosting-models#blazor-server) Kullanıcı Arabirimi güncelleştirmeleri, olay işleme ve JavaScript çağrıları bir [SignalR](xref:signalr/introduction) bağlantı üzerinden işlenir.

ASP.NET Core uygulaması barındırabilen bir Web sunucusu gerekiyor. Visual Studio, ** Blazor sunucu uygulaması** proje şablonunu (`blazorserverside` [DotNet New](/dotnet/core/tools/dotnet-new) komutu kullanılırken şablon) içerir.

## <a name="scalability"></a>Ölçeklenebilirlik

Bir Blazor sunucu uygulaması için kullanılabilir altyapıyı en iyi şekilde kullanmasını sağlamak üzere bir dağıtım planlayın. Sunucu uygulaması ölçeklenebilirliğini karşılamak Blazor için aşağıdaki kaynaklara bakın:

* [Blazor Sunucu uygulamalarının temelleri](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server/threat-mitigation>

### <a name="deployment-server"></a>Dağıtım sunucusu

Tek bir sunucunun ölçeklenebilirliğini değerlendirirken (ölçeği büyütme), bir uygulama için kullanılabilir olan bellek büyük olasılıkla uygulamanın kullanıcı talebi arttıkça arttırabileceği ilk kaynaktır. Sunucudaki kullanılabilir bellek şunları etkiler:

* Bir sunucunun destekleyebileceği etkin devre sayısı.
* İstemcide UI gecikme süresi.

Güvenli ve ölçeklenebilir Blazor sunucu uygulamaları oluşturma hakkında yönergeler için bkz <xref:security/blazor/server/threat-mitigation>..

Her bağlantı hattı en düşük *Merhaba Dünya*stilinde bir uygulama için YAKLAŞıK 250 KB bellek kullanır. Bir devrenin boyutu, uygulamanın koduna ve her bileşenle ilişkili durum bakım gereksinimlerine bağlıdır. Uygulama ve altyapınız için geliştirme sırasında kaynak taleplerini ölçmenizi öneririz, ancak aşağıdaki taban çizgisi, dağıtım hedefini planlamada bir başlangıç noktası olabilir: uygulamanızın 5.000 eşzamanlı kullanıcı desteklemesini istiyorsanız, en az 1,3 GB sunucu belleğini uygulamaya (veya Kullanıcı başına ~ 273 KB) göre bütçeleme yapmayı düşünün.

### <a name="signalr-configuration"></a>SignalRyapılandırmada

BlazorSunucu uygulamaları, tarayıcıyla SignalR iletişim kurmak için ASP.NET Core kullanır. [uygulamasının barındırma ve ölçeklendirme koşulları, sunucu uygulamaları için geçerlidir. SignalR](xref:signalr/publish-to-azure-web-app) Blazor

Blazordaha düşük gecikme süresi, güvenilirlik ve SignalR [güvenlik](xref:signalr/security)nedeniyle taşıma olarak WebSockets kullanırken en iyi şekilde işe yarar. Uzun yoklama, WebSockets kullanılamadığında SignalR veya uygulama açıkça uzun yoklamayı kullanacak şekilde yapılandırıldığında kullanılır. Azure App Service dağıtım sırasında, uygulamayı hizmetin Azure portal ayarları içinde kullanmak üzere yapılandırın. Azure App Service için uygulamayı yapılandırma hakkında ayrıntılı bilgi için bkz. [ SignalR yayımlama yönergeleri](xref:signalr/publish-to-azure-web-app).

#### <a name="azure-signalr-service"></a>Azure SignalR hizmeti

Sunucu uygulamaları için Blazor [ SignalR Azure hizmetini](/azure/azure-signalr) kullanmanızı öneririz. Hizmet, bir Blazor sunucu uygulamasının ölçeğini çok sayıda eşzamanlı SignalR bağlantıya ölçeklendirmeye olanak tanır. Ayrıca, SignalR hizmetin küresel erişim ve yüksek performanslı veri merkezleri Coğrafya nedeniyle gecikme süresini azaltmaya önemli ölçüde yardımcı olur. Azure SignalR hizmetini bir uygulamayı yapılandırmak (ve isteğe bağlı olarak sağlamak) için:

1. [Prerendering sırasında istemciler aynı sunucuya geri yönlendirildiği](xref:blazor/hosting-models#connection-to-the-server) *yapışkan oturumları*desteklemek için hizmeti etkinleştirin. `ServerStickyMode` Seçeneğini veya yapılandırma değerini olarak `Required`ayarlayın. Genellikle, bir uygulama aşağıdaki yaklaşımlardan **birini** kullanarak yapılandırmayı oluşturur:

   * `Startup.ConfigureServices`:
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * Yapılandırma (aşağıdaki yaklaşımlardan **birini** kullanın):
  
     * *appSettings. JSON*:

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * App Service 'in Azure Portal (**ad** `Azure:SignalR:ServerStickyMode`:, **değer**: `Required`) **yapılandırma** > **uygulaması ayarları** .

1. Blazor Sunucu uygulaması Için Visual Studio 'Da bir Azure Apps yayımlama profili oluşturun.
1. **Azure SignalR hizmet** bağımlılığını profile ekleyin. Azure aboneliğinin uygulamaya atanacak önceden mevcut bir Azure SignalR hizmeti örneği yoksa, yeni bir hizmet örneği sağlamak için **Yeni bir Azure SignalR hizmet örneği oluştur** ' u seçin.
1. Uygulamayı Azure’da yayımlama.

#### <a name="iis"></a>IIS

IIS kullanırken şunları etkinleştirin:

* [IIS üzerinde WebSockets](xref:fundamentals/websockets#enabling-websockets-on-iis).
* [Uygulama Isteği yönlendirme Ile yapışkan oturumlar](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

#### <a name="kubernetes"></a>Kubernetes

[Yapışkan oturumlar için aşağıdaki Kubernetes ek açıklamalarını](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)içeren bir giriş tanımı oluşturun:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a>Nginx ile Linux

WebSockets SignalR 'in düzgün çalışması için, proxy 'nin `Upgrade` ve `Connection` üst bilgilerinin aşağıdaki değerlere ayarlandığını ve `$connection_upgrade` şu değerlere eşlenmiş olduğunu doğrulayın:

* Varsayılan olarak yükseltme üst bilgisi değeri.
* `close`Yükseltme üst bilgisi eksik veya boş.

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

Daha fazla bilgi için aşağıdaki makalelere bakın:

* [WebSocket proxy 'Si olarak NGıNX](https://www.nginx.com/blog/websocket-nginx/)
* [WebSocket proxy](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

### <a name="measure-network-latency"></a>Ölçü ağı gecikmesi

Aşağıdaki örnekte gösterildiği gibi, [js birlikte çalışması](xref:blazor/call-javascript-from-dotnet) ağ gecikmesini ölçmek için kullanılabilir:

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

Makul bir kullanıcı arabirimi deneyimi için, 250ms veya daha az sayıda sürekli Kullanıcı arabirimi gecikme süresi önerilir.
