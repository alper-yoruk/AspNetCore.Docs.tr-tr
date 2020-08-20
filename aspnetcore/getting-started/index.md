---
title: ASP.NET Core kullanmaya başlayın
author: rick-anderson
description: ASP.NET Core kullanarak temel bir Merhaba Dünya uygulaması oluşturan ve çalıştıran kısa bir öğretici.
ms.author: riande
ms.custom: mvc
ms.date: 01/07/2020
no-loc:
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
uid: getting-started
ms.openlocfilehash: afded8890afe3b8f7b1d0b5634fc7764906bc9d7
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635014"
---
# <a name="tutorial-get-started-with-aspnet-core"></a>Öğretici: ASP.NET Core kullanmaya başlayın

Bu öğreticide, .NET Core CLI kullanılarak ASP.NET Core bir Web uygulamasının nasıl oluşturulacağı ve çalıştırılacağı gösterilmektedir.

Şunları öğrenirsiniz:

> [!div class="checklist"]
> * Bir Web uygulaması projesi oluşturun.
> * Geliştirme sertifikasına güvenin.
> * Uygulamayı çalıştırın.
> * Bir Razor sayfayı düzenleyin.

Sonunda, yerel makinenizde çalışan bir çalışan Web uygulamanız olacaktır.

![Web uygulaması giriş sayfası](_static/home-page.png)

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE[](~/includes/3.1-SDK.md)]

## <a name="create-a-web-app-project"></a>Web uygulaması projesi oluşturma

Bir komut kabuğu açın ve şu komutu girin:

```dotnetcli
dotnet new webapp -o aspnetcoreapp
```

Yukarıdaki komut:

* Yeni bir Web uygulaması oluşturur.  
* `-o aspnetcoreapp`Parametresi, uygulama için kaynak dosyalarla *aspnetcoreapp* adlı bir dizin oluşturur.

### <a name="trust-the-development-certificate"></a>Geliştirme sertifikasına güven

HTTPS geliştirme sertifikasına güvenin:

# <a name="windows"></a>[Windows](#tab/windows)

```dotnetcli
dotnet dev-certs https --trust
```

Yukarıdaki komutta aşağıdaki iletişim kutusu görüntülenir:

![Güvenlik Uyarısı iletişim kutusu](~/getting-started/_static/cert.png)

Geliştirme sertifikasına güvenmeyi kabul ediyorsanız **Evet** ' i seçin.

# <a name="macos"></a>[macOS](#tab/macos)

```dotnetcli
dotnet dev-certs https --trust
```

Yukarıdaki komut aşağıdaki iletiyi görüntüler:

*Https geliştirme sertifikasına güvenmek istendi. Sertifikaya zaten güvenilmiyorsa, şu komutu çalıştıracağız:*`'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain <<certificate>>'`

Bu komut, sertifikayı sistem anahtarlığınıza yüklemek için parolanızı isteyebilir. Geliştirme sertifikasına güvenmeyi kabul ediyorsanız parolanızı girin.

# <a name="linux"></a>[Linux](#tab/linux)

HTTPS geliştirme sertifikasına güvenmek için Linux dağılııza yönelik belgelere bakın.

---

Daha fazla bilgi için bkz [. asp.NET Core https geliştirme sertifikasına güven](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)

## <a name="run-the-app"></a>Uygulamayı çalıştırma

Aşağıdaki komutları çalıştırın:

```dotnetcli
cd aspnetcoreapp
dotnet watch run
```

Komut kabuğu, uygulamanın başlatıldığını gösteriyorsa, öğesine gidin `https://localhost:5001` .

## <a name="edit-a-no-locrazor-page"></a>Sayfa düzenleme Razor

*Pages/Index. cshtml* dosyasını açın ve sayfayı aşağıdaki vurgulanmış işaretlerle değiştirin ve kaydedin:

[!code-cshtml[](sample/index.cshtml?highlight=9)]

Sayfasına gidin `https://localhost:5001` , sayfayı yenileyin ve değişikliklerin görüntülendiğini doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Bir Web uygulaması projesi oluşturun.
> * Geliştirme sertifikasına güvenin.
> * Projeyi çalıştırın.
> * Değişiklik yapın.

ASP.NET Core hakkında daha fazla bilgi edinmek için bkz. giriş bölümünde önerilen öğrenme yolu:

> [!div class="nextstepaction"]
> <xref:index#recommended-learning-path>
