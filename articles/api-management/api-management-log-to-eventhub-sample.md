<properties
    pageTitle="監控您的 Api Azure API 管理、 事件集線器與 Runscope"
    description="範例示範記錄-eventhub 原則連線 Azure API 管理，Azure 事件集線器，Runscope http 記錄與監視的應用程式"
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="darrmi"/>

# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-runscope"></a>監控您的 Api Azure API 管理、 事件集線器與 Runscope

[API 管理服務](api-management-key-concepts.md)，提供許多功能，來加強傳送給您的 HTTP API 的 HTTP 要求的處理。 邀請和回應存在但暫時性。 提出要求時，其傳送至您的後端 API API 管理服務。 您的 API 處理要求並回應經由傳回 API 消費者。 API 管理服務就會保存顯示 Api 的一些重要統計的 Publisher 入口網站儀表板，但進階詳細資料，會消失。

API 管理服務中使用[記錄-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) [原則](api-management-howto-policies.md)您可以從要求並回應[Azure [事件] 中心](../event-hubs/event-hubs-what-is-event-hubs.md)傳送的任何詳細資料。 有不同的原因，您可能會想要從 HTTP 郵件傳送到您的 Api 產生的事件。 部分範例包括更新、 使用狀況分析、 例外狀況提醒和第 3 廠商整合稽核記錄。   

本文將示範如何擷取整個 HTTP 要求與回應訊息，將其傳送給事件中心然後轉接給協力廠商服務，提供 HTTP 記錄與監視服務的郵件。

## <a name="why-send-from-api-management-service"></a>為什麼傳送 API 管理服務？
若要撰寫 HTTP 介軟體可以插入 HTTP API 架構，擷取 HTTP 邀請和回應，然後送入記錄與監視系統可能是。 這種方法的缺點是 HTTP 介軟體需要整合到後端 API，而且必須符合的 API 平台。 如果有多個 Api 每一個必須部署介軟體。 通常是無法升級後端 Api 的原因。

使用 「 Azure API 管理服務整合與記錄基礎結構提供集中式和平台無關的解決方案。 此外，也可調整，因為[地理複寫](api-management-howto-deploy-multi-region.md)功能 Azure API 管理的部分。

## <a name="why-send-to-an-azure-event-hub"></a>為什麼要傳送給 Azure 事件中心？
它是合理的要求，為什麼要建立的特定 Azure 事件集線器原則？ 有許多不同的位置，我想要登入我的要求。 為何不只是要求直接傳送到目的地？  這是一個選項。 不過，從 API 管理服務的記錄要求時，就必須考慮如何記錄訊息會影響效能的 api。 可以處理載入逐漸增加，以增加可用的系統元件的執行個體，或利用地理複寫。 不過，簡短尖峰流量可能會導致大幅延遲如果要求記錄基礎結構開始在負載變慢的要求。

Azure 事件集線器被為了輸入實在太多了大量資料，與容量處理最高數字的事件，比 HTTP 數要求大部分的 Api 程序。 [事件] 中心做為一種複雜緩衝 API 管理服務之間的基礎結構，就會儲存及處理郵件。 如此一來，可確保您的 API 效能會不會受因為記錄基礎結構。  

一旦將資料已傳遞至事件中心，並保存，且會等待事件中心消費者進行處理。 [事件] 中心會忽略如何處理，它只則確保已成功傳遞郵件。     

事件集線器有多個消費者群組的資料流事件的能力。 這個選項可讓處理完全其他系統的事件。 如此，但不放上處理 API 邀請 API 管理服務中的加法延遲，只有一個事件需求產生支援許多整合案例。

