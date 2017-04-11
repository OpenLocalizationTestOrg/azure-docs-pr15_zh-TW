<properties 
    pageTitle="Azure PowerShell 與資源管理員 |Microsoft Azure" 
    description="若要將多個資源部署至 Azure 資源群組使用 PowerShell 的 Azure 簡介。" 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="powershell" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/18/2016" 
    ms.author="tomfitz"/>

# <a name="using-azure-powershell-with-azure-resource-manager"></a>使用 Azure PowerShell 的 Azure 資源管理員

> [AZURE.SELECTOR]
- [入口網站](azure-portal/resource-group-portal.md) 
- [Azure CLI](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [REST API](resource-manager-rest-api.md)


Azure 資源管理員實作 Azure 資源生命週期控制項的新方法。 而非建立和管理個別的資源，請先正在想像整個方案，例如部落格、 影像中心、 SharePoint 入口網站或 wiki。 您可以使用範本，解決方案的宣告式表示法，以定義包含的所有資源需要支援解決方案的資源群組。 然後，您部署及管理該資源單位為群組邏輯。 

在本教學課程中，您可以瞭解如何使用 PowerShell 的 Azure 與 Azure 資源管理員。 它會引導您完成程序部署解決方案，並使用該方案。 您會使用 PowerShell 的 Azure 和資源管理員範本部署︰

- SQL server-裝載資料庫
- SQL 資料庫的資料儲存
- 防火牆規則-允許連線至資料庫的 web 應用程式
- 應用程式服務計劃-定義功能及 web 應用程式的成本
- 網站-執行 web 應用程式
- 網站組態-儲存資料庫的連線字串 
- 通知規則-監控效能與錯誤
- 應用程式的深入見解-自動調整設定

## <a name="prerequisites"></a>必要條件

若要完成此教學課程中，您需要︰

- Azure 帳戶
  + 您可以[開啟 Azure 帳戶免費](/pricing/free-trial/?WT.mc_id=A261C142F)︰ 取得貸項總計試用付費 Azure 服務，您可以使用和即使使用這些之後最多，您可以將該帳戶，然後使用免費 Azure 服務，例如網站。 您的信用卡不需要付費，除非您明確地變更您的設定，並要求付費。
  
  + 您可以[啟動 MSDN 訂閱者優惠](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)︰ 您的 MSDN 訂閱可讓您貸項總計付費 Azure 服務，您可以使用每個月。
- Azure PowerShell 1.0。 此版本，以及如何將其安裝的相關資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](powershell-install-configure.md)。

本教學課程為設計 PowerShell 的新手，但假設您瞭解基本概念，例如模組、 cmdlet 和工作階段。

## <a name="get-help-for-cmdlets"></a>取得說明的 cmdlet

若要取得您在本教學課程中看到任何 cmdlet 的詳細的說明，請使用取得說明指令程式。 

    Get-Help <cmdlet-name> -Detailed

例如，取得 AzureRmResource 指令程式的相關說明，請輸入︰

    Get-Help Get-AzureRmResource -Detailed

若要在資源模組中使用說明概要取得 cmdlet 的清單，請輸入︰ 

    Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

輸出看起來類似下列摘錄︰

    Name                                   Synopsis
    ----                                   --------
    Find-AzureRmResource                   Searches for resources using the specified parameters.
    Find-AzureRmResourceGroup              Searches for resource group using the specified parameters.
    Get-AzureRmADGroup                     Filters active directory groups.
    Get-AzureRmADGroupMember               Get a group members.
    ...

若要取得指令程式的完整的說明，輸入格式命令︰

    Get-Help <cmdlet-name> -Full
  
## <a name="login-to-your-azure-account"></a>Azure 帳戶登入

前處理您的方案，您必須登入您的帳戶。

