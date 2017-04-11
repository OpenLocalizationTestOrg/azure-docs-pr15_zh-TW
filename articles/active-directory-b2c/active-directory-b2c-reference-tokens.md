<properties
    pageTitle="Azure Active Directory B2C |Microsoft Azure"
    description="權杖中 Azure Active Directory B2C 發出的類型。"
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


# <a name="azure-ad-b2c-token-reference"></a>Azure AD B2C︰ 權杖參照

Azure Active Directory (Azure AD) B2C 發出數種類型的安全性權杖，因為它會處理各項[驗證流程](active-directory-b2c-apps.md)。 這份文件將說明格式、 安全性特性，以及每種類型的權杖的內容。

## <a name="types-of-tokens"></a>權杖的類型

Azure AD B2C 支援[OAuth 2.0 授權通訊協定](active-directory-b2c-reference-protocols.md)，使用的存取權杖重新整理權杖。 也支援驗證與登入透過[OpenID 連線](active-directory-b2c-reference-protocols.md)，會介紹第三個類型的權杖︰ ID 權杖。 每個這些權杖表示為承載者權杖。

承載者權杖是輕量型安全性權杖受保護的資源 」 承載者] 存取。 承載者是可以呈現權杖任何方。 Azure AD 它可以接收承載者權杖之前，必須先驗證合作對象。 但如果所需的步驟不會移至安全的 token 傳輸和儲存空間，是攔截及非預期的廠商所使用。 某些安全性權杖內建機制來防止未經授權的合作對象使用，但是承載者權杖沒有這個機制。 他們必須在安全通道，例如傳輸層安全性 (HTTPS) 傳輸。

如果承載者權杖傳輸到外部安全通道，惡意派可用來攔截攻擊取得權杖，並使用未經授權存取受保護的資源。 儲存或以供日後使用快取承載者權杖時，就會套用相同的安全性原則。 確保您的應用程式來傳送，並將承載者權杖儲存安全的方式。

