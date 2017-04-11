<properties
   pageTitle="不按比例縮放服務布料的轉印圖樣叢集或縮小 |Microsoft Azure"
   description="縮放以符合需求設定的每個節點類型/VM 縮放比例設定的自動調整規則的服務布料的轉印圖樣叢集或縮小。 新增或移除服務布料的轉印圖樣叢集節點"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules"></a>不按比例縮放服務布料的轉印圖樣叢集或縮小使用自動調整規則

虛擬機器比例集是 Azure 計算資源的可用來部署及管理所設定的虛擬機器集合。 服務布料的轉印圖樣叢集中定義的每個節點類型設定為不同的 VM 縮放比例設定。 每個節點輸入然後以進行縮放或出獨立，有幾組不同的連接埠開啟，並可以有不同的容量指標。 進一步瞭解其[服務布料的轉印圖樣 nodetypes](service-fabric-cluster-nodetypes.md)文件中。 由於服務布料的轉印圖樣叢集節點類型所做的 VM 縮放比例設定為在後端，必須先設定自動調整大小的每個節點類型/VM 縮放比例設定的規則。

>[AZURE.NOTE] 您的訂閱必須具備足夠核心，若要新增新 Vm 這個叢集組成。 目前有任何模型驗證，所以您得到部署時間失敗，如果任一配額限制叫用。

## <a name="choose-the-node-typevm-scale-set-to-scale"></a>選擇 [設定為 [不按比例縮放的節點類型/VM 刻度

目前您無法指定自動調整大小的規則 VM 縮放比例設定使用入口網站，讓我們節點類型] 清單，然後將它們的新增自動調整規則中使用 PowerShell 的 Azure （1.0 +）。

若要取得 VM 縮放比例設定叢集組成的清單，請執行下列 cmdlet:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <VM Scale Set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevm-scale-set"></a>設定自動調整大小的節點類型/VM 縮放比例設定的規則

如果您的叢集有多個節點類型，然後重複此每個節點類型/VM 縮放比例設定您想要不按比例縮放 （或縮小）。 考慮的節點數目，您必須設定自動調整大小之前。 您必須在主要節點類型的節點的最小的數字是由您所選擇的可靠性層級導向。 進一步瞭解[可靠性層級](service-fabric-cluster-capacity.md)。

>[AZURE.NOTE]  規模主要節點輸入以小於最小的數字設定叢集不穩定，或將其關閉。 這可能會導致您的應用程式，以及系統服務的資料遺失。

目前自動調整功能不受到載入您的應用程式可能會回報至服務布料的轉印圖樣。 因此您收到自動調整完全導向效能，這次每個 VM 發出的計數器不按比例縮放設定執行個體。  

請依照這些指示[設定的每個 VM 縮放比例設定自動調整](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md)。

