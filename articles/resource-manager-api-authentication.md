<properties 
   pageTitle="Active Directory 驗證與資源管理員 |Microsoft Azure"
   description="Azure 資源管理員 API 與 Active Directory 驗證與其他 Azure 訂閱整合應用程式開發人員指南。"
   services="azure-resource-manager,active-directory"
   documentationCenter="na"
   authors="dushyantgill"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2016"
   ms.author="dugill;tomfitz" />

# <a name="how-to-use-azure-active-directory-and-resource-manager-to-manage-a-customers-resources"></a>如何使用 Azure Active Directory 和資源管理員來管理客戶的資源

## <a name="introduction"></a>簡介

如果您是軟體開發人員需要建立應用程式來管理客戶的 Azure 資源的人員，本主題說明如何使用 Azure 資源管理員 Api 驗證並存取其他訂閱中的資源。 

您的應用程式可以存取資源管理員中的 Api 有幾種方法︰

1. **使用者 + 應用程式存取**︰ 存取資源代表登入使用者的應用程式。 這種方法可搭配使用的應用程式，例如 web 應用程式和命令列工具，處理只 」 互動式 」 Azure 資源管理。
1. **應用程式存取**︰ 執行精靈服務及排程的工作的應用程式。 直接存取的資源授與應用程式的身分識別。 這種方法適合需要長期 」 離線存取 「 Azure 的應用程式。

本主題提供的逐步指示建立應用程式的使用授權這兩種方法。 它會顯示如何執行每個步驟的 REST API 或 C#。 完整的 ASP.NET MVC 應用程式的[https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense)。

