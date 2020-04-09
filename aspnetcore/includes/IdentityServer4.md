ASP.NET Core Identity, ASP.NET Core web uygulamalarına kullanıcı arabirimi (UI) giriş işlevselliği ekler. Web API'lerini ve SP'lerini güvenli hale getirmek için aşağıdakilerden birini kullanın:

* [Azure Active Directory](/azure/api-management/api-management-howto-protect-backend-with-aad)
* [Azure Etkin Dizin B2C](/azure/active-directory-b2c/active-directory-b2c-custom-rest-api-netfw) (Azure AD B2C)]
* [IdentityServer4](https://identityserver.io)

IdentityServer4 Core 3.0 ASP.NET için bir OpenID Connect ve OAuth 2.0 çerçevesidir. IdentityServer4 aşağıdaki güvenlik özelliklerini sağlar:

* Hizmet Olarak Kimlik Doğrulama (AaaS)
* Birden fazla uygulama türü üzerinde tek oturum açma/kapatma (SSO)
* API'ler için erişim denetimi
* Federasyon Ağ Geçidi

Daha fazla bilgi için Bkz. [IdentityServer4'e Hoş Geldiniz.](http://docs.identityserver.io/en/latest/index.html)