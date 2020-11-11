---
title: HTTP REPL ile Web API 'Lerini test etme
author: scottaddie
description: HTTP REPL .NET Core küresel aracının bir ASP.NET Core Web API 'sini taramak ve test etmek için nasıl kullanılacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, devx-track-azurecli
ms.date: 11/10/2020
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
uid: web-api/http-repl
ms.openlocfilehash: 81174b551c5b6d81e6ac80975f7f77ee6664059d
ms.sourcegitcommit: fb72e9c1ae5b279817f1fb4b46a52170449b6f30
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94502045"
---
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="b9984-103">HTTP REPL ile Web API 'Lerini test etme</span><span class="sxs-lookup"><span data-stu-id="b9984-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="b9984-104">[Scott Ade](https://twitter.com/Scott_Addie) tarafından</span><span class="sxs-lookup"><span data-stu-id="b9984-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="b9984-105">HTTP okuma-değerlendirme-yazdırma döngüsü (REPL):</span><span class="sxs-lookup"><span data-stu-id="b9984-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="b9984-106">.NET Core 'un her yerde desteklenen basit, platformlar arası bir komut satırı aracı desteklenir.</span><span class="sxs-lookup"><span data-stu-id="b9984-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="b9984-107">ASP.NET Core Web API 'Lerini (ve non-ASP.NET çekirdek Web API 'Leri) test etmek ve sonuçlarını görüntülemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b9984-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="b9984-108">Localhost ve Azure App Service dahil olmak üzere herhangi bir ortamda barındırılan Web API 'Lerini test etme özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="b9984-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="b9984-109">Aşağıdaki [http fiilleri](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) desteklenir:</span><span class="sxs-lookup"><span data-stu-id="b9984-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="b9984-110">SILMELI</span><span class="sxs-lookup"><span data-stu-id="b9984-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="b9984-111">GET</span><span class="sxs-lookup"><span data-stu-id="b9984-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="b9984-112">BAŞLı</span><span class="sxs-lookup"><span data-stu-id="b9984-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="b9984-113">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="b9984-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="b9984-114">DÜZELTMESI</span><span class="sxs-lookup"><span data-stu-id="b9984-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="b9984-115">POST</span><span class="sxs-lookup"><span data-stu-id="b9984-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="b9984-116">PUT</span><span class="sxs-lookup"><span data-stu-id="b9984-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="b9984-117">Takip etmek için, [örnek ASP.NET Core Web API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) 'sini ([indirme](xref:index#how-to-download-a-sample)) görüntüleyin veya indirin.</span><span class="sxs-lookup"><span data-stu-id="b9984-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b9984-118">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="b9984-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="b9984-119">Yükleme</span><span class="sxs-lookup"><span data-stu-id="b9984-119">Installation</span></span>

<span data-ttu-id="b9984-120">HTTP REPL 'u yüklemek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9984-120">To install the HTTP REPL, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

