 <properties
   pageTitle="Azure AD 權杖參照 |Microsoft Azure"
   description="如需瞭解和評估 SAML 2.0 與 JSON Web 權杖 (JWT) token 發出的 Azure Active Directory (AAD) 中的宣告輔助線"
   documentationCenter="na"
   authors="bryanla"
   services="active-directory"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/06/2016"
   ms.author="mbaldwin"/>

# <a name="azure-ad-token-reference"></a>Azure AD 權杖參照

Azure Active Directory (Azure AD) 會發出數種類型的安全性權杖處理的每個驗證流程。 這份文件將說明格式、 安全性特性，以及每種類型的權杖的內容。

## <a name="types-of-tokens"></a>權杖的類型

Azure AD 支援[OAuth 2.0 授權通訊協定](active-directory-protocols-oauth-code.md)，使用 access_tokens 和 refresh_tokens。  它也支援的驗證及登入透過[OpenID 連線](active-directory-protocols-openid-connect-code.md)，會介紹第三個類型的權杖，id_token。  每個這些權杖表示為 「 承載者權杖 」。

承載者權杖是輕量型安全性權杖受保護的資源 」 承載者] 存取。 因此，「 承載者 」 是可以呈現權杖任何方。 雖然 Azure AD 驗證，才能接收承載者權杖，就必須採取步驟安全性權杖，以避免非預期的廠商所攔截。 承載者權杖沒有使用時，防止未經授權的合作對象的內建機制，因為他們必須在安全通道，例如傳輸層安全性 (HTTPS) 傳輸。 如果承載者權杖會以清除傳輸，男人增益集攻擊可用來取得權杖，以及未經授權存取受保護的資源。 儲存或快取承載者權杖，以供日後使用時，就會套用相同的安全性原則。 確保您的應用程式來傳送，並將承載者權杖儲存安全的方式。 更多承載者權杖上的安全性考量，請參閱[RFC 6750 步驟 5](http://tools.ietf.org/html/rfc6750)。

Azure AD 所發行的權杖許多實作 JSON Web 權杖，或 JWTs。  JWT 是精簡、 URL 安全的方法雙方之間傳送的資訊。  JWTs 中包含的資訊承載者和主旨的權杖相關即為 「 宣告 」 或判斷提示的資訊。  在 JWTs 宣告是編碼及序列化傳輸的 JSON 物件。  由於發出 Azure AD JWTs 的簽章，但未加密，您可以輕鬆地偵錯檢查 JWT 的內容。  有適用於這麼做，例如[jwt.calebb.net](http://jwt.calebb.net)的數個工具。 如需有關 JWTs 的詳細資訊，您可以參考[JWT 規格](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)。

## <a name="idtokens"></a>Id_tokens

Id_tokens 是一種執行驗證使用[OpenID 連線](active-directory-protocols-openid-connect-code.md)時，會收到您的應用程式的登入安全性 token。  他們[JWTs](#types-of-tokens)，以表示，包含您可以用來登入您的應用程式的使用者。  您可以在 id_token 使用宣告，如適時-通常用來顯示帳戶資訊，或應用程式中進行存取控制的決策。

登入，但這次不加密 Id_tokens。  當您的應用程式收到 id_token 時，它必須[驗證簽章](#validating-tokens)以證明權杖的真實性和驗證權杖中的幾個宣告證明其有效性。  驗證應用程式的宣告案例需求而有所不同，但有一些[常見的宣告驗證](#validating-tokens)您的應用程式必須在每個案例中執行的。

Id_tokens 宣告，以及範例 id_token，請參閱下一節的資訊。  請注意，在 id_tokens 宣告不會傳回任何特定順序。  此外，新的宣告可以導入 id_tokens 任何時間-您的應用程式應該不中斷會介紹新宣告。  下列清單包含您的應用程式可以可靠解譯此撰寫時的宣告。  如有需要，可以中[OpenID 連線規格](http://openid.net/specs/openid-connect-core-1_0.html)找到更多詳細資料。

#### <a name="sample-idtoken"></a>範例 id_token

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [AZURE.TIP] 練習，請嘗試以將其貼到[calebb.net](http://jwt.calebb.net)檢查的範例 id_token 宣告。

#### <a name="claims-in-idtokens"></a>在 [id_tokens 宣告

| JWT 宣告 | 名稱 | 描述 |
|-----------|------|-------------|
| `appid`| 應用程式識別碼 | 識別用來存取資源的權杖應用程式。 應用程式可做為自己或代表使用者。 應用程式識別碼通常表示的應用程式物件，但它也可以在 Azure AD 代表服務主體的物件。 <br><br> **範例 JWT 值**︰ <br> `"appid":"15CB020F-3984-482A-864D-1D92265E8268"` |
| `aud`| 對象 | 權杖的收件的者。 接收權杖的應用程式必須確認是正確對象值] 和 [拒絕任何適用於不同的對象的權杖。 <br><br> **範例 SAML 值**︰ <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **範例 JWT 值**︰ <br> `"aud":"https://contoso.com"` |
| `appidacr`| 應用程式驗證內容類別參考 | 指出用戶端驗證的方式。 為公用的用戶端，值為 0。 如果使用用戶端識別碼] 和 [用戶端密碼，值為 1。 <br><br> **範例 JWT 值**︰ <br> `"appidacr": "0"`|
| `acr`| 驗證內容類別參考 | 指出主旨已驗證的方式，而不是用戶端應用程式驗證內容類別參考宣告。 「 0 」 表示使用者驗證不符合 ISO/IEC 29115 的需求。 <br><br> **範例 JWT 值**︰ <br> `"acr": "0"`|
| | 立即驗證 | 日期及時間發生驗證記錄。 <br><br> **範例 SAML 值**︰ <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` |
| `amr`| 驗證方法 | 識別已驗證的權杖主旨的方式。 <br><br> **範例 SAML 值**︰ <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **範例 JWT 值**︰`“amr”: ["pwd"]` |
| `given_name`| 名字 | 提供第一個或上 Azure AD 使用者物件 「 指定 」 的使用者名稱。 <br><br> **範例 SAML 值**︰ <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **範例 JWT 值**︰ <br> `"given_name": "Frank"` |
| `groups`| 群組 | 提供代表主體的群組成員資格的物件識別碼。 這些值是唯一 （請參閱物件識別碼），而且可安全地用於管理存取權，例如強制執行存取資源的授權。 包含在群組宣告群組設定為基礎每個應用程式，透過應用程式顯示的 「 groupMembershipClaims 」 屬性。 Null 值會排除所有群組，僅 Active Directory 安全性群組的成員資格和值為 「 所有 」 會包含安全性群組以及 Office 365 通訊群組清單中會包含 「 SecurityGroup 」 的值。 <br><br> **範例 SAML 值**︰ <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **範例 JWT 值**︰ <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
| `idp` | 身分識別提供者 | 記錄驗證的權杖主旨的身分識別提供者。 此值會與發行者宣告的值相同的除非的使用者帳戶是不同的租用戶比發行者。 <br><br> **範例 SAML 值**︰ <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **範例 JWT 值**︰ <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` | IssuedAt | 儲存權杖的發出。 常用測量 token 的有效期限。 <br><br> **範例 SAML 值**︰ <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **範例 JWT 值**︰ <br> `"iat": 1390234181` |
| `iss` | 發行者 | 識別安全性 token 服務 (STS) 建構並傳回權杖。 Azure AD 傳回權杖，在發行者為 sts.windows.net。 發行者宣告值 GUID 是租用戶 Azure AD 目錄識別碼。 租用戶 ID 是不變，而且可靠的目錄識別碼。 <br><br> **範例 SAML 值**︰ <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **範例 JWT 值**︰ <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` | 姓氏 | 定義 Azure AD 使用者物件中提供的最後一個名稱、 姓氏或系列中的使用者名稱。 <br><br> **範例 SAML 值**︰ <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **範例 JWT 值**︰ <br> `"family_name": "Miller"` |
| `unique_name`| 名稱 | 提供識別的權杖主旨的人力可讀取值。 此值不保證租用戶中是唯一的設計用來使用僅供顯示用。 <br><br> **範例 SAML 值**︰ <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **範例 JWT 值**︰ <br> `"unique_name": "frankm@contoso.com"` |
| `oid` | 物件識別碼 | Azure AD 中包含物件的唯一的識別碼。 此值是不變，無法重新指派或重複使用。 您可以使用的物件識別碼來識別 Azure AD 查詢中的物件。 <br><br> **範例 SAML 值**︰ <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **範例 JWT 值**︰ <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` | 角色 | 代表主旨已授與直接或間接透過群組成員資格，可以用來強制執行角色型存取控制的所有應用程式角色。 應用程式角色定義為基礎每個應用程式，透過`appRoles`應用程式顯示的屬性。 `value`每個應用程式角色的屬性是出現在 [宣告角色中的值。 <br><br> **範例 SAML 值**︰ <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **範例 JWT 值**︰ <br> `“roles”: ["Admin", … ]` |
| `scp` | 範圍 | 指出模擬權限授與用戶端應用程式。 預設權限是`user_impersonation`。 受到保護的資源的擁有者可以在 Azure AD 註冊額外的值。 <br><br> **範例 JWT 值**︰ <br> `"scp": "user_impersonation"`|
| `sub` |主旨| 識別哪些權杖判斷提示資訊，例如使用者的應用程式的本金。 此值不變，而無法重新指派或重複使用，因此，可以用來安全地執行授權檢查。 由於主旨永遠顯示權杖 Azure AD 問題，我們建議您一般用途授權系統中使用此值。 <br> `SubjectConfirmation`未宣告。 說明如何驗證的權杖主旨。 `Bearer`指出，其擁有的權杖來確認主旨。 <br><br> **範例 SAML 值**︰ <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **範例 JWT 值**︰ <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"`|
| `tid` | 租用戶識別碼 | 不變，非可重複使用識別項，識別發出權杖租用戶的目錄。 您可以使用此值，存取在多租用戶應用程式中的特定租用戶的目錄資源。 例如，您可以使用此值來識別圖形 API 在通話中的租用戶。 <br><br> **範例 SAML 值**︰ <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **範例 JWT 值**︰ <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"`|
| `nbf`, `exp`|權杖為單位的週期 | 定義權杖是有效的時間間隔。 驗證的權杖服務應該確認目前日期的權杖存留時間，還它應該拒絕權杖內。 服務可能允許五分鐘的時間 token 存留時間範圍外的時間 (「 時間 skew 」) 的任何差異之間 Azure AD 和服務。 <br><br> **範例 SAML 值**︰ <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **範例 JWT 值**︰ <br> `"nbf":1363289634, "exp":1363293234` |
| `upn`| 使用者主要名稱 | 儲存使用者主體的使用者名稱。<br><br> **範例 JWT 值**︰ <br> `"upn": frankm@contoso.com`|
| `ver`| 版本 | 儲存權杖的版本號碼。 <br><br> **範例 JWT 值**︰ <br> `"ver": "1.0"`|

## <a name="access-tokens"></a>存取權杖

存取權杖，目前只適用於 Microsoft 服務。  您的應用程式應該不需要執行任何驗證或檢查存取權杖的任何目前支援的案例。  您可以視為存取權杖完全不透明-他們是只是字串的應用程式可以將 HTTP 要求傳遞給 Microsoft。

當您要求存取權杖時，Azure AD 也會傳回存取權杖，供您的應用程式使用的部分中繼資料。  這項資訊包含存取權杖，就是有效範圍的到期的時間。  這可讓您執行智慧的快取存取權杖無須解析開啟的應用程式存取權杖本身。

## <a name="refresh-tokens"></a>重新整理權杖

重新整理權杖會安全性權杖，您的應用程式可用來擷取 OAuth 2.0 流程中的新存取權杖。  可讓您的應用程式，而不需使用者互動達成長期資源的存取權代表使用者。

重新整理權杖是多的資源，這表示可能會收到一資源的權杖要求期間但贖回存取權杖完全不同的資源。 若要指定多的資源，請將`resource`參數在邀請中的目標的資源。

重新整理權杖會完全不透明到您的應用程式。 他們的年齡久，但您的應用程式應該寫入預期的重新整理權杖會最後的任何一段時間。  重新整理權杖可失效任何時間內的各種不同的原因。  您知道的重新整理權杖是不是有效的應用程式的唯一方式是嘗試兌換其藉由 Azure AD token 端點的權杖要求。

當您兌換新的存取權杖的重新整理權杖時，您會收到新的重新整理權杖 token 回應。  您應該儲存新發行的重新整理權杖，取代在邀請中所使用的項目。  這會保證您重新整理權杖維持有效盡可能久。

## <a name="validating-tokens"></a>驗證的權杖

在目前，只 token 驗證您的用戶端應用程式應該需要執行進行驗證 id_tokens。  驗證 id_token，才能 id_token 的簽章和 id_token 中的宣告，應該驗證您的應用程式。

我們提供文件庫和程式碼範例，顯示如何輕鬆地處理 token 驗證]，如果您想要瞭解的基礎程序。  還有幾個協力廠商開啟來源文件庫可供 JWT 驗證，幾乎所有的平台和語言的至少其中一個選項。 如需有關 Azure AD 驗證文件庫和程式碼範例的詳細資訊，請參閱[Azure AD 驗證文件庫](active-directory-authentication-libraries.md)。

#### <a name="validating-the-signature"></a>驗證簽章

JWT 包含三個區段，並以分隔`.`字元。  第一個區段稱為**頁首**，**本文**中，為第二個和第三個**簽章**。  簽章區段可用來驗證 id_token 的真偽，使受您的應用程式。

Id_Tokens 為使用業界標準的非對稱加密演算法，例如 RSA 256 登入。 Id_token 的標頭包含用來登入權杖的索引鍵和加密方法的相關資訊︰

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "kriMPdmBvx68skT8-mPAB3BseeA"
}
```

`alg`宣告指出用來登入權杖，時演算法`x5t`宣告指示的特定公開金鑰用來登入權杖。

在任何指定時間點，Azure AD 可能登入使用下列其中一組特定公開私密金鑰組 id_token。 Azure AD 旋轉的金鑰定期，可能的設定，因此您的應用程式應該撰寫自動處理這些的主要變更。  比較合理的頻率，以檢查有更新公開金鑰使用 Azure AD 是每 24 小時。

您可以取得所需使用 OpenID 連線中繼資料文件位於驗證簽章的簽章索引鍵資料︰

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [AZURE.TIP] 請嘗試在瀏覽器中的此 URL ！

此中繼資料文件是資訊的 JSON 物件包含多個項有用，例如執行 OpenID 連線驗證所需的各種結束點的位置。  

也包含`jwks_uri`，其中提供的一組公開金鑰用來登入權杖的位置。  JSON 文件位於`jwks_uri`包含所有的特定時間內使用公用的重要資訊。  您的應用程式可以使用`kid`宣告 JWT 標頭，選取 [這份文件中的哪些公開金鑰已用來登入的特定的權杖中。  然後，可以執行使用正確的公開金鑰及指定的演算法驗證簽章。

執行驗證簽章外這份文件的範圍-有可供有助您取得執行此作業必要的許多開啟來源文件庫。

#### <a name="validating-the-claims"></a>驗證宣告

當您的應用程式收到 id_token 在使用者登入時，則它也應該執行針對宣告的一些檢查 id_token 中。  包括但不是限於︰

  - **對象**宣告式驗證 id_token 要提供給您的應用程式。
  - **不之前**與**期限**宣告式驗證 id_token 尚未過期。
  - **發行者**宣告式驗證的權杖確實核發至您的應用程式 Azure AD。
  - **Nonce** -降低 token 重複攻擊。
  - 以及其他功能...

宣告驗證您的應用程式應該執行的完整清單，請參閱[OpenID 連線規格](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation)。

詳細資料，這些宣告的預期值會包含前[id_token 區段](#id-tokens)一節。

## <a name="sample-tokens"></a>範例權杖

本節會顯示 SAML 和 JWT Azure AD 傳回的權杖的範例。 這些範例可讓您看到的內容中的宣告。
SAML 權杖

這是一般的 SAML 權杖的範例。

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

### <a name="jwt-token---user-impersonation"></a>JWT 權杖-使用者模擬

這是範例的一般 JSON web 權杖 (JWT) 授權的程式碼授與流程中使用。
宣告，除了權杖會包含在**ver**和**appidacr**，驗證內容類別參考，亦即指出在用戶端驗證的方式版本號碼。 為公用的用戶端，值為 0。 如果使用的用戶端識別碼或用戶端私人的值為 1。

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

## <a name="related-content"></a>相關的內容
- 若要進一步瞭解管理透過 Azure AD 圖形 API 的權杖為單位的週期原則，請參閱 Azure AD 圖形[原則作業](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations)與[原則實體](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity)。
- 如需詳細資訊和範例管理原則透過 PowerShell cmdlet，包括範例，請參閱[設定權杖存留時間 Azure AD 中](active-directory-configurable-token-lifetimes.md)。 
