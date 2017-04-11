- 虛擬網路可以在相同或不同 Azure 區域 （位置）。

- 在雲端服務或負載平衡端點無法跨虛擬網路，即使他們連接在一起。

- 除非是必要的跨內部部署連線，連接在一起的多個 Azure 虛擬網路不需要任何內部部署 VPN 閘道器。

- VNet-VNet 支援連接的虛擬網路。 它不支援連線的虛擬機器或雲端服務不在虛擬網路。

- VNet-VNet 需要 Azure VPN 閘道器與 RouteBased （先前稱為動態路由） VPN 類型。 

- 虛擬網路連線可以同時使用多網站 vpn，與最大值 10 （預設/標準閘道） 或 30 （高效能閘道） VPN 連線至其他虛擬網路虛擬網路 VPN 閘道器通道或內部部署網站。

- 不能重疊的虛擬網路與內部部署的區域網路網站的位址空間。 重疊的地址空格會建立的 VNet-VNet 連線失敗。

- 不支援的虛擬網路之間的多餘通道。

- 所有的 VPN 通道的虛擬網路共用 Azure VPN 閘道器和相同的 VPN 閘道器上線時間 SLA Azure 中的可用頻寬。

- VNet-VNet 流量會透過 Microsoft 網路，而非網際網路。

- 在相同的區域內的 VNet-VNet 流量是免費的兩個方向。交叉地區 VNet-VNet 出口流量會負責根據來源區域輸出間 VNet 資料傳輸速度。 請參閱[價格] 頁面](https://azure.microsoft.com/pricing/details/vpn-gateway/)的詳細資料。