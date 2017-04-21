## <a name="how-to-deploy-with-powershell"></a>如何使用 PowerShell 部署

1. 登入您的 Azure 帳戶。

          Add-AzureAccount

   提供您的認證後,] 命令會傳回您的帳戶的相關資訊。

          Id                             Type       ...
          --                             ----    
          example@contoso.com            User       ...   

2. 如果您有多個訂閱，提供您想要使用的部署的訂閱識別碼。 

          Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. 切換到 Azure 資源管理員模組。

          Switch-AzureMode AzureResourceManager

4. 如果您沒有現有的資源群組，建立新的資源群組。 提供您的方案，您需要的位置與資源群組的名稱。

        New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"

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

5. 若要建立新的部署資源群組，請執行**新增 AzureResourceGroupDeployment**命令，然後提供必要的參數。 參數會包含您的部署中的名稱、 建立的範本，您的資源群組、 路徑或 URL 的名稱及您的狀況所需的任何其他參數。 
   
   您有下列選項，可提供參數值︰ 
   
   - 使用內嵌的參數。

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

   - 使用參數物件。

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

   - 使用參數檔案。

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

  當已部署資源群組時，您會看到部署的摘要。

             DeploymentName    : ExampleDeployment
             ResourceGroupName : ExampleResourceGroup
             ProvisioningState : Succeeded
             Timestamp         : 4/14/2015 7:00:27 PM
             Mode              : Incremental
             ...

6. 若要取得部署失敗的相關資訊。

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed

7. 若要取得部署失敗的詳細的資訊。

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput
