<properties
   pageTitle="狀況監控服務布料的轉印圖樣 |Microsoft Azure"
   description="Azure 服務布料的轉印圖樣健康監視模型，提供監視叢集其應用程式與服務的簡介。"
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="oanapl"/>

# <a name="introduction-to-service-fabric-health-monitoring"></a>服務布料的轉印圖樣狀況監視簡介
Azure 服務布料的轉印圖樣介紹提供豐富、 彈性，且可延伸健康評估，和報告的狀況模型。 模型可讓附近即時監視叢集和中執行服務的狀態。 您可以輕鬆地取得健康狀態資訊，並修正可能會發生的問題串聯和會導致大量中斷之前。 一般的模型] 中，服務傳送根據其本機檢視的報表和資訊會彙總提供整體叢集層級檢視。

服務布料的轉印圖樣元件使用此模型中豐富的狀況報告其目前狀態。 您可以使用相同的機制至報表健康狀況，從您的應用程式。 如果您投資高品質的狀況報告擷取您自訂的條件，您可以偵測並更輕易地執行應用程式的修正問題。

> [AZURE.NOTE] 我們開始地址監視升級需要的狀況子系統。 服務架構提供監視應用程式和叢集升級，以確定完整的顯示狀態，沒有停機時間和最小到沒有使用者互動。 若要達成目標，升級會檢查狀況根據升級的原則，並可讓升級到繼續僅當狀況會採用您要的臨界值。 否則，升級會自動回復或暫停要讓系統管理員以修正問題的機率。 若要深入瞭解應用程式升級，請參閱[本文](service-fabric-application-upgrade.md)。

## <a name="health-store"></a>狀況存放區
狀況存放區就會保存的實體的狀況相關資訊以方便擷取和評估叢集。 實作為服務布料的轉印圖樣保存狀態的服務，以確保可用性能與延展性。 狀況市集屬於**布料的轉印圖樣: / 系統**應用程式，而且會提供叢集已啟動和執行。

## <a name="health-entities-and-hierarchy"></a>狀況實體和階層
狀況實體組織中擷取互動和不同的實體之間的相依性的邏輯階層。 根據收到來自服務布料的轉印圖樣元件的報表的狀況存放區會自動建立的實體和階層。

狀況實體對稱服務布料的轉印圖樣項目。 （，例如**狀況應用程式實體**比對時**狀況節點實體**符合服務布料的轉印圖樣叢集節點部署叢集，應用程式執行個體。）狀況階層擷取系統實體的互動而進階的健康評估的基礎。 您可以瞭解的[服務布料的轉印圖樣技術概觀](service-fabric-technical-overview.md)的重要服務布料的轉印圖樣概念。 如需應用程式的詳細資訊，請參閱[服務布料的轉印圖樣應用程式模型](service-fabric-application-model.md)。

狀況實體和階層允許叢集，並會報告、 偵錯及監視的應用程式。 健康模型提供，*微調*正確呈現實際桌子叢集許多移動片段的狀況。

![狀況的項目。][1]
狀況的實體，組織中父項與子項關聯的階層。

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

狀況實體是︰

- **叢集**。 代表服務布料的轉印圖樣叢集的狀況。 叢集狀況報告描述會影響整個叢集，而且無法縮小至一或多個不佳的子系的條件。 範例包括叢集分割因為網路分割或通訊問題的大腦。

- **節點**。 代表服務布料的轉印圖樣節點的狀況。 節點狀況報告描述會影響到節點的條件。 通常，它們會影響所有的部署項目上執行。 範例包括節點超出磁碟空間 （或另一個全機器屬性，例如連線的記憶體），節點已關閉。 節點實體識別節點名稱 (string)。

- **應用程式**。 代表在叢集執行應用程式執行個體的狀況。 應用程式狀況報告描述會影響應用程式的整體健全狀況的條件。 他們無法縮小向下到個別子系 （服務或應用程式部署）。 範例包括應用程式中的不同的服務的端對端互動。 應用程式實體識別應用程式的名稱 (URI)。

- **服務**。 代表在叢集中執行服務的健康狀況。 服務健康狀況報表描述條件會影響整體的健康情況的服務，以及他們無法縮小向下到磁碟分割或複本。 範例包括導致問題的所有磁碟分割區的服務設定 （例如連接埠或外部檔案共用）。 服務實體識別服務名稱 (URI)。

