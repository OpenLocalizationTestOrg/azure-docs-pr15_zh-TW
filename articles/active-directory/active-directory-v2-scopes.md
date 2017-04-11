<properties
    pageTitle="Azure AD v2.0 範圍、 權限，並同意 |Microsoft Azure"
    description="Azure AD v2.0 端點，包括範圍、 權限，以及同意授權的描述。"
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
    ms.date="09/30/2016"
    ms.author="dastrock"/>

# <a name="scopes-permissions--consent-in-the-v20-endpoint"></a>範圍]、 [權限] 與 [同意 v2.0 端點

Azure AD 與整合的應用程式，請遵循可讓使用者控制應用程式存取其資料的方式的特定授權模型。  此授權模型 v2.0 實作已更新，變更應用程式必須與 Azure AD 互動的方式。  本主題會說明此授權模型，包括範圍、 權限，以及同意的基本概念。

> [AZURE.NOTE]
    並非所有的 Azure Active Directory 案例與功能支援 v2.0 端點。  若要判斷是否您應該使用 v2.0 端點，請閱讀有關[v2.0 限制](active-directory-v2-limitations.md)。

## <a name="scopes--permissions"></a>範圍與權限

Azure AD 實作[OAuth 2.0](active-directory-v2-protocols.md)授權通訊協定，這是一種方法可以讓第 3 的廠商應用程式，代表使用者存取網站裝載的資源。  資源識別項或**應用程式識別碼 URI**，會有與 Azure AD 整合任何 web 裝載的資源。  例如，Microsoft 的網站裝載資源包括︰

- Office 365 整合郵件 API:`https://outlook.office.com`
- Azure AD 圖形 API:`https://graph.windows.net`
- Microsoft Graph:`https://graph.microsoft.com`

是一樣的具有與 Azure AD 整合任何第 3 廠商資源。  任何這些資源也可以定義一組可用來劃分成較小的區塊該資源的功能權限。  例如，Microsoft Graph 已定義一些權限︰

