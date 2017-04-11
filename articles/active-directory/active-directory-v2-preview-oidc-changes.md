<properties
    pageTitle="Azure AD v2.0 端點變成 |Microsoft Azure"
    description="應用程式模型 v2.0 公用預覽通訊協定所進行的變更的描述。"
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

# <a name="important-updates-to-the-v20-authentication-protocols"></a>V2.0 驗證通訊協定的重要更新
注意開發人員 ！ 在接下來兩週，我們將進行幾個更新重大變更您撰寫我們預覽期間任何應用程式可能表示我們 v2.0 驗證通訊協定。  

## <a name="who-does-this-affect"></a>這是誰影響？
若要使用 v2.0 撰寫任何應用程式匯集驗證端點

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
```

您可以找到 v2.0 端點的詳細資訊[以下](active-directory-appmodel-v2-overview.md)。

如果您已經建立使用 v2.0 端點編碼直接對 v2.0 通訊協定應用程式，使用任何的我們 OpenID 連線或 OAuth web middlewares 或使用其他第 3 派對文件庫執行驗證]，您還需要準備來測試您的專案，並進行必要的變更。

## <a name="who-doesnt-this-affect"></a>這不會影響誰？
撰寫生產 Azure AD 驗證端點，針對任何應用程式

```
https://login.microsoftonline.com/common/oauth2/authorize
```

這個通訊協定顆中並不會發生任何變更。

此外，如果您應用程式**只能**使用我們 ADAL 的文件庫執行驗證，您無法進行任何變更。  ADAL 具有不但您的應用程式所做的變更。  

## <a name="what-are-the-changes"></a>有哪些變更？
### <a name="removing-the-x5t-value-from-jwt-headers"></a>移除 JWT 標題 x5t 值
V2.0 端點使用 JWT 權杖全面，其中包含頁首參數區段與有關權杖相關的中繼資料。  如果解碼的其中一個我們目前 JWTs 標題時，您會發現類似︰

```
{ 
    "type": "JWT",
    "alg": "RS256",
    "x5t": "MnC_VZcATfM5pOYiJHMba9goEKY",
    "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

位置 」 x5t 」 和 「 孩子 」 兩個屬性識別公開金鑰應該用來擷取從 OpenID 連線中繼資料端點驗證權杖的簽名]。

我們會在此處進行的變更是 「 x5t] 屬性中移除。  您可以繼續使用相同的機制驗證權杖，但僅要擷取的正確公開金鑰，指定 OpenID 連線通訊協定的 「 孩子 」 屬性應該使用。 

> [AZURE.IMPORTANT] **您的工作︰ 請確定您的應用程式不會根據 x5t 值是否存在。**

### <a name="removing-profileinfo"></a>移除 profile_info
先前，v2.0 端點具有已傳回 base64 編碼 JSON 物件中稱為 token 回應`profile_info`。  當從 v2.0 端點要求存取權杖，藉由傳送的要求︰

```
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

回應看起來下列 JSON 物件︰
```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

`profile_info`包含值的使用者登入應用程式為其顯示名稱、 名字、 姓氏、 電子郵件地址、 識別碼] 等資訊。  主要`profile_info`所用的權杖快取及顯示之用。

我們正在移除`profile_info`值-，但不要擔心，我們仍然會稍有不同的位置開發人員提供這項資訊。  而不是`profile_info`，現在會傳回 v2.0 端點`id_token`每個 token 回應︰

