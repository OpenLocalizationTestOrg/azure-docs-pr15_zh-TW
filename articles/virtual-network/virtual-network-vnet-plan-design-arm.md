<properties
   pageTitle="Azure 虛擬網路 (VNet) 計劃和設計指南 |Microsoft Azure"
   description="瞭解如何規劃及設計虛擬網路 Azure 根據隔離、 連線，以及位置的需求。"
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
   ms.date="02/08/2016"
   ms.author="jdial" />

# <a name="plan-and-design-azure-virtual-networks"></a>規劃及設計 Azure 虛擬網路

建立要實驗 VNet 很簡單，但開始，您會在一段時間，以支援您組織的生產需要部署多個 VNets。 某些規劃及設計，您可以部署 VNets 並連接您需要更有效地的資源。 如果您不熟悉 VNets，建議您，您將[瞭解 VNets](virtual-networks-overview.md) ，以及[如何部署](virtual-networks-create-vnet-arm-pportal.md)一個之前。 

## <a name="plan"></a>計劃

深入瞭解 Azure 訂閱、 地區和網路資源是成功的關鍵。 您可以使用清單下方的考量的起點。 只要您瞭解這些考量，您可以定義您的網路設計的需求。

### <a name="considerations"></a>考量

下面回答規劃問題之前，請考慮下列各項︰

