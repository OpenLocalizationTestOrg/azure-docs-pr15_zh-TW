<properties
 pageTitle="在雲端的 Linux HPC 套件叢集選項 |Microsoft Azure"
 description="瞭解 Microsoft HPC 套件，以建立及管理 Linux 高效能運算 (HPC) 叢集 Azure 雲端中的選項"
 services="virtual-machines-linux,cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="09/26/2016"
 ms.author="danlep"/>

# <a name="options-with-hpc-pack-to-create-and-manage-an-hpc-cluster-in-azure-for-linux-workloads"></a>建立及管理 Linux 負載 Azure 中的 HPC 叢集 HPC pack 選項

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

本文著重於用 HPC 套件 Linux 工作負載的選項。 還有執行[HPC pack 的 Windows HPC 工作負載](virtual-machines-windows-hpcpack-cluster-options.md)的選項。

## <a name="run-an-hpc-pack-cluster-in-azure-vms"></a>Azure Vm 中執行 HPC 套件叢集

### <a name="azure-templates"></a>Azure 範本


* (Marketplace)[HPC 套件叢集 Linux 負載](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)

* （快速入門）[建立與 Linux HPC 叢集運算節點](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)


### <a name="powershell-deployment-script"></a>PowerShell 部署指令碼

* [建立 Linux HPC 叢集 HPC 套件 IaaS 部署指令碼](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)

### <a name="tutorials"></a>教學課程

* [教學課程︰ 開始使用 Linux 運算叢集的節點 HPC 套件 Azure 中](virtual-machines-linux-classic-hpcpack-cluster.md)

* [教學課程︰ 執行 Microsoft HPC pack NAMD linux 運算 Azure 中的節點](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [教學課程︰ 執行 OpenFOAM Linux RDMA 叢集 Azure 中的 Microsoft HPC pack](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [教學課程︰ 執行星星-CCM + Microsoft HPC pack 上 Linux RDMA 叢集 Azure 中](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)

### <a name="cluster-management"></a>叢集管理

* [提交到 HPC 套件叢集 Azure 中的工作](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)

* [工作管理 HPC 套件](https://technet.microsoft.com/library/jj899585.aspx)


## <a name="create-rdma-clusters-for-mpi-workloads"></a>建立 RDMA 叢集 MPI 負載

* [教學課程︰ 執行 OpenFOAM Linux RDMA 叢集 Azure 中的 Microsoft HPC pack](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [設定 Linux RDMA 叢集執行 MPI 應用程式](virtual-machines-linux-classic-rdma-cluster.md)

