<properties
   pageTitle="疑難排解應用程式升級 |Microsoft Azure"
   description="本文說明一些常見的問題，周圍升級服務布料的轉印圖樣應用程式，以及如何解決方法。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>

# <a name="troubleshoot-application-upgrades"></a>疑難排解應用程式升級

本文說明某些周圍升級 Azure 服務布料的轉印圖樣應用程式，以及如何解決常見的問題。

## <a name="troubleshoot-a-failed-application-upgrade"></a>疑難排解失敗的應用程式升級

升級失敗時，**取得 ServiceFabricApplicationUpgrade**命令的輸出包含偵錯失敗的其他資訊。  下列清單指定如何使用的其他資訊︰

1. 找出失敗類型。
2. 找出失敗的原因。
3. 找出一或多個失敗元件，以便進一步調查。

服務布料的轉印圖樣偵測到無論是否**FailureAction**失敗回復或暫停升級之後，就能使用這項資訊。

### <a name="identify-the-failure-type"></a>找出失敗類型

**取得 ServiceFabricApplicationUpgrade**輸出， **FailureTimestampUtc**識別的時間戳記 （UTC) 的升級的錯誤偵測服務布料的轉印圖樣和觸發**FailureAction** 。 **FailureReason**識別三個可能的高層級原因失敗的其中一項︰

1. UpgradeDomainTimeout-表示特定升級網域太久完成**UpgradeDomainTimeout**過期。
2. OverallUpgradeTimeout-表示整體升級太久完成**UpgradeTimeout**過期。
3. HealthCheck-表示升級之後的更新的網域，應用程式仍不佳，根據指定的健康原則**HealthCheckRetryTimeout**過期。

這些項目只會顯示在輸出時升級失敗，並開始復原。 如需的資訊會顯示視失敗的類型而定。

### <a name="investigate-upgrade-timeouts"></a>調查升級逾時

升級失敗最常服務可用性問題所造成的逾時。 追蹤此段落的輸出是升級的一般服務複本或執行個體失敗開始新的程式碼版本中的位置。 **UpgradeDomainProgressAtFailure**欄位擷取的任何升級的擱置中工作，一次失敗的快照。

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
~~~

在此範例中，在升級無法升級網域*MYUD1* ，兩個資料分割 （*744c8d9f-1d26-417e-a60e-cd48f5c098f0*和*4b43f4d8-b26b-424e-9307-7a7a62e79750*） 已無法解決問題。 磁碟分割區已無法解決問題，因為執行階段無法在目標節點*Node1*和*Node4*放置主要複本 (*WaitForPrimaryPlacement*)。

**取得 ServiceFabricNode** ] 命令可用來確認這兩個節點升級網域*MYUD1*中。 *UpgradePhase*指出*PostUpgradeSafetyCheck*，這表示在升級的網域中的所有節點升級都完成後，會發生下列安全檢查。 這項資訊會指向可能與應用程式碼的新版本的問題。 最常見的問題是開啟或升級至主要的程式碼路徑服務發生錯誤。

*PreUpgradeSafetyCheck*的*UpgradePhase*表示沒有準備升級的網域，它所執行的問題。 最常見的問題在此情況下會關閉或從主要的程式碼路徑降級服務發生錯誤。

目前**UpgradeState**是*RollingBackCompleted*，原始升級必須先使用復原**FailureAction**，復原自動升級失敗時執行。 如果與**FailureAction**手動執行原始升級，然後升級會改為在已擱置狀態，若要允許即時偵錯的應用程式。

### <a name="investigate-health-check-failures"></a>調查健康狀況] 核取失敗

可由各種升級的網域中的所有節點都完成升級，並傳送所有的安全檢查之後，就會發生的問題觸發健康狀況] 核取失敗。 追蹤此段落輸出是因為失敗的狀況檢查升級失敗的一般。 **UnhealthyEvaluations**欄位擷取根據指定的[健康原則](service-fabric-health-introduction.md)的升級次失敗的狀況檢查的快照。

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
~~~

調查健康狀況] 核取失敗第一次需要瞭解服務布料的轉印圖樣健康模型。 即使沒有這類深入瞭解，我們可以看到兩個服務是不佳，但是︰*布料的轉印圖樣: / demoapp 來/Svc3*和*布料的轉印圖樣: / demoapp 來/Svc2*，以及錯誤狀況報告 (在本例中為 「 InjectedFault 」)。 在此範例中，兩個的四個服務的這是預設的目標 0%不佳 (*MaxPercentUnhealthyServices*) 下方。

依指定的手動**FailureAction**啟動升級時，升級已擱置時失敗。 此模式下，可讓我們進一步採取動作前，先調查即時系統失敗的狀態。

### <a name="recover-from-a-suspended-upgrade"></a>從 [已擱置的升級復原

復原**FailureAction**，就需要升級自動回復時失敗，因為沒有復原。 使用手動**FailureAction**，有幾個復原選項︰

1. 手動觸發復原
2. 手動進行升級的餘數
3. 繼續監視的升級

**開始 ServiceFabricApplicationRollback**命令可隨時啟動復原應用程式。 後命令傳回成功，復原要求已註冊系統中，並啟動不久之後。

