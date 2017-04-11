<properties
    pageTitle=".NET 多層應用程式 |Microsoft Azure"
    description="可協助您的.NET 教學課程開發使用服務匯流排佇列通訊層之間的 Azure 中的多層應用程式。"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="sethm"/>

# <a name="net-multi-tier-application-using-azure-service-bus-queues"></a>使用 Azure 服務匯流排佇列.NET 多層應用程式

## <a name="introduction"></a>簡介

Microsoft Azure 開發可以輕鬆使用 Visual Studio 與免費 Azure SDK.net。 本教學課程中會引導您建立的應用程式使用本機環境中的多個 Azure 資源的步驟。 步驟假設您有使用 Azure 沒有先前經驗。

您將瞭解下列動作︰

-   若要啟用您的電腦，使用單一下載及安裝 Azure 開發的方式。
-   如何使用 Visual Studio 開發 Azure。
-   如何建立使用 web 及工作者角色 Azure 中的多層應用程式。
-   如何使用服務匯流排佇列層之間的通訊。

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

在本教學課程中，您會建立並 Azure 雲端服務中執行多層應用程式。 前端會 ASP.NET MVC 網頁角色與後端會使用服務匯流排佇列中的工作者角色。 您可以為部署至，而不是雲端服務的 Azure 網站 web 專案的前端建立相同的多層應用程式。 如需以不同方式處理前端 Azure 網站上的指示，請參閱[後續步驟](#nextsteps)。 您也可以嘗試[.NET 上部署/雲端混合式應用程式](../service-bus-relay/service-bus-dotnet-hybrid-app-using-service-bus-relay.md)教學課程。

以下螢幕擷取畫面顯示已完成的應用程式。

![][0]

## <a name="scenario-overview-inter-role-communication"></a>案例概觀︰ 間的角色通訊

送出訂單進行處理，前端的使用者介面元件、 執行 web 角色時，必須與執行工作者角色中的中間層邏輯進行互動。 此範例使用服務匯流排代理訊息層之間的通訊。

使用 web 和中間名的層級之間的仲介訊息，以減少兩個元件。 相較於直接訊息 （也就是 TCP 或 HTTP）、 web 層不連接至介直接;請改為其推入單位的工作，做為郵件，將服務匯流排，可靠保留它們，直到介準備取用並進行處理。

服務匯流排提供支援仲介訊息的兩個項目︰ 佇列 」 和 「 主題。 與佇列中，傳送給佇列中的每一封郵件會耗用單一收件者。 主題支援發佈/訂閱的模式中的每個已發佈的訊息供訂閱註冊的主題。 每個訂閱邏輯維護自己佇列中的郵件。 訂閱也可以設定限制的一組郵件傳遞給訂閱佇列中，以符合篩選的篩選規則。 下列範例會使用服務匯流排佇列。

![][1]

這個通訊機制具有透過直接訊息的幾項優點︰

-   **暫時聯繫。** 使用 [非同步訊息模式，產生者和消費者不需要線上一次。 服務匯流排可靠儲存郵件，直到耗用的廠商會收到通知。 這會啟用中斷連線，請主動，例如維護]，或因為元件當機，而不會影響整個系統的分散式應用程式的元件。 此外，使用應用程式可能只需要上線特定的一天的時間。

-   **載入資源撫平。** 在許多應用程式，系統載入不盡相同一段時間，通常是固定工時的每一個單位所需的時間時。 Intermediating 訊息產生者和佇列中使用的消費者表示 （工作） 使用應用程式只需要以容納平均負載，而不是最大使用量載入佈建。 佇列中的深度規模擴大時，並縮小為內送的載入不盡相同。 直接儲存金錢而言基礎結構服務應用程式載入所需的量。

-   **負載平衡。** 隨著負載增加，可以讀取佇列中新增更多背景工作處理程序。 每一封郵件處理僅有一個背景工作處理程序。 此外，此擷取負載平衡可讓充分運用工作者機器即使工作者機器各處理 power 時它們會提取郵件在自己的最大速度。 這個模式通常稱為*競爭消費者*圖樣。

    ![][2]

下列各節討論實作此架構的程式碼。

## <a name="set-up-the-development-environment"></a>設定的開發環境

開發 Azure 應用程式之前，取得工具，並設定您的開發環境。

1.  安裝在[取得工具及 SDK][].NET Azure SDK。

2.  按一下 [**安裝 SDK** Visual Studio 您正在使用的版本。 本教學課程中的步驟，使用 Visual Studio 2015。

4.  當畫面提示您執行或儲存安裝程式，請按一下 [**執行**]。

5.  在**網頁的平台安裝程式**中，按一下 [**安裝**]，然後繼續進行安裝。

6.  安裝完成後，您必須開始開發應用程式所需的所有項目。 SDK 包含可讓您輕鬆地開發 Visual Studio 中的 Azure 應用程式的工具。 如果您沒有安裝的 Visual Studio，SDK 也會安裝免費 Visual Studio Express。

## <a name="create-a-namespace"></a>建立命名空間

下一步是建立服務命名空間，並取得共用 Access 簽章 (SA) 鍵。 命名空間提供服務匯流排透過公開，每個應用程式的應用程式邊界。 建立命名空間時，系統會產生 SA 鍵。 命名空間和 SA 按鍵組合提供的服務進行自我驗證 access 應用程式的認證。

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-web-role"></a>建立 web 角色

在此區段中，您可以建立您的應用程式的前端。 首先，您建立的應用程式顯示的頁面。
之後，將程式碼的送出服務匯流排佇列中的項目，並顯示佇列中的狀態資訊。

### <a name="create-the-project"></a>建立專案

1.  使用系統管理員權限，請啟動 Microsoft Visual Studio。 若要開始 Visual Studio 具有管理員權限， **Visual Studio**程式圖示，以滑鼠右鍵按一下，然後再按一下 [**以系統管理員身分執行**。 本文稍後所述的 Azure 計算模擬器需要 Visual Studio 入門系統管理員權限。

    在 Visual Studio 中，按一下 [**檔案**] 功能表按一下 [**新增**]，然後再按一下 [**專案**。

2.  從**安裝的範本**，在**Visual C#**，按一下**雲端**，然後按一下**Azure 雲端服務**。 專案**MultiTierApp**的名稱。 然後按一下**[確定]**。

    ![][9]

3.  **.NET Framework 4.5**角色的存取，按兩下**ASP.NET 網頁角色**。

    ![][10]

4.  將游標停留在**WebRole1** **Azure 雲端服務方案**底下 [鉛筆] 圖示，然後按一下**FrontendWebRole**重新命名的網頁角色。 然後按一下**[確定]**。 （請確認您輸入 「 Frontend 」 以英文小寫 'e，「 不 」 FrontEnd 」）。

    ![][11]

5.  從**新的 ASP.NET 專案**] 對話方塊中**選取範本**] 清單中，按一下 [ **MVC**]。

    ![][12]

6. 仍在**新的 ASP.NET 專案**] 對話方塊中，按一下 [**變更驗證**] 按鈕。 在**變更驗證**] 對話方塊中，按一下 [**無驗證**]，然後按一下**[確定]**。 在此教學課程中，您部署不需要的使用者登入應用程式。

    ![][16]

