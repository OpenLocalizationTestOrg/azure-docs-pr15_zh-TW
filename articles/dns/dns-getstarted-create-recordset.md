<properties
   pageTitle="建立記錄的設定和使用 PowerShell DNS 區域記錄 |Microsoft Azure"
   description="如何建立 Azure DNS 主機記錄。設定記錄設定及記錄使用 PowerShell"
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



# <a name="create-dns-record-sets-and-records-by-using-powershell"></a>使用 PowerShell 建立 DNS 記錄設定及記錄


> [AZURE.SELECTOR]
- [Azure 入口網站](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Azure CLI](dns-getstarted-create-recordset-cli.md)

本文會引導您完成建立記錄，記錄集使用 Windows PowerShell 的程序。 建立您的 DNS 區域之後, 您可以新增的 DNS 記錄的網域。 若要這麼做，您必須瞭解 DNS 記錄，以及記錄集。

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## <a name="verify-that-you-have-the-latest-version-of-powershell"></a>驗證您擁有最新版本的 PowerShell

請確認您已安裝最新版 Azure 資源管理員 PowerShell cmdlet。 如需安裝的 PowerShell 指令程式的相關資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。

## <a name="create-a-record-set-and-record"></a>建立的記錄設定及記錄

本節將說明如何建立的記錄設定及記錄。


### <a name="1-connect-to-your-subscription"></a>1.連線至您的訂閱

開啟您 PowerShell 主控台，連線到您的帳戶。 您可以使用下列範例可協助您連線︰

    Login-AzureRmAccount

核取訂閱的帳戶。

    Get-AzureRmSubscription

指定您想要使用的訂閱。

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

如需使用 PowerShell 的詳細資訊，請參閱[使用 Windows PowerShell 與資源管理員](../powershell-azure-resource-manager.md)。


### <a name="2-create-a-record-set"></a>2.建立記錄集

您用來建立記錄集`New-AzureRmDnsRecordSet`指令程式。 在建立記錄的設定，必須先指定記錄設定名稱、 區域、 time to live (TTL) 時間和記錄類型。

若要建立一筆記錄的區域 apex 中設定 (在此情況下，「 contoso.com 」)，使用記錄名稱"@",包括括住。 這是常見的 DNS 慣例。

下列範例會建立記錄 」 contoso.com 」 上的 [DNS] 區域中的相對名稱 「 www 」 設定。 記錄集的完整名稱為 「 www.contoso.com 」。 記錄類型是"A"，及 [TTL] 是 60 秒。 完成這個步驟之後，您必須指定至變數*$rs*空白 「 www 」 記錄設定。

    $rs = New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

#### <a name="if-a-record-set-already-exists"></a>如果記錄設定已經存在

如果已設定一筆記錄，則指令會失敗除非*-覆寫*使用參數。 *-覆寫*選項觸發確認提示時，可以使用隱藏*-強制*切換。


    $rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]


在此範例中，您可以指定區域使用區域名稱和資源群組名稱。 或者，您可以指定區域物件]，如下所傳回的`Get-AzureRmDnsZone`或`New-AzureRmDnsZone`。

    $zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
    $rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 –Zone $zone [-Tag $tags] [-Overwrite] [-Force]

`New-AzureRmDnsRecordSet`傳回代表 Azure DNS 中所建立的記錄集的本機物件。

### <a name="3-add-a-record"></a>3.新增一筆記錄

若要使用的新建立的 「 www 」 記錄設定，您需要為其新增記錄。 您可以新增 IPv4 使用下列範例*A*記錄，以 「 www 」 記錄設定。 此範例中依賴變數*$rs*您在上一個步驟中設定。

將記錄新增至記錄設定使用`Add-AzureRmDnsRecordConfig`是離線作業。 僅限本機變數*$rs*會更新。


    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221

### <a name="4-commit-the-changes"></a>4.認可變更

認可記錄設定所做的變更。 使用`Set-AzureRmDnsRecordSet`以變更上傳至設定 Azure dns 記錄。

    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="5-retrieve-the-record-set"></a>5。 擷取記錄集

您可以藉由設定從 Azure DNS 記錄來擷取`Get-AzureRmDnsRecordSet`如下列範例所示。


    Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : MyAzureResourceGroup
    Ttl               : 3600
    Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
    RecordType        : A
    Records           : {134.170.185.46, 134.170.188.221}
    Tags              : {}


您也可以使用 nslookup 工具] 或 [其他 DNS 工具來查詢新記錄的設定。

如果還沒有委派網域 Azure DNS 名稱伺服器，必須先明確地指定名稱、 伺服器，以及您的區域的地址。


    nslookup www.contoso.com ns1-01.azure-dns.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    Name:    www.contoso.com
    Addresses:  134.170.185.46
                134.170.188.221

## <a name="create-a-record-set-of-each-type-with-a-single-record"></a>建立與單一記錄的每個類型的記錄集


下列範例會示範如何建立記錄設定的每個記錄類型。 每個記錄設定包含單一記錄。

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]


## <a name="next-steps"></a>後續步驟

[如何管理 DNS 區域使用 PowerShell](dns-operations-dnszones.md)

[使用 PowerShell 來管理 DNS 記錄並記錄集](dns-operations-recordsets.md)

[自動化與.NET SDK Azure 作業](dns-sdk.md)
