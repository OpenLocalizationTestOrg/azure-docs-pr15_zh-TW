<properties 
    pageTitle="部署及管理通知集線器使用 PowerShell" 
    description="如何建立及管理通知集線器自動化使用 PowerShell" 
    services="notification-hubs" 
    documentationCenter="" 
    authors="ysxu" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="notification-hubs" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="powershell" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>部署及管理通知集線器使用 PowerShell

##<a name="overview"></a>概觀

本文將示範如何用來建立和管理 Azure 通知集線器使用 PowerShell。 本主題會顯示下列一般的自動化工作。

+ 建立通知中心
+ 設定認證

如果您需要建立新的服務匯流排命名空間，您的通知集線器，請參閱[使用 PowerShell 管理服務匯流排](../service-bus-messaging/service-bus-powershell-how-to-provision.md)。

管理通知集線器不支援直接隨附 Azure PowerShell cmdlet。 從 PowerShell 最好的方法是參考 Microsoft.Azure.NotificationHubs.dll 組件。 [Microsoft Azure 通知集線器 NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)散發組件。


## <a name="prerequisites"></a>必要條件

這份文件之前，您必須具備下列項目︰

- Azure 的訂閱。 Azure 是訂閱為基礎的平台。 如需有關如何取得訂閱的詳細資訊，請參閱[購買選項]]、 [[成員提供]，或 [[免費試用版]。

- 使用 PowerShell 的 Azure 電腦。 如需相關指示，請參閱[安裝及設定 PowerShell 的 Azure]。

- PowerShell 指令碼、 NuGet 套件和.NET Framework 大致的瞭解。


## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>包括服務匯流排的.NET 組件的參照

管理 Azure 通知集線器尚未隨附於 PowerShell 的 Azure 中的 PowerShell 指令程式。 佈建通知集線器，您可以使用[Microsoft Azure 通知集線器 NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)中所提供的.NET 用戶端。

首先，請確定您的指令碼可以找出已安裝為 NuGet 套件 Visual Studio 專案中的**Microsoft.Azure.NotificationHubs.dll**組件。 才有彈性，指令碼，請執行下列步驟︰

1. 決定叫用的路徑。
2. 穿過路徑，直到找到名為`packages`。 當您安裝的 Visual Studio 專案 NuGet 套件時，會建立此資料夾。
3. 循環搜尋`packages`名為**Microsoft.Azure.NotificationHubs.dll**的組件] 資料夾。
4. 讓類型可供使用，以供日後使用，請參考的組件。

這些步驟實作的 PowerShell 指令碼的方式如下︰

``` powershell

try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>建立 NamespaceManager 類別

佈建通知集線器，請從 SDK 建立[NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx)類別的執行個體。 

您可以使用 PowerShell 的 Azure 隨附[取得 AzureSBAuthorizationRule] cmdlet 來擷取授權規則，可用來提供連線字串。 我們會儲存參考`NamespaceManager`執行個體中`$NamespaceManager`變數。 我們會使用`$NamespaceManager`佈建通知中心。

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```


## <a name="provisioning-a-new-notification-hub"></a>佈建新的通知中心 

佈建新的通知中心，可以使用[.NET API 通知集線器]。

在這個部分中的指令碼您設定四個區域的變數。 

1. `$Namespace`︰ 設定為 [命名空間名稱要建立通知中心的位置。
2. `$Path`︰ 將此路徑為新的通知中心的名稱。  例如，「 MyHub 」。    
3. `$WnsPackageSid`︰ 設定為 [SID 封裝為您的 Windows 應用程式從[Windows 開發人員中心](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409)。
4. `$WnsSecretkey`︰ 設定為 [私人索引鍵的 Windows 應用程式從[Windows 開發人員中心](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409)。

這些變數用來連線到您的命名空間並建立新的通知中心處理 for Windows 應用程式使用 WNS 認證的 Windows 通知服務 (WNS) 通知設定。 取得套件資訊 SID 和私密金鑰看到，請[通知集線器快速入門](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)教學課程。 

+ 使用指令碼的程式碼片段`NamespaceManager`物件以查看 [通知] 中心是否已由`$Path`存在。

+ 如果不存在，會建立指令碼`NotificationHubDescription`WNS 與認證與傳遞至`NamespaceManager`課程`CreateNotificationHub`方法。

``` powershell

$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```




## <a name="additional-resources"></a>其他資源

- [管理服務匯流排使用 PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [如何建立服務匯流排佇列、 主題及使用 PowerShell 指令碼的訂閱](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [如何建立服務匯流排命名空間和使用 PowerShell 指令碼事件中心](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

部分現成的指令碼也可供下載的︰
- [服務匯流排 PowerShell 指令碼](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)
 

[購買選項]: http://azure.microsoft.com/pricing/purchase-options/
[成員優惠]: http://azure.microsoft.com/pricing/member-offers/
[免費試用版]: http://azure.microsoft.com/pricing/free-trial/
[安裝和設定 PowerShell 的 Azure]: ../powershell-install-configure.md
[通知集線器的 API]: https://msdn.microsoft.com/library/azure/mt414893.aspx
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[取得 AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
 
