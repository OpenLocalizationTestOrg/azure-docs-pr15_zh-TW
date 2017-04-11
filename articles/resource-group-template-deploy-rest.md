<properties
   pageTitle="部署資源 REST API 與範本 |Microsoft Azure"
   description="使用 Azure 資源管理員] 及 [資源管理員 REST API 來部署資源至 Azure。 資源管理員範本中定義的資源。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/11/2016"
   ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>部署資源與資源管理員範本與資源管理員 REST API

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Azure CLI](resource-group-template-deploy-cli.md)
- [入口網站](resource-group-template-deploy-portal.md)
- [REST API](resource-group-template-deploy-rest.md)

本文說明如何使用資源管理員範本中的資源管理員 REST API，若要將您的資源部署至 Azure。  

> [AZURE.TIP] 部署期間偵錯時，發生錯誤的說明，請參閱︰
>
> - 若要深入瞭解取得可協助您的資訊[以 REST API 檢視部署作業](resource-manager-troubleshoot-deployments-rest.md)疑難排解錯誤
> - 若要瞭解如何解決常見的部署錯誤的[疑難排解常見錯誤時部署 Azure 與 Azure 資源管理員資源](resource-manager-common-deployment-errors.md)

本機的檔案或透過 URI 提供的外部檔案，可以是您的範本。 儲存帳戶，位於您的範本，可以限制存取至範本，並提供共用的 access 簽章 (SA) 權杖部署期間。

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-the-rest-api"></a>部署以 REST API
1. 設定[一般參數和標題](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common)，包括驗證的權杖。
2. 如果您沒有現有的資源群組，請建立資源群組。 提供您的訂閱識別碼、 新資源] 群組中，與您的方案，您需要的位置的名稱。 如需詳細資訊，請參閱[建立資源群組]](https://msdn.microsoft.com/library/azure/dn790525.aspx)。

        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
   
3. 驗證您的部署之前執行[驗證範本部署](https://msdn.microsoft.com/library/azure/dn790547.aspx)作業。 當測試部署，提供的參數，相同的方式時執行部署工作 （在下一個步驟所示）。

3. 建立部署。 提供您的訂閱識別碼，部署名稱部署，以及連結至您的範本的 [資源] 群組的名稱。 將範本檔案的相關資訊，請參閱[參數檔案](#parameter-file)。 如需有關 REST API，若要建立資源群組的詳細資訊，請參閱[建立範本部署](https://msdn.microsoft.com/library/azure/dn790564.aspx)。 請注意**模式**設為 [**遞**。 若要執行完整的部署，請為 「**完成**設定**模式**。 請小心時使用完整的模式，可以不小心刪除不在範本中的資源。
    
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
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
              }
            }
          }
   
      如果您想要記錄的回應內容，或兩個要求的內容，請在邀請中包含**debugSetting** 。

        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        }

      您可以使用共用的 access 簽章 (SA) 權杖來設定您儲存的帳戶。 如需詳細資訊，請參閱[委派存取使用共用的 Access 簽章](https://msdn.microsoft.com/library/ee395415.aspx)。

4. 取得範本部署的狀態。 如需詳細資訊，請參閱[取得範本部署的相關資訊](https://msdn.microsoft.com/library/azure/dn790565.aspx)。

          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>後續步驟
- 部署資源的.NET 用戶端文件庫的範例，請參閱[部署資源使用.NET 文件庫和範本](virtual-machines/virtual-machines-windows-csharp-template.md)。
- 若要在範本中定義的參數，請參閱[撰寫範本](resource-group-authoring-templates.md#parameters)。
- 在不同環境中部署您的方案，請參閱[開發和 Microsoft Azure 中的測試環境](solution-dev-test-environments.md)。
- 如需使用 KeyVault 參考傳遞安全值的詳細資訊，請參閱[傳遞安全部署期間的值](resource-manager-keyvault-parameter.md)。
