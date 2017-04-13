<properties
    pageTitle="最佳化上 Azure 您 Linux VM |Microsoft Azure"
    description="進一步瞭解，請確定您已設定您的 Linux VM 上 Azure 最佳效能最佳化秘訣"
    keywords="linux 虛擬機器，虛擬機器 linux ubuntu 虛擬機器" 
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rickstercdn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="rclaus"/>

# <a name="optimize-your-linux-vm-on-azure"></a>最佳化您的 Linux VM Azure 上

建立 Linux 虛擬機器 (VM) 可以輕鬆從命令列，或從入口網站執行的動作。 本教學課程教您如何確保您已將其設定為將 Microsoft Azure 平台上的效能最佳化。 本主題會使用 Ubuntu 伺服器 VM，但您也可以建立 Linux 虛擬機器使用[自己的圖像做為範本](virtual-machines-linux-create-upload-generic.md)。  

## <a name="prerequisites"></a>必要條件

本主題假設您已經有可用的[安裝 Azure CLI](../xplat-cli-install.md) Azure 訂閱 （[免費試用版註冊](https://azure.microsoft.com/pricing/free-trial/)），並已經有提供 VM 到 Azure 訂閱。 在任何與 Azure-這麼做之前，您必須驗證您的訂閱。 若要使用 Azure CLI 這麼做，只要輸入`azure login`開始互動式的程序。 

## <a name="azure-os-disk"></a>Azure OS 磁碟

一旦您建立 Linux Vm Azure 中時，它會有兩個與其相關聯的磁碟。 /dev/sda 是 OS 磁碟，/dev/sdb 會暫時磁碟。  請勿使用主 OS 磁碟 (/ 開發/sda) 以外的作業系統的項目最適合快速 VM 啟動時，不會提供您的工作負載的效能。 您想要將一或多個磁碟附加至取得常設您 VM 和最佳化您的資料的儲存空間。 

## <a name="adding-disks-for-size-and-performance-targets"></a>新增磁碟的大小和效能的目標 

根據虛擬記憶體大小，您可以附加到 16 的數列，D 系列 32 磁碟上的其他磁碟和 G 數列上的 64 磁碟機器-每個 1 TB 的大小。 視需要每個您的空間和 IOps 需求，您可以新增額外的磁碟。 每個磁碟有效能的目標 500 IOps 標準的儲存空間，以及每個磁碟 5000 IOps 最高級儲存空間。  如需進階版儲存空間的磁碟的詳細資訊，請參閱[進階版儲存空間︰ Azure Vm 高效能儲存空間](../storage/storage-premium-storage.md)

若要達成其快取設定有設定 「 唯讀 」 或 「 無 」 的進階版儲存磁碟上的最高 IOps，您必須時裝載 Linux 檔案系統中停用 「 障礙 」。 您不需要障礙因為進階版儲存備份磁碟寫入長期這些快取設定。

- 如果您是使用**reiserFS**，停用障礙使用裝載選項 」 障礙 = 無 」 (啟用障礙，使用 「 障礙 = 靠 」)
- 如果您是使用**ext3/ext4**，停用障礙使用裝載選項 」 障礙 = 0 」 (啟用障礙，使用 「 障礙 = 1")
- 如果您是使用**XFS**，停用障礙使用裝載選項 」 nobarrier 」 （啟用障礙，使用選項] 障礙 」）

## <a name="storage-account-considerations"></a>儲存帳戶的考量事項

當您建立您 Linux VM Azure 中時，請確定您從儲存帳戶位於相同的區域，為您 VM，以確保接近且最小化網路延遲附加磁碟。  每個標準儲存帳戶具有最大值 20 k IOps 和 500 TB 大小容量。  這適約 40 個常用的磁碟包括 OS 磁碟與您所建立的任何資料磁碟。 進階版儲存帳戶沒有最大 IOps 限制，但有 32 TB 大小限制。 

當處理最高 IOps 負載，以及您選擇標準的儲存空間的磁碟時，您可能需要將磁碟分割跨多個儲存帳戶，請確定您不叫用 20000 IOps 限制標準儲存帳戶。 您 VM 可以包含從磁碟多種不同的儲存帳戶和儲存的帳戶類型，以獲得最佳的設定。 

## <a name="your-vm-temporary-drive"></a>VM 暫存磁碟機

依預設您建立 VM，Azure 提供 OS 磁碟 (/ 開發/sda) 與暫存的磁碟 (/ 開發/sdb)。  您新增的所有其他磁碟顯示為 /dev/sdc /dev/sdd、 /dev/sde 等等。 您暫時磁碟 (/ 開發/sdb) 上的所有資料長期，並不如果特定事件等 VM 調整大小、 重新部署，或進行的維修作業強制重新啟動您 VM 可能會遺失。  大小和暫時磁碟類型被與您選擇在部署階段虛擬記憶體大小。 若是任何進階版大小 Vm （DS、 G 和 DS_V2 數列） 的暫存的磁碟機所支援的其他效能最 48 k 本機 SSD IOps。 

## <a name="linux-swap-file"></a>Linux 交換檔案

如果您 Azure VM 來自 Ubuntu 或 CoreOS 圖像，然後您可以使用 CustomData 雲端初始化傳送雲端設定。 如果您[上傳自訂 Linux 影像](virtual-machines-linux-upload-vhd.md)使用雲端初始化，您也設定交換磁碟分割區使用雲端初始化。

Ubuntu 雲端影像，您必須使用雲端初始化設定交換磁碟分割。 Ubuntu wiki，如需詳細資訊請參閱下列頁面︰ [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions)。

從 Azure Marketplace 部署 VM 圖像沒有雲端初始化支援的圖像，有與 OS 整合 VM Linux 代理程式。 此代理程式可讓 VM 與各種 Azure 服務互動。 假設您已經部署從 Azure Marketplace 的標準圖像，您需要執行下列動作，正確設定 Linux 交換檔案設定︰

尋找並修改**/etc/waagent.conf**檔案中的兩個項目。 他們控制專用的交換檔案存在及交換檔案的大小。 想要修改的參數是`ResourceDisk.EnableSwap=N`和`ResourceDisk.SwapSizeMB=0` 

您需要下列變更︰

* ResourceDisk.EnableSwap=Y
* 以符合您需求的 mb ResourceDisk.SwapSizeMB={size} 

一旦您所做的變更，您必須重新啟動 waagent 或重新啟動您 Linux VM，以反映那些變更。  您知道已實作所做的變更，但當您使用已經建立交換檔案`free`] 命令，檢視可用空間。 下列範例會有當做修改 waagent.conf 檔案所建立的 512 MB 交換檔案。

    admin@mylinuxvm:~$ free
                total       used       free     shared    buffers     cached
    Mem:       3525156     804168    2720988        408       8428     633192
    -/+ buffers/cache:     162548    3362608
    Swap:       524284          0     524284
    admin@mylinuxvm:~$
 
