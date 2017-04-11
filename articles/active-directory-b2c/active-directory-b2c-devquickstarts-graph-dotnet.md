<properties
    pageTitle="Azure Active Directory B2C︰ 使用 [圖表 API |Microsoft Azure"
    description="如何使用應用程式識別碼來自動化程序呼叫 B2C 租用戶的圖形 API。"
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-use-the-graph-api"></a>Azure AD B2C︰ 使用 [圖表 API

Azure Active Directory (Azure AD) B2C 租用戶通常會非常大。 這表示，必須以程式設計方式執行許多常見的租用戶管理工作。 主要的範例是使用者管理。 您可能需要 B2C 租用戶移轉現有的使用者存放區。 您可能要主控自己的頁面上的使用者註冊 Azure AD 幕後中建立使用者帳戶。 這類工作需要能夠建立、 讀取、 更新和刪除使用者帳戶。 您可以使用 Azure AD 圖形 API 執行這些工作。

針對 B2C 租用戶，有兩個主要的模式與圖形 API 通訊。

- 互動式、 執行一次的任務，您應該作為 B2C 租用戶系統管理員帳戶時執行的工作。 此模式下需要系統管理員認證登入該管理員才能執行任何的來電至] 圖形 API。
- 對於自動化、 連續的任務，您應該使用某些類型的服務帳戶提供必要的權限，才能執行管理工作。 Azure AD，在您可以執行這個動作登錄應用程式和 Azure AD 進行驗證。 這是以使用[OAuth 2.0 用戶端憑證授與](../active-directory/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)**應用程式識別碼**。 在此情況下，應用程式做為本身，不是作為使用者，以呼叫圖形 API。

本文中，我們將討論如何執行自動使用大小寫。 若要示範，我們會建立.NET 4.5`B2CGraphClient`執行之使用者建立、 讀取、 更新及刪除 (CRUD) 作業。 用戶端會有 Windows 命令列介面 (CLI)，可讓您叫用各種方法。 不過，撰寫程式碼互動式、 自動化的方式運作。

## <a name="get-an-azure-ad-b2c-tenant"></a>取得 Azure AD B2C 租用戶

您可以建立應用程式或使用者，或完全互動 Azure AD 之前，您必須 Azure AD B2C 租用戶和租用戶中的全域管理員帳戶。 如果您沒有租用戶， [Azure AD B2C 快速入門](active-directory-b2c-get-started.md)。

## <a name="register-a-service-application-in-your-tenant"></a>在您的租用戶註冊服務應用程式

