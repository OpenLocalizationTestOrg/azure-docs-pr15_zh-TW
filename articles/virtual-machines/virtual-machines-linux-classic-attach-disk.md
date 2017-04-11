<properties
    pageTitle="將光碟附加至 Linux VM |Microsoft Azure"
    description="瞭解如何將資料磁碟附加至執行 Linux Azure 虛擬機器，並將它初始化，因此可供使用。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="iainfou"/>

# <a name="how-to-attach-a-data-disk-to-a-linux-virtual-machine"></a>如何將資料磁碟附加到 Linux 虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]請參閱如何[附加使用資源管理員部署模型資料磁碟](virtual-machines-linux-add-disk.md)。

您可以附加同時空白及磁碟包含您的 Azure Vm 的資料。 這兩種類型的磁碟是.vhd 檔案位於 Azure 儲存體帳戶。 為與 Linux 電腦，新增任何磁碟附加磁碟之後您要初始化並設定其格式，因此可供使用。 附加同時空白及磁碟已經包含資料至您的 Vm，以及如何然後初始化及格式化新磁碟此文件詳細資料。

> [AZURE.NOTE]最好使用一或多個不同的磁碟儲存虛擬機器中的資料。 當您建立 Azure 虛擬機器時，其作業系統磁碟和暫存的磁碟。 **不要使用暫存的磁碟儲存常設的資料。** 顧名思義，它會提供暫時存放區。 因為它不會位於 Azure 儲存體中提供任何重複或備份。
> 暫時磁碟是 Azure Linux 代理程式通常管理，並且自動裝載**/mnt/resource** （或**/mnt** Ubuntu 圖像上）。 另一方面，資料磁碟名稱將由 Linux 核心類似`/dev/sdc`，和您要分割、 格式化及裝載此資源。 請參閱[Azure Linux 代理程式使用者指南][Agent]如需詳細資訊。

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-linux.md)]

## <a name="initialize-a-new-data-disk-in-linux"></a>初始化 Linux 的新資料磁碟

1. 您 vm SSH。 如需詳細資訊，請參閱[如何登入虛擬機器執行 Linux][Logon]。

