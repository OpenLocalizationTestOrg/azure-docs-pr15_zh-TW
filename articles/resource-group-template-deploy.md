<properties
   pageTitle="部署資源 PowerShell 與範本 |Microsoft Azure"
   description="使用 Azure 資源管理員和 PowerShell 的 Azure Azure 部署資源。 資源管理員範本中定義的資源。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>部署資源管理員範本與 PowerShell 的 Azure 資源

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Azure CLI](resource-group-template-deploy-cli.md)
- [入口網站](resource-group-template-deploy-portal.md)
- [REST API](resource-group-template-deploy-rest.md)

本主題說明如何使用 PowerShell 的 Azure 與資源管理員範本至 Azure 部署資源。  

> [AZURE.TIP] 部署期間偵錯時，發生錯誤的說明，請參閱︰
>
> - [使用 PowerShell 的 Azure 檢視部署作業](resource-manager-troubleshoot-deployments-powershell.md)，瞭解如何取得資訊，可協助您疑難排解錯誤
> - 若要瞭解如何解決常見的部署錯誤的[疑難排解常見錯誤時部署 Azure 與 Azure 資源管理員資源](resource-manager-common-deployment-errors.md)

本機的檔案或透過 URI 提供的外部檔案，可以是您的範本。 儲存帳戶，位於您的範本，可以限制存取至範本，並提供共用的 access 簽章 (SA) 權杖部署期間。

## <a name="quick-steps-to-deployment"></a>快速步驟來部署

本文將說明所有可用的不同選項您部署期間。 不過，通常您只需要兩個簡單的命令。 若要快速開始使用部署，請使用下列命令︰

    New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
    New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

若要進一步瞭解可能更適合用於您的狀況的部署選項，請繼續閱讀本文。

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-powershell"></a>使用 PowerShell 部署

1. Azure 帳戶登入。

        Add-AzureRmAccount

     會傳回您的帳戶的摘要。

        Environment : AzureCloud
        Account     : someone@example.com
        ...

2. 如果您有多個訂閱，提供您想要使用的部署**設定 AzureRmContext** ] 命令的訂閱識別碼。 

        Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

3. 一般而言，在部署新的範本時，您會想要建立包含資源的資源群組。 如果您有您想要部署至現有資源群組，您可以略過此步驟，並使用該資源群組。 

     若要建立資源群組，提供的名稱及資源群組的位置。 您需要提供 [資源] 群組的位置，因為資源群組會將儲存的資源相關的中繼資料。 基於規範，您可能會想要指定的中繼資料的儲存位置。 一般而言，我們建議您指定大部分的資源所在的位置。 使用相同的位置，可以簡化您的範本。

        New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   
     會傳回新的資源群組的摘要。
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
             Actions  NotActions
             =======  ==========
             *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

4. 執行您的部署之前，您可以驗證您的部署設定。 **測試 AzureRmResourceGroupDeployment**指令程式可讓您建立的實際資源之前，先找出問題。 下列範例會示範如何驗證部署。

        Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

