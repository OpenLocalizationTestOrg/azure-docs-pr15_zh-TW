<properties
   pageTitle="使用 Windows VM 副檔名撰寫範本 |Microsoft Azure"
   description="深入瞭解 Windows Vm 撰寫副檔名為 Azure 資源管理員範本"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="authoring-azure-resource-manager-templates-with-windows-vm-extensions"></a>撰寫副檔名為 Windows VM Azure 資源管理員範本

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

從 Azure PowerShell 執行下列 PowerShell 的 Azure cmdlet:

      Get-AzureVMAvailableExtension


這個指令程式會傳回 publisher 名稱、 延伸名稱和版本如下所示︰

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

這三個屬性對應至 「 publisher 」、 「 類型 」 和 「 typeHandlerVersion 」 分別在上述範本程式碼片段。

>[AZURE.NOTE]永遠建議使用最新的擴充版本，以取得最更新的功能。

## <a name="identifying-the-schema-for-the-extension-configuration-parameters"></a>識別副檔名設定參數的結構描述

與撰寫擴充範本下一步是識別提供設定參數的格式。 每個延伸支援組自己的參數。

若要查看 Windows 延伸範例設定，請參閱[Windows 副檔名範例](virtual-machines-windows-extensions-configuration-samples.md)。


請參閱下列命令以取得完全完成 VM 副檔名為範本。

[在 Windows VM 自訂指令碼副檔名](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)


之後撰寫範本，您可以將其使用 PowerShell 的 Azure 部署。
