---
title: ASP.NET Core kullanmaya başlayın
author: rick-anderson
description: Core'u kullanarak temel bir Hello World uygulaması oluşturan ve çalıştıran kısa ASP.NET bir öğretici.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
uid: getting-started
ms.openlocfilehash: 86a0c8d017138a949fddc0356f3de548d368a4c0
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417605"
---
# <a name="tutorial-get-started-with-aspnet-core"></a>Öğretici: ASP.NET Core ile başlayın

Bu öğretici, .NET Core CLI'yi kullanarak bir ASP.NET Core web uygulamasının nasıl oluşturulup çalıştırılabildiğini gösterir.

Şunları öğrenirsiniz:

> [!div class="checklist"]
> * Bir web uygulaması projesi oluşturun.
> * Geliştirme sertifikasına güvenin.
> * Uygulamayı çalıştırın.
> * Razor sayfasını edin.

Sonunda, yerel makinenizde çalışan bir web uygulamanız olur.

![Web uygulaması giriş sayfası](_static/home-page.png)

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a>Bir web uygulaması projesi oluşturma

Komut kabuğunu açın ve aşağıdaki komutu girin:

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

Yukarıdaki komut:

* Yeni bir web uygulaması oluşturur.  
* Parametre, `-o aspnetcoreapp` uygulamanın kaynak dosyalarıyla *aspnetcoreapp* adında bir dizin oluşturur.

### <a name="trust-the-development-certificate"></a>Geliştirme sertifikasına güvenin

HTTPS geliştirme sertifikasına güvenin:

# <a name="windows"></a>[Windows](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

Önceki komut aşağıdaki iletişim kutusunu görüntüler:

![Güvenlik uyarısı iletişim kutusu](~/getting-started/_static/cert.png)

Geliştirme sertifikasına güvenmeyi kabul ederseniz **Evet'i** seçin.

# <a name="macos"></a>[macOS](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

Önceki komut aşağıdaki iletiyi görüntüler:

*HTTPS geliştirme sertifikasına güvenmek istendi. Sertifikaya zaten güvenilen değilse, aşağıdaki komutu çalıştıracağız:*`'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`

Bu komut, parolanızın sertifikayı sistem anahtarlığına yüklemesini isteyebilir. Geliştirme sertifikasına güvenmeyi kabul ederseniz parolanızı girin.

# <a name="linux"></a>[Linux](#tab/linux)

HTTPS geliştirme sertifikasına nasıl güveninizle ilgili Linux dağıtımınızın belgelerine bakın.

---

Daha fazla bilgi için bkz: [ASP.NET Çekirdek HTTPS geliştirme sertifikasına güven](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)

## <a name="run-the-app"></a>Uygulamayı çalıştırma

Aşağıdaki komutları çalıştırın:

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

Komut kabuğu uygulamanın başladığını söyledikten sonra, `https://localhost:5001`'ye göz atın.

## <a name="edit-a-razor-page"></a>Jilet sayfasını edin

*Sayfaları/Index.cshtml'i* açın ve sayfayı aşağıdaki vurgulanan biçimlendirmeyle değiştirin ve kaydedin:

[!code-cshtml[](sample/index.cshtml?highlight=9)]

Sayfaya `https://localhost:5001`göz atın, yenileyin ve değişikliklerin görüntülendiğini doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Bir web uygulaması projesi oluşturun.
> * Geliştirme sertifikasına güvenin.
> * Projeyi çalıştırın.
> * Bir değişiklik yap.

ASP.NET Core hakkında daha fazla bilgi edinmek için, girişte önerilen öğrenme yoluna bakın:

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