2. 接下來，您需要找出初始化資料磁碟的裝置識別碼。 有兩種方式來執行這項作業︰

    a) Grep 的記錄，例如下列命令中步︰

            $sudo grep SCSI /var/log/messages

    最近 Ubuntu 散發，您可能需要使用`sudo grep SCSI /var/log/syslog`因為登入`/var/log/messages`可能會依預設停用。

    您可以找到新增會顯示的訊息中的最後一個資料磁碟的識別碼。

    ![取得磁碟訊息](./media/virtual-machines-linux-classic-attach-disk/scsidisklog.png)

    OR

    b） 使用`lsscsi`] 命令，找出裝置識別碼。 `lsscsi`以 [安裝`yum install lsscsi`（紅色角色基礎散佈） 或`apt-get install lsscsi`（Debian 基礎散佈）。 您可以找到您要尋找由其_lun_或**邏輯單位的數字**的磁碟。 例如，您所附加的磁碟_lun_容易看到從`azure vm disk list <virtual-machine>`為︰

            ~$ azure vm disk list TestVM
            info:    Executing command vm disk list
            + Fetching disk images
            + Getting virtual machines
            + Getting VM disks
            data:    Lun  Size(GB)  Blob-Name                         OS
            data:    ---  --------  --------------------------------  -----
            data:         30        TestVM-2645b8030676c8f8.vhd  Linux
            data:    0    100       TestVM-76f7ee1ef0f6dddc.vhd
            info:    vm disk list command OK

    比較此資料輸出`lsscsi`的相同範例虛擬機器︰

            ops@TestVM:~$ lsscsi
            [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
            [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
            [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
            [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc

    在每個資料列中 tuple 的最後一個數字是_lun_。 請參閱`man lsscsi`如需詳細資訊。

3. 出現提示時，輸入下列命令以建立您的裝置︰

        $sudo fdisk /dev/sdc


4. 出現提示時，輸入要建立新的分割**n** 。


    ![建立裝置](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartition.png)

5. 出現提示時，請輸入**p**進行磁碟分割的主要磁碟分割。 輸入**1** ，使其第一個磁碟分割，然後輸入輸入接受圓柱圖的預設值。 在某些系統，可以顯示預設值的第一個和最後一︰ 類股，而不是圓柱圖。 您可以選擇接受預設值。


    ![建立分割](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartdetails.png)



6. 輸入**p**若要查看的磁碟分割的相關詳細資料。


    ![清單磁碟資訊](./media/virtual-machines-linux-classic-attach-disk/fdiskpartitiondetails.png)



7. 輸入**w**撰寫磁碟的設定。


    ![撰寫磁碟上的變更](./media/virtual-machines-linux-classic-attach-disk/fdiskwritedisk.png)

8. 現在您可以建立的檔案系統上新的磁碟分割。 將的磁碟分割號碼新增至裝置識別碼 (在下列範例`/dev/sdc1`)。 下列範例會建立一個 ext4 磁碟分割 /dev/sdc1 上︰

        # sudo mkfs -t ext4 /dev/sdc1

    ![建立檔案系統](./media/virtual-machines-linux-classic-attach-disk/mkfsext4.png)

    >[AZURE.NOTE] SuSE Linux 企業 11 系統只支援 ext4 檔案系統的唯讀存取權限。 這些系統，建議新的檔案系統的格式設定為 ext3，而不是 ext4。


9. 建立目錄裝載新的檔案系統，如下所示︰

        # sudo mkdir /datadrive


10. 最後您可以將磁碟機裝載，，如下所示︰

        # sudo mount /dev/sdc1 /datadrive

    資料磁碟已經準備好使用為**/datadrive**。
    
    ![建立目錄，並裝載磁碟](./media/virtual-machines-linux-classic-attach-disk/mkdirandmount.png)


11. 新增 /etc/fstab 新的磁碟機︰

    若要確保磁碟機，再重新裝載自動重新啟動電腦之後，必須新增至/等/fstab 檔案。 此外，建議 UUID （通用唯一識別碼） 用於 /etc/fstab 中參照的磁碟機，而不是只裝置名稱 (亦即 /dev/sdc1)。 使用 UUID 避免不正確的磁碟期間開機及任何剩餘的資料磁碟，然後指派這些裝置識別碼 OS 偵測到的磁碟錯誤，無法連接至指定的位置。 若要尋找的新的磁碟機 UUID，您可以使用**blkid**公用程式︰

        # sudo -i blkid

    輸出看起來如下︰

        /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
        /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
        /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"


    >[AZURE.NOTE] 不正確編輯**/etc/fstab**檔案，可能會導致無法開機。 如果不確定，請參閱如何正確地編輯此檔案的詳細資訊的通訊群組的文件。 建議的編輯前請先建立 /etc/fstab 檔案的備份。

    接下來，請在文字編輯器中開啟**/etc/fstab**檔案︰

        # sudo vi /etc/fstab

    在此範例中，我們使用先前的步驟，並掛接點**/datadrive**中所建立的新**/dev/sdc1**裝置的 UUID 值。 新增**/etc/fstab**檔案結尾的下列行︰

        UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2

    或者，您也可以根據 SuSE Linux 系統，您可能需要使用的位置略有不同的格式︰

        /dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults,nofail   1   2
    
    >[AZURE.NOTE] `nofail`選項可確保 VM 開始，即使檔案系統已損毀，或在啟動時不存在的磁碟。 如果沒有這個選項，您可能會遇到行為中[無法 SSH FSTAB 錯誤而 Linux vm](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)所述。

    您現在可以測試檔案系統正確裝載卸載，然後裝載檔案系統，亦即 使用範例連接點`/datadrive`建立的舊版的步驟進行︰

        # sudo umount /datadrive
        # sudo mount /datadrive

    如果`mount`] 命令會產生錯誤、 / 等/fstab 檔案以取得正確的語法。 如果其他資料磁碟機或磁碟分割區在建立/等/fstab 另外也將其輸入。

    請使用這個命令可寫入的磁碟機︰

        # sudo chmod go+w /datadrive

>[AZURE.NOTE] 隨後但不能編輯 fstab 移除資料磁碟可能會導致失敗開機 VM。 如果這是常見的項目時，大部分的散佈提供 [`nofail`及/或`nobootwait`fstab 選項，可讓系統開機，即使磁碟，但無法裝載在啟動時間。 如需這些參數的詳細資訊，請參閱累加分配的文件。

### <a name="trimunmap-support-for-linux-in-azure"></a>Azure 中 Linux TRIM/UNMAP 支援
某些 Linux 核心支援 TRIM/UNMAP 作業，若要放棄磁碟上未使用過的區塊。 這些是作業標準儲存體通知 Azure 刪除頁面的已不再有效，並可捨棄特別有用。 放棄頁面可以儲存成本，如果您建立大型檔案，然後將它們刪除。

有兩種方法可以啟用 TRIM 支援您 Linux VM。 如同以往，諮詢您的通訊群組的建議的方法︰

- 使用`discard`裝載中的選項`/etc/fstab`，例如︰

        UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2

- 或者，您可以執行`fstrim`] 命令手動從命令列，或將其新增至您 crontab 定期執行︰

    **Ubuntu**

        # sudo apt-get install util-linux
        # sudo fstrim /datadrive

    **RHEL/CentOS**

        # sudo yum install util-linux
        # sudo fstrim /datadrive

## <a name="troubleshooting"></a>疑難排解
[AZURE.INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]


## <a name="next-steps"></a>後續步驟
您可以閱讀更多關於使用您 Linux VM 下列文章中︰

- [如何登入執行 Linux 虛擬機器][Logon]

- [如何卸離從 Linux 虛擬機器磁碟](virtual-machines-linux-classic-detach-disk.md)

- [Azure CLI 使用傳統部署模型](../virtual-machines-command-line-tools.md)

<!--Link references-->
[Agent]: virtual-machines-linux-agent-user-guide.md
[Logon]: virtual-machines-linux-mac-create-ssh-keys.md
