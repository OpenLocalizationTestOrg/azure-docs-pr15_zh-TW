<properties
    pageTitle="windows 虛擬機器指導方針 |Microsoft Azure"
    description="瞭解如何將 Azure 部署 windows 虛擬機器重要的設計及實作方針"
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="virtual-machines-guidelines"></a>虛擬機器指導方針

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

本文著重於了解建立及管理 Azure 環境中的虛擬機器 (Vm) 所需規劃的步驟。

## <a name="implementation-guidelines-for-vms"></a>實作 Vm 指導方針
決策︰

- 您需要多少 Vm 各種應用程式層和元件架構的？
- 每個 VM 需要何種 CPU 和記憶體資源，以及儲存需求是什麼？

工作︰

- 定義您的應用程式] 及 [Vm 需要資源的工作量。
- 對齊每個 VM 的適當的 VM 大小和儲存空間類型的資源的需求。
- 定義不同層和元件架構的資源的群組。
- 定義您 VM 命名慣例。
- 建立您的 Vm 使用 PowerShell 的 Azure，網頁入口網站，或與資源管理員範本。

## <a name="virtual-machines"></a>虛擬機器

Azure 環境中的主要元件是可能 Vm。 這是您可在此執行您的應用程式、 資料庫、 驗證服務等。

請務必瞭解[不同 VM 大小](virtual-machines-windows-sizes.md)以正確調整大小的效能與成本觀點來看您環境。 如果您的 Vm 沒有足夠的 CPU 核心或記憶體，應用程式的效能會遭遇無論看看它設計和開發。 當您決定使用您的基礎結構中每個元件 VM 的大小，請檢閱開始算起的每個 VM 數列的建議工作負載。 您可以[變更的 VM 大小](https://azure.microsoft.com/blog/resize-virtual-machines/)部署之後。

儲存於 VM 效能播放重要的角色。 您可以使用標準的儲存空間，請使用一般旋轉磁碟，或是進階版儲存空間，高 I/O 負載和最大使用量效能，使用 SSD 磁碟。 為使用虛擬記憶體大小，會增加成本來選取儲存媒體的考量。 您可以瞭解[儲存基礎結構的指導方針文章](virtual-machines-windows-infrastructure-storage-solutions-guidelines.md)，以了解如何設計適當的儲存空間，以獲得最佳效能的您 Vm。


## <a name="resource-groups"></a>資源群組
管理與使用[Azure 資源群組](../azure-resource-manager/resource-group-overview.md)進行的維修作業，例如 Vm 元件是邏輯群組在一起。 使用資源群組，您可以建立、 管理及監視的應用程式的所有資源。 您也可以實作[角色型存取控制](../active-directory/role-based-access-control-what-is.md)授與存取權給其他人在您的小組只所需的資源。 需要時間來查看您的資源群組和角色指派計劃。 有實際設計及實作資源群組，因此請務必閱讀[資源群組指導方針文章](virtual-machines-windows-infrastructure-resource-groups-guidelines.md)，以瞭解最佳方式，來查看您的 Vm 建立不同的方法。


## <a name="templates"></a>範本 
您可以建立宣告式 JSON 檔案，以建立您的 Vm 所定義的範本。 範本通常也建立所需的儲存空間、 網路、 網路介面、 IP 位址等以及 Vm 本身。 使用範本建立一致、 重現問題的開發環境，輕鬆地複製生產環境中測試用途，反之亦然。 您可以閱讀更多有關[建立及使用的範本](../azure-resource-manager/resource-group-overview.md#template-deployment)以瞭解如何使用這些建立及部署您 Vm。


## <a name="next-steps"></a>後續步驟
[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 