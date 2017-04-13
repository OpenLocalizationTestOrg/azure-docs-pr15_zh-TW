<properties
    pageTitle="SQL Server Vm 儲存設定 |Microsoft Azure"
    description="本主題說明如何 Azure 設定的 SQL Server Vm 期間佈建 （資源管理員部署模型） 儲存空間。 同時也會說明如何設定儲存為您現有的 SQL Server Vm。"
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="ninarn"
    manager="jhubbard"    
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/04/2016"
    ms.author="ninarn" />

# <a name="storage-configuration-for-sql-server-vms"></a>SQL Server Vm 儲存設定

當您設定的 SQL Server 虛擬機器圖像 Azure 中時，入口網站可協助自動化存放裝置設定。 這包含附加的 vm，讓該儲存存取 SQL Server，並將其設定若要最佳化以特定的效能需求的儲存空間。

本主題說明如何 Azure 設定儲存為您的 SQL Server Vm 期間佈建與現有 Vm。 此設定會根據執行 SQL Server Azure Vm[效能的最佳作法](virtual-machines-windows-sql-performance.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統的部署模型。

## <a name="prerequisites"></a>必要條件
若要使用 [自動的儲存設定的設定，您的虛擬機器需要下列特性︰

- 佈建[SQL Server 庫](virtual-machines-windows-sql-server-iaas-overview.md#option-1-deploy-a-sql-vm-per-minute-licensing)。
- 使用[資源管理員部署模型](../resource-manager-deployment-model.md)。
- 使用[進階版儲存空間](../storage/storage-premium-storage.md)。

## <a name="new-vms"></a>新的 Vm
下列各節說明如何設定新的 SQL Server 虛擬機器的儲存空間。

### <a name="azure-portal"></a>Azure 入口網站
佈建時使用的 SQL Server 庫圖像 Azure VM，您可以選擇自動設定您的新 VM 儲存空間。 您指定的儲存空間大小、 效能限制及工作負載類型。 以下螢幕擷取畫面顯示 SQL VM 時所用的儲存空間設定刀佈建。

![SQL Server VM 儲存設定期間佈建](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

根據您的選擇，Azure 之後，執行下列的儲存空間設定工作建立 VM:

- 建立並將進階版存放資料磁碟附加至虛擬機器。
- 設定存取 SQL Server 資料磁碟。
- 設定資料磁碟成儲存集區根據指定大小和效能 （IOPS 和處理量） 需求。
- 關聯新的磁碟機上虛擬機器中的儲存集區。
- 最佳化此新的磁碟機，根據您指定的工作負載類型 （資料倉儲交易處理，或一般）。

如需詳細資訊 Azure 如何設定儲存設定，請參閱[儲存組態] 區段中](#storage-configuration)。 如何建立 SQL Server VM Azure 入口網站中，請參閱[佈建教學課程](virtual-machines-windows-portal-sql-server-provision.md)的完整的逐步解說。

### <a name="resource-manage-templates"></a>資源管理範本
如果您使用下列資源管理員範本，根據預設，沒有儲存集區設定附加兩個進階資料磁碟。 不過，您可以自訂這些範本，變更進階資料磁碟附加至虛擬機器的數目。

- [使用自動備份建立 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
- [使用自動修正建立 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
- [建立 VM AKV 整合](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>現有 Vm
對於現有的 SQL Server Vm，您可以修改 Azure 入口網站中的部分儲存設定。 選取您 VM，請移至 [設定] 區域，然後選取 SQL Server 組態]。 SQL Server 組態刀會顯示您 VM 目前儲存空間使用方式。 上所有磁碟機存在於您 VM 會顯示在此圖表中。 每個磁碟機，儲存空間會顯示四個區段中︰

- SQL 資料
- SQL 記錄檔
- 其他 （非 SQL 儲存區）
- 使用

![針對現有的 SQL Server VM 設定儲存空間](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

若要設定的儲存空間新增新的磁碟機或擴充現有的磁碟機，請按一下圖表上方的 [編輯] 連結。

設定所看到的選項而有所不同是否有使用之前這項功能。 當第一次使用時，您可以指定新的磁碟機您儲存需求。 如果您先前建立的磁碟機中使用此功能，您可以選擇擴充的磁碟機的儲存空間。

### <a name="use-for-the-first-time"></a>第一次使用
如果這是您第一次使用這項功能，您可以指定新的磁碟機的儲存空間大小和效能限制。 此體驗方式類似於您想要查看佈建時間。 主要差異是您不允許指定的工作量類型。 這項限制可防止干擾任何現有的 SQL Server 組態虛擬機器上。

![設定 SQL Server 儲存滑桿](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-usage-sliders.png)

Azure 會建立新的磁碟機，根據您的規格。 在此案例中，Azure 會執行下列的儲存空間設定工作︰

- 建立並將進階版存放資料磁碟附加至虛擬機器。
- 設定存取 SQL Server 資料磁碟。
- 設定資料磁碟成儲存集區根據指定大小和效能 （IOPS 和處理量） 需求。
- 關聯新的磁碟機上虛擬機器中的儲存集區。

如需詳細資訊 Azure 如何設定儲存設定，請參閱[儲存組態] 區段中](#storage-configuration)。

### <a name="add-a-new-drive"></a>新增新的磁碟機
如果您已設定儲存在您的 SQL Server VM，展開儲存會顯示兩個新的選項。 第一個選項是新增新的磁碟機，可以增加您 VM 的效能層級。

![SQL vm 新增新的磁碟機](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-add-new-drive.png)

不過，如果您新增後的磁碟機，您必須執行達成效能提升一些額外手動設定。

### <a name="extend-the-drive"></a>延伸磁碟機
其他選項展開儲存空間是延伸現有的磁碟機。 這個選項會增加可用儲存空間的磁碟機，但不會增加效能。 使用儲存集區，您無法在儲存集區建立之後，變更欄的數。 欄數會決定平行寫入，在資料的磁碟等量的處理的數目。 因此，任何新增的資料的磁碟無法提升效能。 他們只能寫入的資料提供更多儲存空間。 這項限制也就是說，當延伸磁碟機的欄數會決定您可以新增的資料磁碟的最小數目。 如果您有四個資料磁碟建立儲存集區的欄數同時也是四。 隨時在您擴充儲存空間，您必須新增至少四個資料磁碟。

![SQL vm 延伸的磁碟機](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>存放裝置設定
本節提供 Azure 會自動執行 SQL VM 佈建或 Azure 入口網站中的設定期間的儲存空間設定變更的參照。

- 如果您選取了兩個以上的儲存空間的 TBs 您 VM，Azure 並不會建立儲存集區。
- 如果您選取了兩個以上的儲存空間的 TBs 您 VM，Azure 設定儲存集區。 下一節，本主題提供儲存集區設定的詳細資料。
- 自動儲存設定一律使用[進階版儲存](../storage/storage-premium-storage.md)P30 資料磁碟。 因此，有 1:1 之間的對應 Tb 您所選的數] 及 [資料磁碟連接到您 VM 數目。

如價格資訊，請參閱**磁碟儲存空間**] 索引標籤上的[儲存空間價格](https://azure.microsoft.com/pricing/details/storage)] 頁面。

### <a name="creation-of-the-storage-pool"></a>建立的儲存集區
Azure SQL Server Vm 上建立儲存集區使用下列設定。

| 設定 | 值 |
|-----|-----|
| 等量大小  | 256 KB （資料倉儲）;64 KB （交易式） |
| 磁碟大小 | 1 TB |
| 快取 | 已讀取 |
| 配置的大小 | 64 KB NTFS 配置單位大小 |
| 立即檔案初始化 | 啟用 |
| 鎖定記憶體分頁 | 啟用 |
| 復原 | 簡單的復原 （沒有恢復功能） |
| 欄數 | 數字的資料磁碟<sup>1</sup> |
| TempDB 位置 | 儲存在資料磁碟<sup>2</sup> |

<sup>1</sup>的儲存集區建立之後，您無法變更儲存集區中的欄的數。

<sup>2</sup>僅適用於您使用的儲存空間設定功能建立的第一個磁碟機這項設定。

## <a name="workload-optimization-settings"></a>工作負載最佳化設定
下表說明三個工作負載類型的可用選項和其對應最佳化︰

| 工作負載類型 | 描述 | 最佳化 |
|-----|-----|-----|
| **一般** | 支援大部分的工作負載的預設設定 | 無 |
| **交易處理** | 最佳化傳統資料庫 OLTP 工作負載的儲存空間 | 追蹤旗標 1117<br/>追蹤旗標 1118 |
| **資料倉儲** | 最佳化分析與報告工作負載的儲存空間 | 追蹤旗標 610<br/>追蹤旗標 1117 |

>[AZURE.NOTE] 當您提供 SQL 虛擬機器中的儲存空間設定步驟選取時，您可以只指定工作負載類型。

## <a name="next-steps"></a>後續步驟
執行 SQL Server Azure Vm 中其他主題，請參閱[SQL Server Azure 虛擬機器上的商務連絡人](virtual-machines-windows-sql-server-iaas-overview.md)。
