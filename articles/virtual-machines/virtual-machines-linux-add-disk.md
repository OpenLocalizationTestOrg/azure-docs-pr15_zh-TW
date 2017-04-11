<properties
    pageTitle="將磁碟新增 Linux vm |Microsoft Azure"
    description="瞭解如何在您 Linux vm 新增常設磁碟"
    keywords="linux 虛擬機器中，新增資源磁碟"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rickstercdn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.topic="article"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.date="09/06/2016"
    ms.author="rclaus"/>

# <a name="add-a-disk-to-a-linux-vm"></a>新增 Linux VM 磁碟

本文將示範如何將附加常設磁碟您 VM，好讓您可以保留您的資料-即使您 VM reprovisioned 進行的維修作業或調整其大小。 若要新增光碟，您需要在資源管理員模式中設定[Azure CLI](../xplat-cli-install.md) (`azure config mode arm`)。  

## <a name="quick-commands"></a>快速命令

在下列命令範例，之間的值取代&lt;和&gt;使用自己的環境中的值。

```bash
azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>
```

## <a name="attach-a-disk"></a>附加磁碟

附加新磁碟是快速。 輸入`azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>`建立並附加您 VM 新 GB 磁碟。 如果不明確識別儲存帳戶，您建立的任何磁碟放置在同一個儲存帳戶 OS 磁碟的所在位置。  外觀看起來如下所示︰

```bash
azure vm disk attach-new myuniquegroupname myuniquevmname 5
```

輸出

```bash
info:    Executing command vm disk attach-new
+ Looking up the VM "myuniquevmname"
info:    New data disk location: https://cliexxx.blob.core.windows.net/vhds/myuniquevmname-20150526-0xxxxxxx43.vhd
+ Updating VM "myuniquevmname"
info:    vm disk attach-new command OK
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>連線至 Linux VM 裝載新磁碟

> [AZURE.NOTE] 本主題會連線到 VM 使用使用者名稱和密碼。 若要使用公開及私密金鑰組與您 VM 通訊，請參閱[如何使用 SSH Linux Azure 上使用](virtual-machines-linux-mac-create-ssh-keys.md)。 您可以修改**SSH**連線的 Vm 以建立`azure vm quick-create`使用 command`azure vm reset-access`命令，以完全重設**SSH**存取、 新增或移除使用者，或新增至安全的存取公用索引鍵的檔案。

您需要將您的 Azure VM 分割 SSH，以設定格式，請供您 Linux VM 使用裝載您的新磁碟。 如果您不熟悉與**ssh**連線，[] 命令的形式`ssh <username>@<FQDNofAzureVM> -p <the ssh port>`，看起來會像下列動作︰

```bash
ssh ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com -p 22
```

輸出

```bash
The authenticity of host 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com's password:
Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-37-generic x86_64)

* Documentation:  https://help.ubuntu.com/

System information as of Fri May 22 21:02:32 UTC 2015

System load: 0.37              Memory usage: 2%   Processes:       207
Usage of /:  41.4% of 1.94GB   Swap usage:   0%   Users logged in: 0

Graph this data and manage this system at:
  https://landscape.canonical.com/

Get cloud support with Ubuntu Advantage Cloud Guest:
  http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

ops@myuniquevmname:~$
```

現在，您連線到您 VM，您準備好要附加的磁碟。  第一次尋找磁碟，使用`dmesg | grep SCSI`（您用來探索新磁碟的方法可能會視情況而定）。 在此情況下，它看起來類似︰

```bash
dmesg | grep SCSI
```

輸出

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

與此主題，如果`sdc`磁碟是我們想要的項目。 使用磁碟現在分割`sudo fdisk /dev/sdc`--假設您的大小寫的磁碟`sdc`，使其主要的磁碟磁碟分割區 1，並接受的預設值。

```bash
sudo fdisk /dev/sdc
```

輸出

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

建立分割輸入`p`出現提示時︰

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

對和寫入檔案系統磁碟分割使用**mkfs** ] 命令，指定您要的檔案系統的類型和裝置的名稱。 在本主題中，我們將使用`ext4`和`/dev/sdc1`從上方︰

```bash
sudo mkfs -t ext4 /dev/sdc1
```

輸出

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

現在，我們建立裝載檔案系統使用目錄`mkdir`:

```bash
sudo mkdir /datadrive
```

您裝載目錄使用`mount`:

```bash
sudo mount /dev/sdc1 /datadrive
```

資料磁碟現在已可供使用為`/datadrive`。

```bash
ls
```

輸出

```bash
bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var
```

若要確保磁碟機，再重新裝載自動重新啟動電腦之後，必須新增至/等/fstab 檔案。 此外，建議 UUID （通用唯一識別碼） 用於/等/fstab 中參照的磁碟機，而不是只裝置名稱 (例如， `/dev/sdc1`)。 如果 OS 偵測磁碟錯誤開機期間，使用 UUID 避免不正確的磁碟無法連接至指定的位置。 剩餘資料磁碟想再被指派這些相同的裝置識別碼。 若要尋找的新的磁碟機 UUID，使用**blkid**公用程式︰

```bash
sudo -i blkid
```

輸出看起來如下︰

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

>[AZURE.NOTE] 不正確編輯**/etc/fstab**檔案，可能會導致無法開機。 如果不確定，請參閱如何正確地編輯此檔案的詳細資訊的通訊群組的文件。 建議的編輯前請先建立 /etc/fstab 檔案的備份。

接下來，請在文字編輯器中開啟**/etc/fstab**檔案︰

```bash
sudo vi /etc/fstab
```

在此範例中，我們使用先前的步驟，並掛接點**/datadrive**中所建立的新**/dev/sdc1**裝置的 UUID 值。 新增**/etc/fstab**檔案結尾的下列行︰

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

>[AZURE.NOTE] 稍後移除資料磁碟，但不能編輯 fstab 可能會導致失敗開機 VM。 大部分散佈提供 [`nofail`及/或`nobootwait`fstab 選項。 這些選項可讓系統開機，即使磁碟，但無法裝載在啟動時。 如需這些參數的詳細資訊，請參閱累加分配的文件。

>[AZURE.NOTE] **Nofail**選項可確保 VM 開始，即使檔案系統已損毀，或在啟動時不存在的磁碟。 沒有這個選項，您可能會遇到問題所述[無法 SSH FSTAB 錯誤而 Linux vm](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)


### <a name="trimunmap-support-for-linux-in-azure"></a>Azure 中 Linux TRIM/UNMAP 支援
某些 Linux 核心支援 TRIM/UNMAP 作業，若要放棄磁碟上未使用過的區塊。 這是標準的儲存體通知 Azure 刪除頁面的已不再有效，並可捨棄特別有用。 這可以儲存成本，如果您建立大型檔案，然後將它們刪除。

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

- 請記住，您的新磁碟無法使用的 vm，若，除非您將該資訊寫入[fstab](http://en.wikipedia.org/wiki/Fstab)檔案。
- 若要確保您 Linux VM 已正確設定，請檢閱[最佳化 Linux 電腦效能](virtual-machines-linux-optimization.md)的建議。
- 展開您儲存容量新增額外的磁碟及[設定 RAID](virtual-machines-linux-configure-raid.md)取得額外的效能。
