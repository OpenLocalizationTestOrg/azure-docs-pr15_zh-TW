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

# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Azure Active Directory B2C: OAuth 2.0 授權的程式碼流程

OAuth 2.0 授權代碼授與可用於取得受保護的資源，例如網頁 Api 的存取裝置已安裝的應用程式。 藉由使用 OAuth 2.0 的 Azure Active Directory (Azure AD) B2C 實作，您可以新增至您的行動裝置及桌面應用程式的註冊、 登入及其他身分識別管理工作。 本指南是獨立的語言。 說明如何傳送和接收 HTTP 訊息，而不使用任何我們開啟來源文件庫。

<!-- TODO: Need link to libraries -->

[節的 OAuth 2.0 規格 4.1](http://tools.ietf.org/html/rfc6749)中所描述 OAuth 2.0 授權的程式碼流程。 您可以使用其在大部分的應用程式類型，其中包括[web 應用程式](active-directory-b2c-apps.md#web-apps)與[原本就已安裝應用程式](active-directory-b2c-apps.md#mobile-and-native-apps)執行驗證和授權。 它會啟用安全地取得**access_tokens**，可以用來存取[授權伺服器](active-directory-b2c-reference-protocols.md#the-basics)保護的資源的應用程式。

本指南將著重在特定類別的 OAuth 2.0 授權的程式碼流程-**公用用戶端**。 公用的用戶端是密碼的不可信任安全地維護完整性任何用戶端應用程式。 這包含行動應用程式與桌面應用程式，幾乎任何裝置上執行，且需要取得 access_tokens 應用程式。 如果您想要新增使用 Azure AD B2C 身分識別管理 web 應用程式，您應該使用[OpenID 連線](active-directory-b2c-reference-oidc.md)，而非 OAuth 2.0。

Azure AD B2C 延伸的標準的 OAuth 2.0 接續到執行多個簡單的驗證和授權。 它會介紹[**原則參數**](active-directory-b2c-reference-policies.md)，這可讓您使用 OAuth 2.0 將使用者體驗新增至您的應用程式，例如註冊，登入並管理設定檔。 以下介紹如何使用 OAuth 2.0 與原則實作原生應用程式中的每個這些體驗。 我們也會顯示如何取得 access_tokens 存取 web Api。

下列範例 HTTP 要求會使用我們的範例 B2C 目錄、 **fabrikamb2c.onmicrosoft.com**，以及我們的範例應用程式和原則。 您可以使用下列的值，來嘗試查看自己的要求，或您可以使用您自己取代它們。
瞭解如何[取得您自己的 B2C 目錄、 應用程式及原則](#use-your-own-b2c-directory)。

## <a name="1-get-an-authorization-code"></a>1.取得驗證碼
授權碼流程開頭導向使用者將用戶端`/authorize`端點。 這是互動式流量，部分使用者實際上會採取動作的位置。 在此邀請中，用戶端指出在使用者中取得所需的權限`scope`參數和執行原則`p`參數。 三個範例會提供以下 （分行符號，提高可讀性），各使用不同的原則。

#### <a name="use-a-sign-in-policy"></a>使用登入原則

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>使用註冊的原則

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>使用 [編輯設定檔原則

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| 參數 | 需要？ | 描述 |
| ----------------------- | ------------------------------- | ----------------------- |
| client_id | 所需 | [Azure 入口網站](https://portal.azure.com)指派給您的應用程式的應用程式識別碼。 |
| response_type | 所需 | 回應類型，必須包含`code`授權的程式碼流程。 |
| redirect_uri | 所需 | 您的應用程式，可以傳送及接收應用程式驗證回應 redirect_uri。 它必須確實符合您在入口網站，註冊 redirect_uris 其中一項，但必須 URL 編碼。 |
| 範圍 | 所需 | 範圍的空格分隔的清單。 單一範圍值表示 Azure ad 兩個會被要求的權限。 使用範圍會指出您的應用程式需要**存取權杖**，可用來對您自己的服務或網路 API 的用戶端識別碼，表示相同的用戶端識別碼  `offline_access`範圍指示，您的應用程式需要**refresh_token**久存取資源。  您也可以使用`openid`從 Azure AD B2C 要求**id_token**的範圍。 |
| response_mode | 建議 | 若要回到您的應用程式傳送產生 authorization_code 應該使用方法。 它可以是 '查詢 」、 「 form_post 」 或 「 片段 」。
| 狀態 | 建議 | 包含在邀請中也會 token 回應中傳回的值。 它可以是您想要的任何內容的字串。 避免跨網站要求偽造攻擊通常是隨機產生的唯一值。 狀態也會用於驗證要求發生，例如頁面上或正在執行原則之前編碼的應用程式中的使用者的狀態相關資訊。 |
| p | 所需 | 將會執行原則。 它是原則 B2C 目錄中所建立的名稱。 原則名稱] 的值應該開頭 「 b2c\_1\_」。 進一步瞭解[可延伸原則架構](active-directory-b2c-reference-policies.md)中的原則。 |
| 提示 | 選擇性 | 使用者互動所需的類型。 這次僅有效的值是強制使用者輸入其認證，在該要求的 「 登入]。 單一登入不會生效。 |

此時，使用者會被要求完成原則的工作流程。 這可能包含使用者輸入使用者名稱和密碼，登入社交的身分識別，註冊目錄或其他任何數字的步驟，取決於該原則定義的方式。

Azure AD 使用者完成原則之後，會傳回指定在您的應用程式的回應`redirect_uri`，使用中指定的方法`response_mode`參數。 回應會完全相同的每個上述情況下，與無關的原則執行。

使用成功回應`response_mode=query`看起來像︰

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| 程式碼 | 應用程式要求 authorization_code。 應用程式可用來要求 access_token 目標資源的驗證碼。 Authorization_codes 是非常短暫。 一般而言，這些資訊 10 分鐘的時間後過期。 |
| 狀態 | 請參閱在前一個資料表中的完整描述。 如果狀態參數會包含在邀請中，相同的值應該會出現在回應中。 應用程式應該確認邀請及回應的狀態值相同。 |

錯誤回應可能也會傳送至`redirect_uri`，好讓應用程式可以適當地處理︰

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| 錯誤 | 錯誤的程式碼字串可以用來分類的發生錯誤的類型，可以用來回應錯誤。 |
| error_description | 特定的錯誤訊息，可協助您識別驗證錯誤的根本原因開發人員。 |
| 狀態 | 請參閱本節中的第一個資料表中的完整描述。 如果狀態參數會包含在邀請中，相同的值應該會出現在回應中。 應用程式應該確認邀請及回應的狀態值相同。 |


## <a name="2-get-a-token"></a>2.取得權杖
既然您已取得 authorization_code，您可以兌換`code`針對您要傳送給資源的權杖`POST`要求`/token`端點。 Azure AD B2C，您可以要求的權杖只資源是您的應用程式自己的後端網路 API。 用於要求給您自己的權杖的慣例是使用您的應用程式的用戶端識別碼做為範圍︰

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| 參數 | 需要？ | 描述 |
| ----------------------- | ------------------------------- | --------------------- |
| p | 所需 | 用來擷取授權程式碼原則。 您無法使用不同的原則在此邀請。 請注意，您將此參數新增至*查詢字串*，不在張貼內容。 |
| client_id | 所需 | [Azure 入口網站](https://portal.azure.com)指派給您的應用程式的應用程式識別碼。 |
| grant_type | 所需 | 必須授與的類型`authorization_code`授權碼流程。 |
| 範圍 | Reccommended | 範圍的空格分隔的清單。 單一範圍值表示 Azure ad 兩個會被要求的權限。 使用範圍會指出您的應用程式需要**存取權杖**，可用來對您自己的服務或網路 API 的用戶端識別碼，表示相同的用戶端識別碼  `offline_access`範圍指示，您的應用程式需要**refresh_token**久存取資源。  您也可以使用`openid`從 Azure AD B2C 要求**id_token**的範圍。 |
| 程式碼 | 所需 | 您在流程的第一個以取得 authorization_code。 |
| redirect_uri | 所需 | 您收到 authorization_code 的應用程式 redirect_uri。 |

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
| access_token | 簽署的 JSON Web Token (JWT) 權杖您要求。 |
| 範圍 | 範圍的權杖是有效的可用的快取權杖，以供日後使用。 |
| expires_in | 權杖生效 （秒） 的時間長度。 |
| refresh_token | OAuth 2.0 refresh_token。 應用程式可以使用此 token 加目前的權杖到期之後，取得更詳細的權杖。 Refresh_tokens 的時間有多長生活將會變，並可以用來延伸一段時間保留資源的存取權。 如需詳細資訊，請參閱[B2C 權杖參照](active-directory-b2c-reference-tokens.md)。 |

錯誤回應看起來像︰

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| 錯誤 | 錯誤代碼字串可以用來分類的發生錯誤的類型，可以用來回應錯誤。 |
| error_description | 特定的錯誤訊息，可協助您識別驗證錯誤的根本原因開發人員。 |

## <a name="3-use-the-token"></a>3.使用權杖
既然您已成功取得`access_token`，您可以在邀請中使用權杖至您的後端網頁 Api，包括在中`Authorization`頁首︰

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4.重新整理的權杖
存取與識別碼權杖是短期存在。 您必須更新這些後過期，繼續能夠存取的資源。 您可以執行送出另一個`POST`要求`/token`結束點。 這次提供`refresh_token`而不是`code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 參數 | 需要？ | 描述 |
| ----------------------- | ------------------------------- | -------- |
| p | 所需 | 用來擷取原始 refresh_token 原則。 您無法使用不同的原則在此邀請。 請注意，您將此參數新增至*查詢字串*，不中張貼內容。 |
| client_id | 建議 | [Azure 入口網站](https://portal.azure.com)指派給您的應用程式的應用程式識別碼。 |
| grant_type | 所需 | 必須授與的類型`refresh_token`版的授權碼流程此腳。 |
| 範圍 | 建議 | 範圍的空格分隔的清單。 單一範圍值表示 Azure ad 兩個會被要求的權限。 使用範圍會指出您的應用程式需要**存取權杖**，可用來對您自己的服務或網路 API 的用戶端識別碼，表示相同的用戶端識別碼  `offline_access`範圍指示，您的應用程式需要**refresh_token**久存取資源。  您也可以使用`openid`從 Azure AD B2C 要求**id_token**的範圍。 |
| redirect_uri | 選擇性 | 您收到 authorization_code 的應用程式 redirect_uri。 |
| refresh_token | 所需 | 您在第二個的流程以取得原始 refresh_token。 |

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
| access_token | 簽署的 JSON Web Token (JWT) 權杖您要求。 |
| 範圍 | 範圍的權杖是有效的可用的快取權杖，以供日後使用。 |
| expires_in | 權杖生效 （秒） 的時間長度。 |
| refresh_token | OAuth 2.0 refresh_token。 應用程式可以使用此 token 加目前的權杖到期之後，取得更詳細的權杖。 Refresh_tokens 的時間有多長生活將會變，並可以用來延伸一段時間保留資源的存取權。 如需詳細資訊，請參閱[B2C 權杖參照](active-directory-b2c-reference-tokens.md)。 |

錯誤回應看起來像︰

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| 錯誤 | 錯誤代碼字串，可以用來分類類型的錯誤，並可以用來回應錯誤。 |
| error_description | 特定的錯誤訊息，可協助開發人員識別驗證錯誤的根本原因。 |

## <a name="use-your-own-b2c-directory"></a>使用您自己的 B2C 目錄

如果您想要為自己嘗試這些要求，您必須先執行這三個步驟，而且然後使用您自己取代上方的範例值︰

- [建立 B2C 目錄](active-directory-b2c-get-started.md)，然後使用您的目錄，在邀請中的名稱。
- 若要取得應用程式識別碼] 和 [redirect_uri[建立應用程式](active-directory-b2c-app-registration.md)。 您會想要包含在您的應用程式中的**原生的用戶端**。
- [建立您的原則](active-directory-b2c-reference-policies.md)，以取得您原則的名稱。
