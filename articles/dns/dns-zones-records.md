<properties 
   pageTitle="DNS 區域和記錄 |Microsoft Azure" 
   description="支援主控 DNS 區域與 Microsoft Azure DNS 記錄的概觀。" 
   services="dns" 
   documentationCenter="na" 
   authors="jtuliani" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="10/17/2016"
   ms.author="jtuliani"/>

# <a name="dns-zones-and-records"></a>DNS 區域和記錄

本頁面說明網域、 DNS 區域和 DNS 記錄和記錄集，和在 Azure DNS 的支援方式的重要概念。

## <a name="domain-names"></a>網域名稱
 
「 網域名稱系統是網域的階層。 階層啟動 '根 「 網域，名稱會直接從**.**。  下面這是頂層網域，例如 「 com 」、 「 淨利 」、 「 的組織' 'uk （英國）' 或 'jp 」。  以下這些是第二層網域，例如 「 org.uk' 或 'co.jp'。 全域分散 DNS 階層中的網域，主控 DNS 世界各地的名稱伺服器。

網域名稱註冊機構是組織，可讓您購買網域名稱，例如 'contoso.com'。  購買網域名稱，讓您控制 DNS 階層的名稱] 底下，例如可讓您直接您公司的網站名稱 「 www.contoso.com 」 的權限。 註冊機構可能會裝載在其本身的名稱伺服器代表您的網域，或另外，您可以指定其他名稱伺服器。

Azure DNS 提供全域分散式、 可用性高的名稱伺服器基礎結構，您可以使用裝載您的網域。 主控您的網域中 Azure DNS，您可以為其他 Azure 服務管理您的 DNS 記錄的相同的認證、 Api、 工具、 帳單及支援。

Azure DNS 目前不支援購買的網域名稱。 如果您想要購買的網域名稱，您需要使用協力廠商網域名稱註冊機構。 註冊機構通常會收費的小型年度的費用。 網域可以然後會裝載於 Azure DNS 管理的 DNS 記錄。 如需詳細資訊，請參閱[代理人 Azure dns 網域](dns-domain-delegation.md)。

## <a name="dns-zones"></a>DNS 區域 

DNS 區域用來主控特定網域的 DNS 記錄。 若要開始裝載您的網域中 Azure DNS，您需要建立該網域名稱的 DNS 區域。 每個您網域的 DNS 記錄，然後會建立這個 DNS 區域內。 

例如，網域 'contoso.com' 可能包含數個 DNS 記錄，例如 「 mail.contoso.com 」 （適用於郵件伺服器） 和 「 www.contoso.com 」 （適用於網站）。

在建立 DNS 區域中 Azure DNS，區域的名稱中必須是唯一的資源群組。 在不同的資源群組或不同的 Azure 訂閱可重複使用相同的區域名稱。 多個區域共用相同的名稱，請在每個執行個體會被指派不同的名稱伺服器位址。 您可以設定只有一組地址與網域名稱註冊機構。

>[AZURE.NOTE] 您沒有擁有網域名稱，以在 Azure DNS 中該網域名稱建立 DNS 區域。 不過，您需要擁有該網域，將 Azure DNS 名稱伺服器設定為使用網域名稱註冊機構的網域名稱的是正確的名稱伺服器。

如需詳細資訊，請參閱[代理人 Azure dns 網域](dns-domain-delegation.md)。

## <a name="dns-records"></a>DNS 記錄

### <a name="record-types"></a>記錄類型

每個 DNS 記錄有名稱與類型。 記錄會組織成不同的類型，根據他們所包含的資料。 最常見的類型是 '' 記錄，其名稱對應到 IPv4 位址。 另一種常見是 'MX' 記錄中，名稱對應到郵件伺服器。

Azure DNS 支援所有常見的 DNS 記錄類型︰ A、 AAAA、 CNAME、 MX，NS、 PTR、 SOA、 SRV，以及 TXT。