7. 回到 [**新的 ASP.NET 專案**] 對話方塊中，按一下**[確定**] 以建立專案。

6.  在**方案總管**] 中，在**FrontendWebRole**專案中，**參照**，以滑鼠右鍵按一下，然後按一下 [**管理 NuGet 套件**]。

7.  按一下 [**瀏覽**] 索引標籤，然後搜尋`Microsoft Azure Service Bus`。 按一下 [**安裝**]，並接受使用規定。

    ![][13]

    請注意，所需的用戶端會參考組件現在已新增一些新的程式碼檔案。

9.  在**方案總管]**中，以滑鼠右鍵按一下**模型**並按一下 [**新增**]，然後按一下 [**類別**]。 在 [**名稱**] 方塊中，輸入名稱**OnlineOrder.cs**。 然後按一下 [**新增]**。

### <a name="write-the-code-for-your-web-role"></a>撰寫程式碼，您的 web 角色

在此區段中，您可以建立各種應用程式顯示的頁面。

1.  在 Visual Studio 中 [OnlineOrder.cs 檔案，請將現有的命名空間定義取代下列程式碼︰

    ```
    namespace FrontendWebRole.Models
    {
        public class OnlineOrder
        {
            public string Customer { get; set; }
            public string Product { get; set; }
        }
    }
    ```

2.  在**方案總管**] 中，按兩下 [ **Controllers\HomeController.cs**]。 在包含您剛才建立的模型，以及服務匯流排的命名空間的檔案頂端新增下列**使用**陳述式。

    ```
    using FrontendWebRole.Models;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.ServiceBus;
    ```

