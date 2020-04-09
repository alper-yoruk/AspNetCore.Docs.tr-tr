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
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a><span data-ttu-id="51eeb-103">ASP.NET Core web API yardım sayfaları Swagger / OpenAPI ile</span><span class="sxs-lookup"><span data-stu-id="51eeb-103">ASP.NET Core web API help pages with Swagger / OpenAPI</span></span>

<span data-ttu-id="51eeb-104">Yazar: [Christoph Nienaber](https://twitter.com/zuckerthoben) ve [Rico Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="51eeb-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="51eeb-105">Bir Web API'sini tüketirken, çeşitli yöntemlerini anlamak bir geliştirici için zor olabilir.</span><span class="sxs-lookup"><span data-stu-id="51eeb-105">When consuming a Web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="51eeb-106">[OpenAPI](https://www.openapis.org/)olarak da bilinen [Swagger,](https://swagger.io/)Web API'leri için yararlı belgeler ve yardım sayfaları oluşturma sorununu çözer.</span><span class="sxs-lookup"><span data-stu-id="51eeb-106">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for Web APIs.</span></span> <span data-ttu-id="51eeb-107">Etkileşimli dokümantasyon, istemci SDK oluşturma ve API keşfedilebilirlik gibi avantajlar sağlar.</span><span class="sxs-lookup"><span data-stu-id="51eeb-107">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="51eeb-108">Bu makalede, [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) ve [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger uygulamaları sergilenir:</span><span class="sxs-lookup"><span data-stu-id="51eeb-108">In this article, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger implementations are showcased:</span></span>

* <span data-ttu-id="51eeb-109">**Swashbuckle.AspNetCore** ASP.NET Core Web API'ları için Swagger belgeleri oluşturmak için bir açık kaynak projesidir.</span><span class="sxs-lookup"><span data-stu-id="51eeb-109">**Swashbuckle.AspNetCore** is an open source project for generating Swagger documents for ASP.NET Core Web APIs.</span></span>

* <span data-ttu-id="51eeb-110">**NSwag** Swagger belgeleri oluşturmak ve Core web API'ASP.NET [Swagger UI](https://swagger.io/swagger-ui/) veya [ReDoc](https://github.com/Rebilly/ReDoc) entegre için başka bir açık kaynak projesidir.</span><span class="sxs-lookup"><span data-stu-id="51eeb-110">**NSwag** is another open source project for generating Swagger documents and integrating [Swagger UI](https://swagger.io/swagger-ui/) or [ReDoc](https://github.com/Rebilly/ReDoc) into ASP.NET Core web APIs.</span></span> <span data-ttu-id="51eeb-111">Ayrıca, NSwag API'nız için C# ve TypeScript istemci kodu oluşturmak için yaklaşımlar sunar.</span><span class="sxs-lookup"><span data-stu-id="51eeb-111">Additionally, NSwag offers approaches to generate C# and TypeScript client code for your API.</span></span>

## <a name="what-is-swagger--openapi"></a><span data-ttu-id="51eeb-112">Swagger / OpenAPI nedir?</span><span class="sxs-lookup"><span data-stu-id="51eeb-112">What is Swagger / OpenAPI?</span></span>

<span data-ttu-id="51eeb-113">Swagger, [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) API'lerini tanımlamak için bir dil-agnostik belirtimidir.</span><span class="sxs-lookup"><span data-stu-id="51eeb-113">Swagger is a language-agnostic specification for describing [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) APIs.</span></span> <span data-ttu-id="51eeb-114">Swagger projesi [OpenAPI Girişimi'ne](https://www.openapis.org/)bağışlandı ve burada openapi olarak anılıyor.</span><span class="sxs-lookup"><span data-stu-id="51eeb-114">The Swagger project was donated to the [OpenAPI Initiative](https://www.openapis.org/), where it's now referred to as OpenAPI.</span></span> <span data-ttu-id="51eeb-115">Her iki ad birbirinin yerine kullanılır; ancak OpenAPI tercih edilir.</span><span class="sxs-lookup"><span data-stu-id="51eeb-115">Both names are used interchangeably; however, OpenAPI is preferred.</span></span> <span data-ttu-id="51eeb-116">Hem bilgisayarların hem de insanların bir hizmetin özelliklerini uygulamaya (kaynak kodu, ağ erişimi, dokümantasyon) doğrudan erişim olmadan anlamalarını sağlar.</span><span class="sxs-lookup"><span data-stu-id="51eeb-116">It allows both computers and humans to understand the capabilities of a service without any direct access to the implementation (source code, network access, documentation).</span></span> <span data-ttu-id="51eeb-117">Amaçlardan biri, bağlantısı kesilen hizmetleri bağlamak için gereken iş miktarını en aza indirmektir.</span><span class="sxs-lookup"><span data-stu-id="51eeb-117">One goal is to minimize the amount of work needed to connect disassociated services.</span></span> <span data-ttu-id="51eeb-118">Başka bir amaç, bir hizmeti doğru bir şekilde belgelemek için gereken süreyi azaltmaktır.</span><span class="sxs-lookup"><span data-stu-id="51eeb-118">Another goal is to reduce the amount of time needed to accurately document a service.</span></span>

## <a name="swagger-specification-swaggerjson"></a><span data-ttu-id="51eeb-119">Swagger belirtimi (swagger.json)</span><span class="sxs-lookup"><span data-stu-id="51eeb-119">Swagger specification (swagger.json)</span></span>

<span data-ttu-id="51eeb-120">Swagger akışının çekirdeği varsayılan olarak Swagger belirtimidir,&mdash; *swagger.json*adlı bir belgedir.</span><span class="sxs-lookup"><span data-stu-id="51eeb-120">The core to the Swagger flow is the Swagger specification&mdash;by default, a document named *swagger.json*.</span></span> <span data-ttu-id="51eeb-121">Hizmetinize dayalı olarak Swagger takım zinciri (veya üçüncü taraf uygulamaları) tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="51eeb-121">It's generated by the Swagger tool chain (or third-party implementations of it) based on your service.</span></span> <span data-ttu-id="51eeb-122">API'nizin yeteneklerini ve HTTP ile nasıl erişilenleri açıklar.</span><span class="sxs-lookup"><span data-stu-id="51eeb-122">It describes the capabilities of your API and how to access it with HTTP.</span></span> <span data-ttu-id="51eeb-123">Swagger UI'yi kullanır ve bulma ve istemci kodu oluşturmayı etkinleştirmek için takım zinciri tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="51eeb-123">It drives the Swagger UI and is used by the tool chain to enable discovery and client code generation.</span></span> <span data-ttu-id="51eeb-124">Kısaltma için azaltılmış bir Swagger belirtimi örneği aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="51eeb-124">Here's an example of a Swagger specification, reduced for brevity:</span></span>

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

## <a name="swagger-ui"></a><span data-ttu-id="51eeb-125">Swagger UI</span><span class="sxs-lookup"><span data-stu-id="51eeb-125">Swagger UI</span></span>

<span data-ttu-id="51eeb-126">[Swagger Kullanıcı İyi UI, oluşturulan Swagger](https://swagger.io/swagger-ui/) belirtimini kullanarak hizmet hakkında bilgi sağlayan web tabanlı bir kullanıcı arası hizmeti sunar.</span><span class="sxs-lookup"><span data-stu-id="51eeb-126">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated Swagger specification.</span></span> <span data-ttu-id="51eeb-127">Hem Swashbuckle hem de NSwag, bir ara yazılım kayıt çağrısı kullanarak ASP.NET Core uygulamanızda barındırılabilmek için Swagger UI'nin gömülü bir sürümünü içerir.</span><span class="sxs-lookup"><span data-stu-id="51eeb-127">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="51eeb-128">Web UI şuna benzer:</span><span class="sxs-lookup"><span data-stu-id="51eeb-128">The web UI looks like this:</span></span>

![Swagger UI](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="51eeb-130">Denetleyicilerinizdeki her genel eylem yöntemi UI'den test edilebilir.</span><span class="sxs-lookup"><span data-stu-id="51eeb-130">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="51eeb-131">Bölümü genişletmek için bir yöntem adını tıklatın.</span><span class="sxs-lookup"><span data-stu-id="51eeb-131">Click a method name to expand the section.</span></span> <span data-ttu-id="51eeb-132">Gerekli parametreleri ekleyin ve **deneyin tıklayın!**.</span><span class="sxs-lookup"><span data-stu-id="51eeb-132">Add any necessary parameters, and click **Try it out!**.</span></span>

![Örnek Swagger GET testi](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="51eeb-134">Ekran görüntüleri için kullanılan Swagger UI sürümü sürüm 2'dir.</span><span class="sxs-lookup"><span data-stu-id="51eeb-134">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="51eeb-135">Sürüm 3 örneği [için Petstore örneğine](https://petstore.swagger.io/)bakın.</span><span class="sxs-lookup"><span data-stu-id="51eeb-135">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="51eeb-136">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="51eeb-136">Next steps</span></span>

* [<span data-ttu-id="51eeb-137">Swashbuckle kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="51eeb-137">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="51eeb-138">NSwag kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="51eeb-138">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