有 B2C 租用戶之後，您需要使用 Azure AD PowerShell cmdlet 來建立您的服務應用程式。
首先，請下載並安裝[Microsoft Online Services 登入小幫手]](http://go.microsoft.com/fwlink/?LinkID=286152)。 然後下載並安裝[64 位元適用於 Windows PowerShell 的 Azure Active Directory 模組](http://go.microsoft.com/fwlink/p/?linkid=236297)。

> [AZURE.IMPORTANT]
若要使用圖形 API B2C 租用戶，您必須使用 PowerShell 登錄專用的應用程式。 請遵循本文中的指示進行。 您無法重複使用您註冊 Azure 入口網站中的現有 B2C 應用程式。

安裝 PowerShell 模組後，開啟 PowerShell，並連線到您的 B2C 租用戶。 執行之後`Get-Credential`，系統會提示您的使用者名稱和密碼，請輸入使用者名稱和 B2C 租用戶系統管理員帳戶的密碼。

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
```

建立您的應用程式之前，必須先產生新的**用戶端密碼**。  Azure AD 進行驗證，並取得存取權杖，您的應用程式會使用用戶端密碼。 您可以在 PowerShell 中產生正確的密碼︰

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
```

最後一個命令應該列印新的用戶端密碼。 複製某個位置安全。 您必須將其更新版本。 現在您可以建立您的應用程式提供認證的新的用戶端密碼的應用程式︰

```
> New-MsolServicePrincipal -DisplayName "My New B2C Graph API App" -Type password -Value $newClientSecret

DisplayName           : My New B2C Graph API App
ServicePrincipalNames : {dd02c40f-1325-46c2-a118-4659db8a55d5}
ObjectId              : e2bde258-6691-410b-879c-b1f88d9ef664
AppPrincipalId        : dd02c40f-1325-46c2-a118-4659db8a55d5
TrustedForDelegation  : False
AccountEnabled        : True
Addresses             : {}
KeyType               : Password
KeyId                 : a261e39d-953e-4d6a-8d70-1f915e054ef9
StartDate             : 9/2/2015 1:33:09 AM
EndDate               : 9/2/2016 1:33:09 AM
Usage                 : Verify
```

如果您已成功建立應用程式時，它應該印出上面的應用程式的內容。 您必須同時`ObjectId`和`AppPrincipalId`，因此太複製這些值。

在您 B2C 租用戶中建立應用程式之後，必須先將其指派權限，就必須執行使用者 CRUD 作業。 指派的應用程式的三個角色︰ 目錄助讀程式 （若要瞭解使用者）、 目錄作者 （若要建立並更新使用者），以及使用者帳戶系統管理員 （若要刪除的使用者）。 這些角色有已知的識別碼，您可以取代`-RoleMemberObjectId`參數與`ObjectId`從上方，請執行下列命令。 若要查看所有目錄的角色，請嘗試執行清單`Get-MsolRole`。

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```

您現在已有權限來建立、 讀取、 更新，請在應用程式，並刪除您 B2C 租用戶的使用者。

## <a name="download-configure-and-build-the-sample-code"></a>下載、 設定和建立範例

首先，請下載的範例程式碼，並取得執行。 然後，我們要深入瞭解它。  您可以[下載為.zip 檔案的程式碼範例](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip)。 您也可以複製到您所選擇的目錄︰

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

開啟`B2CGraphClient\B2CGraphClient.sln`Visual Studio 中的 Visual Studio 方案。 在 [`B2CGraphClient`專案，請開啟檔案`App.config`。 三個應用程式設定換成您自己的值︰

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

接下來，請以滑鼠右鍵按一下`B2CGraphClient`解決方案並重建樣本。 如果您是成功，您現在應該`B2C.exe`可執行檔位於`B2CGraphClient\bin\Debug`。

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>使用圖形 API 建立使用者 CRUD 作業

若要使用 B2CGraphClient，開啟`cmd`Windows 命令提示]，並變更目錄以`Debug`目錄。 再執行`B2C Help`] 命令。

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

這會顯示每個指令的簡短描述。 您可以叫用一個命令，每次`B2CGraphClient`Azure AD 圖形 api 提出要求。

### <a name="get-an-access-token"></a>取得存取權杖

任何圖形 api 要求存取權杖進行驗證。 `B2CGraphClient`若要協助擷取存取權杖會使用開啟來源 Active Directory 驗證文件庫 (ADAL)。 ADAL 簡化 token 擷取提供簡單的 API，並仔細地使用的一些重要的詳細資料，例如快取存取權杖。 您沒有使用 ADAL 存取權杖，不過。 您也可以製作 HTTP 要求，以取得權杖。

> [AZURE.NOTE]
    此程式碼範例使用 ADAL v2 才能與圖形 API 通訊。  您必須使用 ADAL v2 或 v3 若要取得可用和 Azure AD 圖形 API 的存取權杖。

當`B2CGraphClient`執行時，它會建立的執行個體`B2CGraphClient`類別。 此課程對於設定 ADAL 驗證臨時平台︰

```C#
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

我們將使用`B2C Get-User`命令的範例。 當`B2C Get-User`叫用不含任何額外輸入 CLI 通話`B2CGraphClient.GetAllUsers(...)`方法。 這個方法呼叫`B2CGraphClient.SendGraphGetRequest(...)`，其中送出到圖形 API HTTP GET 要求。 前`B2CGraphClient.SendGraphGetRequest(...)`給 GET 要求、 其第一次取得存取權杖使用 ADAL:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

您可以存取權杖圖形 api，則可電話 ADAL`AuthenticationContext.AcquireToken(...)`方法。 ADAL，則會傳回`access_token`代表應用程式的身分識別。

### <a name="read-users"></a>閱讀使用者

當您想要取得使用者清單，或從圖形 API 取得特定使用者時，您可以傳送 HTTP`GET`要求`/users`結束點。 要求所有使用者的租用戶看都起來像這樣︰

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

若要查看此要求，請執行︰

 ```
 > B2C Get-User
 ```

有兩個重要事項請注意︰

- 取得透過 ADAL 存取權杖會新增至`Authorization`使用標題`Bearer`配置。
- B2C 租用戶，則必須使用查詢參數`api-version=1.6`。

兩個以下詳細處理中`B2CGraphClient.SendGraphGetRequest(...)`方法︰

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>建立消費者使用者帳戶

當您在您 B2C 租用戶中建立使用者帳戶時，您可以傳送 HTTP`POST`要求`/users`結束點︰

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",              // a value that can be used for displaying to the end user
    "mailNickname": "joec",                     // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

大部分的這些屬性，在此邀請，才能建立消費者使用者。 若要深入瞭解，請按一下[這裡](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser)。 請注意，`//`已經的圖例包含註解。 不會包含在實際邀請他們。

若要查看要求，請執行下列命令其中一項︰

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

`Create-User` ] 命令會將.json 檔作為輸入參數。 此頁面包含使用者物件的 JSON 表示法。 範例程式碼中有兩個範例.json 檔案︰`usertemplate-email.json`和`usertemplate-username.json`。 您可以修改以符合您需求的這些檔案。 除了上方的必要欄位中，您可以使用的幾個選用欄位會包含在這些檔案。 [Azure AD 圖形 API 實體參照](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity)中找選用的功能變數的詳細資訊。