3.  也在 Visual Studio 中 [HomeController.cs 檔案] 取代現有的命名空間定義下列程式碼。 將此程式碼包含處理的項目至佇列中送出的方法。

    ```
    namespace FrontendWebRole.Controllers
    {
        public class HomeController : Controller
        {
            public ActionResult Index()
            {
                // Simply redirect to Submit, since Submit will serve as the
                // front page of this application.
                return RedirectToAction("Submit");
            }
    
            public ActionResult About()
            {
                return View();
            }
    
            // GET: /Home/Submit.
            // Controller method for a view you will create for the submission
            // form.
            public ActionResult Submit()
            {
                // Will put code for displaying queue message count here.
    
                return View();
            }
    
            // POST: /Home/Submit.
            // Controller method for handling submissions from the submission
            // form.
            [HttpPost]
            // Attribute to help prevent cross-site scripting attacks and
            // cross-site request forgery.  
            [ValidateAntiForgeryToken]
            public ActionResult Submit(OnlineOrder order)
            {
                if (ModelState.IsValid)
                {
                    // Will put code for submitting to queue here.
    
                    return RedirectToAction("Submit");
                }
                else
                {
                    return View(order);
                }
            }
        }
    }
    ```

4.  按一下 [**建立**] 功能表上的 [**建置解決方案**來測試您的工作的精確度為止。

5.  現在，建立的檢視`Submit()`您先前建立的方法。 內按一下滑鼠右鍵`Submit()`方法 (的超載`Submit()`的沒有參數)，然後選擇 [**新增檢視畫面**。

    ![][14]

6.  建立檢視，會出現的對話方塊。 在 [**範本**] 清單中，選擇 [**建立**]。 在**模型類別**] 清單中，按一下 [ **OnlineOrder**類別。

    ![][15]

7.  按一下 [**新增**]。

8.  現在，變更所顯示的應用程式名稱。 在**方案總管]**中，按兩下**Views\Shared\\_Layout.cshtml** Visual Studio 編輯器中開啟該檔案。

9.  **我的 ASP.NET 應用程式**的所有項目取代**LITWARE 的產品**。

10. 移除**家用版**、**瞭解**以及**連絡人**的連結。 刪除醒目提示的程式碼︰

    ![][28]

11. 最後，修改以包含一些有關佇列中的 [送出] 頁面。 在**方案總管]**中，按兩下**Views\Home\Submit.cshtml**檔案，以在 Visual Studio 編輯器中開啟。 新增後的下列行`<h2>Submit</h2>`。 現在，`ViewBag.MessageCount`是空的。 您會填入更新版本。

    ```
    <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>
    ```

12. 您現在已實作使用者介面。 您可以按**F5**執行您的應用程式，並確認看起來如預期般。

    ![][17]

### <a name="write-the-code-for-submitting-items-to-a-service-bus-queue"></a>撰寫的送出服務匯流排佇列中的項目

