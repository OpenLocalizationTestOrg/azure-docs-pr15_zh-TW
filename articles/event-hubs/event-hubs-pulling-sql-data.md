<properties
   pageTitle="將 Azure 事件集線器抽取 SQL 資料 |Microsoft Azure"
   description="事件集線器概觀匯入 SQL 範例"
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor=""/>

<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="spyros;sethm" />

# <a name="pulling-data-from-sql-into-an-azure-event-hub"></a>從 SQL 抽取資料，將 Azure 事件中心

應用程式以處理即時資料的典型架構需要先將它推到 Azure 事件集線器。 將可能 IoT 案例中，例如監控高速公路的不同往兩邊延伸流量遊戲案例中，監控的 frenzied 的競賽部族的動作或企業案例中的，例如監控建置進駐。 在下列情況下，資料生產者通常外部物件產生收集、 analyse 儲存，您需要的時間序列資料而遵守，而且您可能會有投資大費周章將 out 這些處理程序的基礎結構的建置。 什麼執行您如果您想要將資料從項目等資料庫，而不是串流資料來源，並用搭配其他串流資料？ 假設您要使用 Azure 資料流分析、 遠端資料檔案總管 (RDX) 或其他工具來分析及採取緩慢變更資料 Microsoft Dynamics AX 或自訂設備管理系統？ 若要解決這個問題，我們已撰寫，然後開啟來源的小型雲端範例，您可以修改並部署會提取 SQL 資料表的資料，並加以推入使用下游分析應用程式中輸入 Azure 事件中心。 並了解這是少見的案例中，並以正常方式做什麼事件中樞的相反。 不過，如果您發現自己在這種情況下您的需要您就可以 Azure 範例在圖庫中，[在這裡](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-import-from-sql/)找到程式碼。  

請注意，在這個範例中的程式碼，，樣本。 是**不**是實際執行應用程式並沒有嘗試，使其適合使用這類環境中使用-stricly DIY，則開發人員專屬的範例。 您需要檢閱各種不同的安全性、 效能、 功能，並在坐在端對端架構成本因素。

## <a name="application-structure"></a>應用程式結構

應用程式以 C# 撰寫，且 readme.md 檔案樣本中的包含所有您需要修改、 建立及發佈應用程式的資訊。 下列各節提供高層級的應用程式的功能概觀。

我們開始，其假設您有權存取 SQL Azure 資料表。 您也需要擁有設定 Azure 事件] 中心上，並知道連線字串所需的存取權限。

SqlToEventHub 解決方案啟動時，它會讀取設定檔案 (App.config) 取得的項目，數字，readme.md 檔案中所述。 這些是很自我闡明，例如名稱的運算列表等，且不需要 rehash 的說明。 

應用程式具有讀取設定檔，它進入迴圈，將讀取 SQL 資料表將記錄發送到 [事件] 中心內，然後進行之前，先將其過一次等待使用者定義的就寢時間間隔。 值得，有幾個項目︰

1. 應用程式根據假設 SQL 資料表更新某些外部的程序，而且您想要傳送所有和事件集線器的更新。
2. SQL 資料表必須具有唯一且遞增數字，例如，記錄 numer 的欄位。 這可能是非常簡單，稱為 「 識別碼 」，欄位或其他項目，就會增加為上述程序會更新該資料庫新增記錄，例如 「 Creation_time 」 或 「 Sequence_number 」。 應用程式備忘稿，並將每次儲存該欄位的值。 在循環播放每一個後續通過，應用程式基本上查詢表格該欄位的值，超過值的所有記錄的它所看到的最後一次透過循環播放。 我們的 「 位移 」 呼叫此最後一個值。
3. 應用程式建立的資料表 」 TableOffsets 」 於啟動時，若要儲存位移。 建立資料表與查詢定義設定檔中的 「 CreateOffsetTableQuery 」。 
4. 有數種使用 [位移] 資料表中設定檔中定義為 「 OffsetQuery 」、 「 UpdateOffsetQuery 」，以及 「 InsertOffsetQuery 」 所用的查詢。 您不應該變更這些。
5. 最後，設定檔中的 「 DataQuery 」 定義的查詢是將擷取的記錄，從 SQL 表格執行的查詢。 其功能目前僅適用於最佳化-迴圈每個階段的頂端 1000 記錄如果，例如 25000 記錄已新增至資料庫自上次的查詢，可能會花執行查詢。 藉由每次限制 1000 筆記錄的查詢，查詢會更快。 選取前 1000 簡單將連續的批次 1000 筆記錄的推入 [事件] 中心。    

## <a name="next-steps"></a>後續步驟

若要部署解決方案，複製或下載 SqlToEventHub 應用程式、 編輯 App.config 檔案、 建立，及最後發佈。 一旦您已發佈應用程式，您可以看下 Cloud Services Azure 傳統入口網站中執行，並監控寄至您的事件中樞的事件。 請注意的頻率取決於兩件事︰ SQL 表格，以及您在應用程式的設定檔中指定休眠間隔更新的頻率。