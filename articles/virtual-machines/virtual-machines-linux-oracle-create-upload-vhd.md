<properties
    pageTitle="建立和上傳 Oracle Linux VHD |Microsoft Azure"
    description="瞭解如何建立和上傳 Azure 虛擬硬碟 (VHD)，包含 Oracle Linux 作業系統。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management,azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>

# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>準備 Azure Oracle Linux 虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>必要條件 ##

本文假設您已經有安裝 Oracle Linux 作業系統虛擬的硬碟。 若要建立.vhd 檔案，例如例如 HYPER-V 虛擬化解決方案，有多個工具。 如需相關指示，請參閱[安裝 HYPER-V 角色，並設定虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。


### <a name="oracle-linux-installation-notes"></a>Oracle Linux 安裝備忘稿

- 如需秘訣準備 Azure Linux 也請[一般 Linux 安裝備忘稿](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)。

- Oracle 的紅色角色相容核心和其 UEK3 （永不折損企業核心） 同時支援 HYPER-V 和 Azure 上。 為了獲得最佳結果，請務必更新至最新的核心準備您 Oracle Linux VHD 時。

- Oracle 的 UEK2 不支援 HYPER-V 和 Azure 為不包含必要的驅動程式。

- Azure，只有 [**固定 VHD**中不支援 VHDX 格式。  您可以將磁碟轉換成 VHD 格式使用 HYPER-V 管理員或轉換 vhd 指令程式。

- 安裝 Linux 系統時，建議您使用標準的磁碟分割區，而不是 LVM （通常許多安裝的預設值）。 特別是如果 OS 磁碟需要附加至另一個 VM 進行疑難排解，這樣就可避免複製 Vm LVM 名稱衝突。 如果喜好， [LVM](virtual-machines-linux-configure-lvm.md)或[RAID](virtual-machines-linux-configure-raid.md)可能資料磁碟上使用。

- NUMA 不支援更大的 VM 大小，因為下方 2.6.37 Linux 核心版本中的錯誤。 此問題主要影響分配，使用上游紅色角色 2.6.32 核心。 手動安裝 Azure Linux 代理程式 (waagent) 會自動停用 NUMA Linux 核心幼蟲設定。 下列步驟中，可以找到相關資訊。

- 不要 OS 磁碟上設定交換磁碟分割。 若要建立暫存資源磁碟上的交換檔案，可以設定 Linux 代理程式。  下列步驟中，可以找到相關資訊。

- 所有 Vhd 必須是 1 mb 的多重圖表的大小。

- 請確定`Addons`存放庫已啟用。 編輯檔案`/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) 或`/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux)，並變更行`enabled=0`至`enabled=1`在**[ol6_addons]**或 [此檔案中的**[ol7_addons]**底下。


## <a name="oracle-linux-64"></a>Oracle Linux 6.4 + ##

您必須完成作業系統執行 Azure 虛擬機器中的特定的設定步驟。

1. HYPER-V 管理員的中央窗格中選取的虛擬機器。

2. 按一下 [**連線**至開啟虛擬機器視窗。

3. 解除安裝 NetworkManager，藉由執行下列命令︰

        # sudo rpm -e --nodeps NetworkManager

    **附註︰**如果尚未安裝套件，此命令將會失敗，錯誤訊息。 這是可預期。

4.  建立一個名為**網路**的檔案`/etc/sysconfig/`目錄，包含下列的文字︰

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  建立一個名為**ifcfg eth0**中檔案`/etc/sysconfig/network-scripts/`目錄，包含下列的文字︰

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  修改 udev 規則，以避免產生的乙太網路介面的靜態規則。 這些規則可能會導致問題，請在 Microsoft Azure 或超 v 虛擬機器時

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

7. 請確定網路服務會開始在啟動時，執行下列命令︰

        # chkconfig network on

8. 安裝 python pyasn1 藉由執行下列命令︰

        # sudo yum install python-pyasn1

9.  修改核心開機行包含其他核心參數的 Azure 幼蟲設定中。 若要執行此開啟 「 / boot/grub/menu.lst 」 文字編輯器中，並確保預設核心包含下列參數︰

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    這也可確保所有主控台訊息會都傳送到第一個序列連接埠，就可以協助 Azure 偵錯問題的支援。 這會停用 NUMA，因為 Oracle 的紅色角色相容核心中的錯誤。

    除了以上，建議您是在*移除*下列參數︰

        rhgb quiet crashkernel=auto

    圖形和安靜開機並不能用在雲端環境中想要傳送到序列連接埠的所有記錄。

    `crashkernel`選項可能左設定如有需要，但請注意，此參數會減少的量 128 MB 或更多]，以 VM 中可用的記憶體可能是較小的 VM 大小會造成問題。


10. 確定 SSH 伺服器是安裝並設定為在啟動時啟動。  這通常是預設值。

11. 執行下列命令以安裝 Azure Linux 代理程式。 最新版本是 2.0.15。

        # sudo yum install WALinuxAgent

    請注意，安裝 WALinuxAgent 套件，將會移除 NetworkManager NetworkManager gnome 封包是否沒有已經移除所述的步驟 2。

12. 無法建立交換空間 OS 磁碟上。

    Azure Linux 代理程式可以自動設定使用本機資源磁碟上 Azure 佈建之後連接至 VM 的交換空間。 請注意，本機資源磁碟是一個*暫時性*的磁碟可能已清空時 VM 會取消提供。 之後安裝 Azure Linux 代理程式 （請參閱上一步），視情況修改中 /etc/waagent.conf 下列參數︰

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. 執行下列命令以 deprovision 虛擬機器並備妥以供佈建 Azure 上︰

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14. 按一下 [HYPER-V 管理員中的 [**動作]-> [關閉向下**]。 您 Linux VHD 現在已準備就緒上, 傳至 Azure 的。


----------


## <a name="oracle-linux-70"></a>Oracle Linux 7.0 + ##

**Oracle Linux 7 中的變更**

準備 Azure Oracle Linux 7 虛擬機器是 Oracle Linux 6，不過有幾個重要的差異，值得非常類似︰

 - Azure 支援紅色角色相容核心，並 Oracle 的 UEK3。  建議您 UEK3 核心。
 - Azure Linux 代理程式不再衝突 NetworkManager 套件。 預設會安裝此套件，我們建議您不要移除。
 - GRUB2 現在作為預設開機載入器，以便進行編輯核心參數的程序已變更 （請參閱下方）。
 - XFS 現在預設檔案系統。 如有需要，仍可以使用 ext4 檔案系統。


**設定步驟**

1. 在 [HYPER-V 管理員選取的虛擬機器。

2. 按一下 [**連線**至開啟虛擬機器主控台視窗。

3.  建立一個名為**網路**的檔案`/etc/sysconfig/`目錄，包含下列的文字︰

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.  建立一個名為**ifcfg eth0**中檔案`/etc/sysconfig/network-scripts/`目錄，包含下列的文字︰

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

5.  修改 udev 規則，以避免產生的乙太網路介面的靜態規則。 這些規則可能會導致問題，請在 Microsoft Azure 或超 v 虛擬機器時

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. 請確定網路服務會開始在啟動時，執行下列命令︰

        # sudo chkconfig network on

7. 安裝 python pyasn1 套件藉由執行下列命令︰

        # sudo yum install python-pyasn1

8.  執行下列命令以清除目前 yum 中繼資料，並安裝任何更新︰

        # sudo yum clean all
        # sudo yum -y update

9.  修改核心開機行包含其他核心參數的 Azure 幼蟲設定中。 若要執行此動作開啟 「 / 等/預設/幼蟲 」 中的文字編輯器和編輯`GRUB_CMDLINE_LINUX`參數，例如︰

        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"

    這也可確保所有主控台訊息會都傳送到第一個序列連接埠，就可以協助 Azure 偵錯問題的支援。 它也會開啟新的 OEL 7 命名慣例關閉 Nic。 除了以上，建議您是在*移除*下列參數︰

        rhgb quiet crashkernel=auto

    圖形和安靜開機並不能用在雲端環境中想要傳送到序列連接埠的所有記錄。

    `crashkernel`選項可能左設定如有需要，但請注意，此參數會減少的量 128 MB 或更多]，以 VM 中可用的記憶體可能是較小的 VM 大小會造成問題。


10. 在您完成編輯 「 / 等/預設/幼蟲 「 每個上方，執行下列命令以重建幼蟲設定︰

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

11. 確定 SSH 伺服器是安裝並設定為在啟動時啟動。  這通常是預設值。

12. 安裝 Azure Linux 代理程式執行下列命令︰

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent

13. 無法建立交換空間 OS 磁碟上。

    Azure Linux 代理程式可以自動設定使用本機資源磁碟上 Azure 佈建之後連接至 VM 的交換空間。 請注意，本機資源磁碟是一個*暫時性*的磁碟可能已清空時 VM 會取消提供。 之後安裝 Azure Linux 代理程式 （請參閱上一步），視情況修改中 /etc/waagent.conf 下列參數︰

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. 執行下列命令以 deprovision 虛擬機器並備妥以供佈建 Azure 上︰

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15. 按一下 [HYPER-V 管理員中的 [**動作]-> [關閉向下**]。 您 Linux VHD 現在已準備就緒上, 傳至 Azure 的。


## <a name="next-steps"></a>後續步驟
現在，您準備好要使用您 Oracle Linux.vhd Azure 中建立新的虛擬機器。 如果這是您要上.vhd 檔案傳到 Azure 第一次，請參閱步驟 2 和 3 中[建立和上傳包含 Linux 作業系統的虛擬硬碟](virtual-machines-linux-classic-create-upload-vhd.md)。