您可以看到文章邀請中的建構方式`B2CGraphClient.SendGraphPostRequest(...)`。

- 它會附加至存取權杖`Authorization`要求的標題。
- 它會將`api-version=1.6`。
- 在邀請內文中包括 JSON 使用者物件。

> [AZURE.NOTE]
如果您想要從現有的使用者存放區遷移的帳戶有較低的密碼強度比[強式密碼的強度 Azure AD B2C 強制執行](https://msdn.microsoft.com/library/azure/jj943764.aspx)，您可以停用強式密碼需求使用`DisableStrongPassword`中的值`passwordPolicies`屬性。 例如，您可以修改建立使用者要求提供，如下所示︰ `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`。

### <a name="update-consumer-user-accounts"></a>更新消費者使用者帳戶

當您更新使用者物件時，有類似您使用來建立使用者物件程序。 此程序使用 HTTP 但是`PATCH`方法︰

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer",              // this request updates only the user's displayName
}
```

請試著更新 JSON 檔案以新資料來更新使用者。 然後，您可以使用`B2CGraphClient`執行一個命令︰

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

檢查`B2CGraphClient.SendGraphPatchRequest(...)`方法的詳細資料，傳送此要求。

### <a name="search-users"></a>搜尋使用者

您可以搜尋使用者 B2C 租用戶有幾種方式。 一個使用使用者的物件 ID 或兩個使用使用者的登入識別碼 (也就是`signInNames`屬性)。

執行下列命令以搜尋特定使用者的其中一項︰

```
> B2C Get-User <user-object-id>
> B2C Get-User <filter-query-expression>
```

以下是幾個範例︰

```
> B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
> B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>刪除使用者

刪除使用者的程序相當簡單。 將 HTTP`DELETE`方法和物件識別碼建構具有正確的 URL:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

若要查看範例，輸入此命令及檢視列印到主控台刪除要求︰

```
> B2C Delete-User <object-id-of-user>
```

檢查`B2CGraphClient.SendGraphDeleteRequest(...)`方法的詳細資料，傳送此要求。

您可以執行許多其他動作和 Azure AD 圖形 API 除了使用者管理。 [Azure AD 圖形 API 參考](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)以及範例邀請的每個動作提供詳細資料。

## <a name="use-custom-attributes"></a>使用自訂屬性

大部分的消費者應用程式需要儲存某些類型的自訂使用者設定檔資訊。 您可以透過以下的其中一個方法是在您 B2C 租用戶中定義的自訂屬性。 您將使用者物件上的任何其他屬性以相同的方式時，可以將該屬性。 您可以屬性更新、 刪除屬性、 查詢屬性，傳送登入權杖及其他內容中的宣告的屬性。

若要定義您 B2C 租用戶中的自訂屬性，請參閱[B2C 自訂屬性參考](active-directory-b2c-reference-custom-attr.md)。

您可以檢視定義您 B2C 租用戶中使用的自訂屬性`B2CGraphClient`:

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

這些函數的輸出會顯示每個自訂屬性的詳細資料，例如︰

```JSON
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

您可以使用完整的名稱，例如`extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`，為您的使用者物件的屬性。  更新.json 檔案與新屬性的值] 屬性，然後再執行︰

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

使用`B2CGraphClient`，您有以程式設計方式管理 B2C 租用戶使用者服務應用程式。 `B2CGraphClient`使用自己的應用程式身分驗證 Azure AD 圖形 api。 它也會使用用戶端私人取得權杖。 當您將這項功能合併到您的應用程式時，請記住 B2C 應用程式的幾個重點︰

- 您需要租用戶中的適當權限授與應用程式。
- 現在，您需要使用 ADAL v2 取得存取權杖。 （您也可以傳送通訊協定訊息直接，而不使用文件庫。）
- 當您呼叫圖形 API 時，使用`api-version=1.6`。
- 當您建立並更新消費者使用者時，請幾個屬性所，如上述。

如果您有任何問題或您想要使用圖形 API B2C 租用戶上執行的動作要求，這篇文章上保留註解或 GitHub 程式碼範例存放庫中檔案有問題。
