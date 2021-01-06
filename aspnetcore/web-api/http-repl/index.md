---
title: HttpRepl ile Web API 'Lerini test etme
author: scottaddie
description: Bir ASP.NET Core Web API 'sini taramak ve test etmek için HttpRepl .NET Core küresel aracının nasıl kullanılacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2020
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
uid: web-api/http-repl
ms.openlocfilehash: 1027887738740d50c30e24e800c0402b1ce4ad02
ms.sourcegitcommit: 04a404a9655c59ad1ea02aff5d399ae1b833ad6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854671"
---
# <a name="test-web-apis-with-the-httprepl"></a><span data-ttu-id="eecb3-103">HttpRepl ile Web API 'Lerini test etme</span><span class="sxs-lookup"><span data-stu-id="eecb3-103">Test web APIs with the HttpRepl</span></span>

<span data-ttu-id="eecb3-104">[Scott Ade](https://twitter.com/Scott_Addie) tarafından</span><span class="sxs-lookup"><span data-stu-id="eecb3-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="eecb3-105">HTTP okuma-değerlendirme-yazdırma döngüsü (REPL):</span><span class="sxs-lookup"><span data-stu-id="eecb3-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="eecb3-106">.NET Core 'un her yerde desteklenen basit, platformlar arası bir komut satırı aracı desteklenir.</span><span class="sxs-lookup"><span data-stu-id="eecb3-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="eecb3-107">ASP.NET Core Web API 'Lerini (ve non-ASP.NET çekirdek Web API 'Leri) test etmek ve sonuçlarını görüntülemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="eecb3-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="eecb3-108">Localhost ve Azure App Service dahil olmak üzere herhangi bir ortamda barındırılan Web API 'Lerini test etme özelliğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="eecb3-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="eecb3-109">Aşağıdaki [http fiilleri](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) desteklenir:</span><span class="sxs-lookup"><span data-stu-id="eecb3-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="eecb3-110">SILMELI</span><span class="sxs-lookup"><span data-stu-id="eecb3-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="eecb3-111">GET</span><span class="sxs-lookup"><span data-stu-id="eecb3-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="eecb3-112">BAŞLı</span><span class="sxs-lookup"><span data-stu-id="eecb3-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="eecb3-113">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="eecb3-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="eecb3-114">DÜZELTMESI</span><span class="sxs-lookup"><span data-stu-id="eecb3-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="eecb3-115">Yayınla</span><span class="sxs-lookup"><span data-stu-id="eecb3-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="eecb3-116">PUT</span><span class="sxs-lookup"><span data-stu-id="eecb3-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="eecb3-117">Takip etmek için, [örnek ASP.NET Core Web API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) 'sini ([indirme](xref:index#how-to-download-a-sample)) görüntüleyin veya indirin.</span><span class="sxs-lookup"><span data-stu-id="eecb3-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eecb3-118">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="eecb3-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="eecb3-119">Yükleme</span><span class="sxs-lookup"><span data-stu-id="eecb3-119">Installation</span></span>

<span data-ttu-id="eecb3-120">HttpRepl 'yi yüklemek için şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-120">To install the HttpRepl, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

<span data-ttu-id="eecb3-121">[.NET Core küresel aracı](/dotnet/core/tools/global-tools#install-a-global-tool) , [Microsoft. DotNet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet paketinden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="eecb3-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="eecb3-122">Kullanım</span><span class="sxs-lookup"><span data-stu-id="eecb3-122">Usage</span></span>

<span data-ttu-id="eecb3-123">Aracın başarıyla yüklenmesinden sonra, HttpRepl 'yi başlatmak için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-123">After successful installation of the tool, run the following command to start the HttpRepl:</span></span>

```console
httprepl
```

<span data-ttu-id="eecb3-124">Kullanılabilir HttpRepl komutlarını görüntülemek için aşağıdaki komutlardan birini çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-124">To view the available HttpRepl commands, run one of the following commands:</span></span>

```console
httprepl -h
```

```console
httprepl --help
```

<span data-ttu-id="eecb3-125">Aşağıdaki çıktı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="eecb3-125">The following output is displayed:</span></span>

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

<span data-ttu-id="eecb3-126">HttpRepl komut tamamlama sağlar.</span><span class="sxs-lookup"><span data-stu-id="eecb3-126">The HttpRepl offers command completion.</span></span> <span data-ttu-id="eecb3-127"><kbd>Sekme</kbd> tuşuna basıldığında, yazdığınız KARAKTERLERI veya API uç noktasını tamamlayacak komutların listesi üzerinden yinelenir.</span><span class="sxs-lookup"><span data-stu-id="eecb3-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="eecb3-128">Aşağıdaki bölümlerde kullanılabilir CLı komutları ana hatlarıyla verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="eecb3-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="eecb3-129">Web API 'sine bağlanma</span><span class="sxs-lookup"><span data-stu-id="eecb3-129">Connect to the web API</span></span>

<span data-ttu-id="eecb3-130">Aşağıdaki komutu çalıştırarak bir Web API 'sine bağlanın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-130">Connect to a web API by running the following command:</span></span>

```console
httprepl <ROOT URI>
```

<span data-ttu-id="eecb3-131">`<ROOT URI>` , Web API 'sinin temel URI 'sidir.</span><span class="sxs-lookup"><span data-stu-id="eecb3-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="eecb3-132">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-132">For example:</span></span>

```console
httprepl https://localhost:5001
```

<span data-ttu-id="eecb3-133">Alternatif olarak, HttpRepl çalışırken herhangi bir zamanda aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-133">Alternatively, run the following command at any time while the HttpRepl is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="eecb3-134">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-134">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001
```

### <a name="manually-point-to-the-openapi-description-for-the-web-api"></a><span data-ttu-id="eecb3-135">Web API 'SI için Openapı açıklamasına el ile işaret edin</span><span class="sxs-lookup"><span data-stu-id="eecb3-135">Manually point to the OpenAPI description for the web API</span></span>

<span data-ttu-id="eecb3-136">Yukarıdaki Connect komutu, Openapı açıklamasını otomatik olarak bulmaya çalışacaktır.</span><span class="sxs-lookup"><span data-stu-id="eecb3-136">The connect command above will attempt to find the OpenAPI description automatically.</span></span> <span data-ttu-id="eecb3-137">Bir nedenden dolayı bunu yapamaması durumunda, Web API 'SI için Openapı açıklamasının URI 'sini belirterek `--openapi` seçeneğini kullanabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="eecb3-137">If for some reason it's unable to do so, you can specify the URI of the OpenAPI description for the web API by using the `--openapi` option:</span></span>

```console
connect <ROOT URI> --openapi <OPENAPI DESCRIPTION ADDRESS>
```

<span data-ttu-id="eecb3-138">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-138">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --openapi /swagger/v1/swagger.json
```

### <a name="enable-verbose-output-for-details-on-openapi-description-searching-parsing-and-validation"></a><span data-ttu-id="eecb3-139">Openapı açıklaması arama, ayrıştırma ve doğrulama hakkındaki ayrıntılar için ayrıntılı çıktıyı etkinleştirin</span><span class="sxs-lookup"><span data-stu-id="eecb3-139">Enable verbose output for details on OpenAPI description searching, parsing, and validation</span></span>

<span data-ttu-id="eecb3-140">`--verbose`Komut ile seçeneğinin belirtilmesi, `connect` araç openapı açıklamasını aradığında, ayrıştırdığında ve doğruladığı zaman daha fazla ayrıntı üretecektir.</span><span class="sxs-lookup"><span data-stu-id="eecb3-140">Specifying the `--verbose` option with the `connect` command will produce more details when the tool searches for the OpenAPI description, parses, and validates it.</span></span>

```console
connect <ROOT URI> --verbose
```

<span data-ttu-id="eecb3-141">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-141">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --verbose
Checking https://localhost:5001/swagger.json... 404 NotFound
Checking https://localhost:5001/swagger/v1/swagger.json... 404 NotFound
Checking https://localhost:5001/openapi.json... Found
Parsing... Successful (with warnings)
The field 'info' in 'document' object is REQUIRED [#/info]
The field 'paths' in 'document' object is REQUIRED [#/paths]
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="eecb3-142">Web API 'sinde gezin</span><span class="sxs-lookup"><span data-stu-id="eecb3-142">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="eecb3-143">Kullanılabilir uç noktaları görüntüle</span><span class="sxs-lookup"><span data-stu-id="eecb3-143">View available endpoints</span></span>

<span data-ttu-id="eecb3-144">Web API adresinin geçerli yolundaki farklı uç noktaları (denetleyiciler) listelemek için, `ls` veya `dir` komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-144">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhost:5001/> ls
```

<span data-ttu-id="eecb3-145">Aşağıdaki çıkış biçimi görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="eecb3-145">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

<span data-ttu-id="eecb3-146">Yukarıdaki çıkış, kullanılabilir iki denetleyici olduğunu gösterir: `Fruits` ve `People` .</span><span class="sxs-lookup"><span data-stu-id="eecb3-146">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="eecb3-147">Her iki denetleyici de parametresiz HTTP GET ve POST işlemlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="eecb3-147">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="eecb3-148">Belirli bir denetleyicide gezinmek daha ayrıntılı bilgi gösterir.</span><span class="sxs-lookup"><span data-stu-id="eecb3-148">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="eecb3-149">Örneğin, aşağıdaki komut çıktısı, `Fruits` denetleyiciyi de http get, put ve DELETE işlemlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="eecb3-149">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="eecb3-150">Bu işlemlerin her biri `id` , rotada bir parametre bekler:</span><span class="sxs-lookup"><span data-stu-id="eecb3-150">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits> ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits>
```

<span data-ttu-id="eecb3-151">Alternatif olarak, `ui` Web API 'Sinin Swagger Kullanıcı Arabirimi sayfasını bir tarayıcıda açmak için komutunu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="eecb3-151">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="eecb3-152">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-152">For example:</span></span>

```console
https://localhost:5001/> ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="eecb3-153">Bir uç noktaya gitme</span><span class="sxs-lookup"><span data-stu-id="eecb3-153">Navigate to an endpoint</span></span>

<span data-ttu-id="eecb3-154">Web API 'sindeki farklı bir uç noktaya gitmek için `cd` komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-154">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/> cd people
```

<span data-ttu-id="eecb3-155">Komutu izleyen yol `cd` büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="eecb3-155">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="eecb3-156">Aşağıdaki çıkış biçimi görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="eecb3-156">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people>
```

## <a name="customize-the-httprepl"></a><span data-ttu-id="eecb3-157">HttpRepl 'yi özelleştirme</span><span class="sxs-lookup"><span data-stu-id="eecb3-157">Customize the HttpRepl</span></span>

<span data-ttu-id="eecb3-158">HttpRepl 'nin varsayılan [renkleri](#set-color-preferences) özelleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="eecb3-158">The HttpRepl's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="eecb3-159">Ayrıca, [varsayılan bir metin Düzenleyicisi](#set-the-default-text-editor) tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="eecb3-159">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="eecb3-160">HttpRepl tercihleri geçerli oturum genelinde kalıcı hale getirilir ve gelecekteki oturumlarla kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="eecb3-160">The HttpRepl preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="eecb3-161">Değiştirildikten sonra, Tercihler aşağıdaki dosyada depolanır:</span><span class="sxs-lookup"><span data-stu-id="eecb3-161">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linux"></a>[<span data-ttu-id="eecb3-162">Linux</span><span class="sxs-lookup"><span data-stu-id="eecb3-162">Linux</span></span>](#tab/linux)

<span data-ttu-id="eecb3-163">*% GIRIŞ%/. httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="eecb3-163">*%HOME%/.httpreplprefs*</span></span>

# <a name="macos"></a>[<span data-ttu-id="eecb3-164">macOS</span><span class="sxs-lookup"><span data-stu-id="eecb3-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="eecb3-165">*% GIRIŞ%/. httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="eecb3-165">*%HOME%/.httpreplprefs*</span></span>

# <a name="windows"></a>[<span data-ttu-id="eecb3-166">Windows</span><span class="sxs-lookup"><span data-stu-id="eecb3-166">Windows</span></span>](#tab/windows)

<span data-ttu-id="eecb3-167">*% USERPROFILE% \\ . httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="eecb3-167">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="eecb3-168">*. Httpreplprefs* dosyası başlangıçta yüklendi ve çalışma zamanında değişiklikler için izlenmiyor.</span><span class="sxs-lookup"><span data-stu-id="eecb3-168">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="eecb3-169">Dosyada el ile yapılan değişiklikler yalnızca araç yeniden başlatıldıktan sonra devreye girer.</span><span class="sxs-lookup"><span data-stu-id="eecb3-169">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="eecb3-170">Ayarları görüntüleyin</span><span class="sxs-lookup"><span data-stu-id="eecb3-170">View the settings</span></span>

<span data-ttu-id="eecb3-171">Kullanılabilir ayarları görüntülemek için `pref get` komutunu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="eecb3-171">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="eecb3-172">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-172">For example:</span></span>

```console
https://localhost:5001/> pref get
```

<span data-ttu-id="eecb3-173">Yukarıdaki komut, kullanılabilir anahtar-değer çiftlerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="eecb3-173">The preceding command displays the available key-value pairs:</span></span>

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

### <a name="set-color-preferences"></a><span data-ttu-id="eecb3-174">Renk tercihlerini ayarla</span><span class="sxs-lookup"><span data-stu-id="eecb3-174">Set color preferences</span></span>

<span data-ttu-id="eecb3-175">Yanıt renklendirme Şu anda yalnızca JSON için destekleniyor.</span><span class="sxs-lookup"><span data-stu-id="eecb3-175">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="eecb3-176">Varsayılan HttpRepl araç renklendirmesini özelleştirmek için, değiştirilecek renge karşılık gelen anahtarı bulun.</span><span class="sxs-lookup"><span data-stu-id="eecb3-176">To customize the default HttpRepl tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="eecb3-177">Anahtarları bulma hakkında yönergeler için bkz. [ayarları görüntüleme](#view-the-settings) bölümü.</span><span class="sxs-lookup"><span data-stu-id="eecb3-177">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="eecb3-178">Örneğin, `colors.json` anahtar değerini şu şekilde olacak şekilde `Green` değiştirin `White` :</span><span class="sxs-lookup"><span data-stu-id="eecb3-178">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people> pref set colors.json White
```

<span data-ttu-id="eecb3-179">Yalnızca [izin verilen renkler](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="eecb3-179">Only the [allowed colors](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="eecb3-180">Sonraki HTTP istekleri, yeni renklendirmesi ile çıktıyı görüntüler.</span><span class="sxs-lookup"><span data-stu-id="eecb3-180">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="eecb3-181">Belirli renk anahtarları ayarlanmamışsa, daha genel anahtarlar kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="eecb3-181">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="eecb3-182">Bu geri dönüş davranışını göstermek için aşağıdaki örneği göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="eecb3-182">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="eecb3-183">Eğer `colors.json.name` bir değeri yoksa, `colors.json.string` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="eecb3-183">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="eecb3-184">Eğer `colors.json.string` bir değeri yoksa, `colors.json.literal` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="eecb3-184">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="eecb3-185">Eğer `colors.json.literal` bir değeri yoksa, `colors.json` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="eecb3-185">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="eecb3-186">`colors.json`Bir değere sahip değilse, komut kabuğun varsayılan metin rengi ( `AllowedColors.None` ) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="eecb3-186">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="eecb3-187">Girinti boyutunu ayarla</span><span class="sxs-lookup"><span data-stu-id="eecb3-187">Set indentation size</span></span>

<span data-ttu-id="eecb3-188">Yanıt girintileme boyut özelleştirmesi Şu anda yalnızca JSON için destekleniyor.</span><span class="sxs-lookup"><span data-stu-id="eecb3-188">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="eecb3-189">Varsayılan boyut iki boşluklardan oluşamaz.</span><span class="sxs-lookup"><span data-stu-id="eecb3-189">The default size is two spaces.</span></span> <span data-ttu-id="eecb3-190">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-190">For example:</span></span>

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

<span data-ttu-id="eecb3-191">Varsayılan boyutu değiştirmek için `formatting.json.indentSize` anahtarı ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="eecb3-191">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="eecb3-192">Örneğin, her zaman dört boşluk kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="eecb3-192">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="eecb3-193">Sonraki yanıtlar dört boşluk ayarına uyar:</span><span class="sxs-lookup"><span data-stu-id="eecb3-193">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-the-default-text-editor"></a><span data-ttu-id="eecb3-194">Varsayılan metin düzenleyiciyi ayarlama</span><span class="sxs-lookup"><span data-stu-id="eecb3-194">Set the default text editor</span></span>

<span data-ttu-id="eecb3-195">Varsayılan olarak, HttpRepl 'nin kullanılmak üzere yapılandırılmış metin Düzenleyicisi yok.</span><span class="sxs-lookup"><span data-stu-id="eecb3-195">By default, the HttpRepl has no text editor configured for use.</span></span> <span data-ttu-id="eecb3-196">HTTP istek gövdesi gerektiren Web API yöntemlerini test etmek için varsayılan metin Düzenleyicisi ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="eecb3-196">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="eecb3-197">HttpRepl Aracı, istek gövdesini oluşturma amacıyla yapılandırılmış metin düzenleyicisini başlatır.</span><span class="sxs-lookup"><span data-stu-id="eecb3-197">The HttpRepl tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="eecb3-198">Tercih ettiğiniz metin düzenleyiciyi varsayılan olarak ayarlamak için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-198">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="eecb3-199">Yukarıdaki komutta, `<EXECUTABLE>` Metin düzenleyicisinin yürütülebilir dosyasının tam yoludur.</span><span class="sxs-lookup"><span data-stu-id="eecb3-199">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="eecb3-200">Örneğin, Visual Studio Code varsayılan metin düzenleyicisi olarak ayarlamak için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-200">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linux"></a>[<span data-ttu-id="eecb3-201">Linux</span><span class="sxs-lookup"><span data-stu-id="eecb3-201">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[<span data-ttu-id="eecb3-202">macOS</span><span class="sxs-lookup"><span data-stu-id="eecb3-202">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[<span data-ttu-id="eecb3-203">Windows</span><span class="sxs-lookup"><span data-stu-id="eecb3-203">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="eecb3-204">Varsayılan metin düzenleyiciyi belirli CLı bağımsız değişkenleriyle başlatmak için `editor.command.default.arguments` anahtarı ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="eecb3-204">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="eecb3-205">Örneğin, Visual Studio Code varsayılan metin Düzenleyicisi olduğunu ve her zaman HttpRepl 'nin, uzantılar devre dışı bırakılmış yeni bir oturumda Visual Studio Code açmasını istediğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="eecb3-205">For example, assume Visual Studio Code is the default text editor and that you always want the HttpRepl to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="eecb3-206">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-206">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

> [!TIP]
> <span data-ttu-id="eecb3-207">Varsayılan düzenleyiciniz Visual Studio Code, genellikle `-w` veya `--wait` bağımsız değişkeni, döndürmeden önce dosyayı kapatmanızı beklemek için Visual Studio Code zorlamak üzere geçirmek isteyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="eecb3-207">If your default editor is Visual Studio Code, you'll usually want to pass the `-w` or `--wait` argument to force Visual Studio Code to wait for you to close the file before returning.</span></span>

### <a name="set-the-openapi-description-search-paths"></a><span data-ttu-id="eecb3-208">Openapı açıklaması arama yollarını ayarlama</span><span class="sxs-lookup"><span data-stu-id="eecb3-208">Set the OpenAPI Description search paths</span></span>

<span data-ttu-id="eecb3-209">Varsayılan olarak, HttpRepl, komutu seçeneği olmadan yürütürken Openapı açıklamasını bulmak için kullandığı bir göreli yollar kümesine sahiptir `connect` `--openapi` .</span><span class="sxs-lookup"><span data-stu-id="eecb3-209">By default, the HttpRepl has a set of relative paths that it uses to find the OpenAPI description when executing the `connect` command without the `--openapi` option.</span></span> <span data-ttu-id="eecb3-210">Bu göreli yollar, komutta belirtilen kök ve taban yollarla birleştirilir `connect` .</span><span class="sxs-lookup"><span data-stu-id="eecb3-210">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="eecb3-211">Varsayılan göreli yollar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="eecb3-211">The default relative paths are:</span></span>

- <span data-ttu-id="eecb3-212">*Üzerindeswagger.js*</span><span class="sxs-lookup"><span data-stu-id="eecb3-212">*swagger.json*</span></span>
- <span data-ttu-id="eecb3-213">*Swagger/v1/swagger.jsaçık*</span><span class="sxs-lookup"><span data-stu-id="eecb3-213">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="eecb3-214">*/swagger.js*</span><span class="sxs-lookup"><span data-stu-id="eecb3-214">*/swagger.json*</span></span>
- <span data-ttu-id="eecb3-215">*/Swagger/v1/swagger.js*</span><span class="sxs-lookup"><span data-stu-id="eecb3-215">*/swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="eecb3-216">*Üzerindeopenapi.js*</span><span class="sxs-lookup"><span data-stu-id="eecb3-216">*openapi.json*</span></span>
- <span data-ttu-id="eecb3-217">*/openapi.js*</span><span class="sxs-lookup"><span data-stu-id="eecb3-217">*/openapi.json*</span></span>

<span data-ttu-id="eecb3-218">Ortamınızda farklı bir arama yolları kümesi kullanmak için `swagger.searchPaths` tercihi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="eecb3-218">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="eecb3-219">Değer, göreli yolların kanal ile ayrılmış bir listesi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="eecb3-219">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="eecb3-220">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-220">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

<span data-ttu-id="eecb3-221">Varsayılan listeyi tamamen değiştirmek yerine, liste yol ekleyerek veya kaldırarak da değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="eecb3-221">Instead of replacing the default list altogether, the list can also be modified by adding or removing paths.</span></span>

<span data-ttu-id="eecb3-222">Varsayılan listeye bir veya daha fazla arama yolu eklemek için `swagger.addToSearchPaths` tercihi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="eecb3-222">To add one or more search paths to the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="eecb3-223">Değer, göreli yolların kanal ile ayrılmış bir listesi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="eecb3-223">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="eecb3-224">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-224">For example:</span></span>

```console
pref set swagger.addToSearchPaths "openapi/v2/openapi.json|openapi/v3/openapi.json"
```

<span data-ttu-id="eecb3-225">Varsayılan listeden bir veya daha fazla arama yolunu kaldırmak için `swagger.addToSearchPaths` tercihi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="eecb3-225">To remove one or more search paths from the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="eecb3-226">Değer, göreli yolların kanal ile ayrılmış bir listesi olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="eecb3-226">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="eecb3-227">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-227">For example:</span></span>

```console
pref set swagger.removeFromSearchPaths "swagger.json|/swagger.json"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="eecb3-228">HTTP GET isteklerini test etme</span><span class="sxs-lookup"><span data-stu-id="eecb3-228">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="eecb3-229">Özeti</span><span class="sxs-lookup"><span data-stu-id="eecb3-229">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="eecb3-230">Arguments</span><span class="sxs-lookup"><span data-stu-id="eecb3-230">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="eecb3-231">Varsa, ilişkili denetleyici eylem yöntemi tarafından beklenen rota parametresi.</span><span class="sxs-lookup"><span data-stu-id="eecb3-231">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="eecb3-232">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="eecb3-232">Options</span></span>

<span data-ttu-id="eecb3-233">Komutu için aşağıdaki seçenekler kullanılabilir `get` :</span><span class="sxs-lookup"><span data-stu-id="eecb3-233">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="eecb3-234">Örnek</span><span class="sxs-lookup"><span data-stu-id="eecb3-234">Example</span></span>

<span data-ttu-id="eecb3-235">HTTP GET isteği vermek için:</span><span class="sxs-lookup"><span data-stu-id="eecb3-235">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="eecb3-236">`get`Komutu onu destekleyen bir uç noktada çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-236">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> get
    ```

    <span data-ttu-id="eecb3-237">Yukarıdaki komut aşağıdaki çıkış biçimini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="eecb3-237">The preceding command displays the following output format:</span></span>

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

1. <span data-ttu-id="eecb3-238">Komutuna bir parametre geçirerek belirli bir kaydı alın `get` :</span><span class="sxs-lookup"><span data-stu-id="eecb3-238">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people> get 2
    ```

    <span data-ttu-id="eecb3-239">Yukarıdaki komut aşağıdaki çıkış biçimini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="eecb3-239">The preceding command displays the following output format:</span></span>

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

## <a name="test-http-post-requests"></a><span data-ttu-id="eecb3-240">HTTP POST isteklerini test et</span><span class="sxs-lookup"><span data-stu-id="eecb3-240">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="eecb3-241">Özeti</span><span class="sxs-lookup"><span data-stu-id="eecb3-241">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="eecb3-242">Arguments</span><span class="sxs-lookup"><span data-stu-id="eecb3-242">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="eecb3-243">Varsa, ilişkili denetleyici eylem yöntemi tarafından beklenen rota parametresi.</span><span class="sxs-lookup"><span data-stu-id="eecb3-243">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="eecb3-244">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="eecb3-244">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="eecb3-245">Örnek</span><span class="sxs-lookup"><span data-stu-id="eecb3-245">Example</span></span>

<span data-ttu-id="eecb3-246">HTTP POST isteği vermek için:</span><span class="sxs-lookup"><span data-stu-id="eecb3-246">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="eecb3-247">`post`Komutu onu destekleyen bir uç noktada çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-247">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```

    <span data-ttu-id="eecb3-248">Önceki komutta, `Content-Type` http istek üst bilgisi, JSON türünde bir istek gövdesi medya türünü gösterecek şekilde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="eecb3-248">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="eecb3-249">Varsayılan metin Düzenleyicisi, HTTP istek gövdesini temsil eden bir JSON şablonuyla bir *. tmp* dosyası açar.</span><span class="sxs-lookup"><span data-stu-id="eecb3-249">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="eecb3-250">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-250">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="eecb3-251">Varsayılan metin düzenleyicisini ayarlamak için [varsayılan metin düzenleyiciyi ayarlama](#set-the-default-text-editor) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="eecb3-251">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="eecb3-252">JSON şablonunu model doğrulama gereksinimlerini karşılayacak şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-252">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="eecb3-253">*. Tmp* dosyasını kaydedin ve metin düzenleyicisini kapatın.</span><span class="sxs-lookup"><span data-stu-id="eecb3-253">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="eecb3-254">Aşağıdaki çıktı komut kabuğu 'nda görünür:</span><span class="sxs-lookup"><span data-stu-id="eecb3-254">The following output appears in the command shell:</span></span>

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

## <a name="test-http-put-requests"></a><span data-ttu-id="eecb3-255">HTTP PUT isteklerini test etme</span><span class="sxs-lookup"><span data-stu-id="eecb3-255">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="eecb3-256">Özeti</span><span class="sxs-lookup"><span data-stu-id="eecb3-256">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="eecb3-257">Arguments</span><span class="sxs-lookup"><span data-stu-id="eecb3-257">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="eecb3-258">Varsa, ilişkili denetleyici eylem yöntemi tarafından beklenen rota parametresi.</span><span class="sxs-lookup"><span data-stu-id="eecb3-258">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="eecb3-259">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="eecb3-259">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="eecb3-260">Örnek</span><span class="sxs-lookup"><span data-stu-id="eecb3-260">Example</span></span>

<span data-ttu-id="eecb3-261">HTTP PUT isteği vermek için:</span><span class="sxs-lookup"><span data-stu-id="eecb3-261">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="eecb3-262">*Isteğe bağlı*: `get` verileri değiştirmeden önce görüntülemek için komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-262">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

1. <span data-ttu-id="eecb3-263">`put`Komutu onu destekleyen bir uç noktada çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-263">Run the `put` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="eecb3-264">Önceki komutta, `Content-Type` http istek üst bilgisi, JSON türünde bir istek gövdesi medya türünü gösterecek şekilde ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="eecb3-264">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="eecb3-265">Varsayılan metin Düzenleyicisi, HTTP istek gövdesini temsil eden bir JSON şablonuyla bir *. tmp* dosyası açar.</span><span class="sxs-lookup"><span data-stu-id="eecb3-265">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="eecb3-266">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-266">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="eecb3-267">Varsayılan metin düzenleyicisini ayarlamak için [varsayılan metin düzenleyiciyi ayarlama](#set-the-default-text-editor) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="eecb3-267">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="eecb3-268">JSON şablonunu model doğrulama gereksinimlerini karşılayacak şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-268">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="eecb3-269">*. Tmp* dosyasını kaydedin ve metin düzenleyicisini kapatın.</span><span class="sxs-lookup"><span data-stu-id="eecb3-269">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="eecb3-270">Aşağıdaki çıktı komut kabuğu 'nda görünür:</span><span class="sxs-lookup"><span data-stu-id="eecb3-270">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="eecb3-271">*Isteğe bağlı*: `get` değişiklikleri görmek için bir komut verin.</span><span class="sxs-lookup"><span data-stu-id="eecb3-271">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="eecb3-272">Örneğin, metin düzenleyicisinde "Chraz" yazdıysanız, bir, `get` aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="eecb3-272">For example, if you typed "Cherry" in the text editor, a `get` returns the following output:</span></span>

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

## <a name="test-http-delete-requests"></a><span data-ttu-id="eecb3-273">HTTP SILME isteklerini test etme</span><span class="sxs-lookup"><span data-stu-id="eecb3-273">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="eecb3-274">Özeti</span><span class="sxs-lookup"><span data-stu-id="eecb3-274">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="eecb3-275">Arguments</span><span class="sxs-lookup"><span data-stu-id="eecb3-275">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="eecb3-276">Varsa, ilişkili denetleyici eylem yöntemi tarafından beklenen rota parametresi.</span><span class="sxs-lookup"><span data-stu-id="eecb3-276">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="eecb3-277">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="eecb3-277">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="eecb3-278">Örnek</span><span class="sxs-lookup"><span data-stu-id="eecb3-278">Example</span></span>

<span data-ttu-id="eecb3-279">HTTP SILME isteği vermek için:</span><span class="sxs-lookup"><span data-stu-id="eecb3-279">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="eecb3-280">*Isteğe bağlı*: `get` verileri değiştirmeden önce görüntülemek için komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-280">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

1. <span data-ttu-id="eecb3-281">`delete`Komutu onu destekleyen bir uç noktada çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-281">Run the `delete` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> delete 2
    ```

    <span data-ttu-id="eecb3-282">Yukarıdaki komut aşağıdaki çıkış biçimini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="eecb3-282">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="eecb3-283">*Isteğe bağlı*: `get` değişiklikleri görmek için bir komut verin.</span><span class="sxs-lookup"><span data-stu-id="eecb3-283">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="eecb3-284">Bu örnekte, bir, `get` aşağıdaki çıktıyı döndürür:</span><span class="sxs-lookup"><span data-stu-id="eecb3-284">In this example, a `get` returns the following output:</span></span>

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

## <a name="test-http-patch-requests"></a><span data-ttu-id="eecb3-285">HTTP PATCH isteklerini test etme</span><span class="sxs-lookup"><span data-stu-id="eecb3-285">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="eecb3-286">Özeti</span><span class="sxs-lookup"><span data-stu-id="eecb3-286">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="eecb3-287">Arguments</span><span class="sxs-lookup"><span data-stu-id="eecb3-287">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="eecb3-288">Varsa, ilişkili denetleyici eylem yöntemi tarafından beklenen rota parametresi.</span><span class="sxs-lookup"><span data-stu-id="eecb3-288">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="eecb3-289">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="eecb3-289">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="eecb3-290">HTTP HEAD isteklerini test etme</span><span class="sxs-lookup"><span data-stu-id="eecb3-290">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="eecb3-291">Özeti</span><span class="sxs-lookup"><span data-stu-id="eecb3-291">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="eecb3-292">Arguments</span><span class="sxs-lookup"><span data-stu-id="eecb3-292">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="eecb3-293">Varsa, ilişkili denetleyici eylem yöntemi tarafından beklenen rota parametresi.</span><span class="sxs-lookup"><span data-stu-id="eecb3-293">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="eecb3-294">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="eecb3-294">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="eecb3-295">Sınama HTTP SEÇENEKLERI istekleri</span><span class="sxs-lookup"><span data-stu-id="eecb3-295">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="eecb3-296">Özeti</span><span class="sxs-lookup"><span data-stu-id="eecb3-296">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="eecb3-297">Arguments</span><span class="sxs-lookup"><span data-stu-id="eecb3-297">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="eecb3-298">Varsa, ilişkili denetleyici eylem yöntemi tarafından beklenen rota parametresi.</span><span class="sxs-lookup"><span data-stu-id="eecb3-298">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="eecb3-299">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="eecb3-299">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="eecb3-300">HTTP istek üst bilgilerini ayarla</span><span class="sxs-lookup"><span data-stu-id="eecb3-300">Set HTTP request headers</span></span>

<span data-ttu-id="eecb3-301">Bir HTTP istek üst bilgisi ayarlamak için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-301">To set an HTTP request header, use one of the following approaches:</span></span>

* <span data-ttu-id="eecb3-302">HTTP isteğiyle satır içi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="eecb3-302">Set inline with the HTTP request.</span></span> <span data-ttu-id="eecb3-303">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-303">For example:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```
    
    <span data-ttu-id="eecb3-304">Önceki yaklaşımla, her ayrı HTTP istek üst bilgisi kendi seçeneğini gerektirir `-h` .</span><span class="sxs-lookup"><span data-stu-id="eecb3-304">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

* <span data-ttu-id="eecb3-305">HTTP isteğini göndermeden önce ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="eecb3-305">Set before sending the HTTP request.</span></span> <span data-ttu-id="eecb3-306">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-306">For example:</span></span>

    ```console
    https://localhost:5001/people> set header Content-Type application/json
    ```
    
    <span data-ttu-id="eecb3-307">Bir isteği göndermeden önce üst bilgi ayarlanırken üst bilgi, komut kabuğu oturumunun süresi boyunca ayarlanmış olarak kalır.</span><span class="sxs-lookup"><span data-stu-id="eecb3-307">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="eecb3-308">Üstbilgiyi temizlemek için boş bir değer sağlayın.</span><span class="sxs-lookup"><span data-stu-id="eecb3-308">To clear the header, provide an empty value.</span></span> <span data-ttu-id="eecb3-309">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-309">For example:</span></span>
    
    ```console
    https://localhost:5001/people> set header Content-Type
    ```

## <a name="test-secured-endpoints"></a><span data-ttu-id="eecb3-310">Güvenli uç noktaları test et</span><span class="sxs-lookup"><span data-stu-id="eecb3-310">Test secured endpoints</span></span>

<span data-ttu-id="eecb3-311">HttpRepl, güvenli uç noktaların sınamasını aşağıdaki yollarla destekler:</span><span class="sxs-lookup"><span data-stu-id="eecb3-311">The HttpRepl supports the testing of secured endpoints in the following ways:</span></span>

* <span data-ttu-id="eecb3-312">Oturum açmış kullanıcının varsayılan kimlik bilgileri aracılığıyla.</span><span class="sxs-lookup"><span data-stu-id="eecb3-312">Via the default credentials of the logged in user.</span></span>
* <span data-ttu-id="eecb3-313">HTTP istek üst bilgileri kullanılarak.</span><span class="sxs-lookup"><span data-stu-id="eecb3-313">Through the use of HTTP request headers.</span></span>

### <a name="default-credentials"></a><span data-ttu-id="eecb3-314">Varsayılan kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="eecb3-314">Default credentials</span></span>

<span data-ttu-id="eecb3-315">IIS 'de barındırılan ve Windows kimlik doğrulaması ile güvenliği sağlanan bir Web API 'sini göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="eecb3-315">Consider a web API you're testing that's hosted in IIS and secured with Windows authentication.</span></span> <span data-ttu-id="eecb3-316">Aracı çalıştıran kullanıcının kimlik bilgilerinin, test edilmekte olan HTTP uç noktaları üzerinden akmasını istiyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="eecb3-316">You want the credentials of the user running the tool to flow across to the HTTP endpoints being tested.</span></span> <span data-ttu-id="eecb3-317">Oturum açmış kullanıcının varsayılan kimlik bilgilerini geçirmek için:</span><span class="sxs-lookup"><span data-stu-id="eecb3-317">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="eecb3-318">Tercihi şu `httpClient.useDefaultCredentials` şekilde ayarlayın `true` :</span><span class="sxs-lookup"><span data-stu-id="eecb3-318">Set the `httpClient.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. <span data-ttu-id="eecb3-319">Web API 'sine başka bir istek göndermeden önce çıkın ve aracı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="eecb3-319">Exit and restart the tool before sending another request to the web API.</span></span>
 
### <a name="default-proxy-credentials"></a><span data-ttu-id="eecb3-320">Varsayılan proxy kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="eecb3-320">Default proxy credentials</span></span>

<span data-ttu-id="eecb3-321">Test ettiğiniz Web API 'sinin, Windows kimlik doğrulamasıyla güvenliği sağlanmış bir proxy 'nin arkasında olduğu bir senaryo düşünün.</span><span class="sxs-lookup"><span data-stu-id="eecb3-321">Consider a scenario in which the web API you're testing is behind a proxy secured with Windows authentication.</span></span> <span data-ttu-id="eecb3-322">Aracı çalıştıran kullanıcının kimlik bilgilerinin ara sunucuya akmasını istiyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="eecb3-322">You want the credentials of the user running the tool to flow to the proxy.</span></span> <span data-ttu-id="eecb3-323">Oturum açmış kullanıcının varsayılan kimlik bilgilerini geçirmek için:</span><span class="sxs-lookup"><span data-stu-id="eecb3-323">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="eecb3-324">Tercihi şu `httpClient.proxy.useDefaultCredentials` şekilde ayarlayın `true` :</span><span class="sxs-lookup"><span data-stu-id="eecb3-324">Set the `httpClient.proxy.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.proxy.useDefaultCredentials true
    ```

1. <span data-ttu-id="eecb3-325">Web API 'sine başka bir istek göndermeden önce çıkın ve aracı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="eecb3-325">Exit and restart the tool before sending another request to the web API.</span></span>

### <a name="http-request-headers"></a><span data-ttu-id="eecb3-326">HTTP istek üstbilgileri</span><span class="sxs-lookup"><span data-stu-id="eecb3-326">HTTP request headers</span></span>

<span data-ttu-id="eecb3-327">Desteklenen kimlik doğrulama ve yetkilendirme şemaları örnekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="eecb3-327">Examples of supported authentication and authorization schemes include:</span></span>

* <span data-ttu-id="eecb3-328">temel kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="eecb3-328">basic authentication</span></span>
* <span data-ttu-id="eecb3-329">JWT taşıyıcı belirteçleri</span><span class="sxs-lookup"><span data-stu-id="eecb3-329">JWT bearer tokens</span></span>
* <span data-ttu-id="eecb3-330">Özet kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="eecb3-330">digest authentication</span></span>

<span data-ttu-id="eecb3-331">Örneğin, aşağıdaki komutla bir uç noktaya bir taşıyıcı belirteci gönderebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="eecb3-331">For example, you can send a bearer token to an endpoint with the following command:</span></span>

```console
set header Authorization "bearer <TOKEN VALUE>"
```

<span data-ttu-id="eecb3-332">Azure 'da barındırılan bir uç noktaya erişmek veya [azure REST API](/rest/api/azure/)kullanmak için bir taşıyıcı belirtecine ihtiyacınız vardır.</span><span class="sxs-lookup"><span data-stu-id="eecb3-332">To access an Azure-hosted endpoint or to use the [Azure REST API](/rest/api/azure/), you need a bearer token.</span></span> <span data-ttu-id="eecb3-333">Azure [CLI](/cli/azure/)aracılığıyla Azure Aboneliğinize yönelik bir taşıyıcı belirteci almak için aşağıdaki adımları kullanın.</span><span class="sxs-lookup"><span data-stu-id="eecb3-333">Use the following steps to obtain a bearer token for your Azure subscription via the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="eecb3-334">HttpRepl, bir HTTP istek üstbilgisindeki taşıyıcı belirtecini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="eecb3-334">The HttpRepl sets the bearer token in an HTTP request header.</span></span> <span data-ttu-id="eecb3-335">Azure App Service Web Apps listesi alındı.</span><span class="sxs-lookup"><span data-stu-id="eecb3-335">A list of Azure App Service Web Apps is retrieved.</span></span>

1. <span data-ttu-id="eecb3-336">Azure 'da oturum açın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-336">Sign in to Azure:</span></span>

    ```azurecli
    az login
    ```

1. <span data-ttu-id="eecb3-337">Aşağıdaki komutla abonelik KIMLIĞINIZI alın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-337">Get your subscription ID with the following command:</span></span>

    ```azurecli
    az account show --query id
    ```

1. <span data-ttu-id="eecb3-338">Abonelik KIMLIĞINIZI kopyalayın ve şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-338">Copy your subscription ID and run the following command:</span></span>

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. <span data-ttu-id="eecb3-339">Aşağıdaki komutla taşıyıcı belirtecinizi alın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-339">Get your bearer token with the following command:</span></span>

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. <span data-ttu-id="eecb3-340">HttpRepl aracılığıyla Azure REST API bağlanma:</span><span class="sxs-lookup"><span data-stu-id="eecb3-340">Connect to the Azure REST API via the HttpRepl:</span></span>

    ```console
    httprepl https://management.azure.com
    ```

1. <span data-ttu-id="eecb3-341">`Authorization`Http istek üst bilgisini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-341">Set the `Authorization` HTTP request header:</span></span>

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. <span data-ttu-id="eecb3-342">Aboneliğe gidin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-342">Navigate to the subscription:</span></span>

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. <span data-ttu-id="eecb3-343">Aboneliğinizin Azure App Service Web Apps bir listesini alın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-343">Get a list of your subscription's Azure App Service Web Apps:</span></span>

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    <span data-ttu-id="eecb3-344">Aşağıdaki yanıt görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="eecb3-344">The following response is displayed:</span></span>

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

## <a name="toggle-http-request-display"></a><span data-ttu-id="eecb3-345">HTTP istek görüntüsünü değiştirme</span><span class="sxs-lookup"><span data-stu-id="eecb3-345">Toggle HTTP request display</span></span>

<span data-ttu-id="eecb3-346">Varsayılan olarak, gönderilmekte olan HTTP isteğinin görüntüsü bastırılır.</span><span class="sxs-lookup"><span data-stu-id="eecb3-346">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="eecb3-347">Komut kabuğu oturumunun süresi boyunca ilgili ayarı değiştirmek mümkündür.</span><span class="sxs-lookup"><span data-stu-id="eecb3-347">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="eecb3-348">İstek görüntülemesini etkinleştir</span><span class="sxs-lookup"><span data-stu-id="eecb3-348">Enable request display</span></span>

<span data-ttu-id="eecb3-349">Komutunu çalıştırarak gönderilmekte olan HTTP isteğini görüntüleyin `echo on` .</span><span class="sxs-lookup"><span data-stu-id="eecb3-349">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="eecb3-350">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-350">For example:</span></span>

```console
https://localhost:5001/people> echo on
Request echoing is on
```

<span data-ttu-id="eecb3-351">Geçerli oturumdaki sonraki HTTP istekleri, istek üst bilgilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="eecb3-351">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="eecb3-352">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-352">For example:</span></span>

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

### <a name="disable-request-display"></a><span data-ttu-id="eecb3-353">İstek görüntüsünü devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="eecb3-353">Disable request display</span></span>

<span data-ttu-id="eecb3-354">Komutunu çalıştırarak gönderilen HTTP isteğinin görüntülenmesini gizleyin `echo off` .</span><span class="sxs-lookup"><span data-stu-id="eecb3-354">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="eecb3-355">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-355">For example:</span></span>

```console
https://localhost:5001/people> echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="eecb3-356">Betik çalıştırma</span><span class="sxs-lookup"><span data-stu-id="eecb3-356">Run a script</span></span>

<span data-ttu-id="eecb3-357">Aynı HttpRepl komutları kümesini sıklıkla yürütüyorsanız bunları bir metin dosyasında depolamayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="eecb3-357">If you frequently execute the same set of HttpRepl commands, consider storing them in a text file.</span></span> <span data-ttu-id="eecb3-358">Dosyadaki komutlar, komut satırında el ile yürütülen komutlarla aynı formu alır.</span><span class="sxs-lookup"><span data-stu-id="eecb3-358">Commands in the file take the same form as commands executed manually on the command line.</span></span> <span data-ttu-id="eecb3-359">Komutlar, komutu kullanılarak toplanmış bir biçimde yürütülebilir `run` .</span><span class="sxs-lookup"><span data-stu-id="eecb3-359">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="eecb3-360">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-360">For example:</span></span>

1. <span data-ttu-id="eecb3-361">Yeni satır için ayrılmış komutlar kümesini içeren bir metin dosyası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="eecb3-361">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="eecb3-362">Göstermek için aşağıdaki komutları içeren bir *people-script.txt* dosyası göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="eecb3-362">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="eecb3-363">`run`Metin dosyasının yolunu geçirerek komutunu yürütün.</span><span class="sxs-lookup"><span data-stu-id="eecb3-363">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="eecb3-364">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="eecb3-364">For example:</span></span>

    ```console
    https://localhost:5001/> run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="eecb3-365">Aşağıdaki çıkış görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="eecb3-365">The following output appears:</span></span>

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

## <a name="clear-the-output"></a><span data-ttu-id="eecb3-366">Çıktıyı temizle</span><span class="sxs-lookup"><span data-stu-id="eecb3-366">Clear the output</span></span>

<span data-ttu-id="eecb3-367">Komut kabuğuna yazılan tüm çıktıyı HttpRepl aracı ile kaldırmak için `clear` veya `cls` komutunu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="eecb3-367">To remove all output written to the command shell by the HttpRepl tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="eecb3-368">Göstermek için komut kabuğu 'nun aşağıdaki çıktıyı içerdiğini düşünün:</span><span class="sxs-lookup"><span data-stu-id="eecb3-368">To illustrate, imagine the command shell contains the following output:</span></span>

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

<span data-ttu-id="eecb3-369">Çıktıyı temizlemek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="eecb3-369">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/> clear
```

<span data-ttu-id="eecb3-370">Yukarıdaki komutu çalıştırdıktan sonra, komut kabuğu yalnızca şu çıktıyı içerir:</span><span class="sxs-lookup"><span data-stu-id="eecb3-370">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/>
```

## <a name="additional-resources"></a><span data-ttu-id="eecb3-371">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="eecb3-371">Additional resources</span></span>

* [<span data-ttu-id="eecb3-372">REST API istekleri</span><span class="sxs-lookup"><span data-stu-id="eecb3-372">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="eecb3-373">HttpRepl GitHub deposu</span><span class="sxs-lookup"><span data-stu-id="eecb3-373">HttpRepl GitHub repository</span></span>](https://github.com/dotnet/HttpRepl)
* [<span data-ttu-id="eecb3-374">Visual Studio 'Yu HttpRepl 'yi başlatmak için yapılandırma</span><span class="sxs-lookup"><span data-stu-id="eecb3-374">Configure Visual Studio to launch HttpRepl</span></span>](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/#configure-visual-studio-for-windows-to-launch-httprepl-on-f5)
* [<span data-ttu-id="eecb3-375">HttpRepl 'yi başlatmak için Visual Studio Code yapılandırma</span><span class="sxs-lookup"><span data-stu-id="eecb3-375">Configure Visual Studio Code to launch HttpRepl</span></span>](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/#configure-visual-studio-code-to-launch-httprepl-on-debug)
* [<span data-ttu-id="eecb3-376">HttpRepl 'yi başlatmak için Mac için Visual Studio yapılandırma</span><span class="sxs-lookup"><span data-stu-id="eecb3-376">Configure Visual Studio for Mac to launch HttpRepl</span></span>](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/#configure-visual-studio-for-mac-to-launch-httprepl-as-a-custom-tool)
