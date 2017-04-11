<properties 
    pageTitle="Azure 佇列和服務匯流排佇列-比較並相對於 |Microsoft Azure"
    description="分析相似與相異處之間佇列 Azure 所提供的兩種類型。"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="tbd"
    ms.date="09/23/2016"
    ms.author="sethm" />

# <a name="azure-queues-and-service-bus-queues---compared-and-contrasted"></a>Azure 佇列和服務匯流排佇列-比較並相對於

本文會相似與相異處之間佇列今天 Microsoft Azure 所提供的兩種類型︰ Azure 佇列和服務匯流排佇列。 藉由使用這項資訊，您可以比較對比分別技術，並能瞭解哪種解決方案最符合您需求的更多的資訊決定。

## <a name="introduction"></a>簡介

Microsoft Azure 支援兩種類型的佇列機制︰ **Azure 佇列**及**服務匯流排佇列**。

**Azure 佇列**，也就是[Azure 儲存](https://azure.microsoft.com/services/storage/)基礎結構的一部分，功能簡單的其餘型取得/放入/預覽介面，提供可靠、 持續訊息且服務之間。

**服務匯流排佇列**是狀況提出更廣義[Azure 訊息](https://azure.microsoft.com/services/service-bus/)基礎結構支援佇列以及發佈/訂閱、 Web 服務遠端處理和整合模式的組件。 如需有關服務匯流排佇列主題/訂閱]，以及轉送的詳細資訊，請參閱[服務匯流排通訊概觀](service-bus-messaging-overview.md)。

時同時有兩個佇列技術，Azure 佇列已先掌握 Azure 儲存服務內建專用的佇列中儲存機制推出。 內建服務匯流排佇列上方的狀況提出更廣義 」 代理訊息 「 基礎結構設計來整合應用程式或應用程式元件可能橫跨多個通訊協定，資料合約的信任的網域，及/或網路環境。

本文將比較兩個佇列技術 Azure 所討論的行為和實作每個所提供的功能差異。 本文也會提供指導選擇哪些功能可能會根據您的應用程式開發需求。

## <a name="technology-selection-considerations"></a>技術選取考量

Azure 佇列和服務匯流排佇列是實作佇列目前是在 Microsoft Azure 服務的郵件。 每一個有稍有不同的功能集，這表示您可以選擇一個] 或 [其他]，或使用兩個根據特定的方案或商務/技術問題解決您的需求。

在決定哪一種佇列技術符合指定的解決方案的目的，解決方案設計師和開發人員必須考量下列建議。 如需詳細資訊，請參閱下一節。

身為解決方案架構/開發人員，**您應該考慮使用 Azure 佇列**時︰

- 您的應用程式必須在佇列中，郵件會短 7 天存留時間的位置儲存超過 80 GB 的郵件。

- 您的應用程式想要追蹤進度，以處理內佇列中的郵件。 這是很有用處理郵件的工作者當機。 後續的工作者然後可以從先前的工作者離開的地方繼續使用這項資訊。

- 您需要針對您的佇列執行交易的所有的伺服器端記錄檔。

身為解決方案架構/開發人員，**您應該考慮使用服務匯流排佇列**時︰

- 您的方案必須能夠將接收訊息，而不需要以獲得佇列。 使用服務匯流排如下使用長投票接收使用 TCP 通訊協定服務匯流排支援的作業。

- 您的方案要求提供保證的先中的第一個-出佇列中 (FIFO) 排序傳遞。

- 您想要在 Azure 和 Windows Server （私人雲端） 上對稱體驗。 如需詳細資訊，請參閱[為 Windows Server 服務匯流排](https://msdn.microsoft.com/library/dn282144.aspx)。

- 您必須能夠支援重複的自動偵測您的方案。

- 您想要為平行長資料流處理郵件應用程式 （郵件是相關聯的郵件上使用 [[工作階段識別碼](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx)] 屬性的資料流）。 在此模式中，使用應用程式中的每個節點競相資料流時，而不是郵件。 當資料流提供給耗用的節點時，節點可以檢查使用交易的應用程式串流狀態的狀態。

- 您的方案需要交易行為與傳送或接收來自佇列中的多個訊息時的完整性。

- 存留時間 (TTL) 的特性特定應用程式的工作量可以超過 7 天。

- 應用程式處理可以超過 64 KB 的郵件，但不是可能方法 256 KB 限制。

- 您處理要求寄件者及接收者提供佇列，與不同的權限的角色型存取模型。

- 您佇列中的大小會增加大於 80 GB。

- 您想要使用的 AMQP 1.0 標準的訊息通訊協定。 如需有關 AMQP 的詳細資訊，請參閱[服務匯流排 AMQP 概觀](./service-bus-amqp-overview.md)。

- 您可以構想從佇列中型點對點通訊最終移轉到郵件 exchange 模式，讓緊密地整合其他接收器 （訂閱者），都會收到獨立複本傳送給佇列中的部分或所有郵件。 第二個指的是提供服務匯流排的發佈/訂閱功能。

- 您的訊息解決方案必須能夠支援 」 在大多數-一次 」 傳遞保證，而不需要您建立其他的基礎結構元件。

- 您想要能夠發佈及使用批次的郵件。

- 您需要 Windows Communication Foundation (WCF) 通訊堆疊於.NET Framework 完全整合。

## <a name="comparing-azure-queues-and-service-bus-queues"></a>比較 Azure 佇列和服務匯流排佇列

下列各節中的資料表提供的佇列功能邏輯群組，並可讓您一眼 Azure 佇列與服務匯流排佇列中可用的功能比較。

## <a name="foundational-capabilities"></a>基本功能

本節會比較 Azure 佇列和服務匯流排佇列所提供的基本佇列功能。

|比較準則|Azure 佇列|服務匯流排佇列|
|---|---|---|
|排序保證|**無** <br/><br>如需詳細資訊，請參閱 「 其他資訊 」 區段中的第一個筆記。</br>|**是--先出 (FIFO)**<br/><br>（透過使用訊息工作階段）|
|傳遞保證|**在小次**|**在小次**<br/><br/>**在大部分次**|
|作業支援|**無**|**[是]**<br/><br/>|
|接收行為|**非封鎖**<br/><br/>（完成立即如果找不到沒有新的郵件）|**具有/沒有逾時封鎖**<br/><br/>（優惠長投票或[「 慧星一號技巧 」](http://go.microsoft.com/fwlink/?LinkId=613759)）<br/><br/>**非封鎖**<br/><br/>（透過.NET 使用受管理的 API 只）|
|推入樣式 API|**無**|**[是]**<br/><br/>[OnMessage](https://msdn.microsoft.com/library/azure/jj908682.aspx)和**OnMessage**工作階段.NET API。|
|接收模式|**預覽及租用**|**預覽及鎖定**<br/><br/>**接收及刪除**|
|獨佔式存取模式|**租賃型**|**鎖定型**|
|租賃鎖定持續時間|**30 秒 （預設值）**<br/><br/>**7 天 （最大值）**（您可以更新或放開訊息租用使用[UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx) API）。|**60 秒 （預設值）**<br/><br/>您可以更新使用[RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) API 訊息鎖定。|
|租賃鎖定精確度|**訊息層級**<br/><br/>（每一封郵件可以有不同的逾時值，然後您可以更新視需要使用[UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx) API 處理郵件時）|**佇列中的層級**<br/><br/>（每個佇列已鎖定精確度，套用到所有的郵件，但您可以更新使用[RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx) API 鎖定）。|
|批次接收|**[是]**<br/><br/>（明確地指定郵件計數擷取郵件，上限為 32 的郵件時）|**[是]**<br/><br/>(隱含啟用預先擷取屬性或明確透過使用交易)|
|批次的傳送|**無**|**[是]**<br/><br/>（透過使用交易或用戶端批次處理）|

### <a name="additional-information"></a>其他資訊

- Azure 佇列中的訊息通常是先中的第一個-出，但有時候可能很順序。例如，郵件的可見度逾時工期過期時 （例如，因期間處理損毀的用戶端應用程式）。 可見度逾時時間後，訊息就會看到一次在佇列中的另一個工作，取消佇列它。 此時，顯示新郵件可能會放置在佇列中 （會再度） 之後，後面是原本的佇列郵件。

- 如果您已經在使用 Azure 儲存體 Blob 或表格，並開始使用佇列，並保證 99.9%。 如果您服務匯流排佇列的情況下，使用二進位大型物件或表格，您會有較低的可用性。

- 服務匯流排佇列中的保證的 FIFO 模式需要使用的訊息工作階段。 應用程式當機處理**預覽及鎖定**模式中收到的郵件時下, 一次佇列中的收件者接受訊息的工作階段，則會啟動失敗訊息存留時間 (TTL) 期間到期之後。

- Azure 佇列的設計支援標準佇列情況，例如分開的應用程式元件，可增加延展性及容錯失敗次數，載入資源撫平，並建立程序工作流程。

- 服務匯流排佇列支援*在小次*傳遞保證。 此外，*在大部分次*語意可支援儲存應用程式狀態使用工作階段及使用交易自動接收郵件和更新工作階段狀態。

- Azure 佇列提供統一且一致的程式設計模型佇列、 表格和 Blob-適用於開發人員及作業小組。

- 服務匯流排佇列的內容中的單一佇列中的本機交易提供支援。

- 支援服務匯流排**接收並刪除**模式來降低的傳遞保證提供的功能，以減少訊息作業字數統計 （及相關聯的成本）。

- Azure 佇列提供給租用來擴充租用郵件的能力。 這個選項可讓人員維持簡短租用的郵件]。 因此，如果工作者損毀，郵件可快速處理一次的另一個工作。 此外，如果需要處理，超過目前期背景工作可以擴充郵件的租用。

- Azure 佇列提供可見度逾時，您可以依據 enqueueing 設定或取消佇列的郵件。 此外，可以不同租用值在執行階段更新郵件，並在同一個佇列中的郵件上更新不同的值。 服務匯流排鎖定逾時都定義在佇列中的中繼資料。不過，您可以更新鎖定呼叫[RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx)的方法。

- 封鎖接收作業服務匯流排佇列中的最大的逾時為 24 個工作日。 不過，其餘型逾時有 55 秒內最大值。

- 用戶端批次處理提供的服務匯流排啟用批次多封郵件，將單一傳送作業的佇列中用戶端。 批次只適用於非同步傳送作業。

- Azure 佇列 （詳細說明當您將虛擬化帳戶） 及不受限制的佇列 200 TB 上限等功能可讓理想的平台 SaaS 提供者。

- Azure 佇列提供非常有彈性，效能委派存取控制機制。

## <a name="advanced-capabilities"></a>進階的功能

本節會比較 Azure 佇列和服務匯流排佇列所提供的進階的功能。

|比較準則|Azure 佇列|服務匯流排佇列|
|---|---|---|
|排定的傳送|**[是]**|**[是]**|
|自動無字母|**無**|**[是]**|
|增加佇列中存留時間值|**[是]**<br/><br/>（透過就地更新的可見度逾時）|**[是]**<br/><br/>（透過專用的 API 函數提供）|
|破壞訊息支援|**[是]**|**[是]**|
|就地更新|**[是]**|**[是]**|
|伺服器端交易記錄檔|**[是]**|**無**|
|儲存指標|**[是]**<br/><br/>**分鐘指標**︰ 提供的顯示狀態，TPS，API 的即時指標呼叫計數、 錯誤計數及更多]，所有中進行即時 （每分鐘彙總，然後從只去生產環境中的幾分鐘內報告。 如需詳細資訊，請參閱[關於儲存分析指標](https://msdn.microsoft.com/library/azure/hh343258.aspx)。|**[是]**<br/><br/>（呼叫[GetQueues](https://msdn.microsoft.com/library/azure/hh293128.aspx)大量查詢）。|
|狀態管理|**無**|**[是]**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx)， [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx)， [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx) [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx)|
|郵件自動轉寄|**無**|**[是]**|
|清除佇列函數|**[是]**|**無**|
|郵件群組|**無**|**[是]**<br/><br/>（透過使用訊息工作階段）|
|每個群組的郵件應用程式狀態|**無**|**[是]**|
|重複偵測|**無**|**[是]**<br/><br/>（可在寄件者側）|
|WCF 整合|**無**|**[是]**<br/><br/>（提供的方塊出 WCF 繫結）|
|WF 整合|**自訂**<br/><br/>（需要建立自訂的 WF 活動）|**原生**<br/><br/>（提供的方塊出 WF 活動）|
|瀏覽郵件群組|**無**|**[是]**|
|讀取訊息工作階段識別碼|**無**|**[是]**|

### <a name="additional-information"></a>其他資訊

- 兩個佇列技術啟用排定稍後傳遞郵件。

- 佇列中自動轉寄可讓數以千計的佇列來自動轉寄他們單一佇列中，從接收的應用程式會使用該郵件的郵件。 您可以使用這個機制達到安全性、 控制流程，並隔離儲存空間，每個訊息 publisher 之間。

- Azure 佇列的更新訊息內容提供支援。 您可以使用這項功能保存陳述式資訊和增加進度更新到郵件，讓它可以處理從最後一個已知檢查點，而非重頭開始。 服務匯流排佇列中，您可以啟用透過訊息工作階段使用相同的狀況。 工作階段可讓您儲存並 （藉由使用[SetState](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx)和[GetState](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx)） 擷取的應用程式處理狀態。

- [無字母](service-bus-dead-letter-queues.md)只支援服務匯流排佇列，可以用來找出所接收的應用程式或訊息無法到達到期的存留時間 (TTL) 屬性因為目的地無法順利處理的郵件。 [TTL] 值指定多久郵件仍會保留在佇列中。 使用服務匯流排郵件會移至 [特殊的佇列中，稱為 $DeadLetterQueue TTL 期間到期時。

- 若要尋找 「 有害 」 的訊息 Azure 佇列中時取消佇列郵件應用程式會檢查郵件**[DequeueCount](https://msdn.microsoft.com/library/azure/dd179474.aspx)**屬性。 如果**DequeueCount**臨界值的上方，應用程式會將郵件移到應用程式定義 「 無法傳送信件 」 佇列。

- Azure 佇列可讓您以取得詳細資訊記錄的所有交易執行佇列中，為也為彙總的指標。 兩個選項都有助偵錯時，並瞭解如何應用程式使用 Azure 佇列。 他們的年齡也很適合用於效能調整您的應用程式，並減少使用佇列的成本。

- 「 訊息工作階段 「 支援服務匯流排的概念可讓您指定的收件者在建立郵件和其個別接收器之間類似工作階段的相關性與相關聯的特定邏輯群組的郵件。 您可以啟用此服務匯流排中的進階的功能[工作階段識別碼](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx)] 屬性設定郵件。 接著接收器可以聆聽特定的工作階段識別碼，然後收到訊息，共用指定的工作階段識別碼。

- 重複偵測功能會自動支援服務匯流排佇列中移除重複的郵件傳送給佇列中或主題中，根據[訊息](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx)屬性的值。

## <a name="capacity-and-quotas"></a>容量和配額

本節會比較 Azure 佇列和服務匯流排佇列[容量和配額](service-bus-quotas.md)可能套用觀點。

|比較準則|Azure 佇列|服務匯流排佇列|
|---|---|---|
|佇列最大值|**200 TB**<br/><br/>（僅限於單一儲存帳戶容量）|**1 GB，以 80 GB**<br/><br/>（定義在建立佇列和[啟用分割](service-bus-partitioning.md)– 請參閱 「 其他資訊 」 區段）|
|郵件大小上限|**64 KB**<br/><br/>(使用**Base64**編碼時 48 KB)<br/><br/>Azure 支援大型郵件佇列和 blob – 而此時，您可以在佇列中結合為單一項目最多 200 GB。|**256 KB**或**1 MB**<br/><br/>(包括標題和本文，最大的標頭大小︰ 64 KB)。<br/><br/>[服務層級](service-bus-premium-messaging.md)而定。|
|[TTL] 的最大郵件|**7 天**|**`TimeSpan.Max`**|
|最大的數字的佇列|**無限制**|**10000**<br/><br/>（每個服務的命名空間，您可以增加）|
|同時用戶端的數目上限|**無限制**|**無限制**<br/><br/>（100 同時連線限制僅適用於 TCP 通訊協定的通訊）|

### <a name="additional-information"></a>其他資訊

- 服務匯流排會強制執行佇列大小限制。 佇列最大值為指定的佇列中建立時，並且可以有介於 1 到 80 GB 的值。 如果達到設定佇列中建立的佇列大小值，則額外的內送郵件都會被拒絕和例外狀況會收到呼叫程式碼。 如需在服務匯流排配額的詳細資訊，請參閱[服務匯流排配額](service-bus-quotas.md)。

- 您可以建立服務匯流排佇列中 1、 2、 3、 4 或 5 GB （預設值為 1 GB） 的大小。 使用分割啟用 （這是預設值），然後服務匯流排建立 16 的磁碟分割區您指定每 gb。 因此，如果您建立的大小是 5 GB 佇列中，使用 16 的磁碟分割區佇列最大值成為 (5 * 16) = 80 GB。 您可以查看 [ [Azure 入口網站][]上的 [項目，請參閱分割佇列中或主題的大小上限。

- 使用 Azure 佇列中，如果郵件的內容不是 XML 安全，然後就必須是**Base64**編碼。 如果您**Base64**-編碼的訊息，使用者內容可達 48 KB，而非 64 KB。

- 服務匯流排佇列中，使用儲存在佇列中的每一封郵件包含兩個部分︰ 標題和本文。 郵件的總大小不能超過郵件大小上限支援服務層級。

- 用戶端通訊與服務匯流排佇列 TCP 通訊協定，請同時連線至單一的服務匯流排佇列中的最大的數字時，限制為 100。 這個數字會寄件者及接收者之間共用。 如果達到此配額時，會被拒絕的其他連線的後續要求，然後例外狀況會收到呼叫程式碼。 這項限制不是在用戶端連線至其他為基礎的 API 佇列所設定。

- 如果您需要超過 10000 個佇列中的單一服務匯流排命名空間，您可以連絡 Azure 支援小組，並要求增加。 若要縮放 10000 佇列服務匯流排之外，您也可以建立使用[Azure 入口網站][]的其他命名空間。

## <a name="management-and-operations"></a>管理及作業

本節將提供 Azure 佇列和服務匯流排佇列管理功能進行比較。

|比較準則|Azure 佇列|服務匯流排佇列|
|---|---|---|
|管理通訊協定|**停留在 HTTP/HTTPS**|**停留在 HTTPS**|
|執行階段通訊協定|**停留在 HTTP/HTTPS**|**停留在 HTTPS**<br/><br/>**AMQP 1.0 標準 (TCP 與 TLS)**|
|.NET 受管理的 API|**[是]**<br/><br/>（.NET 管理儲存空間用戶端 API）|**[是]**<br/><br/>（受管理的.NET 代理訊息 API）|
|原生 c + +|**[是]**|**無**|
|Java API|**[是]**|**[是]**|
|PHP API|**[是]**|**[是]**|
|Node.js API|**[是]**|**[是]**|
|任意中繼資料支援|**[是]**|**無**|
|佇列中命名規則|**長達 63 字元**<br/><br/>（佇列中的名稱字母必須小寫。）|**最多 260 個字元**<br/><br/>（佇列路徑和名稱是不區分大小寫）。|
|取得佇列長度函數|**[是]**<br/><br/>（大約值而不刪除郵件到期超出 TTL。）|**[是]**<br/><br/>（完全符合、 時間點值。）|
|預覽函數|**[是]**|**[是]**|

### <a name="additional-information"></a>其他資訊

- Azure 佇列的任意可套用至佇列描述，在表單中的名稱/值組的屬性提供支援。

- 兩個佇列技術提供不必鎖定它，其中會有幫助實作佇列中 explorer/瀏覽器工具時檢視郵件的能力。

- 服務匯流排.NET 代理訊息 Api 善用完整雙面列印的 TCP 連線以改善效能相較於其餘，透過 HTTP，與他們支援 AMQP 1.0 標準的通訊協定。

- Azure 佇列的名稱可 3 63 字元時間有多長，可以包含大小寫字母、 數字和連字號。 如需詳細資訊，請參閱[命名佇列和中繼資料](https://msdn.microsoft.com/library/azure/dd179349.aspx)。

- 服務匯流排佇列名稱可以超過 260 個字元數，而且有限制較少的命名規則。 服務匯流排佇列名稱可以包含字母、 數字、 週期、 連字號和底線。

## <a name="authentication-and-authorization"></a>驗證與授權

本節將說明 Azure 佇列和服務匯流排佇列支援的驗證及授權功能。

|比較準則|Azure 佇列|服務匯流排佇列|
|---|---|---|
|驗證|**對稱金鑰**|**對稱金鑰**|
|安全性模型|透過 SA 權杖委派的存取。|SA|
|身分識別提供者同盟|**無**|**[是]**|

### <a name="additional-information"></a>其他資訊

- 每個要求的佇列技術必須經過驗證。 不支援匿名存取的公用佇列。 使用[SA](service-bus-sas-overview.md)，您可以藉由發佈唯 SA、 唯讀 SA 或甚至完整存取 SA 解決這種情況。

- Azure 佇列所提供的驗證配置涉及使用對稱金鑰，也就是雜湊訊息驗證碼 (HMAC)，使用 sha-256 演算法並編碼為**Base64**字串。 如需有關個別的通訊協定的詳細資訊，請參閱[Azure 儲存服務驗證](https://msdn.microsoft.com/library/azure/dd179428.aspx)。 服務匯流排佇列支援使用對稱金鑰類似模型。 如需詳細資訊，請參閱[共用 Access 簽章驗證與服務匯流排](service-bus-shared-access-signature-authentication.md)。

## <a name="cost"></a>成本

本節會比較成本觀點 Azure 佇列和服務匯流排佇列。

|比較準則|Azure 佇列|服務匯流排佇列|
|---|---|---|
|佇列中交易成本|**$ 0.0036**<br/><br/>（每個 100000 交易）|**基本層**: **$0.05**<br/><br/>（每個數百萬個操作）|
|計費作業|**所有**|**傳送/接收只**<br/><br/>（免費的其他操作）|
|閒置交易|**計費**<br/><br/>（查詢空白的佇列中就會計算為計費交易。）|**計費**<br/><br/>（針對空白的佇列中接收被視為計費郵件）。|
|儲存空間的成本|**$ 0.07**<br/><br/>（每 GB/月）|**$ 0.00**|
|輸出資料傳輸成本|**$0.12-$ 0.19**<br/><br/>（根據地理位置）。|**$0.12-$ 0.19**<br/><br/>（根據地理位置）。|

### <a name="additional-information"></a>其他資訊

- 資料傳輸負責根據總數透過網際網路離開 Azure 資料中心，指定的計費週期中的資料。

- 位於相同的區域內的 Azure 服務之間的資料傳輸沒有收費。

- 本文撰寫時，所有傳入的資料傳輸沒有收費。

- 提供支援長投票，使用服務匯流排佇列能成本有效低延遲傳送必要的情況。

>[AZURE.NOTE] 所有的成本會有所變更。 下表會反映目前的價格，並不包含目前可能使用任何優惠。 更新 Azure 價格的詳細資訊，請參閱[Azure 價格](https://azure.microsoft.com/pricing/)的頁面。 如需有關服務匯流排價格的詳細資訊，請參閱[服務匯流排價格](https://azure.microsoft.com/pricing/details/service-bus/)。

## <a name="conclusion"></a>結束時

以獲得更深入瞭解的兩個技術，您將能夠使用，哪一種佇列技術，更多的資訊決定和時間。 何時使用 Azure 佇列] 或 [服務匯流排佇列決策清楚而定的因素。 這些因素可能會嚴重取決於應用程式和其架構個人的需求。 如果您的應用程式中已使用的 Microsoft Azure 核心功能，您可能會想要選擇 Azure 佇列，尤其是如果您需要基本的通訊與訊息之間的服務或大於 80 GB 大小的需要佇列。

因為服務匯流排佇列提供數字的進階功能，例如工作階段，交易，複製 [自動偵測失效字母及持續性發佈/訂閱功能，它們可能是慣用的選擇，如果您要建立混合式應用程式，或如果您的應用程式否則需要這些功能。

## <a name="next-steps"></a>後續步驟

下列文件提供更多的指導方針，使用 Azure 佇列] 或 [服務匯流排佇列的相關資訊。

- [如何使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
- [如何使用佇列中儲存服務](../storage/storage-dotnet-how-to-use-queues.md)
- [使用服務匯流排改善效能的最佳作法代理訊息](service-bus-performance-improvements.md)
- [介紹佇列和 Azure 服務匯流排主題](http://www.code-magazine.com/article.aspx?quickid=1112041)
- [服務匯流排的開發人員指南](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
- [Azure 中使用佇列服務](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)
- [了解 Azure 儲存體計費 – 頻寬、 交易和生產力](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

[Azure 入口網站]: https://portal.azure.com
 
