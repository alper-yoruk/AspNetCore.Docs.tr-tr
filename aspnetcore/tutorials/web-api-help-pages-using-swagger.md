---
title: ASP.NET Core Web API yardım sayfaları Swagger / OpenAPI ile
author: RicoSuter
description: Bu öğretici, bir Web API uygulaması için belge ve yardım sayfaları oluşturmak için Swagger ekleme nin bir walkthrough sağlar.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/07/2019
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: 4408e02996b958bf009903aa1e4eeda9ad4f457c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658475"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a>ASP.NET Core web API yardım sayfaları Swagger / OpenAPI ile

Yazar: [Christoph Nienaber](https://twitter.com/zuckerthoben) ve [Rico Suter](https://blog.rsuter.com/)

Bir Web API'sini tüketirken, çeşitli yöntemlerini anlamak bir geliştirici için zor olabilir. [OpenAPI](https://www.openapis.org/)olarak da bilinen [Swagger,](https://swagger.io/)Web API'leri için yararlı belgeler ve yardım sayfaları oluşturma sorununu çözer. Etkileşimli dokümantasyon, istemci SDK oluşturma ve API keşfedilebilirlik gibi avantajlar sağlar.

Bu makalede, [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) ve [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger uygulamaları sergilenir:

* **Swashbuckle.AspNetCore** ASP.NET Core Web API'ları için Swagger belgeleri oluşturmak için bir açık kaynak projesidir.

* **NSwag** Swagger belgeleri oluşturmak ve Core web API'ASP.NET [Swagger UI](https://swagger.io/swagger-ui/) veya [ReDoc](https://github.com/Rebilly/ReDoc) entegre için başka bir açık kaynak projesidir. Ayrıca, NSwag API'nız için C# ve TypeScript istemci kodu oluşturmak için yaklaşımlar sunar.

## <a name="what-is-swagger--openapi"></a>Swagger / OpenAPI nedir?

Swagger, [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) API'lerini tanımlamak için bir dil-agnostik belirtimidir. Swagger projesi [OpenAPI Girişimi'ne](https://www.openapis.org/)bağışlandı ve burada openapi olarak anılıyor. Her iki ad birbirinin yerine kullanılır; ancak OpenAPI tercih edilir. Hem bilgisayarların hem de insanların bir hizmetin özelliklerini uygulamaya (kaynak kodu, ağ erişimi, dokümantasyon) doğrudan erişim olmadan anlamalarını sağlar. Amaçlardan biri, bağlantısı kesilen hizmetleri bağlamak için gereken iş miktarını en aza indirmektir. Başka bir amaç, bir hizmeti doğru bir şekilde belgelemek için gereken süreyi azaltmaktır.

## <a name="swagger-specification-swaggerjson"></a>Swagger belirtimi (swagger.json)

Swagger akışının çekirdeği varsayılan olarak Swagger belirtimidir,&mdash; *swagger.json*adlı bir belgedir. Hizmetinize dayalı olarak Swagger takım zinciri (veya üçüncü taraf uygulamaları) tarafından oluşturulur. API'nizin yeteneklerini ve HTTP ile nasıl erişilenleri açıklar. Swagger UI'yi kullanır ve bulma ve istemci kodu oluşturmayı etkinleştirmek için takım zinciri tarafından kullanılır. Kısaltma için azaltılmış bir Swagger belirtimi örneği aşağıda verilmiştir:

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

## <a name="swagger-ui"></a>Swagger UI

[Swagger Kullanıcı İyi UI, oluşturulan Swagger](https://swagger.io/swagger-ui/) belirtimini kullanarak hizmet hakkında bilgi sağlayan web tabanlı bir kullanıcı arası hizmeti sunar. Hem Swashbuckle hem de NSwag, bir ara yazılım kayıt çağrısı kullanarak ASP.NET Core uygulamanızda barındırılabilmek için Swagger UI'nin gömülü bir sürümünü içerir. Web UI şuna benzer:

![Swagger UI](web-api-help-pages-using-swagger/_static/swagger-ui.png)

Denetleyicilerinizdeki her genel eylem yöntemi UI'den test edilebilir. Bölümü genişletmek için bir yöntem adını tıklatın. Gerekli parametreleri ekleyin ve **deneyin tıklayın!**.

![Örnek Swagger GET testi](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> Ekran görüntüleri için kullanılan Swagger UI sürümü sürüm 2'dir. Sürüm 3 örneği [için Petstore örneğine](https://petstore.swagger.io/)bakın.

## <a name="next-steps"></a>Sonraki adımlar

* [Swashbuckle kullanmaya başlama](xref:tutorials/get-started-with-swashbuckle)
* [NSwag kullanmaya başlama](xref:tutorials/get-started-with-nswag)