- **分割**。 代表服務磁碟分割的狀況。 分割狀況報告描述會影響整個複本集中的條件。 範例包括複本數目低於目標計數同時磁碟分割為仲裁遺失。 分割實體識別的磁碟分割識別碼 (GUID)。

- **複本**。 代表狀態服務複本或無服務的執行個體的狀況。 最小 watchdogs 的單位與系統元件可以報告應用程式。 如需狀態服務，範例包括主要複本，報告當它無法複製次要連結和複寫時不在預期進行的作業速度。 此外，不足資源或具有連線發生問題時，可以報告狀態的執行個體。 識別複本實體分割識別碼 (GUID) 和複本或執行個體識別碼 （時間）。

- **DeployedApplication**。 代表*節點上執行應用程式*的狀況。 部署的應用程式狀況報告說明應用程式特定條件無法越服務套件部署位於相同的節點的節點。 範例包括該節點，以及應用程式的安全性原則節點問題設定時，就無法下載應用程式套件。 部署的應用程式是以應用程式的名稱 (URI) 和識別節點名稱 (string)。

- **DeployedServicePackage**。 代表叢集節點上執行的服務封裝的狀況。 說明服務套件的特定的條件不會影響相同的節點的相同的應用程式上的其他服務套件。 範例包括程式碼套件中服務套件無法啟動，便無法讀取設定套件。 部署的服務套件識別應用程式的名稱 (URI)、 節點名稱 (string) 及服務資訊清單名稱 (string)。

健康模型的方式可讓您更容易偵測並修正問題。 例如，如果服務沒有回應、 並可行回報的應用程式執行個體是不佳。 報告在的層級並不理想，不過，因為問題可能不會影響該應用程式中的所有服務。 報表應該套用至不佳的服務或特定子磁碟分割，如果點的磁碟分割的詳細資訊。 資料會自動表面階層，並不佳的磁碟分割就會顯示在服務和應用程式層級。 此彙總協助找出並解決問題的根本原因更快速地。

狀況階層是由父項與子項關聯性。 由叢集節點和應用程式。 應用程式的服務，並部署應用程式。 部署的應用程式已部署服務套件。 服務有磁碟分割區，而且每個資料分割有一或多個複本。 有節點和部署的實體之間特殊的關聯。 如果節點是依其授權的系統元件 （容錯移轉管理員服務） 的報告不佳，它會影響部署的應用程式、 服務套件及部署在其上的複本。

狀況階層代表根據了最新的狀況報告，幾乎即時資訊系統的最新狀態。
內部和外部 watchdogs 可以在相同的實體根據特定應用程式的邏輯或自訂監視的狀況報告。 使用者報告共存系統報表。

規劃投資如何報表與回應期間的大型雲端服務，設計，讓您更容易偵錯、 監控及操作服務健康狀況。

## <a name="health-states"></a>健康狀態
服務結構描述實體是否狀況良好使用三種狀況狀態: [確定]，警告與錯誤。 傳送至市集] 狀況任何報表必須指定這些狀態之一。 狀況評估結果是這些狀態。

