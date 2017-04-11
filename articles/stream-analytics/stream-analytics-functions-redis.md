<properties
    pageTitle="輸出至 Azure Redis 快取，使用 Azure 函數，從 Azure 資料流分析 |Microsoft Azure"
    description="瞭解如何使用 Azure 函數連線來填入資料流分析工作的輸出 Azure Redis 快取服務匯流排佇列。"
    keywords="資料流，意指快取服務匯流排佇列中"
    services="stream-analytics"
    authors="ryancrawcour"
    manager="jhubbard"
    documentationCenter=""
    />

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/26/2016"
    ms.author="ryancraw"/>

# <a name="how-to-store-data-from-azure-stream-analytics-in-an-azure-redis-cache-using-azure-functions"></a>如何從 Azure 資料流分析的資料儲存在 Azure Redis 快取使用 Azure 函數

Azure 資料流分析，可讓您快速開發和部署低成本解決方案，以取得從裝置、 感應器、 基礎結構和應用程式] 或資料的任何資料流即時深入資訊。 讓各種不同的使用情況下，例如即時管理並監控、 命令和控制項、 網路詐騙偵測、 連線的汽車和有更多。 在許多這種情況下，您可能要儲存資料 outputted Azure 資料流分析，例如 Azure Redis 快取分散式的資料存放區。

假設您屬於的電話公司。 您嘗試偵測 SIM 網路詐騙位置相同的身分識別，同時來自多個通話時間、 但在不同地理位置。 您負責儲存 Azure Redis 快取中的所有可能權利行動電話。 在此部落格，我們提供指導方針如何輕鬆地完成您的工作。 

## <a name="prerequisites"></a>必要條件
完成[即時網路詐騙偵測][ fraud-detection] ASA 的逐步解說

## <a name="architecture-overview"></a>架構概觀
![架構的螢幕擷取畫面](./media/stream-analytics-functions-redis/architecture-overview.png)

前圖所示，可讓資料流分析串流查詢並傳送至輸出輸入的資料。 根據輸出，Azure 函數可以再觸發某些類型的事件。 

在此部落格，我們會著重於 Azure 函數的一部分此管道的郵件，或更明確地說觸發的事件會儲存到快取的權利的資料。
完成[即時網路詐騙偵測]後[fraud-detection]教學課程中，您必須輸入 （事件中樞）、 查詢，與輸出 （blob 儲存體） 已經設定，並執行。 在此部落格，我們會變更要改為使用服務匯流排佇列中的輸出。 之後，我們將 Azure 函數連線到此佇列。 

