---
title: ASP.NET Core ile Redux ile Tepki proje şablonunu kullanma
author: SteveSandersonMS
description: Redux ile React ve create-react-app için ASP.NET Core Single Page Application (SPA) proje şablonu ile nasıl başlayın öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/13/2019
uid: spa/react-with-redux
ms.openlocfilehash: ed2e9aea449ddb09fef049a391f40f57452786a8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657642"
---
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a><span data-ttu-id="8bad0-103">ASP.NET Core ile Redux ile Tepki proje şablonunu kullanma</span><span class="sxs-lookup"><span data-stu-id="8bad0-103">Use the React-with-Redux project template with ASP.NET Core</span></span>

<span data-ttu-id="8bad0-104">Güncelleştirilmiş Redux ile Tepki projesi şablonu, zengin, istemci tarafı kullanıcı arabirimi (UI) uygulamak için React, Redux ve [create-react-app](https://github.com/facebookincubator/create-react-app) (MKK) kurallarını kullanarak ASP.NET Core uygulamaları için uygun bir başlangıç noktası sağlar.</span><span class="sxs-lookup"><span data-stu-id="8bad0-104">The updated React-with-Redux project template provides a convenient starting point for ASP.NET Core apps using React, Redux, and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="8bad0-105">Proje oluşturma komutu dışında, Redux ile Tepki şablonu hakkındaki tüm bilgiler React şablonuyla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="8bad0-105">With the exception of the project creation command, all information about the React-with-Redux template is the same as the React template.</span></span> <span data-ttu-id="8bad0-106">Bu proje türünü oluşturmak `dotnet new reactredux` için, '' yerine `dotnet new react`çalıştırın</span><span class="sxs-lookup"><span data-stu-id="8bad0-106">To create this project type, run `dotnet new reactredux` instead of `dotnet new react`.</span></span> <span data-ttu-id="8bad0-107">Her iki Tepki tabanlı şablonda ortak olan işlevsellik hakkında daha fazla bilgi için [bkz.](xref:spa/react)</span><span class="sxs-lookup"><span data-stu-id="8bad0-107">For more information about the functionality common to both React-based templates, see [React template documentation](xref:spa/react).</span></span>

<span data-ttu-id="8bad0-108">IIS'de Redux ile React-with-Redux alt uygulamasının yapılandırılması hakkında daha fazla bilgi için [bkz. &num;](https://github.com/aspnet/Templating/issues/555)</span><span class="sxs-lookup"><span data-stu-id="8bad0-108">For information on configuring a React-with-Redux sub-application in IIS, see [ReactRedux Template 2.1: Unable to use SPA on IIS (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).</span></span>
