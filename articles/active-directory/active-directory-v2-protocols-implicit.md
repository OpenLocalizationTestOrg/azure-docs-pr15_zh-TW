<properties
    pageTitle="Azure AD v2.0 隱含流程 |Microsoft Azure"
    description="建置 web 應用程式使用的隱含流程 Azure AD v2.0 實作單一頁面應用程式。"
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
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---spas-using-the-implicit-flow"></a>v2.0 通訊協定-SPAs 使用隱含的流程
與 v2.0 端點，可以登入您的個人及工作/學校 Microsoft 帳戶的單一頁面應用程式的使用者。  單一頁面和其他 JavaScript 應用程式的執行主要是在瀏覽器美元感興趣的幾個挑戰驗證時︰

- 這些應用程式的安全性特性會明顯不同於傳統伺服器為基礎的 web 應用程式。
- 多個授權伺服器與身分識別提供者不支援 CORS 要求。
- 完整的網頁瀏覽器重新導向離成為特別侵害使用者體驗的應用程式。

這些應用程式 (認為︰ AngularJS、 Ember.js React.js，等) Azure AD 支援 OAuth 2.0 隱含授與流程。  隱含流程是由[OAuth 2.0 規格](http://tools.ietf.org/html/rfc6749#section-4.2)所述。  其主要優點是，可以取得權杖從 Azure AD 而不執行後端伺服器認證 exchange 的應用程式。  這個選項可讓應用程式登入使用者、 維護工作階段，並取得至用戶端中所有其他 web Api 的權杖 JavaScript 程式碼。  有使用隱含流程-特別周圍[用戶端](http://tools.ietf.org/html/rfc6749#section-10.3)和[使用者模擬](http://tools.ietf.org/html/rfc6749#section-10.3)時，請考慮的一些重要的安全性考量。

如果您想要使用的隱含流程和 Azure AD 驗證加入 JavaScript 應用程式，我們建議您使用我們開啟來源 JavaScript 文件庫， [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js)。  有可用的幾個 AngularJS 教學課程[以下](active-directory-appmodel-v2-overview.md#getting-started)可協助您開始使用。  

不過，如果您不想使用單一頁面應用程式中的文件庫，以及傳送通訊協定訊息給您自己，遵循下列步驟，一般。

> [AZURE.NOTE]
    並非所有的 Azure Active Directory 案例與功能支援 v2.0 端點。  若要判斷是否您應該使用 v2.0 端點，請閱讀有關[v2.0 限制](active-directory-v2-limitations.md)。
    
## <a name="protocol-diagram"></a>通訊協定圖表
整個隱含登入流程看起來像這樣-每一個步驟所述下方的詳細資料。

![OpenId 連線區隔線](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-the-sign-in-request"></a>傳送登入邀請

一開始登入您的應用程式的使用者，您可以傳送[OpenID 連線](active-directory-v2-protocols-oidc.md)的授權要求，並取得`id_token`從 v2.0 結束點︰

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token+token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&response_mode=fragment
&state=12345
&nonce=678910
```

> [AZURE.TIP] 按一下要執行這項要求下列連結 ！ 登入後，您的瀏覽器應該重新導向至`https://localhost/myapp/`與`id_token`網址列中。
    <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| 參數 | | 描述 |
| ----------------------- | ------------------------------- | --------------- |
| 租用戶 | 所需 | `{tenant}`要求的 path 中的值可用於控制可以登入應用程式。  允許的值為`common`， `organizations`， `consumers`，及租用戶識別碼。  如需詳細資訊，請參閱[通訊協定的基本概念](active-directory-v2-protocols.md#endpoints)。 |
| client_id | 所需 | 註冊入口網站 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) 指派給您的應用程式的應用程式識別碼。 |
| response_type | 所需 | 必須包含`id_token`用於 OpenID 連線登入。  這也可能包含 response_type `token`。 使用`token`以下可讓您的應用程式，而不需要進行的授權端點的第二個要求存取權杖立即收到來自授權結束點。  如果您是使用`token`response_type，`scope`參數必須包含指出發出的 token 資源的範圍。 |
| redirect_uri | 建議 | 您的應用程式，可以傳送及接收應用程式驗證回應 redirect_uri。  它必須確實符合您註冊在入口網站，但必須 url 編碼 redirect_uris 其中一項。 |
| 範圍 | 所需 | 範圍的空格分隔的清單。  OpenID 連線時，它必須包含範圍`openid`，同意使用者介面中的 [登入 」 權限等同於的角色。  您也可以選擇您可能也想要包含`email`或`profile`的存取其他使用者資料的[範圍](active-directory-v2-scopes.md)。  您也可以在此邀請中的要求同意各種資源包括其他範圍。  |
| response_mode | 建議 | 指定應該用來將產生的權杖返回傳送給您的應用程式的方法。  應該`fragment`隱含的流程。  |
| 狀態 | 建議 | 包含在邀請中也會 token 回應中傳回的值。  它可以是您想要的任何內容的字串。  避免[跨網站要求偽造攻擊](http://tools.ietf.org/html/rfc6749#section-10.12)通常是隨機產生的唯一值。  狀態也會使用編碼之前驗證要求發生，例如 [頁面或檢視上的應用程式中的使用者的狀態相關資訊。 |
| nonce | 所需 | 包含在邀請中，產生的應用程式時，會產生 id_token 宣告為中包含的值。  應用程式可以然後驗證此值，以減少 token 重新執行攻擊。  值，通常是隨機、 唯一的字串，可以用來識別要求的來源。  |
| 提示 | 選擇性 | 指示使用者互動所需的類型。  這次僅有效的值是 [無]，「 登入 」 和 「 同意 」。  `prompt=login`將強制使用者輸入其認證，該要求時，補數單一登入。  `prompt=none`相反-，就能確定的使用者不會顯示任何不的互動式提示。  如果無法透過單一登入自動完成要求，請 v2.0 端點會傳回錯誤。  `prompt=consent`使用者登入，要求使用者授與應用程式的權限後，就會觸發 OAuth 同意] 對話方塊。 |
| login_hint | 選擇性 | 可用來預先填入 [使用者名稱/電子郵件地址] 欄位的登入頁面的使用者，如果您知道提前其使用者名稱。  通常應用程式會使用此參數期間重新驗證，已經有從先前登入使用擷取使用者名稱`preferred_username`宣告。 |
| domain_hint | 選擇性 | 可以是下列其中一項`consumers`或`organizations`。  如果包含，它會略過的電子郵件以探索程序該使用者會經歷在 v2.0 登入] 頁面上，而產生稍微更流暢的使用者體驗。  通常應用程式會使用期間重新驗證，此參數以擷取`tid`從 id_token 宣告。  如果`tid`宣告該值`9188040d-6c67-4c5b-b112-36a304b66dad`，您應該使用`domain_hint=consumers`。  否則，請使用`domain_hint=organizations`。 |

此時，使用者會被要求輸入其認證，並完成驗證。  V2.0 端點也可確保使用者具有同意中指定的權限`scope`查詢參數。  如果使用者有不同意這些權限的任何，它會要求使用者同意必要的權限。  [以下提供權限、 同意及多租用戶應用程式](active-directory-v2-scopes.md)的詳細資訊。

V2.0 端點後，使用者會驗證並授與同意，會傳回指定在您的應用程式的回應`redirect_uri`，使用中指定的方法`response_mode`參數。

#### <a name="successful-response"></a>成功的回應

成功的回應，使用`response_mode=fragment`和`response_type=id_token+token`看起來就像以下，分行符號的可讀性︰

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fmail.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| access_token | 包含的 if`response_type`包含`token`。 應用程式要求，在本例中的 Microsoft Graph 的存取權杖。  存取權杖不應該解碼或否則檢查、 視為不透明的字串。 |
| token_type | 包含的 if`response_type`包含`token`。  一律為`Bearer`。 |
| expires_in | 包含的 if`response_type`包含`token`。  表示權杖是有效的快取用途的秒的數。 |
| 範圍 | 包含的 if`response_type`包含`token`。  指出是有效的 access_token scope(s)。 |
| id_token | 應用程式要求 id_token。 您可以使用 id_token 驗證使用者的身分識別，並開始與使用者的工作階段。  更多詳細資料，id_tokens 和其內容會包含在[v2.0 端點權杖參照](active-directory-v2-tokens.md)。  |
| 狀態 | 如果狀態參數會包含在邀請中，相同的值應該會出現在回應中。 應用程式應該確認邀請及回應的狀態值相同。 |


#### <a name="error-response"></a>錯誤回應
錯誤回應可能也會傳送至`redirect_uri`讓應用程式可以適當地處理︰

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| 錯誤 | 錯誤的程式碼字串，可以用來分類類型的錯誤，並可以用來回應錯誤。 |
| error_description | 特定的錯誤訊息，可協助開發人員識別驗證錯誤的根本原因。  |

## <a name="validate-the-idtoken"></a>驗證 id_token
只要接收 id_token 不足，使用者進行驗證，;您必須驗證 id_token 的簽章，並確認在每個應用程式的需求權杖宣告。  V2.0 端點使用[JSON Web 權杖 (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)和公開金鑰登入權杖，並確認都有效。

您可以選擇以驗證`id_token`在用戶端程式碼，但常見的做法是傳送`id_token`的後端伺服器，並執行那里驗證。  一旦您已驗證簽章 id_token，有幾個宣告要求您確認。  請參閱[v2.0 權杖參照](active-directory-v2-tokens.md)的詳細資訊，包括[驗證的權杖](active-directory-v2-tokens.md#validating-tokens)和[重要資訊的相關簽章金鑰變換](active-directory-v2-tokens.md#validating-tokens)。  我們建議您之後的文件庫使用剖析和驗證的權杖-有至少一個適用於大部分的語言與平台。
<!--TODO: Improve the information on this-->

您也可以驗證額外的宣告，根據您的狀況。  一些常見的驗證包括︰

- 確保使用者/組織已經註冊的應用程式。
- 確保使用者具有適當授權/權限
- 確保特定強度的驗證發生，例如多重因素驗證。

如需有關在 id_token 宣告的詳細資訊，請參閱[v2.0 端點權杖參照](active-directory-v2-tokens.md)。

一旦您完全驗證 id_token，您可以開始與使用者的工作階段，並使用 id_token 中的宣告，以取得應用程式中的使用者資訊。  這項資訊可顯示、 記錄、 授權、 等等。

## <a name="get-access-tokens"></a>取得存取權杖

既然您已註冊使用者到單一頁面應用程式，您可以取得呼叫 web Azure AD，例如[Microsoft Graph](https://graph.microsoft.io)受保護的 Api 來存取權杖。  即使您已收到 token 使用`token`response_type，您可以使用這種方式取得前往其他資源的權杖，而不必重新導向使用者，以再次登入。

在標準的 OpenID 連線/OAuth 流量，這樣藉由 v2.0 要求`/token`端點。  不過，v2.0 端點不支援 CORS 要求，以取得並重新整理權杖打電話 AJAX 超出問題。  不過，您可以使用隱含流程中隱藏的 iframe 新的權杖取得其他 web Api: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

> [AZURE.TIP] 請嘗試複製與貼上至瀏覽器索引標籤的 [邀請] 下方 ！ (若要取代別忘了`domain_hint`和`login_hint`值與您的使用者的正確值)

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint={{consumers-or-organizations}}&login_hint={{your-username}}
```

| 參數 | | 描述 |
| ----------------------- | ------------------------------- | --------------- |
| 租用戶 | 所需 | `{tenant}`要求的 path 中的值可用於控制可以登入應用程式。  允許的值為`common`， `organizations`， `consumers`，及租用戶識別碼。  如需詳細資訊，請參閱[通訊協定的基本概念](active-directory-v2-protocols.md#endpoints)。 |
| client_id | 所需 | 註冊入口網站 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) 指派給您的應用程式的應用程式識別碼。 |
| response_type | 所需 | 必須包含`id_token`用於 OpenID 連線登入。  也可能包括其他 response_types，例如`code`。 |
| redirect_uri | 建議 | 您的應用程式，可以傳送及接收應用程式驗證回應 redirect_uri。  它必須確實符合您註冊在入口網站，但必須 url 編碼 redirect_uris 其中一項。 |
| 範圍 | 所需 | 範圍的空格分隔的清單。  取得權杖，包括感興趣的資源，您需要的所有[範圍](active-directory-v2-scopes.md)。  |
| response_mode | 建議 | 指定應該用來將產生的權杖返回傳送給您的應用程式的方法。  可以是下列其中一項`query`， `form_post`，或`fragment`。  |
| 狀態 | 建議 | 包含在邀請中也會 token 回應中傳回的值。  它可以是您想要的任何內容的字串。  避免跨網站要求偽造攻擊通常是隨機產生的唯一值。  狀態也會使用編碼之前驗證要求發生，例如 [頁面或檢視上的應用程式中的使用者的狀態相關資訊。 |
| nonce | 所需 | 包含在邀請中，產生的應用程式時，會產生 id_token 宣告為中包含的值。  應用程式可以然後驗證此值，以減少 token 重新執行攻擊。  值，通常是隨機、 唯一的字串，可以用來識別要求的來源。  |
| 提示 | 所需 | 重新整理及快速權杖中隱藏的 iframe，您應該使用`prompt=none`以確保 iframe 不會不會在 v2.0 登入] 頁面上，並立即傳回。 |
| login_hint | 所需 | 重新整理及快速權杖中隱藏的 iframe，您必須包含使用者的使用者名稱在此提示中多個使用者的時間，也可以在指定的點的工作階段之間的差異。 您可以從先前登入使用解壓縮的使用者名稱`preferred_username`宣告。 |
| domain_hint | 所需 | 可以是下列其中一項`consumers`或`organizations`。  重新整理與快速權杖中隱藏的 iframe，您必須在邀請中包含 domain_hint。  您應該擷取`tid`宣告從 id_token 的上一個登入來決定要使用的值。  如果`tid`宣告該值`9188040d-6c67-4c5b-b112-36a304b66dad`，您應該使用`domain_hint=consumers`。  否則，請使用`domain_hint=organizations`。 |

感謝至`prompt=none`參數，此要求將 [成功或失敗立即並返回您的應用程式。  成功的回應便會傳送到您的應用程式，在 [指定`redirect_uri`，使用中指定的方法`response_mode`參數。

#### <a name="successful-response"></a>成功的回應
成功的回應，使用`response_mode=fragment`看起來像︰

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| access_token | 應用程式要求權杖。 |
| token_type | 一律為`Bearer`。 |
| 狀態 | 如果狀態參數會包含在邀請中，相同的值應該會出現在回應中。 應用程式應該確認邀請及回應的狀態值相同。 |
| expires_in | 多久存取權杖是生效 （秒）。 |
| 範圍 | 存取權杖是有效的範圍。 |

#### <a name="error-response"></a>錯誤回應
錯誤回應可能也會傳送至`redirect_uri`讓應用程式可以適當地處理。  中的例子`prompt=none`，都必須是的錯誤︰

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| 錯誤 | 錯誤的程式碼字串，可以用來分類類型的錯誤，並可以用來回應錯誤。 |
| error_description | 特定的錯誤訊息，可協助開發人員識別驗證錯誤的根本原因。  |

如果您在邀請中的 iframe 收到此錯誤，使用者必須以互動方式再次登入擷取新的憑證。  您可以選擇處理這種情況中的方式最適合您的應用程式。

## <a name="refreshing-tokens"></a>重新整理權杖

同時`id_token`s 和`access_token`s 便會到期後短時間內，讓您的應用程式必須準備重新整理這些權杖定期。  若要重新整理任一種權杖，您可以執行 from above 使用相同的隱藏的 iframe 要求`prompt=none`控制 Azure AD 行為的參數。  如果您想要接收新`id_token`，請務必使用`response_type=id_token`和`scope=openid`，以及`nonce`參數。


## <a name="send-a-sign-out-request"></a>傳送邀請登出

OpenIdConnect`end_session_endpoint`目前不支援 v2.0 結束點。 這表示您的應用程式無法傳送要求 v2.0 結束點，即可結束使用者的工作階段，並清除 cookie 設定 v2.0 結束點。
若要登入出使用者，請您的應用程式可以只結束自己的工作階段，且使用者，，並將使用者的工作階段留 v2.0 端點中-原封不動。  下次當使用者嘗試登入，就會看到 「 選擇帳戶 」 的頁面上，列出其主動登入帳戶。
在該頁面上，使用者可以選擇登入任何帳戶，結束工作階段與 v2.0 端點。

<!--

When you wish to sign the user out of the  app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->