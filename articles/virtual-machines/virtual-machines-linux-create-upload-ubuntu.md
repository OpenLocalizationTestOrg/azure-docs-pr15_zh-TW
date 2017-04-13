<properties
    pageTitle="建立和上傳 Azure 中 Ubuntu Linux VHD"
    description="瞭解如何建立和上傳 Azure 虛擬硬碟 (VHD)，包含 Ubuntu Linux 作業系統。"
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
    ms.date="08/24/2016"
    ms.author="szark"/>

# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>準備 Azure Ubuntu 虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>正式 Ubuntu 雲端圖像
Ubuntu 現在發佈正式 Azure Vhd 下載[http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/)位置。 如果您需要建立 Azure 特殊的 Ubuntu 圖像，而非使用下列手動程序建議開始使用這些已知使用 Vhd 並依需求自訂。 永遠可以在下列位置找到最新的圖像版本︰

 - Ubuntu 12.04/精確︰ [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/precise/release/ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip)
 - Ubuntu 14.04/駒︰ [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
 - Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)


## <a name="prerequisites"></a>必要條件

本文假設您已經有安裝 Ubuntu Linux 作業系統虛擬的硬碟。 若要建立.vhd 檔案，例如例如 HYPER-V 虛擬化解決方案，有多個工具。 如需相關指示，請參閱[安裝 HYPER-V 角色，並設定虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。

**Ubuntu 安裝備忘稿**

- 如需秘訣準備 Azure Linux 也請[一般 Linux 安裝備忘稿](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)。
- Azure，只有 [**固定 VHD**中不支援 VHDX 格式。  您可以將磁碟轉換成 VHD 格式使用 HYPER-V 管理員或轉換 vhd 指令程式。
- 安裝 Linux 系統時，建議您使用標準的磁碟分割區，而不是 LVM （通常許多安裝的預設值）。 特別是如果 OS 磁碟需要附加至另一個 VM 進行疑難排解，這樣就可避免複製 Vm LVM 名稱衝突。 如果喜好， [LVM](virtual-machines-linux-configure-lvm.md)或[RAID](virtual-machines-linux-configure-raid.md)可能資料磁碟上使用。
- 不要 OS 磁碟上設定交換磁碟分割。 若要建立暫存資源磁碟上的交換檔案，可以設定 Linux 代理程式。  下列步驟中，可以找到相關資訊。
- 所有 Vhd 必須是 1 mb 的多重圖表的大小。


## <a name="manual-steps"></a>手動步驟

> [AZURE.NOTE] 前 Azure 建立您自己的自訂 Ubuntu 圖像，請考慮改用[http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/)圖像。


1. HYPER-V 管理員的中央窗格中選取的虛擬機器。

2. 按一下 [**連線**至開啟虛擬機器視窗。

3.  取代目前的存放庫中使用 Ubuntu 的 Azure repos 的影像。 步驟稍微 Ubuntu 得版本而定。

    編輯之前 /etc/apt/sources.list，建議先備份︰

        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04:

        # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14.04:

        # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        # sudo apt-get update

4. Ubuntu Azure 圖像現在追蹤*硬體預先啟動啟用*(HWE) 核心。 更新為最新的核心的作業系統，藉由執行下列命令︰

    Ubuntu 12.04:

        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

    Ubuntu 14.04:

        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot


5. 修改幼蟲 Azure 包含其他核心參數的核心開機線條。 若要執行此動作開啟 「 / 等/預設/幼蟲 」 在文字編輯器中，尋找 [變數稱為 「 `GRUB_CMDLINE_LINUX_DEFAULT` （或新增如有需要） 和編輯以包含下列參數︰

        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    儲存並關閉此檔案，然後再執行 「`sudo update-grub`」。 如此可確保所有主控台訊息會都傳送到第一個序列連接埠，就可以協助 Azure 偵錯問題的技術支援。

6.  確定 SSH 伺服器是安裝並設定為在啟動時啟動。  這通常是預設值。

7.  安裝 Azure Linux 代理程式︰

        # sudo apt-get update
        # sudo apt-get install walinuxagent

    請注意，安裝`walinuxagent`套件會移除`NetworkManager`和`NetworkManager-gnome`如果安裝套件。

8.  執行下列命令以 deprovision 虛擬機器並備妥以供佈建 Azure 上︰

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

9. 按一下 [HYPER-V 管理員中的 [**動作]-> [關閉向下**]。 您 Linux VHD 現在已準備就緒上, 傳至 Azure 的。


## <a name="next-steps"></a>後續步驟
現在，您準備好要使用的 Ubuntu Linux 虛擬硬碟 Azure 中建立新的虛擬機器。 如果這是您要上.vhd 檔案傳到 Azure 第一次，請參閱步驟 2 和 3 中[建立和上傳包含 Linux 作業系統的虛擬硬碟](virtual-machines-linux-classic-create-upload-vhd.md)。

## <a name="references"></a>參照 ##

Ubuntu 硬體預先啟動啟用 (HWE) 核心︰

- [http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html](http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html)
- [http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html](http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html)
