<properties 
    pageTitle="服務匯流排轉送教學課程 |Microsoft Azure"
    description="應用程式和服務匯流排轉送服務，請建立服務匯流排用戶端。"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-relay-tutorial"></a>服務匯流排轉送教學課程

本教學課程說明如何建立簡單的服務匯流排用戶端應用程式與服務使用的服務匯流排 」 轉送 」 功能。 使用服務匯流排[仲介訊息](../service-bus-messaging/service-bus-messaging-overview.md#Brokered-messaging)的相對應的教學課程，請參閱[服務匯流排代理訊息.NET 教學課程](../service-bus-messaging/service-bus-brokered-tutorial-dotnet.md)。

透過此教學課程中的工作可讓您瞭解的才能建立服務匯流排用戶端和服務應用程式的步驟。 WCF 對應的部分，例如服務是建構，公開一或多個端點，每個公開一或多個服務作業。 服務的端點指定的地址何處可以找到服務，繫結的用戶端必須通訊合約定義提供服務給用戶端的功能，與該服務中的資訊。 WCF 和服務匯流排服務的主要差異是端點公開在雲端而不是本機電腦上。

您逐步完成的主題順序在本教學課程後，您必須執行服務，並可叫用的服務作業的用戶端。 第一個主題說明如何設定的帳戶。 接下來的步驟說明如何定義合約時所使用的服務、 實作服務，以及如何設定程式碼服務。 他們也會說明如何裝載並執行服務。 自行主控所建立的服務並在同一部電腦上執行的用戶端和服務。 您可以設定服務所使用的程式碼或設定檔。

最終的三個步驟說明如何建立用戶端應用程式，請設定用戶端應用程式，並建立及使用可以存取的主機功能的用戶端。

所有在這個區段中的主題假設您使用 Visual Studio 開發環境。

## <a name="sign-up-for-an-account"></a>登入的帳戶

第一步是建立命名空間，並取得共用 Access 簽章 (SA) 鍵。 命名空間提供服務匯流排透過公開，每個應用程式的應用程式邊界。 當您建立的服務命名空間時 SA 鍵會自動產生系統。 服務命名空間和 SA 按鍵組合提供的服務進行自我驗證 access 應用程式的認證。

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract-to-use-with-service-bus"></a>定義 WCF 服務合約與服務匯流排搭配使用

服務合約指定哪些作業 （Web 服務術語方法或函數） 服務支援。 合約的建立方式定義 c + +、 C# 或 Visual Basic 介面。 在介面中的每一種方法會對應到特定的服務作業。 每個介面必須[ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx)屬性，而且每個作業必須擁有套用[OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx)屬性。 如果中具有[ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx)屬性的方法沒有[OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx)屬性，方法不是公開。 在範例中的程序提供這些工作的程式碼。 合約和服務的較大的討論區，請參閱 WCF 文件中的[設計及實作服務](https://msdn.microsoft.com/library/ms729746.aspx)。

### <a name="to-create-a-service-bus-contract-with-an-interface"></a>若要建立服務匯流排合約介面

1. 以管理員身分開啟 Visual Studio，以滑鼠右鍵按一下**[開始**] 功能表上的程式，然後選取 [**以系統管理員身分執行**。

2. 建立新的主控台應用程式專案。 按一下 [**檔案**] 功能表選取 [**新增**]，然後按一下 [**專案**]。 在 [**新專案**] 對話方塊中，按一下**Visual C#** （如果**Visual C#**沒有出現，查看**其他語言**] 之下）。 按一下 [**主控台應用程式**範本，並將其命名為 [ **EchoService**。 按一下**[確定**] 以建立專案。

    ![][2]

3. 安裝的服務匯流排 NuGet 套件。 此套件會自動新增到服務匯流排文件庫，以及 WCF **System.ServiceModel**的參照。 [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx)是命名空間，讓您以程式控制方式存取 WCF 的基本功能。 服務匯流排使用許多物件和屬性 WCF 定義服務合約。

    在方案總管解決方案，請以滑鼠右鍵按一下，然後按一下**管理 NuGet 套件的解決方案**。 按一下 [**瀏覽**] 索引標籤，然後搜尋`Microsoft Azure Service Bus`。 請確定 [**版本**] 方塊中已選取的專案名稱。 按一下 [**安裝**]，並接受使用規定。

    ![][3]

3. 如果尚未開啟，請在方案總管中，按兩下 Program.cs 檔案，在編輯器] 中開啟。

4. 新增下列使用在檔案頂端的陳述式︰

    ```
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. 變更其預設的名稱**EchoService** **Microsoft.ServiceBus.Samples**命名空間名稱。

    >[AZURE.IMPORTANT] 本教學課程中使用 C# 命名空間**Microsoft.ServiceBus.Samples**，也就是受管理的合約類型[WCF 用戶端的設定](#configure-the-wcf-client)步驟中的設定檔中所使用的命名空間。 您可以指定當您建立這個範例中，您需要的命名空間不過，除非您修改合約的命名空間並相應地服務應用程式的設定檔中，便無法運作教學課程。 指定 App.config 檔案中的命名空間必須指定 C# 檔案中的命名空間相同。

1. 直接之後`Microsoft.ServiceBus.Samples`命名空間宣告，但在命名空間，定義名稱為新的介面`IEchoContract`並套用`ServiceContractAttribute`命名空間值為**http://samples.microsoft.com/ServiceModel/Relay/**介面屬性。 命名空間值與不同的命名空間，您使用整個程式碼的範圍。 不過，命名空間值作為此合約的唯一識別碼。 明確地指定命名空間可防止預設命名空間值新增至合約名稱。

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    >[AZURE.NOTE] 一般而言，服務合約命名空間含有命名的配置，其中包含版本資訊。 包括服務合約命名空間版本資訊可讓服務隔離定義新的服務合約與新的命名空間，並將它公開新的端點上的主要變更。 這種方式，用戶端可以繼續使用舊的服務合約，而不需要進行更新。 版本資訊可以包含的日期或建立數字。 如需詳細資訊，請參閱[服務版本設定](http://go.microsoft.com/fwlink/?LinkID=180498)。 進行本教學課程，服務合約命名空間的命名配置不包含版本資訊。

1. 內`IEchoContract`介面、 宣告單一作業的方法`IEchoContract`合約公開介面，並套用`OperationContractAttribute`屬性設定為您想要公開公用服務匯流排合約的方法。

    ```
    [OperationContract]
    string Echo(string text);
    ```

1. 直接之後`IEchoContract`介面定義、 宣告頻道繼承自兩者`IEchoContract`以及`IClientChannel`介面，如下所示︰

    ```
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    頻道是透過該主機和用戶端傳遞資訊彼此的 WCF 物件。 之後，您會針對撰寫程式碼頻道回應之間的兩個應用程式的資訊。

1. 從 [**建置**] 功能表中，按一下 [**建立的解決方案**或按**Ctrl + Shift + B** ，確認您的工作為止。

### <a name="example"></a>範例

下列程式碼會顯示定義服務匯流排合約基本介面。

```
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

現在，建立介面，您可以執行的介面。

## <a name="implement-the-wcf-contract-to-use-service-bus"></a>實作使用服務匯流排 WCF 合約

建立服務匯流排轉送需要您先建立合約時，請使用介面定義。 如需有關如何建立介面的詳細資訊，請參閱上一個步驟。 下一步是實作介面。 這包含建立名為類別`EchoService`實作使用者定義`IEchoContract`介面。 實作介面之後，您設定使用 App.config 設定檔的介面。 設定檔案包含必要的資訊，請在應用程式，例如服務的名稱、 合約和通訊協定，用來與服務匯流排通訊類型的名稱。 在範例中的程序提供這些任務所用的程式碼。 如需如何實作服務合約一般討論，請參閱 WCF 文件中的[實作服務合約](https://msdn.microsoft.com/library/ms733764.aspx)。

1. 建立新類別名為`EchoService`直接後面的定義`IEchoContract`介面。 `EchoService`類別實作`IEchoContract`介面。 

    ```
    class EchoService : IEchoContract
    {
    }
    ```
    
    與其他介面實作，您可以在不同的檔案中實作定義。 不過，在此教學課程，實作位於相同的檔案作為介面定義和`Main`方法。

1. 套用至[ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx)屬性`IEchoContract`介面。 此屬性會指定服務名稱和命名空間。 之後，`EchoService`類別顯示，如下所示︰

    ```
    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. 實作`Echo`中定義方法`IEchoContract`介面中`EchoService`類別。 

    ```
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. 按一下 [**建立**]，然後按一下 [**建立的解決方案**，以確認您的工作的精確度。

### <a name="to-define-the-configuration-for-the-service-host"></a>若要定義為服務主機設定

1. 設定檔案是非常類似 WCF 設定檔。 它包含服務名稱、 結束點 （也就是位置服務匯流排公開的用戶端，並裝載彼此） 並繫結 （用來進行通訊的通訊協定類型）。 主要差異是，此設定的服務端點指的是不是一部分的.NET framework [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx)繫結。 [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx)是一種服務匯流排所定義的繫結。

1. 在**方案總管]**中，按兩下 App.config 檔案，以在 Visual Studio 編輯器中開啟。

2. 在 [`<appSettings>`項目、 版面配置區名稱的服務的命名空間及 SA 按鍵您在先前的步驟中複製的取代。 

1. 內`<system.serviceModel>`標籤]，新增`<services>`項目。 您可以在單一的設定檔中定義多個服務匯流排應用程式。 不過，在此教學課程定義只有一個。
 
    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. 內`<services>`項目，請新增`<service>`項目以定義服務的名稱。

    ```
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. 內`<service>`項目，請定義端點合約，以及端點的繫結類型的位置。

    ```
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    端點定義用戶端主機應用程式的外觀的位置。 更新版本中，教學課程會使用這個步驟，來建立 URI 完全公開透過服務匯流排主機。 繫結宣告，我們使用 TCP 為通訊協定與服務匯流排通訊。

1. 從 [**建置**] 功能表中，按一下 [**建立的解決方案**，以確認您的工作的正確性]。

### <a name="example"></a>範例

下列程式碼會顯示服務合約的實作。

```
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

下列程式碼會顯示服務主機相關聯的 App.config 檔案的基本格式。

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-a-basic-web-service-to-register-with-service-bus"></a>裝載並執行基本的 Web 服務，以註冊服務匯流排

此步驟會說明如何執行基本的服務匯流排服務。

### <a name="to-create-the-service-bus-credentials"></a>若要建立的服務匯流排認證

1. 在`Main()`、 建立兩個變數，用來儲存命名空間和 SA 重要的讀取主控台視窗中。

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    SA 鍵會用來存取服務匯流排專案的更新版本。 命名空間傳遞的參數為`CreateServiceUri`來建立 URI 的服務。

4. 使用[TransportClientEndpointBehavior](https://msdn.microsoft.com/library/microsoft.servicebus.transportclientendpointbehavior.aspx)物件，將您會使用 SA 金鑰認證類型。 直接在最後一個步驟中加入的程式碼之後新增下列程式碼。

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="to-create-a-base-address-for-the-service"></a>若要建立服務基底地址

1. 追蹤您在最後一個步驟中，加入的程式碼建立`Uri`服務執行個體的基底地址。 此 URI 指定服務匯流排配置命名空間，與服務介面的路徑。

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

    「 sb 」 服務匯流排配置的縮寫，表示我們使用 TCP 為通訊協定。 這也先前指出在設定檔中，當[NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx)已指定為繫結。
    
    在此教學課程中，為 URI `sb://putServiceNamespaceHere.windows.net/EchoService`。

### <a name="to-create-and-configure-the-service-host"></a>建立和設定服務主機

1. 若要設定連線模式`AutoDetect`。

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    連線模式說明服務使用與服務匯流排; 通訊的通訊協定HTTP 或 TCP。 使用預設設定`AutoDetect`，如果無法使用 TCP 透過 TCP 如果有的話和 HTTP 連線到服務匯流排嘗試服務。 請注意，這與不同通訊協定服務指定的用戶端的通訊。 使用的繫結取決於該通訊協定。 例如，服務可以使用[BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx)繫結、 指定 （公開服務匯流排） 其結束點通訊與用戶端透過 HTTP。 相同的服務無法指定**ConnectivityMode.AutoDetect** ，以便透過 TCP 服務會與服務匯流排通訊。

1. 建立服務主機，請使用 URI 先前建立此區段中。

    ```
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    WCF 物件會產生服務服務主機。 在這裡，您將它傳遞的服務您想要建立的類型 (`EchoService`類型)，以及也要公開服務的地址。

1. Program.cs 檔案頂端新增[System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx)和[Microsoft.ServiceBus.Description](https://msdn.microsoft.com/library/microsoft.servicebus.description.aspx)的參考。

    ```
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. 返回`Main()`，設定要啟用公用存取的端點。

    ```
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    此步驟會通知您的應用程式找到的公開檢查服務匯流排 ATOM 服務匯流排摘要專案。 您可以將**DiscoveryType**為**私人**，如果客戶想仍然可以存取服務。 不過，服務就不會出現時，搜尋服務匯流排命名空間。 不過，用戶端有事先知道端點路徑。

1. 定義在 App.config 檔案的服務端點套用服務認證︰

    ```
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    上一個步驟所述，您可能會宣告多個服務與設定檔中的端點。 如果您有，將此程式碼會往返的設定檔和搜尋的每個端點，應該要套用您的認證。 不過，在此教學課程中，設定檔有一個端點。

### <a name="to-open-the-service-host"></a>若要開啟服務主機

1. 開啟的服務。

    ```
    host.Open();
    ```

1. 服務正在執行，並說明如何關閉服務，請通知使用者。

    ```
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. 完成後，請關閉 [服務主機。

    ```
    host.Close();
    ```

1. 按下**Ctrl + Shift + B**來建立專案。

### <a name="example"></a>範例

下列範例會在教學課程中，包含的服務合約和實作從先前的步驟，並裝載主控台應用程式中的服務。 編譯可執行檔名為 EchoService.exe 下列。

```
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         
          
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Service Bus credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }
            
            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>建立 WCF 用戶端的服務合約

下一步是建立基本的服務匯流排用戶端應用程式，並定義服務合約在稍後的步驟執行。 請注意下列步驟執行許多看起來像用來建立服務的步驟︰ 定義合約時，請編輯 App.config 的檔案，使用認證以連線至服務匯流排，依此類推。 在範例中的程序提供這些任務所用的程式碼。

1. 建立新專案目前的 Visual Studio 方案中的用戶端執行下列動作︰
    1. 在方案總管中，在相同的方案包含該服務中的目前方案 （非專案），以滑鼠右鍵按一下，然後按一下 [**新增**]。 然後按一下 [**新專案**]。
    2. 在 [**加入新的專案**] 對話方塊中，按一下 [ **Visual C#** （如果**Visual C#**沒有出現，查看**其他語言**] 之下），選取 [**主控台應用程式**範本，並將其命名為 [ **EchoClient**。
    3. 按一下**[確定]**。
<br />

1. 如果尚未開啟，請在方案總管中，按兩下 Program.cs 專案中的檔案**EchoClient** ，在編輯器] 中開啟。

1. 從其預設名稱變更命名空間名稱`EchoClient`至`Microsoft.ServiceBus.Samples`。

1. 安裝[服務匯流排 NuGet 套件](https://www.nuget.org/packages/WindowsAzure.ServiceBus)。 在方案總管中**EchoClient**專案，以滑鼠右鍵按一下，然後再按一下 [**管理 NuGet 套件**。 按一下 [**瀏覽**] 索引標籤，然後搜尋`Microsoft Azure Service Bus`。 按一下 [**安裝**]，並接受使用規定。

    ![][3]

1. 新增`using` [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx)命名空間 Program.cs 檔案中的陳述式。 

    ```
    using System.ServiceModel;
    ```

1. 將服務合約定義加入命名空間，如下列範例所示。 請注意，此定義與**服務**的專案中使用的定義。 您應該在頂端新增此程式碼`Microsoft.ServiceBus.Samples`命名空間。

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. 按下**Ctrl + Shift + B** ，建立用戶端。

### <a name="example"></a>範例

下列程式碼 EchoClient project 中顯示 Program.cs 檔案的目前狀態。

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>設定 WCF 用戶端

在此步驟中，您可以建立基本的用戶端應用程式存取在本教學課程先前所建立的服務 App.config 檔案。 此 App.config 檔案定義合約、 繫結和端點的名稱。 在範例中的程序提供這些任務所用的程式碼。

1. 在方案總管中，在**EchoClient**專案中，按兩下 [ **App.config** Visual Studio 編輯器中開啟檔案。

2. 在 [`<appSettings>`項目、 版面配置區名稱的服務的命名空間及 SA 按鍵您在先前的步驟中複製的取代。

1. 在 system.serviceModel 項目，新增`<client>`項目。

    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    此步驟宣告定義 WCF 樣式用戶端應用程式。

1. 內`client`項目，定義名稱、 合約和端點的繫結類型。

    ```
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    此步驟中定義端點，合約服務，與用戶端應用程式的使用與服務匯流排通訊 TCP 交易中定義的名稱。 結束點名稱用於下一個步驟中，以連結服務 URI 此端點設定。

1. 按一下 [**檔案**]，然後**全部儲存**]。

## <a name="example"></a>範例

下列程式碼會顯示回應用戶端 App.config 檔案。

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client-to-call-service-bus"></a>實作撥打服務匯流排 WCF 用戶端

在此步驟中，您可以實作存取的服務，您先前在本教學課程中建立基本的用戶端應用程式。 類似服務、 用戶端執行許多存取服務匯流排相同的作業︰

1. 設定連線模式。
1. 建立尋找主機服務 URI。
1. 定義的安全性憑證。
1. 適用於連線認證。
1. 隨即會開啟連線。
1. 執行的特定應用程式的工作。
1. 關閉的連線。

不過，其中一項主要差異，是用戶端應用程式使用頻道連線至服務匯流排而服務使用**ServiceHost**呼叫。 在範例中的程序提供這些任務所用的程式碼。

### <a name="to-implement-a-client-application"></a>若要執行的用戶端應用程式

1. 若要**自動偵測**設定連線模式。 新增下列程式碼`Main()` **EchoClient**應用程式的方法。

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. 定義如要保留的值 SA 鍵與服務的命名空間，從主控台讀取的變數。

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. 建立 URI 服務匯流排專案中定義的主機位置。

    ```
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. 建立您的服務命名空間端點認證物件。

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. 建立頻道工廠載入 App.config 檔案中所描述的設定。

    ```
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    頻道工廠是 WCF 物件，建立的頻道用的服務與用戶端應用程式進行通訊。

1. 適用於服務匯流排認證。

    ```
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. 建立並開啟服務頻道。

    ```
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. 撰寫基本的使用者介面和功能回音。

    ```
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    請注意，程式碼使用通道物件的執行個體的 proxy 服務。

1. 關閉頻道，並關閉工廠。

    ```
    channel.Close();
    channelFactory.Close();
    ```

## <a name="to-run-the-applications"></a>若要執行應用程式

1. 按下**Ctrl + Shift + B**建置解決方案。 此建置用戶端專案服務專案中的上一個步驟所建立。

2. 在執行之前的用戶端應用程式，您必須確定服務應用程式正在執行。 在方案總管 Visual Studio 中**EchoService**解決方案，請以滑鼠右鍵按一下，然後按一下 [**內容**]。

3. 在方案內容] 對話方塊中，按一下**啟動專案**，然後按一下 [**多個啟動專案**] 按鈕。 請確定**EchoService**出現在清單中第一個。 

4. 若要**開始**設定**EchoService** 」 和 「 **EchoClient**專案的 [**動作**] 方塊。

    ![][5]

5. 按一下 [**專案的相依性**]。 在 [**專案**] 方塊中，選取**EchoClient**。 在**相依於]**方塊中，請確定已核取 [ **EchoService** 。

    ![][6]

6. 按一下**[確定**] 關閉 [**屬性**] 對話方塊。

7. 若要執行這兩個專案按**F5** 。

8. 這兩個主控台視窗開啟，並提示您輸入的命名空間名稱。 服務必須執行第一個，請在 [ **EchoService**主控台] 視窗中，輸入命名空間，然後按 [ **enter 鍵**。

9. 接著，系統會提示您 SA 鍵。 輸入 SA 鍵，然後按 ENTER。

    以下是從主控台視窗輸出的範例。 請注意，例如如下僅供提供數值。

    `Your Service Namespace: myNamespace`
    `Your SAS Key: <SAS key value>`

    服務應用程式列印至主控台視窗的地址，其為要在其接聽，如下列範例所示。

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`
    `Press [Enter] to exit`
    
10. 在 [ **EchoClient**主控台] 視窗中，輸入您先前輸入服務應用程式的相同資訊。 請遵循上述步驟，輸入用戶端應用程式的同一個服務命名空間和 SA 關鍵值。

11. 輸入之後這些值，請用戶端開啟服務頻道，並會提示您輸入一些文字，如下列主控台輸出範例所示。

    `Enter text to echo (or [Enter] to exit):` 

    輸入一些文字，以傳送至服務應用程式，然後按 Enter。 此文字會傳送至透過回音服務作業的服務，並顯示在服務主控台視窗，如下列範例輸出所示。

    `Echoing: My sample text`

    用戶端應用程式收到的傳回值`Echo`作業，因為它是原始的文字，再將它列印至其主控台] 視窗。 以下是從用戶端主控台視窗輸出的範例。

    `Server echoed: My sample text`

12. 您可以繼續傳送簡訊從用戶端，這種方式中的服務。 當您完成時，按下 Enter 在用戶端與服務的主控台視窗中結束兩個應用程式。

## <a name="example"></a>範例

下列範例示範如何建立用戶端應用程式、 如何呼叫的作業的服務，以及如何操作通話完成後，請關閉用戶端。

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;

            
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="next-steps"></a>後續步驟

本教學課程中會顯示如何建立服務匯流排用戶端應用程式和服務使用的服務匯流排 」 轉送 」 功能。 使用服務匯流排[訊息](../service-bus-messaging/service-bus-messaging-overview.md#Brokered-messaging)的類似教學課程，請參閱[服務匯流排代理訊息.NET 教學課程](../service-bus-messaging/service-bus-brokered-tutorial-dotnet.md)。

若要瞭解詳細服務匯流排資訊，請參閱下列主題。

- [服務匯流排訊息概觀](../service-bus-messaging/service-bus-messaging-overview.md)
- [服務匯流排基本概念](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- [服務匯流排架構](../service-bus-messaging/service-bus-architecture.md)

[Azure classic portal]: http://manage.windowsazure.com

[1]: ./media/service-bus-relay-tutorial/service-bus-policies.png
[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[4]: ./media/service-bus-relay-tutorial/create-ns.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
