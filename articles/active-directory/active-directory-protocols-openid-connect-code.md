<properties
    pageTitle="Azure AD.NET 通訊協定概觀 |Microsoft Azure"
    description="本文將說明如何使用 HTTP 訊息授權 web 應用程式和使用 Azure Active Directory 和 OpenID 連線您租用戶中的網頁 Api 的存取權。"
    services="active-directory"
    documentationCenter=".net"
    authors="priyamohanram"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="priyamo"/>


# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>授權使用 OpenID 連線和 Azure Active Directory 的 web 應用程式存取

[OpenID 連線](http://openid.net/specs/openid-connect-core-1_0.html)是使用 OAuth 2.0 通訊協定建立簡單的身分識別圖層。 OAuth 2.0 定義機制取得，並使用**存取權杖**存取受保護的資源，但不是會定義標準方法提供身分識別的資訊。 OpenID 連線實作驗證以延伸到 OAuth 2.0 授權處理程序，在表單中的使用者資訊提供`id_token`的驗證的使用者身分識別，以及提供基本的設定檔的使用者資訊。

如果您要建立 web 應用程式的伺服器上，透過瀏覽器存取 OpenID 連線是我們建議。

## <a name="authentication-flow-using-openid-connect"></a>使用 OpenID 連線驗證流程

在最基本的登入流程包含下列步驟，請見下方說明每個。

![OpenId 連線驗證流程](media/active-directory-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)


## <a name="send-the-sign-in-request"></a>傳送登入邀請

當您的 web 應用程式需要驗證使用者時，它必須將使用者導向至`/authorize`結束點。 這項要求非常類似的[OAuth 2.0 授權的程式碼流程](active-directory-protocols-oauth-code.md)，有幾個重要的差別第一個以︰

- 要求必須包含範圍`openid`中`scope`參數。
- `response_type`參數必須包含`id_token`。
- 要求必須包含`nonce`參數。

因此範例要求看起來像這樣︰

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| 參數 | | 描述 |
| ----------------------- | ------------------------------- | --------------- |
| 租用戶 | 所需 | `{tenant}`要求的 path 中的值可用於控制可以登入應用程式。  允許的值是租用戶識別項，例如`8eaef023-2b34-4da1-9baa-8bc8c9d6a490`或`contoso.onmicrosoft.com`或`common`的租用戶無關的權杖 |
| client_id | 所需 | 指派給您的應用程式，當您註冊 Azure AD 與應用程式識別碼。 您可以找到這 Azure 傳統入口網站中。 按一下在**Active Directory**，按一下目錄，按一下應用程式，然後按一下 [**設定**] |
| response_type | 所需 | 必須包含`id_token`用於 OpenID 連線登入。  也可能包括其他 response_types，例如`code`。 |
| 範圍 | 所需 | 範圍的空格分隔的清單。  OpenID 連線時，它必須包含範圍`openid`，同意使用者介面中的 [登入 」 權限等同於的角色。  您也可能會要求同意此邀請中包含其他範圍。 |
| nonce | 所需 | 包含在邀請中，將會包含在所產生的應用程式所產生的值`id_token`為宣告。  應用程式可以然後驗證此值，以減少 token 重新執行攻擊。  值通常是隨機且唯一的字串或 GUID，可以用來識別要求的原始文件。  |
| redirect_uri | 建議 | 您的應用程式，可以傳送及接收應用程式驗證回應 redirect_uri。  它必須確實符合您註冊在入口網站，但必須 url 編碼 redirect_uris 其中一項。 |
| response_mode | 建議 | 指定應該用來產生 authorization_code 傳送您的應用程式的方法。  支援的值為`form_post`的*HTTP 表單張貼*或`fragment` *URL*片段。  為 web 應用程式，我們建議使用`response_mode=form_post`以確保權杖最安全傳輸到您的應用程式。  
| 狀態 | 建議 | 包含在邀請中也會 token 回應中傳回的值。  它可以是您想要的任何內容的字串。  避免[跨網站要求偽造攻擊](http://tools.ietf.org/html/rfc6749#section-10.12)通常是隨機產生的唯一值。  狀態也會使用編碼之前驗證要求發生，例如 [頁面或檢視上的應用程式中的使用者的狀態相關資訊。 |
| 提示 | 選擇性 | 指示使用者互動所需的類型。  這次僅有效的值是 [無]，「 登入 」 和 「 同意 」。  `prompt=login`將強制使用者輸入其認證，該要求時，補數單一登入。  `prompt=none`相反-，就能確定的使用者不會顯示任何不的互動式提示。  如果無法透過單一登入自動完成要求，請結束點會傳回錯誤。  `prompt=consent`使用者登入，要求使用者授與應用程式的權限後，就會觸發 OAuth 同意] 對話方塊。 |
| login_hint | 選擇性 | 可用來預先填入 [使用者名稱/電子郵件地址] 欄位的登入頁面的使用者，如果您知道提前其使用者名稱。  通常應用程式會使用此參數期間重新驗證，已經有從先前登入使用擷取使用者名稱`preferred_username`宣告。 |


此時，使用者會被要求輸入其認證，並完成驗證。

### <a name="sample-response"></a>範例回應

範例回應，使用者驗證後，看起來可能像這樣︰

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| id_token | `id_token`應用程式要求。 您可以使用`id_token`驗證使用者的身分識別，並開始與使用者的工作階段。  |
| 狀態 | 包含在邀請中也會 token 回應中傳回的值。 避免[跨網站要求偽造攻擊](http://tools.ietf.org/html/rfc6749#section-10.12)通常是隨機產生的唯一值。  狀態也會使用編碼之前驗證要求發生，例如 [頁面或檢視上的應用程式中的使用者的狀態相關資訊。 |

### <a name="error-response"></a>錯誤回應
錯誤回應可能也會傳送至`redirect_uri`讓應用程式可以適當地處理︰

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| 錯誤 | 錯誤代碼字串，可以用來分類類型的錯誤，並可以用來回應錯誤。 |
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

## <a name="validate-the-idtoken"></a>驗證 id_token

只要接收`id_token`不足，使用者進行驗證，;您必須驗證簽章，並確認中的宣告`id_token`每個應用程式的需求。 Azure AD 端點使用 JSON Web 權杖 (JWTs) 和公開金鑰登入權杖，並確認都有效。

您可以選擇以驗證`id_token`在用戶端程式碼，但常見的做法是傳送`id_token`的後端伺服器，並執行那里驗證。 一旦您已驗證的簽章`id_token`，有幾個宣告要求您確認。

您也可以驗證額外的宣告，根據您的狀況。 一些常見的驗證包括︰

- 確保使用者/組織已經註冊的應用程式。
- 確保使用者具有適當授權/權限
- 確保特定強度的驗證發生，例如多重因素驗證。

您完全經過驗證後`id_token`，您可以開始與使用者的工作階段，並使用中的宣告`id_token`以取得應用程式中的使用者資訊。 這項資訊可顯示、 記錄、 授權、 等等。如需有關的權杖的類型與宣告的詳細資訊，請閱讀[支援權杖和宣告型別](active-directory-token-and-claims.md)。

## <a name="send-a-sign-out-request"></a>傳送邀請登出

當您想要登入登出應用程式的使用者時，它不就能清除您的應用程式 cookie 或否則結束工作階段與使用者。  您也必須重新導向使用者`end_session_endpoint`的登出。  如果您無法執行此作業，使用者將無法重新驗證您的應用程式而不需要輸入其認證，因為他們必須有效單一登入工作階段與 Azure AD 端點。

您可以重新導向使用者`end_session_endpoint`OpenID 連線中繼資料文件中所列︰

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| 參數 | | 描述 |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | 建議 | 使用者必須重新導向至在成功登出後的 URL。  如果不含的功能，使用者就會顯示一般的訊息。  |

## <a name="token-acquisition"></a>權杖擷取

許多 web 應用程式需要登入]，使用者不僅也存取代表使用 OAuth 該使用者的 web 服務。 這種情況時同時取得使用者驗證結合 [OpenID 連線`authorization_code`可以用來取得`access_tokens`使用 OAuth 授權碼流程。

## <a name="get-access-tokens"></a>取得存取權杖

取得更存取權杖，您必須修改上述登入要求︰

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e      // Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // form_post', or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F                                   
&state=12345                                         // Any value, provided by your app
&nonce=678910                                        // Any value, provided by your app
```

在邀請中包括權限範圍，並使用`response_type=code+id_token`、`authorize`結束點，可確保使用者具有同意中指定的權限`scope`查詢參數，並返回您的應用程式的存取權杖 exchange 授權碼。

### <a name="successful-response"></a>成功的回應

成功的回應，使用`response_mode=form_post`看起來像︰

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| id_token | `id_token`應用程式要求。 您可以使用`id_token`驗證使用者的身分識別，並開始與使用者的工作階段。 |
| 程式碼 | 應用程式要求 authorization_code。 應用程式可用來要求存取權杖目標資源的驗證碼。 Authorization_codes 非常短暫，通常是在過期後 10 分鐘。 |
| 狀態 | 如果狀態參數會包含在邀請中，相同的值應該會出現在回應中。 應用程式應該確認狀態中的值要求與回應完全相同。 |

### <a name="error-response"></a>錯誤回應

錯誤回應可能也會傳送至`redirect_uri`讓應用程式可以適當地處理︰

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| 錯誤 | 錯誤代碼字串，可以用來分類類型的錯誤，並可以用來回應錯誤。 |
| error_description | 特定的錯誤訊息，可協助開發人員識別驗證錯誤的根本原因。  |

如需錯誤碼和其建議的用戶端動作的說明，請參閱[錯誤碼授權端點錯誤](#error-codes-for-authorization-endpoint-errors)。

一旦授權`code`和`id_token`，您可以登入的使用者，並取得存取權杖，代表自己。  若要登入使用者，則必須驗證`id_token`完全上所述。 若要取得存取權杖，您可以遵循我們[OAuth 通訊協定文件](active-directory-protocols-oauth-code.md#Use-the-Authorization-Code-to-Request-an-Access-Token)中所述的步驟。
