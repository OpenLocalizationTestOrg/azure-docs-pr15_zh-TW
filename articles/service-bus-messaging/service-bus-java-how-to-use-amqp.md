<properties 
    pageTitle="Java 服務匯流排 API 搭配使用 AMQP 1.0 |Microsoft Azure" 
    description="瞭解如何使用 Azure 服務匯流排和進階訊息佇列 Java 郵件服務 (JMS)"
    services="service-bus"
    documentationCenter="java"
    authors="sethmanheim"  
    manager="timlt" 
    editor="" />

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="java" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>如何使用服務匯流排和 AMQP 1.0 Java 郵件服務 (JMS) API

進階訊息佇列通訊協定 (AMQP) 1.0 是有效、 可靠線層級訊息通訊協定，您可以使用建置強大、 跨平台訊息應用程式。 新增至 Azure 服務匯流排 2012 年 10 月的 AMQP 1.0 支援，可能 2013 轉換至一般可用性 (GA)。

AMQP 1.0 的表示現在運用佇列及發佈/訂閱服務匯流排仲介訊息功能使用有效的二進位通訊協定的平台的範圍。 此外，您可以建立包含元件使用的作業系統、 架構與語言所建立的應用程式。

此 how-to 指南說明如何使用的服務匯流排代理訊息功能 （佇列及發佈/訂閱主題） 從使用常用 Java 郵件服務 (JMS) API 標準 Java 應用程式。

## <a name="get-started-with-service-bus"></a>服務匯流排快速入門