可能的[健康狀態](https://msdn.microsoft.com/library/azure/system.fabric.health.healthstate)是︰

- **OK**. 狀況良好的實體。 沒有報告或 （如果適用的話） 及其下層的已知的問題。

- **警告**。 實體體驗的一些問題，但尚未不健全 （例如，會發生延遲，但他們不會造成任何功能的問題尚未）。 在某些情況下，[警告] 條件可能可以修正不含任何特殊的互動，幫助以提供完善什麼事。 在其他情況下，可能會降低發生問題嚴重時不會使用者互動的警告條件。

- **錯誤**。 實體是不佳。 若要修正問題的狀態，在實體應該採取動作，因為其無法正常運作。

- **為未知**。 實體不存在市集中的狀況。 您可以從合併結果來自多個元件分散式查詢取得此結果。 例如，取得節點清單查詢移至**FailoverManager**和**HealthManager**，取得應用程式清單查詢移至**ClusterManager**和**HealthManager**。 這些查詢合併來自多個系統元件的結果。 如果其他系統元件傳回尚未到達或從狀況市集清理的實體，合併的結果將會有未知的狀況。

## <a name="health-policies"></a>狀況原則
狀況市集適用於狀況原則，以決定是否實體狀況良好根據其報表和子系。

> [AZURE.NOTE] 應用程式顯示 （適用於應用程式評估和任何子系） 或 （適用於叢集和節點健康評估） 叢集資訊清單中，您可以指定健康原則。 健康情況評估要求也可以在自訂健康評估原則，只適用於該評估傳遞。

根據預設，服務布料的轉印圖樣適用於嚴格的規則 （所有項目必須是健全） 的父子式階層的關聯性。 如果即使其中一個子系有一個不佳的事件，上層會被視為不佳。

### <a name="cluster-health-policy"></a>叢集健康原則
[叢集健康原則](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.aspx)用來評估叢集狀況和節點健康狀態。 原則可定義叢集資訊清單中。 如果您不存在，會使用預設的原則 （零 tolerated 失敗）。
包含叢集健康原則︰

- [ConsiderWarningAsError](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.considerwarningaserror.aspx)。 指定是否將警告狀況報告為錯誤狀況評估期間。 預設值︰ false。

- [MaxPercentUnhealthyApplications](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications.aspx)。 指定之前叢集會被視為錯誤可能不佳的應用程式的最大 tolerated 的百分比。

- [MaxPercentUnhealthyNodes](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes.aspx)。 指定叢集視為錯誤之前可能不佳的節點的最大 tolerated 的百分比。 在大型叢集，某些節點一律是向下或縮小的修復，因此這個百分比應設定為容許的。

- [ApplicationTypeHealthPolicyMap](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap.aspx)。 應用程式類型狀況原則地圖可以用於叢集健康評估，來描述特殊的應用程式類型。 根據預設，所有應用程式放入資料庫，並以 MaxPercentUnhealthyApplications 評估上。 如果應該以不同方式處理某些應用程式類型，即可取得登出全域的資料庫。 不過，會評估針對其對應中的應用程式類型名稱與相關聯的百分比。 例如，在叢集有數以千計的應用程式類型不同，，特殊的應用程式類型的幾個控制項應用程式執行個體。 控制應用程式，永遠不應該位於錯誤。 您可以指定全域 MaxPercentUnhealthyApplications 20%容許某些失敗次數，但是針對 「 ControlApplicationType 」 設定為 0 MaxPercentUnhealthyApplications 應用程式類型。 如此一來，如果許多應用程式的一些不佳，但想警告評估叢集全域不佳的百分比] 下方。 警告狀況不會影響叢集升級或其他監控觸發錯誤狀況。 但發生錯誤的一個控制項應用程式會使叢集不佳的觸發程序回復或暫停叢集升級，視升級設定而定。
在地圖中定義的應用程式類型，對於所有的應用程式執行個體取自登出全域的應用程式集區。 會評估根據應用程式類型，使用特定的 MaxPercentUnhealthyApplications 從對應的應用程式的總數。 所有其他的應用程式仍會全域的資料庫，並與 MaxPercentUnhealthyApplications 會進行評估。

下列範例是摘錄自叢集資訊清單。 若要在應用程式類型地圖中定義的項目，請在參數名稱前面加 「 ApplicationTypeMaxPercentUnhealthyApplications-」，後面接著應用程式類型的名稱。

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
    <Parameter Name="ApplicationTypeMaxPercentUnhealthyApplications-ControlApplicationType" Value="0" />
  </Section>
