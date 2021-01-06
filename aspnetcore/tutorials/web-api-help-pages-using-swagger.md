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
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a><span data-ttu-id="c7f49-103">Swagger/Openapı ile Web API 'SI belgelerini ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c7f49-103">ASP.NET Core web API documentation with Swagger / OpenAPI</span></span>

<span data-ttu-id="c7f49-104">Sağlayan- [Christoph Nienaber](https://twitter.com/zuckerthoben) ve [Riko Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="c7f49-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="c7f49-105">Swagger (Openapı), REST API 'Leri açıklamak için dilden bağımsız bir belirtimdir.</span><span class="sxs-lookup"><span data-stu-id="c7f49-105">Swagger (OpenAPI) is a language-agnostic specification for describing REST APIs.</span></span> <span data-ttu-id="c7f49-106">Hem bilgisayarların hem de insanların kaynak koda doğrudan erişim olmadan bir REST API yeteneklerini anlamasına imkan sağlar.</span><span class="sxs-lookup"><span data-stu-id="c7f49-106">It allows both computers and humans to understand the capabilities of a REST API without direct access to the source code.</span></span> <span data-ttu-id="c7f49-107">Ana amaçları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="c7f49-107">Its main goals are to:</span></span>

* <span data-ttu-id="c7f49-108">Ayrılmış hizmetlere bağlanmak için gereken iş miktarını en aza indirin.</span><span class="sxs-lookup"><span data-stu-id="c7f49-108">Minimize the amount of work needed to connect decoupled services.</span></span>
* <span data-ttu-id="c7f49-109">Bir hizmeti doğru bir şekilde belgelemek için gereken süre miktarını azaltın.</span><span class="sxs-lookup"><span data-stu-id="c7f49-109">Reduce the amount of time needed to accurately document a service.</span></span>

<span data-ttu-id="c7f49-110">.NET için iki ana Openapı uygulaması, [swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) ve [nswag](https://github.com/RicoSuter/NSwag)' dir, bkz.:</span><span class="sxs-lookup"><span data-stu-id="c7f49-110">The two main OpenAPI implementations for .NET are [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag), see:</span></span>

* [<span data-ttu-id="c7f49-111">Swashbuckle ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="c7f49-111">Getting Started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="c7f49-112">NSwag ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="c7f49-112">Getting Started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a><span data-ttu-id="c7f49-113">Openapı ile Swagger</span><span class="sxs-lookup"><span data-stu-id="c7f49-113">OpenApi vs. Swagger</span></span>

<span data-ttu-id="c7f49-114">Swagger projesi, 2015 ' de Openapı girişimi 'ne bağlılmıştı ve bu yana Openapı olarak adlandırılmıştı.</span><span class="sxs-lookup"><span data-stu-id="c7f49-114">The Swagger project was donated to the OpenAPI Initiative in 2015 and has since been referred to as OpenAPI.</span></span> <span data-ttu-id="c7f49-115">Her iki ad birbirinin yerine kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c7f49-115">Both names are used interchangeably.</span></span> <span data-ttu-id="c7f49-116">Ancak, "OpenAPI" belirtiye başvurur.</span><span class="sxs-lookup"><span data-stu-id="c7f49-116">However, "OpenAPI" refers to the specification.</span></span> <span data-ttu-id="c7f49-117">"Swagger", Openapı belirtimiyle birlikte çalışan, Smartden açık kaynaklı ve ticari ürünlerin ailesini ifade eder.</span><span class="sxs-lookup"><span data-stu-id="c7f49-117">"Swagger" refers to the family of open-source and commercial products from SmartBear that work with the OpenAPI Specification.</span></span> <span data-ttu-id="c7f49-118">[Openapigenerator](https://github.com/OpenAPITools/openapi-generator)gibi sonraki açık kaynaklı ürünler, Ayrıca, akıllı pul tarafından yayımlanmamasına rağmen Swagger aile adı altına düşdü.</span><span class="sxs-lookup"><span data-stu-id="c7f49-118">Subsequent open-source products, such as [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), also fall under the Swagger family name, despite not being released by SmartBear.</span></span>

<span data-ttu-id="c7f49-119">Kısacası:</span><span class="sxs-lookup"><span data-stu-id="c7f49-119">In short:</span></span>

* <span data-ttu-id="c7f49-120">Openapı bir belirtimdir.</span><span class="sxs-lookup"><span data-stu-id="c7f49-120">OpenAPI is a specification.</span></span>
* <span data-ttu-id="c7f49-121">Swagger, Openapı belirtimini kullanan araçlama araçları.</span><span class="sxs-lookup"><span data-stu-id="c7f49-121">Swagger is tooling that uses the OpenAPI specification.</span></span> <span data-ttu-id="c7f49-122">Örneğin, OpenAPIGenerator ve SwaggerUI.</span><span class="sxs-lookup"><span data-stu-id="c7f49-122">For example, OpenAPIGenerator and SwaggerUI.</span></span>

## <a name="openapi-specification-openapijson"></a><span data-ttu-id="c7f49-123">Openapı belirtimi (openapi.json)</span><span class="sxs-lookup"><span data-stu-id="c7f49-123">OpenAPI specification (openapi.json)</span></span>

<span data-ttu-id="c7f49-124">Openapı belirtimi, API 'nizin yeteneklerini açıklayan bir belgedir.</span><span class="sxs-lookup"><span data-stu-id="c7f49-124">The OpenAPI specification is a document that describes the capabilities of your API.</span></span> <span data-ttu-id="c7f49-125">Belge, denetleyiciler ve modeller içindeki XML ve öznitelik ek açıklamalarını temel alır.</span><span class="sxs-lookup"><span data-stu-id="c7f49-125">The document is based on the XML and attribute annotations within the controllers and models.</span></span> <span data-ttu-id="c7f49-126">Openapı akışının temel parçasıdır ve SwaggerUI gibi araçları sağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c7f49-126">It's the core part of the OpenAPI flow and is used to drive tooling such as SwaggerUI.</span></span> <span data-ttu-id="c7f49-127">Varsayılan olarak, *openapi.jsolarak* adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="c7f49-127">By default, it's named *openapi.json*.</span></span> <span data-ttu-id="c7f49-128">Aşağıda, breçekimi için azaltılmış bir Openapı belirtimi örneği verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="c7f49-128">Here's an example of an OpenAPI specification, reduced for brevity:</span></span>

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

## <a name="swagger-ui"></a><span data-ttu-id="c7f49-129">Swagger Kullanıcı arabirimi</span><span class="sxs-lookup"><span data-stu-id="c7f49-129">Swagger UI</span></span>

<span data-ttu-id="c7f49-130">[Swagger Kullanıcı arabirimi](https://swagger.io/swagger-ui/) , oluşturulan openapı belirtimini kullanarak hizmet hakkında bilgi sağlayan Web tabanlı bir kullanıcı arabirimi sunar.</span><span class="sxs-lookup"><span data-stu-id="c7f49-130">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated OpenAPI specification.</span></span> <span data-ttu-id="c7f49-131">Hem swashbuckle hem de NSwag, Swagger Kullanıcı arabirimi 'nin ekli bir sürümünü içerir, böylece bir ara yazılım kayıt çağrısı kullanarak ASP.NET Core uygulamanızda barındırılabilmesini sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c7f49-131">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="c7f49-132">Web Kullanıcı arabirimi şöyle görünür:</span><span class="sxs-lookup"><span data-stu-id="c7f49-132">The web UI looks like this:</span></span>

![Swagger Kullanıcı arabirimi](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="c7f49-134">Denetleyicilerinizdeki her genel eylem yöntemi kullanıcı arabiriminden test edilebilir.</span><span class="sxs-lookup"><span data-stu-id="c7f49-134">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="c7f49-135">Bölümü genişletmek için bir yöntem adı seçin.</span><span class="sxs-lookup"><span data-stu-id="c7f49-135">Select a method name to expand the section.</span></span> <span data-ttu-id="c7f49-136">Gerekli parametreleri ekleyin ve **deneyin!** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="c7f49-136">Add any necessary parameters, and select **Try it out!**.</span></span>

![Örnek Swagger GET testi](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="c7f49-138">Ekran görüntüleri için kullanılan Swagger Kullanıcı arabirimi sürümü sürüm 2 ' dir.</span><span class="sxs-lookup"><span data-stu-id="c7f49-138">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="c7f49-139">Sürüm 3 örneği için bkz. [petstore örneği](https://petstore.swagger.io/).</span><span class="sxs-lookup"><span data-stu-id="c7f49-139">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="c7f49-140">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="c7f49-140">Next steps</span></span>

* [<span data-ttu-id="c7f49-141">Swashbuckle kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="c7f49-141">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="c7f49-142">NSwag kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="c7f49-142">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
