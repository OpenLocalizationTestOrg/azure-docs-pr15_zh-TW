<properties
   pageTitle="檢視與 Azure CLI 部署作業 |Microsoft Azure"
   description="說明如何使用 Azure CLI 偵測資源管理員部署中的問題。"
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
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-cli"></a>Azure CLI 檢視部署作業

> [AZURE.SELECTOR]
- [入口網站](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
- [REST API](resource-manager-troubleshoot-deployments-rest.md)

如果您收到錯誤 Azure 部署資源時，可能要查看更多詳細資料所執行的部署作業。 Azure CLI 提供命令可讓您尋找錯誤，判斷潛在的修正。

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

您避免發生錯誤，請驗證您的範本和部署之前的基礎結構。 您也可以記錄其他要求與回應可能會很有幫助稍後進行疑難排解的部署期間的資訊。 若要瞭解驗證及記錄邀請及回應的資訊，請參閱[部署 Azure 資源管理員範本的資源群組](resource-group-template-deploy-cli.md)。

## <a name="use-audit-logs-to-troubleshoot"></a>若要疑難排解使用稽核記錄

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

若要查看部署錯誤，請使用下列步驟︰

1. 稽核記錄，請執行 [ **azure 群組記錄檔顯示**的命令。 您可以包含**，最後一個部署**擷取的最新的部署記錄檔] 選項。

        azure group log show ExampleGroup --last-deployment

2. **顯示 azure 群組記錄檔**] 命令會傳回許多的資訊。 疑難排解，您通常要著重於作業失敗。 下列指令碼使用**-json**選項和[jq](https://stedolan.github.io/jq/) JSON 公用程式來搜尋部署失敗的記錄。

        azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'
        
        {
        "claims": {
          "aud": "https://management.core.windows.net/",
          "iss": "https://sts.windows.net/<guid>/",
          "iat": "1442510510",
          "nbf": "1442510510",
          "exp": "1442514410",
          "ver": "1.0",
          "http://schemas.microsoft.com/identity/claims/tenantid": "{guid}",
          "http://schemas.microsoft.com/identity/claims/objectidentifier": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
          "puid": "XXXXXXXXXXXXXXXX",
          "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",
          "altsecid": "1:example.com:XXXXXXXXXXX",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string="">",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
          "name": "Tom FitzMacken",
          "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
          "groups": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
          "wids": "{guid}",
          "appid": "{guid}",
          "appidacr": "0",
          "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
          "http://schemas.microsoft.com/claims/authnclassreference": "1",
          "ipaddr": "000.000.000.000"
        },
        "properties": {
          "statusCode": "Conflict",
          "statusMessage": "{\"Code\":\"Conflict\",\"Message\":\"Website with given name mysite already exists.\",\"Target\":null,\"Details\":[{\"Message\":\"Website with given name
            mysite already exists.\"},{\"Code\":\"Conflict\"},{\"ErrorEntity\":{\"Code\":\"Conflict\",\"Message\":\"Website with given name mysite already exists.\",\"ExtendedCode\":
            \"54001\",\"MessageTemplate\":\"Website with given name {0} already exists.\",\"Parameters\":[\"mysite\"],\"InnerErrors\":null}}],\"Innererror\":null}"
        },
        ...

    您可以查看**內容**包含 json 失敗作業的相關資訊。

    您可以使用**-詳細**與**-vv**選項]，請參閱從記錄其他資訊。  使用**-詳細**選項以顯示的步驟，將作業移到`stdout`。 完成要求歷程記錄中，使用**-vv**選項。 訊息通常會提供相當線索失敗的原因。

3. 若要鎖定失敗項目的 [狀態] 訊息，請使用下列命令︰

        azure group log show ExampleGroup --json | jq -r ".[] | select(.status.value == \"Failed\") | .properties.statusMessage"


## <a name="use-deployment-operations-to-troubleshoot"></a>使用部署作業來疑難排解

1. 取得**azure 群組部署顯示**命令的部署的整體狀態。 在下面的範例部署失敗。

        azure group deployment show --resource-group ExampleGroup --name ExampleDeployment
        
        info:    Executing command group deployment show
        + Getting deployments
        data:    DeploymentName     : ExampleDeployment
        data:    ResourceGroupName  : ExampleGroup
        data:    ProvisioningState  : Failed
        data:    Timestamp          : 2015-08-27T20:03:34.9178576Z
        data:    Mode               : Incremental
        data:    Name             Type    Value
        data:    ---------------  ------  ------------
        data:    siteName         String  ExampleSite
        data:    hostingPlanName  String  ExamplePlan
        data:    siteLocation     String  West US
        data:    sku              String  Free
        data:    workerSize       String  0
        info:    group deployment show command OK

2. 若要查看的部署作業失敗訊息，請使用︰

        azure group deployment operation list --resource-group ExampleGroup --name ExampleDeployment --json  | jq ".[] | select(.properties.provisioningState == \"Failed\") | .properties.statusMessage.Message"


## <a name="next-steps"></a>後續步驟

- 解決特定部署錯誤的說明，請參閱[部署 Azure 與 Azure 資源管理員資源時的常見錯誤的解決方法](resource-manager-common-deployment-errors.md)。
- 若要瞭解如何使用稽核記錄監控其他類型的動作，請參閱[稽核作業與資源管理員](resource-group-audit.md)。
- 若要驗證您的部署執行之前，請參閱[部署 Azure 資源管理員範本的資源群組](resource-group-template-deploy.md)。
