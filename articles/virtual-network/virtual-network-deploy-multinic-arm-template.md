<properties
   pageTitle="部署資源管理員] 中使用範本的多重 NIC Vm |Microsoft Azure"
   description="瞭解如何部署多重 NIC Vm 資源管理員] 中使用的範本"
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
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="deploy-multi-nic-vms-using-a-template"></a>部署多重 NIC Vm 使用範本

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][傳統的部署模型](virtual-network-deploy-multinic-classic-ps.md)。

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

由於時間，此時您無法與單一 NIC Vm 和使用多個 Nic Vm 相同的 [資源] 群組中，在 [資源] 群組中，並在其他安全性群組中的其他所有元件執行後端伺服器。 下列步驟使用 [資源群組*IaaSStory*主要資源] 群組中，與*IaaSStory 後端*的後端伺服器。

## <a name="prerequisites"></a>必要條件

您可以佈署後端伺服器之前，必須先部署這個案例中所需的所有資源的主要資源群組。 若要部署這些資源，請遵循下列步驟。

1. 瀏覽至[[範本] 頁面](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC)。
2. 在 [範本] 頁面右側的 [**父項資源群組**中，按一下 [**部署至 Azure**]。
3. 如有需要變更參數值，然後遵循部署資源群組 Azure 預覽入口網站中的步驟。

> [AZURE.IMPORTANT] 請確定您儲存的帳戶名稱都是唯一的。 您不能 Azure 中有重複的儲存體帳戶名稱。

## <a name="understand-the-deployment-template"></a>瞭解部署範本

部署提供於此文件的範本之前，請確定您了解功能。 下列步驟提供問題範本的概觀。

1. 瀏覽至[[範本] 頁面](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC)。
2. 按一下 [ **azuredeploy.json**開啟範本檔案。
3. 請注意以下*osType*參數。 此參數用來選取要使用的資料庫伺服器哪些 VM 圖像，以及多個作業系統相關的設定。

        "osType": {
          "type": "string",
          "defaultValue": "Windows",
          "allowedValues": [
            "Windows",
            "Ubuntu"
          ],
          "metadata": {
            "description": "Type of OS to use for VMs: Windows or Ubuntu."
          }
        },

4. 捲動到清單，並核取**dbVMSetting**變數，下方所列的定義。 它會收到一**dbVMSettings**變數中包含的陣列項目。 如果您是軟體開發術語熟悉，您可以檢視 hashtable，或 dictionay **dbVMSettings**變數。

        "dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"

5. 假設您決定要部署 Windows Vm 在後端執行 SQL。 然後**osType** ] 的值可為*Windows*，而**dbVMSetting**變數想要包含的項目列在下方，代表**dbVMSettings**變數中的第一個值。

          "Windows": {
            "vmSize": "Standard_DS3",
            "publisher": "MicrosoftSQLServer",
            "offer": "SQL2014SP1-WS2012R2",
            "sku": "Standard",
            "version": "latest",
            "vmName": "DB",
            "osdisk": "osdiskdb",
            "datadisk": "datadiskdb",
            "nicName": "NICDB",
            "ipAddress": "192.168.2.",
            "extensionDeployment": "",
            "avsetName": "ASDB",
            "remotePort": 3389,
            "dbPort": 1433
          },

6. 請注意**vmSize**包含*Standard_DS3*的值。 使用多個 Nic 允許只是某些 VM 大小。 您可以驗證哪種 VM 大小是多重而啟用瀏覽[多重 NIC 概觀](virtual-networks-multiple-nics.md)。
7. 向下**資源**捲動，並注意的第一個項目。 說明儲存帳戶。 此儲存帳戶會用於維護使用的每個資料庫 VM 資料磁碟。 在這個案例中，每個資料庫 VM 會有儲存在一般的儲存空間，OS 磁碟和儲存 SSD （進階版） 儲存區中的兩個資料磁碟。

        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('prmStorageName')]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "Storage Account - Premium"
          },
          "properties": {
            "accountType": "[parameters('prmStorageType')]"
          }
        },

8. 捲動至下一個的資源，如下所示。 這項資源代表 NIC 用於 VM 每個資料庫中的資料庫存取。 請注意，這項資源中的**複製**函數可使用。 範本可讓您部署數量 Vm 所要根據**dbCount**參數。 因此，您必須建立相同的 Nic 資料庫存取，請針對每個 VM 並列文字格式。

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "NetworkInterfaces - DB DA"
          },
          "copy": {
            "name": "dbniccount",
            "count": "[parameters('dbCount')]"
          },
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Static",
                  "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
                  "subnet": {
                    "id": "[variables('backEndSubnetRef')]"
                  }
                }
              }
            ]
          }
        },

