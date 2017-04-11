<properties
   pageTitle="可靠的服務通訊概觀 |Microsoft Azure"
   description="可靠的服務通訊模型，包括在服務開啟接聽、 解決端點，以及通訊服務之間的概觀。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="BharatNarasimman"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="how-to-use-the-reliable-services-communication-apis"></a>如何使用可靠的服務通訊 Api

Azure 服務布料的轉印圖樣做為平台是完全相關服務之間的通訊。 所有的通訊協定和堆疊是可接受，從 HTTP 至 UDP。 會服務提供者選擇服務應如何進行通訊。 可靠的服務應用程式架構提供堆疊內建的通訊，以及您可以用來建立您的自訂通訊元件的 Api。 

## <a name="set-up-service-communication"></a>設定服務的通訊

可靠的服務 API 使用簡單的介面服務通訊。 若要開啟服務端點，只要實作這個介面︰

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

您可以藉由傳回該服務為基礎的類別方法覆寫中新增您的通訊接聽程式實作。

無服務︰

```csharp
class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```

設定狀態服務︰

```csharp
class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

在這兩種情況下，您會傳回接聽的集合。 這個選項可讓您在多個端點，可能會使用不同的通訊協定使用多個接聽程式上接聽的服務。 例如，您可能需要 HTTP 接聽和個別 WebSocket 接聽程式。 每個接聽程式取得名稱，並產生的集合*名稱︰ 地址*組表示 JSON 物件當用戶端要求的磁碟分割或服務的執行個體聆聽地址。

無服務，覆寫會傳回 ServiceInstanceListeners 的集合。 ServiceInstanceListener 包含建立 ICommunicationListener 函數，並讓其名稱。 如需狀態服務，覆寫會傳回 ServiceReplicaListeners 的集合。 這是沒有狀態涵蓋，從位置略有不同，因為 ServiceReplicaListener 開啟 ICommunicationListener 次要複本上的選項。 不只可以服務中使用多個通訊接聽程式，但您也可以指定哪些接聽接受邀請次要複本，並哪些只接聽主要的複本。

例如，您可以讓僅在主要的複本、 採用 RPC 呼叫 ServiceRemotingListener 和採用讀的第二個、 自訂接聽要求次要複本上透過 HTTP:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [AZURE.NOTE] 在建立多個接聽服務時，**必須**每個接聽給予唯一的名稱。

最後，描述所需的服務[服務資訊清單](service-fabric-application-model.md)的端點上] 區段下的結束點。

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

通訊接聽可以存取端點資源配置給它從`CodePackageActivationContext`中`ServiceContext`。 接聽可以開始開啟時接聽要求。

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE] 結束點資源常見整個服務套件，而啟動服務套件時，他們會配置以服務布料的轉印圖樣。 多個服務複本裝載於相同 ServiceHost 可能共用相同的連接埠。 這表示通訊接聽應該支援連接埠共用。 執行此動作的建議的方式是通訊接聽程式時要使用的磁碟分割識別碼和複本/執行個體識別碼就會產生接聽地址。

### <a name="service-address-registration"></a>服務的地址註冊

服務布料的轉印圖樣叢集上執行，稱為*命名服務*的系統服務。 命名服務是服務的服務和使用者接聽每個執行個體或複本的地址的註冊機構。 當`OpenAsync`的方法`ICommunicationListener`完成時，其傳回的值會註冊命名服務。 這個命名服務取得已發佈的傳回值是其值可任何項目，所有的字串。 此字串值是什麼用戶端時就會看到他們從命名服務要求服務的地址。

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);
                        
    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
            
    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));
    
    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```

服務架構提供 Api，讓服務名稱，然後要求至此地址的用戶端和其他服務。 這是很重要，因為服務地址不是靜態。 服務用於資源平衡與可用性叢集內移動。 這是可以讓解決服務的聆聽位址的用戶端機制。

> [AZURE.NOTE] 這逐步解說如何撰寫的完整的`ICommunicationListener`，請參閱[使用 OWIN 自我裝載的服務布料的轉印圖樣 Web API 服務](service-fabric-reliable-services-communication-webapi.md)

## <a name="communicating-with-a-service"></a>通訊與服務
可靠的服務 API 提供下列撰寫與服務的用戶端的文件庫。

### <a name="service-endpoint-resolution"></a>服務端點解析度
通訊與服務的第一個步驟是解決的磁碟分割或您想要與服務的執行個體的端點位址。 `ServicePartitionResolver`公用程式類別是基本的基本有助於判斷在執行階段服務的端點的用戶端。 在服務布料的轉印圖樣術語中，決定服務的端點的程序被指*服務端點解析度*。

