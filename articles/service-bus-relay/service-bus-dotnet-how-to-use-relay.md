<properties
    pageTitle="如何使用.NET 服務匯流排轉送 |Microsoft Azure"
    description="瞭解如何使用 Azure 服務匯流排轉送服務連線裝載於不同位置的兩個應用程式。"
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
    ms.date="09/16/2016"
    ms.author="sethm"/>


# <a name="how-to-use-the-azure-service-bus-relay-service"></a>Azure 服務匯流排轉送服務使用方式

本文將說明如何使用服務匯流排轉送服務。 範例以 C# 撰寫及 Windows Communication Foundation (WCF) API 使用服務匯流排組件中所包含的副檔名。 如需有關服務匯流排轉接的詳細資訊，請參閱[轉送服務匯流排通訊](service-bus-relay-overview.md)概觀。

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-the-service-bus-relay"></a>什麼是服務匯流排轉送？

[服務匯流排*轉送*](service-bus-relay-overview.md)服務可讓您建立的 Azure 資料中心與您的內部部署企業版環境中執行混合式應用程式。 服務匯流排轉送，這可以讓您以安全地公開 Windows Communication Foundation (WCF) 服務，而不需開啟防火牆連線，或對公司網路結構要求侵入變更公用雲端，公司企業網路中的協助。

