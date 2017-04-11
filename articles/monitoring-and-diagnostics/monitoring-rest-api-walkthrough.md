<properties
    pageTitle="Azure 監控 REST API 逐步解說 |Microsoft Azure"
    description="如何驗證要求，並使用 Azure 監控 REST API。"
    authors="mcollier, rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="mcollier"/>

# <a name="azure-monitoring-rest-api-walkthrough"></a>Azure 監控 REST API 逐步解說
本文將示範如何執行驗證，因此您的程式碼，可以使用[Microsoft Azure 監視器 REST API 參照](https://msdn.microsoft.com/library/azure/dn931943.aspx)。         

Azure 監視器 API 可以讓您以程式設計方式擷取可用的預設公制定義 （例如 CPU 時間、 要求等公制類型）、 資料粒度和度量值。 擷取之後，可以在個別的資料存放區，例如 Azure SQL 資料庫、 DocumentDB 或 Azure 資料湖儲存資料。 從該處可以視需要執行其他分析。

除了使用各種不同的公制的資料點，如本文所示，監視器 API 可讓清單通知的規則，檢視活動記錄，以及執行其他功能。 可用的作業的完整清單，請參閱[Microsoft Azure 監視器 REST API 參照](https://msdn.microsoft.com/library/azure/dn931943.aspx)。

## <a name="authenticating-azure-monitor-requests"></a>驗證 Azure 監視器要求

第一步是驗證要求。

針對 Azure 監視器 API 執行的所有工作。 都使用 Azure 資源管理員驗證模型。 因此，所有的要求，必須經過驗證與 Azure Active Directory (Azure AD)。 驗證用戶端應用程式的其中一個方法是建立 Azure AD 服務主要擷取驗證 (JWT) 權杖。 下列範例指令碼示範如何建立 Azure AD 服務的本金透過 PowerShell。 如詳細逐步解說，請參閱使用[PowerShell 的 Azure 來建立主要存取資源的服務](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password—powershell)的文件。 您也可建立[透過 Azure 入口網站的服務原則](../resource-group-create-service-principal-portal.md)。

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"
$location = "centralus"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $pwd

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

若要查詢 Azure 監視器 API，用戶端應用程式應該使用先前建立的服務本金驗證。 下列 PowerShell 指令碼範例使用[Active Directory 驗證庫](../active-directory/active-directory-authentication-libraries.md)(ADAL)，以協助 JWT 驗證 token 的一種方法。 Azure 監視器 REST API 來 JWT 權杖傳遞 HTTP 授權參數在邀請中的一部分。

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.windows.net/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)
 
$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values 
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

驗證設定步驟完成後，對 Azure 監視器 REST API 可以再執行查詢。 有兩個很有幫助的查詢︰

1. 清單資源的公制定義

2. 擷取度量值


## <a name="retrieve-metric-definitions"></a>擷取公制定義
>[AZURE.NOTE] 若要擷取公制定義使用 Azure 監視器 REST API，使用 「 2016年-03-01 」 作為 API 版本。

```PowerShell
$apiVersion = "2016-03-01"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metricDefinitions?api-version=${apiVersion}"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -Verbose
```
Azure 邏輯應用程式]，公制定義會出現類似以下的螢幕擷取畫面︰

![Alt 「 JSON 檢視的公制定義回應 」。](./media/monitoring-rest-api-walkthrough/available_metric_definitions_logic_app_json_response_clean.png)

