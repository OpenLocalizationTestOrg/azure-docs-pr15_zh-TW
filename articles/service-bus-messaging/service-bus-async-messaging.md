<properties 
    pageTitle="服務匯流排非同步訊息 |Microsoft Azure"
    description="服務匯流排非同步訊息的說明。"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/04/2016"
    ms.author="sethm" />

# <a name="asynchronous-messaging-patterns-and-high-availability"></a>非同步訊息的圖樣與可用性

在各種不同的方式可以實作非同步訊息。 使用佇列、 主題及訂閱，Azure 服務匯流排支援看似透過市集及轉寄機制。 正常 （同步），您可以傳送郵件給佇列 」 和 「 主題，並會從佇列及訂閱接收郵件。 您所撰寫的應用程式而定被使用這些項目。 當實體狀況的變更，請在許多情況下，因為您需要提供可以符合大部分的需求精簡的功能實體的方式。

應用程式通常會使用非同步訊息模式，讓通訊案例的數字。 您可以建立的用戶端可以傳送郵件給服務，即使未執行服務應用程式。 應用程式的 [通訊]，[佇列高速量可以幫助的體驗層級載入提供緩衝通訊的位置。 最後，您可以取得簡單而有效負載平衡器來發送訊息多台電腦上。

若要維持可使用任何這些項目，請考慮數種不同的方式，這些項目都無法進行永久性的郵件系統。 一般而言，我們可以看到變成無法使用應用程式中的下列的不同方式撰寫的實體︰

- 無法傳送郵件。

- 無法接收郵件。

- 無法管理實體 （建立、 擷取、 更新或刪除項目）。

- 無法連絡服務。

針對每個這些失敗，不同的失敗模式存在，可讓應用程式，以繼續執行一些精簡功能的層級的工作。 例如，可以傳送郵件，但不是會收到的系統仍然可以收到的客戶訂單，但無法處理這些訂單。 本主題探討可能會發生的潛在問題並降低這些問題的方式。 服務匯流排介紹了數個減輕您必須選擇，將其與這個主題也討論這些參加降低使用的規則。

## <a name="reliability-in-service-bus"></a>在 [服務匯流排可靠性

有數種方式處理郵件和實體的問題，而有這些降低使用適當方針。 若要瞭解指導方針，您必須先瞭解什麼可以在服務匯流排失敗。 Azure 系統的設計，因為這些問題通常是很短暫。 在高的層級，無法使用的其他原因顯示，如下所示︰

-   從外部系統服務匯流排所依賴的節流設定。 節流就會發生儲存和計算資源的互動。

-   系統服務匯流排所依賴的問題。 例如，指定的組件的儲存空間可以發生問題。

-   在單一子系統服務匯流排失敗。 在此情況下，運算節點能將不一致的狀態，必須重新啟動本身，讓它可以做負載平衡其他節點的所有項目。 接著就會導致短時間內變得很慢的郵件處理。

-   Azure 資料中心內的服務匯流排失敗。 這是 「 嚴重失敗 」 的期間系統是無法連線到幾分鐘的幾個小時。

> [AZURE.NOTE] Azure 儲存體和 SQL Azure，可能表示字詞**儲存空間**。

服務匯流排包含降低這些問題的數字。 下列各節討論每個問題，其個別降低。

### <a name="throttling"></a>節流

服務匯流排節流可讓合作訊息工資率管理。 每個個別的服務匯流排節點儲存了許多實體。 每個這些實體而言 CPU、 記憶體、 儲存及其他 facet 系統會要求。 在任何這些 facet 偵測到的使用方式的超過定義臨界值時，服務匯流排可以拒絕指定的要求。 來電者會收到[ServerBusyException][] ，並在 10 秒後重試。

降低必須閱讀錯誤並停止郵件的任何重試至少 10 秒程式碼。 跨設備組件的客戶應用程式就會發生錯誤，因為預期每一段獨立執行重試邏輯。 程式碼可以縮小所啟用佇列中或主題上分割經流速控制的機率。

### <a name="issue-for-an-azure-dependency"></a>Azure 的相依性的問題

Azure 中的其他元件有時候可能的服務問題。 例如時升級服務匯流排使用的系統時，系統可以暫時體驗精簡的功能。 若要解決這類問題，服務匯流排定期檢查及實作降低。 這些降低的影響就會出現。 例如，若要處理的儲存空間的暫時性問題，服務匯流排會執行搭配使用一致的郵件傳送作業 」 可讓系統。 降低的性質，因為已傳送的郵件需要 15 分鐘，會顯示在受影響的佇列中或訂閱並準備好進行接收作業。 一般而言，大部分的項目不會發生此問題。 不過，內 Azure 服務匯流排中指定的項目數目，此降低有時需要服務匯流排客戶的一小部分。

### <a name="service-bus-failure-on-a-single-subsystem"></a>在單一子系統服務匯流排失敗

若任何應用程式，各種情況下可能會導致服務匯流排變成不一致的內部元件。 當服務匯流排偵測此時，它會收集資料的應用程式，以協助診斷有何改變。 後會收集資料，重新啟動應用程式以返回一致的狀態。 此程序，會發生情況相當快速，且較短不對，無法使用進位到幾分鐘，但一般下時間的實體中的結果。

在下列情況下，用戶端應用程式會產生[System.TimeoutException][]或[MessagingException][]的例外狀況。 服務匯流排包含降低形式的用戶端自動重試邏輯這個問題。 耗盡重試期間並不會傳遞郵件之後，您可以瀏覽使用[成對的命名空間][]等其他功能。 成對的命名空間中有其他文件中所討論的注意事項。

### <a name="failure-of-service-bus-within-an-azure-datacenter"></a>Azure 資料中心內的失敗的服務匯流排

Azure 資料中心失敗的最可能原因是失敗升級部署的服務匯流排或相依系統。 成熟平台，因為已經減少，這種失敗的機率。 資料中心失敗也可能發生原因包括下列各項︰

-   」 （電源和產生 power 消失） 的詳細資料。
-   連線 （用戶端和 Azure 之間的網際網路符號）。

在這兩種情況下，自然或許多損壞會導致問題。 若要解決此問題，並確定您仍然可以傳送郵件，您可以使用[成對的命名空間][]啟用的訊息時的主要位置進行良好再次傳送至第二個位置。 如需詳細資訊，請參閱[最佳作法做針對服務匯流排中斷和損毀的應用程式][]。

## <a name="paired-namespaces"></a>成對的命名空間

[成對的命名空間][]功能支援案例中無法使用的服務匯流排實體或部署內的資料中心。 雖然不常發生此事件，分散式的系統仍必須準備處理表現最差案例。 一般而言，此事件發生因為服務匯流排所依賴的某些項目會發生這是短期的問題。 若要維持應用程式的可用性過時期間，服務匯流排使用者可以使用兩個不同的命名空間，最好是在不同的資料中心裝載其訊息的項目。 本節的其餘部分會使用下列用語︰

-   主要命名空間︰ 使用您的應用程式間的互動，針對 [傳送和接收作業的命名空間。

-   第二個命名空間︰ 做為主要的命名空間備份的命名空間。 應用程式邏輯不常使用這個命名空間。

-   容錯移轉間隔︰ 若要接受標準失敗之前應用程式從主要的命名空間切換至第二個命名空間的時間量。

搭配命名空間支援*傳送可用性*。 傳送可用性保留傳送郵件的能力。 若要使用傳送顯示狀態，您的應用程式必須符合下列需求︰

1.  從主要的命名空間只會收到訊息。

2.  郵件傳送至指定的佇列或主題可能送達順序。

3.  如果您的應用程式使用工作階段，工作階段中的郵件可能會送達順序。 這是從標準功能的工作階段的符號。 這表示您的應用程式使用工作階段，以邏輯方式] 群組中的訊息。 在 [主要命名空間只維護工作階段狀態。

4.  工作階段中的郵件可能會送達順序。 這是從標準功能的工作階段的符號。 這表示您的應用程式使用工作階段，以邏輯方式] 群組中的訊息。

5.  在 [主要命名空間只維護工作階段狀態。

6.  主要佇列中，可以上線並開始前次要佇列中提供的所有郵件到主要佇列中接收郵件。

下列各節討論 Api、 Api 如何實作時，並顯示程式碼範例使用該功能。 請注意，這項功能相關聯的帳單影響。

### <a name="the-messagingfactorypairnamespaceasync-api"></a>MessagingFactory.PairNamespaceAsync API

成對的命名空間功能包括[Microsoft.ServiceBus.Messaging.MessagingFactory][]類別上的[PairNamespaceAsync][]方法︰

```
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

工作完成後，請命名空間配對完成並準備好作的任何[MessageReceiver][]， [QueueClient][]，或也[TopicClient][]建立[MessagingFactory][]執行個體。 [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions][]是不同的配對類型，可使用[MessagingFactory][]物件的基底類別。 目前，只在衍生的類別是一個命名的[SendAvailabilityPairedNamespaceOptions][]，來實作傳送可用性需求。 [SendAvailabilityPairedNamespaceOptions][]有建立於彼此的建構函式的一組。 尋找建構函式在大部分的參數，您可以瞭解其他建構函式的行為。

```
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

這些參數的意義如下︰

-   *secondaryNamespaceManager*︰ 第二個命名空間[PairNamespaceAsync][]方法可以用來設定第二個命名空間初始化的[NamespaceManager][]執行個體。 命名空間管理員用來取得佇列命名空間中的清單，並確定必要的積存佇列存在。 如果這些佇列不存在，會建立。 [NamespaceManager][]需要建立與**管理**宣告的權杖的能力。

-   *messagingFactory*︰ 第二個命名空間的[MessagingFactory][]執行個體。 [MessagingFactory][]物件用來傳送，如果[EnableSyphon][]屬性設為**true**，會從積存佇列接收郵件。

-   *backlogQueueCount*︰ 若要建立的積存佇列數目。 此值必須至少為 1。 傳送郵件時要為待處理，其中一個佇列隨機選擇。 如果您設定的值為 1 時，只有一個佇列中可以時使用。 發生這種情況，然後一個積存佇列中會產生錯誤，用戶端無法嘗試不同的積存佇列中，而將郵件傳送可能會失敗。 我們建議您將此值設部分較大的值與預設值為 10 的值。 您可以將它變更為高或較低的值，根據您的應用程式傳送每日的資料量。 每個積存佇列可以保留最多 5 GB 的郵件。

-   *failoverInterval*︰ 時就會接受的主要命名空間失敗之前切換到的任何單一項目，第二個命名空間的時間量。 在實體的實體，會發生錯誤後移轉。 在單一命名空間經常即時服務匯流排內的不同節點中的項目。 在一個實體失敗不代表另一個錯誤。 您可以將此值為[System.TimeSpan.Zero][]次要容錯移轉至您的第一個、 非暫時性失敗之後，立即。 觸發容錯移轉計時器的失敗次數是[IsTransient][]屬性是 false，任何[MessagingException][] [System.TimeoutException][]。 其他的例外狀況，例如[UnauthorizedAccessException][]不會導致錯誤後移轉，因為它們指示用戶端的設定不正確。 [ServerBusyException][]不會導致錯誤後移轉因為正確的模式就是等候 10 秒數，然後重新傳送訊息。

-   *enableSyphon*︰ 會指出此特定配對應該也會 syphon 從第二個命名空間回到主要命名空間的郵件。 一般而言，傳送的郵件應用程式應該將此值設為**false**。接收郵件的應用程式應該將此值設為**true**。 原因是，通常有較少的郵件接收器比郵件的寄件者。 您可以根據接收者的數字，選擇有單一應用程式執行個體處理虹吸之責任。 使用多個接收器有帳單的影響，每個積存佇列。

若要使用的程式碼，建立主要的[MessagingFactory][]執行個體、 次要的[MessagingFactory][]執行個體、 次要[NamespaceManager][]執行個體及[SendAvailabilityPairedNamespaceOptions][]執行個體。 通話很簡單，如下所示︰

```
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

[PairNamespaceAsync][]方法所傳回的工作完成時，所有項目已設定且可供使用。 任務會傳回之前，您可能尚未完成的所有工作所需的配對右背景工作。 如此一來，您應該啟動工作傳回之前，傳送郵件。 如果發生錯誤，例如不正確的認證或無法建立積存佇列中，這些例外狀況工作完成後。 後傳回工作，請確認佇列所找到或檢查您[SendAvailabilityPairedNamespaceOptions][]執行個體的[BacklogQueueCount][]屬性來建立。 上述的程式碼，該作業會出現，如下所示︰

```
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## <a name="next-steps"></a>後續步驟

現在，您學到的服務匯流排非同步訊息的基本概念，閱讀更多詳細資料，[即成對][]的命名空間。

  [ServerBusyException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx
  [System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [做應用程式與服務匯流排中斷和損毀的最佳作法]: service-bus-outages-disasters.md
  [Microsoft.ServiceBus.Messaging.MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [MessageReceiver]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [SendAvailabilityPairedNamespaceOptions]:https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [EnableSyphon]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.enablesyphon.aspx
  [System.TimeSpan.Zero]: https://msdn.microsoft.com/library/azure/system.timespan.zero.aspx
  [IsTransient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.istransient.aspx
  [UnauthorizedAccessException]: https://msdn.microsoft.com/library/azure/system.unauthorizedaccessexception.aspx
  [BacklogQueueCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.backlogqueuecount.aspx
  [成對的命名空間]: service-bus-paired-namespaces.md