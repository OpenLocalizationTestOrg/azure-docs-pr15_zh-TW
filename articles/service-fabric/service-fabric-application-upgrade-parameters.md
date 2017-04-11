
<properties
   pageTitle="應用程式升級︰ 升級參數 |Microsoft Azure"
   description="說明升級服務布料的轉印圖樣應用程式，包括執行的狀況檢查及自動復原升級原則相關的參數。"
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



# <a name="application-upgrade-parameters"></a>應用程式升級參數

本文將說明在 Azure 服務布料的轉印圖樣應用程式的升級過程中套用的各種參數。 參數包含 [名稱] 和 [應用程式版本。 他們控制逾時和升級時，就會套用的狀況檢查的參數，其指定的原則，必須先套用升級失敗。


<br>

| 參數 | 描述 |
| --- | --- |
| ApplicationName | 正在升級的應用程式的名稱。 範例︰ 布料的轉印圖樣: / VisualObjects，布料的轉印圖樣: / ClusterMonitor  |
| TargetApplicationTypeVersion | 應用程式版本輸入的升級的目標。 |
| FailureAction | 升級失敗時，所服務布料的轉印圖樣採取的動作。 應用程式可能已回復為前更新版本 （復原），或升級可能會停止目前升級的網域。 升級模式的情況中，也會變更為手動。 允許的值為復原和手動。 |
| HealthCheckWaitDurationSec | 升級完成前服務布料的轉印圖樣的升級網域上之後，（在秒） 等候時間評估的應用程式的狀況。 此工期也被視為應該執行應用程式，就可以健全視為之前的時間。 如果狀況檢查通過，升級程序會繼續下一步升級的網域。  如果失敗的狀況檢查，請服務布料的轉印圖樣等待的時間間隔 (UpgradeHealthCheckInterval) 前的狀況檢查一次 HealthCheckRetryTimeout 為止。 預設與建議的值是 0 秒。 |
| HealthCheckRetryTimeoutSec | 該服務布料的轉印圖樣繼續執行之前宣告升級為失敗的狀況評估期間 （（秒））。 預設值為 600 秒。 這個期間開始後 HealthCheckWaitDuration 為止。 在此 HealthCheckRetryTimeout，服務布料的轉印圖樣可能會執行多個應用程式健康情況的狀況檢查。 預設值 10 分鐘的時間，而適當地為您的應用程式中自訂。 |
| HealthCheckStableDurationSec | 期間 （（秒）） 來確認應用程式是穩定移到下一個升級網域或完成升級之前。 此等待工期用來執行狀況檢查後避免未偵測到正確的健康情況的變更。 預設值 120 的秒數，而適當地為您的應用程式中自訂。 |
| UpgradeDomainTimeoutSec | 最大時間 （秒） 升級單一升級網域。 達到此逾時，如果升級停駐點，然後繼續進行根據 UpgradeFailureAction 的設定。 絕不會是預設值 （無限），應該適當地自訂應用程式。 |
| UpgradeTimeout | 適用於整個升級 （在秒） 逾時。 如果達到此逾時，就會觸發升級的停駐點與 UpgradeFailureAction。 絕不會是預設值 （無限），應該適當地自訂應用程式。 |
| UpgradeHealthCheckInterval | 已核取 [健康狀態頻率。 這個參數會指定的_叢集__資訊清單_，[ClusterManager] 區段中，未指定做為升級 cmdlet 的一部分。 預設值為 60 秒。  |






<br>
## <a name="service-health-evaluation-during-application-upgrade"></a>在應用程式升級期間的服務健康狀況評估

<br>
狀況評估準則是選擇性的。 如果未指定的狀況評估準則，開始升級時，服務布料的轉印圖樣使用應用程式執行個體 ApplicationManifest.xml 中指定的應用程式狀況原則。


<br>

| 參數 | 描述 |
| --- | --- |
| ConsiderWarningAsError | 預設值為 False。 評估升級期間的應用程式的狀況時，請將應用程式的警告健康事件視為錯誤。 根據預設，服務布料的轉印圖樣不會評估警告狀況事件會失敗 （錯誤），因此即使沒有警告事件，可以繼續升級。   |
| MaxPercentUnhealthyDeployedApplications | 預設與建議的值為 0。 指定部署的應用程式 （請參閱[健康狀況] 區段](service-fabric-health-introduction.md)） 應用程式會被視為不佳，且無法升級之前可能不佳的最大數目。 此參數定義的應用程式狀況節點並有助於在升級期間偵測到問題。 一般而言，應用程式的複本會取得負載平衡到另一個節點可讓應用程式，以顯示良好，因此可允許繼續升級。 藉由指定嚴格的 MaxPercentUnhealthyDeployedApplications 狀況，服務布料的轉印圖樣可以快速偵測應用程式套件的問題，並協助製作快速升級會失敗。 |
| MaxPercentUnhealthyServices | 預設與建議的值為 0。 在應用程式會被視為不佳，且無法升級之前可能不佳的應用程式執行個體中指定的數目上限服務。 |
| MaxPercentUnhealthyPartitionsPerService | 預設與建議的值為 0。 服務會被視為不佳之前可能不佳的服務中指定磁碟分割區的數上限。 |
| MaxPercentUnhealthyReplicasPerPartition | 預設與建議的值為 0。 之前的磁碟分割會被視為不佳可能不佳的磁碟分割中指定複本的數目上限。 |
| UpgradeReplicaSetCheckTimeout | **無服務**-單一升級網域內服務布料的轉印圖樣嘗試確認提供服務的其他執行個體所。 如果目標執行個體計算多個，服務布料的轉印圖樣會等待]，才能使用進位到最大逾時值的多個執行個體。 指定此逾時使用 UpgradeReplicaSetCheckTimeout 屬性。 如果逾時到期，服務布料的轉印圖樣進行升級，無論服務執行個體數目。 如果目標執行個體計數為 1，不會等待服務布料的轉印圖樣，並立即進行升級。 **狀態服務**-單一升級網域內服務布料的轉印圖樣會以確保複本集具有仲裁。 服務布料的轉印圖樣等待仲裁]，才能使用進位到最大逾時值設 （指定 UpgradeReplicaSetCheckTimeout 屬性）。 如果逾時到期，服務布料的轉印圖樣進行升級，無論仲裁。 這項設定，而且為永不 （無限） 時回復，設定 900 秒時復原。 |
| ForceRestart | 如果您不會更新服務代碼更新設定] 或 [資料套件，重新啟動服務時才 ForceRestart 屬性設定為 true。 更新完成後，服務布料的轉印圖樣通知服務的新設定套件資料有。 服務負責套用變更。 如有必要，服務可以重新啟動本身。 |



<br>
<br>
每個服務類型的應用程式執行個體，就可以指定 MaxPercentUnhealthyServices MaxPercentUnhealthyPartitionsPerService，與 MaxPercentUnhealthyReplicasPerPartition 準則。 設定下列參數每個服務可讓應用程式包含不同的服務不同評估原則的類型。 例如，無狀態的閘道器服務類型可以有不同於特定應用程式的執行個體的狀態引擎服務類型 MaxPercentUnhealthyPartitionsPerService。

## <a name="next-steps"></a>後續步驟

[升級您的應用程式使用 Visual Studio](service-fabric-application-upgrade-tutorial.md)逐步引導您進行使用 Visual Studio 應用程式升級。

[升級您的應用程式使用 Powershell](service-fabric-application-upgrade-tutorial-powershell.md)逐步引導您進行使用 PowerShell 應用程式升級。

學習如何使用[資料序列化](service-fabric-application-upgrade-data-serialization.md)，讓您的應用程式升級相容。

瞭解如何時參考[進階主題](service-fabric-application-upgrade-advanced.md)升級您的應用程式使用進階的功能。

參照中[的疑難排解應用程式升級](service-fabric-application-upgrade-troubleshooting.md)的步驟，在應用程式升級中修正常見問題的解決辦法。
 
