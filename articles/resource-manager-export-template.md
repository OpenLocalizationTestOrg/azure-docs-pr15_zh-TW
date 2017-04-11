<properties
    pageTitle="匯出 Azure 資源管理員範本 |Microsoft Azure"
    description="使用 Azure 資源管理]，匯出現有的 [資源] 群組中的範本。"
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/20/2016"
    ms.author="tomfitz"/>

# <a name="export-an-azure-resource-manager-template-from-existing-resources"></a>從現有的資源匯出 Azure 資源管理員範本

資源管理員可讓您從現有的資源，在您的訂閱中匯出資源管理員範本。 若要深入瞭解範本語法，或自動化重新部署您的方案，如有需要您可以使用該產生的範本。

請務必請注意，有兩種不同方式匯出範本︰

- 您可以匯出您部署所用的實際範本。 匯出的範本包含所有參數和變數，一樣在出現的原始範本。 當您完成部署透過入口網站的資源，此方法有幫助。 現在，您想要瞭解如何建構範本來建立這些資源。
- 您可以匯出代表資源群組的目前狀態的範本。 匯出的範本不以用於部署任何範本為基礎。 不過，它會建立範本的 [資源] 群組的快照。 匯出的範本會有許多硬式編碼的值，可能不多個參數，當您通常會定義。 此方法時，您已經修改入口網站，或是指令碼的 [資源] 群組。 現在，您需要擷取另存為範本的 [資源] 群組。

本主題說明這兩種方法。

在本教學課程中，您登入 Azure 入口網站中，建立儲存帳戶，然後匯出為該儲存帳戶的範本。 您新增虛擬網路修改資源群組。 最後，您可以匯出代表其目前狀態的新範本。 本文著重於簡化基礎結構，雖然您可以使用相同的步驟，匯出更為複雜的解決方案的範本。

## <a name="create-a-storage-account"></a>建立儲存的帳戶