### <a name="record-names"></a>記錄名稱

在 Azure DNS 記錄指定使用相對的名稱。 *完整*網域名稱 (FQDN) 包含的區域名稱，而不*相關*的名稱。 例如相關的記錄名稱 「 www 」 中的區域 'contoso.com' 可提供完整的記錄名稱 「 www.contoso.com 」。

*Apex*記錄為 DNS 區域根 （或*apex*） 的 DNS 記錄。 例如，在 DNS 區域 'contoso.com'，apex 記錄也有完整的名稱 （有時稱為*裸*網域） ' contoso.com'。  根據慣例，相關名稱'@'用來建立 apex 記錄。

### <a name="record-sets"></a>記錄集
有時候，您需要建立一個以上的 DNS 記錄，指定名稱與類型。 例如，假設 「 www.contoso.com 」 的網站裝載在兩個不同的 IP 位址。 網站需要兩個不同的記錄，為每個 IP 位址。 記錄集的範例如下︰

    www.contoso.com.        3600    IN  A   134.170.185.46
    www.contoso.com.        3600    IN  A   134.170.188.221

Azure DNS 管理使用*記錄設定*DNS 記錄。 記錄設定 （也稱為*資源*記錄設定） 是在區域中的 DNS 記錄有相同的名稱，而且相同類型的集合。 大部分的記錄集包含單一記錄中，但不是很類似中的記錄組包含多筆記錄的範例。

例如，假設您已經建立 A 記錄 「 www 」 在 「 contoso.com 」 的區域中，指向 [IP 位址 '134.170.185.46 」 （第一筆記錄上方）。  要建立第二個記錄您想要將該記錄新增至現有的記錄集，而不是建立新的記錄設定。

SOA 和 CNAME 記錄類型是例外狀況。 DNS 標準不允許使用相同的名稱，針對這些類型的多筆記錄，因此筆記錄集只能包含單一記錄。

### <a name="time-to-live"></a>存留時間

即時時間] 或 [TTL，請指定多久每一筆記錄快取的用戶端之前重新查詢。 在上述範例中，TTL 是 3600 秒或 1 小時。

在 Azure DNS TTL 指定記錄集，不適用於每一筆記錄，因此相同的值會使用該記錄集內的所有記錄。  您可以指定介於 1 到 2147483647 秒的任何 TTL] 值。

### <a name="wildcard-records"></a>萬用字元記錄

