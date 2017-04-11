<properties 
    pageTitle="資料流分析簡介 |Microsoft Azure" 
    description="深入了解資料流分析，可協助您分析串流資料從網際網路的項目 (IoT) 即時受管理的服務。" 
    keywords="分析服務，為受管理的服務、 資料流處理、 串流分析、 什麼是資料流狀況分析"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>


# <a name="what-is-stream-analytics"></a>什麼是資料流分析？

Azure 資料流分析是完整的管理、 成本有效即時事件處理引擎，若要解除鎖定資料的深深入資訊可協助。 資料流分析可以輕鬆設定即時資料串流從裝置、 感應器、 網站、 社交媒體、 應用程式、 基礎結構系統和更多的分析計算。

按幾下滑鼠，Azure 入口網站中，您可以作者指定的資料流資料，也就是您的工作結果的輸出接收輸入的來源資料流分析工作及資料轉換以類似 SQL 的語言。 您可以監視並調整比例/速度不按比例縮放的幾個 kb gb 或更多的事件秒處理 Azure 入口網站中工作。

資料流分析會運用 Microsoft Research 工時開發年的數高度調整 [串流的即時處理引擎及直覺式的規格之該語言整合。

## <a name="what-can-i-use-stream-analytics-for"></a>我可以使用什麼資料流分析資料？
大量的資料傳送高速度在線上的今天。 可以處理程序和採取此串流中的資料即時組織，可以大幅改善效率，並區別市場中。 案例的即時資料流分析可以找到所有產業︰ 個人化、 即時股票交易分析及財務服務公司; 所提供的通知即時網路詐騙偵測;資料與身分識別保護服務;可靠 ingestion 和感應器和傳動器所產生的資料分析內嵌在實體物件 （網際網路的項目或 IoT）;web 之類的分析。與客戶關係管理 (CRM) 應用程式時服務已降級為時間範圍內的客戶體驗，發出通知。 企業尋找最彈性、 可靠且效益的方式執行高度競爭力現代化商務世界中順利完成這類即時事件串流資料分析。

## <a name="key-capabilities-and-benefits"></a>主要功能與優點
-   **便於使用︰**資料流分析支援說明轉換的簡單的宣告查詢的模型。 若要最佳化以便於使用，串流分析會使用 T SQL variant，，而且不需要處理技術的複雜資料流處理系統的客戶。 使用[資料流分析查詢語言](https://msdn.microsoft.com/library/azure/dn834998.aspx)中的在瀏覽器查詢編輯器] 中，您會收到 intelli 具有意義可協助您可以快速且輕鬆地實作 [時間數列查詢，包括暫時型連接、 視窗的彙總、 暫時篩選及其他常見的作業，例如聯結與彙總、 計帳篩選自動完成。 此外，在瀏覽器查詢測試的範例資料檔案可讓快速反覆開發。  

-   **延展性︰**資料流分析足以處理多達 1 GB/第二個高事件處理量。 與[Azure 事件集線器](https://azure.microsoft.com/services/event-hubs/)和[Azure IoT 集線器](https://azure.microsoft.com/services/iot-hub/)整合来內嵌的每個連線的裝置，整體觀，從第二個來自事件數百萬方案，以及記錄檔，等等。 若要達到此目標串流分析會運用事件集線器，則會產生 1 MB/s 每個資料分割的資料分割功能。 使用者可以分割成數個查詢定義中的邏輯步驟計算，進行進一步的每個分割增加延展性。  

-   **可靠性]、 [重複性及 [快速修復︰**受管理的服務，在雲端，串流分析可協助避免資料遺失，並提供發生失敗透過內建修復功能的業務連續性。 內部維護狀態的能力，服務會提供可重複的結果，確保可以封存事件及重新套用處理以後，永遠獲得的相同的結果。 這可讓客戶回到時間和調查進行根本原因分析、 模擬分析等的運算。  

-   **低成本︰**雲端服務，以資料流分析已最佳化而適合提供使用者低成本上手與維護即時分析解決方案。 該服務是您的付款即可根據上串流單位使用情況和處理系統的資料量。 使用方式被衍生的事件處理音量和計算 power 叢集內佈建處理個別資料流分析工作的基礎。  

-   **參考資料︰**資料流分析讓使用者可以指定並使用 [參考資料。 這可能是歷史資料或較少隨時間變更，只要非串流資料。 系統會簡化將被視為任何其他連入事件串流加入其他事件的資料流 ingested 執行轉換的即時參考資料。  

-   **使用者定義函數︰**資料流分析有整合 Azure 電腦自動學習函數呼叫定義電腦學習服務中，做為資料流分析查詢的一部分。 這會展開以運用現有的 Azure 電腦學習解決方案的資料流分析功能。 如需此的詳細資訊，請檢閱[電腦學習整合教學課程](stream-analytics-machine-learning-integration-tutorial.md)。

-   **連線︰**資料流分析直接連線到 Azure 事件集線器和 Azure IoT 集線器的資料流 ingestion 和 Azure Blob 服務至內嵌歷程記錄的資料。 結果可寫入串流分析從 Azure 儲存體 Blob 或表格、 Azure SQL 資料庫、 Azure 資料湖儲存、 DocumentDB、 事件集線器、 Azure 服務匯流排主題或佇列和 Power BI，位置可以再以視覺化方式檢視，進一步處理的工作流程，透過[Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)批次分析中使用或處理一次為一系列的事件。 使用事件集線器時可能撰寫與其他資料來源和處理引擎搭配多個資料流分析，而不遺失資料流性質的計算。  

## <a name="get-help"></a>取得說明
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟
您已經知道什麼是資料流分析，受管理的服務串流分析的資料是來自網際網路的項目。 若要進一步瞭解這項服務，請參閱︰

- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)

