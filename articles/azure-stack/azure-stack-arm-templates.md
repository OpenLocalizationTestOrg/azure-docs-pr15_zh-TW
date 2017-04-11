<properties
    pageTitle="Azure 堆疊 （租用戶開發人員） 中使用 Azure 資源管理員範本 |Microsoft Azure"
    description="瞭解如何使用 Azure 資源管理員範本 Azure 堆疊的部署及佈建的所有應用程式在單一、 協同作業的資源。"
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
    ms.date="10/25/2016"
    ms.author="helaw"/>

# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Azure 堆疊中使用 Azure 資源管理員範本

Azure 資源管理員範本部署，並提供所有在單一、 協同作業應用程式的資源。 您定義應用程式及如何部署資源。  您也可以重新部署範本，變更 [資源] 群組中的資源。

可以使用 Microsoft Azure 堆疊入口網站、 PowerShell、 命令列及 Visual Studio 部署這些範本。

[AZURE.VIDEO microsoft-azure-stack-tp1--foundational-skills-1-deploying-json-templates]

下列範本上都提供[GitHub](http://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-non-high-availability"></a>部署 SharePoint （非高可用性）

若要建立 SharePoint 2013 伺服器陣列包含下列使用 PowerShell DSC 副檔名為︰

-   虛擬網路

-   三個儲存帳戶

-   兩個外部負載平衡器

-   設定為使用單一網域新樹系網域控制站的一個 VM

-   一個 VM SQL Server 2014 獨立的伺服器設定

-   一個 VM 設為一部電腦的 SharePoint 2013 伺服器陣列

## <a name="deploy-ad-non-high-availability"></a>部署 AD （非高可用性）

若要建立包含下列 AD 網域控制站伺服器使用 PowerShell DSC 副檔名為︰

-   虛擬網路

-   儲存帳戶

-   一個外部負載平衡器

-   設定為使用單一網域新樹系網域控制站的一個 VM

## <a name="deploy-adsql-non-high-availability"></a>部署 AD SQL （非高可用性）

若要建立 SQL Server 2014 獨立伺服器包含下列使用 PowerShell DSC 副檔名為︰

-   虛擬網路

-   兩個儲存帳戶

-   一個外部負載平衡器

-   設定為使用單一網域新樹系網域控制站的一個 VM

-   一個 VM SQL Server 2014 獨立的伺服器設定

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

使用 PowerShell DSC 副檔名來設定現有的虛擬機器組態管理員 (LCM) 和註冊 Azure 自動化帳戶 DSC 提取伺服器。

## <a name="create-a-virtual-machine-from-a-user-image"></a>建立虛擬機器從使用者的圖像

建立虛擬機器從自訂使用者的圖像。 虛擬網路 （DNS)、 公用 IP 位址，以及網路介面，也會部署此範本。

## <a name="simple-vm"></a>簡單 VM

部署簡單的 Windows VM，包括虛擬網路 （DNS)、 公用 IP 位址，以及網路介面。

## <a name="cancel-a-running-template-deployment"></a>取消執行的範本部署

若要取消執行的範本部署，請使用`Stop-AzureRmResourceGroupDeployment`PowerShell 指令程式。


## <a name="next-steps"></a>後續步驟

[部署入口網站範本](azure-stack-deploy-template-portal.md)

[Azure 資源管理員的概觀](../azure-resource-manager/resource-group-overview.md)

