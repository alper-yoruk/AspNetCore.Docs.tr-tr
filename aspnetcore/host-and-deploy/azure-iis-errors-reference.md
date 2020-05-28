---
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a>ASP.NET Core ile Azure App Service ve IIS için ortak hatalar başvurusu

::: moniker range=">= aspnetcore-2.2"

Bu konuda yaygın hatalar açıklanmakta ve Azure Apps hizmetinde ve IIS 'de ASP.NET Core uygulamalar barındırırken belirli hatalar için sorun giderme önerileri sunulmaktadır.

Genel sorun giderme kılavuzu için bkz <xref:test/troubleshoot-azure-iis> ..

Aşağıdaki bilgileri toplayın:

* Tarayıcı davranışı (durum kodu ve hata iletisi)
* Uygulama olay günlüğü girdileri
  * Azure App Service: bkz <xref:test/troubleshoot-azure-iis> ..
  * IIS
    1. **Windows** menüsünde **Başlat** ' ı seçin, *Olay Görüntüleyicisi*yazın ve **ENTER**tuşuna basın.
    1. **Olay Görüntüleyicisi** açıldıktan sonra, kenar çubuğunda **Windows günlükleri** > **uygulaması** ' nı genişletin.
* ASP.NET Core Modülü stdout ve hata ayıklama günlüğü girdileri
  * Azure App Service: bkz <xref:test/troubleshoot-azure-iis> ..
  * IIS: ASP.NET Core modülü konusunun [günlük oluşturma ve yeniden yönlendirme](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) ve [Gelişmiş tanılama günlükleri](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) bölümündeki yönergeleri izleyin.

Hata bilgilerini aşağıdaki yaygın hatalarla karşılaştırın. Bir eşleşme bulunursa, sorun giderme talimatını izleyin.

Bu konudaki hataların listesi ayrıntılı değildir. Burada listelenmeyen bir hatayla karşılaşırsanız, bu konunun en altındaki **içerik geri bildirim** düğmesini kullanarak yeni bir sorun açın ve hatayı yeniden oluşturma hakkında ayrıntılı yönergeler kullanın.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>İşletim sistemi yükseltmesi 32-bit ASP.NET Core modülünü kaldırdı

**Uygulama günlüğü:** **C:\windows\system32\inetsrv\aspnetcore.dll** modül dll 'si yüklenemedi. Veriler hatadır.

Sorun Giderme:

