<properties
    pageTitle="準備 Debian Linux VHD |Microsoft Azure"
    description="瞭解如何建立 Debian 7 和 8 VHD 檔案以供部署 Azure 中。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>



# <a name="prepare-a-debian-vhd-for-azure"></a>準備 Azure Debian VHD

## <a name="prerequisites"></a>必要條件
本節假設您已從.iso 檔案從[Debian 網站](https://www.debian.org/distrib/)下載至虛擬硬碟安裝 Debian Linux 的作業系統。 若要建立.vhd 檔案; 有多個的工具HYPER-V 是只有一個範例。 如需使用 HYPER-V 的指示，請參閱[安裝 HYPER-V 角色，並設定虛擬機器](https://technet.microsoft.com/library/hh846766.aspx)。


## <a name="installation-notes"></a>安裝備註

- 如需秘訣準備 Azure Linux 也請[一般 Linux 安裝備忘稿](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)。
- Azure 中不支援較新的 VHDX 格式。 您可以將磁碟轉換成 VHD 格式使用 HYPER-V 管理員或**轉換 vhd**指令程式。
- 安裝 Linux 系統時，建議您使用標準的磁碟分割區，而不是 LVM （通常許多安裝的預設值）。 特別是如果 OS 磁碟需要附加至另一個 VM 進行疑難排解，這樣就可避免複製 Vm LVM 名稱衝突。 如果喜好， [LVM](virtual-machines-linux-configure-lvm.md)或[RAID](virtual-machines-linux-configure-raid.md)可能資料磁碟上使用。
- 不要 OS 磁碟上設定交換磁碟分割。 若要建立暫存資源磁碟上的交換檔案，可以設定 Azure Linux 代理程式。 下列步驟中，可以找到相關資訊。
- 所有 Vhd 必須是 1 mb 的多重圖表的大小。


## <a name="use-azure-manage-to-create-debian-vhds"></a>使用 Azure 管理建立 Debian Vhd

您可使用工具可用來產生 Debian Vhd 的 Azure，例如[azure-管理](https://github.com/credativ/azure-manage) [credativ](http://www.credativ.com/)指令碼。 這是與從頭開始建立圖像建議的方法。 例如，若要建立 Debian 8 VHD，請執行下列命令以下載 azure-管理 （和相依性） 執行 azure_build_image 指令碼︰

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>手動準備 Debian VHD

1. 在 [HYPER-V 管理員選取的虛擬機器。

2. 按一下 [**連線**至開啟虛擬機器主控台視窗。

3. 註解**deb 光碟機**中的線條`/etc/apt/source.list`如果您設定的 VM 針對 ISO 檔案。

4. 編輯`/etc/default/grub`檔案及修改**GRUB_CMDLINE_LINUX**參數，如下所示的 Azure 包含其他核心參數。

        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200 earlyprintk=ttyS0,115200 rootdelay=30"

5. 重建幼蟲並執行︰

        # sudo update-grub

6. 新增至 /etc/apt/sources.list Debian 的 Azure 存放庫 Debian 7 或 8:

    **「 Debian 7.x Wheezy 」**

        deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
        deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main


    **Debian 8.x 」 潔 」**

        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure jessie main
        deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


7. 安裝 Azure Linux 代理程式︰

        # sudo apt-get update
        # sudo apt-get install waagent

8. Debian 7 中，則需要執行 3.16 型核心從 wheezy backports 存放庫。 第一次建立檔案稱為 /etc/apt/preferences.d/linux.pref 使用下列內容︰

        Package: linux-image-amd64 initramfs-tools
        Pin: release n=wheezy-backports
        Pin-Priority: 500

    若要安裝新的核心然後執行 「 sudo 引起取得安裝 linux-圖像-amd64 」。

8. Deprovision 虛擬機器備妥以供上 Azure 佈建和執行︰

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

9. 按一下 [**動作**]-> [關閉向下在 HYPER-V 管理員。 您 Linux VHD 現在已準備就緒上, 傳至 Azure 的。


## <a name="next-steps"></a>後續步驟

現在，您準備好要使用的 Debian 虛擬硬碟 Azure 中建立新的虛擬機器。 如果這是您要上.vhd 檔案傳到 Azure 第一次，請參閱步驟 2 和 3 中[建立和上傳包含 Linux 作業系統的虛擬硬碟](virtual-machines-linux-classic-create-upload-vhd.md)。