1. 在[Azure 入口網站](https://portal.azure.com)中，選取 [**新增** > **儲存** > **儲存的帳戶**。

      ![建立儲存空間](./media/resource-manager-export-template/create-storage.png)

2. 建立儲存體帳戶名稱**儲存空間**，您的名稱簡寫與日期。 透過 Azure，儲存體帳戶名稱必須是唯一的。 如果名稱已在使用中，您會看到錯誤訊息，指出正在使用的名稱。 請嘗試一種變化。 資源群組建立新的資源群組並將其命名**ExportGroup**。 您可以使用預設值的其他屬性。 選取 [**建立**]。

      ![提供儲存值](./media/resource-manager-export-template/provide-storage-values.png)

部署可能需要幾分鐘。 部署完成之後，您的訂閱包含的儲存空間帳戶。

## <a name="view-a-template-from-deployment-history"></a>檢視部署歷程記錄中的範本

1. 移至 [資源群組刀新資源群組。 請注意刀顯示最後一個部署的結果。 選取此連結。

      ![資源群組刀](./media/resource-manager-export-template/resource-group-blade.png)

2. 您會看到群組的部署的歷程記錄。 在您的情況下，刀可能會列出只有一個部署。 選取 [部署]。

     ![上次部署](./media/resource-manager-export-template/last-deployment.png)

3. 刀顯示部署的摘要。 摘要包含的狀態部署和其作業與您所提供的參數的值。 若要查看您的部署使用的範本，請選取**檢視範本**。

     ![檢視部署摘要](./media/resource-manager-export-template/deployment-summary.png)

4. 資源管理員為您擷取下列六個檔案︰

   1. **範本**的範本，以定義您的方案的基礎結構。 當您建立透過入口網站的儲存空間帳戶時，資源管理員用於範本將其部署，並儲存的範本以供日後參考。
   2. **參數**的參數檔案，您可以使用傳入部署期間的值。 包含您在第一次的部署，期間所提供的值，但您可以在您重新部署範本時變更這些值。
   3. **CLI** -Azure 命令列介面 (CLI) 指令碼檔，您可以用來部署範本。
   4. **PowerShell**的 Azure PowerShell 指令碼檔，您可以用來部署範本。
   5. **.NET** -.NET 類別，您可以使用部署範本。
   6. **[注音標示**為 [注音標示的類別，您可以用來部署範本。

     跨刀的檔案會透過連結提供。 根據預設，刀會顯示範本。

       ![檢視範本](./media/resource-manager-export-template/view-template.png)

     現在就讓我們注意特定的範本。 您的範本看起來應該像︰

        {「 $schema 」: 「 https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json# 」、 「 contentVersion 」: 「 1.0.0.0 「，」 參數 」: {"名稱": {「 類型 」: 」 字串 「}，「 accountType 」: {「 類型 」: 」 字串 「}，[位置]: {「 類型 」: 」 字串 「}，「 encryptionEnabled 」: {「 預設 」: 為 false，「 類型 」: 」 Bool 」}}，「 資源 」: [{「 類型 」: 「 Microsoft.Storage/storageAccounts 「，」 sku 」: {"名稱": 」 [parameters('accountType')] 」}、 「 類型 」: 」 存放區 」，「 名稱 」: 」 [parameters('name')] 」 「 apiVersion 」: 「 2016年-01-01 「，」 位置 」: 」 [parameters('location')] 」 [內容]: {「 加密 」: {」 服務 」: {」 blob 」: {「 啟用 」: 」 [parameters('encryptionEnabled')] 」}}，「 keySource 」: 「 Microsoft.Storage 」}}}]}
 
此範本是用來建立您儲存的帳戶的實際範本。 請注意，包含參數，可讓您部署不同類型的儲存空間的帳戶。 若要進一步瞭解範本的結構，請參閱[撰寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。 您可以使用範本中的功能的完整清單，請參閱[Azure 資源管理員範本函數](resource-group-template-functions.md)。


## <a name="add-a-virtual-network"></a>新增虛擬網路

您在前一節中下載的範本來表示該原始部署的基礎結構。 不過，它不會負責部署之後所做的變更。
若要描繪出這個問題，讓我們來新增到入口網站的虛擬網路修改資源群組。

1. 在 [資源群組刀中，選取 [**新增**。

      ![新增資源](./media/resource-manager-export-template/add-resource.png)

2. 從可用的資源選取**虛擬網路**。

      ![選取虛擬網路](./media/resource-manager-export-template/select-vnet.png)

2. 命名虛擬網路**VNET**，並使用預設值的其他屬性。 選取 [**建立**]。

      ![設定提醒](./media/resource-manager-export-template/create-vnet.png)

3. 虛擬網路已成功部署至您的資源群組之後，請再次看看部署歷程記錄。 現在，您會看到兩個部署。 如果看不到第二個部署，您可能需要關閉您的資源群組刀並重新開啟它。 選取 [最新的部署]。

      ![部署歷程記錄](./media/resource-manager-export-template/deployment-history.png)

4. 檢視該部署的範本。 請注意，它會定義虛擬的網路。 它不包含您部署較舊版本的儲存空間帳戶。 您不再需要代表資源群組中的所有資源的範本。

## <a name="export-the-template-from-resource-group"></a>匯出資源] 群組中的範本

若要取得資源群組的目前狀態，匯出的範本，會顯示資源群組的快照。  

> [AZURE.NOTE] 您無法匯出超過 200 封資源的資源群組的範本。

1. 若要檢視資源群組的範本，請選取 [**自動化指令碼**]。

      ![匯出資源群組](./media/resource-manager-export-template/export-resource-group.png)

     並非所有的資源類型支援匯出範本函數。 資源群組只包含的儲存空間帳戶與此文件中顯示的虛擬網路，如果您沒有看到錯誤。 不過，如果您已建立其他的資源類型，您可能會看到錯誤訊息指出有匯出問題。 您瞭解如何處理這些問題的[修正匯出問題](#fix-export-issues)一節。

      

2. 您再次看到重新部署方案，您可以使用的六個檔案，但這次範本會稍有不同。 此範本具有只有兩個參數︰ 一個用於儲存體帳戶名稱，還有一個虛擬網路名稱。

        "parameters": {
          "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
          },
          "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
          }
        },

     資源管理員未擷取部署期間您使用的範本。 而其產生新的範本為基礎的資源目前的設定。 例如，範本的儲存空間帳戶位置和試驗值設為︰

        "location": "northeurope",
        "tags": {},
        "properties": {
            "accountType": "Standard_RAGRS"
        },

3. 您有幾個選項繼續使用此範本。 您可以下載範本，並在本機處理使用 JSON 編輯器。 或者，您可以將範本儲存至文件庫，並透過網站處理。

     如果您習慣使用[與程式碼](resource-manager-vs-code.md)或[Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)等 JSON 編輯器，您可能會想下載至本機的範本，並使用該編輯器。 如果沒有您設定使用 JSON 編輯器，您可能會想編輯透過網站範本。 本主題的其餘部分假設您已將範本儲存至您的文件庫，在入口網站。 不過，您對相同的語法變更範本是否使用 JSON 編輯器，或透過入口網站，請使用本機。

     若要從本機，請選取 [**下載**]。

      ![下載範本](./media/resource-manager-export-template/download-template.png)

     若要透過入口網站，請選取 [**新增至文件庫**。

      ![新增至文件庫](./media/resource-manager-export-template/add-to-library.png)

     文件庫範本時，輸入範本的名稱和描述。 然後，選取 [**儲存**]。

     ![設定範本值](./media/resource-manager-export-template/set-template-values.png)

4. 若要檢視文件庫中儲存的範本，選取 [**更多服務**] 中，輸入**範本**篩選結果，請選取**範本**。

      ![尋找範本](./media/resource-manager-export-template/find-templates.png)

5. 選取名稱儲存的範本。

      ![選取範本](./media/resource-manager-export-template/select-library-template.png)

## <a name="customize-the-template"></a>自訂的範本

如果您想要建立相同儲存帳戶和每個部署虛擬網路匯出的範本的運作正常。 不過，資源管理員提供選項，讓您可以使用更多的彈性，部署範本。 例如部署期間，您可能要指定儲存的帳戶，以建立或使用虛擬網路位址首碼] 和 [子網路首碼的值的類型。

在此區段中，您新增參數至匯出的範本，好讓您將這些資源部署至其他環境時，您可以重複使用的範本。 您也可以新增一些功能至您的範本，以減少部署您的範本時，發生錯誤的可能性。 您不再需要猜測您儲存的帳戶的唯一名稱。 不過，範本所建立的唯一名稱。 您限制的值，您可以指定儲存的帳戶類型，以有效的選項。

1. 選取 [**編輯**] 以自訂範本。

     ![顯示範本](./media/resource-manager-export-template/show-template.png)

1. 選取的範本。

     ![編輯範本](./media/resource-manager-export-template/edit-template.png)

1. 若要將您可能會想要部署期間所指定的值，取代新的參數定義中的 [**參數**] 區段。 請注意的**allowedValues** **storageAccount_accountType**的值。 如果您不小心提供不正確的值，該錯誤會辨識部署開始之前。 此外，請注意，您提供的前置詞儲存體帳戶名稱，而前置詞是 11 的字元。 如果您限制 11 個字元的前置詞，您確定完整名稱不能超過的儲存空間帳戶的字元數上限。 前置詞可讓您套用到儲存帳戶的命名慣例。 您會看到如何建立唯一的名稱，在下一個步驟。

        "parameters": {
          "storageAccount_prefix": {
            "type": "string",
            "maxLength": 11
          },
          "storageAccount_accountType": {
            "defaultValue": "Standard_RAGRS",
            "type": "string",
            "allowedValues": [
              "Standard_LRS",
              "Standard_ZRS",
              "Standard_GRS",
              "Standard_RAGRS",
              "Premium_LRS"
            ]
          },
          "virtualNetwork_name": {
            "type": "string"
          },
          "addressPrefix": {
            "defaultValue": "10.0.0.0/16",
            "type": "string"
          },
          "subnetName": {
            "defaultValue": "subnet-1",
            "type": "string"
          },
          "subnetAddressPrefix": {
            "defaultValue": "10.0.0.0/24",
            "type": "string"
          }
        },

2. 您的**變數**區段目前是範本的空的。 在 [**變數**] 區段中，您可以建立簡化的語法，您的範本的其餘部分中的值。 本節取代為新的變數定義。 **StorageAccount_name**變數串連從參數前置詞，以唯一的字串，則會產生根據 [資源群組的識別碼。 您不再需要提供參數值時，猜測唯一的名稱。

        "variables": {
          "storageAccount_name": "[concat(parameters('storageAccount_prefix'), uniqueString(resourceGroup().id))]"
        },

3. 若要在資源定義中使用的參數和變數，用新的資源定義來取代 [**資源**] 區段。 指派給資源屬性的值以外的資源定義稍微已變更的通知。 屬性是從匯出範本的內容相同。 您只要指派給參數值，而非硬式編碼值屬性。 資源的位置設為 [資源] 群組，透過**resourceGroup （).location**運算式中使用相同的位置。 經由**變數**運算式參考建立儲存體帳戶名稱的變數。

        "resources": [
          {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetwork_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "properties": {
              "addressSpace": {
                "addressPrefixes": [
                  "[parameters('addressPrefix')]"
                ]
              },
              "subnets": [
                {
                  "name": "[parameters('subnetName')]",
                  "properties": {
                    "addressPrefix": "[parameters('subnetAddressPrefix')]"
                  }
                }
              ]
            },
            "dependsOn": []
          },
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccount_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "tags": {},
            "properties": {
                "accountType": "[parameters('storageAccount_accountType')]"
            },
            "dependsOn": []
          }
        ]

4. 選取**[確定]** ，當您完成編輯範本。

5. 選取 [**儲存**至範本儲存變更。

     ![儲存範本](./media/resource-manager-export-template/save-template.png)

6. 若要部署更新的範本，請選取 [**部署**]。

     ![部署範本](./media/resource-manager-export-template/deploy-template.png)

7. 提供的參數值，並選取新的資源群組部署資源。

## <a name="update-the-downloaded-parameters-file"></a>更新下載的參數檔案

如果您正在使用下載的檔案 （而非入口網站的文件庫），您需要更新下載的參數檔案。 它不會再符合您的範本中的參數。 您沒有使用參數檔案，但當您重新部署環境時，其可簡化程序。 您使用的預設值，好讓您參數檔案只需要兩個值的範本中定義的參數。

取代 parameters.json 檔案的內容︰

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccount_prefix": {
      "value": "storage"
    },
    "virtualNetwork_name": {
      "value": "VNET"
    }
  }
}
```

更新的參數檔案會提供僅適用於沒有預設值的參數值。 當您想為不同的預設值的值時，您可以提供其他參數的值。

## <a name="fix-export-issues"></a>修正匯出問題

並非所有的資源類型支援匯出範本函數。 資源管理員特別並不會匯出部分的資源類型，以避免將機密資料公開。 例如，如果您已連線字串中您的網站設定時，可能不想它明確地顯示在 [匯出的範本。 您可以手動將遺失資源新增至您的範本，就可取得解決這個問題。

> [AZURE.NOTE] 匯出的資源群組，而不從您的部署記錄時，您只會遇到匯出問題。 如果您上次的部署能正確代表資源群組的目前狀態，您應該匯出範本部署記錄] 中，而不是從 [資源] 群組。 僅匯出資源群組，當您對未定義單一範本中的 [資源] 群組所做的變更。

例如，如果您匯出包含 web 應用程式 SQL 資料庫與網站設定中的連線字串的資源群組的範本，您會看到下列訊息︰

![顯示錯誤](./media/resource-manager-export-template/show-error.png)

選取該郵件會顯示您未完全匯出的資源類型。 
     
![顯示錯誤](./media/resource-manager-export-template/show-error-details.png)

本主題說明常見修正方式。

### <a name="connection-string"></a>連線字串

在網站的資源，新增資料庫的連線字串︰

```
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "connectionstrings",
      "dependsOn": [
          "[concat('Microsoft.Web/Sites/', parameters('<site-name>'))]"
      ],
      "properties": {
          "DefaultConnection": {
            "value": "[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('<database-server-name>'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('<database-name>'), ';User Id=', parameters('<admin-login>'), '@', parameters('<database-server-name>'), ';Password=', parameters('<admin-password>'), ';')]",
              "type": "SQLServer"
          }
      }
    }
  ]
}
```    

### <a name="web-site-extension"></a>網站副檔名

在網站的資源，新增要安裝的程式碼的定義︰

```
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "name": "MSDeploy",
      "type": "extensions",
      "location": "[resourceGroup().location]",
      "apiVersion": "2015-08-01",
      "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('<site-name>'))]"
      ],
      "properties": {
        "packageUri": "[concat(parameters('<artifacts-location>'), '/', parameters('<package-folder>'), '/', parameters('<package-file-name>'), parameters('<sas-token>'))]",
        "dbType": "None",
        "connectionString": "",
        "setParameters": {
          "IIS Web Application Name": "[parameters('<site-name>')]"
        }
      }
    }
  ]
}
```

### <a name="virtual-machine-extension"></a>虛擬機器副檔名

有關虛擬機器延伸模組的詳細資訊，請參閱[Azure Windows VM 副檔名設定範例](./virtual-machines/virtual-machines-windows-extensions-configuration-samples.md)。

### <a name="virtual-network-gateway"></a>虛擬網路閘道器

新增虛擬網路閘道器的資源類型。

```
{
  "type": "Microsoft.Network/virtualNetworkGateways",
  "name": "[parameters('<gateway-name>')]",
  "apiVersion": "2015-06-15",
  "location": "[resourceGroup().location]",
  "properties": {
    "gatewayType": "[parameters('<gateway-type>')]",
    "ipConfigurations": [
      {
        "name": "default",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('<vnet-name>'), parameters('<new-subnet-name>'))]"
          },
          "publicIpAddress": {
            "id": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('<new-public-ip-address-Name>'))]"
          }
        }
      }
    ],
    "enableBgp": false,
    "vpnType": "[parameters('<vpn-type>')]"
  },
  "dependsOn": [
    "Microsoft.Network/virtualNetworks/codegroup4/subnets/GatewaySubnet",
    "[concat('Microsoft.Network/publicIPAddresses/', parameters('<new-public-ip-address-Name>'))]"
  ]
},
```

### <a name="local-network-gateway"></a>區域網路閘道器

新增 [區域網路閘道器的資源類型。

```
{
    "type": "Microsoft.Network/localNetworkGateways",
    "name": "[parameters('<local-network-gateway-name>')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "localNetworkAddressSpace": {
        "addressPrefixes": "[parameters('<address-prefixes>')]"
      }
    }
}
```

### <a name="connection"></a>連線

新增連線資源類型。

```
{
    "apiVersion": "2015-06-15",
    "name": "[parameters('<connection-name>')]",
    "type": "Microsoft.Network/connections",
    "location": "[resourceGroup().location]",
    "properties": {
        "virtualNetworkGateway1": {
        "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', parameters('<gateway-name>'))]"
      },
      "localNetworkGateway2": {
        "id": "[resourceId('Microsoft.Network/localNetworkGateways', parameters('<local-gateway-name>'))]"
      },
      "connectionType": "IPsec",
      "routingWeight": 10,
      "sharedKey": "[parameters('<shared-key>')]"
    }
},
```


## <a name="next-steps"></a>後續步驟

恭喜您 ！ 您已經學會如何匯出資源在入口網站中所建立的範本。

- 您可以部署透過[PowerShell](resource-group-template-deploy.md)、 [Azure CLI](resource-group-template-deploy-cli.md)或[REST API](resource-group-template-deploy-rest.md)範本。
- 若要查看如何匯出透過 PowerShell 範本，請參閱[使用 PowerShell 的 Azure 與 Azure 資源管理員](powershell-azure-resource-manager.md)。
- 若要查看如何匯出透過 Azure CLI 範本，請參閱[Azure CLI for Mac、 Linux 和 Windows Azure 資源管理員使用的商務連絡人](xplat-cli-azure-resource-manager.md)。
