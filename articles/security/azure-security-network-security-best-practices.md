<properties
   pageTitle="Azure 網路安全性的最佳作法 |Microsoft Azure"
   description="本文提供一組網路安全性使用內建於 Azure 功能的最佳做法。"
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="swadhwa"
   editor="TomShinder"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/25/2016"
   ms.author="TomSh"/>

# <a name="azure-network-security-best-practices"></a>Azure 網路安全性的最佳作法

Microsoft Azure 可讓您連線虛擬機器及設備到其他網路的裝置放置在 Azure 虛擬網路上。 Azure 虛擬網路是可讓您將虛擬網路介面卡連線到要允許 TCP 型網路啟用裝置之間的通訊的虛擬網路虛擬網路架構。 連線到 Azure 虛擬網路 azure 虛擬機器都能連線至相同 Azure 虛擬在網路上其他 Azure 虛擬網路，在網際網路上或甚至在您的內部網路上的裝置。

本文中，我們將討論各種 Azure 網路安全性的最佳作法。 這些最佳做法從 Azure 網路的經驗，並的客戶體驗等自己。

針對每個最佳作法，我們會說明︰

- 最佳作法是什麼
- 為什麼您要啟用的最佳作法
- 如果您無法啟用的最佳做法，可能結果
- 可能的替代方案的最佳作法
- 您如何可以瞭解如何啟用的最佳作法

本文 Azure 網路安全性的最佳作法根據一致的意見和 Azure 平台功能的功能集，本文寫入的時間點。 意見與技術變更一段時間，本文會以反映那些變更定期更新。

Azure 網路安全性最佳作法本文所述包括︰

- 邏輯區段子網路
- 控制路由行為
- 啟用強制通道
- 使用虛擬網路設備
- 部署安全性分區 Dmz
- 避免發生意外的專用的 WAN 連結網際網路
- 最佳化的執行時間和效能
- 使用全域負載平衡
- 停用 RDP 存取 Azure 虛擬機器
- 啟用 Azure 資訊安全中心
- 將您的資料中心延伸至 Azure


## <a name="logically-segment-subnets"></a>邏輯區段子網路

在您的內部網路上的區域網路[Azure 虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/)類似。 Azure 虛擬網路的概念會為您建立單一私人 IP 位址以空格為基礎的網路，您可以在其中放置所有您[Azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)。 私用的 IP 位址空間是類別 (10.0.0.0/8)、 類別 B (172.16.0.0/12) 和類別 C (192.168.0.0/16) 範圍。

什麼類似執行內部部署，您會想要將子網路區段的較大的位址空間。 若要建立您子網路，您可以使用[CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)基礎子原則。

子網路之間路由會自動發生，您不需要以手動方式設定路由的資料表。 不過，預設值是您在 Azure 虛擬網路建立子網路之間有沒有網路存取控制項。 若要建立子網路之間的網路存取控制項，必須將子網路之間的項目。