- 閱讀使用者的行事曆
- 撰寫使用者的行事曆
- 傳送郵件的使用者
- [+ 更多](https://graph.microsoft.io)

定義這些權限，資源可以有繼承權控制其資料，及如何外面的世界公開。  第 3 的廠商應用程式就可以從使用者-要求這些權限，使用者必須核准權限，才能代表自己可以採取行動應用程式。  依切割成較小的權限集資源的功能，第 3 廠商應用程式可以要求特定權限所需執行其職責的內建。  也可讓使用者知道完全如何應用程式會使用其資料，使其更自信的應用程式的行為不惡意。

Azure AD 中與 OAuth，這些權限即為**範圍**。  您也可能會看到這些稱為**oAuth2Permissions**。  範圍表示 Azure AD 中的字串值。  繼續 Microsoft Graph 的範例，每個權限的範圍值就是︰

- 請參閱使用者的行事曆︰`Calendar.Read`
- 撰寫使用者的行事曆︰`Mail.ReadWrite`
- 傳送郵件的使用者︰`Mail.Send`

應用程式可以要求 v2.0 端點，指定的範圍，以要求這些權限，如下所述。

## <a name="openid-connect-scopes"></a>OpenId 連線的範圍

V2.0 實作 OpenID 連線有幾個明確定義的範圍，不會套用到任何特定的資源- `openid`， `email`， `profile`，及`offline_access`。

#### <a name="openid"></a>OpenId

如果應用程式執行登入使用[OpenID 連線](active-directory-v2-protocols.md#openid-connect-sign-in-flow)，必須要求`openid`範圍。  `openid`範圍的 「 檢視您的設定檔和連線至應用程式與服務使用您的 Microsoft 帳戶 」 權限為個人的 Microsoft 帳戶同意畫面和為 「 登入 」 權限的 [公司帳戶同意] 畫面中會出現。  此權限可讓應用程式的形式收到使用者的唯一識別碼`sub`宣告。  也可以提供應用程式存取權的使用者資訊結束點。  `openid`範圍也可在 v2.0 token 端點取得 id_tokens，可以用來保護不同元件的應用程式之間的 HTTP 通話。

#### <a name="email"></a>電子郵件

`email`範圍可包含沿著`openid`範圍，以及任何其他人。  它可以提供使用者的主要電子郵件地址的表單中的應用程式存取`email`宣告。  `email`宣告會只會包含在權杖如果使用者帳戶，不一定總是大小寫與相關聯的電子郵件地址。  如果使用`email`範圍，您的應用程式還需要準備處理的大小寫`email`宣告不存在於權杖。

#### <a name="profile"></a>設定檔

`profile`範圍可包含沿著`openid`範圍，以及任何其他人。  它可以提供豐富的使用者的相關資訊的應用程式存取。  這包含，但不限於使用者的名字、 姓氏、 慣用的使用者名稱、 物件識別碼等等。  針對指定的使用者 id_tokens 提供的設定檔宣告的完整清單，請參閱[v2.0 權杖參照](active-directory-v2-tokens.md)。

#### <a name="offlineaccess"></a>Offline_access

[`offline_access`範圍](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess)允許您存取資源的應用程式使用者代表一段時間。  在 [工作帳戶同意] 畫面中，此範圍會顯示為 「 隨時存取您的資料 」 權限。  在 [個人 Microsoft 帳戶同意] 畫面，其會顯示為 「 隨時存取您的資訊 」 權限。  當使用者核准`offline_access`範圍，您的應用程式將會啟用重新整理權杖接收 v2.0 token 結束點。  重新整理權杖會久，並允許您的應用程式取得新的存取權杖，為舊的過期。

如果您的應用程式不要求`offline_access`範圍，就不會收到 refresh_tokens。  這表示，當您兌換 authorization_code [OAuth 2.0 授權碼流程](active-directory-v2-protocols.md#oauth2-authorization-code-flow)中的，您只會收到回從 access_token`/token`結束點。  該 access_token 仍會維持有效的一段時間 （通常是一小時），但最後便會到期。  於的時間，您的應用程式中的點需要將使用者導向返回`/authorize`擷取新的 authorization_code 結束點。  在此重新導向]，使用者可能或可能不需要再次輸入其認證，或重新同意權限，而定的應用程式類型。

如需有關如何存取和使用重新整理權杖，請參閱[v2.0 通訊協定參照](active-directory-v2-protocols.md)。


## <a name="requesting-individual-user-consent"></a>要求的個別使用者同意

在[OpenID 連線或 OAuth 2.0](active-directory-v2-protocols.md)授權邀請，應用程式可以要求需要使用的權限`scope`查詢參數。  例如，當使用者登入應用程式，應用程式會傳送要求如下 （換行，提高可讀性）︰

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendar.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

`scope`參數是應用程式要求的範圍的空格分隔清單。  將資源的識別項 (URI 應用程式識別碼) 範圍值所指出每個個別的範圍。  上述要求指出應用程式需要讀取使用者的行事曆及傳送電子郵件的使用者權限。

使用者輸入其認證後，會檢查 v2.0 端點的**使用者同意**相符的記錄。  如果使用者不具有過去同意要求的權限的任何，v2.0 端點會要求使用者授與要求的權限。  

![公司帳戶同意螢幕擷取畫面](../media/active-directory-v2-flows/work_account_consent.png)

當使用者核准權限時，讓使用者不需要重新同意在後續登增益集，就會記錄同意。

## <a name="requesting-consent-for-an-entire-tenant"></a>要求同意為整個承租人

組織購買的授權或應用程式的訂閱時，通常他們想要完整的員工佈建它。  此程序的一部分，公司系統管理員可以授與應用程式必須採取行動代表任何員工同意。  授與同意整個的租用戶，該組織的員工不會發生的應用程式的同意畫面。

如需要求同意所有使用者的租用戶，您的應用程式可以使用**系統同意端點**，如下所述。

## <a name="admin-restricted-scopes"></a>管理員限制範圍

某些 Microsoft 生態高權限權限可以標示為**管理員限制**。  這類範圍的範例包括︰

- 閱讀 organizaion 目錄資料︰`Directory.Read`
- 寫入組織的目錄中的資料︰`Directory.ReadWrite`
- 在組織的目錄中讀取安全性群組︰`Groups.Read.All`

消費者使用者可能會授與應用程式存取這些資料，而組織的使用者權限授與存取權的同一公司的機密資料集。  如果您的應用程式從要求存取這些權限的組織的使用者，使用者就會收到錯誤訊息，指出他們的授權同意到您的應用程式權限。

如果您的應用程式需要存取這些管理員限制的範圍組織，您應該要求其直接從公司系統管理員也使用**系統同意端點**，如下所述。

當系統管理員授與這些權限透過管理員同意端點時，同意會授與所有使用者的租用戶上所述。

## <a name="using-the-admin-consent-endpoint"></a>使用管理員同意端點

遵循下列步驟，您的應用程式能夠收集指定租用戶，包括管理員限制範圍中的所有使用者的權限。  若要查看執行下列步驟 desribed 的程式碼範例，請參閱[管理員限制的範圍範例](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2)。

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>要求應用程式註冊入口網站中的權限

- 如果您未，請瀏覽[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)，或[建立應用程式](active-directory-v2-app-registration.md)中的應用程式。
- 找出**Microsoft Graph 權限**] 區段，然後新增您的應用程式的需求的權限。
- 請確定**儲存**應用程式註冊

#### <a name="recommended-sign-the-user-into-your-app"></a>建議︰ 登入您的應用程式的使用者

通常時建立的應用程式使用管理員同意端點，應用程式會需要的頁面/檢視，讓管理員核准應用程式的權限。  此頁面可應用程式的註冊流程應用程式的設定或專用的 [連線] 流程的一部分。  在許多情況下，讓來顯示此應用程式的 「 連接 」 檢視使用者具有以公司或學校的 Microsoft 帳戶登入後才。

登入這個應用程式的使用者，可讓您識別 organziation 管理員所屬之前要求他們核准的必要權限。  雖然不全然必要，其可協助您建立更容易瞭解您組織的使用者體驗。  若要登入使用者，請遵循我們[v2.0 通訊協定教學課程](active-directory-v2-protocols.md)。

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
| 租用戶 | 所需 | 您想要邀請的權限目錄租用戶。  提供 guid 或好記的名稱格式。 |
| client_id | 所需 | 註冊入口網站 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) 指派給您的應用程式的應用程式識別碼。 |
| redirect_uri | 所需 | 您要傳送給您的應用程式處理回應 redirect_uri。  它必須確實符合您註冊入口網站中 redirect_uris 其中一項。 |
| 狀態 | 建議 | 包含在邀請中也會 token 回應中傳回的值。  它可以是您想要的任何內容的字串。  狀態會用於驗證要求發生，例如 [頁面或檢視上之前編碼的應用程式中的使用者的狀態相關資訊。 |

此時，Azure AD 會強制執行只租用戶系統管理員可以完成要求登入。  系統管理員會被要求核准所有您在註冊入口網站應用程式要求的權限。 

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
如果管理員沒有核准您的應用程式的權限，將會失敗的回應︰

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| 參數 | 描述 |
| ----------------------- | ------------------------------- | --------------- |
| 錯誤 | 錯誤的程式碼字串，可以用來分類的錯誤，並可以用來回應錯誤。 |
| error_description | 特定的錯誤訊息，可協助開發人員找出錯誤的根本原因。  |

當您收到來自管理員同意端點成功的回應時，您的應用程式已取得所需的權限。  您現在可以移動到要求的權杖所需的資源，如下所述。

## <a name="using-permissions"></a>使用 [權限

取得使用者同意應用程式的權限之後，您的應用程式可以取得存取權杖，表示您的應用程式權限來存取有些容量中的資源。  指定的存取權杖只能用於單一的 resorce，但編碼內會出現您的應用程式已被授與該資源的每個權限。  若要取得存取權杖，您的應用程式可以對 v2.0 token 端點進行要求︰

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

產生存取權杖則可在 HTTP 要求資源，其會可靠的資源，表示您的應用程式具有適當的權限，才能執行指定的工作。  

如需詳細資訊，OAuth 2.0 通訊協定以及如何取得存取權杖，請參閱[v2.0 端點通訊協定參照](active-directory-v2-protocols.md)。
