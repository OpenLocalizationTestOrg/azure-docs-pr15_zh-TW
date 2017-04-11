<properties
   pageTitle="服務布料的轉印圖樣叢集資源管理員︰ 移動成本 |Microsoft Azure"
   description="移動成本服務布料的轉印圖樣服務的概觀"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>

# <a name="service-movement-cost-for-influencing-cluster-resource-manager-choices"></a>服務移動成本影響叢集資源管理員選項
當您嘗試決定處理變更，以便輕鬆叢集和分數的解決方案是達到方案的整體成本重要的因素。

至少移動服務執行個體或複本成本 CPU 時間和網路頻寬。 如需狀態服務，也成本建立一份狀態關閉舊複本之前所需的磁碟上的間距量。 清楚您想要最小化 Azure 服務布料的轉印圖樣叢集資源管理員隨附設定任何解決方案的成本。 但您還不想要略過會大幅改善中叢集資源分派的解決方案。

叢集資源管理員有兩種運算成本和限制，即使嘗試管理根據其其他目標叢集方式。 首先，在叢集資源管理員規劃新的版面配置叢集時，計算這會使您每次移動。 在 [簡單的情況下，如果您收到關於相同的兩種解決方案與整體結束時，[餘額 （分數），然後採取最低的成本 （總數量移動） 的項目。

這就很好。 但與預設或靜態載入，不太中所有的移動方式等於複雜的系統。 某些很可能會更高。

## <a name="changing-a-replicas-move-cost-and-factors-to-consider"></a>變更複本的移動成本和考量因素
使用報告載入 （另一個功能的叢集資源管理員），您提供服務的自我報告成本服務將移動特定時間的方式。

程式碼︰

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

MoveCost 有四個階層︰ 零，低、 中型企業版和高。 這些是相對於彼此，除了零。 零表示移動複本免費，而不應該算解決方案的分數。 設定高移動成本**不會移動複本，請只除非有正當理由作，就不會移動它的保證。

![移動成本因素選取移動的複本][Image1]

MoveCost 可協助您尋找干擾至少整體且最簡單的方法達到時仍抵達相當於餘額的解決方案。 成本服務的概念可以相對於多個項目。 在計算移動成本最常見的因素是︰

- 狀態或服務有若要移動的資料量。
- 中斷連線的用戶端的成本。 移動主要的複本的成本大於通常移動次要複本的成本。
- 中斷的 「 執行中 」 的作業的成本。 某些作業的資料儲存區層級，或用戶端打電話來回應所執行的作業會耗費資源。 特定點後不想要停止這些如果您不需要。 因此作業期間，您兩點成本減少的執行個體的服務複本會移動。 完成作業之後，您將它放回 [標準模式。

## <a name="next-steps"></a>後續步驟
- 服務布料的轉印圖樣叢集資源管理員會使用指標來管理消耗量與叢集的容量。 若要瞭解關於指標，以及如何將其設定的詳細資訊，請查看[管理資源消耗並載入的服務與指標布料的轉印圖樣。](service-fabric-cluster-resource-manager-metrics.md)
- 若要瞭解如何叢集資源管理員管理和平衡載入叢集，查看[平衡服務布料的轉印圖樣叢集](service-fabric-cluster-resource-manager-balancing.md)。

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
