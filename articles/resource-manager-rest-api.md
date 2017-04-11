<properties
   pageTitle="資源管理員 REST Api |Microsoft Azure"
   description="資源管理員 REST Api 驗證及使用方式範例的概觀"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="navale;tomfitz;"/>
   
# <a name="resource-manager-rest-apis"></a>資源管理員 REST Api

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)
- [入口網站](./azure-portal/resource-group-portal.md) 
- [REST API](resource-manager-rest-api.md)

前每個已部署的範本，每個呼叫至 Azure 資源管理員中，設定的儲存空間的每一個帳戶後有一或多個的來電至 Azure 資源管理員 RESTful API。 本主題即專注 Api 及如何您可以撥打電話給不在使用任何 SDK。 這可能是非常實用，如果您想要的所有 Azure 或要求如果 SDK 慣用的語言無法使用或不支援作業您想要執行的 「 完全控制。

本文將不會瀏覽公開 Azure 中每個 API，但會而使用一些作為範例如何繼續操作並進行連線。 如果您瞭解基本概念，可以再繼續進行並閱讀的[Azure 資源管理員 REST API 參考](https://msdn.microsoft.com/library/azure/dn790568.aspx)，找出如何使用 Api 的其餘部分的詳細的資訊。

## <a name="authentication"></a>驗證
驗證 ARM 被處理的 Azure Active Directory (AD)。 以連線至任何 API，您必須先收到的驗證的權杖 Azure AD 驗證方法，您可以傳遞給每個要求。 我們會說明 REST Api 直接在純粹通話，因為我們也會假設您不想要使用標準的使用者名稱密碼位置 pop-up 螢幕可能會提示您輸入使用者名稱和密碼並可能在兩個因素驗證案例中使用其他驗證機制驗證。 因此，我們將會建立所謂 Azure AD 應用程式與服務主體將用來登入。 但是請記住，Azure AD 支援幾個驗證程序，全部都無法用來擷取該驗證權杖，我們需要後續的 API 邀請。
如需逐步指示，請遵循[建立 Azure AD 應用程式與服務原則](./resource-group-create-service-principal-portal.md)。

### <a name="generating-an-access-token"></a>產生存取權杖 
驗證的 Azure AD 是由撥 Azure AD，位於 login.microsoftonline.com。 若要驗證您必須具備下列資訊︰

* Azure AD 租用戶識別碼 （您用來登入，通常相同您的公司，但不是必要的 Azure AD 名稱）
* 應用程式識別碼 （Azure AD 應用程式建立步驟期間所）
* （您所選取建立 Azure AD 應用程式時） 的密碼

在下方 HTTP 要求請務必以正確的值取代 「 Azure AD 租用戶識別碼 」、 「 應用程式識別碼 」 和 「 密碼 」。

**一般 HTTP 要求︰**

```HTTP
POST /<Azure AD Tenant ID>/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

...（如果驗證成功） 會產生類似以下的回應︰

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
（上述回應 access_token 有縮短過了以提高可讀性）

**使用艦隊產生存取權杖︰**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**使用 PowerShell 產生存取權杖︰**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

回應包含存取權杖、 多久的權杖是有效的相關資訊以及相關資訊的資源，您可以使用該權杖。
您在上一個 HTTP 通話中收到的存取權杖必須傳遞中所有邀請的對 ARM API 為頁首命名 「 授權 」 與 「 承載者 YOUR_ACCESS_TOKEN 」 的值。 請注意 「 承載者 」 與您存取權杖之間的間距。

您可以看到從上述 HTTP 結果，權杖是有效的特定的一段時間的期間，您應該快取並重複使用的相同的 token。 即使可能為每個 API 呼叫 Azure AD 進行驗證，則會降低高效率。

## <a name="calling-arm-rest-apis"></a>電話 ARM REST Api

[Azure 資源管理員 REST Api 在此處所列](https://msdn.microsoft.com/library/azure/dn790568.aspx)，並將其的不在此教學課程以文件的每個和每個使用之範圍。 這份文件只會用來說明基本使用方式的幾個 Api Api 以及之後，我們會將您的正式的文件。

### <a name="list-all-subscriptions"></a>列出所有的訂閱

最簡單的作業，您可以執行的是清單的可用的訂閱，您可以存取。 在 [要求，您可以查看如何存取權杖會被標題下方。

（取代 YOUR_ACCESS_TOKEN 您實際存取權杖。）

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

...如此一來，您會收到此服務主要可以存取的訂閱清單

（訂閱識別碼下方有縮短過了，提高可讀性）

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### <a name="list-all-resource-groups-in-a-specific-subscription"></a>清單中的特定訂閱的所有資源群組

ARM Api 提供的所有資源的巢都狀嵌入資源群組。 我們將查詢 ARM 現有的資源群組在我們的訂閱使用下方 HTTP GET 要求。 請注意如何訂閱識別碼會被 URL 部份這次。

（取代 YOUR_ACCESS_TOKEN 與 SUBSCRIPTION_ID 與您的實際存取權杖訂閱識別碼）

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

您是否有任何已定義的資源群組取決於您的回應，如果是這樣，多少。

（訂閱識別碼下方有縮短過了，提高可讀性）

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### <a name="create-a-resource-group"></a>建立資源群組

目前我們已只已查詢 ARM Api 的資訊、 正是我們改為建立一些資源，並讓我們開始的最簡單的它們全部，資源群組。 下列 HTTP 要求地區/您所選擇的位置建立新的資源群組，並將一或多個標籤加以便 （樣本下方實際上只會新增一個標記）。

（取代 YOUR_ACCESS_TOKEN、 SUBSCRIPTION_ID RESOURCE_GROUP_NAME 您實際存取權杖訂閱識別碼與您想要建立資源群組的名稱）

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

如果成功，您會看到類似以下的回應

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

您已順利建立 Azure 中的資源群組。 恭喜您 ！

### <a name="deploy-resources-to-a-resource-group-using-an-arm-template"></a>資源群組使用 ARM 範本部署資源

使用 ARM，您可以部署資源使用 ARM 範本。 ARM 範本可定義多個資源和相依性。 本節將只是假設您熟悉 ARM 範本，我們將只顯示您如何啟動的其中一個部署 API 撥號。 可以在這裡找到詳細的文件的手錶範本。

ARM 範本的部署不更不同您撥打其他 Api 的方式。 一個重要的長寬是範本的部署花費很長的時間，什麼是內範本，而定，且就只會傳回 API 通話，以及是由您為查詢部署的狀態的開發人員才能部署完成時，找出。

此範例中，我們要使用上[GitHub](https://github.com/Azure/azure-quickstart-templates)公開的 ARM 範本可用。 我們將使用的範本會將 Linux VM 部署西美國地區。 即使此範本會有可用的範本，例如 GitHub 公用存放庫中，您也可以選取邀請屬於傳遞完整的範本。 請注意，我們會提供參數值的要求，將會用於內使用的範本的一部分。

（取代 SUBSCRIPTION_ID、 RESOURCE_GROUP_NAME、 DEPLOYMENT_NAME、 YOUR_ACCESS_TOKEN、 GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME、 ADMIN_USER_NAME、 ADMIN_PASSWORD 和 DNS_NAME_FOR_PUBLIC_IP 要求適當的值）

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

若要改善此文件的可讀性省略了這個要求很長 JSON 回應。 回應會包含您剛剛建立的範本部署資訊。

