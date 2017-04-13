<properties 
   pageTitle="虛擬網路 VPN 閘道器常見問題集 |Microsoft Azure"
   description="VPN 閘道器常見問題集。 Microsoft Azure 虛擬網路跨內部部署連線、 混合式設定連線，以及要有 vpn 才能閘道器的常見問題集"
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/10/2016"
   ms.author="yushwang" />

# <a name="vpn-gateway-faq"></a>VPN 閘道器常見問題集

## <a name="connecting-to-virtual-networks"></a>連線至虛擬網路

### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>我可以連線在不同的 Azure 區域內的虛擬網路？
[是]。 事實上，沒有地區限制式。 虛擬網路可以連線至相同的區域，或在不同的 Azure 區域中的另一個虛擬網路。

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>我可以連線虛擬網路中的不同訂閱？
[是]。

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>我可以連線至多個網站的單一虛擬網路嗎？

使用 Windows PowerShell 和 Azure REST Api，您可以連線至多個網站。 請參閱[多網站及 VNet-VNet 連線](#multi-site-and-vnet-to-vnet-connectivity)的常見問題集。
## <a name="what-are-my-cross-premises-connection-options"></a>什麼是我跨內部部署連線選項？

支援下列跨內部部署連線︰

- [網站-](vpn-gateway-site-to-site-create.md) -IPsec （IKE v1 和 IKE v2） 上的 VPN 連線。 此連線類型需要 VPN 裝置或 RRAS。

- [點-網站](vpn-gateway-point-to-site-create.md)– SSTP （安全通訊端通道通訊協定） 透過 VPN 連線。 此連線時，不需要 VPN 裝置。

- [VNet-VNet](virtual-networks-configure-vnet-to-vnet-connection.md) – 此連線類型是網站的網站設定相同。 以 VNet VNet 是 VPN 連線透過 IPsec （IKE v1 和 IKE v2）。 不需要 VPN 裝置。

- [多網站](vpn-gateway-multi-site.md)– 這是一種設定可讓您連線到虛擬網路的多個內部部署網站，網站的網站。

- [ExpressRoute](../expressroute/expressroute-introduction.md) – ExpressRoute 是直接連接到 Azure 您 WAN，不透過公用網際網路。 請參閱[ExpressRoute 技術概觀](../expressroute/expressroute-introduction.md)」 和 「 [ExpressRoute 常見問題集](../expressroute/expressroute-faqs.md)的詳細資訊。

如需連線的詳細資訊，請參閱[關於 VPN 閘道器](vpn-gateway-about-vpngateways.md)。

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>什麼是網站-連線與點為網站之間的差異？

**網站的**連線，可讓您連線之間的任何電腦位於您的部署任何虛擬機器或虛擬網路，根據您選擇如何設定路由中的角色執行個體。 它是很棒的選項，隨時都可以使用跨內部部署連線，並非常適合混合式設定。 此連線類型依賴 IPsec VPN 應用裝置 （硬體或柔邊的應用裝置） 必須部署在您的網路的邊緣。 若要建立此連線類型，您必須必要的 VPN 硬體和對外 IPv4 位址。

**點-網站**連線可讓您從任何位置的單一電腦連線到您的虛擬網路中的任何項目。 使用 Windows 中的方塊 VPN 用戶端。 點-網站組態的一部分，您可以安裝憑證和 VPN 用戶端設定套件，其中包含可讓您的電腦連線至任何虛擬機器或虛擬網路中的角色執行個體的設定。 當您想要連線至虛擬網路，且不位於內部部署很棒。 當您沒有要有 vpn 才能硬體或對外 IPv4 位址，都需要連線至網站的存取權時，它是也很好的選項。 

假設您建立閘道使用路由型 VPN 類型您網站的連線，您可以設定您虛擬網路的同時，使用點對網站和網站的網站。 傳送型 VPN 類型稱為動態閘道器在傳統的部署模型中。

### <a name="what-is-expressroute"></a>什麼是 ExpressRoute？

ExpressRoute 可讓您私人之間建立關聯 Microsoft 資料中心，在您的內部部署或代管環境中的基礎結構。 與 ExpressRoute，您可以建立連線至 Microsoft 雲端服務，例如 Microsoft Azure 和 Office 365 在 ExpressRoute 合作夥伴共同位置的設備，或直接從您現有的 WAN 網路 （例如網路服務提供者所提供的 MPLS VPN) 連線。

