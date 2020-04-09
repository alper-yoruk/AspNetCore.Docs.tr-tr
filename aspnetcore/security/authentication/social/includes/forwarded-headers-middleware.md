## <a name="forward-request-information-with-a-proxy-or-load-balancer"></a>İstek bilgilerini proxy veya yük dengeleyicisiyle iletme

Uygulama bir proxy sunucusunun veya yük bakiyesinin arkasına dağıtılırsa, orijinal istek bilgilerinin bazıları istek üstbilgileri yle uygulamaya iletilebilir. Bu bilgiler genellikle güvenli istek`https`düzenini (, ana bilgisayarve istemci IP adresini içerir. Uygulamalar, orijinal istek bilgilerini bulmak ve kullanmak için bu istek üstbilgilerini otomatik olarak okumaz.

Şema, dış sağlayıcılarla kimlik doğrulama akışını etkileyen bağlantı oluşturmada kullanılır. Güvenli düzeni kaybetmek`https`( ) uygulamanın yanlış güvensiz yeniden yönlendirme URL'leri oluşturmasını sağlar.

Orijinal istek bilgilerini istek işleme için uygulamanın kullanımına açmak için Iletili Üstbilgiler Middleware'i kullanın.

Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.