Bir işletim sistemi yükseltmesi sırasında **C:\Windows\SysWOW64\inetsrv** dizininde işletim sistemi olmayan dosyalar korunmaz. ASP.NET Core modülü bir işletim sistemi yükseltmesinden önce yüklendiyse ve sonra herhangi bir uygulama havuzu bir işletim sistemi yükseltmesinden sonra 32 bit modda çalıştıktan sonra bu sorunla karşılaşılmıştır. Bir işletim sistemi yükseltmesinden sonra ASP.NET Core modülünü onarın. Bkz. [.NET Core barındırma paketi 'Ni yüklemeyi](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle). Yükleyici çalıştırıldığında **Onar** ' ı seçin.

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a>Eksik site uzantısı, 32-bit (x86) ve 64-bit (x64) site uzantıları yüklü veya yanlış işlem bit genişliği ayarlanmış

*Azure Uygulama Hizmetleri tarafından barındırılan uygulamalar için geçerlidir.*

* **Tarayıcı:** HTTP hatası 500,0-Işlem Içi Işleyici yükleme hatası

* **Uygulama günlüğü:** InProcess istek işleyicisini bulmak için hostfxr çağırma hiçbir yerel bağımlılığı bulamamadan başarısız oldu. InProcess istek işleyicisi bulunamadı. Hostfxr çağırmadan yakalanan çıkış: herhangi bir uyumlu çerçeve sürümü bulmak mümkün değildi. Belirtilen ' Microsoft. AspNetCore. App ' çerçevesi, ' {VERSION}-Preview-' sürümü \* bulunamadı. '/LM/W3SVC/1416782824/ROOT ' uygulaması başlatılamadı, hata kodu ' 0x8000FFFF '.

* **ASP.NET Core modülü stdout günlüğü:** Uyumlu bir çerçeve sürümü bulmak mümkün değildi. Belirtilen ' Microsoft. AspNetCore. App ' çerçevesi, ' {VERSION}-Preview-' sürümü \* bulunamadı.

* **ASP.NET Core modülü hata ayıklama günlüğü:** InProcess istek işleyicisini bulmak için hostfxr çağırma hiçbir yerel bağımlılığı bulamamadan başarısız oldu. Büyük olasılıkla uygulamanın yanlış yapılandırılmış olduğu anlamına gelir, lütfen uygulamanın hedeflediği ve makinede yüklü olduğu Microsoft. NetCore. App ve Microsoft. AspNetCore. app sürümlerini denetleyin. Başarısız HRESULT döndürüldü: 0x8000FFFF. InProcess istek işleyicisi bulunamadı. Uyumlu bir çerçeve sürümü bulmak mümkün değildi. Belirtilen ' Microsoft. AspNetCore. App ' çerçevesi, ' {VERSION}-Preview-' sürümü \* bulunamadı.

Sorun Giderme:

* Uygulamayı bir önizleme çalışma zamanı üzerinde çalıştırıyorsanız, uygulamanın ve uygulamanın çalışma zamanının bit durumuyla eşleşen 32-bit (x86) **veya** 64 bit (x64) site uzantısını da yükler. **Uzantı veya birden çok çalışma zamanı sürümünü yüklemeyin.**

  * ASP.NET Core {RUNTIME VERSION} (x86) çalışma zamanı
  * ASP.NET Core {RUNTIME VERSION} (x64) çalışma zamanı

  Uygulamayı yeniden başlatın. Uygulamanın yeniden başlatılması için birkaç saniye bekleyin.

* Uygulamayı bir önizleme çalışma zamanında çalıştırmak ve 32-bit (x86) ve 64 bit (x64) [site uzantıları](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) yüklüyse, uygulamanın bit durumuyla eşleşmeyen site uzantısını kaldırın. Site uzantısını kaldırdıktan sonra uygulamayı yeniden başlatın. Uygulamanın yeniden başlatılması için birkaç saniye bekleyin.

* Uygulamayı bir önizleme çalışma zamanında çalıştırmak ve site uzantısının bit kullanımı uygulamayla eşleşiyorsa, önizleme sitesi uzantısının *çalışma zamanı sürümünün* uygulamanın çalışma zamanı sürümüyle eşleştiğini doğrulayın.

* **Uygulamanın uygulama ayarlarındaki** **platformunun** uygulamanın bit durumuyla eşleştiğinden emin olun.

Daha fazla bilgi için bkz. <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>X86 uygulaması dağıtıldı, ancak uygulama havuzu 32-bit uygulamalar için etkinleştirilmemiş

* **Tarayıcı:** HTTP hatası 500,30-Işlem Içi Işlem başlatma hatası

* **Uygulama günlüğü:** ' {PATH} ' fiziksel köküne sahip '/LM/W3SVC/5/ROOT ' uygulaması beklenmeyen yönetilen özel duruma ulaştı, özel durum kodu = ' 0xe0434352 '. Daha fazla bilgi için lütfen stderr günlüklerine bakın. ' {PATH} ' fiziksel köküne sahip '/LM/W3SVC/5/ROOT ' uygulaması clr ve yönetilen uygulamayı yükleyemedi. CLR Worker iş parçacığından erken çıkıldı

* **ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulur ancak boştur.

* **ASP.NET Core modülü hata ayıklama günlüğü:** Başarısız HRESULT döndürüldü: 0x8007023e

Bu senaryo, kendi içinde bulunan bir uygulama yayımlanırken SDK tarafından yakalanarak yapılır. RID platform hedefi ile eşleşmezse SDK bir hata üretir (örneğin, `win10-x64` `<PlatformTarget>x86</PlatformTarget>` Proje dosyasında ile RID).

Sorun Giderme:

X86 çerçevesine bağımlı bir dağıtım () için `<PlatformTarget>x86</PlatformTarget>` , 32 bitlik uygulamalar IÇIN IIS uygulama havuzunu etkinleştirin. IIS Yöneticisi 'nde, uygulama havuzunun **Gelişmiş ayarlarını** açın ve **32 bitlik uygulamaları** **doğru**olarak etkinleştir ayarını yapın.

## <a name="platform-conflicts-with-rid"></a>Platform RID ile çakışıyor

* **Tarayıcı:** HTTP hatası 502,5-Işlem hatası

* **Uygulama günlüğü:** ' C: Path} fiziksel köküne sahip ' MACHıNE/WEBROOT/APPHOST/{ASSEMBLY} ' uygulaması, \{ \' ' "C: \{ Path} {Assembly} komut satırı ile işlem başlatamadı. { exe | dll} "', ErrorCode = ' 0x80004005: ff.

* **ASP.NET Core modülü stdout günlüğü:** İşlenmeyen özel durum: System. BadImageFormatException: ' {ASSEMBLY}. dll ' dosyası veya bütünleştirilmiş kodu yüklenemedi. Bir programı hatalı biçimde yükleme girişiminde bulunuldu.

Sorun Giderme:

* Uygulamanın Kestrel üzerinde yerel olarak çalıştığını doğrulayın. İşlem hatası, uygulamanın içindeki bir sorunun sonucu olabilir. Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.

* Bu özel durum, bir uygulamayı yükseltirken ve daha yeni derlemeler dağıtıldığında bir Azure Apps dağıtımı için oluşursa, önceki dağıtımdan tüm dosyaları el ile silin. Yükseltilmiş bir uygulama dağıtımında, kalan uyumsuz derlemeler bir `System.BadImageFormatException` özel durumla sonuçlanabilir.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>URI uç noktası yanlış veya durdurulmuş Web sitesi

* **Tarayıcı:** ERR_CONNECTION_REFUSED **--veya--** bağlantı kurulamıyor

* **Uygulama günlüğü:** Giriş yok

* **ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulmaz.

* **ASP.NET Core modülü hata ayıklama günlüğü:** Günlük dosyası oluşturulmaz.

Sorun Giderme:

* Uygulamanın kullanımda olduğu doğru URI uç noktasını onaylayın. Bağlamaları denetleyin.

* IIS Web sitesinin *durdurulmuş* durumda olmadığını doğrulayın.

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>CoreWebEngine veya W3SVC sunucu özellikleri devre dışı

**Işletim sistemi özel durumu:** ASP.NET Core modülünü kullanmak için IIS 7,0 CoreWebEngine ve W3SVC özelliklerinin yüklü olması gerekir.

Sorun Giderme:

Uygun rol ve özelliklerin etkinleştirildiğini doğrulayın. Bkz. [IIS yapılandırması](xref:host-and-deploy/iis/index#iis-configuration).

## <a name="incorrect-website-physical-path-or-app-missing"></a>Yanlış web sitesi fiziksel yolu veya uygulaması eksik

* **Tarayıcı:** 403 Yasak-erişim reddedildi **--veya--** 403,14 yasak-Web sunucusu bu dizinin içeriğini listebir şekilde yapılandırılmıştır.

* **Uygulama günlüğü:** Giriş yok

* **ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulmaz.

* **ASP.NET Core modülü hata ayıklama günlüğü:** Günlük dosyası oluşturulmaz.

Sorun Giderme:

IIS Web sitesi **temel ayarları** ve fiziksel uygulama klasörü ' ne bakın. Uygulamanın IIS Web sitesi **fiziksel yolundaki**klasörde olduğunu doğrulayın.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>Yanlış rol, ASP.NET Core modülü yüklü değil veya yanlış izinler

* **Tarayıcı:** 500,19 Iç sunucu hatası-sayfanın ilgili yapılandırma verileri geçersiz olduğundan istenen sayfaya erişilemiyor. **--Veya--** Bu sayfa görüntülenemiyor

* **Uygulama günlüğü:** Giriş yok

* **ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulmaz.

* **ASP.NET Core modülü hata ayıklama günlüğü:** Günlük dosyası oluşturulmaz.

Sorun Giderme:

* Doğru rolün etkin olduğunu onaylayın. Bkz. [IIS yapılandırması](xref:host-and-deploy/iis/index#iis-configuration).

* **Programlar & Özellikler** veya **uygulamalar & özellikleri** açın ve **Windows Server barındırma** 'nın yüklü olduğunu doğrulayın. Yüklü programlar listesinde **Windows Server barındırma** yoksa, .NET Core barındırma paketi ' ni indirip yükleyin.

  [Geçerli .NET Core barındırma paketi yükleyicisi (doğrudan indirme)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Daha fazla bilgi için bkz. [.NET Core barındırma paketini yüklemeye](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

* **Uygulama havuzu** > **işlem modelinin** > **Identity** **applicationpokaydentity** olarak ayarlandığından veya özel kimliğin uygulamanın dağıtım klasörüne erişmek için doğru izinlere sahip olduğundan emin olun.

* ASP.NET Core barındırma paketini kaldırdıysanız ve barındırma paketinin önceki bir sürümünü yüklediyseniz *ApplicationHost. config* dosyası ASP.NET Core modülü için bir bölüm içermez. *ApplicationHost. config* dosyasını *% windir%/system32/inetsrv/config* konumunda açın ve `<configuration><configSections><sectionGroup name="system.webServer">` bölüm grubunu bulun. Bölüm grubunda ASP.NET Core modülünün bölümü eksikse, Bölüm öğesini ekleyin:

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  Alternatif olarak, ASP.NET Core barındıran paketin en son sürümünü de yüklersiniz. En son sürüm, desteklenen ASP.NET Core uygulamalarla geriye dönük olarak uyumludur.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>Hatalı processPath, eksik yol değişkeni, barındırma paketi yüklü değil, sistem/IIS yeniden başlatılmadı, VC + + yeniden dağıtılabilir yüklü değil veya DotNet. exe erişim ihlali

* **Tarayıcı:** HTTP hatası 500,0-Işlem Içi Işleyici yükleme hatası

* **Uygulama günlüğü:** ' C: Path} fiziksel köküne sahip ' MACHıNE/WEBROOT/APPHOST/{ASSEMBLY} ' uygulaması, ' \{ \' "{...}" komut satırı ile işleme başlatılamadı ', ErrorCode = ' 0x80070002:0. ' {PATH} ' uygulaması başlatılamadı. ' {PATH} ' konumunda yürütülebilir dosya bulunamadı. '/LM/W3SVC/2/ROOT ' uygulaması başlatılamadı, hata kodu ' 0x8007023e '.

* **ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulmaz.

* **ASP.NET Core modülü hata ayıklama günlüğü:** Olay günlüğü: ' {PATH} ' uygulaması başlatılamadı. ' {PATH} ' konumunda yürütülebilir dosya bulunamadı. Başarısız HRESULT döndürüldü: 0x8007023e

Sorun Giderme:

* Uygulamanın Kestrel üzerinde yerel olarak çalıştığını doğrulayın. İşlem hatası, uygulamanın içindeki bir sorunun sonucu olabilir. Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.

* *processPath* `<aspNetCore>` Bir çerçeveye bağımlı dağıtım (FDD) veya *web.config* `dotnet` `.\{ASSEMBLY}.exe` [kendi kendine ait dağıtım (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)Için olduğunu doğrulamak üzere Web. config dosyasındaki öğesindeki processPath özniteliğini denetleyin.

* FDD için, *DotNet. exe* ' nin yol ayarları aracılığıyla erişilebilir olmayabilir. *C:\Program Files\dotnet \\ * dosyasının sistem yolu ayarlarında bulunduğunu onaylayın.

* FDD için, *DotNet. exe* ' yi uygulama havuzunun Kullanıcı kimliği için erişilebilir olmayabilir. Uygulama havuzu Kullanıcı kimliğinin *C:\Program Files\dotnet* dizinine erişimi olduğunu doğrulayın. *C:\Program Files\dotnet* ve uygulama dizinlerindeki uygulama havuzu Kullanıcı kimliği için yapılandırılmış reddetme kuralı olmadığını doğrulayın.

* Bir FDD dağıtılmış ve IIS 'nin yeniden başlatılmasına gerek kalmadan .NET Core yüklenmiş olabilir. Bir komut isteminden net **stop was/y** ve ardından **net start w3svc** ' i yürüterek sunucuyu YENIDEN başlatın ya da IIS 'yi yeniden başlatın.

* Bir FDD, barındırma sistemine .NET Core çalışma zamanı yüklenmeden dağıtılmış olabilir. .NET Core çalışma zamanı yüklenmemişse, sistemde **.NET Core barındırma paketi yükleyicisini** çalıştırın.

  [Geçerli .NET Core barındırma paketi yükleyicisi (doğrudan indirme)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Daha fazla bilgi için bkz. [.NET Core barındırma paketini yüklemeye](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

  Belirli bir çalışma zamanı gerekliyse, [.net download arşivleri](https://dotnet.microsoft.com/download/archives) ' nden çalışma zamanını indirin ve sisteme yükleyin. Bir komut isteminden net **stop idi** ve ardından **net start w3svc** ' i yürüterek SISTEMI yeniden başlatarak veya IIS 'yi yeniden başlatarak yüklemeyi doldurun.

## <a name="incorrect-arguments-of-aspnetcore-element"></a>Öğenin bağımsız değişkenleri yanlış \<aspNetCore>

* **Tarayıcı:** HTTP hatası 500,0-Işlem Içi Işleyici yükleme hatası

* **Uygulama günlüğü:** InProcess istek işleyicisini bulmak için hostfxr çağırma hiçbir yerel bağımlılığı bulamamadan başarısız oldu. Büyük olasılıkla uygulamanın yanlış yapılandırılmış olduğu anlamına gelir, lütfen uygulamanın hedeflediği ve makinede yüklü olduğu Microsoft. NetCore. App ve Microsoft. AspNetCore. app sürümlerini denetleyin. InProcess istek işleyicisi bulunamadı. Hostfxr çağırmadan yakalanan çıkış: DotNet SDK komutlarını çalıştırmak mı istediniz? Lütfen https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 '/LM/W3SVC/3/root ' uygulaması başlatılamadı, hata kodu: ' 0x8000FFFF '.

* **ASP.NET Core modülü stdout günlüğü:** DotNet SDK komutlarını çalıştırmak mı istediniz? Lütfen şu kaynaktan DotNet SDK 'Yı yüklemelisiniz:https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409

* **ASP.NET Core modülü hata ayıklama günlüğü:** InProcess istek işleyicisini bulmak için hostfxr çağırma hiçbir yerel bağımlılığı bulamamadan başarısız oldu. Büyük olasılıkla uygulamanın yanlış yapılandırılmış olduğu anlamına gelir, lütfen uygulamanın hedeflediği ve makinede yüklü olduğu Microsoft. NetCore. App ve Microsoft. AspNetCore. app sürümlerini denetleyin. Başarısız HRESULT döndürüldü: 0x8000FFFF, InProcess istek işleyicisi bulamadı. Hostfxr çağırmadan yakalanan çıkış: DotNet SDK komutlarını çalıştırmak mı istediniz? Lütfen kaynağından DotNet SDK 'Yı yükledikten sonra: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 başarısız HRESULT döndürüldü: 0x8000FFFF

Sorun Giderme:

* Uygulamanın Kestrel üzerinde yerel olarak çalıştığını doğrulayın. İşlem hatası, uygulamanın içindeki bir sorunun sonucu olabilir. Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.

* Web. config dosyasındaki *bağımsız değişkenler* özniteliğini inceleyerek, bir `<aspNetCore>` *web.config* `.\{ASSEMBLY}.dll` çerçeveye bağımlı dağıtım (FDD) veya (b) yok, boş bir dize ( `arguments=""` ) veya bağımsız `arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"` bir dağıtım (SCD) için uygulamanın bağımsız değişkenlerinin bir listesini () doğrulayın.

## <a name="missing-net-core-shared-framework"></a>Eksik .NET Core paylaşılan çerçevesi

* **Tarayıcı:** HTTP hatası 500,0-Işlem Içi Işleyici yükleme hatası

* **Uygulama günlüğü:** InProcess istek işleyicisini bulmak için hostfxr çağırma hiçbir yerel bağımlılığı bulamamadan başarısız oldu. Büyük olasılıkla uygulamanın yanlış yapılandırılmış olduğu anlamına gelir, lütfen uygulamanın hedeflediği ve makinede yüklü olduğu Microsoft. NetCore. App ve Microsoft. AspNetCore. app sürümlerini denetleyin. InProcess istek işleyicisi bulunamadı. Hostfxr çağırmadan yakalanan çıkış: herhangi bir uyumlu çerçeve sürümü bulmak mümkün değildi. Belirtilen ' Microsoft. AspNetCore. App ' çerçevesi, ' {VERSION} ' sürümü bulunamadı.

'/LM/W3SVC/5/ROOT ' uygulaması başlatılamadı, hata kodu ' 0x8000FFFF '.

* **ASP.NET Core modülü stdout günlüğü:** Uyumlu bir çerçeve sürümü bulmak mümkün değildi. Belirtilen ' Microsoft. AspNetCore. App ' çerçevesi, ' {VERSION} ' sürümü bulunamadı.

* **ASP.NET Core modülü hata ayıklama günlüğü:** Başarısız HRESULT döndürüldü: 0x8000FFFF

Sorun Giderme:

Çerçeveye bağımlı bir dağıtım (FDD) için, sistemde doğru çalışma zamanının yüklü olduğunu doğrulayın.

## <a name="stopped-application-pool"></a>Uygulama havuzu durduruldu

* **Tarayıcı:** 503 Hizmet kullanılamıyor

* **Uygulama günlüğü:** Giriş yok

* **ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulmaz.

* **ASP.NET Core modülü hata ayıklama günlüğü:** Günlük dosyası oluşturulmaz.

Sorun Giderme:

Uygulama havuzunun *durdurulmuş* durumda olmadığını onaylayın.

## <a name="sub-application-includes-a-handlers-section"></a>Alt uygulama bir bölüm içerir \<handlers>

* **Tarayıcı:** HTTP hatası 500,19-Iç sunucu hatası

* **Uygulama günlüğü:** Giriş yok

* **ASP.NET Core modülü stdout günlüğü:** Kök uygulamanın günlük dosyası oluşturulur ve normal işlemi gösterir. Alt uygulamanın günlük dosyası oluşturulmaz.

* **ASP.NET Core modülü hata ayıklama günlüğü:** Kök uygulamanın günlük dosyası oluşturulur ve normal işlemi gösterir. Alt uygulamanın günlük dosyası oluşturulmaz.

Sorun Giderme:

Alt uygulamanın *Web. config* dosyasının bir `<handlers>` bölüm içermediğinden veya alt uygulamanın üst uygulamanın işleyicilerini almadığından emin olun.

`<system.webServer>` *Web. config* dosyasının üst uygulamanın bölümü bir öğesinin içine yerleştirilir `<location>` . <xref:System.Configuration.SectionInformation.InheritInChildApplications*>Özelliği, `false` öğesi içinde belirtilen ayarların [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) üst uygulamanın bir alt dizininde bulunan uygulamalar tarafından devralınmadığını belirtmek için olarak ayarlanır. Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module>.

## <a name="stdout-log-path-incorrect"></a>stdout günlük yolu yanlış

* **Tarayıcı:** Uygulama normal olarak yanıt verir.

* **Uygulama günlüğü:** C:\Program Files\IIS\Asp.Net Core Module\v2\aspnetcorev2.dll' de stdout yeniden yönlendirmesi başlatılamadı. Özel durum iletisi: {PATH} \aspnetcoremodulev2\commonlib\fileoutputmanager.cpp: 84 konumunda HRESULT 0x80070005 döndürüldü. C:\Program Files\IIS\Asp.Net Core Module\v2\aspnetcorev2.dll' de stdout yeniden yönlendirmesi durdurulamadı. Özel durum iletisi: HRESULT 0x80070002 {PATH} konumunda döndürüldü. {PATH} \ aspnetcorev2_inprocess. dll içinde stdout yeniden yönlendirmesi başlatılamadı.

* **ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulmaz.

* **ASP.NET Core modülü hata ayıklama günlüğü:** C:\Program Files\IIS\Asp.Net Core Module\v2\aspnetcorev2.dll' de stdout yeniden yönlendirmesi başlatılamadı. Özel durum iletisi: {PATH} \aspnetcoremodulev2\commonlib\fileoutputmanager.cpp: 84 konumunda HRESULT 0x80070005 döndürüldü. C:\Program Files\IIS\Asp.Net Core Module\v2\aspnetcorev2.dll' de stdout yeniden yönlendirmesi durdurulamadı. Özel durum iletisi: HRESULT 0x80070002 {PATH} konumunda döndürüldü. {PATH} \ aspnetcorev2_inprocess. dll içinde stdout yeniden yönlendirmesi başlatılamadı.

Sorun Giderme:

* `stdoutLogFile` `<aspNetCore>` *Web. config* öğesinin öğesinde belirtilen yol yok. Daha fazla bilgi için bkz. [ASP.NET Core modülü: günlük oluşturma ve yeniden yönlendirme](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).

* Uygulama havuzu kullanıcısının stdout günlük yoluna yazma erişimi yok.

## <a name="application-configuration-general-issue"></a>Uygulama yapılandırması genel sorunu

* **Tarayıcı:** HTTP hatası 500,0-Işlem Içi Işleyici yükleme hatası **--veya--** HTTP hatası 500,30-Ancm Işlem Içi başlatma hatası

* **Uygulama günlüğü:** Değişken

* **ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulur ancak boş veya, uygulamanın noktası başarısız olana kadar normal girdilerle oluşturulur.

* **ASP.NET Core modülü hata ayıklama günlüğü:** Değişken

Sorun Giderme:

Büyük olasılıkla uygulama yapılandırması veya programlama sorunu nedeniyle işlem başlatılamadı.

Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Bu konuda yaygın hatalar açıklanmakta ve Azure Apps hizmetinde ve IIS 'de ASP.NET Core uygulamalar barındırırken belirli hatalar için sorun giderme önerileri sunulmaktadır.

Genel sorun giderme kılavuzu için bkz <xref:test/troubleshoot-azure-iis> ..

Aşağıdaki bilgileri toplayın:

* Tarayıcı davranışı (durum kodu ve hata iletisi)
* Uygulama olay günlüğü girdileri
  * Azure App Service: bkz <xref:test/troubleshoot-azure-iis> ..
  * IIS
    1. **Windows** menüsünde **Başlat** ' ı seçin, *Olay Görüntüleyicisi*yazın ve **ENTER**tuşuna basın.
    1. **Olay Görüntüleyicisi** açıldıktan sonra, kenar çubuğunda **Windows günlükleri** > **uygulaması** ' nı genişletin.
* ASP.NET Core Modülü stdout ve hata ayıklama günlüğü girdileri
  * Azure App Service: bkz <xref:test/troubleshoot-azure-iis> ..
  * IIS: ASP.NET Core modülü konusunun [günlük oluşturma ve yeniden yönlendirme](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) ve [Gelişmiş tanılama günlükleri](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) bölümündeki yönergeleri izleyin.

Hata bilgilerini aşağıdaki yaygın hatalarla karşılaştırın. Bir eşleşme bulunursa, sorun giderme talimatını izleyin.

Bu konudaki hataların listesi ayrıntılı değildir. Burada listelenmeyen bir hatayla karşılaşırsanız, bu konunun en altındaki **içerik geri bildirim** düğmesini kullanarak yeni bir sorun açın ve hatayı yeniden oluşturma hakkında ayrıntılı yönergeler kullanın.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>İşletim sistemi yükseltmesi 32-bit ASP.NET Core modülünü kaldırdı

**Uygulama günlüğü:** **C:\windows\system32\inetsrv\aspnetcore.dll** modül dll 'si yüklenemedi. Veriler hatadır.

Sorun Giderme:

Bir işletim sistemi yükseltmesi sırasında **C:\Windows\SysWOW64\inetsrv** dizininde işletim sistemi olmayan dosyalar korunmaz. ASP.NET Core modülü bir işletim sistemi yükseltmesinden önce yüklendiyse ve sonra herhangi bir uygulama havuzu bir işletim sistemi yükseltmesinden sonra 32 bit modda çalıştıktan sonra bu sorunla karşılaşılmıştır. Bir işletim sistemi yükseltmesinden sonra ASP.NET Core modülünü onarın. Bkz. [.NET Core barındırma paketi 'Ni yüklemeyi](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle). Yükleyici çalıştırıldığında **Onar** ' ı seçin.

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a>Eksik site uzantısı, 32-bit (x86) ve 64-bit (x64) site uzantıları yüklü veya yanlış işlem bit genişliği ayarlanmış

*Azure Uygulama Hizmetleri tarafından barındırılan uygulamalar için geçerlidir.*

* **Tarayıcı:** HTTP hatası 500,0-Işlem Içi Işleyici yükleme hatası

* **Uygulama günlüğü:** InProcess istek işleyicisini bulmak için hostfxr çağırma hiçbir yerel bağımlılığı bulamamadan başarısız oldu. InProcess istek işleyicisi bulunamadı. Hostfxr çağırmadan yakalanan çıkış: herhangi bir uyumlu çerçeve sürümü bulmak mümkün değildi. Belirtilen ' Microsoft. AspNetCore. App ' çerçevesi, ' {VERSION}-Preview-' sürümü \* bulunamadı. '/LM/W3SVC/1416782824/ROOT ' uygulaması başlatılamadı, hata kodu ' 0x8000FFFF '.

* **ASP.NET Core modülü stdout günlüğü:** Uyumlu bir çerçeve sürümü bulmak mümkün değildi. Belirtilen ' Microsoft. AspNetCore. App ' çerçevesi, ' {VERSION}-Preview-' sürümü \* bulunamadı.

Sorun Giderme:

* Uygulamayı bir önizleme çalışma zamanı üzerinde çalıştırıyorsanız, uygulamanın ve uygulamanın çalışma zamanının bit durumuyla eşleşen 32-bit (x86) **veya** 64 bit (x64) site uzantısını da yükler. **Uzantı veya birden çok çalışma zamanı sürümünü yüklemeyin.**

  * ASP.NET Core {RUNTIME VERSION} (x86) çalışma zamanı
  * ASP.NET Core {RUNTIME VERSION} (x64) çalışma zamanı

  Uygulamayı yeniden başlatın. Uygulamanın yeniden başlatılması için birkaç saniye bekleyin.

* Uygulamayı bir önizleme çalışma zamanında çalıştırmak ve 32-bit (x86) ve 64 bit (x64) [site uzantıları](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) yüklüyse, uygulamanın bit durumuyla eşleşmeyen site uzantısını kaldırın. Site uzantısını kaldırdıktan sonra uygulamayı yeniden başlatın. Uygulamanın yeniden başlatılması için birkaç saniye bekleyin.

* Uygulamayı bir önizleme çalışma zamanında çalıştırmak ve site uzantısının bit kullanımı uygulamayla eşleşiyorsa, önizleme sitesi uzantısının *çalışma zamanı sürümünün* uygulamanın çalışma zamanı sürümüyle eşleştiğini doğrulayın.

* **Uygulamanın uygulama ayarlarındaki** **platformunun** uygulamanın bit durumuyla eşleştiğinden emin olun.

Daha fazla bilgi için bkz. <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>X86 uygulaması dağıtıldı, ancak uygulama havuzu 32-bit uygulamalar için etkinleştirilmemiş

* **Tarayıcı:** HTTP hatası 500,30-Işlem Içi Işlem başlatma hatası

* **Uygulama günlüğü:** ' {PATH} ' fiziksel köküne sahip '/LM/W3SVC/5/ROOT ' uygulaması beklenmeyen yönetilen özel duruma ulaştı, özel durum kodu = ' 0xe0434352 '. Daha fazla bilgi için lütfen stderr günlüklerine bakın. ' {PATH} ' fiziksel köküne sahip '/LM/W3SVC/5/ROOT ' uygulaması clr ve yönetilen uygulamayı yükleyemedi. CLR Worker iş parçacığından erken çıkıldı

* **ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulur ancak boştur.

Bu senaryo, kendi içinde bulunan bir uygulama yayımlanırken SDK tarafından yakalanarak yapılır. RID platform hedefi ile eşleşmezse SDK bir hata üretir (örneğin, `win10-x64` `<PlatformTarget>x86</PlatformTarget>` Proje dosyasında ile RID).

Sorun Giderme:

X86 çerçevesine bağımlı bir dağıtım () için `<PlatformTarget>x86</PlatformTarget>` , 32 bitlik uygulamalar IÇIN IIS uygulama havuzunu etkinleştirin. IIS Yöneticisi 'nde, uygulama havuzunun **Gelişmiş ayarlarını** açın ve **32 bitlik uygulamaları** **doğru**olarak etkinleştir ayarını yapın.

## <a name="platform-conflicts-with-rid"></a>Platform RID ile çakışıyor

* **Tarayıcı:** HTTP hatası 502,5-Işlem hatası

* **Uygulama günlüğü:** ' C: Path} fiziksel köküne sahip ' MACHıNE/WEBROOT/APPHOST/{ASSEMBLY} ' uygulaması, \{ \' ' "C: \{ Path} {Assembly} komut satırı ile işlem başlatamadı. { exe | dll} "', ErrorCode = ' 0x80004005: ff.

* **ASP.NET Core modülü stdout günlüğü:** İşlenmeyen özel durum: System. BadImageFormatException: ' {ASSEMBLY}. dll ' dosyası veya bütünleştirilmiş kodu yüklenemedi. Bir programı hatalı biçimde yükleme girişiminde bulunuldu.

Sorun Giderme:

* Uygulamanın Kestrel üzerinde yerel olarak çalıştığını doğrulayın. İşlem hatası, uygulamanın içindeki bir sorunun sonucu olabilir. Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.

* Bu özel durum, bir uygulamayı yükseltirken ve daha yeni derlemeler dağıtıldığında bir Azure Apps dağıtımı için oluşursa, önceki dağıtımdan tüm dosyaları el ile silin. Yükseltilmiş bir uygulama dağıtımında, kalan uyumsuz derlemeler bir `System.BadImageFormatException` özel durumla sonuçlanabilir.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>URI uç noktası yanlış veya durdurulmuş Web sitesi

* **Tarayıcı:** ERR_CONNECTION_REFUSED **--veya--** bağlantı kurulamıyor

* **Uygulama günlüğü:** Giriş yok

* **ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulmaz.

Sorun Giderme:

* Uygulamanın kullanımda olduğu doğru URI uç noktasını onaylayın. Bağlamaları denetleyin.

* IIS Web sitesinin *durdurulmuş* durumda olmadığını doğrulayın.

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>CoreWebEngine veya W3SVC sunucu özellikleri devre dışı

**Işletim sistemi özel durumu:** ASP.NET Core modülünü kullanmak için IIS 7,0 CoreWebEngine ve W3SVC özelliklerinin yüklü olması gerekir.

Sorun Giderme:

Uygun rol ve özelliklerin etkinleştirildiğini doğrulayın. Bkz. [IIS yapılandırması](xref:host-and-deploy/iis/index#iis-configuration).

## <a name="incorrect-website-physical-path-or-app-missing"></a>Yanlış web sitesi fiziksel yolu veya uygulaması eksik

* **Tarayıcı:** 403 Yasak-erişim reddedildi **--veya--** 403,14 yasak-Web sunucusu bu dizinin içeriğini listebir şekilde yapılandırılmıştır.

* **Uygulama günlüğü:** Giriş yok

* **ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulmaz.

Sorun Giderme:

IIS Web sitesi **temel ayarları** ve fiziksel uygulama klasörü ' ne bakın. Uygulamanın IIS Web sitesi **fiziksel yolundaki**klasörde olduğunu doğrulayın.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>Yanlış rol, ASP.NET Core modülü yüklü değil veya yanlış izinler

* **Tarayıcı:** 500,19 Iç sunucu hatası-sayfanın ilgili yapılandırma verileri geçersiz olduğundan istenen sayfaya erişilemiyor. **--Veya--** Bu sayfa görüntülenemiyor

* **Uygulama günlüğü:** Giriş yok

* **ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulmaz.

Sorun Giderme:

* Doğru rolün etkin olduğunu onaylayın. Bkz. [IIS yapılandırması](xref:host-and-deploy/iis/index#iis-configuration).

* **Programlar & Özellikler** veya **uygulamalar & özellikleri** açın ve **Windows Server barındırma** 'nın yüklü olduğunu doğrulayın. Yüklü programlar listesinde **Windows Server barındırma** yoksa, .NET Core barındırma paketi ' ni indirip yükleyin.

  [Geçerli .NET Core barındırma paketi yükleyicisi (doğrudan indirme)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Daha fazla bilgi için bkz. [.NET Core barındırma paketini yüklemeye](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

* **Uygulama havuzu** > **işlem modelinin** > **Identity** **applicationpokaydentity** olarak ayarlandığından veya özel kimliğin uygulamanın dağıtım klasörüne erişmek için doğru izinlere sahip olduğundan emin olun.

* ASP.NET Core barındırma paketini kaldırdıysanız ve barındırma paketinin önceki bir sürümünü yüklediyseniz *ApplicationHost. config* dosyası ASP.NET Core modülü için bir bölüm içermez. *ApplicationHost. config* dosyasını *% windir%/system32/inetsrv/config* konumunda açın ve `<configuration><configSections><sectionGroup name="system.webServer">` bölüm grubunu bulun. Bölüm grubunda ASP.NET Core modülünün bölümü eksikse, Bölüm öğesini ekleyin:

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  Alternatif olarak, ASP.NET Core barındıran paketin en son sürümünü de yüklersiniz. En son sürüm, desteklenen ASP.NET Core uygulamalarla geriye dönük olarak uyumludur.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>Hatalı processPath, eksik yol değişkeni, barındırma paketi yüklü değil, sistem/IIS yeniden başlatılmadı, VC + + yeniden dağıtılabilir yüklü değil veya DotNet. exe erişim ihlali

* **Tarayıcı:** HTTP hatası 502,5-Işlem hatası

* **Uygulama günlüğü:** ' C: Path} fiziksel köküne sahip ' MACHıNE/WEBROOT/APPHOST/{ASSEMBLY} ' uygulaması, ' \{ \' "{...}" komut satırı ile işleme başlatılamadı ', ErrorCode = ' 0x80070002:0.

* **ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulur ancak boştur.

Sorun Giderme:

* Uygulamanın Kestrel üzerinde yerel olarak çalıştığını doğrulayın. İşlem hatası, uygulamanın içindeki bir sorunun sonucu olabilir. Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.

* *processPath* `<aspNetCore>` Bir çerçeveye bağımlı dağıtım (FDD) veya *web.config* `dotnet` `.\{ASSEMBLY}.exe` [kendi kendine ait dağıtım (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)Için olduğunu doğrulamak üzere Web. config dosyasındaki öğesindeki processPath özniteliğini denetleyin.

* FDD için, *DotNet. exe* ' nin yol ayarları aracılığıyla erişilebilir olmayabilir. *C:\Program Files\dotnet \\ * dosyasının sistem yolu ayarlarında bulunduğunu onaylayın.

* FDD için, *DotNet. exe* ' yi uygulama havuzunun Kullanıcı kimliği için erişilebilir olmayabilir. Uygulama havuzu Kullanıcı kimliğinin *C:\Program Files\dotnet* dizinine erişimi olduğunu doğrulayın. *C:\Program Files\dotnet* ve uygulama dizinlerindeki uygulama havuzu Kullanıcı kimliği için yapılandırılmış reddetme kuralı olmadığını doğrulayın.

* Bir FDD dağıtılmış ve IIS 'nin yeniden başlatılmasına gerek kalmadan .NET Core yüklenmiş olabilir. Bir komut isteminden net **stop was/y** ve ardından **net start w3svc** ' i yürüterek sunucuyu YENIDEN başlatın ya da IIS 'yi yeniden başlatın.

* Bir FDD, barındırma sistemine .NET Core çalışma zamanı yüklenmeden dağıtılmış olabilir. .NET Core çalışma zamanı yüklenmemişse, sistemde **.NET Core barındırma paketi yükleyicisini** çalıştırın.

  [Geçerli .NET Core barındırma paketi yükleyicisi (doğrudan indirme)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Daha fazla bilgi için bkz. [.NET Core barındırma paketini yüklemeye](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

  Belirli bir çalışma zamanı gerekliyse, [.net download arşivleri](https://dotnet.microsoft.com/download/archives) ' nden çalışma zamanını indirin ve sisteme yükleyin. Bir komut isteminden net **stop idi** ve ardından **net start w3svc** ' i yürüterek SISTEMI yeniden başlatarak veya IIS 'yi yeniden başlatarak yüklemeyi doldurun.

## <a name="incorrect-arguments-of-aspnetcore-element"></a>Öğenin bağımsız değişkenleri yanlış \<aspNetCore>

* **Tarayıcı:** HTTP hatası 502,5-Işlem hatası

* **Uygulama günlüğü:** ' C: Path} fiziksel köküne sahip ' MACHıNE/WEBROOT/APPHOST/{ASSEMBLY} ' uygulaması, \{ \' ' "DotNet" CommandLine ile işlemi başlatamadı. \{ ASSEMBLY}. dll ', ErrorCode = ' 0x80004005:80008081.

* **ASP.NET Core modülü stdout günlüğü:** Yürütülecek uygulama yok: ' yol \{ derlemesi}. dll '

Sorun Giderme:

* Uygulamanın Kestrel üzerinde yerel olarak çalıştığını doğrulayın. İşlem hatası, uygulamanın içindeki bir sorunun sonucu olabilir. Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.

* Web. config dosyasındaki *bağımsız değişkenler* özniteliğini inceleyerek, bir `<aspNetCore>` *web.config* `.\{ASSEMBLY}.dll` çerçeveye bağımlı dağıtım (FDD) veya (b) yok, boş bir dize ( `arguments=""` ) veya bağımsız `arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"` bir dağıtım (SCD) için uygulamanın bağımsız değişkenlerinin bir listesini () doğrulayın.

Sorun Giderme:

Çerçeveye bağımlı bir dağıtım (FDD) için, sistemde doğru çalışma zamanının yüklü olduğunu doğrulayın.

## <a name="stopped-application-pool"></a>Uygulama havuzu durduruldu

* **Tarayıcı:** 503 Hizmet kullanılamıyor

* **Uygulama günlüğü:** Giriş yok

* **ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulmaz.

Sorun Giderme:

Uygulama havuzunun *durdurulmuş* durumda olmadığını onaylayın.

## <a name="sub-application-includes-a-handlers-section"></a>Alt uygulama bir bölüm içerir \<handlers>

* **Tarayıcı:** HTTP hatası 500,19-Iç sunucu hatası

* **Uygulama günlüğü:** Giriş yok

* **ASP.NET Core modülü stdout günlüğü:** Kök uygulamanın günlük dosyası oluşturulur ve normal işlemi gösterir. Alt uygulamanın günlük dosyası oluşturulmaz.

Sorun Giderme:

Alt uygulamanın *Web. config* dosyasının bir bölüm içermediğinden emin olun `<handlers>` .

## <a name="stdout-log-path-incorrect"></a>stdout günlük yolu yanlış

* **Tarayıcı:** Uygulama normal olarak yanıt verir.

* **Uygulama günlüğü:** Uyarı: stdoutLogFile oluşturulamadı \\ mi? \{ YOL} \ path_doesnt_exist \ stdout_ {Işlem KIMLIĞI} _ {zaman DAMGASı}. günlük, hata kodu =-2147024893.

* **ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulmaz.

Sorun Giderme:

* `stdoutLogFile` `<aspNetCore>` *Web. config* öğesinin öğesinde belirtilen yol yok. Daha fazla bilgi için bkz. [ASP.NET Core modülü: günlük oluşturma ve yeniden yönlendirme](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).

* Uygulama havuzu kullanıcısının stdout günlük yoluna yazma erişimi yok.

## <a name="application-configuration-general-issue"></a>Uygulama yapılandırması genel sorunu

* **Tarayıcı:** HTTP hatası 502,5-Işlem hatası

* **Uygulama günlüğü:** Fiziksel kökü ' C: path} olan ' MACHıNE/WEBROOT/APPHOST/{ASSEMBLY} ' uygulaması, \{ \' commandLine ' "c: \{ Path} Assembly} ile oluşturulmuş işlem \{ . { exe | dll} "', ancak belirtilen ' {PORT} ' bağlantı noktasında kilitlendi veya yanıt vermedi ya da bu bağlantı noktası üzerinde dinleme yapamadı, ErrorCode = ' {ERROR CODE} '

* **ASP.NET Core modülü stdout günlüğü:** Günlük dosyası oluşturulur ancak boştur.

Sorun Giderme:

Büyük olasılıkla uygulama yapılandırması veya programlama sorunu nedeniyle işlem başlatılamadı.

Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end
