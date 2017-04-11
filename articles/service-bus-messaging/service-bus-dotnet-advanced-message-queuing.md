<properties 
    pageTitle="如何使用.NET 服務匯流排 API AMQP 1.0 |Microsoft Azure" 
    description="瞭解如何使用 Azure 服務匯流排 API 的進階郵件佇列 Protodol (AMQP) 1.0。" 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-amqp-10-with-the-service-bus-net-api"></a>如何使用服務匯流排.NET API AMQP 1.0

進階訊息佇列通訊協定 (AMQP) 1.0 是有效、 可靠線層級訊息通訊協定，您可以使用建置強大、 跨平台訊息應用程式。

支援的服務匯流排 AMQP 1.0 表示您可以使用佇列並發佈/訂閱仲介訊息功能使用有效的二進位通訊協定的平台的範圍。 此外，您可以建立包含元件使用的語言、 架構及作業系統所建立的應用程式。

本文說明如何使用的服務匯流排代理訊息功能 （佇列及發佈/訂閱主題） 使用服務匯流排.NET API 的.NET 應用程式。 有說明如何執行相同的使用標準 Java 郵件服務 (JMS) API[小幫手] 文件](service-bus-java-how-to-use-jms-api-amqp.md)。 您可以在一起使用以下兩本指南，若要深入瞭解使用 AMQP 1.0 的跨平台訊息。

## <a name="get-started-with-service-bus"></a>服務匯流排快速入門

