<properties 
    pageTitle="服務匯流排和與 AMQP 1.0 PHP |Microsoft Azure"
    description="使用 AMQP 從 PHP 服務匯流排。"
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

# <a name="using-service-bus-from-php-with-amqp-10"></a>從 PHP 服務匯流排使用 AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton PHP 是 PHP 語言繫結至 Proton C。就是 Proton PHP 被當作包裝引擎實作於 c。

## <a name="downloading-the-proton-client-library"></a>下載 Proton 用戶端文件庫

您可以從[http://qpid.apache.org/download.html](http://qpid.apache.org/download.html)下載 Proton C 和它相關聯的連結 （包括 PHP）。 下載位於來源程式碼的表單。 若要建置程式碼，請遵循下載套件中所包含的指示。

> [AZURE.IMPORTANT] 在撰寫時，在 Proton C SSL 支援只適用於 Linux 作業系統。 Azure 服務匯流排需要使用 SSL，因為 Proton C （和語言繫結） 可以只會用來存取服務匯流排從 Linux 這一次。 若要啟用 Proton-C ssl Windows 上的工作正在進行中，所以請返回常見問題的更新。

## <a name="working-with-service-bus-queues-topics-and-subscriptions-from-php"></a>使用服務匯流排佇列主題，以及從 PHP 的訂閱

下列程式碼會示範如何傳送及接收郵件，從服務匯流排訊息實體。

### <a name="sending-messages-using-proton-php"></a>使用 Proton PHP 傳送郵件

下列程式碼會示範如何傳送訊息給服務匯流排訊息實體。

```
$messenger = new Messenger();
$message = new Message();
$message->address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";

$message->body = "This is a text string";
$messenger->put($message);
$messenger->send();
```

### <a name="receiving-messages-using-proton-php"></a>接收使用 Proton PHP 的訊息

下列程式碼會顯示如何從服務匯流排接收郵件訊息的實體。

```
$messenger = new Messenger();
$address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";
$messenger->subscribe($address);

$messenger->start();
$messenger->recv(1);

if($messenger->incoming())
{
   $message = new Message();
   $messenger->get($message);      
}

$messenger->stop();
```

## <a name="messaging-between-net-and-proton-php"></a>.NET 和 Proton PHP 之間訊息

### <a name="application-properties"></a>應用程式屬性

#### <a name="protonphp-to-service-bus-net-apis"></a>若要服務匯流排.NET Api ProtonPHP

Proton PHP 郵件支援下列類型的應用程式屬性︰**整數**、**雙**、**布林值**、**字串**，以及**物件**。 下列 PHP 程式碼會顯示如何使用每個內容類型設定郵件的內容。

```
$message->properties["TestInt"] = 1;    
$message->properties["TestDouble"] = 1.5;      
$message->properties["TestBoolean"] = False;
$message->properties["TestString"] = "Service Bus";    
$message->properties["TestObject"] = new UUID("1234123412341234");   
```

在服務匯流排.NET Api，郵件應用程式屬性都會套用[BrokeredMessage][] **Properties**集合中。 下列程式碼會顯示如何讀取的郵件，自 PHP 用戶端應用程式內容。

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}if (message.Properties.Keys.Count > 0)
{
foreach (string name in message.Properties.Keys)
{
  Object value = message.Properties[name];
  Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
}
Console.WriteLine();
}
```

下表會對應到.NET 內容類型的 PHP 內容類型。

| PHP 的內容類型 | .NET 的內容類型 |
|-------------------|--------------------|
| 整數           | int                |
| 雙引號            | 雙引號             |
| 布林值           | bool               |
| 字串            | 字串             |
| 物件            | 物件             |

#### <a name="service-bus-net-apis-to-php"></a>服務匯流排.NET Api php

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
```

下列 PHP 程式碼會顯示如何讀取的郵件，自服務匯流排.NET 用戶端應用程式內容。

```
if ($message->properties != null)
{
  foreach($message->properties as $key => $value)
  {
    printf("-- %s : %s (%s) \n", $key, $value, gettype($value));                       
  }         
}
```

下表會對應到 PHP 內容類型的.NET 屬性類型。

