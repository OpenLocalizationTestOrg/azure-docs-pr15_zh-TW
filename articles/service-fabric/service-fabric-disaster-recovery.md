<properties
   pageTitle="Azure 服務布料的轉印圖樣損毀修復 |Microsoft Azure"
   description="Azure 服務架構提供所需處理損毀的所有類型的功能。 本文將說明可能會發生的損毀，以及如何處理它們的類型。"
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
   ms.date="08/10/2016"
   ms.author="seanmck"/>

# <a name="disaster-recovery-in-azure-service-fabric"></a>Azure 服務布料的轉印圖樣中損毀修復

進行高可用性雲端應用程式的重要確保，它不會受的失敗次數，包括您的控制項之外的所有不同類型。 本文說明實體 Azure 服務布料的轉印圖樣叢集可能損毀的內容中的版面配置，並提供如何處理這種限制或排除停機時間或資料遺失的風險的損毀的指引。

## <a name="physical-layout-of-service-fabric-clusters-in-azure"></a>Azure 中的叢集服務布料的轉印圖樣的實體的版面配置

若要瞭解不同類型的失敗次數所造成的風險，是很有幫助叢集實際的配置方式 Azure 中。

當您建立服務布料的轉印圖樣叢集 Azure 中時，您必須選擇位置主控的區域。 Azure 基礎結構然後佈建新的資源，為該叢集內的區域，尤其要求的虛擬機器 (Vm) 數。 現在就讓我們更仔細查看在這些 Vm 方式及位置佈建。

### <a name="fault-domains"></a>故障網域

根據預設，叢集 Vm 平均散佈在邏輯群組亦稱為故障網域 (FDs)，區段根據主機硬體可能失敗的電腦。 明確地說，如果兩個 Vm 位於兩個不同的 FDs，您可以確定其不要共用相同的電源來源或網路切換。 如此一來的區域網路或停電影響一個 VM 不會影響 [其他]，讓服務來重新叢集內沒有回應的電腦上的工作負載平衡布料的轉印圖樣。

您可以使用叢集地圖[服務布料的轉印圖樣檔案總管](service-fabric-visualizing-your-cluster.md)中提供的故障網域視覺化叢集的版面配置︰

![節點分散服務布料的轉印圖樣檔案總管中的錯誤網域][sfx-cluster-map]

