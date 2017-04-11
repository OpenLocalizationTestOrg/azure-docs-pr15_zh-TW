<properties 
    pageTitle="服務匯流排.NET 與 AMQP 1.0 |Microsoft Azure"
    description="從.NET 服務匯流排使用 AMQP"
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
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="using-service-bus-from-net-with-amqp-10"></a>從.NET 服務匯流排使用 AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

## <a name="downloading-the-service-bus-sdk"></a>下載服務匯流排 SDK

使用服務匯流排 SDK 2.1 或更新版本中 AMQP 1.0 支援。 您可以確保您可以下載[NuGet][]服務匯流排位元的最新版本。

## <a name="configuring-net-applications-to-use-amqp-10"></a>設定使用 AMQP 1.0.NET 應用程式

根據預設，服務匯流排.NET 用戶端文件庫與服務匯流排服務使用 SOAP 為基礎的專用通訊協定進行通訊。 若要使用 AMQP 1.0，而不是預設的通訊協定需要明確設定服務匯流排連接字串下, 一節所述。 這項變更，非使用 AMQP 1.0 時，則維持不變應用程式碼。

在目前的版本，有幾個使用 AMQP 時不受支援的 API 功能。 [不支援的功能、 限制以及行為差異](#unsupported-features-restrictions-and-behavioral-differences)] 區段中，這些不受支援的功能會列出更新版本。 某些進階的組態設定也有不同的意義，使用 AMQP 時。

### <a name="configuration-using-appconfig"></a>使用 App.config 的設定

最好的應用程式使用 App.config 設定檔案儲存設定。 對於服務匯流排應用程式，您可以使用 App.config 儲存服務匯流排**連接字串**值的設定。 範例 App.config 檔案如下所示︰

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
        </appSettings>
    </configuration>

值的`Microsoft.ServiceBus.ConnectionString`設定，則用來設定連線到服務匯流排服務匯流排連線字串。 格式如下所示︰

    Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp

位置`[namespace]`和`SharedAccessKey`從[Azure 入口網站][]取得。 如需詳細資訊，請參閱[開始使用服務匯流排佇列][]。

使用時 AMQP，附加的連線字串`;TransportType=Amqp`。 這個標記法通知，讓它的連線到服務匯流排使用 AMQP 1.0 的用戶端文件庫。

## <a name="message-serialization"></a>訊息序列化

使用預設的通訊協定，.NET 用戶端文件庫的預設序列化行為時使用[DataContractSerializer][]類型序列化傳輸的用戶端文件庫與服務匯流排服務之間的[BrokeredMessage][]執行個體。 使用 AMQP 傳輸模式時，用戶端文件庫會使用 AMQP 類型系統序列化的[仲介的訊息][BrokeredMessage]到 AMQP 訊息。 此序列化可讓郵件接收和解譯接收的應用程式可能不同的平台，例如，使用 JMS API 存取服務匯流排 Java 應用程式上執行。

當您建構[BrokeredMessage][]執行個體時，您可以提供.NET 物件做為參數，做為郵件的本文建構函式。 對應到 AMQP 基本類型的物件，本文序列化為 AMQP 資料類型。 如果物件無法直接對應到 AMQP 基本的類型。也就是說，定義應用程式，然後使用[DataContractSerializer][]和序列的位元組序列化物件的自訂類型是 AMQP 資料訊息中傳送。

若要加快與非.NET 用戶端的互通性，使用可直接將郵件的本文 AMQP 類型序列化.NET 類型。 下列表格詳細說明這些類型以及相對應的對應到 AMQP 類型系統。

| .NET 本文物件類型          | 對應的 AMQP 類型                          | AMQP 本文區段類型                                                                                                                                    |
|--------------------------------|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| bool                           | 布林值                                   | AMQP 值                                                                                                                                                |
| 位元組                           | ubyte                                     | AMQP 值                                                                                                                                                |
| ushort                         | ushort                                    | AMQP 值                                                                                                                                                |
| uint                           | uint                                      | AMQP 值                                                                                                                                                |
| ulong                          | ulong                                     | AMQP 值                                                                                                                                                |
| sbyte                          | 位元組                                      | AMQP 值                                                                                                                                                |
| 簡短                          | 簡短                                     | AMQP 值                                                                                                                                                |
| int                            | int                                       | AMQP 值                                                                                                                                                |
| 長                           | 長                                      | AMQP 值                                                                                                                                                |
| 浮動時間                          | 浮動時間                                     | AMQP 值                                                                                                                                                |
| 雙引號                         | 雙引號                                    | AMQP 值                                                                                                                                                |
| 小數位數                        | decimal128                                | AMQP 值                                                                                                                                                |
| 字元                           | 字元                                      | AMQP 值                                                                                                                                                |
| 日期時間                       | 時間戳記                                 | AMQP 值                                                                                                                                                |
| Guid                           | uuid                                      | AMQP 值                                                                                                                                                |
| byte]                         | 二進位                                    | AMQP 值                                                                                                                                                |
| 字串                         | 字串                                    | AMQP 值                                                                                                                                                |
| System.Collections.IList       | 清單                                      | AMQP 值︰ 集合中包含的項目只能定義此表格中。                                                             |
| System.Array                   | 陣列                                     | AMQP 值︰ 集合中包含的項目只能定義此表格中。                                                             |
| System.Collections.IDictionary | 地圖                                       | AMQP 值︰ 集合中包含的項目只能定義此表格中。注意︰ 只有字串金鑰受支援。                        |
| Uri                            | 說明字串 （請參閱下表） | AMQP 值                                                                                                                                                |
| DateTimeOffset                 | 長描述 （請參閱下表）   | AMQP 值                                                                                                                                                |
| 時段                       | 長描述 （請參閱下列）         | AMQP 值                                                                                                                                                |
| 串流                         | 二進位                                    | AMQP 資料 （可能是多個）。 資料區段包含從資料流物件讀取的原始位元組。                                                           |
| 其他物件                   | 二進位                                    | AMQP 資料 （可能是多個）。 包含序列的二進位使用 DataContractSerializer 或應用程式提供序列化程式的物件。 |

