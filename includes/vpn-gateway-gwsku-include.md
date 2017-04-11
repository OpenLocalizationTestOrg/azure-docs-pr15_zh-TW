當您建立一個虛擬網路閘道時，必須先指定您想要使用的 SKU 的閘道器。 當您選取較高的閘道器 SKU 時，更多的 Cpu 和網路頻寬配置給閘道器，，如此一來，閘道器可支援較高的網路生產力至虛擬網路。

VPN 閘道器可以使用下列 Sku:

- 基本
- 標準
- HighPerformance

選取時的 SKU，請考慮下列動作︰

- 如果您想要使用 PolicyBased VPN 類型，您必須使用基本的 SKU。 在任何其他的 SKU，系統不支援 PolicyBased Vpn （先前稱為靜態路由）。
- 基本 SKU 不支援 BGP。
- ExpressRoute VPN 閘道共存基本 SKU 上不支援的設定。
- 可以在 HighPerformance SKU 只設定主動-主動 S2S VPN 閘道器的連線。