Azure DNS 支援[萬用字元記錄](https://en.wikipedia.org/wiki/Wildcard_DNS_record)。 萬用字元記錄會傳回回應任何查詢名稱相符 （除非沒有深入瞭解相符項目從非萬用字元記錄）。 萬用字元記錄集支援的 NS 和 SOA 以外的所有記錄類型。  

若要建立萬用字元的記錄集，請使用 [記錄集名稱 「\*」。 或者，您也可以使用的名稱與 '\*'為其最左邊的標籤，例如，「\*.foo 」。

### <a name="cname-records"></a>CNAME 記錄

CNAME 記錄集無法與其他具有相同名稱的記錄集同時存在。 例如，您無法建立相對名稱 「 www 」 設定一筆 CNAME 記錄及 A 記錄相對名稱 「 www 」 同時。

因為區域 apex (名稱 =‘@’)一律包含 NS 和 SOA 記錄時建立區域所建立的設定，則無法建立區域 apex 設定一筆 CNAME 記錄。

這些限制式因 DNS 標準，並不是 Azure DNS 的限制。

### <a name="ns-records"></a>NS 記錄

NS 記錄設定就會自動在每個區域的 apex 建立 (名稱 =‘@’),並刪除區域時自動刪除 （其無法刪除個別）。  您可以修改 [TTL] 的 [設定這個記錄，但您無法修改記錄時，也就是預先設定來參照指派至] 區域的 Azure DNS 名稱伺服器。

您可以建立及刪除內的區域，不是在區域 apex 其他 NS 記錄。  這個選項可讓您設定子區域 （請參閱[Azure dns 委派的子網域](dns-domain-delegation.md#delegating-sub-domains-in-azure-dns)）。

### <a name="soa-records"></a>SOA 記錄

在每個區域的 apex，會自動建立 SOA 記錄集 (名稱 =‘@’),並刪除區域時自動刪除。  無法建立或刪除個別 SOA 記錄。 

您可以修改除了 「 主機 」 內容，其為預先設定，請參閱 Azure DNS 所提供的主要名稱伺服器名稱的 SOA 記錄的所有內容。

### <a name="spf-records"></a>SPF 記錄

寄件者原則架構 (SPF) 記錄用來指定允許的電子郵件伺服器傳送電子郵件，代表指定的網域名稱。  正確的 SPF 記錄設定很重要防止標示為 「 垃圾郵件 」 電子郵件的收件者。

DNS Rfc 原本推出新的 「 SPF 」 記錄類型支援這種情況。 若要支援較舊的名稱伺服器，他們也會允許 TXT 記錄類型，若要指定 SPF 記錄的使用。  這會導致混淆， [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1)已解決問題。  此說明 SPF 記錄應只會使用建立 TXT 記錄類型，並已遭取代 SPF 記錄類型。 

**應該使用 TXT 記錄類型建立和 Azure dns 支援 SPF 記錄。** 不支援過時的 SPF 記錄類型。 當[匯入 DNS 區域檔案](dns-import-export.md)，任何使用 SPF 記錄類型的 SPF 記錄會轉換成 TXT 記錄類型。 

### <a name="srv-records"></a>SRV 記錄

各種服務使用[SRV 記錄](https://en.wikipedia.org/wiki/SRV_record)指定伺服器位置。 當指定 Azure DNS SRV 記錄︰

- *服務*與*通訊協定*必須指定為記錄設定名稱，加上底線的一部分。  例如，「\_sip。\_tcp.name 」。  在 [zone apex 記錄，就不需要指定'@'記錄名稱中使用的服務與通訊協定，例如 「\_sip。\_tcp 」。
- *優先順序*、*寬度*、*連接埠*和*目標*指定為記錄設定中的每一筆記錄的參數。 

## <a name="tags-and-metadata"></a>標記和中繼資料

### <a name="tags"></a>標記
標籤都名稱 / 值組的清單，並且使用由 Azure 資源管理員標籤資源。  Azure 資源管理員使用標記來啟用 Azure 帳單，篩選的檢視，也可讓您設定標籤所需的原則。 如需標記的詳細資訊，請參閱[使用標籤以組織 Azure 資源](../resource-group-using-tags.md)。

Azure DNS 支援 DNS zone 資源使用 Azure 資源管理員標籤。  不支援標籤 DNS 記錄集。

### <a name="metadata"></a>中繼資料

記錄設定標籤另一種，Azure DNS 支援註解使用 「 中繼資料 」 的記錄集。  類似的標籤，中繼資料可讓您建立的每個記錄設定關聯名稱 / 值組。  這可能是很有用，例如至記錄的每個記錄設定用途。  與標記不同中繼資料不能用來提供 Azure 帳單篩選的檢視，無法指定 Azure 資源管理員原則。

## <a name="limits"></a>限制

使用 Azure DNS 適用於下列的預設限制︰

[AZURE.INCLUDE [dns-limits](../../includes/dns-limits.md)] 

## <a name="next-steps"></a>後續步驟

- 若要開始使用 Azure DNS，瞭解如何[建立 DNS 區域](dns-getstarted-create-dnszone-portal.md)，並[建立 DNS 記錄](dns-getstarted-create-recordset-portal.md)。
- 若要移轉現有的 DNS 區域，瞭解如何[匯入和 DNS 區域檔案](dns-import-export.md)。
