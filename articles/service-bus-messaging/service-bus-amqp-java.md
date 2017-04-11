<properties 
    pageTitle="服務匯流排和與 AMQP 1.0 Java |Microsoft Azure"
    description="從 Java 服務匯流排使用 AMQP"
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

# <a name="use-service-bus-from-java-with-amqp-10"></a>從 Java 服務匯流排使用 AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Java 郵件服務 (JMS) 是標準的 API 使用 Java 平台上的郵件導向中介軟體。 Microsoft Azure 服務匯流排經過 AMQP 1.0 依據開發 Apache Qpid 專案 JMS 用戶端文件庫。 此文件庫支援完整 JMS 1.1 （英文) API，可以使用任何 AMQP 1.0 相容訊息服務使用。 這種情況下也支援[的 Windows Server 服務匯流排](https://msdn.microsoft.com/library/dn282144.aspx)（內部部署服務匯流排）。 如需詳細資訊，請參閱[中的 Windows Server 服務匯流排 AMQP][]。

## <a name="download-the-apache-qpid-amqp-10-jms-client-library"></a>下載 Apache Qpid AMQP 1.0 JMS 用戶端文件庫

下載最新版的 Apache Qpid JMS AMQP 1.0 用戶端文件庫的相關資訊，請造訪[http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html](http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html)項目。

您必須新增下列四個 JAR 檔案從 Apache Qpid JMS AMQP 1.0 分配封存 Java CLASSPATH 時建立並執行服務匯流排 JMS 應用程式︰

-   geronimo jms\_1.1 （英文)\_.jar 規格-[版本]

-   qpid-amqp-1-0-client-[version].jar

-   qpid-amqp-1-0-client-jms-[version].jar

-   qpid-amqp-1-0-common-[version].jar

## <a name="work-with-service-bus-queues-topics-and-subscriptions-from-jms"></a>使用服務匯流排佇列主題，以及從 JMS 的訂閱

### <a name="java-naming-and-directory-interface-jndi"></a>Java 命名和目錄介面 (JNDI)

JMS 使用 Java 命名與目錄介面 (JNDI) 來建立分隔邏輯和實際的名稱。 使用 JNDI 解決 JMS 物件的兩種︰ **ConnectionFactory**與**目的地**。 JNDI 使用，您可以在其中插入處理名稱解析之責任的不同目錄服務提供者模型。 簡單的內容提供 Apache Qpid JMS AMQP 1.0 文件庫檔案型 JNDI 提供者設定使用文字檔案。

使用下列格式的屬性檔案設定 Qpid 屬性檔案 JNDI 提供者︰

```
# servicebus.properties – sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCONNECTIONFACTORY = amqps://[username]:[password]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
topic.TOPIC = topic1
queue.QUEUE = queue1
```

#### <a name="configure-the-connection-factory"></a>設定連線工廠

用來定義**ConnectionFactory** Qpid 屬性檔案 JNDI 提供者中的項目為下列格式︰

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

位置`[jndi\_name]`和`[ConnectionURL]`意義如下︰

| 名稱            | 意義                                                                                                                                    |   |   |   |   |
|-----------------|--------------------------------------------------------------------------------------------------------------------------------------------|---|---|---|---|
| `[jndi\_name]`    | 連線工廠邏輯的名稱。 此名稱是否已解決 Java 應用程式中使用 JNDI`IntialContext.lookup()`方法。 |   |   |   |   |
| `[ConnectionURL]` | 提供所需的資訊以 AMQP 代理人 JMS 文件庫的 URL。                                                      |   |   |   |   |

連線的格式的 URL 是，如下所示︰

```
amqps://[username]:[password]@[namespace].servicebus.windows.net
```

位置`[namespace]`， `[username]`，及`[password]`意義如下︰

| 名稱          | 意義                                                                        |   |   |   |   |
|---------------|--------------------------------------------------------------------------------|---|---|---|---|
| `[namespace]` | 從[Azure 入口網站][]取得服務匯流排命名空間。                      |   |   |   |   |
| `[username]`  | 從[Azure 入口網站][]取得服務匯流排 SA 索引鍵的名稱。                    |   |   |   |   |
| `[password]`  | 從[Azure 入口網站][]取得服務匯流排 SA 索引鍵的 URL 編碼表單。 |   |   |   |   |