| .NET 類型      | 對應的 AMQP 所述的類型                                                                                              | 備忘稿                   |
|----------------|-------------------------------------------------------------------------------------------------------------------------|-------------------------|
| Uri            | `<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>`                       | Uri.AbsoluteUri         |
| DateTimeOffset | `<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` | DateTimeOffset.UtcTicks |
| 時段       | `<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> `              | TimeSpan.Ticks          |

## <a name="unsupported-features-restrictions-and-behavioral-differences"></a>不支援的功能、 限制以及行為差異

下列功能的服務匯流排.NET API 目前不支援使用 AMQP 時︰

-   交易

-   透過傳輸目的地傳送

也有一些小型差異服務匯流排.NET API 的行為，使用 AMQP，相較於預設的通訊協定時︰

-   忽略[OperationTimeout][]屬性。

-   `MessageReceiver.Receive(TimeSpan.Zero)`被當作`MessageReceiver.Receive(TimeSpan.FromSeconds(10))`。

-   鎖定權杖所完成的郵件可以才最初收到郵件的郵件接收器。

## <a name="controlling-amqp-protocol-settings"></a>控制 AMQP 通訊協定設定

.NET Api 公開了數個設定來控制 AMQP 通訊協定的行為︰

-   **MessageReceiver.PrefetchCount**︰ 控制初始光榮成果套用至] 連結。 預設值為 0。

-   **MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize**︰ 控制項 AMQP 框架大小上限提供在連線交涉期間開啟時間。 預設值為 65536 位元組。

-   **MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval**︰ 如果傳輸 batchable，此值會決定傳送配置的最大的延遲。 依預設繼承的寄件者/接收器。 個別的寄件者/收件者可以覆寫預設值，也就是 20 毫秒。

-   **MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity**︰ 控制是否透過 SSL 連線建立 AMQP 連線。 預設值為**true**。

## <a name="next-steps"></a>後續步驟

準備瞭解更多嗎？ 請參考下列連結︰

- [服務匯流排 AMQP 概觀]
- [AMQP 1.0 支援服務匯流排分割佇列 」 和 「 主題]
- [在 Windows Server 的服務匯流排 AMQP]

  [開始使用服務匯流排佇列]: service-bus-dotnet-get-started-with-queues.md
  [DataContractSerializer]: https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: https://msdn.microsoft.com/library/azure/jj657638.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)]: https://msdn.microsoft.com/library/azure/jj657703.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure 入口網站]: https://portal.azure.com
[服務匯流排 AMQP 概觀]: service-bus-amqp-overview.md
[AMQP 1.0 支援服務匯流排分割佇列 」 和 「 主題]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[在 Windows Server 的服務匯流排 AMQP]: https://msdn.microsoft.com/library/dn574799.aspx
