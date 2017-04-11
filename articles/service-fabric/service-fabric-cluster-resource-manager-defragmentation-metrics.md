<properties
   pageTitle="Azure 服務布料的轉印圖樣計量重組 |Microsoft Azure"
   description="概略瞭解使用重組，或封裝為計量服務布料的轉印圖樣的策略"
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

# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>指標及服務布料的轉印圖樣中載入重組
服務布料的轉印圖樣叢集資源管理員主要被關於平衡而言發佈載入 – 確保所有叢集節點平均使用結束。 這通常是最安全和狀況版面配置，就需要應付失敗，因為其可確保任何指定的失敗不會出一些大型指定工作負載的百分比。 服務布料的轉印圖樣叢集資源管理員支援不同的方法，是重組。 重組通常表示，而不是想要的度量單位，分散叢集，我們應該實際嘗試合併彙算。 這是標準策略 –，而不是最佳化根據最小化平均的標準差，指定的度量單位載入叢集真反轉，我們開始最佳化增加差。 但為什麼您需要這種方法？

如果您已載入平均分配叢集節點然後您已給人吃資源的節點提供部分設定。 通常這不是問題，但有時候某些工作負載建立特別大，並使用大部分的節點服務-假設 75%到 95%節點的資源會得到專用至單一服務執行個體或複本。 這不是問題，叢集資源管理員會偵測需要重新整理此大型工作負載的空間，並瞭解發生這種情形，讓設定才能叢集服務建立時間，但同時的工作量排程叢集等候。

新工作負載的排程通常是至少延遲機密，我們不執行任何動作以不同方式如果我們可以有時這些 Sla 攻擊的效果右如果有很多的服務的和狀態，以移動，尤其是在叢集負載通常都很大 （因此更久叢集中移動）。 實際上，當我們計算實際叢集資料為基礎的模擬建立時間，我們看到的如果服務已夠大，相當利用叢集的建立這些大型服務就會變慢，以及我們無法重組指標的原則，藉改善此。

就像檔案建立] 或 [存取無法取得會向下如果電腦的硬碟已分割，可加速重組磁碟機，讓沒有可用的較大的連續區塊，您可以設定重組指標有叢集資源管理員嘗試主動較少的節點將壓縮的服務，以便 （幾乎） 都有空間更大的服務讓他們能夠快速建立。 大部分的人不需要此，因為服務通常很小，因此不難尋找聊天室，但如果您有大型的服務，需要迅速建立 （，且若要接受其他各方面提高 impactfulness 的失敗次數例如並停留在其排程的工作負載等待 unutilized 的資源），然後重組策略適用於您。

下圖會提供視覺化的方式呈現的兩個不同的叢集，一個重組其中，一個不是。 平衡的大小寫，請考慮是必要位置的最大的服務物件，其中一個新建立、 比較重組叢集，它可能會立即放置的位置節點 4 或 5 如果計時。

![比較平衡與重組叢集][Image1]

## <a name="defragmentation-pros-and-cons"></a>重組優缺點
因此，這些其他概念性必須做的取捨是什麼？ 我們建議您負載之前開啟重組指標徹底度量。 以下是快速表格要考量的事項︰

| 重組專業人員  | 重組缺點 |
|----------------------|----------------------|
|允許更快速地建立大型的服務 | 發表載入到較少的節點，增加競爭
|可讓降低移動資料建立期間    | 失敗可以影響更多服務，會造成其他變換
|可讓豐富的描述需求及回收的間距 | 更複雜的整體資源管理設定

您可以混合重組與標準計量相同叢集以及資源管理員的最佳以確保您收到合併盡量重組指標，為它可以時嘗試擴張其餘的版面配置。 正確的結果，您會收到的平衡標準比較數重組指標和其粗細、 目前載入等數而定。

## <a name="configuring-defragmentation-metrics"></a>設定重組指標
設定重組指標叢集，全域決策並重組可以選取個別的指標︰

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Disk" Value="true" />
    <Parameter Name="CPU" Value="false" />
</Section>
```

## <a name="next-steps"></a>後續步驟
- 叢集資源管理員有許多選項來描述叢集。 若要找出更多相關資訊請查看此文章[說明服務布料的轉印圖樣叢集](service-fabric-cluster-resource-manager-cluster-description.md)
- 指標會服務布料的轉印圖樣叢集資源管理員如何管理消耗量與叢集的容量。 若要瞭解這些和如何將其設定的詳細資訊請參閱[本文](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