現在，新增程式碼送出佇列中的項目。 首先，您可以建立包含您服務匯流排佇列中的連線資訊的類別。 然後，初始化 Global.aspx.cs 從您的連線。 最後，更新 [送出程式碼您先前建立的 HomeController.cs 實際送出服務匯流排佇列中的項目。

1.  在**方案總管]**中，以滑鼠右鍵按一下**FrontendWebRole** （以滑鼠右鍵按一下專案時，不角色）。 按一下 [**新增**]，然後按一下 [**課程**。

2.  類別**QueueConnector.cs**命名。 按一下 [**新增**]，以建立類別]。

3.  現在，新增封裝的連線資訊，並初始化連線到服務匯流排佇列中的程式碼。 下列程式碼，以取代 QueueConnector.cs 的全部內容，並輸入值`your Service Bus namespace`（您的命名空間名稱） 和`yourKey`，這是您先前取得從 Azure 入口網站的**主索引鍵**。

    ```
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.ServiceBus;
    
    namespace FrontendWebRole
    {
        public static class QueueConnector
        {
            // Thread-safe. Recommended that you cache rather than recreating it
            // on every request.
            public static QueueClient OrdersQueueClient;
    
            // Obtain these values from the portal.
            public const string Namespace = "your Service Bus namespace";
    
            // The name of your queue.
            public const string QueueName = "OrdersQueue";
    
            public static NamespaceManager CreateNamespaceManager()
            {
                // Create the namespace manager which gives you access to
                // management operations.
                var uri = ServiceBusEnvironment.CreateServiceUri(
                    "sb", Namespace, String.Empty);
                var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                    "RootManageSharedAccessKey", "yourKey");
                return new NamespaceManager(uri, tP);
            }
    
            public static void Initialize()
            {
                // Using Http to be friendly with outbound firewalls.
                ServiceBusEnvironment.SystemConnectivity.Mode =
                    ConnectivityMode.Http;
    
                // Create the namespace manager which gives you access to
                // management operations.
                var namespaceManager = CreateNamespaceManager();
    
                // Create the queue if it does not exist already.
                if (!namespaceManager.QueueExists(QueueName))
                {
                    namespaceManager.CreateQueue(QueueName);
                }
    
                // Get a client to the queue.
                var messagingFactory = MessagingFactory.Create(
                    namespaceManager.Address,
                    namespaceManager.Settings.TokenProvider);
                OrdersQueueClient = messagingFactory.CreateQueueClient(
                    "OrdersQueue");
            }
        }
    }
    ```

4.  現在，請確定您**初始化**的方法，取得呼叫。 在**方案總管**] 中，按兩下 [ **Global.asax\Global.asax.cs**]。

5.  下列程式碼新增**Application_Start**方法的結尾。

    ```
    FrontendWebRole.QueueConnector.Initialize();
    ```

6.  最後，更新您提交佇列中的項目先前建立的網頁程式碼。 在**方案總管**] 中，按兩下 [ **Controllers\HomeController.cs**]。

7.  更新`Submit()`方法 （超量的狀況不接受參數），如下所示，以取得佇列中的訊息數。

    ```
    public ActionResult Submit()
    {
        // Get a NamespaceManager which allows you to perform management and
        // diagnostic operations on your Service Bus queues.
        var namespaceManager = QueueConnector.CreateNamespaceManager();
    
        // Get the queue, and obtain the message count.
        var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
        ViewBag.MessageCount = queue.MessageCount;
    
        return View();
    }
    ```

8.  更新`Submit(OnlineOrder order)`方法 （採用一個參數超載），如下所示要送出訂單佇列中的資訊。

    ```
    public ActionResult Submit(OnlineOrder order)
    {
        if (ModelState.IsValid)
        {
            // Create a message from the order.
            var message = new BrokeredMessage(order);
    
            // Submit the order.
            QueueConnector.OrdersQueueClient.Send(message);
            return RedirectToAction("Submit");
        }
        else
        {
            return View(order);
        }
    }
    ```

9.  您現在可以執行的應用程式。 送出訂單，每次訊息計數會增加。

    ![][18]

## <a name="create-the-worker-role"></a>建立工作角色

