<properties
    pageTitle="在 [資源管理員範本處理狀態 |Microsoft Azure"
    description="顯示建議使用複雜的物件 Azure 資源管理員範本與連結的範本共用狀態資料的方法"
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
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="tomfitz"/>

# <a name="sharing-state-in-azure-resource-manager-templates"></a>共用 Azure 資源管理員範本中的狀態

本主題說明管理及共用狀態中範本的最佳作法。 參數和在本主題中所顯示的變數是您可以定義的物件類型的範例可以輕鬆地組織您的部署需求。 在上述範例中，您可以實作您自己的物件，與您的環境有意義的屬性值。

本主題是白皮書的較大的一部分。 若要閱讀完整的紙張，下載[全球類別資源管理員範本考量及證明作法](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf)。


## <a name="provide-standard-configuration-settings"></a>提供標準設定的設定

而不是提供的範本，提供總彈性和無數變化，常見的模式就是提供給選取範圍的已知的設定。 作用中使用者可以選取沙箱小型、 中型和大型的標準放置在 t 恤大小。 放置在 t 恤大小的其他範例所提供的產品，例如社群版或企業版。 在其他情況下，可能會技術 – 的工作量特定設定例如地圖減少或沒有 sql。

複雜的物件，您可以建立包含的資料，有時也稱為 「 屬性袋 」 集合變數，並使用該資料來驅動資源宣告在範本中。 此方法均提供客戶預先設定的不同大小的建議，已知的設定。 已知的設定，不範本的使用者必須決定叢集自行縮放、 納入平台資源限制式，並執行數學運算，以識別產生分割儲存帳戶及其他資源 （因為叢集大小和資源限制式）。 除了客戶進行更好的體驗，請幾個已知的設定，更容易支援，可協助您進行較高層級的密度。

下列範例會示範如何定義包含代表資料集合複雜物件的變數。 集合定義的虛擬機器大小、 網路設定、 作業系統設定和可用性設定所使用的值。

    "variables": {
      "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
      "tshirtSizeSmall": {
        "vmSize": "Standard_A1",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
        "vmCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 1,
          "pool": "db",
          "map": [0,0],
          "jumpbox": 0
        }
      },
      "tshirtSizeMedium": {
        "vmSize": "Standard_A3",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-8disk-resources.json')]",
        "vmCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1],
          "jumpbox": 0
        }
      },
      "tshirtSizeLarge": {
        "vmSize": "Standard_A4",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
        "vmCount": 3,
        "slaveCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1,1],
          "jumpbox": 0
        }
      },
      "osSettings": {
        "scripts": [
          "[concat(variables('templateBaseUrl'), 'install_postgresql.sh')]",
          "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
        ],
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "14.04.2-LTS",
          "version": "latest"
        }
      },
      "networkSettings": {
        "vnetName": "[parameters('virtualNetworkName')]",
        "addressPrefix": "10.0.0.0/16",
        "subnets": {
          "dmz": {
            "name": "dmz",
            "prefix": "10.0.0.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          },
          "data": {
            "name": "data",
            "prefix": "10.0.1.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          }
        }
      },
      "availabilitySetSettings": {
        "name": "pgsqlAvailabilitySet",
        "fdCount": 3,
        "udCount": 5
      }
    }

請注意**tshirtSize**變數串連您透過參數 （**小型**、**中型**、**大型**） 所提供的放置在 t 恤大小，以文字**tshirtSize**。 您可以使用此變數來擷取相關聯的複雜物件變數該放置在 t 恤大小。

然後，您可以參考這些範本的更新版本中的變數。 參照命名變數和其屬性的能力簡化範本的語法，並可讓您更容易瞭解內容。 下列範例會定義使用物件顯示先前設定值來部署的資源。 虛擬記憶體大小來擷取的值的設定，例如`variables('tshirtSize').vmSize`時值的磁碟大小會從擷取`variables('tshirtSize').diskSize`。 此外，具有的值設定連結的範本的 URI `variables('tshirtSize').vmTemplate`。

    "name": "master-node",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared')]"
    ],
    "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('tshirtSize').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "value": "[parameters('adminPassword')]"
          },
          "replicatorPassword": {
            "value": "[parameters('replicatorPassword')]"
          },
          "osSettings": {
            "value": "[variables('osSettings')]"
          },
          "subnet": {
            "value": "[variables('networkSettings').subnets.data]"
          },
          "commonSettings": {
            "value": {
              "region": "[parameters('region')]",
              "adminUsername": "[parameters('adminUsername')]",
              "namespace": "ms"
            }
          },
          "storageSettings": {
            "value":"[variables('tshirtSize').storage]"
          },
          "machineSettings": {
            "value": {
              "vmSize": "[variables('tshirtSize').vmSize]",
              "diskSize": "[variables('tshirtSize').diskSize]",
              "vmCount": 1,
              "availabilitySet": "[variables('availabilitySetSettings').name]"
            }
          },
          "masterIpAddress": {
            "value": "0"
          },
          "dbType": {
            "value": "MASTER"
          }
        }
      }
    }

