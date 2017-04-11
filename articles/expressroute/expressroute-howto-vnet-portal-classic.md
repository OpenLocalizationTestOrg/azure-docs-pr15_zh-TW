<properties
   pageTitle="在 [傳統] 入口網站中設定虛擬網路和閘道器的 ExpressRoute |Microsoft Azure"
   description="本文會引導您設定為使用傳統的部署模型和 [傳統] 入口網站 ExpressRoute 虛擬網路。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/20/2016"
   ms.author="cherylmc"/>

# <a name="create-a-virtual-network-for-expressroute-in-the-classic-portal"></a>在 [傳統] 入口網站中建立 ExpressRoute 虛擬網路

本文中的步驟會逐步引導您完成設定與使用傳統的部署模型和 [傳統] 入口網站的 ExpressRoute 的虛擬網路和用於虛擬網路閘道器。

如果您要尋找的資源管理員部署模型的相關指示，您可以使用下列文章︰[建立虛擬網路使用 PowerShell](../virtual-network/virtual-networks-create-vnet-arm-ps.md)和[新增至的 ExpressRoute 資源管理員 VNet VPN 閘道器](expressroute-howto-add-gateway-resource-manager.md)。

**關於 Azure 部署模型**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="create-a-classic-vnet-and-gateway"></a>建立傳統 VNet 和閘道器

下列步驟建立傳統的 VNet 和虛擬網路閘道器。 如果您已經有傳統的 VNet，請參閱本文中的 [[設定現有的傳統 VNet](#config) ] 區段。

1. [Azure 傳統入口網站](http://manage.windowsazure.com)登入。

2. 在畫面的左上角，按一下 [**新增**]。 在功能窗格] 中按一下**網路服務**]，然後按一下 [**虛擬網路**。 按一下 [開始設定精靈的 [**建立自訂**]。

3. 在**虛擬網路詳細資料**頁面上，輸入下列資訊︰

    - **名稱**– 虛擬網路的名稱。 當您部署您 Vm 和 PaaS 的執行個體，因此可能不想讓過於複雜的名稱，您會使用這個虛擬網路名稱。
    - **位置**︰ 位置與直接相關，實體位置 （區域），您希望資源 (Vm) 所在的位置。 例如，如果您想 Vm 您部署實體位於東亞美國此虛擬網路時，選取該位置。 您無法變更建立後，您的虛擬網路與相關聯的區域。

4. 在 [ **DNS 伺服器及 VPN 連線**] 頁面中，輸入下列資訊，然後再按一下上右下方的下一個箭號。 

    - **DNS 伺服器**-輸入的 DNS 伺服器名稱及 IP 位址，或從快顯功能表中選取先前已註冊的 DNS 伺服器。 此設定不會建立 DNS 伺服器。 其可讓您指定您想要使用這個虛擬網路名稱解析 DNS 伺服器。
    - **網站-連線**-**設定網站-VPN**用選取核取方塊。
    - **ExpressRoute** -選取 [**使用 ExpressRoute**核取方塊。 如果您選取 [**設定網站-VPN**，只會出現這個選項。
    - **區域網路**-您必須要有的 ExpressRoute 的區域網路網站。 不過，如果 ExpressRoute 連線，會忽略區域網路網站所指定的地址首碼。 不過，透過 ExpressRoute 電路通知給 Microsoft 的地址首碼會用於路由用途。<BR>如果您已經有的 ExpressRoute 連線所建立的本機網路，您可以從下拉式清單中選取它。 否則，請選取 [**指定新的本機網路**。

5. 如果您選取 [在上一個步驟中，指定新的本機網路，就會出現 [**連線至網站**頁面。 若要設定您的本機電腦，輸入下列資訊，然後按一下下一步的箭號。 

    - 網站的網路**名稱**您要撥打您的本機 （內部部署） 的名稱。
    - **位址空間**-包括開始的 IP 和 CIDR （地址計數）。 它不會與重疊之處虛擬網路的地址範圍時，您可以指定任何地址的範圍。 一般而言，這會指定您的內部網路的位址範圍，但不是使用若是 ExpressRoute，這些設定。 不過，這項設定，才能使用 [傳統] 入口網站時建立本機的網路。
    - **新增地址空間**-此設定不相關的 ExpressRoute。


6. 在 [**虛擬網路位址空間**] 頁面中，輸入下列資訊，然後按一下右下方來設定您的網路上的核取記號。 

    - **位址空間**-包括啟動 IP 和地址的計數。 請確認您所指定的地址空格不致重疊的位址空間，您的區域網路上的任一。
    - **新增子網路**-包括開始的 IP 和地址計數。 其他的子網路且並非必要。
    - **新增閘道器子網路**-按一下以新增閘道器子網路。 閘道器子網路僅用於虛擬網路閘道器，此設定的需要。<BR>閘道器子網路的 ExpressRoute CIDR （地址計數） 必須 /28 或更大 (/ 27 日/26 等等。)。 這可讓足夠的子網路中的 IP 位址允許搭配使用的設定。 在 [傳統] 入口網站，如果您已選取的核取方塊，以使用 ExpressRoute，入口網站指定閘道器子網路與 /28。  您無法調整 CIDR 地址計數傳統入口網站中。 閘道器子網路會以**閘道器**在傳統的入口網站，雖然閘道器子網路所建立的實際名稱實際上**GatewaySubnet**。 使用 PowerShell 或 Azure 入口網站中，您可以檢視此名稱。

7. 按一下 [在頁面底部的核取記號，會開始建立您的虛擬網路。 完成之後，您會看到**建立**[**狀態**列在 [傳統] 入口網站中的 [**網路**] 頁面。

## <a name="gw"></a>建立閘道器

1. 在 [**網路**] 頁面上，按一下您剛才建立的虛擬網路，然後按一下 [**儀表板**頂端的頁面]。

2. 在**儀表板**頁面底部，按一下 [**建立閘道器**，選取 [**動態路由**。 按一下 [**是**] 以確認您想要建立閘道器。

3. 閘道器會開始建立，您會看到訊息讓您知道已啟動的閘道器。 花費 45 分鐘，若要建立閘道器。

4. 連結電路您的網路。 依照指示，請參閱[如何連結至 ExpressRoute 電路 VNets](expressroute-howto-linkvnet-classic.md)。

## <a name="config"></a>設定現有的傳統 VNet 的 ExpressRoute

如果您已經有傳統的 VNet，您可以設定它在 [傳統] 入口網站中連線至 ExpressRoute。 設定並上述節一樣，因此閱讀這些章節以熟悉的所需的設定。 如果您想要建立 ExpressRoute /-網站共存連線，請參閱[本文](expressroute-howto-coexist-classic.md)的步驟。 他們的年齡不同於本文中的步驟進行。
 
1. 建立所需的區域網路之前更新，部分 VNet 設定。 若要建立新的本機網路，這是必要，透過 [傳統] 入口網站設定 ExpressRoute 時，按一下 [**新增** **>** **網路服務** **>** **虛擬網路** **>** **新增區域網路**。 遵循精靈中的步驟來建立區域網路。

2. 若要更新其餘部分將 VNet 的設定，並建立本機網路 VNet 之間的關聯，請使用 [**設定**] 頁面。

3. 之後的設定，請移至本文建立閘道器的 [[建立閘道器](#gw)] 區段。


## <a name="next-steps"></a>後續步驟

- 如果您想要新增至虛擬網路的虛擬機器，請參閱[虛擬機器學習路徑](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)。
- 如果您想要進一步瞭解 ExpressRoute，請參閱[ExpressRoute 概觀](expressroute-introduction.md)。


 
