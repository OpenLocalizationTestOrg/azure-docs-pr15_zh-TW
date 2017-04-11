### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>所有 Azure VPN 閘道器 Sku 都支援 BGP 嗎？

否，BGP 支援 Azure**標準**和**HighPerformance** VPN 閘道器。 **基本**不支援 SKU。

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>可以使用 BGP 與 Azure Policy-Based VPN 閘道嗎？

不支援 BGP 上只傳送型 VPN 閘道器。

### <a name="can-i-use-private-asns-autonomous-system-numbers"></a>可以使用私人 ASNs （獨立系統數字） 嗎？

是的您可以使用您自己的 ASNs 公用或私人 ASNs 內部網路和 Azure 虛擬網路。

#### <a name="are-there-asns-reserved-by-azure"></a>有保留 Azure ASNs 嗎？

是的下列 ASNs 所保留 Azure 的內部和外部 peerings:

- 公用 ASNs: 8075 8076、 12076
- 私人 ASNs: 65515 65517、 65518、 65519、 65520

您無法連線至 Azure VPN 閘道器時，針對您上的部署 VPN 裝置的指定這些 ASNs。

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>可以使用相同的 ASN 內部部署 VPN 網路和 Azure VNets 嗎？

否，您必須指派不同的 ASNs 內部網路和您 Azure VNets 之間，如果您將其連線與 BGP。 Azure VPN 閘道器會有預設值的指派，65515 ASN 是否 BGP 啟用不適用於跨內部部署連線。 您可以藉由建立 VPN 閘道器時指派不同的 ASN 覆寫此預設值，或變更 ASN，建立閘道器後。 您必須將您的內部部署 ASNs 指派給對應 Azure 本機網路閘道器。

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Azure VPN 閘道器會通知哪些位址首碼我？

Azure VPN 閘道器會通知您的內部部署 BGP 裝置的下列路由︰

- 您 VNet 地址前置詞
- 連線至 Azure VPN 閘道器的每個區域的網路閘道器的地址前置詞
- 而學到的 Azure VPN 閘道，**除了預設路由或任何 VNet 前置詞與重疊的路由**其他 BGP 對等工作階段。

#### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>我可以通知 Azure VPN 閘道器的預設路由 (0.0.0.0 / 0)？

不行。

#### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>可以通知的確切的前置詞為我的虛擬網路前置詞嗎？

否，通知相同的前置字元，做為任何的虛擬網路位址首碼會被封鎖或做為篩選依據的 Azure 的平台。 不過，您可以通知是必須虛擬網路內的前置字元。 例如，虛擬網路無法使用地址空間 10.10.0.0/16，您可能會通知 10.0.0.0/8。

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>可以使用 BGP 我 VNet-VNet 連線嗎？

是的您可以使用 BGP 跨內部部署連線和 VNet-VNet 連線兩者均適用。

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>可以我混合 BGP 與非 BGP 連線我 Azure VPN 閘道嗎？

是的您可以混合 BGP 和非 BGP 都相同 Azure VPN 閘道器的連線。

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Azure VPN 閘道器支援 BGP 傳輸路由嗎？

是的 BGP 傳輸路由受到支援，**不**Azure VPN 閘道器會通知其他 BGP 對等的預設通往例外狀況。 若要啟用傳輸路由跨多個 Azure VPN 閘道，您必須啟用 BGP 上所有的中繼 VNet-VNet 連線。

### <a name="can-i-have-more-than-one-tunnels-between-azure-vpn-gateway-and-my-on-premises-network"></a>我能有 Azure VPN 閘道器與我的內部部署網路之間的多個通道？

是的您可以建立 Azure VPN 閘道器與內部部署網路之間的多個 S2S VPN 通道。 請注意︰ 所有這些通道將會計算對通道您 Azure VPN 閘道器的總數。 例如，如果您有兩個重複通道 Azure VPN 閘道與您的內部部署網路時，他們會使用 2 通道登出 Azure VPN 閘道 (標準的 10) 到 30 HighPerformance 的總配額。

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>我能有多個與 BGP 的兩個 Azure VNets 通道？

否，對虛擬網路之間的多餘通道不受支援。

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration"></a>我可以使用 BGP S2S VPN 的 ExpressRoute/S2S VPN 共存設定？

不行。

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Azure VPN 閘道器會使用何種地址 BGP 等 ip？

Azure VPN 閘道器會配置定義的虛擬網路的 GatewaySubnet 範圍中的單一 IP 位址。 根據預設，則範圍內的第二個的最後一個地址。 例如，如果您 GatewaySubnet 10.12.255.0/27，範圍從 10.12.255.0 到 10.12.255.31，然後於 Azure VPN 閘道 BGP 等 IP 位址會 10.12.255.30。 當您在清單的 Azure VPN 閘道器的資訊時，您可以找到這項資訊。

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>我要有 vpn 才能裝置上 BGP 等 IP 位址需求是什麼？

您的內部部署 BGP 對等位址**不必須**是相同的 VPN 裝置的公用 IP 位址。 您 BGP 等 IP VPN 裝置上使用不同的 IP 位址。 可以指派給迴路介面在裝置上的地址。 指定此位址中對應本機表示位置的網路閘道器。

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>什麼應該指定為我的地址前置詞本機網路閘道器的使用 BGP？

Azure 本機網路閘道指定為內部網路的初始位址首碼。 使用 BGP，您必須配置主機首碼 （/ 32 加上字首） 的地址的空間的內部部署網路您 BGP 等 IP 位址。 如果您 BGP 等 IP 10.52.255.254，您應該指定 「 10.52.255.254/32 」 為代表此內部網路的本機網路閘道 localNetworkAddressSpace。 這是為了確保 Azure VPN 閘道器建立透過 S2S VPN 通道 BGP 工作階段。

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>什麼應該新增至我的內部部署 VPN 裝置 BGP 對等工作階段？

您應該新增主機路由 Azure BGP 等 IP 位址指向 IPsec S2S VPN 通道 VPN 裝置上。 例如，如果 Azure VPN 等 IP 是 「 10.12.255.30 」，您應該新增 「 10.12.255.30 」 的相符的 IPsec 通道介面個躍點介面主機路由 VPN 裝置上。
