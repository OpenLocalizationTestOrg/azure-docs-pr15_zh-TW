<properties
   pageTitle="服務布料的轉印圖樣叢集資源管理員應用程式群組 |Microsoft Azure"
   description="應用程式群組功能在服務布料的轉印圖樣叢集資源管理員的概觀"
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

# <a name="introduction-to-application-groups"></a>應用程式群組簡介
服務布料的轉印圖樣的叢集資源管理員通常是透過分配載入 （透過指標來表示） 平均整個叢集管理叢集資源。 服務布料的轉印圖樣也會管理透過容量的概念整個叢集和叢集中節點的容量。 運作很好的許多不同類型的工作負載，但大量使用不同的服務布料的轉印圖樣應用程式執行個體有時提前其他需求的圖樣。 通常是一些額外的需求︰

- 若要保留的部分的節點數目的應用程式執行個體的服務容量的能力
- 限制指定的一組服務應用程式中執行，允許節點總數的能力
- 定義在本身的應用程式執行個體的容量，才能限制的數字或總資源消耗內的服務

以符合這些需求，我們開發支援我們呼叫應用程式群組。

## <a name="managing-application-capacity"></a>管理應用程式容量
應用程式容量可以用來限制的合併的應用程式，同時的總公制載入個別節點的應用程式的執行個體的節點數目。 也可保留叢集應用程式中的資源。

建立; 時，可以為新的應用程式設定應用程式容量您也可以為現有的應用程式，而不用指定容量應用程式建立的更新。

### <a name="limiting-the-maximum-number-of-nodes"></a>限制節點的數目上限
最簡單的使用案例的應用程式容量時，應用程式 [實例需要限制為特定的節點數目上限。 如果有不指定任何應用程式容量，服務布料的轉印圖樣叢集資源管理員會產生複本根據標準的規則 （平衡或重組），這通常表示，將其服務分散到所有可用的節點叢集，或如果重組已開啟的節點一些任意但較小的數目。

下圖顯示不定義的節點數目上限的應用程式執行個體的潛在的位置，然後相同的應用程式的節點數目上限的設定。 請注意，瞭解哪一個複本或執行個體的服務會取得放在一起所做的任何保證。

![定義的節點數目上限的應用程式執行個體][Image1]

在左側的範例中，應用程式沒有設定，應用程式容量，有三種服務。 CRM 已分散所有複本六個可用的節點才能最佳平衡叢集邏輯決策。 在右側的範例中，我們可以看到相同的應用程式的限制三個節點，在與服務布料的轉印圖樣 CRM 已達成最佳餘額的應用程式的服務複本的位置。

控制項的這個問題的參數稱為 MaximumNodes。 此參數可以設定應用程式建立期間或更新的應用程式執行個體已執行的大小寫服務布料的轉印圖樣 CRM 會限制應用程式的服務已定義之數目上限節點的複本。

Powershell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;

adUpdate.Metrics.Add(appMetric);
```

## <a name="application-metrics-load-and-capacity"></a>應用程式的指標、 載入和生產力
應用程式群組也可讓您定義與指定的應用程式的執行個體，以及這些指標與應用程式的容量相關聯的單位。 所以您可以定義的許多服務可能會在中建立您想要

每個公制有 2 可供設定，說明該應用程式執行個體的容量的值︰

-   總應用程式容量 – 代表特定度量單位的應用程式的總容量。 服務布料的轉印圖樣 CRM 會嘗試限制公制載入到指定的值; 此應用程式服務的總和此外，如果應用程式的服務已使用此限制的載入，服務布料的轉印圖樣叢集資源管理員會不允許任何新的服務或這會導致總載入到超過此限制的磁碟分割區的建立。
-   最大的節點容量 – 指定單一節點上的最大的總載入的應用程式服務的複本。 如果在節點的總載入介紹此容量，服務布料的轉印圖樣 CRM 會移至其他節點的複本，以便遵守容量限制式。

## <a name="reserving-capacity"></a>預約容量
以確保叢集內的資源保留的特定應用程式的執行個體，或即使應用程式執行個體沒有內的服務，即使他們沒有尚未分成資源是另一個應用程式群組的常見用法。 讓我們來看看如何仍會使用。  

### <a name="specifying-a-minimum-number-of-nodes-and-resource-reservation"></a>指定最少的節點和資源的預約功能
預約資源的應用程式執行個體需要指定幾個額外的參數︰ *MinimumNodes*和*NodeReservationCapacity*

- MinimumNodes-就像指定目標的節點數目上限的服務應用程式中執行，您也可以指定的最小的應用程式應該在執行的節點數目。 此設定有效定義資源應該保留在最少的節點的數目保證中所建立的應用程式執行個體叢集內的容量。
- NodeReservationCapacity-NodeReservationCapacity 可以定義，在應用程式中的每個度量。 定義保留做為任何節點所在的任何複本或執行個體中的服務上的應用程式的計量負載的量。

現在就讓我們看看範例，容量保留項目︰

![定義保留的容量的應用程式執行個體][Image2]

在左側的範例中，應用程式沒有任何已定義的應用程式容量。 服務布料的轉印圖樣叢集資源管理員會以確保叢集遞減餘額應用程式的子服務複本和執行個體，以及從其他服務 （以外的應用程式）。

在右側範例中，假設建立應用程式設定為 2，3 和公制定義 20，最大的容量 50 和總應用程式的容量 100，節點的預約功能使用的應用程式設定 MaximumNodes MinimumNodes 服務布料的轉印圖樣會保留在藍色應用程式的兩個節點的容量與不允許使用該容量叢集的其他複本。 使用和針對剩餘的叢集容量計數，就會被視為此保留的應用程式的容量。

叢集資源管理員保留項目與建立應用程式時，會保留容量等於 MinimumNodes * 直到應用程式服務的複本會建立並放置 NodeReservationCapacity 叢集，但它會保留在特定的節點的容量。 由於節點都已選取新的複本，如此一來，大的彈性，只在建立時。 時至少有一個複本會放在其上的特定節點保留容量。

## <a name="obtaining-the-application-load-information"></a>取得應用程式載入資訊
每個應用程式的應用程式容量定義您可以取得報告服務的複本的彙總載入的相關資訊。 服務布料的轉印圖樣為此提供 PowerShell 和 Managed API 的查詢。

例如，您可以使用下列 PowerShell 指令程式來擷取載入︰

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1

```

此查詢輸出包含指定的應用程式，例如節點最小值和最大節點的應用程式容量的基本資訊。 也會發生的應用程式目前所用的節點數目的相關資訊。 因此，每個載入公制會有資訊的相關︰
- 度量單位名稱︰ 名稱。
-   這個應用程式會保留在叢集的預約功能容量︰ 叢集容量。
-   應用程式載入︰ 此應用程式的子複本的總載入時。
-   應用程式容量︰ 最大值允許應用程式載入的值。

## <a name="removing-application-capacity"></a>移除應用程式容量
設定好後的應用程式容量參數是應用程式，他們可以使用更新的應用程式 Api 或 PowerShell cmdlet 來移除。 例如︰

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

這個命令會移除所有應用程式容量參數的應用程式，並服務布料的轉印圖樣叢集資源管理員會啟動此應用程式視為叢集沒有已定義之參數的任何其他應用程式。 命令的效果會立即，及叢集資源管理員會刪除所有應用程式容量參數，此應用程式。一次指定需要更新應用程式 Api 呼叫以適當的參數。

## <a name="restrictions-on-application-capacity"></a>應用程式容量的限制
有數種應用程式容量參數必須遵守的限制。 驗證錯誤的情況下建立或更新的應用程式會被拒絕的錯誤。
所有的整數參數必須非負數數字。
此外，個別參數的限制如下所示︰

-   MinimumNodes 永遠不是大於 MaximumNodes。
-   如果已定義的載入公制容量，他們必須遵循下列規則︰
  - 節點保留容量不能大於節點的最大容量。 例如，您無法限制度量 」 CPU 「 2 單位，節點的容量，並嘗試保留在每個節點 3 的單位。
  - 如果指定 MaximumNodes，然後 MaximumNodes 和最大的節點容量產品不能大於容量總數的應用程式。 例如，如果您設定 「 CPU 」 8 和您設定為 10 的最大的節點的載入度量的最大節點容量，然後容量總數的應用程式必須大於 80，此載入的度量。

在應用程式建立時 （在用戶端），及應用程式更新 （在伺服器端） 時，會強制執行限制。 建立時，這是自 MaximumNodes 清除需求的違規使用範例 < MinimumNodes，以及] 命令，將無法在用戶端要求即使傳送到服務布料的轉印圖樣叢集之前︰

