<properties
pageTitle="Oracle Linux 虛擬機器準備 Azure |Microsoft Azure"
description="在 [Microsoft Azure 中執行 Linux Oracle 虛擬機器的逐步設定。"
services="virtual-machines-linux"
authors="bbenz"
manager="timlt"
documentationCenter="virtual-machines"
tags="azure-service-management,azure-resource-manager"
/>

<tags
ms.service="virtual-machines-linux"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="vm-linux"
ms.workload="infrastructure-services"
ms.date="06/22/2015"
ms.author="bbenz" />

# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>準備 Azure Oracle Linux 虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


-   [準備 Azure Oracle Linux 6.4 + 虛擬機器](virtual-machines-linux-oracle-create-upload-vhd.md)

-   [準備 Azure Oracle Linux 7.0 + 虛擬機器](virtual-machines-linux-oracle-create-upload-vhd.md)

## <a name="prerequisites"></a>必要條件
本文假設您已經有安裝 Oracle Linux 作業系統虛擬的硬碟。 若要建立.vhd 檔案，例如例如 HYPER-V 虛擬化解決方案，有多個工具。 如需相關指示，請參閱[安裝 HYPER-V 並建立虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。

**Oracle Linux 安裝備忘稿**

- Oracle 的紅色角色相容核心和其 UEK3 （永不折損企業核心） 同時支援 HYPER-V 和 Azure 上。 為了獲得最佳結果，請務必一併更新至最新的核心準備您 Oracle Linux VHD 時。

- Oracle 的 UEK2 不支援 HYPER-V 和 Azure 為不包含必要的驅動程式。

- Azure 中不支援較新的 VHDX 格式。 您可以轉換成 VHD 格式的磁碟使用 HYPER-V 管理員或轉換 vhd 指令程式。

- 當您要安裝 Linux 系統時，我們建議您使用標準的磁碟分割區，而不是 LVM （通常許多安裝的預設值）。 特別是如果 OS 磁碟需要附加至另一個 VM 進行疑難排解，這樣就可避免複製 Vm LVM 名稱衝突。 如果喜好，LVM 或[RAID](virtual-machines-linux-configure-raid.md)可能資料磁碟上使用。

- NUMA 不支援更大的 VM 大小，因為下方 2.6.37 Linux 核心版本中的錯誤。 此問題主要影響分配，使用上游的紅色角色 2.6.32 核心。 手動安裝 Azure Linux 代理程式 (waagent) 會自動停用 NUMA Linux 核心幼蟲設定。 下列步驟中，可以找到相關資訊。

- 不要 OS 磁碟上設定交換磁碟分割。 若要建立暫存資源磁碟上的交換檔案，可以設定 Linux 代理程式。 下列步驟中，可以找到相關資訊。

- 所有 Vhd 必須是 1 mb 的多重圖表的大小。

- 請確定`Addons`存放庫已啟用。 選擇 [編輯檔案`/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) 或`/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux)，並變更行`enabled=0`至`enabled=1`在**[ol6_addons]**或 [此檔案中的**[ol7_addons]**底下。


## <a name="oracle-linux-64"></a>Oracle Linux 6.4 +
您必須完成作業系統執行 Azure 虛擬機器中的特定的設定步驟。

1. HYPER-V 管理員的中央窗格中選取的虛擬機器。

2. 按一下 [**連線**至開啟虛擬機器視窗。

3. 解除安裝 NetworkManager，藉由執行下列命令︰

        # sudo rpm -e --nodeps NetworkManager

    >[AZURE.NOTE] 如果尚未安裝套件，此命令將會失敗，錯誤訊息。 這是可預期。

4. 建立一個名為**網路**/etc/sysconfig/目錄中包含下列的文字檔案︰

    `NETWORKING=yes`  
    `HOSTNAME=localhost.localdomain`

5.  建立一個名為**ifcfg eth0**中 /etc/sysconfig/network-scripts 檔案 / 目錄，包含下列的文字︰

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
            TYPE=Ethernet
            USERCTL=no
            PEERDNS=yes
        IPV6INIT=no

6.  移動 （或移除） udev 規則，以避免產生的乙太網路介面的靜態規則。 當您要複製超 v 中 Azure 虛擬機器時，這些規則會導致問題

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2\>/dev/null
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2\>/dev/null

7.  請確定網路服務會開始在啟動時，執行下列命令︰

        # chkconfig network on

8.  安裝 python pyasn1 藉由執行下列命令︰

        # sudo yum install python-pyasn1

9.  修改核心開機行包含其他核心參數的 Azure 幼蟲設定中。 若要這麼做，請開啟 「 / boot/grub/menu.lst 」 文字編輯器中，並確保預設核心包含下列參數︰

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    這也可確保所有主控台訊息會都傳送至第一個序列連接埠，就可以協助 Azure 偵錯問題的支援。 這會停用 NUMA，因為 Oracle 的紅色角色相容核心中的錯誤。

    除了以上，我們建議您*移除*下列參數︰

        rhgb quiet crashkernel=auto

    圖形和安靜開機並不能用在雲端環境中想要傳送到序列連接埠的所有記錄。

    `crashkernel`選項可能左設定如有需要，但請注意，此參數會減少 128 MB 以上 VM 中可用的記憶體。 這可能是較小的 VM 大小會造成問題。

10.  確定 SSH 伺服器是安裝並設定為在啟動時啟動。 這通常是預設值。

11.  安裝 Azure Linux 代理程式執行下列命令︰

        # <a name="sudo-yum-install-walinuxagent"></a>sudo yum 安裝 WALinuxAgent

    請注意，安裝 WALinuxAgent 套件，將會移除 NetworkManager NetworkManager gnome 封包是否沒有已經移除所述的步驟 2。

12.  無法建立交換空間 OS 磁碟上。

    Azure Linux 代理程式可以使用本機資源磁碟 VM 附加在 Azure 佈建之後，會自動設定交換空間。 請注意，本機資源磁碟*暫存*的磁碟可能清空時 VM 會取消提供。 在您安裝 Azure Linux 代理程式後 （請參閱上一步），視情況修改中 /etc/waagent.conf 下列參數︰

        ResourceDisk.Format=y

        ResourceDisk.Filesystem=ext4

        ResourceDisk.MountPoint=/mnt/resource

        ResourceDisk.EnableSwap=y

        ResourceDisk.SwapSizeMB=2048 # # 附註︰ 將此設定為您需要它。

13.  執行下列命令以 deprovision 虛擬機器並備妥以供佈建 Azure 上︰

        # <a name="sudo-waagent--force--deprovision"></a>sudo waagent-強制-deprovision
        # <a name="export-histsize0"></a>匯出 HISTSIZE = 0
        # <a name="logout"></a>登出

14.  按一下 [**動作-\>關閉**在 HYPER-V 管理員。 您 Linux VHD 現在已準備就緒上, 傳至 Azure 的。

## <a name="oracle-linux-70"></a>Oracle Linux 7.0 +
**Oracle Linux 7 中的變更**

準備 Azure Oracle Linux 7 虛擬機器是非常類似 Oracle Linux 6 的程序。 然而，有幾個重要的差異，值得︰

-   Azure 支援紅色角色相容核心，並 Oracle 的 UEK3。 我們建議您 UEK3 核心。

-   Azure Linux 代理程式不再衝突 NetworkManager 套件。 根據預設，安裝此套件，我們建議您不要移除。

-   GRUB2 現在作為預設開機載入器，以便進行編輯核心參數的程序已變更 （請參閱下方）。

-   XFS 現在預設檔案系統。 如有需要，仍可以使用 ext4 檔案系統。

**設定步驟**

1.  在 [HYPER-V 管理員選取的虛擬機器。

2.  按一下 [**連線**至開啟虛擬機器主控台視窗。

3.  建立一個名為**網路**/etc/sysconfig/目錄中包含下列的文字檔案︰

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.  建立一個名為**ifcfg eth0**中 /etc/sysconfig/network-scripts 檔案 / 目錄，包含下列的文字︰

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
            PEERDNS=yes
        IPV6INIT=no

5.  移動 （或移除） udev 規則，以避免產生的乙太網路介面的靜態規則。 當您要複製 HYPER-V 中 Microsoft Azure 虛擬機器時，這些規則會造成問題。

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

6.  請確定網路服務會開始在啟動時，執行下列命令︰

        # sudo chkconfig network on

7.  安裝 python pyasn1 套件藉由執行下列命令︰

        # sudo yum install python-pyasn1

8.  執行下列命令以清除目前 yum 中繼資料，並安裝任何更新︰

        # sudo yum clean all
        # sudo yum -y update

9.  修改核心開機行包含其他核心參數的 Azure 幼蟲設定中。 若要這麼做，開啟 「 / 等/預設/幼蟲 」 中的文字編輯器和編輯幼蟲\_命令行\_LINUX 參數，例如︰

        GRUB\_CMDLINE\_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"

    這也可確保所有主控台訊息會都傳送到第一個序列連接埠，就可以協助 Azure 偵錯問題的支援。 除了以上，我們建議您*移除*下列參數︰

        rhgb quiet crashkernel=auto

    圖形和安靜開機並不能用在雲端環境中想要傳送到序列連接埠的所有記錄。

    `crashkernel`選項可能左設定如有需要，但請注意，此參數會減少 128 MB 以上 VM 中可用的記憶體。 這可能是較小的 VM 大小會造成問題。

10.  在您完成編輯 「 / 等/預設/幼蟲 」，執行下列命令以重建幼蟲設定︰

        # <a name="sudo-grub2-mkconfig--o-bootgrub2grubcfg"></a>sudo grub2 mkconfig command 和 o /boot/grub2/grub.cfg

11.  確定 SSH 伺服器是安裝並設定為在啟動時啟動。 這通常是預設值。

12.  安裝 Azure Linux 代理程式執行下列命令︰

        # <a name="sudo-yum-install-walinuxagent"></a>sudo yum 安裝 WALinuxAgent

13.  無法建立交換空間 OS 磁碟上。

    Azure Linux 代理程式可以使用本機資源磁碟 VM 附加在 Azure 佈建之後，會自動設定交換空間。 請注意，本機資源磁碟*暫存*的磁碟可能清空時 VM 會取消提供。 在您安裝 Azure Linux 代理程式後 （請參閱上一步），視情況修改中 /etc/waagent.conf 下列參數︰

        ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 # # 附註︰ 將此設定為您需要它。

14.  執行下列命令以 deprovision 虛擬機器並備妥以供佈建 Azure 上︰

        # <a name="sudo-waagent--force--deprovision"></a>sudo waagent-強制-deprovision
        # <a name="export-histsize0"></a>匯出 HISTSIZE = 0
        # <a name="logout"></a>登出

15.  按一下 [**動作-\>關閉**在 HYPER-V 管理員。 您 Linux VHD 現在已準備就緒上, 傳至 Azure 的。
