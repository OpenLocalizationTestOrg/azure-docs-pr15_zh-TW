<properties
 pageTitle="虛擬機器 extensions 和功能 |Microsoft Azure"
 description="瞭解適用於 Azure 虛擬機器，依什麼他們提供，或是改善分組的副檔名。"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="neilpeterson"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager"/>

<tags
 ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/30/2016"
 ms.author="nepeters"/>

# <a name="about-virtual-machine-extensions-and-features"></a>瞭解虛擬機器副檔名和功能

## <a name="azure-vm-extensions"></a>Azure VM 副檔名

Azure 虛擬機器副檔名是小型上 Azure 虛擬機器提供文章部署設定與自動化工作的應用程式。 例如，如果虛擬機器需要安裝、 防毒保護]，或 Docker 設定的軟體，VM 副檔名為可用來完成這些工作。 可以使用 Azure CLI PowerShell 執行 azure VM 副檔名資源管理範本和 Azure 入口網站。 副檔名可以建立新的虛擬機器部署，或執行任何現有的系統。

這份文件提供如何偵測可用的 VM 延伸 Azure 虛擬機器分機]，並指示的必要條件。 

## <a name="azure-vm-agent"></a>Azure VM 代理程式

Azure VM 代理程式管理 Azure 虛擬機器和 Azure 布料的轉印圖樣控制器之間的互動。 VM 代理程式的部署及管理 Azure 虛擬機器，包括執行 VM 副檔名的許多功能方面負責。 Azure VM 代理程式已預先安裝 Azure 圖庫的圖像，並可以安裝在支援的作業系統。 

有關支援的作業系統和安裝指示，請參閱[Azure 虛擬機器代理程式](./virtual-machines-windows-classic-agents-and-extensions.md)。

## <a name="discover-vm-extensions"></a>探索 VM 副檔名

許多不同的 VM 擴充功能，可用於與 Azure 虛擬機器。 若要查看的完整清單，請透過 Azure CLI 中，以選擇的位置取代位置中執行下列命令。

```none
Get-AzureVMAvailableExtension | Select ExtensionName, Version
```

<br />

## <a name="common-vm-extensions"></a>一般 VM 擴充功能

|副檔名   |描述   |詳細資訊   |
|---|---|---|
|在 Windows 版的自訂指令碼副檔名  | 執行指令碼針對 Azure 虛擬機器  |[在 Windows 版的自訂指令碼副檔名](./virtual-machines-windows-extensions-customscript.md)   |
|在 Windows 版的 DSC 副檔名 | PowerShell DSC （所需的狀態設定） 副檔名。  | [Docker VM 副檔名](./virtual-machines-windows-extensions-dsc-overview.md)  |
|Azure 診斷副檔名 | 管理 Azure 診斷程式 |[Azure 診斷副檔名](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
