<properties
    pageTitle="即時 Twitter 舉動分析資料流分析 |Microsoft Azure"
    description="瞭解如何使用即時 Twitter 舉動分析的資料流分析。 逐步指引事件產生即時儀表板上的資料。"
    keywords="即時 twitter 趨勢分析、 舉動分析、 社交媒體分析趨勢分析的範例"
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
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="social-media-analysis-real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>社交媒體分析︰ 即時 Twitter 舉動分析 Azure 資料流狀況分析

瞭解如何建立舉動分析解決方案社交媒體分析帶入 Azure 事件集線器即時 Twitter 事件。 您所撰寫的 Azure 資料流分析查詢，來分析資料。 您會然後儲存更新閱覽的結果或使用儀表板和[Power BI](https://powerbi.com/) ，提供即時深入資訊。

社交媒體分析工具可協助組織瞭解趨勢的主題，也就是主旨及態度的社交媒體中有大量的文章。 舉動分析，也稱為*意見採礦*，使用社交媒體分析工具，來判斷態度趨近於產品、 想法，依此類推。 即時 Twitter 趨勢分析是很好的範例，因為 # 標籤訂閱模型可讓您以聆聽特定的關鍵字，以及開發摘要舉動分析。

## <a name="scenario-sentiment-analysis-in-real-time"></a>案例︰ 舉動分析即時

公司的新聞媒體網站，為獲得其競爭對手的優點，是其讀者立即相關的網站內容。 公司使用社交媒體分析執行即時舉動 Twitter 資料分析與讀者相關的主題。 具體來說，要找出趨勢即時 Twitter 上的主題，公司會需要的重要的主題相關的推文音量和舉動即時分析。 因此，基本上，需要為舉動分析分析引擎會根據摘要此社交媒體的。

## <a name="prerequisites"></a>必要條件
-   Twitter 帳戶與[OAuth 存取權杖](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
-   從 Microsoft 下載中心[TwitterClient.zip](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip)
-   可省略︰ 來源[GitHub](https://aka.ms/azure-stream-analytics-twitterclient) twitter 用戶端程式碼

## <a name="create-an-event-hub-input-and-a-consumer-group"></a>建立輸入事件中心和消費者群組

範例應用程式會產生的事件，並傳送至事件集線器執行個體 （事件] 中心內，簡稱）。 服務匯流排事件集線器是慣用的事件 ingestion 串流分析方法。 請參閱事件集線器[服務匯流排文件](/documentation/services/service-bus/)中的文件。

您可以使用下列步驟來建立事件中心。

1.  在 Azure 入口網站中，按一下 [**新增** > **應用程式服務** > **服務匯流排** > **事件中心** > **快速建立**，並提供的名稱、 地區和新的或現有的命名空間。  
2.  最佳作法是每一個資料流分析工作應該從單一事件集線器消費者群組]。 我們會逐步引導您完成建立消費者群組稍後的程序。 您可以進一步瞭解消費者群組在[Azure 事件集線器概觀](https://msdn.microsoft.com/library/azure/dn836025.aspx)。 若要建立消費者群組，請移至新建立的事件] 中心內，按一下 [**消費者群組**] 索引標籤，按一下 [**建立**]，底部的頁面]，然後提供您消費者群組的名稱。
3.  若要授與存取權 [事件] 中心內，我們需要建立共用的 access 原則。 按一下 [程式事件] 中心中的 [**設定**] 索引標籤。
4.  在 [**共用存取原則**] 底下，建立新原則有**管理**權限。

    ![共用 Access 原則，您可以在其中建立與管理權限原則。](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-ananlytics-shared-access-policies.png)

5.  按一下 [在頁面底部的 [**儲存**]。
6.  移至 [**儀表板**，按一下 [**連線資訊**]，底部的頁面，然後複製並儲存的連線資訊。 （使用 [複製] 圖示，會出現在 [搜尋] 圖示）。

## <a name="configure-and-start-the-twitter-client-application"></a>設定和啟動 Twitter 用戶端應用程式

我們提供會連線到 Twitter 資料，透過[Twitter 的串流 Api](https://dev.twitter.com/streaming/overview)收集貼入 Tweet 事件的相關主題的參數化設定用戶端應用程式。 [ [Sentiment140](http://help.sentiment140.com/) ] 開啟來源工具會用於為每個推文指定舉動值。

- 0 = 負數
- 2 = 中性
- 4 = 正數

然後，貼入 Tweet 事件會放入 [事件] 中心。  

請遵循下列步驟，設定應用程式︰

1.  [下載 TwitterClient 解決方案](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip)。
2.  開啟 TwitterClient.exe.config，並取代 oauth_consumer_key、 oauth_consumer_secret、 oauth_token 及 oauth_token_secret Twitter 權杖的值。  

    [若要產生 OAuth 存取權杖的步驟](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  

    請注意，您需要進行產生的權杖空的應用程式。  
3.  取代 EventHubConnectionString 和 EventHubName 中的值 TwitterClient.exe.config 連線字串與您的事件中樞的名稱。 您先前複製連線字串可讓您同時連線字串] 和 [事件] 中心內的名稱，因此請務必隔開並將其正確的欄位中。 例如，請考慮下列連線字串︰

        Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub

    TwitterClient.exe.config 檔案包含您的設定，如下列範例所示︰

        add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
        add key="EventHubName" value="yourhub"

    請注意，請務必文字 」 EntityPath = 」 會__不__會出現在 EventHubName 值。

4.  *可省略︰*調整搜尋關鍵字。  做為預設值，此應用程式會尋找 「 Azure、 Skype、 XBox、 Microsoft，西雅圖 」。  如有需要，您可以調整**twitter_keywords** TwitterClient.exe.config 中的值。
5.  執行 TwitterClient.exe 啟動應用程式。 您會看到貼入 Tweet 事件傳送到您的事件中樞的**CreatedAt**、**主題**及**SentimentScore**值。

    ![舉動分析︰ SentimentScore 傳送至 [事件] 中心內的值。](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-sentiment-output-to-event-hub.png)

## <a name="create-a-stream-analytics-job"></a>建立資料流分析工作

既然貼入 Tweet 事件會從 Twitter 的即時資料流中，我們可以將資料流分析工作設定為分析即時這些事件。

### <a name="provision-a-stream-analytics-job"></a>佈建資料流分析工作

1.  在[Azure 入口網站](https://manage.windowsazure.com/)中，按一下 [**新增** > **資料服務** > **串流分析** > **快速建立**。
2.  指定下列的值，然後再按一下 [**建立資料流分析作業**︰

    * **工作名稱**︰ 輸入作業名稱。
    * **地區**︰ 選取您要執行工作的地區。 請考慮放在同一個區域以確保較佳的效能，並確定，您將不會付款區域間傳送資料的工作及 [事件] 中心。
    * **儲存帳戶**︰ 選擇您想要用來儲存監視資料執行此區域內的所有資料流分析工作 Azure 儲存體帳戶。 您可以選擇現有的儲存空間帳戶或建立新的 pin。

3.  按一下 [清單資料流分析工作的左窗格中的 [**資料流分析**]。  
    ![資料流分析服務圖示](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-service-icon.png)

    新的工作會顯示其狀態是 [**建立]**。 在頁面底部的 [**開始**] 按鈕會停用通知。 您可以開始工作之前，您必須設定工作輸入、 輸出和查詢。


### <a name="specify-job-input"></a>指定工作的輸入
1.  在資料流分析工作中，按一下頂端的頁面上，**輸入**，然後按一下 [**新增輸入**。 [開啟] 對話方塊會引導您的步驟設定您的輸入數字。
2.  按一下 [**資料流**]，然後按一下 [從左至右] 按鈕。
3.  按一下 [**事件] 中心內**，，然後按一下 [從左至右] 按鈕。
4.  輸入或選取在第三個頁面上的下列值︰

    * **輸入 alias （別名)**: 輸入好記的名稱，輸入，例如*TwitterStream*此工作。 請注意，您會使用這個名稱在查詢中更新版本。
    **事件中心**︰ 如果您建立的事件中樞在同一份訂閱，以資料流分析工作，請選取 [命名空間中的 [事件] 中心。

        如果您的事件中心位於不同的訂閱，請按一下 [**使用事件中心從另一個訂閱**，，然後手動輸入資訊**服務匯流排命名空間**、**事件中心名稱**、**事件中心原則名稱**、**事件中心原則索引鍵**，以及**事件中樞的磁碟分割計數**。

    * **事件中心名稱**︰ 選取 [事件] 中心內的名稱。

    * **事件中心原則名稱**︰ 選取您之前在本教學課程中建立的事件中心原則。

    * **事件中心消費者群組**︰ 輸入您之前在本教學課程中建立消費者群組的名稱。
5.  按一下 [從左至右] 按鈕。
6.  指定下列的值︰

    * **事件序列化格式**︰ JSON
    * **編碼**︰ UTF8

7.  按一下 [**檢查**] 按鈕來新增此來源，並確認資料流分析，可以成功連線到 [事件] 中心。

### <a name="specify-job-query"></a>指定工作的查詢

資料流分析支援簡單的宣告查詢的模型，描述轉換。 若要進一步瞭解語言，請參閱[Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)。  本教學課程中會協助您撰寫及測試 Twitter 資料的多個查詢。

#### <a name="sample-data-input"></a>範例資料輸入

若要驗證對實際工作資料的查詢，您可以使用**範例資料**功能從您的資料流擷取事件，並建立的事件.json 檔案以進行測試。

1.  選取 [事件] 中心內輸入，然後按一下 [在頁面底部的**範例資料**。
2.  在開啟的對話方塊中，指定**開始時間**開始收集資料和**持續時間**到消耗多少其他資料。
3.  按一下 [**詳細資料**] 按鈕，然後按一下 [**按一下這裡**] 連結，若要下載並儲存產生的.json 檔案。

#### <a name="pass-through-query"></a>傳遞查詢
若要開始，我們會執行簡單的傳遞查詢事件中的所有欄位的專案。

1.  按一下**查詢**資料流分析的 [工作] 頁面的頂端。
2.  在 [程式碼編輯器取代初始查詢範本下列動作︰

        SELECT * FROM TwitterStream

    請確定輸入來源的名稱符合您先前指定的輸入名稱。

3.  在查詢編輯器] 底下，按一下 [**測試**]。
4.  移至您的範例.json 檔案。
5.  按一下 [**檢查**] 按鈕，請參閱下方查詢定義結果。

    ![查詢定義的下方顯示的結果](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sentiment-by-topic.png)

#### <a name="count-of-tweets-by-topic-tumbling-window-with-aggregation"></a>傳出 tweets 主題的計數︰ 解決視窗彙總

若要比較提及主題之間的數字，我們將使用[TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx)主題每五秒取得提及的計數。

1.  變更程式碼編輯器中的查詢︰

        SELECT System.Timestamp as Time, Topic, COUNT(*)
        FROM TwitterStream TIMESTAMP BY CreatedAt
        GROUP BY TUMBLINGWINDOW(s, 5), Topic

    此查詢會使用**時間戳記的**關鍵字裝載暫時計算使用中指定的時間戳記欄位。 如果未指定此欄位，想要使用的時間，每項事件到達 [事件] 中心執行視窗作業。  瞭解更多[資料流分析查詢參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)的 「 送達時間與應用程式時間 」 區段中。

    此查詢也會使用**System.Timestamp**屬性來存取時間戳記的每一個視窗的結尾。

2.  在查詢編輯器] 若要查看查詢結果中按一下 [**重新執行**]。

#### <a name="identify-trending-topics-sliding-window"></a>找出趨勢主題︰ 滑動視窗

若要找出趨勢的主題，我們會查看交互臨界值的 「 提及 」 在指定的時間內的主題。 在進行本教學課程中，我們會檢查最後五個秒中所提及的多個 20 時間使用[SlidingWindow](https://msdn.microsoft.com/library/azure/dn835051.aspx)的主題。

1.  變更程式碼編輯器中的查詢︰ 將 System.Timestamp 選取為 [時間] 主題中，COUNT (*) 為提及從 TwitterStream 時間戳記的 CreatedAt 群組以 SLIDINGWINDOW(s, 5)，不必計算的主題 (*) > 20

2.  在查詢編輯器] 若要查看查詢結果中按一下 [**重新執行**]。

    ![將視窗查詢輸出](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-query-output.png)

#### <a name="count-of-mentions-and-sentiment-tumbling-window-with-aggregation"></a>計數為提及和舉動︰ 解決視窗彙總
我們會測試最終查詢使用**TumblingWindow**取得提及、 平均值、 最小值、 最大值] 及舉動成績的標準差的數字，為每個主題每五秒。

1.  變更程式碼編輯器中的查詢︰

        SELECT System.Timestamp as Time, Topic, COUNT(*), AVG(SentimentScore), MIN(SentimentScore),
        Max(SentimentScore), STDEV(SentimentScore)
        FROM TwitterStream TIMESTAMP BY CreatedAt
        GROUP BY TUMBLINGWINDOW(s, 5), Topic

2.  在查詢編輯器] 若要查看查詢結果中按一下 [**重新執行**]。
3.  這是我們會使用我們的儀表板的查詢。  按一下 [在頁面底部的 [**儲存**]。


## <a name="create-output-sink"></a>建立輸出接收

現在，我們已定義事件資料流時，輸入要內嵌事件，以及執行資料流，轉換的查詢事件中樞的最後一個步驟就是定義為工作輸出接收。  我們會寫入工作查詢彙總貼入 tweet 事件至 Azure Blob 儲存體。  您也可以推結果到 Azure SQL 資料庫，Azure 資料表儲存體，或事件集線器，[根據特定應用程式的需要。

若要建立的容器 Blob 儲存體中，使用下列步驟，如果您還沒有其中一個︰

1.  使用現有的儲存空間帳戶或建立新的儲存空間帳戶的 [**新增** > **資料服務** > **儲存** > **快速建立**，然後下列畫面上的指示。
2.  選取的儲存空間帳戶，按一下 [**容器**頂端的頁面]，再按一下 [**新增**。
3.  指定您容器的**名稱**並將其**存取****公用 Blob**。

## <a name="specify-job-output"></a>指定工作輸出

1.  在資料流分析工作中，按一下 [**輸出**頂端的頁面]，然後按一下**新增輸出。** [開啟] 對話方塊會逐步引導您完成設定您的輸出的幾個步驟。
2.  按一下 [ **BLOB 儲存體**]，然後按一下 [從左至右] 按鈕。
3.  輸入或選取在第三個頁面上的下列值︰

    * **輸出 alias （別名)**: 輸入此工作輸出易記的名稱。

    * **訂閱**︰ 如果您建立 Blob 儲存體在同一份訂閱，以資料流分析工作，請按一下 [**使用目前的訂閱的儲存空間帳戶**。 如果您儲存在不同的訂閱，請按一下 [**使用另一個訂閱的儲存空間帳戶**，並手動輸入資訊**儲存帳戶**、**儲存帳戶金鑰]**，以及**容器**。

    * **儲存帳戶**︰ 選取的儲存體帳戶名稱。

    * **容器**︰ 選取容器的名稱。

    * **檔案名稱首碼**︰ 輸入寫入 blob 輸出時要使用的檔案首碼。

4.  按一下 [從左至右] 按鈕。
5.  指定下列的值︰
    * **事件序列化格式**︰ JSON
    * **編碼**︰ UTF8
6.  按一下 [**檢查**] 按鈕，新增這個來源，並確認資料流分析可以成功連線到您的儲存空間帳戶。

## <a name="start-job"></a>啟動工作

因為工作輸入查詢，與輸出所有已指定，我們已準備好開始串流分析工作。

1.  從工作的**儀表板**中，按一下 [在頁面底部的 [**開始**]。
2.  在開啟的對話方塊中，按一下**工作的開始時間**]，然後按一下對話方塊底部的 [**檢查**] 按鈕。 工作狀態會變更為**開始**，並會很快就會變更為**執行**。


## <a name="view-output-for-sentiment-analysis"></a>檢視輸出舉動分析

您的工作正在執行及處理即時 Twitter 串流之後，選擇您要檢視舉動分析的輸出的方式。 若要檢視您的工作輸出中進行即時使用的工具，例如[Azure 儲存檔案總管](https://azurestorageexplorer.codeplex.com/)或[Azure 檔案總管]](http://www.cerebrata.com/products/azure-explorer/introduction) 。 從這裡開始，您可以使用[Power BI](https://powerbi.com/)延伸至包含自訂的儀表板，類似以下的螢幕擷取畫面的應用程式。

![社交媒體分析︰ 在 Power BI 儀表板串流分析舉動分析 （意見採礦） 輸出。](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-output-power-bi.png)

## <a name="get-support"></a>取得支援
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。


## <a name="next-steps"></a>後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)
