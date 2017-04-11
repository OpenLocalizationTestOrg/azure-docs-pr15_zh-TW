<properties
    pageTitle="管理服務匯流排使用 PowerShell |Microsoft Azure"
    description="使用 PowerShell 指令碼管理服務匯流排"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="sethm"/>

# <a name="manage-service-bus-with-powershell"></a>管理服務匯流排使用 PowerShell

## <a name="overview"></a>概觀

Microsoft Azure PowerShell 是您可以使用來控制及自動化的部署及管理您的工作量 Azure 中的指令碼環境。 本文將說明如何使用 PowerShell 佈建和管理服務匯流排項目，例如命名空間、 佇列和事件集線器使用本機的 PowerShell 的 Azure 主控台。

## <a name="prerequisites"></a>必要條件

這份文件之前，您必須具備下列先決條件︰

- Azure 的訂閱。 Azure 是訂閱為基礎的平台。 如需有關如何取得訂閱的詳細資訊，請參閱[購買選項][]]、 [[成員提供][]，或 [[免費試用版][]。

- 使用 PowerShell 的 Azure 電腦。 如需相關指示，請參閱[安裝及設定 PowerShell 的 Azure][]。

- PowerShell 指令碼、 NuGet 套件和.NET Framework 大致的瞭解。

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>包括服務匯流排的.NET 組件的參照

有適用於管理服務匯流排的有限的 PowerShell 指令程式。 佈建不透過現有 cmdlet 公開的項目，您可以使用.NET 用戶端[服務匯流排 NuGet 套件][]中的服務。

首先，請確定指令碼可以找出 NuGet 套件已安裝的**Microsoft.ServiceBus.dll**組件。 才有彈性，指令碼，請執行下列步驟︰

1. 決定叫用的路徑。
2. 穿過路徑，直到找到名為`packages`。 當您安裝 NuGet 套件時，會建立此資料夾。
3. 循環搜尋`packages`名為**Microsoft.ServiceBus.dll**的組件] 資料夾。
4. 讓類型可供使用，以供日後使用，請參考的組件。

這些步驟實作的 PowerShell 指令碼的方式如下︰

```
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error "Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script."
}
```

## <a name="provision-a-service-bus-namespace"></a>佈建服務匯流排命名空間

兩個 PowerShell cmdlet 支援服務匯流排命名空間作業。 而不是.NET SDK 的 Api，您可以使用[取得 AzureSBNamespace][]及[新增 AzureSBNamespace][]。

本範例會建立一些區域變數中指令碼。`$Namespace` and `$Location`.

- `$Namespace`是我們想要使用的服務匯流排命名空間的名稱。
- `$Location`識別資料中心的指令碼佈建新的命名空間。
- `$CurrentNamespace`儲存參考命名空間的指令碼擷取 （或建立）。

在 [實際的指令碼`$Namespace`和`$Location`可以做為參數傳遞。

此組件的指令碼會執行下列工作︰

1. 嘗試擷取服務匯流排命名空間以提供的名稱。
2. 如果找不到命名空間，它會報告找到的內容。
3. 如果找不到命名空間，它會建立命名空間，然後擷取新建立的命名空間。

    ```
    $Namespace = "MyServiceBusNS"
    $Location = "West US"
    
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
    
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Output "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
    }
    else
    {
        Write-Host "The [$Namespace] namespace does not exist."
        Write-Output "Creating the [$Namespace] namespace in the [$Location] region..."
        New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace $false -NamespaceType Messaging
        $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
        Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
    }
    ```

佈建其他服務匯流排項目，請從 SDK 建立[NamespaceManager][]類別的執行個體。
[取得 AzureSBAuthorizationRule][]指令程式可用來擷取授權規則，可用來提供連線字串。 我們會儲存參考`NamespaceManager`執行個體中`$NamespaceManager`變數。 我們會使用`$NamespaceManager`後面的佈建其他實體的指令碼。

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-other-service-bus-entities"></a>佈建其他服務匯流排實體

佈建其他實體，例如佇列、 主題及事件集線器，才能使用[.NET API 的服務][]。 本文僅著重於事件集線器，但其他實體的步驟很類似。 此外，詳細的範例，包括其他實體，參考本文結尾處。

此組件的指令碼會建立四個更多的區域變數。 這些變數用來產生`EventHubDescription`物件。 指令碼會執行下列工作︰

1. 使用`NamespaceManager`物件，檢查 [事件] 中心如果已由`$Path`存在。
2. 如果不存在，建立`EventHubDescription`和傳遞至`NamespaceManager`課程`CreateEventHubIfNotExists`方法。
3. 使用消費者群組之後決定 [事件] 中心內有，建立`ConsumerGroupDescription`和`NamespaceManager`。

    ```
    $Path  = "MyEventHub"
    $PartitionCount = 12
    $MessageRetentionInDays = 7
    $UserMetadata = $null
    $ConsumerGroupName = "MyConsumerGroup"
        
    # Check to see if the Event Hub already exists
    if ($NamespaceManager.EventHubExists($Path))
    {
        Write-Output "The [$Path] event hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
        $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
        $EventHubDescription.PartitionCount = $PartitionCount
        $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
        $EventHubDescription.UserMetadata = $UserMetadata
        $EventHubDescription.Path = $Path
        $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription);
        Write-Output "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
    }
        
    # Create the consumer group if it doesn't exist
    Write-Output "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
    $ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
    $ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
    $NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription);
    Write-Output "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
    ```

## <a name="migrate-a-namespace-to-another-azure-subscription"></a>移轉至另一個 Azure 訂閱的命名空間

以下的命令順序會從一個 Azure 訂閱命名空間移到另一個。 若要執行這項作業，命名空間已必須啟動，且執行的 PowerShell 命令的使用者必須是來源和目標訂閱的管理員。

```
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>後續步驟

本文提供使用 PowerShell 服務匯流排實體提供基本大綱。 任何項目，您可以使用.NET 用戶端文件庫，您也可以執行中的 PowerShell 指令碼。

這些部落格文章有更多詳細的範例可用︰

- [如何建立服務匯流排佇列、 主題及使用 PowerShell 指令碼的訂閱](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [如何建立服務匯流排命名空間和使用 PowerShell 指令碼事件中心](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

部分現成的指令碼也可供下載的︰
- [服務匯流排 PowerShell 指令碼](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[購買選項]: http://azure.microsoft.com/pricing/purchase-options/
[成員優惠]: http://azure.microsoft.com/pricing/member-offers/
[免費試用版]: http://azure.microsoft.com/pricing/free-trial/
[安裝和設定 PowerShell 的 Azure]: ../powershell-install-configure.md
[服務匯流排 NuGet 套件]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[取得 AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[新 AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[取得 AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[服務匯流排.NET API]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.aspx
[NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
