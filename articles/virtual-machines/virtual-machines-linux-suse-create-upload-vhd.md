<properties
    pageTitle="建立和上傳 SUSE Linux VHD Azure 中"
    description="瞭解如何建立和上傳 Azure 虛擬硬碟 (VHD)，包含 SUSE Linux 作業系統。"
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

# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>準備 Azure 虛擬機器 SLES 或 openSUSE

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>必要條件 ##

本文假設您已安裝的 SUSE 或 openSUSE Linux 作業系統虛擬的硬碟。 若要建立.vhd 檔案，例如例如 HYPER-V 虛擬化解決方案，有多個工具。 如需相關指示，請參閱[安裝 HYPER-V 角色，並設定虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。

### <a name="sles--opensuse-installation-notes"></a>SLES / openSUSE 安裝備忘稿

- 如需秘訣準備 Azure Linux 也請[一般 Linux 安裝備忘稿](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)。

- Azure，只有 [**固定 VHD**中不支援 VHDX 格式。  您可以將磁碟轉換成 VHD 格式使用 HYPER-V 管理員或轉換 vhd 指令程式。

- 安裝 Linux 系統時，建議您使用標準的磁碟分割區，而不是 LVM （通常許多安裝的預設值）。 特別是如果 OS 磁碟需要附加至另一個 VM 進行疑難排解，這樣就可避免複製 Vm LVM 名稱衝突。 如果喜好， [LVM](virtual-machines-linux-configure-lvm.md)或[RAID](virtual-machines-linux-configure-raid.md)可能資料磁碟上使用。

- 不要 OS 磁碟上設定交換磁碟分割。 若要建立暫存資源磁碟上的交換檔案，可以設定 Linux 代理程式。  下列步驟中，可以找到相關資訊。

- 所有 Vhd 必須是 1 mb 的多重圖表的大小。


## <a name="use-suse-studio"></a>使用 SUSE Studio
[SUSE Studio](http://www.susestudio.com)可以輕鬆地建立及管理 Azure 和 HYPER-V SLES 和 openSUSE 圖像。 這是以自訂您自己 SLES 和 openSUSE 圖像的建議的方式。

其他方法來建立您自己的 VHD，SUSE 也會在[VMDepot](https://vmdepot.msopentech.com/User/Show?user=1007)SLES 的發佈 BYOS （將您的訂閱） 圖像。


## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>準備 SUSE Linux Enterprise Server 11 SP4 ##

1. HYPER-V 管理員的中央窗格中選取的虛擬機器。

2. 按一下 [**連線**至開啟虛擬機器視窗。

3. 註冊 SUSE Linux 企業系統為允許下載更新並安裝套件。

4. 最新的更新來更新系統︰

        # sudo zypper update

5. 從 SLES 存放庫安裝 Azure Linux 代理程式︰

        # sudo zypper install WALinuxAgent

6. 存回是否 waagent 會設定為 「 在 「 chkconfig，而且如果不是，啟用自動啟動︰
               
        # sudo chkconfig waagent on

7. 檢查如果 waagent 服務會執行，但如果不是，啟動︰ 

        # sudo service waagent start
                
8. 修改核心開機行包含其他核心參數的 Azure 幼蟲設定中。 若要執行此開啟 「 / boot/grub/menu.lst 」 文字編輯器中，並確保預設核心包含下列參數︰

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    這樣就能確定所有主控台訊息會都傳送到第一個序列連接埠，就可以協助 Azure 偵錯問題的支援。

9. 確認 /boot/grub/menu.lst 和 /etc/fstab 兩者參照，而不磁碟識別碼 （依-識別碼） 使用其 UUID (依-uuid) 磁碟。 

    取得磁碟 UUID
    
        # ls /dev/disk/by-uuid/

    如果 /dev/disk/by-id / 是使用，請更新 /boot/grub/menu.lst 和/等/fstab 適當的 uuid 值

    變更之前
    
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1

    變更後
    
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

10. 修改 udev 規則，以避免產生的乙太網路介面的靜態規則。 這些規則可能會導致問題，請在 Microsoft Azure 或超 v 虛擬機器時

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

11. 它會建議編輯檔案 」 / 等/sysconfig/網路/dhcp 」，並變更`DHCLIENT_SET_HOSTNAME`下列參數︰

        DHCLIENT_SET_HOSTNAME="no"

12. 在 「 / 等/sudoers 」，註解或如果有的話，請移除下列幾行︰

        Defaults targetpw   # ask for the password of the target user i.e. root
        ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

13. 確定 SSH 伺服器是安裝並設定為在啟動時啟動。  這通常是預設值。

14. 無法建立交換空間 OS 磁碟上。

    Azure Linux 代理程式可以自動設定使用本機資源磁碟上 Azure 佈建之後連接至 VM 的交換空間。 請注意，本機資源磁碟是一個*暫時性*的磁碟可能已清空時 VM 會取消提供。 之後安裝 Azure Linux 代理程式 （請參閱上一步），視情況修改中 /etc/waagent.conf 下列參數︰

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15. 執行下列命令以 deprovision 虛擬機器並備妥以供佈建 Azure 上︰

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16. 按一下 [HYPER-V 管理員中的 [**動作]-> [關閉向下**]。 您 Linux VHD 現在已準備就緒上, 傳至 Azure 的。


----------

## <a name="prepare-opensuse-131"></a>準備 openSUSE 13.1 + ##

1. HYPER-V 管理員的中央窗格中選取的虛擬機器。

2. 按一下 [**連線**至開啟虛擬機器視窗。

3. 在命令介面執行命令 '`zypper lr`」。 如果這個命令會傳回輸出類似下列所示，然後如預期般-不是必要的任何調整設定存放庫 （請注意，版本號碼可能會視情況而定）︰

        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes

    如果命令傳回 「...定義沒有存放庫] 然後將這些 repos 使用下列命令︰

        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates

    您可以然後確認已新增存放庫執行命令以 「`zypper lr`」 一次。 萬一相關更新存放庫的其中一個不會啟用，則會啟用下列命令︰

        # sudo zypper mr -e [NUMBER OF REPOSITORY]


4. 更新至最新版的核心︰

        # sudo zypper up kernel-default

    或更新系統的最新的更新︰

        # sudo zypper update

5.  安裝 Azure Linux 代理程式。

        # sudo zypper install WALinuxAgent

6.  修改核心開機行包含其他核心參數的 Azure 幼蟲設定中。 若要這麼做，請開啟 「 / boot/grub/menu.lst 」 文字編輯器中，並確保預設核心包含下列參數︰

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    這樣就能確定所有主控台訊息會都傳送到第一個序列連接埠，就可以協助 Azure 偵錯問題的支援。 此外，如果有的話，請從核心開機行移除下列參數︰

        libata.atapi_enabled=0 reserve=0x1f0,0x8

7.  它會建議編輯檔案 」 / 等/sysconfig/網路/dhcp 」，並變更`DHCLIENT_SET_HOSTNAME`下列參數︰

        DHCLIENT_SET_HOSTNAME="no"

8.  **重要︰**在 「 / 等/sudoers 」，註解或如果有的話，請移除下列幾行︰

        Defaults targetpw   # ask for the password of the target user i.e. root
        ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.  確定 SSH 伺服器是安裝並設定為在啟動時啟動。  這通常是預設值。

10. 無法建立交換空間 OS 磁碟上。

    Azure Linux 代理程式可以自動設定使用本機資源磁碟上 Azure 佈建之後連接至 VM 的交換空間。 請注意，本機資源磁碟是一個*暫時性*的磁碟可能已清空時 VM 會取消提供。 之後安裝 Azure Linux 代理程式 （請參閱上一步），視情況修改中 /etc/waagent.conf 下列參數︰

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. 執行下列命令以 deprovision 虛擬機器並備妥以供佈建 Azure 上︰

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

12. 請確定在啟動時執行 Azure Linux 代理程式︰

        # sudo systemctl enable waagent.service

13. 按一下 [HYPER-V 管理員中的 [**動作]-> [關閉向下**]。 您 Linux VHD 現在已準備就緒上, 傳至 Azure 的。

## <a name="next-steps"></a>後續步驟
現在，您準備好要使用的 SUSE Linux 虛擬硬碟 Azure 中建立新的虛擬機器。 如果這是您要上.vhd 檔案傳到 Azure 第一次，請參閱步驟 2 和 3 中[建立和上傳包含 Linux 作業系統的虛擬硬碟](virtual-machines-linux-classic-create-upload-vhd.md)。