5. 若要部署資源至資源群組中，執行**新增 AzureRmResourceGroupDeployment**命令，然後提供必要的參數。 參數會包含您的部署中的名稱、 建立的範本，您的資源群組、 路徑或 URL 的名稱及您的狀況所需的任何其他參數。 如果未指定**模式**參數，則會使用**累加**的預設值。 若要執行完整的部署，請為 「**完成**設定**模式**。 請小心時使用完整的模式，可以不小心刪除不在範本中的資源。

     若要部署本機的範本，請使用**TemplateFile**參數︰

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

     若要部署外部的範本，請使用**TemplateUri**參數︰

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate>
   
     您有下列選項，可提供參數值︰ 
   
     1. 使用內嵌的參數。

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -myParameterName "parameterValue"

     2. 使用參數物件。

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterObject $parameters

     3. 使用本機參數檔案。 將範本檔案的相關資訊，請參閱[參數檔案](#parameter-file)。

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

     4. 使用外部參數檔案。 將範本檔案的相關資訊，請參閱[參數檔案](#parameter-file)。 

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate> -TemplateParameterUri <LinkToParameterFile>

        當您使用外部參數檔案時，您無法傳遞其他的值是內嵌或從本機檔案。 如需詳細資訊，請參閱[參數的優先順序](#parameter-precendence)。

     已部署資源之後，您會看到部署的摘要。

        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleResourceGroup
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2015 7:00:27 PM
        Mode              : Incremental
        ...

     如果您的範本中的 PowerShell 命令，包括具有相同名稱的其中一個參數的參數，系統會提示您提供的參數值。 從您的範本的參數會包含的後置**FromTemplate**。 例如，參數中名為**ResourceGroupName**您範本與**ResourceGroupName**參數[新增 AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx)指令程式中的衝突。 系統會提示您提供**ResourceGroupNameFromTemplate**的值。 一般而言，您應避免混淆，不使用相同的名稱以用於部署作業的參數命名參數。

6. 如果您想要記錄其他部署相關的資訊可協助您疑難排解部署中的任何錯誤，請使用**DeploymentDebugLogLevel**參數。 您可以指定要求內容、 回應內容，或兩者會記錄部署作業。

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
        
     如需有關如何使用此偵錯內容疑難排解部署的詳細資訊，請參閱[使用 PowerShell 的 Azure 疑難排解資源群組部署](resource-manager-troubleshoot-deployments-powershell.md)。

## <a name="deploy-template-from-storage-with-sas-token"></a>部署與 SA 權杖儲存範本

儲存帳戶並連結到與 SA 權杖部署時，您可以新增您的範本。

> [AZURE.IMPORTANT] 依照下列步驟，包含的範本 blob 是存取帳戶擁有者。 不過，當您建立的 blob 的 SA 權杖，blob 是存取與該 URI 的任何人皆可。 如果其他使用者攔截 URI，使用者就可以存取範本。 使用 SA 權杖是很好的方法限制存取您的範本，但您不應該包含機密資料，例如密碼直接在範本中。

### <a name="add-private-template-to-storage-account"></a>新增私人範本儲存的帳戶

設定儲存帳戶範本下列步驟︰

1. 建立資源群組。

        New-AzureRmResourceGroup -Name ManageGroup -Location "West US"

2. 建立儲存的帳戶。 儲存體帳戶名稱必須在 Azure 是唯一的所以請提供您自己的帳戶的名稱。

        New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates -Type Standard_LRS -Location "West US"

3. 設定目前的儲存空間帳戶。

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

4. 建立容器。 權限會設定為 [**關閉**這表示容器才可存取的擁有者。

        New-AzureStorageContainer -Name templates -Permission Off
        
5. 將您的範本新增至容器。

        Set-AzureStorageBlobContent -Container templates -File c:\Azure\Templates\azuredeploy.json
        
### <a name="provide-sas-token-during-deployment"></a>部署期間提供 SA 權杖

若要部署私人範本儲存帳戶，擷取 SA 權杖，納入範本 URI。

1. 如果您已變更的目前儲存帳戶，將包含您的範本，目前儲存帳戶。

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

2. 建立有讀取權限的 SA 權杖 」 和 「 限制存取的到期時間。 擷取完整 URI 包括 SA 權杖的範本。

        $templateuri = New-AzureStorageBlobSASToken -Container templates -Blob azuredeploy.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

3. 提供包括 SA 權杖 URI 部署範本。

        New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateuri

如需使用 SA 權杖與連結的範本的範例，請參閱[使用連結的範本與 Azure 資源管理員](resource-group-linked-templates.md)。

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="parameter-precedence"></a>參數的優先順序

您可以使用內嵌的參數和參數本機檔案以相同的部署操作。 例如，您可以指定本機參數檔案中的一些值，並部署期間新增內嵌其他數值。 如果您在本機的參數檔案和內嵌參數提供的值，直接採用值。

不過，您無法使用內嵌參數與參數外部檔案。 當您在**TemplateParameterUri**參數中指定參數檔案時，則會忽略文字間的所有參數。 您必須提供外部檔案中的所有參數值。 如果範本包含一個機密的值，不能包含參數檔案中，新增該值到金鑰保存庫及參照金鑰保存庫，在您外部參數的檔案，或以動態方式提供所有參數值內嵌。

如需使用 KeyVault 參考傳遞安全值的詳細資訊，請參閱[傳遞安全部署期間的值](resource-manager-keyvault-parameter.md)。

## <a name="next-steps"></a>後續步驟
- 部署資源的.NET 用戶端文件庫的範例，請參閱[部署資源使用.NET 文件庫和範本](virtual-machines/virtual-machines-windows-csharp-template.md)。
- 若要在範本中定義的參數，請參閱[撰寫範本](resource-group-authoring-templates.md#parameters)。
- 在不同環境中部署您的方案，請參閱[開發和 Microsoft Azure 中的測試環境](solution-dev-test-environments.md)。

