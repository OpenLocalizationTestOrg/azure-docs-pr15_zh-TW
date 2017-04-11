<properties
   pageTitle="Azure 網路安全性概觀 |Microsoft Azure"
   description=" 這份文件，可以方便您瞭解 Microsoft Azure 有提供的網路安全性區域中。 我們提供基本說明網路安全性的核心概念、 需求和資訊 Azure 有提供各這些區域。 "
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="azure-network-security-overview"></a>Azure 網路安全性概觀

Microsoft Azure 包含強大的網路基礎結構，以支援您的應用程式與服務連線的需求。 網路連線可能位於 Azure 之間內部部署的資源，並 Azure 裝載的資源，及與網際網路和 Azure。

本文的目標是要讓您瞭解 Microsoft Azure 有的網路安全性區域中提供更容易。 以下我們提供基本說明網路安全性的核心概念與需求。 我們也提供您資訊 Azure 有提供各這些區域。 有許多連結可讓您更深入瞭解您感興趣的領域的其他內容。

Azure 網路安全性概觀本文將著重於下列動作︰

- Azure 網路
- 網路存取控制
- 安全遠端存取和交互內部部署連線能力
- 顯示狀態
- 記錄
- 名稱解析
- DMZ 架構
- Azure 資訊安全中心

## <a name="azure-networking"></a>Azure 網路

虛擬機器需要網路連線。 若要支援這項需求，Azure 需要連線到 Azure 虛擬網路的虛擬機器。 Azure 虛擬網路是使用實體 Azure 網路布料的轉印圖樣建立邏輯建構。 每個邏輯 Azure 虛擬網路是隔離的所有其他 Azure 虛擬網路。 這可協助確保您部署中的網路流量無法存取其他 Microsoft Azure 客戶。

深入瞭解︰

