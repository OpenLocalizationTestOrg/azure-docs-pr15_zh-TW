<properties
   pageTitle="建立使用 CLI DNS 區域 |Microsoft Azure"
   description="瞭解如何建立 Azure DNS 逐步開始裝載您的 DNS 網域使用 CLI DNS 區域"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="create-an-azure-dns-zone-using-cli"></a>建立使用 CLI Azure DNS 區域


> [AZURE.SELECTOR]
- [Azure 入口網站](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)


本文會引導您執行的步驟，若要使用 CLI 建立 DNS 區域。 您也可以建立使用 PowerShell 或 Azure 入口網站的 DNS 區域。

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


## <a name="before-you-begin"></a>開始之前

這些指示使用 Microsoft Azure CLI。 請務必一併更新至最新的 Azure CLI (0.9.8 或更新版本) 若要使用的 Azure DNS 命令。 輸入`azure -v`檢查 Azure CLI 版本目前已安裝在您的電腦。

## <a name="step-1---set-up-azure-cli"></a>步驟 1-設定 Azure CLI

### <a name="1-install-azure-cli"></a>1.安裝 Azure CLI

您可以安裝的 Windows Azure CLI、 Linux 或 MAC 完成後，您可以管理 Azure DNS 使用 Azure CLI 才需要下列步驟。 在[安裝 Azure CLI](../xplat-cli-install.md)的詳細資訊。 [DNS] 命令需要 Azure CLI 0.9.8 版本或更新版本。

所有的網路提供者命令 CLI 可以找到使用下列命令︰

    azure network

### <a name="2-switch-cli-mode"></a>2.切換 CLI 模式

Azure DNS 使用 Azure 資源管理員。 請確定您切換 CLI 模式]，使用 ARM 命令。

    azure config mode arm

### <a name="3-sign-in-to-your-azure-account"></a>3.登入您 Azure 帳戶

系統會提示您的認證驗證方法。 請記住，您可以只使用 ORGID 帳戶。

    azure login -u "username"

### <a name="4-select-the-subscription"></a>4。 選取訂閱

選擇您要使用的 Azure 訂閱。

    azure account set "subscription name"

### <a name="5-create-a-resource-group"></a>5。 建立資源群組

Azure 資源管理員需要所有資源群組都指定的位置。 這用於做為預設位置的 [資源] 群組中的資源。 不過，因為所有的 DNS 資源是全域管理員、 不地區，資源群組位置的選擇會造成任何影響 Azure dns。

如果您使用現有的資源群組，您可以略過此步驟。

    azure group create -n myresourcegroup --location "West US"


### <a name="6-register"></a>6。 register

Azure DNS 服務是由 Microsoft.Network 資源提供者管理。 Azure 訂閱，必須使用此資源提供者，才能使用 Azure DNS 註冊。 這是針對每個訂閱的一次性作業。

    azure provider register --namespace Microsoft.Network


## <a name="step-2---create-a-dns-zone"></a>步驟 2-建立 DNS 區域

使用建立 DNS 區域`azure network dns zone create`] 命令。 您可以選擇性地建立 DNS 區域，以及標記。 標記的清單名稱 / 值組與使用由 Azure 資源管理員標籤資源的帳單或群組的用途。 如需標記的詳細資訊，請參閱[使用標籤以組織 Azure 資源](../resource-group-using-tags.md)。

Azure dns 區域名稱應指定而不需要終止**'。 「**。 例如，為 '**contoso.com**' 而非 「**contoso.com。**」。


### <a name="to-create-a-dns-zone"></a>若要建立 DNS 區域

下列範例會建立 DNS 區域中的資源群組，稱為*MyResourceGroup*名為*contoso.com* 。

使用範例，建立您的 DNS 區域，以取代您自己的值。

    azure network dns zone create myresourcegroup contoso.com

### <a name="to-create-a-dns-zone-and-tags"></a>若要建立 DNS 區域和標記。

Azure DNS CLI 支援使用選擇性所指定的 DNS 區域的標籤*-標籤*參數。 下列範例會示範如何建立兩個標籤的 [DNS 區域、 專案 = 示範和信封 = 測試。

您可以使用下面的範例中，建立 DNS 區域及標籤]，以取代您自己的值。

    azure network dns zone create myresourcegroup contoso.com -t "project=demo";"env=test"

## <a name="view-records"></a>檢視記錄

建立 DNS 區域時，也會建立下列 DNS 記錄︰

- 「 開始的授權 」 (SOA) 記錄。 這是出現在每個 DNS 區域的根目錄。

- 授權的名稱伺服器 (NS) 記錄。 這些顯示的名稱伺服器所裝載的區域。 Azure DNS 使用集區的名稱伺服器，而且 Azure DNS 中的不同區域，可以指定不同的名稱伺服器。 如需詳細資訊，請參閱[代理人 Azure dns 網域](dns-domain-delegation.md)。

若要檢視這些記錄，請使用`azure network dns-record-set show`。<BR>
*使用方法︰ 網路 dns 記錄設定顯示 < 資源群組 >< dns zone-名稱 > <name><type>*


在下面的範例中，如果您執行命令與 [資源群組*myresourcegroup*，記錄集名稱*"@"*（適用於根記錄），並輸入*SOA*，它會產生下列輸出︰


    azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/SOA
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    SOA record:
    data:      Email                         : msnhst.microsoft.com
    data:      Expire time                   : 604800
    data:      Host                          : edge1.azuredns-cloud.net
    data:      Minimum TTL                   : 300
    data:      Refresh time                  : 900
    data:      Retry time                    : 300
    data:                                    :
<BR>
若要檢視建立與區域的 NS 記錄，請使用下列命令︰

    azure network dns record-set show myresourcegroup "contoso.com" "@" NS
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/NS
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    NS records
    data:        Name server domain name     : ns1-05.azure-dns.com
    data:        Name server domain name     : ns2-05.azure-dns.net
    data:        Name server domain name     : ns3-05.azure-dns.org
    data:        Name server domain name     : ns4-05.azure-dns.info
    data:
    info:    network dns-record-set show command OK

>[AZURE.NOTE] 記錄組的根 （或*apex*） DNS 區域使用**@**記錄設定名稱。

## <a name="test"></a>測試

您可以使用 nslookup，挖掘，例如 DNS 工具測試您的 DNS 區域或`Resolve-DnsName`PowerShell 指令程式。

如果您還沒有委派您的網域以使用新的 Azure DNS 區域，必須先將 DNS 查詢直接到其中一個名稱伺服器，您的區域。 您的區域的名稱伺服器列在的 NS 記錄中,，以 「 azure 網路 dns 記錄設定顯示 「 如上所示。 請確定替代區域下列命令中正確的值。

下列範例會使用挖掘查詢使用 DNS 區域中指定的名稱伺服器網域 contoso.com。 查詢是指向我們所使用的名稱伺服器*@*和使用挖掘區域名稱。

     <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
    (1 server found)
    global options: +cmd
    Got answer:
    ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
    flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
    WARNING: recursion requested but not available

    OPT PSEUDOSECTION:
    EDNS: version: 0, flags:; udp: 4000
    QUESTION SECTION:
    contoso.com.                        IN      A

    AUTHORITY SECTION:
    contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net.
    msnhst.microsoft.com. 6 900 300 604800 300

    Query time: 93 msec
    SERVER: 208.76.47.5#53(208.76.47.5)
    WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
    MSG SIZE  rcvd: 120

## <a name="next-steps"></a>後續步驟

建立 DNS 區域之後, 建立[記錄集和記錄](dns-getstarted-create-recordset-cli.md)啟動解決您的網際網路網域的名稱。
