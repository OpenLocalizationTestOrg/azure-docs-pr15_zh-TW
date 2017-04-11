<properties
    pageTitle="Azure AD.NET 通訊協定概觀 |Microsoft Azure"
    description="本文將說明如何使用 HTTP 訊息授權 web 應用程式和 Azure Active Directory 和 OAuth 2.0 使用您租用戶中的網頁 Api 的存取權。"
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


# <a name="authorize-access-to-web-applications-using-oauth-20-and-azure-active-directory"></a>授權使用 OAuth 2.0 和 Azure Active Directory 的 web 應用程式存取

Azure Active Directory (Azure AD) 會使用 OAuth 2.0，讓您將授權 web 應用程式和 Azure AD 租用戶的網站的 Api 的存取權。 本指南是獨立的語言，並說明如何傳送和接收 HTTP 訊息，而不使用任何我們開啟來源文件庫。

[節的 OAuth 2.0 規格 4.1](https://tools.ietf.org/html/rfc6749#section-4.1)中所描述 OAuth 2.0 授權的程式碼流程。 它會用於執行大多數的應用程式類型，包括 web 應用程式，以及這些安裝應用程式中的驗證及授權。

[AZURE.INCLUDE [active-directory-protocols-getting-started](../../includes/active-directory-protocols-getting-started.md)]


## <a name="oauth-20-authorization-flow"></a>OAuth 2.0 授權流程

在高的層級，應用程式的整個授權流程有點外觀如下︰

![OAuth 驗證程式碼的流程](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)


## <a name="request-an-authorization-code"></a>要求授權碼

授權的程式碼流程開頭導向使用者將用戶端`/authorize`結束點。 在此邀請中，用戶端會指出需要從使用者擷取的權限。 您可以從 Azure 傳統入口網站中的應用程式的頁面中底部抽屜中的 [**檢視結束點**] 按鈕，以取得 OAuth 2.0 結束點。

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| 參數 | | 描述 |
| ----------------------- | ------------------------------- | --------------- |
| 租用戶 | 所需 | `{tenant}`要求的 path 中的值可用於控制可以登入應用程式。  允許的值是租用戶識別項，例如`8eaef023-2b34-4da1-9baa-8bc8c9d6a490`或`contoso.onmicrosoft.com`或`common`的租用戶無關的權杖 |
| client_id | 所需 | 指派給您的應用程式，當您註冊 Azure AD 與應用程式識別碼。 您可以找到這 Azure 管理入口網站中。 按一下在**Active Directory**，按一下目錄，按一下應用程式，然後按一下 [**設定**] |
| response_type | 所需 | 必須包含`code`授權的程式碼流程。 |
| redirect_uri | 建議 | 您的應用程式，可以傳送及接收應用程式驗證回應 redirect_uri。  它必須確實符合您註冊在入口網站，但必須 url 編碼 redirect_uris 其中一項。  原生和行動應用程式，則應使用的預設值`urn:ietf:wg:oauth:2.0:oob`。 |
| response_mode | 建議 | 指定應該用來將產生的權杖返回傳送給您的應用程式的方法。  可`query`或`form_post`。  |
| 狀態 | 建議 | 包含在邀請中也會 token 回應中傳回的值。 避免[跨網站要求偽造攻擊](http://tools.ietf.org/html/rfc6749#section-10.12)通常是隨機產生的唯一值。  狀態也會使用編碼之前驗證要求發生，例如 [頁面或檢視上的應用程式中的使用者的狀態相關資訊。 |
| 資源 | 選擇性 | 應用程式識別碼 URI 網路 API （安全資源）。 若要尋找的 API，web 應用程式識別碼 URI Azure 管理入口網站中，按一下 [ **Active Directory**、 按一下目錄，按一下應用程式，然後按一下 [**設定**。 |
| 提示 | 選擇性 |  表示使用者互動的所需的類型。<p> 有效的值如下︰ <p> *登入*︰ 使用者應該會提示您要重新進行驗證。 <p> *同意*︰ 使用者同意已授與，但必須進行更新。 使用者會提示您應該同意。 <p> *admin_consent*︰ 管理員應該會提示您同意代表組織中的所有使用者 |
| login_hint | 選擇性 | 可用來預先填入 [使用者名稱/電子郵件地址] 欄位的登入頁面的使用者，如果您知道提前其使用者名稱。  通常應用程式會使用此參數期間重新驗證，已經有從先前登入使用擷取使用者名稱`preferred_username`宣告。 |
| domain_hint | 選擇性 | 提供有關租用戶或網域的使用者應該用來登入提示。 Domain_hint 的值是租用戶已註冊的網域。 如果租用戶同盟至內部部署的目錄，AAD 會重新導向至指定的租用戶同盟伺服器。 |

> [AZURE.NOTE] 如果組織中的使用者，組織的系統管理員可以同意或拒絕使用者的代表或允許使用者同意。 提供使用者同意系統管理員允許它時，才的選項。

此時，使用者會被要求輸入其認證，並同意中指定的權限`scope`查詢參數。 Azure AD 使用者會驗證並授與同意之後，傳送至您的應用程式在回應`redirect_uri`在邀請中的地址。

### <a name="successful-response"></a>成功的回應

成功的回應，可能看起來像這樣︰

```
GET  HTTP/1.1 302 Found
Location: http://localhost/myapp/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| 參數 | 描述 |
| -----------------------| --------------- |
| admin_consent | 值為 True，如果系統管理員同意同意要求提示。|
| 程式碼 | 應用程式要求授權程式碼。 應用程式可用來要求存取權杖目標資源的驗證碼。 |
| session_state | 識別目前的使用者工作階段的唯一值。 此值是 GUID，但應視為傳遞，而且檢查不透明值。 |
| 狀態 | 如果狀態參數會包含在邀請中，相同的值應該會出現在回應中。 是好的做法，以驗證狀態中的值要求與回應完全相同，再使用回應應用程式。 這可協助針對用戶端偵測[跨網站要求偽造 (CSRF) 攻擊](https://tools.ietf.org/html/rfc6749#section-10.12)。  

### <a name="error-response"></a>錯誤回應

錯誤回應可能也會傳送至`redirect_uri`，好讓應用程式可以適當地處理。

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 參數 | 描述 |
|-----------|-------------|
| 錯誤 | 錯誤的程式碼值的[OAuth 2.0 授權架構](http://tools.ietf.org/html/rfc6749)的區段 5.2 中所定義。 下表會說明 Azure AD 傳回錯誤碼。 |
| error_description | 錯誤的詳細的描述。 此訊息不是是使用者易記。 |
| 狀態 | [狀態] 值是隨機產生傳送邀請中，以防止跨網站要求偽造 (CSRF) 攻擊的回應中傳回的非重複使用值。 |

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

## <a name="use-the-authorization-code-to-request-an-access-token"></a>使用授權程式碼，要求存取權杖

既然您已取得驗證碼，並已獲授予他人權限的使用者，您可以兌換所需的資源，存取權杖的程式碼，藉由傳送的文章要求`/token`結束點︰

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%2Fmyapp%2F
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| 參數 | | 描述 |
| ----------------------- | ------------------------------- | --------------------- |
| 租用戶 | 所需 |  `{tenant}`要求的 path 中的值可用於控制可以登入應用程式。  允許的值是租用戶識別項，例如`8eaef023-2b34-4da1-9baa-8bc8c9d6a490`或`contoso.onmicrosoft.com`或`common`的租用戶無關的權杖 |
| client_id | 所需 | 指派給您的應用程式，當您註冊 Azure AD 與應用程式識別碼。 您可以找到這 Azure 傳統入口網站中。 按一下在**Active Directory**，按一下目錄，按一下應用程式，然後按一下 [**設定**] |
| grant_type | 所需 | 必須`authorization_code`授權的程式碼流程。 |
| 程式碼 | 所需 | `authorization_code`您取得前一節   |
| redirect_uri | 所需 | 相同`redirect_uri`值用來擷取`authorization_code`。 |
| client_secret | 所需的 web 應用程式 | 您為您的應用程式建立的應用程式註冊入口網站中的應用程式密碼。  它不應在原生應用程式，因為 client_secrets 無法確實儲存在裝置上。  必要的 web 應用程式與網路能夠儲存的 Api`client_secret`安全地在伺服器端。 |
| 資源 | 如果指定在授權的程式碼邀請中，還可省略，則需要 | 應用程式識別碼 URI 網路 API （安全資源）。
若要尋找應用程式識別碼 URI Azure 管理入口網站中，按一下 [ **Active Directory**目錄，按一下 [應用程式，然後按一下然後按一下 [**設定**。

### <a name="successful-response"></a>成功的回應

Azure AD 傳回存取權杖時成功的回應。 最小化網路呼叫從用戶端應用程式和其相關聯的延遲，用戶端應用程式應該快取存取權杖，權杖存留 OAuth 2.0 回應中指定。 若要判斷權杖存留時間，請使用下列一`expires_in`或`expires_on`參數值。

如果 API 資源的 web 會傳回`invalid_token`錯誤碼，這可能會指出資源具有取決於權杖已過期。 如果不同 （稱為 「 時間 skew 」） 的用戶端和資源時鐘的時間，資源可以考慮權杖清除從用戶端快取之前已過期的 token。 如果這種情況，清除快取中，從權杖，即使是在其計算結果的存留時間內仍。

成功的回應，可能看起來像這樣︰

```
{
  "access_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
"id_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.”
}

```

| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| access_token | 要求的存取權杖。 應用程式可以使用此 token 加至安全的資源，例如網路 API 進行驗證。 |
| token_type | 表示的權杖類型的值。 Azure AD 支援的唯一類型是承載者。 如需承載者權杖相關資訊，請參閱[OAuth2.0 授權架構︰ 承載者權杖使用量 (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)  |
| expires_in | 多久存取權杖是生效 （秒）。 |
| expires_on | 存取權杖何時到期時間。 日期表示的秒數為從 1970年-01-01T0:0:0Z UTC 直到的到期時間。 此值用來決定的快取的權杖存留時間。 |
| 資源 | 應用程式識別碼 URI 網路 API （安全資源）。|
| 範圍 | 模擬權限授與用戶端應用程式。 預設權限是`user_impersonation`。 受到保護的資源的擁有者可以在 Azure AD 註冊額外的值。|
| refresh_token |  OAuth 2.0 重新整理權杖中。 取得額外的存取權杖，目前存取權杖到期之後，應用程式可以使用此 token 加。  重新整理權杖久，而且可以用來延伸一段時間保留資源的存取權。 |
| id_token | 未簽署 JSON Web Token (JWT)。 應用程式可以 base64Url 解碼此 token 加要求登入的使用者資訊區段。 應用程式可以快取中的值，並顯示，但其不應使用這些授權或安全性的邊界。 |

### <a name="jwt-token-claims"></a>JWT 權杖宣告
中的值的 JWT token`id_token`可以將下列宣告解碼參數︰

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

`id_token`參數包含下列的宣告類型。 如需有關 JSON web 權杖的詳細資訊，請參閱[JWT IETF 草稿規格](http://go.microsoft.com/fwlink/?LinkId=392344)。 如需有關的權杖類型與宣告的詳細資訊，請閱讀[支援權杖和宣告類型](active-directory-token-and-claims.md)

| 宣告類型 | 描述 |
|------------|-------------|
| aud | 權杖的對象。 用戶端應用程式發出權杖，簡報對象時，`client_id`的用戶端。
| exp | 到期時間。 權杖何時到期時間。 有效的權杖，目前的日期/時間必須小於或等於`exp`值。 時間表示的秒數為從 1970 年 1 月 1 日 (1970年-01-01T0:0:0Z) UTC 發出權杖為止。 |
| family_name | 使用者的姓氏。 應用程式可以顯示此值。 |
| given_name | 使用者的第一個名稱。 應用程式可以顯示此值。 |
| iat | 發行次。 何時發行 JWT 時間。 時間表示的秒數為從 1970 年 1 月 1 日 (1970年-01-01T0:0:0Z) UTC 發出權杖為止。 |
| iss | 識別 token 發行者 |
| nbf | 不之前的時間。 權杖時有效時間。 有效的權杖，目前的日期/時間必須大於或等於 Nbf 值。 時間表示的秒數為從 1970 年 1 月 1 日 (1970年-01-01T0:0:0Z) UTC 發出權杖為止。 |
| oid | Azure AD 中的 [使用者] 物件的物件識別碼 (ID)。 |
| sub | 權杖主旨識別碼。 這是權杖說明使用者的持續性和不變的識別碼。 使用此在快取中的邏輯值。 |
| tid | 租用戶的識別項 (ID) 發出權杖 Azure AD 租用戶。 |
| unique_name | 使用者可以顯示的唯一識別碼。 這通常是使用者主要名稱 (UPN)。 |
| upn | 使用者的使用者主要名稱。 |
| ver | 版本。 JWT 權杖，通常是 1.0 版本。 |

### <a name="error-response"></a>錯誤回應

權杖發行端點錯誤是 HTTP 錯誤碼，因為用戶端直接呼叫權杖發行結束點。 除了 HTTP 狀態碼，Azure AD 權杖發行端點也會傳回 JSON 文件的描述錯誤的物件。

範例錯誤回應看起來可能像這樣︰

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| 錯誤 | 錯誤的程式碼字串，可以用來分類的錯誤，並可以用來回應錯誤。 |
| error_description | 特定的錯誤訊息，可協助開發人員識別驗證錯誤的根本原因。  |
| error_codes | STS 特定錯誤代碼，可協助診斷中的清單。 |
| 時間戳記 | 時間發生錯誤。 |
| trace_id | 可協助診斷中要求的唯一識別碼。  |
| correlation_id | 可以協助診斷元件邀請的唯一識別碼。|

#### <a name="http-status-codes"></a>HTTP 狀態碼

下表列出的權杖發行端點會傳回的 HTTP 狀態碼。 在某些情況下，出現錯誤碼，即足以描述的回應，，但發生錯誤，您必須剖析隨附 JSON 文件，並檢查其錯誤的程式碼。

| HTTP 程式碼 | 描述 |
|-----------|-------------|
| 400       | 預設 HTTP 程式碼。 在大多數情況下使用，通常是因為格式錯誤的要求。 修正，然後重新提交要求。 |
| 401       | 驗證失敗。 例如，要求遺失 client_secret 參數。|
| 403       | 授權失敗。 例如，使用者沒有存取資源的權限。 |
| 500       | 在服務發生內部錯誤。 重試要求。 |

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

## <a name="use-the-access-token-to-access-the-resource"></a>若要存取資源使用存取權杖

既然您已成功取得`access_token`，您可以使用權杖 Web api 邀請中包含中`Authorization`頁首。 [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt)規格會說明如何使用承載者權杖 HTTP 要求存取受保護的資源。

### <a name="sample-request"></a>範例要求

```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>錯誤回應

實作 RFC 6750 問題 HTTP 狀態碼的安全的資源。 如果要求不包含驗證認證或遺漏的權杖，回應包含`WWW-Authenticate`頁首。 資源伺服器要求失敗時，會以 HTTP 狀態碼和錯誤碼。

以下是回應的當用戶端要求不包含承載者權杖的成功的範例︰

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.window.net/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>參數時發生錯誤

| 參數 | 描述 |
|-----------|-------------|
| authorization_uri | 授權伺服器 URI （實際的結束點）。 此值也作為查閱索引鍵以取得關於伺服器的詳細資訊，從探索端點。 <p><p> 用戶端必須是受信任的授權伺服器進行驗證。 Azure AD 受資源，時不足，請確認 URL 開頭 https://login.windows.net 或 Azure AD 支援的另一個主機名稱。 租用戶專屬的資源應該一律會傳回租用戶專屬授權 URI。 |
| 錯誤 | 錯誤的程式碼值的[OAuth 2.0 授權架構](http://tools.ietf.org/html/rfc6749)的區段 5.2 中所定義。|
| error_description | 錯誤的詳細的描述。 此訊息不是是使用者易記。|
| resource_id | 傳回資源的唯一識別碼。 用戶端應用程式可以使用此識別碼為的值`resource`參數要求資源的權杖時。 <p><p> 請務必針對用戶端應用程式，以確認此值，否則惡意服務可能會產生攻擊**提高權限的權限** <p><p> 確認可防止攻擊的建議的策略`resource_id`符合對數的 web URL API 的存取。 例如，如果存取 https://service.contoso.com/data 時，`resource_id`可 htttps://service.contoso.com/。 用戶端應用程式必須拒絕`resource_id`的開頭不是基底 URL 除非有可靠的替代方法可確認識別碼。 |

#### <a name="bearer-scheme-error-codes"></a>承載者配置錯誤碼

RFC 6750 規格定義下列資源，使用該 WWW 驗證標頭和承載者配置在回應中的錯誤。

| HTTP 狀態碼 | 錯誤碼 | 描述 | 用戶端巨集指令 |
|------------------|------------|-------------|---------------|
| 400 | invalid_request | 要求不正確。 例如，這可能會遺失參數，或按兩次使用相同的參數。 | 修正錯誤，並再次要求。 這種錯誤只在開發以及偵測初始測試。 |
| 401 | invalid_token   | 存取權杖會遺失、 無效，或已撤銷。 Error_description 參數的值提供其他詳細資料。 |  從授權伺服器要求新的憑證。 如果新的權杖失敗，請發生意外的錯誤。 傳送給使用者和重試的錯誤訊息之後隨機延遲狀況。 |
| 403 | insufficient_scope | 存取權杖不包含存取資源所需的模擬權限。 | 傳送新的授權要求授權端點。 如果回應包含範圍參數，請使用範圍值在邀請中的資源。 |
| 403 | insufficient_access | 權杖的主旨沒有存取資源所需的權限。 | 提示若要使用不同的帳戶，或要求至指定的資源的權限的使用者。 |

## <a name="refreshing-the-access-tokens"></a>重新整理存取權杖

存取權杖短暫，而且必須重新整理後過期，繼續存取資源。 您可以重新整理`access_token`送出另一個`POST`要求`/token`端點，但這次提供`refresh_token`而不是`code`。

重新整理權杖沒有指定的存留時間。 一般而言的生命週期的重新整理權杖是超長。 不過，在某些情況下中, 重新整理權杖過期、 已撤銷，或沒有足夠的權限，要執行的動作。 您的應用程式必須預期及處理正確的權杖發行端點所傳回的錯誤。

當您收到的重新整理 token 錯誤的回覆時，捨棄目前的重新整理權杖並要求新的授權程式碼或存取權杖。 特別是，當使用重新整理權杖授權的程式碼授與的流程，如果您收到回應`interaction_required`或`invalid_grant`錯誤碼，丟棄重新整理權杖，並要求新的驗證碼。

樣本要求 （您也可以使用的**常見**的端點） 的**租用戶特定**點開始新的存取權杖使用的重新整理權杖看起來像這樣︰

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```
| 參數 | 描述 |
|-----------|-------------|
| access_token | 新存取權杖要求。|
| expires_in   | 剩餘的存留時間權杖秒數。 典型的值為 3600 （1 小時）。 |
| expires_on   | 到期日期和時間的 token。 日期表示的秒數為從 1970年-01-01T0:0:0Z UTC 直到的到期時間。 |
| refresh_token | 新的 OAuth 2.0 refresh_token 可以用來在此回應項目到期時，要求新存取權杖。 |
| 資源     | 受到保護的資源，可用來存取權杖識別用來存取。 |
| 範圍        | 模擬權限授與原生的用戶端應用程式。 預設權限是**user_impersonation**。 目標資源的擁有者可以在 Azure AD 註冊替代的值。 |
| token_type   | 權杖類型。 僅支援的值是**承載者**。 |

### <a name="successful-response"></a>成功的回應

成功的權杖回應看起來像︰

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```

### <a name="error-response"></a>錯誤回應

範例錯誤回應看起來可能像這樣︰

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872.  This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant.  You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- |
| 錯誤 | 錯誤的程式碼字串，可以用來分類的錯誤，並可以用來回應錯誤。 |
| error_description | 特定的錯誤訊息，可協助開發人員識別驗證錯誤的根本原因。  |
| error_codes | STS 特定錯誤代碼，可協助診斷中的清單。 |
| 時間戳記 | 時間發生錯誤。 |
| trace_id | 可協助診斷中要求的唯一識別碼。  |
| correlation_id | 可以協助診斷元件邀請的唯一識別碼。|

如需錯誤碼和建議的用戶端動作的說明，請參閱[錯誤碼 token 端點錯誤](#error-codes-for-token-endpoint-errors)。
