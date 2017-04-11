<properties
   pageTitle="建立資源管理員] 中使用範本內部負載平衡器 |Microsoft Azure"
   description="瞭解如何建立使用範本在資源管理員內部負載平衡器"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="create-an-internal-load-balancer-using-a-template"></a>建立使用範本內部負載平衡器

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][傳統的部署模型](load-balancer-get-started-ilb-classic-ps.md)。

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>藉由按一下部署部署範本

公用存放庫中提供的範例範本會使用參數檔案包含的預設值用來產生上述的情形。 若要部署部署，請遵循[此連結](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer)使用按一下此範本，按一下 [**部署至 Azure**，取代預設參數值，如有必要，，依照入口網站中的指示進行。

## <a name="deploy-the-template-by-using-powershell"></a>使用 PowerShell 來部署範本

若要部署使用 PowerShell 您下載的範本，請遵循下列步驟。

1. 如果您從未使用 PowerShell 的 Azure，瞭解[如何安裝和設定 Azure PowerShell](../../articles/powershell-install-configure.md) ，並遵循指示到登入 Azure，然後選取您的訂閱結束。
2. 將參數檔案下載到您的本機磁碟。
3. 編輯檔案，並將其儲存。
4. **新增 AzureRmResourceGroupDeployment**指令程式來建立資源群組使用的範本。

        New-AzureRmResourceGroupDeployment -Name TestRG -Location westus `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
            -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'

## <a name="deploy-the-template-by-using-the-azure-cli"></a>使用 Azure CLI 部署範本

若要使用 Azure CLI 部署範本，請遵循下列步驟。

1. 如果您從未使用 Azure CLI，請參閱[安裝及設定 Azure CLI](../../articles/xplat-cli-install.md) ，然後依照指示操作，您可在此選取您的 Azure 帳戶及訂閱為止。
2. 執行**azure config 模式**命令以切換到資源管理員模式，如下所示。

        azure config mode arm

    以下是預期的輸出上方的命令︰

        info:    New mode is arm

3. 開啟參數檔案，選取其內容，並將其儲存到您的電腦中的檔案。 例如，我們*parameters.json*儲存該參數檔案。

4. 執行**azure 群組部署建立**命令以使用您下載並修改上方的範本參數及參數檔案部署新的內部負載平衡器。 顯示後輸出說明所用的參數的清單。

        azure group create --name TestRG --location westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json --parameters-file parameters.json

## <a name="next-steps"></a>後續步驟

[設定使用來源 IP 相關性負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定您的負載平衡器的空閒 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)



