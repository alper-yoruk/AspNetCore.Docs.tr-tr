---
title: HTTP REPL ile Web API 'Lerini test etme
author: scottaddie
description: HTTP REPL .NET Core küresel aracının bir ASP.NET Core Web API 'sini taramak ve test etmek için nasıl kullanılacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, devx-track-azurecli
ms.date: 05/20/2020
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
uid: web-api/http-repl
ms.openlocfilehash: b12f4b10230f2631392011a6e443156bf9a2d843
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491450"
---
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="4ef6d-103">HTTP REPL ile Web API 'Lerini test etme</span><span class="sxs-lookup"><span data-stu-id="4ef6d-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="4ef6d-104">[Scott Ade](https://twitter.com/Scott_Addie) tarafından</span><span class="sxs-lookup"><span data-stu-id="4ef6d-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="4ef6d-105">HTTP okuma-değerlendirme-yazdırma döngüsü (REPL):</span><span class="sxs-lookup"><span data-stu-id="4ef6d-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="4ef6d-106">.NET Core 'un her yerde desteklenen basit, platformlar arası bir komut satırı aracı desteklenir.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="4ef6d-107">ASP.NET Core Web API 'Lerini (ve non-ASP.NET çekirdek Web API 'Leri) test etmek ve sonuçlarını görüntülemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="4ef6d-108">Localhost ve Azure App Service dahil olmak üzere herhangi bir ortamda barındırılan Web API 'Lerini test etme özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="4ef6d-109">Aşağıdaki [http fiilleri](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) desteklenir:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="4ef6d-110">SILMELI</span><span class="sxs-lookup"><span data-stu-id="4ef6d-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="4ef6d-111">GET</span><span class="sxs-lookup"><span data-stu-id="4ef6d-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="4ef6d-112">BAŞLı</span><span class="sxs-lookup"><span data-stu-id="4ef6d-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="4ef6d-113">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="4ef6d-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="4ef6d-114">DÜZELTMESI</span><span class="sxs-lookup"><span data-stu-id="4ef6d-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="4ef6d-115">Yayınla</span><span class="sxs-lookup"><span data-stu-id="4ef6d-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="4ef6d-116">PUT</span><span class="sxs-lookup"><span data-stu-id="4ef6d-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="4ef6d-117">Takip etmek için, [örnek ASP.NET Core Web API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) 'sini ([indirme](xref:index#how-to-download-a-sample)) görüntüleyin veya indirin.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4ef6d-118">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="4ef6d-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="4ef6d-119">Yükleme</span><span class="sxs-lookup"><span data-stu-id="4ef6d-119">Installation</span></span>

<span data-ttu-id="4ef6d-120">HTTP REPL 'u yüklemek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-120">To install the HTTP REPL, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

<span data-ttu-id="4ef6d-121">[.NET Core küresel aracı](/dotnet/core/tools/global-tools#install-a-global-tool) , [Microsoft. DotNet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet paketinden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="4ef6d-122">Kullanım</span><span class="sxs-lookup"><span data-stu-id="4ef6d-122">Usage</span></span>

<span data-ttu-id="4ef6d-123">Aracın başarıyla yüklenmesinden sonra, HTTP REPL 'u başlatmak için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-123">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
httprepl
```

<span data-ttu-id="4ef6d-124">Kullanılabilir HTTP REPL komutlarını görüntülemek için aşağıdaki komutlardan birini çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-124">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
httprepl -h
```

```console
httprepl --help
```

<span data-ttu-id="4ef6d-125">Aşağıdaki çıktı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-125">The following output is displayed:</span></span>

```console
Usage:
  httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

Setup Commands:
Use these commands to configure the tool for your API server

connect        Configures the directory structure and base address of the api server
set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

set base       Set the base URI. e.g. `set base http://locahost:5000`
ls             Show all endpoints for the current path
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands
exit           Exit the shell

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\\Program Files\\Microsoft VS Code\\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line
ui             Displays the Swagger UI page, if available, in the default browser

Use `help <COMMAND>` for more detail on an individual command. e.g. `help get`.
For detailed tool info, see https://aka.ms/http-repl-doc.
```

<span data-ttu-id="4ef6d-126">HTTP REPL komut tamamlama sağlar.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-126">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="4ef6d-127"><kbd>Sekme</kbd> tuşuna basıldığında, yazdığınız KARAKTERLERI veya API uç noktasını tamamlayacak komutların listesi üzerinden yinelenir.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="4ef6d-128">Aşağıdaki bölümlerde kullanılabilir CLı komutları ana hatlarıyla verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="4ef6d-129">Web API 'sine bağlanma</span><span class="sxs-lookup"><span data-stu-id="4ef6d-129">Connect to the web API</span></span>

<span data-ttu-id="4ef6d-130">Aşağıdaki komutu çalıştırarak bir Web API 'sine bağlanın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-130">Connect to a web API by running the following command:</span></span>

```console
httprepl <ROOT URI>
```

<span data-ttu-id="4ef6d-131">`<ROOT URI>` , Web API 'sinin temel URI 'sidir.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="4ef6d-132">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-132">For example:</span></span>

```console
httprepl https://localhost:5001
```

<span data-ttu-id="4ef6d-133">Alternatif olarak, HTTP REPL çalışırken istediğiniz zaman aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-133">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="4ef6d-134">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-134">For example:</span></span>

```console
(Disconnected)~ connect https://localhost:5001
```

## <a name="manually-point-to-the-swagger-document-for-the-web-api"></a><span data-ttu-id="4ef6d-135">Web API 'SI için Swagger belgesine el ile işaret edin</span><span class="sxs-lookup"><span data-stu-id="4ef6d-135">Manually point to the Swagger document for the web API</span></span>

<span data-ttu-id="4ef6d-136">Yukarıdaki Connect komutu Swagger belgesini otomatik olarak bulmaya çalışacaktır.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-136">The connect command above will attempt to find the Swagger document automatically.</span></span> <span data-ttu-id="4ef6d-137">Bir nedenden dolayı yapamaması durumunda, bu seçeneği kullanarak Web API 'SI için Swagger belgesinin URI 'sini belirtebilirsiniz `--swagger` :</span><span class="sxs-lookup"><span data-stu-id="4ef6d-137">If for some reason it is unable to do so, you can specify the URI of the Swagger document for the web API by using the `--swagger` option:</span></span>

```console
connect <ROOT URI> --swagger <SWAGGER URI>
```

<span data-ttu-id="4ef6d-138">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-138">For example:</span></span>

```console
(Disconnected)~ connect https://localhost:5001 --swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="4ef6d-139">Web API 'sinde gezin</span><span class="sxs-lookup"><span data-stu-id="4ef6d-139">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="4ef6d-140">Kullanılabilir uç noktaları görüntüle</span><span class="sxs-lookup"><span data-stu-id="4ef6d-140">View available endpoints</span></span>

<span data-ttu-id="4ef6d-141">Web API adresinin geçerli yolundaki farklı uç noktaları (denetleyiciler) listelemek için, `ls` veya `dir` komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-141">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/~ ls
```

<span data-ttu-id="4ef6d-142">Aşağıdaki çıkış biçimi görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-142">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="4ef6d-143">Yukarıdaki çıkış, kullanılabilir iki denetleyici olduğunu gösterir: `Fruits` ve `People` .</span><span class="sxs-lookup"><span data-stu-id="4ef6d-143">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="4ef6d-144">Her iki denetleyici de parametresiz HTTP GET ve POST işlemlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-144">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="4ef6d-145">Belirli bir denetleyicide gezinmek daha ayrıntılı bilgi gösterir.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-145">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="4ef6d-146">Örneğin, aşağıdaki komut çıktısı, `Fruits` denetleyiciyi de http get, put ve DELETE işlemlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-146">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="4ef6d-147">Bu işlemlerin her biri `id` , rotada bir parametre bekler:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-147">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

<span data-ttu-id="4ef6d-148">Alternatif olarak, `ui` Web API 'Sinin Swagger Kullanıcı Arabirimi sayfasını bir tarayıcıda açmak için komutunu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-148">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="4ef6d-149">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-149">For example:</span></span>

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="4ef6d-150">Bir uç noktaya gitme</span><span class="sxs-lookup"><span data-stu-id="4ef6d-150">Navigate to an endpoint</span></span>

<span data-ttu-id="4ef6d-151">Web API 'sindeki farklı bir uç noktaya gitmek için `cd` komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-151">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/~ cd people
```

<span data-ttu-id="4ef6d-152">Komutu izleyen yol `cd` büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-152">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="4ef6d-153">Aşağıdaki çıkış biçimi görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-153">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="4ef6d-154">HTTP REPL 'ı özelleştirme</span><span class="sxs-lookup"><span data-stu-id="4ef6d-154">Customize the HTTP REPL</span></span>

<span data-ttu-id="4ef6d-155">HTTP REPL 'un varsayılan [renkleri](#set-color-preferences) özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-155">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="4ef6d-156">Ayrıca, [varsayılan bir metin Düzenleyicisi](#set-the-default-text-editor) tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-156">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="4ef6d-157">HTTP REPL tercihleri geçerli oturum genelinde kalıcı hale getirilir ve gelecekteki oturumlarda kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-157">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="4ef6d-158">Değiştirildikten sonra, Tercihler aşağıdaki dosyada depolanır:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-158">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linux"></a>[<span data-ttu-id="4ef6d-159">Linux</span><span class="sxs-lookup"><span data-stu-id="4ef6d-159">Linux</span></span>](#tab/linux)

<span data-ttu-id="4ef6d-160">*% GIRIŞ%/. httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="4ef6d-160">*%HOME%/.httpreplprefs*</span></span>

# <a name="macos"></a>[<span data-ttu-id="4ef6d-161">macOS</span><span class="sxs-lookup"><span data-stu-id="4ef6d-161">macOS</span></span>](#tab/macos)

<span data-ttu-id="4ef6d-162">*% GIRIŞ%/. httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="4ef6d-162">*%HOME%/.httpreplprefs*</span></span>

# <a name="windows"></a>[<span data-ttu-id="4ef6d-163">Windows</span><span class="sxs-lookup"><span data-stu-id="4ef6d-163">Windows</span></span>](#tab/windows)

<span data-ttu-id="4ef6d-164">*% USERPROFILE% \\ . httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="4ef6d-164">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="4ef6d-165">*. Httpreplprefs* dosyası başlangıçta yüklendi ve çalışma zamanında değişiklikler için izlenmiyor.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-165">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="4ef6d-166">Dosyada el ile yapılan değişiklikler yalnızca araç yeniden başlatıldıktan sonra devreye girer.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-166">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="4ef6d-167">Ayarları görüntüleyin</span><span class="sxs-lookup"><span data-stu-id="4ef6d-167">View the settings</span></span>

<span data-ttu-id="4ef6d-168">Kullanılabilir ayarları görüntülemek için `pref get` komutunu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-168">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="4ef6d-169">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-169">For example:</span></span>

```console
https://localhost:5001/~ pref get
```

<span data-ttu-id="4ef6d-170">Yukarıdaki komut, kullanılabilir anahtar-değer çiftlerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-170">The preceding command displays the available key-value pairs:</span></span>

```console
colors.json=Green
colors.json.arrayBrace=BoldCyan
colors.json.comma=BoldYellow
colors.json.name=BoldMagenta
colors.json.nameSeparator=BoldWhite
colors.json.objectBrace=Cyan
colors.protocol=BoldGreen
colors.status=BoldYellow
```

### <a name="set-color-preferences"></a><span data-ttu-id="4ef6d-171">Renk tercihlerini ayarla</span><span class="sxs-lookup"><span data-stu-id="4ef6d-171">Set color preferences</span></span>

<span data-ttu-id="4ef6d-172">Yanıt renklendirme Şu anda yalnızca JSON için destekleniyor.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-172">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="4ef6d-173">Varsayılan HTTP REPL aracı renklendirmesini özelleştirmek için, değiştirilecek renge karşılık gelen anahtarı bulun.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-173">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="4ef6d-174">Anahtarları bulma hakkında yönergeler için bkz. [ayarları görüntüleme](#view-the-settings) bölümü.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-174">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="4ef6d-175">Örneğin, `colors.json` anahtar değerini şu şekilde olacak şekilde `Green` değiştirin `White` :</span><span class="sxs-lookup"><span data-stu-id="4ef6d-175">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people~ pref set colors.json White
```

<span data-ttu-id="4ef6d-176">Yalnızca [izin verilen renkler](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-176">Only the [allowed colors](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="4ef6d-177">Sonraki HTTP istekleri, yeni renklendirmesi ile çıktıyı görüntüler.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-177">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="4ef6d-178">Belirli renk anahtarları ayarlanmamışsa, daha genel anahtarlar kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-178">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="4ef6d-179">Bu geri dönüş davranışını göstermek için aşağıdaki örneği göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-179">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="4ef6d-180">Eğer `colors.json.name` bir değeri yoksa, `colors.json.string` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-180">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="4ef6d-181">Eğer `colors.json.string` bir değeri yoksa, `colors.json.literal` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-181">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="4ef6d-182">Eğer `colors.json.literal` bir değeri yoksa, `colors.json` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-182">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="4ef6d-183">`colors.json`Bir değere sahip değilse, komut kabuğun varsayılan metin rengi ( `AllowedColors.None` ) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-183">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="4ef6d-184">Girinti boyutunu ayarla</span><span class="sxs-lookup"><span data-stu-id="4ef6d-184">Set indentation size</span></span>

<span data-ttu-id="4ef6d-185">Yanıt girintileme boyut özelleştirmesi Şu anda yalnızca JSON için destekleniyor.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-185">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="4ef6d-186">Varsayılan boyut iki boşluklardan oluşamaz.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-186">The default size is two spaces.</span></span> <span data-ttu-id="4ef6d-187">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-187">For example:</span></span>

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

<span data-ttu-id="4ef6d-188">Varsayılan boyutu değiştirmek için `formatting.json.indentSize` anahtarı ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-188">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="4ef6d-189">Örneğin, her zaman dört boşluk kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-189">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="4ef6d-190">Sonraki yanıtlar dört boşluk ayarına uyar:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-190">Subsequent responses honor the setting of four spaces:</span></span>

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-the-default-text-editor"></a><span data-ttu-id="4ef6d-191">Varsayılan metin düzenleyiciyi ayarlama</span><span class="sxs-lookup"><span data-stu-id="4ef6d-191">Set the default text editor</span></span>

<span data-ttu-id="4ef6d-192">Varsayılan olarak, HTTP REPL 'un kullanılmak üzere yapılandırılmış metin Düzenleyicisi yok.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-192">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="4ef6d-193">HTTP istek gövdesi gerektiren Web API yöntemlerini test etmek için varsayılan metin Düzenleyicisi ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-193">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="4ef6d-194">HTTP REPL Aracı, istek gövdesini oluşturma amacıyla yapılandırılmış metin düzenleyicisini başlatır.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-194">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="4ef6d-195">Tercih ettiğiniz metin düzenleyiciyi varsayılan olarak ayarlamak için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-195">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="4ef6d-196">Yukarıdaki komutta, `<EXECUTABLE>` Metin düzenleyicisinin yürütülebilir dosyasının tam yoludur.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-196">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="4ef6d-197">Örneğin, Visual Studio Code varsayılan metin düzenleyicisi olarak ayarlamak için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-197">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linux"></a>[<span data-ttu-id="4ef6d-198">Linux</span><span class="sxs-lookup"><span data-stu-id="4ef6d-198">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[<span data-ttu-id="4ef6d-199">macOS</span><span class="sxs-lookup"><span data-stu-id="4ef6d-199">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[<span data-ttu-id="4ef6d-200">Windows</span><span class="sxs-lookup"><span data-stu-id="4ef6d-200">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="4ef6d-201">Varsayılan metin düzenleyiciyi belirli CLı bağımsız değişkenleriyle başlatmak için `editor.command.default.arguments` anahtarı ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-201">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="4ef6d-202">Örneğin, Visual Studio Code varsayılan metin Düzenleyicisi olduğunu ve her zaman HTTP REPL 'un uzantıları devre dışı bırakılmış yeni bir oturumda Visual Studio Code açmasını istediğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-202">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="4ef6d-203">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-203">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

### <a name="set-the-swagger-search-paths"></a><span data-ttu-id="4ef6d-204">Swagger arama yollarını ayarla</span><span class="sxs-lookup"><span data-stu-id="4ef6d-204">Set the Swagger search paths</span></span>

<span data-ttu-id="4ef6d-205">Varsayılan olarak, HTTP REPL, komutu seçeneği olmadan yürütürken Swagger belgesini bulmak için kullandığı bir göreli yollar kümesine sahiptir `connect` `--swagger` .</span><span class="sxs-lookup"><span data-stu-id="4ef6d-205">By default, the HTTP REPL has a set of relative paths that it uses to find the Swagger document when executing the `connect` command without the `--swagger` option.</span></span> <span data-ttu-id="4ef6d-206">Bu göreli yollar, komutta belirtilen kök ve taban yollarla birleştirilir `connect` .</span><span class="sxs-lookup"><span data-stu-id="4ef6d-206">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="4ef6d-207">Varsayılan göreli yollar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-207">The default relative paths are:</span></span>

- <span data-ttu-id="4ef6d-208">\* Üzerindeswagger.js\*</span><span class="sxs-lookup"><span data-stu-id="4ef6d-208">*swagger.json*</span></span>
- <span data-ttu-id="4ef6d-209">*Swagger/v1/swagger.jsaçık*</span><span class="sxs-lookup"><span data-stu-id="4ef6d-209">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="4ef6d-210">*/swagger.js*</span><span class="sxs-lookup"><span data-stu-id="4ef6d-210">*/swagger.json*</span></span>
- <span data-ttu-id="4ef6d-211">*/Swagger/v1/swagger.js*</span><span class="sxs-lookup"><span data-stu-id="4ef6d-211">*/swagger/v1/swagger.json*</span></span>

<span data-ttu-id="4ef6d-212">Ortamınızda farklı bir arama yolları kümesi kullanmak için `swagger.searchPaths` tercihi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-212">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="4ef6d-213">Değer, göreli yolların kanal ile ayrılmış bir listesi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-213">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="4ef6d-214">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-214">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="4ef6d-215">HTTP GET isteklerini test etme</span><span class="sxs-lookup"><span data-stu-id="4ef6d-215">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="4ef6d-216">Özeti</span><span class="sxs-lookup"><span data-stu-id="4ef6d-216">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="4ef6d-217">Arguments</span><span class="sxs-lookup"><span data-stu-id="4ef6d-217">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="4ef6d-218">Varsa, ilişkili denetleyici eylem yöntemi tarafından beklenen rota parametresi.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-218">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="4ef6d-219">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="4ef6d-219">Options</span></span>

<span data-ttu-id="4ef6d-220">Komutu için aşağıdaki seçenekler kullanılabilir `get` :</span><span class="sxs-lookup"><span data-stu-id="4ef6d-220">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="4ef6d-221">Örnek</span><span class="sxs-lookup"><span data-stu-id="4ef6d-221">Example</span></span>

<span data-ttu-id="4ef6d-222">HTTP GET isteği vermek için:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-222">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="4ef6d-223">`get`Komutu onu destekleyen bir uç noktada çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-223">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ get
    ```

    <span data-ttu-id="4ef6d-224">Yukarıdaki komut aşağıdaki çıkış biçimini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-224">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 03:38:45 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "name": "Scott Hunter"
      },
      {
        "id": 2,
        "name": "Scott Hanselman"
      },
      {
        "id": 3,
        "name": "Scott Guthrie"
      }
    ]


    https://localhost:5001/people~
    ```

1. <span data-ttu-id="4ef6d-225">Komutuna bir parametre geçirerek belirli bir kaydı alın `get` :</span><span class="sxs-lookup"><span data-stu-id="4ef6d-225">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people~ get 2
    ```

    <span data-ttu-id="4ef6d-226">Yukarıdaki komut aşağıdaki çıkış biçimini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-226">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 06:17:57 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 2,
        "name": "Scott Hanselman"
      }
    ]


    https://localhost:5001/people~
    ```

## <a name="test-http-post-requests"></a><span data-ttu-id="4ef6d-227">HTTP POST isteklerini test et</span><span class="sxs-lookup"><span data-stu-id="4ef6d-227">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="4ef6d-228">Özeti</span><span class="sxs-lookup"><span data-stu-id="4ef6d-228">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="4ef6d-229">Arguments</span><span class="sxs-lookup"><span data-stu-id="4ef6d-229">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="4ef6d-230">Varsa, ilişkili denetleyici eylem yöntemi tarafından beklenen rota parametresi.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-230">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="4ef6d-231">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="4ef6d-231">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="4ef6d-232">Örnek</span><span class="sxs-lookup"><span data-stu-id="4ef6d-232">Example</span></span>

<span data-ttu-id="4ef6d-233">HTTP POST isteği vermek için:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-233">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="4ef6d-234">`post`Komutu onu destekleyen bir uç noktada çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-234">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    <span data-ttu-id="4ef6d-235">Önceki komutta, `Content-Type` http istek üst bilgisi, JSON türünde bir istek gövdesi medya türünü gösterecek şekilde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-235">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="4ef6d-236">Varsayılan metin Düzenleyicisi, HTTP istek gövdesini temsil eden bir JSON şablonuyla bir *. tmp* dosyası açar.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-236">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="4ef6d-237">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-237">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="4ef6d-238">Varsayılan metin düzenleyicisini ayarlamak için [varsayılan metin düzenleyiciyi ayarlama](#set-the-default-text-editor) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-238">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="4ef6d-239">JSON şablonunu model doğrulama gereksinimlerini karşılayacak şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-239">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="4ef6d-240">*. Tmp* dosyasını kaydedin ve metin düzenleyicisini kapatın.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-240">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="4ef6d-241">Aşağıdaki çıktı komut kabuğu 'nda görünür:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-241">The following output appears in the command shell:</span></span>

    ```console
    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Date: Thu, 27 Jun 2019 21:24:18 GMT
    Location: https://localhost:5001/people/4
    Server: Kestrel
    Transfer-Encoding: chunked

    {
      "id": 4,
      "name": "Scott Addie"
    }


    https://localhost:5001/people~
    ```

## <a name="test-http-put-requests"></a><span data-ttu-id="4ef6d-242">HTTP PUT isteklerini test etme</span><span class="sxs-lookup"><span data-stu-id="4ef6d-242">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="4ef6d-243">Özeti</span><span class="sxs-lookup"><span data-stu-id="4ef6d-243">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="4ef6d-244">Arguments</span><span class="sxs-lookup"><span data-stu-id="4ef6d-244">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="4ef6d-245">Varsa, ilişkili denetleyici eylem yöntemi tarafından beklenen rota parametresi.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-245">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="4ef6d-246">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="4ef6d-246">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="4ef6d-247">Örnek</span><span class="sxs-lookup"><span data-stu-id="4ef6d-247">Example</span></span>

<span data-ttu-id="4ef6d-248">HTTP PUT isteği vermek için:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-248">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="4ef6d-249">*Isteğe bağlı*: `get` verileri değiştirmeden önce görüntülemek için komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-249">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. <span data-ttu-id="4ef6d-250">`put`Komutu onu destekleyen bir uç noktada çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-250">Run the `put` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits~ put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="4ef6d-251">Önceki komutta, `Content-Type` http istek üst bilgisi, JSON türünde bir istek gövdesi medya türünü gösterecek şekilde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-251">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="4ef6d-252">Varsayılan metin Düzenleyicisi, HTTP istek gövdesini temsil eden bir JSON şablonuyla bir *. tmp* dosyası açar.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-252">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="4ef6d-253">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-253">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="4ef6d-254">Varsayılan metin düzenleyicisini ayarlamak için [varsayılan metin düzenleyiciyi ayarlama](#set-the-default-text-editor) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-254">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="4ef6d-255">JSON şablonunu model doğrulama gereksinimlerini karşılayacak şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-255">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="4ef6d-256">*. Tmp* dosyasını kaydedin ve metin düzenleyicisini kapatın.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-256">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="4ef6d-257">Aşağıdaki çıktı komut kabuğu 'nda görünür:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-257">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="4ef6d-258">*Isteğe bağlı*: `get` değişiklikleri görmek için bir komut verin.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-258">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="4ef6d-259">Örneğin, metin düzenleyicisinde "Chraz" yazdıysanız, bir, `get` şunu döndürür:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-259">For example, if you typed "Cherry" in the text editor, a `get` returns the following:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:08:20 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Cherry"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-delete-requests"></a><span data-ttu-id="4ef6d-260">HTTP SILME isteklerini test etme</span><span class="sxs-lookup"><span data-stu-id="4ef6d-260">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="4ef6d-261">Özeti</span><span class="sxs-lookup"><span data-stu-id="4ef6d-261">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="4ef6d-262">Arguments</span><span class="sxs-lookup"><span data-stu-id="4ef6d-262">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="4ef6d-263">Varsa, ilişkili denetleyici eylem yöntemi tarafından beklenen rota parametresi.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-263">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="4ef6d-264">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="4ef6d-264">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="4ef6d-265">Örnek</span><span class="sxs-lookup"><span data-stu-id="4ef6d-265">Example</span></span>

<span data-ttu-id="4ef6d-266">HTTP SILME isteği vermek için:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-266">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="4ef6d-267">*Isteğe bağlı*: `get` verileri değiştirmeden önce görüntülemek için komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-267">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. <span data-ttu-id="4ef6d-268">`delete`Komutu onu destekleyen bir uç noktada çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-268">Run the `delete` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits~ delete 2
    ```

    <span data-ttu-id="4ef6d-269">Yukarıdaki komut aşağıdaki çıkış biçimini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-269">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="4ef6d-270">*Isteğe bağlı*: `get` değişiklikleri görmek için bir komut verin.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-270">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="4ef6d-271">Bu örnekte, bir, `get` şunu döndürür:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-271">In this example, a `get` returns the following:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:16:30 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-patch-requests"></a><span data-ttu-id="4ef6d-272">HTTP PATCH isteklerini test etme</span><span class="sxs-lookup"><span data-stu-id="4ef6d-272">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="4ef6d-273">Özeti</span><span class="sxs-lookup"><span data-stu-id="4ef6d-273">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="4ef6d-274">Arguments</span><span class="sxs-lookup"><span data-stu-id="4ef6d-274">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="4ef6d-275">Varsa, ilişkili denetleyici eylem yöntemi tarafından beklenen rota parametresi.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-275">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="4ef6d-276">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="4ef6d-276">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="4ef6d-277">HTTP HEAD isteklerini test etme</span><span class="sxs-lookup"><span data-stu-id="4ef6d-277">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="4ef6d-278">Özeti</span><span class="sxs-lookup"><span data-stu-id="4ef6d-278">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="4ef6d-279">Arguments</span><span class="sxs-lookup"><span data-stu-id="4ef6d-279">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="4ef6d-280">Varsa, ilişkili denetleyici eylem yöntemi tarafından beklenen rota parametresi.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-280">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="4ef6d-281">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="4ef6d-281">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="4ef6d-282">Sınama HTTP SEÇENEKLERI istekleri</span><span class="sxs-lookup"><span data-stu-id="4ef6d-282">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="4ef6d-283">Özeti</span><span class="sxs-lookup"><span data-stu-id="4ef6d-283">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="4ef6d-284">Arguments</span><span class="sxs-lookup"><span data-stu-id="4ef6d-284">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="4ef6d-285">Varsa, ilişkili denetleyici eylem yöntemi tarafından beklenen rota parametresi.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-285">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="4ef6d-286">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="4ef6d-286">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="4ef6d-287">HTTP istek üst bilgilerini ayarla</span><span class="sxs-lookup"><span data-stu-id="4ef6d-287">Set HTTP request headers</span></span>

