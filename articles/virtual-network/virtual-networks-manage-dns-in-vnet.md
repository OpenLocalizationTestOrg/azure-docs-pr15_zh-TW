<properties 
   pageTitle="管理虛擬網路 (VNet) 所使用的 DNS 伺服器"
   description="瞭解如何新增及移除 [虛擬網路 (vnet) 中的 [DNS 伺服器"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="manage-dns-servers-used-by-a-virtual-network-vnet"></a>管理虛擬網路 (VNet) 所使用的 DNS 伺服器

您可以管理 VNet，在 [管理] 入口網站，或網路設定檔中所使用的 DNS 伺服器的清單。 您可以針對每個 VNet 新增最多 12 DNS 伺服器。 指定時 DNS 伺服器，請務必以驗證您列出您的 DNS 伺服器環境的順序正確無誤。 DNS 伺服器清單無法使用循環。 使用已指定的順序。 如果在清單上的第一個 DNS 伺服器能夠接聽，用戶端會使用無論 DNS 伺服器是否運作是否正確的 DNS 伺服器。 若要變更 DNS 伺服器順序虛擬網路，從清單中移除的 DNS 伺服器，並將其新增回您想要的順序。

>[AZURE.WARNING] [DNS] 清單已更新之後，您必須重新啟動虛擬機器位於虛擬網路，好讓他們選擇新的 DNS 伺服器設定。 虛擬機器會繼續使用目前的設定，它們都必須重新啟動。

## <a name="edit-a-dns-server-list-for-a-virtual-network-using-the-management-portal"></a>編輯虛擬網路，使用 [管理入口網站的 DNS 伺服器清單

1. 登入**管理入口網站**。

1. 在功能窗格] 中按一下**網路**]，然後按一下 [**名稱**] 欄中虛擬網路的名稱。

1. 按一下 [**設定**]。

1. 在 [ **DNS 伺服器**，您可以設定下列項目︰

    - **註冊 （新增） 新的 DNS 伺服器︰**只要在方塊中輸入名稱及 IP 位址。 這將虛擬網路 DNS 伺服器] 清單中的 DNS 伺服器，也註冊 Azure DNS 伺服器。

    - **若要新增的已登錄 – DNS 伺服器**如果您已註冊的 DNS 伺服器與 Azure，您可以從預先填入的清單中選取它。

    - **若要移除您的虛擬網路 – 從 DNS 伺服器**按一下您想要移除的伺服器旁的 [X]。 請注意，這只會移除伺服器虛擬網路清單。 DNS 伺服器仍會保留已註冊的其他虛擬網路使用的 Azure 中。 若要從您的訂閱刪除 DNS 伺服器，請移至 [**網路]-> [Dns** ] 頁面。

    - **若要重新排序 DNS 伺服器︰**移除所有的 DNS 伺服器未列出，，然後將其新增回您想要的順序。 請記住這不是循環 DNS 清單。

    - **若要重新命名的 DNS 伺服器︰**醒目提示 DNS 伺服器，在清單中，然後輸入新名稱。 這會在 Azure 註冊新的 DNS 伺服器，以及將其新增至您的虛擬網路的 DNS 伺服器清單。 舊的 DNS 伺服器及 IP 位址仍會維持註冊 Azure 使用。 您可以刪除它在 [ **DNS 伺服器**頁面如果您不使用它的任何其他虛擬網路。

1. 按一下 [**儲存**在頁面底部的儲存您新增的 DNS 伺服器設定]。

1. 重新啟動虛擬機器中的虛擬網路，讓他們取得新的 DNS 設定。

## <a name="edit-a-dns-server-list-using-a-network-configuration-file"></a>編輯使用網路的設定檔的 DNS 伺服器清單

若要使用網路的設定檔中編輯 DNS 伺服器清單，您會先從管理入口網站匯出您設定的設定。 然後將編輯網路設定檔，並透過管理入口網站傳回匯入。 以下是高層級的步驟，以完成此程序清單。

1. 將虛擬網路設定匯出到網路的設定檔。 如需的資訊與步驟，匯出您的網路設定的設定，請參閱[匯出虛擬網路設定網路設定檔](virtual-networks-using-network-configuration-file.md)。

1. 指定虛擬網路的 DNS 伺服器資訊。 如需有關如何指定 DNS 伺服器的詳細資訊，請參閱[指定虛擬的網路設定檔中的 DNS 伺服器](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)。 如需網路設定檔的詳細資訊，請參閱[Azure 虛擬網路設定結構描述](https://msdn.microsoft.com/library/azure/jj157100.aspx)和[設定虛擬網路使用網路的設定檔](virtual-networks-using-network-configuration-file.md)。

1. 匯入網路設定檔。 如需的資訊與匯入您的網路設定檔的步驟，請參閱[匯入網路的設定檔](virtual-networks-using-network-configuration-file.md)。

1. 重新啟動虛擬機器中的虛擬網路，讓他們取得新的 DNS 設定。
