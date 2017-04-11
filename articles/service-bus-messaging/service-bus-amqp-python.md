<properties 
    pageTitle="服務匯流排和與 AMQP 1.0 Python |Microsoft Azure"
    description="使用 AMQP 從 Python 服務匯流排。"
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
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="using-service-bus-from-python-with-amqp-10"></a>從 Python 服務匯流排使用 AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton Python 是 Python 語言繫結至 Proton C。就是 Proton Python 被當作包裝引擎實作於 c。

## <a name="download-the-proton-client-library"></a>下載 Proton 用戶端文件庫

您可以從[http://qpid.apache.org/download.html](http://qpid.apache.org/download.html)下載 Proton C 和它相關聯的連結 （包括 Python）。 下載位於來源程式碼的表單。 若要建置程式碼，請遵循下載套件中所包含的指示。

請注意，在撰寫時，在 Proton C SSL 支援只適用於 Linux 作業系統。 Azure 服務匯流排需要使用 SSL，因為 Proton C （和語言繫結） 可以只會用來存取服務匯流排從 Linux 這一次。 若要啟用 Proton-C ssl Windows 上的工作是進行中，核取返回常見問題的更新。

## <a name="working-with-service-bus-queues-topics-and-subscriptions-from-python"></a>使用服務匯流排佇列主題，以及從 Python 的訂閱

下列程式碼會示範如何傳送及接收郵件，從服務匯流排訊息實體。

### <a name="send-messages-using-proton-python"></a>使用 Proton Python 傳送郵件

下列程式碼會示範如何傳送訊息給服務匯流排訊息實體。

```
messenger = Messenger()
message = Message()
message.address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"

message.body = u"This is a text string"
messenger.put(message)
messenger.send()
```

### <a name="receive-messages-using-proton-python"></a>接收郵件使用 Proton Python

下列程式碼會顯示如何從服務匯流排接收郵件訊息的實體。

```
messenger = Messenger()
address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"
messenger.subscribe(address)

messenger.start()
messenger.recv(1)
message = Message()
if messenger.incoming:
      messenger.get(message)
messenger.stop()
```

## <a name="messaging-between-net-and-proton-python"></a>.NET 和 Proton Python 之間訊息

### <a name="application-properties"></a>應用程式屬性

#### <a name="proton-python-to-service-bus-net-apis"></a>Proton-Python 服務匯流排.NET Api

Proton Python 郵件支援下列類型的應用程式屬性︰ **int**、**長**、**浮動時間**、 **uuid**、 **bool**、**字串**。 下列 Python 程式碼會顯示如何使用每個內容類型設定郵件的內容。

```
message.properties[u"TestString"] = u"This is a string"    
message.properties[u"TestInt"] = 1
message.properties[u"TestLong"] = 1000L
message.properties[u"TestFloat"] = 1.5    
message.properties[u"TestGuid"] = uuid.uuid1()    
```

在服務匯流排.NET API，郵件應用程式屬性都會套用[BrokeredMessage][] **Properties**集合中。 下列程式碼會顯示如何讀取的郵件，自 Python 用戶端應用程式內容。

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}
```

下表會對應到.NET 內容類型的 Python 內容類型。

| Python 的內容類型 | .NET 的內容類型 |
|----------------------|--------------------|
| int                  | int                |
| 浮動時間                | 雙引號             |
| 長                 | int64              |
| uuid                 | guid               |
| bool                 | bool               |
| 字串               | 字串             |

#### <a name="service-bus-net-apis-to-proton-python"></a>若要 Proton Python 服務匯流排.NET Api

[BrokeredMessage][]支援下列類型的應用程式屬性︰**位元組**、 **sbyte**、 **char**、**簡短**、 **ushort**、 **int**、 **uint**、**長**、 **ulong**、**浮動時間**、**雙**、**小數位數**、 **bool**、 **Guid**、**字串**、 **Uri**、 **DateTime**、 **DateTimeOffset**和**時段**。 下列.NET 程式碼會顯示如何設定使用每個內容類型的[BrokeredMessage][]物件屬性。

```
message.Properties["TestByte"] = (byte)128;
message.Properties["TestSbyte"] = (sbyte)-22;
message.Properties["TestChar"] = (char) 'X';
message.Properties["TestShort"] = (short)-12345;
message.Properties["TestUshort"] = (ushort)12345;
message.Properties["TestInt"] = (int)-100;
message.Properties["TestUint"] = (uint)100;
message.Properties["TestLong"] = (long)-12345;
message.Properties["TestUlong"] = (ulong)12345;
message.Properties["TestFloat"] = (float)3.14159;
message.Properties["TestDouble"] = (double)3.14159;
message.Properties["TestDecimal"] = (decimal)3.14159;
message.Properties["TestBoolean"] = true;
message.Properties["TestGuid"] = Guid.NewGuid();
message.Properties["TestString"] = "Service Bus";
message.Properties["TestUri"] = new Uri("http://www.bing.com");
message.Properties["TestDateTime"] = DateTime.Now;
message.Properties["TestDateTimeOffSet"] = DateTimeOffset.Now;
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

下列 Python 程式碼會顯示如何讀取的郵件，自服務匯流排.NET 用戶端應用程式內容。

