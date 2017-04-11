<properties
    pageTitle="呼叫 [邏輯應用程式中的 [自訂的 API"
    description="使用您自訂的 API 裝載於邏輯應用程式的應用程式服務"
    authors="stepsic-microsoft-com"
    manager="dwrede"
    editor=""
    services="logic-apps"
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="stepsic"/>

# <a name="using-your-custom-api-hosted-on-app-service-with-logic-apps"></a>使用您自訂的 API 裝載於邏輯應用程式的應用程式服務

雖然邏輯應用程式具有豐富的 40 + 連接器的各種不同的服務，您可能會想要撥入自己自訂的 API，可以執行程式碼。 其中一個最簡單的方法和最擴充主控自己的自訂網路 API 的方法是使用應用程式服務。 本文說明如何撥入任何 web API 裝載於應用程式服務 API 應用程式、 web app 或行動應用程式。

建置 Api 以觸發程序或邏輯應用程式中的動作的詳細資訊，請參閱[本文](app-service-logic-create-api-app.md)。

## <a name="deploy-your-web-app"></a>部署 Web 應用程式

首先，必須將您的 API 部署為應用程式服務中的 Web 應用程式。 以下指示封面基本部署︰[建立 ASP.NET web 應用程式](../app-service-web/web-sites-dotnet-get-started.md)。  時從邏輯應用程式，您可以撥入任何 API，為最佳體驗，我們建議您新增的邏輯應用程式動作，輕鬆地整合 Swagger 中繼資料。  您可以新增[swagger](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui)尋找詳細資料。

### <a name="api-settings"></a>API 設定

剖析您 Swagger 邏輯應用程式的設計工具的順序，請務必您啟用 CORS，並設定您的 web 應用程式的 APIDefinition 屬性。  這是很容易 Azure 入口網站中設定。  只要開啟 Web 應用程式，設定刀和 api > 一節底下設定 'API 」 url 的定義 swagger.json 檔案 （通常是 https://{name}.azurewebsites.net/swagger/docs/v1），並新增 CORS 原則' *' 允許來自邏輯應用程式設計工具的要求。

## <a name="calling-into-the-api"></a>呼叫 API

在邏輯應用程式入口網站中，如果您已設定 CORS 和 API 定義屬性您應該可以輕鬆地新增自訂 API 您流程中的動作。  在設計工具中，您可以選取瀏覽您的訂閱網站定義 swagger url 清單的網站。  您也可以使用 HTTP + Swagger 指向 swagger，並列出可用的動作及輸入所要的動作。  最後，您隨時都可以建立使用 HTTP 動作撥打任何 API，即使沒有或不會揭露 swagger 文件的要求。

如果您想要保護您的 API，然後有幾種不同的方式執行這項作業︰

1. 程式碼變更不需要的 Azure Active Directory 可用來保護您的 API，而不需要任何程式碼變更或重新部署。
1. 強制執行基本驗證、 AAD 驗證或憑證驗證，在您的 API 的程式碼。

## <a name="securing-calls-to-your-api-without-a-code-change"></a>安全的來電至您的 API，而不變更程式碼

在此區段中，您會建立兩個 Azure Active Directory 應用程式 – 一個邏輯應用程式，一個用於您的 Web 應用程式。  您要驗證的來電至您的 Web 應用程式，使用服務的主要 （用戶端識別碼與密碼） 邏輯應用程式的 AAD 應用程式相關聯。 最後，您會包含該應用程式在您的邏輯應用程式定義 ID。

### <a name="part-1-setting-up-an-application-identity-for-your-logic-app"></a>第 1 部分︰ 設定應用程式識別邏輯應用程式

這是邏輯應用程式使用 active directory 進行驗證。 您只*需要*執行一次為您的目錄。 例如，您可以選擇針對所有邏輯相關應用程式，都使用相同的身分識別雖然您也可以建立唯一識別每邏輯應用程式，如果您想。 您可以在使用者介面中這麼做，或使用 PowerShell。

#### <a name="create-the-application-identity-using-the-azure-classic-portal"></a>建立使用 Azure 傳統入口網站應用程式識別碼

1. 瀏覽到[Active directory Azure 傳統入口網站中的](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)，然後選取您要用於您的 Web 應用程式目錄
2. 按一下 [**應用程式**] 索引標籤
3. 按一下 [**新增**]，請在頁面底部的命令列
4. 授與您的身分識別的名稱，請按一下下一步的箭號
5. 將格式設定的網域中有兩個文字] 方塊中，為唯一字串中，按一下 [核取記號
6. 按一下 [這個應用程式的 [**設定**] 索引標籤
7. 複製**用戶端識別碼**，這將會用於邏輯應用程式中
8. [**索引鍵**] 區段中按一下 [**選取期間**]，然後選擇 [1 年或 2 的年數
9. 按一下 [（您可能需要等候數秒）] 畫面底部的 [**儲存**] 按鈕
10. 現在請務必在] 方塊中複製的金鑰。 這也可用於邏輯應用程式

#### <a name="create-the-application-identity-using-powershell"></a>建立使用 PowerShell 應用程式識別碼
1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. 請務必複製**租用戶識別碼**、**應用程式識別碼**與您所使用的密碼

### <a name="part-2-protect-your-web-app-with-an-aad-app-identity"></a>第 2 部分︰ 保護您的 Web 應用程式與 AAD 應用程式身分識別

如果您的 Web 應用程式已部署您可以在入口網站，只要啟動它。 否則，您可以在其中進行啟用授權 Azure 資源管理員部署的一部分。

#### <a name="enable-authorization-in-the-azure-portal"></a>啟用 Azure 入口網站中的授權

1. 瀏覽至 Web 應用程式，然後按一下 [命令] 列中的**設定**]。
2. 按一下**授權/驗證**]。
3. 將其**開啟**。

