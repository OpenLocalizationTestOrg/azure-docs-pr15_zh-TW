<properties
    pageTitle="Azure 堆疊的工具和 PaaS 服務 |Microsoft Azure"
    description="瞭解如何開始使用 PaaS Azure 堆疊的服務。"
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="erikje"/>

# <a name="tools-for-azure-stack"></a>Azure 堆疊的工具

您可以下載如下所述的工具。 如果您想要的新服務及工具時收到通知，請遵循 #AzureStack，Twitter 上。

## <a name="template-tools"></a>範本工具

### <a name="azure-stack-github-templates"></a>Azure 堆疊 Github 範本
瀏覽成長的[Azure 堆疊 GitHub 範本](https://github.com/Azure/AzureStack-QuickStart-Templates)集合中。 就像[Azure](https://github.com/Azure/azure-quickstart-templates)，這些 」 快速啟動] Azure 資源管理員範本的目標您開始使用簡單的建置組塊和範例，可以將 Microsoft Azure 堆疊技術預覽證明的概念環境部署。 其中包含的第一方工作負載的範例[ad-非-ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha)， [sql-2014年-非-ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha)， [sharepoint-2013年-非-ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha)，如也以多個簡單的 101 範本，例如[101 簡單-windows vm](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-simple-windows-vm)。


### <a name="marketplace-item-packaging-tool-and-sample"></a>服務商場的項目包裝工具] 及 [範例
[下載及使用包裝工具](http://www.aka.ms/azurestackmarketplaceitem)來建立您自己的自訂範本，以新增至 Azure 堆疊服務商場的服務商場項目。 如何建立服務商場項目，並將您的租用戶供指示可以[建立服務商場項目](azure-stack-create-and-publish-marketplace-item.md)中找到。

## <a name="developer-tools"></a>開發人員工具


### <a name="use-visual-studio-and-azure-stack-tp2-on-the-mas-con01-virtual-machine"></a>使用 Visual Studio 和 Azure 堆疊 TP2 MAS CON01 虛擬機器
如果您想要使用 Visual Studio 主控台 VM 上使用 Azure 堆疊範本，您必須安裝正確的版本的必要工具。 使用下列程序來安裝受支援的版本的 TP2。

1. 使用遠端桌面連線到 MAS CON01 虛擬機器使用 azurestack\azurestackadmin 的認證登入。
2. 安裝並開啟 Web 平台安裝程式。
3. 尋找並安裝**Microsoft Azure SDK 2.9.5 與 Visual Studio 社群 2015年**。
4. 使用 [**新增/移除程式**，請解除安裝**Microsoft Azure PowerShell （年 9 月）**安裝時 SDK 的一部分。
5. 開啟網頁的平台安裝程式。
6. 尋找並安裝**Microsoft Azure PowerShell 的 Azure 堆疊技術預覽 2**。 
7. 重新啟動 MAS CON01 虛擬機器。
8. 使用遠端桌面連線到 MAS CON01 虛擬機器使用 azurestack\azurestackadmin 的認證登入。
9. 開啟 Visual Studio 和驗證，您可以連接到 Azure 堆疊環境，取得範本，依此類推。 

### <a name="azure-powershell-sdk"></a>Azure PowerShell SDK
Azure PowerShell 是提供 cmdlet 來管理 Azure 和使用 Windows PowerShell 的 Azure 堆疊的模組。 您可以使用 cmdlet 來建立、 測試、 部署，並管理解決方案與服務透過 Azure 堆疊平台。
[下載 Azure PowerShell SDK](http://aka.ms/azStackPsh) ，而且[安裝 PowerShell](azure-stack-connect-powershell.md)。

### <a name="azure-cross-platform-command-line-interfaces"></a>Azure 交叉平台命令列介面
快速安裝 Azure 命令列介面 (Azure CLI) 建立及管理資源 Microsoft Azure 堆疊中的使用一組開啟來源命令介面為基礎的命令。

[下載 Windows CLI](http://aka.ms/azstack-windows-cli)

[下載 Mac CLI](http://aka.ms/azstack-linux-cli)

[下載 Linux CLI](http://aka.ms/azstack-mac-cli)

>[AZURE.NOTE]
>
> + 如果您是在 Mac 或 Linux 電腦上，您也可以取得 CLI 使用命令`npm install -g azure-cli@0.9.11`</br>
> + 如果您收到憑證驗證問題，請執行命令`set NODE_TLS_REJECT_UNAUTHORIZED=0`
