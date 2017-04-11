<BR> 
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>常見問題集-反向 DNS 您 Azure 指定的 IP 位址

### <a name="how-much-do-reverse-dns-records-cost"></a>執行反向 DNS 記錄成本多少？
這些是免費 ！  有任何額外的成本反向 DNS 記錄的查詢。

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>會解決從網際網路我反向 DNS 記錄嗎？
[是]。 一旦您反向 DNS 屬性設定您的雲端服務，請 Azure 管理所有的 DNS 委派與確保所有的網際網路使用者可解析反向 DNS 記錄所需的 DNS 區域。

### <a name="will-a-default-reverse-dns-record-be-created-for-my-cloud-services"></a>將我的雲端服務的建立預設反向 DNS 記錄嗎？
[否]。 反向 DNS 是選擇加入集功能。 如果您選擇不進行設定，則會建立沒有預設反向 DNS 記錄。

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>完整網域名稱 (FQDN) 的格式為何？
Fqdn 指定轉寄順序，必須結束以點 (例如，「 app1.contoso.com 」)。

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>如果已指定反向 dns 驗證檢查失敗，會發生什麼情況？
位置會檢查反向 dns 驗證失敗，服務管理操作會失敗。 請修正反向 DNS 值為必要項目，然後再試一次。

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>管理反向 DNS 我 Azure 網站？
Azure 網站不支援反向 DNS。 反向 DNS 支援 Azure PaaS 角色和 IaaS 虛擬機器。

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-cloud-service"></a>可以設定多個反向 DNS 記錄的 [我的雲端服務嗎？
[否]。 Azure 支援單一反向 DNS 記錄，為每個 Azure 雲端服務。 每個 Azure 雲端服務但是有自己的反向 DNS 記錄。

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>可以傳送電子郵件給外部網域我計算 Azure 服務？
[否]。 [計算 azure 服務不支援的外部網域傳送電子郵件](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/)。
