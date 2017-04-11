<properties
   pageTitle="服務布料的轉印圖樣叢集資源管理員位置原則 |Microsoft Azure"
   description="其他位置的原則和服務布料的轉印圖樣服務的規則概觀"
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

# <a name="placement-policies-for-service-fabric-services"></a>服務布料的轉印圖樣服務的位置原則
有許多不同的其他規則，您可能會關懷相關，如果您的服務布料的轉印圖樣叢集合併不同地理地區中，會顯示多個資料中心或 Azure 區域，或如果您的環境橫跨多個區域的淉控制項 （或某些其他情況下，您有法律或原則界限您關注的資訊，或相關的距離有實際的效能延遲的影響）。 大部分的這些無法設定透過節點內容和位置的限制式，但部分更為複雜。 若要簡化我們提供這些額外的 commmands。 就像使用其他位置的限制式，位置原則可以設定每個名稱為服務執行個體為基礎。

## <a name="specifying-invalid-domains"></a>指定正確的網域
InvalidDomain 位置原則可讓您指定特定的錯誤網域不正確的這個工作負載。 此原則可確保特定服務永遠不會執行中的特定區域，例如基於淉或公司原則。 透過個別原則指定多個不正確的網域。

![不正確的網域範例][Image1]

程式碼︰

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>指定所需的網域
必要的網域位置原則要求的所有設定狀況的複本或服務的狀態服務執行個體中指定的網域。 您可以指定多個必要的網域，透過個別原則。

![必要的網域範例][Image2]

程式碼︰

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas"></a>指定慣用的網域，主要的複本
慣用的主要網域會是感興趣的控制項，因為其允許的主要應放置如果執行此作業可能的錯誤網域的選取範圍。 所有項目時健全主要最後會在此網域。 應該網域或主要複本會失敗，或關閉因故主要將也會移轉至其他位置。 如果此位置不在慣用的網域，然後盡可能叢集資源管理員會移回慣用的網域。 自然此設定僅有意義狀態的服務。 此原則會在跨越 Azure 區域或多個資料中心的叢集最有用。 在下列情況中方法，您可以使用重複，所有的位置，但想在特定位置放置主要複本，才能提供作業移至主要的較低的延遲 (撰寫也預設所有讀取都由主要和)。

![慣用的主要網域和容錯移轉][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replicas-to-be-distributed-among-all-domains-and-disallowing-packing"></a>要求會散佈到所有的網域和允許封裝的複本
您可以指定的其他原則是需要一律會散佈到使用錯誤網域的複本。 這會改變依預設在大部分情況下，叢集健全，不過有變質情況位置複本的指定的磁碟分割可能會暫時封裝成單一故障或升級的網域。 例如，假設雖然叢集 9 節點 3 故障網域 （0、 1 和 2） 中和您的服務 3 的複本、 已用於這些錯誤網域 1 和 2 的複本節點已關閉，而由於容量問題，因此沒有任何這些網域中的其他節點是有效。 如果要建立這些複本的服務布料的轉印圖樣，叢集資源管理員必須將它們放在故障網域 0，但建立位置違反故障網域限制式了一種情況。 也會增加整個複本集可能會遺失，（如果 FD 0 會遺失 permananently） 的機率。 （如需詳細資訊，在 [限制式和限制式優先順序通常來說，請參閱[本主題](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities)）

如果您所見過狀況警告，例如 「 負載平衡器偵測此複本︰ 布料的轉印圖樣限制式違規使用: /<some service name>次要磁碟分割<some partition ID>違反限制式︰ FaultDomain 」 這種情況或類似它已按下。 通常這種情況下會暫時 （節點不保持下長時間，或如果他們執行，我們需要建立有取代項目中的正確的錯誤網域，也就是有效的其他節點），但還有一些而進行貿易遺失所有其複本的風險的可用性的工作量。 依指定的 「 RequireDomainDistribution 」 原則，將保證從相同的磁碟分割沒有兩個複本會被允許在相同的錯誤或升級網域，我們可以執行此動作。

某些工作負載希望目標的複本數 （省/市的複本） 在任何時間 （針對總計網域失敗打賭並知道它們通常可以復原本機狀態），而其他人而需要花停機時間的風險的正確性和 dataloss 考量之前。 大部分的生產負載執行與 3 個以上的複本，因為預設為不需要的網域通訊群組，並讓平衡與容錯移轉正常處理情況下，即使這表示的暫時網域都有多個複本封裝到它。

程式碼︰

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

現在，有可能使用這些設定的不地理位置合併該叢集 services？ 確定您無法 ！ 但沒有的絕佳理由太 – 應避免使用特別是必要與無效，慣用的網域設定，除非您實際執行的主要跨距的叢集-它沒有任何意義嘗試強制執行在單一機架，或移到另一個偏好本機叢集的某些區段，除非有不同類型的硬體或工作量分割移指定的工作量並可處理這種情況下，透過標準位置的限制式。

## <a name="next-steps"></a>後續步驟
- 深入瞭解其他可用的選項設定服務上的其他叢集資源管理員設定主題取出的可用[深入了解設定服務](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