## <a name="a-policy-to-send-applicationhttp-messages"></a>若要傳送的郵件應用程式/http 原則
事件中心接受事件資料，以簡單的字串。 該字串的內容完全是之外。 若要能夠封裝設定 HTTP 要求，並將其傳送到事件我們需要設定格式的字串的要求或回應的資訊。 情況下，像這樣，是否有現有的格式我們可以重複使用，，然後我們可能不會使撰寫自己剖析程式碼。 一開始我被視為使用[哈](http://www.softwareishard.com/blog/har-12-spec/)傳送 HTTP 邀請和回應。 不過，此格式最適合以 JSON 基礎格式儲存一系列 HTTP 要求。 它包含必要的項目新增不必要的複雜的網路連線傳遞 HTTP 訊息的案例的數字。  

若要使用的替代選項已`application/http`媒體類型 HTTP 規格[RFC 7230](http://tools.ietf.org/html/rfc7230)所述。 此媒體類型使用完全相同的格式，可用來實際傳送 HTTP 訊息，透過網路，但是整個郵件放置在另一個 HTTP 邀請內文中。 在此例中我們只要傳送給事件集線器作為訊息的本文。 在此便利地，有[Microsoft ASP.NET 網頁 API 2.2 用](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)文件庫可以剖析此格式，並將其轉換成原生中存在的剖析器`HttpRequestMessage`和`HttpResponseMessage`物件。

若要建立這則訊息，我們需要利用 C# 依據 Azure API 管理[原則的運算式](https://msdn.microsoft.com/library/azure/dn910913.aspx)。 以下是將 HTTP 要求訊息傳送至 Azure 事件集線器原則。

       <log-to-eventhub logger-id="conferencelogger" partition-id="0">
       @{
           var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                       context.Request.Method,
                                                       context.Request.Url.Path + context.Request.Url.QueryString);

           var body = context.Request.Body?.As<string>(true);
           if (body != null && body.Length > 1024)
           {
               body = body.Substring(0, 1024);
           }

           var headers = context.Request.Headers
                                  .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                                  .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                  .ToArray<string>();

           var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

           return "request:"   + context.Variables["message-id"] + "\n"
                               + requestLine + headerString + "\r\n" + body;
       }
       </log-to-eventhub>

### <a name="policy-declaration"></a>原則宣告
那里幾個值得有關此原則運算式的特定項目。 記錄檔-eventhub 原則有記錄器識別碼參照到記錄器 API 管理服務中所建立的名稱] 屬性。 在文件中[如何記錄事件到 Azure 事件 Azure API 管理](api-management-howto-log-event-hubs.md)找如何設定在 API 管理服務事件中心記錄器的詳細資料。 第二個屬性是指示事件集線器分割來儲存郵件中的選擇性參數。 事件集線器會磁碟分割區以啟用 scalabilty 且需要兩個最小值。 磁碟分割內只保證排序的傳遞郵件。 如果我們不執行指示要將郵件的磁碟分割，它會使用循環演算法將負載事件中心。 不過，，可能會導致處理順序訊息的部分。  

### <a name="partitions"></a>磁碟分割區
若要確保我們的郵件會傳送到消費者順序，並利用的磁碟分割區的載入通訊功能，我選擇將 HTTP 要求訊息傳送至一個資料分割和第二個磁碟分割 HTTP 回覆郵件。 這樣就能確定偶數載入分配，我們可確保所有邀請會都耗用的順序，及所有回覆會都都耗用的順序。 可能要對應的邀請，再使用回應，但的並不是問題，我們已為不同的機制相互關聯的要求來回應，且我們知道要求永遠前面回應。

### <a name="http-payloads"></a>HTTP 裝載
建置之後`requestLine`我們檢查是否邀請本文應該會取至整數。 只 1024年會被要求本文。 這可能會增加，不過個別事件中心郵件限於 256 KB，因此可能部分 HTTP 郵件組織不符合單一郵件的會。 進行記錄與分析大量的資訊可以從 HTTP 要求列和標題。 此外，許多 API 邀請只傳回小型本文與遺失的資訊值截斷大型主體究竟是相當最小與降低傳輸、 處理及儲存空間的成本，讓所有本文內容。 關於處理本文最後一個注意事項是我們需要傳遞`true`要另存新檔<string>（） 方法因為我們正在閱讀本文內容，但也是要後端 API 都能閱讀本文。 將 true 變為這個方法傳遞我們會造成主體緩衝，讓它可以讀取一次。 這是很重要，必須注意如果您有不上傳的檔案很大，或使用長投票的 API。 在這種情況下是最好避免在閱讀本文。   

### <a name="http-headers"></a>HTTP 標頭
HTTP 標頭可以只是透過傳送到郵件格式的簡單索引鍵/值組格式。 我們已取出某些安全性機密欄位，若要避免不必要遺漏認證資訊。 不太的 API 金鑰與其他認證想要用於分析。 如果想要進行使用者及特定產品的分析，他們使用然後我們無法取得的從`context`物件並新增至郵件。     
### <a name="message-metadata"></a>郵件的中繼資料
建立時，傳送給 [事件] 中心內完整的訊息，不是第一行的實際部分`application/http`訊息。 第一行是由郵件是否要求或回應訊息及訊息識別碼用來建立關聯回覆要求額外的中繼資料。 郵件識別碼建立並使用其他的原則，看起來像這樣︰

    <set-variable name="message-id" value="@(Guid.NewGuid())" />

我們無法建立邀請郵件，直到回應已傳回，然後直接傳送邀請及回應為單一郵件的儲存在變數。 不過，獨立地傳送邀請及回應，然後使用郵件識別碼建立兩個之間的關係，我們取得更大的彈性郵件大小，以利用多個資料分割而維護訊息順序和邀請會出現在我們記錄儀表板更快的能力。 也可能有某些情況下，有效的回應永遠不會傳送至 [事件] 中心內，可能是因為 API 管理服務中，要求嚴重錯誤，但我們仍會有一筆記錄的要求。

