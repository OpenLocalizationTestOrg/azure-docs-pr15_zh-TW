<properties
   pageTitle="建立自訂的 DNS 記錄的 web 應用程式 |Microsoft Azure  "
   description="如何建立的自訂網域的 DNS 記錄，透過 Azure DNS 的 web 應用程式。"
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

# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>建立的自訂網域的 DNS 記錄的 web 應用程式

您可以使用 Azure DNS 裝載您的 web 應用程式的自訂網域。 例如，您所建立 Azure web 應用程式，而且您希望使用者存取，請使用 contoso.com 或 www.contoso.com 作為 FQDN。

若要這麼做，您必須建立兩筆記錄︰

- 指向 [contoso.com 根"A"記錄
- [CNAME] 記錄指向 A 記錄 www 名稱

請記住，如果您是 Azure 中建立 A 記錄以在 web 應用程式，A 記錄必須手動更新如果基礎的 IP 位址變更 web 應用程式。

## <a name="before-you-begin"></a>開始之前

您開始之前，必須先中 Azure DNS 建立 DNS 區域，然後在您的註冊機構 Azure dns 委派區域。

1. 若要建立 DNS 區域，請依照[建立 DNS 區域](dns-getstarted-create-dnszone.md)中的步驟。
2. 若要委派您的 DNS Azure dns，請依照[DNS 網域委派](dns-domain-delegation.md)中的步驟。

建立區域，Azure dns 委派它，然後您可以建立記錄您的自訂網域。


## <a name="1-create-an-a-record-for-your-custom-domain"></a>1.建立 A 記錄以您的自訂網域

A 記錄用來將名稱對應到其 IP 位址。 在下列範例中，我們將會指派@為 A 記錄，IPv4 位址︰

### <a name="step-1"></a>步驟 1

建立 A 記錄，並指派給變數 $rs

    $rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600

### <a name="step-2"></a>步驟 2

將 IPv4 值新增至先前建立的記錄設定"@"使用指派的 $rs 變數。 指派 IPv4 值就是您的 web 應用程式的 IP 位址。

若要尋找的 web 應用程式的 IP 位址，請遵循[設定自訂網域名稱 Azure 應用程式服務中](../web-sites-custom-domain-name.md#Find-the-virtual-IP-address)的步驟進行。

    Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address <your web app IP address>

### <a name="step-3"></a>步驟 3

認可記錄設定所做的變更。 使用`Set-AzureRMDnsRecordSet`以變更上傳至設定 Azure dns 記錄︰

    Set-AzureRMDnsRecordSet -RecordSet $rs

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2.建立您的自訂網域的 CNAME 記錄

如果您的網域已受 Azure DNS (請參閱[DNS 網域委派](dns-domain-delegation.md)，您可以使用下列範例中，若要建立 contoso.azurewebsites.net 的 CNAME 記錄。

### <a name="step-1"></a>步驟 1

開啟 PowerShell，並建立新的 CNAME 記錄集並指派給變數 $rs。 此範例會建立記錄設定類型 CNAME 」 時間 live 」 600 秒的名為 「 contoso.com 」 的 DNS 區域中。

    $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600

    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
    RecordType        : CNAME
    Records           : {}
    Tags              : {}


### <a name="step-2"></a>步驟 2

一旦建立 CNAME 記錄設定，必須先建立 web 應用程式會指向 alias （別名） 值。

使用先前指派的變數 」 $rs 「 您可以使用下列 PowerShell 命令建立 web 應用程式 contoso.azurewebsites.net 的別名。

    Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"

    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}

### <a name="step-3"></a>步驟 3

確認使用的變更`Set-AzureRMDnsRecordSet`指令程式︰

    Set-AzureRMDnsRecordSet -RecordSet $rs

您可以在驗證記錄已正確地建立查詢 」 www.contoso.com 「 使用 nslookup，如下所示︰

    PS C:\> nslookup
    Default Server:  Default
    Address:  192.168.0.1

    > www.contoso.com
    Server:  default server
    Address:  192.168.0.1

    Non-authoritative answer:
    Name:    <instance of web app service>.cloudapp.net
    Address:  <ip of web app service>
    Aliases:  www.contoso.com
    contoso.azurewebsites.net
    <instance of web app service>.vip.azurewebsites.windows.net

## <a name="create-an-awverify-record-for-web-apps"></a>建立 「 awverify 」 記錄的 web 應用程式


如果您決定要用於您的 web 應用程式的 A 記錄，您必須經過驗證程序，以確保您擁有自訂的網域。 建立名為 「 awverify 」 特殊 CNAME 記錄，以完成此步驟中驗證。 本節適用於僅限記錄。


### <a name="step-1"></a>步驟 1

建立 「 awverify 」 記錄。 在下面的範例中，我們會建立 contoso.com 確認擁有權的自訂網域的 「 aweverify 」 記錄。

    $rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "awverify" -RecordType "CNAME" -Ttl 600

    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
    RecordType        : CNAME
    Records           : {}
    Tags              : {}


### <a name="step-2"></a>步驟 2

一旦建立記錄設定 「 awverify 」，指定 CNAME 記錄設定別名。 在下面的範例中，我們將會指派別名設定為 [awverify.contoso.azurewebsites.net 的 CNAMe 記錄。

    Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"

    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {awverify.contoso.azurewebsites.net}
    Tags              : {}

### <a name="step-3"></a>步驟 3

確認使用的變更`Set-AzureRMDnsRecordSet cmdlet`，如以下的指令所示。

    Set-AzureRMDnsRecordSet -RecordSet $rs



## <a name="next-steps"></a>後續步驟

遵循步驟來設定您的 web 應用程式，使用自訂網域的 [[設定應用程式服務的自訂網域名稱](../app-service-web/web-sites-custom-domain-name.md)。








