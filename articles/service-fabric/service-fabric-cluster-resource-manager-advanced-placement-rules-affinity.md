<properties
   pageTitle="服務布料的轉印圖樣叢集資源管理員相關性 |Microsoft Azure"
   description="設定服務布料的轉印圖樣服務的相關性的概觀"
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

# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>設定和使用服務相關性中服務布料的轉印圖樣

相關性是控制項的主要提供協助減輕雲端和 microservices 世界的較大的整合應用程式的轉場效果。 然而，也會用於在某些情況下為正確的最佳化提升效能的服務，但這樣可能會影響。

例如，假設您要使較大的應用程式，或只是不在設計 microservices 記住，服務布料的轉印圖樣。 此轉場效果實際常見，而我們透過數種客戶 （內部和外部） 在這種情況。 您開始以提升環境，讓它封裝中，執行整個應用程式。 然後您就可以開始細分為不同的所有彼此的較小服務。

則為 「 Oops...]。 「 Oops 」 通常可分為其中一個類別︰

1. 整合應用程式中的部分元件 X 記載的相依性元件 Y，只要開啟這些到不同的服務。 由於這些立即執行的不同的節點叢集，就會中斷。
2.  透過通訊這些項目 (本機具名管道 | 共用記憶體 | 磁碟上的檔案)，但真的需要能獨立稍微加速更新。 我會稍後移除辛苦完成的相依性。
3.  所有項目沒有問題，但其實這兩個元件都實際上非常談話效能機密。 當時，將它們移到另一個服務整體 tanked 應用程式的效能或延遲增加。 如此一來，整體的應用程式不符合期望。

讓我們在這些情況下不想要遺失我們重構的工作，並不想要回到 monolith，但我們需要執行一些有意義的位置。 這會保留直到我們可以重新設計的元件以自然為服務，或我們可以解決效能期望其他方式，如果可以的話，直到。

如何？ 以及您可以嘗試開啟相關性。

## <a name="how-to-configure-affinity"></a>如何設定相似性
若要設定相關性，您可以定義相關性之間的關聯性兩個不同的服務。 您可以將相關性在另一個 「 指向 」 一項服務，以及說 「 這項服務只能執行位置正在執行服務。 」 有時候我們參照相關性父子關聯性 （在此時子上層）。 相關性可確保一項服務的執行個體的複本會放在相同的節點為複本或另一個執行個體上。

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## <a name="different-affinity-options"></a>不同的相關性選項
相關性表示透過數種相互關聯配置，其中一項，而有兩種不同的模式。 最常見模式是相關性的指 NonAlignedAffinity。 在 NonAlignedAffinity 不同的服務的執行個體的複本會放在相同的節點。 其他模式是 AlignedAffinity。 對齊的相關性依然相當實用只能與狀態的服務。 設定兩個狀態的服務，以有對齊相關性可確保這些服務主運算位於相同的節點為彼此。 也會造成各個成對的次要連結放在同一個節點這些服務。 您也可 （雖然較不普遍） 來設定 NonAlignedAffinity 狀態的服務。 NonAlignedAffinity 的兩個狀態的服務不同的複本會組上相同的節點，但不想要嘗試對齊其主運算或次要連結。

![相關性模式和及其作用][Image1]

### <a name="best-effort-desired-state"></a>最佳的所需的工作狀態
有相關性和整合架構提供的一些差異。 許多都是因為相關性關聯最佳效果。 相關性關聯的服務是基本上是不同的實體可能會失敗，獨立地移動。 還有的相關性關聯會中斷為什麼的原因。 例如，在指定的節點可以容納容量限制只有部分的服務物件相關性關聯中的位置。 在這些情況下即使只有相關性關聯的位置，就無法強制執行因為其他限制式。 如果可以在稍後會自動校正相關性限制式違反強制所有其他限制式和相關性。  

### <a name="chains-vs-stars"></a>與顆星鏈結
現在我們無法相關性關聯的模型鏈結。 這表示的是一個相關性關聯子系服務不能是另一個相關性關聯的上層。 如果您想要的模型此類型的關聯，有效必須為星形，而不是鏈結模型它。 才能執行這項操作，底部的子會是父項 「 中間名 」 的子父改為。 根據您的服務的排列方式，您可能需要建立要做為多個子上層 」 版面配置區 」 服務。

![鏈結與顆星相關性關聯的內容][Image2]

至今天相關性關聯性的相關附註的另一項是它們的方向。 這表示的 「 關聯 」 規則只會強制執行的子系是上層] 位於何處。 如果，例如上層突然無法移到另一個節點然後叢集資源管理員不認為有錯誤的任何項目，直到注意到子不是位於上層;不會立即強制關聯。

### <a name="partitioning-support"></a>分割支援
請注意，相關性的最後一個項目是關聯不支援上層分割的位置的相關性。 這是我們可能最後支援，但不是允許今天的項目。

## <a name="next-steps"></a>後續步驟
- 深入瞭解其他可用的選項設定服務上的其他叢集資源管理員設定主題取出的可用[深入了解設定服務](service-fabric-cluster-resource-manager-configure-services.md)
- 原因相關性，例如限制小型企業服務的使用位置設定的電腦，然後嘗試彙總的服務，集合中的載入更有效地支援透過應用程式的群組。 查看[應用程式群組](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png
