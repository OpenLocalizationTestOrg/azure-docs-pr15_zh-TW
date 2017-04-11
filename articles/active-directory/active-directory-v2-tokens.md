<properties
    pageTitle="Azure AD v2.0 權杖參照 |Microsoft Azure"
    description="權杖及宣告 v2.0 端點所發出的類型"
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

# <a name="v20-token-reference"></a>v2.0 權杖參照

V2.0 端點會發出安全性權杖的各項[驗證流程](active-directory-v2-flows.md)處理中的數種。 這份文件將說明格式、 安全性特性，以及每種類型的權杖的內容。

> [AZURE.NOTE]
    並非所有的 Azure Active Directory 案例與功能支援 v2.0 端點。  若要判斷是否您應該使用 v2.0 端點，請閱讀有關[v2.0 限制](active-directory-v2-limitations.md)。

## <a name="types-of-tokens"></a>權杖的類型

V2.0 端點支援[OAuth 2.0 授權通訊協定](active-directory-v2-protocols.md)，使用 access_tokens 和 refresh_tokens。  它也支援的驗證及登入透過[OpenID 連線](active-directory-v2-protocols.md#openid-connect-sign-in-flow)，會介紹第三個類型的權杖，id_token。  每個這些權杖表示為 「 承載者權杖 」。

承載者權杖是輕量型安全性權杖受保護的資源 」 承載者] 存取。 因此，「 承載者 」 是可以呈現權杖任何方。 如果所需的步驟不會移至安全的 token 傳輸和儲存空間，必須先派驗證與 Azure AD 接收承載者權杖，雖然是攔截及非預期的廠商所使用。 雖然某些安全性權杖有使用時，防止未經授權的合作對象的內建機制，承載者權杖沒有這個機制，且必須安全通道，例如傳輸層安全性 (HTTPS) 中傳輸。 如果承載者權杖會以清除傳輸，男人增益集攻擊可讓惡意派取得權杖，並用於未經授權存取受保護的資源。 儲存或快取承載者權杖，以供日後使用時，就會套用相同的安全性原則。 確保您的應用程式來傳送，並將承載者權杖儲存安全的方式。 更多承載者權杖上的安全性考量，請參閱[RFC 6750 步驟 5](http://tools.ietf.org/html/rfc6750)。

許多由 v2.0 端點的權杖實作 Json Web 權杖，或 JWTs。  JWT 是精簡、 URL 安全的方法雙方之間傳送的資訊。  JWTs 中包含的資訊承載者和主旨的權杖相關即為 「 宣告 」 或判斷提示的資訊。  在 JWTs 宣告是編碼及序列化傳輸的 JSON 物件。  由於發出 v2.0 端點 JWTs 的簽章，但未加密，您可以輕鬆地偵錯檢查 JWT 的內容。 如需有關 JWTs 的詳細資訊，您可以參考[JWT 規格](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)。

## <a name="idtokens"></a>Id_tokens

Id_tokens 是一種執行驗證使用[OpenID 連線](active-directory-v2-protocols.md#openid-connect-sign-in-flow)時，會收到您的應用程式的登入安全性 token。  他們[JWTs](#types-of-tokens)，以表示，包含您可以用來登入您的應用程式的使用者。  您可以在 id_token 使用宣告，如適時-通常用來顯示帳戶資訊，或應用程式中進行存取控制的決策。  V2.0 端點只問題其中一種 id_token，有一組連續的宣告，無論有登入的使用者的類型。  這是要表達的格式和 id_tokens 內容，將為相同的兩個個人的 Microsoft 帳戶使用者和公司或學校帳戶。

登入，但這次不加密 Id_tokens。  當您的應用程式收到 id_token 時，它必須[驗證簽章](#validating-tokens)以證明權杖的真實性和驗證權杖中的幾個宣告證明其有效性。  驗證應用程式的宣告案例需求而有所不同，但有一些[常見的宣告驗證](#validating-tokens)您的應用程式必須在每個案例中執行的。

在中 id_tokens 宣告的完整詳細資料提供，以及範例 id_token。  請注意，在 id_tokens 宣告不會傳回任何特定順序。  此外，新的宣告可以導入 id_tokens 任何時間-您的應用程式應該不中斷會介紹新宣告。  下列清單包含您的應用程式可以可靠解譯此撰寫時的宣告。  如有需要，可以中[OpenID 連線規格](http://openid.net/specs/openid-connect-core-1_0.html)找到更多詳細資料。

#### <a name="sample-idtoken"></a>範例 id_token

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [AZURE.TIP] 練習，請嘗試以將其貼到[calebb.net](https://calebb.net)檢查的範例 id_token 宣告。

#### <a name="claims-in-idtokens"></a>在 [id_tokens 宣告
| 名稱 | 宣告 | 範例值 | 描述 |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| 對象 | `aud` | `6731de76-14a6-49ae-97bc-6eba6914391e` | 識別權杖收件的的者。  在 id_tokens，簡報對象會為指派給您的應用程式，在應用程式註冊入口網站應用程式的應用程式識別碼。  您的應用程式應該驗證此值，並拒絕權杖，如果不相符。 |
| 發行者 | `iss` | `https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` | 識別安全性 token 服務 (STS) 建構並傳回權杖，以及使用者驗證的 Azure AD 租用戶。  您的應用程式應該驗證發行者宣告，以確保權杖來自 v2.0 結束點。  它也應該使用宣告的 guid 部分限制的租用戶允許登入應用程式的設定。  指出使用者 guid 是 microsoft 帳戶是消費者使用者`9188040d-6c67-4c5b-b112-36a304b66dad`。 |
| 在發行 | `iat` | `1452285331` | 用以發出權杖，表示期間時間。 |
| 到期時間 | `exp` | `1452289231` | 時間的權杖會變成無效，表示期間時間。  您的應用程式應該使用此宣告驗證的權杖存留時間的有效性。  |
| 不之前 | `nbf` | `1452285331` |  時間的權杖會變成有效，表示期間時間。 通常是發行時間相同。  您的應用程式應該使用此宣告驗證的權杖為單位的週期有效性。  |
| 版本 | `ver` | `2.0` | Azure AD 所定義 id_token 版本。  值為 v2.0 端點， `2.0`。 |
| 租用戶識別碼 | `tid` | `b9419818-09af-49c2-b0c3-653adc1f376e` | Guid 代表 Azure AD 租用戶也就是使用者。  公司與學校帳戶，guid 就會變的租用戶 ID 的使用者所屬組織。  個人帳戶，會`9188040d-6c67-4c5b-b112-36a304b66dad`。  `profile`範圍必要才會收到此宣告。 |
| 程式碼雜湊 | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | 程式碼雜湊會包含在 id_tokens 中，只有在同時 OAuth 2.0 授權碼發出 id_token 時。  可以用於驗證授權程式碼的授權。  執行這項驗證，請參閱[OpenID 連線規格](http://openid.net/specs/openid-connect-core-1_0.html)如需詳細資訊。 |
| 存取權杖雜湊 | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | 存取權杖雜湊會包含在 id_tokens 中，只有在同時 OAuth 2.0 存取權杖發出 id_token 時。  可以用於驗證的存取權杖授權。  執行這項驗證，請參閱[OpenID 連線規格](http://openid.net/specs/openid-connect-core-1_0.html)如需詳細資訊。 |
| Nonce | `nonce` | `12345` | Nonce 是降低 token 重新執行攻擊的策略。  您的應用程式可以使用中的授權要求中指定 nonce`nonce`查詢參數。  在邀請中所提供的值會發出中 id_token`nonce`宣告，請修改。  這個選項可讓您的應用程式，以驗證根據其指定在邀請中，與指定的 id_token 產生關聯的應用程式工作階段的值的值。  您的應用程式應該執行這項驗證 id_token 驗證程序時。 |
| 名稱 | `name` | `Babe Ruth` | 名稱宣告提供識別的權杖主旨的人力可讀取值。 此值不一定都是唯一的可變動，而設計用來使用僅供顯示用。  `profile`範圍必要才會收到此宣告。 |
| 電子郵件 | `email` | `thegreatbambino@nyy.onmicrosoft.com` | 如果有的話相關聯的使用者帳戶，主要的電子郵件地址。  其值可變動，可能會隨時間變更針對指定的使用者。  `email`範圍必要才會收到此宣告。 |
| 慣用的使用者名稱 | `preferred_username` | `thegreatbambino@nyy.onmicrosoft.com` | 用來代表 v2.0 端點中的使用者主要的使用者名稱。  可能的電子郵件地址、 電話號碼或一般的使用者名稱，不指定的格式。  其值可變動，可能會隨時間變更針對指定的使用者。  `profile`範圍必要才會收到此宣告。 |
| 主旨 | `sub` | `MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | 主要的權杖判斷提示資訊，例如使用者的應用程式。 此值不變，而無法重新指派或重複使用，因此，可以用來安全地，例如當權杖會用來存取資源執行授權檢查。 由於主旨永遠顯示權杖 Azure AD 問題，我們建議您一般用途授權系統中使用此值。 |
| ObjectId | `oid` | `a1dbdde8-e4f9-4571-ad93-3059e3750d23` | Azure AD 系統中的工作或學校帳戶的物件。  此宣告不會發出個人 Microsoft 帳戶。  `profile`範圍必要才會收到此宣告。 |


## <a name="access-tokens"></a>存取權杖

存取權杖發出 v2.0 端點，目前只適用於 Microsoft 服務。  您的應用程式應該不需要執行任何驗證或檢查存取權杖的任何目前支援的案例。  您可以視為存取權杖完全不透明-他們是只是字串的應用程式可以將 HTTP 要求傳遞給 Microsoft。

在不久的將來，v2.0 端點會介紹存取權杖接收其他用戶端應用程式的能力。  此時，這項資訊就會更新您的應用程式必須執行 access token 驗證和其他類似工作的資訊。

當您從 v2.0 端點要求存取權杖時，v2.0 端點也會傳回存取權杖，供您的應用程式使用的部分中繼資料。  這項資訊包含存取權杖，就是有效範圍的到期的時間。  這可讓您執行智慧的快取存取權杖無須解析開啟的應用程式存取權杖本身。

## <a name="refresh-tokens"></a>重新整理權杖

重新整理權杖是您的應用程式可用來取得新的安全性權杖存取權杖 OAuth 2.0 流程中的。  可讓您的應用程式，而不需使用者互動達成長期資源的存取權代表使用者。

重新整理權杖是多的資源。  也就是說，在某個資源的權杖要求期間收到的重新整理權杖可折存取權杖完全不同的資源。

您的應用程式必須要求與授與才會在重新整理收到 token 的回應，`offline_acesss`範圍。   若要深入瞭解`offline_access`範圍，請參閱[以下同意與範圍文章](active-directory-v2-scopes.md)。

重新整理權杖，以及一律為，完全不透明到您的應用程式。  他們 Azure AD v2.0 端點的時間和可以只會檢查與解譯 v2.0 結束點。  他們的年齡久，但您的應用程式應該寫入預期的重新整理權杖會最後的任何一段時間。  重新整理權杖可失效任何時間內的各種不同的原因。  您知道的重新整理權杖是不是有效的應用程式的唯一方式是嘗試兌換其藉由 v2.0 端點的權杖要求。

當您兌換新的存取權杖的重新整理權杖 (如果您的應用程式已獲授予他人`offline_access`範圍)，您會收到新的重新整理權杖 token 回應。  您應該儲存新發行的重新整理權杖，取代在邀請中所使用的項目。  這會保證您重新整理權杖維持有效盡可能久。

## <a name="validating-tokens"></a>驗證的權杖

在目前，只 token 驗證您的應用程式應該需要執行進行驗證 id_tokens。  驗證 id_token，才能 id_token 的簽章和 id_token 中的宣告，應該驗證您的應用程式。

<!-- TODO: Link -->
我們提供文件庫與程式碼範例，顯示如何輕鬆地處理 token 驗證-那些人想要瞭解的基礎程序只會提供資訊] 下方。  還有幾個第 3 廠商開啟來源文件庫可以使用 JWT 驗證-幾乎所有的平台和語言有至少有一個選項。

#### <a name="validating-the-signature"></a>驗證簽章
JWT 包含三個區段，並以分隔`.`字元。  第一個區段稱為**頁首**，**本文**中，為第二個和第三個**簽章**。  簽章區段可用來驗證 id_token 的真偽，使受您的應用程式。

Id_Tokens 為使用業界標準的非對稱加密演算法，例如 RSA 256 登入。 Id_token 的標頭包含用來登入權杖的索引鍵和加密方法的相關資訊︰

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

`alg`宣告指出用來登入權杖，時演算法`kid`宣告指示的特定公開金鑰用來登入權杖。

在任何指定時間點，v2.0 端點可能登入使用下列其中一組特定公開私密金鑰組 id_token。  V2.0 端點旋轉的金鑰定期，可能的設定，讓您的應用程式應該撰寫自動處理這些的主要變更。  比較合理的頻率，以檢查有更新 v2.0 端點所使用的公用索引鍵是關於 24 小時的時間。

您可以取得所需使用 OpenID 連線中繼資料文件位於驗證簽章的簽章索引鍵資料︰

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [AZURE.TIP] 請嘗試在瀏覽器中的此 URL ！

此中繼資料文件是資訊的 JSON 物件包含多個項有用，例如執行 OpenID 連線驗證所需的各種結束點的位置。  

也包含`jwks_uri`，其中提供的一組公開金鑰用來登入權杖的位置。  JSON 文件位於`jwks_uri`包含所有的特定時間內使用公用的重要資訊。  您的應用程式可以使用`kid`宣告 JWT 標頭，選取 [這份文件中的哪些公開金鑰已用來登入的特定的權杖中。  然後，可以執行使用正確的公開金鑰及指定的演算法驗證簽章。

執行驗證簽章外這份文件的範圍-有可供有助您取得執行此作業必要的許多開啟來源文件庫。

#### <a name="validating-the-claims"></a>驗證宣告
當您的應用程式收到 id_token 在使用者登入時，則它也應該執行針對宣告的一些檢查 id_token 中。  包括但不是限於︰

- **對象**宣告式驗證 id_token 要提供給您的應用程式。
- **不之前**與**期限**宣告式驗證 id_token 尚未過期。
- **發行者**宣告式驗證的權杖確實發行至您的應用程式，v2.0 結束點。
- **Nonce** -為 token 重新執行攻擊降低。
- 以及其他功能...

宣告驗證您的應用程式應該執行的完整清單，請參閱[OpenID 連線規格](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation)。

[Id_token] 區段](#id_tokens)中，這些宣告預期的值的詳細資料會包含上方。


## <a name="token-lifetimes"></a>權杖存留時間

下列 token 存留時間會顯示一些只提供您的瞭解程度，他們可以協助開發及偵錯應用程式。  您的應用程式應該寫入預期任何這些保持不變的存留時間-他們與時間會變更任何指定的點。

| 權杖 | 為單位的週期 | 描述 |
| ----------------------- | ------------------------------- | ------------ |
| Id_Tokens （公司或學校帳戶） | 1 小時 | Id_Tokens 是通常有效小時。  您的 web 應用程式可以使用此相同的存留時間維護自己的工作階段與使用者 （建議使用），或選擇完全不同的工作階段存留時間。  如果您的應用程式需要取得新的 id_token，它只需要進行的新登入要求 v2.0 授權端點。  如果使用者擁有有效的瀏覽器工作階段與 v2.0 端點，它們可能不需要再次輸入他們的認證。 |
| Id_Tokens （個人帳戶） | 24 小時的時間 | 24 小時，Id_Tokens 個人帳戶是通常是有效的。  您的 web 應用程式可以使用此相同的存留時間維護自己的工作階段與使用者 （建議使用），或選擇完全不同的工作階段存留時間。  如果您的應用程式需要取得新的 id_token，它只需要進行的新登入要求 v2.0 授權端點。  如果使用者擁有有效的瀏覽器工作階段與 v2.0 端點，它們可能不需要再次輸入他們的認證。 |
| 存取權杖 （公司或學校帳戶） | 1 小時 | 權杖回覆中顯示為 token 中繼資料的一部分。 |
| 存取權杖 （個人帳戶） | 1 小時 | 權杖回覆中顯示為 token 中繼資料的一部分。  Access_tokens 發出代表個人帳戶設定的不同的存留時間，但一小時通常是大小寫。 |
| 重新整理權杖 （公司或學校帳戶） | 最多 14 天 | 單一重新整理權杖是有效的 14 天的最大值。  不過，重新整理權杖可能會變成無效的任何數字的原因，隨時都讓您的應用程式應該繼續嘗試使用的重新整理權杖，直到失敗，或您的應用程式會將其取代新的重新整理權杖。  重新整理權杖也會變成無效，如果使用者輸入其認證，因為已經過 90 天。 |
| 重新整理權杖 （個人帳戶） | 最多為 1 年 | 單一重新整理權杖是有效的 1 年的最大值。  不過，重新整理權杖可能會變成無效的任何數字的原因，隨時都讓您的應用程式應該繼續嘗試使用的重新整理權杖，直到它會失敗。 |
| 授權代碼 （公司或學校帳戶） | 10 分鐘的時間 | 授權代碼故意短暫，而且應該 access_tokens 和 refresh_tokens 立即贖回，已接收。 |
| 授權碼 （個人帳戶） | 5 分鐘 | 授權代碼故意短暫，而且應該 access_tokens 和 refresh_tokens 立即贖回，已接收。  授權碼發出代表個人帳戶，也會使用一次。 |
