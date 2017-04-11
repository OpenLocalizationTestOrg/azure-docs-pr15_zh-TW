<properties
    pageTitle="要求能源技術指南中的預測 |Microsoft Azure"
    description="技術指南方案範本與 Microsoft Cortana 智慧能源預測的需求。"
    services="cortana-analytics"
    documentationCenter=""
    authors="yijichen"
    manager="ilanr9"
    editor="yijichen"/>

<tags
    ms.service="cortana-analytics"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="inqiu;yijichen;ilanr9"/>

# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-demand-forecast-in-energy"></a>視需要預測能源 Cortana 智慧解決方案範本技術指南

## <a name="overview"></a>**概觀**

方案範本的設計加速建置上方 Cortana 智慧套件 E2E 示範程序。 部署的範本會提供您的訂閱，必要的 Cortana 智慧元件，並建立之間的關聯性。 它也會設資料管線快速產生的資料模擬應用程式的範例資料。 下載資料模擬器提供的連結並將其安裝在您的本機電腦，請參閱需使用模擬器的指示。 從模擬器所產生的資料會產生的資料管線和開始產生然後視覺化 Power BI 儀表板上的電腦學習預測。

您可以找到解決方案範本[以下](https://gallery.cortanaintelligence.com/SolutionTemplate/Demand-Forecasting-for-Energy-1) 

部署程序會引導您完成設定您的方案認證的幾個步驟。 請確定您錄製這些認證，例如方案名稱、 使用者名稱和部署期間所提供的密碼。

這份文件的目標是要說明的參考架構與佈建在您的訂閱，屬於此方案範本中的不同元件。 有關如何使用您自己能看到的深入見解/預測您得資料的實際資料要取代範例資料，也說話文件。 此外，文件是在想要修改如果您想要自訂您自己的資料與解決方案的解決方案範本組件。 如何建立此方案範本的 [Power BI 儀表板上的指示會提供結尾。

## <a name="big-picture"></a>**一窺全貌**

![](media\cortana-analytics-technical-guide-demand-forecast\ca-topologies-energy-forecasting.png)

### <a name="architecture-explained"></a>說明架構
部署解決方案時，會啟用 Cortana 分析套件中的各種 Azure 服務 (*亦即*事件] 中心內，*串流分析、 HDInsight、 資料工廠、 電腦學習等*)。 上述架構圖表會顯示，在高的層級，從端對端的能源方案範本 Demand 預測建構方式。 您可以建立的解決方案部署解決方案範本圖表上按一下這些調查這些服務。 下列各節說明每一段。

## <a name="data-source-and-ingestion"></a>**資料來源並 Ingestion**

### <a name="synthetic-data-source"></a>綜合資料來源

此範本所使用的資料來源產生從桌面及應用程式，您會下載成功部署在本機上執行。 您會發現下載並安裝這個應用程式屬性列中，當您選取第一個節點稱為能源預測資料模擬器解決方案範本圖上的指示進行。 這個應用程式摘要[Azure 事件中心](#azure-event-hub)服務的資料點或將會用於解決方案流程的其餘部分中的事件。

事件產生應用程式會填入 Azure 事件中心，只有在您的電腦上執行時，只。

### <a name="azure-event-hub"></a>Azure 事件中心

[Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)服務會提供綜合上述的資料來源所輸入的收件者。

## <a name="data-preparation-and-analysis"></a>**資料準備及分析**


### <a name="azure-stream-analytics"></a>Azure 資料流狀況分析

[Azure 資料流分析](https://azure.microsoft.com/services/stream-analytics/)服務用來提供附近[Azure 事件中心](#azure-event-hub)服務輸入資料流即時分析及發佈到[Power BI](https://powerbi.microsoft.com)儀表板，以及封存所有原始的連入事件[Azure 儲存體](https://azure.microsoft.com/services/storage/)服務稍後處理[Azure 資料工廠](https://azure.microsoft.com/documentation/services/data-factory/)服務的結果。

### <a name="hd-insights-custom-aggregation"></a>HD 獲得深入見解自訂彙總

Azure HD 充分服務用來執行[登錄區](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx)（Azure 資料工廠協調） 的指令碼提供的彙總所使用的 Azure 資料流分析服務封存的原始事件。

### <a name="azure-machine-learning"></a>Azure 機器學習

使用[Azure 電腦學習](https://azure.microsoft.com/services/machine-learning/)服務 （Azure 資料工廠協調） 上的特定區域指定收到的輸入未來 power 消耗進行預測。

## <a name="data-publishing"></a>**資料發佈**


### <a name="azure-sql-database-service"></a>Azure SQL 資料庫服務

[Azure SQL 資料庫](https://azure.microsoft.com/services/sql-database/)服務用來儲存 （受管理的 Azure 資料工廠） 將會用在[Power BI](https://powerbi.microsoft.com)儀表板 Azure 電腦學習服務接收預測。

## <a name="data-consumption"></a>**資料使用**

### <a name="power-bi"></a>Power BI

[Power BI](https://powerbi.microsoft.com)服務用來顯示包含彙總，提供由[Azure 資料流分析](https://azure.microsoft.com/services/stream-analytics/)服務，以及視需要預測結果儲存於[Azure SQL 資料庫](https://azure.microsoft.com/services/sql-database/)中使用[Azure 電腦學習](https://azure.microsoft.com/services/machine-learning/)服務作業所產生的儀表板。 如需如何建立此方案範本的 [Power BI 儀表板上的指示，請參閱下方的一節。

## <a name="how-to-bring-in-your-own-data"></a>**如何以將在自己的資料**

本節說明如何將 Azure 自己的資料，您匯入此架構的資料區域需要變更。

不太任何資料集，您將會比對此方案範本所用的資料集。 瞭解您的資料，需求會在您修改此範本，使用您自己的資料的方式很重要。 如果這是您第一份揭露 Azure 電腦學習服務，您可以取得的簡介[如何建立您的第一個實驗](machine-learning\machine-learning-create-experiment.md)中使用範例。

下列各節將討論的章節會引入新的資料集時，需要修改的範本。

### <a name="azure-event-hub"></a>Azure 事件中心

[Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)服務，是非常一般，資料發佈中樞 CSV 或 JSON 格式。 沒有特殊的處理會發生在 Azure 事件中心內，但請務必瞭解送入的資料。

這份文件不說明如何內嵌您的資料，但其中一個可以輕鬆地事件或傳送資料到 Azure 事件集線器，使用[事件中心 API](event-hubs\event-hubs-programming-guide.md)。

### <a name="azure-stream-analytics"></a>Azure 資料流狀況分析

[Azure 資料流分析](https://azure.microsoft.com/services/stream-analytics/)服務用來提供附近即時分析，以資料流讀取和輸出資料來源的任何數字。

視需要預測能源解決方案範本，Azure 資料流分析查詢包含兩個子查詢，每個使用事件從 Azure 事件中心服務做為輸入與輸出需要兩個不同的位置。 這些輸出包含一個 Power BI 資料集和一個 Azure 儲存位置。

可以找到[Azure 資料流分析](https://azure.microsoft.com/services/stream-analytics/)查詢︰

-   登入[Azure 管理入口網站](https://manage.windowsazure.com/)

-   尋找資料流分析工作![](media\cortana-analytics-technical-guide-demand-forecast\icon-stream-analytics.png)解決方案部署時產生的。 一個用於推入 blob 儲存體 (例如 mytest1streaming432822asablob) 的資料，而另一個值會用於推入至 Power BI (例如 mytest1streaming432822asapbi) 的資料。


-   選取

    -   若要檢視查詢輸入的***輸入***

    -   若要檢視查詢本身的***查詢***

    -   若要檢視不同的輸出會***將輸出***

MSDN 上的[資料流分析查詢參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)中找 Azure 資料流分析查詢建築的相關資訊。

在本方案中，將輸出以資料集內送資料流的 Power BI 儀表板的即時分析資訊附近 Azure 資料流分析工作，提供為此方案範本的一部分。 因為沒有明確瞭解相關的內送的資料格式，這些查詢需要更改根據您的資料格式。

Azure 資料流分析作業輸出至[Azure 儲存體](https://azure.microsoft.com/services/storage/)的所有[事件中心](https://azure.microsoft.com/services/event-hubs/)事件，並為完整的事件資訊會傳送至儲存空間，因此需要不論您資料的格式為何沒有變更。

### <a name="azure-data-factory"></a>Azure 資料工廠

[Azure 資料工廠](https://azure.microsoft.com/documentation/services/data-factory/)服務協調移動及處理資料。 視需要預測能源解決方案範本中資料 factory 組成十二個[管線](data-factory\data-factory-create-pipelines.md)移動及處理使用各種不同的技術資料。

  您可以存取您的資料工廠開啟底部的 [建立的解決方案部署解決方案範本圖表的資料工廠節點。 這會將您帶往您 Azure 管理入口網站上的資料工廠。 如果您看到錯誤，在您的資料集，都可以略那些，因為它們是因為資料工廠部署之前開始的資料產生器。 這些錯誤不會使您資料的工廠無法運作。

本節將討論的必要的[管線](data-factory\data-factory-create-pipelines.md)和[Azure 資料工廠](https://azure.microsoft.com/documentation/services/data-factory/)中所包含的[活動](data-factory\data-factory-create-pipelines.md)。 以下是方案的 [圖表] 檢視。

![](media\cortana-analytics-technical-guide-demand-forecast\ADF2.png)

五個這個工廠的管線包含用來分割和彙總資料的[登錄區](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx)指令碼。 當所述，指令碼會位於在安裝期間所建立的[Azure 儲存體](https://azure.microsoft.com/services/storage/)帳戶。 其位置會是︰ demandforecasting\\\\指令碼\\\\登錄區\\\\ （或 https://[Your 解決方案 name].blob.core.windows.net/demandforecasting）。

類似於[Azure 資料流分析](#azure-stream-analytics-1)查詢，[登錄區](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx)指令碼的內送的資料格式的隱含瞭解，這些查詢所需變更根據您的資料格式及[功能工程](machine-learning\machine-learning-feature-selection-and-engineering.md)需求。

#### <a name="aggregatedemanddatato1hrpipeline"></a>*AggregateDemandDataTo1HrPipeline*

此[管線](data-factory\data-factory-create-pipelines.md)管線包含單一活動-使用執行彙總每 10 秒內的[登錄區](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx)指令碼[HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) [HDInsightHive](data-factory\data-factory-hive-activity.md)活動串流分站每小時地區階層層級要求資料，並透過 Azure 資料流分析工作放置中[Azure 儲存體](https://azure.microsoft.com/services/storage/)。

此分割任務的[登錄區](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx)指令碼是***AggregateDemandRegion1Hr.hql***


#### <a name="loadhistorydemanddatapipeline"></a>*LoadHistoryDemandDataPipeline*

此[管道的郵件](data-factory\data-factory-create-pipelines.md)包含兩個活動︰
- 使用執行彙總的每小時歷程記錄的登錄區指令碼[HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx)的[HDInsightHive](data-factory\data-factory-hive-activity.md)活動要求每小時區域層級分站層級中的資料和 Azure 資料流分析工作期間置於 Azure 儲存體

- 將彙總的資料從 Azure 儲存體 blob 移到佈屬於解決方案範本安裝 Azure SQL 資料庫的[複製](https://msdn.microsoft.com/library/azure/dn835035.aspx)活動。

此工作的[登錄區](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx)指令碼是***AggregateDemandHistoryRegion.hql***。


#### <a name="mlscoringregionxpipeline"></a>*MLScoringRegionXPipeline*

這些[管線](data-factory\data-factory-create-pipelines.md)包含多個活動，而且其最後的結果是從使用此方案範本相關聯的 Azure 電腦學習實驗 scored 的預測。 除了每個只會處理的每個區域的傳入 ADF 管線和登錄區指令碼的不同 RegionID 所進行的不同區域都幾乎一樣。  
包含在此活動有︰
-   使用執行登錄區指令碼來執行彙總和 Azure 電腦學習實驗所需的功能工程[HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) [HDInsightHive](data-factory\data-factory-hive-activity.md)活動。 這項工作的登錄區指令碼會分別***PrepareMLInputRegionX.hql***。

-   將結果從[HDInsightHive](data-factory\data-factory-hive-activity.md)活動移至單一 Azure 儲存體 blob 的可存取[AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx)活動的[複製](https://msdn.microsoft.com/library/azure/dn835035.aspx)活動。

-   通話 Azure 電腦學習的[AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx)活動嘗試 blob 結果放在單一的 Azure 儲存體中的結果。

#### <a name="copyscoredresultregionxpipeline"></a>*CopyScoredResultRegionXPipeline*
這些[管線](data-factory\data-factory-create-pipelines.md)包含單一活動-將 Azure 電腦學習實驗的結果分別***MLScoringRegionXPipeline***從移至 [Azure SQL 資料庫佈解決方案範本安裝的一部分的[複製](https://msdn.microsoft.com/library/azure/dn835035.aspx)活動。

#### <a name="copyaggdemandpipeline"></a>*CopyAggDemandPipeline*
此[管線](data-factory\data-factory-create-pipelines.md)包含單一活動-將彙總的後續要求資料從***LoadHistoryDemandDataPipeline***移到佈屬於解決方案範本安裝 Azure SQL 資料庫[複製](https://msdn.microsoft.com/library/azure/dn835035.aspx)活動。

#### <a name="copyregiondatapipeline-copysubstationdatapipeline-copytopologydatapipeline"></a>*CopyRegionDataPipeline，CopySubstationDataPipeline，CopyTopologyDataPipeline*
這些[管線](data-factory\data-factory-create-pipelines.md)包含單一活動-移動屬於 Azure SQL 資料庫屬於解決方案範本安裝佈建的解決方案範本安裝上傳至 Azure 儲存體 blob 地區/分站/Topologygeo 參考資料[複製](https://msdn.microsoft.com/library/azure/dn835035.aspx)活動。

### <a name="azure-machine-learning"></a>Azure 機器學習
[Azure 電腦學習](https://azure.microsoft.com/services/machine-learning/)實驗用於此方案範本提供的指定區域的預測。 實驗特定到消耗資料集，因此需要修改或取代特殊帶中的資料。

## <a name="monitor-progress"></a>**監控進度**
啟動資料產生器，管線開始取得 hydrated 後解決方案的不同元件啟動動作下列資料工廠所發出的命令將選定。 有兩種方法您可以監視管道的郵件。

1. 核取 Azure Blob 儲存體中的資料。

    其中一個資料流分析工作的原始的內送資料寫入 blob 儲存體。 如果您按一下**Azure Blob 儲存體**元件，您的方案從畫面上您成功部署解決方案，然後按一下 [在右側的面板中的 [**開啟**，會將您帶往[Azure 管理入口網站](https://portal.azure.com)。 一次，按一下**二進位大型物件**。 在下一步] 面板中，您會看到容器的清單。 按一下**「 energysadata 」**。 在 [下一步] 面板中，您會看到**「 demandongoing 」**資料夾。 在 rawdata] 資料夾中，您會看到資料夾的名稱，例如日期 = 2016年-01-28 等。如果您看到這些資料夾時，就表示的原始資料成功所產生的電腦上與儲存在 blob 儲存體。 您應該會看到這些資料夾中 mb 應有有限大小的檔案。

2. 核取 [從 Azure SQL 資料庫的資料。

    管線的最後一個步驟是將 SQL 資料庫撰寫資料 （例如從電腦學習預測）。 您可能必須等候最大值的資料顯示在 SQL 資料庫 2 的時數。 監控資料量有 SQL 資料庫中的其中一個方法是透過[Azure 管理入口網站](https://manage.windowsazure.com/)。 在 [左] 面板上找出 SQL 資料庫![](media\cortana-analytics-technical-guide-demand-forecast\SQLicon2.png)並按一下它。 接著找出您的資料庫 (亦即 demo123456db)，然後按一下它。 在**[連線到您的資料庫]**區段下 [下一步] 頁面中，按一下**「 執行 SQL 查詢 SQL 資料庫]**。

    在這裡，您可以按一下 [在新的查詢，然後查詢 (例如 「 選取 count(*) 從 DemandRealHourly) 的資料列數目 」 資料庫規模擴大時，在資料表中的資料列數目應該增加。）

3. 核取 [Power BI 儀表板中的資料。

    您可以監視未經處理的內送資料來設定 Power BI 忙碌路徑儀表板。 請依照 「 Power BI 儀表板 」 一節中的指示。



## <a name="power-bi-dashboard"></a>**Power BI 儀表板**

### <a name="overview"></a>概觀

本節將說明如何設定 Power BI 儀表板，若要從 Azure 資料流分析 （熱門的路徑） 的即時資料視覺化，以及預測從 Azure 電腦學習 （低溫路徑） 的結果。


### <a name="setup-hot-path-dashboard"></a>設定快速鍵路徑儀表板

下列步驟會引導您以視覺化方式呈現資料流分析作業所產生的解決方案部署次的即時資料輸出的方式。 [Power BI 線上](http://www.powerbi.com/)帳戶，才能執行下列步驟。 如果您沒有帳戶，您可以[建立一個](https://powerbi.microsoft.com/pricing)。

1.  新增 Power BI 輸出中 Azure 資料流分析 (ASA)。

    -  您必須遵循中的指示[Azure 資料流分析及 Power BI︰ 串流資料的即時掌握即時分析儀表板](stream-analytics-power-bi-dashboard.md)設定為 Power BI 儀表板 Azure 資料流分析工作的成果。

    - 在您[Azure 管理入口網站](https://manage.windowsazure.com)中，找出串流分析作業。 工作的名稱都必須是︰ YourSolutionName + 「 streamingjob 」 + 隨機數字 + 「 asapbi 」 (亦即 demostreamingjob123456asapbi)。

    - 加入中的輸出 ASA 工作。 將**輸出別名**設為**「 PBIoutput 」**。 設定您的**資料集的名稱**及**表格名稱**為**「 EnergyStreamData 」**。 一次您已新增輸出中，按一下**[開始]**頁面的底部開始串流分析工作。 您應收到的確認訊息 （*例如*，「 開始串流分析工作 myteststreamingjob12345asablob 成功 」）。

2. 登入[Power BI 線上](http://www.powerbi.com)

    -   在左側面板資料集] 區段中 [我的工作區中，您應該能夠看到在 Power BI 的左窗格中顯示新的資料集。 這是您從 Azure 資料流分析推入前一個步驟的串流資料。

    -   請確定已開啟 [***視覺效果***] 窗格，而且會顯示在螢幕右側。

3. 建立 「 Demand 戳記] 方塊︰
    -   按一下 [在左側面板資料集] 區段的資料集**'EnergyStreamData'** 。

    -   按一下**[折線圖]**圖示![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic8.png)。

    -   按一下 [**欄位**] 面板中的 ['EnergyStreamData 」]。

    -   按一下**[時間戳記 」** ，並確定沒有顯示在 [座標軸] 下。 按一下**[載入]** ，並確認其會顯示在 [值] 下方。

    -   在頂端按一下 [**儲存**並命名為 「 EnergyStreamDataReport 」 的報表。 名為 「 EnergyStreamDataReport 」 報表會顯示在左側的 [導覽] 窗格中的 [報表] 區段中。

    -   按一下 [ **」 Pin 視覺 」** ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png)在此折線圖的右上角的圖示，在 「 釘選至儀表板 」 視窗可能會顯示供您選擇儀表板。 請選取 「 EnergyStreamDataReport 」，然後按一下 「 固定 」。

    -   將滑鼠移至儀表板在這個並排顯示，按一下 「 編輯 」 上若要變更其標題為 「 指定的時間戳記 」 右上角的圖示

4.  建立其他根據適當的資料集的儀表板方塊。 最終的儀表板檢視如下所示。
        ![](media\cortana-analytics-technical-guide-demand-forecast\PBIFullScreen.png)


### <a name="setup-cold-path-dashboard"></a>設定低溫路徑儀表板
低溫路徑資料管道，不可或缺的目標是取得 demand 預測的每個區域。 Power BI 連線至 Azure SQL 資料庫作為資料來源，預測結果的儲存位置。

> [AZURE.NOTE] 1） 花幾個小時，收集在儀表板不夠預測的結果。 我們建議您啟動此程序 2-3 小時後午餐資料產生器。 2） 在此步驟中，前提是下載並安裝免費的軟體[Power BI desktop](https://powerbi.microsoft.com/desktop)。



1.  取得資料庫認證。

    移至下一個步驟之前，您會需要**資料庫伺服器的名稱、 資料庫名稱、 使用者名稱和密碼**。 以下是指導您如何找到這些步驟。

    -   後解決方案範本圖表上的**「 Azure SQL 資料庫 」**變成綠色，請按一下它，然後按一下 [ **」 開啟 「**。 您將會引導您 Azure 管理入口網站，也會開啟資料庫的 [資訊] 頁面。

    -   在頁面上，您可以尋找 「 資料庫 」 區段。 它會列出查看您已建立的資料庫。 **「 您的方案名稱 + 隨機數字 + 'db' 」** (例如 「 mytest12345db 」)，應該是資料庫的您的名稱。

    -   按一下您的資料庫中的新的快顯 pannel，您可以在頂端找到您的資料庫伺服器名稱。 您資料庫伺服器名稱名稱應該是**「 您的方案名稱 + 隨機數字 + 'database.windows.net,1433' 」** (例如 「 mytest12345.database.windows.net,1433 」)。

    -   您的資料庫**的使用者名稱**和**密碼**是相同的使用者名稱和密碼先前錄製期間部署方案。

2.  更新低溫路徑 Power BI 檔案的資料來源
    -  請確定您已安裝最新版的[Power BI desktop](https://powerbi.microsoft.com/desktop)。

    -   在您下載**」 DemandForecastingDataGeneratorv1.0 」**資料夾中，按一下**「 Power BI Template\DemandForecastPowerBI.pbix 」**檔案。 初始的視覺效果會根據空的資料。 **附註︰**如果您看到的錯誤處理，請確定您已經安裝最新版的 Power BI Desktop。

        一旦您開啟時，上方的檔案，請按一下 [**編輯查詢**]。 在快顯視窗中，按兩下**[來源]**右側的面板。
    ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic1.png)

    -   在快顯視窗中，使用您自己的伺服器和資料庫名稱取代**「 伺服器 」**及**「 資料庫 」** ，然後按一下**[確定]**。 伺服器名稱，請確定您指定的連接埠 1433 (**YourSolutionName.database.windows.net，1433年**)。 略過的畫面顯示的警告訊息]。

    -   中的下一個快顯視窗，您會看到這兩個選項，在左窗格 （**Windows**和**資料庫**）。 按一下**「 資料庫 」**中，填寫您的**「 使用者名稱 」**和**「 密碼 」** （這是使用者名稱和密碼，您可以輸入當您第一次部署解決方案，並建立 Azure SQL 資料庫）。 在 [***選取要套用至這些設定的層級***，請核取資料庫層級的選項。 然後按一下**[連線]**。

    -   一旦您引導至前一頁，請關閉視窗。 就會出現一則訊息分門別類-按一下 [**套用**]。 最後，按一下 [**儲存**] 按鈕，以儲存變更]。 Power BI 檔案現在已建立連線到伺服器。 如果是空的視覺效果，請確定清除圖例的右上角的 [橡皮擦] 圖示，即可呈現的所有資料的視覺效果上的選擇。 使用 [重新整理] 按鈕，以反映新的資料視覺效果。 開始時，您只會看到種子資料對視覺效果為資料 factory 排程重新整理每 3 小時。 3 小時後，您會看到時重新整理的資料，反映在您的視覺效果中的新預測。

3. （選用）[Power](http://www.powerbi.com/)bi 線上發佈低溫路徑儀表板。 請注意此步驟中需要 Power BI 帳戶 （或 Office 365 帳戶）。

    -   按一下**「 發佈 」** ，數秒稍後會出現一個視窗顯示 「 發佈至 Power BI 成功 ！ 」 綠色核取記號。 按一下下列 「 Power BI 中開啟 demoprediction.pbix 」 連結。 若要尋找的詳細的指示，請參閱[從 Power BI Desktop 發佈](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop)。

    -   若要建立新的儀表板︰ 按一下 [**+**旁在左窗格的 [**儀表板**] 區段的符號。 輸入此新儀表板的名稱 「 Demand 預測示範 」。

    -   一旦您開啟報表，請按一下 [![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png)釘選至您的儀表板的所有視覺效果。 若要尋找的詳細的指示，請參閱[從報表的 Power BI 儀表板方塊的 Pin](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report)。
        移至儀表板頁面與調整大小和位置的視覺效果和編輯其標題。 若要尋找如何編輯磚的詳細的指示，請參閱[編輯磚，以調整大小、 移動、 重新命名、 pin、 刪除、 新增超連結](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename)。 以下是範例儀表板與釘選到其部分低溫路徑視覺效果。

        ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic7.png)

4. （選用）排程重新整理的資料來源。
    -     排程重新整理的資料，將游標移**EnergyBPI 完稿**資料集，請按一下 [ ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic3.png) ]，然後選擇 [**重新整理排程**。
    **附註︰**如果您看到警告操作，按一下 [**編輯認證**，請確定您的資料庫認證是所步驟 1 所述的相同。

    ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic4.png)

    -   展開 [**重新整理排程**] 區段。 開啟 「 保留您的資料保持最新狀態]。

    -   排定的重新整理，根據您的需求。 若要尋找詳細資訊，請參閱[Power BI 中的資料重新整理](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)。


## <a name="how-to-delete-your-solution"></a>**如何刪除您的方案**
請確定您未使用方案，如下所執行的資料產生器會產生更高的成本時停止資料產生器。 請如果您不使用它，刪除方案。 刪除您的方案，會刪除部署解決方案時，在您的訂閱佈建的所有元件。 若要刪除您的方案名稱解決方案範本的 [左] 面板中的方案按一下，然後按一下 [刪除]。

## <a name="cost-estimation-tools"></a>**成本估計工具**

下列兩種工具可協助您進一步瞭解 Demand 預測能源解決方案範本在執行您的訂閱所需的總成本︰

-   [Microsoft Azure 成本估算程式工具 （線上）](https://azure.microsoft.com/pricing/calculator/)

-   [Microsoft Azure 成本估算程式工具 （桌面）](http://www.microsoft.com/download/details.aspx?id=43376)

## <a name="acknowledgements"></a>**通知**
本文是由資料科學家 Yijing 存取許可權和軟體工程師 Qiu 最小值，在 Microsoft 撰寫。
