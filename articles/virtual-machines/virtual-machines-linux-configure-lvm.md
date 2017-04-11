<properties 
    pageTitle="虛擬機器執行 Linux 設定 LVM |Microsoft Azure" 
    description="瞭解如何設定 LVM linux Azure 中。" 
    services="virtual-machines-linux" 
    documentationCenter="na" 
    authors="szarkos"  
    manager="timlt" 
    editor="tysonn"
    tag="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="szark"/>


# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>設定 LVM Linux VM Azure 中

本文將討論如何設定在 Azure 虛擬機器中的邏輯大量管理員 (LVM)。 雖然可以設定 LVM 附加至虛擬機器任何磁碟上，依預設大部分的雲端圖像不會有 LVM OS 磁碟上設定。 這是為了避免重複的大量群組的問題，如果 OS 磁碟附加至另一個 VM 相同的通訊和類型，也就是在復原案例期間。 因此，建議只使用 LVM 資料磁碟上。


## <a name="linear-vs-striped-logical-volumes"></a>線性與條紋邏輯區

LVM 可用來結合單一儲存區中的數字的實體磁碟。 依預設 LVM 通常會建立線性邏輯區，這表示實際的儲存空間串連在一起。 在此情況下讀/寫作業會通常只會傳送到單一磁碟。 相反地，我們也可以建立條紋位置分散到包含 （亦即是在 RAID0 類似） 的 [音量] 群組中的多個磁碟的讀取和寫入的邏輯磁碟區。 基於效能可能會想要等量磁碟區邏輯區，以便讀取和寫入利用所有附加的資料磁碟。

這份文件會說明如何將多個資料磁碟合併成單一音量] 群組中，並建立條紋邏輯音量。 使用大部分的散佈有點歸納起來，執行下列步驟。 在大多數情況下公用程式及管理 LVM Azure 上的工作流程無法根本上有所不同於其他環境。 如同以往，也請您 Linux 廠商的文件並使用 LVM 與您的特定分配的最佳做法。


## <a name="attaching-data-disks"></a>附加資料磁碟
其中一個通常會想要開始使用 LVM 時的兩個或多個空白資料磁碟。 根據您 IO 的需求，您可以選擇要附加會儲存在與多達 500 IO/ps 每個磁碟或我們進階版儲存與多達 5000 IO/ps 每個磁碟我們標準儲存空間的磁碟。 本文將深入說明如何佈建和 Linux 虛擬機器中附加資料磁碟。 請參閱如何將 Linux 上 Azure 虛擬機器中附加空的資料磁碟上的 Microsoft Azure 文章[附加磁碟](virtual-machines-linux-add-disk.md)的詳細指示。

## <a name="install-the-lvm-utilities"></a>安裝 LVM 公用程式

- **Ubuntu**

        # sudo apt-get update
        # sudo apt-get install lvm2

- **RHEL、 CentOS 與 Oracle Linux**

        # sudo yum install lvm2

- **SLES 12 和 openSUSE**

        # sudo zypper install lvm2

- **SLES 11**

        # sudo zypper install lvm2

    在 SLES11 必須也編輯 /etc/sysconfig/lvm 並設定`LVM_ACTIVATED_ON_DISCOVERED`」 啟用 」:

        LVM_ACTIVATED_ON_DISCOVERED="enable" 


## <a name="configure-lvm"></a>設定 LVM
本指南中，我們將假設您所附加三個資料磁碟，我們將參照為`/dev/sdc`，`/dev/sdd`和`/dev/sde`。 請注意，這些不一定會在您 VM 相同的路徑名稱。 您可以執行 「`sudo fdisk -l`' 或類似的命令可列出您的可用磁碟。

1. 準備的磁碟區︰

        # sudo pvcreate /dev/sd[cde]
          Physical volume "/dev/sdc" successfully created
          Physical volume "/dev/sdd" successfully created
          Physical volume "/dev/sde" successfully created


2.  建立大量群組。 在此範例中，我們會呼叫大量群組 」 資料-vg01 」:

        # sudo vgcreate data-vg01 /dev/sd[cde]
          Volume group "data-vg01" successfully created


3. 建立邏輯的區。 以下我們命令將會建立稱為 「 資料-lv01 」 橫跨整個音量] 群組中，但請注意，您也可以建立 [音量] 群組中的多個邏輯區單一邏輯音量。

        # sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
          Logical volume "data-lv01" created.


4. 格式化邏輯的音量

        # sudo mkfs -t ext4 /dev/data-vg01/data-lv01

  >[AZURE.NOTE] 使用 SLES11 」-t ext3 」，而不是 ext4。 SLES11 只支援 ext4 filesystems 唯讀存取。


## <a name="add-the-new-file-system-to-etcfstab"></a>新增新的檔案系統 /etc/fstab

**注意︰**不正確編輯 /etc/fstab 檔案，可能會導致無法開機。 如果不確定，請參閱累加分配的文件，瞭解如何正確地編輯此檔案的詳細資訊。 建議的編輯前請先建立 /etc/fstab 檔案的備份。

1. 建立所需的連接點的新檔案系統，例如︰

        # sudo mkdir /data


2. 找出邏輯大量路徑

        # lvdisplay
        --- Logical volume ---
        LV Path                /dev/data-vg01/data-lv01
        ....


3. 在文字編輯器中開啟 /etc/fstab 並新增新的檔案系統中，項目，例如︰

        /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2

    然後儲存並關閉 /etc/fstab。


4. 測試/等/fstab 項目正確︰

        # sudo mount -a

    如果這個命令會產生錯誤訊息，請檢查/等/fstab 檔案中的語法。

    下一步執行`mount`命令，以確保檔案系統會裝載︰

        # mount
        ......
        /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)


5. （選用）在 [/etc/fstab 保全開機參數

    包含許多散佈`nobootwait`或`nofail`裝載可能會新增至/等/fstab 檔案的參數。 這些參數允許失敗時，裝載特定檔案系統，並允許以繼續開機，即使是無法正確裝載 RAID 檔案系統的 Linux 系統。 請參閱如需有關這些參數的累加分配的文件。

    範例 (Ubuntu):

        /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
