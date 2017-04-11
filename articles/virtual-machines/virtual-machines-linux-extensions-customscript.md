<properties
   pageTitle="自訂指令碼上 Linux Vm |Microsoft Azure"
   description="使用自訂指令碼延伸來自動化 Linux VM 設定工作"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="nepeters"/>

# <a name="using-the-azure-custom-script-extension-with-linux-virtual-machines"></a>使用 Linux 虛擬機器中的副檔名為 Azure 自訂指令碼

自訂指令碼副檔名下載並執行指令碼 Azure 虛擬機器上。 這個延伸相當實用文章部署設定、 軟體安裝，或任何其他設定 / 管理工作。 指令碼可以下載從 Azure 儲存空間或其他可存取的網際網路位置，或提供執行階段副檔名。 自訂指令碼副檔名整合 Azure 資源管理員範本，也可以用使用 Azure CLI、 PowerShell、 Azure 入口網站或 Azure 虛擬機器 REST API。

這份文件也詳細資料 Linux 系統的疑難排解步驟，以及如何使用 [從 Azure CLI 與 Azure 資源管理員範本，自訂指令碼延伸模組的詳細資料。

## <a name="extension-configuration"></a>延伸設定

自訂指令碼副檔名設定指定項目，例如指令碼位置和 [執行] 命令。 此設定可以儲存設定檔中，指定在命令列，或在 Azure 資源管理員範本中。 機密資料可以儲存在受保護的設定，這是加密，只解密虛擬機器內。 受保護的設定時，[執行] 命令包含機密資料，例如密碼。

### <a name="public-configuration"></a>公用設定

結構描述︰

- **commandToExecute**: （必要，請字串） 若要執行的項目點指令碼
- **fileUris**: （選擇性字串陣列） 下載檔案的 Url。
- **時間戳記**（選擇性整數） 使用此欄位來觸發的指令碼重新執行藉由變更這個欄位的值。

```none
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>受保護的設定

結構描述︰

- **commandToExecute**: （選擇性字串） 若要執行的項目點指令碼。 如果您的命令含有機密資料，例如密碼，請改為使用此欄位。
- **storageAccountName**: （選擇性字串） 的儲存體帳戶名稱。 如果您指定儲存的認證，所有 fileUris 必須都是 Azure Blob 的 Url。
- **storageAccountKey**: （選擇性字串） 儲存帳戶的便捷鍵。


```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>Azure CLI

使用時 Azure CLI 執行自訂指令碼擴充功能，設定或建立檔案的檔案 uri，並執行指令包含最小的檔案。

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /scirpt-config.json
```

您也可以命令可以執行使用`--public-config`和`--private-config`選項，指定執行時，不另外的設定檔的設定。

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI 範例

**範例 1** -公用設定指令碼檔案。

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
  "commandToExecute": "./hello.sh"
}
```

Azure CLI 命令︰

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**範例 2** -沒有指令碼檔與公用設定。

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI 命令︰

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**範例 3** -公用的設定檔用來指定指令碼檔 URI，並受保護的設定檔用來指定要執行命令。

公用的設定檔︰

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
}
```

受保護的設定檔︰  

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Azure CLI 命令︰

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path ./public.json --private-config-path ./protected.json
```

## <a name="resource-manager-template"></a>資源管理員範本

可以執行自訂指令碼副檔名為 Azure 虛擬機器部署時使用資源管理員範本。 若要這麼做，請將部署範本中的正確格式的 JSON。

### <a name="resource-manager-examples"></a>資源管理員範例

**範例 1** -公用設定。

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

**範例 2** -執行命令中受保護的設定。

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

請參閱.Net 核心音樂市集示範完成的範例-[音樂市集示範](https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db)。

## <a name="troubleshooting"></a>疑難排解

自訂指令碼副檔名執行時，指令碼建立，或下載到類似下面的範例目錄。 命令輸出也會儲存在這個目錄`stdout`和`stderr`檔案。

```none
/var/lib/azure/custom-script/download/0/
```

Azure 指令碼副檔名產生的記錄，可以在這裡找到。

```none
/var/log/azure/customscript/handler.log
```

也可以使用 Azure CLI 擷取自訂指令碼副檔名的執行狀態。

```none
azure vm extension get <resource-group> <vm-name>
```

輸出看起來像下列文字︰

```none
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>後續步驟

其他 VM 指令碼擴充的資訊，請參閱[Azure 指令碼副檔名 Linux 概觀](./virtual-machines-linux-extensions-features.md)。