```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

您可能會解碼和剖析 id_token 擷取您自 profile_info 的相同資訊。  Id_token 是 JSON Web Token (JWT)，以指定的 OpenID 連線的內容。  執行此步驟的程式碼，因此應為非常類似 – 您只需要擷取中間名的 「 」 區段 （本文） id_token，base64 解碼存取內的 JSON 物件。

在接下來兩週，程式碼來擷取的使用者資訊，從 [應用程式`id_token`或`profile_info`;以會出現。  如此一來變更時，您的應用程式流暢地處理從轉換`profile_info`至`id_token`不受干擾。

> [AZURE.IMPORTANT] **您的工作︰ 請確定您的應用程式不會根據是否存在`profile_info`值。**

### <a name="removing-idtokenexpiresin"></a>移除 id_token_expires_in
類似`profile_info`，我們也會移除`id_token_expires_in`參數的回應。  先前，v2.0 端點會傳回的值`id_token_expires_in`以及每個 id_token 回應，例如在授權回應︰

```
https://myapp.com?id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...&id_token_expires_in=3599...
```

或在 token 回應︰

```
{ 
    "token_type": "Bearer",
    "id_token_expires_in": 3599,
    "scope": "openid",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

`id_token_expires_in`值會指出 id_token 仍然有效的秒數。  現在，我們會移除`id_token_expires_in`完全值。  不過，您可能會使用 OpenID 連線的標準`nbf`和`exp`宣告檢查 id_token 的有效性。  請參閱[v2.0 權杖參照](active-directory-v2-tokens.md)如需詳細資訊，這些宣告上。

> [AZURE.IMPORTANT] **您的工作︰ 請確定您的應用程式不會根據是否存在`id_token_expires_in`值。**


### <a name="changing-the-claims-returned-by-scopeopenid"></a>變更所傳回的範圍宣告 = openid
這項變更最重要的 – 事實上，它會影響使用 v2.0 端點幾乎所有應用程式。  許多應用程式傳送邀請給 v2.0 端點使用`openid`想範圍︰

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid offline_access https://outlook.office.com/mail.read
```

今天，當使用者授與的同意`openid`範圍，您的應用程式中產生 id_token 收到豐富的使用者的相關資訊。  他們的名稱、 慣用的使用者名稱、 電子郵件地址、 物件識別碼等，可以包含這些宣告。

在此更新，我們要變更資訊的`openid`範圍可以提供更佳的 comform OpenID 連線規格，您應用程式存取。  `openid`範圍會只允許您的應用程式登入]，使用者和接收使用者在中的特定應用程式的識別項`sub`id_token 的宣告。  只 id_token 中的宣告`openid`授與的範圍會缺乏任何個人資訊。  範例 id_token 宣告為︰

```
{ 
    "aud": "580e250c-8f26-49d0-bee8-1c078add1609",
    "iss": "https://login.microsoftonline.com/b9410318-09af-49c2-b0c3-653adc1f376e/v2.0 ",
    "iat": 1449520283,
    "nbf": 1449520283,
    "exp": 1449524183,
    "nonce": "12345",
    "sub": "MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q",
    "tid": "b9410318-09af-49c2-b0c3-653adc1f376e",
    "ver": "2.0",
}
```

如果您想要取得相關應用程式中的使用者的個人識別資訊 (PII)，您的應用程式必須要求其他權限的使用者。  我們會介紹新的兩個範圍的支援 OpenID 連線規格 –`email`和`profile`範圍-可讓您執行此作業。

- `email`範圍是非常簡單，其可讓您的應用程式存取權的使用者主要的電子郵件地址，透過`email`中 id_token 宣告。  請注意，`email`宣告不一定會出現在 id_tokens – 只會包含該如果有的話，使用者的設定檔中。
- `profile`範圍提供所有其他基本的使用者資訊 – 其名稱、 慣用的使用者名稱，您應用程式存取物件識別碼，依此類推。

這個選項可讓您以最小揭露方式應用程式的程式碼，您可以要求使用者的應用程式，來完成工作所需的資訊集。  如果您想要繼續取得完整的應用程式目前所收到的使用者資訊集，您應該包含所有三個範圍中您的授權要求︰

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid profile email offline_access https://outlook.office.com/mail.read
```

您的應用程式可以開始傳送`email`和`profile`立即範圍及 v2.0 端點會接受下列兩個範圍，並開始視要求使用者的權限。  不過，變更的解譯`openid`範圍才會生效數週。

> [AZURE.IMPORTANT] **您的工作︰ 新增`profile`和`email`範圍如果您的應用程式要求使用者的相關資訊。**  請注意，ADAL 會包含這兩個這些權限在邀請中預設。 

### <a name="removing-the-issuer-trailing-slash"></a>移除結尾斜線的發行者。
先前，會顯示在權杖 v2.0 端點的發行者值原本表單

```
https://login.microsoftonline.com/{some-guid}/v2.0/
```

Guid 所在 Azure AD 租用戶發行權杖 tenantId。  使用這些變更，成為簽發者值

```
https://login.microsoftonline.com/{some-guid}/v2.0 
```

兩個權杖和 OpenID 連線探索文件中。

> [AZURE.IMPORTANT] **您的工作︰ 請確定您的應用程式發行者驗證期間接受及未斜線發行者] 值。**

## <a name="why-change"></a>為什麼變更？
介紹這些變更的主要動機就是與 OpenID 連線標準規格相容。  藉由 OpenID 連線相容，我們希望最小化整合 Microsoft 身分識別服務，以及與業界其他身分識別服務之間的差異。  我們想要讓開發人員不必修改文件庫中使用其最愛的開啟來源驗證文件庫，以容納 Microsoft 的差異。

## <a name="what-can-you-do"></a>您可以做什麼？
到今天，就可以開始進行所有上述的變更。  您應該立即︰

1.  **在移除任何相依性`x5t`頁首參數。**
2.  **妥善處理從轉換`profile_info`至`id_token`token 回覆中。**
3.  **在移除任何相依性`id_token_expires_in`回應參數。**
3.  **新增`profile`和`email`範圍至您的應用程式，如果您的應用程式需要基本的使用者資訊。**
4.  **接受簽發者權杖及未斜線中的值。**

問題的[v2.0 通訊協定文件](active-directory-v2-protocols.md)已更新以反映這些變更，因此您可能會將其作為參照能夠協助更新您的程式碼。

如果您的變更範圍有任何進一步的問題，請不吝連絡我們在 Twitter 上@AzureAD。

## <a name="how-often-will-protocol-changes-occur"></a>將通訊協定修訂的出現頻率？
我們預見任何進一步中斷變更為驗證通訊協定。  我們會刻意搭售這些變更成一個版本，這樣，您不需要進行此類型的更新程序一次隨時推出。  當然，我們將會繼續將功能新增至您可以利用，交集的 v2.0 驗證服務，但是這些變更都應該附加與符號現有的程式碼。

最後，我們想要說出感謝您的預覽期間試用項目。  獲得深入見解和我們早期使用者的體驗已準確到目前為止，，我們希望您就會繼續共用您的意見和想法。

祝您 ！

Microsoft 身分識別除法