| .NET 的內容類型 | PHP 的內容類型 | 備忘稿                                                                                                                                                               |
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 位元組               | 整數           | -                                                                                                                                                                     |
| sbyte              | 整數           | -                                                                                                                                                                     |
| 字元               | 字元              | Proton PHP 類別                                                                                                                                                    |
| 簡短              | 整數           | -                                                                                                                                                                     |
| ushort             | 整數           | -                                                                                                                                                                     |
| int                | 整數           | -                                                                                                                                                                     |
| uint               | 整數           | -                                                                                                                                                                     |
| 長               | 整數           | -                                                                                                                                                                     |
| ulong              | 整數           | -                                                                                                                                                                     |
| 浮動時間              | 雙引號            | -                                                                                                                                                                     |
| 雙引號             | 雙引號            | -                                                                                                                                                                     |
| 小數位數            | 字串            | 小目前不支援使用 Proton。                                                                                                                     |
| bool               | 布林值           | -                                                                                                                                                                     |
| Guid               | UUID              | Proton PHP 類別                                                                                                                                                    |
| 字串             | 字串            | -                                                                                                                                                                     |
| 日期時間           | 整數           | -                                                                                                                                                                     |
| DateTimeOffset     | DescribedType     | DateTimeOffset.UtcTicks 對應到 AMQP 類型︰<type name="datetime-offset" class=restricted source="long"><descriptor name="com.microsoft:datetime-offset" /></type> |
| 時段           | DescribedType     | Timespan.Ticks 對應到 AMQP 類型︰<type name="timespan" class=restricted source="long"><descriptor name="com.microsoft:timespan" /></type>                        |
| Uri                | DescribedType     | Uri.AbsoluteUri 對應到 AMQP 類型︰<type name="uri" class=restricted source="string"><descriptor name="com.microsoft:uri" /></type>                               |

### <a name="standard-properties"></a>[標準摘要資訊

下表顯示 Proton PHP 標準訊息內容和[BrokeredMessage][]標準訊息內容之間的對應。

| Proton PHP           | 服務匯流排.NET         | 備忘稿                                                    |
|----------------------|--------------------------|----------------------------------------------------------|
| 長期              | n/a                      | 服務匯流排只支援長期的郵件。          |
| 優先順序             | n/a                      | 服務匯流排只支援的單一郵件的優先順序。 |
| [Ttl]                  | Message.TimeToLive       | 轉換，Proton PHP TTL 定義以毫秒為單位。   |
| 第一個\_獲取者      | -                          | -                                                          |
| 傳遞\_計數      | -                          | -                                                          |
| 識別碼                   | Message.Id               | -                                                          |
| 使用者\_識別碼             | -                          | -                                                          |
| 地址              | Message.To               | -                                                          |
| 主旨              | Message.Label            | -                                                          |
| 回覆\_至            | Message.ReplyTo          | -                                                          |
| 相互關聯\_識別碼      | Message.CorrelationId    | -                                                          |
| 內容\_類型        | Message.ContentType      | -                                                          |
| 內容\_編碼    | n/a                      | -                                                          |
| 到期\_時間         | Message.ExpiresAtUTC     | -                                                          |
| 建立\_時間       | n/a                      | -                                                          |
| 群組\_識別碼            | Message.SessionId        | -                                                          |
| 群組\_順序      | -                          | -                                                          |
| 回覆\_至\_群組\_識別碼 | Message.ReplyToSessionId | -                                                          |
| 格式               | n/a                      | -

#### <a name="service-bus-net-apis-to-proton-php"></a>服務匯流排.NET Api Proton php

| 服務匯流排.NET        | Proton PHP                                             | 備忘稿                                                  |
|-------------------------|--------------------------------------------------------|--------------------------------------------------------|
| ContentType             | 訊息-\>內容\_類型                                | -                                                        |
| 相互關聯識別碼           | 訊息-\>相互關聯\_識別碼                              | -                                                        |
| EnqueuedTimeUtc         | 訊息-\>註解 [x-選擇-佇列-時間]             | -                                                        |
| 標籤                   | 訊息-\>主旨                                      | -                                                        |
| 訊息               | 訊息-\>識別碼                                           | -                                                        |
| ReplyTo                 | 訊息-\>回覆\_至                                    | -                                                        |
| ReplyToSessionId        | 訊息-\>回覆\_至\_群組\_識別碼                         | -                                                        |
| ScheduledEnqueueTimeUtc | 訊息-\>註解 [「 x-選擇-排程-佇列中的時間 」] | -                                                        |
| 工作階段識別碼               | 訊息-\>群組\_識別碼                                    | -                                                        |
| TimeToLive              | 訊息-\>ttl                                          | 轉換，Proton PHP TTL 定義以毫秒為單位。 |
| 若要                      | 訊息-\>地址                                      | -                                                        |

## <a name="next-steps"></a>後續步驟

準備瞭解更多嗎？ 請參考下列連結︰

- [服務匯流排 AMQP 概觀]
- [在 Windows Server 的服務匯流排 AMQP]


[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[在 Windows Server 的服務匯流排 AMQP]: https://msdn.microsoft.com/library/dn574799.aspx
[服務匯流排 AMQP 概觀]: service-bus-amqp-overview.md
