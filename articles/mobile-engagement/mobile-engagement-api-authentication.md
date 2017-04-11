<properties 
    pageTitle="使用行動互動 REST Api 驗證"
    description="說明如何使用 Azure 行動互動 REST Api 驗證" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="10/05/2016"
    ms.author="wesmc;ricksal"/>

# <a name="authenticate-with-mobile-engagement-rest-apis"></a>使用行動互動 REST Api 驗證

## <a name="overview"></a>概觀

這份文件將說明如何取得有效的 AAD Oauth token 行動互動 REST Api 驗證方法。 

假設您擁有有效的 Azure 訂閱，而且您已建立使用其中一個我們[開發人員教學課程](mobile-engagement-windows-store-dotnet-get-started.md)的行動電話的行動應用程式。

## <a name="authentication"></a>驗證

Microsoft Azure Active Directory 依據的 OAuth 權杖用於驗證。 

驗證 API 邀請順序，授權標頭必須新增至每個要求這是下列格式︰

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

>[AZURE.NOTE] Azure Active Directory 權杖過期中 1 小時。

有數種方式取得權杖。 由於來自雲端服務，通常稱為的 Api 您想要使用的 API 金鑰。 Azure 術語中的 API 金鑰稱為服務本金密碼。 下列程序說明手動設定的其中一個方法。

### <a name="one-time-setup-using-script"></a>一次設定 （使用指令碼）

您應該遵循下列指示執行安裝程式使用 PowerShell 指令碼需要針對設定最小的時間，但使用最所允許的預設值的設定。 （選擇性） 您也可以直接從 Azure 入口網站執行[手動設定](mobile-engagement-api-authentication-manual.md)中的指示，請依照下列，並執行更妥善地設定。 

1. 從[這裡](http://aka.ms/webpi-azps)取得 PowerShell 的 Azure 的最新版本。 如需有關下載指示的詳細資訊，您可以看到這個[連結](../powershell-install-configure.md)。  

2. PowerShell 的 Azure 安裝後，請以確定您有安裝**Azure 模組**中使用下列命令︰

    。 請確定 Azure PowerShell 模組是在清單中的 [可用的模組。 
    
        Get-Module –ListAvailable 

    ![可用的 Azure 模組][1]
        
    b。 如果您找不到 Azure PowerShell 模組中您需要執行下列動作︰
        
        Import-Module Azure 
        
3. 從 PowerShell 來執行下列命令和 Azure 帳戶提供您的使用者名稱和密碼登入至 Azure 資源管理員︰ 
        
        Login-AzureRmAccount

4. 如果您有多個訂閱，那麼您應該執行下列動作︰

    。 取得您的訂閱的清單，然後複製您想要使用的訂閱 SubscriptionId。 請確定此訂閱同一個具有您要與使用 Api 互動行動的行動應用程式。 

        Get-AzureRmSubscription

    b。 執行下列命令提供 SubscriptionId 設定使用訂閱。

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>

5. 將文字[新增 AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1)指令碼複製到本機電腦，並將其儲存為 PowerShell 指令程式 (例如`APIAuth.ps1`) 並執行該`.\APIAuth.ps1`。 
    
6. 指令碼會要求您提供**principalName**輸入。 您想要用來建立您的 Active Directory 應用程式 (例如 APIAuth)，提供一個合適的名稱。 

7. 指令碼完成之後，它將會顯示下列您將需要驗證方法以程式設計方式 AD 的四個值，因此請務必複製它們。 
        
    **TenantId**、 **SubscriptionId**、 **ApplicationId**及**密碼**。

    您會使用為 TenantId `{TENANT_ID}`，為 ApplicationId`{CLIENT_ID}`和為私人`{CLIENT_SECRET}`。

    > [AZURE.NOTE] 您預設的安全性原則可能封鎖您執行的 PowerShell 指令碼。 如果是這樣，您會暫時設定您執行原則，以允許執行指令碼使用下列命令︰

        > Set-ExecutionPolicy RemoteSigned

8. 以下是如何 PS cmdlet 一組如下所示。 

    ![][3]

9. 檢查 Azure 管理入口網站中的呼叫**principalName**底下**顯示的應用程式公司所擁有**的指令碼至您所提供的名稱與建立新的 AD 應用程式。

    ![][4]

#### <a name="steps-to-get-a-valid-token"></a>若要取得有效的權杖的步驟

1. 呼叫使用下列參數 API，並確認取代租用戶\_識別碼、 用戶端\_識別碼和用戶端\_密碼︰

    - **要求的 URL**為*https://login.microsoftonline.com/ {租用戶\_識別碼} / oauth2/權杖*
    - 為*應用程式/x-www-表單-urlencoded* **HTTP 內容類型的標頭**
    - 為**HTTP 要求本文***授與\_類型 = 用戶端\_認證與 client_id = {用戶端\_識別碼} & client_secret = {用戶端\_私人} & resource=https%3A%2F%2Fmanagement.core.windows.net%2F*

    以下是範例要求︰

        POST /{TENANT_ID}/oauth2/token HTTP/1.1
        Host: login.microsoftonline.com
        Content-Type: application/x-www-form-urlencoded
        grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
        urce=https%3A%2F%2Fmanagement.core.windows.net%2F

    以下是範例回應︰

        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Content-Length: 1234
    
        {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
        5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}

    此範例中所包含的文章參數，URL 編碼`resource`值實際上是`https://management.core.windows.net/`。 請小心也 URL 編碼`{CLIENT_SECRET}`時，可能包含特殊字元。

    > [AZURE.NOTE] 以進行測試，您可以使用 HTTP 用戶端工具，例如[Fiddler](http://www.telerik.com/fiddler)或[Chrome 郵差副檔名](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) 

2. 現在在每個 API 通話，包括授權要求標頭︰

        Authorization: Bearer {ACCESS_TOKEN}

    如果您收到傳回 401 狀態碼，請核取 [回應內容，它可能會告訴您權杖已過期。 在此情況下，取得新的憑證。

##<a name="using-the-apis"></a>使用 Api

您已經有的有效的權杖，就可以開始進行 API 通話。

1. 在每個 API 邀請中，您必須將取得有效的、 未到期權杖傳遞前一節。

2. 您必須以插入到邀請 URI 用來識別您的應用程式的一些參數。 要求 URI 看起來如下

        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/

    取得參數，按一下您的應用程式名稱，按一下 [儀表板，您會看到類似下列所有 3 個參數的頁面。

    - **1**`{subscription-id}`
    - **2**`{app-collection}`
    - **3**`{app-resource-name}`
    - **4** ，您的資源群組的名稱會**MobileEngagement** ，除非您建立一個新即將。 

    ![行動互動 API URI 參數][2]

>[AZURE.NOTE] <br/>
>1. 忽略這是先前 API 根地址。<br/>
>2. 如果您建立使用傳統 Azure 入口網站應用程式您需要使用不同的應用程式名稱本身的應用程式資源名稱。 如果您在 Azure 入口網站中建立應用程式，您應該使用應用程式的名稱本身 （有應用程式資源名稱] 和 [建立新的入口網站的應用程式的應用程式名稱沒有區別）。  

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/ad-app-creation.png



