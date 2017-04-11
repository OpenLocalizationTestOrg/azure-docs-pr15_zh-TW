<properties 
    pageTitle="如何將事件記錄到 Azure 事件集線器 Azure API 管理 |Microsoft Azure" 
    description="瞭解如何將事件記錄到 Azure 事件集線器 Azure API 管理。" 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>如何將事件記錄到 Azure 事件集線器 Azure API 管理

Azure 事件集線器是彈性的資料輸入服務，讓您可以處理程序及分析大量資料所產生的連線的裝置及應用程式可以內嵌數百萬秒的事件。 事件集線器做為 「 大門 」 的事件管道的郵件，並在之後插入事件集線器收集資料，可以轉換和儲存使用任何即時分析提供者或儲存批次處理介面卡。 事件集線器，讓事件使用者皆可存取自己排程事件，以減少的事件的這些事件，耗用的資料流。

本文是[整合 Azure API 管理事件集線器與](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/)視訊，並說明如何使用 Azure 事件集線器 API 管理事件記錄。

## <a name="create-an-azure-event-hub"></a>建立 Azure 事件中心

若要建立新的事件集線器，請登入[Azure 傳統入口網站](https://manage.windowsazure.com)，按一下 [**新增**->**應用程式服務**->**服務匯流排**->**事件中心**->**快速建立**。 輸入 [事件] 中心名稱，區域，請選取訂閱，然後選取 [命名空間。 如果您還沒有先前建立的命名空間，您可以建立**命名空間**文字方塊中輸入的名稱。 一旦設定所有內容，請按一下 [**建立新的事件中心**建立 [事件] 中心。

![建立事件中心][create-event-hub]

接下來，您的新事件集線器瀏覽至 [**設定**] 索引標籤，並建立兩個**共用的存取原則**。 命名為第一個**傳送**，並為其**傳送**的權限。

![傳送原則][sending-policy]

命名**接收**第二個，讓它**聆聽**權限]，再按一下 [**儲存**]。

![接收原則][receiving-policy]

每個共用的存取原則可讓應用程式，以傳送及接收事件中樞的事件。 若要存取這些原則的連接字串，瀏覽至 [事件] 中心的 [**儀表板**] 索引標籤，然後按一下 [**連線資訊**]。

![連線字串][event-hub-dashboard]

**傳送**連線字串使用記錄事件時，**接收**連線字串是時從 [事件] 中心下載事件。

![連線字串][event-hub-connection-string]

## <a name="create-an-api-management-logger"></a>建立 API 管理記錄器

現在您有一個事件集線器下, 一步就是在您的 API 管理服務設定[記錄器](https://msdn.microsoft.com/library/azure/mt592020.aspx)，以便事件記錄事件中心。

使用[API 管理 REST API](http://aka.ms/smapi)設定 API 管理記錄程式。 第一次使用 REST API 之前, 檢閱[必要條件](https://msdn.microsoft.com/library/azure/dn776326.aspx#Prerequisites)，並確保您已[啟用 REST API 的存取](https://msdn.microsoft.com/library/azure/dn776326.aspx#EnableRESTAPI)。

要建立記錄器，請使用下列的 URL 範本 HTTP 保留要求。

    https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview

-   取代`{your service}`API 管理服務執行個體名稱。
-   取代`{new logger name}`與您的新記錄器所需的名稱。 設定[記錄-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)原則時，會參照此名稱

邀請新增下列標題。

-   內容類型︰ 應用程式/json
-   授權︰ SharedAccessSignature uid =...
    -   如需相關指示，在產生`SharedAccessSignature`請參閱[Azure API 管理 REST API 驗證](https://msdn.microsoft.com/library/azure/dn798668.aspx)。

指定使用下列範本邀請內文。

    {
      "type" : "AzureEventHub",
      "description" : "Sample logger description",
      "credentials" : {
        "name" : "Name of the Event Hub from the Azure Classic Portal",
        "connectionString" : "Endpoint=Event Hub Sender connection string"
        }
    }

-   `type`必須設定為 [ `AzureEventHub`。
-   `description`提供記錄器的選擇性描述，並視需要，可以是零長度字串。
-   `credentials`包含`name`和`connectionString`的 Azure 事件中心。

當您進行要求，如果記錄器建立狀態碼`201 Created`會傳回。 

>[AZURE.NOTE] 如需其他可能傳回代碼與他們的原因，請參閱[建立記錄器](https://msdn.microsoft.com/library/azure/mt592020.aspx#PUT)。 若要查看如何執行其他的作業，例如清單、 更新及刪除]，請參閱[記錄器](https://msdn.microsoft.com/library/azure/mt592020.aspx)實體文件。

## <a name="configure-log-to-eventhubs-policies"></a>設定記錄-eventhubs 原則

一旦您記錄器 API 管理設定，您可以設定您想要的事件記錄的記錄-eventhubs 原則。 記錄檔-eventhubs 原則可在 [輸入的原則] 區段或輸出原則] 區段中。

若要設定原則，登入[Azure 傳統入口網站](https://manage.windowsazure.com)中，瀏覽您 API 管理的服務，然後按一下 [ **publisher 入口網站**] 或 [**管理**] 以存取 publisher 入口網站。

![Publisher 入口網站][publisher-portal]

按一下左側的 API 管理] 功能表中的 [**原則**，選取所需的產品和 API，並按一下 [**新增原則**。 在此範例中，我們要新增原則到**回音 API**的**無限制**產品。

![新增原則][add-policy]

將游標放在`inbound`原則] 區段，然後按一下 [若要插入的**記錄檔，以 EventHub**原則`log-to-eventhub`原則陳述式範本。

![原則編輯器][event-hub-policy]

    <log-to-eventhub logger-id ='logger-id'>
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
    </log-to-eventhub>

取代`logger-id`API 管理記錄器您在上一個步驟中設定的名稱。

您可以使用做為的值傳回字串的任何運算式`log-to-eventhub`項目。 在此範例會記錄包含日期和時間、 服務名稱、 要求識別碼、 要求 ip 位址，然後作業名稱的字串。

按一下 [儲存更新的原則設定的 [**儲存**]。 文件會儲存為原則已啟動，並指定的事件中樞會記錄事件。

## <a name="next-steps"></a>後續步驟

-   深入瞭解 Azure 事件集線器
    -   [Azure 事件集線器快速入門](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
    -   [接收郵件] 使用 EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost)
    -   [事件集線器程式設計指南](../event-hubs/event-hubs-programming-guide.md)
-   深入瞭解 API 管理和事件集線器整合
    -   [記錄器實體參考](https://msdn.microsoft.com/library/azure/mt592020.aspx)
    -   [記錄檔-eventhub 原則參照](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
    -   [監控您的 Api Azure API 管理、 事件集線器與 Runscope](api-management-log-to-eventhub-sample.md)    

## <a name="watch-a-video-walkthrough"></a>觀看視訊的逐步解說

> [AZURE.VIDEO integrate-azure-api-management-with-event-hubs]


[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png






