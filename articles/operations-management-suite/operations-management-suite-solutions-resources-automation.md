<properties
   pageTitle="OMS 解決方案中的自動化資源 |Microsoft Azure"
   description="解決方案中 OMS 通常會併入 runbooks Azure 自動化，這樣便可自動化程序，例如收集並處理監控的資料。  本文將說明如何在方案中加入 runbooks 與他們相關的資源。"
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/19/2016"
   ms.author="bwren" />

# <a name="automation-resources-in-oms-solutions-preview"></a>自動化資源 OMS 解決方案 （預覽版本）

>[AZURE.NOTE]這是目前預覽中的 OMS 中建立管理解決方案的初步的文件。 任何如下所述的結構描述會有所變更。   

[管理解決方案中 OMS](operations-management-suite-solutions.md)通常會併入 runbooks Azure 自動化，這樣便可自動化程序，例如收集並處理監控的資料。  除了 runbooks，自動化帳戶包含資產，如變數及支援使用 runbooks 方案中的排程。  本文將說明如何在方案中加入 runbooks 與他們相關的資源。

>[AZURE.NOTE]本文中的範例使用參數和 [必要] 或 [通用管理解決方案並[建立管理解決方案作業管理套件 (OMS) 中](operations-management-suite-solutions-creating.md)所述的變數 


## <a name="prerequisites"></a>必要條件
本文假設您已經熟悉如何與[建立管理解決方案](operations-management-suite-solutions-creating.md)的和結構解決方案檔案。

## <a name="samples"></a>範例
您可以取得範例資源管理員範本自動化資源從[GitHub 中的快速入門範本](https://github.com/azureautomation/automation-packs/tree/master/101-sample-automation-resource-templates)。

## <a name="automation-account"></a>自動化帳戶
Azure 自動化中的所有資源都包含在[自動化帳戶](../automation/automation-security-overview.md#automation-account-overview)。  [OMS 工作區和自動化帳戶](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account)中所述的自動化帳戶不包含在管理解決方案，但必須先安裝方案存在。  如果不提供，將會失敗解決方案安裝。

自動化帳戶的名稱會在每個自動化資源名稱。  這是與**accountName**參數，如下列範例 runbook 資源的解決方案。
    
    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooks
[Azure 自動化 runbook](../automation/automation-runbook-types.md)資源**Microsoft.Automation/automationAccounts/runbooks**類型，並在下表中包含的內容。

| 屬性 | 描述 |
|:--|:--|
| runbookType | 指定 runbook 的類型。 <br><br> 指令碼的 PowerShell 指令碼 <br>PowerShell-PowerShell 工作流程 <br> GraphPowerShell-圖形的 PowerShell 指令碼 runbook <br> GraphPowerShellWorkflow-圖形 PowerShell 工作流程 runbook   |
| logProgress | 指定是否要[進度記錄](../automation/automation-runbook-output-and-messages.md)產生的 runbook。 |
| logVerbose  | 指定是否應該產生 runbook 的[詳細資訊的記錄](../automation/automation-runbook-output-and-messages.md)。 |
| 描述 | Runbook 選擇性描述。 |
| publishContentLink | 指定 runbook 的內容。 <br><br>uri-Uri runbook 的內容。  這是 PowerShell 和指令碼 runbooks.ps1 檔案與圖形 runbook 匯出圖形 runbook 檔案。  <br> 版本-您自己的追蹤 runbook 的版本。 |

以下是 runbook 資源的範例  在此情況下，它會擷取 runbook [PowerShell 圖庫](https://www.powershellgallery.com)中。

    "name": "[concat(parameters('accountName'), '/Start-AzureV2VMs'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]"
    ],
    "tags": { },
    "properties": {
        "runbookType": "PowerShell",
        "logProgress": "true",
        "logVerbose": "true",
        "description": "",
        "publishContentLink": {
            "uri": "https://www.powershellgallery.com/api/v2/items/psscript/package/Update-ModulesInAutomationToLatestVersion/1.0/Start-AzureV2VMs.ps1",
            "version": "1.0"
        }
    }

### <a name="starting-a-runbook"></a>啟動 runbook
有兩種方法可以開始 runbook 管理方案中。

- 若要安裝解決方案時，請開始 runbook，建立[工作資源](#automation-jobs)，如下所述。
- 若要開始 runbook 排程，請建立一個[排程資源](#schedules)，如下所述。 


## <a name="automation-jobs"></a>自動化工作
若要啟動 runbook，管理方案安裝時，您可以建立**工作**資源。  工作資源**Microsoft.Automation/automationAccounts/jobs**類型，並在下表中包含的內容。

| 屬性 | 描述 |
|:--|:--|
| runbook    | 單一**名稱**實體 runbook 的名稱。  |
| 參數 | 每個參數 runbook 所需的實體。 |


工作包括 runbook 名稱] 和 [傳送到 runbook 任何參數值。  工作必須[取決於](operations-management-suite-solutions-creating.md#resources)作業之前，必須建立啟動自 runbook runbook。  您也可以建立相依性 runbooks 必須完成之前的目前這一版的其他工作。

工作資源的名稱必須包含 GUID 這通常由參數分派。  您可以閱讀更多關於 GUID 參數中[建立的解決方案中作業管理套件 (OMS)](operations-management-suite-solutions-creating.md#parameters)。  

下列是工作資源時所啟動的 runbook 管理方案已安裝的範例。  其他 runbooks 必須完成前這一一開始，，讓它具有相依性的 runbook 的工作。  透過參數和變數而非直接指定提供工作的詳細資料。

    {
        "name": "[concat(parameters('accountName'), '/', parameters('startVmsRunbookGuid'))]",
        "type": "Microsoft.Automation/automationAccounts/jobs",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "location": "[parameters('regionId')]",
        "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]",
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/jobs/', parameters('initialPrepRunbookGuid'))]"
        ],
        "tags": { },
        "properties": {
            "runbook": {
                "name": "[variables('startVmsRunbookName')]"
            },
            "parameters": {
                "AzureConnectionAssetName": "[variables('AzureConnectionAssetName')]",
                "ResourceGroupName": "[variables('ResourceGroupName')]"
            }
        }
    }


## <a name="certificates"></a>憑證
[Azure 自動化憑證](../automation/automation-certificates.md) **Microsoft.Automation/automationAccounts/certificates**類型，並在下表中包含的內容。

| 屬性 | 描述 |
|:--|:--|
| base64Value   | 基底 64 憑證值。 |
| 指紋    | 憑證指紋。 |

憑證資源的範例如下。

    "name": "[concat(parameters('accountName'), '/MyCertificate')]",
    "type": "certificates",
    "apiVersion": "2015-01-01-preview",
    "location": "[parameters('regionId')]",
    "tags": {},
    "dependsOn": [
    ],
    "properties": {
        "base64Value": "MIIC1jCCAA8gAwIAVgIYJY4wXCXH/YAHMtALh7qEFzANAgkqhkiG5w0AAQUFGDAUMRIwEBYDVQQDEwlsA3NhAGevc3QqHhcNMTZwNjI5MHQxMjAsWhcNOjEwNjI5NKWwMDAwWjAURIwEAYDVQQBEwlsA2NhAGhvc3QwggEiMA0GCSqGSIA3DQEAAQUAA4IADwAwggEKAoIAAQDIyzv2A0RUg1/AAryI9W1DGAHAqqGdlFfTkUSDfv+hEZTAwKv0p8daqY6GroT8Du7ctQmrxJsy8JxIpDWxUaWwXtvv1kR9eG9Vs5dw8gqhjtOwgXvkOcFdKdQwA82PkcXoHlo+NlAiiPPgmHSELGvcL1uOgl3v+UFiiD1ro4qYqR0ITNhSlq5v2QJIPnka8FshFyPHhVtjtKfQkc9G/xDePW8dHwAhfi8VYRmVMmJAEOLCAJzRjnsgAfznP8CZ/QUczPF8LuTZ/WA/RaK1/Arj6VAo1VwHFY4AZXAolz7xs2sTuHplVO7FL8X58UvF7nlxq48W1Vu0l8oDi2HjvAgMAAAGjJDAiMAsGA1UdDwREAwIEsDATAgNVHSUEDDAKAggrAgEFNQcDATANAgkqhkiG9w0AAQUFAAOCAQEAk8ak2A5Ug4Iay2v0uXAk95qdAthJQN5qIVA13Qay8p4MG/S5+aXOVz4uMXGt18QjGds1A7Q8KDV4Slnwn95sVgA5EP7akvoGXhgAp8Dm90sac3+aSG4fo1V7Y/FYgAgpEy4C/5mKFD1ATeyyhy3PmF0+ZQRJ7aLDPAXioh98LrzMZr1ijzlAAKfJxzwZhpJamAwjZCYqiNZ54r4C4wA4QgX9sVfQKd5e/gQnUM8gTQIjQ8G2973jqxaVNw9lZnVKW3C8/QyLit20pNoqX2qQedwsqg3WCUcPRUUqZ4NpQeHL/AvKIrt158zAfU903yElAEm2Zr3oOUR4WfYQ==",
        "thumbprint": "F485CBE5569F7A5019CB68D7G6D987AC85124B4C"
    }

## <a name="credentials"></a>認證
[Azure 自動化認證](../automation/automation-credentials.md) **Microsoft.Automation/automationAccounts/credentials**類型，與下表中包含的內容。

| 屬性 | 描述 |
|:--|:--|
| 使用者名稱   | 認證的使用者名稱。 |
| 密碼   | 認證的密碼。 |

認證資源的範例如下。

    "name": "[concat(parameters('accountName'), '/', variables('credentialName'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks/credentials",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "userName": "User01",
        "password": "password"
    }


## <a name="schedules"></a>排程
[Azure 自動化排程](../automation/automation-schedules.md) **Microsoft.Automation/automationAccounts/schedules**類型，並在下表中包含的內容。

| 屬性 | 描述 |
|:--|:--|
| 描述 | 排程的選擇性描述。 |
| 開始時間   | 指定的日期時間物件的排程開始時間。 如果轉為有效的日期時間時，可提供字串。 |
| isEnabled   | 指定是否要啟用排程。 |
| 間隔    | 排程的間隔類型。<br><br>一天<br>小時 |
| 頻率   | 排程應該 fire 中的日數或時間的頻率。 |

排程資源的範例如下。

    "name": "[concat(parameters('accountName'), '/', variables('variableName'))]",
    "type": "microsoft.automation/automationAccounts/schedules",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Schedule for StartByResourceGroup runbook",
        "startTime": "10/30/2016 12:00:00",
        "isEnabled": "true",
        "interval": "day",
        "frequency": "1"
    }


## <a name="variables"></a>變數
[Azure 自動化變數](../automation/automation-variables.md) **Microsoft.Automation/automationAccounts/variables**類型，並在下表中包含的內容。

| 屬性 | 描述 |
|:--|:--|
| 描述 | 變數的選擇性描述。 |
| isEncrypted | 指定是否應該加密變數。 |
| 類型        | 變數的資料類型。 |
| 值       | 變數值。 |

以下是變數資源的範例

    "name": "[concat(parameters('accountName'), '/', variables('StartTargetResourceGroupsAssetName')) ]",
    "type": "microsoft.automation/automationAccounts/variables",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Description for the variable.",
        "isEncrypted": "true",
        "type": "string",
        "value": "'This is a string value.'"
    }


## <a name="modules"></a>模組
管理方案不需要定義使用您 runbooks，因為它們永遠都可以[共用模組](../automation/automation-integration-modules.md)。  您需要加入的任何其他使用您的 runbooks 的模組資源和 runbook 應該取決於模組資源，以確保建立 runbook 之前。

[整合模組](../automation/automation-integration-modules.md) **Microsoft.Automation/automationAccounts/modules**類型，並在下表中包含的內容。

| 屬性 | 描述 |
|:--|:--|
| contentLink | 指定在模組的內容。 <br><br>uri-Uri runbook 的內容。  這是 PowerShell 和指令碼 runbooks.ps1 檔案與圖形 runbook 匯出圖形 runbook 檔案。  <br> 版本-您自己的追蹤 runbook 的版本。 |

模組資源的範例如下。

    {       
        "name": "[concat(parameters('accountName'), '/', variables('OMSIngestionModuleName'))]",
        "type": "Microsoft.Automation/automationAccounts/modules",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "properties": {
            "contentLink": {
                "uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
            }
        }
    }

### <a name="updating-modules"></a>更新模組
如果您更新管理解決方案，包括使用排程，runbook 方案的新版本有新的模組使用該 runbook，runbook 可能會使用舊版本的模組。  您應該在您的方案中加入下列 runbooks，並建立並執行任何其他 runbooks 之前的工作。  這樣就能確定模組會更新為必要之前 runbooks 會載入。

- [更新 ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript)可確保所有 runbooks 方案中所使用的模組都是最新版本。  
- [ReRegisterAutomationSchedule-MS-管理](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript)將登錄的所有排程資源，以確保的 runbooks 連結至使用最新的模組。


下列是必要的項目，以支援模組更新的解決方案的範例。
    
    "parameters": {
        "ModuleImportGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        },
        "ReRegisterRunbookGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        }
    },

    "variables": {
        "ModuleImportRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ModuleImportRunbookUri": "https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/Content/Update-ModulesInAutomationToLatestVersion.ps1",
        "ModuleImportRunbookDescription": "Imports modules and also updates all Azure dependent modules that are in the same Automation Account.",
        "ReRegisterSchedulesRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ReRegisterSchedulesRunbookUri": "https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/Content/ReRegisterAutomationSchedule-MS-Mgmt.ps1",
        "ReRegisterSchedulesRunbookDescription": "Reregisters schedules to ensure that they use latest modules."
    },

    "resources": [
        {
            "name": "[concat(parameters('accountName'), '/', variables('ModuleImportRunbookName'))]",
            "type": "Microsoft.Automation/automationAccounts/runbooks",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "dependsOn": [
            ],
            "location": "[parameters('regionId')]",
            "tags": { },
            "properties": {
                "runbookType": "PowerShell",
                "logProgress": "true",
                "logVerbose": "true",
                "description": "[variables('ModuleImportRunbookDescription')]",
                "publishContentLink": {
                    "uri": "[variables('ModuleImportRunbookUri')]",
                    "version": "1.0.0.0"
                }
            }
        },
        {
            "name": "[concat(parameters('accountName'), '/', parameters('ModuleImportGuid'))]",
            "type": "Microsoft.Automation/automationAccounts/jobs",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "location": "[parameters('regionId')]",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ModuleImportRunbookName'))]"
            ],
            "tags": { },
            "properties": {
                "runbook": {
                    "name": "[variables('ModuleImportRunbookName')]"
                },
                "parameters": {
                    "ResourceGroupName": "[resourceGroup().name]",
                    "AutomationAccountName": "[parameters('accountName')]",
                    "NewModuleName": "AzureRM.Insights"
                }
            }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('ReRegisterSchedulesRunbookName'))]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "PowerShell",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('ReRegisterSchedulesRunbookDescription')]",
            "publishContentLink": {
              "uri": "[variables('ReRegisterSchedulesRunbookUri')]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', parameters('reRegisterSchedulesGuid'))]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ReRegisterSchedulesRunbookName'))]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('ReRegisterSchedulesRunbookName')]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
        }
    ]


## <a name="next-steps"></a>後續步驟

- 若要以視覺化方式呈現收集的資料[新增至您的方案檢視](operations-management-suite-solutions-resources-views.md)。