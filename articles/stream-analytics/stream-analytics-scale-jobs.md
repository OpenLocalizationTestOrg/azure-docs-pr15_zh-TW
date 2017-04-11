<properties
    pageTitle="不按比例縮放串流分析工作，以增加處理量 |Microsoft Azure"
    description="瞭解如何設定輸入磁碟分割區、 調整查詢，並設定工作串流單位縮放串流分析工作。"
    keywords="資料串流、 串流資料處理微調狀況分析"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="scale-azure-stream-analytics-jobs-to-increase-stream-data-processing-throughput"></a>不按比例縮放 Azure 資料流分析工作，以增加串流資料處理處理量

瞭解如何調整分析工作，並計算*串流單位*串流分析如何調整串流分析工作設定輸入的分割、 調整分析查詢定義並設定串流單位的工作。 

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>什麼是資料流分析工作的部分？
資料流分析工作定義包含輸入、 查詢，以及輸出。 輸入的工作位置讀取資料流、 查詢用來轉換資料輸入資料流，以及輸出是工作傳送工作結果的位置。  

工作需要至少有一個輸入的來源資料串流。 可以儲存資料串流輸入的來源，在 Azure 服務匯流排事件中心或 Azure Blob 儲存體。 如需詳細資訊，請參閱[Azure 資料流分析簡介](stream-analytics-introduction.md)，並[開始使用 Azure 資料流分析](stream-analytics-get-started.md)。

## <a name="configuring-streaming-units"></a>設定串流單位
串流單位 (SUs) 代表資源及計算執行 Azure 資料流分析作業所需的能力。 SUs 提供描述相對事件處理容量根據的 CPU、 記憶體、 混合量值與讀取和寫入費率。 每個串流單位會對應到約 1 MB/第二個處理量。 

