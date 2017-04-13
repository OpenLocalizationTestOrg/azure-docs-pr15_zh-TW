<properties 
    pageTitle="設定使用網路的設定檔的虛擬網路" 
    description="若要匯出及匯入至 Azure 管理入口網站，才能建立或修改虛擬網路的網路設定檔的指示進行。 " 
    services="virtual-network" 
    documentationCenter="" 
    authors="jimdial" 
    manager="carmonm" 
    editor="tysonn"/>

<tags
    ms.service="virtual-network"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services" 
    ms.date="03/15/2016"
    ms.author="jdial"/>

# <a name="configure-a-virtual-network-using-a-network-configuration-file"></a>設定使用網路的設定檔的虛擬網路

使用 [Azure 管理入口網站，或是使用網路的設定檔，您可以設定虛擬網路 (VNet)。

## <a name="creating-and-modifying-a-network-configuration-file"></a>建立與修改網路設定檔 
作者網路的設定檔的最簡單方法是從現有的虛擬網路組態]，匯出的網路設定，然後修改以包含您要設定您的虛擬網路的設定檔。

若要編輯的網路設定檔案，您可以直接開啟的檔案、 進行適當的變更，然後儲存檔案。 若要變更網路設定檔，您可以使用任何*xml*編輯器。 

您應該密切追蹤[網路設定檔的結構描述設定](https://msdn.microsoft.com/library/azure/jj157100.aspx)的指引。 

Azure 會考慮子網路已部署至其為**使用中**的項目。 使用子網路時，您無法進行修改。 修改之前，將任何子網路，以不同的子網路的不要修改的部署的項目。   請參閱[移動 VM 或角色執行個體，以不同的子網路](virtual-networks-move-vm-role-to-subnet.md)。

## <a name="export-and-import-virtual-network-settings-using-the-management-portal"></a>匯出與匯入使用 [管理入口網站的虛擬網路設定  
您可以匯入及匯出使用 PowerShell 或管理入口網站，您的網路設定檔中所包含的網路設定的設定。 以下指示可協助您匯出與匯入使用 [管理入口網站。 

### <a name="to-export-your-network-settings"></a>若要匯出您的網路設定
匯出時，所有您的訂閱的虛擬網路的設定將寫入.xml 檔案。 

1. 登入**管理入口網站**。
2. 在管理入口網站上的 [**網路**] 頁面底部，按一下 [**匯出**]。 
3. 在 [**匯出網路設定**] 視窗中，確認您已選取您要匯出您的網路設定的訂閱。 接著，按一下右下的核取記號。 
4. 當系統提示您時， *NetworkConfig.xml*將檔案儲存至您選擇的位置。


### <a name="to-import-your-network-settings"></a>若要匯入您的網路設定

1. 在**管理入口網站**，在左下角，在功能窗格中按一下 [**新增**]。
2. 按一下 [**網路服務** -> **虛擬網路** -> **匯入設定**。
3. 在**匯入網路設定檔**] 頁面中，瀏覽至您的網路設定的檔案，然後再按一下**下一步**的箭號。
4. 在 [**建立您的網路**] 頁面中，您會看到畫面會顯示您的網路設定的章節會變更，或建立的詳細資訊。 如果變更外觀正確給您，請按一下 [核取記號，以進行更新，或建立您的虛擬網路]。 