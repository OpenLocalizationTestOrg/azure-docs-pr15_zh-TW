<properties
   pageTitle="使用 Elasticsearch 為服務布料的轉印圖樣應用程式追蹤儲存 |Microsoft Azure"
   description="說明如何服務布料的轉印圖樣應用程式使用 Elasticsearch 和 Kibana 來儲存、 index、 和搜尋應用程式追蹤 （記錄）"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/09/2016"
   ms.author="karolz@microsoft.com"/>

# <a name="use-elasticsearch-as-a-service-fabric-application-trace-store"></a>使用 Elasticsearch 服務布料的轉印圖樣應用程式追蹤與儲存
## <a name="introduction"></a>簡介
本文將說明如何[Azure 服務布料的轉印圖樣](https://azure.microsoft.com/documentation/services/service-fabric/)應用程式可以使用**Elasticsearch**和**Kibana**應用程式追蹤儲存空間、 索引編製功能，及搜尋。 [Elasticsearch](https://www.elastic.co/guide/index.html)是開啟來源、 分散式及調整即時搜尋和分析引擎，非常適合這項工作。 您可以在 Microsoft Azure 中執行的 Windows 和 Linux 虛擬機器上安裝。 Elasticsearch 可以有效率地處理所產生使用技術，例如**追蹤 Windows 事件 (ETW)**的*結構化*追蹤。

使用 ETW 服務布料的轉印圖樣執行階段來源診斷資訊 （追蹤）。 則建議的服務布料的轉印圖樣應用程式，以太來源他們的診斷資訊的方式。 使用相同的機制可以執行階段提供與應用程式所提供的追蹤與疑難排解更加容易讓之間的關聯。 在 Visual Studio 服務布料的轉印圖樣專案範本包含預設發出 ETW 追蹤記錄 API （根據.NET **EventSource**類別）。 使用 ETW 服務布料的轉印圖樣應用程式追蹤的概觀，請參閱[監控和診斷在本機電腦開發安裝的服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)。

若要顯示在 Elasticsearch 追蹤，他們必須 （執行應用程式） 時，在服務布料的轉印圖樣叢集節點即時擷取及傳送至 Elasticsearch 端點。 有兩種主要的搜尋選項，以追蹤擷取︰

+ **在 [程序追蹤擷取**  
應用程式或更精確地服務程序，負責傳送診斷資料以追蹤儲存區 (Elasticsearch)。

+ **出程序追蹤擷取**  
個別的代理程式會擷取服務程序或處理程序的追蹤，並傳送至追蹤存放區。

以下，我們會說明如何設定在 Azure Elasticsearch、 討論專業人員和兩個缺點擷取選項]，並說明如何設定服務布料的轉印圖樣服務傳送資料至 Elasticsearch。


## <a name="set-up-elasticsearch-on-azure"></a>設定上 Azure Elasticsearch
Azure Elasticsearch 服務所設定的最簡單方法是透過[**Azure 資源管理員範本**](../azure-resource-manager/resource-group-overview.md)。 您也可以從 Azure 快速入門範本存放庫內使用全面涵蓋所有內容的[快速入門 Azure 資源管理員 Elasticsearch 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch)。 此範本會使用不同的儲存帳戶的刻度] 單位 （節點的群組）。 它也可以提供另一個用戶端和伺服器節點具有不同資料磁碟連接的各種不同的數字。

