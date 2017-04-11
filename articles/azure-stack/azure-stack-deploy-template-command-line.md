<properties
    pageTitle="Azure 堆疊的命令列以部署範本 |Microsoft Azure"
    description="瞭解如何使用跨平台的命令列介面 (CLI) 部署範本的 ClientVM 內，或使用 VPN 連線至 Azure 堆疊之後。"
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>部署 Azure 堆疊使用命令列中的範本

若要將 Azure 資源管理員範本部署至 Azure 堆疊 POC 使用命令列。 Azure 資源管理員範本部署，並提供您的應用程式，在單一、 協調運算中的所有資源。

## <a name="download-template"></a>下載範本        
若要測試 CLI 的部署，可以從[建立儲存帳戶範例範本](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account)下載檔案 azuredeploy.json 及 azuredeploy.parameters.json。

## <a name="deploy-template"></a>部署範本
瀏覽至這些檔案已下載並執行下列命令以部署範本位置的資料夾︰

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

這個命令至 [資源群組**cliRG** Azure 堆疊 POC 的預設位置部署範本。

## <a name="validate-template-deployment"></a>驗證範本部署
若要查看此資源群組和儲存的帳戶，請使用下列命令︰

    azure group list

    azure storage account list

## <a name="next-steps"></a>後續步驟

[管理使用者權限](azure-stack-manage-permissions.md)
