<properties
    pageTitle="有關磁碟與 Linux Vm 的 Vhd |Microsoft Azure"
    description="瞭解的磁碟及 Vhd Linux 虛擬機器 Azure 中的基本概念。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/16/2016"
    ms.author="cynthn"/>

# <a name="about-disks-and-vhds-for-azure-virtual-machines"></a>有關磁碟與 Vhd Azure 虛擬機器

就像其他任何電腦中 Azure 虛擬機器會使用磁碟作業系統、 應用程式，以及資料儲存為位置。 所有 Azure 虛擬機器有兩個以上的磁碟 – Linux 作業系統光碟 （如果 Linux VM) 和暫存的磁碟。 作業系統磁碟會建立從影像，而同時作業系統磁碟及圖像實際虛擬硬碟 (Vhd) 儲存在 Azure 儲存體帳戶。 虛擬機器也可以有一或多個資料的磁碟，也會儲存為 Vhd。 本文也適用於[Windows 虛擬機器](virtual-machines-windows-about-disks-vhds.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

> [AZURE.NOTE] 如果您有幾分鐘的時間，請協助我們改進 Azure Linux VM 文件進行[快速問卷](https://aka.ms/linuxdocsurvey)的體驗。 每個答案可幫助幫助您完成工作。

## <a name="operating-system-disk"></a>作業系統磁碟

每個虛擬機器具有附加的作業系統磁碟。 它已註冊為 SATA 磁碟機，並標示為預設的 c 磁碟機。 此磁碟有 1023 gb 最大容量。 

##<a name="temporary-disk"></a>暫存磁碟

暫時磁碟會自動建立適合您。 在 Linux 虛擬機器，磁碟通常是 /dev/sdb 格式化與裝載 /mnt/resource 至 Azure Linux 代理程式。

暫時磁碟的大小，根據的虛擬機器大小而異。 如需詳細資訊，請參閱[Linux 虛擬機器的大小](virtual-machines-linux-sizes.md)。

>[AZURE.WARNING] 不儲存暫時磁碟上的資料。 它提供暫存應用程式與處理程序，並用於只能儲存資料，例如頁面或交換檔案。 

如需有關如何 Azure 使用暫存的磁碟的詳細資訊，請參閱[瞭解暫存的磁碟機上的 Microsoft Azure 虛擬機器](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>資料磁碟

資料磁碟是 VHD 附加至虛擬機器儲存應用程式的資料或您需要保留其他資料。 資料磁碟已經註冊為 SCSI 磁碟機，，並標示您選擇以字母。  每個資料磁碟有 1023 GB 最大容量。 虛擬機器的大小會決定您可以使用多少可附加至，再輸入的儲存空間的資料磁碟裝載磁碟。

>[AZURE.NOTE] 如需虛擬機器容量的詳細資訊，請參閱[Linux 虛擬機器的大小](virtual-machines-linux-sizes.md)。

Azure 建立從影像建立虛擬機器作業系統磁碟。 如果您使用圖像，其中包含資料磁碟，Azure 也會建立資料磁碟建立虛擬機器時。 否則，您建立虛擬機器之後新增資料磁碟。

您可以新增資料磁碟虛擬機器隨時**附加**到虛擬機器磁碟。 您可以使用 VHD 您上傳或複製到您儲存的帳戶，或是 Azure 會為您建立的。 附加資料磁碟關聯 VHD 檔案儲存空間帳戶虛擬機器中，將 「 租用' 放置在 VHD，因此無法加以刪除仍附加時從儲存空間。

## <a name="about-vhds"></a>關於 Vhd

用於 Azure Vhd 是.vhd 檔案儲存為標準或付費的儲存空間帳戶 Azure 中的頁面二進位大型物件。 如需頁面二進位大型物件的詳細資訊，請參閱[瞭解區塊 blob 和頁面二進位大型物件](https://msdn.microsoft.com/library/ee691964.aspx)。 如需進階版儲存空間的詳細資訊，請參閱[進階版儲存空間︰ Azure 虛擬機器負載高效能儲存空間](../storage/storage-premium-storage.md)。

Azure 支援固定的磁碟 VHD 格式。 固定的格式配置邏輯磁碟直線在檔案中，因此該磁碟位移 X 儲存在 blob 位移 X。Blob 的結尾的小型頁尾描述 VHD 的屬性。 通常，固定的格式會因為大多數的磁碟中有未使用過的大範圍浪費空間。 不過，Azure 儲存.vhd 檔案疏鬆的格式，讓您同時接收固定和動態磁碟的優點。 如需詳細資訊，請參閱[快速入門虛擬硬碟](https://technet.microsoft.com/library/dd979539.aspx)。

您想要使用做為來源建立磁碟或圖像的 Azure 中的所有.vhd 檔案都是唯讀狀態。 當您建立的磁碟或圖像時，Azure 可.vhd 檔案的複本。 這些複本可以是唯讀或讀取和寫入，取決於您如何使用 VHD。

當您建立虛擬機器的圖像時，Azure 所建立的磁碟虛擬機器來源.vhd 檔案的複本。 若要防止不慎刪除，Azure 放到租用用來建立圖像、 作業系統或資料磁碟任何來源.vhd 檔案。

您可以刪除來源.vhd 檔案之前，必須移除租用刪除磁碟或圖像。 若要刪除的用作虛擬機器作業系統磁碟.vhd 檔案，您可以刪除虛擬機器，作業系統磁碟，然後來源.vhd 檔案，一次刪除虛擬機器並刪除所有相關聯的磁碟。 不過，刪除的來源資料的磁碟的.vhd 檔案需要幾個步驟，設定順序--卸離從虛擬機器磁碟、 刪除磁碟，然後刪除 [.vhd 檔案。

>[AZURE.WARNING] 如果您刪除的來源.vhd 檔案儲存空間，或刪除您儲存的帳戶時，Microsoft 就無法為您復原該資料。


## <a name="troubleshooting"></a>疑難排解
[AZURE.INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>後續步驟

-  若要為您 VM 新增額外的儲存空間[附加磁碟](virtual-machines-linux-add-disk.md)。
-  重複[設定軟體 RAID](virtual-machines-linux-configure-raid.md) 。
-  [擷取 Linux 虛擬機器](virtual-machines-linux-classic-capture-image.md)，您可以快速部署其他 Vm。


