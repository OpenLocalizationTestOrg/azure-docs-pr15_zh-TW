<properties
 pageTitle="關於使用 Linux 運算密集 Vm |Microsoft Azure"
 description="取得背景資訊和使用 Linux Vm H 數列與 A8、 A9、 A10 和 A11 運算密集大小的考量因素"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>關於 H 數列和運算密集的數列 Vm 

以下是背景資訊和使用新版 Azure H 數列和舊版 A8、 A9、 A10 及 A11 大小，也稱為*運算密集*執行個體的一些考量。 本文著重於 Linux Vm 使用這些大小。 本文也適用於[Windows Vm](virtual-machines-windows-a8-a9-a10-a11-specs.md)。




[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>RDMA 網路存取權

您可以建立執行下列其中一項支援 Linux HPC 散佈和善用 Azure RDMA 網路支援的 MPI 實作 RDMA 簡訊 Linux Vm 的叢集。 請參閱[設定 Linux RDMA 叢集執行 MPI 應用程式](virtual-machines-linux-classic-rdma-cluster.md)的部署選項和範例設定步驟。

* **散佈**-您必須從 Azure Marketplace RDMA 簡訊 SUSE Linux Enterprise Server (SLES) 或 OpenLogic CentOS 型 HPC 影像部署 Vm。 下列服務商場圖像支援必要的 Linux RDMA 驅動程式︰

    * SLES 12 的 HPC，SLES 12 SP1 SP1 的 HPC （進階版）
    
    * SLES 12 的 HPC，SLES 12 的 HPC （進階版）
    
    * CentOS 型 7.1 HPC
    
    * CentOS 型 6.5 HPC
    
    >[AZURE.NOTE]H 數列 Vm 建議 [HPC 圖像的 SLES 12 SP1 或 CentOS 型 7.1 HPC 圖像。
    >
    >在 CentOS 型 HPC 圖像核心更新已停用**yum**設定檔中。 這是因為 Linux RDMA 驅動程式分配為轉速套件，並更新核心時，可能無法運作驅動程式的更新。

* **MPI** Intel MPI 文件庫 5.x

    服務商場圖像根據您選擇，另一個授權，安裝，或設定 Intel MPI 可能需要，如下所示︰ 
    
    * **SLES 12 SP1 HPC 影像**-安裝 Intel MPI 封包發佈 VM 執行下列命令︰
    
            sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm

    * **HPC 圖像的 SLES 12** -您必須下載並安裝 Intel MPI 分別註冊。 請參閱[Intel MPI 文件庫安裝指南](https://software.intel.com/sites/default/files/managed/7c/2c/intelmpi-2017-installguide-linux.pdf)。
    
    * 已安裝**centOS 型 HPC 圖像**Intel MPI 5.1。  

    需要其他系統設定執行叢集 Vm MPI 工作。 例如，叢集上的 Vm，您需要建立運算節點之間的信任。 一般設定]，請參閱[設定 Linux RDMA 叢集執行 MPI 應用程式](virtual-machines-linux-classic-rdma-cluster.md)。


## <a name="considerations-for-hpc-pack-and-linux"></a>HPC 套件與 Linux 的考量

[HPC 套件](https://technet.microsoft.com/library/jj899572.aspx)，Microsoft 的免費 HPC 叢集及工作管理解決方案，提供您使用 Linux 運算密集執行個體的其中一個選項。 HPC 套件 2012 R2 支援最新版本上執行的多個 Linux 散佈計算部署 Azure Vm，管理的 Windows Server 的標頭節點的節點。 執行 Intel MPI RDMA 簡訊 Linux 運算節點，HPC 套件可以排程與執行 Linux MPI 存取 RDMA 網路的應用程式。 若要開始，請參閱[開始使用 Linux 運算叢集的節點 HPC 套件 Azure 中](virtual-machines-linux-classic-hpcpack-cluster.md)。

## <a name="network-topology-considerations"></a>網路拓撲考量

* 在 RDMA 啟用 Linux Vm Azure 中 Eth1 保留 RDMA 網路流量。 不要變更任何 Eth1 設定] 或 [參照到這個網路的設定檔中的任何資訊。 Eth0 保留一般 Azure 網路流量。

* Azure 中不支援 IP 透過 InfiniBand (IB)。 僅透過 IB RDMA 功能受支援。

## <a name="rdma-driver-updates-for-sles-12"></a>SLES 12 RDMA 驅動程式的更新

建立 VM 根據 SLES 12 HPC 圖像之後，您可能需要更新的 RDMA 網路連線 Vm RDMA 驅動程式。 

>[AZURE.IMPORTANT]此步驟是**必要**的 SLES 12 HPC VM 部署所有 Azure 區域。 
>如果您部署 SLES 12 SP1 HPC、 CentOS 型 7.1 HPC 或 CentOS 型 6.5 HPC VM，不需要此步驟。 

更新驅動程式之前，請停止所有**zypper**處理程序或鎖定 SUSE repo 資料庫 VM 任何處理程序。 否則驅動程式可能無法正確更新。  

若要更新的每個 VM Linux RDMA 驅動程式，請從您的用戶端電腦執行 Azure CLI 命令下列其中一項。

**HPC VM SLES 12 佈建後，在傳統的部署模型**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**HPC VM SLES 12 佈建後，在資源管理員部署模型**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

>[AZURE.NOTE]可能需要一些時間才安裝驅動程式，並傳回不輸出的命令。 更新後、 您 VM 會重新啟動，而且應該可供使用，在幾分鐘。

### <a name="sample-script-for-driver-updates"></a>範例指令碼的驅動程式的更新

如果您有 HPC Vm SLES 12 叢集，您可以的指令碼的驅動程式更新所有節點叢集中。 例如，下列指令碼更新 8 節點叢集驅動程式。

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model, use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## <a name="next-steps"></a>後續步驟

* 如需可用性和價格的運算密集大小的詳細資訊，請參閱[虛擬機器價格](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux)。

* 儲存容量及磁碟詳細資料，請參閱[虛擬機器的大小](virtual-machines-linux-sizes.md)。

* 若要開始部署，並將 linux RDMA 運算密集大小，請參閱[設定 Linux RDMA 叢集執行 MPI 應用程式](virtual-machines-linux-classic-rdma-cluster.md)。