此時，您會自動建立應用程式。 因此您需要您需要的第 3 部分此應用程式的用戶端識別碼︰

1. 移至 [ [Active directory 中 Azure 傳統入口網站](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)，然後選取您的目錄。
2. 搜尋 [搜尋] 方塊中的應用程式
3. 按一下清單中
4. 按一下 [**設定**] 索引標籤上的 [
5. 您應該會看到**的用戶端識別碼**

#### <a name="deploying-your-web-app-using-an-arm-template"></a>部署 Web 應用程式使用 ARM 範本

首先，您必須建立 Web 應用程式的應用程式。 應該是不同的應用程式，用於邏輯應用程式。 開始追蹤上述步驟中第 1 部分，但現在**首頁**和**IdentifierUris**實際 https://**URL** Web 應用程式。

>[AZURE.NOTE]當您建立 Web 應用程式的應用程式時，您必須使用[Azure 傳統入口網站的方法](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)，PowerShell commandlet 未設定必要的權限登入網站的使用者。

一次，您可以用戶端識別碼] 及 [的識別碼租用戶中部署範本包含下列子資源的 Web 應用程式︰

```
"resources" : [
    {
        "apiVersion" : "2015-08-01",
        "name" : "web",
        "type" : "config",
        "dependsOn" : [
            "[concat('Microsoft.Web/sites/','parameters('webAppName'))]"
        ],
        "properties" : {
            "siteAuthEnabled": true,
            "siteAuthSettings": {
              "clientId": "<<clientID>>",
              "issuer": "https://sts.windows.net/<<tenantID>>/",
            }
        }
    }
]
```

若要執行的部署自動部署的空白的 Web 應用程式和邏輯的應用程式一起使用 AAD，按一下 [動作] 按鈕︰

[![部署至 Azure](./media/app-service-logic-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

完成的範本，請參閱[邏輯應用程式到裝載於應用程式服務，並受 AAD 自訂 API](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json)。


### <a name="part-3-populate-the-authorization-section-in-the-logic-app"></a>第 3 部分︰ 填入 [授權] 區段中的邏輯應用程式

在 [**授權**] 區段的**HTTP**動作︰`{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| 項目 | 描述 |
|---------|-------------|
| 類型 | 驗證類型。 ActiveDirectoryOAuth 驗證的值是 ActiveDirectoryOAuth。 |
| 租用戶 | 用來識別 AD 租用戶租用戶識別碼。 |
| 對象 | 所需。 您連線到該資源。 |
| clientID | Azure AD 應用程式的用戶端識別碼。 |
| 密碼 | 所需。 要求權杖的用戶端的密碼。 |

上述範本中已經有此設定，但如果您直接撰寫邏輯應用程式，您必須包含的完整授權一節。

## <a name="securing-your-api-in-code"></a>保護您的 API 的程式碼

### <a name="certificate-auth"></a>憑證驗證

您可以使用用戶端憑證驗證傳入的邀請到您的 Web 應用程式。 請參閱如何設定您的程式碼的[方式來設定 TLS 相互驗證 Web 應用程式](../app-service-web/app-service-web-configure-tls-mutual-auth.md)。

在 [*授權*] 區段中，您應提供︰ `{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`。

| 項目 | 描述 |
|---------|-------------|
| 類型 | 所需。 驗證類型。SSL 用戶端憑證] 的值必須是 ClientCertificate。 |
| pfx | 所需。 Base64 編碼 PFX 檔案的內容。 |
| 密碼 | 所需。 若要存取 PFX 檔案的密碼。 |

### <a name="basic-auth"></a>基本驗證

您可以使用基本驗證 （例如使用者名稱和密碼），以驗證傳入的邀請。 基本驗證的常見模式，而您可以進行此動作您建立您的應用程式中的任何語言。

在 [*授權*] 區段中，您應提供︰ `{"type": "basic","username": "test","password": "test"}`。

| 項目 | 描述 |
|---------|-------------|
| 類型 | 所需。 驗證類型。 基本驗證的值必須是基本。 |
| 使用者名稱 | 所需。 若要驗證的使用者名稱。 |
| 密碼 | 所需。 要驗證的密碼。 |

### <a name="handle-aad-auth-in-code"></a>在 [程式碼的控點 AAD 驗證

根據預設，您在入口網站中啟用的 Azure Active Directory 驗證不細微授權。 例如，它不鎖定您的 API 特定使用者或應用程式，但只以特定的租用戶。

如果您想要限制只邏輯應用程式，例如 API 程式碼中，您就可以擷取包含 JWT 和查看來電，拒絕不符合任何要求的標題。

往，如果您想要實作整個保存在自己的程式碼，並不會利用入口網站功能，您可以閱讀這篇文章︰[使用 Active Directory 中 Azure 應用程式服務進行驗證](../app-service-web/web-sites-authentication-authorization.md)。

您仍需要遵循上述步驟來建立應用程式識別邏輯應用程式，並使用該呼叫 API。
