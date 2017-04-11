<BR> 
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>常見問題集-反向 DNS 您 Azure 指定的 IP 位址

### <a name="how-much-do-reverse-dns-records-cost"></a>執行反向 DNS 記錄成本多少？
這些是免費 ！  有任何額外的成本反向 DNS 記錄的查詢。

### <a name="will-the-reverse-dns-records-for-my-azure-assigned-public-ip-address-resolve-from-the-internet"></a>會解決從網際網路我 Azure 指派公用 IP 位址的反向 DNS 記錄嗎？
[是]。 一旦您反向 DNS 屬性設定您的公用 IP 位址，請 Azure 會管理所有的 DNS 委派與確保所有的網際網路使用者可解析反向 DNS 記錄所需的 DNS 區域。

### <a name="will-a-default-reverse-dns-record-be-created-for-my-public-ip-addresses"></a>將我的公用 IP 位址的建立預設反向 DNS 記錄嗎？
[否]。 反向 DNS 是選擇加入集功能。 如果您選擇不進行設定，則會建立沒有預設反向 DNS 記錄。

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>完整網域名稱 (FQDN) 的格式為何？
Fqdn 指定轉寄順序，必須結束以點 (例如，「 app1.contoso.com 」)。

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>如果已指定反向 dns 驗證檢查失敗，會發生什麼情況？
位置會檢查反向 dns 驗證失敗，服務管理操作會失敗。 請修正反向 DNS 值為必要項目，然後再試一次。

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>管理反向 DNS 我 Azure 網站？
Azure 網站不支援反向 DNS。 反向 DNS 支援的 Azure 虛擬機器。

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-public-ip-address"></a>可以設定多個反向 DNS 記錄的 [我的公用 IP 位址嗎？
[否]。 Azure 支援單一反向 DNS 記錄，每個公用 IP 位址。 每個公用 IP 位址但是有自己的反向 DNS 記錄。

### <a name="can-i-configure-reverse-dns-records-for-an-ipv6-public-ip-address"></a>可以設定反向 DNS 記錄 IPv6 公用 IP 位址嗎？
[否]。  此時，反向 DNS 記錄支援的公用 IP 位址 IPv4 只。

### <a name="can-i-configure-a-reverse-dns-record-for-my-public-ip-address-without-having-a-domainnamelabel-specified"></a>設定反向 DNS 記錄的 [我的公用 IP 位址而不需指定 DomainNameLabel？
[否]。 若要針對您的公用 IP 位址運用反向 DNS 記錄，您必須指定 DomainNameLabel 屬性。

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>可以傳送電子郵件給外部網域我計算 Azure 服務？
[否]。 [計算 azure 服務不支援的外部網域傳送電子郵件](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/)。
