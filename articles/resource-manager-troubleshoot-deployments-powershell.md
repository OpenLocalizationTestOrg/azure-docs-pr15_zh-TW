<properties
   pageTitle="檢視使用 PowerShell 部署作業 |Microsoft Azure"
   description="說明如何使用 PowerShell 的 Azure 偵測資源管理員部署中的問題。"
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/14/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-powershell"></a>使用 PowerShell 的 Azure 檢視部署作業

> [AZURE.SELECTOR]
- [入口網站](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
- [REST API](resource-manager-troubleshoot-deployments-rest.md)

您可以檢視透過 PowerShell 的 Azure 部署的作業。 您可能會是最感興趣檢視作業，當您收到錯誤部署時，本文著重於檢視作業已失敗。 PowerShell 提供可讓您輕鬆地找到錯誤，並決定潛在修正的 cmdlet。

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

您避免發生錯誤，請驗證您的範本和部署之前的基礎結構。 您也可以記錄其他要求與回應可能會很有幫助稍後進行疑難排解的部署期間的資訊。 若要瞭解驗證及記錄邀請及回應的資訊，請參閱[部署 Azure 資源管理員範本的資源群組](resource-group-template-deploy.md)。

## <a name="use-deployment-operations-to-troubleshoot"></a>使用部署作業來疑難排解

1. 若要部署的整體狀態，請使用 [**取得 AzureRmResourceGroupDeployment**命令。 您可以篩選僅失敗的部署的結果。

        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
    以下列格式傳回的部署失敗︰
        
        DeploymentName          : Microsoft.Template
        ResourceGroupName       : ExampleGroup
        ProvisioningState       : Failed
        Timestamp               : 6/14/2016 9:55:21 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                    Name                Type                 Value
                    ===============     ===================  ==========
                    storageAccountName  String               tfstorage9855
                    adminUsername       String               tfadmin
                    adminPassword       SecureString
                    dnsNameforLBIP      String               dns
                    vmNamePrefix        String               myVM
                    imagePublisher      String               MicrosoftWindowsServer
                    imageOffer          String               WindowsServer
                    imageSKU            String               2012-R2-Datacenter
                    lbName              String               myLB
                    nicNamePrefix       String               nic
                    publicIPAddressName String               myPublicIP
                    vnetName            String               myVNET
                    vmSize              String               Standard_D1

        Outputs                 :
        DeploymentDebugLogLevel :

2. 每個部署通常組成的多個作業，代表部署程序中的步驟操作。 若要探索錯在哪裡部署，您通常需要部署作業的相關詳細資料。 您可以看到與**取得 AzureRmResourceGroupDeploymentOperation**作業的狀態。

        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName Microsoft.Template
        
    會傳回以下列格式中的每一個多項作業︰
        
        Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
        OperationId    : A3EB2DA598E0A780
        Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                         duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                         serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
        PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                         serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}

3. 若要取得更多詳細資料作業失敗，擷取作業**失敗**狀態的屬性。

        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
        
    這會傳回所有使用下列格式中的每一個失敗的作業︰
        
        provisioningOperation : Create
        provisioningState     : Failed
        timestamp             : 2016-06-14T21:54:55.1468068Z
        duration              : PT3.1449887S
        trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
        serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
        statusCode            : BadRequest
        statusMessage         : @{error=}
        targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                                Microsoft.Network/publicIPAddresses/myPublicIP;
                                resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}

    請注意作業的追蹤識別碼。 您會在下一個步驟中使用，將焦點集中在特定的作業。

4. 若要取得作業失敗的狀態訊息，請使用下列命令︰

        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
        
    傳回的︰
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}

## <a name="use-audit-logs-to-troubleshoot"></a>若要疑難排解使用稽核記錄

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

若要查看部署錯誤，請使用下列步驟︰

1. 若要擷取的記錄項目，請執行**取得 AzureRmLog** ] 命令。 您可以使用**ResourceGroup**及**狀態**參數傳回單一資源群組失敗的事件。 如果您未指定的開始和結束時間，則會傳回最後一個小時的項目。
例如，若要擷取過去小時作業失敗執行︰

        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    您可以指定特定的時段。 在下一個範例中，我們會尋找失敗的動作的最後一天。 

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -Status Failed
      
    或者，您可以設定的確切的開始和結束時間失敗的動作︰

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. 如果這個命令傳回太多項目和內容，您可以業務稽核擷取**Properties**屬性。 我們也會包含**DetailedOutput**參數，若要查看的錯誤訊息。

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -DetailedOutput).Properties
        
    會傳回記錄項目的屬性，以下列格式︰
        
        Content
        -------
        {} 
        {[statusCode, BadRequest], [statusMessage, {"error":{"code":"DnsRecordInUse","message":"DNS record dns.westus.clouda...
        {[statusCode, BadRequest], [serviceRequestId, a426f689-5d5a-448d-a2f0-9784d14c900a], [statusMessage, {"error":{"code...

3. 根據這些結果，我們著重於第二個項目。 您可以查看該項目的狀態訊息，進一步縮小結果。

        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput -StartTime (Get-Date).AddDays(-1)).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
        
    傳回的︰
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}



## <a name="next-steps"></a>後續步驟

- 解決特定部署錯誤的說明，請參閱[部署 Azure 與 Azure 資源管理員資源時的常見錯誤的解決方法](resource-manager-common-deployment-errors.md)。
- 若要瞭解如何使用稽核記錄監控其他類型的動作，請參閱[稽核作業與資源管理員](resource-group-audit.md)。
- 若要驗證您的部署之前執行，請參閱[部署 Azure 資源管理員範本的資源群組](resource-group-template-deploy.md)。

