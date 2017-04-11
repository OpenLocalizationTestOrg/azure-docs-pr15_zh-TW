<properties
    pageTitle="混合式部署上部署/雲端應用程式 (.NET) |Microsoft Azure"
    description="瞭解如何建立使用 Azure 服務匯流排轉送.NET 上部署/雲端混合式應用程式。"
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
    ms.topic="hero-article"
    ms.date="09/16/2016"
    ms.author="sethm"/>

# <a name="net-on-premisescloud-hybrid-application-using-azure-service-bus-relay"></a>使用 Azure 服務匯流排轉送.NET 上部署/雲端混合式應用程式

## <a name="introduction"></a>簡介

本文將說明如何建立與 Microsoft Azure Visual Studio 的混合式雲端應用程式。 教學課程假設您有使用 Azure 沒有先前經驗。 30 分鐘內，則必須使用多個 Azure 資源設定的應用程式，並在雲端中執行。

您將瞭解︰

-   如何建立或調整供使用現有的 web 服務的網頁解決方案。
-   如何使用 Azure 服務匯流排轉送服務 Azure 應用程式和 web 服務間共用資料裝載於別處。

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="how-the-service-bus-relay-helps-with-hybrid-solutions"></a>服務匯流排轉送如何協助使用混合式部署解決方案

商務解決方案通常被組成撰寫處理新且唯一的業務需求和解決方案的和系統已置於所提供的現有功能的自訂程式碼的組合。

開始使用更容易處理比例需求和較低的操作成本雲端解決方案架構。 如此一來，他們到現有的服務資產他們想要使用建置組塊，其解決方案的和公司防火牆內部的並不容易達到存取雲端解決方案。 許多內部服務不是內建或裝載於，他們可以輕鬆地公開在公司網路的方式。

服務匯流排轉送針對現有的 Windows Communication Foundation (WCF) web 服務的使用案例放進行這些服務安全存取解決方案的公司外不對公司網路結構要求侵入變更。 這類服務匯流排轉送服務仍會裝載於現有的環境，但是他們代理人接聽內送工作階段及雲端裝載服務匯流排要求。 服務匯流排也會保護未經授權的存取這些服務使用[共用的 Access 簽章](../service-bus-messaging/service-bus-sas-overview.md)(SA) 驗證。

## <a name="solution-scenario"></a>解決方案案例

在本教學課程中，您將建立的 ASP.NET 網站可讓您在產品庫存頁面上看到的產品清單。

![][0]

