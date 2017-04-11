<properties
   pageTitle="使用 [Azure 入口網站管理 DNS 記錄集和記錄 |Microsoft Azure"
   description="裝載您的網域，在 Azure 的 DNS 時，管理 DNS 記錄集和 Azure DNS 記錄。 所有 PowerShell 命令，記錄集和記錄上進行的作業。"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="manage-dns-records-and-record-sets-by-using-powershell"></a>使用 PowerShell 來管理 DNS 記錄並記錄集



> [AZURE.SELECTOR]
- [Azure 入口網站](dns-operations-recordsets-portal.md)
- [Azure CLI](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)



本文將示範如何使用 Windows PowerShell 管理記錄集和您的 DNS 區域記錄。

請務必瞭解 DNS 記錄集與個別的 DNS 記錄之間的差異。 記錄會記錄在區域中有相同的名稱，而且相同類型的集合。 如需詳細資訊，請參閱[建立 DNS 記錄設定和使用 Azure 入口網站的記錄](dns-getstarted-create-recordset-portal.md)。

若要管理您的記錄集和記錄，您需要最新版 Azure 資源管理員 PowerShell cmdlet。 如需詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。 如需使用 PowerShell 的詳細資訊，請參閱[使用 PowerShell 的 Azure 與 Azure 資源管理員](../powershell-azure-resource-manager.md)。



## <a name="create-a-new-record-set-and-a-record"></a>建立新的記錄設定及記錄

若要建立使用 PowerShell 來設定記錄，請參閱[建立 DNS 記錄設定和使用 PowerShell 的記錄](dns-getstarted-create-recordset.md)。

## <a name="get-a-record-set"></a>取得記錄的設定

若要擷取現有記錄集，請使用`Get-AzureRmDnsRecordSet`。 指定記錄設定相對的名稱、 記錄類型，與該區域。

    $rs = Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

使用`New-AzureRmDnsRecordSet`，記錄名稱必須是相關的名稱，這表示它必須排除區域名稱。

使用區域名稱和資源群組的名稱，或使用區域物件，您可以指定區域︰

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
    $rs = Get-AzureRmDnsRecordSet -Name www –RecordType A -Zone $zone

`Get-AzureRmDnsRecordSet`傳回代表 Azure DNS 中所建立的記錄集的本機物件。

## <a name="list-record-sets"></a>清單記錄集

您也可以使用`Get-AzureRmDnsRecordSet`到清單記錄集如果您省略*– 名稱*及/或*– RecordType*參數。

### <a name="to-list-all-record-sets"></a>列出所有記錄集

此範例會傳回所有記錄集，無論名稱或記錄類型︰

    $list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

### <a name="to-list-record-sets-of-a-given-record-type"></a>到清單中的特定的記錄類型的記錄集

此範例會傳回符合指定的記錄類型的所有記錄集。 在這個案例中，記錄集就是傳回是 」 「 記錄︰

    $list = Get-AzureRmDnsRecordSet –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

使用 [ *– ZoneName*及*– ResourceGroupName*參數 （如下圖所示），或指定區域物件，您可以指定區域︰

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
    $list = Get-AzureRmDnsRecordSet -Zone $zone

## <a name="add-a-record-to-a-record-set"></a>新增記錄至記錄的設定

您將記錄新增至記錄集使用`Add-AzureRmDnsRecordConfig`指令程式。 這是離線的作業。 僅限本機的物件，代表記錄集即會變更。

將記錄新增至記錄的設定參數記錄集類型而有所不同。 例如時使用"A"類型的記錄設定，您可以只指定記錄和參數*-IPv4Address*。

其他的記錄可以新增至每一筆記錄其他的來電至] 來設定`Add-AzureRmDnsRecordConfig`。 您可以新增任何記錄設定 20 個記錄。 不過，類型 [CNAME] 記錄組最多可以包含最多一筆記錄，然後記錄的設定不能包含兩個相同的記錄。 空白的記錄集 （含零記錄） 您可以建立，但不是會顯示在 Azure DNS 名稱伺服器上。

記錄集包含所要的記錄集合後，您必須以確認其使用`Set-AzureRmDnsRecordSet`指令程式。 已認可記錄的設定之後，它會取代現有的記錄中 Azure DNS 設定。

### <a name="to-create-an-a-record-set-with-a-single-record"></a>若要建立 A 記錄以單一記錄設定

    $rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
    Set-AzureRmDnsRecordSet -RecordSet $rs

若要建立一筆記錄的作業的順序也會*傳送*，這表示您使用管道，而不是將它做為參數傳遞傳遞記錄設定物件。 例如︰

    New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### <a name="additional-record-type-examples"></a>其他的記錄類型的範例

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## <a name="modify-existing-record-sets"></a>修改現有記錄集

修改現有的記錄設定的步驟很類似時建立記錄所採取的步驟。 作業的順序如下所示︰

1.  擷取現有記錄設定使用`Get-AzureRmDnsRecordSet`。

2.  修改記錄以將記錄新增、 移除記錄、 變更記錄的參數，設定或變更記錄設定時間 (ttl)。 這是離線的作業。 僅限本機的物件，代表記錄集即會變更。

