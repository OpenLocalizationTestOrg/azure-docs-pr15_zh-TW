下表列出服務匯流排訊息的特定的配額資訊。 事件集線器限制會包含在此表格中，但事件集線器更具體的資訊，請參閱[事件集線器價格](https://azure.microsoft.com/pricing/details/event-hubs/)。 其他服務匯流排配額和價格資訊，請參閱[服務匯流排價格](https://azure.microsoft.com/pricing/details/service-bus/)的概觀。

|配額名稱|範圍|類型|超過時的行為|值|
|---|---|---|---|---|
| 最大的數字的每個 Azure 訂閱的命名空間|命名空間|靜態|其他命名空間的後續要求會被拒絕入口網站。|100|
|佇列/主題大小|實體|定義在佇列中/主題的建立。|內送郵件都會被拒絕和例外狀況會收到呼叫程式碼。|1、 2、 3、 4 或 5 GB。<br /><br />如果已啟用[分割](service-bus-partitioning.md)，最大的佇列中主題大小會與 80 GB。|
|命名空間同時連線的數目|命名空間|靜態|其他連線的後續要求會被拒絕和例外狀況會收到呼叫程式碼。 其他作業不計於同時 TCP 連線。|NetMessaging: 1000<br /><br />AMQP: 5000|
|在佇列中/主題訂閱實體上同時連線的數目|實體|靜態|其他連線的後續要求會被拒絕和例外狀況會收到呼叫程式碼。 其他作業不計於同時 TCP 連線。|長，每個命名空間同時連線的限制。|
|並行數收到邀請佇列中/主題訂閱實體|實體|靜態|後續收到要求會被拒絕和例外狀況會收到呼叫程式碼。 此配額適用於合併數並行主題的所有訂閱接收作業。|5000|
|每個服務命名空間的主題/佇列數目|系統|靜態|建立新的主題或服務命名空間佇列中的後續要求會被拒絕。 因此，如果透過[Azure 入口網站][]設定，將會產生錯誤訊息。 如果呼叫從管理 API，呼叫的程式碼將會收到例外狀況。|10000<br /><br />主題加上佇列中的服務命名空間的總數必須小於或等於 10000。<br/>為所有項目分割不用於進階版。|
|分割主題/佇列每個服務命名空間的數字|系統|靜態|建立新的分割的主題或服務命名空間佇列中的後續要求會被拒絕。 因此，如果透過[Azure 入口網站][]設定，將會產生錯誤訊息。 如果呼叫從管理 API，呼叫的程式碼將會收到**QuotaExceededException**例外狀況。|基本和標準層-100<br />進階版-1000<br/><br />每個分割佇列中或主題計算的每個命名空間 10000 實體配額根據。|
|任何訊息的實體路徑的大小上限︰ 佇列或主題|實體|靜態|-|260 個字元|
|任何訊息的實體名稱的大小上限︰ 命名空間，訂閱、 訂閱規則或事件中心|實體|靜態|-|50 個字元|
|事件集線器事件的大小上限|系統|靜態|-|256 KB|
|郵件大小的佇列/主題訂閱實體|系統|靜態|超過這些配額的內送郵件都會被拒絕和例外狀況會收到呼叫程式碼。|郵件大小上限︰ 256 KB （[標準層](../articles/service-bus/service-bus-premium-messaging.md)） / 1 MB （[Premium 層](../articles/service-bus/service-bus-premium-messaging.md)）。 <br /><br />**附註**系統負荷，因為這項限制通常是少一點。<br /><br />最大的標頭大小︰ 64 KB<br /><br />最大的數個屬性包中的標題屬性︰**位元組 int。MaxValue**<br /><br />最大值] 屬性中屬性包︰ 沒有明確的限制。 受限於最大的標頭大小。|
|佇列/主題訂閱實體訊息屬性大小|系統|靜態|會產生**SerializationException**例外。|屬性的郵件大小上限每個屬性是 32 K。 所有屬性的累計大小不得超過 64k。 這會套用至整個標頭[BrokeredMessage](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.aspx)，同時有哪些使用者屬性，以及系統內容 （例如[SequenceNumber](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.sequencenumber.aspx)、[標籤](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.label.aspx)、[訊息](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx)等等）。|
|主題每個訂閱的數字|系統|靜態|用於建立主題的其他訂閱的後續要求會被拒絕。 因此，如果透過入口網站設定，就會顯示錯誤訊息。 如果從管理 API 呼叫呼叫的程式碼將會收到例外狀況。|2000|
|每個主題 SQL 篩選數目|系統|靜態|建立主題上的其他篩選器的後續要求會被拒絕和例外狀況會收到呼叫程式碼。|2000|
|相互關聯的數字篩選每主題|系統|靜態|建立主題上的其他篩選器的後續要求會被拒絕和例外狀況會收到呼叫程式碼。|100000|
|SQL 篩選器/動作的大小|系統|靜態|其他篩選器建立的後續要求會被拒絕和例外狀況會收到呼叫程式碼。|篩選條件字串的長度上限︰ 最低 1024 (1 K)。<br /><br />規則動作字串的長度上限︰ 最低 1024 (1 K)。<br /><br />每個規則執行動作的運算式的最大數目︰ 32。|
|每個命名空間、 佇列中或主題[SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)規則的數目|命名空間的實體|靜態|建立其他規則的後續要求會被拒絕和例外狀況會收到呼叫程式碼。|最大的數字的規則︰ 12。 <br /><br /> 服務匯流排命名空間設定的規則套用至所有佇列及該命名空間中的主題。

[Azure 入口網站]: https://portal.azure.com