- [虛擬網路概觀](../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>網路存取控制
網路存取控制是指限制特定的裝置，或在 Azure 虛擬網路的子網路的連線。 網路存取控制的目標是要請確認您的虛擬機器和服務存取只有使用者和裝置所要進行存取。 以存取控制項上允許或拒絕連線的決策與您的虛擬機器或服務。

Azure 支援許多種類型的網路存取控制。 這些功能包括︰

- 網路圖層控制項
- 將控制項和強制通道
- 虛擬網路安全性應用裝置

### <a name="network-layer-control"></a>網路圖層控制項
任何不安全的部署需要網路存取控制的一些量值。 網路存取控制的目標是，請確定您的虛擬機器和網路服務的虛擬機器上執行的能以無聲的只能與其他網路的裝置必須與通訊，並封鎖所有其他嘗試連線。

如果您需要基本網路層級的存取控制 （根據 IP 位址和 TCP 或 UDP 通訊協定），您可以使用網路安全性群組。 網路安全性群組 (NSG) 是基本狀態封包篩選的防火牆，其可讓您控制根據[5 表示 tuple](https://www.techopedia.com/definition/28190/5-tuple)的存取權。 NSGs 不提供應用程式層檢查或驗證 access 控制項。

深入瞭解︰

- [網路安全性群組](../virtual-network/virtual-networks-nsg.md)

### <a name="route-control-and-forced-tunneling"></a>將控制項與強制通道
控制路由 Azure 虛擬網路上的行為的功能是要徑網路的安全性和存取控制功能。 如果路由的設定不正確，應用程式與服務裝載在您的虛擬機器上可能會連線到您不想要連線的裝置包括擁有和潛在攻擊者所營運的裝置。

Azure 網路連線支援自訂 Azure 虛擬網路上的網路流量路由行為的功能。 這可讓您變更 Azure 虛擬網路中的預設路由表格項目。 路由行為的控制權，可協助您確保所有來自特定裝置或群組的裝置流量進入或離開 Azure 虛擬網路透過的特定位置。

例如，您可能必須虛擬網路安全性應用裝置 Azure 虛擬網路上。 您想要確定所有流量 Azure 虛擬網路都經歷的虛擬安全性應用裝置。 您可以設定 Azure 中的[使用者定義的路徑](../virtual-network/virtual-networks-udr-overview.md)。

[強制通道](https://www.petri.com/azure-forced-tunneling)是以確保您的服務，不允許進行裝置的網際網路連線，您可以使用的機制。 請注意，這是不同的接受傳入的連線，然後回應它們。 前端網頁伺服器需要從網際網路主機回應要求，因此來源網際網路流量允許連入這些網頁伺服器，並允許網頁伺服器回應。

您不想要允許是前端網頁伺服器進行輸出的要求。 這類要求可能代表安全性風險，因為這些連線無法使用下載惡意程式碼。 即使您想這些前端伺服器進行輸出網際網路要求，您可能會想要強迫他們，好讓您可以利用 URL 篩選和記錄可幫助您內部部署網頁 proxy。

不過，您想要使用 [若要避免此 [強制通道。 當您啟用強制通道時，透過您的內部部署閘道強制所有連線至網際網路。 您可以設定強制通道利用的使用者定義的路徑。

深入瞭解︰

- [使用者定義的路徑和 IP 轉接是什麼](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>虛擬網路安全性應用裝置
雖然網路安全性群組]、 [使用者定義的路徑，以及 [強制通道提供您的網路傳輸層級的[OSI 模型](https://en.wikipedia.org/wiki/OSI_model)的安全性層級，可能會的有時您想要啟用安全性層級高於網路。

例如，可能會包含您的安全性需求︰

- 驗證與之前允許存取您的應用程式授權
- 侵入偵測與侵入回應
- 應用程式的高層級的通訊協定的圖層檢查
- URL 篩選
- 網路層級的防毒軟體與反惡意程式碼
- 反傀儡保護
- 應用程式存取控制
- 其他 DDoS 保護 （上方保護提供 Azure 布料的轉印圖樣本身 DDoS)

您可以使用 Azure 合作夥伴解決方案，以存取這些增強的網路安全性功能。 您可以造訪[Azure Marketplace](https://azure.microsoft.com/marketplace/) ，搜尋 「 安全性 」 和 「 網路安全性 」 找到最新的 Azure 合作夥伴網路安全性解決方案。

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>安全的遠端存取及交叉部署連線
安裝、 設定及管理遠端完成您 Azure 資源的需求。 此外，您可能會想要部署[混合式部署的 IT](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx)解決方案的元件內部部署和 Azure 公用雲端中。 這些案例需要安全的遠端存取。

Azure 網路支援下列安全的遠端存取的案例︰

- Azure 虛擬網路連線個別工作站
- 將您的內部部署網路連線到 VPN Azure 虛擬網路
- 將您的內部部署網路連線到專用的 WAN 連結 Azure 虛擬網路
- 相互連接 Azure 虛擬網路

### <a name="connect-individual-workstations-to-an-azure-virtual-network"></a>連線到 Azure 虛擬網路的個別工作站
可能會有您想来讓個別的開發人員或作業人員來管理虛擬機器和 Azure 服務。 比方說，必須有權存取虛擬機器 Azure 虛擬網路上，您的安全性原則不允許個別的虛擬機器 RDP 或 SSH 遠端存取。 在此情況下，您可以使用點為網站 VPN 連線。

點-網站 VPN 連線，您可以設定使用者和 Azure 虛擬網路之間的私人和安全連線使用[SSTP VPN](https://technet.microsoft.com/library/cc731352.aspx)通訊協定。 一旦建立 VPN 連線時，使用者將能夠 RDP 或 SSH 透過 VPN 連結至 Azure 虛擬網路上的任何虛擬機器 （假設使用者可以進行驗證，並授權）。

深入瞭解︰

- [點-網站使用 PowerShell 虛擬網路連線的設定](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-vpn"></a>將您的內部部署網路連線到 VPN Azure 虛擬網路
若要將整個公司網路或部分，連接 Azure 虛擬網路。 這是在混合式 IT 中常見案例位置公司[延伸到 Azure 其內部部署資料中心](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84)。 在許多情況下公司會裝載的服務 Azure 和組件內部部署，例如解決方案時包含前端網頁伺服器 Azure 和後端資料庫內部部署中的部分。 這些類型的 「 跨內部部署 」 連線也讓 Azure 位於管理資源更安全，並啟用案例，例如將 Active Directory 網域控制站延伸到 Azure。

若要完成這項工作的其中一個方法是使用[網站-VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn)。 網站-VPN 與點為網站 VPN 之間的差異為的點為網站 VPN 單一裝置連線至 Azure 虛擬網路時網站-VPN 連線到 Azure 虛擬網路的整個網路 （例如您的內部部署網路）。 Azure 虛擬網路網站-Vpn 使用高度安全的 IPsec 通道模式 VPN 通訊協定。

深入瞭解︰

- [建立資源管理員 VNet 網站-VPN 連線，使用 [Azure 入口網站](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [規劃及設計 VPN 閘道器](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-dedicated-wan-link"></a>將您的內部部署網路連線到專用的 WAN 連結 Azure 虛擬網路
點-網站和網站-VPN 連線是有效的啟用跨內部部署連線。 不過，在某些組織請考慮他們有下列缺點︰

- VPN 連線在網際網路上移動資料 – 此公開這些潛在安全性問題的相關資料移到公用網路的連線。 此外，無法保證可靠性和網際網路連線的可用性。
- VPN 連線至 Azure 虛擬網路頻寬限制部分應用程式與用途，請為他們最大值出在 200Mbps 周圍的列入考量。

組織通常需要最高層級的安全性和可用性其跨內部部署連線，連線到遠端網站使用專屬的 WAN 連結。 Azure 可讓您能夠使用專用的 WAN 連結，您可以使用您的內部部署網路連線至 Azure 虛擬網路。 這被啟用透過 Azure ExpressRoute。

深入瞭解︰

- [ExpressRoute 技術概觀](../expressroute/expressroute-introduction.md)

### <a name="connect-azure-virtual-networks-to-each-other"></a>相互連接 Azure 虛擬網路
您可以使用許多 Azure 虛擬網路您部署可能是。 原因有許多為何要這麼做。 其中一個原因可能是以簡化管理;其他可能基於安全性理由。 無論動機或解釋放在不同的 Azure 虛擬網路上的資源，可能會的有時候您希望在每一個連線與其他網路上的資源。

其中一個選項就會服務的網路連線到另一個 Azure 虛擬網路上的服務 」 回迴圈 」 一個 Azure 虛擬透過網際網路。 連線想啟動 Azure 虛擬網路上、 網際網路]，瀏覽，然後回到目的地 Azure 虛擬網路。 這個選項會公開固有以網際網路為基礎的任何通訊的安全性問題的連線。

若要建立 Azure 虛擬網路-Azure 虛擬網路網站-VPN 可能更好的選項。 此 Azure 虛擬網路-Azure 虛擬網路網站-VPN 為前述跨內部部署網站-VPN 連線使用相同的[IPsec 通道模式](https://technet.microsoft.com/library/cc786385.aspx)通訊協定。

使用 Azure 虛擬網路-Azure 虛擬網路網站-VPN 的優點是建立 VPN 連線時，是透過 Azure 網路布料的轉印圖樣。它不會透過網際網路連線。 這可讓您了額外的安全性透過網際網路連線的網站-Vpn 比較。

深入瞭解︰

- [使用 Azure 資源管理員與 PowerShell 設定 VNet-VNet 連線](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>顯示狀態
可用性是任何安全性程式的主要元件。 如果您的使用者和系統無法存取其需要透過網路存取，可以遭到盜用視為服務。 Azure 具有網路的技術支援下列高可用性機制︰

- HTTP 負載平衡
- 網路層級負載平衡
- 全域負載平衡

負載平衡是設計用來平均分散多個裝置之間的連線機制。 負載平衡的目標是︰

- 提高可用性 –，當您在多個裝置上負載平衡連線、 一或多個裝置可能無法使用及剩餘的線上裝置上執行的服務繼續處理來自該服務的內容
- 提升效能-當您在多個裝置上載入平衡連線，才能處理器點擊沒有單一的裝置。 不過，提供內容處理和記憶體要求橫跨多個裝置。

### <a name="http-based-load-balancing"></a>HTTP 負載平衡
經常執行 web 服務的組織想要讓前面 web 服務可協助確保適當的層級的效能及可用性 HTTP 為基礎的負載平衡器。 相較於傳統的網路負載平衡器、 負載平衡 HTTP 為基礎的決策負載平衡器會根據特性 HTTP 通訊協定，不是在網路和傳輸通訊協定。

若要提供 HTTP 負載平衡 web 服務，Azure 可讓您 Azure 應用程式閘道器。 Azure 應用程式閘道支援︰

- HTTP 型負載平衡 – 負載平衡決策進行根據特性特殊 HTTP 通訊協定
- Cookie 為基礎的工作階段相關性 – 此功能可確保其中一個之後的負載平衡器伺服器建立連線保持不變，用戶端和伺服器之間。 如此交易的穩定性。
- SSL 卸載 – 當用戶端連線建立負載平衡器與用戶端與負載平衡器之間的工作階段使用 HTTPS 加密 (SSL /) 通訊協定。 不過，以提升效能，您可以將負載平衡器與負載平衡器使用 HTTP （未加密） 通訊協定的網頁伺服器之間的連線。 這被稱為 「 SSL 卸載 」 因為前負載平衡器的網頁伺服器與沒有經驗的處理器花費使用加密，因此應該能服務要求更快速地。
- URL 型傳閱內容以 – 這項功能可讓負載平衡器決定要轉寄的目標 URL 所根據的連線。 這會提供更多的彈性，比解決方案的負載平衡根據 IP 位址。

深入瞭解︰

- [應用程式閘道器概觀](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>網路層級負載平衡
相較於 HTTP 負載平衡、 網路層級的負載平衡可讓您根據 IP 位址和連接埠 （TCP 或 UDP） 的數字的負載平衡決策。
您可以取得網路層級負載平衡 Azure 中使用 Azure 負載平衡器優點。 Azure 負載平衡器的一些主要特性包括︰

- 網路層級的負載平衡根據 IP 位址和連接埠號碼
- 支援的任何應用程式層通訊協定
- 負載平衡 Azure 虛擬機器及雲端服務的角色執行個體
- 可以使用網際網路 （外部負載平衡）] 及 [非網際網路對 （內部負載平衡） 應用程式與虛擬機器
- 這用來決定是否任一項服務前負載平衡器無法監控端點

深入瞭解︰

- [網際網路對多個虛擬機器或服務之間的負載平衡器](../load-balancer/load-balancer-internet-overview.md)
- [內部負載平衡器概觀](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>全域負載平衡
在某些組織會想可能最高層級的可用性。 若要達到此目標的其中一個方法是全域分散式資料中心的主應用程式。 應用程式裝載在位於全世界的資料中心，時，可能會變成無法使用，並仍應用程式的整個淉區域，而且正在執行。

除了您取得裝載全域分散式資料中心的應用程式的可用性優點，您也可以取得效能。 使用機制導引至最接近的裝置，提出要求資料中心的服務的要求，您可以取得這些效能優點。

全域負載平衡可以提供您兩個優點。 Azure 中，您可以取得全域的負載平衡使用 Azure 流量管理員的優點。

深入瞭解︰

- [什麼是流量管理員？](../traffic-manager/traffic-manager-overview.md)

## <a name="logging"></a>記錄
在網路層級的記錄功能是任何網路安全性案例的主要功能。 Azure 中您可以將資訊記錄取得網路安全性群組，以取得網路層級的記錄資訊。 使用 NSG 記錄，您會收到的資訊︰

- 稽核記錄 – 這些記錄用來檢視您 Azure 訂閱提交的所有作業。 依預設會啟用這些記錄，然後 Azure 入口網站中可以使用。
- 事件記錄檔 – 這些記錄提供哪些 NSG 規則已套用的相關資訊。
- 計數器記錄 – 這些記錄檔可讓您知道每個 NSG 規則已套用至拒絕或允許流量的次數。

您也可以使用[Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/)，一個功能強大的資料視覺效果的工具，來檢視和分析這些記錄。

深入瞭解︰

- [網路安全性群組 (NSGs) 記錄狀況分析](../virtual-network/virtual-network-nsg-manage-log.md)

## <a name="name-resolution"></a>名稱解析
名稱解析是您主控 Azure 中的所有服務的重要功能。 從安全性的觀點，洩漏的名稱解析函數可能會導致攻擊重新導向您的網站攻擊的網站的要求。 安全的名稱解析是您所有的雲端裝載服務要求。

有兩種類型的名稱解析您需要地址︰

- Azure 虛擬網路、 內部網路，或兩者的服務會使用內部名稱解析 – 內部名稱解析。 用於內部名稱解析的名稱並不在網際網路上存取。 為獲得最佳的安全性，請務必您內部名稱解析配置不是外部使用者存取。
- 使用外部名稱解析 – 外部名稱解析人員與您的內部部署和 Azure 虛擬網路以外的裝置。 這些是名稱會顯示至網際網路，而且用來直接雲端服務的連線。

內部名稱解析，您有兩個選項︰

- Azure 虛擬網路 DNS 伺服器 – 當您建立新的 Azure 虛擬網路，DNS 伺服器是為您建立。 這個 DNS 伺服器可以解決該 Azure 虛擬網路上的電腦的名稱。 這個 DNS 伺服器可設定並不由更安全的名稱解析解決方案的 Azure 布料的轉印圖樣管理員來管理。
- 將您自己的 DNS 伺服器，因此您可以選擇將 Azure 虛擬網路上選擇自己的 DNS 伺服器。 這個 DNS 伺服器可能 Active Directory 整合 DNS 伺服器或 Azure 的合作夥伴，您可以從 Azure Marketplace 取得所提供的專用的 DNS 伺服器解決方案。

深入瞭解︰

- [虛擬網路概觀](../virtual-network/virtual-networks-overview.md)
- [管理虛擬網路 (VNet) 所使用的 DNS 伺服器](../virtual-network/virtual-networks-manage-dns-in-vnet.md)

外部的 DNS 解析，將這兩個選項︰

- 主控您的外部 DNS 伺服器內部部署
- 服務提供者主控您自己的外部 DNS 伺服器

許多大型組織主控自己的 DNS 伺服器內部部署。 因為他們的網路專業知識及全域的目前狀態，若要這麼做，他們可以執行此動作。

在大部分情況下，最好的服務提供者主控您的 DNS 名稱解析服務。 這些服務提供者有的網路專業知識及全域的目前狀態，以確保您的名稱解析服務可用性更高。 如果您的名稱解析服務失敗，任何人都能達到您對服務的網際網路之後，所以不可或缺的 DNS 服務的可用性。

Azure 會提供您高度可用和形式的 Azure DNS 效能外部 DNS 方案。 此外部名稱解析解決方案利用全球 Azure DNS 基礎結構。 其可讓您主控您的網域中 Azure 使用相同的認證，Api，工具，與其他 Azure 服務與計費。 Azure 的一部分，它也會繼承平內建的強大的安全性控制項。

深入瞭解︰

- [Azure DNS 概觀](../dns/dns-overview.md)

## <a name="dmz-architecture"></a>DMZ 架構
許多企業組織使用 Dmz 區段建立網際網路和其服務之間緩衝區域網路。 網路的 DMZ 部分會被視為低安全性區域，並沒有最高值資產會放在該網路區段。 通常，您會看到有網路介面 DMZ 區段和其他網路介面連線到網路有虛擬機器及接受傳入的連線，從網際網路服務的網路安全性裝置。

有幾種變化 DMZ 設計和部署 DMZ 何種類型的 DMZ 使用如果您決定要使用其中一個，然後根據您的網路安全性需求。

深入瞭解︰

- [Microsoft 雲端服務和網路安全性](../best-practices-network-security.md)

## <a name="azure-security-center"></a>Azure 資訊安全中心
資訊安全中心 」 可協助您避免偵測到，與回應威脅，，並提供您增加完善，並控制，您 Azure 資源的安全性。 整合式的安全性的監控和原則管理提供您 Azure 訂閱，有助於偵測威脅，否則請忽略和相關事務的安全性解決方案的主要生態系統。

Azure 資訊安全中心 」 可協助您最佳化，並監控所網路安全性︰

- 提供網路安全性的建議
- 監控您的網路安全性設定的狀態
- 提醒您的網路基礎潛在威脅兩個端點和網路層級

深入瞭解︰

- [Azure 安全性中心簡介](../security-center/security-center-intro.md)