</FabricSettings>
```

### <a name="application-health-policy"></a>應用程式的健康原則
[應用程式的健康原則](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.aspx)說明應用程式與孩子的事件和子狀態彙總的評估已完成的工作。 它可以定義中的應用程式清單， **ApplicationManifest.xml**，應用程式套件。 如果未指定沒有原則，服務布料的轉印圖樣假設實體如果有狀況報表或子系在警告或錯誤狀況不佳。
可設定原則是︰

- [ConsiderWarningAsError](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.considerwarningaserror.aspx)。 指定是否將警告狀況報告為錯誤狀況評估期間。 預設值︰ false。

- [MaxPercentUnhealthyDeployedApplications](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications.aspx)。 指定部署之前應用程式會被視為錯誤可能不佳的應用程式的最大 tolerated 的百分比。 這個百分比的計算方式在應用程式目前部署叢集的節點數目除以不佳的部署應用程式的數目。 計算會以容許一個失敗小型的節點的數字無條件進位。 預設百分比︰ 零。

- [DefaultServiceTypeHealthPolicy](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy.aspx)。 指定預設的狀況原則應用程式中的所有服務類型] 會取代預設服務類型狀況原則。

- [ServiceTypeHealthPolicyMap](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap.aspx)。 提供服務健康狀況原則，每個服務類型的地圖。 這些原則會取代預設服務類型狀況原則，針對每個指定的服務類型。 比方說，如果應用程式沒有狀態的閘道器服務類型和狀態引擎服務類型，您可以不同設定其評估的狀況原則。 當您指定每個服務類型的原則時，您可以更細緻的控制權的服務健康狀況。

### <a name="service-type-health-policy"></a>服務健康狀況類型的原則
[服務類型健康原則](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.aspx)指定如何評估，和服務] 和 [服務的子系彙總。 包含原則︰

- [MaxPercentUnhealthyPartitionsPerService](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice.aspx)。 指定不佳的磁碟分割區的最大 tolerated 的百分比之前服務會被視為不佳。 預設百分比︰ 零。

- [MaxPercentUnhealthyReplicasPerPartition](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition.aspx)。 指定的最大 tolerated 的百分比不佳的複本的前一個磁碟分割會被視為不佳。 預設百分比︰ 零。

- [MaxPercentUnhealthyServices](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices.aspx)。 應用程式會被視為不健全之前，請指定不健全的服務的最大 tolerated 的百分比。 預設百分比︰ 零。

下列範例是摘錄自應用程式資訊︰

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## <a name="health-evaluation"></a>健康評估
使用者和自動化的服務可以隨時評估的任何實體的狀況。 若要評估的實體狀況，狀況市集彙總所有健康狀況實體上的報表和評估所有子系 （如果適用的話）。 狀況彙總演算法會使用狀況指定原則，評估狀況報告的方式，以及如何彙總子健康狀態 （如果適用的話）。

### <a name="health-report-aggregation"></a>狀況報表彙總
一個實體可以有多個其他屬性的不同正常值 （系統元件或 watchdogs） 所傳送的狀況報告。 彙總會使用相關聯的狀況原則，特別是應用程式或叢集健康原則的 ConsiderWarningAsError 成員。 ConsiderWarningAsError 指定如何評估警告。

*最糟*狀況報告實體上觸發彙總的狀況。 如果沒有至少有一個錯誤狀況報告，彙總的狀況是錯誤。

![使用錯誤報告狀況報表彙總。][2]

有一或多個錯誤狀況報告狀況實體會評估為錯誤。 相同的為 true 的過期的狀況報告，不論其狀況。

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

如果沒有錯誤報告及一或多個警告，警告或錯誤，根據 ConsiderWarningAsError 原則標幟，也會是彙總的狀況。

![警告報表與 ConsiderWarningAsError false 狀況報表彙總。][3]

警告報表與 ConsiderWarningAsError 狀況報表彙總設定為 false （預設）。

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>子狀況彙總
彙總的狀況的實體會反映出子健康狀態 （如果適用的話）。 彙總子健康狀態演算法會使用狀況原則適用的實體類型為根據。

![子實體狀況彙總。][4]

根據健康原則子系彙總。

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

狀況存放區已評估所有子系之後，則會彙總根據設定的最大百分比不佳的子系其健康狀態。 這個百分比是來自原則根據實體和子類型。

- 如果所有的子系確定狀態，子系彙總健康狀態是 [確定]。

- 如果子系確定] 和 [警告狀態，警告子系彙總狀況。

- 如果有不受影響的不佳的子系百分比的上限的錯誤狀態的字詞，請彙總的狀況會發生錯誤。

- 如果錯誤狀態子系尊重不佳的字詞的最大可容許的百分比，警告彙總的狀況。

## <a name="health-reporting"></a>狀況報告
系統元件、 系統布料的轉印圖樣應用程式，以及內部/外部 watchdogs 可以針對服務布料的轉印圖樣實體報告。 正常值進行*本機*決定的監視的項目，並根據他們所監視的條件的狀況。 不需要查看任何全域狀態或彙總資料。 想要的行為是簡單正常值，並不複雜生態需要查看許多推斷要傳送的資訊。

若要傳送的狀況存放區狀況資料，請報表工具必須找出受影響的實體並建立狀況報告。 透過使用[FabricClient.HealthClient.ReportHealth](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient_members.aspx)API、 PowerShell 或其他部分，然後會傳送報表。

### <a name="health-reports"></a>狀況報告
為每個叢集實體[狀況報告](https://msdn.microsoft.com/library/azure/system.fabric.health.healthreport.aspx)包含下列資訊︰

- **SourceId**。 唯一識別健康事件報表工具的字串。

- **實體識別碼**。 識別的實體套用報表的位置。 它會根據[實體輸入](service-fabric-health-introduction.md#health-entities-and-hierarchy)︰

  - 叢集。 無。

  - 節點。 節點名稱 (string)。

  - 應用程式。 應用程式名稱 (URI)。 代表在叢集部署的應用程式執行個體名稱。

  - 服務。 服務名稱 (URI)。 代表在叢集部署的服務執行個體名稱。

  - 分割。 分割識別碼 (GUID)。 代表磁碟分割的唯一識別碼。

  - 複本。 狀態服務複本 ID 或無服務執行個體識別碼 (INT64)。

  - DeployedApplication。 應用程式名稱 (URI)，以及節點名稱 (string)。

  - DeployedServicePackage。 應用程式的名稱 (URI)、 節點名稱 (string)，以及服務資訊清單名稱 (string)。

- **屬性**。 *字串*（不固定列舉），讓報表工具分類的實體的特定屬性的狀況事件。 例如，報表工具 A 可以報告 Node01 」 存放區 」 屬性和報表工具 B 的狀況報告 Node01 」 連線] 屬性的狀況。 市集中的狀況，這些報表會被視為 Node01 實體個別的健康事件。

- **描述**。 字串，可讓報表工具提供的健康事件的詳細的資訊。 **SourceId****屬性**，與**HealthState**應該完整描述報表。 描述將閱讀報表的相關資訊。 文字可讓您更輕鬆地系統管理員和使用者瞭解狀況報告。

- **HealthState**。 [列舉](service-fabric-health-introduction.md#health-states)描述報表的狀況。 接受的值是 [確定]，警告與錯誤。

- **TimeToLive**。 表示多久狀況報告是有效的時段。 搭配**RemoveWhenExpired**，讓知道如何評估過期的事件的狀況存放區。 根據預設的值無限，，永遠是有效的報表。

- **RemoveWhenExpired**。 布林值。 如果設為 true，過期的狀況報表會自動移除狀況 store]，與報告，不會影響實體健康評估。 報表是有效的時間，在指定期間內，而且報表工具不需要明確清除該時使用。 若要刪除的狀況存放區中的報表也使用 （例如，可會變更和停止傳送報告與先前的來源] 屬性監視）。 可以傳送有以及 RemoveWhenExpired 以清除任何先前的狀態，從狀況存放區的簡短 TimeToLive 的報告。 如果值設為 false，過期的報表就會被視為健康評估發生錯誤。 False 值表示狀況市集來源應會定期報告的內容。 如果不是，然後必須有錯誤與監視。 考慮為錯誤的事件會擷取監視的狀況。

- **SequenceNumber**。 需要不斷增加正整數，它會表示報表的順序。 狀況市集使用偵測過期接收到落後，因為網路延遲或其他問題的報表。 報表遭到拒絕時的順序數字小於或等於最最近套用相同的實體、 來源和屬性的數字。 如果未指定，就會自動產生的順序編號。 就必須報告狀態轉場效果時，才置於順序編號。 在此情況下，來源需要請記住，它所傳送的報表，並保留在容錯移轉復原的資訊。

這四項的資訊，SourceId、 實體識別碼、 屬性和 HealthState-所需的每個狀況報告。 開始使用不允許字串 SourceId 前置詞 「**系統**」，系統會保留的報表。 相同的實體有相同的來源和屬性只有一個報表。 相同的來源和屬性的多個報表覆寫彼此，狀況用戶端 （如果批次處理） 或狀況儲存側。 取代為基礎順序編號;較新的報表 （含較高的順序編號） 取代舊的報表。

### <a name="health-events"></a>健康事件
在內部，狀況市集保留[狀況事件](https://msdn.microsoft.com/library/azure/system.fabric.health.healthevent.aspx)，其中包含從報表和其他的中繼資料的所有資訊。 中繼資料包含報表所提供的狀況用戶端時間，以及修改伺服器端的時間。 [狀況查詢](service-fabric-view-entities-aggregated-health.md#health-queries)所傳回健康事件。

新增中繼資料包含︰

- **SourceUtcTimestamp**。 時間報表所提供的狀況用戶端 （國際標準時間）。

- **LastModifiedUtcTimestamp**。 伺服器端 （國際標準時間） 上次修改報表的時間。

- **IsExpired**。 旗標，指出狀況存放區來執行查詢時，是否已到期報表。 只有當 RemoveWhenExpired 為 false，則可以過期事件。 否則，事件不會傳回查詢，會從儲存區中移除。

- **LastOkTransitionAt**， **LastWarningTransitionAt**， **LastErrorTransitionAt**。 確定/警告錯誤轉場效果的最後一個時間。 這些欄位會提供狀況的歷程記錄事件的狀態轉場效果。

狀態轉場效果欄位可用於能夠事半功倍的通知或 「 歷程 」 健康事件資訊。 例如，他們啟用案例︰

- 當屬性已在警告/錯誤的多個 X 分鐘提醒。 檢查一段時間的條件，可避免暫時狀況的通知。 例如，如果狀況有超過五分鐘已警告的提醒可以成數 (HealthState = = 警告，並立即-LastWarningTransitionTime > 5 分鐘)。

- 僅在最後一次變更的條件通知 X 分鐘。 如果報表已在錯誤之前指定的時間，可以忽略因為它已經先前通知。

- 如果屬性警告及錯誤之間切換，決定要多久已不佳 （亦即不按 [確定]）。 例如，如果超過五分鐘，屬性尚未被健全的提醒可以成數 (HealthState ！ = [確定] 和 [立即-LastOkTransitionTime > 5 分鐘)。

## <a name="example-report-and-evaluate-application-health"></a>範例︰ 報表與評估應用程式健康狀況
下列範例會傳送到應用程式透過 PowerShell 狀況報表**布料的轉印圖樣: / WordCount** **MyWatchdog**來源。 狀況報告中包含狀況屬性錯誤狀況，無限 TimeToLive 使用中的 「 可用性 」 的相關資訊。 然後，查詢會傳回彙總健康狀態 」 的錯誤和報告的狀況事件的健康事件清單中的應用程式健全狀況。

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 131032204762818013
                                  SentAt                : 3/23/2016 3:27:56 PM
                                  ReceivedAt            : 3/23/2016 3:27:56 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Ok->Error = 3/23/2016 3:27:56 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="health-model-usage"></a>狀況模型]
健康模型可讓雲端服務與基礎服務布料的轉印圖樣平台，若要縮放，因為監控和健康情況決定分散到不同的監視器叢集內。
其他系統會有單一且集中式服務會剖析所有*可能*有用的資訊是由服務發出叢集層級。 此方法均會阻礙其延展性。 它也不允許他們收集具體的資訊可協助找出問題和為根本原因盡可能接近可能發生的問題。

監控和診斷、 評估叢集和應用程式健康狀況和監視升級，是大量使用狀況模型。 其他服務使用狀況資料執行自動修復、 建立叢集狀況歷程記錄，以及議題在特定條件的提醒。

## <a name="next-steps"></a>後續步驟
[檢視服務布料的轉印圖樣狀況報告](service-fabric-view-entities-aggregated-health.md)

[用於疑難排解系統的狀況報告](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[報告，並檢查服務健康狀況](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[新增自訂的服務布料的轉印圖樣狀況報告](service-fabric-report-health.md)

[監控和診斷本機服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[服務布料的轉印圖樣應用程式升級](service-fabric-application-upgrade.md)
