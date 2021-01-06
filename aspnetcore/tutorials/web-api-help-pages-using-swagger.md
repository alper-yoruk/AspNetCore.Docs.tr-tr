---
title: Swagger/Openapı ile Web API 'SI belgelerini ASP.NET Core
author: RicoSuter
description: Bu öğretici, bir Web API uygulaması için belge ve yardım sayfaları oluşturmak üzere Swagger ekleme hakkında bir yol sağlar.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
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
ms.openlocfilehash: e5442c88048cf41e289fb476b4082cb6029b1b75
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93062460"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a>Swagger/Openapı ile Web API 'SI belgelerini ASP.NET Core

Sağlayan- [Christoph Nienaber](https://twitter.com/zuckerthoben) ve [Riko Suter](https://blog.rsuter.com/)

Swagger (Openapı), REST API 'Leri açıklamak için dilden bağımsız bir belirtimdir. Hem bilgisayarların hem de insanların kaynak koda doğrudan erişim olmadan bir REST API yeteneklerini anlamasına imkan sağlar. Ana amaçları şunlardır:

* Ayrılmış hizmetlere bağlanmak için gereken iş miktarını en aza indirin.
* Bir hizmeti doğru bir şekilde belgelemek için gereken süre miktarını azaltın.

.NET için iki ana Openapı uygulaması, [swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) ve [nswag](https://github.com/RicoSuter/NSwag)' dir, bkz.:

* [Swashbuckle ile çalışmaya başlama](xref:tutorials/get-started-with-swashbuckle)
* [NSwag ile çalışmaya başlama](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a>Openapı ile Swagger

Swagger projesi, 2015 ' de Openapı girişimi 'ne bağlılmıştı ve bu yana Openapı olarak adlandırılmıştı. Her iki ad birbirinin yerine kullanılır. Ancak, "OpenAPI" belirtiye başvurur. "Swagger", Openapı belirtimiyle birlikte çalışan, Smartden açık kaynaklı ve ticari ürünlerin ailesini ifade eder. [Openapigenerator](https://github.com/OpenAPITools/openapi-generator)gibi sonraki açık kaynaklı ürünler, Ayrıca, akıllı pul tarafından yayımlanmamasına rağmen Swagger aile adı altına düşdü.

Kısacası:

* Openapı bir belirtimdir.
* Swagger, Openapı belirtimini kullanan araçlama araçları. Örneğin, OpenAPIGenerator ve SwaggerUI.

## <a name="openapi-specification-openapijson"></a>Openapı belirtimi (openapi.json)

Openapı belirtimi, API 'nizin yeteneklerini açıklayan bir belgedir. Belge, denetleyiciler ve modeller içindeki XML ve öznitelik ek açıklamalarını temel alır. Openapı akışının temel parçasıdır ve SwaggerUI gibi araçları sağlamak için kullanılır. Varsayılan olarak, *openapi.jsolarak* adlandırılır. Aşağıda, breçekimi için azaltılmış bir Openapı belirtimi örneği verilmiştir:

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

Denetleyicilerinizdeki her genel eylem yöntemi kullanıcı arabiriminden test edilebilir. Bölümü genişletmek için bir yöntem adı seçin. Gerekli parametreleri ekleyin ve **deneyin!** seçeneğini belirleyin.

![Örnek Swagger GET testi](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> Ekran görüntüleri için kullanılan Swagger Kullanıcı arabirimi sürümü sürüm 2 ' dir. Sürüm 3 örneği için bkz. [petstore örneği](https://petstore.swagger.io/).

## <a name="next-steps"></a>Sonraki adımlar

* [Swashbuckle kullanmaya başlama](xref:tutorials/get-started-with-swashbuckle)
* [NSwag kullanmaya başlama](xref:tutorials/get-started-with-nswag)
