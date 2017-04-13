<properties
   pageTitle="如何將多個要有 vpn 才能閘道器至網站連線至虛擬網路使用 Azure 入口網站的資源管理員部署模型 |Microsoft Azure"
   description="新增多網站 S2S 連線 VPN 閘道器已有現有的連線"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>



# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>新增網站至網站連線至現有的 VPN 閘道器連線 VNet

> [AZURE.SELECTOR]
- [資源管理員-入口網站](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
- [傳統-PowerShell](vpn-gateway-multi-site.md)

本文會引導您使用 Azure 入口網站新增至網站的 (S2S) 連線 VPN 閘道器已有現有的連線。 此連線類型通常稱為 「 多的網站 」 設定。 

若要新增已經有 S2S 連線、 點為網站的連線或 VNet-VNet 連線 VNet S2S 連線，您可以使用這份文件。 新增連線時，有一些限制。 核取您啟動您的設定之前，請先確認本文中的 [[之前](#before)] 區段。 

本文適用於 VNets 使用資源管理員部署模型建立具有 RouteBased VPN 閘道器。 下列步驟不適用於 ExpressRoute /-網站共存連線設定。 如需有關共存連線資訊，請參閱[ExpressRoute/S2S 共存連線](../expressroute/expressroute-howto-coexist-resource-manager.md)。

### <a name="deployment-models-and-methods"></a>部署模型和方法

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

我們更新此資料表為新的文件和其他工具，就可以使用此設定。 使用文件時，我們連結直接從這個資料表。

[AZURE.INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)] 


## <a name="before"></a>開始之前

請確認下列項目︰

- 您不會建立 ExpressRoute/S2S 共存連線。
- 您有虛擬網路所建立的資源管理員部署模型使用現有的連線。
- 針對您 VNet 虛擬網路閘道器在 RouteBased。 如果您有 PolicyBased VPN 閘道器時，您必須刪除虛擬網路閘道器，並建立為 RoutBased VPN 閘道。
- 無位址範圍的重疊的此 VNet 連線到 VNets。
- 您有相容 VPN 裝置，以及可以設定讓它的人。 請參閱[關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。 如果您不熟悉設定您要有 vpn 才能的裝置，或不熟悉 IP 位址範圍位於您的內部部署網路設定，您需要進行共同作業的人可以為您提供這些詳細資料。
- 您要有 vpn 才能裝置有對外公開 IP 位址。 Nat 找不到這個 IP 位址


## <a name="part1"></a>第 1 部分-設定連線

1. 從瀏覽器中，瀏覽至[Azure 入口網站](http://portal.azure.com)，如果有需要，請登入您的 Azure 帳戶。
2. 按一下 [**所有資源**並找出您的**虛擬網路閘道器**清單中的資源，並都按一下它。
3. 在**虛擬網路閘道**刀中，按一下 [**連線**]。

    ![連線刀](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Connections blade")<br>

4. 在**連線**刀中，按一下 [ **+ 新增]**。

    ![[新增連線] 按鈕](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Add connection button")<br>

5. 在 [**新增連線**刀中，填寫下列欄位︰
    - **名稱︰**您想要讓您建立的連線的網站名稱。
    - **連線類型︰**選取**網站的網站 」 (IPsec)**]。

    ![新增連線刀](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Add connection blade")<br>

## <a name="part2"></a>第 2 部分-新增 [區域網路的閘道器

1. 按一下 [**區域網路閘道器*****選擇的區域網路的閘道器***。 這會開啟**選擇區域網路閘道器**刀。

    ![選擇 [區域網路閘道器](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Choose local network gateway")<br>
2. 按一下 [**建立新**開啟**建立本機網路閘道**刀。

    ![建立區域網路閘道器刀](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Create local network gateway")<br>

3. 在**建立本機網路閘道**刀中，填寫下列欄位︰
    - **名稱︰**您要授與本機網路閘道器資源名稱。
    - **IP 位址︰**您要連線的網站上的 VPN 裝置的公用 IP 位址。
    - **位址空間︰**您想要傳送到新的區域網路網站位址空間。
4. 在**建立本機網路閘道**刀，以儲存變更，按一下**[確定]** 。

## <a name="part3"></a>第 3 部分-新增共用的金鑰並建立連線

1. 在 [**新增連線**刀中，加上您要用來建立您的連線的共用索引鍵。 您可以從您要有 vpn 才能的裝置，取得共用索引鍵，或將其中一個此處並設定 VPN 裝置使用相同的共用的金鑰。 最重要的是按鍵完全相同。

    ![共用索引鍵](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Shared key")<br>
2. 在刀底部，按一下**[確定**] 以建立連線。

## <a name="part4"></a>第 4 部分-驗證 VPN 連線

您可以驗證您的 VPN 連線在入口網站，或使用 PowerShell。

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]


## <a name="next-steps"></a>後續步驟

- 完成您的連線之後，您可以新增到您的虛擬網路的虛擬機器。 請參閱虛擬機器[學習路徑](https://azure.microsoft.com/documentation/learning-paths/virtual-machines)如需詳細資訊。
