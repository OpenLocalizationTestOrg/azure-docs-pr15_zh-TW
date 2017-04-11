<properties 
    pageTitle="價格模型的邏輯應用程式 |Microsoft Azure" 
    description="價格的運作方式邏輯應用程式中的詳細資料" 
    authors="kevinlam1" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="klam"/>

# <a name="logic-apps-pricing-model"></a>價格模型的邏輯應用程式

Azure 邏輯應用程式可讓您不按比例縮放和雲端中執行整合工作流程。  以下是帳單和價格方案邏輯應用程式的詳細資訊。

## <a name="consumption-pricing"></a>消耗價格

新建立的邏輯應用程式使用消耗計劃。 使用邏輯應用程式消耗價格模型，您只是付款您所使用的。  使用消耗方案時，不會節流邏輯應用程式。
所有執行的邏輯應用程式執行個體中執行的動作是計量都付費。

### <a name="what-are-action-executions"></a>什麼是巨集指令執行？

邏輯應用程式定義中的每一個步驟就是動作。  這包含引動程序，等條件，為每個迴圈的範圍，請執行直到迴圈的來電至連接器與原生動作的通話控制項流程步驟。
觸發程序是特殊只設計用來產生新邏輯應用程式的執行個體，有特定的事件發生時的動作。  有數種不同的行為，這可能會影響 [計量付費邏輯應用程式是如何觸發程序。

-   **投票觸發程序，以**– 此觸發程序持續輪詢端點，直到收到一則訊息，滿足建立新的邏輯應用程式的執行個體的準則。  在設計工具中的邏輯應用程式的觸發程序中，您可以設定輪詢間隔。  每個投票要求，即使就不會建立新的執行個體邏輯的應用程式，會計算為巨集指令執行中。

-   **Webhook 觸發程序**– 此觸發程序等待用戶端加以傳送要求特定的端點上。  為巨集指令執行會計算每個傳送給 webhook 端點的要求。 要求與 HTTP Webhook 觸發是兩個 webhook 引動程序。

-   **循環觸發程序**– 此觸發程序會建立新的執行個體，根據週期間隔設定觸發程序中的邏輯應用程式。  例如，週期性觸發程序可以設定執行每 3 天或甚至每分鐘。

在觸發程序記錄組件中的邏輯應用程式資源刀能夠看到觸發程序執行。

所有所執行的動作是否已成功或失敗計量付費為巨集指令執行。  已略過，因為不符合條件的動作 」 或 「 未執行，因為邏輯應用程式終止完成前的動作不會計算為巨集指令執行中。

迴圈中執行的動作會計算每個迴圈的。  例如，在單一動作的每個迴圈逐一 10 個項目清單將會計算為乘上的循環播放 (1) 中的動作清單 (10) 中的項目計數加上一個初始的循環播放哪些，在此範例中，就會 (10 * 1) + 1 = 11 巨集指令執行。

邏輯已停用的應用程式不能有產生新的執行個體，因此已停用期間無法取得付費。  留意之後停用邏輯應用程式，可能需要執行個體靜止之前完全停用的一些時間。

## <a name="app-service-plans"></a>應用程式服務方案

若要建立邏輯應用程式不再需要應用程式服務方案。  您也可以參考應用程式與現有的邏輯應用程式的服務方案。  邏輯與應用程式服務方案先前建立的應用程式會繼續運作之前，根據方案選擇、 會取得經流速控制每日次數超過後不計費使用巨集指令執行計量表。

應用程式服務方案，每日允許巨集指令執行程序︰

| |空閒/共用/基本|標準|進階版|
|---|---|---|---|
|每日的巨集指令執行| 200|10000|50000|

### <a name="convert-from-consumption-to-app-service-plan-pricing"></a>從消耗轉換成應用程式服務方案價格

參照消耗邏輯應用程式應用程式服務方案，可讓您只是[執行下列 PowerShell 指令碼](https://github.com/logicappsio/ConsumptionToAppServicePlan)。  請確定您必須先安裝[PowerShell 的 Azure 工具](https://github.com/Azure/azure-powershell)。

``` powershell
Param(
    [string] $AppService_RG = '<app-service-resource-group>',
    [string] $AppService_Name = '<app-service-name>',
    [string] $LogicApp_RG = '<logic-app-resource-group>',
    [string] $LogicApp_Name = '<logic-app-name>',
    [string] $subscriptionId = '<azure-subscription-id>'
)

Login-AzureRmAccount 
$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId
$appserviceplan = Get-AzureRmResource -ResourceType "Microsoft.Web/serverFarms" -ResourceGroupName $AppService_RG -ResourceName $AppService_Name
$logicapp = Get-AzureRmResource -ResourceType "Microsoft.Logic/workflows" -ResourceGroupName $LogicApp_RG -ResourceName $LogicApp_Name

$sku = @{
    "name" = $appservicePlan.Sku.tier;
    "plan" = @{
      "id" = $appserviceplan.ResourceId;
      "type" = "Microsoft.Web/ServerFarms";
      "name" = $appserviceplan.Name  
    }
}

$updatedProperties = $logicapp.Properties | Add-Member @{sku = $sku;} -PassThru

$updatedLA = Set-AzureRmResource -ResourceId $logicapp.ResourceId -Properties $updatedProperties -ApiVersion 2015-08-01-preview
```

### <a name="convert-from-app-service-plan-pricing-to-consumption"></a>從應用程式服務方案價格到消耗轉換

若要變更具有應用程式服務，規劃到消耗模型與其相關聯之邏輯應用程式會移除應用程式服務規劃參考中的邏輯應用程式定義。  這可以使用 PowerShell cmdlet 通話︰

`Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`

## <a name="pricing"></a>價格

定價詳細資料請參閱[邏輯應用程式價格](https://azure.microsoft.com/pricing/details/logic-apps/)。

## <a name="next-steps"></a>後續步驟

- [邏輯應用程式的概觀][whatis]
- [建立您的第一個邏輯應用程式][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: app-service-logic-what-are-logic-apps.md
[create]: app-service-logic-create-a-logic-app.md

