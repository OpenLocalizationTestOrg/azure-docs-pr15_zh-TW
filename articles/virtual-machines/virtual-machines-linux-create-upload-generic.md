<properties
    pageTitle="建立和上傳 Linux VHD Azure 中"
    description="瞭解如何建立和上傳 Azure 虛擬硬碟 (VHD)，包含 Linux 的作業系統。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="szark"/>

# <a name="information-for-non-endorsed-distributions"></a>非背書散佈的資訊 #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


**重要事項**︰ 虛擬機器執行 Linux OS，其中的[背書分配，](virtual-machines-linux-endorsed-distros.md)使用時，只適用於 Azure 平台 SLA。 Azure 圖像庫中所提供的所有 Linux 散發都是背書分配，使用所需的設定。

- [在 Azure-Linux 背書散佈](virtual-machines-linux-endorsed-distros.md)
- [Microsoft Azure 中的 Linux 圖像的支援](https://support.microsoft.com/kb/2941892)

Azure 上執行的所有散發都必須符合數目有機會正確的平台上執行的先決條件。  本文是不是全面涵蓋所有內容是不同的; 每個分配然後，很可能，即使您符合下列所有條件您仍需要大幅調整您的 Linux 系統，以確保其正確執行平台上。

因此我們建議您開始使用我們[在 Azure 背書散佈 Linux](virtual-machines-linux-endorsed-distros.md)時可能是。 下列文章會引導您如何準備 Azure 都支援各種 endorsed 的 Linux 散佈︰

- **[CentOS 型分配](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[紅色的角色企業 Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES 與 openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**

本文的其餘部分將著重於 Azure 上執行您 Linux 通訊群組的一般指導方針。


## <a name="general-linux-installation-notes"></a>一般 Linux 安裝備忘稿 ##

- Azure，只有 [**固定 VHD**中不支援 VHDX 格式。  您可以將磁碟轉換成 VHD 格式使用 HYPER-V 管理員或轉換 vhd 指令程式。 如果您使用的 VirtualBox 代表選取**固定的大小**，而不動態配置建立磁碟時的預設值。

- 安裝 Linux 系統時，*建議*您在使用標準的磁碟分割區，而不是 LVM （通常許多安裝的預設值）。 特別是如果 OS 磁碟需要附加至另一個相同 VM 進行疑難排解，這樣就可避免複製 Vm LVM 名稱衝突。 [LVM](virtual-machines-linux-configure-lvm.md)或[RAID](virtual-machines-linux-configure-raid.md)可用於資料磁碟上。

- 需要裝載 UDF 檔案系統的核心支援。 在第一次開機上 Azure 佈建設定會傳遞給 Linux VM 透過來賓附加 UDF 格式化媒體。 您必須能夠裝載 UDF 檔案系統設定讀佈建 VM Azure Linux 代理程式。

- 下方 2.6.37 Linux 核心版本不支援 NUMA 上 HYPER-V 使用較大的 VM 大小。 這個問題主要影響較舊的分配，使用上游紅色角色 2.6.32 核心和已固定在 RHEL 6.6 (核心-2.6.32-504)。 系統非 2.6.32-504 必須設定開機參數執行自訂核心早於 2.6.37 或較舊的 [RHEL 型核心`numa=off`核心 grub.conf 中的命令列上。 如需詳細資訊，請參閱紅色角色[KB 436883](https://access.redhat.com/solutions/436883)。

- 不要 OS 磁碟上設定交換磁碟分割。 若要建立暫存資源磁碟上的交換檔案，可以設定 Linux 代理程式。  下列步驟中，可以找到相關資訊。

- 所有 Vhd 必須是 1 mb 的多重圖表的大小。


### <a name="installing-linux-without-hyper-v"></a>安裝 Linux 不 HYPER-V ###

在某些情況下，Linux 安裝程式可能會併入驅動程式的 HYPER-V （initrd 或 initramfs） 初始 ramdisk 除非偵測到它正在執行 HYPER-V 環境。  時使用不同的虛擬化系統 （亦即 Virtualbox、 KVM 等） 來準備您 Linux 圖像，您可能必須重建，確定 initrd 至少`hv_vmbus`和`hv_storvsc`核心模組中可以使用初始 ramdisk。  這至少根據上游紅色角色分配系統是已知的問題。

重建 initrd 或 initramfs 圖像的機制可能會有所不同分配。 請參閱累加分配的文件或支援適當的程序。  以下是如何重建 initrd 使用其中一個範例`mkinitrd`公用程式︰

請先備份現有的 initrd 圖像︰

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

接下來，重建與 initrd`hv_vmbus`和`hv_storvsc`核心模組︰

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>調整大小的 Vhd ###

Azure VHD 影像必須對齊到 1 MB 的虛擬大小。  一般而言，建立使用 HYPER-V Vhd 應該已正確對齊。  如果未正確對齊 VHD 您可能會收到錯誤訊息類似下列當您嘗試從您 VHD 建立*圖像*︰

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

若要解決這個問題，您可以調整 VM 使用 HYPER-V 管理員主控台或[調整大小 VHD](http://technet.microsoft.com/library/hh848535.aspx) Powershell 指令程式。  如果您不執行 Windows 環境中然後則建議使用 qemu 影像轉換 （如有需要），並重新調整 VHD。

> [AZURE.NOTE] Qemu 影像版本中有已知的錯誤 > = 2.2.1 會產生不正確的格式 VHD。 已經在 QEMU 2.6 修正問題。 建議使用 qemu 影像 2.2.0 或較低，或更新 2.6 或更高。 參照︰ https://bugs.launchpad.net/qemu/+bug/1490611。


 1. 調整大小直接使用工具，例如 VHD`qemu-img`或`vbox-manage`可能會導致無法啟動 VHD。  因此，建議第一個轉換原始磁碟映像 VHD。  如果已建立 VM 圖像以原始磁碟圖像 （例如 KVM 一些 Hypervisors 的預設值） 然後您可能會略過此步驟︰

        # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

 2. 計算磁碟映像，以確保虛擬大小會對齊到 1 MB 必要的大小。  這可以協助下列艦隊命令介面指令碼。  指令碼會使用 「`qemu-img info`」 來判斷虛擬磁碟映像的大小，然後計算到下一個 1 MB 大小︰

        rawdisk="MyLinuxVM.raw"
        vhddisk="MyLinuxVM.vhd"

        MB=$((1024*1024))
        size=$(qemu-img info -f raw --output json "$rawdisk" | \
               gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        rounded_size=$((($size/$MB + 1)*$MB))
        echo "Rounded Size = $rounded_size"

 3. 在上述的指令碼中設定為使用 rounded_size 原始磁碟，調整大小︰

        # qemu-img resize MyLinuxVM.raw $rounded_size

 4. 現在，回到固定大小 VHD 轉換原始磁碟︰

        # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd



## <a name="linux-kernel-requirements"></a>Linux 核心需求 ##

直接上游 Linux 核心被 aggregate HYPER-V 和 Azure Linux 整合服務 （清單） 驅動程式。 加入新的 Linux 核心版本 (亦即 3.x) 的許多散佈會有提供這些驅動程式，或否則提供其核心 backported 這些驅動程式版本。  所以盡可能建議執行[背書分配](virtual-machines-linux-endorsed-distros.md)會包含這些修正和更新的這些驅動程式會持續更新上游核心功能，與新的修正。

如果您執行的紅色角色企業 Linux 版本**6.0 6.3**的變化，您會需要安裝最新的清單驅動程式 HYPER-V。 [在這個位置](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409)找驅動程式。 RHEL **6.4 +** （及衍生） 清單驅動程式已經隨附於核心而，因此沒有額外的安裝套件所需執行 Azure 這些系統。

如果需要自訂的核心，建議使用較新的核心版本 （亦即**3.8 +**）。 為這些散佈或廠商維護自己核心，某些項目會清單驅動程式定期 backport 才能從您的自訂核心上游核心。  即使您已執行的較新的核心版本，建議來追蹤清單驅動程式和 backport 任何上游修正那些視。 Linux 核心來源樹狀目錄中[其困難之處](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS)檔案中有清單驅動程式來源檔案的位置︰

    F:  arch/x86/include/asm/mshyperv.h
    F:  arch/x86/include/uapi/asm/hyperv.h
    F:  arch/x86/kernel/cpu/mshyperv.c
    F:  drivers/hid/hid-hyperv.c
    F:  drivers/hv/
    F:  drivers/input/serio/hyperv-keyboard.c
    F:  drivers/net/hyperv/
    F:  drivers/scsi/storvsc_drv.c
    F:  drivers/video/fbdev/hyperv_fb.c
    F:  include/linux/hyperv.h
    F:  tools/hv/

在很小，下列項目缺少已知會造成問題 Azure 上並，因此這些必須包含在核心。 這是清單不詳盡或完成所有散發︰

- [ata_piix︰ 預設延磁碟 HYPER-V 驅動程式](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
- [storvsc︰ 重設 path 中的傳輸中封包帳戶](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
- [storvsc︰ 避免 WRITE_SAME 使用方式](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
- [storvsc︰ 停用撰寫相同的 RAID 和虛擬主機介面卡驅動程式](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
- [storvsc: NULL 指標參考修正](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
- [storvsc︰ 撥打緩衝可能會導致 I/O 凍結](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
- [scsi_sysfs︰ 防範 __scsi_remove_device 雙執行](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)


## <a name="the-azure-linux-agent"></a>Azure Linux 代理程式 ##

[Azure Linux 代理程式](virtual-machines-linux-agent-user-guide.md)(waagent)，才能正確佈建 Linux 中 Azure 虛擬機器。 您可以取得最新版本，檔案的問題，或提交提取[Linux 代理程式 GitHub repo](https://github.com/Azure/WALinuxAgent)要求。

- [Apache 2.0 授權放開 Linux 代理程式。 許多散佈已提供 RPM 或 deb 封包代理程式，因此在某些情況下這可以和安裝輕鬆更新。

- Azure Linux 代理程式需要 Python v2.6 +。

- 代理程式也需要 python pyasn1 模組。 大部分散佈會提供此為不同的安裝套件。

- 在某些情況下可能無法與 NetworkManager 相容 Azure Linux 代理程式。 散佈所提供的 RPM/Deb 套件許多設定 NetworkManager 為 waagent 套件，衝突，因此會解除安裝 NetworkManager 當您安裝 Linux 代理程式套件。


## <a name="general-linux-system-requirements"></a>一般 Linux 系統需求 ##

- 修改核心開機行幼蟲或 GRUB2 包含下列參數。 這也可確保所有主控台訊息會都傳送到第一個序列連接埠，就可以協助 Azure 偵錯問題支援︰

        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300

    這也可確保所有主控台訊息會都傳送到第一個序列連接埠，就可以協助 Azure 偵錯問題的支援。

    除了以上，建議若要*移除*下列參數如果有︰

        rhgb quiet crashkernel=auto

    圖形和安靜開機並不能用在雲端環境中想要傳送到序列連接埠的所有記錄。

    `crashkernel`選項可能左設定如有需要，但請注意，此參數會減少的量 128 MB 或更多]，以 VM 中可用的記憶體可能是較小的 VM 大小會造成問題。

- 安裝 Azure Linux 代理程式

    Azure Linux 代理程式時需要佈建 Azure Linux 影像。  許多散佈提供代理程式為 RPM 或 Deb 套件 （套件通常稱為 「 WALinuxAgent' 或 'walinuxagent 」）。  也可以[Linux 代理程式指南](virtual-machines-linux-agent-user-guide.md)中的步驟進行，以手動方式安裝代理程式。

- 確定 SSH 伺服器是安裝並設定為在啟動時啟動。  這通常是預設值。

- 不要 OS 磁碟上建立交換空間

    Azure Linux 代理程式可以自動設定使用本機資源磁碟上 Azure 佈建之後連接至 VM 的交換空間。 請注意，本機資源磁碟是一個*暫時性*的磁碟可能已清空時 VM 會取消提供。 之後安裝 Azure Linux 代理程式 （請參閱上一步），視情況修改中 /etc/waagent.conf 下列參數︰

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

- 最後一個步驟中，執行下列命令以 deprovision 虛擬機器︰

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

    >[AZURE.NOTE] 在 Virtualbox 您可能會看到下列錯誤後執行 「 waagent-強制-deprovision': `[Errno 5] Input/output error`。 此錯誤訊息並不重要，可以略過。

- 您然後必須關閉虛擬機器，並將 VHD 上載至 Azure。
