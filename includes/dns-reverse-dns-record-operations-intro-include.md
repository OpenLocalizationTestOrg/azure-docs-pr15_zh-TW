## <a name="what-is-reverse-dns"></a>反向 DNS 是什麼？

傳統的 DNS 記錄可讓對應 DNS 名稱 （例如 「 www.contoso.com 」) 的 IP 位址 （例如 64.4.6.100)。  反向 DNS 可讓您的 IP 位址 (64.4.6.100)，回到的名稱 (「 www.contoso.com 」) 的翻譯。

在許多情況下會使用相反的 DNS 記錄。 例如，反向 DNS 記錄是廣泛用於對抗垃圾電子郵件驗證電子郵件的寄件者。  接收的郵件伺服器會擷取反向 DNS 記錄的 [傳送郵件的伺服器 IP 位址，並確認是否要將該主機授權從原始網域傳送電子郵件。 (請注意[計算 Azure 服務不支援的外部網域傳送電子郵件](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/)。)

## <a name="how-reverse-dns-works"></a>如何反向 DNS 運作方式

反向 DNS 記錄所裝載特殊 DNS 區域，稱為 「 ARPA] 區域中。  這些區域的主機服務網域，如 'contoso.com' 標準階層的平行表單個別 DNS 階層。

比方說，DNS 記錄] www.contoso.com' 實作 zone 'contoso.com' 中使用名稱 「 www 」 」 的 「 DNS 記錄。  此 A 記錄指向對應的 IP 位址，在此例 64.4.6.100。  反向對應實作分別使用 「 PTR 」 記錄名為 「 100 的' '6.4.64.in-addr.arpa 」 的區域 （IP 位址相反 ARPA 區域附註）。 此 PTR 記錄，如果它已正確設定指向名稱 「 www.contoso.com 」。

當組織已指派的 IP 地址區塊時，他們也取得的權限管理的相對應的 ARPA 區域。 裝載於對應至 Azure 所使用的 IP 位址區塊 ARPA 區域，而且由 Microsoft 管理。 您的 ISP 可能會裝載您的 IP 位址的 ARPA 區域，或可能會讓您管理您的選擇，例如 Azure DNS 的 DNS 服務中 ARPA 區域。

>[AZURE.NOTE] 在個別的平行 DNS 階層中實作轉寄 DNS 查閱並反向 DNS 查閱。 「 Www.contoso.com' 反向查閱是**不**裝載在 'contoso.com' 的區域，而是存放 ARPA 區域的相對應的 IP 位址區塊中。

如需有關反向 DNS 的詳細資訊，請參閱[反向 DNS 查詢](http://en.wikipedia.org/wiki/Reverse_DNS_lookup)。

## <a name="azure-support-for-reverse-dns"></a>反向 DNS azure 支援

Azure 支援反向 DNS 的相關的兩種不同情況︰

1. 裝載 ARPA 區域對應到您的 IP 位址區塊。
2. 允許您設定指派給 Azure 服務的 IP 位址的反向 DNS 記錄。

若要支援前者，Azure DNS 可以用來裝載您 ARPA 區域及管理每個反向 DNS 查閱的 PTR 記錄。  建立 ARPA 區域、 設定委派，以及設定 PTR 記錄的程序會與一般 DNS 區域相同。  僅限差異是透過您的 isp 連絡，而不是您的 DNS 註冊機構，必須設定委派，並應使用 PTR 記錄類型。

若要支援後者，Azure 可讓您設定反向查閱的 IP 位址配置給您的服務。  依 Azure 設定此反向對應為 PTR 記錄中的相對應的 ARPA 區域。  由 Microsoft 裝載這些 ARPA 區域，對應到所有 Azure 所使用的 IP 範圍。 **這篇文章的其餘部分說明這種情況下的詳細資料。**