教學課程假設您現有的內部部署系統中有產品資訊，並使用服務匯流排轉送連至該系統。 這被模擬以執行簡單的主控台應用程式中，於記憶體中一組產品 web 服務。 您可以在 [您自己的電腦上執行此主控台應用程式，並將 Azure 部署網頁角色。 如此一來，您會看到如何執行 Azure 資料中心的 web 角色會確實撥入您的電腦，即使您的電腦將幾乎都位於至少有一個防火牆被網路位址轉譯 (NAT) 圖層。

以下是已完成的 web 應用程式的 [開始] 頁面的螢幕擷取畫面。

![][1]

## <a name="set-up-the-development-environment"></a>設定的開發環境

開發 Azure 應用程式之前，取得工具，並設定您的開發環境。

1.  安裝.net Azure SDK，從 [[取得的工具和 SDK][]頁面。

2.  按一下 [**安裝 SDK** Visual Studio 您正在使用的版本。 本教學課程中的步驟，使用 Visual Studio 2015。

4.  當畫面提示您執行或儲存安裝程式，請按一下 [**執行**]。

5.  在**網頁的平台安裝程式**中，按一下 [**安裝**]，然後繼續進行安裝。

6.  安裝完成後，您必須開始開發應用程式所需的所有項目。 SDK 包含可讓您輕鬆地開發 Visual Studio 中的 Azure 應用程式的工具。 如果您沒有安裝的 Visual Studio，SDK 也會安裝免費 Visual Studio Express。

## <a name="create-a-namespace"></a>建立命名空間

若要開始使用的 Azure 服務匯流排功能，您必須先建立服務命名空間。 命名空間提供範圍容器來處理您的應用程式中的服務匯流排資源。

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>建立內部部署伺服器

首先，您會建立一個 （模擬） 的內部部署產品類別目錄系統。 就會相當簡單。您可以檢視此為代表實際的內部部署產品目錄的系統與我們想要整合完成服務介面。

此專案是 Visual Studio 主控台應用程式，並使用[Azure 服務匯流排 NuGet 套件](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)包含的服務匯流排文件庫與設定的設定。

### <a name="create-the-project"></a>建立專案

1.  使用系統管理員權限，請啟動 Microsoft Visual Studio。 若要開始 Visual Studio 具有管理員權限， **Visual Studio**程式圖示，以滑鼠右鍵按一下，然後再按一下 [**以系統管理員身分執行**。

2.  在 Visual Studio 中，按一下 [**檔案**] 功能表按一下 [**新增**]，然後再按一下 [**專案**。

3.  從**安裝的範本**， **Visual C#**，按一下 [**主控台應用程式**。 在 [**名稱**] 方塊中，輸入名稱**ProductsServer**:

    ![][11]

4.  按一下**[確定**] 以建立**ProductsServer**專案。

7.  如果您已安裝的 Visual Studio NuGet 封裝管理員，跳至下一個步驟。 否則，請造訪[NuGet][] ，然後按一下 [[安裝 NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)。 依提示安裝 NuGet 套件管理員，然後重新啟動 [Visual Studio。

7.  在 [方案總管**ProductsServer**專案，以滑鼠右鍵按一下，然後按一下 [**管理 NuGet 套件**。

8.  按一下 [**瀏覽**] 索引標籤，然後搜尋`Microsoft Azure Service Bus`。 按一下 [**安裝**]，並接受使用規定。

    ![][13]

    請注意，現在參考必要的用戶端的組件。

9.  新增您的產品合約的新類別。 在方案總管中以滑鼠右鍵按一下**ProductsServer**專案並按一下 [**新增**]，然後按一下**類別**。

10. 在 [**名稱**] 方塊中，輸入名稱**ProductsContract.cs**。 然後按一下 [**新增]**。

11. 在**ProductsContract.cs**，取代下列程式碼，定義服務合約中的命名空間定義。

    ```
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;
    
        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }
    
        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();
    
        }
    
        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

12. 在 Program.cs，取代下列程式碼，新增設定檔服務和，主應用程式中的命名空間定義。

    ```
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;
    
        // Implement the IProducts interface.
        class ProductsService : IProducts
        {
    
            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };
    
            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }
    
        }
    
        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();
    
                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();
    
                sh.Close();
            }
        }
    }
    ```

13. 在方案總管中按兩下**App.config**檔案，以在 Visual Studio 編輯器中開啟。 在底部**&lt;系統。ServiceModel&gt;**項目 (但仍中&lt;系統。ServiceModel&gt;)，新增下列 XML 程式碼。 請務必*yourServiceNamespace*命名空間，及*yourKey*名稱取代先前擷取從入口網站的 SA 索引鍵︰

    ```
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```
14. 在 App.config，還是在**&lt;和 appSettings&gt;**項目、 連線之前取得從入口網站中的連接字串與字串值取代。 

    ```
    <appSettings>
    <!-- Service Bus specific app settings for messaging connections -->
    <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

14. 按下**Ctrl + Shift + B**或從 [**建置**] 功能表中，按一下 [**建立的解決方案**建置應用程式，並確認工作的精確度為止。

## <a name="create-an-aspnet-application"></a>建立 ASP.NET 應用程式

此區段中，您會建立簡單的 ASP.NET 應用程式，顯示從您的產品服務擷取資料。

### <a name="create-the-project"></a>建立專案

1.  確定的 Visual Studio 正在執行具有管理員權限。

2.  在 Visual Studio 中，按一下 [**檔案**] 功能表按一下 [**新增**]，然後再按一下 [**專案**。

3.  從**安裝的範本**， **Visual C#**，按一下 [ **ASP.NET Web 應用程式**。 專案**ProductsPortal**的名稱。 然後按一下**[確定]**。

    ![][15]

4.  從 [**選取範本**] 清單中，按一下 [ **MVC**]。 

6.  主機**在雲端**，核取方塊。

    ![][16]

5. 按一下 [**變更驗證**] 按鈕。 在**變更驗證**] 對話方塊中，按一下 [**無驗證**]，然後按一下**[確定]**。 在此教學課程中，您部署不需要的使用者登入應用程式。

    ![][18]

6.  在 [**新的 ASP.NET 專案**] 對話方塊的 [ **Microsoft Azure** ] 區段中，請確認已選取 [**主控在雲端**，然後在下拉式清單中，會選取**應用程式服務**。

    ![][19]

7. 按一下**[確定]**。 

8. 現在，您必須設定新的 web 應用程式的 Azure 資源。 請遵循[設定 Azure 資源之新的 web 應用程式](../app-service-web/web-sites-dotnet-get-started.md#configure-azure-resources-for-a-new-web-app)] 區段中的所有步驟。 接著，返回本教學課程，然後繼續進行下一個步驟。

5.  在方案總管中，以滑鼠右鍵按一下**模型**並按一下 [**新增**]，然後按一下 [**類別**]。 在 [**名稱**] 方塊中，輸入名稱**Product.cs**。 然後按一下 [**新增]**。

    ![][17]

### <a name="modify-the-web-application"></a>修改 web 應用程式

1.  在 Visual Studio 中 Product.cs 檔案，取代現有的命名空間定義下列程式碼。

    ```
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
        public class Product
        {
            public string Id { get; set; }
            public string Name { get; set; }
            public string Quantity { get; set; }
        }
    }
    ```

2.  在方案總管中，展開**控制站**資料夾，然後按兩下**HomeController.cs**檔案，以在 Visual Studio 中開啟。

3. 在**HomeController.cs**，取代現有的命名空間定義下列程式碼。

    ```
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;
    
        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

3.  在方案總管中，展開 [Views\Shared] 資料夾中，再按兩下**_Layout.cshtml**在 Visual Studio 編輯器中開啟它。

5.  變更**我的 ASP.NET 應用程式**的所有項目**LITWARE 的產品**。

6. 移除**家用版**、**瞭解**以及**連絡人**的連結。 在下列範例中，刪除 [醒目提示的程式碼]。

    ![][41]

7.  在方案總管中，展開 [Views\Home] 資料夾中，再按兩下**Index.cshtml**在 Visual Studio 編輯器中開啟它。
    取代下列程式碼之檔案的完整內容。

    ```
    @model IEnumerable<ProductsWeb.Models.Product>
    
    @{
            ViewBag.Title = "Index";
    }
    
    <h2>Prod Inventory</h2>
    
    <table>
            <tr>
                <th>
                    @Html.DisplayNameFor(model => model.Name)
                </th>
                  <th></th>
                <th>
                    @Html.DisplayNameFor(model => model.Quantity)
                </th>
            </tr>
    
    @foreach (var item in Model) {
            <tr>
                <td>
                    @Html.DisplayFor(modelItem => item.Name)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Quantity)
                </td>
            </tr>
    }
    
    </table>
    ```

9.  到目前為止，以驗證您的工作的精確度您可以按**Ctrl + Shift + B**來建立專案。


### <a name="run-the-app-locally"></a>在本機上執行應用程式

執行應用程式，以確認其運作方式。

1.  請確定**ProductsPortal**使用中的專案。 以滑鼠右鍵按一下方案總管] 中的專案名稱，然後選取 [**設定為啟動專案**。
2.  在 Visual Studio 中，請按 F5。
3.  您的應用程式應該會出現，在瀏覽器中執行。

    ![][21]

## <a name="put-the-pieces-together"></a>將項目放在一起

下一步是連結內部部署產品伺服器 ASP.NET 應用程式。

1.  如果尚未開啟，在 Visual Studio 中重新開啟您在 [[建立 ASP.NET 應用程式](#create-an-aspnet-application)] 區段中建立的**ProductsPortal**專案。

2.  類似的步驟，在 「 建立開啟內部部署伺服器 」 區段中，新增 NuGet 封裝專案參考。 在 [方案總管**ProductsPortal**專案，以滑鼠右鍵按一下，然後按一下 [**管理 NuGet 套件**。

3.  搜尋 「 服務匯流排 」，並選取**Microsoft Azure 服務匯流排**項目。 接著完成安裝，然後關閉此對話方塊。

4.  在方案總管**ProductsPortal**專案，以滑鼠右鍵按一下，然後按一下 [**新增**]，然後**現有項目**]。

5.  瀏覽至**ProductsContract.cs**檔案**ProductsServer**主控台專案。 按一下以醒目提示 ProductsContract.cs。 按一下 [**新增**] 旁的向下箭號，然後按一下 [**新增格式化為連結**]。

    ![][24]

6.  現在 Visual Studio 編輯器中開啟**HomeController.cs**檔案，並以下列程式碼取代命名空間定義。 請務必*yourServiceNamespace*服務命名空間，及*yourKey*名稱取代 SA 金鑰。 這樣會啟用用戶端呼叫的內部部署的服務，傳回呼叫的結果。

    ```
    namespace ProductsWeb.Controllers
    {
        using System.Linq;
        using System.ServiceModel;
        using System.Web.Mvc;
        using Microsoft.ServiceBus;
        using Models;
        using ProductsServer;
    
        public class HomeController : Controller
        {
            // Declare the channel factory.
            static ChannelFactory<IProductsChannel> channelFactory;
    
            static HomeController()
            {
                // Create shared access signature token credentials for authentication.
                channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                    "sb://yourServiceNamespace.servicebus.windows.net/products");
                channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                    TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                        "RootManageSharedAccessKey", "yourKey") });
            }
    
            public ActionResult Index()
            {
                using (IProductsChannel channel = channelFactory.CreateChannel())
                {
                    // Return a view of the products inventory.
                    return this.View(from prod in channel.GetProducts()
                                     select
                                         new Product { Id = prod.Id, Name = prod.Name,
                                             Quantity = prod.Quantity });
                }
            }
        }
    }
    ```

7.  在方案總管中，以滑鼠右鍵按一下**ProductsPortal**方案 （請務必以滑鼠右鍵按一下方案，而非專案）。 按一下 [**新增**]，然後按一下 [**現有的專案**。

8.  瀏覽至**ProductsServer**專案，然後按兩下**ProductsServer.csproj**解決方案檔案，以將其新增]。

9.  **ProductsServer**必須執行才能**ProductsPortal**上顯示的資料。 在方案總管中，以滑鼠右鍵按一下**ProductsPortal**解決方案，然後按一下 [**內容**。 [**頁面屬性**] 對話方塊隨即出現。

10. 在左邊，按一下 [**啟動專案**]。 在右側，按一下 [**多個啟動專案**。 請確定， **ProductsServer**和**ProductsPortal**會出現，請使用**開始**設定為兩個動作。

      ![][25]

11. 仍在 [**屬性**] 對話方塊中，按一下左側的**專案的相依性**。

12. 在 [**專案**] 清單中，按一下 [ **ProductsServer**。 請確定**ProductsPortal**是 「**未**」 選取。

14. 在 [**專案**] 清單中，按一下 [ **ProductsPortal**。 確定已選取 [ **ProductsServer** 。 

    ![][26]

15. 按一下 [**頁面屬性**] 對話方塊中的**[確定**]。

## <a name="run-the-project-locally"></a>在本機上執行的專案

若要測試本機的應用程式，在 Visual Studio 中按**F5**。 內部部署伺服器 (**ProductsServer**) 應該第一個，然後**ProductsPortal**應用程式應該在瀏覽器視窗中啟動。 這次，您會看到產品庫存清單從產品服務內部部署系統擷取資料。

![][10]

在 [ **ProductsPortal**頁面上，按下**重新整理**。 每次重新整理頁面，您會看到顯示郵件的伺服器應用程式時`GetProducts()`從**ProductsServer**一部分。

關閉這兩種應用程式，再繼續執行下一個步驟。

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>部署 ProductsPortal 專案至 Azure web 應用程式

下一個步驟是將**ProductsPortal** frontend 轉換成 Azure web 應用程式。 首先，部署**ProductsPortal**專案，在[部署 web 專案 Azure web 應用程式](../app-service-web/web-sites-dotnet-get-started.md#deploy-the-web-project-to-the-azure-web-app)] 區段中的所有步驟。 部署完成後，返回本教學課程，然後繼續進行下一個步驟。

> [AZURE.NOTE] 部署之後自動啟動**ProductsPortal** web 專案時，您可能會看到瀏覽器視窗中的錯誤訊息。 這預期，就會發生**ProductsServer**應用程式還不在執行。

複製的 URL 部署的 web 應用程式時，您會需要的下一個步驟中的 URL。 您也可以取得這個 URL Azure 應用程式服務活動視窗中，在 Visual Studio 中︰

![][9] 

### <a name="set-productsportal-as-web-app"></a>為 web app 設定 ProductsPortal

之前執行應用程式在雲端，您必須確定**ProductsPortal**從啟動為 web 應用程式的 Visual Studio 中。

1. 在 Visual Studio 中，以滑鼠右鍵按一下**ProjectsPortal**專案，然後按一下**屬性**。

3. 在 [左] 欄中，按一下 [ **Web**]。

5. 在**起始動作**] 區段中，按一下 [**啟動 URL** ] 按鈕，，在 [文字] 方塊中輸入的 URL 您先前已部署的 web 應用程式。例如， `http://productsportal1234567890.azurewebsites.net/`。

    ![][27]

6. 從 [Visual Studio 中的 [**檔案**] 功能表中，按一下 [**全部儲存**]。

7. 從 [Visual Studio 中的 [建立] 功能表中，按一下 [**重建方案**]。

## <a name="run-the-application"></a>執行應用程式

2.  按 F5 以建立並執行應用程式。 內部部署伺服器 （ **ProductsServer**主控台應用程式） 應該第一個，然後在瀏覽器視窗中，應開始**ProductsPortal**應用程式，如以下的螢幕擷取畫面所示。 請注意再次產品存貨列出從產品服務內部部署系統，擷取資料，並在 web 應用程式中顯示該資料。 請確定**ProductsPortal**正在執行在雲端，為 Azure web 應用程式的 URL。 

    ![][1]

    > [AZURE.IMPORTANT] **ProductsServer**主控台應用程式必須執行，並且可以做**ProductsPortal**應用程式的資料。 如果在瀏覽器中顯示錯誤，請稍候更多的**ProductsServer**載入並顯示下列訊息。 然後按下**重新整理**瀏覽器中。

    ![][37]

3. 回瀏覽器中，按下 [ **ProductsPortal** ] 頁面上的**重新整理**。 每次重新整理頁面，您會看到顯示郵件的伺服器應用程式時`GetProducts()`從**ProductsServer**一部分。

    ![][38]

## <a name="next-steps"></a>後續步驟  

若要瞭解詳細服務匯流排資訊，請參閱下列資源︰  

* [Azure 服務匯流排][sbwacom]  
* [如何使用服務匯流排佇列][sbwacomqhowto]  


  [0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [取得工具及 SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  
  [11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
  [18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
  [19]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-6.png
  [9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
  [10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

  [21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png
  
  [36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
  [41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png


  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