本指南假設您已經有包含名稱為**queue1**佇列中的服務匯流排命名空間。 如果您沒有後，您可以[建立命名空間和佇列中](service-bus-create-namespace-portal.md)使用[Azure 入口網站](https://portal.azure.com)。 如需有關如何建立服務匯流排命名空間和佇列的詳細資訊，請參閱[如何使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)。

### <a name="downloading-the-amqp-10-jms-client-library"></a>下載 AMQP 1.0 JMS 用戶端文件庫

如要下載最新版的 Apache Qpid JMS AMQP 1.0 用戶端文件庫的相關資訊，請造訪[https://qpid.apache.org/download.html](https://qpid.apache.org/download.html)。

您必須新增下列四個 JAR 檔案從 Apache Qpid JMS AMQP 1.0 分配封存 Java CLASSPATH 時建立並執行服務匯流排 JMS 應用程式︰

*    geronimo jms\_1.1 （英文)\_規格 1.0.jar
*    qpid-amqp-1-0-client-[version].jar
*    qpid-amqp-1-0-client-jms-[version].jar
*    qpid-amqp-1-0-common-[version].jar

## <a name="coding-java-applications"></a>編碼 Java 應用程式

### <a name="java-naming-and-directory-interface-jndi"></a>Java 命名和目錄介面 (JNDI)

JMS 使用 Java 命名與目錄介面 (JNDI) 來建立分隔邏輯和實際的名稱。 使用 JNDI 解決 JMS 物件的兩種︰ ConnectionFactory 與目的地。 JNDI 使用，您可以在其中插入處理名稱解析之責任的不同目錄服務提供者模型。 文件庫隨附簡單的屬性 Apache Qpid JMS AMQP 1.0 檔案型 JNDI 提供者設定使用屬性的下列檔案格式︰

```
# servicebus.properties – sample JNDI configuration
    
# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[username]:[password]@[namespace].servicebus.windows.net
    
# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="configure-the-connectionfactory"></a>設定 ConnectionFactory

用來定義**ConnectionFactory** Qpid 屬性檔案 JNDI 提供者中的項目為下列格式︰

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

在**[jndi_name]**和**[ConnectionURL]**的意義如下︰

- **[jndi_name]**: ConnectionFactory 的邏輯名稱。 這是會解析使用 JNDI IntialContext.lookup() 方法 Java 應用程式中的名稱。
- **[ConnectionURL]**: 提供所需的資訊以 AMQP 代理人 JMS 文件庫的 URL。

**ConnectionURL**的格式如下所示︰

```
amqps://[username]:[password]@[namespace].servicebus.windows.net
```

在****[命名空間]**、 [使用者名稱]**和**[密碼]**的意義如下︰

- **[命名空間]**: 服務匯流排命名空間。
- **[使用者名稱]**: 服務匯流排發行者的名稱。
- **[密碼]**: 服務匯流排發行者索引鍵的 URL 編碼的表單。

> [AZURE.NOTE] 您必須進行 URL 編碼密碼以手動方式。 URL 編碼的公用的[http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp)。

#### <a name="configure-destinations"></a>設定目的地

用來定義目的地 Qpid 屬性檔案 JNDI 提供者中的項目為下列格式︰

```
queue.[jndi_name] = [physical_name]
```
或

```
topic.[jndi_name] = [physical_name]
```

位置**[jndi\_名稱]**和**[實體\_名稱]**意義如下︰

- **[jndi_name]**: 邏輯目的地的名稱。 這是會解析使用 JNDI IntialContext.lookup() 方法 Java 應用程式中的名稱。
- **[physical_name]**: 服務匯流排實體的應用程式傳送或接收訊息的名稱。

> [AZURE.NOTE] 接收服務匯流排主題訂閱，JNDI 中指定的實體名稱應該主題的名稱。 長期訂閱會建立在 JMS 應用程式碼時，會提供訂閱名稱。 [服務匯流排 AMQP 1.0 開發人員指南](service-bus-amqp-dotnet.md)處理從 JMS 服務匯流排主題訂閱提供更多詳細資料。

### <a name="write-the-jms-application"></a>撰寫 JMS 應用程式

有任何特殊的 Api 或使用服務匯流排 JMS 時所需的選項。 然而，有一些限制探討的更新版本。 身為任何 JMS 應用程式，必須先為 JNDI 環境中，若要解決**ConnectionFactory**與目的地的設定。

#### <a name="configure-the-jndi-initialcontext"></a>設定 JNDI InitialContext

藉由將 javax.naming.InitialContext 類別建構函式傳遞的設定資訊 hashtable 設定 JNDI 環境。 兩個必要表中的項目初始內容 Factory 和提供者的 URL 的類別名稱。 下列程式碼會顯示如何設定使用 Qpid 屬性檔案 JNDI 環境屬性檔案命名**servicebus.properties**基礎 JNDI 提供者。

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>使用服務匯流排佇列中的簡單 JMS 應用程式

下列範例程式 JMS TextMessages 服務匯流排佇列 JNDI 邏輯名稱的佇列中，與接收郵件回。

```
// SimpleSenderReceiver.java
    
import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Hashtable;
import java.util.Random;
    
public class SimpleSenderReceiver implements MessageListener {
    private static boolean runReceiver = true;
    private Connection connection;
    private Session sendSession;
    private Session receiveSession;
    private MessageProducer sender;
    private MessageConsumer receiver;
    private static Random randomGenerator = new Random();
    
    public SimpleSenderReceiver() throws Exception {
        // Configure JNDI environment
        Hashtable<String, String> env = new Hashtable<String, String>();
        env.put(Context.INITIAL_CONTEXT_FACTORY, 
                   "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
        env.put(Context.PROVIDER_URL, "servicebus.properties");
        Context context = new InitialContext(env);
    
        // Lookup ConnectionFactory and Queue
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        Destination queue = (Destination) context.lookup("QUEUE");
    
        // Create Connection
        connection = cf.createConnection();
    
        // Create sender-side Session and MessageProducer
        sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        sender = sendSession.createProducer(queue);
    
        if (runReceiver) {
            // Create receiver-side Session, MessageConsumer,and MessageListener
            receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            receiver = receiveSession.createConsumer(queue);
            receiver.setMessageListener(this);
            connection.start();
        }
    }
    
    public static void main(String[] args) {
        try {
    
            if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
                runReceiver = false;
            }
    
            SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
            System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
            BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));
    
            while (true) {
                String s = commandLine.readLine();
                if (s.equalsIgnoreCase("exit")) {
                    simpleSenderReceiver.close();
                    System.exit(0);
                } else {
                    simpleSenderReceiver.sendMessage();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    
    private void sendMessage() throws JMSException {
        TextMessage message = sendSession.createTextMessage();
        message.setText("Test AMQP message from JMS");
        long randomMessageID = randomGenerator.nextLong() >>>1;
        message.setJMSMessageID("ID:" + randomMessageID);
        sender.send(message);
        System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
    }
    
    public void close() throws JMSException {
        connection.close();
    }
    
    public void onMessage(Message message) {
        try {
            System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
            message.acknowledge();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}   
```

### <a name="run-the-application"></a>執行應用程式

執行應用程式會產生下列輸出︰

```
> java SimpleSenderReceiver
Press [enter] to send a message. Type 'exit' + [enter] to quit.
    
Sent message with JMSMessageID = ID:2867600614942270318
Received message with JMSMessageID = ID:2867600614942270318
    
Sent message with JMSMessageID = ID:7578408152750301483
Received message with JMSMessageID = ID:7578408152750301483
    
Sent message with JMSMessageID = ID:956102171969368961
Received message with JMSMessageID = ID:956102171969368961
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>跨平台之間 JMS 和.NET 訊息

本指南示範如何傳送和接收訊息服務匯流排使用 JMS。 不過的 AMQP 1.0 的主要優點之一是它可以讓應用程式，以從不同的語言，撰寫的郵件交換可靠和完整的元件。

您可以使用上述範例 JMS 應用程式及隨附指南，[如何使用 AMQP 1.0 和.NET 服務匯流排.NET API](service-bus-dotnet-advanced-message-queuing.md)，將類似的.NET 應用程式的 exchange.NET 與 Java 之間的訊息。 

如需關於跨平台訊息使用服務匯流排和 AMQP 1.0 的詳細資料的詳細資訊，請參閱[服務匯流排 AMQP 1.0 開發人員指南](service-bus-amqp-dotnet.md)。

### <a name="jms-to-net"></a>.Net JMS

若要示範 JMS.NET 訊息︰

* 啟動.NET 範例應用程式不含任何命令列引數。
* Java 範例應用程式開始 「 sendonly 」 命令列引數。 在此模式中，應用程式不會收到的郵件從佇列中，它只會傳送。
* 按下**Enter**幾次 Java 應用程式主控台，會導致要傳送的訊息中。
* .NET 應用程式會收到下列訊息。

#### <a name="output-from-jms-application"></a>輸出 JMS 應用程式

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### <a name="output-from-net-application"></a>輸出.NET 應用程式

```
> SimpleSenderReceiver.exe  
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### <a name="net-to-jms"></a>.NET JMS

若要示範.NET JMS 訊息︰

* 開始 「 sendonly 」 命令列引數的.NET 範例應用程式。 在此模式中，應用程式不會收到的郵件從佇列中，它只會傳送。
* 啟動 Java 範例應用程式，不含任何命令列引數。
* 按下**Enter**幾次.NET 應用程式主控台，會導致要傳送的訊息中。
* Java 應用程式會收到下列訊息。

#### <a name="output-from-net-application"></a>輸出.NET 應用程式

```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3  
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### <a name="output-from-jms-application"></a>輸出 JMS 應用程式

```
> java SimpleSenderReceiver 
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## <a name="unsupported-features-and-restrictions"></a>不支援的功能和限制

下列限制存在，也就在與服務匯流排 AMQP 1.0 使用 JMS 時︰

* 只在一個**MessageProducer**或**MessageConsumer**允許每個**工作階段**。 如果您需要建立多個**MessageProducers**或**MessageConsumers**應用程式中，建立專用的**工作階段**的每個。
* 目前不支援動態主題訂閱。
* 目前不支援**MessageSelectors** 。
* 暫時目的地，亦即**TemporaryQueue**、 **TemporaryTopic**目前不支援，以及**QueueRequestor**和**TopicRequestor** Api 使用它們。
* 交易工作階段與分散式的交易不受支援。

## <a name="summary"></a>摘要

本文示範如何使用服務匯流排訊息功能 （佇列和發佈/訂閱主題） 從 Java 使用常用 JMS API 和 AMQP 1.0。

您也可以從其他語言，包括.NET、 C、 Python，以及 PHP 使用服務匯流排 AMQP 1.0。 使用這些不同語言所建立的元件可以可靠 exchange 郵件，並在使用 AMQP 1.0 的完整逼真度支援服務匯流排。 如需詳細資訊，請參閱[服務匯流排 AMQP 1.0 開發人員指南](service-bus-amqp-dotnet.md)。

## <a name="next-steps"></a>後續步驟

* [Azure 服務匯流排 AMQP 1.0 支援](service-bus-amqp-overview.md)
* [如何使用服務匯流排.NET API AMQP 1.0](service-bus-dotnet-advanced-message-queuing.md)
* [服務匯流排 AMQP 1.0 開發人員指南](service-bus-amqp-dotnet.md)
* [如何使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
 
