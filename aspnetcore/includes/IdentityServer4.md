ASP.NET Core kimlik, ASP.NET Core Web uygulamalarına Kullanıcı arabirimi (UI) oturum açma işlevselliği ekler. Web API 'Leri ve maça 'ları güvenli hale getirmek için aşağıdakilerden birini kullanın:

* [Azure Active Directory](/azure/api-management/api-management-howto-protect-backend-with-aad)
* [Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-custom-rest-api-netfw) (Azure AD B2C)
* [Identityserver4](https://identityserver.io)

Identityserver4, ASP.NET Core 3,0 için bir OpenID Connect ve OAuth 2,0 çerçevesidir. Identityserver4 aşağıdaki güvenlik özelliklerini sunar:

* Hizmet olarak kimlik doğrulaması (AaaS)
* Birden çok uygulama türü üzerinde çoklu oturum açma/kapatma (SSO)
* API 'Ler için erişim denetimi
* Federasyon ağ geçidi

Daha fazla bilgi için bkz. [ıdentityserver4 to Welcome](http://docs.identityserver.io/en/latest/index.html).
