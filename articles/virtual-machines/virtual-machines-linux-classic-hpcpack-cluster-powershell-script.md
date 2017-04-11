<properties
   pageTitle="PowerShell 指令碼部署 Linux HPC 叢集 |Microsoft Azure"
   description="執行部署 Linux HPC 套件叢集 Azure 虛擬機器中的 PowerShell 指令碼"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="big-compute"
   ms.date="07/07/2016"
   ms.author="danlep"/>

# <a name="create-a-linux-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>建立 Linux 高效能運算 (HPC) 叢集 HPC 套件 IaaS 部署指令碼

執行 HPC 套件 IaaS 部署部署完成 HPC 叢集 Linux 負載 Azure 虛擬機器中的 PowerShell 指令碼。 叢集包含 Active Directory 加入標頭節點執行 Windows Server 和 Microsoft HPC 套件，並執行其中一個支援 HPC 套件 Linux 散佈的運算節點。 如果您想要部署中的 Windows Azure 負載 HPC 套件叢集，請參閱[建立 Windows HPC 叢集 HPC 套件 IaaS 部署指令碼](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)。 您也可以使用 Azure 資源管理員範本部署 HPC 套件叢集。 例如，請參閱[建立 Linux HPC 叢集運算節點](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-file"></a>範例設定檔

下列設定檔所建立的新網域控制站和網域樹系，及部署 HPC 套件叢集有 1 與本機資料庫的標頭節點和 10 Linux 運算節點。 所有的雲端服務中建立的直接中式地址的位置。 2 的雲端服務與 2 的儲存空間帳戶 (亦即_MyLnxCN 0001_ _MyLnxCN 0005_ _MyLnxCNService01_和_mylnxstorage01_，以) 和_MyLnxCN 0006_ _MyLnxCN 0010_ _MyLnxCNService02_和_mylnxstorage02_來建立 Linux 運算節點。 從 OpenLogic CentOS 7.0 版 Linux 圖像建立運算節點。 

取代您的訂閱名稱及帳戶和服務名稱值。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
    </DomainController>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>10</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## <a name="troubleshooting"></a>疑難排解

* **「 VNet 不存在 」 錯誤**-如果您執行 HPC 套件 IaaS 部署指令碼部署 Azure 中的多個叢集同時在一個訂閱，一或多個部署可能會產生錯誤而失敗 」 VNet *VNet\_名稱*不存在 」。
如果發生此錯誤，重新執行失敗部署的指令碼。

* **問題存取網際網路從 Azure 虛擬網路**-如果您建立新的網域控制站 HPC 套件叢集使用部署指令碼或手動升階標頭節點 VM 網域控制站，您可能會遇到問題 Vm Azure 虛擬網路連線至網際網路。 如果轉寄站 DNS 伺服器會自動設定的網域控制站，而此轉寄站 DNS 伺服器沒有正確解析可以發生這種情況。

    若要解決此問題，請登入網域控制站及 [移除轉寄站設定的設定，或設定有效的轉寄站 DNS 伺服器。 若要這麼做，請在 [伺服器管理員] 中按一下 [**工具** >
    **DNS** ]，以開啟 [DNS 管理員]，然後按兩下 [**轉寄站**。
    
## <a name="next-steps"></a>後續步驟

* 請參閱[開始使用 Linux 運算叢集的節點 HPC 套件 Azure 中](virtual-machines-linux-classic-hpcpack-cluster.md)有關支援 Linux 散佈移動資料，並提交工作 Linux HPC 套件叢集運算節點。
* 使用指令碼建立叢集，並執行 Linux HPC 工作負載的教學課程，請參閱︰
    * [Microsoft HPC pack linux 執行的 NAMD 運算 Azure 中的節點](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
    * [Microsoft HPC pack linux 執行的 OpenFOAM 運算 Azure 中的節點](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)
    * [執行星星-CCM + Microsoft HPC pack linux 運算 Azure 中的節點](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)
