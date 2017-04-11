<properties
   pageTitle="管理 DNS 記錄設定和使用 Azure CLI Azure DNS 記錄 |Microsoft Azure"
   description="裝載您的網域，在 Azure 的 DNS 時，管理 DNS 記錄集和 Azure DNS 記錄。 所有 CLI 命令，記錄集和記錄上進行的作業。"
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
   ms.date="09/22/2016"
   ms.author="jtuliani"/>

# <a name="manage-dns-records-and-record-sets-by-using-cli"></a>管理 DNS 記錄，然後使用 CLI 記錄設定


> [AZURE.SELECTOR]
- [Azure 入口網站](dns-operations-recordsets-portal.md)
- [Azure CLI](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


本文將示範如何使用跨平台 Azure 命令列介面 (CLI) 管理記錄集和您的 DNS 區域記錄。

請務必瞭解 DNS 記錄集與個別的 DNS 記錄之間的差異。 記錄的設定是區域中有相同的名稱，而且相同類型的記錄的集合。 如需詳細資訊，請參閱[瞭解記錄集和記錄](dns-getstarted-create-recordset-cli.md)。


## <a name="configure-the-cross-platform-azure-cli"></a>設定跨平台 Azure CLI

Azure DNS 是 Azure 資源管理員專用服務。 沒有 Azure 服務管理 API。 請確定 Azure CLI 已設定使用資源管理員模式，請使用`azure config mode arm`] 命令。

如果您看到**錯誤: 「 dns 「 不是 azure 命令**，可能是因為您使用的 Azure CLI Azure 服務管理模式中，在資源管理員模式中。

## <a name="create-a-new-record-set-and-record"></a>建立新的記錄設定及記錄

若要建立新記錄中 Azure 入口網站設定，請參閱[建立記錄設定及記錄](dns-getstarted-create-recordset-cli.md)。


## <a name="retrieve-a-record-set"></a>擷取記錄的設定

若要擷取現有記錄集，請使用`azure network dns record-set show`。 指定 [資源] 群組中，區域名稱記錄設定相關的名稱，然後記錄類型。 使用下方，取代的值，使用您自己的範例。

    azure network dns record-set show myresourcegroup contoso.com www A


## <a name="list-record-sets"></a>清單記錄集

您可以使用列出 DNS 區域中的所有記錄`azure network dns record-set list`] 命令。 您要指定區域名稱與資源群組名稱。

### <a name="to-list-all-record-sets"></a>列出所有記錄集

此範例會傳回所有記錄集，無論名稱或記錄類型︰

    azure network dns record-set list myresourcegroup contoso.com

### <a name="to-list-record-sets-of-a-given-type"></a>到清單的指定類型的記錄集

此範例會傳回符合指定的記錄類型 （在此例中，「 「 記錄） 的所有記錄集︰

    azure network dns record-set list myresourcegroup contoso.com A


## <a name="add-a-record-to-a-record-set"></a>新增記錄至記錄的設定

您將記錄新增至記錄集使用`azure network dns record-set add-record`] 命令。 將記錄新增至記錄的設定的參數設定之記錄的類型而有所不同。 例如，當您使用"A"類型的記錄設定，您可以只指定記錄和參數`-a <IPv4 address>`。

若要建立記錄的設定，請使用`azure network dns record-set create`] 命令。 指定 [資源] 群組中，區域名稱記錄設定相對的名稱、 記錄類型和 live (TTL) 的時間。 如果`--ttl`參數未定義，以四個預設值] 的值 （秒）。

    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300


建立 「 「 記錄集之後，請利用新增 IPv4 位址`azure network dns record-set add-record`] 命令。

    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1


下列範例會示範如何建立記錄設定的每個記錄類型。 每個記錄設定包含單一記錄。

[AZURE.INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]


## <a name="update-a-record-in-a-record-set"></a>更新資料錄集中記錄

### <a name="to-add-another-ip-address-1234-to-an-existing-a-record-set-www"></a>若要將另一個 IP 位址 (1.2.3.4) 新增至現有的 「 」 記錄設定 (「 www 」):

    azure network dns record-set add-record  myresourcegroup contoso.com  A
    -a 1.2.3.4
    info:    Executing command network dns record-set add-record
    Record set name: www
    + Looking up the dns zone "contoso.com"
    + Looking up the DNS record set "www"
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/a
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:        IPv4 address                : 192.168.1.1
    data:        IPv4 address                : 1.2.3.4
    data:
    info:    network dns record-set add-record command OK

### <a name="to-remove-an-existing-value-from-a-record-set"></a>若要從記錄中移除現有的值
使用`azure network dns record-set delete-record`。

    azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
    info:    Executing command network dns record-set delete-record
    + Looking up the DNS record set "www"
    Delete DNS record? [y/n] y
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/A
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:    IPv4 address                    : 192.168.1.1
    data:
    info:    network dns record-set delete-record command OK



## <a name="remove-a-record-from-a-record-set"></a>從記錄中移除記錄

您可以從設定使用記錄移除記錄`azure network dns record-set delete-record`。 要移除的記錄必須是完全符合現有記錄的所有參數。

移除記錄設定的最後一筆記錄，並不會刪除記錄的設定。 如需詳細資訊，請參閱本文稱為[刪除記錄集](#delete)一節。

    azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

    azure network dns record-set delete myresourcegroup contoso.com www A

### <a name="remove-an-aaaa-record-from-a-record-set"></a>從記錄中移除 AAAA 記錄

    azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"

### <a name="remove-a-cname-record-from-a-record-set"></a>從記錄中移除一筆 CNAME 記錄

    azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com


### <a name="remove-an-mx-record-from-a-record-set"></a>MX 記錄移除記錄的設定

    azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5

### <a name="remove-an-ns-record-from-record-set"></a>NS 記錄移除記錄設定

    azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"

### <a name="remove-a-ptr-record-from-a-record-set"></a>從記錄中移除 PTR 記錄
在此情況下 「 我的 arpa-zone.com 」 代表代表您的 IP 範圍的 ARPA 區域。  設定此區域中的每個 PTR 記錄會對應到這個 IP 範圍內的 IP 位址。

    azure network dns record-set delete-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"

### <a name="remove-an-srv-record-from-a-record-set"></a>從記錄中移除一筆 SRV 記錄

    azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com"

### <a name="remove-a-txt-record-from-a-record-set"></a>TXT 記錄移除記錄的設定

    azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## <a name="delete"></a>刪除記錄集

使用也可以刪除記錄集`Remove-AzureRmDnsRecordSet`指令程式。 您無法刪除 SOA 及 NS 記錄設定區域 apex (名稱 ="@")建立自動建立區域的時間。 他們會自動刪除如果刪除該區域。

在下列範例中，「 測試-的 「 設定 」 的 「 記錄會從 「 contoso.com 「 DNS 區域︰

    azure network dns record-set delete myresourcegroup contoso.com  "test-a" A

若要隱藏出現確認提示時可選用*-q*切換。


## <a name="next-steps"></a>後續步驟

如需有關 Azure DNS 的詳細資訊，請參閱[Azure DNS 概觀](dns-overview.md)。 自動化 DNS 的相關資訊，請參閱[建立 DNS 區域和記錄設定為使用.NET SDK](dns-sdk.md)。

如果您想要使用相反的 DNS 記錄，請參閱[如何管理服務使用 Azure CLI 反向 DNS 記錄](dns-reverse-dns-record-operations-cli.md)。
