<properties
    pageTitle="建立和上傳 Azure 中使用紅色角色企業 Linux VHD"
    description="瞭解如何建立和上傳 Azure 虛擬硬碟 (VHD)，包含紅色角色 Linux 作業系統。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/17/2016"
    ms.author="mingzhan"/>


# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>準備 Azure 虛擬機器紅色角色為基礎

在本文中，您將學習如何紅色角色企業 Linux (RHEL) 虛擬機器準備 Azure 中使用。 本文涵蓋 RHEL 版本是 6.7、 7.1 和 7.2。 本文中所述的準備 Hypervisors 是 HYPER-V 的核心虛擬機器 (KVM)，以及 VMware。 如需有關紅色角色雲端存取程式的資格需求的詳細資訊，請參閱[紅色角色雲端存取網站](http://www.redhat.com/en/technologies/cloud-computing/cloud-access)和[Azure 上執行的 RHEL](https://access.redhat.com/articles/1989673)。

[從 HYPER-V 管理員準備 RHEL 6.7 虛擬機器](#rhel67hyperv)

[從 HYPER-V 管理員準備 RHEL 7.1/7.2 虛擬機器](#rhel7xhyperv)

[從 KVM 準備 RHEL 6.7 虛擬機器](#rhel67kvm)

[從 KVM 準備 RHEL 7.1/7.2 虛擬機器](#rhel7xkvm)

[從 VMware 準備 RHEL 6.7 虛擬機器](#rhel67vmware)

[從 VMware 準備 RHEL 7.1/7.2 虛擬機器](#rhel7xvmware)

[準備 RHEL 7.1/7.2 虛擬機器從啟動檔案](#rhel7xkickstart)


## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>從 HYPER-V 管理員準備紅色角色為基礎的虛擬機器
### <a name="prerequisites"></a>必要條件
本節假設您已安裝的 RHEL 圖像 （來自您是從紅色角色網站取得 ISO 檔案） 虛擬硬碟 (VHD)。 如需如何使用 HYPER-V 管理員安裝作業系統映像的詳細資訊，請參閱[安裝 HYPER-V 角色，並設定虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。

**RHEL 安裝備忘稿**

- 如需秘訣準備 Azure Linux 也請[一般 Linux 安裝備忘稿](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)。

- Azure 中不支援較新的 VHDX 格式。 您可以轉換成 VHD 格式的磁碟使用 HYPER-V 管理員或**轉換 vhd** PowerShell 指令程式。

- Vhd 必須建立為 「 固定 」-動態 Vhd 不受支援。

- 當您要安裝 Linux 系統時，我們建議您使用標準的磁碟分割區，而不是 LVM （通常許多安裝的預設值）。 特別是如果 OS 磁碟需要附加至另一個 VM 進行疑難排解，這樣就可避免複製 Vm LVM 名稱衝突。 如果喜好，LVM 或[RAID](virtual-machines-linux-configure-raid.md)可能資料磁碟上使用。

- 不要 OS 磁碟上設定交換磁碟分割。 您可以設定 Linux 代理程式建立暫存資源磁碟上的交換檔案。 使用下列步驟中相關資訊。

- 所有 Vhd 必須是 1 mb 的多重圖表的大小。

- 當您使用**qemu 影像**磁碟映像轉換 VHD 格式時，請注意，有已知的錯誤 qemu 影像版本 2.2.1 或更新版本。 此錯誤會產生不正確的格式 VHD。 問題是固定在即將推出的新版 qemu 影像。 現在，我們建議您使用 qemu 影像版本 2.2.0 或更舊版本。

### <a id="rhel67hyperv"></a>從 HYPER-V 管理員準備 RHEL 6.7 虛擬機器###


1.  在 [HYPER-V 管理員選取的虛擬機器。

2.  按一下 [**連線**至開啟虛擬機器主控台視窗。

3.  解除安裝 NetworkManager，藉由執行下列命令︰

        # sudo rpm -e --nodeps NetworkManager

    請注意，是否尚未安裝套件，此命令將會失敗的錯誤訊息。 這是可預期。

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

6.  移動 （或移除） udev 規則，以避免產生的乙太網路介面的靜態規則。 當您複製超 v 中 Microsoft Azure 虛擬機器這些規則會造成問題

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  請確定網路服務會開始在啟動時，執行下列命令︰

        # sudo chkconfig network on

8.  註冊您的紅色角色訂閱，才能從 RHEL 存放庫套件的安裝藉由執行下列命令︰

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9.  WALinuxAgent 套件`WALinuxAgent-<version>`已推到紅色角色的額外儲存機制。 執行下列命令以啟用 extras 存放庫︰

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

10. 修改核心開機行包含其他核心參數的 Azure 幼蟲設定中。 若要這麼做，請開啟`/boot/grub/menu.lst`文字編輯器中，並確保預設核心包含下列參數︰

        console=ttyS0
        earlyprintk=ttyS0
        rootdelay=300
        numa=off

    這也可確保所有主控台訊息會都傳送至第一個序列連接埠，就可以協助 Azure 偵錯問題的支援。 這會停用 NUMA，因為由 RHEL 6 核心版本中的錯誤。

    除了上面的動作，我們建議您移除下列參數︰

        rhgb quiet crashkernel=auto

    圖形和安靜開機並不能用在雲端環境中想要傳送到序列連接埠的所有記錄。

    Crashkernel 選項可向左設定如有需要，但請注意，此參數會減少 128 MB 以上 VM 中可用的記憶體。 這可能是較小 VM 會造成問題。

11. 確定 SSH 伺服器是安裝並設定為在啟動時啟動。 這通常是預設值。 修改 /etc/ssh/sshd_config 来包含的下列行︰

        ClientAliveInterval 180

12. 安裝 Azure Linux 代理程式執行下列命令︰

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

    請注意，安裝 WALinuxAgent 套件，將會移除 NetworkManager NetworkManager gnome 封包是否沒有已經移除所述的步驟 2。

13. 無法建立交換空間 OS 磁碟上。
Azure Linux 代理程式可以使用本機資源磁碟附加至 VM VM 已在 Azure 佈建後，會自動設定交換空間。 請注意，本機資源磁碟是一個暫時性的磁碟可能已清空時 VM 會取消提供。 在您安裝 Azure Linux 代理程式後 （請參閱上一步），視情況修改中 /etc/waagent.conf 下列參數︰

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. 註冊訂閱 （如有需要），執行下列命令︰

        # sudo subscription-manager unregister

15. 執行下列命令以 deprovision 虛擬機器並備妥以供佈建 Azure 上︰

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16. 按一下 [**動作 > 關閉**在 HYPER-V 管理員。 您 Linux VHD 現在已準備就緒上, 傳至 Azure 的。
 

### <a id="rhel7xhyperv"></a>從 HYPER-V 管理員準備 RHEL 7.1/7.2 虛擬機器###

1.  在 [HYPER-V 管理員選取的虛擬機器。

2.  按一下 [**連線**至開啟虛擬機器主控台視窗。

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

5.  請確定網路服務會開始在啟動時，執行下列命令︰

        # sudo chkconfig network on

6.  註冊您的紅色角色訂閱，才能從 RHEL 存放庫套件的安裝藉由執行下列命令︰

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.  修改核心開機行幼蟲設定来包括額外的核心參數 Azure 中。 若要這麼做，請開啟`/etc/default/grub`在文字編輯器，並編輯**GRUB_CMDLINE_LINUX**參數。 例如︰

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    這也可確保所有主控台訊息會都傳送至第一個序列連接埠，就可以協助 Azure 偵錯問題的支援。 除了上面的動作，我們建議您移除下列參數︰

        rhgb quiet crashkernel=auto

    圖形和安靜開機並不能用在雲端環境中想要傳送到序列連接埠的所有記錄。
    Crashkernel 選項可向左設定如有需要，但請注意，此參數會減少 128 MB 以上 VM 中可用的記憶體。 這可能是較小 VM 會造成問題。

8.  完成後編輯`/etc/default/grub`，執行下列命令以重建幼蟲設定︰

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9.  確定 SSH 伺服器是安裝並設定為在啟動時啟動。 這通常是預設值。 修改`/etc/ssh/sshd_config`要包含的下列行︰

        ClientAliveInterval 180

10. WALinuxAgent 套件`WALinuxAgent-<version>`已推到紅色角色的額外儲存機制。 執行下列命令以啟用 extras 存放庫︰

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

11. 安裝 Azure Linux 代理程式執行下列命令︰

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

12. 無法建立交換空間 OS 磁碟上。 Azure Linux 代理程式可以使用本機資源磁碟附加至 VM VM 已在 Azure 佈建後，會自動設定交換空間。 請注意，本機資源磁碟是一個暫時性的磁碟可能已清空時 VM 會取消提供。 在您安裝 Azure Linux 代理程式後 （請參閱上一步） 中的下列參數`/etc/waagent.conf`適當︰

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. 如果您要移除註冊訂閱，請執行下列命令︰

        # sudo subscription-manager unregister

14. 執行下列命令以 deprovision 虛擬機器並備妥以供佈建 Azure 上︰

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15. 按一下 [**動作 > 關閉**在 HYPER-V 管理員。 您 Linux VHD 現在已準備就緒上, 傳至 Azure 的。
 


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>從 KVM 準備紅色角色為基礎的虛擬機器

### <a id="rhel67kvm"></a>從 KVM 準備 RHEL 6.7 虛擬機器###


1.  紅色角色的網站下載 RHEL 6.7 KVM 影像。

2.  根密碼設定。

    產生加密的密碼，然後複製命令的輸出︰

        # openssl passwd -1 changeme

    設定 guestfish 根密碼︰

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    變更的第二個欄位從根使用者的 「 ！ 」 若要加密的密碼。

3.  建立虛擬機器中 KVM 從 qcow2 圖像、 **qcow2**，以設定磁碟類型及虛擬網路介面裝置型號設**virtio**。 然後啟動虛擬機器並根身分登入。

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

6.  移動 （或移除） udev 規則，以避免產生的乙太網路介面的靜態規則。 當您複製超 v 中 Microsoft Azure 虛擬機器這些規則會造成問題

        # mkdir -m 0700 /var/lib/waagent
        # mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  請確定網路服務會開始在啟動時，執行下列命令︰

        # chkconfig network on

8.  註冊您的紅色角色訂閱，才能從 RHEL 存放庫套件的安裝藉由執行下列命令︰

        # subscription-manager register --auto-attach --username=XXX --password=XXX

9.  修改核心開機行包含其他核心參數的 Azure 幼蟲設定中。 若要這麼做，請開啟`/boot/grub/menu.lst`文字編輯器中，並確保預設核心包含下列參數︰

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    這也可確保所有主控台訊息會都傳送至第一個序列連接埠，就可以協助 Azure 偵錯問題的支援。 這會停用 NUMA，因為由 RHEL 6 核心版本中的錯誤。

    除了上面的動作，我們建議您移除下列參數︰

        rhgb quiet crashkernel=auto

    圖形和安靜開機並不能用在雲端環境中想要傳送到序列連接埠的所有記錄。
    Crashkernel 選項可能會向左設定如有需要，但請注意，此參數會減少 128 MB 以上 VM 中可用的記憶體。 這可能是較小 VM 會造成問題。

10. 將 initramfs HYPER-V 模組︰  

    編輯`/etc/dracut.conf`及新增內容︰ add_drivers + = 」 hv_vmbus hv_netvsc hv_storvsc 」

    重建 initramfs: # dracut – f v

11. 解除安裝雲端初始化︰

        # yum remove cloud-init

12. 請確定 SSH 伺服器是安裝並設定為在啟動時啟動︰

        # chkconfig sshd on

    修改 /etc/ssh/sshd_config 包含下列行︰

        PasswordAuthentication yes
        ClientAliveInterval 180

    重新啟動 sshd:

        # service sshd restart

13. WALinuxAgent 套件`WALinuxAgent-<version>`已推到紅色角色的額外儲存機制。 執行下列命令以啟用 extras 存放庫︰

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

14. 安裝 Azure Linux 代理程式執行下列命令︰

        # yum install WALinuxAgent
        # chkconfig waagent on

15. Azure Linux 代理程式可以使用本機資源磁碟附加至 VM VM 已在 Azure 佈建後，會自動設定交換空間。 請注意，本機資源磁碟是一個暫時性的磁碟可能已清空時 VM 會取消提供。 在您安裝 Azure Linux 代理程式後 （請參閱上一步），視情況修改中**/etc/waagent.conf**下列參數︰

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. 註冊訂閱 （如有需要），執行下列命令︰

        # subscription-manager unregister

17. 執行下列命令以 deprovision 虛擬機器並備妥以供佈建 Azure 上︰

        # waagent -force -deprovision
        # export HISTSIZE=0
        # logout

18. 關閉中 KVM VM。

19. 將 qcow2 圖像轉換成 VHD 格式。
    第一次將圖像轉換成原始的格式︰

         # qemu-img convert -f qcow2 –O raw rhel-6.7.qcow2 rhel-6.7.raw
    請確認您原始圖像的大小會對齊 1 MB。 否則，無條件進位到 1 MB 對齊大小: # MB = $((1024*1024)) # 大小 = $(qemu 影像資訊 f 原始-輸出 json 」 rhel-6.7.raw 」 | \ gawk ' 符合 ($0，/ 「 虛擬大小]: ([0-9] +)，/ val) {列印 val[1]}') # rounded_size = $((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-6.7.raw $rounded_size

    原始的磁碟轉換為固定大小 VHD:

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xkvm"></a>從 KVM 準備 RHEL 7.1/7.2 虛擬機器###


1.  紅色角色網站下載 KVM 圖像 RHEL 7.1 （或 7.2）。 我們將在以下範例使用 RHEL 7.1。

2.  根密碼設定。

    產生加密的密碼，然後複製命令的輸出︰

        # openssl passwd -1 changeme

    設定 guestfish 根密碼。

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    變更的第二個欄位從根使用者的 「 ！ 」 若要加密的密碼。

3.  建立虛擬機器中 KVM 從 qcow2 圖像、 **qcow2**，以設定磁碟類型及虛擬網路介面裝置型號設**virtio**。 然後啟動虛擬機器並根身分登入。

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

6.  請確定網路服務會開始在啟動時，執行下列命令︰

        # chkconfig network on

7.  註冊您的紅色角色訂閱，才能從 RHEL 存放庫套件的安裝藉由執行下列命令︰

        # subscription-manager register --auto-attach --username=XXX --password=XXX

8.  修改核心開機行包含其他核心參數的 Azure 幼蟲設定中。 若要這麼做，請開啟`/etc/default/grub`在文字編輯器，並編輯**GRUB_CMDLINE_LINUX**參數。 例如︰

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    這也可確保所有主控台訊息會都傳送至第一個序列連接埠，就可以協助 Azure 偵錯問題的支援。 除了上面的動作，我們建議您移除下列參數︰

        rhgb quiet crashkernel=auto

    圖形和安靜開機並不能用在雲端環境中想要傳送到序列連接埠的所有記錄。
    Crashkernel 選項可向左設定如有需要，但請注意，此參數會減少 128 MB 以上 VM 中可用的記憶體。 這可能是較小 VM 會造成問題。

9.  完成後編輯`/etc/default/grub`，執行下列命令以重建幼蟲設定︰

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10. 將 initramfs HYPER-V 模組︰

    編輯`/etc/dracut.conf`及新增內容︰

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    重建 initramfs:

        # dracut –f -v

11. 解除安裝雲端初始化︰

        # yum remove cloud-init

12. 請確定 SSH 伺服器是安裝並設定為在啟動時啟動︰

        # systemctl enable sshd

    修改 /etc/ssh/sshd_config 包含下列行︰

        PasswordAuthentication yes
        ClientAliveInterval 180

    重新啟動 sshd:

        systemctl restart sshd

13. WALinuxAgent 套件`WALinuxAgent-<version>`已推到紅色角色的額外儲存機制。 執行下列命令以啟用 extras 存放庫︰

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

14. 安裝 Azure Linux 代理程式執行下列命令︰

        # yum install WALinuxAgent

    啟用 waagent 服務︰

        # systemctl enable waagent.service

15. 無法建立交換空間 OS 磁碟上。 Azure Linux 代理程式可以使用本機資源磁碟附加至 VM VM 已在 Azure 佈建後，會自動設定交換空間。 請注意，本機資源磁碟是一個暫時性的磁碟可能已清空時 VM 會取消提供。 在您安裝 Azure Linux 代理程式後 （請參閱上一步） 中的下列參數`/etc/waagent.conf`適當︰

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. 註冊訂閱 （如有需要），執行下列命令︰

        # subscription-manager unregister

17. 執行下列命令以 deprovision 虛擬機器並備妥以供佈建 Azure 上︰

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

18. 關閉虛擬機器中 KVM。

19. 將 qcow2 圖像轉換成 VHD 格式。

    第一次將圖像轉換成原始的格式︰

         # qemu-img convert -f qcow2 –O raw rhel-7.1.qcow2 rhel-7.1.raw

    請確認您原始圖像的大小會對齊 1 MB。 否則，無條件進位到 1 MB 對齊大小︰

         # MB=$((1024*1024))
         # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                  gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
         # rounded_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-7.1.raw $rounded_size

    原始的磁碟轉換為固定大小 VHD:

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>從 VMware 準備紅色角色為基礎的虛擬機器
### <a name="prerequisites"></a>必要條件
本節假設您已經有安裝 RHEL 虛擬機器中 VMware。 如何安裝作業系統中 VMware 的詳細資訊，請參閱[VMware 來賓作業系統安裝指南](http://partnerweb.vmware.com/GOSIG/home.html)。

- 當您安裝 Linux 作業系統時，我們建議您使用標準的磁碟分割區，而不是 LVM （通常許多安裝的預設值）。 特別是如果 OS 磁碟需要附加至另一個 VM 進行疑難排解，這樣就可避免複製 Vm LVM 名稱衝突。 如果喜好，LVM 或 RAID 可以資料磁碟上使用。

- 不要 OS 磁碟上設定交換磁碟分割。 您可以設定 Linux 代理程式建立暫存資源磁碟上的交換檔案。 下列步驟中，您可以找到相關資訊。

- 當您建立虛擬硬碟時，選取**存放區虛擬磁碟當作單一檔案**。



### <a id="rhel67vmware"></a>從 VMware 準備 RHEL 6.7 虛擬機器###

1.  解除安裝 NetworkManager，藉由執行下列命令︰

         # sudo rpm -e --nodeps NetworkManager

    請注意，是否尚未安裝套件，此命令將會失敗的錯誤訊息。 這是可預期。

2.  建立一個名為**網路**/etc/sysconfig/目錄中包含下列的文字檔案︰

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

3.  建立一個名為**ifcfg eth0**中 /etc/sysconfig/network-scripts 檔案 / 目錄，包含下列的文字︰

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

4.  移動 （或移除） udev 規則，以避免產生的乙太網路介面的靜態規則。 當您複製超 v 中 Microsoft Azure 虛擬機器這些規則會造成問題

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

5.  請確定網路服務會開始在啟動時，執行下列命令︰

        # sudo chkconfig network on

6.  註冊您的紅色角色訂閱，才能從 RHEL 存放庫套件的安裝藉由執行下列命令︰

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.  WALinuxAgent 套件`WALinuxAgent-<version>`已推到紅色角色的額外儲存機制。 執行下列命令以啟用 extras 存放庫︰

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

8.  修改核心開機行包含其他核心參數的 Azure 幼蟲設定中。 若要這麼做，請開啟 「 / boot/grub/menu.lst 」 文字編輯器中，並確保預設核心包含下列參數︰

        console=ttyS0
        earlyprintk=ttyS0
        rootdelay=300
        numa=off

    這也可確保所有主控台訊息會都傳送至第一個序列連接埠，就可以協助 Azure 偵錯問題的支援。 這會停用 NUMA，因為由 RHEL 6 核心版本中的錯誤。
    除了上面的動作，我們建議您移除下列參數︰

        rhgb quiet crashkernel=auto

    圖形和安靜開機並不能用在雲端環境中想要傳送到序列連接埠的所有記錄。
    Crashkernel 選項可向左設定如有需要，但請注意，此參數會減少 128 MB 以上 VM 中可用的記憶體。 這可能是較小 VM 會造成問題。

9.  將 initramfs HYPER-V 模組︰

        Edit `/etc/dracut.conf` and add content:

            add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

        Rebuild initramfs:

            # dracut –f -v

10. 確定 SSH 伺服器是安裝並設定為在啟動時啟動。 這通常是預設值。 修改`/etc/ssh/sshd_config`要包含的下列行︰

        ClientAliveInterval 180

11. 安裝 Azure Linux 代理程式執行下列命令︰

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

12. 無法建立交換空間 OS 磁碟上︰

    Azure Linux 代理程式可以使用本機資源磁碟附加至 VM VM 已在 Azure 佈建後，會自動設定交換空間。 請注意，本機資源磁碟是一個暫時性的磁碟可能已清空時 VM 會取消提供。 在您安裝 Azure Linux 代理程式後 （請參閱上一步） 中的下列參數`/etc/waagent.conf`適當︰

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. 註冊訂閱 （如有需要），執行下列命令︰

        # sudo subscription-manager unregister

14. 執行下列命令以 deprovision 虛擬機器並備妥以供佈建 Azure 上︰

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15. 關閉 [VM，並將 VMDK 檔案轉換為.vhd 檔案。

    第一次將圖像轉換成原始的格式︰

        # qemu-img convert -f vmdk –O raw rhel-6.7.vmdk rhel-6.7.raw

    請確認您原始圖像的大小會對齊 1 MB。 否則，無條件進位到 1 MB 對齊大小︰

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \
                gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.7.raw $rounded_size

    原始的磁碟轉換為固定大小 VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xvmware"></a>從 VMware 準備 RHEL 7.1/7.2 虛擬機器###

1.  建立一個名為**網路**/etc/sysconfig/目錄中包含下列的文字檔案︰

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2.  建立一個名為**ifcfg eth0**中 /etc/sysconfig/network-scripts 檔案 / 目錄，包含下列的文字︰

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

3.  請確定網路服務會開始在啟動時，執行下列命令︰

        # sudo chkconfig network on

4.  註冊您的紅色角色訂閱，才能從 RHEL 存放庫套件的安裝藉由執行下列命令︰

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5.  修改核心開機行包含其他核心參數的 Azure 幼蟲設定中。 若要這麼做，請開啟`/etc/default/grub`在文字編輯器，並編輯**GRUB_CMDLINE_LINUX**參數。 例如︰

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    這也可確保所有主控台訊息會都傳送至第一個序列連接埠，就可以協助 Azure 偵錯問題的支援。 除了上面的動作，我們建議您移除下列參數︰

        rhgb quiet crashkernel=auto

    圖形和安靜開機並不能用在雲端環境中想要傳送到序列連接埠的所有記錄。
    Crashkernel 選項可向左設定如有需要，但請注意，此參數會減少 128 MB 以上 VM 中可用的記憶體。 這可能是較小 VM 會造成問題。

6.  完成後編輯`/etc/default/grub`，執行下列命令以重建幼蟲設定︰

         # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7.  將 initramfs HYPER-V 模組︰

    編輯`/etc/dracut.conf`，新增內容︰

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    重建 initramfs:

        # dracut –f -v

8.  確定 SSH 伺服器是安裝並設定為在啟動時啟動。 這通常是預設值。 修改`/etc/ssh/sshd_config`要包含的下列行︰

        ClientAliveInterval 180

9.  WALinuxAgent 套件`WALinuxAgent-<version>`已推到紅色角色的額外儲存機制。 執行下列命令以啟用 extras 存放庫︰

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

10. 安裝 Azure Linux 代理程式執行下列命令︰

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

11. 無法建立交換空間 OS 磁碟上。 Azure Linux 代理程式可以使用本機資源磁碟附加至 VM VM 已在 Azure 佈建後，會自動設定交換空間。 請注意，本機資源磁碟是一個暫時性的磁碟可能已清空時 VM 會取消提供。 在您安裝 Azure Linux 代理程式後 （請參閱上一步） 中的下列參數`/etc/waagent.conf`適當︰

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12. 如果您要移除註冊訂閱，請執行下列命令︰

        # sudo subscription-manager unregister

13. 執行下列命令以 deprovision 虛擬機器並備妥以供佈建 Azure 上︰

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14. 關閉 [VM，並將 VMDK 檔案轉換為 VHD 格式。

    第一次將圖像轉換成原始的格式︰

        # qemu-img convert -f vmdk –O raw rhel-7.1.vmdk rhel-7.1.raw

    請確認您原始圖像的大小會對齊 1 MB。 否則，無條件進位到 1 MB 對齊大小︰

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                 gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.1.raw $rounded_size

    原始的磁碟轉換為固定大小 VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>準備從 ISO 的紅色角色為基礎的虛擬機器自動使用啟動檔案


### <a id="rhel7xkickstart"></a>準備 RHEL 7.1/7.2 虛擬機器從啟動檔案###


1.  建立啟動檔案內容，然後儲存檔案。 如需啟動安裝的詳細資訊，請參閱[啟動安裝指南](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html)。



        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
        auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff



        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=yes
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

2.  將啟動檔案放在安裝系統可存取的地方。

3.  在 HYPER-V 管理員建立新的 VM。 在 [**連線的虛擬硬碟**] 頁面上選取**附加虛擬硬碟更新版本**，並完成新增虛擬機器精靈。

4.  開啟 VM 設定︰

    。  將新的虛擬硬碟附加的 vm。 請務必選取 [ **VHD 格式**及**固定的大小**。

    b。  將安裝 ISO 附加至 DVD 光碟機中。

    c。  設定從 CD 開機 BIOS。

5.  啟動 VM。 安裝指南出現時，按下**] 索引標籤**來設定啟動選項。

6.  輸入`inst.ks=<the location of the kickstart file>`結尾的啟動選項]，然後按下**enter 鍵**。

7.  安裝完成，請等候。 完成後，VM 將會自動關閉。 您 Linux VHD 現在已準備就緒上, 傳至 Azure 的。

## <a name="known-issues"></a>已知的問題
當您使用的 RHEL 7.1 HYPER-V 和 Azure 中時，則會已知問題。

### <a name="disk-io-freeze"></a>磁碟 I/O 凍結

在 HYPER-V 和 Azure RHEL 7.1 經常儲存磁碟 I/O 活動期間可能會發生這個問題。   

重現率︰

此問題是間歇性發生。 不過，它就會發生更多經常期間程度應 HYPER-V 和 Azure 中的磁碟 I/O 作業。   


[AZURE.NOTE] 以紅色角色已經已處理此已知的問題。 若要安裝相關聯的修正方式，請執行下列命令︰

    # sudo yum update

### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>HYPER-V 驅動程式可能不會包含在初始的 RAM 磁碟使用非 HYPER-V hypervisor 時

在某些情況下，Linux 安裝程式可能會併入驅動程式的 HYPER-V 初始的 RAM 磁碟 （initrd 或 initramfs） 除非偵測到它正在執行 HYPER-V 環境中。

當您準備 Linux 圖像使用不同的虛擬化系統 （亦即 Virtualbox、 Xen 等） 時，您可能需要重建 initrd，以確保至少 hv_vmbus 和 hv_storvsc 核心模組可在初始的 RAM 磁碟上。 這至少根據上游紅色角色分配系統是已知的問題。

若要解決此問題，您需要將 initramfs HYPER-V 模組重建其︰

編輯`/etc/dracut.conf`及新增內容︰

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

重建 initramfs:

        # dracut –f -v

如需詳細資訊，請參閱[重建 initramfs](https://access.redhat.com/solutions/1958)的相關資訊。

## <a name="next-steps"></a>後續步驟
現在，您準備好要使用的紅色角色企業 Linux 虛擬硬碟 Azure 中建立新的虛擬機器。 如果這是您要上.vhd 檔案傳到 Azure 第一次，請參閱步驟 2 和 3 中[建立和上傳包含 Linux 作業系統的虛擬硬碟](virtual-machines-linux-classic-create-upload-vhd.md)。

如需取得認證執行紅色角色企業 Linux hypervisors 的詳細資訊，請參閱[紅色角色網站](https://access.redhat.com/certified-hypervisors)。
