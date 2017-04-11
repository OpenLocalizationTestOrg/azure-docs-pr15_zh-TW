<properties
   pageTitle="在服務布料的轉印圖樣叢集資源管理員節流 |Microsoft Azure"
   description="瞭解如何設定所提供的服務布料的轉印圖樣叢集資源管理員節流。"
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


# <a name="throttling-the-behavior-of-the-service-fabric-cluster-resource-manager"></a>節流行為的服務布料的轉印圖樣叢集資源管理員
即使您已正確設定叢集資源管理員，可以取得中斷叢集。 例如可能會同時節點或錯誤網域失敗-如果您在升級期間會發生，會發生什麼情況？ 資源管理員會嘗試維持最佳修正所有項目，但就像這樣的時間您可能會想要考慮的支援，以便叢集本身有機會穩定前往了再次執行的節點，網路條件治療本身 (部署更正後的位元）。 為了協助各種情況，服務布料的轉印圖樣叢集資源管理員包含多個節流。 請注意，下列節流是相當破壞通常不應使用，除非有一些小心完成周圍的平行實際可以叢集，以及經常執行的工時量的數學需要回應這些排序的 (ahem) 尚未計劃 macroscopic 重新設定事件 (AKA: 「 很不正確的天數 」)。

一般而言，我們建議您避免錯誤其他選項 （例如一般的程式碼更新或避免開始 overscheduling 叢集） 之間的天數，而不節流，防止嘗試修正本身時，使用資源叢集）。 節流沒有我們透過經驗，確定預設值]，找到，但您可能應該看並調整其您預期的實際負載的預設值。 雖然不太限制或載入叢集是最佳作法，您可能會決定是在 （除非您可以加以修正） 案例的您需要的位置，有幾個節流即使表示叢集會穩定較長的時間。

##<a name="configuring-the-throttles"></a>設定節流
預設所含的節流是︰

-   GlobalMovementThrottleThreshold – 這會控制叢集計時的總數 （定義為 GlobalMovementThrottleCountingInterval，秒數的值） 的一些時間
-   MovementPerPartitionThrottleThreshold – 這會控制的任何服務分割計時的總數一些時間 (MovementPerPartitionThrottleCountingInterval，秒數的值)

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

請注意，大多數我們看到客戶使用已經過這些節流因為他們已在資源限制環境 （例如，將個別節點或未最平行複本的需求的磁碟有限的網路頻寬建置的已放在） 哪些全新這類的作業不成功或就會變得很慢繼續。  在下列情況中的客戶都習慣知道，它們可能已延伸需要花達到穩定的狀態，包括知道他們無法結束時，所經流速控制執行較低的整體可靠性在叢集的時間量。

## <a name="next-steps"></a>後續步驟
- 若要瞭解叢集資源管理員如何管理和平衡叢集載入時，請查看[平衡載入](service-fabric-cluster-resource-manager-balancing.md)上的文件
- 叢集資源管理員有許多選項來描述叢集。 若要找出更多相關資訊請查看此文章[說明服務布料的轉印圖樣叢集](service-fabric-cluster-resource-manager-cluster-description.md)
