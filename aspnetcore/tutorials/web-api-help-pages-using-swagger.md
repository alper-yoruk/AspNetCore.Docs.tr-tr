---
title: Swagger/Openapı ile Web API Yardım sayfaları ASP.NET Core
author: RicoSuter
description: Bu öğretici, bir Web API uygulaması için belge ve yardım sayfaları oluşturmak üzere Swagger ekleme hakkında bir yol sağlar.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/07/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: 815581bbee3169f04f1da67227f6fa8c7275071b
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408818"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a>Swagger/Openapı ile Web API Yardım sayfaları ASP.NET Core

Sağlayan- [Christoph Nienaber](https://twitter.com/zuckerthoben) ve [Riko Suter](https://blog.rsuter.com/)

Bir Web API 'sini kullanırken, çeşitli yöntemlerini anlamak bir geliştirici için zor olabilir. [Openapı](https://www.openapis.org/)olarak da bilinen [Swagger](https://swagger.io/), Web API 'leri için faydalı belge ve yardım sayfaları oluşturma sorununu çözer. Etkileşimli belgeler, istemci SDK 'Sı oluşturma ve API bulunabilirliği gibi avantajlar sağlar.

Bu makalede, [swashbuckle. AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) ve [nswag](https://github.com/RicoSuter/NSwag) .net Swagger uygulamaları gösterilir:

* **Swashbuckle. AspNetCore** , ASP.NET Core Web API 'Leri için Swagger belgelerini oluşturmaya yönelik açık kaynak bir projem.

* **Nswag** , Swagger belgelerini oluşturmaya ve [Swagger Kullanıcı arabirimini](https://swagger.io/swagger-ui/) veya [yeniden belgeyi](https://github.com/Rebilly/ReDoc) ASP.NET Core Web API 'lerine tümleştirmede başka bir açık kaynak projem. Ayrıca, NSwag API 'niz için C# ve TypeScript istemci kodu oluşturma yaklaşımları sunmaktadır.

## <a name="what-is-swagger--openapi"></a>Swagger/Openapı nedir?

Swagger, [rest](https://en.wikipedia.org/wiki/Representational_state_transfer) API 'leri açıklamak için dilden bağımsız bir belirtimdir. Swagger projesi, openapı [Initiative](https://www.openapis.org/)'e bağlılmıştı ve burada openapı olarak adlandırılmıştır. Her iki ad de birbirinin yerine kullanılır; Ancak, Openapı tercih edilir. Hem bilgisayarların hem de insanların uygulamaya doğrudan erişim (kaynak kodu, ağ erişimi, belgeler) olmadan bir hizmetin yeteneklerini anlamasına imkan sağlar. Bir hedef, bağlantı ilişkilendirmesi yapılan hizmetleri bağlamak için gereken iş miktarını en aza indirmektir. Başka bir amaç, bir hizmeti doğru bir şekilde belgelemek için gereken süre miktarını azaltmaktır.

## <a name="swagger-specification-swaggerjson"></a>Swagger belirtimi (swagger.json)

Swagger akışının çekirdeği, &mdash; Varsayılan olarak, *üzerindeswagger.js*adlı bir belge olan Swagger belirtimidir. Bu, hizmetinize bağlı olarak Swagger araç zinciri (veya bunun üçüncü taraf uygulamaları) tarafından oluşturulmuştur. API 'nizin yeteneklerini ve HTTP ile nasıl erişebileceğinizi açıklar. Swagger Kullanıcı arabirimini yürütür ve araç zinciri tarafından keşif ve istemci kodu oluşturmayı etkinleştirmek için kullanılır. Aşağıda, breçekimi için azaltılmış bir Swagger belirtimi örneği verilmiştir:

```json
{
   "swagger": "2.0",
   "info": {
       "version": "v1",
       "title": "API V1"
   },
   "basePath": "/",
   "paths": {
       "/api/Todo": {
           "get": {
               "tags": [
                   "Todo"
               ],
               "operationId": "ApiTodoGet",
               "consumes": [],
               "produces": [
                   "text/plain",
                   "application/json",
                   "text/json"
               ],
               "responses": {
                   "200": {
                       "description": "Success",
                       "schema": {
                           "type": "array",
                           "items": {
                               "$ref": "#/definitions/TodoItem"
                           }
                       }
                   }
                }
           },
           "post": {
               ...
           }
       },
       "/api/Todo/{id}": {
           "get": {
               ...
           },
           "put": {
               ...
           },
           "delete": {
               ...
   },
   "definitions": {
       "TodoItem": {
           "type": "object",
            "properties": {
                "id": {
                    "format": "int64",
                    "type": "integer"
                },
                "name": {
                    "type": "string"
                },
                "isComplete": {
                    "default": false,
                    "type": "boolean"
                }
            }
       }
   },
   "securityDefinitions": {}
}
```

## <a name="swagger-ui"></a>Swagger Kullanıcı arabirimi

[Swagger Kullanıcı arabirimi](https://swagger.io/swagger-ui/) , oluşturulan Swagger belirtimini kullanarak hizmet hakkında bilgi sağlayan Web tabanlı bir kullanıcı arabirimi sunar. Hem swashbuckle hem de NSwag, Swagger Kullanıcı arabirimi 'nin ekli bir sürümünü içerir, böylece bir ara yazılım kayıt çağrısı kullanarak ASP.NET Core uygulamanızda barındırılabilmesini sağlayabilirsiniz. Web Kullanıcı arabirimi şöyle görünür:

![Swagger Kullanıcı arabirimi](web-api-help-pages-using-swagger/_static/swagger-ui.png)

Denetleyicilerinizdeki her genel eylem yöntemi kullanıcı arabiriminden test edilebilir. Bölümü genişletmek için bir yöntem adına tıklayın. Gerekli parametreleri ekleyin ve **deneyin!**' e tıklayın.

![Örnek Swagger GET testi](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> Ekran görüntüleri için kullanılan Swagger Kullanıcı arabirimi sürümü sürüm 2 ' dir. Sürüm 3 örneği için bkz. [petstore örneği](https://petstore.swagger.io/).

## <a name="next-steps"></a>Sonraki adımlar

* [Swashbuckle kullanmaya başlama](xref:tutorials/get-started-with-swashbuckle)
* [NSwag kullanmaya başlama](xref:tutorials/get-started-with-nswag)
