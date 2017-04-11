<properties 
    pageTitle="服務匯流排代理訊息.NET 教學課程 |Microsoft Azure"
    description="仲介訊息的.NET 教學課程。"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-brokered-messaging-net-tutorial"></a>服務匯流排代理訊息.NET 教學課程

Azure 服務匯流排提供兩個全面涵蓋所有內容的訊息解決方案 – 透過集中 「 轉接 」 服務執行支援各種不同的傳輸通訊協定和網頁的雲端中的一個服務標準，包括 SOAP，WS-*，與其餘部分。 用戶端便不需要直接連接到內部部署服務，也必須知道位置服務駐留，與內部部署服務並不需要任何輸入的連接埠的防火牆上開啟。

第二個訊息方案可讓 「 仲介 」 訊息的功能。 您可以想像為非同步，或分離訊息功能，可支援發行-訂閱、 暫時聯繫，並負載平衡服務匯流排訊息基礎結構的使用案例這些。 低耦合的通訊有許多優點。例如 [用戶端與伺服器可以視需要連線和非同步的方式執行作業。

本教學課程概觀，以及與佇列實務經驗，讓您法定保障，其中一項服務匯流排的核心元件代理訊息。 您逐步完成的主題順序在本教學課程後，您必須填入訊息的清單、 建立佇列中，並傳送郵件給該佇列中的應用程式。 最後，應用程式會收到並顯示佇列中的郵件，然後清除其資源和結束。 說明如何建立使用服務匯流排轉送的應用程式的相對應的教學課程，請參閱[服務匯流排轉送訊息的教學課程](../service-bus-relay/service-bus-relay-tutorial.md)。

## <a name="introduction-and-prerequisites"></a>簡介和先決條件

佇列提供第一個在第一個查看 (FIFO) 訊息傳送給一或多個競爭消費者。 FIFO 表示郵件通常是預期的接收和處理的佇列，而且會收到並處理只有一封郵件消費者的每一封郵件的暫時順序接收器。 使用佇列的主要優點是為了要*暫時耦合*的應用程式元件︰ 換句話說，產生者和消費者不需要以傳送及接收訊息一次，因為郵件長期儲存在佇列中。 相關的優點是*載入資源撫平*，可讓生產者和消費者傳送及接收郵件，以不同的比率。

以下是一些應該先按照開始教學課程之前的系統管理和必要步驟。 首先，若要建立服務命名空間，以取得共用的 access 簽章 (SA) 鍵。 命名空間提供服務匯流排透過公開，每個應用程式的應用程式邊界。 當您建立的服務命名空間時 SA 鍵會自動產生系統。 服務命名空間和 SA 按鍵組合提供的服務匯流排送給 access 應用程式的認證。

### <a name="create-a-service-namespace-and-obtain-a-sas-key"></a>建立服務命名空間，並取得 SA 索引鍵

第一個步驟是，建立服務命名空間，並取得[共用 Access 簽章](service-bus-sas-overview.md)(SA) 鍵。 命名空間提供服務匯流排透過公開，每個應用程式的應用程式邊界。 當您建立的服務命名空間時 SA 鍵會自動產生系統。 服務命名空間和 SA 按鍵組合提供的服務進行自我驗證 access 應用程式的認證。

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

下一步是建立 Visual Studio 專案，撰寫逗點分隔的訊息清單載入強型別[BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) .NET[清單](https://msdn.microsoft.com/library/6sh2ey19.aspx)物件的兩個協助函數。

### <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

1. 以滑鼠右鍵按一下 [開始] 功能表上的程式，並按一下 [**以系統管理員身分執行**以系統管理員身分開啟 Visual Studio。

1. 建立新的主控台應用程式專案。 按一下 [**檔案**] 功能表選取 [**新增**]，然後按一下 [**專案**]。 在 [**新專案**] 對話方塊中，按一下**Visual C#** （如果**Visual C#**沒有出現，查看**其他語言**] 之下），按一下 [**主控台應用程式**範本，並將其命名為 [ **QueueSample**。 使用預設的**位置**。 按一下**[確定**] 以建立專案。

1. 若要新增的服務匯流排文件庫至您的專案中使用 NuGet 封裝管理員︰
    1. 在方案總管中，以滑鼠右鍵按一下**QueueSample**專案，然後按一下**管理 NuGet 套件**。
    2. 在 [**管理 Nuget 套件**] 對話方塊中，按一下 [**瀏覽**] 索引標籤和搜尋**Azure 服務匯流排**，然後按一下 [**安裝**]。
<br />
1. 在方案總管中按兩下 Program.cs 檔案，以在 Visual Studio 編輯器中開啟。 從其預設名稱變更命名空間名稱`QueueSample`至`Microsoft.ServiceBus.Samples`。

    ```
    Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. 修改`using`陳述式下列所示。

    ```
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;
    ```

1. 建立命名的文字檔案 Data.csv，並複製下列逗點分隔文字中的。

    ```
    IssueID,IssueTitle,CustomerID,CategoryID,SupportPackage,Priority,Severity,Resolved
    1,Package lost,1,1,Basic,5,1,FALSE
    2,Package damaged,1,1,Basic,5,1,FALSE
    3,Product defective,1,2,Premium,5,2,FALSE
    4,Product damaged,2,2,Premium,5,2,FALSE
    5,Package lost,2,2,Basic,5,2,TRUE
    6,Package lost,3,2,Basic,5,2,FALSE
    7,Package damaged,3,7,Premium,5,3,FALSE
    8,Product defective,3,2,Premium,5,3,FALSE
    9,Product damaged,4,6,Premium,5,3,TRUE
    10,Package lost,4,8,Basic,5,3,FALSE
    11,Package damaged,5,4,Basic,5,4,FALSE
    12,Product defective,5,4,Basic,5,4,FALSE
    13,Package lost,6,8,Basic,5,4,FALSE
    14,Package damaged,6,7,Premium,5,5,FALSE
    15,Product defective,6,2,Premium,5,5,FALSE
    ```

    儲存並關閉 Data.csv 檔案，然後請記住，將其儲存的位置。

1. 在方案總管中，以滑鼠右鍵按一下您的專案名稱 （在此範例中， **QueueSample**），按一下 [**新增**]，然後按一下 [**現有的項目**。

1. 瀏覽至您在步驟 6 中建立 Data.csv 檔案。 按一下 [檔案]，然後按一下 [**新增**]。 確定**所有檔案 (*。*)* *] 清單中選取的檔案類型。

### <a name="create-a-method-that-parses-a-list-of-messages"></a>建立剖析的訊息清單的方法

1. 在`Program`課程之前`Main()`方法，將兩個變數︰ 其中一個**資料表**，以包含的郵件清單中 Data.csv 類型。 其他必須是強型別[BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)類型清單物件。 後者是仲介教學課程中的後續步驟會使用的郵件清單。

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        class Program
        {
    
            private static DataTable issues;
            private static List<BrokeredMessage> MessageList;
    ```

1. 外部`Main()`，定義`ParseCSV()`剖析 Data.csv 中的郵件清單，並將郵件載入至[資料表](https://msdn.microsoft.com/library/azure/system.data.datatable.aspx)資料表，如下所示的方法。 方法會傳回一個**資料表**的物件。

    ```
    static DataTable ParseCSVFile()
    {
        DataTable tableIssues = new DataTable("Issues");
        string path = @"..\..\data.csv";
        try
        {
            using (StreamReader readFile = new StreamReader(path))
            {
                string line;
                string[] row;
    
                // create the columns
                line = readFile.ReadLine();
                foreach (string columnTitle in line.Split(','))
                {
                    tableIssues.Columns.Add(columnTitle);
                }
    
                while ((line = readFile.ReadLine()) != null)
                {
                    row = line.Split(',');
                    tableIssues.Rows.Add(row);
                }
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Error:" + e.ToString());
        }
    
        return tableIssues;
    }
    ```

1. 在 [`Main()`方法，加入通話的陳述式`ParseCSVFile()`方法︰

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
    
    }
    ```

### <a name="create-a-method-that-loads-the-list-of-messages"></a>建立載入的郵件清單的方法

1. 外部`Main()`，定義`GenerateMessages()`所傳回的**資料表**物件的方法`ParseCSVFile()`並載入強型別仲介郵件清單中的資料表。 接著方法會傳回**清單**物件，如下列範例所示。 

    ```
    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
        // Instantiate the brokered list object
        List<BrokeredMessage> result = new List<BrokeredMessage>();
    
        // Iterate through the table and create a brokered message for each row
        foreach (DataRow item in issues.Rows)
        {
            BrokeredMessage message = new BrokeredMessage();
            foreach (DataColumn property in issues.Columns)
            {
                message.Properties.Add(property.ColumnName, item[property]);
            }
            result.Add(message);
        }
        return result;
    }
    ```

1. 在 [ `Main()`，通話後面直接`ParseCSVFile()`，加入通話的陳述式`GenerateMessages()`具有的傳回值的方法`ParseCSVFile()`做為引數︰

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
    }
    ```

### <a name="obtain-user-credentials"></a>取得使用者認證

1. 首先，建立三個全域的字串變數，如要保留這些值。 將這些變數宣告直接後的前一個變數宣告。例如︰

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        public class Program
        {
    
            private static DataTable issues;
            private static List<BrokeredMessage> MessageList; 

            // Add these variables
            private static string ServiceNamespace;
            private static string sasKeyName = "RootManageSharedAccessKey";
            private static string sasKeyValue;
            …
    ```

1. 接下來，建立接受並儲存的服務命名空間和 SA 索引鍵的函數。 新增外部這個方法`Main()`。 例如︰ 

    ```
    static void CollectUserInput()
    {
        // User service namespace
        Console.Write("Please enter the namespace to use: ");
        ServiceNamespace = Console.ReadLine();
    
        // Issuer key
        Console.Write("Enter the SAS key to use: ");
        sasKeyValue = Console.ReadLine();
    }
    ```

1. 在 [ `Main()`，通話後面直接`GenerateMessages()`，加入通話的陳述式`CollectUserInput()`方法︰

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
        
        // Collect user input
        CollectUserInput();
    }
    ```

### <a name="build-the-solution"></a>建置解決方案

從 Visual Studio 中 [**建置**] 功能表中，您可以按一下 [**建立的解決方案**，或按**Ctrl + Shift + B** ，確認您的工作為止。

## <a name="create-management-credentials"></a>建立管理認證

在此步驟中，您可以定義您會使用建立共用的 access 應用程式會使用獲授權的簽章 (SA) 認證的管理作業。

1. 為避免混淆，本教學課程中放置不同的方法佇列中的所有作業。 建立非同步`Queue()`中的方法`Program`之後課程`Main()`方法。 例如︰
 
    ```
    public static void Main(string[] args)
    {
    …
    }
    static async Task Queue()
    {
    }
    ```

1. 下一步是建立使用[TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx)物件 SA 認證。 建立方法使用 SA 機碼名稱，取得在值`CollectUserInput()`方法。 新增下列程式碼，`Queue()`方法︰

    ```
    static async Task Queue()
    {
        // Create management credentials
        TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
    }
    ```

2. 建立新的命名空間管理物件，具有 URI 包含命名空間名稱] 和 [管理認證，取得在上一個步驟中，做為引數。 將此程式碼直接後面新增一個步驟中加入的程式碼。 請務必將`<yourNamespace>`服務命名空間的名稱︰
    
    ```
    NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

### <a name="example"></a>範例

此時，看起來類似下列程式碼︰

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  public class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Task.WaitAll(Queue());
    }

    static async Task Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

## <a name="send-messages-to-the-queue"></a>傳送郵件給佇列中

在此步驟中，您可以建立佇列中，然後傳送給佇列仲介郵件清單中所包含的郵件。

### <a name="create-queue-and-send-messages-to-the-queue"></a>建立佇列中，並傳送郵件給佇列中

1. 首先，建立佇列。 例如，呼叫該`myQueue`，並將它直接在您新增的管理作業之後`Queue()`中最後一個步驟的方法︰

    ```
    QueueDescription myQueue;

    if (namespaceClient.QueueExists("IssueTrackingQueue"))
    {
        namespaceClient.DeleteQueue("IssueTrackingQueue");
    }

    myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
    ```

1. 在 [`Queue()`方法，建立一個新建立服務匯流排 URI 做為引數具有訊息工廠物件。 直接在您新增的最後一個步驟中的管理作業之後新增下列程式碼。 請務必將`<yourNamespace>`服務命名空間的名稱︰

    ```
    MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

1. 接下來，建立使用[QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx)類別的佇列物件。 新增下列程式碼直接代碼之後，您新增的最後一個步驟︰

    ```
    QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");
    ```

1. 接下來，新增循環仲介郵件先前所建立，傳送至佇列中的每個清單的程式碼。 新增下列程式碼直接之後`CreateQueueClient()`陳述式中上一個步驟︰
    
    ```
    // Send messages
    Console.WriteLine("Now sending messages to the queue.");
    for (int count = 0; count < 6; count++)
    {
        var issue = MessageList[count];
        issue.Label = issue.Properties["IssueTitle"].ToString();
        await myQueueClient.SendAsync(issue);
        Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
    }
    ```

## <a name="receive-messages-from-the-queue"></a>會從佇列中接收郵件

在此步驟中，您可從您在上一個步驟中建立佇列中取得郵件的清單。

### <a name="create-a-receiver-and-receive-messages-from-the-queue"></a>建立收件者，並從佇列接收郵件

在 [`Queue()`方法佇列中重複使用及接收郵件[QueueClient.ReceiveAsync](https://msdn.microsoft.com/library/azure/dn130423.aspx)方法，列印到主控台每一封郵件。 新增下列程式碼直接代碼之後，您新增在先前的步驟︰

```
Console.WriteLine("Now receiving messages from Queue.");
BrokeredMessage message;
while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();
    
        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

請注意，`Thread.Sleep`只會用來模擬郵件處理和您可能就不需要在實際訊息應用程式中。

### <a name="end-the-queue-method-and-clean-up-resources"></a>結束佇列方法，並清除資源

直接在上一個程式碼之後，新增下列程式碼，清理訊息工廠和佇列資源︰

```
factory.Close();
myQueueClient.Close();
namespaceClient.DeleteQueue("IssueTrackingQueue");
```

### <a name="call-the-queue-method"></a>呼叫佇列中

最後一個步驟是將通話的陳述式`Queue()`的方法`Main()`。 新增下列程式碼的醒目提示的行結尾的 main （）︰
    
```
public static void Main(string[] args)
{
    // Collect user input
    CollectUserInput();
    
    // Populate test data
    issues = ParseCSVFile();
    MessageList = GenerateMessages(issues);
    
    // Add this call
    Queue();
}
```

### <a name="example"></a>範例

下列程式碼包含完整的**QueueSample**應用程式。

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
    public class Program
    {
        private static DataTable issues;
        private static List<BrokeredMessage> MessageList;

        // Add these variables
        private static string ServiceNamespace;
        private static string sasKeyName = "RootManageSharedAccessKey";
        private static string sasKeyValue;

        static void Main(string[] args)
        {

            // Populate test data
            issues = ParseCSVFile();
            MessageList = GenerateMessages(issues);

            // Collect user input
            CollectUserInput();

            Queue();

        }

        static async Task Queue()
        {
            // Create management credentials
            TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
            NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueDescription myQueue;

            if (namespaceClient.QueueExists("IssueTrackingQueue"))
            {
                namespaceClient.DeleteQueue("IssueTrackingQueue");
            }
            
            myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
            
            MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");

            // Send messages
            Console.WriteLine("Now sending messages to the queue.");
            for (int count = 0; count < 6; count++)
            {
                var issue = MessageList[count];
                issue.Label = issue.Properties["IssueTitle"].ToString();
                await myQueueClient.SendAsync(issue);
                Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
            }

            Console.WriteLine("Now receiving messages from Queue.");
            BrokeredMessage message;
            while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
            {
                Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
                message.Complete();

                Console.WriteLine("Processing message (sleeping...)");
                Thread.Sleep(1000);
            }

            factory.Close();
            myQueueClient.Close();
            namespaceClient.DeleteQueue("IssueTrackingQueue");


        }

        static void CollectUserInput()
        {
            // User service namespace
            Console.Write("Please enter the namespace to use: ");
            ServiceNamespace = Console.ReadLine();

            // Issuer key
            Console.Write("Enter the SAS key to use: ");
            sasKeyValue = Console.ReadLine();
        }

        static List<BrokeredMessage> GenerateMessages(DataTable issues)
        {
            // Instantiate the brokered list object
            List<BrokeredMessage> result = new List<BrokeredMessage>();

            // Iterate through the table and create a brokered message for each row
            foreach (DataRow item in issues.Rows)
            {
                BrokeredMessage message = new BrokeredMessage();
                foreach (DataColumn property in issues.Columns)
                {
                    message.Properties.Add(property.ColumnName, item[property]);
                }
                result.Add(message);
            }
            return result;
        }

        static DataTable ParseCSVFile()
        {
            DataTable tableIssues = new DataTable("Issues");
            string path = @"..\..\data.csv";
            try
            {
                using (StreamReader readFile = new StreamReader(path))
                {
                    string line;
                    string[] row;

                    // create the columns
                    line = readFile.ReadLine();
                    foreach (string columnTitle in line.Split(','))
                    {
                        tableIssues.Columns.Add(columnTitle);
                    }

                    while ((line = readFile.ReadLine()) != null)
                    {
                        row = line.Split(',');
                        tableIssues.Rows.Add(row);
                    }
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Error:" + e.ToString());
            }

            return tableIssues;
        }
    }
}
```

## <a name="build-and-run-the-queuesample-application"></a>建立並執行 QueueSample 應用程式

現在您已完成上述步驟，您可以建立並執行**QueueSample**應用程式。

### <a name="build-the-queuesample-application"></a>建立 QueueSample 應用程式

在 Visual Studio 中，從 [**建置**] 功能表中，按一下 [**建立的解決方案**，或按**Ctrl + Shift + B**。 如果發生錯誤，請確認您的程式碼是正確根據呈現結尾的上一個步驟完成的範例。

## <a name="next-steps"></a>後續步驟

本教學課程中會顯示如何建立服務匯流排用戶端應用程式和服務使用的服務匯流排代理訊息功能。 使用服務匯流排[轉送](service-bus-messaging-overview.md#Relayed-messaging)的類似教學課程，請參閱[服務匯流排轉送訊息的教學課程](../service-bus-relay/service-bus-relay-tutorial.md)。

若要瞭解詳細[服務匯流排](https://azure.microsoft.com/services/service-bus/)資訊，請參閱下列主題。

- [服務匯流排訊息概觀](service-bus-messaging-overview.md)
- [服務匯流排基本概念](service-bus-fundamentals-hybrid-solutions.md)
- [服務匯流排架構](service-bus-architecture.md)

