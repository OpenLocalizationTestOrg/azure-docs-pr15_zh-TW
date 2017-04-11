<properties
   pageTitle="管理 DNS 區域使用 PowerShell |Microsoft Azure"
   description="您可以管理使用 Powershell 的 Azure DNS 區域。 如何更新、 刪除和 Azure DNS 上建立 DNS 區域"
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

# <a name="how-to-manage-dns-zones-using-powershell"></a>如何管理 DNS 區域使用 PowerShell

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)



本文將說明如何使用 PowerShell 來管理您的 DNS 區域。 若要使用下列步驟，必須安裝最新版的 （1.0 或更新版本） 的 Azure 資源管理員 PowerShell 指令程式。 如需安裝的 PowerShell 指令程式的相關資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。


## <a name="create-a-new-dns-zone"></a>建立新的 DNS 區域

若要建立 DNS 區域，請參閱[建立使用 PowerShell DNS 區域](dns-getstarted-create-dnszone.md)。

## <a name="get-a-dns-zone"></a>取得 DNS 區域

若要擷取 DNS 區域，請使用`Get-AzureRmDnsZone`指令程式。 這項作業會傳回對應到現有的 Azure DNS 區域的 DNS 區域物件。 物件包含資料的相關的區域 （例如記錄集的號碼），但不包含自己記錄集。

    $zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

## <a name="list-dns-zones"></a>清單 DNS 區域

省略區域名稱從`Get-AzureRmDnsZone`，您可以列舉資源群組中的所有區域。 這項作業會傳回陣列的區域物件。

    $zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup

## <a name="update-a-dns-zone"></a>更新 DNS 區域

DNS zone 資源可以變更使用`Set-AzureRmDnsZone`。 這並不會更新 DNS 記錄集區域內的任何 （請參閱[如何管理 DNS 記錄](dns-operations-recordsets.md)）。 它只會用來更新區域資源本身的內容。 這是目前有限至 Azure 資源管理員 「 標籤 」 區域資源。 如需詳細資訊，請參閱[Etag 和標籤](dns-getstarted-create-dnszone.md#Etags-and-tags)。

更新 DNS 區域的兩種方法使用下列其中一項︰

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>指定使用區域名稱和資源群組的區域

    Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### <a name="specify-the-zone-using-a-zone-object"></a>指定使用 $zone 物件的區域

指定使用 $zone 物件的區域`Get-AzureRmDnsZone`。 使用時`Set-AzureRmDnsZone`$zone 物件時，會使用 Etag 檢查以確保同時變更不會覆寫。 您可以使用 [選擇性*-覆寫*切換至 [隱藏這些檢查。 如需詳細資訊，請參閱[Etag 和標籤](dns-getstarted-create-dnszone.md#Etags-and-tags)。


    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    <..modify $zone.Tags here...>
    Set-AzureRmDnsZone -Zone $zone [-Overwrite]


## <a name="delete-a-dns-zone"></a>刪除 DNS 區域

可以使用移除 AzureRmDnsZone 指令程式會刪除 DNS 區域。

在刪除之前 Azure DNS 區域，您會需要刪除所有記錄集，除了 NS 和 SOA 區域根目錄的記錄建立區域時自動建立。

使用下列兩種方法之一來移除 DNS 區域︰

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>指定使用區域的名稱和資源群組名稱的區域

這項作業具有選用*-強制*切換碼，它會隱藏，確認您要移除的 DNS 區域的提示。

    Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]

### <a name="specify-the-zone-using-a-zone-object"></a>指定使用 $zone 物件的區域

指定使用 $zone 物件的區域`Get-AzureRmDnsZone`。 這項作業具有選用*-強制*切換碼，它會隱藏，確認您要移除的 DNS 區域的提示。 使用`Set-AzureRmDnsZone`，指定使用 $zone 物件的區域可讓 Etag 檢查以確保同時變更並不會刪除。 <BR>
選擇性*-覆寫*標幟隱藏這些檢查。 如需詳細資訊，請參閱[Etag 和標籤](dns-getstarted-create-dnszone.md#Etags-and-tags)。

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]



區域物件可以也可傳送，而不是傳遞做為參數︰

    Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]

## <a name="next-steps"></a>後續步驟

建立 DNS 區域之後, 建立[記錄集和記錄](dns-getstarted-create-recordset.md)啟動解決您的網際網路網域的名稱。