<properties
   pageTitle="疑難排解系統的狀況報告與 |Microsoft Azure"
   description="說明疑難排解叢集或應用程式的問題傳送 Azure 服務布料的轉印圖樣元件和使用狀況報告。"
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

# <a name="use-system-health-reports-to-troubleshoot"></a>使用系統健康狀態報表疑難排解

Azure 服務布料的轉印圖樣元件報表預設工作叢集內的所有項目。 [狀況儲存](service-fabric-health-introduction.md#health-store)會建立並刪除以根據系統報表的項目。 它也會將組織這些擷取實體互動階層中。

> [AZURE.NOTE] 若要瞭解狀況相關的概念，閱讀更多在[服務布料的轉印圖樣健康模型](service-fabric-health-introduction.md)。

系統的狀況報告提供完善叢集應用程式的功能和標幟透過狀況的問題。 應用程式和服務，實體實作和運作不正確，從服務布料的轉印圖樣的角度，請確認系統的狀況報告。 報表不提供任何健康監視服務的商務邏輯或偵測停止回應的處理程序。 使用者服務可以豐富狀況資料與他們的邏輯的特定資訊。

> [AZURE.NOTE] 只在系統元件*之後*建立實體，會顯示 watchdogs 狀況報告。 刪除實體時，狀況存放區就會自動刪除所有與其相關聯的狀況報告。 建立新的執行個體的實體時也是一樣 （例如，建立新的服務複本執行個體）。 刪除舊的執行個體相關聯的所有報表或從市集]。

系統元件報表的來源，第一句來識別 「**系統。**」 前置字元。 Watchdogs 不能使用相同的前置詞、 其來源為報表有無效的參數會遭到拒絕。
讓我們來看看一些系統報告，以瞭解觸發它們，以及如何修正所代表的可能問題。

> [AZURE.NOTE] 若要改善有什麼新鮮事叢集和應用程式中看到感興趣的條件上新增報表繼續服務布料的轉印圖樣。

## <a name="cluster-system-health-reports"></a>叢集系統的狀況報告
叢集狀況實體會自動建立市集中的狀況。 如果一切運作正常，沒有系統報表。

### <a name="neighborhood-loss"></a>鄰近遺失
偵測到鄰近遺失時， **System.Federation**報表錯誤。 報表是來自個別節點，並且屬性名稱中包含的節點 ID。 如果在整份服務布料的轉印圖樣遺失一個鄰近，您通常可以預期的兩個事件 （兩邊的間距報表）。 如果有多個鄰近地區遺失，還有更多的事件。

報表指定存留時間全域租用逾時。 報表重新傳送的 TTL 工期每隔半，只要條件維持作用中。 自動在到期時，會移除事件。 移除到期的行為，可確保的報表從狀況存放區已正確地清除，即使報表節點已關閉。

- **SourceId**: System.Federation
- **屬性**︰ 啟動與**鄰近**，其中包括節點資訊
- **後續步驟**︰ 調查遺失鄰近為何 （例如，檢查叢集節點之間的通訊）。

