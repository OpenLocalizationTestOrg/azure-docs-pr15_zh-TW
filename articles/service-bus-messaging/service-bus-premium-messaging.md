<properties
    pageTitle="服務匯流排進階版和標準訊息價格層概觀 |Microsoft Azure"
    description="服務匯流排進階版和標準的訊息"
    services="service-bus"
    documentationCenter=".net"
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/02/2016"
    ms.author="darosa;sethm"/>

# <a name="service-bus-premium-and-standard-messaging-tiers"></a>服務匯流排進階版和標準的訊息層 

訊息服務匯流排，包含訊息項目，例如佇列且主題中，將企業訊息與豐富的功能發行訂閱在雲端的語意。 服務匯流排訊息會作為通訊時，則使用許多複雜的雲端解決方案。

服務匯流排訊息的*進階版*層地址常見的客戶要求周圍小數位數、 效能及重要的應用程式的可用性。 雖然幾乎相同的功能集，這兩層的服務匯流排訊息被用來作為不同的使用案例。

下表中醒目提示一些高層級的差異。

| 進階版                               | 標準                       |
|---------------------------------------|--------------------------------|
| 高處理量                       | 變數處理量            |
| 預期的效能               | 變數延遲               |
| 如預期呈現的價格                   | 支付另存新檔您前往變數價格 |
| 不按比例縮放上下工作負載的能力 | N/A                            |
| 郵件大小 > 256 KB                  | 郵件大小為 256 KB          |

**服務匯流排進階版訊息**資源隔離層的 CPU 和記憶體提供隔離中執行的每一個客戶工作量。 此資源容器稱為*訊息單位*。 每個進階版命名空間配置至少有一個訊息的單位。 您可以購買 1、 2 或 4 個訊息的單位，每個服務匯流排進階版命名空間。 在單一工作負載或實體可以橫跨多個訊息的單位，雖然帳單是在 24 小時] 或 [每日的工資率費用，可以將] 下，在變更訊息單位數量。 結果會如預期呈現且可重複服務匯流排型解決方案的效能。

不只是此效能更可預測和使用]，但是也會更快速地。 服務匯流排進階版訊息是根據儲存引擎[Azure 事件集線器](https://azure.microsoft.com/services/event-hubs/)中推出。 進階版訊息時，最大使用量效能就比與標準層更快。

## <a name="premium-messaging-technical-differences"></a>進階版訊息技術差異

以下是幾項進階版和標準的訊息層之間的差異。

### <a name="partitioned-queues-and-topics"></a>分割的佇列 」 和 「 主題

分割的佇列 」 和 「 主題支援的進階通訊，但無法運作方式相同標準和 Basic 層的服務匯流排訊息。 進階版的訊息不會使用 SQL 做為資料存放區，並不會再可能的資源競爭與相關聯的共用的平台。 如此一來，分割不需要。 此外，已變更的磁碟分割計算從 16 的磁碟分割區中的進階版中 2 的磁碟分割的標準訊息。 有兩個資料分割可確保可用性，而是更進階版執行階段環境的數字。 如需有關如何分割的詳細資訊，請參閱[分割佇列 」 和 「 主題](service-bus-partitioning.md)。

### <a name="express-entities"></a>Express 項目

進階版訊息完全隔離的 runtime 環境中執行，因為進階版命名空間中不支援快速的項目。 如需快速功能的詳細資訊，請參閱[Microsoft.ServiceBus.Messaging.QueueDescription.EnableExpress](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enableexpress.aspx)屬性。

## <a name="next-steps"></a>後續步驟

若要瞭解更多關於服務匯流排訊息，請參閱下列主題。

- [介紹 Azure 服務匯流排進階版訊息 （部落格文章）](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [介紹 Azure 服務匯流排進階版訊息 (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [服務匯流排訊息概觀](service-bus-messaging-overview.md)
- [如何使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
