<properties 
   pageTitle="如何從相關性群組移轉到地區虛擬網路 (VNet)"
   description="瞭解如何移轉到地區 vnets 相關性群組"
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

# <a name="how-to-migrate-from-affinity-groups-to-a-regional-virtual-network-vnet"></a>如何從相關性群組移轉到地區虛擬網路 (VNet)

您可以使用相關性群組，以確保的緊密結合，讓這些資源，快速通訊的伺服器實際裝載相同的相關性群組內建立的資源。 過去相關性群組已建立虛擬網路 (VNets) 的需求。 此時，管理 VNets 網路管理員服務無法僅適用於中一組實體伺服器或刻度] 單位。 建築的改良功能增加區域網路管理的範圍。

這些架構的增強功能，當做相關性群組不再建議，或所需的虛擬網路。 區域就會被取代 VNets 的相關性群組使用。 區域與相關聯的 VNets 稱為地區 VNets。

此外，建議您不在 [一般使用相關性群組。 除了 VNet 需求，也是重要使用，以確保資源，例如計算和儲存空間，已放置在彼此靠近相關性群組。 不過，使用目前的 Azure 網路架構，這些位置需求不再需要。 請參閱[相關性群組和 Vm](#Affinity-groups-and-VMs)幾個剩餘特定情況下可能會想要使用相關性群組。

## <a name="creating-and-migrating-to-regional-vnets"></a>建立和移轉至地區 VNets

建立新的 VNets 時，請將轉寄、，請使用*區域*。 您會看到以下訊息管理入口網站中的選項。 請注意，在網路的設定檔中，這會顯示為*位置*。

>[AZURE.IMPORTANT] 雖然仍可行建立與相關性群組相關聯的虛擬網路，但是沒有理由執行此作業。 許多新功能的詳細資訊，例如網路安全性群組，只提供使用地區的 VNet 時，而不適用於虛擬相關性群組相關聯的網路。

### <a name="about-vnets-currently-associated-with-affinity-groups"></a>關於目前相關性群組與相關聯的 VNets

目前相關性群組與關聯的 VNets 已啟用移轉至地區 VNets。 若要移轉至地區的 VNet，請遵循下列步驟︰

1. 匯出網路設定檔。 您可以使用 PowerShell 或管理入口網站。 如需使用 [管理入口網站的指示，請參閱[設定您 VNet 使用網路的設定檔](virtual-networks-using-network-configuration-file.md)。

1. 編輯您的網路設定檔，以新的值取代舊的值。 

    > [AZURE.NOTE] **位置**是您所指定的相關性群組與您 VNet 相關聯的區域。 例如，如果您 VNet 關聯與相關性群組位於西美國，當您移轉時，您所在位置必須指向西美國。 
    
    編輯下列幾行在您的網路設定的檔案，使用您自己取代的值︰ 

    **舊的值︰**\<VirtualNetworkSitename = 「 VNetUSWest 」 AffinityGroup = 「 VNetDemoAG 」\> 

    **新的值︰**\<VirtualNetworkSitename = 「 VNetUSWest 」 位置 = 」 西美國 」\>

1. 儲存變更並[匯入](virtual-networks-using-network-configuration-file.md)的網路設定至 Azure。

>[AZURE.NOTE] 此移轉不會導致服務任何停機時間。

## <a name="affinity-groups-and-vms"></a>相關性群組和 Vm

如先前所述，Vm 不再通常會建議相關性群組。 只有在一組 Vm 必須絕對最低網路之間的延遲 Vm 時，您應該使用相關性群組。 藉由放置 Vm 相關性] 群組中，所有放置 Vm 在相同的運算叢集] 或 [時幅單位。

請務必請注意，使用相關性群組可以有兩種，可能是負的結果︰

- 一組的 VM 大小會限制為 VM 大小計算刻度] 單位所提供的設定。

- 有無法配置新 VM 較高的機率。 會發生這種情況時相關性] 群組中的特定的縮放比例單位超出容量。

### <a name="what-to-do-if-you-have-a-vm-in-an-affinity-group"></a>如果您有 VM 相關性] 群組

從 [相關性] 群組中移除不需要 Vm 正在相關性] 群組。

只要 VM 部署時，其部署到單一刻度] 單位。 相關性群組可以限制新 VM 部署，可以使用 VM 大小的設定，但部署任何現有 VM 已有一組可用部署 VM 時的縮放比例單位的 VM 大小的限制。 因此，從 [相關性] 群組中移除 VM 會有任何效果。
 