>[AZURE.NOTE] 與其他叢集對應中的座標軸顯示升級的網域，邏輯群組依據 [預定進行的維護活動的節點。 Azure 中的服務布料的轉印圖樣叢集永遠配置五個升級的網域。

### <a name="geographic-distribution"></a>地理通訊群組

目前有[世界各地 26 Azure 區域][azure-regions]，其中有數個其他宣告。 個別的區域可以包含一或多個實體資料中心，視需求和可用的其他因素之間的適當位置而定。 但是請注意，即使是在包含多個實體資料中心的區域，不保證有您叢集 Vm 會平均分配到這些實體的位置。 事實上，目前，指定叢集的所有 Vm 佈都建單一的實體網站內。

## <a name="dealing-with-failures"></a>處理失敗

有數種類型的失敗次數，進而影響叢集，兩者都有它自己降低。 我們會查看這些發生的可能性順序。

### <a name="individual-machine-failures"></a>個別電腦失敗

如上所述，個別電腦失敗次數，內 VM 或中的硬體或軟體裝載故障網域，請在風險沒有自己。 服務布料的轉印圖樣通常會偵測失敗，數秒內，並回覆會相應地根據叢集狀態。 比方說，如果節點已分割的主要複本，新的主要被選定的磁碟分割的次要的複本。 當 Azure 使失敗的 machine 備份時，它會自動重新加入叢集，並再次採取的工作量其共用。

### <a name="multiple-concurrent-machine-failures"></a>多個同時機器失敗

時故障網域大幅降低同時機器失敗的風險，一定會有多個要同時將多個機器叢集的隨機失敗的可能性。

一般而言，只要大多數的節點都會持續為可用，叢集會繼續運作，即使是在較低的容量狀態複本取得封裝成一組較小的電腦並無較少的執行個體可散佈載入。

#### <a name="quorum-loss"></a>仲裁遺失

分割大部分狀態服務的磁碟分割的複本的向下，如果進入狀態亦稱為 「 仲裁遺失。 」 此時，服務布料的轉印圖樣停止允許寫入若要確保其狀態一致且可靠的磁碟分割。 實際上，我們選擇接受一段以確保的用戶端不另行告知其資料的儲存事實上並非時無法使用。 請注意，是否您已選擇加入允許讀取從次要複本狀態服務，您可以繼續執行這些讀取作業，在 [此狀態。 分割仍會保留在仲裁遺失，直到足夠的複本數目完假回來或叢集系統管理員強制移動上使用[修復 ServiceFabricPartition API]系統[repair-partition-ps]。

>[AZURE.WARNING] 執行修復動作，向下的主要複本時，會導致資料遺失。

系統服務可能也會受到影響仲裁遺失，與特定的服務問題的影響。 例如，仲裁損失命名服務會影響名稱解析，而建立新的服務與容錯移轉，將會封鎖仲裁損失容錯移轉管理員服務。 請注意，與不同的服務，嘗試修復系統服務*不*建議使用。 不過，最好的向下複本傳回，只要等候。

#### <a name="minimizing-the-risk-of-quorum-loss"></a>最小化仲裁遺失的風險

您可以藉由增加您的服務的目標複本設定大小最小化仲裁遺失的風險。 還是有幫助您需要時可用的剩餘寫入，儲存記住該應用程式後，可容許在無法使用的節點數目，或叢集升級進行節點暫時無法使用，除了硬體失敗考慮的複本數目。

請考慮下列範例假設您已設定讓您的服務，讓 MinReplicaSetSize 的三個，建議使用生產服務的最小值。 使用三個 TargetReplicaSetSize （一個主要及次要兩個連結） 故障升級 （向下兩個複本） 時將會導致仲裁遺失與您的服務會變成唯讀。 或者，如果您有五個複本時，您能為剩餘的兩個複本仍然可以表單中的最小的複本設定仲裁的兩個失敗承受升級 （三個複本，向下）。

### <a name="data-center-outages-or-destruction"></a>資料中心中斷或損毀

在某些情況中實際的資料中心會暫時無法使用，因為 power 或網路連線中斷。 在下列情況下，您的應用程式和服務布料的轉印圖樣叢集同樣會無法使用，但會保留您的資料。 針對叢集 Azure 中執行，您可以在[Azure 狀態] 頁面]上中斷檢視更新[azure-status-dashboard]。

在不太事件中整個實體資料中心會損毀，有裝載任何服務布料的轉印圖樣叢集會遺失，以及其狀態。

若要防止這個可能性，它是非常重要的定期地理多餘的儲存區的 [[備份您的狀態](service-fabric-reliable-services-backup-restore.md)，並確保您已驗證的功能，將它還原。 您執行備份的頻率會取決於您的復原點目標 (RPO)。 即使您未完全實作備份與還原尚未，您應該執行的處理常式`OnDataLoss`事件，好讓您可以記錄時如下︰

```c#
protected virtual Task<bool> OnDataLoss(CancellationToken cancellationToken)
{
  ServiceEventSource.Current.ServiceMessage(this, "OnDataLoss event received.");
  return Task.FromResult(true);
}
```


### <a name="software-failures-and-other-sources-of-data-loss"></a>軟體失敗及其他來源的資料遺失

資料遺失的原因，服務、 人力操作錯誤，以及安全性漏洞中的問題是常見比廣泛的資料中心失敗。 不過，在所有的情況下，修復策略是相同︰ 定期備份的所有狀態服務並練習還原該狀態的能力。

## <a name="next-steps"></a>後續步驟

- 瞭解如何模擬使用[可測試性架構](service-fabric-testability-overview.md)的各種失敗
- 閱讀其他損毀修復和高可用性資源。 Microsoft 發佈大量的指導方針這些主題。 雖然這些文件的部分參考用於其他產品中的特定技巧，其中包含許多一般的最佳作法，您可以套用的服務布料的轉印圖樣內容中︰
 - [可用性檢查清單](../best-practices-availability-checklist.md)
 - [執行損毀復原向下切入](../sql-database/sql-database-disaster-recovery-drills.md)
 - [損毀修復和 Azure 應用程式的可用性][dr-ha-guide]


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
