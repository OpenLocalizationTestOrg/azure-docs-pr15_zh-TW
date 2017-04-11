<properties
    pageTitle="如何規劃虛擬網路 Azure RemoteApp 集合 |Microsoft Azure"
    description="瞭解如何規劃虛擬網路 Azure RemoteApp 集合。"
    services="remoteapp"
    documentationCenter="" 
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="how-to-plan-your-virtual-network-for-azure-remoteapp"></a>如何規劃 Azure RemoteApp 虛擬網路

> [AZURE.IMPORTANT]
> Azure RemoteApp 會被停用。 閱讀[公告](https://go.microsoft.com/fwlink/?linkid=821148)如需詳細資訊。

這份文件會說明如何設定 Azure RemoteApp Azure 虛擬網路 (VNET) 和子網路。 如果您不熟悉 Azure 虛擬網路，這是可協助您虛擬化您的網路基礎結構至雲端，並使用 Azure 及內部部署資源建立混合式解決方案功能。 您可以閱讀更多相關[以下](../virtual-network/virtual-networks-overview.md)。

如果您想要定義中您要在其中部署 Azure RemoteApp 虛擬網路流量 （內送和外寄） 的安全性原則，我們強烈建議您部署 Azure 虛擬網路中的其餘 Azure RemoteApp 建立不同的子網路。 如需有關如何定義 Azure 虛擬網路子網路上的安全性原則的詳細資訊，請參閱[什麼是網路安全性群組 (NSG)？](../virtual-network/virtual-networks-nsg.md)。

## <a name="types-of-azure-remoteapp-collections-with-azure-virtual-networks"></a>Azure RemoteApp 集合與 Azure 虛擬網路的類型

當您要使用虛擬網路時，下列圖形會顯示兩個不同的集合選項。

### <a name="azure-remoteapp-cloud-collection-with-vnet"></a>Azure RemoteApp 雲端集合 VNET

 ![Azure RemoteApp-VNET 雲端集合](./media/remoteapp-planvpn/ra-cloudvpn.png)

這表示 Azure RemoteApp 集合 Azure 中部署 RemoteApp 工作階段主機需要存取的所有資源的位置。 也可以在相同的 VNET 為 RemoteApp VNET 或 Azure 中的不同 VNET。

### <a name="azure-remoteapp-hybrid-collection-with-vnet"></a>Azure RemoteApp 混合式集合 VNET

![Azure RemoteApp-VNET 混合式集合](./media/remoteapp-planvpn/ra-hybridvpn.png)

這表示 Azure RemoteApp 集合部分 RemoteApp 工作階段主機需要存取的資源是內部部署的位置。 RemoteApp VNET 已連結至內部部署網路使用 Azure 混合式技術網站-VPN 或 Express 路由等。


## <a name="how-the-system-works"></a>系統的運作方式

在幕後 Azure RemoteApp 部署到虛擬網路的子網路期間佈建所選擇的 Azure 虛擬機器 （上傳圖像）。 如果您選擇的混合式集合，我們嘗試解決網域控制站佈建的工作流程中輸入虛擬網路中提供的 DNS 伺服器的 FQDN。  
如果您連線到現有的虛擬網路，請確定公開必要的 Azure RemoteApp 子網路網路安全性群組中的連接埠。 

我們建議您使用[的 Azure RemoteApp 子網路夠大](remoteapp-vnetsizing.md)。 最大的支援 Azure 虛擬網路是/8 （使用 CIDR 子網路定義）。 您子網路應該夠大，以容納所有 Azure RemoteApp Vm 升級期間，當更多使用者存取應用程式。 

以下是您將需要啟用虛擬網路子網路的項目︰ 

2.  子網路的外寄流量的連接埠範圍 10101 10175 上允許通訊的其中一個內部 Azure RemoteApp 服務。
3.  外寄流量允許來自您子網路連線至連接埠 443 的 Azure 儲存體
4.  如果您有裝載於 Azure Active Directory，請確定虛擬網路的子 Azure RemoteApp 內任何 VM 可以連線至該網域控制站。 虛擬網路中的 DNS，應該能夠解決這個網域控制站的 FQDN。


## <a name="virtual-network-with-forced-tunneling"></a>虛擬網路強制通道

[強制通道](../vpn-gateway/vpn-gateway-about-forced-tunneling.md)，現在支援所有新的 Azure RemoteApp 集合。 我們目前不支援移轉支援強制通道現有的集合。  您會有刪除所有現有的集合使用連結至 Azure RemoteApp VNET，建立一個新若要取得強制您集合上啟用通道。 