ExpressRoute 連線會提供更好的安全性、 更多可靠性，較高的頻寬，與下方的延遲時間，比一般連線透過網際網路。 在某些情況下，使用您的內部部署網路和 Azure 之間傳送資料的 ExpressRoute 連線可能也會產生嚴重成本優點。 如果您已經建立交互內部部署連線從您的內部部署網路至 Azure，您可以移轉到 ExpressRoute 連線，並讓您虛擬的網路保持不變。

請參閱[ExpressRoute 常見問題集](../expressroute/expressroute-faqs.md)，如需詳細資訊。

## <a name="site-to-site-connections-and-vpn-devices"></a>網站的連線和 VPN 裝置

### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>選取要有 vpn 才能裝置時，我應該考慮什麼？

我們確認標準網站-VPN 裝置合作關係中的一組與裝置廠商。 您可以找到清單已知相容的 VPN 裝置、 其對應的設定指示或範例和裝置規格[以下](vpn-gateway-about-vpn-devices.md)。 已知的相容的方式列出裝置系列中的所有裝置應該都使用虛擬網路。 若要協助設定 VPN 裝置，請參閱裝置設定範例或對應到適當的裝置系列中的連結。

### <a name="what-do-i-do-if-i-have-a-vpn-device-that-isnt-in-the-known-compatible-device-list"></a>如果我有不在已知相容的裝置清單中的 VPN 裝置我該怎麼做？