Azure 帳戶登入，使用 [**新增 AzureRmAccount**指令程式。

    Add-AzureRmAccount

指令程式會提示您登入認證，Azure 帳戶。 登入之後，下載您的帳戶設定，使其使用 PowerShell 的 Azure。 

帳戶設定後過期，，因此您需要重新整理其有時候。 若要重新整理帳戶設定，請再次執行**新增 AzureRmAccount** 。 

>[AZURE.NOTE] 資源管理員模組需要新增 AzureRmAccount。 無法滿足所需的發佈的設定檔。     

如果您有多個訂閱，請提供您想要使用的部署**設定 AzureRmContext** cmdlet 的訂閱識別碼。

    Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

## <a name="create-a-resource-group"></a>建立資源群組

部署之前的任何資源至您的訂閱，您必須建立包含資源資源群組。 

若要建立資源群組，請使用**新增 AzureRmResourceGroup**指令程式。

命令會使用指定的名稱，指定其位置的 [資源群組與**位置**參數**名稱**參數。 根據我們發現前一節中，我們會使用 「 西美國 」 的位置。

    New-AzureRmResourceGroup -Name TestRG1 -Location "West US"
    
輸出會類似︰

    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

資源群組已順利建立。

## <a name="deploy-your-solution"></a>部署方案

本主題不會顯示您如何建立您的範本或討論範本的結構。 該資訊，請參閱[撰寫 Azure 資源管理員範本](resource-group-authoring-templates.md)與[資源管理員範本逐步解說](resource-manager-template-walkthrough.md)。 您會部署從[Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)預先定義的[佈建與 SQL 資料庫在 Web 應用程式](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/)範本。

您有資源群組，而且您有您的範本，您已準備好要部署在您的範本以 [資源] 群組中定義的基礎結構。 您部署資源的 [**新增 AzureRmResourceGroupDeployment**指令程式。 範本會指定許多的預設值，我們將使用，因此您不需要提供這些參數值。 基本語法看起來像︰

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -administratorLogin exampleadmin -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

指定 [資源] 群組和範本的位置。 如果您的範本是本機的檔案，您會使用**-TemplateFile**參數，並指定範本的路徑。 您可以設定**-模式**參數**遞**或**完成**。 根據預設，資源管理員，請執行部署; 期間的累加更新因此，不需要設定**-模式**您希望**遞**。 若要瞭解這些部署模式之間的差異，請參閱[部署具有 Azure 資源管理員範本的應用程式](resource-group-template-deploy.md)。 

###<a name="dynamic-template-parameters"></a>動態範本參數

如果您是使用 PowerShell 熟悉，您知道您可以輸入減號 （-），然後按 [TAB 鍵循環 cmdlet 可用的參數。 相同的功能也適用於您在您的範本中定義的參數。 一旦您輸入範本名稱，cmdlet 擷取範本剖析，與範本參數新增命令以動態方式。 如此可讓您很容易指定範本參數值。

當您輸入命令時，系統會提示您遺失強制參數**administratorLoginPassword**。 然後，當您輸入密碼，也隱藏的安全的字串值。 此策略不提供的密碼以純文字的風險。

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

如果範本包含名稱開頭為符合其中一個命令部署的範本 （例如，包括您在[新增 AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx) cmdlet **ResourceGroupName**參數相同的範本] 中名為**ResourceGroupName**參數） 中參數的參數，系統會提示您提供的參數使用的後置**FromTemplate** （例如**ResourceGroupNameFromTemplate**) 的值。 一般而言，您應避免混淆，不使用相同的名稱以用於部署作業的參數命名參數。

命令會執行，並建立資源時，會傳回的郵件。 最後，您會看到您的部署中的結果。

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 4/11/2016 7:26:11 PM
    Mode              : Incremental
    TemplateLink      :
                Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json
                ContentVersion : 1.0.0.0
    Parameters        :
                Name             Type                       Value
                ===============  =========================  ==========
                skuName          String                     F1
                skuCapacity      Int                        1
                administratorLogin  String                  exampleadmin
                administratorLoginPassword  SecureString
                databaseName     String                     sampledb
                collation        String                     SQL_Latin1_General_CP1_CI_AS
                edition          String                     Basic
                maxSizeBytes     String                     1073741824
                requestedServiceObjectiveName  String       Basic

    Outputs           :
                Name             Type                       Value
                ===============  =========================  ==========
                siteUri          String                     websites5wdai7p2k2g4.azurewebsites.net
                sqlSvrFqdn       String                     sqlservers5wdai7p2k2g4.database.windows.net
                    
    DeploymentDebugLogLevel :

只需要幾個步驟，我們會建立並部署複雜網站所需的資源。 

### <a name="log-debug-information"></a>記錄偵錯資訊

在部署範本時，您可以在時執行**新增 AzureRmResourceGroupDeployment**指定**-DeploymentDebugLogLevel**參數記錄邀請及回應的其他資訊。 這項資訊可協助您疑難排解部署錯誤。 預設值為 [**無**表示沒有要求或回應的內容登入。 您可以指定記錄邀請、 回應，或兩者的內容。  如需有關疑難排解部署與記錄偵錯資訊的詳細資訊，請參閱[使用 PowerShell 的 Azure 疑難排解資源群組部署](resource-manager-troubleshoot-deployments-powershell.md)。 下列範例會記錄要求內容和回應內容以供部署。

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -DeploymentDebugLogLevel All -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

> [AZURE.NOTE] 設定 DeploymentDebugLogLevel 參數，請仔細考慮部署期間傳遞中的資訊的類型。 以要求或回應的記錄資訊，您可能無法公開機密資料擷取完成部署作業。 


## <a name="get-information-about-your-resource-groups"></a>取得資源群組的相關資訊

建立之後資源群組，您可以使用資源管理員模組中的指令程式來管理您的資源群組。

- 若要取得您的訂閱中的資源群組，請使用**取得 AzureRmResourceGroup**指令程式︰

        Get-AzureRmResourceGroup -ResourceGroupName TestRG1
    
    傳回的下列資訊︰

        ResourceGroupName : TestRG1
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
        
        ...

    如果您不指定資源群組名稱，指令程式會傳回的所有資源群組中您的訂閱。

- 若要取得資源的資源群組中，使用 [**尋找 AzureRmResource** cmdlet 和其**ResourceGroupNameContains**參數。 不含參數，尋找 AzureRmResource 取得 Azure 訂閱中的所有資源。

        Find-AzureRmResource -ResourceGroupNameContains TestRG1
    
     傳回的資源的清單格式，例如︰
        
        Name              : sqlservers5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/sqlservers5wdai7p2k2g4
        ResourceName      : sqlservers5wdai7p2k2g4
        ResourceType      : Microsoft.Sql/servers
        Kind              : v2.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
        Tags              : {System.Collections.Hashtable}
        ...
            
- 您可以使用 [標記以邏輯方式組織您的訂閱中的資源，並可擷取透過**尋找 AzureRmResource**和**尋找 AzureRmResourceGroup** cmdlet 的資源。

        Find-AzureRmResource -TagName displayName -TagValue Website

        Name              : webSites5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/webSites5wdai7p2k2g4
        ResourceName      : webSites5wdai7p2k2g4
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
      There is much more you can do with tags. For more information, see [Using tags to organize your Azure resources](resource-group-using-tags.md).

## <a name="add-to-a-resource-group"></a>新增到資源群組

若要將資源新增至 [資源] 群組中，您可以使用 [**新增 AzureRmResource**指令程式。 不過，這種方式新增資源可能會導致未來混淆，因為您的範本中不存在的新資源。 如果您重新部署舊的範本，您想要部署不完整的解決方案。 如果您經常要部署，您會發現您更輕鬆且更可靠將新的資源新增至您的範本，並將其重新部署。

## <a name="move-a-resource"></a>移動資源

您可以將現有的資源移到新的資源群組。 如需範例，請參閱[移動資源，新的資源群組或訂閱。](resource-group-move-resources.md)

## <a name="export-template"></a>匯出範本

針對現有的資源群組 （透過 PowerShell 或其他方法，例如入口網站的部署），您可以檢視 [資源] 群組的資源管理員範本。 匯出範本提供兩個優點︰

1. 您可以輕鬆地自動化未來部署解決方案的因為所有的基礎結構定義在範本中。

2. 您可以熟悉範本語法尋找在 JavaScript 物件標記法 (JSON)，表示您的方案。

透過 PowerShell，您可以產生範本，表示目前狀態的資源的群組，或擷取特定的部署所用的範本。

匯出資源群組的範本時，很有幫助您資源] 群組中，對所做的變更，需要以擷取其目前狀態的 JSON 表示。 不過，產生的範本包含最小數字的參數和沒有變數。 大部分的範本中的值會硬式編碼。 部署之前產生的範本，您可能會想要將多個值轉換成參數，因此您可以自訂不同環境中部署。

當您需要檢視時所用部署資源的實際範本時，匯出在特定的部署的範本是很有幫助。 範本會包含所有參數和定義原始部署的變數。 不過，如果您組織中的人員具有外部定義在範本中的 [資源] 群組中進行變更，此範本會代表資源群組的目前狀態。

> [AZURE.NOTE] 匯出範本 」 功能在 [預覽]，而不是所有的資源類型目前支援匯出範本。 嘗試匯出為範本，您可能會看到錯誤的一些資源未匯出。 如有需要以手動方式之後下載定義這些資源在範本中。

###<a name="export-template-from-resource-group"></a>匯出資源] 群組中的範本

若要檢視資源群組的範本，請執行**匯出 AzureRmResourceGroup**指令程式。

    Export-AzureRmResourceGroup -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\TestRG1.json
    
###<a name="download-template-from-deployment"></a>從部署下載範本

若要下載特定的部署使用的範本，請執行**儲存 AzureRmResourceGroupDeploymentTemplate**指令程式。

    Save-AzureRmResourceGroupDeploymentTemplate -DeploymentName azuredeploy -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\azuredeploy.json

## <a name="delete-resources-or-resource-group"></a>刪除資源或資源群組

- 若要刪除 [資源] 群組中的資源，請使用**移除 AzureRmResource**指令程式。 這個 cmdlet 刪除資源，但並不會刪除資源群組。

    這個命令 TestRG1 資源群組中移除 TestSite 網站。

        Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01

- 若要刪除資源群組，請使用**移除 AzureRmResourceGroup**指令程式。 資源群組和它的資源，則會刪除這個指令程式。

        Remove-AzureRmResourceGroup -Name TestRG1
        
    系統要求您確認刪除。
        
        Confirm
        Are you sure you want to remove resource group 'TestRG1'
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

## <a name="deployment-script"></a>部署指令碼

舊版的部署範例，顯示個別的 cmdlet 本主題中所需 Azure 部署資源。 下列範例會建立資源] 群組中，並部署資源的部署指令碼。

    <#
      .SYNOPSIS
      Deploys a template to Azure
      
      .DESCRIPTION
      Deploys an Azure Resource Manager template

      .PARAMETER subscriptionId
      The subscription id where the template will be deployed.

      .PARAMETER resourceGroupName
      The resource group where the template will be deployed. Can be the name of an existing or a new resource group.

      .PARAMETER resourceGroupLocation
      Optional, a resource group location. If specified, will try to create a new resource group in this location. If not specified, assumes resource group is existing.

      .PARAMETER deploymentName
      The deployment name.

      .PARAMETER templateFilePath
      Optional, path to the template file. Defaults to template.json.

      .PARAMETER parametersFilePath
      Optional, path to the parameters file. Defaults to parameters.json. If file is not found, will prompt for parameter values based on template.
    #>

    param(
      [Parameter(Mandatory=$True)]
      [string]
      $subscriptionId,

      [Parameter(Mandatory=$True)]
      [string]
      $resourceGroupName,

      [string]
      $resourceGroupLocation,

      [Parameter(Mandatory=$True)]
      [string]
      $deploymentName,

      [string]
      $templateFilePath = "template.json",

      [string]
      $parametersFilePath = "parameters.json"
    )

    #******************************************************************************
    # Script body
    # Execution begins here 
    #******************************************************************************
    $ErrorActionPreference = "Stop"

    # sign in
    Write-Host "Logging in...";
    Add-AzureRmAccount;

    # select subscription
    Write-Host "Selecting subscription '$subscriptionId'";
    Set-AzureRmContext -SubscriptionID $subscriptionId;

    #Create or check for existing resource group
    $resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
    if(!$resourceGroup)
    {
      Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
      if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
      }
      Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
      New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
    }
    else{
      Write-Host "Using existing resource group '$resourceGroupName'";
    }

    # Start the deployment
    Write-Host "Starting deployment...";
    if(Test-Path $parametersFilePath) {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
    } else {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
    }

## <a name="next-steps"></a>後續步驟

- 若要瞭解如何建立資源管理員範本，請參閱[撰寫 Azure 資源管理員範本](./resource-group-authoring-templates.md)。
- 若要瞭解部署範本，請參閱[部署具有 Azure 資源管理員範本的應用程式](./resource-group-template-deploy.md)。
- 部署專案的詳細範例，請參閱[Azure 中預測部署 microservices](app-service-web/app-service-deploy-complex-application-predictably.md)。
- 若要瞭解無法部署的疑難排解，請參閱[Azure 中的疑難排解資源群組部署](./resource-manager-troubleshoot-deployments-powershell.md)。

