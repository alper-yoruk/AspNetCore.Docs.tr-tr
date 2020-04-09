---
title: Visual Studio Code ile Azure'da ASP.NET Core uygulaması yayınlama
author: rick-anderson
description: Visual Studio Code'u kullanarak Azure Uygulama Hizmeti'nde ASP.NET Core uygulamasını nasıl yayınlayacağınızı öğrenin
ms.author: riserrad
ms.custom: mvc
ms.date: 07/10/2019
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: 5f117cb2867a6e7b54269ef39abe819256b429ec
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80242684"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a>Visual Studio Code ile Azure'da ASP.NET Core uygulaması yayınlama

Yazar: [Ricardo Serradas](https://twitter.com/ricardoserradas)

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

Bir Uygulama Hizmeti dağıtım sorununu <xref:test/troubleshoot-azure-iis>gidermek için bkz.

## <a name="intro"></a>Giriş

Bu öğretici ile, nasıl bir ASP.Net Core MVC Uygulama oluşturmak ve Visual Studio Code içinde dağıtmak öğreneceksiniz.

## <a name="set-up"></a>Ayarla

- Hesabınız yoksa ücretsiz bir [Azure hesabı](https://azure.microsoft.com/free/dotnet/) açın.
- [Install .NET Core SDK](https://dotnet.microsoft.com/download)
- [Visual Studio Kodunu](https://code.visualstudio.com/Download) Yükleyin
  - [C# Uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) Visual Studio Koduna Yükleyin
  - Azure [Uygulama Hizmeti Uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) Visual Studio Koduna yükleyin ve devam etmeden önce yapılandırın

## <a name="create-an-aspnet-core-mvc-project"></a>ASP.Net Core MVC projesi oluşturma

Bir terminal kullanarak, projenin oluşturulmasını istediğiniz klasöre gidin ve aşağıdaki komutu kullanın:

```dotnetcli
dotnet new mvc
```

Aşağıdakilere benzer bir klasör yapınız olacak:

```cmd
      appsettings.Development.json
      appsettings.json
<DIR> Controllers
<DIR> Models
      netcore-vscode.csproj
<DIR> obj
      Program.cs
<DIR> Properties
      Startup.cs
<DIR> Views
<DIR> wwwroot
```

## <a name="open-it-with-visual-studio-code"></a>Visual Studio Code ile açın

Projeniz oluşturulduktan sonra aşağıdaki seçeneklerden birini kullanarak Visual Studio Code ile açabilirsiniz:

### <a name="through-the-command-line"></a>Komut satırı ndan

Projeyi oluşturduğunuz klasörde aşağıdaki komutu kullanın:

```cmd
> code .
```

Aşağıdaki komut çalışmıyorsa, [yüklemenizin bu bağlantıya](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform)başvurarak düzgün bir şekilde yapılandırıp yapılmadığını kontrol edin.

### <a name="through-visual-studio-code-interface"></a>Visual Studio Code arabirimi ile

- Görsel Stüdyo Kodunu Aç
- Menüde,`File > Open Folder`
- MVC Projesi'ni oluşturduğunuz klasörün kökünü seçin

Proje klasörünü açtığınızda, oluşturmak ve hata ayıklamak için gerekli varlıkların eksik olduğunu belirten bir ileti alırsınız. Bunları eklemek için yardım kabul edin.

![Proje yüklü Görsel Stüdyo Kodu arayüzü](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

Proje `.vscode` yapısının altında bir klasör oluşturulur. Aşağıdaki dosyaları içerecektir:

```cmd
launch.json
tasks.json
```

Bunlar,.NET Core Web Uygulamanızı oluşturmanıza ve hata ayıklamanıza yardımcı olacak yardımcı program dosyalarıdır.

## <a name="run-the-app"></a>Uygulamayı çalıştırma

Uygulamayı Azure'a dağıtmadan önce, uygulamanın yerel makinenizde düzgün şekilde çalıştırdığından emin olun.

- Projeyi çalıştırmak için F5 tuşuna basın

Web uygulamanız varsayılan tarayıcınızın yeni bir sekmesinde çalışmaya başlar. Başlar başlamaz bir gizlilik uyarısı fark edebilirsiniz. Bunun nedeni, uygulamanızın HTTP ve HTTPS'yi kullanmaya başlaması ve varsayılan olarak HTTPS bitiş noktasına yönlendirilen uygulamadır.

![Uygulamayı yerel olarak hata ayıklarken gizlilik uyarısı](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

Hata ayıklama oturumunu tutmak `Advanced` için `Continue to localhost (unsafe)`tıklatın ve sonra.

## <a name="generate-the-deployment-package-locally"></a>Dağıtım paketini yerel olarak oluşturma

- Açık Görsel Stüdyo Kodu terminali
- Bir `publish`alt klasöre `Release` bir paket oluşturmak için aşağıdaki komutu kullanın:
  - `dotnet publish -c Release -o ./publish`
- Proje `publish` yapısı altında yeni bir klasör oluşturulacak

![Klasör yapısını yayımlama](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a>Azure App Service’e yayımlama

Visual Studio Code için Azure Uygulama Hizmeti uzantısından yararlanarak, web sitesini doğrudan Azure Uygulama Hizmeti'nde yayınlamak için aşağıdaki adımları izleyin.

### <a name="if-youre-creating-a-new-web-app"></a>Yeni bir Web Uygulaması oluşturuyorsanız

- Klasöre `publish` sağ tıklayın ve`Deploy to Web App...`
- Web Uygulamasını oluşturmak istediğiniz aboneliği seçin
- `Create New Web App` seçeneğini belirleyin
- Web Uygulaması için bir ad girin

Uzantı yeni Web Uygulamasını oluşturur ve paketi otomatik olarak dağıtmaya başlar. Dağıtım tamamlandıktan sonra, `Browse Website` dağıtımı doğrulamak için tıklatın.

![Dağıtım başarılı ileti](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

Tıklattığınızda, `Browse Website`varsayılan tarayıcınızı kullanarak tarayıcıya gidersiniz:

![Yeni Web Uygulaması başarıyla dağıtıldı](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a>Varolan bir Web Uygulamasına dağıtım yapıyorsunuz

- Klasöre `publish` sağ tıklayın ve`Deploy to Web App...`
- Varolan Web Uygulamasının bulunduğu aboneliği seçin
- Listeden Web Uygulamasını seçin
- Visual Studio Code, varolan içeriğin üzerine yazmak isteyip istemediğinizi sorar. Onaylamak `Deploy` için tıklayın

Uzantı, güncelleştirilmiş içeriği Web Uygulamasına dağıtacaktır. Bu iş bittikten `Browse Website` sonra, dağıtımı doğrulamak için tıklatın.

![Mevcut Web Uygulaması başarıyla dağıtıldı](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a>Sonraki adımlar

- [İlk Azure DevOps ardCihazınızı oluşturun](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a>Ek kaynaklar

- [Azure App Service](/azure/app-service/app-service-web-overview)
- [Azure kaynak grupları](/azure/azure-resource-manager/resource-group-overview#resource-groups)