3.  使用認可變更`Set-AzureRmDnsRecordSet`指令程式。 這會取代現有的記錄中 Azure DNS 設定。


### <a name="to-update-a-record-in-an-existing-record-set"></a>若要更新現有的記錄設定中的記錄

在此範例中，我們可以變更現有的 「 一 」 筆記錄的 IP 位址︰

    $rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Records[0].Ipv4Address = "134.170.185.46"
    Set-AzureRmDnsRecordSet -RecordSet $rs

`Set-AzureRmDnsRecordSet`指令程式會使用 etag 檢查以確保同時變更不會覆寫。 使用*-覆寫*標幟] 來隱藏這些檢查。 如需詳細資訊，請參閱[關於 etag 和標記](dns-getstarted-create-dnszone.md#tagetag)。

### <a name="to-modify-an-soa-record"></a>若要修改 SOA 記錄

您無法新增或移除記錄會自動建立 SOA 記錄設定區域 apex (名稱 = "@")。 不過，您可以修改任何 SOA 記錄 （除了 「 主機 」） 中的參數，記錄設定 [TTL]。

下列範例會示範如何變更 SOA 記錄的*電子郵件*] 屬性︰

    $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Records[0].Email = "admin.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="to-modify-ns-records-at-the-zone-apex"></a>若要修改區域 apex NS 記錄

您無法新增若要移除，或修改自動建立 NS 記錄集區域 apex 中的記錄 (名稱 = "@")。 只允許變更是修改記錄設定 [TTL]。

下列範例會示範如何變更的 NS 記錄設定 [TTL] 屬性︰

    $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Ttl = 300
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="to-add-records-to-an-existing-record-set"></a>若要將記錄新增至現有的記錄設定

在此範例中，我們可以新增兩個額外的 MX 記錄到現有記錄的設定︰

    $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
    Set-AzureRmDnsRecordSet -RecordSet $rs

## <a name="remove-a-record-from-an-existing-record-set"></a>移除現有記錄集的記錄

您可以從設定使用記錄移除記錄`Remove-AzureRmDnsRecordConfig`。 要移除的記錄必須是完全符合現有記錄的所有參數。 必須使用交付變更`Set-AzureRmDnsRecordSet`。

移除記錄設定的最後一筆記錄，並不會刪除記錄的設定。 如需詳細資訊，請參閱下方的 [[刪除記錄集](#delete-a-record-set)。


    $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
    Set-AzureRmDnsRecordSet -RecordSet $rs

若要移除的記錄組中的一筆記錄的作業的順序可以也可傳送，這表示您使用管道，而不是將它做為參數傳遞傳遞記錄設定物件。 例如︰

    Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### <a name="remove-an-aaaa-record-from-a-record-set"></a>從記錄中移除 AAAA 記錄

    $rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-a-cname-record-from-a-record-set"></a>從記錄中移除一筆 CNAME 記錄

這是因為 CNAME 記錄設定可以包含最多一筆記錄，移除該筆記錄保留空白的記錄設定。

    $rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-mx-record-from-a-record-set"></a>MX 記錄移除記錄的設定

    $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-ns-record-from-record-set"></a>NS 記錄移除記錄設定

    $rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-srv-record-from-a-record-set"></a>從記錄中移除一筆 SRV 記錄

    $rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-a-txt-record-from-a-record-set"></a>TXT 記錄移除記錄的設定

    $rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
    Set-AzureRmDnsRecordSet -RecordSet $rs

## <a name="delete-a-record-set"></a>刪除記錄集

使用也可以刪除記錄集`Remove-AzureRmDnsRecordSet`指令程式。 您無法刪除 SOA 及 NS 記錄設定區域 apex (名稱 ="@")建立自動建立區域的時間。 他們會自動刪除如果刪除該區域。

若要移除的記錄組中使用下列三種方法之一︰

### <a name="specify-all-the-parameters-by-name"></a>指定的所有參數名稱

選擇性*-強制*可以隱藏出現確認提示時使用參數。

    Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]


### <a name="specify-the-record-set-by-name-and-type-and-specify-the-zone-by-object"></a>指定記錄集的名稱與類型，並指定的物件的區域

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### <a name="specify-the-record-set-by-object"></a>指定設定物件的記錄

    $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

當您指定記錄集使用物件時，它會讓 etag 檢查以確保同時變更不會刪除。 選擇性*-覆寫*標幟隱藏這些檢查。 如需詳細資訊，請參閱[Etag 和標籤](dns-getstarted-create-dnszone.md#tagetag)。

記錄設定物件可以也可傳送，而不是傳遞做為參數︰

    Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]

## <a name="next-steps"></a>後續步驟

如需有關 Azure DNS 的詳細資訊，請參閱[Azure DNS 概觀](dns-overview.md)。 自動化 DNS 的相關資訊，請參閱[建立 DNS 區域和記錄設定為使用.NET SDK](dns-sdk.md)。

如需有關反向 DNS 記錄的詳細資訊，請參閱[如何管理服務使用 PowerShell 反向 DNS 記錄](dns-reverse-dns-record-operations-ps.md)。
