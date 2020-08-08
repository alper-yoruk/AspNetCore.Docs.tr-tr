---
title: Swagger/Openapı ile Web API Yardım sayfaları ASP.NET Core
author: RicoSuter
description: Bu öğretici, bir Web API uygulaması için belge ve yardım sayfaları oluşturmak üzere Swagger ekleme hakkında bir yol sağlar.
ms.author: scaddie
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: a3b8efcdb1f196fc878e0ad29580d305bb24f50a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021984"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a>Swagger/Openapı ile Web API Yardım sayfaları ASP.NET Core

Sağlayan- [Christoph Nienaber](https://twitter.com/zuckerthoben) ve [Riko Suter](https://blog.rsuter.com/)

Bir Web API 'sini kullanırken, çeşitli yöntemlerini anlamak bir geliştirici için zor olabilir. [Openapı](https://www.openapis.org/)olarak da bilinen [Swagger](https://swagger.io/), Web API 'leri için faydalı belge ve yardım sayfaları oluşturma sorununu çözer. Etkileşimli belgeler, istemci SDK 'Sı oluşturma ve API bulunabilirliği gibi avantajlar sağlar.

Bu makalede, [swashbuckle. AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) ve [nswag](https://github.com/RicoSuter/NSwag) .net Swagger uygulamaları gösterilir:

* **Swashbuckle. AspNetCore** , ASP.NET Core Web API 'Leri için Swagger belgelerini oluşturmaya yönelik açık kaynak bir projem.

* **Nswag** , Swagger belgelerini oluşturmaya ve [Swagger Kullanıcı arabirimini](https://swagger.io/swagger-ui/) veya [yeniden belgeyi](https://github.com/Rebilly/ReDoc) ASP.NET Core Web API 'lerine tümleştirmede başka bir açık kaynak projem. Ayrıca, NSwag API 'niz için C# ve TypeScript istemci kodu oluşturma yaklaşımları sunmaktadır.

## <a name="what-is-swagger--openapi"></a>Swagger/Openapı nedir?

Swagger, [rest](https://en.wikipedia.org/wiki/Representational_state_transfer) API 'leri açıklamak için dilden bağımsız bir belirtimdir. Swagger projesi, openapı [Initiative](https://www.openapis.org/)'e bağlılmıştı ve burada openapı olarak adlandırılmıştır. Her iki ad de birbirinin yerine kullanılır; Ancak, Openapı tercih edilir. Hem bilgisayarların hem de insanların uygulamaya doğrudan erişim (kaynak kodu, ağ erişimi, belgeler) olmadan bir hizmetin yeteneklerini anlamasına imkan sağlar. Bir hedef, bağlantı ilişkilendirmesi yapılan hizmetleri bağlamak için gereken iş miktarını en aza indirmektir. Başka bir amaç, bir hizmeti doğru bir şekilde belgelemek için gereken süre miktarını azaltmaktır.

## <a name="openapi-specification-openapijson"></a>Openapı belirtimi (openapi.json)

Openapı akışının çekirdeği, &mdash; Varsayılan olarak, *openapi.js*adlı bir belge olan belirtimdir. Bu, hizmetinize bağlı olarak Openapı araç zinciri (veya bunun üçüncü taraf uygulamaları) tarafından oluşturulmuştur. API 'nizin yeteneklerini ve HTTP ile nasıl erişebileceğinizi açıklar. Swagger Kullanıcı arabirimini yürütür ve araç zinciri tarafından keşif ve istemci kodu oluşturmayı etkinleştirmek için kullanılır. Aşağıda, breçekimi için azaltılmış bir Openapı belirtimi örneği verilmiştir:

```json
{
  "openapi": "3.0.1",
  "info": {
    "title": "API V1",
    "version": "v1"
  },
  "paths": {
    "/api/Todo": {
      "get": {
        "tags": [
          "Todo"
        ],
        "operationId": "ApiTodoGet",
        "responses": {
          "200": {
            "description": "Success",
            "content": {
              "text/plain": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "application/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "text/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              }
            }
          }
        }
      },
      "post": {
        …
      }
    },
    "/api/Todo/{id}": {
      "get": {
        …
      },
      "put": {
        …
      },
      "delete": {
        …
      }
    }
  },
  "components": {
    "schemas": {
      "ToDoItem": {
        "type": "object",
        "properties": {
          "id": {
            "type": "integer",
            "format": "int32"
          },
          "name": {
            "type": "string",
            "nullable": true
          },
          "isCompleted": {
            "type": "boolean"
          }
        },
        "additionalProperties": false
      }
    }
  }
}
```

## <a name="swagger-ui"></a>Swagger Kullanıcı arabirimi

[Swagger Kullanıcı arabirimi](https://swagger.io/swagger-ui/) , oluşturulan openapı belirtimini kullanarak hizmet hakkında bilgi sağlayan Web tabanlı bir kullanıcı arabirimi sunar. Hem swashbuckle hem de NSwag, Swagger Kullanıcı arabirimi 'nin ekli bir sürümünü içerir, böylece bir ara yazılım kayıt çağrısı kullanarak ASP.NET Core uygulamanızda barındırılabilmesini sağlayabilirsiniz. Web Kullanıcı arabirimi şöyle görünür:

![Swagger Kullanıcı arabirimi](web-api-help-pages-using-swagger/_static/swagger-ui.png)

Denetleyicilerinizdeki her genel eylem yöntemi kullanıcı arabiriminden test edilebilir. Bölümü genişletmek için bir yöntem adına tıklayın. Gerekli parametreleri ekleyin ve **deneyin!**' e tıklayın.

![Örnek Swagger GET testi](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> Ekran görüntüleri için kullanılan Swagger Kullanıcı arabirimi sürümü sürüm 2 ' dir. Sürüm 3 örneği için bkz. [petstore örneği](https://petstore.swagger.io/).

## <a name="next-steps"></a>Sonraki adımlar

* [Swashbuckle kullanmaya başlama](xref:tutorials/get-started-with-swashbuckle)
* [NSwag kullanmaya başlama](xref:tutorials/get-started-with-nswag)