如果您沒有看到您的裝置已知的相容 VPN 裝置的方式列出，而您想要用於您的 VPN 連線，您必須驗證支援 IPsec/IKE 設定選項和參數列出[以下](vpn-gateway-about-vpn-devices.md#devices-not-on-the-compatible-list)符合。 裝置會議的最低需求也應該使用 VPN 閘道器。 取得其他支援與設定指示，請連絡您的裝置製造商。

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>為什麼我原則型 VPN 通道到閒置流量時？

這是預期的行為的原則 （也稱為靜態路由） VPN 閘道器。 在透過通道流量閒置超過 5 分鐘，通道會結束。 當流量啟動以任意方向書寫方向時，通道就會立即重新建立。

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>可以使用軟體 Vpn 連線至 Azure 嗎？

我們網站-跨內部部署設定的支援 Windows Server 2012 路由及遠端存取 (RRAS) 伺服器。

只要其符合工業標準 IPsec 實作其他軟體 VPN 解決方案應該使用我們的閘道器。 如需設定和支援的指示，請連絡軟體廠商。

## <a name="point-to-site-connections"></a>點-網站的連線

### <a name="what-operating-systems-can-i-use-with-point-to-site"></a>以點為網站可以使用哪些作業系統？

支援下列作業系統︰

- Windows 7 （32 位元與 64 位元）

- Windows Server 2008 R2 （僅限 64 位元）

- Windows 8 （32 位元與 64 位元）

- Windows 8.1 （32 位元與 64 位元）

- Windows Server 2012 （僅限 64 位元）

- Windows Server 2012 R2 （僅限 64 位元）

- 在 Windows 10

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp"></a>我可以使用點對網站支援 SSTP 的任何軟體 VPN 用戶端嗎？

[否]。 支援並僅限於以上所列的 Windows 作業系統版本。

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>我點為網站設定中可以擁有多少 VPN 用戶端結束點？

我們支援 128 個 VPN 用戶端可以同時虛擬網路連線。

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>可以使用自己內部 PKI 根 CA 點為網站連線嗎？

[是]。 之前，您可以使用僅自我簽署的根憑證。 您仍然可以上傳 20 根憑證。

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>可以往返 proxy 和防火牆使用點為網站功能？

[是]。 我們使用透過防火牆通道 SSTP （安全通訊端通道通訊協定）。 此通道會顯示為 HTTPs 連線。

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>如果我重新設定點-網站的用戶端電腦時，會 VPN 自動重新連線？

根據預設，用戶端電腦會無法重新建立 VPN 連線自動。

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>是否台的點支援自動重新連線並在 VPN 用戶端上的 DDNS 嗎？

自動重新連線及 DDNS 目前不支援點為網站 Vpn。

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>我可以使用網站的網站和點為網站組態並存，為相同的虛擬網路嗎？

[是]。 如果您有一個 RouteBased VPN 類型閘道，就能使用這兩個這些解決方案。 對於傳統的部署模型，您需要動態閘道器。 我們執行不支援點對網站的靜態路由 VPN 閘道器或使用-VpnType PolicyBased 的閘道器。

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>設定同時以多個虛擬網路連線的點為網站用戶端？

是的可能是。 但虛擬的網路不能有重疊 IP 前置詞，虛擬網路之間不能重疊的點為網站位址空間。

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>我預期會透過連線至網站或點為網站多少處理量？

很難維護 VPN 通道的確切處理量。 IPsec 和 SSTP 是加密大量 VPN 通訊協定。 處理量也會限制您部署與網際網路之間的頻寬與延遲。

## <a name="gateways"></a>閘道器

### <a name="what-is-a-policy-based-static-routing-gateway"></a>什麼是原則 （靜態路由） 閘道器？

原則為基礎的閘道器實作原則 Vpn。 原則 Vpn 加密，並直接封包透過 IPsec 通道，根據您的內部網路和 Azure VNet 之間的地址前置字元的組合。 原則 （或流量選取器） 定義通常是 VPN 設定的存取清單。

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>什麼是路由型 （動態路由） 閘道器？

路由為基礎的閘道器實作路由 Vpn。 傳送 Vpn IP 轉寄或路由至直接封包的資料表，將其對應的通道介面中使用 「 路由 」。 通道介面然後加密，或與通道登出封包解密。 傳送根據 Vpn 的原則] 或 [流量選取器設定為任何-至-任何 （或萬用字元）。

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>我在建立前可以取得我 VPN 閘道器的 IP 位址？

[否]。 您必須建立閘道第一次以取得 IP 位址。 如果您刪除並重新建立 VPN 閘道，就會變更的 IP 位址。

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>我如何 VPN 通道取得驗證？

Azure VPN 使用 PSK （預先共用按鍵） 驗證。 我們建立 VPN 通道時，我們就會產生預先共用的金鑰 (PSK)。 您可以針對您的設定預先共用金鑰 PowerShell cmdlet 或 REST API 變更自動產生的 PSK。

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>我可以使用設定預先共用金鑰 API 設定 [我原則基礎 （靜態路由） 的閘道器 VPN 嗎？

是的設定預先共用金鑰 API 及 PowerShell 指令程式可用來設定 Azure 原則 （靜態） Vpn 及傳送 （動態） 路由 Vpn。

### <a name="can-i-use-other-authentication-options"></a>可以使用其他驗證選項嗎？

我們所使用的預先共用的金鑰 (PSK) 進行驗證。

### <a name="what-is-the-gateway-subnet-and-why-is-it-needed"></a>什麼是 「 閘道器的子網路 」，以及為何需要？

我們已啟用跨內部部署連線執行的閘道器服務。 

您需要建立閘道器子網路的設定 VPN 閘道您 VNet。 閘道器的所有子網路必須命名 GatewaySubnet 才能正常運作。 沒有名稱閘道器的子網路其他項目。 並不到閘道器的子網路部署 Vm 或其他項目。

閘道器的子網路最小的大小完全取決於您想要建立的設定。 雖然您可以建立閘道器子網路小 /29 某些設定，我們建議您建立閘道器子 /28 或更大 (28 日 / /27、 /26 等等。)。 

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>我可以部署虛擬機器或角色執行個體到我的閘道器子網路嗎？

[否]。

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>如何指定哪些流量通過 VPN 閘道器？

如果您使用的 Azure 傳統入口網站，請新增您想要透過閘道傳送虛擬網路在本機的網路] 下的 [網路] 頁面上的每個範圍。

### <a name="can-i-configure-forced-tunneling"></a>設定強制通道？

[是]。 請參閱[設定強制通道](vpn-gateway-about-forced-tunneling.md)。

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>我可以 Azure 自己 VPN 伺服器設定並使用它來連線到內部部署網路嗎？

是的您可以部署您自己的 VPN 閘道器或從 Azure Marketplace 或建立您自己的 VPN 路由器 Azure 中的伺服器。 您必須設定以確保您的內部網路和您的虛擬網路子網路之間正確路由流量虛擬網路的使用者定義路由。

### <a name="why-are-certain-ports-opened-on-my-vpn-gateway"></a>我要有 vpn 才能閘道器在為什麼開啟特定的連接埠？

它們是必要 Azure 基礎結構通訊。 受 （鎖定） 的 Azure 的憑證。 不正確的憑證，外部項目，包括客戶的這些閘道器，將無法會導致這些端點上的任何效果。

閘道根本上有所是一個 NIC 接通客戶私人網路，且 NIC 對公用網路的多重裝置。 Azure 基礎結構的實體無法善用基於法規遵循的客戶私人網路，因此其需要使用基礎結構通訊公用結束點。 Azure 安全性稽核會定期掃描公用的結束點。


### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>閘道器類型、 需求及處理量的詳細資訊

如需詳細資訊，請參閱[關於 VPN 閘道器設定](vpn-gateway-about-vpn-gateway-settings.md)。

## <a name="multi-site-and-vnet-to-vnet-connectivity"></a>多個網站及 VNet-VNet 連線

### <a name="which-type-of-gateways-can-support-multi-site-and-vnet-to-vnet-connectivity"></a>哪種類型的閘道器可以支援多網站及 VNet-VNet 連線？

只傳送型 （動態路由） Vpn。

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>我可以連線 VNet 與 RouteBased VPN 類型到另一個 VNet 與 PolicyBased VPN 類型？

傳送型 （動態路由）] 就必須使用沒有、 兩個虛擬網路 Vpn。

