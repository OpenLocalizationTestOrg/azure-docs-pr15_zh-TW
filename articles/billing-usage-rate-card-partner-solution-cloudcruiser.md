<properties
   pageTitle="雲端巡洋艦和帳單 API 整合 Microsoft Azure |Microsoft Azure"
   description="從 Microsoft Azure 計費合作夥伴雲端巡洋艦 Azure 計費 Api 整合其產品經驗上提供唯一的觀點來看。  這是尤其有用的 Azure 和雲端巡洋艦有興趣使用/嘗試雲端巡洋艦的 Microsoft Azure 套件的客戶。"
   services=""
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""
   tags="billing"
   />

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="09/08/2016"
   ms.author="mobandyo;sirishap;bryanla"/>

# <a name="cloud-cruiser-and-microsoft-azure-billing-api-integration"></a>雲端巡洋艦和 Microsoft Azure 計費 API 整合

本文將說明如何從新的 Microsoft Azure 帳單 Api 收集的資訊可在雲端巡洋艦工作流程成本模擬和分析。

## <a name="azure-ratecard-api"></a>Azure RateCard API
RateCard API 會從 Azure 提供工資率資訊。 驗證具有適當認證之後，您可以查詢來收集 Azure 上的可用服務的相關的中繼資料，以及您提供的識別碼。 相關聯的工資率 API

以下是從顯示 A0 價格 API 範例回應 (Windows) 執行個體︰

    {
        "MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",
        "MeterName": "Compute Hours",
        "MeterCategory": "Virtual Machines",
        "MeterSubCategory": "A0 VM (Windows)",
        "Unit": "Hours",
        "MeterRates":
        {
            "0": 0.029
        },
        "EffectiveDate": "2014-08-01T00:00:00Z",
        "IncludedQuantity": 0.0,
        "MeterStatus": "Active"
    },

### <a name="cloud-cruisers-interface-to-azure-ratecard-api"></a>雲端 Azure RateCard API 巡洋艦的介面
雲端巡洋艦可以運用 RateCard API 資訊，以不同方式。 在本文中，我們會示範如何使用它，讓 IaaS 成本模擬及分析的工作量。

若要示範此使用大小寫，假設執行 Microsoft Azure 套件 (WAP) 上的多個執行個體的工作量。 目標是要模擬 Azure 此相同工作負載及估計的執行這類移轉成本。 若要建立這個模擬，有要執行兩個主要的工作︰

1. **匯入及程序服務收集的資訊從 RateCard API。** 活頁簿，轉換及發佈至新的工資率計劃從 RateCard API 解壓縮的位置上也執行這個工作。 此新費率計劃會用於模擬來估計 Azure 價格。

2. **將標準化 WAP 服務及 IaaS Azure 服務。** 根據預設，WAP 服務以個別資源 （CPU、 記憶體大小、 磁碟大小等），而 Azure 服務根據執行個體大小 （A0、 A1、 A2 等）。 第一個工作可以執行的雲端巡洋艦 ETL 引擎，稱為位置這些資源可以執行個體大小，類似於 Azure 的執行個體服務上建立的活頁簿。

### <a name="import-data-from-the-ratecard-api"></a>從 RateCard API 匯入資料

雲端巡洋艦活頁簿提供自動化的方式來收集並處理從 RateCard API 的資訊。  ETL （解壓縮-轉換-載入） 活頁簿可讓您設定的集合、 轉換及至雲端巡洋艦資料庫的資料發佈。

每個活頁簿可以有一或多個集合，可讓您建立關聯互補或擴大使用情況資料的不同來源的資訊。 下列兩個螢幕擷取畫面顯示如何建立新*集合*中現有的活頁簿，並將從 RateCard API*集合*匯入的資訊︰

![圖 1-建立新的集合][1]

![圖 2-從新集合匯入資料][2]

之後將活頁簿的資料匯入，就可以建立多個步驟及轉換處理程序，即可修改及模型資料。 例如，因為我們只是要基礎結構為-的-服務 (IaaS) 我們可以使用轉換步驟來移除不必要的資料列或記錄，相關 IaaS 以外的服務。

以下螢幕擷取畫面顯示用來處理資料收集從 RateCard API 的轉換步驟︰