<span data-ttu-id="4ef6d-288">Bir HTTP istek üst bilgisi ayarlamak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-288">To set an HTTP request header, use one of the following approaches:</span></span>

* <span data-ttu-id="4ef6d-289">HTTP isteğiyle satır içi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-289">Set inline with the HTTP request.</span></span> <span data-ttu-id="4ef6d-290">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-290">For example:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```
    
    <span data-ttu-id="4ef6d-291">Önceki yaklaşımla, her ayrı HTTP istek üst bilgisi kendi seçeneğini gerektirir `-h` .</span><span class="sxs-lookup"><span data-stu-id="4ef6d-291">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

* <span data-ttu-id="4ef6d-292">HTTP isteğini göndermeden önce ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-292">Set before sending the HTTP request.</span></span> <span data-ttu-id="4ef6d-293">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-293">For example:</span></span>

    ```console
    https://localhost:5001/people~ set header Content-Type application/json
    ```
    
    <span data-ttu-id="4ef6d-294">Bir isteği göndermeden önce üst bilgi ayarlanırken üst bilgi, komut kabuğu oturumunun süresi boyunca ayarlanmış olarak kalır.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-294">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="4ef6d-295">Üstbilgiyi temizlemek için boş bir değer sağlayın.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-295">To clear the header, provide an empty value.</span></span> <span data-ttu-id="4ef6d-296">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-296">For example:</span></span>
    
    ```console
    https://localhost:5001/people~ set header Content-Type
    ```

## <a name="test-secured-endpoints"></a><span data-ttu-id="4ef6d-297">Güvenli uç noktaları test et</span><span class="sxs-lookup"><span data-stu-id="4ef6d-297">Test secured endpoints</span></span>

<span data-ttu-id="4ef6d-298">HTTP REPL, güvenli uç noktaların sınamasını iki şekilde destekler: oturum açan kullanıcının varsayılan kimlik bilgileri veya HTTP istek üst bilgilerinin kullanımı aracılığıyla.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-298">The HTTP REPL supports the testing of secured endpoints in two ways: via the default credentials of the logged in user or through the use of HTTP request headers.</span></span> 

### <a name="default-credentials"></a><span data-ttu-id="4ef6d-299">Varsayılan kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="4ef6d-299">Default credentials</span></span>

<span data-ttu-id="4ef6d-300">Test ettiğiniz Web API 'sinin IIS 'de barındırıldığı ve Windows kimlik doğrulaması ile güvenliği sağlanan bir senaryoyu düşünün.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-300">Consider a scenario in which the web API you're testing is hosted in IIS and is secured with Windows authentication.</span></span> <span data-ttu-id="4ef6d-301">Aracı çalıştıran kullanıcının kimlik bilgilerinin, test edilmekte olan HTTP uç noktaları üzerinden akmasını istiyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-301">You want the credentials of the user running the tool to flow across to the HTTP endpoints being tested.</span></span> <span data-ttu-id="4ef6d-302">Oturum açmış kullanıcının varsayılan kimlik bilgilerini geçirmek için:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-302">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="4ef6d-303">Tercihi şu `httpClient.useDefaultCredentials` şekilde ayarlayın `true` :</span><span class="sxs-lookup"><span data-stu-id="4ef6d-303">Set the `httpClient.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. <span data-ttu-id="4ef6d-304">Web API 'sine başka bir istek göndermeden önce çıkın ve aracı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-304">Exit and restart the tool before sending another request to the web API.</span></span>

