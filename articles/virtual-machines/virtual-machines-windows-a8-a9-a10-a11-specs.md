<properties
 pageTitle="關於與 Windows 運算密集 Vm |Microsoft Azure"
 description="取得背景資訊和 Windows Vm 和雲端服務中使用 Azure H 數列與 A8、 A9、 A10 和 A11 運算密集大小的考量"
 services="virtual-machines-windows, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>關於 H 數列和運算密集的數列 Vm

以下是背景資訊和使用新版 Azure H 數列和舊版 A8、 A9、 A10 及 A11 執行個體，也就是*運算密集*執行個體的一些考量。 本文著重於 Windows Vm 使用這些執行個體。 本文也適用於[Linux Vm](virtual-machines-linux-a8-a9-a10-a11-specs.md)。


[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>RDMA 網路存取權

您可以建立的 RDMA 簡訊 Windows Server 執行個體叢集並部署支援 MPI 實作利用 Azure RDMA 網路之一。 僅限 MPI 流量保留此低延遲，高處理量的網路。

* **作業系統**
    * **虛擬機器**-Windows Server 2012 R2，Windows Server 2012
    * **雲端服務**的 Windows Server 2012 R2、 Windows Server 2012、 Windows Server 2008 R2 來賓 OS 家族

* **MPI** Microsoft MPI (MS-MPI) 2012 R2 或更新版本、 Intel MPI 文件庫 5.x

支援的 MPI 實作使用 Microsoft 網路直接介面之間執行個體進行通訊。 請參閱[設定 Windows RDMA 叢集 HPC pack 執行 MPI 應用程式](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)和[使用多個執行個體執行的工作 Azure 批次中的郵件傳遞介面 (MPI) 應用程式](../batch/batch-mpi.md)的部署選項和範例設定步驟。


>[AZURE.NOTE]在 RDMA 簡訊運算密集 Vm HpcVmDrivers 副檔名必須會新增至安裝 Windows 網路的裝置驅動程式所需的 RDMA 連線 Vm。 大部分的部署，會自動新增 HpcVmDrivers 副檔名。 如果您需要自行新增擴充功能，請參閱[管理 VM 副檔名](virtual-machines-windows-classic-manage-extensions.md)。

## <a name="considerations-for-hpc-pack-and-windows"></a>HPC 套件與 Windows 的考量

[Microsoft HPC 套件](https://technet.microsoft.com/library/jj899572.aspx)，Microsoft 的免費 HPC 叢集及工作管理解決方案，則不需要您使用 Windows Server 的運算密集執行個體。 不過，則您建立叢集 Azure 中執行 Windows 型 MPI 應用程式與其他 HPC 工作負載的其中一個選項。 HPC 套件 2012 R2 及更新版本包含 MS MPI 可使用 RDMA 簡訊 Vm Azure RDMA 網路部署時執行階段環境。

如需的資訊與 HPC pack 在 Windows Server 上使用運算密集執行個體的檢查清單，請參閱[設定 Windows RDMA 叢集 HPC pack 執行 MPI 應用程式](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)。




## <a name="next-steps"></a>後續步驟

* 如需可用性和價格運算密集大小的詳細資訊，請參閱[虛擬機器定價](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)和[雲端服務價格](https://azure.microsoft.com/pricing/details/cloud-services/)。

* 儲存容量及磁碟詳細資料，請參閱[虛擬機器的大小](virtual-machines-linux-sizes.md)。

* 若要開始部署及 HPC pack Windows 上使用運算密集執行個體，請參閱[設定 Windows RDMA 叢集 HPC pack 執行 MPI 應用程式](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)。

* 瞭解如何使用 A8 和 A9 執行個體 Azure 批次執行 MPI 應用程式的資訊，請參閱[使用多個執行個體執行的工作 Azure 批次中的郵件傳遞介面 (MPI) 應用程式](../batch/batch-mpi.md)。
