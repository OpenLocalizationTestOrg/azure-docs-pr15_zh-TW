<properties
   pageTitle="可靠的服務 WCF 通訊堆疊 |Microsoft Azure"
   description="服務布料的轉印圖樣的內建 WCF 通訊堆疊提供用戶端服務 WCF 通訊可靠的服務。"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="07/26/2016"
   ms.author="bharatn"/>

# <a name="wcf-based-communication-stack-for-reliable-services"></a>WCF 為基礎的通訊堆疊可靠的服務
可靠的服務架構可讓服務作者選擇他們想要使用的服務通訊堆疊。 他們可以選擇透過**ICommunicationListener** [CreateServiceReplicaListeners 或 CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md)方法所傳回的通訊堆疊中插入。 架構提供的服務作者，想要使用 WCF 為基礎的通訊基礎 Windows Communication Foundation (WCF) 的通訊堆疊的實作。

## <a name="wcf-communication-listener"></a>WCF 通訊接聽程式
WCF 特定實作**ICommunicationListener**是由提供**Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener**類別。

點假設有類型的服務合約`ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

我們可以建立的 WCF 通訊接聽服務下列方式。

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## <a name="writing-clients-for-the-wcf-communication-stack"></a>撰寫 WCF 通訊堆疊的用戶端
撰寫用戶端可以使用 WCF 服務與通訊，架構提供**WcfClientCommunicationFactory**，也就是[ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md)WCF 特定實作。

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

WCF 通訊通道可以從建立**WcfCommunicationClientFactory** **WcfCommunicationClient**存取。

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

用戶端程式碼，可以使用**WcfCommunicationClientFactory**以及**WcfCommunicationClient**實作**ServicePartitionClient**來決定服務端點，以及與服務通訊。

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
>[AZURE.NOTE] 預設 ServicePartitionResolver 假設用戶端正在執行相同的叢集與服務。 如果是不的情況下，建立 ServicePartitionResolver 物件和傳入叢集連線結束點。

## <a name="next-steps"></a>後續步驟
* [使用可靠的服務遠端遠端程序呼叫](service-fabric-reliable-services-communication-remoting.md)

* [網路 OWIN 可靠的服務中使用的 API](service-fabric-reliable-services-communication-webapi.md)

* [安全通訊可靠的服務](service-fabric-reliable-services-secure-communication.md)
