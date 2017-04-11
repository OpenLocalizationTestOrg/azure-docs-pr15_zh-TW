<properties
    pageTitle="服務匯流排其餘教學課程使用轉送訊息 |Microsoft Azure"
    description="建立簡單服務匯流排轉送主機公開的應用程式的其餘部分為基礎的介面。"
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
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-relay-rest-tutorial"></a>服務匯流排轉送其餘教學課程

本教學課程說明如何建立簡單的服務匯流排主應用程式公開其餘為基礎的介面。 其他可讓網頁用戶端，例如網頁瀏覽器中，透過 HTTP 要求存取服務匯流排 Api。

本教學課程中使用 Windows Communication Foundation (WCF) 其餘程式設計模型建構服務匯流排的其餘服務。 如需詳細資訊，請參閱[WCF 其餘程式設計模型](https://msdn.microsoft.com/library/bb412169.aspx)和[設計及實作服務](https://msdn.microsoft.com/library/ms729746.aspx)的 WCF 文件。

## <a name="step-1-create-a-service-namespace"></a>步驟 1︰ 建立服務命名空間

第一步是建立命名空間，並取得共用 Access 簽章 (SA) 鍵。 命名空間提供服務匯流排透過公開，每個應用程式的應用程式邊界。 當您建立的服務命名空間時 SA 鍵會自動產生系統。 服務命名空間和 SA 按鍵組合提供的服務進行自我驗證 access 應用程式的認證。

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="step-2-define-a-rest-based-wcf-service-contract-to-use-with-service-bus"></a>步驟 2︰ 定義其餘型 WCF 服務合約與服務匯流排搭配使用

以與其他服務匯流排服務，當您建立其他樣式服務，您必須定義合約。 合約指定主應用程式支援哪些作業。 服務作業可視為 web 服務方法。 合約的建立方式定義 c + +、 C# 或 Visual Basic 介面。 在介面中的每一種方法會對應到特定的服務作業。 [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx)屬性必須套用至每個介面，並[OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx)屬性必須套用至每一項作業。 如果中具有[ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx)的方法沒有[OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx)，方法不是公開。 使用這些任務的程式碼會顯示在範例中的程序。

基本的服務匯流排合約和其他樣式合約的主要差異是加入的屬性以[OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx): [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx)。 此屬性可讓您將在您的介面中的方法對應到另一端介面的方法。 在此案例中，我們將使用[WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx) HTTP GET 連結方法。 這個選項可讓服務匯流排精準地擷取與判讀介面傳送的命令。

### <a name="to-create-a-service-bus-contract-with-an-interface"></a>若要建立服務匯流排合約介面

1. 開啟 [以系統管理員的 Visual Studio︰ 以滑鼠右鍵按一下 [**開始**] 功能表上的程式，然後按一下 [**系統管理員身分執行**。

2. 建立新的主控台應用程式專案。 按一下 [**檔案**] 功能表選取 [**新增**]，然後選取 [**專案**]。 在 [**新專案**] 對話方塊中，按一下 [ **Visual C#**、 選取**主控台應用程式**範本，並將其命名**ImageListener**。 使用預設的**位置**。 按一下**[確定**] 以建立專案。

3. Visual Studio 建立 C# 專案，`Program.cs`檔案。 這個類別包含空`Main()`主控台應用程式專案正確地建立所需的方法。

4. 新增到專案服務匯流排和**System.ServiceModel.dll**參考安裝的服務匯流排 NuGet 套件。 此套件會自動新增到服務匯流排文件庫，以及 WCF **System.ServiceModel**的參照。 在方案總管中**ImageListener**專案，以滑鼠右鍵按一下，然後再按一下 [**管理 NuGet 套件**。 按一下 [**瀏覽**] 索引標籤，然後搜尋`Microsoft Azure Service Bus`。 按一下 [**安裝**]，並接受使用規定。

5. 您必須明確將專案**System.ServiceModel.Web.dll**的參考加入︰

    。 在方案總管中，以滑鼠右鍵按一下專案資料夾下的 [**參考資料**] 資料夾，然後按一下**新增的參照**。

    b。 在**新增的參照位址**] 對話方塊中，按一下 [**架構**] 索引標籤左側，並在 [**搜尋**] 方塊中，輸入**System.ServiceModel.Web**。 選取**System.ServiceModel.Web** ] 核取方塊，然後按一下**[確定**]。

6. 新增下列`using`Program.cs 檔案頂端的陳述式。

    ```
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx)是命名空間，讓以程式設計方式存取 WCF 基本功能。 服務匯流排使用許多物件和屬性 WCF 定義服務合約。 在大部分的服務匯流排轉送應用程式中，您會使用這個命名空間。 同樣地， [System.ServiceModel.Channels](https://msdn.microsoft.com/library/system.servicemodel.channels.aspx)可協助定義頻道，也就是透過這個通訊服務匯流排與用戶端的網頁瀏覽器的物件。 最後， [System.ServiceModel.Web](https://msdn.microsoft.com/library/system.servicemodel.web.aspx)的內容類型，可讓您建立 web 應用程式。

7. 重新命名`ImageListener` **Microsoft.ServiceBus.Samples**命名空間。

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

8. 左大括弧的命名空間宣告，定義後新介面名為**IImageContract** ，並套用**ServiceContractAttribute**屬性值為介面直接`http://samples.microsoft.com/ServiceModel/Relay/`。 命名空間值與不同的命名空間，您使用整個程式碼的範圍。 命名空間值作為本合約、 的唯一識別碼，然後應有版本資訊。 如需詳細資訊，請參閱[服務版本設定](http://go.microsoft.com/fwlink/?LinkID=180498)。 明確地指定命名空間可防止預設命名空間值新增至合約名稱。

    ```
    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

9. 內`IImageContract`介面、 宣告單一作業的方法`IImageContract`合約公開介面，並套用`OperationContractAttribute`屬性設定為您想要公開公用服務匯流排合約的方法。

    ```
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

10. 在**OperationContract**屬性中，新增**WebGet**值。

    ```
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

    如此可讓路由 HTTP GET 要求服務匯流排`GetImage`，以及轉譯的傳回值`GetImage`到 HTTP GETRESPONSE 回覆。 稍後的教學課程中，您會使用網頁瀏覽器，此方法，並顯示在瀏覽器中的圖像。

11. 直接之後`IImageContract`定義，宣告頻道繼承自兩者`IImageContract`和`IClientChannel`介面。

    ```
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

    頻道是透過此服務與用戶端傳遞資訊彼此的 WCF 物件。 之後，您將建立頻道主機應用程式中。 服務匯流排然後使用此通道，將您的**GetImage**實作從瀏覽器的 HTTP GET 要求。 服務匯流排也會使用**GetImage**傳回值並將它解譯成的用戶端瀏覽器 HTTP GETRESPONSE 的頻道。

12. 從 [**建置**] 功能表中，按一下 [**建立的解決方案**，確認您的工作的精確度為止。

### <a name="example"></a>範例

下列程式碼會顯示定義服務匯流排合約基本介面。

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="step-3-implement-a-rest-based-wcf-service-contract-to-use-service-bus"></a>步驟 3︰ 實作使用服務匯流排其餘型 WCF 服務合約

建立其他樣式服務匯流排服務要求您先建立合約時，請使用介面定義。 下一步是實作介面。 這包含建立名為**ImageService**實作使用者定義**IImageContract**介面的類別。 實作合約之後，您設定使用 App.config 檔案的介面。 設定檔案包含必要的資訊，請在應用程式，例如服務的名稱、 合約和通訊協定，用來與服務匯流排通訊類型的名稱。 在範例中的程序提供這些任務所用的程式碼。

使用先前的步驟，為差別極少實作其餘樣式合約和基本的服務匯流排合約。

### <a name="to-implement-a-rest-style-service-bus-contract"></a>若要執行的其他樣式服務匯流排合約

1. 建立新類別命名**ImageService**直接**IImageContract**介面的定義。 **ImageService**類別實作**IImageContract**介面。

    ```
    class ImageService : IImageContract
    {
    }
    ```
    與其他介面實作，您可以在不同的檔案中實作定義。 不過，在此教學課程，實作會出現在介面定義為相同的檔案和`Main()`方法。

2. 套用的[ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx)屬性以表示類別是 WCF 合約的實作**IImageService**類別。

    ```
    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    如先前所述，此命名空間不是傳統的命名空間。 不過，它是識別合約 WCF 架構的一部分。 如需詳細資訊，請參閱 WCF 文件中的[資料合約名稱](https://msdn.microsoft.com/library/ms731045.aspx)主題。

3. 新增.jpg 圖像至您的專案。  

    這是服務會顯示在接收的瀏覽器中的圖片。 以滑鼠右鍵按一下您的專案，然後按一下 [**新增**]。 然後按一下 [**現有的項目**。 若要瀏覽至適當的.jpg，使用 [**新增現有的項目**] 對話方塊，然後按一下 [**新增]**。

    新增時的檔案，請確認**所有檔案**中已都選取的下拉式清單旁**檔案名稱︰**欄位。 本教學課程的其餘部分假設圖像的名稱為 「 image.jpg 」。 如果您有不同的檔案，您必須重新命名圖像，或變更您的程式碼賠償。

4. 若要確保執行服務可以找到圖像檔案，在**方案總管**中的圖像檔案，以滑鼠右鍵按一下，然後按一下 [**屬性**。 在 [**屬性**] 窗格中，設定**複製到輸出目錄****如果較新**的複本。

5. 新增**System.Drawing.dll**組件的參考至專案，以及加入下列相關聯`using`陳述式。  

    ```
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

6. 在**ImageService**類別中，新增下列建構函式的載入點陣圖，並備妥用戶端瀏覽器中加以傳送。

    ```
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

7. 直接後的前一個的程式碼，請將下列**GetImage**方法加入**ImageService**類別，傳回包含圖像的 HTTP 郵件中。

    ```
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);

        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

        return stream;
    }
    ```

    這個實作使用**/ / 將**擷取圖像，並備妥以供串流至瀏覽器。 它會啟動資料流位置的零，宣告串流內容為 jpeg，並會傳送資訊。

8. 從 [**建置**] 功能表中，按一下 [**建立的解決方案**。

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>若要定義服務匯流排上執行的 web 服務的設定

1. 在**方案總管**] 中，按兩下 [ **App.config** Visual Studio 編輯器中開啟]。

    **App.config**檔案類似 WCF 設定檔，並包含服務名稱、 結束點 （也就是位置服務匯流排公開的用戶端，並裝載彼此） 並繫結 （用來進行通訊的通訊協定類型）。 主要差異是設定的服務端點是指[WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx)繫結的不是.NET Framework 的一部分。

2. `<system.serviceModel>` XML 項目是定義一個或多個服務的 WCF 項目。 在這裡，將其用於定義 [服務名稱] 和 [結束點。 底部`<system.serviceModel>`項目 (但仍中`<system.serviceModel>`)，新增`<bindings>`有下列內容項目。 定義使用應用程式中的繫結。 您可以定義多個連結，但在本教學課程定義只有一個。

    ```
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    此步驟中定義服務匯流排[WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx)繫結與**relayClientAuthenticationType**設定為 [**無]**。 此設定會指出使用此繫結端點不需要用戶端認證。

3. 之後`<bindings>`項目，請新增`<services>`項目。 類似於繫結，您可以在單一的設定檔中定義多個服務。 不過，在此教學課程中，您定義只有一個。

    ```
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    此步驟設定使用預先定義的預設**webHttpRelayBinding**服務。 也會使用預設**sbTokenProvider**，定義在下一個步驟中。

4. 之後`<services>`項目，請建立`<behaviors>`下列內容，以您先前取得從[Azure 入口網站][]的*共用 Access 簽章*(SA) 金鑰取代 「 SAS_KEY 」 的項目。

    ```
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

5. 在 App.config，還是在`<appSettings>`項目、 整個連線之前取得從入口網站中的連接字串與字串值取代。 

    ```
    <appSettings>
    <!-- Service Bus specific app settings for messaging connections -->
    <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

6. 從 [**建置**] 功能表中，按一下 [**建立的解決方案**建置整個方案]。

### <a name="example"></a>範例

下列程式碼會顯示在上執行服務匯流排使用**WebHttpRelayBinding**繫結的其餘服務合約和服務實作。

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

下列範例顯示與服務相關的 App.config 檔案。

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="[SAS_KEY]" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app setings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
</configuration>
```

## <a name="step-4-host-the-rest-based-wcf-service-to-use-service-bus"></a>步驟 4︰ 主控其餘型 WCF 服務，才能使用服務匯流排

此步驟會說明如何執行服務匯流排上使用主控台應用程式的 web 服務。 在此步驟中撰寫的程式碼的完整清單提供的程序的範例。

### <a name="to-create-a-base-address-for-the-service"></a>若要建立服務基底地址

1. 在 [`Main()`函數宣告，請建立一個變數來儲存您的服務匯流排專案的命名空間。 請務必將`yourNamespace`服務命名空間您先前建立的名稱。

    ```
    string serviceNamespace = "yourNamespace";
    ```
    服務匯流排會用來建立唯一的 URI 命名空間的名稱。

2. 建立`Uri`基底地址服務為基礎的命名空間的執行個體。

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>建立及設定 web 服務主機

- 建立 web 服務主機，使用此區段中先前建立的 URI 地址。

    ```
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    WCF 物件會產生主應用程式服務主機。 此範例中傳送的主機您想要建立 ( **ImageService**)，以及您要的方式公開主應用程式的地址的類型。

### <a name="to-run-the-web-service-host"></a>若要執行的 web 服務主機

1. 開啟的服務。

    ```
    host.Open();
    ```
    立即執行服務。

2. 顯示訊息，指出服務會執行，以及如何停止服務。

    ```
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

3. 完成後，請關閉 [服務主機。

    ```
    host.Close();
    ```

## <a name="example"></a>範例

下列範例會在教學課程中包含的服務合約和實作從先前的步驟，並裝載主控台應用程式中的服務。 編譯可執行檔名為 ImageListener.exe 下列程式碼。

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

### <a name="compiling-the-code"></a>編譯程式碼

建置方案之後，請執行下列命令以執行應用程式︰

1. 按**F5**，或瀏覽到可執行檔的位置 (ImageListener\bin\Debug\ImageListener.exe)，來執行服務。 讓應用程式執行，請執行下一個步驟是必要。

2. 複製並貼到瀏覽器] 以查看影像的命令提示字元中的地址。

3. 當您完成時，按下**enter 鍵**在命令提示字元視窗中關閉應用程式。

## <a name="next-steps"></a>後續步驟

現在，您建立的應用程式使用的服務匯流排轉送服務，請參閱下列文章，進一步瞭解轉送訊息︰

- [Azure 服務匯流排架構概觀](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#relays)

- [如何使用服務匯流排轉送服務](service-bus-dotnet-how-to-use-relay.md)

[Azure 入口網站]: https://portal.azure.com