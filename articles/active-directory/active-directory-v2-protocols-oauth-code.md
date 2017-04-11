

<properties
    pageTitle="Azure AD v2.0 OAuth 授權程式碼流程 |Microsoft Azure"
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
    ms.date="08/08/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---oauth-20-authorization-code-flow"></a>流程 v2.0 通訊協定-OAuth 2.0 授權程式碼

OAuth 2.0 授權碼授與可用於取得受保護的資源，例如網頁的 Api 的存取裝置已安裝的應用程式。  使用應用程式模型 v2.0 實作 OAuth 2.0，您可以新增登入並 API 存取您的行動裝置及桌面應用程式。  本指南語言無關，並說明如何傳送和接收 HTTP 訊息，而不使用任何我們開啟來源文件庫。

<!-- TODO: Need link to libraries -->

> [AZURE.NOTE]
    並非所有的 Azure Active Directory 案例與功能支援 v2.0 端點。  若要判斷是否您應該使用 v2.0 端點，請閱讀有關[v2.0 限制](active-directory-v2-limitations.md)。

[OAuth 2.0 授權的程式碼流程中一節](http://tools.ietf.org/html/rfc6749)所述的 OAuth 2.0 規格 4.1。  它會用來執行大部分的應用程式類型，其中包括[web 應用程式](active-directory-v2-flows.md#web-apps)與[原本就已安裝應用程式](active-directory-v2-flows.md#mobile-and-native-apps)中的驗證及授權。  它會啟用安全地取得 access_tokens 這可以用來存取使用 v2.0 端點保護的資源的應用程式。  

## <a name="protocol-diagram"></a>通訊協定圖表
在高的層級，原生/行動應用程式的整個驗證流程有點外觀如下︰

![OAuth 驗證程式碼的流程](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="request-an-authorization-code"></a>要求授權碼
授權的程式碼流程開頭導向使用者將用戶端`/authorize`結束點。  在此邀請中，用戶端會指出需要從使用者擷取的權限︰

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [AZURE.TIP] 按一下要執行這項要求下列連結 ！ 登入後，您的瀏覽器應該重新導向至`https://localhost/myapp/`與`code`網址列中。
    <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| 參數 | | 描述 |
| ----------------------- | ------------------------------- | --------------- |
| 租用戶 | 所需 | `{tenant}`要求的 path 中的值可用於控制可以登入應用程式。  允許的值為`common`， `organizations`， `consumers`，及租用戶識別碼。  如需詳細資訊，請參閱[通訊協定的基本概念](active-directory-v2-protocols.md#endpoints)。 |
| client_id | 所需 | 註冊入口網站 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) 指派給您的應用程式的應用程式識別碼。 |
| response_type | 所需 | 必須包含`code`授權的程式碼流程。 |
| redirect_uri | 建議 | 您的應用程式，可以傳送及接收應用程式驗證回應 redirect_uri。  它必須確實符合您註冊在入口網站，但必須 url 編碼 redirect_uris 其中一項。  原生和行動應用程式，則應使用的預設值`https://login.microsoftonline.com/common/oauth2/nativeclient`。 |
| 範圍 | 所需 | 您希望使用者同意的[範圍](active-directory-v2-scopes.md)的空格分隔的清單。  |
| response_mode | 建議 | 指定應該用來將產生的權杖返回傳送給您的應用程式的方法。  可`query`或`form_post`。  |
| 狀態 | 建議 | 包含在邀請中也會 token 回應中傳回的值。  它可以是您想要的任何內容的字串。  避免[跨網站要求偽造攻擊](http://tools.ietf.org/html/rfc6749#section-10.12)通常是隨機產生的唯一值。  狀態也會使用編碼之前驗證要求發生，例如 [頁面或檢視上的應用程式中的使用者的狀態相關資訊。 |
| 提示 | 選擇性 | 指示使用者互動所需的類型。  這次僅有效的值是 [無]，「 登入 」 和 「 同意 」。  `prompt=login`將強制使用者輸入其認證，該要求時，補數單一登入。  `prompt=none`相反-，就能確定的使用者不會顯示任何不的互動式提示。  如果無法透過單一登入自動完成要求，請 v2.0 端點會傳回錯誤。  `prompt=consent`使用者登入，要求使用者授與應用程式的權限後，就會觸發 OAuth 同意] 對話方塊。 |
| login_hint | 選擇性 | 可用來預先填入 [使用者名稱/電子郵件地址] 欄位的登入頁面的使用者，如果您知道提前其使用者名稱。  通常應用程式會使用此參數期間重新驗證，已經有從先前登入使用擷取使用者名稱`preferred_username`宣告。 |
| domain_hint | 選擇性 | 可以是下列其中一項`consumers`或`organizations`。  如果包含，它會略過的電子郵件以探索程序該使用者會經歷在 v2.0 登入] 頁面上，而產生稍微更流暢的使用者體驗。  通常應用程式會使用期間重新驗證，此參數以擷取`tid`從先前的登入。  如果`tid`宣告該值`9188040d-6c67-4c5b-b112-36a304b66dad`，您應該使用`domain_hint=consumers`。  否則，請使用`domain_hint=organizations`。 |

此時，使用者會被要求輸入其認證，並完成驗證。  V2.0 端點也可確保使用者具有同意中指定的權限`scope`查詢參數。  如果使用者有不同意這些權限的任何，它會要求使用者同意必要的權限。  [以下提供權限、 同意及多租用戶應用程式](active-directory-v2-scopes.md)的詳細資訊。

V2.0 端點後，使用者會驗證並授與同意，會傳回指定在您的應用程式的回應`redirect_uri`，使用中指定的方法`response_mode`參數。

#### <a name="successful-response"></a>成功的回應
成功的回應，使用`response_mode=query`看起來像︰

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| 程式碼 | 應用程式要求 authorization_code。 應用程式可用來要求存取權杖目標資源的驗證碼。  Authorization_codes 非常短暫，通常是過期後 10 分鐘。 |
| 狀態 | 如果狀態參數會包含在邀請中，相同的值應該會出現在回應中。 應用程式應該確認邀請及回應的狀態值相同。 |

#### <a name="error-response"></a>錯誤回應
錯誤回應可能也會傳送至`redirect_uri`讓應用程式可以適當地處理︰

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| 錯誤 | 錯誤的程式碼字串，可以用來分類的錯誤，並可以用來回應錯誤。 |
| error_description | 特定的錯誤訊息，可協助開發人員識別驗證錯誤的根本原因。  |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>錯誤碼授權端點錯誤

下表說明可以傳回的各種錯誤碼`error`錯誤回應的參數。

| 錯誤碼 | 描述 | 用戶端巨集指令 |
|------------|-------------|---------------|
| invalid_request | 通訊協定錯誤，例如遺失必要的參數。 | 修正，然後重新提交要求。 這是開發初始測試期間通常攔截錯誤。|
| unauthorized_client | 若要要求授權程式碼不允許的用戶端應用程式。 | 這通常是用戶端應用程式中 Azure AD 未註冊或不會新增到使用者的 Azure AD 租用戶。 應用程式可以提示使用者安裝應用程式，並將其新增至 Azure AD 的指示。 |
| access_denied | 拒絕同意資源擁有者 | 用戶端應用程式可以通知，無法繼續，除非取得使用者同意的使用者。 |
| unsupported_response_type | 授權伺服器不支援在邀請中的回應類型。 | 修正，然後重新提交要求。 這是開發初始測試期間通常攔截錯誤。|
|server_error | 伺服器時發生意外的錯誤。 | 重試要求。 這些錯誤造成的暫時性的條件。 用戶端應用程式可能會說明使用者其回應的延遲到期暫時錯誤。 |
| temporarily_unavailable | 伺服器暫時為忙碌，處理要求。 | 重試要求。 用戶端應用程式可能會說明使用者其回應的延遲是暫時到期。 |
| invalid_resource |目標資源無效，因為它不存在、 Azure AD 找不到，或設定不正確。| 這會指出資源，是否有的話，未設定租用戶中。 應用程式可以提示使用者安裝應用程式，並將其新增至 Azure AD 的指示。 |

## <a name="request-an-access-token"></a>要求存取權杖
現在，您取得 authorization_code 必須授與權限的使用者，您可以兌換`code`的`access_token`所需的資源，藉由傳送`POST`要求`/token`結束點︰

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] 請嘗試執行這項要求郵差 ！ (若要取代別忘了`code`)    [![郵差中執行](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

| 參數 | | 描述 |
| ----------------------- | ------------------------------- | --------------------- |
| 租用戶 | 所需 | `{tenant}`要求的 path 中的值可用於控制可以登入應用程式。  允許的值為`common`， `organizations`， `consumers`，及租用戶識別碼。  如需詳細資訊，請參閱[通訊協定的基本概念](active-directory-v2-protocols.md#endpoints)。 |
| client_id | 所需 | 註冊入口網站 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) 指派給您的應用程式的應用程式識別碼。 |
| grant_type | 所需 | 必須`authorization_code`授權的程式碼流程。 |
| 範圍 | 所需 | 範圍的空格分隔的清單。  在此以中要求的範圍必須等於或範圍中第一個以要求子集。  如果此要求中指定範圍橫跨多個資源伺服器，v2.0 端點會傳回第一個範圍內所指定的資源的權杖。  範圍的更詳細說明，請參閱[權限、 同意及範圍](active-directory-v2-scopes.md)。  |
| 程式碼 | 所需 | 您在流程的第一個以取得 authorization_code。   |
| redirect_uri | 所需 | 用來擷取 authorization_code 相同 redirect_uri 值。 |
| client_secret | 所需的 web 應用程式 | 您為您的應用程式建立的應用程式註冊入口網站中的應用程式密碼。  它不應在原生應用程式，因為 client_secrets 無法確實儲存在裝置上。  需要的 web 應用程式與網路能夠安全地儲存 client_secret 伺服器端的 Api。 |

#### <a name="successful-response"></a>成功的回應
成功的權杖回應看起來像︰

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| access_token | 要求的存取權杖。 應用程式可以使用此 token 加至安全的資源，例如網路 API 進行驗證。 |
| token_type | 表示的權杖類型的值。 Azure AD 支援的唯一類型是承載者  |
| expires_in | 多久存取權杖是生效 （秒）。 |
| 範圍 | Access_token 是有效的範圍。 |
| refresh_token |  OAuth 2.0 重新整理權杖中。 應用程式可以使用此 token 加目前存取權杖到期之後，取得額外的存取權杖。  Refresh_tokens 久，而且可以用來延伸一段時間保留資源的存取權。  如需詳細資訊，請參閱[v2.0 權杖參照](active-directory-v2-tokens.md)。  |
| id_token | 未簽署 JSON Web Token (JWT)。 應用程式可以 base64Url 解碼此 token 加要求登入的使用者資訊區段。 應用程式可以快取中的值，並顯示，但其不應使用這些授權或安全性的邊界。  如需 id_tokens 相關資訊請參閱[v2.0 端點權杖參照](active-directory-v2-tokens.md)。 |

#### <a name="error-response"></a>錯誤回應
錯誤回應看起來像︰

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
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
| 錯誤 | 錯誤的程式碼字串，可以用來分類的錯誤，並可以用來回應錯誤。 |
| error_description | 特定的錯誤訊息，可協助開發人員識別驗證錯誤的根本原因。  |
| error_codes | STS 特定錯誤代碼，可協助診斷中的清單。  |
| 時間戳記 | 時間發生錯誤。 |
| trace_id | 可協助診斷中要求的唯一識別碼。  |
| correlation_id | 可以協助診斷元件邀請的唯一識別碼。 |

#### <a name="error-codes-for-token-endpoint-errors"></a>錯誤碼 token 端點錯誤

| 錯誤碼 | 描述 | 用戶端巨集指令 |
|------------|-------------|---------------|
| invalid_request | 通訊協定錯誤，例如遺失必要的參數。 | 修正，然後重新提交要求 |
| invalid_grant | 授權程式碼不正確或過期。 | 請嘗試新要求`/authorize`端點 |
| unauthorized_client | 經過驗證的用戶端未獲授權使用此授權授與類型。 | 這通常是用戶端應用程式中 Azure AD 未註冊或不會新增到使用者的 Azure AD 租用戶。 應用程式可以提示使用者安裝應用程式，並將其新增至 Azure AD 的指示。 |
| invalid_client | 用戶端驗證失敗。 | 用戶端憑證不是有效的。 若要修正此問題，應用程式管理員更新認證。 |
| unsupported_grant_type | 授權伺服器不支援授權授與類型。 | 變更要求的授與類型。 這種錯誤只在開發以及初始測試期間偵測到。 |
| invalid_resource | 目標資源無效，因為它不存在、 Azure AD 找不到，或設定不正確。 | 這會指出資源，是否有的話，未設定租用戶中。 應用程式可以提示使用者安裝應用程式，並將其新增至 Azure AD 的指示。 |
| interaction_required | 要求使用者的互動。 例如，就需額外的驗證步驟。 | 再試一次使用相同資源的要求。 |
| temporarily_unavailable | 伺服器暫時為忙碌，處理要求。 | 重試要求。 用戶端應用程式可能會說明使用者其回應的延遲是暫時到期。|

## <a name="use-the-access-token"></a>使用存取權杖
既然您已成功取得`access_token`，您可以使用權杖 Web api 邀請中包含中`Authorization`頁首︰

> [AZURE.TIP] 執行這項要求郵差 ！ (取代`Authorization`頁首第一個)    [![郵差中執行](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>重新整理存取權杖
Access_tokens 簡短生活將會變，而且您必須重新整理其後過期，繼續存取資源。  您可以執行送出另一個`POST`要求`/token`端點，這次提供`refresh_token`而不是`code`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] 請嘗試執行這項要求郵差 ！ (若要取代別忘了`refresh_token`)    [![郵差中執行](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

| 參數 | | 描述 |
| ----------------------- | ------------------------------- | -------- |
| 租用戶 | 所需 | `{tenant}`要求的 path 中的值可用於控制可以登入應用程式。  允許的值為`common`， `organizations`， `consumers`，及租用戶識別碼。  如需詳細資訊，請參閱[通訊協定的基本概念](active-directory-v2-protocols.md#endpoints)。 |
| client_id | 所需 | 註冊入口網站 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) 指派給您的應用程式的應用程式識別碼。 |
| grant_type | 所需 | 必須`refresh_token`版的授權的程式碼流程此腳。 |
| 範圍 | 所需 | 範圍的空格分隔的清單。  在此以中要求的範圍必須等於或所要求的原始 authorization_code 要求以範圍子集。  如果此要求中指定範圍橫跨多個資源伺服器，v2.0 端點會傳回第一個範圍內所指定的資源的權杖。  範圍的更詳細說明，請參閱[權限、 同意及範圍](active-directory-v2-scopes.md)。  |
| refresh_token | 所需 | 您在第二個的流程以取得 refresh_token。   |
| redirect_uri | 所需 | 用來擷取 authorization_code 相同 redirect_uri 值。 |
| client_secret | 所需的 web 應用程式 | 您為您的應用程式建立的應用程式註冊入口網站中的應用程式密碼。  它不應在原生應用程式，因為 client_secrets 無法確實儲存在裝置上。  需要的 web 應用程式與網路能夠安全地儲存 client_secret 伺服器端的 Api。 |

#### <a name="successful-response"></a>成功的回應
成功的權杖回應看起來像︰

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| access_token | 要求的存取權杖。 應用程式可以使用此 token 加至安全的資源，例如網路 API 進行驗證。 |
| token_type | 表示的權杖類型的值。 Azure AD 支援的唯一類型是承載者  |
| expires_in | 多久存取權杖是生效 （秒）。 |
| 範圍 | Access_token 是有效的範圍。 |
| refresh_token |  新的 OAuth 2.0 重新整理權杖。 您應該取代舊的重新整理權杖與此新的重新整理權杖，以確定您重新整理權杖仍然有效盡可能久。  |
| id_token | 未簽署 JSON Web Token (JWT)。 應用程式可以 base64Url 解碼此 token 加要求登入的使用者資訊區段。 應用程式可以快取中的值，並顯示，但其不應使用這些授權或安全性的邊界。  如需 id_tokens 相關資訊請參閱[v2.0 端點權杖參照](active-directory-v2-tokens.md)。 |

#### <a name="error-response"></a>錯誤回應
```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
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
| 錯誤 | 錯誤的程式碼字串，可以用來分類的錯誤，並可以用來回應錯誤。 |
| error_description | 特定的錯誤訊息，可協助開發人員識別驗證錯誤的根本原因。  |
| error_codes | STS 特定錯誤代碼，可協助診斷中的清單。  |
| 時間戳記 | 時間發生錯誤。 |
| trace_id | 可協助診斷中要求的唯一識別碼。  |
| correlation_id | 可以協助診斷元件邀請的唯一識別碼。 |

如需錯誤碼和建議的用戶端動作的說明，請參閱[錯誤碼 token 端點錯誤](#error-codes-for-token-endpoint-errors)。
