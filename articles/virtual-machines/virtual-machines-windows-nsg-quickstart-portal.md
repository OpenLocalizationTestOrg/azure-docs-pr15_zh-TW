<properties
   pageTitle="開啟與 VM 使用 Azure 入口網站的連接埠 |Microsoft Azure"
   description="瞭解如何開啟連接埠 / 建立您的 Windows vm Azure 入口網站中使用的資源管理員部署模型端點"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="opening-ports-to-a-vm-in-azure-using-the-azure-portal"></a>開啟連接埠與 VM Azure 使用 Azure 入口網站中
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>快速命令
您也可以[執行下列步驟使用 PowerShell 的 Azure](virtual-machines-windows-nsg-quickstart-powershell.md)。

首先，建立您的網路安全性群組。 選取資源群組入口網站中，按一下 [**新增**]，然後搜尋並選取 「 網路安全性群組 」:

![新增網路安全性群組](./media/virtual-machines-windows-nsg-quickstart-portal/add-nsg.png)

輸入您的網路安全性群組的名稱，選取或建立資源群組，並選取的位置。 按一下 [**建立**完成時︰

![建立網路安全性群組](./media/virtual-machines-windows-nsg-quickstart-portal/create-nsg.png)

選取新的網路安全性群組。 選取 [輸入的安全性規則]，然後按一下 [**新增**] 按鈕，以建立規則︰

![新增連入的規則](./media/virtual-machines-windows-nsg-quickstart-portal/add-inbound-rule.png)

提供新規則的名稱。 預設會已輸入連接埠 80。 此刀是位置您想變更來源、 通訊協定，與目的地其他規則至您的網路安全性群組時。 按一下**[確定**] 以建立規則︰

![建立連入的規則](./media/virtual-machines-windows-nsg-quickstart-portal/create-inbound-rule.png)

您的最後一個步驟是建立您的網路安全性群組與子網路或特定的網路介面之間的關聯。 現在就讓我們關聯子網路網路安全性群組。 選取 「 子網路]，然後按一下 [**建立關聯**︰

![建立網路安全性群組與子網路之間的關聯](./media/virtual-machines-windows-nsg-quickstart-portal/associate-subnet.png)

選取您的虛擬網路，然後選取 [適當的子網路︰

![建立網路安全性群組與虛擬網路的關聯](./media/virtual-machines-windows-nsg-quickstart-portal/select-vnet-subnet.png)

您現在已經建立網路安全性群組，建立一個輸入的規則，允許流量的連接埠 80，並聯子網路。 您連線至子網路任何 Vm 是連線到連接埠 80。


## <a name="more-information-on-network-security-groups"></a>網路安全性群組的詳細資訊
此處的快速命令可讓您快速上手與您 vm 流量。 網路安全性群組提供許多很棒的功能及資料粒度來控制存取您的資源。 您可以閱讀更多關於[建立網路安全性群組和以下 ACL 規則](../virtual-network/virtual-networks-create-nsg-arm-ps.md)。

您可以定義網路安全性群組以及 ACL 規則 Azure 資源管理員範本的一部分。 閱讀更多關於[建立網路安全性群組的範本](../virtual-network/virtual-networks-create-nsg-arm-template.md)。

如果您要使用內部連接埠上您 VM 對應唯一的外部連接埠的連接埠轉接，請使用負載平衡器和網路位址轉譯 (NAT) 規則。 例如，可能要公開 TCP 連接埠 8080 外部有流量導向至 TCP 連接埠 80 VM 上。 您可以瞭解如何[建立具網際網路負載平衡器](../load-balancer/load-balancer-get-started-internet-arm-ps.md)。

## <a name="next-steps"></a>後續步驟
在此範例中，您可以建立簡單的規則，以允許 HTTP 流量。 您可以找到下列文章中建立更詳細的環境的詳細資訊︰

- [Azure 資源管理員的概觀](../azure-resource-manager/resource-group-overview.md)
- [什麼是網路安全性群組 (NSG)？](../virtual-network/virtual-networks-nsg.md)
- [Azure 負載平衡器資源管理員概觀](../load-balancer/load-balancer-arm.md)
