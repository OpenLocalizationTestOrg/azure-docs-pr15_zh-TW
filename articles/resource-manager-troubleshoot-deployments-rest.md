<properties
   pageTitle="檢視與 REST API 部署作業 |Microsoft Azure"
   description="說明如何使用 Azure 資源管理員 REST API 偵測資源管理員部署中的問題。"
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/13/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-resource-manager-rest-api"></a>檢視部署作業以 Azure 資源管理員 REST API

> [AZURE.SELECTOR]
- [入口網站](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
- [REST API](resource-manager-troubleshoot-deployments-rest.md)

如果您收到錯誤 Azure 部署資源時，可能要查看更多詳細資料所執行的部署作業。 REST API 提供作業，可讓您尋找錯誤，並判斷潛在的修正。

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

您避免發生錯誤，請驗證您的範本和部署之前的基礎結構。 您也可以記錄其他要求與回應可能會很有幫助稍後進行疑難排解的部署期間的資訊。 若要瞭解驗證及記錄邀請及回應的資訊，請參閱[部署 Azure 資源管理員範本的資源群組](resource-group-template-deploy-rest.md)。

## <a name="troubleshoot-with-rest-api"></a>疑難排解以 REST API

1. 部署您[建立的範本部署](https://msdn.microsoft.com/library/azure/dn790564.aspx)作業的資源。 若要保留可能會很有幫助偵錯的資訊，請將**debugSetting**屬性**requestContent**及/或**responseContent**JSON 邀請中。 

        PUT https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0",
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0",      
              },
              "debugSetting": {
                "detailLevel": "requestContent, responseContent"
              }
            }
          }

    根據預設， **debugSetting**值設定為 [**無]**。 指定**debugSetting**值時，請仔細考慮部署期間傳遞中的資訊的類型。 以要求或回應的記錄資訊，您可能無法公開機密資料擷取完成部署作業。 

2. 取得[取得範本部署的相關資訊](https://msdn.microsoft.com/library/azure/dn790565.aspx)的作業的部署的相關資訊。

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}

    在回應中，注意特別**provisioningState**與**相互關聯識別碼****錯誤**的項目。 **相互關聯識別碼**用來追蹤相關的事件，然後使用技術支援，以疑難排解問題時可以很有幫助。
    
        { 
          ...
          "properties": {
            "provisioningState":"Failed",
            "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
            ...
            "error":{
              "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
              "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
            }  
          }
        }

3. 取得部署作業與[範本部署的所有作業] 都清單中](https://msdn.microsoft.com/library/azure/dn790518.aspx)的相關資訊。 

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}

    回應會包含要求及/或回應根據什麼期間所指定**debugSetting** ] 屬性中部署的資訊。
    
        {
          ...
          "properties": 
          {
            ...
            "request":{
              "content":{
                "location":"West US",
                "properties":{
                  "accountType": "Standard_LRS"
                }
              }
            },
            "response":{
              "content":{
                "error":{
                  "message":"Conflict","code":"Conflict"
                }
              }
            }
          }
        }

4. 稽核記錄檔中取得[清單管理中的事件訂閱](https://msdn.microsoft.com/library/azure/dn931934.aspx)作業部署事件。

        GET https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version={api-version}&$filter={filter-expression}&$select={comma-separated-property-names}


## <a name="next-steps"></a>後續步驟

- 解決特定部署錯誤的說明，請參閱[部署 Azure 與 Azure 資源管理員資源時的常見錯誤的解決方法](resource-manager-common-deployment-errors.md)。
- 若要瞭解如何使用稽核記錄監控其他類型的動作，請參閱[稽核作業與資源管理員](resource-group-audit.md)。
- 若要驗證您的部署之前執行，請參閱[部署 Azure 資源管理員範本的資源群組](resource-group-template-deploy.md)。
