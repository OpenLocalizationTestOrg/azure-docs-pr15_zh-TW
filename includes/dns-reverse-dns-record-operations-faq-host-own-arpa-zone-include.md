<BR> 
## <a name="faq---hosting-your-arpa-zone-in-azure-dns"></a>常見問題集-裝載在 Azure DNS 您 ARPA 區域

### <a name="can-i-host-arpa-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>可以在 Azure DNS 我 ISP 指定的 IP 區塊主控 ARPA 區域？
[是]。 主控自己的 IP 範圍中 Azure DNS ARPA 區域完全受支援。

只要[建立 Azure DNS 的區域](dns-getstarted-create-dnszone.md)，請選取然後使用您的 isp 連絡以[代理人的區域](dns-domain-delegation.md)。  然後您可以管理每個反向對應的 PTR 記錄其他類型的記錄的相同方式。

您也可以[匯入使用 Azure CLI 現有反向對應區域](dns-import-export.md)。

### <a name="how-much-does-hosting-my-arpa-zone-cost"></a>多少裝載我 ARPA 區域成本？
您的 isp 連絡指派 ip 裝載 ARPA 區域中 Azure DNS 區塊薪資[Azure DNS 的標準工資率](https://azure.microsoft.com/pricing/details/dns/)。

### <a name="can-i-host-arpa-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>可以主控 IPv4 和 IPv6 位址 Azure ARPA 區域？
[是]。