![圖 3-轉換收集的資料，從 RateCard API [處理程序的步驟][3]

### <a name="defining-new-services-and-rate-plans"></a>定義新的服務與工資率方案

有不同的方式，以定義在雲端巡洋艦的服務。 其中一個選項是從使用情況資料匯入服務。 當您使用公用雲朵，其中服務已定義的提供者時，通常會使用這個方法。

工資率計劃是一組費率或可套用至不同的服務，根據有效的日期或群組的其他選項之間的客戶的價格。 工資率方案也可在雲端巡洋艦建立模擬或 「 模擬 」 的情況下，若要瞭解如何在服務中的變更可能會影響工作負載的總成本。

在此範例中，我們會使用服務的資訊 RateCard API 雲端巡洋艦中定義新的服務。 以相同的方式，我們可以使用的服務相關聯的工資率上，建立新的工資率規劃雲端巡洋艦。

轉換程序的結尾，則無法建立新的步驟，並將資料從 RateCard API 發佈為新服務及比率。

![圖 4-為新的服務及比率發佈 RateCard API 的資料][4]

### <a name="verify-azure-services-and-rates"></a>驗證 Azure 服務及比率

發佈服務及比率之後，您可以驗證在雲端巡洋艦*服務*] 索引標籤中匯入服務的清單︰

![圖 5-驗證新的服務][5]

在*方案工資率*] 索引標籤中，您可以檢查新名為 「 AzureSimulation 「 成本匯入 RateCard API 的工資率計劃。

![圖 6-確認新方案的工資率和相關聯的比率][6]

### <a name="normalize-wap-and-azure-services"></a>標準化 WAP 和 Azure 服務

根據預設，WAP 提供根據使用的計算、 記憶體和網路資源使用狀況資訊。 您可以在雲端巡洋艦中定義基礎服務直接在分派或這些資源的計量付費的使用方式。 例如，您可以設定基本的工資率的 CPU 使用率每小時或收費 GB 的記憶體配置執行個體。

此範例中，以比較成本之間 WAP 和 Azure，我們需要彙總上 WAP 配搭，然後可以 Azure 服務對應到資源使用狀況。 可以輕鬆地實作此轉換活頁簿中︰

![圖 7-WAP 要標準化服務的資料][7]

在活頁簿的最後一個步驟是發佈至雲端巡洋艦資料庫的資料。 在此步驟中，使用情況資料結合在一起的服務 （對應至 Azure 服務） 到且會連結至建立費用的預設工資率。

完成之後活頁簿，您可以自動化處理的資料，新增任務在排程器，並在指定的頻率和活頁簿若要執行的時間。

![圖 8-活頁簿排程][8]

### <a name="create-reports-for-workload-cost-simulation-analysis"></a>建立工作負載成本的模擬分析報表

使用狀況會收集並費用會載入到雲端巡洋艦資料庫後，我們可以運用雲端巡洋艦獲得深入見解模組，若要建立成本模擬我們所需的工作量。

為了示範這種情況下，我們可以建立下列報表︰

![成本的比較][9]

上方的圖表顯示成本比較服務、 比較 WAP （深藍色） 和 Azure （淺藍色） 之間執行工作負載的每個特定服務的價格。

下圖顯示相同的資料，但細分部門。 這會顯示每個部門 WAP 和 Azure 上執行的工作量，以及它們儲蓄列 （綠色） 之間的差異的成本。

## <a name="azure-usage-api"></a>Azure 的使用狀況 API


### <a name="introduction"></a>簡介

Microsoft 最近推出 Azure 使用 API，允許訂閱者以程式設計方式提取獲得深入見解到消耗他們的使用情況資料。 這是絕佳新聞的雲端巡洋艦客戶可利用透過這個 API 提供更豐富的資料集。

雲端巡洋艦可以使用數種方式使用 api 整合。 資料粒度 （每小時的使用狀況資訊），並可透過 API 的資源中繼資料資訊提供必要的資料集，以支援彈性 Showback 或 Chargeback 模型。 

