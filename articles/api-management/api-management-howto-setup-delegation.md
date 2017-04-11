<properties 
    pageTitle="如何委派使用者註冊與產品的訂閱" 
    description="瞭解如何委派第三方 Azure API 管理中的使用者註冊與產品訂閱。" 
    services="api-management" 
    documentationCenter="" 
    authors="antonba" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="antonba"/>

# <a name="how-to-delegate-user-registration-and-product-subscription"></a>如何委派使用者註冊與產品的訂閱

委派 」 能讓您處理開發人員登-在/sign-up 和訂閱產品，而不是使用內建功能，在開發人員入口網站中使用現有的網站。 這可讓您擁有自己的使用者資料，以及自訂的方式執行的步驟驗證的網站。

## <a name="delegate-signin-up"></a>委派開發人員登入並註冊

若要委派開發人員登入並註冊您現有的網站，您必須做為進入點從 API 管理開發人員入口網站發起的租用戶的任何這類邀請的網站上建立特殊的委派端點。

最後一個工作流程會如下所示︰

1. 開發人員只要按下登入或註冊的連結，在 API 管理開發人員入口網站
2. 在瀏覽器重新導向至委派端點
3. 委派端點回報重新導向，或呈現要求使用者登入或註冊的使用者介面
4. 成功，使用者會重新導向至其從啟動的 API 管理的開發人員入口網站頁面


若要開始，現在就讓我們來路由傳送的第一個設定 API 管理要求透過您的委派結束點。 在 [API 管理 publisher 入口網站中，按一下 [**安全性**，然後按一下 [**委派**] 索引標籤。 按一下 [若要啟用 「 委派登入並註冊] 的核取方塊。

![委派頁面][api-management-delegation-signin-up]

* 決定哪些您特殊委派端點的 URL 會並在 [**委派端點 URL** ] 欄位中輸入。 

* **委派驗證索引鍵**欄位中輸入密碼，會用來計算所提供給您的驗證，以確保要求確實來自 Azure API 管理簽章。 您可以按一下 [**產生**] 按鈕，有 API Managemnet 隨機為您產生金鑰。

現在，您需要建立**委派結束點**。 若要執行的動作數字有︰

1. 收到要求下列格式︰

    > *http://www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl= {的來源] 頁面的 URL} & salt = {字串} & 簽章 = {字串}*

    登入 / 註冊大小寫的查詢參數︰
    - **作業**︰ 識別何種類型的委派要求存取-它只可以在此情況下是**登入**
    - **傳回的 Url**: [使用者登入或註冊連結所按的位置] 頁面的 URL
    - **salt**︰ 用來計算安全性雜湊特殊 salt 字串
    - **簽章**︰ 用於比較到您自己的計算的安全性雜湊計算雜湊

2. 驗證 （選擇性的但建議的安全性） 的邀請來自 Azure API 管理

    * 計算 HMAC SHA512 的雜湊字串**傳回的 Url**及**salt**查詢參數 （[提供的範例程式碼]）︰
        > HMAC （**salt** + '\n' +**傳回的 Url**）
         
    * 比較上方計算雜湊**簽章**查詢參數的值。 如果兩個雜湊相符，請移至下一個步驟，否則拒絕要求。

2. 請確認您收到登輸入/登上的要求︰**作業**查詢參數會設為 「**登入**]。

3. 向使用者呈現登入或註冊的使用者介面

4. 如果使用者登入向上必須建立這些 API 管理中的相對應的帳戶。 以 API 管理 REST API[建立使用者]] 。 當這麼做，請確定您設定的使用者識別碼，則在您的使用者存放區相同或 ID，您可以追蹤。

5. 當順利驗證使用者︰

    * [要求的單一登入 (SSO) 權杖]透過 API 管理 REST API

    * 將傳回的 Url 查詢參數新增至您收到來自 API 通話上方 SSO URL:
        > 例如 https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 

    * 將使用者導向至上述產生的 URL

除了 [**登入**] 作業，您也可以執行帳戶管理遵循上述步驟，使用下列作業。

-   **變更密碼**
-   **ChangeProfile**
-   **CloseAccount**

您必須通過帳戶管理操作的下列查詢參數。