**履歷表 ServiceFabricApplicationUpgrade** ] 命令可用來手動進行升級的其餘部分，一次一個升級的網域。 在此模式中，系統會執行只安全檢查。 執行其他的狀況檢查。 這個命令僅能時*UpgradeState*顯示*RollingForwardPending*，這表示目前升級的網域，已完成升級，但的下一個啟動 （擱置）。

**更新 ServiceFabricApplicationUpgrade** ] 命令可用來恢復監視的升級兩個安全和健康情況檢查正在執行。

~~~
PS D:\temp> Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored

UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :

PS D:\temp>
~~~

升級位置的最後一個擱置的網域與使用相同升級的參數和健康情況原則之前，繼續升級。 如有需要任何升級的參數和顯示在先前的輸出的健康情況原則可以變更的相同的命令時繼續升級。 在此範例中，在升級已恢復在監視模式中，使用參數和不變的狀況原則。

## <a name="further-troubleshooting"></a>更多疑難排解

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>服務布料的轉印圖樣不追蹤指定的健康原則

可能的原因 1:

服務布料的轉印圖樣轉譯實際的數字的實體 （例如，複本、 磁碟分割區，與服務） 的健康評估為所有的百分比，而一律進位整個項目。 例如，如果最大_MaxPercentUnhealthyReplicasPerPartition_ 21%，而有五個複本，然後服務布料的轉印圖樣允許兩個不佳的複本 (該 is,'Math.Ceiling (5\*0.21))。 因此，應該會相應地設定健康原則。

可能的原因 2:

狀況原則所指定的總服務不是特定的服務執行個體的百分比。 例如之前升級時，如果應用程式有四其中服務 D 是不佳，但太大的影響，應用程式與服務 A、 B、 C 和 D 的執行個體。 我們想要忽略已知的健康服務 D 期間升級和參數是 25%，假設 A、 B 和 C *MaxPercentUnhealthyServices*必須健全的設定。

不過，在升級期間，D 可能會變成健全時 C 會變成不佳。 因為只 25%的服務健康，仍會成功升級。 不過，可能會導致您無法預料的錯誤，因為 C 正在意外不佳，而不是 d 鍵。在此情況下，D 應該模型為不同的服務類型從 A，B 和 c。狀況原則指定每個服務類型，因為不同的健康百分比臨界值可以套用不同的服務。 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>我未指定的健康原則應用程式升級，但升級仍然失敗的 [未指定的一些逾時

狀況原則不提供升級的要求，它們是來自目前的應用程式版本*ApplicationManifest.xml* 。 比方說，如果您要升級應用程式 X 1.0 版版本 2.0，應用程式狀況原則 1.0 版中指定的用。 如果應該使用不同的健康原則升級原則必須指定為應用程式升級 API 通話的部分。 升級期間只適用於 API 通話的部分所指定的原則。 升級完成後，會使用*ApplicationManifest.xml*中指定的原則。

### <a name="incorrect-time-outs-are-specified"></a>指定正確的逾時

您可能會好奇逾時設定不一致時，會發生什麼情況。 例如，您可能必須小於*UpgradeDomainTimeout* *UpgradeTimeout* 。 答案是會傳回錯誤。 如果*UpgradeDomainTimeout*小於*HealthCheckWaitDuration*和*HealthCheckRetryTimeout*，或如果*UpgradeDomainTimeout*小於*HealthCheckWaitDuration*和*HealthCheckStableDuration*的總和，會傳回錯誤。

### <a name="my-upgrades-are-taking-too-long"></a>我的升級的時間太長

升級完成的時間，取決於的狀況檢查及指定逾時。 狀況檢查逾時與取決於所需的時間來複製、 部署及穩定應用程式。 正在太積極逾時可能表示更失敗的升級，因此我們建議您從開始保守更長的時間逾時。

以下是快速複習逾時與升級時間互動的方式︰

升級的升級的網域無法比*HealthCheckWaitDuration*快完成 + *HealthCheckStableDuration*。

升級失敗無法比*HealthCheckWaitDuration*快發生 + *HealthCheckRetryTimeout*。

*UpgradeDomainTimeout*受限於升級的網域的升級時間。  如果*HealthCheckRetryTimeout*和*HealthCheckStableDuration*都非零，就會保存來回切換應用程式的狀況，然後升級最後逾時*UpgradeDomainTimeout*上。 *UpgradeDomainTimeout*開始升級目前升級網域開始之後，向下計數。

## <a name="next-steps"></a>後續步驟

[升級您的應用程式使用 Visual Studio](service-fabric-application-upgrade-tutorial.md)逐步引導您進行使用 Visual Studio 應用程式升級。

[升級您的應用程式使用 Powershell](service-fabric-application-upgrade-tutorial-powershell.md)逐步引導您進行使用 PowerShell 應用程式升級。

控制您的應用程式使用[升級參數](service-fabric-application-upgrade-parameters.md)升級的方式。

學習如何使用[資料序列化](service-fabric-application-upgrade-data-serialization.md)，讓您的應用程式升級相容。

瞭解如何時參考[進階主題](service-fabric-application-upgrade-advanced.md)升級您的應用程式使用進階的功能。

參照中[的疑難排解應用程式升級](service-fabric-application-upgrade-troubleshooting.md)的步驟，在應用程式升級中修正常見問題的解決辦法。
 
