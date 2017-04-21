## <a name="how-to-deploy-with-azure-cli"></a>如何透過 Azure CLI 部署

1. 登入您的 Azure 帳戶。

        azure login

  提供您的認證後,] 命令會傳回您的登入的結果。

        ...
        info:    login command OK

2. 如果您有多個訂閱，提供您想要使用的部署的訂閱識別碼。

        azure account set <YourSubscriptionNameOrId>

3. 切換至 Azure 資源管理員模組

        azure config mode arm

   您會收到新的模式的確認訊息。

        info:     New mode is arm

4. 如果您沒有現有的資源群組，建立新的資源群組。 提供您的方案，您需要的位置與資源群組的名稱。

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

5. 若要建立新的部署資源群組，請執行下列命令，然後提供必要的參數。 參數會包含您的部署中的名稱、 建立的範本，您的資源群組、 路徑或 URL 的名稱及您的狀況所需的任何其他參數。

   您有下列選項，可提供參數值︰

   - 使用內嵌的參數和本機的範本。

             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - 使用內嵌的參數和範本的連結。

             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - 使用參數檔案。

             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

  當已部署資源群組時，您會看到部署的摘要。

         info:    Executing command group deployment create
         + Initializing template configurations and parameters
         + Creating a deployment
         ...
         info:    group deployment create command OK


6. 若要取得最新部署的相關資訊。

         azure group log show -l ExampleResourceGroup

7. 若要取得部署失敗的詳細的資訊。

         azure group log show -l -v ExampleResourceGroup