## <a name="io-scheduling-algorithm-for-premium-storage"></a>進階版儲存空間的 I/O 排程演算法

使用 2.6.18 Linux 核心 I/O 排程演算法從期限變更 CFQ （完全展覽佇列演算法） 的預設值。 隨機存取 I/O 模式，有明顯差異 CFQ 與期限的效能差異。  為其中的磁碟 I/O 模式是主要連續 SSD 為基礎的磁碟，切換回 NOOP 或期限演算法可以獲得更佳的 I/O 效能。

### <a name="view-the-current-io-scheduler"></a>檢視目前 I/O 排程器

使用下列命令︰  

    admin@mylinuxvm:~# cat /sys/block/sda/queue/scheduler

您會看到下列輸出，亦即指出目前排程器。  

    noop [deadline] cfq

###<a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>變更目前的裝置 (/ 開發/sda) 的排程演算法 I/O

使用下列命令︰  

    azureuser@mylinuxvm:~$ sudo su -
    root@mylinuxvm:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mylinuxvm:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mylinuxvm:~# update-grub

>[AZURE.NOTE] 設定此 /dev/sda 了解單獨使用不是非常有用。 需要上設定的所有資料磁碟位置循序 I/O 勝於 I/O 圖樣。  

您應該會看到下列輸出指出該 grub.cfg 重建成功與 NOOP 的更新預設排程器。  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Redhat 分配系列，您只需要下列命令︰   

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## <a name="using-software-raid-to-achieve-higher-iops"></a>使用以獲得更高我的軟體 RAID / 選項

如果您的工作量需要更多 IOps 非單一磁碟可以提供，您需要使用多個磁碟的軟體 RAID 設定。 Azure 已執行磁碟恢復本機布料的轉印圖樣層級，因為您達到最高層級的效能從 raid-0 等量設定。  您需要佈建和 Azure 環境中建立新的磁碟分割、 格式設定及裝載磁碟機之前您 Linux vm 中附加。  在文件中**[設定軟體襲擊 Linux](virtual-machines-linux-configure-raid.md)**找設定軟體 RAID 安裝在您的 Linux VM azure 中的更多詳細資料。


## <a name="next-steps"></a>後續步驟

請記住，當所有最佳化討論區時，您都需要執行測量的影響，變更將會每項變更前後的測試。  最佳化是會有不同的結果，您的環境中不同的電腦上的逐步程序。  其他人可能無法正常運作的設定。

其他資源一些實用的連結︰ 

- [Azure 虛擬機器工作負載的進階版儲存空間︰ 高效能儲存空間](../storage/storage-premium-storage.md)
- [Azure Linux 代理程式使用者指南](virtual-machines-linux-agent-user-guide.md)
- [最佳化 Azure Linux Vm MySQL 效能](virtual-machines-linux-classic-optimize-mysql.md)
- [設定軟體襲擊 Linux](virtual-machines-linux-configure-raid.md)