## <a name="pass-state-to-a-template"></a>傳遞狀態為範本

您將透過直接期間部署所提供的參數範本共用狀態。

下表列出在範本中的常用的參數。

名稱 | 值 | 描述
---- | ----- | -----------
位置    | 字串限制清單中的 Azure 區域   | 部署資源的位置的位置。
storageAccountNamePrefix    | 字串    | VM 的磁碟放置的位置儲存帳戶的唯一 DNS 名稱
網域名稱  | 字串    | 網域名稱的格式公開存取 jumpbox VM: **{domainName}。 {位置}.cloudapp.com** ，例如︰ **mydomainname.westus.cloudapp.azure.com**
adminUsername   | 字串    | Vm 的使用者名稱
adminPassword   | 字串    | Vm 密碼
tshirtSize  | 限制清單中的字串提供放置在 t 恤大小   | 佈建命名的縮放比例單位大小。 例如，「 小 」，「 中 」、 「 大 」
virtualNetworkName  | 字串    | 消費者想要使用的虛擬網路的名稱。
enableJumpbox   | 字串限制清單 （啟用/停用） | 識別是否要啟用 jumpbox 環境的參數。 值: 「 啟用 」，「 停用 」

在 [上一節中使用**tshirtSize**參數定義為︰

    "parameters": {
      "tshirtSize": {
        "type": "string",
        "defaultValue": "Small",
        "allowedValues": [
          "Small",
          "Medium",
          "Large"
        ],
        "metadata": {
          "Description": "T-shirt size of the MongoDB deployment"
        }
      }
    }


## <a name="pass-state-to-linked-templates"></a>傳遞狀態連結的範本

連接至連結的範本時，您經常使用的靜態，並產生的變數。

### <a name="static-variables"></a>靜態變數

靜態變數通常用於提供基底值，例如的整個範本所使用的 Url。

在下列範本摘錄， `templateBaseUrl` GitHub 中指定範本的根位置。 下一行建立新的變數`sharedTemplateUrl`的串連基底 URL 已知共用的資源範本的名稱。 線下, 面的複雜的物件變數用來儲存放置在 t 恤大小，其中的基礎 URL 是串連已知的設定範本位置，而儲存在`vmTemplate`屬性。

這種方法的優點是，如果範本位置的變更，您只需要變更 [傳送所有連結的範本中的一個位置的靜態變數。

    "variables": {
      "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
      "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
      "tshirtSizeSmall": {
        "vmSize": "Standard_A1",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
        "vmCount": 2,
        "slaveCount": 1,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 1,
          "pool": "db",
          "map": [0,0],
          "jumpbox": 0
        }
      }
    }

### <a name="generated-variables"></a>產生的變數

除了靜態變數，是以動態方式產生的數個變數。 本節將說明一些常見的產生的變數。

#### <a name="tshirtsize"></a>tshirtSize

您已熟悉上述範例的這個產生的變數。

#### <a name="networksettings"></a>networkSettings

在容量、 功能或端對端範圍的方案範本，連結的範本通常是在網路上建立存在的資源。 一個簡單的方法是使用複雜物件儲存網路的設定，並將其傳遞至連結的範本。

下面能夠看到傳達網路設定的範例。

    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    }

#### <a name="availabilitysettings"></a>availabilitySettings

通常可用性集中放在連結的範本建立的資源。 在下列範例中，指定可用性設定名稱，並使用計算錯誤網域和更新的網域。

    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

如果您需要多個可用性集 （例如，一個主節點），另一個資料節點，您可以使用名稱作為前置字元，指定多個可用性集]，或依照先前顯示建立特定的放置在 t 恤大小的變數的模型。

#### <a name="storagesettings"></a>storageSettings

儲存詳細資料通常被共用連結的範本。 在下面的範例中， *storageSettings*物件會提供詳細資料的儲存空間的帳戶和容器的名稱。

    "storageSettings": {
        "vhdStorageAccountName": "[parameters('storageAccountName')]",
        "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
        "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
    }

#### <a name="ossettings"></a>osSettings

使用連結的範本，您可能需要作業系統設定為了在已知的設定不同類型的各種節點類型。 複雜的物件輕鬆儲存及共用作業系統的資訊，也可讓您更容易以供部署支援多個作業系統選項。