> [AZURE.NOTE] 您必須進行 URL 編碼密碼以手動方式。 實用的 URL 編碼公用程式的[http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp)。

例如，如果從取得資訊入口網站是，如下所示︰

| 命名空間︰   | test.servicebus.windows.net                  |
|--------------|----------------------------------------------|
| 發行者名稱︰ | RootManageSharedAccessKey                                        |
| 發行者機碼︰  | abcdefg |

然後，以定義具名的**ConnectionFactory**物件`SBCONNECTIONFACTORY`，設定的字串是，如下所示︰

```
connectionfactory.SBCONNECTIONFACTORY = amqps://RootManageSharedAccessKey:abcdefg@test.servicebus.windows.net
```

#### <a name="configure-destinations"></a>設定目的地

定義目的地 Qpid 屬性檔案 JNDI 提供者中的項目為下列格式︰

```
queue.[jndi_name] = [physical_name]
topic.[jndi_name] = [physical_name]
```

位置`[jndi\_name]`和`[physical\_name]`意義如下︰

| 名稱              | 意義                                                                                                                                  |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| `[jndi\_name]`    | 邏輯目的地的名稱。 這是名稱解析 Java 應用程式中使用 JNDI`IntialContext.lookup()`方法。 |
| `[physical\name]` | 服務匯流排實體的應用程式傳送或接收訊息的名稱。                                                  |

請注意下列事項︰

- `[physical\name]`值可服務匯流排佇列或主題。
- 接收服務匯流排主題訂閱，JNDI 中指定的實體名稱應該主題的名稱。 長期訂閱會建立在 JMS 應用程式碼時，會提供訂閱名稱。
- 您也可將服務匯流排主題訂閱視為 JMS 佇列。 有許多優點，此方法均︰ 相同的收件者程式碼可用於佇列及主題訂閱及所有的地址資訊 （主題和訂閱名稱） 是允屬性檔案中。
- 若要服務匯流排主題訂閱視為 JMS 佇列中，內容檔案中的項目應與表單的︰ `queue.[jndi\_name] = [topic\_name]/Subscriptions/[subscription\_name]`。 |

若要定義名為 「 主題 」，對應到名稱為 「 topic1 」 服務匯流排主題的邏輯 JMS 目的地，內容檔案中的項目是，如下所示︰

```
topic.TOPIC = topic1
```

### <a name="send-messages-using-jms"></a>使用 JMS 傳送郵件

下列程式碼會示範如何傳送訊息給服務匯流排主題。 它會引用本身的`SBCONNECTIONFACTORY`和`TOPIC`前一節所述， **servicebus.properties**設定檔案中所定義。

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
 
InitialContext context = new InitialContext(env); 
 
ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
Topic topic = (Topic) context.lookup("TOPIC");
Connection connection = cf.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
MessageProducer producer = session.createProducer(topic);
TextMessage message = session.createTextMessage("This is a text string"); 
producer.send(message);
```

### <a name="receive-messages-using-jms"></a>接收郵件使用 JMS

下列程式碼會顯示`how`若要從服務匯流排主題訂閱收到一則訊息。 它會引用本身的`SBCONNECTIONFACTORY`及主題定義**servicebus.properties**設定檔中上一節所述。 也引用本身的訂閱名稱是`subscription1`。

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
 
InitialContext context = new InitialContext(env);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
Topic topic = (Topic) context.lookup("TOPIC");
Connection connection = cf.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
TopicSubscriber subscriber = session.createDurableSubscriber(topic, "subscription1");
connection.start();
Message message = messageConsumer.receive();
```

### <a name="guidelines-for-building-robust-applications"></a>建置強大的應用程式的指導方針

JMS 規格中定義的 API 方法和應用程式碼的例外狀況合約應如何寫入處理這類例外狀況。 以下是一些關於例外處理考慮其他事項︰

-   註冊使用**connection.setExceptionListener**JMS 連線**ExceptionListener** 。 這會啟用非同步通知的問題的用戶端。 會有其他方法可以進一步瞭解其連線失敗時，特別重要的連線，只會耗用的郵件，此通知。 如果有問題基礎的 AMQP 連線、 工作階段或連結，稱為**ExceptionListener** 。 在此情況下，應用程式應該**JMS 連線**、**工作階段**、 **MessageProducer**和**MessageConsumer**物件中從頭開始重新建立。