### <a name="http-request-headers"></a><span data-ttu-id="4ef6d-305">HTTP istek üstbilgileri</span><span class="sxs-lookup"><span data-stu-id="4ef6d-305">HTTP request headers</span></span>

<span data-ttu-id="4ef6d-306">Desteklenen kimlik doğrulama ve yetkilendirme düzenlerine örnek olarak temel kimlik doğrulaması, JWT taşıyıcı belirteçleri ve Özet kimlik doğrulaması verilebilir.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-306">Examples of supported authentication and authorization schemes include basic authentication, JWT bearer tokens, and digest authentication.</span></span> <span data-ttu-id="4ef6d-307">Örneğin, aşağıdaki komutla bir uç noktaya bir taşıyıcı belirteci gönderebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-307">For example, you can send a bearer token to an endpoint with the following command:</span></span>

```console
set header Authorization "bearer <TOKEN VALUE>"
```

<span data-ttu-id="4ef6d-308">Azure 'da barındırılan bir uç noktaya erişmek veya [azure REST API](/rest/api/azure/)kullanmak için bir taşıyıcı belirtecine ihtiyacınız vardır.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-308">To access an Azure-hosted endpoint or to use the [Azure REST API](/rest/api/azure/), you need a bearer token.</span></span> <span data-ttu-id="4ef6d-309">Azure [CLI](/cli/azure/)aracılığıyla Azure Aboneliğinize yönelik bir taşıyıcı belirteci almak için aşağıdaki adımları kullanın.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-309">Use the following steps to obtain a bearer token for your Azure subscription via the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="4ef6d-310">HTTP REPL, bir HTTP istek üstbilgisindeki taşıyıcı belirtecini ayarlar ve Azure App Service Web Apps listesini alır.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-310">The HTTP REPL sets the bearer token in an HTTP request header and retrieves a list of Azure App Service Web Apps.</span></span>

