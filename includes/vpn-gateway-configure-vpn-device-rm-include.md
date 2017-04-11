
若要設定 VPN 裝置，您需要的虛擬網路閘道器的公用 IP 位址設定您的內部部署 VPN 裝置。 使用您裝置的製造商的特定的設定資訊並設定您的裝置。 請參閱深入瞭解適用於 Azure VPN 裝置[VPN 裝置](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md)。

若要尋找您的虛擬網路閘道使用 PowerShell 的公用 IP 位址，請使用下列範例︰

    Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG

您也可以使用 Azure 入口網站，來檢視您的虛擬網路閘道器的公用 IP 位址。 瀏覽至**虛擬網路閘道器**，然後按一下 [閘道的名稱。