本文假設您已經有服務匯流排命名空間含有佇列中名為 「 queue1 」。 如果您沒有，然後您可以建立命名空間和使用[Azure 入口網站][]的佇列。 如需有關如何建立服務匯流排命名空間和佇列的詳細資訊，請參閱[開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md#1-create-a-namespace-using-the-Azure-portal)。

## <a name="download-the-service-bus-sdk"></a>下載服務匯流排 SDK

使用服務匯流排 SDK 2.1 或更新版本中 AMQP 1.0 支援。 您可以從 NuGet 下載最新的位元，在[http://nuget.org/packages/WindowsAzure.ServiceBus/](http://nuget.org/packages/WindowsAzure.ServiceBus/)。

## <a name="code-net-applications"></a>程式碼.NET 應用程式

根據預設，服務匯流排.NET 用戶端文件庫與服務匯流排服務使用 SOAP 為基礎的專用通訊協定進行通訊。 若要使用 AMQP 1.0，而不是預設的通訊協定需要明確服務匯流排連接字串中的下一節所述的設定。 這項變更，非使用 AMQP 1.0 時將維持不變基本上應用程式碼。

在目前的版本，有幾個使用 AMQP 時不受支援的 API 功能。 [不支援的功能和限制](#unsupported-features-and-restrictions)] 區段中，這些不受支援的功能會列出更新版本。 某些進階的組態設定也有不同的意義，使用 AMQP 時。 無這些設定會使用本文中，但更多詳細資料可在[服務匯流排 AMQP 概觀](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences)。

### <a name="configure-via-appconfig"></a>透過 App.config 設定

是建議的應用程式會使用 App.config 設定檔，在儲存設定。 對於服務匯流排應用程式，您可以使用 App.config 儲存服務匯流排**連接字串**。 此範例應用程式也會使用 App.config 儲存訊息它所使用的實體服務匯流排的名稱。

如下所示的範例 App.config 檔案︰

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
            <add key="EntityName" value="queue1" />
    </appSettings>
</configuration>
```

### <a name="configure-the-service-bus-connection-string"></a>設定服務匯流排連線字串

**Microsoft.ServiceBus.ConnectionString**設定的值是用來設定連線到服務匯流排服務匯流排連線字串。 格式如下所示︰

```
Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp
```

位置`[namespace]`和`[SAS key]`從[Azure 入口網站][]取得。 如需詳細資訊，請參閱 [如何使用服務匯流排佇列] []。

使用時 AMQP，加連接字串`;TransportType=Amqp`，表示使用 AMQP 1.0，讓它的連線到服務匯流排的用戶端文件庫。

### <a name="configure-the-entity-name"></a>設定的實體名稱

此範例應用程式使用`EntityName`App.config 檔案**和 appSettings**一節中設定以設定與應用程式交換郵件佇列中的名稱。

### <a name="a-simple-net-application-using-a-service-bus-queue"></a>使用服務匯流排佇列中的簡單.NET 應用程式

下列範例會傳送和接收服務匯流排佇列中的郵件。

```
// SimpleSenderReceiver.cs
    
using System;
using System.Configuration;
using System.Threading;
using Microsoft.ServiceBus.Messaging;
    
namespace SimpleSenderReceiver
{
    class SimpleSenderReceiver
    {
        private static string connectionString;
        private static string entityName;
        private static Boolean runReceiver = true;
        private MessagingFactory factory;
        private MessageSender sender;
        private MessageReceiver receiver;
        private MessageListener messageListener;
        private Thread listenerThread;
    
        static void Main(string[] args)
        {
            try
            {
                if ((args.Length > 0) && args[0].ToLower().Equals("sendonly"))
                    runReceiver = false;
                    
                string ConnectionStringKey = "Microsoft.ServiceBus.ConnectionString";
                string entityNameKey = "EntityName";
                entityName = ConfigurationManager.AppSettings[entityNameKey];
                connectionString = ConfigurationManager.AppSettings[ConnectionStringKey];
                SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
    
                Console.WriteLine("Press [enter] to send a message. " +
                    "Type 'exit' + [enter] to quit.");
    
                while (true)
                {
                    string s = Console.ReadLine();
                    if (s.ToLower().Equals("exit"))
                    {
                        simpleSenderReceiver.Close();
                        System.Environment.Exit(0);
                    }
                    else
                        simpleSenderReceiver.SendMessage();
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Caught exception: " + e.Message);
            }
        }
    
        public SimpleSenderReceiver()
        {
            factory = MessagingFactory.CreateFromConnectionString(connectionString);
            sender = factory.CreateMessageSender(entityName);
    
            if (runReceiver)
            {
                receiver = factory.CreateMessageReceiver(entityName);
                messageListener = new MessageListener(receiver);
                listenerThread = new Thread(messageListener.Listen);
                listenerThread.Start();
            }
        }
    
        public void Close()
        {
            messageListener.RequestStop();
            listenerThread.Join();
            factory.Close();
        }
    
        private void SendMessage()
        {
            BrokeredMessage message = new BrokeredMessage("Test AMQP message from .NET");
            sender.Send(message);
            Console.WriteLine("Sent message with MessageID = " 
                + message.MessageId);
        }

    }
    
    public class MessageListener
    {
        private MessageReceiver messageReceiver;
        public MessageListener(MessageReceiver receiver)
        {
            messageReceiver = receiver;
        }
    
        public void Listen()
        {
            while (!_shouldStop)
            {
                try
                {
                    BrokeredMessage message = 
                        messageReceiver.Receive(new TimeSpan(0, 0, 10));
                    if (message != null)
                    {
                        Console.WriteLine("Received message with MessageID = " + 
                            message.MessageId);
                        message.Complete();
                    }
                }
                catch (Exception e)
                {
                    Console.WriteLine("Caught exception: " + e.Message);
                    break;
                }
            }
        }
    
        public void RequestStop()
        {
            _shouldStop = true;
        }
    
        private volatile bool _shouldStop;
    }
}
```

### <a name="run-the-application"></a>執行應用程式

執行應用程式會產生輸出的表單︰

```
> SimpleSenderReceiver.exe
Press [enter] to send a message. Type 'exit' + [enter] to quit.
    
Sent message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
Received message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
    
Sent message with MessageID = d00e2e088f06416da7956b58310f7a06
Received message with MessageID = d00e2e088f06416da7956b58310f7a06
    
Received message with MessageID = f27f79ec124548c196fd0db8544bca49
Sent message with MessageID = f27f79ec124548c196fd0db8544bca49
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>跨平台之間 JMS 和.NET 訊息

本主題說明如何傳送郵件給使用.NET 服務匯流排以及如何接收使用.NET 這些郵件。 不過的 AMQP 1.0 的主要優點之一是它可以讓應用程式，以從不同的語言，撰寫的郵件交換可靠和完整的元件。

使用上述範例.NET 應用程式和類似 Java 應用程式將小幫手指南，[如何使用 Java 郵件服務 (JMS) 與服務匯流排與 AMQP 1.0 API](service-bus-java-how-to-use-jms-api-amqp.md)，可能是 exchange.NET 與 Java 之間的訊息。 

如需關於跨平台訊息使用服務匯流排和 AMQP 1.0 的詳細資料的詳細資訊，請參閱[服務匯流排 AMQP 1.0 概觀](service-bus-amqp-overview.md)。

### <a name="jms-to-net"></a>.Net JMS

若要示範 JMS.NET 訊息︰

* 啟動.NET 範例應用程式不含任何命令列引數。
* Java 範例應用程式開始 「 sendonly 」 命令列引數。 在此模式中，應用程式不會收到的郵件從佇列中，它只會傳送。
* 按下**Enter**幾次 Java 應用程式主控台，會導致要傳送的訊息中。
* .NET 應用程式會收到下列訊息。

### <a name="output-from-jms-application"></a>輸出 JMS 應用程式

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

### <a name="output-from-net-application"></a>輸出.NET 應用程式

```
> SimpleSenderReceiver.exe  
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

## <a name="net-to-jms"></a>.NET JMS

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

下列功能的.NET 服務匯流排 API 目前不支援使用 AMQP 時︰

 * 交易
 * 透過傳輸目的地傳送

如需詳細資訊，請參閱[不受支援的功能的限制，與行為差異](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences)。

## <a name="summary"></a>摘要

本文說明如何從.NET 存取服務匯流排代理的訊息功能 （佇列和發佈/訂閱主題） 使用 AMQP 1.0 和服務匯流排.NET API。

您也可以包含 Java、 C、 Python 和 PHP 其他語言的使用服務匯流排 AMQP 1.0。 使用這些語言的內建的元件可靠工作與居家使用服務匯流排 AMQP 1.0 的完整逼真度交換郵件。 如需詳細資訊，請參閱[服務匯流排 AMQP 概觀](service-bus-amqp-dotnet.md)。

## <a name="next-steps"></a>後續步驟

既然您已讀取服務匯流排和.net AMQP 的概觀，請參閱下列連結，如需詳細資訊。

* [Azure 服務匯流排 AMQP 1.0 支援](service-bus-amqp-overview.md)
* [如何使用服務匯流排與 AMQP 1.0 Java 郵件服務 (JMS) API](service-bus-java-how-to-use-jms-api-amqp.md)
* [如何使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
 
[Azure 入口網站]: https://portal.azure.com