1. <span data-ttu-id="4ef6d-311">Azure 'da oturum açın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-311">Log in to Azure:</span></span>

    ```azurecli
    az login
    ```

1. <span data-ttu-id="4ef6d-312">Aşağıdaki komutla abonelik KIMLIĞINIZI alın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-312">Get your subscription ID with the following command:</span></span>

    ```azurecli
    az account show --query id
    ```

1. <span data-ttu-id="4ef6d-313">Abonelik KIMLIĞINIZI kopyalayın ve şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-313">Copy your subscription ID and run the following command:</span></span>

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. <span data-ttu-id="4ef6d-314">Aşağıdaki komutla taşıyıcı belirtecinizi alın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-314">Get your bearer token with the following command:</span></span>

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. <span data-ttu-id="4ef6d-315">HTTP REPL aracılığıyla Azure REST API bağlanma:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-315">Connect to the Azure REST API via the HTTP REPL:</span></span>

    ```console
    httprepl https://management.azure.com
    ```

1. <span data-ttu-id="4ef6d-316">`Authorization`Http istek üst bilgisini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-316">Set the `Authorization` HTTP request header:</span></span>

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. <span data-ttu-id="4ef6d-317">Aboneliğe gidin:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-317">Navigate to the subscription:</span></span>

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. <span data-ttu-id="4ef6d-318">Aboneliğinizin Azure App Service Web Apps bir listesini alın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-318">Get a list of your subscription's Azure App Service Web Apps:</span></span>

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    <span data-ttu-id="4ef6d-319">Aşağıdaki yanıt görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-319">The following response is displayed:</span></span>

    ```console
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 35948
    Content-Type: application/json; charset=utf-8
    Date: Thu, 19 Sep 2019 23:04:03 GMT
    Expires: -1
    Pragma: no-cache
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    X-Content-Type-Options: nosniff
    x-ms-correlation-request-id: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-original-request-ids: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx;xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-ratelimit-remaining-subscription-reads: 11999
    x-ms-request-id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    x-ms-routing-request-id: WESTUS:xxxxxxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx
    {
      "value": [
        <AZURE RESOURCES LIST>
      ]
    }
    ```

