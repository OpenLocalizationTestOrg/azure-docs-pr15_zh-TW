## <a name="about-records"></a>關於記錄

每個 DNS 記錄有名稱與類型。 記錄會組織成不同的類型，根據他們所包含的資料。 「 一 」 記錄，其名稱對應到 IPv4 位址是最常見的類型。 其他類型是 [MX] 記錄，其會對應到郵件伺服器的名稱。

Azure DNS 支援所有常見的 DNS 記錄類型，包括，AAAA，CNAME，MX，NS、 PTR、 SOA、 SRV 和 TXT。 請注意︰
- SOA 記錄集會自動建立的每個區域時，他們無法個別。
- 使用 [TXT 記錄類型，應該會建立 SPF 記錄。 如需詳細資訊，請參閱[此頁面](http://tools.ietf.org/html/rfc7208#section-3.1)。

在 Azure DNS 記錄指定使用相對的名稱。 「 完整的 「 網域名稱 (FQDN) 包含的區域名稱，而不會 「 相對 」 的名稱。 例如，相關的記錄名稱 「 www 」 在 「 contoso.com 」 的區域中提供完整的記錄名稱 www.contoso.com。

## <a name="about-record-sets"></a>關於記錄集

有時候，您需要建立一個以上的 DNS 記錄，指定名稱與類型。 例如，假設 「 www.contoso.com 」 的網站裝載在兩個不同的 IP 位址。 網站需要兩個不同的記錄，為每個 IP 位址。 記錄集的範例如下︰

    www.contoso.com.        3600    IN  A   134.170.185.46
    www.contoso.com.        3600    IN  A   134.170.188.221

Azure DNS 管理 DNS 記錄的使用記錄集。 記錄會區域中的 DNS 記錄有相同的名稱，而且相同類型的集合。 大部分的記錄集包含單一記錄中，但不是很類似中的記錄組包含多筆記錄的範例。

SOA 和 CNAME 記錄集是例外狀況。 DNS 標準不允許使用相同的名稱，針對這些類型的多筆記錄。

即時時間] 或 [TTL，請指定多久每一筆記錄快取的用戶端之前重新查詢。 在此範例中，TTL 是 3600 秒或 1 小時。 [TTL] 指定記錄的設定，不會用於每一筆記錄，因此相同的值會使用該記錄集內的所有記錄的。

#### <a name="wildcard-record-sets"></a>萬用字元記錄集

Azure DNS 支援[萬用字元記錄](https://en.wikipedia.org/wiki/Wildcard_DNS_record)。 （除非有非萬用字元的記錄集深入瞭解相符項目），這些會傳回任何查詢名稱相符。 萬用字元的記錄集支援的 NS 和 SOA 以外的所有記錄類型。  

若要建立萬用字元的記錄設定，使用記錄集名稱 「\*」。 或者，您也可以使用標籤名稱 「\*」，例如，「\*.foo 」。

#### <a name="cname-record-sets"></a>CNAME 記錄集

CNAME 記錄集無法同時存在同名的其他記錄集。 例如，您無法建立相對名稱 「 www 」 設定一筆 CNAME 記錄及 A 記錄相對名稱 「 www 」 一次。 因為區域 apex (名稱 =‘@’)一律包含 NS 和 SOA 記錄時建立區域所建立的設定，則無法建立區域 apex 設定一筆 CNAME 記錄。 這些限制式因 DNS 標準，且不 Azure DNS 的限制。