``` posh
New-ServiceFabricApplication –Name fabric:/MyApplication1 –MinimumNodes 6 –MaximumNodes 2
```

無效 update 的範例如下所示。 如果我們對現有的應用程式，並更新一些值的最大節點，請將傳遞更新︰

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MaximumNodes 2
```

之後，我們可以嘗試更新最小的節點︰

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MinimumNodes 6
```

用戶端沒有足夠的相關應用程式的內容，它會允許傳遞至服務布料的轉印圖樣叢集更新。 不過，在 [叢集，服務布料的轉印圖樣會驗證新增現有的參數與參數，將會失敗更新作業，因為值對手最低節點大於最大節點的值。 在此情況下，應用程式容量參數仍會維持不變。

下列限制會放置在順序叢集資源管理員的才能提供最佳的應用程式的服務放置複本的位置。

## <a name="how-not-to-use-application-capacity"></a>如何不使用應用程式容量

-   不要使用應用程式容量限制特定的子集合節點的應用程式︰ 雖然服務布料的轉印圖樣，可確保的每個應用程式已指定的應用程式容量，使用者無法決定哪些節點會被的初始化遵守最大的節點。 這可以使用服務的位置的限制式來達成。
-   若要確保從相同的應用程式的兩個服務永遠放彼此不使用應用程式容量。 如下使用服務之間的相關性關聯，而相關性可能僅限於應該實際放置在一起的服務。

## <a name="next-steps"></a>後續步驟
- 深入瞭解其他可用的選項設定服務上的其他叢集資源管理員設定主題取出的可用[深入了解設定服務](service-fabric-cluster-resource-manager-configure-services.md)
- 若要瞭解叢集資源管理員如何管理和平衡叢集載入時，請查看[平衡載入](service-fabric-cluster-resource-manager-balancing.md)上的文件
- 開始的開頭和[介紹至服務布料的轉印圖樣叢集資源管理員](service-fabric-cluster-resource-manager-introduction.md)
- 如需有關指標通常運作的方式的詳細資訊，閱讀[服務布料的轉印圖樣載入指標](service-fabric-cluster-resource-manager-metrics.md)
- 叢集資源管理員有許多選項來描述叢集。 若要找出更多相關資訊請查看此文章[說明服務布料的轉印圖樣叢集](service-fabric-cluster-resource-manager-cluster-description.md)


[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