在承載者權杖額外的安全性考量，請參閱[RFC 6750 步驟 5](http://tools.ietf.org/html/rfc6750)。

Azure AD B2C 問題的權杖許多實作為 JSON 網頁權杖 (JWTs)。 JWT 是精簡、 URL 安全的方法雙方之間傳送的資訊。 JWTs 包含稱為宣告的資訊。 這些是承載者的相關資訊的執行方式與權杖的主旨。 在 JWTs 宣告是編碼與傳輸序列化 JSON 物件。 Azure AD B2C 發出 JWTs 的簽名，但未加密，因為您可以輕鬆地檢查 JWT 偵錯的內容。 可使用數種工具，可以這麼做，包括[calebb.net](http://calebb.net)。 如需有關 JWTs 的詳細資訊，請參閱[JWT 規格](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)。

### <a name="id-tokens"></a>識別碼權杖

識別碼權杖是一種應用程式會接收來自 Azure AD B2C 的安全性 token`authorize`和`token`結束點。 識別碼權杖會顯示成[JWTs](#types-of-tokens)，並且包含可用來找出您的應用程式中的使用者的宣告。 從時取得識別碼權杖`authorize`端點，通常用來登入 web 應用程式的使用者。 從時取得識別碼權杖`token`端點，他們可以傳送 HTTP 要求期間的相同的應用程式或服務的兩個元件之間的通訊。 您可以使用宣告識別碼權杖中，根據您的需要。 他們常用顯示帳戶資訊，或在 [應用程式中進行存取控制的決策。  

識別碼權杖簽署，但不是經過加密這一次。 當您的應用程式或 API 收到 ID 權杖時，就必須以證明權杖是真正的 [[驗證簽章](#token-validation)。 您的應用程式或 API 也必須驗證權杖中以證明是有效的幾個宣告。 根據案例的需求，宣告驗證應用程式而有所不同，但您的應用程式必須執行一些[常見的宣告驗證](#token-validation)中每一種情況。

#### <a name="sample-id-token"></a>範例識別碼權杖
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

### <a name="access-tokens"></a>存取權杖

存取權杖也是一種應用程式會接收來自 Azure AD B2C 的安全性 token`authorize`和`token`結束點。 存取權杖也會顯示成[JWTs](#types-of-tokens)，並且包含可用來識別使用者在您的 web 服務與 Api 的宣告。

簽署存取權杖，，但不是這次-加密和 id 權杖十分相似。  存取權杖應該用於提供 web 服務與 Api 的存取，以識別與使用者進行驗證，在這些服務。  不過，它們不提供任何判斷提示的授權，這些服務。  也就是說，`scp`宣告存取權杖中的不會限制或否則代表的權杖主旨已授與存取權。

當您的 API 收到存取權杖時，它必須以證明權杖是真正的 [[驗證簽章](#token-validation)。 您的 API 也必須驗證權杖中以證明是有效的幾個宣告。 根據案例的需求，宣告驗證應用程式而有所不同，但您的應用程式必須執行一些[常見的宣告驗證](#token-validation)中每一種情況。

### <a name="claims-in-id--access-tokens"></a>宣告識別碼與存取權杖

當您使用 Azure AD B2C 時，您會有繼承權控制您的權杖的內容。 您可以設定[原則](active-directory-b2c-reference-policies.md)傳送您的應用程式需要為其作業的宣告特定資料集的使用者。 這些宣告可以包含標準的屬性，例如使用者的`displayName`和`emailAddress`。 他們也可以包含[自訂使用者屬性](active-directory-b2c-reference-custom-attr.md)，您可以定義 B2C 目錄中。 每個識別碼與存取權杖，您會收到包含一組特定的安全性相關宣告。 您的應用程式可以使用這些宣告安全地驗證的使用者和要求。

請注意，在 ID 權杖宣告不會傳回任何特定順序。 此外，新的宣告可以隨時推出 ID 權杖中。 為新的宣告會介紹，應該不會中斷您的應用程式。 以下是您希望識別碼與存取權杖發出 Azure AD B2C 存在於宣告。 任何其他宣告取決於原則。 練習，請嘗試以將其貼到[calebb.net](http://calebb.net)檢查範例識別碼權杖宣告。 在[OpenID 連線規格](http://openid.net/specs/openid-connect-core-1_0.html)找進一步的詳細資料。

| 名稱 | 宣告 | 範例值 | 描述 |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| 對象 | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | 對象宣告識別權杖收件的的者。 Azure AD B2C，簡報對象是您的應用程式的應用程式識別碼，所指派的應用程式註冊入口網站應用程式。 您的應用程式應該驗證此值，並拒絕權杖，如果不相符。 |
| 發行者 | `iss` | `https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | 此宣告識別安全性 token 服務 (STS) 建構並傳回權杖。 它也可識別使用者已通過驗證的 Azure AD 目錄。 您的應用程式應該驗證發行者宣告，以確保權杖來自 v2.0 結束點。 |
| 在發行 | `iat` | `1438535543` | 此宣告是用以權杖發出，期間時間表示。 |
| 到期時間 | `exp` | `1438539443` | 宣告是用以權杖會變成無效，時間的到期時間中表示期間時間。 您的應用程式應該使用此宣告驗證的權杖存留時間的有效性。  |
| 不之前 | `nbf` | `1438535543` | 此宣告是有效、 期間時間表示權杖變成的時間。 這通常是的發出權杖相同。 您的應用程式應該使用此宣告驗證的權杖存留時間的有效性。  |
| 版本 | `ver` | `1.0` | Azure AD 所定義，這是識別碼權杖的版本。 |
| 程式碼雜湊 | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | 程式碼雜湊併入識別碼權杖權杖會發出與 OAuth 2.0 授權程式碼時，才。 程式碼雜湊可以用於驗證授權碼的授權。 如何執行此驗證，請參閱[OpenID 連線規格](http://openid.net/specs/openid-connect-core-1_0.html)如需詳細資訊。 |
| 存取權杖雜湊 | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | 存取權杖雜湊併入識別碼權杖權杖會發出與 OAuth 2.0 存取權杖時，才。 存取權杖雜湊可以用於驗證的存取權杖授權。 如何執行此驗證，請參閱[OpenID 連線規格](http://openid.net/specs/openid-connect-core-1_0.html)如需詳細資訊。 |
| Nonce | `nonce` | `12345` | Nonce 是用來降低 token 重新執行攻擊的策略。 您的應用程式可以使用中的授權要求中指定 nonce`nonce`查詢參數。 在邀請中所提供的值會發出中修改`nonce`的識別碼權杖只宣告。 這個選項可讓您的應用程式，以驗證根據其指定在邀請中，將應用程式的工作階段與指定的識別碼權杖相關聯的值的值。 您的應用程式應該執行識別碼 token 驗證程序期間此驗證。 |
| 主旨 | `sub` | `Not supported currently. Use oid claim.` | 這是一個主體的權杖判斷提示資訊，例如使用者的應用程式。 此值是不變，無法重新指派或重複使用。 可以用於安全地，例如當權杖會用來存取資源執行授權檢查。 不過，主旨宣告尚未實作 Azure AD B2C 中。 您應設定您要包含的物件識別碼的原則`oid`宣告及使用其值來找出使用者，而不是使用授權的主旨宣告。 |
| 驗證內容類別參考 | `acr` | `b2c_1_sign_in` | 這是用來取得識別碼權杖原則的名稱。  |
| 驗證時間 | `auth_time` | `1438535543` | 此宣告是使用者最後一個輸入的認證，表示期間時間的時間。 |


### <a name="refresh-tokens"></a>重新整理權杖

重新整理權杖是您的應用程式可用來取得新的識別碼權杖和存取權杖 OAuth 2.0 流程中的安全性權杖。 提供代表使用者長期存取資源的應用程式，而不需要這些使用者的互動。

若要接收重新整理 token token 的回應，您的應用程式必須要求`offline_acesss`範圍。 若要深入瞭解`offline_access`範圍，請參閱[Azure AD B2C 通訊協定參照](active-directory-b2c-reference-protocols.md)。

重新整理權杖，以及一律為，完全不透明到您的應用程式。 他們所發行的 Azure AD 和可以檢查並僅由 Azure AD 解譯。 他們的年齡久，但您的應用程式不應該寫入的重新整理權杖會最後特定的一段時間，期望。 重新整理權杖可失效，隨時各種不同的原因。 您知道的重新整理權杖是不是有效的應用程式的唯一方式是嘗試兌換其藉由 Azure AD 權杖的要求。

當您兌換新的權杖重新整理權杖 (如果已授與您的應用程式`offline_access`範圍)，您會收到新的重新整理權杖 token 回應。 您應該儲存新發行的重新整理權杖。 它應該取代您先前使用邀請中的重新整理 token。 這可協助確保您重新整理權杖維持有效盡可能久。

## <a name="token-validation"></a>權杖驗證

若要驗證的權杖，您的應用程式應該核取 [簽名] 和 [宣告的權杖。

多個開啟的來源文件庫可供驗證 JWTs，根據您慣用的語言。 我們建議您瀏覽這些選項，而非實作驗證邏輯。 本指南中的資訊可協助您瞭解如何適當地使用這些文件庫。

### <a name="validate-the-signature"></a>驗證簽章
JWT 包含三個區段，以分隔`.`字元。 第一個區段**頁首**、 第二個是**本文**，而第三個會**簽章**。 簽章區段可以用於驗證的權杖真偽，使受您的應用程式。

Azure AD B2C 權杖為使用業界標準的非對稱式加密演算法，例如 RSA 256 登入。 頁首的權杖包含用來登入權杖的索引鍵和加密方法的相關資訊︰

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

`alg`宣告指出用來登入權杖演算法。 `kid`宣告指示的特定公開金鑰用來登入權杖。

在任何時候 Azure AD 可能會使用下列其中一組特定公開私密金鑰組簽署權杖。 Azure AD 定期旋轉的金鑰可能的設定，讓您的應用程式應該撰寫自動處理這些的主要變更。 比較合理的頻率，以檢查有更新公開金鑰使用 Azure AD 是每 24 小時。

Azure AD B2C 有 OpenID 連線中繼資料端點。 這個選項可讓應用程式]，擷取 Azure AD B2C 的階段資訊。 這項資訊包括端點、 權杖內容，以及權杖簽署金鑰。 您 B2C 目錄中包含 JSON 中繼資料文件的每個原則。 例如，在中繼資料的文件`b2c_1_sign_in`原則`fabrikamb2c.onmicrosoft.com`位於︰

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com`用來驗證使用者，B2C 目錄及`b2c_1_sign_in`是用來擷取權杖的原則。 若要判斷哪些原則用來登入權杖 （並移至 [擷取中繼資料的位置），您有兩個選項。 首先，請在原則名稱所包含的`acr`宣告權杖中。 您可以 64 基本解碼本文和還原序列化結果 JSON 字串來剖析登出 JWT 本文宣告。 `acr`宣告會用來發出的權杖原則的名稱。  將另一個選項是編碼中的值的原則`state`參數要求，然後再將它來決定使用哪個原則解碼。 任何一種方法是有效的。

中繼資料文件是資訊的 JSON 物件包含多個項有用。 其中包含執行 OpenID 連線驗證所需的結束點的位置。 同時包含`jwks_uri`，為提供的一組公開金鑰的位置，會用來登入權杖。 這裡所提供的位置，但最好使用中繼資料文件，剖析出動態擷取位置`jwks_uri`:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

JSON 文件位於此 URL 會包含所有在特定時間使用公開金鑰資訊。 您的應用程式可以使用`kid`宣告，用來登入的特定的權杖 JSON 文件中選取公開金鑰 JWT 標頭中。 然後可以使用正確的公開金鑰和指定的演算法執行驗證簽章。

如何執行驗證簽章的描述是這份文件的範圍。 多個開啟的來源文件庫，可協助您進行此，如果您需要。

### <a name="validate-the-claims"></a>驗證宣告
當您的應用程式或 API 收到 ID 權杖時，它還應該執行多個檢查針對宣告識別碼權杖中。 這些包含，但並不會限制︰

- **對象**宣告︰ 這個確認識別碼權杖要提供給您的應用程式。
- **不之前**與**期限**宣告︰ 這些驗證的識別碼 token 尚未過期。
- **發行者**宣告︰ 這會驗證的權杖由 Azure AD 發出至您的應用程式。
- **Nonce**︰ 這是 token 重新執行攻擊降低策略。

驗證您的應用程式應該執行的完整清單，請參閱[OpenID 連線規格](https://openid.net)。 詳細資料，這些宣告的預期值都包含前一分[token] 區段](#types-of-tokens)中。  

## <a name="token-lifetimes"></a>權杖存留時間

若要進一步瞭解提供下列 token 存留時間。 當您開發和偵錯應用程式時，他們可以協助您。 請注意，您的應用程式應該寫入預期任何這些存留時間保持不變。 他們可以，並會變更。  您可以閱讀更多關於自訂的權杖存留時間中 Azure AD B2C[以下](active-directory-b2c-token-session-sso.md)。

| 權杖 | 為單位的週期 | 描述 |
| ----------------------- | ------------------------------- | ------------ |
| 識別碼權杖 | 一小時 | 識別碼權杖是通常有效小時。 Web 應用程式可以使用此存留時間 （建議使用） 的使用者，維護自己的工作階段。 您也可以選擇不同的工作階段存留時間。 如果您的應用程式需要取得新的識別碼權杖，它只需要進行 Azure AD 新的登入要求。 如果使用者有 Azure AD 有效的瀏覽器工作階段，該使用者可能不需要再次輸入認證。 |
| 重新整理權杖 | 最多 14 天 | 單一重新整理權杖是有效的 14 天的最大值。 不過，重新整理權杖可能會變成無效隨時的任何數字的原因。 您的應用程式應該繼續嘗試使用的重新整理權杖，直到要求失敗，或您的應用程式的重新整理 token 取代為新的項目。  重新整理權杖也可以會變成無效如果 90 天已經過了，因為使用者最後一個輸入認證。 |
| 授權碼 | 5 分鐘 | 授權代碼是刻意短暫。 他們應該贖回立即存取權杖、 ID 權杖，或重新整理權杖已接收。 |