### <a name="is-the-vnet-to-vnet-traffic-secure"></a>已 VNet-VNet 流量安全嗎？

是的其受 IPsec/IKE 加密。

### <a name="does-vnet-to-vnet-traffic-travel-over-the-azure-backbone"></a>VNet-VNet 流量通過 Azure 時，則會嗎？

[是]。

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>多少內部部署網站與虛擬網路可以虛擬網路連線至？

最大值。 10 結合的基本版和標準動態路由閘道器。高效能 VPN 閘道 30。

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>我可以使用點-網站 Vpn 與使用多個 VPN 通道我虛擬網路嗎？

是的點網站 (P2S) Vpn 可以使用多個內部部署網站及其他虛擬的網路連線 VPN 閘道器。

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>設定多個通道我虛擬網路之間使用多個網站 VPN 我內部部署網站？

否，Azure 虛擬網路與內部部署網站之間的多餘隧道不受支援。

### <a name="can-there-be-overlapping-address-spaces-among-the-connected-virtual-networks-and-on-premises-local-sites"></a>可以那里重疊的地址可連線的虛擬網路和內部部署本機網站之間的空間嗎？

[否]。 重疊的地址空格會導致網路設定檔案上傳或 「 建立虛擬網路 」 失敗。

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>取得更多的頻寬，其他網站-vpn 比單一虛擬網路嗎？

否，所有 VPN 通道，包括點為網站 Vpn 都共用相同的 Azure VPN 閘道和可用的頻寬。

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>可以使用 Azure VPN 閘道器至 「 我的內部部署網站之間或另一個虛擬網路傳輸流量嗎？

