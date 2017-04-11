<properties
    pageTitle="自動啟用 [診斷設定使用資源管理員範本 |Microsoft Azure"
    description="瞭解如何使用資源管理員範本來建立診斷設定，可讓您將串流事件到您診斷記錄，或將它們儲存在儲存帳戶。"
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="johnkem"/>

# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>自動在使用資源管理員範本建立資源時啟用診斷設定
本文中，我們會示範如何使用[Azure 資源管理員範本](../resource-group-authoring-templates.md)來建立時，資源設定診斷設定。 這可讓您串流您診斷記錄和指標到事件，封存儲存帳戶，或傳送至記錄分析建立資源時，自動啟動。

啟用範本資源管理員的診斷記錄的方式取決於資源類型。

- **非計算**資源 （例如，網路安全性群組的邏輯應用程式，自動化） 使用[本文中所述診斷設定](./monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)。
- **計算**（WAD/小為基礎） 資源使用[本文所述的 WAD/小設定檔](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)。

本文中，我們會說明如何設定診斷使用方法。

基本步驟如下所示︰

1. 說明如何建立資源的成本，並啟用診斷 JSON 檔案建立的範本。
2. [部署使用任何部署方法的範本](../resource-group-template-deploy.md)。

以下我們為您需要產生非計算及計算資源的範本 JSON 檔案的範例。

## <a name="non-compute-resource-template"></a>非計算資源的範本
非計算資源，您將需要執行兩個動作︰

1. 加入參數，參數 blob 儲存體帳戶名稱、 服務匯流排規則識別碼，及/或 OMS 記錄分析工作區識別碼 （啟用診斷記錄的儲存空間帳戶，串流的記錄檔給事件集線器，及/或將記錄傳送給記錄分析封存）。

    ```json
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. 新增您要啟用診斷記錄資源的資源陣列中的 [類型的資源`[resource namespace]/providers/diagnosticSettings`。

    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/service",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2015-07-01",
        "properties": {
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
          "workspaceId": "[parameters('workspaceId')]",
          "logs": [ 
            {
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ]
        }
      }
    ]
    ```

[診斷設定屬性 blob 遵循[本文中所述的格式](https://msdn.microsoft.com/library/azure/dn931931.aspx)。

以下是完整的範例會建立網路安全性群組，就會開啟至事件集線器，在儲存帳戶中的儲存空間。

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nsgName": {
      "type": "string",
      "metadata": {
        "description": "Name of the NSG that will be created."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "[parameters('nsgName')]",
      "apiVersion": "2016-03-30",
      "location": "westus",
      "properties": {
        "securityRules": []
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "Microsoft.Insights/service",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgName'))]"
          ],
          "apiVersion": "2015-07-01",
          "properties": {
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "NetworkSecurityGroupEvent",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              },
              {
                "category": "NetworkSecurityGroupRuleCounter",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```

## <a name="compute-resource-template"></a>計算資源的範本
若要啟用診斷計算資源，例如虛擬機器或服務布料的轉印圖樣叢集，您需要︰

1. 將 VM 資源定義 Azure 診斷副檔名。
2. 指定儲存帳戶及/或事件中樞做為參數。
3. 將 [XMLCfg] 屬性中正確逸出所有的 XML 字元 WADCfg XML 檔案的內容。

> [AZURE.WARNING] 最後一個步驟可能會很難正確的。 [請參閱本文](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md#diagnostics-configuration-variables)的診斷設定結構描述分割變數逸出字元及格式正確的範例。

整個程序，包括範例，是描述如何在[文件中](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)。


## <a name="next-steps"></a>後續步驟
- [閱讀更多關於 Azure 診斷記錄](./monitoring-overview-of-diagnostic-logs.md)
- [串流到事件 Azure 診斷記錄](./monitoring-stream-diagnostic-logs-to-event-hubs.md)
