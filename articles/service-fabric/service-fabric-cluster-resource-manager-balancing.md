<properties
   pageTitle="平衡叢集與 Azure 服務布料的轉印圖樣叢集資源管理員 |Microsoft Azure"
   description="若要平衡叢集與服務布料的轉印圖樣叢集資源管理員簡介。"
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

# <a name="balancing-your-service-fabric-cluster"></a>平衡服務布料的轉印圖樣叢集
服務布料的轉印圖樣叢集資源管理員可讓報表動態載入、 反應變更叢集、 修正限制式衝突和平衡叢集，如有必要。 但頻率時，這些項目，及觸發它？ 有多個與此相關的控制項。

第一組控制項周圍平衡是計時器一組。 這些計時器管理頻率叢集資源管理員會檢查需要處理的項目叢集的狀態。 有三個不同類別的工作，都有自己的對應計時器。 它們是︰

1.  位置 – 此階段處理放在任何設定狀況的複本或無狀態的執行個體會遺失。 這包含新服務及處理狀態複本或失敗，並必須重新建立無執行個體。 刪除拖放複本或執行個體也會處理以下。
2.  限制式檢查 – 此階段檢查並更正的不同位置的限制式 （規則） 系統中的衝突。 規則的範例是項目，例如確保節點不是透過容量，而且符合的服務的位置限制式 （需這些更新版本）。
3.  平衡 – 此階段檢查是否積極平衡，則需要根據不同的度量平衡設定所需層級，如果是這樣嘗試尋找更多平衡叢集的排列方式。

## <a name="configuring-cluster-resource-manager-steps-and-timers"></a>設定叢集資源管理員步驟及計時器
每個不同類型的修正叢集資源管理員可控制不同的計時器控制它的頻率。 因此，例如，如果您只想要處理新的服務負載置於叢集，每小時 （以批次處理這些設定），但想要平衡一般檢查每隔幾秒鐘，您可以設定的行為。 當每個計時器啟動時，任務排程。 根據預設，資源管理員掃描其狀態且適用於更新 （批次之後發生節點已關閉的最後一個掃描，例如注意的所有變更） 每個 1/10 秒，設定的位置和限制式] 核取旗標每秒，，平衡標幟每 5 秒。

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

目前我們只執行其中一個動作，一次，依序 (因此我們會將這些設定為 「 最小的時間間隔 」))。 這就是這麼一來，例如，我們已回覆來建立新的複本，才能我們移到平衡叢集任何擱置的要求。 您可以看到所指定的預設時間間隔，我們可以掃描，並檢查我們需要做通常這表示您的變更一組我們會將每個步驟的結尾的任何項目會比較小，通常是︰ 我們無法掃描到小時叢集中的變更，並嘗試修正，一次，我們正在處理較多或較少發生，但某些批次，在發生的多個項目時的項目同時間。 如此可讓服務布料的轉印圖樣資源管理員就可能會發生的各項非常回應叢集。

