<properties
   pageTitle="報告，並檢查與 Azure 服務布料的轉印圖樣健全狀況 |Microsoft Azure"
   description="瞭解如何從您的服務代碼傳送狀況報告，以及如何使用 Azure 服務架構提供的狀況監控工具來檢查您的服務健康狀況。"
   services="service-fabric"
   documentationCenter=".net"
   authors="toddabel"
   manager="mfussell"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/06/2016"
   ms.author="toddabel"/>

# <a name="report-and-check-service-health"></a>報表] 和 [核取的服務健康狀況
當您的服務發生問題時，回應並修正工作量及中斷的能力，取決於快速偵測問題的能力。 如果您從您的服務代碼，報告問題和失敗 Azure 服務布料的轉印圖樣狀況的管理員，您可以使用標準健康監視服務布料的轉印圖樣檢查的健康狀態所提供的工具。

有兩種方式，您可以在報告服務健康狀況︰

- 使用[磁碟分割](https://msdn.microsoft.com/library/system.fabric.istatefulservicepartition.aspx)或[CodePackageActivationContext](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.aspx)物件。  
您可以使用`Partition`和`CodePackageActivationContext`報告的狀況的項目目前的內容的一部分的物件。 例如執行的複本的程式碼可以在狀況報告僅在該複本與其所屬的磁碟分割的應用程式，則部分。

- 使用`FabricClient`。   
您可以使用`FabricClient`至報表從服務的程式碼叢集不[安全](service-fabric-cluster-security.md)，或按一下 [管理員] 權限是否正在執行服務的健康狀況。 將不會在大部分的真實案例中，則為 true。 使用`FabricClient`，您可以在任何屬於叢集的實體報告健康狀況。 理想的情況下，不過，服務代碼應只傳送至自己的健康狀況相關的報告。

本文會引導您範例報表從服務的程式碼的狀況。 範例也會顯示如何使用服務架構提供的工具，若要檢查的健康狀態。 本文被要健康監視服務布料的轉印圖樣的功能的快速簡介。 如需詳細資訊，您可以瞭解在您採取進階的相關文章狀況開頭與結尾的這篇文章連結的一系列。

## <a name="prerequisites"></a>必要條件
您必須安裝下列軟體︰

   * Visual Studio 2015
   * 服務 SDK 布料的轉印圖樣

## <a name="to-create-a-local-secure-dev-cluster"></a>若要建立本機安全開發叢集
- 使用管理員權限，開啟 PowerShell，並執行下列命令。

![顯示如何建立安全開發叢集的命令](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>部署應用程式，並檢查其健康狀況

1. 以管理員身分開啟 Visual Studio。

2. 建立專案使用的**服務狀態**範本。

    ![建立服務布料的轉印圖樣應用程式與服務狀態](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)

3. 按**F5**偵錯模式中執行應用程式。 將應用程式部署到本機叢集。

4. 執行應用程式之後，請以滑鼠右鍵按一下通知區域中的 [本機叢集管理員] 圖示，然後從快顯功能表開啟服務布料的轉印圖樣檔案總管] 中選取 [**管理本機叢集**。

    ![開啟 [通知區域中的 [服務布料的轉印圖樣總管](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)

5. 與此圖像顯示應用程式健康狀況。 此時，應用程式應該健全沒有發生錯誤。

    ![在 [服務布料的轉印圖樣總管健全應用程式](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)

6. 您也可以使用 PowerShell 檢查健全狀況。 您可以使用```Get-ServiceFabricApplicationHealth```檢查應用程式的狀況，而且您可以使用```Get-ServiceFabricServiceHealth```檢查服務的健康狀況。 在 PowerShell 中相同的應用程式的狀況報告位於此圖像。

    ![在 PowerShell 中健全的應用程式](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>若要將自訂的狀況事件新增至您的服務代碼
在 Visual Studio 服務布料的轉印圖樣專案範本包含範例程式碼。 下列步驟說明如何提自訂健康事件，以及在您的服務程式碼。 這類報表會自動顯示在標準工具健康監視的服務架構提供，例如服務布料的轉印圖樣總管 Azure 入口網站的健康狀況] 檢視中，與 PowerShell。

1. 重新開啟 Visual Studio 中，在先前建立的應用程式，或使用**服務狀態**Visual Studio 範本建立新的應用程式。

2. 開啟 [Stateful1.cs 的檔案，並尋找`myDictionary.TryGetValueAsync`撥入`RunAsync`方法。 您可以看到這個方法會傳回`result`的保留計數器的目前值，因為此應用程式中的關鍵邏輯執行計算。 如果這是真正的應用程式，而且缺少的結果表示失敗，您想要加上標幟該事件。

3. 若要報告時的結果表示失敗的狀況事件，請新增下列步驟。

    。 新增`System.Fabric.Health`Stateful1.cs 檔案命名空間。

    ```csharp
    using System.Fabric.Health;
    ```

    b。 新增下列程式碼之後`myDictionary.TryGetValueAsync`通話

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    我們會報告複本狀況，因為其報告的服務狀態。 `HealthInformation`參數儲存報告的狀況問題的相關資訊。

    如果您建立無狀態的服務，請使用下列程式碼

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```

4. 如果您的服務正在使用管理員權限，或如果不[安全](service-fabric-cluster-security.md)叢集，您也可以使用`FabricClient`至報表健康狀況如下所示。  

    。 建立`FabricClient`後執行個體`var myDictionary`宣告。

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    b。 新增下列程式碼之後`myDictionary.TryGetValueAsync`來電。

    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

5. 現在就讓我們模擬此錯誤，查看狀況監控工具] 中顯示。 若要模擬失敗，註解的狀況報告您先前新增的程式碼中的第一行。 您的註解的第一行後，程式碼會類似下列範例中。

    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
 將此程式碼會立即啟動此狀況報告每次`RunAsync`執行。 進行變更之後，請按**F5**執行應用程式。

6. 執行應用程式之後，開啟 [檢查應用程式的健康情況的服務布料的轉印圖樣總管]。 這次請服務布料的轉印圖樣總管會顯示應用程式是不佳。 這是因為已報告的程式碼我們先前新增的錯誤。

    ![服務布料的轉印圖樣檔案總管] 中的不良應用程式](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

7. 如果您選取的主要複本服務布料的轉印圖樣總管] 的樹狀檢視中時，您會看到**狀況**太指出錯誤。 服務布料的轉印圖樣總管也會顯示已新增至狀況報告詳細資料`HealthInformation`程式碼中的參數。 您可以看到 PowerShell 和 Azure 入口網站中相同的狀況報告。

    ![在 [服務布料的轉印圖樣總管複本狀況](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

此報表會保留在狀況管理員中，直到另一個報表或刪除此複本會被取代。 因為我們並未設定`TimeToLive`在此狀況報告`HealthInformation`物件、 永不過期報表。

我們建議您的狀況應該報告上最細層級，在本例中是複本。 您也可以回報狀況上`Partition`。

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

報表上的健康`Application`， `DeployedApplication`，及`DeployedServicePackage`，使用`CodePackageActivationContext`。

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>後續步驟
[服務布料的轉印圖樣健康深度剖析](service-fabric-health-introduction.md)
