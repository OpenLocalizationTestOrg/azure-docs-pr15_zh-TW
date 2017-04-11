<properties
    pageTitle="Azure Active Directory B2C |Microsoft Azure"
    description="建立 web 應用程式使用 OpenID 連線驗證通訊協定的 Azure Active Directory 實作。"
    services="active-directory-b2c"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>Azure Active Directory B2C︰ 網頁以登入 OpenID 連線

OpenID 連線會建立在 OAuth 2.0，可用來安全地登入 web 應用程式的 [使用者驗證通訊協定。  使用 Azure Active Directory (Azure AD) B2C 實作 OpenID 連線，您可以外包註冊、 登入及其他身分識別管理體驗 Azure ad web 應用程式中。 本指南會顯示如何執行此作業語言無關的方式。 它會說明如何傳送和接收 HTTP 訊息，而不使用任何我們開啟來源文件庫。

[OpenID 連線](http://openid.net/specs/openid-connect-core-1_0.html)延伸作為*驗證*通訊協定的 OAuth 2.0*授權*通訊協定。 這個選項可讓您使用 OAuth 來執行單一登入。 它會介紹的概念`id_token`，這是安全性權杖，用戶端驗證的使用者身分識別並取得基本的設定檔的使用者資訊。

因為其擴充 OAuth 2.0，同時也會啟用安全地取得**access_tokens**應用程式。 您可以使用 access_tokens 存取受到[授權伺服器](active-directory-b2c-reference-protocols.md#the-basics)的資源。 我們建議您 OpenID 連線您建立的 web 應用程式是裝載在伺服器上，並透過瀏覽器存取。 如果您想要使用 Azure AD B2C 身分識別管理新增您的行動裝置或桌面應用程式，您應該使用[OAuth 2.0](active-directory-b2c-reference-oauth-code.md) ，而非 OpenID 連線。

Azure AD B2C 延伸標準的 OpenID 連線通訊協定執行多個簡單的驗證和授權。 它會介紹[**原則參數**](active-directory-b2c-reference-policies.md)，這可讓您使用 OpenID 連線將使用者體驗到您的應用程式，例如註冊，登入並管理設定檔。 以下我們會告訴您如何使用 OpenID 連線和原則實作 web 應用程式中的每個這些體驗。 我們也會告訴您如何取得 access_tokens 存取 web Api。

下列範例 HTTP 要求會使用我們的範例 B2C 目錄、 **fabrikamb2c.onmicrosoft.com**，以及我們的範例應用程式**https://aadb2cplayground.azurewebsites.net**及原則。 您可用來試用邀請自己使用這些值，或您可以使用您自己取代它們。
瞭解如何[取得自己 B2C 租用戶、 應用程式及原則](#use-your-own-b2c-directory)。

## <a name="send-authentication-requests"></a>傳送驗證要求
當您的 web 應用程式需要使用者進行驗證，並執行原則時，就可以將使用者導向至`/authorize`結束點。 這是流程的互動式部分使用者實際上會採取動作，根據原則的位置。

在此邀請中，用戶端指出在使用者中取得所需的權限`scope`參數和執行原則`p`參數。 三個範例會提供以下 （分行符號，提高可讀性），各使用不同的原則。 若要瞭解每個要求的運作方式，請嘗試在瀏覽器中貼入邀請，並執行。

#### <a name="use-a-sign-in-policy"></a>使用登入原則

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>使用註冊的原則

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>使用 [編輯設定檔原則

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| 參數 | 需要？ | 描述 |
| ----------------------- | ------------------------------- | ----------------------- |
| client_id | 所需 | [Azure 入口網站](https://portal.azure.com/)指派給您的應用程式的應用程式識別碼。 |
| response_type | 所需 | 回應類型，必須包含`id_token`的 OpenID 連線。 如果您的 web 應用程式也會需要權杖撥號網路 API，您可以使用`code+id_token`，因為我們的做法。 |
| redirect_uri | 建議 | 您的應用程式，可以傳送及接收應用程式驗證回應 redirect_uri。 它必須確實符合您在入口網站，註冊 redirect_uris 其中一項，但必須 URL 編碼。 |
| 範圍 | 所需 | 範圍的空格分隔的清單。 單一範圍值表示 Azure ad 兩個會被要求的權限。 `openid`範圍指示登入的使用者，並取得資料形式**id_tokens** （更多豐富功能在此文件中的更新版本） 的使用者權限。 `offline_access`範圍是選擇性的 web 應用程式。 表示，您的應用程式需要**refresh_token**久存取資源。 |
| response_mode | 建議 | 若要回到您的應用程式傳送結果 authorization_code 應該使用方法。 它可以是 「 查詢 」、 「 form_post 」 或 「 片段]。  「 form_post 」 建議的最佳的安全性。 |
| 狀態 | 建議 | 包含在邀請中也會 token 回應中傳回的值。 它可以是您想要的任何內容的字串。 避免跨網站要求偽造攻擊通常是隨機產生的唯一值。 狀態也會使用編碼之前驗證要求發生，例如頁面上的應用程式中的使用者的狀態相關資訊。 |
| nonce | 所需 | 將會包含在宣告為結果 id_token 要求 （產生應用程式） 中包含的值。 應用程式可以然後驗證此值，以減少 token 重新執行攻擊。 值，通常是隨機、 唯一的字串，可以用來識別要求的來源。 |
| p | 所需 | 將會執行原則。 它是原則的在您的 B2C 租用戶中建立的名稱。 原則名稱] 的值應該開頭 「 b2c\_1\_」。 進一步瞭解[可延伸原則架構](active-directory-b2c-reference-policies.md)中的原則。 |
| 提示 | 選擇性 | 使用者互動所需的類型。 這次僅有效的值是強制使用者輸入其認證，在該要求的 「 登入]。 單一登入不會生效。 |

此時，使用者會被要求完成原則的工作流程。 這可能包含使用者輸入使用者名稱和密碼，登入社交的身分識別，註冊目錄或其他任何數字的步驟，取決於該原則定義的方式。

Azure AD 使用者完成原則之後，會傳回指定在您的應用程式的回應`redirect_uri`，使用中指定的方法`response_mode`參數。 回應會完全相同的每個上述情況下，獨立執行的原則。

成功的回應，使用`response_mode=fragment`如下所示︰

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| id_token | 應用程式要求 id_token。 您可以使用 id_token 驗證使用者的身分識別，並開始與使用者的工作階段。 [Azure AD B2C 權杖參照](active-directory-b2c-reference-tokens.md)中包含 id_tokens 和其內容的更多詳細資料。 |
| 程式碼 | 要求應用程式，如果您是使用 authorization_code `response_type=code+id_token`。 應用程式可用來要求 access_token 目標資源的驗證碼。 Authorization_codes 是非常短暫。 一般而言，他們關於 10 分鐘的時間後過期。 |
| 狀態 | 如果狀態參數會包含在邀請中，相同的值應該會出現在回應中。 應用程式應該確認邀請及回應的狀態值相同。 |

錯誤回應同時送到`redirect_uri`，好讓應用程式可以適當地處理︰

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| 錯誤 | 錯誤的程式碼字串，可以用來分類類型的錯誤，並可以用來回應錯誤。 |
| error_description | 特定的錯誤訊息，可協助開發人員識別驗證錯誤的根本原因。 |
| 狀態 | 請參閱在前一個資料表中的完整描述。 如果狀態參數會包含在邀請中，相同的值應該會出現在回應中。 應用程式應該確認邀請及回應的狀態值相同。 |


## <a name="validate-the-idtoken"></a>驗證 id_token
只會收到 id_token 不夠驗證使用者，您必須驗證 id_token 的簽章，並確認依照您的應用程式需求權杖宣告。 Azure AD B2C 使用[JSON Web 權杖 (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)和公開金鑰登入權杖，並確認都有效。

有許多開啟來源文件庫所使用的驗證 JWTs，根據您的語言喜好設定。 我們建議您探索這些選項，而不是實作自己驗證邏輯。 這裡的資訊會用於找出如何適當使用這些文件庫。

Azure AD B2C 有 OpenID 連線中繼資料端點，可讓應用程式，擷取 Azure AD B2C 的階段資訊。 這項資訊包括端點、 權杖內容，以及權杖簽署金鑰。 有 JSON 中繼資料文件的每個原則 B2C 租用戶。 例如中繼資料文件中的`b2c_1_sign_in`原則`fabrikamb2c.onmicrosoft.com`位於︰

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

此設定文件的屬性的內容之一是`jwks_uri`、 其值是相同的原則︰

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

在順序來決定哪些原則用於登入 id_token （和電腦中的中繼資料的位置），您有兩個選項。 首先，請在原則名稱所包含的`acr`中 id_token 宣告。 剖析從 id_token 宣告方式的詳細資訊，請參閱[Azure AD B2C 權杖參照](active-directory-b2c-reference-tokens.md)。 將另一個選項是編碼中的值的原則`state`參數要求，然後再將它來決定使用哪個原則解碼。 任何一種方法是完全有效。

您已取得的 OpenID 連線的中繼資料端點的中繼資料份文件之後，您可以使用 RSA 256 公用金鑰 （位於此端點） 以驗證簽章 id_token。 可能有多個鍵時間列在任一特定點此端點，每個由`kid`。 Id_token 的標題也包含`kid`宣告，這表示哪一項這些按鍵用於簽署 id_token。 請參閱[Azure AD B2C 權杖參照](active-directory-b2c-reference-tokens.md)的詳細資訊，包括章節[驗證權杖](active-directory-b2c-reference-tokens.md#validating-tokens)並[登入金鑰變換的重要資訊](active-directory-b2c-reference-tokens.md#validating-tokens)。
<!--TODO: Improve the information on this-->

您已驗證的 id_token 簽章之後，有數種宣告，您將需要驗證]，例如︰

- 您應該驗證`nonce`以防止 token 重新執行攻擊宣告。 其值應該是您指定在登入要求。
- 您應該驗證`aud`宣告以確保 id_token 核發應用程式。 其值應該是您的應用程式的應用程式識別碼。
- 您應該驗證`iat`和`exp`宣告以確保 id_token 尚未過期。

此外，還有幾個更多驗證，您應執行，在[OpenID 連線核心規格](http://openid.net/specs/openid-connect-core-1_0.html)的詳細說明。  您也可以驗證其他宣告，根據您的狀況。 一些常見的驗證包括︰

- 確保使用者/組織已經註冊的應用程式。
- 確定使用者具有適當授權/權限。
- 確保特定強度的驗證發生的例如 Azure 多重因素驗證。

如需有關在 id_token 宣告的詳細資訊，請參閱[Azure AD B2C 權杖參照](active-directory-b2c-reference-tokens.md)。

完全驗證 id_token 之後，您可以開始與使用者的工作階段，並使用 id_token 中的宣告，以取得應用程式中的使用者資訊。 這項資訊可用於顯示、 記錄、 授權、 等等。

## <a name="get-a-token"></a>取得權杖
如果所有您要的 web 應用程式需求是執行原則，您可以略過章節。 這些區段只適用於 web 應用程式需要驗證的來電至網路 API，並也會受到 Azure AD B2C。

您可以兌換您 authorization_code (使用`response_type=code+id_token`) 的所需的資源，藉由傳送的權杖`POST`要求`/token`端點。 目前，您可以要求的權杖只資源是您的應用程式自己的後端網路 API。 要求給您自己的權杖的慣例是使用您的應用程式的用戶端識別碼做為範圍︰

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| 參數 | 需要？ | 描述 |
| ----------------------- | ------------------------------- | --------------------- |
| p | 所需 | 用來擷取授權程式碼原則。 您無法使用不同的原則在此邀請。 請注意，您將此參數新增至**查詢字串**，不在張貼內容。 |
| client_id | 所需 | [Azure 入口網站](https://portal.azure.com/)指派給您的應用程式的應用程式識別碼。 |
| grant_type | 所需 | 必須授與的類型`authorization_code`授權的程式碼流程。 |
| 範圍 | 建議 | 範圍的空格分隔的清單。 單一範圍值表示 Azure ad 兩個會被要求的權限。 `openid`範圍指示登入的使用者，並取得資料的**id_tokens**表單中的使用者權限。 可用於取得權杖至您的應用程式自己的後端 web API，會以相同的用戶端應用程式識別碼來表示。 `offline_access`範圍指示，您的應用程式需要**refresh_token**久存取資源。 |
| 程式碼 | 所需 | 您在流程的第一個以取得 authorization_code。 |
| redirect_uri | 所需 | 您收到 authorization_code 的應用程式 redirect_uri。 |
| client_secret | 所需 | 您產生在[Azure 入口網站](https://portal.azure.com/)應用程式密碼。 這個應用程式密碼是重要的安全性成品。 您應該將其儲存安全地在伺服器上。 您應該也請注意旋轉定期這個用戶端密碼。 |

成功的權杖回應看起來像︰

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| not_before | 時間的權杖會被視為有效期間時間。 |
| token_type | 權杖類型的值。 Azure AD 支援的唯一類型是承載者。 |
| access_token | 簽署的 JWT 權杖您要求。 |
| 範圍 | 範圍的權杖是有效的可用的快取權杖，以供日後使用。 |
| expires_in | Access_token 生效 （秒） 的時間長度。 |
| refresh_token | OAuth 2.0 refresh_token。 應用程式可以使用此 token 加目前的權杖到期之後，取得更詳細的權杖。  Refresh_tokens 的時間有多長生活將會變，並可以用來延伸一段時間保留資源的存取權。 如需詳細資訊，請參閱[B2C 權杖參照](active-directory-b2c-reference-tokens.md)。 請注意，您必須有使用範圍`offline_access`在 [授權] 和 [才會收到 refresh_token 權杖要求。 |

錯誤回應看起來像︰

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| 錯誤 | 錯誤的程式碼字串，可以用來分類類型的錯誤，並可以用來回應錯誤。 |
| error_description | 特定的錯誤訊息，可協助開發人員識別驗證錯誤的根本原因。 |

## <a name="use-the-token"></a>使用權杖
既然您已成功取得`access_token`，您可以在邀請中使用權杖至您的後端網頁 Api，包括在中`Authorization`頁首︰

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>重新整理的權杖
Id_tokens 是短期存在。 您必須更新這些後過期，繼續能夠存取的資源。 您可以執行送出另一個`POST`要求`/token`結束點。 這次請提供`refresh_token`而不是`code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| 參數 | 所需 | 描述 |
| ----------------------- | ------------------------------- | -------- |
| p | 所需 | 用來擷取原始 refresh_token 原則。 您無法使用不同的原則在此邀請。 請注意，您將此參數新增至**查詢字串**，不在張貼內容。 |
| client_id | 所需 | [Azure 入口網站](https://portal.azure.com/)指派給您的應用程式的應用程式識別碼。 |
| grant_type | 所需 | 必須授與的類型`refresh_token`版的授權的程式碼流程此腳。 |
| 範圍 | 建議 | 範圍的空格分隔的清單。 單一範圍值表示 Azure ad 兩個會被要求的權限。 `openid`範圍指示登入的使用者，並取得資料的**id_tokens**表單中的使用者權限。 可用於取得權杖至您的應用程式自己的後端 web API，會以相同的用戶端應用程式識別碼來表示。 `offline_access`範圍指示，您的應用程式需要**refresh_token**久存取資源。 |
| redirect_uri | 建議 | 您收到 authorization_code 的應用程式 redirect_uri。 |
| refresh_token | 所需 | 您在第二個的流程以取得原始 refresh_token。 請注意，您必須有使用範圍`offline_access`在 [授權] 和 [才會收到 refresh_token 權杖要求。 |
| client_secret | 所需 | 您產生在[Azure 入口網站](https://portal.azure.com/)應用程式密碼。 這個應用程式密碼是重要的安全性成品。 您應該將其儲存安全地在伺服器上。 您應該也請注意旋轉定期這個用戶端密碼。 |

成功的權杖回應看起來像︰

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| not_before | 時間的權杖會被視為有效期間時間。 |
| token_type | 權杖類型的值。 Azure AD 支援的唯一類型是承載者。 |
| access_token | 簽署的 JWT 權杖您要求。 |
| 範圍 | 範圍的權杖是有效的可用的快取權杖，以供日後使用。 |
| expires_in | Access_token 生效 （秒） 的時間長度。 |
| refresh_token | OAuth 2.0 refresh_token。 應用程式可以使用此 token 加目前的權杖到期之後，取得更詳細的權杖。  Refresh_tokens 的時間有多長生活將會變，並可以用來延伸一段時間保留資源的存取權。 如需詳細資訊，請參閱[B2C 權杖參照](active-directory-b2c-reference-tokens.md)。 |

錯誤回應看起來像︰

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| 錯誤 | 錯誤的程式碼字串，可以用來分類類型的錯誤，並可以用來回應錯誤。 |
| error_description | 特定的錯誤訊息，可協助開發人員識別驗證錯誤的根本原因。 |


## <a name="send-a-sign-out-request"></a>傳送教具借出要求

當您想要登入登出應用程式的使用者時，將其不夠若要清除您的應用程式 cookie 或否則結束工作階段與使用者。 您也必須重新使用者導向至 Azure AD 若要登出。 如果您無法執行此作業，使用者可能無法重新驗證您的應用程式，而不需要再次輸入他們的認證。 這是因為他們必須有效單一登入與工作階段 Azure AD。

您可以重新導向使用者`end_session_endpoint`列於相同 OpenID 連線中繼資料的文件前一節的 「 驗證 id_token 」 所述︰

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| 參數 | 需要？ | 描述 |
| ----------------------- | ------------------------------- | ------------ |
| p | 所需 | 您要用來登入的使用者不在您的應用程式原則。 |
| post_logout_redirect_uri | 建議 | 使用者必須重新導向至之後的 URL 成功教具借出。 如果未包含，使用者就會顯示一般訊息以 Azure AD B2C。  |

> [AZURE.NOTE]
    同時將使用者導向`end_session_endpoint`將會清除部分使用者的單一登入狀態 Azure AD B2C，它不會登入的使用者不在使用者的社交身分識別提供者 (IDP) 工作階段。 如果使用者選取相同的 IDP 後續的登入期間，他們會重新驗證，而不需要輸入認證。 如果使用者想要登出 B2C 應用程式，它不一定要完全登出其 Facebook 帳戶。 不過，如果本機帳戶，使用者的工作階段會結束工作流程正確。

## <a name="use-your-own-b2c-tenant"></a>使用您自己的 B2C 租用戶

如果您想要為自己嘗試這些要求，您必須先執行這三個步驟，而且然後使用您自己取代上方的範例值︰

- [建立 B2C 租用戶](active-directory-b2c-get-started.md)，並使用您的租用戶，在邀請中的名稱。
- 若要取得應用程式識別碼] 和 [redirect_uri[建立應用程式](active-directory-b2c-app-registration.md)。 您會想要包含**web 應用程式/網頁 api**在您的應用程式中，您也可以建立**應用程式的密碼**。
- [建立您的原則](active-directory-b2c-reference-policies.md)，以取得您原則的名稱。