您可以使用來完成這項工作的項目是[網路安全性群組](../virtual-network/virtual-networks-nsg.md)(NSG)。 NSGs 會使用 5 tuple （來源 IP、 來源連接埠、 目的地 IP、 目的地連接埠] 和 [圖層 4 通訊協定） 的簡單的狀態封包檢查裝置的方法來建立允許/拒絕規則網路流量。 您可以接受或拒絕單一 IP 位址，並從多個 IP 位址或甚至與整個子網路流量。

使用子網路之間的網路存取控制 NSGs 可讓您將資源屬於相同的安全性區域或自己的子網路中的角色。 例如，將具有 web 層、 應用程式邏輯層和資料庫層的簡單 3 層應用程式。 您將這些層的每個屬於將自己的子網路的虛擬機器。 然後您可以使用 NSGs 控制子網路之間的資料傳輸︰

- 網頁層虛擬機器只可以啟動應用程式邏輯電腦的連線，並只可以接受從網際網路連線
- 應用程式邏輯虛擬機器只可以啟動與資料庫層連線，並僅可接受從 web 層的連線
- 資料庫層虛擬機器無法啟動連線子網路以外的任何項目，並僅可接受從應用程式邏輯層的連線

若要進一步瞭解網路安全性群組，以及如何使用這些邏輯區段 Azure 虛擬網路，請閱讀，請參閱[什麼是網路安全性群組](../virtual-network/virtual-networks-nsg.md)(NSG)。

## <a name="control-routing-behavior"></a>控制路由行為

當您將虛擬機器放在 Azure 虛擬網路上時，您會發現虛擬機器可以連線到相同 Azure 虛擬在網路上的任何其他虛擬機器即使其他虛擬機器不同子網路上。 為什麼這是通訊的可能的原因是通訊的系統路由，依預設會啟用，讓此類型的集合。 這些預設路由允許虛擬機器 Azure 虛擬網路上進行彼此與 （適用於網際網路只連外通訊） 網際網路連線。

預設系統路由很適合用於有許多部署案例時，有您想来自訂您的部署路由的設定。 這些自訂項目可讓您設定達到特定目的地的下一個躍點地址。

我們建議您設定使用者定義的路徑，當您部署的虛擬網路安全性應用裝置，我們將在更新版本的最佳作法討論。

> [AZURE.NOTE] 使用者定義，而不是必要與預設系統路由運作在大部分的執行個體。

您可以進一步瞭解使用者定義的路徑，以及如何將其設定閱讀[什麼是使用者定義的路徑和 IP 轉寄功能](../virtual-network/virtual-networks-udr-overview.md)的文章。

## <a name="enable-forced-tunneling"></a>啟用強制通道

若要進一步瞭解強制通道，很有用了解哪些 「 分割通道 」。
透過 VPN 連線，會看到分割通道最常見的範例。 假設您旅館聊天室的建立 VPN 連線，您的公司網路。 此連線可讓您連線至公司網路上的資源，並在您的公司網路上的資源的所有通訊 VPN 通道。

當您要連線至網際網路的資源時，會發生什麼情況？ 啟用分割通道時，這些連線，請直接到網際網路，而非透過 VPN 通道。 某些安全性專家，請考慮這可能有風險，因此建議您分割通道停用和所有連線，指向的網際網路以及目的地為公司的資源，幫助 VPN 通道。 執行此動作的優點是，然後強制網際網路連線到公司網路的安全性裝置，如果 VPN 用戶端連線至網際網路 VPN 通道以外，不是大小寫。

現在讓我們來將這回 Azure 虛擬網路上的虛擬機器。 預設的路由 Azure 虛擬網路允許虛擬機器以進行網際網路流量。 這也可以代表安全性風險，這些傳出連線可能會增加攻擊的虛擬機器並加以利用攻擊。
因此，建議您啟用強制通道上您的虛擬機器 Azure 虛擬網路與內部部署網路之間有跨內部部署連線時。 我們會討論交叉部署連線，本文稍後 Azure 網路最佳作法。

如果您沒有交叉部署連線，請確定您的網路安全性群組 （討論較舊版本） 或 Azure 虛擬利用網路以防止傳出連線至網際網路 Azure 虛擬機器中的安全性就 （討論的下一步）。

若要進一步瞭解強制通道，以及如何啟用它，請閱讀[設定強制通道](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)使用 PowerShell 和 Azure 資源管理員。

## <a name="use-virtual-network-appliances"></a>使用虛擬網路設備

雖然網路安全性群組及使用者定義路由可以提供的網路安全性[OSI 模型](https://en.wikipedia.org/wiki/OSI_model)網路和傳輸層級的特定量值，那里要成為您會想要或需要啟用安全性層級高的堆疊場合。 在這種情況下，我們建議您部署虛擬網路安全性應用裝置提供 Azure 合作夥伴。

Azure 網路安全性就可以透過網路層級的控制項來所提供的內容傳遞大幅增強的安全性層級。 虛擬網路安全性應用裝置所提供的網路安全性功能包括︰

- 防火牆功能
- 侵入偵測/侵入防止
- 弱點管理
- 應用程式控制項
- 網路基礎異常偵測
- 網站篩選
- 防毒軟體
- Botnet 保護

如果您需要較高的安全性層級網路比您可以取得網路層級存取控制項，然後我們建議您調查並部署 Azure 虛擬網路安全性應用裝置。

若要瞭解有關哪些 Azure 虛擬網路安全性就可供使用，以及其功能，請造訪[Azure Marketplace](https://azure.microsoft.com/marketplace/) ，搜尋 「 安全性 」 和 「 網路安全性]。

##<a name="deploy-dmzs-for-security-zoning"></a>部署安全性分區 Dmz
DMZ 或 「 周邊網路 「 實體或邏輯網路區段是設計用來提供額外的安全性，您的資產與網際網路之間的階層。 DMZ 的目的是特殊的網路存取控制裝置置於 DMZ 網路的邊緣，以便過去的網路安全性裝置，然後放入 Azure 虛擬網路允許只想要的流量。

監控、 記錄和 Azure 虛擬網路的邊緣報告的裝置上，您就能專注您管理網路存取控制項，因為，Dmz 很有用。 以下您想要通常是啟用 DDoS 防止、 侵入偵測/侵入防止系統 (識別碼/IP)、 防火牆規則和原則、 網站篩選、 網路反惡意程式碼和更多。 網路安全性裝置坐網際網路和 Azure 虛擬網路之間，而且有兩個網路上的介面。

雖然這是基本 DMZ 的設計，有許多不同的 DMZ 設計，例如背對背三位於、 多重，與其他人。

我們建議您考量部署，以增強網路 Azure 資源的安全性層級 DMZ 所有高的安全性部署。

若要進一步瞭解 Dmz，以及如何將其部署 Azure 中，請閱讀[Microsoft 雲端服務和網路安全性](../best-practices-network-security.md)的文章。

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>避免發生意外的專用的 WAN 連結網際網路
許多組織所選擇的混合式 IT 路由。 在混合式 IT，公司的資訊資產部分是 Azure，其他人仍然內部部署。 在許多情況下服務的部分元件會執行 Azure 中其他元件仍然內部部署。

在混合式部署 IT 情況，通常沒有某些類型的跨內部部署連線。 此跨內部部署連線，讓他們的內部部署網路連線至 Azure 虛擬網路公司。 有兩種跨內部連接解決方案︰

- 若要網站 VPN
- ExpressRoute

[網站-VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)代表您的內部網路和 Azure 虛擬網路之間的虛擬私人連線。 此連線在網際網路上生效，並可讓您內部網路和 Azure 之間的加密連結 」 通道 」 資訊。 網站-VPN 是由的各種企業部署表明安全、 成熟技術。 加密通道使用[IPsec 通道模式](https://technet.microsoft.com/library/cc786385.aspx)執行。

信任、 可靠且既有的技術網站-VPN 時，在通道中的流量會往返網際網路。 此外，頻寬以往 200Mbps 相關的最大限制。

如果您需要的安全性或效能卓越層級的跨內部部署連線時，建議您使用跨內部部署連線 Azure ExpressRoute。 ExpressRoute 是專用的 WAN 您內部部署的位置或 Exchange 的主機服務提供者之間的連結。 這是電話連線，因為您的資料無法透過網際網路傳送，因此不會公開給固有網際網路通訊的潛在風險。

若要進一步瞭解 Azure ExpressRoute 的運作方式，以及如何部署，請閱讀本文[ExpressRoute 技術概觀](../expressroute/expressroute-introduction.md)。

## <a name="optimize-uptime-and-performance"></a>最佳化的執行時間和效能
機密性、 完整性與顯示狀態 (感謝 CIA) 包含今天最有影響力的安全性模型三角理論。 機密性的加密和隱私權完整性是如何確保未經授權的人員無法變更資料，並顯示狀態是並確認授權的個人都可以存取他們有權存取的資訊。 這些區域中，任何一個失敗表示的潛在漏洞安全性。

可用性可以視為有關的執行時間和效能。 向下服務時，無法存取資訊。 如果為讓資料無法使用，因此不佳的效能，我們可以考慮無法存取資料。 因此，從安全性的觀點，我們需要執行上述程序我們可以請務必確保我們的服務有最佳的執行時間和效能。
用來強化可用性和效能的常用且有效方法是使用負載平衡。 負載平衡是服務的一種方法將網路流量分散到伺服器所包含。 比方說，如果您有前端網頁伺服器，做為您的服務的一部分，您可以使用負載平衡若要將流量分散到多個前端網頁伺服器。

此分配的流量會增加可用性，因為如果其中一個網頁伺服器無法使用，負載平衡器會停止傳送至該伺服器的流量與流量重新導向至線上仍的伺服器。 負載平衡亦有助於效能，因為處理器、 網路和記憶體早餐要求開銷分散到所有的負載平衡伺服器。

我們建議您針對採用負載平衡時，視您的服務。 我們會將下列各節中的查看其適地址。
Azure 虛擬網路層級，Azure 會提供您與三個主要負載平衡選項︰

- HTTP 負載平衡
- 外部負載平衡
- 內部負載平衡

## <a name="http-based-load-balancing"></a>HTTP 負載平衡
HTTP 負載平衡會決定哪些伺服器傳送使用 HTTP 通訊協定的特性連線的相關的決策。 Azure 有進入的應用程式的閘道器名稱 HTTP 負載平衡器。

我們建議您我們 Azure 應用程式的閘道器時︰

- 需要達到相同的後端虛擬機器相同的使用者/用戶端工作階段要求的應用程式。 購物車應用程式與 web 電子郵件伺服器，便購物的範例。
- 想要利用應用程式閘道[SSL 卸載](https://f5.com/glossary/ssl-offloading)功能空閒 SSL 終止開銷從網頁伺服器陣列的應用程式。
- 需要多個路由傳送或載入的相同長 TCP 連接 HTTP 要求平衡到不同的後端伺服器應用程式，例如內容傳遞網路。

若要瞭解更多關於 Azure 應用程式的閘道器的運作方式及如何使用它在您的部署，請閱讀本文[應用程式閘道器的概觀](../application-gateway/application-gateway-introduction.md)。

## <a name="external-load-balancing"></a>外部負載平衡
從網際網路連入連線負載平衡所有位於 Azure 虛擬網路伺服器時，外部負載平衡發生地點。 Azure 外部負載平衡器可以提供此功能，建議您時，使用，您不需要自黏工作階段，或卸載 SSL。

相較於 HTTP 負載平衡、 外部負載平衡器會使用資訊，網路傳輸層級 OSI 網路模型，做出決策載入平衡連線至何種伺服器上。

我們建議您使用外部負載平衡隨時接受傳入的邀請，從網際網路[無狀態應用程式](http://whatis.techtarget.com/definition/stateless-app)。

若要進一步瞭解 Azure 外部負載平衡器的運作方式及如何部署，請閱讀的文件[建立網際網路對負載平衡器資源管理員] 中使用 PowerShell 的 [快速入門](../load-balancer/load-balancer-get-started-internet-arm-ps.md)。

## <a name="internal-load-balancing"></a>內部負載平衡
內部負載平衡外部負載平衡與類似，並使用相同的機制負載平衡連線到伺服器後方。 唯一的差別的負載平衡器在本例中為接受來自連線不是在網際網路的虛擬機器。 在大部分情況下，可接受的負載平衡連線的起始者 Azure 虛擬網路上的裝置。

我們建議您使用內部的負載平衡助益從這項功能，例如當您需要負載平衡連線至 SQL Server 或內部網頁伺服器的案例。

若要進一步瞭解如何 Azure 內部的負載平衡運作，以及如何將其部署，請閱讀的文件[建立使用 PowerShell 內部負載平衡器的 [快速入門](../load-balancer/load-balancer-get-started-internet-arm-ps.md#update-an-existing-load-balancer)。

## <a name="use-global-load-balancing"></a>使用全域負載平衡
公用雲端運算會讓它可以全面部署的分散式應用程式有位於全世界的資料中心的元件。 這可能是在 Microsoft Azure 由 Azure 的全域資料中心的目前狀態。 相較於負載平衡先前所述的技術，全域負載平衡可讓整個資料中心可能會無法使用時，即使讓服務使用。

您可以取得此類型的全域之負載平衡 Azure 中利用[Azure 流量管理員](https://azure.microsoft.com/documentation/services/traffic-manager/)。 便流量管理員可利用負載平衡服務根據使用者的位置的連線。

例如，若使用者從歐盟，以進行您的服務要求，請連線導向至您位於歐盟資料中心的服務。 此組件的流量管理員全域載入平衡協助改善效能，因為連線到最接近的資料中心連線到資料中心的遠比快。

在可用性方面，全域負載平衡可確保您的服務會提供，不論是否應該才可供整個資料中心。

例如，如果 Azure 資料中心應該環境的原因或中斷 （例如區域網路失敗） 而無法使用，連線到您的服務會路徑，以便最近的線上的資料中心。 利用 DNS 原則，您可以建立在流量管理員來完成這個全域負載平衡。

我們建議您使用的任何雲端解決方案開發有普遍的範圍的跨多個區域，並且需要最高層級的執行時間可能流量管理員。

若要進一步瞭解 Azure 流量管理員，以及如何將其部署，請參閱[什麼是流量管理員](../traffic-manager/traffic-manager-overview.md)文章。

## <a name="disable-rdpssh-access-to-azure-virtual-machines"></a>停用 RDP/SSH 存取 Azure 虛擬機器
請可達到 Azure 虛擬機器使用[遠端桌面通訊協定](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol)(RDP) 和[安全命令介面](https://en.wikipedia.org/wiki/Secure_Shell)(SSH) 通訊協定。 這些通訊協定讓您從遠端位置管理虛擬機器中且標準的資料中心的電腦。

透過網際網路使用這些通訊協定的潛在安全性問題是攻擊，可以使用各種不同的[暴力](https://en.wikipedia.org/wiki/Brute-force_attack)技術來存取至 Azure 虛擬機器。 一旦攻擊存取，他們可以作為您的虛擬機器啟動點危害 Azure 虛擬網路上的其他電腦，或甚至攻擊 Azure 外部網路的裝置。

因此，建議您停用直接 RDP 和 SSH 存取您 Azure 虛擬機器從網際網路。 直接存取 RDP 和 SSH 從網際網路] 會停用後，您會有其他選項，您可以用來存取遠端管理這些虛擬機器︰

- 點-網站 VPN
- 若要網站 VPN
- ExpressRoute

[點對網站 VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md)是另一個字詞的遠端存取 VPN 用戶端/伺服器連線。 點-網站 VPN 可讓單一使用者透過網際網路連線至 Azure 虛擬網路。 點-網站連線之後，使用者將能夠使用 RDP 或 SSH 連線至位於 Azure 虛擬網路使用者透過點為網站 VPN 連線至的任何虛擬機器。 這是假設達到這些虛擬機器授權的使用者。

點-網站 VPN 是比直接 RDP 或 SSH 連線更安全，因為使用者進行驗證連線至虛擬機器之前，按兩次。 首先，使用者必須驗證 （和授權） 建立點為網站 VPN 連線。第二，使用者必須驗證 （和授權） 建立 RDP 或 SSH 工作階段。

[網站-VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)整個網路連線到其他網路透過網際網路。 您可以使用網站-VPN 連線至 Azure 虛擬網路的內部部署網路。 如果您部署網站-VPN，使用者內部部署網路都能連線至 Azure 虛擬網路上的虛擬機器使用 RDP 或 SSH 通訊協定網站-VPN 連線，然後不需要您允許直接 RDP 或 SSH 存取網際網路。

您也可以使用專用的 WAN 連結，以提供網站-VPN 類似的功能。 主要差異為 1。 網際網路、 和 2，無法通過專用的 WAN 連結。 更多穩定，效能，通常是專用的 WAN 連結。 Azure 可讓您在表單中的[ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/)專用的 WAN 連結解決方案。

## <a name="enable-azure-security-center"></a>啟用 Azure 資訊安全中心
Azure 資訊安全中心 」 可協助您避免偵測到，與回應威脅，，並提供您增加完善，並控制，您 Azure 資源的安全性。 整合式的安全性的監控和原則管理提供您 Azure 訂閱，有助於偵測威脅，否則請忽略和相關事務的安全性解決方案的主要生態系統。

Azure 資訊安全中心 」 可協助您最佳化，並監控所網路安全性︰

- 提供網路安全性的建議
- 監控您的網路安全性設定的狀態
- 提醒您的網路基礎潛在威脅兩個端點和網路層級

我們強烈建議您在針對您所有的 Azure 部署啟用 Azure 資訊安全中心。

若要進一步瞭解 Azure 資訊安全中心 」，以及如何啟用您的部署，請閱讀[Azure 資訊安全中心](../security-center/security-center-intro.md)簡介。

## <a name="securely-extend-your-datacenter-into-azure"></a>安全地將您的資料中心延伸至 Azure
許多企業 IT 組織想要展開至雲端，而不是成長他們的內部部署資料中心。 此擴充至公用雲端代表現有的 IT 基礎結構的副檔名。 利用跨內部部署連線選項可能是 Azure 虛擬網路視為只是另一個子網路上您的內部部署網路基礎結構。

然而，有許多規劃及設計需要先解決的問題。 這是特別重要的網路安全性區域中。 若要瞭解如何處理種這類設計的最佳方式是範例。

Microsoft 所建立[的資料中心副檔名參考架構圖表](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84#content)和支援資料來協助您瞭解這類資料中心副檔名的外觀。 這會提供您可以用來規劃及設計雲端副檔名為安全的企業資料中心的參考實作範例。 我們建議您檢閱這份文件，以瞭解安全解決方案的主要元件。

若要瞭解如何安全地將您的資料中心延伸至 Azure 詳細資訊，請檢視影片[延伸到 Microsoft Azure 的您資料中心](https://www.youtube.com/watch?v=Th1oQQCb2KA)。