若要連線至服務叢集，內`ServicePartitionResolver`建立可使用預設設定。 這是建議的用法，大多數的情況下︰

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```

若要連線到不同的叢集，服務`ServicePartitionResolver`可以建立一組叢集閘道器結束點。 請注意，閘道器端點需連線至相同叢集只是以不同的結束點。 例如︰

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

或者，`ServicePartitionResolver`可以建立指定函數`FabricClient`內部使用︰ 
 
```csharp
public delegate FabricClient CreateFabricClientDelegate();
```

`FabricClient`是用來與服務布料的轉印圖樣叢集的各種管理運算叢集通訊的物件。 當您想要如何更多的控制，這是很有用`ServicePartitionResolver`叢集進行互動。 `FabricClient`執行的快取內部，並建立，通常昂貴，因此請務必要重複使用`FabricClient`盡可能的執行個體。 

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```

解決方法是然後用來擷取資料分割服務的服務資料分割或服務的地址。

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```

服務的地址可以輕鬆地使用來解決`ServicePartitionResolver`，但更多的工作，才能確保解決的地址可正確。 您的用戶端需要偵測是否嘗試連線失敗，因為暫時性的錯誤，並可以重試 （例如服務已移動或暫時無法使用），或永久性錯誤 （例如服務已遭刪除，或要求的資源不再存在）。 服務執行個體或複本可以移動從節點節點隨時多個原因。 透過解決服務地址`ServicePartitionResolver`可能過期，您的用戶端程式碼會嘗試連線的時間。 在此情況下再次用戶端必須重新解析地址。 提供前一個`ResolvedServicePartition`表示解析程式需要再試一次而不只是擷取快取的地址。

一般而言，用戶端程式碼需要無法搭配`ServicePartitionResolver`直接。 就會建立並傳送至通訊用戶端工廠可靠的服務 API 中。 工廠內部使用解析程式產生可以用來與服務的用戶端物件。

### <a name="communication-clients-and-factories"></a>通訊用戶端和工廠

通訊工廠文件庫實作容易正在重試連線解決的服務端點的一般錯誤處理重試圖樣。 工廠文件庫所提供的錯誤處理常式提供重試機制。

`ICommunicationClientFactory`定義所產生的用戶端，可以與服務布料的轉印圖樣服務通訊用戶端工廠實作基底介面。 CommunicationClientFactory 實作取決於通訊堆疊服務使用的服務布料的轉印圖樣用戶端要進行通訊。 可靠的服務 API 提供`CommunicationClientFactoryBase<TCommunicationClient>`。 這會提供基底實作`ICommunicationClientFactory`介面，並執行常見的所有通訊堆疊的工作。 (這些工作包括使用`ServicePartitionResolver`決定服務端點)。 用戶端通常實作抽象 CommunicationClientFactoryBase 類別處理的特定通訊堆疊邏輯。

只要通訊用戶端收到的位址，然後用來連線到服務。 用戶端可以使用它想要的任何通訊的協定。

```csharp
class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```

用戶端工廠的主要職責建立通訊用戶端。 不會保留持續的連線，例如 HTTP 用戶端的用戶端的工廠只需要建立，並傳回用戶端。 維護持續的連線，某些二進位通訊協定，例如其他通訊協定也應該驗證來決定需要重新建立連線工廠。  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```

最後，例外處理常式是 reponsible 決定時發生例外狀況時採取的動作。 例外狀況分類到**重試**和**非重試**。 

 - **非重試**只要取得重新傳回例外回撥來電者。 
 - 將**暫時性**和**非暫時性**進一步分類**Retriable**例外狀況。
  - 只要而不重新解決服務端點位址重試的就是**暫時性**的例外狀況。 這些會包含暫時性網路問題或服務錯誤回應以外，指出不存在服務端點位址。 
  - **非暫時性**例外是需要重新解決服務端點位址。 這些包括表示無法存取服務端點的例外狀況，指出服務會移到不同的節點。 

`TryHandleException`可讓您指定的例外狀況相關的決策。 如果它**不知道**什麼決定進行例外狀況的相關，它應該會傳回**false**。 如果該**怎麼知道**哪些決策，讓，它應該依此設定結果並傳回**true**。
 
```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
### <a name="putting-it-all-together"></a>全部整合
使用`ICommunicationClient`， `ICommunicationClientFactory`，及`IExceptionHandler`周圍通訊協定，內建`ServicePartitionClient`是一起自動換行，並提供錯誤處理和服務的磁碟分割地址解析度迴圈這些元件。

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```

## <a name="next-steps"></a>後續步驟
 - 請參閱[在 GitHUb 範例專案](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount)中的服務之間的 HTTP 通訊的範例。

 - [使用可靠的服務遠端遠端程序呼叫](service-fabric-reliable-services-communication-remoting.md)

 - [網路可靠的服務中使用 OWIN 的 API](service-fabric-reliable-services-communication-webapi.md)

 - [使用可靠 Services WCF 通訊](service-fabric-reliable-services-communication-wcf.md)
