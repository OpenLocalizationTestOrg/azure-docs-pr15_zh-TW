<properties
   pageTitle="Azure Active Directory 驗證文件庫 |Microsoft Azure"
   description="Azure AD 驗證文件庫 (ADAL) 可讓用戶端應用程式開發人員輕鬆驗證使用者，以雲端或內部部署 Active Directory (AD)，然後取得存取權杖，目的在保護安全 API 呼叫。"
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="mbaldwin" />

# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory 驗證文件庫

Azure AD 驗證文件庫 (ADAL)，啟用用戶端應用程式開發人員輕鬆驗證使用者，以 cloud 或內部部署 Active Directory (AD)，然後取得存取權杖，目的在保護安全 API 呼叫。 ADAL 具有許多功能，讓驗證更容易適用於開發非同步的支援，可以設定的權杖快取儲存存取權杖和重新整理權杖，自動權杖重新整理時存取權杖到期，而且的重新整理權杖就可以使用，例如人員及其他內容。 藉由處理最複雜，ADAL 可以協助開發人員著重於在其應用程式中的商務邏輯，並輕鬆地安全性的專家不安全的資源。

使用各種不同的平台上 ADAL。

|平台|套件名稱|用戶端/伺服器|下載|原始程式碼|文件及範例|
|---|---|---|---|---|---|
|.NET 的用戶端，Windows 市集 UWP Xamarin iOS 和 Android 版|Active Directory 驗證文件庫 (ADAL) 的.NET v3 |用戶端|[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory)|[ADAL.net (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet)|[文件](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory)|
|.NET 用戶端，Windows 市集，Windows Phone 8.1 |Active Directory 驗證文件庫 (ADAL) 的.NET v2 |用戶端|[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.2)|[ADAL.net (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.2)|[文件](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory)|
|JavaScript|Active Directory 驗證文件庫 (ADAL) 的 JavaScript|用戶端|[ADAL 的 JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|[ADAL 的 JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|範例︰ [SinglePageApp DotNet (Github)](https://github.com/AzureADSamples/SinglePageApp-DotNet)|
|OS X iOS|Active Directory 驗證文件庫 (ADAL) 的目標 C|用戶端|[ADAL 的目標 C (CocoaPods)](http://cocoadocs.org/docsets/ADAL/)|[ADAL 的目標 C (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-objc)|範例︰ [NativeClient iOS (Github)](https://github.com/AzureADSamples/NativeClient-iOS)|
|Android|Android 適用的 active Directory 驗證庫 (ADAL)|用戶端|[ADAL 版 （中央存放庫）](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/)|[ADAL 版 (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-android)|範例︰ [NativeClient Android (Github)](https://github.com/AzureADSamples/NativeClient-Android)|
|Node.js|Active Directory 驗證文件庫 (ADAL) 的 Node.js|用戶端|[ADAL 的 Node.js (npm)](https://www.npmjs.com/package/adal-node)|[ADAL 的 Node.js (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs)|範例︰ [WebAPI Nodejs (Github)](https://github.com/AzureADSamples/WebAPI-Nodejs)|
|Node.js|Microsoft Azure Active Directory 密碼驗證介軟體節點|用戶端|[Azure Active Directory 密碼的 Node.js (npm)](https://www.npmjs.com/package/passport-azure-ad)|[Node.js (Github) 的 azure Active Directory](https://github.com/AzureAD/passport-azure-ad)||
|Java|Active Directory 驗證文件庫 (ADAL) java|用戶端|[ADAL java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)|[ADAL java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)||
|.NET|Microsoft.NET Framework 4.5 的身分識別通訊協定副檔名|伺服器|[Microsoft.IdentityModel.Protocol.Extensions (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions)|[Azure AD 身分識別模型的副檔名].NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|Microsoft.Net Framework 4.5 的 JSON Web Token 處理常式|伺服器|[System.IdentityModel.Tokens.Jwt (NuGet)](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt)|[Azure AD 身分識別模型.NET (Github) 的副檔名](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|可讓應用程式使用 Microsoft 的技術驗證 OWIN 介軟體。|伺服器|[Microsoft.Owin.Security.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)||
|.NET|可讓應用程式使用 OpenIDConnect 驗證 OWIN 介軟體。|伺服器|[Microsoft.Owin.Security.OpenIdConnect (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|範例︰ [WebApp OpenIDConnecty DotNet (Github)](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet)|
|.NET|可讓應用程式使用 WS 同盟驗證 OWIN 介軟體。|伺服器|[Microsoft.Owin.Security.WsFederation (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|範例︰ [WebApp WSFederation DotNet (Github)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet)|

## <a name="scenarios"></a>案例

以下是三個常見的案例的 ADAL 可用於驗證。  

### <a name="authenticating-users-of-a-client-application-to-a-remote-resource"></a>驗證用戶端應用程式到遠端資源的使用者

在此案例中，開發人員，請在用戶端，例如 WPF 應用程式，所要存取遠端 Azure AD，例如網路 API 受保護的資源。 他 Azure 的訂閱，知道如何叫用下游 web API，並知道網路 API 使用 Azure AD 租用戶。 如此一來，他也可以使用 ADAL 順利驗證 Azure AD，或明確地處理使用者認證來完整委派 ADAL 驗證體驗。 ADAL 會讓它方便使用者進行驗證，從 Azure AD，取得存取權杖，重新整理權杖，然後使用存取權杖進行要求網路 API。

程式碼範例示範如何使用 Azure ad 驗證這種情況，請參閱[Web api 的原生用戶端 WPF 應用程式](https://github.com/azureadsamples/nativeclient-dotnet)。

### <a name="authenticating-a-server-application-to-a-remote-resource"></a>驗證伺服器應用程式到遠端資源

在此案例中，開發人員需要存取遠端資源受到 Azure AD，例如網路 API 在伺服器上執行的應用程式。 他 Azure 的訂閱，知道如何叫用下游的服務，並知道 Azure AD 租用戶 web API 使用。 如此一來，他也可以使用 ADAL 順利驗證 Azure AD 藉由其他處理應用程式的認證。 ADAL 可輕鬆地從 Azure AD 擷取權杖，使用應用程式的用戶端認證，然後使用該權杖進行要求網路 API。 ADAL 也會處理快取，並視需要更新管理存取權杖的存留時間。 程式碼範例示範這種情況，請參閱[Web API 主控台應用程式](https://github.com/AzureADSamples/Daemon-DotNet)。

### <a name="authenticating-a-server-application-on-behalf-of-a-user-to-access-a-remote-resource"></a>驗證伺服器應用程式代表使用者存取遠端資源

在此案例中，開發人員需要存取遠端資源受到 Azure AD，例如網路 API 在伺服器上執行的應用程式。 可代表使用者 Azure AD 也必須要求。 他 Azure 訂閱、 知道如何叫用下游網路 API，並知道 Azure AD 租用戶的服務使用。 當使用者驗證的 web 應用程式時，應用程式可以從 Azure AD 就使用者取得授權碼。 Web 應用程式使用 ADAL 取得存取權杖，並重新整理代表使用者使用的授權碼和用戶端的認證與應用程式從 Azure AD 相關聯的 token。 擁有存取權杖的 web 應用程式後，它就可呼叫網路 API，直到權杖到期。 當權杖到期時，web 應用程式可以使用新的存取權杖使用取得重新整理 token 先前所收到的 ADAL。


## <a name="see-also"></a>另請參閱

[Azure Active Directory 開發人員指南](active-directory-developers-guide.md)

[適用於 Azure Active directory 驗證案例](active-directory-authentication-scenarios.md)

[Azure Active Directory 程式碼範例](active-directory-code-samples.md)