## <a name="create-and-connect-a-service-bus-queue-output"></a>建立並連接服務匯流排佇列輸出
若要建立服務匯流排佇列中，請依照下列步驟 1 和 2 的 [.NET] 區段中[開始使用服務匯流排佇列][servicebus-getstarted]。
現在讓我們來連線到資料流分析作業舊版的網路詐騙偵測逐步說明中所建立的 [佇列。



1. 在 Azure 入口網站中，移至工作的**輸出**刀，選取 [**新增**頁面的頂端。

    ![新增輸出](./media/stream-analytics-functions-redis/adding-outputs.png)

2. 選擇為**接收****服務匯流排佇列中**，然後依照畫面上的指示進行。 請務必選擇服務匯流排佇列中[開始使用服務匯流排佇列]中所建立的命名空間[servicebus-getstarted]。 當您完成時，請按一下 [右] 按鈕。
3. 指定下列的值︰
    - **事件序列化格式**︰ JSON
    - **編碼**︰ UTF8
    - **格式**︰ 分隔線

4. 按一下 [**建立**] 按鈕，新增這個來源，並確認資料流分析可以成功連線到您的儲存空間帳戶。

5. 在 [**查詢**] 索引標籤，取代目前的查詢以下列。 您在步驟 3 中所建立的輸出名稱取代*[您的服務匯流排名稱]* 。 

    ```    

        SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2

        INTO [YOUR SERVICE BUS NAME]
    
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
    
        WHERE CS1.SwitchNum != CS2.SwitchNum
    
    ```

## <a name="create-an-azure-redis-cache"></a>建立 Azure 意指快取
按照 [.NET] 區段中[使用 Azure Redis 快取如何]建立 Azure Redis 快取[use-rediscache]直到***設定快取用戶端***稱為的區段。
完成之後，您有新的 Redis 快取。 在**所有設定**] 底下選取 [**便捷鍵**和向下***主要的連線字串***的附註]。

![架構的螢幕擷取畫面](./media/stream-analytics-functions-redis/redis-cache-keys.png)

## <a name="create-an-azure-function"></a>建立一個 Azure 函數
請依照[建立您的第一個 Azure 函數][functions-getstarted]教學課程，瞭解如何開始使用 Azure 函數。 如果您已經有您想要使用的 Azure 函數，然後跳到[寫入 Redis 快取](#Writing-to-Redis-Cache)

1. 在入口網站，從左側導覽畫面中，選取 [應用程式服務，然後按一下您的 Azure 函數應用程式名稱，以取得函數的應用程式的網站。
    ![應用程式服務函數清單的螢幕擷取畫面](./media/stream-analytics-functions-redis/app-services-function-list.png)

2. 按一下 [**新函數 > ServiceBusQueueTrigger – C#**。 下列欄位，請遵循以下指示︰
    - **佇列名稱**︰ 輸入當您在[開始使用服務匯流排佇列]佇列中的名稱相同的名稱[servicebus-getstarted]（非名稱服務匯流排）。 請確定您使用的已連線至資料流分析輸出佇列。
    - **服務匯流排連線**︰ 選取 [**新增連線字串**。 若要尋找的連接字串，請移至 [傳統] 入口網站，請選取**服務匯流排**、 服務匯流排您建立了，在畫面底部的**連線資訊**。 請確定您是在此頁面上的主畫面上。 複製並貼上的連接字串。 請隨意輸入任何連線名稱。
    
        ![服務匯流排連線的螢幕擷取畫面](./media/stream-analytics-functions-redis/servicebus-connection.png)
    - **AccessRights**︰ 選擇 [**管理**


3. 按一下 [**建立**]

## <a name="writing-to-redis-cache"></a>寫入 Redis 快取
現在，我們已建立讀取服務匯流排佇列 Azure 函數。 所有就是使用我們的函數撰寫此資料至 Redis 快取。 

1. 選取您剛建立的**ServiceBusQueueTrigger**，然後在右上角中按一下**函數應用程式設定**。 選取 [**移至 [應用程式服務設定] > 設定 > 應用程式設定**

2. 在 [連線字串] 區段中，建立 [**名稱**] 區段中的名稱。 貼上您在**建立 Redis 的快取**步驟，將 [**值**] 區段中找到的主要的連接字串。 選取 [**自訂**位置顯示**SQL 資料庫**]。

3. 按一下頂端的 [**儲存**]。

    ![服務匯流排連線的螢幕擷取畫面](./media/stream-analytics-functions-redis/function-connection-string.png)

4. 現在回到應用程式服務設定]，然後選取 [**工具 > 應用程式服務編輯器 （預覽版本） > 在 > 移**。

    ![服務匯流排連線的螢幕擷取畫面](./media/stream-analytics-functions-redis/app-service-editor.png)

5. 在您所選擇的編輯器中，建立一個名為**project.json**以下列的 JSON 檔案並將其儲存至本機磁碟。

        {
            "frameworks": {
                "net46": {
                    "dependencies": {
                        "StackExchange.Redis":"1.1.603",
                        "Newtonsoft.Json": "9.0.1"
                    }
                }
            }
        }

6. 此檔案上傳到您的函數 (不 WWWROOT) 的根目錄。 出現自動命名**project.lock.json**的檔案，請確認 Nuget 封裝 「 StackExchange.Redis 」 和 「 Newtonsoft.Json 」 都已匯入時，您應該會看到。

7. 在**run.csx**檔案，取代下列程式碼中預先產生的程式碼。 LazyConnection 函數中用您在步驟 2**到意指快取資料的儲存**建立的名稱來取代 「 連線名稱 」。

````

    using System;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;
    
    public static void Run(string myQueueItem, TraceWriter log)
    {
        log.Info($"Function processed message: {myQueueItem}");

        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = Connection.GetDatabase();
        log.Info($"Created database {db}");
    
        // Parse JSON and extract the time
        var message = JsonConvert.DeserializeObject<dynamic>(myQueueItem);
        string time = message.time;
        string callingnum1 = message.callingnum1;

        // Perform cache operations using the cache object...
        // Simple put of integral data types into the cache
        string key = time + " - " + callingnum1;
        db.StringSet(key, myQueueItem);
        log.Info($"Object put in database. Key is {key} and value is {myQueueItem}");

        // Simple get of data types from the cache
        string value = db.StringGet(key);
        log.Info($"Database got: {value}"); 
    }

    // Connect to the Service Bus
    private static Lazy<ConnectionMultiplexer> lazyConnection = 
        new Lazy<ConnectionMultiplexer>(() =>
            {
                var cnn = ConfigurationManager.ConnectionStrings["CONN NAME"].ConnectionString
                return ConnectionMultiplexer.Connect();
            });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

````

## <a name="start-the-stream-analytics-job"></a>啟動資料流分析作業

1. 啟動 telcodatagen.exe 應用程式。 使用狀況如下所示︰````telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]````

2. 從資料流分析工作刀在入口網站中，按一下 [在頁面頂端的 [**開始**]。

    ![開始工作的螢幕擷取畫面](./media/stream-analytics-functions-redis/starting-job.png)

3. 在**啟動工作**的刀出現，選取 [**立即**，然後按一下畫面底部的 [**開始**] 按鈕。 工作狀態的變更來啟動和執行的一些時間變更後。
 
    ![開始工作階段選取的螢幕擷取畫面](./media/stream-analytics-functions-redis/start-job-time.png)

## <a name="run-solution-and-check-results"></a>執行方案，並檢查結果
回到您**ServiceBusQueueTrigger**的頁面，您現在應該會看到記錄陳述式。 這些記錄會顯示您從服務匯流排佇列中，將它放入資料庫，取得項目，並出索引鍵使用時間中擷取 ！

若要驗證您的資料是在您意指快取中，移至您意指快取頁面新的入口網站 （如前述[建立 Azure Redis 快取](#Create-an-Azure-Redis-Cache)步驟中所示），然後選取主控台。

您現在可以撰寫意指確認資料實際上是快取中的命令。

![意指主控台的螢幕擷取畫面](./media/stream-analytics-functions-redis/redis-console.png)

## <a name="next-steps"></a>後續步驟
我們很棒的新項目 Azure 函數串流分析如何在一起，並希望此解除為您的新的可能性。 如果您有任何您想要接下來的意見，歡迎使用[Azure UserVoice 網站](https://feedback.azure.com/forums/270577-stream-analytics)。

如果您是新的 Microsoft Azure，邀請您試試看，註冊[免費的 Azure 試用帳戶](https://azure.microsoft.com/pricing/free-trial/)。 如果您是新資料流分析，再邀請您建立[您的第一個資料流分析作業](stream-analytics-create-a-job.md)。

如果您需要任何說明] 或 [ [MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)或[Stackoverflow](http://stackoverflow.com/questions/tagged/azure-stream-analytics)論壇上張貼有問題。 

您也可以參閱下列資源︰

- [Azure 函數開發人員參考](../azure-functions/functions-reference.md)
- [Azure 函數 C# 開發人員參考](../azure-functions/functions-reference-csharp.md)
- [Azure 函數 F # 開發人員參考](../azure-functions/functions-reference-fsharp.md)
- [Azure 函數 NodeJS 開發人員參考](../azure-functions/functions-reference.md)
- [Azure 函數引動程序及連結](../azure-functions/functions-triggers-bindings.md)
- [如何監視 Azure Redis 快取](../redis-cache/cache-how-to-monitor.md)

若要保持最新版本上所有的最新消息和功能，請依照[@AzureStreaming](https://twitter.com/AzureStreaming)Twitter 上。


[fraud-detection]: stream-analytics-real-time-fraud-detection.md
[servicebus-getstarted]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[use-rediscache]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md
[functions-getstarted]: ../azure-functions/functions-create-first-azure-function.md
