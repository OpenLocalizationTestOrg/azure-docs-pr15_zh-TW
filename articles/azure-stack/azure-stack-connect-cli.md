<properties
    pageTitle="連線至 Azure 堆疊 CLI |Microsoft Azure"
    description="瞭解如何使用跨平台的命令列介面 (CLI) 可用於管理及部署 Azure 堆疊資源"
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="helaw"/>

# <a name="install-and-configure-azure-stack-cli"></a>安裝和設定 Azure 堆疊 CLI

在此文件中，我們引導您完成程序來管理 Azure 堆疊資源 Linux 和 Mac 的用戶端平台上使用 Azure 命令列介面 (CLI)。  

## <a name="install-azure-stack-cli"></a>安裝 Azure 堆疊 CLI

如果您是在 Mac 或 Linux，您可以取得 CLI 使用下列命令︰
  
    `npm install -g azure-cli@0.10.4`.


## <a name="connect-to-azure-stack"></a>連線至 Azure 堆疊
在下列步驟，您可以設定連線至 Azure 堆疊 Azure CLI。 然後您登入，並擷取訂閱資訊。

1.  藉由執行這個 PowerShell 來擷取 active directory-資源識別碼] 的值︰
        
          (Invoke-RestMethod -Uri https://api.azurestack.local/metadata/endpoints?api-version=1.0 -Method Get).authentication.audiences[0]

2.  使用下列 CLI 命令新增 Azure 堆疊環境中，確保更新*-active directory-資源識別碼*URL 擷取上一個步驟中的資料︰

           azure account env add AzureStack --resource-manager-endpoint-url "https://api.azurestack.local" --management-endpoint-url "https://api.azurestack.local" --active-directory-endpoint-url  "https://login.windows.net" --portal-url "https://portal.azurestack.local" --gallery-endpoint-url "https://portal.azurestack.local" --active-directory-resource-id "https://azurestack.local-api/" --active-directory-graph-resource-id "https://graph.windows.net/"

3.  登入，請使用下列命令 (取代*使用者名稱*與您的使用者名稱):

        azure login -e AzureStack -u “<username>”

    >[AZURE.NOTE]如果您收到憑證驗證問題，來停用憑證驗證執行命令`set        NODE_TLS_REJECT_UNAUTHORIZED=0`。

4.  設定至 Azure 資源管理員 Azure 設定模式，使用下列命令︰

        azure config mode arm

5.  擷取訂閱的清單。

        azure account list     

## <a name="next-steps"></a>後續步驟

[以 Azure CLI 部署範本](azure-stack-deploy-template-command-line.md)

[使用 PowerShell 連線](azure-stack-connect-powershell.md)

[管理使用者權限](azure-stack-manage-permissions.md)
