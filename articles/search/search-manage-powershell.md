<properties 
    pageTitle="使用 Powershell 指令碼管理 Azure 搜尋 |Microsoft Azure |裝載的雲端搜尋服務" 
    description="管理您的 Azure 搜尋服務的 PowerShell 指令碼。 建立或更新 Azure 搜尋服務及管理 Azure 搜尋系統索引鍵" 
    services="search" 
    documentationCenter="" 
    authors="seansaleh" 
    manager="mblythe" 
    editor=""
    tags="azure-resource-manager"/>

<tags 
    ms.service="search" 
    ms.devlang="na" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="powershell" 
    ms.date="08/15/2016" 
    ms.author="seasa"/>

# <a name="manage-your-azure-search-service-with-powershell"></a>管理 PowerShell 的 Azure 搜尋服務
> [AZURE.SELECTOR]
- [入口網站](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [REST API](search-get-started-management-api.md)

本主題說明來執行許多管理工作 Azure 搜尋服務的 PowerShell 命令。 我們會逐步建立搜尋服務、 縮放比例，及管理其 API 金鑰。
這些命令平行[Azure 搜尋管理 REST API](http://msdn.microsoft.com/library/dn832684.aspx)中可用的 [管理] 選項。

## <a name="prerequisites"></a>必要條件
 
- 您必須具備 Azure PowerShell 1.0 或更大。 如需相關指示，請參閱[安裝及設定 PowerShell 的 Azure](../powershell-install-configure.md)。
- 您必須登入至您在如下所述的 PowerShell 的 Azure 訂閱。

首先，您必須使用這個命令的登入 Azure:

    Login-AzureRmAccount

Microsoft Azure 登入] 對話方塊中，指定您 Azure 帳戶和其密碼的電子郵件地址。

或者，您可以[登入非互動方式與主要的服務](../resource-group-authenticate-service-principal.md)。

如果您有多個 Azure 訂閱，必須先設定您的 Azure 訂閱。 若要查看您目前訂閱的清單，請執行此命令。

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

若要指定訂閱，請執行下列命令。 在下列範例中，是訂閱名稱`ContosoSubscription`。

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>命令可協助您快速入門

    $serviceName = "your-service-name-lowercase-with-dashes"
    $sku = "free" # or "basic" or "standard" for paid services
    $location = "West US"
    # You can get a list of potential locations with
    # (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
    $resourceGroupName = "YourResourceGroup" 
    # If you don't already have this resource group, you can create it with 
    # New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Register the ARM provider idempotently. This must be done once per subscription
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search" -Force

    # Create a new search service
    # This command will return once the service is fully created
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
        -NameFromTemplate $serviceName `
        -Sku $sku `
        -Location $location `
        -PartitionCount 1 `
        -ReplicaCount 1
    
    # Get information about your new service and store it in $resource
    $resource = Get-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19
    
    # View your resource
    $resource
    
    # Get the primary admin API key
    $primaryKey = (Invoke-AzureRmResourceAction `
        -Action listAdminKeys `
        -ResourceId $resource.ResourceId `
        -ApiVersion 2015-08-19).PrimaryKey

    # Regenerate the secondary admin API Key
    $secondaryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action secondary).SecondaryKey

    # Create a query key for read only access to your indexes
    $queryKeyDescription = "query-key-created-from-powershell"
    $queryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/createQueryKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action $queryKeyDescription).Key
    
    # View your query key
    $queryKey

    # Delete query key
    Remove-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19
        
    # Scale your service up
    # Note that this will only work if you made a non "free" service
    # This command will not return until the operation is finished
    # It can take 15 minutes or more to provision the additional resources
    $resource.Properties.ReplicaCount = 2
    $resource | Set-AzureRmResource
    
    # Delete your service
    # Deleting your service will delete all indexes and data in the service
    $resource | Remove-AzureRmResource
    
## <a name="next-steps"></a>後續步驟
    
現在，建立您的服務，您可以採取的下一個步驟︰ 建立[索引](search-what-is-an-index.md)，[查詢索引](search-query-overview.md)，最後建立並管理您自己使用 Azure 搜尋的搜尋應用程式。

- [Azure 入口網站中建立 Azure 搜尋索引](search-create-index-portal.md)

- [Azure 入口網站中使用搜尋檔案總管 Azure 搜尋索引的查詢](search-explorer.md)

- [設定重新載入資料從其他服務](search-indexer-overview.md)

- [如何使用.NET Azure 搜尋](search-howto-dotnet-sdk.md)

- [分析您的 Azure 搜尋流量](search-traffic-analytics.md)
