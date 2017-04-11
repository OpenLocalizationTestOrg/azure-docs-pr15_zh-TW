<properties
   pageTitle="委派您的網域 Azure dns |Microsoft Azure"
   description="瞭解如何變更網域委派，以及使用 Azure DNS 名稱伺服器提供網域的主機服務。"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/30/2016"
   ms.author="sewhee"/>


# <a name="delegate-a-domain-to-azure-dns"></a>委派 Azure dns 網域

Azure DNS 可讓您管理 DNS 區域及管理 Azure 中的網域的 DNS 記錄。 達到 Azure DNS 網域的 DNS 查詢的順序，網域有 Azure dns 委派從上層網域。 請記住 Azure DNS 不網域註冊機構。 本文說明網域委派的運作方式，以及如何代理人 Azure dns 的網域。




## <a name="how-dns-delegation-works"></a>DNS 委派的運作方式

### <a name="domains-and-zones"></a>網域和區域

「 網域名稱系統是網域的階層。 階層啟動 '根 「 網域，名稱會直接從**.**。  下面這是頂層網域，例如 「 com 」、 「 淨利 」、 「 的組織' 'uk （英國）' 或 'jp 」。  以下這些是第二層網域，例如 「 org.uk' 或 'co.jp'。  等等。 使用不同的 DNS 區域主控 DNS 階層中的網域。 全域分散這些區域，裝載的世界各地的 DNS 名稱伺服器。

**DNS 區域**

網域是在網域名稱系統，例如 「 contoso.com 」 中的唯一名稱。 DNS 區域用來主控特定網域的 DNS 記錄。 例如，網域 'contoso.com' 可能包含數字的 DNS 記錄，例如 「 mail.contoso.com 」 （適用於郵件伺服器） 和 「 www.contoso.com 」 （適用於網站）。

**網域註冊機構**

網域註冊機構是公司人員可提供網際網路網域名稱。 他們會驗證是否您想要使用的網際網路網域有，則可讓您購買。 註冊的網域名稱之後，您才法律網域名稱的擁有者。 如果您已經有網際網路網域，您會使用目前的網域註冊機構委派給 Azure DNS。