在此教學課程中，我們將提供一個雲端巡洋艦可以助益的 API 使用狀況資訊的範例。 更明確地說，我們會建立 Azure 上的 [資源群組、 建立關聯的標籤帳戶結構，然後說明抽取和處理標籤資訊到雲端巡洋艦的程序。
 
最終的目標是可以建立下列項目，例如報表，並可以分析成本與消耗量根據填入標籤帳戶結構。

![圖 10-使用標籤分類的報表][10]

### <a name="microsoft-azure-tags"></a>Microsoft Azure 標籤

透過 Azure 使用 API 可用的資料包含而不是只是耗用的詳細資訊，包括任何標籤與其相關聯的資源中繼資料。 您需要，請確定標籤提供一個簡單的方法來組織您的資源，但才能生效，請︰

- 標籤正確佈建次套用至資源
- Showback/Chargeback 程序正確地使用標籤將組織的帳戶結構的使用方式。

兩個這些需求可以會容易，特別是有佈建或付費側手動程序。 打錯、 不正確或甚至遺失標籤時會使用標記與這些錯誤進行生活充電端很難從客戶的常見抱怨。

以新的 Azure 使用 API，雲端巡洋艦可以提取資源標記的詳細資訊，並透過名為活頁簿的複雜 ETL 工具，修復這些常見的標記錯誤]。 使用規則運算式 」 和 「 資料相互關聯的轉換，到雲端巡洋艦可以找出不正確的標記的資源，並套用正確的標籤中，確保資源的消費者與正確的關聯。

充電位於雲端巡洋艦自動化 Showback/Chargeback 程序，並可以利用標籤資訊繫結至適當的消費者 （部門、 部門、 專案等） 的使用方式。 此自動化提供一大改進，且確定一致且可稽核充電程序。
 

### <a name="creating-a-resource-group-with-tags-on-microsoft-azure"></a>在 Microsoft Azure 標籤建立資源群組
本教學課程中的第一步是建立資源群組在 Azure 入口網站，然後建立新的資源產生關聯的標籤。 例如，我們會建立下列標記︰ 部門環境擁有者，專案。

以下螢幕擷取畫面顯示範例資源群組相關聯的標籤。

![圖 11-相關聯標籤 Azure 入口網站上的 [資源] 群組][11]

下一步是從使用 API 的資訊擷取到雲端巡洋艦。 使用 API 目前提供 JSON 格式中的回覆。 以下是擷取資料的範例︰


    {
      "id": "/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXX/providers/Microsoft.Commerce/UsageAggregates/Daily_BRSDT_20150623_0000",
      "name": "Daily_BRSDT_20150623_0000",
      "type": "Microsoft.Commerce/UsageAggregate",
      "properties":
      {
        "subscriptionId": "bb678b04-0e48-4b44-XXXX-XXXXXXXXX",
        "usageStartTime": "2015-06-22T00:00:00+00:00",
        "usageEndTime": "2015-06-23T00:00:00+00:00",
        "meterName": "Compute Hours",
        "meterRegion": "",
        "meterCategory": "Virtual Machines",
        "meterSubCategory": "Standard_D1 VM (Non-Windows)",
        "unit": "Hours",
        "instanceData": "{\"Microsoft.Resources\":{\"resourceUri\":\"/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXXX/resourceGroups/DEMOUSAGEAPI/providers/Microsoft.Compute/virtualMachines/MyDockerVM\",\"location\":\"eastus\",\"tags\":{\"Department\":\"Sales\",\"Project\":\"Demo Usage API\",\"Environment\":\"Test\",\"Owner\":\"RSE\"},\"additionalInfo\":{\"ImageType\":\"Canonical\",\"ServiceType\":\"Standard_D1\"}}}",
        "meterId": "e60caf26-9ba0-413d-a422-6141f58081d6",
        "infoFields": {},
        "quantity": 8

      },
    },


### <a name="import-data-from-the-usage-api-into-cloud-cruiser"></a>將資料匯入的使用狀況 API 雲端巡洋艦

雲端巡洋艦活頁簿提供自動化的方式來收集並處理程序的 API 使用狀況資訊。 在 ETL （解壓縮-轉換-載入） 活頁簿可讓您設定集合、 轉換，以及在雲端巡洋艦資料庫的資料發佈。

每個活頁簿可以有一或多個集合。 這可讓您建立關聯互補或擴大使用情況資料的不同來源的資訊。 例如，我們會 Azure 範本活頁簿中建立新的工作表 (_UsageAPI)_設定匯入資訊的使用狀況 API 新的_集合_。

![圖 3-使用 API 將資料匯入 UsageAPI 工作表][12]