<span data-ttu-id="b9984-121">[.NET Core küresel aracı](/dotnet/core/tools/global-tools#install-a-global-tool) , [Microsoft. DotNet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet paketinden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="b9984-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="b9984-122">Kullanım</span><span class="sxs-lookup"><span data-stu-id="b9984-122">Usage</span></span>

<span data-ttu-id="b9984-123">Aracın başarıyla yüklenmesinden sonra, HTTP REPL 'u başlatmak için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9984-123">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
httprepl
```

<span data-ttu-id="b9984-124">Kullanılabilir HTTP REPL komutlarını görüntülemek için aşağıdaki komutlardan birini çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9984-124">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
httprepl -h
```

```console
httprepl --help
```

<span data-ttu-id="b9984-125">Aşağıdaki çıktı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="b9984-125">The following output is displayed:</span></span>

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

<span data-ttu-id="b9984-126">HTTP REPL komut tamamlama sağlar.</span><span class="sxs-lookup"><span data-stu-id="b9984-126">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="b9984-127"><kbd>Sekme</kbd> tuşuna basıldığında, yazdığınız KARAKTERLERI veya API uç noktasını tamamlayacak komutların listesi üzerinden yinelenir.</span><span class="sxs-lookup"><span data-stu-id="b9984-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="b9984-128">Aşağıdaki bölümlerde kullanılabilir CLı komutları ana hatlarıyla verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="b9984-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="b9984-129">Web API 'sine bağlanma</span><span class="sxs-lookup"><span data-stu-id="b9984-129">Connect to the web API</span></span>

<span data-ttu-id="b9984-130">Aşağıdaki komutu çalıştırarak bir Web API 'sine bağlanın:</span><span class="sxs-lookup"><span data-stu-id="b9984-130">Connect to a web API by running the following command:</span></span>

```console
httprepl <ROOT URI>
```

<span data-ttu-id="b9984-131">`<ROOT URI>` , Web API 'sinin temel URI 'sidir.</span><span class="sxs-lookup"><span data-stu-id="b9984-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="b9984-132">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b9984-132">For example:</span></span>

```console
httprepl https://localhost:5001
```

<span data-ttu-id="b9984-133">Alternatif olarak, HTTP REPL çalışırken istediğiniz zaman aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9984-133">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="b9984-134">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b9984-134">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001
```

### <a name="manually-point-to-the-openapi-description-for-the-web-api"></a><span data-ttu-id="b9984-135">Web API 'SI için Openapı açıklamasına el ile işaret edin</span><span class="sxs-lookup"><span data-stu-id="b9984-135">Manually point to the OpenAPI description for the web API</span></span>

<span data-ttu-id="b9984-136">Yukarıdaki Connect komutu, Openapı açıklamasını otomatik olarak bulmaya çalışacaktır.</span><span class="sxs-lookup"><span data-stu-id="b9984-136">The connect command above will attempt to find the OpenAPI description automatically.</span></span> <span data-ttu-id="b9984-137">Bir nedenden dolayı bunu yapamaması durumunda, Web API 'SI için Openapı açıklamasının URI 'sini belirterek `--openapi` seçeneğini kullanabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="b9984-137">If for some reason it's unable to do so, you can specify the URI of the OpenAPI description for the web API by using the `--openapi` option:</span></span>

```console
connect <ROOT URI> --openapi <OPENAPI DESCRIPTION ADDRESS>
```

<span data-ttu-id="b9984-138">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b9984-138">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --openapi /swagger/v1/swagger.json
```

### <a name="enable-verbose-output-for-details-on-openapi-description-searching-parsing-and-validation"></a><span data-ttu-id="b9984-139">Openapı açıklaması arama, ayrıştırma ve doğrulama hakkındaki ayrıntılar için ayrıntılı çıktıyı etkinleştirin</span><span class="sxs-lookup"><span data-stu-id="b9984-139">Enable verbose output for details on OpenAPI description searching, parsing, and validation</span></span>

<span data-ttu-id="b9984-140">`--verbose`Komut ile seçeneğinin belirtilmesi, `connect` araç openapı açıklamasını aradığında, ayrıştırdığında ve doğruladığı zaman daha fazla ayrıntı üretecektir.</span><span class="sxs-lookup"><span data-stu-id="b9984-140">Specifying the `--verbose` option with the `connect` command will produce more details when the tool searches for the OpenAPI description, parses, and validates it.</span></span>

```console
connect <ROOT URI> --verbose
```

<span data-ttu-id="b9984-141">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b9984-141">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --verbose
Checking https://localhost:5001/swagger.json... 404 NotFound
Checking https://localhost:5001/swagger/v1/swagger.json... 404 NotFound
Checking https://localhost:5001/openapi.json... Found
Parsing... Successful (with warnings)
The field 'info' in 'document' object is REQUIRED [#/info]
The field 'paths' in 'document' object is REQUIRED [#/paths]
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="b9984-142">Web API 'sinde gezin</span><span class="sxs-lookup"><span data-stu-id="b9984-142">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="b9984-143">Kullanılabilir uç noktaları görüntüle</span><span class="sxs-lookup"><span data-stu-id="b9984-143">View available endpoints</span></span>

<span data-ttu-id="b9984-144">Web API adresinin geçerli yolundaki farklı uç noktaları (denetleyiciler) listelemek için, `ls` veya `dir` komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9984-144">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/> ls
```

<span data-ttu-id="b9984-145">Aşağıdaki çıkış biçimi görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="b9984-145">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

<span data-ttu-id="b9984-146">Yukarıdaki çıkış, kullanılabilir iki denetleyici olduğunu gösterir: `Fruits` ve `People` .</span><span class="sxs-lookup"><span data-stu-id="b9984-146">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="b9984-147">Her iki denetleyici de parametresiz HTTP GET ve POST işlemlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="b9984-147">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="b9984-148">Belirli bir denetleyicide gezinmek daha ayrıntılı bilgi gösterir.</span><span class="sxs-lookup"><span data-stu-id="b9984-148">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="b9984-149">Örneğin, aşağıdaki komut çıktısı, `Fruits` denetleyiciyi de http get, put ve DELETE işlemlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="b9984-149">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="b9984-150">Bu işlemlerin her biri `id` , rotada bir parametre bekler:</span><span class="sxs-lookup"><span data-stu-id="b9984-150">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits> ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits>
```

<span data-ttu-id="b9984-151">Alternatif olarak, `ui` Web API 'Sinin Swagger Kullanıcı Arabirimi sayfasını bir tarayıcıda açmak için komutunu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b9984-151">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="b9984-152">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b9984-152">For example:</span></span>

```console
https://localhost:5001/> ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="b9984-153">Bir uç noktaya gitme</span><span class="sxs-lookup"><span data-stu-id="b9984-153">Navigate to an endpoint</span></span>

<span data-ttu-id="b9984-154">Web API 'sindeki farklı bir uç noktaya gitmek için `cd` komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9984-154">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/> cd people
```

<span data-ttu-id="b9984-155">Komutu izleyen yol `cd` büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="b9984-155">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="b9984-156">Aşağıdaki çıkış biçimi görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="b9984-156">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people>
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="b9984-157">HTTP REPL 'ı özelleştirme</span><span class="sxs-lookup"><span data-stu-id="b9984-157">Customize the HTTP REPL</span></span>

<span data-ttu-id="b9984-158">HTTP REPL 'un varsayılan [renkleri](#set-color-preferences) özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="b9984-158">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="b9984-159">Ayrıca, [varsayılan bir metin Düzenleyicisi](#set-the-default-text-editor) tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="b9984-159">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="b9984-160">HTTP REPL tercihleri geçerli oturum genelinde kalıcı hale getirilir ve gelecekteki oturumlarda kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="b9984-160">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="b9984-161">Değiştirildikten sonra, Tercihler aşağıdaki dosyada depolanır:</span><span class="sxs-lookup"><span data-stu-id="b9984-161">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linux"></a>[<span data-ttu-id="b9984-162">Linux</span><span class="sxs-lookup"><span data-stu-id="b9984-162">Linux</span></span>](#tab/linux)

<span data-ttu-id="b9984-163">*% GIRIŞ%/. httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="b9984-163">*%HOME%/.httpreplprefs*</span></span>

# <a name="macos"></a>[<span data-ttu-id="b9984-164">macOS</span><span class="sxs-lookup"><span data-stu-id="b9984-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="b9984-165">*% GIRIŞ%/. httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="b9984-165">*%HOME%/.httpreplprefs*</span></span>

# <a name="windows"></a>[<span data-ttu-id="b9984-166">Windows</span><span class="sxs-lookup"><span data-stu-id="b9984-166">Windows</span></span>](#tab/windows)

<span data-ttu-id="b9984-167">*% USERPROFILE% \\ . httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="b9984-167">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="b9984-168">*. Httpreplprefs* dosyası başlangıçta yüklendi ve çalışma zamanında değişiklikler için izlenmiyor.</span><span class="sxs-lookup"><span data-stu-id="b9984-168">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="b9984-169">Dosyada el ile yapılan değişiklikler yalnızca araç yeniden başlatıldıktan sonra devreye girer.</span><span class="sxs-lookup"><span data-stu-id="b9984-169">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="b9984-170">Ayarları görüntüleyin</span><span class="sxs-lookup"><span data-stu-id="b9984-170">View the settings</span></span>

<span data-ttu-id="b9984-171">Kullanılabilir ayarları görüntülemek için `pref get` komutunu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b9984-171">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="b9984-172">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b9984-172">For example:</span></span>

```console
https://localhost:5001/> pref get
```

<span data-ttu-id="b9984-173">Yukarıdaki komut, kullanılabilir anahtar-değer çiftlerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="b9984-173">The preceding command displays the available key-value pairs:</span></span>

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

### <a name="set-color-preferences"></a><span data-ttu-id="b9984-174">Renk tercihlerini ayarla</span><span class="sxs-lookup"><span data-stu-id="b9984-174">Set color preferences</span></span>

<span data-ttu-id="b9984-175">Yanıt renklendirme Şu anda yalnızca JSON için destekleniyor.</span><span class="sxs-lookup"><span data-stu-id="b9984-175">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="b9984-176">Varsayılan HTTP REPL aracı renklendirmesini özelleştirmek için, değiştirilecek renge karşılık gelen anahtarı bulun.</span><span class="sxs-lookup"><span data-stu-id="b9984-176">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="b9984-177">Anahtarları bulma hakkında yönergeler için bkz. [ayarları görüntüleme](#view-the-settings) bölümü.</span><span class="sxs-lookup"><span data-stu-id="b9984-177">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="b9984-178">Örneğin, `colors.json` anahtar değerini şu şekilde olacak şekilde `Green` değiştirin `White` :</span><span class="sxs-lookup"><span data-stu-id="b9984-178">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people> pref set colors.json White
```

<span data-ttu-id="b9984-179">Yalnızca [izin verilen renkler](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b9984-179">Only the [allowed colors](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="b9984-180">Sonraki HTTP istekleri, yeni renklendirmesi ile çıktıyı görüntüler.</span><span class="sxs-lookup"><span data-stu-id="b9984-180">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="b9984-181">Belirli renk anahtarları ayarlanmamışsa, daha genel anahtarlar kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="b9984-181">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="b9984-182">Bu geri dönüş davranışını göstermek için aşağıdaki örneği göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="b9984-182">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="b9984-183">Eğer `colors.json.name` bir değeri yoksa, `colors.json.string` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b9984-183">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="b9984-184">Eğer `colors.json.string` bir değeri yoksa, `colors.json.literal` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b9984-184">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="b9984-185">Eğer `colors.json.literal` bir değeri yoksa, `colors.json` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b9984-185">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="b9984-186">`colors.json`Bir değere sahip değilse, komut kabuğun varsayılan metin rengi ( `AllowedColors.None` ) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b9984-186">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="b9984-187">Girinti boyutunu ayarla</span><span class="sxs-lookup"><span data-stu-id="b9984-187">Set indentation size</span></span>

<span data-ttu-id="b9984-188">Yanıt girintileme boyut özelleştirmesi Şu anda yalnızca JSON için destekleniyor.</span><span class="sxs-lookup"><span data-stu-id="b9984-188">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="b9984-189">Varsayılan boyut iki boşluklardan oluşamaz.</span><span class="sxs-lookup"><span data-stu-id="b9984-189">The default size is two spaces.</span></span> <span data-ttu-id="b9984-190">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b9984-190">For example:</span></span>

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

<span data-ttu-id="b9984-191">Varsayılan boyutu değiştirmek için `formatting.json.indentSize` anahtarı ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b9984-191">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="b9984-192">Örneğin, her zaman dört boşluk kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="b9984-192">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="b9984-193">Sonraki yanıtlar dört boşluk ayarına uyar:</span><span class="sxs-lookup"><span data-stu-id="b9984-193">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-the-default-text-editor"></a><span data-ttu-id="b9984-194">Varsayılan metin düzenleyiciyi ayarlama</span><span class="sxs-lookup"><span data-stu-id="b9984-194">Set the default text editor</span></span>

<span data-ttu-id="b9984-195">Varsayılan olarak, HTTP REPL 'un kullanılmak üzere yapılandırılmış metin Düzenleyicisi yok.</span><span class="sxs-lookup"><span data-stu-id="b9984-195">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="b9984-196">HTTP istek gövdesi gerektiren Web API yöntemlerini test etmek için varsayılan metin Düzenleyicisi ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b9984-196">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="b9984-197">HTTP REPL Aracı, istek gövdesini oluşturma amacıyla yapılandırılmış metin düzenleyicisini başlatır.</span><span class="sxs-lookup"><span data-stu-id="b9984-197">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="b9984-198">Tercih ettiğiniz metin düzenleyiciyi varsayılan olarak ayarlamak için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9984-198">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="b9984-199">Yukarıdaki komutta, `<EXECUTABLE>` Metin düzenleyicisinin yürütülebilir dosyasının tam yoludur.</span><span class="sxs-lookup"><span data-stu-id="b9984-199">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="b9984-200">Örneğin, Visual Studio Code varsayılan metin düzenleyicisi olarak ayarlamak için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9984-200">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linux"></a>[<span data-ttu-id="b9984-201">Linux</span><span class="sxs-lookup"><span data-stu-id="b9984-201">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[<span data-ttu-id="b9984-202">macOS</span><span class="sxs-lookup"><span data-stu-id="b9984-202">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[<span data-ttu-id="b9984-203">Windows</span><span class="sxs-lookup"><span data-stu-id="b9984-203">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="b9984-204">Varsayılan metin düzenleyiciyi belirli CLı bağımsız değişkenleriyle başlatmak için `editor.command.default.arguments` anahtarı ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b9984-204">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="b9984-205">Örneğin, Visual Studio Code varsayılan metin Düzenleyicisi olduğunu ve her zaman HTTP REPL 'un uzantıları devre dışı bırakılmış yeni bir oturumda Visual Studio Code açmasını istediğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="b9984-205">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="b9984-206">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9984-206">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

> [!TIP]
> <span data-ttu-id="b9984-207">Varsayılan düzenleyiciniz Visual Studio Code, genellikle `-w` veya `--wait` bağımsız değişkeni, döndürmeden önce dosyayı kapatmanızı beklemek için Visual Studio Code zorlamak üzere geçirmek isteyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="b9984-207">If your default editor is Visual Studio Code, you'll usually want to pass the `-w` or `--wait` argument to force Visual Studio Code to wait for you to close the file before returning.</span></span>

### <a name="set-the-openapi-description-search-paths"></a><span data-ttu-id="b9984-208">Openapı açıklaması arama yollarını ayarlama</span><span class="sxs-lookup"><span data-stu-id="b9984-208">Set the OpenAPI Description search paths</span></span>

<span data-ttu-id="b9984-209">Varsayılan olarak, HTTP REPL, komutu seçeneği olmadan yürütürken Openapı açıklamasını bulmak için kullandığı bir göreli yollar kümesine sahiptir `connect` `--openapi` .</span><span class="sxs-lookup"><span data-stu-id="b9984-209">By default, the HTTP REPL has a set of relative paths that it uses to find the OpenAPI description when executing the `connect` command without the `--openapi` option.</span></span> <span data-ttu-id="b9984-210">Bu göreli yollar, komutta belirtilen kök ve taban yollarla birleştirilir `connect` .</span><span class="sxs-lookup"><span data-stu-id="b9984-210">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="b9984-211">Varsayılan göreli yollar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="b9984-211">The default relative paths are:</span></span>

- <span data-ttu-id="b9984-212">*Üzerindeswagger.js*</span><span class="sxs-lookup"><span data-stu-id="b9984-212">*swagger.json*</span></span>
- <span data-ttu-id="b9984-213">*Swagger/v1/swagger.jsaçık*</span><span class="sxs-lookup"><span data-stu-id="b9984-213">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="b9984-214">*/swagger.js*</span><span class="sxs-lookup"><span data-stu-id="b9984-214">*/swagger.json*</span></span>
- <span data-ttu-id="b9984-215">*/Swagger/v1/swagger.js*</span><span class="sxs-lookup"><span data-stu-id="b9984-215">*/swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="b9984-216">*Üzerindeopenapi.js*</span><span class="sxs-lookup"><span data-stu-id="b9984-216">*openapi.json*</span></span>
- <span data-ttu-id="b9984-217">*/openapi.js*</span><span class="sxs-lookup"><span data-stu-id="b9984-217">*/openapi.json*</span></span>

<span data-ttu-id="b9984-218">Ortamınızda farklı bir arama yolları kümesi kullanmak için `swagger.searchPaths` tercihi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b9984-218">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="b9984-219">Değer, göreli yolların kanal ile ayrılmış bir listesi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b9984-219">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="b9984-220">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b9984-220">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

<span data-ttu-id="b9984-221">Varsayılan listeyi tamamen değiştirmek yerine, liste yol ekleyerek veya kaldırarak da değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="b9984-221">Instead of replacing the default list altogether, the list can also be modified by adding or removing paths.</span></span>

<span data-ttu-id="b9984-222">Varsayılan listeye bir veya daha fazla arama yolu eklemek için `swagger.addToSearchPaths` tercihi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b9984-222">To add one or more search paths to the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="b9984-223">Değer, göreli yolların kanal ile ayrılmış bir listesi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b9984-223">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="b9984-224">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b9984-224">For example:</span></span>

```console
pref set swagger.addToSearchPaths "openapi/v2/openapi.json|openapi/v3/openapi.json"
```

<span data-ttu-id="b9984-225">Varsayılan listeden bir veya daha fazla arama yolunu kaldırmak için `swagger.addToSearchPaths` tercihi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b9984-225">To remove one or more search paths from the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="b9984-226">Değer, göreli yolların kanal ile ayrılmış bir listesi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b9984-226">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="b9984-227">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b9984-227">For example:</span></span>

```console
pref set swagger.removeFromSearchPaths "swagger.json|/swagger.json"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="b9984-228">HTTP GET isteklerini test etme</span><span class="sxs-lookup"><span data-stu-id="b9984-228">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="b9984-229">Özeti</span><span class="sxs-lookup"><span data-stu-id="b9984-229">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="b9984-230">Arguments</span><span class="sxs-lookup"><span data-stu-id="b9984-230">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="b9984-231">Varsa, ilişkili denetleyici eylem yöntemi tarafından beklenen rota parametresi.</span><span class="sxs-lookup"><span data-stu-id="b9984-231">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="b9984-232">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="b9984-232">Options</span></span>

<span data-ttu-id="b9984-233">Komutu için aşağıdaki seçenekler kullanılabilir `get` :</span><span class="sxs-lookup"><span data-stu-id="b9984-233">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="b9984-234">Örnek</span><span class="sxs-lookup"><span data-stu-id="b9984-234">Example</span></span>

<span data-ttu-id="b9984-235">HTTP GET isteği vermek için:</span><span class="sxs-lookup"><span data-stu-id="b9984-235">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="b9984-236">`get`Komutu onu destekleyen bir uç noktada çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9984-236">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> get
    ```

    <span data-ttu-id="b9984-237">Yukarıdaki komut aşağıdaki çıkış biçimini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="b9984-237">The preceding command displays the following output format:</span></span>

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


    https://localhost:5001/people>
    ```

1. <span data-ttu-id="b9984-238">Komutuna bir parametre geçirerek belirli bir kaydı alın `get` :</span><span class="sxs-lookup"><span data-stu-id="b9984-238">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people> get 2
    ```

    <span data-ttu-id="b9984-239">Yukarıdaki komut aşağıdaki çıkış biçimini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="b9984-239">The preceding command displays the following output format:</span></span>

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


    https://localhost:5001/people>
    ```

## <a name="test-http-post-requests"></a><span data-ttu-id="b9984-240">HTTP POST isteklerini test et</span><span class="sxs-lookup"><span data-stu-id="b9984-240">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="b9984-241">Özeti</span><span class="sxs-lookup"><span data-stu-id="b9984-241">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="b9984-242">Arguments</span><span class="sxs-lookup"><span data-stu-id="b9984-242">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="b9984-243">Varsa, ilişkili denetleyici eylem yöntemi tarafından beklenen rota parametresi.</span><span class="sxs-lookup"><span data-stu-id="b9984-243">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="b9984-244">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="b9984-244">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="b9984-245">Örnek</span><span class="sxs-lookup"><span data-stu-id="b9984-245">Example</span></span>

<span data-ttu-id="b9984-246">HTTP POST isteği vermek için:</span><span class="sxs-lookup"><span data-stu-id="b9984-246">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="b9984-247">`post`Komutu onu destekleyen bir uç noktada çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9984-247">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```

    <span data-ttu-id="b9984-248">Önceki komutta, `Content-Type` http istek üst bilgisi, JSON türünde bir istek gövdesi medya türünü gösterecek şekilde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="b9984-248">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="b9984-249">Varsayılan metin Düzenleyicisi, HTTP istek gövdesini temsil eden bir JSON şablonuyla bir *. tmp* dosyası açar.</span><span class="sxs-lookup"><span data-stu-id="b9984-249">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="b9984-250">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b9984-250">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="b9984-251">Varsayılan metin düzenleyicisini ayarlamak için [varsayılan metin düzenleyiciyi ayarlama](#set-the-default-text-editor) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b9984-251">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="b9984-252">JSON şablonunu model doğrulama gereksinimlerini karşılayacak şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="b9984-252">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="b9984-253">*. Tmp* dosyasını kaydedin ve metin düzenleyicisini kapatın.</span><span class="sxs-lookup"><span data-stu-id="b9984-253">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="b9984-254">Aşağıdaki çıktı komut kabuğu 'nda görünür:</span><span class="sxs-lookup"><span data-stu-id="b9984-254">The following output appears in the command shell:</span></span>

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


    https://localhost:5001/people>
    ```

## <a name="test-http-put-requests"></a><span data-ttu-id="b9984-255">HTTP PUT isteklerini test etme</span><span class="sxs-lookup"><span data-stu-id="b9984-255">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="b9984-256">Özeti</span><span class="sxs-lookup"><span data-stu-id="b9984-256">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="b9984-257">Arguments</span><span class="sxs-lookup"><span data-stu-id="b9984-257">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="b9984-258">Varsa, ilişkili denetleyici eylem yöntemi tarafından beklenen rota parametresi.</span><span class="sxs-lookup"><span data-stu-id="b9984-258">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="b9984-259">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="b9984-259">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="b9984-260">Örnek</span><span class="sxs-lookup"><span data-stu-id="b9984-260">Example</span></span>

<span data-ttu-id="b9984-261">HTTP PUT isteği vermek için:</span><span class="sxs-lookup"><span data-stu-id="b9984-261">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="b9984-262">*Isteğe bağlı* : `get` verileri değiştirmeden önce görüntülemek için komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9984-262">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits> get
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

1. <span data-ttu-id="b9984-263">`put`Komutu onu destekleyen bir uç noktada çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9984-263">Run the `put` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="b9984-264">Önceki komutta, `Content-Type` http istek üst bilgisi, JSON türünde bir istek gövdesi medya türünü gösterecek şekilde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="b9984-264">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="b9984-265">Varsayılan metin Düzenleyicisi, HTTP istek gövdesini temsil eden bir JSON şablonuyla bir *. tmp* dosyası açar.</span><span class="sxs-lookup"><span data-stu-id="b9984-265">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="b9984-266">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b9984-266">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="b9984-267">Varsayılan metin düzenleyicisini ayarlamak için [varsayılan metin düzenleyiciyi ayarlama](#set-the-default-text-editor) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b9984-267">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="b9984-268">JSON şablonunu model doğrulama gereksinimlerini karşılayacak şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="b9984-268">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="b9984-269">*. Tmp* dosyasını kaydedin ve metin düzenleyicisini kapatın.</span><span class="sxs-lookup"><span data-stu-id="b9984-269">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="b9984-270">Aşağıdaki çıktı komut kabuğu 'nda görünür:</span><span class="sxs-lookup"><span data-stu-id="b9984-270">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="b9984-271">*Isteğe bağlı* : `get` değişiklikleri görmek için bir komut verin.</span><span class="sxs-lookup"><span data-stu-id="b9984-271">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="b9984-272">Örneğin, metin düzenleyicisinde "Chraz" yazdıysanız, bir, `get` aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="b9984-272">For example, if you typed "Cherry" in the text editor, a `get` returns the following output:</span></span>

    ```console
    https://localhost:5001/fruits> get
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


    https://localhost:5001/fruits>
    ```

## <a name="test-http-delete-requests"></a><span data-ttu-id="b9984-273">HTTP SILME isteklerini test etme</span><span class="sxs-lookup"><span data-stu-id="b9984-273">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="b9984-274">Özeti</span><span class="sxs-lookup"><span data-stu-id="b9984-274">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="b9984-275">Arguments</span><span class="sxs-lookup"><span data-stu-id="b9984-275">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="b9984-276">Varsa, ilişkili denetleyici eylem yöntemi tarafından beklenen rota parametresi.</span><span class="sxs-lookup"><span data-stu-id="b9984-276">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="b9984-277">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="b9984-277">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="b9984-278">Örnek</span><span class="sxs-lookup"><span data-stu-id="b9984-278">Example</span></span>

<span data-ttu-id="b9984-279">HTTP SILME isteği vermek için:</span><span class="sxs-lookup"><span data-stu-id="b9984-279">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="b9984-280">*Isteğe bağlı* : `get` verileri değiştirmeden önce görüntülemek için komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9984-280">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits> get
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

1. <span data-ttu-id="b9984-281">`delete`Komutu onu destekleyen bir uç noktada çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9984-281">Run the `delete` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> delete 2
    ```

    <span data-ttu-id="b9984-282">Yukarıdaki komut aşağıdaki çıkış biçimini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="b9984-282">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="b9984-283">*Isteğe bağlı* : `get` değişiklikleri görmek için bir komut verin.</span><span class="sxs-lookup"><span data-stu-id="b9984-283">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="b9984-284">Bu örnekte, bir, `get` aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="b9984-284">In this example, a `get` returns the following output:</span></span>

    ```console
    https://localhost:5001/fruits> get
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


    https://localhost:5001/fruits>
    ```

## <a name="test-http-patch-requests"></a><span data-ttu-id="b9984-285">HTTP PATCH isteklerini test etme</span><span class="sxs-lookup"><span data-stu-id="b9984-285">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="b9984-286">Özeti</span><span class="sxs-lookup"><span data-stu-id="b9984-286">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="b9984-287">Arguments</span><span class="sxs-lookup"><span data-stu-id="b9984-287">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="b9984-288">Varsa, ilişkili denetleyici eylem yöntemi tarafından beklenen rota parametresi.</span><span class="sxs-lookup"><span data-stu-id="b9984-288">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="b9984-289">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="b9984-289">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="b9984-290">HTTP HEAD isteklerini test etme</span><span class="sxs-lookup"><span data-stu-id="b9984-290">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="b9984-291">Özeti</span><span class="sxs-lookup"><span data-stu-id="b9984-291">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="b9984-292">Arguments</span><span class="sxs-lookup"><span data-stu-id="b9984-292">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="b9984-293">Varsa, ilişkili denetleyici eylem yöntemi tarafından beklenen rota parametresi.</span><span class="sxs-lookup"><span data-stu-id="b9984-293">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="b9984-294">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="b9984-294">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="b9984-295">Sınama HTTP SEÇENEKLERI istekleri</span><span class="sxs-lookup"><span data-stu-id="b9984-295">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="b9984-296">Özeti</span><span class="sxs-lookup"><span data-stu-id="b9984-296">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="b9984-297">Arguments</span><span class="sxs-lookup"><span data-stu-id="b9984-297">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="b9984-298">Varsa, ilişkili denetleyici eylem yöntemi tarafından beklenen rota parametresi.</span><span class="sxs-lookup"><span data-stu-id="b9984-298">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="b9984-299">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="b9984-299">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="b9984-300">HTTP istek üst bilgilerini ayarla</span><span class="sxs-lookup"><span data-stu-id="b9984-300">Set HTTP request headers</span></span>

<span data-ttu-id="b9984-301">Bir HTTP istek üst bilgisi ayarlamak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="b9984-301">To set an HTTP request header, use one of the following approaches:</span></span>

* <span data-ttu-id="b9984-302">HTTP isteğiyle satır içi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b9984-302">Set inline with the HTTP request.</span></span> <span data-ttu-id="b9984-303">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b9984-303">For example:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```
    
    <span data-ttu-id="b9984-304">Önceki yaklaşımla, her ayrı HTTP istek üst bilgisi kendi seçeneğini gerektirir `-h` .</span><span class="sxs-lookup"><span data-stu-id="b9984-304">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

* <span data-ttu-id="b9984-305">HTTP isteğini göndermeden önce ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b9984-305">Set before sending the HTTP request.</span></span> <span data-ttu-id="b9984-306">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b9984-306">For example:</span></span>

    ```console
    https://localhost:5001/people> set header Content-Type application/json
    ```
    
    <span data-ttu-id="b9984-307">Bir isteği göndermeden önce üst bilgi ayarlanırken üst bilgi, komut kabuğu oturumunun süresi boyunca ayarlanmış olarak kalır.</span><span class="sxs-lookup"><span data-stu-id="b9984-307">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="b9984-308">Üstbilgiyi temizlemek için boş bir değer sağlayın.</span><span class="sxs-lookup"><span data-stu-id="b9984-308">To clear the header, provide an empty value.</span></span> <span data-ttu-id="b9984-309">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b9984-309">For example:</span></span>
    
    ```console
    https://localhost:5001/people> set header Content-Type
    ```

## <a name="test-secured-endpoints"></a><span data-ttu-id="b9984-310">Güvenli uç noktaları test et</span><span class="sxs-lookup"><span data-stu-id="b9984-310">Test secured endpoints</span></span>

<span data-ttu-id="b9984-311">HTTP REPL, güvenli uç noktaların sınamasını aşağıdaki yollarla destekler:</span><span class="sxs-lookup"><span data-stu-id="b9984-311">The HTTP REPL supports the testing of secured endpoints in the following ways:</span></span>

* <span data-ttu-id="b9984-312">Oturum açmış kullanıcının varsayılan kimlik bilgileri aracılığıyla.</span><span class="sxs-lookup"><span data-stu-id="b9984-312">Via the default credentials of the logged in user.</span></span>
* <span data-ttu-id="b9984-313">HTTP istek üst bilgileri kullanılarak.</span><span class="sxs-lookup"><span data-stu-id="b9984-313">Through the use of HTTP request headers.</span></span>

### <a name="default-credentials"></a><span data-ttu-id="b9984-314">Varsayılan kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="b9984-314">Default credentials</span></span>

<span data-ttu-id="b9984-315">IIS 'de barındırılan ve Windows kimlik doğrulaması ile güvenliği sağlanan bir Web API 'sini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="b9984-315">Consider a web API you're testing that's hosted in IIS and secured with Windows authentication.</span></span> <span data-ttu-id="b9984-316">Aracı çalıştıran kullanıcının kimlik bilgilerinin, test edilmekte olan HTTP uç noktaları üzerinden akmasını istiyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="b9984-316">You want the credentials of the user running the tool to flow across to the HTTP endpoints being tested.</span></span> <span data-ttu-id="b9984-317">Oturum açmış kullanıcının varsayılan kimlik bilgilerini geçirmek için:</span><span class="sxs-lookup"><span data-stu-id="b9984-317">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="b9984-318">Tercihi şu `httpClient.useDefaultCredentials` şekilde ayarlayın `true` :</span><span class="sxs-lookup"><span data-stu-id="b9984-318">Set the `httpClient.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. <span data-ttu-id="b9984-319">Web API 'sine başka bir istek göndermeden önce çıkın ve aracı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="b9984-319">Exit and restart the tool before sending another request to the web API.</span></span>
 
### <a name="default-proxy-credentials"></a><span data-ttu-id="b9984-320">Varsayılan proxy kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="b9984-320">Default proxy credentials</span></span>

<span data-ttu-id="b9984-321">Test ettiğiniz Web API 'sinin, Windows kimlik doğrulamasıyla güvenliği sağlanmış bir proxy 'nin arkasında olduğu bir senaryo düşünün.</span><span class="sxs-lookup"><span data-stu-id="b9984-321">Consider a scenario in which the web API you're testing is behind a proxy secured with Windows authentication.</span></span> <span data-ttu-id="b9984-322">Aracı çalıştıran kullanıcının kimlik bilgilerinin ara sunucuya akmasını istiyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="b9984-322">You want the credentials of the user running the tool to flow to the proxy.</span></span> <span data-ttu-id="b9984-323">Oturum açmış kullanıcının varsayılan kimlik bilgilerini geçirmek için:</span><span class="sxs-lookup"><span data-stu-id="b9984-323">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="b9984-324">Tercihi şu `httpClient.proxy.useDefaultCredentials` şekilde ayarlayın `true` :</span><span class="sxs-lookup"><span data-stu-id="b9984-324">Set the `httpClient.proxy.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.proxy.useDefaultCredentials true
    ```

1. <span data-ttu-id="b9984-325">Web API 'sine başka bir istek göndermeden önce çıkın ve aracı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="b9984-325">Exit and restart the tool before sending another request to the web API.</span></span>

### <a name="http-request-headers"></a><span data-ttu-id="b9984-326">HTTP istek üstbilgileri</span><span class="sxs-lookup"><span data-stu-id="b9984-326">HTTP request headers</span></span>

<span data-ttu-id="b9984-327">Desteklenen kimlik doğrulama ve yetkilendirme şemaları örnekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="b9984-327">Examples of supported authentication and authorization schemes include:</span></span>

* <span data-ttu-id="b9984-328">temel kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="b9984-328">basic authentication</span></span>
* <span data-ttu-id="b9984-329">JWT taşıyıcı belirteçleri</span><span class="sxs-lookup"><span data-stu-id="b9984-329">JWT bearer tokens</span></span>
* <span data-ttu-id="b9984-330">Özet kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="b9984-330">digest authentication</span></span>

<span data-ttu-id="b9984-331">Örneğin, aşağıdaki komutla bir uç noktaya bir taşıyıcı belirteci gönderebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="b9984-331">For example, you can send a bearer token to an endpoint with the following command:</span></span>

```console
set header Authorization "bearer <TOKEN VALUE>"
```

<span data-ttu-id="b9984-332">Azure 'da barındırılan bir uç noktaya erişmek veya [azure REST API](/rest/api/azure/)kullanmak için bir taşıyıcı belirtecine ihtiyacınız vardır.</span><span class="sxs-lookup"><span data-stu-id="b9984-332">To access an Azure-hosted endpoint or to use the [Azure REST API](/rest/api/azure/), you need a bearer token.</span></span> <span data-ttu-id="b9984-333">Azure [CLI](/cli/azure/)aracılığıyla Azure Aboneliğinize yönelik bir taşıyıcı belirteci almak için aşağıdaki adımları kullanın.</span><span class="sxs-lookup"><span data-stu-id="b9984-333">Use the following steps to obtain a bearer token for your Azure subscription via the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="b9984-334">HTTP REPL, bir HTTP istek üstbilgisindeki taşıyıcı belirtecini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="b9984-334">The HTTP REPL sets the bearer token in an HTTP request header.</span></span> <span data-ttu-id="b9984-335">Azure App Service Web Apps listesi alındı.</span><span class="sxs-lookup"><span data-stu-id="b9984-335">A list of Azure App Service Web Apps is retrieved.</span></span>

1. <span data-ttu-id="b9984-336">Azure 'da oturum açın:</span><span class="sxs-lookup"><span data-stu-id="b9984-336">Sign in to Azure:</span></span>

    ```azurecli
    az login
    ```

1. <span data-ttu-id="b9984-337">Aşağıdaki komutla abonelik KIMLIĞINIZI alın:</span><span class="sxs-lookup"><span data-stu-id="b9984-337">Get your subscription ID with the following command:</span></span>

    ```azurecli
    az account show --query id
    ```

1. <span data-ttu-id="b9984-338">Abonelik KIMLIĞINIZI kopyalayın ve şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9984-338">Copy your subscription ID and run the following command:</span></span>

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. <span data-ttu-id="b9984-339">Aşağıdaki komutla taşıyıcı belirtecinizi alın:</span><span class="sxs-lookup"><span data-stu-id="b9984-339">Get your bearer token with the following command:</span></span>

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. <span data-ttu-id="b9984-340">HTTP REPL aracılığıyla Azure REST API bağlanma:</span><span class="sxs-lookup"><span data-stu-id="b9984-340">Connect to the Azure REST API via the HTTP REPL:</span></span>

    ```console
    httprepl https://management.azure.com
    ```

1. <span data-ttu-id="b9984-341">`Authorization`Http istek üst bilgisini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="b9984-341">Set the `Authorization` HTTP request header:</span></span>

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. <span data-ttu-id="b9984-342">Aboneliğe gidin:</span><span class="sxs-lookup"><span data-stu-id="b9984-342">Navigate to the subscription:</span></span>

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. <span data-ttu-id="b9984-343">Aboneliğinizin Azure App Service Web Apps bir listesini alın:</span><span class="sxs-lookup"><span data-stu-id="b9984-343">Get a list of your subscription's Azure App Service Web Apps:</span></span>

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    <span data-ttu-id="b9984-344">Aşağıdaki yanıt görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="b9984-344">The following response is displayed:</span></span>

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

## <a name="toggle-http-request-display"></a><span data-ttu-id="b9984-345">HTTP istek görüntüsünü değiştirme</span><span class="sxs-lookup"><span data-stu-id="b9984-345">Toggle HTTP request display</span></span>

<span data-ttu-id="b9984-346">Varsayılan olarak, gönderilmekte olan HTTP isteğinin görüntüsü bastırılır.</span><span class="sxs-lookup"><span data-stu-id="b9984-346">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="b9984-347">Komut kabuğu oturumunun süresi boyunca ilgili ayarı değiştirmek mümkündür.</span><span class="sxs-lookup"><span data-stu-id="b9984-347">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="b9984-348">İstek görüntülemesini etkinleştir</span><span class="sxs-lookup"><span data-stu-id="b9984-348">Enable request display</span></span>

<span data-ttu-id="b9984-349">Komutunu çalıştırarak gönderilmekte olan HTTP isteğini görüntüleyin `echo on` .</span><span class="sxs-lookup"><span data-stu-id="b9984-349">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="b9984-350">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b9984-350">For example:</span></span>

```console
https://localhost:5001/people> echo on
Request echoing is on
```

<span data-ttu-id="b9984-351">Geçerli oturumdaki sonraki HTTP istekleri, istek üst bilgilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="b9984-351">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="b9984-352">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b9984-352">For example:</span></span>

```console
https://localhost:5001/people> post

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


https://localhost:5001/people>
```

### <a name="disable-request-display"></a><span data-ttu-id="b9984-353">İstek görüntüsünü devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="b9984-353">Disable request display</span></span>

<span data-ttu-id="b9984-354">Komutunu çalıştırarak gönderilen HTTP isteğinin görüntülenmesini gizleyin `echo off` .</span><span class="sxs-lookup"><span data-stu-id="b9984-354">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="b9984-355">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b9984-355">For example:</span></span>

```console
https://localhost:5001/people> echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="b9984-356">Betik çalıştırma</span><span class="sxs-lookup"><span data-stu-id="b9984-356">Run a script</span></span>

<span data-ttu-id="b9984-357">Aynı HTTP REPL komutları kümesini sıklıkla yürütüyorsanız bunları bir metin dosyasında depolamayı göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="b9984-357">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="b9984-358">Dosyadaki komutlar, komut satırında el ile yürütülen komutlarla aynı formu alır.</span><span class="sxs-lookup"><span data-stu-id="b9984-358">Commands in the file take the same form as commands executed manually on the command line.</span></span> <span data-ttu-id="b9984-359">Komutlar, komutu kullanılarak toplanmış bir biçimde yürütülebilir `run` .</span><span class="sxs-lookup"><span data-stu-id="b9984-359">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="b9984-360">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b9984-360">For example:</span></span>

1. <span data-ttu-id="b9984-361">Yeni satır için ayrılmış komutlar kümesini içeren bir metin dosyası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b9984-361">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="b9984-362">Göstermek için aşağıdaki komutları içeren bir *people-script.txt* dosyası göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="b9984-362">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="b9984-363">`run`Metin dosyasının yolunu geçirerek komutunu yürütün.</span><span class="sxs-lookup"><span data-stu-id="b9984-363">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="b9984-364">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b9984-364">For example:</span></span>

    ```console
    https://localhost:5001/> run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="b9984-365">Aşağıdaki çıkış görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="b9984-365">The following output appears:</span></span>

    ```console
    https://localhost:5001/> set base https://localhost:5001
    Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/> ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/> cd People
    /People    [get|post]
    
    https://localhost:5001/People> ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People> get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People>
    ```

## <a name="clear-the-output"></a><span data-ttu-id="b9984-366">Çıktıyı temizle</span><span class="sxs-lookup"><span data-stu-id="b9984-366">Clear the output</span></span>

<span data-ttu-id="b9984-367">HTTP REPL aracı tarafından komut kabuğu 'na yazılan tüm çıktıyı kaldırmak için `clear` veya `cls` komutunu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b9984-367">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="b9984-368">Göstermek için komut kabuğu 'nun aşağıdaki çıktıyı içerdiğini düşünün:</span><span class="sxs-lookup"><span data-stu-id="b9984-368">To illustrate, imagine the command shell contains the following output:</span></span>

```console
httprepl https://localhost:5001
(Disconnected)> set base "https://localhost:5001"
Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/> ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

<span data-ttu-id="b9984-369">Çıktıyı temizlemek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b9984-369">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/> clear
```

<span data-ttu-id="b9984-370">Yukarıdaki komutu çalıştırdıktan sonra, komut kabuğu yalnızca şu çıktıyı içerir:</span><span class="sxs-lookup"><span data-stu-id="b9984-370">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/>
```

## <a name="additional-resources"></a><span data-ttu-id="b9984-371">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b9984-371">Additional resources</span></span>

* [<span data-ttu-id="b9984-372">REST API istekleri</span><span class="sxs-lookup"><span data-stu-id="b9984-372">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="b9984-373">HTTP REPL GitHub deposu</span><span class="sxs-lookup"><span data-stu-id="b9984-373">HTTP REPL GitHub repository</span></span>](https://github.com/dotnet/HttpRepl)