**傳統的部署模型**<br>
透過 Azure VPN 閘道轉送流量使用傳統的部署模型，但依賴網路設定檔中的靜態定義的地址空格。 BGP 尚未支援 Azure 虛擬網路與使用傳統的部署模型 VPN 閘道器。 不 BGP，以手動方式定義傳輸位址空間是很容易發生，並不建議使用的錯誤。<br>
**資源管理員部署模型**<br>
如果您使用的資源管理員部署模型，請參閱[BGP](#bgp)如需詳細資訊。

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Azure 會產生的相同的虛擬網路我 VPN 連線相同的 IPsec/IKE 預先共用的金鑰？

否，Azure 依預設會產生不同 VPN 連線的不同預先共用的的金鑰。 不過，您可以使用設定 VPN 閘道器金鑰 REST API 或 PowerShell cmdlet 來設定您想要的索引鍵值。 索引鍵必須 alphanumerical 字串的長度介於 1 到 128 個字元。

### <a name="does-azure-charge-for-traffic-between-virtual-networks"></a>Azure 收取流量虛擬網路之間的費用嗎？

不同的 Azure 虛擬網路流量，Azure 費用僅適用於流量到另一個通過從一個 Azure 的區域。 Azure [VPN 閘道器價格](https://azure.microsoft.com/pricing/details/vpn-gateway/)] 頁面會列出收費工資率。


### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>我可以連線虛擬網路與 IPsec Vpn 至我的 ExpressRoute 電路嗎？

是的這是受支援。 如需詳細資訊，請參閱[設定 ExpressRoute 和網站-VPN 連線的同時存在](../expressroute/expressroute-howto-coexist-classic.md)。

## <a name="bgp"></a>BGP

[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)] 



## <a name="cross-premises-connectivity-and-vms"></a>跨內部部署連線與 Vm

### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>如果我的虛擬機器中虛擬網路，而有跨內部部署連線，方法應該連線至 VM？

您有幾個選項。 如果您有啟用 RDP 您已經建立端點，您可以使用 VIP 連線至您的虛擬機器。 在此情況下，您可以指定 VIP 和您要連線的連接埠。 您需要設定您的虛擬機器流量的連接埠。 一般而言，您會移至 Azure 傳統入口網站，並將 RDP 連線的設定儲存到電腦。 設定包含必要的連線資訊。

如果您有虛擬網路跨內部部署連線設定時，您可以連線至您的虛擬機器使用內部 DIP 或私用的 IP 位址。 您也可以從另一個位於相同的虛擬網路的虛擬機器連線至您的內部 DIP 的虛擬機器。 您無法 RDP 到您的虛擬機器使用 DIP，如果您從某個位置以外虛擬網路連線。 例如，如果您有設定以點為網站虛擬網路，您不從您的電腦建立連線，您無法連線至 DIP 的虛擬機器。

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>如果我的虛擬機器是在虛擬網路中使用跨內部部署連線，會從 [我的 VM 所有流量都移到該連接？

[否]。 含有目的地流量所指定的虛擬網路本機的網路 IP 位址範圍中所包含的 IP 都會經過虛擬網路閘道器。 流量有 IP 內虛擬網路保持在虛擬網路的目的地。 透過負載平衡器傳送到公用網路，或透過 Azure VPN 閘道傳送使用強制通道，如果其他傳輸。 如果您正在進行疑難排解，請務必確定您已列在您想要傳送至 [閘道器的區域網路中的所有範圍。 確認 [區域網路位址範圍沒有與任何虛擬網路中的地址範圍重疊。 此外，您會想要確認您使用的 DNS 伺服器解析名稱至適當的 IP 位址。


## <a name="virtual-network-faq"></a>虛擬網路常見問題集

您可以檢視其他虛擬網路資訊的[虛擬網路常見問題](../virtual-network/virtual-networks-faq.md)集。
 