## <a name="toggle-http-request-display"></a><span data-ttu-id="4ef6d-320">HTTP istek görüntüsünü değiştirme</span><span class="sxs-lookup"><span data-stu-id="4ef6d-320">Toggle HTTP request display</span></span>

<span data-ttu-id="4ef6d-321">Varsayılan olarak, gönderilmekte olan HTTP isteğinin görüntüsü bastırılır.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-321">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="4ef6d-322">Komut kabuğu oturumunun süresi boyunca ilgili ayarı değiştirmek mümkündür.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-322">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="4ef6d-323">İstek görüntülemesini etkinleştir</span><span class="sxs-lookup"><span data-stu-id="4ef6d-323">Enable request display</span></span>

<span data-ttu-id="4ef6d-324">Komutunu çalıştırarak gönderilmekte olan HTTP isteğini görüntüleyin `echo on` .</span><span class="sxs-lookup"><span data-stu-id="4ef6d-324">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="4ef6d-325">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-325">For example:</span></span>

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

<span data-ttu-id="4ef6d-326">Geçerli oturumdaki sonraki HTTP istekleri, istek üst bilgilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-326">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="4ef6d-327">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-327">For example:</span></span>

```console
https://localhost:5001/people~ post

[main 2019-06-28T18:50:11.930Z] update#setState idle
Request to https://localhost:5001...

POST /people HTTP/1.1
Content-Length: 41
Content-Type: application/json
User-Agent: HTTP-REPL

{
  "id": 0,
  "name": "Scott Addie"
}

Response from https://localhost:5001...

HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Fri, 28 Jun 2019 18:50:21 GMT
Location: https://localhost:5001/people/4
Server: Kestrel
Transfer-Encoding: chunked

{
  "id": 4,
  "name": "Scott Addie"
}


https://localhost:5001/people~
```

