<properties
    pageTitle="卸離 Windows VM 資料磁碟 |Microsoft Azure"
    description="瞭解如何卸離中使用的資源管理員部署模型 Azure 虛擬機器中的資料磁碟。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>



# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>如何卸離資料磁碟從 Windows 虛擬機器


當您不再需要附加至虛擬機器資料磁碟時，您可以輕鬆地中斷。 這磁碟移除虛擬機器，但不會移除儲存空間。 

> [AZURE.WARNING] 如果您中斷的磁碟，不會自動被刪除。 如果您有訂閱進階版儲存空間，您會繼續造成儲存空間的磁碟的費用。 如需詳細資訊請參閱[價格和帳單時使用進階版儲存空間](../storage/storage-premium-storage.md#pricing-and-billing)。 

如果您想要一次使用磁碟上的現有資料，您可以重新附加在相同的虛擬機器或其他方案。  


## <a name="detach-a-data-disk-using-the-portal"></a>中斷連結資料磁碟使用入口網站

1. 在入口網站的中心內，選取 [**虛擬機器**]。

2. 選取具有您想要中斷的連結，然後按一下 [**所有設定**資料磁碟的虛擬機器。

3. 在**設定**刀中，選取 [**磁碟**]。

4. 在**磁碟**刀中，選取您想要中斷連結的資料磁碟。

5. 在資料磁碟刀，按一下 [**中斷連結**]。


    ![顯示 [中斷連結] 按鈕的螢幕擷取畫面。](./media/virtual-machines-windows-detach-disk/detach-disk.png)

磁碟仍會保留在儲存空間，但不會再附加至虛擬機器。


## <a name="detach-a-data-disk-using-powershell"></a>卸離使用 PowerShell 資料磁碟

在此範例中，第一個命令會取得虛擬機器中名為 「 **MyVM07** **RG11**資源群組使用取得 AzureRmVM 指令程式。 命令儲存虛擬機器**$VirtualMachine**變數中。 

第二個命令移除命名 DataDisk3 虛擬機器中的資料磁碟。 

最後一個命令就會更新虛擬機器完成移除資料磁碟的程序的狀態。

    $VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" 
    Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
    Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine


如需詳細資訊，請參閱[移除 AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx)

## <a name="next-steps"></a>後續步驟

如果您想要重複使用的資料磁碟，您可以只[將其另一個 vm 附加](virtual-machines-windows-attach-disk-portal.md)
