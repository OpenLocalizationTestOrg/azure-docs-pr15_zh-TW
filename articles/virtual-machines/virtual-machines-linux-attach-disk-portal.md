<properties
    pageTitle="將資料磁碟附加至 Linux VM |Microsoft Azure"
    description="如何將新的或現有的資料磁碟附加至 Linux VM Azure 入口網站使用的資源管理員部署模型中。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="cynthn"/>

# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>如何將資料磁碟附加到 Linux VM Azure 入口網站中

本文將示範如何將新的或現有的磁碟附加到 Linux 虛擬機器透過 Azure 入口網站。 您也可以[附加到 Windows VM Azure 入口網站中的資料磁碟](virtual-machines-windows-attach-disk-portal.md)。 這麼做之前，請檢閱這些秘訣︰

- 虛擬機器大小控制，您可以附加多少資料磁碟。 如需詳細資訊，請參閱[虛擬機器的大小](virtual-machines-linux-sizes.md)。
- 若要使用進階版儲存空間，您必須 DS 數列] 或 [GS 系列虛擬機器。 您可以使用這些虛擬機器磁碟進階版及 [標準] 儲存的帳戶。 在特定區域中有進階版儲存空間。 如需詳細資訊，請參閱[進階版儲存空間︰ Azure 虛擬機器負載高效能儲存空間](../storage/storage-premium-storage.md)。
- 附加至虛擬機器的磁碟是實際.vhd Azure 儲存體帳戶中的檔案。 如需詳細資訊，請參閱[關於磁碟及 Vhd 虛擬機器](virtual-machines-linux-about-disks-vhds.md)。
- 新的磁碟，您不需要先建立目錄，因為當您附加 Azure 建立它。
- 現有磁碟.vhd 檔案必須提供 Azure 儲存體帳戶。 您可以使用已.vhd，如果無法連到另一個虛擬機器或上傳您自己的.vhd 檔案儲存空間的帳戶。


[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a name="next-steps"></a>後續步驟

新增磁碟] 後，必須先備妥以供使用。 請參閱在虛擬機器的作業系統中: 」 如何︰ 初始化新資料磁碟 Linux 中的 「 本[文章](virtual-machines-linux-classic-attach-disk.md#how-to-initialize-a-new-data-disk-in-linux)中。