```
if message.properties != None:
   for k,v in message.properties.items():         
         print "--   %s : %s (%s)" % (k, str(v), type(v))         
```

下表會對應到 Python 內容類型的.NET 屬性類型。

| .NET 的內容類型 | Python 的內容類型 | 備忘稿                                                                                                                                                               |
|--------------------|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 位元組               | int                  | -                                                                                                                                                                     |
| sbyte              | int                  | -                                                                                                                                                                     |
| 字元               | 字元                 | Proton Python 類別                                                                                                                                                 |
| 簡短              | int                  | -                                                                                                                                                                     |
| ushort             | int                  | -                                                                                                                                                                     |
| int                | int                  | -                                                                                                                                                                     |
| uint               | int                  | -                                                                                                                                                                     |
| 長               | int                  | -                                                                                                                                                                     |
| ulong              | 長                 | Proton Python 類別                                                                                                                                                 |
| 浮動時間              | 浮動時間                | -                                                                                                                                                                     |
| 雙引號             | 浮動時間                | -                                                                                                                                                                     |
| 小數位數            | 字串               | 小目前不支援使用 Proton。                                                                                                                     |
| bool               | bool                 | -                                                                                                                                                                     |
| Guid               | uuid                 | Proton Python 類別                                                                                                                                                 |
| 字串             | 字串               | -                                                                                                                                                                     |
| 日期時間           | 時間戳記            | Proton Python 類別                                                                                                                                                 |
| DateTimeOffset     | DescribedType        | DateTimeOffset.UtcTicks 對應到 AMQP 類型︰<type name=”datetime-offset” class=restricted source=”long”><descriptor name=”com.microsoft:datetime-offset” /></type> |
| 時段           | DescribedType        | Timespan.Ticks 對應到 AMQP 類型︰<type name=”timespan” class=restricted source=”long”><descriptor name=”com.microsoft:timespan” /></type>                        |
| Uri                | DescribedType        | Uri.AbsoluteUri 對應到 AMQP 類型︰<type name=”uri” class=restricted source=”string”><descriptor name=”com.microsoft:uri” /></type>                               |

### <a name="standard-properties"></a>[標準摘要資訊

下表顯示 Proton Python 標準訊息內容和[BrokeredMessage][]標準訊息內容之間的對應。

#### <a name="proton-python-to-service-bus-net-apis"></a>Proton-Python 服務匯流排.NET Api

| Proton Python        | 服務匯流排.NET         | 備忘稿                                                     |
|----------------------|--------------------------|-----------------------------------------------------------|
| 長期              | n/a                      | 服務匯流排只支援長期的郵件。               |
| 優先順序             | n/a                      | 服務匯流排只支援的單一郵件的優先順序。      |
| [Ttl]                  | Message.TimeToLive       | 轉換，Proton Python TTL 定義以毫秒為單位。 |
| 第一個\_獲取者      | n/a                      | -                                                           |
| 傳遞\_計數      | n/a                      | -                                                           |
| 識別碼                   | Message.MessageID        | -                                                           |
| 使用者\_識別碼             | n/a                      | -                                                           |
| 地址              | Message.To               | -                                                           |
| 主旨              | Message.Label            | -                                                           |
| 回覆\_至            | Message.ReplyTo          | -                                                           |
| 相互關聯\_識別碼      | Message.CorrelationID    | -                                                           |
| 內容\_類型        | Message.ContentType      | -                                                           |
| 內容\_編碼    | n/a                      | -                                                           |
| 到期\_時間         | n/a                      | -                                                           |
| 建立\_時間       | n/a                      | -                                                           |
| 群組\_識別碼            | Message.SessionId        | -                                                           |
| 群組\_順序      | n/a                      | -                                                           |
| 回覆\_至\_群組\_識別碼 | Message.ReplyToSessionId | -                                                           |
| 格式               | n/a                      | -                                                           |

| 服務匯流排.NET        | Proton                       | 備忘稿                                                     |
|-------------------------|------------------------------|-----------------------------------------------------------|
| ContentType             | Message.content\_類型        | -                                                           |
| 相互關聯識別碼           | Message.correlation\_識別碼      | -                                                           |
| EnqueuedTimeUtc         | n/a                          | -                                                           |
| 標籤                   | Message.subject              | -                                                           |
| 訊息               | Message.id                   | -                                                           |
| ReplyTo                 | Message.reply\_至            | -                                                           |
| ReplyToSessionId        | Message.reply\_至\_群組\_識別碼 | -                                                           |
| ScheduledEnqueueTimeUtc | n/a                          | -                                                           |
| 工作階段識別碼               | Message.group\_識別碼            | -                                                           |
| TimeToLive              | Message.ttl                  | 轉換，Proton Python TTL 定義以毫秒為單位。 |
| 若要                      | Message.address              | -                                                           |

## <a name="next-steps"></a>後續步驟

準備瞭解更多嗎？ 請參考下列連結︰

- [服務匯流排 AMQP 概觀]
- [在 Windows Server 的服務匯流排 AMQP]

[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[在 Windows Server 的服務匯流排 AMQP]: https://msdn.microsoft.com/library/dn574799.aspx

[服務匯流排 AMQP 概觀]: service-bus-amqp-overview.md