下列範例顯示的*osSettings*物件︰

    "osSettings": {
      "imageReference": {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    }

#### <a name="machinesettings"></a>machineSettings

產生的變數， *machineSettings*是包含的核心變數建立 VM 混合的複雜物件。 變數包含管理員的使用者名稱和密碼、 VM 名稱，以及作業系統影像參照的前置字元。

    "machineSettings": {
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "machineNamePrefix": "mongodb-",
        "osImageReference": {
            "publisher": "[variables('osFamilySpec').imagePublisher]",
            "offer": "[variables('osFamilySpec').imageOffer]",
            "sku": "[variables('osFamilySpec').imageSKU]",
            "version": "latest"
        }
    },

請注意， *osImageReference*從主要的範本中定義的*osSettings*變數擷取的值。 這表示您可以輕鬆地變更 vm 的作業系統，完全或根據範本取用者的喜好設定。

#### <a name="vmscripts"></a>vmScripts

*VmScripts*物件包含下載並執行 VM 的執行個體，包括外部及參照的指令碼的詳細資料。 外部參照包含基礎結構。 內部參照包含安裝的安裝的軟體和設定。

您可以使用*scriptsToDownload*屬性清單的指令碼的 vm 下載。 這個物件也包含不同類型的動作的命令列引數的參照。 這些動作會包括執行個別的節點的預設安裝、 部署所有節點後, 所執行的安裝及任何其他的指令碼，可能與指定的範本。

此範例中是用來部署 MongoDB，需要進行可用性仲裁者的範本。 *ArbiterNodeInstallCommand*已新增至*vmScripts*安裝仲裁者。

[變數] 區段會找到定義執行指令碼以適當的值的特定文字的變數。

    "vmScripts": {
        "scriptsToDownload": [
            "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
            "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
        ],
        "regularNodeInstallCommand": "[variables('installCommand')]",
        "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
        "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
    },


## <a name="return-state-from-a-template"></a>傳回從範本的狀態

您要不只傳送資料至範本，您也可以共用資料給呼叫的範本。 在 [**輸出**的連結的範本] 區段中，您可以提供可由來源範本的金鑰/值組。

下列範例會示範如何傳送連結的範本所產生的私人 IP 位址。

    "outputs": {
        "masterip": {
            "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].properties.privateIPAddress]",
            "type": "string"
         }
    }

在主要的範本，您可以使用該資料與下列語法︰

    "[reference('master-node').outputs.masterip.value]"

您可以使用這個輸出區段或主要範本的 [資源] 區段中的運算式。 您無法在變數] 區段中使用運算式，因為它依賴執行階段狀態。 若要從主要的範本，傳回此值，請使用︰

    "outputs": { 
      "masterIpAddress": {
        "value": "[reference('master-node').outputs.masterip.value]",
        "type": "string"
      }
     
若要傳回資料磁碟虛擬機器中使用連結的範本的輸出一節的範例，請參閱[建立虛擬機器的多個資料磁碟](resource-group-create-multiple.md#creating-multiple-data-disks-for-a-virtual-machine)。

## <a name="define-authentication-settings-for-virtual-machine"></a>定義虛擬機器驗證設定

您可以使用相同的模式顯示先前設定的設定來指定虛擬機器驗證設定。 您建立的驗證類型傳遞的參數。

    "parameters": {
      "authenticationType": {
        "allowedValues": [
          "password",
          "sshPublicKey"
        ],
        "defaultValue": "password",
        "metadata": {
          "description": "Authentication type"
        },
        "type": "string"
      }
    }

新增不同的驗證類型，變數，儲存哪種類型的變數用來部署根據的參數值。

    "variables": {
      "osProfile": "[variables(concat('osProfile', parameters('authenticationType')))]",
      "osProfilepassword": {
        "adminPassword": "[parameters('adminPassword')]",
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]"
      },
      "osProfilesshPublicKey": {
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]",
        "linuxConfiguration": {
          "disablePasswordAuthentication": "true",
          "ssh": {
            "publicKeys": [
              {
                "keyData": "[parameters('sshPublicKey')]",
                "path": "/home/notused/.ssh/authorized_keys"
              }
            ]
          }
        }
      }
    }

在定義虛擬機器時，您可以設定**osProfile**您建立的變數。

    {
      "type": "Microsoft.Compute/virtualMachines",
      ...
      "osProfile": "[variables('osProfile')]"
    }


## <a name="next-steps"></a>後續步驟
- 若要進一步瞭解範本的節，請參閱[撰寫 Azure 資源管理員範本](resource-group-authoring-templates.md)
- 若要查看的可用範本中的函數，請參閱[Azure 資源管理員範本函數](resource-group-template-functions.md)

