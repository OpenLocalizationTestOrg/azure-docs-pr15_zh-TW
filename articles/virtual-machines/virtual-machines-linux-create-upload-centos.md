<properties
    pageTitle="建立和上傳 CentOS 型 Linux VHD Azure 中"
    description="瞭解如何建立和上傳 Azure 虛擬硬碟 (VHD)，包含 CentOS 型 Linux 作業系統。"
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
    ms.date="05/09/2016"
    ms.author="szarkos"/>

# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>準備 Azure 虛擬機器 CentOS 型

- [準備 Azure CentOS 6.x 虛擬機器](#centos-6x)
- [準備 Azure CentOS 7.0 + 虛擬機器](#centos-70)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>必要條件 ##

本文假設您已安裝 CentOS （或類似衍生項目） Linux 作業系統虛擬的硬碟。 若要建立.vhd 檔案，例如例如 HYPER-V 虛擬化解決方案，有多個工具。 如需相關指示，請參閱[安裝 HYPER-V 角色，並設定虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。


**CentOS 安裝備忘稿**

- 如需秘訣準備 Azure Linux 也請[一般 Linux 安裝備忘稿](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)。

- Azure，只有 [**固定 VHD**中不支援 VHDX 格式。  您可以將磁碟轉換成 VHD 格式使用 HYPER-V 管理員或轉換 vhd 指令程式。

- 安裝 Linux 系統時，建議您使用標準的磁碟分割區，而不是 LVM （通常許多安裝的預設值）。 特別是如果 OS 磁碟需要附加至另一個 VM 進行疑難排解，這樣就可避免複製 Vm LVM 名稱衝突。  如果喜好， [LVM](virtual-machines-linux-configure-lvm.md)或[RAID](virtual-machines-linux-configure-raid.md)可能資料磁碟上使用。

- NUMA 不支援更大的 VM 大小，因為下方 2.6.37 Linux 核心版本中的錯誤。 此問題主要影響分配，使用上游紅色角色 2.6.32 核心。 手動安裝 Azure Linux 代理程式 (waagent) 會自動停用 NUMA Linux 核心幼蟲設定。 下列步驟中，可以找到相關資訊。

- 不要 OS 磁碟上設定交換磁碟分割。 若要建立暫存資源磁碟上的交換檔案，可以設定 Linux 代理程式。  下列步驟中，可以找到相關資訊。

- 所有 Vhd 必須是 1 mb 的多重圖表的大小。


## <a name="centos-6x"></a>CentOS 6.x ##

1. 在 [HYPER-V 管理員選取的虛擬機器。

2. 按一下 [**連線**至開啟虛擬機器主控台視窗。

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

        # sudo chkconfig network on


8. **僅 centOS 6.3**︰ 安裝驅動程式 Linux 整合服務 （清單）。

    **重要事項︰ 步驟是僅適用於 CentOS 6.3 及舊版。**  在 CentOS 6.4 + Linux 整合服務都*已在標準的核心*。

    - 遵循安裝指令[清單下載頁面](https://www.microsoft.com/en-us/download/details.aspx?id=46842)上，並安裝至影像 RPM。  


9. 安裝 python pyasn1 套件藉由執行下列命令︰

        # sudo yum install python-pyasn1

10. 如果您想要使用 OpenLogic 鏡像裝載 Azure 資料中心內，然後使用下列存放庫取代 /etc/yum.repos.d/CentOS-Base.repo 檔案。  這也會加入**[openlogic]**存放庫包含封包 Azure Linux 代理程式︰

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

    **附註︰**本指南的其餘部分將假設您使用的至少有 [openlogic] repo，會用來安裝下列 Azure Linux 代理程式。


11. 新增 /etc/yum.conf 的下列行︰

        http_caching=packages

    及**CentOS 6.3 只在**新增的下列行︰

        exclude=kernel*

12. 編輯檔案來停用 yum 模組 」 fastestmirror 」 」 / etc/yum/pluginconf.d/fastestmirror.conf 」，並在`[main]`，請輸入︰

        set enabled=0

13. 執行下列命令以清除目前 yum 中繼資料︰

        # yum clean all

14. **在 CentOS 6.3 只**更新核心使用下列命令︰

        # sudo yum --disableexcludes=all install kernel

15. 修改核心開機行包含其他核心參數的 Azure 幼蟲設定中。 若要這麼做，請開啟 「 / boot/grub/menu.lst 」 文字編輯器中，並確保預設核心包含下列參數︰

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    這也可確保所有主控台訊息會都傳送到第一個序列連接埠，就可以協助 Azure 偵錯問題的支援。 這會停用 NUMA，因為使用 CentOS 6 的核心版本中的錯誤。

    除了以上，建議您是在*移除*下列參數︰

        rhgb quiet crashkernel=auto

    圖形和安靜開機並不能用在雲端環境中想要傳送到序列連接埠的所有記錄。

    `crashkernel`選項可能左設定如有需要，但請注意，此參數會減少的量 128 MB 或更多]，以 VM 中可用的記憶體可能是較小的 VM 大小會造成問題。


16. 確定 SSH 伺服器是安裝並設定為在啟動時啟動。  這通常是預設值。

17. 安裝 Azure Linux 代理程式執行下列命令︰

        # sudo yum install WALinuxAgent

    請注意，安裝 WALinuxAgent 套件，將會移除 NetworkManager NetworkManager gnome 封包是否沒有已經移除所述的步驟 2。

18. 無法建立交換空間 OS 磁碟上。

    Azure Linux 代理程式可以自動設定使用本機資源磁碟上 Azure 佈建之後連接至 VM 的交換空間。 請注意，本機資源磁碟是一個*暫時性*的磁碟可能已清空時 VM 會取消提供。 之後安裝 Azure Linux 代理程式 （請參閱上一步），視情況修改中 /etc/waagent.conf 下列參數︰

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

19. 執行下列命令以 deprovision 虛擬機器並備妥以供佈建 Azure 上︰

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

20. 按一下 [HYPER-V 管理員中的 [**動作]-> [關閉向下**]。 您 Linux VHD 現在已準備就緒上, 傳至 Azure 的。


----------


## <a name="centos-70"></a>CentOS 7.0 + ##

**變更 CentOS 7 （與類似衍生）**

準備 Azure 虛擬機器 CentOS 7 是 CentOS 6，不過有幾個重要的差異，值得非常類似︰

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

6.  修改 udev 規則，以避免產生的乙太網路介面的靜態規則。 這些規則可能會導致問題，請在 Microsoft Azure 或超 v 虛擬機器時

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. 請確定網路服務會開始在啟動時，執行下列命令︰

        # sudo chkconfig network on

7. 安裝 python pyasn1 套件藉由執行下列命令︰

        # sudo yum install python-pyasn1

8. 如果您想要使用 OpenLogic 鏡像裝載 Azure 資料中心內，然後使用下列存放庫取代 /etc/yum.repos.d/CentOS-Base.repo 檔案。  這也會加入**[openlogic]**存放庫包含封包 Azure Linux 代理程式︰

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7



    **附註︰**本指南的其餘部分將假設您使用的至少有 [openlogic] repo，會用來安裝下列 Azure Linux 代理程式。

9.  執行下列命令以清除目前 yum 中繼資料，並安裝任何更新︰

        # sudo yum clean all
        # sudo yum -y update

10. 修改核心開機行包含其他核心參數的 Azure 幼蟲設定中。 若要這麼做，開啟 「 / 等/預設/幼蟲 」 中的文字編輯器和編輯`GRUB_CMDLINE_LINUX`參數，例如︰

        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"

    這也可確保所有主控台訊息會都傳送到第一個序列連接埠，就可以協助 Azure 偵錯問題的支援。 它也會開啟新的 CentOS 7 命名慣例關閉 Nic。 除了以上，建議您是在*移除*下列參數︰

        rhgb quiet crashkernel=auto

    圖形和安靜開機並不能用在雲端環境中想要傳送到序列連接埠的所有記錄。

    `crashkernel`選項可能左設定如有需要，但請注意，此參數會減少的量 128 MB 或更多]，以 VM 中可用的記憶體可能是較小的 VM 大小會造成問題。

11. 在您完成編輯 「 / 等/預設/幼蟲 「 每個上方，執行下列命令以重建幼蟲設定︰

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

12. 確定 SSH 伺服器是安裝並設定為在啟動時啟動。  這通常是預設值。

13. **VMWare、 VirtualBox 或 KVM 建置圖像時，才︰**將 initramfs HYPER-V 模組︰

    編輯`/etc/dracut.conf`，新增內容︰

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    重建 initramfs:

        # dracut –f -v

14. 安裝 Azure Linux 代理程式執行下列命令︰

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent

15. 無法建立交換空間 OS 磁碟上。

    Azure Linux 代理程式可以自動設定使用本機資源磁碟上 Azure 佈建之後連接至 VM 的交換空間。 請注意，本機資源磁碟是一個*暫時性*的磁碟可能已清空時 VM 會取消提供。 之後安裝 Azure Linux 代理程式 （請參閱上一步），視情況修改中 /etc/waagent.conf 下列參數︰

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. 執行下列命令以 deprovision 虛擬機器並備妥以供佈建 Azure 上︰

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17. 按一下 [HYPER-V 管理員中的 [**動作]-> [關閉向下**]。 您 Linux VHD 現在已準備就緒上, 傳至 Azure 的。

## <a name="next-steps"></a>後續步驟
現在，您準備好要使用的 CentOS Linux 虛擬硬碟 Azure 中建立新的虛擬機器。 如果這是您要上.vhd 檔案傳到 Azure 第一次，請參閱步驟 2 和 3 中[建立和上傳包含 Linux 作業系統的虛擬硬碟](virtual-machines-linux-classic-create-upload-vhd.md)。