### <a name="disable-request-display"></a><span data-ttu-id="4ef6d-328">İstek görüntüsünü devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="4ef6d-328">Disable request display</span></span>

<span data-ttu-id="4ef6d-329">Komutunu çalıştırarak gönderilen HTTP isteğinin görüntülenmesini gizleyin `echo off` .</span><span class="sxs-lookup"><span data-stu-id="4ef6d-329">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="4ef6d-330">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-330">For example:</span></span>

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="4ef6d-331">Betik çalıştırma</span><span class="sxs-lookup"><span data-stu-id="4ef6d-331">Run a script</span></span>

<span data-ttu-id="4ef6d-332">Aynı HTTP REPL komutları kümesini sıklıkla yürütüyorsanız bunları bir metin dosyasında depolamayı göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-332">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="4ef6d-333">Dosyadaki komutlar, komut satırında el ile çalıştıranlarla aynı formu alır.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-333">Commands in the file take the same form as those executed manually on the command line.</span></span> <span data-ttu-id="4ef6d-334">Komutlar, komutu kullanılarak toplanmış bir biçimde yürütülebilir `run` .</span><span class="sxs-lookup"><span data-stu-id="4ef6d-334">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="4ef6d-335">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-335">For example:</span></span>

1. <span data-ttu-id="4ef6d-336">Yeni satır için ayrılmış komutlar kümesini içeren bir metin dosyası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-336">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="4ef6d-337">Göstermek için aşağıdaki komutları içeren bir *people-script.txt* dosyası göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-337">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="4ef6d-338">`run`Metin dosyasının yolunu geçirerek komutunu yürütün.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-338">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="4ef6d-339">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-339">For example:</span></span>

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="4ef6d-340">Aşağıdaki çıkış görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-340">The following output appears:</span></span>

    ```console
    https://localhost:5001/~ set base https://localhost:5001
    Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/~ ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/~ cd People
    /People    [get|post]
    
    https://localhost:5001/People~ ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People~ get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People~
    ```