- 您建立 Azure 中的所有項目是由組成的一或多個資源。 虛擬機器 (VM) 為資源、 網路介面卡 (NIC) 使用 VM 是資源，NIC 所使用的公用 IP 位址是資源，NIC 已連線至 VNet 為資源。
- 您建立內[Azure 地區](https://azure.microsoft.com/regions/#services)碼和訂閱的資源。 與資源僅可連線至相同的地區和訂閱中存在 VNet。 
- 您可以連線至其他的 VNets 使用 Azure [VPN 閘道器](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)。 您也可以連接 VNets 各區域及訂閱這種方式。
- 您可以連線至您的內部網路的 VNets 使用其中一個可用的[連線選項](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site)Azure 中。 
- 其他資源可以組成[資源群組](../azure-resource-manager/resource-group-overview.md#resource-groups)，讓您更容易管理為單位的資源。 資源群組可以包含多個區域的資源，只要資源屬於同一份訂閱。

### <a name="define-requirements"></a>定義需求

開始使用] 下方的問題，為 Azure 網路設計。  

1. 您要使用哪些 Azure 位置主機 VNets？
2. 您需要提供這些 Azure 位置之間的通訊嗎？
3. 您需要提供您 Azure VNet(s) 和您的內部部署 datacenter(s) 之間的通訊嗎？
4. 角色和 web 應用程式執行您的方案，您需要多少基礎結構服務 (IaaS) Vm 雲端服務嗎？
5. 您需要隔離根據群組 Vm （亦即前端網頁伺服器和後端資料庫伺服器） 的流量嗎？
6. 您需要使用虛擬就流量流程嗎？
7. 使用者需要不同的權限，以不同的 Azure 資源集嗎？

### <a name="understand-vnet-and-subnet-properties"></a>了解 VNet 和子網路屬性

VNet 和子網路資源協助定義 Azure 中執行的工作負載的安全邊界。 VNet 在於位址空間，定義為 CIDR 區塊的集合。 

>[AZURE.NOTE] 網路管理員熟悉 CIDR 標記法。 如果您不熟悉 CIDR，[深入了解更多](http://whatismyipaddress.com/cidr)。

VNets 包含下列屬性。

|屬性|描述|限制式|
|---|---|---|
|**名稱**|VNet 名稱|最多 80 個字元的字串。 可能包含字母、 數字、 底線、 句號或連字號。 必須以字母或數字開始。 必須以字母、 數字或底線結尾。 可以包含大寫或小寫字母。|  
|**位置**|Azure （也稱為 「 地區 」） 的位置。|必須是有效的 Azure 位置。|
|**addressSpace**|CIDR 法 VNet 組成的地址前置詞的集合。|必須是有效的 CIDR 地址區塊] 包括公用 IP 位址範圍陣列。|
|**子網路**|子網路 VNet 組成的集合|請參閱下的子網路屬性表。||
|**dhcpOptions**|包含單一的必要的屬性的物件名稱為**dnsServers**。||
|**dnsServers**|VNet 所使用的 DNS 伺服器陣列。 如果未指定伺服器，則會使用 Azure 內部名稱解析。|必須是最多 10 個 DNS 伺服器陣列，依 IP 位址。| 

子網路為子資源的 VNet，並協助定義在使用 IP 位址首碼 CIDR 區塊的地址空格的區段。 Nic 可以新增至子網路，並連線到 Vm，提供各種工作負載的連線。

子網路包含下列屬性。 

|屬性|描述|限制式|
|---|---|---|
|**名稱**|子網路名稱|最多 80 個字元的字串。 可能包含字母、 數字、 底線、 句號或連字號。 必須以字母或數字開始。 必須以字母、 數字或底線結尾。 可以包含大寫或小寫字母。|
|**位置**|Azure （也稱為 「 地區 」） 的位置。|必須是有效的 Azure 位置。|
|**addressPrefix**|單一組成 CIDR 法子網路的地址前置詞|必須是單一 CIDR 區塊的其中一個 VNet 的地址空格。|
|**networkSecurityGroup**|套用至子網路 NSG|請參閱[NSGs](resource-groups-networking.md#Network-Security-Group)|
|**routeTable**|路由表套用至子網路|請參閱[UDR](resource-groups-networking.md#Route-table)|
|**ipConfigurations**|連線到子網路 Nic 所用的 IP 設定物件的集合|請參閱[IP 設定](../resource-groups-networking.md#IP-configurations)|

### <a name="name-resolution"></a>名稱解析

根據預設，使用您 VNet [Azure 所提供的名稱解析。](virtual-networks-name-resolution-for-vms-and-role-instances.md#Azure-provided-name-resolution) 若要解決內 VNet，並在公用網際網路上的名稱。 不過，如果您將 VNets 連線至您的內部部署資料中心時，您需要提供[您自己的 DNS 伺服器](virtual-networks-name-resolution-for-vms-and-role-instances.md#Name-resolution-using-your-own-DNS-server)，以解決您的網路之間的名稱。  

### <a name="limits"></a>限制

檢閱[Azure 限制](../azure-subscription-service-limits.md#networking-limits)的文件，以確保您的設計不衝突限制的任何網路限制。 一些限制，可以增加開啟支援票證。

### <a name="role-based-access-control-rbac"></a>角色型存取控制 (RBAC)

您可以使用[Azure RBAC](../active-directory/role-based-access-built-in-roles.md)控制不同的使用者可能必須 Azure 中的其他資源的存取層級。 如此一來，您可以將根據其需要您的小組所完成的工作。 

虛擬網路而言，就在**網路參與者**的角色的使用者會有 Azure 資源管理員虛擬網路資源的完全控制權。 同樣地，在**傳統的網路參與者**的角色的使用者會有傳統的虛擬網路資源的完全控制權。

>[AZURE.NOTE] 您也可以將您的系統管理需求的 [[建立您自己的角色](../active-directory/role-based-access-control-configure.md)。

## <a name="design"></a>設計

一旦您知道[計劃](#Plan)] 區段中問題的答案，請檢閱下列定義您 VNets 之前。

### <a name="number-of-subscriptions-and-vnets"></a>訂閱及 VNets 數目

您應該考慮建立多個 VNets 在以下情況︰

- **Vm 需要放到不同的 Azure 位置**。 VNets Azure 中的區域。 他們無法橫跨位置。 因此您必須至少有一個 VNet 為每個您想要在主機 Vm 的 Azure 位置。
- **需要為完全從另一個獨立的工作量**。 您可以建立另一個 VNets，甚至隔離不同的工作負載從另一個使用相同的 IP 位址空間]。 

請記住，您看到上方的限制是每個地區，每個訂閱。 這表示您可以使用多個訂閱，若要增加您維護 Azure 中的資源的限制。 您可以使用網站-VPN 或 ExpressRoute 線電路連線 VNets 在不同的訂閱。

### <a name="subscription-and-vnet-design-patterns"></a>訂閱和 VNet 設計模式

下表顯示一些常見的設計模式的訂閱和 VNets 使用。

|案例|圖表|專業人員|缺點|
|---|---|---|---|
|單一訂閱，每個應用程式的兩個 VNets|![單一訂閱](./media/virtual-network-vnet-plan-design-arm/figure1.png)|若要管理的只有一個訂閱。|每個 Azure 地區 VNets 的最大數目。 之後，您需要更多的訂閱。 檢閱[Azure 限制](../azure-subscription-service-limits.md#networking-limits)文章以取得詳細資料。|
|應用程式，每個應用程式的兩個 VNets 每一個訂閱|![單一訂閱](./media/virtual-network-vnet-plan-design-arm/figure2.png)|使用每個訂閱的只有兩個 VNets。|難管理當有太多的應用程式。|
|每個業務單位，每個應用程式的兩個 VNets 訂閱。|![單一訂閱](./media/virtual-network-vnet-plan-design-arm/figure3.png)|[餘額的訂閱的數字與 VNets 之間。|最大數目 VNets 每個業務單位 （訂閱）。 檢閱[Azure 限制](../azure-subscription-service-limits.md#networking-limits)文章以取得詳細資料。|
|每個業務單位，每個群組的應用程式的兩個 VNets 訂閱。|![單一訂閱](./media/virtual-network-vnet-plan-design-arm/figure4.png)|[餘額的訂閱的數字與 VNets 之間。|必須使用子網路和 NSGs 隔離應用程式。|


### <a name="number-of-subnets"></a>子網路的數字

您應該考慮 VNet 在下列情況中的多個子網路︰

- **沒有足夠的子網路中的所有 Nic 私用的 IP 位址**。 如果您子網路位址空間的 Nic 子網路中的數字的沒有足夠的 IP 位址，您需要建立多個子網路。 請記住，Azure 保留 5 私人 IP 位址無法用於每一個子網路︰ 位址空間 （適用於子網路地址與多點傳送） 的第一個和最後一個地址和 3 的地址用於內部 （DHCP 和 DNS）。 
- **安全性**。 您可以使用子網路的 Vm 群組從另一個工作負載多層結構，並套用不同的[網路安全性群組 (NSGs)](virtual-networks-nsg.md#subnets)為這些子網路。
- **混合式連線**。 您可以使用 VPN 閘道和 ExpressRoute 電路[連線](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site)，以及您的內部部署資料 center(s) 您 VNets。 VPN 閘道和 ExpressRoute 電路需要自己建立的子網路。
- **虛擬設備**。 您可以使用虛擬應用裝置，例如防火牆、 WAN 加速器或要有 vpn 才能閘道器 Azure VNet 中。 當您這麼做時，您會需要[路由流量](virtual-networks-udr-overview.md)至這些設備，其隔離在自己的子網路。

### <a name="subnet-and-nsg-design-patterns"></a>子網路和 NSG 設計模式

下表顯示一些常見的設計模式使用子網路。

|案例|圖表|專業人員|缺點|
|---|---|---|---|
|單一子網路，NSGs 每一層級的應用程式，每個應用程式|![單一子網路](./media/virtual-network-vnet-plan-design-arm/figure5.png)|只有一個要管理的子網路。|多個 NSGs 必要隔離每個應用程式。|
|應用程式，每個應用程式層 NSGs 每一個子網路|![每個應用程式的子網路](./media/virtual-network-vnet-plan-design-arm/figure6.png)|若要管理的較少 NSGs。|若要管理多個子網路。|
|應用程式層級]，每個應用程式的 NSGs 每一個子網路。|![每一層級的子網路](./media/virtual-network-vnet-plan-design-arm/figure7.png)|[餘額的子網路的數字與 NSGs 之間。|最大數目 NSGs 每個訂閱。 檢閱[Azure 限制](../azure-subscription-service-limits.md#networking-limits)文章以取得詳細資料。|
|每個應用程式層級]，每個應用程式，NSGs 子網路每一個子網路|![每一層級，每個應用程式的子網路](./media/virtual-network-vnet-plan-design-arm/figure8.png)|可能是較小的數字的 NSGs。|若要管理多個子網路。|

## <a name="sample-design"></a>範例設計

若要說明的應用程式的本文中的資訊，請考慮下列案例。

您有北美地區 2 的資料中心及兩個資料中心歐洲的公司內工作。 識別 6 不同的客戶具應用程式維護 2 的其他您想要移轉至 Azure 試用為的業務單位。 應用程式的基本架構如下所示︰

- App1、 App2、 App3 和 App4 是在執行 Ubuntu Linux 伺服器上裝載的 web 應用程式。 每個應用程式連線至另一個應用程式伺服器主控 RESTful Linux 伺服器上的服務。 RESTful 服務連線到 MySQL 後端資料庫。
- App5 和 App6 是在執行 Windows Server 2012 R2 的 Windows 伺服器上裝載的 web 應用程式。 每個應用程式連線至 SQL Server 後端資料庫。
- 北美地區的公司的資料中心的其中一種目前裝載所有應用程式。
- 內部部署資料中心使用 10.0.0.0/8 位址空間。

您需要設計虛擬網路解決方案，以符合下列需求︰

- 每個業務單位未受到消耗資源的其他業務單位。
- 您應該最小化 VNets 和子網路，輕鬆地管理的量。
- 每個業務單位應該具有單一測試/開發 VNet 用於所有應用程式。
- 每個應用程式被裝載在 2 的不同 Azure 資料中心，每個大陸 （北美地區、 歐洲）。
- 每個應用程式是完全彼此隔離的項目。
- 每個應用程式可以透過網際網路使用 HTTP 客戶存取。
- 每個應用程式可以使用加密的通道連接到內部部署資料中心的使用者存取。
- 連線到內部部署資料中心應該使用現有的 VPN 裝置。
- 公司網路群組應具備完整控制 VNet 設定。
- 在每個業務單位的開發人員只能將 Vm 部署至現有子網路。
- 將移轉所有應用程式，就和至 Azure (增益 shift)。
- 每個位置中的資料庫應該複寫到其他 Azure 位置一天。
- 每個應用程式應該使用 5 的前端網頁伺服器，2 應用程式伺服器 （如果有必要的話） 及 2 資料庫伺服器。

### <a name="plan"></a>計劃

您應該能如常您規劃回答的問題，在 [[定義需求](#Define-requirements)] 區段中，如下所示的設計。

1. 您要使用哪些 Azure 位置主機 VNets？

    北美地區 2 的位置和歐洲地區 2 的位置。 您應該挑選基礎的實體您現有的內部部署資料中心的位置。 如此一來 Azure 從您的實體位置的連線會有更好的延遲。

2. 您需要提供這些 Azure 位置之間的通訊嗎？

    [是]。 由於資料庫必須複寫到所有的位置。

3. 您需要提供您 Azure VNet(s) 和您的內部部署之間的通訊資料 center(s) 嗎？

    [是]。 因為使用者連線到內部部署資料中心必須透過加密通道存取應用程式。
 
4. 您需要多少 IaaS Vm 解決方案？

    200 IaaS Vm。 App1、 App2 和 App3 需要 5 網頁伺服器每個、 2 應用程式伺服器每個及 2 資料庫伺服器每個。 這是每個應用程式，9 IaaS Vm 或 36 IaaS Vm 總計。 App5 和 App6 需要 5 的網頁伺服器及 2 資料庫伺服器每個。 這是 7 IaaS Vm 每個應用程式或 14 IaaS Vm 總計。 因此，您需要 50 IaaS Vm 各 Azure 區域中的所有應用程式。 我們需要使用 4 的區域，因為會有 200 IaaS Vm。

    您也必須提供 DNS 伺服器在每個 VNet，或您的內部部署資料中心，以解決您的 Azure IaaS Vm 與內部部署網路之間的名稱。 

5. 您需要隔離根據群組 Vm （亦即前端網頁伺服器和後端資料庫伺服器） 的流量嗎？

    [是]。 每個應用程式應該彼此，完全隔離，而且每個應用程式層也應該隔離。 

6. 您需要使用虛擬就流量流程嗎？

    [否]。 虛擬就可以用於提供更多的控制流量，包括詳細的資料平面記錄。 

7. 使用者需要不同的權限，以不同的 Azure 資源集嗎？

    [是]。 網路小組開發人員應只會將其 Vm 部署至現有子網路時，必須虛擬網路設定]，[完全控制。 

### <a name="design"></a>設計

您應該遵循指定訂閱、 VNets、 子網路和 NSGs 設計。 我們將在這裡，討論 NSGs，但您應該深入瞭解[NSGs](virtual-networks-nsg.md)之前先完成您的設計。

**訂閱及 VNets 數目**

若要訂閱並 VNets 相關下列需求︰

- 每個業務單位未受到消耗資源的其他業務單位。
- 您應該最小化 VNets 和子網路的量。
- 每個業務單位應該具有單一測試/開發 VNet 用於所有應用程式。
- 每個應用程式被裝載在 2 的不同 Azure 資料中心，每個大陸 （北美地區、 歐洲）。

根據這些需求，必須訂閱的每個業務單位。 如此一來，資源的業務單位的會計算根據限制的其他業務單位。 與您想要最小化的 VNets 數，您應該考慮使用**一個訂閱，每個業務單位，每個群組的應用程式的兩個 VNets**模式，如下所示。

![單一訂閱](./media/virtual-network-vnet-plan-design-arm/figure9.png)

您也需要指定每個 VNet 位址空間。 因為您需要之間的內部部署資料連線中心 Azure 區域中，與內部部署網路中，使用 Azure VNets 的地址空間無法相衝突，並不會與其他現有的 VNets 應該相衝突使用每個 VNet 的地址空間。 若要符合這些需求，您可以使用下表中的位址空間。  

|**訂閱**|**VNet**|**Azure 區域**|**位址空間**|
|---|---|---|---|
|BU1|ProdBU1US1|西美制]。|172.16.0.0/16|
|BU1|ProdBU1US2|設定適用於美國|172.17.0.0/16|
|BU1|ProdBU1EU1|北美歐洲|172.18.0.0/16|
|BU1|ProdBU1EU2|西歐|172.19.0.0/16|
|BU1|TestDevBU1|西美制]。|172.20.0.0/16|
|BU2|TestDevBU2|西美制]。|172.21.0.0/16|
|BU2|ProdBU2US1|西美制]。|172.22.0.0/16|
|BU2|ProdBU2US2|設定適用於美國|172.23.0.0/16|
|BU2|ProdBU2EU1|北美歐洲|172.24.0.0/16|
|BU2|ProdBU2EU2|西歐|172.25.0.0/16|

**子網路數與 NSGs**

網路和 NSGs 相關下列需求︰

- 您應該最小化 VNets 和子網路的量。
- 每個應用程式是完全彼此隔離的項目。
- 每個應用程式可以透過網際網路使用 HTTP 客戶存取。
- 每個應用程式可以使用加密的通道連接到內部部署資料中心的使用者存取。
- 連線到內部部署資料中心應該使用現有的 VPN 裝置。
- 每個位置中的資料庫應該複寫到其他 Azure 位置一天。

根據這些需求，您可以在簡報中使用應用程式層級] 中，每一個子網路，使用 NSGs 篩選流量每個應用程式。 如此一來，您只能有 3 的子網路中每個 VNet （前端、 應用程式層和資料層級） 和每個應用程式的子網路每一個 NSG。 在此情況下，您應該考慮使用**應用程式層級]，每個應用程式的 NSGs 每一個子網路**設計模式。 下圖顯示代表**ProdBU1US1** VNet 設計模式的使用。

![圖層，每個應用程式，每一層級一個 NSG 每一個子網路](./media/virtual-network-vnet-plan-design-arm/figure11.png)

不過，您也需要建立 VPN 連線 VNets，與您的內部部署資料中心之間的額外子網路。 與您要指定每一個子網路位址空間。 下圖顯示**ProdBU1US1** VNet 範例解決方案。 您想要的每個 VNet 複寫這種情況。 每種色彩代表不同的應用程式。

![範例 VNet](./media/virtual-network-vnet-plan-design-arm/figure10.png)

**存取控制**

若要存取控制相關下列需求︰

- 公司網路群組應具備完整控制 VNet 設定。
- 在每個業務單位的開發人員只能將 Vm 部署至現有子網路。

根據這些需求，您可以將使用者從網路小組加入內建的網路中的**參與者**角色每個訂閱。並授與這些權限新增到現有的子網路的 Vm 每個訂閱中的應用程式開發人員建立自訂的角色。

## <a name="next-steps"></a>後續步驟

- [部署虛擬網路](virtual-networks-create-vnet-arm-template-click.md)根據情況。
- 瞭解如何[負載平衡](../load-balancer/load-balancer-overview.md)IaaS Vm 與[管理多個 Azure 區域上方路由](../traffic-manager/traffic-manager-overview.md)。
- 進一步瞭解[NSGs，以及如何規劃及設計](virtual-networks-nsg.md)NSG 解決方案。
- 進一步瞭解您[跨內部部署和 VNet 連線選項](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site)。