>[AZURE.NOTE] 在下案例的比例，除非您節點的類型有 Gold 或銀色持續性層級您需要呼叫[移除 ServiceFabricNodeState cmdlet](https://msdn.microsoft.com/library/azure/mt125993.aspx) ，以適當的節點名稱。

## <a name="manually-add-vms-to-a-node-typevm-scale-set"></a>手動將 Vm 新增至節點類型/VM 縮放設定

依照[快速入門的範本藝廊](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)Vm 中每個節點類型的數字變更的範例/指示操作。 

>[AZURE.NOTE] 新增的 Vm 花費的時間，因此不會新增為瞬間完成的項目。 因此計劃在時間，以允許超過 10 分鐘的時間才能 VM 容量複本新增容量 / 服務取得放置的執行個體。

## <a name="manually-remove-vms-from-the-primary-node-typevm-scale-set"></a>手動將 Vm 移除主要節點類型/VM 縮放設定

>[AZURE.NOTE] 主要節點鍵入叢集中，執行服務布料的轉印圖樣系統服務。 永遠不應該關閉或下的執行個體中的節點類型的數字不按比例縮放的可靠性層小於保證。 請參考[上可靠性層級的詳細資料](service-fabric-cluster-capacity.md)。 

您需要執行下列步驟 VM 執行個體之一，一次。 如此一來關閉正常上您要移除的 VM 執行個體和其他節點上建立的新複本的系統服務 （和狀態服務）。

1. 執行[停用 ServiceFabricNode](https://msdn.microsoft.com/library/mt125852.aspx)企圖 'RemoveNode 「 停用您要移除 （節點類型最高執行個體） 的節點。

2. 執行[取得 ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) ，請確定節點已確實轉換為 [停用。 如果沒有的話，請等候節點] 會停用。 您不能快一點此步驟。

2. 依照[快速入門的範本藝廊](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)，若要變更的數字的 Vm 中的節點類型的範例/指示操作。 移除的執行個體是最高 VM 執行個體。 

3. 重複步驟 1 到 3，如有需要但不是縮小主要節點類型小於可靠性層的保證的執行個體數目。 請參考[上可靠性層級的詳細資料](service-fabric-cluster-capacity.md)。 

## <a name="manually-remove-vms-from-the-non-primary-node-typevm-scale-set"></a>手動將 Vm 移除非主要節點類型/VM 縮放設定

>[AZURE.NOTE] 設定狀態服務，您需要最多可隨時會維持可用性並保留您的服務狀態的節點數目。 在很小，您需要節點的數目等於分割或服務的目標複本設定計數。 

您一次需要執行下列步驟 VM 執行個體之一。 這個選項可讓系統服務 （和狀態服務） 若要關閉正常上您要移除的 VM 執行個體，然後建立新的複本的其他位置。

1. 執行[停用 ServiceFabricNode](https://msdn.microsoft.com/library/mt125852.aspx)企圖 'RemoveNode 「 停用您要移除 （節點類型最高執行個體） 的節點。

2. 執行[取得 ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) ，請確定節點已確實轉換為 [停用。 如果，不先等節點] 會停用。 您不能快一點此步驟。

2. 依照[快速入門的範本藝廊](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)，若要變更的數字的 Vm 中的節點類型的範例/指示操作。 這會立即移除的最高 VM 執行個體。 

3. 重複步驟 1 到 3，如有需要但不是縮小主要節點類型小於可靠性層的保證的執行個體數目。 請參考[上可靠性層級的詳細資料](service-fabric-cluster-capacity.md)。

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>您可能會看到服務布料的轉印圖樣檔案總管中的行為

當您要縮放設定叢集服務布料的轉印圖樣總管會反映 （VM 縮放比例設定執行個體） 屬於叢集的節點數目。  不過，當您不按比例縮放叢集下您會看到顯示不佳的狀態，除非您有適當的節點名稱[移除 ServiceFabricNodeState cmd](https://msdn.microsoft.com/library/mt125993.aspx)移除的節點 VM 執行個體。   

以下是這個問題的說明。

服務布料的轉印圖樣檔案總管中所列的節點會反映出哪些服務布料的轉印圖樣系統服務 (FM 特別) 知道叢集有/具有的節點數目。 當您要縮放設定向下 VM 小數位數時，VM 已遭刪除，但 FM 系統服務仍認為的節點 （已對應至已遭刪除的 VM） 會完假回來。 因此服務布料的轉印圖樣總管持續顯示該節點 （雖然狀況可能錯誤] 或 [未知）。

若要確認移除 VM 時，會移除節點，您有兩個選項︰

1) 選擇 [金色或銀色持續性層級 (可用推出) 叢集節點類型的為您提供基礎結構的整合。 這會自動節點從我們的系統服務 (FM) 狀態時移除向下不按比例縮放。
請參閱[持續性層級的詳細資料](service-fabric-cluster-capacity.md)

2) 一旦已縮小 VM 執行個體，您必須洽詢[移除 ServiceFabricNodeState 指令程式](https://msdn.microsoft.com/library/mt125993.aspx)。

>[AZURE.NOTE] 服務布料的轉印圖樣叢集需要時間必須開啟設定，所有項目才維持可用性與保留狀態-稱為 「 維護仲裁。 」 的節點數目 因此，則通常不安全關閉所有叢集電腦，除非您已先執行[完整備份您的狀態](service-fabric-reliable-services-backup-restore.md)。

## <a name="next-steps"></a>後續步驟
閱讀，也進一步瞭解規劃叢集容量、 升級叢集，和分割服務下列動作︰

- [規劃您叢集容量](service-fabric-cluster-capacity.md)
- [叢集升級](service-fabric-cluster-upgrade.md)
- [最大比例的磁碟分割狀態服務](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png
