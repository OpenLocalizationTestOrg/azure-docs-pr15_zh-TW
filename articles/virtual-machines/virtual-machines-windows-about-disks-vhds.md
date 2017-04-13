<properties
    pageTitle="有關磁碟與 Windows Vm Vhd |Microsoft Azure"
    description="深入了解磁碟及 Windows Vhd 虛擬機器 Azure 中的基本概念。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="about-disks-and-vhds-for-azure-virtual-machines"></a>有關磁碟與 Vhd Azure 虛擬機器

就像其他任何電腦中 Azure 虛擬機器會使用磁碟作業系統、 應用程式，以及資料儲存為位置。 所有 Azure 虛擬機器有兩個以上的磁碟 – Windows 作業系統磁碟和暫存的磁碟。 作業系統磁碟會建立從影像，而同時作業系統磁碟及圖像虛擬硬碟 (Vhd) 儲存在 Azure 儲存體帳戶。 虛擬機器也可以有一或多個資料的磁碟，也會儲存為 Vhd。 本文也適用於[Linux 虛擬機器](virtual-machines-linux-about-disks-vhds.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



## <a name="operating-system-disk"></a>作業系統磁碟

每個虛擬機器具有附加的作業系統磁碟。 它已註冊為 SATA 磁碟機，並標示為預設的 c 磁碟機。 此磁碟有 1023 gb 最大容量。 

##<a name="temporary-disk"></a>暫存磁碟

暫時磁碟會自動建立適合您。 根據預設，用來儲存 pagefile.sys，暫時磁碟會標示為 d 磁碟機。 

暫時磁碟的大小，根據的虛擬機器大小而異。 如需詳細資訊，請參閱[Windows 大小虛擬機器](virtual-machines-windows-sizes.md)。

>[AZURE.WARNING] 不儲存暫時磁碟上的資料。 它提供暫存應用程式與處理程序，並用於只能儲存資料，例如頁面或交換檔案。 若要重新對應到另一個磁碟機代號磁碟，請參閱[變更 Windows 暫時磁碟機字母](virtual-machines-windows-classic-change-drive-letter.md)。

如需有關如何 Azure 使用磁碟暫存的詳細資訊，請參閱[瞭解暫存的磁碟機上的 Microsoft Azure 虛擬機器](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>資料磁碟

資料磁碟是 VHD 附加至虛擬機器儲存應用程式的資料或您需要保留其他資料。 資料磁碟已經註冊為 SCSI 磁碟機，，並標示您選擇以字母。  每個資料磁碟有 1023 GB 最大容量。 虛擬機器的大小會決定您可以使用多少可附加至，再輸入的儲存空間的資料磁碟裝載磁碟。

>[AZURE.NOTE] 如需有關虛擬機器容量的詳細資訊，請參閱[Windows 大小虛擬機器](virtual-machines-windows-sizes.md)。

Azure 建立從影像建立虛擬機器作業系統磁碟。 如果您使用圖像，其中包含資料磁碟，Azure 也會建立資料磁碟建立虛擬機器時。 否則，您建立虛擬機器之後新增資料磁碟。

您可以新增資料磁碟虛擬機器隨時**附加**到虛擬機器磁碟。 您可以使用 VHD 您上傳或複製到您儲存的帳戶，或是 Azure 會為您建立的。 附加資料磁碟關聯 VHD 檔案 VM '租用' 置於 VHD，因此無法加以刪除仍附加時從儲存空間。

## <a name="about-vhds"></a>關於 Vhd

用於 Azure Vhd 是.vhd 檔案儲存為標準或付費的儲存空間帳戶 Azure 中的頁面二進位大型物件。 如需有關頁面二進位大型物件的詳細資訊，請參閱[瞭解區塊 blob 和頁面二進位大型物件](https://msdn.microsoft.com/library/ee691964.aspx)。 如需進階版儲存空間的詳細資訊，請參閱[進階版儲存空間︰ Azure 虛擬機器負載高效能儲存空間](../storage/storage-premium-storage.md)。

Azure 支援固定的磁碟 VHD 格式。 固定格式配置邏輯磁碟直線在檔案中，因此該磁碟位移 X 儲存在 blob 位移 X。Blob 的結尾的小型頁尾描述 VHD 的屬性。 通常，固定格式浪費的空間大多數的磁碟因為很大未使用的範圍。 不過，Azure 儲存.vhd 檔案疏鬆的格式，讓您同時接收固定和動態磁碟的優點。 如需詳細資訊，請參閱[快速入門虛擬硬碟](https://technet.microsoft.com/library/dd979539.aspx)。

您想要使用做為來源建立磁碟或圖像的 Azure 中的所有.vhd 檔案都是唯讀狀態。 當您建立的磁碟或圖像時，Azure 可.vhd 檔案的複本。 這些複本可以是唯讀或讀取和寫入，取決於您如何使用 VHD。

當您建立虛擬機器的圖像時，Azure 所建立的磁碟虛擬機器來源.vhd 檔案的複本。 若要防止不慎刪除，Azure 放到租用用來建立圖像、 作業系統或資料磁碟任何來源.vhd 檔案。

您可以刪除來源.vhd 檔案之前，必須移除租用刪除磁碟或圖像。 您可以刪除虛擬機器，作業系統磁碟，然後來源.vhd 檔案，一次刪除虛擬機器並刪除所有相關聯的磁碟。 不過，刪除的來源資料的磁碟的.vhd 檔案需要幾個步驟以設定的順序。 第一次您卸離虛擬機器中，從磁碟，然後刪除磁碟，然後刪除.vhd 檔案。

>[AZURE.WARNING] 如果您刪除的來源.vhd 檔案儲存空間，或刪除您儲存的帳戶，Microsoft 就無法為您復原該資料。



## <a name="next-steps"></a>後續步驟
-  若要為您 VM 新增額外的儲存空間[附加磁碟](virtual-machines-windows-attach-disk-portal.md)。
-  若要建立新的 VM 時使用，[上傳至 Azure 的 Windows VM 圖像](virtual-machines-windows-upload-image.md)。
-  [變更 Windows 暫時磁碟機字母](virtual-machines-windows-classic-change-drive-letter.md)，讓您的應用程式可以使用 d 磁碟機的資料。