請注意此活頁簿已經有從 Azure (_ImportServices_)，匯入服務並處理消耗資訊計費 API (_PublishData_) 從其他工作表。

下一步我們會填入_UsageAPI_工作表中，使用使用 API，並建立關聯的計費 API 消耗資料_PublishData_工作表上的資訊。

### <a name="processing-the-tag-information-from-the-usage-api"></a>處理使用 API 的標籤資訊

之後將活頁簿的資料匯入，我們將_UsageAPI_工作表中建立轉換步驟才能處理 API 的資訊。 第一步是使用 「 JSON 分割 」 的處理器，擷取單一欄位標籤，然後建立這些 （部門、 Project、 擁有人和環境） 的每一個欄位。

![圖 4-建立新欄位的標籤資訊][13]

請注意 「 網路 」 服務就會缺少的標籤資訊 （黃色方塊），但我們可以驗證其查看_ResourceGroupName_欄位是相同的資源群組中的一部分。 因為此資源群組的其他資源的標籤，我們可以使用這項資訊，將遺失的標籤套用這項資源稍後的程序。

下一步是建立查閱資料表建立關聯的標籤_ResourceGroupName_的資訊。 在下一個步驟將用於此查閱表格豐富消耗資料標記的資訊。

### <a name="adding-the-tag-information-to-the-consumption-data"></a>新增標籤資訊到消耗資料

現在我們跳至_PublishData_工作表，處理計費 API 的使用資訊，並新增標籤從擷取的欄位。 查看在上一個步驟中，做為查閱索引鍵使用_ResourceGroupName_建立查閱表格來執行此程序。

![圖 5-填入帳戶結構的查閱中的資訊][14]

請注意 「 網路 」 服務的適當的帳戶結構欄位已套用的以遺失標籤修正問題。 我們也填入資源的帳戶結構欄位以外我們的目標資源群組的 「 其他 」，才能在報表上區別它們。

現在我們只需要新增至發佈的使用情況資料步驟。 在此步驟中，每個服務上我們工資率計劃所定義的適當工資率會套用到的使用狀況資訊，與載入資料庫產生收費。

您只需要完成這個程序一次移已最佳的組件。 完成活頁簿時，您只需要將其新增至排程器，它會在排定的時間執行每小時或每日。 是的建立新報表，或自訂現有的以分析資料，以取得有意義的深入見解從您的雲端使用方式。

### <a name="next-steps"></a>後續步驟

+ 如需建立雲端巡洋艦活頁簿和報表的詳細說明，請參閱雲端巡洋艦線上[文件](http://docs.cloudcruiser.com/)（所需的有效登入）。  如需有關雲端巡洋艦的詳細資訊，請連絡[info@cloudcruiser.com](mailto:info@cloudcruiser.com)。
+ 如需 Azure 資源使用狀況和 RateCard Api 的概觀，請參閱[獲得深入見解到消耗 Microsoft Azure 資源](billing-usage-rate-card-overview.md)。
+ 請參閱[Azure 計費 REST API 參考](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)的兩個 Api 的一部分的 Api 提供 Azure 資源管理員所設定的詳細資訊。
+ 如果您想要深入瞭解向右將程式碼範例，請查看我們 Microsoft Azure 計費 API 程式碼範例[Azure 程式碼](https://azure.microsoft.com/documentation/samples/?term=billing)範例。

### <a name="learn-more"></a>深入瞭解
+ 請參閱[Azure 資源管理員的概觀](azure-resource-manager/resource-group-overview.md)，瞭解更多關於 Azure 資源管理員。

<!--Image references-->
 
[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "圖 1-建立新的集合"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "圖 2-從新集合匯入資料"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "圖 3-轉換收集的資料，從 RateCard API [處理程序的步驟"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "圖 4-為新的服務及比率發佈 RateCard API 的資料"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "圖 5-驗證新的服務"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "圖 6-確認新方案的工資率和相關聯的比率"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "圖 7-WAP 要標準化服務的資料"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "圖 8-活頁簿排程"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "圖 9-工作量成本比較案例的範例報表"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "圖 10-使用標籤分類的報表"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "圖 11-相關聯標籤 Azure 入口網站上的 [資源] 群組"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "圖 12-使用 API 將資料匯入 UsageAPI 工作表"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "圖 13-建立新欄位的標籤資訊"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "圖 14-填入帳戶結構的查閱中的資訊"
