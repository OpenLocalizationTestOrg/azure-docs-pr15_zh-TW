
<properties
    pageTitle="Azure AD v2.0 OAuth 用戶端認證流程 |Microsoft Azure"
    description="建置 web 應用程式使用 Azure AD 實作 OAuth 2.0 驗證通訊協定。"
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---oauth-20-client-credentials-flow"></a>流程 v2.0 通訊協定-OAuth 2.0 的用戶端憑證

[授與 OAuth 2.0 用戶端憑證](http://tools.ietf.org/html/rfc6749#section-4.4)，有時也稱為 「 兩腳 OAuth 」，可以用來存取網站裝載於資源使用應用程式的身分識別。  通常用於必須立即 precense 的使用者不在背景中執行的伺服器互動。  這些應用程式類型通常稱為**協助程式**或**服務帳戶**。

> [AZURE.NOTE]
    並非所有的 Azure Active Directory 案例與功能支援 v2.0 端點。  若要判斷是否您應該使用 v2.0 端點，請閱讀有關[v2.0 限制](active-directory-v2-limitations.md)。

在其他一般三個 」 三個腳 OAuth，「 用戶端應用程式會授與存取資源代表特定使用者的權限。  權限是**委派**使用者的應用程式，通常是在[同意](active-directory-v2-scopes.md)程序。  不過，在用戶端認證流程，權限授與**直接**應用程式本身。  當應用程式會顯示資源的權杖時，會強制執行資源應用程式本身具有執行某些動作-不一些使用者已授權的授權。

## <a name="protocol-diagram"></a>通訊協定圖表
整個用戶端認證流程看起來像這樣-每一個步驟所述下方的詳細資料。

![用戶端認證流程](../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>取得直接的授權 
有兩種應用程式通常會收到直接存取資源的授權︰ 透過存取控制清單的資源，或透過 Azure AD 中的應用程式的權限指派。  有數種其他授權其用戶端，可以選擇的資源與資源的每個伺服器可以選擇最適合其應用程式的方法。  這兩種方法是最常見 Azure AD，而且會 reccommended 的用戶端和想要執行的用戶端認證流程的資源。

### <a name="access-control-lists"></a>存取控制清單
指定的資源提供者可能會強制執行授權檢查的應用程式識別碼，知道並授與某些特定的層級的存取權的清單為基礎。  當資源接收權杖從 v2.0 結束時，它解碼權杖，並擷取從用戶端應用程式識別碼`appid`和`iss`宣告。  然後，您只要比較它維護針對某些存取控制清單 (ACL) 的應用程式。  資料粒度和存取控制清單的方法可能會大幅從資源至資源。

常見的使用案例的這類 Acl 測試跑石 web 應用程式或網頁 api。  網路 api 可能只授與其完整的權限的子集合其各種不同的用戶端。  但才能執行上 api 的端對端測試，測試用戶端會建立一個取得從 v2.0 端點的權杖，並將其傳送到 api。  Api 可以 ACL 測試用戶端應用程式識別碼的 api 的整個功能的完整功能。  請注意，是否您有此清單上您的服務時，您應該確認不僅驗證的來電者`appid`，但也驗證的`iss`的權杖也信任時。

此類型是授權的常見協助程式與服務帳戶需要存取資料與個人 Microsoft 帳戶的消費者使用者擁有的項目。  針對組織所擁有的資料，建議您取得透過應用程式 perimssions 必要的授權。

### <a name="application-permissions"></a>應用程式權限
而不必使用 Acl Api 可以公開一組的**應用程式權限**可授與應用程式。  應用程式權限授與應用程式的組織中的系統管理員，只用於存取擁有的組織和員工的資料。  例如，Microsoft Graph 公開多個應用程式權限︰

- 讀取郵件，在所有的信箱
- 閱讀及撰寫郵件，在所有的信箱
- 傳送郵件的任何使用者
- 讀取目錄的資料
- [+ 更多](https://graph.microsoft.io)

若要取得這些應用程式中的權限，您可以執行下列步驟。

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>要求應用程式註冊入口網站中的權限

- 如果您未，請瀏覽[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)，或[建立應用程式](active-directory-v2-app-registration.md)中的應用程式。  您必須以確保您的應用程式所建立至少有一個應用程式的密碼。
- 找出**直接應用程式權限**] 區段，然後新增您的應用程式的需求的權限。
- 請確定**儲存**應用程式註冊

#### <a name="recommended-sign-the-user-into-your-app"></a>建議︰ 登入您的應用程式的使用者

通常時所建立的應用程式使用應用程式權限，應用程式會需要的頁面/檢視，讓管理員核准應用程式的權限。  此頁面可應用程式的註冊流程應用程式的設定或專用的 [連線] 流程的一部分。  在許多情況下，讓來顯示此應用程式的 「 連接 」 檢視使用者具有以公司或學校的 Microsoft 帳戶登入後才。

登入這個應用程式的使用者，可讓您識別 organziation 所屬之前要求他們要核准的應用程式權限的使用者。  雖然不全然必要，其可協助您建立更容易瞭解您組織的使用者體驗。  若要登入使用者，請遵循我們[v2.0 通訊協定教學課程](active-directory-v2-protocols.md)。

#### <a name="request-the-permissions-from-a-directory-admin"></a>要求從目錄管理員的權限

您準備好時，要求權限從公司的系統管理員，您可以將使用者重新導向 v2.0**同意端點的管理員**。

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro Tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| 參數 | | 描述 |
| ----------------------- | ------------------------------- | --------------- |
| 租用戶 | 所需 | 您想要邀請的權限目錄租用戶。  提供 guid 或好記的名稱格式。  如果不知道使用者所屬的租用戶並想要讓使用者使用任何租用戶登入，請使用`common`。 |
| client_id | 所需 | 註冊入口網站 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) 指派給您的應用程式的應用程式識別碼。 |
| redirect_uri | 所需 | 您要傳送給您的應用程式處理回應 redirect_uri。  它必須確實符合您註冊在入口網站，但它必須進行 url 編碼，而且可以有其他路徑區段 redirect_uris 其中一項。 |
| 狀態 | 建議 | 包含在邀請中也會 token 回應中傳回的值。  它可以是您想要的任何內容的字串。  狀態會用於驗證要求發生，例如 [頁面或檢視上之前編碼的應用程式中的使用者的狀態相關資訊。 |

此時，Azure AD 會強制執行只租用戶系統管理員可以完成要求登入。  系統管理員會被要求核准所有您在註冊入口網站應用程式要求的直接應用程式權限。 

##### <a name="successful-response"></a>成功的回應
如果管理員核准應用程式的權限，就會成功的回應︰

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- | --------------- |
| 租用戶 | 該要求 guid 格式的權限授與您的應用程式目錄租用戶。 |
| 狀態 | 包含在邀請中也會 token 回應中傳回的值。  它可以是您想要的任何內容的字串。  狀態會用於驗證要求發生，例如 [頁面或檢視上之前編碼的應用程式中的使用者的狀態相關資訊。 |
| admin_consent | 將被設定為`True`。 |


##### <a name="error-response"></a>錯誤回應
如果管理員沒有核准應用程式的權限，將會失敗的回應︰

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- | --------------- |
| 錯誤 | 錯誤的程式碼字串，可以用來分類的錯誤，並可以用來回應錯誤。 |
| error_description | 特定的錯誤訊息，可協助開發人員找出錯誤的根本原因。  |

當您收到來自佈建端點的應用程式的成功的回應時，您的應用程式已取得該要求的直接應用程式權限。  您現在可以移動到要求的權杖所需的資源。

## <a name="get-a-token"></a>取得權杖
一旦您取得所需的授權應用程式，您可以繼續取得 api 的存取權杖。  若要取得使用用戶端的權杖認證授與，傳送的文章要求`/token`v2.0 結束點︰

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| 參數 | | 描述 |
| ----------------------- | ------------------------------- | --------------- |
| client_id | 所需 | 註冊入口網站 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) 指派給您的應用程式的應用程式識別碼。 |
| 範圍 | 所需 | 傳遞值`scope`此邀請中的參數應該是資源識別項 (URI 應用程式識別碼) 的所需的資源，與貼附`.default`後置字元。  例如 Microsoft Graph 指定值應該是讓`https://graph.microsoft.com/.default`。  此值會通知 v2.0 端點的所有直接應用程式的權限已設定應用程式，它應該發出權杖的所需的資源相關的項目。 |
| client_secret | 所需 | 您在註冊入口網站中產生應用程式的應用程式密碼。 |
| grant_type | 所需 | 必須`client_credentials`。 | 

#### <a name="successful-response"></a>成功的回應
成功回應帶表單︰

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| access_token | 要求的存取權杖。 應用程式可以使用此 token 加至安全的資源，例如網路 API 進行驗證。 |
| token_type | 表示的權杖類型的值。 Azure AD 支援的唯一類型`Bearer`。  |
| expires_in | 多久存取權杖是生效 （秒）。 |

#### <a name="error-response"></a>錯誤回應
錯誤回應帶表單︰

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| 錯誤 | 錯誤的程式碼字串，可以用來分類類型的錯誤，並可以用來回應錯誤。 |
| error_description | 特定的錯誤訊息，可協助開發人員識別驗證錯誤的根本原因。  |
| error_codes | STS 特定錯誤代碼，可協助診斷中的清單。  |
| 時間戳記 | 時間發生錯誤。 |
| trace_id | 可協助診斷中要求的唯一識別碼。  |
| correlation_id | 可以協助診斷元件邀請的唯一識別碼。 |

## <a name="use-a-token"></a>使用
既然您已取得權杖，您可以使用的權杖進行要求的資源。  當權杖到期時，只需重複邀請`/token`取得最新的存取權杖的結束點。

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro Tip: Try the below command out! (but replace the token with your own)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-sample"></a>程式碼範例
若要查看 client_credentials 授與使用系統的實作同意端點應用程式的範例，請參閱我們的[v2.0 精靈程式碼範例](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)。