![轉送概念](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

服務匯流排轉送可讓您現有的企業版環境中裝載 WCF 服務。 您可以再代理人接聽內送的工作階段及服務匯流排服務 Azure 中執行這些 WCF 服務要求。 這可讓您以公開 Azure 中執行的應用程式碼或行動裝置的人力或外部網路合作夥伴環境這些服務。 服務匯流排可讓您安全地控制能夠存取精密的層級這些服務。 它提供公開應用程式功能與您現有的企業方案的資料，並利用這個雲端功能強大且安全的方法。

本文將示範如何建立使用 TCP 頻道繫結實作安全交談雙方公開的 WCF web 服務使用服務匯流排轉送。

## <a name="create-a-service-namespace"></a>建立服務命名空間

若要開始使用服務匯流排轉送 Azure 中，您必須先建立命名空間。 命名空間提供範圍容器來處理您的應用程式中的服務匯流排資源。

若要建立服務命名空間︰

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>取得服務匯流排 NuGet 套件

[服務匯流排 NuGet 套件](https://www.nuget.org/packages/WindowsAzure.ServiceBus)是最簡單的方法，取得服務匯流排 API，並設定您的應用程式與服務匯流排相依性的所有。 若要安裝 NuGet 套件您專案中，執行下列動作︰

1.  在方案總管中，以滑鼠右鍵按一下 [**參考資料**，然後按一下 [**管理 NuGet 套件**。
2.  搜尋 「 服務匯流排 」，並選取**Microsoft Azure 服務匯流排**項目。 按一下 [**安裝**] 以完成安裝，然後關閉 [動作] 對話方塊︰

    ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="use-service-bus-to-expose-and-consume-a-soap-web-service-with-tcp"></a>使用服務匯流排公開及取用 TCP SOAP web 服務

若要到消耗外部的現有 WCF SOAP web 服務的方式公開，您必須變更服務繫結和地址。 您可能需要變更您的設定檔，或它可能需要變更程式碼，您必須設定和設定 WCF 服務的方式。 請注意 WCF 可讓您在相同的服務，有多個網路端點，以便您可以同時新增外部存取服務匯流排端點時保留現有的內部結束點。

在此工作中，您會建立一個簡單的 WCF 服務，並為其新增的服務匯流排接聽。 此練習假設的 Visual Studio 中，，因此不會不逐步瞭解建立專案的所有詳細資料。 不過，它著重於程式碼。

在開始之前這些步驟，完成下列程序設定您的環境︰

1.  在 Visual Studio 中，建立包含兩個專案，用戶端] 和 [服務]，在方案主控台應用程式。
2.  新增 Microsoft Azure 服務匯流排 NuGet 封裝這兩個專案。 此套件會將您的專案的所有必要的組件參考。

### <a name="how-to-create-the-service"></a>如何建立服務

首先，建立服務本身。 任何 WCF 服務包含至少三個不同的部分︰

-   將說明哪些郵件交換及作業是叫用合約的定義。
-   實作前述合約。
-   主機裝載 WCF 服務，公開數個端點。

本節中的程式碼範例地址這些元件。

合約定義單一作業， `AddNumbers`，將兩個數字並傳回結果。 `IProblemSolverChannel`介面，可讓更輕鬆地管理 proxy 存留時間用戶端。 建立這類介面最佳作法是。 最好將合約定義放入另一個檔案，以便您可以從您 「 用戶端 」 和 「 服務 」 專案，參照該檔案，但您也可以將程式碼複製到兩個專案。

```
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

就地合約時，請使用實作是一般。

```
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### <a name="configure-a-service-host-programmatically"></a>以程式設計方式設定服務主機

合約和實作中的位置，您現在可以主控服務。 裝載[System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/azure/system.servicemodel.servicehost.aspx)物件，負責管理服務的執行個體內，就會發生和主控接聽訊息的結束點。 下列程式碼設定的服務與一般的本機端點和服務匯流排結束點，以描繪出並排內部和外部端點的外觀。 使用您的命名空間名稱和*yourKey*取代 SA 索引鍵取得在先前的設定步驟的字串*命名空間*。

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "<yourKey>")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

在範例中，您可以建立相同的合約實作中的兩個端點。 其中一個是本機，一個透過服務匯流排規劃。 主要差異是繫結;[NetTcpBinding](https://msdn.microsoft.com/library/azure/system.servicemodel.nettcpbinding.aspx)本機和[NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx)服務匯流排端點和地址。 本機端點具有區域網路地址使用不同的連接埠。 服務匯流排端點具有組成的字串的結束點地址`sb`，您的命名空間的名稱和路徑 「 規劃求解 」。 這會導致 URI `sb://[serviceNamespace].servicebus.windows.net/solver`，為服務匯流排 TCP 端點的識別服務端點使用完整的外部 DNS 名稱。 如果您將取代到版面配置區的程式碼`Main`函數的**服務**應用程式，則必須功能的服務。 若要以獨佔模式接聽服務匯流排服務，請移除本機端點宣告。

### <a name="configure-a-service-host-in-the-appconfig-file"></a>設定服務主機 App.config 檔案中

您也可以設定主應用程式使用 App.config 檔案。 在此情況下裝載程式碼服務會出現在下一個範例。

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

端點定義移到 App.config 檔案。 NuGet 套件已加到 App.config 檔案，也就是必要的設定的副檔名服務匯流排定義範圍。 下列範例中，相當於確切的上一個程式碼，它應該會出現正下方的**system.serviceModel**項目。 程式碼假設您的專案 C# 命名空間名稱為**服務**。
取代預留位置與您的服務匯流排服務命名空間 SA 鍵。

```
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://namespace.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

服務進行這些變更之後，啟動之前，一樣，但具有兩個即時端點︰ 一個本機和雲端中的一個聆聽。

### <a name="create-the-client"></a>建立用戶端

#### <a name="configure-a-client-programmatically"></a>以程式設計方式設定用戶端

若要使用的服務，您可以建立使用[ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx)物件 WCF 用戶端。 服務匯流排使用使用 SA 實作權杖為基礎的安全性模型。 [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx)類別代表安全性權杖提供者的內建的工廠傳回一些已知的權杖提供者的方法。 下列範例會使用[CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx)方法來處理適當 SA 權杖擷取。 [名稱] 和 [索引鍵是前一節所述，從入口網站取得。

首先，參照或複製`IProblemSolver`合約到您的用戶端專案的程式碼服務。

然後，取代中的程式碼`Main`用戶端，一次與您的服務匯流排命名空間 SA 鍵取代預留位置文字的方法。

```
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","<yourKey>") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

您現在可以建立用戶端和服務，進行 （第一次執行服務），然後在用戶端呼叫服務及列印**9**。 您可以在不同的電腦上執行的用戶端和伺服器，甚或網路，及通訊仍能運作。 在雲端中或本機，也可以執行的用戶端程式碼。

#### <a name="configure-a-client-in-the-appconfig-file"></a>設定 App.config 檔案中的用戶端

下列程式碼會顯示如何設定使用 App.config 檔案的用戶端。

```
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

端點定義移到 App.config 檔案。 下列範例中，這是先前所列的程式碼相同，應該會出現正下方的**system.serviceModel**項目。 在這裡，為之前，您必須取代預留位置與您的服務匯流排命名空間 SA 鍵。

```
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://namespace.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## <a name="next-steps"></a>後續步驟

現在，您學到的服務匯流排轉送服務的基本概念，請遵循這些連結，瞭解更多。

- [服務匯流排轉送訊息的概觀](service-bus-relay-overview.md)
- [Azure 服務匯流排架構概觀](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- 從[Azure 範例][]下載服務匯流排範例，或查看[服務匯流排範例的概觀][]。

  [Shared Access Signature Authentication with Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
  [Azure 範例]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [服務匯流排範例的概觀]: ../service-bus-messaging/service-bus-samples.md