## <a name="clear-the-output"></a><span data-ttu-id="4ef6d-341">Çıktıyı temizle</span><span class="sxs-lookup"><span data-stu-id="4ef6d-341">Clear the output</span></span>

<span data-ttu-id="4ef6d-342">HTTP REPL aracı tarafından komut kabuğu 'na yazılan tüm çıktıyı kaldırmak için `clear` veya `cls` komutunu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="4ef6d-342">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="4ef6d-343">Göstermek için komut kabuğu 'nun aşağıdaki çıktıyı içerdiğini düşünün:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-343">To illustrate, imagine the command shell contains the following output:</span></span>

```console
httprepl https://localhost:5001
(Disconnected)~ set base "https://localhost:5001"
Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/~ ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="4ef6d-344">Çıktıyı temizlemek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-344">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/~ clear
```

<span data-ttu-id="4ef6d-345">Yukarıdaki komutu çalıştırdıktan sonra, komut kabuğu yalnızca şu çıktıyı içerir:</span><span class="sxs-lookup"><span data-stu-id="4ef6d-345">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a><span data-ttu-id="4ef6d-346">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="4ef6d-346">Additional resources</span></span>

* [<span data-ttu-id="4ef6d-347">REST API istekleri</span><span class="sxs-lookup"><span data-stu-id="4ef6d-347">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="4ef6d-348">HTTP REPL GitHub deposu</span><span class="sxs-lookup"><span data-stu-id="4ef6d-348">HTTP REPL GitHub repository</span></span>](https://github.com/dotnet/HttpRepl)