-   **作業**︰ 識別何種類型的委派要求 （變更密碼、 ChangeProfile，或 CloseAccount）
-   **使用者識別碼**︰ 若要管理的帳戶的使用者識別碼
-   **salt**︰ 用來計算安全性雜湊特殊 salt 字串
-   **簽章**︰ 用於比較到您自己的計算的安全性雜湊計算雜湊

## <a name="delegate-product-subscription"></a>委派產品訂閱

委派產品訂閱的運作方式類似委派使用者登入/向上。 最後一個工作流程便如下所示︰

1. 開發人員選擇 API 管理開發人員入口網站中的 [產品，然後按一下 [訂閱] 按鈕
2. 在瀏覽器重新導向至委派端點
3. 委派端點會執行所需的產品訂閱步驟-這是之外，可能會發生重新導向到其他頁面要求其他問題的回答，或直接將資訊儲存並不需要任何使用者動作的計費資訊


若要啟用的功能，按一下 [在 [**委派**頁面上的 [**代理人產品訂閱**]。

然後確定委派端點執行下列動作︰


1. 收到要求下列格式︰

    > *http://www.yourwebsite.com/apimdelegation?operation= {作業} & productId = {產品訂閱} & 使用者識別碼 = {要求使用者} & salt = {字串} & 簽章 = {字串}*

    產品訂閱大小寫的查詢參數︰
    - **作業**︰ 識別這是何種類型的委派要求。 產品的訂閱是要求有效的選項︰
        - [訂閱]: 若要訂閱的使用者，以使用產品的要求提供識別碼 （如下所示）
        - [取消]: 取消訂閱的使用者，產品的要求
        - [更新]: requst 更新的訂閱 （例如可能即將過期）
    - **產品識別碼**︰ 若要訂閱的使用者要求的產品識別碼
    - **使用者識別碼**︰ 要求對象的使用者識別碼
    - **salt**︰ 用來計算安全性雜湊特殊 salt 字串
    - **簽章**︰ 用於比較到您自己的計算的安全性雜湊計算雜湊


2. 驗證 （選擇性的但建議的安全性） 的邀請來自 Azure API 管理

    * 計算根據 [**產品識別碼**、**使用者識別碼**和**salt**查詢參數的字串 HMAC SHA512:
        > HMAC （**salt** + '\n' + **productId** + '\n' +**使用者識別碼**）
         
    * 比較上方計算雜湊**簽章**查詢參數的值。 如果兩個雜湊相符，請移至下一個步驟，否則拒絕要求。
    
3. 執行任何的要求**作業**-例如帳單，進一步的問題等作業類型為根據的產品訂閱處理。

4. 在成功訂閱您位於產品的使用者，請致電[產品訂閱 REST API]訂閱 API 管理產品的使用者。

## <a name="delegate-example-code"></a>程式碼範例 ##

這些程式碼範例顯示*委派驗證鍵*，在 publisher 入口網站的 [委派] 畫面中設定以建立可供您驗證簽章，證明傳遞傳回的 Url 的有效性 HMAC 使用方式。 相同的程式碼適用於產品識別碼和使用者識別碼與稍微修改一下。

**C# 程式碼來產生雜湊傳回的 Url**

    using System.Security.Cryptography;

    string key = "delegation validation key";
    string returnUrl = "returnUrl query parameter";
    string salt = "salt query parameter";
    string signature;
    using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
    {
        signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
        // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
        // compare signature to sig query parameter
    }


**NodeJS 程式碼，以產生雜湊傳回的 Url**

    var crypto = require('crypto');
    
    var key = 'delegation validation key'; 
    var returnUrl = 'returnUrl query parameter';
    var salt = 'salt query parameter';
    
    var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
    var digest = hmac.update(salt + '\n' + returnUrl).digest();
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
    
    var signature = digest.toString('base64');

## <a name="next-steps"></a>後續步驟

如需有關委派的詳細資訊，請參閱以下的影片。

> [AZURE.VIDEO delegating-user-authentication-and-product-subscription-to-a-3rd-party-site]

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[要求的單一登入 (SSO) 權杖]: http://go.microsoft.com/fwlink/?LinkId=507409
[建立使用者]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[產品訂閱呼叫 REST API]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Next steps]: #next-steps
[以下提供程式碼的範例]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 