您現在會建立程序的順序提交的工作者角色。 此範例使用**工作者角色與服務匯流排佇列**Visual Studio 專案範本。 您已從入口網站取得必要的認證。

1. 請確定您已連線 Visual Studio Azure 帳戶。

2.  在 Visual Studio 中，在**方案總管**中以滑鼠右鍵按一下在**MultiTierApp**專案底下的 [**角色**] 資料夾。

3.  按一下 [**新增**]，然後按一下 [**新工作角色專案**。 **加入新的角色專案**] 對話方塊隨即出現。

    ![][26]

4.  在 [**加入新的角色專案**] 對話方塊中，按一下**工作者角色與服務匯流排佇列**。

    ![][23]

5.  在 [**名稱**] 方塊中，為專案**OrderProcessingRole**的名稱。 然後按一下 [**新增]**。

6.  將取得連線字串中的 [建立服務匯流排命名空間 」 一節的步驟 9 複製到剪貼簿。

7.  在**方案總管]**中，以滑鼠右鍵按一下您在步驟 5 中建立**OrderProcessingRole** （請確定您按一下滑鼠右鍵**OrderProcessingRole** **角色**與不在課程] 下）。 然後按一下 [**屬性**]。

8.  在 [**設定**] 索引標籤的 [**屬性**] 對話方塊的**Microsoft.ServiceBus.ConnectionString**，按一下 [**值**] 方塊內，再貼上您在步驟 6 中複製的結束點值。

    ![][25]

9.  建立**OnlineOrder**類別來代表訂單，當您從佇列中處理。 您可以重複使用您已經建立一個類別。 在**方案總管]**中，以滑鼠右鍵按一下**OrderProcessingRole**類別 （以滑鼠右鍵按一下 [類別] 圖示，不角色）。 按一下 [**新增**]，然後按一下 [**現有的項目**]。

10. 瀏覽至**FrontendWebRole\Models**的子資料夾，然後按兩下 [ **OnlineOrder.cs**將其新增至 [此專案。

11. 在**WorkerRole.cs**，變更從**QueueName**變數的值`"ProcessingQueue"`至`"OrdersQueue"`下列所示。

    ```
    // The name of your queue.
    const string QueueName = "OrdersQueue";
    ```

12. 新增下列使用 WorkerRole.cs 檔案頂端的陳述式。

    ```
    using FrontendWebRole.Models;
    ```

13. 在 [`Run()`內部函數`OnMessage()`通話、 取代的內容`try`子句下列程式碼。

    ```
    Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
    // View the message as an OnlineOrder.
    OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
    Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
    receivedMessage.Complete();
    ```

14. 您已完成的應用程式。 您可以測試完整的應用程式，以滑鼠右鍵按一下 [MultiTierApp 專案總管] 中的，選取 [**設定為啟動專案**，然後按 F5。 請注意，郵件計數不遞增，因為工作者角色處理佇列中的項目，並將其標示為完成。 您可以檢視 Azure 計算模擬器 UI，請參閱追蹤輸出的工作者角色。 您可以執行這個動作，以滑鼠右鍵按一下工作列的通知區域中的 [模擬器] 圖示，選取 [**顯示計算模擬器 UI**上。

    ![][19]

    ![][20]

## <a name="next-steps"></a>後續步驟  

若要瞭解詳細服務匯流排資訊，請參閱下列資源︰  

* [Azure 服務匯流排][sbmsdn]  
* [服務匯流排服務] 頁面][sbwacom]  
* [如何使用服務匯流排佇列][sbwacomqhowto]  

若要進一步瞭解多層案例，請參閱︰  

* [使用儲存空間的資料表、 佇列和 Blob 的.NET 多層應用程式][mutitierstorage]  

  [0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [取得工具及 SDK]: http://go.microsoft.com/fwlink/?LinkId=271920


  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx
  [NamespaceMananger]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx

  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx

  [EventHubClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx

  [9]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
  [10]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-14.png
  [17]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png

  [19]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [23]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [25]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [28]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png

  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx  
  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: service-bus-dotnet-get-started-with-queues.md  
  [mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  