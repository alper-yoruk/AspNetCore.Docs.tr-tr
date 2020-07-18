<a name="ddav"></a>
### <a name="disable-default-account-verification"></a>Varsayılan hesap doğrulamasını devre dışı bırak

Varsayılan Şablonlar ile Kullanıcı, `Account.RegisterConfirmation` hesabın onaylandığı bir bağlantı seçtikleri yere yönlendirilir. Varsayılan değer `Account.RegisterConfirmation` ***yalnızca*** test için kullanılır, bir üretim uygulamasında otomatik hesap doğrulaması devre dışı bırakılmalıdır.

Onaylanmış bir hesap gerektirmek ve kayıt sırasında hemen oturum açmayı engellemek için, `DisplayConfirmAccountLink = false` */Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs*içinde ayarlayın:

[!code-csharp[](~/security/authentication/identity/sample/WebApp3/Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs?name=snippet&highlight=34)]