在這裡，我們會使用另一個範本，稱為**ES MultiNode**從[Azure 診斷工具存放庫](https://github.com/Azure/azure-diagnostics-tools)。 此範本是更容易使用，然後建立受到 HTTP 基本驗證的 Elasticsearch 叢集。 在繼續之前，下載存放庫 GitHub 從您的電腦 （依 [請將存放庫或下載 zip 檔案）。 ES MultiNode 範本位於同名稱資料夾。

### <a name="prepare-a-machine-to-run-elasticsearch-installation-scripts"></a>準備電腦以執行 Elasticsearch 安裝指令碼
若要使用 ES MultiNode 範本最簡單的方法是透過提供的 PowerShell 的 Azure 指令碼，稱為`CreateElasticSearchCluster`。 若要使用這個指令碼，您必須安裝 PowerShell 模組和稱為**openssl**的工具。 第二個所需的建立可從遠端管理 Elasticsearch 叢集 SSH 機碼。

`CreateElasticSearchCluster`指令碼，從 Windows 電腦 ES MultiNode 範本搭配使用，以便於在設計。 可以在非 Windows 電腦上使用範本，但這種情況下超出本文的範圍。

1. 如果您還沒有已安裝這些，安裝[**Azure PowerShell 模組**](http://aka.ms/webpi-azps)。 出現提示時，請按一下 [**執行**，然後 [**安裝**]。 需要 azure PowerShell 1.3 或更新版本。

2. **Openssl**工具會包含在的[**Windows 版給**](http://www.git-scm.com/downloads)通訊群組。 如果您尚未執行此動作會立即安裝[Windows 版給](http://www.git-scm.com/downloads)。 （預設的安裝選項是 [確定]）。

3. 假設給已安裝，但不是包含系統路徑中，開啟 Microsoft Azure PowerShell 視窗，然後執行下列命令︰

    ```powershell
    $ENV:PATH += ";<Git installation folder>\usr\bin"
    $ENV:OPENSSL_CONF = "<Git installation folder>\usr\ssl\openssl.cnf"
    ```

    取代`<Git installation folder>`給位置，在您的電腦。預設值為**「 C:\Program Files\Git 」**。 請注意分號字元開頭的第一個路徑。

4. 請確定您已經登入 Azure (透過[`Add-AzureRmAccount`](https://msdn.microsoft.com/library/mt619267.aspx)cmdlet)，而且您選取的應該可以用來建立彈性的搜尋叢集的訂閱。 您可以驗證正確的訂閱已使用`Get-AzureRmContext`和`Get-AzureRmSubscription`cmdlet。

5. 如果您尚未這麼做，請 ES MultiNode 資料夾變更目前的目錄。

### <a name="run-the-createelasticsearchcluster-script"></a>執行 CreateElasticSearchCluster 指令碼
執行指令碼之前，開啟`azuredeploy-parameters.json`檔案，並確認或提供的指令碼參數值。 提供下列參數︰

|參數名稱           |描述|
|-----------------------  |--------------------------|
|dnsNameForLoadBalancerIP |用來建立的彈性搜尋叢集公開可見 DNS 名稱 （藉由將 Azure 地區網域提供的名稱） 的名稱。 例如，如果此參數值是 「 myBigCluster 」，且所選的 Azure 區域西美國，產生 DNS 名稱叢集是 myBigCluster.westus.cloudapp.azure.com。 <br /><br />彈性的搜尋叢集，例如資料節點名稱相關聯的許多成品的根名稱也是這個名稱。|
|adminUsername           |管理彈性搜尋叢集的系統管理員帳戶的名稱 （自動產生對應 SSH 鍵）。|
|dataNodeCount           |彈性的搜尋叢集節點的數字。 目前版本的指令碼不會區分資料] 與 [查詢節點。所有的節點播放這兩種角色。 預設為 3 的節點。|
|dataDiskSize            |每個資料節點已配置資料磁碟 （在 GB) 的大小。 每個節點接收 4 資料磁碟，以獨佔模式專用的彈性搜尋服務。|
|區域                  |Azure 區域應該位於彈性搜尋叢集的名稱。|
|esUserName              |使用者已有權存取 ES 叢集 （愛爾蘭 HTTP 基本驗證） 的使用者名稱。 密碼不是參數檔案的一部分，而且必須提供何時`CreateElasticSearchCluster`指令碼叫用。|
|vmSizeDataNodes         |彈性的搜尋叢集節點 Azure 虛擬機器大小。 Standard_D2 預設值。|

現在您已經準備好執行指令碼。 請輸入以下命令︰

```powershell
CreateElasticSearchCluster -ResourceGroupName <es-group-name> -Region <azure-region> -EsPassword <es-password>
```

位置 

|指令碼參數名稱    |描述|
|-----------------------  |--------------------------|
|`<es-group-name>`        |Azure 資源群組會包含所有的彈性搜尋叢集資源的名稱。|
|`<azure-region>`         |彈性的搜尋叢集應該會建立 Azure 區域的名稱。|         
|`<es-password>`          |彈性的搜尋使用者的密碼。|

>[AZURE.NOTE] 如果您收到 NullReferenceException 從測試 AzureResourceGroup 指令程式時，您忘記登入 Azure (`Add-AzureRmAccount`)。

如果您執行這個指令碼收到錯誤，您判斷錯誤因錯誤範本參數值修正參數檔案，然後再次執行指令碼，以不同的資源群組名稱。 您也可以重複使用相同資源的群組名稱，並有清理舊的項目，藉由新增的指令碼`-RemoveExistingResourceGroup`指令碼引動參數。

### <a name="result-of-running-the-createelasticsearchcluster-script"></a>執行 CreateElasticSearchCluster 指令碼的結果
執行之後`CreateElasticSearchCluster`指令碼，將會建立下列主要成品。 此範例中，我們假設您使用 「 myBigCluster 」 的值為`dnsNameForLoadBalancerIP`參數，而且您用來建立叢集區域西美國。

|成品|名稱、 位置及註解|
|----------------------------------|----------------------------------|
|遠端管理 SSH 鍵 |myBigCluster.key 檔案 （已執行 CreateElasticSearchCluster 目錄）。 <br /><br />此重要的檔案可以用於連線至 [管理員] 節點並 （透過 [管理員] 節點） 叢集內的資料節點。|
|管理員節點                        |myBigCluster admin.westus.cloudapp.azure.com <br /><br />遠端 Elasticsearch 叢集管理-僅有一個允許外部 SSH 連線專用的 VM。 在所有 Elasticsearch 叢集節點，相同的虛擬網路上執行，但不會執行任何 Elasticsearch 服務。|
|資料節點                        |myBigCluster1... myBigCluster*N* <br /><br />執行 Elasticsearch 和 Kibana 服務的資料節點。 您可以連線至每個節點，SSH 透過但僅透過管理員節點。|
|Elasticsearch 叢集             |http://myBigCluster.westus.cloudapp.azure.com/es/ <br /><br />主要結束點 Elasticsearch 叢集 （請注意 /es 後置字元）。 保護基本 HTTP 驗證 （的認證指定的 esUserName/esPassword 參數的 ES MultiNode 範本）。 叢集也有基本叢集管理碼外掛程式安裝 (http://myBigCluster.westus.cloudapp.azure.com/es/_plugin/head)。|
|Kibana 服務                    |http://myBigCluster.westus.cloudapp.azure.com <br /><br />若要顯示資料的建立 Elasticsearch Kibana 服務已設定。 保護相同的驗證認證，作為叢集本身。|

## <a name="in-process-versus-out-of-process-trace-capturing"></a>處理中與出程序追蹤擷取
我們簡介中, 所提及兩種收集診斷資料的基本方式︰ 中的程序與出程序。 各有優缺點。

**在 [程序追蹤擷取**的優點包括︰

1. *輕鬆設定及部署*

    * 設定診斷資料收集只是此應用程式設定的部分。 您可以輕鬆永遠保留 」 同步 」 與其他的應用程式。

    * 每個應用程式或每個服務的設定不轉化。

    * 出程序追蹤擷取通常需要個別的部署與額外管理工作及錯誤的潛在來源的診斷代理程式的設定。 特定代理程式技術通常可讓只有一個的每一虛擬機器 （節點） 代理程式的執行個體。 這表示該設定的所有應用程式之間共用診斷設定的集合，該節點上執行的服務。

2. *彈性*

    * 應用程式可以傳送資料，它所需的地方，只要用戶端文件庫支援的目標的資料儲存系統。 可以新增新接收，視需要。

    * 可以執行複雜的擷取、 篩選及資料彙總規則。

    * 代理程式支援的資料接收通常會限制出程序追蹤擷取。 某些代理程式的可延伸。

3. *內部應用程式的資料和內容的存取權*

    * 內的應用程式或服務程序執行診斷子系統可以輕鬆地擴大關聯式資訊追蹤。

    * 在出程序的方法，必須透過部分的程序間的通訊機制，例如 Windows 事件追蹤代理程式傳送資料。 這個機制可能會限制其他的限制。

**出程序追蹤擷取**的優點包括︰

1. *監視應用程式，以及收集損毀的能力傾印*

    * 在 [程序追蹤擷取可能會失敗，如果應用程式無法啟動，或當機。 獨立的代理程式有更容易擷取重要的疑難排解資訊。<br /><br />

2. *到期日、 穩定性及經過驗證的效能*

    * 開發平台廠商 （例如 Microsoft Azure 診斷代理程式） 代理程式已嚴格測試和戰役強化主旨。

    * 與程序中追蹤擷取小心，以確保的活動的應用程式的程序傳送診斷資料不會影響應用程式的主要工作或介紹預存時間或效能問題。 獨立執行代理程式容易這些問題，而專為限制對系統的影響。

若要結合和受益這兩種方法可能是。 實際上，可能是許多應用程式的最佳解決方案。

在這裡，我們使用**Microsoft.Diagnostic.Listeners 文件庫**和程序中追蹤擷取傳送資料至 Elasticsearch 叢集服務布料的轉印圖樣應用程式。

## <a name="use-the-listeners-library-to-send-diagnostic-data-to-elasticsearch"></a>使用 [傳送診斷資料至 Elasticsearch 接聽文件庫
Microsoft.Diagnostic.Listeners 文件庫是 PartyCluster 範例服務布料的轉印圖樣應用程式的一部分。 若要使用它︰

1. 下載 GitHub [PartyCluster 範例](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster)。

2. 傳送資料至 Elasticsearch 應使用的應用程式的解決方案資料夾複製 PartyCluster 範例目錄 Microsoft.Diagnostics.Listeners 和 Microsoft.Diagnostics.Listeners.Fabric 專案 （整個資料夾）。

3. 開啟的目標方案，以滑鼠右鍵按一下方案總管] 中的 [方案] 節點並選擇 [**新增現有的專案**。 新增 Microsoft.Diagnostics.Listeners 專案至的方案。 重複相同的 Microsoft.Diagnostics.Listeners.Fabric 專案。

4. 從您的服務專案中加入兩個新增專案的專案參考。 （應該傳送資料至 Elasticsearch 每個服務應該參考 Microsoft.Diagnostics.EventListeners 和 Microsoft.Diagnostics.EventListeners.Fabric。）

    ![專案參照 Microsoft.Diagnostics.EventListeners 及 Microsoft.Diagnostics.EventListeners.Fabric 文件庫][1]

### <a name="service-fabric-general-availability-release-and-microsoftdiagnosticstracing-nuget-package"></a>服務布料的轉印圖樣一般可用性發行和 Microsoft.Diagnostics.Tracing Nuget 套件
應用程式與服務布料的轉印圖樣一般可用性版本 (2.0.135，發行 2016 年 3 月 31 日) 內建目標**.NET Framework 4.5.2**。 此版本是 Azure GA 版本時支援的.NET framework 的最新版本。 很抱歉，此版本的架構缺少 Microsoft.Diagnostics.Listeners 文件庫需要的某些 EventListener Api。 因為緊密結合 EventSource （布料的轉印圖樣應用程式中記錄 Api 的基礎元件） 和 EventListener，使用 Microsoft.Diagnostics.Listeners 文件庫的每個專案必須使用 EventSource 的替代實作。 此實作被提供**Microsoft.Diagnostics.Tracing Nuget 套件**，Microsoft 所設計。 套件包含在架構，所以無程式碼變更不需要參考命名空間變更以外的 EventSource 是完整回溯相容。

若要開始使用 Microsoft.Diagnostics.Tracing 實作 EventSource 類別，請遵循下列步驟傳送資料至 Elasticsearch 需要的每個服務專案︰

1. 以滑鼠右鍵按一下該服務的專案，然後選擇 [**管理 Nuget 套件**。

2. 切換至 nuget.org 套件來源 （如果尚未選取），然後搜尋 「**Microsoft.Diagnostics.Tracing**」。

3. 安裝`Microsoft.Diagnostics.Tracing.EventSource`套件 （和其相依性）。

4. 在您的服務專案中開啟的**ServiceEventSource.cs**或**ActorEventSource.cs**檔案並取代`using System.Diagnostics.Tracing`檔案的上方指示詞`using Microsoft.Diagnostics.Tracing`指示詞。

**.NET Framework 4.6**支援的 Microsoft Azure 之後，這些步驟將不會必要。

### <a name="elasticsearch-listener-instantiation-and-configuration"></a>Elasticsearch 接聽 [實例和設定
傳送診斷資料 Elasticsearch 到最後一個步驟是，建立的執行個體`ElasticSearchListener`並將它設定 Elasticsearch 連線的資料。 接聽自動擷取服務專案中定義的 EventSource 類別透過引發的所有事件。 它必須是有效的服務，存留期間，服務初始化程式碼中為您建立的最佳的位置。 以下是必要的變更後的狀態服務初始化程式碼無法外觀 (加入項目開始著手的註解中指出`****`):

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Runtime;

// **** Add the following directives
using Microsoft.Diagnostics.EventListeners;
using Microsoft.Diagnostics.EventListeners.Fabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>        
        private static void Main()
        {
            try
            {
                // **** Instantiate ElasticSearchListener
                var configProvider = new FabricConfigurationProvider("ElasticSearchEventListener");
                ElasticSearchListener esListener = null;
                if (configProvider.HasConfiguration)
                {
                    esListener = new ElasticSearchListener(configProvider);
                }

                // The ServiceManifest.XML file defines one or more service type names.
                // Registering a service maps a service type name to a .NET type.
                // When Service Fabric creates an instance of this service type,
                // an instance of the class is created in this host process.

                ServiceRuntime.RegisterServiceAsync("Stateless1Type", 
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                // Prevents this host process from terminating so services keep running.
                Thread.Sleep(Timeout.Infinite);

                // **** Ensure that the ElasticSearchListner instance is not garbage-collected prematurely
                GC.KeepAlive(esListener);
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e);
                throw;
            }
        }
    }
}
```

在個別] 區段中的服務設定檔案 (**PackageRoot\Config\Settings.xml**) 應 Elasticsearch 連線的資料。 節的名稱必須對應到值傳遞給`FabricConfigurationProvider`建構函式，例如︰

```xml
<Section Name="ElasticSearchEventListener">
  <Parameter Name="serviceUri" Value="http://myBigCluster.westus.cloudapp.azure.com/es/" />
  <Parameter Name="userName" Value="(ES user name)" />
  <Parameter Name="password" Value="(ES user password)" />
  <Parameter Name="indexNamePrefix" Value="myapp" />
</Section>
```
值的`serviceUri`，`userName`和`password`參數會對應到 Elasticsearch 叢集端點位址、 Elasticsearch 的使用者名稱和密碼，分別。 `indexNamePrefix`是的前置詞 Elasticsearch 索引。Microsoft.Diagnostics.Listeners 文件庫每天建立新的索引，其資料。

### <a name="verification"></a>驗證
這樣就可以 ！ 現在，隨時執行服務時，它會開始傳送 Elasticsearch 服務設定中指定的追蹤。 您可以驗證此開啟 Kibana UI 相關聯的目標 Elasticsearch 執行個體。 在我們的範例，網址是 http://myBigCluster.westus.cloudapp.azure.com/。 要使用的名稱前置詞索引的核取`ElasticSearchListener`確實已建立並填入資料執行個體。

![Kibana 顯示 PartyCluster 應用程式的事件][2]

## <a name="next-steps"></a>後續步驟
- [進一步瞭解診斷和監視服務布料的轉印圖樣服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

<!--Image references-->
[1]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/listener-lib-references.png
[2]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/kibana.png