傳送回應 HTTP 郵件的原則和要求的很類似，因此完成的原則設定看起來像這樣︰

      <policies>
        <inbound>
            <set-variable name="message-id" value="@(Guid.NewGuid())" />
            <log-to-eventhub logger-id="conferencelogger" partition-id="0">
              @{
                  var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                              context.Request.Method,
                                                              context.Request.Url.Path + context.Request.Url.QueryString);

                  var body = context.Request.Body?.As<string>(true);
                  if (body != null && body.Length > 1024)
                  {
                      body = body.Substring(0, 1024);
                  }

                  var headers = context.Request.Headers
                                       .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                                       .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                       .ToArray<string>();

                  var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

                  return "request:"   + context.Variables["message-id"] + "\n"
                                      + requestLine + headerString + "\r\n" + body;
              }
          </log-to-eventhub>
        </inbound>
        <backend>
            <forward-request follow-redirects="true" />
        </backend>
        <outbound>
            <log-to-eventhub logger-id="conferencelogger" partition-id="1">
              @{
                  var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                                      context.Response.StatusCode,
                                                      context.Response.StatusReason);

                  var body = context.Response.Body?.As<string>(true);
                  if (body != null && body.Length > 1024)
                  {
                      body = body.Substring(0, 1024);
                  }

                  var headers = context.Response.Headers
                                                  .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                                  .ToArray<string>();

                  var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

                  return "response:"  + context.Variables["message-id"] + "\n"
                                      + statusLine + headerString + "\r\n" + body;
             }
          </log-to-eventhub>
        </outbound>
      </policies>

`set-variable`原則會建立一個兩者都可以存取`log-to-eventhub`原則`<inbound>`區段和`<outbound>`] 區段。  

## <a name="receiving-events-from-event-hubs"></a>從事件集線器接收事件
使用[AMQP 通訊協定](http://www.amqp.org/)，會收到從 Azure 事件中心的事件。 Microsoft 服務匯流排小組進行用戶端才能更輕鬆地使用事件的文件庫。 有兩種不同的方法支援其中一個正在*直接消費者*與其他使用`EventProcessorHost`類別。 範例這兩種方法可以[事件集線器程式設計指南](../event-hubs/event-hubs-programming-guide.md)中找到。 精簡版的差異，`Direct Consumer`能提供完整的控制，以及`EventProcessorHost`會部分配管工作，但可讓特定假設您如何處理這些事件的。  

### <a name="eventprocessorhost"></a>EventProcessorHost
在這個範例中，我們會使用`EventProcessorHost`為了簡化，不過可能會無法這個案例的最佳選擇。 `EventProcessorHost`會辛苦完成的工作的確保您不必擔心執行緒特定事件處理器類別中的問題。 不過，在我們的案例中，我們會直接將郵件轉換成另一種格式，並將它傳遞到其他服務使用非同步方法。 必要的更新和共用的狀態，因此沒有的執行緒問題的風險。 在大部分情況下，`EventProcessorHost`可能是最佳選擇和一定是更容易的選項。     

### <a name="ieventprocessor"></a>IEventProcessor
使用時的核心概念`EventProcessorHost`是建立的實作`IEventProcessor`包含方法介面`ProcessEventAsync`。 方法如下所示︰

  非同步工作 IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages) {

           foreach (EventData eventData in messages)
           {
               _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

               try
               {
                   var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
                   await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
               }
               catch (Exception ex)
               {
                   _Logger.LogError(ex.Message);
               }
           }
            ... checkpointing code snipped ...
        }

EventData 物件清單傳遞到方法，我們逐一查看該清單。 每一種方法的位元組剖析為 HttpMessage 物件，且該物件傳遞給 IHttpMessageProcessor 的執行個體。

### <a name="httpmessage"></a>HttpMessage
`HttpMessage`執行個體包含資料的三種︰

      public class HttpMessage
       {
           public Guid MessageId { get; set; }
           public bool IsRequest { get; set; }
           public HttpRequestMessage HttpRequestMessage { get; set; }
           public HttpResponseMessage HttpResponseMessage { get; set; }

        ... parsing code snipped ...

      }

`HttpMessage`執行個體包含`MessageId`GUID，以讓我們將 HTTP 要求連接到對應 HTTP 回應和一個 boolean 值，識別如果物件包含 HttpRequestMessage 和 HttpResponseMessage 執行個體。 從 HTTP 類別中使用內建`System.Net.Http`，可以利用`application/http`剖析中所包含的程式碼`System.Net.Http.Formatting`。  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
`HttpMessage`執行個體再轉寄給實作`IHttpMessageProcessor`這是我所建立的減少接收並從 Azure 事件中心事件的解譯的實際的處理。