如需詳細資訊，請參閱[] 清單中 Azure 監視器 REST API 的資源單位的定義](https://msdn.microsoft.com/library/azure/mt743621.aspx)文件。

## <a name="retrieve-metric-values"></a>擷取度量值
一旦已知可用公制定義，然後可能是擷取相關的度量值。 使用公制名稱 「 值 」 (不 ' localizedValue 」) 的任何篩選要求 （例如，擷取 'CpuTime 」 和 「 要求' 公制的資料點）。 如果有不指定任何篩選，則會傳回預設度量。

>[AZURE.NOTE] 若要擷取使用 Azure 監視器 REST API 的度量值，使用 「 2016年-06-01 」 作為 API 版本。

**方法**︰ 取得

**要求 URI**: https://management.azure.com/subscriptions/_{訂閱識別碼}_/resourceGroups/_{資源的群組名稱的}_/providers/_{資源提供者-命名空間}_/_{資源類型}_/_{資源名稱}_/providers/microsoft.insights/metrics?$filter=_{篩選}_和 api 版本 =_{apiVersion}_

例如，若要擷取之指定的時間範圍以及 1 小時的時間粒 RunsSucceeded 公制的資料點，要求會，如下所示︰

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2016-09-23 and endTime eq 2016-09-24 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

結果會出現類似下列螢幕擷取畫面範例︰

![Alt 」 顯示平均回應時間公制值 JSON 回應 」](./media/monitoring-rest-api-walkthrough/available_metrics_logic_app_json_response.png)

若要擷取資料或彙總的多個點，加入公制的定義名稱和彙總類型篩選器，如下列範例所示︰

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2016-09-23T13:30:00Z and endTime eq 2016-09-23T14:30:00Z and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

### <a name="use-armclient"></a>使用 ARMClient
使用 PowerShell （如上所示），或者是使用[ARMClient](https://github.com/projectkudu/ARMClient) Windows 電腦上。 ARMClient 自動處理 Azure AD 驗證 （和 JWT 產生的權杖）。 下列步驟外框 ARMClient 的用於擷取公制資料︰

1. 安裝[Chocolatey](https://chocolatey.org/)及[ARMClient](https://github.com/projectkudu/ARMClient)。

2. 在終端機的視窗中，輸入_armclient.exe 登入_。 這會提示您登入 Azure。

3. 輸入_armclient 取得 [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01_

4. 輸入_armclient 取得 [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-06-01_


![Alt 」 與 Azure 監控 REST API 搭配使用 ARMClient 」](./media/monitoring-rest-api-walkthrough/armclient_metricdefinitions.png)


## <a name="retrieve-the-resource-id"></a>擷取資源識別碼
使用 REST API 真的有助於了解可用公制定義資料粒度，與相關的值。 使用[Azure 管理文件庫](https://msdn.microsoft.com/library/azure/mt417623.aspx)時，該資訊有幫助。

要使用的資源識別碼前面的程式碼，是您要的 Azure 資源的完整路徑。 例如，若要查詢 Azure Web 應用程式，就是資源識別碼︰

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

下列清單包含的幾種不同的 Azure 資源的資源識別碼格式範例︰

* **IoT 中心**/subscriptions/_{訂閱識別碼}_/resourceGroups/_{資源的群組名稱的}_/providers/Microsoft.Devices/IotHubs/_{iot 中心-名稱}_

* **彈性的 SQL 資料庫**/subscriptions/_{訂閱識別碼}_/resourceGroups/_{資源的群組名稱的}_/providers/Microsoft.Sql/servers/_{資料庫 db}_/elasticpools/_{sql 資料庫的名稱}_

* **SQL 資料庫 (v12)** /subscriptions/_{訂閱識別碼}_/resourceGroups/_{資源的群組名稱的}_/providers/Microsoft.Sql/servers/_{伺服器名稱}_/databases/_{資料庫名稱}_

* **服務匯流排**/subscriptions/_{訂閱識別碼}_/resourceGroups/_{資源的群組名稱的}_/providers/Microsoft.ServiceBus/_{命名空間}_/_{servicebus 名稱}_

* **VM 縮放比例設定**/subscriptions/_{訂閱識別碼}_/resourceGroups/_{資源的群組名稱的}_/providers/Microsoft.Compute/virtualMachineScaleSets/_{vm 名稱}_

* **Vm** /subscriptions/_{訂閱識別碼}_/resourceGroups/_{資源的群組名稱的}_/providers/Microsoft.Compute/virtualMachines/_{vm 名稱}_

* **事件集線器**/subscriptions/_{訂閱識別碼}_/resourceGroups/_{資源的群組名稱的}_/providers/Microsoft.EventHub/namespaces/_{eventhub 命名空間}_


還有其他方法，以擷取資源識別碼，包括使用 Azure 資源總管] 中，在 Azure 入口網站，並透過 PowerShell 或 Azure CLI 檢視所需的資源。

### <a name="azure-resource-explorer"></a>Azure 資源檔案總管
若要尋找所要的資源的資源識別碼，還是有幫助的其中一個方法是使用 [ [Azure 資源檔案總管](https://resources.azure.com)] 工具。 瀏覽至所要的資源，然後查看所示，如以下的螢幕擷取畫面所示的識別碼︰

![Alt 」 Azure 資源檔案總管 」](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure 入口網站
資源識別碼也可以從 Azure 入口網站取得。 若要這麼做，請瀏覽至所要的資源，，然後選取 [摘要資訊。 資源識別碼會顯示屬性刀，如以下的螢幕擷取畫面所示︰

![替代 「 資源識別碼顯示在 Azure 入口網站屬性刀 」](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell
可以使用 PowerShell 的 Azure 指令程式中擷取資源識別碼。 例如，若要取得資源識別碼 Azure Web 應用程式，執行取得 AzureRmWebApp cmdlet，如以下的螢幕擷取畫面所示︰

![替代 「 資源識別碼透過 PowerShell 取得 」](./media\monitoring-rest-api-walkthrough\resourceid_powershell.png)

### <a name="azure-cli"></a>Azure CLI
若要擷取使用 Azure CLI 資源識別碼，請執行 azure webapp 顯示] 命令，指定 '-json 」 選項，如以下的螢幕擷取畫面所示︰

![替代 「 資源識別碼透過 PowerShell 取得 」](./media\monitoring-rest-api-walkthrough\resourceid_azurecli.png)

## <a name="retrieve-activity-log-data"></a>擷取活動記錄資料
除了使用公制定義與相關的值，也可擷取 Azure 資源相關的其他感興趣深入資訊。 例如，可能是查詢[活動記錄](https://msdn.microsoft.com/library/azure/dn931934.aspx)資料。 下列範例會示範使用 Azure 監視器 REST API，查詢活動記錄資料特定的日期範圍內的 Azure 訂閱︰

```PowerShell
$apiVersion = "2014-04-01"
$filter = "eventTimestamp ge '2016-09-23' and eventTimestamp le '2016-09-24'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

## <a name="next-steps"></a>後續步驟
* 檢閱[的監控概觀](monitoring-overview.md)。
* 檢視[與 Azure 監視器支援指標](monitoring-supported-metrics.md)。
* 檢閱[Microsoft Azure 監視器 REST API 參照](https://msdn.microsoft.com/library/azure/dn931943.aspx)。
* 檢閱[Azure 管理文件庫](https://msdn.microsoft.com/library/azure/mt417623.aspx)。
