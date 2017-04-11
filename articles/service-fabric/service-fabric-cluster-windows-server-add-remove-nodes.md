<properties
   pageTitle="新增或移除獨立版服務布料的轉印圖樣叢集節點 |Microsoft Azure"
   description="瞭解如何新增或移除到 Azure 服務布料的轉印圖樣叢集實體或執行 Windows Server，可能是內部部署的虛擬機器或任何雲端中的節點。"
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="dkshir;chackdan"/>


# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>新增或移除獨立版服務布料的轉印圖樣叢集 Windows 伺服器上執行的節點

[建立您在 Windows Server 的電腦上的獨立版服務布料的轉印圖樣叢集](service-fabric-cluster-creation-for-windows-server.md)之後，您的業務需求可能會變更，以便您可能需要新增或移除多個叢集節點。 本文提供詳細的步驟以達到這個目的。


## <a name="add-nodes-to-your-cluster"></a>新增至叢集節點

1. 準備 VM/電腦您想要新增至您的叢集[準備其他電腦上以符合叢集部署的必要條件](service-fabric-cluster-creation-for-windows-server.md#preparemachines)一節所述的步驟進行。
2. 規劃哪些故障網域和您要新增此 VM/machine 以升級的網域。
3. 將您想要新增到叢集 VM/電腦的遠端桌面 (RDP)。
4. 複製或[下載的 Windows Server 服務布料的轉印圖樣的獨立套件](http://go.microsoft.com/fwlink/?LinkId=730690)到此 VM/電腦並將它解壓縮套件。
5. 執行 Powershell 身為管理員，並瀏覽至未解壓縮的套件的位置。
6. 使用說明要加入的新節點的參數，請執行*AddNode.ps1* Powershell。 下列範例會將新的節點與類型 NodeType0，IP 位址 182.17.34.52 UD1 和 FD1 稱為 VM5。 *ExistingClusterConnectionEndPoint*是已在現有的叢集節點的連接端點。 此端點，您可以選擇叢集的*任何*節點的 IP 位址。

```
.\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain FD1 -AcceptEULA

```

## <a name="remove-nodes-from-your-cluster"></a>移除叢集節點

1. 根據選擇叢集 Reliablity 層級的情況下，您無法移除主要節點類型的前面的 n 個 (3/5/7/9) 節點
2. 不支援的開發叢集上執行 RemoveNode 命令。
2. 將您想要移除的 VM/電腦的遠端桌面 (RDP)。
2. 複製或[下載的 Windows Server 服務布料的轉印圖樣獨立封裝](http://go.microsoft.com/fwlink/?LinkId=730690)並將它解壓縮這個 VM/機器套件。
3. 執行 Powershell 身為管理員，並瀏覽至未解壓縮的套件的位置。
4. 在 PowerShell 中執行*RemoveNode.ps1* 。 下列範例會移除叢集目前的節點。 *ExistingClientConnectionEndpoint*是用戶端連線結束點會保留在叢集任何節點。 叢集中，選擇 [IP 位址和*任何***其他節點**的端點連接埠]。 這個**其他節點**將在更新移除節點叢集設定。 

```
.\RemoveNode.ps1 -ExistingClientConnectionEndpoint 182.17.34.50:19000
```

請注意，即使移除節點，它可能會顯示為查詢和 SFX 中當掉。 這是已知的缺失，而且會固定在即將來臨的版本。 


## <a name="next-steps"></a>後續步驟
- [獨立 Windows 叢集組態設定](service-fabric-cluster-manifest.md)
- [安全獨立叢集使用 Windows 安全性的 Windows 上](service-fabric-windows-cluster-windows-security.md)
- [安全獨立叢集上 Windows 使用 X509 憑證](service-fabric-windows-cluster-x509-security.md)
- [執行 Windows Azure Vm 以建立獨立版服務布料的轉印圖樣叢集](service-fabric-cluster-creation-with-windows-azure-vms.md)