9. 捲動至下一個的資源，如下所示。 這項資源代表 NIC 用於 VM 每個資料庫中的管理。 同樣地，您需要的這些 Nic VM 每個資料庫。 請注意**networkSecurityGroup**項目，請連結可讓 RDP/SSH 此 NIC 只以存取 NSG。

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "NetworkInterfaces - DB RA"
          },
          "copy": {
            "name": "dbniccount",
            "count": "[parameters('dbCount')]"
          },
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "networkSecurityGroup": {
                    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
                  },
                  "privateIPAllocationMethod": "Static",
                  "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
                  "subnet": {
                    "id": "[variables('backEndSubnetRef')]"
                  }
                }
              }
            ]
          }
        },

10. 捲動至下一個的資源，如下所示。 這項資源代表設定為 [所有資料庫 Vm 都共用顯示狀態。 如此一來，您保證，永遠中會有一個 VM 執行期間進行的維修作業的設定。

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/availabilitySets",
          "name": "[variables('dbVMSetting').avsetName]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "AvailabilitySet - DB"
          }
        },

11. 捲動至下一個的資源。 這項資源表示資料庫 Vm，第一個看到下列幾行。 請注意，使用**複製**功能，確保**dbCount**參數會根據建立的多個 Vm。 另請注意**dependsOn**集合。 它會列出所要部署 VM，以及可用性設定，並儲存帳戶之前，先建立所需的兩個 Nic。

          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
          "location": "[variables('location')]",
          "dependsOn": [
            "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
            "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
            "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
            "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
          ],
          "tags": {
            "displayName": "VMs - DB"
          },
          "copy": {
            "name": "dbvmcount",
            "count": "[parameters('dbCount')]"
          },

12. 向下捲動 VM 資源**networkProfile**元素而定，如下所示。 請注意，有兩個 Nic 被每個 VM 參考。 當您建立多個 Nic vm 時，您必須設定*，則為 true*，並為*false*其餘的其中一個 Nic 的**主索引**] 屬性。

        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
              "properties": { "primary": true }
            },
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
              "properties": { "primary": false }
            }
          ]
        }
      }

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>藉由按一下部署部署手臂範本

> [AZURE.IMPORTANT] 請確定您追蹤的[必要條件](#Pre-requisites)步驟之前下列指示。

公用存放庫中提供的範例範本會使用參數檔案包含的預設值用來產生上述的情形。 若要部署部署，請遵循[此連結](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC)使用按一下此範本，右邊的**後端資源群組 （請參閱文件）**按一下 [**部署至 Azure**，取代預設參數值，如有必要，，依照入口網站中的指示進行。

下圖顯示新的 [資源] 群組中，內容部署之後。

![後端資源群組](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## <a name="deploy-the-template-by-using-powershell"></a>使用 PowerShell 來部署範本

若要部署使用 PowerShell 您下載的範本，請遵循下列步驟。

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

3. 執行**`New-AzureRmResourceGroup`**cmdlet 來建立資源群組使用的範本。

        New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
            -TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'

    預期的輸出︰

        ResourceGroupName : IaaSStory-Backend
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *                  

        Resources         :
                            Name                 Type                                 Location
                            ===================  ===================================  ========
                            ASDB                 Microsoft.Compute/availabilitySets   westus  
                            DB1                  Microsoft.Compute/virtualMachines    westus  
                            DB2                  Microsoft.Compute/virtualMachines    westus  
                            NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
                            NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
                            NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
                            NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
                            wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  

        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## <a name="deploy-the-template-by-using-the-azure-cli"></a>使用 Azure CLI 部署範本

若要使用 Azure CLI 部署範本，請遵循下列步驟。

1. 如果您從未使用 Azure CLI，請參閱[安裝及設定 Azure CLI](../xplat-cli-install.md) ，然後依照指示操作，您可在此選取您的 Azure 帳戶及訂閱為止。
2. 執行**`azure config mode`**命令，以切換到資源管理員模式，如下所示。

        azure config mode arm

    以下是預期的輸出上方的命令︰

        info:    New mode is arm

3. 開啟[參數檔案](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json)，選取其內容，並將其儲存到您的電腦中的檔案。 例如，我們*parameters.json*儲存該參數檔案。

4. 執行**`azure group deployment create`**cmdlet 來使用的範本及參數部署新 VNet 檔案您下載並修改上方。 顯示後輸出說明所用的參數的清單。

        azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json

    預期的輸出︰

        info:    Executing command group create
        + Getting resource group IaaSStory-Backend
        + Creating resource group IaaSStory-Backend
        info:    Created resource group IaaSStory-Backend
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
        data:    Name:                IaaSStory-Backend
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