## <a name="node-system-health-reports"></a>節點系統的狀況報告
**System.FM**，它代表容錯移轉管理員服務，是管理叢集節點的相關資訊的授權。 每個節點應該有一份報表從 System.FM 顯示的狀態。 （請參閱[RemoveNodeStateAsync](https://msdn.microsoft.com/library/azure/mt161348.aspx)） 移除節點狀態時，會移除節點項目。

### <a name="node-updown"></a>節點漲跌
System.FM 報告為 [確定]，當節點加入撥打給 （已執行）。 節點離開撥打給時，則會回報錯誤 (升級或只是已關閉，是因為其失敗)。 內建狀況存放區的狀況階層相互關聯與 System.FM 節點報表中的已部署實體上採取的動作。 它會考慮節點的所有已部署實體虛擬上層。 如果已為最報告節點，該節點的部署的實體透過查詢公開，System.FM，為項目相關聯的執行個體相同的執行個體。 當 System.FM 報告節點關機，或重新啟動 （新的執行個體） 時，狀況市集自動清理可以存在於只在向下節點或節點的上一個執行個體上的部署實體。

- **SourceId**: System.FM
- **屬性**︰ 狀態
- **後續步驟**︰ 如果節點下升級，它應該恢復之後已升級。 在此情況下，狀況應該切換回 [確定]。 如果節點不完假回來或失敗，問題需要更多的調查。

下列範例顯示的 [確定] 節點狀況 System.FM 事件︰

```powershell

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 2
                        SentAt                : 4/24/2015 5:27:33 PM
                        ReceivedAt            : 4/24/2015 5:28:50 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 5:28:50 PM

```


### <a name="certificate-expiration"></a>憑證到期日
節點所使用的憑證附近到期時， **System.FabricNode**報表警告。 有三個每個節點的憑證︰ **Certificate_cluster** **Certificate_server**，與**Certificate_default_client**。 至少兩週後到期時，報表健康狀態是 [確定]。 在兩週內到期時，則報表類型為警告。 [TTL] 的下列事件無限，而節點離開叢集時，他們會移除。

- **SourceId**: System.FabricNode
- **屬性**︰ 開始使用**的憑證**，並且包含憑證類型的詳細資訊
- **下一步**︰ 更新附近到期時的憑證。

### <a name="load-capacity-violation"></a>載入容量衝突
服務布料的轉印圖樣負載平衡器報表警告，如果偵測到節點容量違規使用。

 - **SourceId**: System.PLB
 - **屬性**︰**容量**的開頭
 - **後續步驟**︰ 核取 [提供指標，然後在節點檢視目前的容量。

## <a name="application-system-health-reports"></a>應用程式系統的狀況報告
**System.CM**，它代表叢集管理員服務，是管理應用程式的相關資訊的授權。

### <a name="state"></a>狀態
System.CM 報告為 [確定] 時已建立或更新應用程式。 它會告知狀況存放區已刪除的應用程式，以便從中存放區。

- **SourceId**: System.CM
- **屬性**︰ 狀態
- **後續步驟**︰ 如果已建立應用程式，它應該包含叢集管理員狀況報告。 否則，檢查發出查詢的 [應用程式的狀態 (例如，PowerShell cmdlet**取得 ServiceFabricApplication ApplicationName *applicationName** *)。

下列範例顯示狀態事件**布料的轉印圖樣: / WordCount**應用程式︰

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 82
                                  SentAt                : 4/24/2015 6:12:51 PM
                                  ReceivedAt            : 4/24/2015 6:12:51 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/24/2015 6:12:51 PM
```

## <a name="service-system-health-reports"></a>服務系統的狀況報告
**System.FM**，它代表容錯移轉管理員服務，是管理服務的相關資訊的授權。

### <a name="state"></a>狀態
System.FM 報告為 [確定] 建立之後服務。 服務已被刪除時，會刪除狀況存放區中的實體。

- **SourceId**: System.FM
- **屬性**︰ 狀態

下列範例顯示在該服務的狀態事件**布料的轉印圖樣: / WordCount/WordCountService**:

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService

ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Ok
PartitionHealthStates :
                        PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:01 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:01 PM
```

### <a name="unplaced-replicas-violation"></a>放置的複本衝突
**System.PLB**報表警告，如果找不到一或多個服務複本的位置。 到期時，會移除報表。

- **SourceId**: System.FM
- **屬性**︰ 狀態
- **後續步驟**︰ 檢查服務限制及目前狀態的位置。

下列範例顯示違規使用的服務與 5 的節點叢集 7 目標複本以設定︰

```xml
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService


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
                        SequenceNumber        : 131032232425505477
                        SentAt                : 3/23/2016 4:14:02 PM
                        ReceivedAt            : 3/23/2016 4:14:03 PM
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
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="partition-system-health-reports"></a>分割系統的狀況報告
**System.FM**，它代表容錯移轉管理員服務，是管理服務磁碟分割區的相關資訊的授權。

### <a name="state"></a>狀態
System.FM 報告為 [確定] 時的磁碟分割一建立之後，以及狀況良好。 分割刪除時，會刪除從狀況存放區實體。

如果的磁碟分割的最小的複本計算] 下方，則會回報錯誤。 如果磁碟分割不下方的最小的複本計算，但其下的目標複本計算，它會報告警告。 如果的磁碟分割仲裁遺失，System.FM 報表錯誤。

其他的重要事件時重新設定耗費的時間比預期，並建立所需的時間比預期時，請包含警告。 預期時間建立和重新設定都是可設定為依據服務案例。 例如，如果服務的狀態，例如 SQL 資料庫 tb 建立時間長於少量的狀態與服務。

- **SourceId**: System.FM
- **屬性**︰ 狀態
- **後續步驟**︰ 如果狀況不是 [確定]，則可能的一些複本尚未建立、 開啟，或升級至主要或次要正確。 在許多情況下，根本原因會是在開啟或變更角色實作服務錯誤。

下列範例顯示良好的磁碟分割︰

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/StatelessPiApplication/StatelessPiService | Get-ServiceFabricPartitionHealth
PartitionId           : 29da484c-2c08-40c5-b5d9-03774af9a9bf
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 38
                        SentAt                : 4/24/2015 6:33:10 PM
                        ReceivedAt            : 4/24/2015 6:33:31 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:33:31 PM
```

下列範例顯示的目標複本計數] 下方的狀況。 下一步是分割說明，其中顯示的設定方式︰ **MinReplicaSetSize**是兩個， **TargetReplicaSetSize**是 7。 然後取得叢集的節點數目︰ 五個。 因此，在此情況下，兩個複本不能放。

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 37
                        SentAt                : 4/24/2015 6:13:12 PM
                        ReceivedAt            : 4/24/2015 6:13:31 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/24/2015 6:13:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService

PartitionId            : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
PartitionKind          : Int64Range
PartitionLowKey        : 1
PartitionHighKey       : 26
PartitionStatus        : Ready
LastQuorumLossDuration : 00:00:00
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 7
HealthState            : Warning
DataLossNumber         : 130743727710830900
ConfigurationNumber    : 8589934592


PS C:\> @(Get-ServiceFabricNode).Count
5
```

### <a name="replica-constraint-violation"></a>違反複本限制
**System.PLB**報表警告，它會偵測複本限制式違規使用，而無法將磁碟分割的複本。

- **SourceId**: System.PLB
- **屬性**︰ **ReplicaConstraintViolation**的開頭

## <a name="replica-system-health-reports"></a>複本系統的狀況報告
**System.RA**，它代表重新設定代理程式元件，是複本狀態的授權。

### <a name="state"></a>狀態
**System.RA**報告為 [確定] 建立之後的複本。

- **SourceId**: System.RA
- **屬性**︰ 狀態

下列範例顯示良好的複本︰

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth
PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
ReplicaId             : 130743727717237310
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743727718018580
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:02 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:02 PM
```

### <a name="replica-open-status"></a>複本開啟狀態
被叫用的 API 通話時，此狀況報告的描述會包含的開始時間 （國際標準時間）。

如果開啟複本耗費的時間比設定期間， **System.RA**報表警告 (預設︰ 30 分鐘)。 如果 API 影響服務可用性，報表會發出速度 （可設定的間隔，預設值為 30 秒）。 計算的時間包含複寫程式開啟和服務開啟所需的時間。 如果開啟完成屬性變更為 [確定]。

- **SourceId**: System.RA
- **屬性**︰ **ReplicaOpenStatus**
- **後續步驟**︰ 如果狀況不是 [確定]，開啟複本為什麼耗費的時間比預期調查。

### <a name="slow-service-api-call"></a>服務緩慢 API 通話
**System.RAP**和**System.Replicator**報表的警告，如果使用者服務代碼在通話時間長於設定的時間。 完成通話後，會清除警告。

- **SourceId**: System.RAP 或 System.Replicator
- **屬性**︰ 變得很慢的 API 的名稱。 描述會提供更多詳細資料 API 已擱置的時間。
- **後續步驟**︰ 調查為什麼通話耗費的時間比預期。

下列範例會顯示在 [仲裁遺失和調查步驟完成] 以找出原因磁碟分割。 其中一個複本都有警告健康狀態，因此取得其健康狀況。 它會顯示 [服務] 作業會超過預期時間所 System.RAP 報告的事件。 收到這項資訊後下, 一步是檢查服務的程式碼，並有調查。 此例中為**RunAsync**實作狀態服務會擲回處理的例外狀況。 資源回收複本，因此您可能不會出現任何警告狀態的複本。 您可以再試一次 [快速狀況，並查看任何差異複本識別碼。 在某些情況下，重可讓您線索。

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful | Get-ServiceFabricPartitionHealth

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
AggregatedHealthState : Error
UnhealthyEvaluations  :
                        Error event: SourceId='System.FM', Property='State'.

ReplicaHealthStates   :
                        ReplicaId             : 130743748372546446
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746168084332
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746195428808
                        AggregatedHealthState : Warning

                        ReplicaId             : 130743746195428807
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Error
                        SequenceNumber        : 182
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:31 PM
                        TTL                   : Infinite
                        Description           : Partition is in quorum loss.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 4/24/2015 6:51:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful

PartitionId            : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
PartitionKind          : Int64Range
PartitionLowKey        : -9223372036854775808
PartitionHighKey       : 9223372036854775807
PartitionStatus        : InQuorumLoss
LastQuorumLossDuration : 00:00:13
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 3
HealthState            : Error
DataLossNumber         : 130743746152927699
ConfigurationNumber    : 227633266688

PS C:\> Get-ServiceFabricReplica 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

ReplicaId           : 130743746195428808
ReplicaAddress      : PartitionId: 72a0fb3e-53ec-44f2-9983-2f272aca3e38, ReplicaId: 130743746195428808
ReplicaRole         : Primary
NodeName            : Node.3
ReplicaStatus       : Ready
LastInBuildDuration : 00:00:01
HealthState         : Warning

PS C:\> Get-ServiceFabricReplicaHealth 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
ReplicaId             : 130743746195428808
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.RAP', Property='ServiceOpenOperationDuration', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743756170185892
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:33 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 7:00:33 PM

                        SourceId              : System.RAP
                        Property              : ServiceOpenOperationDuration
                        HealthState           : Warning
                        SequenceNumber        : 130743756399407044
                        SentAt                : 4/24/2015 7:00:39 PM
                        ReceivedAt            : 4/24/2015 7:00:59 PM
                        TTL                   : Infinite
                        Description           : Start Time (UTC): 2015-04-24 19:00:17.019
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/24/2015 7:00:59 PM
```

當您啟動偵錯工具錯誤的應用程式時，診斷事件 windows 顯示從 RunAsync 引發的例外狀況︰

![Visual Studio 2015 診斷事件︰ 布料的轉印圖樣 RunAsync 失敗: / HelloWorldStatefulApplication。][1]

Visual Studio 2015 診斷事件︰ RunAsync 失敗**布料的轉印圖樣: / HelloWorldStatefulApplication**。

[1]: ./media/service-fabric-understand-and-troubleshoot-with-system-health-reports/servicefabric-health-vs-runasync-exception.png


### <a name="replication-queue-full"></a>完整的複寫佇列
**System.Replicator**報表警告，如果複寫佇列已滿。 在主要，這通常是因為一或多個次要的複本會變得很慢，確認作業。 在 [次要，這通常是因為服務時套用作業變得很慢。 不完整佇列中時，會清除警告。

- **SourceId**: System.Replicator
- **屬性**︰ **PrimaryReplicationQueueStatus**或**SecondaryReplicationQueueStatus**，根據複本角色

### <a name="slow-naming-operations"></a>命名作業緩慢

**System.NamingService**其主要複本時命名作業所需的時間比可接受的報表健康情況。 命名作業的範例是[CreateServiceAsync](https://msdn.microsoft.com/library/azure/mt124028.aspx) [DeleteServiceAsync](https://msdn.microsoft.com/library/azure/mt124029.aspx)。 更多的方法可以找到下 FabricClient，例如底下[服務管理方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.aspx)或[屬性管理方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.propertymanagementclient.aspx)。

> [AZURE.NOTE] 命名服務服務名稱解析叢集中的位置，並讓使用者管理服務名稱和內容。 這是服務布料的轉印圖樣分割保存服務。 其中一個資料分割代表授權擁有者，其中包含所有服務布料的轉印圖樣名稱和服務的相關的中繼資料。 服務布料的轉印圖樣名稱對應到不同的磁碟分割，稱為名稱的擁有者磁碟分割區，因此服務是可延伸。 進一步瞭解[命名服務](service-fabric-architecture.md)。

當命名作業耗費的時間比預期時，作業，就會標示與*主要命名服務磁碟分割的是作業複本*上警告報表。 如果作業成功完成，則會清除警告。 如果發生錯誤，完成作業，狀況報告包含錯誤的詳細資訊。

- **SourceId**: System.NamingService
- **屬性**︰ 從開始**Duration_**加上字首，識別變得很慢的作業，以及套用操作的服務布料的轉印圖樣名稱。 例如，如果在名稱布料的轉印圖樣建立服務: / MyApp/MyService 的時間太長，屬性是 Duration_AOCreateService.fabric:/MyApp/MyService。 AO 指向此名稱] 和 [作業的命名磁碟分割的角色。
- **後續步驟**︰ 核取命名作業失敗的原因。 每一項作業可能不同根本原因。 比方說，刪除服務可能會停在節點，因為應用程式主機保持損毀問題造成的使用者服務代碼節點。

下列範例顯示建立服務作業。 作業超過設定持續時間。 AO 重試，並將工作傳送到 [否]。 無法完成最後一個作業與逾時。 在此情況下，相同的複本是主要 AO] 及 [無角色。

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>DeployedApplication 系統的狀況報告
**System.Hosting**是已部署實體的授權。

### <a name="activation"></a>啟動
System.Hosting 報告為 [確定] 時，應用程式順利啟動節點。 否則，則會回報錯誤。

- **SourceId**: System.Hosting
- **屬性**︰ 啟動，包括首展版本
- **後續步驟**︰ 如果應用程式不佳，調查啟動失敗的原因。

下列範例顯示成功啟動︰

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName Node.1 -ApplicationName fabric:/WordCount

ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130743727751144415
                                     SentAt                : 4/24/2015 6:12:55 PM
                                     ReceivedAt            : 4/24/2015 6:13:03 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### <a name="download"></a>下載
如果應用程式套件下載失敗， **System.Hosting**報表錯誤。

- **SourceId**: System.Hosting
- **屬性**︰**下載︰*RolloutVersion***
- **後續步驟**︰ 調查下載節點失敗的原因。

## <a name="deployedservicepackage-system-health-reports"></a>DeployedServicePackage 系統的狀況報告
**System.Hosting**是已部署實體的授權。

### <a name="service-package-activation"></a>服務套件啟動
服務套件上的啟動節點是否順利 System.Hosting 報告為 [確定]。 否則，則會回報錯誤。

- **SourceId**: System.Hosting
- **屬性**︰ 啟動
- **後續步驟**︰ 調查啟動失敗的原因。

### <a name="code-package-activation"></a>程式碼套件啟動
**System.Hosting**報表以確定每個程式碼封裝是否順利啟動。 如果啟動失敗，請將其設定報表警告。 如果**CodePackage**無法啟動，或以錯誤大於設定**CodePackageHealthErrorThreshold**，裝載報表錯誤。 如果服務套件中包含多個程式碼套件，以啟動報表產生的每一個。

- **SourceId**: System.Hosting
- **屬性**︰ 使用前置詞**CodePackageActivation**並包含的程式碼封裝並為進入點名稱**CodePackageActivation:*CodePackageName*:*SetupEntryPoint/進入點*** (例如， **CodePackageActivation:Code:SetupEntryPoint**)

### <a name="service-type-registration"></a>服務類型註冊
如果已經順利註冊服務類型**System.Hosting**報告為 [確定]。 如果註冊未完成時間 （如使用**ServiceTypeRegistrationTimeout**設定），則會回報錯誤。 如果是從節點未註冊服務類型，這是因為已關閉的執行的時間。 管理報表警告。

- **SourceId**: System.Hosting
- **屬性**︰ 使用前置詞**ServiceTypeRegistration**並包含的服務類型的名稱 (例如， **ServiceTypeRegistration:FileStoreServiceType**)

下列範例顯示良好的部署的服務套件︰

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName Node.1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130743727751456915
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130743727751613185
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 130743727753644473
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### <a name="download"></a>下載
如果服務套件下載失敗， **System.Hosting**報表錯誤。

- **SourceId**: System.Hosting
- **屬性**︰**下載︰*RolloutVersion***
- **後續步驟**︰ 調查下載節點失敗的原因。

### <a name="upgrade-validation"></a>升級驗證
如果在升級過程中驗證失敗或是升級失敗的節點， **System.Hosting**報表錯誤。

- **SourceId**: System.Hosting
- **屬性**︰ 使用前置詞**FabricUpgradeValidation**並包含升級版本
- **描述**︰ 指向發生錯誤

## <a name="next-steps"></a>後續步驟
[檢視服務布料的轉印圖樣狀況報告](service-fabric-view-entities-aggregated-health.md)

[報告，並檢查服務健康狀況](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[監控和診斷本機服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[服務布料的轉印圖樣應用程式升級](service-fabric-application-upgrade.md)