選擇多少 SUs 所需的特定工作輸入和查詢定義的作業的磁碟分割設定而定。 您可以選取進位到您在 [串流單位工作使用 Azure 傳統入口網站的配額。 每個 Azure 的訂閱，依預設會有進位到所有的分析工作 50 串流單位的配額中的特定區域。 若要增加您的訂閱串流單位，請連絡[Microsoft 支援服務](http://support.microsoft.com)。

工作可以利用的串流單位數量輸入和查詢定義的作業的磁碟分割設定而定。 請注意，必須使用串流單位是有效的值。 有效的值從 1、 3、 6 開始，然後向上的遞增量 6，如下所示。

![Azure 資料流分析串流單位縮放比例][img.stream.analytics.streaming.units.scale]

本文將說明如何計算和微調查詢，增加的分析工作處理量。

## <a name="embarrassingly-parallel-job"></a>平行工作
平行工作是我們 Azure 資料流分析中有最調整案例。 連線到一個磁碟分割輸出的一個磁碟分割輸入到查詢的執行個體之一。 要達到此平行需要幾個項目︰

1.  如果您的查詢邏輯相依處理相同的查詢執行個體的相同的索引鍵，您必須確定事件前往您輸入的相同的磁碟分割。 如果事件集線器，這表示事件資料必須具備**PartitionKey**設定，或您可以使用分割的寄件者。 Blob，這表示事件會傳送到相同的磁碟分割資料夾。 如果您的查詢邏輯時，不需要相同的金鑰處理相同的查詢執行個體，您可以忽略這項需求。 此範例是一個簡單的選取/專案篩選查詢。  
2.  後資料的配置，必須輸入端等，我們需要確認已分割查詢。 這需要您使用**的磁碟分割**中的所有步驟。 允許多個步驟，但它們都必須分割以相同的金鑰。 另一個注意的是，目前，分割的索引鍵需要擁有完全平行工作設為**PartitionId** 。  
3.  目前僅事件集線器和 Blob 支援分割的輸出。 事件集線器輸出，您需要設定為**PartitionId** **PartitionKey**欄位。 Blob 的您不必執行任何動作。  
4.  注意，輸入磁碟分割區數必須等同的輸出磁碟分割區的另一個動作。 Blob 輸出目前不支援磁碟分割區，但沒有關係，它將繼承上游查詢的磁碟分割配置。 允許完整平行工作的磁碟分割值的範例︰  
    1.  8 事件集線器輸入磁碟分割區和 8 事件集線器輸出磁碟分割區
    2.  8 事件集線器輸入磁碟分割區和 Blob 輸出  
    3.  8 blob 輸入磁碟分割區及 Blob 輸出  
    4.  8 blob 輸入磁碟分割區和 8 事件集線器輸出磁碟分割區  

以下是一些範例案例的平行。

### <a name="simple-query"></a>簡單的查詢
8 的磁碟分割區以輸入-8 磁碟分割輸出事件集線器 – 事件中心

**查詢︰**

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

此查詢簡單的篩選器，而，我們不需要擔心分割我們傳送給事件集線器輸入。 您會發現查詢具有**分割區以**的**PartitionId**，因此我們滿足上面不同時的要求 #2。 在結果中輸出，我們需要設定事件集線器輸出中設定為 [ **PartitionId** **PartitionKey**欄位的工作。 一個的最後一個核取、 輸入磁碟分割區 = = 輸出資料分割。 此拓撲是平行。

### <a name="query-with-grouping-key"></a>分組索引鍵的查詢
輸入-8 磁碟分割輸出事件集線器 – Blob

**查詢︰**

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

這個查詢有群組索引鍵，因此，相同的金鑰需要處理相同的查詢執行個體。 這表示我們要分割的方式傳送到事件的我們事件。 我們想瞭解哪一個按鍵？ **PartitionId**工作邏輯概念，我們關注的資訊之實際鍵是**TollBoothId**。 這表示我們應該設定**PartitionKey**事件資料的我們傳送給事件集線器為**TollBoothId**的事件。 查詢有**分割區以**的**PartitionId**，因此我們還有建議。 在結果中輸出，因為它是 Blob，我們不需要擔心設定**PartitionKey**。 針對需求 #4，一次，這是 Blob，因此我們不需要擔心。 此拓撲是平行。

### <a name="multi-step-query-with-grouping-key"></a>多重步驟查詢分組索引鍵 ###
輸入-8 事件中心分割輸出-8 的磁碟分割的事件中心

**查詢︰**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

這個查詢有群組索引鍵，因此，相同的金鑰需要處理相同的查詢執行個體。 我們可以使用相同的策略為先前的查詢。 查詢會有多個步驟。 會有**分割區以** **PartitionId**的每一個步驟？ 是的因此我們建議。 在結果中輸出，我們需要**PartitionKey**設**PartitionId**等探討，我們也可以查看有數相同的磁碟分割區輸入。 此拓撲是平行。


## <a name="example-scenarios-that-are-not-embarrassingly-parallel"></a>範例案例的不是平行

### <a name="mismatched-partition-count"></a>不相符的資料分割計數 ###
32 的磁碟分割區以輸入-8 磁碟分割輸出事件集線器 – 事件中心

不論查詢功能在此情況下因為輸入分割計數 ！ = 輸出資料分割計數。

### <a name="not-using-event-hubs-or-blobs-as-output"></a>不使用事件集線器或 Blob 作為輸出
輸入-8 磁碟分割輸出事件集線器 – 中

中輸出目前不支援分割。

### <a name="multi-step-query-with-different-partition-by-values"></a>使用不同的磁碟分割的值的多重步驟查詢
輸入-8 事件中心分割輸出-8 的磁碟分割的事件中心

**查詢︰**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    
如您所見，第二個步驟就會使用**TollBoothId**作為分割索引鍵。 這不相同的第一個步驟，並會因此我們執行隨機要求。 

以下是一些範例與 counterexamples 串流分析工作，都能獲得平行拓撲以及與其潛在的最大的小數位數。 對於不符合其中一個設定檔的工作，將會進行詳細說明如何編碼不按比例縮放的其他標準串流分析案例未來的更新。

現在請使用下列的一般指導方針的︰

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>計算串流工作的單位的最大值
可讓資料流分析工作的串流單位的總數取決於數字的工作和數字的磁碟分割區的每一個步驟所定義的查詢中的步驟。

### <a name="steps-in-a-query"></a>在查詢中的步驟
查詢可以有一或多個步驟。 每一個步驟是子查詢使用**與**關鍵字定義。 超出**與**關鍵字只查詢也會計算步驟，例如**SELECT**陳述式中下列查詢︰

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

上一個查詢有兩個步驟。

> [AZURE.NOTE] 在本文稍後會解釋這個範例查詢。

### <a name="partition-a-step"></a>分割步驟

分割步驟需要下列條件︰

- 必須分割輸入的來源。 如需詳細資訊，請參閱[事件集線器程式設計指南](../event-hubs/event-hubs-programming-guide.md)。
- **SELECT**陳述式的查詢必須讀取分割輸入來源。
- 必須**磁碟分割的**關鍵字查詢中的步驟。

當已分割查詢時，輸入的事件會處理和彙總中的另一個磁碟分割群組，並為每個群組產生輸出事件。 如果需要合併彙總，您必須建立第二個以上的步驟進行彙總。

### <a name="calculate-the-max-streaming-units-for-a-job"></a>計算串流單位工作的最大值

所有非分割步驟一起可以調整串流分析工作最多六個串流單位。 若要新增其他串流單位步驟必須分割。 每個資料分割可以有六個串流單位。

<table border="1">
<tr><th>工作的查詢</th><th>最大值串流單位為您的工作</th></td>

<tr><td>
<ul>
<li>查詢中包含一個步驟。</li>
<li>未分割的步驟。</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>輸入的資料流分割為 3。</li>
<li>查詢中包含一個步驟。</li>
<li>已分割的步驟。</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>查詢中包含兩個步驟。</li>
<li>分割這兩個步驟。</li>
</ul>
</td>
<td>6</td></tr>



<tr><td>
<ul>
<li>資料串流輸入分割為 3。</li>
<li>查詢中包含兩個步驟。 [輸入] 步驟中的分割並不是第二個步驟。</li>
<li>SELECT 陳述式讀取分割輸入。</li>
</ul>
</td>
<td>24 (如分割步驟 18) + 6 的非分割的步驟</td></tr>
</table>

### <a name="examples-of-scale"></a>縮放比例的範例
下列查詢會計算汽車通過免付費電話具有站三分鐘視窗中的三個 tollbooths 的數。 此查詢可以縮放至六個串流單位。

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

若要查詢的使用更多串流單位，兩個資料流輸入，必須分割查詢。 假設資料串流磁碟分割設定為 3，可以最 18 串流單位縮放下列修改的查詢︰

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

當已分割的查詢時，輸入的事件處理和分割] 群組中的彙總。 每個群組，也會產生輸出事件。 分割可能會導致某些非預期的結果時 [**群組依據**] 欄位中的資料串流輸入分割索引鍵。 例如，在先前範例查詢中的**TollBoothId**欄位不 Input1 分割鍵。 可以在多個資料分割散佈亭 #1 的資料。

每個 Input1 磁碟分割區以資料流分析會分別處理，才會顯示多筆記錄的相同的 [解決] 視窗中相同的亭的汽車傳遞到計數。 如果您無法變更的輸入磁碟分割鍵，就可以解決此問題，例如新增額外的非磁碟分割步驟，以︰

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

此查詢可以調整為 24 串流單位。

>[AZURE.NOTE] 如果您要加入兩個資料流時，請確定資料流分割方式來執行聯結，，與您有兩個資料流中的數相同的磁碟分割區的資料行分割索引鍵。


## <a name="configure-stream-analytics-job-partition"></a>設定資料流分析工作分割

**若要調整工作串流單位**

1. [管理入口網站](https://manage.windowsazure.com)登入。
2. 在左窗格中，按一下 [**資料流分析**。
3. 按一下您想要不按比例縮放串流分析工作]。
4. 按一下 [**縮放比例**頂端的頁面]。

![Azure 資料流分析串流單位縮放比例][img.stream.analytics.streaming.units.scale]

在 Azure 入口網站，可以在 [設定存取縮放設定︰

![Azure 入口網站串流分析工作設定][img.stream.analytics.preview.portal.settings.scale]

## <a name="monitor-job-performance"></a>監視工作的效能

使用 [管理] 入口網站，您可以追蹤中工作的事件/第二︰

![Azure 資料流分析監視作業][img.stream.analytics.monitor.job]

計算中的工作負載的預期處理量事件秒。 如果處理量小於比預期，微調輸入磁碟分割、 微調查詢，並將其他串流單位加入您的工作。

## <a name="stream-analytics-throughput-at-scale---raspberry-pi-scenario"></a>在 [縮放比例-覆盆子 Pi 案例串流分析處理量


若要瞭解如何串流分析工作不按比例縮放處理傳送量龐大的一般案例中跨多個串流單位，以下是將事件] 中心傳送感應器資料 （用戶端）、 程序，並會傳送通知或統計資料輸出到另一個事件集線器實驗。

用戶端會傳送到事件合成感應器資料串流分析 JSON 格式及資料輸出必須使用 JSON 格式。  以下是範例資料的外觀類似 –  

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

查詢: 「 傳送通知時精簡已 」  

    SELECT AVG(lght),
     “LightOff” as AlertText
    FROM input TIMESTAMP
    BY devicetime
     WHERE
        lght< 0.05 GROUP BY TumblingWindow(second, 1)

在這個內容處理量測量處理能力︰ 是時間的輸入資料流分析處理在固定 （10 分鐘） 內的資料量。 若要達到最佳處理傳送量輸入資料，兩個資料流輸入，必須分割查詢。 也**count （）**會包含在測量處理多少輸入的事件的查詢。 若要確保工作不只等待輸入更多的事件，輸入事件中樞的每個資料分割已預先載入足夠的輸入資料 (約 300 MB)。  

以下是結果，並增加串流單位數量和事件集線器中的相對應的磁碟分割字數統計。  

<table border="1">
<tr><th>輸入磁碟分割區</th><th>輸出磁碟分割區</th><th>串流單位</th><th>持續處理量
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4.06 MB/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8.06 MB/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38.32 MB/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172.67 MB/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454.27 MB/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609.69 MB/s</td>
</tr>
</table>

![img.stream.analytics.perfgraph][img.stream.analytics.perfgraph]

## <a name="get-help"></a>取得說明
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。


## <a name="next-steps"></a>後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 
