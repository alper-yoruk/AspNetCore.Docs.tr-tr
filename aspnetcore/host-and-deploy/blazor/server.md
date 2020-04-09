---
title: Core Blazor Server'ı barındırma ve dağıtma ASP.NET
author: guardrex
description: ASP.NET Core'u kullanarak Blazor bir Sunucu uygulamasını nasıl barındırıp dağıttığınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: 866bb348180c872d8ab20787283cfb7217183a8d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79025420"
---
# <a name="host-and-deploy-opno-locblazor-server"></a>Sunucu'ya Blazor ana bilgisayar ve dağıtım

Luke [Latham](https://github.com/guardrex)tarafından , [Rainer Stropek](https://www.timecockpit.com), ve [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Ana bilgisayar yapılandırma değerleri

Sunucu uygulamaları [Genel Ana Bilgisayar yapılandırma değerlerini](xref:fundamentals/host/generic-host#host-configuration)kabul edebilir. [ Blazor ](xref:blazor/hosting-models#blazor-server)

## <a name="deployment"></a>Dağıtım

Sunucu barındırma modeli Blazor kullanılarak, sunucuda bir ASP.NET Core uygulaması içinden yürütülür. [ Blazor ](xref:blazor/hosting-models#blazor-server) UI güncelleştirmeleri, olay işleme ve JavaScript [SignalR](xref:signalr/introduction) çağrıları bir bağlantı üzerinden işlenir.

ASP.NET Core uygulaması na sahip bir web sunucusu gereklidir. Visual ** Blazor Studio, Sunucu Uygulaması** `blazorserverside` proje şablonunu [(dotnet yeni](/dotnet/core/tools/dotnet-new) komutunu kullanırken şablon) içerir.

## <a name="scalability"></a>Ölçeklenebilirlik

Bir Blazor Sunucu uygulaması için kullanılabilir altyapıdan en iyi şekilde yararlanmak için bir dağıtım planlayın. Sunucu uygulaması ölçeklenebilirliğini ele Blazor almak için aşağıdaki kaynaklara bakın:

* [Sunucu uygulamalarının Blazor temelleri](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server>

### <a name="deployment-server"></a>Dağıtım sunucusu

Tek bir sunucunun ölçeklenebilirliği göz önüne alındığında (ölçeklendirin), bir uygulamanın kullanabileceği bellek, kullanıcı talepleri arttıkça uygulamanın tükeceği ilk kaynak tır. Sunucudaki kullanılabilir bellek aşağıdakileri etkiler:

* Bir sunucunun destekleyebilir etkin devrelerin sayısı.
* Müşteride ui gecikmesi.

Güvenli ve ölçeklenebilir Blazor sunucu uygulamaları oluşturma <xref:security/blazor/server>kılavuzu için bkz.

Her devre, en az *Merhaba World*tarzı uygulama için yaklaşık 250 KB bellek kullanır. Bir devrenin boyutu, uygulamanın koduna ve her bileşenle ilişkili durum bakım gereksinimlerine bağlıdır. Uygulamanız ve altyapınız için geliştirme sırasında kaynak taleplerini ölçmenizi öneririz, ancak aşağıdaki temel, dağıtım hedefinizi planlamada bir başlangıç noktası olabilir: Uygulamanızın 5.000 eşzamanlı kullanıcıyı desteklemesini bekliyorsanız, uygulamaya en az 1,3 GB sunucu belleği (veya kullanıcı başına ~273 KB) bütçeleme yapmayı düşünün.

### <a name="opno-locsignalr-configuration"></a>SignalRYapılandırma

BlazorSunucu uygulamaları tarayıcı SignalR yla iletişim kurmak için ASP.NET Core'u kullanır. Sunucu uygulamaları için Blazor [barındırma ve ölçekleme koşulları geçerlidir. SignalR](xref:signalr/publish-to-azure-web-app)

Blazordaha düşük gecikme, güvenilirlik SignalR ve [güvenlik](xref:signalr/security)nedeniyle aktarım olarak WebSockets kullanırken en iyi şekilde çalışır. Uzun Yoklama, SignalR WebSockets'in kullanılamadığı veya uygulamanın açıkça Uzun Yoklama'yı kullanacak şekilde yapılandırıldığı zaman kullanılır. Azure Uygulama Hizmeti'ne dağıtılırken, uygulamayı hizmetiçin Azure portal ayarlarında WebSockets kullanacak şekilde yapılandırın. Azure Uygulama Hizmeti için uygulamayı yapılandırma hakkında ayrıntılı bilgi için [ SignalR yayımlama yönergelerine](xref:signalr/publish-to-azure-web-app)bakın.

#### <a name="azure-opno-locsignalr-service"></a>Azure SignalR Hizmeti

Sunucu uygulamaları için Blazor [ SignalR Azure Hizmetini](/azure/azure-signalr) kullanmanızı öneririz. Bu hizmet, bir Blazor Server uygulamasını çok sayıda eşzamanlı SignalR bağlantıya ölçeklemenize olanak tanır. Buna ek SignalR olarak, hizmetin küresel erişimi ve yüksek performanslı veri merkezleri, coğrafya nedeniyle gecikme süresini azaltmaya önemli ölçüde yardımcı dır. Bir uygulamayı yapılandırmak (ve isteğe SignalR bağlı olarak sağlama) Azure Hizmeti için:

1. Hizmetin, istemcilerin [önceden işletilirken aynı sunucuya yönlendirildiği](xref:blazor/hosting-models#connection-to-the-server) *yapışkan oturumları*desteklemesini etkinleştirin. `ServerStickyMode` Seçeneği veya yapılandırma değerini `Required`' olarak ayarlayın Genellikle, bir uygulama aşağıdaki **yaklaşımlardan birini** kullanarak yapılandırmayı oluşturur:

   * `Startup.ConfigureServices`:
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * Yapılandırma (aşağıdaki **yaklaşımlardan birini** kullanın):
  
     * *appsettings.json*:

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * Uygulama hizmetinin Azure portalındaki **Yapılandırma** > **Uygulaması ayarları** ( `Required`**Ad**: , `Azure:SignalR:ServerStickyMode` **Değer**: ).

1. Blazor Sunucu uygulaması için Visual Studio'da bir Azure Apps yayımlama profili oluşturun.
1. Profile **Azure SignalR Hizmeti** bağımlılığını ekleyin. Azure aboneliğinde uygulamaya atamak için önceden SignalR varolan bir Azure Hizmeti örneği yoksa, yeni bir hizmet örneği sağlamak için **yeni bir Azure SignalR Hizmeti örneği oluştur'u** seçin.
1. Uygulamayı Azure’da yayımlama.

#### <a name="iis"></a>IIS

IIS kullanırken şunları etkinleştirin:

* [IIS üzerinde WebSockets](xref:fundamentals/websockets#enabling-websockets-on-iis).
* [Uygulama İsteği Yönlendirme ile yapışkan oturumlar.](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)

#### <a name="kubernetes"></a>Kubernetes

Yapışkan oturumlar için aşağıdaki [Kubernetes ek açıklamaları](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)ile bir giriş tanımı oluşturun:

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

SignalR WebSockets'lerin düzgün çalışması için proxy'nin `Upgrade` `Connection` ve üstbilginin aşağıdaki değerlere `$connection_upgrade` ayarladığını ve bu değerlerin hangisine eşlenediğini onaylayın:

* Yükseltme üstbilgi değeri varsayılan olarak.
* `close`Yükseltme üstbilgisi eksik veya boş olduğunda.

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

* [Bir WebSocket Proxy olarak NGINX](https://www.nginx.com/blog/websocket-nginx/)
* [WebSocket proxying](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

### <a name="measure-network-latency"></a>Ağ gecikmesi ölçme

[JS interop](xref:blazor/call-javascript-from-dotnet) aşağıdaki örnekte gösterildiği gibi, ağ gecikmesini ölçmek için kullanılabilir:

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

Makul bir UI deneyimi için, 250ms veya daha az sürekli bir UI gecikmesi öneririz.