-   若要驗證，郵件已成功傳送從**MessageProducer**服務匯流排實體，請確定 [應用程式已有設定**qpid.sync\_發佈**系統屬性設定。 您可以透過啟動程式**-Dqpid.sync\_發佈 = true** Java VM 啟動應用程式時，設定命令列上的選項。 設定此選項會設定不會傳回從傳送通話鍵，直到已收到的郵件已經接受服務匯流排確認文件庫。 如果傳送作業期間發生的問題，會引發**JMSException** 。 有兩個可能的原因︰ 
    1. 如果問題是因為服務匯流排拒絕所傳送的特定郵件，將會引發**MessageRejectedException**例外狀況。 此錯誤是暫時性，或因為郵件的一些問題。 建議是動作的以數種嘗試的一些返回關閉邏輯再試。 如果問題持續發生，郵件應該放棄本機登發生錯誤。 有不需要重新建立**JMS 連線**、**工作階段**或**MessageProducer**物件在此情況下。 
    2. 如果問題是因為服務匯流排關閉 AMQP 連結，將會引發例外狀況**InvalidDestinationException** 。 這可能是因為的暫時性問題，或刪除的郵件實體。 在任一情況， **JMS 連線**與**工作階段**， **MessageProducer**物件應該會重新建立。 如果暫時性錯誤狀況，然後這項作業最後會成功。 如果已刪除的實體，則會永久失敗。

## <a name="messaging-between-net-and-jms"></a>.NET 和 JMS 之間訊息

### <a name="message-bodies"></a>郵件本文

JMS 定義五種不同的郵件類型︰ **BytesMessage**、 **MapMessage**、 **ObjectMessage**、 **StreamMessage**及**TextMessage**。 服務匯流排.NET API 具有單一郵件類型， [BrokeredMessage][]。

#### <a name="jms-to-service-bus-net-api"></a>JMS 服務匯流排.NET API

下列各節說明如何使用每個從.NET JMS 訊息類型的訊息。 **ObjectMessage**範例不已加入，如**ObjectMessage**的本文包含在 Java 程式設計語言，無法解譯.NET 應用程式可序列化物件。

##### <a name="bytesmessage"></a>BytesMessage

下列程式碼會示範如何使用服務匯流排.NET Api 使用**BytesMessage**物件的本文。

```
Stream stream = message.GetBody<Stream>();
int streamLength = (int)stream.Length;

byte[] byteArray = new byte[streamLength];
stream.Read(byteArray, 0, streamLength);

Console.WriteLine("Length = " + streamLength);
for (int i = 0; i < stream.Length; i++)
{
  Console.Write("[" + (sbyte) byteArray[i] + "]");
}
```

##### <a name="mapmessage"></a>MapMessage

下列程式碼會示範如何使用服務匯流排.NET Api 使用**MapMessage**物件的本文。 將此程式碼逐一地圖，顯示 [名稱] 和 [每一個元素的值的項目。

```
Dictionary<String, Object> dictionary = message.GetBody<Dictionary<String, Object>>();

foreach (String mapItemName in dictionary.Keys)
{
  Object mapItemValue = null;
  if (dictionary.TryGetValue(mapItemName, out mapItemValue))
  {
    Console.WriteLine(mapItemName + ":" + mapItemValue);
  }
}
```

##### <a name="streammessage"></a>StreamMessage

下列程式碼會示範如何使用服務匯流排.NET Api 使用**StreamMessage**物件的本文。 將此程式碼會列出每個資料流，與他們類型的項目。

```
List<Object> list = message.GetBody<List<Object>>();

foreach (Object item in list)
{
  Console.WriteLine(item + " (" + item.GetType() + ")");
}
```

##### <a name="textmessage"></a>TextMessage

下列程式碼會示範如何使用服務匯流排.NET Api 使用**TextMessage**物件的本文。 將此程式碼會顯示在郵件本文中所包含的文字字串。

```
Console.WriteLine("Text: " + message.GetBody<String>());
```

#### <a name="service-bus-net-apis-to-jms"></a>若要 JMS 服務匯流排.NET Api

下列各節會顯示如何.NET 應用程式建立的每個不同的 JMS 訊息類型 JMS 中收到郵件。 **ObjectMessage**範例不已加入，如**ObjectMessage**的本文包含在 Java 程式設計語言，無法解譯.NET 應用程式可序列化物件。

##### <a name="bytesmessage"></a>BytesMessage

下列程式碼會顯示如何建立[BrokeredMessage][]物件為**BytesMessage**JMS 用戶端收到的.NET 中。

```
byte[] bytes = { 33, 12, 45, 33, 12, 45, 33, 12, 45, 33, 12, 45 };
message = new BrokeredMessage(bytes);
```

##### <a name="streammessage"></a>StreamMessage

下列程式碼會顯示如何建立[BrokeredMessage][]物件為**StreamMessage**JMS 用戶端收到的.NET 中。

```
List<Object> list = new List<Object>();
list.Add("String 1");
list.Add("String 2");
list.Add("String 3");
list.Add((double)3.14159);
message = new BrokeredMessage(list);
```

##### <a name="textmessage"></a>TextMessage

下列程式碼會示範如何使用**TextMessage**本文使用服務匯流排.NET API。 將此程式碼會顯示在郵件本文中所包含的文字字串。

```
message = new BrokeredMessage("this is a text string");
```

### <a name="application-properties"></a>應用程式屬性

####<a name="jms-to-service-bus-net-apis"></a>若要服務匯流排.NET Api JMS

JMS 郵件支援下列類型的應用程式屬性︰**布林值**、**位元組**、**簡短**、 **int**、**長**、**浮動時間**、**雙**，以及**字串**。 下列 Java 程式碼會顯示如何使用每個內容類型設定郵件的內容。

```
message.setBooleanProperty("TestBoolean", true); 
message.setByteProperty("TestByte", (byte) 33); 
message.setDoubleProperty("TestDouble", 3.14159D); 
message.setFloatProperty("TestFloat", 3.13159F); 
message.setIntProperty("TestInt", 100); 
message.setStringProperty("TestString", "Service Bus");
```

在服務匯流排.NET Api，郵件應用程式屬性都會套用[BrokeredMessage][] **Properties**集合中。 下列程式碼會顯示如何讀取的郵件，自 JMS 用戶端應用程式內容。

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

下表顯示 JMS 屬性類型如何對應到.NET 屬性類型。

| JMS 的內容類型 | .NET 的內容類型 |
|-------------------|--------------------|
| 位元組              | sbyte              |
| 整數           | int                |
| 浮動時間             | 浮動時間              |
| 點兩下            | 雙引號             |
| 布林值           | bool               |
| 字串            | 字串             |

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

下列 Java 程式碼會顯示如何讀取的郵件，自服務匯流排.NET 用戶端應用程式內容。

```
Enumeration propertyNames = message.getPropertyNames(); 
while (propertyNames.hasMoreElements()) 
{ 
  String name = (String) propertyNames.nextElement(); 
  Object value = message.getObjectProperty(name); 
  System.out.println(name + ": " + value + " (" + value.getClass() + ")"); 
}
```

下表顯示.NET 屬性類型如何對應到 JMS 屬性類型。

| .NET 的內容類型 | JMS 的內容類型 | 備忘稿                                                                                                                                                               |
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 位元組               | UnsignedByte      | -                                                                                                                                                                      |
| sbyte              | 位元組              | -                                                                                                                                                                     |
| 字元               | 字元         | -                                                                                                                                                                     |
| 簡短              | 簡短             | -                                                                                                                                                                     |
| ushort             | UnsignedShort     | -                                                                                                                                                                     |
| int                | 整數           | -                                                                                                                                                                     |
| uint               | UnsignedInteger   | -                                                                                                                                                                     |
| 長               | 長              | -                                                                                                                                                                     |
| ulong              | UnsignedLong      | -                                                                                                                                                                     |
| 浮動時間              | 浮動時間             | -                                                                                                                                                                     |
| 雙引號             | 點兩下            | -                                                                                                                                                                     |
| 小數位數            | BigDecimal        | -                                                                                                                                                                     |
| bool               | 布林值           | -                                                                                                                                                                     |
| Guid               | UUID              | -                                                                                                                                                                     |
| 字串             | 字串            | -                                                                                                                                                                     |
| 日期時間           | 日期              | -                                                                                                                                                                     |
| DateTimeOffset     | DescribedType     | DateTimeOffset.UtcTicks 對應到 AMQP 類型︰<type name=”datetime-offset” class=restricted source=”long”><descriptor name=”com.microsoft:datetime-offset” /></type> |
| 時段           | DescribedType     | Timespan.Ticks 對應到 AMQP 類型︰<type name=”timespan” class=restricted source=”long”><descriptor name=”com.microsoft:timespan” /></type>                        |
| Uri                | DescribedType     | Uri.AbsoluteUri 對應到 AMQP 類型︰<type name=”uri” class=restricted source=”string”><descriptor name=”com.microsoft:uri” /></type>                               |

### <a name="standard-headers"></a>標準的標題

下表顯示如何 JMS 標準標題和[BrokeredMessage][]標準屬性對應使用 AMQP 1.0。

#### <a name="jms-to-service-bus-net-apis"></a>若要服務匯流排.NET Api JMS

| JMS              | 服務匯流排.NET               | 備忘稿                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|------------------|--------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| JMSCorrelationID | Message.CorrelationID          | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| JMSDeliveryMode  | 目前無法使用        | 服務匯流排僅支援長期訊息。例如，DeliveryMode.PERSISTENT，不論所指定。                                                                                                                                                                                                                                                                                                                                                         |
| JMSDestination   | Message.To                     | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| JMSExpiration    | 訊息。 TimeToLive            | 轉換                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| JMSMessageID     | Message.MessageID              | 根據預設，JMSMessageID 編碼 AMQP 訊息中的二進位格式。 在收到二進位訊息識別碼，.NET 用戶端文件庫將轉換為根據的位元組的 unicode 值的字串表示。 若要切換使用字串訊息識別碼 JMS 文件庫，附加 「 二進位訊息 = false 」 的 JNDI ConnectionURL 查詢參數的字串。 例如︰“amqps://[username]:[password]@[namespace].servicebus.windows.net?二進位訊息 = false 」。 |
| JMSPriority      | 目前無法使用        | 服務匯流排不支援訊息優先順序。                                                                                                                                                                                                                                                                                                                                                                                                                             |
| JMSRedelivered   | 目前無法使用        | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| JMSReplyTo       | 訊息。 ReplyTo               | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| JMSTimestamp     | Message.EnqueuedTimeUtc        | 轉換                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| JMSType          | Message.Properties [」 jms-輸入 「] | -                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |

#### <a name="service-bus-net-apis-to-jms"></a>若要 JMS 服務匯流排.NET Api

| 服務匯流排.NET        | JMS              | 備忘稿                   |
|-------------------------|------------------|-------------------------|
| ContentType             | -                  | 目前無法使用 |
| 相互關聯識別碼           | JMSCorrelationID | -                         |
| EnqueuedTimeUtc         | JMSTimestamp     | 轉換              |
| 標籤                   | n/a              | 目前無法使用 |
| 訊息               | JMSMessageID     | -                         |
| ReplyTo                 | JMSReplyTo       | -                         |
| ReplyToSessionId        | n/a              | 目前無法使用 |
| ScheduledEnqueueTimeUtc | n/a              | 目前無法使用 |
| 工作階段識別碼               | n/a              | 目前無法使用 |
| TimeToLive              | JMSExpiration    | 轉換              |
| 若要                      | JMSDestination   | -                         |

## <a name="unsupported-features-and-restrictions"></a>不支援的功能和限制

下列限制存在於上與服務匯流排 AMQP 1.0 使用 JMS 時︰

-   只在一個**MessageProducer**或**MessageConsumer**允許每個工作階段。 如果您想要建立應用程式中的多個**MessageProducer**或**MessageConsumer**物件，建立每個專用的工作的階段。

-   目前不支援動態主題訂閱。

-   不支援**MessageSelector**物件。

-   暫存的目的地。例如， **TemporaryQueue**或**TemporaryTopic**，不支援，以及**QueueRequestor**和**TopicRequestor** Api 使用它們。

-   不支援交易的工作階段。

-   不支援分散式的交易。

## <a name="next-steps"></a>後續步驟

準備瞭解更多嗎？ 請參考下列連結︰

- [服務匯流排 AMQP 概觀]
- [在 Windows Server 的服務匯流排 AMQP]

[在 Windows Server 的服務匯流排 AMQP]: https://msdn.microsoft.com/library/dn574799.aspx
[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx

[服務匯流排 AMQP 概觀]: service-bus-amqp-overview.md
[Azure 入口網站]: https://portal.azure.com
