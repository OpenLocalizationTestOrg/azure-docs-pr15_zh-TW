- **PolicyBased:**PolicyBased Vpn 先前已在傳統的部署模型中稱為靜態路由的閘道器。 原則 Vpn 加密，並直接封包透過 IPsec 通道根據 IPsec 原則設定您的內部網路和 Azure VNet 之間的地址前置字元的組合。 原則 （或流量選取器） 定義通常是 VPN 裝置設定的存取清單。 PolicyBased VPN 類型的值就是*PolicyBased*。 使用時 PolicyBased VPN，請牢記以下的限制︰

    - PolicyBased Vpn 可以**只**使用基本的閘道器 SKU 上。 此 VPN 類型不相容 Sku 其他閘道器。
    - 使用 PolicyBased VPN 時，您可以讓僅 1 通道。
    - 您只可以使用 PolicyBased Vpn S2S 連線]，並且只針對特定設定。 大部分的 VPN 閘道器組態需要 RouteBased VPN。

- **RouteBased**: RouteBased Vpn 先前所呼叫動態路由閘道器在傳統的部署模型中。 RouteBased Vpn IP 轉寄或路由至直接封包的資料表，將其對應的通道介面中使用 「 路由 」。 通道介面然後加密，或與通道登出封包解密。 原則 （或流量選取器） 的 RouteBased Vpn 設定為任何-任何 （或萬用字元）。 RouteBased VPN 類型的值就是*RouteBased*。