<properties
   pageTitle="如何檢視 Azure 服務布料的轉印圖樣實體的彙總狀況 |Microsoft Azure"
   description="說明如何查詢、 檢視與評估 Azure 服務布料的轉印圖樣實體的彙總健康情況，透過狀況查詢及一般的查詢。"
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

# <a name="view-service-fabric-health-reports"></a>檢視服務布料的轉印圖樣狀況報告
Azure 服務布料的轉印圖樣介紹包含哪些系統元件和 watchdogs 可以回報他們所監視的本機條件的狀況實體[健康模型](service-fabric-health-introduction.md)。 [狀況儲存](service-fabric-health-introduction.md#health-store)彙總，判斷是否健全的實體所有狀況資料。

預設工作，叢集會填入傳送由系統元件的狀況報告。 [疑難排解使用系統健康狀態報表](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)，了解更多。

服務布料的轉印圖樣提供多種方式，以取得的實體彙總的狀況︰

- [服務布料的轉印圖樣總管](service-fabric-visualizing-your-cluster.md)或其他視覺效果的工具

- 狀況 （透過 PowerShell、 API 或其餘的內容） 的查詢

- 一般查詢實體的健康狀況與其中一個 （透過 PowerShell、 API 或其餘的內容） 的內容，傳回的清單

若要示範這些選項，讓我們來使用本機叢集五個節點。 接下來要**布料的轉印圖樣: / 系統**應用程式 （存在預設工作），其他應用程式部署。 這些應用程式的內容之一是**布料的轉印圖樣: / WordCount**。 這個應用程式的內容七複本以設定的狀態服務。 因為 5 節點，系統元件會顯示警告的磁碟分割下方目標計數。

```xml
<Service Name="WordCountService">
    <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
      <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
    </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>在服務布料的轉印圖樣總管] 中的狀況
服務布料的轉印圖樣 Explorer 提供視覺化叢集的檢視。 下圖中，您可以看到的︰

- 應用程式**布料的轉印圖樣: / WordCount**是紅色 （在錯誤），因為它所**MyWatchdog**報告**可用性**] 屬性的錯誤事件。

- 其中一項服務，**布料的轉印圖樣: / WordCount/WordCountService**黃色 （在警告）。 服務設定與七複本，因為它們無法所有放置，僅有五個節點所。 它不會顯示在這裡，雖然服務分割因為黃色系統報告。 黃色的磁碟分割觸發的黃色的服務。

- 叢集因為紅色紅色的應用程式。

使用預設的原則叢集資訊清單與應用程式資訊清單。 他們嚴格原則，不容許任何失敗。

服務布料的轉印圖樣檔案總管叢集的檢視︰

![檢視服務布料的轉印圖樣檔案總管叢集。][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE] 進一步瞭解[服務布料的轉印圖樣檔案總管](service-fabric-visualizing-your-cluster.md)。

## <a name="health-queries"></a>查詢狀況
服務布料的轉印圖樣公開狀況查詢的每個支援的[實體類型](service-fabric-health-introduction.md#health-entities-and-hierarchy)。 他們可以透過 API （方法可以在**FabricClient.HealthManager**找到）、 PowerShell cmdlet 和其餘存取。 下列查詢會傳回完成的健康狀態資訊的實體︰ 彙總的狀況、 實體健康事件、 子健康狀態 （如果適用的話） 及不健全的實體時不佳的評估。

> [AZURE.NOTE] 它完全填入市集中的狀況時，會傳回狀況實體。 實體必須是作用中 （不會刪除），並讓系統報表。 階層鏈結及其父項目也必須系統報表。 如果沒有符合以下任一情況，狀況查詢會傳回顯示為什麼實體就不會傳回的例外狀況。

狀況查詢必須經過實體識別項實體類型而定。 查詢接受選用的狀況原則參數。 如果沒有狀況原則指定，從叢集或應用程式資訊清單[健康原則](service-fabric-health-introduction.md#health-policies)用於評估。 查詢也接受篩選傳回只有部分的字詞或事件，尊重指定之的篩選的項目。

> [AZURE.NOTE] 輸出篩選會套用伺服器端，因此訊息回覆縮小時。 我們建議您使用的輸出篩選來限制傳回的資料，而非在用戶端上套用篩選。

包含實體的狀況︰

- 實體的彙總的狀況。 計算方式根據實體狀況報告、 子健康狀態 （如果適用的話），以及健康原則的狀況存放區。 進一步瞭解[實體健康評估](service-fabric-health-introduction.md#entity-health-evaluation)。  

- 在實體健康事件。

- 可以有子系的實體的所有子系的健康狀態的集合。 健康狀態包含實體識別項和彙總的狀況。 若要取得完整的狀況的子標題，來電查詢狀況子實體類型和傳入子識別碼。

- 如果不健全的實體指向觸發的實體狀態報表不佳的評估。

## <a name="get-cluster-health"></a>取得叢集狀況
傳回叢集實體的狀況，並包含健康狀態的應用程式和節點 （子系叢集）。 輸入︰

- [選擇性]用來評估節點和叢集事件的叢集狀況原則。

- [選擇性]應用程式狀況原則地圖，以用來覆寫的應用程式的資訊清單原則的狀況原則。

- [選擇性]篩選事件、 節點及指定哪些項目會感興趣的和應傳回結果 （例如，錯誤，或警告和錯誤） 中的應用程式。 所有事件節點，與應用程式都用來評估的彙總的實體狀況，無論篩選。

### <a name="api"></a>API
若要取得叢集狀況，建立`FabricClient`，並在其**HealthManager**呼叫[GetClusterHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthasync.aspx)方法。

下列呼叫取得叢集狀況︰

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

下列程式碼會取得叢集狀況節點和應用程式中使用自訂叢集健康原則和篩選。 它會建立[ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthquerydescription.aspx)，其中包含輸入的資訊。

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
若要取得叢集狀況 cmdlet 是[取得 ServiceFabricClusterHealth](https://msdn.microsoft.com/library/mt125850.aspx)。 首先，請使用連線到叢集[連線 ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx)指令程式。

叢集的狀態是五個節點系統應用程式，與布料的轉印圖樣: / WordCount 設定所述。

下列指令程式使用狀況的預設原則取得叢集健康狀況。 彙總的狀況是在警告中，因為布料的轉印圖樣: / WordCount 應用程式位於警告。 請注意不佳的評估的條件來觸發的彙總的狀況所提供的詳細資料。

```xml
PS C:\> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=false.


NodeHealthStates        :
                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

下列 PowerShell 指令程式會使用自訂的應用程式原則的叢集的狀況。 它會篩選以取得錯誤或警告應用程式和節點的結果。 如此一來，不會傳回節點，只要在所有健全。 僅限布料的轉印圖樣: / WordCount 應用程式會採用應用程式篩選。 因為自訂原則指定考慮布料的轉印圖樣的警告為錯誤: / WordCount 應用程式，應用程式的評估與錯誤，還有叢集。

```powershell
PS c:\> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error"


AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

### <a name="rest"></a>其餘
您可以取得叢集健康狀況與[取得要求](https://msdn.microsoft.com/library/azure/dn707669.aspx)或[文章邀請](https://msdn.microsoft.com/library/azure/dn707696.aspx)內文中包含所述的健康情況原則。

## <a name="get-node-health"></a>取得節點狀況
傳回節點實體的狀況，並包含報告節點的狀況事件。 輸入︰

- [必要]識別節點的節點名稱。

- [選擇性]用來評估健康情況的叢集狀況原則設定。

- [選擇性]指定感興趣的項目，並應傳回的結果 （例如，錯誤，或警告和錯誤） 的事件的篩選。 所有事件都用來評估的彙總的實體狀況，無論篩選。

### <a name="api"></a>API
若要取得節點狀況透過 API，建立`FabricClient`，並在其 HealthManager 呼叫[GetNodeHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getnodehealthasync.aspx)方法。

下列程式碼會指定的節點名稱的節點狀況︰

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

下列程式碼會節點狀況取得指定的節點名稱，並在事件篩選器及自訂原則經過[NodeHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.nodehealthquerydescription.aspx):

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
若要取得節點狀況 cmdlet 是[取得 ServiceFabricNodeHealth](https://msdn.microsoft.com/library/mt125937.aspx)。 首先，請使用連線到叢集[連線 ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx)指令程式。
下列指令程式使用狀況的預設原則取得節點狀況︰

```powershell
PS C:\> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 6
                        SentAt                : 3/22/2016 7:47:56 PM
                        ReceivedAt            : 3/22/2016 7:48:19 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:48:19 PM, LastWarning = 1/1/0001 12:00:00 AM
```

下列 cmdlet 叢集中取得所有節點的狀況︰

```powershell
PS C:\> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_2                     Ok
_Node_0                     Ok
_Node_1                     Ok
_Node_3                     Ok
_Node_4                     Ok
```

### <a name="rest"></a>其餘
您可以取得節點健康狀況與[取得要求](https://msdn.microsoft.com/library/azure/dn707650.aspx)或[文章邀請](https://msdn.microsoft.com/library/azure/dn707665.aspx)內文中包含所述的健康情況原則。

## <a name="get-application-health"></a>取得應用程式健全狀況
傳回應用程式實體的狀況。 它所含的部署的應用程式和服務的子健康狀態。 輸入︰

- [必要]應用程式的名稱 (URI) 識別應用程式。

- [選擇性]若要覆寫的應用程式的資訊清單原則所用的應用程式狀況原則。

- [選擇性]篩選事件、 服務及指定哪些項目的部署應用程式會感興趣，應傳回的結果 （例如，錯誤，或警告和錯誤）。 所有事件、 服務，以及已部署應用程式都用來評估的彙總的實體狀況，無論篩選。

### <a name="api"></a>API
若要取得應用程式狀況，建立`FabricClient`，並在其 HealthManager 呼叫[GetApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getapplicationhealthasync.aspx)方法。

下列程式碼會指定的應用程式名稱 (URI) 的應用程式狀況︰

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

下列程式碼會指定的應用程式名稱 (URI)、 篩選與透過[ApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.applicationhealthquerydescription.aspx)指定的自訂原則的應用程式狀況。

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
若要取得應用程式狀況 cmdlet 是[取得 ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx)。 首先，請使用連線到叢集[連線 ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx)指令程式。

下列指令程式會傳回的健康情況**布料的轉印圖樣: / WordCount**應用程式︰

```powershell
PS c:\>
PS C:\WINDOWS\system32>  Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

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
                                  HealthState           : Ok
                                  SequenceNumber        : 131031545225930951
                                  SentAt                : 3/22/2016 9:08:42 PM
                                  ReceivedAt            : 3/22/2016 9:08:42 PM
                                  TTL                   : Infinite
                                  Description           : Availability checked successfully, latency ok
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 8:55:39 PM, LastWarning = 1/1/0001 12:00:00 AM
```

下列 PowerShell 指令程式會在 [自訂原則傳遞。 也會篩選子系和事件。

```powershell
PS C:\> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>其餘
您可以取得應用程式健康狀況與[取得要求](https://msdn.microsoft.com/library/azure/dn707681.aspx)或[文章邀請](https://msdn.microsoft.com/library/azure/dn707643.aspx)內文中包含所述的健康情況原則。

## <a name="get-service-health"></a>取得服務健康狀況
傳回是服務的實體健康的情況。 它所含的磁碟分割健康狀態。 輸入︰

- [必要]識別服務服務名稱 (URI)。

- [選擇性]若要覆寫的應用程式的資訊清單原則所用的應用程式狀況原則。

- [選擇性]篩選事件及指定哪些項目的磁碟分割區感興趣，應傳回的結果 （例如，錯誤，或警告和錯誤）。 所有事件和磁碟分割區都用來評估的彙總的實體狀況，無論篩選。

### <a name="api"></a>API
若要透過 API 取得服務健康狀況，建立`FabricClient`，並在其 HealthManager 呼叫[GetServiceHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getservicehealthasync.aspx)方法。

下列範例會取得指定的服務名稱 (URI) 的服務健康的狀況︰

```charp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

下列程式碼會指定篩選器及自訂原則透過[ServiceHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.servicehealthquerydescription.aspx)指定的服務名稱 (URI)，服務健康狀況︰

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
若要取得服務健康狀況 cmdlet 是[取得 ServiceFabricServiceHealth](https://msdn.microsoft.com/library/mt125984.aspx)。 首先，請使用連線到叢集[連線 ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx)指令程式。

下列 cmdlet 取得使用預設健康原則的服務健康狀況︰

```powershell
PS C:\> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB',
                        Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                        ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 10
                        SentAt                : 3/22/2016 7:56:53 PM
                        ReceivedAt            : 3/22/2016 7:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        HealthState           : Warning
                        SequenceNumber        : 131031547693687021
                        SentAt                : 3/22/2016 9:12:49 PM
                        ReceivedAt            : 3/22/2016 9:12:49 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        fabric:/WordCount/WordCountService Secondary Partition a1f83a35-d6bf-4d39-b90d-28d15f39599b could not be placed, possibly,
                        due to the following constraints and properties:  
                        Placement Constraint: N/A
                        Depended Service: N/A

                        Constraint Elimination Sequence:
                        ReplicaExclusionStatic eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        ReplicaExclusionDynamic eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        ReplicaExclusionStatic:
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status:
                        None/None

                        ReplicaExclusionDynamic:
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status:
                        None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
```

### <a name="rest"></a>其餘
您可以取得[取得要求](https://msdn.microsoft.com/library/azure/dn707609.aspx)或包含本文所述的健康情況原則[文章要求](https://msdn.microsoft.com/library/azure/dn707646.aspx)的服務健康狀況。

## <a name="get-partition-health"></a>取得分割健全狀況
傳回磁碟分割實體健康的情況。 包含複本健康狀態。 輸入︰

- [必要]識別碼 (GUID) 可識別磁碟分割的磁碟分割。

- [選擇性]若要覆寫的應用程式的資訊清單原則所用的應用程式狀況原則。

- [選擇性]篩選事件及指定感興趣的項目，並應傳回的結果 （例如，錯誤，或警告和錯誤） 的複本。 所有事件和複本會都用來評估的彙總的實體狀況，無論篩選。

### <a name="api"></a>API
若要取得分割狀況透過 API，建立`FabricClient`，並在其 HealthManager 呼叫[GetPartitionHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getpartitionhealthasync.aspx)方法。 若要指定選用的參數，請建立[PartitionHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.partitionhealthquerydescription.aspx)。

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
若要取得的磁碟分割狀況 cmdlet 是[取得 ServiceFabricPartitionHealth](https://msdn.microsoft.com/library/mt125869.aspx)。 首先，請使用連線到叢集[連線 ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx)指令程式。

下列 cmdlet 取得所有的磁碟分割的狀況**布料的轉印圖樣: / WordCount/WordCountService**服務︰

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 131031502143040223
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844060
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844059
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844061
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844058
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 76
                        SentAt                : 3/22/2016 7:57:26 PM
                        ReceivedAt            : 3/22/2016 7:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>其餘
您可以取得[取得要求](https://msdn.microsoft.com/library/azure/dn707683.aspx)或包含本文所述的健康情況原則[文章要求](https://msdn.microsoft.com/library/azure/dn707680.aspx)的磁碟分割狀況。

## <a name="get-replica-health"></a>取得複本狀況
傳回狀態服務複本或無服務的執行個體的狀況。 輸入︰

- [必要]分割識別碼 (GUID) 和複本 ID 可識別複本。

- [選擇性]若要覆寫的應用程式的資訊清單原則所用的應用程式狀況原則參數。

- [選擇性]指定感興趣的項目，並應傳回的結果 （例如，錯誤，或警告和錯誤） 的事件的篩選。 所有事件都用來評估的彙總的實體狀況，無論篩選。

### <a name="api"></a>API
若要取得複本狀況透過 API，建立`FabricClient`，並在其 HealthManager 呼叫[GetReplicaHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getreplicahealthasync.aspx)方法。 若要指定進階的參數，請使用[ReplicaHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.replicahealthquerydescription.aspx)。

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
若要取得複本狀況 cmdlet 是[取得 ServiceFabricReplicaHealth](https://msdn.microsoft.com/library/mt125808.aspx)。 首先，請使用連線到叢集[連線 ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx)指令程式。

下列 cmdlet 取得服務的所有磁碟分割區的主要複本的狀況︰

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
ReplicaId             : 131031502143040223
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131031502145556748
                        SentAt                : 3/22/2016 7:56:54 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>其餘
您可以取得複本健康狀況與[取得要求](https://msdn.microsoft.com/library/azure/dn707673.aspx)或[文章邀請](https://msdn.microsoft.com/library/azure/dn707641.aspx)內文中包含所述的健康情況原則。

## <a name="get-deployed-application-health"></a>取得應用程式健康狀況
傳回節點實體上部署的應用程式的狀況。 包含已部署的服務套件健康狀態。 輸入︰

- [必要]找出部署的應用程式的應用程式的名稱 (URI) 和節點名稱 (string)。

- [選擇性]若要覆寫的應用程式的資訊清單原則所用的應用程式狀況原則。

- [選擇性]篩選事件及指定哪些項目的部署的服務套件感興趣，應傳回的結果 （例如，錯誤，或警告和錯誤）。 所有事件和部署的服務套件，會都用來評估彙總的實體狀況，無論篩選。

### <a name="api"></a>API
若要透過 API 節點上部署的應用程式的健康情況，請建立`FabricClient`，並在其 HealthManager 呼叫[GetDeployedApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync.aspx)方法。 若要指定選用的參數，請使用[DeployedApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedapplicationhealthquerydescription.aspx)。

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
取得應用程式狀況 cmdlet 是[取得 ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx)。 首先，請使用連線到叢集[連線 ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx)指令程式。 若要找出部署應用程式的位置，請執行[取得 ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) ，看看部署的應用程式子系。

下列 cmdlet 取得的健康情況**布料的轉印圖樣: / WordCount** **_Node_2**上部署的應用程式。

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_2


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_2
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131031502143710698
                                     SentAt                : 3/22/2016 7:56:54 PM
                                     ReceivedAt            : 3/22/2016 7:57:12 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>其餘
您可以取得部署的應用程式健康狀況與[取得要求](https://msdn.microsoft.com/library/azure/dn707644.aspx)或[文章邀請](https://msdn.microsoft.com/library/azure/dn707688.aspx)內文中包含所述的健康情況原則。

## <a name="get-deployed-service-package-health"></a>取得已部署的服務套件健康狀況
傳回已部署的服務套件實體的狀況。 輸入︰

- [必要]找出已部署的服務套件的應用程式的名稱 (URI)、 節點名稱 (string) 及服務資訊清單名稱 (string)。

- [選擇性]若要覆寫的應用程式的資訊清單原則所用的應用程式狀況原則。

- [選擇性]指定感興趣的項目，並應傳回的結果 （例如，錯誤，或警告和錯誤） 的事件的篩選。 所有事件都用來評估的彙總的實體狀況，無論篩選。

### <a name="api"></a>API
若要取得已部署的服務套件的健康情況，透過 API，建立`FabricClient`，並在其 HealthManager 呼叫[GetDeployedServicePackageHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync.aspx)方法。 若要指定選用的參數，請使用[DeployedServicePackageHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedservicepackagehealthquerydescription.aspx)。

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
若要取得已部署的服務套件健康狀況 cmdlet 是[取得 ServiceFabricDeployedServicePackageHealth](https://msdn.microsoft.com/library/mt163525.aspx)。 首先，請使用連線到叢集[連線 ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx)指令程式。 若要查看應用程式部署的位置，請執行[取得 ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) ，查看已部署的應用程式。 若要查看哪些服務套件是在應用程式，看看部署的服務套件子系[取得 ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx)輸出中。

下列 cmdlet 取得的**WordCountServicePkg**服務套件的健康情況**布料的轉印圖樣: / WordCount** **_Node_2**上部署的應用程式。 實體有成功的服務套件進入點啟動和成功的服務類型登錄**System.Hosting**報表。

```powershell
PS C:\> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : _Node_2
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 131031502301306211
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 131031502301568982
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 131031502314788519
                        SentAt                : 3/22/2016 7:57:11 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>其餘
您可以取得[取得要求](https://msdn.microsoft.com/library/azure/dn707677.aspx)或包含本文所述的健康情況原則[文章要求](https://msdn.microsoft.com/library/azure/dn707689.aspx)的部署的服務套件健康狀況。

## <a name="health-chunk-queries"></a>狀況區塊查詢
狀況區塊查詢可以傳回多層次叢集子系 （遞迴） 後，每輸入篩選器。 支援允許多的彈性，表達的特定的字詞，會傳回由其唯一識別碼或其他群組識別碼，及/或狀況的進階的篩選。 根據預設，沒有子系在內，而不是 [一律包含 [第一層級子系的狀況命令。

[狀況查詢](service-fabric-view-entities-aggregated-health.md#health-queries)會傳回只第一層級子系指定的實體每個所需的篩選。 若要取得子系的字詞，您必須在每個實體感興趣的呼叫其他狀況 Api。 同樣地，若要取得健全狀況的特定項目，您必須為每個您要的實體呼叫一個健康 API。 進階篩選的區塊查詢可讓您以要求在查詢中，最小化的郵件大小和訊息的數目感興趣的多個項目。

區塊查詢的值是，您可以在一個通話的更多叢集實體 （潛在所有叢集實體開始，所需的根） 取得狀況。 例如，您可以表示複雜的狀況查詢︰

- 傳回只有在錯誤，以及這些應用程式的應用程式會包含在警告的所有服務 | 錯誤。 如需傳回服務，包括所有磁碟分割區。

- 傳回 4 應用程式，指定其名稱的狀況。

- 傳回您要的應用程式類型的應用程式的狀況。

- 傳回所有已部署的實體節點。 傳回所有應用程式，所有部署上指定的節點的應用程式與該節點的所有已部署的服務套件。

- 傳回在錯誤的所有複本。 傳回所有應用程式、 服務、 分割和在錯誤的複本。

- 傳回所有應用程式。 針對指定的服務，包括所有磁碟分割區。

目前僅適用於叢集實體公開狀況區塊查詢。 會傳回叢集狀況區塊，其中包含︰

- 叢集彙總狀況。

- 尊重輸入篩選條件的節點的健康狀態區塊清單。

- 尊重輸入篩選條件的應用程式的健康狀態區塊清單。 每個應用程式健康狀態區塊包含區塊清單與尊重輸入篩選器和區塊清單尊重篩選的所有已部署應用程式的所有服務。 有相同的服務已部署應用程式的字詞。 如此一來，叢集內的所有項目可能會傳回如果要求，請以階層式的方式。

### <a name="cluster-health-chunk-query"></a>叢集狀況區塊查詢
傳回叢集實體的狀況，包含必要的子系的階層式健康狀態區塊。 輸入︰

- [選擇性]用來評估節點和叢集事件的叢集狀況原則。

- [選擇性]應用程式狀況原則地圖，以用來覆寫的應用程式的資訊清單原則的狀況原則。

- [選擇性]篩選節點及指定感興趣的項目，並應傳回結果中的應用程式。 篩選特定的實體實體/群組] 或 [適用於該階層的所有項目。 一個一般篩選及/或細微實體查詢所傳回的特定識別項篩選，就可以包含篩選的清單。 如果是空白，依預設將不會傳回子系。
進一步瞭解[NodeHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.nodehealthstatefilter.aspx)和[ApplicationHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthstatefilter.aspx)篩選。 應用程式篩選可遞迴指定子系的進階的篩選。

區塊結果包含尊重篩選器的字詞。

目前，區塊查詢不會傳回不佳的評估或實體的事件。 您可以使用現有的叢集狀況查詢取得額外資訊。

### <a name="api"></a>API
若要取得叢集狀況區塊，建立`FabricClient`，並在其**HealthManager**呼叫[GetClusterHealthChunkAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync.aspx)方法。 您可以傳入[ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthchunkquerydescription.aspx)描述健康原則和進階篩選。

下列程式碼會取得叢集狀況區塊與進階篩選。

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
若要取得叢集狀況 cmdlet 是[取得 ServiceFabricClusterChunkHealth](https://msdn.microsoft.com/library/mt644772.aspx)。 首先，請使用連線到叢集[連線 ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx)指令程式。

下列程式碼會取得節點，只有當他們是除了特定節點，應該一律會傳回錯誤。

```xml
PS C:\> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters

HealthState                  : Error
NodeHealthStateChunks        :
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

下列 cmdlet 取得應用程式篩選叢集區塊。

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters

HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 1

                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks :
                                   TotalCount            : 1

                                   ServiceName           : fabric:/WordCount/WordCountService
                                   HealthState           : Error
                                   PartitionHealthStateChunks :
                                       TotalCount            : 1

                                       PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                                       HealthState           : Error
                                       ReplicaHealthStateChunks :
                                           TotalCount            : 5

                                           ReplicaOrInstanceId   : 131031502143040223
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844060
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844059
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844061
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844058
                                           HealthState           : Error
```

下列指令程式會傳回所有已部署的實體節點。

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 2

                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 1

                                       ServiceManifestName   : FAS
                                       HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 2

                                       ServiceManifestName   : WordCountServicePkg
                                       HealthState           : Ok

                                       ServiceManifestName   : WordCountWebServicePkg
                                       HealthState           : Ok
```

### <a name="rest"></a>其餘
您可以取得叢集狀況區塊，以[取得要求](https://msdn.microsoft.com/library/azure/mt656722.aspx)或[文章邀請](https://msdn.microsoft.com/library/azure/mt656721.aspx)內文中包含健康原則與說明的進階的篩選。

## <a name="general-queries"></a>一般的查詢
一般查詢傳回的服務布料的轉印圖樣實體指定類型的清單。 這些是透過 API （透過**FabricClient.QueryManager**方法）、 PowerShell cmdlet 和其餘公開。 這些查詢彙總從多個元件子查詢。 這些是[狀況儲存](service-fabric-health-introduction.md#health-store)，其會填入彙總的狀況的每個查詢的結果。  

> [AZURE.NOTE] 一般查詢傳回的實體彙總的狀況，而且不包含豐富的健康情況的資料。 如果不健全實體，您可以遵循以取得所有其狀況資訊，包括事件、 子健康狀態，以及不佳的評估狀況查詢。

如果一般查詢傳回的實體未知的狀況，可能是狀況存放區沒有完整的實體的資料。 您也可將子查詢用狀況存放區未成功 （例如，通訊錯誤，或狀況存放區已經流速控制）。 追蹤使用狀況查詢的實體。 如果子查詢發生暫時性錯誤，例如網路問題，可能會成功這個待處理的查詢。 它也可以提供更多詳細資料從瞭解為什麼未公開實體狀況存放區。

包含**HealthState**的實體查詢為︰

- 節點清單︰ 傳回清單中的節點叢集 （分頁）。
  - API: [FabricClient.QueryClient.GetNodeListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getnodelistasync.aspx)
  - PowerShell︰ 取得 ServiceFabricNode
- 應用程式的清單︰ 叢集 （分頁） 會傳回應用程式的清單。
  - API: [FabricClient.QueryClient.GetApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getapplicationlistasync.aspx)
  - PowerShell︰ 取得 ServiceFabricApplication
- 服務清單︰ 傳回服務清單，在應用程式 （分頁）。
  - API: [FabricClient.QueryClient.GetServiceListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getservicelistasync.aspx)
  - PowerShell︰ 取得 ServiceFabricService
- [分割] 清單︰ 傳回 （分頁） 服務中的磁碟分割之清單。
  - API: [FabricClient.QueryClient.GetPartitionListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getpartitionlistasync.aspx)
  - PowerShell︰ 取得 ServiceFabricPartition
- 複本清單︰ 在磁碟分割 （分頁） 會傳回複本的清單。
  - API: [FabricClient.QueryClient.GetReplicaListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getreplicalistasync.aspx)
  - PowerShell︰ 取得 ServiceFabricReplica
- 部署應用程式的清單︰ 傳回節點部署的應用程式清單。
  - API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync.aspx)
  - PowerShell︰ 取得 ServiceFabricDeployedApplication
- 部署服務套件清單︰ 傳回中部署的應用程式服務套件的清單。
  - API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync.aspx)
  - PowerShell︰ 取得 ServiceFabricDeployedApplication

> [AZURE.NOTE] 某些查詢傳回分頁的結果。 這些查詢傳回的是衍生自清單[PagedList<T>](https://msdn.microsoft.com/library/azure/mt280056.aspx)。 如果結果不符合郵件中，只會傳回頁面 ContinuationToken 追蹤的列舉停止的地方。 您應該繼續通話相同的查詢，並從先前的查詢，以取得下一個結果傳遞接續權杖中。

### <a name="examples"></a>範例

下列程式碼會取得叢集不佳的應用程式︰

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

下列 cmdlet 布料的轉印圖樣取得應用程式詳細資料: / WordCount 應用程式。 請注意，狀況在警告。

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

下列 cmdlet 取得服務的警告狀況︰

```powershell
PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## <a name="cluster-and-application-upgrades"></a>叢集和應用程式升級
監視升級期間叢集和應用程式，服務布料的轉印圖樣，請檢查以確保所有項目仍然健全狀況。 如果實體為評估使用原則設定的狀況不佳，升級適用於特定升級的原則，以決定下一個動作。 升級可能暫停允許使用者互動 （例如修正錯誤情況或變更原則），或它可能會自動回復到前一個好版本。

*叢集*升級期間，您可以取得叢集升級狀態。 升級狀態包含不佳的評估，指向功能叢集不佳。 升級復原由於狀況的問題，因此，如果升級狀態會記住的最後一個不佳的原因。 這項資訊可協助調查錯在哪裡升級復原或停止之後的系統管理員。

同樣地，在*應用程式*升級時，任何不佳的評估都包含在應用程式升級狀態。

下列顯示修改布料的轉印圖樣應用程式升級狀態: / WordCount 應用程式。 監視報告其複本的其中一個錯誤。 狀況檢查未遵守，所以，循環升級。

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2015 5:23:26 PM
FailureTimestampUtc           : 4/21/2015 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

進一步瞭解[升級服務布料的轉印圖樣應用程式](service-fabric-application-upgrade.md)。

## <a name="use-health-evaluations-to-troubleshoot"></a>若要疑難排解的使用狀況評估
每當有叢集或應用程式發生問題，請查看叢集或應用程式找出什麼狀況。 不佳的評估提供什麼觸發目前的健康狀態的詳細資料。 如果您需要您可以向下切入不佳的子實體識別根本原因。

> [AZURE.NOTE] 不佳的評估顯示實體的第一個原因評估為目前的狀況。 可能有多個其他事件的觸發這個狀態，但不是會反映在評估。 若要取得詳細資訊，向下切入找出所有不佳的報表，在叢集的狀況實體。

## <a name="next-steps"></a>後續步驟
[使用系統健康狀態報表疑難排解](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[新增自訂的服務布料的轉印圖樣狀況報告](service-fabric-report-health.md)

[報告，並檢查服務健康狀況](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[監控和診斷本機服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[服務布料的轉印圖樣應用程式升級](service-fabric-application-upgrade.md)
