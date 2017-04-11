<properties
   pageTitle="建立 web 前端使用 ASP.NET 核心應用程式 |Microsoft Azure"
   description="使用 ASP.NET 核心 Web API 專案和透過 ServiceProxy 間服務通訊的方式公開至 web 應用程式服務布料的轉印圖樣。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/11/2016"
   ms.author="seanmck"/>


# <a name="build-a-web-service-front-end-for-your-application-using-aspnet-core"></a>建立 web 服務前端使用 ASP.NET 核心應用程式

根據預設，Azure 服務布料的轉印圖樣服務請勿提供公用網路介面。 若要公開 HTTP 用戶端應用程式的功能，您需要建立 web 專案作為進入點，並從該處個別服務然後進行通訊。

在本教學課程中，我們會拿離開的步驟[建立您的第一個應用程式在 Visual Studio 中](service-fabric-create-your-first-application-in-visual-studio.md)教學課程中，並新增 web 服務的狀態計數器服務前面。 如果您有不這麼做，請返回並逐步教學課程中的第一次。

## <a name="add-an-aspnet-core-service-to-your-application"></a>新增 ASP.NET 核心服務應用程式

ASP.NET 核心是輕量型、 跨平台 web 開發架構，您可以用來建立新 web UI 網頁 Api。 現在就讓我們 ASP.NET Web API 專案加入我們現有的應用程式。

>[AZURE.NOTE] 若要完成此教學課程中，您需要[安裝.NET 核心 1.0][dotnetcore-install]。

