---
title: ASP.NET Core ile tepki verme-Redux proje şablonunu kullanın
author: SteveSandersonMS
description: Redux ve Create-tepki-App ile tepki vermek için ASP.NET Core tek sayfalı uygulama (SPA) proje şablonunu kullanmaya nasıl başlacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/13/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: spa/react-with-redux
ms.openlocfilehash: 9ae96b14f3f50d4079933bbd893ff95fa677d273
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054508"
---
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a><span data-ttu-id="2283b-103">ASP.NET Core ile tepki verme-Redux proje şablonunu kullanın</span><span class="sxs-lookup"><span data-stu-id="2283b-103">Use the React-with-Redux project template with ASP.NET Core</span></span>

<span data-ttu-id="2283b-104">Güncelleştirilmiş Redux proje şablonu, zengin, istemci tarafı bir kullanıcı arabirimi (UI) uygulamak için tepki verme, Redux ve [oluşturma-tepki düzeyi uygulama](https://github.com/facebookincubator/create-react-app) (CRA) kurallarını kullanan ASP.NET Core uygulamalar için uygun bir başlangıç noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="2283b-104">The updated React-with-Redux project template provides a convenient starting point for ASP.NET Core apps using React, Redux, and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="2283b-105">Proje oluşturma komutu hariç, Redux şablonuyla ilgili tüm bilgiler, yanıt verme şablonuyla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="2283b-105">With the exception of the project creation command, all information about the React-with-Redux template is the same as the React template.</span></span> <span data-ttu-id="2283b-106">Bu proje türünü oluşturmak için yerine çalıştırın `dotnet new reactredux` `dotnet new react` .</span><span class="sxs-lookup"><span data-stu-id="2283b-106">To create this project type, run `dotnet new reactredux` instead of `dotnet new react`.</span></span> <span data-ttu-id="2283b-107">Yanıt tabanlı şablonlar için ortak işlevsellik hakkında daha fazla bilgi için bkz. tepki verme [şablonu belgeleri](xref:spa/react).</span><span class="sxs-lookup"><span data-stu-id="2283b-107">For more information about the functionality common to both React-based templates, see [React template documentation](xref:spa/react).</span></span>

<span data-ttu-id="2283b-108">IIS 'de bir yanıt verme-Redux alt uygulaması yapılandırma hakkında daha fazla bilgi için bkz. [ReactRedux Template 2,1: IIS 'de Spa kullanılamıyor (ASPNET/şablon oluşturma &num; 555)](https://github.com/aspnet/Templating/issues/555).</span><span class="sxs-lookup"><span data-stu-id="2283b-108">For information on configuring a React-with-Redux sub-application in IIS, see [ReactRedux Template 2.1: Unable to use SPA on IIS (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).</span></span>
