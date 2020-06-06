## <a name="troubleshoot"></a>Sorun giderme

### <a name="cookies-and-site-data"></a>Tanımlama bilgileri ve site verileri

Tanımlama bilgileri ve site verileri, uygulama güncelleştirmelerinde kalıcı hale getiriyor ve test ve sorun gidermeye engel olabilir. Uygulama kodu değişiklikleri yaparken, sağlayıcı ile Kullanıcı hesabı değişiklikleri veya sağlayıcı uygulama yapılandırma değişiklikleri yaparken aşağıdakileri temizleyin:

* Kullanıcı oturum açma tanımlama bilgileri
* Uygulama tanımlama bilgileri
* Önbelleğe alınmış ve depolanan site verileri

Kalan tanımlama bilgilerinin ve site verilerinin test ve sorun giderme konusunda kesintiye uğramasını önleyen bir yaklaşım:

* Tarayıcı yapılandırma
  * Tarayıcı her kapatıldığında tüm tanımlama bilgilerini ve site verilerini silmek üzere yapılandırabileceğiniz test için bir tarayıcı kullanın.
  * Tarayıcının el ile veya IDE tarafından, uygulama, test kullanıcısı veya sağlayıcı yapılandırmasındaki herhangi bir değişiklik arasında kapatıldığından emin olun.
* Visual Studio 'da veya özel modda tarayıcı açmak için özel bir komut kullanın:
  * Visual Studio 'nun **Çalıştır** düğmesinden iletişim **kutusunu açın.**
  * **Ekle** düğmesini seçin.
  * **Program** alanında tarayıcınızın yolunu belirtin. Aşağıdaki yürütülebilir yollar Windows 10 için tipik yükleme konumlarıdır. Tarayıcınız farklı bir konuma yüklenirse veya Windows 10 kullanmıyorsanız, tarayıcının yürütülebilir dosyasının yolunu belirtin.
    * Microsoft Edge:`C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`
    * Google Chrome:`C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`
    * Mozilla Firefox:`C:\Program Files\Mozilla Firefox\firefox.exe`
  * **Bağımsız değişkenler** alanında, tarayıcının ınbilito veya Private modunda açmak için kullandığı komut satırı seçeneğini belirtin. Bazı tarayıcılar uygulamanın URL 'sini gerektirir.
    * Microsoft Edge:`-inprivate`
    * Google Chrome:`--incognito --new-window https://localhost:5001`
    * Mozilla Firefox:`-private -url https://localhost:5001`
  * **Kolay ad** alanına bir ad girin. Örneğin, `Firefox Auth Testing`.
  * **Tamam** düğmesini seçin.
  * Her bir uygulamayla test yinelemesi için tarayıcı profilini seçmek zorunda kalmamak için, profili varsayılan olarak **Ayarla** düğmesi ile varsayılan olarak ayarlayın.
  * Uygulamanın, test kullanıcısının veya sağlayıcı yapılandırmasındaki herhangi bir değişiklik arasında IDE tarafından kapatıldığından emin olun.

### <a name="run-the-server-app"></a>Sunucu uygulamasını çalıştırma

Barındırılan bir Blazor uygulamasını test etmek ve sorunlarını giderirken, uygulamayı **sunucu** projesinden çalıştırdığınızdan emin olun. Örneğin, Visual Studio 'da aşağıdaki yaklaşımlardan biriyle uygulamaya başlamadan önce sunucu projesinin **Çözüm Gezgini** vurgulandığını doğrulayın:

* **Çalıştır** düğmesini seçin.
* Menüden **hata**  >  **ayıklamayı Başlat komutunu** kullanın.
* <kbd>F5</kbd>tuşuna basın.

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a>JSON Web Token (JWT) içeriğini İnceleme

Bir JSON Web Token (JWT) kodunu çözmek için Microsoft 'un [JWT.MS](https://jwt.ms/) aracını kullanın. Kullanıcı arabirimindeki değerler hiçbir şekilde tarayıcınızdan bırakmayın.
