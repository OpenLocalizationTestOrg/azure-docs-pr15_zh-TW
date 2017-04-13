<properties
    pageTitle="使用資源管理員範本建立 VM |Microsoft Azure"
    description="使用資源管理員範本與 PowerShell 輕鬆建立新的 Windows 虛擬機器。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/06/2016"
    ms.author="davidmu"/>

# <a name="create-a-windows-virtual-machine-with-a-resource-manager-template"></a>資源管理員從範本建立 Windows 虛擬機器

本文會向您介紹 Azure 資源管理員範本，並說明如何使用 PowerShell 來將其部署。 範本部署執行 Windows Server 的新虛擬單一子網路中的單一虛擬機器。

應該花關於 20 分鐘本文中的步驟。

> [AZURE.IMPORTANT] 如果您想要加入的可用性設定您 VM，將其新增至設定當您建立 VM。 目前沒有 VM 新增到可用性之後建立之後設定郵件的方式。

## <a name="step-1-create-the-template-file"></a>步驟 1︰ 建立的範本檔案

您可以建立自己的範本使用[撰寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)中找到的資訊。 您也可以部署已為您建立從[Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)的範本。

1. 開啟您最愛的文字編輯器，並新增必要的結構描述元素和必要的 contentVersion 項目︰

        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
        }
2. [參數](../resource-group-authoring-templates.md#parameters)並非必要，但它們提供範本部署時，請輸入值。 新增參數的項目和子項目 contentVersion 項目之後︰

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }

3. [變數](../resource-group-authoring-templates.md#variables)可以用於範本，若要指定可能會經常變更的值或需要建立的參數值組合的值。 在 [參數] 區段後新增變數項目︰

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }
        
4. 範本中下一步定義[資源](../resource-group-authoring-templates.md#resources)，例如虛擬機器、 虛擬網路及儲存帳戶。 新增 [資源] 區段後的變數區段︰

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvn1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
          
    >[AZURE.NOTE] 本文會建立虛擬機器執行 Windows 伺服器作業系統版本。 若要進一步瞭解選取其他圖像，請參閱[瀏覽並使用 Windows PowerShell 和 Azure CLI 選取 Azure 虛擬機器圖像](virtual-machines-linux-cli-ps-findimage.md)。  
            
2. 將範本檔案儲存為*VirtualMachineTemplate.json*。

## <a name="step-2-create-the-parameters-file"></a>步驟 2︰ 建立參數檔案

若要指定資源參數的範本中所定義的值，建立參數檔案包含部署範本時所使用的值。

1. 在文字編輯器中，此 JSON 將內容複製到新的檔案，稱為*Parameters.json*:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

    >[AZURE.NOTE] 請參閱深入瞭解[使用者名稱和密碼的需求](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm)。

2. 儲存參數的檔案。

## <a name="step-3-install-azure-powershell"></a>步驟 3︰ 安裝 Azure PowerShell

如需安裝最新版本的 PowerShell 的 Azure、 選取您的訂閱，並登入您的帳戶的資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。

## <a name="step-4-create-a-resource-group"></a>步驟 4︰ 建立資源群組

[資源群組](../azure-resource-manager/resource-group-overview.md)中，必須先部署所有資源。

1. 取得可用的位置，可以在其中建立資源的清單。

        Get-AzureRmLocation | sort DisplayName | Select DisplayName

2. 取代的位置，從清單中，例如**美國中部** **$locName**的值。 建立的變數。

        $locName = "location name"
        
3. **$RgName**的值取代為新的資源群組的名稱。 建立變數和資源群組。

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    您應該會看到類似此範例中︰
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1

## <a name="step-5-create-the-resources-with-the-template-and-parameters"></a>步驟 5︰ 建立資源的範本和參數值

**$TemplateFile**的值取代的路徑和範本檔案的名稱。 **$ParameterFile**的值取代的路徑和參數檔案的名稱。 建立變數，然後再部署 [範本。 

        $templateFile = "template file"
        $parameterFile = "parameter file"
        New-AzureRmResourceGroupDeployment -ResourceGroupName $rgName -TemplateFile $templateFile -TemplateParameterFile $parameterFile

    You will see something like this:

        DeploymentName    : VirtualMachineTemplate
        ResourceGroupName : myrg1
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2016 8:11:37 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            adminUsername    String                     mytestacct1
                            adminPassword    SecureString

        Outputs           :

>[AZURE.NOTE] 您也可以部署範本參數及參數值從 Azure 儲存體帳戶。 若要深入瞭解，請參閱[使用 Azure PowerShell 的 Azure 儲存體](../storage/storage-powershell-guide-full.md)。

## <a name="next-steps"></a>後續步驟

- 如果有問題的部署，下一步就會看[Azure 入口網站與疑難排解資源群組部署](../resource-manager-troubleshoot-deployments-portal.md)
- 瞭解如何管理您檢閱[使用 Azure 資源管理員與 PowerShell 管理虛擬機器](virtual-machines-windows-ps-manage.md)建立虛擬機器。