>[AZURE.NOTE] 若要瞭解詳細資訊，在 [擁有者指定的網域名稱，或如何購買網域的詳細資訊，請參閱[Azure AD 網際網路網域管理](https://msdn.microsoft.com/library/azure/hh969248.aspx)。

### <a name="resolution-and-delegation"></a>解析度和委派

有兩種類型的 DNS 伺服器︰

- _授權_的 DNS 伺服器主控 DNS 區域。 它可以解答 DNS 查詢只這些區域中的記錄。
- _遞迴_DNS 伺服器不主控 DNS 區域。 它，則可電話收集資料需要授權的 DNS 伺服器解答 DNS 的所有查詢。

>[AZURE.NOTE] Azure DNS 提供授權的 DNS 服務。  不提供遞迴 DNS 服務。

> 雲端服務與 Vm Azure 中的會自動設定 Azure 的基礎結構的一部分，分別使用所提供的遞迴 DNS 服務。  如需變更這些 DNS 設定的資訊，請參閱[Azure 中的名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。

在電腦或行動裝置的 DNS 用戶端通常呼叫遞迴 DNS 伺服器執行 DNS 查詢用戶端應用程式需要。

當遞迴 DNS 伺服器收到的 DNS 記錄，例如 「 www.contoso.com 」 查詢時，就必須先尋找裝載的區域 'contoso.com' 網域的名稱伺服器。 若要這麼做，它會開始在根名稱伺服器，並從該處找到裝載 'com' 區域的名稱伺服器。 然後查詢 'com 」 的名稱伺服器，若要尋找裝載 'contoso.com' 區域的名稱伺服器。  最後，則可以查詢 'www.contoso.com' 這些名稱伺服器。

這稱為 DNS 名稱解析。 嚴格來說，DNS 解析包含下列 CNAMEs，例如額外的步驟，但不一定要了解 DNS 委派的運作方式。

如何會上層區域 '指向' 對子區域的名稱伺服器？ 它會使用特殊類型的稱為 NS 記錄 （NS 單獨的 [名稱伺服器]） 的 DNS 記錄。 例如，根區域包含 「 com 」 的 NS 記錄，並顯示 「 com' 區域的名稱伺服器。 接著，「 com' 區域包含 'contoso.com'，其中顯示 'contoso.com' 區域的名稱伺服器的 NS 記錄。 設定對子區域上層區域中的 NS 記錄，稱為委派網域。


![Dns 名稱伺服器](./media/dns-domain-delegation/image1.png)

每個委派實際上有兩份的 NS 記錄。一個指向子，和在本身的子區域中的另一個上層區域中。 'Contoso.com' 區域包含 'contoso.com' （除了 「 com 」 中的 NS 記錄） 的 NS 記錄。 這些稱為代表性 NS 記錄，其坐在子區域的 apex。


## <a name="delegating-a-domain-to-azure-dns"></a>委派 Azure dns 網域

一旦您在 Azure DNS 中建立您的 DNS 區域，您需要設定中的上層區域的 NS 記錄，讓 Azure DNS 名稱解析，您的區域的授權來源。 註冊機構購買網域，您的註冊機構會提供這些 NS 記錄設定的選項。

>[AZURE.NOTE] 您沒有在 Azure DNS 中該網域名稱與建立 DNS 區域擁有網域。 不過，您需要擁有該網域註冊機構委派 Azure dns 設定。

例如，假設您購買的網域 'contoso.com' Azure DNS 中建立與名稱 'contoso.com' 區域。 該網域的擁有者，您的註冊機構，讓您將設定名稱伺服器位址 （也就是 NS 記錄） 選項為您的網域。 註冊機構會儲存這些 NS 記錄，在上層網域，在此例 '.com 」。 嘗試解決 'contoso.com' 中的 DNS 記錄時，用戶端世界各地的然後會導向至您的網域 Azure DNS 區域中。

### <a name="finding-the-name-server-names"></a>尋找名稱伺服器名稱

您可以委派您的 DNS 區域 Azure dns 之前，您必須知道您的區域的名稱伺服器名稱。 Azure DNS 配置每次建立區域的資料庫中的名稱伺服器。

若要查看指派給您的區域的名稱伺服器的最簡單方法是透過 Azure 入口網站。  在此範例中，「 contoso.net 」 的區域已指派名稱伺服器 'ns1-01.azure-dns.com 」，ns2 01.azure dns.net，' ns3-01.azure-dns.org 」，並' ns4-01.azure-dns.info 」:

 ![Dns 名稱伺服器](./media/dns-domain-delegation/viewzonens500.png)

Azure DNS 會自動建立授權的 NS 記錄，將包含指定的名稱伺服器的區域中。  若要查看透過 PowerShell 的 Azure 或 Azure CLI 的名稱伺服器名稱，您只需要擷取這些記錄。

使用 PowerShell 的 Azure，授權的 NS 記錄可以擷取，如下所示。 請注意，記錄名稱“@”用來記錄的區域 apex 參照。

    PS> $zone = Get-AzureRmDnsZone –Name contoso.net –ResourceGroupName MyResourceGroup
    PS> Get-AzureRmDnsRecordSet –Name “@” –RecordType NS –Zone $zone

    Name              : @
    ZoneName          : contoso.net
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
    RecordType        : NS
    Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                        ns4-01.azure-dns.info}
    Tags              : {}

您也可以使用跨平台 Azure CLI 来擷取的授權的 NS 記錄，因此探索指派給您的區域的名稱伺服器︰

    C:\> azure network dns record-set show MyResourceGroup contoso.net @ NS
    info:    Executing command network dns record-set show
        + Looking up the DNS Record Set "@" of type "NS"
    data:    Id                              : /subscriptions/.../resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.net/NS/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/NS
    data:    Location                        : global
    data:    TTL                             : 172800
    data:    NS records
    data:        Name server domain name     : ns1-01.azure-dns.com.
    data:        Name server domain name     : ns2-01.azure-dns.net.
    data:        Name server domain name     : ns3-01.azure-dns.org.
    data:        Name server domain name     : ns4-01.azure-dns.info.
    data:
    info:    network dns record-set show command OK

### <a name="to-set-up-delegation"></a>若要設定委派

每個註冊機構有自己的 DNS 管理工具，若要變更的網域名稱伺服器記錄。 在註冊機構的 DNS 管理頁面上，編輯 NS 記錄，並取代的 NS 記錄 Azure DNS 建立的項目。

當委派 Azure dns 網域，您必須使用 Azure DNS 所提供的名稱伺服器名稱。  您應該一律使用所有 4 名稱伺服器的名稱，無論您的網域名稱。  網域委派時，不需要使用相同的頂層網域為您的網域的名稱伺服器名稱。

不應使用 「 黏附記錄 」 指向 Azure DNS 名稱伺服器 IP 位址，因為這些 IP 位址可能會在未來的變更。 Azure DNS 目前不支援委派您自己的區域，有時也稱為 「 vanity 名稱伺服器，」 中使用名稱伺服器名稱。

### <a name="to-verify-name-resolution-is-working"></a>若要確認名稱解析正常運作

完成後委派，您可以驗證名稱解析正在使用的工具，例如 「 nslookup 」 來查詢您的區域 （這也會自動建立區域建立時） SOA 記錄。

請注意，您就沒有指定 Azure DNS 名稱伺服器，因為是標準的 DNS 解析程序會自動尋找名稱伺服器，如果委派設定正確。

    nslookup –type=SOA contoso.com

    Server: ns1-04.azure-dns.com
    Address: 208.76.47.4

    contoso.com
    primary name server = ns1-04.azure-dns.com
    responsible mail addr = msnhst.microsoft.com
    serial = 1
    refresh = 900 (15 mins)
    retry = 300 (5 mins)
    expire = 604800 (7 days)
    default TTL = 300 (5 mins)

## <a name="delegating-sub-domains-in-azure-dns"></a>委派中 Azure DNS 的子網域

如果您想要設定不同的子區域，您可以委派 Azure DNS 中的子網域。 例如，無法設定和委派 'contoso.com' Azure dns 假設您想要設定不同的子區域時，「 partners.contoso.com 」。

設定子網域為標準的委派遵循類似的程序。 唯一的差別的步驟 3 的 NS 記錄必須建立父區 'contoso.com' Azure DNS，而不是透過網域註冊機構所設定的。


1. 建立子區域 'partners.contoso.com' Azure dns。
2. 查詢授權的 NS 記錄，以取得裝載 Azure DNS 的子區域的名稱伺服器子區域中。
3. 委派子區域設定 NS 記錄，指向 [子區域的上層區域中。


### <a name="to-delegate-a-sub-domain"></a>若要委派的子網域

下列 PowerShell 範例會示範如何運作。 透過 Azure 入口網站，或是透過跨平台 Azure CLI，可以執行相同的步驟。

#### <a name="step-1-create-the-parent-and-child-zones"></a>步驟 1。 建立父項與子項區域

首先，我們建立父項與子項區域。 這些可以在相同的資源群組或不同的資源群組。

    $parent = New-AzureRmDnsZone -Name contoso.com -ResourceGroupName RG1
    $child = New-AzureRmDnsZone -Name partners.contoso.com -ResourceGroupName RG1

#### <a name="step-2-retrieve-ns-records"></a>步驟 2。 擷取 NS 記錄

接下來，我們會從子區域中的下一個範例所示擷取授權的 NS 記錄。  此頁面包含分派給子區域的名稱伺服器。

    $child_ns_recordset = Get-AzureRmDnsRecordSet -Zone $child -Name "@" -RecordType NS

#### <a name="step-3-delegate-the-child-zone"></a>步驟 3。 代理人子區域

建立對應中的上層區域設定完成委派的 NS 記錄。 請注意父系區域] 中的記錄集名稱符合子區域名稱，在本例中 「 合作夥伴 」。

    $parent_ns_recordset = New-AzureRmDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
    $parent_ns_recordset.Records = $child_ns_recordset.Records
    Set-AzureRmDnsRecordSet -RecordSet $parent_ns_recordset

### <a name="to-verify-name-resolution-is-working"></a>若要確認名稱解析正常運作

您可以驗證的所有項目設定正確無誤查看 SOA 記錄的子區域。

    nslookup –type=SOA partners.contoso.com

    Server: ns1-08.azure-dns.com
    Address: 208.76.47.8

    partners.contoso.com
        primary name server = ns1-08.azure-dns.com
        responsible mail addr = msnhst.microsoft.com
        serial = 1
        refresh = 900 (15 mins)
        retry = 300 (5 mins)
        expire = 604800 (7 days)
        default TTL = 300 (5 mins)

## <a name="next-steps"></a>後續步驟

[管理 DNS 區域](dns-operations-dnszones.md)

[管理 DNS 記錄](dns-operations-recordsets.md)

