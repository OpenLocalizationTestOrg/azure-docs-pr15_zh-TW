<properties
   pageTitle="開始使用 Azure DNS |Microsoft Azure"
   description="瞭解如何建立 DNS 區域 Azure dns。這是在步驟步驟以取得您啟動裝載您的 DNS 網域使用 PowerShell 所建立的第一個 DNS 區域。"
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

# <a name="create-a-dns-zone-using-powershell"></a>建立使用 Powershell DNS 區域

> [AZURE.SELECTOR]
- [Azure 入口網站](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)

本文會引導您執行的步驟，使用 PowerShell 來建立 DNS 區域。 您也可以建立使用 CLI 或 Azure 入口網站的 DNS 區域。

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="tagetag"></a>關於 Etag 和標籤

### <a name="etags"></a>Etag

假設您有兩人或兩個程序嘗試同時修改 DNS 記錄。 哪種贏得？ 與並輸贏知道，他們已只覆寫變更由其他人建立嗎？

Azure DNS 使用 Etag 安全地處理相同資源的同時變更。 每個 DNS 資源 （區域或記錄設定） 具有與其相關聯 Etag。 擷取資源時，其 Etag 也擷取。 在更新資源時，您可以傳送回 Etag 讓 Azure DNS 可以確認 Etag 伺服器相符項目上。 每次更新資源產生 Etag 重新產生，因為 Etag 不符表示同時變更。 Etag 也會使用建立新的資源時以確保的資源已經存在。

根據預設，DNS PowerShell 的 Azure 會使用 Etag 封鎖區域的同時變更，記錄集。 選擇性*-覆寫*切換可以用來隱藏 Etag 檢查，這種情況發生任何同時變更將會覆寫。

層級 Azure DNS REST API，Etag 會指定使用 HTTP 標題。  下表中提供的行為︰

|頁首|行為|
|------|--------|
|無|將永遠成功 （沒有 Etag 檢查）|
|如果符合的項目<etag>|將只成功如果資源存在，並 Etag 比對|
|如果符合 *     | 將只成功如果資源|
|如果無-比對 * |  如果資源不存在，只順利完成放入|

### <a name="tags"></a>標記

標記是 Etag 不同。 標記的清單名稱 / 值組與使用由 Azure 資源管理員標籤資源的帳單或群組的用途。 如需標記的詳細資訊，請參閱[使用標籤以組織 Azure 資源](../resource-group-using-tags.md)。

Azure DNS PowerShell 支援標籤區域和記錄集指定使用的選項上`-Tag`參數。


## <a name="before-you-begin"></a>開始之前

確認您有下列項目，才能開始您的設定。

- Azure 的訂閱。 如果您還沒有 Azure 的訂閱，您可以啟動您的[MSDN 訂閱者優惠](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或註冊設定[免費的帳戶](https://azure.microsoft.com/pricing/free-trial/)。

- 您需要安裝最新版的 （1.0 或更新版本） 的 Azure 資源管理員 PowerShell 指令程式。 如需安裝的 PowerShell 指令程式的相關資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。

## <a name="step-1---sign-in"></a>步驟 1-登入

開啟您 PowerShell 主控台，連線到您的帳戶。 如需詳細資訊，請參閱[使用 Windows PowerShell 與資源管理員](../powershell-azure-resource-manager.md)。

您可以使用下列範例可協助您連線︰

    Login-AzureRmAccount

核取訂閱的帳戶。

    Get-AzureRmSubscription

指定您想要使用的訂閱。

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## <a name="step-2---create-a-resource-group"></a>步驟 2-建立資源群組

Azure 資源管理員需要所有資源群組都指定的位置。 這用於做為預設位置的 [資源] 群組中的資源。 不過，因為所有的 DNS 資源是全域管理員、 不地區，資源群組位置的選擇會造成任何影響 Azure dns。

如果您使用現有的資源群組，您可以略過此步驟。

    New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"


## <a name="step-3---register"></a>步驟 3-Register

Azure DNS 服務是由 Microsoft.Network 資源提供者管理。 Azure 訂閱，必須使用此資源提供者，才能使用 Azure DNS 註冊。 這是針對每個訂閱的一次性作業。

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network


## <a name="step-4----create-a-dns-zone"></a>步驟 4-建立 DNS 區域

使用建立 DNS 區域`New-AzureRmDnsZone`指令程式。 有包含或不含標籤建立 DNS 區域下方的範例。 如需標記的詳細資訊，請參閱本文中的[標籤](#tags)上的 [] 區段。

>[AZURE.NOTE] Azure dns 區域名稱應指定而不需要終止**'。 「**。 例如，為 '**contoso.com**' 而非 「**contoso.com。**」。

### <a name="to-create-a-dns-zone"></a>若要建立 DNS 區域

下列範例會建立 DNS 區域中的資源群組，稱為*MyResourceGroup*名為*contoso.com* 。 使用範例，建立 DNS 區域，以取代您自己的值。

    New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

### <a name="to-create-a-dns-zone-with-tags"></a>若要建立 DNS 區域標籤

下列範例會示範如何使用兩個標籤]，建立 DNS 區域*專案 = 示範*和*信封 = 測試*。 使用範例，建立 DNS 區域，以取代您自己的值。

    New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )

## <a name="view-records"></a>檢視記錄

建立 DNS 區域時，也會建立下列 DNS 記錄︰

- *開始的授權單位*(SOA) 記錄。 這是出現在每個 DNS 區域的根目錄。

- 授權的名稱伺服器 (NS) 記錄。 這些顯示的名稱伺服器所裝載的區域。 Azure DNS 使用集區的名稱伺服器，以及不同的名稱伺服器可能會指派給 Azure DNS 中的不同區域。 請參閱[委派 Azure dns 網域](dns-domain-delegation.md)如需詳細資訊。

若要檢視這些記錄，請使用`Get-AzureRmDnsRecordSet`:

    Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

    Name              : @
    ZoneName          : contoso.com
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 2b855de1-5c7e-4038-bfff-3a9e55b49caf
    RecordType        : SOA
    Records           : {[ns1-01.azure-dns.com,msnhst.microsoft.com,900,300,604800,300]}
    Tags              : {}

    Name              : @
    ZoneName          : contoso.com
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
    RecordType        : NS
    Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                  ns4-01.azure-dns.info}
    Tags              : {}


記錄組的根 （或*apex*） DNS 區域使用**@**記錄設定名稱。


## <a name="test"></a>測試

您可以使用 DNS 工具，例如 nslookup、 挖掘或[解決 DnsName PowerShell cmdlet](https://technet.microsoft.com/library/jj590781.aspx)來測試您的 DNS 區域。

如果您還沒有委派您的網域以使用新的 Azure DNS 區域，您必須將 DNS 查詢直接到其中一個名稱伺服器，您的區域。 在所列的 NS 記錄，指定您區域的名稱伺服器`Get-AzureRmDnsRecordSet`上方。 請確定替代正確的值，您的區域將以下的指令。

    nslookup
    > set type=SOA
    > server ns1-01.azure-dns.com
    > contoso.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    contoso.com
            primary name server = ns1-01.azure-dns.com
            responsible mail addr = msnhst.microsoft.com
            serial  = 1
            refresh = 900 (15 mins)
            retry   = 300 (5 mins)
            expire  = 604800 (7 days)
            default TTL = 300 (5 mins)


## <a name="next-steps"></a>後續步驟

建立 DNS 區域之後, 建立[記錄集和記錄](dns-getstarted-create-recordset.md)啟動解決您的網際網路網域的名稱。

