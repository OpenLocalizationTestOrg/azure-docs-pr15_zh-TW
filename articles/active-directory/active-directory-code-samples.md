<properties
   pageTitle="Azure Active Directory 程式碼範例 |Microsoft Azure"
   description="Azure Active Directory 程式碼範例、 依案例的索引。"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="priyamohanram"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="azure-active-directory-code-samples"></a>Azure Active Directory 程式碼範例

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

若要將驗證和授權新增至您的 web 應用程式與網路 Api，您可以使用 Microsoft Azure Active Directory (Azure AD)。 此區段連結至程式碼範例，顯示如何完成，您可以使用您的應用程式中的程式碼片段。 在 [程式碼範例] 頁面中，您可以找到詳細的讀取-我主題可協助需求、 安裝及設定。 與程式碼註解，可協助您瞭解重要的區段。

若要瞭解基本的案例中每個範例類型，請參閱 Azure AD 驗證案例。

參與我們的範例上 GitHub: [Microsoft Azure Active Directory 範例和文件](https://github.com/Azure-Samples?page=3&query=active-directory)。

## <a name="web-browser-to-web-application"></a>Web 應用程式的網頁瀏覽器

下列範例會示範如何撰寫 web 應用程式的指示進行 Azure ad 登入使用者的瀏覽器。



| 語言/平台 | 範例 | 描述
| ----------------- | ------ | -----------
| C# /.NET | [WebApp-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) | 使用 OpenID 連線 （ASP.Net OpenID 連線 OWIN 介軟體） 來驗證使用者從 Azure AD 租用戶。
| C# /.NET | [WebApp MultiTenant-OpenIdConnect DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) | 多租用戶使用 OpenID 連線 （ASP.Net OpenID 連線 OWIN 介軟體） 來驗證使用者從多個 Azure AD 租用戶的.NET MVC web 應用程式。
| C# /.NET | [WebApp-WSFederation-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | 從 Azure AD 租用戶，驗證的使用者使用 WS 同盟 （ASP.Net WS 同盟 OWIN 介軟體）。






## <a name="single-page-application-spa"></a>單一頁面應用程式] 選項

此範例會示範如何撰寫 Azure AD 以保護單一頁面應用程式。  

| 語言/平台 | 範例 | 描述
| ----------------- | ------ | -----------
| JavaScript、 C# /.NET | [SinglePageApp DotNet](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | 使用 JavaScript 和 Azure AD ADAL 的安全性與 ASP.NET web API 後端實作 AngularJS 為基礎的單一頁面應用程式。


## <a name="native-application-to-web-api"></a>網路 API 原生應用程式

這些程式碼範例會示範如何建立通話 web 受到 Azure AD 的 Api 的原生的用戶端應用程式。 他們使用[Azure AD 驗證文件庫 (ADAL)](active-directory-authentication-libraries.md)與[OAuth 2.0 Azure AD 中。](https://msdn.microsoft.com/library/azure/dn645545.aspx)

| 語言/平台 | 範例 | 描述
| ----------------- | ------ | -----------
| Javascript | [NativeClient-MultiTarget-Cordova](https://github.com/Azure-Samples/active-directory-cordova-multitarget) | 您可以使用 Apache Cordova 的 ADAL 外掛程式，建立 Apache Cordova 應用程式的呼叫網路 API，並使用 Azure AD 進行驗證。
| C# /.NET | [NativeClient DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) | 使用 Azure AD 受保護的通話網路 API 的.NET WPF 應用程式。
| C# /.NET | [NativeClient WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) | 使用 Azure AD 保護通話網路 API 的 Windows 市集應用程式。
| C# /.NET | [NativeClient WebAPI-MultiTenant WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) | 呼叫的多租用戶 web 與 Azure AD API 受到保護的 Windows 市集應用程式。
| C# /.NET | [WebAPI-OnBehalfOf-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) | 通話的 web API，它會原始的使用者，代表所做的權杖，然後再使用權杖撥打另一個網路 API 原生的用戶端應用程式。
| C# /.NET | [NativeClient WindowsPhone8.1](https://github.com/Azure-Samples/active-directory-dotnet-windowsphone-8.1) | Azure AD 受保護的通話網路 API 的 Windows Phone 8.1 版的 Windows 市集應用程式。
| ObjC | [NativeClient iOS](https://github.com/Azure-Samples/active-directory-ios) | IOS 應用程式的通話網路 API 需要 Azure AD 進行驗證。
| C# /.NET | [WebAPI-ManuallyValidateJwt-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation) | 包含處理 JWT 中的權杖網路 API，而不是使用 OWIN 介軟體邏輯原生的用戶端應用程式。
| C# / Xamarin | [NativeClient Xamarin Android](https://github.com/Azure-Samples/active-directory-xamarin-android) | Android 的文件庫 Xamarin 繫結至原生 Azure AD 驗證文件庫 (ADAL)。
| C# / Xamarin | [NativeClient-Xamarin-iOS](https://github.com/Azure-Samples/active-directory-xamarin-ios) | IOS Xamarin 繫結至原生 Azure AD 驗證文件庫 (ADAL)。
| C# / Xamarin | [NativeClient-MultiTarget-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-multitarget) | Azure AD 受保護的目標五個平台，通話網路 API Xamarin 專案。
| C# /.NET | [NativeClient 遠端控制 DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-headless) | Azure AD 受保護的原生應用程式執行非互動式驗證並通話網路 API。



## <a name="web-application-to-web-api"></a>網路 API 的 web 應用程式

顯示如何使用建置通話的 web 應用程式[中 Azure AD OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)這些程式碼範例 web Api，受到 Azure AD。

| 語言/平台 | 範例 | 描述
| ----------------- | ------ | -----------
| C# /.NET | [WebApp WebAPI-OpenIDConnect DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect) | 通話與登入使用者的權限的網路 API。
|  C# /.NET | [WebApp-WebAPI-OAuth2-AppIdentity-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity) | 通話與應用程式的權限的網路 API。
| C# /.NET | [WebApp-WebAPI-OAuth2-UserIdentity-Dotnet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | 讓它可以呼叫網路 API，則您可以將現有的 web 應用程式[中 Azure AD OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) 2.0 的授權。
| JavaScript | [WebAPI Nodejs](https://github.com/Azure-Samples/active-directory-node-webapi) | 設定整合 Azure AD API 保護的 REST API 服務。 包含網頁的 api Node.js 伺服器。
| C# /.NET | [WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-multitenant-openidconnect) |  多租用戶 MVC web 應用程式，使用 OpenID 連線 （ASP.Net OpenID 連線 OWIN 介軟體） 來驗證使用者從 Azure AD 租用戶。 使用叫用圖形 API 的驗證碼。

## <a name="server-or-daemon-application-to-web-api"></a>伺服器或網路 API 精靈應用程式

這些程式碼範例會示範如何建立使用[Azure AD 驗證文件庫 (ADAL)](active-directory-authentication-libraries.md)和[Azure AD 中 OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)會從網路 API 取得資源的精靈或伺服器應用程式。

| 語言/平台 | 範例 | 描述
| ----------------- | ------ | -----------
| C# /.NET | [精靈 DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon) | 主控台應用程式的通話網路 API。 用戶端認證是密碼。
| C# /.NET | [精靈-CertificateCredential-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) | 通話網路 API 主控台應用程式。 用戶端認證是憑證。


## <a name="calling-azure-ad-graph-api"></a>呼叫 Azure AD 圖形 API

下列程式碼範例會示範如何建立呼叫 Azure AD 圖形 API 以讀取和寫入目錄資料的應用程式。

| 語言/平台 | 範例 | 描述
| ----------------- | ------ | -----------
| Java | [WebApp-GraphAPI-Java](https://github.com/Azure-Samples/active-directory-java-graphapi-web) | 使用圖形 API 來存取 Azure AD 目錄資料的 web 應用程式。
| PHP | [WebApp-GraphAPI-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-web) | 使用圖形 API 來存取 Azure AD 目錄資料的 web 應用程式。
| C# /.NET | [WebApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) | 使用圖形 API 來存取 Azure AD 目錄資料的 web 應用程式。
| C# /.NET | [ConsoleApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-console) | 此主控台應用程式示範圖形 api，一般讀取和寫入通話，並顯示如何執行使用者授權的指派，並更新使用者的縮圖相片和連結。
| C# /.NET | [ConsoleApp GraphAPI-DiffQuery DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-diffquery) | Azure AD 租用戶中的使用者物件主控台應用程式中圖形 API 使用差異的查詢，以取得定期的變更。
| C# /.NET | [WebApp GraphAPI-DirectoryExtensions DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-directoryextensions-web) | MVC 應用程式使用圖形 API 查詢產生簡單的公司的組織圖。
| PHP | [WebApp GraphAPI-DirectoryExtensions PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-directoryextensions-web) | 通話註冊副檔名與然後讀取、 更新及刪除擴充屬性中的值圖形 API PHP 應用程式。


## <a name="authorization"></a>授權

這些程式碼範例顯示如何使用 Azure AD 的授權。

| 語言/平台 | 範例 | 描述
| ----------------- | ------ | -----------
| C# /.NET | [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | 執行整合 Azure AD 的應用程式中使用 Azure Active Directory 群組宣告角色為基礎存取控制 (RBAC)。
| C# /.NET | [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | 執行整合 Azure AD 的應用程式中使用 Azure Active Directory 應用程式角色角色為基礎存取控制 (RBAC)。


## <a name="legacy-walkthroughs"></a>舊版的逐步解說

這些逐步解說使用稍早的技術，但仍可能會感興趣。

| 語言/平台 | 範例 | 描述
| ----------------- | ------ | -----------
| C# /.NET | [在 Microsoft Azure AD 應用程式中的角色和 ACL 授權](http://go.microsoft.com/fwlink/?LinkId=331694) | Azure AD 整合的應用程式中執行的角色為基礎的授權 (RBAC) 和 ACL 為基礎的授權。
| C# /.NET |  [AAL-其餘服務的 Windows 市集應用程式-驗證](http://go.microsoft.com/fwlink/?LinkId=330605) |  若要新增至 Windows 市集應用程式的 [使用者驗證功能使用[Azure AD 驗證文件庫 (ADAL)](active-directory-authentication-libraries.md) (舊稱 AAL) 的 Windows 市集測試版。
| C# /.NET | [透過瀏覽器] 對話方塊的 AAD ADAL 原生應用程式至其餘服務-驗證](http://go.microsoft.com/fwlink/?LinkId=259814) |  您可以使用[Azure AD 驗證文件庫 (ADAL)](active-directory-authentication-libraries.md) ，將使用者驗證功能新增至 WPF 用戶端。
| C# /.NET | [透過瀏覽器] 對話方塊的 ACS ADAL 原生應用程式至其餘服務-驗證](http://code.msdn.microsoft.com/AAL-Native-App-to-REST-de57f2cc) |  使用[Azure AD 驗證文件庫 (ADAL)](active-directory-authentication-libraries.md)和[Access 控制項服務 2.0 (ACS)](http://msdn.microsoft.com/library/azure/hh147631.aspx) WPF 用戶端中新增使用者驗證的功能。
| C# /.NET | [ADAL-伺服器驗證](http://go.microsoft.com/fwlink/?LinkId=259816) | 您可以使用[Azure AD 驗證文件庫 (ADAL)](active-directory-authentication-libraries.md)來保護服務的伺服器端處理程序中的來電至 MVC4 Web API 其餘服務。
| C# /.NET | [新增登入 Web 應用程式使用 Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) | 設定網頁單一登入您 Azure AD 企業目錄針對執行.NET 應用程式。
| C# /.NET | [開發 Azure AD 租用戶多 Web 應用程式](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) | 若要新增至單一登入和目錄存取功能的.NET 應用程式搭配使用跨多個的組織使用 Azure AD。
JAVA | [Azure AD 圖表 API Java 範例應用程式](http://go.microsoft.com/fwlink/?LinkId=263969) | 您可以使用存取目錄資料的圖形 API 從 Azure AD。
PHP | [Azure AD 圖表 API PHP 範例應用程式](http://code.msdn.microsoft.com/PHP-Sample-App-For-Windows-228c6ddb) | 您可以使用存取目錄資料的圖形 API 從 Azure AD。
| C# /.NET | [Azure AD 圖表 API 範例應用程式](http://go.microsoft.com/fwlink/?LinkID=262648) | 您可以使用存取目錄資料的圖形 API 從 Azure AD。
| C# /.NET | [Azure AD 圖形差異查詢範例應用程式](http://go.microsoft.com/fwlink/?LinkId=275398) | Azure AD 租用戶中取得定期變更使用者物件中使用圖表 API 的差異的查詢。
| C# /.NET | [範例應用程式以整合 Azure AD 租用戶多雲端應用程式](http://go.microsoft.com/fwlink/?LinkId=275397) | 整合 Azure AD 租用戶多應用程式。
| C# /.NET | [設定安全性的 Windows 市集應用程式和使用 Azure AD REST Web Service](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) | 建立簡單的網路 API 資源和使用 Azure AD 的用戶端 Windows 市集應用程式和[Azure AD 驗證文件庫 (ADAL)](active-directory-authentication-libraries.md)。
| C# /.NET| [使用圖形 API 來查詢 Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) | Microsoft.NET 應用程式設定成使用 Azure AD 圖形 API 來存取資料從 Azure AD 租用戶目錄。

## <a name="see-also"></a>另請參閱

##### <a name="other-resources"></a>其他資源

[Azure Active Directory 開發人員指南](active-directory-developers-guide.md)

[Azure AD 圖形 API 概念性與參照](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD 圖形 API 協助程式庫](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)

