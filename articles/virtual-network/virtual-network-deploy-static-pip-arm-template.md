<properties
   pageTitle="部署資源管理員] 中使用範本固定公用 ip VM |Microsoft Azure"
   description="瞭解如何部署 Vm 固定的公用 ip 資源管理員] 中使用的範本"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="deploy-a-vm-with-a-static-public-ip-using-a-template"></a>部署 VM 固定的公用 ip 使用範本

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]傳統的部署模型。

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="public-ip-resources-in-a-template-file"></a>範本檔案中的公用 IP 資源

您可以檢視及下載[的範例範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json)。

以下的章節會顯示根據上述情況的公用 IP 資源的定義。

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[variables('webVMSetting').pipName]",
        "location": "[variables('location')]",
        "properties": {
          "publicIPAllocationMethod": "Static"
        },
        "tags": {
          "displayName": "PublicIPAddress - Web"
        }
      },

請注意**publicIPAllocationMethod**屬性，為*靜態*。 *動態*（預設值） 或*靜態*，可使用此屬性。 將它設定為 [永遠不會變更指派的公用 IP 位址的靜態保證。

以下的章節會顯示與網路介面關聯的公用 IP 位址。

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "[variables('webVMSetting').nicName]",
        "location": "[variables('location')]",
        "tags": {
          "displayName": "NetworkInterface - Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Network/publicIPAddresses/', variables('webVMSetting').pipName)]",
          "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
          "ipConfigurations": [
            {
              "name": "ipconfig1",
              "properties": {
                "privateIPAllocationMethod": "Static",
                "privateIPAddress": "[variables('webVMSetting').ipAddress]",
                "publicIPAddress": {
                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('webVMSetting').pipName)]"
                },
                "subnet": {
                  "id": "[variables('frontEndSubnetRef')]"
                }
              }
            }
          ]
        }
      },

請注意指向具名**variables('webVMSetting').pipName**資源**識別碼** **publicIPAddress**屬性。 這是上方所顯示的公用 IP 資源的名稱。

最後，上述的網路介面會列在 VM 所建立的**networkProfile**屬性。

      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('webVMSetting').nicName)]"
          }
        ]
      }

## <a name="deploy-the-template-by-using-click-to-deploy"></a>藉由按一下部署部署範本

公用存放庫中提供的範例範本會使用參數檔案包含的預設值用來產生上述的情形。 若要部署部署使用按一下此範本，按一下 [**部署至 Azure** Readme.md 的檔案中，[使用靜態 PIP VM](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/03-Static-public-IP)範本。 如果有需要，取代預設的參數值，然後輸入空白的參數值。  依照入口網站中的指示，建立虛擬機器靜態的公用 IP 位址。

## <a name="deploy-the-template-by-using-powershell"></a>使用 PowerShell 來部署範本

若要部署使用 PowerShell 您下載的範本，請遵循下列步驟。

1. 如果您從未使用 PowerShell 的 Azure，瞭解[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) ，並依照步驟 1 至 3 中的指示進行。

2. 在 PowerShell 主控台中，執行如有必要，建立新的 [資源] 群組中，**新增 AzureRmResourceGroup**指令程式。 如果您已經建立的資源群組，請移至步驟 3。

        New-AzureRmResourceGroup -Name PIPTEST -Location westus

    預期的輸出︰

        ResourceGroupName : PIPTEST
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/StaticPublicIP

3. 在 PowerShell 主控台中，執行**新增 AzureRmResourceGroupDeployment** cmdlet 部署範本。

        New-AzureRmResourceGroupDeployment -Name DeployVM -ResourceGroupName PIPTEST `
            -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json `
            -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json

    預期的輸出︰

        DeploymentName    : DeployVM
        ResourceGroupName : PIPTEST
        ProvisioningState : Succeeded
        Timestamp         : <Deployment date> <Deployment time>
        Mode              : Incremental
        TemplateLink      :
                            Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/mas
                            ter/IaaS-Story/03-Static-public-IP/azuredeploy.json
                            ContentVersion : 1.0.0.0

        Parameters        :
                            Name                      Type                       Value     
                            ========================  =========================  ==========
                            vnetName                  String                     WTestVNet
                            vnetPrefix                String                     192.168.0.0/16
                            frontEndSubnetName        String                     FrontEnd  
                            frontEndSubnetPrefix      String                     192.168.1.0/24
                            storageAccountNamePrefix  String                     iaasestd  
                            stdStorageType            String                     Standard_LRS
                            osType                    String                     Windows   
                            adminUsername             String                     adminUser
                            adminPassword             SecureString                         

        Outputs           :

## <a name="deploy-the-template-by-using-the-azure-cli"></a>使用 Azure CLI 部署範本

若要使用 Azure CLI 部署範本，請遵循下列步驟。

1. 如果您從未使用 Azure CLI，請遵循[安裝和設定 Azure CLI](../xplat-cli-install.md)文件中的步驟，然後將 CLI 連線至您的訂閱[Azure 訂閱從 Azure 命令列介面 (Azure CLI) 連線](../xplat-cli-connect.md)文件的 [驗證互動的方式使用 azure 登入] 區段中的步驟。
2. 執行**azure config 模式**命令以切換到資源管理員模式，如下所示。

        azure config mode arm

    以下是預期的輸出上方的命令︰

        info:    New mode is arm

3. 開啟[參數檔案](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json)，選取其內容，並將其儲存到您的電腦中的檔案。 例如，參數會儲存到名稱為*parameters.json*的檔案。 變更如果有需要，請在檔案中的參數值，但至少，建議您變更唯一的複雜密碼的 adminPassword 參數的值。

4. 執行要使用的範本和參數的檔案您下載並修改上方部署新 VNet **azure 群組部署建立**指令程式。 在 [下] 命令，將<path>的路徑，您儲存檔案。 

        azure group create -n PIPTEST2 -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json -e <path>\parameters.json

    預期的輸出 （清單所用的參數值）︰

        info:    Executing command group create
        + Getting resource group PIPTEST2
        + Creating resource group PIPTEST2
        info:    Created resource group PIPTEST2
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/<Subscription ID>/resourceGroups/PIPTEST2
        data:    Name:                PIPTEST2
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
