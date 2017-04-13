<properties
    pageTitle="Windows 虛擬機器概觀 |Microsoft Azure"
    description="深入了解建立及管理 Windows Azure 虛擬機器。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/20/2016"
    ms.author="davidmu"/>

# <a name="overview-of-windows-virtual-machines-in-azure"></a>Windows Azure 虛擬機器的概觀

Azure 虛擬機器 (VM) 是[視需要，可調整計算資源](../app-service-web/choose-web-site-cloud-service-vm.md)的 Azure 提供數種類型。 一般而言，您選擇 VM，當您需要更多的控制運算環境提供其他選項。 本文可讓您瞭解您應該考慮建立 VM、 建立，及如何管理之前。

Azure VM 可讓您虛擬化的彈性，而不需購買及維護執行的實體硬體。 不過，您仍需要維護 VM 執行工作，例如設定與修補安裝在其執行軟體。

Azure 虛擬機器可以用於各種不同的方式。 以下是一些範例︰

- **開發和測試**– Azure Vm 提供的快速又簡單方式建立具有特定設定的電腦所需的程式碼，然後測試應用程式。
- **在雲端的應用程式**– 可以波動應用程式的需求，因為這可能合理經濟 Azure 中 VM 上執行。 您會支付額外 Vm，當您需要進行，其時關閉您不要。
- **擴充的資料中心**– Azure 虛擬網路中的虛擬機器可以輕鬆地連接到貴組織的網路。

Vm 應用程式使用數可以調整設定，並查看上述程序，才能符合您的需求。

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>我需要考慮建立 VM 之前什麼？

當您建立出 Azure 中的應用程式基礎結構時，一律是產生的[設計考量](virtual-machines-windows-infrastructure-virtual-machine-guidelines.md)。 這些層面 VM 重視您開始之前，先考量下列事項︰

- 您的應用程式資源的名稱
- 資源的儲存位置
- VM 的大小
- 您可以建立的 Vm 數目上限
- VM 執行的作業系統
- VM 開始後的設定
- 需要 VM 相關的資源

### <a name="naming"></a>命名

虛擬機器指派[名稱](virtual-machines-windows-infrastructure-naming-guidelines.md)各有其設定為 [組件的作業系統的電腦名稱。 VM 的名稱可最多 15 個字元。

如果您使用 Azure 建立作業系統磁碟時，電腦名稱和虛擬機器名稱都相同。 如果您[上傳並使用您自己的圖像](virtual-machines-windows-upload-image.md)包含先前設定的作業系統，並使用它來建立虛擬機器，名稱可以會不同。 我們建議您上傳您自己的圖像檔案，當您進行的電腦名稱作業系統中虛擬機器名稱相同。

### <a name="locations"></a>位置

Azure 中建立的所有資源都分散到多個世界各地的[地理區域](https://azure.microsoft.com/regions/)。 通常地區時建立 VM 呼叫**的位置**。 Vm，這個位置會指定虛擬硬碟的儲存位置。

下表顯示一些您可以取得可用的位置清單的方式。

| 方法 | 描述 |
| ------ | ----------- |
| Azure 入口網站 | 當您建立 VM 時，請從清單中選取一個位置。 |
| Azure PowerShell | 使用 [[取得 AzureRmLocation](https://msdn.microsoft.com/library/mt619449.aspx)命令。 |
| REST API | 使用[清單的位置](https://msdn.microsoft.com/library/dn790540.aspx)作業。 |

### <a name="vm-size"></a>虛擬記憶體大小

您使用的 VM[大小](virtual-machines-windows-sizes.md)取決於您想要執行的工作量。 然後您所選擇的大小會決定各種因素處理 power、 記憶體和儲存空間容量。 Azure 提供各種不同的支援多種類型的用途的大小。

Azure 費用根據 VM 的大小和作業系統[每小時的價格](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)。 部分小時，Azure 僅適用於使用通話分鐘方案的費用。 儲存為價格，而且會分別。

### <a name="vm-limits"></a>VM 限制

您的訂閱有預設[配額限制](../azure-subscription-service-limits.md)，可能會影響專案的許多 Vm 部署的位置。 每個訂閱基礎目前限制為每個地區 20 Vm。 可能引發限制，以納入要求增加的支援票證。

### <a name="operating-system-disks-and-images"></a>作業系統磁碟和圖像

虛擬機器儲存其作業系統 (OS) 和資料使用[虛擬硬碟 (Vhd)](virtual-machines-windows-about-disks-vhds.md) 。 Vhd 也會使用您可以選擇從安裝作業系統的影像。 

Azure 提供許多[服務商場圖像](https://azure.microsoft.com/marketplace/virtual-machines/)，若要使用不同版本和 Windows 伺服器作業系統的類型。 服務商場圖像會識別圖像 publisher、 優惠、 sku 及版本 （通常是為最新的指定版本）。 

下表顯示一些方法您可以找到圖像的資訊。

| 方法 | 描述 |
| ------ | ----------- |
| Azure 入口網站 | 會自動為您指定值，當您選取要使用的影像上。 |
| Azure PowerShell | [取得 AzureRMVMImagePublisher](https://msdn.microsoft.com/library/mt603484.aspx) -「 位置 」 的位置<BR>[取得 AzureRMVMImageOffer](https://msdn.microsoft.com/library/mt603824.aspx) -位置 」 位置 」-Publisher 「 publisherName 」<BR>[取得 AzureRMVMImageSku](https://msdn.microsoft.com/library/mt619458.aspx) -位置 」 位置 」-Publisher 「 publisherName 」-提供 「 offerName 」 |
| REST Api | [清單的圖像發行者](https://msdn.microsoft.com/library/mt743702.aspx)<BR>[提供清單的圖像](https://msdn.microsoft.com/library/mt743700.aspx)<BR>[清單圖像 sku](https://msdn.microsoft.com/library/mt743701.aspx) |

您可以選擇要[上傳並使用您自己的圖像](virtual-machines-windows-upload-image.md)和執行時，不使用 publisher 名稱、 提供的服務，以及 sku。

### <a name="extensions"></a>副檔名

VM[擴充功能](virtual-machines-windows-extensions-features.md)讓您透過文章部署設定和自動化的工作的 VM 其他功能。

這些一般工作，可以使用副檔名來完成︰

- **執行自訂指令碼**–[自訂指令碼延伸模組](virtual-machines-windows-extensions-customscript.md)，可協助您設定 VM 負載時 VM 也會佈建後，請執行指令碼。
- **部署及管理設定**– [PowerShell 所需的狀態設定 (DSC) 副檔名](virtual-machines-windows-extensions-dsc-overview.md)可協助您管理設定和環境設定 DSC VM 上。
- **收集診斷資料**– [Azure 診斷副檔名](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/)，可協助您設定的 VM 收集可以用來監控您的應用程式的健康情況的診斷資料。

### <a name="related-resources"></a>相關的資源

此表格中的資源 VM，可使用，而且需要存在於，或建立 VM 時建立。

| 資源 | 所需 | 描述 |
| -------- | -------- | ----------- |
| [資源群組](../azure-resource-manager/resource-group-overview.md) | [是] | 資源群組中，就必須包含 VM。 |
| [儲存帳戶](../storage/storage-create-storage-account.md) | [是] | VM 必須將其虛擬硬碟的儲存空間帳戶。 |
| [虛擬網路](../virtual-network/virtual-networks-overview.md) | [是] | VM 必須虛擬網路的成員。 |
| [公用 IP 位址](../virtual-network/virtual-network-ip-addresses-overview-arm.md) | 無 | VM 可以有獲得指派遠端存取的公用 IP 位址。 |
| [網路介面](../virtual-network/virtual-network-network-interface-overview.md) | [是] | VM 必須通訊網路中的網路介面。 |
| [資料磁碟](virtual-machines-windows-attach-disk-portal.md) | 無 | VM 可以包含資料磁碟以展開儲存功能。 |

## <a name="how-do-i-create-my-first-vm"></a>如何建立我的第一個 VM？

您有幾個選擇建立您 VM。 您所做的選擇取決於您是在環境。 

下表提供資訊協助您開始建立您 VM。

| 方法 | 文件 |
| ------ | ------- |
| Azure 入口網站 | [建立虛擬機器執行 Windows 使用入口網站](virtual-machines-windows-hero-tutorial.md) |
| 範本 | [資源管理員從範本建立 Windows 虛擬機器](virtual-machines-windows-ps-template.md) |
| Azure PowerShell | [建立使用 PowerShell 的 Windows VM](virtual-machines-windows-ps-create.md) |
| 用戶端 Sdk | [部署使用 C# Azure 資源](virtual-machines-windows-csharp.md) |
| REST Api | [建立或更新 VM](https://msdn.microsoft.com/library/mt163591.aspx) |

您希望並未發生，但是偶爾發生錯誤。 如果您發生這種狀況，請查看[疑難排解資源管理員部署問題建立 Windows 中 Azure 虛擬機器](virtual-machines-windows-troubleshoot-deployment-new-vm.md)中的資訊。

## <a name="how-do-i-manage-the-vm-that-i-created"></a>我要如何管理我在自己建立的 VM？

可以使用瀏覽器型入口網站中，使用支援的指令碼，或直接透過 Api 的命令列工具來管理 Vm。 您可能需要執行一些一般管理工作會取得登入 VM，管理的顯示狀態，及製作備份 VM 的相關資訊。

### <a name="get-information-about-a-vm"></a>取得 VM 的相關資訊

下表顯示您一些您可以取得 VM 的相關資訊的方式。

| 方法 | 描述 |
| ------ | ----------- |
| Azure 入口網站 | 在中樞] 功能表中，按一下**虛擬機器**，然後從清單中選取 [VM。 在 vm 刀，您有概觀資訊的存取權，設定值，並監控指標。 |
| Azure PowerShell | 有關如何使用 PowerShell 來管理 Vm，請參閱[管理 Azure 虛擬機器使用資源管理員與 PowerShell](virtual-machines-windows-ps-manage.md)。 |
| REST API | 您可以使用 [[取得 VM 資訊](https://msdn.microsoft.com/library/mt163682.aspx)操作，取得 VM 的相關資訊。 |
| 用戶端 Sdk | 使用 C# 管理 Vm 相關資訊，請參閱[管理 Azure 虛擬機器使用 Azure 資源管理員及 C#](virtual-machines-windows-csharp-manage.md)。 |

### <a name="log-on-to-the-vm"></a>登入 VM

您可以使用 [連線] 按鈕啟動[遠端桌面 (RDP) 工作階段](virtual-machines-windows-connect-logon.md)Azure 入口網站中。 嘗試使用遠端連線時，有可能有時發生錯誤。 如果您發生這種狀況，請參閱[疑難排解遠端桌面連線執行 Windows Azure 虛擬機器](virtual-machines-windows-troubleshoot-rdp-connection.md)中的 [說明] 資訊。

### <a name="manage-availability"></a>管理可用性

請務必為您瞭解如何[確保高可用性](virtual-machines-windows-manage-availability.md)應用程式。 此設定會包括建立多個 Vm，以確保至少一個已執行。

為了讓您的部署合格我們 99.95 VM 服務等級協定，您需要部署兩個或多個 Vm 執行您的工作量內[可用性設定](virtual-machines-windows-infrastructure-availability-sets-guidelines.md)。 此設定可確保您的 Vm 分散到多個錯誤網域，並已部署至不同的維護 windows 主機。 完整的[Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/)說明保證可使用 Azure 整個。
 
### <a name="back-up-the-vm"></a>備份 VM
 
[修復服務保存庫](../backup/backup-introduction-to-azure-backup.md)用來保護資料和 Azure 備份和 Azure 網站復原服務中的資產。 您可以使用復原服務保存庫[部署](../backup/backup-azure-vms-automation.md)及管理資源管理員部署 Vm 使用 PowerShell 的備份。 

## <a name="next-steps"></a>後續步驟

- 如果您想来使用 Linux Vm，查看[Azure 及 Linux](virtual-machines-linux-azure-overview.md)。
- 進一步瞭解周圍設定[範例 Azure 基礎結構的逐步解說](virtual-machines-windows-infrastructure-example.md)中基礎結構的指導方針。
- 請確定您所追蹤[的執行 Windows VM 上 Azure 最佳作法](virtual-machines-windows-guidance-compute-single-vm.md)。