在大部分的這些工作很簡單 （如果有限制式衝突，修正，如果有要建立的服務建立）、 叢集資源管理員也必須一些其他資訊 」 來判斷如果 imbalanced 叢集。 為該我們有兩個其他設備組件的設定︰*平衡臨界值*] 和 [*活動臨界值*。

## <a name="balancing-thresholds"></a>平衡臨界值
平衡臨界值會觸發積極平衡的主控制項 （請記住，計時器只頻率叢集資源管理員應該檢查-不是任何項目會發生這種情形）。 平衡閥值定義如何 imbalanced 叢集需要為特定的度量單位，順序的叢集資源管理員將其 imbalanced 和觸發程序平衡。

平衡臨界值是每個公制為基礎的叢集定義所定義。 如需有關[這份文件](service-fabric-cluster-resource-manager-metrics.md)的指標取出的詳細資訊。

ClusterManifest.xml

``` xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

度量單位的平衡閾值是比例。 如果負載量除以負載量至少載入節點的最載入節點超過此數字，然後叢集會被視為 imbalanced，平衡將會觸發下次叢集資源管理員會檢查 （依預設，也能 5 秒，為受控於 MinLoadBalancingInterval，如上所示）。

![平衡臨界值的範例][Image1]

在此範例中的每個服務正在使用一些度量的單位。 在上方的範例中，最大的載入節點上 5，而最小值是 2。 假設此公制的平衡臨界值為 3。 因此，在上方的範例中，叢集會被視為平衡並沒有平衡將會觸發叢集資源管理員檢查 （由於叢集比 5/2 = 2.5，也就是少於指定平衡臨界值的 3）。

在底部範例中，節點的最大負載時，10，最小值是 2 結果為 5 的比例。 這會將叢集放在該公制 3 的設計平衡臨界值。 如此一來，全域 rebalancing 執行會排程平衡計時器會引發的下一次。 請注意，開始平衡搜尋這不表示任何項目會移動-有時叢集是 imbalanced，但無法改善這種情況-類似的情況下，但 (至少預設) 載入幾乎都將散發至 Node3 部分。 請注意，因為我們不使用窮盡的方法一些載入可能也散發給 Node2 之後，會導致降低整體之間的差異節點，但我們預期，絕大多數的載入想流程至 Node3。

![平衡閥值範例動作][Image2]

請注意，取得平衡臨界值以下不明確的目標 – 平衡臨界值是只*觸發程序*，告訴看起來應該將來決定哪些改良功能，可，如果有任何叢集叢集資源管理員。

## <a name="activity-thresholds"></a>活動臨界值
有時候，雖然節點以往 imbalanced，叢集載入的*總*數量很低。 這可能是只是因為，天的時間，或因為叢集是新，只要發生引導。 在任一情況，您可能不想要花時間平衡叢集，因為獲得確實很難 – 您會只花費網路和計算資源來移動項目，而不進行任何絕對的差異。 我們想要避免執行此動作，因為有另一個控制項內資源管理員，稱為多載入活動臨界值，如果沒有節點至少有這可讓您指定的活動 – 一些絕對下限，然後平衡將不會觸發即使符合平衡閥值。

做為範例假設我們有消耗下列總計這些節點的報表。 現在就讓我們也假設我們保留我們平衡臨界值時，此的度量，3，但現在我們也有 1536年活動臨界值。 在第一個的情況下，每平衡閥值 imbalanced 叢集時沒有節點符合的最小的活動閥值，因此我們保留項目。 在底部範例中，Node1 是方式活動閥值，透過平衡將會執行 （因為超過平衡臨界值和活動臨界值的公制）

![活動臨界值的範例][Image3]

就像平衡臨界值活動臨界值的已定義之每個-公制透過叢集定義︰

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

請注意，平衡與活動臨界值繫結至公制-平衡同時將只會觸發是否平衡與活動臨界值超過，相同的度量。 因此，如果我們超出記憶體和活動臨界值的平衡臨界值的 CPU，平衡將不會觸發，只要不超過剩餘臨界值 （平衡臨界值的 CPU） 和記憶體活動臨界值。

## <a name="balancing-services-together"></a>在一起平衡服務
有趣的是，叢集是否 imbalanced 或不是整個叢集的決定，但我們修正它的方式移動個別服務複本和周圍的執行個體。 這是合理，正確嗎？ 如果記憶體堆疊直條圖一個節點，多個複本，或可能導致執行個體，因此可能需要移動的任何狀態複本或使用受影響、 imbalanced 公制的無執行個體。

有時候雖然我們設定或檔案，將來電客戶的票證拒絕的服務，並不是 imbalanced 會被移。 如何可能會發生服務取得移動，即使該服務的指標的所有已平衡、 更完美，請一次的其他不平衡？ 我們來看看 ！

為例四個服務、 Service1、 Service2、 Service3，以及 Service4。 Service1 報表針對指標 Metric1 及 Metric2，針對 Metric2 和 Mmetric3 Service3 針對 Metric3 和 Metric4，並針對某些公制 Metric99 Service4 Service2。 當然，您可以看到位置我們以下。 我們有鏈結 ！ 從觀點來看的叢集資源管理員中，我們不是真正有四個獨立的服務，我們有多個服務的相關 （Service1 Service2，與 Service3） 和關閉是在本身的項目。

![在一起平衡服務][Image4]

因此，很可能不平衡 Metric1 中的可能會導致複本或屬於 Service3 若要移動的執行個體。 通常這些計時限於相似，但可以會根據完全如何 imbalanced Metric1 較大取得並哪些變更是必要叢集，才能加以修正。 也說，務必的指標 1、 2 或 3 中的不平衡永遠不會造成計時中 Service4 – 有無點自移動複本或執行個體屬於 Service4 周圍可以絕對直接影響餘額的指標 1、 2 或 3。

叢集資源管理員自動找出哪些服務相關的因為服務可能已新增、 移除，或有其公制設定變更 –，例如，兩個執行平衡 Service2 之間可能有已重新設定，以移除 Metric2。 這會中斷 Service1 和 Service2 之間鏈結。 現在，而不是兩個群組的服務，您有三種︰

![在一起平衡服務][Image5]

## <a name="next-steps"></a>後續步驟
- 指標會服務布料的轉印圖樣叢集資源管理員如何管理消耗量與叢集的容量。 若要瞭解這些和如何將其設定的詳細資訊請參閱[本文](service-fabric-cluster-resource-manager-metrics.md)
- 移動成本為訊號叢集資源管理員某些服務會比其他移動較昂貴的其中一個方法。 若要進一步瞭解移動成本，請參閱[本文](service-fabric-cluster-resource-manager-movement-cost.md)
- 叢集資源管理員有數種節流，您可以設定減慢叢集變換。 這些不是以正常方式有必要，但如果您需要您可以瞭解這些[以下](service-fabric-cluster-resource-manager-advanced-throttling.md)


[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