您可以嘗試在[http://vipswapper.azurewebsites.net/cloudsense](http://vipswapper.azurewebsites.net/cloudsense)的 web 應用程式執行本主題所有的程式碼的。 

## <a name="what-the-web-app-does"></a>Web 應用程式的功能

Web 應用程式︰

1. 符號集 Azure 的使用者。
2. 要求使用者在 web 應用程式存取權授予到資源管理員。
3. 取得使用者 + 應用程式存取權杖存取資源管理員。
4. 使用呼叫資源管理員，並為訂閱，讓應用程式長期存取訂閱的角色指派的應用程式服務主要權杖 （從步驟 3）。
5. 取得應用程式存取權杖。
6. 使用 （從步驟 5） 的權杖，來管理訂閱透過資源管理員中的資源。

以下是 web 應用程式的端對端流程。

![資源管理員驗證流程](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

使用者，請您提供的訂閱識別碼您想要使用的訂閱︰

![提供訂閱識別碼](./media/resource-manager-api-authentication/sample-ux-1.png)

選取要用於登入的帳戶。

![選取帳戶](./media/resource-manager-api-authentication/sample-ux-2.png)

提供您的認證。

![提供的認證](./media/resource-manager-api-authentication/sample-ux-3.png)

將應用程式存取權授予您 Azure 的訂閱︰
 
![授與存取權](./media/resource-manager-api-authentication/sample-ux-4.png)
 
管理您已連線的訂閱︰

![連線的訂閱](./media/resource-manager-api-authentication/sample-ux-7.png)


## <a name="register-application"></a>註冊應用程式

您開始撰寫程式碼之前，請註冊您的 web 應用程式與 Azure Active Directory (AD)。 應用程式註冊 Azure AD 中，建立您的應用程式的中央識別。 它會保留 OAuth 用戶端識別碼、 回覆 Url 等應用程式使用項目會驗證並存取 Azure 資源管理員 Api 的認證的應用程式的基本資訊。 應用程式註冊也會記錄您的應用程式需要存取 Microsoft Api 代表使用者時的各種委派權限。 

您的應用程式存取其他訂閱，因此您必須將它設定為多租用戶應用程式。 若要通過驗證，提供您的 Active Directory 與相關聯的網域。 若要查看您的 Active Directory 與相關聯的網域，請登入[傳統入口網站](https://manage.windowsazure.com)。 選取您的 Active Directory，然後選取 [**網域**。

下列範例會示範如何使用 PowerShell 的 Azure 登錄應用程式。 您必須搭配使用此命令 PowerShell 的 Azure 的最新版本 (年 8 月 2016)。 

    $app = New-AzureRmADApplication -DisplayName "{app name}" -HomePage "https://{your domain}/{app name}" -IdentifierUris "https://{your domain}/{app name}" -Password "{your password}" -AvailableToOtherTenants $true
    
若要登入與 AD 應用程式，您需要的應用程式識別碼與密碼。 若要查看所傳回的前一個命令的應用程式識別碼，請使用︰

    $app.ApplicationId

下列範例會示範如何使用 Azure CLI 登錄應用程式。 

    azure ad app create --name {app name} --home-page https://{your domain}/{app name} --identifier-uris https://{your domain}/{app name} --password {your password} --available true

結果包含 AppId，您需要驗證的應用程式時。

### <a name="optional-configuration---certificate-credential"></a>選擇性設定-憑證認證

Azure AD 也支援憑證認證的應用程式︰ 建立自我簽署的憑證、 保留 [私人] 鍵，並新增公開金鑰 Azure AD 應用程式註冊。 進行驗證，您的應用程式的小型的內容以使用您的私密金鑰簽署 Azure AD 與 Azure AD 驗證使用公開金鑰註冊您的簽名。

使用憑證建立 AD 應用程式的詳細資訊，請參閱[使用 Azure PowerShell 來建立主要存取資源的服務](resource-group-authenticate-service-principal.md#create-service-principal-with-certificate)或[使用 Azure CLI 來建立主要存取資源的服務](resource-group-authenticate-service-principal-cli.md#create-service-principal-with-certificate)。

## <a name="get-tenant-id-from-subscription-id"></a>從訂閱識別碼取得租用戶識別碼

若要要求可以用來撥打資源管理員的權杖，您的應用程式必須知道主控 Azure 訂閱的 Azure AD 租用戶的租用戶識別碼。 最有可能使用者知道他們訂閱識別碼，但它們可能不知道自己的租用戶識別碼適用於 Active Directory。 若要取得使用者的租用戶識別碼，要求使用者訂閱 id。 傳送關於訂閱的要求時，請提供該訂閱識別碼︰

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

要求失敗，因為使用者不登入，但是您可以從回應擷取的租用戶識別碼。 在 [例外狀況，從回應標頭值為**WWW 驗證**擷取租用戶識別碼。 您會看到此實作[GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20)方法。

## <a name="get-user--app-access-token"></a>取得使用者 + 應用程式存取權杖

您的應用程式使用者重新導向至 Azure AD 與 OAuth 2.0 授權要求-驗證使用者的認證，並返回驗證碼。 應用程式會用來存取權杖資源管理員的驗證碼。 [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42)方法所建立的授權要求。

本主題說明 REST API 要求使用者進行驗證。 您也可以執行驗證您的程式碼中使用協助文件庫。 如需有關這些文件庫的詳細資訊，請參閱[Azure Active Directory 驗證文件庫](./active-directory/active-directory-authentication-libraries.md)。 整合身分識別管理應用程式中的，請參閱[Azure Active Directory 開發人員指南](./active-directory/active-directory-developers-guide.md)。

### <a name="auth-request-oauth-20"></a>驗證要求 (OAuth 2.0)

Azure AD 授權端點，時發生問題開啟識別碼連線/OAuth2.0 授權要求︰

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

可供這個要求的查詢字串參數是[要求授權程式碼](./active-directory/active-directory-protocols-oauth-code.md#request-an-authorization-code)主題中描述。

下列範例會示範如何要求 OAuth2.0 授權︰

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD 授權的使用者，，必要時，會要求使用者授與應用程式的權限。 它會傳回授權程式碼至您的應用程式回覆 URL。 請根據要求 response_mode，也就是 Azure AD 會傳回資料查詢字串中或張貼資料。

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>（開啟識別碼連接） 的授權要求

如果您不只想要存取 Azure 資源管理員代表使用者，但也可讓使用者將使用其 Azure AD 帳戶的應用程式登入，議題開啟識別碼連線授權要求。 與開啟的識別碼連線，您的應用程式也會從您的應用程式可用來登入使用者的 Azure AD 接收 id_token。

可供這個要求的查詢字串參數會[傳送要求登入](./active-directory/active-directory-protocols-openid-connect-code.md#send-the-sign-in-request)主題中描述。

範例開啟識別碼連線要求是︰

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD 授權的使用者，，必要時，會要求使用者授與應用程式的權限。 它會傳回授權程式碼至您的應用程式回覆 URL。 請根據要求 response_mode，也就是 Azure AD 會傳回資料查詢字串中或張貼資料。

開啟識別碼連線回應範例是︰

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>權杖要求 （OAuth2.0 程式碼授與流程）

現在您的應用程式有從 Azure AD 收到的驗證碼，就來存取權杖 Azure 資源管理員的時間。  張貼 OAuth2.0 程式碼授與權杖要求 Azure AD Token 結束點︰ 

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

查詢字串參數，可在此邀請的說明主題中的[使用授權程式碼](./active-directory/active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)。

下列範例顯示與密碼認證的程式碼授與權杖要求︰

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

使用憑證認證工作時，建立 JSON Web Token (JWT) 並登入 (RSA SHA256) 使用私密金鑰的應用程式的憑證認證。 宣告類型的權杖會顯示在[JWT token 宣告](./active-directory/active-directory-protocols-oauth-code.md#jwt-token-claims)。 參照，請參閱登用戶端判斷提示 JWT 權杖[Active Directory 驗證文件庫 (.NET) 程式碼](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs)。

[開啟識別碼連線規格](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication)如需詳細資訊，請參閱用戶端驗證]。 

下列範例顯示與憑證認證的程式碼授與權杖要求︰

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1
    
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012
    
    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

程式碼授與權杖範例回應︰ 

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>處理的程式碼授與權杖回應

成功的權杖回應包含 （使用者 + 應用程式） 存取權杖，Azure 資源管理員。 您的應用程式會使用此存取權杖，來代表使用者存取資源管理員。 存取權杖 Azure AD 發出的生命週期是一小時。 不太 web 應用程式必須更新 （使用者 + 應用程式） 存取權杖。 如果需要更新存取權杖，使用 [重新整理權杖應用程式所收到的權杖回應。 張貼 OAuth2.0 Token 要求 Azure AD Token 結束點︰ 

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

若要使用 [重新整理要求的參數會中[重新整理存取權杖](./active-directory/active-directory-protocols-oauth-code.md#refreshing-the-access-tokens)描述。

下列範例會示範如何使用 [重新整理 token:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

雖然重新整理權杖可用來取得新的存取權杖，Azure 資源管理員，他們並不適合您的應用程式的離線存取。 重新整理權杖生命週期有限，並重新整理權杖繫結至使用者。 如果使用者離開組織，使用 [重新整理權杖的應用程式就會失去存取。 此方法無法適用於管理其 Azure 資源小組所使用的應用程式。

## <a name="check-if-user-can-assign-access-to-subscription"></a>核取 [是否使用者可以指派存取訂閱

您的應用程式中已經有的權杖代表使用者存取 Azure 資源管理員。 下一個步驟是將您的應用程式連線至訂閱。 連線之後，您的應用程式可以管理這些訂閱即使使用者不存在 （長期離線存取）。 

如需連線每個訂閱，呼叫[資源管理員清單的權限](https://msdn.microsoft.com/library/azure/dn906889.aspx)API 來決定使用者是否具有存取管理權限的訂閱。

ASP.NET MVC 範例應用程式的[UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44)方法實作此通話。

下列範例會示範如何要求訂閱使用者的權限。 83cfe939-2402-4581-b761-4f59b0a041e4 是訂閱的識別碼。

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

若要取得訂閱的使用者權限回應的範例是︰

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

API 的權限會傳回多個權限。 每個權限允許的動作 （動作） 組成，並不允許動作 (notactions)。 如果動作中允許的動作] 清單中的任何權限，而不存在的權限 notactions 清單中，使用者可以執行的動作。 **microsoft.authorization/roleassignments/write**是的授與存取權的權限管理的動作。 您的應用程式必須剖析查看此動作中字串的動作與每個權限 notactions regex 相符的權限結果。

## <a name="get-app-only-access-token"></a>取得應用程式存取權杖

現在，就會知道是否使用者可以指派存取 Azure 訂閱。 下一個步驟如下︰

1. 適當的 RBAC 角色指派給您的應用程式訂閱的身分識別。
2. 查詢訂閱的應用程式的權限或存取資源管理員使用應用程式專用的權杖，請確認 access 工作分派。
1. 在您的應用程式 」 連線的訂閱 「 資料結構-保存的訂閱識別碼記錄連線。

讓我們來看深入瞭解的第一個步驟。 若要指定應用程式的身分識別的適當的 RBAC 角色，您必須判斷︰

- 在使用者的 Azure Active Directory 中的應用程式的身分識別的物件識別碼
- 您的應用程式需要訂閱 RBAC 角色的識別碼

當您的應用程式驗證使用者從 Azure AD 時，它會在該 Azure AD 建立服務應用程式的主體物件。 Azure 可讓 RBAC 角色指派給 Azure 資源相對應的應用程式直接存取權授與服務主體。 此動作正是我們希望執行。 查詢 Azure AD 圖形 API 來判斷您在登入使用者的應用程式服務主體的識別碼的 Azure AD。

您只需要存取權杖，Azure 資源管理員，您需要新的存取權杖，撥打 Azure AD 圖形 API。 Azure AD 中的每個應用程式有查詢自己服務本金物件，為您的應用程式存取權杖足夠的權限。

<a id="app-azure-ad-graph">
### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>取得應用程式存取權杖 Azure AD 圖形 API

若要驗證您的應用程式，並取得 Azure AD 圖形 api 的權杖，發出 Azure AD token 端點**（/https://login.microsoftonline.com/ {directory_domain_name} / OAuth2/權杖**） 用戶端認證授與 OAuth2.0 流程權杖要求。

ASP.net MVC 範例應用程式的[GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs)方法取得應用程式存取權杖圖形 API.net 使用 Active Directory 驗證庫。

可供這個要求的查詢字串參數是[要求存取權杖](./active-directory/active-directory-protocols-oauth-service-to-service.md#request-an-access-token)主題中描述。

用戶端認證授與權杖範例要求︰ 

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

用戶端認證授與權杖範例回應︰ 

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>取得應用程式服務主要的 ObjectId 使用者 Azure AD

現在，請使用應用程式存取權杖來查詢[Azure AD 圖形服務原則](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity)API 來決定應用程式的服務本金在目錄中的物件識別碼。

ASP.net MVC 範例應用程式的[GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#)方法實作此通話。

下列範例會示範如何要求應用程式的服務主要。 a0448380-c346-4f9f-b897-c18733de9394 是用戶端識別碼的應用程式。

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

下列範例顯示的應用程式的服務要求的回應本金 

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>取得 Azure RBAC 角色識別碼

若要將適當的 RBAC 角色指派給您主要的服務，您必須決定 Azure RBAC 角色的識別碼。

應用程式的右 RBAC 角色︰

- 如果您的應用程式只能監視訂閱，而不進行任何變更，需要讀取權限訂閱。 **閱讀程式**角色指派。
- 如果您的應用程式管理 Azure 訂閱，請建立/修改/刪除的項目，需要其中一個參與者] 權限。
  - 若要管理特定類型的資源，指派的特定資源參與者角色 （虛擬機器參與者、 虛擬網路參與者、 儲存帳戶參與者等）
  - 若要管理任何資源類型，指派的**參與者**角色。

應用程式的角色指派為可見，選取最小必要權限的使用者。

[撥號給[資源管理員角色定義 API](https://msdn.microsoft.com/library/azure/dn906879.aspx)所有 Azure RBAC 角色及搜尋] 都清單，然後逐一查看依名稱尋找所要的角色定義結果。

ASP.net MVC 範例應用程式的[GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246)方法實作此通話。

下列要求範例會示範如何取得 Azure RBAC 角色識別碼。 09cbd307-aa71-4aca-b346-5f253e6e3ebb 是訂閱的識別碼。

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

回應是以下列格式︰ 

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

您不需要當前 j。 一旦您已認定已知 GUID 角色定義的您可以建立為角色定義識別碼︰

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

以下是已知 guid 常用內建的角色︰

| 角色 | Guid |
| ----- | ------ |
| 閱讀程式 | acdd72a7-3385-48ef-bd42-f606fba81ae7
| 參與者 | b24988ac-6180-42a0-ab88-20f7382dd24c
| 虛擬機器參與者 | d73bb868-a0df-4d4d-bd69-98a00b01fccb
| 虛擬網路參與者 | b34d265f-36f7-4a0d-a4d4-e158ca92e90f
| 儲存帳戶參與者 | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25
| 網站參與者 | de139f84-1756-47ae-9be6-808fbbe84772
| Web 計劃參與者 | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b
| SQL Server 參與者 | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437
| SQL DB 參與者 | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec

### <a name="assign-rbac-role-to-application"></a>RBAC 角色指派給應用程式

您有您要使用[資源管理員建立角色指派](https://msdn.microsoft.com/library/azure/dn906887.aspx)API 將適當的 RBAC 角色指派給您主要的服務的所有項目。

ASP.net MVC 範例應用程式的[GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170)方法實作此通話。

範例要求 RBAC 角色指派給應用程式︰ 

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

在邀請中，請使用下列的值︰

| Guid | 描述 |
| ------ | --------- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb | id 的訂閱
| c3097b31-7309-4c59-b4e3-770f8406bad2 | 服務主體的應用程式的物件識別碼
| acdd72a7-3385-48ef-bd42-f606fba81ae7 | 讀取者角色的識別碼
| 4f87261d-2816-465d-8311-70a27558df4c | 建立新的角色指派新的 guid

回應是以下列格式︰ 

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>取得應用程式存取權杖，Azure 資源管理員

若要驗證該應用程式有想要的存取訂閱，請使用應用程式專用的權杖訂閱執行測試工作。

若要取得應用程式存取權杖，請從不同的值，資源參數的區段[存取應用程式專用的 Azure AD 圖形 API 權杖](#app-azure-ad-graph)，請依照指示進行︰ 

    https://management.core.windows.net/

ASP.NET MVC 範例應用程式的[ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110)方法取得應用程式存取權杖 Azure 資源管理員的.net 使用 Active Directory 驗證庫。

#### <a name="get-applications-permissions-on-subscription"></a>訂閱取得應用程式的權限

若要查看您的應用程式具有所需的存取，Azure 的訂閱，您可能也呼叫[資源管理員的權限](https://msdn.microsoft.com/library/azure/dn906889.aspx)API。 這種方法很類似您如何決定使用者是否具有管理存取權限的訂閱。 不過，這次請打電話給您在上一個步驟中收到的應用程式存取權杖與的權限 API。

ASP.NET MVC 範例應用程式的[ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110)方法實作此通話。

## <a name="manage-connected-subscriptions"></a>管理連線的訂閱

適當的 RBAC 角色指派給您的應用程式服務本金訂閱，您的應用程式可以繼續監控/管理並使用應用程式存取權杖，Azure 資源管理員。

如果訂閱的擁有者移除應用程式的角色指派使用傳統的入口網站或命令列工具，您的應用程式不再能存取該訂閱。 在此情況下，您應該通知已嚴重從與訂閱連線損毀應用程式外的使用者，並為其授與修復 」 連線的選項。 [修復] 會只要重新建立離線已遭刪除的角色指派。

就像您啟用的使用者連線至您的應用程式的 [訂閱]，您必須允許太中斷訂閱使用者。 從 access 管理觀點來看，中斷連線所代表的意義移除應用程式的服務主要有訂閱的角色指派。 您也可以可能太移除訂閱的應用程式中的任何狀態。 只有具備使用者管理權限存取訂閱，就可以中斷連線訂閱。

ASP.net MVC 範例應用程式的[RevokeRoleFromServicePrincipalOnSubscription 方法](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200)實作此通話。

這樣就可以-使用者可以立即輕鬆連線及管理其 Azure 的訂閱，與您的應用程式。

