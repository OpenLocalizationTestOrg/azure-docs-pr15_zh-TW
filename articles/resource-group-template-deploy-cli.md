<properties
   pageTitle="部署資源 Azure CLI 與範本 |Microsoft Azure"
   description="使用 Azure 資源管理員和 Azure CLI Azure 部署資源。 資源管理員範本中定義的資源。"
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

# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>部署資源與資源管理員範本和 Azure CLI

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Azure CLI](resource-group-template-deploy-cli.md)
- [入口網站](resource-group-template-deploy-portal.md)
- [REST API](resource-group-template-deploy-rest.md)

本主題說明如何將您的資源部署至 Azure Azure CLI 使用資源管理員範本。  

> [AZURE.TIP] 部署期間偵錯時，發生錯誤的說明，請參閱︰
>
> - 若要深入瞭解取得可協助您的資訊[與 Azure CLI 檢視部署作業](resource-manager-troubleshoot-deployments-cli.md)疑難排解錯誤
> - 若要瞭解如何解決常見的部署錯誤的[疑難排解常見錯誤時部署 Azure 與 Azure 資源管理員資源](resource-manager-common-deployment-errors.md)

本機的檔案或透過 URI 提供的外部檔案，可以是您的範本。 儲存帳戶，位於您的範本，可以限制存取至範本，並提供共用的 access 簽章 (SA) 權杖部署期間。

## <a name="quick-steps-to-deployment"></a>快速步驟來部署

本文將說明所有可用的不同選項您部署期間。 不過，通常您只需要兩個簡單的命令。 若要快速開始使用部署，請使用下列命令︰

    azure group create -n ExampleResourceGroup -l "West US"
    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

若要進一步瞭解可能更適合用於您的狀況的部署選項，請繼續閱讀本文。

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-azure-cli"></a>透過 Azure CLI 部署

如果您先前未使用 Azure CLI 與資源管理員，請參閱[使用 Mac、 Linux，和 Azure 資源管理 Windows Azure CLI](xplat-cli-azure-resource-manager.md)。

1. Azure 帳戶登入。 提供您的認證後,] 命令會傳回您的登入的結果。

        azure login
  
        ...
        info:    login command OK

2. 如果您有多個訂閱，提供您想要使用的部署的訂閱識別碼。

        azure account set <YourSubscriptionNameOrId>

3. 切換到 Azure 資源管理員模組。 您收到新的模式的確認訊息。

        azure config mode arm
   
        info:     New mode is arm

4. 如果您沒有現有的資源群組，請建立資源群組。 提供您的方案，您需要的位置與資源群組的名稱。 您需要提供 [資源] 群組的位置，因為資源群組會將儲存的資源相關的中繼資料。 基於規範，您可能會想要指定的中繼資料的儲存位置。 一般而言，我們建議您指定大部分的資源所在的位置。 使用相同的位置，可以簡化您的範本。

        azure group create -n ExampleResourceGroup -l "West US"

     會傳回新的資源群組的摘要。
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. 驗證您的部署之前執行**azure 群組範本驗證**命令。 當測試部署，提供的參數，相同的方式時執行部署工作 （在下一個步驟所示）。

        azure group template validate -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup

5. 若要部署資源至資源群組，執行下列命令，然後提供必要的參數。 參數會包含您的部署中的名稱、 資源群組、 路徑或 URL 的範本，及您的狀況所需的任何其他參數的名稱。 
   
     您有下列三個選項，可提供參數值︰ 

     1. 使用內嵌的參數和本機的範本。 每個參數是格式︰ `"ParameterName": { "value": "ParameterValue" }`。 下列範例顯示使用逸出字元的參數。

            azure group deployment create -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment

     2. 使用內嵌的參數和範本的連結。

            azure group deployment create --template-uri <LinkToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment

     3. 使用參數檔案。 將範本檔案的相關資訊，請參閱[參數檔案](#parameter-file)。
    
            azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     透過上方的三種方法的其中一個已部署資源之後，您會看到部署的摘要。
  
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        ...
        info:    group deployment create command OK

     若要執行完整的部署，請為 「**完成**設定**模式**。 請小心時使用這個模式，可以不小心刪除不在範本中的資源。

        azure group deployment create --mode Complete -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

6. 如果您想要記錄其他部署相關的資訊可協助您疑難排解部署中的任何錯誤，請使用**偵錯設定**參數。 您可以指定要求內容、 回應內容，或兩者會記錄部署作業。

        azure group deployment create --debug-setting All -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

## <a name="deploy-template-from-storage-with-sas-token"></a>部署與 SA 權杖儲存範本

儲存帳戶並連結到與 SA 權杖部署時，您可以新增您的範本。

> [AZURE.IMPORTANT] 依照下列步驟，包含的範本 blob 是存取帳戶擁有者。 不過，當您建立的 blob 的 SA 權杖，blob 是存取與該 URI 的任何人皆可。 如果其他使用者攔截 URI，使用者就可以存取範本。 使用 SA 權杖是很好的方法限制存取您的範本，但您不應該包含機密資料，例如密碼直接在範本中。

### <a name="add-private-template-to-storage-account"></a>新增私人範本儲存的帳戶

設定儲存帳戶範本下列步驟︰

1. 建立資源群組。

        azure group create -n "ManageGroup" -l "westus"

2. 建立儲存的帳戶。 儲存體帳戶名稱必須在 Azure 是唯一的所以請提供您自己的帳戶的名稱。

        azure storage account create -g ManageGroup -l "westus" --sku-name LRS --kind Storage storagecontosotemplates

3. 設定儲存帳戶和索引鍵的變數。

        export AZURE_STORAGE_ACCOUNT=storagecontosotemplates
        export AZURE_STORAGE_ACCESS_KEY={storage_account_key}

4. 建立容器。 權限會設定為 [**關閉**這表示容器才可存取的擁有者。

        azure storage container create --container templates -p Off 
        
4. 將您的範本新增至容器。

        azure storage blob upload --container templates -f c:\Azure\Templates\azuredeploy.json
        
### <a name="provide-sas-token-during-deployment"></a>部署期間提供 SA 權杖

若要部署私人範本儲存帳戶，擷取 SA 權杖，納入範本 URI。

1. 建立有讀取權限的 SA 權杖 」 和 「 限制存取的到期時間。 設定為允許足夠的時間才能完成部署的到期時間。 擷取完整 URI 包括 SA 權杖的範本。

        expiretime=$(date -I'minutes' --date "+30 minutes")
        fullurl=$(azure storage blob sas create --container templates --blob azuredeploy.json --permissions r --expiry $expiretimetime --json  | jq ".url")

2. 提供包括 SA 權杖 URI 部署範本。

        azure group deployment create --template-uri $fullurl -g ExampleResourceGroup

如需使用 SA 權杖與連結的範本的範例，請參閱[使用連結的範本與 Azure 資源管理員](resource-group-linked-templates.md)。

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>後續步驟
- 部署資源的.NET 用戶端文件庫的範例，請參閱[部署資源使用.NET 文件庫和範本](virtual-machines/virtual-machines-windows-csharp-template.md)。
- 若要在範本中定義的參數，請參閱[撰寫範本](resource-group-authoring-templates.md#parameters)。
- 在不同環境中部署您的方案，請參閱[開發和 Microsoft Azure 中的測試環境](solution-dev-test-environments.md)。
- 如需使用 KeyVault 參考傳遞安全值的詳細資訊，請參閱[傳遞安全部署期間的值](resource-manager-keyvault-parameter.md)。

