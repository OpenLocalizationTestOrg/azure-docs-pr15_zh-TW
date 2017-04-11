<properties
   pageTitle="開始使用服務布料的轉印圖樣可靠的動作項目 |Microsoft Azure"
   description="本教學課程中會引導您建立、 偵錯，及部署使用服務布料的轉印圖樣可靠的動作項目一個簡單的動作項目型服務的步驟。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="getting-started-with-reliable-actors"></a>開始使用可靠的動作項目

> [AZURE.SELECTOR]
- [C# Windows 上](service-fabric-reliable-actors-get-started.md)
- [Java linux](service-fabric-reliable-actors-get-started-java.md)

本文說明 Azure 服務布料的轉印圖樣可靠動作項目中的基本概念，，逐步引導您建立、 偵錯，並將簡單的可靠的動作項目應用程式 Visual Studio 中部署。

## <a name="installation-and-setup"></a>安裝和設定
您開始之前，請確定您已在您的電腦上設定服務布料的轉印圖樣的開發環境。
如果您需要進行設定，請參閱[如何設定的開發環境](service-fabric-get-started.md)的詳細的指示。

## <a name="basic-concepts"></a>基本概念
若要開始使用可靠的動作項目，您只需要瞭解幾個基本概念︰

 * **動作項目服務**。 可靠的動作項目被封裝在可靠的服務，可以部署服務布料的轉印圖樣基礎結構。 在指定的服務執行個體中啟動動作項目執行個體。
 
 * **動作項目登錄**。 為可靠的服務，可靠的動作項目服務需要註冊服務布料的轉印圖樣執行階段。 此外，必須註冊的動作項目執行階段動作項目類型。
 
 * **動作項目介面**。 動作項目介面用來定義的動作項目強公用介面。 在可靠的動作項目模型術語中，動作項目介面定義動作項目能夠瞭解的訊息與程序的類型。 動作項目介面使用其他的動作項目和用戶端應用程式 」 」 （非同步） 將訊息傳送至動作項目。 可靠的動作項目可以實作多個介面。
 
 * **ActorProxy 類別**。 用戶端應用程式會使用 ActorProxy 類別叫用的動作項目介面透過公開的方法。 ActorProxy 課程提供兩個重要功能︰
    * 名稱解析︰ 可在叢集 （尋找叢集其裝載的節點） 中找出動作項目。
    * 錯誤處理︰ 可以再試一次方法引動並重新解析，例如需要重新放置到另一個節點叢集動作項目失敗後的 [動作項目位置。

值得，是屬於動作項目介面下列規則︰

- 無法超載動作項目介面方法。
- 方法不得出的動作項目介面，ref 或選擇性參數。
- 不支援一般介面。

## <a name="create-a-new-project-in-visual-studio"></a>在 Visual Studio 中建立新專案
您已安裝的服務布料的轉印圖樣工具的 Visual Studio 之後，您可以建立新的專案類型。 新的專案類型是**新的專案**] 對話方塊的 [**雲端**] 類別底下。


![Visual Studio-新專案的服務布料的轉印圖樣工具][1]

在下一步] 對話方塊中，您可以選擇您想要建立的專案類型。

![服務布料的轉印圖樣專案範本][5]

HelloWorld 專案，讓我們來使用服務布料的轉印圖樣可靠的動作項目服務。

您建立的解決方案之後，您應該會看到下列結構︰

![服務布料的轉印圖樣專案結構][2]

## <a name="reliable-actors-basic-building-blocks"></a>可靠的動作項目基本建置組塊

一般可靠的動作項目解決方案是由三個專案所組成︰

* **應用程式專案 (MyActorApplication)**。 這是套件的所有服務放在一起以供部署的專案。 它所含的*ApplicationManifest.xml*和 PowerShell 指令碼管理應用程式。

* **介面專案 (MyActor.Interfaces)**。 這是包含動作項目介面定義的專案。 在 MyActor.Interfaces 專案中，您可以定義方案中的動作項目會使用的介面。 可以在任何專案中有任何的名稱，定義您的動作項目介面，不過介面定義的動作項目實作共用的動作項目合約和用戶端，呼叫動作項目，因此通常道理進行定義的動作項目實作不同，可以共用多個其他專案的組件中。

```csharp
public interface IMyActor : IActor
{
    Task<string> HelloWorld();
}
```

* **動作項目服務專案 (MyActor)**。 這是用來定義要主控動作項目服務布料的轉印圖樣服務的專案。 它所含的動作項目。 動作項目實作是衍生自基底類型的類別`Actor`及實作 MyActor.Interfaces 專案中所定義的介面。 動作項目類別也必須實作建構接受`ActorService`執行個體和`ActorId`，並將基底`Actor`類別。 如此一來建構函式相依性的插入的平台相依性。

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> HelloWorld()
    {
        return Task.FromResult("Hello world!");
    }
}
```

動作項目服務必須註冊與服務布料的轉印圖樣執行階段中服務類型。 若要執行您的動作項目執行個體的動作項目服務的順序，您的動作項目類型必須註冊動作項目服務。 `ActorRuntime`註冊方法執行這項工作的動作項目。

```csharp
internal static class Program
{
    private static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<MyActor>(
                (context, actorType) => new ActorService(context, actorType, () => new MyActor())).GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

如果您開始新的 Visual Studio 專案，您有一個動作項目定義在 Visual Studio 產生的程式碼中的預設會包含註冊。 如果您定義服務中的其他動作項目，您需要新增的動作項目註冊使用︰

```csharp
 ActorRuntime.RegisterActorAsync<MyOtherActor>();

```

> [AZURE.TIP] 服務布料的轉印圖樣動作項目執行階段會發出[事件及效能計數器相關動作項目方法](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters)部分。 很有幫助診斷和效能監視。


## <a name="debugging"></a>偵錯

Visual Studio 的服務布料的轉印圖樣工具支援偵錯時，在您的本機電腦上。 您可以按 F5 鍵，以啟動偵錯工作階段。 Visual Studio 組建 （如有需要） 套件。 同時也部署本機服務布料的轉印圖樣叢集上的應用程式，並將附加偵錯工具。

部署過程中，您可以看到**輸出**視窗中的進度。

![服務布料的轉印圖樣偵錯輸出視窗][3]


## <a name="next-steps"></a>後續步驟
 - [如何可靠的動作項目使用的服務布料的轉印圖樣平台](service-fabric-reliable-actors-platform.md)
 - [動作項目狀態管理](service-fabric-reliable-actors-state-management.md)
 - [動作項目生命週期與回收集合](service-fabric-reliable-actors-lifecycle.md)
 - [動作項目 API 參考文件](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [程式碼範例](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
