<properties
 pageTitle="排程器概念、 詞彙及實體 |Microsoft Azure"
 description="Azure 排程器概念、 詞彙及實體階層，包括工作和工作集合。  顯示完整排程工作的範例。"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="get-started-article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="scheduler-concepts-terminology--entity-hierarchy"></a>排程器概念、 詞彙，+ 實體階層

## <a name="scheduler-entity-hierarchy"></a>排程器實體階層

下表說明公開或使用的排程器 API 的主要資源︰

|資源 | 描述 |
|---|---|
|**工作集合**|工作集合包含的工作群組，並維護設定、 配額和節流共用的集合中的工作。 工作集合會建立訂閱的擁有者，並群組根據分派狀況] 或 [應用程式範圍的工作。 它會限制為一個區域。 您也可以強制執行配額限制該集合中的所有工作的使用方式。 配額包含 MaxJobs 及 MaxRecurrence。|
|**工作**|工作定義單一循環動作，以執行簡單或複雜的策略。 動作可能包含 HTTP、 儲存空間佇列、 服務匯流排佇列中或服務匯流排主題要求。|
|**工作歷程記錄**|工作歷程記錄代表執行工作的詳細資料。 包含成功與失敗，以及任何回應詳細資料。|

## <a name="scheduler-entity-management"></a>排程器實體管理

在高的層級，排程器，服務管理 API 公開的資源下列作業︰

|功能|描述和 URI 地址|
|---|---|
|**工作管理**|取得，放，然後刪除支援建立及修改工作集合，其中包含的工作。 工作集合是容器的工作和對應至配額和共用的設定。 稍後所述的配額的範例是最大的數字的工作和最小的循環間隔。 <p>放置並刪除︰`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p><p>取得︰`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p>
|**工作管理**|取得，將、 文章、 更新，及刪除支援建立和修改的工作。 所有的工作必須屬於工作集合已存在，因此沒有隱含的建立。 <p>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`</p>|
|**工作歷程記錄管理**|取得支援擷取工作執行歷程記錄，例如工作經過的時間和工作的執行結果的 60 天。 新增查詢字串參數支援篩選依據的狀態和狀態。 <P>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`</p>|

## <a name="job-types"></a>工作類型

有多個類型的工作︰ HTTP 工作 （包括支援 SSL 的 HTTPS 作業）、 儲存空間佇列工作、 服務匯流排佇列工作和服務匯流排主題工作。 HTTP 工作是理想如果您有現有的工作量或服務的端點。 您可以使用儲存空間佇列工作張貼郵件儲存空間佇列中，讓這些工作非常適合使用儲存空間佇列的工作量。 同樣地，服務匯流排工作非常適合使用服務匯流排佇列 」 和 「 主題的工作量。

## <a name="the-job-entity-in-detail"></a>「 工作 」 實體的詳細資料

在基本的層級，排程的工作會有幾個部分︰

- 當工作計時器啟動時執行的動作  

- （選用）若要執行的工作時間  

- （選用）何時及如何通常重複的工作  

- （選用）如果主要動作無法啟動動作  

在內部，排程的作業也包含系統提供的資料，例如 [下一步排程的執行時間。

下列程式碼提供排程工作的完整的範例。 下列各節會提供詳細資料。

    {
        "startTime": "2012-08-04T00:00Z",               // optional
        "action":
        {
            "type": "http",
            "retryPolicy": { "retryType":"none" },
            "request":
            {
                "uri": "http://contoso.com/foo",        // required
                "method": "PUT",                        // required
                "body": "Posting from a timer",         // optional
                "headers":                              // optional

                {
                    "Content-Type": "application/json"
                },
            },
           "errorAction":
           {
               "type": "http",
               "request":
               {
                   "uri": "http://contoso.com/notifyError",
                   "method": "POST",
               },
           },
        },
        "recurrence":                                   // optional
        {
            "frequency": "week",                        // can be "year" "month" "day" "week" "minute"
            "interval": 1,                              // optional, how often to fire (default to 1)
            "schedule":                                 // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]
            },
            "count": 10,                                 // optional (default to recur infinitely)
            "endTime": "2012-11-04",                     // optional (default to recur infinitely)
        },
        "state": "disabled",                           // enabled or disabled
        "status":                                       // controlled by Scheduler service
        {
            "lastExecutionTime": "2007-03-01T13:00:00Z",
            "nextExecutionTime": "2007-03-01T14:00:00Z ",
            "executionCount": 3,
                                                "failureCount": 0,
                                                "faultedCount": 0
        },
    }

如同的上述範例排程的工作，工作定義有多個部分︰

- 開始時間 （「 開始時間 」）  

- 動作 （」 動作 」），其中包含錯誤的動作 (「 errorAction 」)

- 週期性 （「 週期 」）  

- 狀態 （「 狀態 」）  

- 狀態 （「 狀態 」）  

- 再試一次原則 (「 retryPolicy 」)  

現在就讓我們逐行查看每個的詳細資料︰

## <a name="starttime"></a>開始時間

[開始時間] 是開始時間，並允許來電者指定的時間位移[ISO 8601](http://en.wikipedia.org/wiki/ISO_8601)格式的網路上。

## <a name="action-and-erroraction"></a>巨集指令和 errorAction

[執行] 來啟動的每個項目] 動作，並說明一種服務叫用。 動作是什麼提供的排程將會執行。 排程器支援 HTTP、 儲存佇列、 服務匯流排主題及服務匯流排佇列中的動作。

上述範例中的動作是 HTTP 動作。 以下是儲存佇列中動作的範例︰

    {
            "type": "storageQueue",
            "queueMessage":
            {
                "storageAccount": "myStorageAccount",  // required
                "queueName": "myqueue",                // required
                "sasToken": "TOKEN",                   // required
                "message":                             // required
                    "My message body",
            },
    }

以下是服務匯流排主題動作的範例。

  「 動作 」: {「 type 」: 「 serviceBusTopic 「，」 serviceBusTopicMessage 」: {「 topicPath 」: 「 t1 」，  
      「 命名空間 」: 「 mySBNamespace 「，」 transportType 」: 「 netMessaging 」，/ / 可以 netMessaging 或 AMQP 「 驗證 」: {「 sasKeyName 」: 「 QPolicy 」，「 類型 」: 「 sharedAccessKey 」}，「 郵件 」: 「 一些郵件 」，「 brokeredMessageProperties 」: {}、 「 customMessageProperties 」: {」 應用程式名稱 」: 「 FromScheduler 」}}，}

以下是服務匯流排佇列中動作的範例︰


  「 動作 」: {「 serviceBusQueueMessage 」: {「 queueName 」: 「 q1 」，  
      「 命名空間 」: 「 mySBNamespace 「，」 transportType 」: 「 netMessaging 」，/ / 可以 netMessaging 或 AMQP 「 驗證 」: {  
        「 sasKeyName 」: 「 QPolicy 」，「 type 」: 「 sharedAccessKey 」}，「 郵件 」: 「 一些郵件 」，  
      「 brokeredMessageProperties 」: {}、 「 customMessageProperties 」: {」 應用程式名稱 」: 「 FromScheduler 」}}，「 type 」: 「 serviceBusQueue 」}

「 ErrorAction 」 是錯誤處理常式叫用的主要動作失敗時的動作。 您可以使用此變數呼叫端點錯誤處理或傳送使用者通知。 這可以用於不讓其與主要無法使用 （例如，如果在網站上的端點損毀） 的大小寫在第二個端點，或可以用於通知錯誤處理結束點。 就像主要的動作，錯誤動作可根據採取其他動作的簡單或複合索引邏輯。 若要瞭解如何建立 SA 權杖，請參閱[建立及使用共用的 Access 簽章](https://msdn.microsoft.com/library/azure/jj721951.aspx)。

## <a name="recurrence"></a>循環

循環具有多個部分︰

- 頻率︰ 其中一項分、 小時、 天、 週、 月、 年  

- 循環指定的頻率間隔︰ 間隔  

- 預定的排程︰ 指定分鐘、 小時、 工作天、 月數和 monthdays 循環  

- 計數︰ 字數統計的項目  

- 結束時間︰ 後指定的結束時間，會執行任何工作  

如果有指定其 JSON 定義中的週期性物件，為週期性工作。 如果指定計算和結束時間，完成規則的第一次就會生效。

## <a name="state"></a>狀態

工作狀態是四個值的其中一個︰ 啟用、 停用、 完成或發生錯誤。 您可以將或修補程式工作，以便進行更新啟用或停用狀態。 如果工作已經完成或發生錯誤，則無法更新 （但仍可刪除作業） 最後狀態。 [狀態] 屬性的範例如下所示︰


        "state": "disabled", // enabled, disabled, completed, or faulted
60 天後，會刪除已完成與錯誤的工作。

## <a name="status"></a>狀態

一旦開始排程器工作，資訊就會傳回相關工作的目前狀態。 這個物件不是由使用者進行設定，它由系統設定。 不過，它會包含在工作物件 （而不是個別的連結的資源），使一個可以輕鬆地取得工作狀態。

工作狀態包含時間的前一個執行 （如果有的話）]、 [下一個排程執行 （適用於進行中工作） 的時間，以及執行計數的工作。

## <a name="retrypolicy"></a>retryPolicy

如果排程器工作失敗，可能是指定重試原則，以決定是否及如何動作重試一次。 這由**retryType**物件，將其設定為 [**無如果沒有重試原則，如上所示**。 如果有重試原則，請將它設定為**固定**。

若要設定重試原則，指定兩個額外的設定︰ 重試間隔 (**retryInterval**) 和次數 (**retryCount**) 的值。

重試間隔，指定**retryInterval**物件時，會重試之間的間隔。 預設值為 30 秒、 其最小的可設定值 15 秒數，而其最大的值會 18 個月。 在 [可用的工作集合中的工作有小可設定值為 1 小時。  定義 ISO 8601 格式。 同樣地， **retryCount**物件; 指定的次數值則會在嘗試重試的次數。 預設值為 4，且最大值為 20\.兩者**retryInterval**和**retryCount**是選擇性的。 授與他們如果**retryType**設定為 [**固定**的值和沒有值明確指定其預設值。

## <a name="see-also"></a>另請參閱

 [什麼是排程器？](scheduler-intro.md)

 [開始使用 Azure 入口網站中的排程器](scheduler-get-started-portal.md)

 [方案與帳單方面的 Azure 排程器](scheduler-plans-billing.md)

 [如何建立複雜的排程和進階的循環 Azure 排程器](scheduler-advanced-complexity.md)

 [Azure 排程器 REST API 參照](https://msdn.microsoft.com/library/mt629143)

 [Azure 排程器 PowerShell cmdlet 參考](scheduler-powershell-reference.md)

 [Azure 排程器高可用性和可靠性](scheduler-high-availability-reliability.md)

 [Azure 排程器限制與預設值]，出現錯誤碼](scheduler-limits-defaults-errors.md)

 [Azure 排程器輸出驗證](scheduler-outbound-authentication.md)
