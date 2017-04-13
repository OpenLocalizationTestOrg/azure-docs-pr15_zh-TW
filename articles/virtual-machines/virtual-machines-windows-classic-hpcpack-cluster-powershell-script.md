<properties
   pageTitle="PowerShell 指令碼部署 Windows HPC 叢集 |Microsoft Azure"
   description="執行部署 Windows HPC 套件叢集 Azure 虛擬機器中的 PowerShell 指令碼"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="big-compute"
   ms.date="07/07/2016"
   ms.author="danlep"/>

# <a name="create-a-windows-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>建立的 Windows 高效能運算 (HPC) 叢集 HPC 套件 IaaS 部署指令碼

執行 HPC 套件 IaaS 部署部署完成 HPC 叢集 Windows Azure 虛擬機器中的工作負載的 PowerShell 指令碼。 Active Directory 加入標頭節點執行 Windows Server 和 Microsoft HPC 套件，包括叢集和其他 Windows 計算您指定的資源。 如果您想要部署 Linux 負載 HPC 套件叢集 Azure 中的，請參閱[建立 Linux HPC 叢集 HPC 套件 IaaS 部署指令碼](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)。 您也可以使用 Azure 資源管理員範本部署 HPC 套件叢集。 如需範例，請參閱[建立 HPC 叢集](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)並[建立自訂的 HPC 叢集計算節點圖像](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-files"></a>範例設定檔

在下列範例中，以取代您的訂閱 Id 的值或名稱和帳戶和服務的名稱。

### <a name="example-1"></a>範例 1

下列設定檔部署已與本機資料庫的標頭節點 HPC 套件叢集和五個運算節點執行 Windows Server 2012 R2 的作業系統。 所有的雲端服務中建立的直接西美國位置。 主節點做為網域控制站的網域樹系。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2012R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### <a name="example-2"></a>範例 2

下列設定檔部署在現有的網域樹系 HPC 套件叢集。 有 1 與本機資料庫的標頭節點並 12 運算節點套用 BGInfo VM 副檔名。
在網域樹系所有 Vm 已停都用自動安裝 Windows 更新。 所有的雲端服務中建立的直接中式地址的位置。 建立運算節點三個雲端服務與三個儲存帳戶︰ _MyHPCCN 0001_至_MyHPCCN 0005_ _MyHPCCNService01_和_mycnstorage01_;_MyHPCCN 0006_至_MyHPCCN0010_ _MyHPCCNService02_和_mycnstorage02_;與_MyHPCCN 0011_至_MyHPCCN 0012_ _MyHPCCNService03_和_mycnstorage03_中）。 從現有私人影像擷取從運算節點建立運算節點。 自動變大和縮小服務已啟用使用預設變大和縮小成時間間隔。

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
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### <a name="example-3"></a>範例 3

下列設定檔部署在現有的網域樹系 HPC 套件叢集。 叢集包含一個主節點、 一個資料庫伺服器以 500 GB 資料光碟、 2 代理人節點執行 Windows Server 2012 R2 的作業系統，以及執行 Windows Server 2012 R2 作業系統的五個運算節點。 雲端服務 MyHPCCNService 中會建立相關性群組*MyIBAffinityGroup*，並且相關性群組*MyAffinityGroup*建立其他的雲端服務。 在主節點時啟用 HPC 工作排程器 REST API 及 HPC 入口網站。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```



### <a name="example-4"></a>範例 4

下列設定檔部署在現有的網域樹系 HPC 套件叢集。 有兩個標頭的節點本機資料庫時，會建立兩個 Azure 節點範本，並三個大小中型 Azure 節點建立 Azure 節點範本_AzureTemplate1_。 主節點上的指令碼檔執行之後設定主節點。

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```

## <a name="troubleshooting"></a>疑難排解


* **「 VNet 不存在 」 錯誤**-如果您執行的指令碼部署 Azure 中的多個叢集同時在一個訂閱，一或多個部署可能會產生錯誤而失敗 」 VNet *VNet\_名稱*不存在 」。
如果發生此錯誤，請執行一次失敗的部署的指令碼。

* **問題存取網際網路從 Azure 虛擬網路**-如果您建立新的網域控制站叢集使用部署指令碼或手動升階標頭節點 VM 網域控制站，您可能會遇到問題 Vm 連線至網際網路。 如果轉寄站 DNS 伺服器會自動設定的網域控制站，而此轉寄站的 DNS 伺服器沒有正確解析，就可能發生此問題。

    若要解決此問題，請登入網域控制站及 [移除轉寄站設定的設定，或設定有效的轉寄站 DNS 伺服器。 若要設定此設定，伺服器管理員] 中按一下 [**工具** >
  開啟 [DNS 管理員]，然後連按兩下**轉寄站**的  **DNS** 。

* **問題存取 RDMA 網路運算密集 Vm 從**-如果您新增 Windows Server 計算或仲介處理節點 Vm 使用 RDMA 簡訊的大小，例如 A8 或 A9，您可能會遇到問題 RDMA 應用程式的網路連線這些 Vm。 就會發生此問題的其中一個原因是如果 HpcVmDrivers 副檔名安裝不正確時 Vm 會新增至叢集。 例如，副檔名為卡住安裝狀態。

    若要解決這個問題，請先核取的狀態中 Vm 副檔名。 如果副檔名不正確的安裝，請嘗試移除 HPC 叢集節點，然後再次新增節點。 例如，您可以新增運算節點 Vm 在執行新增 HpcIaaSNode.ps1 指令碼。
    
## <a name="next-steps"></a>後續步驟

* 請嘗試執行測試工作量叢集上。 例如，請參閱[快速入門指南](https://technet.microsoft.com/library/jj884144)HPC 套件。

* 指令碼叢集部署，並執行 HPC 工作負載的教學課程，請參閱[快速入門中執行 Excel 和 SOA 負載 Azure HPC 套件叢集](virtual-machines-windows-excel-cluster-hpcpack.md)。

* 嘗試 HPC 封包的工具開始、 停止、 新增和移除您所建立的叢集運算節點。 請參閱[管理計算 Azure 中 HPC 套件叢集節點](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)。

* 若要取得設定叢集提交工作，從本機電腦，請參閱[從內部部署電腦到 HPC 套件叢集 Azure 中送出 HPC 工作](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)。
