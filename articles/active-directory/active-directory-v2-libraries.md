<properties
   pageTitle="Azure Active Directory v2.0 驗證文件庫 |Microsoft Azure"
   description="相容的用戶端文件庫與伺服器介軟體文件庫和相關的文件庫、 來源和範例的連結，Azure Active Directory v2.0 端點。"
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/30/2016"
   ms.author="skwan;bryanla"/>


# <a name="azure-active-directory-v20-authentication-libraries"></a>Azure Active Directory v2.0 驗證文件庫
Azure Active Directory (Azure AD) v2.0 端點支援業界標準 OAuth 2.0 和 OpenID 連線 1.0 通訊協定。 您可以使用 Microsoft 和其他組織的各種文件庫與 v2.0 端點。

當您使用 v2.0 端點的應用程式時，建議您使用的由追蹤安全性開發週期 (SDL) 方法，例如[一個後面 Microsoft]的人員通訊協定網域專家所撰寫的文件庫[Microsoft-SDL]。 如果您決定要撰寫支援的通訊協定，我們建議您追蹤 SDL 方法，請注意的安全性考量在標準的相關規格，每個通訊協定。

## <a name="types-of-libraries"></a>文件庫類型

Azure AD v2.0 搭配兩種類型的文件庫︰

- **用戶端文件庫**。 原生的用戶端與伺服器使用用戶端文件庫，取得撥資源，例如 Microsoft Graph 的存取權杖。
- **伺服器介軟體文件庫**。 Web 應用程式會使用伺服器介軟體文件庫的 [使用者登入。 Web Api 使用驗證的原生的用戶端或其他伺服器會傳送權杖 server 介軟體文件庫。

## <a name="library-support"></a>文件庫支援
當您使用 v2.0 結束點時，您可以選擇任何符合標準文件庫，因為很重要事項何處可取得支援。 如需問題，在文件庫的程式碼中的功能要求連絡文件庫擁有者。 問題與服務端的通訊協定實作中的功能要求，請連絡 Microsoft。

文件庫有兩種支援類型︰

- **Microsoft 支援**。 Microsoft 提供這些文件庫，其他修正方式，並已完成 SDL 到期這些文件庫上的注意。
- **相容**。 Microsoft 具有測試這些文件庫中的基本工作，並確認其使用 v2.0 結束點。 Microsoft 不會提供這些文件庫的修正和未完成檢閱這些文件庫。 問題與功能要求應該會導向至的文件庫開啟來源專案。

如需使用 v2.0 端點的文件庫的清單，請參閱本文中的下一個區段。

## <a name="microsoft-supported-client-libraries"></a>Microsoft 支援的用戶端文件庫
| 平台| 文件庫的名稱| 下載 | 原始程式碼 | 範例 |
| :-: | :-: | :-: | :-: | :-: |
| Windows 市集.NET Xamarin | .NET Microsoft 驗證文件庫 (MSAL) | [Microsoft.Identity.Client (NuGet)][ClientLib-NET-Lib] | [MSAL.net (GitHub)][ClientLib-NET-Repo] | [Windows 桌面的原生用戶端範例][ClientLib-NET-Sample] |
| Node.js | Microsoft Azure Active Directory Passport.js 外掛程式 | [密碼 Azure AD (npm)][ClientLib-Node-Lib] | [密碼 Azure AD (GitHub)][ClientLib-Node-Repo] | 即將推出 |

<!--- COMMENTING OUT UNTIL THEY ARE READY
| iOS, Mac | Microsoft Authentication Library (MSAL) for ObjC | In development | In development | In development |
| Android | Microsoft Authentication Library (MSAL) for Android | In development | In development | In development |
| JavaScript | Microsoft Authentication Library (MSAL) for JavaScript | In development | In development | In development |
 -->

## <a name="microsoft-supported-server-middleware-libraries"></a>Microsoft 支援伺服器介軟體文件庫
| 平台| 文件庫的名稱| 下載 | 原始程式碼 | 範例 |
| :-: | :-: | :-: | :-: | :-: |
| .NET 4.x | ASP.NET OWIN OpenID 連線介軟體 | [Microsoft.Owin.Security.OpenIdConnect (NuGet)][ServerLib-Net4-Owin-Oidc-Lib] | [武士專案 (CodePlex)][ServerLib-Net4-Owin-Oidc-Repo] | [Web 應用程式的範例][ServerLib-Net4-Owin-Oidc-Sample] |
| .NET 4.x | Asp.net OWIN OAuth 承載者介軟體 | [Microsoft.Owin.Security.OAuth (NuGet)][ServerLib-Net4-Owin-Oauth-Lib] | [武士專案 (CodePlex)][ServerLib-Net4-Owin-Oauth-Repo] | [網路 API 範例][ServerLib-Net4-Owin-Oauth-Sample] |
| .NET 核心 | OWIN OpenID.NET 核心連線介軟體 | [Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] | [ASP.NET 安全性 (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] | [Web 應用程式的範例][ServerLib-NetCore-Owin-Oidc-Sample] |
| .NET 核心 | .NET 核心 OWIN OAuth 承載者介軟體 | [Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] | [ASP.NET 安全性 (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] | 即將推出 |
| Node.js | Microsoft Azure Active Directory Passport.js 外掛程式 | [密碼 Azure AD (npm)][ServerLib-Node-Lib] | [密碼 Azure AD (GitHub)][ServerLib-Node-Repo] | [Web 應用程式的範例][ServerLib-Node-Sample] |
<!--- COMMENTING UNTIL SAMPLE IS AVAILABLE
| .NET 4.x, .NET Core | JSON Web Token Handler for .NET | [System.IdentityModel.Tokens.Jwt (NuGet)][ServerLib-Net-Jwt-Lib] | [Azure AD identity model extensions for .NET (GitHub)][ServerLib-Net-Jwt-Repo] | Coming soon |
--->
## <a name="compatible-client-libraries"></a>相容的用戶端文件庫
| 平台| 文件庫的名稱 | 測試的版 | 原始程式碼 | 範例 |
| :-: | :-: | :-: | :-: | :-: |
| Android | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) | 0.2.1 | [OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) | [原生應用程式的範例](active-directory-v2-devquickstarts-android.md) |
| iOS | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | 1.2.8 | [NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) | [原生應用程式的範例](active-directory-v2-devquickstarts-ios.md)|
| JavaScript | [Hello.js](https://adodson.com/hello.js/) | 1.13.5 | [Hello.js](https://github.com/MrSwitch/hello.js) | 即將推出 |
| Python Flask | [Flask OAuthlib](https://github.com/lepture/flask-oauthlib) | 0.9.3 | [Flask OAuthlib](https://github.com/lepture/flask-oauthlib) | 即將推出 |
| [注音標示 | [OmniAuth](https://github.com/omniauth/omniauth/wiki) | omniauth:1.3.1</br>omniauth-oauth2:1.4.0 | [OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) | 即將推出 |
<!--- REMOVING BRANDON'S FOR NOW
|  |  |  |  |  |
| Android | [OAuth2 Client](https://github.com/wuman/android-oauth-client) |   | [OAuth2 Client](https://github.com/wuman/android-oauth-client)  | Coming soon  |
| Java | [WSO2 Identity Server](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server) | [Version 5.2.0](http://wso2.com/products/identity-server/) | [Source](https://docs.wso2.com/display/IS500/Building+from+Source) | [Samples index](https://docs.wso2.com/display/IS500/Samples)  |
| Java | [Java Gluu Server](https://gluu.org/docs/) |   | [oxAuth](https://github.com/GluuFederation/oxAuth)  | Coming soon |
| Node.js | [NPM passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect) | 0.0.1  | [Passport-OpenID Connect](https://github.com/jaredhanson/passport-openidconnect) | Coming soon  |
| PHP | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP) |   | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP)  | Coming soon  |
-->

## <a name="compatible-server-middleware-libraries"></a>相容的伺服器介軟體文件庫
即將推出

## <a name="related-content"></a>相關的內容
如需有關 Azure AD v2.0 端點的詳細資訊，請參閱[Azure AD 應用程式模型 v2.0 概觀][AAD-App-Model-V2-Overview]。

若要協助我們精簡和圖案內容，請使用 Disqus 註解功能結尾的這篇文章提供意見反應。

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:
[ClientLib-Iosmac-Lib]:
[ClientLib-Iosmac-Repo]:
[ClientLib-Iosmac-Sample]:
[ClientLib-Android-Lib]:
[ClientLib-Android-Repo]:
[ClientLib-Android-Sample]:
[ClientLib-Js-Lib]:
[ClientLib-Js-Repo]:
[ClientLib-Js-Sample]:
[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-node-web/