1. 在方案總管中，以滑鼠右鍵按一下應用程式專案中的**服務**，然後選擇 [**新增 > 新的服務布料的轉印圖樣**。

    ![將新的服務新增至現有的應用程式][vs-add-new-service]

2. 在 [**建立服務**] 頁面上選擇**ASP.NET 核心**，並為它命名。

    ![在 [新增服務] 對話方塊中選擇 ASP.NET web 服務][vs-new-service-dialog]

3. [下一步] 頁面會提供一組 ASP.NET 核心專案範本。 請注意，這些是是否您建立的外部服務布料的轉印圖樣應用程式的 ASP.NET 核心專案，您會看到的同一個範本。 在此教學課程中，我們會選擇**Web API**。 不過，您可以套用相同的概念，建立一個完整的 web 應用程式。

    ![選擇 ASP.NET 專案類型][vs-new-aspnet-project-dialog]

    Web API 專案建立後，您會有兩種服務應用程式中。 繼續建立您的應用程式時，您會以完全相同的方式來新增更多服務。 每個可獨立版本與升級。

>[AZURE.TIP] 若要進一步瞭解建立 ASP.NET 核心服務，請參閱[ASP.NET 核心文件](https://docs.asp.net)。

## <a name="run-the-application"></a>執行應用程式

若要具體的我們已完成的工作，現在就讓我們部署新的應用程式，看看 ASP.NET 核心 Web API 範本可提供的預設行為。

1. 在 Visual Studio 中偵錯應用程式，請按 F5。

2. 部署完成時，Visual Studio 便會啟動瀏覽器，ASP.NET Web API 服務，就像 http://localhost:33003 的根目錄。 連接埠號碼隨機指派，可能會在您的電腦上。 ASP.NET 核心 Web API 範本不的根目錄，提供預設行為，讓您將會在瀏覽器中看到的錯誤。

3. 新增`/api/values`瀏覽器中的位置。 這將會啟動`Get`上在 Web API 範本 ValuesController 方法。 它會傳回預設回應所提供的範本，包含兩個字串的 JSON 陣列︰

    ![傳回從 ASP.NET 核心 Web API 範本的預設值][browser-aspnet-template-values]

    教學課程結束時，我們會有取代這些預設值的最新的計數器值我們狀態的服務。


## <a name="connect-the-services"></a>將服務連線

服務架構提供完整的彈性，在您與可靠的服務的通訊。 在單一的應用程式，您可能需要透過 TCP、 可透過 HTTP REST API，其他服務與其他服務的無障礙 web 通訊端透過仍可存取的服務。 可用的選項和必須做的取捨相關上的背景，請參閱[通信與服務](service-fabric-connect-and-communicate-with-services.md)。 我們將在本教學課程中，執行下列其中一個簡單的方法，並使用`ServiceProxy` / `ServiceRemotingListener` SDK 中所提供的類別。

在 [ `ServiceProxy` （建立模型的遠端程序呼叫或 Rpc） 的方式，定義作為公用合約介面。 然後，您可以使用該介面產生的互動服務 proxy 類別。


### <a name="create-the-interface"></a>建立介面

我們會開始建立作為狀態服務和用戶端，包括 ASP.NET 核心專案之間的合約介面。

1. 在方案總管中，以滑鼠右鍵按一下您的方案，然後選擇 [**新增** > **新的專案**。

2. 在左側的功能窗格中選擇**Visual C#**項目，然後選取 [**類別文件庫**範本。 確定.NET Framework 版本設定為**4.5.2**。

    ![建立您的狀態服務介面專案][vs-add-class-library-project]

3. 使用介面順序`ServiceProxy`，它必須衍生自 IService 介面。 這個介面會包含在其中一個服務布料的轉印圖樣 NuGet 套件。 若要新增套件，以滑鼠右鍵按一下新的類別文件庫專案，然後選擇 [**管理 NuGet 套件**。

4. 搜尋**Microsoft.ServiceFabric.Services**套件，並將其安裝。

    ![新增服務 NuGet 套件][vs-services-nuget-package]

5. 在類別文件庫中，建立一個方法，介面`GetCountAsync`，並從 IService 擴充介面。

    ```c#
    namespace MyStatefulService.Interfaces
    {
        using Microsoft.ServiceFabric.Services.Remoting;

        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```


### <a name="implement-the-interface-in-your-stateful-service"></a>在您的狀態服務實作介面

現在，我們已定義的介面，我們需要狀態服務中實作。

1. 在您的狀態服務中新增類別庫專案包含介面的參考。

    ![類別庫專案參考加入狀態服務][vs-add-class-library-reference]

2. 找出繼承自類別`StatefulService`，例如`MyStatefulService`，及擴充實作`ICounter`介面。

    ```c#
    using MyStatefulService.Interfaces;

    ...

    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. 現在實作單一方法中定義的`ICounter`介面， `GetCountAsync`。

    ```c#
    public async Task<long> GetCountAsync()
    {
      var myDictionary =
        await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```


### <a name="expose-the-stateful-service-using-a-service-remoting-listener"></a>顯示狀態服務使用的服務遠端接聽

使用`ICounter`實作介面，啟用狀態，可以從其他服務呼叫服務的最後一個步驟是開啟的通訊通道。 如需狀態服務，服務布料的轉印圖樣提供可覆寫方法稱為`CreateServiceReplicaListeners`。 使用此方法，您可以指定一個或多個通訊接聽，根據您想要啟用您的服務的通訊類型。

>[AZURE.NOTE] 對等的方法為開啟狀態服務的通訊通道稱為`CreateServiceInstanceListeners`。

在此案例中，我們將會取代現有的`CreateServiceReplicaListeners`方法，並提供的執行個體`ServiceRemotingListener`，以建立 RPC 端點的是從用戶端透過呼叫`ServiceProxy`。  

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```


### <a name="use-the-serviceproxy-class-to-interact-with-the-service"></a>使用 ServiceProxy 類別互動服務

我們狀態服務現在可接收來自其他服務流量。 因此，仍會保留就新增 ASP.NET web 服務與程式碼。

1. 在 ASP.NET 專案中，新增 [類別文件庫包含參照`ICounter`介面。

2. 從 [**建置**] 功能表中，開啟 [**組態管理員**]。 您應該會看到像這樣︰

    ![設定為 AnyCPU 管理員顯示類別庫][vs-configuration-manager]

    請注意類別庫專案**MyStatefulService.Interface**，會設定為建置任何 CPU。 若要正確處理服務布料的轉印圖樣，它必須明確地指定目標 x64。 按一下平台下拉式清單，並選擇 [**新增**]，然後建立 x64 平台設定。

    ![建立新的類別文件庫的平台][vs-create-platform]

3. ASP.NET 專案中，新增 Microsoft.ServiceFabric.Services 套件，如同前述類別文件庫專案。 這會提供`ServiceProxy`類別。

4. 在 [**控制站**] 資料夾中，開啟`ValuesController`類別。 請注意，`Get`方法目前只會傳回 「 value1 」 和 「 value2]，符合我們之前在瀏覽器中看到的硬式編碼字串陣列。 此實作取代下列程式碼︰

    ```c#
    using MyStatefulService.Interfaces;
    using Microsoft.ServiceFabric.Services.Remoting.Client;

    ...

    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));

        long count = await counter.GetCountAsync();

        return new string[] { count.ToString() };
    }
    ```

    程式碼的第一行是索引鍵。 若要建立狀態服務 ICounter proxy]，您需要提供兩部分資訊︰ 分割識別碼] 和 [服務的名稱。

    您可以使用分割比例狀態服務，以不同的值區，將其狀態根據您所定義，例如 [客戶識別碼 」 或 「 郵遞區號索引鍵。 一般應用程式，狀態服務僅有一個磁碟分割，因此鍵沒有影響。 您所提供的任一按鍵將會導致相同的磁碟分割。 若要進一步瞭解分割服務，請參閱[如何分割服務布料的轉印圖樣可靠的服務](service-fabric-concepts-partitioning.md)。

    服務名稱是表單布料的轉印圖樣的 URI: /&lt;application_name&gt;/&lt;服務名稱&gt;。

    使用下列兩項資訊，服務布料的轉印圖樣可以唯一識別要求應該傳送到電腦。 `ServiceProxy`類別也完美控點的位置主控狀態服務磁碟分割之電腦失敗，另一部電腦必須升級來取代原來的大小寫。 此抽象可讓您撰寫用戶端程式碼，處理簡單了其他服務。

    一旦 proxy，我們只呼叫`GetCountAsync`方法，並傳回結果。

5. 按 F5 以執行修改過的應用程式。 為之前，Visual Studio 便會自動啟動專案的根目錄，網頁瀏覽器。 新增 「 api/值 」 路徑，您應該會看到目前計數器的值傳回。

    ![在瀏覽器中顯示的狀態計數器值][browser-aspnet-counter-value]

    重新整理瀏覽器定期，請參閱更新計數器值。


>[AZURE.WARNING] ASP.NET 核心 web 伺服器提供在範本中，稱為 Kestrel，是[目前不支援處理直接網際網路流量](https://docs.asp.net/en/latest/fundamentals/servers.html#kestrel)。 生產情況下，請考慮裝載 ASP.NET 核心端點前[API 管理][api-management-landing-page]或其他具網際網路閘道器。 請注意，以供部署 IIS 中不支援服務布料的轉印圖樣。


## <a name="what-about-actors"></a>動作項目呢？

新增 web 前端通訊與狀態服務著重於此教學課程。 不過，您可以遵循非常類似的模型，以與動作項目。 事實上，則較為簡單。

當您建立的動作項目專案時，Visual Studio 就會自動為您產生的介面專案。 您可以使用該介面產生的動作項目 proxy web 專案進行通訊的動作項目。 自動提供的通訊通道。 因此您不需要執行任何動作，就等於建立`ServiceRemotingListener`等您是否已在本教學課程狀態的服務。

## <a name="how-web-services-work-on-your-local-cluster"></a>在您的本機叢集 web 服務的運作方式

一般而言，您可以部署完全相同服務布料的轉印圖樣的應用程式至您部署在您的本機叢集多機器叢集，而是非常確定它就能如您預期的。 這是因為您的本機叢集只要摺疊成單一電腦的五個節點設定。

Web 服務時，然而，有一項主要的差異。 當叢集位於後面的負載平衡器，Azure 中一樣時，您必須確定 web 服務部署每個電腦上之後負載平衡器會直接循環流量跨電腦。 您可以藉由設定`InstanceCount`服務才能特殊 」-1] 的值。

相反地，當您在本機上執行 web 服務，您需要以確保只有一個執行個體時執行服務。 否則，您會遇到衝突從聆聽相同的路徑和連接埠的多個程序。 如此一來，web 服務的執行個體計數應該會設定為 「 1 」 本機部署。

若要瞭解如何設定不同的值，針對不同的環境，請參閱[管理多個環境的應用程式參數](service-fabric-manage-multiple-environment-app-configuration.md)。

## <a name="next-steps"></a>後續步驟

- [Azure 中建立叢集部署至雲端應用程式](service-fabric-cluster-creation-via-portal.md)
- [深入瞭解通訊與服務](service-fabric-connect-and-communicate-with-services.md)
- [深入瞭解分割狀態服務](service-fabric-concepts-partitioning.md)

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png
[vs-configuration-manager]: ./media/service-fabric-add-a-web-frontend/vs-configuration-manager.png
[vs-create-platform]: ./media/service-fabric-add-a-web-frontend/vs-create-platform.png


<!-- external links -->
[dotnetcore-install]: https://www.microsoft.com/net/core#windows
[api-management-landing-page]: https://azure.microsoft.com/en-us/services/api-management/