## <a name="forwarding-the-http-message"></a>轉寄 HTTP 訊息
此範例中，決定會透過發送 HTTP 要求， [Runscope](http://www.runscope.com)到感興趣。 Runscope 是雲端服務的 HTTP 偵錯、 記錄與監視。 有免費的層]，以便輕鬆嘗試，並可讓我們的 HTTP 中要求即時通過我們 API 管理的服務。

`IHttpMessageProcessor`實作看起來像這樣，

      public class RunscopeHttpMessageProcessor : IHttpMessageProcessor
       {
           private HttpClient _HttpClient;
           private ILogger _Logger;
           private string _BucketKey;
           public RunscopeHttpMessageProcessor(HttpClient httpClient, ILogger logger)
           {
               _HttpClient = httpClient;
               var key = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-KEY", EnvironmentVariableTarget.User);
               _HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", key);
               _HttpClient.BaseAddress = new Uri("https://api.runscope.com");
               _BucketKey = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-BUCKET", EnvironmentVariableTarget.User);
               _Logger = logger;
           }

           public async Task ProcessHttpMessage(HttpMessage message)
           {
               var runscopeMessage = new RunscopeMessage()
               {
                   UniqueIdentifier = message.MessageId
               };

               if (message.IsRequest)
               {
                   _Logger.LogInfo("Sending HTTP request " + message.MessageId.ToString());
                   runscopeMessage.Request = await RunscopeRequest.CreateFromAsync(message.HttpRequestMessage);
               }
               else
               {
                   _Logger.LogInfo("Sending HTTP response " + message.MessageId.ToString());
                   runscopeMessage.Response = await RunscopeResponse.CreateFromAsync(message.HttpResponseMessage);
               }

               var messagesLink = new MessagesLink() { Method = HttpMethod.Post };
               messagesLink.BucketKey = _BucketKey;
               messagesLink.RunscopeMessage = runscopeMessage;
               var runscopeResponse = await _HttpClient.SendAsync(messagesLink.CreateRequest());
               _Logger.LogDebug("Request sent to Runscope");
           }
       }

可以利用輕鬆推入[現有的用戶端文件庫的 Runscope](http://www.nuget.org/packages/Runscope.net.hapikit/0.9.0-alpha) `HttpRequestMessage`和`HttpResponseMessage`執行個體設定，將其服務。 存取 Runscope API 您需要帳戶與 API 金鑰。 [建立應用程式，以存取 Runscope API](http://blog.runscope.com/posts/creating-applications-to-access-the-runscope-api)影片中找取得 API 金鑰指示進行。

## <a name="complete-sample"></a>完整的範例
[程式碼](https://github.com/darrelmiller/ApimEventProcessor)，以測試樣本是 Github。 您必須[API 管理服務](api-management-get-started.md)、[連線的事件中心](api-management-howto-log-event-hubs.md)及[儲存帳戶](../storage/storage-create-storage-account.md)執行您自己的範例。   

樣本是只要簡單主控台應用程式接聽的事件來自 [事件] 中心內，轉換成`HttpRequestMessage`和`HttpResponseMessage`物件，然後再將它們轉寄哪 Runscope API。

在下列動畫圖像，您可以看到正在對 API 的主控台應用程式顯示已接收、 處理及轉寄郵件然後要求並回應 Runscope 流量偵測器中顯示開發人員入口網站的要求。

![要求被轉寄給 Runscope 的示範](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>摘要
Azure API 管理服務，提供擷取往來您 Api HTTP 傳輸理想的位置。 Azure 事件集線器擷取資料的彈性，低成本解決方案，而該裝入記錄的第二個處理系統的監控和其他複雜的分析。 連線到第 3 監控系統等 Runscope 為數十個少量的程式碼是一個簡單的廠商流量。

## <a name="next-steps"></a>後續步驟
-   深入瞭解 Azure 事件集線器
    -   [Azure 事件集線器快速入門](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
    -   [接收郵件] 使用 EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost)
    -   [事件集線器程式設計指南](../event-hubs/event-hubs-programming-guide.md)
-   深入瞭解 API 管理和事件集線器整合
    -   [如何將事件記錄到 Azure 事件集線器 Azure API 管理](api-management-howto-log-event-hubs.md)
    -   [記錄器實體參考](https://msdn.microsoft.com/library/azure/mt592020.aspx)
    -   [記錄檔-eventhub 原則參照](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
    