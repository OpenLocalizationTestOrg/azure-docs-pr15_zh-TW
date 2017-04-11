<properties
   pageTitle="撰寫副檔名為 Linux VM 範本 |Microsoft Azure"
   description="深入了解撰寫 Linux Vm 副檔名為 Azure 資源管理員範本"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="authoring-azure-resource-manager-templates-with-linux-vm-extensions"></a>撰寫副檔名為 Linux VM Azure 資源管理員範本

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

從 Azure CLI 中，執行下列達︰

      Azure VM extension list

這個命令傳回的 publisher 名稱、 副檔名和為下列的版本︰

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

這三個屬性對應至 「 publisher 」、 「 類型 」 和 「 typeHandlerVersion 」 分別在上述範本程式碼片段。

>[AZURE.NOTE]永遠建議使用最新的擴充版本，以取得最更新的功能。

## <a name="identifying-the-schema-for-the-extension-configuration-parameters"></a>識別副檔名設定參數的結構描述

與撰寫擴充範本下一步是識別提供設定參數的格式。 每個延伸支援組自己的參數。

若要查看 Linux 延伸範例設定]，按一下文件，請參閱[Linux eExtensions 範例](virtual-machines-linux-extensions-configuration-samples.md)。

請參閱下列命令以取得完全完成 VM 副檔名為範本。

[自訂指令碼 Linux VM 副檔名](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

之後撰寫範本，您可以將其使用 Azure CLI 部署。
